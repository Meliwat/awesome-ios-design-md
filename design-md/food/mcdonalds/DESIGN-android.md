# McDonald's (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports McDonald's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (MyMcDonald's Rewards card, deal tile, order-mode selector, the elevated Order FAB bottom bar), navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Golden Arches Yellow `#FFC72C` with mandatory `#1A1A1A` text, McDonald's Red `#DA291C`, the rewards points hero, the glowing center Order FAB) while making everything idiomatic Android — `NavigationBar` + a `FloatingActionButton` docked in a `Scaffold`, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for product imagery. No color extraction — McDonald's palette is a fixed two-color brand set, so Palette is not used. Light-first; a full dark scheme is provided (near-black `#121212`).

## 1. Color Tokens

```kotlin
// ui/theme/McdColors.kt
import androidx.compose.ui.graphics.Color

object McdColors {
    // Brand
    val Yellow        = Color(0xFFFFC72C)
    val YellowPressed = Color(0xFFE6B015)
    val Red           = Color(0xFFDA291C)
    val RedPressed    = Color(0xFFB71F14)
    val DarkRed       = Color(0xFFB0210E)

    // Text on yellow — mandatory, never white
    val OnYellow = Color(0xFF1A1A1A)

    // Canvas & Surfaces (Light)
    val Canvas    = Color(0xFFFFFFFF)
    val Surface1L = Color(0xFFF4F4F5)
    val Surface2L = Color(0xFFEAEAEB)
    val DividerL  = Color(0xFFE3E3E5)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF121212) // near-black, NOT pure black
    val DarkSurface1 = Color(0xFF1C1C1E)
    val DarkSurface2 = Color(0xFF2A2A2A)
    val DarkDivider  = Color(0xFF2C2C2E)

    // Text
    val TextPrimaryL   = Color(0xFF1A1A1A)
    val TextSecondaryL = Color(0xFF5C5C5C)
    val TextTertiaryL  = Color(0xFF8A8A8A)
    val TextPrimaryD   = Color(0xFFF2F2F2)
    val TextSecondaryD = Color(0xFFA0A0A0)

    // Semantic
    val Success = Color(0xFF2B8A3E)
    val Warning = Color(0xFFE6A700)
    val Info    = Color(0xFF2E6DB4)
}
```

Wire it into both schemes. McDonald's is light-first; the dark scheme uses the signature `#121212` charcoal, never true black. Yellow and red are identical across modes; `onPrimary` is `#1A1A1A` (the mandatory text-on-yellow color).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val McdLight = lightColorScheme(
    primary        = McdColors.Yellow,
    onPrimary      = McdColors.OnYellow,   // #1A1A1A — never white
    secondary      = McdColors.Red,
    onSecondary    = Color.White,
    background      = McdColors.Canvas,
    onBackground    = McdColors.TextPrimaryL,
    surface         = McdColors.Surface1L,
    onSurface       = McdColors.TextPrimaryL,
    surfaceVariant  = McdColors.Surface2L,
    outline         = McdColors.DividerL,
    error           = McdColors.Red,
)

private val McdDark = darkColorScheme(
    primary        = McdColors.Yellow,
    onPrimary      = McdColors.OnYellow,   // still #1A1A1A in dark
    secondary      = McdColors.Red,
    onSecondary    = Color.White,
    background      = McdColors.DarkCanvas,
    onBackground    = McdColors.TextPrimaryD,
    surface         = McdColors.DarkSurface1,
    onSurface       = McdColors.TextPrimaryD,
    surfaceVariant  = McdColors.DarkSurface2,
    outline         = McdColors.DarkDivider,
    error           = McdColors.Red,
)

@Composable
fun McdTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) McdDark else McdLight,
    typography = McdTypography,
    content = content,
)
```

## 2. Typography

McDonald's uses proprietary **Speedee** — drop the licensed TTFs in `res/font/`. Weight carries hierarchy (800/900); the rewards number is the loudest. Numerals are tabular for points/prices/timers.

```kotlin
// ui/theme/McdType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Speedee = FontFamily(
    Font(R.font.speedee_regular, FontWeight.Normal),
    Font(R.font.speedee_medium,  FontWeight.Medium),
    Font(R.font.speedee_bold,    FontWeight.Bold),
)

