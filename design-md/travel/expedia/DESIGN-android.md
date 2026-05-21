# Expedia (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Expedia's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, an M3 `Typography`, paste-ready `@Composable`s (property card, mode switch, One Key strip), navigation, motion, and haptics.

> The DESIGN.md tokens are platform-neutral. This guide keeps the *visual* identity (Expedia Yellow as the savings signal, Action Blue as interactive, navy review-score badge, the search-and-results loop) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Card` elevation, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for property photos. Do **not** enable Material You dynamic color — Expedia's yellow/blue/navy identity must hold regardless of wallpaper.

## 1. Color Tokens (Compose + M3)

```kotlin
// ui/theme/ExpediaColors.kt
import androidx.compose.ui.graphics.Color

object ExpediaColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF5F7FA)
    val SurfacePressed = Color(0xFFECEFF4)
    val Divider        = Color(0xFFE3E7ED)

    // Canvas & Surfaces (Dark) — navy-biased, NOT pure black
    val DarkCanvas   = Color(0xFF0E1116)
    val DarkSurface1 = Color(0xFF161B22)
    val DarkSurface2 = Color(0xFF1F2630)
    val DarkDivider  = Color(0xFF2A323E)

    // Text
    val TextPrimary     = Color(0xFF1A1F26)
    val TextSecondary   = Color(0xFF5A6573)
    val TextTertiary    = Color(0xFF8A95A3)
    val DarkTextPrimary   = Color(0xFFE8EBEF)
    val DarkTextSecondary = Color(0xFF9AA4B2)

    // Brand & interactive
    val Yellow        = Color(0xFFFFC94D) // savings/deal/loyalty — text on it is Navy
    val YellowDeep    = Color(0xFFFFB31A)
    val ActionBlue    = Color(0xFF1668E3) // interactive — white text on it
    val ActionPressed = Color(0xFF0F4FB0)
    val Navy          = Color(0xFF00355F) // brand/trust
    val NavySoft      = Color(0xFF14416B)
    val OneKeyGold    = Color(0xFFF5C518) // rewards-only

    // Semantic
    val Success = Color(0xFF1A8B4B)
    val Error   = Color(0xFFD93A3A)
    val Warning = Color(0xFFE8830C)
}

// Review-score badge color for a 0–10 guest score
fun reviewBadgeColor(score: Double): Color = when {
    score >= 9.0 -> ExpediaColors.Success     // Wonderful
    score >= 8.0 -> ExpediaColors.ActionBlue  // Excellent
    score >= 7.0 -> ExpediaColors.NavySoft    // Good
    score >= 6.0 -> ExpediaColors.TextSecondary
    else         -> ExpediaColors.TextTertiary
}
```

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val ExpediaLight = lightColorScheme(
    primary        = ExpediaColors.ActionBlue,
    onPrimary      = ExpediaColors.Canvas,
    secondary      = ExpediaColors.Yellow,
    onSecondary    = ExpediaColors.Navy,
    background      = ExpediaColors.SurfaceGray,
    onBackground    = ExpediaColors.TextPrimary,
    surface         = ExpediaColors.Canvas,
    onSurface       = ExpediaColors.TextPrimary,
    surfaceVariant  = ExpediaColors.SurfacePressed,
    outline         = ExpediaColors.Divider,
    error           = ExpediaColors.Error,
)

private val ExpediaDark = darkColorScheme(
    primary        = ExpediaColors.ActionBlue,
    onPrimary      = ExpediaColors.Canvas,
    secondary      = ExpediaColors.Yellow,
    onSecondary    = ExpediaColors.Navy,
    background      = ExpediaColors.DarkCanvas,
    onBackground    = ExpediaColors.DarkTextPrimary,
    surface         = ExpediaColors.DarkSurface1,
    onSurface       = ExpediaColors.DarkTextPrimary,
    surfaceVariant  = ExpediaColors.DarkSurface2,
    outline         = ExpediaColors.DarkDivider,
    error           = ExpediaColors.Error,
)

@Composable
fun ExpediaTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) ExpediaDark else ExpediaLight,
    typography  = ExpediaTypography,
    content     = content,
)
```

## 2. Typography (M3)

Expedia Sans is proprietary — drop the TTFs in `res/font/` only if licensed; otherwise use Inter (closest free substitute). Prices and review scores need tabular figures.

