# Zomato (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Zomato's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the semantic rating pill, the veg/non-veg mark, the bordered ADD button, the Delivery/Dining toggle, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (white canvas, Zomato Red, the semantic rating pill, the veg/non-veg mark, the bordered ADD, the Delivery/Dining toggle) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a UIKit sheet, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for hero photos and dish thumbnails. Zomato's palette is a fixed brand + semantic rating scale, so dynamic color extraction (Palette / Material You) is **not** used.

## 1. Color Tokens

```kotlin
// ui/theme/ZomatoColors.kt
import androidx.compose.ui.graphics.Color

object ZomatoColors {
    // Brand (interactive)
    val Red          = Color(0xFFE23744)
    val RedPressed   = Color(0xFFC42531)

    // Rating scale (semantic)
    val RatingGreen  = Color(0xFF267E3E)
    val RatingAmber  = Color(0xFFDB7C38)
    val RatingRed    = Color(0xFFE23744) // = brand
    val RatingGrey   = Color(0xFF9C9C9C)

    // Membership & dining
    val Gold         = Color(0xFFEFC75E)
    val DiningBlue   = Color(0xFF256FEF)

    // Canvas & Surfaces (Light)
    val Canvas       = Color(0xFFFFFFFF)
    val Surface1     = Color(0xFFF4F4F5)
    val Surface2     = Color(0xFFEBEBEC)
    val Divider      = Color(0xFFE8E8E8)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF121212) // warm near-black — NOT pure black
    val DarkSurface1 = Color(0xFF1C1C1E)
    val DarkSurface2 = Color(0xFF262629)
    val DarkDivider  = Color(0xFF2C2C2E)

    // Text
    val TextPrimary     = Color(0xFF1C1C1C)
    val TextSecondary   = Color(0xFF696969)
    val TextTertiary    = Color(0xFF9C9C9C)
    val DarkTextPrimary = Color(0xFFF2F2F2)

    // Veg / non-veg
    val VegGreen     = Color(0xFF267E3E)
    val NonVegMaroon = Color(0xFFB91C1C)

    // Semantic
    val Error        = Color(0xFFE03A3A)
}

// Semantic rating → fill color
fun ratingFill(score: Double): Color = when {
    score >= 4.0 -> ZomatoColors.RatingGreen
    score >= 3.0 -> ZomatoColors.RatingAmber
    score > 0.0  -> ZomatoColors.RatingRed
    else         -> ZomatoColors.RatingGrey
}
```

Wire it into both schemes. Zomato is light-first (white, photo-forward, dense); the dark scheme uses a warm `#121212`, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val ZomatoLight = lightColorScheme(
    primary        = ZomatoColors.Red,
    onPrimary      = Color.White,
    background      = ZomatoColors.Canvas,
    onBackground   = ZomatoColors.TextPrimary,
    surface        = ZomatoColors.Surface1,
    onSurface      = ZomatoColors.TextPrimary,
    surfaceVariant = ZomatoColors.Surface2,
    outline        = ZomatoColors.Divider,
    error          = ZomatoColors.Error,
)

private val ZomatoDark = darkColorScheme(
    primary        = ZomatoColors.Red,
    onPrimary      = Color.White,
    background      = ZomatoColors.DarkCanvas,
    onBackground   = ZomatoColors.DarkTextPrimary,
    surface        = ZomatoColors.DarkSurface1,
    onSurface      = ZomatoColors.DarkTextPrimary,
    surfaceVariant = ZomatoColors.DarkSurface2,
    outline        = ZomatoColors.DarkDivider,
    error          = ZomatoColors.Error,
)

