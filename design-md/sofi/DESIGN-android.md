# SoFi (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports SoFi's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the gradient member hero, account tiles, cross-sell cards, the activity row, navigation, and motion.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (SoFi's dark-native navy + electric blue, the gradient member hero, the consolidated tile grid, pill buttons, color-coded performance) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Brush.linearGradient` hero, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for any avatars. No color extraction — SoFi's palette is fixed brand colors, so Palette is not needed. SoFi is **dark-native**: navy is the canonical canvas in both themes; a light scheme is provided that lifts surfaces while keeping the navy + electric-blue identity.

## 1. Color Tokens

```kotlin
// ui/theme/SofiColors.kt
import androidx.compose.ui.graphics.Color

object SofiColors {
    // Brand (Blue)
    val Blue        = Color(0xFF00A0DF)
    val BlueBright  = Color(0xFF29C2FF)
    val BluePressed = Color(0xFF0086BC)
    val BlueSoft      = Color(0xFFE6F4FB) // light
    val BlueSoftDark  = Color(0xFF0E2A44) // dark-native
    val OnBlueInk   = Color(0xFF042235)

    // Canvas & Surfaces (dark-native — canonical)
    val Navy     = Color(0xFF0A0E27)
    val Surface1 = Color(0xFF121736)
    val Surface2 = Color(0xFF1B2147)
    val Divider  = Color(0xFF252C55)

    // Canvas & Surfaces (light)
    val LightCanvas   = Color(0xFFF4F7FB)
    val LightSurface1 = Color(0xFFFFFFFF)
    val LightSurface2 = Color(0xFFEEF2F8)
    val LightDivider  = Color(0xFFE2E7F0)

    // Text (on navy)
    val TextPrimary   = Color(0xFFE8EBF7)
    val TextSecondary = Color(0xFF9BA3C7)
    val TextTertiary  = Color(0xFF6B7299)
    val TextPrimaryLight   = Color(0xFF0C1330)
    val TextSecondaryLight = Color(0xFF5A6286)

    // Semantic
    val Positive      = Color(0xFF2FD08A) // navy
    val PositiveLight = Color(0xFF0FA968) // light
    val Negative      = Color(0xFFFF6B6B) // navy
    val NegativeLight = Color(0xFFE0484D) // light
    val Gold          = Color(0xFFF2C14E) // navy
    val GoldLight     = Color(0xFFC8920F) // light
    val PillGreen     = Color(0xFF6EF0B6) // change-pill text on hero
    val GradientMid   = Color(0xFF1B53C4) // hero gradient middle stop
}
```

Wire it into both schemes. SoFi is dark-native — navy `#0A0E27` is canonical; the light scheme lifts surfaces but the navy + electric-blue identity holds.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val SofiDark = darkColorScheme(
    primary        = SofiColors.Blue,
    onPrimary      = SofiColors.OnBlueInk,
    background      = SofiColors.Navy,
    onBackground    = SofiColors.TextPrimary,
    surface         = SofiColors.Surface1,
    onSurface       = SofiColors.TextPrimary,
    surfaceVariant  = SofiColors.Surface2,
    outline         = SofiColors.Divider,
    error           = SofiColors.Negative,
)

private val SofiLight = lightColorScheme(
    primary        = SofiColors.Blue,
    onPrimary      = SofiColors.OnBlueInk,
    background      = SofiColors.LightCanvas,
    onBackground    = SofiColors.TextPrimaryLight,
    surface         = SofiColors.LightSurface1,
    onSurface       = SofiColors.TextPrimaryLight,
    surfaceVariant  = SofiColors.LightSurface2,
    outline         = SofiColors.LightDivider,
    error           = SofiColors.NegativeLight,
)

