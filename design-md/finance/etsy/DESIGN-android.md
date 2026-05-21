# Etsy (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Etsy's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Etsy's warm-cream handmade canvas, single-orange accent, the bouncing favorite heart) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Animatable` keyframes instead of a UIKit spring, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/EtsyColors.kt
import androidx.compose.ui.graphics.Color

object EtsyColors {
    // Canvas & Surfaces
    val Canvas         = Color(0xFFFAF5EF)
    val Card           = Color(0xFFFFFFFF)
    val Surface        = Color(0xFFF1F1F1)
    val Divider        = Color(0xFFE1DDD5)
    val SurfacePressed = Color(0xFFEFEAE2)

    // Text
    val TextPrimary    = Color(0xFF222222)
    val TextSecondary  = Color(0xFF595959)
    val TextTertiary   = Color(0xFF8A8A8A)

    // Brand
    val Orange         = Color(0xFFF1641E)
    val OrangePressed  = Color(0xFFD5571A)
    val OrangeTint     = Color(0xFFFDEDE4)

    // Semantic
    val StarFilled     = Color(0xFF222222)
    val SaleRed        = Color(0xFFA61A12)
    val Success        = Color(0xFF258635)

    // Warm-tinted card shadow (brown ambient — not neutral)
    val CardShadow     = Color(0xFF785A32)
}
```

Wire it into a Material 3 `lightColorScheme`. Etsy is warm-light-first.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val EtsyScheme = lightColorScheme(
    primary        = EtsyColors.Orange,
    onPrimary      = Color.White,
    background     = EtsyColors.Canvas,
    onBackground   = EtsyColors.TextPrimary,
    surface        = EtsyColors.Card,
    onSurface      = EtsyColors.TextPrimary,
    surfaceVariant = EtsyColors.Surface,
    outline        = EtsyColors.Divider,
    error          = EtsyColors.SaleRed,
)

@Composable
fun EtsyTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = EtsyScheme, typography = EtsyTypography, content = content)
```

## 2. Typography

Graphik is proprietary. Drop substitute Inter TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto).

```kotlin
// ui/theme/EtsyType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val EtsyGraphik = FontFamily(
    Font(R.font.inter_regular, FontWeight.Normal),  // 400
    Font(R.font.inter_medium,  FontWeight.Medium),  // 500
    Font(R.font.inter_bold,    FontWeight.Bold),    // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object EtsyText {
    val TitleLarge = TextStyle(EtsyGraphik, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val Listing    = TextStyle(EtsyGraphik, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Section    = TextStyle(EtsyGraphik, fontWeight = FontWeight.Bold,   fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Price      = TextStyle(EtsyGraphik, fontWeight = FontWeight.Bold,   fontSize = 24.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val CardTitle  = TextStyle(EtsyGraphik, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 20.sp)
    val CardPrice  = TextStyle(EtsyGraphik, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val Body       = TextStyle(EtsyGraphik, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 23.sp)
    val ShopName   = TextStyle(EtsyGraphik, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 18.sp)
    val Button     = TextStyle(EtsyGraphik, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 20.sp)
    val Meta       = TextStyle(EtsyGraphik, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val Badge      = TextStyle(EtsyGraphik, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
    val Tab        = TextStyle(EtsyGraphik, fontWeight = FontWeight.Medium, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
    val ReviewCnt  = TextStyle(EtsyGraphik, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val EtsyTypography = Typography(
    headlineLarge = EtsyText.TitleLarge,
    headlineSmall = EtsyText.Section,
    titleMedium   = EtsyText.CardTitle,
    bodyMedium    = EtsyText.Body,
    labelSmall    = EtsyText.Tab,
)
```

## 3. Signature Components

### Favorite Heart (the emotional core)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.outlined.FavoriteBorder
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp
import kotlinx.coroutines.launch

