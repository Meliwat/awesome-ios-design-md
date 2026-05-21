# Waze (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Waze's playful, cartoon-forward visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (the cartoon hazard speech bubble, the cyan arrow puck, the next-turn card, the report FAB, the speed-limit tile, the "Go" button), `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the Waze Purple + Cyan electric duo, the **chunky cartoon speech-bubble hazard reports with triangular tails**, the cyan arrow puck, the loud saturated cartography, the tinted purple FAB shadow, Boing's roundness) while making everything idiomatic Android — Google Maps Compose instead of MapKit, SF Pro Rounded's analog (`FontFamily` fallback) for Boing, custom `Shape`s for the tail and arrow, `LocalHapticFeedback` instead of `.sensoryFeedback`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and `com.google.maps.android:maps-compose` for the loud cartography. No color extraction, so `androidx.palette` is not required.

## 1. Color Tokens

```kotlin
// ui/theme/WazeColors.kt
import androidx.compose.ui.graphics.Color

object WazeColors {
    // Brand
    val Purple      = Color(0xFF7E55BE) // logo, polyline, FAB, "Go", primary CTAs
    val PurpleDeep  = Color(0xFF5B3C9A)
    val PurpleTint  = Color(0xFFE8DEF5)
    val Cyan        = Color(0xFF33CCFF) // location puck, info glyphs, 2nd logo color
    val CyanDeep    = Color(0xFF0099E5)

    // Hazard colors (the cartoon system)
    val PoliceRed   = Color(0xFFEF6A65)
    val TrafficOrng = Color(0xFFF69833)
    val ClosureYel  = Color(0xFFF9C42E)
    val ClearedGrn  = Color(0xFF75C73E)
    val HazardBrown = Color(0xFF8B6F47)
    val CameraGray  = Color(0xFF6B6B6B)

    // Map cartography (light) — LOUD by design
    val MapCream     = Color(0xFFFFFCF2)
    val MapWater     = Color(0xFF9BDEEF)
    val MapPark      = Color(0xFFC5E89B)
    val MapRoadMajor = Color(0xFFFFFFFF)
    val MapRoadMinor = Color(0xFFF5F0E5)
    val MapHighway   = Color(0xFFFFD970)
    val MapBuilding  = Color(0xFFE8E2D1)
    val MapLabel     = Color(0xFF3D3D3D)

    // UI chrome
    val CardCanvas   = Color(0xFFFFFFFF)
    val SurfaceGray  = Color(0xFFF5F5F7)
    val SurfaceGray2 = Color(0xFFEAEAEC)
    val Divider      = Color(0xFFD6D6D9)

    // Text
    val Ink       = Color(0xFF1A1A1A) // warm near-black
    val Secondary = Color(0xFF6B6B6B)
    val Tertiary  = Color(0xFFA0A0A0)

    // Semantic
    val Success = Color(0xFF34C759)
    val Warning = Color(0xFFF9C42E)
    val Error   = Color(0xFFEF6A65)

    // Dark (night driving)
    val DarkMapLand  = Color(0xFF1E2026)
    val DarkMapWater = Color(0xFF0F3D5E)
    val DarkMapPark  = Color(0xFF1F3A1F)
    val DarkCardSurf = Color(0xFF262932)
    val DarkSurface2 = Color(0xFF3A3D47)
    val DarkDivider  = Color(0xFF4A4D58)
    val PurpleDark   = Color(0xFF9F76DA) // brightened for dark legibility
    val CyanDark     = Color(0xFF5DD9FF)
}
```

Waze is a map-first app: a loud light cartography by day, near-black "night driving" by night — but the **Waze Purple + Cyan stay at brand saturation in both**. Wire both Material 3 schemes accordingly.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val WazeLight = lightColorScheme(
    primary        = WazeColors.Purple,
    onPrimary      = Color.White,
    secondary      = WazeColors.Cyan,
    background      = WazeColors.CardCanvas,
    onBackground   = WazeColors.Ink,
    surface        = WazeColors.CardCanvas,
    onSurface      = WazeColors.Ink,
    surfaceVariant = WazeColors.SurfaceGray,
    outline        = WazeColors.Divider,
    error          = WazeColors.Error,
)

