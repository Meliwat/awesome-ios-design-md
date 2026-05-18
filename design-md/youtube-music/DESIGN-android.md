# YouTube Music (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports YouTube Music's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the immersive Now Playing + Song/Video toggle + up-next shelf, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (near-black `#030303` canvas, art-glow backdrop, white play button, the Song/Video toggle, YT red as chrome-only) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` for the queue, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for artwork. No Palette extraction — the backdrop is the *blurred artwork itself*, not a derived swatch. YT Music is dark-only; do not provide a light scheme.

## 1. Color Tokens

```kotlin
// ui/theme/YTMColors.kt
import androidx.compose.ui.graphics.Color

object YTMColors {
    // Canvas & Surfaces (dark — the only mode)
    val Canvas      = Color(0xFF030303) // near-black — darker than Spotify on purpose
    val Surface1    = Color(0xFF1F1F1F)
    val Surface2    = Color(0xFF272727)
    val MiniSurface = Color(0xFF282828)
    val ChipBg      = Color(0xFF2A2A2A)
    val Divider     = Color(0xFF303030)
    val TabBar      = Color(0xFF0A0A0A)

    // Brand
    val Red         = Color(0xFFFF0000) // chrome only — never the play button
    val RedPressed  = Color(0xFFCC0000)
    val ActionWhite = Color(0xFFFFFFFF) // the play surface + primary text

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFAAAAAA)
    val TextTertiary  = Color(0xFF717171)

    // Semantic
    val Success = Color(0xFF2BA640)
    val Error   = Color(0xFFFF4E45)
}
```

Wire it into a **dark-only** scheme. YT Music has no light mode on iOS; mirror that.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme

private val YTMScheme = darkColorScheme(
    primary        = YTMColors.ActionWhite, // primary action surface is white
    onPrimary      = YTMColors.Canvas,
    background      = YTMColors.Canvas,
    onBackground    = YTMColors.TextPrimary,
    surface         = YTMColors.Surface1,
    onSurface       = YTMColors.TextPrimary,
    surfaceVariant  = YTMColors.Surface2,
    outline         = YTMColors.Divider,
    error           = YTMColors.Error,
    secondary       = YTMColors.Red,        // brand chrome accent
)

@Composable
fun YTMTheme(content: @Composable () -> Unit) = MaterialTheme(
    colorScheme = YTMScheme, // always dark — never branch on isSystemInDarkTheme()
    typography  = YTMTypography,
    content     = content,
)
```

## 2. Typography (M3)

YouTube Music uses **Roboto** across every YouTube product — drop the TTFs in `res/font/`, never substitute. Roboto is also Android's platform default, so this is the most native of the four music apps. Body 400, rows 500, headers/buttons 700, screen titles 900.

```kotlin
// ui/theme/YTMType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Roboto = FontFamily(
    Font(R.font.roboto_regular, FontWeight.Normal),
    Font(R.font.roboto_medium,  FontWeight.Medium),
    Font(R.font.roboto_bold,    FontWeight.Bold),
    Font(R.font.roboto_black,   FontWeight.Black),
)

