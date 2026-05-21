# SoundCloud (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports SoundCloud's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the signature waveform scrubber, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (SoundCloud's light canvas, single-orange accent, the commentable waveform) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Canvas`-drawn waveform instead of a SwiftUI shape, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/SoundCloudColors.kt
import androidx.compose.ui.graphics.Color

object SoundCloudColors {
    // Light
    val Canvas  = Color(0xFFFFFFFF)
    val Surface = Color(0xFFF2F2F2)
    val Divider = Color(0xFFE5E5E5)

    // Dark
    val CanvasDark  = Color(0xFF1A1A1A)
    val SurfaceDark = Color(0xFF262626)
    val DividerDark = Color(0xFF333333)

    // Text
    val TextPrimary     = Color(0xFF333333)
    val TextPrimaryDark = Color(0xFFFFFFFF)
    val TextSecondary   = Color(0xFF999999)
    val TextTertiary    = Color(0xFFBFBFBF)

    // Brand
    val Orange        = Color(0xFFFF5500)
    val OrangeLight   = Color(0xFFFF7700)
    val OrangePressed = Color(0xFFE64A00)
    val ErrorRed      = Color(0xFFE5484D)
}
```

SoundCloud ships both light and dark. Provide both Material 3 schemes; the orange is constant across them.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val SCLight = lightColorScheme(
    primary        = SoundCloudColors.Orange,
    onPrimary      = Color.White,
    background      = SoundCloudColors.Canvas,
    onBackground    = SoundCloudColors.TextPrimary,
    surface         = SoundCloudColors.Surface,
    onSurface       = SoundCloudColors.TextPrimary,
    surfaceVariant  = SoundCloudColors.Surface,
    outline         = SoundCloudColors.Divider,
    error           = SoundCloudColors.ErrorRed,
)

private val SCDark = darkColorScheme(
    primary        = SoundCloudColors.Orange,
    onPrimary      = Color.White,
    background      = SoundCloudColors.CanvasDark,
    onBackground    = SoundCloudColors.TextPrimaryDark,
    surface         = SoundCloudColors.SurfaceDark,
    onSurface       = SoundCloudColors.TextPrimaryDark,
    surfaceVariant  = SoundCloudColors.SurfaceDark,
    outline         = SoundCloudColors.DividerDark,
    error           = SoundCloudColors.ErrorRed,
)

@Composable
fun SoundCloudTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) SCDark else SCLight,
    typography  = SoundCloudTypography,
    content     = content,
)
```

## 2. Typography

Interstate is licensed. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the bundled Inter, then the system font (Roboto).

```kotlin
// ui/theme/SoundCloudType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Interstate = FontFamily(
    Font(R.font.interstate_regular, FontWeight.Normal), // 400
    Font(R.font.interstate_medium,  FontWeight.Medium), // 500
    Font(R.font.interstate_bold,    FontWeight.Bold),   // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object SoundCloudText {
    val TitleLarge  = TextStyle(Interstate, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.3).sp)
    val NowPlaying  = TextStyle(Interstate, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Section     = TextStyle(Interstate, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val ProfileName = TextStyle(Interstate, fontWeight = FontWeight.Bold,   fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.1).sp)
    val TrackTitle  = TextStyle(Interstate, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 21.sp)
    val CardTitle   = TextStyle(Interstate, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 20.sp)
    val Subtitle    = TextStyle(Interstate, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 18.sp)
    val Body        = TextStyle(Interstate, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 22.sp)
    val Comment     = TextStyle(Interstate, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(Interstate, fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 16.sp)
    val LabelUpper  = TextStyle(Interstate, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Button      = TextStyle(Interstate, fontWeight = FontWeight.Bold,   fontSize = 15.sp, lineHeight = 18.sp, letterSpacing = 0.2.sp)
    val Tab         = TextStyle(Interstate, fontWeight = FontWeight.Medium, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val Timestamp   = TextStyle(Interstate, fontWeight = FontWeight.Medium, fontSize = 11.sp, lineHeight = 13.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val SoundCloudTypography = Typography(
    headlineLarge = SoundCloudText.TitleLarge,
    headlineSmall = SoundCloudText.Section,
    titleMedium   = SoundCloudText.TrackTitle,
    bodyMedium    = SoundCloudText.Body,
    labelSmall    = SoundCloudText.Tab,
)
```

