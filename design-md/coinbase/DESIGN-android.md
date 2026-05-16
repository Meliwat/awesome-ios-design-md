# Coinbase (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Coinbase's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Coinbase's institutional white canvas, single electric-blue accent, brand-color asset icons, tabular-numeral asset columns) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, an inline `Canvas` sparkline instead of Swift Charts, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` if you load remote asset artwork (icons here are color circles + glyphs, so Coil is optional).

## 1. Color Tokens

```kotlin
// ui/theme/CoinbaseColors.kt
import androidx.compose.ui.graphics.Color

object CoinbaseColors {
    // Brand
    val Blue        = Color(0xFF0052FF)
    val BluePressed = Color(0xFF0040CC)
    val BlueTint    = Color(0xFFE5EDFF)
    val BlueDark    = Color(0xFF3B6CFF) // brightened for dark-mode AA
    val Black       = Color(0xFF0A0B0D) // Coinbase Black — slight cool tint, NOT pure #000
    val Charcoal    = Color(0xFF1A1C1F)

    // Crypto asset colors (each token's official brand identity)
    val Bitcoin   = Color(0xFFF7931A)
    val Ethereum  = Color(0xFF627EEA)
    val USDC      = Color(0xFF2775CA)
    val Solana    = Color(0xFF9945FF)
    val Cardano   = Color(0xFF0033AD)
    val Tether    = Color(0xFF26A17B)

    // Canvas & Surfaces (light)
    val Canvas       = Color(0xFFFFFFFF)
    val SurfaceGray  = Color(0xFFF7F8FA)
    val SurfaceGray2 = Color(0xFFEEF0F3)
    val Divider      = Color(0xFFE1E4E8)

    // Text (light)
    val TextPrimary   = Color(0xFF0A0B0D) // Coinbase Black
    val TextSecondary = Color(0xFF5B616E)
    val TextTertiary  = Color(0xFF80868F)
    val TextMuted     = Color(0xFFA0A4AA)

    // Semantic — color binds to direction
    val Success     = Color(0xFF05B169)
    val SuccessTint = Color(0xFFE6F7EF)
    val Loss        = Color(0xFFCF202F)
    val LossTint    = Color(0xFFFCE7E9)
    val Warning     = Color(0xFFF5A623)

    // Dark mode
    val DarkCanvas   = Color(0xFF0A0B0D)
    val DarkSurface1 = Color(0xFF13151A)
    val DarkSurface2 = Color(0xFF1E2026)
    val DarkDivider  = Color(0xFF2A2E36)
    val DarkTextPri  = Color(0xFFFFFFFF)
    val DarkTextSec  = Color(0xFFA0A4AA)
}
```

Coinbase is white-canvas first; wire a `lightColorScheme` and a deep near-black `darkColorScheme`. Coinbase Blue brightens to `#3B6CFF` on dark for AA contrast.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.runtime.Composable

private val CoinbaseLight = lightColorScheme(
    primary        = CoinbaseColors.Blue,
    onPrimary      = CoinbaseColors.Canvas,
    background     = CoinbaseColors.Canvas,
    onBackground   = CoinbaseColors.TextPrimary,
    surface        = CoinbaseColors.Canvas,
    onSurface      = CoinbaseColors.TextPrimary,
    surfaceVariant = CoinbaseColors.SurfaceGray,
    outline        = CoinbaseColors.Divider,
    error          = CoinbaseColors.Loss,
)

private val CoinbaseDark = darkColorScheme(
    primary        = CoinbaseColors.BlueDark, // AA on dark
    onPrimary      = CoinbaseColors.Canvas,
    background     = CoinbaseColors.DarkCanvas,
    onBackground   = CoinbaseColors.DarkTextPri,
    surface        = CoinbaseColors.DarkSurface1,
    onSurface      = CoinbaseColors.DarkTextPri,
    surfaceVariant = CoinbaseColors.DarkSurface2,
    outline        = CoinbaseColors.DarkDivider,
    error          = CoinbaseColors.Loss,
)

@Composable
fun CoinbaseTheme(useDark: Boolean = false, content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (useDark) CoinbaseDark else CoinbaseLight,
        typography  = CoinbaseTypography,
        content     = content,
    )
