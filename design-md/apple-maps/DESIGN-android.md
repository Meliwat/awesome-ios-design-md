# Apple Maps (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. Apple Maps is the canonical reference for native iOS map UI. This file ports that visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Apple Maps' cream-parchment cartography, sliding bottom search sheet, the pulsing blue location puck, blur-as-depth) while making everything idiomatic Android — Material 3 `BottomSheetScaffold` instead of `.presentationDetents`, a translucent `Surface` instead of `.regularMaterial` (Android has no live blur), `sp`/`dp` instead of `pt`. The cartography is rendered by Google's `Maps Compose` SDK with a custom JSON style approximating the cream/water/park palette.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, `androidx.compose.material3:material3` with the bottom-sheet APIs, and `com.google.maps.android:maps-compose` `4.3+` for the map surface (style it via a `MapStyleOptions` JSON to the cream palette below).

## 1. Color Tokens

```kotlin
// ui/theme/MapsColors.kt
import androidx.compose.ui.graphics.Color

object MapsColors {
    // Map Cartography — the brand
    val Cream       = Color(0xFFF6F1E6) // land surface
    val Water       = Color(0xFFB9D9EB) // oceans, rivers, lakes
    val ParkGreen   = Color(0xFFD6E5C9) // parks, green space
    val RoadWhite   = Color(0xFFFFFFFF) // major roads
    val RoadOutline = Color(0xFFD5CFC0) // minor road strokes
    val Highway     = Color(0xFFFFD966) // highway fill
    val Building2D  = Color(0xFFEFE9DD)
    val Building3D  = Color(0xFFE6DFD0)
    val MapLabel    = Color(0xFF594F3F) // warm dark slate map text

    // Brand
    val Blue        = Color(0xFF0A84FF) // directions polyline, puck, primary CTAs
    val BluePressed = Color(0xFF0967D2)
    val BlueDark    = Color(0xFF409CFF) // brighter blue for dark mode
    val Red         = Color(0xFFFF3B30) // dropped pin (iOS system Red)

    // Category Colors
    val Food     = Color(0xFFFF9500)
    val Coffee   = Color(0xFFA5694C)
    val Drinks   = Color(0xFF5AC8FA)
    val Shopping = Color(0xFFFFCC00)
    val Parking  = Color(0xFFAF52DE)
    val Hotel    = Color(0xFF5856D6)
    val Health   = Color(0xFFFF2D55)
    val EVGreen  = Color(0xFF34C759)

    // UI Chrome
    val CardCanvas   = Color(0xFFFFFFFF)
    val SurfaceGray  = Color(0xFFF2F2F7) // search field fill, chip backgrounds
    val SurfaceGray2 = Color(0xFFE5E5EA) // pressed list rows
    val Divider      = Color(0xFFC7C7CC)

    // Text (base colors; apply alpha for secondary tiers)
    val Ink       = Color(0xFF000000)            // primary — pure black on cream
    val Secondary = Color(0xFF3C3C43)            // apply .copy(alpha = 0.60f)
    // tertiary = Secondary @ 0.30f, placeholder = Secondary @ 0.18f

    // Semantic
    val Success = Color(0xFF34C759)
    val Warning = Color(0xFFFF9500)
    val Error   = Color(0xFFFF3B30)

    // Dark mode (warm dark land — the dark equivalent of cream paper)
    val DarkMapLand  = Color(0xFF2A2826)
    val DarkMapWater = Color(0xFF1C2638)
    val DarkMapPark  = Color(0xFF2C3A2A)
    val DarkCardSurf = Color(0xFF1C1C1E)
    val DarkSurface2 = Color(0xFF2C2C2E)
    val DarkDivider  = Color(0xFF38383A)
}
```