@Composable
fun ZomatoTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) ZomatoDark else ZomatoLight,
    typography = ZomatoTypography,
    content = content,
)
```

## 2. Typography (Material 3)

Zomato's brand face is a custom Okra-family grotesque (proprietary); use **Inter** (SIL OFL) — drop the TTFs in `res/font/`. The app is dense and heavy: names 800, screen titles 900; numbers tabular.

```kotlin
// ui/theme/ZomatoType.kt
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
object ZomatoText {
    val ScreenTitle    = TextStyle(Inter, fontWeight = FontWeight.Black,     fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.5).sp)
    val RestaurantHero = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.4).sp)
    val Section        = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val MenuCategory   = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.2).sp)
    val ItemName       = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp)
    val Body           = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 14.sp, lineHeight = 20.sp)
    val Price          = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 14.sp, lineHeight = 18.sp)
    val Meta           = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val RatingPill     = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 14.sp, lineHeight = 14.sp)
    val RatingCaption  = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 11.sp, lineHeight = 14.sp)
    val Tag            = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.4.sp)
    val Button         = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 16.sp)
    val AddLabel       = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 14.sp, lineHeight = 14.sp, letterSpacing = 0.3.sp)
    val Tab            = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Caption        = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val ZomatoTypography = Typography(
    headlineLarge  = ZomatoText.ScreenTitle,
    headlineMedium = ZomatoText.RestaurantHero,
    titleLarge     = ZomatoText.Section,
    titleMedium    = ZomatoText.MenuCategory,
    bodyMedium     = ZomatoText.Body,
    labelSmall     = ZomatoText.Tab,
)
```

For tabular figures on prices/ratings, use `TextStyle.copy(fontFeatureSettings = "tnum")`.

## 3. Signature Components

### Semantic Rating Pill

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.unit.dp

@Composable
fun RatingPill(score: Double, count: String? = null) {
    Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(5.dp)) {
        Row(
            Modifier.clip(RoundedCornerShape(7.dp)).background(ratingFill(score))
                .padding(horizontal = 9.dp, vertical = 4.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(4.dp),
        ) {
            Icon(Icons.Filled.Star, null, tint = Color.White, modifier = Modifier.size(11.dp))
            Text(String.format("%.1f", score),
                style = ZomatoText.RatingPill.copy(fontFeatureSettings = "tnum"), color = Color.White)
        }
        if (count != null) {
            Text(count, style = ZomatoText.RatingCaption,
                color = ZomatoColors.TextSecondary, textDecoration = TextDecoration.Underline)
        }
    }
}
```

### Veg / Non-Veg Mark

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.border
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Path

