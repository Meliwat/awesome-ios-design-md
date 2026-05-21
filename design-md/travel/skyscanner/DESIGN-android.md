# Skyscanner (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Skyscanner's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the search card / price grid / flight row / sort tabs, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Skyscanner's white tool-canvas, single Sky Blue action, the green-amber-red price language, "Everywhere") while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `SegmentedButton` instead of a UISegmentedControl, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. Skyscanner's palette is fixed (no Material You extraction — a price-comparison tool must read identically regardless of wallpaper). A full dark scheme is provided; the traffic-light trio is theme-invariant.

## 1. Color Tokens

```kotlin
// ui/theme/SkyColors.kt
import androidx.compose.ui.graphics.Color

object SkyColors {
    // Canvas & Surfaces (Light)
    val Canvas    = Color(0xFFFFFFFF)
    val Surface1  = Color(0xFFF5F7FA)
    val Surface2  = Color(0xFFE9EDF2)
    val Divider   = Color(0xFFE2E7EC)

    // Canvas & Surfaces (Dark) — cool navy-black, NOT pure black
    val DarkCanvas   = Color(0xFF0B0F14)
    val DarkSurface1 = Color(0xFF141A22)
    val DarkSurface2 = Color(0xFF1C242E)
    val DarkDivider  = Color(0xFF25303C)

    // Text
    val TextPrimary    = Color(0xFF0E1B2C)
    val TextSecondary  = Color(0xFF5C6B7A)
    val TextTertiary   = Color(0xFF8B97A3)
    val DarkTextPrim   = Color(0xFFE8EDF2)
    val DarkTextSec    = Color(0xFF9AA7B4)

    // Brand — Sky Blue carries 100% of primary actions
    val SkyBlue        = Color(0xFF0770E3)
    val SkyBluePressed = Color(0xFF0A5BBA)
    val SkyBright      = Color(0xFF05A8FA) // dark links / Everywhere accent
    val DeepNavy       = Color(0xFF05203C)

    // Price traffic-light (theme-invariant — NEVER swap for dark)
    val PriceLow  = Color(0xFF00A698)
    val PriceAvg  = Color(0xFFFFB81C)
    val PriceHigh = Color(0xFFE5392E)

    // Tints
    val LowTint  = Color(0xFF00A698).copy(alpha = 0.16f)
    val AvgTint  = Color(0xFFFFB81C).copy(alpha = 0.16f)
    val HighTint = Color(0xFFE5392E).copy(alpha = 0.16f)
}
```

Wire it into both schemes. Skyscanner is light-first (white tool); the dark scheme uses the signature `#0B0F14` navy-black, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val SkyLight = lightColorScheme(
    primary        = SkyColors.SkyBlue,
    onPrimary      = SkyColors.Canvas,
    background      = SkyColors.Canvas,
    onBackground    = SkyColors.TextPrimary,
    surface         = SkyColors.Surface1,
    onSurface       = SkyColors.TextPrimary,
    surfaceVariant  = SkyColors.Surface2,
    outline         = SkyColors.Divider,
    error           = SkyColors.PriceHigh,
)

private val SkyDark = darkColorScheme(
    primary        = SkyColors.SkyBlue,        // brand action unchanged
    onPrimary      = SkyColors.Canvas,
    background      = SkyColors.DarkCanvas,
    onBackground    = SkyColors.DarkTextPrim,
    surface         = SkyColors.DarkSurface1,
    onSurface       = SkyColors.DarkTextPrim,
    surfaceVariant  = SkyColors.DarkSurface2,
    outline         = SkyColors.DarkDivider,
    error           = SkyColors.PriceHigh,
)

@Composable
fun SkyTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) SkyDark else SkyLight,
    typography = SkyTypography,
    content = content,
)
```

## 2. Typography

Skyscanner ships **Skyscanner Relative** (Dalton Maag); drop the TTFs in `res/font/` or fall back to the platform sans. Numbers are the hero — every money/time style uses tabular figures. Body 400, fares/times 700–800.

```kotlin
// ui/theme/SkyType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextGeometricTransform
import androidx.compose.ui.unit.sp

