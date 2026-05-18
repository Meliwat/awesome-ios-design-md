# Deliveroo (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Deliveroo's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the restaurant feed and menu, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (white canvas, one Deliveroo Teal for everything, the fee pill, the floating teal `+`, heavy type) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a UIKit sheet, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for restaurant photos and thumbnails. Deliveroo's palette is one fixed brand color, so dynamic color extraction (Palette / Material You) is **not** used.

## 1. Color Tokens

```kotlin
// ui/theme/DeliverooColors.kt
import androidx.compose.ui.graphics.Color

object DeliverooColors {
    // Brand (the ONE color)
    val Teal        = Color(0xFF00CCBC)
    val TealPressed = Color(0xFF00A99C)
    val TealInk     = Color(0xFF003733) // on-teal content — NOT white

    // Membership & promo
    val PlusMint    = Color(0xFFC4F4EF)
    val PromoGold   = Color(0xFFFFC100)

    // Canvas & Surfaces (Light)
    val Canvas      = Color(0xFFFFFFFF)
    val Surface1    = Color(0xFFF4F4F2)
    val Surface2    = Color(0xFFEAEAE8)
    val Divider     = Color(0xFFE8E8E6)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF121212) // warm near-black — NOT pure black
    val DarkSurface1 = Color(0xFF1C1C1E)
    val DarkSurface2 = Color(0xFF262629)
    val DarkDivider  = Color(0xFF2C2C2E)

    // Text
    val TextPrimary     = Color(0xFF1D1D1B)
    val TextSecondary   = Color(0xFF6B6B6B)
    val TextTertiary    = Color(0xFFA0A0A0)
    val DarkTextPrimary = Color(0xFFF4F4F2)

    // Semantic
    val Error       = Color(0xFFE2483D)

    // Plus banner gradient (deep teal)
    val PlusBgFrom  = Color(0xFF003E3A)
    val PlusBgTo    = Color(0xFF00524B)
}
```

Wire it into both schemes. Deliveroo is light-first (white, photo-forward); the dark scheme uses a warm `#121212`, never true black. `primary` is the one teal; `onPrimary` is Teal Ink, not white.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val DeliverooLight = lightColorScheme(
    primary        = DeliverooColors.Teal,
    onPrimary      = DeliverooColors.TealInk,   // NOT white — Deliveroo signature
    background      = DeliverooColors.Canvas,
    onBackground   = DeliverooColors.TextPrimary,
    surface        = DeliverooColors.Surface1,
    onSurface      = DeliverooColors.TextPrimary,
    surfaceVariant = DeliverooColors.Surface2,
    outline        = DeliverooColors.Divider,
    error          = DeliverooColors.Error,
)

private val DeliverooDark = darkColorScheme(
    primary        = DeliverooColors.Teal,
    onPrimary      = DeliverooColors.TealInk,
    background      = DeliverooColors.DarkCanvas,
    onBackground   = DeliverooColors.DarkTextPrimary,
    surface        = DeliverooColors.DarkSurface1,
    onSurface      = DeliverooColors.DarkTextPrimary,
    surfaceVariant = DeliverooColors.DarkSurface2,
    outline        = DeliverooColors.DarkDivider,
    error          = DeliverooColors.Error,
)

