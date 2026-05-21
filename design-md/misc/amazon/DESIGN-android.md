# Amazon (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Amazon's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Amazon's deep-navy chrome, yellow-with-black-text CTA, superscript-cents pricing, dense info-packed product tiles) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `RoundedCornerShape` with per-corner radii for the search bar's split, `sp`/`dp` instead of `pt`. Amazon is a *light-canvas* storefront, so we wire a Material 3 `lightColorScheme`, not a dark one.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for the remote product photography.

## 1. Color Tokens

```kotlin
// ui/theme/AmazonColors.kt
import androidx.compose.ui.graphics.Color

object AmazonColors {
    // Canvas & Surfaces
    val Canvas        = Color(0xFFFFFFFF)
    val SurfaceMuted  = Color(0xFFF3F3F3)
    val SurfaceTint   = Color(0xFFF7F8F8)
    val Divider       = Color(0xFFDDDDDD)
    val BorderDefault = Color(0xFFD5D9D9)

    // Text
    val TextPrimary   = Color(0xFF0F1111) // titles, prices, body
    val TextSecondary = Color(0xFF565959) // metadata, "shipped by", helper
    val TextTertiary  = Color(0xFF848A8C) // disabled, placeholder

    // Brand
    val Yellow          = Color(0xFFFF9900) // primary CTA — Add to Cart
    val YellowPressed   = Color(0xFFE68A00)
    val YellowHighlight = Color(0xFFFCD200) // 1dp top-edge 3D cue
    val BuyNowOrange    = Color(0xFFF08804) // Buy Now / Place your order
    val DeepNavy        = Color(0xFF131921) // top nav chrome
    val SecondaryNavy   = Color(0xFF232F3E) // sub-nav, account panel

    // Semantic
    val PriceRed     = Color(0xFFB12704) // deals, savings, struck-through
    val AlertRed     = Color(0xFFCC0C39) // errors, cart badge, out-of-stock
    val SuccessGreen = Color(0xFF007600) // "FREE Delivery", in stock
    val PrimeTeal    = Color(0xFF007185) // Prime UI, in-body links, review count
    val PrimeSky     = Color(0xFF00A8E1) // `prime` wordmark accent
    val RatingGold   = Color(0xFFFFA41C) // 5-star glyphs
    val StarEmpty    = Color(0xFFE3E6E6) // empty star track

    // Dark mode (light-first app; dark is a variant)
    val DarkCanvas   = Color(0xFF0F1111)
    val DarkSurface1 = Color(0xFF1A1F25)
    val DarkSurface2 = Color(0xFF232F3E)
    val DarkDivider  = Color(0xFF3A4553)
    val DarkText     = Color(0xFFF5F5F5)
    val DarkTextSec  = Color(0xFFAAB7B8)
}
```

Amazon is light-first, so wire a Material 3 `lightColorScheme` (provide the dark variant for dark mode). The yellow CTA carries the brand even in chrome, so `primary` stays Amazon Yellow with black `onPrimary`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val AmazonLight = lightColorScheme(
    primary        = AmazonColors.Yellow,
    onPrimary      = AmazonColors.TextPrimary,  // intentional: black on yellow, not white
    background     = AmazonColors.Canvas,
    onBackground   = AmazonColors.TextPrimary,
    surface        = AmazonColors.Canvas,
    onSurface      = AmazonColors.TextPrimary,
    surfaceVariant = AmazonColors.SurfaceMuted,
    outline        = AmazonColors.BorderDefault,
    error          = AmazonColors.AlertRed,
)

private val AmazonDark = darkColorScheme(
    primary        = AmazonColors.Yellow,       // unchanged on dark
    onPrimary      = AmazonColors.TextPrimary,
    background     = AmazonColors.DarkCanvas,
    onBackground   = AmazonColors.DarkText,
    surface        = AmazonColors.DarkSurface1,
    onSurface      = AmazonColors.DarkText,
    surfaceVariant = AmazonColors.DarkSurface2,
    outline        = AmazonColors.DarkDivider,
    error          = AmazonColors.AlertRed,
)

