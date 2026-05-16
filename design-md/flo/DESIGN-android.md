# Flo (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Flo's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Flo's soft white-and-blush space, the cycle wheel, coral/lavender phase language, gently rounded cards) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas`/`drawArc` instead of a `CAShapeLayer`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and an Inter `FontFamily` in `res/font/`.

## 1. Color Tokens

```kotlin
// ui/theme/FloColors.kt
import androidx.compose.ui.graphics.Color

object FloColors {
    // Canvas & Surfaces
    val Canvas        = Color(0xFFFFFFFF)
    val Blush         = Color(0xFFFFF0F3)
    val SurfaceSunken = Color(0xFFFBE9ED)
    val Divider       = Color(0xFFF3DDE3)

    // Text
    val TextPrimary   = Color(0xFF1A1A2E)
    val TextSecondary = Color(0xFF6E6A82)
    val TextTertiary  = Color(0xFFA09CB0)

    // Coral (lead accent)
    val Coral        = Color(0xFFFF6B81)
    val CoralPressed = Color(0xFFE85870)
    val CoralSoft    = Color(0xFFFFD9DF)

    // Lavender (phase partner)
    val Lavender     = Color(0xFFC5B3E6)
    val LavenderDeep = Color(0xFFA893D6)
    val LavenderSoft = Color(0xFFEDE6F7)

    // Semantic (soft)
    val GentlePositive = Color(0xFF7DC9A8)
    val GentleAlert    = Color(0xFFF2B36B)
}
```

Wire it into a Material 3 `lightColorScheme` — Flo is light-first. A dark scheme exists only for OS parity.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val FloScheme = lightColorScheme(
    primary        = FloColors.Coral,
    onPrimary      = Color.White,
    secondary      = FloColors.Lavender,
    background      = FloColors.Canvas,
    onBackground   = FloColors.TextPrimary,
    surface        = FloColors.Blush,
    onSurface      = FloColors.TextPrimary,
    surfaceVariant = FloColors.CoralSoft,
    outline        = FloColors.Divider,
    error          = FloColors.GentleAlert, // soft amber, never harsh red
)

@Composable
fun FloTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = FloScheme, typography = FloTypography, content = content)
```

## 2. Typography

Inter is open-source (SIL OFL). Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Enable tabular figures on the wheel center and calendar.

```kotlin
// ui/theme/FloType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_medium,   FontWeight.Medium),   // 500
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

