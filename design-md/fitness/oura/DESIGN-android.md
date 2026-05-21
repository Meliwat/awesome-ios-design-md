# Oura (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Oura's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Oura's cool-charcoal instrument panel, the score ring, the tri-domain color language, contributor bars) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas`/`drawArc` instead of a `CAShapeLayer`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and an Inter `FontFamily` in `res/font/`.

## 1. Color Tokens

```kotlin
// ui/theme/OuraColors.kt
import androidx.compose.ui.graphics.Color

object OuraColors {
    // Canvas & Surfaces
    val Canvas   = Color(0xFF0B0B0F)
    val Surface1 = Color(0xFF16161C)
    val Surface2 = Color(0xFF1E1E26)
    val Surface3 = Color(0xFF27272F)
    val Divider  = Color(0xFF2A2A33)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF9A9AA5)
    val TextTertiary  = Color(0xFF6A6A73)

    // Domain accents (color = meaning)
    val Readiness     = Color(0xFF4FD1C5)
    val ReadinessDeep = Color(0xFF37B3A8)
    val Sleep         = Color(0xFF7C6FF0)
    val SleepDeep     = Color(0xFF6354D6)
    val Activity      = Color(0xFFF5A623)
    val ActivityDeep  = Color(0xFFD98A12)

    // Semantic
    val NegativeDelta = Color(0xFFE0746B)
}

enum class OuraDomain(val hue: Color, val deep: Color, val label: String) {
    Readiness(OuraColors.Readiness, OuraColors.ReadinessDeep, "READINESS"),
    Sleep(OuraColors.Sleep, OuraColors.SleepDeep, "SLEEP"),
    Activity(OuraColors.Activity, OuraColors.ActivityDeep, "ACTIVITY"),
}
```

Wire it into a Material 3 `darkColorScheme`. Oura is dark-first; do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val OuraScheme = darkColorScheme(
    primary        = OuraColors.Readiness,   // default domain accent
    onPrimary      = OuraColors.Canvas,      // dark text on hue fills
    background     = OuraColors.Canvas,
    onBackground   = OuraColors.TextPrimary,
    surface        = OuraColors.Surface1,
    onSurface      = OuraColors.TextPrimary,
    surfaceVariant = OuraColors.Surface2,
    outline        = OuraColors.Divider,
    error          = OuraColors.NegativeDelta,
)

@Composable
fun OuraTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = OuraScheme, typography = OuraTypography, content = content)
```

## 2. Typography

Inter is open-source (SIL OFL). Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Enable tabular figures so scores never reflow.

```kotlin
// ui/theme/OuraType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

private const val TNUM = "tnum" // tabular figures

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object OuraText {
    val RingScore   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 52.sp, letterSpacing = (-1.0).sp, fontFeatureSettings = TNUM)
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 26.sp, letterSpacing = (-0.4).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 20.sp, letterSpacing = (-0.2).sp)
    val Score2      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 34.sp, letterSpacing = (-0.6).sp, fontFeatureSettings = TNUM)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, letterSpacing = (-0.1).sp)
    val MetricVal   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 22.sp, letterSpacing = (-0.2).sp, fontFeatureSettings = TNUM)
    val Contributor = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 23.sp)
    val Subtitle    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp)
    val Delta       = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, fontFeatureSettings = TNUM)
    val DomainLabel = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 13.sp, letterSpacing = 0.8.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 16.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, letterSpacing = 0.2.sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 11.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val OuraTypography = Typography(
    headlineLarge = OuraText.ScreenTitle,
    headlineSmall = OuraText.Section,
    titleMedium   = OuraText.CardTitle,
    bodyLarge     = OuraText.Body,
    labelSmall    = OuraText.Tab,
)
```

## 3. Signature Components

### Score Ring (signature)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.dp

@Composable
fun OuraScoreRing(
    score: Int,
    domain: OuraDomain,
    modifier: Modifier = Modifier,
    diameter: Int = 220,
    strokeWidth: Int = 12,
) {
    val progress = remember { Animatable(0f) }
    val shown by animateIntAsState(
        targetValue = (progress.value * 100).toInt().coerceAtMost(score),
        label = "count",
    )
    LaunchedEffect(score) {
        progress.animateTo(
            score / 100f,
            tween(900, easing = EaseOutCubic),
        )
    }

    Box(
        modifier.size(diameter.dp),
        contentAlignment = Alignment.Center,
    ) {
        Canvas(Modifier.fillMaxSize()) {
            val sw = strokeWidth.dp.toPx()
            val inset = sw / 2
            val arcSize = Size(size.width - sw, size.height - sw)
            drawArc(
                color = OuraColors.Surface3,
                startAngle = 0f, sweepAngle = 360f, useCenter = false,
                topLeft = androidx.compose.ui.geometry.Offset(inset, inset),
                size = arcSize, style = Stroke(sw),
            )
            drawArc(
                color = domain.hue,
                startAngle = -90f, sweepAngle = 360f * progress.value, useCenter = false,
                topLeft = androidx.compose.ui.geometry.Offset(inset, inset),
                size = arcSize, style = Stroke(sw, cap = StrokeCap.Round),
            )
        }
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Text("$shown", style = OuraText.RingScore, color = OuraColors.TextPrimary)
            Text(domain.label, style = OuraText.DomainLabel, color = domain.hue)
        }
    }
}
```

