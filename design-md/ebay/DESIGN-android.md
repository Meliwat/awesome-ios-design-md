# eBay (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports eBay's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (eBay's clean white catalog canvas, four-color brand mark used with discipline, the bid-vs-Buy-It-Now distinction) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a tabular-figure countdown, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/EbayColors.kt
import androidx.compose.ui.graphics.Color

object EbayColors {
    // Canvas & Surfaces
    val Canvas         = Color(0xFFFFFFFF)
    val Surface        = Color(0xFFF7F7F7)
    val Divider        = Color(0xFFE5E5E5)
    val SurfacePressed = Color(0xFFEFEFEF)

    // Text
    val TextPrimary    = Color(0xFF191919)
    val TextSecondary  = Color(0xFF707070)
    val TextTertiary   = Color(0xFF9B9B9B)

    // Four-color brand
    val Blue           = Color(0xFF0064D2)
    val BluePressed    = Color(0xFF0050A8)
    val Red            = Color(0xFFE53238)
    val Yellow         = Color(0xFFF5AF02)
    val Green          = Color(0xFF86B817)

    // Semantic
    val FreeShip       = Color(0xFF258635)
    val ErrorRed       = Color(0xFFC7000B)
}
```

Wire it into a Material 3 `lightColorScheme`. eBay is light-first.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val EbayScheme = lightColorScheme(
    primary        = EbayColors.Blue,
    onPrimary      = Color.White,
    background     = EbayColors.Canvas,
    onBackground   = EbayColors.TextPrimary,
    surface        = EbayColors.Canvas,
    onSurface      = EbayColors.TextPrimary,
    surfaceVariant = EbayColors.Surface,
    outline        = EbayColors.Divider,
    error          = EbayColors.ErrorRed,
)

@Composable
fun EbayTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = EbayScheme, typography = EbayTypography, content = content)
```

## 2. Typography

Market Sans is proprietary. Drop substitute Inter TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto). Use tabular figures for the countdown.

```kotlin
// ui/theme/EbayType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val MarketSans = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object EbayText {
    val TitleLarge = TextStyle(MarketSans, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val Listing    = TextStyle(MarketSans, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp)
    val Section    = TextStyle(MarketSans, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Price      = TextStyle(MarketSans, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val CardPrice  = TextStyle(MarketSans, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val CardTitle  = TextStyle(MarketSans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp)
    val Body       = TextStyle(MarketSans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 23.sp)
    val Button     = TextStyle(MarketSans, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 20.sp)
    val Badge      = TextStyle(MarketSans, fontWeight = FontWeight.Bold,     fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.2.sp)
    val Meta       = TextStyle(MarketSans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val TimeLeft   = TextStyle(MarketSans, fontWeight = FontWeight.Bold,     fontSize = 13.sp, lineHeight = 16.sp, fontFeatureSettings = "tnum")
    val Tab        = TextStyle(MarketSans, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
    val Condition  = TextStyle(MarketSans, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val EbayTypography = Typography(
    headlineLarge = EbayText.TitleLarge,
    headlineSmall = EbayText.Section,
    titleMedium   = EbayText.CardPrice,
    bodyMedium    = EbayText.Body,
    labelSmall    = EbayText.Tab,
)
```

## 3. Signature Components

### Bid vs Buy-It-Now Badge (the signature)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

sealed interface BuyMechanic {
    data object BuyItNow : BuyMechanic
    data class Auction(val bids: Int) : BuyMechanic
}

