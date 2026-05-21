# Garmin Connect (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Garmin Connect's data-cockpit visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the activity-detail screen (route map, stat grid, Training Status ring, Body Battery gauge, HR zones), navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (true-black canvas, single Garmin-Blue accent, condensed tabular numerals, glowing GPS route, fixed-meaning data-viz colors) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas` for the route + rings, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for any tile imagery. Garmin Connect is **dark-first**; a light scheme is provided but the dark palette is the default. **Do not** enable Material You dynamic color — Garmin's identity is the fixed true-black + single blue, and data-viz colors are semantic.

## 1. Color Tokens

```kotlin
// ui/theme/GCColors.kt
import androidx.compose.ui.graphics.Color

object GCColors {
    // Canvas & Surfaces (Dark — primary)
    val Canvas   = Color(0xFF000000)   // true black — the Garmin signature
    val Surface1 = Color(0xFF121417)
    val Surface2 = Color(0xFF1B1E22)
    val Surface3 = Color(0xFF23272C)
    val Divider  = Color(0xFF2A2E33)

    // Canvas & Surfaces (Light — optional)
    val LightCanvas  = Color(0xFFFFFFFF)
    val LightSurface = Color(0xFFF4F5F7)
    val LightDivider = Color(0xFFE2E5E9)

    // Text
    val TextPrimary     = Color(0xFFFFFFFF)
    val TextSecondary   = Color(0xFFA6ACB3)
    val TextTertiary    = Color(0xFF6B7178)
    val LightTextPrimary = Color(0xFF1A1D21)

    // Brand (single accent)
    val Blue        = Color(0xFF007CC3)   // fills (buttons, route)
    val BlueOnDark  = Color(0xFF2A9FD6)   // text / active on black (AA)
    val BluePressed = Color(0xFF00689F)

    // Functional / data-viz (fixed meaning — theme-invariant)
    val BodyBattery   = Color(0xFF2EA8E0)
    val BodyBatteryLo = Color(0xFF1E6FA8)
    val Intensity     = Color(0xFF00A8A8)
    val Steps         = Color(0xFFC8B560)
    val Zone1 = Color(0xFF9AA0A6)
    val Zone2 = Color(0xFF4CAF50)
    val Zone3 = Color(0xFFC8B560)
    val Zone4 = Color(0xFFF0A030)
    val Zone5 = Color(0xFFE5402A)

    // Semantic
    val Success = Color(0xFF4CAF50)
    val Warning = Color(0xFFF0A030)
    val Error   = Color(0xFFE5402A)
    val Track   = Color(0xFF23272C)
}
```

Wire it into both schemes. Garmin is dark-first; the dark scheme is the default.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val GCDark = darkColorScheme(
    primary        = GCColors.Blue,
    onPrimary      = GCColors.TextPrimary,
    background     = GCColors.Canvas,
    onBackground   = GCColors.TextPrimary,
    surface        = GCColors.Surface1,
    onSurface      = GCColors.TextPrimary,
    surfaceVariant = GCColors.Surface2,
    outline        = GCColors.Divider,
    error          = GCColors.Error,
)

private val GCLight = lightColorScheme(
    primary        = GCColors.Blue,
    onPrimary      = GCColors.TextPrimary,
    background     = GCColors.LightCanvas,
    onBackground   = GCColors.LightTextPrimary,
    surface        = GCColors.LightSurface,
    onSurface      = GCColors.LightTextPrimary,
    surfaceVariant = Color(0xFFEDEFF2),
    outline        = GCColors.LightDivider,
    error          = GCColors.Error,
)

@Composable
fun GCTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) GCDark else GCLight,   // dark is the intended default
    typography  = GCTypography,
    content     = content,
)
```

## 2. Typography

Garmin pairs **Roboto** (UI/body/labels) with **Roboto Condensed** (every meaningful number, tabular). Drop the TTFs in `res/font/`. Both Apache 2.0. The value is the visual hero; labels are small uppercase whispers.

```kotlin
// ui/theme/GCType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextGeometricTransform
import androidx.compose.ui.unit.sp

val Roboto = FontFamily(
    Font(R.font.roboto_regular, FontWeight.Normal),
    Font(R.font.roboto_medium,  FontWeight.Medium),
    Font(R.font.roboto_bold,    FontWeight.Bold),
    Font(R.font.roboto_black,   FontWeight.Black),
)
val RobotoCondensed = FontFamily(
    Font(R.font.roboto_condensed_regular, FontWeight.Normal),
    Font(R.font.roboto_condensed_bold,    FontWeight.Bold),
)

