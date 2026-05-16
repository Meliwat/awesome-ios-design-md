# Lyft (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Lyft's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics — including the full-screen map + rounded bottom sheet model and the ride-type selector.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Lyft's full-screen map, the soft rounded bottom sheet, single pink accent, very-rounded geometry, the ride-type selector) while making everything idiomatic Android — `ModalBottomSheet` instead of UIKit detents, `dp`/`sp` instead of `pt`, Compose haptics instead of `UIImpactFeedbackGenerator`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, the Maps Compose library (`com.google.maps.android:maps-compose`), and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/LyftColors.kt
import androidx.compose.ui.graphics.Color

object LyftColors {
    // Light
    val Canvas      = Color(0xFFFFFFFF)
    val Surface     = Color(0xFFF4F4F7)
    val SurfaceDeep = Color(0xFFE9E9EF)
    val Divider     = Color(0xFFE5E5EA)
    val TextPrimary   = Color(0xFF11111F)
    val TextSecondary = Color(0xFF6B6B7B)
    val TextTertiary  = Color(0xFF9A9AA8)

    // Dark
    val CanvasDark      = Color(0xFF11111F)
    val SurfaceDark     = Color(0xFF1C1C2B)
    val SurfaceDeepDark = Color(0xFF26263A)
    val DividerDark     = Color(0xFF2C2C40)
    val TextPrimaryDark = Color(0xFFFFFFFF)
    val TextSecondaryDark = Color(0xFFA0A0B4)

    // Brand
    val Pink         = Color(0xFFFF00BF)
    val PinkPressed  = Color(0xFFD500A0)
    val PinkTint     = Color(0xFFFFE5F7)
    val PinkTintDark = Color(0xFF33102B)

    // Semantic
    val Success = Color(0xFF00A862)
    val Gold    = Color(0xFFFFB400)
    val Error   = Color(0xFFE5484D)
}
```

Lyft ships **both** light and dark — wire two Material 3 schemes and pick by system theme. The pink stays identical on both.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val LyftLight = lightColorScheme(
    primary = LyftColors.Pink, onPrimary = Color.White,
    background = LyftColors.Canvas, onBackground = LyftColors.TextPrimary,
    surface = LyftColors.Canvas, onSurface = LyftColors.TextPrimary,
    surfaceVariant = LyftColors.Surface, onSurfaceVariant = LyftColors.TextSecondary,
    outline = LyftColors.Divider, error = LyftColors.Error,
)
private val LyftDark = darkColorScheme(
    primary = LyftColors.Pink, onPrimary = Color.White,
    background = LyftColors.CanvasDark, onBackground = LyftColors.TextPrimaryDark,
    surface = LyftColors.CanvasDark, onSurface = LyftColors.TextPrimaryDark,
    surfaceVariant = LyftColors.SurfaceDark, onSurfaceVariant = LyftColors.TextSecondaryDark,
    outline = LyftColors.DividerDark, error = LyftColors.Error,
)

@Composable
fun LyftTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) LyftDark else LyftLight,
        typography = LyftTypography, content = content,
    )
```

## 2. Typography

Lyft uses Inter with a friendly, rounded character. Drop the TTFs in `res/font/` (lowercase, snake_case). Fall back to the system font (Roboto).

```kotlin
// ui/theme/LyftType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object LyftText {
    val SheetTitle = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val WhereTo    = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 20.sp, lineHeight = 25.sp, letterSpacing = (-0.2).sp)
    val RideName   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val Price      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 20.sp)
    val Body       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Button     = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 20.sp)
    val DriverName = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 18.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val Meta       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val RideSub    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Badge      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
    val Caption    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 15.sp)
    val PinLabel   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 13.sp, lineHeight = 15.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val LyftTypography = Typography(
    headlineSmall = LyftText.SheetTitle,
    titleMedium   = LyftText.RideName,
    bodyMedium    = LyftText.Body,
    labelSmall    = LyftText.Badge,
)
```

Render prices, ETAs, and fare breakdowns with tabular figures via `TextStyle(fontFeatureSettings = "tnum")`.

## 3. Signature Components

