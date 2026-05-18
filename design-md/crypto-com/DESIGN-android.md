# Crypto.com (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Crypto.com's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (including the metallic Visa card), navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (navy-tinted dark canvas, two-blue system, the metallic tiered Visa card as hero, monospace money, the floating center Trade action) while making everything idiomatic Android — `NavigationBar` + a `FloatingActionButton`-style center action, `Brush.linearGradient` for card metals, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+`. No Material You dynamic color — Crypto.com's accent/navy and card metals must hold regardless of wallpaper. Dark-first; a light scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/CryptoColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object CryptoColors {
    // Canvas & Surfaces (Dark — default)
    val Canvas    = Color(0xFF03060F)
    val Surface1  = Color(0xFF0B1426)
    val Surface2  = Color(0xFF103F68) // Navy 2
    val Surface3  = Color(0xFF15263F)
    val Divider   = Color(0xFF1C3354)

    // Canvas & Surfaces (Light — secondary)
    val CanvasLight   = Color(0xFFFFFFFF)
    val Surface1Light = Color(0xFFF4F7FB)
    val DividerLight  = Color(0xFFE3E9F2)

    // Brand (decorative)
    val Navy  = Color(0xFF002D74)
    val Navy2 = Color(0xFF103F68)

    // Accent (interactive)
    val Accent        = Color(0xFF1199FA)
    val AccentPressed = Color(0xFF0B7AD1)
    val AccentTint    = Color(0x1F1199FA) // 12%

    // Market semantics (never invert)
    val Up   = Color(0xFF00C08B)
    val Down = Color(0xFFF6485D)

    // Card metals
    val Gold = Color(0xFFC8A24A)

    // Text
    val TextPrimary    = Color(0xFFF4F7FB)
    val TextSecondary  = Color(0xFF8DA0BD)
    val TextTertiary   = Color(0xFF5B6E8C)
    val TextPrimaryLt  = Color(0xFF0A1F44)

    // Semantic
    val Warning = Color(0xFFF5A623)
}

object CryptoBrushes {
    val BrandNavy = Brush.linearGradient(listOf(CryptoColors.Navy, CryptoColors.Navy2))
    val CardMidnight = Brush.linearGradient(listOf(Color(0xFF1A1A1E), Color(0xFF2A2A30)))
    val CardRuby     = Brush.linearGradient(listOf(Color(0xFF5A1020), Color(0xFF8A1830)))
    val CardJade     = Brush.linearGradient(listOf(Color(0xFF0E3A30), Color(0xFF14564A)))
    val CardObsidian = Brush.linearGradient(listOf(Color(0xFF0A0A0C), Color(0xFF1A1A1E), Color(0xFF26262C)))
    val Gloss        = Brush.linearGradient(listOf(Color(0x14FFFFFF), Color(0x00000000)))
}
```

Wire into both schemes. Dark-first with a navy cast; light only restyles canvas + text.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val CryptoDark = darkColorScheme(
    primary        = CryptoColors.Accent,
    onPrimary      = Color.White,
    background      = CryptoColors.Canvas,
    onBackground   = CryptoColors.TextPrimary,
    surface        = CryptoColors.Surface1,
    onSurface      = CryptoColors.TextPrimary,
    surfaceVariant = CryptoColors.Surface3,
    outline        = CryptoColors.Divider,
    error          = CryptoColors.Down,
)

private val CryptoLight = lightColorScheme(
    primary        = CryptoColors.Accent,
    onPrimary      = Color.White,
    background     = CryptoColors.CanvasLight,
    onBackground   = CryptoColors.TextPrimaryLt,
    surface        = CryptoColors.Surface1Light,
    onSurface      = CryptoColors.TextPrimaryLt,
    surfaceVariant = CryptoColors.Surface1Light,
    outline        = CryptoColors.DividerLight,
    error          = CryptoColors.Down,
)

@Composable
fun CryptoTheme(
    dark: Boolean = isSystemInDarkTheme(), // default dark in practice
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) CryptoDark else CryptoLight,
    typography  = CryptoTypography,
    content     = content,
)
```

## 2. Typography

UI face is a custom grotesque — use **Manrope** as the open stand-in (`res/font/`); money/PAN/APR uses **Roboto Mono** with **tabular figures** (`FontFeatureSetting("tnum")`).

