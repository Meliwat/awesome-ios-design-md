# Walmart (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Walmart's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Walmart's bright white canvas, single-blue action color, Spark Yellow savings chrome, Rollback tag) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `sp`/`dp` instead of `pt`, Material 3 ripple instead of iOS press scale where appropriate.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/WalmartColors.kt
import androidx.compose.ui.graphics.Color

object WalmartColors {
    // Canvas & Surfaces
    val Canvas      = Color(0xFFFFFFFF)
    val SurfaceTint = Color(0xFFF2F8FD)
    val SurfaceGray = Color(0xFFF5F5F5)
    val Divider     = Color(0xFFE2E8F0)
    val Border      = Color(0xFFC9D2DE)

    // Text
    val TextPrimary   = Color(0xFF2E2F32)
    val TextSecondary = Color(0xFF74767C)
    val TextTertiary  = Color(0xFF9BA0A8)

    // Brand
    val Blue         = Color(0xFF0071DC)
    val BluePressed  = Color(0xFF004F9A)
    val BlueTint     = Color(0xFFE6F1FC)
    val Spark        = Color(0xFFFFC220) // logo + savings chrome only
    val SparkPressed = Color(0xFFE5A800)

    // Semantic
    val Success  = Color(0xFF1A7F37)
    val Savings  = Color(0xFF2A8703)
    val Warning  = Color(0xFFB25E00)
    val Error    = Color(0xFFD03A2D)
    val StarGold = Color(0xFFFFB81C)
}
```

Wire it into a Material 3 `lightColorScheme` so ripples, dividers, and default component colors inherit the brand. Walmart is light-first; provide a dark scheme only if you target system dark mode.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val WalmartScheme = lightColorScheme(
    primary          = WalmartColors.Blue,
    onPrimary        = Color.White,
    primaryContainer = WalmartColors.BlueTint,
    background        = WalmartColors.Canvas,
    onBackground      = WalmartColors.TextPrimary,
    surface           = WalmartColors.Canvas,
    onSurface         = WalmartColors.TextPrimary,
    surfaceVariant    = WalmartColors.SurfaceTint,
    outline           = WalmartColors.Border,
    outlineVariant    = WalmartColors.Divider,
    error             = WalmartColors.Error,
)

@Composable
fun WalmartTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = WalmartScheme, typography = WalmartTypography, content = content)
```

## 2. Typography

Bogle is proprietary. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto); Inter is the closest substitute if you bundle it.

```kotlin
// ui/theme/WalmartType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Bogle = FontFamily(
    Font(R.font.bogle_regular,  FontWeight.Normal),   // 400
    Font(R.font.bogle_medium,   FontWeight.Medium),   // 500
    Font(R.font.bogle_semibold, FontWeight.SemiBold), // 600
    Font(R.font.bogle_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object WalmartText {
    val TitleLarge   = TextStyle(Bogle, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.3).sp)
    val Section      = TextStyle(Bogle, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val PriceLarge   = TextStyle(Bogle, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Subsection   = TextStyle(Bogle, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val ProductTitle = TextStyle(Bogle, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 21.sp)
    val Body         = TextStyle(Bogle, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Button       = TextStyle(Bogle, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp)
    val PriceInline  = TextStyle(Bogle, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 18.sp)
    val Struck       = TextStyle(Bogle, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 16.sp)
    val Meta         = TextStyle(Bogle, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Tag          = TextStyle(Bogle, fontWeight = FontWeight.Bold,     fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.2.sp)
    val Tab          = TextStyle(Bogle, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.1.sp)
    val Caption      = TextStyle(Bogle, fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val WalmartTypography = Typography(
    headlineLarge = WalmartText.TitleLarge,
    headlineSmall = WalmartText.Section,
    titleMedium   = WalmartText.ProductTitle,
    bodyMedium    = WalmartText.Body,
    labelSmall    = WalmartText.Tab,
)
```

For tabular price digits, apply a `TextStyle` with `fontFeatureSettings = "tnum"`:

```kotlin
val PriceTabular = WalmartText.PriceLarge.copy(fontFeatureSettings = "tnum")
```