@Composable
fun AmazonTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (darkTheme) AmazonDark else AmazonLight,
    typography  = AmazonTypography,
    content     = content,
)
```

## 2. Typography

Amazon Ember (proprietary since 2015) is on every Amazon surface. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto) — a humanist sans tuned for small-size legibility, the closest free substitute.

```kotlin
// ui/theme/AmazonType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val AmazonEmber = FontFamily(
    Font(R.font.amazon_ember_light,   FontWeight.Light),  // 300
    Font(R.font.amazon_ember_regular, FontWeight.Normal), // 400
    Font(R.font.amazon_ember_medium,  FontWeight.Medium), // 500
    Font(R.font.amazon_ember_bold,    FontWeight.Bold),   // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object AmazonText {
    val NavHero       = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 25.sp, letterSpacing = (-0.2).sp)
    val Section       = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,   fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.1).sp)
    val ProductTitle  = TextStyle(AmazonEmber, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 21.sp)
    val PDPTitle      = TextStyle(AmazonEmber, fontWeight = FontWeight.Medium, fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.1).sp)
    val PriceHero     = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val PriceSuper    = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,   fontSize = 14.sp, lineHeight = 14.sp)
    val PriceCard     = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val PriceStruck   = TextStyle(AmazonEmber, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 17.sp)
    val Body          = TextStyle(AmazonEmber, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 22.sp)
    val RatingCount   = TextStyle(AmazonEmber, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val Delivery      = TextStyle(AmazonEmber, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 17.sp)
    val Meta          = TextStyle(AmazonEmber, fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 16.sp)
    val Button        = TextStyle(AmazonEmber, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 15.sp)
    val ButtonSmall   = TextStyle(AmazonEmber, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 14.sp)
    val PrimeBadge    = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.3.sp)
    val Tab           = TextStyle(AmazonEmber, fontWeight = FontWeight.Medium, fontSize = 10.sp, lineHeight = 10.sp)
    val SearchHint    = TextStyle(AmazonEmber, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 16.sp)
    val PromoBadge    = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.5.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val AmazonTypography = Typography(
    headlineMedium = AmazonText.NavHero,
    headlineSmall  = AmazonText.Section,
    titleMedium    = AmazonText.PDPTitle,
    bodyLarge      = AmazonText.Body,
    bodyMedium     = AmazonText.ProductTitle,
    labelLarge     = AmazonText.Button,
    labelSmall     = AmazonText.Tab,
)
```

## 3. Signature Components

### Top Nav with Search Bar (navy + split yellow action)

The SwiftUI `RoundedCorner` shape becomes `RoundedCornerShape(topStart, topEnd, bottomEnd, bottomStart)` — the search field rounds its left corners only, the yellow mic/scan block rounds its right corners only.

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
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
import androidx.compose.ui.unit.dp

@Composable
fun AmazonTopNav(onSearch: () -> Unit, onMicOrScan: () -> Unit) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .height(56.dp)
            .background(AmazonColors.DeepNavy)
            .padding(horizontal = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Icon(
            Icons.Filled.ShoppingCart, // stand-in for the smile logo (ship a vector drawable)
            contentDescription = "Amazon",
            tint = Color.White,
            modifier = Modifier.size(28.dp),
        )
        Row(Modifier.weight(1f).height(44.dp)) {
            Row(
                modifier = Modifier
                    .weight(1f)
                    .fillMaxHeight()
                    .clip(RoundedCornerShape(topStart = 8.dp, bottomStart = 8.dp))
                    .background(AmazonColors.Canvas)
                    .clickable(onClick = onSearch)
                    .padding(horizontal = 12.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(8.dp),
            ) {
                Icon(Icons.Filled.Search, contentDescription = null,
                    tint = AmazonColors.TextSecondary, modifier = Modifier.size(18.dp))
                Text("Search Amazon", style = AmazonText.SearchHint,
                    color = AmazonColors.TextSecondary)
            }
            Row(
                modifier = Modifier
                    .fillMaxHeight()
                    .clip(RoundedCornerShape(topEnd = 8.dp, bottomEnd = 8.dp))
                    .background(AmazonColors.Yellow)
                    .clickable(onClick = onMicOrScan)
                    .padding(horizontal = 14.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(10.dp),
            ) {
                Icon(Icons.Filled.Mic, contentDescription = "Voice search",
                    tint = AmazonColors.TextPrimary, modifier = Modifier.size(18.dp))
                Icon(Icons.Filled.QrCodeScanner, contentDescription = "Scan barcode",
                    tint = AmazonColors.TextPrimary, modifier = Modifier.size(20.dp))
            }
        }
    }
}
```

