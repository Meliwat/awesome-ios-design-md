# Snapchat (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Snapchat's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the camera-first black canvas, the aggressive Snap Yellow, the 82dp concentric capture button, the color-coded snap-type inbox, bitmoji-as-avatar) while making everything idiomatic Android — a `HorizontalPager` for the 5-screen swipe nav instead of a paged `TabView`, `pointerInput` for the tap/long-press/double-tap capture gesture, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for bitmoji avatars and story-preview imagery. (No `androidx.palette` — Snapchat does no color extraction; the camera feed is the content surface.)

## 1. Color Tokens

```kotlin
// ui/theme/SnapColors.kt
import androidx.compose.ui.graphics.Color

object SnapColors {
    // Canvas & Surfaces — camera-default dark
    val Canvas    = Color(0xFF000000)
    val Surface1  = Color(0xFF1A1A1A) // received chat bubble, discover cards, sheets
    val Surface2  = Color(0xFF2C2C2C) // sent chat bubble, selected rows
    val Divider   = Color(0xFF333333)

    // Canvas (Light — limited: settings, memories)
    val LightCanvas   = Color(0xFFFFFFFF)
    val LightSurface1 = Color(0xFFF2F2F2)

    // Text
    val TextPrimary       = Color(0xFFFFFFFF)
    val TextPrimaryLight  = Color(0xFF000000)
    val TextSecondary     = Color(0xFF8A8A8F)
    val TextTertiary      = Color(0xFF555555)

    // Brand — the aggressive single accent
    val Yellow        = Color(0xFFFFFC00)
    val YellowPressed = Color(0xFFE6E300)

    // Snap type colors (color-coded inbox)
    val PhotoRed    = Color(0xFFFF2E3D)
    val VideoPurple = Color(0xFF9B51FF)
    val ChatBlue    = Color(0xFF4DA7FF)
    val AudioGreen  = Color(0xFF4CD964)

    // Semantic
    val ErrorRed     = Color(0xFFFF3B30)
    val SuccessGreen = Color(0xFF00D873)
    val LiveRed      = Color(0xFFFF2E3D)

    // Snap Map heat
    val MapWarm = Color(0xFFFF9A1F)
}
```

Snapchat's gravity is **dark/camera-first** — pure black, never a warm dark. Wire a Material 3 `darkColorScheme`; do not provide a light scheme as default (light appears only inside settings/memories). `primary` is Snap Yellow; `onPrimary` is black (yellow CTAs carry black text/icons). The snap-type colors are explicit tokens, never theme roles.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val SnapScheme = darkColorScheme(
    primary        = SnapColors.Yellow,
    onPrimary      = SnapColors.Canvas,      // intentional: black on yellow
    background     = SnapColors.Canvas,
    onBackground   = SnapColors.TextPrimary,
    surface        = SnapColors.Surface1,
    onSurface      = SnapColors.TextPrimary,
    surfaceVariant = SnapColors.Surface2,
    outline        = SnapColors.Divider,
    error          = SnapColors.ErrorRed,
)

