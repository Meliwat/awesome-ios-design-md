# Booking.com (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Booking.com's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics — including the signature review-score badge and the dense property card.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Booking.com's white card marketplace, navy brand / bright-blue action split, the review-score badge, the Genius banner, the search form card) while making everything idiomatic Android — `NavigationBar` + a navy `TopAppBar` instead of UIKit bars, `dp`/`sp` instead of `pt`, Compose haptics instead of `UIImpactFeedbackGenerator`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/BookingColors.kt
import androidx.compose.ui.graphics.Color

object BookingColors {
    // Canvas & Surfaces
    val Canvas      = Color(0xFFFFFFFF)
    val Surface     = Color(0xFFF2F2F2)
    val SurfaceDeep = Color(0xFFE6E6E6)
    val Divider     = Color(0xFFE0E0E0)

    // Text
    val TextPrimary   = Color(0xFF1A1A1A)
    val TextSecondary = Color(0xFF6B6B6B)
    val TextTertiary  = Color(0xFF949494)

    // Brand
    val Blue       = Color(0xFF003580) // brand / structure
    val Cta        = Color(0xFF0071C2) // action
    val CtaPressed = Color(0xFF005A9C)
    val BlueTint   = Color(0xFFE7F0F7)
    val Yellow     = Color(0xFFFEBB02) // star rating only

    // Semantic
    val Success = Color(0xFF008009)
    val Deal    = Color(0xFFCC0000)
    val Warning = Color(0xFFF5A623)
}
```

Wire it into a Material 3 `lightColorScheme`. Booking.com is light-only by design; do not provide a dark scheme unless mirroring system dark with identical brand colors.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val BookingScheme = lightColorScheme(
    primary          = BookingColors.Cta,        // action blue
    onPrimary        = Color.White,
    secondary         = BookingColors.Blue,       // brand navy
    onSecondary       = Color.White,
    background        = BookingColors.Canvas,
    onBackground      = BookingColors.TextPrimary,
    surface           = BookingColors.Canvas,
    onSurface         = BookingColors.TextPrimary,
    surfaceVariant    = BookingColors.Surface,
    onSurfaceVariant  = BookingColors.TextSecondary,
    outline           = BookingColors.Divider,
    error             = BookingColors.Deal,
)

@Composable
fun BookingTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = BookingScheme, typography = BookingTypography, content = content)
```

## 2. Typography

Booking.com uses a BookingSans-equivalent grotesque — substitute Inter. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto).

```kotlin
// ui/theme/BookingType.kt
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
object BookingText {
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val PropName    = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val Price       = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 20.sp)
    val ScoreNum    = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 18.sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 20.sp)
    val ScoreWord   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 14.sp, lineHeight = 17.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Tag         = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.1.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.1.sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val BookingTypography = Typography(
    headlineSmall = BookingText.ScreenTitle,
    titleMedium   = BookingText.PropName,
    bodyMedium    = BookingText.Body,
    labelSmall    = BookingText.Tab,
)
```

Render prices and review scores with tabular figures via `TextStyle(fontFeatureSettings = "tnum")`.

## 3. Signature Components

### Review-Score Badge

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import java.util.Locale

private fun wordFor(score: Double) = when {
    score >= 9 -> "Fabulous"
    score >= 8 -> "Very good"
    score >= 7 -> "Good"
    else       -> "Pleasant"
}

@Composable
fun ReviewScoreBadge(score: Double, reviews: Int, modifier: Modifier = Modifier) {
    Row(modifier, verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp)) {
        Box(
            Modifier
                .clip(RoundedCornerShape(5.dp))
                .background(BookingColors.Blue)
                .padding(horizontal = 7.dp, vertical = 4.dp),
        ) {
            Text(
                String.format(Locale.US, "%.1f", score), // always one decimal
                style = BookingText.ScoreNum.copy(fontFeatureSettings = "tnum"),
                color = Color.White,
            )
        }
        Text(wordFor(score), style = BookingText.ScoreWord, color = BookingColors.TextPrimary)
        Text("· ${"%,d".format(reviews)} reviews",
            style = BookingText.Tag.copy(fontWeight = FontWeight.Normal),
            color = BookingColors.TextSecondary)
    }
}
```

### Property Card (dense, border-defined)

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.draw.scale
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun PropertyCard(
    photoUrl: String,
    name: String,
    area: String,
    distance: String,
    score: Double,
    reviews: Int,
    originalPrice: String?,
    price: String,
    scarcity: String?,
    onClick: () -> Unit,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.99f else 1f, label = "cardScale")

    Row(
        Modifier
            .fillMaxWidth()
            .scale(scale)
            .clip(RoundedCornerShape(8.dp))
            .background(BookingColors.Canvas)
            .border(1.dp, BookingColors.Divider, RoundedCornerShape(8.dp))
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(12.dp),
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        AsyncImage(
            model = photoUrl, contentDescription = name,
            modifier = Modifier.size(120.dp).clip(RoundedCornerShape(8.dp)),
            contentScale = ContentScale.Crop,
        )
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(name, style = BookingText.PropName, color = BookingColors.TextPrimary,
                maxLines = 2, overflow = TextOverflow.Ellipsis)
            Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Text(area, style = BookingText.Meta, color = BookingColors.Cta)
                Text(distance, style = BookingText.Meta, color = BookingColors.TextSecondary)
            }
            Text("Free cancellation", style = BookingText.Meta, color = BookingColors.Success)
            ReviewScoreBadge(score, reviews)
            Spacer(Modifier.weight(1f))
            Column(horizontalAlignment = Alignment.End) {
                if (originalPrice != null) {
                    Text(originalPrice, style = BookingText.Meta.copy(
                        textDecoration = TextDecoration.LineThrough),
                        color = BookingColors.TextTertiary)
                }
                Text(price, style = BookingText.Price.copy(fontFeatureSettings = "tnum"),
                    color = BookingColors.TextPrimary)
                Text("Includes taxes and fees", style = BookingText.Caption,
                    color = BookingColors.TextSecondary)
                if (scarcity != null) {
                    Text(scarcity, style = BookingText.Tag, color = BookingColors.Deal)
                }
            }
        }
    }
}
```

