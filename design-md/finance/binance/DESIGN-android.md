# Binance (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Binance's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the markets list + order book + trade ticket, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Binance's near-black canvas, single yellow accent, absolute green/red market semantics, sans/mono split with tabular figures) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyColumn` for the high-frequency markets list, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for coin icons. No Material You dynamic color — Binance's yellow + market semantics must hold regardless of wallpaper. Binance is dark-first; a light scheme is provided but should default to dark.

## 1. Color Tokens

```kotlin
// ui/theme/BinanceColors.kt
import androidx.compose.ui.graphics.Color

object BinanceColors {
    // Canvas & Surfaces (Dark — default)
    val Canvas    = Color(0xFF0B0E11)
    val Surface1  = Color(0xFF181A20)
    val Surface2  = Color(0xFF1E2026)
    val Surface3  = Color(0xFF2B3139)
    val Divider   = Color(0xFF2B3139)

    // Canvas & Surfaces (Light — secondary)
    val CanvasLight   = Color(0xFFFFFFFF)
    val Surface1Light = Color(0xFFF5F5F5)
    val DividerLight  = Color(0xFFEAECEF)

    // Brand (single accent)
    val Yellow        = Color(0xFFF0B90B)
    val YellowPressed = Color(0xFFC99400)
    val YellowTint    = Color(0x1FF0B90B) // 12% yellow

    // Market semantics (never invert)
    val Up          = Color(0xFF0ECB81)
    val Down        = Color(0xFFF6465D)
    val UpPressed   = Color(0xFF0BA572)
    val DownPressed = Color(0xFFD93849)
    val AskFill     = Color(0x24F6465D) // ~0.14 alpha
    val BidFill     = Color(0x240ECB81)

    // Text
    val TextPrimary    = Color(0xFFEAECEF)
    val TextSecondary  = Color(0xFF848E9C)
    val TextTertiary   = Color(0xFF5E6673)
    val TextPrimaryLt  = Color(0xFF1E2329)

    // Coin brand colors
    val Btc  = Color(0xFFF7931A)
    val Eth  = Color(0xFF627EEA)
    val Bnb  = Color(0xFFF0B90B)
    val Usdt = Color(0xFF26A17B)
    val Sol  = Color(0xFF14F195)

    // Info
    val Info = Color(0xFF3375BB)
}
```

Wire it into both schemes. Binance is dark-first; the light scheme only restyles canvas + text — yellow/green/red are theme-invariant.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val BinanceDark = darkColorScheme(
    primary        = BinanceColors.Yellow,
    onPrimary      = BinanceColors.Canvas,      // black text on yellow
    background     = BinanceColors.Canvas,
    onBackground   = BinanceColors.TextPrimary,
    surface        = BinanceColors.Surface1,
    onSurface      = BinanceColors.TextPrimary,
    surfaceVariant = BinanceColors.Surface3,
    outline        = BinanceColors.Divider,
    error          = BinanceColors.Down,
)

private val BinanceLight = lightColorScheme(
    primary        = BinanceColors.Yellow,
    onPrimary      = BinanceColors.Canvas,
    background      = BinanceColors.CanvasLight,
    onBackground   = BinanceColors.TextPrimaryLt,
    surface        = BinanceColors.Surface1Light,
    onSurface      = BinanceColors.TextPrimaryLt,
    surfaceVariant = BinanceColors.Surface1Light,
    outline        = BinanceColors.DividerLight,
    error          = BinanceColors.Down,
)

@Composable
fun BinanceTheme(
    dark: Boolean = isSystemInDarkTheme(), // default to dark in practice
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) BinanceDark else BinanceLight,
    typography  = BinanceTypography,
    content     = content,
)
```

## 2. Typography

Binance's UI face is `BinancePlex` (IBM Plex Sans derivative); numbers use `BinancePlex Mono`. Drop **IBM Plex Sans** + **IBM Plex Mono** TTFs in `res/font/` as the open fallback. Rule: words use the sans, all numbers use the mono with **tabular figures** (`FontFeatureSetting("tnum")`).

```kotlin
// ui/theme/BinanceType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val PlexSans = FontFamily(
    Font(R.font.ibm_plex_sans_regular,  FontWeight.Normal),
    Font(R.font.ibm_plex_sans_medium,   FontWeight.Medium),
    Font(R.font.ibm_plex_sans_semibold, FontWeight.SemiBold),
    Font(R.font.ibm_plex_sans_bold,     FontWeight.Bold),
)
val PlexMono = FontFamily(
    Font(R.font.ibm_plex_mono_medium,   FontWeight.Medium),
    Font(R.font.ibm_plex_mono_semibold, FontWeight.SemiBold),
)

private const val TNUM = "tnum" // tabular figures — mandatory on numbers