### Full-Screen Map + Rounded Bottom Sheet

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import com.google.maps.android.compose.GoogleMap

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun RideHomeScreen() {
    val sheetState = rememberStandardBottomSheetState(
        initialValue = SheetValue.PartiallyExpanded,
        skipHiddenState = true,
    )
    val scaffold = rememberBottomSheetScaffoldState(bottomSheetState = sheetState)

    BottomSheetScaffold(
        scaffoldState = scaffold,
        sheetPeekHeight = 140.dp,
        sheetShape = RoundedCornerShape(topStart = 28.dp, topEnd = 28.dp), // very rounded
        sheetContainerColor = MaterialTheme.colorScheme.surface,
        sheetDragHandle = {
            Box(Modifier.padding(top = 8.dp).size(width = 36.dp, height = 4.dp)
                .clip(RoundedCornerShape(2.dp)).background(LyftColors.Divider))
        },
        sheetContent = { ChooseRideSheet() },
    ) {
        Box(Modifier.fillMaxSize()) {
            GoogleMap(modifier = Modifier.fillMaxSize())  // full-bleed map
            MapIconButton(
                "menu",
                Modifier.padding(16.dp).align(Alignment.TopStart),
            ) { /* open menu */ }
        }
    }
}
```

> `BottomSheetScaffold` snaps with a spring. For the signature slight overshoot, wrap programmatic snaps in `animateTo(target, spring(dampingRatio = 0.85f, stiffness = 220f))`.

### Ride-Type Selector Row

```kotlin
import androidx.compose.animation.animateColorAsState
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.clip
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun RideTypeRow(
    iconUrl: String,
    name: String,
    subtitle: String,
    capacityEta: String,
    price: String,
    selected: Boolean,
    onClick: () -> Unit,
) {
    val haptics = LocalHapticFeedback.current
    val bg by animateColorAsState(if (selected) LyftColors.PinkTint else Color.Transparent,
        label = "rowBg")
    val border by animateColorAsState(if (selected) LyftColors.Pink else Color.Transparent,
        label = "rowBorder")

    Row(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(16.dp))
            .background(bg)
            .border(2.dp, border, RoundedCornerShape(16.dp))
            .clickable {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                onClick()
            }
            .padding(horizontal = 16.dp, vertical = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        AsyncImage(model = iconUrl, contentDescription = null,
            modifier = Modifier.size(width = 56.dp, height = 40.dp))
        Column(Modifier.weight(1f)) {
            Text(name, style = LyftText.RideName, color = MaterialTheme.colorScheme.onSurface)
            Text(subtitle, style = LyftText.RideSub, color = MaterialTheme.colorScheme.onSurfaceVariant)
            Text(capacityEta, style = LyftText.Meta, color = MaterialTheme.colorScheme.onSurfaceVariant)
        }
        Text(price, style = LyftText.Price.copy(fontFeatureSettings = "tnum"),
            color = MaterialTheme.colorScheme.onSurface)
    }
}
```

### Primary CTA

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.draw.scale

@Composable
fun LyftCTA(label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f,
        spring(dampingRatio = 0.8f), label = "ctaScale")
    Box(
        modifier
            .fillMaxWidth()
            .height(56.dp)
            .scale(scale)
            .clip(RoundedCornerShape(16.dp))
            .background(if (pressed) LyftColors.PinkPressed else LyftColors.Pink)
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = LyftText.Button, color = Color.White)
    }
}
```

### Pickup Pin (with drop)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Path

@Composable
fun PickupPin(modifier: Modifier = Modifier) {
    val drop = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(Unit) {
        haptics.performHapticFeedback(HapticFeedbackType.LongPress)
        drop.animateTo(1f, spring(dampingRatio = 0.5f, stiffness = 200f)) // bounce
    }
    Box(
        modifier
            .size(28.dp, 38.dp)
            .scale(0.6f + 0.4f * drop.value)
            .offset(y = (-40 * (1 - drop.value)).dp),
        contentAlignment = Alignment.TopCenter,
    ) {
        Canvas(Modifier.matchParentSize()) {
            val w = size.width
            val path = Path().apply {
                addOval(androidx.compose.ui.geometry.Rect(0f, 0f, w, w))
                moveTo(w / 2, w * 0.5f)
                lineTo(w / 2, size.height)
                close()
            }
            drawPath(path, LyftColors.Pink)
            drawCircle(Color.White, radius = w * 0.18f, center = Offset(w / 2, w * 0.42f))
        }
    }
}
```

### "Where to?" Search Bar

```kotlin
import androidx.compose.ui.draw.shadow

