# Monzo (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Monzo's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the Hot Coral card hero + transaction feed + Pots, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the Hot Coral card hero, Monzo Navy, the emoji transaction feed, round Pot coins) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyColumn` for the feed, `LazyRow` for Pots, `graphicsLayer` for the card flip, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for merchant logos. Do **not** enable Material You `dynamicColorScheme()` — Hot Coral is the brand and must hold regardless of wallpaper. Monzo is dark-and-navy friendly; a full dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/MonzoColors.kt
import androidx.compose.ui.graphics.Color

object MonzoColors {
    // Brand
    val Coral        = Color(0xFFFF3464)
    val CoralPressed = Color(0xFFE02855)
    val Navy         = Color(0xFF14233C)
    val NavyDeep     = Color(0xFF0E1620)

    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFF6F7F9)
    val Surface        = Color(0xFFFFFFFF)
    val SurfacePressed = Color(0xFFEEF0F3)
    val Divider        = Color(0xFFE4E7EB)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF0E1620) // deep navy — NOT pure black
    val DarkSurface1 = Color(0xFF14233C) // the brand navy
    val DarkSurface2 = Color(0xFF1B2F4D)
    val DarkDivider  = Color(0xFF243A57)

    // Text
    val TextPrimary       = Color(0xFF14233C)
    val TextSecondary     = Color(0xFF5C6B82)
    val TextTertiary      = Color(0xFF94A1B5)
    val DarkTextPrimary   = Color(0xFFF4F6F9)
    val DarkTextSecondary = Color(0xFF9FB0C6)
    val DarkTextTertiary  = Color(0xFF61748E)

    // Pot / category accents (light / dark)
    val MintLight = Color(0xFF2FB59A); val Mint = Color(0xFF5CE0C4)
    val SkyLight  = Color(0xFF3E7FC4); val Sky  = Color(0xFF5AA9F0)
    val GoldLight = Color(0xFFE0A23A); val Gold = Color(0xFFFFC75F)
    val VioletLight = Color(0xFF7A4FD0); val Violet = Color(0xFFA98AE6)
    val TealLight = Color(0xFF1F9E8F); val Teal = Color(0xFF46C7B7)

    // Semantic
    val IncomeLight = Color(0xFF1FA971); val Income = Color(0xFF2FCB8F)
    val ErrorLight  = Color(0xFFE0354A); val Error  = Color(0xFFFF5A6E)
    val WarningLight = Color(0xFFD98A1F); val Warning = Color(0xFFFFB347)
}
```

Wire it into both schemes. Monzo is happiest dark-and-navy; the dark scheme uses `#0E1620` deep navy, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val MonzoLight = lightColorScheme(
    primary        = MonzoColors.Coral,
    onPrimary      = Color.White,
    background     = MonzoColors.Canvas,
    onBackground   = MonzoColors.TextPrimary,
    surface        = MonzoColors.Surface,
    onSurface      = MonzoColors.TextPrimary,
    surfaceVariant = MonzoColors.SurfacePressed,
    outline        = MonzoColors.Divider,
    error          = MonzoColors.ErrorLight,
)

private val MonzoDark = darkColorScheme(
    primary        = MonzoColors.Coral,
    onPrimary      = Color.White,
    background     = MonzoColors.DarkCanvas,
    onBackground   = MonzoColors.DarkTextPrimary,
    surface        = MonzoColors.DarkSurface1,
    onSurface      = MonzoColors.DarkTextPrimary,
    surfaceVariant = MonzoColors.DarkSurface2,
    outline        = MonzoColors.DarkDivider,
    error          = MonzoColors.Error,
)

