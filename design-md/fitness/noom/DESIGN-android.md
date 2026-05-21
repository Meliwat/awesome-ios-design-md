# Noom (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Noom's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the signature lesson card + food log + weight graph as `@Composable`s, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This keeps the *visual* identity (the blue→teal lesson card, the sacred green/yellow/red food-color system, fully pill-shaped buttons, the encouraging coach voice) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas`/`drawPath` instead of SwiftUI `Path`, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. No color extraction — Noom's palette (and especially the food-color system) is a fixed, learned set, so Palette is not used. Noom is **light-first**; a dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/NoomColors.kt
import androidx.compose.ui.graphics.Color

object NoomColors {
    // Brand
    val Blue        = Color(0xFF2A5DF6)
    val BluePressed = Color(0xFF1E47C8)
    val Teal        = Color(0xFF1FC29B)
    val Navy        = Color(0xFF0C1B4D)

    // Canvas & Surfaces (Light)
    val Canvas      = Color(0xFFFBFBFD)
    val Surface     = Color(0xFFFFFFFF)
    val SurfaceSub  = Color(0xFFF2F3F7)
    val DividerLt   = Color(0xFFE6E7EC)

    // Canvas & Surfaces (Dark)
    val DarkCanvas  = Color(0xFF121212)
    val DarkSurf1   = Color(0xFF1C1C1E)
    val DarkSurf2   = Color(0xFF262629)
    val DarkDivider = Color(0xFF2E2E31)

    // Text
    val TextPriLt = Color(0xFF1A1A1F)
    val TextSecLt = Color(0xFF6A6A70)
    val TextPriDk = Color(0xFFF0F0F2)
    val TextSecDk = Color(0xFF9A9AA0)

    // Food-color system (FIXED both modes)
    val FoodGreen  = Color(0xFF34C759)
    val FoodYellow = Color(0xFFFFC531)
    val FoodRed    = Color(0xFFFF5A52)

    // Coach
    val CoachPurple = Color(0xFF7B61FF)

    // Semantic
    val Success = Color(0xFF34C759)
    val Error   = Color(0xFFFF5A52)
    val Flame   = Color(0xFFFF8A3D)
}

enum class FoodClass(val color: Color, val tintAlpha: Float, val label: String) {
    Green(NoomColors.FoodGreen, 0.16f, "Green"),
    Yellow(NoomColors.FoodYellow, 0.16f, "Yellow"),
    Red(NoomColors.FoodRed, 0.16f, "Red"),
}
```

Wire it into both schemes. Noom is light-first and cheerful; the dark scheme keeps brand energy.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val NoomLight = lightColorScheme(
    primary        = NoomColors.Blue,
    onPrimary      = Color(0xFFFFFFFF),
    background     = NoomColors.Canvas,
    onBackground   = NoomColors.TextPriLt,
    surface        = NoomColors.Surface,
    onSurface      = NoomColors.TextPriLt,
    surfaceVariant = NoomColors.SurfaceSub,
    outline        = NoomColors.DividerLt,
    secondary      = NoomColors.Teal,
    tertiary       = NoomColors.CoachPurple,
    error          = NoomColors.Error,
)

private val NoomDark = darkColorScheme(
    primary        = NoomColors.Blue,
    onPrimary      = Color(0xFFFFFFFF),
    background     = NoomColors.DarkCanvas,
    onBackground   = NoomColors.TextPriDk,
    surface        = NoomColors.DarkSurf1,
    onSurface      = NoomColors.TextPriDk,
    surfaceVariant = NoomColors.DarkSurf2,
    outline        = NoomColors.DarkDivider,
    secondary      = NoomColors.Teal,
    tertiary       = NoomColors.CoachPurple,
    error          = NoomColors.Error,
)

@Composable
fun NoomTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) NoomDark else NoomLight,
    typography = NoomTypography,
    content = content,
)
```

## 2. Typography (M3)

