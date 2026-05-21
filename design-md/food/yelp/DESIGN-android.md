# Yelp (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Yelp's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, a half-star `RatingBar`, the review card, the business header, navigation, and motion.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Yelp's white canvas, the rationed Red, the warm orange-red 5-star scale, the review wall) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` for filters, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for business photos and avatars. No color extraction — Yelp's palette is fixed brand colors, so Palette is not needed. Yelp is light-mode-first; a full dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/YelpColors.kt
import androidx.compose.ui.graphics.Color

object YelpColors {
    // Brand (interactive)
    val Red        = Color(0xFFFF1A1A)
    val RedLogo    = Color(0xFFD32323)
    val RedPressed = Color(0xFFB81E1E)

    // Rating
    val StarFilled    = Color(0xFFF25C05)
    val StarEmpty     = Color(0xFFE3E3E0)
    val StarEmptyDark = Color(0xFF3A3A3A)

    // Canvas & Surfaces (Light)
    val Canvas      = Color(0xFFFFFFFF)
    val SurfaceGray = Color(0xFFF5F5F5)
    val SectionBand = Color(0xFFF0F0F0)
    val Divider     = Color(0xFFE0E0E0)
    val PressedRow  = Color(0xFFEDEDED)

    // Canvas & Surfaces (Dark)
    val DarkCanvas      = Color(0xFF161616) // near-black, NOT pure black
    val DarkSurface1    = Color(0xFF1E1E1E)
    val DarkSurface2    = Color(0xFF2A2A2A)
    val DarkDivider     = Color(0xFF303030)
    val DarkSectionBand = Color(0xFF0C0C0C)

    // Text
    val TextPrimary       = Color(0xFF2B2B2B)
    val TextSecondary     = Color(0xFF6E6E6E)
    val TextTertiary      = Color(0xFF9A9A9A)
    val DarkTextPrimary   = Color(0xFFE6E6E6)
    val DarkTextSecondary = Color(0xFFA8A8A8)

    // Semantic
    val Open     = Color(0xFF2DA44E)
    val Closed   = Color(0xFFE03E3E)
    val Warning  = Color(0xFFD9730D)
    val Link     = Color(0xFF0073BB)
    val LinkDark = Color(0xFF4FA3D9)
    val Elite    = Color(0xFFA2231D)
}
```

Wire it into both schemes. Yelp is light-first (white paper + photos); the dark scheme uses `#161616`, never true black, and the brand Red stays saturated.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val YelpLight = lightColorScheme(
    primary        = YelpColors.Red,
    onPrimary      = YelpColors.Canvas,
    background      = YelpColors.Canvas,
    onBackground    = YelpColors.TextPrimary,
    surface         = YelpColors.SurfaceGray,
    onSurface       = YelpColors.TextPrimary,
    surfaceVariant  = YelpColors.PressedRow,
    outline         = YelpColors.Divider,
    error           = YelpColors.Closed,
)

private val YelpDark = darkColorScheme(
    primary        = YelpColors.Red,           // brand red stays saturated on dark
    onPrimary      = YelpColors.Canvas,
    background      = YelpColors.DarkCanvas,
    onBackground    = YelpColors.DarkTextPrimary,
    surface         = YelpColors.DarkSurface1,
    onSurface       = YelpColors.DarkTextPrimary,
    surfaceVariant  = YelpColors.DarkSurface2,
    outline         = YelpColors.DarkDivider,
    error           = YelpColors.Closed,
)

@Composable
fun YelpTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) YelpDark else YelpLight,
    typography = YelpTypography,
    content = content,
)
```

## 2. Typography

Yelp's type family is **Open Sans** (SIL OFL via Google Fonts — drop the TTFs in `res/font/`). Heavy headers/numbers (700-800), 400 body, 600 meta — scanning over reading.

```kotlin
// ui/theme/YelpType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val OpenSans = FontFamily(
    Font(R.font.opensans_regular,    FontWeight.Normal),
    Font(R.font.opensans_semibold,   FontWeight.SemiBold),
    Font(R.font.opensans_bold,       FontWeight.Bold),
    Font(R.font.opensans_extrabold,  FontWeight.ExtraBold),
)

