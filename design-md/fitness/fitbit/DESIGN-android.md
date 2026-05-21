# Fitbit (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Fitbit's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (rings, tiles, scores), navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (teal-black canvas, the per-metric color system, big-number-as-hero cards, the progress-ring shape language, warm coaching copy) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas`/`drawArc` rings, `dp`/`sp` instead of `pt`. (Fitbit itself is a Google product, so an Android target is first-class.)

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+`. No Material You dynamic color — the per-metric hues must be exact regardless of wallpaper. Dark-friendly; a light scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/FitbitColors.kt
import androidx.compose.ui.graphics.Color

object FitbitColors {
    // Canvas & Surfaces (Dark — default)
    val Canvas    = Color(0xFF001017)
    val Surface1  = Color(0xFF002A3A)
    val Surface2  = Color(0xFF00384D)
    val Surface3  = Color(0xFF0A475E) // ring track
    val Divider   = Color(0xFF143E50)

    // Canvas & Surfaces (Light — secondary)
    val CanvasLight   = Color(0xFFFFFFFF)
    val Surface1Light = Color(0xFFF2F7F8)
    val Surface3Light = Color(0xFFE3EEF0)
    val DividerLight  = Color(0xFFDCE7E9)

    // Brand
    val Teal        = Color(0xFF00B0B9)
    val TealBright  = Color(0xFF21D9CE) // on-dark text/link/active tab
    val TealPressed = Color(0xFF008A91)
    val TealTint    = Color(0x2600B0B9) // 15%

    // Per-metric hues (fixed across themes)
    val Steps     = Color(0xFF00B0B9)
    val HeartRate = Color(0xFFFF6B81)
    val Sleep     = Color(0xFF7C5CFF)
    val Readiness = Color(0xFFB8E986)
    val Zone      = Color(0xFFFF8A3D)
    val Calories  = Color(0xFFFFC233)
    val Spo2      = Color(0xFF4FC3F7)

    // Text
    val TextPrimary    = Color(0xFFEAF6F9)
    val TextSecondary  = Color(0xFF8AAEB8)
    val TextTertiary   = Color(0xFF5C808B)
    val TextPrimaryLt  = Color(0xFF0B2A33)

    // Semantic
    val Success = Color(0xFF3FC7A6)
    val Warning = Color(0xFFFF8A3D)
    val Alert   = Color(0xFFFF6B81)
}
```

Wire into both schemes. Dark-friendly with a teal cast; light only restyles canvas + text.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val FitbitDark = darkColorScheme(
    primary        = FitbitColors.Teal,
    onPrimary      = FitbitColors.Canvas,       // deep ink on teal
    background     = FitbitColors.Canvas,
    onBackground   = FitbitColors.TextPrimary,
    surface        = FitbitColors.Surface1,
    onSurface      = FitbitColors.TextPrimary,
    surfaceVariant = FitbitColors.Surface3,
    outline        = FitbitColors.Divider,
    error          = FitbitColors.Alert,
)

private val FitbitLight = lightColorScheme(
    primary        = FitbitColors.Teal,
    onPrimary      = FitbitColors.Canvas,
    background     = FitbitColors.CanvasLight,
    onBackground   = FitbitColors.TextPrimaryLt,
    surface        = FitbitColors.Surface1Light,
    onSurface      = FitbitColors.TextPrimaryLt,
    surfaceVariant = FitbitColors.Surface3Light,
    outline        = FitbitColors.DividerLight,
    error          = FitbitColors.Alert,
)

@Composable
fun FitbitTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) FitbitDark else FitbitLight,
    typography  = FitbitTypography,
    content     = content,
)
```

## 2. Typography

Fitbit's brand sans → use **DM Sans** (SIL OFL) in `res/font/`. The number is always the hero.

```kotlin
// ui/theme/FitbitType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val DMSans = FontFamily(
    Font(R.font.dmsans_regular,  FontWeight.Normal),
    Font(R.font.dmsans_medium,   FontWeight.Medium),
    Font(R.font.dmsans_semibold, FontWeight.SemiBold),
    Font(R.font.dmsans_bold,     FontWeight.Bold),
)

