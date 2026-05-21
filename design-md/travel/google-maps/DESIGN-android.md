# Google Maps (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Google Maps' visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (floating search bar, Directions FAB, location dot, teardrop pin, bottom-sheet drawer, turn card), `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? Google Maps *is* a Material app — the iOS build is Material wearing SF Pro. On Android this guide goes home: the real Maps Compose SDK (`com.google.maps.android:maps-compose`) draws the map, a true `FloatingActionButton` is the Directions action, the drawer is `BottomSheetScaffold` with anchored detents, and `sp`/`dp` replace `pt`. Because it is Material-native, this is one of the few apps where you should **embrace `dynamicColorScheme()`** for chrome (see §8).

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [`maps-compose`](https://github.com/googlemaps/android-maps-compose) `4.3+` for the map surface, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for place photos.

## 1. Color Tokens

```kotlin
// ui/theme/GMapsColors.kt
import androidx.compose.ui.graphics.Color

object GMapsColors {
    // Canvas & Surfaces (Light)
    val Canvas       = Color(0xFFFFFFFF)
    val SurfaceMuted = Color(0xFFF1F3F4)
    val Divider      = Color(0xFFDADCE0)

    // Text
    val TextPrimary   = Color(0xFF202124)
    val TextSecondary = Color(0xFF5F6368)
    val TextTertiary  = Color(0xFF80868B)

    // Google logo colors (the pin system)
    val Blue        = Color(0xFF4285F4)
    val BluePressed = Color(0xFF1A73E8)
    val BlueDark    = Color(0xFF174EA6) // route casing
    val Red         = Color(0xFFEA4335) // default pin
    val Yellow      = Color(0xFFFBBC04) // rating star / alerts
    val Green       = Color(0xFF34A853) // saved pin
    val Orange      = Color(0xFFFB8C00) // category pin / Pegman

    // Blue ramp
    val BlueHalo     = Color(0x2E4285F4) // rgba(66,133,244,0.18) accuracy fill
    val BlueHaloEdge = Color(0x664285F4) // rgba(66,133,244,0.40) accuracy outline

    // Map tiles (light)
    val RoadWhite     = Color(0xFFFFFFFF)
    val HighwayYellow = Color(0xFFFDF6E3)
    val WaterBlue     = Color(0xFFAADAFF)
    val ParkGreen     = Color(0xFFC8E6C9)
    val BuildingFill  = Color(0xFFF0F0F0)
    val LandNeutral   = Color(0xFFF5F5F5)

    // Traffic overlay
    val TrafficRed    = Color(0xFFD50000)
    val TrafficOrange = Color(0xFFFB8C00)
    val TrafficGreen  = Color(0xFF0F9D58)

    // Dark Mode (Google's warm near-black, NOT pure black)
    val DarkCanvas   = Color(0xFF202124)
    val DarkSurface1 = Color(0xFF2D2E31)
    val DarkSurface2 = Color(0xFF3C4043)
    val DarkDivider  = Color(0xFF3C4043)
    val DarkText     = Color(0xFFE8EAED)
    val DarkTextSec  = Color(0xFF9AA0A6)
    // Dark map tiles
    val DarkMapLand  = Color(0xFF242F3E)
    val DarkMapRoad  = Color(0xFF38414E)
    val DarkMapWater = Color(0xFF17263C)
    val DarkMapPark  = Color(0xFF023E58)
}
```

Wire it into a Material 3 scheme. Dark mode is `#202124`; the map itself uses a JSON map style, not the color scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.platform.LocalContext
import android.os.Build

private val GMapsLight = lightColorScheme(
    primary        = GMapsColors.Blue,
    onPrimary      = Color.White,
    background      = GMapsColors.Canvas,
    onBackground    = GMapsColors.TextPrimary,
    surface         = GMapsColors.Canvas,
    onSurface       = GMapsColors.TextPrimary,
    surfaceVariant  = GMapsColors.SurfaceMuted,
    secondaryContainer = Color(0xFFE8F0FE), // active chip / selected pill
    outline         = GMapsColors.Divider,
    error           = GMapsColors.Red,
)

private val GMapsDark = darkColorScheme(
    primary        = GMapsColors.Blue,
    onPrimary      = Color.White,
    background      = GMapsColors.DarkCanvas,
    onBackground    = GMapsColors.DarkText,
    surface         = GMapsColors.DarkSurface1,
    onSurface       = GMapsColors.DarkText,
    surfaceVariant  = GMapsColors.DarkSurface2,
    secondaryContainer = GMapsColors.Blue.copy(alpha = 0.24f),
    outline         = GMapsColors.DarkDivider,
    error           = GMapsColors.Red,
)

@Composable
fun GMapsTheme(
    dark: Boolean = isSystemInDarkTheme(),
    dynamic: Boolean = true,  // Material-native Google app — Material You ON for chrome
    content: @Composable () -> Unit,
) {
    val ctx = LocalContext.current
    val scheme = when {
        dynamic && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S ->
            if (dark) dynamicDarkColorScheme(ctx) else dynamicLightColorScheme(ctx)
        dark -> GMapsDark
        else -> GMapsLight
    }
    MaterialTheme(colorScheme = scheme, typography = GMapsTypography, content = content)
}
```

## 2. Typography

**Google Sans** for headings/CTAs/turn cards; **Roboto** (Android's system default — what iOS deliberately falls back to SF Pro for) for body, addresses, and all data. Drop Google Sans TTFs in `res/font/`. Distances, ETAs, ratings, and speeds use tabular figures (`tnum`) so list columns align.

```kotlin
// ui/theme/GMapsType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val GoogleSans = FontFamily(
    Font(R.font.google_sans_regular, FontWeight.Normal),  // 400
    Font(R.font.google_sans_medium,  FontWeight.Medium),  // 500
    Font(R.font.google_sans_bold,    FontWeight.Bold),    // 700 — turn cards only
)
val Roboto = FontFamily(
    Font(R.font.roboto_regular, FontWeight.Normal),
    Font(R.font.roboto_medium,  FontWeight.Medium),
)
const val TNUM = "tnum" // tabular figures — every distance / ETA / rating / speed

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, line-height = size × ratio)
object GMapsText {
    val NavTurn      = TextStyle(GoogleSans, fontWeight = FontWeight.Bold,   fontSize = 36.sp, lineHeight = 40.sp, letterSpacing = (-0.4).sp)
    val ScreenTitle  = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.3).sp)
    val PlaceTitle   = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val SectionHead  = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val RowTitle     = TextStyle(Roboto,     fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 21.sp)
    val Body         = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 20.sp)
    val Address      = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 19.sp, fontFeatureSettings = TNUM)
    val Meta         = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp, fontFeatureSettings = TNUM)
    val Rating       = TextStyle(Roboto,     fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 17.sp, fontFeatureSettings = TNUM)
    val Chip         = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 14.sp)
    val Button       = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 16.sp)
    val ButtonSmall  = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 14.sp)
    val Tab          = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
    val Eta          = TextStyle(Roboto,     fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 16.sp, fontFeatureSettings = TNUM)
    val DistancePill = TextStyle(Roboto,     fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 13.sp, fontFeatureSettings = TNUM)
}