Apple Maps' chrome is a light frosted layer over warm cartography, so wire a Material 3 `lightColorScheme` (provide the dark variant for dark mode). `primary` is Maps Blue.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val MapsLight = lightColorScheme(
    primary        = MapsColors.Blue,
    onPrimary      = Color.White,
    background     = MapsColors.CardCanvas,
    onBackground   = MapsColors.Ink,            // pure black on cream
    surface        = MapsColors.CardCanvas,
    onSurface      = MapsColors.Ink,
    surfaceVariant = MapsColors.SurfaceGray,
    outline        = MapsColors.Divider,
    error          = MapsColors.Error,
)

private val MapsDark = darkColorScheme(
    primary        = MapsColors.BlueDark,       // brighter blue for dark legibility
    onPrimary      = Color.White,
    background     = MapsColors.DarkCardSurf,
    onBackground   = Color.White,
    surface        = MapsColors.DarkCardSurf,
    onSurface      = Color.White,
    surfaceVariant = MapsColors.DarkSurface2,
    outline        = MapsColors.DarkDivider,
    error          = MapsColors.Error,
)

@Composable
fun MapsTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (darkTheme) MapsDark else MapsLight,
    typography  = MapsTypography,
    content     = content,
)
```

## 2. Typography

Apple Maps uses SF Pro at the system stack with three optical variants. On Android, SF Pro is unavailable — use the system font (Roboto), the closest neutral grotesque, and reserve `Inter` (bundled in `res/font/`) if you want a tighter match to SF Pro's metrics. Tabular figures are mandatory on every distance/time/ETA — apply `FontFeatureSetting("tnum")`.

```kotlin
// ui/theme/MapsType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

// System Roboto stack; swap to FontFamily(Font(R.font.inter_…)) for a closer SF Pro match
private val SF = FontFamily.Default
private val Tnum = "tnum" // FontFeatureSetting via fontFeatureSettings on numeric styles

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object MapsText {
    val PlaceTitle    = TextStyle(SF, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val PlaceSubtitle = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp)
    val SearchHint    = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 22.sp)
    val NavTitle      = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val SectionHdr    = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.4.sp) // UPPERCASE
    val ListTitle     = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 22.sp)
    val ListSubtitle  = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 16.sp)
    val ETATime       = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 22.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp, fontFeatureSettings = Tnum)
    val ETADuration   = TextStyle(SF, fontWeight = FontWeight.Medium,   fontSize = 17.sp, lineHeight = 20.sp, fontFeatureSettings = Tnum)
    val StepTitle     = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val StepDistance  = TextStyle(SF, fontWeight = FontWeight.Medium,   fontSize = 17.sp, lineHeight = 22.sp, fontFeatureSettings = Tnum)
    val CategoryLabel = TextStyle(SF, fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 14.sp)
    val Button        = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 17.sp)
    val CityLabel     = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val Neighborhood  = TextStyle(SF, fontWeight = FontWeight.Medium,   fontSize = 12.sp, lineHeight = 12.sp)
    val Street        = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 10.sp, lineHeight = 10.sp)
    val TransitBadge  = TextStyle(SF, fontWeight = FontWeight.Bold,     fontSize = 12.sp, lineHeight = 12.sp)
    val Caption       = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots
