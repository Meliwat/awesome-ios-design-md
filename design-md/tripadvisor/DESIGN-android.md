# Tripadvisor (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Tripadvisor's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Tripadvisor's bright white gallery canvas, single-green accent, the five-circle bubble rating) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush` scrim instead of a CALayer gradient, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/TripColors.kt
import androidx.compose.ui.graphics.Color

object TripColors {
    // Canvas & Surfaces
    val Canvas         = Color(0xFFFFFFFF)
    val Surface        = Color(0xFFF2F2F2)
    val Divider        = Color(0xFFE0E0E0)
    val SurfacePressed = Color(0xFFE8E8E8)

    // Text
    val TextPrimary    = Color(0xFF000000)
    val TextSecondary  = Color(0xFF6B6B6B)
    val TextTertiary   = Color(0xFF9B9B9B)

    // Brand
    val Green          = Color(0xFF34E0A1)
    val GreenPressed   = Color(0xFF21C589)
    val OwlBlack       = Color(0xFF000000)

    // Semantic
    val EmptyBubble    = Color(0xFFD9D9D9)
    val ErrorRed       = Color(0xFFD6122E)
}
```

Wire it into a Material 3 `lightColorScheme` so ripples, dividers, and default component colors inherit the brand. Tripadvisor is light-first.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val TripScheme = lightColorScheme(
    primary        = TripColors.Green,
    onPrimary      = TripColors.OwlBlack,    // intentional: black on bright mint
    background     = TripColors.Canvas,
    onBackground   = TripColors.TextPrimary,
    surface        = TripColors.Canvas,
    onSurface      = TripColors.TextPrimary,
    surfaceVariant = TripColors.Surface,
    outline        = TripColors.Divider,
    error          = TripColors.ErrorRed,
)

@Composable
fun TripTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = TripScheme, typography = TripTypography, content = content)
```

## 2. Typography

Trip Sans is proprietary. Drop substitute Inter TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto).

```kotlin
// ui/theme/TripType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val TripSans = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object TripText {
    val TitleLarge = TextStyle(TripSans, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val PlaceHero  = TextStyle(TripSans, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.3).sp)
    val Section    = TextStyle(TripSans, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val PlaceName  = TextStyle(TripSans, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val CardTitle  = TextStyle(TripSans, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val ReviewBody = TextStyle(TripSans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Button     = TextStyle(TripSans, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 20.sp)
    val ButtonSec  = TextStyle(TripSans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 19.sp)
    val Meta       = TextStyle(TripSans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Tab        = TextStyle(TripSans, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
    val Badge      = TextStyle(TripSans, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
    val Caption    = TextStyle(TripSans, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val TripTypography = Typography(
    headlineLarge = TripText.TitleLarge,
    headlineSmall = TripText.Section,
    titleMedium   = TripText.PlaceName,
    bodyMedium    = TripText.ReviewBody,
    labelSmall    = TripText.Tab,
)
```

## 3. Signature Components

### Five-Circle Bubble Rating

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.unit.dp

@Composable
fun BubbleRating(
    value: Float,                 // 0f .. 5f
    size: Dp = 16.dp,
    reviewCount: Int? = null,
    modifier: Modifier = Modifier,
) {
    Row(modifier, verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp)) {
        Row(horizontalArrangement = Arrangement.spacedBy(2.dp)) {
            repeat(5) { i ->
                val fill = (value - i).coerceIn(0f, 1f) // 0, 0.5, or 1
                Canvas(Modifier.size(size)) {
                    val r = this.size.minDimension / 2f
                    drawCircle(TripColors.EmptyBubble, r, Offset(r, r))
                    if (fill > 0f) {
                        clipRect(right = this.size.width * fill) {
                            drawCircle(TripColors.Green, r, Offset(r, r))
                        }
                    }
                }
            }
        }
        if (reviewCount != null) {
            Text(
                "%.1f (%,d)".format(value, reviewCount),
                style = TripText.Meta,
                color = TripColors.TextSecondary,
            )
        }
    }
}
```

### Interactive Bubble Picker (Review Write)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun BubblePicker(
    rating: Int,
    onChange: (Int) -> Unit,
    size: Dp = 32.dp,
) {
    val haptics = LocalHapticFeedback.current
    Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
        (1..5).forEach { i ->
            val selected = i <= rating
            val scale by animateFloatAsState(
                targetValue = if (i == rating) 1.15f else 1f,
                animationSpec = spring(dampingRatio = 0.55f),
                label = "bubble$i",
            )
            Box(
                Modifier
                    .size(size)
                    .scale(scale)
                    .clip(CircleShape)
                    .background(if (selected) TripColors.Green else TripColors.EmptyBubble)
                    .clickable(remember { MutableInteractionSource() }, indication = null) {
                        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS light impact
                        onChange(i)
                    }
            )
        }
    }
}
```

