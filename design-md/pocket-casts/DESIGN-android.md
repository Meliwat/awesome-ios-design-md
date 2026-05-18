# Pocket Casts (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Pocket Casts' visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the per-podcast theme-tint `CompositionLocal`, paste-ready `@Composable`s (Now Playing player, episode row, mini-player), motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. Pocket Casts actually ships on Android too, and its identity — near-black `#1A1A1A` canvas, the single Red accent, **per-podcast theme tint sampled from cover art**, circular transport, pill buttons — ports cleanly. This file keeps that identity while making everything idiomatic Android: `NavigationBar` instead of a UITabBar, `Slider` for the scrubber, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for cover images, and `androidx.palette:palette-ktx` to extract the per-podcast tint. Pocket Casts is dark-first; a full light scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/PCColors.kt
import androidx.compose.ui.graphics.Color

object PCColors {
    // Brand
    val Red        = Color(0xFFF43E37)
    val RedPressed = Color(0xFFD32B25)

    // Canvas & Surfaces (Dark — canonical)
    val Canvas   = Color(0xFF1A1A1A) // near-black — NOT pure #000000
    val Surface1 = Color(0xFF232323)
    val Surface2 = Color(0xFF2E2E2E)
    val Divider  = Color(0xFF383838)

    // Canvas & Surfaces (Light)
    val CanvasLight  = Color(0xFFFFFFFF)
    val SurfaceLight = Color(0xFFF2F2F4)
    val DividerLight = Color(0xFFE2E2E4)

    // Text
    val TextPrimary    = Color(0xFFFFFFFF)
    val TextSecondary  = Color(0xFFB8B8B8)
    val TextTertiary   = Color(0xFF757575)
    val TextPrimaryLt  = Color(0xFF1A1A1A)
    val TextSecondaryLt= Color(0xFF6B6B6B)

    // Accent & semantic
    val InfoBlue    = Color(0xFF03A9F4)
    val PlayedGreen = Color(0xFF78D549)
    val StarGold    = Color(0xFFFBC02D)
    val WarnAmber   = Color(0xFFFFB300)

    // Theme-tint examples (sampled from cover art via Palette at runtime)
    val TintWarm   = Color(0xFFE0533C)
    val TintCool   = Color(0xFF3E7BC2)
    val TintGreen  = Color(0xFF3FA66A)
    val TintPurple = Color(0xFF7E57C2)
    val TintPink   = Color(0xFFD6457E)
}

// Per-podcast accent — provided at the player root, defaults to brand Red
val LocalPCThemeTint = androidx.compose.runtime.compositionLocalOf { PCColors.Red }
```

Wire it into both schemes. Pocket Casts is dark-first; the dark scheme uses the signature `#1A1A1A` charcoal, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val PCDark = darkColorScheme(
    primary        = PCColors.Red,
    onPrimary      = Color(0xFFFFFFFF),
    background      = PCColors.Canvas,
    onBackground    = PCColors.TextPrimary,
    surface         = PCColors.Surface1,
    onSurface       = PCColors.TextPrimary,
    surfaceVariant  = PCColors.Surface2,
    outline         = PCColors.Divider,
    error           = PCColors.Red,
)

private val PCLight = lightColorScheme(
    primary        = PCColors.Red,
    onPrimary      = Color(0xFFFFFFFF),
    background      = PCColors.CanvasLight,
    onBackground    = PCColors.TextPrimaryLt,
    surface         = PCColors.SurfaceLight,
    onSurface       = PCColors.TextPrimaryLt,
    surfaceVariant  = Color(0xFFE6E6E8),
    outline         = PCColors.DividerLight,
    error           = PCColors.Red,
)

@Composable
fun PCTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) PCDark else PCLight,
    typography  = PCTypography,
    content     = content,
)
```

## 2. Typography (M3)

Pocket Casts uses the platform system font (Roboto on Android stands in for SF Pro). Body 400, titles 700/800; timecodes use tabular figures.

```kotlin
// ui/theme/PCType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextGeometricTransform
import androidx.compose.ui.unit.sp

