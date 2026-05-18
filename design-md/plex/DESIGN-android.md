# Plex (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Plex's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the server picker, the On Deck rail, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (cool charcoal canvas — not true black, single Plex Yellow accent with mandatory dark ink, the server picker, the On Deck progress bar, the quiet unwatched dot) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` for the server sheet, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for poster art and On Deck stills. No color extraction — Plex's palette is a fixed charcoal+yellow system, so Palette is not needed. Plex's media experience is **dark-first**; a pure-white light scheme is not the identity.

## 1. Color Tokens

```kotlin
// ui/theme/PlexColors.kt
import androidx.compose.ui.graphics.Color

object PlexColors {
    // Canvas & Surfaces (dark-first, cool charcoal — NOT true black)
    val Canvas   = Color(0xFF1F2326)
    val Surface1 = Color(0xFF282C30) // server pill / sheet
    val Surface2 = Color(0xFF32373B) // rows / search / ghost button
    val Surface3 = Color(0xFF3C4146) // selected row / progress track
    val Divider  = Color(0xFF34393D)

    // Text
    val TextPrimary   = Color(0xFFF2F3F4)
    val TextSecondary = Color(0xFF9BA0A4)
    val TextTertiary  = Color(0xFF6B7075)

    // Brand & status
    val Yellow        = Color(0xFFE5A00D) // the ONLY accent
    val YellowPressed = Color(0xFFC98A09)
    val YellowInk     = Color(0xFF1A1304) // text on yellow — mandatory, never white
    val Online        = Color(0xFF4CAF50)
    val Offline       = Color(0xFF6B7075)
    val Error         = Color(0xFFE5484D)
}
```

Wire it into a dark-first scheme. Plex's media surfaces are charcoal — not true black, not a light mode.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.runtime.Composable

private val PlexScheme = darkColorScheme(
    primary        = PlexColors.Yellow,         // the single accent
    onPrimary      = PlexColors.YellowInk,       // dark-on-yellow (mandatory)
    background     = PlexColors.Canvas,
    onBackground   = PlexColors.TextPrimary,
    surface        = PlexColors.Surface1,
    onSurface      = PlexColors.TextPrimary,
    surfaceVariant = PlexColors.Surface2,
    outline        = PlexColors.Divider,
    error          = PlexColors.Error,
)

@Composable
fun PlexTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = PlexScheme,   // media stays dark charcoal — don't branch on isSystemInDarkTheme()
        typography  = PlexTypography,
        content     = content,
    )
```

## 2. Typography

Plex's product face is **Inter** — neutral, legible, functional. Drop the TTFs in `res/font/`. Inter is SIL OFL — free to ship.

```kotlin
// ui/theme/PlexType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,    FontWeight.Normal),
    Font(R.font.inter_medium,     FontWeight.Medium),
    Font(R.font.inter_semibold,   FontWeight.SemiBold),
    Font(R.font.inter_bold,       FontWeight.Bold),
    Font(R.font.inter_extrabold,  FontWeight.ExtraBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object PlexText {
    val ScreenTitle   = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val HeroTitle     = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val SectionHeader = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Subsection    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val Body          = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val CardTitle     = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Meta          = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val ButtonLabel   = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val ServerName    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 18.sp)
    val ServerMeta    = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 12.sp, lineHeight = 16.sp)
    val Tab           = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val TechChip      = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val PlexTypography = Typography(
    headlineLarge  = PlexText.ScreenTitle,
    headlineMedium = PlexText.HeroTitle,
    titleMedium    = PlexText.CardTitle,
    bodyMedium     = PlexText.Body,
    labelSmall     = PlexText.Tab,
)
```

## 3. Signature Components

### Server Picker Pill + Sheet Row

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material.icons.filled.KeyboardArrowDown
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

@Composable
fun ServerPill(name: String, isOnline: Boolean, onClick: () -> Unit) {
    Row(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(PlexColors.Surface1)
            .border(1.dp, PlexColors.Divider, RoundedCornerShape(999.dp))
            .clickable(onClick = onClick)
            .padding(horizontal = 14.dp, vertical = 7.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Box(Modifier.size(8.dp).clip(CircleShape).background(if (isOnline) PlexColors.Online else PlexColors.Offline))
        Text(name, style = PlexText.ServerName.copy(fontSize = 13.sp), color = PlexColors.TextPrimary)
        Icon(Icons.Filled.KeyboardArrowDown, contentDescription = null, tint = PlexColors.TextSecondary, modifier = Modifier.size(14.dp))
    }
}

@Composable
fun ServerSheetRow(
    name: String,
    meta: String,                 // "Online · 2,481 items · LAN"
    isOnline: Boolean,
    isSelected: Boolean,
    onClick: () -> Unit,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(8.dp))
            .background(if (isSelected) PlexColors.Surface3 else PlexColors.Surface2)
            .clickable(onClick = onClick)
            .padding(horizontal = 14.dp, vertical = 13.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(9.dp).clip(CircleShape).background(if (isOnline) PlexColors.Online else PlexColors.Offline))
        Column(Modifier.weight(1f)) {
            Text(name, style = PlexText.ServerName, color = PlexColors.TextPrimary)
            Text(meta, style = PlexText.ServerMeta, color = PlexColors.TextSecondary)
        }
        if (isSelected) {
            Icon(Icons.Filled.Check, contentDescription = "Selected", tint = PlexColors.Yellow, modifier = Modifier.size(16.dp))
        }
    }
}
```