```

## 2. Typography

Coinbase Sans / Display / Mono are proprietary (Frere-Jones Type, ~2021). Drop the TTFs in `res/font/` (lowercase, snake_case). Fall back to the system sans for Sans/Display and the system monospace for Mono. Apply `fontFeatureSettings = "tnum"` (tabular figures) on every price, percentage, and quantity — non-negotiable for the institutional column.

```kotlin
// ui/theme/CoinbaseType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val CoinbaseSans = FontFamily(
    Font(R.font.coinbase_sans_regular,  FontWeight.Normal),
    Font(R.font.coinbase_sans_medium,   FontWeight.Medium),
    Font(R.font.coinbase_sans_semibold, FontWeight.SemiBold),
    Font(R.font.coinbase_sans_bold,     FontWeight.Bold),
)
val CoinbaseDisplay = FontFamily(
    Font(R.font.coinbase_display_bold, FontWeight.Bold),
)
val CoinbaseMono = FontFamily(
    Font(R.font.coinbase_mono_regular, FontWeight.Normal),
    Font(R.font.coinbase_mono_medium,  FontWeight.Medium),
)

private const val TNUM = "tnum" // tabular figures — apply to every financial number

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object CoinbaseText {
    val PortfolioHero = TextStyle(CoinbaseDisplay, fontWeight = FontWeight.Bold,     fontSize = 40.sp, lineHeight = 40.sp, letterSpacing = (-0.5).sp, fontFeatureSettings = TNUM)
    val BuyAmount     = TextStyle(CoinbaseDisplay, fontWeight = FontWeight.Bold,     fontSize = 56.sp, lineHeight = 56.sp, letterSpacing = (-1.0).sp, fontFeatureSettings = TNUM)
    val ScreenTitle   = TextStyle(CoinbaseDisplay, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val SectionHeader = TextStyle(CoinbaseSans,    fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val AssetTitle    = TextStyle(CoinbaseSans,    fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 21.sp)
    val Ticker        = TextStyle(CoinbaseSans,    fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 17.sp, letterSpacing = 0.3.sp)
    val AssetPrice    = TextStyle(CoinbaseSans,    fontWeight = FontWeight.Medium,   fontSize = 16.sp, lineHeight = 19.sp, fontFeatureSettings = TNUM)
    val AssetChange   = TextStyle(CoinbaseSans,    fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 17.sp, fontFeatureSettings = TNUM)
    val Body          = TextStyle(CoinbaseSans,    fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val BodySmall     = TextStyle(CoinbaseSans,    fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val WalletAddr    = TextStyle(CoinbaseMono,    fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 18.sp)
    val TxHash        = TextStyle(CoinbaseMono,    fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 17.sp)
    val Button        = TextStyle(CoinbaseSans,    fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp)
    val ButtonSmall   = TextStyle(CoinbaseSans,    fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 13.sp)
    val Tab           = TextStyle(CoinbaseSans,    fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val AllCaps       = TextStyle(CoinbaseSans,    fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val RangeChip     = TextStyle(CoinbaseSans,    fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
}

// Map onto Material 3 slots
val CoinbaseTypography = Typography(
    displayLarge  = CoinbaseText.PortfolioHero,
    headlineLarge = CoinbaseText.ScreenTitle,
    titleLarge    = CoinbaseText.SectionHeader,
    titleMedium   = CoinbaseText.AssetTitle,
    bodyMedium    = CoinbaseText.Body,
    labelSmall    = CoinbaseText.Tab,
)
```

## 3. Signature Components

### Portfolio Hero

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import java.text.NumberFormat
import kotlin.math.abs

@Composable
fun PortfolioHero(
    value: Double,
    dayChange: Double,
    dayChangePct: Double,
    modifier: Modifier = Modifier,
) {
    val up = dayChange >= 0
    val sign = if (up) "+" else "−"
    val color = if (up) CoinbaseColors.Success else CoinbaseColors.Loss
    val usd = NumberFormat.getCurrencyInstance()

    Column(
        modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 16.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Text("PORTFOLIO BALANCE", style = CoinbaseText.AllCaps, color = CoinbaseColors.TextSecondary)
        Text(usd.format(value), style = CoinbaseText.PortfolioHero, color = CoinbaseColors.TextPrimary)
        Text(
            "$sign${usd.format(abs(dayChange))} ($sign${"%.2f".format(abs(dayChangePct * 100))}%)",
            style = CoinbaseText.AssetPrice,
            color = color,
        )
    }
}
```

### Asset Row with Mini Sparkline

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.background
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.clip
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.graphics.Path

@Composable
fun AssetRow(
    assetName: String,
    ticker: String,
    holdings: String,
    price: Double,
    dayChangePct: Double,
    iconColor: Color,         // CoinbaseColors.Bitcoin, .Ethereum, .USDC …
    glyph: String,            // "₿", "Ξ", "$"
    sparkline: List<Float>,   // 24h price points
    modifier: Modifier = Modifier,
) {
    val up = dayChangePct >= 0
    val dir = if (up) CoinbaseColors.Success else CoinbaseColors.Loss
    val sign = if (up) "+" else "−"
    val usd = NumberFormat.getCurrencyInstance()

    Row(
        modifier
            .fillMaxWidth()
            .height(64.dp)
            .background(CoinbaseColors.Canvas)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(40.dp).clip(CircleShape).background(iconColor), contentAlignment = Alignment.Center) {
            Text(glyph, style = CoinbaseText.AssetTitle.copy(fontSize = 18.sp), color = Color.White)
        }
        Column(verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(assetName, style = CoinbaseText.AssetTitle, color = CoinbaseColors.TextPrimary)
            Row(horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(ticker, style = CoinbaseText.Ticker, color = CoinbaseColors.TextSecondary)
                Text("· $holdings", style = CoinbaseText.BodySmall, color = CoinbaseColors.TextSecondary)
            }
        }
        Spacer(Modifier.weight(1f))
        MiniSparkline(sparkline, dir, Modifier.size(width = 56.dp, height = 20.dp))
        Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(usd.format(price), style = CoinbaseText.AssetPrice, color = CoinbaseColors.TextPrimary)
            Text("$sign${"%.2f".format(abs(dayChangePct * 100))}%", style = CoinbaseText.AssetChange, color = dir)
        }
    }
}

@Composable
fun MiniSparkline(points: List<Float>, color: Color, modifier: Modifier = Modifier) {
    Canvas(modifier) {
        if (points.size < 2) return@Canvas
        val min = points.min(); val max = points.max()
        val range = (max - min).takeIf { it > 0f } ?: 1f
        val dx = size.width / (points.size - 1)
        val path = Path()
        points.forEachIndexed { i, p ->
            val x = i * dx
            val y = size.height - ((p - min) / range) * size.height
            if (i == 0) path.moveTo(x, y) else path.lineTo(x, y)
        }
        drawPath(path, color, style = Stroke(width = 1.5.dp.toPx(), cap = StrokeCap.Round))
    }
}
```

### 4-Up Action Row (Buy / Sell / Send / Receive)

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ArrowDownward
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material.icons.filled.QrCode
import androidx.compose.material.icons.filled.Send
import androidx.compose.material3.Icon
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun AssetActionRow(
    onBuy: () -> Unit, onSell: () -> Unit, onSend: () -> Unit, onReceive: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(modifier.fillMaxWidth().padding(horizontal = 16.dp), horizontalArrangement = Arrangement.spacedBy(8.dp)) {
        ActionButton(Icons.Filled.ArrowUpward, "Buy", onBuy, Modifier.weight(1f))
        ActionButton(Icons.Filled.ArrowDownward, "Sell", onSell, Modifier.weight(1f))
        ActionButton(Icons.Filled.Send, "Send", onSend, Modifier.weight(1f))
        ActionButton(Icons.Filled.QrCode, "Receive", onReceive, Modifier.weight(1f))
    }
}

@Composable
private fun ActionButton(icon: ImageVector, label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    Column(
        modifier
            .clip(RoundedCornerShape(12.dp))
            .background(CoinbaseColors.SurfaceGray)
            .clickable(remember { MutableInteractionSource() }, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // light — these are previews
                onClick()
            }
            .padding(vertical = 16.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(icon, contentDescription = label, tint = CoinbaseColors.Blue, modifier = Modifier.size(24.dp))
        Text(label, style = CoinbaseText.ButtonSmall, color = CoinbaseColors.TextPrimary)
    }
}
```

### Primary / Secondary CTA

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.draw.scale

@Composable
fun CBPrimaryButton(label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, spring(), label = "cta")
    val haptics = LocalHapticFeedback.current
    Box(
        modifier
            .fillMaxWidth()
            .height(48.dp)
            .scale(scale)
            .clip(RoundedCornerShape(12.dp))
            .background(if (pressed) CoinbaseColors.BluePressed else CoinbaseColors.Blue)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium on Buy confirm
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = CoinbaseText.Button, color = Color.White)
    }
}

@Composable
fun CBSecondaryButton(label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Box(
        modifier
            .fillMaxWidth().height(48.dp)
            .clip(RoundedCornerShape(12.dp))
            .background(CoinbaseColors.SurfaceGray2)
            .clickable(onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = CoinbaseText.Button, color = CoinbaseColors.TextPrimary)
    }
}
```

### Coinbase C-Mark Logomark

A perfect circle with a horizontal line through it — always Coinbase Blue, always geometric.

```kotlin
@Composable
fun CoinbaseCMark(size: androidx.compose.ui.unit.Dp = 28.dp, color: Color = CoinbaseColors.Blue, modifier: Modifier = Modifier) {
    Canvas(modifier.size(size)) {
        val stroke = this.size.minDimension * 0.18f
        drawCircle(color, radius = (this.size.minDimension - stroke) / 2f, style = Stroke(width = stroke))
        val barW = this.size.width * 0.42f
        val barH = this.size.height * 0.16f
        drawRect(
            color,
            topLeft = Offset((this.size.width - barW) / 2f, (this.size.height - barH) / 2f),
            size = androidx.compose.ui.geometry.Size(barW, barH),
        )
    }
}

// Loading variant — slow 1500ms rotation
@Composable
fun CoinbaseCMarkLoading(modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "cmark")
    val deg by t.animateFloat(0f, 360f, infiniteRepeatable(tween(1500, easing = LinearEasing)), label = "rot")
    CoinbaseCMark(modifier = modifier.rotate(deg))
}
```

### Wallet Address Display (Mono — never proportional Sans)

```kotlin
import androidx.compose.material.icons.filled.Check
import androidx.compose.material.icons.filled.ContentCopy
import androidx.compose.ui.platform.LocalClipboardManager
import androidx.compose.ui.text.AnnotatedString
import kotlinx.coroutines.delay

@Composable
fun WalletAddressView(address: String, modifier: Modifier = Modifier) {
    var copied by remember { mutableStateOf(false) }
    val clipboard = LocalClipboardManager.current
    val haptics = LocalHapticFeedback.current
    val truncated = if (address.length > 14) "${address.take(10)}...${address.takeLast(8)}" else address
    LaunchedEffect(copied) { if (copied) { delay(1500); copied = false } }

    Row(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(8.dp))
            .background(CoinbaseColors.SurfaceGray)
            .clickable {
                clipboard.setText(AnnotatedString(address))
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                copied = true
            }
            .padding(12.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Text(truncated, style = CoinbaseText.WalletAddr, color = CoinbaseColors.TextPrimary)
        Spacer(Modifier.weight(1f))
        Icon(
            if (copied) Icons.Filled.Check else Icons.Filled.ContentCopy,
            contentDescription = "Copy address",
            tint = if (copied) CoinbaseColors.Success else CoinbaseColors.TextSecondary,
            modifier = Modifier.size(20.dp),
        )
    }
}
```

## 4. Portfolio Chart with Scrubber (the signature dynamic)

Coinbase has no color extraction — its distinctive dynamic system is the **full-bleed 2dp portfolio line with a finger-tracking scrubber that morphs the hero value in real time**. Build the line on `Canvas`; drag updates the highlighted price via numeric morph (`animateFloatAsState` with a snappy spring on the hero value).

```kotlin
import androidx.compose.foundation.gestures.detectDragGestures
import androidx.compose.ui.input.pointer.pointerInput
import kotlin.math.roundToInt

@Composable
fun PortfolioChart(
    points: List<Float>,         // price series for the selected range
    up: Boolean,
    onScrub: (price: Float?) -> Unit, // null when finger lifts → restore live value
    modifier: Modifier = Modifier,
) {
    val line = if (up) CoinbaseColors.Success else CoinbaseColors.Loss
    var scrubX by remember { mutableStateOf<Float?>(null) }

    Canvas(
        modifier
            .fillMaxWidth()
            .height(180.dp)
            .pointerInput(points) {
                detectDragGestures(
                    onDragStart = { scrubX = it.x },
                    onDrag = { change, _ -> scrubX = change.position.x.coerceIn(0f, size.width.toFloat()) },
                    onDragEnd = { scrubX = null; onScrub(null) },
                )
            },
    ) {
        if (points.size < 2) return@Canvas
        val min = points.min(); val max = points.max()
        val range = (max - min).takeIf { it > 0f } ?: 1f
        val dx = size.width / (points.size - 1)
        val path = Path()
        points.forEachIndexed { i, p ->
            val x = i * dx
            val y = size.height - ((p - min) / range) * size.height
            if (i == 0) path.moveTo(x, y) else path.lineTo(x, y)
        }
        drawPath(path, line, style = Stroke(width = 2.dp.toPx(), cap = StrokeCap.Round))

        scrubX?.let { sx ->
            val idx = (sx / dx).roundToInt().coerceIn(0, points.lastIndex)
            onScrub(points[idx]) // hero value morphs to this; no fill below the line — Coinbase style
            drawLine(CoinbaseColors.Divider, Offset(sx, 0f), Offset(sx, size.height), strokeWidth = 1.dp.toPx())
        }
    }
}

// Hoist the live value; the hero recomposes with a numeric morph during scrubbing:
// val display by animateFloatAsState(scrubbed ?: liveValue, animationSpec = spring(stiffness = 900f), label = "hero")
```

Range chips below the chart (`1H / 1D / 1W / 1M / 1Y / ALL`): transparent default, `SurfaceGray2` selected, text `TextSecondary` → `TextPrimary` when active, 8dp corners.

## 5. Navigation

Coinbase uses a **5-tab bottom bar** (Home / Trade / Cards / Earn / Wallet). iOS uses an opaque white tab bar — Android has no live blur, so use an opaque `Canvas`-white `NavigationBar` with a hairline top divider. Active tint is Coinbase Blue.

```kotlin
import androidx.compose.material.icons.filled.CreditCard
import androidx.compose.material.icons.filled.Home
import androidx.compose.material.icons.filled.Percent
import androidx.compose.material.icons.filled.SwapHoriz
import androidx.compose.material.icons.filled.Wallet
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun CoinbaseBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = CoinbaseColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home" to Icons.Filled.Home,
            "Trade" to Icons.Filled.SwapHoriz,
            "Cards" to Icons.Filled.CreditCard,
            "Earn" to Icons.Filled.Percent,
            "Wallet" to Icons.Filled.Wallet,
        )
        val haptics = LocalHapticFeedback.current
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS .selectionChanged
                    onSelect(i)
                },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = CoinbaseText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = CoinbaseColors.Blue,
                    selectedTextColor = CoinbaseColors.Blue,
                    unselectedIconColor = CoinbaseColors.TextSecondary,
                    unselectedTextColor = CoinbaseColors.TextSecondary,
                    indicatorColor = CoinbaseColors.BlueTint,
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Buy/Sell tap | `animateFloatAsState` 1 → 0.98 with `spring(stiffness ≈ 600f)`; `HapticFeedbackType.LongPress` (~iOS medium) |
| Order placed | success modal slides up 350ms; checkmark `scale` 0.5 → 1.0 spring delayed 100ms; success haptic |
| Chart scrubber drag | instant `detectDragGestures` (no spring on the line); hero value numeric morph via `animateFloatAsState(spring(stiffness = 900f))` |
| Asset row tap | background fade `Canvas` → `SurfaceGray` over 150ms before navigating |
| Tab switch | icon swap + label color `TextSecondary` → `Blue` over 200ms; `HapticFeedbackType.TextHandleMove` |
| C-mark loading | `rememberInfiniteTransition` `rotate` 0 → 360 over 1500ms, `LinearEasing` |
| Wallet copy | 1500ms `Success` toast at top — no confetti |

