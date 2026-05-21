# Chime (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Chime's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the mint balance hero, the transaction row, the SpotMe banner, the instant alert, navigation, and motion.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Chime's mint-everything personality, the gradient balance hero, the calm green-charcoal dark canvas, neutral-not-red spending) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Snackbar`/animated banner instead of a SwiftUI overlay, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for any avatars. No color extraction — Chime's palette is fixed mint, so Palette is not needed. Chime is light-mode-first; a full dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/ChimeColors.kt
import androidx.compose.ui.graphics.Color

object ChimeColors {
    // Brand (Mint)
    val Mint        = Color(0xFF1EC677)
    val MintBright  = Color(0xFF00D67E)
    val MintPressed = Color(0xFF16A862)
    val MintSoft      = Color(0xFFE4F7EE) // light
    val MintSoftDark  = Color(0xFF123A2A) // dark
    val SpotMe        = Color(0xFF12B981) // light
    val SpotMeDark    = Color(0xFF2EE6A6) // dark
    val BalanceInk  = Color(0xFF062014)

    // Canvas & Surfaces (Light)
    val Canvas   = Color(0xFFF6FBF8) // soft green off-white, NOT pure white
    val Surface1 = Color(0xFFFFFFFF)
    val Surface2 = Color(0xFFF0F6F2)
    val Divider  = Color(0xFFE4EDE8)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF0E1411) // green-charcoal, NOT neutral black
    val DarkSurface1 = Color(0xFF16201B)
    val DarkSurface2 = Color(0xFF1F2C25)
    val DarkDivider  = Color(0xFF243029)

    // Text
    val TextPrimary       = Color(0xFF0F1A14)
    val TextSecondary     = Color(0xFF5C6E63)
    val TextTertiary      = Color(0xFF90A399)
    val DarkTextPrimary   = Color(0xFFEAF2EC)
    val DarkTextSecondary = Color(0xFF9DB0A4)

    // Semantic
    val Positive     = Color(0xFF0FAE63) // light
    val PositiveDark = Color(0xFF00D67E) // dark
    val Negative     = Color(0xFFE5484D) // light
    val NegativeDark = Color(0xFFFF6B6B) // dark
    val Warning      = Color(0xFFE08600) // light
    val WarningDark  = Color(0xFFFFB23E) // dark
}
```

Wire it into both schemes. Chime is light-first (soft mint paper); the dark scheme uses the signature green-charcoal `#0E1411`, never neutral black, and mint stays the brand's whole personality.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val ChimeLight = lightColorScheme(
    primary        = ChimeColors.Mint,
    onPrimary      = ChimeColors.BalanceInk,
    background      = ChimeColors.Canvas,
    onBackground    = ChimeColors.TextPrimary,
    surface         = ChimeColors.Surface1,
    onSurface       = ChimeColors.TextPrimary,
    surfaceVariant  = ChimeColors.Surface2,
    outline         = ChimeColors.Divider,
    error           = ChimeColors.Negative,
)

private val ChimeDark = darkColorScheme(
    primary        = ChimeColors.MintBright,
    onPrimary      = ChimeColors.BalanceInk,
    background      = ChimeColors.DarkCanvas,
    onBackground    = ChimeColors.DarkTextPrimary,
    surface         = ChimeColors.DarkSurface1,
    onSurface       = ChimeColors.DarkTextPrimary,
    surfaceVariant  = ChimeColors.DarkSurface2,
    outline         = ChimeColors.DarkDivider,
    error           = ChimeColors.NegativeDark,
)

@Composable
fun ChimeTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) ChimeDark else ChimeLight,
    typography = ChimeTypography,
    content = content,
)
```

## 2. Typography

Chime's type is a clean geometric sans — **DM Sans** is the faithful free stand-in (SIL OFL; drop the TTFs in `res/font/`). Numbers always 700 and tabular (`FontFeature` `tnum`).

```kotlin
// ui/theme/ChimeType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextGeometricTransform
import androidx.compose.ui.unit.sp

