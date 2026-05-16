# Bumble (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Bumble's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the `Hexagon` shape, the glowing heart action button, the It's-a-Match celebration, and the 24-hour countdown chip.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Bumble's stadium-loud yellow, pure-black-on-yellow contrast, the hexagon spatial signature, the heavy Brando ladder) while making everything idiomatic Android — a custom `Shape` for the hex, `NavigationBar` instead of a UITabBar, `Brush` glow approximated with elevation + a radial scrim, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for profile photos. No color extraction, so no `androidx.palette`.

## 1. Color Tokens

```kotlin
// ui/theme/BumbleColors.kt
import androidx.compose.ui.graphics.Color

object BumbleColors {
    // Brand
    val Yellow      = Color(0xFFFFC629) // the brand — CTAs, tab indicator, match, heart
    val HoneyDeep   = Color(0xFFF5B616) // pressed state on yellow
    val YellowLight = Color(0xFFFFE9A1) // soft chip fills, premium gradients

    // Mode colors (Date / BFF / Bizz)
    val BFFTeal     = Color(0xFF11AAA8)
    val BizzOrange  = Color(0xFFFF8000)

    // Canvas & Surfaces
    val Canvas      = Color(0xFFFFFFFF) // Date mode
    val BFFCream    = Color(0xFFFFFCF2) // BFF mode tint
    val Surface1    = Color(0xFFF5F5F5) // input fills, settings rows
    val Surface2    = Color(0xFFEDEDED) // pressed states, chip fills
    val Divider     = Color(0xFFE5E5E5) // 0.5dp hairlines

    // Text
    val Black       = Color(0xFF1F1F1F) // primary on white (warm, NOT pure)
    val Slate       = Color(0xFF5A5A5A) // secondary
    val Mist        = Color(0xFF9C9C9C) // tertiary, placeholders
    val OnYellow    = Color(0xFF000000) // pure black — required on yellow for WCAG AA

    // Semantic
    val MatchPink   = Color(0xFFE94B7B) // ONLY the It's-a-Match heart
    val Verified    = Color(0xFF0066FF) // verified checkmark
    val Warning     = Color(0xFFFF9500) // incomplete-profile warning
    val Error       = Color(0xFFD72638) // validation errors, expired countdown
    val Success     = Color(0xFF00A86B) // confirmation toasts

    // Dark mode (warm OLED — yellow unchanged)
    val DarkCanvas   = Color(0xFF0F0F0F)
    val DarkSurface1 = Color(0xFF1A1A1A)
    val DarkSurface2 = Color(0xFF2A2A2A)
    val DarkDivider  = Color(0xFF2F2F2F)
    val DarkText     = Color(0xFFF2F2F2)
    val DarkTextSec  = Color(0xFF9C9C9C)
    val HoneyDeepDk  = Color(0xFFFFD45C) // brightened for OLED pressed states
}
```

Wire it into a Material 3 `lightColorScheme` (Date mode) — Bumble's primary surface is white. Provide a parallel dark scheme; the yellow stays identical because it reads loud on OLED.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val BumbleLight = lightColorScheme(
    primary        = BumbleColors.Yellow,
    onPrimary      = BumbleColors.OnYellow,   // pure black on yellow — non-negotiable
    background     = BumbleColors.Canvas,
    onBackground   = BumbleColors.Black,
    surface        = BumbleColors.Canvas,
    onSurface      = BumbleColors.Black,
    surfaceVariant = BumbleColors.Surface1,
    outline        = BumbleColors.Divider,
    error          = BumbleColors.Error,
)

private val BumbleDark = darkColorScheme(
    primary        = BumbleColors.Yellow,     // unchanged on dark
    onPrimary      = BumbleColors.OnYellow,
    background     = BumbleColors.DarkCanvas,
    onBackground   = BumbleColors.DarkText,
    surface        = BumbleColors.DarkSurface1,
    onSurface      = BumbleColors.DarkText,
    surfaceVariant = BumbleColors.DarkSurface2,
    outline        = BumbleColors.DarkDivider,
    error          = BumbleColors.Error,
)

@Composable
fun BumbleTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) BumbleDark else BumbleLight,
        typography = BumbleTypography,
        content = content,
    )