private val WazeDark = darkColorScheme(
    primary        = WazeColors.PurpleDark,   // brightened, still brand
    onPrimary      = Color.White,
    secondary      = WazeColors.CyanDark,
    background      = WazeColors.DarkMapLand,
    onBackground   = Color.White,
    surface        = WazeColors.DarkCardSurf,
    onSurface      = Color.White,
    surfaceVariant = WazeColors.DarkSurface2,
    outline        = WazeColors.DarkDivider,
    error          = WazeColors.Error,
)

@Composable
fun WazeTheme(darkTheme: Boolean = isSystemInDarkTheme(), content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (darkTheme) WazeDark else WazeLight,
        typography = WazeTypography,
        content = content,
    )
```

## 2. Typography

Waze ships **Boing**, a proprietary rounded sans — roundness IS the brand. The correct Android fallback is the rounded system family. Compose has no built-in `.rounded` design like iOS, so bundle Boing TTFs in `res/font/`; if unavailable, ship a rounded open-source face (Quicksand / Nunito) as the fallback `FontFamily`. **Boing Mono on every numeric** (distance, time, speed, ETA) — variable widths break the at-a-glance driving read.

```kotlin
// ui/theme/WazeType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

// Boing, with a rounded open-source fallback (Quicksand/Nunito) bundled in res/font/
val Boing = FontFamily(
    Font(R.font.boing_regular, FontWeight.Normal), // 400
    Font(R.font.boing_bold,    FontWeight.Bold),   // 700
    Font(R.font.boing_black,   FontWeight.Black),  // 900 — hero moments only
)
val BoingMono = FontFamily(
    Font(R.font.boing_mono_medium, FontWeight.Medium),
    Font(R.font.boing_mono_bold,   FontWeight.Bold),
    Font(R.font.boing_mono_black,  FontWeight.Black),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1). Numerics use BoingMono + tabular figures.