private const val TNUM = "tnum" // tabular figures

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object FloText {
    val WheelCenter = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 30.sp, letterSpacing = (-0.4).sp, fontFeatureSettings = TNUM)
    val Greeting    = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 28.sp, letterSpacing = (-0.3).sp)
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 24.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 22.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, letterSpacing = (-0.1).sp)
    val Prediction  = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 20.sp, letterSpacing = (-0.2).sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 25.sp)
    val ChipLabel   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp)
    val Subtitle    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, letterSpacing = 0.1.sp)
    val CalDay      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, fontFeatureSettings = TNUM)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 13.sp, letterSpacing = 0.1.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, letterSpacing = 0.2.sp)
    val LabelUpper  = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 12.sp, letterSpacing = 0.8.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val FloTypography = Typography(
    headlineLarge = FloText.Greeting,
    headlineSmall = FloText.ScreenTitle,
    titleMedium   = FloText.CardTitle,
    bodyLarge     = FloText.Body,
    labelSmall    = FloText.Tab,
)
```

## 3. Signature Components

### Cycle Wheel (signature)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.draw.scale
import androidx.compose.ui.unit.dp

data class WheelSegment(val start: Float, val end: Float, val color: Color) // fractions 0..1

@Composable
fun CycleWheel(
    cycleDay: Int,
    phaseName: String,
    segments: List<WheelSegment>,
    onLog: () -> Unit,
    modifier: Modifier = Modifier,
    diameter: Int = 260,
) {
    val sweep = remember { Animatable(0f) }
    val centerScale = remember { Animatable(0.9f) }
    val centerAlpha = remember { Animatable(0f) }
    LaunchedEffect(Unit) {
        launch { sweep.animateTo(1f, tween(700, easing = EaseOutCubic)) }
        launch { centerScale.animateTo(1f, tween(700, easing = EaseOutCubic)) }
        launch { centerAlpha.animateTo(1f, tween(700)) }
    }

    Column(modifier, horizontalAlignment = Alignment.CenterHorizontally) {
        Box(Modifier.size(diameter.dp), contentAlignment = Alignment.Center) {
            Canvas(Modifier.fillMaxSize()) {
                val sw = 14.dp.toPx()
                val inset = sw / 2
                val arcSize = Size(size.width - sw, size.height - sw)
                drawArc(FloColors.Divider, 0f, 360f, false,
                    topLeft = Offset(inset, inset), size = arcSize, style = Stroke(sw))
                segments.forEach { seg ->
                    drawArc(
                        color = seg.color,
                        startAngle = -90f + seg.start * 360f,
                        sweepAngle = (seg.end - seg.start) * 360f * sweep.value,
                        useCenter = false,
                        topLeft = Offset(inset, inset), size = arcSize,
                        style = Stroke(sw, cap = StrokeCap.Round),
                    )
                }
            }
            Column(
                Modifier.scale(centerScale.value).graphicsLayer { alpha = centerAlpha.value },
                horizontalAlignment = Alignment.CenterHorizontally,
            ) {
                Text("Day $cycleDay", style = FloText.WheelCenter, color = FloColors.TextPrimary)
                Text(phaseName, style = FloText.Subtitle, color = FloColors.TextSecondary)
            }
        }
        Spacer(Modifier.height(16.dp))
        FloPrimaryButton("Log period", onLog)
    }
}
```

### Prediction Card (signature)

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.WaterDrop
import androidx.compose.material3.Icon

@Composable
fun PredictionCard(
    label: String, emphasis: String, sub: String, modifier: Modifier = Modifier,
) {
    Row(
        modifier
            .fillMaxWidth()
            .shadow(20.dp, RoundedCornerShape(20.dp),
                spotColor = FloColors.Coral.copy(alpha = 0.10f))
            .clip(RoundedCornerShape(20.dp))
            .background(FloColors.Blush)
            .padding(20.dp),
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Icon(Icons.Filled.WaterDrop, null, tint = FloColors.Coral,
            modifier = Modifier.size(28.dp))
        Column(verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(label.uppercase(), style = FloText.LabelUpper, color = FloColors.TextSecondary)
            Text(emphasis, style = FloText.Prediction, color = FloColors.Coral)
            Text(sub, style = FloText.Subtitle, color = FloColors.TextSecondary)
        }
    }
}
```

### Symptom Log Chip

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun SymptomChip(
    title: String,
    selected: Boolean,
    onToggle: () -> Unit,
    modifier: Modifier = Modifier,
    icon: androidx.compose.ui.graphics.vector.ImageVector? = null,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.96f else 1f, label = "chipScale")
    val haptics = LocalHapticFeedback.current
    val bg by animateColorAsState(
        if (selected) FloColors.CoralSoft else FloColors.Blush,
        tween(200), label = "chipBg",
    )

    Row(
        modifier
            .scale(scale)
            .clip(RoundedCornerShape(18.dp))
            .background(bg)
            .then(
                if (selected) Modifier.border(BorderStroke(1.5.dp, FloColors.Coral),
                    RoundedCornerShape(18.dp)) else Modifier
            )
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                onToggle()
            }
            .padding(horizontal = 16.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        if (icon != null) Icon(icon, null,
            tint = if (selected) FloColors.CoralPressed else FloColors.TextPrimary,
            modifier = Modifier.size(18.dp))
        Text(title, style = FloText.ChipLabel,
            color = if (selected) FloColors.CoralPressed else FloColors.TextPrimary)
    }
}
```