object PCText {
    val ScreenTitle = TextStyle(fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.4).sp)
    val NowPlaying  = TextStyle(fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Episode     = TextStyle(fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val RowTitle    = TextStyle(fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Body        = TextStyle(fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val ShowName    = TextStyle(fontWeight = FontWeight.Medium,    fontSize = 14.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Button      = TextStyle(fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Chip        = TextStyle(fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Tab         = TextStyle(fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Caption     = TextStyle(fontWeight = FontWeight.Normal,    fontSize = 12.sp, lineHeight = 16.sp)
    val Eyebrow     = TextStyle(fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.6.sp)
    // Tabular timecode — pair with String.uppercase() at call sites for eyebrows
    val Timecode    = TextStyle(fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 14.sp)
}

val PCTypography = Typography(
    headlineLarge = PCText.ScreenTitle,
    headlineMedium = PCText.NowPlaying,
    titleMedium    = PCText.Episode,
    bodyMedium     = PCText.Body,
    labelSmall     = PCText.Tab,
)
```

> For tabular digits, render timecodes with `androidx.compose.ui.text.style.TextGeometricTransform` is not needed — instead use a `FontFamily` with `"tnum"` features via `Font(..., variationSettings)` on API 26+, or simply right-align in a fixed-width box so layout never shifts while scrubbing.

## 3. Signature Components

### Per-Podcast Theme Tint (Palette extraction + cross-fade)

```kotlin
import androidx.compose.animation.animateColorAsState
import androidx.compose.animation.core.tween
import androidx.compose.runtime.*
import androidx.palette.graphics.Palette

@Composable
fun rememberPodcastTint(bitmap: android.graphics.Bitmap?): Color {
    var raw by remember { mutableStateOf(PCColors.Red) }
    LaunchedEffect(bitmap) {
        bitmap ?: return@LaunchedEffect
        val swatch = Palette.from(bitmap).generate().vibrantSwatch
        raw = swatch?.let { Color(it.rgb) } ?: PCColors.Red
    }
    // cross-fade, never a hard cut
    val tint by animateColorAsState(raw, tween(250), label = "pcTint")
    return tint
}
```

### Now Playing Player

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.unit.dp

@Composable
fun NowPlaying(episodeTitle: String, showName: String, modifier: Modifier = Modifier) {
    val tint = LocalPCThemeTint.current
    var progress by remember { mutableFloatStateOf(0.42f) }
    var playing by remember { mutableStateOf(true) }

    Column(modifier.fillMaxSize().background(PCColors.Canvas)) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 20.dp, vertical = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            Icon(Icons.Filled.KeyboardArrowDown, null, tint = PCColors.TextPrimary)
            Text("NOW PLAYING", style = PCText.Eyebrow.copy(letterSpacing = 1.sp), color = PCColors.TextSecondary)
            Icon(Icons.Filled.MoreHoriz, null, tint = PCColors.TextPrimary)
        }

        Box(
            Modifier.padding(horizontal = 36.dp, vertical = 8.dp)
                .fillMaxWidth().aspectRatio(1f)
                .clip(RoundedCornerShape(8.dp))
                .background(Brush.linearGradient(listOf(tint, tint.copy(alpha = 0.55f)))),
            contentAlignment = Alignment.Center,
        ) {
            Box(Modifier.size(130.dp).clip(CircleShape)
                .border(7.dp, Color.White.copy(alpha = 0.92f), CircleShape))
        }

        Column(Modifier.padding(horizontal = 36.dp)) {
            Text(episodeTitle, style = PCText.NowPlaying, color = PCColors.TextPrimary)
            Text(showName, style = PCText.ShowName, color = tint, modifier = Modifier.padding(top = 4.dp))
        }

        Column(Modifier.padding(horizontal = 30.dp, vertical = 6.dp)) {
            Slider(
                value = progress, onValueChange = { progress = it },
                colors = SliderDefaults.colors(
                    thumbColor = Color.White,
                    activeTrackColor = tint,
                    inactiveTrackColor = PCColors.Surface2,
                ),
            )
            Row(Modifier.fillMaxWidth().padding(horizontal = 6.dp), horizontalArrangement = Arrangement.SpaceBetween) {
                Text("14:22", style = PCText.Timecode, color = PCColors.TextSecondary)
                Text("-19:48", style = PCText.Timecode, color = PCColors.TextSecondary)
            }
        }

        Row(
            Modifier.fillMaxWidth().padding(horizontal = 40.dp, vertical = 18.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            Icon(Icons.Filled.Replay, "Back 15", tint = PCColors.TextPrimary, modifier = Modifier.size(24.dp))
            Icon(Icons.Filled.SkipPrevious, "Previous", tint = PCColors.TextPrimary, modifier = Modifier.size(28.dp))
            Box(
                Modifier.size(68.dp).clip(CircleShape).background(tint)
                    .clickable { playing = !playing },
                contentAlignment = Alignment.Center,
            ) {
                Icon(if (playing) Icons.Filled.Pause else Icons.Filled.PlayArrow, "Play/Pause",
                    tint = Color.White, modifier = Modifier.size(26.dp))
            }
            Icon(Icons.Filled.SkipNext, "Next", tint = PCColors.TextPrimary, modifier = Modifier.size(28.dp))
            Icon(Icons.Filled.Forward30, "Forward 30", tint = PCColors.TextPrimary, modifier = Modifier.size(24.dp))
        }

        Row(
            Modifier.fillMaxWidth().padding(horizontal = 28.dp).padding(bottom = 14.dp),
            horizontalArrangement = Arrangement.SpaceAround,
        ) {
            Chip(Icons.Filled.QueueMusic, "Up Next", tint, active = true)
            Chip(Icons.Filled.Speed, "1.2×", tint, active = false)
            Chip(Icons.Filled.GraphicEq, "Trim", tint, active = true)
            Chip(Icons.Filled.StarBorder, "Star", tint, active = false)
        }
    }
}

@Composable
private fun Chip(icon: androidx.compose.ui.graphics.vector.ImageVector, label: String, tint: Color, active: Boolean) {
    val c = if (active) tint else PCColors.TextSecondary
    Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(5.dp)) {
        Icon(icon, label, tint = c, modifier = Modifier.size(19.dp))
        Text(label, style = PCText.Chip, color = c)
    }
}
```

### Episode List Row

```kotlin
@Composable
fun EpisodeRow(
    dateLabel: String, title: String, meta: String,
    artwork: Brush = Brush.linearGradient(listOf(Color(0xFFE0533C), Color(0xFF7E2018))),
) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Box(Modifier.size(52.dp).clip(RoundedCornerShape(6.dp)).background(artwork))
        Column(Modifier.weight(1f)) {
            Text(dateLabel.uppercase(), style = PCText.Eyebrow, color = PCColors.TextTertiary)
            Text(title, style = PCText.RowTitle, color = PCColors.TextPrimary, maxLines = 1)
            Text(meta, style = PCText.Meta, color = PCColors.TextSecondary)
        }
        Box(
            Modifier.size(30.dp).clip(CircleShape)
                .border(1.5.dp, PCColors.TextSecondary, CircleShape),
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.PlayArrow, "Play", tint = PCColors.TextPrimary, modifier = Modifier.size(12.dp)) }
    }
    HorizontalDivider(color = PCColors.Divider)
}
```

### Pill Buttons

```kotlin
@Composable
fun PCPrimaryButton(title: String, onClick: () -> Unit) {
    Button(
        onClick = onClick,
        shape = CircleShape,
        colors = ButtonDefaults.buttonColors(containerColor = PCColors.Red, contentColor = Color.White),
        contentPadding = PaddingValues(horizontal = 28.dp, vertical = 14.dp),
    ) { Text(title, style = PCText.Button) }
}

@Composable
fun PCTintButton(title: String, onClick: () -> Unit) {
    val tint = LocalPCThemeTint.current
    Button(
        onClick = onClick,
        shape = CircleShape,
        colors = ButtonDefaults.buttonColors(containerColor = tint, contentColor = Color.White),
        contentPadding = PaddingValues(horizontal = 28.dp, vertical = 14.dp),
    ) { Text(title, style = PCText.Button) }
}

@Composable
fun PCOutlineButton(title: String, onClick: () -> Unit) {
    OutlinedButton(
        onClick = onClick,
        shape = CircleShape,
        border = BorderStroke(1.5.dp, PCColors.TextSecondary),
        colors = ButtonDefaults.outlinedButtonColors(contentColor = PCColors.TextPrimary),
        contentPadding = PaddingValues(horizontal = 22.dp, vertical = 12.dp),
    ) { Text(title, style = PCText.RowTitle) }
}
```

### Trim Silence Toggle Row

```kotlin
@Composable
fun PCEffectRow(title: String, subtitle: String, checked: Boolean, onCheckedChange: (Boolean) -> Unit) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.SpaceBetween,
    ) {
        Column(Modifier.weight(1f)) {
            Text(title, style = PCText.RowTitle, color = PCColors.TextPrimary)
            Text(subtitle, style = PCText.Caption, color = PCColors.TextSecondary)
        }
        Switch(
            checked = checked, onCheckedChange = onCheckedChange,
            colors = SwitchDefaults.colors(
                checkedThumbColor = Color.White,
                checkedTrackColor = PCColors.Red,
                uncheckedThumbColor = Color.White,
                uncheckedTrackColor = PCColors.Surface2,
            ),
        )
    }
    HorizontalDivider(color = PCColors.Divider)
}
```

### Mini-Player

```kotlin
@Composable
fun PCMiniPlayer(title: String, show: String, playing: Boolean, onToggle: () -> Unit) {
    val tint = LocalPCThemeTint.current
    Column {
        Box(Modifier.fillMaxWidth().height(2.dp).background(tint))
        Row(
            Modifier.fillMaxWidth().height(64.dp).background(PCColors.Surface1)
                .padding(horizontal = 14.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(Modifier.size(44.dp).clip(RoundedCornerShape(6.dp)).background(tint))
            Column(Modifier.weight(1f)) {
                Text(title, style = PCText.RowTitle, color = PCColors.TextPrimary, maxLines = 1)
                Text(show, style = PCText.Caption, color = PCColors.TextSecondary, maxLines = 1)
            }
            Icon(
                if (playing) Icons.Filled.Pause else Icons.Filled.PlayArrow, "Play/Pause",
                tint = PCColors.TextPrimary,
                modifier = Modifier.size(22.dp).clickable { onToggle() },
            )
        }
    }
}
```

## 4. Core Navigation (Bottom Bar)

Pocket Casts has a 4-tab bottom strip. No tint pill — active is just brand Red.

```kotlin
@Composable
fun PCBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = PCColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Podcasts" to Icons.Filled.GridView,
            "Filters"  to Icons.Filled.FilterList,
            "Discover" to Icons.Filled.Explore,
            "Profile"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = PCText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = PCColors.Red,
                    selectedTextColor = PCColors.Red,
                    unselectedIconColor = PCColors.TextTertiary,
                    unselectedTextColor = PCColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Pocket Casts has none
                ),
            )
        }
    }
}
```

## 5. Navigation

Use Navigation Compose / Nav3 with four destinations (Podcasts, Filters, Discover, Profile) and a modal `NowPlaying` route presented as a bottom sheet that expands from the mini-player. Page transitions slide horizontally `tween(300)`; the mini-player → full-player transition is a `slideInVertically` from the bottom `tween(300)` with the artwork shared-element transitioning into the large cover.

## 6. Motion

Pocket Casts motion is quiet — 150–300ms ease ranges. The only "glow" is the theme-tinted shadow under the circular play button.

| Moment | Compose recipe |
|--------|----------------|
| Play/pause | icon swap `Crossfade(playing, tween(200))`; press scale `0.94` via `Modifier.scale(animateFloatAsState)` |
| Scrubber drag | `Slider` thumb; grow effect via `interactionSource` pressed → thumb radius animate |
| Theme-tint cross-fade | `animateColorAsState(rawTint, tween(250))` — never a hard cut |
| Mini → full player | `slideInVertically(tween(300))` + shared-element artwork |
| Episode-row swipe | `SwipeToDismissBox` 1:1 reveal, 50% commit, `spring(dampingRatio = 0.85f)` |
| Up Next reorder | `LazyColumn` + `Modifier.draggable`; neighbors `animateItemPlacement(tween(200))`; soft haptic on drop |
| Page navigation | `NavHost` slide push `tween(300)` |
| Download complete | tick `scaleIn(spring(stiffness = 600f))` in `#78D549` |

```kotlin
// Theme-tint cross-fade — the canonical Pocket Casts motion
val tint by animateColorAsState(rawTint, tween(250), label = "pcTint")

// Play button press
val scale by animateFloatAsState(if (pressed) 0.94f else 1f, tween(120), label = "playScale")
```

Haptics: use `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on play, scrubber boundary ticks, Up Next reorder pickup/drop, and toggle flips. Auto-progress save is silent — no haptic.

## 7. Icons

Pocket Casts uses simple stroked glyphs with circular play buttons; `androidx.compose.material:material-icons-extended` covers all of them.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Podcasts (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Filters (tab) | `line.3.horizontal.decrease` | `Icons.Filled.FilterList` |
| Discover (tab) | `safari` | `Icons.Filled.Explore` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Play / Pause | `play.fill` / `pause.fill` | `Icons.Filled.PlayArrow` / `Icons.Filled.Pause` |
| Previous / Next | `backward.fill` / `forward.fill` | `Icons.Filled.SkipPrevious` / `Icons.Filled.SkipNext` |
| Skip back 15 | `gobackward.15` | `Icons.Filled.Replay` |
| Skip forward 30 | `goforward.30` | `Icons.Filled.Forward30` |
| Up Next | `list.bullet` | `Icons.Filled.QueueMusic` |
| Playback speed | `speedometer` | `Icons.Filled.Speed` |
| Trim Silence | `waveform` | `Icons.Filled.GraphicEq` |
| Volume Boost | `speaker.wave.3.fill` | `Icons.Filled.VolumeUp` |
| Star | `star` / `star.fill` | `Icons.Filled.StarBorder` / `Icons.Filled.Star` |
| Mark played | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Download / Downloaded | `arrow.down.circle` / `.fill` | `Icons.Filled.Download` / `Icons.Filled.DownloadDone` |
| Collapse player | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Reorder handle | `line.3.horizontal` | `Icons.Filled.DragHandle` |
| AirPlay / Cast | `airplayaudio` | `Icons.Filled.Cast` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Slider`, `Palette`, modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the dark canonical theme wants light-content system bars. The mini-player sits directly above the `NavigationBar` inside the navigation-bar inset.
- **Per-podcast tint**: extract via `Palette.from(bitmap).generate().vibrantSwatch`; fall back to `PCColors.Red` when null or when contrast against `#1A1A1A` is below 3:1. Cross-fade with `animateColorAsState(tween(250))` — never a hard cut.
- **Tabular timecodes**: render durations/timecodes in a fixed-width container (or `tnum` font feature on API 26+) so digits never shift while scrubbing.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, episode titles, body, metadata. Pin layout-sensitive text (10sp tab labels, chip labels, timecodes, date eyebrows) via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: label the play button "Play" / "Pause, {episode}"; the `Slider` exposes a percentage `stateDescription` — add custom actions for "Skip forward 30 seconds" / "Skip back 15 seconds"; episode rows announce "Episode: {title}, {meta}, {date}".
- **Touch targets**: Material guidance is 48.dp. The 68.dp play button is fine; give 24–30.dp transport/episode-row controls a 48.dp hit via padding; tab items are 48.dp by default.
- **Contrast**: `#FFFFFF` on `#1A1A1A` passes WCAG AAA. The per-podcast tint must keep ≥3:1 on `#1A1A1A` for the play button and ≥4.5:1 for the tinted show-name text — validate the sampled swatch, fall back to Red on failure.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the theme-tint cross-fade (instant), the scrubber-thumb grow, and the play/pause crossfade; keep state changes.
- **Dark mode**: the canonical theme — `#1A1A1A`, NOT true black; `#FFFFFF` text. Do **not** enable Material You `dynamicColorScheme()` — Pocket Casts' Red + per-podcast-tint identity must hold regardless of wallpaper.
