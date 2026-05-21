# Paramount+ (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Paramount+'s visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the brand-hubs row + hero billboard + content rows, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the midnight-navy canvas, the brand-hubs row, the single Paramount+ Blue accent, the prestige hero) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyRow` instead of horizontal `ScrollView`, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for key art. Paramount+ is dark-only — no light scheme, no Material You dynamic color (the navy + single-blue identity must hold regardless of wallpaper).

## 1. Color Tokens

```kotlin
// ui/theme/ParamountColors.kt
import androidx.compose.ui.graphics.Color

object ParamountColors {
    // Canvas & surfaces (dark-only product)
    val Canvas   = Color(0xFF0A0E2D) // midnight navy — never pure black
    val Canvas2  = Color(0xFF0E1438) // subtle lift under hero
    val Surface1 = Color(0xFF161C44)
    val Surface2 = Color(0xFF1F2655)
    val Divider  = Color(0xFF2A3266)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFAEB4D6)
    val TextTertiary  = Color(0xFF6F77A6)

    // Brand (single accent)
    val Blue        = Color(0xFF0064FF)
    val BluePressed = Color(0xFF0052CC)
    val Sky         = Color(0xFF4D9DFF) // active-tab tint, eyebrows
    val Glass       = Color(0x24FFFFFF) // ~14% white

    // Semantic
    val Live    = Color(0xFFFF2D46)
    val Success = Color(0xFF1ED760)
    val ErrorR  = Color(0xFFFF453A)
    val Gold    = Color(0xFFF5C518)
}
```

Paramount+ is dark-first. Wire a single dark scheme; the light scheme is intentionally a copy (only legal/web views ever appear light, and those are out of scope).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.runtime.Composable

private val ParamountDark = darkColorScheme(
    primary        = ParamountColors.Blue,        // single blue accent
    onPrimary      = ParamountColors.TextPrimary,
    background     = ParamountColors.Canvas,
    onBackground   = ParamountColors.TextPrimary,
    surface        = ParamountColors.Surface1,
    onSurface      = ParamountColors.TextPrimary,
    surfaceVariant = ParamountColors.Surface2,
    outline        = ParamountColors.Divider,
    error          = ParamountColors.ErrorR,
)

@Composable
fun ParamountTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = ParamountDark,   // dark-only; do NOT use dynamicDarkColorScheme()
        typography  = ParamountTypography,
        content     = content,
    )
```

## 2. Typography

Paramount Sans is proprietary; ship **Inter** (SIL OFL) in `res/font/`. Hero/title heavy (800-900); `pt → sp` 1:1.

```kotlin
// ui/theme/ParamountType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),
    Font(R.font.inter_medium,    FontWeight.Medium),
    Font(R.font.inter_semibold,  FontWeight.SemiBold),
    Font(R.font.inter_bold,      FontWeight.Bold),
    Font(R.font.inter_extrabold, FontWeight.ExtraBold),
    Font(R.font.inter_black,     FontWeight.Black),
)

object ParamountText {
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.Black,     fontSize = 32.sp, lineHeight = 34.sp, letterSpacing = (-0.6).sp)
    val HeroTitle   = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 27.sp, letterSpacing = (-0.5).sp)
    val RowHeader   = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 25.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 22.sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 24.sp)
    val BodyReg     = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val PosterCap   = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 11.sp, lineHeight = 14.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Eyebrow     = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 1.6.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp)
    val HubLabel    = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.3.sp)
    val Badge       = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 8.sp,  lineHeight = 8.sp,  letterSpacing = 0.5.sp)
}

