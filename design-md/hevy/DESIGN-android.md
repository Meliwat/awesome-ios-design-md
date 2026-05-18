# Hevy (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Hevy's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the live workout log, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Hevy's near-black training canvas, the single Hevy-Blue action color, the dense set table with tabular figures, the gold PR badge) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `BasicTextField` set cells, an `OngoingActivity` notification instead of a Live Activity, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars. Hevy is dark-first; a light scheme is provided but opt-in. Do **not** enable Material You dynamic color — the single Hevy-Blue accent must hold regardless of wallpaper.

## 1. Color Tokens

```kotlin
// ui/theme/HevyColors.kt
import androidx.compose.ui.graphics.Color

object HevyColors {
    // Canvas & Surfaces (Dark — default)
    val Canvas    = Color(0xFF0E1116) // near-black, blue-cool — NOT pure black
    val Card      = Color(0xFF161B22)
    val Input     = Color(0xFF1F2630)
    val Surface3  = Color(0xFF283040)
    val Divider   = Color(0xFF262C36)

    // Canvas & Surfaces (Light — optional, opt-in)
    val LightCanvas  = Color(0xFFF4F6FA)
    val LightCard    = Color(0xFFFFFFFF)
    val LightInput   = Color(0xFFEEF1F6)
    val LightDivider = Color(0xFFE2E6EE)

    // Brand — single action color
    val Blue        = Color(0xFF1E6FFF)
    val BluePressed = Color(0xFF1857CC)
    val BlueSoft    = Color(0xFF14233F)

    // Text
    val TextPrimary   = Color(0xFFF2F4F8)
    val TextSecondary = Color(0xFF9BA3B0)
    val TextTertiary  = Color(0xFF5E6675)

    // Semantic
    val Done  = Color(0xFF2FBF71)
    val PR    = Color(0xFFF5B83D)
    val Error = Color(0xFFF1545B)
    val Warn  = Color(0xFFF5A623)

    // Translucent
    val RowWash   = Color(0xFF2FBF71).copy(alpha = 0.16f)
    val PRBadgeBg = Color(0xFFF5B83D).copy(alpha = 0.18f)
    val RingTrack = Color(0xFF1E6FFF).copy(alpha = 0.30f)
    val RingEdge  = Color(0xFF1E6FFF).copy(alpha = 0.35f)
    val TabFill   = Color(0xFF1E6FFF).copy(alpha = 0.16f)
}
```

Wire it into both schemes. Hevy is dark-first; the dark scheme uses the signature `#0E1116`, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme

private val HevyDark = darkColorScheme(
    primary        = HevyColors.Blue,
    onPrimary      = Color(0xFFFFFFFF),
    background     = HevyColors.Canvas,
    onBackground   = HevyColors.TextPrimary,
    surface        = HevyColors.Card,
    onSurface      = HevyColors.TextPrimary,
    surfaceVariant = HevyColors.Input,
    outline        = HevyColors.Divider,
    error          = HevyColors.Error,
)

private val HevyLight = lightColorScheme(
    primary        = HevyColors.Blue,
    onPrimary      = Color(0xFFFFFFFF),
    background      = HevyColors.LightCanvas,
    onBackground   = Color(0xFF11151C),
    surface        = HevyColors.LightCard,
    onSurface      = Color(0xFF11151C),
    surfaceVariant = HevyColors.LightInput,
    outline        = HevyColors.LightDivider,
    error          = HevyColors.Error,
)

@Composable
fun HevyTheme(
    dark: Boolean = isSystemInDarkTheme(),   // default true in app settings
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) HevyDark else HevyLight,
    typography = HevyTypography,
    content = content,
)
```

## 2. Typography

Hevy ships one face — **Inter** — drop the TTFs in `res/font/`. Every numeric style sets a tabular-figures `FontFeatureSetting` so the set table and timers never reflow.

```kotlin
// ui/theme/HevyType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),
    Font(R.font.inter_medium,    FontWeight.Medium),
    Font(R.font.inter_semibold,  FontWeight.SemiBold),
    Font(R.font.inter_bold,      FontWeight.Bold),
    Font(R.font.inter_extrabold, FontWeight.ExtraBold),
)

private const val TNUM = "tnum"   // tabular figures — lock column widths