@Composable
fun SofiTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    // Default to dark — SoFi is dark-native; light only when the system asks.
    colorScheme = if (dark) SofiDark else SofiLight,
    typography = SofiTypography,
    content = content,
)
```

## 2. Typography

SoFi's type is a confident geometric sans — **Manrope** is the faithful free stand-in (SIL OFL; drop the TTFs in `res/font/`). Numbers always 700-800 and tabular (`tnum`).

```kotlin
// ui/theme/SofiType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Manrope = FontFamily(
    Font(R.font.manrope_regular,   FontWeight.Normal),
    Font(R.font.manrope_medium,    FontWeight.Medium),
    Font(R.font.manrope_semibold,  FontWeight.SemiBold),
    Font(R.font.manrope_bold,      FontWeight.Bold),
    Font(R.font.manrope_extrabold, FontWeight.ExtraBold),
)

private const val TNUM = "tnum"  // fontFeatureSettings on amount Text

object SofiText {
    val NetWorth    = TextStyle(Manrope, fontWeight = FontWeight.ExtraBold, fontSize = 40.sp, lineHeight = 42.sp, letterSpacing = (-1.2).sp, fontFeatureSettings = TNUM)
    val ScreenTitle = TextStyle(Manrope, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val Section     = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val TileValue   = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.4).sp, fontFeatureSettings = TNUM)
    val CardTitle   = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 21.sp)
    val Body        = TextStyle(Manrope, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val RowTitle    = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp)
    val Amount      = TextStyle(Manrope, fontWeight = FontWeight.ExtraBold, fontSize = 15.sp, lineHeight = 18.sp, fontFeatureSettings = TNUM)
    val Meta        = TextStyle(Manrope, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Caption     = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 17.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Chip        = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Link        = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 17.sp)
}

val SofiTypography = Typography(
    headlineLarge  = SofiText.NetWorth,
    headlineMedium = SofiText.ScreenTitle,
    titleMedium    = SofiText.CardTitle,
    bodyMedium     = SofiText.Body,
    labelSmall     = SofiText.Tab,
)
```

## 3. Signature Components

### Member Hero (gradient)

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
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.unit.dp
import androidx.compose.foundation.layout.WindowInsets
import androidx.compose.foundation.layout.statusBars
import androidx.compose.foundation.layout.windowInsetsPadding

@Composable
fun SofiMemberHero(
    greeting: String, netWorth: String, change: String, isUp: Boolean,
) {
    Column(
        Modifier
            .fillMaxWidth()
            .background(
                Brush.linearGradient(
                    colorStops = arrayOf(
                        0.0f to SofiColors.Blue,
                        0.45f to SofiColors.GradientMid,
                        1.0f to SofiColors.Navy,
                    ),
                )
            )
            .windowInsetsPadding(WindowInsets.statusBars)
            .padding(start = 20.dp, end = 20.dp, top = 8.dp, bottom = 28.dp),
    ) {
        Row(
            Modifier.fillMaxWidth().padding(bottom = 18.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            Text(greeting, style = SofiText.CardTitle.copy(fontSize = 18.sp), color = Color.White)
            Row(horizontalArrangement = Arrangement.spacedBy(16.dp)) {
                Icon(Icons.Filled.NotificationsNone, null, tint = Color.White, modifier = Modifier.size(22.dp))
                Icon(Icons.Filled.AccountCircle, null, tint = Color.White, modifier = Modifier.size(22.dp))
            }
        }
        Text("Net worth", style = SofiText.Caption, color = Color.White.copy(alpha = 0.75f))
        Text(netWorth, style = SofiText.NetWorth, color = Color.White, modifier = Modifier.padding(top = 4.dp))
        Row(
            Modifier
                .padding(top = 8.dp)
                .clip(RoundedCornerShape(50))
                .background(SofiColors.Positive.copy(alpha = 0.18f))
                .padding(horizontal = 10.dp, vertical = 5.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(5.dp),
        ) {
            Icon(if (isUp) Icons.Filled.ArrowUpward else Icons.Filled.ArrowDownward,
                null, tint = SofiColors.PillGreen, modifier = Modifier.size(11.dp))
            Text(change, style = SofiText.Link, color = SofiColors.PillGreen)
        }
    }
}
```

