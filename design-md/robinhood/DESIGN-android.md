# Robinhood (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Robinhood's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the stark white canvas with true-black text, direction-bound green/orange, the full-bleed portfolio chart with its draggable scrubber, tabular numerals everywhere) while making everything idiomatic Android — a `Canvas`-drawn line graph with `pointerInput` scrubbing instead of Swift `Charts`, `NavigationBar` instead of a UITabBar, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for fetched company/ticker logos.

## 1. Color Tokens

```kotlin
// ui/theme/RobinhoodColors.kt
import androidx.compose.ui.graphics.Color

object RobinhoodColors {
    // Canvas & Surfaces (Light)
    val CanvasLight     = Color(0xFFFFFFFF)
    val SurfaceGray     = Color(0xFFF7F7F7)
    val SurfaceGray2    = Color(0xFFEFEFEF)
    val DividerLight    = Color(0xFFE6E6E6)

    // Text (Light) — true black, no warm grays
    val TextPrimary     = Color(0xFF000000)
    val TextSecondary   = Color(0xFF5C6166)
    val TextTertiary    = Color(0xFF9B9EA3)
    val TextMuted       = Color(0xFFC2C5CA)

    // Brand — color is bound to direction
    val Green           = Color(0xFF00C805) // up
    val GreenPressed    = Color(0xFF00A904)
    val GreenDim        = Color(0xFF21CE99) // heritage
    val GreenBg         = Color(0xFFE6F9E0)
    val Red             = Color(0xFFFF5000) // down — a saturated ORANGE, not red
    val RedBg           = Color(0xFFFFEDE5)

    // Semantic
    val ErrorTrue       = Color(0xFFE62232) // a TRUE red — hard errors only, distinct from loss orange
    val Warning         = Color(0xFFFFB800)
    val Info            = Color(0xFF1D6FF2)

    // Crypto accents
    val Bitcoin         = Color(0xFFF7931A)
    val Ethereum        = Color(0xFF627EEA)

    // Dark mode — true black; the green line glows electric
    val DarkCanvas      = Color(0xFF000000)
    val DarkSurface1    = Color(0xFF181B1F)
    val DarkSurface2    = Color(0xFF23272D)
    val DarkDivider     = Color(0xFF2D3138)
    val DarkTextPrimary = Color(0xFFFFFFFF)
    val DarkTextSecondary = Color(0xFFA4A8AD)
}
```

Robinhood's default is a **stark light canvas** — clean tech-product, deliberate counterpoint to legacy brokerages. Wire a Material 3 `lightColorScheme`/`darkColorScheme`. **Do not map green/orange onto `primary`/`error`** — they are *direction* signals, not theme roles. Keep `primary` as black (the universal Trade CTA color) and the brand greens/orange as explicit tokens so no stock component can accidentally use them.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val RobinhoodLight = lightColorScheme(
    primary        = RobinhoodColors.TextPrimary,   // black — the universal Trade CTA
    onPrimary      = Color.White,
    background     = RobinhoodColors.CanvasLight,
    onBackground   = RobinhoodColors.TextPrimary,
    surface        = RobinhoodColors.CanvasLight,
    onSurface      = RobinhoodColors.TextPrimary,
    surfaceVariant = RobinhoodColors.SurfaceGray,
    outline        = RobinhoodColors.DividerLight,
    error          = RobinhoodColors.ErrorTrue,     // the TRUE red, not the loss orange
)

private val RobinhoodDark = darkColorScheme(
    primary        = RobinhoodColors.DarkTextPrimary, // white — Trade CTA on black
    onPrimary      = RobinhoodColors.DarkCanvas,
    background     = RobinhoodColors.DarkCanvas,
    onBackground   = RobinhoodColors.DarkTextPrimary,
    surface        = RobinhoodColors.DarkSurface1,
    onSurface      = RobinhoodColors.DarkTextPrimary,
    surfaceVariant = RobinhoodColors.DarkSurface2,
    outline        = RobinhoodColors.DarkDivider,
    error          = RobinhoodColors.ErrorTrue,
)

