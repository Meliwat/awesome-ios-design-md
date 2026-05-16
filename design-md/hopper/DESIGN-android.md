# Hopper (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Hopper's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Hopper's clean white canvas, friendly red accent, the green/red price-prediction heatmap) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush` gradient legend instead of a CAGradientLayer, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/HopperColors.kt
import androidx.compose.ui.graphics.Color

object HopperColors {
    // Canvas & Surfaces
    val Canvas         = Color(0xFFFFFFFF)
    val Surface        = Color(0xFFF5F5F7)
    val Divider        = Color(0xFFE5E5EA)
    val SurfacePressed = Color(0xFFEBEBF0)

    // Text
    val TextPrimary    = Color(0xFF1D1D1F)
    val TextSecondary  = Color(0xFF6E6E73)
    val TextTertiary   = Color(0xFFA1A1A6)

    // Brand
    val Red            = Color(0xFFFA4747)
    val RedPressed     = Color(0xFFE03A3A)
    val BuyGreen       = Color(0xFF34C759)

    // Heatmap (prediction scale: cheapest → most expensive)
    val BuyBest   = Color(0xFF34C759)
    val BuyGood   = Color(0xFFA8E6B8)
    val Neutral   = Color(0xFFF5F5F7)
    val WaitHigh  = Color(0xFFFFC7C7)
    val WaitWorst = Color(0xFFFA4747)

    // Semantic
    val PriceDrop  = Color(0xFF34C759)
    val ErrorRed   = Color(0xFFD70015)
}
```

Wire it into a Material 3 `lightColorScheme` so ripples, dividers, and default component colors inherit the brand. Hopper is light-first.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val HopperScheme = lightColorScheme(
    primary        = HopperColors.Red,
    onPrimary      = Color.White,
    background     = HopperColors.Canvas,
    onBackground   = HopperColors.TextPrimary,
    surface        = HopperColors.Canvas,
    onSurface      = HopperColors.TextPrimary,
    surfaceVariant = HopperColors.Surface,
    outline        = HopperColors.Divider,
    error          = HopperColors.ErrorRed,
)

@Composable
fun HopperTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = HopperScheme, typography = HopperTypography, content = content)
```

## 2. Typography

Hopper's geometric face is proprietary. Drop substitute Inter TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto).

```kotlin
// ui/theme/HopperType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val HopperSans = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object HopperText {
    val Price      = TextStyle(HopperSans, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 31.sp, letterSpacing = (-0.5).sp)
    val TitleLarge = TextStyle(HopperSans, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val Section    = TextStyle(HopperSans, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Route      = TextStyle(HopperSans, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val CardTitle  = TextStyle(HopperSans, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val Body       = TextStyle(HopperSans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Button     = TextStyle(HopperSans, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 20.sp)
    val Verdict    = TextStyle(HopperSans, fontWeight = FontWeight.Bold,     fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Meta       = TextStyle(HopperSans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val CalDay     = TextStyle(HopperSans, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 16.sp)
    val CalPrice   = TextStyle(HopperSans, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp)
    val Tab        = TextStyle(HopperSans, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val HopperTypography = Typography(
    headlineLarge = HopperText.TitleLarge,
    headlineSmall = HopperText.Section,
    titleMedium   = HopperText.Route,
    bodyMedium    = HopperText.Body,
    labelSmall    = HopperText.Tab,
)
```

## 3. Signature Components

### Price-Prediction Calendar Heatmap

```kotlin
import androidx.compose.animation.animateColorAsState
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.animation.core.tween
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

enum class Prediction { BuyBest, BuyGood, Neutral, WaitHigh, WaitWorst }

private fun Prediction.fill() = when (this) {
    Prediction.BuyBest   -> HopperColors.BuyBest
    Prediction.BuyGood   -> HopperColors.BuyGood
    Prediction.Neutral   -> HopperColors.Neutral
    Prediction.WaitHigh  -> HopperColors.WaitHigh
    Prediction.WaitWorst -> HopperColors.WaitWorst
}
private fun Prediction.fg() =
    if (this == Prediction.BuyBest || this == Prediction.WaitWorst) Color.White else HopperColors.TextPrimary

@Composable
fun DayCell(
    day: Int,
    price: String,
    prediction: Prediction,
    selected: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val haptics = LocalHapticFeedback.current
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 1.05f else 1f, spring(dampingRatio = 0.6f), label = "cell")
    // Heatmap "breathes" when the prediction changes
    val bg by animateColorAsState(prediction.fill(), tween(300), label = "fill")

    Column(
        modifier
            .scale(scale)
            .clip(RoundedCornerShape(8.dp))
            .background(bg)
            .then(
                if (selected) Modifier.border(2.dp, HopperColors.TextPrimary, RoundedCornerShape(8.dp))
                else Modifier
            )
            .height(44.dp)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS light impact
                onClick()
            },
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center,
    ) {
        Text("$day", style = HopperText.CalDay, color = prediction.fg())
        Text(price, style = HopperText.CalPrice, color = prediction.fg().copy(alpha = 0.85f))
    }
}

@Composable
fun HeatmapLegend() {
    Column(verticalArrangement = Arrangement.spacedBy(4.dp)) {
        Box(
            Modifier
                .fillMaxWidth()
                .height(6.dp)
                .clip(RoundedCornerShape(3.dp))
                .background(
                    Brush.horizontalGradient(
                        listOf(HopperColors.BuyBest, HopperColors.BuyGood,
                               HopperColors.Neutral, HopperColors.WaitHigh, HopperColors.WaitWorst)
                    )
                )
        )
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
            Text("Buy", style = HopperText.Meta, color = HopperColors.BuyGreen)
            Text("Wait", style = HopperText.Meta, color = HopperColors.Red)
        }
    }
}
```