@Composable
fun DeliverooTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) DeliverooDark else DeliverooLight,
    typography = DeliverooTypography,
    content = content,
)
```

## 2. Typography (Material 3)

Deliveroo's brand face is a custom heavy grotesque (proprietary); use **Inter** (SIL OFL) — drop the TTFs in `res/font/`. The app runs heavy: section headers/names at 800, screen titles at 900; numbers tabular.

```kotlin
// ui/theme/DeliverooType.kt
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
    Font(R.font.inter_black,     FontWeight.Black),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object DeliverooText {
    val ScreenTitle    = TextStyle(Inter, fontWeight = FontWeight.Black,     fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val RestaurantHero = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section        = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val Subsection     = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val RestaurantName = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 21.sp)
    val MenuName       = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp)
    val Body           = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 22.sp)
    val Price          = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 14.sp, lineHeight = 18.sp)
    val Meta           = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val FeePill        = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 17.sp)
    val Badge          = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = (-0.1).sp)
    val Button         = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 16.sp)
    val Tab            = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Caption        = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val DeliverooTypography = Typography(
    headlineLarge  = DeliverooText.ScreenTitle,
    headlineMedium = DeliverooText.RestaurantHero,
    titleLarge     = DeliverooText.Section,
    titleMedium    = DeliverooText.RestaurantName,
    bodyMedium     = DeliverooText.Body,
    labelSmall     = DeliverooText.Tab,
)
```

For tabular figures on prices/fees, use `TextStyle.copy(fontFeatureSettings = "tnum")`.

## 3. Signature Components

### Restaurant Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.DirectionsBike
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.FavoriteBorder
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun RestaurantCard(
    name: String, meta: String, rating: Double, fee: String,
    badge: String?, badgeIsPromo: Boolean, imageUrl: String,
    modifier: Modifier = Modifier,
) {
    var saved by remember { mutableStateOf(false) }
    Column(modifier.fillMaxWidth().padding(bottom = 22.dp)) {
        Box(Modifier.fillMaxWidth().aspectRatio(5f / 3f).clip(RoundedCornerShape(14.dp))) {
            AsyncImage(model = imageUrl, contentDescription = name,
                modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
            if (badge != null) {
                Text(badge, style = DeliverooText.Badge,
                    color = if (badgeIsPromo) Color(0xFF1A1206) else DeliverooColors.TealInk,
                    modifier = Modifier.padding(10.dp)
                        .clip(RoundedCornerShape(6.dp))
                        .background(if (badgeIsPromo) DeliverooColors.PromoGold else DeliverooColors.Teal)
                        .padding(horizontal = 9.dp, vertical = 4.dp))
            }
            Icon(
                if (saved) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder,
                contentDescription = if (saved) "Saved" else "Save",
                tint = if (saved) DeliverooColors.Teal else Color.White,
                modifier = Modifier.align(Alignment.TopEnd).padding(10.dp)
                    .size(32.dp).clip(RoundedCornerShape(50))
                    .background(Color.Black.copy(alpha = 0.4f))
                    .clickable { saved = !saved }.padding(8.dp),
            )
        }
        Row(Modifier.fillMaxWidth().padding(top = 10.dp), verticalAlignment = Alignment.CenterVertically) {
            Text(name, style = DeliverooText.RestaurantName, color = DeliverooColors.TextPrimary)
            Spacer(Modifier.weight(1f))
            Icon(Icons.Filled.Star, null, tint = DeliverooColors.Teal, modifier = Modifier.size(13.dp))
            Spacer(Modifier.width(4.dp))
            Text(String.format("%.1f", rating), style = DeliverooText.FeePill, color = DeliverooColors.TextPrimary)
        }
        Text(meta, style = DeliverooText.Meta, color = DeliverooColors.TextSecondary,
            modifier = Modifier.padding(top = 4.dp))
        Row(
            Modifier.padding(top = 8.dp).clip(RoundedCornerShape(50))
                .background(DeliverooColors.Surface1).padding(horizontal = 10.dp, vertical = 5.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(5.dp),
        ) {
            Icon(Icons.Filled.DirectionsBike, null, tint = DeliverooColors.Teal, modifier = Modifier.size(11.dp))
            Text(fee, style = DeliverooText.FeePill, color = DeliverooColors.TextPrimary)
        }
    }
}
```

### Menu Item Row (floating teal +)