### Calendar Day Cell

```kotlin
enum class DayState { Ordinary, PredictedPeriod, LoggedPeriod, Fertile, Ovulation }

@Composable
fun CalendarDayCell(day: Int, state: DayState, isToday: Boolean, modifier: Modifier = Modifier) {
    val fill = when (state) {
        DayState.LoggedPeriod -> FloColors.CoralPressed
        DayState.Fertile      -> FloColors.LavenderSoft
        else                  -> Color.Transparent
    }
    val border = when (state) {
        DayState.PredictedPeriod -> FloColors.Coral
        DayState.Ovulation       -> FloColors.LavenderDeep
        else                     -> Color.Transparent
    }
    val borderW = when (state) {
        DayState.Ovulation       -> 2.dp
        DayState.PredictedPeriod -> 1.5.dp
        else                     -> 0.dp
    }
    val textColor = when (state) {
        DayState.LoggedPeriod    -> Color.White
        DayState.PredictedPeriod -> FloColors.Coral
        else                     -> FloColors.TextPrimary
    }
    Box(modifier.size(40.dp), contentAlignment = Alignment.Center) {
        Box(
            Modifier.size(40.dp).clip(CircleShape).background(fill)
                .then(if (borderW > 0.dp) Modifier.border(borderW, border, CircleShape) else Modifier),
            contentAlignment = Alignment.Center,
        ) {
            Text("$day", style = FloText.CalDay, color = textColor)
        }
        if (isToday) Box(
            Modifier.align(Alignment.BottomCenter).padding(bottom = 2.dp)
                .size(5.dp).clip(CircleShape).background(FloColors.Coral)
        )
    }
}
```

### Primary / Soft Buttons

```kotlin
@Composable
fun FloPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f,
        tween(200, easing = EaseOut), label = "btnScale")
    val haptics = LocalHapticFeedback.current
    Box(
        modifier
            .scale(scale)
            .clip(RoundedCornerShape(26.dp))
            .background(if (pressed) FloColors.CoralPressed else FloColors.Coral)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            }
            .padding(horizontal = 32.dp, vertical = 16.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = FloText.Button, color = Color.White)
    }
}
```

## 4. Bottom Navigation (Tab Bar)