@Composable
fun WhereToBar(modifier: Modifier = Modifier) {
    Row(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 20.dp)
            .shadow(12.dp, RoundedCornerShape(16.dp),
                spotColor = LyftColors.TextPrimary.copy(alpha = 0.16f))
            .clip(RoundedCornerShape(16.dp))
            .background(MaterialTheme.colorScheme.surface)
            .padding(horizontal = 18.dp)
            .height(56.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(10.dp).clip(RoundedCornerShape(3.dp)).background(LyftColors.Pink))
        Text("Where to?", style = LyftText.WhereTo,
            color = MaterialTheme.colorScheme.onSurfaceVariant)
    }
}
```

## 4. Sheet State Progression

```kotlin
enum class TripState { Idle, ChooseRide, ConfirmPickup, EnRoute, Rate }
// Hold TripState in a ViewModel. Map each to a target sheet value + content:
// Idle → PartiallyExpanded "Where to?"; ChooseRide → Expanded ride list;
// ConfirmPickup → a mid anchor + address/Confirm; EnRoute → driver card; Rate → rating+tip.
// On state change call sheetState.expand()/partialExpand() (or animateTo a custom anchor).
```

## 5. Navigation (No Tab Bar)

Lyft has **no `NavigationBar`**. The root is the map + `BottomSheetScaffold`; trip phases are state-driven. The floating menu button opens account/history as a separate destination (`navController.navigate(...)` to a full-screen route or a `ModalBottomSheet`), not a tab.

```kotlin
@Composable
fun MapIconButton(system: String, modifier: Modifier = Modifier, onClick: () -> Unit) {
    val icon = when (system) {
        "menu" -> Icons.Filled.Menu
        "back" -> Icons.Filled.ArrowBackIosNew
        "locate" -> Icons.Filled.MyLocation
        else -> Icons.Filled.Layers
    }
    Box(
        modifier
            .size(44.dp)
            .shadow(12.dp, CircleShape, spotColor = LyftColors.TextPrimary.copy(alpha = 0.16f))
            .clip(CircleShape)
            .background(MaterialTheme.colorScheme.surface)
            .clickable(onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Icon(icon, contentDescription = system, tint = MaterialTheme.colorScheme.onSurface,
            modifier = Modifier.size(20.dp))
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Sheet detent snap | `BottomSheetScaffold` default spring; programmatic `animateTo(spring(dampingRatio = 0.85f, stiffness = 220f))` for the slight overshoot |
| Ride-type select | `animateColorAsState` for bg/border ring; `HapticFeedbackType.TextHandleMove` |
| Pickup pin drop | `Animatable` 0 → 1 `spring(dampingRatio = 0.5f, stiffness = 200f)`; scale + offset; `HapticFeedbackType.LongPress` (~soft) |
| CTA press | `Modifier.scale` 1 → 0.98 via `collectIsPressedAsState()` |
| Map recenter | `cameraPositionState.animate(update, durationMs = 400)` |
| ETA bubble update | `AnimatedContent` cross-fade on the number; tabular figures prevent layout jump |

```kotlin
// Map recenter
LaunchedEffect(recenterTrigger) {
    cameraPositionState.animate(
        CameraUpdateFactory.newLatLngZoom(userLatLng, 16f), durationMs = 400)
}
```

Haptics: prefer `LocalHapticFeedback`. For the pickup-pin landing, `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) approximates iOS `.soft` impact.

## 7. Icons

Lyft ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Lyft's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Menu (map) | `line.3.horizontal` | `Icons.Filled.Menu` |
| Back / dismiss | `chevron.left` | `Icons.Filled.ArrowBackIosNew` |
| Recenter / locate | `location.fill` | `Icons.Filled.MyLocation` |
| Layers | `square.3.layers.3d` | `Icons.Filled.Layers` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Saved: Home | `house.fill` | `Icons.Filled.Home` |
| Saved: Work | `briefcase.fill` | `Icons.Filled.Work` |
| Rating star | `star.fill` | `Icons.Filled.Star` |
| Contact driver | `phone.fill` / `message.fill` | `Icons.Filled.Call` / `Icons.Filled.Message` |
| Share trip | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Safety | `shield.fill` | `Icons.Filled.Shield` |
| Where-to dot | (drawn pink square) | — |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `BottomSheetScaffold` + Maps Compose are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the map renders full-bleed behind the system bars. System-bar icon contrast must flip with the map's day/night tiles — use `WindowCompat` and update on theme change. Inset floating controls with `Modifier.windowInsetsPadding(WindowInsets.systemBars)`.
- **Font scaling**: `sp` honors the user's font scale — keep it on sheet titles, ride names, body. Pin badges and pin labels; the "Where to?" prompt scales to a max and never wraps.
- **TalkBack**: ride-type rows use `Modifier.semantics { role = Role.RadioButton; stateDescription = if (selected) "Selected" else "Not selected" }` and merge name/sub/price; the CTA announces "Select Lyft"; the map exposes a summary `contentDescription`.
- **Touch targets**: Material guidance is 48.dp minimum. The CTA (56) and ride rows (~72) clear it; floating icon buttons are 44 — bump to 48 if your audit requires, keeping the visual circle at 44 via inner padding.
- **Reduce Motion**: check `Settings.Global.ANIMATOR_DURATION_SCALE` (or accessibility manager) and swap sheet/pin springs for short tween animations.
- **Contrast**: `#6B6B7B` on white and `#A0A0B4` on `#11111F` pass WCAG AA at 14sp+. Lyft Pink `#FF00BF` on white passes AA for bold 17sp+ — keep the CTA bold and verify the pressed `#D500A0` too.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()`/`dynamicDarkColorScheme()` — Lyft Pink must stay fixed across both themes regardless of wallpaper.