```kotlin
import androidx.compose.foundation.shadow
import androidx.compose.material.icons.filled.Add
import androidx.compose.ui.draw.clip
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun MenuItemRow(
    name: String, desc: String, price: String, imageUrl: String,
    onAdd: () -> Unit, modifier: Modifier = Modifier,
) {
    Row(
        modifier.fillMaxWidth().padding(vertical = 14.dp),
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Column(Modifier.weight(1f)) {
            Text(name, style = DeliverooText.MenuName, color = DeliverooColors.TextPrimary)
            Text(desc, style = DeliverooText.Body, color = DeliverooColors.TextSecondary,
                maxLines = 2, overflow = TextOverflow.Ellipsis, modifier = Modifier.padding(top = 4.dp))
            Text(price, style = DeliverooText.Price, color = DeliverooColors.TextPrimary,
                modifier = Modifier.padding(top = 4.dp))
        }
        Box(Modifier.size(84.dp)) {
            AsyncImage(model = imageUrl, contentDescription = null,
                modifier = Modifier.size(84.dp).clip(RoundedCornerShape(12.dp)),
                contentScale = ContentScale.Crop)
            Box(
                Modifier.align(Alignment.BottomEnd)
                    .offset(x = 8.dp, y = 10.dp)        // hangs off the corner
                    .size(28.dp)
                    .shadow(4.dp, RoundedCornerShape(50))
                    .clip(RoundedCornerShape(50))
                    .background(DeliverooColors.Teal)
                    .clickable { onAdd() },
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Filled.Add, "Add $name", tint = DeliverooColors.TealInk, modifier = Modifier.size(16.dp)) }
        }
    }
    HorizontalDivider(color = DeliverooColors.Divider, thickness = 1.dp)
}
```

### Quantity Stepper

```kotlin
import androidx.compose.material.icons.filled.Remove
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun QuantityStepper(quantity: Int, onChange: (Int) -> Unit) {
    val haptics = LocalHapticFeedback.current
    Row(
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        StepControl(Icons.Filled.Remove, enabled = quantity > 1) {
            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove); onChange(quantity - 1)
        }
        Text("$quantity", style = DeliverooText.Button.copy(fontFeatureSettings = "tnum"),
            color = DeliverooColors.TextPrimary, modifier = Modifier.widthIn(min = 18.dp))
        StepControl(Icons.Filled.Add, enabled = true) {
            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove); onChange(quantity + 1)
        }
    }
}

@Composable
private fun StepControl(icon: androidx.compose.ui.graphics.vector.ImageVector, enabled: Boolean, onClick: () -> Unit) {
    Box(
        Modifier.size(32.dp).clip(RoundedCornerShape(50))
            .background(DeliverooColors.Teal.copy(alpha = if (enabled) 1f else 0.35f))
            .clickable(enabled = enabled) { onClick() },
        contentAlignment = Alignment.Center,
    ) { Icon(icon, null, tint = DeliverooColors.TealInk, modifier = Modifier.size(16.dp)) }
}
```

### Sticky Basket Bar

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.ui.draw.scale

