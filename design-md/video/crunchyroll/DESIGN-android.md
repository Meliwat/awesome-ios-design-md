# Crunchyroll (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Crunchyroll's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the resume-aware episode list, the sliding-underline segmented control, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (true-black OLED canvas, single Crunchyroll Orange accent, full-bleed key-art with a black scrim, the resume progress bar) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush.verticalGradient` for the scrim, `AnimatedContent`/`animateDpAsState` for the segmented underline, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for key-art and stills. No color extraction — Crunchyroll's palette is a fixed dark system, so Palette is not needed. Crunchyroll is **dark-only**; there is no light scheme.

## 1. Color Tokens

```kotlin
// ui/theme/CrunchyrollColors.kt
import androidx.compose.ui.graphics.Color

object CrunchyrollColors {
    // Canvas & Surfaces (dark-only)
    val Canvas   = Color(0xFF000000) // true black OLED
    val Surface1 = Color(0xFF16171A) // sheets / modals
    val Surface2 = Color(0xFF23252B) // cards / press / search field
    val Surface3 = Color(0xFF2E3035) // progress track / chips
    val Divider  = Color(0xFF2A2C31)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFA0A0A0)
    val TextTertiary  = Color(0xFF6A6C70)
    val TextOnScrim   = Color(0xFFD8D8D8)

    // Brand & status
    val Orange        = Color(0xFFF47521) // the ONLY accent
    val OrangePressed = Color(0xFFD8610F)
    val OrangeTint    = Color(0xFFFF8C42)
    val PremiumGold   = Color(0xFFFFC107) // Premium badge / lock only
    val PremiumInk    = Color(0xFF1A1304) // text on gold
    val Simulcast     = Color(0xFF2BB673)
    val NewBlue       = Color(0xFF2A9DF4)
    val Error         = Color(0xFFE03E3E)
}
```

Wire it into a dark-only scheme. Crunchyroll has **no light mode** — always use the dark scheme; a light canvas breaks the OLED key-art floor.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.runtime.Composable

private val CrunchyrollScheme = darkColorScheme(
    primary        = CrunchyrollColors.Orange,        // the single accent
    onPrimary      = CrunchyrollColors.TextPrimary,
    background     = CrunchyrollColors.Canvas,
    onBackground   = CrunchyrollColors.TextPrimary,
    surface        = CrunchyrollColors.Surface1,
    onSurface      = CrunchyrollColors.TextPrimary,
    surfaceVariant = CrunchyrollColors.Surface2,
    outline        = CrunchyrollColors.Divider,
    error          = CrunchyrollColors.Error,
)

@Composable
fun CrunchyrollTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = CrunchyrollScheme,   // always dark — never switch on isSystemInDarkTheme()
        typography  = CrunchyrollTypography,
        content     = content,
    )