Noom uses **Poppins** (Google Fonts, SIL OFL). Drop the TTFs in `res/font/`. Heavy numerals, conversational body.

```kotlin
// ui/theme/NoomType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Poppins = FontFamily(
    Font(R.font.poppins_regular,   FontWeight.Normal),
    Font(R.font.poppins_medium,    FontWeight.Medium),
    Font(R.font.poppins_semibold,  FontWeight.SemiBold),
    Font(R.font.poppins_bold,      FontWeight.Bold),
    Font(R.font.poppins_extrabold, FontWeight.ExtraBold),
)

object NoomText {
    val ScreenTitle = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val Greeting    = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val HeroStat    = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 24.sp, lineHeight = 27.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val Body        = TextStyle(Poppins, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val ListItem    = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 21.sp)
    val Meta        = TextStyle(Poppins, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Eyebrow     = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 1.0.sp)
    val Button      = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.1.sp)
    val Pill        = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
    val Axis        = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 9.sp,  lineHeight = 9.sp)
    val Tab         = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

val NoomTypography = Typography(
    headlineLarge = NoomText.ScreenTitle,
    headlineMedium = NoomText.Section,
    titleMedium   = NoomText.CardTitle,
    bodyMedium    = NoomText.Body,
    labelSmall    = NoomText.Tab,
)
```

## 3. Signature Components

