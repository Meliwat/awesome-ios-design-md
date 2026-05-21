# Airbnb (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Airbnb's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Airbnb's white editorial canvas, photography-as-hero stay cards, single coral accent, soft 16dp radii) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a translucent `Surface` instead of `.regularMaterial` blur, `sp`/`dp` instead of `pt`. Airbnb is a *light-canvas* app, so we wire a Material 3 `lightColorScheme`, not a dark one.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for the remote stay photography.

## 1. Color Tokens

```kotlin
// ui/theme/AirbnbColors.kt
import androidx.compose.ui.graphics.Color

object AirbnbColors {
    // Canvas, Surfaces & Dividers
    val Canvas       = Color(0xFFFFFFFF)
    val SurfaceGray  = Color(0xFFF7F7F7)
    val SurfaceGray2 = Color(0xFFEBEBEB)
    val Divider      = Color(0xFFEBEBEB)

    // Text
    val Hof        = Color(0xFF484848) // primary text — titles, body
    val Foggy      = Color(0xFF767676) // secondary — host byline, review count, dates
    val FoggyLight = Color(0xFFB0B0B0) // tertiary — placeholders, disabled
    val Ink        = Color(0xFF222222) // hero titles, borders, visited map pin

    // Brand
    val Coral        = Color(0xFFFF385C) // primary — Reserve, save-heart fill, active tab
    val CoralPressed = Color(0xFFE31C5F)
    val Rausch       = Color(0xFFFF5A5F) // heritage — Belo logomark only
    val Babu         = Color(0xFF00A699) // Plus / Experiences / Superhost seal
    val Arches       = Color(0xFFFC642D) // Trips module
    val Beach        = Color(0xFFFFB400) // star yellow — review screens only

    // Semantic
    val Success = Color(0xFF008A05)
    val Error   = Color(0xFFC13515)

    // Dark mode (warm — preserves photography integrity, not true black)
    val DarkCanvas  = Color(0xFF121212)
    val DarkSurface = Color(0xFF1C1C1E)
    val DarkText    = Color(0xFFDDDDDD)
    val DarkTextSec = Color(0xFFA0A0A0)
}
```

Airbnb is a white-canvas editorial app, so wire a Material 3 `lightColorScheme` (provide the warm-dark scheme for dark mode). Ripples, dividers, and stock component defaults then inherit the brand.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val AirbnbLight = lightColorScheme(
    primary        = AirbnbColors.Coral,
    onPrimary      = Color.White,
    background     = AirbnbColors.Canvas,
    onBackground   = AirbnbColors.Hof,         // warm #484848, never pure black
    surface        = AirbnbColors.Canvas,
    onSurface      = AirbnbColors.Hof,
    surfaceVariant = AirbnbColors.SurfaceGray,
    outline        = AirbnbColors.Divider,
    error          = AirbnbColors.Error,
)

private val AirbnbDark = darkColorScheme(
    primary        = AirbnbColors.Coral,       // coral is unchanged on dark
    onPrimary      = Color.White,
    background     = AirbnbColors.DarkCanvas,
    onBackground   = AirbnbColors.DarkText,
    surface        = AirbnbColors.DarkSurface,
    onSurface      = AirbnbColors.DarkText,
    surfaceVariant = AirbnbColors.DarkSurface,
    outline        = Color(0xFF2C2C2E),
    error          = AirbnbColors.Error,
)