### Account Tile

```kotlin
enum class SubKind { Gain, Loss, Rewards, Neutral }

@Composable
fun SofiAccountTile(
    icon: androidx.compose.ui.graphics.vector.ImageVector,
    label: String, value: String, subStat: String,
    subKind: SubKind = SubKind.Neutral, modifier: Modifier = Modifier,
) {
    val subColor = when (subKind) {
        SubKind.Gain -> SofiColors.Positive
        SubKind.Loss -> SofiColors.Negative
        SubKind.Rewards -> SofiColors.Gold
        SubKind.Neutral -> SofiColors.TextSecondary
    }
    Column(
        modifier
            .clip(RoundedCornerShape(18.dp))
            .background(SofiColors.Surface1)
            .border(1.dp, SofiColors.Divider, RoundedCornerShape(18.dp))
            .padding(16.dp),
    ) {
        Box(
            Modifier.size(36.dp).clip(RoundedCornerShape(11.dp)).background(SofiColors.BlueSoftDark),
            contentAlignment = Alignment.Center,
        ) { Icon(icon, null, tint = SofiColors.BlueBright, modifier = Modifier.size(18.dp)) }
        Spacer(Modifier.height(12.dp))
        Text(label, style = SofiText.Caption, color = SofiColors.TextSecondary)
        Text(value, style = SofiText.TileValue, color = SofiColors.TextPrimary, modifier = Modifier.padding(top = 3.dp))
        Text(subStat, style = SofiText.Caption, color = subColor, modifier = Modifier.padding(top = 4.dp))
    }
}

// 2-column grid: LazyVerticalGrid(GridCells.Fixed(2), horizontalArrangement = Arrangement.spacedBy(12.dp), verticalArrangement = Arrangement.spacedBy(12.dp)) { … }
```

### Cross-Sell Product Card & Activity Row

```kotlin
@Composable
fun SofiProductCard(
    icon: androidx.compose.ui.graphics.vector.ImageVector,
    title: String, valueProp: String, modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .width(240.dp)
            .clip(RoundedCornerShape(18.dp))
            .background(SofiColors.Surface2)
            .border(1.dp, SofiColors.Divider, RoundedCornerShape(18.dp))
            .padding(16.dp),
    ) {
        Box(Modifier.size(36.dp).clip(RoundedCornerShape(11.dp)).background(SofiColors.BlueSoftDark),
            contentAlignment = Alignment.Center) {
            Icon(icon, null, tint = SofiColors.BlueBright, modifier = Modifier.size(18.dp))
        }
        Spacer(Modifier.height(12.dp))
        Text(title, style = SofiText.CardTitle, color = SofiColors.TextPrimary)
        Text(valueProp, style = SofiText.Caption.copy(fontWeight = FontWeight.Normal),
            color = SofiColors.TextSecondary, modifier = Modifier.padding(top = 3.dp))
    }
}

@Composable
fun SofiActivityRow(
    icon: androidx.compose.ui.graphics.vector.ImageVector, tint: Color,
    name: String, meta: String, amount: String, positive: Boolean,
) {
    Row(
        Modifier.fillMaxWidth().padding(vertical = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(40.dp).clip(RoundedCornerShape(13.dp)).background(SofiColors.BlueSoftDark),
            contentAlignment = Alignment.Center) {
            Icon(icon, null, tint = tint, modifier = Modifier.size(18.dp))
        }
        Column(Modifier.weight(1f)) {
            Text(name, style = SofiText.RowTitle, color = SofiColors.TextPrimary)
            Text(meta, style = SofiText.Meta, color = SofiColors.TextSecondary)
        }
        Text(amount, style = SofiText.Amount,
            color = if (positive) SofiColors.Positive else SofiColors.TextPrimary)
    }
    HorizontalDivider(color = SofiColors.Divider)
}
```