// Map onto Material 3 slots so stock components inherit the brand
val GMapsTypography = Typography(
    headlineLarge = GMapsText.ScreenTitle,
    headlineSmall = GMapsText.PlaceTitle,
    titleMedium   = GMapsText.SectionHead,
    bodyMedium    = GMapsText.Body,
    labelLarge    = GMapsText.Button,
    labelSmall    = GMapsText.Tab,
)
```

## 3. Signature Components

### Floating Top Search Bar

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Mic
import androidx.compose.material.icons.filled.Person
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

@Composable
fun GMSearchBar(onTap: () -> Unit, onMic: () -> Unit, modifier: Modifier = Modifier) {
    Surface(
        modifier = modifier.fillMaxWidth().padding(horizontal = 16.dp).height(48.dp),
        shape = CircleShape,                         // full pill
        color = MaterialTheme.colorScheme.surface,
        shadowElevation = 8.dp,                      // rgba(0,0,0,0.15) 0 2px 8px
        onClick = onTap,
    ) {
        Row(
            Modifier.fillMaxSize().padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(Modifier.size(28.dp).clip(CircleShape).background(GMapsColors.SurfaceMuted), contentAlignment = Alignment.Center) {
                Icon(Icons.Filled.Person, "Profile", tint = Color.White, modifier = Modifier.size(16.dp))
            }
            Text("Search here", style = GMapsText.Button, color = GMapsColors.TextSecondary, modifier = Modifier.weight(1f))
            IconButton(onClick = onMic, modifier = Modifier.size(24.dp)) {
                Icon(Icons.Filled.Mic, "Voice search", tint = GMapsColors.TextSecondary)
            }
        }
    }
}
```