object YTMText {
    val ScreenTitle = TextStyle(Roboto, fontWeight = FontWeight.Black,  fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val NowPlaying  = TextStyle(Roboto, fontWeight = FontWeight.Bold,   fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Roboto, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val CardHeader  = TextStyle(Roboto, fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Roboto, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 24.sp)
    val RowTitle    = TextStyle(Roboto, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 20.sp)
    val Subtitle    = TextStyle(Roboto, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 19.sp)
    val Toggle      = TextStyle(Roboto, fontWeight = FontWeight.Bold,   fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val Chip        = TextStyle(Roboto, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 13.sp)
    val Timestamp   = TextStyle(Roboto, fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 11.sp)
    val Eyebrow     = TextStyle(Roboto, fontWeight = FontWeight.Bold,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.6.sp)
    val Tab         = TextStyle(Roboto, fontWeight = FontWeight.Medium, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(Roboto, fontWeight = FontWeight.Bold,   fontSize = 15.sp, lineHeight = 15.sp)
}

val YTMTypography = Typography(
    headlineLarge = YTMText.ScreenTitle,
    headlineMedium = YTMText.Section,
    titleMedium   = YTMText.CardHeader,
    bodyMedium    = YTMText.Body,
    labelSmall    = YTMText.Tab,
)
```

## 3. Signature Components

### Immersive Now Playing (art-glow backdrop + art + meta)

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.background
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.blur
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import androidx.compose.foundation.shape.RoundedCornerShape
import coil.compose.AsyncImage

@Composable
fun NowPlaying(artUrl: String, track: String, artist: String) {
    Box(Modifier.fillMaxSize().background(YTMColors.Canvas)) {
        // Art-derived blurred backdrop glow (RenderEffect blur, API 31+; fallback below)
        AsyncImage(
            model = artUrl,
            contentDescription = null,
            modifier = Modifier
                .matchParentSize()
                .scale(1.4f)
                .blur(40.dp), // requires API 31+; for 24–30 pre-blur a downscaled bitmap
            contentScale = ContentScale.Crop,
        )
        Box(
            Modifier
                .matchParentSize()
                .background(
                    Brush.verticalGradient(
                        0.4f to Color.Transparent,
                        1f to YTMColors.Canvas,
                    )
                )
        )

        Column(Modifier.fillMaxSize(), horizontalAlignment = Alignment.CenterHorizontally) {
            Spacer(Modifier.height(26.dp))
            AsyncImage(
                model = artUrl,
                contentDescription = "Album art",
                modifier = Modifier.size(232.dp).clip(RoundedCornerShape(8.dp)),
                contentScale = ContentScale.Crop,
            )
            Spacer(Modifier.height(22.dp))
            SongVideoToggle()
            Column(Modifier.fillMaxWidth().padding(horizontal = 24.dp, vertical = 22.dp)) {
                Text(track, style = YTMText.NowPlaying, color = YTMColors.TextPrimary)
                Spacer(Modifier.height(4.dp))
                Text(artist, style = YTMText.Subtitle, color = YTMColors.TextSecondary)
            }
        }
    }
}
```

### Song / Video Toggle (Signature)

```kotlin
import androidx.compose.animation.animateColorAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun SongVideoToggle() {
    var mode by remember { mutableStateOf("song") }
    val haptics = LocalHapticFeedback.current

    Row(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(Color.White.copy(alpha = 0.08f))
            .padding(4.dp),
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        listOf("song" to "Song", "video" to "Video").forEach { (key, label) ->
            val active = mode == key
            val bg by animateColorAsState(
                if (active) YTMColors.ActionWhite else Color.Transparent,
                tween(200), label = "toggleBg",
            )
            Box(
                Modifier
                    .clip(RoundedCornerShape(999.dp))
                    .background(bg)
                    .clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) {
                        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                        mode = key
                    }
                    .padding(horizontal = 18.dp, vertical = 7.dp),
            ) {
                Text(label, style = YTMText.Toggle, color = if (active) YTMColors.Canvas else YTMColors.TextSecondary)
            }
        }
    }
}
```

### Scrubber (white at rest, red while dragging)

```kotlin
import androidx.compose.foundation.gestures.detectDragGestures
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.unit.dp

@Composable
fun Scrubber() {
    var progress by remember { mutableFloatStateOf(0.42f) }
    var dragging by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current

    Column {
        Canvas(
            Modifier
                .fillMaxWidth()
                .height(16.dp)
                .pointerInput(Unit) {
                    detectDragGestures(
                        onDragStart = { dragging = true },
                        onDragEnd = {
                            dragging = false
                            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                        },
                        onDrag = { change, _ ->
                            progress = (change.position.x / size.width).coerceIn(0f, 1f)
                        },
                    )
                }
        ) {
            val cy = size.height / 2
            drawLine(Color.White.copy(alpha = 0.22f), Offset(0f, cy), Offset(size.width, cy), strokeWidth = 3.dp.toPx())
            val tint = if (dragging) YTMColors.Red else YTMColors.ActionWhite
            drawLine(tint, Offset(0f, cy), Offset(size.width * progress, cy), strokeWidth = 3.dp.toPx())
            if (dragging) drawCircle(YTMColors.Red.copy(alpha = 0.18f), 14.dp.toPx(), Offset(size.width * progress, cy))
            drawCircle(tint, (if (dragging) 8 else 6).dp.toPx(), Offset(size.width * progress, cy))
        }
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
            Text("1:48", style = YTMText.Timestamp, color = YTMColors.TextSecondary)
            Text("-2:34", style = YTMText.Timestamp, color = YTMColors.TextSecondary)
        }
    }
}
```

### Transport Controls (white play button)

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.material3.IconButton

@Composable
fun Transport() {
    var playing by remember { mutableStateOf(true) }
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 32.dp),
        horizontalArrangement = Arrangement.SpaceBetween,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(Icons.Filled.SkipPrevious, "Previous", tint = YTMColors.TextPrimary, modifier = Modifier.size(22.dp))
        Icon(Icons.Filled.FastRewind, "Rewind", tint = YTMColors.TextPrimary, modifier = Modifier.size(26.dp))
        Box(
            Modifier.size(64.dp).clip(RoundedCornerShape(999.dp)).background(YTMColors.ActionWhite)
                .clickable { playing = !playing },
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                if (playing) Icons.Filled.Pause else Icons.Filled.PlayArrow,
                contentDescription = if (playing) "Pause" else "Play",
                tint = YTMColors.Canvas, // black glyph on white — never red
                modifier = Modifier.size(26.dp),
            )
        }
        Icon(Icons.Filled.FastForward, "Forward", tint = YTMColors.TextPrimary, modifier = Modifier.size(26.dp))
        Icon(Icons.Filled.SkipNext, "Next", tint = YTMColors.TextPrimary, modifier = Modifier.size(22.dp))
    }
}
```

### Up-Next Queue Shelf (ModalBottomSheet)

```kotlin
import androidx.compose.material3.ExperimentalMaterial3Api
import androidx.compose.material3.ModalBottomSheet
import androidx.compose.material3.rememberModalBottomSheetState
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun UpNextShelf(items: List<Pair<String, String>>, onDismiss: () -> Unit) {
    ModalBottomSheet(
        onDismissRequest = onDismiss,
        containerColor = YTMColors.Canvas,
        sheetState = rememberModalBottomSheetState(skipPartiallyExpanded = false),
    ) {
        Row(
            Modifier.fillMaxWidth().padding(16.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text("UP NEXT", style = YTMText.Eyebrow, color = YTMColors.TextSecondary)
            Icon(Icons.Filled.QueueMusic, "Queue", tint = YTMColors.TextSecondary, modifier = Modifier.size(20.dp))
        }
        LazyColumn {
            items(items) { (title, artist) ->
                Row(
                    Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 8.dp),
                    horizontalArrangement = Arrangement.spacedBy(10.dp),
                    verticalAlignment = Alignment.CenterVertically,
                ) {
                    Box(Modifier.size(40.dp).clip(RoundedCornerShape(4.dp)).background(YTMColors.Surface2))
                    Column(Modifier.weight(1f)) {
                        Text(title, style = YTMText.RowTitle, color = YTMColors.TextPrimary)
                        Text(artist, style = YTMText.Subtitle, color = YTMColors.TextSecondary)
                    }
                    Icon(Icons.Filled.DragHandle, "Reorder", tint = YTMColors.TextTertiary, modifier = Modifier.size(18.dp))
                }
            }
        }
    }
}
```

## 4. Core Navigation

YT Music has minimal chrome: a 4-tab bottom strip (Home / Samples / Explore / Library) and an immersive Now Playing reached by tapping the mini-player. Active is **pure white** — no Material tint pill, no red.

```kotlin
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun YTMBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = YTMColors.TabBar, tonalElevation = 0.dp) {
        val items = listOf(
            "Home" to Icons.Filled.Home,
            "Samples" to Icons.Filled.VideoLibrary,
            "Explore" to Icons.Filled.Search,
            "Library" to Icons.Filled.LibraryMusic,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = YTMText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = YTMColors.ActionWhite,   // white, not red
                    selectedTextColor = YTMColors.ActionWhite,
                    unselectedIconColor = YTMColors.TextTertiary,
                    unselectedTextColor = YTMColors.TextTertiary,
                    indicatorColor = Color.Transparent,          // no Material pill
                ),
            )
        }
    }
}
```

The mini-player sits directly above this bar (`MiniSurface` `#282828`, 8.dp corners, a 2.dp `ActionWhite` progress line at its bottom edge); tapping it pushes up the immersive Now Playing.