val MapsTypography = Typography(
    headlineMedium = MapsText.PlaceTitle,
    titleMedium    = MapsText.NavTitle,
    bodyLarge      = MapsText.ListTitle,
    bodyMedium     = MapsText.PlaceSubtitle,
    labelLarge     = MapsText.Button,
    labelSmall     = MapsText.Caption,
)
```

## 3. Signature Components

### Search Card content (sliding bottom sheet — see §4 for the sheet host)

iOS uses `.regularMaterial` on the card; Android has no live blur, so the sheet is a 90%-opaque `CardCanvas` surface. The grabber is a `BottomSheetScaffold` default, but the spec's exact 36×5dp / 30%-alpha pill is shown.

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.input.TextFieldValue
import androidx.compose.ui.unit.dp

@Composable
fun SearchCardContent(expanded: Boolean) {
    Column(Modifier.fillMaxWidth()) {
        // Drag indicator (grabber) — exact spec
        Box(
            Modifier
                .padding(top = 8.dp)
                .align(Alignment.CenterHorizontally)
                .size(width = 36.dp, height = 5.dp)
                .clip(RoundedCornerShape(2.5.dp))
                .background(MapsColors.Secondary.copy(alpha = 0.30f)),
        )

        // Search field
        var query by remember { mutableStateOf(TextFieldValue("")) }
        Row(
            modifier = Modifier
                .padding(horizontal = 16.dp, vertical = 12.dp)
                .fillMaxWidth()
                .height(44.dp)
                .clip(RoundedCornerShape(10.dp))
                .background(MapsColors.SurfaceGray)
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Icon(Icons.Filled.Search, contentDescription = null,
                tint = MapsColors.Secondary.copy(alpha = 0.60f), modifier = Modifier.size(18.dp))
            Text(
                if (query.text.isEmpty()) "Search Maps" else query.text,
                style = MapsText.SearchHint,
                color = if (query.text.isEmpty())
                    MapsColors.Secondary.copy(alpha = 0.30f) else MapsColors.Ink,
                modifier = Modifier.weight(1f),
            )
            Icon(Icons.Filled.Mic, contentDescription = "Voice search",
                tint = MapsColors.Secondary.copy(alpha = 0.60f), modifier = Modifier.size(16.dp))
        }

        // Category pills — only at medium+ detent
        if (expanded) {
            LazyRow(
                contentPadding = PaddingValues(horizontal = 16.dp),
                horizontalArrangement = Arrangement.spacedBy(12.dp),
                modifier = Modifier.padding(top = 4.dp, bottom = 16.dp),
            ) {
                item { CategoryPill(Icons.Filled.Restaurant, "Food", MapsColors.Food) }
                item { CategoryPill(Icons.Filled.LocalCafe, "Coffee", MapsColors.Coffee) }
                item { CategoryPill(Icons.Filled.LocalBar, "Drinks", MapsColors.Drinks) }
                item { CategoryPill(Icons.Filled.ShoppingBag, "Shopping", MapsColors.Shopping) }
                item { CategoryPill(Icons.Filled.LocalParking, "Parking", MapsColors.Parking) }
                item { CategoryPill(Icons.Filled.Tram, "Transit", MapsColors.Blue) }
            }
        }
    }
}
```

### Category Pill

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun CategoryPill(icon: ImageVector, label: String, color: Color, onClick: () -> Unit = {}) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        if (pressed) 0.96f else 1f,
        spring(dampingRatio = 0.7f, stiffness = 600f),
        label = "pillScale",
    )
    Column(
        modifier = Modifier
            .size(width = 64.dp, height = 88.dp)
            .scale(scale)
            .shadow(12.dp, RoundedCornerShape(14.dp), spotColor = Color.Black.copy(alpha = 0.12f))
            .clip(RoundedCornerShape(14.dp))
            .background(MapsColors.CardCanvas) // solid white — NOT translucent
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(vertical = 16.dp, horizontal = 8.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(icon, contentDescription = label, tint = color, modifier = Modifier.size(28.dp))
        Text(label, style = MapsText.CategoryLabel, color = MapsColors.Ink, maxLines = 1)
    }
}
```

### Current-Location Puck (pulsing blue dot + directional cone)

The 2s infinite pulse is a `rememberInfiniteTransition`; the cone is a custom `Path` rotated to the heading.

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.drawscope.rotate

@Composable
fun CurrentLocationPuck(heading: Float?, modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "puck")
    val pulseScale by t.animateFloat(
        1f, 1.6f,
        infiniteRepeatable(tween(2000, easing = EaseOut), RepeatMode.Restart),
        label = "pulseScale",
    )
    val pulseAlpha by t.animateFloat(
        0.30f, 0f,
        infiniteRepeatable(tween(2000, easing = EaseOut), RepeatMode.Restart),
        label = "pulseAlpha",
    )

    Box(modifier.size(80.dp), contentAlignment = Alignment.Center) {
        // Pulsing outer ring (30dp scaling 1 → 1.6, alpha 0.30 → 0)
        Box(
            Modifier
                .size(30.dp)
                .scale(pulseScale)
                .clip(CircleShape)
                .background(MapsColors.Blue.copy(alpha = pulseAlpha)),
        )
        // Directional cone (90°, gradient, 60dp) when heading known
        if (heading != null) {
            Canvas(Modifier.size(80.dp)) {
                rotate(heading, pivot = center) {
                    val cone = Path().apply {
                        moveTo(center.x, center.y)
                        lineTo(center.x - size.width * 0.4f, center.y - 60.dp.toPx())
                        lineTo(center.x + size.width * 0.4f, center.y - 60.dp.toPx())
                        close()
                    }
                    drawPath(
                        cone,
                        Brush.verticalGradient(
                            0f to MapsColors.Blue.copy(alpha = 0.4f),
                            1f to MapsColors.Blue.copy(alpha = 0f),
                            startY = center.y - 60.dp.toPx(),
                            endY = center.y,
                        ),
                    )
                }
            }
        }
        // Blue dot with 3dp white ring
        Box(
            Modifier
                .size(22.dp)
                .clip(CircleShape)
                .background(MapsColors.Blue)
                .border(3.dp, Color.White, CircleShape)
                .shadow(4.dp, CircleShape, spotColor = Color.Black.copy(alpha = 0.2f)),
        )
    }
}
```