```

## 2. Typography

Brando is the proprietary Bumble face — slab-influenced, humanist, heavy. Drop the TTFs in `res/font/` (lowercase, snake_case). Bumble has **no Light or Thin** — only 400/500/700/900. Fall back to Roboto (the closest free Android substitute); for a tighter brand match consider bundling Bricolage Grotesque / Manrope Heavy.

```kotlin
// ui/theme/BumbleType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Brando = FontFamily(
    Font(R.font.brando_regular, FontWeight.Normal),   // 400
    Font(R.font.brando_medium,  FontWeight.Medium),   // 500
    Font(R.font.brando_bold,    FontWeight.Bold),     // 700
    Font(R.font.brando_black,   FontWeight.Black),    // 900
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, same weights/tracking)
object BumbleText {
    val MatchHero    = TextStyle(Brando, fontWeight = FontWeight.Black,  fontSize = 44.sp, lineHeight = 46.sp, letterSpacing = (-0.8).sp)
    val Display      = TextStyle(Brando, fontWeight = FontWeight.Black,  fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.6).sp)
    val ScreenTitle  = TextStyle(Brando, fontWeight = FontWeight.Black,  fontSize = 24.sp, lineHeight = 28.sp, letterSpacing = (-0.4).sp)
    val CardName     = TextStyle(Brando, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section      = TextStyle(Brando, fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val Body         = TextStyle(Brando, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 22.sp)
    val BodyBold     = TextStyle(Brando, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 22.sp)
    val BodySmall    = TextStyle(Brando, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 20.sp)
    val Button       = TextStyle(Brando, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 16.sp)
    val ButtonLarge  = TextStyle(Brando, fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 18.sp)
    val Tab          = TextStyle(Brando, fontWeight = FontWeight.Bold,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp)
    val Chip         = TextStyle(Brando, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 13.sp)
    val Meta         = TextStyle(Brando, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 17.sp)
    val Counter      = TextStyle(Brando, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
    val Compliment   = TextStyle(Brando, fontWeight = FontWeight.Black,  fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val BumbleTypography = Typography(
    displayLarge  = BumbleText.Display,
    headlineLarge = BumbleText.ScreenTitle,
    titleLarge    = BumbleText.CardName,
    titleMedium   = BumbleText.Section,
    bodyMedium    = BumbleText.Body,
    labelSmall    = BumbleText.Tab,
)
```

The 24-hour countdown is the only place numerals must hold column width — pin tabular figures on `Meta` (countdown) and `Counter` (badges) with `TextStyle(fontFeatureSettings = "tnum")`.

## 3. Signature Components

### Hexagon Shape (the brand spatial signature)

Bumble's hex is point-up. This is the SwiftUI `Hexagon: Shape` ported to a Compose `Shape` — reuse it for match avatars, mode-toggle buttons, and the brand mark.

```kotlin
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Outline
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.Shape
import androidx.compose.ui.unit.Density
import androidx.compose.ui.unit.LayoutDirection
import kotlin.math.cos
import kotlin.math.min
import kotlin.math.sin

val HexagonShape = object : Shape {
    override fun createOutline(
        size: androidx.compose.ui.geometry.Size,
        layoutDirection: LayoutDirection,
        density: Density,
    ): Outline {
        val r = min(size.width, size.height) / 2f
        val cx = size.width / 2f
        val cy = size.height / 2f
        val path = Path()
        for (i in 0 until 6) {
            val angle = (Math.PI / 3 * i - Math.PI / 2).toFloat() // point-up
            val x = cx + r * cos(angle)
            val y = cy + r * sin(angle)
            if (i == 0) path.moveTo(x, y) else path.lineTo(x, y)
        }
        path.close()
        return Outline.Generic(path)
    }
}
```

### Yellow Primary CTA

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.foundation.clickable
import androidx.compose.ui.unit.dp

@Composable
fun BumblePrimaryButton(
    label: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    hasGlow: Boolean = false,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "ctaScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .fillMaxWidth()
            .height(56.dp)
            .scale(scale)
            // Yellow glow halo — Android has no colored blur shadow, so a tinted
            // ambient/spot shadow approximates the brand glow on hero CTAs.
            .then(
                if (hasGlow)
                    Modifier.shadow(24.dp, CircleShape, ambientColor = BumbleColors.Yellow,
                        spotColor = BumbleColors.Yellow)
                else Modifier
            )
            .clip(CircleShape) // 28dp capsule == full pill at 56dp height
            .background(if (pressed) BumbleColors.HoneyDeep else BumbleColors.Yellow)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .impact(.medium)
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = BumbleText.Button, color = BumbleColors.OnYellow) // pure black
    }
}
```

### Swipe Action Row (under the swipe card)

Five circular buttons; the yellow heart is the hero with a glow halo. Press scales to 0.9; the haptic varies per button.

```kotlin
import androidx.compose.foundation.border
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Close
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.Star
import androidx.compose.material.icons.automirrored.filled.Undo
import androidx.compose.material3.Icon
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun SwipeActionRow(
    onRewind: () -> Unit,
    onPass: () -> Unit,
    onLike: () -> Unit,
    onSuper: () -> Unit,
    onCompliment: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.spacedBy(24.dp, Alignment.CenterHorizontally),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        ActionCircle(48.dp, BumbleColors.Surface1, null, HapticFeedbackType.TextHandleMove, onRewind) {
            Icon(Icons.AutoMirrored.Filled.Undo, "Rewind", tint = BumbleColors.Mist, modifier = Modifier.size(20.dp))
        }
        ActionCircle(56.dp, BumbleColors.Canvas, BumbleColors.Black, HapticFeedbackType.TextHandleMove, onPass) {
            Icon(Icons.Filled.Close, "Pass", tint = BumbleColors.Black, modifier = Modifier.size(22.dp))
        }
        // Heart Yes — 64dp hero with yellow glow halo
        HeartActionButton(onLike)
        ActionCircle(56.dp, BumbleColors.Yellow, null, HapticFeedbackType.LongPress, onSuper) {
            Icon(Icons.Filled.Star, "SuperSwipe", tint = BumbleColors.OnYellow, modifier = Modifier.size(22.dp))
        }
        ActionCircle(56.dp, BumbleColors.Canvas, BumbleColors.Yellow, HapticFeedbackType.TextHandleMove, onCompliment) {
            // Bee glyph is proprietary — ship as a vector drawable; placeholder star here
            Icon(Icons.Filled.Star, "Compliment", tint = BumbleColors.Yellow, modifier = Modifier.size(22.dp))
        }
    }
}

@Composable
private fun ActionCircle(
    diameter: androidx.compose.ui.unit.Dp,
    fill: Color,
    stroke: Color?,
    haptic: HapticFeedbackType,
    onClick: () -> Unit,
    content: @Composable () -> Unit,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.9f else 1f, label = "actionScale")
    val haptics = LocalHapticFeedback.current
    Box(
        Modifier
            .size(diameter)
            .scale(scale)
            .clip(CircleShape)
            .background(fill)
            .then(if (stroke != null) Modifier.border(1.5.dp, stroke, CircleShape) else Modifier)
            .clickable(interaction, indication = null) { haptics.performHapticFeedback(haptic); onClick() },
        contentAlignment = Alignment.Center,
    ) { content() }
}

@Composable
private fun HeartActionButton(onLike: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        targetValue = if (pressed) 1.2f else 1f,
        animationSpec = spring(dampingRatio = 0.6f, stiffness = 600f),
        label = "heartScale",
    )
    val haptics = LocalHapticFeedback.current
    Box(
        Modifier
            .size(64.dp)
            .scale(scale)
            .shadow(16.dp, CircleShape, ambientColor = BumbleColors.Yellow, spotColor = BumbleColors.Yellow)
            .clip(CircleShape)
            .background(BumbleColors.Yellow)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .impact(.medium)
                onLike()
            },
        contentAlignment = Alignment.Center,
    ) { Icon(Icons.Filled.Favorite, "Like", tint = BumbleColors.OnYellow, modifier = Modifier.size(28.dp)) }
}
```

### Swipe Card (the hero component)

3:4 photo, 12dp radius (firmer edge than Hinge), 4-segment Stories-style progress bar at the top, bottom gradient carrying name + verified check + bio.

```kotlin
import androidx.compose.foundation.gestures.detectHorizontalDragGestures
import androidx.compose.material.icons.filled.Verified
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import coil.compose.AsyncImage

@Composable
fun SwipeCard(
    photoUrls: List<String>,
    name: String,
    age: Int,
    bio: String,
    isVerified: Boolean,
    onSwiped: (liked: Boolean) -> Unit,
    modifier: Modifier = Modifier,
) {
    var current by remember { mutableStateOf(0) }
    var dragX by remember { mutableStateOf(0f) }

    Box(
        modifier = modifier
            .fillMaxWidth()
            .aspectRatio(3f / 4f)
            .shadow(16.dp, RoundedCornerShape(12.dp), spotColor = Color.Black.copy(alpha = 0.12f))
            .clip(RoundedCornerShape(12.dp))
            .pointerInput(Unit) {
                detectHorizontalDragGestures(
                    onDragEnd = { if (kotlin.math.abs(dragX) > size.width * 0.3f) onSwiped(dragX > 0); dragX = 0f },
                ) { _, drag -> dragX += drag }
            },
    ) {
        AsyncImage(
            model = photoUrls[current],
            contentDescription = "$name, $age",
            modifier = Modifier.fillMaxSize(),
            contentScale = ContentScale.Crop,
        )
        // Bottom gradient overlay (bottom 30%, transparent → 0.7 black)
        Box(
            Modifier
                .align(Alignment.BottomCenter)
                .fillMaxWidth()
                .fillMaxHeight(0.3f)
                .background(Brush.verticalGradient(listOf(Color.Transparent, Color.Black.copy(alpha = 0.7f))))
        )
        // Photo progress bar — 4 segments, 3dp tall, 4dp gap
        Row(
            Modifier.fillMaxWidth().padding(12.dp),
            horizontalArrangement = Arrangement.spacedBy(4.dp),
        ) {
            repeat(photoUrls.size) { i ->
                Box(
                    Modifier
                        .weight(1f)
                        .height(3.dp)
                        .clip(CircleShape)
                        .background(if (i == current) Color.White else Color.White.copy(alpha = 0.4f))
                )
            }
        }
        // Name + age + verified + bio
        Column(
            Modifier.align(Alignment.BottomStart).padding(horizontal = 20.dp, vertical = 20.dp),
            verticalArrangement = Arrangement.spacedBy(4.dp),
        ) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                Text("$name, $age", style = BumbleText.CardName, color = Color.White)
                if (isVerified) {
                    Icon(Icons.Filled.Verified, "Verified", tint = BumbleColors.Verified, modifier = Modifier.size(18.dp))
                }
            }
            Text(bio, style = BumbleText.Body, color = Color.White, maxLines = 1, overflow = TextOverflow.Ellipsis)
        }
    }
}
```

### 24-Hour Countdown Chip (the core mechanic)

```kotlin
import androidx.compose.animation.core.RepeatMode
import androidx.compose.animation.core.infiniteRepeatable
import androidx.compose.animation.core.rememberInfiniteTransition
import androidx.compose.animation.core.tween
import androidx.compose.ui.draw.alpha

@Composable
fun CountdownChip(remainingSeconds: Long, modifier: Modifier = Modifier) {
    val expired = remainingSeconds <= 0
    val label = if (expired) "Time's up — extend?"
        else "Your turn: ${remainingSeconds / 3600}h ${(remainingSeconds % 3600) / 60}m"

    val t = rememberInfiniteTransition(label = "countdownPulse")
    val a by t.animateFloat(
        initialValue = 0.95f, targetValue = 1f,
        animationSpec = infiniteRepeatable(tween(1500), RepeatMode.Reverse),
        label = "pulse",
    )

    Box(
        modifier
            .fillMaxWidth()
            .height(32.dp)
            .alpha(a)
            .clip(CircleShape)
            .background(if (expired) BumbleColors.Error else BumbleColors.Yellow)
            .border(1.dp, if (expired) BumbleColors.Error else BumbleColors.HoneyDeep, CircleShape)
            .padding(horizontal = 16.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(
            label,
            style = BumbleText.Meta.copy(fontWeight = FontWeight.Bold, fontFeatureSettings = "tnum"),
            color = if (expired) Color.White else BumbleColors.OnYellow, // pure black on yellow
        )
    }
}
```

### It's a Match! Celebration

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.ui.draw.scale

@Composable
fun MatchCelebration(
    myAvatarUrl: String,
    theirAvatarUrl: String,
    theirName: String,
    onSendMessage: () -> Unit,
    onKeepSwiping: () -> Unit,
) {
    val heartScale = remember { Animatable(0.2f) }
    val haptics = LocalHapticFeedback.current

    LaunchedEffect(Unit) {
        haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .notification(.success)
        heartScale.animateTo(1f, spring(dampingRatio = 0.55f, stiffness = 500f)) // bounce in
    }

    Box(Modifier.fillMaxSize().background(BumbleColors.Yellow)) { // full-screen yellow canvas
        Column(
            Modifier.fillMaxSize().padding(horizontal = 24.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.Center,
        ) {
            Box(contentAlignment = Alignment.Center) {
                Row(horizontalArrangement = Arrangement.spacedBy(32.dp)) {
                    HexAvatar(myAvatarUrl)
                    HexAvatar(theirAvatarUrl)
                }
                Icon(
                    Icons.Filled.Favorite, contentDescription = null,
                    tint = BumbleColors.MatchPink, // ONLY place this pink appears
                    modifier = Modifier.size(48.dp).scale(heartScale.value),
                )
            }
            Spacer(Modifier.height(24.dp))
            Text("It's a Match!", style = BumbleText.MatchHero, color = BumbleColors.Black)
            Text("You and $theirName want to chat", style = BumbleText.Body, color = BumbleColors.Black)
            Spacer(Modifier.height(12.dp))
            Text("She has 24 hours to make the first move", style = BumbleText.Meta,
                color = BumbleColors.Black.copy(alpha = 0.8f))
            Spacer(Modifier.height(48.dp))
            // Primary CTA is WHITE here (yellow canvas) with warm-black text
            Box(
                Modifier.fillMaxWidth().height(56.dp).clip(CircleShape).background(Color.White)
                    .clickable { onSendMessage() },
                contentAlignment = Alignment.Center,
            ) { Text("Send a Message", style = BumbleText.ButtonLarge, color = BumbleColors.Black) }
            Spacer(Modifier.height(16.dp))
            Text("Keep Swiping", style = BumbleText.Button, color = BumbleColors.Black,
                modifier = Modifier.clickable { onKeepSwiping() })
        }
    }
}

@Composable
fun HexAvatar(url: String, size: androidx.compose.ui.unit.Dp = 120.dp) {
    AsyncImage(
        model = url,
        contentDescription = null,
        modifier = Modifier
            .size(size)
            .clip(HexagonShape)
            .border(4.dp, Color.White, HexagonShape),
        contentScale = ContentScale.Crop,
    )
}
```

### Chat Input with Send Button

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.ui.text.input.TextFieldValue

@Composable
fun BumbleChatInput(
    value: TextFieldValue,
    onValueChange: (TextFieldValue) -> Unit,
    onSend: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val canSend = value.text.isNotBlank()
    val haptics = LocalHapticFeedback.current
    Row(
        modifier.fillMaxWidth().padding(16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box(
            Modifier.weight(1f).clip(CircleShape).background(BumbleColors.Surface1)
                .padding(horizontal = 20.dp, vertical = 14.dp),
        ) {
            BasicTextField(
                value = value, onValueChange = onValueChange,
                textStyle = BumbleText.Body.copy(color = BumbleColors.Black),
                decorationBox = { inner ->
                    if (value.text.isEmpty()) {
                        Text("Type your message…", style = BumbleText.Body, color = BumbleColors.Mist)
                    }
                    inner()
                },
            )
        }
        Box(
            Modifier
                .size(40.dp)
                .clip(CircleShape)
                .background(if (canSend) BumbleColors.Yellow else BumbleColors.Surface1)
                .clickable(enabled = canSend) {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                    onSend()
                },
            contentAlignment = Alignment.Center,
        ) {
            Icon(Icons.Filled.ArrowUpward, "Send",
                tint = if (canSend) BumbleColors.OnYellow else BumbleColors.Mist,
                modifier = Modifier.size(18.dp))
        }
    }
}
```

## 4. Mode Switching (the distinctive system)

Bumble's defining structural feature is one app, three products: Date (yellow) / BFF (teal) / Bizz (orange). Switching modes recolors **all** brand chrome while the hexagon shape stays constant. Drive this with a `CompositionLocal` so every composable reads the active accent without prop-drilling.

```kotlin
enum class BumbleMode(val accent: Color, val pressed: Color) {
    Date(BumbleColors.Yellow, BumbleColors.HoneyDeep),
    BFF(BumbleColors.BFFTeal, Color(0xFF0E908E)),
    Bizz(BumbleColors.BizzOrange, Color(0xFFE57300)),
}

val LocalBumbleMode = androidx.compose.runtime.staticCompositionLocalOf { BumbleMode.Date }

@Composable
fun BumbleModeProvider(mode: BumbleMode, content: @Composable () -> Unit) {
    androidx.compose.runtime.CompositionLocalProvider(LocalBumbleMode provides mode, content = content)
}

// A hexagonal mode-toggle button — literally hex-shaped, not rectangular
@Composable
fun ModeHexButton(mode: BumbleMode, active: Boolean, onClick: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    Box(
        Modifier
            .size(64.dp)
            .clip(HexagonShape)
            .then(
                if (active) Modifier.background(mode.accent)
                else Modifier.border(2.dp, BumbleColors.Mist, HexagonShape)
            )
            .clickable {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ≈ .selection
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(Icons.Filled.Star, mode.name, // mode glyph (hive/heart/briefcase) — vector drawable in prod
            tint = if (active) BumbleColors.OnYellow else BumbleColors.Mist, modifier = Modifier.size(24.dp))
    }
}
```

Then read `LocalBumbleMode.current.accent` anywhere yellow appears (CTAs, tab indicator, match ring) so the whole app retints on switch. The hex is never replaced with a circle.

## 5. Navigation

Bumble has 5 bottom tabs: People / Hives / Matches / Chats / Profile — the Hives icon is a literal honeycomb. Use Material 3 `NavigationBar`. The iOS tab bar is opaque (Bumble likes hard surfaces, no blur) — Android has no live blur anyway, so use a solid `Canvas` container with a 0.5dp top divider. **Active tint is warm Bumble Black** with a 4dp yellow indicator dot below the label — not the Material pill.

```kotlin
import androidx.compose.material.icons.filled.Chat
import androidx.compose.material.icons.filled.Hexagon
import androidx.compose.material.icons.filled.People
import androidx.compose.material.icons.filled.Person
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun BumbleBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    val accent = LocalBumbleMode.current.accent
    NavigationBar(containerColor = BumbleColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "People"  to Icons.Filled.People,
            "Hives"   to Icons.Filled.Hexagon,
            "Matches" to Icons.Filled.Favorite,
            "Chats"   to Icons.Filled.Chat,
            "Profile" to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = BumbleText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = BumbleColors.Black,  // warm black, not yellow
                    selectedTextColor   = BumbleColors.Black,
                    unselectedIconColor = BumbleColors.Mist,
                    unselectedTextColor = BumbleColors.Mist,
                    indicatorColor      = accent.copy(alpha = 0.0f), // no Material pill — Bumble has none
                ),
            )
        }
    }
}
```

Render the 4dp yellow (or mode-accent) indicator dot manually 4dp beneath the selected item's label if you want exact parity — `NavigationBar` has no built-in dot affordance. The top header is a slim 56dp row (no large-title) with `ScreenTitle` text leading and trailing action glyphs.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Heart tap | `animateFloatAsState` 1 → 1.2 with `spring(dampingRatio = 0.6f)`; `HapticFeedbackType.LongPress` (.impact medium) |
| SuperSwipe (star) | full-screen yellow flash 200ms via `Animatable` alpha 0→1→0; `HapticFeedbackType.LongPress` (.success) |
| Swipe commit | `animateFloatAsState` `rotationZ` → 8° + `offset` 500dp off-screen over 350ms (`spring`) |
| Yes/No confetti | `rememberInfiniteTransition` / `Animatable` driving falling yellow hexes for 800ms |
| Match celebration | photos slide in from edges; pink heart `Animatable.animateTo(1f, spring(dampingRatio = 0.55f))` |
| 24h chip pulse | `rememberInfiniteTransition` alpha 0.95 → 1.0 over 1500ms `RepeatMode.Reverse` |
| Hex loading state | `rememberInfiniteTransition` `rotationZ` 0 → 360 over 1200ms on a 6-hex honeycomb |
| Tab switch | 200ms `tween`; indicator dot `fadeIn`; `HapticFeedbackType.TextHandleMove` (.selection) |

```kotlin
// SuperSwipe full-screen yellow flash
@Composable
fun SuperSwipeFlash(trigger: Boolean) {
    val flash = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(trigger) {
        if (trigger) {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .notification(.success)
            flash.animateTo(1f, tween(200))
            flash.animateTo(0f, tween(200))
        }
    }
    if (flash.value > 0f) {
        Box(Modifier.fillMaxSize().alpha(flash.value).background(BumbleColors.Yellow))
    }
}
```

Honor **Reduce Motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, skip the hex confetti and the heart scale-bounce on match — keep the haptics. For richer haptics use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's `.soft` Compliment tap. For the match-celebration shared photo, prefer `SharedTransitionLayout` + `Modifier.sharedElement()` (Compose 1.7+).

## 7. Icons

Bumble uses outline/filled pairs; the closest first-party set is `androidx.compose.material:material-icons-extended`. The **bee glyph** and the **hexagonal "B" logomark** are proprietary — ship them as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Heart action (filled) | `heart.fill` | `Icons.Filled.Favorite` |
| X Pass | `xmark` | `Icons.Filled.Close` |
| Star SuperSwipe | `star.fill` | `Icons.Filled.Star` |
| Rewind | `arrow.uturn.backward` | `Icons.AutoMirrored.Filled.Undo` |
| Bee Compliment | `ant.fill` (placeholder) | custom vector drawable (proprietary bee) |
| Match heart | `heart.fill` (Match Pink) | `Icons.Filled.Favorite` tinted `MatchPink` |
| Send (chat input) | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Verified check | `checkmark.seal.fill` | `Icons.Filled.Verified` (tint `Verified`) |
| People tab | `person.2` / `.fill` | `Icons.Filled.People` |
| Hives tab | `hexagon` / `.fill` | `Icons.Filled.Hexagon` |
| Matches tab | `heart` / `.fill` | `Icons.Filled.Favorite` |
| Chats tab | `bubble.left` / `.fill` | `Icons.Filled.Chat` |
| Profile tab | `person.crop.circle` / `.fill` | `Icons.Filled.Person` |
| Filters | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Brand "B" logomark | (hex bee mark) | custom vector drawable |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `SharedTransitionLayout` comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The light Date canvas wants dark system-bar icons (`WindowCompat` light appearance); the dark scheme wants light icons. Pin the chat input above the IME with `Modifier.imePadding()` and the tab bar above the gesture nav with `Scaffold` insets.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on match hero, display, screen titles, body, bio, section headers. Pin layout-sensitive text (10sp tab labels, 11sp counter badges, 13sp chip labels, the 24-hour countdown) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: announce the match screen as one node — `Modifier.semantics(mergeDescendants = true) { contentDescription = "It's a Match. You and $name want to chat. She has 24 hours. Send a Message button." }`. Give the swipe card an accessibility action set (`"Like"`, `"Pass"`, `"SuperSwipe"`, `"Compliment"`) so users can act without dragging. Re-announce the countdown chip when it crosses a 30-minute boundary.
- **Touch targets**: Material guidance is 48.dp minimum. The 64dp heart and 56dp CTA clear it; the 48dp Rewind and 40dp send button are at/above the floor — keep the send button's 40dp visual inside a 48dp hit area via padding.
- **Contrast**: Bumble Yellow `#FFC629` requires **pure black** (`#000000`) text — warm `#1F1F1F` on yellow *fails* WCAG AA at small sizes. Use `BumbleColors.OnYellow` on every yellow surface, no exceptions. `#9C9C9C` Mist on white is borderline at body size — keep it to ≥14sp metadata and validate with a contrast checker.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Bumble's identity is the fixed `#FFC629` yellow (and the teal/orange mode accents) regardless of wallpaper. (Material You suits Google/Material-first apps; Bumble is a strong-brand exception.) The yellow stays identical across light and dark; only canvas/surface/text tokens invert.
