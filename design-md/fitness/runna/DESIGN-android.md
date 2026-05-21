# Runna (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Runna's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the signature week strip + session card + structure breakdown as `@Composable`s, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This keeps the *visual* identity (the Indigo-tinted dark canvas, the color-coded plan week strip, the two-color Indigo/Lime system, Lime as action-only) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas`/`Row(weight)` instead of SwiftUI layout, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. No color extraction — Runna's two-color brand and run-type system are fixed and learned, so Palette is not used. Runna is **dark-first**; an optional light scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/RunnaColors.kt
import androidx.compose.ui.graphics.Color

object RunColors {
    // Brand
    val Indigo        = Color(0xFF4F46E5)
    val IndigoBright  = Color(0xFF6366F1)
    val IndigoPressed = Color(0xFF4338CA)
    val Lime          = Color(0xFFC2F94E)
    val LimePressed   = Color(0xFFA3D93B)

    // Canvas & Surfaces (Dark, default)
    val Canvas   = Color(0xFF0E0E16) // Indigo-tinted blue-black, NOT neutral
    val Surface1 = Color(0xFF181826)
    val Surface2 = Color(0xFF222234)
    val Divider  = Color(0xFF2C2C42)

    // Canvas & Surfaces (Light, optional)
    val LightCanvas  = Color(0xFFF6F6FB)
    val LightSurface = Color(0xFFFFFFFF)
    val LightDivider = Color(0xFFE5E5F0)

    // Text
    val TextPrimary   = Color(0xFFF3F3FB)
    val TextSecondary = Color(0xFF9C9CB8)
    val TextTertiary  = Color(0xFF65657E)
    val TextOnLime    = Color(0xFF1A1A1A) // ONLY text color on Lime

    // Run-type system (FIXED both themes)
    val RtEasy     = Color(0xFF34D399)
    val RtTempo    = Color(0xFFFBBF24)
    val RtInterval = Color(0xFFFB7185)
    val RtLong     = Color(0xFF4F46E5) // Indigo

    // Semantic
    val Success = Color(0xFF34D399)
    val Error   = Color(0xFFFB7185)
    val Warning = Color(0xFFFBBF24)
    val PR      = Color(0xFFC2F94E)
}

enum class RunType(val color: Color, val onColor: Color) {
    Easy(RunColors.RtEasy, RunColors.TextOnLime),
    Tempo(RunColors.RtTempo, RunColors.TextOnLime),
    Interval(RunColors.RtInterval, RunColors.TextOnLime),
    Long(RunColors.RtLong, Color.White),
}
```

Wire it into both schemes. Runna is dark-first; the dark scheme is the signature.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val RunDark = darkColorScheme(
    primary        = RunColors.Lime,           // the "go" color
    onPrimary      = RunColors.TextOnLime,
    secondary      = RunColors.Indigo,          // structural
    onSecondary    = Color.White,
    background     = RunColors.Canvas,
    onBackground   = RunColors.TextPrimary,
    surface        = RunColors.Surface1,
    onSurface      = RunColors.TextPrimary,
    surfaceVariant = RunColors.Surface2,
    outline        = RunColors.Divider,
    error          = RunColors.Error,
)

private val RunLight = lightColorScheme(
    primary        = RunColors.Lime,
    onPrimary      = RunColors.TextOnLime,
    secondary      = RunColors.Indigo,
    onSecondary    = Color.White,
    background     = RunColors.LightCanvas,
    onBackground   = Color(0xFF15151F),
    surface        = RunColors.LightSurface,
    onSurface      = Color(0xFF15151F),
    surfaceVariant = RunColors.LightDivider,
    outline        = RunColors.LightDivider,
    error          = RunColors.Error,
)

@Composable
fun RunnaTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) RunDark else RunLight,
    typography = RunTypography,
    content = content,
)
```

## 2. Typography (M3)

Runna uses **Sora** (Google Fonts, SIL OFL). Drop the TTFs in `res/font/`. Heavy stats, regular prose.

