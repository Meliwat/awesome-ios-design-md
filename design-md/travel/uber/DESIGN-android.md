# Uber (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Uber's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (the "Where to?" sheet, the black CTA, ride-option cards, the active-trip card, the driver beacon), `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the **map as hero**, the stark monochrome chrome, the pure-black primary CTA, Uber Move Mono for every ETA/fare/address, the bottom sheet as the primary container) while making everything idiomatic Android — Google Maps Compose instead of MapKit, a `ModalBottomSheet` with detents instead of `PresentationDetents`, `NavigationBar` instead of a UITabBar, `LocalHapticFeedback` instead of `.sensoryFeedback`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for the driver photo, and `com.google.maps.android:maps-compose` for the map hero. No color extraction, so `androidx.palette` is not required.

## 1. Color Tokens

```kotlin
// ui/theme/UberColors.kt
import androidx.compose.ui.graphics.Color

object UberColors {
    // Canvas & Brand
    val Black      = Color(0xFF000000)
    val White      = Color(0xFFFFFFFF)
    val CanvasDark = Color(0xFF0C0C0C) // near-black, NOT pure — matches dark map, less OLED bleed

    // Gray ramp (Base design system)
    val Gray50  = Color(0xFFF6F6F6)
    val Gray100 = Color(0xFFEEEEEE)
    val Gray200 = Color(0xFFE5E5E5)
    val Gray400 = Color(0xFFAFAFAF)
    val Gray600 = Color(0xFF757575)
    val Gray700 = Color(0xFF545454)
    val Gray900 = Color(0xFF2F2F2F)
    val Gray950 = Color(0xFF1A1A1A)

    // Functional (semantic — each does ONE job)
    val Green = Color(0xFF05A357) // active trip, driver arrived, confirmed
    val Red   = Color(0xFFD72113) // surge, cancel, error
    val Blue  = Color(0xFF0A47FF) // info, Uber for Business
    val Amber = Color(0xFFFFCB00) // high-tier surge, caution

    // Functional lightened for dark mode
    val GreenDark = Color(0xFF22C77A)
    val RedDark   = Color(0xFFFF4C3F)
    val BlueDark  = Color(0xFF3D6DFF)

    // Map tint (light)
    val MapLand  = Color(0xFFF2F2F2)
    val MapRoad  = Color(0xFFFFFFFF)
    val MapWater = Color(0xFFD9E5F2)
    // Map tint (dark)
    val MapLandDark  = Color(0xFF1E1E1E)
    val MapRoadDark  = Color(0xFF2F2F2F)
    val MapWaterDark = Color(0xFF0F1B2A)

    // Dark surfaces
    val DarkSurface1 = Color(0xFF1A1A1A)
    val DarkSurface2 = Color(0xFF2F2F2F)
    val DarkDivider  = Color(0xFF3A3A3A)
}
```

Uber's chrome is light-first (white canvas, black UI) and flips to a `#0C0C0C` dark canvas that matches the dark map. Wire both Material 3 schemes; the **primary is always black**, never a brand color.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val UberLight = lightColorScheme(
    primary        = UberColors.Black,        // primary CTA is always black
    onPrimary      = UberColors.White,
    background     = UberColors.White,
    onBackground   = UberColors.Black,
    surface        = UberColors.White,
    onSurface      = UberColors.Black,
    surfaceVariant = UberColors.Gray50,
    outline        = UberColors.Gray200,
    error          = UberColors.Red,
)

private val UberDark = darkColorScheme(
    primary        = UberColors.White,        // on dark, the "black bar" inverts to white
    onPrimary      = UberColors.Black,
    background     = UberColors.CanvasDark,
    onBackground   = UberColors.White,
    surface        = UberColors.DarkSurface1,
    onSurface      = UberColors.White,
    surfaceVariant = UberColors.DarkSurface2,
    outline        = UberColors.DarkDivider,
    error          = UberColors.RedDark,
)

@Composable
fun UberTheme(darkTheme: Boolean = isSystemInDarkTheme(), content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (darkTheme) UberDark else UberLight,
        typography = UberTypography,
        content = content,
    )
