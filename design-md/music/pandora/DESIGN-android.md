# Pandora (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Pandora's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the signature thumb up/down, the album-art gradient Now Playing, and the station-list row, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps Pandora's *visual identity* (the thumb up/down as the Music Genome product, the solid-up / hollow-down asymmetry, the album-art gradient Now Playing with no card frame, the navy canvas from Pandora Blue, the station list as home) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush.verticalGradient` for the Now Playing hero, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for artwork, and `androidx.palette:palette` for sampling the gradient color. Pandora is dark-first; a light scheme is provided for settings/account flows only.

## 1. Color Tokens

```kotlin
// ui/theme/PandoraColors.kt
import androidx.compose.ui.graphics.Color

object PandoraColors {
    // Brand
    val Blue         = Color(0xFF224099) // heritage
    val Bright       = Color(0xFF3668FF) // THE accent
    val BrightPressed = Color(0xFF2A55D8)

    // Canvas & Surfaces (Dark — primary)
    val Canvas      = Color(0xFF0B0F1C) // navy from Pandora Blue, NOT neutral
    val Surface1    = Color(0xFF141A2B)
    val Surface2    = Color(0xFF1E2538)
    val Divider     = Color(0xFF28304A)
    val GradientMid = Color(0xFF1A2240)

    // Canvas & Surfaces (Light — settings/account only)
    val CanvasLight   = Color(0xFFFFFFFF)
    val Surface1Light = Color(0xFFF4F6FB)
    val DividerLight  = Color(0xFFE4E8F2)

    // Text
    val TextPrimary       = Color(0xFFEDF0F7)
    val TextSecondary     = Color(0xFF9AA3BD)
    val TextTertiary      = Color(0xFF646E8C)
    val TextPrimaryLight  = Color(0xFF11162A)
    val TextSecondaryLight = Color(0xFF5C6680)
    val OnBright = Color(0xFFFFFFFF)

    // Semantic
    val Success = Color(0xFF2BC48A)
    val Warning = Color(0xFFF2A93A)
    val Error   = Color(0xFFF0526B)
}
```

Wire it into both schemes. Pandora is dark-first; the canvas is the blue-navy `#0B0F1C` derived from Pandora Blue — never a neutral gray.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val PandoraDark = darkColorScheme(
    primary        = PandoraColors.Bright,     // accent / play / active thumb-up
    onPrimary      = PandoraColors.OnBright,
    background      = PandoraColors.Canvas,
    onBackground   = PandoraColors.TextPrimary,
    surface         = PandoraColors.Surface1,
    onSurface      = PandoraColors.TextPrimary,
    surfaceVariant = PandoraColors.Surface2,
    outline         = PandoraColors.Divider,
    error           = PandoraColors.Error,
)

private val PandoraLight = lightColorScheme(   // settings/account only
    primary        = PandoraColors.Bright,
    onPrimary      = PandoraColors.OnBright,
    background      = PandoraColors.CanvasLight,
    onBackground   = PandoraColors.TextPrimaryLight,
    surface         = PandoraColors.Surface1Light,
    onSurface      = PandoraColors.TextPrimaryLight,
    surfaceVariant = PandoraColors.Surface1Light,
    outline         = PandoraColors.DividerLight,
    error           = PandoraColors.Error,
)

@Composable
fun PandoraTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) PandoraDark else PandoraLight,
    typography  = PandoraTypography,
    content     = content,
)
```

## 2. Typography (M3)

Pandora's brand face is a clean grotesque. License it and drop the TTFs in `res/font/`, or ship **Inter** (SIL OFL) — keep the named ramp identical. Timecodes/counts are tabular (`fontFeatureSettings = "tnum"`).

```kotlin
// ui/theme/PandoraType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val PandoraSans = FontFamily(
    Font(R.font.pandora_regular,   FontWeight.Normal),
    Font(R.font.pandora_medium,    FontWeight.Medium),
    Font(R.font.pandora_semibold,  FontWeight.SemiBold),
    Font(R.font.pandora_bold,      FontWeight.Bold),
    Font(R.font.pandora_extrabold, FontWeight.ExtraBold),
)

