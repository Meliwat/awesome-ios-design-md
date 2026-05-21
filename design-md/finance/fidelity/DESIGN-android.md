# Fidelity (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Fidelity's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the portfolio→quote spine, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Fidelity's calm institutional surfaces, the green brand action, sacred gain/loss color, tabular money, the balance-hero + sparkline + sticky Trade button) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Modifier.drawBehind`/Canvas for sparklines, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for any imagery. No color extraction — Fidelity's palette is a fixed institutional set. Fidelity is light-first but ships a full evergreen dark scheme.

## 1. Color Tokens

```kotlin
// ui/theme/FidelityColors.kt
import androidx.compose.ui.graphics.Color

object FidelityColors {
    // Brand
    val Green        = Color(0xFF368727)
    val GreenPressed = Color(0xFF2A6B1F)
    val Heritage     = Color(0xFF00754A)
    val Leaf         = Color(0xFF4FB23B)

    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val Surface        = Color(0xFFF4F6F4)
    val SurfacePressed = Color(0xFFE8ECE8)
    val Divider        = Color(0xFFE1E5E1)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF0E1411) // evergreen — NOT pure black
    val DarkSurface1 = Color(0xFF16201B)
    val DarkSurface2 = Color(0xFF1F2C24)
    val DarkDivider  = Color(0xFF26342B)

    // Text
    val TextPrimary       = Color(0xFF1A1F1B)
    val TextSecondary     = Color(0xFF5A655C)
    val TextTertiary      = Color(0xFF8A938C)
    val DarkTextPrimary   = Color(0xFFE8EFE9)
    val DarkTextSecondary = Color(0xFF9DB0A2)
    val DarkTextTertiary  = Color(0xFF6B7E72)

    // Financial semantics (sacred — gain/loss only)
    val GainLight = Color(0xFF15833E)
    val GainDark  = Color(0xFF15B374)
    val LossLight = Color(0xFFD32F2F)
    val LossDark  = Color(0xFFE5544B)

    // System / accent
    val Warning = Color(0xFFC8862B)
    val Info    = Color(0xFF1E6FB8)
    val Gold    = Color(0xFFC8A24B)
}

/** Resolve a change value to its semantic color (flat -> secondary text). */
fun changeColor(change: Double, dark: Boolean): Color = when {
    change > 0 -> if (dark) FidelityColors.GainDark else FidelityColors.GainLight
    change < 0 -> if (dark) FidelityColors.LossDark else FidelityColors.LossLight
    else       -> if (dark) FidelityColors.DarkTextSecondary else FidelityColors.TextSecondary
}
```

Wire it into both schemes. Fidelity is light-first (paper-calm); the dark scheme uses the signature `#0E1411` evergreen, never true black. Do **not** enable Material You `dynamicColorScheme()` — the institutional green identity must hold regardless of wallpaper.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val FidLight = lightColorScheme(
    primary        = FidelityColors.Green,
    onPrimary      = FidelityColors.Canvas,
    secondary      = FidelityColors.Heritage,
    background     = FidelityColors.Canvas,
    onBackground   = FidelityColors.TextPrimary,
    surface        = FidelityColors.Surface,
    onSurface      = FidelityColors.TextPrimary,
    surfaceVariant = FidelityColors.SurfacePressed,
    outline        = FidelityColors.Divider,
    error          = FidelityColors.LossLight,
)

private val FidDark = darkColorScheme(
    primary        = FidelityColors.Green,
    onPrimary      = FidelityColors.Canvas,
    secondary      = FidelityColors.Leaf,
    background     = FidelityColors.DarkCanvas,
    onBackground   = FidelityColors.DarkTextPrimary,
    surface        = FidelityColors.DarkSurface1,
    onSurface      = FidelityColors.DarkTextPrimary,
    surfaceVariant = FidelityColors.DarkSurface2,
    outline        = FidelityColors.DarkDivider,
    error          = FidelityColors.LossDark,
)

@Composable
fun FidelityTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) FidDark else FidLight,
    typography = FidelityTypography,
    content = content,
)
```

## 2. Typography

Fidelity's brand face is "Average Sans"; bundle **Inter** (SIL OFL) in `res/font/` as the closest free match. The defining rule: every monetary figure carries tabular figures via `TextStyle(fontFeatureSettings = "tnum")`.

```kotlin
// ui/theme/FidelityType.kt
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

