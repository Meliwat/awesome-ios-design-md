# Deezer (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Deezer's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the living-gradient Flow artwork, the embedded equalizer, the circular gradient play button, the gradient scrubber, the now-playing-aware song row, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Deezer's violet near-black canvas, the scarce purple→pink gradient, the living Flow artwork with embedded equalizer) while making everything idiomatic Android — a `NavigationBar` instead of a UITabBar, `Brush.linearGradient` instead of SwiftUI `LinearGradient`, `dp`/`sp` instead of `pt`, and `LocalHapticFeedback` instead of `sensoryFeedback`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for any remote artwork. No Material You dynamic color — Deezer's gradient identity must hold regardless of wallpaper. Deezer is dark-native; the dark scheme is primary.

## 1. Color Tokens

```kotlin
// ui/theme/DeezerColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.geometry.Offset

object DeezerColors {
    // Canvas & Surfaces (Dark — the only real theme)
    val Canvas     = Color(0xFF0F0D13) // violet-tinted near-black — NOT pure black
    val Surface1   = Color(0xFF19161F)
    val Surface2   = Color(0xFF221E2B)
    val Divider    = Color(0xFF2A2633)
    val Scrim      = Color(0xD90F0D13)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFA29CB0)
    val TextTertiary  = Color(0xFF6E6880)

    // Brand (the gradient + its endpoints)
    val Purple     = Color(0xFFA238FF)
    val Pink       = Color(0xFFFF0092)
    val PurpleDeep = Color(0xFF7C28C4)
    val PinkPress  = Color(0xFFD60079)
    val ArtMagenta = Color(0xFFC71F8E)

    // Semantic
    val Success = Color(0xFF1ED760)
    val Error   = Color(0xFFFF4D5E)
    val Warning = Color(0xFFFFB02E)

    // The Deezer signature gradient — ONLY on alive elements
    val FlowBrush = Brush.linearGradient(listOf(Purple, Pink))
    val PlayBrush = Brush.linearGradient(
        colors = listOf(Purple, Pink),
        start = Offset(0f, 0f), end = Offset.Infinite,
    )
    val ArtworkBrush = Brush.linearGradient(
        0f to Purple, 0.45f to ArtMagenta, 1f to Pink,
        start = Offset(0f, 0f), end = Offset.Infinite,
    )
}
```

Wire it into both schemes. Deezer is dark-first; the dark scheme uses the signature `#0F0D13` violet near-black, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.runtime.Composable

private val DeezerDark = darkColorScheme(
    primary        = DeezerColors.Purple,
    onPrimary      = DeezerColors.TextPrimary,
    background     = DeezerColors.Canvas,
    onBackground   = DeezerColors.TextPrimary,
    surface        = DeezerColors.Surface1,
    onSurface      = DeezerColors.TextPrimary,
    surfaceVariant = DeezerColors.Surface2,
    outline        = DeezerColors.Divider,
    error          = DeezerColors.Error,
)

// Fallback only — Deezer ships no real light theme.
private val DeezerLightFallback = lightColorScheme(
    primary    = DeezerColors.Purple,
    background = Color(0xFFFFFFFF),
    surface    = Color(0xFFF4F2F7),
    onBackground = Color(0xFF15121C),
)

@Composable
fun DeezerTheme(content: @Composable () -> Unit) = MaterialTheme(
    colorScheme = DeezerDark,           // pin dark — Deezer's identity
    typography  = DeezerTypography,
    content     = content,
)
```

## 2. Typography

Deezer ships **Deezer Sans**; use **Inter** as the faithful fallback (SIL OFL — drop the TTFs in `res/font/`). Body/metadata uses Inter at 400/500; titles use the brand face at 700–800. Bold by default — the dark canvas needs heavy anchors.

```kotlin
// ui/theme/DeezerType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

// Swap deezer_sans_* → inter_* if the brand face isn't licensed.
val DeezerSans = FontFamily(
    Font(R.font.deezer_sans_semibold,  FontWeight.SemiBold),
    Font(R.font.deezer_sans_bold,      FontWeight.Bold),
    Font(R.font.deezer_sans_extrabold, FontWeight.ExtraBold),
)
val Inter = FontFamily(
    Font(R.font.inter_regular, FontWeight.Normal),
    Font(R.font.inter_medium,  FontWeight.Medium),
)

