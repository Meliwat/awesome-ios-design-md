# Grubhub (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Grubhub's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the restaurant list and live order tracking, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (white canvas, wide food photography, Grubhub Red actions + Orange savings + Gold Perks + Blue tracking) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Scaffold` + `BottomSheetScaffold` instead of UIKit sheets, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for restaurant photos and avatars. Grubhub's palette is fixed brand colors, so dynamic color extraction (Palette / Material You) is **not** used.

## 1. Color Tokens

```kotlin
// ui/theme/GrubhubColors.kt
import androidx.compose.ui.graphics.Color

object GrubhubColors {
    // Brand (interactive)
    val Red          = Color(0xFFF63440)
    val RedPressed   = Color(0xFFD2202B)
    val Orange       = Color(0xFFFF8000)
    val OrangePressed= Color(0xFFE06E00)

    // Membership & status
    val PerksGold    = Color(0xFFFFB81C)
    val TrackBlue    = Color(0xFF00A0DF)
    val RatingGreen  = Color(0xFF18A957)

    // Canvas & Surfaces (Light)
    val Canvas       = Color(0xFFFFFFFF)
    val Surface1     = Color(0xFFF6F6F4)
    val Surface2     = Color(0xFFECECEA)
    val Divider      = Color(0xFFE6E6E6)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF121212) // warm near-black — NOT pure black
    val DarkSurface1 = Color(0xFF1C1C1E)
    val DarkSurface2 = Color(0xFF262626)
    val DarkDivider  = Color(0xFF2C2C2E)

    // Text
    val TextPrimary     = Color(0xFF1B1B1B)
    val TextSecondary   = Color(0xFF707070)
    val TextTertiary    = Color(0xFFA0A0A0)
    val DarkTextPrimary = Color(0xFFF2F2F2)

    // Semantic
    val Error        = Color(0xFFF0454F)
    val Success      = Color(0xFF18A957)
    val PerksInk     = Color(0xFF1A1206) // near-black on gold/orange

    // Tracking banner gradient (dark)
    val TrackBgFrom  = Color(0xFF11363F)
    val TrackBgTo    = Color(0xFF0E2A33)
}
```

Wire it into both schemes. Grubhub is light-first (white, photo-forward); the dark scheme uses a warm `#121212`, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val GrubhubLight = lightColorScheme(
    primary        = GrubhubColors.Red,
    onPrimary      = Color.White,
    secondary      = GrubhubColors.Orange,
    background      = GrubhubColors.Canvas,
    onBackground   = GrubhubColors.TextPrimary,
    surface        = GrubhubColors.Surface1,
    onSurface      = GrubhubColors.TextPrimary,
    surfaceVariant = GrubhubColors.Surface2,
    outline        = GrubhubColors.Divider,
    error          = GrubhubColors.Error,
)

private val GrubhubDark = darkColorScheme(
    primary        = GrubhubColors.Red,
    onPrimary      = Color.White,
    secondary      = GrubhubColors.Orange,
    background      = GrubhubColors.DarkCanvas,
    onBackground   = GrubhubColors.DarkTextPrimary,
    surface        = GrubhubColors.DarkSurface1,
    onSurface      = GrubhubColors.DarkTextPrimary,
    surfaceVariant = GrubhubColors.DarkSurface2,
    outline        = GrubhubColors.DarkDivider,
    error          = GrubhubColors.Error,
)