@Composable
fun SnapTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = SnapScheme, typography = SnapTypography, content = content)
```

## 2. Typography

Avenir Next is the iOS system face; on Android it is not bundled. Drop the licensed Avenir Next TTFs in `res/font/` and build a `FontFamily`. Fall back to the system font (Roboto) — a warm humanist geometric sans is the closest free substitute. Snapchat uses **Medium (500) and Bold (700) only**, with Heavy (900) on splash/streak hero — never regular/light/thin.

```kotlin
// ui/theme/SnapType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val AvenirNext = FontFamily(
    Font(R.font.avenir_next_medium, FontWeight.Medium), // 500 — chat, captions
    Font(R.font.avenir_next_bold,   FontWeight.Bold),   // 700 — names, headings, buttons
    Font(R.font.avenir_next_heavy,  FontWeight.Black),  // 900 — splash / streak hero only
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object SnapText {
    val SplashTitle    = TextStyle(AvenirNext, fontWeight = FontWeight.Black,  fontSize = 48.sp, lineHeight = 48.sp, letterSpacing = (-0.5).sp)
    val ScreenTitle    = TextStyle(AvenirNext, fontWeight = FontWeight.Bold,   fontSize = 24.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val SectionHeader  = TextStyle(AvenirNext, fontWeight = FontWeight.Bold,   fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.1).sp)
    val ChatRowName    = TextStyle(AvenirNext, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 20.sp)
    val ChatMessage    = TextStyle(AvenirNext, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 21.sp)
    val ChatStatus     = TextStyle(AvenirNext, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 16.sp)
    val StoryName      = TextStyle(AvenirNext, fontWeight = FontWeight.Bold,   fontSize = 14.sp, lineHeight = 17.sp)
    val Timestamp      = TextStyle(AvenirNext, fontWeight = FontWeight.Medium, fontSize = 12.sp, lineHeight = 14.sp)
    val StreakCount    = TextStyle(AvenirNext, fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 18.sp)
    val HudLabel       = TextStyle(AvenirNext, fontWeight = FontWeight.Bold,   fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
    val ButtonPrimary  = TextStyle(AvenirNext, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val LensLabel      = TextStyle(AvenirNext, fontWeight = FontWeight.Bold,   fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
    val BitmojiCallout = TextStyle(AvenirNext, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 18.sp)
    val SpotlightCap   = TextStyle(AvenirNext, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 18.sp)
    val DiscoverTitle  = TextStyle(AvenirNext, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
}

val SnapTypography = Typography(
    displayMedium = SnapText.SplashTitle,
    headlineSmall = SnapText.ScreenTitle,
    titleMedium   = SnapText.ChatRowName,
    bodyMedium    = SnapText.ChatMessage,
    labelMedium   = SnapText.Timestamp,
)
```

## 3. Signature Components

### Snap Capture Button (the 82dp concentric double-circle)

The most recognizable component in the app. Outer 82dp / 6dp Snap-Yellow ring + inner 64dp white circle. Tap = photo; long-press = video (inner fills yellow, outer ring becomes a clockwise progress arc up to 60s); double-tap = camera flip. One `pointerInput` block disambiguates all three.

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.spring
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp
import kotlinx.coroutines.delay

@Composable
fun SnapCaptureButton(
    isRecording: Boolean,
    recordProgress: Float, // 0f..1f over 60s
    onPhoto: () -> Unit,
    onVideoStart: () -> Unit,
    onVideoStop: () -> Unit,
    onFlip: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val haptics = LocalHapticFeedback.current
    val innerScale = remember { Animatable(1f) }

    androidx.compose.foundation.layout.Box(
        modifier = modifier
            .size(82.dp)
            .pointerInput(Unit) {
                awaitPointerEventScope {
                    while (true) {
                        val down = awaitFirstDown()
                        val downTime = System.currentTimeMillis()
                        var longFired = false
                        // long-press → video
                        val longJob = launchAndDelay(350) {
                            longFired = true
                            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // impact medium
                            onVideoStart()
                        }
                        val up = waitForUpOrCancellation()
                        longJob.cancel()
                        if (longFired) {
                            onVideoStop()
                        } else if (up != null) {
                            val now = System.currentTimeMillis()
                            if (now - lastTap < 300) {           // double-tap → flip
                                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                                onFlip()
                            } else {                              // single tap → photo
                                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // impact soft
                                onPhoto()
                            }
                            lastTap = now
                        }
                    }
                }
            },
        contentAlignment = androidx.compose.ui.Alignment.Center,
    ) {
        // Outer yellow ring + (when recording) clockwise progress arc
        Canvas(Modifier.size(82.dp)) {
            val stroke = 6.dp.toPx()
            drawCircle(SnapColors.Yellow, radius = (size.minDimension - stroke) / 2f, style = Stroke(stroke))
            if (isRecording) {
                drawArc(
                    color = SnapColors.Yellow,
                    startAngle = -90f,
                    sweepAngle = 360f * recordProgress,
                    useCenter = false,
                    topLeft = Offset(stroke / 2f, stroke / 2f),
                    size = Size(size.width - stroke, size.height - stroke),
                    style = Stroke(stroke),
                )
            }
        }
        // Inner circle — white, or yellow while recording
        androidx.compose.foundation.layout.Box(
            Modifier
                .size(64.dp)
                .scale(innerScale.value)
                .clip(CircleShape)
                .background(if (isRecording) SnapColors.Yellow else SnapColors.TextPrimary)
        )
    }

    // photo-tap squish
    LaunchedEffect(Unit) { /* drive innerScale 0.92 → 1.0 from onPhoto in real code */ }
}

// (helpers `lastTap`, `launchAndDelay` are sketches — wire to a rememberCoroutineScope in production)
```

### Chat Inbox Row (color-coded snap type)

72dp tall on black, 1dp divider. 48dp circular **bitmoji**, name, then the snap-type indicator (filled square unopened / outline opened) + status, with timestamp + 🔥 streak trailing.

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ArrowDownward
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.ui.Alignment
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

enum class SnapType { PHOTO, VIDEO, CHAT, AUDIO, NONE }
enum class SnapDirection { INCOMING, OUTGOING }

@Composable
fun SnapChatRow(
    name: String,
    bitmojiUrl: String,
    status: String,        // "Received · 2m ago"
    timestamp: String,     // "2m"
    snapType: SnapType,
    direction: SnapDirection,
    isUnread: Boolean,
    streakDays: Int?,
    modifier: Modifier = Modifier,
) {
    Column(modifier) {
        Row(
            Modifier.fillMaxWidth().height(72.dp).padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            AsyncImage(
                model = bitmojiUrl, contentDescription = null, contentScale = ContentScale.Crop,
                modifier = Modifier.size(48.dp).clip(CircleShape),
            )
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(name, style = SnapText.ChatRowName, color = SnapColors.TextPrimary)
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                    if (snapType != SnapType.NONE) {
                        SnapTypeIndicator(snapType, direction, isUnread)
                    }
                    Text(status, style = SnapText.ChatStatus, color = SnapColors.TextSecondary)
                }
            }
            Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(timestamp, style = SnapText.Timestamp, color = SnapColors.TextSecondary)
                streakDays?.let {
                    Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(2.dp)) {
                        Text("🔥")
                        Text("$it", style = SnapText.StreakCount, color = SnapColors.TextPrimary)
                    }
                }
            }
        }
        Spacer(Modifier.fillMaxWidth().height(1.dp).background(SnapColors.Divider))
    }
}