Material 3 `NavigationBar`. Flo's iOS tab bar is a near-opaque white `.regularMaterial`; Android has no first-class live blur, so use a 94%-opaque white surface with a soft pink hairline. **Active tint is Flo Coral.**

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun FloBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = Color.White.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Today"    to Icons.Filled.Circle,
            "Calendar" to Icons.Filled.CalendarMonth,
            "Insights" to Icons.Filled.Lightbulb,
            "Partner"  to Icons.Filled.Favorite,
            "More"     to Icons.Filled.MoreHoriz,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = FloText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = FloColors.Coral,
                    selectedTextColor   = FloColors.Coral,
                    unselectedIconColor = FloColors.TextTertiary,
                    unselectedTextColor = FloColors.TextTertiary,
                    indicatorColor      = FloColors.CoralSoft, // gentle Material pill
                ),
            )
        }
    }
}
```

## 5. Motion

| Moment | Compose recipe |
|--------|----------------|
| Wheel reveal | `Animatable` sweep 0 → 1 `tween(700, EaseOutCubic)`; center scale 0.9 → 1 + alpha 0 → 1 in sync |
| Wheel phase morph | `animateColorAsState` per segment, `tween(300, EaseInOut)` crossfade when the cycle advances |
| Chip select | `animateColorAsState` blush → coral-soft `tween(200)`; `scale` 0.96 on press |
| Card tap | `animateFloatAsState` 1 → 0.98 `tween(200, EaseOut)`, `HapticFeedbackType.LongPress` |
| Calendar day select | `animateFloatAsState` scale 1 → 1.08 `tween(200, EaseOutCubic)` + soft shadow bloom |
| Screen transition | `AnimatedContent` `fadeIn(tween(350)) + slideInVertically { 8 }` — gentle, never a hard slide |
| Log success | soft sage check `fadeIn(tween(300))` + single `HapticFeedbackType.LongPress`; wheel morphs |

```kotlin
@Composable
fun PhaseLegend() { // decodes the wheel/calendar colors — required for color-blind safety
    Row(horizontalArrangement = Arrangement.spacedBy(16.dp)) {
        legendDot(FloColors.Coral, "Period")
        legendDot(FloColors.Lavender, "Fertile")
        legendDot(FloColors.LavenderDeep, "Ovulation")
        legendDot(Color.Transparent, "Predicted", outline = FloColors.Coral)
    }
}
```

Haptics: prefer `LocalHapticFeedback`. Keep them gentle — Flo is calm. For a softer pulse use `Vibrator` `VibrationEffect.createOneShot(8, 60)` instead of the default click.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Period / flow | `drop.fill` | `Icons.Filled.WaterDrop` |
| Fertile / nature | `leaf.fill` | `Icons.Filled.Spa` |
| Ovulation | `sparkle` | `Icons.Filled.AutoAwesome` |
| Mood | `face.smiling` | `Icons.Outlined.SentimentSatisfied` |
| Symptom (generic) | `heart.text.square` | `Icons.Outlined.MonitorHeart` |
| Add / log | `plus` | `Icons.Filled.Add` |
| Edit | `pencil` | `Icons.Filled.Edit` |
| Info | `info.circle` | `Icons.Outlined.Info` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Today (tab) | `circle.circle.fill` | `Icons.Filled.Circle` |
| Calendar (tab) | `calendar` | `Icons.Filled.CalendarMonth` |
| Insights (tab) | `lightbulb.fill` | `Icons.Filled.Lightbulb` |
| Partner (tab) | `heart.circle.fill` | `Icons.Filled.Favorite` |
| More (tab) | `ellipsis` | `Icons.Filled.MoreHoriz` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas`/`drawArc` and modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the white canvas wants dark-content system bars. Apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the tab bar clears the gesture nav and the wheel centers in the safe area.
- **Tabular figures**: set `fontFeatureSettings = "tnum"` on wheel-center/calendar styles (already in `FloText`) so the grid and center never reflow; confirm the bundled Inter ships `tnum`.
- **Font scaling**: `sp` honors the user's font scale — keep it on greetings, titles, body. Pin the wheel center (must fit the circle), calendar day numbers, tab labels, captions by wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Color is reassurance, not the only signal**: always render the phase legend + text labels so coral/lavender are decodable by color-blind users. Never communicate period/fertile/ovulation by hue alone.
- **TalkBack**: give the cycle wheel a combined `contentDescription` ("Cycle day 14, follicular phase, period in 5 days"); calendar cells announce state ("May 19, predicted period"); merge prediction-card text with `Modifier.semantics(mergeDescendants = true)`.
- **Reduce Motion**: gate the wheel sweep + center scale and the phase crossfade on `Settings.Global.ANIMATOR_DURATION_SCALE` — render the final state immediately when off.
- **Sensitive content**: Flo content is private — support an app-lock and apply `FLAG_SECURE` (or a cover composable on lifecycle `STOPPED`) so the screen is obscured in Recents when the user enables it.
- **Touch targets**: Material guidance is 48.dp minimum — the primary CTA and chips clear it; give 20.dp action glyphs a `Modifier.size(48.dp)` hit area via padding; calendar cells are 40.dp (acceptable inside a grid with adequate spacing).
- **Contrast**: `#6E6A82` on white passes AA at 14sp+; coral `#FF6B81` on white is borderline for small text — use `#E85870` for any sub-16sp coral copy.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Flo's identity requires the fixed white-and-blush canvas with the exact coral/lavender phase language regardless of wallpaper.