## 3. Signature Components

### Commentable Waveform Scrubber

The iOS waveform is a custom-drawn amplitude graph. On Android, draw it on a Compose `Canvas`, detect drag for seek, and pin comment avatars along the baseline.

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.gestures.detectDragGestures
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.foundation.layout.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.drawscope.DrawScope
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp
import kotlin.math.roundToInt

data class WaveComment(val id: String, val position: Float, val avatarUrl: String, val text: String)

@Composable
fun WaveformScrubber(
    samples: List<Float>,           // 0..1 normalized amplitudes
    progress: Float,                // 0..1 playback position
    comments: List<WaveComment> = emptyList(),
    height: Dp = 64.dp,
    onSeek: (Float) -> Unit = {},
    modifier: Modifier = Modifier,
) {
    val haptics = LocalHapticFeedback.current
    var width by remember { mutableStateOf(1f) }

    Box(modifier.fillMaxWidth().height(height + 16.dp)) {
        Canvas(
            Modifier
                .fillMaxWidth()
                .height(height)
                .pointerInput(Unit) {
                    detectTapGestures { onSeek((it.x / size.width).coerceIn(0f, 1f)) }
                }
                .pointerInput(Unit) {
                    detectDragGestures { change, _ ->
                        val p = (change.position.x / size.width).coerceIn(0f, 1f)
                        onSeek(p)
                        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                    }
                },
        ) {
            width = size.width
            val barW = 1.5.dp.toPx()
            val gap = 1.dp.toPx()
            val count = (size.width / (barW + gap)).toInt().coerceAtLeast(1)
            for (i in 0 until count) {
                val amp = if (samples.isEmpty()) 0.3f else samples[(i * samples.size / count)]
                val played = i.toFloat() / count <= progress
                val h = (amp * (size.height - 8.dp.toPx())).coerceAtLeast(3.dp.toPx())
                drawBar(
                    x = i * (barW + gap),
                    height = h,
                    width = barW,
                    canvasHeight = size.height,
                    color = if (played) SoundCloudColors.Orange
                            else SoundCloudColors.OrangeLight.copy(alpha = 0.45f),
                )
            }
            // playhead
            val px = progress * size.width
            drawLine(
                color = SoundCloudColors.TextPrimary,
                start = Offset(px, 0f),
                end = Offset(px, size.height),
                strokeWidth = 1.dp.toPx(),
            )
        }

        // inline comment avatars on the baseline
        comments.forEach { c ->
            AsyncImage(
                model = c.avatarUrl,
                contentDescription = "Comment by ${c.id}",
                modifier = Modifier
                    .size(20.dp)
                    .offset { IntOffset((c.position * width - 10.dp.toPx()).roundToInt(), (height - 4.dp).roundToPx()) }
                    .clip(CircleShape)
                    .border(1.5.dp, SoundCloudColors.Canvas, CircleShape),
            )
        }
    }
}