```kotlin
// ui/theme/ExpediaType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.unit.sp

// If licensed, swap in expedia_sans_* font resources.
val ExpediaSans = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),
    Font(R.font.inter_semibold,  FontWeight.SemiBold),
    Font(R.font.inter_bold,      FontWeight.Bold),
    Font(R.font.inter_extrabold, FontWeight.ExtraBold),
)

object ExpediaText {
    val Display      = TextStyle(ExpediaSans, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val ScreenTitle  = TextStyle(ExpediaSans, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section      = TextStyle(ExpediaSans, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val CardTitle    = TextStyle(ExpediaSans, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Body         = TextStyle(ExpediaSans, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val CardSubtitle = TextStyle(ExpediaSans, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Meta         = TextStyle(ExpediaSans, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Strike       = TextStyle(ExpediaSans, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 16.sp, textDecoration = TextDecoration.LineThrough)
    val Badge        = TextStyle(ExpediaSans, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
    val OneKeyLine   = TextStyle(ExpediaSans, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 14.sp, letterSpacing = 0.1.sp)
    val Button       = TextStyle(ExpediaSans, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab          = TextStyle(ExpediaSans, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val PriceNow     = TextStyle(ExpediaSans, fontWeight = FontWeight.ExtraBold, fontSize = 18.sp, lineHeight = 22.sp)
    val ScoreNum     = TextStyle(ExpediaSans, fontWeight = FontWeight.ExtraBold, fontSize = 15.sp, lineHeight = 16.sp)
}

val ExpediaTypography = Typography(
    headlineLarge  = ExpediaText.Display,
    headlineMedium = ExpediaText.ScreenTitle,
    titleLarge     = ExpediaText.Section,
    titleMedium    = ExpediaText.CardTitle,
    bodyMedium     = ExpediaText.Body,
    labelSmall     = ExpediaText.Tab,
)
```

> For tabular figures use `TextStyle(... fontFeatureSettings = "tnum")` on `PriceNow` / `ScoreNum`, or apply `Modifier` with a monospaced numeral font if your licensed Expedia Sans ships one.

## 3. Signature Components

### Property Result Card (@Composable)

```kotlin
import androidx.compose.animation.core.Spring
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.outlined.FavoriteBorder
import androidx.compose.material3.Card
import androidx.compose.material3.CardDefaults
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun PropertyCard(
    imageUrl: String,
    dealFlag: String?,
    title: String,
    location: String,
    score: Double,
    scoreWord: String,
    reviewCount: Int,
    strikePrice: Int?,
    nightlyPrice: Int,
    oneKeyEarn: Int,
    modifier: Modifier = Modifier,
) {
    var saved by remember { mutableStateOf(false) }
    val heartScale by animateFloatAsState(if (saved) 1f else 1f, spring(Spring.DampingRatioMediumBouncy), label = "heart")
    val haptics = LocalHapticFeedback.current

    Card(
        modifier = modifier.fillMaxWidth(),
        shape = RoundedCornerShape(16.dp),
        colors = CardDefaults.cardColors(containerColor = ExpediaColors.Canvas),
        border = androidx.compose.foundation.BorderStroke(0.5.dp, ExpediaColors.Divider),
        elevation = CardDefaults.cardElevation(defaultElevation = 3.dp),
    ) {
        Column {
            Box(Modifier.fillMaxWidth().aspectRatio(16f / 10f)) {
                AsyncImage(
                    model = imageUrl, contentDescription = title,
                    modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop,
                )
                if (dealFlag != null) {
                    Text(
                        dealFlag, style = ExpediaText.Badge, color = ExpediaColors.Navy,
                        modifier = Modifier
                            .padding(12.dp).align(Alignment.TopStart)
                            .clip(RoundedCornerShape(6.dp))
                            .background(ExpediaColors.Yellow)
                            .padding(vertical = 4.dp, horizontal = 9.dp),
                    )
                }
                Box(
                    Modifier
                        .padding(10.dp).align(Alignment.TopEnd)
                        .size(30.dp).scale(heartScale)
                        .clip(RoundedCornerShape(50))
                        .background(Color.Black.copy(alpha = 0.4f))
                        .clickable {
                            haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                            saved = !saved
                        },
                    contentAlignment = Alignment.Center,
                ) {
                    Icon(
                        if (saved) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
                        contentDescription = if (saved) "Saved" else "Save",
                        tint = if (saved) ExpediaColors.ActionBlue else Color.White,
                        modifier = Modifier.size(18.dp),
                    )
                }
            }

            Column(Modifier.padding(start = 14.dp, end = 14.dp, top = 12.dp, bottom = 14.dp)) {
                Text(title, style = ExpediaText.CardTitle, color = ExpediaColors.TextPrimary)
                Text(location, style = ExpediaText.CardSubtitle, color = ExpediaColors.TextSecondary,
                    modifier = Modifier.padding(top = 3.dp))

                Row(
                    Modifier.padding(top = 8.dp),
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(6.dp),
                ) {
                    Text(
                        String.format("%.1f", score),
                        style = ExpediaText.ScoreNum, color = Color.White,
                        modifier = Modifier
                            .clip(RoundedCornerShape(6.dp))
                            .background(reviewBadgeColor(score))
                            .padding(vertical = 3.dp, horizontal = 7.dp),
                    )
                    Text(scoreWord, style = ExpediaText.Badge.copy(fontWeight = FontWeight.Bold), color = ExpediaColors.TextPrimary)
                    Text("· ${"%,d".format(reviewCount)} reviews", style = ExpediaText.Badge, color = ExpediaColors.TextSecondary)
                }

                Row(
                    Modifier.padding(top = 10.dp),
                    verticalAlignment = Alignment.Bottom,
                    horizontalArrangement = Arrangement.spacedBy(6.dp),
                ) {
                    if (strikePrice != null) {
                        Text("$$strikePrice", style = ExpediaText.Strike, color = ExpediaColors.TextTertiary)
                    }
                    Text("$$nightlyPrice", style = ExpediaText.PriceNow, color = ExpediaColors.TextPrimary)
                    Text("/ night", style = ExpediaText.Badge, color = ExpediaColors.TextSecondary)
                }

                Row(
                    Modifier.padding(top = 7.dp),
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(5.dp),
                ) {
                    Box(Modifier.size(8.dp).clip(RoundedCornerShape(50)).background(ExpediaColors.OneKeyGold))
                    Text("Earn ${"%,d".format(oneKeyEarn)} One Key cash", style = ExpediaText.OneKeyLine, color = ExpediaColors.OneKeyGold)
                }
            }
        }
    }
}
```