@Composable
fun VegMark(isVeg: Boolean) {
    val c = if (isVeg) ZomatoColors.VegGreen else ZomatoColors.NonVegMaroon
    Box(
        Modifier.size(16.dp).border(1.5.dp, c, RoundedCornerShape(3.dp)),
        contentAlignment = Alignment.Center,
    ) {
        if (isVeg) {
            Box(Modifier.size(7.dp).clip(RoundedCornerShape(50)).background(c))
        } else {
            Canvas(Modifier.size(9.dp)) {
                val p = Path().apply {
                    moveTo(size.width / 2f, 0f)
                    lineTo(size.width, size.height)
                    lineTo(0f, size.height)
                    close()
                }
                drawPath(p, c)
            }
        }
    }
}
```

### Menu Item Row (bordered ADD → stepper)

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shadow
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Remove
import androidx.compose.material.icons.filled.Star
import androidx.compose.runtime.*
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.IntOffset
import coil.compose.AsyncImage

@Composable
fun MenuItemRow(
    isVeg: Boolean, bestseller: Boolean, name: String, price: String,
    rating: Double, ratingCount: Int, imageUrl: String,
) {
    var qty by remember { mutableIntStateOf(0) }
    val haptics = LocalHapticFeedback.current
    fun tick(n: Int) { haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove); qty = n }

    Row(
        Modifier.fillMaxWidth().padding(vertical = 14.dp),
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Column(Modifier.weight(1f)) {
            VegMark(isVeg)
            if (bestseller) {
                Row(Modifier.padding(top = 7.dp, bottom = 3.dp),
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    Icon(Icons.Filled.Star, null, tint = ZomatoColors.RatingAmber, modifier = Modifier.size(9.dp))
                    Text("Bestseller", style = ZomatoText.Tag, color = ZomatoColors.RatingAmber)
                }
            }
            Text(name, style = ZomatoText.ItemName, color = ZomatoColors.TextPrimary,
                modifier = if (bestseller) Modifier else Modifier.padding(top = 7.dp))
            Text(price, style = ZomatoText.Price, color = ZomatoColors.TextPrimary,
                modifier = Modifier.padding(top = 5.dp))
            Row(Modifier.padding(top = 6.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Icon(Icons.Filled.Star, null, tint = ZomatoColors.RatingGreen, modifier = Modifier.size(10.dp))
                Text("${"%.1f".format(rating)} ($ratingCount)",
                    style = ZomatoText.Caption, color = ZomatoColors.TextSecondary)
            }
        }

        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            AsyncImage(model = imageUrl, contentDescription = null,
                modifier = Modifier.size(width = 96.dp, height = 90.dp).clip(RoundedCornerShape(12.dp)),
                contentScale = ContentScale.Crop)
            Box(
                Modifier
                    .offset { IntOffset(0, -16.dp.roundToPx()) }   // overlaps the thumbnail
                    .shadow(8.dp, RoundedCornerShape(10.dp))
                    .clip(RoundedCornerShape(10.dp))
                    .background(ZomatoColors.Canvas)
                    .border(1.dp, ZomatoColors.Red, RoundedCornerShape(10.dp)),
            ) {
                if (qty == 0) {
                    Text("ADD", style = ZomatoText.AddLabel, color = ZomatoColors.Red,
                        modifier = Modifier.clickable { qty = 1 }
                            .padding(horizontal = 22.dp, vertical = 8.dp))
                } else {
                    Row(verticalAlignment = Alignment.CenterVertically) {
                        Icon(Icons.Filled.Remove, "Decrease", tint = ZomatoColors.Red,
                            modifier = Modifier.size(36.dp).clickable { if (qty > 1) tick(qty - 1) else qty = 0 }
                                .padding(11.dp))
                        Text("$qty", style = ZomatoText.ItemName.copy(fontFeatureSettings = "tnum"),
                            color = ZomatoColors.Red, modifier = Modifier.widthIn(min = 24.dp))
                        Icon(Icons.Filled.Add, "Increase", tint = ZomatoColors.Red,
                            modifier = Modifier.size(36.dp).clickable { tick(qty + 1) }.padding(11.dp))
                    }
                }
            }
        }
    }
    HorizontalDivider(color = ZomatoColors.Divider, thickness = 1.dp)
}
```

### Delivery / Dining Out Toggle

```kotlin
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.foundation.layout.fillMaxWidth

@Composable
fun DeliveryDiningToggle(isDelivery: Boolean, onChange: (Boolean) -> Unit) {
    Row(
        Modifier.fillMaxWidth().clip(RoundedCornerShape(50))
            .background(ZomatoColors.Surface1).padding(4.dp),
    ) {
        Seg("Delivery",   isDelivery,  Modifier.weight(1f)) { onChange(true) }
        Seg("Dining Out", !isDelivery, Modifier.weight(1f)) { onChange(false) }
    }
}

@Composable
private fun Seg(label: String, active: Boolean, modifier: Modifier, onClick: () -> Unit) {
    Box(
        modifier.clip(RoundedCornerShape(50))
            .background(if (active) ZomatoColors.Red else Color.Transparent)
            .clickable { onClick() }
            .padding(vertical = 9.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(label,
            style = ZomatoText.RatingPill.copy(fontSize = 13.sp, fontWeight = FontWeight.Bold),
            color = if (active) Color.White else ZomatoColors.TextSecondary)
    }
}
```

### Restaurant Detail Header (hero + pull-up card)

