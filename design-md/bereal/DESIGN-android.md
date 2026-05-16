# BeReal (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports BeReal's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (BeReal's pure-black darkroom, the monochrome no-brand-hue stance, the dual-lens composite, RealMoji reactions) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `CameraX` instead of AVFoundation, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for images, and `CameraX` for the dual capture.

## 1. Color Tokens

```kotlin
// ui/theme/BeRealColors.kt
import androidx.compose.ui.graphics.Color

object BeRealColors {
    // Canvas & Surfaces
    val Canvas   = Color(0xFF000000)
    val Surface1 = Color(0xFF1C1C1E)
    val Surface2 = Color(0xFF2C2C2E)
    val Divider  = Color(0xFF2C2C2E)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF8E8E93)
    val TextTertiary  = Color(0xFF636366)

    // Accent — intentionally monochrome, no brand hue
    val Accent   = Color(0xFFFFFFFF)
    val OnAccent = Color(0xFF000000)

    // Semantic
    val Late   = Color(0xFFFFD60A)
    val Error  = Color(0xFFFF453A)
    val Online = Color(0xFF30D158)
}
```

Wire it into a Material 3 `darkColorScheme`. BeReal is dark-only by design — the photographic darkroom metaphor depends on true black; do **not** provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val BeRealScheme = darkColorScheme(
    primary        = BeRealColors.Accent,
    onPrimary      = BeRealColors.OnAccent,    // black on white — intentional inversion
    background     = BeRealColors.Canvas,
    onBackground   = BeRealColors.TextPrimary,
    surface        = BeRealColors.Surface1,
    onSurface      = BeRealColors.TextPrimary,
    surfaceVariant = BeRealColors.Surface2,
    outline        = BeRealColors.Divider,
    error          = BeRealColors.Error,
)

@Composable
fun BeRealTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = BeRealScheme, typography = BeRealTypography, content = content)
```

## 2. Typography

BeReal ships **no custom typeface** — it uses the system font. On Android that is Roboto; reference `FontFamily.Default` so there is nothing to drop in `res/font/`.

```kotlin
// ui/theme/BeRealType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

private val Sys = FontFamily.Default   // BeReal uses the system font, no custom face

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object BeRealText {
    val Timer        = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 26.sp) // use tabular figures via FontFeatureSettings("tnum")
    val ScreenTitle  = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Username     = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val Caption      = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp)
    val Body         = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val CommentAuthor= TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 18.sp)
    val Meta         = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val LateBadge    = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.2.sp)
    val Button       = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val Tab          = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp)
    val Retake       = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp)
}

// Map onto Material 3 slots so stock components inherit the system-font, dark identity
val BeRealTypography = Typography(
    headlineMedium = BeRealText.ScreenTitle,
    titleMedium    = BeRealText.Username,
    bodyMedium     = BeRealText.Body,
    labelSmall     = BeRealText.Tab,
)
```

## 3. Signature Components

### Dual-Lens Composite Card (the signature)

```kotlin
import androidx.compose.foundation.gestures.detectDragGestures
import androidx.compose.foundation.layout.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.unit.dp
import androidx.compose.foundation.shape.RoundedCornerShape
import coil.compose.AsyncImage

enum class SelfieCorner { TopStart, TopEnd, BottomStart, BottomEnd }

@Composable
fun DualLensCard(backUrl: String, frontUrl: String, modifier: Modifier = Modifier) {
    var corner by remember { mutableStateOf(SelfieCorner.TopStart) }
    var lifted by remember { mutableStateOf(false) }
    val scale by androidx.compose.animation.core.animateFloatAsState(
        if (lifted) 1.05f else 1f,
        androidx.compose.animation.core.spring(dampingRatio = 0.75f, stiffness = 300f),
        label = "selfieLift",
    )

    Box(
        modifier
            .fillMaxWidth()
            .aspectRatio(3f / 4f)
            .shadow(24.dp, RoundedCornerShape(18.dp), spotColor = androidx.compose.ui.graphics.Color.Black.copy(0.6f))
            .clip(RoundedCornerShape(18.dp)),
    ) {
        AsyncImage(model = backUrl, contentDescription = "BeReal back photo",
            modifier = Modifier.fillMaxSize())

        AsyncImage(
            model = frontUrl,
            contentDescription = "BeReal selfie",
            modifier = Modifier
                .align(corner.toAlignment())
                .padding(12.dp)
                .size(100.dp, 134.dp)
                .scale(scale)
                .shadow(if (lifted) 18.dp else 0.dp, RoundedCornerShape(14.dp))
                .clip(RoundedCornerShape(14.dp))
                .pointerInput(Unit) {
                    detectDragGestures(
                        onDragStart = { lifted = true },
                        onDragEnd = {
                            lifted = false
                            corner = SelfieCorner.entries.random()
                        },
                    ) { _, _ -> }
                },
        )
    }
}