## 5. Motion

YT Music motion is purposeful and quick. Depth comes from the **art glow**, not Material elevation.

| Moment | Compose recipe |
|--------|----------------|
| Song ↔ Video toggle | active bg `animateColorAsState(tween(200))`; player body `AnimatedContent` cross-morph `tween(250)`; never restart playback |
| Now Playing present | mini-bar → full screen: `AnimatedVisibility` `slideInVertically(tween(320))` from bottom |
| Up-next shelf | `ModalBottomSheet` default spring + scrim; no custom timing needed |
| Scrubber drag | thumb radius + color swap driven by `dragging` state, ~`tween(80)` in / `tween(150)` out |
| Art backdrop crossfade | `Crossfade(targetState = artUrl, animationSpec = tween(400))` |
| Tab switch | instant icon swap — no slide, no pill animation |
| Samples feed | `VerticalPager` with snap, `spring(dampingRatio = 0.85f)` settle |

```kotlin
// Song ↔ Video — cross-morph the player surface
AnimatedContent(
    targetState = mode,
    transitionSpec = { fadeIn(tween(250)) togetherWith fadeOut(tween(250)) },
    label = "songVideo",
) { m -> if (m == "song") SquareArtPlayer() else VideoPlayer16x9() }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for the soft tick on toggle flip, scrubber touch-down/release, and queue-reorder commit. Track changes and auto-advance are silent.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. The block handle for queue reorder maps to `DragHandle`; the play button is white with a `Canvas`-colored glyph.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Samples (tab) | `rectangle.stack.fill` | `Icons.Filled.VideoLibrary` |
| Explore (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Library (tab) | `books.vertical.fill` | `Icons.Filled.LibraryMusic` |
| Dismiss player | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Overflow | `ellipsis` | `Icons.Filled.MoreVert` |
| Play / Pause | `play.fill` / `pause.fill` | `Icons.Filled.PlayArrow` / `Icons.Filled.Pause` |
| Next / Previous | `forward.fill` / `backward.fill` | `Icons.Filled.FastForward` / `Icons.Filled.FastRewind` |
| Skip end / start | `forward.end.fill` / `backward.end.fill` | `Icons.Filled.SkipNext` / `Icons.Filled.SkipPrevious` |
| Queue / up-next | `list.bullet` | `Icons.Filled.QueueMusic` |
| Drag handle | `line.3.horizontal` | `Icons.Filled.DragHandle` |
| Shuffle | `shuffle` | `Icons.Filled.Shuffle` |
| Repeat | `repeat` / `repeat.1` | `Icons.Filled.Repeat` / `Icons.Filled.RepeatOne` |
| Like | `hand.thumbsup.fill` | `Icons.Filled.ThumbUp` |
| Cast | `airplayaudio` | `Icons.Filled.Cast` |
| Download | `arrow.down.circle` | `Icons.Filled.Download` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (`compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`). `Modifier.blur()` requires **API 31+**; on 24–30 the art-glow backdrop must be a pre-blurred, downscaled bitmap (use `androidx.renderscript` Toolkit or a downsample+box-blur) so the immersive effect still reads
- **Edge-to-edge**: call `enableEdgeToEdge()`; the near-black canvas wants light-content system bars in both nav and status bar. The immersive Now Playing draws behind the status bar (the art glow extends to the top); inset only the top-bar controls
- **Dark-only**: never branch on `isSystemInDarkTheme()` and never enable `dynamicColorScheme()` — YT Music has no light mode and no wallpaper-harmonized accent; the `#FF0000` red and near-black canvas are fixed brand
- **Font scaling**: `sp` honors the user scale on screen title, section, body, row title; pin the Song/Video toggle label, 10sp tab labels, timestamps, and "UP NEXT" eyebrow via `dp`-derived sizing or a fixed-`fontScale` density so the player layout doesn't break
- **Roboto**: it is the Android platform default *and* the YouTube brand face — bundle the explicit weights anyway (Regular/Medium/Bold/Black) so the heavy 900 screen title renders correctly across OEM skins
- **TalkBack**: label the play button "Play"/"Pause"; the Song/Video toggle as "Song, selected"/"Video"; expose the scrubber via `Modifier.semantics { progressBarRangeInfo = ProgressBarRangeInfo(progress, 0f..1f) }` plus custom Seek-forward / Seek-back actions; mark the art-glow backdrop `Modifier.clearAndSetSemantics {}` (decorative)
- **Touch targets**: Material guidance is 48.dp — give the 22–26.dp transport icons and the 12–16.dp scrubber thumb a 48.dp hit area; the 64.dp play button already exceeds it; toggle segments get full-segment 48.dp-tall hit areas
- **Contrast**: `#FFFFFF` and `#AAAAAA` on `#030303` pass WCAG AA; `#717171` is for non-essential metadata only. The white play button's `#030303` glyph passes trivially — never recolor it red
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the art-backdrop `Crossfade` and the toggle slide (substitute an instant swap); keep the scrubber thumb size/color change (it conveys drag state)
- **Video mode**: render the 16:9 surface with Media3/ExoPlayer; the Song↔Video toggle swaps the rendering surface while keeping the same `Player` instance and position — do not recreate the session
