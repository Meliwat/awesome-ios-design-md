# WHOOP (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports WHOOP's instrument-panel aesthetic to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the Recovery ring with red→yellow→green interpolation, the strain bar, the sleep stage chart, neon glows, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (WHOOP's pitch-black cockpit canvas, neon Strain Green, the score-driven Recovery ramp, tabular DIN numerals, neon-glow elevation) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `drawArc`/`Canvas` instead of SwiftUI `Path`, a translucent `Surface` instead of `.systemThinMaterialDark`, `sp`/`dp` instead of `pt`. WHOOP is dark-canvas-only by design — there is no light scheme.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. No remote-image or color-extraction libraries are needed — WHOOP's palette is fixed brand chrome and the Recovery color is computed, not extracted.

## 1. Color Tokens

```kotlin
// ui/theme/WhoopColors.kt
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.lerp

object WhoopColors {
    // Canvas & surfaces (dark-only)
    val Canvas   = Color(0xFF0A0A0A)
    val Surface1 = Color(0xFF1C1C1E)
    val Surface2 = Color(0xFF2A2A2A)
    val Surface3 = Color(0xFF3A3A3A)
    val Divider  = Color(0xFF252525)
    val Hairline = Color(0xFF1A1A1A)

    // Strain (primary brand)
    val Strain       = Color(0xFF00FF7B)
    val StrainBright = Color(0xFF67E26B)
    val StrainDim    = Color(0xFF005F2F)

    // Recovery spectrum
    val RecoveryRed    = Color(0xFFFF0026)
    val RecoveryYellow = Color(0xFFFFDE00)
    val RecoveryGreen  = Color(0xFF16EC06)

    // Sleep
    val SleepBlue       = Color(0xFF0093E7)
    val SleepBlueBright = Color(0xFF4FB8FF)
    val REMPurple       = Color(0xFF9C4DFF)
    val DeepIndigo      = Color(0xFF3D3DFF)
    val LightCyan       = Color(0xFF3DD9FF)

    // Text
    val BrightWhite = Color(0xFFFFFFFF)
    val SoftWhite   = Color(0xFFF5F5F7)
    val Gray400     = Color(0xFFA1A1AA)
    val Gray600     = Color(0xFF5F5F65)
    val Gray700     = Color(0xFF3F3F45)

    // Semantic
    val AlertRed   = Color(0xFFFF453A)
    val WarningAmber = Color(0xFFFFAA00)
    val InfoBlue   = Color(0xFF0A84FF)
    val Success    = Color(0xFF30D158)
    val PremiumGold = Color(0xFFD4AF37)

    /** Recovery ring color for a score 0..100, lerped red→yellow→green. */
    fun recoveryColor(percent: Float): Color {
        val p = percent.coerceIn(0f, 100f) / 100f
        return when {
            p < 0.33f -> RecoveryRed
            p < 0.67f -> lerp(RecoveryRed, RecoveryYellow, (p - 0.33f) / 0.34f)
            else      -> lerp(RecoveryYellow, RecoveryGreen, (p - 0.67f) / 0.33f)
        }
    }
}
```

WHOOP has **no light mode**. Provide a single `darkColorScheme` — the dark canvas and neon Strain Green are inseparable from the brand.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val WhoopScheme = darkColorScheme(
    primary        = WhoopColors.Strain,
    onPrimary      = WhoopColors.Canvas,   // intentional: black on neon green
    background     = WhoopColors.Canvas,
    onBackground   = WhoopColors.SoftWhite,
    surface        = WhoopColors.Surface1,
    onSurface      = WhoopColors.SoftWhite,
    surfaceVariant = WhoopColors.Surface2,
    outline        = WhoopColors.Divider,
    error          = WhoopColors.AlertRed,
)

@Composable
fun WhoopTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = WhoopScheme, typography = WhoopTypography, content = content)
```

## 2. Typography

WHOOP licenses **DIN 2014**. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to a tabular sans — bundle Inter (`res/font/inter_*`), the only acceptable substitute because it shares DIN's mechanical, slightly condensed proportions and ships tabular figures.

```kotlin
// ui/theme/WhoopType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextGeometricTransform
import androidx.compose.ui.unit.sp