object YelpText {
    val ScreenTitle  = TextStyle(OpenSans, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.4).sp)
    val BizName      = TextStyle(OpenSans, fontWeight = FontWeight.ExtraBold, fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.3).sp)
    val Section      = TextStyle(OpenSans, fontWeight = FontWeight.Bold,      fontSize = 20.sp, lineHeight = 25.sp, letterSpacing = (-0.2).sp)
    val CardHeader   = TextStyle(OpenSans, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 21.sp)
    val Emphasis     = TextStyle(OpenSans, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp)
    val Body         = TextStyle(OpenSans, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 22.sp)
    val ReviewerName = TextStyle(OpenSans, fontWeight = FontWeight.Bold,      fontSize = 14.sp, lineHeight = 18.sp)
    val Meta         = TextStyle(OpenSans, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 18.sp)
    val Caption      = TextStyle(OpenSans, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 17.sp, letterSpacing = 0.1.sp)
    val Button       = TextStyle(OpenSans, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp)
    val Tab          = TextStyle(OpenSans, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Chip         = TextStyle(OpenSans, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 13.sp)
    val Link         = TextStyle(OpenSans, fontWeight = FontWeight.Bold,      fontSize = 14.sp, lineHeight = 18.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val YelpTypography = Typography(
    headlineLarge  = YelpText.ScreenTitle,
    headlineMedium = YelpText.BizName,
    titleMedium    = YelpText.Section,
    bodyMedium     = YelpText.Body,
    labelSmall     = YelpText.Tab,
)
```

## 3. Signature Components

### 5-Star Rating (half-star, Canvas-drawn)

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp

@Composable
fun YelpRatingBar(
    rating: Float,                 // 0f..5f, render to nearest 0.5
    starSize: Dp = 18.dp,
    dark: Boolean = false,
    modifier: Modifier = Modifier,
) {
    val empty = if (dark) YelpColors.StarEmptyDark else YelpColors.StarEmpty
    Canvas(modifier.size(width = starSize * 5 + 2.dp * 4, height = starSize)
        .semantics { contentDescription = "$rating out of 5 stars" }) {
        val s = starSize.toPx()
        val gap = 2.dp.toPx()
        repeat(5) { i ->
            val left = i * (s + gap)
            val fill = (rating - i).coerceIn(0f, 1f)
            drawStar(Offset(left, 0f), s, empty)
            if (fill > 0f) {
                clipRect(left, 0f, left + s * fill, s) { drawStar(Offset(left, 0f), s, YelpColors.StarFilled) }
            }
        }
    }
}

private fun androidx.compose.ui.graphics.drawscope.DrawScope.drawStar(
    o: Offset, size: Float, color: Color,
) {
    val cx = o.x + size / 2; val cy = o.y + size / 2
    val rOuter = size / 2; val rInner = rOuter * 0.42f
    val path = Path()
    for (k in 0 until 10) {
        val r = if (k % 2 == 0) rOuter else rInner
        val a = Math.toRadians((-90 + k * 36).toDouble())
        val x = cx + (r * Math.cos(a)).toFloat()
        val y = cy + (r * Math.sin(a)).toFloat()
        if (k == 0) path.moveTo(x, y) else path.lineTo(x, y)
    }
    path.close()
    drawPath(path, color)
}
```

### Interactive Rating Input

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Star
import androidx.compose.material.icons.outlined.StarBorder
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun YelpRatingInput(value: Float, onValueChange: (Float) -> Unit, starSize: Dp = 32.dp) {
    val haptics = LocalHapticFeedback.current
    Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
        (1..5).forEach { i ->
            Icon(
                imageVector = if (i <= value) Icons.Filled.Star else Icons.Outlined.StarBorder,
                contentDescription = "$i stars",
                tint = if (i <= value) YelpColors.StarFilled else YelpColors.StarEmpty,
                modifier = Modifier.size(starSize).clickable {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                    onValueChange(i.toFloat())
                },
            )
        }
    }
}
```

### Business Header

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun BusinessHeader(
    photoUrl: String, name: String, rating: Float, reviewCount: Int,
    priceCategory: String, isOpen: Boolean, hours: String,
) {
    Column(Modifier.fillMaxWidth()) {
        Box(Modifier.fillMaxWidth().height(184.dp)) {
            AsyncImage(model = photoUrl, contentDescription = null,
                modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
            Text("1 / 248 photos", style = YelpText.Caption, color = Color.White,
                modifier = Modifier.align(Alignment.BottomEnd).padding(14.dp)
                    .clip(RoundedCornerShape(6.dp)).background(Color.Black.copy(alpha = 0.6f))
                    .padding(horizontal = 10.dp, vertical = 4.dp))
        }
        Column(Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(name, style = YelpText.BizName, color = YelpColors.TextPrimary)
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                YelpRatingBar(rating)
                Text("%.1f".format(rating), style = YelpText.Emphasis, color = YelpColors.TextPrimary)
                Text("$reviewCount reviews", style = YelpText.Link, color = YelpColors.Link)
            }
            Text(priceCategory, style = YelpText.Meta, color = YelpColors.TextSecondary)
            Row(horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(if (isOpen) "Open" else "Closed",
                    style = YelpText.Meta.copy(fontWeight = FontWeight.Bold),
                    color = if (isOpen) YelpColors.Open else YelpColors.Closed)
                Text("· $hours", style = YelpText.Meta, color = YelpColors.TextSecondary)
            }
        }
        Box(Modifier.fillMaxWidth().height(8.dp).background(YelpColors.SectionBand)) // section band
    }
}
```

### Review Card

```kotlin
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.*
import androidx.compose.runtime.*
import androidx.compose.ui.graphics.Brush

@Composable
fun ReviewCard(
    initials: String, name: String, location: String, reviewerCount: Int,
    rating: Float, date: String, body: String,
) {
    var useful by remember { mutableStateOf(false) }
    Column(Modifier.fillMaxWidth().padding(vertical = 16.dp)) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(10.dp)) {
            Box(Modifier.size(40.dp).clip(CircleShape)
                .background(Brush.linearGradient(listOf(YelpColors.RedLogo, Color(0xFF8A1818)))),
                contentAlignment = Alignment.Center) {
                Text(initials, style = YelpText.Emphasis, color = Color.White)
            }
            Column {
                Text(name, style = YelpText.ReviewerName, color = YelpColors.TextPrimary)
                Text("$location · $reviewerCount reviews", style = YelpText.Caption, color = YelpColors.TextSecondary)
            }
        }
        Row(Modifier.fillMaxWidth().padding(top = 10.dp),
            horizontalArrangement = Arrangement.SpaceBetween, verticalAlignment = Alignment.CenterVertically) {
            YelpRatingBar(rating, starSize = 15.dp)
            Text(date, style = YelpText.Caption, color = YelpColors.TextSecondary)
        }
        Text(body, style = YelpText.Body, color = YelpColors.TextPrimary, modifier = Modifier.padding(top = 8.dp))
        Row(Modifier.padding(top = 10.dp), horizontalArrangement = Arrangement.spacedBy(18.dp)) {
            Vote(Icons.Filled.ThumbUp, "Useful 24", useful) { useful = !useful }
            Vote(Icons.Filled.SentimentSatisfied, "Funny 6", false) {}
            Vote(Icons.Filled.FavoriteBorder, "Cool 11", false) {}
        }
        HorizontalDivider(color = YelpColors.Divider, modifier = Modifier.padding(top = 16.dp))
    }
}

@Composable
private fun Vote(icon: androidx.compose.ui.graphics.vector.ImageVector, label: String, on: Boolean, onTap: () -> Unit) {
    val c = if (on) YelpColors.Red else YelpColors.TextSecondary
    Row(Modifier.clickable(onClick = onTap), verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(5.dp)) {
        Icon(icon, contentDescription = label, tint = c, modifier = Modifier.size(13.dp))
        Text(label, style = YelpText.Caption, color = c)
    }
}
```

### Primary Button & Category Chip

```kotlin
@Composable
fun YelpPrimaryButton(text: String, modifier: Modifier = Modifier, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        modifier
            .heightIn(min = 44.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) YelpColors.RedPressed else YelpColors.Red)
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) { Text(text, style = YelpText.Button, color = Color.White, modifier = Modifier.padding(horizontal = 26.dp, vertical = 13.dp)) }
}

@Composable
fun YelpCategoryChip(text: String, icon: androidx.compose.ui.graphics.vector.ImageVector, selected: Boolean) {
    Row(
        Modifier
            .clip(RoundedCornerShape(50))
            .background(if (selected) YelpColors.Red else YelpColors.SurfaceGray)
            .then(if (selected) Modifier else Modifier.border(1.dp, YelpColors.Divider, RoundedCornerShape(50)))
            .padding(horizontal = 16.dp, vertical = 9.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(7.dp),
    ) {
        Icon(icon, null, tint = if (selected) Color.White else YelpColors.Red, modifier = Modifier.size(13.dp))
        Text(text, style = YelpText.Chip, color = if (selected) Color.White else YelpColors.TextPrimary)
    }
}
```

## 4. Navigation

Yelp has a 5-tab bottom strip — Search, Nearby, Write, Activity, Me. On Android, model it as a `NavigationBar`; active is the brand Red with the filled icon (no Material tint pill).

```kotlin
@Composable
fun YelpBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = YelpColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Search"   to Icons.Filled.Search,
            "Nearby"   to Icons.Filled.Home,
            "Write"    to Icons.Filled.Create,
            "Activity" to Icons.Filled.Notifications,
            "Me"       to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = YelpText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = YelpColors.Red,
                    selectedTextColor = YelpColors.Red,
                    unselectedIconColor = Color(0xFF7E7E7E),
                    unselectedTextColor = Color(0xFF7E7E7E),
                    indicatorColor = Color.Transparent,  // no Material pill — Yelp has none
                ),
            )
        }
    }
}
```

The filter sheet is a Material 3 `ModalBottomSheet` (`#FFFFFF` / `#1E1E1E`, 12dp top corners) holding Sort, Price ($–$$$$ toggles), Distance, an Open-Now switch, and category chips, with a pinned full-width red Apply button. The business-detail screen uses a `TopAppBar` that is transparent over the photo header and fades to a solid bar with the compact business name when the header scrolls off (`enterAlwaysScrollBehavior` + an animated container color).

## 5. Motion

Yelp motion is quiet — 120–300ms ease-out, soft haptics.

| Moment | Compose recipe |
|--------|----------------|
| Photo header parallax | `LazyColumn` first item with `Modifier.graphicsLayer { translationY = scrollState.value * 0.5f }` |
| Compact title cross-fade | `TopAppBar` container color `animateColorAsState` driven by scroll offset past 120.dp |
| Rating increment | per-star `clickable` + `HapticFeedbackType.LongPress`; fill grows `tween(120)` |
| Vote tap | count `Text` scale `animateFloatAsState` spring bump 1f → 1.2f → 1f; icon tint → `Red` |
| Filter sheet | `ModalBottomSheet` (300ms slide, scrim alpha 0.4) |
| Tab switch | instant; active icon swaps to filled `Red`, no slide |
| Photo viewer | shared-element zoom into a full-screen `HorizontalPager` `tween(300)` |
| Check-in | outline chip → solid `Red` `tween(200)` + `HapticFeedbackType.Confirm`, then a `Snackbar` |

```kotlin
// Vote count bump — the canonical Yelp micro-interaction
val scale by animateFloatAsState(if (justVoted) 1.2f else 1f,
    animationSpec = spring(dampingRatio = 0.5f), label = "voteBump")
Text("Useful $count", Modifier.graphicsLayer { scaleX = scale; scaleY = scale })
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for each rating increment, `HapticFeedbackType.Confirm` on check-in, a soft `LocalView.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` on vote. Pull-to-refresh on search/activity uses a `Red` indicator.

## 6. Icons

Yelp's iconography is simple and line-based; `androidx.compose.material:material-icons-extended` covers it. The 5-star control is Canvas-drawn (§3), not an icon font, so half-stars render crisply.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Nearby (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Write (tab) | `square.and.pencil` | `Icons.Filled.Create` |
| Activity (tab) | `bell` / `waveform.path.ecg` | `Icons.Filled.Notifications` |
| Me (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Star (rating) | `star.fill` / `star` | Canvas-drawn star (§3) |
| Write a Review | `square.and.pencil` | `Icons.Filled.Create` |
| Save | `bookmark` / `bookmark.fill` | `Icons.Filled.BookmarkBorder` / `Icons.Filled.Bookmark` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Directions | `arrow.triangle.turn.up.right.diamond` | `Icons.Filled.Directions` |
| Call | `phone.fill` | `Icons.Filled.Call` |
| Useful | `hand.thumbsup` | `Icons.Filled.ThumbUp` |
| Funny | `face.smiling` | `Icons.Filled.SentimentSatisfied` |
| Cool | `heart` | `Icons.Filled.FavoriteBorder` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Map toggle | `map` | `Icons.Filled.Map` |
| Add photo | `camera` | `Icons.Filled.PhotoCamera` |
| Location pill | `chevron.down` | `Icons.Filled.ExpandMore` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; the Canvas star + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the photo header is full-bleed into the status-bar area with light-content system bars over the dark photo, dark-content over light content. The filter `ModalBottomSheet` and the editor respect `Modifier.imePadding()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen title, business name, section headers, body, meta, review text. Pin layout-sensitive text (10sp tab labels, chip text, photo-count chip, vote labels) via `dp`-derived sizes or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. The Canvas star scales with its `starSize` param, kept proportional to the adjacent rating number.
- **TalkBack**: rating bars expose `contentDescription = "{x.x} out of 5 stars"`; the rating input uses `Modifier.semantics { role = Role.Button; stateDescription = "$value of 5" }` and an `androidx…ProgressBarRangeInfo` style adjustable action; review cards announce "Review by {name}, {x} stars, {date}"; vote buttons toggle `stateDescription` ("Marked useful"). Never convey open/closed by color alone — keep the literal "Open"/"Closed" text.
- **Touch targets**: Material guidance is 48.dp. Give the 15-18.dp rating stars (input mode) and 13.dp vote rows a 48.dp hit area via padding; primary buttons are ≥ 44.dp; tab items are full-height tappable.
- **Contrast**: `#2B2B2B` on `#FFFFFF` passes WCAG AA for body at 14sp. The warm star `#F25C05` meets the 3:1 non-text contrast bar on white. Pair "Open" green `#2DA44E` with the word, never color alone.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable header parallax (static image) and the vote-count bump (instant); keep the rating fill (it conveys value).
- **Dark mode**: invert via the `Dark*` palette — `#161616`, NOT true black; `#2B2B2B` text becomes `#E6E6E4`; section bands become `#0C0C0C`; links shift `#0073BB → #4FA3D9`. The 5-star **filled** color stays `#F25C05` in both modes; only the empty color swaps to `#3A3A3A`. Do **not** enable Material You `dynamicColorScheme()` — Yelp's rationed-Red identity must hold regardless of wallpaper.