private fun SelfieCorner.toAlignment() = when (this) {
    SelfieCorner.TopStart    -> Alignment.TopStart
    SelfieCorner.TopEnd      -> Alignment.TopEnd
    SelfieCorner.BottomStart -> Alignment.BottomStart
    SelfieCorner.BottomEnd   -> Alignment.BottomEnd
}
```

### Capture Button (Dual Shutter)

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import kotlinx.coroutines.delay
import kotlinx.coroutines.launch

@Composable
fun CaptureButton(onCapture: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()
    val scale by androidx.compose.animation.core.animateFloatAsState(if (pressed) 0.92f else 1f, label = "capScale")
    val disc  by androidx.compose.animation.core.animateDpAsState(if (pressed) 64.dp else 0.dp, label = "capDisc")

    Box(
        modifier
            .size(76.dp)
            .scale(scale)
            .clip(androidx.compose.foundation.shape.CircleShape)
            .border(4.dp, BeRealColors.Accent, androidx.compose.foundation.shape.CircleShape)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)   // heavy ≈ back camera
                onCapture()
                scope.launch {
                    delay(500)
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // soft ≈ front
                }
            },
        contentAlignment = Alignment.Center,
    ) {
        Box(Modifier.size(disc).clip(androidx.compose.foundation.shape.CircleShape).background(BeRealColors.Accent))
    }
}
```

### Primary / Secondary Button

```kotlin
enum class BeRealBtn { Filled, Outline }

@Composable
fun BeRealButton(text: String, onClick: () -> Unit, style: BeRealBtn = BeRealBtn.Filled, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by androidx.compose.animation.core.animateFloatAsState(if (pressed) 0.97f else 1f, label = "btn")
    val filled = style == BeRealBtn.Filled

    Box(
        modifier
            .scale(scale)
            .clip(androidx.compose.foundation.shape.CircleShape)
            .then(
                if (filled) Modifier.background(BeRealColors.Accent)
                else Modifier.border(1.dp, BeRealColors.TextPrimary.copy(alpha = 0.4f), androidx.compose.foundation.shape.CircleShape)
            )
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = if (filled) 32.dp else 24.dp, vertical = if (filled) 14.dp else 12.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = BeRealText.Button,
            color = if (filled) BeRealColors.OnAccent else BeRealColors.TextPrimary)
    }
}
```

### Countdown Banner & Late Badge

```kotlin
import androidx.compose.ui.text.font.FontFeature
import androidx.compose.ui.text.style.TextAlign

@Composable
fun CountdownBanner(remaining: String, modifier: Modifier = Modifier) {
    // Android has no live blur — use a 60%-opaque black pill with the amber border
    Box(
        modifier
            .fillMaxWidth()
            .clip(androidx.compose.foundation.shape.CircleShape)
            .background(androidx.compose.ui.graphics.Color.Black.copy(alpha = 0.6f))
            .border(1.dp, BeRealColors.Late.copy(alpha = 0.4f), androidx.compose.foundation.shape.CircleShape)
            .padding(horizontal = 20.dp, vertical = 12.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(
            "⏰ $remaining left to capture a BeReal",
            style = BeRealText.Timer.copy(fontFeatureSettings = "tnum"),  // tabular digits
            color = BeRealColors.Late,
            textAlign = TextAlign.Center,
        )
    }
}

@Composable
fun LateBadge(text: String) =
    Text("⚠ $text", style = BeRealText.LateBadge, color = BeRealColors.Late)
```

### RealMoji Reaction Cluster

```kotlin
@Composable
fun RealMojiCluster(selfieUrls: List<String>, onAdd: () -> Unit, modifier: Modifier = Modifier) {
    Row(modifier, verticalAlignment = Alignment.CenterVertically) {
        selfieUrls.forEachIndexed { i, url ->
            AsyncImage(
                model = url,
                contentDescription = "Reaction",          // never a count — reactions are faces
                modifier = Modifier
                    .offset(x = if (i == 0) 0.dp else (-8 * i).dp)  // overlap by 8dp
                    .size(30.dp)
                    .clip(androidx.compose.foundation.shape.CircleShape)
                    .border(2.dp, androidx.compose.ui.graphics.Color.Black, androidx.compose.foundation.shape.CircleShape),
            )
        }
        Box(
            Modifier
                .padding(start = 12.dp)
                .size(30.dp)
                .clip(androidx.compose.foundation.shape.CircleShape)
                .border(1.5.dp, BeRealColors.TextPrimary.copy(alpha = 0.6f), androidx.compose.foundation.shape.CircleShape)
                .clickable(onClick = onAdd),
            contentAlignment = Alignment.Center,
        ) { Text("+", style = BeRealText.Button, color = BeRealColors.TextPrimary) }
    }
}
```

## 4. Distinctive System — Selfie Corner-Snap in Compose