@Composable
fun MonzoTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) MonzoDark else MonzoLight,
    typography = MonzoTypography,
    content = content,
)
```

## 2. Typography

Monzo's product face is **Inter** (SIL OFL) — drop the TTFs in `res/font/`. Money values use tabular figures via a font feature setting. Body 400; titles 700; balance/screen-title 800.

```kotlin
// ui/theme/MonzoType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.font.FontFeature
import androidx.compose.ui.text.style.TextGeometricTransform
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,    FontWeight.Normal),
    Font(R.font.inter_medium,     FontWeight.Medium),
    Font(R.font.inter_semibold,   FontWeight.SemiBold),
    Font(R.font.inter_bold,       FontWeight.Bold),
    Font(R.font.inter_extrabold,  FontWeight.ExtraBold),
)

// Tabular-figures feature for money
val Tnum = androidx.compose.ui.text.font.FontFeatureSettings("tnum")

object MonzoText {
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val Balance     = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 30.sp, lineHeight = 33.sp, letterSpacing = (-0.5).sp, fontFeatureSettings = "tnum")
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Subsection  = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 23.sp)
    val Merchant    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val Amount      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp, fontFeatureSettings = "tnum")
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 13.sp, lineHeight = 18.sp)
    val Label       = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val CardNumber  = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val MonzoTypography = Typography(
    headlineLarge = MonzoText.ScreenTitle,
    headlineMedium = MonzoText.Section,
    titleMedium   = MonzoText.Subsection,
    bodyMedium    = MonzoText.Body,
    labelSmall    = MonzoText.Tab,
)
```

## 3. Signature Components

### Hot Coral Card Hero

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Image
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.graphicsLayer
import androidx.compose.ui.unit.dp

@Composable
fun MonzoCardHero(
    balance: String,
    maskedNumber: String,
    modifier: Modifier = Modifier,
) {
    var flipped by remember { mutableStateOf(false) }
    val rotation by animateFloatAsState(if (flipped) 180f else 0f, tween(350), label = "cardFlip")

    Box(
        modifier
            .padding(horizontal = 20.dp)
            .fillMaxWidth()
            .height(132.dp)
            .graphicsLayer { rotationY = rotation; cameraDistance = 12f * density }
            .shadow(30.dp, RoundedCornerShape(18.dp), spotColor = MonzoColors.Coral.copy(alpha = 0.4f))
            .clip(RoundedCornerShape(18.dp))
            .background(
                Brush.linearGradient(listOf(Color(0xFFFF3464), Color(0xFFFF5A7F), Color(0xFFE02855)))
            )
            .clickable { flipped = !flipped }
            .padding(20.dp),
    ) {
        Text("MONZO", style = MonzoText.Label.copy(fontWeight = FontWeight.Bold, letterSpacing = 1.sp),
            color = Color.White.copy(alpha = 0.9f), modifier = Modifier.align(Alignment.TopEnd))
        Column(Modifier.align(Alignment.CenterStart)) {
            Text(balance, style = MonzoText.Balance, color = Color.White)
            Text("Available to spend", style = MonzoText.Label, color = Color.White.copy(alpha = 0.85f))
        }
        Text(maskedNumber, style = MonzoText.CardNumber, color = Color.White.copy(alpha = 0.9f),
            modifier = Modifier.align(Alignment.BottomStart))
    }
}
```

### Transaction Row

```kotlin
@Composable
fun TransactionRow(
    emoji: String,
    tint: Color,
    merchant: String,
    category: String,
    time: String,
    amount: String,
    isIncome: Boolean = false,
    isPending: Boolean = false,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .alpha(if (isPending) 0.6f else 1f)
            .padding(horizontal = 20.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Box(
            Modifier.size(42.dp).clip(CircleShape).background(tint.copy(alpha = 0.18f)),
            contentAlignment = Alignment.Center,
        ) { Text(emoji, fontSize = 20.sp) }

        Column(Modifier.weight(1f)) {
            Text(merchant, style = MonzoText.Merchant, color = MonzoColors.DarkTextPrimary)
            Text("$category · $time", style = MonzoText.Meta, color = MonzoColors.DarkTextSecondary)
        }
        Text(
            amount, style = MonzoText.Amount,
            color = if (isIncome) MonzoColors.Income else MonzoColors.DarkTextPrimary,
        )
    }
}
```

### Pot Coin