@Composable
fun FavoriteHeart(
    saved: Boolean,
    onToggle: () -> Unit,
    overPhoto: Boolean = true,
    size: Int = 24,
    modifier: Modifier = Modifier,
) {
    val haptics = LocalHapticFeedback.current
    val scale = remember { Animatable(1f) }
    val scope = rememberCoroutineScope()

    Box(
        modifier
            .then(
                if (overPhoto) Modifier
                    .size(36.dp)
                    .clip(CircleShape)
                    .background(Color.Black.copy(alpha = 0.45f))
                else Modifier
            )
            .scale(scale.value)
            .clickable(remember { MutableInteractionSource() }, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                scope.launch {
                    scale.animateTo(1.25f, tween(150)) // bounce up
                    scale.animateTo(1f, tween(150))    // settle
                }
                onToggle()
            },
        contentAlignment = androidx.compose.ui.Alignment.Center,
    ) {
        Icon(
            imageVector = if (saved) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
            contentDescription = if (saved) "Saved to favorites" else "Add to favorites",
            tint = if (saved) EtsyColors.Orange else if (overPhoto) Color.White else EtsyColors.TextPrimary,
            modifier = Modifier.size(size.dp),
        )
    }
}
```

### Star Row

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material.icons.filled.Star
import androidx.compose.material.icons.outlined.StarBorder
import androidx.compose.material3.Text

@Composable
fun StarRow(rating: Double, reviews: Int, starSize: Int = 11) {
    val r = Math.round(rating).toInt()
    Row(verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp)) {
        Row {
            (1..5).forEach { i ->
                Icon(
                    if (i <= r) Icons.Filled.Star else Icons.Outlined.StarBorder,
                    contentDescription = null,
                    tint = EtsyColors.StarFilled,
                    modifier = Modifier.size(starSize.dp),
                )
            }
        }
        Text("(%,d)".format(reviews), style = EtsyText.ReviewCnt, color = EtsyColors.TextSecondary)
    }
}
```

### Handmade Product Card

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.shadow
import coil.compose.AsyncImage

@Composable
fun ProductCard(
    title: String,
    photoUrl: String,
    price: String,
    rating: Double,
    reviews: Int,
    shop: String,
    bestseller: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    var saved by remember { mutableStateOf(false) }
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val cardScale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "card")

    Column(
        modifier
            .scale(cardScale)
            // Warm-tinted ambient shadow
            .shadow(10.dp, RoundedCornerShape(12.dp), ambientColor = EtsyColors.CardShadow,
                    spotColor = EtsyColors.CardShadow)
            .clip(RoundedCornerShape(12.dp))
            .background(EtsyColors.Card)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(8.dp),
        verticalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Box {
            AsyncImage(
                model = photoUrl,
                contentDescription = title,
                modifier = Modifier.fillMaxWidth().aspectRatio(1f).clip(RoundedCornerShape(12.dp)),
                contentScale = ContentScale.Crop,
            )
            if (bestseller) {
                Text(
                    "BESTSELLER",
                    style = EtsyText.Badge,
                    color = EtsyColors.Orange,
                    modifier = Modifier
                        .align(Alignment.TopStart).padding(8.dp)
                        .clip(CircleShape).background(EtsyColors.OrangeTint)
                        .padding(horizontal = 10.dp, vertical = 4.dp),
                )
            }
            FavoriteHeart(
                saved = saved, onToggle = { saved = !saved },
                modifier = Modifier.align(Alignment.TopEnd).padding(8.dp),
            )
        }
        Text(title, style = EtsyText.CardTitle, color = EtsyColors.TextPrimary, maxLines = 2)
        Text(price, style = EtsyText.CardPrice, color = EtsyColors.TextPrimary)
        StarRow(rating, reviews)
        Text(shop, style = EtsyText.ShopName, color = EtsyColors.TextSecondary)
        Text("Free shipping", style = EtsyText.Meta, color = EtsyColors.TextSecondary)
    }
}
```

### Primary Button

```kotlin
@Composable
fun EtsyButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "btn")
    Box(
        modifier
            .fillMaxWidth()
            .scale(scale)
            .clip(CircleShape) // full pill
            .background(if (pressed) EtsyColors.OrangePressed else EtsyColors.Orange)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            }
            .padding(vertical = 16.dp),
        contentAlignment = Alignment.Center,
    ) { Text(text, style = EtsyText.Button, color = Color.White) }
}
```

### Listing Detail Hero (gallery)

```kotlin
import androidx.compose.foundation.pager.HorizontalPager
import androidx.compose.foundation.pager.rememberPagerState