### Segmented Mode Switch (@Composable)

```kotlin
@Composable
fun ModeSwitch(selected: Int, onSelect: (Int) -> Unit, modifier: Modifier = Modifier) {
    val modes = listOf("Stays", "Flights", "Cars", "Bundle")
    Row(modifier.fillMaxWidth().padding(horizontal = 16.dp), horizontalArrangement = Arrangement.spacedBy(8.dp)) {
        modes.forEachIndexed { i, label ->
            val active = selected == i
            Box(
                Modifier
                    .weight(1f)
                    .clip(RoundedCornerShape(10.dp))
                    .background(if (active) ExpediaColors.Yellow else ExpediaColors.SurfaceGray)
                    .then(if (active) Modifier else Modifier.border(0.5.dp, ExpediaColors.Divider, RoundedCornerShape(10.dp)))
                    .clickable { onSelect(i) }
                    .padding(vertical = 9.dp),
                contentAlignment = Alignment.Center,
            ) {
                Text(label, style = ExpediaText.Badge, color = if (active) ExpediaColors.Navy else ExpediaColors.TextSecondary)
            }
        }
    }
}
```

### One Key Rewards Strip (@Composable)

```kotlin
@Composable
fun OneKeyStrip(balance: String, modifier: Modifier = Modifier) {
    Row(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(12.dp))
            .background(ExpediaColors.SurfaceGray)
            .border(0.5.dp, ExpediaColors.Divider, RoundedCornerShape(12.dp))
            .padding(vertical = 14.dp, horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(
            Modifier.size(36.dp).clip(RoundedCornerShape(50)).background(ExpediaColors.OneKeyGold),
            contentAlignment = Alignment.Center,
        ) { Text("1K", style = ExpediaText.Badge.copy(fontWeight = FontWeight.ExtraBold, fontSize = 13.sp), color = Color(0xFF1B1B1B)) }
        Column(Modifier.weight(1f)) {
            Text("One Key cash available", style = ExpediaText.Badge.copy(fontWeight = FontWeight.Bold, fontSize = 13.sp), color = ExpediaColors.TextPrimary)
            Text("Apply at checkout on any stay, flight, or car", style = ExpediaText.OneKeyLine.copy(color = ExpediaColors.TextSecondary))
        }
        Text(balance, style = ExpediaText.PriceNow.copy(fontSize = 16.sp), color = ExpediaColors.OneKeyGold)
    }
}
```

## 4. Navigation

