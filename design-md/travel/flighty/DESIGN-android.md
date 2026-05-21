# Flighty (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Flighty's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Flighty's deep instrument-panel black, single-blue accent, the glowing live flight arc, strict status semantics) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Canvas` quadratic-Bézier arc instead of a CAShapeLayer, an `ActivityKit`-equivalent ongoing notification, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/FlightyColors.kt
import androidx.compose.ui.graphics.Color

object FlightyColors {
    // Canvas & Surfaces
    val Canvas   = Color(0xFF0B0B0F)
    val Surface1 = Color(0xFF1A1A1F)
    val Surface2 = Color(0xFF222228)
    val Surface3 = Color(0xFF2C2C34)
    val Divider  = Color(0xFF2E2E36)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF8E8E96)
    val TextTertiary  = Color(0xFF5A5A62)

    // Brand
    val Blue        = Color(0xFF0A84FF)
    val BluePressed = Color(0xFF0066CC)
    val BlueGlow    = Color(0x730A84FF) // ~45% alpha

    // Status — strict semantics
    val OnTime    = Color(0xFF30D158)
    val Delay     = Color(0xFFFFD60A)
    val Cancelled = Color(0xFFFF453A)

    // Map
    val MapLand      = Color(0xFF16161B)
    val MapGraticule = Color(0xFF1F1F26)
    val ArcRemaining = Color(0x590A84FF) // ~35% alpha
}
```

Wire it into a Material 3 `darkColorScheme`. Flighty is effectively dark-only — do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val FlightyScheme = darkColorScheme(
    primary        = FlightyColors.Blue,
    onPrimary      = FlightyColors.TextPrimary,
    background      = FlightyColors.Canvas,
    onBackground    = FlightyColors.TextPrimary,
    surface         = FlightyColors.Surface1,
    onSurface       = FlightyColors.TextPrimary,
    surfaceVariant  = FlightyColors.Surface2,
    outline         = FlightyColors.Divider,
    error           = FlightyColors.Cancelled,
)

@Composable
fun FlightyTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = FlightyScheme, typography = FlightyTypography, content = content)
```

## 2. Typography

Flighty uses SF Pro on iOS; the Android analog is Roboto, which has excellent tabular figures. Drop substitute Inter TTFs in `res/font/` if you want a closer match. Always enable tabular figures on flight data via a `FontFeatureSetting`.

```kotlin
// ui/theme/FlightyType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val FlightySans = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

private const val TNUM = "tnum" // tabular figures

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object FlightyText {
    val TimeHero   = TextStyle(FlightySans, fontWeight = FontWeight.Bold,     fontSize = 32.sp, lineHeight = 34.sp, letterSpacing = (-0.5).sp, fontFeatureSettings = TNUM)
    val TitleLarge = TextStyle(FlightySans, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val RouteCode  = TextStyle(FlightySans, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 26.sp, letterSpacing = 0.5.sp)
    val Section    = TextStyle(FlightySans, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val CardTitle  = TextStyle(FlightySans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val Body       = TextStyle(FlightySans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Button     = TextStyle(FlightySans, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp)
    val Status     = TextStyle(FlightySans, fontWeight = FontWeight.Bold,     fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.3.sp)
    val Gate       = TextStyle(FlightySans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 18.sp, letterSpacing = 0.5.sp, fontFeatureSettings = TNUM)
    val Meta       = TextStyle(FlightySans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val OnTimePct  = TextStyle(FlightySans, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 22.sp, fontFeatureSettings = TNUM)
    val Tab        = TextStyle(FlightySans, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
    val Timestamp  = TextStyle(FlightySans, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, fontFeatureSettings = TNUM)
}

// Map onto Material 3 slots so stock components inherit the brand
val FlightyTypography = Typography(
    headlineLarge = FlightyText.TitleLarge,
    headlineSmall = FlightyText.Section,
    titleMedium   = FlightyText.CardTitle,
    bodyMedium    = FlightyText.Body,
    labelSmall    = FlightyText.Tab,
)
```

## 3. Signature Components

### Flight Status Chip

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

sealed interface FlightStatus {
    data object OnTime : FlightStatus
    data class Delayed(val mins: Int) : FlightStatus
    data object Cancelled : FlightStatus
}