### Primary CTA

```kotlin
@Composable
fun BookingCTA(label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f,
        spring(dampingRatio = 0.8f), label = "ctaScale")
    Box(
        modifier
            .fillMaxWidth()
            .height(52.dp)
            .scale(scale)
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) BookingColors.CtaPressed else BookingColors.Cta)
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = BookingText.Button, color = Color.White)
    }
}
```

### Search Form Card

```kotlin
@Composable
fun SearchFormCard(modifier: Modifier = Modifier) {
    Column(
        modifier
            .fillMaxWidth()
            .shadow(6.dp, RoundedCornerShape(8.dp),
                spotColor = BookingColors.TextPrimary.copy(alpha = 0.08f))
            .clip(RoundedCornerShape(8.dp))
            .background(BookingColors.Canvas)
            .padding(12.dp),
    ) {
        FormRow(Icons.Filled.LocationOn, "Where are you going?")
        HorizontalDivider(color = BookingColors.Divider)
        FormRow(Icons.Filled.DateRange, "Fri 12 Jul — Sun 14 Jul")
        HorizontalDivider(color = BookingColors.Divider)
        FormRow(Icons.Filled.Person, "2 adults · 0 children · 1 room")
        Spacer(Modifier.height(12.dp))
        BookingCTA("Search", onClick = {})
    }
}

@Composable
private fun FormRow(icon: ImageVector, text: String) {
    Row(
        Modifier.fillMaxWidth().height(52.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(icon, contentDescription = null, tint = BookingColors.TextSecondary,
            modifier = Modifier.size(18.dp))
        Text(text, style = BookingText.Body, color = BookingColors.TextPrimary)
    }
}
```

### Genius Banner

```kotlin
@Composable
fun GeniusBanner(modifier: Modifier = Modifier) {
    Row(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(8.dp))
            .background(BookingColors.Blue)
            .padding(16.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Column(Modifier.weight(1f)) {
            Text("Genius", style = BookingText.PropName, color = Color.White)
            Text("You're a Genius level 1 member — enjoy 10% off",
                style = BookingText.Meta, color = Color.White.copy(alpha = 0.9f))
        }
        Box(
            Modifier.clip(RoundedCornerShape(50)).background(BookingColors.Yellow)
                .padding(horizontal = 8.dp, vertical = 4.dp),
        ) { Text("10% off", style = BookingText.Tag, color = BookingColors.Blue) }
    }
}
```

### Filter Chip

```kotlin
@Composable
fun FilterChip(label: String, selected: Boolean, onClick: () -> Unit) {
    Box(
        Modifier
            .clip(RoundedCornerShape(50))
            .background(if (selected) BookingColors.BlueTint else BookingColors.Canvas)
            .border(if (selected) 1.5.dp else 1.dp,
                if (selected) BookingColors.Cta else BookingColors.Divider,
                RoundedCornerShape(50))
            .clickable(onClick = onClick)
            .padding(horizontal = 16.dp, vertical = 8.dp),
    ) {
        Text(
            label,
            style = BookingText.Tag.copy(
                fontWeight = if (selected) FontWeight.Bold else FontWeight.SemiBold),
            color = if (selected) BookingColors.Cta else BookingColors.TextPrimary,
        )
    }
}
```

## 4. Map Toggle + Price Pin

```kotlin
@Composable
fun PricePin(price: String, selected: Boolean) {
    val scale by animateFloatAsState(if (selected) 1.12f else 1f,
        spring(dampingRatio = 0.7f), label = "pinScale")
    Box(
        Modifier
            .scale(scale)
            .shadow(6.dp, RoundedCornerShape(50),
                spotColor = BookingColors.TextPrimary.copy(alpha = 0.16f))
            .clip(RoundedCornerShape(50))
            .background(if (selected) BookingColors.Blue else BookingColors.Canvas)
            .padding(horizontal = 10.dp, vertical = 6.dp),
    ) {
        Text(price,
            style = BookingText.Tag.copy(fontWeight = FontWeight.Bold, fontFeatureSettings = "tnum"),
            color = if (selected) Color.White else BookingColors.TextPrimary)
    }
}
// A "Map"/"List" pill toggle floats above the list; selecting a pin slides one
// PropertyCard up from the bottom (AnimatedVisibility + slideInVertically, or a
// ModalBottomSheet at a low detent).
```