@Composable
fun RobinhoodTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) RobinhoodDark else RobinhoodLight,
    typography  = RobinhoodTypography,
    content     = content,
)
```

## 2. Typography

Capsule Sans (drawn by XYZ Type for Robinhood) is proprietary. Drop the TTFs in `res/font/` and build two families — `CapsuleSansText` for body, `CapsuleSansDisplay` for hero values. Fall back to the system font; on the fallback, **always** apply `FontFeatureSettings("tnum")` (tabular numerals) — this is the single most important typography rule on a brokerage app: columns of prices must align as the user scrolls.

```kotlin
// ui/theme/RobinhoodType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val CapsuleText = FontFamily(
    Font(R.font.capsule_sans_text_regular,  FontWeight.Normal),   // 400
    Font(R.font.capsule_sans_text_medium,   FontWeight.Medium),   // 500
    Font(R.font.capsule_sans_text_semibold, FontWeight.SemiBold), // 600
    Font(R.font.capsule_sans_text_bold,     FontWeight.Bold),     // 700
)
val CapsuleDisplay = FontFamily(
    Font(R.font.capsule_sans_display_bold,  FontWeight.Bold),     // hero values 40sp+
)

// Tabular numerals — non-negotiable on every price / percent / share count
private const val TNUM = "tnum"

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object RobinhoodText {
    val PortfolioHero  = TextStyle(CapsuleDisplay, fontWeight = FontWeight.Bold,     fontSize = 40.sp, lineHeight = 40.sp, letterSpacing = (-0.5).sp, fontFeatureSettings = TNUM)
    val DayChange      = TextStyle(CapsuleText,    fontWeight = FontWeight.Medium,   fontSize = 16.sp, lineHeight = 19.sp, fontFeatureSettings = TNUM)
    val ScreenTitle    = TextStyle(CapsuleText,    fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 25.sp, letterSpacing = (-0.2).sp)
    val SectionHeader  = TextStyle(CapsuleText,    fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val PositionTitle  = TextStyle(CapsuleText,    fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 21.sp)
    val Ticker         = TextStyle(CapsuleText,    fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 17.sp, letterSpacing = 0.3.sp)
    val PositionValue  = TextStyle(CapsuleText,    fontWeight = FontWeight.Medium,   fontSize = 17.sp, lineHeight = 20.sp, fontFeatureSettings = TNUM)
    val PositionChange = TextStyle(CapsuleText,    fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 17.sp, fontFeatureSettings = TNUM)
    val ChartAxis      = TextStyle(CapsuleText,    fontWeight = FontWeight.Medium,   fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp, fontFeatureSettings = TNUM)
    val Body           = TextStyle(CapsuleText,    fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val BodySmall      = TextStyle(CapsuleText,    fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val ButtonPrimary  = TextStyle(CapsuleText,    fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp)
    val RangeChip      = TextStyle(CapsuleText,    fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
    val TabLabel       = TextStyle(CapsuleText,    fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp)
    val AllCaps        = TextStyle(CapsuleText,    fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
}

val RobinhoodTypography = Typography(
    displayMedium = RobinhoodText.PortfolioHero,
    headlineSmall = RobinhoodText.ScreenTitle,
    titleMedium   = RobinhoodText.PositionTitle,
    bodyMedium    = RobinhoodText.Body,
    labelSmall    = RobinhoodText.TabLabel,
)
```

## 3. Signature Components

### Portfolio Hero (top of Investing tab)

`BUYING POWER` all-caps label → the giant tabular `$` value in Capsule Display → the day-change line in green (up) or orange (down) — no pill chrome, just colored text. The value morphs in <100ms during chart scrubbing.

```kotlin
import androidx.compose.animation.AnimatedContent
import androidx.compose.animation.core.tween
import androidx.compose.animation.fadeIn
import androidx.compose.animation.fadeOut
import androidx.compose.animation.togetherWith
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import java.text.NumberFormat
import java.util.Locale

private val usd = NumberFormat.getCurrencyInstance(Locale.US)

@Composable
fun PortfolioHero(
    portfolioValue: Double,
    dayChange: Double,
    dayChangePct: Double,
    modifier: Modifier = Modifier,
) {
    val up = dayChange >= 0
    val changeColor = if (up) RobinhoodColors.Green else RobinhoodColors.Red
    val sign = if (up) "+" else "−"

    Column(
        modifier = modifier
            .fillMaxWidth()
            .padding(horizontal = 24.dp, vertical = 16.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Text("BUYING POWER", style = RobinhoodText.AllCaps, color = RobinhoodColors.TextSecondary)
        // Value morphs during scrub
        AnimatedContent(
            targetState = portfolioValue,
            transitionSpec = { (fadeIn(tween(90)) togetherWith fadeOut(tween(90))) },
            label = "portfolioValue",
        ) { v ->
            Text(usd.format(v), style = RobinhoodText.PortfolioHero, color = RobinhoodColors.TextPrimary)
        }
        Text(
            "$sign${usd.format(kotlin.math.abs(dayChange))} ($sign${"%.2f".format(dayChangePct * 100)}%) Today",
            style = RobinhoodText.DayChange,
            color = changeColor,
        )
    }
}
```

### Portfolio Chart with Scrubber (THE Robinhood interaction)

Swift `Charts` has no Compose equivalent that supports the live scrubber idiomatically — draw it with `Canvas` and capture the drag with `pointerInput`. A 2.dp stroke in the day's color, **no fill below the line**, full-bleed. Dragging snaps the bubble to the nearest data point and drives the hero value (hoist `scrubValue` to the screen and feed it back into `PortfolioHero`).

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.gestures.detectDragGestures
import androidx.compose.runtime.*
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.hapticfeedback.HapticFeedbackType

@Composable
fun PortfolioChart(
    points: List<Float>,           // y-values, evenly spaced in x
    isUp: Boolean,
    onScrub: (index: Int?) -> Unit, // null on release → hero snaps back to current
    modifier: Modifier = Modifier,
) {
    val lineColor = if (isUp) RobinhoodColors.Green else RobinhoodColors.Red
    var activeIndex by remember { mutableStateOf<Int?>(null) }
    val haptics = LocalHapticFeedback.current
    val strokePx = with(androidx.compose.ui.platform.LocalDensity.current) { 2.dp.toPx() }

    Canvas(
        modifier = modifier
            .fillMaxWidth()
            .height(220.dp) // 180 on small phones, 240 on Max — derive from windowSizeClass
            .pointerInput(points) {
                detectDragGestures(
                    onDragStart = { off ->
                        val idx = ((off.x / size.width) * (points.size - 1)).toInt().coerceIn(points.indices)
                        activeIndex = idx; onScrub(idx)
                        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                    },
                    onDrag = { change, _ ->
                        val idx = ((change.position.x / size.width) * (points.size - 1)).toInt().coerceIn(points.indices)
                        if (idx != activeIndex) {
                            activeIndex = idx; onScrub(idx) // instant — no smoothing
                        }
                    },
                    onDragEnd = { activeIndex = null; onScrub(null) },
                    onDragCancel = { activeIndex = null; onScrub(null) },
                )
            },
    ) {
        if (points.size < 2) return@Canvas
        val maxV = points.max(); val minV = points.min()
        val range = (maxV - minV).takeIf { it != 0f } ?: 1f
        fun px(i: Int) = size.width * i / (points.size - 1)
        fun py(v: Float) = size.height * (1f - (v - minV) / range)

        val path = Path().apply {
            moveTo(px(0), py(points[0]))
            for (i in 1 until points.size) lineTo(px(i), py(points[i]))
        }
        drawPath(path, color = lineColor, style = Stroke(width = strokePx)) // no fill below

        activeIndex?.let { i ->
            // thin vertical rule + solid black point bubble
            drawLine(RobinhoodColors.TextPrimary, Offset(px(i), 0f), Offset(px(i), size.height), strokeWidth = 1f)
            drawCircle(RobinhoodColors.TextPrimary, radius = 8f, center = Offset(px(i), py(points[i])))
        }
    }
}
```

### Chart Range Chips

Text-only, no container. Active flips to the chart-line color with a 2.dp underline.

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Box

@Composable
fun ChartRangeChips(
    selected: String,
    isUp: Boolean,
    onSelect: (String) -> Unit,
    modifier: Modifier = Modifier,
) {
    val active = if (isUp) RobinhoodColors.Green else RobinhoodColors.Red
    val ranges = listOf("1D", "1W", "1M", "3M", "YTD", "1Y", "5Y", "ALL")
    val haptics = LocalHapticFeedback.current

    Row(
        modifier = modifier.fillMaxWidth().padding(horizontal = 16.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        ranges.forEach { r ->
            val on = r == selected
            Column(
                horizontalAlignment = androidx.compose.ui.Alignment.CenterHorizontally,
                modifier = Modifier
                    .weight(1f)
                    .heightIn(min = 44.dp) // 44dp min touch even though visual is smaller
                    .clickableNoRipple {
                        onSelect(r)
                        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                    },
            ) {
                Text(
                    r,
                    style = RobinhoodText.RangeChip,
                    color = if (on) active else RobinhoodColors.TextSecondary,
                    modifier = Modifier.padding(vertical = 6.dp),
                )
                Box(
                    Modifier
                        .fillMaxWidth()
                        .height(2.dp)
                        .background(if (on) active else Color.Transparent)
                )
            }
        }
    }
}

import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.ui.composed
fun Modifier.clickableNoRipple(onClick: () -> Unit): Modifier = composed {
    clickable(remember { MutableInteractionSource() }, indication = null, onClick = onClick)
}
```

### Position Row

56.dp tall. 32.dp rounded-square logo, company name above tracked ticker + share count, market value + day-change (tabular) right-aligned, 0.5.dp bottom divider.

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.Alignment
import coil.compose.AsyncImage

@Composable
fun PositionRow(
    ticker: String,
    company: String,
    shares: Double,
    marketValue: Double,
    dayChange: Double,
    dayChangePct: Double,
    logoUrl: String?,
    modifier: Modifier = Modifier,
) {
    val up = dayChange >= 0
    val changeColor = if (up) RobinhoodColors.Green else RobinhoodColors.Red
    val sign = if (up) "+" else "−"

    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(56.dp)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        if (logoUrl != null) {
            AsyncImage(
                model = logoUrl,
                contentDescription = null,
                contentScale = ContentScale.Crop,
                modifier = Modifier.size(32.dp).clip(RoundedCornerShape(8.dp)),
            )
        } else {
            Box(
                Modifier.size(32.dp).clip(RoundedCornerShape(8.dp)).background(RobinhoodColors.SurfaceGray),
                contentAlignment = Alignment.Center,
            ) { Text(ticker.take(1), style = RobinhoodText.PositionTitle, color = RobinhoodColors.TextPrimary) }
        }

        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(company, style = RobinhoodText.PositionTitle, color = RobinhoodColors.TextPrimary, maxLines = 1)
            Row(horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(ticker, style = RobinhoodText.Ticker, color = RobinhoodColors.TextSecondary)
                Text("· ${shares.formatShares()} shares", style = RobinhoodText.BodySmall, color = RobinhoodColors.TextSecondary)
            }
        }

        Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(usd.format(marketValue), style = RobinhoodText.PositionValue, color = RobinhoodColors.TextPrimary)
            Text(
                "$sign${usd.format(kotlin.math.abs(dayChange))} ($sign${"%.2f".format(dayChangePct * 100)}%)",
                style = RobinhoodText.PositionChange,
                color = changeColor,
            )
        }
    }
    Spacer(Modifier.fillMaxWidth().height(0.5.dp).background(RobinhoodColors.DividerLight))
}

private fun Double.formatShares(): String =
    if (this % 1.0 == 0.0) toInt().toString() else "%.4f".format(this).trimEnd('0').trimEnd('.')
```

### Trade Button (sticky footer) & Buy/Sell Pair

The universal Trade CTA is **black** — green/orange are reserved for Buy/Sell inside the trade flow.

```kotlin
@Composable
fun TradeButton(label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    Box(
        modifier = modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .height(48.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(RobinhoodColors.TextPrimary) // black, not green
            .clickableNoRipple {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~impact medium
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) { Text(label, style = RobinhoodText.ButtonPrimary, color = Color.White) }
}

@Composable
fun BuySellButtons(onBuy: () -> Unit, onSell: () -> Unit, modifier: Modifier = Modifier) {
    Row(modifier.fillMaxWidth().padding(horizontal = 16.dp), horizontalArrangement = Arrangement.spacedBy(12.dp)) {
        Box(
            Modifier.weight(1f).height(48.dp).clip(RoundedCornerShape(8.dp))
                .background(RobinhoodColors.Red).clickableNoRipple(onSell),
            contentAlignment = Alignment.Center,
        ) { Text("Sell", style = RobinhoodText.ButtonPrimary, color = Color.White) }
        Box(
            Modifier.weight(1f).height(48.dp).clip(RoundedCornerShape(8.dp))
                .background(RobinhoodColors.Green).clickableNoRipple(onBuy),
            contentAlignment = Alignment.Center,
        ) { Text("Buy", style = RobinhoodText.ButtonPrimary, color = Color.White) }
    }
}
```

## 4. The Portfolio Chart Scrubber — wiring the live hero update

§3 draws the chart; this is the data flow that makes the scrubber Robinhood's signature. The screen owns the scrub index; while dragging, the hero shows the scrubbed value and on release snaps back to current. The numeric morph runs in <100ms via `AnimatedContent`.

```kotlin
@Composable
fun InvestingScreen(
    series: List<Pair<Long, Float>>, // (epochMillis, portfolioValue)
    currentValue: Double,
    dayChange: Double,
    dayChangePct: Double,
) {
    var scrubIndex by remember { mutableStateOf<Int?>(null) }
    val isUp = dayChange >= 0
    val shownValue = scrubIndex?.let { series[it].second.toDouble() } ?: currentValue

    Column {
        PortfolioHero(
            portfolioValue = shownValue,                // updates live during scrub
            dayChange = dayChange,
            dayChangePct = dayChangePct,
        )
        PortfolioChart(
            points = series.map { it.second },
            isUp = isUp,
            onScrub = { scrubIndex = it },              // null on release → snaps back
        )
        ChartRangeChips(selected = "1D", isUp = isUp, onSelect = { /* reload series */ })
    }
}
```

Reduce-motion: skip the `AnimatedContent` morph during scrubbing — swap the value instantly (drag is already 60fps with no smoothing).

## 5. Bottom Navigation (Tab Bar)

Five labeled destinations in a Material 3 `NavigationBar`: Investing, Search, Crypto, Cash, Account. Active tint is **black** (not green — green is the direction signal). Solid surface, 0.5.dp top divider, no blur. Below it, a single sticky black Trade footer on the stock-detail screen.

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun RobinhoodBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    val haptics = LocalHapticFeedback.current
    NavigationBar(
        containerColor = RobinhoodColors.CanvasLight,
        tonalElevation = 0.dp,
    ) {
        val tabs = listOf(
            "Investing" to Icons.Filled.ShowChart,
            "Search"    to Icons.Filled.Search,
            "Crypto"    to Icons.Filled.CurrencyBitcoin,
            "Cash"      to Icons.Filled.AccountBalanceWallet,
            "Account"   to Icons.Filled.AccountCircle,
        )
        tabs.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = {
                    onSelect(i)
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // selection
                },
                icon = { Icon(icon, label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = RobinhoodText.TabLabel) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = RobinhoodColors.TextPrimary, // black, not green
                    selectedTextColor   = RobinhoodColors.TextPrimary,
                    unselectedIconColor = RobinhoodColors.TextTertiary,
                    unselectedTextColor = RobinhoodColors.TextTertiary,
                    indicatorColor      = Color.Transparent,
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Chart scrubber drag | `pointerInput` + `detectDragGestures`, snap to nearest index, **no smoothing** (instant) |
| Portfolio value update | `AnimatedContent` cross-fade `tween(90)` — <100ms numeric morph |
| Buy/Sell confirm | `animateFloatAsState` 1 → 0.97 → 1 `spring(dampingRatio = 0.7f)` + `HapticFeedbackType.LongPress` (impact medium) |
| Order placed | green particle burst (`Canvas` + `Animatable` over ~800ms) + `HapticFeedbackType.Confirm` (success) |
| Tab switch | instant icon/label color change + `HapticFeedbackType.TextHandleMove` (selection) |
| Range chip switch | `animateColorAsState` text + underline cross-fade `spring(stiffness = 700f)` |
| Position row tap | `animateColorAsState` bg white → `SurfaceGray` over 150ms before navigating |

```kotlin
// Buy/Sell press scale
@Composable
fun rememberPressScale(interaction: MutableInteractionSource): Float {
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        if (pressed) 0.97f else 1f,
        spring(dampingRatio = 0.7f, stiffness = 500f),
        label = "pressScale",
    )
    return scale
}
```

Haptics: prefer `LocalHapticFeedback`. The scrub tick and tab switch use `HapticFeedbackType.TextHandleMove` (selection-like); Buy/Sell confirm uses `LongPress` (impact medium); order success uses `HapticFeedbackType.Confirm`. For finer control, `Vibrator` `VibrationEffect.createOneShot(10, ...)` approximates iOS's medium impact.

## 7. Icons

Robinhood uses custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. The portfolio chart is hand-drawn (§3, not an icon). Custom marks (success confetti, order states) ship as vector drawables via `ImageVector.vectorResource(...)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Investing (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.ShowChart` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Crypto (tab) | `bitcoinsign.circle` | `Icons.Filled.CurrencyBitcoin` |
| Cash (tab) | `wallet.pass` | `Icons.Filled.AccountBalanceWallet` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Search (in field) | `magnifyingglass` | `Icons.Filled.Search` |
| Set alert | `bell` / `bell.fill` | `Icons.Filled.NotificationsNone` / `Icons.Filled.Notifications` |
| Add to list | `plus.circle` | `Icons.Filled.AddCircleOutline` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Order placed (success) | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Order failed | `xmark.circle.fill` | `Icons.Filled.Cancel` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24**. `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. The chart is a hand-drawn `Canvas` (no extra dependency) so it works at minSdk with no Swift-Charts equivalent needed.
- **Edge-to-edge**: call `enableEdgeToEdge()`. The light canvas wants dark-content system bars (white wants light-content on dark mode). The chart goes edge-to-edge; apply `Scaffold` insets only to the hero, lists, and the sticky Trade footer (clears gesture nav).
- **Tabular numerals are critical**: every `Text` rendering a price, percent, or share count must use `fontFeatureSettings = "tnum"` (baked into the `RobinhoodText` styles). On the fallback font this guarantees columns align as the user scrolls a watchlist — the brand-defining rule.
- **Font scaling**: `sp` honors user scale on body, secondary text, list rows. The portfolio hero (40sp) scales but **clamp at 56sp** to preserve layout; chart axis labels (11sp), range chips, and tab labels are fixed (layout-sensitive) — wrap in a fixed-`fontScale` `LocalDensity`. Position values honor scaling but clamp at 22sp.
- **TalkBack**: the hero should announce "Buying power $24,847.93, up $847.93, up 3.52 percent today" via one merged `semantics`. Position rows: "Apple Inc., A A P L, 4 shares, market value $1,247.92, up $24.13, up 1.97 percent today". The chart needs a `contentDescription` summarizing the trend plus an accessible "scrub to time" affordance (the drag gesture alone is not TalkBack-reachable).
- **Color-blind**: green/orange is the primary direction signal — always pair with the `+`/`−` sign prefix (already in the components) so it is never color-only. When `isHighTextContrastEnabled`/differentiate-without-color is set, prefix an up/down arrow on the change text.
- **Touch targets**: range chips are visually small but must keep a 44.dp min hit (`heightIn(min = 44.dp)`, already applied); Trade button is 48.dp; tab items 56.dp.
- **Contrast**: `#000000` on `#FFFFFF` is maximum; `#5C6166` on white passes AA at 14sp+; `#9B9EA3` (inactive tab labels) passes AA only at 18sp+ — keep the 10sp tab label at `#000000` once selected. Dark mode: green `#00C805` and orange `#FF5000` stay identical (they read electric on `#000000`).
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — the stark white/true-black canvas and the direction-bound green/orange are the brand. A wallpaper-tinted primary would break the "color = direction" contract and the clean tech-product aesthetic.
