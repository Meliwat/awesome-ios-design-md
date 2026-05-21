# Shazam (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Shazam's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the radial gradient, the signature pulsing button + concentric rings, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Shazam's radial blue gradient, the single dominant button, concentric pulse rings, glass surfaces, no tab bar) while making everything idiomatic Android — `Brush.radialGradient` instead of SwiftUI's `RadialGradient`, a `ModalBottomSheet` instead of a UIKit sheet, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/ShazamColors.kt
import androidx.compose.ui.graphics.Color

object ShazamColors {
    // Gradient stops
    val Core        = Color(0xFF0050FF)
    val Blue        = Color(0xFF0088FF)
    val Space       = Color(0xFF08090E)
    val BluePressed = Color(0xFF006FE0)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFB8C4FF)               // periwinkle, tuned to the gradient
    val TextTertiary  = Color(0xFFB8C4FF).copy(alpha = 0.55f)

    // Glass
    val Glass       = Color.White.copy(alpha = 0.08f)
    val GlassStrong = Color.White.copy(alpha = 0.14f)
    val Divider     = Color.White.copy(alpha = 0.12f)

    // Semantic
    val AppleMusicPink = Color(0xFFFA243C)
    val ErrorRed       = Color(0xFFFF453A)
}
```

Wire it into a Material 3 `darkColorScheme`. Shazam's hero is dark by design; do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val ShazamScheme = darkColorScheme(
    primary        = ShazamColors.Blue,
    onPrimary      = Color.White,
    background      = ShazamColors.Space,
    onBackground    = ShazamColors.TextPrimary,
    surface         = ShazamColors.Space,
    onSurface       = ShazamColors.TextPrimary,
    surfaceVariant  = ShazamColors.Glass,
    outline         = ShazamColors.Divider,
    error           = ShazamColors.ErrorRed,
)

@Composable
fun ShazamTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = ShazamScheme, typography = ShazamTypography, content = content)
```

The signature hero gradient:

```kotlin
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush

@Composable
fun heroBrush(size: androidx.compose.ui.geometry.Size) = Brush.radialGradient(
    colorStops = arrayOf(
        0.0f  to ShazamColors.Blue,
        0.30f to ShazamColors.Core,
        1.0f  to ShazamColors.Space,
    ),
    center = Offset(size.width * 0.5f, size.height * 0.42f),
    radius = size.maxDimension * 0.75f,
)
```

## 2. Typography

Montserrat is on Google Fonts. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto).

```kotlin
// ui/theme/ShazamType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Montserrat = FontFamily(
    Font(R.font.montserrat_medium, FontWeight.Medium), // 500
    Font(R.font.montserrat_bold,   FontWeight.Bold),   // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object ShazamText {
    val ResultLarge = TextStyle(Montserrat, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.3).sp)
    val Result      = TextStyle(Montserrat, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Section     = TextStyle(Montserrat, fontWeight = FontWeight.Bold,   fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Prompt      = TextStyle(Montserrat, fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 22.sp, letterSpacing = 0.2.sp)
    val CardTitle   = TextStyle(Montserrat, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 21.sp)
    val Subtitle    = TextStyle(Montserrat, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 18.sp)
    val Body        = TextStyle(Montserrat, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 22.sp)
    val Meta        = TextStyle(Montserrat, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 17.sp)
    val LabelUpper  = TextStyle(Montserrat, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 1.0.sp)
    val Button      = TextStyle(Montserrat, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = 0.3.sp)
    val ButtonSec   = TextStyle(Montserrat, fontWeight = FontWeight.Bold,   fontSize = 14.sp, lineHeight = 18.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val ShazamTypography = Typography(
    headlineLarge = ShazamText.ResultLarge,
    headlineSmall = ShazamText.Section,
    titleMedium   = ShazamText.CardTitle,
    bodyMedium    = ShazamText.Body,
    labelSmall    = ShazamText.LabelUpper,
)
```

## 3. Signature Components