## 3. Signature Components

### Primary CTA — Add to Cart

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.draw.scale

enum class CtaVariant { Primary, Secondary, Deal }

@Composable
fun WalmartCtaButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    variant: CtaVariant = CtaVariant.Primary,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "ctaScale")
    val haptics = LocalHapticFeedback.current

    val bg = when {
        variant == CtaVariant.Primary && pressed -> WalmartColors.BluePressed
        variant == CtaVariant.Primary            -> WalmartColors.Blue
        variant == CtaVariant.Deal               -> WalmartColors.Spark
        else                                     -> Color.Transparent
    }
    val fg = when (variant) {
        CtaVariant.Primary   -> Color.White
        CtaVariant.Deal      -> WalmartColors.TextPrimary
        CtaVariant.Secondary -> WalmartColors.Blue
    }

    Box(
        modifier = modifier
            .scale(scale)
            .heightIn(min = 48.dp)
            .clip(RoundedCornerShape(24.dp))
            .background(bg)
            .then(if (variant == CtaVariant.Secondary) Modifier.border(1.5.dp, WalmartColors.Blue, RoundedCornerShape(24.dp)) else Modifier)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS light impact
                onClick()
            }
            .padding(horizontal = 24.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = WalmartText.Button, color = fg)
    }
}
```

### Rollback Price Tag (signature)

```kotlin
enum class TagKind { Rollback, Clearance, BestSeller }

@Composable
fun RollbackTag(kind: TagKind = TagKind.Rollback, modifier: Modifier = Modifier) {
    val (bg, fg, label) = when (kind) {
        TagKind.Rollback   -> Triple(WalmartColors.Spark,    WalmartColors.TextPrimary, "ROLLBACK")
        TagKind.Clearance  -> Triple(WalmartColors.Error,    Color.White,               "CLEARANCE")
        TagKind.BestSeller -> Triple(WalmartColors.BlueTint, WalmartColors.Blue,        "BEST SELLER")
    }
    Box(
        modifier
            .clip(RoundedCornerShape(4.dp))
            .background(bg)
            .padding(horizontal = 8.dp, vertical = 3.dp),
    ) {
        Text(label, style = WalmartText.Tag, color = fg)
    }
}
```

### Price Block

```kotlin
@Composable
fun PriceBlock(
    price: String,
    wasPrice: String? = null,
    savings: String? = null,
    rollback: Boolean = false,
    modifier: Modifier = Modifier,
) {
    Column(modifier, verticalArrangement = Arrangement.spacedBy(4.dp)) {
        if (rollback) RollbackTag(TagKind.Rollback)
        Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            Text(price, style = WalmartText.PriceLarge.copy(fontFeatureSettings = "tnum"), color = WalmartColors.TextPrimary)
            if (wasPrice != null) {
                Text(
                    wasPrice,
                    style = WalmartText.Struck.copy(textDecoration = TextDecoration.LineThrough),
                    color = WalmartColors.TextSecondary,
                )
            }
        }
        if (savings != null) {
            Text(savings, style = WalmartText.Meta.copy(fontWeight = FontWeight.SemiBold), color = WalmartColors.Savings)
        }
    }
}
```

### Product Card

```kotlin
@Composable
fun ProductCard(
    title: String,
    price: String,
    wasPrice: String? = null,
    rollback: Boolean = false,
    rating: Int,
    onAdd: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .clip(RoundedCornerShape(12.dp))
            .background(WalmartColors.Canvas)
            .border(1.dp, WalmartColors.Divider, RoundedCornerShape(12.dp))
            .padding(12.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Box(
            Modifier
                .fillMaxWidth()
                .aspectRatio(1f)
                .clip(RoundedCornerShape(8.dp))
                .background(WalmartColors.SurfaceGray),
        )
        PriceBlock(price = price, wasPrice = wasPrice, rollback = rollback)
        Text(title, style = WalmartText.ProductTitle, color = WalmartColors.TextPrimary, maxLines = 2, overflow = TextOverflow.Ellipsis)
        Row(horizontalArrangement = Arrangement.spacedBy(2.dp)) {
            repeat(5) { i ->
                Icon(
                    if (i < rating) Icons.Filled.Star else Icons.Outlined.StarBorder,
                    contentDescription = null,
                    tint = WalmartColors.StarGold,
                    modifier = Modifier.size(11.dp),
                )
            }
        }
        WalmartCtaButton("Add to cart", onAdd, Modifier.fillMaxWidth().height(40.dp))
    }
}
```

### Pickup / Delivery Toggle (signature)

```kotlin
import androidx.compose.animation.core.animateDpAsState