@Composable
fun SnapTypeIndicator(type: SnapType, direction: SnapDirection, isUnread: Boolean) {
    val color = when (type) {
        SnapType.PHOTO -> SnapColors.PhotoRed
        SnapType.VIDEO -> SnapColors.VideoPurple
        SnapType.CHAT  -> SnapColors.ChatBlue
        SnapType.AUDIO -> SnapColors.AudioGreen
        SnapType.NONE  -> SnapColors.TextSecondary
    }
    Box(Modifier.size(16.dp), contentAlignment = Alignment.Center) {
        Box(
            Modifier
                .size(16.dp)
                .clip(RoundedCornerShape(2.dp))
                .then(if (isUnread) Modifier.background(color) else Modifier)
                .border(1.5.dp, color, RoundedCornerShape(2.dp))
        )
        Icon(
            if (direction == SnapDirection.INCOMING) Icons.Filled.ArrowDownward else Icons.Filled.ArrowUpward,
            contentDescription = null,
            tint = if (isUnread) Color.White else color,
            modifier = Modifier.size(8.dp),
        )
    }
}

import androidx.compose.foundation.border
```

### Story Thumbnail (pulsing unread ring)

120dp × 200dp, 16dp corner. 3dp ring: yellow (unread, subtle 2s opacity pulse), gray (read), red (live). Bitmoji top-left, creator name bottom-left with a drop shadow for legibility on imagery.

```kotlin
import androidx.compose.animation.core.RepeatMode
import androidx.compose.animation.core.animateFloat
import androidx.compose.animation.core.infiniteRepeatable
import androidx.compose.animation.core.rememberInfiniteTransition
import androidx.compose.animation.core.tween
import androidx.compose.ui.graphics.graphicsLayer
import androidx.compose.ui.draw.drawBehind

