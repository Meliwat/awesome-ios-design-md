# Strong (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Strong's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the set-log table, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Strong's flat neutral-grey canvas, the single Strong-Blue accent, the dense set table with tabular figures, borderless flat cards, the amber PR flag) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `BasicTextField` set cells, an `OngoingActivity` notification instead of a Live Activity, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. Strong is dark-first; a light scheme is provided but opt-in. Do **not** enable Material You dynamic color — the single Strong-Blue accent and the neutral-grey identity must hold regardless of wallpaper.

## 1. Color Tokens

```kotlin
// ui/theme/StrongColors.kt
import androidx.compose.ui.graphics.Color

object StrongColors {
    // Canvas & Surfaces (Dark — default) — true NEUTRAL grey, not blue-tinted
    val Canvas    = Color(0xFF1A1A1A)
    val Card      = Color(0xFF242424)
    val Input     = Color(0xFF2E2E2E)
    val Surface3  = Color(0xFF383838)
    val Divider   = Color(0xFF333333)

    // Canvas & Surfaces (Light — optional, opt-in)
    val LightCanvas  = Color(0xFFF2F2F2)
    val LightCard    = Color(0xFFFFFFFF)
    val LightInput   = Color(0xFFEBEBEB)
    val LightDivider = Color(0xFFDDDDDD)

    // Brand — single accent
    val Blue        = Color(0xFF2F80ED)
    val BluePressed = Color(0xFF2566C0)
    val BlueSoft    = Color(0xFF16263D)

    // Text
    val TextPrimary   = Color(0xFFF5F5F5)
    val TextSecondary = Color(0xFF9A9A9A)
    val TextTertiary  = Color(0xFF636363)

    // Semantic
    val Done  = Color(0xFF27AE60)
    val PR    = Color(0xFFF2C94C)
    val Error = Color(0xFFEB5757)
    val Warn  = Color(0xFFF2994A)

    // Translucent
    val RowFill   = Color(0xFF27AE60).copy(alpha = 0.18f)
    val PRFlagBg  = Color(0xFFF2C94C).copy(alpha = 0.18f)
    val RestFill  = Color(0xFF2F80ED).copy(alpha = 0.22f)
    val TabFill   = Color(0xFF2F80ED).copy(alpha = 0.18f)
}
```

Wire it into both schemes. Strong is dark-first; the dark scheme uses the signature flat `#1A1A1A`, never true black, never blue-tinted.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme

private val StrongDark = darkColorScheme(
    primary        = StrongColors.Blue,
    onPrimary      = Color(0xFFFFFFFF),
    background     = StrongColors.Canvas,
    onBackground   = StrongColors.TextPrimary,
    surface        = StrongColors.Card,
    onSurface      = StrongColors.TextPrimary,
    surfaceVariant = StrongColors.Input,
    outline        = StrongColors.Divider,
    error          = StrongColors.Error,
)

private val StrongLight = lightColorScheme(
    primary        = StrongColors.Blue,
    onPrimary      = Color(0xFFFFFFFF),
    background      = StrongColors.LightCanvas,
    onBackground   = Color(0xFF1A1A1A),
    surface        = StrongColors.LightCard,
    onSurface      = Color(0xFF1A1A1A),
    surfaceVariant = StrongColors.LightInput,
    outline        = StrongColors.LightDivider,
    error          = StrongColors.Error,
)

@Composable
fun StrongTheme(
    dark: Boolean = isSystemInDarkTheme(),   // default true in app settings
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) StrongDark else StrongLight,
    typography = StrongTypography,
    content = content,
)
```

## 2. Typography

Strong ships one face — **Inter** — drop the TTFs in `res/font/`. Every numeric style sets a tabular-figures `FontFeatureSetting` so the set table and timers never reflow.

```kotlin
// ui/theme/StrongType.kt
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