object WazeText {
    val HeroStreet     = TextStyle(Boing,     fontWeight = FontWeight.Black,  fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.4).sp)
    val NextTurnDist   = TextStyle(BoingMono, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 31.sp, fontFeatureSettings = "tnum")
    val StepTitle      = TextStyle(Boing,     fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val StepSubtitle   = TextStyle(Boing,     fontWeight = FontWeight.Normal, fontSize = 17.sp, lineHeight = 22.sp)
    val ETATime        = TextStyle(BoingMono, fontWeight = FontWeight.Bold,   fontSize = 24.sp, lineHeight = 26.sp, fontFeatureSettings = "tnum")
    val ETADistance    = TextStyle(BoingMono, fontWeight = FontWeight.Medium, fontSize = 17.sp, lineHeight = 20.sp, fontFeatureSettings = "tnum")
    val PlaceTitle     = TextStyle(Boing,     fontWeight = FontWeight.Bold,   fontSize = 26.sp, lineHeight = 30.sp, letterSpacing = (-0.3).sp)
    val PlaceSubtitle  = TextStyle(Boing,     fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 20.sp)
    val SearchHint     = TextStyle(Boing,     fontWeight = FontWeight.Normal, fontSize = 17.sp, lineHeight = 22.sp)
    val Section        = TextStyle(Boing,     fontWeight = FontWeight.Bold,   fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.6.sp) // UPPERCASE at call site
    val ListTitle      = TextStyle(Boing,     fontWeight = FontWeight.Medium, fontSize = 17.sp, lineHeight = 22.sp)
    val ListSubtitle   = TextStyle(Boing,     fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 16.sp)
    val SpeedLimitNum  = TextStyle(BoingMono, fontWeight = FontWeight.Black,  fontSize = 22.sp, lineHeight = 22.sp, fontFeatureSettings = "tnum")
    val CurrentSpeed   = TextStyle(BoingMono, fontWeight = FontWeight.Bold,   fontSize = 32.sp, lineHeight = 32.sp, fontFeatureSettings = "tnum")
    val SpeedLimitLbl  = TextStyle(Boing,     fontWeight = FontWeight.Bold,   fontSize = 9.sp,  lineHeight = 11.sp)
    val MphLabel       = TextStyle(Boing,     fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 13.sp)
    val HazardTitle    = TextStyle(Boing,     fontWeight = FontWeight.Bold,   fontSize = 14.sp, lineHeight = 15.sp)
    val HazardTime     = TextStyle(Boing,     fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 12.sp)
    val Button         = TextStyle(Boing,     fontWeight = FontWeight.Bold,   fontSize = 17.sp, lineHeight = 17.sp)
    val Caption        = TextStyle(Boing,     fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 16.sp)
    val Tab            = TextStyle(Boing,     fontWeight = FontWeight.Medium, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val WazeTypography = Typography(
    headlineLarge = WazeText.HeroStreet,
    headlineSmall = WazeText.PlaceTitle,
    titleMedium   = WazeText.StepTitle,
    bodyMedium    = WazeText.PlaceSubtitle,
    labelLarge    = WazeText.Button,
    labelSmall    = WazeText.Tab,
)
```

## 3. Signature Components

### Hazard Speech Bubble (The Signature)

The single most recognizable component: a chunky 14.dp-corner rounded rectangle in a full-saturation hazard color with a **triangular tail** pointing down to the location. Implemented with a custom `Shape` for the tail (no SF Symbol / Compose primitive matches it).

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.drawBehind
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.unit.dp

enum class HazardType { POLICE, TRAFFIC, CLOSURE, CLEARED, POTHOLE, CAMERA }

val HazardType.color: Color
    get() = when (this) {
        HazardType.POLICE  -> WazeColors.PoliceRed
        HazardType.TRAFFIC -> WazeColors.TrafficOrng
        HazardType.CLOSURE -> WazeColors.ClosureYel
        HazardType.CLEARED -> WazeColors.ClearedGrn
        HazardType.POTHOLE -> WazeColors.HazardBrown
        HazardType.CAMERA  -> WazeColors.CameraGray
    }

val HazardType.icon: ImageVector
    get() = when (this) {
        HazardType.POLICE  -> Icons.Filled.Shield
        HazardType.TRAFFIC -> Icons.Filled.Warning
        HazardType.CLOSURE -> Icons.Filled.Block
        HazardType.CLEARED -> Icons.Filled.CheckCircle
        HazardType.POTHOLE -> Icons.Filled.ReportProblem
        HazardType.CAMERA  -> Icons.Filled.CameraAlt
    }

val HazardType.title: String
    get() = name.lowercase().replaceFirstChar { it.uppercase() }

@Composable
fun HazardSpeechBubble(
    type: HazardType,
    timeAgo: String?,
    modifier: Modifier = Modifier,
) {
    val appear = remember { Animatable(0f) }
    LaunchedEffect(Unit) {
        appear.animateTo(1f, spring(dampingRatio = 0.55f, stiffness = 400f)) // pop: 0 → 1.15 → 1.0
    }

    Column(
        modifier = modifier
            .graphicsLayer { scaleX = appear.value; scaleY = appear.value }
            .shadow(12.dp, RoundedCornerShape(14.dp), spotColor = Color.Black.copy(alpha = 0.2f)),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Row(
            Modifier
                .clip(RoundedCornerShape(14.dp))
                .background(type.color)
                .widthIn(min = 88.dp)
                .padding(horizontal = 12.dp, vertical = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Icon(type.icon, contentDescription = null, tint = Color.White, modifier = Modifier.size(24.dp))
            Column {
                Text(type.title, style = WazeText.HazardTitle, color = Color.White)
                if (timeAgo != null) Text(timeAgo, style = WazeText.HazardTime, color = Color.White.copy(alpha = 0.8f))
            }
        }
        // Downward triangular tail — custom drawn, same color as the bubble
        Box(
            Modifier
                .size(12.dp, 8.dp)
                .drawBehind {
                    val p = Path().apply {
                        moveTo(0f, 0f)
                        lineTo(size.width, 0f)
                        lineTo(size.width / 2f, size.height)
                        close()
                    }
                    drawPath(p, type.color)
                }
        )
    }
}
```

### Report FAB (Tinted Purple Shadow — Signature)

Bottom-right Waze Purple circle with a **tinted purple shadow** (`spotColor` purple, not black). Opens the report wheel.

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun WazeFAB(onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.94f else 1f, label = "fabScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .size(56.dp)
            .scale(scale)
            .shadow(16.dp, CircleShape, spotColor = WazeColors.Purple, ambientColor = WazeColors.Purple) // SIGNATURE tinted purple
            .clip(CircleShape)
            .background(if (pressed) WazeColors.PurpleDeep else WazeColors.Purple)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                onClick()
            }
            .semantics { contentDescription = "Report a hazard" },
        contentAlignment = Alignment.Center,
    ) {
        Icon(Icons.Filled.Campaign, contentDescription = null, tint = Color.White, modifier = Modifier.size(24.dp))
    }
}
```

> `spotColor` is honored on API 28+; on older devices draw a soft purple radial glow behind the circle so the signature reads on every device.

### Current-Location Puck (Cyan Arrow with Pulse)

Waze's puck is a **chunky cyan arrow** (NOT a dot), white-outlined, rotating with heading, with a faint 60.dp pulse ring. Both the arrow and the pulse are custom-drawn.

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke as DrawStroke

@Composable
fun WazeLocationPuck(headingDegrees: Float, modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "puck")
    val pulse by t.animateFloat(
        0f, 1f, infiniteRepeatable(tween(2000, easing = LinearEasing), RepeatMode.Restart), label = "pulse",
    )

    Box(modifier, contentAlignment = Alignment.Center) {
        // Outer cyan pulse ring — 0.15 alpha, scale 1.0 → 1.5
        Box(
            Modifier
                .size(60.dp)
                .graphicsLayer { scaleX = 1f + pulse * 0.5f; scaleY = 1f + pulse * 0.5f }
                .background(WazeColors.Cyan.copy(alpha = 0.15f * (1f - pulse)), CircleShape)
        )
        // Chunky cyan arrow, rotates with heading, 3.dp white outline
        Canvas(
            Modifier.size(32.dp, 36.dp).graphicsLayer { rotationZ = headingDegrees }
        ) {
            val w = size.width
            val h = size.height
            val arrow = Path().apply {
                moveTo(w * 0.5f, 0f)            // tip
                lineTo(w, h * 0.85f)            // bottom-right
                lineTo(w * 0.5f, h * 0.70f)     // V indent
                lineTo(0f, h * 0.85f)           // bottom-left
                close()
            }
            drawPath(arrow, WazeColors.Cyan)
            drawPath(arrow, Color.White, style = DrawStroke(width = 3.dp.toPx()))
        }
    }
}
```