```kotlin
// ui/theme/RunType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Sora = FontFamily(
    Font(R.font.sora_regular,   FontWeight.Normal),
    Font(R.font.sora_medium,    FontWeight.Medium),
    Font(R.font.sora_semibold,  FontWeight.SemiBold),
    Font(R.font.sora_bold,      FontWeight.Bold),
    Font(R.font.sora_extrabold, FontWeight.ExtraBold),
)

object RunText {
    val ScreenTitle  = TextStyle(Sora, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val PlanTitle    = TextStyle(Sora, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val SessionTitle = TextStyle(Sora, fontWeight = FontWeight.ExtraBold, fontSize = 21.sp, lineHeight = 25.sp, letterSpacing = (-0.3).sp)
    val Section      = TextStyle(Sora, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val CardTitle    = TextStyle(Sora, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val Body         = TextStyle(Sora, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val StepItem     = TextStyle(Sora, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 21.sp)
    val Meta         = TextStyle(Sora, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val StatValue    = TextStyle(Sora, fontWeight = FontWeight.ExtraBold, fontSize = 18.sp, lineHeight = 20.sp)
    val Eyebrow      = TextStyle(Sora, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 1.2.sp)
    val Button       = TextStyle(Sora, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.1.sp)
    val Pace         = TextStyle(Sora, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val DayNum       = TextStyle(Sora, fontWeight = FontWeight.ExtraBold, fontSize = 14.sp, lineHeight = 14.sp)
    val Tab          = TextStyle(Sora, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

val RunTypography = Typography(
    headlineLarge = RunText.ScreenTitle,
    headlineMedium = RunText.Section,
    titleMedium   = RunText.CardTitle,
    bodyMedium    = RunText.Body,
    labelSmall    = RunText.Tab,
)
```

## 3. Signature Components

### Training-Plan Week Strip

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.alpha
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

sealed interface DayState {
    data object Done : DayState
    data object Today : DayState
    data object Rest : DayState
    data class Upcoming(val type: RunType) : DayState
}
data class Day(val weekday: String, val date: Int, val state: DayState)

@Composable
fun WeekStrip(days: List<Day>, modifier: Modifier = Modifier) {
    Row(modifier.fillMaxWidth(), horizontalArrangement = Arrangement.spacedBy(7.dp)) {
        days.forEachIndexed { i, d ->
            val a = remember { Animatable(0f) }
            LaunchedEffect(Unit) { a.animateTo(1f, tween(220, delayMillis = i * 40, easing = EaseOutCubic)) }
            DayCell(d, Modifier.weight(1f).alpha(a.value))
        }
    }
}