object FitbitText {
    val MetricHero  = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 44.sp, lineHeight = 46.sp, letterSpacing = (-1.0).sp)
    val ScreenTitle = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.5).sp)
    val TileValue   = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 26.sp, lineHeight = 28.sp, letterSpacing = (-0.5).sp)
    val Greeting    = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 27.sp, letterSpacing = (-0.3).sp)
    val CardTitle   = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 18.sp, lineHeight = 23.sp)
    val Body        = TextStyle(DMSans, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val ValueInline = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Label       = TextStyle(DMSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 19.sp)
    val Unit        = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp)
    val Caption     = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 15.sp)
}

val FitbitTypography = Typography(
    headlineLarge  = FitbitText.ScreenTitle,
    headlineMedium = FitbitText.Greeting,
    titleMedium    = FitbitText.CardTitle,
    bodyMedium     = FitbitText.Body,
    labelSmall     = FitbitText.Tab,
)
```

## 3. Signature Components

### Progress Ring (the signature shape)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.size
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp

@Composable
fun MetricRing(
    progress: Float,            // 0f..1f (can exceed)
    color: Color,
    size: Dp = 116.dp,
    stroke: Dp = 11.dp,
) {
    val anim = remember { Animatable(0f) }
    LaunchedEffect(progress) {
        anim.animateTo(progress.coerceAtMost(1f), tween(900))
    }
    Canvas(Modifier.size(size)) {
        val sw = stroke.toPx()
        val arcSize = Size(this.size.width - sw, this.size.height - sw)
        val topLeft = androidx.compose.ui.geometry.Offset(sw / 2, sw / 2)
        drawArc(FitbitColors.Surface3, 0f, 360f, false, topLeft, arcSize, style = Stroke(sw))
        drawArc(
            color, -90f, 360f * anim.value, false, topLeft, arcSize,
            style = Stroke(sw, cap = StrokeCap.Round),
        )
    }
}
```

### Steps Ring Hero

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.clip

@Composable
fun StepsHero(steps: Int, goal: Int, headline: String, support: String, trend: String) {
    Row(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 20.dp)
            .clip(RoundedCornerShape(24.dp))
            .background(FitbitColors.Surface1)
            .padding(22.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(18.dp),
    ) {
        Box(contentAlignment = Alignment.Center) {
            MetricRing(progress = steps.toFloat() / goal, color = FitbitColors.Steps)
            Column(horizontalAlignment = Alignment.CenterHorizontally) {
                Text("%,d".format(steps), style = FitbitText.TileValue, color = FitbitColors.TextPrimary)
                Text("of %,d steps".format(goal), style = FitbitText.Caption.copy(fontWeight = FontWeight.Normal, fontSize = 11.sp),
                    color = FitbitColors.TextSecondary)
            }
        }
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(3.dp)) {
            Text(headline, style = FitbitText.ValueInline, color = FitbitColors.TextPrimary)
            Text(support, style = FitbitText.Caption.copy(fontWeight = FontWeight.Normal, fontSize = 13.sp),
                color = FitbitColors.TextSecondary)
            Box(
                Modifier
                    .padding(top = 6.dp)
                    .clip(RoundedCornerShape(500.dp))
                    .background(FitbitColors.TealTint)
                    .padding(horizontal = 10.dp, vertical = 5.dp),
            ) {
                Text(trend, style = FitbitText.Caption, color = FitbitColors.TealBright)
            }
        }
    }
}
```

### Metric Tile

```kotlin
import androidx.compose.material3.Icon
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun MetricTile(
    metricColor: Color, icon: ImageVector, label: String,
    value: String, unit: String?, sub: String,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .clip(RoundedCornerShape(20.dp))
            .background(FitbitColors.Surface1)
            .padding(16.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(7.dp)) {
            Box(
                Modifier.size(24.dp).clip(RoundedCornerShape(7.dp)).background(metricColor),
                contentAlignment = Alignment.Center,
            ) { Icon(icon, label, tint = FitbitColors.Canvas, modifier = Modifier.size(13.dp)) }
            Text(label, style = FitbitText.Caption, color = FitbitColors.TextSecondary)
        }
        Row(
            Modifier.padding(top = 10.dp),
            verticalAlignment = Alignment.Bottom,
            horizontalArrangement = Arrangement.spacedBy(3.dp),
        ) {
            Text(value, style = FitbitText.TileValue, color = FitbitColors.TextPrimary)
            if (unit != null) Text(unit, style = FitbitText.Unit, color = FitbitColors.TextSecondary,
                modifier = Modifier.padding(bottom = 3.dp))
        }
        Text(sub, style = FitbitText.Caption.copy(fontWeight = FontWeight.Normal),
            color = FitbitColors.TextTertiary, modifier = Modifier.padding(top = 3.dp))
    }
}
```

### Score Badge + Daily Readiness Card

```kotlin
import androidx.compose.ui.graphics.Brush