@Composable
fun BuyMechanicBadge(
    mechanic: BuyMechanic,
    bestOffer: Boolean = false,
    modifier: Modifier = Modifier,
) {
    Row(modifier, verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp)) {
        val isBin = mechanic is BuyMechanic.BuyItNow
        Box(
            Modifier
                .clip(RoundedCornerShape(4.dp)) // squarer than CTAs — these are tags
                .background(if (isBin) EbayColors.Blue.copy(alpha = 0.10f) else EbayColors.Surface)
                .padding(horizontal = 8.dp, vertical = 4.dp),
        ) {
            Text(
                text = when (mechanic) {
                    BuyMechanic.BuyItNow -> "BUY IT NOW"
                    is BuyMechanic.Auction -> "${mechanic.bids} BID${if (mechanic.bids == 1) "" else "S"}"
                },
                style = EbayText.Badge,
                color = if (isBin) EbayColors.Blue else EbayColors.TextPrimary,
            )
        }
        if (bestOffer) {
            Text("or Best Offer", style = EbayText.Meta, color = EbayColors.Green)
        }
    }
}
```

### "Time Left" Countdown

```kotlin
@Composable
fun TimeLeft(secondsRemaining: Int, modifier: Modifier = Modifier) {
    val urgent = secondsRemaining < 86_400 // < 24h
    val d = secondsRemaining / 86_400
    val h = (secondsRemaining % 86_400) / 3_600
    val m = (secondsRemaining % 3_600) / 60
    val s = secondsRemaining % 60
    val label = when {
        d > 0 -> "${d}d ${h}h left"
        h > 0 -> "${h}h ${m}m left"
        else  -> "${m}m ${s}s left"
    }
    Text(
        label,
        style = EbayText.TimeLeft, // fontFeatureSettings = "tnum" → no jitter
        color = if (urgent) EbayColors.Red else EbayColors.TextSecondary,
        modifier = modifier,
    )
}
```

### Watch Heart

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.outlined.FavoriteBorder
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import kotlinx.coroutines.launch

@Composable
fun WatchHeart(
    watching: Boolean,
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
                    .size(36.dp).clip(CircleShape).background(Color.Black.copy(alpha = 0.40f))
                else Modifier
            )
            .scale(scale.value)
            .clickable(remember { MutableInteractionSource() }, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                scope.launch {
                    scale.animateTo(1.2f, tween(130))
                    scale.animateTo(1f, tween(130))
                }
                onToggle()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            if (watching) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
            contentDescription = if (watching) "Watching this item" else "Add to watchlist",
            tint = if (watching) EbayColors.Red else if (overPhoto) Color.White else EbayColors.TextPrimary,
            modifier = Modifier.size(size.dp),
        )
    }
}
```

### Listing Card

```kotlin
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.text.style.TextDecoration
import coil.compose.AsyncImage

@Composable
fun ListingCard(
    title: String,
    photoUrl: String,
    price: String,
    original: String?,
    mechanic: BuyMechanic,
    bestOffer: Boolean,
    freeShipping: Boolean,
    secondsLeft: Int?,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    var watching by remember { mutableStateOf(false) }
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val cardScale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "card")

    Column(
        modifier
            .scale(cardScale)
            .shadow(4.dp, RoundedCornerShape(8.dp), spotColor = Color.Black.copy(alpha = 0.06f))
            .clip(RoundedCornerShape(8.dp))
            .background(EbayColors.Canvas)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(8.dp),
        verticalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Box {
            AsyncImage(
                model = photoUrl,
                contentDescription = title,
                modifier = Modifier.fillMaxWidth().aspectRatio(1f).clip(RoundedCornerShape(8.dp)),
                contentScale = ContentScale.Crop,
            )
            WatchHeart(watching, { watching = !watching },
                modifier = Modifier.align(Alignment.TopEnd).padding(8.dp))
        }
        Text(title, style = EbayText.CardTitle, color = EbayColors.TextPrimary, maxLines = 2)
        Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(price, style = EbayText.CardPrice, color = EbayColors.TextPrimary)
            if (original != null) {
                Text(original, style = EbayText.Meta, color = EbayColors.TextTertiary,
                     textDecoration = TextDecoration.LineThrough)
            }
        }
        BuyMechanicBadge(mechanic, bestOffer)
        Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            if (freeShipping) Text("Free shipping", style = EbayText.Meta, color = EbayColors.FreeShip)
            if (secondsLeft != null) TimeLeft(secondsLeft)
        }
    }
}
```

### Primary / Bid Buttons

```kotlin
enum class EbayButtonStyle { Filled, BidOutline }

@Composable
fun EbayButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    style: EbayButtonStyle = EbayButtonStyle.Filled,
) {
    val haptics = LocalHapticFeedback.current
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "btn")
    val filled = style == EbayButtonStyle.Filled
    Box(
        modifier
            .fillMaxWidth()
            .scale(scale)
            .clip(CircleShape) // full pill
            .then(
                if (filled) Modifier.background(if (pressed) EbayColors.BluePressed else EbayColors.Blue)
                else Modifier.border(1.5.dp, EbayColors.Blue, CircleShape)
            )
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            }
            .padding(vertical = if (filled) 15.dp else 13.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = EbayText.Button, color = if (filled) Color.White else EbayColors.Blue)
    }
}
```

