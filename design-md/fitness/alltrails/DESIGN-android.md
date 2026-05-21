# AllTrails (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports AllTrails' visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (AllTrails' topo-paper light space, the trail card, the difficulty color scale, map-first explore, the route trace) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas`/`drawPath` instead of a `CAShapeLayer`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for photos, the Maps Compose library for the map, and an Inter `FontFamily` in `res/font/`.

## 1. Color Tokens

```kotlin
// ui/theme/AllTrailsColors.kt
import androidx.compose.ui.graphics.Color

object AllTrailsColors {
    // Canvas & Surfaces
    val Canvas        = Color(0xFFFFFFFF)
    val SurfaceSage   = Color(0xFFF2F4F1)
    val SurfaceSunken = Color(0xFFEBEEE8)
    val Divider       = Color(0xFFE2E6DF)

    // Text
    val TextPrimary   = Color(0xFF1A1A1A)
    val TextSecondary = Color(0xFF6B6F68)
    val TextTertiary  = Color(0xFF9A9E96)

    // Brand
    val Green        = Color(0xFF428000)
    val GreenPressed = Color(0xFF336300)
    val GreenSoft    = Color(0xFFE8F0DF)

    // Difficulty scale (color = meaning)
    val Easy       = Color(0xFF428000)
    val Moderate   = Color(0xFFC77700)
    val Hard       = Color(0xFFB3261E)
    val EasyBg     = Color(0xFFE8F0DF)
    val ModerateBg = Color(0xFFFBEEDD)
    val HardBg     = Color(0xFFF7E0DE)

    // Semantic
    val StarGold = Color(0xFFF2A93B)
}

enum class Difficulty(val color: Color, val bg: Color, val label: String) {
    Easy(AllTrailsColors.Easy, AllTrailsColors.EasyBg, "EASY"),
    Moderate(AllTrailsColors.Moderate, AllTrailsColors.ModerateBg, "MODERATE"),
    Hard(AllTrailsColors.Hard, AllTrailsColors.HardBg, "HARD"),
}
```

Wire it into a Material 3 `lightColorScheme` — AllTrails is light-first. A dark scheme exists only for OS parity / night nav.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val AllTrailsScheme = lightColorScheme(
    primary        = AllTrailsColors.Green,
    onPrimary      = Color.White,
    background      = AllTrailsColors.Canvas,
    onBackground   = AllTrailsColors.TextPrimary,
    surface        = AllTrailsColors.SurfaceSage,
    onSurface      = AllTrailsColors.TextPrimary,
    surfaceVariant = AllTrailsColors.GreenSoft,
    outline        = AllTrailsColors.Divider,
    error          = AllTrailsColors.Hard,
)

@Composable
fun AllTrailsTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = AllTrailsScheme, typography = AllTrailsTypography, content = content)
```

## 2. Typography

Inter is open-source (SIL OFL). Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Enable tabular figures on stats and ratings.

```kotlin
// ui/theme/AllTrailsType.kt
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

private const val TNUM = "tnum" // tabular figures

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object AllTrailsText {
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 28.sp, letterSpacing = (-0.4).sp)
    val DetailTitle = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 24.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 20.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 25.sp)
    val StatValue   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 15.sp, fontFeatureSettings = TNUM)
    val Subtitle    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val DifficultyT = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 13.sp, letterSpacing = 0.3.sp)
    val StatLabel   = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, letterSpacing = 0.1.sp)
    val Rating      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 14.sp, fontFeatureSettings = TNUM)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, letterSpacing = 0.2.sp)
    val PinLabel    = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 12.sp)
    val LabelUpper  = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 12.sp, letterSpacing = 0.8.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val AllTrailsTypography = Typography(
    headlineLarge = AllTrailsText.ScreenTitle,
    headlineSmall = AllTrailsText.Section,
    titleMedium   = AllTrailsText.CardTitle,
    bodyLarge     = AllTrailsText.Body,
    labelSmall    = AllTrailsText.Tab,
)
```

## 3. Signature Components

### Difficulty Pill

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

@Composable
fun DifficultyPill(difficulty: Difficulty, solid: Boolean = false, modifier: Modifier = Modifier) {
    Box(
        modifier
            .clip(RoundedCornerShape(11.dp))
            .background(if (solid) difficulty.color else difficulty.bg)
            .padding(horizontal = 12.dp, vertical = 6.dp),
    ) {
        Text(
            difficulty.label,
            style = AllTrailsText.DifficultyT,
            color = if (solid) Color.White else difficulty.color,
        )
    }
}
```

### Trail Card (signature)

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.Star
import androidx.compose.material.icons.outlined.FavoriteBorder
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.scale
import androidx.compose.ui.layout.ContentScale

