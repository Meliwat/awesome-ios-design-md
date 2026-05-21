# TIDAL (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports TIDAL's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the signature quality badge, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (TIDAL's pure-black void, white actions, fidelity-only cyan, square art, radical flatness) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, tonal-step surfaces instead of shadows, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/TidalColors.kt
import androidx.compose.ui.graphics.Color

object TidalColors {
    // Canvas & Surfaces (tonal steps — TIDAL uses these instead of shadows)
    val Canvas   = Color(0xFF000000)
    val Surface1 = Color(0xFF0A0A0A)
    val Surface2 = Color(0xFF1A1A1A)
    val Divider  = Color(0xFF262626)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF9A9A9A)
    val TextTertiary  = Color(0xFF5C5C5C)

    // Brand
    val White        = Color(0xFFFFFFFF) // action color
    val Cyan         = Color(0xFF00FFFF) // quality-tier badges ONLY
    val CyanPressed  = Color(0xFF00CCCC)
    val ErrorRed     = Color(0xFFFF453A)
}
```

Wire it into a Material 3 `darkColorScheme`. TIDAL is dark-only; do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val TidalScheme = darkColorScheme(
    primary        = TidalColors.White,       // white is the action color
    onPrimary      = TidalColors.Canvas,      // black on white
    background      = TidalColors.Canvas,
    onBackground    = TidalColors.TextPrimary,
    surface         = TidalColors.Surface1,
    onSurface       = TidalColors.TextPrimary,
    surfaceVariant  = TidalColors.Surface2,
    outline         = TidalColors.Divider,
    tertiary        = TidalColors.Cyan,        // reserved: quality badges only
    error           = TidalColors.ErrorRed,
)

@Composable
fun TidalTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = TidalScheme, typography = TidalTypography, content = content)
```

## 2. Typography

TIDAL's brand sans is proprietary. Drop the TTFs in `res/font/` (lowercase, snake_case), or use Space Grotesk as the closest free geometric grotesque; final fallback is the system font (Roboto).

```kotlin
// ui/theme/TidalType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val SpaceGrotesk = FontFamily(
    Font(R.font.space_grotesk_regular,  FontWeight.Normal),   // 400
    Font(R.font.space_grotesk_semibold, FontWeight.SemiBold), // 600
    Font(R.font.space_grotesk_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object TidalText {
    val TitleLarge  = TextStyle(SpaceGrotesk, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val NowPlaying  = TextStyle(SpaceGrotesk, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(SpaceGrotesk, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val AlbumTitle  = TextStyle(SpaceGrotesk, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val TrackTitle  = TextStyle(SpaceGrotesk, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 21.sp)
    val CardTitle   = TextStyle(SpaceGrotesk, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Subtitle    = TextStyle(SpaceGrotesk, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val Body        = TextStyle(SpaceGrotesk, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Meta        = TextStyle(SpaceGrotesk, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Badge       = TextStyle(SpaceGrotesk, fontWeight = FontWeight.Bold,     fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 1.0.sp)
    val LabelUpper  = TextStyle(SpaceGrotesk, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.8.sp)
    val Button      = TextStyle(SpaceGrotesk, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 18.sp, letterSpacing = 0.3.sp)
    val Tab         = TextStyle(SpaceGrotesk, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.3.sp)
    val Timestamp   = TextStyle(SpaceGrotesk, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val TidalTypography = Typography(
    headlineLarge = TidalText.TitleLarge,
    headlineSmall = TidalText.Section,
    titleMedium   = TidalText.TrackTitle,
    bodyMedium    = TidalText.Body,
    labelSmall    = TidalText.Tab,
)
```

## 3. Signature Components

### Quality-Tier Badge

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.semantics.contentDescription
import androidx.compose.ui.semantics.semantics
import androidx.compose.ui.unit.dp

enum class TidalQuality(val label: String) {
    MASTER("MASTER"), HIFI("HIFI"), MAX("MAX"), ATMOS("DOLBY ATMOS")
}

