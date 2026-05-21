# Citymapper (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Citymapper's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the mode-color system, the leg strip, the GO button, GO trip mode, and the departures board.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Citymapper's calm canvas, the loud per-mode colors, the leg strip, the shape/color-locked GO pill) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `ModalBottomSheet` for the route-list-over-map, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. Citymapper's mode palette is fixed (no Material You — a transit map must read identically regardless of wallpaper). A full dark scheme is provided; the mode colors and GO green are theme-invariant.

## 1. Color Tokens

```kotlin
// ui/theme/CMColors.kt
import androidx.compose.ui.graphics.Color

object CMColors {
    // Canvas & Surfaces (Light)
    val Canvas    = Color(0xFFFFFFFF)
    val Surface1  = Color(0xFFF4F5F8)
    val Surface2  = Color(0xFFE8EAF0)
    val Divider   = Color(0xFFE3E5EC)

    // Canvas & Surfaces (Dark) — deep blue-black, NOT pure black
    val DarkCanvas   = Color(0xFF0C0E14)
    val DarkSurface1 = Color(0xFF15171F)
    val DarkSurface2 = Color(0xFF1E212B)
    val DarkDivider  = Color(0xFF282C38)

    // Text
    val TextPrimary   = Color(0xFF10131A)
    val TextSecondary = Color(0xFF5A6473)
    val TextTertiary  = Color(0xFF8A93A3)
    val DarkTextPrim  = Color(0xFFECEEF3)
    val DarkTextSec   = Color(0xFF98A0AE)

    // Brand
    val Blue        = Color(0xFF2B5BFF)
    val BlueBright  = Color(0xFF4D7BFF)
    val BluePressed = Color(0xFF1E45CC)
    val GoGreen     = Color(0xFF00C281) // GO button only — never generic UI

    // Transit mode colors (theme-invariant — the brand)
    val ModeWalk  = Color(0xFF00B894)
    val ModeBus   = Color(0xFFE8453C)
    val ModeTube  = Color(0xFF2B5BFF)
    val ModeRail  = Color(0xFF8E44D8)
    val ModeBike  = Color(0xFF00A8C5)
    val ModeCab   = Color(0xFFFFB400)
    val ModeFerry = Color(0xFF0094C6)

    // Semantic
    val Disruption = Color(0xFFFF8A00)
}

enum class Mode(val color: Color) {
    WALK(CMColors.ModeWalk), BUS(CMColors.ModeBus), TUBE(CMColors.ModeTube),
    RAIL(CMColors.ModeRail), BIKE(CMColors.ModeBike), CAB(CMColors.ModeCab), FERRY(CMColors.ModeFerry)
}
```

Wire it into both schemes. Citymapper is light-first; the dark scheme uses the signature `#0C0E14` blue-black, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val CMLight = lightColorScheme(
    primary        = CMColors.Blue,
    onPrimary      = CMColors.Canvas,
    background      = CMColors.Canvas,
    onBackground    = CMColors.TextPrimary,
    surface         = CMColors.Surface1,
    onSurface       = CMColors.TextPrimary,
    surfaceVariant  = CMColors.Surface2,
    outline         = CMColors.Divider,
    error           = CMColors.ModeBus,
)

private val CMDark = darkColorScheme(
    primary        = CMColors.BlueBright,
    onPrimary      = CMColors.Canvas,
    background      = CMColors.DarkCanvas,
    onBackground    = CMColors.DarkTextPrim,
    surface         = CMColors.DarkSurface1,
    onSurface       = CMColors.DarkTextPrim,
    surfaceVariant  = CMColors.DarkSurface2,
    outline         = CMColors.DarkDivider,
    error           = CMColors.ModeBus,
)

@Composable
fun CMTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) CMDark else CMLight,
    typography = CMTypography,
    content = content,
)
```

## 2. Typography

Citymapper ships a custom chunky grotesque; drop the TTFs in `res/font/` or use Inter (400–900). ETAs/countdowns use tabular figures.

```kotlin
// ui/theme/CMType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val CMGrotesque = FontFamily(
    Font(R.font.cm_regular,  FontWeight.Normal),
    Font(R.font.cm_semibold, FontWeight.SemiBold),
    Font(R.font.cm_bold,     FontWeight.Bold),
    Font(R.font.cm_heavy,    FontWeight.ExtraBold),
    Font(R.font.cm_black,    FontWeight.Black),
)

