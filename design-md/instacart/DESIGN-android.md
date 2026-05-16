# Instacart (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Instacart's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Instacart's white grocery shelf, green-action accent, carrot-orange deals, the − n + stepper, the persistent cart bar) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `dp`/`sp` instead of `pt`, Compose haptics instead of `UIImpactFeedbackGenerator`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/InstacartColors.kt
import androidx.compose.ui.graphics.Color

object InstacartColors {
    // Canvas & Surfaces
    val Canvas  = Color(0xFFFFFFFF)
    val Surface = Color(0xFFF6F7F8)
    val Divider = Color(0xFFE8E9EB)

    // Text
    val TextPrimary   = Color(0xFF242529)
    val TextSecondary = Color(0xFF72757E)
    val TextTertiary  = Color(0xFFA6A8AE)

    // Brand
    val Green        = Color(0xFF0AAD0A)
    val GreenPressed = Color(0xFF098F09)
    val GreenTint    = Color(0xFFE6F6E6)

    // Deal / Semantic
    val Carrot     = Color(0xFFFF7009)
    val CarrotTint = Color(0xFFFFF0E6)
    val Error      = Color(0xFFD6203A)
}
```

Wire it into a Material 3 `lightColorScheme` so ripples, dividers, and default component colors inherit the brand. Instacart is effectively light-only; do not provide a dark scheme unless mirroring system dark with identical brand colors.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val InstacartScheme = lightColorScheme(
    primary        = InstacartColors.Green,
    onPrimary      = Color.White,
    background      = InstacartColors.Canvas,
    onBackground    = InstacartColors.TextPrimary,
    surface         = InstacartColors.Canvas,
    onSurface       = InstacartColors.TextPrimary,
    surfaceVariant  = InstacartColors.Surface,
    onSurfaceVariant = InstacartColors.TextSecondary,
    outline         = InstacartColors.Divider,
    tertiary        = InstacartColors.Carrot,
    error           = InstacartColors.Error,
)

@Composable
fun InstacartTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = InstacartScheme, typography = InstacartTypography, content = content)
```

## 2. Typography

Instacart uses Inter. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto).

```kotlin
// ui/theme/InstacartType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object InstacartText {
    val ScreenTitle  = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 25.sp, letterSpacing = (-0.3).sp)
    val StoreName    = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.2).sp)
    val ItemTitle    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp)
    val Price        = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 18.sp)
    val Body         = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Button       = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 20.sp)
    val StepperValue = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 20.sp)
    val Meta         = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Badge        = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.2.sp)
    val Tab          = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.1.sp)
    val Caption      = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val InstacartTypography = Typography(
    headlineLarge = InstacartText.ScreenTitle,
    headlineSmall = InstacartText.Section,
    titleMedium   = InstacartText.StoreName,
    bodyMedium    = InstacartText.Body,
    labelSmall    = InstacartText.Tab,
)
```

For prices and quantities, render with tabular figures via `TextStyle(fontFeatureSettings = "tnum")`.

## 3. Signature Components

### Quantity Stepper (− n +)

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun QuantityStepper(
    quantity: Int,
    onChange: (Int) -> Unit,
    modifier: Modifier = Modifier,
) {
    val haptics = LocalHapticFeedback.current
    fun step(delta: Int) {
        val next = (quantity + delta).coerceAtLeast(0)
        if (next != quantity) {
            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS light impact
            onChange(next)
        }
    }
    Row(
        modifier = modifier
            .height(32.dp)
            .clip(CircleShape)
            .background(InstacartColors.Green),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        GlyphButton("−") { step(-1) }
        Text(
            quantity.toString(),
            style = InstacartText.StepperValue.copy(fontFeatureSettings = "tnum"),
            color = Color.White,
            modifier = Modifier.widthIn(min = 24.dp),
        )
        GlyphButton("+") { step(1) }
    }
}

