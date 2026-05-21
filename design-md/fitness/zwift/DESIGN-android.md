# Zwift (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Zwift's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the rider HUD over a 3D world, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Zwift's full-bleed 3D world, glassmorphic overlays, the fixed metric color semantics, tall condensed numerals, the Power-Up game affordance) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `HazeChild`/blur-backed surface instead of `.ultraThinMaterial`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 26` (RenderEffect blur is `API 31+`; below that, fall back to a translucent scrim), Material 3. Zwift is effectively always-dark — no light scheme. Do **not** enable Material You dynamic color — the fixed metric semantics and Zwift Orange must hold regardless of wallpaper.

## 1. Color Tokens

```kotlin
// ui/theme/ZwiftColors.kt
import androidx.compose.ui.graphics.Color

object ZwiftColors {
    // Brand / action
    val Orange        = Color(0xFFFC6719)
    val OrangePressed = Color(0xFFD9530E)
    val OrangeSoft    = Color(0xFF3A2110)

    // Ride metric semantics (FIXED — do not remap)
    val Power   = Color(0xFFFC6719) // power = orange (hero metric)
    val Cadence = Color(0xFFE8C547)
    val Heart   = Color(0xFFF0413E)
    val Wkg     = Color(0xFF2BD4D9)

    // App-shell surfaces (solid, off-ride)
    val Canvas   = Color(0xFF161616)
    val Card     = Color(0xFF1F1F1F)
    val Surface2 = Color(0xFF2A2A2A)
    val Surface3 = Color(0xFF353535)
    val Divider  = Color(0xFF303030)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFA8AAAD)
    val TextTertiary  = Color(0xFF6C6E72)

    // Semantic
    val Success = Color(0xFF57C84D)

    // Glass tints (layer over a blurred surface)
    val Glass       = Color(0xFF0C0C0C).copy(alpha = 0.60f)
    val GlassHeavy  = Color(0xFF121212).copy(alpha = 0.92f)
    val GlassStroke = Color(0xFFFFFFFF).copy(alpha = 0.10f)
    val GlassButton = Color(0xFFFFFFFF).copy(alpha = 0.10f)
}
```

Zwift is always-dark; there is no light scheme. Wire the off-ride shell:

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme

private val ZwiftDark = darkColorScheme(
    primary        = ZwiftColors.Orange,
    onPrimary      = Color(0xFFFFFFFF),
    background     = ZwiftColors.Canvas,
    onBackground   = ZwiftColors.TextPrimary,
    surface        = ZwiftColors.Card,
    onSurface      = ZwiftColors.TextPrimary,
    surfaceVariant = ZwiftColors.Surface2,
    outline        = ZwiftColors.Divider,
    error          = ZwiftColors.Heart,
)

@Composable
fun ZwiftTheme(content: @Composable () -> Unit) = MaterialTheme(
    colorScheme = ZwiftDark,          // always dark — no light scheme
    typography = ZwiftTypography,
    content = content,
)
```

## 2. Typography

Two faces: **Barlow Semi Condensed** (the closest free analog to Zwift's condensed numerics) for numbers/titles/buttons, **Inter** for body/lists. Drop both in `res/font/`. Every metric style sets a tabular-figures `FontFeatureSetting`.

```kotlin
// ui/theme/ZwiftType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Barlow = FontFamily(
    Font(R.font.barlow_semi_condensed_bold,      FontWeight.Bold),
    Font(R.font.barlow_semi_condensed_extrabold, FontWeight.ExtraBold),
)
val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
    Font(R.font.inter_bold,     FontWeight.Bold),
)

private const val TNUM = "tnum"   // tabular figures — HUD must not jump