object BinanceText {
    // UI sans
    val ScreenTitle = TextStyle(PlexSans, fontWeight = FontWeight.Bold,     fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.6).sp)
    val Section     = TextStyle(PlexSans, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val RowTitle    = TextStyle(PlexSans, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 23.sp)
    val Body        = TextStyle(PlexSans, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val ListLabel   = TextStyle(PlexSans, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 18.sp)
    val Meta        = TextStyle(PlexSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 19.sp)
    val Pill        = TextStyle(PlexSans, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp)
    val Caption     = TextStyle(PlexSans, fontWeight = FontWeight.Medium,   fontSize = 12.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(PlexSans, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(PlexSans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 15.sp)

    // Numeric mono — always tabular
    val Balance     = TextStyle(PlexMono, fontWeight = FontWeight.SemiBold, fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.5).sp, fontFeatureSettings = TNUM)
    val Price       = TextStyle(PlexMono, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp, fontFeatureSettings = TNUM)
    val Number      = TextStyle(PlexMono, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 18.sp, fontFeatureSettings = TNUM)
    val MonoCaption = TextStyle(PlexMono, fontWeight = FontWeight.Medium,   fontSize = 12.sp, lineHeight = 16.sp, fontFeatureSettings = TNUM)
    val MonoSmall   = TextStyle(PlexMono, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 14.sp, fontFeatureSettings = TNUM)
}

val BinanceTypography = Typography(
    headlineLarge  = BinanceText.ScreenTitle,
    headlineMedium = BinanceText.Section,
    titleMedium    = BinanceText.RowTitle,
    bodyMedium     = BinanceText.Body,
    labelSmall     = BinanceText.Tab,
)
```

## 3. Signature Components

### Markets List Row

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

@Composable
fun MarketRow(
    symbol: String, quote: String, volume: String,
    price: String, usd: String, changePct: Double,
    iconColor: Color = BinanceColors.Btc,
) {
    val up = changePct >= 0
    Row(
        Modifier
            .fillMaxWidth()
            .background(BinanceColors.Canvas)
            .padding(horizontal = 16.dp, vertical = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box(
            Modifier.size(30.dp).clip(CircleShape).background(iconColor),
            contentAlignment = Alignment.Center,
        ) {
            Text(symbol.take(1), style = BinanceText.ListLabel, color = BinanceColors.Canvas)
        }

        Column(Modifier.weight(1f)) {
            Row {
                Text(symbol, style = BinanceText.ListLabel, color = BinanceColors.TextPrimary)
                Text(" /$quote", style = BinanceText.Caption.copy(fontSize = 11.sp), color = BinanceColors.TextTertiary)
            }
            Text("Vol $volume", style = BinanceText.Caption.copy(fontSize = 11.sp), color = BinanceColors.TextSecondary)
        }

        Column(horizontalAlignment = Alignment.End) {
            Text(price, style = BinanceText.Number, color = BinanceColors.TextPrimary)
            Text(usd, style = BinanceText.MonoSmall, color = BinanceColors.TextSecondary)
        }

        Box(
            Modifier
                .widthIn(min = 64.dp)
                .clip(RoundedCornerShape(4.dp))
                .background(if (up) BinanceColors.Up else BinanceColors.Down)
                .padding(vertical = 6.dp, horizontal = 8.dp),
            contentAlignment = Alignment.Center,
        ) {
            Text("${if (up) "+" else ""}${"%.2f".format(changePct)}%",
                style = BinanceText.Pill, color = Color.White)
        }
    }
    HorizontalDivider(color = BinanceColors.Divider, thickness = 1.dp)
}
```

### Order Book (depth-shaded)

```kotlin
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.fillMaxHeight
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.ui.Alignment

@Composable
fun OrderBookRow(price: String, qty: String, depthRatio: Float, isAsk: Boolean) {
    Box(Modifier.fillMaxWidth().height(22.dp), contentAlignment = Alignment.CenterEnd) {
        Box(
            Modifier
                .fillMaxHeight()
                .fillMaxWidth(depthRatio)
                .background(if (isAsk) BinanceColors.AskFill else BinanceColors.BidFill)
                .align(Alignment.CenterEnd),
        )
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 8.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            Text(price, style = BinanceText.MonoCaption, color = if (isAsk) BinanceColors.Down else BinanceColors.Up)
            Text(qty,   style = BinanceText.MonoCaption, color = BinanceColors.TextPrimary)
        }
    }
}

@Composable
fun SpreadRow(last: String, up: Boolean) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 8.dp, vertical = 6.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Text(last, style = BinanceText.Price.copy(fontSize = 16.sp),
            color = if (up) BinanceColors.Up else BinanceColors.Down)
        Text(if (up) "↑" else "↓", color = if (up) BinanceColors.Up else BinanceColors.Down)
    }
}
```

### Balance Hero

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.outlined.Visibility
import androidx.compose.material3.Icon

@Composable
fun BalanceHero(value: String, currency: String, pnl: String, gain: Boolean) {
    Column(Modifier.padding(horizontal = 16.dp), verticalArrangement = Arrangement.spacedBy(4.dp)) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Text("Est. Total Value", style = BinanceText.Caption, color = BinanceColors.TextSecondary)
            Icon(Icons.Outlined.Visibility, null, tint = BinanceColors.TextSecondary, modifier = Modifier.size(12.dp))
        }
        Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(value, style = BinanceText.Balance, color = BinanceColors.TextPrimary)
            Text(currency, style = BinanceText.Caption.copy(fontSize = 14.sp), color = BinanceColors.TextSecondary,
                modifier = Modifier.padding(bottom = 4.dp))
        }
        Text("$pnl Today", style = BinanceText.MonoCaption,
            color = if (gain) BinanceColors.Up else BinanceColors.Down)
    }
}
```

### Buy / Sell Trade Ticket

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.runtime.*

@Composable
fun TradeTicket() {
    var isBuy by remember { mutableStateOf(true) }
    var pct by remember { mutableIntStateOf(0) }
    val accent = if (isBuy) BinanceColors.Up else BinanceColors.Down

    Column(
        Modifier.fillMaxWidth().background(BinanceColors.Surface1).padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Row(Modifier.clip(RoundedCornerShape(8.dp))) {
            listOf(true, false).forEach { buy ->
                Box(
                    Modifier
                        .weight(1f).height(36.dp)
                        .background(if (isBuy == buy) (if (buy) BinanceColors.Up else BinanceColors.Down) else BinanceColors.Surface3)
                        .clickable { isBuy = buy },
                    contentAlignment = Alignment.Center,
                ) {
                    Text(if (buy) "Buy" else "Sell", style = BinanceText.Button,
                        color = if (isBuy == buy) Color.White else BinanceColors.TextSecondary)
                }
            }
        }

        Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            listOf(25, 50, 75, 100).forEach { p ->
                Box(
                    Modifier
                        .weight(1f)
                        .clip(RoundedCornerShape(500.dp))
                        .background(if (pct == p) BinanceColors.YellowTint else BinanceColors.Surface3)
                        .clickable { pct = p }
                        .padding(vertical = 7.dp),
                    contentAlignment = Alignment.Center,
                ) {
                    Text("$p%", style = BinanceText.Pill,
                        color = if (pct == p) BinanceColors.Yellow else BinanceColors.TextSecondary)
                }
            }
        }

        Box(
            Modifier.fillMaxWidth().height(48.dp).clip(RoundedCornerShape(8.dp)).background(accent)
                .clickable { },
            contentAlignment = Alignment.Center,
        ) {
            Text(if (isBuy) "Buy BTC" else "Sell BTC", style = BinanceText.Button, color = Color.White)
        }
    }
}
```