### Next-Turn Card (Top of Screen During Navigation)

Full-width 100.dp solid Waze Purple card, 16.dp **bottom** corners only (extends into the safe area on top), big direction arrow + Mono distance + street name.

```kotlin
@Composable
fun NextTurnCard(
    arrowIcon: ImageVector,      // e.g. Icons.AutoMirrored.Filled.TurnRight
    arrowRotation: Float,
    distance: String,            // "0.4 mi"
    streetName: String,          // "Market Street"
    subInstruction: String?,     // "then turn left in 0.6 mi"
    modifier: Modifier = Modifier,
) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(bottomStart = 16.dp, bottomEnd = 16.dp)) // top extends into safe area
            .background(WazeColors.Purple)
            .padding(horizontal = 20.dp, vertical = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        Icon(
            arrowIcon, contentDescription = null, tint = Color.White,
            modifier = Modifier.size(64.dp).graphicsLayer { rotationZ = arrowRotation },
        )
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(distance, style = WazeText.NextTurnDist, color = Color.White)
            Text(streetName, style = WazeText.StepTitle, color = Color.White, maxLines = 2)
            if (subInstruction != null) {
                Text(subInstruction, style = WazeText.Caption.copy(fontSize = 14.sp), color = Color.White.copy(alpha = 0.8f))
            }
        }
    }
}
```