### Directions FAB

```kotlin
import androidx.compose.material.icons.filled.Directions
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun GMDirectionsFab(onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    FloatingActionButton(
        onClick = {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .impact(.medium)
            onClick()
        },
        containerColor = MaterialTheme.colorScheme.primary, // Google Blue
        contentColor = Color.White,
        shape = CircleShape,
        elevation = FloatingActionButtonDefaults.elevation(defaultElevation = 6.dp, pressedElevation = 12.dp),
        modifier = modifier.size(56.dp),
    ) {
        Icon(Icons.Filled.Directions, contentDescription = "Directions", modifier = Modifier.size(24.dp))
    }
}
```

### Your Location Dot (accuracy halo + heading cone)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.Canvas
import androidx.compose.ui.draw.rotate
import androidx.compose.ui.draw.scale
import androidx.compose.ui.geometry.Offset

@Composable
fun GMLocationDot(headingDegrees: Float? = null, accuracyDp: Dp = 140.dp) {
    val pulse by rememberInfiniteTransition(label = "loc").animateFloat(
        initialValue = 1f, targetValue = 1.15f,
        animationSpec = infiniteRepeatable(tween(1800, easing = EaseInOut), RepeatMode.Reverse),
        label = "pulse",
    )
    Box(contentAlignment = Alignment.Center) {
        // Accuracy circle — the trust signal
        Box(
            Modifier
                .size(accuracyDp)
                .clip(CircleShape)
                .background(GMapsColors.BlueHalo)
                .border(1.dp, GMapsColors.BlueHaloEdge, CircleShape),
        )
        // Heading cone (optional)
        if (headingDegrees != null) {
            Canvas(Modifier.size(40.dp).offset(y = (-48).dp).rotate(headingDegrees)) {
                drawPath(
                    androidx.compose.ui.graphics.Path().apply {
                        moveTo(size.width / 2f, 0f); lineTo(size.width, size.height); lineTo(0f, size.height); close()
                    },
                    brush = androidx.compose.ui.graphics.Brush.verticalGradient(
                        listOf(GMapsColors.Blue.copy(alpha = 0.55f), GMapsColors.Blue.copy(alpha = 0f)),
                    ),
                )
            }
        }
        // Inner blue dot + 3dp white ring
        Box(
            Modifier.size(18.dp).scale(pulse).clip(CircleShape).background(Color.White),
            contentAlignment = Alignment.Center,
        ) {
            Box(Modifier.size(12.dp).clip(CircleShape).background(GMapsColors.Blue))
        }
    }
}
```

### Teardrop Map Pin

```kotlin
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.drawscope.DrawScope

enum class PinKind { Default, Saved, HomeWork, Category }

fun PinKind.fill() = when (this) {
    PinKind.Default  -> GMapsColors.Red
    PinKind.Saved    -> GMapsColors.Green
    PinKind.HomeWork -> GMapsColors.Blue
    PinKind.Category -> GMapsColors.Orange
}