### Primary (Yellow) Button

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState

@Composable
fun BinancePrimaryButton(title: String, onClick: () -> Unit) {
    val src = remember { MutableInteractionSource() }
    val pressed by src.collectIsPressedAsState()
    Box(
        Modifier
            .fillMaxWidth().height(48.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) BinanceColors.YellowPressed else BinanceColors.Yellow)
            .clickable(interactionSource = src, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = BinanceText.Button, color = BinanceColors.Canvas) // black text
    }
}
```

## 4. Navigation (Bottom Bar)

Binance has a 5-item bottom strip, active in yellow, **no Material tint pill** (Binance has none).

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun BinanceBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = BinanceColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"    to Icons.Filled.Home,
            "Markets" to Icons.Filled.BarChart,
            "Trade"   to Icons.Filled.SwapHoriz,
            "Futures" to Icons.Filled.TrendingUp,
            "Wallets" to Icons.Filled.AccountBalanceWallet,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, Modifier.size(22.dp)) },
                label = { Text(label, style = BinanceText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = BinanceColors.Yellow,
                    selectedTextColor   = BinanceColors.Yellow,
                    unselectedIconColor = BinanceColors.TextTertiary,
                    unselectedTextColor = BinanceColors.TextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill
                ),
            )
        }
    }
}
```

The markets/favorites/spot tabs are a `ScrollableTabRow` (or a custom `Row`) with a 2dp `Yellow` indicator under the active text; the trade screen stacks a candlestick `Canvas`, the depth-shaded order book (`LazyColumn`), and the sticky trade ticket.

## 5. Motion

Binance motion is functional and fast — data must feel reliable, so **no bouncy springs on data**. Quiet 120–250ms `tween`s.