### Add to Cart Button (Yellow + 1dp top highlight)

The vestigial 3D cue (1dp `#FCD200` top edge) is a `drawBehind` line over the rounded fill.

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.runtime.*
import androidx.compose.ui.draw.drawBehind
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalDensity
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun AmazonAddToCartButton(onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        if (pressed) 0.98f else 1f,
        spring(dampingRatio = 0.8f, stiffness = 600f),
        label = "cartScale",
    )
    val haptics = LocalHapticFeedback.current
    val strokePx = with(LocalDensity.current) { 1.dp.toPx() }

    Box(
        modifier = modifier
            .fillMaxWidth()
            .heightIn(min = 44.dp)
            .scale(scale)
            .shadow(2.dp, RoundedCornerShape(8.dp), spotColor = Color.Black.copy(alpha = 0.1f))
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) AmazonColors.YellowPressed else AmazonColors.Yellow)
            .drawBehind {
                drawLine( // 1dp top highlight
                    color = AmazonColors.YellowHighlight,
                    start = Offset(0f, strokePx / 2),
                    end = Offset(size.width, strokePx / 2),
                    strokeWidth = strokePx,
                )
            }
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS success on cart inc
                onClick()
            }
            .padding(horizontal = 16.dp, vertical = 12.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text("Add to Cart", style = AmazonText.Button, color = AmazonColors.TextPrimary)
    }
}
```

### Buy Now Button (Orange)

```kotlin
@Composable
fun AmazonBuyNowButton(onClick: () -> Unit, modifier: Modifier = Modifier) {
    Box(
        modifier = modifier
            .fillMaxWidth()
            .heightIn(min = 48.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(AmazonColors.BuyNowOrange)
            .clickable(onClick = onClick)
            .padding(horizontal = 16.dp, vertical = 12.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text("Buy Now", style = AmazonText.Button, color = AmazonColors.TextPrimary)
    }
}
```

### Star Rating Row (gold stars + teal review-count link)

```kotlin
import androidx.compose.material.icons.filled.Star

@Composable
fun StarRatingRow(rating: Double, reviewCount: Int, modifier: Modifier = Modifier) {
    Row(modifier, verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(2.dp)) {
        repeat(5) { i ->
            Icon(
                Icons.Filled.Star,
                contentDescription = null,
                tint = if (i < rating) AmazonColors.RatingGold else AmazonColors.StarEmpty,
                modifier = Modifier.size(12.dp),
            )
        }
        Text(" (${"%,d".format(reviewCount)})", style = AmazonText.RatingCount,
            color = AmazonColors.PrimeTeal) // tappable teal-blue link
    }
}
```

### Product Card (Grid Tile — packs photo + title + rating + price + Prime + delivery)

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.text.font.FontWeight
import coil.compose.AsyncImage

@Composable
fun AmazonProductCard(
    title: String,
    rating: Double,
    reviewCount: Int,
    price: String,
    originalPrice: String?,
    isPrime: Boolean,
    deliveryLine: String,
    imageUrl: String,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier = modifier
            .background(AmazonColors.Canvas)
            .padding(8.dp),
        verticalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        AsyncImage(
            model = imageUrl,
            contentDescription = title,
            modifier = Modifier
                .fillMaxWidth()
                .aspectRatio(1f) // square product photo
                .clip(RoundedCornerShape(4.dp)),
            contentScale = ContentScale.Fit,
        )
        Text(
            title,
            style = AmazonText.ProductTitle,
            color = AmazonColors.TextPrimary,
            maxLines = 2, // signature 2-line clamp
            overflow = TextOverflow.Ellipsis,
        )
        StarRatingRow(rating, reviewCount)
        Row(verticalAlignment = Alignment.Bottom,
            horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(price, style = AmazonText.PriceCard, color = AmazonColors.TextPrimary)
            if (originalPrice != null) {
                Text(
                    originalPrice,
                    style = AmazonText.PriceStruck.copy(
                        textDecoration = androidx.compose.ui.text.style.TextDecoration.LineThrough),
                    color = AmazonColors.TextSecondary,
                )
            }
        }
        if (isPrime) {
            Row(horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Text("prime", style = AmazonText.PrimeBadge, color = AmazonColors.PrimeSky)
                Text(deliveryLine, style = AmazonText.Delivery, color = AmazonColors.SuccessGreen)
            }
        }
    }
}
```

### PDP Price Block (hero price with superscript cents)

iOS uses `baselineOffset`; in Compose the cleanest equivalent is an `AnnotatedString` with `BaselineShift` on the `$` and cents spans.

```kotlin
import androidx.compose.ui.text.AnnotatedString
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.style.BaselineShift
import androidx.compose.ui.text.withStyle

@Composable
fun AmazonPDPPriceBlock(
    dollars: Int,
    cents: Int,
    originalPrice: String?,
    savingsLine: String?,
    deliveryText: String,
) {
    val priceText = buildAnnotatedString {
        withStyle(SpanStyle(
            fontSize = AmazonText.PriceSuper.fontSize,
            fontWeight = FontWeight.Bold,
            baselineShift = BaselineShift(0.5f),
        )) { append("$") }
        withStyle(SpanStyle(
            fontSize = AmazonText.PriceHero.fontSize,
            fontWeight = FontWeight.Bold,
        )) { append("$dollars") }
        withStyle(SpanStyle(
            fontSize = AmazonText.PriceSuper.fontSize,
            fontWeight = FontWeight.Bold,
            baselineShift = BaselineShift(0.5f),
        )) { append("%02d".format(cents)) }
    }

    Column(verticalArrangement = Arrangement.spacedBy(6.dp)) {
        Text(
            priceText,
            color = AmazonColors.TextPrimary,
            // single VoiceOver utterance — see §8
            modifier = Modifier.semantics { contentDescription = "\$$dollars.${"%02d".format(cents)}" },
        )
        if (originalPrice != null) {
            Text(
                originalPrice,
                style = AmazonText.PriceStruck.copy(
                    textDecoration = androidx.compose.ui.text.style.TextDecoration.LineThrough),
                color = AmazonColors.TextSecondary,
            )
        }
        if (savingsLine != null) {
            Text(savingsLine, style = AmazonText.Button, color = AmazonColors.PriceRed)
        }
        Row(horizontalArrangement = Arrangement.spacedBy(4.dp)) {
            Text("prime", style = AmazonText.PrimeBadge, color = AmazonColors.PrimeSky)
            Text(deliveryText, style = AmazonText.Button, color = AmazonColors.SuccessGreen)
        }
    }
}
```

### Lightning Deal Countdown Banner

```kotlin
import androidx.compose.material.icons.filled.Bolt
import androidx.compose.ui.graphics.Brush

@Composable
fun AmazonLightningDealBanner(countdown: String) {
    Row(
        modifier = Modifier
            .clip(RoundedCornerShape(4.dp))
            .background(Brush.horizontalGradient(
                listOf(AmazonColors.PriceRed, AmazonColors.AlertRed)))
            .padding(horizontal = 12.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(Icons.Filled.Bolt, contentDescription = null,
            tint = Color.White, modifier = Modifier.size(18.dp))
        Text("Lightning Deal", style = AmazonText.Delivery.copy(fontWeight = FontWeight.Bold),
            color = Color.White)
        Spacer(Modifier.weight(1f))
        Text("Ends in $countdown", style = AmazonText.Delivery, color = Color.White)
    }
}
```

### Quantity Stepper

```kotlin
import androidx.compose.foundation.border
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Remove

@Composable
fun AmazonQuantityStepper(count: Int, onChange: (Int) -> Unit) {
    Row(
        modifier = Modifier
            .height(32.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(AmazonColors.SurfaceMuted)
            .border(1.dp, AmazonColors.BorderDefault, RoundedCornerShape(8.dp)),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Box(Modifier.size(44.dp, 32.dp).clickable { if (count > 1) onChange(count - 1) },
            contentAlignment = Alignment.Center) {
            Icon(Icons.Filled.Remove, "Decrease", tint = AmazonColors.TextPrimary,
                modifier = Modifier.size(16.dp))
        }
        Text("$count", style = AmazonText.Button, color = AmazonColors.TextPrimary,
            modifier = Modifier.width(36.dp), textAlign = androidx.compose.ui.text.style.TextAlign.Center)
        Box(Modifier.size(44.dp, 32.dp).clickable { onChange(count + 1) },
            contentAlignment = Alignment.Center) {
            Icon(Icons.Filled.Add, "Increase", tint = AmazonColors.TextPrimary,
                modifier = Modifier.size(16.dp))
        }
    }
}
```

## 4. Add-to-Cart Toast + Cart-Badge Bump

Amazon's most distinctive feedback loop is the bottom slide-up confirmation paired with the cart-badge count bump. iOS uses `.offset` + spring; Compose uses `AnimatedVisibility` with `slideInVertically` and a `LaunchedEffect` auto-dismiss.

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.slideInVertically
import androidx.compose.animation.slideOutVertically
import androidx.compose.animation.core.tween
import androidx.compose.material.icons.filled.CheckCircle
import kotlinx.coroutines.delay

@Composable
fun AddToCartToast(visible: Boolean, onDismiss: () -> Unit) {
    LaunchedEffect(visible) {
        if (visible) { delay(2500); onDismiss() } // auto-dismiss after 2.5s
    }
    AnimatedVisibility(
        visible = visible,
        enter = slideInVertically(tween(300)) { it },   // slide up from bottom
        exit = slideOutVertically(tween(200)) { it },
    ) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .shadow(12.dp, spotColor = Color.Black.copy(alpha = 0.15f))
                .background(AmazonColors.Canvas)
                .height(48.dp)
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Icon(Icons.Filled.CheckCircle, contentDescription = null,
                tint = AmazonColors.SuccessGreen, modifier = Modifier.size(20.dp))
            Text("Added to your Cart", style = AmazonText.Button,
                color = AmazonColors.TextPrimary)
        }
    }
}