@Composable
fun TrailCard(
    name: String, location: String, difficulty: Difficulty,
    rating: Double, reviews: Int, length: String, time: String, elevation: String,
    photoUrl: String, saved: Boolean, onToggleSave: () -> Unit, onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "cardScale")

    Column(
        modifier
            .scale(scale)
            .clickable(interaction, indication = null, onClick = onClick),
        verticalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box {
            AsyncImage(
                model = photoUrl, contentDescription = null, contentScale = ContentScale.Crop,
                modifier = Modifier.fillMaxWidth().aspectRatio(4f / 3f)
                    .clip(RoundedCornerShape(12.dp)),
            )
            Box(
                Modifier.align(Alignment.TopEnd).padding(12.dp)
                    .size(34.dp).clip(CircleShape).background(Color.Black.copy(alpha = 0.25f))
                    .clickable(onClick = onToggleSave),
                contentAlignment = Alignment.Center,
            ) {
                Icon(
                    if (saved) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
                    contentDescription = if (saved) "Saved" else "Save",
                    tint = if (saved) AllTrailsColors.Green else Color.White,
                    modifier = Modifier.size(18.dp),
                )
            }
        }
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.CenterVertically) {
            DifficultyPill(difficulty)
            Row(verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Icon(Icons.Filled.Star, null, tint = AllTrailsColors.StarGold,
                    modifier = Modifier.size(12.dp))
                Text("%.1f".format(rating), style = AllTrailsText.Rating,
                    color = AllTrailsColors.TextPrimary)
                Text("($reviews)", style = AllTrailsText.Meta,
                    color = AllTrailsColors.TextSecondary)
            }
        }
        Text(name, style = AllTrailsText.CardTitle, color = AllTrailsColors.TextPrimary, maxLines = 1)
        Text(location, style = AllTrailsText.Subtitle, color = AllTrailsColors.TextSecondary)
        Row(verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Stat("Length", length); Dot(); Stat("Est.", time); Dot(); Stat("↑", elevation)
        }
    }
}

@Composable private fun Dot() =
    Text("·", color = AllTrailsColors.TextTertiary)

@Composable private fun Stat(label: String, value: String) {
    Row(verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp)) {
        Text(label, style = AllTrailsText.StatLabel, color = AllTrailsColors.TextSecondary)
        Text(value, style = AllTrailsText.StatValue, color = AllTrailsColors.TextPrimary)
    }
}
```

### Record Button (signature)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.material.icons.filled.FiberManualRecord
import androidx.compose.material.icons.filled.Stop
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun RecordButton(recording: Boolean, onToggle: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    val pulse by rememberInfiniteTransition(label = "rec").animateFloat(
        1f, 1.06f, infiniteRepeatable(tween(800), RepeatMode.Reverse), label = "pulse",
    )
    val tint = if (recording) AllTrailsColors.Hard else AllTrailsColors.Green

    Box(
        modifier
            .size(60.dp)
            .scale(if (recording) pulse else 1f)
            .shadow(18.dp, CircleShape, spotColor = tint.copy(alpha = 0.30f))
            .clip(CircleShape)
            .background(tint)
            .clickable(indication = null,
                interactionSource = remember { MutableInteractionSource() }) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                onToggle()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            if (recording) Icons.Filled.Stop else Icons.Filled.FiberManualRecord,
            contentDescription = if (recording) "Stop recording" else "Record",
            tint = Color.White, modifier = Modifier.size(26.dp),
        )
    }
}
```

### Route Trace (signature)

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.PathMeasure
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.StrokeJoin
import androidx.compose.ui.graphics.drawscope.Stroke

@Composable
fun RouteTrace(points: List<Offset>, modifier: Modifier = Modifier) {
    val progress = remember { Animatable(0f) }
    LaunchedEffect(Unit) {
        progress.animateTo(1f, tween(1000, easing = EaseOutCubic))
    }
    Canvas(modifier.fillMaxSize()) {
        if (points.size < 2) return@Canvas
        val full = Path().apply {
            moveTo(points.first().x, points.first().y)
            points.drop(1).forEach { lineTo(it.x, it.y) }
        }
        val measure = PathMeasure().apply { setPath(full, false) }
        val drawn = Path()
        measure.getSegment(0f, measure.length * progress.value, drawn, true)
        drawPath(
            drawn, AllTrailsColors.Green,
            style = Stroke(width = 4.dp.toPx(), cap = StrokeCap.Round, join = StrokeJoin.Round),
        )
    }
}
```

### Primary / Outline Buttons

```kotlin
@Composable
fun ATPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f,
        tween(200, easing = EaseOut), label = "btnScale")
    val haptics = LocalHapticFeedback.current
    Box(
        modifier
            .scale(scale)
            .clip(RoundedCornerShape(25.dp))
            .background(if (pressed) AllTrailsColors.GreenPressed else AllTrailsColors.Green)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            }
            .padding(horizontal = 28.dp, vertical = 14.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = AllTrailsText.Button, color = Color.White)
    }
}

