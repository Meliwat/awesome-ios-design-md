# Nike Run Club (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports NRC's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the signature Volt run-tracking ring, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (NRC's true-black stadium void, radioactive Volt accent, screaming Trade Gothic Condensed) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `drawArc` `Canvas` ring instead of a SwiftUI `Circle().trim`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for plan-card photography. No color extraction — NRC's accent system is fixed Volt/Red, so Palette is not needed.

## 1. Color Tokens

```kotlin
// ui/theme/NikeColors.kt
import androidx.compose.ui.graphics.Color

object NikeColors {
    // Nike Brand
    val Volt        = Color(0xFFCCFF00)
    val VoltPressed = Color(0xFF9FCC00)
    val VoltDim     = Color(0xFF8AA800) // light-mode accessible accent
    val Red         = Color(0xFFFA5400)
    val BrakeRed    = Color(0xFFE50916)

    // Canvas & Surfaces
    val Canvas    = Color(0xFF000000) // true black — Nike's stadium void
    val Charcoal  = Color(0xFF0A0A0A)
    val Surface1  = Color(0xFF1A1A1A)
    val Surface2  = Color(0xFF2B2B2B)
    val Divider   = Color(0xFF333333)

    // Text
    val White = Color(0xFFFFFFFF)
    val Gray1 = Color(0xFFB3B3B3)
    val Gray2 = Color(0xFF8E8E8E)
    val Gray3 = Color(0xFF5C5C5C)

    // Heart Rate Zones (workout results "training effect" page)
    val HR1Blue   = Color(0xFF3D87F4)
    val HR2Green  = Color(0xFF48C77E)
    val HR3Yellow = Color(0xFFFFD600)
    val HR4Orange = Color(0xFFFA5400) // same as Red
    val HR5Red    = Color(0xFFE50916)

    // Semantic
    val Warning = Color(0xFFFFC400)
}
```

Wire it into a Material 3 `darkColorScheme` so ripples, dividers, and default component colors inherit the brand. NRC is dark-first by brand design; a light scheme exists only for accessibility — keep the app dark regardless of the system setting.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val NikeScheme = darkColorScheme(
    primary        = NikeColors.Volt,
    onPrimary      = NikeColors.Canvas,   // black on Volt
    secondary      = NikeColors.Red,
    background      = NikeColors.Canvas,
    onBackground   = NikeColors.White,
    surface        = NikeColors.Surface1,
    onSurface      = NikeColors.White,
    surfaceVariant = NikeColors.Surface2,
    outline        = NikeColors.Divider,
    error          = NikeColors.BrakeRed,
)

@Composable
fun NikeTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = NikeScheme, typography = NikeTypography, content = content)
```

## 2. Typography

Trade Gothic Next LT (Heavy/Bold Condensed) is proprietary (Linotype/Monotype). Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to **Oswald** (a close geometric-condensed Google Font) or system condensed. Body/metadata use the system font (Roboto, the SF Pro analog). Stats are tabular — set `fontFeatureSettings = "tnum"`.

```kotlin
// ui/theme/NikeType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val TradeGothic = FontFamily(
    Font(R.font.trade_gothic_next_bold,  FontWeight.Bold),   // 700 Bold Condensed
    Font(R.font.trade_gothic_next_heavy, FontWeight.Black),  // 900 Heavy Condensed
)
// Body / metadata — system (Roboto) is the SF Pro analog
val NikeBodyFamily = FontFamily.Default