object CMText {
    val Display   = TextStyle(CMGrotesque, fontWeight = FontWeight.Black,     fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.5).sp)
    val EtaHero   = TextStyle(CMGrotesque, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 30.sp, letterSpacing = (-0.3).sp, fontFeatureSettings = "tnum")
    val Section   = TextStyle(CMGrotesque, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val RowTitle  = TextStyle(CMGrotesque, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val Body      = TextStyle(CMGrotesque, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 23.sp)
    val Place     = TextStyle(CMGrotesque, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Meta      = TextStyle(CMGrotesque, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val ModeBadge = TextStyle(CMGrotesque, fontWeight = FontWeight.ExtraBold, fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.3.sp)
    val EtaSuffix = TextStyle(CMGrotesque, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 13.sp)
    val GoLabel   = TextStyle(CMGrotesque, fontWeight = FontWeight.Black,     fontSize = 18.sp, lineHeight = 18.sp, letterSpacing = 0.4.sp)
    val Button    = TextStyle(CMGrotesque, fontWeight = FontWeight.ExtraBold, fontSize = 15.sp, lineHeight = 15.sp)
    val Tag       = TextStyle(CMGrotesque, fontWeight = FontWeight.ExtraBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.4.sp)
    val DepMin    = TextStyle(CMGrotesque, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 16.sp, fontFeatureSettings = "tnum")
    val Tab       = TextStyle(CMGrotesque, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

val CMTypography = Typography(
    headlineLarge = CMText.Display,
    headlineMedium = CMText.EtaHero,
    titleLarge    = CMText.Section,
    bodyMedium    = CMText.Body,
    labelSmall    = CMText.Tab,
)
```

## 3. Signature Components

### Mode Chip & Leg Strip

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.unit.dp

@Composable
fun ModeChip(mode: Mode, icon: ImageVector, label: String) {
    Row(
        Modifier
            .clip(RoundedCornerShape(8.dp))
            .background(mode.color)
            .padding(horizontal = 9.dp, vertical = 5.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(5.dp),
    ) {
        Icon(icon, null, tint = Color.White, modifier = Modifier.size(13.dp))
        Text(label, style = CMText.ModeBadge, color = Color.White)
    }
}

data class Leg(val mode: Mode, val icon: ImageVector, val label: String)

@Composable
fun LegStrip(legs: List<Leg>) {
    FlowRow(horizontalArrangement = Arrangement.spacedBy(6.dp), verticalArrangement = Arrangement.spacedBy(6.dp)) {
        legs.forEachIndexed { i, leg ->
            ModeChip(leg.mode, leg.icon, leg.label)
            if (i < legs.size - 1) {
                Text("›", style = CMText.Button, color = CMColors.TextTertiary,
                    modifier = Modifier.align(Alignment.CenterVertically))
            }
        }
    }
}
```

### Route Option Card

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.CircleShape

@Composable
fun RouteCard(eta: Int, tag: Pair<String, Color>?, legs: List<Leg>, best: Boolean) {
    Column(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(16.dp))
            .background(CMColors.Surface1)
            .border(1.dp, if (best) CMColors.GoGreen else CMColors.Divider, RoundedCornerShape(16.dp))
            .padding(horizontal = 16.dp, vertical = 14.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically) {
            Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Text("$eta", style = CMText.Section, color = CMColors.TextPrimary)
                Text("min", style = CMText.EtaSuffix, color = CMColors.TextSecondary)
            }
            Spacer(Modifier.weight(1f))
            tag?.let { (text, color) ->
                Box(Modifier.clip(CircleShape).background(color.copy(alpha = 0.18f))
                    .padding(horizontal = 9.dp, vertical = 4.dp)) {
                    Text(text.uppercase(), style = CMText.Tag, color = color)
                }
            }
        }
        LegStrip(legs)
    }
}
```

### GO Button (shape/color-locked)

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun GoButton(onClick: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    val darkGreenText = Color(0xFF003322)
    Row(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .height(54.dp)
            .clip(RoundedCornerShape(28.dp))           // LOCKED shape
            .background(CMColors.GoGreen)              // LOCKED color — never theme/context dependent
            .clickable {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // medium impact analog
                onClick()
            },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.Center,
    ) {
        Icon(Icons.Filled.PlayArrow, null, tint = darkGreenText, modifier = Modifier.size(20.dp))
        Spacer(Modifier.width(8.dp))
        Text("GO", style = CMText.GoLabel, color = darkGreenText)
    }
}
```

### Origin → Destination Card

```kotlin
@Composable
fun ODCard(from: String, to: String) {
    Column(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(14.dp))
            .background(CMColors.Surface1)
            .border(1.dp, CMColors.Divider, RoundedCornerShape(14.dp))
            .padding(horizontal = 14.dp, vertical = 12.dp),
    ) {
        ODRow(CMColors.TextSecondary, from)
        Box(Modifier.padding(start = 4.dp).width(2.dp).height(14.dp).background(CMColors.Divider))
        Divider(color = CMColors.Divider, modifier = Modifier.padding(vertical = 2.dp))
        ODRow(CMColors.Blue, to)
    }
}

@Composable
private fun ODRow(dot: Color, text: String) {
    Row(
        Modifier.height(34.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(10.dp).clip(CircleShape).background(dot))
        Text(text, style = CMText.Place, color = CMColors.TextPrimary)
    }
}
```

### Departures Board

```kotlin
data class Departure(val badge: String, val mode: Mode, val destination: String, val minutes: Int)

@Composable
fun DeparturesBoard(rows: List<Departure>) {
    Column(
        Modifier.clip(RoundedCornerShape(14.dp)).background(CMColors.Surface2),
    ) {
        rows.forEachIndexed { i, d ->
            Row(
                Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 13.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(12.dp),
            ) {
                Box(
                    Modifier.size(width = 34.dp, height = 24.dp)
                        .clip(RoundedCornerShape(6.dp)).background(d.mode.color),
                    contentAlignment = Alignment.Center,
                ) { Text(d.badge, style = CMText.ModeBadge, color = Color.White) }
                Text(d.destination, style = CMText.Place, color = CMColors.TextPrimary,
                    modifier = Modifier.weight(1f))
                Text("${d.minutes} min", style = CMText.DepMin,
                    color = if (d.minutes <= 3) CMColors.Disruption else CMColors.GoGreen)
            }
            if (i < rows.size - 1) Divider(color = CMColors.Divider)
        }
    }
}
```

### GO Trip-Mode Header & Disruption Banner

```kotlin
@Composable
fun GoTripHeader(instruction: String, sub: String, legColor: Color) {
    Row(Modifier.padding(20.dp), horizontalArrangement = Arrangement.spacedBy(14.dp)) {
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Box(Modifier.size(12.dp).clip(CircleShape).background(legColor))
            Box(Modifier.width(3.dp).height(60.dp).background(legColor))
            Box(Modifier.size(12.dp).clip(CircleShape).border(2.dp, CMColors.TextTertiary, CircleShape))
        }
        Column(verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(instruction, style = CMText.EtaHero, color = CMColors.TextPrimary)
            Text(sub, style = CMText.Body, color = CMColors.TextSecondary)
        }
    }
}

@Composable
fun DisruptionBanner(text: String, severe: Boolean = false) {
    val c = if (severe) CMColors.ModeBus else CMColors.Disruption
    Row(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(10.dp))
            .background(c.copy(alpha = 0.18f))
            .border(1.dp, c, RoundedCornerShape(10.dp))
            .padding(12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Text("⚠", color = c)
        Text(text, style = CMText.RowTitle.copy(fontSize = 14.sp), color = CMColors.TextPrimary)
    }
}
```

## 4. Navigation

Citymapper has a 4-tab bottom strip and the route-list-over-map sheet. On Android, model the strip as a `NavigationBar` (no tint pill — active is Citymapper Blue) and the route list as a `ModalBottomSheet` / persistent `BottomSheetScaffold` over the map.

```kotlin
@Composable
fun CMBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = CMColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Get me" to Icons.Filled.Search,
            "Nearby" to Icons.Filled.LocationOn,
            "Saved"  to Icons.Filled.FavoriteBorder,
            "You"    to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = CMText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = CMColors.Blue,
                    selectedTextColor = CMColors.Blue,
                    unselectedIconColor = CMColors.TextTertiary,
                    unselectedTextColor = CMColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no pill — Citymapper has none
                ),
            )
        }
    }
}
```

The route list lives in a `BottomSheetScaffold` with `sheetPeekHeight = 120.dp` over a full-screen `GoogleMap`/`MapView`; drag snaps between peek / half / expanded with a spring. GO trip mode is a full-screen route that replaces the scaffold.

## 5. Motion

Citymapper motion is purposeful — the sheet drag and the GO-mode step transitions carry the experience.

| Moment | Compose recipe |
|--------|----------------|
| Route list ⇄ map drag | `BottomSheetScaffold` `sheetSwipeEnabled`; snap `spring(dampingRatio = 0.85f, stiffness = 350f)` |
| GO start | `Crossfade` into GO screen `tween(350)`; progress line `Canvas` `animateFloatAsState` trim 0→1 `tween(500)` |
| GO-mode step | completed-leg fill `animateFloatAsState` `tween(250)`; instruction `AnimatedContent` slide-in from top `tween(200)`; medium haptic |
| Departure tick | minutes `AnimatedContent` with `fadeIn/fadeOut` `tween(150)`; crossing ≤3 min → color `animateColorAsState` to `Disruption` |
| Route cards stagger | `LazyColumn` items `AnimatedVisibility` `fadeIn(tween(250))` + `slideInVertically`, delay `i * 60ms` |
| Disruption banner | `AnimatedVisibility` slide-down `tween(250)`; border `animateColorAsState` pulse once |
| Tab switch | content swap instant; active icon `animateFloatAsState` scale 0.9→1 `tween(120)` |

```kotlin
// Departure tick — the canonical Citymapper live motion
AnimatedContent(targetState = minutes, transitionSpec = {
    fadeIn(tween(150)) togetherWith fadeOut(tween(150))
}, label = "depMin") { m -> Text("$m min", style = CMText.DepMin,
    color = if (m <= 3) CMColors.Disruption else CMColors.GoGreen) }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for the light tick on route select / tab change; `HapticFeedbackType.LongPress` (medium analog) on GO start and each GO-mode step transition; for disruptions on the active route use `view.performHapticFeedback(HapticFeedbackConstants.LONG_PRESS)` as a warning cue.

## 6. Icons

Citymapper ships custom mode glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. Real operator line bullets should be bundled as vector assets and override the category color when known.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Get me (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Nearby (tab) | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Saved (tab) | `heart` | `Icons.Filled.FavoriteBorder` / `Favorite` |
| You (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Walk leg | `figure.walk` | `Icons.Filled.DirectionsWalk` |
| Bus leg | `bus.fill` | `Icons.Filled.DirectionsBus` |
| Tube/Metro leg | `tram.fill` | `Icons.Filled.DirectionsSubway` |
| Rail leg | `train.side.front.car` | `Icons.Filled.Train` |
| Bike leg | `bicycle` | `Icons.Filled.DirectionsBike` |
| Cab leg | `car.fill` | `Icons.Filled.LocalTaxi` |
| Ferry leg | `ferry.fill` | `Icons.Filled.DirectionsBoat` |
| GO | `play.fill` | `Icons.Filled.PlayArrow` |
| Leg arrow | `chevron.right` | `Icons.Filled.ChevronRight` |
| Disruption | `exclamationmark.triangle.fill` | `Icons.Filled.Warning` |
| Live dot | `circle.fill` | `Icons.Filled.Circle` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Rain-safe tag | `cloud.rain.fill` | `Icons.Filled.WaterDrop` |
| Step-free tag | `figure.roll` | `Icons.Filled.Accessible` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (`compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`); `BottomSheetScaffold` and the motion APIs are comfortable at 24
- **Edge-to-edge**: `enableEdgeToEdge()`; the calm canvas wants dark-content system bars (light-content on dark); the route sheet peek and the GO button must clear the gesture/nav bar (use `WindowInsets.navigationBars`)
- **Tabular figures**: every ETA / departure-min / GO-mode countdown `TextStyle` sets `fontFeatureSettings = "tnum"` so countdowns don't jitter
- **Font scaling**: `sp` honors the user's font scale on display/ETA/body/place and scales the GO-mode instruction generously; pin layout-sensitive text (mode badges, route tags, the GO label, departure-min, 10sp tab labels) via `dp`-derived sizing or a fixed-`fontScale` `LocalDensity`
- **TalkBack**: merge a leg strip into one node — "Route, 24 minutes, fastest: walk 4 minutes, Victoria line, walk 6 minutes" (so the *mode* is spoken, not just colored); departures as "Victoria line to Brixton, 2 minutes, leaving soon"
- **Color-blind safety**: every mode chip carries a glyph + label; the ≤3-min state is announced as "leaving soon" and the disruption banner has a ⚠ glyph + text — never rely on color alone
- **Touch targets**: Material guidance is 48.dp — the GO pill is 54.dp; give tappable mode chips a 48.dp hit area via padding; route cards and departure rows are full-width tappable
- **Contrast**: `#10131A` on `#FFFFFF` and `#ECEEF3` on `#0C0E14` pass WCAG AA; white-on-mode-color is tuned (cab amber `#FFB400` may need a darker on-color for its badge text)
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the progress-line draw-in and the route-card stagger — keep a single `Crossfade`; keep the departure-minute crossfade (it conveys state)
- **Dark mode**: invert via the `Dark*` palette — `#0C0E14`, NOT true black; the GO button's green glow stays on dark; card separation uses the `#15171F` → `#1E212B` ramp. Do **not** enable `dynamicColorScheme()` — a transit map and the per-mode colors must hold regardless of wallpaper. Every `Mode.color` and `GoGreen` is **never** swapped between schemes; when an operator's official line color is known, override the category color with it.
