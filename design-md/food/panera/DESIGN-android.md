# Panera (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Panera's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the order/rewards spine, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Panera's bakery-warm cream canvas, single Panera Green action color, food-photo-first cards, MyPanera rewards engine) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a SwiftUI sheet, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for food photography. No color extraction — Panera's palette is a fixed warm brand set, so Palette is not needed. Panera is light-first (bakery-warm); a full warm dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/PaneraColors.kt
import androidx.compose.ui.graphics.Color

object PaneraColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val Cream          = Color(0xFFF4EFE1)
    val Oat            = Color(0xFFFAF6EC)
    val SurfacePressed = Color(0xFFEDE6D4)
    val Divider        = Color(0xFFE7E0CF)

    // Canvas & Surfaces (Dark) — warm near-black, NOT cold gray
    val DarkCanvas   = Color(0xFF14140F)
    val DarkSurface1 = Color(0xFF1E1E18)
    val DarkSurface2 = Color(0xFF28281F)
    val DarkDivider  = Color(0xFF33332A)

    // Text — warm dark, NOT pure black
    val TextPrimary       = Color(0xFF2A2A1F)
    val TextSecondary     = Color(0xFF6C6655)
    val TextTertiary      = Color(0xFF9C9684)
    val DarkTextPrimary   = Color(0xFFF3EFE4)
    val DarkTextSecondary = Color(0xFFB4AE9C)

    // Brand (single action color)
    val Green        = Color(0xFF4C8B2B)
    val GreenBright  = Color(0xFF6BBE45) // dark-mode CTA / emphasis
    val GreenPressed = Color(0xFF3C6E22)
    val GreenTint    = Color(0xFFEAF3E3)
    val GreenTintDk  = Color(0xFF1E2A18)

    // Photographic accents
    val BreadTan   = Color(0xFFD8B271)
    val SoupOrange = Color(0xFFE07A2F)
    val Berry      = Color(0xFFB0324B)
    val GoldStar   = Color(0xFFF2B705)
    val SipCoffee  = Color(0xFF5A3A22)

    // Semantic
    val Error     = Color(0xFFD6452F)
    val ErrorDark = Color(0xFFE0594B)
}
```

Wire it into both schemes. Panera is light-first (bakery cream); the dark scheme uses the warm `#14140F`, never cold gray or true black, with `GreenBright` as the primary so CTAs stay legible.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val PaneraLight = lightColorScheme(
    primary        = PaneraColors.Green,
    onPrimary      = PaneraColors.Canvas,
    background     = PaneraColors.Canvas,
    onBackground   = PaneraColors.TextPrimary,
    surface        = PaneraColors.Cream,
    onSurface      = PaneraColors.TextPrimary,
    surfaceVariant = PaneraColors.Oat,
    outline        = PaneraColors.Divider,
    error          = PaneraColors.Error,
)

private val PaneraDark = darkColorScheme(
    primary        = PaneraColors.GreenBright, // brightened so CTA reads on #14140F
    onPrimary      = PaneraColors.DarkCanvas,
    background     = PaneraColors.DarkCanvas,
    onBackground   = PaneraColors.DarkTextPrimary,
    surface        = PaneraColors.DarkSurface1,
    onSurface      = PaneraColors.DarkTextPrimary,
    surfaceVariant = PaneraColors.DarkSurface2,
    outline        = PaneraColors.DarkDivider,
    error          = PaneraColors.ErrorDark,
)

