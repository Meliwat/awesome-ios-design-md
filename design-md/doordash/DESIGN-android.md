# DoorDash (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports DoorDash's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (DoorDash's appetizing white canvas, single aggressive red CTA, photo-first merchant cards, the floating checkout button) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, Coil instead of `AsyncImage`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` — DoorDash is photo-first, so Coil for food imagery is required.

## 1. Color Tokens

```kotlin
// ui/theme/DoorDashColors.kt
import androidx.compose.ui.graphics.Color

object DoorDashColors {
    // Brand — the "spend money" color
    val Red        = Color(0xFFEB1700)
    val RedPressed = Color(0xFFC31500)
    val RedTint    = Color(0xFFFFEBE8) // promo banners — NOT full red

    // Canvas & Text
    val Canvas        = Color(0xFFFFFFFF)
    val Charcoal      = Color(0xFF191919) // primary text — NOT pure black
    val TextSecondary = Color(0xFF757575)
    val TextTertiary  = Color(0xFFAFAFAF)
    val Divider       = Color(0xFFE5E5E5)
    val SurfaceMuted  = Color(0xFFF7F7F7)
    val SurfaceTint   = Color(0xFFFAFAFA)

    // Semantic — orange + green are the ONLY semantic chromas
    val RatingOrange = Color(0xFFFF8000)
    val FeeGreen     = Color(0xFF008B4A)
    val Warning      = Color(0xFFF5B800)
    val Error        = Color(0xFFD1350F)
    val InfoBlue     = Color(0xFF0066E3)
    val DashPass     = Color(0xFF006B82)

    // Dark mode (NOT true black — keeps food appetizing)
    val DarkBackground = Color(0xFF191919)
    val DarkSurface1   = Color(0xFF262626)
    val DarkSurface2   = Color(0xFF303030)
    val DarkDivider    = Color(0xFF3A3A3A)
}
```

DoorDash is white-canvas first; wire a `lightColorScheme` with a warm-`#191919` dark counterpart. DoorDash Red is reserved for interactive commit moments — never a default container fill on non-actionable surfaces.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.runtime.Composable

private val DoorDashLight = lightColorScheme(
    primary        = DoorDashColors.Red,
    onPrimary      = DoorDashColors.Canvas,
    background     = DoorDashColors.Canvas,
    onBackground   = DoorDashColors.Charcoal,
    surface        = DoorDashColors.Canvas,
    onSurface      = DoorDashColors.Charcoal,
    surfaceVariant = DoorDashColors.SurfaceMuted,
    outline        = DoorDashColors.Divider,
    error          = DoorDashColors.Error,
)

private val DoorDashDark = darkColorScheme(
    primary        = DoorDashColors.Red,
    onPrimary      = DoorDashColors.Canvas,
    background     = DoorDashColors.DarkBackground,
    onBackground   = DoorDashColors.Canvas,
    surface        = DoorDashColors.DarkSurface1,
    onSurface      = DoorDashColors.Canvas,
    surfaceVariant = DoorDashColors.DarkSurface2,
    outline        = DoorDashColors.DarkDivider,
    error          = DoorDashColors.Error,
)

@Composable
fun DoorDashTheme(useDark: Boolean = false, content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (useDark) DoorDashDark else DoorDashLight,
        typography  = DoorDashTypography,
        content     = content,
    )
```

## 2. Typography

`TT Norms Pro` is proprietary (DoorDash's brand face — no separate display family). Drop the TTFs in `res/font/` (lowercase, snake_case). Fall back to the system sans (Roboto) and system monospace for order numbers / promo codes. Money is **never light** — every currency amount is weight 700.

```kotlin
// ui/theme/DoorDashType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val TTNormsPro = FontFamily(
    Font(R.font.tt_norms_pro_regular,  FontWeight.Normal),
    Font(R.font.tt_norms_pro_medium,   FontWeight.Medium),
    Font(R.font.tt_norms_pro_semibold, FontWeight.SemiBold),
    Font(R.font.tt_norms_pro_bold,     FontWeight.Bold),
)