@Composable
fun ListingHero(photos: List<String>) {
    val pager = rememberPagerState { photos.size }
    var saved by remember { mutableStateOf(false) }
    Box {
        HorizontalPager(state = pager) { i ->
            AsyncImage(
                model = photos[i],
                contentDescription = "Listing photo ${i + 1}",
                modifier = Modifier.fillMaxWidth().aspectRatio(4f / 5f),
                contentScale = ContentScale.Crop,
            )
        }
        FavoriteHeart(saved, { saved = !saved },
            modifier = Modifier.align(Alignment.TopEnd).padding(16.dp))
        Row(
            Modifier.align(Alignment.BottomCenter).padding(bottom = 14.dp),
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            repeat(photos.size) { i ->
                Box(
                    Modifier.size(6.dp).clip(CircleShape)
                        .background(if (i == pager.currentPage) Color.White else Color.White.copy(alpha = 0.5f))
                )
            }
        }
        Text(
            "${pager.currentPage + 1} / ${photos.size}",
            style = EtsyText.Meta, color = Color.White,
            modifier = Modifier
                .align(Alignment.BottomEnd).padding(12.dp)
                .clip(CircleShape).background(Color.Black.copy(alpha = 0.5f))
                .padding(horizontal = 10.dp, vertical = 4.dp),
        )
    }
}
```

## 4. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Etsy's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 97%-opaque cream surface. **Active tint is Etsy Orange** — orange is the active indicator.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun EtsyBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = EtsyColors.Canvas.copy(alpha = 0.97f), tonalElevation = 0.dp) {
        val items = listOf(
            "Home"      to Icons.Filled.Home,
            "Search"    to Icons.Filled.Search,
            "You"       to Icons.Filled.Person,
            "Favorites" to Icons.Filled.Favorite,
            "Cart"      to Icons.Filled.ShoppingCart,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = EtsyText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = EtsyColors.Orange,
                    selectedTextColor   = EtsyColors.Orange,
                    unselectedIconColor = EtsyColors.TextSecondary,
                    unselectedTextColor = EtsyColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // no Material pill — Etsy has none
                ),
            )
        }
    }
}
```

The Cart tab carries an orange count badge when non-empty — use `BadgedBox` around the cart `Icon`.

## 5. Navigation

The top bar carries the Etsy wordmark (left, orange) and notification + cart icons (right). Below it, a pinned search field — a `#FFFFFF` `CircleShape` pill, 48.dp tall, 1.dp `EtsyColors.Divider` border. On scroll, collapse to a compact bar; Android has no live blur, so use the 97%-opaque cream surface. The listing detail uses a bottom sticky bar (`Scaffold` `bottomBar`) with the price and a full-width orange "Add to cart" pill.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Favorite-heart bounce | `Animatable` keyframes 1 → 1.25 → 1 over 300ms; `HapticFeedbackType.LongPress` |
| Add-to-cart | `HapticFeedbackType.LongPress` + a `SharedTransitionLayout` fly-to-cart of the thumbnail; bump the cart badge |
| Card tap | `animateFloatAsState` 1 → 0.98 `spring(dampingRatio = 0.8f)` |
| Gallery paging | `HorizontalPager` snap; a light haptic on `pagerState.currentPage` change |
| Sticky bar reveal | `AnimatedVisibility` + `slideInVertically` as the hero scrolls off |

```kotlin
// Favorite bounce (inside FavoriteHeart's click)
scope.launch {
    scale.animateTo(1.25f, tween(150))
    scale.animateTo(1f, tween(150))
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's `.medium` impact.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity with Etsy's custom glyphs, export them as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Favorite | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Star | `star` / `star.fill` | `Icons.Outlined.StarBorder` / `Icons.Filled.Star` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Cart | `cart` / `cart.fill` | `Icons.Filled.ShoppingCart` |
| Filters | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Message seller | `bubble.left` | `Icons.AutoMirrored.Outlined.Chat` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| You (tab) | `person.fill` | `Icons.Filled.Person` |
| Favorites (tab) | `heart.fill` | `Icons.Filled.Favorite` |
| Cart (tab) | `cart.fill` | `Icons.Filled.ShoppingCart` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the warm cream canvas wants `WindowCompat` dark-content system bars. Apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` or `Scaffold` insets; the listing hero bleeds full-width while the sticky bar clears the gesture nav.
- **Warm shadow**: pass `ambientColor`/`spotColor = EtsyColors.CardShadow` to `Modifier.shadow` for the brown tint (API 28+). On older APIs the shadow is neutral — add a 1.dp `EtsyColors.Divider` border so cards still separate from cream.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on the listing title, price, body, card title (2-line clamp). Pin badge text, tab labels, the gallery photo-count pill, and star glyphs (geometric) by deriving size from `dp`.
- **TalkBack**: give the favorite heart a `contentDescription` toggle (`"Saved" / "Add to favorites"`); merge the star row into one `contentDescription` like `"Rated 4.9 out of 5, 1,284 reviews"`; group product-card text with `Modifier.semantics(mergeDescendants = true)`.
- **Touch targets**: Material guidance is 48.dp minimum. The CTA clears it; ensure the 36.dp favorite-heart backing and gallery floating controls have ≥48.dp effective touch via padding.
- **Contrast**: `#595959` on `#FAF5EF` passes WCAG AA at 13sp+. The cream canvas is intentionally low-contrast warmth — keep headings/prices at `#222222`.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Etsy's handmade identity requires the fixed warm-cream canvas and single-orange accent regardless of wallpaper.
