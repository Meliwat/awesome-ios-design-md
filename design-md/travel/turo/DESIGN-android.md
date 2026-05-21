# Turo (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Turo's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the signature photo hero, host card, and sticky Book bar, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps Turo's *visual identity* (the full-bleed car photo as the product, Turo Purple as the booking action, Turo Teal as the trust accent, the host card, the pinned Book bar, the near-black canvas) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `HorizontalPager` for the carousel, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for car photos and host avatars, and `androidx.compose.foundation.pager`. No color extraction — Turo's palette is the fixed purple/teal brand. The dark scheme is the primary catalog rendering; a full light scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/TuroColors.kt
import androidx.compose.ui.graphics.Color

object TuroColors {
    // Brand
    val Purple        = Color(0xFF593BFB) // booking action
    val PurpleBright  = Color(0xFF7C5CFF) // link / gradient hi
    val PurplePressed = Color(0xFF4A2FD6)
    val Teal          = Color(0xFF5CE0B8) // trust / value
    val TealDeep      = Color(0xFF1FB890)

    // Canvas & Surfaces (Light)
    val Canvas   = Color(0xFFFFFFFF)
    val Surface1 = Color(0xFFF6F5FA)
    val Surface2 = Color(0xFFFFFFFF)
    val Pressed  = Color(0xFFECEAF4)
    val Divider  = Color(0xFFE6E4EF)

    // Canvas & Surfaces (Dark) — near-black so photos pop
    val DarkCanvas   = Color(0xFF0F0F12)
    val DarkSurface1 = Color(0xFF1A1A1F)
    val DarkSurface2 = Color(0xFF25252C)
    val DarkDivider  = Color(0xFF2D2D35)

    // Text
    val TextPrimary       = Color(0xFF15131F)
    val TextSecondary     = Color(0xFF6B6878)
    val TextTertiary      = Color(0xFF9C99AB)
    val DarkTextPrimary   = Color(0xFFF2F1F6)
    val DarkTextSecondary = Color(0xFFA4A2B3)
    val DarkTextTertiary  = Color(0xFF6F6D7E)
    val OnPurple = Color(0xFFFFFFFF)
    val OnTeal   = Color(0xFF06231B)

    // Semantic
    val Success     = Color(0xFF1FB890)
    val SuccessDark = Color(0xFF5CE0B8)
    val Error       = Color(0xFFF5536B)
    val Star        = Color(0xFFFFB400)
}
```

Wire it into both schemes. Turo's dark canvas is near-black `#0F0F12` — never a tinted gray — so photography pops.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val TuroLight = lightColorScheme(
    primary        = TuroColors.Purple,        // booking action
    onPrimary      = TuroColors.OnPurple,
    secondary      = TuroColors.Teal,
    onSecondary    = TuroColors.OnTeal,
    background      = TuroColors.Canvas,
    onBackground   = TuroColors.TextPrimary,
    surface         = TuroColors.Surface2,
    onSurface      = TuroColors.TextPrimary,
    surfaceVariant = TuroColors.Surface1,
    outline         = TuroColors.Divider,
    error           = TuroColors.Error,
)

private val TuroDark = darkColorScheme(
    primary        = TuroColors.Purple,        // identical across themes
    onPrimary      = TuroColors.OnPurple,
    secondary      = TuroColors.Teal,
    onSecondary    = TuroColors.OnTeal,
    background      = TuroColors.DarkCanvas,
    onBackground   = TuroColors.DarkTextPrimary,
    surface         = TuroColors.DarkSurface1,
    onSurface      = TuroColors.DarkTextPrimary,
    surfaceVariant = TuroColors.DarkSurface2,
    outline         = TuroColors.DarkDivider,
    error           = TuroColors.Error,
)

@Composable
fun TuroTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) TuroDark else TuroLight,
    typography  = TuroTypography,
    content     = content,
)
```

## 2. Typography (M3)

Turo's brand face is a geometric humanist sans. License it and drop the TTFs in `res/font/`, or ship **Manrope** (SIL OFL) — keep the named ramp identical. Prices/counts are tabular (`fontFeatureSettings = "tnum"`).

```kotlin
// ui/theme/TuroType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val TuroSans = FontFamily(
    Font(R.font.turo_regular,   FontWeight.Normal),
    Font(R.font.turo_medium,    FontWeight.Medium),
    Font(R.font.turo_semibold,  FontWeight.SemiBold),
    Font(R.font.turo_bold,      FontWeight.Bold),
    Font(R.font.turo_extrabold, FontWeight.ExtraBold),
)