```kotlin
import androidx.compose.material.icons.filled.ArrowBack
import androidx.compose.material.icons.filled.FavoriteBorder
import androidx.compose.material.icons.filled.Search

@Composable
fun RestaurantDetailHeader(
    imageUrl: String, name: String, meta: String,
    deliveryRating: Double, diningRating: Double,
) {
    Column {
        Box(Modifier.fillMaxWidth().height(200.dp)) {
            AsyncImage(model = imageUrl, contentDescription = name,
                modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
            Row(
                Modifier.fillMaxWidth().padding(start = 16.dp, end = 16.dp, top = 14.dp),
                verticalAlignment = Alignment.CenterVertically,
            ) {
                HeroBtn(Icons.Filled.ArrowBack)
                Spacer(Modifier.weight(1f))
                Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {
                    HeroBtn(Icons.Filled.Search)
                    HeroBtn(Icons.Filled.FavoriteBorder)
                }
            }
        }
        Column(
            Modifier.fillMaxWidth()
                .offset { IntOffset(0, -22.dp.roundToPx()) }
                .clip(RoundedCornerShape(topStart = 22.dp, topEnd = 22.dp))
                .background(ZomatoColors.Canvas)
                .padding(horizontal = 18.dp).padding(top = 20.dp),
        ) {
            Text(name, style = ZomatoText.RestaurantHero, color = ZomatoColors.TextPrimary)
            Text(meta, style = ZomatoText.Meta, color = ZomatoColors.TextSecondary,
                modifier = Modifier.padding(top = 5.dp))
            Row(Modifier.padding(top = 14.dp), horizontalArrangement = Arrangement.spacedBy(18.dp)) {
                RatingPill(deliveryRating, "12.4K Delivery")
                RatingPill(diningRating, "3.1K Dining")
            }
        }
    }
}

@Composable
private fun HeroBtn(icon: androidx.compose.ui.graphics.vector.ImageVector) {
    Box(
        Modifier.size(34.dp).clip(RoundedCornerShape(50)).background(Color.Black.copy(alpha = 0.4f)),
        contentAlignment = Alignment.Center,
    ) { Icon(icon, null, tint = Color.White, modifier = Modifier.size(17.dp)) }
}
```

### Primary Button

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.draw.scale

@Composable
fun ZButton(title: String, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        Modifier.fillMaxWidth().heightIn(min = 52.dp)
            .scale(if (pressed) 0.98f else 1f)
            .clip(RoundedCornerShape(12.dp))
            .background(if (pressed) ZomatoColors.RedPressed else ZomatoColors.Red)
            .clickable(interaction, indication = null) { onClick() },
        contentAlignment = Alignment.Center,
    ) { Text(title, style = ZomatoText.Button, color = Color.White) }
}
// Embeds price: ZButton("Add item · ₹320", onClick = {})
```

## 4. Navigation

Zomato has a 4-tab bottom strip and no Material tint pill — active is just Zomato Red.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun ZomatoBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = ZomatoColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Delivery" to Icons.Filled.Home,
            "Dining"   to Icons.Filled.Restaurant,
            "Live"     to Icons.Filled.GraphicEq,
            "Profile"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = ZomatoText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = ZomatoColors.Red,
                    selectedTextColor   = ZomatoColors.Red,
                    unselectedIconColor = Color(0xFF888888),
                    unselectedTextColor = Color(0xFF888888),
                    indicatorColor      = Color.Transparent, // no Material pill — Zomato has none
                ),
            )
        }
    }
}
```

The customize, cart and address surfaces are `ModalBottomSheet` with a 22.dp top radius (matching the detail pull-up card). The rider map uses Google Maps Compose with a Zomato-Red route + rider marker; the sticky cart bar floats above the menu with the item count + total embedded in a `ZButton`.

## 5. Motion

Zomato motion is functional — the rating color is the signal, not animation.

| Moment | Compose recipe |
|--------|----------------|
| ADD → stepper | `AnimatedContent` swapping the ADD label for the stepper `tween(180)`; cart bar `slideInVertically` `tween(280)` + `HapticFeedbackType.LongPress` |
| Quantity change | `Crossfade` / `AnimatedContent` on the count + `TextHandleMove` haptic |
| Delivery/Dining toggle | active fill `animateDpAsState` x-offset `spring(dampingRatio = 0.85f)`; content `Crossfade(tween(200))` |
| Hero pull-up parallax | `LazyColumn` + `nestedScroll`; hero `graphicsLayer { translationY = scroll * 0.5f }`; nav alpha animates as the hero scrolls away |
| Card → detail | shared element via `SharedTransitionLayout` (Compose 1.7+) `tween(320)` |
| Rating pill appear | `fadeIn` + slight `scaleIn` `tween(150)` — color is the signal, keep it subtle |
| Sheet present | `ModalBottomSheet` default slide; scrim fades in parallel |
| Order tracking advance | status node `animateDpAsState` `tween(250)` + `HapticFeedbackType.LongPress` (medium analog) |

