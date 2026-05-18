# Swiggy (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Swiggy's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (photo-forward restaurant card, dish row with the floating ADD button, rating chip, floating cart bar), navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Swiggy Orange `#FC8019`, photo-forward cards with bottom scrims, the green rating chip, the floating ADD button, dashed footers) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Card`/`Surface` semantics, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for food imagery. No color extraction — Swiggy's palette is a fixed brand set, so Palette is not needed. Swiggy is light-first; a full dark scheme is provided (near-black `#121212`).

## 1. Color Tokens

```kotlin
// ui/theme/SwiggyColors.kt
import androidx.compose.ui.graphics.Color

object SwiggyColors {
    // Brand
    val Orange        = Color(0xFFFC8019)
    val OrangePressed = Color(0xFFE06D0C)
    val OrangeTint    = Color(0x1AFC8019) // 10%

    // Rating & trust
    val RatingGreen = Color(0xFF48C479)
    val RatingAmber = Color(0xFFDB7C38)
    val RatingRed   = Color(0xFFE84A4A)

    // Vertical accents
    val InstamartPurple = Color(0xFF982C61)
    val DineoutRed      = Color(0xFFD7385E)

    // Canvas & Surfaces (Light)
    val Canvas    = Color(0xFFFFFFFF)
    val Surface1L = Color(0xFFF4F4F6)
    val Surface2L = Color(0xFFECECEE)
    val DividerL  = Color(0xFFE2E2E7)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF121212) // near-black, NOT pure black
    val DarkSurface1 = Color(0xFF1C1C1E)
    val DarkSurface2 = Color(0xFF2A2A2A)
    val DarkDivider  = Color(0xFF2C2C2E)

    // Text
    val TextPrimaryL   = Color(0xFF02060C)
    val TextSecondaryL = Color(0xFF686B78)
    val TextTertiaryL  = Color(0xFF93959F)
    val TextPrimaryD   = Color(0xFFF2F2F2)
    val TextSecondaryD = Color(0xFFA0A0A0)

    // Semantic
    val Error = Color(0xFFE84A4A)
    val Info  = Color(0xFF1A73E8)
}

fun ratingColor(score: Double): Color = when {
    score >= 4.0 -> SwiggyColors.RatingGreen
    score >= 3.0 -> SwiggyColors.RatingAmber
    else         -> SwiggyColors.RatingRed
}
```

Wire it into both schemes. Swiggy is light-first; the dark scheme uses the signature `#121212` charcoal, never true black, and the brand orange is identical in both.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val SwiggyLight = lightColorScheme(
    primary        = SwiggyColors.Orange,
    onPrimary      = Color.White,
    background     = SwiggyColors.Canvas,
    onBackground   = SwiggyColors.TextPrimaryL,
    surface        = SwiggyColors.Surface1L,
    onSurface      = SwiggyColors.TextPrimaryL,
    surfaceVariant = SwiggyColors.Surface2L,
    outline        = SwiggyColors.DividerL,
    error          = SwiggyColors.Error,
)

private val SwiggyDark = darkColorScheme(
    primary        = SwiggyColors.Orange,
    onPrimary      = Color.White,
    background     = SwiggyColors.DarkCanvas,
    onBackground   = SwiggyColors.TextPrimaryD,
    surface        = SwiggyColors.DarkSurface1,
    onSurface      = SwiggyColors.TextPrimaryD,
    surfaceVariant = SwiggyColors.DarkSurface2,
    outline        = SwiggyColors.DarkDivider,
    error          = SwiggyColors.Error,
)

@Composable
fun SwiggyTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) SwiggyDark else SwiggyLight,
    typography = SwiggyTypography,
    content = content,
)
```

## 2. Typography

Swiggy uses a ProximaNova-lineage display face and a Basis-Grotesque-style UI face — drop the licensed TTFs in `res/font/`. Weight carries hierarchy (700/800 on names, prices, offers). Numerals are tabular for money/time/ratings.

```kotlin
// ui/theme/SwiggyType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextGeometricTransform
import androidx.compose.ui.unit.sp

val ProximaNova = FontFamily(
    Font(R.font.proximanova_regular,  FontWeight.Normal),
    Font(R.font.proximanova_semibold, FontWeight.SemiBold),
    Font(R.font.proximanova_bold,     FontWeight.Bold),
)
val BasisGrotesque = FontFamily(
    Font(R.font.basisgrotesque_regular, FontWeight.Normal),
    Font(R.font.basisgrotesque_medium,  FontWeight.Medium),
    Font(R.font.basisgrotesque_bold,    FontWeight.Bold),
)