```kotlin
// ui/theme/CryptoType.kt
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
val RobotoMono = FontFamily(
    Font(R.font.roboto_mono_medium,   FontWeight.Medium),
    Font(R.font.roboto_mono_semibold, FontWeight.SemiBold),
)

private const val TNUM = "tnum"

object CryptoText {
    // UI sans
    val ScreenTitle = TextStyle(Manrope, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.6).sp)
    val Section     = TextStyle(Manrope, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val RowTitle    = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val Body        = TextStyle(Manrope, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val ListLabel   = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp)
    val CardTier    = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 1.5.sp)
    val Meta        = TextStyle(Manrope, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Chip        = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 13.sp)
    val Caption     = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp)

    // Numeric mono — always tabular
    val Balance = TextStyle(RobotoMono, fontWeight = FontWeight.SemiBold, fontSize = 30.sp, lineHeight = 34.sp, letterSpacing = (-0.5).sp, fontFeatureSettings = TNUM)
    val Price   = TextStyle(RobotoMono, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp, fontFeatureSettings = TNUM)
    val PAN     = TextStyle(RobotoMono, fontWeight = FontWeight.Medium,   fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = 2.sp, fontFeatureSettings = TNUM)
    val PctSm   = TextStyle(RobotoMono, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 16.sp, fontFeatureSettings = TNUM)
}

val CryptoTypography = Typography(
    headlineLarge  = CryptoText.ScreenTitle,
    headlineMedium = CryptoText.Section,
    titleMedium    = CryptoText.RowTitle,
    bodyMedium     = CryptoText.Body,
    labelSmall     = CryptoText.Tab,
)
```

## 3. Signature Components

### Metallic Visa Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.unit.dp

enum class CardTier(val label: String, val brush: () -> Brush) {
    Midnight("Midnight Blue", { CryptoBrushes.CardMidnight }),
    Ruby("Ruby Steel",        { CryptoBrushes.CardRuby }),
    Jade("Jade Green",        { CryptoBrushes.CardJade }),
    Obsidian("Obsidian",      { CryptoBrushes.CardObsidian }),
}

@Composable
fun VisaCard(tier: CardTier, pan: String, holder: String, modifier: Modifier = Modifier) {
    Box(
        modifier
            .fillMaxWidth()
            .aspectRatio(1.586f)
            .shadow(30.dp, RoundedCornerShape(18.dp), spotColor = Color.Black.copy(alpha = 0.8f))
            .clip(RoundedCornerShape(18.dp))
            .background(tier.brush())
            .background(CryptoBrushes.Gloss) // metallic gloss
            .padding(20.dp),
    ) {
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
            Text(tier.label.uppercase(), style = CryptoText.CardTier, color = CryptoColors.Gold)
            Text("◆", color = Color.White)
        }
        Box(
            Modifier
                .padding(top = 44.dp)
                .size(36.dp, 26.dp)
                .clip(RoundedCornerShape(5.dp))
                .background(Brush.linearGradient(listOf(Color(0xFFD9C079), Color(0xFFA88D45)))),
        )
        Column(Modifier.align(Alignment.BottomStart)) {
            Text(pan, style = CryptoText.PAN, color = Color.White.copy(alpha = 0.92f))
            Row(
                Modifier.fillMaxWidth().padding(top = 6.dp),
                horizontalArrangement = Arrangement.SpaceBetween,
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Text(holder.uppercase(), style = CryptoText.Caption.copy(letterSpacing = 1.sp),
                    color = Color.White.copy(alpha = 0.75f))
                Text("VISA", style = CryptoText.Section.copy(fontSize = 18.sp, fontStyle = FontStyle.Italic),
                    color = Color.White)
            }
        }
    }
}
```

### Total Balance Hero

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.outlined.Visibility
import androidx.compose.material.icons.outlined.VisibilityOff
import androidx.compose.material3.Icon
import androidx.compose.runtime.*

@Composable
fun BalanceHero(value: String, pnl: String, gain: Boolean) {
    var hidden by remember { mutableStateOf(false) }
    Column(Modifier.padding(horizontal = 20.dp), verticalArrangement = Arrangement.spacedBy(4.dp)) {
        Text("Total Balance (USD)", style = CryptoText.Caption, color = CryptoColors.TextSecondary)
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(10.dp)) {
            Text(if (hidden) "••••••" else value, style = CryptoText.Balance, color = CryptoColors.TextPrimary)
            Icon(
                if (hidden) Icons.Outlined.VisibilityOff else Icons.Outlined.Visibility,
                contentDescription = "Toggle balance",
                tint = CryptoColors.TextSecondary,
                modifier = Modifier.size(14.dp).clickable { hidden = !hidden },
            )
        }
        Text("$pnl Today", style = CryptoText.PctSm, color = if (gain) CryptoColors.Up else CryptoColors.Down)
    }
}
```

### Watchlist Row