The front selfie inset is the soul of BeReal. `DualLensCard` (above) wires `detectDragGestures`: drag start sets `lifted = true` (scale animates to 1.05 via `spring(dampingRatio = 0.75f)`), drag end snaps `corner` to a `SelfieCorner` and the `Alignment` recomposes. A non-drag tap on the selfie swaps which camera is full-bleed — animate a `Crossfade` between the two `AsyncImage`s. Keep the snap spring at `dampingRatio = 0.75f, stiffness = 300f` to match the iOS feel.

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. BeReal's iOS tab bar is opaque true-black with **no blur** — Android has no first-class live blur anyway, so a solid `#000000` surface is an exact match. Active tint is **white** (BeReal has no brand hue); kill the Material indicator pill.

```kotlin
@Composable
fun BeRealBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = BeRealColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Friends"  to Icons.Filled.People,
            "Official" to Icons.Filled.Star,
            "Search"   to Icons.Filled.Search,
            "Profile"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(26.dp)) },
                label = { Text(label, style = BeRealText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = BeRealColors.TextPrimary,         // white, no brand hue
                    selectedTextColor   = BeRealColors.TextPrimary,
                    unselectedIconColor = BeRealColors.TextPrimary.copy(alpha = 0.45f),
                    unselectedTextColor = BeRealColors.TextPrimary.copy(alpha = 0.45f),
                    indicatorColor = androidx.compose.ui.graphics.Color.Transparent, // BeReal has no pill
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Dual-shutter capture | white-flash overlay (`AnimatedVisibility` 120ms) + `HapticFeedbackType.LongPress`, then `delay(500)` + soft haptic for the front lens |
| Camera flip | `graphicsLayer { rotationY = … }` animated 0 → 180 over 350ms `tween`, light haptic |
| Selfie drag-snap | `animateFloatAsState`/alignment recompose with `spring(dampingRatio = 0.75f, stiffness = 300f)` |
| RealMoji pop | `Animatable` keyframes 0 → 1.2 → 1 over 280ms |
| Feed un-blur | friends' cards `Modifier.blur(30.dp)` → `0.dp` once you post, `animateDpAsState` 400ms |

```kotlin
// Camera flip
val rot by animateFloatAsState(if (flipped) 180f else 0f, tween(350), label = "flip")
Box(Modifier.graphicsLayer { rotationY = rot; cameraDistance = 12f * density }) { /* viewfinder */ }

// RealMoji pop
val pop = remember { Animatable(0f) }
LaunchedEffect(Unit) {
    pop.animateTo(1.2f, tween(160)); pop.animateTo(1f, spring())
}
```

Haptics: prefer `LocalHapticFeedback`. For the heavy capture impact use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.LONG_PRESS)` or a `Vibrator` `VibrationEffect.createOneShot(20, 255)`; follow with a lighter `CONTEXT_CLICK` ~500ms later for the front camera.

## 7. Icons

BeReal uses simple glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Camera flip | `arrow.triangle.2.circlepath.camera` | `Icons.Filled.FlipCameraIos` |
| Flash | `bolt.fill` / `bolt.slash.fill` | `Icons.Filled.FlashOn` / `Icons.Filled.FlashOff` |
| Late badge | `exclamationmark.triangle.fill` | `Icons.Filled.Warning` |
| Add RealMoji | `plus` | `Icons.Filled.Add` |
| Comments | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Friends (tab) | `person.2.fill` | `Icons.Filled.People` |
| Official (tab) | `star.circle.fill` | `Icons.Filled.Star` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| BTS video | `play.rectangle.on.rectangle` | `Icons.Filled.Collections` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `CameraX` dual capture + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the pure-black canvas wants light-content system bars via `WindowCompat`. Let the dual-lens card draw edge-to-edge horizontally (`Modifier.fillMaxWidth()`, no inset) while keeping the tab bar inside `systemBars` insets.
- **Font scaling**: `sp` honors the user's font scale — keep it on usernames, captions, comments. Pin the countdown (`fontFeatureSettings = "tnum"` and a fixed size), late badge, retake count, and tab labels by deriving from `dp` or a `CompositionLocalProvider(LocalDensity …, fontScale = 1f)`.
- **TalkBack**: set `contentDescription` on the dual-lens card ("jordan's BeReal, 2 hours late"); mark the selfie as a draggable element with a custom `stateDescription`; announce RealMojis as "Reaction from <name>" and **never** a numeric count — there is none.
- **Touch targets**: Material guidance is 48.dp minimum. The 76.dp capture button is well clear; give the 30.dp RealMoji `+` a 48.dp hit area via padding.
- **Contrast**: `#8E8E93` on `#000000` passes WCAG AA at 13sp+. The amber `#FFD60A` on black is high-contrast — reserve it for lateness/urgency only.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — BeReal's identity is the fixed pure-black canvas and the deliberate *absence* of a brand hue, regardless of wallpaper.