### Fare Verdict Pill

```kotlin
import androidx.compose.foundation.shape.CircleShape

enum class Verdict { Buy, Wait }

@Composable
fun VerdictPill(verdict: Verdict, detail: String? = null, modifier: Modifier = Modifier) {
    Row(
        modifier
            .clip(CircleShape)
            .background(if (verdict == Verdict.Buy) HopperColors.BuyGreen else HopperColors.Red)
            .padding(horizontal = 12.dp, vertical = 5.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Text(if (verdict == Verdict.Buy) "BUY" else "WAIT", style = HopperText.Verdict, color = Color.White)
        if (detail != null) Text("· $detail", style = HopperText.Meta, color = Color.White.copy(alpha = 0.9f))
    }
}
```

### Fare Card

```kotlin
import androidx.compose.foundation.border
import androidx.compose.ui.draw.shadow

@Composable
fun FareCard(
    route: String,
    airline: String,
    duration: String,
    price: String,
    verdict: Verdict,
    confidence: Int,
) {
    val filled = ((confidence / 100f) * 4).toInt()
    val accent = if (verdict == Verdict.Buy) HopperColors.BuyGreen else HopperColors.Red

    Column(
        Modifier
            .fillMaxWidth()
            .shadow(16.dp, RoundedCornerShape(16.dp), spotColor = Color.Black.copy(alpha = 0.06f))
            .clip(RoundedCornerShape(16.dp))
            .background(HopperColors.Canvas)
            .border(1.dp, HopperColors.Divider, RoundedCornerShape(16.dp))
            .padding(20.dp),
        verticalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Column(verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(route, style = HopperText.Route, color = HopperColors.TextPrimary)
            Text("$airline · $duration", style = HopperText.Meta, color = HopperColors.TextSecondary)
        }
        Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(10.dp)) {
            Text(price, style = HopperText.Price, color = HopperColors.TextPrimary)
            VerdictPill(verdict, if (verdict == Verdict.Buy) "won't drop" else "cheaper soon")
        }
        Column(verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Row(horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                repeat(4) { i ->
                    Box(
                        Modifier
                            .weight(1f).height(6.dp).clip(CircleShape)
                            .background(if (i < filled) accent else HopperColors.Divider)
                    )
                }
            }
            Text("Hopper is $confidence% confident", style = HopperText.Meta, color = HopperColors.TextSecondary)
        }
        HopButton("Watch this trip") {}
    }
}
```

### Watch-Price Toggle

```kotlin
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Notifications
import androidx.compose.material3.Icon

@Composable
fun WatchToggle(on: Boolean, onToggle: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    val offset by animateDpAsState(if (on) 22.dp else 0.dp, spring(dampingRatio = 0.7f), label = "knob")
    Box(
        Modifier
            .size(width = 52.dp, height = 32.dp)
            .clip(CircleShape)
            .background(if (on) HopperColors.Red else HopperColors.Divider)
            .clickable(remember { MutableInteractionSource() }, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onToggle()
            }
            .padding(2.dp),
        contentAlignment = Alignment.CenterStart,
    ) {
        Box(
            Modifier
                .offset(x = offset)
                .size(28.dp)
                .shadow(4.dp, CircleShape, spotColor = Color.Black.copy(alpha = 0.12f))
                .clip(CircleShape)
                .background(Color.White),
            contentAlignment = Alignment.Center,
        ) {
            if (on) Icon(Icons.Filled.Notifications, contentDescription = null,
                         tint = HopperColors.Red, modifier = Modifier.size(12.dp))
        }
    }
}
```

