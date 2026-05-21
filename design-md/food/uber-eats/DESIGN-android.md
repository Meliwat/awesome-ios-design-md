# Uber Eats (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Uber Eats' visual language to **Android with Jetpack Compose (Material 3)**: color token objects (light + dark), a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Uber Eats' bright photo-first white canvas, single green accent, sticky cart bar, live map tracking) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, Google Maps Compose for tracking, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for images, and `maps-compose` `4.3+` for the tracking map.

## 1. Color Tokens

```kotlin
// ui/theme/UEColors.kt
import androidx.compose.ui.graphics.Color

object UEColors {
    // Light — default
    val Canvas    = Color(0xFFFFFFFF)
    val Surface   = Color(0xFFF3F3F3)
    val Surface2  = Color(0xFFEEEEEE)
    val Divider   = Color(0xFFE8E8E8)
    val TextPrimary   = Color(0xFF000000)
    val TextSecondary = Color(0xFF6B6B6B)
    val TextTertiary  = Color(0xFFA6A6A6)

    // Dark
    val DarkCanvas    = Color(0xFF000000)
    val DarkSurface   = Color(0xFF1C1C1E)
    val DarkSurface2  = Color(0xFF2C2C2E)
    val DarkDivider   = Color(0xFF2C2C2E)
    val DarkTextPrimary   = Color(0xFFFFFFFF)
    val DarkTextSecondary = Color(0xFFA6A6A6)

    // Brand — identical in light & dark
    val Green        = Color(0xFF06C167)
    val GreenPressed = Color(0xFF05A658)
    val GreenTint    = Color(0xFFE7F8EF)
    val ErrorRed     = Color(0xFFE11900)
    val BusyAmber    = Color(0xFFFF8A00)
}
```

Provide both Material 3 schemes; the green is the `primary` in **both** (only neutrals invert).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*

private val UELight = lightColorScheme(
    primary       = UEColors.Green,
    onPrimary     = Color.White,
    background     = UEColors.Canvas,
    onBackground   = UEColors.TextPrimary,
    surface        = UEColors.Surface,
    onSurface      = UEColors.TextPrimary,
    surfaceVariant = UEColors.Surface2,
    outline        = UEColors.Divider,
    error          = UEColors.ErrorRed,
)

private val UEDark = darkColorScheme(
    primary       = UEColors.Green,            // unchanged in dark
    onPrimary     = Color.White,
    background     = UEColors.DarkCanvas,
    onBackground   = UEColors.DarkTextPrimary,
    surface        = UEColors.DarkSurface,
    onSurface      = UEColors.DarkTextPrimary,
    surfaceVariant = UEColors.DarkSurface2,
    outline        = UEColors.DarkDivider,
    error          = UEColors.ErrorRed,
)

@Composable
fun UETheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) UEDark else UELight,
        typography = UETypography,
        content = content,
    )