@Composable
private fun DayCell(d: Day, modifier: Modifier) {
    val today = d.state is DayState.Today
    val dateColor = when (d.state) {
        is DayState.Done -> RunColors.RtEasy
        is DayState.Today -> Color.White
        is DayState.Rest -> RunColors.TextTertiary
        is DayState.Upcoming -> RunColors.TextPrimary
    }
    val dotColor = when (val s = d.state) {
        is DayState.Done -> RunColors.RtEasy
        is DayState.Today -> RunColors.Lime
        is DayState.Rest -> RunColors.Divider
        is DayState.Upcoming -> s.type.color
    }
    val bg = when (d.state) {
        is DayState.Done -> RunColors.RtEasy.copy(alpha = 0.12f)
        is DayState.Today -> RunColors.Indigo
        else -> RunColors.Surface1
    }
    val border = when (d.state) {
        is DayState.Done -> RunColors.RtEasy.copy(alpha = 0.35f)
        is DayState.Today -> RunColors.IndigoBright
        else -> RunColors.Divider
    }

    Column(
        modifier
            .aspectRatio(0.62f)
            .clip(RoundedCornerShape(14.dp))
            .background(bg)
            .border(1.dp, border, RoundedCornerShape(14.dp))
            .padding(vertical = 9.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Text(d.weekday, style = RunText.Tab.copy(fontWeight = androidx.compose.ui.text.font.FontWeight.Bold),
            color = if (today) Color.White else RunColors.TextTertiary)
        Spacer(Modifier.weight(1f))
        Text("${d.date}", style = RunText.DayNum, color = dateColor)
        Box(Modifier.padding(top = 6.dp).size(7.dp).clip(CircleShape).background(dotColor))
    }
}
```

### Guided Run-Session Card

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.Icon
import androidx.compose.ui.draw.*
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.text.style.TextAlign

@Composable
fun SessionCard(
    tag: String, title: String, distance: String, duration: String, pace: String,
    onStart: () -> Unit, modifier: Modifier = Modifier,
) {
    val appear = remember { Animatable(0.97f) }
    LaunchedEffect(Unit) { appear.animateTo(1f, tween(300, easing = EaseOutCubic)) }
    val ix = remember { MutableInteractionSource() }
    val pressed by ix.collectIsPressedAsState()

    Box(
        modifier
            .fillMaxWidth()
            .scale(appear.value)
            .alpha(((appear.value - 0.97f) / 0.03f).coerceIn(0f, 1f))
            .shadow(28.dp, RoundedCornerShape(22.dp), spotColor = RunColors.Indigo.copy(alpha = 0.30f))
            .clip(RoundedCornerShape(22.dp))
            .background(Brush.linearGradient(listOf(RunColors.Indigo, Color(0xFF3A33B8))))
            .padding(18.dp),
    ) {
        Box(
            Modifier.align(Alignment.BottomEnd).offset(x = 34.dp, y = 34.dp)
                .size(130.dp).clip(CircleShape).background(RunColors.Lime.copy(alpha = 0.14f))
        )
        Column {
            Box(Modifier.clip(RoundedCornerShape(999.dp)).background(RunColors.Lime)
                .padding(horizontal = 10.dp, vertical = 4.dp)) {
                Text(tag.uppercase(), style = RunText.Eyebrow.copy(fontSize = 10.sp), color = RunColors.TextOnLime)
            }
            Text(title, style = RunText.SessionTitle, color = Color.White, modifier = Modifier.padding(top = 12.dp))
            Row(Modifier.padding(top = 12.dp), horizontalArrangement = Arrangement.spacedBy(18.dp)) {
                Stat(distance, "Distance"); Stat(duration, "Duration"); Stat(pace, "Avg pace")
            }
            Row(
                Modifier.padding(top = 16.dp).fillMaxWidth()
                    .clip(RoundedCornerShape(14.dp))
                    .background(if (pressed) RunColors.LimePressed else RunColors.Lime)
                    .clickable(ix, indication = null, onClick = onStart)
                    .padding(vertical = 13.dp),
                horizontalArrangement = Arrangement.Center,
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Icon(Icons.Filled.PlayArrow, null, tint = RunColors.TextOnLime, modifier = Modifier.size(16.dp))
                Spacer(Modifier.width(8.dp))
                Text("Start guided run", style = RunText.Button, color = RunColors.TextOnLime)
            }
        }
    }
}

@Composable
private fun Stat(v: String, l: String) {
    Column {
        Text(v, style = RunText.StatValue, color = Color.White)
        Text(l.uppercase(), style = RunText.Tab.copy(fontSize = 10.sp), color = Color.White.copy(alpha = 0.7f))
    }
}
```

### Workout-Structure Breakdown

```kotlin
data class WorkoutStep(val code: String, val title: String, val detail: String, val pace: String, val color: Color, val flex: Float)

@Composable
fun StructureBreakdown(steps: List<WorkoutStep>, modifier: Modifier = Modifier) {
    val grow = remember { Animatable(0f) }
    LaunchedEffect(Unit) { grow.animateTo(1f, tween(500, easing = EaseOutCubic)) }

    Column(modifier.fillMaxWidth()) {
        Text("Workout structure", style = RunText.CardTitle.copy(fontSize = 13.sp),
            color = RunColors.TextPrimary, modifier = Modifier.padding(bottom = 10.dp))

        Row(Modifier.fillMaxWidth().height(30.dp).clip(RoundedCornerShape(8.dp)),
            horizontalArrangement = Arrangement.spacedBy(3.dp)) {
            steps.forEach { s ->
                Box(Modifier.weight((s.flex * grow.value).coerceAtLeast(0.0001f)).fillMaxHeight().background(s.color))
            }
        }

        steps.forEachIndexed { i, s ->
            Row(
                Modifier.fillMaxWidth().padding(vertical = 10.dp)
                    .then(if (i < steps.lastIndex) Modifier.drawBehind {
                        drawLine(RunColors.Divider, androidx.compose.ui.geometry.Offset(0f, size.height),
                            androidx.compose.ui.geometry.Offset(size.width, size.height), 1f)
                    } else Modifier),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(12.dp),
            ) {
                Box(Modifier.size(32.dp).clip(RoundedCornerShape(9.dp)).background(s.color),
                    contentAlignment = Alignment.Center) {
                    Text(s.code, style = RunText.Eyebrow.copy(fontSize = 11.sp), color = RunColors.TextOnLime)
                }
                Column(Modifier.weight(1f)) {
                    Text(s.title, style = RunText.StepItem.copy(fontWeight = androidx.compose.ui.text.font.FontWeight.Bold), color = RunColors.TextPrimary)
                    Text(s.detail, style = RunText.Pace.copy(fontWeight = androidx.compose.ui.text.font.FontWeight.Medium, letterSpacing = 0.sp), color = RunColors.TextSecondary)
                }
                Column(horizontalAlignment = Alignment.End) {
                    Text(s.pace, style = RunText.StatValue.copy(fontSize = 13.sp), color = RunColors.TextPrimary)
                    Text("/km", style = RunText.Tab.copy(fontSize = 10.sp), color = RunColors.TextTertiary)
                }
            }
        }
    }
}
```

### Primary "Go" Button

```kotlin
@Composable
fun RunGoButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val ix = remember { MutableInteractionSource() }
    val pressed by ix.collectIsPressedAsState()
    Row(
        modifier
            .fillMaxWidth()
            .scale(if (pressed) 0.98f else 1f)
            .clip(RoundedCornerShape(14.dp))
            .background(if (pressed) RunColors.LimePressed else RunColors.Lime)
            .clickable(ix, indication = null, onClick = onClick)
            .padding(vertical = 15.dp),
        horizontalArrangement = Arrangement.Center,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(Icons.Filled.PlayArrow, null, tint = RunColors.TextOnLime, modifier = Modifier.size(16.dp))
        Spacer(Modifier.width(8.dp))
        Text(title, style = RunText.Button, color = RunColors.TextOnLime)
    }
}
```

## 4. Navigation

Runna has a 5-tab bottom strip and no opaque top bar (the plan header lives in scroll content). Active state is just the Lime color — **no Material tint pill**.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun RunBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = RunColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Plan"     to Icons.Filled.CalendarMonth,
            "Run"      to Icons.Filled.DirectionsRun,
            "Progress" to Icons.Filled.TrendingUp,
            "Club"     to Icons.Filled.Groups,
            "Profile"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, Modifier.size(22.dp)) },
                label = { Text(label, style = RunText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = RunColors.Lime,    // Lime, no pill
                    selectedTextColor = RunColors.Lime,
                    unselectedIconColor = RunColors.TextTertiary,
                    unselectedTextColor = RunColors.TextTertiary,
                    indicatorColor = Color.Transparent,    // Runna has no tint pill
                ),
            )
        }
    }
}
```

The plan header is content, not a `TopAppBar`: an eyebrow ("5K PLAN · WEEK 4 OF 8", `RunText.Eyebrow` in `Lime`, 1.2sp tracking, uppercase), a plan title ("Build week", `RunText.PlanTitle`), a meta line, and a trailing "Week 4 ▾" pill (`Surface2`, `Divider` border, 999.dp) that opens a `ModalBottomSheet` week picker.

## 5. Motion

Runna motion is energetic but disciplined — 200–500ms ease-out. Lime presses give a soft haptic; km splits a medium one.

| Moment | Compose recipe |
|--------|----------------|
| Week strip load | each cell `Animatable` alpha 0→1 `tween(220, delayMillis = i*40, EaseOutCubic)` |
| Today cell pulse | one-shot `Animatable` scale `1 → 1.04 → 1` via `animateTo` sequence |
| Session card entrance | `Animatable` 0.97→1 `tween(300, EaseOutCubic)` + alpha fade |
| Structure bar grow | each segment `weight` driven by an `Animatable` 0→1 `tween(500, EaseOutCubic)` |
| Start run | Lime press scale 0.98 + `HapticFeedbackType.LongPress`, then slide-up to live run (Nav transition `tween(350)`) |
| Live run km | `HapticFeedbackType.LongPress` (medium) on each km; pace counts via `animateIntAsState` |
| Step complete (live) | row Lime sweep `clipRect` left→right `tween(300)` + check |
| Tab switch | `Crossfade(tween(200))` |
| PR celebration | badge `spring(dampingRatio = 0.6f, stiffness = 140f)` + `HapticFeedbackType.Confirm` |
| Segmented control | indicator `animateDpAsState(tween(220, EaseOutCubic))` |

```kotlin
// Canonical week-strip stagger
val a = remember { Animatable(0f) }
LaunchedEffect(Unit) { a.animateTo(1f, tween(220, delayMillis = i * 40, easing = EaseOutCubic)) }
// Modifier.alpha(a.value)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft Start press and km splits; `HapticFeedbackType.Confirm` for a PR. Auto-sync of a completed run is silent — show a snackbar only on failure.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Plan (tab) | `calendar` | `Icons.Filled.CalendarMonth` |
| Run (tab) | `figure.run` | `Icons.Filled.DirectionsRun` |
| Progress (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Club (tab) | `person.2` / `.fill` | `Icons.Filled.Groups` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Start run | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause / stop | `pause.fill` / `stop.fill` | `Icons.Filled.Pause` / `Icons.Filled.Stop` |
| Week switcher | `chevron.down` | `Icons.Filled.ExpandMore` |
| Completed step | `checkmark` | `Icons.Filled.Check` |
| PR / achievement | `trophy.fill` / `bolt.fill` | `Icons.Filled.EmojiEvents` / `Icons.Filled.Bolt` |
| Pace / speed | `speedometer` | `Icons.Filled.Speed` |
| Route map | `map` | `Icons.Filled.Map` |
| Kudos | `hand.thumbsup.fill` | `Icons.Filled.ThumbUp` |
| Back / close | `chevron.left` / `xmark` | `Icons.AutoMirrored.Filled.ArrowBack` / `Icons.Filled.Close` |
| Settings | `gearshape` | `Icons.Filled.Settings` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `Canvas` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; dark-first wants light-content system bars (dark-content on the optional light theme). The plan header respects the status-bar inset; add bottom content padding for the `NavigationBar` + insets; the run-setup sheet uses `Modifier.imePadding()`.
- **Tabular numerals**: apply a tabular-figures `FontFeatureSetting` (or a Sora tabular variant) to the live clock/pace/distance so the run screen doesn't shift.
- **Run-type accessibility**: never color-only — set `Modifier.semantics { contentDescription = "Friday 16, today, intervals" }` on week cells and include the run-type name in step descriptions; expose the structure bar as an ordered list with pace targets.
- **TalkBack**: announce the session card as "Today, intervals: 6 by 800 meters at 5K pace, 8.4 km, about 48 minutes, average pace 4:52"; the live run speaks periodic pace/distance.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen/plan/session titles, section, card title, body, step item; pin layout-sensitive text (eyebrow, pace/tag, day-cell number, stat labels, 10sp tab) via `dp`-derived sizing. Cap session title scale so the card doesn't overflow.
- **Touch targets**: Material guidance is 48.dp — day cells full-cell tappable ≥ 44.dp; Lime Start ≥ 48.dp tall; tab icons 22.dp with 48.dp hit; week-switcher pill ≥ 36.dp.
- **Contrast**: `#F3F3FB` on `#0E0E16`/`#181826` passes WCAG AA; near-black `#1A1A1A` on Lime `#C2F94E` passes AA strongly; white on the Indigo gradient passes AA at 18sp weight 700 — if Dynamic Type shrinks it, keep weight ≥ Bold.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, render week strip & structure bar at final state (no stagger/grow), substitute a `Crossfade`, drop the today-cell pulse, and show a static PR badge — keep the run-type colors (they convey state).
- **Dark mode**: dark-first via `RunDark` (Indigo-tinted `#0E0E16`, NOT neutral). The optional `RunLight` uses `#F6F6FB`. **Runna Indigo `#4F46E5`, Lime `#C2F94E`, and the run-type system are identical in both themes** (athletes read effort/state by color); Lime always pairs with near-black text. Do **not** enable Material You `dynamicColorScheme()` — Runna's two-color brand identity must hold regardless of wallpaper.
- **Wear OS (companion)**: if shipping a Wear module, mirror pace/distance/current step + Lime progress with large tabular numerals and maximal contrast for mid-run glance-ability.