enum class StoryState { UNREAD, READ, LIVE }

@Composable
fun SnapStoryThumb(
    creatorName: String,
    bitmojiUrl: String,
    previewUrl: String,
    state: StoryState,
    modifier: Modifier = Modifier,
) {
    val ringColor = when (state) {
        StoryState.UNREAD -> SnapColors.Yellow
        StoryState.READ   -> SnapColors.TextTertiary
        StoryState.LIVE   -> SnapColors.LiveRed
    }
    val pulse = rememberInfiniteTransition(label = "ring")
    val ringAlpha by pulse.animateFloat(
        1f, 0.7f,
        infiniteRepeatable(tween(2000), RepeatMode.Reverse),
        label = "ringAlpha",
    )

    Box(
        modifier
            .size(width = 120.dp, height = 200.dp)
            .clip(RoundedCornerShape(16.dp))
            .drawBehind { } // image drawn by AsyncImage below
    ) {
        AsyncImage(
            model = previewUrl, contentDescription = "$creatorName story", contentScale = ContentScale.Crop,
            modifier = Modifier.matchParentSize(),
        )
        Box(
            Modifier
                .matchParentSize()
                .border(
                    width = 3.dp,
                    color = ringColor.copy(alpha = if (state == StoryState.UNREAD) ringAlpha else 1f),
                    shape = RoundedCornerShape(16.dp),
                )
        )
        AsyncImage(
            model = bitmojiUrl, contentDescription = null, contentScale = ContentScale.Crop,
            modifier = Modifier.padding(8.dp).size(40.dp).clip(CircleShape).align(Alignment.TopStart),
        )
        Text(
            creatorName,
            style = SnapText.StoryName,
            color = Color.White,
            modifier = Modifier
                .align(Alignment.BottomStart)
                .padding(10.dp)
                .graphicsLayer { shadowElevation = 2f }, // ~rgba(0,0,0,0.4) text shadow proxy
        )
    }
}
```

### Chat Bubble (dark gray, not iMessage-colored)

Both sent and received are dark-gray variants — never blue. 20dp corner, no tail, max 75% width.

```kotlin
@Composable
fun SnapChatBubble(text: String, fromMe: Boolean, modifier: Modifier = Modifier) {
    Row(modifier.fillMaxWidth().padding(horizontal = 16.dp)) {
        if (fromMe) Spacer(Modifier.weight(1f).widthIn(min = 60.dp))
        Text(
            text,
            style = SnapText.ChatMessage,
            color = SnapColors.TextPrimary,
            modifier = Modifier
                .widthIn(max = (LocalConfiguration.current.screenWidthDp * 0.75f).dp)
                .clip(RoundedCornerShape(20.dp))
                .background(if (fromMe) SnapColors.Surface2 else SnapColors.Surface1)
                .padding(horizontal = 14.dp, vertical = 10.dp),
        )
        if (!fromMe) Spacer(Modifier.weight(1f).widthIn(min = 60.dp))
    }
}