@Composable
fun GMMapPin(kind: PinKind, glyph: androidx.compose.ui.graphics.vector.ImageVector? = null) {
    Box(contentAlignment = Alignment.TopCenter) {
        Canvas(Modifier.size(32.dp, 40.dp)) {
            // ground-hugging ellipse shadow — makes the pin feel dropped into the world
            drawOval(
                Color.Black.copy(alpha = 0.25f),
                topLeft = Offset(size.width * 0.15f, size.height - 6.dp.toPx()),
                size = androidx.compose.ui.geometry.Size(size.width * 0.7f, 6.dp.toPx()),
            )
            drawTeardrop(kind.fill())
        }
        if (kind == PinKind.Default) {
            Box(Modifier.padding(top = 10.dp).size(10.dp).clip(CircleShape).background(Color.White))
        } else if (glyph != null) {
            Icon(glyph, null, tint = Color.White, modifier = Modifier.padding(top = 9.dp).size(14.dp))
        }
    }
}

private fun DrawScope.drawTeardrop(color: Color) {
    val w = size.width
    val r = w / 2f
    val path = Path().apply {
        addOval(androidx.compose.ui.geometry.Rect(0f, 0f, w, w))
        moveTo(2f, r)
        quadraticBezierTo(0f, w, w / 2f, size.height)
        quadraticBezierTo(w, w, w - 2f, r)
        close()
    }
    drawPath(path, color)
}
```

### Place Sheet Card

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.filled.Star
import androidx.compose.material.icons.outlined.StarBorder
import androidx.compose.ui.text.style.TextOverflow
import coil.compose.AsyncImage

@Composable
fun GMPlaceCard(
    title: String,
    rating: Double,
    reviewCount: Int,
    category: String,
    distance: String,
    isOpen: Boolean,
    photoUrl: String?,
) {
    Column(
        Modifier.fillMaxWidth().background(MaterialTheme.colorScheme.surface).padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
            AsyncImage(
                model = photoUrl,
                contentDescription = null,
                modifier = Modifier.size(72.dp).clip(RoundedCornerShape(12.dp)).background(GMapsColors.SurfaceMuted),
            )
            Column(verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(title, style = GMapsText.PlaceTitle, color = GMapsColors.TextPrimary, maxLines = 2, overflow = TextOverflow.Ellipsis)
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    Text("%.1f".format(rating), style = GMapsText.Rating, color = GMapsColors.TextPrimary)
                    repeat(5) { i ->
                        Icon(
                            if (i < rating.toInt()) Icons.Filled.Star else Icons.Outlined.StarBorder,
                            null, tint = GMapsColors.Yellow, modifier = Modifier.size(12.dp),
                        )
                    }
                    Text("($reviewCount)", style = GMapsText.Body, color = GMapsColors.TextSecondary)
                }
                Text("$category · $distance", style = GMapsText.Meta, color = GMapsColors.TextSecondary)
                if (isOpen) Text("Open now", style = GMapsText.Meta.copy(fontWeight = FontWeight.Medium), color = GMapsColors.Green)
            }
        }
        Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
            GMPill(Icons.Filled.Directions, "Directions", filled = true)
            GMPill(Icons.Outlined.BookmarkBorder, "Save", filled = false)
            GMPill(Icons.Filled.Share, "Share", filled = false)
            GMPill(Icons.Filled.Call, "Call", filled = false)
        }
    }
}

@Composable
fun GMPill(icon: androidx.compose.ui.graphics.vector.ImageVector, title: String, filled: Boolean) {
    Row(
        Modifier
            .height(36.dp)
            .clip(CircleShape)
            .background(if (filled) GMapsColors.Blue else Color.Transparent)
            .then(if (filled) Modifier else Modifier.border(1.dp, GMapsColors.Divider, CircleShape))
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(icon, null, tint = if (filled) Color.White else GMapsColors.Blue, modifier = Modifier.size(16.dp))
        Text(title, style = GMapsText.ButtonSmall, color = if (filled) Color.White else GMapsColors.Blue)
    }
}
```

### Navigation Turn Card