```kotlin
@Composable
fun PotCoin(emoji: String, name: String, amount: String, gradient: List<Color>) {
    Column(horizontalAlignment = Alignment.CenterHorizontally) {
        Box(
            Modifier
                .size(92.dp)
                .shadow(16.dp, RoundedCornerShape(16.dp), spotColor = Color.Black.copy(alpha = 0.2f))
                .clip(RoundedCornerShape(16.dp))
                .background(Brush.linearGradient(gradient)),
            contentAlignment = Alignment.Center,
        ) { Text(emoji, fontSize = 30.sp) }
        Spacer(Modifier.height(8.dp))
        Text(name, style = MonzoText.Label, color = MonzoColors.DarkTextPrimary)
        Text(amount, style = MonzoText.Label.copy(fontSize = 11.sp), color = MonzoColors.DarkTextSecondary)
    }
}
```

### Pot Card (detail / list)

```kotlin
import androidx.compose.foundation.shape.CircleShape

@Composable
fun PotCard(emoji: String, name: String, saved: String, goal: String, progress: Float) {
    Row(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(16.dp))
            .background(MonzoColors.DarkSurface2)
            .padding(16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Box(
            Modifier.size(48.dp).clip(RoundedCornerShape(12.dp))
                .background(Brush.linearGradient(listOf(MonzoColors.Mint, MonzoColors.MintLight))),
            contentAlignment = Alignment.Center,
        ) { Text(emoji, fontSize = 22.sp) }

        Column(Modifier.weight(1f)) {
            Text(name, style = MonzoText.Merchant, color = MonzoColors.DarkTextPrimary)
            Text("$saved of $goal goal", style = MonzoText.Label, color = MonzoColors.DarkTextSecondary)
            Spacer(Modifier.height(8.dp))
            Box(Modifier.fillMaxWidth().height(6.dp).clip(RoundedCornerShape(3.dp)).background(MonzoColors.DarkDivider)) {
                Box(Modifier.fillMaxWidth(progress).height(6.dp).clip(RoundedCornerShape(3.dp)).background(MonzoColors.Mint))
            }
        }
    }
}
```

### Buttons

```kotlin
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.OutlinedButton

@Composable
fun MonzoPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Button(
        onClick = onClick,
        modifier = modifier.fillMaxWidth().heightIn(min = 48.dp),
        shape = RoundedCornerShape(14.dp),
        colors = ButtonDefaults.buttonColors(containerColor = MonzoColors.Coral, contentColor = Color.White),
    ) { Text(title, style = MonzoText.Button) }
}

@Composable
fun MonzoPillButton(title: String, onClick: () -> Unit) {
    OutlinedButton(
        onClick = onClick,
        shape = RoundedCornerShape(500.dp),
        border = androidx.compose.foundation.BorderStroke(1.5.dp, MonzoColors.Coral),
    ) { Text(title, style = MonzoText.Label.copy(fontWeight = FontWeight.SemiBold, fontSize = 14.sp), color = MonzoColors.Coral) }
}
```

## 4. Navigation