```kotlin
import androidx.compose.foundation.shape.CircleShape

@Composable
fun WatchRow(symbol: String, name: String, price: String, pct: Double, iconColor: Color = Color(0xFFF7931A)) {
    val up = pct >= 0
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 20.dp, vertical = 11.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(34.dp).clip(CircleShape).background(iconColor), contentAlignment = Alignment.Center) {
            Text(symbol.take(1), style = CryptoText.ListLabel, color = Color.White)
        }
        Column(Modifier.weight(1f)) {
            Text(symbol, style = CryptoText.ListLabel, color = CryptoColors.TextPrimary)
            Text(name, style = CryptoText.Caption.copy(fontWeight = FontWeight.Normal), color = CryptoColors.TextSecondary)
        }
        Column(horizontalAlignment = Alignment.End) {
            Text(price, style = CryptoText.Price, color = CryptoColors.TextPrimary)
            Text("${if (up) "+" else ""}${"%.2f".format(pct)}%",
                style = CryptoText.PctSm, color = if (up) CryptoColors.Up else CryptoColors.Down)
        }
    }
}
```

### Primary Pill Button

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState

@Composable
fun CryptoPrimaryButton(title: String, onClick: () -> Unit) {
    val src = remember { MutableInteractionSource() }
    val pressed by src.collectIsPressedAsState()
    Box(
        Modifier
            .fillMaxWidth().height(52.dp)
            .clip(RoundedCornerShape(500.dp))
            .background(if (pressed) CryptoColors.AccentPressed else CryptoColors.Accent)
            .clickable(interactionSource = src, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = CryptoText.Button, color = Color.White)
    }
}
```

### Quick-Action Circle Row

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun QuickActions() {
    val items = listOf(
        Icons.Filled.Add to "Deposit",
        Icons.Filled.ArrowUpward to "Send",
        Icons.Filled.SwapHoriz to "Trade",
        Icons.Filled.PieChart to "Earn",
    )
    Row(Modifier.fillMaxWidth().padding(horizontal = 20.dp)) {
        items.forEach { (icon, label) ->
            Column(Modifier.weight(1f), horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.spacedBy(6.dp)) {
                Box(Modifier.size(46.dp).clip(CircleShape).background(CryptoColors.Surface1),
                    contentAlignment = Alignment.Center) {
                    Icon(icon, label, tint = CryptoColors.Accent, modifier = Modifier.size(20.dp))
                }
                Text(label, style = CryptoText.Caption, color = CryptoColors.TextSecondary)
            }
        }
    }
}
```

## 4. Navigation (Bottom Bar + floating Trade)

5-tab `NavigationBar` with a center accent FAB-style Trade action and **no Material tint pill**.

```kotlin
import androidx.compose.material3.*
import androidx.compose.foundation.layout.offset

@Composable
fun CryptoBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    Box(contentAlignment = Alignment.TopCenter) {
        NavigationBar(containerColor = CryptoColors.Canvas, tonalElevation = 0.dp) {
            val items = listOf(
                0 to (Icons.Filled.Home to "Home"),
                1 to (Icons.Filled.BarChart to "Prices"),
                -1 to (Icons.Filled.SwapHoriz to ""),     // placeholder slot for center
                3 to (Icons.Filled.Percent to "Earn"),
                4 to (Icons.Filled.CreditCard to "Card"),
            )
            items.forEach { (i, pair) ->
                val (icon, label) = pair
                if (i == -1) {
                    NavigationBarItem(selected = false, onClick = {}, enabled = false,
                        icon = { Spacer(Modifier.size(24.dp)) }, label = {})
                } else {
                    NavigationBarItem(
                        selected = selected == i,
                        onClick = { onSelect(i) },
                        icon = { Icon(icon, label, Modifier.size(20.dp)) },
                        label = { Text(label, style = CryptoText.Tab) },
                        colors = NavigationBarItemDefaults.colors(
                            selectedIconColor   = CryptoColors.Accent,
                            selectedTextColor   = CryptoColors.Accent,
                            unselectedIconColor = CryptoColors.TextTertiary,
                            unselectedTextColor = CryptoColors.TextTertiary,
                            indicatorColor      = Color.Transparent, // no Material pill
                        ),
                    )
                }
            }
        }
        // Floating center Trade action with accent-tinted glow
        Box(
            Modifier
                .offset(y = (-22).dp)
                .size(44.dp)
                .shadow(16.dp, CircleShape, spotColor = CryptoColors.Accent.copy(alpha = 0.4f))
                .clip(CircleShape)
                .background(CryptoColors.Accent)
                .clickable { onSelect(2) },
            contentAlignment = Alignment.Center,
        ) {
            Icon(Icons.Filled.SwapHoriz, "Trade", tint = Color.White, modifier = Modifier.size(20.dp))
        }
    }
}
```

The Visa card sits in a horizontal `Pager` (carousel) on Home when the user has multiple cards; the focused card scales `0.94 → 1.0`. The promo/brand banner is the only large surface filled with `CryptoBrushes.BrandNavy`.

## 5. Motion

Crypto.com motion is premium and tactile — a bit more expressive than Binance for the card and CTAs, but still controlled (200–400ms).