## 5. Bottom Navigation + Navy Top Bar

The top app bar is solid Booking Blue with white content; the bottom bar is white with a CTA-blue active tint.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun BookingTopBar(title: String) {
    TopAppBar(
        title = { Text(title, style = BookingText.Button, color = Color.White) },
        colors = TopAppBarDefaults.topAppBarColors(
            containerColor = BookingColors.Blue,
            titleContentColor = Color.White,
            navigationIconContentColor = Color.White,
        ),
    )
}

@Composable
fun BookingBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = BookingColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Search"   to Icons.Filled.Search,
            "Saved"    to Icons.Filled.FavoriteBorder,
            "Bookings" to Icons.Filled.Work,
            "Profile"  to Icons.Filled.Person,
            "Help"     to Icons.Filled.HelpOutline,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = BookingText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = BookingColors.Cta,
                    selectedTextColor = BookingColors.Cta,
                    unselectedIconColor = BookingColors.TextSecondary,
                    unselectedTextColor = BookingColors.TextSecondary,
                    indicatorColor = BookingColors.BlueTint,
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Card tap | `Modifier.scale` 1 → 0.99 via `collectIsPressedAsState()` + `animateFloatAsState` |
| Filter chip toggle | crossfade border/background ~150ms (`animateColorAsState(tween(150))`) |
| Map pin select | `animateFloatAsState` scale 1 → 1.12 `spring`; recolor navy; card `slideInVertically` |
| CTA press | `Modifier.scale` 1 → 0.98 via `collectIsPressedAsState()` |
| Score badge | **no animation** — consistency is the brand |
| Sheet present | `ModalBottomSheet` default slide; scrim `BookingColors.TextPrimary.copy(alpha = 0.45f)` |

```kotlin
// Filter chip color crossfade
val border by animateColorAsState(
    if (selected) BookingColors.Cta else BookingColors.Divider, tween(150), label = "chipBorder")
```

Haptics: this UI is mostly tap-to-navigate, so haptics are light. Use `LocalHapticFeedback.current.performHapticFeedback(HapticFeedbackType.TextHandleMove)` on filter-chip toggles and stepper taps; no haptic on the static score badge.

## 7. Icons

Booking.com ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Booking.com's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Saved (tab) | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Bookings (tab) | `suitcase` | `Icons.Filled.Work` |
| Profile (tab) | `person` | `Icons.Filled.Person` |
| Help (tab) | `questionmark.circle` | `Icons.Filled.HelpOutline` |
| Destination | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Dates | `calendar` | `Icons.Filled.DateRange` |
| Guests | `person` / `person.2` | `Icons.Filled.Person` / `Icons.Filled.People` |
| Map toggle | `map` | `Icons.Filled.Map` |
| Filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Star rating | `star.fill` | `Icons.Filled.Star` |
| Back | `chevron.left` | `Icons.Filled.ArrowBackIosNew` |
| Stepper minus / plus | `minus` / `plus` | `Icons.Filled.Remove` / `Icons.Filled.Add` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; cards/badge are layout-only and comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the navy `TopAppBar` wants light system-bar icons via `WindowCompat`. Apply `Scaffold` insets so content and the bottom bar clear the gesture nav.
- **Font scaling**: `sp` honors the user's font scale — keep it on property names, body, policy copy. The score badge always shows `X.X`; pin small tags and tab labels via `dp`-derived sizing.
- **TalkBack**: merge the badge into one node — `Modifier.semantics(mergeDescendants = true) { contentDescription = "Review score 8.9, Fabulous, 1,284 reviews" }`; the card reads name → score → price → scarcity; filter chips use `Modifier.semantics { role = Role.Checkbox; stateDescription = if (selected) "Selected" else "Not selected" }`.
- **Touch targets**: Material guidance is 48.dp minimum. Cards and the CTA clear it; filter chips (~32) and steppers must get 48.dp hit areas via padding even though the visual is smaller.
- **Contrast**: `#6B6B6B` on `#FFFFFF` passes WCAG AA at 13sp+. CTA Blue `#0071C2` on white passes AA for 16sp+ bold; white on Booking Blue `#003580` (badge/banner/top bar) is high-contrast. Deal red `#CC0000` on white passes AA — keep scarcity text weight 600.
- **Color is not the only signal**: the score's numeric value and word label carry the meaning, not just the navy chip. Do **not** enable Material You `dynamicLightColorScheme()` — the navy/bright-blue/yellow Booking.com identity must be fixed regardless of wallpaper.