private const val TNUM = "tnum" // tabular digits — pace columns must align

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, ALL CAPS at call sites)
object NikeText {
    val RunHero     = TextStyle(TradeGothic, fontWeight = FontWeight.Black, fontSize = 88.sp, lineHeight = 84.sp, letterSpacing = 0.5.sp, fontFeatureSettings = TNUM)
    val PreHero     = TextStyle(TradeGothic, fontWeight = FontWeight.Black, fontSize = 64.sp, lineHeight = 61.sp, letterSpacing = 0.4.sp, fontFeatureSettings = TNUM)
    val Headline    = TextStyle(TradeGothic, fontWeight = FontWeight.Black, fontSize = 48.sp, lineHeight = 46.sp, letterSpacing = 0.3.sp)
    val ScreamHdr   = TextStyle(TradeGothic, fontWeight = FontWeight.Black, fontSize = 32.sp, lineHeight = 32.sp, letterSpacing = 0.3.sp)
    val SubHdr      = TextStyle(TradeGothic, fontWeight = FontWeight.Bold,  fontSize = 22.sp, lineHeight = 24.sp, letterSpacing = 0.3.sp)
    val StatLabel   = TextStyle(TradeGothic, fontWeight = FontWeight.Bold,  fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 1.0.sp)
    val StatValue   = TextStyle(TradeGothic, fontWeight = FontWeight.Black, fontSize = 64.sp, lineHeight = 61.sp, letterSpacing = 0.4.sp, fontFeatureSettings = TNUM)
    val Button      = TextStyle(TradeGothic, fontWeight = FontWeight.Black, fontSize = 17.sp, lineHeight = 17.sp, letterSpacing = 0.5.sp)
    val Achievement = TextStyle(TradeGothic, fontWeight = FontWeight.Black, fontSize = 56.sp, lineHeight = 53.sp, fontFeatureSettings = TNUM)