### Speed Limit + Current Speed Tile

US-style speed-limit signage as an 80×80.dp bottom-left tile; the current-speed number flips red when speeding.

```kotlin
import androidx.compose.foundation.border
import androidx.compose.material3.HorizontalDivider

@Composable
fun SpeedTile(limit: Int, current: Int, modifier: Modifier = Modifier) {
    val speeding = current > limit
    Column(
        modifier = modifier
            .width(80.dp)
            .shadow(8.dp, RoundedCornerShape(12.dp), spotColor = Color.Black.copy(alpha = 0.10f))
            .clip(RoundedCornerShape(12.dp))
            .background(WazeColors.CardCanvas)
            .padding(8.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Text("SPEED LIMIT", style = WazeText.SpeedLimitLbl, color = WazeColors.Ink)
        Box(
            Modifier.size(40.dp).clip(CircleShape).background(Color.White).border(2.dp, WazeColors.Ink, CircleShape),
            contentAlignment = Alignment.Center,
        ) { Text("$limit", style = WazeText.SpeedLimitNum, color = WazeColors.Ink) }
        HorizontalDivider(color = WazeColors.Divider, modifier = Modifier.padding(horizontal = 8.dp))
        Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
            Text("$current", style = WazeText.CurrentSpeed, color = if (speeding) WazeColors.Error else WazeColors.Ink)
            Text("mph", style = WazeText.MphLabel, color = WazeColors.Secondary)
        }
    }
}
```

### ETA Bottom Bar & "Go" Button

```kotlin
@Composable
fun WazeETABar(
    duration: String,            // "12 min"
    distance: String,            // "5.2 mi"
    arrival: String,             // "6:14 PM"
    alternativeRouteSaves: String?,  // "Save 3 min"
    onEnd: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier = modifier.fillMaxWidth().height(80.dp).background(WazeColors.CardCanvas).padding(horizontal = 20.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(duration, style = WazeText.ETATime, color = WazeColors.Ink)
            Text("$distance · $arrival", style = WazeText.ETADistance, color = WazeColors.Secondary)
        }
        if (alternativeRouteSaves != null) {
            Box(
                Modifier.clip(CircleShape).background(WazeColors.ClearedGrn).padding(horizontal = 14.dp, vertical = 8.dp)
            ) { Text(alternativeRouteSaves, style = WazeText.Button.copy(fontSize = 14.sp), color = Color.White) }
        }
        Box(
            Modifier.clip(CircleShape).background(WazeColors.CardCanvas).border(1.5.dp, WazeColors.Error, CircleShape)
                .clickable(onClick = onEnd).padding(horizontal = 20.dp, vertical = 12.dp)
        ) { Text("End", style = WazeText.Button, color = WazeColors.Error) }
    }
}

@Composable
fun WazeGoButton(onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "goScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .fillMaxWidth()
            .height(56.dp)
            .scale(scale)
            .shadow(12.dp, CircleShape, spotColor = WazeColors.Purple, ambientColor = WazeColors.Purple) // tinted purple
            .clip(CircleShape)
            .background(if (pressed) WazeColors.PurpleDeep else WazeColors.Purple)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS heavy impact (see §6 for stronger)
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Text("Go", style = WazeText.Button, color = Color.White)
    }
}
```

## 4. Loud Cartography + Report Wheel (the distinctive interaction)

Waze has no color extraction. Its defining systems are the **loud saturated cartography** and the **cartoon report wheel**. Use Google Maps Compose with a JSON style built from the bright Waze tokens; the polyline is a 6.dp purple "ant trail".