### Map Controls (floating top-right stack)

iOS uses `.regularMaterial`; Android has no live blur, so each button is a 92%-opaque white circular `Surface`.

```kotlin
import androidx.compose.material3.Surface

@Composable
fun MapControls(
    onLayers: () -> Unit,
    onLocation: () -> Unit,
    on3D: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier = modifier.padding(top = 16.dp, end = 16.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        MapControlButton(Icons.Filled.Layers, "Map layers", onLayers)
        MapControlButton(Icons.Filled.MyLocation, "Center on my location", onLocation)
        MapControlButton(Icons.Filled.ThreeDRotation, "Toggle 3D", on3D)
    }
}

@Composable
private fun MapControlButton(icon: ImageVector, desc: String, onClick: () -> Unit) {
    Surface(
        color = MapsColors.CardCanvas.copy(alpha = 0.92f), // translucent — no live blur on Android
        shape = CircleShape,
        modifier = Modifier.size(44.dp),
        onClick = onClick,
    ) {
        Box(contentAlignment = Alignment.Center) {
            Icon(icon, contentDescription = desc, tint = MapsColors.Ink,
                modifier = Modifier.size(18.dp))
        }
    }
}
```

### Place Card (detail view) — Action Pill row

```kotlin
import androidx.compose.foundation.border

enum class PillStyle { Filled, Outlined }

@Composable
fun ActionPill(
    label: String,
    icon: ImageVector,
    style: PillStyle,
    onClick: () -> Unit,
) {
    val haptics = androidx.compose.ui.platform.LocalHapticFeedback.current
    val filled = style == PillStyle.Filled
    Row(
        modifier = Modifier
            .heightIn(min = 44.dp)
            .clip(CircleShape) // 22dp full pill at 44dp height
            .then(
                if (filled) Modifier.background(MapsColors.Blue)
                else Modifier
                    .background(MapsColors.CardCanvas)
                    .border(1.dp, MapsColors.Blue, CircleShape)
            )
            .clickable {
                haptics.performHapticFeedback(
                    androidx.compose.ui.hapticfeedback.HapticFeedbackType.LongPress) // ~iOS .medium
                onClick()
            }
            .padding(horizontal = 20.dp, vertical = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(icon, contentDescription = null,
            tint = if (filled) Color.White else MapsColors.Blue, modifier = Modifier.size(14.dp))
        Text(label, style = MapsText.Button, color = if (filled) Color.White else MapsColors.Blue)
    }
}

@Composable
fun PlaceCardHeader(title: String, subtitle: String, onClose: () -> Unit) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 20.dp, vertical = 16.dp),
        verticalAlignment = Alignment.Top,
    ) {
        Column(Modifier.weight(1f)) {
            Text(title, style = MapsText.PlaceTitle, color = MapsColors.Ink)
            Text(subtitle, style = MapsText.PlaceSubtitle,
                color = MapsColors.Secondary.copy(alpha = 0.60f))
        }
        Surface(
            color = MapsColors.SurfaceGray.copy(alpha = 0.92f),
            shape = CircleShape,
            modifier = Modifier.size(30.dp),
            onClick = onClose,
        ) {
            Box(contentAlignment = Alignment.Center) {
                Icon(Icons.Filled.Close, contentDescription = "Close",
                    tint = MapsColors.Secondary.copy(alpha = 0.80f),
                    modifier = Modifier.size(14.dp))
            }
        }
    }
}
```

