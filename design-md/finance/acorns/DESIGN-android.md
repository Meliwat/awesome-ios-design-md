# Acorns (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Acorns' visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the allocation donut + Round-Ups card + Found Money feed, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Oak purple + Acorn Green, the allocation donut, the Round-Ups gradient card, friendly heavy rounded type) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Canvas` arc donut, `LazyColumn` for the dashboard, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` if you load brand logos. Do **not** enable Material You `dynamicColorScheme()` — Oak + Acorn Green are the brand and must hold regardless of wallpaper. A full dark scheme (deep aubergine) is provided.

## 1. Color Tokens

```kotlin
// ui/theme/AcornColors.kt
import androidx.compose.ui.graphics.Color

object AcornColors {
    // Growth / action
    val Green        = Color(0xFF6FBF4E)
    val GreenBright  = Color(0xFF84D962)
    val GreenPressed = Color(0xFF5AA53D)
    val ForestInk    = Color(0xFF14302A) // text on green

    // Brand (Oak)
    val Oak        = Color(0xFF5A2B82)
    val OakBright  = Color(0xFF7B43A8)
    val OakPressed = Color(0xFF47206A)
    val OakSoft    = Color(0xFFC8AEDF)

    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFF5F3F8)
    val Surface        = Color(0xFFFFFFFF)
    val SurfacePressed = Color(0xFFEDE9F2)
    val Divider        = Color(0xFFE5E1EC)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF14121A) // deep aubergine — NOT pure black
    val DarkSurface1 = Color(0xFF1E1B27)
    val DarkSurface2 = Color(0xFF292533)
    val DarkDivider  = Color(0xFF383143)

    // Text
    val TextPrimary       = Color(0xFF1F1A2B)
    val TextSecondary     = Color(0xFF6E6480)
    val TextTertiary      = Color(0xFF9C92AD)
    val DarkTextPrimary   = Color(0xFFF2EFF6)
    val DarkTextSecondary = Color(0xFFADA4BC)
    val DarkTextTertiary  = Color(0xFF756B85)

    // Allocation / chart palette (fixed slice → asset map)
    val SliceLarge = Color(0xFF5A2B82) // Oak
    val SliceSmall = Color(0xFF6FBF4E) // Green
    val SliceIntl  = Color(0xFF45C2B0) // Teal
    val SliceREIT  = Color(0xFFF2C84B) // Gold
    val SliceBonds = Color(0xFFF2785C) // Coral
    val SliceEM    = Color(0xFF9B6FD4) // Lilac

    // Semantic
    val GainLight   = Color(0xFF6FBF4E); val Gain   = Color(0xFF84D962)
    val LossLight   = Color(0xFFE0584F); val Loss   = Color(0xFFFF6B6B)
    val WarningLight = Color(0xFFE0A52A); val Warning = Color(0xFFF2C84B)
}
```

Wire it into both schemes. Acorns is light-first and friendly; the dark scheme uses `#14121A` deep aubergine, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val AcornLight = lightColorScheme(
    primary        = AcornColors.Green,
    onPrimary      = AcornColors.ForestInk, // dark forest text on bright green (AA)
    secondary      = AcornColors.Oak,
    onSecondary    = Color.White,
    background     = AcornColors.Canvas,
    onBackground   = AcornColors.TextPrimary,
    surface        = AcornColors.Surface,
    onSurface      = AcornColors.TextPrimary,
    surfaceVariant = AcornColors.SurfacePressed,
    outline        = AcornColors.Divider,
    error          = AcornColors.LossLight,
)

private val AcornDark = darkColorScheme(
    primary        = AcornColors.Green,
    onPrimary      = AcornColors.ForestInk,
    secondary      = AcornColors.OakSoft,
    onSecondary    = AcornColors.DarkCanvas,
    background     = AcornColors.DarkCanvas,
    onBackground   = AcornColors.DarkTextPrimary,
    surface        = AcornColors.DarkSurface1,
    onSurface      = AcornColors.DarkTextPrimary,
    surfaceVariant = AcornColors.DarkSurface2,
    outline        = AcornColors.DarkDivider,
    error          = AcornColors.Loss,
)

@Composable
fun AcornTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) AcornDark else AcornLight,
    typography = AcornTypography,
    content = content,
)
```

## 2. Typography

Acorns ships a rounded brand face; bundle **Nunito Sans** (SIL OFL) as the free substitute in `res/font/`. Currency uses tabular figures via a font feature setting. Body 400; headline numbers/titles 800–900.

```kotlin
// ui/theme/AcornType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val NunitoSans = FontFamily(
    Font(R.font.nunitosans_regular,   FontWeight.Normal),
    Font(R.font.nunitosans_semibold,  FontWeight.SemiBold),
    Font(R.font.nunitosans_bold,      FontWeight.Bold),
    Font(R.font.nunitosans_extrabold, FontWeight.ExtraBold),
    Font(R.font.nunitosans_black,     FontWeight.Black),
)

