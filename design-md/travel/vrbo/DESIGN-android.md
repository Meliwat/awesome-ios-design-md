# Vrbo (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Vrbo's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the signature photo gallery, the sticky booking bar, listing cards, map price pins, the bottom bar, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Vrbo's white canvas, the whole-home framing, full-bleed swipeable galleries, the sticky price-forward booking bar, the single gold review star) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `HorizontalPager` for the gallery, `ModalBottomSheet` for the date picker, Maps Compose for pins, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for photos, Maps Compose for the map. Vrbo has a fixed brand palette (no Material You extraction). It is light-mode-first; a full dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/VrboColors.kt
import androidx.compose.ui.graphics.Color

object VrboColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF3F5F8)
    val SurfacePressed = Color(0xFFE7EBF1)
    val Divider        = Color(0xFFE1E5EB)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF101317) // cool blue-charcoal — NOT pure black
    val DarkSurface1 = Color(0xFF181C22)
    val DarkSurface2 = Color(0xFF21262E)
    val DarkDivider  = Color(0xFF2A3038)

    // Text
    val TextPrimary       = Color(0xFF15181D) // near-black, NOT pure black
    val TextSecondary     = Color(0xFF5A6470)
    val TextTertiary      = Color(0xFF8A93A0)
    val DarkTextPrimary   = Color(0xFFEDEFF2)
    val DarkTextSecondary = Color(0xFFA3AAB4)

    // Brand
    val Blue        = Color(0xFF245ABC)
    val BluePressed = Color(0xFF1B4794)
    val BlueBright  = Color(0xFF4F8BF0) // dark-mode links/pins/outline
    val SkyBlue     = Color(0xFF1D6FB8)

    // Accent & semantic
    val GoldStar = Color(0xFFF2B01E) // the only warm color — constant across schemes
    val Success  = Color(0xFF1F9D57)
    val Error    = Color(0xFFE04444)
    val Warning  = Color(0xFFE8920C)
}
```

Wire it into both schemes. Vrbo is light-first (bright white); the dark scheme uses `#101317` blue-charcoal, never true black. Links/pins/outline brighten to `#4F8BF0` on dark; the "Book now" fill stays the deep brand blue.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val VrboLight = lightColorScheme(
    primary        = VrboColors.Blue,
    onPrimary      = Color.White,
    background      = VrboColors.Canvas,
    onBackground   = VrboColors.TextPrimary,
    surface        = VrboColors.SurfaceGray,
    onSurface      = VrboColors.TextPrimary,
    surfaceVariant = VrboColors.SurfacePressed,
    outline        = VrboColors.Divider,
    error          = VrboColors.Error,
)

private val VrboDark = darkColorScheme(
    primary        = VrboColors.Blue,      // "Book now" fill stays deep brand blue
    onPrimary      = Color.White,
    background      = VrboColors.DarkCanvas,
    onBackground   = VrboColors.DarkTextPrimary,
    surface        = VrboColors.DarkSurface1,
    onSurface      = VrboColors.DarkTextPrimary,
    surfaceVariant = VrboColors.DarkSurface2,
    outline        = VrboColors.DarkDivider,
    error          = VrboColors.Error,
)

@Composable
fun VrboTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) VrboDark else VrboLight,
    typography  = VrboTypography,
    content     = content,
)

// Links / pins / outline use BlueBright on dark; helper:
@Composable
fun vrboAccent(dark: Boolean = isSystemInDarkTheme()) =
    if (dark) VrboColors.BlueBright else VrboColors.Blue
```

## 2. Typography

Vrbo ships **no custom typeface** — it uses the platform system font with the OS font-scale. Price is one of the heaviest styles on every screen.

```kotlin
// ui/theme/VrboType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

private val Sys = FontFamily.Default // system font — no bundled face