@Composable
fun StatusChip(status: FlightStatus, modifier: Modifier = Modifier) {
    val (color, label) = when (status) {
        FlightStatus.OnTime    -> FlightyColors.OnTime to "ON TIME"
        is FlightStatus.Delayed -> FlightyColors.Delay to "DELAYED ${status.mins}m"
        FlightStatus.Cancelled -> FlightyColors.Cancelled to "CANCELLED"
    }
    Text(
        text = label,
        style = FlightyText.Status,
        color = color,
        modifier = modifier
            .clip(CircleShape)
            .background(color.copy(alpha = 0.15f))
            .padding(horizontal = 10.dp, vertical = 5.dp),
    )
}
```

### Live Flight Map Arc

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.runtime.*
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.PathMeasure
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.graphics.PathEffect

@Composable
fun LiveFlightMap(
    origin: Offset,        // fractional 0..1
    destination: Offset,
    livePosition: Float,   // 0..1
    modifier: Modifier = Modifier,
) {
    val drawn = remember { Animatable(0f) }
    LaunchedEffect(livePosition) {
        drawn.animateTo(livePosition, tween(900))
    }

    Box(modifier.fillMaxSize().background(FlightyColors.Canvas)) {
        Canvas(Modifier.fillMaxSize()) {
            val o = Offset(origin.x * size.width, origin.y * size.height)
            val d = Offset(destination.x * size.width, destination.y * size.height)
            val c = Offset((o.x + d.x) / 2f, minOf(o.y, d.y) - size.height * 0.18f)

            val full = Path().apply { moveTo(o.x, o.y); quadraticBezierTo(c.x, c.y, d.x, d.y) }
            val pm = PathMeasure().apply { setPath(full, false) }
            val len = pm.length

            // Remaining: dashed dim
            drawPath(
                full, FlightyColors.ArcRemaining,
                style = Stroke(width = 2.dp.toPx(),
                    pathEffect = PathEffect.dashPathEffect(floatArrayOf(4f * density, 5f * density))),
            )
            // Glow under-stroke
            drawPath(full, FlightyColors.BlueGlow, style = Stroke(width = 9.dp.toPx(), cap = StrokeCap.Round))
            // Flown: solid blue, trimmed to progress
            val flown = Path()
            pm.getSegment(0f, len * drawn.value, flown, true)
            drawPath(flown, FlightyColors.Blue, style = Stroke(width = 2.5.dp.toPx(), cap = StrokeCap.Round))

            // Plane marker + glow at live position
            val pos = FloatArray(2)
            pm.getPosTan(len * livePosition, pos, null)
            drawCircle(FlightyColors.BlueGlow, radius = 10.dp.toPx(), center = Offset(pos[0], pos[1]))
            drawCircle(FlightyColors.TextPrimary, radius = 4.dp.toPx(), center = Offset(pos[0], pos[1]))
        }
    }
}
```

### Flight Card

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Flight
import androidx.compose.material3.Icon
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.shadow

@Composable
fun FlightCard(
    airline: String, flightNo: String,
    originCode: String, destCode: String,
    depTime: String, arrTime: String,
    depGate: String, arrGate: String,
    meta: String, status: FlightStatus,
) {
    Column(
        Modifier
            .fillMaxWidth()
            .shadow(16.dp, RoundedCornerShape(16.dp), spotColor = Color.Black.copy(alpha = 0.4f))
            .clip(RoundedCornerShape(16.dp))
            .background(FlightyColors.Surface1)
            .border(1.dp, FlightyColors.Divider, RoundedCornerShape(16.dp))
            .padding(18.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.CenterVertically) {
            Text("$airline · $flightNo", style = FlightyText.CardTitle, color = FlightyColors.TextPrimary)
            StatusChip(status)
        }
        Row(verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(16.dp)) {
            Text(originCode, style = FlightyText.RouteCode, color = FlightyColors.TextPrimary)
            Icon(Icons.Filled.Flight, contentDescription = null,
                 tint = FlightyColors.Blue, modifier = Modifier.weight(1f).size(14.dp))
            Text(destCode, style = FlightyText.RouteCode, color = FlightyColors.TextPrimary)
        }
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
            Column(verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(depTime, style = FlightyText.TimeHero, color = FlightyColors.TextPrimary)
                Text(depGate, style = FlightyText.Gate, color = FlightyColors.TextSecondary)
            }
            Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(arrTime, style = FlightyText.TimeHero, color = FlightyColors.TextPrimary)
                Text(arrGate, style = FlightyText.Gate, color = FlightyColors.TextSecondary)
            }
        }
        Text(meta, style = FlightyText.Meta, color = FlightyColors.TextSecondary)
    }
}
```

### On-Time Percentage Ring

```kotlin
@Composable
fun OnTimeRing(percent: Int, size: Dp = 72.dp) {
    Box(Modifier.size(size), contentAlignment = Alignment.Center) {
        Canvas(Modifier.fillMaxSize()) {
            val stroke = 3.dp.toPx()
            drawArc(FlightyColors.Divider, 0f, 360f, false, style = Stroke(stroke))
            drawArc(
                FlightyColors.OnTime, -90f, 360f * percent / 100f, false,
                style = Stroke(stroke, cap = StrokeCap.Round),
            )
        }
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Text("$percent%", style = FlightyText.OnTimePct, color = FlightyColors.TextPrimary)
            Text("on time", style = FlightyText.Timestamp, color = FlightyColors.TextSecondary)
        }
    }
}
```

### Primary Button

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun FltButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "btn")
    Box(
        modifier
            .fillMaxWidth()
            .scale(scale)
            .clip(RoundedCornerShape(14.dp))
            .background(if (pressed) FlightyColors.BluePressed else FlightyColors.Blue)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            }
            .padding(vertical = 15.dp),
        contentAlignment = Alignment.Center,
    ) { Text(text, style = FlightyText.Button, color = FlightyColors.TextPrimary) }
}
```