### Direction Step Card + ETA Bar

```kotlin
@Composable
fun StepCard(arrow: ImageVector, arrowRotation: Float, title: String, distance: String) {
    Surface(color = MapsColors.CardCanvas.copy(alpha = 0.92f), tonalElevation = 0.dp) {
        Row(
            Modifier.fillMaxWidth().height(96.dp).padding(horizontal = 20.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(16.dp),
        ) {
            Icon(
                arrow, contentDescription = null, tint = MapsColors.Blue,
                modifier = Modifier
                    .size(56.dp)
                    .androidx.compose.ui.draw.rotate(arrowRotation),
            )
            Column(Modifier.weight(1f)) {
                Text(title, style = MapsText.StepTitle, color = MapsColors.Ink, maxLines = 2)
                Text(distance, style = MapsText.StepDistance,
                    color = MapsColors.Secondary.copy(alpha = 0.60f))
            }
        }
    }
}

@Composable
fun ETABar(duration: String, arrival: String, onEnd: () -> Unit) {
    Surface(color = MapsColors.CardCanvas.copy(alpha = 0.92f), tonalElevation = 0.dp) {
        Row(
            Modifier.fillMaxWidth().height(80.dp).padding(horizontal = 20.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Column(Modifier.weight(1f)) {
                Text(duration, style = MapsText.ETATime, color = MapsColors.Ink)
                Text(arrival, style = MapsText.ETADuration,
                    color = MapsColors.Secondary.copy(alpha = 0.60f))
            }
            Row(
                Modifier
                    .clip(CircleShape)
                    .background(MapsColors.CardCanvas)
                    .border(1.dp, MapsColors.Error, CircleShape)
                    .clickable(onClick = onEnd)
                    .padding(horizontal = 20.dp, vertical = 12.dp),
            ) {
                Text("End", style = MapsText.Button, color = MapsColors.Error)
            }
        }
    }
}
```

### Transit Line Badge

```kotlin
@Composable
fun TransitBadge(label: String, color: Color) {
    Box(
        Modifier
            .defaultMinSize(minWidth = 24.dp, minHeight = 24.dp)
            .clip(RoundedCornerShape(4.dp))
            .background(color) // agency-accurate color, never recolored
            .padding(horizontal = 6.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = MapsText.TransitBadge, color = Color.White)
    }
}
```

## 4. The Sliding Bottom Search Sheet (signature interaction)

Apple Maps' defining move is the translucent search card with three detents. iOS uses `.presentationDetents([.height(88), .medium, .large])`; the Compose equivalent is Material 3 `BottomSheetScaffold` with a custom `SheetState`. The sheet stays partially expanded (peek = 88.dp) so the user can still pan the map beneath it — that's the Apple Maps "background interaction" behavior, native to `BottomSheetScaffold`.

