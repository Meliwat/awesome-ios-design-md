# Southwest (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Southwest's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the signature boarding-position card and 24-hour check-in countdown, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps Southwest's *visual identity* (no seat map, the giant `A23` boarding position in Warm Yellow, the launch-clock check-in countdown, the Heart tri-color role system, the navy canvas) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas`/`drawArc` for the countdown ring, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. No color extraction — Southwest's palette is the fixed Heart tri-color. The dark scheme is the primary catalog rendering; a full light scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/SWColors.kt
import androidx.compose.ui.graphics.Color

object SWColors {
    // Heart tri-color (brand) — strict role system
    val Blue        = Color(0xFF304CB2) // structure
    val BlueBright  = Color(0xFF2567E8) // link
    val BlueDeep    = Color(0xFF243C8E) // hero gradient end
    val Red         = Color(0xFFE51D23) // ALERTS ONLY
    val RedPressed  = Color(0xFFC0161B)
    val Yellow      = Color(0xFFF9B612) // THE CTA
    val YellowPressed = Color(0xFFE0A300)

    // Canvas & Surfaces (Light)
    val Canvas   = Color(0xFFFFFFFF)
    val Wash     = Color(0xFFF2F5FB)
    val Surface  = Color(0xFFFFFFFF)
    val Pressed  = Color(0xFFE7ECF6)
    val Divider  = Color(0xFFDCE2EE)

    // Canvas & Surfaces (Dark) — navy, NOT neutral black
    val DarkCanvas   = Color(0xFF0E1726)
    val DarkSurface1 = Color(0xFF16223A)
    val DarkSurface2 = Color(0xFF1F2E4A)
    val DarkDivider  = Color(0xFF2A3A57)

    // Text
    val TextPrimary       = Color(0xFF1A2233)
    val TextSecondary     = Color(0xFF5A6783)
    val TextTertiary      = Color(0xFF8C99B3)
    val DarkTextPrimary   = Color(0xFFEAF0FA)
    val DarkTextSecondary = Color(0xFF9DAAC4)
    val DarkTextTertiary  = Color(0xFF67748F)
    val OnYellow = Color(0xFF1A1A1A)
    val OnBlue   = Color(0xFFFFFFFF)

    // Semantic
    val Success     = Color(0xFF1E8E4E)
    val SuccessDark = Color(0xFF1FAE5E)
    // Warning == Yellow, Error == Red (alerts only), Info == Blue / BlueBright
}
```

Wire it into both schemes. Southwest's dark canvas is the signature navy `#0E1726` — never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val SWLight = lightColorScheme(
    primary        = SWColors.Yellow,        // the one CTA
    onPrimary      = SWColors.OnYellow,
    secondary      = SWColors.Blue,
    onSecondary    = SWColors.OnBlue,
    background      = SWColors.Wash,
    onBackground   = SWColors.TextPrimary,
    surface         = SWColors.Surface,
    onSurface      = SWColors.TextPrimary,
    surfaceVariant = SWColors.Pressed,
    outline         = SWColors.Divider,
    error           = SWColors.Red,           // alerts only
)

private val SWDark = darkColorScheme(
    primary        = SWColors.Yellow,        // identical across themes
    onPrimary      = SWColors.OnYellow,
    secondary      = SWColors.BlueBright,
    onSecondary    = SWColors.OnBlue,
    background      = SWColors.DarkCanvas,
    onBackground   = SWColors.DarkTextPrimary,
    surface         = SWColors.DarkSurface1,
    onSurface      = SWColors.DarkTextPrimary,
    surfaceVariant = SWColors.DarkSurface2,
    outline         = SWColors.DarkDivider,
    error           = SWColors.Red,
)

@Composable
fun SWTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) SWDark else SWLight,
    typography  = SWTypography,
    content     = content,
)
```

## 2. Typography (M3)

Brand face is **Southwest Sans** (proprietary, Monotype). License it and drop the TTFs in `res/font/`, or ship **Inter** (SIL OFL) as the fallback — keep the named ramp identical. Numbers are tabular wherever they update (`FontFeatureSetting "tnum"`).

```kotlin
// ui/theme/SWType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val SWSans = FontFamily(
    Font(R.font.sw_regular,    FontWeight.Normal),
    Font(R.font.sw_medium,     FontWeight.Medium),
    Font(R.font.sw_semibold,   FontWeight.SemiBold),
    Font(R.font.sw_bold,       FontWeight.Bold),
    Font(R.font.sw_extrabold,  FontWeight.ExtraBold),
    Font(R.font.sw_black,      FontWeight.Black),
)

private const val TNUM = "tnum"  // apply via fontFeatureSettings on updating numbers