### Contributor Bar List (signature)

```kotlin
data class Contributor(val name: String, val normalized: Float, val value: String)

@Composable
fun ContributorBarList(
    contributors: List<Contributor>,
    domain: OuraDomain,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .clip(RoundedCornerShape(16.dp))
            .background(OuraColors.Surface1),
    ) {
        contributors.forEachIndexed { i, c ->
            val w = remember { Animatable(0f) }
            LaunchedEffect(c) {
                delay(i * 40L)
                w.animateTo(c.normalized, tween(500, easing = EaseOutCubic))
            }
            Row(
                Modifier.fillMaxWidth().height(44.dp).padding(horizontal = 16.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(12.dp),
            ) {
                Text(c.name, style = OuraText.Contributor, color = OuraColors.TextPrimary,
                    maxLines = 1, modifier = Modifier.width(130.dp))
                Box(
                    Modifier.weight(1f).height(6.dp)
                        .clip(RoundedCornerShape(3.dp)).background(OuraColors.Surface3),
                ) {
                    Box(
                        Modifier.fillMaxWidth(w.value).height(6.dp)
                            .clip(RoundedCornerShape(3.dp)).background(domain.hue),
                    )
                }
                Text(c.value, style = OuraText.Delta, color = OuraColors.TextPrimary,
                    modifier = Modifier.width(44.dp), textAlign = TextAlign.End)
            }
            if (i < contributors.lastIndex) {
                HorizontalDivider(color = OuraColors.Divider, thickness = 1.dp,
                    modifier = Modifier.padding(start = 16.dp))
            }
        }
    }
}
```

### Metric Tile

```kotlin
@Composable
fun OuraMetricTile(
    label: String, value: String, unit: String, delta: String, domain: OuraDomain,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .clip(RoundedCornerShape(16.dp))
            .background(OuraColors.Surface1)
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Text(label.uppercase(), style = OuraText.DomainLabel, color = OuraColors.TextSecondary)
        Row(verticalAlignment = Alignment.Bottom,
            horizontalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(value, style = OuraText.MetricVal, color = OuraColors.TextPrimary)
            Text(unit, style = OuraText.Subtitle, color = OuraColors.TextSecondary,
                modifier = Modifier.padding(bottom = 3.dp))
        }
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.CenterVertically) {
            Box(Modifier.width(40.dp).height(3.dp)
                .clip(RoundedCornerShape(2.dp)).background(domain.hue))
            Text(delta, style = OuraText.Delta, color = domain.hue)
        }
    }
}
```

### Insight Card

```kotlin
@Composable
fun OuraInsightCard(
    icon: androidx.compose.ui.graphics.vector.ImageVector,
    title: String, body: String, domain: OuraDomain, modifier: Modifier = Modifier,
) {
    Row(
        modifier
            .clip(RoundedCornerShape(16.dp))
            .background(OuraColors.Surface1)
            .padding(18.dp),
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Icon(icon, null, tint = domain.hue, modifier = Modifier.size(24.dp))
        Column(verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(title, style = OuraText.CardTitle, color = OuraColors.TextPrimary)
            Text(body, style = OuraText.Body, color = OuraColors.TextSecondary)
        }
    }
}
```

### Primary Button

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun OuraPrimaryButton(
    title: String, domain: OuraDomain, onClick: () -> Unit, modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f,
        tween(200, easing = EaseOut), label = "btnScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier
            .fillMaxWidth()
            .scale(scale)
            .clip(RoundedCornerShape(24.dp))
            .background(if (pressed) domain.deep else domain.hue)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS soft
                onClick()
            }
            .padding(horizontal = 28.dp, vertical = 14.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = OuraText.Button, color = OuraColors.Canvas)
    }
}
```

## 4. Bottom Navigation (Tab Bar)

Material 3 `NavigationBar`. Oura's iOS tab bar is `.regularMaterial` glass; Android has no first-class live blur, so use an 82%-opaque canvas surface. **Active tint is neutral white** — domain color never touches chrome.

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun OuraBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = OuraColors.Canvas.copy(alpha = 0.82f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Today"     to Icons.Filled.Circle,
            "Vitals"    to Icons.Filled.MonitorHeart,
            "My Health" to Icons.Filled.Favorite,
            "Explore"   to Icons.Filled.GridView,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = OuraText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = OuraColors.TextPrimary,   // neutral white
                    selectedTextColor   = OuraColors.TextPrimary,
                    unselectedIconColor = OuraColors.TextTertiary,
                    unselectedTextColor = OuraColors.TextTertiary,
                    indicatorColor      = Color.Transparent, // Oura has no Material pill
                ),
            )
        }
    }
}
```