object ZwiftText {
    // Display / numeric — Barlow Semi Condensed
    val HUD         = TextStyle(Barlow, fontWeight = FontWeight.ExtraBold, fontSize = 34.sp, lineHeight = 34.sp, letterSpacing = (-0.3).sp, fontFeatureSettings = TNUM)
    val HUDCompact  = TextStyle(Barlow, fontWeight = FontWeight.ExtraBold, fontSize = 28.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp, fontFeatureSettings = TNUM)
    val ScreenTitle = TextStyle(Barlow, fontWeight = FontWeight.ExtraBold, fontSize = 28.sp, lineHeight = 31.sp, letterSpacing = 0.4.sp)
    val Section     = TextStyle(Barlow, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = 0.2.sp)
    val CardTitle   = TextStyle(Barlow, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = 0.2.sp)
    val Unit        = TextStyle(Barlow, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 12.sp, letterSpacing = 0.6.sp)
    val Button      = TextStyle(Barlow, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.3.sp)
    val Badge       = TextStyle(Barlow, fontWeight = FontWeight.ExtraBold, fontSize = 11.sp, lineHeight = 12.sp, letterSpacing = 0.4.sp)
    // Body / UI — Inter
    val Body        = TextStyle(Inter,  fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val ListRow     = TextStyle(Inter,  fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Caption     = TextStyle(Inter,  fontWeight = FontWeight.Medium,    fontSize = 13.sp, lineHeight = 18.sp)
    val Tab         = TextStyle(Inter,  fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val ZwiftTypography = Typography(
    headlineLarge = ZwiftText.ScreenTitle,
    headlineMedium = ZwiftText.Section,
    titleMedium   = ZwiftText.CardTitle,
    bodyMedium    = ZwiftText.Body,
    labelSmall    = ZwiftText.Tab,
)
```

## 3. Signature Components

### Glass surface modifier

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

// API 31+: blur the content behind via a HazeChild or RenderEffect on the parent.
// This modifier supplies the tint + stroke that complete the glass look.
fun Modifier.zwiftGlass(radius: Int = 12, heavy: Boolean = false) = this
    .clip(RoundedCornerShape(radius.dp))
    .background(if (heavy) ZwiftColors.GlassHeavy else ZwiftColors.Glass)
    .border(1.dp, ZwiftColors.GlassStroke, RoundedCornerShape(radius.dp))
```

### HUD Metric Tile (the core atom)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.graphics.Color
import kotlin.math.roundToInt

enum class RideMetric(val color: Color, val unit: String, val dp: Int) {
    Power(ZwiftColors.Power, "WATTS", 0),
    Cadence(ZwiftColors.Cadence, "RPM", 0),
    Heart(ZwiftColors.Heart, "BPM", 0),
    Wkg(ZwiftColors.Wkg, "W/KG", 1),
}

@Composable
fun HUDTile(metric: RideMetric, value: Float) {
    // Interpolate so the number ticks, never jumps
    val shown by animateFloatAsState(value, tween(200), label = "hud-${metric.name}")
    Column(
        Modifier.widthIn(min = 88.dp).zwiftGlass(12).padding(vertical = 10.dp, horizontal = 8.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Text(
            if (metric.dp == 1) String.format("%.1f", shown) else shown.roundToInt().toString(),
            style = ZwiftText.HUD, color = metric.color,
        )
        Text(metric.unit, style = ZwiftText.Unit, color = ZwiftColors.TextSecondary)
    }
}

@Composable
fun RiderHUD(power: Float, cadence: Float, heart: Float) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 14.dp),
        horizontalArrangement = Arrangement.SpaceAround,
    ) {
        HUDTile(RideMetric.Power, power)
        HUDTile(RideMetric.Cadence, cadence)
        HUDTile(RideMetric.Heart, heart)
    }
}
```

### Route Banner

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Flag
import androidx.compose.material3.Icon

@Composable
fun RouteBanner(route: String, sub: String, progress: Float) {
    Column(Modifier.fillMaxWidth().padding(horizontal = 14.dp).zwiftGlass(12).padding(14.dp),
           verticalArrangement = Arrangement.spacedBy(8.dp)) {
        Row(verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(10.dp)) {
            Box(Modifier.size(30.dp).clip(RoundedCornerShape(8.dp)).background(ZwiftColors.Orange),
                contentAlignment = Alignment.Center) {
                Icon(Icons.Filled.Flag, null, tint = Color.White, modifier = Modifier.size(16.dp))
            }
            Column(Modifier.weight(1f)) {
                Text(route, style = ZwiftText.CardTitle, color = Color.White)
                Text(sub, style = ZwiftText.Caption, color = ZwiftColors.TextSecondary)
            }
        }
        Box(Modifier.fillMaxWidth().height(4.dp).clip(RoundedCornerShape(2.dp))
            .background(Color.White.copy(alpha = 0.18f))) {
            Box(Modifier.fillMaxHeight().fillMaxWidth(progress).background(ZwiftColors.Orange))
        }
    }
}
```

### Power-Up Tile

```kotlin
import androidx.compose.animation.core.Spring
import androidx.compose.animation.core.spring
import androidx.compose.material.icons.filled.Bolt
import androidx.compose.runtime.*
import androidx.compose.ui.graphics.graphicsLayer
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun PowerUpTile(armed: Boolean, onDeploy: () -> Unit) {
    val scale by animateFloatAsState(
        if (armed) 1f else 0.8f,
        spring(dampingRatio = Spring.DampingRatioMediumBouncy, stiffness = Spring.StiffnessHigh),
        label = "powerup",
    )
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(armed) { if (armed) haptics.performHapticFeedback(HapticFeedbackType.LongPress) }

    Box(
        Modifier.size(56.dp).graphicsLayer { scaleX = scale; scaleY = scale }
            .clip(RoundedCornerShape(10.dp))
            .background(if (armed) ZwiftColors.Orange else ZwiftColors.GlassButton)
            .border(1.dp, ZwiftColors.GlassStroke, RoundedCornerShape(10.dp))
            .clickable(enabled = armed) { onDeploy() },
        contentAlignment = Alignment.Center,
    ) {
        Icon(Icons.Filled.Bolt, "Power-up", tint = Color.White, modifier = Modifier.size(24.dp))
    }
}
```

### Achievement Burst

```kotlin
import androidx.compose.foundation.shape.CircleShape

@Composable
fun AchievementBurst(label: String) {
    var shown by remember { mutableStateOf(false) }
    val scale by animateFloatAsState(
        if (shown) 1f else 0.8f,
        spring(dampingRatio = Spring.DampingRatioMediumBouncy, stiffness = Spring.StiffnessLow),
        label = "achv",
    )
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(Unit) { shown = true; haptics.performHapticFeedback(HapticFeedbackType.LongPress) }

    Box(
        Modifier.graphicsLayer { scaleX = scale; scaleY = scale; alpha = if (shown) 1f else 0f }
            .clip(CircleShape).background(ZwiftColors.Success)
            .padding(horizontal = 18.dp, vertical = 8.dp),
    ) { Text(label.uppercase(), style = ZwiftText.Badge, color = Color.White) }
}
```

### Primary CTA

```kotlin
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults

@Composable
fun ZwiftPrimaryButton(title: String, onClick: () -> Unit) {
    Button(
        onClick = onClick,
        shape = RoundedCornerShape(10.dp),
        colors = ButtonDefaults.buttonColors(containerColor = ZwiftColors.Orange),
        contentPadding = PaddingValues(horizontal = 30.dp, vertical = 14.dp),
        modifier = Modifier.fillMaxWidth(),
    ) { Text(title.uppercase(), style = ZwiftText.Button, color = Color.White) }
}
```

## 4. Navigation

In-ride is a game-control surface; off-ride is a standard `NavigationBar`. No tint pill — active is Zwift Orange.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun ZwiftBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = ZwiftColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Workouts" to Icons.Filled.DirectionsBike,
            "Events"   to Icons.Filled.CalendarMonth,
            "Clubs"    to Icons.Filled.Groups,
            "Profile"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = ZwiftText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = ZwiftColors.Orange,
                    selectedTextColor = ZwiftColors.Orange,
                    unselectedIconColor = ZwiftColors.TextTertiary,
                    unselectedTextColor = ZwiftColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill
                ),
            )
        }
    }
}
```

The **in-ride action bar** is a `zwiftGlass(heavy = true)` row of game controls (Action / Power-Up / Workout / Riders / Menu) overlaid on the rendered 3D scene — not page navigation. The 3D world itself is drawn by the engine (a `SurfaceView` / `GLSurfaceView`) behind the Compose overlay.

## 5. Motion

Zwift overlay motion is quiet (200–300ms); the *world's* motion is the rendered 3D scene.

| Moment | Compose recipe |
|--------|----------------|
| HUD metric tick | `animateFloatAsState(value, tween(200))` — interpolate, never jump |
| Power-Up collected | `animateFloatAsState` 0.8→1.0 `spring(MediumBouncy, High)` + glow; medium haptic |
| Power-Up deployed | scale back + a world VFX (engine-side) |
| Achievement burst | `animateFloatAsState` 0.8→1.0 `spring(MediumBouncy, Low)` + alpha; success haptic; orange confetti |
| Ride-On burst | `AnimatedVisibility` `fadeIn/fadeOut` ~600ms + soft haptic |
| Route progress | `Box` fill width animates linearly with distance |
| Overlay show/hide | `AnimatedVisibility` `fadeIn + slideInVertically(8.dp)` `tween(200)` |
| App-shell nav | Nav3/`NavHost` slide push `tween(300)` |

```kotlin
// HUD metric tick — the canonical Zwift overlay motion
val shown by animateFloatAsState(value, tween(200), label = "hud")
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the power-up collect and the achievement; for Ride-On / lap use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The 3D world is engine-rendered; these are for overlays + app shell.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Workouts (tab) | `figure.indoor.cycle` | `Icons.Filled.DirectionsBike` |
| Events (tab) | `calendar` | `Icons.Filled.CalendarMonth` |
| Clubs (tab) | `person.3.fill` | `Icons.Filled.Groups` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Route flag | `flag.fill` | `Icons.Filled.Flag` |
| Power-Up (generic) | `bolt.fill` | `Icons.Filled.Bolt` |
| Power-Up: light | `feather` | `Icons.Filled.Air` |
| Action (U-turn) | `arrow.uturn.left` | `Icons.Filled.UTurnLeft` |
| Workout | `stopwatch` | `Icons.Filled.Timer` |
| Riders nearby | `person.2.fill` | `Icons.Filled.Groups` |
| Menu | `line.3.horizontal` | `Icons.Filled.Menu` |
| Heart-rate | `heart.fill` | `Icons.Filled.Favorite` |
| Power | `bolt.fill` | `Icons.Filled.Bolt` |
| Elevation / climb | `mountain.2.fill` | `Icons.Filled.Terrain` |
| Ride On (kudos) | `hand.thumbsup.fill` | `Icons.Filled.ThumbUp` |
| Map | `map.fill` | `Icons.Filled.Map` |
| Camera angle | `camera.fill` | `Icons.Filled.Videocam` |
| Achievement | `rosette` | `Icons.Filled.MilitaryTech` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 26** recommended (Compose floor is 21; `RenderEffect` blur for true glass is `API 31+` — below 31, fall back to a solid `GlassHeavy` scrim instead of blur). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the world bleeds full-screen behind transparent system bars (light-content). The route banner must clear the camera cutout; the action bar respects the navigation bar — inset the *overlays*, not the world.
- **Tabular figures**: every metric `TextStyle` sets `fontFeatureSettings = "tnum"`; interpolate value changes with `animateFloatAsState(tween(200))` so the HUD ticks smoothly and never jumps.
- **Font scaling**: `sp` honors the user's font scale — keep it on body/lists/captions (Inter). Pin HUD metrics, unit labels, screen titles, button labels, and tab labels by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` — glance-legibility and glass-tile layout depend on fixed sizing.
- **TalkBack**: label HUD tiles "Power, 248 watts" / "Heart rate, 156 BPM" (value + metric name); the Power-Up is a button labelled with its type + armed state; announce achievements with `LiveRegionMode.Assertive`.
- **Touch targets**: Material guidance is 48.dp. HUD tiles are large by nature; give the 56.dp Power-Up and action-bar items a ≥48.dp hit area; app-shell rows ≥48.dp.
- **Contrast**: white on the glass tiles passes AA because the glass darkens the world beneath; each metric color is paired with a unit label + glyph so color is never the sole differentiator.
- **Reduce transparency / blur unavailable**: when `RenderEffect` blur is unavailable (< API 31) or the user reduces transparency, swap glass to a solid `GlassHeavy` (`rgba(18,18,18,0.92+)`) so overlays stay legible.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the power-up pop, achievement spring, and confetti (use a `Crossfade`); keep the smooth metric tick and route-progress fill.
- **Orientation**: in-ride is landscape-preferred (the trainer layout) — lock the ride Activity/screen to landscape; the app shell is portrait-primary.
- **Always dark**: there is no light scheme. Do **not** enable `dynamicColorScheme()` — the fixed metric semantics and Zwift Orange identity must hold regardless of wallpaper.