```kotlin
import androidx.compose.material3.BottomSheetScaffold
import androidx.compose.material3.SheetValue
import androidx.compose.material3.rememberBottomSheetScaffoldState
import androidx.compose.material3.rememberStandardBottomSheetState

@OptIn(androidx.compose.material3.ExperimentalMaterial3Api::class)
@Composable
fun MapScreen() {
    val sheetState = rememberStandardBottomSheetState(
        initialValue = SheetValue.PartiallyExpanded, // ≈ iOS "small" detent
        skipHiddenState = true,
    )
    val scaffoldState = rememberBottomSheetScaffoldState(bottomSheetState = sheetState)
    val expanded = sheetState.currentValue == SheetValue.Expanded

    BottomSheetScaffold(
        scaffoldState = scaffoldState,
        sheetPeekHeight = 88.dp,                          // small detent
        sheetContainerColor = MapsColors.CardCanvas.copy(alpha = 0.90f), // translucent (no live blur)
        sheetShape = RoundedCornerShape(topStart = 10.dp, topEnd = 10.dp),
        sheetContent = { SearchCardContent(expanded = expanded) },
    ) {
        Box(Modifier.fillMaxSize()) {
            // GoogleMap(...) styled with MapStyleOptions JSON → cream palette goes here
            MapControls(
                onLayers = {}, onLocation = {}, on3D = {},
                modifier = Modifier.align(Alignment.TopEnd),
            )
        }
    }
}
```

For a true 3-stop detent (88dp / ~50% / full), wrap a custom `AnchoredDraggable` (Compose Foundation 1.6+) with three anchors, or gate `SearchCardContent`'s category row on a `derivedStateOf` height threshold as shown. Animate detent changes with `spring(dampingRatio = 0.85f, stiffness = 380f)` to match the spec's "350ms physical paper" feel.

## 5. Navigation

Apple Maps has **no bottom tab bar** — the entire experience is the map plus the sliding search sheet and a contextual place card. Do **not** add a Material `NavigationBar`. Navigation between Map → Place Card → Directions is modeled as bottom-sheet content swaps inside the single `BottomSheetScaffold` (use `AnimatedContent` to cross-fade Search ↔ Place ↔ Directions content). Floating chrome (map controls, step card, ETA bar) overlays the map with translucent `Surface`s — Android has no live blur, so a 92%-opaque surface stands in for `.regularMaterial`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Search card detent transition | `spring(dampingRatio = 0.85f, stiffness = 380f)` ≈ 350ms physical |
| Pin drop (long-press) | `Animatable` translationY + `spring(dampingRatio = 0.65f)` ~400ms bounce; `HapticFeedbackType.LongPress` |
| Current-location lock | camera animate 600ms `EaseOut`, then puck `Animatable` 1 → 1.2 → 1 pulse |
| Category pill tap | `animateFloatAsState` 1 → 0.96 → 1 `spring(dampingRatio = 0.7f)` |
| Direction step transition | `AnimatedContent` 300ms cross-fade; arrow `animateFloatAsState` rotation 250ms |
| Map style picker | `AnimatedVisibility` expand from the layer button (no native shared-element morph required) |
| Look Around launch | 600ms `Crossfade` from map to 360° view |
| Current-location pulse | `rememberInfiniteTransition` 2000ms: scale 1 → 1.6, alpha 0.30 → 0, infinite |