val ParamountTypography = Typography(
    headlineLarge  = ParamountText.ScreenTitle,
    headlineMedium = ParamountText.HeroTitle,
    titleLarge     = ParamountText.RowHeader,
    titleMedium    = ParamountText.CardTitle,
    bodyMedium     = ParamountText.Body,
    labelSmall     = ParamountText.Tab,
)
```

## 3. Signature Components

### Brand-Hubs Row

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.items
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import androidx.compose.animation.core.animateFloatAsState

data class Hub(val name: String, val gradient: List<Color>, val labelDark: Boolean = false)

@Composable
fun BrandHubsRow(hubs: List<Hub>, onSelect: (Hub) -> Unit) {
    LazyRow(
        contentPadding = PaddingValues(horizontal = 18.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
        modifier = Modifier.padding(bottom = 12.dp),
    ) {
        items(hubs) { hub ->
            val interaction = remember { MutableInteractionSource() }
            val pressed by interaction.collectIsPressedAsState()
            val s by animateFloatAsState(if (pressed) 0.96f else 1f, label = "hub")
            Box(
                Modifier
                    .scale(s)
                    .heightIn(min = 40.dp)
                    .widthIn(min = 70.dp)
                    .clip(RoundedCornerShape(8.dp))
                    .background(Brush.linearGradient(hub.gradient))
                    .clickable(interaction, indication = null) { onSelect(hub) }
                    .padding(horizontal = 12.dp),
                contentAlignment = Alignment.Center,
            ) {
                Text(
                    hub.name,
                    style = ParamountText.HubLabel,
                    color = if (hub.labelDark) Color(0xFF1B1B1B) else Color.White,
                )
            }
        }
    }
}
```

### Hero Billboard

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.*
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun HeroBillboard(
    artUrl: String,
    eyebrow: String,
    title: String,
    meta: String,
    onPlay: () -> Unit,
    onAdd: () -> Unit,
) {
    Box(Modifier.fillMaxWidth().height(332.dp)) {
        AsyncImage(
            model = artUrl, contentDescription = title,
            modifier = Modifier.matchParentSize(), contentScale = ContentScale.Crop,
        )
        Box(
            Modifier.matchParentSize().background(
                Brush.verticalGradient(
                    0.36f to Color.Transparent,
                    0.70f to ParamountColors.Canvas.copy(alpha = 0.72f),
                    1.0f  to ParamountColors.Canvas,
                )
            )
        )
        Column(
            Modifier.align(Alignment.BottomStart).padding(horizontal = 20.dp, vertical = 14.dp),
            verticalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Text(eyebrow.uppercase(), style = ParamountText.Eyebrow, color = ParamountColors.Sky)
            Text(title, style = ParamountText.HeroTitle, color = ParamountColors.TextPrimary)
            Text(meta, style = ParamountText.Meta, color = ParamountColors.TextSecondary)
            Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {
                Button(
                    onClick = onPlay,
                    colors = ButtonDefaults.buttonColors(containerColor = ParamountColors.Blue, contentColor = Color.White),
                    shape = RoundedCornerShape(6.dp),
                    contentPadding = PaddingValues(horizontal = 28.dp, vertical = 13.dp),
                ) {
                    Icon(Icons.Filled.PlayArrow, null, modifier = Modifier.size(16.dp))
                    Spacer(Modifier.width(7.dp))
                    Text("Play", style = ParamountText.Button)
                }
                Surface(onClick = onAdd, color = ParamountColors.Glass, shape = RoundedCornerShape(6.dp)) {
                    Row(Modifier.padding(horizontal = 16.dp, vertical = 12.dp), verticalAlignment = Alignment.CenterVertically) {
                        Icon(Icons.Filled.Add, null, tint = Color.White, modifier = Modifier.size(16.dp))
                        Spacer(Modifier.width(6.dp))
                        Text("My List", style = ParamountText.Body, color = Color.White)
                    }
                }
            }
        }
    }
}
```

### Poster Card (LIVE / NEW)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.shape.RoundedCornerShape

@Composable
fun LiveFlag() {
    val t = rememberInfiniteTransition(label = "live")
    val a by t.animateFloat(1f, 0.4f, infiniteRepeatable(tween(1200), RepeatMode.Reverse), label = "dot")
    Row(
        Modifier.clip(RoundedCornerShape(4.dp)).background(ParamountColors.Live)
            .padding(horizontal = 6.dp, vertical = 3.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Box(Modifier.size(5.dp).clip(CircleShape).background(Color.White.copy(alpha = a)))
        Text("LIVE", style = ParamountText.Badge, color = Color.White)
    }
}

@Composable
fun PosterCard(artUrl: String, title: String, live: Boolean = false, isNew: Boolean = false) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "poster")

    Column(Modifier.width(116.dp)) {
        Box(Modifier.size(116.dp, 164.dp).scale(scale).clip(RoundedCornerShape(8.dp))) {
            AsyncImage(model = artUrl, contentDescription = title,
                modifier = Modifier.matchParentSize(), contentScale = ContentScale.Crop)
            if (live) {
                Box(Modifier.align(Alignment.TopStart).padding(6.dp)) { LiveFlag() }
            } else if (isNew) {
                Box(
                    Modifier.align(Alignment.TopStart).padding(6.dp)
                        .clip(RoundedCornerShape(4.dp)).background(ParamountColors.Blue)
                        .padding(horizontal = 6.dp, vertical = 3.dp)
                ) { Text("NEW", style = ParamountText.Badge, color = Color.White) }
            }
        }
        Spacer(Modifier.height(6.dp))
        Text(title, style = ParamountText.PosterCap, color = ParamountColors.TextSecondary, maxLines = 1)
    }
}
```