// Apply to ANY currency / price / % / quantity
private const val TNUM = "tnum"

object FidText {
    val BalanceHero = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 34.sp, lineHeight = 38.sp, letterSpacing = (-0.6).sp, fontFeatureSettings = TNUM)
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val RowValue    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 21.sp, fontFeatureSettings = TNUM)
    val RowSymbol   = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val NumericMono = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 17.sp, fontFeatureSettings = TNUM)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val RangeTab    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
}

val FidelityTypography = Typography(
    headlineLarge  = FidText.BalanceHero,
    headlineMedium = FidText.ScreenTitle,
    titleMedium    = FidText.CardTitle,
    bodyMedium     = FidText.Body,
    labelSmall     = FidText.Tab,
)
```

## 3. Signature Components

### Balance Header (Compose) — the screen hero

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

@Composable
fun BalanceHeader(
    greeting: String, title: String, value: String,
    changeAmount: String, changePercent: String, change: Double,
    dark: Boolean,
) {
    val primary = if (dark) FidelityColors.DarkTextPrimary else FidelityColors.TextPrimary
    val secondary = if (dark) FidelityColors.DarkTextSecondary else FidelityColors.TextSecondary
    val c = changeColor(change, dark)

    Column(Modifier.fillMaxWidth().padding(horizontal = 20.dp).padding(bottom = 16.dp)) {
        Text(greeting, style = FidText.Caption, color = secondary)
        Text(title, style = FidText.ScreenTitle, color = primary, modifier = Modifier.padding(top = 2.dp))
        Text("Total account value", style = FidText.Caption, color = secondary, modifier = Modifier.padding(top = 18.dp))
        Text(value, style = FidText.BalanceHero, color = primary, modifier = Modifier.padding(top = 4.dp))
        Row(Modifier.padding(top = 8.dp)) {
            Text(if (change >= 0) "▲ " else "▼ ", color = c, style = FidText.NumericMono)
            Text("$changeAmount ", style = FidText.NumericMono, color = c)
            Text("($changePercent) today", style = FidText.NumericMono, color = secondary)
        }
    }
}
```