@Composable
fun ScoreBadge(score: Int, color: Color, size: Dp = 60.dp) {
    Box(Modifier.size(size), contentAlignment = Alignment.Center) {
        Canvas(Modifier.size(size)) {
            val sw = 4.dp.toPx()
            drawArc(color, 0f, 360f, false,
                style = Stroke(sw),
                topLeft = androidx.compose.ui.geometry.Offset(sw / 2, sw / 2),
                size = Size(this.size.width - sw, this.size.height - sw))
        }
        Text("$score", style = FitbitText.TileValue.copy(fontSize = (size.value * 0.36).sp), color = color)
    }
}

@Composable
fun ReadinessCard(score: Int, message: String) {
    Row(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 20.dp)
            .clip(RoundedCornerShape(20.dp))
            .background(Brush.linearGradient(listOf(Color(0xFF0E3A2E), Color(0xFF14564A))))
            .padding(18.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        ScoreBadge(score, FitbitColors.Readiness, 60.dp)
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(3.dp)) {
            Text("Daily Readiness", style = FitbitText.ValueInline, color = FitbitColors.TextPrimary)
            Text(message, style = FitbitText.Caption.copy(fontWeight = FontWeight.Normal),
                color = Color(0xFFA9D9C8))
        }
    }
}
```

### Primary Pill Button

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState

@Composable
fun FitbitPrimaryButton(title: String, onClick: () -> Unit) {
    val src = remember { MutableInteractionSource() }
    val pressed by src.collectIsPressedAsState()
    Box(
        Modifier
            .fillMaxWidth().height(52.dp)
            .clip(RoundedCornerShape(500.dp))
            .background(if (pressed) FitbitColors.TealPressed else FitbitColors.Teal)
            .clickable(interactionSource = src, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = FitbitText.Button, color = FitbitColors.Canvas) // deep ink
    }
}
```

## 4. Navigation (Bottom Bar)

4-tab `NavigationBar`, active in bright teal, **no Material tint pill** (Fitbit has none).

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material.icons.outlined.*
import androidx.compose.material3.*