object DeezerText {
    val ScreenTitle = TextStyle(DeezerSans, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val NowPlaying  = TextStyle(DeezerSans, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section     = TextStyle(DeezerSans, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Subhead     = TextStyle(DeezerSans, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Inter,      fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val RowTitle    = TextStyle(DeezerSans, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val NowArtist   = TextStyle(Inter,      fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(Inter,      fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Overline    = TextStyle(DeezerSans, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.4.sp)
    val ScrubTime   = TextStyle(Inter,      fontWeight = FontWeight.Medium,    fontSize = 11.sp, lineHeight = 11.sp)
    val TabLabel    = TextStyle(DeezerSans, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(DeezerSans, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Chip        = TextStyle(DeezerSans, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
}

val DeezerTypography = Typography(
    headlineLarge  = DeezerText.ScreenTitle,
    headlineMedium = DeezerText.NowPlaying,
    titleLarge     = DeezerText.Section,
    bodyLarge      = DeezerText.Body,
    labelSmall     = DeezerText.TabLabel,
)
```

## 3. Signature Components

### Living-Gradient Flow Artwork (with FLOW badge + embedded equalizer)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Bolt
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.unit.dp

private val EQ_BARS = listOf(0.38f,0.62f,0.88f,0.54f,0.76f,0.42f,0.68f,0.92f,0.50f,0.72f,0.34f,0.60f)

@Composable
fun FlowArtwork(isPlaying: Boolean = true, modifier: Modifier = Modifier) {
    Box(
        modifier
            .fillMaxWidth()
            .aspectRatio(1f)
            .shadow(24.dp, RoundedCornerShape(16.dp), spotColor = DeezerColors.Purple, ambientColor = DeezerColors.Purple)
            .clip(RoundedCornerShape(16.dp))
            .background(DeezerColors.ArtworkBrush),
    ) {
        // soft white bloom top-right (decorative)
        Box(
            Modifier
                .matchParentSize()
                .background(
                    Brush.radialGradient(
                        colors = listOf(Color.White.copy(alpha = 0.22f), Color.Transparent),
                        center = Offset(0.78f, 0.22f), radius = 600f,
                    )
                )
        )

        // FLOW badge
        Row(
            Modifier
                .align(Alignment.TopStart)
                .padding(16.dp)
                .clip(RoundedCornerShape(999.dp))
                .background(DeezerColors.Canvas.copy(alpha = 0.55f))
                .padding(vertical = 7.dp, horizontal = 12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(7.dp),
        ) {
            Icon(Icons.Filled.Bolt, null, tint = Color.White, modifier = Modifier.size(12.dp))
            Text("FLOW", style = DeezerText.Overline, color = Color.White)
        }

        // Embedded equalizer
        Row(
            Modifier
                .align(Alignment.BottomCenter)
                .fillMaxWidth()
                .fillMaxHeight(0.46f)
                .padding(start = 18.dp, end = 18.dp, bottom = 22.dp),
            verticalAlignment = Alignment.Bottom,
            horizontalArrangement = Arrangement.spacedBy(4.dp),
        ) {
            EQ_BARS.forEach { h ->
                Box(
                    Modifier
                        .weight(1f)
                        .fillMaxHeight(h)
                        .clip(RoundedCornerShape(topStart = 3.dp, topEnd = 3.dp))
                        .background(Color.White.copy(alpha = 0.62f))
                )
            }
        }
    }
}
```

### Circular Gradient Play Button

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.Pause
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun PlayButton(playing: Boolean, onToggle: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.94f else 1f, tween(120), label = "playScale")
    val haptics = LocalHapticFeedback.current

    Box(
        Modifier
            .size(68.dp)
            .scale(scale)
            .shadow(14.dp, CircleShape, spotColor = DeezerColors.Pink, ambientColor = DeezerColors.Pink)
            .clip(CircleShape)
            .background(DeezerColors.PlayBrush)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onToggle()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            if (playing) Icons.Filled.Pause else Icons.Filled.PlayArrow,
            contentDescription = if (playing) "Pause" else "Play",
            tint = Color.White,
            modifier = Modifier.size(26.dp).offset(x = if (playing) 0.dp else 2.dp),
        )
    }
}
```

### Gradient Scrubber

```kotlin
import androidx.compose.foundation.gestures.detectHorizontalDragGestures
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.layout.onSizeChanged

@Composable
fun GradientScrubber(progress: Float, elapsed: String, remaining: String, onSeek: (Float) -> Unit) {
    var widthPx by remember { mutableStateOf(1f) }
    var dragging by remember { mutableStateOf(false) }
    val knob = if (dragging) 17.dp else 13.dp

    Column {
        Box(
            Modifier
                .fillMaxWidth()
                .height(17.dp)
                .onSizeChanged { widthPx = it.width.toFloat() }
                .pointerInput(Unit) {
                    detectHorizontalDragGestures(
                        onDragStart = { dragging = true },
                        onDragEnd = { dragging = false },
                    ) { change, _ -> onSeek((change.position.x / widthPx).coerceIn(0f, 1f)) }
                },
            contentAlignment = Alignment.CenterStart,
        ) {
            Box(Modifier.fillMaxWidth().height(4.dp).clip(RoundedCornerShape(2.dp)).background(DeezerColors.Surface2))
            Box(Modifier.fillMaxWidth(progress).height(4.dp).clip(RoundedCornerShape(2.dp)).background(DeezerColors.FlowBrush))
            Box(
                Modifier
                    .offset { IntOffset((progress * widthPx - with(LocalDensity.current) { knob.toPx() / 2 }).toInt(), 0) }
                    .size(knob)
                    .clip(CircleShape)
                    .background(Color.White)
            )
        }
        Row(Modifier.fillMaxWidth().padding(top = 9.dp), horizontalArrangement = Arrangement.SpaceBetween) {
            Text(elapsed, style = DeezerText.ScrubTime, color = DeezerColors.TextSecondary)
            Text(remaining, style = DeezerText.ScrubTime, color = DeezerColors.TextSecondary)
        }
    }
}
```

### Now-Playing-Aware Song Row

```kotlin
import androidx.compose.animation.animateColorAsState

@Composable
fun SongRow(title: String, artist: String, isPlaying: Boolean) {
    val titleColor by animateColorAsState(
        if (isPlaying) DeezerColors.Pink else DeezerColors.TextPrimary, tween(180), label = "rowTitle"
    )
    Row(
        Modifier.fillMaxWidth().height(64.dp).padding(horizontal = 24.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(44.dp).clip(RoundedCornerShape(6.dp)).background(DeezerColors.ArtworkBrush))
        Column(Modifier.weight(1f)) {
            Text(title, style = DeezerText.RowTitle, color = titleColor)
            Text(artist, style = DeezerText.Meta, color = DeezerColors.TextSecondary)
        }
        if (isPlaying) EqualizerMark() else Icon(
            Icons.Filled.MoreHoriz, contentDescription = "More", tint = DeezerColors.TextSecondary, modifier = Modifier.size(18.dp)
        )
    }
}

@Composable
fun EqualizerMark() {
    val transition = rememberInfiniteTransition(label = "eq")
    val base = listOf(7f, 14f, 5f, 11f)
    Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(2.dp), modifier = Modifier.height(16.dp)) {
        base.forEachIndexed { i, h ->
            val anim by transition.animateFloat(
                initialValue = h, targetValue = base[(i + 1) % base.size],
                animationSpec = infiniteRepeatable(tween(500), RepeatMode.Reverse), label = "bar$i",
            )
            Box(Modifier.width(3.dp).height(anim.dp).clip(RoundedCornerShape(1.dp)).background(DeezerColors.Pink))
        }
    }
}
```

## 4. Navigation

Deezer has a 4-tab bottom strip with a purple active tint — no Material pill indicator. Model the Now Playing top bar as a custom row (collapse chevron, two-line context, overflow).

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun DeezerBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = DeezerColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"    to Icons.Filled.Home,
            "Search"  to Icons.Filled.Search,
            "Music"   to Icons.Filled.LibraryMusic,
            "Profile" to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = DeezerText.TabLabel) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = DeezerColors.Purple,    // Deezer purple, not the gradient
                    selectedTextColor = DeezerColors.Purple,
                    unselectedIconColor = DeezerColors.TextTertiary,
                    unselectedTextColor = DeezerColors.TextTertiary,
                    indicatorColor = Color.Transparent,         // no Material pill — Deezer has none
                ),
            )
        }
    }
}

@Composable
fun NowPlayingTopBar(onCollapse: () -> Unit, onMore: () -> Unit) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 24.dp, vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(Icons.Filled.KeyboardArrowDown, "Collapse", tint = DeezerColors.TextPrimary,
            modifier = Modifier.size(22.dp).clickable(onClick = onCollapse))
        Spacer(Modifier.weight(1f))
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Text("FLOW · YOUR MIX", style = DeezerText.Overline, color = DeezerColors.TextSecondary)
            Text("Made for you", style = DeezerText.Body.copy(fontSize = 13.sp, fontWeight = FontWeight.Bold), color = DeezerColors.TextPrimary)
        }
        Spacer(Modifier.weight(1f))
        Icon(Icons.Filled.MoreHoriz, "More", tint = DeezerColors.TextPrimary,
            modifier = Modifier.size(22.dp).clickable(onClick = onMore))
    }
}
```

## 5. Motion

Deezer motion is tight and purposeful — colored glows signal "alive," and the equalizer freezes the moment playback stops.

| Moment | Compose recipe |
|--------|----------------|
| Play/pause | `animateFloatAsState` scale 1 → 0.94 `tween(120)` + `HapticFeedbackType.LongPress`; ▶/❚❚ icon swap |
| Player expand/collapse | bottom-sheet / shared-element `spring(dampingRatio = 0.82f, stiffness = 380f)` (~320ms) |
| Living gradient drift | `rememberInfiniteTransition` shifting brush offsets over `tween(12000, easing = LinearEasing)` — only while playing |
| Equalizer bars | `infiniteRepeatable(tween(500), RepeatMode.Reverse)` while playing; cancel/freeze on pause |
| Song-row activation | `animateColorAsState` title `#FFFFFF` → `#FF0092` `tween(180)`; equalizer fades in |
| Tab switch | instant content swap; active icon tints `Purple` over `tween(120)` |
| Skip / next | artwork `AnimatedContent` slide horizontally `tween(220)` + metadata crossfade |
| Scrubber drag | `detectHorizontalDragGestures` 1:1; knob 13 → 17.dp on drag start |

```kotlin
// The canonical Deezer motion: equalizer that MUST stop on pause
val eq = rememberInfiniteTransition(label = "eq")
val barHeight by eq.animateFloat(
    initialValue = 5f, targetValue = 14f,
    animationSpec = infiniteRepeatable(tween(500), RepeatMode.Reverse), label = "bar",
)
// When isPlaying == false: do NOT compose the infinite transition — render a static bar set.
```

Haptics: use `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on play/pause, favorite toggle, and scrub release. Skip is silent; only show a snackbar on errors (failed download/offline).

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The equalizer and Flow artwork are drawn primitives, not icons.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Collapse player | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Overflow menu | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Previous | `backward.fill` | `Icons.Filled.SkipPrevious` |
| Next | `forward.fill` | `Icons.Filled.SkipNext` |
| Shuffle | `shuffle` | `Icons.Filled.Shuffle` |
| Repeat | `repeat` | `Icons.Filled.Repeat` |
| Favorite (filled) | `heart.fill` | `Icons.Filled.Favorite` |
| FLOW badge | `bolt.fill` | `Icons.Filled.Bolt` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Music (tab) | `music.note.list` | `Icons.Filled.LibraryMusic` |
| Profile (tab) | `person.fill` | `Icons.Filled.Person` |
| Voice search | `mic.fill` | `Icons.Filled.Mic` |
| Download | `arrow.down.circle` | `Icons.Filled.DownloadForOffline` |
| Add | `plus` | `Icons.Filled.Add` |
| Queue | `list.bullet` | `Icons.Filled.QueueMusic` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; gradients + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the dark canvas wants light-content system bars always (Deezer is dark-native). The Now Playing top bar respects the camera cutout; the mini player sits above the `NavigationBar` with the gradient progress line at its top edge.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen/now-playing titles, section headers, body, row text. Pin layout-sensitive text (tab labels, scrubber time, overline, FLOW badge, chip text) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Font choice**: bundle **Deezer Sans** if licensed; otherwise ship **Inter** (SIL OFL) — never leave brand titles to the system default.
- **TalkBack**: label the play button "Play"/"Pause"; expose the scrubber via `Modifier.semantics { progressBarRangeInfo = ProgressBarRangeInfo(progress, 0f..1f) }` with a seek `customAction`; announce the FLOW artwork as "Flow, your personalized mix". The equalizer is decorative — `Modifier.clearAndSetSemantics {}`; convey now-playing via `stateDescription = "Now playing"` on the active row, not pink color alone.
- **Touch targets**: Material guidance is 48.dp. The 68.dp play button is fine; give the 22.dp tab icons and 18.dp row overflow a 48.dp hit area; the scrubber gets a 44.dp vertical hit slop; rows are 64.dp (full-row tappable).
- **Contrast**: `#FFFFFF` on `#0F0D13` and white on the gradient pass WCAG AA; `#A29CB0` on `#0F0D13` passes AA for ≥14sp. Validate the pink now-playing title against `#0F0D13` (passes AA at 15sp bold).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the living-gradient drift, the equalizer loop (render static bars), and the skip slide; keep the play/pause feedback (state-critical).
- **Dark mode**: pin the `Dark*` palette — `#0F0D13`, NOT true black. Do **not** enable Material You `dynamicColorScheme()` — Deezer's scarce purple→pink gradient identity must hold regardless of wallpaper (there is no brand accent to harmonize with one). The equalizer must visually freeze on pause — never animate while playback is stopped.