val Din2014 = FontFamily(
    Font(R.font.din2014_regular,   FontWeight.Normal),    // 400
    Font(R.font.din2014_demi,      FontWeight.Medium),    // 500
    Font(R.font.din2014_bold,      FontWeight.Bold),      // 700
    Font(R.font.din2014_extrabold, FontWeight.ExtraBold), // 800
    // Fallback chain: Inter ships tabular figures with similar proportions
    Font(R.font.inter_regular,     FontWeight.Normal),
    Font(R.font.inter_bold,        FontWeight.Bold),
)

// Tabular figures are non-negotiable — apply to every metric value.
import androidx.compose.ui.text.font.FontFeatureSetting
private const val TNUM = "tnum"

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1).
object WhoopText {
    val RecoveryHero = TextStyle(Din2014, fontWeight = FontWeight.ExtraBold, fontSize = 76.sp, lineHeight = 76.sp, letterSpacing = (-1.6).sp, fontFeatureSettings = TNUM)
    val StrainHero   = TextStyle(Din2014, fontWeight = FontWeight.ExtraBold, fontSize = 56.sp, lineHeight = 56.sp, letterSpacing = (-1.0).sp, fontFeatureSettings = TNUM)
    val SleepHero    = TextStyle(Din2014, fontWeight = FontWeight.ExtraBold, fontSize = 48.sp, lineHeight = 48.sp, letterSpacing = (-0.8).sp, fontFeatureSettings = TNUM)
    val SectionCaps  = TextStyle(Din2014, fontWeight = FontWeight.Bold,      fontSize = 14.sp, lineHeight = 14.sp, letterSpacing = 1.6.sp)  // UPPERCASE
    val LargeNav     = TextStyle(Din2014, fontWeight = FontWeight.Bold,      fontSize = 24.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Subhead      = TextStyle(Din2014, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 22.sp)
    val CardTitle    = TextStyle(Din2014, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 19.sp, letterSpacing = (-0.05).sp)
    val Body         = TextStyle(Din2014, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val MetaCaps     = TextStyle(Din2014, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 1.2.sp)  // UPPERCASE
    val MetricValue  = TextStyle(Din2014, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 18.sp, fontFeatureSettings = TNUM)
    val MetricLabel  = TextStyle(Din2014, fontWeight = FontWeight.Medium,    fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.6.sp)  // UPPERCASE
    val Button       = TextStyle(Din2014, fontWeight = FontWeight.Bold,      fontSize = 14.sp, lineHeight = 14.sp, letterSpacing = 1.0.sp)  // UPPERCASE
    val Tab          = TextStyle(Din2014, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.8.sp)  // UPPERCASE
    val ChartAxis    = TextStyle(Din2014, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp, fontFeatureSettings = TNUM)
}

// Headers/labels are uppercased at the call site (text.uppercase()) — Compose has no text-transform.

val WhoopTypography = Typography(
    displayLarge  = WhoopText.RecoveryHero,
    headlineLarge = WhoopText.LargeNav,
    titleLarge    = WhoopText.Subhead,
    titleMedium   = WhoopText.CardTitle,
    bodyMedium    = WhoopText.Body,
    labelSmall    = WhoopText.Tab,
)
```

## 3. Signature Components

### Primary CTA (instrument-panel button — 4dp radius, never a pill)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun WhoopPrimaryButton(
    label: String, // pass already-uppercase or call label.uppercase()
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "ctaScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .fillMaxWidth()
            .heightIn(min = 48.dp)
            .scale(scale)
            .clip(RoundedCornerShape(4.dp)) // instrument-panel rectangle
            .background(if (pressed) WhoopColors.StrainBright else WhoopColors.Strain)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                onClick()
            }
            .padding(horizontal = 28.dp, vertical = 14.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(label.uppercase(), style = WhoopText.Button, color = WhoopColors.Canvas)
    }
}

@Composable
fun WhoopOutlineButton(label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Box(
        modifier = modifier
            .fillMaxWidth()
            .heightIn(min = 48.dp)
            .clip(RoundedCornerShape(4.dp))
            .border(1.5.dp, WhoopColors.Strain, RoundedCornerShape(4.dp))
            .clickable(onClick = onClick)
            .padding(horizontal = 28.dp, vertical = 14.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(label.uppercase(), style = WhoopText.Button, color = WhoopColors.Strain)
    }
}
```

### Strain Bar (gradient fill + sliding white marker)

```kotlin
import androidx.compose.foundation.layout.BoxWithConstraints
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.unit.Dp

@Composable
fun StrainBar(
    currentStrain: Float, // 0f..21f
    modifier: Modifier = Modifier,
    height: Dp = 12.dp,
) {
    val fraction = (currentStrain / 21f).coerceIn(0f, 1f)
    val animFraction by animateFloatAsState(
        targetValue = fraction,
        animationSpec = spring(dampingRatio = 0.7f, stiffness = 250f), // response ~0.5
        label = "strainFill",
    )
    Column(verticalArrangement = Arrangement.spacedBy(6.dp)) {
        // Tick labels at 8/10/14/18 → 0.4/0.5/0.7/0.9
        BoxWithConstraints(Modifier.fillMaxWidth().height(14.dp)) {
            val w = maxWidth
            listOf(0.4f to "LOW", 0.5f to "MOD", 0.7f to "HIGH", 0.9f to "ALL-OUT").forEach { (pos, lbl) ->
                Text(
                    lbl,
                    style = WhoopText.ChartAxis,
                    color = WhoopColors.Gray400,
                    modifier = Modifier.offset(x = w * pos),
                )
            }
        }
        BoxWithConstraints(Modifier.fillMaxWidth().height(height)) {
            val w = maxWidth
            Box(Modifier.fillMaxSize().clip(RoundedCornerShape(6.dp)).background(WhoopColors.Surface2))
            Box(
                Modifier
                    .fillMaxHeight()
                    .width(w * animFraction)
                    .clip(RoundedCornerShape(6.dp))
                    .background(
                        Brush.horizontalGradient(
                            listOf(WhoopColors.StrainDim, WhoopColors.Strain, WhoopColors.StrainBright)
                        )
                    )
            )
            Box(
                Modifier
                    .offset(x = (w * animFraction - 8.dp).coerceAtLeast(0.dp))
                    .align(Alignment.CenterStart)
                    .size(16.dp)
                    .clip(CircleShape)
                    .background(WhoopColors.BrightWhite)
            )
        }
    }
}
```

### Sleep Stage Chart (horizontal stacked bar)

```kotlin
enum class SleepStage(val color: Color) {
    Awake(WhoopColors.Gray600),
    Light(WhoopColors.LightCyan),
    Deep(WhoopColors.DeepIndigo),
    Rem(WhoopColors.REMPurple),
}

data class SleepSegment(val stage: SleepStage, val durationMin: Int)

@Composable
fun SleepStageChart(
    segments: List<SleepSegment>,
    startLabel: String, // "11 PM"
    endLabel: String,   // "7 AM"
    modifier: Modifier = Modifier,
) {
    val total = segments.sumOf { it.durationMin }.coerceAtLeast(1)
    Column(modifier, verticalArrangement = Arrangement.spacedBy(6.dp)) {
        Row(
            Modifier
                .fillMaxWidth()
                .height(32.dp)
                .clip(RoundedCornerShape(4.dp)),
            horizontalArrangement = Arrangement.spacedBy(1.dp),
        ) {
            segments.forEach { seg ->
                Box(
                    Modifier
                        .weight(seg.durationMin.toFloat() / total)
                        .fillMaxHeight()
                        .background(seg.stage.color)
                )
            }
        }
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
            Text(startLabel, style = WhoopText.ChartAxis, color = WhoopColors.Gray400)
            Text(endLabel, style = WhoopText.ChartAxis, color = WhoopColors.Gray400)
        }
    }
}
```

### Activity Row

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ChevronRight
import androidx.compose.material.icons.filled.DirectionsRun
import androidx.compose.material3.Icon
import androidx.compose.ui.graphics.SolidColor

@Composable
fun ActivityRow(
    icon: androidx.compose.ui.graphics.vector.ImageVector,
    name: String,
    strain: Float,
    duration: String,
    startTime: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Column {
        Row(
            modifier = modifier
                .fillMaxWidth()
                .height(72.dp)
                .background(WhoopColors.Canvas)
                .clickable(onClick = onClick)
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(
                Modifier
                    .size(40.dp)
                    .border(1.5.dp, WhoopColors.Strain, CircleShape),
                contentAlignment = Alignment.Center,
            ) {
                Icon(icon, contentDescription = null, tint = WhoopColors.Strain, modifier = Modifier.size(18.dp))
            }
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(name, style = WhoopText.CardTitle, color = WhoopColors.BrightWhite)
                Text("%.1f".format(strain), style = WhoopText.MetricValue, color = WhoopColors.Strain)
            }
            Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(duration, style = WhoopText.Body, color = WhoopColors.SoftWhite)
                Text(startTime, style = WhoopText.ChartAxis, color = WhoopColors.Gray400)
            }
            Icon(Icons.Filled.ChevronRight, contentDescription = null, tint = WhoopColors.Gray600, modifier = Modifier.size(12.dp))
        }
        Box(Modifier.fillMaxWidth().height(0.5.dp).background(WhoopColors.Divider))
    }
}
```

### Metric Card (hairline border, no shadow)

```kotlin
@Composable
fun MetricCard(label: String, modifier: Modifier = Modifier, content: @Composable ColumnScope.() -> Unit) {
    Column(
        modifier = modifier
            .clip(RoundedCornerShape(12.dp))
            .background(WhoopColors.Surface1)
            .border(1.dp, WhoopColors.Divider, RoundedCornerShape(12.dp))
            .padding(20.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Text(label.uppercase(), style = WhoopText.MetricLabel, color = WhoopColors.Gray400)
        content()
    }
}
```

## 4. Recovery Ring (red→yellow→green interpolation) + Neon Glows

WHOOP's hero. The ring is **open at the top** (a small gap between stroke start and end), drawn with `Canvas`/`drawArc`. Its color is computed by score via `WhoopColors.recoveryColor`. Active/live elements get a **neon glow** instead of a neutral shadow — Android has no live blur, so approximate the iOS `rgba(0,255,123,0.18) 0 0 12px` halo with a stacked, blurred `Box` (`Modifier.blur` + `RenderEffect`) or a soft radial-gradient backdrop.

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.size
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.Dp

@Composable
fun RecoveryRing(
    percent: Float, // 0f..100f
    modifier: Modifier = Modifier,
    diameter: Dp = 240.dp,
    strokeWidth: Dp = 10.dp,
) {
    val animPercent by animateFloatAsState(
        targetValue = percent,
        animationSpec = spring(dampingRatio = 0.78f, stiffness = 80f), // ~1200ms ease-out w/ slight overshoot
        label = "recoveryFill",
    )
    val ringColor = WhoopColors.recoveryColor(percent)

    Box(modifier.size(diameter), contentAlignment = Alignment.Center) {
        Canvas(Modifier.size(diameter)) {
            val sw = strokeWidth.toPx()
            val inset = sw / 2
            val arcSize = Size(size.width - sw, size.height - sw)
            val topLeft = Offset(inset, inset)
            val gapDeg = 4f // ~2pt visual gap, "open" at top
            val startAngle = -90f + gapDeg / 2f
            val sweepFull = 360f - gapDeg
            // Track
            drawArc(
                color = WhoopColors.Surface2,
                startAngle = startAngle,
                sweepAngle = sweepFull,
                useCenter = false,
                topLeft = topLeft,
                size = arcSize,
                style = Stroke(width = sw, cap = StrokeCap.Round),
            )
            // Fill — proportional to score
            drawArc(
                color = ringColor,
                startAngle = startAngle,
                sweepAngle = sweepFull * (animPercent / 100f),
                useCenter = false,
                topLeft = topLeft,
                size = arcSize,
                style = Stroke(width = sw, cap = StrokeCap.Round),
            )
        }
        Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text("RECOVERY", style = WhoopText.SectionCaps, color = WhoopColors.Gray400)
            Row(verticalAlignment = Alignment.Bottom) {
                Text("${percent.toInt()}", style = WhoopText.RecoveryHero, color = WhoopColors.BrightWhite)
                Text("%", style = WhoopText.SectionCaps.copy(fontSize = 24.sp), color = WhoopColors.BrightWhite)
            }
        }
    }
}

/** Soft neon halo — Android's analog to iOS's `0 0 12px rgba(0,255,123,0.18)` glow. */
fun Modifier.neonGlow(color: Color, radius: Dp, alpha: Float): Modifier = this.then(
    Modifier.drawBehind {
        drawCircle(
            brush = Brush.radialGradient(
                colors = listOf(color.copy(alpha = alpha), Color.Transparent),
                radius = radius.toPx(),
            ),
            radius = radius.toPx(),
        )
    }
)
```

### Live Workout Banner (heavy green glow + pulsing dot)

```kotlin
import androidx.compose.animation.core.RepeatMode
import androidx.compose.animation.core.infiniteRepeatable
import androidx.compose.animation.core.rememberInfiniteTransition
import androidx.compose.animation.core.tween

@Composable
fun LiveWorkoutBanner(elapsed: String, currentStrain: Float, modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "liveBanner")
    val dotAlpha by t.animateFloat(
        initialValue = 0.4f, targetValue = 1f,
        animationSpec = infiniteRepeatable(tween(1200), RepeatMode.Reverse),
        label = "dotPulse",
    )
    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(28.dp)
            .background(WhoopColors.Strain)
            .neonGlow(WhoopColors.Strain, radius = 48.dp, alpha = 0.32f)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Box(
            Modifier
                .size(8.dp)
                .clip(CircleShape)
                .background(WhoopColors.Canvas.copy(alpha = dotAlpha))
        )
        Text(
            "LIVE — $elapsed · STRAIN ${"%.1f".format(currentStrain)}",
            style = WhoopText.MetaCaps,
            color = WhoopColors.Canvas,
        )
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar` with the four canonical tabs (Overview, Coaching, Community, Stats). WHOOP's iOS tab bar uses `.systemThinMaterialDark` blur; Android has no first-class live blur, so use a 92%-opaque `Surface1`. **Active tint is Strain Green with a soft glow** — apply `neonGlow` behind the selected icon.

```kotlin
import androidx.compose.material.icons.filled.BarChart
import androidx.compose.material.icons.filled.Dashboard
import androidx.compose.material.icons.filled.Groups
import androidx.compose.material.icons.filled.SelfImprovement
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun WhoopBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = WhoopColors.Surface1.copy(alpha = 0.92f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "OVERVIEW"  to Icons.Filled.Dashboard,
            "COACHING"  to Icons.Filled.SelfImprovement,
            "COMMUNITY" to Icons.Filled.Groups,
            "STATS"     to Icons.Filled.BarChart,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    val m = if (selected == i)
                        Modifier.size(22.dp).neonGlow(WhoopColors.Strain, 18.dp, 0.18f)
                    else Modifier.size(22.dp)
                    Icon(icon, contentDescription = label, modifier = m)
                },
                label = { Text(label, style = WhoopText.Tab) },
                alwaysShowLabel = true,
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = WhoopColors.Strain,
                    selectedTextColor   = WhoopColors.Strain,
                    unselectedIconColor = WhoopColors.Gray600,
                    unselectedTextColor = WhoopColors.Gray600,
                    indicatorColor      = Color.Transparent, // no Material pill — WHOOP has none
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Recovery ring fill | `animateFloatAsState` with `spring(dampingRatio = 0.78f, stiffness = 80f)` — ~1200ms ease-out + slight overshoot |
| Strain marker slide | `animateFloatAsState` with `spring(dampingRatio = 0.7f, stiffness = 250f)` (iOS response 0.5) |
| Heart-rate beat pulse | `Animatable` keyframes `scale` 1 → 1.15 → 1 over 600ms, looped per beat |
| Live banner dot | `rememberInfiniteTransition` alpha 0.4 ↔ 1 every 1200ms |
| Day switcher tap | `animateFloatAsState` scale 1 → 1.08 (spring); `HapticFeedbackType.SegmentTick` |
| Primary CTA tap | scale 1 → 0.98 over 100ms, then `HapticFeedbackType.LongPress` (~medium impact) |
| Sleep segment tap | callout `AnimatedVisibility` `slideInVertically + fadeIn` 200ms; `HapticFeedbackType.TextHandleMove` |
| Sync complete | `Animatable` scale 1 → 1.3 → 1 once; `HapticFeedbackType.Confirm` (API 34) |

```kotlin
// Heart-rate beat pulse — syncs the heart glyph to the live BPM
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.keyframes

@Composable
fun HeartPulse(bpm: Int, content: @Composable () -> Unit) {
    val scale = remember { Animatable(1f) }
    LaunchedEffect(bpm) {
        while (true) {
            scale.animateTo(1.15f, keyframes { durationMillis = 200; 1.15f at 100 })
            scale.animateTo(1f, tween(400))
            kotlinx.coroutines.delay((60_000L / bpm.coerceAtLeast(40)) - 600)
        }
    }
    Box(Modifier.scale(scale.value)) { content() }
}
```

Haptics: prefer `LocalHapticFeedback`. `HapticFeedbackType.SegmentTick` ≈ iOS `.selection` for the day switcher and strain scrub; `HapticFeedbackType.LongPress` ≈ `.impact(.medium)` for CTAs. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(12, 80)`.

## 7. Icons

WHOOP ships a **proprietary DIN-icon set** — none of the tab glyphs have exact Material twins. Use `androidx.compose.material:material-icons-extended` as a substitute and ship WHOOP's real glyphs as vector drawables loaded via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Overview tab | `circle.dashed` | `Icons.Filled.Dashboard` (or vector drawable) |
| Coaching tab | `person.crop.square` | `Icons.Filled.SelfImprovement` |
| Community tab | `person.3` | `Icons.Filled.Groups` |
| Stats tab | `chart.bar` | `Icons.Filled.BarChart` |
| Activity — run | `figure.run` | `Icons.Filled.DirectionsRun` |
| Activity — cycling | `figure.outdoor.cycle` | `Icons.Filled.DirectionsBike` |
| Activity — yoga | `figure.yoga` | `Icons.Filled.SelfImprovement` |
| Activity — strength | `dumbbell.fill` | `Icons.Filled.FitnessCenter` |
| Heart-rate live | `heart.fill` | `Icons.Filled.Favorite` |
| Battery | `battery.75` | `Icons.Filled.BatteryFull` |
| Sync | `arrow.triangle.2.circlepath` | `Icons.Filled.Sync` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Row chevron | `chevron.right` | `Icons.Filled.ChevronRight` |
| Live banner dot | `circle.fill` | `Icons.Filled.Circle` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `drawArc`, springs, and `RenderEffect` blur are comfortable at 24+; `Modifier.blur` for true glow needs API 31, so the radial-gradient `neonGlow` fallback covers 24–30). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity` with light-content system bars (the canvas is near-black). The live workout banner should sit *above* the status-bar inset, replacing its background; apply `Scaffold` insets so the tab bar clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale — keep it on body, card titles, coaching headlines, and the large nav title. **Pin** every hero metric (76sp Recovery, 56sp Strain, 48sp Sleep), tab labels, chart axes, and metric labels by deriving them from a fixed-`fontScale` `LocalDensity` — they are layout-critical and the ring/bar geometry depends on them not reflowing.
- **TalkBack**: announce the Recovery ring as `"Recovery 73 percent, high recovery"`; the strain bar as `"Current strain 11.8 out of 21, high range"`; the sleep chart as a single merged node summarizing total + stage percentages. Use `Modifier.semantics(mergeDescendants = true)` on the activity row and CTA.
- **Touch targets**: Material guidance is 48.dp minimum. Day-switcher chips render at 36.dp visual — pad to 48.dp hit area. The primary CTA is already 48.dp; sleep-stage segments need a 24.dp minimum hit-width even when visually thinner.
- **Reduce Motion**: respect the system "remove animations" setting — skip the Recovery ring fill (snap to value), the heart-beat pulse, and the live-banner dot pulse; the day switcher should not scale.
- **Contrast**: Bright White on `#0A0A0A` exceeds AAA. Strain Green `#00FF7B` on `#0A0A0A` meets AA only at large/heavy sizes — keep it for short caps labels, ring/bar fills, and icons, **never body text**. `#A1A1AA` Gray400 is the secondary-text floor; do not push secondary text dimmer than this on the black canvas.
- **No light mode & no dynamic color**: there is no light scheme, and Material You `dynamicColorScheme()` must stay **off** — WHOOP's identity is the fixed black canvas, neon Strain Green, and the computed Recovery ramp regardless of wallpaper. Force the dark scheme at the root.