### The Pulsing Shazam Button + Concentric Rings

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun ShazamButton(
    isListening: Boolean,
    onTap: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val size = 132.dp
    val haptics = LocalHapticFeedback.current
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()

    // Idle breathing
    val breatheT = rememberInfiniteTransition(label = "breathe")
    val breathe by breatheT.animateFloat(
        1f, 1.04f, infiniteRepeatable(tween(2400, easing = EaseInOut), RepeatMode.Reverse), label = "b",
    )
    val pressScale by animateFloatAsState(if (pressed) 0.94f else 1f, spring(dampingRatio = 0.8f), label = "press")
    val glowAlpha by animateFloatAsState(if (isListening) 0.5f else 0f, tween(500), label = "glow")

    Box(modifier.size(size * 2.6f), contentAlignment = Alignment.Center) {
        // Concentric emitting rings
        if (isListening) {
            repeat(3) { i -> PulseRing(delayMs = i * 600, base = size) }
        }

        // Listening glow
        Box(
            Modifier
                .size(size * 1.4f)
                .clip(CircleShape)
                .background(ShazamColors.Blue.copy(alpha = glowAlpha)),
        )

        // The button
        Box(
            Modifier
                .size(size)
                .scale(pressScale * if (isListening) 1f else breathe)
                .clip(CircleShape)
                .background(
                    Brush.radialGradient(
                        listOf(Color.White, Color(0xFFE8F0FF), Color(0xFFCFE0FF)),
                        center = Offset(size.value * 0.38f, size.value * 0.32f),
                        radius = size.value * 0.9f,
                    )
                )
                .border(1.dp, Color.White.copy(alpha = 0.4f), CircleShape)
                .clickable(interaction, indication = null) {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS heavy impact
                    onTap()
                },
            contentAlignment = Alignment.Center,
        ) {
            // Stylized Shazam mark — replace with the licensed vector asset in production
            Canvas(Modifier.size(size * 0.4f)) {
                val w = this.size.width
                val path = androidx.compose.ui.graphics.Path().apply {
                    moveTo(w * 0.62f, w * 0.18f)
                    cubicTo(w * 0.40f, w * 0.18f, w * 0.30f, w * 0.30f, w * 0.30f, w * 0.46f)
                    cubicTo(w * 0.30f, w * 0.62f, w * 0.70f, w * 0.50f, w * 0.70f, w * 0.62f)
                    cubicTo(w * 0.70f, w * 0.78f, w * 0.58f, w * 0.86f, w * 0.38f, w * 0.86f)
                }
                drawPath(path, ShazamColors.Blue, style = Stroke(width = w * 0.13f, cap = androidx.compose.ui.graphics.StrokeCap.Round))
            }
        }
    }
}

