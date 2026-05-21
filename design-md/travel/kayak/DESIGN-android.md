# KAYAK (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports KAYAK's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, an M3 `Typography`, paste-ready `@Composable`s (fare card, price-calendar strip, forecast banner, fare-compare matrix), navigation, motion, and haptics.

> The DESIGN.md tokens are platform-neutral. This guide keeps the *visual* identity (KAYAK Orange as the single scarce accent, the color-coded price calendar, the forecast banner, the fare-compare matrix) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Card` elevation, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for airline logos. Do **not** enable Material You dynamic color — KAYAK's lone orange accent must hold regardless of wallpaper.

## 1. Color Tokens (Compose + M3)

```kotlin
// ui/theme/KayakColors.kt
import androidx.compose.ui.graphics.Color

object KayakColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF4F5F7)
    val SurfacePressed = Color(0xFFE9EBEE)
    val Divider        = Color(0xFFE2E5E9)

    // Canvas & Surfaces (Dark) — near-neutral, NO brand tint
    val DarkCanvas   = Color(0xFF101214)
    val DarkSurface1 = Color(0xFF181B1E)
    val DarkSurface2 = Color(0xFF21262B)
    val DarkDivider  = Color(0xFF2C3338)

    // Text
    val TextPrimary     = Color(0xFF16191C)
    val TextSecondary   = Color(0xFF5C656E)
    val TextTertiary    = Color(0xFF8B949C)
    val DarkTextPrimary   = Color(0xFFE9ECEF)
    val DarkTextSecondary = Color(0xFF9BA3AB)

    // Brand (single accent)
    val Orange        = Color(0xFFFF690F)
    val OrangePressed = Color(0xFFE0560A)
    val OrangeSoft    = Color(0xFFFF8A42)
    val Link          = Color(0xFF2E7CF6)

    // Functional semantic (NOT brand)
    val PriceLow  = Color(0xFF1E9E5A)
    val PriceHigh = Color(0xFFE5484D)
    val Wait      = Color(0xFFE8A317)
}

enum class PriceBand { Low, Mid, High }

fun kykPriceColor(band: PriceBand, dark: Boolean = false): Color = when (band) {
    PriceBand.Low  -> KayakColors.PriceLow
    PriceBand.Mid  -> if (dark) KayakColors.DarkTextPrimary else KayakColors.TextPrimary
    PriceBand.High -> KayakColors.PriceHigh
}

enum class ForecastAdvice { Buy, Wait, Rise }

fun kykAdviceColor(a: ForecastAdvice): Color = when (a) {
    ForecastAdvice.Buy  -> KayakColors.PriceLow
    ForecastAdvice.Wait -> KayakColors.Wait
    ForecastAdvice.Rise -> KayakColors.Orange   // rising uses brand orange in KAYAK
}
```

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val KayakLight = lightColorScheme(
    primary        = KayakColors.Orange,
    onPrimary      = KayakColors.Canvas,
    background      = KayakColors.SurfaceGray,
    onBackground    = KayakColors.TextPrimary,
    surface         = KayakColors.Canvas,
    onSurface       = KayakColors.TextPrimary,
    surfaceVariant  = KayakColors.SurfacePressed,
    outline         = KayakColors.Divider,
    error           = KayakColors.PriceHigh,
)

private val KayakDark = darkColorScheme(
    primary        = KayakColors.Orange,
    onPrimary      = KayakColors.Canvas,
    background      = KayakColors.DarkCanvas,
    onBackground    = KayakColors.DarkTextPrimary,
    surface         = KayakColors.DarkSurface1,
    onSurface       = KayakColors.DarkTextPrimary,
    surfaceVariant  = KayakColors.DarkSurface2,
    outline         = KayakColors.DarkDivider,
    error           = KayakColors.PriceHigh,
)

@Composable
fun KayakTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) KayakDark else KayakLight,
    typography  = KayakTypography,
    content     = content,
)
```

## 2. Typography (M3)

KAYAK's product face is proprietary — drop the TTFs in `res/font/` only if licensed; otherwise use Inter (closest free substitute). Prices/times/matrix cells need tabular figures.