### Holding / Watchlist Row

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun HoldingRow(
    symbol: String, name: String, value: String,
    changePct: String, change: Double, isBrandFund: Boolean = false, dark: Boolean,
) {
    val primary = if (dark) FidelityColors.DarkTextPrimary else FidelityColors.TextPrimary
    val secondary = if (dark) FidelityColors.DarkTextSecondary else FidelityColors.TextSecondary
    val chipBg = if (isBrandFund) FidelityColors.Green.copy(alpha = 0.16f)
                 else if (dark) FidelityColors.DarkSurface2 else FidelityColors.Surface

    Column {
        Row(
            Modifier.fillMaxWidth().height(64.dp).clickable {}
                .padding(horizontal = 20.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(
                Modifier.size(38.dp).clip(RoundedCornerShape(10.dp)).background(chipBg),
                contentAlignment = Alignment.Center,
            ) {
                Text(symbol, style = FidText.Caption.copy(fontWeight = androidx.compose.ui.text.font.FontWeight.ExtraBold),
                     color = if (isBrandFund) FidelityColors.Leaf else primary)
            }
            Column(Modifier.weight(1f)) {
                Text(symbol, style = FidText.RowSymbol, color = primary)
                Text(name, style = FidText.Caption, color = secondary, maxLines = 1, overflow = TextOverflow.Ellipsis)
            }
            Column(horizontalAlignment = Alignment.End) {
                Text(value, style = FidText.RowValue, color = primary)
                Text(changePct, style = FidText.NumericMono, color = changeColor(change, dark))
            }
        }
        Box(Modifier.fillMaxWidth().height(0.5.dp)
            .background(if (dark) FidelityColors.DarkDivider else FidelityColors.Divider))
    }
}
```

### Sparkline (Canvas) + Range Tabs

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.*
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.StrokeJoin
import androidx.compose.ui.graphics.drawscope.Stroke

@Composable
fun Sparkline(points: List<Float>, isUp: Boolean, modifier: Modifier = Modifier) {
    val stroke = if (isUp) FidelityColors.Leaf else FidelityColors.LossDark
    Canvas(modifier.fillMaxWidth().height(64.dp)) {
        val step = size.width / (points.size - 1).coerceAtLeast(1)
        val line = Path().apply {
            points.forEachIndexed { i, v ->
                val x = i * step; val y = size.height - v * size.height
                if (i == 0) moveTo(x, y) else lineTo(x, y)
            }
        }
        val area = Path().apply {
            addPath(line); lineTo(size.width, size.height); lineTo(0f, size.height); close()
        }
        drawPath(area, Brush.verticalGradient(listOf(stroke.copy(alpha = 0.32f), stroke.copy(alpha = 0f))))
        drawPath(line, stroke, style = Stroke(width = 2.dp.toPx(), join = StrokeJoin.Round))
    }
}

@Composable
fun RangeTabs() {
    var sel by remember { mutableStateOf(0) }
    val haptics = LocalHapticFeedback.current
    val ranges = listOf("1D", "1W", "1M", "1Y", "5Y", "All")
    Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
        ranges.forEachIndexed { i, r ->
            Box(
                Modifier.clip(RoundedCornerShape(50))
                    .background(if (i == sel) FidelityColors.Green else Color.Transparent)
                    .clickable { sel = i; haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) }
                    .padding(horizontal = 9.dp, vertical = 3.dp),
            ) {
                Text(r, style = FidText.RangeTab, color = if (i == sel) Color.White else FidelityColors.DarkTextSecondary)
            }
        }
    }
}
```

### Sticky Trade Button

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.Text

@Composable
fun StickyTradeBar(onTrade: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    Column(
        Modifier.fillMaxWidth()
            .background(FidelityColors.DarkCanvas.copy(alpha = 0.94f))
            .navigationBarsPadding(),
    ) {
        Box(Modifier.fillMaxWidth().height(0.5.dp).background(FidelityColors.DarkDivider))
        Button(
            onClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onTrade() },
            shape = RoundedCornerShape(8.dp),
            colors = ButtonDefaults.buttonColors(containerColor = FidelityColors.Green, contentColor = Color.White),
            modifier = Modifier.fillMaxWidth().height(52.dp).padding(horizontal = 16.dp).padding(top = 12.dp),
        ) { Text("Trade", style = FidText.Button) }
    }
}
```

### Performance Pill

```kotlin
@Composable
fun PerformancePill(kind: String /* gain | loss | flat */, text: String) {
    val (fg, bg, pre) = when (kind) {
        "gain" -> Triple(FidelityColors.GainDark, FidelityColors.GainDark.copy(alpha = 0.16f), "▲ ")
        "loss" -> Triple(FidelityColors.LossDark, FidelityColors.LossDark.copy(alpha = 0.16f), "▼ ")
        else   -> Triple(FidelityColors.DarkTextSecondary, FidelityColors.DarkSurface2, "— ")
    }
    Box(
        Modifier.clip(RoundedCornerShape(8.dp)).background(bg).padding(horizontal = 14.dp, vertical = 8.dp),
    ) { Text("$pre$text", style = FidText.NumericMono, color = fg) }
}
```

## 4. Navigation

Fidelity has minimal chrome: a five-tab bottom strip. On Android model it as a `NavigationBar`; there is no Material tint pill — active is just the green icon/label.

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun FidelityBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = FidelityColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Summary"  to Icons.Filled.Home,
            "Planning" to Icons.Filled.BarChart,
            "Invest"   to Icons.Filled.TrendingUp,
            "News"     to Icons.Filled.Article,
            "Profile"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = FidText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = FidelityColors.Leaf,
                    selectedTextColor = FidelityColors.Leaf,
                    unselectedIconColor = FidelityColors.DarkTextTertiary,
                    unselectedTextColor = FidelityColors.DarkTextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill
                ),
            )
        }
    }
}
```