### Daily Psychology Lesson Card

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.automirrored.filled.ArrowForward
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.*
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun LessonCard(
    eyebrow: String, title: String, step: Int, total: Int,
    onContinue: () -> Unit, modifier: Modifier = Modifier,
) {
    val appear = remember { Animatable(0.96f) }
    LaunchedEffect(Unit) { appear.animateTo(1f, tween(320, easing = EaseOutCubic)) }

    Box(
        modifier
            .fillMaxWidth()
            .scale(appear.value)
            .alpha((appear.value - 0.96f) / 0.04f)
            .shadow(26.dp, RoundedCornerShape(20.dp), spotColor = NoomColors.Blue.copy(alpha = 0.22f))
            .clip(RoundedCornerShape(20.dp))
            .background(Brush.linearGradient(listOf(NoomColors.Blue, NoomColors.Teal)))
            .padding(18.dp),
    ) {
        // decorative translucent circle off the top-right
        Box(
            Modifier
                .align(Alignment.TopEnd)
                .offset(x = 30.dp, y = (-30).dp)
                .size(110.dp)
                .clip(androidx.compose.foundation.shape.CircleShape)
                .background(Color.White.copy(alpha = 0.10f))
        )
        Column {
            Text(eyebrow, style = NoomText.Eyebrow, color = Color.White.copy(alpha = 0.85f))
            Text(title, style = NoomText.CardTitle, color = Color.White,
                modifier = Modifier.padding(top = 8.dp).fillMaxWidth(0.8f))

            Row(Modifier.padding(top = 14.dp), verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                Box(Modifier.weight(1f).height(6.dp).clip(RoundedCornerShape(3.dp))
                    .background(Color.White.copy(alpha = 0.25f))) {
                    Box(Modifier.fillMaxHeight().fillMaxWidth(step.toFloat() / total)
                        .clip(RoundedCornerShape(3.dp)).background(Color.White))
                }
                Text("$step of $total", style = NoomText.Pill, color = Color.White)
            }

            Row(
                Modifier.padding(top = 14.dp)
                    .clip(RoundedCornerShape(999.dp))
                    .background(Color.White)
                    .clickable(onClick = onContinue)
                    .padding(horizontal = 16.dp, vertical = 9.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(6.dp),
            ) {
                Text("Continue lesson", style = NoomText.Pill.copy(fontSize = 13.sp), color = NoomColors.Blue)
                Icon(Icons.AutoMirrored.Filled.ArrowForward, null, tint = NoomColors.Blue, modifier = Modifier.size(13.dp))
            }
        }
    }
}
```

### Food Log Row + Stacked Ratio Bar

```kotlin
import androidx.compose.foundation.shape.CircleShape

@Composable
fun FoodRatioBar(green: Float, yellow: Float, red: Float, modifier: Modifier = Modifier) {
    val sum = (green + yellow + red).takeIf { it > 0f } ?: 1f
    Row(modifier.fillMaxWidth().height(10.dp).clip(RoundedCornerShape(5.dp))) {
        Box(Modifier.weight(green / sum).fillMaxHeight().background(NoomColors.FoodGreen))
        Box(Modifier.weight(yellow / sum).fillMaxHeight().background(NoomColors.FoodYellow))
        Box(Modifier.weight(red / sum).fillMaxHeight().background(NoomColors.FoodRed))
    }
}

@Composable
fun FoodRow(name: String, cls: FoodClass, kcal: Int, modifier: Modifier = Modifier) {
    Row(
        modifier
            .fillMaxWidth()
            .padding(vertical = 9.dp)
            .drawBehind {
                drawLine(NoomColors.DividerLt, androidx.compose.ui.geometry.Offset(0f, size.height),
                    androidx.compose.ui.geometry.Offset(size.width, size.height), 1f)
            },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(10.dp).clip(CircleShape).background(cls.color))
        Text(name, style = NoomText.ListItem, color = NoomColors.TextPriLt, modifier = Modifier.weight(1f))
        Box(
            Modifier.clip(RoundedCornerShape(999.dp))
                .background(cls.color.copy(alpha = cls.tintAlpha))
                .padding(horizontal = 9.dp, vertical = 3.dp)
        ) { Text(cls.label, style = NoomText.Pill, color = cls.color) }
        Text("$kcal", style = NoomText.Pill.copy(fontSize = 13.sp), color = NoomColors.TextSecLt,
            modifier = Modifier.width(52.dp), textAlign = androidx.compose.ui.text.style.TextAlign.End)
    }
}
```

### Weight Graph Card

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.*
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.graphics.drawscope.clipRect

/** points: normalized x:0..1, y:0..1 (0=top). goalY normalized 0..1 */
@Composable
fun WeightGraphCard(points: List<Offset>, goalY: Float, modifier: Modifier = Modifier) {
    val progress = remember { Animatable(0f) }
    LaunchedEffect(Unit) { progress.animateTo(1f, tween(700, easing = EaseOutCubic)) }

    Column(
        modifier
            .fillMaxWidth()
            .shadow(18.dp, RoundedCornerShape(18.dp), spotColor = NoomColors.Navy.copy(alpha = 0.06f))
            .clip(RoundedCornerShape(18.dp))
            .background(NoomColors.Surface)
            .border(1.dp, NoomColors.DividerLt, RoundedCornerShape(18.dp))
            .padding(16.dp),
    ) {
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
            Text("Weight", style = NoomText.CardTitle.copy(fontSize = 14.sp), color = NoomColors.TextPriLt)
            Text("Last 30 days", style = NoomText.Tab.copy(fontSize = 11.sp), color = NoomColors.TextSecLt)
        }
        Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(8.dp),
            modifier = Modifier.padding(top = 4.dp)) {
            Text("164.2 lb", style = NoomText.HeroStat, color = NoomColors.TextPriLt)
            Text("▼ 6.8 lb", style = NoomText.Tab.copy(fontSize = 13.sp, fontWeight = FontWeight.SemiBold),
                color = NoomColors.Teal)
        }

        Canvas(Modifier.fillMaxWidth().height(90.dp).padding(top = 6.dp)) {
            val w = size.width; val h = size.height
            fun pt(o: Offset) = Offset(o.x * w, o.y * h)
            val line = Path().apply {
                moveTo(pt(points.first()).x, pt(points.first()).y)
                points.drop(1).forEach { lineTo(pt(it).x, pt(it).y) }
            }
            val fill = Path().apply {
                addPath(line); lineTo(w, h); lineTo(0f, h); close()
            }
            drawPath(fill, Brush.verticalGradient(
                0f to NoomColors.Blue.copy(alpha = 0.30f),
                1f to NoomColors.Blue.copy(alpha = 0.02f)), alpha = progress.value)
            // dashed teal goal line
            drawLine(NoomColors.Teal, Offset(4f, goalY * h), Offset(w - 4f, goalY * h), 1.5f,
                pathEffect = PathEffect.dashPathEffect(floatArrayOf(3f, 4f)), alpha = progress.value)
            // blue trend, clip-revealed left→right
            clipRect(right = w * progress.value) {
                drawPath(line, NoomColors.Blue, style = Stroke(width = 2.5.dp.toPx(), cap = StrokeCap.Round))
            }
        }

        Row(Modifier.fillMaxWidth().padding(top = 6.dp), horizontalArrangement = Arrangement.SpaceBetween) {
            listOf("Apr 14", "Apr 28", "May 12").forEach { Text(it, style = NoomText.Axis, color = NoomColors.TextSecLt) }
        }
    }
}
```

