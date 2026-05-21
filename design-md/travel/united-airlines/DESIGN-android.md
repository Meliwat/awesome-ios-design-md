# United (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports United's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the boarding pass + interactive seat map, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the two-blue United Blue / Rhapsody Blue brand, the perforated boarding pass, the interactive seat map, refined functional motion) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+`. No color extraction — United's palette is a fixed two-blue brand system. United is light-mode-first; a full deep-navy dark scheme is provided.

## 1. Color Tokens (Compose + M3)

```kotlin
// ui/theme/UAColors.kt
import androidx.compose.ui.graphics.Color

object UAColors {
    // Brand
    val Blue          = Color(0xFF002244)
    val BlueMid       = Color(0xFF1B3D6E)
    val Rhapsody      = Color(0xFF1414FF)
    val RhapsodyPress = Color(0xFF0E0EC8)
    val RhapsodyBright = Color(0xFF4A4AFF)
    val PremierGold   = Color(0xFFC2A14D)
    val PremierGoldBright = Color(0xFFD8B863)

    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF3F5FA)
    val SurfacePressed = Color(0xFFE5EAF3)
    val DividerLight   = Color(0xFFD4DCE9)

    // Canvas & Surfaces (Dark) — deep navy, NOT pure black
    val DarkCanvas   = Color(0xFF0A1424)
    val DarkSurface1 = Color(0xFF111E33)
    val DarkSurface2 = Color(0xFF1A2A45)
    val DarkDivider  = Color(0xFF243652)

    // Text
    val TextPrimary       = Color(0xFF0C1F38)
    val TextSecondary     = Color(0xFF566884)
    val TextTertiary      = Color(0xFF8493AC)
    val DarkTextPrimary   = Color(0xFFE9EEF6)
    val DarkTextSecondary = Color(0xFF97A6BE)
    val DarkTextTertiary  = Color(0xFF5E7090)

    // Flight status (functional, constant)
    val OnTime       = Color(0xFF1E8E5A)
    val OnTimeDark   = Color(0xFF4FD18C)
    val Boarding     = Color(0xFF1414FF)
    val BoardingDark = Color(0xFF7A7AFF)
    val Delayed      = Color(0xFFC98A1E)
    val DelayedDark  = Color(0xFFE0A943)
    val Canceled     = Color(0xFFD8434F)
    val CanceledDark = Color(0xFFF0808A)
}
```

Wire it into both schemes. United is light-first; the dark scheme uses the signature `#0A1424` deep navy, never true black, so the brand navy survives.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val UALight = lightColorScheme(
    primary        = UAColors.Rhapsody,   // action accent
    onPrimary      = Color.White,
    secondary      = UAColors.Blue,       // brand/surface
    onSecondary    = Color.White,
    background     = UAColors.Canvas,
    onBackground   = UAColors.TextPrimary,
    surface        = UAColors.SurfaceGray,
    onSurface      = UAColors.TextPrimary,
    surfaceVariant = UAColors.SurfacePressed,
    outline        = UAColors.DividerLight,
    error          = UAColors.Canceled,
)

private val UADark = darkColorScheme(
    primary        = UAColors.Rhapsody,
    onPrimary      = Color.White,
    secondary      = UAColors.BlueMid,
    onSecondary    = Color.White,
    background     = UAColors.DarkCanvas,
    onBackground   = UAColors.DarkTextPrimary,
    surface        = UAColors.DarkSurface1,
    onSurface      = UAColors.DarkTextPrimary,
    surfaceVariant = UAColors.DarkSurface2,
    outline        = UAColors.DarkDivider,
    error          = UAColors.CanceledDark,
)

@Composable
fun UATheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) UADark else UALight,
    typography  = UATypography,
    content     = content,
)
```

## 2. Typography (M3)

United uses a clean humanist corporate sans; **Open Sans** (Apache 2.0) is the closest free analog. Drop the TTFs in `res/font/`. Data is bold and big; the weight ladder is 400/600/700/800.

```kotlin
// ui/theme/UAType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val OpenSans = FontFamily(
    Font(R.font.opensans_regular,   FontWeight.Normal),
    Font(R.font.opensans_medium,    FontWeight.Medium),
    Font(R.font.opensans_semibold,  FontWeight.SemiBold),
    Font(R.font.opensans_bold,      FontWeight.Bold),
    Font(R.font.opensans_extrabold, FontWeight.ExtraBold),
)