| Moment | Compose recipe |
|--------|----------------|
| Card flip (reveal CVV) | `graphicsLayer { rotationY = anim }`, `animateFloatAsState(0↔180, tween(400))`; gate behind BiometricPrompt |
| Balance reveal | `Crossfade(hidden, animationSpec = tween(200))` between value and `••••••` |
| Floating Trade press | scale `1 → 0.92 → 1` via `Animatable` + `spring(dampingRatio = 0.55)` |
| Buy/Sell toggle | thumb `animateDpAsState` + bg `animateColorAsState` green↔red `tween(200)` |
| Price tick | text color `animateColorAsState` pulse to `Up/Down` for 150ms (no bg flash) |
| Card carousel | `HorizontalPager` + `pageOffset`-driven `graphicsLayer { scaleX = scaleY = lerp(0.94f,1f,…) }` |
| Sheet present | `ModalBottomSheet`; scrim fades in |
| Confirmation | success check `scaleIn(spring())` + success haptic |

```kotlin
val rotation by animateFloatAsState(if (flipped) 180f else 0f, tween(400), label = "cardFlip")
// Modifier.graphicsLayer { rotationY = rotation; cameraDistance = 12 * density }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the card flip; `HapticFeedbackType.TextHandleMove` (or `HapticFeedbackConstants.CLOCK_TICK`) for term/segment changes; a success vibration on completed transaction. Price ticks are silent.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. The Crypto.com brand mark and coin glyphs should be bundled vector assets.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Prices (tab) | `chart.bar.fill` | `Icons.Filled.BarChart` |
| Trade (center) | `arrow.left.arrow.right` | `Icons.Filled.SwapHoriz` |
| Earn (tab) | `percent` | `Icons.Filled.Percent` |
| Card (tab) | `creditcard.fill` | `Icons.Filled.CreditCard` |
| Notifications | `bell` | `Icons.Filled.Notifications` |
| Menu | `line.3.horizontal` | `Icons.Filled.Menu` |
| Hide balance | `eye` / `eye.slash` | `Icons.Outlined.Visibility` / `VisibilityOff` |
| Deposit | `plus` | `Icons.Filled.Add` |
| Send | `arrow.up.right` | `Icons.Filled.ArrowUpward` |
| Receive | `arrow.down.left` | `Icons.Filled.ArrowDownward` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Up tick | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Down tick | `arrow.down` | `Icons.Filled.ArrowDownward` |
| Stake / reward | `star.circle` | `Icons.Filled.StarBorder` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Lock card | `lock.fill` | `Icons.Filled.Lock` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; the card flip + pager + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the navy-dark canvas wants light-content system bars. The floating Trade action needs `Modifier.navigationBarsPadding()`; amount inputs need `Modifier.imePadding()`.
- **Tabular figures mandatory**: every numeric style sets `fontFeatureSettings = "tnum"`. Without it the watchlist prices and the card PAN don't align.
- **Font scaling**: `sp` honors the user scale — keep it on screen titles, section heads, body, labels. Pin card text (tier/PAN), numeric mono columns, and 10sp tab labels via `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` so the card layout and tabular alignment hold.
- **Card security**: gate the card flip / number reveal behind `androidx.biometric.BiometricPrompt`; never log or persist the full PAN/CVV; the displayed PAN should be masked until authenticated.
- **TalkBack**: the Visa card is one element — `Modifier.semantics { contentDescription = "Obsidian Visa card, ending 7310, 5 percent cashback" }`; watchlist rows announce "Bitcoin, 67,284 dollars, up 2.34 percent"; the floating action reads "Trade". Don't announce every price tick.
- **Color is not the only signal**: pair every green/red value with a `+`/`−` sign and an up/down arrow glyph (deuteranopia confuses `#00C08B`/`#F6485D`).
- **Touch targets**: Material guidance is 48.dp. Watchlist rows are ~56dp (full-row tap); the floating Trade circle is 44dp (acceptable for a primary FAB-like action but pad its hit area to 48dp); pill buttons ≥48dp; quick-action circles 46dp.
- **Contrast**: `#F4F7FB` on `#03060F` and white on `#1199FA` pass WCAG AA. The gold tier text on dark card faces is decorative — convey the tier via the accessibility label, not the gold contrast. Validate navy surface steps (`#0B1426`/`#15263F`/`#1C3354`) keep ≥3:1 separation.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the 3D card flip with a `Crossfade`, disable the floating-button spring and price pulse; keep instant data updates and the colored sign + arrow.
- **Dark mode**: primary mode — default to `CryptoDark`. Light only swaps canvas (`#FFFFFF`) and primary text (`#0A1F44`); **never invert** `Up`/`Down` and never change `Accent`/`Navy`/card brushes. Do **not** enable Material You `dynamicColorScheme()` — the two-blue system and metallic card identity must hold regardless of wallpaper. Floating sheets get a 1dp `Divider` border since shadows are subtle on `#03060F`.