### Primary Button

```kotlin
@Composable
fun HopButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "btn")
    Box(
        modifier
            .fillMaxWidth()
            .scale(scale)
            .clip(RoundedCornerShape(14.dp))
            .background(if (pressed) HopperColors.RedPressed else HopperColors.Red)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            }
            .padding(vertical = 16.dp),
        contentAlignment = Alignment.Center,
    ) { Text(text, style = HopperText.Button, color = Color.White) }
}
```

## 4. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Hopper's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 96%-opaque white surface. **Active tint is Hopper Red** — red is the active indicator.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun HopperBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = HopperColors.Canvas.copy(alpha = 0.96f), tonalElevation = 0.dp) {
        val items = listOf(
            "Home"    to Icons.Filled.Home,
            "Watches" to Icons.Filled.Notifications,
            "Trips"   to Icons.Filled.Work,
            "Profile" to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = HopperText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = HopperColors.Red,
                    selectedTextColor   = HopperColors.Red,
                    unselectedIconColor = HopperColors.TextSecondary,
                    unselectedTextColor = HopperColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // no Material pill — Hopper has none
                ),
            )
        }
    }
}
```

## 5. Navigation

The top bar carries the screen title (left, `HopperText.TitleLarge`) and a profile avatar (right). Below it sits the trip-builder search card — a white `RoundedCornerShape(16.dp)` block, 1.dp `HopperColors.Divider` border, with stacked "From / To / Dates / Travelers" rows separated by hairline `Divider`s and a circular red swap-route button on the From/To boundary. On scroll, collapse it to a compact bar; Android has no live blur, so use the 96%-opaque white surface.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Calendar date select | `animateFloatAsState` 1 → 1.05 `spring(dampingRatio = 0.6f)`; `HapticFeedbackType.TextHandleMove` |
| Heatmap color shift | `animateColorAsState(prediction.fill(), tween(300))` per cell |
| Watch toggle arm | `animateDpAsState` knob `spring(dampingRatio = 0.7f)`, track color crossfade; `HapticFeedbackType.LongPress` |
| Price-drop celebration | bottom sheet `slideInVertically` + an animated counter (`animateIntAsState`) ticking the drop |
| Verdict pill entrance | `animateFloatAsState` 0.94 → 1 `spring(dampingRatio = 0.6f)` |

```kotlin
// Price-drop counter
val shown by animateIntAsState(targetValue = droppedAmount, animationSpec = tween(700), label = "drop")
Text("↓ $$shown", style = HopperText.Price, color = HopperColors.PriceDrop)
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's `.medium` impact.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity with Hopper's custom glyphs (including the bunny), export them as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Watch / bell | `bell` / `bell.fill` | `Icons.Outlined.Notifications` / `Icons.Filled.Notifications` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Swap route | `arrow.up.arrow.down` | `Icons.Filled.SwapVert` |
| Calendar | `calendar` | `Icons.Filled.CalendarMonth` |
| Plane | `airplane` | `Icons.Filled.Flight` |
| Price drop | `arrow.down` | `Icons.Filled.ArrowDownward` |
| Prediction info | `info.circle` | `Icons.Outlined.Info` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Watches (tab) | `bell.fill` | `Icons.Filled.Notifications` |
| Trips (tab) | `suitcase.fill` | `Icons.Filled.Work` |
| Profile (tab) | `person.fill` | `Icons.Filled.Person` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the white canvas wants `WindowCompat` dark-content system bars. Apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` or `Scaffold` insets so the calendar and tab bar clear the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on the predicted price, route titles, body. Pin layout-sensitive units (calendar day/price labels, verdict pill, tab labels) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: never rely on color alone — give each calendar cell a `contentDescription` like `"12, predicted $189, good price to buy"`; give the verdict block one combined description; mark the watch toggle with `Role.Switch` and a `stateDescription`.
- **Color-blind safety**: the per-cell price text and the literal "BUY"/"WAIT" pill label ensure the red/green heatmap is never the only channel of meaning. Consider a subtle icon (down-arrow for buy, hourglass for wait) on the strongest cells.
- **Touch targets**: Material guidance is 48.dp minimum. The CTA clears it; ensure each 44.dp calendar cell and the 52×32.dp toggle have ≥48.dp effective touch via padding.
- **Contrast**: `#6E6E73` on `#FFFFFF` passes WCAG AA at 13sp+. Light heatmap tints keep dark text; strong fills use white — validate both.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Hopper's brand requires the fixed white canvas and the friendly-red accent regardless of wallpaper.