object SwiggyText {
    val ScreenTitle = TextStyle(ProximaNova,  fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.4).sp)
    val Hero        = TextStyle(ProximaNova,  fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 33.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(ProximaNova,  fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(ProximaNova,  fontWeight = FontWeight.ExtraBold, fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.3).sp)
    val Subsection  = TextStyle(ProximaNova,  fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Location    = TextStyle(ProximaNova,  fontWeight = FontWeight.ExtraBold, fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val Button      = TextStyle(ProximaNova,  fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp, letterSpacing = 0.2.sp)
    val Add         = TextStyle(ProximaNova,  fontWeight = FontWeight.ExtraBold, fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
    val Badge       = TextStyle(ProximaNova,  fontWeight = FontWeight.ExtraBold, fontSize = 12.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
    val Body        = TextStyle(BasisGrotesque, fontWeight = FontWeight.Normal,  fontSize = 16.sp, lineHeight = 24.sp)
    val DishTitle   = TextStyle(BasisGrotesque, fontWeight = FontWeight.SemiBold,fontSize = 15.sp, lineHeight = 21.sp)
    val Price       = TextStyle(BasisGrotesque, fontWeight = FontWeight.SemiBold,fontSize = 14.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(BasisGrotesque, fontWeight = FontWeight.Normal,  fontSize = 14.sp, lineHeight = 20.sp)
    val Rating      = TextStyle(BasisGrotesque, fontWeight = FontWeight.Bold,    fontSize = 12.sp, lineHeight = 12.sp)
    val Caption     = TextStyle(BasisGrotesque, fontWeight = FontWeight.Normal,  fontSize = 12.sp, lineHeight = 17.sp)
    val Tab         = TextStyle(BasisGrotesque, fontWeight = FontWeight.Medium,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val SwiggyTypography = Typography(
    headlineLarge = SwiggyText.ScreenTitle,
    headlineMedium = SwiggyText.Section,
    titleMedium   = SwiggyText.CardTitle,
    bodyMedium    = SwiggyText.Body,
    labelSmall    = SwiggyText.Tab,
)
```

## 3. Signature Components

### Rating Chip

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

@Composable
fun RatingChip(score: Double) {
    Row(
        Modifier
            .clip(RoundedCornerShape(6.dp))
            .background(ratingColor(score))
            .padding(horizontal = 6.dp, vertical = 2.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(3.dp),
    ) {
        Icon(Icons.Filled.Star, null, tint = Color.White, modifier = Modifier.size(9.dp))
        Text("%.1f".format(score), style = SwiggyText.Rating, color = Color.White)
    }
}
```

### Veg / Non-Veg Mark

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.ChangeHistory // triangle

@Composable
fun DietMark(isVeg: Boolean) {
    val tint = if (isVeg) SwiggyColors.RatingGreen else SwiggyColors.Error
    Box(
        Modifier
            .size(16.dp)
            .border(1.5.dp, tint, RoundedCornerShape(3.dp)),
        contentAlignment = Alignment.Center,
    ) {
        if (isVeg) {
            Box(Modifier.size(7.dp).clip(CircleShape).background(tint))
        } else {
            Icon(Icons.Filled.ChangeHistory, null, tint = tint, modifier = Modifier.size(10.dp))
        }
    }
}
```

### Photo-Forward Restaurant Card

```kotlin
import androidx.compose.foundation.layout.Column
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.layout.ContentScale
import androidx.compose.material.icons.filled.AccessTime
import androidx.compose.material.icons.filled.FavoriteBorder
import coil.compose.AsyncImage

@Composable
fun RestaurantCard(
    imageUrl: String, offerTitle: String, offerSub: String, name: String,
    rating: Double, eta: String, cuisines: String, locality: String,
    footer: String, isAd: Boolean = false, modifier: Modifier = Modifier,
) {
    Column(modifier.fillMaxWidth().padding(bottom = 22.dp)) {
        Box(
            Modifier
                .fillMaxWidth()
                .aspectRatio(16f / 10f)
                .clip(RoundedCornerShape(16.dp)),
        ) {
            AsyncImage(imageUrl, null, Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
            Box(
                Modifier.matchParentSize().background(
                    Brush.verticalGradient(
                        0.45f to Color.Transparent, 1f to Color.Black.copy(alpha = 0.78f)
                    )
                )
            )
            Column(Modifier.align(Alignment.BottomStart).padding(12.dp)) {
                Text(offerTitle, style = SwiggyText.CardTitle, color = Color.White)
                Text(offerSub, style = SwiggyText.Badge, color = Color.White.copy(alpha = 0.92f))
            }
            if (isAd) {
                Box(
                    Modifier.align(Alignment.TopStart).padding(12.dp)
                        .clip(RoundedCornerShape(5.dp)).background(Color.Black.copy(alpha = 0.4f))
                        .padding(horizontal = 7.dp, vertical = 3.dp)
                ) { Text("AD", style = SwiggyText.Badge, color = Color.White) }
            }
            Box(
                Modifier.align(Alignment.TopEnd).padding(12.dp)
                    .size(32.dp).clip(CircleShape).background(Color.Black.copy(alpha = 0.4f)),
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Filled.FavoriteBorder, null, tint = Color.White, modifier = Modifier.size(16.dp)) }
        }

        Text(name, style = SwiggyText.CardTitle, color = SwiggyColors.TextPrimaryD, modifier = Modifier.padding(top = 10.dp))
        Row(
            Modifier.padding(top = 6.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            RatingChip(rating)
            Text("·", color = SwiggyColors.TextTertiaryL)
            Text(eta, style = SwiggyText.Price, color = SwiggyColors.TextPrimaryD)
        }
        Text(cuisines, style = SwiggyText.Meta, color = SwiggyColors.TextSecondaryD, modifier = Modifier.padding(top = 4.dp))
        Text(locality, style = SwiggyText.Meta, color = SwiggyColors.TextSecondaryD, modifier = Modifier.padding(top = 2.dp))

        DashedDivider(Modifier.padding(top = 10.dp))
        Row(
            Modifier.padding(top = 10.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Icon(Icons.Filled.AccessTime, null, tint = SwiggyColors.Orange, modifier = Modifier.size(13.dp))
            Text(footer, style = SwiggyText.Rating, color = SwiggyColors.Orange)
        }
    }
}

@Composable
fun DashedDivider(modifier: Modifier = Modifier) {
    androidx.compose.foundation.Canvas(modifier.fillMaxWidth().height(1.dp)) {
        drawLine(
            color = SwiggyColors.DarkDivider,
            start = androidx.compose.ui.geometry.Offset(0f, 0f),
            end = androidx.compose.ui.geometry.Offset(size.width, 0f),
            pathEffect = androidx.compose.ui.graphics.PathEffect.dashPathEffect(floatArrayOf(8f, 8f)),
        )
    }
}
```

### Dish Row with Floating ADD Button

```kotlin
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Remove
import androidx.compose.material3.Surface
import androidx.compose.runtime.*

@Composable
fun DishRow(
    isVeg: Boolean, name: String, price: String, rating: Double,
    ratingCount: String, desc: String, imageUrl: String,
) {
    var qty by remember { mutableIntStateOf(0) }
    Row(
        Modifier
            .fillMaxWidth()
            .padding(vertical = 16.dp),
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(6.dp)) {
            DietMark(isVeg)
            Text(name, style = SwiggyText.DishTitle, color = SwiggyColors.TextPrimaryD)
            Text(price, style = SwiggyText.Price, color = SwiggyColors.TextPrimaryD)
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(3.dp)) {
                Icon(Icons.Filled.Star, null, tint = SwiggyColors.RatingGreen, modifier = Modifier.size(10.dp))
                Text("%.1f ($ratingCount)".format(rating), style = SwiggyText.Rating, color = SwiggyColors.RatingGreen)
            }
            Text(desc, style = SwiggyText.Caption, color = SwiggyColors.TextSecondaryD, maxLines = 2)
        }

        Box(Modifier.width(110.dp), contentAlignment = Alignment.BottomCenter) {
            AsyncImage(
                imageUrl, null,
                Modifier.size(110.dp).clip(RoundedCornerShape(14.dp)),
                contentScale = ContentScale.Crop,
            )
            Surface(
                shape = RoundedCornerShape(10.dp),
                color = SwiggyColors.DarkSurface2,
                border = androidx.compose.foundation.BorderStroke(1.dp, SwiggyColors.RatingGreen),
                shadowElevation = 8.dp,
                modifier = Modifier.offset(y = 12.dp),
            ) {
                if (qty == 0) {
                    Box(
                        Modifier
                            .clickable {
                                qty = 1 // soft haptic at call site
                            }
                            .padding(horizontal = 26.dp, vertical = 7.dp)
                    ) { Text("ADD", style = SwiggyText.Add, color = SwiggyColors.RatingGreen) }
                } else {
                    Row(
                        Modifier.padding(horizontal = 14.dp, vertical = 7.dp),
                        verticalAlignment = Alignment.CenterVertically,
                        horizontalArrangement = Arrangement.spacedBy(14.dp),
                    ) {
                        Icon(Icons.Filled.Remove, "Decrease", tint = SwiggyColors.RatingGreen,
                            modifier = Modifier.size(16.dp).clickable { qty-- })
                        Text("$qty", style = SwiggyText.Add, color = SwiggyColors.RatingGreen)
                        Icon(Icons.Filled.Add, "Increase", tint = SwiggyColors.RatingGreen,
                            modifier = Modifier.size(16.dp).clickable { qty++ })
                    }
                }
            }
        }
    }
    DashedDivider()
}
```

### Primary Button

```kotlin
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults

@Composable
fun SwiggyPrimaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Button(
        onClick = onClick,
        interactionSource = interaction,
        shape = RoundedCornerShape(12.dp),
        colors = ButtonDefaults.buttonColors(
            containerColor = if (pressed) SwiggyColors.OrangePressed else SwiggyColors.Orange,
            contentColor = Color.White,
        ),
        modifier = modifier.fillMaxWidth().height(50.dp),
    ) {
        Text(text.uppercase(), style = SwiggyText.Button)
    }
}
```

### Floating Cart Bar

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.slideInVertically
import androidx.compose.animation.slideOutVertically
import androidx.compose.animation.core.spring
import androidx.compose.material.icons.filled.ChevronRight

@Composable
fun CartBar(visible: Boolean, itemCount: Int, total: String, onView: () -> Unit) {
    AnimatedVisibility(
        visible,
        enter = slideInVertically(spring(dampingRatio = 0.8f)) { it },
        exit = slideOutVertically { it },
    ) {
        Row(
            Modifier
                .fillMaxWidth()
                .padding(horizontal = 12.dp)
                .clip(RoundedCornerShape(12.dp))
                .background(SwiggyColors.Orange)
                .clickable(onClick = onView)
                .padding(horizontal = 18.dp, vertical = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text(
                "$itemCount item${if (itemCount == 1) "" else "s"}  |  $total",
                style = SwiggyText.CardTitle, color = Color.White,
            )
            Spacer(Modifier.weight(1f))
            Text("VIEW CART", style = SwiggyText.Add, color = Color.White)
            Icon(Icons.Filled.ChevronRight, null, tint = Color.White, modifier = Modifier.size(16.dp))
        }
    }
}
```

## 4. Navigation

Swiggy has a 4-tab bottom strip (Swiggy / Instamart / Dineout / Cart). On Android, model it as a `NavigationBar`. Active is a filled glyph in Swiggy Orange — there is **no** Material tint pill.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun SwiggyBottomBar(selected: Int, cartCount: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = SwiggyColors.DarkCanvas,
        tonalElevation = 0.dp,
    ) {
        data class Item(val label: String, val icon: androidx.compose.ui.graphics.vector.ImageVector, val badge: Int = 0)
        val items = listOf(
            Item("Swiggy", Icons.Filled.Home),
            Item("Instamart", Icons.Filled.GridView),
            Item("Dineout", Icons.Filled.Send),
            Item("Cart", Icons.Filled.ShoppingCart, cartCount),
        )
        items.forEachIndexed { i, it ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (it.badge > 0) {
                        BadgedBox(badge = { Badge(containerColor = SwiggyColors.Orange) { Text("${it.badge}") } }) {
                            Icon(it.icon, it.label, Modifier.size(22.dp))
                        }
                    } else Icon(it.icon, it.label, Modifier.size(22.dp))
                },
                label = { Text(it.label, style = SwiggyText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = SwiggyColors.Orange,
                    selectedTextColor = SwiggyColors.Orange,
                    unselectedIconColor = Color(0xFF888888),
                    unselectedTextColor = Color(0xFF888888),
                    indicatorColor = Color.Transparent, // no Material pill — Swiggy has none
                ),
            )
        }
    }
}
```

The sticky location header (orange `mappin`, "Home ▾" `Location` text, address sub-line, trailing avatar) sits above a 46.dp search field (`DarkSurface1`, 14.dp radius, leading orange search + trailing orange mic). The Instamart destination re-themes the accent to `InstamartPurple` and Dineout to `DineoutRed` while reusing the same card/list composables.

## 5. Motion

Swiggy motion is fast and functional — tied to the order flow, 150–320ms, never decorative.

| Moment | Compose recipe |
|--------|----------------|
| ADD → stepper morph | `Crossfade(targetState = qty == 0, animationSpec = tween(180))` between ADD and the `[ − n + ]` row; soft haptic on tap |
| Cart bar reveal | `AnimatedVisibility` `slideInVertically(spring(dampingRatio = 0.8f)) { it }`; price `animateIntAsState` roll |
| Card → restaurant detail | shared-element `SharedTransitionLayout` hero zoom `tween(320)` (or Nav3 shared bounds) |
| Filter chip select | chip `backgroundColor` `animateColorAsState(tween(150))`; list re-sort `animateItemPlacement(tween(200))` |
| Pull-to-refresh | `PullToRefreshContainer`, orange indicator, soft haptic on release |
| Bottom sheet (offers/customise) | `ModalBottomSheet`, 20.dp top corners, `tween(300)` |
| Live tracking | delivery `mappin` `animate*AsState` along route; step ticks `scaleIn` pop |

```kotlin
// ADD → stepper — the canonical Swiggy micro-interaction
Crossfade(targetState = qty == 0, animationSpec = tween(180), label = "addMorph") { empty ->
    if (empty) AddButton(onAdd = { qty = 1 }) else QtyStepper(qty, { qty-- }, { qty++ })
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on ADD tap and chip toggle; for the "Place Order" success use a stronger confirm pattern via `Vibrator`/`VibrationEffect.EFFECT_HEAVY_CLICK`. Pull-to-refresh ticks with `HapticFeedbackConstants.CLOCK_TICK`.

## 6. Icons

Swiggy uses custom brand glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. Bundle the wordmark as a vector drawable.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Swiggy / Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Instamart (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Dineout (tab) | `paperplane.fill` | `Icons.Filled.Send` |
| Cart (tab) | `cart.fill` | `Icons.Filled.ShoppingCart` |
| Location pin | `mappin.circle.fill` | `Icons.Filled.LocationOn` |
| Location chevron | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Voice search | `mic.fill` | `Icons.Filled.Mic` |
| Rating star | `star.fill` | `Icons.Filled.Star` |
| Favourite | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Favorite` |
| Filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Footer delivery time | `clock` | `Icons.Filled.AccessTime` |
| Free delivery | `bicycle` | `Icons.Filled.DirectionsBike` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Non-veg triangle | `triangle.fill` | `Icons.Filled.ChangeHistory` |
| Cart chevron | `chevron.right` | `Icons.Filled.ChevronRight` |
| Add / qty | `plus` / `minus` | `Icons.Filled.Add` / `Icons.Filled.Remove` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `ModalBottomSheet` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the dark canvas wants light-content system bars. The restaurant detail hero bleeds under the status bar with the scrim; the floating cart bar must sit above the `NavigationBar` and the gesture inset — pad with `WindowInsets.navigationBars`.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, section headers, body, dish descriptions. Pin layout-critical text (rating chips, offer badges, 10sp tab labels, the qty stepper, veg marks) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Tabular numerals**: prices/ETAs/ratings should not reflow as quantities change — use a monospaced-digit font feature or fixed-width number slots.
- **TalkBack**: label restaurant cards "{name}, rated {score}, {eta}, {cuisines}"; the rating chip "Rated {score} out of 5"; the ADD button "Add {dish} to cart" and the stepper "Quantity {n}, decrease/increase"; the diet mark "Vegetarian"/"Non-vegetarian".
- **Touch targets**: Material guidance is 48.dp. The ~32.dp ADD button / stepper and the 22.dp tab icons need a 48.dp hit area via padding; restaurant cards are full-card clickable.
- **Contrast**: white on `#FC8019` and on `#48C479` passes WCAG AA at button sizes; `#F2F2F2` on `#121212` passes AA for body. Validate any custom amber pairing.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the shared-element hero zoom and cart-bar spring with a `Crossfade`; keep ADD→stepper as an instant swap.
- **Dark mode**: invert via the `Dark*` palette — `#121212`, NOT true black; text becomes `#F2F2F2`. Food imagery stays full-saturation; bottom sheets get a 1.dp `DarkDivider` top border as the elevation cue. Do **not** enable Material You `dynamicColorScheme()` — Swiggy Orange must hold regardless of wallpaper. Re-theme only per vertical (Instamart `#982C61`, Dineout `#D7385E`).