```kotlin
// ui/theme/KayakType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val KayakSans = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),
    Font(R.font.inter_semibold,  FontWeight.SemiBold),
    Font(R.font.inter_bold,      FontWeight.Bold),
    Font(R.font.inter_extrabold, FontWeight.ExtraBold),
)

object KayakText {
    val Display   = TextStyle(KayakSans, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val Route     = TextStyle(KayakSans, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section   = TextStyle(KayakSans, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Body      = TextStyle(KayakSans, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val Label     = TextStyle(KayakSans, fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 19.sp)
    val Meta      = TextStyle(KayakSans, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val CalDay    = TextStyle(KayakSans, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 12.sp)
    val Tag       = TextStyle(KayakSans, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 12.sp, letterSpacing = 0.3.sp)
    val Button    = TextStyle(KayakSans, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab       = TextStyle(KayakSans, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    // Numeric — apply fontFeatureSettings = "tnum"
    val Price     = TextStyle(KayakSans, fontWeight = FontWeight.ExtraBold, fontSize = 20.sp, lineHeight = 24.sp, fontFeatureSettings = "tnum")
    val LegTime   = TextStyle(KayakSans, fontWeight = FontWeight.ExtraBold, fontSize = 18.sp, lineHeight = 22.sp, fontFeatureSettings = "tnum")
    val CalPrice  = TextStyle(KayakSans, fontWeight = FontWeight.ExtraBold, fontSize = 13.sp, lineHeight = 14.sp, fontFeatureSettings = "tnum")
    val MatrixNum = TextStyle(KayakSans, fontWeight = FontWeight.ExtraBold, fontSize = 12.sp, lineHeight = 14.sp, fontFeatureSettings = "tnum")
}

val KayakTypography = Typography(
    headlineLarge  = KayakText.Display,
    headlineMedium = KayakText.Route,
    titleLarge     = KayakText.Section,
    bodyMedium     = KayakText.Body,
    labelSmall     = KayakText.Tab,
)
```

## 3. Signature Components

### Fare Card (@Composable)

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Card
import androidx.compose.material3.CardDefaults
import androidx.compose.material3.HorizontalDivider
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

