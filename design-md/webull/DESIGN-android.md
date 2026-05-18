# Webull (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Webull's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the candlestick chart + order ladder, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Webull's near-black terminal canvas, the blue→cyan gradient, pervasive saturated up/down color, the candlestick chart + order ladder, the docked Buy/Sell pair) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas` for candles, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. No color extraction — Webull's palette is fixed. Webull is **dark-native**: force the dark scheme; do not offer a primary light theme.

## 1. Color Tokens

```kotlin
// ui/theme/WebullColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object WebullColors {
    // Brand
    val Blue        = Color(0xFF1B9EFB)
    val Cyan        = Color(0xFF20D5C4)
    val BluePressed = Color(0xFF1684D6)
    val OnBrand     = Color(0xFF04121C)
    val BrandGradient = Brush.linearGradient(listOf(Blue, Cyan))

    // Canvas & surfaces (dark — the only mode)
    val Canvas   = Color(0xFF0B0E11) // near-black for max chart contrast
    val Surface1 = Color(0xFF14181D)
    val Surface2 = Color(0xFF1C2127)
    val Divider  = Color(0xFF232931)
    val GridLine = Color(0xFF1A1F26)

    // Text
    val TextPrimary   = Color(0xFFEAEEF2)
    val TextSecondary = Color(0xFF8B95A1)
    val TextTertiary  = Color(0xFF5A636E)

    // Market semantics (loud & pervasive)
    val Up       = Color(0xFF00C076)
    val Down     = Color(0xFFFA5252)
    val OnUp     = Color(0xFF03150D)
    val OnDown   = Color(0xFF1B0606)
    val UpFill   = Color(0x2400C076) // ≈14% green
    val DownFill = Color(0x24FA5252) // ≈14% red

    // System / accent
    val Amber = Color(0xFFF7A600)
}

/** US default: up=green, down=red. Region setting may invert (Asia markets). */
fun wbColor(change: Double, upIsGreen: Boolean = true): Color {
    if (change == 0.0) return WebullColors.TextSecondary
    val positive = change > 0
    val green = if (upIsGreen) positive else !positive
    return if (green) WebullColors.Up else WebullColors.Down
}
```

Webull is dark-native — force the dark scheme. Do **not** enable Material You `dynamicColorScheme()`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val WebullDark = darkColorScheme(
    primary        = WebullColors.Blue,
    onPrimary      = WebullColors.OnBrand,
    secondary      = WebullColors.Cyan,
    background     = WebullColors.Canvas,
    onBackground   = WebullColors.TextPrimary,
    surface        = WebullColors.Surface1,
    onSurface      = WebullColors.TextPrimary,
    surfaceVariant = WebullColors.Surface2,
    outline        = WebullColors.Divider,
    error          = WebullColors.Down,
)

@Composable
fun WebullTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = WebullDark, typography = WebullTypography, content = content)
```

## 2. Typography

Bundle **Inter** (SIL OFL) with tabular figures in `res/font/`. Defining rule: every numeric carries `fontFeatureSettings = "tnum"` so the ladder and option chain align.

```kotlin
// ui/theme/WebullType.kt
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

private const val TNUM = "tnum"

object WBText {
    val LastPrice   = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 36.sp, letterSpacing = (-0.6).sp, fontFeatureSettings = TNUM)
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Symbol      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 22.sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 23.sp)
    val RowSymbol   = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 18.sp)
    val RowValue    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp, fontFeatureSettings = TNUM)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val NumericMono = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 16.sp, fontFeatureSettings = TNUM)
    val Ladder      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 17.sp, fontFeatureSettings = TNUM)
    val Label       = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.4.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 15.sp, lineHeight = 15.sp, letterSpacing = 0.3.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Timeframe   = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
}

val WebullTypography = Typography(
    headlineLarge  = WBText.LastPrice,
    headlineMedium = WBText.ScreenTitle,
    titleMedium    = WBText.Symbol,
    bodyMedium     = WBText.Body,
    labelSmall     = WBText.Tab,
)
```

## 3. Signature Components

### Quote Header

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

@Composable
fun QuoteHeader(
    symbol: String, name: String, price: String, delta: String, change: Double, isPaper: Boolean,
) {
    val c = wbColor(change)
    Column(
        Modifier.fillMaxWidth().background(WebullColors.Canvas)
            .padding(horizontal = 18.dp).padding(top = 4.dp, bottom = 10.dp),
    ) {
        Row(verticalAlignment = Alignment.Top) {
            Column(Modifier.weight(1f)) {
                Text(symbol, style = WBText.Section.copy(fontSize = 20.sp), color = WebullColors.TextPrimary)
                Text(name, style = WBText.Meta, color = WebullColors.TextSecondary)
            }
            if (isPaper) {
                Box(
                    Modifier.clip(RoundedCornerShape(4.dp)).background(WebullColors.Cyan)
                        .padding(horizontal = 8.dp, vertical = 3.dp),
                ) { Text("PAPER", style = WBText.Label.copy(fontWeight = FontWeight.ExtraBold), color = WebullColors.OnBrand) }
            }
        }
        Text(price, style = WBText.LastPrice, color = c, modifier = Modifier.padding(top = 12.dp))
        Text("$delta ${if (change >= 0) "▲" else "▼"}", style = WBText.RowValue, color = c, modifier = Modifier.padding(top = 4.dp))
    }
}
```