```kotlin
// Order-confirmation checkmark scale-in
val scale by animateFloatAsState(
    targetValue = if (showCheck) 1f else 0.5f,
    animationSpec = spring(dampingRatio = 0.7f, stiffness = 380f),
    label = "check",
)
Icon(Icons.Filled.CheckCircle, null, tint = CoinbaseColors.Success, modifier = Modifier.scale(scale))
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or `Vibrator` `VibrationEffect.createOneShot(...)` for the order-success notification feel.

## 7. Icons

Coinbase ships a geometric C-mark (drawn on `Canvas` in §3) and per-token asset glyphs. The rest map to `androidx.compose.material:material-icons-extended`; ship custom token glyphs as vector drawables.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Home tab | `house` | `Icons.Filled.Home` |
| Trade tab | `arrow.left.arrow.right` | `Icons.Filled.SwapHoriz` |
| Cards tab | `creditcard` | `Icons.Filled.CreditCard` |
| Earn tab | `percent` | `Icons.Filled.Percent` |
| Wallet tab | `wallet.pass` | `Icons.Filled.Wallet` |
| 4-up Buy | `arrow.up` | `Icons.Filled.ArrowUpward` |
| 4-up Sell | `arrow.down` | `Icons.Filled.ArrowDownward` |
| 4-up Send | `paperplane` | `Icons.Filled.Send` |
| 4-up Receive | `qrcode` | `Icons.Filled.QrCode` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Notifications | `bell` | `Icons.Filled.Notifications` |
| Watchlist | `star` / `star.fill` | `Icons.Outlined.StarBorder` / `Icons.Filled.Star` |
| Back | `chevron.left` | `Icons.Filled.ChevronLeft` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Success | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Pending | `clock.fill` | `Icons.Filled.Schedule` |
| Copy | `doc.on.doc` | `Icons.Filled.ContentCopy` |
| C-mark | (custom) | Custom `Canvas` `CoinbaseCMark` — always geometric |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. White canvas wants dark-content system bars (`isAppearanceLightStatusBars = true`); apply `Scaffold` insets so the tab bar and sticky Buy/Sell footer clear gesture nav.
- **Tabular figures**: every price, percentage, and quantity must carry `fontFeatureSettings = "tnum"` (baked into `CoinbaseText.AssetPrice` / `AssetChange` / `PortfolioHero`) so the asset list aligns into the institutional column.
- **Font scaling**: `sp` honors the user's font scale — keep it on body, asset titles, secondary labels. Pin layout-sensitive text (tab labels, range chips, Mono addresses). Portfolio hero (40sp) scales but cap at 56sp; Buy/Sell entry (56sp) cap at 72sp; asset price scales to 20sp max.
- **TalkBack**: merge the asset row into one node — `Modifier.semantics(mergeDescendants = true) { contentDescription = "Bitcoin, B T C, you own 0.1842, price $67,234.18, up 1.92 percent today" }`. Portfolio hero: announce balance + change. Expand the truncated wallet address to the full string for the announcement.
- **Touch targets**: Material guidance is 48dp minimum. Asset row is 64dp (clear); the 24dp tab icons sit in 48dp+ `NavigationBarItem` slots; ensure range chips and the copy glyph reach 48dp hit area via padding.
- **Contrast**: `#0A0B0D` on white exceeds WCAG AAA; Coinbase Blue `#0052FF` on white clears AAA. The real low-contrast pairs are **`#80868F` TextTertiary on white** and **`#A0A4AA` TextMuted on white** — validate at 11sp tab/all-caps labels and darken toward `#5B616E` if targeting compliance. Pair green/red with directional `+`/`−` and sparkline shape when `differentiateWithoutColor` is set.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, skip the hero numeric morph during scrubbing and the C-mark rotation (crossfade instead).
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Coinbase's brand requires the fixed `#0052FF` electric blue regardless of wallpaper. Strong-brand app: keep the curated scheme.