```

## 2. Typography

Uber Move is proprietary and ships in **three cuts** doing three jobs: Display (≥20pt), Text (UI), Mono (numbers/addresses). Drop the TTFs in `res/font/`. Fall back to the system font (Roboto); for the Mono cut fall back to a monospace family — the data-precision cue is load-bearing, never substitute the Text cut for numerals.

```kotlin
// ui/theme/UberType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val UberMove = FontFamily(
    Font(R.font.uber_move_medium, FontWeight.Medium),
    Font(R.font.uber_move_bold,   FontWeight.Bold),
)
val UberMoveText = FontFamily(
    Font(R.font.uber_move_text_regular, FontWeight.Normal),
    Font(R.font.uber_move_text_medium,  FontWeight.Medium),
    Font(R.font.uber_move_text_bold,    FontWeight.Bold),
)
val UberMoveMono = FontFamily(
    Font(R.font.uber_move_mono_regular, FontWeight.Normal),
    Font(R.font.uber_move_mono_medium,  FontWeight.Medium),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object UberText {
    val Hero        = TextStyle(UberMove,     fontWeight = FontWeight.Bold,   fontSize = 36.sp, lineHeight = 40.sp, letterSpacing = (-0.6).sp)
    val SheetTitle  = TextStyle(UberMove,     fontWeight = FontWeight.Bold,   fontSize = 24.sp, lineHeight = 28.sp, letterSpacing = (-0.4).sp)
    val NavTitle    = TextStyle(UberMove,     fontWeight = FontWeight.Medium, fontSize = 18.sp, lineHeight = 22.sp)
    val WhereTo     = TextStyle(UberMoveText, fontWeight = FontWeight.Medium, fontSize = 18.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val RowTitle    = TextStyle(UberMoveText, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(UberMoveText, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 21.sp)
    val Meta        = TextStyle(UberMoveText, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 18.sp)
    val LabelUpper  = TextStyle(UberMoveText, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Button      = TextStyle(UberMoveText, fontWeight = FontWeight.Medium, fontSize = 17.sp, lineHeight = 17.sp)
    val ButtonSmall = TextStyle(UberMoveText, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 15.sp)
    val Tab         = TextStyle(UberMoveText, fontWeight = FontWeight.Medium, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
    val Caption     = TextStyle(UberMoveText, fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 16.sp)
    // Mono — every fare, ETA, address
    val Price       = TextStyle(UberMoveMono, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 19.sp)
    val ETA         = TextStyle(UberMoveMono, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 17.sp)
    val ETABadge    = TextStyle(UberMoveMono, fontWeight = FontWeight.Medium, fontSize = 18.sp, lineHeight = 22.sp)
    val Address     = TextStyle(UberMoveMono, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val UberTypography = Typography(
    headlineLarge = UberText.Hero,
    headlineSmall = UberText.SheetTitle,
    titleMedium   = UberText.RowTitle,
    bodyMedium    = UberText.Body,
    labelLarge    = UberText.Button,
    labelSmall    = UberText.Tab,
)
```

## 3. Signature Components

### Primary Black CTA

The signature: always `#000000`, never branded. 56.dp tall, full-width inside the sheet, 8.dp corners.

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.CircularProgressIndicator
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun UberPrimaryButton(
    title: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    isLoading: Boolean = false,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, spring(0.85f, 600f), label = "ctaScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .fillMaxWidth()
            .height(56.dp)
            .scale(scale)
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) UberColors.Gray900 else UberColors.Black)
            .clickable(interaction, indication = null, enabled = !isLoading) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        if (isLoading) {
            CircularProgressIndicator(color = UberColors.White, strokeWidth = 2.dp, modifier = Modifier.size(20.dp))
        } else {
            Text(title, style = UberText.Button, color = UberColors.White)
        }
    }
}
```

### "Where to?" Bottom Sheet Input

The signature rideshare entry point. Leading "From"→"To" dot stack connected by a dashed line; the recognizable "Where to?" placeholder.

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Schedule
import androidx.compose.material3.Icon

@Composable
fun WhereToInput(onTap: () -> Unit, modifier: Modifier = Modifier) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(56.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(UberColors.Gray50)
            .clickable(onClick = onTap),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Column(
            Modifier.padding(start = 16.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(4.dp),
        ) {
            Box(Modifier.size(8.dp).background(UberColors.Black))
            repeat(3) { Box(Modifier.size(1.dp, 3.dp).background(UberColors.Gray400)) }
            Box(Modifier.size(8.dp).background(UberColors.Black))
        }
        Text("Where to?", style = UberText.WhereTo, color = UberColors.Black)
        Spacer(Modifier.weight(1f))
        Row(
            Modifier
                .padding(end = 8.dp)
                .clip(CircleShape)
                .border(1.dp, UberColors.Gray200, CircleShape)
                .padding(horizontal = 12.dp, vertical = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Icon(Icons.Filled.Schedule, contentDescription = null, tint = UberColors.Black, modifier = Modifier.size(14.dp))
            Text("Later", style = UberText.ButtonSmall, color = UberColors.Black)
        }
    }
}
```

### Ride Option Card

72.dp selectable row; selected = 2.dp black border + `#F6F6F6` fill. Price/ETA in Uber Move Mono.

```kotlin
import androidx.compose.material.icons.filled.Person
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun RideOptionCard(
    name: String,
    eta: String,
    capacity: Int,
    price: String,
    carImageUrl: String,
    selected: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val haptics = LocalHapticFeedback.current
    val borderWidth by animateFloatAsState(if (selected) 2f else 1f, label = "rideBorder")

    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(72.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(if (selected) UberColors.Gray50 else UberColors.White)
            .border(borderWidth.dp, if (selected) UberColors.Black else UberColors.Gray200, RoundedCornerShape(8.dp))
            .clickable {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS selection
                onClick()
            }
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        AsyncImage(model = carImageUrl, contentDescription = null, modifier = Modifier.size(56.dp), contentScale = ContentScale.Fit)
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Text(name, style = UberText.RowTitle, color = UberColors.Black)
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(2.dp)) {
                    Icon(Icons.Filled.Person, null, tint = UberColors.Gray600, modifier = Modifier.size(10.dp))
                    Text("$capacity", style = UberText.Caption, color = UberColors.Gray600)
                }
            }
            Text("$eta away", style = UberText.Address, color = UberColors.Gray600)
        }
        Text(price, style = UberText.Price, color = UberColors.Black)
    }
}
```

### Active Trip Card

Driver photo + name + rating, license plate in a `#F6F6F6` Mono pill, Message/Call/Share circular actions.

```kotlin
import androidx.compose.material.icons.filled.Call
import androidx.compose.material.icons.filled.Message
import androidx.compose.material.icons.filled.Share
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.HorizontalDivider
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun ActiveTripCard(
    driverName: String,
    rating: Double,
    carModel: String,
    plate: String,
    driverPhotoUrl: String,
    modifier: Modifier = Modifier,
) {
    Column(modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(16.dp)) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(12.dp)) {
            AsyncImage(
                model = driverPhotoUrl, contentDescription = "$driverName photo",
                modifier = Modifier.size(48.dp).clip(CircleShape), contentScale = ContentScale.Crop,
            )
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(driverName, style = UberText.RowTitle, color = UberColors.Black)
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    Icon(Icons.Filled.Star, null, tint = UberColors.Gray600, modifier = Modifier.size(11.dp))
                    Text(String.format("%.2f", rating), style = UberText.ETA, color = UberColors.Gray600)
                }
            }
            Box(
                Modifier.clip(CircleShape).background(UberColors.Gray50).padding(horizontal = 10.dp, vertical = 4.dp)
            ) { Text(plate, style = UberText.ETA, color = UberColors.Black) }
        }
        Text(carModel, style = UberText.Meta, color = UberColors.Gray700)
        HorizontalDivider(color = UberColors.Gray200)
        Row(horizontalArrangement = Arrangement.spacedBy(24.dp)) {
            TripAction(Icons.Filled.Message, "Message")
            TripAction(Icons.Filled.Call, "Call")
            TripAction(Icons.Filled.Share, "Share")
        }
    }
}

@Composable
private fun TripAction(icon: ImageVector, label: String) {
    Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(6.dp)) {
        Box(
            Modifier.size(44.dp).clip(CircleShape).background(UberColors.Gray50),
            contentAlignment = Alignment.Center,
        ) { Icon(icon, contentDescription = label, tint = UberColors.Black, modifier = Modifier.size(20.dp)) }
        Text(label, style = UberText.Caption, color = UberColors.Gray600)
    }
}
```

### Bottom Sheet with Detents

iOS uses 3 `PresentationDetents`. The Compose analog is Material 3 `ModalBottomSheet` (or a custom `AnchoredDraggable`). Top corners 16.dp, a 36×4.dp grabber, bottom flush.

```kotlin
import androidx.compose.material3.ExperimentalMaterial3Api
import androidx.compose.material3.ModalBottomSheet
import androidx.compose.material3.SheetState
import androidx.compose.material3.rememberModalBottomSheetState

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun UberBottomSheet(
    sheetState: SheetState = rememberModalBottomSheetState(skipPartiallyExpanded = false),
    onDismiss: () -> Unit = {},
    content: @Composable ColumnScope.() -> Unit,
) {
    ModalBottomSheet(
        onDismissRequest = onDismiss,
        sheetState = sheetState,
        containerColor = UberColors.White,
        shape = RoundedCornerShape(topStart = 16.dp, topEnd = 16.dp), // bottom flush
        dragHandle = {
            Box(Modifier.fillMaxWidth().padding(top = 6.dp, bottom = 14.dp), contentAlignment = Alignment.Center) {
                Box(Modifier.size(36.dp, 4.dp).clip(RoundedCornerShape(2.dp)).background(UberColors.Gray200))
            }
        },
    ) {
        Column(Modifier.padding(horizontal = 16.dp, vertical = 0.dp), content = content)
    }
}
```

## 4. Map as Hero + Base Tokens (the distinctive interaction)

Uber has no color extraction. Its defining system is **the map filling 60–80% of every screen** with a custom-tinted style and the Base monochrome polyline/pin. On Android use Google Maps Compose with a JSON style string built from the Base map tokens.

```kotlin
import com.google.maps.android.compose.GoogleMap
import com.google.maps.android.compose.MapProperties
import com.google.maps.android.compose.MapType
import com.google.maps.android.compose.Marker
import com.google.maps.android.compose.Polyline
import com.google.maps.android.compose.rememberCameraPositionState
import com.google.android.gms.maps.model.MapStyleOptions
import com.google.android.gms.maps.model.LatLng

@Composable
fun UberMap(
    pickup: LatLng,
    destination: LatLng,
    route: List<LatLng>,
    modifier: Modifier = Modifier,
    darkTheme: Boolean = isSystemInDarkTheme(),
) {
    val cameraPositionState = rememberCameraPositionState()
    GoogleMap(
        modifier = modifier.fillMaxSize(), // map IS the hero — fills the screen, sheet floats over
        cameraPositionState = cameraPositionState,
        properties = MapProperties(
            mapType = MapType.NORMAL,
            // Load a JSON style derived from Base tokens: land #F2F2F2, roads #FFFFFF, water #D9E5F2
            // (dark: land #1E1E1E, roads #2F2F2F, water #0F1B2A); POIs excluded.
            mapStyleOptions = MapStyleOptions(if (darkTheme) UBER_MAP_STYLE_DARK else UBER_MAP_STYLE_LIGHT),
        ),
    ) {
        Marker(state = com.google.maps.android.compose.MarkerState(pickup))
        Marker(state = com.google.maps.android.compose.MarkerState(destination)) // swap icon → DestinationPin
        Polyline(
            points = route,
            color = if (darkTheme) UberColors.White else UberColors.Black, // 5.dp mono polyline
            width = with(androidx.compose.ui.platform.LocalDensity.current) { 5.dp.toPx() },
        )
    }
}

// Provide these as raw style JSON strings (Base map tokens → Google Maps style array):
const val UBER_MAP_STYLE_LIGHT = """[ /* land #F2F2F2, water #D9E5F2, roads #FFFFFF, labels muted, POIs off */ ]"""
const val UBER_MAP_STYLE_DARK  = """[ /* land #1E1E1E, water #0F1B2A, roads #2F2F2F, labels light, POIs off */ ]"""
```

### Destination Pin & Floating Map Control

```kotlin
import androidx.compose.foundation.layout.Box
import androidx.compose.ui.draw.shadow
import androidx.compose.material.icons.filled.MyLocation

@Composable
fun DestinationPin(modifier: Modifier = Modifier) {
    Box(modifier.size(32.dp, 40.dp), contentAlignment = Alignment.BottomCenter) {
        Box(
            Modifier
                .size(28.dp, 8.dp)
                .background(Color.Black.copy(alpha = 0.25f), CircleShape) // ground shadow
        )
        Box(
            Modifier
                .size(32.dp)
                .clip(RoundedCornerShape(4.dp))
                .background(UberColors.Black)
        )
    }
}

@Composable
fun FloatingMapButton(icon: ImageVector, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Box(
        modifier = modifier
            .size(44.dp)
            .shadow(8.dp, CircleShape, spotColor = Color.Black.copy(alpha = 0.12f))
            .clip(CircleShape)
            .background(UberColors.White)
            .clickable(onClick = onClick),
        contentAlignment = Alignment.Center,
    ) { Icon(icon, contentDescription = null, tint = UberColors.Black, modifier = Modifier.size(20.dp)) }
}
```

### Driver Arrival Beacon

Three concentric green rings emanating from the car icon. Compose uses `rememberInfiniteTransition` with staggered delays.

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.material.icons.filled.DirectionsCar
import androidx.compose.ui.draw.scale

@Composable
fun DriverBeacon(modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "beacon")
    Box(modifier, contentAlignment = Alignment.Center) {
        repeat(3) { i ->
            val progress by t.animateFloat(
                initialValue = 0f, targetValue = 1f,
                animationSpec = infiniteRepeatable(
                    tween(1800, delayMillis = 0, easing = EaseInOut),
                    RepeatMode.Restart,
                    initialStartOffset = StartOffset(i * 600),
                ),
                label = "ring$i",
            )
            Box(
                Modifier
                    .size(48.dp)
                    .scale(1f + progress * 1.4f)
                    .background(UberColors.Green.copy(alpha = (0.3f - i * 0.1f) * (1f - progress)), CircleShape)
            )
        }
        Box(
            Modifier.size(24.dp).clip(CircleShape).background(UberColors.White),
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.DirectionsCar, "Driver has arrived", tint = UberColors.Black, modifier = Modifier.size(14.dp)) }
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Material 3 `NavigationBar`. Uber's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a near-opaque surface with a hairline top border. **Active tint is black** (filled variant), inactive `#757575` (outline) — labels always shown.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun UberBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = UberColors.White.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Services" to Icons.Filled.GridView,
            "Activity" to Icons.Filled.Schedule,
            "Account"  to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = UberText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = UberColors.Black, // black, not a brand color
                    selectedTextColor   = UberColors.Black,
                    unselectedIconColor = UberColors.Gray600,
                    unselectedTextColor = UberColors.Gray600,
                    indicatorColor      = Color.Transparent, // no Material pill — Uber has none
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| CTA press | `animateFloatAsState` 1 → 0.98 with `spring(dampingRatio = 0.85f)`, fill → `#2F2F2F`, `HapticFeedbackType.LongPress` |
| Sheet detent change | `ModalBottomSheet` / `AnchoredDraggable` spring snap to collapsed / medium / expanded |
| Ride option select | border `animateFloatAsState` 1.dp → 2.dp over 150ms, `HapticFeedbackType.TextHandleMove` |
| Destination pin drop | `Animatable` spring with bounce on place; shadow grows with the pin |
| Driver pulse | `rememberInfiniteTransition`, 3 staggered rings, 1.8s `RepeatMode.Restart`, `StartOffset(i*600)` |
| Route polyline draw | animate the polyline point count or a dash phase over 400ms `tween` on trip confirm |
| Recenter tap | `cameraPositionState.animate(...)` ~0.6s, no haptic (soft action) |

```kotlin
// Ride-option border animating 1 → 2.dp on selection (DESIGN.md §6)
val border by animateFloatAsState(
    targetValue = if (selected) 2f else 1f,
    animationSpec = tween(150),
    label = "rideSelect",
)
```

Haptics: prefer `LocalHapticFeedback`. For the warning on Cancel-ride confirmation use a `Vibrator` double-pulse (`VibrationEffect.createWaveform(longArrayOf(0, 30, 60, 30), -1)`); for "Driver arrived" a single `VibrationEffect.createOneShot(20, ...)` approximates iOS `.success`.

## 7. Icons

Uber ships geometric 2pt-stroke glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity (the destination pin, the car silhouette), ship vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Recenter | `location.fill` | `Icons.Filled.MyLocation` |
| Zoom In / Out | `plus` / `minus` | `Icons.Filled.Add` / `Icons.Filled.Remove` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBackIos` |
| Close | `xmark` | `Icons.Filled.Close` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Clock (Later) | `clock` | `Icons.Filled.Schedule` |
| Home (saved) | `house.fill` | `Icons.Filled.Home` |
| Work (saved) | `briefcase.fill` | `Icons.Filled.Work` |
| Star (saved) | `star.fill` | `Icons.Filled.Star` |
| Person (capacity) | `person.fill` | `Icons.Filled.Person` |
| Message | `message.fill` | `Icons.Filled.Message` |
| Call | `phone.fill` | `Icons.Filled.Call` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Home tab | `house.fill` | `Icons.Filled.Home` |
| Services tab | `square.grid.2x2.fill` | `Icons.Filled.GridView` |
| Activity tab | `clock.fill` | `Icons.Filled.Schedule` |
| Account tab | `person.fill` | `Icons.Filled.Person` |
| Payment card | `creditcard.fill` | `Icons.Filled.CreditCard` |
| Car | `car.fill` | `Icons.Filled.DirectionsCar` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; Maps Compose + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. Add a Google Maps API key in the manifest for the map hero.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the map extends under the status bar / camera cutout. Floating map controls must respect `WindowInsets.systemBars`; the bottom sheet's smallest detent must sit above the gesture-nav inset (`WindowInsets.navigationBars`).
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on row titles, sheet titles, body. Pin layout-sensitive text: the destination-pin label, license-plate pill, and tab labels are visual-consistency / layout-critical — derive from `dp` or wrap in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. Cap the 56.dp CTA's label at ~120% then ellipsize so the bar never grows.
- **TalkBack**: add `contentDescription` on map markers ("Pickup at 212 Market Street"); group the active-trip card into one element with `Modifier.semantics(mergeDescendants = true)`; announce the pulse beacon as "Driver has arrived" (it already carries that `contentDescription`).
- **Touch targets**: Material guidance is 48.dp minimum. The 56.dp CTA and full-row 56–72.dp list rows clear it; the 44.dp floating map buttons should carry a 48.dp hit area via padding even though the visual circle is 44.dp; the payment chip is a full-width 44.dp+ row.
- **Contrast**: `#757575` on `#FFFFFF` passes WCAG AA for 14sp+ Regular. The 11sp UPPERCASE labels and 13sp Mono address are below that — use `#545454` (Gray700) for anything 13sp or smaller targeting compliance. Green `#05A357` on white passes AA at 14sp+ Medium. White on the black CTA passes AAA.
- **Reduce motion**: when a reduced-motion preference is set, freeze the driver beacon as a single static green ring and swap the polyline stroke animation for an instant draw.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme` — Uber's identity is the strict monochrome chrome (black CTA, white canvas, functional-only color). Tinting the primary or surface from the wallpaper would break the "signage, not software" feel and the semantic meaning of green/red/blue.