### Keep-Watching Progress + Live Badge

```kotlin
@Composable
fun WatchProgress(fraction: Float, label: String) {
    val w by animateFloatAsState(fraction, tween(400), label = "progress")
    Column(verticalArrangement = Arrangement.spacedBy(6.dp)) {
        Box(Modifier.fillMaxWidth().height(4.dp).clip(RoundedCornerShape(2.dp)).background(ParamountColors.Surface2)) {
            Box(Modifier.fillMaxWidth(w).height(4.dp).clip(RoundedCornerShape(2.dp)).background(ParamountColors.Blue))
        }
        Text(label, style = ParamountText.PosterCap, color = ParamountColors.TextSecondary)
    }
}

@Composable
fun LiveBadge() {
    val t = rememberInfiniteTransition(label = "liveBadge")
    val a by t.animateFloat(1f, 0.4f, infiniteRepeatable(tween(1200), RepeatMode.Reverse), label = "dot")
    Row(
        Modifier.clip(RoundedCornerShape(4.dp)).background(ParamountColors.Live)
            .padding(horizontal = 10.dp, vertical = 5.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Box(Modifier.size(7.dp).clip(CircleShape).background(Color.White.copy(alpha = a)))
        Text("LIVE", style = ParamountText.Eyebrow.copy(letterSpacing = 0.6.sp), color = Color.White)
    }
}
```

## 4. Navigation