@Composable
private fun PulseRing(delayMs: Int, base: androidx.compose.ui.unit.Dp) {
    val t = rememberInfiniteTransition(label = "ring")
    val p by t.animateFloat(
        0f, 1f,
        infiniteRepeatable(tween(1800, delayMillis = delayMs, easing = EaseOut), RepeatMode.Restart),
        label = "p",
    )
    Box(
        Modifier
            .size(base)
            .scale(1f + p * 1.6f)
            .border((2).dp, Color.White.copy(alpha = 0.22f * (1f - p)), CircleShape),
    )
}
```

### Hero Screen (no bottom bar)

```kotlin
@Composable
fun ShazamHome() {
    var listening by remember { mutableStateOf(false) }
    BoxWithConstraints(Modifier.fillMaxSize()) {
        val brush = Brush.radialGradient(
            colorStops = arrayOf(0f to ShazamColors.Blue, 0.30f to ShazamColors.Core, 1f to ShazamColors.Space),
            center = Offset(constraints.maxWidth * 0.5f, constraints.maxHeight * 0.42f),
            radius = maxOf(constraints.maxWidth, constraints.maxHeight) * 0.75f,
        )
        Box(Modifier.fillMaxSize().background(brush)) {
            Row(
                Modifier.fillMaxWidth().statusBarsPadding().padding(horizontal = 20.dp, vertical = 8.dp),
                horizontalArrangement = Arrangement.SpaceBetween,
            ) {
                Icon(Icons.Outlined.AccountCircle, "Profile", tint = Color.White, modifier = Modifier.size(26.dp))
                Icon(Icons.Filled.QueueMusic, "History", tint = Color.White, modifier = Modifier.size(26.dp))
            }
            Column(
                Modifier.fillMaxSize(),
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.Center,
            ) {
                ShazamButton(isListening = listening, onTap = { listening = !listening })
                Spacer(Modifier.height(24.dp))
                Text(
                    if (listening) "Listening for music…" else "Tap to Shazam",
                    style = if (listening) ShazamText.Button.copy(color = Color.White) else ShazamText.Prompt,
                    color = Color.White,
                )
            }
        }
    }
}
```

### Result Card

```kotlin
@Composable
fun ShazamResultCard(title: String, artist: String, artworkUrl: String) {
    Column(
        Modifier
            .padding(horizontal = 20.dp)
            .clip(RoundedCornerShape(20.dp))
            .background(ShazamColors.Glass)
            .border(1.dp, ShazamColors.GlassStrong, RoundedCornerShape(20.dp))
            .padding(20.dp),
        verticalArrangement = Arrangement.spacedBy(20.dp),
    ) {
        Row(horizontalArrangement = Arrangement.spacedBy(16.dp)) {
            AsyncImage(
                model = artworkUrl, contentDescription = null,
                modifier = Modifier.size(88.dp).clip(RoundedCornerShape(12.dp)),
                contentScale = ContentScale.Crop,
            )
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(6.dp)) {
                Text(title, style = ShazamText.Result, color = Color.White, maxLines = 2)
                Text(artist, style = ShazamText.Subtitle, color = ShazamColors.TextSecondary, maxLines = 1)
            }
        }

        val ams = remember { MutableInteractionSource() }
        val amsPressed by ams.collectIsPressedAsState()
        Row(
            Modifier
                .fillMaxWidth()
                .clip(CircleShape)
                .background(if (amsPressed) Color(0xFFE8F0FF) else Color.White)
                .clickable(ams, indication = null) { }
                .padding(vertical = 14.dp),
            horizontalArrangement = Arrangement.Center,
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Icon(Icons.Filled.MusicNote, null, tint = ShazamColors.AppleMusicPink, modifier = Modifier.size(18.dp))
            Spacer(Modifier.width(8.dp))
            Text("Open in Apple Music", style = ShazamText.Button, color = ShazamColors.Space)
        }

        Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
            listOf(
                Icons.Filled.IosShare to "Share",
                Icons.Filled.Add to "Add",
                Icons.Filled.FormatQuote to "Lyrics",
            ).forEach { (icon, label) ->
                Column(
                    Modifier
                        .weight(1f)
                        .clip(CircleShape)
                        .background(ShazamColors.Glass)
                        .border(1.dp, Color.White.copy(alpha = 0.16f), CircleShape)
                        .padding(vertical = 12.dp),
                    horizontalAlignment = Alignment.CenterHorizontally,
                    verticalArrangement = Arrangement.spacedBy(6.dp),
                ) {
                    Icon(icon, label, tint = Color.White, modifier = Modifier.size(18.dp))
                    Text(label, style = ShazamText.ButtonSec, color = Color.White)
                }
            }
        }
    }
}
```

## 4. Listening State Machine

```kotlin
sealed interface ShazamState {
    data object Idle : ShazamState
    data object Listening : ShazamState
    data class Matched(val title: String, val artist: String) : ShazamState
    data object NoMatch : ShazamState
}
// tap → Listening; recognizer result → Matched (reveal card);
// failure → NoMatch (soft shake + HapticFeedbackType.LongPress / error vibration).
```

## 5. Navigation — No Bottom Bar

Shazam deliberately has **no `NavigationBar`**. The hero is the root; the library/history is a `ModalBottomSheet`:

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun ShazamRoot() {
    var showLibrary by remember { mutableStateOf(false) }
    val sheet = rememberModalBottomSheetState(skipPartiallyExpanded = false)

    ShazamHome()

    if (showLibrary) {
        ModalBottomSheet(
            onDismissRequest = { showLibrary = false },
            sheetState = sheet,
            containerColor = ShazamColors.GlassStrong,
            shape = RoundedCornerShape(topStart = 24.dp, topEnd = 24.dp),
            dragHandle = {
                Box(
                    Modifier.fillMaxWidth().padding(vertical = 12.dp),
                    contentAlignment = Alignment.Center,
                ) { Box(Modifier.size(36.dp, 4.dp).clip(CircleShape).background(Color.White.copy(alpha = 0.4f))) }
            },
        ) {
            // "Your Shazams" list — 68.dp rows, 52.dp art (10.dp radius), 1.dp Divider
        }
    }
}
```