@Composable
fun QualityBadge(quality: TidalQuality, modifier: Modifier = Modifier) {
    Text(
        text = quality.label,
        style = TidalText.Badge,
        color = TidalColors.Cyan,
        modifier = modifier
            .border(1.dp, TidalColors.Cyan, RoundedCornerShape(4.dp))
            .padding(horizontal = 8.dp, vertical = 4.dp)
            .semantics { contentDescription = "${quality.label} quality" },
    )
}
```

### Primary Play Button (flat — no shadow)

```kotlin
@Composable
fun TidalPlayButton(
    isPlaying: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    size: Dp = 64.dp,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.93f else 1f, spring(dampingRatio = 0.75f), label = "play")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier
            .size(size)
            .scale(scale)
            .clip(CircleShape)
            .background(if (pressed) Color(0xFFE5E5E5) else TidalColors.White), // no shadow — flat
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            if (isPlaying) Icons.Filled.Pause else Icons.Filled.PlayArrow,
            contentDescription = if (isPlaying) "Pause" else "Play",
            tint = TidalColors.Canvas, // black on white
            modifier = Modifier.size(size * 0.4f),
        )
        // intentionally no .clickable indication ripple beyond default; attach onClick:
    }.also {
        // attach click + haptic
    }
}
```

Use this click-wired variant in practice:

```kotlin
@Composable
fun TidalPlayButton(isPlaying: Boolean, onClick: () -> Unit, size: Dp = 64.dp) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.93f else 1f, spring(dampingRatio = 0.75f), label = "play")
    val haptics = LocalHapticFeedback.current
    Box(
        Modifier
            .size(size).scale(scale).clip(CircleShape)
            .background(if (pressed) Color(0xFFE5E5E5) else TidalColors.White)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            if (isPlaying) Icons.Filled.Pause else Icons.Filled.PlayArrow,
            contentDescription = if (isPlaying) "Pause" else "Play",
            tint = TidalColors.Canvas,
            modifier = Modifier.size(size * 0.4f),
        )
    }
}
```

### Primary / Outline Pill

```kotlin
enum class PillStyle { Filled, Outline }

@Composable
fun TidalPill(text: String, onClick: () -> Unit, modifier: Modifier = Modifier, style: PillStyle = PillStyle.Filled) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "pill")
    val filled = style == PillStyle.Filled
    Box(
        modifier
            .scale(scale)
            .clip(CircleShape) // full pill
            .then(
                if (filled) Modifier.background(if (pressed) Color(0xFFE5E5E5) else TidalColors.White)
                else Modifier.border(1.dp, if (pressed) TidalColors.White else TidalColors.TextSecondary, CircleShape)
            )
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = if (filled) 32.dp else 24.dp, vertical = 11.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(
            text,
            style = if (filled) TidalText.Button else TidalText.Subtitle.copy(fontWeight = FontWeight.SemiBold),
            color = if (filled) TidalColors.Canvas else TidalColors.TextPrimary,
        )
    }
}
```

### Track Row (with quality badge + equalizer)

```kotlin
@Composable
fun TrackRow(
    title: String,
    artist: String,
    artworkUrl: String,
    duration: String,
    quality: TidalQuality?,
    isPlaying: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Row(
        modifier
            .fillMaxWidth()
            .height(60.dp)
            .background(if (pressed) TidalColors.Surface2 else TidalColors.Canvas)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box {
            AsyncImage(
                model = artworkUrl,
                contentDescription = null,
                modifier = Modifier.size(44.dp), // square — no clip/round
                contentScale = ContentScale.Crop,
            )
            if (isPlaying) Equalizer(Modifier.align(Alignment.CenterStart).padding(start = 4.dp))
        }
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(title, style = TidalText.TrackTitle, color = TidalColors.TextPrimary, maxLines = 1, overflow = TextOverflow.Ellipsis)
            Text(artist, style = TidalText.Subtitle, color = TidalColors.TextSecondary, maxLines = 1, overflow = TextOverflow.Ellipsis)
        }
        quality?.let { QualityBadge(it) }
        Text(duration, style = TidalText.Timestamp, color = TidalColors.TextSecondary)
        Icon(Icons.Filled.MoreHoriz, "More", tint = TidalColors.TextSecondary, modifier = Modifier.size(20.dp))
    }
}