val DMSans = FontFamily(
    Font(R.font.dmsans_regular,  FontWeight.Normal),
    Font(R.font.dmsans_medium,   FontWeight.Medium),
    Font(R.font.dmsans_semibold, FontWeight.SemiBold),
    Font(R.font.dmsans_bold,     FontWeight.Bold),
)

private const val TNUM = "tnum"  // pass via fontFeatureSettings on amount Text

object ChimeText {
    val Balance      = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 44.sp, lineHeight = 46.sp, letterSpacing = (-1.5).sp, fontFeatureSettings = TNUM)
    val BalanceCents = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 26.sp, lineHeight = 28.sp, fontFeatureSettings = TNUM)
    val ScreenTitle  = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val Section      = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val CardTitle    = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 18.sp, lineHeight = 23.sp)
    val Body         = TextStyle(DMSans, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val RowTitle     = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Amount       = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 18.sp, fontFeatureSettings = TNUM)
    val Meta         = TextStyle(DMSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 19.sp)
    val Caption      = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 17.sp, letterSpacing = 0.1.sp)
    val Button       = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 16.sp)
    val Tab          = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Chip         = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Link         = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 13.sp, lineHeight = 17.sp)
}

val ChimeTypography = Typography(
    headlineLarge  = ChimeText.ScreenTitle,
    headlineMedium = ChimeText.Section,
    titleMedium    = ChimeText.CardTitle,
    bodyMedium     = ChimeText.Body,
    labelSmall     = ChimeText.Tab,
)
```

## 3. Signature Components

### Balance Hero Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

@Composable
fun ChimeBalanceHero(
    dollars: String, cents: String,
    onMove: () -> Unit, onPay: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .shadow(16.dp, RoundedCornerShape(24.dp), spotColor = ChimeColors.Mint.copy(alpha = 0.45f))
            .clip(RoundedCornerShape(24.dp))
            .background(Brush.linearGradient(listOf(ChimeColors.Mint, Color(0xFF12A862))))
            .padding(22.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Icon(Icons.Filled.CreditCard, null, tint = ChimeColors.BalanceInk.copy(alpha = 0.7f), modifier = Modifier.size(13.dp))
            Text("Checking", style = ChimeText.Caption, color = ChimeColors.BalanceInk.copy(alpha = 0.7f))
        }
        Row(Modifier.padding(top = 6.dp), verticalAlignment = Alignment.Top) {
            Text(dollars, style = ChimeText.Balance, color = ChimeColors.BalanceInk)
            Text(".$cents", style = ChimeText.BalanceCents, color = ChimeColors.BalanceInk, modifier = Modifier.padding(top = 4.dp))
        }
        Text("Available to spend", style = ChimeText.Caption,
            color = ChimeColors.BalanceInk.copy(alpha = 0.65f), modifier = Modifier.padding(top = 4.dp))
        Row(Modifier.padding(top = 20.dp), horizontalArrangement = Arrangement.spacedBy(10.dp)) {
            HeroBtn(Icons.Filled.SwapHoriz, "Move money", Modifier.weight(1f), onMove)
            HeroBtn(Icons.Filled.Send, "Pay anyone", Modifier.weight(1f), onPay)
        }
    }
}

@Composable
private fun HeroBtn(icon: androidx.compose.ui.graphics.vector.ImageVector, label: String, modifier: Modifier, onClick: () -> Unit) {
    Row(
        modifier
            .height(40.dp)
            .clip(RoundedCornerShape(12.dp))
            .background(ChimeColors.BalanceInk.copy(alpha = 0.14f))
            .clickable(onClick = onClick),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.Center,
    ) {
        Icon(icon, null, tint = ChimeColors.BalanceInk, modifier = Modifier.size(15.dp))
        Spacer(Modifier.width(6.dp))
        Text(label, style = ChimeText.Link, color = ChimeColors.BalanceInk)
    }
}
```