### Primary Button (Pill)

```kotlin
@Composable
fun NoomPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        modifier
            .fillMaxWidth()
            .scale(if (pressed) 0.98f else 1f)
            .clip(RoundedCornerShape(999.dp))
            .background(if (pressed) NoomColors.BluePressed else NoomColors.Blue)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(vertical = 15.dp, horizontal = 30.dp),
        contentAlignment = Alignment.Center,
    ) { Text(title, style = NoomText.Button, color = Color.White) }
}
```

## 4. Navigation

Noom has a 5-tab bottom strip and no opaque top bar (the greeting lives in scroll content). Active state is just the Noom Blue color — **no Material tint pill**.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun NoomBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = NoomColors.Surface, tonalElevation = 0.dp) {
        val items = listOf(
            "Today"   to Icons.Filled.Home,
            "Learn"   to Icons.Filled.MenuBook,
            "Log"     to Icons.Filled.AddCircle,
            "Coach"   to Icons.Filled.ChatBubble,
            "Profile" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, Modifier.size(22.dp)) },
                label = { Text(label, style = NoomText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = NoomColors.Blue,   // Noom Blue, no pill
                    selectedTextColor = NoomColors.Blue,
                    unselectedIconColor = NoomColors.TextSecDk,
                    unselectedTextColor = NoomColors.TextSecDk,
                    indicatorColor = Color.Transparent,    // Noom has no tint pill
                ),
            )
        }
    }
}
```

The screen header is content, not a `TopAppBar`: a greeting ("Good morning, Maya", `NoomText.Greeting`) and a momentum meta line ("Day 24 · You're building momentum", `NoomText.Meta` in `TextSecLt`). The coach surface uses `CoachPurple` accents and a `ModalBottomSheet` composer with a purple send button.

## 5. Motion

Noom motion is warm and encouraging — 200–700ms ease-out. The lesson card scales in; the graph draws; the food bar re-animates on add.

| Moment | Compose recipe |
|--------|----------------|
| Lesson card entrance | `Animatable` 0.96→1 `tween(320, EaseOutCubic)` + alpha fade |
| Weight graph draw-in | `clipRect(right = w * progress)` reveal + fill `alpha = progress`, `tween(700, EaseOutCubic)` |
| Food ratio bar update | animate each segment `weight` via `animateFloatAsState(tween(400))` when a food is added |
| New food row | `AnimatedVisibility` `slideInVertically { -it } + fadeIn` `tween(250)` |
| Segmented control | indicator `animateDpAsState(tween(220, EaseOutCubic))` |
| Lesson complete | progress bar width `animateFloatAsState(tween(400))` + `HapticFeedbackType.Confirm` |
| Tab switch | `Crossfade(tween(200))` between destinations |
| Coach typing | 3 dots `infiniteRepeatable(tween(600), RepeatMode.Reverse)` scale 0.6↔1 |

```kotlin
// Canonical lesson entrance
val appear = remember { Animatable(0.96f) }
LaunchedEffect(Unit) { appear.animateTo(1f, tween(320, easing = EaseOutCubic)) }
// Modifier.scale(appear.value).alpha((appear.value - 0.96f) / 0.04f)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.Confirm)` on lesson completion and goal reached; `HapticFeedbackType.LongPress` (soft) on toggle. Auto-save of a logged food is silent — show a brief snackbar only on failure.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Today (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Learn (tab) | `book` / `book.fill` | `Icons.Filled.MenuBook` |
| Log (tab) | `plus.circle` / `.fill` | `Icons.Filled.AddCircle` |
| Coach (tab) | `bubble.left` / `.fill` | `Icons.Filled.ChatBubble` |
| Profile (tab) | `person.crop.circle` / `.fill` | `Icons.Filled.AccountCircle` |
| Lesson CTA arrow | `arrow.right` | `Icons.AutoMirrored.Filled.ArrowForward` |
| Weight delta down | `arrow.down` | `Icons.Filled.ArrowDownward` |
| Search / scan | `magnifyingglass` / `barcode.viewfinder` | `Icons.Filled.Search` / `Icons.Filled.QrCodeScanner` |
| Coach send | `arrow.up.circle.fill` | `Icons.AutoMirrored.Filled.Send` |
| Streak | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Add food | `plus` | `Icons.Filled.Add` |
| Back / close | `chevron.left` / `xmark` | `Icons.AutoMirrored.Filled.ArrowBack` / `Icons.Filled.Close` |
| Goal reached | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Settings | `gearshape` | `Icons.Filled.Settings` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas` paths + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; light-first wants dark-content system bars (light-content in dark mode). The greeting respects the status-bar inset; add bottom content padding for the `NavigationBar` + system insets; the coach composer uses `Modifier.imePadding()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen title, greeting, hero stat, section, card title, body, list item; pin layout-sensitive text (eyebrow, food pill, 9sp axis, 10sp tab) via `dp`-derived sizing. Cap the lesson title scale so the card doesn't overflow.
- **Food-color accessibility is critical**: never rely on color alone — the dot is always paired with the text label ("Green"/"Yellow"/"Red"). Set `Modifier.semantics { contentDescription = "$name, ${cls.label} food, $kcal calories" }` on each row; expose the ratio bar as "58 percent green, 30 percent yellow, 12 percent red".
- **TalkBack**: announce the weight graph as a single element summarizing the trend ("Down 6.8 pounds over 30 days, currently 164.2") instead of the path; the lesson card reads eyebrow + title + "step 2 of 5".
- **Touch targets**: Material guidance is 48.dp — pill buttons ≥ 48.dp tall; food rows full-row tappable, ≥ 44.dp; tab icons 22.dp with 48.dp hit; coach send button 40.dp.
- **Contrast**: `#1A1A1F` on `#FBFBFD` and `#F0F0F2` on `#121212` pass WCAG AA; white text on the blue→teal gradient passes AA at 18sp weight 700 — if Dynamic Type shrinks it, keep weight ≥ SemiBold; the Yellow food pill needs a darker text token on its light tint to stay AA.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, render the lesson card and graph at final state (no scale-in/draw), substitute a `Crossfade`, and disable the coach typing pulse.
- **Dark mode**: invert via `NoomDark` — `#121212` canvas, `#1C1C1E` surface; **Noom Blue `#2A5DF6` and the food-color hues (`#34C759` / `#FFC531` / `#FF5A52`) are identical in both modes** (users read food by color). Do **not** enable Material You `dynamicColorScheme()` — Noom's blue/teal brand and the learned food-color system must hold regardless of wallpaper.