import androidx.compose.ui.platform.LocalConfiguration
```

## 4. Snapchat-Specific Feature: the Chromeless Camera HUD

The signature: the app opens to a full-bleed live viewfinder with **no UI frame** — controls float chromelessly in the corners with a drop shadow for legibility over any scene. Above the capture button is the lens carousel (active lens scales 60dp → 72dp with a 3dp yellow ring).

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.material.icons.filled.*
import androidx.compose.ui.unit.dp

@Composable
fun SnapCameraHUD(
    cameraFeed: @Composable () -> Unit, // your CameraX PreviewView wrapped in AndroidView
) {
    var flashOn by remember { mutableStateOf(false) }
    var selectedLens by remember { mutableStateOf(2) }
    var isRecording by remember { mutableStateOf(false) }
    var progress by remember { mutableStateOf(0f) }
    val lenses = listOf("😎", "🎭", "🐶", "😂", "✨")

    Box(Modifier.fillMaxSize().background(SnapColors.Canvas)) {
        cameraFeed() // edge-to-edge, no frame

        Column(Modifier.fillMaxSize()) {
            // Top floating HUD
            Row(
                Modifier.fillMaxWidth().padding(16.dp),
                horizontalArrangement = Arrangement.SpaceBetween,
            ) {
                HudIcon(Icons.Filled.AccountCircle)
                Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                    HudIcon(
                        if (flashOn) Icons.Filled.FlashOn else Icons.Filled.FlashOff,
                        tint = if (flashOn) SnapColors.Yellow else Color.White,
                    ) { flashOn = !flashOn }
                    HudIcon(Icons.Filled.FlipCameraAndroid)
                    HudIcon(Icons.Filled.Search)
                }
            }

            Spacer(Modifier.weight(1f))

            LensCarousel(lenses, selectedLens) { selectedLens = it }
            Spacer(Modifier.height(16.dp))

            Row(
                Modifier.fillMaxWidth().padding(horizontal = 32.dp, vertical = 16.dp),
                horizontalArrangement = Arrangement.SpaceBetween,
                verticalAlignment = Alignment.CenterVertically,
            ) {
                HudIcon(Icons.Filled.PhotoLibrary, size = 32.dp)
                SnapCaptureButton(
                    isRecording = isRecording,
                    recordProgress = progress,
                    onPhoto = {},
                    onVideoStart = { isRecording = true },
                    onVideoStop = { isRecording = false },
                    onFlip = {},
                )
                HudIcon(Icons.AutoMirrored.Filled.Chat, size = 32.dp)
            }
        }
    }
}

@Composable
fun HudIcon(
    icon: androidx.compose.ui.graphics.vector.ImageVector,
    size: androidx.compose.ui.unit.Dp = 28.dp,
    tint: Color = Color.White,
    onClick: () -> Unit = {},
) {
    Box(
        Modifier.size(44.dp).clickableNoRipple(onClick),
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            icon, contentDescription = null, tint = tint,
            modifier = Modifier
                .size(size)
                .graphicsLayer { shadowElevation = 2f }, // legibility over any scene
        )
    }
}

@Composable
fun LensCarousel(lenses: List<String>, selected: Int, onSelect: (Int) -> Unit) {
    LazyRow(
        horizontalArrangement = Arrangement.spacedBy(12.dp),
        contentPadding = PaddingValues(horizontal = 16.dp),
        modifier = Modifier.fillMaxWidth(),
    ) {
        itemsIndexed(lenses) { i, lens ->
            val active = i == selected
            val s by animateFloatAsState(if (active) 72f else 60f, label = "lensSize")
            Box(
                Modifier
                    .size(s.dp)
                    .clip(CircleShape)
                    .background(SnapColors.Surface2)
                    .then(if (active) Modifier.border(3.dp, SnapColors.Yellow, CircleShape) else Modifier)
                    .clickableNoRipple { onSelect(i) },
                contentAlignment = Alignment.Center,
            ) { Text(lens, fontSize = (if (active) 32 else 28).sp) }
        }
    }
}

import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.layout.PaddingValues
import androidx.compose.foundation.lazy.itemsIndexed
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.ui.composed
fun Modifier.clickableNoRipple(onClick: () -> Unit): Modifier = composed {
    clickable(remember { MutableInteractionSource() }, indication = null, onClick = onClick)
}
```

## 5. Navigation — swipe-between-five-screens (NOT a tab bar)

Snapchat has no traditional tab bar. iOS uses a paged `TabView`; the Compose equivalent is a `HorizontalPager` with the **Camera centered (page 2)** as the default, plus a minimal indicator strip (translucent over the camera, solid black elsewhere — Android has no live blur).