## 4. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Flighty's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 92%-opaque `#0B0B0F` surface. **Active tint is Flighty Blue** — blue is the active indicator.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun FlightyBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = FlightyColors.Canvas.copy(alpha = 0.92f), tonalElevation = 0.dp) {
        val items = listOf(
            "Flights" to Icons.Filled.Flight,
            "Search"  to Icons.Filled.Search,
            "Airport" to Icons.Filled.Apartment,
            "Profile" to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = FlightyText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = FlightyColors.Blue,
                    selectedTextColor   = FlightyColors.Blue,
                    unselectedIconColor = FlightyColors.TextSecondary,
                    unselectedTextColor = FlightyColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // no Material pill — Flighty has none
                ),
            )
        }
    }
}
```

The Live-Activity-style status bar maps to an **ongoing notification with a custom `RemoteViews` / `Notification.MediaStyle`-like layout** plus, on API 31+, a foreground-service progress notification. Render the same origin→plane→destination track and status chip so the lock-screen surface matches the in-app card.

## 5. Navigation

The top bar carries the large title (left, `FlightyText.TitleLarge`) and a blue circular "+" add-flight button (right). Below it, a segmented control ("Upcoming · Past · Tracked") — a `#1A1A1F` track with a blue-filled selected segment (`RoundedCornerShape(10.dp)`). The live map is full-bleed with a `ModalBottomSheet` (Material 3) at three detents over it; style the sheet `containerColor = FlightyColors.Surface1` with a `#2E2E36` drag handle and a 20.dp top corner radius.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Arc draw-in | `Animatable(0f).animateTo(live, tween(900))`, trim the path via `PathMeasure.getSegment` |
| Plane advance | animate `livePosition`; recompute the marker via `PathMeasure.getPosTan` |
| Status change | `Crossfade`/`animateColorAsState` on the chip color + a single `HapticFeedbackType.LongPress` |
| Latest timeline node pulse | `rememberInfiniteTransition` scaling a glow ring in the node's status color |
| Card press | `animateFloatAsState` 1 → 0.98 `spring(dampingRatio = 0.8f)` |
| Sheet detents | `ModalBottomSheet` with `SheetState` + spring animation between detents |

```kotlin
// Plane advance
val live = remember { Animatable(0f) }
LaunchedEffect(updatedFraction) { live.animateTo(updatedFraction, tween(1000)) }
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's `.medium` impact.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity with Flighty's custom glyphs, export them as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Plane / arc | `airplane` | `Icons.Filled.Flight` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Refresh | `arrow.clockwise` | `Icons.Filled.Refresh` |
| Notify | `bell` / `bell.fill` | `Icons.Outlined.Notifications` / `Icons.Filled.Notifications` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Open map | `map` / `map.fill` | `Icons.Filled.Map` |
| Add flight | `plus` | `Icons.Filled.Add` |
| On-time | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Delay | `clock.badge.exclamationmark` | `Icons.Filled.WarningAmber` |
| Flights (tab) | `airplane` | `Icons.Filled.Flight` |
| Airport (tab) | `building.2.fill` | `Icons.Filled.Apartment` |
| Profile (tab) | `person.fill` | `Icons.Filled.Person` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the deep-black canvas wants `WindowCompat` light-content system bars. Apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` or `Scaffold` insets; the live map bleeds full-width with a top scrim and the tab bar clears the gesture nav.
- **Tabular figures**: `fontFeatureSettings = "tnum"` on every numeric flight field so live updates don't shift layout. Pin layout-sensitive text (status chips, gate/terminal, timeline timestamps, tab labels) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: never rely on color alone for status — give the chip a `contentDescription` (`"Delayed 22 minutes"`); describe the arc with a combined `contentDescription` like `"Flight 62 percent complete, en route SFO to JFK"`; the on-time ring gets `"$percent percent on time"`.
- **Touch targets**: Material guidance is 48.dp minimum. The CTA clears it; ensure card action icons (22.dp) and the map sheet grabber have ≥48.dp effective touch via padding.
- **Contrast**: `#8E8E96` on `#0B0B0F` passes WCAG AA at 13sp+. Validate amber `#FFD60A` text on its 15%-tint fill; for accessibility builds, raise the fill alpha or the chip can fail at small sizes.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Flighty's instrument-panel identity requires the fixed `#0B0B0F` canvas and single-blue accent regardless of wallpaper.