### Transaction Row

```kotlin
sealed interface Tile {
    data class Merchant(val initial: String) : Tile
    data class Action(val icon: androidx.compose.ui.graphics.vector.ImageVector) : Tile
}

@Composable
fun ChimeTransactionRow(
    tile: Tile, name: String, meta: String, amount: String, positive: Boolean,
) {
    Row(
        Modifier.fillMaxWidth().padding(vertical = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        when (tile) {
            is Tile.Merchant -> Box(
                Modifier.size(42.dp).clip(RoundedCornerShape(14.dp))
                    .background(Brush.linearGradient(listOf(Color(0xFF2A3A33), Color(0xFF1A2620)))),
                contentAlignment = Alignment.Center,
            ) { Text(tile.initial, style = ChimeText.Amount, color = Color.White) }
            is Tile.Action -> Box(
                Modifier.size(42.dp).clip(RoundedCornerShape(14.dp)).background(ChimeColors.MintSoft),
                contentAlignment = Alignment.Center,
            ) { Icon(tile.icon, null, tint = ChimeColors.SpotMe, modifier = Modifier.size(19.dp)) }
        }
        Column(Modifier.weight(1f)) {
            Text(name, style = ChimeText.RowTitle, color = ChimeColors.TextPrimary)
            Text(meta, style = ChimeText.Meta, color = ChimeColors.TextSecondary)
        }
        Text(amount, style = ChimeText.Amount,
            color = if (positive) ChimeColors.Positive else ChimeColors.TextPrimary)
    }
    HorizontalDivider(color = ChimeColors.Divider)
}
```

### SpotMe Banner & Instant Alert

```kotlin
@Composable
fun ChimeSpotMeBanner(limit: String, modifier: Modifier = Modifier) {
    Row(
        modifier.fillMaxWidth().padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(16.dp)).background(ChimeColors.Surface1)
            .border(1.dp, ChimeColors.Divider, RoundedCornerShape(16.dp)).padding(14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(38.dp).clip(RoundedCornerShape(12.dp)).background(ChimeColors.MintSoft),
            contentAlignment = Alignment.Center) {
            Icon(Icons.Filled.Star, null, tint = ChimeColors.SpotMe, modifier = Modifier.size(19.dp))
        }
        Column(Modifier.weight(1f)) {
            Text("SpotMe is on", style = ChimeText.RowTitle.copy(fontWeight = FontWeight.Bold), color = ChimeColors.TextPrimary)
            Text("Overdraft up to your limit, fee-free", style = ChimeText.Caption, color = ChimeColors.TextSecondary)
        }
        Text(limit, style = ChimeText.Amount, color = ChimeColors.SpotMe)
    }
}

@Composable
fun ChimeInstantAlert(title: String, sub: String, amount: String, visible: Boolean) {
    AnimatedVisibility(
        visible = visible,
        enter = slideInVertically { -it } + fadeIn(),
        exit  = slideOutVertically { -it } + fadeOut(tween(250)),
    ) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 16.dp)
                .shadow(14.dp, RoundedCornerShape(18.dp), spotColor = ChimeColors.TextPrimary.copy(alpha = 0.16f))
                .clip(RoundedCornerShape(18.dp)).background(ChimeColors.Surface2)
                .border(1.dp, ChimeColors.Divider, RoundedCornerShape(18.dp)).padding(14.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(14.dp),
        ) {
            Box(Modifier.size(44.dp).clip(CircleShape).background(ChimeColors.Mint),
                contentAlignment = Alignment.Center) {
                Icon(Icons.Filled.Check, null, tint = ChimeColors.BalanceInk, modifier = Modifier.size(22.dp))
            }
            Column(Modifier.weight(1f)) {
                Text(title, style = ChimeText.RowTitle.copy(fontWeight = FontWeight.Bold), color = ChimeColors.TextPrimary)
                Text(sub, style = ChimeText.Caption, color = ChimeColors.TextSecondary)
            }
            Text(amount, style = ChimeText.CardTitle, color = ChimeColors.Positive)
        }
    }
}
```