val Relative = FontFamily(
    Font(R.font.relative_regular, FontWeight.Normal),
    Font(R.font.relative_medium,  FontWeight.Medium),
    Font(R.font.relative_bold,    FontWeight.Bold),
    Font(R.font.relative_black,   FontWeight.Black),
)

// Tabular figures via OpenType feature
private val tnum = androidx.compose.ui.text.font.FontFeatureSettings("tnum")

object SkyText {
    val Display     = TextStyle(Relative, fontWeight = FontWeight.Black,  fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.5).sp)
    val ScreenTitle = TextStyle(Relative, fontWeight = FontWeight.Bold,   fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Relative, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(Relative, fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 23.sp)
    val FareLarge   = TextStyle(Relative, fontWeight = FontWeight.Black,  fontSize = 19.sp, lineHeight = 23.sp, fontFeatureSettings = "tnum")
    val Body        = TextStyle(Relative, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 23.sp)
    val PriceInline = TextStyle(Relative, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 20.sp, fontFeatureSettings = "tnum")
    val TimeText    = TextStyle(Relative, fontWeight = FontWeight.Bold,   fontSize = 17.sp, lineHeight = 20.sp, fontFeatureSettings = "tnum")
    val Meta        = TextStyle(Relative, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 20.sp)
    val Airport     = TextStyle(Relative, fontWeight = FontWeight.Medium, fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.4.sp)
    val FieldLabel  = TextStyle(Relative, fontWeight = FontWeight.Medium, fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Button      = TextStyle(Relative, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 16.sp)
    val Chip        = TextStyle(Relative, fontWeight = FontWeight.Bold,   fontSize = 13.sp, lineHeight = 13.sp)
    val Tab         = TextStyle(Relative, fontWeight = FontWeight.Medium, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val CalDay      = TextStyle(Relative, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 12.sp, fontFeatureSettings = "tnum")
    val CalPrice    = TextStyle(Relative, fontWeight = FontWeight.Medium, fontSize = 8.sp,  lineHeight = 9.sp,  fontFeatureSettings = "tnum")
}

val SkyTypography = Typography(
    headlineLarge = SkyText.Display,
    headlineMedium = SkyText.ScreenTitle,
    titleLarge    = SkyText.Section,
    bodyMedium    = SkyText.Body,
    labelSmall    = SkyText.Tab,
)
```

## 3. Signature Components

### Stacked Search Card (From / To / Dates + Swap)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

@Composable
fun SearchCard(from: String, to: String, dates: String, onSwap: () -> Unit) {
    Box(Modifier.padding(horizontal = 18.dp)) {
        Column(
            Modifier
                .clip(RoundedCornerShape(16.dp))
                .background(SkyColors.Canvas)
                .border(1.dp, SkyColors.Divider, RoundedCornerShape(16.dp)),
        ) {
            SearchField(Icons.Filled.LocationOn, "FROM", from)
            Divider(color = SkyColors.Divider)
            SearchField(Icons.Filled.LocationOn, "TO", to)
            Divider(color = SkyColors.Divider)
            SearchField(Icons.Filled.DateRange, "DEPART · RETURN", dates)
        }
        // swap button centered on the From–To divider
        Surface(
            onClick = onSwap,
            shape = CircleShape,
            color = SkyColors.Canvas,
            border = BorderStroke(1.dp, SkyColors.Divider),
            modifier = Modifier.align(Alignment.TopEnd).padding(end = 16.dp).offset(y = 56.dp).size(30.dp),
        ) {
            Icon(Icons.Filled.SwapVert, null, tint = SkyColors.TextSecondary,
                modifier = Modifier.padding(7.dp))
        }
    }
}

@Composable
private fun SearchField(icon: androidx.compose.ui.graphics.vector.ImageVector, label: String, value: String) {
    Row(
        Modifier.fillMaxWidth().padding(vertical = 14.dp, horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Icon(icon, null, tint = SkyColors.SkyBlue, modifier = Modifier.size(18.dp))
        Column {
            Text(label, style = SkyText.FieldLabel, color = SkyColors.TextTertiary)
            Text(value, style = SkyText.PriceInline, color = SkyColors.TextPrimary)
        }
    }
}
```

### Month Price Grid (signature surface)

```kotlin
import androidx.compose.foundation.lazy.grid.GridCells
import androidx.compose.foundation.lazy.grid.LazyVerticalGrid
import androidx.compose.foundation.lazy.grid.itemsIndexed
import androidx.compose.foundation.clickable
import androidx.compose.runtime.*
import androidx.compose.ui.draw.alpha

enum class Band { LOW, AVG, HIGH }
data class CalDay(val number: Int, val fare: Int?, val band: Band?)

private fun bandColor(b: Band?) = when (b) {
    Band.LOW -> SkyColors.PriceLow
    Band.AVG -> SkyColors.PriceAvg
    Band.HIGH -> SkyColors.PriceHigh
    null -> SkyColors.TextTertiary
}

@Composable
fun PriceGrid(days: List<CalDay>, selected: Int?, onSelect: (Int) -> Unit) {
    Column(Modifier.padding(horizontal = 18.dp)) {
        Text("Cheapest month — October", style = SkyText.Section, color = SkyColors.TextPrimary)
        Text("Green is a good deal · prices per person", style = SkyText.Meta,
            color = SkyColors.TextSecondary, modifier = Modifier.padding(bottom = 12.dp))
        LazyVerticalGrid(columns = GridCells.Fixed(7), userScrollEnabled = false) {
            itemsIndexed(days) { i, d ->
                val sel = selected == i
                Box(
                    Modifier
                        .padding(2.5.dp)
                        .aspectRatio(1f)
                        .clip(RoundedCornerShape(7.dp))
                        .background(if (sel) SkyColors.SkyBlue else SkyColors.Surface1)
                        .border(if (sel) 0.dp else 1.dp, SkyColors.Divider, RoundedCornerShape(7.dp))
                        .alpha(if (d.fare == null) 0.35f else 1f)
                        .clickable(enabled = d.fare != null) { onSelect(i) },
                    contentAlignment = Alignment.Center,
                ) {
                    Column(horizontalAlignment = Alignment.CenterHorizontally) {
                        Text("${d.number}", style = SkyText.CalDay,
                            color = if (sel) SkyColors.Canvas else SkyColors.TextPrimary)
                        if (d.fare != null) Text("£${d.fare}", style = SkyText.CalPrice,
                            color = if (sel) SkyColors.Canvas else bandColor(d.band))
                    }
                }
            }
        }
    }
}
```

### Flight Result Row

```kotlin
@Composable
fun FlightRow(
    depTime: String, depCode: String, arrTime: String, arrCode: String,
    duration: String, stops: String, fare: String, stopColor: Color = SkyColors.PriceLow,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(14.dp))
            .background(SkyColors.Surface1)
            .border(1.dp, SkyColors.Divider, RoundedCornerShape(14.dp))
            .padding(16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Column { Text(depTime, style = SkyText.TimeText, color = SkyColors.TextPrimary)
                 Text(depCode, style = SkyText.Airport, color = SkyColors.TextSecondary) }
        Column(Modifier.weight(1f), horizontalAlignment = Alignment.CenterHorizontally) {
            Text(duration, style = SkyText.Meta.copy(fontSize = 11.sp), color = SkyColors.TextTertiary)
            Box(Modifier.fillMaxWidth().padding(vertical = 6.dp)) {
                Divider(color = SkyColors.Divider)
                Box(Modifier.align(Alignment.CenterEnd).size(5.dp).clip(CircleShape).background(SkyColors.SkyBlue))
            }
            Text(stops, style = SkyText.Airport.copy(fontWeight = FontWeight.SemiBold), color = stopColor)
        }
        Column { Text(arrTime, style = SkyText.TimeText, color = SkyColors.TextPrimary)
                 Text(arrCode, style = SkyText.Airport, color = SkyColors.TextSecondary) }
        Column(horizontalAlignment = Alignment.End) {
            Text(fare, style = SkyText.FareLarge, color = SkyColors.TextPrimary)
            Text("return", style = SkyText.Airport, color = SkyColors.TextSecondary)
        }
    }
}
```

### Best / Cheapest / Fastest Sort Tabs & Price Chip

```kotlin
@Composable
fun SortTabs(selected: String, fares: Map<String, String>, onSelect: (String) -> Unit) {
    val tabs = listOf("Best", "Cheapest", "Fastest")
    Row(Modifier.fillMaxWidth()) {
        tabs.forEach { s ->
            val active = selected == s
            Column(
                Modifier.weight(1f).clickable { onSelect(s) },
                horizontalAlignment = Alignment.CenterHorizontally,
            ) {
                Text(s, style = SkyText.Chip,
                    color = if (active) SkyColors.TextPrimary else SkyColors.TextSecondary)
                Text(fares[s] ?: "—", style = SkyText.PriceInline,
                    color = if (active) SkyColors.TextPrimary else SkyColors.TextSecondary)
                Spacer(Modifier.height(4.dp))
                Box(Modifier.fillMaxWidth().height(2.dp)
                    .background(if (active) SkyColors.SkyBlue else Color.Transparent))
            }
        }
    }
}

@Composable
fun PriceChip(fare: String, band: Band) {
    val (c, t, lbl) = when (band) {
        Band.LOW  -> Triple(SkyColors.PriceLow,  SkyColors.LowTint,  "Cheap")
        Band.AVG  -> Triple(SkyColors.PriceAvg,  SkyColors.AvgTint,  "Average")
        Band.HIGH -> Triple(SkyColors.PriceHigh, SkyColors.HighTint, "Expensive")
    }
    Box(Modifier.clip(RoundedCornerShape(8.dp)).background(t).padding(horizontal = 14.dp, vertical = 7.dp)) {
        Text("$fare · $lbl", style = SkyText.Chip, color = c)
    }
}
```

## 4. Navigation

Skyscanner has minimal chrome: a 4-tab bottom strip and a trip-type segmented control. On Android, model the strip as a `NavigationBar` (no tint pill — active is Sky Blue) and the trip-type selector as a Material 3 `SingleChoiceSegmentedButtonRow`.

```kotlin
@Composable
fun SkyBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = SkyColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Explore" to Icons.Filled.Send,
            "Trips"   to Icons.AutoMirrored.Filled.List,
            "Saved"   to Icons.Filled.FavoriteBorder,
            "Profile" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = SkyText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = SkyColors.SkyBlue,
                    selectedTextColor = SkyColors.SkyBlue,
                    unselectedIconColor = SkyColors.TextTertiary,
                    unselectedTextColor = SkyColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no pill — Skyscanner has none
                ),
            )
        }
    }
}

@Composable
fun TripTypeSelector(selected: Int, onSelect: (Int) -> Unit) {
    val options = listOf("Return", "One way", "Multi-city")
    SingleChoiceSegmentedButtonRow(Modifier.fillMaxWidth().padding(horizontal = 18.dp)) {
        options.forEachIndexed { i, label ->
            SegmentedButton(
                selected = selected == i,
                onClick = { onSelect(i) },
                shape = SegmentedButtonDefaults.itemShape(i, options.size, RoundedCornerShape(8.dp)),
                colors = SegmentedButtonDefaults.colors(
                    activeContainerColor = SkyColors.SkyBlue,
                    activeContentColor = SkyColors.Canvas,
                    inactiveContainerColor = SkyColors.Surface1,
                    inactiveContentColor = SkyColors.TextSecondary,
                ),
            ) { Text(label, style = SkyText.Chip.copy(fontSize = 12.sp)) }
        }
    }
}
```

## 5. Motion

Skyscanner motion is quiet and functional — the only "alive" moment is fares streaming in.

| Moment | Compose recipe |
|--------|----------------|
| Calendar day select | cell background `animateColorAsState` to `SkyBlue` `tween(150)` |
| Sort tab switch | underline `Modifier.offset`/width `animateDpAsState` `tween(200)`; list re-sort `animateItemPlacement(tween(250))` |
| Swap From/To | swap glyph `animateFloatAsState` 0 → 180° `tween(250)`; values `Crossfade` |
| Live price streaming | rows enter via `AnimatedVisibility` `fadeIn(tween(200))` + `slideInVertically`, staggered by `index * 40ms`; fare change = `Crossfade` 150ms |
| Price-alert bell | `animateFloatAsState` scale 1 → 1.15 → 1 (`keyframes`); soft haptic |
| Page navigation | Nav slide push `tween(300)` |
| Pull to refresh | `PullToRefreshContainer`; on release, restream rows |

```kotlin
// Calendar selection — the canonical Skyscanner motion
val bg by animateColorAsState(
    if (selected) SkyColors.SkyBlue else SkyColors.Surface1,
    animationSpec = tween(150), label = "calCell",
)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for the light tick on calendar select and tab switch; `HapticFeedbackType.LongPress` (as a success analog) when a price alert is created. Live fares stream silently — no haptic per row.

## 6. Icons

Skyscanner ships a simple line-icon set; the closest first-party match is `androidx.compose.material:material-icons-extended`. Carrier logos should be bundled as monochrome-tolerant vectors.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Explore (tab) | `paperplane` | `Icons.Filled.Send` |
| Trips (tab) | `list.bullet` | `Icons.AutoMirrored.Filled.List` |
| Saved (tab) | `heart` | `Icons.Filled.FavoriteBorder` / `Favorite` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| From / To | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Dates | `calendar` | `Icons.Filled.DateRange` |
| Swap | `arrow.up.arrow.down` | `Icons.Filled.SwapVert` |
| Everywhere | `globe` | `Icons.Filled.Public` |
| Price alert | `bell` / `bell.fill` | `Icons.Filled.NotificationsNone` / `Notifications` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Filters | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Sort | `arrow.up.arrow.down.circle` | `Icons.Filled.SwapVert` |
| Stop dot | `circle.fill` | `Icons.Filled.Circle` |
| Refresh fares | `arrow.clockwise` | `Icons.Filled.Refresh` |
| Share trip | `square.and.arrow.up` | `Icons.Filled.Share` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (`compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`)
- **Edge-to-edge**: `enableEdgeToEdge()`; light canvas wants dark-content system bars (light-content in dark mode); the top app bar respects the camera cutout; autocomplete `Popup` must sit above the IME — use `Modifier.imePadding()`
- **Tabular figures**: every fare/time/calendar-price `TextStyle` sets `fontFeatureSettings = "tnum"` so columns align — non-negotiable for the price grid
- **Font scaling**: `sp` honors the user's font scale on display/title/body/fares; pin layout-sensitive text (tab labels, airport codes, field labels, the 11sp/8sp calendar text) via `dp`-derived sizing or a fixed-`fontScale` `LocalDensity`
- **TalkBack**: announce calendar cells as "October 12, 42 pounds, cheap price" (date + fare + band, so the traffic-light meaning is *spoken* not just colored); flight rows as "London Heathrow 07:25 to Barcelona 10:40, 2 hours 15 minutes, direct, 42 pounds return"
- **Color-blind safety**: never rely on color alone — price chips include the word ("Cheap"/"Average"/"Expensive") beside the colored fare; calendar cells expose the band in `contentDescription`
- **Touch targets**: Material guidance is 48.dp — give the 30.dp swap button and calendar cells a 48.dp hit area via padding; the search button is ≥ 50.dp; sort tabs are full-segment tappable
- **Contrast**: `#0E1B2C` on `#FFFFFF` and `#E8EDF2` on `#0B0F14` pass WCAG AA; amber `#FFB81C` always sits on a tint with a paired label, never as thin text on white
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the swap rotation and the staggered price-stream — substitute a single `Crossfade`; keep the calendar selection color (it conveys state)
- **Dark mode**: invert via the `Dark*` palette — `#0B0F14`, NOT true black; drop the Search button's blue glow on dark (separation comes from the `#141A22` → `#1C242E` surface ramp). Do **not** enable `dynamicColorScheme()` — Skyscanner's neutral-tool identity and the fixed traffic-light meaning must hold regardless of wallpaper. The `PriceLow`/`PriceAvg`/`PriceHigh` trio is **never** swapped between schemes.