@Composable
fun FareCard(
    airlineCode: String,
    airlineName: String,
    isHackerFare: Boolean,
    departTime: String, departCode: String,
    arriveTime: String, arriveCode: String,
    duration: String, stopsText: String, nonstop: Boolean,
    provider: String, price: Int,
    modifier: Modifier = Modifier,
) {
    Card(
        modifier = modifier.fillMaxWidth(),
        shape = RoundedCornerShape(14.dp),
        colors = CardDefaults.cardColors(containerColor = KayakColors.Canvas),
        border = BorderStroke(0.5.dp, KayakColors.Divider),
        elevation = CardDefaults.cardElevation(defaultElevation = 2.dp),
    ) {
        Column(Modifier.padding(14.dp)) {
            Row(verticalAlignment = Alignment.CenterVertically) {
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                    Box(
                        Modifier.size(26.dp).clip(RoundedCornerShape(7.dp)).background(KayakColors.SurfaceGray),
                        contentAlignment = Alignment.Center,
                    ) { Text(airlineCode, style = KayakText.Tag.copy(fontSize = 10.sp), color = KayakColors.TextSecondary) }
                    Text(airlineName, style = KayakText.Meta.copy(fontWeight = FontWeight.SemiBold), color = KayakColors.TextSecondary)
                }
                Spacer(Modifier.weight(1f))
                if (isHackerFare) {
                    Text(
                        "HACKER FARE", style = KayakText.Tag, color = androidx.compose.ui.graphics.Color.White,
                        modifier = Modifier
                            .clip(RoundedCornerShape(5.dp)).background(KayakColors.Orange)
                            .padding(vertical = 3.dp, horizontal = 7.dp),
                    )
                }
            }

            Row(
                Modifier.padding(top = 12.dp).fillMaxWidth(),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(12.dp),
            ) {
                Column(horizontalAlignment = Alignment.CenterHorizontally) {
                    Text(departTime, style = KayakText.LegTime, color = KayakColors.TextPrimary)
                    Text(departCode, style = KayakText.Meta.copy(fontSize = 11.sp), color = KayakColors.TextSecondary)
                }
                Column(Modifier.weight(1f), horizontalAlignment = Alignment.CenterHorizontally) {
                    Text(duration, style = KayakText.Meta.copy(fontSize = 11.sp), color = KayakColors.TextSecondary)
                    HorizontalDivider(Modifier.padding(vertical = 6.dp), thickness = 1.dp, color = KayakColors.Divider)
                    Text(
                        stopsText,
                        style = KayakText.Meta.copy(fontSize = 11.sp, fontWeight = FontWeight.Bold),
                        color = if (nonstop) KayakColors.TextSecondary else KayakColors.Orange,
                    )
                }
                Column(horizontalAlignment = Alignment.CenterHorizontally) {
                    Text(arriveTime, style = KayakText.LegTime, color = KayakColors.TextPrimary)
                    Text(arriveCode, style = KayakText.Meta.copy(fontSize = 11.sp), color = KayakColors.TextSecondary)
                }
            }

            HorizontalDivider(Modifier.padding(top = 14.dp), thickness = 0.5.dp, color = KayakColors.Divider)
            Row(
                Modifier.padding(top = 12.dp).fillMaxWidth(),
                verticalAlignment = Alignment.Bottom,
                horizontalArrangement = Arrangement.SpaceBetween,
            ) {
                Text(provider, style = KayakText.Meta.copy(fontSize = 11.sp), color = KayakColors.TextSecondary)
                Column(horizontalAlignment = Alignment.End) {
                    Text("$$price", style = KayakText.Price, color = KayakColors.TextPrimary)
                    Text("round-trip", style = KayakText.Meta.copy(fontSize = 11.sp), color = KayakColors.TextSecondary)
                }
            }
        }
    }
}
```

### Price-Calendar Strip (@Composable)

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.itemsIndexed

data class CalDay(val label: String, val price: Int, val band: PriceBand)

@Composable
fun PriceCalendarStrip(days: List<CalDay>, selected: Int, onSelect: (Int) -> Unit) {
    LazyRow(
        horizontalArrangement = Arrangement.spacedBy(6.dp),
        contentPadding = PaddingValues(horizontal = 16.dp),
    ) {
        itemsIndexed(days) { i, d ->
            val sel = i == selected
            Column(
                Modifier
                    .width(50.dp)
                    .clip(RoundedCornerShape(10.dp))
                    .background(if (sel) KayakColors.Orange else KayakColors.Canvas)
                    .then(if (sel) Modifier else Modifier.border(0.5.dp, KayakColors.Divider, RoundedCornerShape(10.dp)))
                    .clickable { onSelect(i) }
                    .padding(vertical = 8.dp),
                horizontalAlignment = Alignment.CenterHorizontally,
            ) {
                Text(d.label, style = KayakText.CalDay, color = if (sel) androidx.compose.ui.graphics.Color.White else KayakColors.TextSecondary)
                Text(
                    "$${d.price}",
                    style = KayakText.CalPrice,
                    color = if (sel) androidx.compose.ui.graphics.Color.White else kykPriceColor(d.band),
                    modifier = Modifier.padding(top = 4.dp),
                )
            }
        }
    }
}
```

### Forecast Banner (@Composable)

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.CheckCircle
import androidx.compose.material.icons.filled.Schedule
import androidx.compose.material.icons.filled.TrendingUp
import androidx.compose.material3.Icon