```kotlin
import androidx.compose.material.icons.automirrored.filled.DirectionsWalk
import androidx.compose.material.icons.filled.TurnRight
import androidx.compose.material.icons.filled.TurnLeft

@Composable
fun GMTurnCard(instruction: String, distance: String, nextInstruction: String?) {
    Surface(
        modifier = Modifier.fillMaxWidth().padding(horizontal = 16.dp),
        shape = RoundedCornerShape(16.dp),
        color = GMapsColors.Blue,        // the only display moment — blue card, bold white type
        shadowElevation = 16.dp,
    ) {
        Column(Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(8.dp)) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(16.dp)) {
                Icon(Icons.Filled.TurnRight, null, tint = Color.White, modifier = Modifier.size(44.dp))
                Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
                    Text(instruction, style = GMapsText.PlaceTitle.copy(fontFamily = GoogleSans, fontWeight = FontWeight.Bold, fontSize = 22.sp), color = Color.White, maxLines = 2)
                    Text("in $distance", style = GMapsText.Body, color = Color.White.copy(alpha = 0.7f))
                }
            }
            if (nextInstruction != null) {
                HorizontalDivider(color = Color.White.copy(alpha = 0.2f))
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(10.dp)) {
                    Icon(Icons.Filled.TurnLeft, null, tint = Color.White.copy(alpha = 0.8f), modifier = Modifier.size(22.dp))
                    Text("Then $nextInstruction", style = GMapsText.Body, color = Color.White.copy(alpha = 0.8f))
                }
            }
        }
    }
}
```

## 4. Google-Maps-Specific Feature: Map-as-Hero + Floating Bottom-Sheet Drawer

The map owns 60–85% of the viewport on every primary screen — the UI floats on top, never stacks below. Use `maps-compose`'s `GoogleMap` as the full-bleed base layer and a Material 3 `BottomSheetScaffold` whose `sheetPeekHeight` realizes the three Maps detents (collapsed 140.dp / medium 380.dp / expanded near-full). The route polyline is a `Polyline` composable with a darker casing drawn beneath it.

```kotlin
import androidx.compose.material3.BottomSheetScaffold
import androidx.compose.material3.rememberBottomSheetScaffoldState
import androidx.compose.material3.SheetValue
import com.google.maps.android.compose.*
import com.google.android.gms.maps.model.LatLng

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun MapScreen(routePoints: List<LatLng>) {
    val cameraPositionState = rememberCameraPositionState()
    val scaffoldState = rememberBottomSheetScaffoldState()

    BottomSheetScaffold(
        scaffoldState = scaffoldState,
        sheetPeekHeight = 140.dp,                       // collapsed detent
        sheetContainerColor = MaterialTheme.colorScheme.surface,
        sheetShape = RoundedCornerShape(topStart = 16.dp, topEnd = 16.dp), // 16dp top corners
        sheetDragHandle = {                             // the 32x4dp grabber
            Box(
                Modifier.padding(top = 8.dp).size(32.dp, 4.dp)
                    .clip(CircleShape).background(GMapsColors.Divider),
            )
        },
        sheetContent = {
            // search results list / GMPlaceCard at expanded detent
            GMPlaceCard("Blue Bottle Coffee", 4.5, 2345, "Coffee shop · $$", "1.2 mi", true, null)
        },
    ) {
        Box(Modifier.fillMaxSize()) {
            // 1) The map IS the canvas — full-bleed, edge to edge
            GoogleMap(
                modifier = Modifier.fillMaxSize(),
                cameraPositionState = cameraPositionState,
                properties = MapProperties(mapStyleOptions = darkOrLightStyle()),
                uiSettings = MapUiSettings(zoomControlsEnabled = false, myLocationButtonEnabled = false),
            ) {
                if (routePoints.isNotEmpty()) {
                    // Casing first (wider, darker), then the 5dp blue fill on top
                    Polyline(points = routePoints, color = GMapsColors.BlueDark, width = 13f)
                    Polyline(points = routePoints, color = GMapsColors.Blue, width = 10f)
                }
            }
            // 2) Floating chrome — pills + FAB, 16dp from edges, never stacked below the map
            GMSearchBar(onTap = {}, onMic = {}, modifier = Modifier.align(Alignment.TopCenter).statusBarsPadding().padding(top = 8.dp))
            GMDirectionsFab(
                onClick = {},
                modifier = Modifier.align(Alignment.BottomEnd).padding(16.dp).offset(y = (-148).dp), // clears the collapsed sheet
            )
        }
    }
}
```