@Composable
fun FitbitBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = FitbitColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            Triple("Today", Icons.Filled.RadioButtonChecked, Icons.Outlined.RadioButtonUnchecked),
            Triple("Coach", Icons.Filled.DirectionsRun, Icons.Outlined.DirectionsRun),
            Triple("Community", Icons.Filled.Groups, Icons.Outlined.Groups),
            Triple("You", Icons.Filled.AccountCircle, Icons.Outlined.AccountCircle),
        )
        items.forEachIndexed { i, (label, on, off) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(if (selected == i) on else off, label, Modifier.size(22.dp)) },
                label = { Text(label, style = FitbitText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = FitbitColors.TealBright,
                    selectedTextColor   = FitbitColors.TealBright,
                    unselectedIconColor = FitbitColors.TextTertiary,
                    unselectedTextColor = FitbitColors.TextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill
                ),
            )
        }
    }
}
```

Today is a `LazyColumn` of cards (steps hero, metric tile grid via `FlowRow`/2-col `Row`s, sleep score, readiness). Metric detail screens use a `Day/Week/Month` `SegmentedButton`-style pill over a metric-colored chart `Canvas`.

## 5. Motion

Fitbit motion is warm and rewarding — the ring "draw" and number count-up are core; celebrations are joyful but brief.

| Moment | Compose recipe |
|--------|----------------|
| Ring fill sweep | `Animatable.animateTo(progress, tween(900, easing = FastOutSlowIn))` (see `MetricRing`) |
| Number count-up | `animateIntAsState(target, tween(700, easing = LinearOutSlowIn))` → display the animated value |
| Today card stagger-in | `LazyColumn` items with `Modifier.animateItemPlacement()`; each card `AnimatedVisibility` fade + 8dp slide with `tween(350, delayMillis = index * 40)` |
| Tile tap | `Modifier.scale` via `Animatable` 1 → 0.97 → 1 (`tween(120)`) |
| Goal celebration | ring `pulse` (`scale` spring) + confetti overlay + success haptic |
| Range switch | chart `Crossfade(range, tween(400))` |
| Sleep stage bar | per-segment width `animateFloatAsState` staggered |

```kotlin
val animated by animateIntAsState(targetValue = steps, animationSpec = tween(700), label = "stepCount")
Text("%,d".format(animated), style = FitbitText.TileValue, color = FitbitColors.TextPrimary)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` (or a custom success `VibrationEffect`) on goal hit / milestone; `HapticFeedbackType.TextHandleMove` for range/segment changes; a light tick on tile tap.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. Per-metric glyphs render in `Canvas` (deep ink) on their colored chip.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Today (tab) | `circle.dashed` | `Icons.Filled.RadioButtonChecked` |
| Coach (tab) | `figure.run` | `Icons.Filled.DirectionsRun` |
| Community (tab) | `person.2.fill` | `Icons.Filled.Groups` |
| You (tab) | `person.crop.square` | `Icons.Filled.AccountCircle` |
| Steps | `figure.walk` | `Icons.Filled.DirectionsWalk` |
| Heart Rate | `heart.fill` | `Icons.Filled.Favorite` |
| Sleep | `moon.fill` | `Icons.Filled.Bedtime` |
| Active Zone | `bolt.fill` | `Icons.Filled.Bolt` |
| Calories | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Readiness | `checkmark.seal.fill` | `Icons.Filled.Verified` |
| SpO2 | `lungs.fill` | `Icons.Filled.MonitorHeart` |
| Distance | `location.fill` | `Icons.Filled.Place` |
| Trend up | `arrow.up.right` | `Icons.Filled.TrendingUp` |
| Trend down | `arrow.down.right` | `Icons.Filled.TrendingDown` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Add / Log | `plus` | `Icons.Filled.Add` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Notifications | `bell` | `Icons.Filled.Notifications` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas` rings + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the teal-dark canvas wants light-content system bars (dark-content in light theme). The greeting row clears the camera cutout; the tab bar uses `Modifier.navigationBarsPadding()`.
- **Font scaling**: `sp` honors the user scale — keep it on greeting, card titles, body, labels. Pin unit suffixes, 10sp tab labels, and trend-chip text via `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`; let hero numbers `AutoSize` (Compose `TextAutoSize`) or `maxLines = 1` + `softWrap = false`.
- **Per-metric color is a mnemonic, not the only signal**: always render the metric icon + text label with the hue. A colorblind user must tell sleep (`#7C5CFF`) from heart rate (`#FF6B81`) by icon/label, never color alone.
- **Health data**: read via Health Connect (`androidx.health.connect`); request only needed permissions; never log raw biometric values; gate sensitive detail screens behind `BiometricPrompt` where appropriate.
- **TalkBack**: rings expose `Modifier.semantics { contentDescription = "Steps, 7,842 of 10,000, 78 percent" }`; tiles announce "Heart Rate, 68 beats per minute, resting"; the readiness card reads score + full recommendation. Mark celebratory confetti as decorative (`Modifier.clearAndSetSemantics {}`).
- **Touch targets**: Material guidance is 48.dp. Metric tiles are full-tile tappable (≥120dp); tab icons 22dp glyph, 48dp hit; pill buttons ≥48dp; stepper buttons 36dp visual / 48dp hit.
- **Contrast**: `#EAF6F9` on `#001017` passes WCAG AA; deep ink `#001017` on `#00B0B9` passes AA for the pill. Verify each metric chip's deep-ink glyph passes AA on its hue — lime `#B8E986` and gold `#FFC233` require the dark glyph (never white); coral/purple/orange also use the dark glyph for consistency.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, set the ring to its final value instantly, show the number directly (no count-up), and skip confetti; keep the goal-hit haptic.
- **Dark mode**: dark-friendly in practice — default to `FitbitDark`. Light only swaps canvas (`#FFFFFF`) and primary text (`#0B2A33`); **every per-metric hue stays identical**. On dark, brand teal text/links/active tab use the brighter `#21D9CE`. Do **not** enable Material You `dynamicColorScheme()` — the per-metric color system must be exact regardless of wallpaper. Floating sheets get a 1dp `Divider` border since shadows are soft on `#001017`.