private const val TNUM = "tnum"

// Named ramp — mirrors DESIGN.md §3 (pt → sp 1:1)
object TuroText {
    val Display    = TextStyle(TuroSans, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val PriceHero  = TextStyle(TuroSans, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 29.sp, letterSpacing = (-0.3).sp, fontFeatureSettings = TNUM)
    val CarTitle   = TextStyle(TuroSans, fontWeight = FontWeight.ExtraBold, fontSize = 23.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val Section    = TextStyle(TuroSans, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 24.sp, letterSpacing = (-0.1).sp)
    val Body       = TextStyle(TuroSans, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val BodyEmph   = TextStyle(TuroSans, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 24.sp)
    val CardTitle  = TextStyle(TuroSans, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val CardPrice  = TextStyle(TuroSans, fontWeight = FontWeight.ExtraBold, fontSize = 14.sp, lineHeight = 17.sp, fontFeatureSettings = TNUM)
    val Meta       = TextStyle(TuroSans, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Eyebrow    = TextStyle(TuroSans, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.5.sp)
    val Caption    = TextStyle(TuroSans, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp)
    val Button     = TextStyle(TuroSans, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.1.sp)
    val Tab        = TextStyle(TuroSans, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val RatingNum  = TextStyle(TuroSans, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 13.sp, fontFeatureSettings = TNUM)
}

val TuroTypography = Typography(
    displayLarge  = TuroText.Display,
    headlineLarge = TuroText.CarTitle,
    titleMedium   = TuroText.Section,
    bodyLarge     = TuroText.Body,
    labelSmall    = TuroText.Tab,
)
```

## 3. Signature Components

### Photo Hero (full-bleed `HorizontalPager`)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.pager.HorizontalPager
import androidx.compose.foundation.pager.rememberPagerState
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.foundation.clickable
import coil.compose.AsyncImage

@Composable
fun CarPhotoHero(
    photos: List<String>,
    isFavorite: Boolean,
    onToggleFavorite: () -> Unit,
    onBack: () -> Unit,
    onShare: () -> Unit,
) {
    val pager = rememberPagerState { photos.size }
    Box(Modifier.fillMaxWidth().height(260.dp).background(TuroColors.DarkSurface2)) {
        HorizontalPager(state = pager, modifier = Modifier.fillMaxSize()) { page ->
            AsyncImage(
                model = photos[page], contentDescription = "Photo ${page + 1} of ${photos.size}",
                modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop,
            )
        }

        // Page dots — active grows to an 18dp pill
        Row(
            Modifier.align(Alignment.BottomCenter).padding(bottom = 14.dp),
            horizontalArrangement = Arrangement.spacedBy(5.dp),
        ) {
            repeat(photos.size) { i ->
                val w by animateDpAsState(if (i == pager.currentPage) 18.dp else 6.dp, label = "dot")
                Box(Modifier.size(width = w, height = 6.dp).clip(RoundedCornerShape(3.dp))
                    .background(if (i == pager.currentPage) Color.White else Color.White.copy(alpha = 0.45f)))
            }
        }

        // Floating controls
        Row(
            Modifier.align(Alignment.TopCenter).fillMaxWidth().padding(horizontal = 16.dp, vertical = 56.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            HeroBtn(Icons.Filled.ArrowBack, "Back", onBack)
            Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {
                HeroBtn(Icons.Filled.IosShare, "Share", onShare)
                HeroBtn(
                    if (isFavorite) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder,
                    "Favorite", onToggleFavorite,
                    tint = if (isFavorite) TuroColors.Teal else Color.White,
                )
            }
        }
    }
}

@Composable
private fun HeroBtn(icon: androidx.compose.ui.graphics.vector.ImageVector,
                    desc: String, onClick: () -> Unit, tint: Color = Color.White) {
    Box(
        Modifier.size(38.dp).clip(CircleShape).background(Color(0x8C0F0F12))
            .clickable { onClick() },
        contentAlignment = Alignment.Center,
    ) { Icon(icon, desc, tint = tint, modifier = Modifier.size(18.dp)) }
}
```

### Specs Strip

```kotlin
import androidx.compose.foundation.border
import androidx.compose.material3.Text

data class CarSpec(val icon: androidx.compose.ui.graphics.vector.ImageVector, val value: String, val label: String)

@Composable
fun SpecsStrip(specs: List<CarSpec>, modifier: Modifier = Modifier) {
    Row(modifier.padding(horizontal = 20.dp), horizontalArrangement = Arrangement.spacedBy(10.dp)) {
        specs.forEach { s ->
            Column(
                Modifier.weight(1f).clip(RoundedCornerShape(12.dp))
                    .background(TuroColors.DarkSurface1)
                    .border(1.dp, TuroColors.DarkDivider, RoundedCornerShape(12.dp))
                    .padding(vertical = 12.dp),
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.spacedBy(6.dp),
            ) {
                Icon(s.icon, null, tint = TuroColors.Teal, modifier = Modifier.size(18.dp))
                Text(s.value, style = TuroText.RatingNum, color = TuroColors.DarkTextPrimary)
                Text(s.label, style = TuroText.Caption.copy(fontSize = 10.sp), color = TuroColors.DarkTextSecondary)
            }
        }
    }
}
```

### Host Card

```kotlin
import androidx.compose.ui.graphics.Brush

@Composable
fun HostCard(
    initials: String, name: String, allStar: Boolean, subline: String,
    onClick: () -> Unit, modifier: Modifier = Modifier,
) {
    Row(
        modifier
            .padding(horizontal = 20.dp)
            .clip(RoundedCornerShape(14.dp))
            .background(TuroColors.DarkSurface1)
            .border(1.dp, TuroColors.DarkDivider, RoundedCornerShape(14.dp))
            .clickable { onClick() }
            .padding(14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(
            Modifier.size(46.dp).clip(CircleShape)
                .background(Brush.linearGradient(listOf(TuroColors.PurpleBright, TuroColors.Teal))),
            contentAlignment = Alignment.Center,
        ) { Text(initials, style = TuroText.CardTitle.copy(fontWeight = FontWeight.ExtraBold, fontSize = 17.sp), color = Color.White) }

        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(3.dp)) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Text(name, style = TuroText.CardTitle, color = TuroColors.DarkTextPrimary)
                if (allStar) {
                    Row(
                        Modifier.clip(RoundedCornerShape(50)).background(TuroColors.Teal.copy(alpha = 0.16f))
                            .padding(horizontal = 8.dp, vertical = 3.dp),
                        verticalAlignment = Alignment.CenterVertically,
                        horizontalArrangement = Arrangement.spacedBy(4.dp),
                    ) {
                        Icon(Icons.Filled.Star, null, tint = TuroColors.Teal, modifier = Modifier.size(9.dp))
                        Text("ALL-STAR HOST", style = TuroText.Caption.copy(fontWeight = FontWeight.ExtraBold, fontSize = 10.sp), color = TuroColors.Teal)
                    }
                }
            }
            Text(subline, style = TuroText.Caption, color = TuroColors.DarkTextSecondary)
        }
        Icon(Icons.Filled.ChevronRight, null, tint = TuroColors.DarkTextTertiary, modifier = Modifier.size(14.dp))
    }
}
```

### Sticky Price + Book Bar

```kotlin
@Composable
fun BookBar(
    dailyRate: String, estTotal: String,
    onTotalClick: () -> Unit, onContinue: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier
            .fillMaxWidth()
            .background(Color(0xF50F0F12)) // blurred-glass analog; Android has no live blur
            .drawBehind {
                drawLine(TuroColors.DarkDivider, androidx.compose.ui.geometry.Offset(0f, 0f),
                    androidx.compose.ui.geometry.Offset(size.width, 0f), 0.5.dp.toPx())
            }
            .navigationBarsPadding()
            .padding(horizontal = 20.dp, vertical = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.SpaceBetween,
    ) {
        Column(verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(dailyRate, style = TuroText.PriceHero.copy(fontSize = 20.sp), color = TuroColors.DarkTextPrimary)
                Text("/ day", style = TuroText.Caption.copy(fontSize = 13.sp), color = TuroColors.DarkTextSecondary)
            }
            Text(estTotal, style = TuroText.Caption.copy(fontSize = 11.sp,
                textDecoration = androidx.compose.ui.text.style.TextDecoration.Underline),
                color = TuroColors.Teal, modifier = Modifier.clickable { onTotalClick() })
        }
        androidx.compose.material3.Button(
            onClick = onContinue,
            shape = RoundedCornerShape(10.dp),
            colors = ButtonDefaults.buttonColors(containerColor = TuroColors.Purple, contentColor = TuroColors.OnPurple),
            modifier = Modifier.height(44.dp),
        ) { Text("Continue", style = TuroText.Button) }
    }
}
```

### Map Price Pin

```kotlin
enum class PinState { Default, Visited, Selected }

@Composable
fun MapPricePin(price: String, state: PinState = PinState.Default, modifier: Modifier = Modifier) {
    val bg = when (state) { PinState.Default -> TuroColors.Purple; PinState.Selected -> Color.White; PinState.Visited -> TuroColors.DarkSurface2 }
    val fg = when (state) { PinState.Default -> Color.White; PinState.Selected -> TuroColors.Purple; PinState.Visited -> TuroColors.DarkTextSecondary }
    Box(
        modifier
            .shadow(8.dp, RoundedCornerShape(50), spotColor = Color.Black.copy(alpha = 0.5f))
            .clip(RoundedCornerShape(50))
            .background(bg)
            .then(if (state == PinState.Visited) Modifier.border(1.dp, TuroColors.DarkDivider, RoundedCornerShape(50)) else Modifier)
            .padding(horizontal = 12.dp, vertical = 7.dp),
    ) { Text(price, style = TuroText.RatingNum.copy(fontWeight = FontWeight.ExtraBold), color = fg) }
}
```

### Primary Button

```kotlin
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.interaction.MutableInteractionSource

@Composable
fun TuroPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Button(
        onClick = onClick,
        interactionSource = interaction,
        shape = RoundedCornerShape(10.dp),
        colors = ButtonDefaults.buttonColors(
            containerColor = if (pressed) TuroColors.PurplePressed else TuroColors.Purple,
            contentColor = TuroColors.OnPurple,
        ),
        modifier = modifier.fillMaxWidth().height(52.dp),
    ) { Text(title, style = TuroText.Button) }
}
```

## 4. Navigation

Turo has a 5-tab bottom strip; active state is a Turo Purple tint with **no Material pill** (Turo has none).

```kotlin
@Composable
fun TuroBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = TuroColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Search" to Icons.Filled.Search,
            "Trips" to Icons.Filled.DirectionsCar,
            "Favorites" to Icons.Filled.FavoriteBorder,
            "Inbox" to Icons.Filled.ChatBubbleOutline,
            "More" to Icons.Filled.Menu,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, Modifier.size(22.dp)) },
                label = { Text(label, style = TuroText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = TuroColors.Purple,    // Turo Purple active
                    selectedTextColor = TuroColors.Purple,
                    unselectedIconColor = TuroColors.DarkTextTertiary,
                    unselectedTextColor = TuroColors.DarkTextTertiary,
                    indicatorColor = Color.Transparent,        // no pill — Turo has none
                ),
            )
        }
    }
}
```

The top bar over the photo hero is just the floating circular controls (no app bar); a non-photo screen uses a transparent `CenterAlignedTopAppBar` over the canvas. The price-breakdown, filters, and date picker are `ModalBottomSheet`s; the listing-open card→hero transition uses a shared-element/`AnimatedContent` bounds transform.

## 5. Motion

Turo motion is calm — the photo carries the richness. The favorite pop and the card→hero expand are the expressive moments; everything else is quiet 120–320ms ease-out.

| Moment | Compose recipe |
|--------|----------------|
| Carousel page dot morph | `animateDpAsState` width 6 → 18 `tween(200)`; soft selection haptic on `currentPage` change |
| Favorite tap | `animateFloatAsState`/keyframes scale 1 → 1.25 → 1; color crossfade to Teal; light haptic |
| Listing open | shared-element `Modifier.sharedBounds` / `AnimatedContent` — card photo → 260.dp hero `tween(320, EaseOut)` |
| Map pin select | `animateFloatAsState` scale 1 → 1.12 `tween(150)` + recolor to white selected |
| Filter chip toggle | `animateColorAsState(tween(150))` to Purple |
| Book bar CTA press | `collectIsPressedAsState` → Purple → PurplePressed + scale 0.98 `tween(120)` |
| Tab change | instant tint to Purple; no indicator slide (no pill exists) |
| Bottom sheet | `ModalBottomSheet` slide + `tween(320)` scrim |
| Page navigation | Nav slide push `tween(300)` |
| Booking confirmed | success haptic + check animation |

```kotlin
// Favorite pop — the expressive Turo moment
val favScale by animateFloatAsState(
    targetValue = if (favPulse) 1.25f else 1f,
    animationSpec = tween(140), label = "fav",
    finishedListener = { favPulse = false },
)
LaunchedEffect(isFavorite) {
    if (isFavorite) haptics.performHapticFeedback(HapticFeedbackType.LongPress)
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the favorite/booking confirm; `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` for the carousel paging tick and CTA press.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Host avatars are Coil-loaded images, not icons.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Trips (tab) | `car.fill` | `Icons.Filled.DirectionsCar` |
| Favorites (tab) | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Favorite` |
| Inbox (tab) | `bubble.left` | `Icons.Filled.ChatBubbleOutline` |
| More (tab) | `line.3.horizontal` | `Icons.Filled.Menu` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Favorite | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Favorite` |
| Star rating | `star.fill` | `Icons.Filled.Star` |
| Instant book | `bolt.fill` | `Icons.Filled.Bolt` |
| Fuel / electric | `fuelpump.fill` | `Icons.Filled.LocalGasStation` / `ElectricCar` |
| Seats | `person.2.fill` | `Icons.Filled.AirlineSeatReclineNormal` |
| Transmission | `gearshape.fill` | `Icons.Filled.Settings` |
| Doors | `car.side` | `Icons.Filled.DirectionsCar` |
| Location | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Trip dates | `calendar` | `Icons.Filled.CalendarMonth` |
| Chevron (row) | `chevron.right` | `Icons.Filled.ChevronRight` |
| Filters | `slider.horizontal.3` | `Icons.Filled.Tune` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `HorizontalPager`, shared-element, modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; over the photo hero the system bars are transparent with light icons; pin the Book bar above the nav bar with `Modifier.navigationBarsPadding()`; floating controls offset for the camera cutout.
- **Tabular numerals**: keep `fontFeatureSettings = "tnum"` on prices, est. totals, trip counts, and ratings (already in the ramp).
- **Font scaling**: `sp` honors the user's font scale — keep it on display, car title, sections, body, card titles, prices. Pin layout-critical text (tab labels, badge eyebrows, rating number) by deriving from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`; the photo hero never scales.
- **One Purple per screen**: `TuroColors.Purple` is the single booking-CTA fill; trust badges/savings use `TuroColors.Teal`/`secondary`. Enforce in review.
- **Photos sacred**: never tint or color-block Coil car images — only thin top/bottom legibility gradients via a `Brush.verticalGradient` overlay.
- **TalkBack**: the photo hero gets `contentDescription` "Photo 1 of 8, Tesla Model 3"; the host card a full trust summary ("Host Marcus J., All-Star Host, 214 trips, responds in about 10 minutes"); the Book CTA a label including rate + est. total. Pair the All-Star badge and trip status with icon + text — never color alone.
- **Touch targets**: Material guidance is 48.dp. The primary/Book CTA is 52.dp (44.dp compact); give the 38.dp floating controls a 48.dp ripple bound; cards are full-card clickable; map pins ≥ 44.dp hit.
- **Contrast**: white on `#593BFB` and `#06231B` on `#5CE0B8` pass WCAG AA; on the near-black canvas use `#F2F1F6` text and the brightened `#7C5CFF` link.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the card→hero expand and the favorite pop (substitute crossfades); keep the success haptic.
- **Dark mode**: the canvas is near-black `#0F0F12`, NOT a tinted gray, so photos pop; `#15131F` text becomes `#F2F1F6`. Shadows read poorly on near-black, so the 1.dp `DarkDivider` border carries card elevation. Do **not** enable Material You `dynamicColorScheme()` — Turo's purple/teal identity must hold regardless of wallpaper.