@Composable
fun FulfillmentToggle(
    isDelivery: Boolean,
    onSelect: (Boolean) -> Unit,
    modifier: Modifier = Modifier,
) {
    var width by remember { mutableStateOf(0.dp) }
    val density = LocalDensity.current
    val offset by animateDpAsState(if (isDelivery) width / 2 else 0.dp, tween(220), label = "thumb")

    Box(
        modifier
            .clip(RoundedCornerShape(20.dp))
            .background(WalmartColors.SurfaceTint)
            .onSizeChanged { width = with(density) { it.width.toDp() } }
            .padding(4.dp),
    ) {
        Box(
            Modifier
                .offset(x = offset)
                .fillMaxWidth(0.5f)
                .height(32.dp)
                .clip(RoundedCornerShape(16.dp))
                .background(Color.White),
        )
        Row(Modifier.fillMaxWidth()) {
            listOf("Pickup" to false, "Delivery" to true).forEach { (label, value) ->
                val selected = value == isDelivery
                Box(
                    Modifier.weight(1f).height(32.dp).clickable { onSelect(value) },
                    contentAlignment = Alignment.Center,
                ) {
                    Text(
                        label,
                        style = WalmartText.Button,
                        color = if (selected) WalmartColors.Blue else WalmartColors.TextSecondary,
                    )
                }
            }
        }
    }
}
```

### Quantity Stepper

```kotlin
@Composable
fun QuantityStepper(qty: Int, onChange: (Int) -> Unit, modifier: Modifier = Modifier) {
    Row(
        modifier
            .border(1.dp, WalmartColors.Border, RoundedCornerShape(18.dp))
            .height(36.dp)
            .padding(horizontal = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        Icon(
            if (qty <= 1) Icons.Outlined.Delete else Icons.Filled.Remove,
            contentDescription = if (qty <= 1) "Remove" else "Decrease",
            tint = WalmartColors.Blue,
            modifier = Modifier.size(16.dp).clickable { onChange(maxOf(0, qty - 1)) },
        )
        Text("$qty", style = WalmartText.Button.copy(fontFeatureSettings = "tnum"), color = WalmartColors.TextPrimary)
        Icon(
            Icons.Filled.Add,
            contentDescription = "Increase",
            tint = WalmartColors.Blue,
            modifier = Modifier.size(16.dp).clickable { onChange(qty + 1) },
        )
    }
}
```

## 4. Spark Logo Mark

```kotlin
@Composable
fun SparkMark(size: Dp = 28.dp) {
    Box(Modifier.size(size), contentAlignment = Alignment.Center) {
        repeat(6) { i ->
            Box(
                Modifier
                    .graphicsLayer {
                        rotationZ = i * 60f
                        translationY = with(this) { -size.toPx() * 0.30f }
                    }
                    .size(width = size * 0.16f, height = size * 0.42f)
                    .clip(RoundedCornerShape(50))
                    .background(WalmartColors.Spark),
            )
        }
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Walmart's iOS tab bar is opaque white with a hairline top border (no live blur — readability first). **Active tint is Walmart Blue.**

```kotlin
@Composable
fun WalmartBottomBar(selected: Int, cartCount: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = WalmartColors.Canvas,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Shop"     to Icons.Filled.Home,
            "Services" to Icons.Filled.Build,
            "Search"   to Icons.Filled.Search,
            "Cart"     to Icons.Filled.ShoppingCart,
            "Account"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (label == "Cart" && cartCount > 0) {
                        BadgedBox(badge = { Badge(containerColor = WalmartColors.Blue) { Text("$cartCount") } }) {
                            Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp))
                        }
                    } else {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp))
                    }
                },
                label = { Text(label, style = WalmartText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = WalmartColors.Blue,
                    selectedTextColor   = WalmartColors.Blue,
                    unselectedIconColor = WalmartColors.TextSecondary,
                    unselectedTextColor = WalmartColors.TextSecondary,
                    indicatorColor      = WalmartColors.BlueTint,
                ),
            )
        }
    }
}
```

Place a hairline `Divider(color = WalmartColors.Divider, thickness = 0.5.dp)` directly above the bar; on PDP, render a sticky white "Add to cart" bar in the `Scaffold` `bottomBar` slot stacked above it.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Add-to-cart bump | `Animatable` keyframes 1 → 0.96 → 1.04 → 1 over ~260ms; `HapticFeedbackType.LongPress`; pop the cart badge |
| Pickup/Delivery toggle | `animateDpAsState` thumb offset, `tween(220)` ease-in-out; label color crossfade |
| Card press | `animateFloatAsState` 1 → 0.98, `tween(150)`; border darkens to `Border` |
| Subtotal roll | `AnimatedContent` with `slideInVertically`/`slideOutVertically` on the subtotal Text, 200ms |
| Spark launch | stagger 6 rays' alpha/scale over 200ms with `animateFloatAsState` + per-ray delay |

```kotlin
// Add-to-cart bump
val bump = remember { Animatable(1f) }
LaunchedEffect(cartTrigger) {
    if (cartTrigger > 0) {
        bump.animateTo(0.96f, tween(100))
        bump.animateTo(1.04f, tween(80))
        bump.animateTo(1f, spring(dampingRatio = 0.6f))
    }
}
Modifier.scale(bump.value)
```

Haptics: prefer `LocalHapticFeedback`. For a softer iOS-like "bump" use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, …)`.

## 7. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Walmart glyphs as vector drawables and load via `ImageVector.vectorResource(...)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Shop (tab) | `house.fill` | `Icons.Filled.Home` |
| Services (tab) | `wrench.and.screwdriver.fill` | `Icons.Filled.Build` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Cart (tab) | `cart.fill` | `Icons.Filled.ShoppingCart` |
| Account (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| Barcode scan | `barcode.viewfinder` | `Icons.Filled.QrCodeScanner` |
| Heart / list | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Rating star | `star` / `star.fill` | `Icons.Outlined.StarBorder` / `Icons.Filled.Star` |
| Quantity minus | `minus` | `Icons.Filled.Remove` |
| Quantity remove | `trash` | `Icons.Outlined.Delete` |
| Quantity plus | `plus` | `Icons.Filled.Add` |
| Location chevron | `chevron.down` | `Icons.Filled.ExpandMore` |
| Fulfillment ETA | `clock` | `Icons.Filled.Schedule` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the bright canvas wants dark-content system bars (`WindowCompat`). Apply `Scaffold` insets so the sticky "Add to cart" bar clears gesture nav.
- **Font scaling**: `sp` honors the user's scale — keep it on titles, prices, body. Pin layout-sensitive text (Rollback tag, tab labels, stepper digit) with a fixed `Density` wrapper or derive from `dp`.
- **TalkBack**: set a product-specific `contentDescription` on Add to cart; merge card text with `Modifier.semantics(mergeDescendants = true)` and expose the CTA as a separate button; announce price + savings together.
- **Touch targets**: Material minimum is 48.dp. The 36.dp stepper buttons and 24.dp tab icons need `Modifier.size(48.dp)` hit padding even though the glyphs are smaller.
- **Contrast**: dark `#2E2F32` on Spark Yellow `#FFC220` passes WCAG AA — never white on yellow. `#74767C` on white passes AA at 14sp+; bump 11sp captions toward `#5E6068` for strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Walmart's brand requires the fixed `#0071DC` blue and `#FFFFFF` canvas regardless of wallpaper.