### Primary Button & Performance Chip

```kotlin
@Composable
fun SofiPrimaryButton(text: String, modifier: Modifier = Modifier, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        modifier
            .heightIn(min = 52.dp)
            .clip(RoundedCornerShape(50))                       // FULL PILL
            .background(if (pressed) SofiColors.BluePressed else SofiColors.Blue)
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) { Text(text, style = SofiText.Button, color = SofiColors.OnBlueInk, modifier = Modifier.padding(horizontal = 28.dp)) }
}

sealed interface SofiPerf {
    data object Active : SofiPerf
    data class Up(val v: String) : SofiPerf
    data class Down(val v: String) : SofiPerf
    data class Rewards(val v: String) : SofiPerf
}

@Composable
fun SofiPerfChip(perf: SofiPerf) {
    val (bg, fg, text) = when (perf) {
        SofiPerf.Active     -> Triple(SofiColors.BlueSoftDark, SofiColors.BlueBright, "Active")
        is SofiPerf.Up      -> Triple(SofiColors.Positive.copy(alpha = 0.16f), SofiColors.Positive, "▲ ${perf.v}")
        is SofiPerf.Down    -> Triple(SofiColors.Negative.copy(alpha = 0.16f), SofiColors.Negative, "▼ ${perf.v}")
        is SofiPerf.Rewards -> Triple(SofiColors.Gold.copy(alpha = 0.16f), SofiColors.Gold, perf.v)
    }
    Box(Modifier.clip(RoundedCornerShape(50)).background(bg).padding(horizontal = 14.dp, vertical = 7.dp)) {
        Text(text, style = SofiText.Chip, color = fg)
    }
}
```

## 4. Navigation

SoFi has a 5-tab bottom strip — Home, Invest, Banking, Borrow, Me. On Android, model it as a `NavigationBar`; active is bright blue with the filled icon (no Material tint pill).

```kotlin
@Composable
fun SofiBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = SofiColors.Navy, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"    to Icons.Filled.Home,
            "Invest"  to Icons.Filled.TrendingUp,
            "Banking" to Icons.Filled.CreditCard,
            "Borrow"  to Icons.Filled.AccountBalance,
            "Me"      to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = SofiText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = SofiColors.BlueBright,
                    selectedTextColor = SofiColors.BlueBright,
                    unselectedIconColor = SofiColors.TextTertiary,
                    unselectedTextColor = SofiColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — SoFi has none
                ),
            )
        }
    }
}
```

The Transfer / Invest flow is a Material 3 `ModalBottomSheet` (`Surface1` / `LightSurface1`, 24dp top corners) with a huge centered amount entry, a funding-source row, and a pinned blue **pill** CTA. The member home's top "header" is the gradient hero itself — there is no separate `TopAppBar`; cross-sell cards sit in a `LazyRow` with `snapFlingBehavior`.

## 5. Motion

SoFi motion is smooth — ease-outs and snaps, measured haptics. The signature is the net-worth count-up and the gradient parallax.

| Moment | Compose recipe |
|--------|----------------|
| Net-worth count-up | `animateFloatAsState` previous → new, `tween(600, easing = EaseOut)`, format as currency |
| Gradient parallax | hero `Modifier.graphicsLayer { translationY = scrollState.value * -0.3f }` |
| Tile press | `Modifier.scale(animateFloatAsState(if (pressed) 0.97f else 1f, tween(150)))` |
| Performance tick | `Crossfade`/`animateColorAsState` on the %/chip when data refreshes (`tween(200)`) |
| Transfer success | full-screen blue check `drawWithCache` stroke `animateFloatAsState` 0 → 1 `tween(500)` + medium haptic |
| Cross-sell carousel | `LazyRow` + `rememberSnapFlingBehavior`, peeking next card |
| Tab switch | instant; active icon → bright blue, no slide |
| Pull to refresh | `PullToRefreshContainer` blue indicator; re-count net worth + tiles if changed |