```kotlin
// ADD → stepper — the canonical Zomato motion
AnimatedContent(targetState = qty == 0, label = "addToStepper",
    transitionSpec = { fadeIn(tween(180)) togetherWith fadeOut(tween(120)) }) { isAdd ->
    if (isAdd) /* ADD label */ else /* bordered red stepper */
}
```

Haptics: `HapticFeedbackType.TextHandleMove` for the stepper tick; `HapticFeedbackType.LongPress` for ADD / stage-complete. Order-placed success can use `VibrationEffect.createPredefined(EFFECT_HEAVY_CLICK)` via the system `Vibrator`.

## 6. Icons

The closest first-party set to Zomato's iconography is `androidx.compose.material:material-icons-extended`. The veg/non-veg mark is custom-drawn (Canvas), not an icon.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Delivery (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Dining (tab) | `fork.knife` | `Icons.Filled.Restaurant` |
| Live (tab) | `dot.radiowaves.left.and.right` | `Icons.Filled.GraphicEq` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Rating star | `star.fill` | `Icons.Filled.Star` |
| Hero back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Hero search | `magnifyingglass` | `Icons.Filled.Search` |
| Hero save | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Icons.Filled.Favorite` |
| ADD minus | `minus` | `Icons.Filled.Remove` |
| ADD plus | `plus` | `Icons.Filled.Add` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Dining — book | `calendar` | `Icons.Filled.CalendarMonth` |
| Offer | `tag.fill` | `Icons.Filled.LocalOffer` |
| Pro / Gold | `crown.fill` | `Icons.Filled.WorkspacePremium` |
| Rider (tracking) | `bicycle` | `Icons.Filled.DirectionsBike` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion and `SharedTransitionLayout` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the white canvas wants dark-content system bars (light-content over the hero photo and in dark mode). The hero nav scrims respect the camera cutout; the bottom tab + sticky cart bar use `Modifier.navigationBarsPadding()`.
- **Semantic rating**: always derive the pill fill from `ratingFill(score)` — never hard-code one color. The color carrying the score's quality is the core of Zomato.
- **Veg/non-veg is not color-only**: the `VegMark` must carry a `contentDescription` ("Vegetarian"/"Non-vegetarian"); it is legally significant in India and must not depend on color perception.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, restaurant names, body and prices. Pin layout-sensitive text (the rating pill, veg mark, 11sp Bestseller tag, 10sp tab labels, the ADD label) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Tabular numerals**: apply `fontFeatureSettings = "tnum"` to ratings, prices, review counts and quantity counters.
- **TalkBack**: the rating pill announces score **and** quality ("4.3 stars, rated great"); the menu row is one focusable node combining veg status + name + price + rating; the ADD button is "Add {dish}" and after tap becomes a stepper with state.
- **Touch targets**: Material guidance is 48.dp. Give the ADD button and the 36.dp stepper controls a 48.dp hit area via padding; the segmented toggle halves are ≥ 44.dp tall; primary buttons ≥ 52.dp.
- **Contrast**: `#1C1C1C` on `#FFFFFF` passes WCAG AAA; white on the rating greens/ambers/reds passes AA for the pill score; the semantic scale is always paired with the star + an accessible label so it is never color-only.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the ADD→stepper transition and the toggle's sliding fill (substitute a `Crossfade`); keep the rating pill static.
- **Dark mode**: invert via the `Dark*` palette — `#121212`, NOT true black; `#1C1C1C` text becomes `#F2F2F2`. Brand red, the full rating scale, veg colors, gold and dining blue are constant so a pill's color always means the same thing. Shadows are nearly invisible on dark, so add a 0.5dp `DarkDivider` border on floating surfaces (ADD button, sticky cart). Do **not** enable Material You `dynamicColorScheme()` — Zomato's red identity and the semantic rating scale must hold regardless of wallpaper.