```

## 2. Typography

Crunchyroll's product face is **Lato**, leaning hard on the 900 (Black) weight for hero titles, CTAs, and badges. Drop the TTFs in `res/font/`. Lato is SIL OFL — free to ship. Body 400; titles/CTAs/badges 900.

```kotlin
// ui/theme/CrunchyrollType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Lato = FontFamily(
    Font(R.font.lato_regular, FontWeight.Normal),
    Font(R.font.lato_bold,    FontWeight.Bold),
    Font(R.font.lato_black,   FontWeight.Black),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object CrunchyrollText {
    val ScreenTitle   = TextStyle(Lato, fontWeight = FontWeight.Black,  fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.5).sp)
    val HeroTitle     = TextStyle(Lato, fontWeight = FontWeight.Black,  fontSize = 28.sp, lineHeight = 30.sp, letterSpacing = (-0.5).sp)
    val SectionHeader = TextStyle(Lato, fontWeight = FontWeight.Black,  fontSize = 22.sp, lineHeight = 25.sp, letterSpacing = (-0.2).sp)
    val RowHeader     = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 22.sp)
    val Body          = TextStyle(Lato, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 24.sp)
    val EpisodeTitle  = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 15.sp, lineHeight = 20.sp)
    val Synopsis      = TextStyle(Lato, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 18.sp)
    val Meta          = TextStyle(Lato, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 20.sp)
    val ButtonLabel   = TextStyle(Lato, fontWeight = FontWeight.Black,  fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.3.sp)
    val Segmented     = TextStyle(Lato, fontWeight = FontWeight.Black,  fontSize = 14.sp, lineHeight = 14.sp)
    val Badge         = TextStyle(Lato, fontWeight = FontWeight.Black,  fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.4.sp)
    val Tab           = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Caption       = TextStyle(Lato, fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val CrunchyrollTypography = Typography(
    headlineLarge  = CrunchyrollText.ScreenTitle,
    headlineMedium = CrunchyrollText.HeroTitle,
    titleMedium    = CrunchyrollText.EpisodeTitle,
    bodyMedium     = CrunchyrollText.Body,
    labelSmall     = CrunchyrollText.Tab,
)
```

## 3. Signature Components

### Anime Detail Hero (full-bleed key-art + scrim)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.platform.LocalConfiguration
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun AnimeHero(
    keyArtUrl: String,
    title: String,
    meta: String,                 // "★ 4.9 · 2024 · TV-14 · Action"
    isSimulcast: Boolean,
    isPremium: Boolean,
) {
    val screenH = LocalConfiguration.current.screenHeightDp.dp
    Box(Modifier.fillMaxWidth().height(screenH * 0.6f)) {
        AsyncImage(
            model = keyArtUrl,
            contentDescription = title,
            modifier = Modifier.fillMaxSize(),     // full-bleed, no corner radius
            contentScale = ContentScale.Crop,
        )
        // Hero-to-black scrim — the signature depth cue
        Box(
            Modifier
                .fillMaxSize()
                .background(
                    Brush.verticalGradient(
                        0.0f to Color.Transparent,
                        0.55f to Color.Black.copy(alpha = 0.55f),
                        1.0f to Color.Black,
                    )
                )
        )
        Column(
            Modifier
                .align(Alignment.BottomStart)
                .padding(horizontal = 20.dp)
                .padding(bottom = 18.dp),
            verticalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                if (isSimulcast) ContentBadge(BadgeKind.Simulcast)
                if (isPremium)   ContentBadge(BadgeKind.Premium)
            }
            Text(title, style = CrunchyrollText.HeroTitle, color = CrunchyrollColors.TextPrimary, maxLines = 2)
            Text(meta,  style = CrunchyrollText.Meta,      color = CrunchyrollColors.TextOnScrim)
        }
    }
}
```

### Primary CTA ("Start Watching" / "Resume")

```kotlin
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.Icon

@Composable
fun WatchButton(label: String, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Button(
        onClick = onClick,
        interactionSource = interaction,
        shape = RoundedCornerShape(8.dp),
        colors = ButtonDefaults.buttonColors(
            containerColor = if (pressed) CrunchyrollColors.OrangePressed else CrunchyrollColors.Orange,
            contentColor = CrunchyrollColors.TextPrimary,
        ),
        modifier = Modifier.fillMaxWidth().height(52.dp),
    ) {
        Icon(Icons.Filled.PlayArrow, contentDescription = null, modifier = Modifier.size(18.dp))
        Spacer(Modifier.width(10.dp))
        Text(label, style = CrunchyrollText.ButtonLabel)
    }
}
```

### Episode Row (resume-aware)

```kotlin
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.draw.clip
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun EpisodeRow(
    number: String,           // "E1"
    stillUrl: String,
    title: String,
    synopsis: String,
    progress: Float,          // 0f..1f
) {
    Column {
        Row(
            Modifier.fillMaxWidth().padding(vertical = 12.dp),
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(Modifier.size(112.dp, 64.dp).clip(RoundedCornerShape(6.dp)).background(CrunchyrollColors.Surface2)) {
                AsyncImage(model = stillUrl, contentDescription = null,
                    modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
                Box(
                    Modifier
                        .align(Alignment.TopStart).padding(4.dp)
                        .clip(RoundedCornerShape(3.dp))
                        .background(Color.Black.copy(alpha = 0.7f))
                        .padding(horizontal = 6.dp, vertical = 2.dp)
                ) { Text(number, style = CrunchyrollText.Badge, color = CrunchyrollColors.TextPrimary) }

                if (progress > 0f) {
                    Box(Modifier.align(Alignment.BottomStart).fillMaxWidth().height(3.dp)
                        .background(Color.White.copy(alpha = 0.25f))) {
                        Box(Modifier.fillMaxHeight().fillMaxWidth(progress).background(CrunchyrollColors.Orange))
                    }
                }
            }
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(3.dp)) {
                Text(title, style = CrunchyrollText.EpisodeTitle, color = CrunchyrollColors.TextPrimary)
                Text(synopsis, style = CrunchyrollText.Synopsis, color = CrunchyrollColors.TextSecondary,
                    maxLines = 2, overflow = TextOverflow.Ellipsis)
            }
        }
        HorizontalDivider(color = CrunchyrollColors.Divider, thickness = 1.dp)
    }
}
```

### Content Badge (Simulcast / Premium / New Episode)

```kotlin
enum class BadgeKind { Simulcast, Premium, NewEpisode }

@Composable
fun ContentBadge(kind: BadgeKind) {
    val (bg, fg, label) = when (kind) {
        BadgeKind.Simulcast  -> Triple(CrunchyrollColors.Simulcast,   CrunchyrollColors.TextPrimary, "SIMULCAST")
        BadgeKind.Premium    -> Triple(CrunchyrollColors.PremiumGold, CrunchyrollColors.PremiumInk,  "PREMIUM")
        BadgeKind.NewEpisode -> Triple(CrunchyrollColors.NewBlue,     CrunchyrollColors.TextPrimary, "NEW EPISODE")
    }
    Box(
        Modifier
            .clip(RoundedCornerShape(4.dp))
            .background(bg)
            .padding(horizontal = 8.dp, vertical = 4.dp)
    ) { Text(label, style = CrunchyrollText.Badge, color = fg) }
}
```

### Segmented Control (sliding orange underline)

```kotlin
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.clickable
import androidx.compose.ui.unit.Dp

@Composable
fun CRSegmented(items: List<String>, selected: Int, onSelect: (Int) -> Unit) {
    val tabWidths = remember { mutableStateMapOf<Int, Dp>() }
    val tabX = remember { mutableStateMapOf<Int, Dp>() }
    val density = LocalDensity.current

    val underlineX by animateDpAsState(tabX[selected] ?: 0.dp, tween(220), label = "underlineX")
    val underlineW by animateDpAsState(tabWidths[selected] ?: 0.dp, tween(220), label = "underlineW")

    Column {
        Row(horizontalArrangement = Arrangement.spacedBy(24.dp), modifier = Modifier.padding(bottom = 10.dp)) {
            items.forEachIndexed { i, label ->
                Text(
                    label,
                    style = CrunchyrollText.Segmented,
                    color = if (i == selected) CrunchyrollColors.TextPrimary else CrunchyrollColors.TextSecondary,
                    modifier = Modifier
                        .clickable { onSelect(i) }
                        .onGloballyPositioned {
                            tabX[i] = with(density) { it.positionInParent().x.toDp() }
                            tabWidths[i] = with(density) { it.size.width.toDp() }
                        },
                )
            }
        }
        Box(Modifier.fillMaxWidth()) {
            HorizontalDivider(color = CrunchyrollColors.Divider, thickness = 1.dp)
            Box(
                Modifier
                    .offset(x = underlineX)
                    .width(underlineW)
                    .height(3.dp)
                    .clip(RoundedCornerShape(2.dp))
                    .background(CrunchyrollColors.Orange)
            )
        }
    }
}
```

## 4. Navigation

Crunchyroll has minimal chrome: a translucent floating top bar over the key-art and a 5-tab bottom strip. On Android, model the strip as a `NavigationBar`. There is no tint pill — active is just Crunchyroll Orange.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun CRBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = CrunchyrollColors.Canvas.copy(alpha = 0.95f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"      to Icons.Filled.Home,
            "Browse"    to Icons.Filled.GridView,
            "Watchlist" to Icons.Filled.Add,
            "Manga"     to Icons.Filled.MenuBook,
            "Profile"   to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = CrunchyrollText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = CrunchyrollColors.Orange,     // the only accent
                    selectedTextColor   = CrunchyrollColors.Orange,
                    unselectedIconColor = CrunchyrollColors.TextTertiary,
                    unselectedTextColor = CrunchyrollColors.TextTertiary,
                    indicatorColor      = Color.Transparent,            // no Material pill
                ),
            )
        }
    }
}
```

The detail-screen top controls (back, share, cast) are *not* a solid `TopAppBar` — they float directly over the key-art at the status-bar inset, switching to a solid `Canvas` bar with the compact title once the hero title scrolls out.

## 5. Motion

Crunchyroll motion is cinematic and calm — 150–300ms ease-out, including a deliberate "lights down" fade-to-black before the player.

| Moment | Compose recipe |
|--------|----------------|
| Segmented underline | `animateDpAsState` for x + width, `tween(220)` |
| Card press | `animateFloatAsState` scale → 0.97, `tween(150)` |
| "Lights down" → player | fade detail `alpha` 1 → 0 `tween(250)`, then navigate to the player |
| Progress fill on return | `animateFloatAsState` 0 → watched, `tween(400)` |
| Hero parallax | track `LazyListState.firstVisibleItemScrollOffset`; key-art `Modifier.graphicsLayer { translationY = offset * 0.5f }` |
| Top-bar reveal | `AnimatedVisibility` crossfade of the solid `Canvas` bar over `tween(200)` once hero title scrolls out |
| Sheet present | `ModalBottomSheet` default slide-up; scrim `Color.Black.copy(alpha = 0.6f)` |

```kotlin
// The canonical "lights down" before playback
var detailAlpha by remember { mutableFloatStateOf(1f) }
val a by animateFloatAsState(detailAlpha, tween(250), label = "lightsDown") {
    if (it == 0f) navController.navigate("player/$episodeId")
}
Box(Modifier.graphicsLayer { alpha = a }) { /* detail content */ }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft tick on Watchlist toggle and segment change; a stronger `HapticFeedbackConstants.CONFIRM` (via `LocalView`) on "Start Watching".