```kotlin
// Net-worth count-up — the canonical SoFi motion
val shown by animateFloatAsState(
    targetValue = netWorth,
    animationSpec = tween(600, easing = androidx.compose.animation.core.EaseOut),
    label = "netWorthCount",
)
Text("$%,.2f".format(shown), style = SofiText.NetWorth, color = Color.White)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.Confirm)` on transfer/invest success, a soft `LocalView.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` on tile and tab taps and pull-refresh release. Silent data refresh has no haptic; only success/failure does.

## 6. Icons

SoFi's iconography is clean and line-based; `androidx.compose.material:material-icons-extended` covers it. The wordmark ("SoFi") is brand text, not an icon-font glyph.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Invest (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Banking (tab) | `creditcard` / `creditcard.fill` | `Icons.Filled.CreditCard` |
| Borrow (tab) | `building.columns` / `.fill` | `Icons.Filled.AccountBalance` |
| Me (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Checking & Savings tile | `creditcard` | `Icons.Filled.CreditCard` |
| Invest tile | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Loans tile | `clock.arrow.circlepath` | `Icons.Filled.History` |
| Credit Card tile | `star.fill` | `Icons.Filled.Star` |
| Rewards | `star.circle.fill` | `Icons.Filled.CardGiftcard` |
| Net-worth up | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Net-worth down | `arrow.down` | `Icons.Filled.ArrowDownward` |
| Notifications | `bell` | `Icons.Filled.NotificationsNone` |
| Transfer | `arrow.left.arrow.right` | `Icons.Filled.SwapHoriz` |
| Auto-invest | `arrow.triangle.2.circlepath` | `Icons.Filled.Repeat` |
| Direct deposit | `dollarsign.arrow.circlepath` | `Icons.Filled.Payments` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Settings | `gearshape` | `Icons.Filled.Settings` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; the gradient hero + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the gradient/navy ground is dark in both themes, so use **light-content** system bars throughout (even in "light mode" the hero is the gradient). The hero uses `windowInsetsPadding(WindowInsets.statusBars)`; the Transfer `ModalBottomSheet` respects `Modifier.imePadding()`.
- **Tabular figures**: keep `fontFeatureSettings = "tnum"` on net worth and every amount so columns align and the count-up doesn't reflow.
- **Font scaling**: `sp` honors the user's font scale — keep it on net worth, screen titles, section headers, tile values, body, row titles, meta. Pin layout-sensitive text (10sp tab labels, chip text, the change-pill text) via `dp`-derived sizes or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: the hero exposes `contentDescription = "Net worth, $84,210.55, up $1,840.22 this month"`; tiles announce "{label}, {value}, {sub-stat}"; activity rows announce "{name}, {meta}, {signed amount}". Never convey performance by color alone — keep the ▲/▼ glyph and the `+`/`-` sign.
- **Touch targets**: Material guidance is 48.dp. Tiles are full-tile tappable (≥ 96.dp); cross-sell cards full-card; primary pill buttons ≥ 52.dp; tab items full-height.
- **Contrast**: `#042235` on `#00A0DF` passes WCAG AA (the near-black blue ink is chosen for this); white on the gradient passes throughout. `#2FD08A` on navy passes for graphical/large text — pair with the arrow glyph for small %s.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, set net worth instantly (no roll), skip the gradient parallax and the check-stroke draw; keep tile press as a plain state change.
- **Dark-native / light**: navy `#0A0E27` is the canonical canvas. The light scheme lifts surfaces (`Surface1 → white`, `BlueSoftDark → BlueSoft`, `Positive → PositiveLight`, etc.) but the hero gradient and `#042235` ink stay constant and the navy + electric-blue identity must remain. Do **not** enable Material You `dynamicColorScheme()` — SoFi's electric-blue-on-navy brand must hold regardless of wallpaper.