### On Deck Tile (resume-aware)

```kotlin
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import coil.compose.AsyncImage

@Composable
fun OnDeckTile(
    artUrl: String,
    title: String,
    sub: String,                  // "S2 · E5 · 22 min left"
    progress: Float,              // 0f..1f
) {
    Column(Modifier.width(220.dp)) {
        Box(
            Modifier
                .size(220.dp, 124.dp)
                .clip(RoundedCornerShape(6.dp))
                .background(PlexColors.Surface2)
        ) {
            AsyncImage(model = artUrl, contentDescription = title,
                modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
            Box(
                Modifier
                    .align(Alignment.Center)
                    .size(42.dp).clip(CircleShape)
                    .background(Color.Black.copy(alpha = 0.55f)),
                contentAlignment = Alignment.Center,
            ) {
                Icon(Icons.Filled.PlayArrow, contentDescription = null, tint = Color.White, modifier = Modifier.size(16.dp))
            }
            Box(
                Modifier
                    .align(Alignment.BottomStart)
                    .fillMaxWidth().height(3.dp)
                    .background(Color.White.copy(alpha = 0.22f))
            ) {
                Box(Modifier.fillMaxHeight().fillMaxWidth(progress).background(PlexColors.Yellow))
            }
        }
        Spacer(Modifier.height(8.dp))
        Text(title, style = PlexText.CardTitle, color = PlexColors.TextPrimary, maxLines = 1, overflow = TextOverflow.Ellipsis)
        Text(sub, style = PlexText.Meta, color = PlexColors.TextSecondary, maxLines = 1, overflow = TextOverflow.Ellipsis)
    }
}
```

### Primary Play Button (dark-on-yellow)

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.runtime.getValue
import androidx.compose.runtime.remember

@Composable
fun PlayButton(onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Button(
        onClick = onClick,
        interactionSource = interaction,
        shape = RoundedCornerShape(8.dp),
        colors = ButtonDefaults.buttonColors(
            containerColor = if (pressed) PlexColors.YellowPressed else PlexColors.Yellow,
            contentColor = PlexColors.YellowInk,        // dark ink — NEVER white
        ),
        modifier = Modifier.fillMaxWidth().height(48.dp),
    ) {
        Icon(Icons.Filled.PlayArrow, contentDescription = null, modifier = Modifier.size(16.dp))
        Spacer(Modifier.width(8.dp))
        Text("Play", style = PlexText.ButtonLabel)
    }
}
```

### Library Poster + Unwatched Dot

```kotlin
import androidx.compose.ui.draw.shadow