### Primary Button & Status Chip

```kotlin
@Composable
fun ChimePrimaryButton(text: String, modifier: Modifier = Modifier, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        modifier.heightIn(min = 52.dp).clip(RoundedCornerShape(14.dp))
            .background(if (pressed) ChimeColors.MintPressed else ChimeColors.Mint)
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) { Text(text, style = ChimeText.Button, color = ChimeColors.BalanceInk, modifier = Modifier.padding(horizontal = 28.dp)) }
}

enum class ChimeStatus { Posted, Pending, SpotMe, Declined }

@Composable
fun ChimeStatusChip(status: ChimeStatus) {
    val (bg, fg, label) = when (status) {
        ChimeStatus.Posted   -> Triple(ChimeColors.MintSoft, ChimeColors.Positive, "Posted")
        ChimeStatus.Pending  -> Triple(ChimeColors.Warning.copy(alpha = 0.15f), ChimeColors.Warning, "Pending")
        ChimeStatus.SpotMe   -> Triple(ChimeColors.SpotMe.copy(alpha = 0.14f), ChimeColors.SpotMe, "SpotMe covered")
        ChimeStatus.Declined -> Triple(ChimeColors.Negative.copy(alpha = 0.15f), ChimeColors.Negative, "Declined")
    }
    Box(Modifier.clip(RoundedCornerShape(50)).background(bg).padding(horizontal = 14.dp, vertical = 7.dp)) {
        Text(label, style = ChimeText.Chip, color = fg)
    }
}
```

## 4. Navigation

Chime has a 5-tab bottom strip — Home, Move, Card, Grow, Me. On Android, model it as a `NavigationBar`; active is bright mint with the filled icon (no Material tint pill).

```kotlin
@Composable
fun ChimeBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = ChimeColors.Surface1, tonalElevation = 0.dp) {
        val items = listOf(
            "Home" to Icons.Filled.Home,
            "Move" to Icons.Filled.SwapHoriz,
            "Card" to Icons.Filled.CreditCard,
            "Grow" to Icons.Filled.TrendingUp,
            "Me"   to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = ChimeText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = ChimeColors.MintBright,
                    selectedTextColor = ChimeColors.MintBright,
                    unselectedIconColor = ChimeColors.TextTertiary,
                    unselectedTextColor = ChimeColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Chime has none
                ),
            )
        }
    }
}
```

The Move money / Pay anyone flow is a Material 3 `ModalBottomSheet` (`Surface1` / `DarkSurface1`, 20dp top corners) with a contact list, a huge centered amount entry, and a pinned mint primary CTA. The home screen's top "header" is just a `Row` (greeting + bell + avatar), not a `TopAppBar`.

## 5. Motion

Chime motion is soft — gentle springs and ease-outs, gentle haptics. The signature is the balance count-up and the slide-down instant alert.

| Moment | Compose recipe |
|--------|----------------|
| Balance count-up | `animateFloatAsState` previous → new, `tween(600, easing = EaseOut)`, format as currency |
| Instant alert | `AnimatedVisibility` `slideInVertically { -it } + fadeIn` / `slideOutVertically { -it } + fadeOut`; auto-dismiss `LaunchedEffect { delay(4000); visible = false }` |
| SpotMe boost | limit `Text` scale `animateFloatAsState` `keyframes` 1f → 1.15f → 1f spring + medium haptic |
| Transfer success | full-screen mint check `drawWithCache` stroke `animateFloatAsState` 0 → 1 `tween(500)` + success haptic |
| New transaction insert | `LazyColumn` item `animateItemPlacement(tween(250))` + `AnimatedVisibility` fade/slide |
| Tab switch | instant; active icon → bright mint, no slide |
| Pull to refresh | `PullToRefreshContainer` with a mint indicator; re-count balance if changed |