// Helper: every metric must use tabular figures
import androidx.compose.ui.text.font.FontFeatureSetting
private val TNUM = "tnum"

object GCText {
    // Roboto — UI / body / labels
    val ScreenTitle = TextStyle(Roboto, fontWeight = FontWeight.Black,  fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val Section     = TextStyle(Roboto, fontWeight = FontWeight.Bold,   fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Roboto, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 24.sp)
    val BodyRegular = TextStyle(Roboto, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 24.sp)
    val Meta        = TextStyle(Roboto, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 20.sp)
    val Eyebrow     = TextStyle(Roboto, fontWeight = FontWeight.Bold,   fontSize = 12.sp, lineHeight = 15.sp, letterSpacing = 0.5.sp)
    val Unit        = TextStyle(Roboto, fontWeight = FontWeight.Medium, fontSize = 11.sp, lineHeight = 13.sp)
    val Caption     = TextStyle(Roboto, fontWeight = FontWeight.Medium, fontSize = 11.sp, lineHeight = 14.sp)
    val Button      = TextStyle(Roboto, fontWeight = FontWeight.Bold,   fontSize = 15.sp, lineHeight = 15.sp, letterSpacing = 0.5.sp)
    val Tab         = TextStyle(Roboto, fontWeight = FontWeight.Medium, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Axis        = TextStyle(Roboto, fontWeight = FontWeight.Medium, fontSize = 10.sp, lineHeight = 10.sp)

    // Roboto Condensed — metrics (tabular via fontFeatureSettings)
    val HeroMetric   = TextStyle(RobotoCondensed, fontWeight = FontWeight.Bold, fontSize = 40.sp, lineHeight = 42.sp, letterSpacing = (-0.5).sp, fontFeatureSettings = TNUM)
    val ActivityName = TextStyle(RobotoCondensed, fontWeight = FontWeight.Bold, fontSize = 26.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp, fontFeatureSettings = TNUM)
    val StatValue    = TextStyle(RobotoCondensed, fontWeight = FontWeight.Bold, fontSize = 22.sp, lineHeight = 24.sp, fontFeatureSettings = TNUM)
    val StatValueLg  = TextStyle(RobotoCondensed, fontWeight = FontWeight.Bold, fontSize = 28.sp, lineHeight = 30.sp, fontFeatureSettings = TNUM)
}

// Map onto Material 3 slots
val GCTypography = Typography(
    headlineLarge = GCText.ScreenTitle,
    titleLarge    = GCText.Section,
    bodyLarge     = GCText.Body,
    labelSmall    = GCText.Tab,
)
```

## 3. Signature Components

### Activity Detail Header

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

@Composable
fun ActivityHeader(type: String, name: String, whenText: String) {
    Column(Modifier.fillMaxWidth().padding(horizontal = 16.dp).padding(bottom = 16.dp)) {
        Text(type.uppercase(), style = GCText.Eyebrow, color = GCColors.BlueOnDark)
        Text(name, style = GCText.ActivityName, color = GCColors.TextPrimary, modifier = Modifier.padding(top = 4.dp))
        Text(whenText, style = GCText.Meta, color = GCColors.TextSecondary, modifier = Modifier.padding(top = 4.dp))
    }
}
```

### GPS Route Map (Canvas)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.runtime.*
import androidx.compose.ui.draw.clip
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.*
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.foundation.shape.RoundedCornerShape

@Composable
fun RouteMap(points: List<Offset>, modifier: Modifier = Modifier) {
    val draw = remember { Animatable(0f) }
    LaunchedEffect(Unit) { draw.animateTo(1f, tween(700, easing = LinearOutSlowInEasing)) }

    Box(
        modifier
            .fillMaxWidth()
            .height(168.dp)
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(12.dp))
            .background(Brush.linearGradient(listOf(Color(0xFF1A2329), Color(0xFF0C1216)))),
    ) {
        Canvas(Modifier.fillMaxSize()) {
            if (points.size < 2) return@Canvas
            val pts = points.map { Offset(it.x * size.width, it.y * size.height) }
            val path = Path().apply {
                moveTo(pts.first().x, pts.first().y)
                pts.drop(1).forEach { lineTo(it.x, it.y) }
            }
            val measure = PathMeasure().apply { setPath(path, false) }
            val partial = Path()
            measure.getSegment(0f, measure.length * draw.value, partial, true)
            // soft glow
            drawPath(partial, GCColors.Blue.copy(alpha = 0.5f),
                style = Stroke(width = 10.dp.toPx(), cap = StrokeCap.Round, join = StrokeJoin.Round))
            drawPath(partial, GCColors.Blue,
                style = Stroke(width = 4.dp.toPx(), cap = StrokeCap.Round, join = StrokeJoin.Round))
            // pins
            drawCircle(Color.White, 9.dp.toPx(), pts.first())
            drawCircle(GCColors.Success, 7.dp.toPx(), pts.first())
            drawCircle(Color.White, 9.dp.toPx(), pts.last())
            drawCircle(GCColors.Error, 7.dp.toPx(), pts.last())
        }
    }
}
```

### Stat Grid

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.grid.GridCells
import androidx.compose.foundation.lazy.grid.LazyVerticalGrid
import androidx.compose.foundation.lazy.grid.items

data class Stat(val label: String, val value: String, val unit: String = "")

@Composable
fun StatGrid(stats: List<Stat>) {
    Box(
        Modifier
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(12.dp))
            .background(GCColors.Divider),
    ) {
        LazyVerticalGrid(
            columns = GridCells.Fixed(3),
            userScrollEnabled = false,
            horizontalArrangement = Arrangement.spacedBy(1.dp),
            verticalArrangement   = Arrangement.spacedBy(1.dp),
            modifier = Modifier.heightIn(max = 1000.dp),
        ) {
            items(stats) { s ->
                Column(
                    Modifier
                        .fillMaxWidth()
                        .defaultMinSize(minHeight = 64.dp)
                        .background(GCColors.Surface1)
                        .padding(vertical = 14.dp, horizontal = 12.dp),
                ) {
                    Text(s.label.uppercase(), style = GCText.Eyebrow, color = GCColors.TextSecondary)
                    Row(verticalAlignment = Alignment.Bottom) {
                        Text(s.value, style = GCText.StatValue, color = GCColors.TextPrimary)
                        if (s.unit.isNotEmpty())
                            Text(" ${s.unit}", style = GCText.Unit, color = GCColors.TextTertiary)
                    }
                }
            }
        }
    }
}
```

### Training Status Ring + Body Battery Gauge

```kotlin
@Composable
fun TrainingStatusCard(progress: Float, status: String, detail: String) {
    val p = remember { Animatable(0f) }
    LaunchedEffect(Unit) {
        p.animateTo(progress, spring(dampingRatio = 0.85f, stiffness = Spring.StiffnessMediumLow))
    }
    Row(
        Modifier
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(12.dp))
            .background(GCColors.Surface1)
            .then(borderModifier())
            .padding(16.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Canvas(Modifier.size(52.dp)) {
            val sw = 6.dp.toPx()
            drawArc(GCColors.Track, 0f, 360f, false, style = Stroke(sw))
            drawArc(GCColors.Blue, -90f, 360f * p.value, false,
                style = Stroke(sw, cap = StrokeCap.Round))
        }
        Column(Modifier.padding(start = 14.dp)) {
            Text("TRAINING STATUS", style = GCText.Eyebrow, color = GCColors.TextSecondary)
            Text(status, style = GCText.Body.copy(fontWeight = FontWeight.Bold), color = GCColors.BlueOnDark)
            Text(detail, style = GCText.Unit, color = GCColors.TextSecondary)
        }
    }
}

@Composable
fun BodyBatteryCard(value: Int) {
    val w = remember { Animatable(0f) }
    LaunchedEffect(Unit) { w.animateTo(value / 100f, tween(600, easing = LinearOutSlowInEasing)) }
    Column(
        Modifier
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(12.dp))
            .background(GCColors.Surface1)
            .then(borderModifier())
            .padding(16.dp),
    ) {
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.Bottom) {
            Text("Body Battery", style = GCText.Body.copy(fontSize = 13.sp), color = GCColors.TextPrimary)
            Text("$value", style = GCText.StatValue, color = GCColors.BodyBattery)
        }
        Box(
            Modifier
                .padding(top = 12.dp)
                .fillMaxWidth()
                .height(8.dp)
                .clip(RoundedCornerShape(4.dp))
                .background(GCColors.Track),
        ) {
            Box(
                Modifier
                    .fillMaxWidth(w.value)
                    .fillMaxHeight()
                    .clip(RoundedCornerShape(4.dp))
                    .background(Brush.horizontalGradient(listOf(GCColors.BodyBatteryLo, GCColors.BodyBattery))),
            )
        }
        Row(Modifier.fillMaxWidth().padding(top = 6.dp), horizontalArrangement = Arrangement.SpaceBetween) {
            Text("0", style = GCText.Axis, color = GCColors.TextTertiary)
            Text("Charged", style = GCText.Axis, color = GCColors.TextTertiary)
            Text("100", style = GCText.Axis, color = GCColors.TextTertiary)
        }
    }
}

import androidx.compose.foundation.border
@Composable
private fun borderModifier(): Modifier = Modifier.border(1.dp, GCColors.Divider, RoundedCornerShape(12.dp))
```

### Heart Rate Zones

```kotlin
data class HRZone(val name: String, val frac: Float, val dur: String, val color: Color)

@Composable
fun HRZonesCard(
    zones: List<HRZone> = listOf(
        HRZone("Z2", 0.26f, "9:48",  GCColors.Zone2),
        HRZone("Z3", 0.54f, "22:30", GCColors.Zone3),
        HRZone("Z4", 0.38f, "8:12",  GCColors.Zone4),
        HRZone("Z5", 0.10f, "1:48",  GCColors.Zone5),
    ),
) {
    Column(
        Modifier
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(12.dp))
            .background(GCColors.Surface1)
            .then(borderModifier())
            .padding(16.dp),
    ) {
        Text("Heart Rate Zones", style = GCText.Body.copy(fontSize = 13.sp),
            color = GCColors.TextPrimary, modifier = Modifier.padding(bottom = 12.dp))
        zones.forEach { z ->
            Row(verticalAlignment = Alignment.CenterVertically,
                modifier = Modifier.padding(bottom = 8.dp)) {
                Text(z.name, style = GCText.Caption, color = GCColors.TextSecondary,
                    modifier = Modifier.width(30.dp))
                Box(
                    Modifier
                        .weight(1f)
                        .height(10.dp)
                        .clip(RoundedCornerShape(3.dp))
                        .background(GCColors.Track),
                ) {
                    Box(
                        Modifier
                            .fillMaxWidth(z.frac)
                            .fillMaxHeight()
                            .clip(RoundedCornerShape(3.dp))
                            .background(z.color),
                    )
                }
                Text(z.dur, style = GCText.Caption, color = GCColors.TextSecondary,
                    modifier = Modifier.width(40.dp), textAlign = androidx.compose.ui.text.style.TextAlign.End)
            }
        }
    }
}
```

## 4. Navigation

Garmin has minimal chrome: a 4-tab bottom strip and a back-style top app bar. No tint pill — active is just the brightened Garmin Blue.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*

@Composable
fun GCBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = GCColors.Surface1, tonalElevation = 0.dp) {
        val items = listOf(
            "My Day"     to Icons.Filled.RadioButtonChecked,
            "Challenges" to Icons.Filled.TrendingUp,
            "Calendar"   to Icons.Filled.CalendarMonth,
            "More"       to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = GCText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = GCColors.BlueOnDark,
                    selectedTextColor   = GCColors.BlueOnDark,
                    unselectedIconColor = GCColors.TextTertiary,
                    unselectedTextColor = GCColors.TextTertiary,
                    indicatorColor      = Color.Transparent,   // no Material pill — Garmin has none
                ),
            )
        }
    }
}

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun GCActivityTopBar(parent: String, onBack: () -> Unit) {
    TopAppBar(
        title = {},
        navigationIcon = {
            Row(verticalAlignment = Alignment.CenterVertically,
                modifier = Modifier.padding(start = 8.dp)) {
                Icon(Icons.Filled.ChevronLeft, contentDescription = "Back",
                    tint = GCColors.TextPrimary, modifier = Modifier.size(18.dp))
                Text(parent, style = GCText.Body, color = GCColors.TextPrimary)
            }
        },
        actions = {
            Icon(Icons.Filled.Settings, "Settings", tint = GCColors.TextPrimary,
                modifier = Modifier.size(19.dp).padding(end = 16.dp))
            Icon(Icons.Filled.IosShare, "Share", tint = GCColors.TextPrimary,
                modifier = Modifier.size(19.dp).padding(end = 16.dp))
        },
        colors = TopAppBarDefaults.topAppBarColors(containerColor = GCColors.Canvas),
    )
}
```

## 5. Motion

Garmin motion is purposeful — the route draws on, rings/bars fill from 0, stat cells stagger-rise; quiet otherwise. 150–700ms ease-out.

| Moment | Compose recipe |
|--------|----------------|
| Route line draws on | `PathMeasure.getSegment` + `Animatable` 0→1 `tween(700, LinearOutSlowInEasing)` |
| Ring / gauge fill | `Animatable` 0→progress `spring(dampingRatio = 0.85f)` (slight overshoot) |
| Bar fill | `Animatable` 0→frac `tween(600, LinearOutSlowInEasing)` |
| Stat cells reveal | `AnimatedVisibility` per cell `fadeIn() + slideInVertically { 6.dp }`, 30ms stagger |
| Card expand (Splits/HR) | chevron `animateFloatAsState` 0→90° `tween(150)`; body `expandVertically(tween(220))` |
| Tab switch | `Crossfade(tween(150))`; glyph color tween 120ms |
| Pull to sync | `PullToRefreshContainer`; success → `Snackbar` "Synced" auto-dismiss |
| Live value tick | `animateIntAsState` with tabular figures so width is stable |

```kotlin
// Canonical Garmin motion — ring filling from zero
val p = remember { Animatable(0f) }
LaunchedEffect(target) {
    p.animateTo(target, spring(dampingRatio = 0.85f, stiffness = Spring.StiffnessMediumLow))
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` on goal-met and tab change (soft impact analog); a `View.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` on segmented-range change; success haptic on sync complete. Auto-sync that succeeds silently shows only a "Synced" snackbar.

## 6. Icons

Garmin ships custom activity glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| My Day (tab) | `circle.circle.fill` | `Icons.Filled.RadioButtonChecked` |
| Challenges (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Calendar (tab) | `calendar` | `Icons.Filled.CalendarMonth` |
| More (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Back | `chevron.left` | `Icons.Filled.ChevronLeft` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Expand card | `chevron.right` / `chevron.down` | `Icons.Filled.ChevronRight` (rotate 90°) |
| Heart rate | `heart.fill` | `Icons.Filled.Favorite` |
| Run | `figure.run` | `Icons.Filled.DirectionsRun` |
| Cycling | `figure.outdoor.cycle` | `Icons.Filled.DirectionsBike` |
| Elevation | `mountain.2.fill` | `Icons.Filled.Terrain` |
| Body Battery | `bolt.fill` | `Icons.Filled.Bolt` |
| Steps | `shoeprints.fill` | `Icons.Filled.DirectionsWalk` |
| Map / route | `map` | `Icons.Filled.Map` |
| Sync | `arrow.triangle.2.circlepath` | `Icons.Filled.Sync` |
| Goal met | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas`/`PathMeasure` + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; true-black canvas wants light-content system bars in dark mode. The bottom button respects the navigation bar inset via `Modifier.navigationBarsPadding()`; the activity top bar respects the camera cutout.
- **Tabular numerals**: every metric `TextStyle` sets `fontFeatureSettings = "tnum"` so split tables and live values don't shift. Do **not** substitute a proportional font for Roboto Condensed numbers.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, section headings, body, meta. Pin layout-sensitive text (stat values, units, 10sp tab labels, chart axis, eyebrow labels) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: merge label + value + unit into one node via `Modifier.semantics(mergeDescendants = true) { contentDescription = "Average pace 5 minutes 2 seconds per kilometre" }`; HR-zone rows announced "Zone 4 threshold, 8 minutes 12 seconds"; expose the route `Canvas` with `contentDescription = "Route, 8.42 kilometres, Cedar Loop Trail"`.
- **Color is never the only signal**: HR-zone bars keep a Z2…Z5 text label; goal-met pairs `#4CAF50` with a check icon.
- **Touch targets**: Material guidance is 48.dp — give the tab icons, card-expand chevrons, and stat cells ≥ 48.dp hit areas (stat cells are full-cell tappable, ≥ 64.dp tall); primary buttons ≥ 48.dp.
- **Contrast**: `#FFFFFF` on `#000000` is maximal; `#2A9FD6` on `#000000` passes WCAG AA — never render small text in raw `#007CC3` on black.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, skip the route draw-on (draw full path), set rings/bars to final value via a `Crossfade`, and drop the stat-cell stagger.
- **Dark mode**: dark is the default; surfaces step `#000000 → #121417 → #1B1E22`; floating layers (sheets/menus) get `#1B1E22` + a soft `0.6` black shadow. **Do not** enable Material You `dynamicColorScheme()` — Garmin's true-black + single-blue identity and semantic data-viz colors must hold regardless of wallpaper.