```kotlin
import androidx.compose.foundation.pager.HorizontalPager
import androidx.compose.foundation.pager.rememberPagerState

@Composable
fun SnapRootSwipeNav() {
    val pager = rememberPagerState(initialPage = 2, pageCount = { 5 }) // Camera = 2, the default

    Box(Modifier.fillMaxSize()) {
        HorizontalPager(state = pager, modifier = Modifier.fillMaxSize()) { page ->
            when (page) {
                0 -> SnapMapScreen()
                1 -> ChatListScreen()
                2 -> SnapCameraHUD(cameraFeed = { /* CameraX preview */ })
                3 -> StoriesScreen()
                4 -> SpotlightScreen()
            }
        }
        SnapNavIndicator(
            selected = pager.currentPage,
            overCamera = pager.currentPage == 2,
            modifier = Modifier.align(Alignment.BottomCenter),
        )
    }
}

@Composable
fun SnapNavIndicator(selected: Int, overCamera: Boolean, modifier: Modifier = Modifier) {
    val icons = listOf(
        Icons.Filled.Map, Icons.AutoMirrored.Filled.Chat, Icons.Filled.CameraAlt,
        Icons.Filled.PlayArrow, Icons.Filled.Diamond,
    )
    Row(
        modifier
            .fillMaxWidth()
            .background(if (overCamera) Color.Black.copy(alpha = 0.5f) else SnapColors.Canvas)
            .height(56.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        icons.forEachIndexed { i, icon ->
            Icon(
                icon, contentDescription = null,
                tint = if (i == selected) Color.White else SnapColors.TextTertiary,
                modifier = Modifier.weight(1f).size(28.dp),
            )
        }
    }
}
```

`HorizontalPager` gives the 300ms spring page transition; set `pageSpacing` and rely on the default fling for the rubber-band edge feel. No labels — icons only.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Screen swipe (5-screen) | `HorizontalPager` default spring (~300ms) + fling; `HapticFeedbackType.TextHandleMove` on edge resistance |
| Capture tap (photo) | inner-circle `Animatable` 0.92 → 1.0 `spring(stiffness = 500f)` + white-flash `Box` 100ms + `HapticFeedbackType.LongPress` (soft) |
| Capture hold (video) | outer arc `drawArc` driven by a 60s `Animatable`/`tween`; `LongPress` on start, soft tick per 1s |
| Camera flip (double-tap) | shutter icon `rotate` 180° over 400ms + `HapticFeedbackType.TextHandleMove` |
| Lens swipe/select | `animateFloatAsState` 60dp → 72dp + yellow-ring `fadeIn`; AR overlay cross-fade 200ms |
| Snap send | preview `slideOutHorizontally { -it }` 300ms + confetti `Canvas`/`Animatable` + `HapticFeedbackType.Confirm` |
| Streak save | flame `Animatable` 1.0 → 1.3 → 1.0 + "+1 🔥" `offset`/`fadeOut` 600ms + success haptic |
| Story ring pulse | `rememberInfiniteTransition` opacity 1.0 ↔ 0.7 over 2s (unread only) |

```kotlin
// Streak-save bounce + floating "+1 🔥"
@Composable
fun StreakSaveBurst(trigger: Any) {
    val scale = remember { Animatable(1f) }
    val rise = remember { Animatable(0f) }
    LaunchedEffect(trigger) {
        scale.animateTo(1.3f, spring(dampingRatio = 0.5f)); scale.animateTo(1f, spring())
        rise.animateTo(1f, tween(600))
    }
    Box {
        Text("🔥", Modifier.scale(scale.value), style = SnapText.StreakCount)
        Text(
            "+1 🔥",
            Modifier
                .offset(y = (-20).dp * rise.value)
                .graphicsLayer { alpha = 1f - rise.value },
            style = SnapText.HudLabel,
            color = SnapColors.Yellow,
        )
    }
}

import androidx.compose.foundation.layout.offset
```

Haptics: prefer `LocalHapticFeedback`. Photo capture = `LongPress` (soft impact), video start = `LongPress` (medium), camera flip / screen-edge / lens = `TextHandleMove`, streak save / snap sent = `HapticFeedbackType.Confirm`. The capture haptic should also pair with a shutter sound. Honor reduce-motion: disable the story-ring pulse and skip the flip-spin while still firing haptics.

## 7. Icons