Paramount+ has a 4-tab bottom strip and a solid top bar with the hubs row pinned below it. On Android, model the strip as a `NavigationBar`. There is no tint pill — active is the sky-blue icon.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun ParamountBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = ParamountColors.Canvas.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"    to Icons.Filled.Home,
            "Live TV" to Icons.Filled.Tv,
            "Search"  to Icons.Filled.Search,
            "My List" to Icons.Filled.VideoLibrary,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = ParamountText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = ParamountColors.Sky,
                    selectedTextColor   = ParamountColors.Sky,
                    unselectedIconColor = ParamountColors.TextTertiary,
                    unselectedTextColor = ParamountColors.TextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill — Paramount+ has none
                ),
            )
        }
    }
}
```

The top bar is a solid `Canvas` `Row` (mountain logomark SVG + "Paramount" + blue "+" leading; search + profile trailing) with the `BrandHubsRow` pinned directly below it. Content rows are `LazyRow`s with `contentPadding = PaddingValues(horizontal = 18.dp)` so the last card clips off the trailing edge. The detail page is a full-bleed `AsyncImage` with a back chevron on a 40%-black circle.

## 5. Motion

Paramount+ motion is prestige-quiet — 120-400ms ease-out, never bouncy.

| Moment | Compose recipe |
|--------|----------------|
| Poster press | `collectIsPressedAsState()` → `animateFloatAsState(0.97f, tween())` on `Modifier.scale` |
| Live dot pulse | `rememberInfiniteTransition` → `animateFloat(1f, 0.4f, infiniteRepeatable(tween(1200), Reverse))` |
| Hub re-skin | `Crossfade(targetState = activeHubId, animationSpec = tween(300))` over the browse content |
| Hero cross-fade (~8s) | `Crossfade(targetState = featuredIndex, animationSpec = tween(400))` |
| Detail open | shared-element via `SharedTransitionLayout` (Compose 1.7+) or `AnimatedContent` slide `tween(300)` |
| Progress fill | `animateFloatAsState(fraction, tween(400))` on `fillMaxWidth(w)` |
| Sheet present | `ModalBottomSheet` (Material 3) — slide-up 300ms; scrim `Color.Black.copy(alpha = 0.6f)` |
| Row scroll | `LazyRow` native fling — free scroll, no snap |

```kotlin
// Hub re-skin — the canonical Paramount+ structural motion
Crossfade(targetState = activeHubId, animationSpec = tween(300), label = "hubReskin") { id ->
    HubBrowse(networkId = id)   // whole browse re-tinted into the network's brand color
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft confirm on hub switch and add-to-My-List; a lighter `HapticFeedbackConstants.CLOCK_TICK` (via `LocalView`) for poster selection. Playback start/stop is silent.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The mountain logomark + "Paramount+" wordmark must be a vector drawable / `ImageVector` — it is not an icon-font glyph.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Live TV (tab) | `tv` | `Icons.Filled.Tv` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| My List (tab) | `plus.rectangle.on.rectangle` | `Icons.Filled.VideoLibrary` |
| Play (CTA) | `play.fill` | `Icons.Filled.PlayArrow` |
| Add | `plus` / `checkmark` | `Icons.Filled.Add` / `Icons.Filled.Check` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| More info | `info.circle` | `Icons.Filled.InfoOutline` |
| Download | `arrow.down.circle` | `Icons.Filled.Download` |
| Cast | `airplayvideo` | `Icons.Filled.Cast` |
| Profile | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Live | `dot.radiowaves.left.and.right` | `Icons.Filled.SensorsOutlined` |
| Trailer | `play.rectangle` | `Icons.Filled.SmartDisplay` |
| Schedule (Live) | `calendar` | `Icons.Filled.CalendarMonth` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `ModalBottomSheet` + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the navy canvas wants light-content system bars in all states (the app is dark-only). Hero art bleeds under the status bar; the bottom bar respects the navigation bar inset.
- **Dark-only**: ship only `ParamountDark`; do **not** call `dynamicDarkColorScheme()` — the navy + single-blue identity must hold regardless of wallpaper. Ignore `isSystemInDarkTheme()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen/hero titles, row headers, body, captions. Pin layout-sensitive text (tab labels, 11sp eyebrows, hub labels, badges, "LIVE") via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` so it never overflows poster art.
- **TalkBack**: label hub chips "{network} hub, opens {network} shows"; the blue CTA "Play {title}"; live cards "Live now"; group hero eyebrow+title+meta into one `Modifier.semantics(mergeDescendants = true)` node; mark the pulsing live dot `Modifier.clearAndSetSemantics {}` (decorative — the "LIVE" text carries meaning).
- **Touch targets**: Material guidance is 48.dp. Hub chips (≥70×40.dp) and poster cards approach/exceed it; give the 22.dp tab icons a 48.dp hit area (`NavigationBarItem` handles this); the Play button is ≥ 44.dp tall.
- **Contrast**: white `#FFFFFF` and `#AEB4D6` on `#0A0E2D` pass WCAG AA; white on `#0064FF` passes AA for the CTA; sky-blue `#4D9DFF` on navy passes AA for the active tab. Never place body text on the blue accent or over un-scrimmed art — always keep the bottom hero scrim.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the hub `Crossfade` and hero `Crossfade` with instant swaps and stop the live-dot pulse (show a static dot); keep the press scale subtle or disable.
- **Elevation cues**: shadows are nearly invisible on the navy canvas — use the subtle `#0E1438` hero lift and a 1.dp `Divider` border on `ModalBottomSheet` / context menus as the floating cue, mirroring the iOS behavior.