object AcornText {
    val Portfolio  = TextStyle(NunitoSans, fontWeight = FontWeight.Black,     fontSize = 38.sp, lineHeight = 40.sp, letterSpacing = (-1).sp, fontFeatureSettings = "tnum")
    val HeroNumber = TextStyle(NunitoSans, fontWeight = FontWeight.Black,     fontSize = 28.sp, lineHeight = 31.sp, letterSpacing = (-0.5).sp, fontFeatureSettings = "tnum")
    val Section    = TextStyle(NunitoSans, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Subsection = TextStyle(NunitoSans, fontWeight = FontWeight.ExtraBold, fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Body       = TextStyle(NunitoSans, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 23.sp)
    val RowTitle   = TextStyle(NunitoSans, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val Amount     = TextStyle(NunitoSans, fontWeight = FontWeight.ExtraBold, fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp, fontFeatureSettings = "tnum")
    val Meta       = TextStyle(NunitoSans, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 18.sp)
    val Label      = TextStyle(NunitoSans, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.4.sp)
    val Button     = TextStyle(NunitoSans, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 16.sp)
    val Tab        = TextStyle(NunitoSans, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Delta      = TextStyle(NunitoSans, fontWeight = FontWeight.Bold,      fontSize = 14.sp, lineHeight = 17.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val AcornTypography = Typography(
    headlineLarge = AcornText.Portfolio,
    headlineMedium = AcornText.Section,
    titleMedium   = AcornText.Subsection,
    bodyMedium    = AcornText.Body,
    labelSmall    = AcornText.Tab,
)
```

## 3. Signature Components

### Portfolio Header

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

@Composable
fun AcornPortfolioHeader(total: String, gain: String, modifier: Modifier = Modifier) {
    Column(
        modifier.fillMaxWidth().padding(vertical = 18.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Text(total, style = AcornText.Portfolio, color = AcornColors.DarkTextPrimary)
        Row(horizontalArrangement = Arrangement.spacedBy(4.dp), modifier = Modifier.padding(top = 4.dp)) {
            Text("▲ $gain", style = AcornText.Delta, color = AcornColors.Gain)
            Text("all time", style = AcornText.Delta, color = AcornColors.DarkTextSecondary)
        }
    }
}
```

### Allocation Donut + Legend

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.runtime.*
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.dp

data class Slice(val name: String, val percent: Float, val value: String, val color: Color)

@Composable
fun AcornDonut(slices: List<Slice>, styleLabel: String, modifier: Modifier = Modifier) {
    var started by remember { mutableStateOf(false) }
    val sweep by animateFloatAsState(if (started) 1f else 0f, tween(600), label = "donutSweep")
    LaunchedEffect(Unit) { started = true }

    Box(modifier.size(168.dp), contentAlignment = Alignment.Center) {
        Canvas(Modifier.size(168.dp)) {
            val stroke = 29.dp.toPx()
            val inset = stroke / 2
            var startAngle = -90f
            slices.forEach { s ->
                val total = s.percent * 360f
                drawArc(
                    color = s.color,
                    startAngle = startAngle,
                    sweepAngle = total * sweep,
                    useCenter = false,
                    topLeft = androidx.compose.ui.geometry.Offset(inset, inset),
                    size = Size(size.width - stroke, size.height - stroke),
                    style = Stroke(width = stroke),
                )
                startAngle += total
            }
        }
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Text(styleLabel.uppercase(), style = AcornText.Label, color = AcornColors.DarkTextSecondary)
            Text("100% invested", style = AcornText.Subsection, color = AcornColors.DarkTextPrimary,
                modifier = Modifier.padding(top = 3.dp))
        }
    }
}

@Composable
fun AcornLegendRow(slice: Slice) {
    Row(
        Modifier.fillMaxWidth().padding(vertical = 9.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(12.dp).clip(RoundedCornerShape(4.dp)).background(slice.color))
        Text(slice.name, style = AcornText.RowTitle, color = AcornColors.DarkTextPrimary)
        Text("${(slice.percent * 100).toInt()}%", style = AcornText.Meta, color = AcornColors.DarkTextSecondary)
        Spacer(Modifier.weight(1f))
        Text(slice.value, style = AcornText.Amount, color = AcornColors.DarkTextPrimary,
            modifier = Modifier.width(78.dp), textAlign = androidx.compose.ui.text.style.TextAlign.End)
    }
}
```

### Round-Ups Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Brush

@Composable
fun AcornRoundUpsCard(amount: String, subtitle: String, modifier: Modifier = Modifier) {
    Column(
        modifier
            .padding(horizontal = 18.dp)
            .fillMaxWidth()
            .shadow(24.dp, RoundedCornerShape(18.dp), spotColor = Color(0xFF4A206A).copy(alpha = 0.4f))
            .clip(RoundedCornerShape(18.dp))
            .background(Brush.linearGradient(listOf(AcornColors.Oak, AcornColors.OakBright)))
            .padding(18.dp),
    ) {
        Row(Modifier.fillMaxWidth(), verticalAlignment = Alignment.CenterVertically) {
            Text("ROUND-UPS", style = AcornText.Label, color = Color.White.copy(alpha = 0.85f))
            Spacer(Modifier.weight(1f))
            Box(
                Modifier.clip(RoundedCornerShape(500.dp)).background(Color.White.copy(alpha = 0.16f))
                    .padding(horizontal = 10.dp, vertical = 4.dp)
            ) { Text("Auto", style = AcornText.Label.copy(letterSpacing = 0.sp), color = Color.White) }
        }
        Text(amount, style = AcornText.HeroNumber, color = Color.White, modifier = Modifier.padding(top = 12.dp))
        Text(subtitle, style = AcornText.Meta, color = Color.White.copy(alpha = 0.8f), modifier = Modifier.padding(top = 2.dp))
    }
}
```

### Found Money Row

```kotlin
@Composable
fun AcornFoundMoneyRow(
    monogram: String, brandColor: Color, brand: String, detail: String, amount: String,
) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 22.dp, vertical = 11.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Box(
            Modifier.size(40.dp).clip(RoundedCornerShape(12.dp)).background(brandColor),
            contentAlignment = Alignment.Center,
        ) { Text(monogram, style = AcornText.Subsection.copy(fontWeight = FontWeight.Black), color = Color.White) }
        Column(Modifier.weight(1f)) {
            Text(brand, style = AcornText.RowTitle, color = AcornColors.DarkTextPrimary)
            Text(detail, style = AcornText.Meta, color = AcornColors.DarkTextSecondary)
        }
        Text(amount, style = AcornText.Amount, color = AcornColors.Gain)
    }
}
```

### Pill Buttons

```kotlin
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults

@Composable
fun AcornPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Button(
        onClick = onClick,
        modifier = modifier.fillMaxWidth().heightIn(min = 48.dp),
        shape = RoundedCornerShape(500.dp),
        colors = ButtonDefaults.buttonColors(containerColor = AcornColors.Green, contentColor = AcornColors.ForestInk),
    ) { Text(title, style = AcornText.Button) }
}

@Composable
fun AcornSecondaryButton(title: String, onClick: () -> Unit) {
    Button(
        onClick = onClick,
        shape = RoundedCornerShape(500.dp),
        colors = ButtonDefaults.buttonColors(containerColor = AcornColors.Oak, contentColor = Color.White),
    ) { Text(title, style = AcornText.RowTitle.copy(fontWeight = FontWeight.ExtraBold), color = Color.White) }
}
```

## 4. Navigation

Acorns has a 5-tab bottom strip with **no tint pill** — active is just the green icon + label (Acorn Green on light, Green Bright on dark).

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun AcornBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = AcornColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Invest"  to Icons.Filled.TrendingUp,
            "Later"   to Icons.Filled.CalendarMonth,
            "Spend"   to Icons.Filled.CreditCard,
            "Earn"    to Icons.Filled.CardGiftcard,
            "Profile" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = AcornText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = AcornColors.GreenBright,
                    selectedTextColor   = AcornColors.GreenBright,
                    unselectedIconColor = AcornColors.DarkTextTertiary,
                    unselectedTextColor = AcornColors.DarkTextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill — Acorns has none
                ),
            )
        }
    }
}
```

The Invest screen is a `LazyColumn`: portfolio header (`item`), the donut (`item`, centered), the legend rows, the Round-Ups card (`item`), then the "Found Money" section header + brand rows. The header collapses on scroll via a `TopAppBar` + `nestedScroll`.

## 5. Motion

Acorns motion is warm and celebratory — the donut sweeps, numbers tick up, milestones get confetti.

| Moment | Compose recipe |
|--------|----------------|
| Donut sweep | `animateFloatAsState` 0 → 1 `tween(600)` scaling each `drawArc` sweep angle clockwise from -90° |
| Number ticker | `animateIntAsState(targetCents, tween(300))` then format to currency |
| Milestone celebration | a Lottie confetti/leaf overlay (`LottieAnimation`) + success haptic |
| Card press | `Modifier.scale(animateFloatAsState(if (pressed) 0.98f else 1f, tween(80)))` |
| Range switch | `Crossfade(targetState = range, animationSpec = tween(250))` for the chart/total |
| Found Money credit | new row `AnimatedVisibility` `slideInVertically { -it } + fadeIn(tween(250))`; green wash `tween(800)` fade |
| Tab change | instant color crossfade (NavigationBar handles); ~120ms feel |
| Page navigation | Nav3/`NavHost` slide push `tween(300)` |

```kotlin
// Portfolio ticker — the canonical Acorns "watch it grow" motion
val shown by animateIntAsState(totalCents, tween(300), label = "portfolioTick")
Text(formatUSD(shown), style = AcornText.Portfolio, color = AcornColors.DarkTextPrimary)
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` (light) for card press and range switch; for completed invest / milestone use `view.performHapticFeedback(HapticFeedbackConstants.CONFIRM)`. Tab change gets a soft `CLOCK_TICK`. The donut sweep is silent.

## 6. Icons

Allocation chips and Found Money monograms are colored squares, not icon-font icons. UI chrome maps to `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Invest (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Later (tab) | `calendar` | `Icons.Filled.CalendarMonth` |
| Spend (tab) | `creditcard.fill` | `Icons.Filled.CreditCard` |
| Earn (tab) | `gift.fill` | `Icons.Filled.CardGiftcard` |
| Profile (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| Notifications | `bell` | `Icons.Filled.Notifications` |
| Round-Ups | `arrow.up.circle.fill` | `Icons.Filled.ArrowCircleUp` |
| Gain (▲) | `arrowtriangle.up.fill` | `Icons.Filled.ArrowDropUp` |
| Loss (▼) | `arrowtriangle.down.fill` | `Icons.Filled.ArrowDropDown` |
| Recurring | `repeat` | `Icons.Filled.Repeat` |
| One-time invest | `dollarsign.circle` | `Icons.Filled.AttachMoney` |
| Found Money | `gift.fill` | `Icons.Filled.CardGiftcard` |
| Goal / projection | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Info | `info.circle` | `Icons.Filled.Info` |
| Linked account | `building.columns` | `Icons.Filled.AccountBalance` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas` arcs + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; deep-aubergine canvas wants light-content system bars. The Invest header respects the camera cutout; sheets respect the IME with `Modifier.imePadding()`.
- **Tabular figures**: set `fontFeatureSettings = "tnum"` on the portfolio total, amount, and delta styles so currency aligns; verify on your bundled Nunito Sans.
- **Font scaling**: `sp` honors the user's font scale — keep it on portfolio total, section, body, row title, meta, delta. Pin layout-sensitive text (10sp tab labels, 12sp eyebrow labels) by deriving from `dp` or a fixed-`fontScale` `CompositionLocalProvider`.
- **TalkBack**: expose the donut as ONE allocation summary element ("Allocation: Large Company 38%, Small Company 26%, International 18%, Real Estate 18%") plus per-slice legend rows; the portfolio header announces "Portfolio total {amount}, up {gain} all time"; Found Money rows "{brand}, {detail}, earned {amount}".
- **Touch targets**: Material guidance is 48.dp. Donut segments give a ≥44.dp effective arc; legend / Found Money rows full-row tappable (≥56.dp); 22.dp tab icons a 48.dp hit area; primary buttons ≥ 48.dp.
- **Contrast**: `#1F1A2B` on `#F5F3F8` and `#F2EFF6` on `#14121A` pass WCAG AA for body; `ForestInk` `#14302A` on Acorn Green `#6FBF4E` passes AA — **never** put white on the bright green (enforce via `onPrimary = ForestInk`); validate the gold slice's adjacent labels with a contrast checker.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the donut sweep (draw final arcs), the portfolio ticker (set value directly), and the milestone confetti (show a static badge); keep the gain/loss color cue.
- **Dark mode**: invert via the `Dark*` palette — `#14121A` deep aubergine, NOT true black; surfaces `#1E1B27`; text `#F2EFF6`; positive text uses Green Bright `#84D962`. Shadows are weak on dark, so a 1dp `DarkDivider` border on cards/sheets is the elevation cue. Do **not** enable `dynamicColorScheme()` — Oak + Acorn Green are the brand and must hold regardless of wallpaper.