> A true backdrop blur behind the sheet needs `HazeState`/`Modifier.haze` (the `dev.chrisbanes.haze` library) or a `RenderEffect.createBlurEffect` on API 31+. Material 3's `ModalBottomSheet` alone gives a scrim, not a blur.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Idle breathing | `rememberInfiniteTransition` `animateFloat` 1 → 1.04, `tween(2400, EaseInOut)`, `RepeatMode.Reverse` |
| Concentric rings | `animateFloat` 0 → 1 over `tween(1800, EaseOut)`, `RepeatMode.Restart`, staggered `delayMillis = i*600`; scale `1 + p*1.6`, alpha `0.22*(1-p)` |
| Tap | `animateFloatAsState` 1 → 0.94 `spring(dampingRatio = 0.8f)`, `HapticFeedbackType.LongPress` |
| Result reveal | `AnimatedVisibility` + `scaleIn(initialScale = 0.85f)` + `fadeIn()` over `spring(dampingRatio = 0.8f)` |
| No match | `Animatable` keyframes offset 0 → -8 → 8 → 0; error vibration |
| Sheet | `ModalBottomSheet` detents (`rememberModalBottomSheetState`) |

```kotlin
// Result reveal
AnimatedVisibility(
    visible = state is ShazamState.Matched,
    enter = scaleIn(initialScale = 0.85f, animationSpec = spring(dampingRatio = 0.8f)) + fadeIn(),
) { ShazamResultCard(/* … */) }
```

Haptics: prefer `LocalHapticFeedback`. For the heavy tap, `LocalView.current.performHapticFeedback(HapticFeedbackConstants.LONG_PRESS)`; for no-match use a `Vibrator` `VibrationEffect.createWaveform(...)`.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The Shazam mark itself ships as a licensed vector drawable — load via `ImageVector.vectorResource(R.drawable.shazam_glyph)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Profile / Settings | `person.crop.circle` | `Icons.Outlined.AccountCircle` |
| Library / History | `music.note.list` | `Icons.Filled.QueueMusic` |
| Open in Apple Music | `music.note` | `Icons.Filled.MusicNote` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Add | `plus` | `Icons.Filled.Add` |
| Lyrics | `text.quote` | `Icons.Filled.FormatQuote` |
| Close result | `xmark` | `Icons.Filled.Close` |
| Sheet chevron | `chevron.right` | `Icons.Filled.ChevronRight` |
| Search (library) | `magnifyingglass` | `Icons.Filled.Search` |
| Play / Pause (mini) | `play.fill` / `pause.fill` | `Icons.Filled.PlayArrow` / `Icons.Filled.Pause` |
| No-match | `waveform.slash` | `Icons.Filled.MusicOff` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Brush.radialGradient` + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. For a true sheet blur, `RenderEffect` is API 31+.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the dark gradient wants light-content system bars. The radial gradient draws full-bleed under the status bar — apply `Modifier.statusBarsPadding()` only to the top icon row.
- **Font scaling**: `sp` honors the user's font scale — keep it on result title, artist, sheet rows, body. Pin the centered "Tap to Shazam" prompt and listening label by deriving size from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Reduce Motion**: check `Settings.Global.ANIMATOR_DURATION_SCALE` (or wrap in an app setting) — when motion is reduced, replace ring emission with a static glow + a subtle alpha pulse.
- **TalkBack**: the button's `contentDescription` = "Shazam. Tap to identify the music playing around you."; announce state changes ("Listening", "Match found: <title> by <artist>", "No match found") via `LiveRegionMode.Assertive` on a status `Text`.
- **Touch targets**: the 132.dp button vastly exceeds the 48.dp minimum. Give the 26.dp top-bar icons a 48.dp hit area via padding.
- **Contrast**: periwinkle `#B8C4FF` on the dark gradient edge passes WCAG AA at 14sp+; validate over the brighter `#0088FF` core and darken toward `#A6B4FF` if targeting strict compliance.
- **No bottom bar**: do not add a Material `NavigationBar` — the single-purpose hero is the defining choice. Library is a `ModalBottomSheet`.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Shazam's brand requires the fixed radial blue gradient regardless of wallpaper. There is no light theme.