```kotlin
import com.google.maps.android.compose.GoogleMap
import com.google.maps.android.compose.MapProperties
import com.google.maps.android.compose.Polyline
import com.google.maps.android.compose.rememberCameraPositionState
import com.google.android.gms.maps.model.MapStyleOptions
import com.google.android.gms.maps.model.LatLng
import com.google.android.gms.maps.model.RoundCap

@Composable
fun WazeMap(route: List<LatLng>, modifier: Modifier = Modifier, darkTheme: Boolean = isSystemInDarkTheme()) {
    val cameraPositionState = rememberCameraPositionState()
    GoogleMap(
        modifier = modifier.fillMaxSize(),
        cameraPositionState = cameraPositionState,
        properties = MapProperties(
            // JSON style from Waze tokens: water #9BDEEF, parks #C5E89B, land #FFFCF2,
            // highways #FFD970, roads #FFFFFF — LOUD by design (dark: #0F3D5E / #1F3A1F / #1E2026)
            mapStyleOptions = MapStyleOptions(if (darkTheme) WAZE_MAP_STYLE_DARK else WAZE_MAP_STYLE_LIGHT),
        ),
    ) {
        Polyline(
            points = route,
            color = WazeColors.Purple,           // 6.dp purple — thicker than typical
            width = with(androidx.compose.ui.platform.LocalDensity.current) { 6.dp.toPx() },
            startCap = RoundCap(), endCap = RoundCap(),
        )
        // Ant-trail chevron overlay: a second dashed polyline whose pattern offset animates (see §6)
    }
}

const val WAZE_MAP_STYLE_LIGHT = """[ /* water #9BDEEF, parks #C5E89B, land #FFFCF2, highway #FFD970, roads #FFFFFF */ ]"""
const val WAZE_MAP_STYLE_DARK  = """[ /* water #0F3D5E, parks #1F3A1F, land #1E2026, roads #2F2F2F */ ]"""
```

### Report Wheel (Cartoon Dial)

A literal cartoon dial — embrace it, never tone it down. 8 wedges of hazard icons over a purple scrim.

```kotlin
import androidx.compose.foundation.layout.Box
import kotlin.math.cos
import kotlin.math.sin

@Composable
fun ReportWheel(
    onPick: (HazardType) -> Unit,
    onClose: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val types = listOf(
        HazardType.POLICE, HazardType.TRAFFIC, HazardType.CLOSURE, HazardType.POTHOLE,
        HazardType.CAMERA, HazardType.CLEARED,
    )
    val wheelScale = remember { Animatable(0f) }
    LaunchedEffect(Unit) { wheelScale.animateTo(1f, spring(dampingRatio = 0.7f, stiffness = 300f)) } // spins in

    Box(
        modifier
            .fillMaxSize()
            .background(WazeColors.Purple.copy(alpha = 0.85f)) // semi-transparent purple scrim
            .clickable(interactionSource = remember { MutableInteractionSource() }, indication = null, onClick = onClose),
        contentAlignment = Alignment.BottomCenter,
    ) {
        Box(
            Modifier
                .padding(bottom = 80.dp)
                .size(280.dp)
                .graphicsLayer { scaleX = wheelScale.value; scaleY = wheelScale.value }
                .clip(CircleShape)
                .background(WazeColors.CardCanvas),
            contentAlignment = Alignment.Center,
        ) {
            types.forEachIndexed { i, type ->
                val angle = Math.toRadians((i * (360.0 / types.size)) - 90.0)
                val r = 90.dp
                Box(
                    Modifier
                        .offset(x = (cos(angle) * r.value).dp, y = (sin(angle) * r.value).dp)
                        .size(44.dp).clip(CircleShape).background(type.color.copy(alpha = 0.20f))
                        .clickable { onPick(type) },
                    contentAlignment = Alignment.Center,
                ) { Icon(type.icon, contentDescription = type.title, tint = type.color, modifier = Modifier.size(28.dp)) }
            }
            Box(
                Modifier.size(56.dp).clip(CircleShape).background(WazeColors.SurfaceGray).clickable(onClick = onClose),
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Filled.Close, contentDescription = "Close", tint = WazeColors.Ink, modifier = Modifier.size(24.dp)) }
        }
    }
}
```