@Composable
fun GrubhubTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) GrubhubDark else GrubhubLight,
    typography = GrubhubTypography,
    content = content,
)
```

## 2. Typography (Material 3)

Grubhub's brand face is **Graphik** (proprietary); use **Inter** (SIL OFL) — drop the TTFs in `res/font/`. Body 500, titles 700–900; numbers tabular.

```kotlin
// ui/theme/GrubhubType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextMotion
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,    FontWeight.Normal),
    Font(R.font.inter_medium,     FontWeight.Medium),
    Font(R.font.inter_semibold,   FontWeight.SemiBold),
    Font(R.font.inter_bold,       FontWeight.Bold),
    Font(R.font.inter_extrabold,  FontWeight.ExtraBold),
    Font(R.font.inter_black,      FontWeight.Black),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object GrubhubText {
    val ScreenTitle    = TextStyle(Inter, fontWeight = FontWeight.Black,     fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.5).sp)
    val RestaurantHero = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section        = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val CardTitle      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val RestaurantName = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 21.sp)
    val Body           = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 22.sp)
    val Price          = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp)
    val Meta           = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Deal           = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 18.sp)
    val Overline       = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.4.sp)
    val Button         = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab            = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Caption        = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val GrubhubTypography = Typography(
    headlineLarge  = GrubhubText.ScreenTitle,
    headlineMedium = GrubhubText.RestaurantHero,
    titleLarge     = GrubhubText.Section,
    titleMedium    = GrubhubText.CardTitle,
    bodyMedium     = GrubhubText.Body,
    labelSmall     = GrubhubText.Tab,
)
```

For tabular figures on prices/ETAs, build a `TextStyle.copy(fontFeatureSettings = "tnum")`.

## 3. Signature Components

### Restaurant Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.FavoriteBorder
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun RestaurantCard(
    name: String, cuisine: String, eta: String, fee: String,
    rating: Double, deal: String?, perksBadge: String?, imageUrl: String,
    modifier: Modifier = Modifier,
) {
    var saved by remember { mutableStateOf(false) }
    Column(modifier.fillMaxWidth().padding(bottom = 22.dp)) {
        Box(
            Modifier.fillMaxWidth().aspectRatio(16f / 9f).clip(RoundedCornerShape(14.dp))
        ) {
            AsyncImage(model = imageUrl, contentDescription = name,
                modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)

            if (perksBadge != null) {
                Text(perksBadge, style = GrubhubText.Overline, color = GrubhubColors.PerksInk,
                    modifier = Modifier.padding(10.dp)
                        .clip(RoundedCornerShape(6.dp))
                        .background(GrubhubColors.PerksGold)
                        .padding(horizontal = 8.dp, vertical = 4.dp))
            }
            Icon(
                if (saved) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder,
                contentDescription = if (saved) "Saved" else "Save",
                tint = if (saved) GrubhubColors.Red else Color.White,
                modifier = Modifier.align(Alignment.TopEnd).padding(10.dp)
                    .size(30.dp).clip(RoundedCornerShape(50))
                    .background(Color.Black.copy(alpha = 0.45f))
                    .clickable { saved = !saved }.padding(7.dp),
            )
        }
        Row(Modifier.fillMaxWidth().padding(top = 10.dp), verticalAlignment = Alignment.CenterVertically) {
            Text(name, style = GrubhubText.RestaurantName, color = GrubhubColors.TextPrimary)
            Spacer(Modifier.weight(1f))
            Icon(Icons.Filled.Star, null, tint = GrubhubColors.RatingGreen, modifier = Modifier.size(12.dp))
            Spacer(Modifier.width(4.dp))
            Text(String.format("%.1f", rating), style = GrubhubText.Deal, color = GrubhubColors.TextPrimary)
        }
        Text("$cuisine · $eta · $fee", style = GrubhubText.Meta,
            color = GrubhubColors.TextSecondary, modifier = Modifier.padding(top = 4.dp))
        if (deal != null) {
            Text(deal, style = GrubhubText.Deal, color = GrubhubColors.Orange,
                modifier = Modifier.padding(top = 5.dp))
        }
    }
}
```

### Cuisine Chip Rail

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.itemsIndexed