object HevyText {
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val Routine     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val Exercise    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Metric      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 20.sp, fontFeatureSettings = TNUM)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val SetCell     = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp, fontFeatureSettings = TNUM)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 13.sp, lineHeight = 18.sp)
    val Column      = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 12.sp, letterSpacing = 0.5.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Badge       = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.3.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val HevyTypography = Typography(
    headlineLarge = HevyText.ScreenTitle,
    headlineMedium = HevyText.Routine,
    titleMedium   = HevyText.Exercise,
    bodyMedium    = HevyText.Body,
    labelSmall    = HevyText.Tab,
)
```

## 3. Signature Components

### Active-Workout Header

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

@Composable
fun WorkoutHeader(
    routineName: String,
    duration: String, volume: String, setCount: Int,
    onFinish: () -> Unit,
) {
    Column(Modifier.fillMaxWidth().padding(horizontal = 18.dp).padding(bottom = 12.dp),
           verticalArrangement = Arrangement.spacedBy(12.dp)) {
        Row(verticalAlignment = Alignment.CenterVertically) {
            Text(routineName, style = HevyText.Routine, color = HevyColors.TextPrimary)
            Spacer(Modifier.weight(1f))
            Button(
                onClick = onFinish,
                shape = RoundedCornerShape(10.dp),
                colors = ButtonDefaults.buttonColors(containerColor = HevyColors.Blue),
                contentPadding = PaddingValues(horizontal = 16.dp, vertical = 7.dp),
            ) { Text("Finish", style = HevyText.Button.copy(fontSize = 13.sp), color = Color.White) }
        }
        Row(horizontalArrangement = Arrangement.spacedBy(22.dp)) {
            Stat("Duration", duration, live = true)
            Stat("Volume", volume, live = false)
            Stat("Sets", "$setCount", live = false)
        }
    }
}

@Composable
private fun Stat(label: String, value: String, live: Boolean) {
    Column {
        Text(label, style = HevyText.Caption.copy(fontSize = 11.sp), color = HevyColors.TextSecondary)
        Text(value, style = HevyText.Metric, color = if (live) HevyColors.Blue else HevyColors.TextPrimary)
    }
}
```

### Set Row (the core atom)

```kotlin
import androidx.compose.animation.animateColorAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.input.KeyboardType
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.foundation.text.KeyboardOptions
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsFocusedAsState

@Composable
fun SetRow(index: Int, previous: String) {
    var weight by remember { mutableStateOf("") }
    var reps by remember { mutableStateOf("") }
    var done by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current
    val rowBg by animateColorAsState(if (done) HevyColors.RowWash else Color.Transparent, tween(150), label = "rowBg")

    Row(
        Modifier.fillMaxWidth().clip(RoundedCornerShape(8.dp)).background(rowBg).padding(vertical = 7.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box(Modifier.size(28.dp).clip(RoundedCornerShape(8.dp)).background(HevyColors.Input),
            contentAlignment = Alignment.Center) {
            Text("$index", style = HevyText.Caption.copy(fontWeight = FontWeight.Bold), color = HevyColors.TextSecondary)
        }
        Text(previous, style = HevyText.Caption.copy(fontFeatureSettings = "tnum"),
             color = HevyColors.TextTertiary, modifier = Modifier.weight(1f), textAlign = TextAlign.Center)

        Cell(weight, { weight = it }, done)
        Cell(reps, { reps = it }, done)

        Box(
            Modifier.size(26.dp).clip(RoundedCornerShape(8.dp))
                .background(if (done) HevyColors.Done else Color.Transparent)
                .border(1.5.dp, if (done) HevyColors.Done else HevyColors.Divider, RoundedCornerShape(8.dp))
                .clickable {
                    done = !done
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress) // success analog
                },
            contentAlignment = Alignment.Center,
        ) {
            if (done) Icon(Icons.Filled.Check, null, tint = Color.White, modifier = Modifier.size(14.dp))
        }
    }
}

@Composable
private fun RowScope.Cell(value: String, onChange: (String) -> Unit, done: Boolean) {
    val src = remember { MutableInteractionSource() }
    val focused by src.collectIsFocusedAsState()
    BasicTextField(
        value = value, onValueChange = onChange,
        interactionSource = src,
        textStyle = HevyText.SetCell.copy(
            color = if (done) HevyColors.Done else HevyColors.TextPrimary, textAlign = TextAlign.Center),
        keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Decimal),
        modifier = Modifier.weight(1f).height(36.dp).clip(RoundedCornerShape(8.dp))
            .background(HevyColors.Input)
            .border(1.5.dp, if (focused) HevyColors.Blue else Color.Transparent, RoundedCornerShape(8.dp)),
    )
}
```

### Rest-Timer Pill

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke

@Composable
fun RestTimerPill(remaining: Int, total: Int, onSkip: () -> Unit) {
    val progress = if (total == 0) 0f else 1f - remaining.toFloat() / total
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 18.dp).height(44.dp)
            .clip(RoundedCornerShape(12.dp)).background(HevyColors.BlueSoft)
            .border(1.dp, HevyColors.RingEdge, RoundedCornerShape(12.dp))
            .padding(horizontal = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Canvas(Modifier.size(22.dp)) {
            drawCircle(HevyColors.RingTrack, style = Stroke(width = 3.dp.toPx()))
            drawArc(
                color = HevyColors.Blue, startAngle = -90f, sweepAngle = 360f * progress,
                useCenter = false, size = Size(size.width, size.height),
                style = Stroke(width = 3.dp.toPx(), cap = StrokeCap.Round),
            )
        }
        Text("Rest · ${remaining / 60}:${(remaining % 60).toString().padStart(2, '0')}",
             style = HevyText.SetCell.copy(fontSize = 14.sp), color = HevyColors.Blue,
             modifier = Modifier.weight(1f))
        Text("Skip", style = HevyText.Caption.copy(fontWeight = FontWeight.SemiBold),
             color = HevyColors.TextSecondary, modifier = Modifier.clickable { onSkip() })
    }
}
```

### PR Badge

```kotlin
import androidx.compose.animation.core.Spring
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring

@Composable
fun PRBadge(text: String) {
    var shown by remember { mutableStateOf(false) }
    val scale by animateFloatAsState(
        if (shown) 1f else 0.9f,
        spring(dampingRatio = Spring.DampingRatioMediumBouncy, stiffness = Spring.StiffnessLow),
        label = "prScale",
    )
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(Unit) { shown = true; haptics.performHapticFeedback(HapticFeedbackType.LongPress) }

    Box(
        Modifier.graphicsLayer { scaleX = scale; scaleY = scale; alpha = if (shown) 1f else 0f }
            .clip(RoundedCornerShape(6.dp)).background(HevyColors.PRBadgeBg)
            .padding(horizontal = 7.dp, vertical = 3.dp),
    ) {
        Text(text.uppercase(), style = HevyText.Badge, color = HevyColors.PR)
    }
}
```

### Exercise Card

```kotlin
import androidx.compose.material.icons.filled.MoreHoriz
import androidx.compose.material.icons.filled.FitnessCenter

@Composable
fun ExerciseCard(
    name: String,
    onAddSet: () -> Unit,
    sets: @Composable ColumnScope.() -> Unit,
) {
    Column(
        Modifier.fillMaxWidth().clip(RoundedCornerShape(16.dp)).background(HevyColors.Card)
            .border(1.dp, HevyColors.Divider, RoundedCornerShape(16.dp)).padding(14.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(10.dp)) {
            Box(Modifier.size(36.dp).clip(RoundedCornerShape(10.dp)).background(HevyColors.Surface3),
                contentAlignment = Alignment.Center) {
                Icon(Icons.Filled.FitnessCenter, null, tint = HevyColors.Blue, modifier = Modifier.size(20.dp))
            }
            Text(name, style = HevyText.Exercise, color = HevyColors.Blue, modifier = Modifier.weight(1f))
            Icon(Icons.Filled.MoreHoriz, "Exercise menu", tint = HevyColors.TextTertiary)
        }
        sets()
        Box(
            Modifier.fillMaxWidth().height(34.dp).clip(RoundedCornerShape(9.dp))
                .background(HevyColors.Input).clickable { onAddSet() },
            contentAlignment = Alignment.Center,
        ) { Text("+ Add Set", style = HevyText.Caption.copy(fontWeight = FontWeight.Bold), color = HevyColors.TextSecondary) }
    }
}
```

## 4. Navigation

Hevy has a 5-tab bottom strip and large-title screens. On Android, model the strip as a `NavigationBar`. There is no tint pill — active is Hevy Blue with a soft fill.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun HevyBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = HevyColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"      to Icons.Filled.Home,
            "Profile"   to Icons.Filled.AccountCircle,
            "Workout"   to Icons.Filled.AddCircle,
            "History"   to Icons.Filled.CalendarMonth,
            "Exercises" to Icons.Filled.FitnessCenter,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(if (i == 2) 24.dp else 22.dp)) },
                label = { Text(label, style = HevyText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = HevyColors.Blue,
                    selectedTextColor = HevyColors.Blue,
                    unselectedIconColor = HevyColors.TextTertiary,
                    unselectedTextColor = HevyColors.TextTertiary,
                    indicatorColor = HevyColors.TabFill,   // soft blue fill, not a Material pill
                ),
            )
        }
    }
}
```