    // SF Pro analog (Roboto) for body / metadata — mixed case allowed
    val CardTitle = TextStyle(NikeBodyFamily, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val Body      = TextStyle(NikeBodyFamily, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val BodyBold  = TextStyle(NikeBodyFamily, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val Meta      = TextStyle(NikeBodyFamily, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Caption   = TextStyle(NikeBodyFamily, fontWeight = FontWeight.Medium,   fontSize = 11.sp, lineHeight = 14.sp, letterSpacing = 0.1.sp)
    val Tab       = TextStyle(NikeBodyFamily, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.3.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val NikeTypography = Typography(
    displayLarge  = NikeText.RunHero,
    headlineLarge = NikeText.ScreamHdr,
    titleMedium   = NikeText.CardTitle,
    bodyMedium    = NikeText.Body,
    labelSmall    = NikeText.Tab,
)
```

## 3. Signature Components

### Primary Action Button ("START RUN" / "LET'S DO THIS" / "FINISH")

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.border
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
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

enum class NikeButtonStyle { White, Volt, Outline }

@Composable
fun NikeActionButton(
    title: String, // pass already-UPPERCASE
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    style: NikeButtonStyle = NikeButtonStyle.White,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        targetValue = if (pressed) 0.98f else 1f,
        animationSpec = spring(dampingRatio = 0.7f, stiffness = 700f),
        label = "ctaScale",
    )
    val haptics = LocalHapticFeedback.current
    val bg = when (style) {
        NikeButtonStyle.White   -> NikeColors.White
        NikeButtonStyle.Volt    -> NikeColors.Volt
        NikeButtonStyle.Outline -> Color.Transparent
    }
    val fg = if (style == NikeButtonStyle.Outline) NikeColors.White else NikeColors.Canvas

    Box(
        modifier = modifier
            .fillMaxWidth()
            .height(56.dp)
            .scale(scale)
            .clip(RoundedCornerShape(32.dp)) // very rounded, not a full pill
            .background(bg)
            .then(if (style == NikeButtonStyle.Outline) Modifier.border(2.dp, NikeColors.White, RoundedCornerShape(32.dp)) else Modifier)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // heaviest — Nike's committed CTA
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = NikeText.Button, color = fg)
    }
}
```

### Volt Progress Ring + Run Tracking Screen

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke

@Composable
fun VoltProgressRing(
    progress: Float, // 0f..1f
    modifier: Modifier = Modifier,
    diameter: Dp = 280.dp,
    strokeWidth: Dp = 6.dp,
) {
    val animated by animateFloatAsState(
        targetValue = progress.coerceIn(0f, 1f),
        animationSpec = spring(stiffness = 50f), // smooth fill toward the goal
        label = "ringProgress",
    )
    Canvas(modifier.size(diameter)) {
        val sw = strokeWidth.toPx()
        val inset = sw / 2
        val arcSize = Size(size.width - sw, size.height - sw)
        // Track
        drawArc(
            color = NikeColors.White.copy(alpha = 0.1f),
            startAngle = 0f, sweepAngle = 360f, useCenter = false,
            topLeft = androidx.compose.ui.geometry.Offset(inset, inset),
            size = arcSize,
            style = Stroke(width = sw, cap = StrokeCap.Round),
        )
        // Volt fill — starts at 12 o'clock (-90°), clockwise
        drawArc(
            color = NikeColors.Volt,
            startAngle = -90f, sweepAngle = 360f * animated, useCenter = false,
            topLeft = androidx.compose.ui.geometry.Offset(inset, inset),
            size = arcSize,
            style = Stroke(width = sw, cap = StrokeCap.Round),
        )
    }
}

@Composable
fun NRCStat(label: String, value: String, modifier: Modifier = Modifier) {
    Column(modifier, horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(4.dp)) {
        Text(label.uppercase(), style = NikeText.StatLabel, color = NikeColors.White.copy(alpha = 0.7f))
        Text(value, style = NikeText.StatValue, color = NikeColors.White)
    }
}

@Composable
fun RunTrackingScreen(
    elapsed: String,  // "12:34"
    distance: String, // "1.4"
    pace: String,     // "8:42"
    hr: String,       // "152"
    progress: Float,
    paused: Boolean,
    onPauseToggle: () -> Unit,
    onLock: () -> Unit,
) {
    val haptics = LocalHapticFeedback.current
    Box(
        Modifier.fillMaxSize().background(NikeColors.Canvas),
        contentAlignment = Alignment.TopCenter,
    ) {
        Column(
            Modifier.fillMaxSize().padding(top = 64.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(32.dp),
        ) {
            Row(
                Modifier.fillMaxWidth().padding(horizontal = 24.dp),
                horizontalArrangement = Arrangement.SpaceBetween,
            ) {
                NRCStat("Distance", distance, Modifier.weight(1f))
                NRCStat("Min/Mi", pace, Modifier.weight(1f))
                NRCStat("BPM", hr, Modifier.weight(1f))
            }
            Box(contentAlignment = Alignment.Center) {
                VoltProgressRing(progress)
                Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(4.dp)) {
                    Text(elapsed, style = NikeText.RunHero, color = NikeColors.White)
                    Text("ELAPSED", style = NikeText.StatLabel, color = NikeColors.White.copy(alpha = 0.7f))
                }
            }
            Spacer(Modifier.weight(1f))
            Row(
                Modifier.fillMaxWidth().padding(bottom = 48.dp, end = 16.dp),
                horizontalArrangement = Arrangement.Center,
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Box(
                    Modifier
                        .size(72.dp)
                        .clip(androidx.compose.foundation.shape.CircleShape)
                        .background(if (paused) NikeColors.Volt else NikeColors.White)
                        .clickable {
                            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // heavy
                            onPauseToggle()
                        },
                    contentAlignment = Alignment.Center,
                ) {
                    Icon(
                        if (paused) Icons.Filled.PlayArrow else Icons.Filled.Pause,
                        contentDescription = if (paused) "Resume" else "Pause",
                        tint = NikeColors.Canvas,
                        modifier = Modifier.size(28.dp),
                    )
                }
            }
        }
    }
}
```

### Run Plan Card (Home Feed)

```kotlin
import coil.compose.AsyncImage
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.layout.ContentScale

@Composable
fun RunPlanCard(
    photoUrl: String,
    eyebrow: String,     // "GUIDED RUN · 25 MIN"
    title: String,       // "RECOVERY MILE"
    description: String,  // "Coach Bennett · Easy effort"
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "planScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .height(320.dp)
            .scale(scale)
            .clip(RoundedCornerShape(16.dp)) // no shadow — Nike is flat on black
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                onClick()
            },
        contentAlignment = Alignment.BottomStart,
    ) {
        AsyncImage(model = photoUrl, contentDescription = null, modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
        Box(
            Modifier
                .fillMaxSize()
                .background(Brush.verticalGradient(listOf(Color.Transparent, Color.Black.copy(alpha = 0.6f), Color.Black.copy(alpha = 0.85f)))),
        )
        Column(Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(eyebrow.uppercase(), style = NikeText.Caption, color = NikeColors.White.copy(alpha = 0.8f))
            Text(title.uppercase(), style = NikeText.ScreamHdr, color = NikeColors.White)
            Text(description, style = NikeText.Meta, color = NikeColors.White.copy(alpha = 0.85f))
        }
    }
}
```

### Achievement Medal (hexagonal reveal)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.drawscope.DrawScope

fun DrawScope.hexagonPath(): Path = Path().apply {
    val w = size.width; val h = size.height
    moveTo(w * 0.5f, 0f)
    lineTo(w, h * 0.25f)
    lineTo(w, h * 0.75f)
    lineTo(w * 0.5f, h)
    lineTo(0f, h * 0.75f)
    lineTo(0f, h * 0.25f)
    close()
}

@Composable
fun AchievementMedal(
    milestone: String, // "100"
    label: String,     // "100 MILES"
    subtitle: String,   // "Lifetime distance"
) {
    val scale = remember { Animatable(0.5f) }
    val alpha = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(Unit) {
        haptics.performHapticFeedback(HapticFeedbackType.Confirm) // .success analog
        alpha.animateTo(1f, spring(stiffness = 200f))
        scale.animateTo(1f, spring(dampingRatio = 0.6f, stiffness = 400f))
    }
    Column(
        Modifier.padding(24.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        Box(contentAlignment = Alignment.Center) {
            Canvas(
                Modifier
                    .size(200.dp)
                    .scale(scale.value)
                    .graphicsLayer { this.alpha = alpha.value },
            ) {
                drawPath(
                    hexagonPath(),
                    brush = Brush.linearGradient(listOf(NikeColors.Volt, NikeColors.Red)),
                )
            }
            Text(milestone, style = NikeText.Achievement, color = NikeColors.Canvas, modifier = Modifier.scale(scale.value))
        }
        Text(label.uppercase(), style = NikeText.SubHdr, color = NikeColors.White)
        Text(subtitle, style = NikeText.Body, color = NikeColors.Gray1)
    }
}
```

### Heart Rate Zone Bar

```kotlin
@Composable
fun HRZoneBar(times: List<Float>, modifier: Modifier = Modifier) {
    val zones = listOf(
        NikeColors.HR1Blue to "Z1 Easy",
        NikeColors.HR2Green to "Z2 Steady",
        NikeColors.HR3Yellow to "Z3 Mod",
        NikeColors.HR4Orange to "Z4 Hard",
        NikeColors.HR5Red to "Z5 Max",
    )
    val total = times.sum().coerceAtLeast(1f)
    Column(modifier, verticalArrangement = Arrangement.spacedBy(12.dp)) {
        Row(
            Modifier.fillMaxWidth().height(8.dp).clip(RoundedCornerShape(4.dp)),
        ) {
            times.forEachIndexed { i, t ->
                Box(Modifier.fillMaxHeight().weight((t / total).coerceAtLeast(0.001f)).background(zones[i].first))
            }
        }
        Row(horizontalArrangement = Arrangement.spacedBy(16.dp)) {
            zones.forEach { (c, label) ->
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    Box(Modifier.size(6.dp).clip(androidx.compose.foundation.shape.CircleShape).background(c))
                    Text(label.uppercase(), style = NikeText.Caption, color = NikeColors.White)
                }
            }
        }
    }
}
```

## 4. The Volt Progress Ring (signature interaction)

The 280.dp Volt ring inside the Run Tracking screen is NRC's defining moment — the closest a phone gets to feeling like a piece of equipment. Implemented above in §3 via `Canvas` + `drawArc`: a `rgba(255,255,255,0.1)` full-circle track, a Volt sweep from `-90°` (12 o'clock) clockwise, both with `StrokeCap.Round` (never sharp caps). The 88.sp Trade Gothic elapsed time sits dead-center, cropped tight by the `84.sp` line height so it kisses the ring's inner edge — that tight crop is intentional Nike.

Drive `progress` from the run goal and let the ring tick discretely per mile/km/interval:

```kotlin
// In the run ViewModel — emit progress toward the goal each tick
val progress by produceState(0f, distanceMeters, goalMeters) {
    value = (distanceMeters / goalMeters).coerceIn(0f, 1f)
}
// Pass straight into VoltProgressRing(progress) — the spring(stiffness = 50f) gives the
// smooth linear-feeling fill; for hard 1s ticks per unit swap to tween(1000, easing = LinearEasing).
```

A `lock` affordance (48.dp `Surface2` circle, white lock glyph, top-right inset 16.dp) disables accidental mid-run taps; render it as an overlay aligned `Alignment.TopEnd`.

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. NRC's iOS tab bar is a plain black bar with a 0.5pt `#333333` hairline — Android has no live blur but none is needed here (the bar is opaque `#000000`). 5 tabs: Home / Activity / Coach / Goal / Profile. Active is **white icon + a 4.dp Volt dot** under it; Material's pill indicator is suppressed.

```kotlin
@Composable
fun NikeBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    val haptics = LocalHapticFeedback.current
    NavigationBar(
        containerColor = NikeColors.Canvas,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Activity" to Icons.Filled.BarChart,
            "Coach"    to Icons.Filled.Person,
            "Goal"     to Icons.Filled.TrackChanges,
            "Profile"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            val active = selected == i
            NavigationBarItem(
                selected = active,
                onClick = {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // .selection — never heavy on tabs
                    onSelect(i)
                },
                icon = {
                    Column(horizontalAlignment = Alignment.CenterHorizontally) {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp))
                        Spacer(Modifier.height(4.dp))
                        Box(
                            Modifier
                                .size(4.dp)
                                .clip(androidx.compose.foundation.shape.CircleShape)
                                .background(if (active) NikeColors.Volt else Color.Transparent),
                        )
                    }
                },
                label = { Text(label, style = NikeText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = NikeColors.White,
                    selectedTextColor = NikeColors.White,
                    unselectedIconColor = NikeColors.Gray3,   // #5C5C5C
                    unselectedTextColor = NikeColors.Gray2,   // #8E8E8E
                    indicatorColor = Color.Transparent, // no Material pill — NRC uses a Volt dot
                ),
            )
        }
    }
}
```

## 6. Motion

NRC motion is deterministic and committed — linear ticks on the ring, heavy haptics on commitment, springy medal reveals.

| Moment | Compose recipe |
|--------|----------------|
| Progress ring fill | `animateFloatAsState` `spring(stiffness = 50f)`, or `tween(1000, LinearEasing)` for hard per-unit ticks |
| START RUN tap | scale 1 → 0.98 `spring(0.7f)` + `HapticFeedbackType.LongPress` (heaviest), then 400ms `Crossfade` to Run Tracking |
| Pause tap | circle scale 1 → 0.95 + heavy haptic, morph to Resume (Volt) + Stop (red) over 250ms spring |
| Achievement reveal | `Animatable` scale 0.5 → 1 `spring(response 0.4, damping 0.6)` + alpha fade + Confirm haptic; confetti burst |
| Tab switch | Volt dot slides via `SharedTransitionLayout`/`animateDpAsState`, icon cross-fades 200ms, `TextHandleMove` haptic |
| PR confetti | 16-particle Volt burst + `LongPress` heavy haptic |

```kotlin
// Volt confetti burst on a PR / milestone — 16 particles
@Composable
fun VoltConfetti(trigger: Boolean) {
    val haptics = LocalHapticFeedback.current
    val t = remember { Animatable(0f) }
    LaunchedEffect(trigger) {
        if (trigger) {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // heavy
            t.snapTo(0f); t.animateTo(1f, tween(600))
        }
    }
    Canvas(Modifier.fillMaxSize()) {
        if (t.value == 0f) return@Canvas
        val cx = size.width / 2; val cy = size.height / 2
        repeat(16) { i ->
            val angle = (i / 16f) * 2f * Math.PI
            val r = 320f * t.value
            drawCircle(
                color = NikeColors.Volt.copy(alpha = 1f - t.value),
                radius = 6f,
                center = androidx.compose.ui.geometry.Offset(
                    cx + (r * kotlin.math.cos(angle)).toFloat(),
                    cy + (r * kotlin.math.sin(angle)).toFloat(),
                ),
            )
        }
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For the heaviest "START RUN"/"PAUSE" feel use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.LONG_PRESS)` or a `Vibrator` `VibrationEffect.createOneShot(25, VibrationEffect.DEFAULT_AMPLITUDE)`.

## 7. Icons

NRC ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. The hexagonal medal is NOT an icon — draw it via `Canvas` (§3); the Volt active-tab dot is also drawn, not an icon.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Play / Resume | `play.fill` | `Icons.Filled.PlayArrow` |
| Stop | `stop.fill` | `Icons.Filled.Stop` |
| Lock | `lock.fill` | `Icons.Filled.Lock` |
| GPS strength | `location.fill` | `Icons.Filled.LocationOn` |
| Coach Bennett | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Activity (tab) | `chart.bar` / `.fill` | `Icons.Filled.BarChart` |
| Coach (tab) | `person.crop.square` / `.fill` | `Icons.Filled.Person` |
| Goal (tab) | `target` | `Icons.Filled.TrackChanges` |
| Profile (tab) | `person.circle` / `.fill` | `Icons.Filled.AccountCircle` |
| Heart rate | `heart.fill` | `Icons.Filled.Favorite` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Settings | `gearshape.fill` | `Icons.Filled.Settings` |
| Music | `music.note` | `Icons.Filled.MusicNote` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas`-based ring + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the true-black canvas wants `WindowCompat` light-content system bars. The top app bar / Run Tracking stats sit below the camera cutout; the pause control respects the gesture-nav inset via `Scaffold`/`windowInsetsPadding`.
- **Font scaling**: `sp` honors the user's font scale automatically — scale body, metadata, and plan-card titles. Pin the visual identity (88sp `RunHero`, 64sp `StatValue`, 17sp `Button`, 13sp `StatLabel`, 10sp tab labels) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` — NRC's identity depends on these exact sizes.
- **Tabular digits**: keep `fontFeatureSettings = "tnum"` on every stat (pace, distance, elapsed, BPM, splits) so columns align on the results page.
- **TalkBack**: make the Run Tracking screen read each stat as a separate node so a user can spot-check one at a time — e.g. "Run in progress, 12 minutes 34 seconds elapsed", "1.4 miles", "8 minute 42 second per mile pace", "heart rate 152 BPM", then "Pause button". Use `Modifier.semantics { contentDescription = ... }` per stat.
- **Touch targets**: Material guidance is 48.dp minimum. The 72.dp pause circle and 56.dp CTA clear it; give the 48.dp lock circle its full hit area.
- **Contrast**: Volt `#CCFF00` on `#000000` passes WCAG AAA at all sizes; Volt on white FAILS — use `VoltDim` `#8AA800` on any light surface. `#8E8E8E` tab labels on black pass AA at 10sp+; validate the 11sp `Caption`.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, skip the medal scale spring and the confetti burst — fade the medal in over 300ms with no scale change; keep the ring fill (it conveys state, not decoration).
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — NRC's brand requires the fixed `#000000` canvas and the radioactive `#CCFF00` Volt regardless of wallpaper. Keep the app dark even when the system is light (light mode is an accessibility-only fallback using `VoltDim`).