| Moment | Compose recipe |
|--------|----------------|
| Price tick flash | `animateColorAsState` row bg → `Up/Down @ 0.10 alpha` for 150ms then back; trigger on price change |
| Markets tab underline | `animateDpAsState` indicator x `tween(200)` |
| Buy/Sell toggle | segment bg `animateColorAsState` green↔red `tween(180)` |
| Order book reflow | `LazyColumn` + `Modifier.animateItemPlacement(tween(120))` — no spring |
| Convert swap | tiles swap with `tween(250)` vertical translate + soft haptic |
| Sheet present | `ModalBottomSheet` default; scrim fades in |
| Page navigation | Nav slide push `tween(300)` |
| Pull to refresh | yellow circular indicator; haptic on trigger |

```kotlin
// Price tick flash — the app's heartbeat
val flashColor by animateColorAsState(
    targetValue = if (flashing) (if (up) BinanceColors.Up else BinanceColors.Down).copy(alpha = 0.10f)
                  else Color.Transparent,
    animationSpec = tween(150),
    label = "tickFlash",
)
// Modifier.background(flashColor)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for order submit; `HapticFeedbackType.TextHandleMove` (or `HapticFeedbackConstants.CLOCK_TICK` via `LocalView`) for the percentage-chip snap; a success vibration on filled order. Live price ticks are silent.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Coin glyphs should be bundled vector assets (`ImageVector` / `painterResource`), not Material icons.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Markets (tab) | `chart.bar.fill` | `Icons.Filled.BarChart` |
| Trade (tab) | `arrow.left.arrow.right` | `Icons.Filled.SwapHoriz` |
| Futures (tab) | `chart.xyaxis.line` | `Icons.Filled.TrendingUp` |
| Wallets (tab) | `creditcard.fill` | `Icons.Filled.AccountBalanceWallet` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Notifications | `bell` | `Icons.Filled.Notifications` |
| Hide balance | `eye` / `eye.slash` | `Icons.Outlined.Visibility` / `VisibilityOff` |
| Favorite | `star` / `star.fill` | `Icons.Filled.StarBorder` / `Star` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Convert swap | `arrow.up.arrow.down` | `Icons.Filled.SwapVert` |
| Up tick | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Down tick | `arrow.down` | `Icons.Filled.ArrowDownward` |
| Deposit | `arrow.down.to.line` | `Icons.Filled.SouthEast` |
| Withdraw | `arrow.up.to.line` | `Icons.Filled.NorthEast` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Chart type | `chart.bar.xaxis` | `Icons.Filled.CandlestickChart` |
| Settings | `gearshape` | `Icons.Filled.Settings` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; high-frequency `LazyColumn` + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the dark canvas wants light-content system bars. The sticky trade ticket needs `Modifier.navigationBarsPadding()`; amount inputs need `Modifier.imePadding()`.
- **Tabular figures mandatory**: every numeric style sets `fontFeatureSettings = "tnum"`. Without it, the markets list and order book visibly jitter as digits change.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, body, labels. Pin numeric mono columns, % pills, order-book rows, and 10sp tab labels by deriving from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` so columns stay aligned.
- **List performance**: the markets list is high-frequency. Use `LazyColumn` with stable keys (`key = { it.symbol }`), hoist a `derivedStateOf` price map, and only recompose changed rows; throttle socket updates to ~4–10 Hz. Flash only rows whose price changed.
- **TalkBack**: announce a market row as "Bitcoin, 67,284 dollars, up 2.34 percent" via `Modifier.semantics { contentDescription = ... }`; order-book rows announce side + price + quantity; the submit button reads "Buy Bitcoin" / "Sell Bitcoin". Don't announce every price tick — debounce or only announce significant balance changes via `LiveRegionMode.Polite`.
- **Color is not the only signal**: pair every green/red value with a `+`/`−` sign and an up/down arrow glyph (deuteranopia confuses `#0ECB81`/`#F6465D`).
- **Touch targets**: Material guidance is 48.dp. Markets rows are ~56dp (full-row tap); give the order-book rows (22dp) a larger tap area when used to prefill price; percentage chips and Buy/Sell buttons ≥48dp.
- **Contrast**: `#EAECEF` on `#0B0E11` and `#0B0E11` on `#F0B90B` pass WCAG AA. Never put white text on the yellow button. Validate the secondary surface steps (`#181A20`/`#1E2026`/`#2B3139`) keep ≥3:1 separation.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the price-tick flash and Convert flip; keep instant data updates (no fade) and keep the colored sign + arrow.
- **Dark mode**: this is the primary mode — default to `BinanceDark`. The light scheme only swaps canvas (`#FFFFFF`) and primary text (`#1E2329`); **never invert** `Up`/`Down`/`Yellow`. Do **not** enable Material You `dynamicColorScheme()` — Binance's yellow + absolute market semantics must hold regardless of wallpaper. Floating sheets get a 1dp `Divider` border since shadows barely register on `#0B0E11`.