## 4. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. eBay's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 97%-opaque white surface. **Active tint is eBay Blue** — blue is the active indicator.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun EbayBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = EbayColors.Canvas.copy(alpha = 0.97f), tonalElevation = 0.dp) {
        val items = listOf(
            "Home"          to Icons.Filled.Home,
            "Saved"         to Icons.Filled.Favorite,
            "Notifications" to Icons.Filled.Notifications,
            "My eBay"       to Icons.Filled.Person,
            "Selling"       to Icons.Filled.Sell,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = EbayText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = EbayColors.Blue,
                    selectedTextColor   = EbayColors.Blue,
                    unselectedIconColor = EbayColors.TextSecondary,
                    unselectedTextColor = EbayColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // no Material pill — eBay has none
                ),
            )
        }
    }
}
```

The Notifications tab carries a red count badge when non-empty — wrap its `Icon` in `BadgedBox`.

## 5. Navigation

The top bar carries the eBay multicolor wordmark (left — e red, B blue, a yellow, y green; equity-locked) and cart + notification icons (right). Below it, a pinned search field — a `#F7F7F7` `RoundedCornerShape(8.dp)` box, 44.dp tall, 1.dp `EbayColors.Divider` border, with a trailing blue camera icon for visual search. On scroll, collapse to a compact bar; Android has no live blur, so use the 97%-opaque white surface. The listing detail uses a bottom sticky bar (`Scaffold` `bottomBar`) with the price and a full-width blue "Buy It Now" / "Place bid" pill.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Watch-heart toggle | `Animatable` keyframes 1 → 1.2 → 1 over 260ms; `HapticFeedbackType.LongPress` |
| Bid placement | confirm `ModalBottomSheet`; on success `animateIntAsState` ticks the bid number + increments the count, medium haptic |
| "Time left" tick | a 1s `LaunchedEffect` loop decrementing seconds; `fontFeatureSettings = "tnum"` keeps digits aligned |
| Card tap | `animateFloatAsState` 1 → 0.98 `spring(dampingRatio = 0.8f)` |
| Photo carousel | `HorizontalPager` snap; a light haptic on `pagerState.currentPage` change |
| Sticky bar reveal | `AnimatedVisibility` + `slideInVertically` as the hero scrolls off |

```kotlin
// "Time left" countdown loop
var seconds by remember { mutableIntStateOf(initialSeconds) }
LaunchedEffect(Unit) {
    while (seconds > 0) { kotlinx.coroutines.delay(1000); seconds-- }
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's `.medium` impact.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity with eBay's custom glyphs, export them as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Watch | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Visual search | `camera` | `Icons.Filled.PhotoCamera` |
| Star (feedback) | `star.fill` | `Icons.Filled.Star` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Cart | `cart` / `cart.fill` | `Icons.Filled.ShoppingCart` |
| Filters | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Saved (tab) | `heart.fill` | `Icons.Filled.Favorite` |
| Notifications (tab) | `bell.fill` | `Icons.Filled.Notifications` |
| My eBay (tab) | `person.fill` | `Icons.Filled.Person` |
| Selling (tab) | `tag.fill` | `Icons.Filled.Sell` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the white canvas wants `WindowCompat` dark-content system bars. Apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` or `Scaffold` insets; the listing photo carousel bleeds full-width while the sticky bar clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on listing title, price, body, card title (2-line clamp). Pin layout-sensitive units (buy-mechanic badge, condition tag, "Time left", tab labels) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Tabular figures**: `fontFeatureSettings = "tnum"` on the countdown so the seconds never reflow as they tick.
- **TalkBack**: never rely on color alone for the buy mechanic — give the badge a `contentDescription` (`"Buy It Now"` / `"Auction, 12 bids"`); mark the watch-heart with `Role.Switch` + a `stateDescription`; read "Time left" as a full phrase ("1 day 4 hours remaining").
- **Touch targets**: Material guidance is 48.dp minimum. The CTA clears it; ensure the 36.dp watch-heart backing and gallery floating controls have ≥48.dp effective touch via padding.
- **Contrast**: `#707070` on `#FFFFFF` passes WCAG AA at 13sp+. eBay Yellow `#F5AF02` fails as small text on white — use it for star fills/accents only, never body copy.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — eBay's four-color brand mark is equity-locked and must not shift with wallpaper; keep the fixed white canvas and exact brand hues.