The portfolio→quote spine is a `NavHost` slide push: Summary → Account (Positions/Activity/Balances tabs via `TabRow` underline) → Position → Quote (chart + range tabs + sticky `StickyTradeBar`) → Trade ticket (`ModalBottomSheet`).

## 5. Motion

Fidelity motion is calm — 120–300ms ease-out, never aggressive on money.

| Moment | Compose recipe |
|--------|----------------|
| Price tick | animate text color only via `animateColorAsState(tween(600))`, then settle to primary — digits never roll |
| Chart range morph | re-path Canvas; animate progress with `animateFloatAsState(tween(280))` interpolating point arrays |
| Chart scrub | `pointerInput` drag → vertical crosshair + tabular value bubble, 1:1, no easing |
| Push navigation | `NavHost` slide `tween(300)` |
| Sticky Trade button | always present (not animated); top divider visible as content scrolls beneath |
| Tab switch | instant content swap; icon color cross-fade `tween(120)` |
| Pull-to-refresh | `PullToRefreshContainer` tinted `FidelityColors.Green`; light haptic on trigger |

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for tab/range change; `HapticFeedbackType.LongPress` for tap-Trade / Place-order; on order filled use `view.performHapticFeedback(HapticFeedbackConstants.CONFIRM)`.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. For directional gain/loss prefer Unicode triangles `▲ ▼` so the sign survives monochrome rendering.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Summary (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Planning (tab) | `chart.bar.fill` | `Icons.Filled.BarChart` |
| Invest (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| News (tab) | `newspaper.fill` | `Icons.Filled.Article` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Day change up | `triangle.fill` | `▲` (text) / `Icons.Filled.ArrowDropUp` |
| Day change down | `arrowtriangle.down.fill` | `▼` (text) / `Icons.Filled.ArrowDropDown` |
| Watchlist toggle | `star` / `star.fill` | `Icons.Outlined.StarBorder` / `Icons.Filled.Star` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Trade | `arrow.left.arrow.right` | `Icons.Filled.SwapHoriz` |
| Transfer | `arrow.down.circle` | `Icons.Filled.ArrowCircleDown` |
| Alerts | `bell.fill` | `Icons.Filled.Notifications` |
| Disclosure | `chevron.right` | `Icons.Filled.ChevronRight` |
| Allocation ring | `chart.pie.fill` | `Icons.Filled.PieChart` |
| Documents | `doc.text` | `Icons.Filled.Description` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24**, `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; light canvas wants dark-content system bars (light-content in dark mode). The sticky Trade bar uses `Modifier.navigationBarsPadding()`; trade-ticket fields use `Modifier.imePadding()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on balance hero, titles, body, meta. Pin layout-sensitive text (10sp tab labels, range tabs, chart-axis labels, table headers) via `dp`-derived sizes or a fixed-`fontScale` `LocalDensity`. Tabular figures must stay tabular at every scale (`fontFeatureSettings = "tnum"`).
- **Do not rely on color alone**: gain/loss must always carry a `+`/`−` sign and `▲`/`▼` glyph (the components above do). This is a hard accessibility requirement for a finance app.
- **TalkBack**: announce a holding row as one node — "Apple, A A P L, 229 dollars 87 cents, up 1.42 percent today". Use `Modifier.semantics(mergeDescendants = true)` and a composed `contentDescription`; never expose color as the only state.
- **Touch targets**: Material guidance is 48.dp — list rows are 64.dp (fine); give the range-tab pills and the watchlist star a 48.dp hit area via padding. Primary buttons ≥ 48.dp.
- **Contrast**: `#15B374`/`#E5544B` on `#0E1411` pass WCAG AA at the bold numeric weights used; `#368727` with white button text passes AA at 16sp bold. Validate any custom pair.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, skip the price-tick color animation and the chart morph (set final state instantly); keep the static sign/arrow which conveys direction.
- **Dark mode**: invert via the `Dark*` palette — `#0E1411`, NOT true black; `#1A1F1B` text becomes `#E8EFE9`. Shadows are nearly invisible on the evergreen canvas, so add a 1dp `DarkDivider` border to cards, the trade-ticket sheet, and the quote panel as the elevation cue. Do **not** enable `dynamicColorScheme()` — the institutional green identity must not harmonize with wallpaper.