object StrongText {
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val Workout     = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val Exercise    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Metric      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 20.sp, fontFeatureSettings = TNUM)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val SetCell     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp, fontFeatureSettings = TNUM)
    val RowTitle    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 13.sp, lineHeight = 18.sp)
    val Column      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 12.sp, letterSpacing = 0.4.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Flag        = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.3.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val StrongTypography = Typography(
    headlineLarge = StrongText.ScreenTitle,
    headlineMedium = StrongText.Workout,
    titleMedium   = StrongText.Exercise,
    bodyMedium    = StrongText.Body,
    labelSmall    = StrongText.Tab,
)
```

## 3. Signature Components

### Active-Workout Header (title + stat pills)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

@Composable
fun StrongWorkoutHeader(
    title: String, time: String, volume: String, setCount: Int,
    onFinish: () -> Unit,
) {
    Column(
        Modifier.fillMaxWidth().padding(horizontal = 18.dp).padding(bottom = 10.dp)
            .drawBottomDivider(StrongColors.Divider),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically) {
            Text(title, style = StrongText.Workout, color = StrongColors.TextPrimary)
            Spacer(Modifier.weight(1f))
            Button(
                onClick = onFinish,
                shape = RoundedCornerShape(8.dp),
                colors = ButtonDefaults.buttonColors(containerColor = StrongColors.Blue),
                contentPadding = PaddingValues(horizontal = 16.dp, vertical = 8.dp),
            ) { Text("Finish", style = StrongText.Button.copy(fontSize = 13.sp), color = Color.White) }
        }
        Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            Pill("Time", time); Pill("Volume", volume); Pill("Sets", "$setCount")
        }
    }
}

@Composable
private fun Pill(label: String, value: String) {
    Row(
        Modifier.clip(RoundedCornerShape(7.dp)).background(StrongColors.Input)
            .padding(horizontal = 10.dp, vertical = 5.dp),
        horizontalArrangement = Arrangement.spacedBy(4.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Text(label, style = StrongText.Caption.copy(fontSize = 12.sp, fontWeight = FontWeight.SemiBold),
             color = StrongColors.TextSecondary)
        Text(value, style = StrongText.Metric.copy(fontSize = 12.sp), color = StrongColors.Blue)
    }
}

// Tiny helper for the hairline divider
fun Modifier.drawBottomDivider(color: Color) = drawBehind {
    drawLine(color, androidx.compose.ui.geometry.Offset(0f, size.height),
             androidx.compose.ui.geometry.Offset(size.width, size.height), 1f)
}
```

### Set Row (the core atom)

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsFocusedAsState
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.foundation.text.KeyboardOptions
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.input.KeyboardType
import androidx.compose.ui.text.style.TextAlign

@Composable
fun StrongSetRow(label: String, isWarmup: Boolean, previous: String) {
    var weight by remember { mutableStateOf("") }
    var reps by remember { mutableStateOf("") }
    var done by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current

    Row(
        Modifier.fillMaxWidth().padding(vertical = 6.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Text(label, style = StrongText.SetCell.copy(fontWeight = FontWeight.Bold),
             color = if (isWarmup) StrongColors.Warn else StrongColors.TextSecondary,
             modifier = Modifier.width(36.dp), textAlign = TextAlign.Center)
        Text(previous, style = StrongText.Caption.copy(fontFeatureSettings = "tnum"),
             color = StrongColors.TextTertiary, modifier = Modifier.weight(1f), textAlign = TextAlign.Center)

        Cell(weight, { weight = it }, done)
        Cell(reps, { reps = it }, done)

        Box(
            Modifier.size(24.dp).clip(RoundedCornerShape(7.dp))
                .background(if (done) StrongColors.Done else Color.Transparent)
                .border(1.5.dp, if (done) StrongColors.Done else StrongColors.Divider, RoundedCornerShape(7.dp))
                .clickable {
                    done = !done
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress) // success analog
                },
            contentAlignment = Alignment.Center,
        ) {
            if (done) Icon(Icons.Filled.Check, null, tint = Color.White, modifier = Modifier.size(13.dp))
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
        textStyle = StrongText.SetCell.copy(
            color = if (done) StrongColors.Done else StrongColors.TextPrimary, textAlign = TextAlign.Center),
        keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Decimal),
        modifier = Modifier.weight(1f).height(34.dp).clip(RoundedCornerShape(7.dp))
            .background(if (done) StrongColors.RowFill else StrongColors.Input)
            .border(1.5.dp, if (focused) StrongColors.Blue else Color.Transparent, RoundedCornerShape(7.dp)),
    )
}
```

### Rest-Timer Bar

```kotlin
import androidx.compose.material.icons.filled.Timer