Expedia uses a 5-tab bottom bar (Search / Saved / Trips / Support / Account). On Android, model it as a Material 3 `NavigationBar`. Active is Action Blue; there is no Material tint pill.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun ExpediaBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = ExpediaColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Search"  to Icons.Filled.Search,
            "Saved"   to Icons.Filled.FavoriteBorder,
            "Trips"   to Icons.Filled.Work,
            "Support" to Icons.Filled.HelpOutline,
            "Account" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = ExpediaText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = ExpediaColors.ActionBlue,
                    selectedTextColor = ExpediaColors.ActionBlue,
                    unselectedIconColor = ExpediaColors.TextTertiary,
                    unselectedTextColor = ExpediaColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Expedia has none
                ),
            )
        }
    }
}
```

The detail screen uses a collapsing top app bar (`TopAppBar` with `enterAlwaysScrollBehavior`) that is transparent over the photo hero and solidifies to `Canvas`/`DarkCanvas` on scroll, with a sticky bottom CTA bar (`BottomAppBar`, elevation 8.dp) holding the "Reserve" and "Bundle & save" buttons.

## 5. Motion

| Moment | Compose recipe |
|--------|----------------|
| Save heart | `animateFloatAsState` 1 → 1.2 → 1 via `spring(DampingRatioMediumBouncy)`; soft haptic on toggle |
| Mode switch | `animateColorAsState(tween(200))` on background + text color |
| Price drop | `animateColorAsState` flash to `Yellow` then back to `TextPrimary` over ~500ms |
| Bundle reveal | `AnimatedVisibility(expandVertically(tween(240)) + scaleIn(initialScale = 0.96f) + fadeIn())` |
| Card → detail | shared element via `SharedTransitionLayout` (Compose 1.7+); push `tween(320)` |
| Filter sheet | `ModalBottomSheet` slide-up `tween(300)`; result count `animateIntAsState` |
| Sticky CTA bar | `AnimatedVisibility(slideInVertically { it } + fadeIn(), tween(200))` past fold |
| Pull-to-refresh | `PullToRefreshContainer` tinted `Yellow` |

```kotlin
val priceColor by animateColorAsState(
    targetValue = if (justDropped) ExpediaColors.Yellow else ExpediaColors.TextPrimary,
    animationSpec = tween(180), label = "priceFlash",
)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on save, mode switch, and date-range commit; a success cue on booking confirmation. Auto-applied loyalty discounts are silent — show a snackbar only on error.

## 6. Icons

Use `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Saved (tab) | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Trips (tab) | `suitcase` | `Icons.Filled.Work` |
| Support (tab) | `questionmark.circle` | `Icons.Filled.HelpOutline` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Save heart (card) | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Sort | `arrow.up.arrow.down` | `Icons.Filled.SwapVert` |
| Flight | `airplane` | `Icons.Filled.Flight` |
| Stay | `bed.double` | `Icons.Filled.Hotel` |
| Car | `car` | `Icons.Filled.DirectionsCar` |
| Bundle | `shippingbox` | `Icons.Filled.Inventory2` |
| Calendar | `calendar` | `Icons.Filled.CalendarMonth` |
| Travelers | `person.2` | `Icons.Filled.Group` |
| Map | `map` | `Icons.Filled.Map` |
| One Key | `key.fill` | `Icons.Filled.VpnKey` |
| Price trend up | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24**, `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; light canvas wants dark-content system bars (light-content in dark mode). The collapsing top bar respects the camera cutout; the sticky CTA respects the gesture nav inset.
- **Font scaling**: `sp` honors the user's font scale — keep it on display, titles, body, prices. Pin layout-sensitive text (10sp tab labels, deal flags/badges, One Key lines, the review-score number) via a fixed-density wrapper.
- **Font choice**: Expedia Sans is proprietary — use it only if licensed; Inter is the parity substitute. Apply tabular figures (`fontFeatureSettings = "tnum"`) to prices and scores so list columns align.
- **TalkBack**: label the card "Property: {title}, {scoreWord} {score} out of 10, {reviewCount} reviews, {nightlyPrice} dollars per night"; the save toggle "Save {title}, {checked/unchecked}"; expose the One Key earn line as informative text.
- **Touch targets**: Material guidance is 48.dp. The 30.dp save heart and segment pills should expand hit area to 48.dp via padding; bottom-bar items are 48.dp; primary buttons ≥ 48.dp tall.
- **Contrast**: Navy `#00355F` on Yellow `#FFC94D` and white on Action Blue `#1668E3` pass WCAG AA at button sizes; validate any custom yellow pairing with a contrast checker.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, drop the heart bounce and bundle scale; substitute a `Crossfade`; keep the price-drop color flash (conveys state).
- **Dark mode**: invert via the `Dark*` palette — `#0E1116` navy-biased charcoal, NOT true black; `#1A1F26` text becomes `#E8EBEF`. Keep brand Yellow/ActionBlue/OneKeyGold fixed. Shadows are faint on dark, so add a 0.5dp `DarkDivider` border to floating sheets as the elevation cue. Do **not** enable `dynamicColorScheme()` — Expedia's identity must hold regardless of wallpaper.