## 5. Navigation (search bottom sheet, no tab bar)

Waze has no bottom tab bar during driving — the map is full-screen with the next-turn card on top, the ETA bar on the bottom, and a **search bottom sheet** (3 detents: small ≈88.dp, medium ≈50%, full). Android has no live blur; Waze uses a **solid white card** anyway (explicitly unlike Apple's translucent sheet), so a plain opaque `Surface` is the correct choice — no translucency needed.

```kotlin
import androidx.compose.material3.ExperimentalMaterial3Api
import androidx.compose.material3.ModalBottomSheet
import androidx.compose.material3.rememberModalBottomSheetState
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.ui.graphics.SolidColor

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun WazeSearchSheet(onDismiss: () -> Unit, content: @Composable ColumnScope.() -> Unit) {
    ModalBottomSheet(
        onDismissRequest = onDismiss,
        sheetState = rememberModalBottomSheetState(skipPartiallyExpanded = false),
        containerColor = WazeColors.CardCanvas, // solid white — Waze never uses a translucent sheet
        shape = RoundedCornerShape(topStart = 16.dp, topEnd = 16.dp),
        dragHandle = {
            Box(Modifier.fillMaxWidth().padding(top = 8.dp, bottom = 8.dp), contentAlignment = Alignment.Center) {
                Box(Modifier.size(36.dp, 5.dp).clip(RoundedCornerShape(2.5.dp)).background(WazeColors.Divider))
            }
        },
    ) {
        Column(Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(16.dp)) {
            Row(
                Modifier.fillMaxWidth().height(48.dp).clip(RoundedCornerShape(16.dp)).background(WazeColors.SurfaceGray)
                    .padding(horizontal = 16.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(8.dp),
            ) {
                Icon(Icons.Filled.Search, null, tint = WazeColors.Secondary, modifier = Modifier.size(17.dp))
                Text("Where to?", style = WazeText.SearchHint, color = WazeColors.Tertiary)
            }
            content()
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| FAB tap | `animateFloatAsState(0.94f)` + shadow tighten; `HapticFeedbackType.LongPress` |
| Report wheel open | `Animatable.animateTo(1f, spring(dampingRatio = 0.7f, stiffness = 300f))` — wheel spins in (~350ms) |
| Hazard bubble appear | `Animatable.animateTo(1f, spring(dampingRatio = 0.55f))` — pop 0 → 1.15 → 1.0 over ~400ms |
| Hazard dismiss (timer) | `animateFloatAsState` alpha 1 → 0 over 1000ms + slight `rotationZ` |
| Location puck pulse | `rememberInfiniteTransition` scale 1.0 → 1.5, alpha 0.15 → 0, 2000ms `RepeatMode.Restart` |
| Polyline ant trail | animate a dash-phase `Float` linearly, 2s loop, `RepeatMode.Restart` |
| Next-turn update | `Crossfade` 250ms + arrow `rotationZ` to the new turn |
| "Go" tap | `animateFloatAsState(0.97f)` + heavy haptic; 400ms `Crossfade` into navigation mode |
| Reach destination | confetti/sparkle `Canvas` over the destination pin + success haptic |

```kotlin
// Polyline "ant trail" — animate the dash phase (DESIGN.md §6: 1 chevron / 2 s)
@Composable
fun rememberAntTrailPhase(): Float {
    val t = rememberInfiniteTransition(label = "antTrail")
    val phase by t.animateFloat(
        0f, -24f, infiniteRepeatable(tween(2000, easing = LinearEasing), RepeatMode.Restart), label = "phase",
    )
    return phase // feed into a PathEffect dashPathEffect on a Canvas-drawn route overlay
}
```

Haptics: prefer `LocalHapticFeedback`. For the heavier "Go" / arrival moments use a `Vibrator` `VibrationEffect.createOneShot(25, VibrationEffect.DEFAULT_AMPLITUDE)` to approximate iOS `.impact(.heavy)` / `.success`; the speeding warning maps to a single `VibrationEffect.createOneShot(15, ...)` triggered once when current speed crosses the limit.

## 7. Icons

Waze ships custom cartoon glyphs (the police hat, the chunky direction arrows); the closest first-party set is `androidx.compose.material:material-icons-extended`. For the playful cartoon feel that makes contributing fun, export Waze's hazard glyphs and the direction arrows as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`. The triangular speech-bubble tail and the cyan arrow puck are custom `Path`s (above) — no SF Symbol or Compose primitive matches them.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| FAB report icon | `exclamationmark.bubble.fill` | `Icons.Filled.Campaign` (or custom speech-bubble drawable) |
| Police hazard | `shield.fill` (or custom hat) | `Icons.Filled.Shield` |
| Traffic hazard | `cone.fill` | `Icons.Filled.Warning` |
| Closure | `xmark.octagon.fill` | `Icons.Filled.Block` |
| Cleared | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Pothole | `exclamationmark.triangle.fill` | `Icons.Filled.ReportProblem` |
| Camera | `camera.fill` | `Icons.Filled.CameraAlt` |
| Direction right / left | `arrow.turn.up.right` / `.left` | `Icons.AutoMirrored.Filled.TurnRight` / `TurnLeft` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Speed warning | `exclamationmark.octagon.fill` | `Icons.Filled.Report` |
| Beacon share | `bell.badge.fill` | `Icons.Filled.NotificationsActive` |
| Gas | `fuelpump.fill` | `Icons.Filled.LocalGasStation` |
| Home | `house.fill` | `Icons.Filled.Home` |
| Work | `briefcase.fill` | `Icons.Filled.Work` |
| Favorite | `star.fill` | `Icons.Filled.Star` |
| Recent | `clock` | `Icons.Filled.History` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; Maps Compose + the tinted shadows are comfortable at 24 — `spotColor` needs API 28+, fall back to a soft purple radial glow behind the FAB/"Go" on older devices). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. Add a Maps API key in the manifest.
- **Edge-to-edge**: call `enableEdgeToEdge()`. The next-turn card intentionally extends into the status-bar area (top corners are square) with 16.dp of content padding from the safe-area top; the ETA bar and FAB respect `WindowInsets.navigationBars` (the FAB sits 16.dp above the ETA bar's inset).
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on the place card title, body, list rows, and search. **FIX** the glance-critical text — next-turn card, ETA numbers, speed-limit number, hazard speech-bubble text are read at driving speed and must not reflow: derive from `dp` or wrap those subtrees in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: the hazard bubble reads as `"$type, reported $timeAgo, ahead"` via `Modifier.semantics(mergeDescendants = true)`; the location puck announces `"Your current position, heading <direction>"`; the speed tile reads limit + current speed and announces a warning state.
- **Touch targets**: Material guidance is 48.dp minimum. The 56.dp FAB and 56.dp "Go" button clear it; the full hazard bubble is tappable; report-wheel wedges are 44.dp (pad to a 48.dp hit slop); the Wazer avatar carries a 48.dp hit area on a 24.dp glyph.
- **Contrast**: Ink `#1A1A1A` on white passes WCAG AAA. White on Waze Purple `#7E55BE` passes AAA at all sizes (next-turn card, "Go"). The hazard speech bubbles use white text on full-saturation hazard colors — Closure Yellow `#F9C42E` with white is a known low-contrast pair; keep the 14sp Bold title weight (it passes AA at that weight) and never go below 14sp for hazard text. The 0.8-alpha white timestamp on hazard colors stays ≥11sp Bold-adjacent.
- **Reduce motion**: when a reduced-motion preference is set, skip the hazard "pop", freeze the location-puck pulse and the polyline ant trail (static), and replace the "Go" → navigation crossfade with an instant cut.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme` — Waze's identity is the fixed electric duo (Waze Purple `#7E55BE` + Cyan `#33CCFF`) and the full-saturation hazard palette. These must stay at brand saturation across light/dark/night modes; wallpaper-derived tints would mute the cartoon system and break the at-a-glance hazard color code.