@Composable
fun BasketBar(itemCount: Int, total: String, onClick: () -> Unit) {
    var bump by remember { mutableStateOf(false) }
    val scale by animateFloatAsState(if (bump) 1.03f else 1f, label = "basketPulse")
    LaunchedEffect(itemCount) { bump = true; kotlinx.coroutines.delay(120); bump = false }

    Box(
        Modifier.fillMaxWidth().padding(horizontal = 16.dp)
            .scale(scale)
            .heightIn(min = 52.dp)
            .clip(RoundedCornerShape(50))
            .background(DeliverooColors.Teal)
            .clickable { onClick() },
        contentAlignment = Alignment.Center,
    ) {
        Text("View basket · $itemCount items · $total",
            style = DeliverooText.Button, color = DeliverooColors.TealInk)
    }
}
```

### Category Icon Row

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.itemsIndexed
import androidx.compose.material.icons.filled.*

data class Cat(val icon: androidx.compose.ui.graphics.vector.ImageVector, val label: String)

@Composable
fun CategoryRow(cats: List<Cat>) {
    var selected by remember { mutableIntStateOf(0) }
    LazyRow(
        horizontalArrangement = Arrangement.spacedBy(18.dp),
        contentPadding = PaddingValues(horizontal = 16.dp),
    ) {
        itemsIndexed(cats) { i, c ->
            val active = i == selected
            Column(horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.spacedBy(7.dp),
                modifier = Modifier.clickable { selected = i }) {
                Box(
                    Modifier.size(54.dp).clip(RoundedCornerShape(16.dp))
                        .background(if (active) DeliverooColors.Teal else DeliverooColors.Surface1),
                    contentAlignment = Alignment.Center,
                ) { Icon(c.icon, c.label,
                        tint = if (active) DeliverooColors.TealInk else DeliverooColors.Teal,
                        modifier = Modifier.size(24.dp)) }
                Text(c.label, style = DeliverooText.Caption,
                    color = if (active) DeliverooColors.TextPrimary else DeliverooColors.TextSecondary)
            }
        }
    }
}
```

### Primary Button

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState

@Composable
fun RooButton(title: String, onClick: () -> Unit, dark: Boolean = false) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val bg = when {
        dark -> DeliverooColors.TextPrimary
        pressed -> DeliverooColors.TealPressed
        else -> DeliverooColors.Teal
    }
    Box(
        Modifier.fillMaxWidth().heightIn(min = 52.dp)
            .scale(if (pressed) 0.98f else 1f)
            .clip(RoundedCornerShape(50)).background(bg)
            .clickable(interaction, indication = null) { onClick() },
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = DeliverooText.Button,
            color = if (dark) Color.White else DeliverooColors.TealInk)
    }
}
// Checkout: RooButton("Checkout · £24.50", onClick = {}, dark = true)
```

## 4. Navigation

Deliveroo has a 5-tab bottom strip and no Material tint pill — active is just the one teal.

```kotlin
@Composable
fun DeliverooBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = DeliverooColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"       to Icons.Filled.Home,
            "Search"     to Icons.Filled.Search,
            "Orders"     to Icons.Filled.ReceiptLong,
            "Favourites" to Icons.Filled.FavoriteBorder,
            "Account"    to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = DeliverooText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = DeliverooColors.Teal,
                    selectedTextColor   = DeliverooColors.Teal,
                    unselectedIconColor = Color(0xFF888888),
                    unselectedTextColor = Color(0xFF888888),
                    indicatorColor      = Color.Transparent, // no Material pill — Deliveroo has none
                ),
            )
        }
    }
}
```

The address bar is a top `Row` (label + value + teal chevron, trailing teal basket circle); the item-customize, basket and address surfaces are `ModalBottomSheet` with a 16.dp top radius. The rider map uses Google Maps Compose with a teal moped marker; the sticky basket bar floats above the content with the count + total embedded.

## 5. Motion

Deliveroo motion is functional — confirm an add, slide the basket, advance a stage.

| Moment | Compose recipe |
|--------|----------------|
| Floating `+` add | `animateFloatAsState` scale 1 → 0.9 → 1 `spring(dampingRatio = 0.5f)`; then morph to inline stepper; basket bar `slideInVertically` `tween(280)` + `HapticFeedbackType.LongPress` |
| Quantity change | `Crossfade` / `AnimatedContent` on the count + `TextHandleMove` haptic |
| Basket bar pulse | `animateFloatAsState` 1 → 1.03 → 1 driven by an `itemCount` `LaunchedEffect` |
| Category tile select | background `animateColorAsState` `tween(150)`; glyph color flips |
| Card → menu | shared element via `SharedTransitionLayout` (Compose 1.7+) `tween(320)` |
| Search focus | pill outline `animateColorAsState` to teal `tween(150)`; results `fadeIn` + slide |
| Sheet present | `ModalBottomSheet` default slide; scrim fades in parallel |
| Order tracking advance | status node `animateDpAsState` `tween(250)` + `HapticFeedbackType.LongPress` (medium analog) |

```kotlin
// Floating + add — the canonical Deliveroo motion
val plusScale by animateFloatAsState(
    if (pressed) 0.9f else 1f, spring(dampingRatio = 0.5f), label = "plus")