```kotlin
// Balance count-up — the canonical Chime motion
val shown by animateFloatAsState(
    targetValue = balance,
    animationSpec = tween(600, easing = androidx.compose.animation.core.EaseOut),
    label = "balanceCount",
)
Text("$%,.2f".format(shown), style = ChimeText.Balance, color = ChimeColors.BalanceInk)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.Confirm)` for incoming-money / deposit-alert success, `HapticFeedbackType.LongPress` (medium analog) on SpotMe boost and transfer success, a soft `LocalView.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` on tab and chip taps. Auto-save / silent state changes have no motion.

## 6. Icons

Chime's iconography is simple and rounded; `androidx.compose.material:material-icons-extended` covers it. The logomark (a mint circle with a "C" stroke) is a custom vector, not an icon-font glyph.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Move (tab) | `arrow.left.arrow.right` | `Icons.Filled.SwapHoriz` |
| Card (tab) | `creditcard` / `creditcard.fill` | `Icons.Filled.CreditCard` |
| Grow (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Me (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Move money | `arrow.left.arrow.right` | `Icons.Filled.SwapHoriz` |
| Pay anyone | `paperplane.fill` | `Icons.Filled.Send` |
| SpotMe | `star.fill` / `shield.lefthalf.filled` | `Icons.Filled.Star` / `Icons.Filled.Shield` |
| Deposit / incoming | `arrow.down.circle.fill` | `Icons.Filled.ArrowCircleDown` |
| Alert check | `checkmark` | `Icons.Filled.Check` |
| Notifications | `bell` | `Icons.Filled.NotificationsNone` |
| Add to Spot | `plus` | `Icons.Filled.Add` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Card frozen | `snowflake` | `Icons.Filled.AcUnit` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Direct deposit | `dollarsign.arrow.circlepath` | `Icons.Filled.Payments` |
| Grow / savings | `leaf.fill` | `Icons.Filled.Eco` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; the gradient hero + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the soft mint canvas wants dark-content system bars (light-content in dark mode). The Move-money `ModalBottomSheet` and any editor respect `Modifier.imePadding()`.
- **Tabular figures**: keep `fontFeatureSettings = "tnum"` on the balance and every amount so columns align and the count-up animation doesn't reflow.
- **Font scaling**: `sp` honors the user's font scale — keep it on balance, screen titles, section/card headers, body, row titles, meta. Pin layout-sensitive text (10sp tab labels, chip text, the cents superscript) via `dp`-derived sizes or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: the balance exposes `contentDescription = "Available to spend, $2,847.19"`; transaction rows announce "{name}, {meta}, {signed amount}"; the instant alert uses `LiveRegionMode.Assertive` so it's announced when it appears; SpotMe announces "SpotMe is on, limit $200". Never convey money direction by color alone — keep the `+`/`-` sign and the word "received".
- **Touch targets**: Material guidance is 48.dp. The transaction row is full-width tappable; balance-card buttons are 40.dp tall (give a 48.dp hit via padding); primary buttons ≥ 52.dp; tab items are full-height.
- **Contrast**: `#062014` on the mint hero passes WCAG AA (the near-black mint ink is chosen for exactly this). `#0F1A14` on `#F6FBF8` passes AA. Validate `#0FAE63` positive text on white at small sizes — bump to `#0C8F50` if it fails.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, set the balance instantly (no roll), skip the SpotMe bump and the check-stroke draw; keep the alert as a plain fade.
- **Dark mode**: invert via the `Dark*` palette — `#0E1411`, NOT neutral black; `#0F1A14` text becomes `#EAF2EC`. The mint hero gradient and `#062014` ink stay constant; soft fills → `#123A2A`, SpotMe → `#2EE6A6`, positive → `#00D67E`, negative → `#FF6B6B`. Do **not** enable Material You `dynamicColorScheme()` — Chime's mint-everything identity must hold regardless of wallpaper.