@Composable
fun CuisineChips(items: List<String>) {
    var selected by remember { mutableIntStateOf(0) }
    LazyRow(
        horizontalArrangement = Arrangement.spacedBy(10.dp),
        contentPadding = PaddingValues(horizontal = 16.dp),
    ) {
        itemsIndexed(items) { i, label ->
            val active = i == selected
            Box(
                Modifier
                    .height(34.dp)
                    .clip(RoundedCornerShape(50))
                    .background(if (active) GrubhubColors.Red else GrubhubColors.Surface1)
                    .border(0.5.dp, if (active) GrubhubColors.Red else GrubhubColors.Divider, RoundedCornerShape(50))
                    .clickable { selected = i }
                    .padding(horizontal = 14.dp),
                contentAlignment = Alignment.Center,
            ) {
                Text(label, style = GrubhubText.Deal,
                    color = if (active) Color.White else GrubhubColors.TextSecondary)
            }
        }
    }
}
```

### Quantity Stepper

```kotlin
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Remove
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun QuantityStepper(quantity: Int, onChange: (Int) -> Unit) {
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier
            .clip(RoundedCornerShape(50))
            .background(GrubhubColors.Surface1)
            .border(0.5.dp, GrubhubColors.Divider, RoundedCornerShape(50))
            .padding(horizontal = 8.dp, vertical = 6.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        StepControl(Icons.Filled.Remove, enabled = quantity > 1) {
            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove); onChange(quantity - 1)
        }
        Text("$quantity", style = GrubhubText.Price.copy(fontFeatureSettings = "tnum"),
            color = GrubhubColors.TextPrimary, modifier = Modifier.widthIn(min = 18.dp))
        StepControl(Icons.Filled.Add, enabled = true) {
            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove); onChange(quantity + 1)
        }
    }
}

@Composable
private fun StepControl(icon: androidx.compose.ui.graphics.vector.ImageVector, enabled: Boolean, onClick: () -> Unit) {
    Box(
        Modifier.size(30.dp).clip(RoundedCornerShape(50))
            .background(GrubhubColors.Red.copy(alpha = if (enabled) 1f else 0.35f))
            .clickable(enabled = enabled) { onClick() },
        contentAlignment = Alignment.Center,
    ) { Icon(icon, null, tint = Color.White, modifier = Modifier.size(16.dp)) }
}
```

### Live Order Tracking Banner

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.ui.graphics.Brush

@Composable
fun OrderTrackingBanner(eta: String, restaurant: String, status: String, progress: Float) {
    val infinite = rememberInfiniteTransition(label = "pulse")
    val scale by infinite.animateFloat(
        1f, 1.2f,
        infiniteRepeatable(tween(1400), RepeatMode.Reverse), label = "dot",
    )
    Column(
        Modifier.fillMaxWidth()
            .clip(RoundedCornerShape(16.dp))
            .background(Brush.linearGradient(listOf(GrubhubColors.TrackBgFrom, GrubhubColors.TrackBgTo)))
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            Box(Modifier.size(8.dp).graphicsLayer { scaleX = scale; scaleY = scale }
                .clip(RoundedCornerShape(50)).background(GrubhubColors.TrackBlue))
            Text(eta, style = GrubhubText.Deal, color = GrubhubColors.DarkTextPrimary)
            Spacer(Modifier.weight(1f))
            Text(restaurant, style = GrubhubText.Caption, color = GrubhubColors.TextSecondary)
        }
        Box(Modifier.fillMaxWidth().height(4.dp).clip(RoundedCornerShape(50))
            .background(Color.White.copy(alpha = 0.12f))) {
            Box(Modifier.fillMaxWidth(progress).height(4.dp)
                .clip(RoundedCornerShape(50)).background(GrubhubColors.TrackBlue))
        }
        Text(status, style = GrubhubText.Caption.copy(fontWeight = FontWeight.SemiBold),
            color = GrubhubColors.TrackBlue)
    }
}
```

### Order Tracking Timeline