### Primary CTA Pill

```kotlin
enum class PillStyle { Filled, Outline }

@Composable
fun TripPillButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    style: PillStyle = PillStyle.Filled,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "pillScale")
    val filled = style == PillStyle.Filled

    Box(
        modifier = modifier
            .scale(scale)
            .clip(CircleShape)
            .then(
                if (filled) Modifier.background(if (pressed) TripColors.GreenPressed else TripColors.Green)
                else Modifier.border(1.dp, TripColors.OwlBlack, CircleShape)
            )
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = if (filled) 28.dp else 24.dp, vertical = if (filled) 14.dp else 12.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = if (filled) TripText.Button else TripText.ButtonSec, color = TripColors.OwlBlack)
    }
}
```

### Place Card

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.outlined.FavoriteBorder
import androidx.compose.material3.Icon
import coil.compose.AsyncImage

@Composable
fun PlaceCard(
    name: String,
    photoUrl: String,
    rating: Float,
    reviews: Int,
    category: String,
    priceTier: String,
    distance: String,
    awarded: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    var saved by remember { mutableStateOf(false) }
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "cardScale")

    Column(
        modifier
            .scale(scale)
            .clickable(interaction, indication = null, onClick = onClick),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Box {
            AsyncImage(
                model = photoUrl,
                contentDescription = name,
                modifier = Modifier
                    .fillMaxWidth()
                    .aspectRatio(3f / 2f)
                    .clip(RoundedCornerShape(16.dp)),
                contentScale = ContentScale.Crop,
            )
            Box(
                Modifier
                    .align(Alignment.TopEnd)
                    .padding(12.dp)
                    .size(36.dp)
                    .clip(CircleShape)
                    .background(Color.Black.copy(alpha = 0.30f))
                    .clickable { saved = !saved },
                contentAlignment = Alignment.Center,
            ) {
                Icon(
                    if (saved) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
                    contentDescription = if (saved) "Saved" else "Save",
                    tint = if (saved) TripColors.Green else Color.White,
                    modifier = Modifier.size(20.dp),
                )
            }
        }

        if (awarded) {
            Box(
                Modifier
                    .clip(CircleShape)
                    .background(TripColors.Green)
                    .padding(horizontal = 10.dp, vertical = 4.dp),
            ) { Text("TRAVELERS' CHOICE", style = TripText.Badge, color = TripColors.OwlBlack) }
        }

        Text(name, style = TripText.PlaceName, color = TripColors.TextPrimary)
        BubbleRating(value = rating, size = 16.dp, reviewCount = reviews)
        Text("$category · $priceTier · $distance", style = TripText.Meta, color = TripColors.TextSecondary)
    }
}
```

### Place Detail Hero (scrim over photo)

```kotlin
import androidx.compose.ui.graphics.Brush