object VrboText {
    val LargeTitle   = TextStyle(Sys, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val PriceHero    = TextStyle(Sys, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val ListingTitle = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 21.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Section      = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Body         = TextStyle(Sys, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val CardTitle    = TextStyle(Sys, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val FactValue    = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 19.sp)
    val Meta         = TextStyle(Sys, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Badge        = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.2.sp)
    val Button       = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab          = TextStyle(Sys, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Caption      = TextStyle(Sys, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 15.sp)
}

val VrboTypography = Typography(
    headlineLarge  = VrboText.LargeTitle,
    headlineMedium = VrboText.PriceHero,
    titleMedium    = VrboText.ListingTitle,
    bodyMedium     = VrboText.Body,
    labelSmall     = VrboText.Tab,
)
```

## 3. Signature Components

### Photo Gallery (the core component)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.pager.HorizontalPager
import androidx.compose.foundation.pager.rememberPagerState
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.material3.IconButton
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun PhotoGallery(
    photos: List<String>,
    saved: Boolean,
    onBack: () -> Unit,
    onShare: () -> Unit,
    onToggleSave: () -> Unit,
) {
    val pager = rememberPagerState(pageCount = { photos.size })
    Box(Modifier.fillMaxWidth().height(290.dp)) {
        HorizontalPager(state = pager, modifier = Modifier.fillMaxSize()) { page ->
            AsyncImage(model = photos[page], contentDescription = "Photo ${page + 1} of ${photos.size}",
                modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
        }
        Box(
            Modifier.align(Alignment.BottomCenter).fillMaxWidth().height(145.dp)
                .background(Brush.verticalGradient(listOf(Color.Transparent, VrboColors.DarkCanvas.copy(alpha = 0.55f))))
        )
        Row(
            Modifier.align(Alignment.TopCenter).fillMaxWidth().padding(horizontal = 16.dp).padding(top = 54.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            GalleryButton(Icons.Filled.ArrowBack, onBack)
            Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {
                GalleryButton(Icons.Filled.IosShare, onShare)
                GalleryButton(if (saved) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder,
                    onToggleSave, tint = if (saved) VrboColors.Error else Color.White)
            }
        }
        Row(
            Modifier.align(Alignment.BottomCenter).padding(bottom = 14.dp),
            horizontalArrangement = Arrangement.spacedBy(5.dp),
        ) {
            repeat(photos.size) { i ->
                Box(Modifier
                    .height(6.dp).width(if (i == pager.currentPage) 18.dp else 6.dp)
                    .clip(CircleShape)
                    .background(if (i == pager.currentPage) Color.White else Color.White.copy(alpha = 0.45f)))
            }
        }
        Row(
            Modifier.align(Alignment.BottomEnd).padding(end = 16.dp, bottom = 14.dp)
                .clip(CircleShape).background(VrboColors.DarkCanvas.copy(alpha = 0.7f))
                .padding(horizontal = 11.dp, vertical = 5.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Icon(Icons.Filled.PhotoLibrary, null, tint = Color.White, modifier = Modifier.size(12.dp))
            Text("${pager.currentPage + 1} / ${photos.size}", style = VrboText.Caption, color = Color.White)
        }
    }
}

@Composable
private fun GalleryButton(icon: androidx.compose.ui.graphics.vector.ImageVector, onClick: () -> Unit, tint: Color = Color.White) {
    IconButton(
        onClick = onClick,
        modifier = Modifier.size(36.dp).clip(CircleShape).background(VrboColors.DarkCanvas.copy(alpha = 0.55f)),
    ) { Icon(icon, contentDescription = null, tint = tint, modifier = Modifier.size(18.dp)) }
}
```

### Sticky Booking Bar

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.style.TextDecoration

@Composable
fun BookingBar(price: Int, dateRange: String, onDates: () -> Unit, onBook: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier
            .fillMaxWidth()
            .height(76.dp)
            .background(VrboColors.DarkCanvas.copy(alpha = 0.97f))
            .padding(horizontal = 18.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.SpaceBetween,
    ) {
        Column {
            Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Text("$$price", style = VrboText.PriceHero, color = VrboColors.DarkTextPrimary)
                Text("/ night", fontSize = 13.sp, color = VrboColors.DarkTextSecondary, modifier = Modifier.padding(bottom = 3.dp))
            }
            Text(dateRange, fontSize = 12.sp, color = VrboColors.DarkTextSecondary,
                textDecoration = TextDecoration.Underline,
                modifier = Modifier.clickable(onClick = onDates))
        }
        Button(
            onClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onBook() },
            interactionSource = interaction,
            shape = RoundedCornerShape(10.dp),
            colors = ButtonDefaults.buttonColors(
                containerColor = if (pressed) VrboColors.BluePressed else VrboColors.Blue,
                contentColor = Color.White,
            ),
            contentPadding = PaddingValues(horizontal = 26.dp, vertical = 13.dp),
        ) { Text("Book now", style = VrboText.Button) }
    }
}
```

### Listing Card (results)

```kotlin
import androidx.compose.material.icons.filled.Star

@Composable
fun ListingCard(
    imageUrl: String, title: String, location: String,
    rating: Double, reviews: Int, price: Int,
    saved: Boolean, onToggleSave: () -> Unit,
) {
    Column(verticalArrangement = Arrangement.spacedBy(8.dp)) {
        Box {
            AsyncImage(
                model = imageUrl, contentDescription = title,
                modifier = Modifier.fillMaxWidth().aspectRatio(4f / 3f).clip(RoundedCornerShape(14.dp)),
                contentScale = ContentScale.Crop,
            )
            IconButton(
                onClick = onToggleSave,
                modifier = Modifier.align(Alignment.TopEnd).padding(10.dp)
                    .size(36.dp).clip(CircleShape).background(VrboColors.DarkCanvas.copy(alpha = 0.55f)),
            ) {
                Icon(if (saved) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder, "Save",
                    tint = if (saved) VrboColors.Error else Color.White, modifier = Modifier.size(17.dp))
            }
        }
        Text("Entire home", style = VrboText.Caption, color = VrboColors.DarkTextSecondary)
        Text(title, style = VrboText.CardTitle, color = VrboColors.DarkTextPrimary)
        Text(location, style = VrboText.Meta, color = VrboColors.DarkTextSecondary)
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Icon(Icons.Filled.Star, null, tint = VrboColors.GoldStar, modifier = Modifier.size(12.dp))
            Text("%.1f".format(rating), fontSize = 14.sp, fontWeight = FontWeight.Bold, color = VrboColors.DarkTextPrimary)
            Text("· $reviews", style = VrboText.Meta, color = VrboColors.DarkTextSecondary)
        }
        Row(verticalAlignment = Alignment.Bottom) {
            Text("$$price", fontSize = 16.sp, fontWeight = FontWeight.Bold, color = VrboColors.DarkTextPrimary)
            Text(" night", fontSize = 14.sp, color = VrboColors.DarkTextPrimary)
        }
    }
}
```

### Map Price Pin + Trust Badge

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.border
import androidx.compose.ui.draw.scale

enum class PinState { Default, Viewed, Selected }

@Composable
fun MapPricePin(price: Int, state: PinState) {
    val scale by animateFloatAsState(if (state == PinState.Selected) 1.12f else 1f, tween(180), label = "pin")
    val (bg, fg) = when (state) {
        PinState.Default  -> VrboColors.Blue to Color.White
        PinState.Viewed   -> VrboColors.DarkSurface2 to VrboColors.DarkTextSecondary
        PinState.Selected -> VrboColors.GoldStar to Color(0xFF1A1206)
    }
    Box(
        Modifier
            .scale(scale)
            .clip(CircleShape)
            .background(bg)
            .then(if (state == PinState.Viewed) Modifier.border(1.dp, VrboColors.DarkDivider, CircleShape) else Modifier)
            .padding(horizontal = 12.dp, vertical = 7.dp),
    ) { Text("$$price", fontSize = 14.sp, fontWeight = FontWeight.Bold, color = fg) }
}

@Composable
fun TrustBadge(premier: Boolean) {
    val accent = if (premier) VrboColors.BlueBright else VrboColors.Success
    Box(
        Modifier
            .clip(RoundedCornerShape(6.dp))
            .background(accent.copy(alpha = 0.16f))
            .border(1.dp, accent.copy(alpha = 0.35f), RoundedCornerShape(6.dp))
            .padding(horizontal = 9.dp, vertical = 4.dp),
    ) { Text(if (premier) "Premier Host" else "Instant Book", style = VrboText.Badge, color = accent) }
}
```

### Trip Board Card

```kotlin
@Composable
fun TripBoardCard(thumb: String, name: String, facts: String, rating: Double, tripName: String) {
    Row(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(12.dp))
            .background(VrboColors.DarkSurface2)
            .border(1.dp, VrboColors.DarkDivider, RoundedCornerShape(12.dp))
            .padding(12.dp),
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        AsyncImage(model = thumb, contentDescription = name,
            modifier = Modifier.size(64.dp).clip(RoundedCornerShape(8.dp)), contentScale = ContentScale.Crop)
        Column(Modifier.weight(1f)) {
            Text(name, fontSize = 14.sp, fontWeight = FontWeight.Bold, color = VrboColors.DarkTextPrimary)
            Text(facts, fontSize = 12.sp, color = VrboColors.DarkTextSecondary, modifier = Modifier.padding(top = 3.dp))
            Text("★ %.1f · Saved to \"%s\"".format(rating, tripName),
                fontSize = 11.sp, fontWeight = FontWeight.Bold, color = VrboColors.GoldStar,
                modifier = Modifier.padding(top = 8.dp))
        }
    }
}
```

## 4. Bottom Navigation Bar

There is no Material tint pill — active is just the brand blue.

```kotlin
import androidx.compose.material3.*

@Composable
fun VrboBottomBar(selected: Int, onSelect: (Int) -> Unit, hasUnread: Boolean = false) {
    NavigationBar(containerColor = VrboColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Search"        to Icons.Filled.Search,
            "Trips"         to Icons.Filled.Work,
            "Inbox"         to Icons.Filled.ChatBubbleOutline,
            "Notifications" to Icons.Filled.Notifications,
            "Account"       to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (i == 2 && hasUnread) {
                        BadgedBox(badge = { Badge(containerColor = VrboColors.Error) }) {
                            Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp))
                        }
                    } else Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp))
                },
                label = { Text(label, style = VrboText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = VrboColors.BlueBright,
                    selectedTextColor = VrboColors.BlueBright,
                    unselectedIconColor = VrboColors.TextTertiary,
                    unselectedTextColor = VrboColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Vrbo has none
                ),
            )
        }
    }
}
```

## 5. Navigation

Vrbo's loop is Search → results list/map → listing detail → trip board. Model it with Navigation-Compose / Nav3: a 300ms slide push between screens; the bottom bar persists on the five primary tabs and is hidden on the listing detail (which has its own gallery overlay nav + sticky booking bar). The date/guest picker and filters are `ModalBottomSheet`s. The map is a Maps-Compose `GoogleMap` with custom `MarkerComposable` price pins and a `ModalBottomSheet` (or anchored card) peek on pin tap.

## 6. Motion

Vrbo motion is calm — 180–300ms ease-out/spring. Photography and price carry the weight, not animation.

| Moment | Compose recipe |
|--------|----------------|
| Gallery progress dot | dot width `animateDpAsState(if (current) 18.dp else 6.dp, tween(200))` |
| Booking-bar price update | `AnimatedContent(price, transitionSpec = { fadeIn() togetherWith fadeOut() })` over 250ms |
| Save heart pop | `animateFloatAsState` 1f → 1.25f → 1f via keyframes `tween(280)` + soft haptic |
| Map pin select | scale `animateFloatAsState(1.12f, tween(180))` + recolor; peek card `ModalBottomSheet` slide-up |
| Results card stagger | `AnimatedVisibility` `fadeIn(tween(220)) + slideInVertically { it / 6 }`, 60ms delay per item |
| Page navigation | Nav3/`NavHost` slide push `tween(300)` |
| Sheet present | `ModalBottomSheet` default slide-up; scrim fades 250ms |

```kotlin
// Booking-bar price — the canonical Vrbo motion
AnimatedContent(targetState = price, label = "price",
    transitionSpec = { fadeIn(tween(130)) togetherWith fadeOut(tween(120)) }) { p ->
    Text("$$p", style = VrboText.PriceHero, color = VrboColors.DarkTextPrimary)
}
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on "Book now" and save; `HapticFeedbackType.TextHandleMove` (or `HapticFeedbackConstants.CLOCK_TICK`) for date-range endpoints and tab changes.

## 7. Icons

Use `androidx.compose.material:material-icons-extended`. Map price pins and progress dots are drawn with `Box`+`CircleShape`; the gold star uses `Icons.Filled.Star`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Trips (tab) | `suitcase` | `Icons.Filled.Work` |
| Inbox (tab) | `bubble.left` | `Icons.Filled.ChatBubbleOutline` |
| Notifications (tab) | `bell` | `Icons.Filled.Notifications` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Back (gallery) | `chevron.left` | `Icons.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Save | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Favorite` |
| Photo grid / counter | `photo.on.rectangle` | `Icons.Filled.PhotoLibrary` |
| Rating star | `star.fill` | `Icons.Filled.Star` |
| Guests | `person.2` | `Icons.Filled.Group` |
| Bedrooms | `bed.double` | `Icons.Filled.KingBed` |
| Bathrooms | `shower` | `Icons.Filled.Bathtub` |
| Whole home | `house` | `Icons.Filled.Home` |
| Filters | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Map toggle | `map` | `Icons.Filled.Map` |
| Calendar / dates | `calendar` | `Icons.Filled.CalendarMonth` |
| Location | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Instant Book | `bolt.fill` | `Icons.Filled.Bolt` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `HorizontalPager` + Maps Compose are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the gallery extends under the status bar with white overlay controls (use light-content system bars while a gallery is visible). The booking bar must sit above the IME-free bottom inset; respect the navigation bar inset.
- **Font scaling**: `sp` honors the user's font scale — keep it on titles, price, listing title, headers, body. Pin layout-sensitive text (10sp tab labels, trust badges, the photo counter, progress-dot legends) via a fixed-density wrapper: `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **No bundled font**: use `FontFamily.Default` (system) everywhere. Do not ship a custom typeface.
- **TalkBack**: gallery exposes "Photo {n} of {total}" via `contentDescription`; announce the booking bar "Price {amount} per night, {dates}"; cards "{title}, entire home, {rating} stars, {reviews} reviews, {price} per night"; trust badges carry text labels (not color alone).
- **Touch targets**: Material guidance is 48.dp. Give the 36.dp gallery overlay buttons and the 36.dp save heart a ≥ 48.dp hit area; map price pins are the tap target (≥ 48.dp effective); the "Book now" button is full-width and ≥ 48.dp tall; list cards are fully tappable.
- **Contrast**: `#15181D` on `#FFFFFF` and `#EDEFF2` on `#101317` pass WCAG AA. The gold star is paired with a numeric rating; trust badges include text — color is never the sole channel.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the save-heart pop and pin scale; cross-dissolve the price (no count); keep gallery paging but drop the dot-width animation.
- **Dark mode**: invert via the `Dark*` palette — `#101317`, NOT pure black; `#15181D` text becomes `#EDEFF2`; links/pins/outline brighten to `#4F8BF0` while the "Book now" fill stays `#245ABC`; the gold star is constant. Shadows nearly vanish on dark, so floating panels (peek card, menus) add a 1dp `DarkDivider` border. Do **not** enable Material You `dynamicColorScheme()`: Vrbo's white + blue + single-gold-star identity must hold regardless of wallpaper.
- **Photography**: gallery images are full-bleed and never carry a shadow; preserve aspect ratio and supply per-photo `contentDescription` for TalkBack.