private const val TNUM = "tnum"

// Named ramp — mirrors DESIGN.md §3 (pt → sp 1:1)
object PandoraText {
    val Display     = TextStyle(PandoraSans, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val ScreenTitle = TextStyle(PandoraSans, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val TrackTitle  = TextStyle(PandoraSans, fontWeight = FontWeight.ExtraBold, fontSize = 21.sp, lineHeight = 25.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(PandoraSans, fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(PandoraSans, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val StationName = TextStyle(PandoraSans, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(PandoraSans, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val AlbumLine   = TextStyle(PandoraSans, fontWeight = FontWeight.Medium,    fontSize = 13.sp, lineHeight = 18.sp)
    val Eyebrow     = TextStyle(PandoraSans, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 1.0.sp)
    val Time        = TextStyle(PandoraSans, fontWeight = FontWeight.Medium,    fontSize = 11.sp, lineHeight = 14.sp, fontFeatureSettings = TNUM)
    val Button      = TextStyle(PandoraSans, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.1.sp)
    val Tab         = TextStyle(PandoraSans, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val ThumbCount  = TextStyle(PandoraSans, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 14.sp, fontFeatureSettings = TNUM)
}

val PandoraTypography = Typography(
    displayLarge  = PandoraText.Display,
    headlineLarge = PandoraText.ScreenTitle,
    titleLarge    = PandoraText.TrackTitle,
    titleMedium   = PandoraText.Section,
    bodyLarge     = PandoraText.Body,
    labelSmall    = PandoraText.Tab,
)
```

## 3. Signature Components

### Thumb Up / Down (the signature atom)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.foundation.layout.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ThumbUp
import androidx.compose.material.icons.outlined.ThumbUp
import androidx.compose.material.icons.outlined.ThumbDown
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

enum class ThumbState { None, Up, Down }

@Composable
fun ThumbButton(kind: ThumbKind, state: ThumbState, onTap: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    var pop by remember { mutableStateOf(false) }
    val scale by animateFloatAsState(if (pop) 1.2f else 1f, tween(120), label = "thumbPop",
        finishedListener = { if (pop) pop = false })

    val active = (kind == ThumbKind.Up && state == ThumbState.Up) ||
                 (kind == ThumbKind.Down && state == ThumbState.Down)
    // HARD RULE: thumb-up may be SOLID Bright when active; thumb-down is ALWAYS an outline
    val filled = kind == ThumbKind.Up && active
    val icon = when {
        filled -> Icons.Filled.ThumbUp
        kind == ThumbKind.Up -> Icons.Outlined.ThumbUp
        else -> Icons.Outlined.ThumbDown            // never the filled thumbs-down
    }
    val tint = when {
        filled -> PandoraColors.Bright
        kind == ThumbKind.Up -> PandoraColors.TextTertiary
        active -> PandoraColors.TextSecondary       // active down: slightly brighter outline
        else -> PandoraColors.TextTertiary
    }

    Box(
        Modifier.size(44.dp).pointerInput(Unit) {
            detectTapGestures {
                pop = true
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // light analog
                onTap()
            }
        },
        contentAlignment = Alignment.Center,
    ) {
        Icon(icon, contentDescription = null, tint = tint, modifier = Modifier.size(26.dp).scale(scale))
    }
}

enum class ThumbKind { Up, Down }
```

### Now Playing (album-art gradient hero)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Text
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun NowPlayingScreen(
    artwork: String, title: String, artist: String, albumLine: String, stationName: String,
    artTopColor: Color, progress: Float, elapsed: String, remaining: String,
    isPlaying: Boolean, onPlayPause: () -> Unit,
    thumb: ThumbState, onUp: () -> Unit, onDown: () -> Unit,
) {
    Box(
        Modifier.fillMaxSize().background(
            Brush.verticalGradient(listOf(artTopColor, PandoraColors.GradientMid, PandoraColors.Canvas))
        )
    ) {
        Column(Modifier.fillMaxSize()) {
            // Top bar — floats over the gradient, NO card frame
            Row(
                Modifier.fillMaxWidth().padding(horizontal = 20.dp, vertical = 8.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.SpaceBetween,
            ) {
                Icon(Icons.Filled.KeyboardArrowDown, "Collapse", tint = PandoraColors.TextPrimary)
                Column(horizontalAlignment = Alignment.CenterHorizontally) {
                    Text("STATION", style = PandoraText.Eyebrow, color = PandoraColors.TextSecondary)
                    Text(stationName, style = PandoraText.AlbumLine.copy(fontWeight = FontWeight.Bold),
                        color = PandoraColors.TextPrimary)
                }
                Icon(Icons.Filled.MoreHoriz, "More", tint = PandoraColors.TextPrimary)
            }

            AsyncImage(
                model = artwork, contentDescription = "Album art",
                contentScale = ContentScale.Crop,
                modifier = Modifier
                    .padding(horizontal = 32.dp).padding(top = 26.dp)
                    .fillMaxWidth().aspectRatio(1f)
                    .shadow(25.dp, RoundedCornerShape(10.dp), spotColor = Color.Black.copy(alpha = 0.7f))
                    .clip(RoundedCornerShape(10.dp)),
            )

            Column(Modifier.padding(horizontal = 32.dp).padding(top = 22.dp)) {
                Text(title, style = PandoraText.TrackTitle, color = PandoraColors.TextPrimary)
                Text(artist, style = PandoraText.StationName.copy(fontWeight = FontWeight.Medium),
                    color = PandoraColors.TextSecondary, modifier = Modifier.padding(top = 4.dp))
                Text(albumLine, style = PandoraText.AlbumLine, color = PandoraColors.TextTertiary,
                    modifier = Modifier.padding(top = 2.dp))
            }

            // Scrubber
            Column(Modifier.padding(horizontal = 32.dp).padding(top = 20.dp)) {
                Box(Modifier.fillMaxWidth().height(4.dp).clip(RoundedCornerShape(2.dp))
                    .background(PandoraColors.Surface2)) {
                    Box(Modifier.fillMaxWidth(progress).fillMaxHeight()
                        .background(PandoraColors.Bright, RoundedCornerShape(2.dp)))
                }
                Row(Modifier.fillMaxWidth().padding(top = 8.dp),
                    horizontalArrangement = Arrangement.SpaceBetween) {
                    Text(elapsed, style = PandoraText.Time, color = PandoraColors.TextTertiary)
                    Text(remaining, style = PandoraText.Time, color = PandoraColors.TextTertiary)
                }
            }

            // Transport: thumb-down · skip-back · play · skip-forward · thumb-up
            Row(
                Modifier.fillMaxWidth().padding(horizontal = 36.dp).padding(top = 22.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.SpaceBetween,
            ) {
                ThumbButton(ThumbKind.Down, thumb, onDown)
                Icon(Icons.Filled.SkipPrevious, "Previous", tint = PandoraColors.TextPrimary,
                    modifier = Modifier.size(26.dp))
                Box(
                    Modifier.size(66.dp).clip(CircleShape).background(PandoraColors.Bright)
                        .shadow(13.dp, CircleShape, spotColor = PandoraColors.Bright.copy(alpha = 0.7f))
                        .pointerInput(Unit) { detectTapGestures { onPlayPause() } },
                    contentAlignment = Alignment.Center,
                ) {
                    Icon(if (isPlaying) Icons.Filled.Pause else Icons.Filled.PlayArrow,
                        "Play/Pause", tint = Color.White, modifier = Modifier.size(26.dp))
                }
                Icon(Icons.Filled.SkipNext, "Next", tint = PandoraColors.TextPrimary,
                    modifier = Modifier.size(26.dp))
                ThumbButton(ThumbKind.Up, thumb, onUp)
            }
        }
    }
}
```

### Station List Row

```kotlin
@Composable
fun StationRow(
    artwork: String, name: String, subtitle: String, isPlaying: Boolean,
    onClick: () -> Unit, modifier: Modifier = Modifier,
) {
    Row(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(12.dp))
            .background(if (isPlaying) PandoraColors.Surface2 else Color.Transparent)
            .pointerInput(Unit) { detectTapGestures { onClick() } }
            .padding(10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        AsyncImage(model = artwork, contentDescription = null, contentScale = ContentScale.Crop,
            modifier = Modifier.size(52.dp).clip(RoundedCornerShape(8.dp)))
        Column(Modifier.weight(1f)) {
            Text(name, style = PandoraText.StationName.copy(fontWeight = FontWeight.Bold),
                color = PandoraColors.TextPrimary)
            Text(subtitle, style = PandoraText.ThumbCount, color = PandoraColors.TextSecondary,
                modifier = Modifier.padding(top = 2.dp))
        }
        if (isPlaying) EqualizerBars()
    }
}

@Composable
fun EqualizerBars() {
    val transition = rememberInfiniteTransition(label = "eq")
    val heights = listOf(0.6f, 1f, 0.4f, 0.8f)
    Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(2.dp),
        modifier = Modifier.height(16.dp)) {
        heights.forEachIndexed { i, h ->
            val f by transition.animateFloat(
                initialValue = if (i % 2 == 0) 0.4f else 1f,
                targetValue = if (i % 2 == 0) 1f else 0.4f,
                animationSpec = infiniteRepeatable(tween(500), RepeatMode.Reverse), label = "bar$i",
            )
            Box(Modifier.width(3.dp).fillMaxHeight(h * f)
                .background(PandoraColors.Bright, RoundedCornerShape(1.dp)))
        }
    }
}
```

### Primary Button

```kotlin
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults

@Composable
fun PandPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Button(
        onClick = onClick,
        interactionSource = interaction,
        shape = RoundedCornerShape(50),     // pill — Pandora's CTA shape
        colors = ButtonDefaults.buttonColors(
            containerColor = if (pressed) PandoraColors.BrightPressed else PandoraColors.Bright,
            contentColor = PandoraColors.OnBright,
        ),
        modifier = modifier.height(50.dp),
    ) { Text(title, style = PandoraText.Button) }
}
```

## 4. Navigation

Pandora has a 4-tab bottom strip; active state is a text-primary tint with **no Material pill** (Pandora has none).

```kotlin
@Composable
fun PandoraBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = PandoraColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "For You" to Icons.Filled.Home,
            "Browse" to Icons.Filled.Search,
            "My Collection" to Icons.Filled.LibraryMusic,
            "Recents" to Icons.Filled.History,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, Modifier.size(22.dp)) },
                label = { Text(label, style = PandoraText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = PandoraColors.TextPrimary,   // text-primary active
                    selectedTextColor = PandoraColors.TextPrimary,
                    unselectedIconColor = PandoraColors.TextTertiary,
                    unselectedTextColor = PandoraColors.TextTertiary,
                    indicatorColor = Color.Transparent,               // no pill — Pandora has none
                ),
            )
        }
    }
}
```

The mini player is a pinned 56.dp bar above the `NavigationBar` (blurred-navy analog `Color(0xF20B0F1C)`, 0.5.dp top divider) holding 40.dp art + one-line title/artist + play/pause + thumb-up; tapping it expands into `NowPlayingScreen` via a shared-element/`AnimatedContent` bounds transform. Create-station, station-detail, and queue are `ModalBottomSheet`s.

## 5. Motion

Pandora motion is smooth and unhurried — track/gradient changes are 200–400ms cross-fades; nothing snappy.

| Moment | Compose recipe |
|--------|----------------|
| Thumb tap | `animateFloatAsState` scale 1 → 1.2 → 1 `tween(120)`; thumb-up cross-fades hollow→solid; light haptic; thumb-down also triggers skip |
| Track change | art `Crossfade`/`AnimatedContent` + scale 0.96 → 1.0 `tween(320)`; `artTopColor` `animateColorAsState(tween(400))`; title/artist `fadeIn/Out` 200ms |
| Skip forward/back | art `slideInHorizontally`/`slideOutHorizontally` then settle `tween(260, EaseOut)` |
| Play/pause | icon crossfade 150ms; button scale 1 → 0.96 → 1 on press |
| Scrubber drag | `Slider`/custom drag 1:1; timecodes recompose live (tabular, no shift) |
| Mini → Now Playing | shared-element art expand `tween(320, EaseOut)` + gradient `fadeIn` |
| Station start | `EqualizerBars` `rememberInfiniteTransition` fades in; push Now Playing `tween(300)` |
| Tab change | instant tint to text-primary; no indicator slide (no pill) |
| Bottom sheet | `ModalBottomSheet` slide + `tween(320)` scrim |
| Station created | success haptic |

```kotlin
// Gradient re-derive on track change — the canonical Pandora motion
val topColor by animateColorAsState(sampledArtColor, tween(400), label = "npGradient")
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the light tick on thumb up/down and play; a softer constant via `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` for skip; a stronger one on station-created.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The thumb-up uses `Icons.Filled.ThumbUp` (solid) only when active; the thumb-down uses `Icons.Outlined.ThumbDown` **always** — never the filled variant.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| For You (tab) | `house.fill` | `Icons.Filled.Home` |
| Browse (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| My Collection (tab) | `music.note.list` | `Icons.Filled.LibraryMusic` |
| Recents (tab) | `clock.fill` | `Icons.Filled.History` |
| Thumb up (active) | `hand.thumbsup.fill` | `Icons.Filled.ThumbUp` |
| Thumb up (idle) | `hand.thumbsup` | `Icons.Outlined.ThumbUp` |
| Thumb down (always outline) | `hand.thumbsdown` | `Icons.Outlined.ThumbDown` |
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Skip forward | `forward.fill` | `Icons.Filled.SkipNext` |
| Skip back | `backward.fill` | `Icons.Filled.SkipPrevious` |
| Collapse Now Playing | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Create station | `plus.circle.fill` | `Icons.Filled.AddCircle` |
| Shuffle | `shuffle` | `Icons.Filled.Shuffle` |
| Add variety | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Why this track | `info.circle` | `Icons.Filled.InfoOutline` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Brush` gradients, shared-element, infinite transitions comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the gradient extends under the system bars (light-content icons always — dark-first); pin the mini player + `NavigationBar` above the gesture area with `Modifier.navigationBarsPadding()`.
- **Tabular numerals**: keep `fontFeatureSettings = "tnum"` on timecodes, durations, and thumb counts (already in the ramp).
- **Album-art color sampling**: use `androidx.palette` (`Palette.from(bitmap).generate()` → `darkVibrantSwatch ?: dominantSwatch`) to derive the gradient top color; clamp it dark enough that `#EDF0F7` title text keeps ≥ 4.5:1 contrast; cache per track and animate the change with `animateColorAsState(tween(400))`.
- **Thumb asymmetry**: enforce in `ThumbButton` — `Icons.Filled.ThumbUp` only when up is active; `Icons.Outlined.ThumbDown` always. This is a hard rule, not a style choice.
- **Font scaling**: `sp` honors the user's font scale — keep it on display, screen title, track title, body, station name. Pin layout-critical text (tab labels, the eyebrow, timecodes, thumb counts) by deriving from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`; album art never scales.
- **TalkBack**: the thumb-up announces "Thumb up, {selected}, shapes this station"; the thumb-down "Thumb down, skips and tunes the station away" (distinct hints so the asymmetry is conveyed non-visually); the play button "Play / Pause"; the station row "{name}, {subtitle}, double-tap to play". Never rely on fill alone to convey thumb state.
- **Touch targets**: Material guidance is 48.dp. Give the 26.dp thumb glyphs a 44–48.dp hit and clear separation from skip/play; the play button is 66.dp; skip ≥ 44.dp; station row full-row; tab icons 22.dp in ≥48.dp.
- **Contrast**: `#EDF0F7` on `#0B0F1C` and white on `#3668FF` pass WCAG AA; verify the sampled gradient top color keeps title contrast ≥ 4.5:1 (darken the swatch if needed).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the art crossfade-scale, the thumb pop, and the equalizer (substitute a static 3-bar glyph); keep the gradient (it conveys the track).
- **Dark mode**: Pandora is dark-first — the canvas is the blue-navy `#0B0F1C` derived from Pandora Blue, NOT a neutral gray; `#3668FF` is constant. Do **not** enable Material You `dynamicColorScheme()` — Pandora's blue identity and the per-track album gradient must hold regardless of wallpaper.