Monzo has a 5-tab bottom strip with a Hot Coral active state and **no tint pill** — active is just the coral icon + label.

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun MonzoBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = MonzoColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Payments" to Icons.Filled.CreditCard,
            "Pots"     to Icons.Filled.GridView,
            "Trends"   to Icons.Filled.TrendingUp,
            "Account"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = MonzoText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = MonzoColors.Coral,
                    selectedTextColor   = MonzoColors.Coral,
                    unselectedIconColor = MonzoColors.DarkTextTertiary,
                    unselectedTextColor = MonzoColors.DarkTextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill — Monzo has none
                ),
            )
        }
    }
}
```

The Home screen is a `LazyColumn`: card hero (`item`), Pots `LazyRow` (`item`), then day-grouped transaction rows with sticky day-header rows (`stickyHeader`). The large-title header collapses on scroll via a `TopAppBar` + `nestedScroll`.

## 5. Motion

Monzo motion is confident and physical — the card flips, coins spring into Pots, numbers tick.

| Moment | Compose recipe |
|--------|----------------|
| Card flip to controls | `animateFloatAsState` 0 → 180° `tween(350)` on `graphicsLayer { rotationY }` with `cameraDistance` |
| Pot money move | drag a coin via `Modifier.draggable`, `animateOffsetAsState` spring `(dampingRatio = 0.7f)` into the Pot; balance via `animateIntAsState` ticker |
| Feed insert | new row `AnimatedVisibility` `slideInVertically { -it } + fadeIn(tween(250))`; highlight wash `tween(800)` fade-out |
| Pull-to-refresh | `PullToRefreshBox` with `MonzoColors.Coral` indicator |
| Tab change | instant color crossfade (NavigationBar handles); ~120ms feel |
| Page navigation | Nav3/`NavHost` slide push `tween(300)` |
| Number ticker | `animateIntAsState(targetBalancePence, tween(250))` then format to currency |

```kotlin
// Balance ticker — the canonical Monzo "money moved" motion
val shown by animateIntAsState(balancePence, tween(250), label = "balanceTick")
Text(formatCurrency(shown), style = MonzoText.Balance, color = Color.White)
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` as the soft impact on Pot money move and card flip. For payment success use `view.performHapticFeedback(HapticFeedbackConstants.CONFIRM)`. Tab change gets a light `CLOCK_TICK`.

## 6. Icons

Monzo's category glyphs are emoji (user/merchant data), not icon-font icons. UI chrome maps to `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Payments (tab) | `creditcard.fill` | `Icons.Filled.CreditCard` |
| Pots (tab) | `circle.grid.2x2.fill` | `Icons.Filled.GridView` |
| Trends (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Account (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Add money | `plus` | `Icons.Filled.Add` |
| Freeze card | `snowflake` | `Icons.Filled.AcUnit` |
| Split bill | `person.2` | `Icons.Filled.Group` |
| Map pin | `mappin.circle.fill` | `Icons.Filled.LocationOn` |
| Receipt | `doc.text` | `Icons.Filled.Receipt` |
| Notes | `square.and.pencil` | `Icons.Filled.Edit` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Income | `arrow.down.left` | `Icons.Filled.SouthWest` |
| Pending | `clock` | `Icons.Filled.Schedule` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `graphicsLayer` rotation + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; deep-navy canvas wants light-content system bars. The card hero and feed respect the camera cutout; sheets respect the IME with `Modifier.imePadding()`.
- **Tabular figures**: set `fontFeatureSettings = "tnum"` on balance and amount styles so money columns align; verify on your bundled Inter.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen title, balance, section, body, merchant, meta. Pin layout-sensitive text (10sp tab labels, card number, Pot-coin captions) by deriving from `dp` or a fixed-`fontScale` `CompositionLocalProvider`.
- **TalkBack**: label transaction rows "{merchant}, {category}, {amount}, {time}{, pending}"; the card hero is a button — "Current account, balance {amount}, double-tap to show card details"; expose Pot progress as a `progressBarRangeInfo`.
- **Touch targets**: Material guidance is 48.dp. Transaction rows are full-row tappable (≥56.dp effective); give 22.dp tab icons a 48.dp hit area; primary buttons ≥ 48.dp.
- **Contrast**: `#14233C` on `#F6F7F9` and `#F4F6F9` on `#0E1620` pass WCAG AA for body. The category-tint circles use ~18% alpha fills behind a solid emoji — fine; validate any solid-colored chip text (especially gold) with a contrast checker.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the card flip (`Crossfade` between faces), the Pot coin spring (snap to target), and the balance ticker (set value directly); keep the income/spend color cue.
- **Dark mode**: invert via the `Dark*` palette — `#0E1620` deep navy, NOT true black; `#14233C` is the surface; `#F4F6F9` text. Shadows are weak on dark, so add a 1dp `DarkDivider` border to sheets as the elevation cue. Do **not** enable `dynamicColorScheme()` — Hot Coral is the brand and must hold regardless of wallpaper.