@Composable
fun PlaceHero(
    name: String,
    photoUrl: String,
    rating: Float,
    reviews: Int,
) {
    Box(Modifier.fillMaxWidth().height(280.dp)) {
        AsyncImage(
            model = photoUrl,
            contentDescription = "$name photo",
            modifier = Modifier.fillMaxSize(),
            contentScale = ContentScale.Crop,
        )
        Box(
            Modifier
                .fillMaxSize()
                .background(
                    Brush.verticalGradient(
                        0.5f to Color.Transparent,
                        1f to Color.Black.copy(alpha = 0.55f),
                    )
                )
        )
        Column(
            Modifier.align(Alignment.BottomStart).padding(20.dp),
            verticalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Text(name, style = TripText.PlaceHero, color = Color.White)
            Row(verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                BubbleRating(value = rating, size = 18.dp)
                Text("%,d reviews".format(reviews), style = TripText.Meta, color = Color.White)
            }
        }
    }
}
```

## 4. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Tripadvisor's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 96%-opaque white surface. **Active tint is Tripadvisor Green** — green is the active indicator here.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun TripBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = TripColors.Canvas.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Explore" to Icons.Filled.Explore,
            "Search"  to Icons.Filled.Search,
            "Trips"   to Icons.Filled.Work,
            "Review"  to Icons.Filled.Create,
            "More"    to Icons.Filled.MoreHoriz,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = TripText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = TripColors.Green,
                    selectedTextColor   = TripColors.Green,
                    unselectedIconColor = TripColors.TextSecondary,
                    unselectedTextColor = TripColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // no Material pill — Tripadvisor has none
                ),
            )
        }
    }
}
```

## 5. Navigation

The top bar carries the owl logomark (left, 28.dp), a location pill (center), and the profile avatar (right). Below it, a pinned `#F2F2F2` search field at 48.dp with a full-pill `CircleShape`. On scroll, collapse to a compact blurred bar — Android has no live blur, so use the 96%-opaque white surface and a 1.dp `Divider`. Render the search field in a `Scaffold` `topBar` slot so list content scrolls beneath it.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Save-heart bounce | `Animatable` keyframes 1 → 1.2 → 1 over 280ms; `HapticFeedbackType.LongPress` |
| Bubble selection | `animateFloatAsState` 1 → 1.15 `spring(dampingRatio = 0.55f)`; `HapticFeedbackType.TextHandleMove` |
| Card tap | `animateFloatAsState` 1 → 0.98 `spring(dampingRatio = 0.8f)` |
| Hero parallax | read `LazyListState.firstVisibleItemScrollOffset`, apply `Modifier.graphicsLayer { translationY = offset * 0.5f }` to the hero |
| Tab switch | `Crossfade` 200ms, active icon swaps to filled variant |

```kotlin
// Save-heart bounce
val scale = remember { Animatable(1f) }
LaunchedEffect(saved) {
    if (saved) {
        scale.animateTo(1.2f, tween(140))
        scale.animateTo(1f, tween(140))
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(8, ...)` to approximate iOS's `.light` impact.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity with Tripadvisor's custom glyphs, export them as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Save heart | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Filters | `line.3.horizontal.decrease` | `Icons.Filled.Tune` |
| Location pin | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Directions | `arrow.triangle.turn.up.right.diamond` | `Icons.Filled.Navigation` |
| Helpful vote | `hand.thumbsup` | `Icons.Outlined.ThumbUp` / `Icons.Filled.ThumbUp` |
| Explore (tab) | `safari.fill` | `Icons.Filled.Explore` |
| Trips (tab) | `suitcase.fill` | `Icons.Filled.Work` |
| Review (tab) | `square.and.pencil` | `Icons.Filled.Create` |
| More (tab) | `ellipsis` | `Icons.Filled.MoreHoriz` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the white canvas wants `WindowCompat` dark-content system bars. Apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` or `Scaffold` insets; the detail hero bleeds full-width while the sticky bar clears the status bar.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on place names, review body, headers. Pin layout-sensitive units (the bubble rating, tab labels, photo-count pill) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: expose the bubble rating as one combined `contentDescription` (`"Rated 4.5 out of 5, 1,284 reviews"`); make the review picker `Modifier.semantics { stateDescription = "$rating of 5 bubbles" }` with an adjustable role; mark the save heart as a separate toggle button.
- **Touch targets**: Material guidance is 48.dp minimum. The CTA pill clears it; ensure the 36.dp save-heart overlay has a 48.dp hit area via padding, and each review-form bubble has ≥44.dp effective touch.
- **Contrast**: `#6B6B6B` on `#FFFFFF` passes WCAG AA at 13sp+. Black-on-`#34E0A1` is intentional and high-contrast — do not switch to white text.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Tripadvisor's brand requires the fixed white canvas and the single mint-green accent regardless of wallpaper.