## 5. Motion

| Moment | Compose recipe |
|--------|----------------|
| Score reveal | `Animatable` 0 → score/100 `tween(900, EaseOutCubic)`; `animateIntAsState` counts the center figure in lockstep |
| Contributor bars | per-row `Animatable` 0 → normalized, `delay(i * 40L)`, `tween(500, EaseOutCubic)` |
| Range switch (7D/30D/90D) | `AnimatedContent`/`animate*AsState` morph dataset over `tween(350, EaseInOut)` |
| Card tap | `animateFloatAsState` 1 → 0.98 `tween(200, EaseOut)`, `HapticFeedbackType.LongPress` |
| Tab change | `Crossfade(targetState = tab, animationSpec = tween(250))` |
| Sync pull-to-refresh | a `drawArc` filling like a ring, then `HapticFeedbackType.LongPress` on success |

```kotlin
@Composable
fun TrendArea(values: List<Float>, domain: OuraDomain) {
    Canvas(Modifier.fillMaxWidth().height(140.dp)) {
        // line in domain.hue; fill domain.hue.copy(alpha = 0.16f); optimal band behind
        // gridlines OuraColors.Divider at 0.5.dp; axis text drawn via drawIntoCanvas
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For a softer pulse than the default click use `Vibrator` `VibrationEffect.createOneShot(8, 60)` to approximate iOS's `.soft` impact.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Readiness | `bolt.heart` | `Icons.Filled.Bolt` |
| Sleep | `moon.zzz` | `Icons.Filled.Bedtime` |
| Activity | `flame` | `Icons.Filled.LocalFireDepartment` |
| HRV insight | `waveform.path.ecg` | `Icons.Filled.MonitorHeart` |
| Heart rate | `heart` | `Icons.Filled.Favorite` |
| Temperature | `thermometer.medium` | `Icons.Filled.DeviceThermostat` |
| Add tag | `plus.circle` | `Icons.Outlined.AddCircleOutline` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Info | `info.circle` | `Icons.Outlined.Info` |
| Date chevron | `chevron.down` | `Icons.Filled.ExpandMore` |
| Ring battery | `circle.bottomhalf.filled` | `Icons.Filled.BatteryStd` |
| Today (tab) | `circle.circle.fill` | `Icons.Filled.Circle` |
| Vitals (tab) | `waveform.path.ecg` | `Icons.Filled.MonitorHeart` |
| My Health (tab) | `heart.text.square` | `Icons.Filled.Favorite` |
| Explore (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas`/`drawArc` and modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the cool-charcoal canvas wants light-content system bars. Apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the tab bar clears the gesture nav and the hero ring centers in the safe area.
- **Tabular figures**: set `fontFeatureSettings = "tnum"` on score/metric/delta styles (already in `OuraText`) so the count-up animation and contributor columns never jitter; confirm the bundled Inter ships `tnum`.
- **Font scaling**: `sp` honors the user's font scale — keep it on titles, contributors, body. Pin the ring score (must fit the circle), domain labels, deltas, axis captions, and tab labels by wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Color is information**: always pair the domain hue with its uppercase text label + icon — never hue alone. This is both brand-correct and color-blind-safe.
- **TalkBack**: give the score ring a combined `contentDescription` ("Readiness 82 out of 100"); expose contributor rows with state words ("Resting Heart Rate, good"); describe trend graphs via a semantics summary.
- **Reduce Motion**: gate the ring sweep + count-up and the staggered bars on `Settings.Global.ANIMATOR_DURATION_SCALE` — when off, render final values immediately.
- **Touch targets**: Material guidance is 48.dp minimum — the ring is a large single target; give 20.dp action glyphs a `Modifier.size(48.dp)` hit area via padding; contributor rows are 44.dp (acceptable as full-width list items).
- **Contrast**: `#9A9AA5` on `#0B0B0F` passes AA at 13sp+; validate the 11sp axis captions and bump toward `#AEAEB8` if the build targets accessibility compliance.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Oura's identity requires the fixed cool-charcoal canvas and the exact tri-domain accents regardless of wallpaper.