```

## 2. Typography

Uber Move is a licensed brand typeface. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto).

```kotlin
// ui/theme/UEType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val UberMove = FontFamily(
    Font(R.font.uber_move_regular, FontWeight.Normal), // 400
    Font(R.font.uber_move_medium,  FontWeight.Medium), // 500
    Font(R.font.uber_move_bold,    FontWeight.Bold),   // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object UEText {
    val RestaurantHeader = TextStyle(UberMove, fontWeight = FontWeight.Bold,   fontSize = 30.sp, lineHeight = 33.sp, letterSpacing = (-0.4).sp)
    val TitleLarge       = TextStyle(UberMove, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val SectionHeader    = TextStyle(UberMove, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Subsection       = TextStyle(UberMove, fontWeight = FontWeight.Bold,   fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.3).sp)
    val RestaurantName   = TextStyle(UberMove, fontWeight = FontWeight.Bold,   fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val MenuItemTitle    = TextStyle(UberMove, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val Body             = TextStyle(UberMove, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 22.sp)
    val Price            = TextStyle(UberMove, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 20.sp)
    val Meta             = TextStyle(UberMove, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 18.sp)
    val Pill             = TextStyle(UberMove, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 18.sp)
    val Caption          = TextStyle(UberMove, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val LabelUpper       = TextStyle(UberMove, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Button           = TextStyle(UberMove, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = 0.2.sp)
    val Tab              = TextStyle(UberMove, fontWeight = FontWeight.Medium, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val CartBadge        = TextStyle(UberMove, fontWeight = FontWeight.Bold,   fontSize = 12.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val UETypography = Typography(
    headlineLarge = UEText.TitleLarge,
    headlineSmall = UEText.SectionHeader,
    titleMedium   = UEText.RestaurantName,
    bodyMedium    = UEText.Body,
    labelSmall    = UEText.Tab,
)
```

## 3. Signature Components

### Primary Green Button

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.*
import androidx.compose.ui.unit.dp

@Composable
fun UEPrimaryButton(
    title: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    trailing: String? = null,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        if (pressed) 0.98f else 1f,
        spring(dampingRatio = 0.7f, stiffness = 600f), label = "ctaScale",
    )
    val haptics = LocalHapticFeedback.current

    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(52.dp)
            .scale(scale)
            .clip(RoundedCornerShape(12.dp))
            .background(if (pressed) UEColors.GreenPressed else UEColors.Green)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS light impact
                onClick()
            }
            .padding(horizontal = if (trailing != null) 20.dp else 0.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = if (trailing != null) Arrangement.SpaceBetween
                                 else Arrangement.Center,
    ) {
        Text(title, style = UEText.Button, color = Color.White)
        if (trailing != null) Text(trailing, style = UEText.Button, color = Color.White)
    }
}
```

### Photo-First Restaurant Card

```kotlin
@Composable
fun UERestaurantCard(
    name: String,
    rating: String,
    eta: String,
    fee: String,
    freeDelivery: Boolean,
    photoUrl: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val scheme = MaterialTheme.colorScheme
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "cardScale")

    Column(modifier.clickable(interaction, indication = null, onClick = onClick)) {
        Box(
            Modifier
                .fillMaxWidth()
                .aspectRatio(16f / 9f)
                .scale(scale)
                .clip(RoundedCornerShape(12.dp)),
        ) {
            AsyncImage(model = photoUrl, contentDescription = name,
                modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
            if (freeDelivery) {
                Text(
                    "$0 Delivery Fee",
                    style = UEText.Caption.copy(fontWeight = FontWeight.Bold),
                    color = Color.White,
                    modifier = Modifier
                        .align(Alignment.TopStart).padding(10.dp)
                        .clip(RoundedCornerShape(50)).background(UEColors.Green)
                        .padding(horizontal = 8.dp, vertical = 4.dp),
                )
            }
            Box(
                Modifier
                    .align(Alignment.TopEnd).padding(10.dp)
                    .size(36.dp).clip(CircleShape).background(Color.White.copy(alpha = 0.9f)),
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Outlined.FavoriteBorder, contentDescription = "Favorite",
                      tint = Color.Black, modifier = Modifier.size(18.dp)) }
        }
        Spacer(Modifier.height(8.dp))
        Text(name, style = UEText.RestaurantName, color = scheme.onBackground)
        Row(verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
            modifier = Modifier.padding(top = 4.dp)) {
            Icon(Icons.Filled.Star, contentDescription = null,
                tint = scheme.onBackground, modifier = Modifier.size(12.dp)) // monochrome, not yellow
            Text(rating, style = UEText.Meta, color = scheme.onBackground)
            Text("· $eta ·", style = UEText.Meta, color = UEColors.TextSecondary)
            Text(fee, style = UEText.Meta, color = if (freeDelivery) UEColors.Green else UEColors.TextSecondary)
        }
    }
}
```

### Sticky Cart Bar

```kotlin
@Composable
fun UEStickyCartBar(
    count: Int,
    total: String,
    onView: () -> Unit,
    modifier: Modifier = Modifier,
) {
    AnimatedVisibility(
        visible = count > 0,
        enter = slideInVertically { it } + fadeIn(),
        exit = slideOutVertically { it } + fadeOut(),
        modifier = modifier,
    ) {
        Row(
            Modifier
                .fillMaxWidth()
                .padding(16.dp)
                .shadow(12.dp, RoundedCornerShape(12.dp), spotColor = Color.Black.copy(alpha = 0.12f))
                .clip(RoundedCornerShape(12.dp))
                .background(UEColors.Green)
                .clickable(onClick = onView)
                .height(56.dp)
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Box(Modifier.size(28.dp).clip(CircleShape).background(Color.White),
                contentAlignment = Alignment.Center) {
                Text("$count", style = UEText.CartBadge, color = UEColors.Green)
            }
            Text("View cart", style = UEText.Button, color = Color.White,
                modifier = Modifier.weight(1f), textAlign = TextAlign.Center)
            Text(total, style = UEText.Button, color = Color.White)
        }
    }
}
```

### Menu Item Row + Cart-Count Bump

```kotlin
@Composable
fun UEMenuItemRow(
    name: String, desc: String, price: String, photoUrl: String,
    onAdd: () -> Unit, modifier: Modifier = Modifier,
) {
    val scheme = MaterialTheme.colorScheme
    val haptics = LocalHapticFeedback.current
    Row(
        modifier
            .fillMaxWidth()
            .padding(vertical = 12.dp)
            .drawBehind {
                drawLine(UEColors.Divider, Offset(0f, size.height), Offset(size.width, size.height), 1.dp.toPx())
            },
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Column(Modifier.weight(1f)) {
            Text(name, style = UEText.MenuItemTitle, color = scheme.onBackground)
            Text(desc, style = UEText.Meta, color = UEColors.TextSecondary, maxLines = 2,
                overflow = TextOverflow.Ellipsis, modifier = Modifier.padding(top = 4.dp))
            Text(price, style = UEText.Body, color = scheme.onBackground, modifier = Modifier.padding(top = 2.dp))
        }
        Box(Modifier.size(88.dp)) {
            AsyncImage(model = photoUrl, contentDescription = name,
                modifier = Modifier.size(88.dp).clip(RoundedCornerShape(12.dp)),
                contentScale = ContentScale.Crop)
            Box(
                Modifier
                    .align(Alignment.BottomEnd)
                    .offset(x = 6.dp, y = 6.dp)
                    .size(28.dp).clip(CircleShape).background(UEColors.Green)
                    .clickable {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                        onAdd()
                    },
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Filled.Add, contentDescription = "Add", tint = Color.White,
                      modifier = Modifier.size(16.dp)) }
        }
    }
}

@Composable
fun UECartCountBadge(count: Int) {
    val scale = remember { Animatable(1f) }
    LaunchedEffect(count) {
        scale.animateTo(1.25f, spring(dampingRatio = 0.5f)); scale.animateTo(1f, spring())
    }
    Box(
        Modifier.scale(scale.value).defaultMinSize(18.dp, 18.dp)
            .clip(CircleShape).background(UEColors.Green).padding(horizontal = 4.dp),
        contentAlignment = Alignment.Center,
    ) { Text("$count", style = UEText.CartBadge, color = Color.White) }
}
```

## 4. Live Map Order Tracking

```kotlin
import com.google.android.gms.maps.model.LatLng
import com.google.maps.android.compose.*

@Composable
fun UEOrderTracking(route: List<LatLng>, etaText: String, step: Int) {
    val scheme = MaterialTheme.colorScheme
    var courier by remember { mutableStateOf(route.first()) }
    LaunchedEffect(route) {
        route.forEach { p ->
            courier = p          // Marker eases via MarkerState; or animate a custom LatLng tween
            delay(3000)
        }
    }

    Box(Modifier.fillMaxSize()) {
        GoogleMap(
            modifier = Modifier.fillMaxSize(),
            cameraPositionState = rememberCameraPositionState {
                position = com.google.maps.android.compose.CameraPositionState().position
            },
        ) {
            Polyline(points = route, color = Color.Black, width = 12f) // near-black route
            Marker(state = MarkerState(position = courier))            // green pin via custom icon in production
        }

        Column(
            Modifier
                .align(Alignment.BottomCenter)
                .fillMaxWidth()
                .shadow(28.dp, RoundedCornerShape(topStart = 16.dp, topEnd = 16.dp),
                        spotColor = Color.Black.copy(alpha = 0.16f))
                .clip(RoundedCornerShape(topStart = 16.dp, topEnd = 16.dp))
                .background(scheme.background)
                .padding(20.dp),
            verticalArrangement = Arrangement.spacedBy(16.dp),
        ) {
            Box(Modifier.align(Alignment.CenterHorizontally)
                .width(40.dp).height(5.dp).clip(RoundedCornerShape(3.dp)).background(UEColors.Divider))
            Text(etaText, style = UEText.SectionHeader, color = scheme.onBackground)
            Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                repeat(4) { i ->
                    Box(
                        Modifier.weight(1f).height(4.dp).clip(RoundedCornerShape(2.dp))
                            .background(if (i <= step) UEColors.Green else UEColors.Surface2)
                    )
                }
            }
        }
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. **Active tint is near-black/white, NOT green** — green is reserved for action surfaces (cart bar, CTAs, badges).

```kotlin
@Composable
fun UEBottomBar(selected: Int, cartCount: Int, onSelect: (Int) -> Unit) {
    val scheme = MaterialTheme.colorScheme
    NavigationBar(containerColor = scheme.background, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"    to Icons.Filled.Home,
            "Browse"  to Icons.Filled.GridView,
            "Search"  to Icons.Filled.Search,
            "Cart"    to Icons.Filled.ShoppingCart,
            "Account" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (i == 3 && cartCount > 0) {
                        BadgedBox(badge = {
                            Badge(containerColor = UEColors.Green, contentColor = Color.White) {
                                Text("$cartCount")
                            }
                        }) { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) }
                    } else {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp))
                    }
                },
                label = { Text(label, style = UEText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = scheme.onBackground,   // near-black/white, NOT green
                    selectedTextColor   = scheme.onBackground,
                    unselectedIconColor = UEColors.TextTertiary,
                    unselectedTextColor = UEColors.TextTertiary,
                    indicatorColor      = Color.Transparent,
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Cart-count bump | `Animatable` 1 → 1.25 → 1 (`spring(dampingRatio = 0.5f)`) keyed on count |
| Sticky cart bar appear | `AnimatedVisibility` + `slideInVertically { it }` + `fadeIn()` |
| Map courier marker | `MarkerState` position update; for smooth easing tween a `LatLng` via `animateValueAsState` (~1s) and feed it to the marker |
| Card press | `animateFloatAsState` 1 → 0.98, `tween(150)` ease-out |
| Pill select | `animateColorAsState` on background (`tween(150)`); scale 0.97 on press |

```kotlin
// Smoothly tween the courier between two LatLngs
@Composable
fun rememberEasedLatLng(target: LatLng): State<LatLng> {
    val lat by animateFloatAsState(target.latitude.toFloat(),
        tween(1000, easing = FastOutSlowInEasing), label = "lat")
    val lng by animateFloatAsState(target.longitude.toFloat(),
        tween(1000, easing = FastOutSlowInEasing), label = "lng")
    return remember { derivedStateOf { LatLng(lat.toDouble(), lng.toDouble()) } }
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(8, ...)` to approximate iOS's `.light` impact.

## 7. Icons

Uber Eats ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Uber Eats' glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Add | `plus` | `Icons.Filled.Add` |
| Quantity − / + | `minus` / `plus` | `Icons.Filled.Remove` / `Icons.Filled.Add` |
| Rating star | `star.fill` | `Icons.Filled.Star` (monochrome) |
| Favorite | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Address chevron | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Courier message | `message.fill` | `Icons.Filled.Chat` |
| Courier call | `phone.fill` | `Icons.Filled.Call` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Browse (tab) | `square.grid.2x2.fill` | `Icons.Filled.GridView` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Cart (tab) | `cart.fill` | `Icons.Filled.ShoppingCart` |
| Account (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; Maps Compose + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. Requires a Google Maps API key for the tracking screen.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the white canvas wants dark-content status bar in light mode (and light-content in dark). Apply `Scaffold` insets so the sticky cart bar and map sheet clear gesture nav.
- **Light-first**: default to the light scheme; `isSystemInDarkTheme()` swaps neutrals but `UEColors.Green` is the `primary` in **both** schemes.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on restaurant names, menu items, descriptions, ETA. Pin layout-sensitive text (tab labels, cart badge, map ETA chip) via `dp`-derived sizes.
- **TalkBack**: announce cards ("Sunrise Diner, 4.8 stars, 25 minute delivery, 99 cent delivery fee"); the cart bar as a single button ("View cart, 3 items, $28.40"); wrap the map ETA in `Modifier.semantics { liveRegion = LiveRegionMode.Polite }`.
- **Touch targets**: Material guidance is 48.dp minimum. The 52–56.dp CTA/cart bar clear it; pad the 28.dp `+` add button to a 48.dp hit area.
- **Contrast**: `#6B6B6B` on `#FFFFFF` passes WCAG AA at 14sp+. White on `#06C167` passes AA at 16sp bold (the CTA size) — validate any smaller green text. In dark mode keep `#06C167` and re-verify green-on-`#1C1C1E` for green text/icons.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Uber Eats' brand requires the fixed white (or near-black) canvas and the single `#06C167` green regardless of wallpaper.