@Composable
fun PaneraTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) PaneraDark else PaneraLight,
    typography = PaneraTypography,
    content = content,
)
```

## 2. Typography (M3)

Panera pairs a warm geometric display sans (**Poppins**, the closest free analog to its marketing face) with a neutral body sans (**Inter**) — both SIL OFL; drop the TTFs in `res/font/`. Display carries titles/names/prices/reward headlines; body carries descriptions and metadata.

```kotlin
// ui/theme/PaneraType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Poppins = FontFamily(
    Font(R.font.poppins_semibold,  FontWeight.SemiBold),
    Font(R.font.poppins_bold,      FontWeight.Bold),
    Font(R.font.poppins_extrabold, FontWeight.ExtraBold),
)
val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object PaneraText {
    val ScreenTitle = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.5).sp)
    val Hero        = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 33.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 18.sp, lineHeight = 23.sp)
    val ItemName    = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Price       = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 14.sp, lineHeight = 18.sp)
    val RewardTag   = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.4.sp)
    val Button      = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 18.sp, letterSpacing = 0.2.sp)
    val Body        = TextStyle(Inter,   fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val Meta        = TextStyle(Inter,   fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Pill        = TextStyle(Inter,   fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(Inter,   fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Caption     = TextStyle(Inter,   fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 17.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val PaneraTypography = Typography(
    headlineLarge  = PaneraText.ScreenTitle,
    headlineMedium = PaneraText.Hero,
    titleLarge     = PaneraText.Section,
    titleMedium    = PaneraText.CardTitle,
    bodyMedium     = PaneraText.Body,
    labelSmall     = PaneraText.Tab,
)
```

## 3. Signature Components

### MyPanera Rewards Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ChevronRight
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

@Composable
fun MyPaneraCard(headline: String, subtext: String, progress: Float, modifier: Modifier = Modifier) {
    Column(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(18.dp))
            .background(Brush.linearGradient(listOf(PaneraColors.Green, PaneraColors.GreenBright)))
            .padding(16.dp),
    ) {
        Row(Modifier.fillMaxWidth(), verticalAlignment = Alignment.CenterVertically) {
            Text("MYPANERA REWARDS",
                style = PaneraText.RewardTag.copy(letterSpacing = 0.6.sp),
                color = Color.White.copy(alpha = 0.92f))
            Spacer(Modifier.weight(1f))
            Icon(Icons.Filled.ChevronRight, null, tint = Color.White.copy(alpha = 0.9f), modifier = Modifier.size(16.dp))
        }
        Text(headline, style = PaneraText.CardTitle.copy(fontSize = 19.sp), color = Color.White, modifier = Modifier.padding(top = 8.dp))
        Text(subtext, style = PaneraText.Caption, color = Color.White.copy(alpha = 0.92f), modifier = Modifier.padding(top = 3.dp))
        Box(Modifier.fillMaxWidth().height(6.dp).padding(top = 0.dp).clip(RoundedCornerShape(3.dp))
                .background(Color.White.copy(alpha = 0.28f))) {
            Box(Modifier.fillMaxWidth(progress).fillMaxHeight().clip(RoundedCornerShape(3.dp)).background(Color.White))
        }
    }
}
```

### Menu Item Row

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.material.icons.filled.Add
import coil.compose.AsyncImage
import androidx.compose.ui.layout.ContentScale

@Composable
fun MenuItemRow(
    imageUrl: String, name: String, desc: String, price: String,
    rewardTag: String? = null, onAdd: () -> Unit, modifier: Modifier = Modifier,
) {
    Row(
        modifier.fillMaxWidth().padding(vertical = 14.dp),
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        AsyncImage(
            model = imageUrl, contentDescription = null,
            modifier = Modifier.size(92.dp).clip(RoundedCornerShape(14.dp)),
            contentScale = ContentScale.Crop,
        )
        Column(Modifier.weight(1f)) {
            Text(name, style = PaneraText.ItemName, color = PaneraColors.TextPrimary)
            Text(desc, style = PaneraText.Caption, color = PaneraColors.TextSecondary, maxLines = 2, modifier = Modifier.padding(top = 3.dp))
            if (rewardTag != null) {
                Text("★ $rewardTag", style = PaneraText.RewardTag, color = PaneraColors.Green, modifier = Modifier.padding(top = 4.dp))
            }
            Row(Modifier.fillMaxWidth().padding(top = 8.dp), verticalAlignment = Alignment.CenterVertically) {
                Text(price, style = PaneraText.Price, color = PaneraColors.TextPrimary)
                Spacer(Modifier.weight(1f))
                Box(
                    Modifier.size(30.dp).clip(RoundedCornerShape(50)).background(PaneraColors.Green).clickable { onAdd() },
                    contentAlignment = Alignment.Center,
                ) { Icon(Icons.Filled.Add, "Add to order", tint = Color.White, modifier = Modifier.size(15.dp)) }
            }
        }
    }
    HorizontalDivider(thickness = 0.5.dp, color = PaneraColors.Divider)
}
```

### Quantity Stepper

```kotlin
import androidx.compose.material.icons.filled.Remove
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun QuantityStepper(count: Int, onCount: (Int) -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    Row(
        modifier
            .clip(RoundedCornerShape(50))
            .background(PaneraColors.Oat)
            .border(1.dp, PaneraColors.Divider, RoundedCornerShape(50))
            .padding(horizontal = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        IconButton(onClick = { if (count > 1) { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onCount(count - 1) } },
            modifier = Modifier.size(44.dp)) {
            Icon(Icons.Filled.Remove, "Decrease", tint = PaneraColors.Green)
        }
        Text("$count", style = PaneraText.ItemName, color = PaneraColors.TextPrimary)
        IconButton(onClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onCount(count + 1) },
            modifier = Modifier.size(44.dp)) {
            Icon(Icons.Filled.Add, "Increase", tint = PaneraColors.Green)
        }
    }
}
```

### Sticky "Add to Order" Bar

```kotlin
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.Surface

@Composable
fun AddToOrderBar(priceLabel: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    Surface(color = PaneraColors.Canvas, shadowElevation = 12.dp, modifier = modifier.fillMaxWidth()) {
        Button(
            onClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onClick() },
            shape = RoundedCornerShape(50),
            colors = ButtonDefaults.buttonColors(containerColor = PaneraColors.Green, contentColor = Color.White),
            modifier = Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 10.dp).height(52.dp),
        ) {
            Text("Add to Order · $priceLabel", style = PaneraText.Button)
        }
    }
}
```

### Pickup / Delivery Toggle

```kotlin
@Composable
fun PickupDeliveryToggle(isPickup: Boolean, onChange: (Boolean) -> Unit, modifier: Modifier = Modifier) {
    Row(
        modifier.clip(RoundedCornerShape(50)).background(PaneraColors.Oat)
            .border(1.dp, PaneraColors.Divider, RoundedCornerShape(50)).padding(4.dp),
    ) {
        @Composable fun Seg(label: String, on: Boolean, tap: () -> Unit) {
            Box(
                Modifier.weight(1f).clip(RoundedCornerShape(50))
                    .background(if (on) PaneraColors.Green else Color.Transparent)
                    .clickable { tap() }.padding(vertical = 10.dp),
                contentAlignment = Alignment.Center,
            ) { Text(label, style = PaneraText.Pill, color = if (on) Color.White else PaneraColors.TextSecondary) }
        }
        Seg("Pickup", isPickup) { onChange(true) }
        Seg("Delivery", !isPickup) { onChange(false) }
    }
}
```

## 4. Navigation

Panera has minimal chrome: a 5-tab bottom bar over a food-photo-driven content area. On Android, model it as a `NavigationBar`. There is no Material tint pill — active is the brand green; in dark mode use `GreenBright` so it reads on `#14140F`.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun PaneraBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = PaneraColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"      to Icons.Filled.Home,
            "Order"     to Icons.Filled.ShoppingBag,
            "Rewards"   to Icons.Filled.Star,
            "Favorites" to Icons.Filled.Bookmark,
            "Account"   to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = PaneraText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = PaneraColors.Green,   // GreenBright in dark scheme
                    selectedTextColor = PaneraColors.Green,
                    unselectedIconColor = PaneraColors.TextTertiary,
                    unselectedTextColor = PaneraColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Panera has none
                ),
            )
        }
    }
}
```

The cart is a `ModalBottomSheet` (rounded 24.dp top) listing line items with a thumbnail, name, `QuantityStepper`, and price; a Subtotal / Rewards-applied / Total stack; and a pinned full-width green "Checkout" button. Applied-reward rows tint `GreenTint` with a green checkmark.

## 5. Motion

Panera motion is gentle and appetizing — soft 180–600ms ease-out, never harsh.

| Moment | Compose recipe |
|--------|----------------|
| Add to Order | button `scale` 1 → 0.98 on press `tween(120)`; cart badge `animateIntAsState`; soft haptic |
| Rewards progress fill | `animateFloatAsState(progress, tween(600, easing = FastOutSlowInEasing))` width fraction |
| Category underline | `animateDpAsState` x-offset `tween(200)`; list `Crossfade` |
| Item detail open | shared element via `LookaheadScope` / Nav3 transition `tween(300)` |
| Cart sheet | `ModalBottomSheet` default slide; backdrop scrim auto |
| Pickup/Delivery toggle | selected fill `animateColorAsState` + position `tween(180)` |
| Pull to refresh | `PullToRefreshBox` with a Panera-mark indicator |

```kotlin
// Rewards progress — the canonical Panera loyalty motion
val animated by animateFloatAsState(
    targetValue = progress,
    animationSpec = tween(600, easing = FastOutSlowInEasing),
    label = "rewardProgress",
)
Box(Modifier.fillMaxWidth(animated).fillMaxHeight().background(Color.White))
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on Add to Order, stepper tap, and toggle switch. For the "reward unlocked" celebration use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)`. Auto-saved cart edits are silent; only show a snackbar on error.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Order (tab) | `bag.fill` | `Icons.Filled.ShoppingBag` |
| Rewards (tab) | `star.circle.fill` | `Icons.Filled.Star` |
| Favorites (tab) | `bookmark.fill` | `Icons.Filled.Bookmark` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Add to order | `plus` | `Icons.Filled.Add` |
| Stepper minus / plus | `minus` / `plus` | `Icons.Filled.Remove` / `Icons.Filled.Add` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Favorite (heart) | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Reward star | `star.fill` | `Icons.Filled.Star` |
| Sip Club | `cup.and.saucer.fill` | `Icons.Filled.LocalCafe` |
| Pickup / location | `mappin.and.ellipse` | `Icons.Filled.Place` |
| Delivery | `bicycle` | `Icons.Filled.DeliveryDining` |
| Calories / info | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the warm cream canvas wants dark-content system bars (light-content in dark mode). The full-bleed item-detail hero photo wants light-content status icons with a top scrim; the sticky "Add to Order" bar must sit above the navigation bar inset (`Modifier.navigationBarsPadding()`).
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, item names, descriptions, prices, calorie counts. Pin layout-sensitive text (10sp tab labels, reward tags, filter chips, stepper glyphs) by deriving from `dp` or a fixed-`fontScale` density.
- **TalkBack**: label menu rows "{name}, {price}, {calories}, double-tap to view; actions: Add to order"; expose the add `+` as a separate action; the rewards card announces "MyPanera, {headline}, {percent} percent to next reward".
- **Touch targets**: Material guidance is 48.dp. The 30.dp add-button and 14.dp stepper glyphs get 44–48.dp hit areas via `Modifier.size`; menu rows are full-row tappable (≥ 100.dp); the primary button is 52.dp.
- **Contrast**: `#2A2A1F` on `#FFFFFF` and on `#F4EFE1` passes WCAG AA for body at 16sp; white text on `#4C8B2B` passes AA. In dark mode use `#6BBE45` for actions so contrast holds on `#14140F`.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the Add-to-Order scale-bounce and the rewards-progress sweep — substitute an instant value set; keep the cart badge count change.
- **Dark mode**: invert via the `Dark*` palette — `#14140F`, NOT true black or cold gray; `#2A2A1F` text becomes `#F3EFE4`; the action color becomes `GreenBright`. Do **not** enable Material You `dynamicColorScheme()` — Panera's bakery-warm-and-green identity must hold regardless of wallpaper (the single green action color is the brand).