@Composable
fun StrongRestBar(remaining: Int, total: Int, onAdjust: (Int) -> Unit) {
    val fill = if (total == 0) 0f else remaining.toFloat() / total
    Box(
        Modifier.fillMaxWidth().padding(horizontal = 18.dp).height(40.dp)
            .clip(RoundedCornerShape(8.dp)).background(StrongColors.BlueSoft),
        contentAlignment = Alignment.CenterStart,
    ) {
        Box(Modifier.fillMaxHeight().fillMaxWidth(fill).background(StrongColors.RestFill))
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(10.dp),
        ) {
            Icon(Icons.Filled.Timer, null, tint = StrongColors.Blue, modifier = Modifier.size(16.dp))
            Text("Rest Timer · ${remaining / 60}:${(remaining % 60).toString().padStart(2, '0')}",
                 style = StrongText.SetCell.copy(fontSize = 13.sp), color = StrongColors.Blue,
                 modifier = Modifier.weight(1f))
            Text("−15", style = StrongText.Caption.copy(fontWeight = FontWeight.Bold),
                 color = StrongColors.TextSecondary, modifier = Modifier.clickable { onAdjust(-15) })
            Text("+15", style = StrongText.Caption.copy(fontWeight = FontWeight.Bold),
                 color = StrongColors.TextSecondary, modifier = Modifier.clickable { onAdjust(15) })
        }
    }
}
```

### PR Flag

```kotlin
import androidx.compose.animation.core.Spring
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.ui.graphics.graphicsLayer

@Composable
fun StrongPRFlag(text: String) {
    var shown by remember { mutableStateOf(false) }
    val scale by animateFloatAsState(
        if (shown) 1f else 0.92f,
        spring(dampingRatio = Spring.DampingRatioMediumBouncy, stiffness = Spring.StiffnessMedium),
        label = "prScale",
    )
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(Unit) { shown = true; haptics.performHapticFeedback(HapticFeedbackType.LongPress) }

    Box(
        Modifier.graphicsLayer { scaleX = scale; scaleY = scale; alpha = if (shown) 1f else 0f }
            .clip(RoundedCornerShape(5.dp)).background(StrongColors.PRFlagBg)
            .padding(horizontal = 6.dp, vertical = 2.dp),
    ) { Text(text.uppercase(), style = StrongText.Flag, color = StrongColors.PR) }
}
```

### Exercise Card (flat, borderless — a signature)

```kotlin
import androidx.compose.material.icons.filled.MoreHoriz
import androidx.compose.material.icons.filled.FitnessCenter

@Composable
fun StrongExerciseCard(
    name: String,
    note: String? = null,
    onAddSet: () -> Unit,
    sets: @Composable ColumnScope.() -> Unit,
) {
    Column(
        Modifier.fillMaxWidth().clip(RoundedCornerShape(14.dp))
            .background(StrongColors.Card)   // flat — NO border (signature)
            .padding(14.dp),
        verticalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(10.dp)) {
            Box(Modifier.size(34.dp).clip(RoundedCornerShape(9.dp)).background(StrongColors.Surface3),
                contentAlignment = Alignment.Center) {
                Icon(Icons.Filled.FitnessCenter, null, tint = StrongColors.Blue, modifier = Modifier.size(19.dp))
            }
            Text(name, style = StrongText.Exercise, color = StrongColors.Blue, modifier = Modifier.weight(1f))
            Icon(Icons.Filled.MoreHoriz, "Exercise menu", tint = StrongColors.TextTertiary)
        }
        if (note != null) Text(note, style = StrongText.Caption, color = StrongColors.TextSecondary)
        sets()
        Box(
            Modifier.fillMaxWidth().height(34.dp).clip(RoundedCornerShape(8.dp))
                .background(StrongColors.Input).clickable { onAddSet() },
            contentAlignment = Alignment.Center,
        ) { Text("+ Add Set", style = StrongText.Caption.copy(fontWeight = FontWeight.Bold), color = StrongColors.Blue) }
    }
}
```

## 4. Navigation

Strong has a 5-tab bottom strip and large-title screens. On Android, model the strip as a `NavigationBar`. There is no tint pill — active is Strong Blue with a soft fill.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun StrongBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = StrongColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Profile"   to Icons.Filled.Menu,
            "History"   to Icons.Filled.CalendarMonth,
            "Workout"   to Icons.Filled.AddCircle,
            "Exercises" to Icons.Filled.FitnessCenter,
            "Settings"  to Icons.Filled.Settings,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(if (i == 2) 24.dp else 22.dp)) },
                label = { Text(label, style = StrongText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = StrongColors.Blue,
                    selectedTextColor = StrongColors.Blue,
                    unselectedIconColor = StrongColors.TextTertiary,
                    unselectedTextColor = StrongColors.TextTertiary,
                    indicatorColor = StrongColors.TabFill,   // soft blue fill, not a Material pill
                ),
            )
        }
    }
}
```

Large-title screens (History / Exercises / Profile) use a `LargeTopAppBar` whose 32sp title collapses to a 17sp centered title on scroll (`TopAppBarDefaults.exitUntilCollapsedScrollBehavior`).