### Candlestick Chart (Canvas) + Timeframe Strip

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.*
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.PathEffect
import androidx.compose.ui.graphics.drawscope.Stroke

data class Candle(val o: Float, val h: Float, val l: Float, val c: Float)

@Composable
fun CandleChart(candles: List<Candle>, prevClose: Float, min: Float, max: Float, modifier: Modifier = Modifier) {
    Canvas(modifier.fillMaxWidth().height(158.dp)) {
        fun y(v: Float) = size.height - (v - min) / (max - min) * size.height
        val step = size.width / candles.size

        // grid lines — nearly invisible
        for (i in 1..3) {
            val yy = size.height * i / 4
            drawLine(WebullColors.GridLine, Offset(0f, yy), Offset(size.width, yy), 1f)
        }
        // prev-close dashed
        drawLine(
            WebullColors.Blue, Offset(0f, y(prevClose)), Offset(size.width, y(prevClose)),
            strokeWidth = 1f, pathEffect = PathEffect.dashPathEffect(floatArrayOf(6f, 6f)),
        )
        // candles
        candles.forEachIndexed { i, c ->
            val x = step * (i + 0.5f)
            val col = if (c.c >= c.o) WebullColors.Up else WebullColors.Down
            drawLine(col, Offset(x, y(c.h)), Offset(x, y(c.l)), strokeWidth = 1.5.dp.toPx())
            val top = minOf(y(c.o), y(c.c))
            val bh = maxOf(kotlin.math.abs(y(c.o) - y(c.c)), 1f)
            drawRect(col, topLeft = Offset(x - step * 0.3f, top), size = androidx.compose.ui.geometry.Size(step * 0.6f, bh))
        }
    }
}

@Composable
fun TimeframeStrip() {
    var sel by remember { mutableIntStateOf(2) }
    val haptics = LocalHapticFeedback.current
    val tfs = listOf("1m", "5m", "1D", "5D", "1M", "1Y")
    Row(
        Modifier.padding(horizontal = 14.dp, vertical = 0.dp).padding(top = 10.dp),
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        tfs.forEachIndexed { i, t ->
            Box(
                Modifier.clip(RoundedCornerShape(4.dp))
                    .background(if (i == sel) WebullColors.Blue.copy(alpha = 0.16f) else Color.Transparent)
                    .clickable { sel = i; haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) }
                    .padding(horizontal = 10.dp, vertical = 5.dp),
            ) {
                Text(t, style = WBText.Timeframe, color = if (i == sel) WebullColors.Blue else WebullColors.TextSecondary)
            }
        }
    }
}
```

### Order-Book Ladder

```kotlin
data class Level(val price: String, val size: String, val frac: Float)

@Composable
fun LadderRow(level: Level, side: String /* bid | ask */) {
    Box(Modifier.fillMaxWidth().height(22.dp), contentAlignment = Alignment.CenterStart) {
        Box(
            Modifier.fillMaxWidth(level.frac).fillMaxHeight().padding(vertical = 3.dp)
                .align(Alignment.CenterEnd).clip(RoundedCornerShape(2.dp))
                .background(if (side == "bid") WebullColors.UpFill else WebullColors.DownFill),
        )
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween, verticalAlignment = Alignment.CenterVertically) {
            Text(level.price, style = WBText.NumericMono, color = if (side == "bid") WebullColors.Up else WebullColors.Down, modifier = Modifier.width(64.dp))
            Text(level.size, style = WBText.NumericMono, color = WebullColors.TextSecondary)
        }
    }
}