object McdText {
    val ScreenTitle   = TextStyle(Speedee, fontWeight = FontWeight.Black,    fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.4).sp)
    val RewardsNumber = TextStyle(Speedee, fontWeight = FontWeight.Black,    fontSize = 28.sp, lineHeight = 30.sp, letterSpacing = (-0.5).sp)
    val Hero          = TextStyle(Speedee, fontWeight = FontWeight.ExtraBold,fontSize = 26.sp, lineHeight = 33.sp, letterSpacing = (-0.3).sp)
    val Greeting      = TextStyle(Speedee, fontWeight = FontWeight.ExtraBold,fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.4).sp)
    val Section       = TextStyle(Speedee, fontWeight = FontWeight.ExtraBold,fontSize = 19.sp, lineHeight = 25.sp, letterSpacing = (-0.3).sp)
    val CardTitle     = TextStyle(Speedee, fontWeight = FontWeight.ExtraBold,fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.3).sp)
    val Body          = TextStyle(Speedee, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val DealTitle     = TextStyle(Speedee, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 21.sp)
    val Price         = TextStyle(Speedee, fontWeight = FontWeight.Bold,     fontSize = 14.sp, lineHeight = 20.sp)
    val Meta          = TextStyle(Speedee, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val Badge         = TextStyle(Speedee, fontWeight = FontWeight.ExtraBold,fontSize = 12.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
    val Button        = TextStyle(Speedee, fontWeight = FontWeight.ExtraBold,fontSize = 15.sp, lineHeight = 15.sp, letterSpacing = 0.1.sp)
    val Caption       = TextStyle(Speedee, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 17.sp)
    val Tab           = TextStyle(Speedee, fontWeight = FontWeight.Bold,     fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val ModeLabel     = TextStyle(Speedee, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp)
}

val McdTypography = Typography(
    headlineLarge = McdText.ScreenTitle,
    headlineMedium = McdText.Section,
    titleMedium   = McdText.CardTitle,
    bodyMedium    = McdText.Body,
    labelSmall    = McdText.Tab,
)
```

## 3. Signature Components

### MyMcDonald's Rewards Card

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.withStyle
import androidx.compose.ui.unit.dp

@Composable
fun RewardsCard(points: Int, progress: Float, pointsToNext: Int, nextReward: String) {
    val fill by animateFloatAsState(progress, tween(600, easing = EaseOut), label = "rewardFill")
    var shown by remember { mutableIntStateOf(0) }
    LaunchedEffect(points) {
        val steps = 30
        repeat(steps + 1) { i -> shown = points * i / steps; kotlinx.coroutines.delay(20) }
    }

    Box(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 18.dp)
            .clip(RoundedCornerShape(16.dp))
            .background(McdColors.DarkSurface1),
    ) {
        // corner glow
        Box(
            Modifier
                .align(Alignment.TopEnd)
                .offset(x = 30.dp, y = (-30).dp)
                .size(130.dp)
                .background(
                    Brush.radialGradient(
                        listOf(McdColors.Yellow.copy(alpha = 0.18f), Color.Transparent),
                        center = Offset.Unspecified, radius = 200f,
                    ),
                    shape = androidx.compose.foundation.shape.CircleShape,
                )
        )
        Column(Modifier.padding(16.dp)) {
            Text("MYMCDONALD'S REWARDS", style = McdText.Badge, color = McdColors.TextSecondaryD)
            Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(6.dp),
                modifier = Modifier.padding(top = 6.dp)) {
                Text("%,d".format(shown), style = McdText.RewardsNumber, color = McdColors.Yellow)
                Text("points", style = McdText.Price, color = McdColors.TextSecondaryD, modifier = Modifier.padding(bottom = 4.dp))
            }
            Box(
                Modifier
                    .fillMaxWidth().height(8.dp)
                    .padding(vertical = 0.dp)
                    .clip(RoundedCornerShape(4.dp))
                    .background(Color(0xFF3A3A3A))
                    .padding(top = 0.dp),
            ) {
                Box(
                    Modifier
                        .fillMaxWidth(fill).height(8.dp)
                        .clip(RoundedCornerShape(4.dp))
                        .background(Brush.horizontalGradient(listOf(McdColors.Yellow, Color(0xFFFFD75E))))
                )
            }
            Spacer(Modifier.height(8.dp))
            Text(
                buildAnnotatedString {
                    append("Just ")
                    withStyle(SpanStyle(color = McdColors.TextPrimaryD, fontWeight = FontWeight.Bold)) {
                        append("$pointsToNext points")
                    }
                    append(" away from $nextReward")
                },
                style = McdText.Caption, color = McdColors.TextSecondaryD,
            )
        }
    }
}
```

### Deal Tile

```kotlin
import androidx.compose.foundation.border
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Icon
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun DealTile(
    imageUrl: String, flag: String, name: String, offer: String, pointsAlt: String,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .clip(RoundedCornerShape(16.dp))
            .background(McdColors.DarkSurface1)
            .border(1.dp, McdColors.DarkDivider, RoundedCornerShape(16.dp)),
    ) {
        Box(Modifier.fillMaxWidth().height(96.dp)) {
            AsyncImage(imageUrl, null, Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
            Box(
                Modifier
                    .align(Alignment.TopStart).padding(8.dp)
                    .clip(RoundedCornerShape(6.dp)).background(McdColors.Red)
                    .padding(horizontal = 7.dp, vertical = 3.dp)
            ) { Text(flag, style = McdText.Badge, color = Color.White) }
        }
        Column(Modifier.padding(start = 12.dp, end = 12.dp, top = 10.dp, bottom = 14.dp)) {
            Text(name, style = McdText.CardTitle, color = McdColors.TextPrimaryD)
            Text(offer, style = McdText.Caption, color = McdColors.TextSecondaryD, modifier = Modifier.padding(top = 3.dp))
            Row(
                Modifier.padding(top = 8.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(4.dp),
            ) {
                Icon(Icons.Filled.Star, null, tint = McdColors.Yellow, modifier = Modifier.size(11.dp))
                Text("or $pointsAlt pts", style = McdText.Badge, color = McdColors.Yellow)
            }
        }
    }
}
```

### Order-Mode Selector

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.material.icons.filled.*
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun OrderModeSelector() {
    data class Mode(val label: String, val icon: ImageVector)
    val modes = listOf(
        Mode("Pickup", Icons.Filled.ShoppingBag),
        Mode("Curbside", Icons.Filled.DirectionsCar),
        Mode("Drive Thru", Icons.Filled.DriveEta),
        Mode("Delivery", Icons.Filled.DeliveryDining),
    )
    var sel by remember { mutableIntStateOf(0) }
    val haptics = LocalHapticFeedback.current

    Row(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 18.dp)
            .clip(RoundedCornerShape(16.dp))
            .background(McdColors.DarkSurface1)
            .padding(14.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        modes.forEachIndexed { i, m ->
            val active = i == sel
            Column(
                Modifier
                    .weight(1f)
                    .clip(RoundedCornerShape(12.dp))
                    .background(if (active) McdColors.Yellow.copy(alpha = 0.08f) else Color.Transparent)
                    .border(1.5.dp, if (active) McdColors.Yellow else McdColors.DarkDivider, RoundedCornerShape(12.dp))
                    .clickable {
                        sel = i
                        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                    }
                    .padding(vertical = 12.dp),
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.spacedBy(6.dp),
            ) {
                Icon(m.icon, m.label, tint = if (active) McdColors.Yellow else McdColors.TextPrimaryD, modifier = Modifier.size(22.dp))
                Text(m.label, style = McdText.ModeLabel, color = if (active) McdColors.Yellow else McdColors.TextPrimaryD)
            }
        }
    }
}
```

### Buttons

```kotlin
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults

@Composable
fun McdPrimaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val src = remember { MutableInteractionSource() }
    val pressed by src.collectIsPressedAsState()
    Button(
        onClick = onClick, interactionSource = src,
        shape = RoundedCornerShape(999.dp),
        colors = ButtonDefaults.buttonColors(
            containerColor = if (pressed) McdColors.YellowPressed else McdColors.Yellow,
            contentColor = McdColors.OnYellow, // #1A1A1A — never white
        ),
        modifier = modifier.fillMaxWidth().height(50.dp),
    ) { Text(text, style = McdText.Button) }
}

@Composable
fun McdSecondaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val src = remember { MutableInteractionSource() }
    val pressed by src.collectIsPressedAsState()
    Button(
        onClick = onClick, interactionSource = src,
        shape = RoundedCornerShape(999.dp),
        colors = ButtonDefaults.buttonColors(
            containerColor = if (pressed) McdColors.RedPressed else McdColors.Red,
            contentColor = Color.White,
        ),
        modifier = modifier.fillMaxWidth().height(50.dp),
    ) { Text(text, style = McdText.Button.copy(color = Color.White)) }
}
```

## 4. Navigation (Bottom bar + docked Order FAB)

McDonald's has a 5-slot bottom strip with the **Order** action elevated in the center. On Android, use a `Scaffold` with a `NavigationBar` plus a docked `FloatingActionButton`. Active is a filled glyph in Golden Arches Yellow — there is **no** Material tint pill.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun McdScaffold(selected: Int, onSelect: (Int) -> Unit, onOrder: () -> Unit, content: @Composable (PaddingValues) -> Unit) {
    Scaffold(
        containerColor = McdColors.DarkCanvas,
        floatingActionButton = {
            FloatingActionButton(
                onClick = onOrder,
                containerColor = McdColors.Yellow,
                contentColor = McdColors.OnYellow,
                shape = androidx.compose.foundation.shape.CircleShape,
                modifier = Modifier.size(56.dp),
            ) { Icon(Icons.Filled.ShoppingCart, "Order", Modifier.size(24.dp)) }
        },
        floatingActionButtonPosition = FabPosition.Center,
        bottomBar = {
            NavigationBar(containerColor = McdColors.DarkCanvas, tonalElevation = 0.dp) {
                val items = listOf(
                    "Home" to Icons.Filled.Home,
                    "Rewards" to Icons.Filled.Star,
                    null to null,                       // spacer under the docked FAB
                    "Menu" to Icons.Filled.GridView,
                    "More" to Icons.Filled.AccountCircle,
                )
                items.forEachIndexed { i, (label, icon) ->
                    if (label == null) {
                        Spacer(Modifier.weight(1f))
                    } else {
                        NavigationBarItem(
                            selected = selected == i,
                            onClick = { onSelect(i) },
                            icon = { Icon(icon!!, label, Modifier.size(22.dp)) },
                            label = { Text(label, style = McdText.Tab) },
                            colors = NavigationBarItemDefaults.colors(
                                selectedIconColor = McdColors.Yellow,
                                selectedTextColor = McdColors.Yellow,
                                unselectedIconColor = Color(0xFF888888),
                                unselectedTextColor = Color(0xFF888888),
                                indicatorColor = Color.Transparent, // no Material pill — McDonald's has none
                            ),
                        )
                    }
                }
            }
        },
        content = content,
    )
}
```

The greeting header ("Hi, {name}" Speedee 22sp/800 with the yellow arches and a 36.dp circular bell) sits above the rewards card. Use `BottomAppBar` with `FloatingActionButton(elevation = FloatingActionButtonDefaults.elevation(8.dp))` if you want the FAB to overlap the bar with the signature yellow glow.

## 5. Motion

McDonald's motion is cheerful and quick — 150–300ms, always tied to ordering or rewards.

| Moment | Compose recipe |
|--------|----------------|
| Add to Mobile Order | item flies into FAB; FAB `animateFloatAsState` scale `1.0 → 1.08 → 1.0` via `keyframes`/`repeatable(2)` `tween(130)`; soft haptic; badge `scaleIn` |
| Rewards progress fill | `animateFloatAsState(progress, tween(600, EaseOut))`; points `LaunchedEffect` count-up loop |
| Order-mode select | border/fill `animateColorAsState(tween(150))`; store-picker `AnimatedVisibility expandVertically(tween(220))` |
| Deal tile → detail | shared-element `SharedTransitionLayout` image zoom `tween(300)` |
| Order READY badge flip | color swap + `scaleIn(spring(dampingRatio = 0.5f))`; success haptic |
| Curbside "I'm here" | button fills; `Crossfade` status text; checkmark `drawWithCache` reveal |
| Bottom sheet (curbside/customise) | `ModalBottomSheet`, 20.dp top corners, `tween(300)` |
| Pull-to-refresh | `PullToRefreshContainer`, yellow indicator, tick haptic on release |

```kotlin
// FAB pulse — the canonical "added to order" feedback
val pulse by animateFloatAsState(
    targetValue = if (pulsing) 1.08f else 1f,
    animationSpec = repeatable(2, tween(130), RepeatMode.Reverse),
    label = "fabPulse",
)
Modifier.scale(pulse)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for soft impact on add-to-order; `HapticFeedbackType.TextHandleMove` for the light tick on mode select & chip toggle; a stronger confirm pattern (`VibrationEffect.EFFECT_HEAVY_CLICK`) on order-placed / reward-redeemed.

## 6. Icons

McDonald's uses the proprietary Golden Arches plus standard glyphs; closest first-party set is `androidx.compose.material:material-icons-extended`. Bundle the arches as a vector drawable — **never recolor it**.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Rewards (tab) | `star` / `star.fill` | `Icons.Filled.Star` |
| Order (FAB) | `cart.fill` | `Icons.Filled.ShoppingCart` |
| Menu (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| More (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Notification bell | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| Pickup | `bag` | `Icons.Filled.ShoppingBag` |
| Curbside | `car` | `Icons.Filled.DirectionsCar` |
| Drive Thru | `car.side` | `Icons.Filled.DriveEta` |
| Delivery | `cart` | `Icons.Filled.DeliveryDining` |
| Rewards star | `star.fill` | `Icons.Filled.Star` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Store / location | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Order ready | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Promo / coupon | `tag.fill` | `Icons.Filled.LocalOffer` |
| Close | `xmark` | `Icons.Filled.Close` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; docked FAB + `ModalBottomSheet` comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the dark canvas wants light-content system bars. The docked Order FAB must clear the gesture inset — `Scaffold` + `FabPosition.Center` handles most of it; sticky CTA bars use `WindowInsets.navigationBars`.
- **Text on yellow**: `onPrimary` is hard-set to `#1A1A1A`; never let a yellow surface carry white text (button label, points chip, FAB glyph). White-on-yellow fails contrast and violates the brand.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, section headers, body, deal copy. Pin layout-critical text (the rewards number, deal flags, status badges, 10sp tab labels, mode-tile labels) via `dp` or a fixed-`fontScale` `CompositionLocalProvider`.
- **Tabular numerals**: the points balance, prices, and the order timer must not reflow while rolling — use a monospaced-digit feature or fixed number slots.
- **TalkBack**: rewards card → "MyMcDonald's Rewards, {points} points, {pointsToNext} from {reward}"; deal tiles → "{name}, {offer}, or {pts} points"; the FAB → "Order, button"; mode tiles → "{mode}, selected/not selected".
- **Touch targets**: Material guidance is 48.dp. The Order FAB is 56.dp; the 22.dp tab icons and 11sp mode tiles need a 48.dp hit area via padding; pill CTAs ≥ 48.dp tall.
- **Contrast**: `#1A1A1A` on `#FFC72C` passes WCAG AA (the reason white-on-yellow is forbidden); white on `#DA291C` passes AA; `#F2F2F2` on `#121212` passes AA.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, set the rewards fill/points directly (no roll), replace fly-to-FAB with a badge increment, and make the READY flip an instant color change.
- **Dark mode**: invert via the `Dark*` palette — `#121212`, NOT true black; text becomes `#F2F2F2`. Food imagery stays full-saturation; the Order FAB's yellow glow is more prominent; bottom sheets get a 1.dp `DarkDivider` top border as the elevation cue. Do **not** enable Material You `dynamicColorScheme()` — McDonald's yellow + red must hold regardless of wallpaper, and `#1A1A1A`-on-yellow must never be themed away.
