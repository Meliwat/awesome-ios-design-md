# Charles Schwab (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Schwab's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the account→trade spine, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Schwab's maritime navy, the single bright-blue action, the navy gradient hero, the trade ticket as centerpiece, tabular money, sacred gain/loss) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Brush.linearGradient` hero, `SegmentedButton`-style toggle, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for any imagery. No color extraction — Schwab's palette is a fixed institutional set. Schwab is light-first but ships a full maritime-navy dark scheme.

## 1. Color Tokens

```kotlin
// ui/theme/SchwabColors.kt
import androidx.compose.ui.graphics.Color

object SchwabColors {
    // Brand
    val Blue        = Color(0xFF009DDC)
    val BluePressed = Color(0xFF0080B5)
    val Navy        = Color(0xFF003B5C)
    val NavyDeep    = Color(0xFF002A42)
    val Sky         = Color(0xFF46BEEC)
    val SkyLight    = Color(0xFF0078A8)
    val OnBlue      = Color(0xFF002233) // text ON Schwab Blue (white fails contrast)

    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val Surface        = Color(0xFFF2F6F8)
    val SurfacePressed = Color(0xFFE6EDF1)
    val Divider        = Color(0xFFDBE3E8)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF0A1622) // maritime navy — NOT pure black
    val DarkSurface1 = Color(0xFF11212F)
    val DarkSurface2 = Color(0xFF18303F)
    val DarkDivider  = Color(0xFF1F3A4A)

    // Text
    val TextPrimary       = Color(0xFF10222E)
    val TextSecondary     = Color(0xFF5A707C)
    val TextTertiary      = Color(0xFF8597A0)
    val DarkTextPrimary   = Color(0xFFE6EEF3)
    val DarkTextSecondary = Color(0xFF9DB2BF)
    val DarkTextTertiary  = Color(0xFF6A8392)
    val OnHero            = Color(0xFFFFFFFF)
    val OnHeroSub         = Color(0xFF9DC4D8)

    // Financial semantics (sacred — gain/loss only)
    val GainLight  = Color(0xFF16895F)
    val GainDark   = Color(0xFF18B07B)
    val LossLight  = Color(0xFFC8443D)
    val LossDark   = Color(0xFFE2564E)
    val OnHeroGain = Color(0xFF5FE3B0)

    // System / accent
    val Warning = Color(0xFFC8862B)
    val Info    = Color(0xFF0078A8)
    val Gold    = Color(0xFFC8A24B)
}

/** Resolve a change value to its semantic color (flat -> secondary text). */
fun changeColor(change: Double, dark: Boolean): Color = when {
    change > 0 -> if (dark) SchwabColors.GainDark else SchwabColors.GainLight
    change < 0 -> if (dark) SchwabColors.LossDark else SchwabColors.LossLight
    else       -> if (dark) SchwabColors.DarkTextSecondary else SchwabColors.TextSecondary
}
```

Wire it into both schemes. Schwab is light-first; the dark scheme uses the signature `#0A1622` maritime navy, never true black. Do **not** enable Material You `dynamicColorScheme()` — the banker-blue identity must hold regardless of wallpaper.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val SchwabLight = lightColorScheme(
    primary        = SchwabColors.Blue,
    onPrimary      = SchwabColors.OnBlue, // dark navy, NOT white
    secondary      = SchwabColors.Navy,
    background     = SchwabColors.Canvas,
    onBackground   = SchwabColors.TextPrimary,
    surface        = SchwabColors.Surface,
    onSurface      = SchwabColors.TextPrimary,
    surfaceVariant = SchwabColors.SurfacePressed,
    outline        = SchwabColors.Divider,
    error          = SchwabColors.LossLight,
)

private val SchwabDark = darkColorScheme(
    primary        = SchwabColors.Blue,
    onPrimary      = SchwabColors.OnBlue,
    secondary      = SchwabColors.Sky,
    background     = SchwabColors.DarkCanvas,
    onBackground   = SchwabColors.DarkTextPrimary,
    surface        = SchwabColors.DarkSurface1,
    onSurface      = SchwabColors.DarkTextPrimary,
    surfaceVariant = SchwabColors.DarkSurface2,
    outline        = SchwabColors.DarkDivider,
    error          = SchwabColors.LossDark,
)

@Composable
fun SchwabTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) SchwabDark else SchwabLight,
    typography = SchwabTypography,
    content = content,
)
```

## 2. Typography

Schwab's brand face is "Charles Modern"; bundle **Source Sans 3** (SIL OFL) in `res/font/` as the closest free match. Defining rule: every monetary figure carries tabular figures via `fontFeatureSettings = "tnum"`.

```kotlin
// ui/theme/SchwabType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val SourceSans = FontFamily(
    Font(R.font.sourcesans3_regular,   FontWeight.Normal),
    Font(R.font.sourcesans3_medium,    FontWeight.Medium),
    Font(R.font.sourcesans3_semibold,  FontWeight.SemiBold),
    Font(R.font.sourcesans3_bold,      FontWeight.Bold),
    Font(R.font.sourcesans3_extrabold, FontWeight.ExtraBold),
)