@Composable
fun Equalizer(modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "eq")
    Row(modifier, horizontalArrangement = Arrangement.spacedBy(2.dp), verticalAlignment = Alignment.Bottom) {
        repeat(3) { i ->
            val h by t.animateFloat(0.3f, 1f, infiniteRepeatable(tween(360 + i * 120), RepeatMode.Reverse), label = "bar$i")
            Box(Modifier.width(3.dp).height((16 * h).dp).background(TidalColors.White))
        }
    }
}
```

## 4. Quality Tier Mapping

```kotlin
fun badgeFor(format: String): TidalQuality? = when (format) {
    "flac-24"     -> TidalQuality.MASTER
    "flac-16"     -> TidalQuality.HIFI
    "flac-24-max" -> TidalQuality.MAX
    "atmos"       -> TidalQuality.ATMOS
    else          -> null // no badge for lossy
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. TIDAL's iOS tab bar is `.regularMaterial` blur over near-black; Android has no first-class live blur, so use a 96%-opaque black surface. **Active tint is white** — cyan is fidelity-only.

```kotlin
@Composable
fun TidalBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = TidalColors.Canvas.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"          to Icons.Filled.Home,
            "Videos"        to Icons.Filled.Movie,
            "Search"        to Icons.Filled.Search,
            "My Collection" to Icons.Filled.LibraryMusic,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = TidalText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = TidalColors.White,
                    selectedTextColor   = TidalColors.White,
                    unselectedIconColor = TidalColors.TextSecondary,
                    unselectedTextColor = TidalColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // TIDAL has no Material pill
                ),
            )
        }
    }
}
```

The persistent **Now Playing mini-bar** sits directly above this bar: a 52.dp `Surface(color = TidalColors.Surface2)` row with a 1.dp `TidalColors.White` progress hairline pinned to its top edge — render it in the `Scaffold` `bottomBar` slot stacked above `TidalBottomBar`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Screen / now-playing transition | `fadeIn()` / `fadeOut()` crossfade only — no slide, no spring |
| Play/pause tap | `animateFloatAsState` 1 → 0.93 with `spring(dampingRatio = 0.75f)`, `HapticFeedbackType.LongPress` |
| Scrubber drag | Animate track thickness 1.dp → 3.dp; `HapticFeedbackType.TextHandleMove` ticks |
| Equalizer (now-playing row) | `rememberInfiniteTransition` driving 3 white bars' height, frozen when paused |
| Favorite tap | `Animatable` keyframes 1 → 1.12 → 1 over 260ms; `HapticFeedbackType.LongPress` |
| Mini-bar → full player | `Crossfade` on the screen + `SharedTransitionLayout` artwork that fades (not springs) |

```kotlin
// Crossfade navigation (TIDAL's editorial restraint — no slide)
Crossfade(targetState = route, animationSpec = tween(250), label = "nav") { r ->
    when (r) {
        Route.Home -> HomeScreen()
        Route.NowPlaying -> NowPlayingScreen()
        else -> {}
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For scrubber ticks, `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` (API 30+) approximates iOS selection feedback.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export TIDAL's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Next / Previous | `forward.end.fill` / `backward.end.fill` | `Icons.Filled.SkipNext` / `SkipPrevious` |
| Shuffle | `shuffle` | `Icons.Filled.Shuffle` |
| Repeat | `repeat` | `Icons.Filled.Repeat` |
| Favorite | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Add to collection | `plus.circle` | `Icons.Outlined.AddCircle` |
| Credits | `info.circle` | `Icons.Outlined.Info` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Videos (tab) | `play.rectangle.fill` | `Icons.Filled.Movie` |
| Collection (tab) | `square.stack.fill` | `Icons.Filled.LibraryMusic` |
| Cast | `airplayaudio` | `Icons.Filled.Cast` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the pure-black canvas wants light-content system bars. Apply `Scaffold` insets so the mini-bar clears gesture nav.
- **Font scaling**: `sp` honors the user's font scale — keep it on titles, artists, body. Pin quality badges, timestamps, and tab labels (exact geometry) by deriving size from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: include the quality tier in the row semantics ("…, Master quality, 3:08"); the `QualityBadge` already sets `contentDescription`.
- **Touch targets**: Material guidance is 48.dp minimum. The 64.dp play button clears it; give the 20.dp ellipsis and the 10.dp quality badge a 48.dp hit area via padding.
- **Contrast**: `#00FFFF` on `#000000` is AAA — safe for the 10sp badge. `#9A9A9A` on `#000000` passes AA at 14sp+; validate the 11sp meta/timestamp and lighten toward `#A8A8A8` if targeting strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — TIDAL's brand requires the fixed pure-black canvas and the white/cyan system regardless of wallpaper. There is no light theme.