Box(Modifier.scale(plusScale) /* the teal + circle */)
```

Haptics: `HapticFeedbackType.TextHandleMove` for the stepper tick; `HapticFeedbackType.LongPress` for add / stage-complete. Order-placed success can use `VibrationEffect.createPredefined(EFFECT_HEAVY_CLICK)` via the system `Vibrator`.

## 6. Icons

The closest first-party set to Deliveroo's iconography is `androidx.compose.material:material-icons-extended`. The moped/fee glyph maps to `DirectionsBike` (or a bundled scooter vector).

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Orders (tab) | `list.bullet.rectangle` | `Icons.Filled.ReceiptLong` |
| Favourites (tab) | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Icons.Filled.Favorite` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Rating star | `star.fill` | `Icons.Filled.Star` |
| Address chevron | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Basket | `bag` / `bag.fill` | `Icons.Filled.ShoppingBag` |
| Fee pill (moped) | `scooter` / `bicycle` | `Icons.Filled.DirectionsBike` |
| Floating add | `plus` | `Icons.Filled.Add` |
| Stepper minus | `minus` | `Icons.Filled.Remove` |
| Category — Restaurants | `fork.knife` | `Icons.Filled.Restaurant` |
| Category — Grocery | `cart` | `Icons.Filled.ShoppingCart` |
| Category — Fast | `clock` | `Icons.Filled.Schedule` |
| Category — Treats | `birthday.cake` | `Icons.Filled.Cake` |
| Category — Offers | `tag` | `Icons.Filled.LocalOffer` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Rider (tracking) | `scooter` | `Icons.Filled.DirectionsBike` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion and `SharedTransitionLayout` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the white canvas wants dark-content system bars (light-content in dark mode and over the tracking map). The address bar respects the camera cutout; the bottom tab + sticky basket bar use `Modifier.navigationBarsPadding()`.
- **On-teal color**: `onPrimary` is Teal Ink `#003733`, never white — this is the Deliveroo signature and the only WCAG-AA-passing pairing on the bright teal.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, restaurant names, body and prices. Pin layout-sensitive text (12sp badges, the fee pill, 10sp tab labels, category labels) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Tabular numerals**: apply `fontFeatureSettings = "tnum"` to prices, fees, minimums and quantity counters.
- **TalkBack**: a restaurant card is one focusable node — set a merged `contentDescription` "{name}, {rating} stars, {meta}, {fee}". The saved heart is a toggle. The floating `+` is "Add {item}" and the basket bar announces changes via `Modifier.semantics { liveRegion = LiveRegionMode.Polite }`.
- **Touch targets**: Material guidance is 48.dp. The 28.dp floating `+` and 32.dp stepper controls get a 48.dp hit area via padding; category tiles are 54.dp (full-tile tappable); primary buttons ≥ 52.dp.
- **Contrast**: `#1D1D1B` on `#FFFFFF` passes WCAG AAA; **Teal Ink `#003733` on `#00CCBC` passes AA** for button labels (white on teal does NOT — hence the dark ink).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the `+` bounce and the basket-bar pulse (substitute a `Crossfade`); keep stage advance instant.
- **Dark mode**: invert via the `Dark*` palette — `#121212`, NOT true black; `#1D1D1B` text becomes `#F4F4F2`. The one teal and Teal Ink are constant. Shadows are nearly invisible on dark, so add a 0.5dp `DarkDivider` border on floating surfaces (basket bar, customize sheet). Do **not** enable Material You `dynamicColorScheme()` — Deliveroo's mono-teal identity must hold regardless of wallpaper.