Large-title screens (Home / History / Exercises) use a `LargeTopAppBar` whose 32sp title collapses to a 17sp centered title on scroll (`TopAppBarDefaults.exitUntilCollapsedScrollBehavior`).

## 5. Motion

Hevy motion is functional and fast — a gym tool must feel instant. Range 120–320ms.

| Moment | Compose recipe |
|--------|----------------|
| Set check | row bg `animateColorAsState` → `RowWash` `tween(150)`; checkmark fills; success haptic; auto-start rest |
| PR badge | `animateFloatAsState` 0.9→1.0 `spring(MediumBouncy, Low)` + alpha; medium haptic |
| Add set | new row `AnimatedVisibility` `expandVertically(tween(200)) + fadeIn` |
| Finish → summary | navigate to full-screen route, slide-up `tween(320)` |
| Rest ring | `Canvas` arc driven by a 1s-tick countdown (or `animateFloatAsState` linear over total) |
| Tab switch | instant — no crossfade (immediacy over polish) |
| Pull-to-refresh (feed) | Material `PullToRefreshBox`; cards `fadeIn` on load |

```kotlin
// Set check — the canonical Hevy motion
val rowBg by animateColorAsState(
    targetValue = if (done) HevyColors.RowWash else Color.Transparent,
    animationSpec = tween(150),
    label = "rowBg",
)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft success on set check and the medium tap on a PR; for the rest-timer ±/end use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`. Auto-save of the in-progress workout is silent — no motion, no haptic.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The set-checkmark is `Check`; exercise glyphs map to `FitnessCenter`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Workout (tab) | `plus.circle.fill` | `Icons.Filled.AddCircle` |
| History (tab) | `calendar` | `Icons.Filled.CalendarMonth` |
| Exercises (tab) | `chart.bar.fill` | `Icons.Filled.FitnessCenter` |
| Set checkmark | `checkmark` | `Icons.Filled.Check` |
| Exercise menu | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Rest timer | `timer` | `Icons.Filled.Timer` |
| Add | `plus` | `Icons.Filled.Add` |
| PR badge | `star.fill` | `Icons.Filled.Star` |
| Superset | `link` | `Icons.Filled.Link` |
| Warm-up | `flame` | `Icons.Filled.LocalFireDepartment` |
| Reorder | `line.3.horizontal` | `Icons.Filled.DragHandle` |
| Delete | `trash` | `Icons.Filled.Delete` |
| Volume chart | `chart.xyaxis.line` | `Icons.Filled.ShowChart` |
| Kudos | `hand.thumbsup` | `Icons.Filled.ThumbUp` |
| Comment | `bubble.left` | `Icons.Filled.ChatBubbleOutline` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `BasicTextField` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the near-black canvas wants light-content system bars. The set-cell keyboard must not hide the active row — use `Modifier.imePadding()` on the workout list and keep the rest pill pinned above the IME.
- **Tabular figures**: every numeric `TextStyle` sets `fontFeatureSettings = "tnum"` (weights, reps, sets, volume, 1RM, timers) so the set table and timers never reflow.
- **Font scaling**: `sp` honors the user's font scale — keep it on titles/body/cells. Pin layout-sensitive text (11sp column labels, 10sp tab labels, PR badge, set-index chip) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: label a set row "Set {n}, previous {prev}, {weight} kilograms, {reps} reps, {completed/not completed}"; expose the checkmark via `Modifier.toggleable`; announce a PR with `LiveRegionMode.Assertive` ("New personal record, {detail}").
- **Touch targets**: Material guidance is 48.dp. Give the 26.dp checkmark and 28.dp index chip a 48.dp hit area via padding; set cells are 36.dp tall but full-cell tappable; primary buttons ≥ 36.dp.
- **Contrast**: `#F2F4F8` on `#0E1116` and `#1E6FFF` on `#0E1116` pass WCAG AA. The green row-wash is decorative — the checkmark + TalkBack state carry completion, not color alone.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the PR spring/pulse and the add-set expand (substitute a plain `Crossfade`); keep the rest-ring sweep (it conveys remaining time).
- **Ongoing rest timer**: mirror the rest countdown to an `OngoingActivity` / foreground-service notification (and Wear `OngoingActivity` if targeting Wear OS) with the remaining time; keep digits monospaced.
- **Dark mode**: dark is the default; the light scheme is opt-in via settings. Do **not** enable `dynamicColorScheme()` — Hevy's single-blue identity must hold regardless of wallpaper.