## 6. Icons

Crunchyroll's iconography is simple and stroked; the closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Browse (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Watchlist (tab) | `plus` | `Icons.Filled.Add` |
| Manga (tab) | `book.fill` | `Icons.Filled.MenuBook` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Play (CTA) | `play.fill` | `Icons.Filled.PlayArrow` |
| Add to Watchlist | `plus` / `checkmark` | `Icons.Filled.Add` / `Icons.Filled.Check` |
| Rate | `hand.thumbsup` | `Icons.Filled.ThumbUp` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Comments | `bubble.left` | `Icons.Filled.ChatBubbleOutline` |
| Cast | `airplayvideo` | `Icons.Filled.Cast` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Notifications | `bell` | `Icons.Filled.Notifications` |
| Premium lock | `lock.fill` | `Icons.Filled.Lock` |
| Download | `arrow.down.circle` | `Icons.Filled.Download` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `Brush` gradients are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` and draw the key-art *under* the status bar (full-bleed). The canvas is true black so use light-content system bars permanently; the detail top controls must clear the camera cutout.
- **Dark-only**: do **not** branch on `isSystemInDarkTheme()` or enable Material You `dynamicColorScheme()` — Crunchyroll has no light mode and no wallpaper-harmonized accent; the OLED black + single orange identity must hold.
- **Font scaling**: `sp` honors the user's font scale — keep it on hero title, section headers, body, synopsis, meta. Pin layout-sensitive text (badges, 10sp tab labels, segmented items, episode-number chip, progress-bar geometry) by deriving from `dp` or a fixed-`fontScale` `CompositionLocalProvider`.
- **TalkBack**: label the hero "{title}, {meta}, Simulcast/Premium"; episode rows "Episode {n}, {title}, {percent} percent watched"; expose progress via `Modifier.semantics { progressBarRangeInfo = ProgressBarRangeInfo(progress, 0f..1f) }`.
- **Touch targets**: Material guidance is 48.dp. The CTA is 52.dp (fine); give the 22.dp tab icons a 48.dp hit area; episode rows are full-row tappable (≥64.dp); segmented items get a 44.dp hit height.
- **Contrast**: `#FFFFFF` on `#000000` is maximal. The PREMIUM badge uses dark `#1A1304` on gold `#FFC107` to pass WCAG AA — never white-on-gold. `#A0A0A0` meta on the scrim passes AA at 14sp.
- **Single-accent discipline is an a11y asset**: orange is the *only* "actionable / resume" signal — never add a second accent; it would dilute the cue.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable hero parallax and skip the "lights down" fade (cut straight to the player); replace the segmented-underline slide with a `Crossfade`. Keep the progress fill static at the watched value.
- **Player**: lock the video activity to landscape + immersive full-screen; precede it with the 250ms fade-to-black for the cinematic transition.