@Composable
fun AirbnbTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (darkTheme) AirbnbDark else AirbnbLight,
    typography  = AirbnbTypography,
    content     = content,
)
```

## 2. Typography

Airbnb Cereal (Dalton Maag, 2018) is proprietary. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto) — but Cereal is warm and tightly spaced, so if you ship a substitute prefer bundling `Plus Jakarta Sans` / `Manrope`, which capture its roundness better than a pure grotesque.

```kotlin
// ui/theme/AirbnbType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val AirbnbCereal = FontFamily(
    Font(R.font.airbnb_cereal_light,     FontWeight.Light),     // 300
    Font(R.font.airbnb_cereal_book,      FontWeight.Normal),    // 400
    Font(R.font.airbnb_cereal_medium,    FontWeight.Medium),    // 500
    Font(R.font.airbnb_cereal_bold,      FontWeight.Bold),      // 700
    Font(R.font.airbnb_cereal_extrabold, FontWeight.ExtraBold), // 800
    Font(R.font.airbnb_cereal_black,     FontWeight.Black),     // 900
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object AirbnbText {
    val LargeNav    = TextStyle(AirbnbCereal, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val Hero        = TextStyle(AirbnbCereal, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(AirbnbCereal, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Subsection  = TextStyle(AirbnbCereal, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val CardTitle   = TextStyle(AirbnbCereal, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.05).sp)
    val Body        = TextStyle(AirbnbCereal, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 22.sp)
    val BodySmall   = TextStyle(AirbnbCereal, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(AirbnbCereal, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 18.sp)
    val RatingNum   = TextStyle(AirbnbCereal, fontWeight = FontWeight.Medium,    fontSize = 14.sp, lineHeight = 17.sp)
    val PriceInline = TextStyle(AirbnbCereal, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 18.sp)
    val PriceHero   = TextStyle(AirbnbCereal, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Button      = TextStyle(AirbnbCereal, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val ButtonSm    = TextStyle(AirbnbCereal, fontWeight = FontWeight.Medium,    fontSize = 14.sp, lineHeight = 14.sp)
    val Tab         = TextStyle(AirbnbCereal, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Chip        = TextStyle(AirbnbCereal, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 12.sp)
    val Caption     = TextStyle(AirbnbCereal, fontWeight = FontWeight.Normal,    fontSize = 12.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val AirbnbTypography = Typography(
    headlineLarge  = AirbnbText.LargeNav,
    headlineMedium = AirbnbText.Hero,
    headlineSmall  = AirbnbText.Section,
    titleMedium    = AirbnbText.CardTitle,
    bodyLarge      = AirbnbText.Body,
    bodyMedium     = AirbnbText.BodySmall,
    labelSmall     = AirbnbText.Tab,
)
```

## 3. Signature Components

### Save Heart (top-right of every stay card)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.outlined.FavoriteBorder
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun SaveHeart(
    isSaved: Boolean,
    onToggle: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val haptics = LocalHapticFeedback.current
    val scale = remember { Animatable(1f) }
    LaunchedEffect(isSaved) {
        if (isSaved) {
            scale.animateTo(1.25f, spring(dampingRatio = 0.6f, stiffness = 500f))
            scale.animateTo(1f, spring(dampingRatio = 0.6f, stiffness = 500f))
        }
    }

    Box(
        modifier = modifier
            .size(44.dp) // 44dp tap target around a 32dp glyph
            .clickable(
                interactionSource = remember { MutableInteractionSource() },
                indication = null,
            ) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS .soft impact
                onToggle()
            },
        contentAlignment = Alignment.Center,
    ) {
        if (isSaved) {
            Icon(
                Icons.Filled.Favorite,
                contentDescription = "Saved",
                tint = AirbnbColors.Coral,
                modifier = Modifier.size(28.dp).scale(scale.value),
            )
        } else {
            // White fill + ink outline so it stays legible over varied photography
            Icon(
                Icons.Filled.Favorite,
                contentDescription = null,
                tint = Color.White,
                modifier = Modifier
                    .size(28.dp)
                    .shadow(4.dp, CircleShape, spotColor = Color.Black.copy(alpha = 0.2f)),
            )
            Icon(
                Icons.Outlined.FavoriteBorder,
                contentDescription = "Save",
                tint = AirbnbColors.Ink,
                modifier = Modifier.size(28.dp),
            )
        }
    }
}
```

### Rating Row (star + number + review count)

```kotlin
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Row
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Text

@Composable
fun RatingRow(rating: Double, reviewCount: Int, modifier: Modifier = Modifier) {
    Row(
        modifier = modifier,
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Icon(
            Icons.Filled.Star,
            contentDescription = null,
            tint = AirbnbColors.Hof, // dark gray on cards — NOT yellow
            modifier = Modifier.size(12.dp),
        )
        Text("%.2f".format(rating), style = AirbnbText.RatingNum, color = AirbnbColors.Hof)
        Text(" · ${"%,d".format(reviewCount)}", style = AirbnbText.Meta, color = AirbnbColors.Foggy)
    }
}
```

### Stay Card (the hero component)

`HorizontalPager` from `androidx.compose.foundation.pager` replaces SwiftUI's paged `TabView`.

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.pager.HorizontalPager
import androidx.compose.foundation.pager.rememberPagerState
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import coil.compose.AsyncImage

@Composable
fun StayCard(
    photoUrls: List<String>,
    title: String,
    host: String,
    dates: String,
    pricePerNight: Int,
    rating: Double,
    reviewCount: Int,
    modifier: Modifier = Modifier,
) {
    var isSaved by remember { mutableStateOf(false) }
    val pager = rememberPagerState(pageCount = { photoUrls.size })

    Column(modifier = modifier.fillMaxWidth()) {
        Box {
            HorizontalPager(
                state = pager,
                modifier = Modifier
                    .fillMaxWidth()
                    .aspectRatio(4f / 3f) // signature 4:3
                    .clip(RoundedCornerShape(16.dp)), // signature 16dp radius
            ) { page ->
                AsyncImage(
                    model = photoUrls[page],
                    contentDescription = "$title photo ${page + 1}",
                    modifier = Modifier.fillMaxSize(),
                    contentScale = ContentScale.Crop,
                )
            }
            SaveHeart(
                isSaved = isSaved,
                onToggle = { isSaved = !isSaved },
                modifier = Modifier.align(Alignment.TopEnd).padding(12.dp),
            )
            // Dot indicators bottom-center (6 max)
            Row(
                Modifier.align(Alignment.BottomCenter).padding(bottom = 8.dp),
                horizontalArrangement = Arrangement.spacedBy(6.dp),
            ) {
                repeat(minOf(photoUrls.size, 6)) { i ->
                    Box(
                        Modifier
                            .size(6.dp)
                            .clip(CircleShape)
                            .background(
                                if (i == pager.currentPage) Color.White
                                else Color.White.copy(alpha = 0.5f)
                            ),
                    )
                }
            }
        }

        RatingRow(rating, reviewCount, Modifier.padding(top = 8.dp))
        Text(title, style = AirbnbText.CardTitle, color = AirbnbColors.Hof,
            maxLines = 1, overflow = TextOverflow.Ellipsis, modifier = Modifier.padding(top = 2.dp))
        Text(host, style = AirbnbText.Meta, color = AirbnbColors.Foggy,
            maxLines = 1, overflow = TextOverflow.Ellipsis, modifier = Modifier.padding(top = 2.dp))
        Text(dates, style = AirbnbText.Meta, color = AirbnbColors.Foggy,
            modifier = Modifier.padding(top = 6.dp))
        Row(Modifier.padding(top = 4.dp), horizontalArrangement = Arrangement.spacedBy(4.dp)) {
            Text("$$pricePerNight", style = AirbnbText.PriceInline, color = AirbnbColors.Hof)
            Text("night", style = AirbnbText.PriceInline.copy(fontWeight = FontWeight.Normal),
                color = AirbnbColors.Hof)
        }
    }
}
```

### Search Pill (Explore top)

```kotlin
import androidx.compose.foundation.border
import androidx.compose.material.icons.filled.Search
import androidx.compose.material.icons.filled.Tune

@Composable
fun SearchPill(onClick: () -> Unit, modifier: Modifier = Modifier) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(56.dp)
            .shadow(12.dp, CircleShape, spotColor = Color.Black.copy(alpha = 0.08f))
            .clip(CircleShape) // full pill
            .background(AirbnbColors.Canvas)
            .border(0.5.dp, AirbnbColors.Divider, CircleShape)
            .clickable(onClick = onClick)
            .padding(horizontal = 20.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Icon(Icons.Filled.Search, contentDescription = null,
            tint = AirbnbColors.Ink, modifier = Modifier.size(16.dp))
        Column(Modifier.weight(1f)) {
            Text("Where to?", style = AirbnbText.CardTitle.copy(fontWeight = FontWeight.Bold),
                color = AirbnbColors.Ink)
            Text("Anywhere · Any week · Add guests", style = AirbnbText.Caption,
                color = AirbnbColors.Foggy)
        }
        Box(
            Modifier
                .size(36.dp)
                .clip(CircleShape)
                .border(1.dp, AirbnbColors.Divider, CircleShape),
            contentAlignment = Alignment.Center,
        ) {
            Icon(Icons.Filled.Tune, contentDescription = "Filters",
                tint = AirbnbColors.Ink, modifier = Modifier.size(14.dp))
        }
    }
}
```

### Category Bar (sliding underline — see §4 for the motion)

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.itemsIndexed
import androidx.compose.animation.animateColorAsState

@Composable
fun CategoryBar(
    categories: List<Pair<androidx.compose.ui.graphics.vector.ImageVector, String>>,
    selected: Int,
    onSelect: (Int) -> Unit,
) {
    LazyRow(
        modifier = Modifier
            .fillMaxWidth()
            .height(72.dp)
            .background(AirbnbColors.Canvas),
        contentPadding = PaddingValues(horizontal = 16.dp),
        horizontalArrangement = Arrangement.spacedBy(32.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        itemsIndexed(categories) { i, (icon, label) ->
            val isSel = i == selected
            val tint by animateColorAsState(
                if (isSel) AirbnbColors.Ink else Color(0xFF717171), label = "catTint")
            Column(
                horizontalAlignment = Alignment.CenterHorizontally,
                modifier = Modifier.clickable(
                    interactionSource = remember { MutableInteractionSource() },
                    indication = null,
                ) { onSelect(i) },
            ) {
                Icon(icon, contentDescription = label, tint = tint, modifier = Modifier.size(24.dp))
                Spacer(Modifier.height(6.dp))
                Text(label, style = AirbnbText.Chip, color = tint)
                Spacer(Modifier.height(8.dp))
                Box(
                    Modifier
                        .height(2.dp)
                        .width(if (isSel) 28.dp else 0.dp)
                        .background(AirbnbColors.Ink),
                )
            }
        }
    }
}
```

### Sticky Booking Footer (Stay Detail bottom)

iOS uses `.regularMaterial` blur; Android has no first-class live blur, so use a 95%-opaque canvas `Surface` with a hairline top divider.

```kotlin
import androidx.compose.material3.Surface
import androidx.compose.ui.text.style.TextDecoration

@Composable
fun BookingFooter(
    totalPrice: Int,
    dateRange: String,
    onReserve: () -> Unit,
) {
    val haptics = LocalHapticFeedback.current
    Surface(color = AirbnbColors.Canvas.copy(alpha = 0.95f), tonalElevation = 0.dp) {
        Column {
            Box(Modifier.fillMaxWidth().height(0.5.dp).background(AirbnbColors.Divider))
            Row(
                modifier = Modifier
                    .fillMaxWidth()
                    .height(80.dp)
                    .padding(horizontal = 24.dp),
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Column(Modifier.weight(1f)) {
                    Row(verticalAlignment = Alignment.Bottom,
                        horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                        Text("$$totalPrice", style = AirbnbText.Subsection.copy(fontSize = 16.sp),
                            color = AirbnbColors.Ink)
                        Text("total", style = AirbnbText.BodySmall, color = AirbnbColors.Ink)
                    }
                    Text(dateRange, style = AirbnbText.BodySmall, color = AirbnbColors.Ink,
                        textDecoration = TextDecoration.Underline)
                }
                ReservePill(text = "Reserve") {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS .medium impact
                    onReserve()
                }
            }
        }
    }
}

@Composable
fun ReservePill(text: String, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "reserveScale")
    Box(
        modifier = Modifier
            .scale(scale)
            .heightIn(min = 48.dp)
            .clip(RoundedCornerShape(8.dp)) // soft rectangle, NOT a full pill
            .background(if (pressed) AirbnbColors.CoralPressed else AirbnbColors.Coral)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 28.dp, vertical = 14.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = AirbnbText.Button, color = Color.White)
    }
}
```

### Map Price Bubble

```kotlin
enum class BubbleState { Default, Visited, Selected }

@Composable
fun MapPriceBubble(price: Int, state: BubbleState = BubbleState.Default) {
    val bg = when (state) {
        BubbleState.Default  -> Color.White
        BubbleState.Visited  -> AirbnbColors.Ink
        BubbleState.Selected -> AirbnbColors.Coral
    }
    val fg = if (state == BubbleState.Default) AirbnbColors.Ink else Color.White
    Box(
        Modifier
            .shadow(6.dp, RoundedCornerShape(12.dp), spotColor = Color.Black.copy(alpha = 0.2f))
            .clip(RoundedCornerShape(12.dp)) // rounded rect, NOT a full pill
            .background(bg)
            .padding(horizontal = 10.dp, vertical = 6.dp),
    ) {
        Text("$$price", style = AirbnbText.PriceInline.copy(fontWeight = FontWeight.Bold), color = fg)
    }
}
```

## 4. Category Underline — Shared-Element Slide

Airbnb's signature interaction is the category-bar underline that slides to the tapped chip (iOS `matchedGeometryEffect`). The §3 `CategoryBar` animates width per chip; for a true single underline that travels across chips, drive an `Animatable` offset with a `spring`, mirroring DESIGN.md §6's "underline slides … 250ms spring".

```kotlin
import androidx.compose.foundation.layout.offset
import androidx.compose.ui.unit.Dp

@Composable
fun SlidingUnderline(
    chipCount: Int,
    selected: Int,
    chipWidth: Dp,
    chipSpacing: Dp = 32.dp,
) {
    val targetX = (chipWidth + chipSpacing) * selected
    val x by animateDpAsState(
        targetValue = targetX,
        animationSpec = spring(dampingRatio = 0.8f, stiffness = 400f), // ~250ms feel
        label = "underline",
    )
    Box(
        Modifier
            .offset(x = x)
            .width(chipWidth)
            .height(2.dp)
            .background(AirbnbColors.Ink),
    )
}
```

The **search-pill → modal** morph is Compose's `SharedTransitionLayout` + `Modifier.sharedElement()` (Compose 1.7+) on the pill container; fall back to `AnimatedVisibility` with `slideInVertically` + a 400ms cross-fade if you target older BOMs. The **Belo logomark** loading pulse is a `rememberInfiniteTransition` driving alpha `0.6f → 1f → 0.6f` over 1200ms, tinted `AirbnbColors.Rausch` (never Coral).

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Airbnb's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 92%-opaque canvas surface. **Active tint is Primary Coral** (icon + label), inactive is `#717171`. All five tabs are always labeled.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun AirbnbBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = AirbnbColors.Canvas.copy(alpha = 0.92f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Explore"   to Icons.Filled.Search,
            "Wishlists" to Icons.Filled.Favorite,
            "Trips"     to Icons.Filled.Flight,
            "Inbox"     to Icons.Filled.ChatBubbleOutline,
            "Profile"   to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = AirbnbText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = AirbnbColors.Coral,
                    selectedTextColor = AirbnbColors.Coral,
                    unselectedIconColor = Color(0xFF717171),
                    unselectedTextColor = Color(0xFF717171),
                    indicatorColor = Color.Transparent, // no Material pill — Airbnb has none
                ),
            )
        }
    }
}
```

The collapsing **large-title nav bar** (`#222222` Cereal 32sp ExtraBold over a metadata strip) is best built with a `LargeTopAppBar` + `TopAppBarDefaults.exitUntilCollapsedScrollBehavior()`, with the `CategoryBar` pinned directly beneath it inside the `Scaffold` content.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Save heart tap | `Animatable` 1 → 1.25 → 1 spring (`dampingRatio = 0.6f`); `HapticFeedbackType.TextHandleMove` (~iOS `.soft`) |
| Photo carousel swipe | `HorizontalPager` 60fps paging, rubber-band at edges; dot indicators cross-fade |
| Category bar switch | `animateDpAsState` underline offset, `spring(dampingRatio = 0.8f)` ≈ 250ms |
| Search pill → modal | `SharedTransitionLayout` + `Modifier.sharedElement()` (1.7+); fallback `slideInVertically` + 400ms cross-fade |
| Reserve tap | `animateFloatAsState` 1 → 0.98; `HapticFeedbackType.LongPress` (~`.medium`); success → see below |
| Map pin select | scale 1 → 1.1 + color `White` → `Coral` via `animateColorAsState`; card slides up from bottom |
| Belo loading | `rememberInfiniteTransition` alpha `0.6 → 1 → 0.6` over 1200ms, Rausch |

```kotlin
// Save-heart bounce — exact 1 → 1.25 → 1 spring (damping 0.6)
@Composable
fun rememberHeartBounce(isSaved: Boolean): Float {
    val scale = remember { Animatable(1f) }
    LaunchedEffect(isSaved) {
        if (isSaved) {
            scale.animateTo(1.25f, spring(dampingRatio = 0.6f, stiffness = 500f))
            scale.animateTo(1f,    spring(dampingRatio = 0.6f, stiffness = 500f))
        }
    }
    return scale.value
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's `.soft` impact on the save heart, and `.medium` on Reserve. Booking-confirmed → a double-buzz `VibrationEffect.createWaveform(longArrayOf(0, 40, 60, 40), -1)` mirrors iOS `.notificationOccurred(.success)`.

## 7. Icons

Airbnb ships custom category glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. Export Airbnb's bespoke category icons and the Belo logomark as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Save (unsaved) | `heart` | `Icons.Outlined.FavoriteBorder` |
| Save (saved) | `heart.fill` | `Icons.Filled.Favorite` |
| Rating star | `star.fill` | `Icons.Filled.Star` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Filters | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Show all photos | `square.grid.2x2.fill` | `Icons.Filled.GridView` |
| Superhost | `checkmark.seal.fill` (Babu) | `Icons.Filled.Verified` |
| Explore tab | `magnifyingglass` | `Icons.Filled.Search` |
| Wishlists tab | `heart` / `heart.fill` | `Icons.Filled.Favorite` |
| Trips tab | `airplane` | `Icons.Filled.Flight` |
| Inbox tab | `message` / `message.fill` | `Icons.Filled.ChatBubbleOutline` |
| Profile tab | `person.circle` | `Icons.Filled.AccountCircle` |
| Wifi amenity | `wifi` | `Icons.Filled.Wifi` |
| TV amenity | `tv.fill` | `Icons.Filled.Tv` |
| Parking amenity | `parkingsign.circle` | `Icons.Filled.LocalParking` |
| Back (detail) | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBackIos` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Belo logomark | — | custom vector drawable, Rausch `#FF5A5F` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `HorizontalPager` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the white canvas wants `WindowCompat` dark-content (dark icons) system bars. Apply `Scaffold` insets / `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the sticky Reserve footer clears the gesture nav and the large title clears the status bar.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on stay-card titles, host bylines, dates, body copy, and large nav titles. Pin layout-sensitive text (10sp tab labels, 12sp chip labels, the map price bubble, rating numbers) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. Stay-card title scales but clamp at ~18sp.
- **TalkBack**: merge the whole stay card into one node with `Modifier.semantics(mergeDescendants = true) { contentDescription = "Private room in Reykjavik, hosted by Sigrún, Superhost, 4.92 stars, 324 reviews, Oct 12 to 17, \$214 per night" }`; expose the save heart as a *separate* button node (`"Save"` / `"Saved"`).
- **Touch targets**: Material guidance is 48.dp minimum. The save heart already uses a 44.dp box — bump to 48.dp via padding. The Reserve pill is `heightIn(min = 48.dp)`; category chips fill the 72.dp bar height.
- **Contrast**: Hof (`#484848`) on white passes WCAG AA at all sizes. Foggy (`#767676`) on white passes AA only at ≥18sp Bold or ≥14sp Medium — do not use Foggy for small body text; for the 14sp Book host byline this is borderline, nudge toward `#6A6A6A` if targeting strict AA.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, skip the heart 1.25 bounce and the underline slide — fall back to an instant color/position change.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Airbnb's brand requires the fixed white canvas and the specific `#FF385C` coral regardless of the user's wallpaper. Photography is the color story, not the system accent.