Map controls (recenter, layers, compass) stack on the trailing edge as 40.dp `Surface(shape = CircleShape, shadowElevation = 6.dp)` circles, offset above the current sheet detent — never more than 3, to keep the map breathable. Provide a dark-tile `MapStyleOptions` JSON (`#242F3E` land / `#17263C` water / `#023E58` parks) for dark mode and cross-fade map styles over ~1.2s on theme toggle.

## 5. Navigation

Primary navigation is the Material 3 `NavigationBar` (Explore / Go / Saved / Contribute / Updates). Android has no live blur, so the bar is a 92%-opaque canvas `Surface` with a 0.5dp top divider. Active tint is Google Blue with the filled icon variant.

```kotlin
@Composable
fun GMBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    Column {
        HorizontalDivider(color = GMapsColors.Divider, thickness = 0.5.dp)
        NavigationBar(
            containerColor = MaterialTheme.colorScheme.surface.copy(alpha = 0.92f),
            tonalElevation = 0.dp,
        ) {
            val items = listOf(
                "Explore"    to Icons.Filled.Explore,
                "Go"         to Icons.Filled.NearMe,
                "Saved"      to Icons.Filled.Bookmark,
                "Contribute" to Icons.Filled.AddCircle,
                "Updates"    to Icons.AutoMirrored.Filled.Article,
            )
            items.forEachIndexed { i, (label, icon) ->
                NavigationBarItem(
                    selected = selected == i,
                    onClick = { onSelect(i) },
                    icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                    label = { Text(label, style = GMapsText.Tab) },
                    colors = NavigationBarItemDefaults.colors(
                        selectedIconColor   = GMapsColors.Blue,
                        selectedTextColor   = GMapsColors.Blue,
                        unselectedIconColor = GMapsColors.TextSecondary,
                        unselectedTextColor = GMapsColors.TextSecondary,
                        indicatorColor      = Color.Transparent, // Maps has no Material pill behind tabs
                    ),
                )
            }
        }
    }
}
```

The category chip row (Restaurants / Coffee / Gas / Hotels …) is a `LazyRow` of `FilterChip`s directly under the search bar with an 8.dp gap; active chip → `#E8F0FE` container, `#4285F4` border, `#1967D2` text.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Pin drop | `Animatable` `spring(dampingRatio = 0.55f, stiffness = 400f)` 1.1 → 1.0; shadow scales with pin; `HapticFeedbackType.LongPress` (≈ `.impact(.medium)`) |
| Search-bar expand | `AnimatedVisibility` morph to full-screen, 280ms ease-out, list `fadeIn` |
| Sheet detent change | `BottomSheetScaffold` anchored drag — `spring(dampingRatio = 0.85f)` snap between detents |
| FAB press | `FloatingActionButton` `pressedElevation` lift (Material does not scale) + `LongPress` haptic |
| Route draw-on | animate `Polyline` `Animatable` 0 → 1 fraction over 600ms ease-out, then `cameraPositionState.animate()` to follow |
| Location dot pulse | `rememberInfiniteTransition` scale 1.0 → 1.15 over 1.8s `EaseInOut` reverse |
| Turn-card swap | `AnimatedContent` `slideInVertically { it } togetherWith fadeOut()`, 300ms; light haptic on each turn |
| Camera recenter | `cameraPositionState.animate(update, durationMs = 600)` — no haptic (soft action) |

```kotlin
// Pin drop with overshoot + haptic
@Composable
fun DroppingPin(kind: PinKind) {
    val scale = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(Unit) {
        haptics.performHapticFeedback(HapticFeedbackType.LongPress)
        scale.animateTo(1f, spring(dampingRatio = 0.55f, stiffness = 400f))
    }
    Box(Modifier.scale(scale.value)) { GMMapPin(kind) }
}
```