```kotlin
enum class StepState { Done, Active, Pending }
data class TrackStep(val title: String, val sub: String, val state: StepState)

@Composable
fun TrackingTimeline(steps: List<TrackStep>) {
    Column {
        steps.forEachIndexed { i, step ->
            Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
                Column(horizontalAlignment = Alignment.CenterHorizontally) {
                    val fill = when (step.state) {
                        StepState.Done -> GrubhubColors.TrackBlue
                        StepState.Active -> GrubhubColors.Red
                        StepState.Pending -> GrubhubColors.Canvas
                    }
                    val stroke = when (step.state) {
                        StepState.Done -> GrubhubColors.TrackBlue
                        StepState.Active -> GrubhubColors.Red
                        StepState.Pending -> GrubhubColors.Divider
                    }
                    Box(
                        Modifier.size(16.dp).clip(RoundedCornerShape(50))
                            .background(fill)
                            .border(3.dp, stroke, RoundedCornerShape(50))
                    )
                    if (i < steps.lastIndex) {
                        Box(Modifier.width(3.dp).heightIn(min = 26.dp).weight(1f, fill = false)
                            .background(if (step.state == StepState.Done) GrubhubColors.TrackBlue else GrubhubColors.Divider))
                    }
                }
                Column(Modifier.padding(bottom = 22.dp)) {
                    Text(step.title, style = GrubhubText.Meta.copy(fontWeight = FontWeight.Bold),
                        color = GrubhubColors.TextPrimary)
                    Text(step.sub, style = GrubhubText.Caption, color = GrubhubColors.TextSecondary)
                }
            }
        }
    }
}
```

### Primary Button

```kotlin
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.interaction.MutableInteractionSource

@Composable
fun GHButton(title: String, onClick: () -> Unit, orange: Boolean = false) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val bg = when {
        orange && pressed -> GrubhubColors.OrangePressed
        orange -> GrubhubColors.Orange
        pressed -> GrubhubColors.RedPressed
        else -> GrubhubColors.Red
    }
    Box(
        Modifier.fillMaxWidth().heightIn(min = 52.dp)
            .scale(if (pressed) 0.98f else 1f)
            .clip(RoundedCornerShape(50)).background(bg)
            .clickable(interaction, indication = null) { onClick() },
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = GrubhubText.Button,
            color = if (orange) GrubhubColors.PerksInk else Color.White)
    }
}
// Checkout: GHButton("Go to checkout · $42.18", onClick = {})
```

## 4. Navigation

Grubhub has a 5-tab bottom strip and no Material tint pill — active is just Grubhub Red.

```kotlin
@Composable
fun GrubhubBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = GrubhubColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Delivery" to Icons.Filled.Home,
            "Search"   to Icons.Filled.Search,
            "Orders"   to Icons.Filled.ReceiptLong,
            "Saved"    to Icons.Filled.FavoriteBorder,
            "Account"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = GrubhubText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = GrubhubColors.Red,
                    selectedTextColor   = GrubhubColors.Red,
                    unselectedIconColor = Color(0xFF888888),
                    unselectedTextColor = Color(0xFF888888),
                    indicatorColor      = Color.Transparent, // no Material pill — Grubhub has none
                ),
            )
        }
    }
}
```

The address bar is a top `Row` (label + value + chevron, trailing bag `Badge`); sheets (bag, address, filters) are `ModalBottomSheet` with a 16.dp top radius and a sticky `GHButton` checkout pill. The courier map uses Google Maps Compose with a red destination marker.

## 5. Motion

Grubhub motion is functional — confirm an action, advance a stage, never decorative.

| Moment | Compose recipe |
|--------|----------------|
| Add-to-bag badge bump | `animateFloatAsState` keyframes 1 → 1.25 → 1 `spring(dampingRatio = 0.5f)` + `HapticFeedbackType.LongPress` |
| Quantity change | `Crossfade` / `AnimatedContent` on the count + `TextHandleMove` haptic |
| Tracking progress fill | `animateFloatAsState(progress, tween(600, easing = FastOutSlowInEasing))` driving `fillMaxWidth(progress)` |
| Tracking dot pulse | `rememberInfiniteTransition` `animateFloat(1f, 1.2f, tween(1400), Reverse)` |
| Step timeline advance | `animateDpAsState` node offset `tween(250)` + `HapticFeedbackType.LongPress` (medium analog) |
| Card → detail | shared element via `SharedTransitionLayout` (Compose 1.7+) `tween(320)` |
| Chip select | filled background `animateColorAsState` `tween(150)` |
| Sheet present | `ModalBottomSheet` default slide; scrim fades in parallel |

