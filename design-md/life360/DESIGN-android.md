# Life360 (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Life360's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the family-map chrome + member sheet, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Life360's violet night, the dark-styled map, fixed per-member identity colors, the floating Circle pill, the draggable member sheet) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `BottomSheetScaffold` instead of SwiftUI detents, Google Maps Compose, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, `maps-compose` `4.3+`, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for member photos. No color extraction — member identity colors are assigned data, brand is fixed; Palette is not used. A light scheme is provided; dark (violet night) is the default.

## 1. Color Tokens

```kotlin
// ui/theme/Life360Colors.kt
import androidx.compose.ui.graphics.Color

object Life360Colors {
    // Canvas & Surfaces (Dark — default)
    val Canvas   = Color(0xFF161325) // violet-tinted night
    val Surface1 = Color(0xFF1F1B33)
    val Surface2 = Color(0xFF2A2542)
    val Sheet    = Color(0xFF211D36)
    val Divider  = Color(0xFF332E4D)
    val Border   = Color(0xFF3D3759)

    // Map palette (dark-styled — keeps member pins salient)
    val MapBase  = Color(0xFF1A2138)
    val MapRoad  = Color(0xFF2C3654)
    val MapPark  = Color(0xFF1B3A2E)
    val MapWater = Color(0xFF16314A)
    val MapLabel = Color(0xFF7E7A99)

    // Surfaces (light)
    val CanvasLight  = Color(0xFFFFFFFF)
    val SurfaceLight = Color(0xFFF5F3FA)
    val MapBaseLight = Color(0xFFEDEFF5)
    val DividerLight = Color(0xFFE6E3F0)

    // Text
    val TextPrimary   = Color(0xFFECEAF5)
    val TextSecondary = Color(0xFFA6A0C2)
    val TextTertiary  = Color(0xFF6F6990)
    val TextOnLight   = Color(0xFF1C1830)

    // Brand (Life360 Purple — NEVER a member color)
    val Purple        = Color(0xFF582C83)
    val PurpleLight   = Color(0xFF7E57C2)
    val PurpleBright  = Color(0xFF8B5CF6)
    val PurplePressed = Color(0xFF4A2470)

    // Safety semantic (reserved — rationed)
    val Safe    = Color(0xFF34C759)
    val SOS     = Color(0xFFFF6B6B)
    val Warning = Color(0xFFFFB020)
}

// Member identity palette — assign in Circle join order; fixed per person everywhere.
val MemberPalette = listOf(
    Color(0xFFF2A33C), // amber
    Color(0xFF2DD4BF), // teal
    Color(0xFFF472B6), // pink
    Color(0xFF60A5FA), // blue
    Color(0xFFFB7185), // coral
    Color(0xFFA3E635), // lime
    Color(0xFF38BDF8), // sky
)
fun memberColor(slot: Int): Color = MemberPalette[slot % MemberPalette.size]
fun batteryColor(pct: Int): Color = if (pct <= 20) Life360Colors.SOS else Life360Colors.Safe
```

Wire it into both schemes. Life360 is violet-night dark by default; a day-styled light scheme is provided.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val Life360Dark = darkColorScheme(
    primary        = Life360Colors.PurpleLight,  // brand spine
    onPrimary      = Color.White,
    background     = Life360Colors.Canvas,
    onBackground   = Life360Colors.TextPrimary,
    surface        = Life360Colors.Surface1,
    onSurface      = Life360Colors.TextPrimary,
    surfaceVariant = Life360Colors.Surface2,
    outline        = Life360Colors.Divider,
    error          = Life360Colors.SOS,
)

private val Life360Light = lightColorScheme(
    primary        = Life360Colors.PurpleLight,
    onPrimary      = Color.White,
    background      = Life360Colors.CanvasLight,
    onBackground   = Life360Colors.TextOnLight,
    surface        = Life360Colors.SurfaceLight,
    onSurface      = Life360Colors.TextOnLight,
    surfaceVariant = Color(0xFFEDEAF5),
    outline        = Life360Colors.DividerLight,
    error          = Life360Colors.SOS,
)