@Composable
fun LibraryPoster(artUrl: String, unwatched: Boolean) {
    Box(Modifier.aspectRatio(2f / 3f).clip(RoundedCornerShape(6.dp)).background(PlexColors.Surface2)) {
        AsyncImage(model = artUrl, contentDescription = null,
            modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
        if (unwatched) {
            Box(
                Modifier
                    .align(Alignment.TopEnd)
                    .padding(7.dp)
                    .size(9.dp)
                    .shadow(2.dp, CircleShape)
                    .clip(CircleShape)
                    .background(PlexColors.Yellow)
            )
        }
    }
}
```

### Tech / Quality Chip

```kotlin
@Composable
fun TechChip(text: String) {
    Box(
        Modifier
            .clip(RoundedCornerShape(4.dp))
            .background(PlexColors.Surface2)
            .padding(horizontal = 8.dp, vertical = 4.dp)
    ) { Text(text, style = PlexText.TechChip, color = PlexColors.TextSecondary) }
}
```

## 4. Navigation

Plex has a clean top inset and a 5-tab bottom strip. On Android, model the strip as a `NavigationBar` and the server sheet as a `ModalBottomSheet`. There is no tint pill — active is just Plex Yellow.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun PlexBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = PlexColors.Canvas.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Library"  to Icons.Filled.GridView,
            "Discover" to Icons.Filled.PlayCircle,
            "Live TV"  to Icons.Filled.Tv,
            "You"      to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = PlexText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = PlexColors.Yellow,        // the only accent
                    selectedTextColor   = PlexColors.Yellow,
                    unselectedIconColor = PlexColors.TextTertiary,
                    unselectedTextColor = PlexColors.TextTertiary,
                    indicatorColor      = Color.Transparent,         // no Material pill
                ),
            )
        }
    }
}
```

The server sheet (`ModalBottomSheet`, `Surface1` background, rounded 12.dp top) holds a list of `ServerSheetRow`s; the selected server's row uses `Surface3` and a yellow check. Plex keeps a clean top inset (server pill + nav) — do not use a translucent full-bleed top bar.

## 5. Motion

Plex motion is quiet and calm — 150–280ms ease-out, **no bounce**.

| Moment | Compose recipe |
|--------|----------------|
| Server switch | check `AnimatedVisibility` `fadeIn(tween(150))`; dismiss sheet; Home content `Crossfade(tween(250))` to the new server |
| On Deck resume | tile press `animateFloatAsState` scale → 0.97 `tween(150)`; on return, progress `animateFloatAsState` 0 → watched `tween(350)` |
| Card press | `animateFloatAsState` scale → 0.97 `tween(150)` |
| Mark as Watched | unwatched dot `AnimatedVisibility` `fadeOut(tween(200))` + a "Marked as watched" snackbar |
| Status dot online | color cross-fade gray → green `tween(200)` (no spring) |
| Sheet present | `ModalBottomSheet` default slide-up; scrim `Color.Black.copy(alpha = 0.55f)` |
| Tab switch | instant content swap; active-tab color cross-fade `tween(120)` |

```kotlin
// On Deck progress fill on return — the canonical Plex resume cue
val fill by animateFloatAsState(if (focused) watched else 0f, tween(350), label = "onDeckFill")
Box(Modifier.fillMaxHeight().fillMaxWidth(fill).background(PlexColors.Yellow))
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for a soft tick on server selection, tile resume, and Mark-as-Watched — nothing heavier; Plex motion is deliberately quiet.

## 6. Icons

Plex iconography is clean and functional; the closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Library (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Discover (tab) | `play.circle` | `Icons.Filled.PlayCircle` |
| Live TV (tab) | `tv` | `Icons.Filled.Tv` |
| You (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Server chevron | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Selected server | `checkmark` | `Icons.Filled.Check` |
| Play (CTA / overlay) | `play.fill` | `Icons.Filled.PlayArrow` |
| Mark as Watched | `checkmark.circle` | `Icons.Filled.CheckCircle` |
| Add to Playlist | `text.badge.plus` | `Icons.Filled.PlaylistAdd` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Cast | `airplayvideo` | `Icons.Filled.Cast` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Download | `arrow.down.circle` | `Icons.Filled.Download` |
| Online/Offline dot | (a `Box` circle) | (a `Box` circle, not an icon) |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `ModalBottomSheet` + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`. Plex keeps a clean top inset (server pill / nav) — do not draw poster art under the status bar. The cool charcoal canvas wants light-content system bars.
- **Dark-first media**: do **not** branch on `isSystemInDarkTheme()` for the library/browse surfaces, and do **not** enable Material You `dynamicColorScheme()` — the cool charcoal + single-yellow identity must hold regardless of wallpaper.
- **Dark-on-yellow is mandatory**: `onPrimary = YellowInk` and every yellow fill uses `#1A1304` content — white-on-yellow fails WCAG AA. Centralize so it can't drift.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen/hero titles, section headers, body, summaries, meta. Pin layout-sensitive text (10sp tab labels, tech chips, progress-bar geometry, status dots, unwatched dot) by deriving from `dp`.
- **TalkBack**: label the server pill "Server: {name}, {online/offline}"; server rows "{name}, {meta}, {selected}"; On Deck tiles "{title}, {sub}, {percent} percent watched" with `Modifier.semantics { progressBarRangeInfo = ProgressBarRangeInfo(progress, 0f..1f) }`.
- **Color-only state**: status dots convey online/offline by color — always back them with the text label ("Online"/"Offline") in the meta line so the state isn't color-only.
- **Touch targets**: Material guidance is 48.dp. The Play button is 48.dp; give the 22.dp tab icons a 48.dp hit area; On Deck tiles are full-tile tappable (≥124.dp); server rows are full-row (≥44.dp).
- **Contrast**: `#F2F3F4` on `#1F2326` passes AA; `#9BA0A4` meta passes AA at 14sp; the yellow check is an icon supported by the row's selected `Surface3` background.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the Home `Crossfade` on server switch with an instant swap; keep the On Deck progress static at the watched value (no fill animation). Plex's motion is already minimal, so little else changes.
- **Player**: lock the video activity to landscape + immersive full-screen; pull-to-refresh rescans the current server with a subtle yellow indicator.