private const val TNUM = "tnum" // tabular figures on prices

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object DoorDashText {
    val HeroTitle    = TextStyle(TTNormsPro, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val SectionTitle = TextStyle(TTNormsPro, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Merchant     = TextStyle(TTNormsPro, fontWeight = FontWeight.Bold,     fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val MenuItem     = TextStyle(TTNormsPro, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.15).sp)
    val Body         = TextStyle(TTNormsPro, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val PriceLarge   = TextStyle(TTNormsPro, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp, fontFeatureSettings = TNUM)
    val PriceBody    = TextStyle(TTNormsPro, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 18.sp, fontFeatureSettings = TNUM)
    val Meta         = TextStyle(TTNormsPro, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Chip         = TextStyle(TTNormsPro, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val ButtonCTA    = TextStyle(TTNormsPro, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 16.sp)
    val ButtonSmall  = TextStyle(TTNormsPro, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val Tab          = TextStyle(TTNormsPro, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val PromoBadge   = TextStyle(TTNormsPro, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.3.sp)
    val NavTitle     = TextStyle(TTNormsPro, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val NavLarge     = TextStyle(TTNormsPro, fontWeight = FontWeight.Bold,     fontSize = 34.sp, lineHeight = 37.sp, letterSpacing = (-0.5).sp)
}

val DoorDashTypography = Typography(
    displaySmall  = DoorDashText.NavLarge,
    headlineLarge = DoorDashText.HeroTitle,
    titleLarge    = DoorDashText.SectionTitle,
    titleMedium   = DoorDashText.Merchant,
    bodyMedium    = DoorDashText.Body,
    labelSmall    = DoorDashText.Tab,
)
```

## 3. Signature Components

### Floating Checkout CTA (the app's whole point)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp
import java.text.NumberFormat

@Composable
fun FloatingCheckoutCTA(
    itemCount: Int,
    total: Double,
    onTap: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, spring(dampingRatio = 0.7f, stiffness = 600f), label = "cta")
    val usd = NumberFormat.getCurrencyInstance()

    Row(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .scale(scale)
            .shadow(24.dp, RoundedCornerShape(28.dp), spotColor = DoorDashColors.Red.copy(alpha = 0.35f))
            .clip(RoundedCornerShape(28.dp))
            .background(if (pressed) DoorDashColors.RedPressed else DoorDashColors.Red)
            .clickable(interaction, indication = null, onClick = onTap)
            .height(52.dp)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        if (itemCount >= 1) {
            Box(
                Modifier.defaultMinSize(22.dp, 22.dp).clip(CircleShape).background(Color.White).padding(horizontal = 4.dp),
                contentAlignment = Alignment.Center,
            ) { Text("$itemCount", style = DoorDashText.PromoBadge, color = DoorDashColors.Red) }
            Spacer(Modifier.width(8.dp))
        }
        Text("Checkout", style = DoorDashText.ButtonCTA, color = Color.White)
        Spacer(Modifier.weight(1f))
        Text(usd.format(total), style = DoorDashText.ButtonCTA, color = Color.White)
    }
}
```

### Merchant Card (photo-first — never a placeholder)

```kotlin
import androidx.compose.foundation.layout.aspectRatio
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.FavoriteBorder
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Icon
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun MerchantCard(
    name: String,
    rating: Double,
    deliveryMinutes: Int,
    deliveryFee: String,
    hasFreeDelivery: Boolean,
    promoBadge: String?,
    photoUrl: String,
    isSaved: Boolean,
    onToggleSave: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .width(260.dp)
            .shadow(12.dp, RoundedCornerShape(12.dp), spotColor = Color.Black.copy(alpha = 0.06f))
            .background(DoorDashColors.Canvas),
    ) {
        Box {
            AsyncImage(
                model = photoUrl,
                contentDescription = name,
                contentScale = ContentScale.Crop,
                modifier = Modifier
                    .fillMaxWidth()
                    .aspectRatio(16f / 10f)
                    .clip(RoundedCornerShape(12.dp)),
            )
            promoBadge?.let {
                Text(
                    it.uppercase(),
                    style = DoorDashText.PromoBadge,
                    color = DoorDashColors.Red,
                    modifier = Modifier
                        .padding(8.dp)
                        .clip(RoundedCornerShape(4.dp))
                        .background(DoorDashColors.RedTint)
                        .padding(horizontal = 7.dp, vertical = 3.dp),
                )
            }
            Icon(
                imageVector = if (isSaved) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder,
                contentDescription = if (isSaved) "Remove from saved" else "Save",
                tint = if (isSaved) DoorDashColors.Red else Color.Black,
                modifier = Modifier
                    .align(Alignment.TopEnd)
                    .padding(8.dp)
                    .size(32.dp)
                    .clip(CircleShape)
                    .background(Color.White.copy(alpha = 0.85f)) // white scrim keeps icon legible
                    .clickable(onClick = onToggleSave)
                    .padding(7.dp),
            )
        }
        Column(Modifier.padding(12.dp), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(name, style = DoorDashText.Merchant, color = DoorDashColors.Charcoal)
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Icon(Icons.Filled.Star, null, tint = DoorDashColors.RatingOrange, modifier = Modifier.size(12.dp))
                Text("%.1f".format(rating), style = DoorDashText.Meta, color = DoorDashColors.Charcoal)
                Text("· $deliveryMinutes min", style = DoorDashText.Meta, color = DoorDashColors.TextSecondary)
                if (hasFreeDelivery) {
                    Text("· \$0 Delivery", style = DoorDashText.Meta.copy(fontWeight = FontWeight.SemiBold), color = DoorDashColors.FeeGreen)
                } else {
                    Text("· $deliveryFee", style = DoorDashText.Meta, color = DoorDashColors.TextSecondary)
                }
            }
        }
    }
}
```

### Category Chip

```kotlin
@Composable
fun CategoryChip(emoji: String, label: String, isActive: Boolean, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Row(
        modifier
            .clip(RoundedCornerShape(20.dp))
            .background(if (isActive) DoorDashColors.Charcoal else DoorDashColors.SurfaceMuted)
            .clickable(onClick = onClick)
            .padding(horizontal = 14.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Text(emoji, style = DoorDashText.Chip)
        Text(label, style = DoorDashText.Chip, color = if (isActive) Color.White else DoorDashColors.Charcoal)
    }
}
```

### Promo Badge

```kotlin
@Composable
fun PromoBadge(text: String, modifier: Modifier = Modifier) {
    Text(
        text.uppercase(),
        style = DoorDashText.PromoBadge,
        color = DoorDashColors.Red,
        modifier = modifier
            .clip(RoundedCornerShape(4.dp))
            .background(DoorDashColors.RedTint)
            .padding(horizontal = 8.dp, vertical = 4.dp),
    )
}
```

### Quantity Stepper

```kotlin
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Remove

@Composable
fun QuantityStepper(count: Int, onCountChange: (Int) -> Unit, min: Int = 0, max: Int = 99, modifier: Modifier = Modifier) {
    Row(modifier, verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(12.dp)) {
        Box(
            Modifier.size(28.dp).clip(CircleShape).background(DoorDashColors.SurfaceMuted)
                .clickable(enabled = count > min) { onCountChange(count - 1) },
            contentAlignment = Alignment.Center,
        ) {
            Icon(Icons.Filled.Remove, "Decrease", tint = if (count > min) DoorDashColors.Charcoal else DoorDashColors.TextTertiary, modifier = Modifier.size(14.dp))
        }
        Text("$count", style = DoorDashText.PriceLarge.copy(fontSize = 16.sp), color = DoorDashColors.Charcoal, modifier = Modifier.defaultMinSize(20.dp))
        Box(
            Modifier.size(28.dp).clip(CircleShape).background(DoorDashColors.SurfaceMuted)
                .clickable(enabled = count < max) { onCountChange(count + 1) },
            contentAlignment = Alignment.Center,
        ) {
            Icon(Icons.Filled.Add, "Increase", tint = DoorDashColors.Charcoal, modifier = Modifier.size(14.dp))
        }
    }
}
```

## 4. Add-to-Cart Pulse & Order-Placed Confetti (the signature dynamic)

DoorDash has no color extraction — its distinctive dynamic system is the **add-to-cart pulse driving the floating CTA into existence**, and the **full-screen red/orange confetti + success haptic on order placed**. Animate the cart badge with a spring bounce, then explode confetti from the top on confirmation.

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.drawscope.rotate
import kotlinx.coroutines.launch
import kotlin.random.Random

// Cart-badge bounce on add-to-cart
@Composable
fun rememberCartBadgeBump(itemCount: Int): Float {
    val scale = remember { Animatable(1f) }
    LaunchedEffect(itemCount) {
        if (itemCount > 0) {
            scale.animateTo(1.25f, spring(dampingRatio = 0.45f, stiffness = 700f))
            scale.animateTo(1f, spring(dampingRatio = 0.6f, stiffness = 500f))
        }
    }
    return scale.value
}

// Full-screen order-placed confetti — DoorDash red + rating orange
@Composable
fun OrderPlacedConfetti(visible: Boolean, modifier: Modifier = Modifier) {
    if (!visible) return
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(Unit) { haptics.performHapticFeedback(HapticFeedbackType.LongPress) } // ~iOS .success

    data class Piece(val x: Float, val color: Color, val delay: Int, val spin: Float)
    val palette = listOf(DoorDashColors.Red, DoorDashColors.RatingOrange, DoorDashColors.FeeGreen)
    val pieces = remember {
        List(14) { Piece(Random.nextFloat(), palette.random(), Random.nextInt(0, 220), Random.nextFloat() * 360f) }
    }
    val progress = remember { Animatable(0f) }
    LaunchedEffect(Unit) { progress.animateTo(1f, androidx.compose.animation.core.tween(1200)) }

    Canvas(modifier.fillMaxSize()) {
        pieces.forEach { p ->
            val t = ((progress.value * 1200f - p.delay) / 1200f).coerceIn(0f, 1f)
            val y = t * (size.height + 40f) - 20f
            rotate(p.spin * t, pivot = Offset(p.x * size.width, y)) {
                drawRoundRect(
                    color = p.color,
                    topLeft = Offset(p.x * size.width - 4f, y),
                    size = androidx.compose.ui.geometry.Size(8.dp.toPx(), 14.dp.toPx()),
                    cornerRadius = androidx.compose.ui.geometry.CornerRadius(2.dp.toPx()),
                )
            }
        }
    }
}
```

Add-to-cart button pulse: scale 0.95 → 1.05 → 1.0 over 250ms with a success haptic — pair it with the cart-badge bump above.

## 5. Navigation

DoorDash uses a **5-tab bottom bar** (Home / Grocery / Offers / Orders / Account) with always-visible labels — DoorDash labels its tabs. Active tint is DoorDash Red with a filled icon variant. The bar is solid (not translucent — Android has no live blur anyway); the floating checkout CTA hovers above it whenever the cart is non-empty.

```kotlin
import androidx.compose.material.icons.filled.Home
import androidx.compose.material.icons.filled.LocalGroceryStore
import androidx.compose.material.icons.filled.LocalOffer
import androidx.compose.material.icons.filled.Receipt
import androidx.compose.material.icons.filled.Person
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun DoorDashBottomBar(selected: Int, cartCount: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = DoorDashColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home" to Icons.Filled.Home,
            "Grocery" to Icons.Filled.LocalGroceryStore,
            "Offers" to Icons.Filled.LocalOffer,
            "Orders" to Icons.Filled.Receipt,
            "Account" to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    Box(contentAlignment = Alignment.TopEnd) {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp))
                        if (label == "Orders" && cartCount > 0) {
                            Box(
                                Modifier.offset(x = 8.dp, y = (-4).dp).defaultMinSize(16.dp, 16.dp)
                                    .clip(CircleShape).background(DoorDashColors.Red).padding(horizontal = 4.dp),
                                contentAlignment = Alignment.Center,
                            ) { Text("$cartCount", style = DoorDashText.Tab, color = Color.White) }
                        }
                    }
                },
                label = { Text(label, style = DoorDashText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = DoorDashColors.Red,
                    selectedTextColor = DoorDashColors.Red,
                    unselectedIconColor = DoorDashColors.TextSecondary,
                    unselectedTextColor = DoorDashColors.TextSecondary,
                    indicatorColor = Color.Transparent,
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Card entrance | `fadeIn` + `slideInVertically { it / 8 }`, ≈300ms ease-out |
| Add-to-cart | button `scale` 0.95 → 1.05 → 1.0 via `Animatable` (~250ms); `HapticFeedbackType.LongPress` (~iOS .success) |
| Cart badge update | `Animatable` spring bounce 1 → 1.25 → 1 (`dampingRatio = 0.45f`) |
| Carousel snap | `LazyRow` + `rememberSnapFlingBehavior` (rubber-band edge) |
| Order placed | full-screen `Canvas` confetti for 1.2s + success haptic + checkmark scale-in |
| CTA press | `animateFloatAsState` 1 → 0.98 with `spring(stiffness ≈ 600f)` |

```kotlin
// Carousel with snap + cards bleeding past the right edge ("there's more, swipe")
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.gestures.snapping.rememberSnapFlingBehavior
import androidx.compose.foundation.lazy.rememberLazyListState

val state = rememberLazyListState()
LazyRow(
    state = state,
    flingBehavior = rememberSnapFlingBehavior(state),
    contentPadding = PaddingValues(start = 16.dp, end = 48.dp), // last card peeks ~30%
    horizontalArrangement = Arrangement.spacedBy(12.dp),
) { /* MerchantCard items */ }
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(...)` for the order-placed `.success` notification feel.

## 7. Icons

DoorDash leans on SF Symbol equivalents; the closest first-party set is `androidx.compose.material:material-icons-extended`. The red DoorDash delivery pin / logo ships as a vector drawable loaded via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Heart (save) | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Star (rating) | `star.fill` | `Icons.Filled.Star` (tinted `#FF8000`) |
| Home tab | `house.fill` | `Icons.Filled.Home` |
| Grocery tab | `bag.fill` | `Icons.Filled.LocalGroceryStore` |
| Offers tab | `percent` | `Icons.Filled.LocalOffer` |
| Orders tab | `bag` | `Icons.Filled.Receipt` |
| Account tab | `person.fill` | `Icons.Filled.Person` |
| Cart | `bag` / `bag.fill` | `Icons.Filled.ShoppingBag` |
| Location | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Delivery | `bicycle` / `car.fill` | `Icons.Filled.DirectionsBike` / `Icons.Filled.DirectionsCar` |
| DashPass | `sparkles` | `Icons.Filled.AutoAwesome` |
| Stepper minus / plus | `minus` / `plus` | `Icons.Filled.Remove` / `Icons.Filled.Add` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Delivery pin | (custom red pin) | Vector drawable — DoorDash logo embedded |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; Coil + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. White canvas wants dark-content system bars (`isAppearanceLightStatusBars = true`); the floating checkout CTA must use `Modifier.windowInsetsPadding(WindowInsets.navigationBars)` so it hovers 16dp above the gesture nav and never collides with it.
- **Font scaling**: `sp` honors the user's font scale — keep it on merchant names, prices, body. Cap price numerals at 140% (`fontScale.coerceAtMost(1.4f)`) and category chips at 140% to prevent wrap breakage; tab labels scale to 14sp max. Prices carry `fontFeatureSettings = "tnum"` so amounts stay column-aligned.
- **TalkBack**: the checkout CTA is one node — `Modifier.semantics(mergeDescendants = true) { contentDescription = "Checkout, 2 items, total \$23.40" }`. Merchant card merges name + rating + delivery meta; the save heart is a separate toggle with state.
- **Touch targets**: Material guidance is 48dp minimum. The 52dp CTA is huge; the 28dp stepper circles and 32dp save heart need 48dp hit area via padding; category chips clear with `minHeight = 32.dp` plus padding.
- **Contrast**: `#191919` on white exceeds WCAG AAA; DoorDash Red `#EB1700` on white clears AA at 16sp bold (button text only). The real low-contrast pair is **`#757575` TextSecondary on white** — passes AA at 14sp+, validate at 11sp tab/promo labels and darken if targeting compliance. Pair `#FF8000` rating and `#008B4A` $0-fee with their glyphs so they read without color.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, skip the order-placed confetti and the cart-badge bounce — show a static checkmark instead.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — DoorDash Red `#EB1700` is the brand's "spend money" signal and must not shift with wallpaper. Strong-brand app: keep the curated scheme.