// Cart-badge bump: 1 → 1.3 → 1 spring when the count changes
@Composable
fun CartBadge(count: Int) {
    val scale = remember { androidx.compose.animation.core.Animatable(1f) }
    LaunchedEffect(count) {
        scale.animateTo(1.3f, spring(dampingRatio = 0.55f, stiffness = 700f))
        scale.animateTo(1f, spring(dampingRatio = 0.55f, stiffness = 700f))
    }
    Box(
        Modifier
            .scale(scale.value)
            .clip(androidx.compose.foundation.shape.CircleShape)
            .background(AmazonColors.AlertRed)
            .padding(horizontal = 6.dp, vertical = 2.dp),
    ) {
        Text("$count", style = AmazonText.Meta.copy(fontWeight = FontWeight.Bold),
            color = Color.White)
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Amazon's bar is opaque white with a hairline top border (no blur, so no translucency needed). **Active tint is Amazon Yellow** with the filled icon variant; inactive is `#565959`. The Cart tab carries a red badge.

```kotlin
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults
import androidx.compose.material3.BadgedBox
import androidx.compose.material3.Badge

@Composable
fun AmazonBottomBar(selected: Int, cartCount: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = AmazonColors.Canvas,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"   to Icons.Filled.Home,
            "Menu"   to Icons.Filled.Menu,
            "Cart"   to Icons.Filled.ShoppingCart,
            "You"    to Icons.Filled.Person,
            "Search" to Icons.Filled.Search,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (label == "Cart" && cartCount > 0) {
                        BadgedBox(badge = {
                            Badge(containerColor = AmazonColors.AlertRed,
                                contentColor = Color.White) { Text("$cartCount") }
                        }) { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) }
                    } else {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp))
                    }
                },
                label = { Text(label, style = AmazonText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = AmazonColors.Yellow,
                    selectedTextColor = AmazonColors.Yellow,
                    unselectedIconColor = AmazonColors.TextSecondary,
                    unselectedTextColor = AmazonColors.TextSecondary,
                    indicatorColor = Color.Transparent, // no Material pill — Amazon has none
                ),
            )
        }
    }
}
```

The **navy sub-nav strip** ("Deliver to … City Zip") is a 36.dp `Surface(color = AmazonColors.SecondaryNavy)` row with a leading pin icon, rendered directly under `AmazonTopNav` inside the `Scaffold` `topBar`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Add to Cart tap | `animateFloatAsState` 0.98 → 1 `spring(dampingRatio = 0.8f)`; `HapticFeedbackType.LongPress` (~iOS success) |
| Cart badge increment | `Animatable` 1 → 1.3 → 1 `spring(dampingRatio = 0.55f)` over ~200ms |
| Search expand | 250ms `tween` morph navy bar → full-screen search (`AnimatedContent`) |
| Image carousel swipe | `HorizontalPager` 60fps paging; page dots cross-fade |
| Variant swatch select | border 1dp → 2dp via `animateDpAsState` 150ms + light haptic |
| Add-to-cart toast | `slideInVertically` 300ms, hold 2.5s, `slideOutVertically` 200ms |
| Buy Now confirm | full-screen green check: `Animatable` scale + alpha over 500ms |
| Deal countdown | recomposes every 1s, **no animation** (efficiency — matches DESIGN.md) |

```kotlin
// Add-to-Cart press scale (exact 0.98 → 1, damping 0.8)
val scale by animateFloatAsState(
    targetValue = if (pressed) 0.98f else 1f,
    animationSpec = spring(dampingRatio = 0.8f, stiffness = 600f),
    label = "cartScale",
)
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) — map `.selection` to the quantity stepper, a `.success`-style double-buzz `VibrationEffect.createWaveform(longArrayOf(0, 30, 50, 30), -1)` to the cart increment, and `VibrationEffect.createOneShot(20, DEFAULT_AMPLITUDE)` to "Place your order".

## 7. Icons

Amazon ships the smile logo and custom glyphs as artwork; the closest first-party set is `androidx.compose.material:material-icons-extended`. Export the Amazon smile/arrow logo and the `prime` wordmark as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Cart | `cart.fill` / `cart` | `Icons.Filled.ShoppingCart` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Mic (search) | `mic.fill` | `Icons.Filled.Mic` |
| Barcode scanner | `barcode.viewfinder` | `Icons.Filled.QrCodeScanner` |
| Lightning deal | `bolt.fill` | `Icons.Filled.Bolt` |
| Star (rating) | `star.fill` / `star` | `Icons.Filled.Star` |
| Checkmark (success) | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Plus / Minus (stepper) | `plus` / `minus` | `Icons.Filled.Add` / `Icons.Filled.Remove` |
| Hamburger menu | `line.3.horizontal` | `Icons.Filled.Menu` |
| Bell (notifications) | `bell.fill` | `Icons.Filled.Notifications` |
| Person (You tab) | `person.fill` | `Icons.Filled.Person` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Lock (secure transaction) | `lock.fill` | `Icons.Filled.Lock` |
| Amazon smile logo | — | custom vector drawable |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `HorizontalPager` + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The navy nav extends under the status bar, so request light-content (light icons) system bars while the nav is showing; switch to dark-content over the white canvas. Apply `Scaffold` insets so the cart drawer clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale. Keep it on product titles, body, and reviews. Prices scale but **cap at ~140%** — wrap the price block in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = minOf(fontScale, 1.4f)))`. The `prime` wordmark and 10sp tab labels are fixed (brand/layout-sensitive).
- **TalkBack**: combine the superscript dollars + cents into one utterance via `Modifier.semantics { contentDescription = "$28.99" }` (already wired in §3) — never let the screen reader read "$ 28 99" as three tokens. Label Add to Cart as `"Add Wireless Headphones to cart for $24.99"`; the star row as `"4.7 out of 5 stars, 12,345 reviews"`.
- **Touch targets**: Material guidance is 48.dp minimum. The Add to Cart button is `heightIn(min = 44.dp)` — bump to 48.dp on the PDP. The stepper cells are 44.dp wide; the star row needs a 48.dp tall hit box even though glyphs are 12.dp.
- **Contrast**: `#565959` secondary text on `#FFFFFF` passes WCAG AA at ≥13sp. `#848A8C` tertiary is below AA — restrict it to non-essential labels only. Black-on-yellow (`#0F1111` on `#FF9900`) is the iconic CTA contrast and passes AA — do not switch to white text.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, replace the cart-badge bump with a 100ms fade and skip the toast slide (show/hide instantly).
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Amazon Yellow `#FF9900` and Deep Navy `#131921` are load-bearing brand assets; the yellow CTA must never recolor to the user's wallpaper. The flat yellow (no gradient) is also load-bearing.