@Composable
fun OrderLadder(asks: List<Level>, bids: List<Level>, mid: String, spread: String) {
    Column(Modifier.fillMaxWidth().padding(horizontal = 18.dp).padding(top = 12.dp)) {
        asks.forEach { LadderRow(it, "ask") }
        Box(
            Modifier.fillMaxWidth().padding(vertical = 4.dp)
                .drawBehind {
                    drawLine(WebullColors.Divider, Offset(0f, 0f), Offset(size.width, 0f), 0.5.dp.toPx())
                    drawLine(WebullColors.Divider, Offset(0f, size.height), Offset(size.width, size.height), 0.5.dp.toPx())
                }
                .padding(vertical = 6.dp),
            contentAlignment = Alignment.Center,
        ) { Text("$mid  ·  Spread $spread", style = WBText.Ladder, color = WebullColors.TextPrimary) }
        bids.forEach { LadderRow(it, "bid") }
    }
}
```

### Docked Buy / Sell Pair + Watchlist Row

```kotlin
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults

@Composable
fun BuySellBar(onBuy: () -> Unit, onSell: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    fun tap() = haptics.performHapticFeedback(HapticFeedbackType.LongPress)
    Row(
        Modifier.fillMaxWidth().background(WebullColors.Canvas.copy(alpha = 0.94f))
            .navigationBarsPadding().padding(horizontal = 18.dp, vertical = 12.dp),
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Button(
            onClick = { tap(); onBuy() },
            shape = RoundedCornerShape(6.dp),
            colors = ButtonDefaults.buttonColors(containerColor = WebullColors.Up, contentColor = WebullColors.OnUp),
            modifier = Modifier.weight(1f).height(48.dp),
        ) { Text("Buy", style = WBText.Button) }
        Button(
            onClick = { tap(); onSell() },
            shape = RoundedCornerShape(6.dp),
            colors = ButtonDefaults.buttonColors(containerColor = WebullColors.Down, contentColor = WebullColors.OnDown),
            modifier = Modifier.weight(1f).height(48.dp),
        ) { Text("Sell", style = WBText.Button) }
    }
}

@Composable
fun WatchlistRow(symbol: String, name: String, price: String, pct: String, change: Double, spark: List<Float>) {
    val up = change >= 0
    Row(
        Modifier.fillMaxWidth().padding(vertical = 12.dp)
            .drawBehind { drawLine(WebullColors.Divider, Offset(0f, size.height), Offset(size.width, size.height), 0.5.dp.toPx()) },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Column(Modifier.weight(1f)) {
            Text(symbol, style = WBText.RowSymbol, color = WebullColors.TextPrimary)
            Text(name, style = WBText.Meta.copy(fontSize = 11.sp), color = WebullColors.TextSecondary, maxLines = 1)
        }
        Canvas(Modifier.size(56.dp, 28.dp)) {
            val st = size.width / (spark.size - 1).coerceAtLeast(1)
            for (i in 1 until spark.size) {
                drawLine(
                    if (up) WebullColors.Up else WebullColors.Down,
                    Offset((i - 1) * st, size.height - spark[i - 1] * size.height),
                    Offset(i * st, size.height - spark[i] * size.height),
                    strokeWidth = 2.dp.toPx(),
                )
            }
        }
        Column(horizontalAlignment = Alignment.End) {
            Text(price, style = WBText.RowValue, color = WebullColors.TextPrimary)
            Box(
                Modifier.padding(top = 4.dp).clip(RoundedCornerShape(4.dp)).background(wbColor(change))
                    .padding(horizontal = 7.dp, vertical = 3.dp),
            ) { Text(pct, style = WBText.NumericMono.copy(fontWeight = FontWeight.ExtraBold, fontSize = 11.sp), color = if (up) WebullColors.OnUp else WebullColors.OnDown) }
        }
    }
}
```

## 4. Navigation

Webull has a five-tab bottom strip. On Android model it as a `NavigationBar`; there is no Material tint pill — active is just the blue icon/label.

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun WebullBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = WebullColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Markets"   to Icons.Filled.Search,
            "Quotes"    to Icons.Filled.ShowChart,
            "Trade"     to Icons.Filled.AttachMoney,
            "Portfolio" to Icons.Filled.BarChart,
            "Menu"      to Icons.Filled.Menu,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = WBText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = WebullColors.Blue,
                    selectedTextColor = WebullColors.Blue,
                    unselectedIconColor = WebullColors.TextTertiary,
                    unselectedTextColor = WebullColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill
                ),
            )
        }
    }
}
```

The product spine is a `NavHost` slide push: Quotes (watchlist) → Symbol → Chart (`CandleChart` + `TimeframeStrip` + `OrderLadder`) → Trade ticket (`ModalBottomSheet`, confirm button inherits green/red). Landscape full-screen chart: allow rotation on the chart route only.

## 5. Motion