@Composable
fun Life360Theme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) Life360Dark else Life360Light,
    typography = Life360Typography,
    content = content,
)
```

## 2. Typography

Life360's brand face is friendly rounded-humanist; **Plus Jakarta Sans** is the closest free analog (SIL OFL — drop the TTFs in `res/font/`). Single brand face, no user switching; member names bold for a reassuring tone.

```kotlin
// ui/theme/Life360Type.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val PlusJakarta = FontFamily(
    Font(R.font.plus_jakarta_sans_regular,   FontWeight.Normal),
    Font(R.font.plus_jakarta_sans_medium,    FontWeight.Medium),
    Font(R.font.plus_jakarta_sans_semibold,  FontWeight.SemiBold),
    Font(R.font.plus_jakarta_sans_bold,      FontWeight.Bold),
    Font(R.font.plus_jakarta_sans_extrabold, FontWeight.ExtraBold),
)

// Named ramp — mirrors DESIGN.md §3 (pt → sp 1:1)
object Life360Text {
    val Display    = TextStyle(PlusJakarta, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.5).sp)
    val Title      = TextStyle(PlusJakarta, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section    = TextStyle(PlusJakarta, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val MemberName = TextStyle(PlusJakarta, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body       = TextStyle(PlusJakarta, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val RowTitle   = TextStyle(PlusJakarta, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Status     = TextStyle(PlusJakarta, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 17.sp)
    val Meta       = TextStyle(PlusJakarta, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Caption    = TextStyle(PlusJakarta, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.4.sp)
    val PinInitial = TextStyle(PlusJakarta, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab        = TextStyle(PlusJakarta, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button     = TextStyle(PlusJakarta, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp)
    val SOS        = TextStyle(PlusJakarta, fontWeight = FontWeight.ExtraBold, fontSize = 15.sp, lineHeight = 15.sp, letterSpacing = 0.5.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val Life360Typography = Typography(
    headlineLarge  = Life360Text.Display,
    headlineMedium = Life360Text.Title,
    titleMedium    = Life360Text.RowTitle,
    bodyMedium     = Life360Text.Body,
    labelSmall     = Life360Text.Tab,
)
```

## 3. Signature Components

### Member Pin (the atomic Life360 element)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun MemberPin(
    initial: String,
    color: Color,            // the member's FIXED identity color
    photoUrl: String? = null,
    selected: Boolean = false,
) {
    val pulse by animateFloatAsState(if (selected) 1.04f else 1f, spring(), label = "pinPulse")
    Column(horizontalAlignment = Alignment.CenterHorizontally, modifier = Modifier.scale(pulse)) {
        Box(
            Modifier
                .size(46.dp)
                .shadow(12.dp, CircleShape, ambientColor = Color.Black, spotColor = Color.Black)
                .clip(CircleShape)
                .background(color)
                .border(if (selected) 4.dp else 3.dp, Color.White, CircleShape),
            contentAlignment = Alignment.Center,
        ) {
            if (photoUrl != null) {
                AsyncImage(photoUrl, null, Modifier.size(40.dp).clip(CircleShape))
            } else {
                Text(initial, style = Life360Text.PinInitial, color = Color.White)
            }
        }
        // downward pointer
        Canvas(Modifier.size(12.dp, 8.dp)) {
            val p = Path().apply {
                moveTo(size.width / 2f, size.height); lineTo(0f, 0f); lineTo(size.width, 0f); close()
            }
            drawPath(p, Color.White)
        }
    }
}
```

### Member Sheet Row

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.LocationOn
import androidx.compose.material3.Icon

@Composable
fun MemberRow(
    initial: String,
    color: Color,
    name: String,
    placeText: String,
    battery: Int,
    onClick: () -> Unit,
) {
    val bc = batteryColor(battery)
    Row(
        Modifier.fillMaxWidth().heightIn(min = 62.dp).clickable { onClick() }
            .padding(horizontal = 18.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(42.dp).clip(CircleShape).background(color), contentAlignment = Alignment.Center) {
            Text(initial, style = Life360Text.RowTitle, color = Color.White)
        }
        Column(Modifier.weight(1f)) {
            Text(name, style = Life360Text.MemberName, color = Life360Colors.TextPrimary)
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                Icon(Icons.Filled.LocationOn, null, tint = Life360Colors.TextSecondary, modifier = Modifier.size(11.dp))
                Text(placeText, style = Life360Text.Status, color = Life360Colors.TextSecondary)
            }
        }
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
            BatteryGlyph(battery, bc)
            Text("$battery%", style = Life360Text.Status, color = bc)
        }
    }
}

@Composable
fun BatteryGlyph(level: Int, color: Color) {
    Row(verticalAlignment = Alignment.CenterVertically) {
        Box(
            Modifier.size(22.dp, 11.dp)
                .border(1.5.dp, color, RoundedCornerShape(3.dp))
                .padding(1.5.dp),
            contentAlignment = Alignment.CenterStart,
        ) {
            Box(Modifier.fillMaxHeight().fillMaxWidth(level / 100f).clip(RoundedCornerShape(1.dp)).background(color))
        }
        Box(Modifier.size(2.dp, 4.dp).background(color))
    }
}
```

### Circle Selector Pill

```kotlin
import androidx.compose.material.icons.filled.ExpandMore
import androidx.compose.material.icons.filled.Notifications

@Composable
fun CircleSelector(
    circleName: String,
    hasAlerts: Boolean,
    onSwitch: () -> Unit,
    onBell: () -> Unit,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(999.dp))
            .background(Life360Colors.Surface1.copy(alpha = 0.92f)) // Android has no live blur — opaque-ish pill
            .border(1.dp, Life360Colors.Border, RoundedCornerShape(999.dp))
            .shadow(20.dp, RoundedCornerShape(999.dp), spotColor = Color.Black.copy(alpha = 0.45f))
            .padding(horizontal = 16.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.SpaceBetween,
    ) {
        Row(
            Modifier.clickable { onSwitch() },
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(7.dp),
        ) {
            Text(circleName, style = Life360Text.RowTitle.copy(fontWeight = FontWeight.Bold), color = Life360Colors.TextPrimary)
            Icon(Icons.Filled.ExpandMore, null, tint = Life360Colors.TextSecondary, modifier = Modifier.size(13.dp))
        }
        Box(
            Modifier.size(30.dp).clip(CircleShape).background(Life360Colors.Surface2).clickable { onBell() },
            contentAlignment = Alignment.Center,
        ) {
            Icon(Icons.Filled.Notifications, "Alerts", tint = Life360Colors.TextPrimary, modifier = Modifier.size(15.dp))
            if (hasAlerts) Box(Modifier.align(Alignment.TopEnd).size(8.dp).clip(CircleShape).background(Life360Colors.SOS))
        }
    }
}
```

### Primary & SOS Buttons

```kotlin
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.runtime.*
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.platform.LocalHapticFeedback
import kotlinx.coroutines.delay

@Composable
fun Life360PrimaryButton(title: String, onClick: () -> Unit) {
    Button(
        onClick = onClick,
        shape = RoundedCornerShape(28.dp),
        colors = ButtonDefaults.buttonColors(containerColor = Life360Colors.PurpleLight, contentColor = Color.White),
        contentPadding = PaddingValues(horizontal = 26.dp, vertical = 14.dp),
    ) { Text(title, style = Life360Text.Button) }
}

@Composable
fun SOSButton(onTrigger: () -> Unit) {
    var progress by remember { mutableFloatStateOf(0f) }
    var holding by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current
    val animated by animateFloatAsState(if (holding) 1f else 0f,
        animationSpec = androidx.compose.animation.core.tween(1500), label = "sosFill")

    LaunchedEffect(holding) {
        if (holding) { delay(1500); if (holding) { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onTrigger() } }
    }
    Box(
        Modifier
            .clip(RoundedCornerShape(28.dp))
            .background(Life360Colors.SOS.copy(alpha = 0.55f))
            .pointerInput(Unit) {
                detectTapGestures(onPress = {
                    holding = true; tryAwaitRelease(); holding = false
                })
            },
    ) {
        Box(Modifier.matchParentSize().fillMaxWidth(animated).background(Life360Colors.SOS))
        Text("SOS", style = Life360Text.SOS, color = Color.White,
            modifier = Modifier.padding(horizontal = 26.dp, vertical = 14.dp))
    }
}
```

## 4. Navigation + Member Sheet

Life360 has a 4-tab bottom strip and a draggable member sheet over a full-bleed map. On Android, model the strip as a `NavigationBar` (active = Purple Light, no tint pill) and the sheet as a `BottomSheetScaffold`.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun Life360BottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = Life360Colors.Sheet, tonalElevation = 0.dp) {
        val items = listOf(
            "Map"     to Icons.Filled.LocationOn,
            "Places"  to Icons.Filled.Home,
            "Driving" to Icons.Filled.DirectionsCar,
            "Safety"  to Icons.Filled.Shield,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, Modifier.size(21.dp)) },
                label = { Text(label, style = Life360Text.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = Life360Colors.PurpleLight,   // brand
                    selectedTextColor = Life360Colors.PurpleLight,
                    unselectedIconColor = Life360Colors.TextTertiary,
                    unselectedTextColor = Life360Colors.TextTertiary,
                    indicatorColor = Color.Transparent,              // no Material pill — purple is enough
                ),
            )
        }
    }
}
```

Use `BottomSheetScaffold` with `sheetPeekHeight = 180.dp`, `sheetContainerColor = Life360Colors.Sheet`, `sheetShape = RoundedCornerShape(topStart = 22.dp, topEnd = 22.dp)`, and a 38.dp drag handle. The map is `GoogleMap` (maps-compose) with a dark `MapStyleOptions` JSON using `MapBase`/`MapRoad`/`MapPark`/`MapWater`; member pins are `MarkerComposable`s rendering `MemberPin`. Place geofences are `Circle` overlays — `fillColor = PurpleLight.copy(alpha = 0.16f)`, `strokeColor = PurpleLight.copy(alpha = 0.55f)`, `strokeWidth = 2.dp`.

## 5. Motion

Life360 motion is calm and reassuring — pins glide, sheets snap softly, red is the only urgent note.

| Moment | Compose recipe |
|--------|----------------|
| Pin movement | animate the marker `LatLng` with `Animatable` over ~600ms `tween(easing = FastOutSlowIn)`; pulse `scale` 1→1.08→1 `keyframes` |
| Map auto-frame | `cameraPositionState.animate(CameraUpdateFactory.newLatLngBounds(bounds, padding), 400)` |
| Pin tap → focus | `cameraPositionState.animate(...center..., 350)` + grow ring (`border` 3→4dp) + member glow |
| Member sheet | `BottomSheetScaffold` drag → nearest anchor `spring(dampingRatio = 0.85f, stiffness = Medium)` |
| Geofence entry ripple | overlay `Circle` radius `animateFloatAsState` 1→1.06 + alpha 1→0 `tween(350)` |
| Breadcrumb draw | animate the `Polyline` point count head→tail over ~500ms |
| Tab switch | `Crossfade(tween(200))`; the `GoogleMap` persists underneath |

```kotlin
// Pin glide — the canonical Life360 motion (never teleport)
val lat = remember { Animatable(start.latitude.toFloat()) }
val lng = remember { Animatable(start.longitude.toFloat()) }
LaunchedEffect(target) {
    launch { lat.animateTo(target.latitude.toFloat(), tween(600, easing = FastOutSlowInEasing)) }
    launch { lng.animateTo(target.longitude.toFloat(), tween(600, easing = FastOutSlowInEasing)) }
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the sheet detent snap and the SOS completion; `view.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` for pin-tap selection; for a new Alert-red notification fire `HapticFeedbackConstants.REJECT`. The SOS hold should emit an escalating series of ticks while filling.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The member pin and battery glyph are custom `Canvas`/`Box` drawings, not icon glyphs.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Map (tab) | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Places (tab) | `house.fill` | `Icons.Filled.Home` |
| Driving (tab) | `car.fill` | `Icons.Filled.DirectionsCar` |
| Safety (tab) | `shield.fill` | `Icons.Filled.Shield` |
| Member at place | `mappin.circle.fill` | `Icons.Filled.LocationOn` |
| Member driving | `clock.fill` | `Icons.Filled.Schedule` |
| Circle chevron | `chevron.down` | `Icons.Filled.ExpandMore` |
| Notification bell | `bell.fill` | `Icons.Filled.Notifications` |
| Add place | `plus` | `Icons.Filled.Add` |
| Recenter map | `location.fill` | `Icons.Filled.MyLocation` |
| Place — Home | `house.fill` | `Icons.Filled.Home` |
| Place — School | `graduationcap.fill` | `Icons.Filled.School` |
| Place — Work | `briefcase.fill` | `Icons.Filled.Work` |
| Driving event | `exclamationmark.triangle.fill` | `Icons.Filled.Warning` |
| Crash detected | `car.side.and.exclamationmark` | `Icons.Filled.CarCrash` |
| Check in | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Invite | `person.badge.plus` | `Icons.Filled.PersonAdd` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `BottomSheetScaffold` + maps-compose comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`, `maps-compose 4.3+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the full-bleed dark map wants light-content system bars. Only chrome respects insets — the Circle pill below the cutout, the member sheet + `NavigationBar` above the gesture nav; the map draws under both. Forms use `Modifier.imePadding()`.
- **Map styling is mandatory**: apply a dark `MapStyleOptions` JSON (`MapBase`/`MapRoad`/`MapPark`/`MapWater`) so member pins are the brightest objects. Use a day style only in light mode. Do **not** enable Material You `dynamicColorScheme()` — member identity colors + brand purple + safety colors are semantic and must not be wallpaper-tinted.
- **Member identity color is data, not theme**: persist each member's slot/color on the member model; reuse it for the pin ring, sheet avatar, breadcrumb `Polyline`, and driving-report trail. Never recompute per screen.
- **Font scaling**: `sp` honors the user's font scale — keep it on Display/Title/Section/MemberName/Body/RowTitle and let member rows grow. Pin layout-sensitive text (pin initials, 10sp tab labels, map/place-tag text) via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: announce a pin/row as "{name}, at {place}, {time} ago, battery {n} percent" via `Modifier.semantics(mergeDescendants = true)`; the identity color is decorative — the name carries identity so color-blind users are never reliant on hue.
- **Color is not the only signal**: member name always accompanies the color; battery shows a percentage + glyph fill, not just red/green; safety state has text ("Left School"), not just a red dot.
- **SOS accessibility**: the press-and-hold must have an alternative for motor-impaired users — expose an "Emergency" action in the Safety tab and a `Modifier.semantics { onClick(...) }` / custom action on the button so it's triggerable without a sustained long-press.
- **Touch targets**: Material guidance is 48.dp. Member rows are full-row tappable (≥62.dp); the pin visible bubble ≥46.dp; the Circle pill ≥44.dp; tab items get a 48.dp hit area; the SOS button ≥52.dp.
- **Contrast**: `#ECEAF5` on `#161325` ≈ 14:1 (AAA). Member colors back white initials / are pin rings — validated for the white initial; never use a member hue as small text on the dark canvas.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, snap pins instead of gliding, skip the geofence ripple and breadcrumb draw, keep the sheet snap; never disable the SOS haptic ramp (it is a safety affordance).
- **Dark mode**: the violet night + dark map is the default. The light scheme swaps canvas/map to the day set, but member colors, brand purple, Safe green, and SOS red are identical across modes (they carry meaning).