@Composable
fun ATOutlineButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        modifier
            .clip(RoundedCornerShape(25.dp))
            .background(if (pressed) AllTrailsColors.GreenSoft else Color.Transparent)
            .border(1.5.dp, AllTrailsColors.Green, RoundedCornerShape(25.dp))
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 24.dp, vertical = 13.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = AllTrailsText.Button, color = AllTrailsColors.Green)
    }
}
```

## 4. Bottom Navigation (Tab Bar)

Material 3 `NavigationBar`. AllTrails' iOS tab bar is a near-opaque white `.regularMaterial`; Android has no first-class live blur, so use a 96%-opaque white surface with a sage hairline. **Active tint is AllTrails Green.**

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun AllTrailsBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = Color.White.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Explore"   to Icons.Filled.Map,
            "Saved"     to Icons.Filled.Bookmark,
            "Navigator" to Icons.Filled.NearMe,
            "Community" to Icons.Filled.Groups,
            "Profile"   to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = AllTrailsText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = AllTrailsColors.Green,
                    selectedTextColor   = AllTrailsColors.Green,
                    unselectedIconColor = AllTrailsColors.TextTertiary,
                    unselectedTextColor = AllTrailsColors.TextTertiary,
                    indicatorColor      = AllTrailsColors.GreenSoft,
                ),
            )
        }
    }
}
```

## 5. Motion

| Moment | Compose recipe |
|--------|----------------|
| Route trace draw | `Animatable` 0 → 1 `tween(1000, EaseOutCubic)`; `PathMeasure.getSegment` reveals the polyline |
| Map pin select | `animateFloatAsState` pin 1 → 1.2 + white ring; mini card `slideInVertically` over 300ms |
| Card tap | `animateFloatAsState` 1 → 0.98 `tween(200, EaseOut)`, `HapticFeedbackType.LongPress` |
| Map/List toggle | `animateFloatAsState` active-fill offset over `tween(250)` |
| Record start | color swap green → red, infinite `pulse` scale, firm haptic, banner `expandVertically` |
| Save heart | `Animatable` keyframes 1 → 1.15 → 1 over 300ms, `HapticFeedbackType.LongPress` |
| Live tracking | append GPS points to the path; the `Canvas` recomposes to extend the polyline; a `Hard` dot pulses |

Haptics: prefer `LocalHapticFeedback`. For richer control use `Vibrator` `VibrationEffect.createOneShot(12, ...)` to approximate iOS's medium impact on Record start/stop.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Record | `record.circle` | `Icons.Filled.FiberManualRecord` |
| Stop recording | `stop.fill` | `Icons.Filled.Stop` |
| Save | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Star (rating) | `star.fill` | `Icons.Filled.Star` |
| Directions | `arrow.triangle.turn.up.right.diamond.fill` | `Icons.Filled.Directions` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Elevation | `arrow.up.forward` | `Icons.Filled.TrendingUp` |
| Explore (tab) | `map.fill` | `Icons.Filled.Map` |
| Saved (tab) | `bookmark.fill` | `Icons.Filled.Bookmark` |
| Navigator (tab) | `location.north.circle.fill` | `Icons.Filled.NearMe` |
| Community (tab) | `person.2.fill` | `Icons.Filled.Groups` |
| Profile (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas`/`PathMeasure` and modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. Use the **Maps Compose** library for the Explore base map.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the white topo-paper canvas wants dark-content system bars. Apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the tab bar and Record FAB clear the gesture nav; let the map extend edge-to-edge under floating controls.
- **Tabular figures**: set `fontFeatureSettings = "tnum"` on stat/rating styles (already in `AllTrailsText`) so a scrolling trail list keeps its columns aligned; confirm the bundled Inter ships `tnum`.
- **Font scaling**: `sp` honors the user's font scale — keep it on titles, trail names, body; let the stat row wrap at large sizes; pin the difficulty pill, tab labels, and map-pin labels by wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Color is information, not the only signal**: the difficulty pill always carries the word (EASY/MODERATE/HARD); a legend decodes the map-pin tints. Never communicate difficulty by hue alone — required for color-blind safety.
- **TalkBack**: make the trail card one node with `Modifier.semantics(mergeDescendants = true)` and a full `contentDescription` ("Eagle Peak Trail, moderate, 4.7 stars, 1,284 reviews, 5.2 miles, 1,200 feet gain"); mark the save toggle as a separate button.
- **Reduce Motion**: gate the route-trace draw and pin-scale on `Settings.Global.ANIMATOR_DURATION_SCALE` — render the full route immediately when animations are off.
- **Outdoor legibility**: support the system "high contrast text" setting (bump `#6B6F68` toward `#54584F`); do not auto-dim — the bright high-contrast theme is intentional for in-sun reading.
- **Touch targets**: Material guidance is 48.dp minimum — the 60.dp Record button and 50.dp CTAs clear it; give the 18-20.dp save/share glyphs a `Modifier.size(48.dp)` hit area via padding; enlarge map-pin tap targets to ~44.dp even though the glyph is smaller.
- **Contrast**: `#6B6F68` on white passes AA at 14sp+; the Hard difficulty `#B3261E` on `#F7E0DE` and Moderate `#C77700` on `#FBEEDD` pass AA at 13sp bold — keep the pill bold.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — AllTrails' identity requires the fixed topo-paper canvas, brand green, and the exact difficulty scale regardless of wallpaper.