Webull motion is fast and data-first.

| Moment | Compose recipe |
|--------|----------------|
| Live price tick | flash bright then settle: `animateColorAsState` brief overshoot to a lighter up/down then back — digits never roll |
| Chart timeframe switch | re-path Canvas; `animateFloatAsState(tween(250))` interpolating candle arrays |
| Crosshair scrub | `pointerInput` drag → vertical+horizontal `#8B95A1` lines + OHLC tooltip, 1:1, no easing |
| Ladder update | rows update in place; depth-bar `animateFloatAsState` width on size change |
| Buy/Sell → ticket | `ModalBottomSheet` slide-up (~280ms); confirm inherits green/red |
| Tab switch | instant content swap; icon color cross-fade `tween(120)` |
| Pull-to-refresh | `PullToRefreshContainer` tinted with the blue→cyan gradient; light haptic |

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for timeframe/tab change and crosshair crossing key levels; `HapticFeedbackType.LongPress` for order submit; on fill use `view.performHapticFeedback(HapticFeedbackConstants.CONFIRM)`.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. For up/down direction prefer Unicode triangles `▲ ▼` (critical — the up/down→color mapping can invert by region).

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Markets (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Quotes (tab) | `chart.xyaxis.line` | `Icons.Filled.ShowChart` |
| Trade (tab) | `dollarsign.circle` | `Icons.Filled.AttachMoney` |
| Portfolio (tab) | `chart.bar.fill` | `Icons.Filled.BarChart` |
| Menu (tab) | `line.3.horizontal` | `Icons.Filled.Menu` |
| Watchlist star | `star` / `star.fill` | `Icons.Outlined.StarBorder` / `Icons.Filled.Star` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Up tick | `arrowtriangle.up.fill` | `▲` (text) / `Icons.Filled.ArrowDropUp` |
| Down tick | `arrowtriangle.down.fill` | `▼` (text) / `Icons.Filled.ArrowDropDown` |
| Alerts | `bell.fill` | `Icons.Filled.Notifications` |
| Indicators | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Chart fullscreen | `arrow.up.left.and.arrow.down.right` | `Icons.Filled.Fullscreen` |
| Order type | `list.bullet` | `Icons.Filled.FormatListBulleted` |
| Quantity +/− | `plus` / `minus` | `Icons.Filled.Add` / `Icons.Filled.Remove` |
| Depth / level 2 | `chart.bar.doc.horizontal` | `Icons.Filled.AlignHorizontalLeft` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24**, `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Dark-native**: force the dark scheme app-wide; do not offer a primary light theme. The near-black `#0B0E11` canvas is deliberate for candle/contrast. Do **not** enable `dynamicColorScheme()`.
- **Edge-to-edge**: call `enableEdgeToEdge()` with light system-bar icons. The chart is full-bleed; the docked Buy/Sell bar uses `Modifier.navigationBarsPadding()`; order-entry uses `Modifier.imePadding()`. Allow landscape only on the chart route (full-screen chart with crosshair is a core feature) via the activity's `requestedOrientation` or a per-route side effect.
- **Font scaling**: `sp` honors the user's font scale — scale last price, titles, body modestly. Pin the ladder, option chain, timeframe chips, tab labels, and column headers (tabular layout-critical grids) via `dp`-derived sizes. Tabular must stay tabular (`fontFeatureSettings = "tnum"`).
- **Do not rely on color alone**: up/down must always carry `+`/`−` and `▲`/`▼` (components above do). This is essential because the up/down→color mapping can invert by region (many Asia markets use up=red) — color can never be the only signal. Expose an `upIsGreen` setting threaded through `wbColor()`.
- **TalkBack**: announce a watchlist row as one node — "Apple, 229.87, up 1.42 percent"; a ladder row as "Ask, 248.92, size 1204". Use `Modifier.semantics(mergeDescendants = true)`; never expose color alone.
- **Touch targets**: Material guidance is 48.dp — watchlist rows are 56.dp (fine); give timeframe chips and quantity steppers a 48.dp hit area via padding. Buy/Sell buttons are 48.dp.
- **Contrast**: `#00C076`/`#FA5252` on `#0B0E11` pass WCAG AA at the bold weights; on-button text `#03150D`/`#1B0606` on solid green/red passes AA at 15sp w800; `#1B9EFB` with `#04121C` passes AA.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, skip the timeframe re-layout and price flash (set final state instantly); keep the static sign/arrow and crosshair (they convey data).
- **Performance**: `Canvas` for 60fps candles; throttle live ticks to ~10Hz on a background flow to save battery; the crosshair `pointerInput` should run at high priority.