// Named ramp — mirrors DESIGN.md §3 (pt → sp 1:1)
object SWText {
    val BoardingGroup = TextStyle(SWSans, fontWeight = FontWeight.Black,     fontSize = 88.sp, lineHeight = 80.sp, letterSpacing = (-2).sp)
    val BoardingNum   = TextStyle(SWSans, fontWeight = FontWeight.ExtraBold, fontSize = 52.sp, lineHeight = 52.sp, letterSpacing = (-1).sp, fontFeatureSettings = TNUM)
    val ScreenTitle   = TextStyle(SWSans, fontWeight = FontWeight.Black,     fontSize = 32.sp, lineHeight = 36.sp, letterSpacing = (-0.4).sp)
    val AirportCode   = TextStyle(SWSans, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 27.sp, letterSpacing = (-0.4).sp)
    val Section       = TextStyle(SWSans, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 27.sp, letterSpacing = (-0.2).sp)
    val Subsection    = TextStyle(SWSans, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 24.sp)
    val Body          = TextStyle(SWSans, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val BodyEmphasis  = TextStyle(SWSans, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 24.sp)
    val CardTitle     = TextStyle(SWSans, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val FarePrice     = TextStyle(SWSans, fontWeight = FontWeight.ExtraBold, fontSize = 19.sp, lineHeight = 21.sp, fontFeatureSettings = TNUM)
    val Eyebrow       = TextStyle(SWSans, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.6.sp)
    val Meta          = TextStyle(SWSans, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp)
    val Button        = TextStyle(SWSans, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.1.sp)
    val Tab           = TextStyle(SWSans, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Clock         = TextStyle(SWSans, fontWeight = FontWeight.ExtraBold, fontSize = 11.sp, lineHeight = 11.sp, fontFeatureSettings = TNUM)
    val ClockLarge    = TextStyle(SWSans, fontWeight = FontWeight.ExtraBold, fontSize = 34.sp, lineHeight = 34.sp, fontFeatureSettings = TNUM)
}

val SWTypography = Typography(
    displayLarge  = SWText.BoardingGroup,
    headlineLarge = SWText.ScreenTitle,
    headlineMedium = SWText.Section,
    titleMedium   = SWText.Subsection,
    bodyLarge     = SWText.Body,
    labelSmall    = SWText.Tab,
)
```

## 3. Signature Components

### Boarding Position Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import androidx.compose.foundation.shape.RoundedCornerShape

@Composable
fun BoardingPositionCard(
    group: String, position: Int, boardingStarts: String, confirmation: String,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .padding(horizontal = 20.dp)
            .shadow(15.dp, RoundedCornerShape(20.dp), spotColor = SWColors.Blue.copy(alpha = 0.30f))
            .clip(RoundedCornerShape(20.dp))
            .background(Brush.linearGradient(listOf(SWColors.Blue, SWColors.BlueDeep)))
            .padding(22.dp),
    ) {
        Text("BOARDING POSITION", style = SWText.Eyebrow, color = Color.White.copy(alpha = 0.78f))
        Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(4.dp),
            modifier = Modifier.padding(vertical = 6.dp)) {
            Text(group, style = SWText.BoardingGroup, color = SWColors.Yellow)
            Text("$position", style = SWText.BoardingNum, color = Color.White)
        }
        Text("Board in group $group, position $position",
            style = SWText.CardTitle, color = Color.White.copy(alpha = 0.85f))
        HorizontalDivider(color = Color.White.copy(alpha = 0.18f), modifier = Modifier.padding(vertical = 16.dp))
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
            FootCol("BOARDING STARTS", boardingStarts)
            FootCol("CONFIRMATION", confirmation)
        }
    }
}

@Composable
private fun FootCol(label: String, value: String) {
    Column(verticalArrangement = Arrangement.spacedBy(2.dp)) {
        Text(label, style = SWText.Meta.copy(fontWeight = FontWeight.SemiBold, letterSpacing = 0.5.sp),
            color = Color.White.copy(alpha = 0.7f))
        Text(value, style = SWText.Subsection, color = Color.White)
    }
}
```

### Check-In Countdown (Canvas ring)

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.material3.Surface
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke

@Composable
fun CheckInCountdown(
    progress: Float, clock: String, isOpen: Boolean = false, onAction: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier
            .padding(horizontal = 20.dp)
            .clip(RoundedCornerShape(16.dp))
            .background(SWColors.DarkSurface1)
            .border(1.dp, SWColors.DarkDivider, RoundedCornerShape(16.dp))
            .padding(18.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Box(Modifier.size(54.dp), contentAlignment = Alignment.Center) {
            Canvas(Modifier.size(54.dp)) {
                val sw = 5.dp.toPx()
                drawArc(SWColors.DarkDivider, -90f, 360f, false,
                    style = Stroke(sw), size = Size(size.width - sw, size.height - sw),
                    topLeft = androidx.compose.ui.geometry.Offset(sw / 2, sw / 2))
                drawArc(SWColors.Yellow, -90f, 360f * progress, false,
                    style = Stroke(sw, cap = StrokeCap.Round), size = Size(size.width - sw, size.height - sw),
                    topLeft = androidx.compose.ui.geometry.Offset(sw / 2, sw / 2))
            }
            Text(clock, style = SWText.Clock, color = SWColors.Yellow)
        }
        Column(Modifier.weight(1f)) {
            Text(if (isOpen) "Check-in is open" else "Check-in opens soon",
                style = SWText.CardTitle, color = SWColors.DarkTextPrimary)
            Text("Opens 24 hrs before departure to lock your spot",
                style = SWText.Meta, color = SWColors.DarkTextSecondary)
        }
        Box(
            Modifier.clip(RoundedCornerShape(8.dp)).background(SWColors.Yellow)
                .clickable { onAction() }.padding(horizontal = 16.dp, vertical = 10.dp)
        ) {
            Text(if (isOpen) "Check in" else "Notify", style = SWText.Button, color = SWColors.OnYellow)
        }
    }
}
```

### Wanna Get Away Fare Card

```kotlin
@Composable
fun FareCard(
    name: String, price: String, points: String, selected: Boolean, onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .clip(RoundedCornerShape(12.dp))
            .background(if (selected) SWColors.Yellow.copy(alpha = 0.10f) else SWColors.DarkSurface2)
            .border(if (selected) 2.dp else 1.dp,
                if (selected) SWColors.Yellow else SWColors.DarkDivider, RoundedCornerShape(12.dp))
            .clickable { onClick() }
            .padding(vertical = 14.dp, horizontal = 12.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Text(name.uppercase(), style = SWText.Eyebrow.copy(fontSize = 11.sp, letterSpacing = 0.4.sp),
            color = SWColors.DarkTextSecondary)
        Text(price, style = SWText.FarePrice, color = SWColors.DarkTextPrimary,
            modifier = Modifier.padding(top = 8.dp))
        Text(points, style = SWText.Meta.copy(fontWeight = FontWeight.SemiBold, fontSize = 11.sp),
            color = SWColors.DarkTextSecondary, modifier = Modifier.padding(top = 2.dp))
    }
}
```

### Rapid Rewards Points Pill

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Icon

@Composable
fun PointsPill(text: String, tier: Boolean = false, modifier: Modifier = Modifier) {
    Row(
        modifier
            .clip(RoundedCornerShape(50))
            .background(if (tier) SWColors.Yellow else SWColors.Blue)
            .padding(horizontal = 16.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(Icons.Filled.Star, null, tint = if (tier) SWColors.Blue else SWColors.Yellow,
            modifier = Modifier.size(13.dp))
        Text(text, style = SWText.Button.copy(fontSize = 14.sp),
            color = if (tier) SWColors.OnYellow else SWColors.OnBlue)
    }
}
```

### Primary Button

```kotlin
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults

@Composable
fun SWPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Button(
        onClick = onClick,
        interactionSource = interaction,
        shape = RoundedCornerShape(8.dp),
        colors = ButtonDefaults.buttonColors(
            containerColor = if (pressed) SWColors.YellowPressed else SWColors.Yellow,
            contentColor = SWColors.OnYellow,
        ),
        modifier = modifier.fillMaxWidth().height(52.dp),
    ) { Text(title, style = SWText.Button) }
}
```

## 4. Navigation

Southwest has a 5-tab bottom strip; active state is a Warm Yellow tint with **no Material pill** (Southwest has none).

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun SWBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = SWColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home" to Icons.Filled.Home,
            "Book" to Icons.Filled.Send,
            "Trips" to Icons.Filled.Work,
            "Rapid Rewards" to Icons.Filled.Star,
            "Account" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, Modifier.size(22.dp)) },
                label = { Text(label, style = SWText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = SWColors.Yellow,    // Warm Yellow active
                    selectedTextColor = SWColors.Yellow,
                    unselectedIconColor = SWColors.DarkTextTertiary,
                    unselectedTextColor = SWColors.DarkTextTertiary,
                    indicatorColor = Color.Transparent,      // no pill — Southwest has none
                ),
            )
        }
    }
}
```

The top app bar is a transparent `CenterAlignedTopAppBar` (or large `TopAppBar` on Home/Trips) over the canvas; trailing help/clock action sits in a 34.dp circular `SurfaceVariant` icon button. The "seat-free" explainer and fare details are `ModalBottomSheet`s.

## 5. Motion

Southwest motion is confident and warm — the check-in and boarding-position reveal are the loud moments; everything else is quiet 150–320ms ease-out.

| Moment | Compose recipe |
|--------|----------------|
| Boarding-position reveal | `animateFloatAsState` scale 0.96 → 1.0 `tween(280, easing = EaseOut)`; group letter `AnimatedVisibility(fadeIn(tween(280)) + slideInVertically())`; success haptic |
| Check-in clock tick | re-key the clock `Text` with `AnimatedContent`/`Crossfade` `tween(120)`; the ring `progress` animated continuously |
| At T-0 (check-in opens) | `Crossfade(tween(240))` Notify pill → full-width Yellow "Check in" CTA |
| Primary CTA press | `collectIsPressedAsState` → Yellow → YellowPressed + `scale 0.98` `tween(120)` |
| Fare selection | border color `animateColorAsState(tween(150))` to Yellow; price `scale` `keyframes` 1 → 1.03 → 1 |
| Tab change | instant tint to Yellow; no indicator slide (no pill exists) |
| Bottom sheet | `ModalBottomSheet` default slide + `tween(320)` scrim fade |
| Page navigation | Nav slide push `tween(300)` |
| Cancellation banner (only red motion) | `AnimatedVisibility(slideInVertically(tween(220)))` + warning haptic |

```kotlin
// Boarding-position reveal — the payoff moment
val scale by animateFloatAsState(if (revealed) 1f else 0.96f, tween(280), label = "reveal")
LaunchedEffect(revealed) {
    if (revealed) haptics.performHapticFeedback(HapticFeedbackType.LongPress) // success analog
}
```

Haptics: use `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the success feel on check-in and the position reveal; `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` for the light CTA-press tick; a stronger constant on the cancellation banner. Auto-state updates are silent.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The boarding-pass barcode is a `Canvas`/`ZXing`-rendered bitmap, not an icon.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Book (tab) | `paperplane.fill` | `Icons.Filled.Send` |
| Trips (tab) | `suitcase.fill` | `Icons.Filled.Work` |
| Rapid Rewards (tab) | `star.fill` | `Icons.Filled.Star` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Help / clock | `clock` | `Icons.Filled.Schedule` |
| Bags fly free | `bag.fill` | `Icons.Filled.Luggage` |
| Flight / route | `airplane` | `Icons.Filled.Flight` |
| On time / success | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Delay / warning | `exclamationmark.triangle.fill` | `Icons.Filled.Warning` |
| Cancelled (red only) | `xmark.octagon.fill` | `Icons.Filled.Cancel` |
| Points / rewards | `star.fill` | `Icons.Filled.Star` |
| Companion Pass | `person.2.fill` | `Icons.Filled.Group` |
| Add a bag | `plus.circle` | `Icons.Filled.AddCircleOutline` |
| Calendar | `calendar` | `Icons.Filled.CalendarMonth` |
| Notification | `bell.fill` | `Icons.Filled.Notifications` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas` arc, `AnimatedContent`, modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the navy canvas wants light-content system bars (dark-content on the light theme). The top app bar respects the camera cutout; pin the primary CTA above the nav bar with `Modifier.navigationBarsPadding()`.
- **Tabular numerals**: keep `fontFeatureSettings = "tnum"` on the countdown clock, fares, and points balance so digits never jitter (already in the ramp).
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, sections, body, card titles, fare prices. Pin layout-critical text (boarding group/number, countdown clock, 10sp tab labels, eyebrow labels, airport codes) by deriving from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **One Yellow per screen**: `SWColors.Yellow` is the single primary-CTA fill; secondary actions use `SWColors.Blue`/`secondary`. Enforce in review.
- **Red discipline**: `SWColors.Red` / `error` only on cancellation/irregular-ops banners and the final destructive confirm — never a CTA, badge, or decoration.
- **TalkBack**: label the boarding card "Boarding position: group A, position 23. Boarding starts 4:25 PM. Confirmation K9F2Q2." Label the countdown "Check-in opens in 23 hours 41 minutes"; the CTA exposes a state-aware `contentDescription`. Pair the red cancellation state with an icon + text — never color alone.
- **Touch targets**: Material guidance is 48.dp. The primary CTA is 52.dp; give the 13.dp star and the countdown action a 48.dp hit area; fare cards are full-card clickable; the 34.dp icon button gets a 48.dp ripple bounds.
- **Contrast**: `#1A1A1A` on `#F9B612` and white on `#304CB2` pass WCAG AA; on the navy canvas use `#EAF0FA` text and the brightened `#2567E8` link.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the reveal scale and clock crossfade (substitute an instant set); keep the success haptic on check-in.
- **Dark mode**: invert via the `Dark*` palette — navy `#0E1726`, NOT true black; `#1A2233` text becomes `#EAF0FA`. Shadows read poorly on navy, so the 1.dp `DarkDivider` border carries card elevation; the boarding card keeps its blue-glow `spotColor`. Do **not** enable Material You `dynamicColorScheme()` — the Heart tri-color must hold regardless of wallpaper.