@Composable
fun ForecastBanner(advice: ForecastAdvice, title: String, subtitle: String, modifier: Modifier = Modifier) {
    val tint = kykAdviceColor(advice)
    val icon = when (advice) {
        ForecastAdvice.Buy  -> Icons.Filled.CheckCircle
        ForecastAdvice.Wait -> Icons.Filled.Schedule
        ForecastAdvice.Rise -> Icons.Filled.TrendingUp
    }
    Row(
        modifier
            .fillMaxWidth().padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(10.dp))
            .background(tint.copy(alpha = 0.12f))
            .border(0.5.dp, tint.copy(alpha = 0.4f), RoundedCornerShape(10.dp))
            .padding(vertical = 11.dp, horizontal = 13.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(icon, contentDescription = null, tint = tint, modifier = Modifier.size(18.dp))
        Column(Modifier.weight(1f)) {
            Text(title, style = KayakText.Meta.copy(fontSize = 12.sp, fontWeight = FontWeight.Bold), color = KayakColors.TextPrimary)
            Text(subtitle, style = KayakText.Meta.copy(fontSize = 11.sp), color = KayakColors.TextSecondary)
        }
    }
}
```

### Fare-Compare Matrix (@Composable)

```kotlin
data class MatrixRow(val airline: String, val cells: List<Int?>, val bestIndex: Int?)

@Composable
fun FareMatrix(columns: List<String>, rows: List<MatrixRow>, modifier: Modifier = Modifier) {
    Column(
        modifier
            .clip(RoundedCornerShape(10.dp))
            .border(0.5.dp, KayakColors.Divider, RoundedCornerShape(10.dp)),
    ) {
        Row(Modifier.fillMaxWidth().background(KayakColors.SurfaceGray).padding(vertical = 10.dp, horizontal = 8.dp)) {
            Text("Airline", style = KayakText.Label, color = KayakColors.TextPrimary, modifier = Modifier.weight(1.4f))
            columns.forEach { c ->
                Text(c, style = KayakText.Meta, color = KayakColors.TextSecondary,
                    modifier = Modifier.weight(1f), textAlign = androidx.compose.ui.text.style.TextAlign.Center)
            }
        }
        rows.forEach { row ->
            HorizontalDivider(thickness = 0.5.dp, color = KayakColors.Divider)
            Row(Modifier.fillMaxWidth().padding(vertical = 10.dp, horizontal = 8.dp)) {
                Text(row.airline, style = KayakText.Label, color = KayakColors.TextPrimary, modifier = Modifier.weight(1.4f))
                row.cells.forEachIndexed { ci, v ->
                    val best = row.bestIndex == ci
                    Text(
                        v?.let { "$$it" } ?: "—",
                        style = KayakText.MatrixNum,
                        color = when { v == null -> KayakColors.TextTertiary; best -> KayakColors.PriceLow; else -> KayakColors.TextPrimary },
                        modifier = Modifier.weight(1f),
                        textAlign = androidx.compose.ui.text.style.TextAlign.Center,
                    )
                }
            }
        }
    }
}
```

## 4. Navigation

KAYAK uses a 5-tab bottom bar (Search / Trends / Trips / Price Alerts / Profile). On Android, model it as a Material 3 `NavigationBar`. Active is KAYAK Orange; no Material tint pill.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun KayakBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = KayakColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Search"       to Icons.Filled.Search,
            "Trends"       to Icons.Filled.TrendingUp,
            "Trips"        to Icons.Filled.BookmarkBorder,
            "Price Alerts" to Icons.Filled.NotificationsNone,
            "Profile"      to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = KayakText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = KayakColors.Orange,
                    selectedTextColor = KayakColors.Orange,
                    unselectedIconColor = KayakColors.TextTertiary,
                    unselectedTextColor = KayakColors.TextTertiary,
                    indicatorColor = androidx.compose.ui.graphics.Color.Transparent, // no Material pill
                ),
            )
        }
    }
}
```

The results screen uses a `TopAppBar` (compact route + edit-search + share), with the price-calendar strip + forecast banner pinned below it, then a `LazyColumn` of fare cards; a `BottomAppBar` holds the sticky "View Deal" CTA on the fare-detail screen.

## 5. Motion

Motion is functional — 140–300ms ease-out, never showy. Shadow only marks tappable cards.