```kotlin
// Tracking progress — the canonical Grubhub motion
val width by animateFloatAsState(progress, tween(600, easing = FastOutSlowInEasing), label = "trackFill")
Box(Modifier.fillMaxWidth(width).height(4.dp).background(GrubhubColors.TrackBlue))
```

Haptics: `HapticFeedbackType.TextHandleMove` for the stepper tick; `HapticFeedbackType.LongPress` for add-to-bag and stage-complete. Order-placed success can use `VibrationEffect.createPredefined(EFFECT_HEAVY_CLICK)` via the system `Vibrator`.

## 6. Icons

The closest first-party set to Grubhub's iconography is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Delivery (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Orders (tab) | `list.bullet.rectangle` | `Icons.Filled.ReceiptLong` |
| Saved (tab) | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Icons.Filled.Favorite` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Rating star | `star.fill` | `Icons.Filled.Star` |
| Address chevron | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Bag / cart | `bag` / `bag.fill` | `Icons.Filled.ShoppingBag` |
| Stepper minus | `minus` | `Icons.Filled.Remove` |
| Stepper plus | `plus` | `Icons.Filled.Add` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Driver call | `phone.fill` | `Icons.Filled.Call` |
| Driver message | `message.fill` | `Icons.Filled.Chat` |
| Tracking pin | `mappin.circle.fill` | `Icons.Filled.LocationOn` |
| Promo / deal | `tag.fill` | `Icons.Filled.LocalOffer` |
| Filters | `slider.horizontal.3` | `Icons.Filled.Tune` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion and `SharedTransitionLayout` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the white canvas wants dark-content system bars (light-content in dark mode and over the tracking map). The address bar respects the camera cutout; the bottom tab + sticky checkout pill use `Modifier.navigationBarsPadding()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, restaurant names, body and prices. Pin layout-sensitive text (12sp Perks badges, 13sp deal lines, 10sp tab labels, chip text, ETA dot label) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Tabular numerals**: apply `fontFeatureSettings = "tnum"` to prices, fees, ETAs and quantity counters so figures don't jitter.
- **TalkBack**: a restaurant card is one focusable node — set a merged `contentDescription` "{name}, {rating} stars, {cuisine}, {eta}, {fee}". The saved heart is a toggle with state. The tracking timeline labels each step with its state ("Driver picking up, in progress"); post a `liveRegion` announcement when a stage advances.
- **Touch targets**: Material guidance is 48.dp. The 30.dp stepper controls and 30.dp heart get a 48.dp hit area via padding; chips are 34.dp tall but full-pill tappable; primary buttons ≥ 52.dp.
- **Contrast**: `#1B1B1B` on `#FFFFFF` passes WCAG AAA; white on `#F63440` passes AA for button labels; orange deal text `#FF8000` is used at 13sp 600 (bold) where it passes AA — never as small regular body.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the dot pulse and the timeline node slide (substitute a `Crossfade`); keep the progress fill but make it instant (it conveys state).
- **Dark mode**: invert via the `Dark*` palette — `#121212`, NOT true black; `#1B1B1B` text becomes `#F2F2F2`. Brand Red/Orange/Gold/Blue/Green are constant. Shadows are nearly invisible on dark, so add a 0.5dp `DarkDivider` border on floating commerce surfaces (bag sheet, sticky checkout, tracking banner). Do **not** enable Material You `dynamicColorScheme()` — Grubhub's red/orange identity must hold regardless of wallpaper.