private const val TNUM = "tnum"

object SchwabText {
    val TotalValue  = TextStyle(SourceSans, fontWeight = FontWeight.ExtraBold, fontSize = 34.sp, lineHeight = 38.sp, letterSpacing = (-0.6).sp, fontFeatureSettings = TNUM)
    val ScreenTitle = TextStyle(SourceSans, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(SourceSans, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(SourceSans, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val Body        = TextStyle(SourceSans, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val RowValue    = TextStyle(SourceSans, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 21.sp, fontFeatureSettings = TNUM)
    val AcctName    = TextStyle(SourceSans, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(SourceSans, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val NumericMono = TextStyle(SourceSans, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 17.sp, fontFeatureSettings = TNUM)
    val Caption     = TextStyle(SourceSans, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 16.sp)
    val Button      = TextStyle(SourceSans, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Tab         = TextStyle(SourceSans, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val TicketField = TextStyle(SourceSans, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 16.sp, fontFeatureSettings = TNUM)
}

val SchwabTypography = Typography(
    headlineLarge  = SchwabText.TotalValue,
    headlineMedium = SchwabText.ScreenTitle,
    titleMedium    = SchwabText.CardTitle,
    bodyMedium     = SchwabText.Body,
    labelSmall     = SchwabText.Tab,
)
```

## 3. Signature Components

### Account Hero (navy gradient — the lead)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Notifications
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.unit.dp

@Composable
fun AccountHero(
    title: String, totalValue: String,
    changeAmount: String, changePercent: String, change: Double,
    onBell: () -> Unit,
) {
    Column(
        Modifier.fillMaxWidth()
            .background(Brush.linearGradient(
                listOf(SchwabColors.Navy, SchwabColors.NavyDeep),
                start = Offset(0f, 0f), end = Offset.Infinite))
            .padding(horizontal = 20.dp).padding(top = 8.dp, bottom = 22.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically) {
            Text(title, style = SchwabText.ScreenTitle, color = SchwabColors.OnHero, modifier = Modifier.weight(1f))
            IconButton(onClick = onBell, modifier = Modifier.size(34.dp)
                .clip(CircleShape).background(SchwabColors.OnHero.copy(alpha = 0.10f))) {
                Icon(Icons.Filled.Notifications, contentDescription = "Alerts", tint = SchwabColors.OnHero, modifier = Modifier.size(16.dp))
            }
        }
        Text("TOTAL VALUE", style = SchwabText.Caption, color = SchwabColors.OnHeroSub,
             modifier = Modifier.padding(top = 18.dp))
        Text(totalValue, style = SchwabText.TotalValue, color = SchwabColors.OnHero,
             modifier = Modifier.padding(top = 4.dp))
        Row(Modifier.padding(top = 8.dp), verticalAlignment = Alignment.CenterVertically) {
            Text(if (change >= 0) "▲ " else "▼ ", color = SchwabColors.OnHeroGain, style = SchwabText.NumericMono)
            Text("$changeAmount ", style = SchwabText.NumericMono, color = SchwabColors.OnHeroGain)
            Text("($changePercent) today", style = SchwabText.NumericMono, color = SchwabColors.OnHeroSub)
        }
    }
}
```

### Account Card / List Cell

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.filled.*
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun AccountCard(
    icon: androidx.compose.ui.graphics.vector.ImageVector,
    name: String, mask: String, value: String, changePct: String, change: Double, dark: Boolean,
) {
    val primary = if (dark) SchwabColors.DarkTextPrimary else SchwabColors.TextPrimary
    val secondary = if (dark) SchwabColors.DarkTextSecondary else SchwabColors.TextSecondary
    val surface2 = if (dark) SchwabColors.DarkSurface2 else SchwabColors.Surface
    val sky = if (dark) SchwabColors.Sky else SchwabColors.SkyLight

    Column {
        Row(
            Modifier.fillMaxWidth().height(66.dp).clickable {}.padding(horizontal = 20.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(Modifier.size(38.dp).clip(RoundedCornerShape(10.dp)).background(surface2),
                contentAlignment = Alignment.Center) {
                Icon(icon, contentDescription = null, tint = sky, modifier = Modifier.size(18.dp))
            }
            Column(Modifier.weight(1f)) {
                Text(name, style = SchwabText.AcctName, color = primary)
                Text(mask, style = SchwabText.Caption, color = secondary, maxLines = 1, overflow = TextOverflow.Ellipsis)
            }
            Column(horizontalAlignment = Alignment.End) {
                Text(value, style = SchwabText.RowValue, color = primary)
                Text(changePct, style = SchwabText.NumericMono, color = changeColor(change, dark))
            }
        }
        Box(Modifier.fillMaxWidth().height(0.5.dp)
            .background(if (dark) SchwabColors.DarkDivider else SchwabColors.Divider))
    }
}
```

### Trade Ticket (the centerpiece)

```kotlin
import androidx.compose.animation.core.tween
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults

@Composable
fun TradeTicket(fields: List<Pair<String, String>>, onReview: () -> Unit) {
    var side by remember { mutableIntStateOf(0) }
    val haptics = LocalHapticFeedback.current

    Column(
        Modifier.fillMaxWidth().clip(RoundedCornerShape(12.dp))
            .background(SchwabColors.DarkSurface2).padding(16.dp),
    ) {
        Row(
            Modifier.clip(RoundedCornerShape(8.dp)).background(SchwabColors.DarkCanvas).padding(3.dp),
            horizontalArrangement = Arrangement.spacedBy(3.dp),
        ) {
            listOf("Buy", "Sell").forEachIndexed { i, label ->
                Box(
                    Modifier.weight(1f).height(36.dp).clip(RoundedCornerShape(6.dp))
                        .background(if (i == side) SchwabColors.Blue else Color.Transparent)
                        .clickable {
                            side = i
                            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                        },
                    contentAlignment = Alignment.Center,
                ) {
                    Text(label, style = SchwabText.NumericMono,
                        color = if (i == side) SchwabColors.OnBlue else SchwabColors.DarkTextSecondary)
                }
            }
        }

        fields.forEachIndexed { idx, (label, value) ->
            Row(
                Modifier.fillMaxWidth().padding(vertical = 12.dp),
                horizontalArrangement = Arrangement.SpaceBetween,
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Text(label, style = SchwabText.NumericMono, color = SchwabColors.DarkTextSecondary)
                Text(value, style = SchwabText.TicketField, color = SchwabColors.DarkTextPrimary)
            }
            if (idx != fields.lastIndex) {
                Box(Modifier.fillMaxWidth().height(1.dp).background(SchwabColors.DarkDivider))
            }
        }

        Button(
            onClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onReview() },
            shape = RoundedCornerShape(8.dp),
            colors = ButtonDefaults.buttonColors(containerColor = SchwabColors.Blue, contentColor = SchwabColors.OnBlue),
            modifier = Modifier.fillMaxWidth().height(52.dp).padding(top = 14.dp),
        ) { Text("Review order", style = SchwabText.Button) }
    }
}
```

### Sticky Trade Button & Balances Tiles

```kotlin
@Composable
fun StickyTradeBar(onTrade: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    Column(
        Modifier.fillMaxWidth()
            .background(SchwabColors.DarkCanvas.copy(alpha = 0.94f))
            .navigationBarsPadding(),
    ) {
        Box(Modifier.fillMaxWidth().height(0.5.dp).background(SchwabColors.DarkDivider))
        Button(
            onClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onTrade() },
            shape = RoundedCornerShape(8.dp),
            colors = ButtonDefaults.buttonColors(containerColor = SchwabColors.Blue, contentColor = SchwabColors.OnBlue),
            modifier = Modifier.fillMaxWidth().height(52.dp).padding(horizontal = 16.dp).padding(top = 12.dp),
        ) { Text("Trade", style = SchwabText.Button) }
    }
}

@Composable
fun BalancesTiles(tiles: List<Triple<String, String, Double?>>, dark: Boolean) {
    val bg = if (dark) SchwabColors.DarkSurface2 else SchwabColors.Surface
    val primary = if (dark) SchwabColors.DarkTextPrimary else SchwabColors.TextPrimary
    val secondary = if (dark) SchwabColors.DarkTextSecondary else SchwabColors.TextSecondary
    Column(verticalArrangement = Arrangement.spacedBy(12.dp)) {
        tiles.chunked(2).forEach { row ->
            Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
                row.forEach { (label, value, change) ->
                    Column(
                        Modifier.weight(1f).clip(RoundedCornerShape(12.dp)).background(bg)
                            .padding(horizontal = 16.dp, vertical = 14.dp),
                    ) {
                        Text(label, style = SchwabText.Caption, color = secondary)
                        Text(value, style = SchwabText.TicketField.copy(fontSize = 19.sp),
                            color = change?.let { changeColor(it, dark) } ?: primary,
                            modifier = Modifier.padding(top = 6.dp))
                    }
                }
            }
        }
    }
}
```

## 4. Navigation

Schwab has a five-tab bottom strip. On Android model it as a `NavigationBar`; there is no Material tint pill — active is just the sky-blue icon/label.

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun SchwabBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = SchwabColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Accounts" to Icons.Filled.Home,
            "Trade"    to Icons.Filled.TrendingUp,
            "Research" to Icons.Filled.Search,
            "Messages" to Icons.Filled.Email,
            "More"     to Icons.Filled.GridView,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = SchwabText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = SchwabColors.Sky,
                    selectedTextColor = SchwabColors.Sky,
                    unselectedIconColor = SchwabColors.DarkTextTertiary,
                    unselectedTextColor = SchwabColors.DarkTextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill
                ),
            )
        }
    }
}
```

The account→trade spine is a `NavHost` slide push: Accounts → Account detail (`TabRow` underline: Positions/Balances/Activity) → Quote → Trade ticket (the `TradeTicket` composable) → Review → confirmation.

## 5. Motion

Schwab motion is calm — 120–300ms ease-out, never aggressive on money.

| Moment | Compose recipe |
|--------|----------------|
| Balance tick | animate text color only via `animateColorAsState(tween(600))`, settle to primary — digits never roll |
| Trade Buy/Sell | active pill `animateOffsetAsState`/`Modifier.offset` `tween(200)` between segments |
| Chart range morph | re-path Canvas; `animateFloatAsState(tween(280))` interpolating point arrays |
| Push navigation | `NavHost` slide `tween(300)` |
| Sticky Trade button | always present; top divider visible as content scrolls beneath |
| Tab switch | instant content swap; icon color cross-fade `tween(120)` |
| Pull-to-refresh | `PullToRefreshContainer` tinted `SchwabColors.Blue`; light haptic on trigger |

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for tab/segmented/range change; `HapticFeedbackType.LongPress` for tap-Trade / Place-order; on order accepted use `view.performHapticFeedback(HapticFeedbackConstants.CONFIRM)`.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. For directional gain/loss prefer Unicode triangles `▲ ▼`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Accounts (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Trade (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Research (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Messages (tab) | `envelope.fill` | `Icons.Filled.Email` |
| More (tab) | `square.grid.2x2.fill` | `Icons.Filled.GridView` |
| Brokerage account | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Retirement account | `clock` | `Icons.Filled.Schedule` |
| Bank account | `creditcard` | `Icons.Filled.CreditCard` |
| Alerts bell | `bell.fill` | `Icons.Filled.Notifications` |
| Day change up | `arrowtriangle.up.fill` | `▲` (text) / `Icons.Filled.ArrowDropUp` |
| Day change down | `arrowtriangle.down.fill` | `▼` (text) / `Icons.Filled.ArrowDropDown` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Disclosure | `chevron.right` | `Icons.Filled.ChevronRight` |
| Transfer | `arrow.left.arrow.right` | `Icons.Filled.SwapHoriz` |
| Documents | `doc.text` | `Icons.Filled.Description` |
| Order type | `slider.horizontal.3` | `Icons.Filled.Tune` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24**, `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; let the navy hero gradient draw under the status bar (use `statusBarsPadding()` only on non-hero screens). The sticky Trade bar uses `Modifier.navigationBarsPadding()`; the trade keypad uses `Modifier.imePadding()`.
- **Schwab Blue contrast**: `onPrimary` is `OnBlue` (`#002233`), never white — white-on-`#009DDC` fails WCAG at body sizes. Enforced in the color scheme above.
- **Font scaling**: `sp` honors the user's font scale — keep it on total value, titles, body, meta. Pin layout-sensitive text (10sp tab labels, ticket field values, table headers) via `dp`-derived sizes. Tabular figures must stay tabular at every scale (`fontFeatureSettings = "tnum"`).
- **Do not rely on color alone**: gain/loss must always carry `+`/`−` and `▲`/`▼` (components above do). Hard requirement for a finance app.
- **TalkBack**: announce an account row as one node — "Roth IRA, Retirement ending 8820, 158,330 dollars 7 cents, up 0.71 percent". Use `Modifier.semantics(mergeDescendants = true)`; never expose color as the only state.
- **Touch targets**: Material guidance is 48.dp — account rows are 66.dp (fine); give the bell button and segmented segments a 48.dp hit area via padding. Primary buttons ≥ 48.dp.
- **Contrast**: `#009DDC` + `#002233` text passes AA at 16sp bold; `#18B07B`/`#E2564E` on `#0A1622` pass AA at the bold numeric weights; `#5FE3B0` on the navy hero passes AA. Validate any custom pair.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, snap the Buy/Sell pill, skip the chart morph and balance-tick color animation (set final state instantly); keep the static sign/arrow.
- **Dark mode**: invert via the `Dark*` palette — `#0A1622`, NOT true black; `#10222E` text becomes `#E6EEF3`; keep the navy hero gradient on dark. Shadows vanish on the navy canvas, so add a 1dp `DarkDivider` border to cards, the ticket, and sheets as the elevation cue. Do **not** enable `dynamicColorScheme()` — the banker-blue identity must not harmonize with wallpaper.