## 5. Motion

Strong motion is functional and fast — the app must feel instant. Range 120–300ms.

| Moment | Compose recipe |
|--------|----------------|
| Set check | cell bg → `RowFill` (instant or `animateColorAsState tween(140)`); checkmark fills; success haptic; auto-run rest |
| PR flag | `animateFloatAsState` 0.92→1.0 `spring(MediumBouncy, Medium)` + alpha; soft haptic |
| Add set | new row `AnimatedVisibility` `expandVertically(tween(200)) + fadeIn` |
| Finish → summary | navigate to full-screen route, slide-up `tween(300)` |
| Rest bar | fill width driven by a 1s-tick countdown (or `animateFloatAsState` linear over total) |
| Tab switch | instant — no crossfade (immediacy over polish) |
| Pull-to-refresh (history) | Material `PullToRefreshBox`; rows `fadeIn` on load |

```kotlin
// Set check — the canonical Strong motion (cell fill)
val cellBg by animateColorAsState(
    targetValue = if (done) StrongColors.RowFill else StrongColors.Input,
    animationSpec = tween(140),
    label = "cellBg",
)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft success on set check and the PR appearance; for the rest-timer ±/end use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`. Auto-save of the in-progress workout is silent.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The set-checkmark is `Check`; exercise glyphs map to `FitnessCenter`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Profile (tab) | `line.3.horizontal` | `Icons.Filled.Menu` |
| History (tab) | `calendar` | `Icons.Filled.CalendarMonth` |
| Workout (tab) | `plus.circle.fill` | `Icons.Filled.AddCircle` |
| Exercises (tab) | `chart.bar.fill` | `Icons.Filled.FitnessCenter` |
| Settings (tab) | `gearshape.fill` | `Icons.Filled.Settings` |
| Set checkmark | `checkmark` | `Icons.Filled.Check` |
| Exercise menu | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Rest timer | `timer` | `Icons.Filled.Timer` |
| Add | `plus` | `Icons.Filled.Add` |
| PR flag | `triangle.fill` | `Icons.Filled.ArrowDropUp` |
| Superset | `link` | `Icons.Filled.Link` |
| Plate calculator | `circle.grid.2x2.fill` | `Icons.Filled.Album` |
| Reorder | `line.3.horizontal` | `Icons.Filled.DragHandle` |
| Delete | `trash` | `Icons.Filled.Delete` |
| Volume / 1RM chart | `chart.xyaxis.line` | `Icons.Filled.ShowChart` |
| Body measurements | `ruler` | `Icons.Filled.Straighten` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `BasicTextField` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the flat near-black canvas wants light-content system bars. The set-cell keyboard must not hide the active row — use `Modifier.imePadding()` on the workout list and keep the rest bar pinned above the IME.
- **Tabular figures**: every numeric `TextStyle` sets `fontFeatureSettings = "tnum"` (weights, reps, sets, volume, 1RM, timers) so the set table and timers never reflow.
- **Font scaling**: `sp` honors the user's font scale — keep it on titles/body/cells. Pin layout-sensitive text (11sp column labels, 10sp tab labels, PR flag, set-index marker) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: label a set row "Set {label}, previous {prev}, {weight} kilograms, {reps} reps, {logged/not logged}"; expose the checkmark via `Modifier.toggleable`; announce a PR with `LiveRegionMode.Assertive` ("Personal record, {detail}").
- **Touch targets**: Material guidance is 48.dp. Give the 24.dp checkmark a 48.dp hit area via padding; set cells are 34.dp tall but full-cell tappable; primary buttons ≥ 36.dp.
- **Contrast**: `#F5F5F5` on `#1A1A1A` and `#2F80ED` on `#1A1A1A` pass WCAG AA. The green cell fill is decorative — the checkmark + TalkBack state carry completion, not color alone.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the PR spring/pulse and the add-set expand (substitute a plain `Crossfade`); keep the rest-bar fill depletion (it conveys remaining time).
- **Ongoing rest timer**: mirror the rest countdown to an `OngoingActivity` / foreground-service notification (and Wear `OngoingActivity` if targeting Wear OS) with the remaining time; keep digits monospaced.
- **Flat cards**: exercise cards have no border by design — do not add an `outline` stroke; the `#242424` surface lift is the depth cue.
- **Dark mode**: dark is the default; the light scheme is opt-in via settings. Do **not** enable `dynamicColorScheme()` — Strong's single-blue, neutral-grey identity must hold regardless of wallpaper.