Snapchat uses custom glyphs extensively (ghost logo, capture button is hand-drawn in §3); the closest first-party set is `androidx.compose.material:material-icons-extended`. The ghost logo and bitmoji ship as assets (vector drawable / Coil-loaded). Bitmoji always replaces a generic person icon.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Camera (nav) | `camera.fill` | `Icons.Filled.CameraAlt` |
| Chat (nav) | `bubble.left.fill` | `Icons.AutoMirrored.Filled.Chat` |
| Map (nav) | `map.fill` | `Icons.Filled.Map` |
| Stories (nav) | `play.rectangle.fill` | `Icons.Filled.PlayArrow` |
| Spotlight (nav) | `diamond.fill` | `Icons.Filled.Diamond` |
| Memories (HUD) | `photo.stack.fill` | `Icons.Filled.PhotoLibrary` |
| Flash on | `bolt.fill` | `Icons.Filled.FlashOn` |
| Flash off | `bolt.slash.fill` | `Icons.Filled.FlashOff` |
| Camera flip | `arrow.triangle.2.circlepath.camera.fill` | `Icons.Filled.FlipCameraAndroid` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Send | `paperplane.fill` | `Icons.AutoMirrored.Filled.Send` |
| New chat | `square.and.pencil` | `Icons.Filled.Edit` |
| Profile | `person.crop.circle.fill` (or bitmoji) | `Icons.Filled.AccountCircle` (or bitmoji) |
| Snap incoming | `arrow.down` in square | `Icons.Filled.ArrowDownward` (in 16dp square) |
| Snap outgoing | `arrow.up` in square | `Icons.Filled.ArrowUpward` (in 16dp square) |
| Microphone (audio) | `mic.fill` | `Icons.Filled.Mic` |
| Sticker | `face.smiling` | `Icons.Filled.AddReaction` |
| Streak / BFF / birthday | 🔥 / 💛 / 🎂 | emoji glyphs (not icons) |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (CameraX + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. The capture button and progress arc are hand-drawn `Canvas` — no extra dependency.
- **Edge-to-edge**: call `enableEdgeToEdge()`. The camera is **edge-to-edge by design** (`Modifier.fillMaxSize()` + `ignoresSafeArea` equivalent — no insets on the viewfinder). Apply insets only to the floating HUD icons (clear the status bar / Dynamic-Island-equivalent cutout) and the bottom nav strip (clear gesture nav). Capture button sits ~20.dp above the nav strip.
- **Font scaling**: `sp` honors user scale on chat messages, row names, bios, settings. **Pin camera HUD labels, streak counts, lens labels, timestamps, and story-thumbnail names to fixed sizes** (layout-sensitive) via a fixed-`fontScale` `LocalDensity`.
- **TalkBack**: every floating HUD icon needs an explicit `contentDescription` ("Flash, off", "Camera, switch to front"). The capture button announces "Snap camera, double tap to capture photo, press and hold to record video". The swipe nav is disorienting for screen readers — provide a rotor/`semantics` "Go to screen" action listing Map / Chat / Camera / Stories / Spotlight as discrete destinations. Bitmoji images are decorative: `Modifier.semantics { hideFromAccessibility() }` (or `contentDescription = null`) and rely on the adjacent name.
- **Reduce motion**: respect the accessibility setting — disable the story-ring pulse, skip the camera-flip spin and the streak burst, but still fire haptics. **Reduce transparency**: the translucent nav indicator falls back to solid `#000000`.
- **Alternatives for inaccessible gestures**: long-press-for-video is not screen-reader-operable — provide an explicit "Record video" mode toggle. Flash/Live states must always carry a text label, never color alone.
- **Contrast**: white on `#000000` is maximum; `#8A8A8F` secondary on black passes AA at 13sp+. The chromeless HUD relies on the drop shadow over arbitrary camera scenes — offer an accessibility "opaque chrome background" toggle that puts a semi-transparent pill behind each icon.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Snap Yellow is an intentionally aggressive fixed brand signal flare, and the snap-type colors (red/purple/blue/green) are a semantic at-a-glance language. Both must be identical regardless of wallpaper.