Haptics: prefer `LocalHapticFeedback`. `HapticFeedbackType.LongPress` ≈ iOS `.impact(.medium)` (pin drop / Start); `TextHandleMove` ≈ `.selection` (tab / chip). For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(14, …)`.

## 7. Icons

Google Maps uses Material Symbols natively; `androidx.compose.material:material-icons-extended` is the first-party match. The Maps pin glyph, Pegman, and turn-arrow set ship as vector drawables (`ImageVector.vectorResource(R.drawable.…)`).

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Directions FAB | `arrow.triangle.turn.up.right.diamond.fill` | `Icons.Filled.Directions` |
| Search mic | `mic.fill` | `Icons.Filled.Mic` |
| Location dot | custom circle + ring | (drawn in `Canvas`) |
| Saved bookmark | `bookmark` / `.fill` | `Icons.Outlined.BookmarkBorder` / `Icons.Filled.Bookmark` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Call | `phone.fill` | `Icons.Filled.Call` |
| Recenter | `location.fill` | `Icons.Filled.MyLocation` |
| Layers | `square.3.layers.3d` | `Icons.Filled.Layers` |
| Compass | `safari` | `Icons.Filled.Explore` |
| Turn right / left | `arrow.turn.up.right` / `.left` | `Icons.Filled.TurnRight` / `Icons.Filled.TurnLeft` |
| Straight | `arrow.up` | `Icons.Filled.Straight` |
| Rating star | `star.fill` | `Icons.Filled.Star` |
| Explore (tab) | `safari.fill` | `Icons.Filled.Explore` |
| Go (tab) | `location.north.circle.fill` | `Icons.Filled.NearMe` |
| Saved (tab) | `bookmark.fill` | `Icons.Filled.Bookmark` |
| Contribute (tab) | `plus.circle.fill` | `Icons.Filled.AddCircle` |
| Updates (tab) | `newspaper.fill` | `Icons.AutoMirrored.Filled.Article` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `maps-compose` and `BottomSheetScaffold` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. Add the Maps SDK API key in the manifest.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity` — the map is full-bleed under the status bar and gesture nav. Float the search bar with `statusBarsPadding()` + 8.dp; the bottom sheet honors the navigation-bar inset and pads content 24.dp above it. The Directions FAB stays 16.dp from edges and offsets above the current sheet detent.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on place titles, addresses, reviews, row titles. Pin layout-sensitive text (distance/ETA labels, the speed chip, the 11sp tab labels) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`; clamp the nav turn instruction at ~48sp max so it stays legible at speed without breaking the card.
- **Tabular figures**: keep `fontFeatureSettings = "tnum"` on every distance / ETA / rating / speed style — list column alignment ("1.2 mi" over "14.7 mi") breaks without it.
- **TalkBack**: announce the location dot as "Your location, accuracy 30 feet" via `Modifier.semantics`; give each pin a `contentDescription` of its category + place name; collapse the place card into one node with `semantics(mergeDescendants = true)`. Map controls and the FAB are separate labeled buttons.
- **Touch targets**: Material guidance is 48.dp minimum. The FAB (56.dp) and search bar (48.dp) are clear; give 32.dp visual pins a ≥40–48.dp hit area, and ensure the 16.dp action-pill glyphs sit inside ≥36–48.dp rows.
- **Contrast**: `#202124` on `#FFFFFF` exceeds AAA. `#5F6368` secondary meets AA at 14sp+ on white — validate `#80868B` tertiary at 13sp and the white-70% turn-card subtext on `#4285F4`; boost toward solid white / `#3C4043` when the system "increase contrast" setting (`isHighTextContrastEnabled`) is on.
- **Dynamic color**: **RECOMMENDED — embrace `dynamicColorScheme()` for chrome.** Google Maps is a Material-native Google app; on Android 12+ the floating chrome (sheet surface, search bar, tab container) should adopt Material You from the user's wallpaper. Keep the *semantic* anchors strictly fixed: the four pin colors (Red default / Green saved / Blue home-work / Orange category), the blue route polyline + `#174EA6` casing, the rating yellow `#FBBC04`, the red/orange/green traffic overlay, and the map tile palette — these encode meaning on the map and must never drift with the wallpaper. The Directions FAB / location dot may follow the dynamic `primary` since blue is also the generic accent, but pin/route/traffic semantics stay constant.