object UAText {
    val RouteIATA   = TextStyle(OpenSans, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 34.sp, letterSpacing = (-0.5).sp)
    val ScreenTitle = TextStyle(OpenSans, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 30.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(OpenSans, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val PaxName     = TextStyle(OpenSans, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 22.sp)
    val DataLarge   = TextStyle(OpenSans, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 24.sp)
    val Body        = TextStyle(OpenSans, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val DataInline  = TextStyle(OpenSans, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(OpenSans, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Eyebrow     = TextStyle(OpenSans, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.6.sp)
    val CellLabel   = TextStyle(OpenSans, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.6.sp)
    val Button      = TextStyle(OpenSans, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Tab         = TextStyle(OpenSans, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val StatusPill  = TextStyle(OpenSans, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
}

val UATypography = Typography(
    headlineLarge  = UAText.ScreenTitle,
    headlineMedium = UAText.Section,
    titleMedium    = UAText.PaxName,
    bodyMedium     = UAText.Body,
    labelSmall     = UAText.Tab,
)
```

## 3. Signature Components (@Composable)

### Boarding Pass (United Blue header + perforation)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.drawBehind
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.PathEffect
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.dp

@Composable
fun UABoardingPass(
    paxName: String, gate: String, seat: String, boards: String,
    premierGroup: String, dark: Boolean = true,
) {
    Column(
        Modifier
            .clip(RoundedCornerShape(16.dp))
            .background(UAColors.DarkSurface1)
            .border(1.dp, UAColors.DarkDivider, RoundedCornerShape(16.dp)),
    ) {
        Row(
            Modifier.fillMaxWidth().background(UAColors.Blue)
                .padding(horizontal = 18.dp, vertical = 12.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text("◍ UNITED", style = UAText.RouteIATA.copy(fontSize = 13.sp), color = Color.White)
            Text(premierGroup, style = UAText.Eyebrow, color = UAColors.PremierGoldBright)
        }
        Column(Modifier.padding(18.dp)) {
            Text("PASSENGER", style = UAText.CellLabel, color = UAColors.DarkTextSecondary)
            Text(paxName.uppercase(), style = UAText.PaxName, color = UAColors.DarkTextPrimary,
                modifier = Modifier.padding(top = 2.dp))
            Row(Modifier.padding(top = 16.dp), horizontalArrangement = Arrangement.spacedBy(16.dp)) {
                PassCell("GATE", gate, UAColors.DarkTextPrimary, Modifier.weight(1f))
                PassCell("SEAT", seat, UAColors.DarkTextPrimary, Modifier.weight(1f))
                PassCell("BOARDS", boards, UAColors.PremierGoldBright, Modifier.weight(1f))
            }
            Perforation(Modifier.padding(vertical = 16.dp))
            BarcodeStripe(dark, Modifier.fillMaxWidth().height(54.dp))
        }
    }
}

@Composable
private fun PassCell(label: String, value: String, valueColor: Color, modifier: Modifier = Modifier) {
    Column(modifier) {
        Text(label, style = UAText.CellLabel, color = UAColors.DarkTextTertiary)
        Text(value, style = UAText.DataLarge, color = valueColor, modifier = Modifier.padding(top = 3.dp))
    }
}

/** Dashed line with circular notch cut-outs at the edges — the "tear here" affordance. */
@Composable
fun Perforation(modifier: Modifier = Modifier) {
    Box(modifier.fillMaxWidth().height(20.dp), contentAlignment = Alignment.Center) {
        Box(
            Modifier.fillMaxWidth().height(1.dp).drawBehind {
                drawLine(
                    color = UAColors.DarkDivider,
                    start = Offset(0f, size.height / 2),
                    end = Offset(size.width, size.height / 2),
                    pathEffect = PathEffect.dashPathEffect(floatArrayOf(12f, 10f)),
                )
            },
        )
        Box(Modifier.size(20.dp).offset(x = (-28).dp).clip(androidx.compose.foundation.shape.CircleShape).background(UAColors.DarkCanvas)
            .align(Alignment.CenterStart))
        Box(Modifier.size(20.dp).offset(x = 28.dp).clip(androidx.compose.foundation.shape.CircleShape).background(UAColors.DarkCanvas)
            .align(Alignment.CenterEnd))
    }
}

/** High-contrast bars; inverts to light bars in dark mode so a gate scanner reads a dimmed screen. */
@Composable
fun BarcodeStripe(dark: Boolean, modifier: Modifier = Modifier) {
    val bar = if (dark) UAColors.DarkTextPrimary else UAColors.TextPrimary
    Row(modifier.clip(RoundedCornerShape(8.dp))) {
        repeat(64) { i ->
            Box(Modifier.width(if (i % 3 == 0) 3.dp else 1.dp).fillMaxHeight().background(bar))
            Spacer(Modifier.width(2.dp))
        }
    }
}
```

### Interactive Seat Map

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.runtime.*
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

enum class SeatKind { Standard, EconPlus, Taken, Selected, Aisle }

@Composable
fun SeatMap(rows: List<List<SeatKind>>) {
    var selected by remember { mutableStateOf("7A") }
    val haptics = LocalHapticFeedback.current
    Column(
        Modifier
            .clip(RoundedCornerShape(16.dp)).background(UAColors.DarkSurface1)
            .border(1.dp, UAColors.DarkDivider, RoundedCornerShape(16.dp)).padding(18.dp),
    ) {
        Text("Choose your seat", style = UAText.DataInline.copy(fontWeight = FontWeight.Bold),
            color = UAColors.DarkTextPrimary)
        Text("Economy Plus · Row 7–9 · $selected selected", style = UAText.Meta,
            color = UAColors.DarkTextSecondary, modifier = Modifier.padding(top = 4.dp, bottom = 16.dp))
        Column(verticalArrangement = Arrangement.spacedBy(6.dp)) {
            rows.forEach { row ->
                Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                    row.forEach { kind ->
                        SeatCell(kind, Modifier.weight(1f)) {
                            if (kind == SeatKind.Standard || kind == SeatKind.EconPlus) {
                                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                            }
                        }
                    }
                }
            }
        }
        Row(Modifier.padding(top = 16.dp), horizontalArrangement = Arrangement.spacedBy(16.dp)) {
            Legend(UAColors.Rhapsody.copy(alpha = 0.4f), "Econ+")
            Legend(UAColors.Rhapsody, "Selected")
            Legend(UAColors.DarkCanvas, "Taken")
        }
    }
}

@Composable
private fun SeatCell(kind: SeatKind, modifier: Modifier = Modifier, onTap: () -> Unit) {
    if (kind == SeatKind.Aisle) { Box(modifier.aspectRatio(1f)); return }
    val fill = when (kind) {
        SeatKind.Selected -> UAColors.Rhapsody
        SeatKind.EconPlus -> UAColors.Rhapsody.copy(alpha = 0.18f)
        SeatKind.Taken    -> UAColors.DarkCanvas
        else              -> UAColors.DarkSurface2
    }
    val border = when (kind) {
        SeatKind.Selected -> UAColors.RhapsodyBright
        SeatKind.EconPlus -> UAColors.RhapsodyBright.copy(alpha = 0.4f)
        else              -> UAColors.DarkDivider
    }
    Box(
        modifier
            .aspectRatio(1f)
            .clip(RoundedCornerShape(topStart = 6.dp, topEnd = 6.dp, bottomStart = 3.dp, bottomEnd = 3.dp))
            .background(fill)
            .border(1.dp, border, RoundedCornerShape(topStart = 6.dp, topEnd = 6.dp, bottomStart = 3.dp, bottomEnd = 3.dp))
            .clickable { onTap() },
    )
}

@Composable
private fun Legend(c: Color, t: String) {
    Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
        Box(Modifier.size(10.dp).clip(RoundedCornerShape(3.dp)).background(c))
        Text(t, style = UAText.Eyebrow.copy(fontWeight = FontWeight.Normal, fontSize = 11.sp),
            color = UAColors.DarkTextSecondary)
    }
}
```

### Flight Detail Header + Buttons + Status Chip

```kotlin
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.graphics.Brush
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Flight

@Composable
fun FlightDetailHeader(
    flightNo: String, origin: String, originCity: String, dest: String,
    destCity: String, dur: String, depRow: String, arrRow: String, status: String,
) {
    Column(
        Modifier
            .fillMaxWidth()
            .background(Brush.linearGradient(listOf(UAColors.Blue, Color(0xFF04152C))))
            .padding(horizontal = 18.dp).padding(top = 8.dp, bottom = 18.dp),
    ) {
        Row(Modifier.fillMaxWidth(), Arrangement.SpaceBetween, Alignment.CenterVertically) {
            Text(flightNo.uppercase(), style = UAText.Eyebrow.copy(letterSpacing = 1.sp),
                color = Color(0xFF7FA0CE))
            StatusChip(StatusKind.OnTime, status)
        }
        Row(Modifier.padding(top = 14.dp)) {
            Column {
                Text(origin, style = UAText.RouteIATA, color = Color.White)
                Text(originCity, style = UAText.Meta, color = Color(0xFF9FBADC))
            }
            Column(Modifier.weight(1f).padding(top = 8.dp), horizontalAlignment = Alignment.CenterHorizontally) {
                Icon(Icons.Filled.Flight, null, tint = Color.White, modifier = Modifier.size(20.dp))
                Text(dur, style = UAText.Meta.copy(fontSize = 11.sp), color = Color(0xFF9FBADC))
            }
            Column(horizontalAlignment = Alignment.End) {
                Text(dest, style = UAText.RouteIATA, color = Color.White)
                Text(destCity, style = UAText.Meta, color = Color(0xFF9FBADC))
            }
        }
        Row(Modifier.fillMaxWidth().padding(top = 6.dp), Arrangement.SpaceBetween) {
            Text(depRow, style = UAText.DataInline, color = Color(0xFFCFE0F2))
            Text(arrRow, style = UAText.DataInline, color = Color(0xFFCFE0F2))
        }
    }
}

enum class StatusKind { OnTime, Boarding, Delayed, Canceled }

@Composable
fun StatusChip(kind: StatusKind, label: String) {
    val (fg, bg) = when (kind) {
        StatusKind.OnTime   -> UAColors.OnTimeDark   to UAColors.OnTime.copy(alpha = 0.18f)
        StatusKind.Boarding -> UAColors.BoardingDark to UAColors.Boarding.copy(alpha = 0.22f)
        StatusKind.Delayed  -> UAColors.DelayedDark  to UAColors.Delayed.copy(alpha = 0.18f)
        StatusKind.Canceled -> UAColors.CanceledDark to UAColors.Canceled.copy(alpha = 0.18f)
    }
    Text(label, style = UAText.StatusPill, color = fg,
        modifier = Modifier.clip(CircleShape).background(bg).padding(horizontal = 14.dp, vertical = 7.dp))
}

@Composable
fun UAPrimaryButton(title: String, onClick: () -> Unit) {
    androidx.compose.material3.Button(
        onClick = onClick,
        shape = RoundedCornerShape(4.dp),  // crisp, low-radius
        colors = androidx.compose.material3.ButtonDefaults.buttonColors(
            containerColor = UAColors.Rhapsody, contentColor = Color.White),
        modifier = Modifier.fillMaxWidth().height(48.dp),
    ) { Text(title, style = UAText.Button) }
}
```

## 4. Navigation

United has minimal chrome: a 5-tab bottom strip. On Android model it as a `NavigationBar` — no tint pill, active is the Rhapsody-blue icon + label.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun UABottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = UAColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"        to Icons.Filled.Home,
            "Book"        to Icons.Filled.Search,
            "Flights"     to Icons.Filled.Flight,
            "Trips"       to Icons.Filled.Receipt,
            "MileagePlus" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = UAText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = UAColors.RhapsodyBright,
                    selectedTextColor   = UAColors.RhapsodyBright,
                    unselectedIconColor = UAColors.DarkTextTertiary,
                    unselectedTextColor = UAColors.DarkTextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill — United has none
                ),
            )
        }
    }
}
```

Use a `NavHost` for Home/Book/Flights/Trips/MileagePlus; a `ModalBottomSheet` for fare details and the upgrade list; push transitions are a 300ms horizontal slide.

## 5. Motion

United motion is refined — never decorative bounce. Shadows signal "the thing you act on / hold up at the gate".

| Moment | Compose recipe |
|--------|----------------|
| Aircraft glide (header) | `animateFloatAsState` 0 → 1 `tween(900, easing = FastOutSlowInEasing)` |
| Seat select | `animateFloatAsState` scale 0.9 → 1 `spring(dampingRatio = 0.6f)` + light haptic |
| Status change | pill color `animateColorAsState(tween(250))`; affected row `tween(300)`; soft haptic |
| Boarding pass open | `AnimatedVisibility` scale-in `spring(dampingRatio = 0.8f)`; raise screen brightness |
| Bag tracker advance | active dot fill `tween(300)` + light haptic on scan event |
| Tab switch | `Crossfade(tween(150))` |
| Page navigation | Nav slide push `tween(300)` |

```kotlin
val seatScale by animateFloatAsState(if (selected) 1f else 0.9f,
    animationSpec = spring(dampingRatio = 0.6f, stiffness = Spring.StiffnessMedium), label = "seat")

val aircraftProgress by animateFloatAsState(if (visible) 1f else 0f,
    animationSpec = tween(900, easing = FastOutSlowInEasing), label = "aircraftGlide")
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on seat select and status change; on gate change use `view.performHapticFeedback(HapticFeedbackConstants.CONFIRM)`. Brightness: cache and raise via `WindowManager.LayoutParams.screenBrightness = 1f` while the full-screen pass is shown; restore on dismiss. Auto-refresh is silent.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Book (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Flights (tab) | `airplane` | `Icons.Filled.Flight` |
| Trips (tab) | `rectangle.stack.fill` | `Icons.Filled.Receipt` |
| MileagePlus (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| Aircraft (header) | `airplane` | `Icons.Filled.Flight` |
| Departed | `airplane.departure` | `Icons.Filled.FlightTakeoff` |
| Arrived | `airplane.arrival` | `Icons.Filled.FlightLand` |
| Seat | `chair.lounge.fill` | `Icons.Filled.EventSeat` |
| Bag tracker | `suitcase.fill` | `Icons.Filled.Luggage` |
| Add to Wallet | `wallet.pass` | `Icons.Filled.AccountBalanceWallet` |
| Boarding pass | `qrcode` | `Icons.Filled.QrCode2` |
| Upgrade list | `arrow.up.circle.fill` | `Icons.Filled.Upgrade` |
| Gate change | `exclamationmark.triangle.fill` | `Icons.Filled.Warning` |
| Premier tier | `star.circle.fill` | `Icons.Filled.Stars` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (`Popup`, modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the navy flight-detail header draws under the status bar with light-content system icons; the boarding-pass barcode must stay above the navigation-bar inset.
- **Font scaling**: `sp` honors the user's font scale — keep it on route IATA, screen title, section, body, meta. Pin layout-sensitive text (barcode, seat-map cells, 10sp cell labels, 10sp tab labels, status-pill text) via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. Data values scale one step then clamp to preserve the 3-cell grid.
- **TalkBack**: label the pass "Boarding pass, {name}, gate {gate}, seat {seat}, boards {time}, {premierGroup}"; each seat "Seat {id}, {Economy Plus / standard / taken / selected}, {row}"; the status chip announces the full status word; the bag tracker announces the current step + carousel.
- **Touch targets**: Material guidance is 48.dp — primary button ≥ 48.dp; seat cells ≥ 32.dp tap (expand small cabins); list rows 52.dp full-row tappable.
- **Contrast**: `#E9EEF6` on `#0A1424` and `#0C1F38` on `#FFFFFF` pass WCAG AA; the functional status pairs are validated against their translucent fills — re-check any custom amber pair.
- **Brightness**: raise `WindowManager.LayoutParams.screenBrightness = 1f` while the full-screen pass is visible (gate scanning); restore the user's value on dismiss.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the aircraft glide and seat-pop spring; keep the status color change as an instant swap and the selected-seat state (they convey state).
- **Dark mode**: invert via the `Dark*` palette — `#0A1424`, NOT true black, so the United navy survives; the barcode flips to a light bar pattern so it stays machine-scannable. Do **not** enable Material You `dynamicColorScheme()` — United's two-blue identity must hold regardless of wallpaper.