| Moment | Compose recipe |
|--------|----------------|
| Calendar day select | cell fill is immediate; fare list `Crossfade(targetState = date, animationSpec = tween(220))`; skeleton shimmer while re-pricing |
| Forecast reveal | `AnimatedVisibility(slideInVertically { -it } + fadeIn(), tween(240))` on results load |
| Fare card → detail | shared element via `SharedTransitionLayout` (Compose 1.7+); push `tween(300)` |
| Matrix cell tap | `animateColorAsState` flash to `Orange.copy(alpha=.12f)` then back, `tween(140)` |
| Price-drop pulse (Alerts) | `animateFloatAsState` scale 1 → 1.08 → 1, `tween(180)`, one cycle |
| Sort/filter sheet | `ModalBottomSheet` slide-up `tween(300)`; result count `animateIntAsState` |
| Pull-to-refresh | `PullToRefreshContainer` tinted `Orange`; re-runs metasearch |

```kotlin
Crossfade(targetState = selectedDate, animationSpec = tween(220), label = "fareList") { date ->
    FareList(date = date)
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on calendar select, segment switch, and filter toggle; a success cue when a tracked price drop is captured. Re-pricing is silent except for the skeleton shimmer.

## 6. Icons

Use `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Trends (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Trips (tab) | `bookmark` | `Icons.Filled.BookmarkBorder` |
| Price Alerts (tab) | `bell` | `Icons.Filled.NotificationsNone` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Route arrow | `arrow.right` | `Icons.Filled.ArrowForward` |
| Forecast rise | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Forecast buy | `checkmark.circle` | `Icons.Filled.CheckCircle` |
| Forecast wait | `clock` | `Icons.Filled.Schedule` |
| Swap | `arrow.up.arrow.down` | `Icons.Filled.SwapVert` |
| Filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Sort | `arrow.up.arrow.down.circle` | `Icons.Filled.FilterList` |
| Flight | `airplane` | `Icons.Filled.Flight` |
| Stay | `bed.double` | `Icons.Filled.Hotel` |
| Car | `car` | `Icons.Filled.DirectionsCar` |
| Calendar | `calendar` | `Icons.Filled.CalendarMonth` |
| Travelers | `person.2` | `Icons.Filled.Group` |
| Price up | `arrow.up.right` | `Icons.Filled.ArrowUpward` |
| Price down | `arrow.down.right` | `Icons.Filled.ArrowDownward` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24**, `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; light canvas wants dark-content system bars (light-content in dark). The top bar respects the camera cutout; the sticky CTA respects the gesture-nav inset.
- **Font scaling**: `sp` honors the user's font scale — keep it on display, route, section, body, prices, leg times. Pin calendar day/price, the Hacker Fare tag, tab labels, and matrix cells via a fixed-density wrapper (the strip + grid are layout-sensitive).
- **Font choice**: KAYAK's product face is proprietary — use it only if licensed; Inter is the parity substitute. Apply `fontFeatureSettings = "tnum"` to every price/time/matrix style so columns align.
- **TalkBack**: label the fare card "{airlineName}, departs {departTime} {departCode}, arrives {arriveTime} {arriveCode}, {stopsText}, {price} dollars round-trip"; the calendar cell "{label}, {price} dollars, lowest/typical/highest"; the forecast banner reads title + subtitle; the Hacker Fare tag announced as "two separate one-way tickets".
- **Don't encode price band by color alone**: the calendar/matrix also encode cheapness via position; add `Modifier.semantics { contentDescription = "lowest price these dates" }` so colorblind users get the signal.
- **Touch targets**: Material guidance is 48.dp. Calendar cells (50.dp wide) and matrix rows should reach a 48.dp tap height; bottom-bar items are 48.dp; primary buttons ≥ 48.dp tall.
- **Contrast**: white on Orange `#FF690F` passes WCAG AA at button sizes; the price-signal green/red on white/dark pass AA for the small bold figures used.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, swap the fare-list `Crossfade` for an instant change; drop the price-drop pulse; keep the calendar fill (conveys selection).
- **Dark mode**: invert via the `Dark*` palette — `#101214` near-neutral, NOT true black, and intentionally with NO brand tint so the lone orange accent stays loud; `#16191C` text becomes `#E9ECEF`; pass `dark = true` into `kykPriceColor` so the `Mid` band resolves. Keep Orange + functional semantics fixed. Do **not** enable `dynamicColorScheme()` — KAYAK's single-accent identity must hold regardless of wallpaper.