```kotlin
// Pin drop — exact 400ms ease-out bounce
@Composable
fun rememberPinDrop(dropped: Boolean): Float {
    val offsetY = remember { Animatable(-120f) }
    LaunchedEffect(dropped) {
        if (dropped) offsetY.animateTo(
            0f,
            spring(dampingRatio = 0.65f, stiffness = 350f),
        )
    }
    return offsetY.value
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) — map `.medium` (pin drop, Directions tap) to `VibrationEffect.createOneShot(15, DEFAULT_AMPLITUDE)`, `.selection` (map-style picker) to a short tick, and `.success` (arrived at destination) to a double-buzz `VibrationEffect.createWaveform(longArrayOf(0, 40, 60, 40), -1)`, mirroring iOS `.notificationOccurred(.success)`.

## 7. Icons

Apple Maps uses SF Symbols; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity (the directional cone, the agency transit badges, the dropped-pin teardrop) ship custom vector drawables and load via `ImageVector.vectorResource(R.drawable.…)` — the cone is drawn with `Canvas` (see §3).

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Microphone | `mic.fill` | `Icons.Filled.Mic` |
| Close | `xmark` | `Icons.Filled.Close` |
| Layer picker | `square.3.layers.3d` | `Icons.Filled.Layers` |
| Current location (locked) | `location.fill` | `Icons.Filled.MyLocation` |
| Current location (unlocked) | `location` | `Icons.Filled.LocationSearching` |
| 3D toggle | `view.3d` | `Icons.Filled.ThreeDRotation` |
| Compass | `safari` | `Icons.Filled.Explore` |
| Dropped pin | `mappin.and.ellipse` | `Icons.Filled.LocationOn` (or custom teardrop drawable, Maps Red) |
| Directions | `arrow.triangle.turn.up.right.diamond.fill` | `Icons.AutoMirrored.Filled.Directions` |
| Call | `phone.fill` | `Icons.Filled.Call` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Save | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Look Around | `binoculars.fill` | `Icons.Filled.Visibility` |
| Food category | `fork.knife` | `Icons.Filled.Restaurant` |
| Coffee category | `cup.and.saucer.fill` | `Icons.Filled.LocalCafe` |
| Drinks category | `wineglass.fill` | `Icons.Filled.LocalBar` |
| Shopping | `bag.fill` | `Icons.Filled.ShoppingBag` |
| Parking | `parkingsign.circle.fill` | `Icons.Filled.LocalParking` |
| Transit | `tram.fill` | `Icons.Filled.Tram` |
| Direction arrow (turn) | `arrow.turn.up.right` / `.left` | `Icons.AutoMirrored.Filled.TurnRight` / `TurnLeft` |
| Phone (place row) | `phone.fill` | `Icons.Filled.Call` |
| Globe (website row) | `globe` | `Icons.Filled.Language` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `BottomSheetScaffold` + `Canvas` cone + Maps Compose are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The map is full-bleed under the status bar; request dark-content (dark icons) over the cream canvas, light-content in dark mode. Apply `Scaffold`/`WindowInsets.systemBars` padding so the search sheet's small detent clears the gesture nav and map controls sit 16.dp below the status bar / Dynamic-Island analog (the display cutout).
- **Font scaling**: `sp` honors the user's font scale — keep it on the place-card title, list rows, button labels, and body. Pin layout/zoom-sensitive text (map labels, ETA numbers, distance/time tabular figures, 14sp category labels) by deriving size from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. The place-card title scales but clamp at ~36sp. Keep `fontFeatureSettings = "tnum"` on every numeric style for column alignment.
- **TalkBack**: the place card reads as one node — `Modifier.semantics(mergeDescendants = true) { contentDescription = "$title, $subtitle, ${if (isOpen) "Open" else "Closed"}, $closingTime" }`; the current-location puck announces `"Your current location"`; the dropped pin exposes its reverse-geocoded address. Mark each map control button with its own `contentDescription` ("Center on my location", etc.).
- **Touch targets**: Material guidance is 48.dp minimum. Map control buttons and action pills are 44.dp — bump to 48.dp via padding. The category pill is 64×88dp (well clear). The pin tap target needs a 48.dp hit box centered on the teardrop tip even though the glyph is smaller.
- **Contrast**: pure black on cream `#F6F1E6` passes WCAG AAA. `#3C3C43`@60% on cream passes AA only at ≥18sp Bold or ≥14sp Medium — do not use it for the 13sp list subtitle as primary information; it's acceptable as supporting metadata only.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, skip the 2s puck pulse (render a static blue dot) and the pin-drop bounce (place the pin instantly).
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — the cream `#F6F1E6` cartography, Maps Blue `#0A84FF` polyline/puck, and Maps Red `#FF3B30` pin are signature, intentionally system-Apple (not system-Android) colors and must not recolor to the user's wallpaper.