private fun DrawScope.drawBar(x: Float, height: Float, width: Float, canvasHeight: Float, color: Color) {
    drawRoundRect(
        color = color,
        topLeft = Offset(x, (canvasHeight - height) / 2f),
        size = Size(width, height),
        cornerRadius = androidx.compose.ui.geometry.CornerRadius(1f, 1f),
    )
}
```

### Primary Play FAB

```kotlin
@Composable
fun SCPlayButton(
    isPlaying: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    size: Dp = 64.dp,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.93f else 1f, spring(dampingRatio = 0.7f), label = "fab")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier
            .size(size)
            .scale(scale)
            .shadow(20.dp, CircleShape, spotColor = SoundCloudColors.Orange.copy(alpha = 0.32f))
            .clip(CircleShape)
            .background(if (pressed) SoundCloudColors.OrangePressed else SoundCloudColors.Orange)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            if (isPlaying) Icons.Filled.Pause else Icons.Filled.PlayArrow,
            contentDescription = if (isPlaying) "Pause" else "Play",
            tint = Color.White,
            modifier = Modifier.size(size * 0.4f),
        )
    }
}
```

### Primary / Outline Pill (Follow / Following)

```kotlin
enum class PillStyle { Filled, Outline }

@Composable
fun SCPill(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    style: PillStyle = PillStyle.Filled,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "pill")
    val filled = style == PillStyle.Filled
    val shape = RoundedCornerShape(4.dp) // SoundCloud's slight rounding, not a full pill

    Box(
        modifier
            .scale(scale)
            .clip(shape)
            .then(
                if (filled) Modifier.background(if (pressed) SoundCloudColors.OrangePressed else SoundCloudColors.Orange)
                else Modifier.border(1.dp, if (pressed) SoundCloudColors.Orange else SoundCloudColors.TextSecondary, shape)
            )
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = if (filled) 24.dp else 20.dp, vertical = 9.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(
            text,
            style = if (filled) SoundCloudText.Button else SoundCloudText.Subtitle,
            color = if (filled) Color.White else MaterialTheme.colorScheme.onBackground,
        )
    }
}
```

### Track Row (with mini-waveform)

```kotlin
@Composable
fun TrackRow(
    title: String,
    uploader: String,
    artworkUrl: String,
    samples: List<Float>,
    progress: Float,
    isPlaying: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()

    Row(
        modifier
            .fillMaxWidth()
            .height(72.dp)
            .background(if (pressed) MaterialTheme.colorScheme.surface else Color.Transparent)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        AsyncImage(
            model = artworkUrl,
            contentDescription = null,
            modifier = Modifier.size(56.dp).clip(RoundedCornerShape(4.dp)),
            contentScale = ContentScale.Crop,
        )
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(uploader, style = SoundCloudText.Subtitle, color = SoundCloudColors.TextSecondary, maxLines = 1, overflow = TextOverflow.Ellipsis)
            Text(
                title,
                style = SoundCloudText.TrackTitle,
                color = if (isPlaying) SoundCloudColors.Orange else MaterialTheme.colorScheme.onBackground,
                maxLines = 1, overflow = TextOverflow.Ellipsis,
            )
        }
        Box(Modifier.width(110.dp).height(28.dp)) {
            WaveformScrubber(samples = samples, progress = progress, height = 28.dp)
        }
        Icon(Icons.Filled.MoreHoriz, "More", tint = SoundCloudColors.TextSecondary, modifier = Modifier.size(20.dp))
    }
}
```

## 4. Inline Comment Reveal

Drive reveal from the player position so a comment surfaces exactly as the orange sweep reaches its avatar:

```kotlin
@Composable
fun rememberRevealedComment(progress: Float, comments: List<WaveComment>): WaveComment? {
    var revealed by remember { mutableStateOf<WaveComment?>(null) }
    LaunchedEffect(progress) {
        comments.firstOrNull { kotlin.math.abs(it.position - progress) < 0.004f }?.let {
            revealed = it
            kotlinx.coroutines.delay(3000)
            revealed = null
        }
    }
    return revealed
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. SoundCloud's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 94%-opaque canvas surface. **Active tint is SoundCloud Orange.**

```kotlin
@Composable
fun SCBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = MaterialTheme.colorScheme.background.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"    to Icons.Filled.Home,
            "Search"  to Icons.Filled.Search,
            "Library" to Icons.Filled.LibraryMusic,
            "Upload"  to Icons.Filled.AddCircle,
            "You"     to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(if (label == "Upload") 28.dp else 24.dp)) },
                label = { Text(label, style = SoundCloudText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = SoundCloudColors.Orange,
                    selectedTextColor   = SoundCloudColors.Orange,
                    unselectedIconColor = SoundCloudColors.TextSecondary,
                    unselectedTextColor = SoundCloudColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // SoundCloud has no Material pill
                ),
            )
        }
    }
}
```

The persistent **Now Playing mini-bar** sits directly above this bar: a 52.dp `Surface` row with a 2.dp `SoundCloudColors.Orange` line pinned to its top edge as the always-visible progress cue — render it in the `Scaffold` `bottomBar` slot stacked above `SCBottomBar`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Waveform progress sweep | Advance `progress` linearly from the player clock (`position / duration`); recompose the `Canvas` — no easing |
| Play/pause tap | `animateFloatAsState` 1 → 0.93 with `spring(dampingRatio = 0.7f)`, `HapticFeedbackType.LongPress` |
| Inline comment reveal | `AnimatedVisibility` + `slideInVertically` + `fadeIn` over 200ms when the playhead passes the avatar |
| Like tap | `Animatable` keyframes 1 → 1.2 → 1 over 280ms; `HapticFeedbackType.LongPress` |
| Scrub | `HapticFeedbackType.TextHandleMove` on each drag delta crossing ~5% |
| Mini-bar → full player | `SharedTransitionLayout` + `Modifier.sharedElement()` on the artwork (Compose 1.7+); fallback `AnimatedVisibility` + `slideInVertically` |

```kotlin
// Like bounce
@Composable
fun LikeButton(liked: Boolean, onToggle: () -> Unit) {
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()
    Icon(
        if (liked) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
        contentDescription = "Like",
        tint = if (liked) SoundCloudColors.Orange else SoundCloudColors.TextSecondary,
        modifier = Modifier
            .size(20.dp)
            .scale(scale.value)
            .clickable {
                scope.launch {
                    scale.animateTo(1.2f, tween(140)); scale.animateTo(1f, tween(140))
                }
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onToggle()
            },
    )
}
```

Haptics: prefer `LocalHapticFeedback`. For scrub ticks, `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` (API 30+) approximates iOS's selection feedback.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export SoundCloud's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Next / Previous | `forward.end.fill` / `backward.end.fill` | `Icons.Filled.SkipNext` / `SkipPrevious` |
| Shuffle | `shuffle` | `Icons.Filled.Shuffle` |
| Repeat | `repeat` | `Icons.Filled.Repeat` |
| Like | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Repost | `arrow.2.squarepath` | `Icons.Filled.Repeat` |
| Comment | `bubble.right` | `Icons.Outlined.ChatBubbleOutline` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Library (tab) | `play.square.stack` | `Icons.Filled.LibraryMusic` |
| Upload (tab) | `arrow.up.circle.fill` | `Icons.Filled.AddCircle` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; the `Canvas` waveform + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` or `Scaffold` insets so the mini-bar clears gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on titles, uploader names, body, comments. Pin layout-sensitive text (waveform timestamps, tab labels) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: give the waveform `Modifier.semantics { progressBarRangeInfo = ProgressBarRangeInfo(progress, 0f..1f) }` and a custom seek action; announce inline comments via `LiveRegionMode.Polite`.
- **Touch targets**: Material guidance is 48.dp minimum. The 64.dp FAB clears it; ensure the 20.dp action icons get `Modifier.size(48.dp)` hit area via padding.
- **Contrast**: `#999999` on `#FFFFFF` passes WCAG AA at 14sp+; validate the 11sp timestamps and darken toward `#808080` if targeting strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — SoundCloud's brand requires the fixed orange and the `#FFFFFF`/`#1A1A1A` canvases regardless of wallpaper.