@Composable
private fun GlyphButton(glyph: String, onClick: () -> Unit) {
    Box(
        Modifier.size(36.dp, 32.dp).clickable(onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text(glyph, color = Color.White, fontSize = 18.sp, fontWeight = FontWeight.Bold)
    }
}
```

### Add Button → Stepper Morph

```kotlin
import androidx.compose.animation.*

@Composable
fun AddOrStepper(modifier: Modifier = Modifier) {
    var quantity by remember { mutableStateOf(0) }
    AnimatedContent(
        targetState = quantity == 0,
        transitionSpec = { (fadeIn() + scaleIn()) togetherWith (fadeOut() + scaleOut()) },
        label = "addMorph",
        modifier = modifier,
    ) { isEmpty ->
        if (isEmpty) {
            Box(
                Modifier
                    .height(32.dp)
                    .clip(CircleShape)
                    .background(InstacartColors.Canvas)
                    .border(1.5.dp, InstacartColors.Green, CircleShape)
                    .clickable { quantity = 1 }
                    .padding(horizontal = 20.dp),
                contentAlignment = Alignment.Center,
            ) {
                Text("Add", color = InstacartColors.Green, fontWeight = FontWeight.Bold, fontSize = 14.sp)
            }
        } else {
            QuantityStepper(quantity = quantity, onChange = { quantity = it })
        }
    }
}
```

### Persistent Green Cart Bar

```kotlin
@Composable
fun CartBar(
    itemCount: Int,
    subtotal: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    AnimatedVisibility(
        visible = itemCount > 0,
        enter = slideInVertically { it } + fadeIn(),
        exit = slideOutVertically { it } + fadeOut(),
        modifier = modifier,
    ) {
        val interaction = remember { MutableInteractionSource() }
        val pressed by interaction.collectIsPressedAsState()
        Row(
            Modifier
                .fillMaxWidth()
                .padding(horizontal = 16.dp)
                .height(56.dp)
                .clip(RoundedCornerShape(12.dp))
                .background(if (pressed) InstacartColors.GreenPressed else InstacartColors.Green)
                .clickable(interaction, indication = null, onClick = onClick)
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Box(
                Modifier.clip(CircleShape).background(Color.White)
                    .padding(horizontal = 12.dp, vertical = 6.dp),
            ) {
                Text("$itemCount items", color = InstacartColors.Green,
                    style = InstacartText.Meta.copy(fontWeight = FontWeight.Bold, fontFeatureSettings = "tnum"))
            }
            Spacer(Modifier.weight(1f))
            Text("View cart  $subtotal", style = InstacartText.Button.copy(fontFeatureSettings = "tnum"),
                color = Color.White)
        }
    }
}
```

### Store Card Row

```kotlin
@Composable
fun StoreRow(
    logoUrl: String,
    name: String,
    eta: String,
    deliveryFee: String,
    onClick: () -> Unit,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Row(
        Modifier
            .fillMaxWidth()
            .background(if (pressed) InstacartColors.Surface else InstacartColors.Canvas)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 16.dp, vertical = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        AsyncImage(
            model = logoUrl, contentDescription = null,
            modifier = Modifier.size(56.dp).clip(RoundedCornerShape(12.dp)),
            contentScale = ContentScale.Crop,
        )
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(name, style = InstacartText.StoreName, color = InstacartColors.TextPrimary)
            Text("$eta · $deliveryFee delivery", style = InstacartText.Meta, color = InstacartColors.TextSecondary)
        }
        Icon(Icons.Filled.ChevronRight, contentDescription = null,
            tint = InstacartColors.TextTertiary, modifier = Modifier.size(20.dp))
    }
}
```

### Product Card

```kotlin
@Composable
fun ProductCard(
    photoUrl: String,
    price: String,
    unitPrice: String,
    title: String,
    dealFlag: String? = null,
) {
    Column(Modifier.width(150.dp), verticalArrangement = Arrangement.spacedBy(6.dp)) {
        Box {
            AsyncImage(
                model = photoUrl, contentDescription = title,
                modifier = Modifier.size(150.dp).clip(RoundedCornerShape(12.dp))
                    .background(InstacartColors.Surface),
                contentScale = ContentScale.Crop,
            )
            if (dealFlag != null) {
                Box(
                    Modifier.align(Alignment.TopStart).padding(8.dp)
                        .clip(CircleShape).background(InstacartColors.Carrot)
                        .padding(horizontal = 8.dp, vertical = 4.dp),
                ) { Text(dealFlag, style = InstacartText.Badge, color = Color.White) }
            }
            AddOrStepper(Modifier.align(Alignment.BottomEnd).padding(8.dp))
        }
        Text(price, style = InstacartText.Price.copy(fontFeatureSettings = "tnum"),
            color = InstacartColors.TextPrimary)
        Text(unitPrice, style = InstacartText.Meta, color = InstacartColors.TextSecondary)
        Text(title, style = InstacartText.ItemTitle, color = InstacartColors.TextPrimary,
            maxLines = 2, overflow = TextOverflow.Ellipsis)
    }
}
```

### Replacement-Preference Radio Row

```kotlin
@Composable
fun ReplacementOption(
    label: String,
    selected: Boolean,
    onSelect: () -> Unit,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(12.dp))
            .border(1.5.dp, if (selected) InstacartColors.Green else InstacartColors.Divider,
                RoundedCornerShape(12.dp))
            .clickable(onClick = onSelect)
            .padding(16.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Text(label, style = InstacartText.Body, color = InstacartColors.TextPrimary,
            modifier = Modifier.weight(1f))
        Box(
            Modifier.size(22.dp).clip(CircleShape)
                .border(1.5.dp, if (selected) InstacartColors.Green else InstacartColors.Divider, CircleShape),
            contentAlignment = Alignment.Center,
        ) {
            AnimatedVisibility(selected, enter = scaleIn(), exit = scaleOut()) {
                Box(Modifier.size(12.dp).clip(CircleShape).background(InstacartColors.Green))
            }
        }
    }
}
```

## 4. Add-to-Cart Fly-Up

Compose's analog to an iOS arced path animation: hoist a shared `Animatable` 0 → 1, drive it with `tween(450, easing = FastOutSlowInEasing)`, and `graphicsLayer` a ghost thumbnail along an interpolated arc (offset Y with a parabolic mid-bump). On completion, bump the cart-badge scale.

```kotlin
suspend fun flyToCart(progress: Animatable<Float, *>, badge: Animatable<Float, *>) {
    progress.snapTo(0f)
    progress.animateTo(1f, tween(450, easing = FastOutSlowInEasing))
    badge.animateTo(1.2f, spring(dampingRatio = 0.5f))
    badge.animateTo(1f, spring(dampingRatio = 0.6f))
}
```

Drive the ghost with `Modifier.graphicsLayer { translationX = lerp(startX, endX, p); translationY = lerp(startY, endY, p) - arc(p) }` where `arc(p) = 60f * sin(p * PI)`.

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Instacart's iOS tab bar is an opaque white surface with a hairline divider and a **green active tint**. The Cart tab carries a count badge.

```kotlin
@Composable
fun InstacartBottomBar(selected: Int, onSelect: (Int) -> Unit, cartCount: Int) {
    NavigationBar(
        containerColor = InstacartColors.Canvas,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            Triple("Stores",       Icons.Filled.Storefront,    0),
            Triple("Buy it again", Icons.Filled.Replay,         0),
            Triple("Lists",        Icons.Filled.List,           0),
            Triple("Account",      Icons.Filled.Person,         0),
            Triple("Cart",         Icons.Filled.ShoppingCart,   cartCount),
        )
        items.forEachIndexed { i, (label, icon, badge) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (badge > 0) {
                        BadgedBox(badge = { Badge(containerColor = InstacartColors.Green) { Text("$badge") } }) {
                            Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp))
                        }
                    } else {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp))
                    }
                },
                label = { Text(label, style = InstacartText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = InstacartColors.Green,
                    selectedTextColor = InstacartColors.Green,
                    unselectedIconColor = InstacartColors.TextSecondary,
                    unselectedTextColor = InstacartColors.TextSecondary,
                    indicatorColor = InstacartColors.GreenTint,
                ),
            )
        }
    }
}
```

The persistent **green cart bar** sits directly above this bar — render `CartBar` in the `Scaffold` `bottomBar` slot, stacked above `InstacartBottomBar`, padded `16.dp` from the sides.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Stepper increment / decrement | `HapticFeedbackType.TextHandleMove`; value swap is immediate |
| Add pill → stepper morph | `AnimatedContent` with `(fadeIn()+scaleIn()) togetherWith (fadeOut()+scaleOut())` |
| Cart bar entrance | `AnimatedVisibility` `slideInVertically { it } + fadeIn()`, spring stiffness medium |
| Card tap | `Modifier.scale` 1 → 0.97 driven by `collectIsPressedAsState()` + `animateFloatAsState` |
| Replacement select | inner dot `AnimatedVisibility` `scaleIn()/scaleOut()`; border color crossfade |
| Add-to-cart fly-up | `Animatable` 0→1 `tween(450, FastOutSlowInEasing)` arced; badge `spring` bounce 1→1.2→1 |

```kotlin
// Card press scale
val interaction = remember { MutableInteractionSource() }
val pressed by interaction.collectIsPressedAsState()
val scale by animateFloatAsState(if (pressed) 0.97f else 1f, spring(dampingRatio = 0.8f), label = "cardScale")
Modifier.scale(scale).clickable(interaction, indication = null) { /* open product */ }
```

Haptics: prefer `LocalHapticFeedback`. For a crisper grocery "tick" on stepper taps, `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` (API 30+) approximates iOS `.light` impact well.

## 7. Icons

Instacart ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Instacart's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Stepper minus / plus | `minus` / `plus` | `Icons.Filled.Remove` / `Icons.Filled.Add` |
| Stores (tab) | `storefront` | `Icons.Filled.Storefront` |
| Buy it again (tab) | `arrow.clockwise` | `Icons.Filled.Replay` |
| Lists (tab) | `list.bullet` | `Icons.Filled.List` |
| Account (tab) | `person` | `Icons.Filled.Person` |
| Cart (tab) | `cart` | `Icons.Filled.ShoppingCart` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Chevron | `chevron.right` | `Icons.Filled.ChevronRight` |
| Save / heart | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Deal / tag | `tag.fill` | `Icons.Filled.LocalOffer` |
| Replacement / swap | `arrow.triangle.2.circlepath` | `Icons.Filled.SwapHoriz` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + badges are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the white canvas wants dark system-bar icons via `WindowCompat`. Apply `Scaffold` insets so the cart bar clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on product titles, store names, body. Pin layout-sensitive text (stepper value, tab labels, deal badges) by deriving size from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: give the stepper `Modifier.semantics { stateDescription = "Quantity $quantity"; }` and expose increment/decrement as custom actions; merge product-card text with `Modifier.semantics(mergeDescendants = true)`; the cart bar announces "View cart, $itemCount items, $subtotal".
- **Touch targets**: Material guidance is 48.dp minimum. The 32.dp stepper pill must give each glyph a 48.dp hit area via padding even though the pill itself is compact.
- **Contrast**: `#72757E` on `#FFFFFF` passes WCAG AA at 13sp+. Carrot `#FF7009` text on white is decorative — keep sale prices bold and add a "Sale" text label so color is not the only signal.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Instacart's brand requires the fixed white canvas, green action color, and carrot-orange deal color regardless of wallpaper.
