# Delta (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Delta's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the boarding pass + flight-status timeline, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Delta Blue navy, the navy→red boarding pass, the status-first trip header, calm functional motion) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+`. No color extraction — Delta's palette is a fixed brand system. Delta is light-mode-first; a full navy-charcoal dark scheme is provided.

## 1. Color Tokens (Compose + M3)

```kotlin
// ui/theme/DeltaColors.kt
import androidx.compose.ui.graphics.Color

object DeltaColors {
    // Brand
    val Blue        = Color(0xFF003268)
    val BlueBright  = Color(0xFF0F4C9A)
    val Red         = Color(0xFFC8102E)
    val RedPressed  = Color(0xFFA30D26)
    val WidgetMaroon = Color(0xFF862633)
    val SkyMilesGold = Color(0xFFC99700)

    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF4F6F9)
    val SurfacePressed = Color(0xFFE8ECF3)
    val DividerLight   = Color(0xFFD6DCE7)

    // Canvas & Surfaces (Dark) — deep navy-charcoal, NOT pure black
    val DarkCanvas   = Color(0xFF0B1320)
    val DarkSurface1 = Color(0xFF131C2B)
    val DarkSurface2 = Color(0xFF1C2738)
    val DarkDivider  = Color(0xFF25314A)

    // Text
    val TextPrimary       = Color(0xFF10243F)
    val TextSecondary     = Color(0xFF5A6A82)
    val TextTertiary      = Color(0xFF8A98AE)
    val DarkTextPrimary   = Color(0xFFE8ECF3)
    val DarkTextSecondary = Color(0xFF9AA6BC)
    val DarkTextTertiary  = Color(0xFF5F6E88)

    // Flight status (functional, constant)
    val OnTime       = Color(0xFF1E8E5A)
    val OnTimeDark   = Color(0xFF4FD18C)
    val Boarding     = Color(0xFF0F4C9A)
    val BoardingDark = Color(0xFF6FB0EA)
    val Delayed      = Color(0xFFC99700)
    val DelayedDark  = Color(0xFFE0B743)
    val Canceled     = Color(0xFFC8102E)
    val CanceledDark = Color(0xFFE0414E)
}
```

Wire it into both schemes. Delta is light-first; the dark scheme uses the signature `#0B1320` navy-charcoal, never true black, so the brand navy survives.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val DeltaLight = lightColorScheme(
    primary        = DeltaColors.Red,          // primary CTA — the single high-energy accent
    onPrimary      = Color.White,
    secondary      = DeltaColors.Blue,         // brand/navigation anchor
    onSecondary    = Color.White,
    background     = DeltaColors.Canvas,
    onBackground   = DeltaColors.TextPrimary,
    surface        = DeltaColors.SurfaceGray,
    onSurface      = DeltaColors.TextPrimary,
    surfaceVariant = DeltaColors.SurfacePressed,
    outline        = DeltaColors.DividerLight,
    error          = DeltaColors.Canceled,
)

private val DeltaDark = darkColorScheme(
    primary        = DeltaColors.Red,
    onPrimary      = Color.White,
    secondary      = DeltaColors.BlueBright,
    onSecondary    = Color.White,
    background     = DeltaColors.DarkCanvas,
    onBackground   = DeltaColors.DarkTextPrimary,
    surface        = DeltaColors.DarkSurface1,
    onSurface      = DeltaColors.DarkTextPrimary,
    surfaceVariant = DeltaColors.DarkSurface2,
    outline        = DeltaColors.DarkDivider,
    error          = DeltaColors.CanceledDark,
)

@Composable
fun DeltaTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) DeltaDark else DeltaLight,
    typography  = DeltaTypography,
    content     = content,
)
```

## 2. Typography (M3)

Delta uses a Whitney-class humanist sans; **Open Sans** (Apache 2.0) is the closest free analog. Drop the TTFs in `res/font/`. Data is bold and big; document the weight ladder 400/600/700/800.

```kotlin
// ui/theme/DeltaType.kt
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

object DeltaText {
    val RouteIATA   = TextStyle(OpenSans, fontWeight = FontWeight.ExtraBold, fontSize = 34.sp, lineHeight = 36.sp, letterSpacing = (-0.5).sp)
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

val DeltaTypography = Typography(
    headlineLarge  = DeltaText.ScreenTitle,
    headlineMedium = DeltaText.Section,
    titleMedium    = DeltaText.PaxName,
    bodyMedium     = DeltaText.Body,
    labelSmall     = DeltaText.Tab,
)
```

## 3. Signature Components (@Composable)

### Digital Boarding Pass

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

@Composable
fun BoardingPass(
    paxName: String, gate: String, seat: String, group: String,
    boardsAt: String, zone: String, dark: Boolean = true,
) {
    Column(
        Modifier
            .clip(RoundedCornerShape(18.dp))
            .background(DeltaColors.DarkSurface1)
            .border(1.dp, DeltaColors.DarkDivider, RoundedCornerShape(18.dp)),
    ) {
        Box(
            Modifier
                .fillMaxWidth().height(6.dp)
                .background(Brush.horizontalGradient(listOf(DeltaColors.Blue, DeltaColors.Red))),
        )
        Column(Modifier.padding(18.dp)) {
            Text("PASSENGER", style = DeltaText.CellLabel, color = DeltaColors.DarkTextSecondary)
            Text(paxName.uppercase(), style = DeltaText.PaxName, color = DeltaColors.DarkTextPrimary,
                modifier = Modifier.padding(top = 2.dp))

            Row(Modifier.padding(top = 16.dp), horizontalArrangement = Arrangement.spacedBy(18.dp)) {
                PassCell("GATE", gate, DeltaColors.DarkTextPrimary, Modifier.weight(1f))
                PassCell("SEAT", seat, DeltaColors.DarkTextPrimary, Modifier.weight(1f))
                PassCell("GROUP", group, DeltaColors.SkyMilesGold, Modifier.weight(1f))
            }

            Row(
                Modifier
                    .padding(top = 16.dp).fillMaxWidth()
                    .clip(RoundedCornerShape(10.dp))
                    .background(DeltaColors.DarkSurface2)
                    .padding(14.dp),
                horizontalArrangement = Arrangement.SpaceBetween,
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Text("Boards $boardsAt · Zone", style = DeltaText.DataInline.copy(fontWeight = FontWeight.SemiBold),
                    color = DeltaColors.DarkTextSecondary)
                Text(zone, style = DeltaText.DataInline.copy(fontWeight = FontWeight.Bold),
                    color = DeltaColors.DarkTextPrimary)
            }

            BarcodeStripe(dark = dark, modifier = Modifier.padding(top = 16.dp).fillMaxWidth().height(56.dp))
        }
    }
}

@Composable
private fun PassCell(label: String, value: String, valueColor: Color, modifier: Modifier = Modifier) {
    Column(modifier) {
        Text(label, style = DeltaText.CellLabel, color = DeltaColors.DarkTextTertiary)
        Text(value, style = DeltaText.DataLarge, color = valueColor, modifier = Modifier.padding(top = 3.dp))
    }
}

/** High-contrast bars; inverts to light bars in dark mode so a gate scanner reads a dimmed screen. */
@Composable
fun BarcodeStripe(dark: Boolean, modifier: Modifier = Modifier) {
    val bar = if (dark) DeltaColors.DarkTextPrimary else DeltaColors.TextPrimary
    Row(modifier.clip(RoundedCornerShape(8.dp))) {
        repeat(64) { i ->
            Box(Modifier.width(if (i % 3 == 0) 3.dp else 1.dp).fillMaxHeight().background(bar))
            Spacer(Modifier.width(2.dp))
        }
    }
}
```

### Flight Status Timeline

```kotlin
import androidx.compose.foundation.shape.CircleShape

data class FlightStep(val title: String, val meta: String, val done: Boolean)

@Composable
fun FlightStatusTimeline(steps: List<FlightStep>, modifier: Modifier = Modifier) {
    Column(
        modifier
            .clip(RoundedCornerShape(16.dp))
            .background(DeltaColors.DarkSurface1)
            .border(1.dp, DeltaColors.DarkDivider, RoundedCornerShape(16.dp))
            .padding(18.dp),
    ) {
        Text("Flight Status", style = DeltaText.DataInline.copy(fontWeight = FontWeight.Bold),
            color = DeltaColors.DarkTextPrimary, modifier = Modifier.padding(bottom = 16.dp))
        Row(horizontalArrangement = Arrangement.spacedBy(14.dp)) {
            Column(horizontalAlignment = Alignment.CenterHorizontally) {
                steps.forEachIndexed { i, s ->
                    Box(
                        Modifier.size(12.dp).clip(CircleShape)
                            .background(if (s.done) DeltaColors.OnTimeDark else DeltaColors.DarkTextSecondary)
                            .border(2.dp, if (s.done) DeltaColors.OnTimeDark else DeltaColors.DarkTextSecondary, CircleShape),
                    )
                    if (i < steps.lastIndex) {
                        Box(Modifier.width(2.dp).height(28.dp)
                            .background(DeltaColors.DarkDivider).padding(vertical = 2.dp))
                    }
                }
            }
            Column(verticalArrangement = Arrangement.spacedBy(18.dp)) {
                steps.forEach { s ->
                    Column {
                        Text(s.title, style = DeltaText.DataInline.copy(fontWeight = FontWeight.Bold),
                            color = DeltaColors.DarkTextPrimary)
                        Text(s.meta, style = DeltaText.Meta, color = DeltaColors.DarkTextSecondary,
                            modifier = Modifier.padding(top = 2.dp))
                    }
                }
            }
        }
    }
}
```

### Trip Header + Buttons + Status Chip

```kotlin
@Composable
fun TripHeader(flightNo: String, origin: String, dest: String, status: String) {
    Column(
        Modifier
            .fillMaxWidth()
            .background(Brush.verticalGradient(listOf(DeltaColors.Blue, Color(0xFF06203F))))
            .padding(horizontal = 18.dp).padding(top = 8.dp, bottom = 16.dp),
    ) {
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.CenterVertically) {
            Text("TODAY · $flightNo", style = DeltaText.Eyebrow.copy(letterSpacing = 1.sp),
                color = Color(0xFF8FB4DD))
            StatusChip(StatusKind.OnTime, status)
        }
        Row(Modifier.padding(top = 12.dp), verticalAlignment = Alignment.Bottom,
            horizontalArrangement = Arrangement.spacedBy(14.dp)) {
            Text(origin, style = DeltaText.RouteIATA, color = Color.White)
            Row(Modifier.weight(1f).padding(bottom = 8.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Box(Modifier.weight(1f).height(2.dp).background(Color.White.copy(alpha = 0.35f)))
                Icon(Icons.Filled.Flight, null, tint = Color.White, modifier = Modifier.size(18.dp))
                Box(Modifier.weight(1f).height(2.dp).background(Color.White.copy(alpha = 0.35f)))
            }
            Text(dest, style = DeltaText.RouteIATA, color = Color.White)
        }
    }
}

enum class StatusKind { OnTime, Boarding, Delayed, Canceled }

@Composable
fun StatusChip(kind: StatusKind, label: String) {
    val (fg, bg) = when (kind) {
        StatusKind.OnTime   -> DeltaColors.OnTimeDark   to DeltaColors.OnTime.copy(alpha = 0.18f)
        StatusKind.Boarding -> DeltaColors.BoardingDark to DeltaColors.Boarding.copy(alpha = 0.24f)
        StatusKind.Delayed  -> DeltaColors.DelayedDark  to DeltaColors.Delayed.copy(alpha = 0.18f)
        StatusKind.Canceled -> DeltaColors.CanceledDark to DeltaColors.Canceled.copy(alpha = 0.18f)
    }
    Text(label, style = DeltaText.StatusPill, color = fg,
        modifier = Modifier.clip(CircleShape).background(bg).padding(horizontal = 14.dp, vertical = 7.dp))
}

@Composable
fun DeltaPrimaryButton(title: String, onClick: () -> Unit) {
    androidx.compose.material3.Button(
        onClick = onClick,
        shape = RoundedCornerShape(6.dp),
        colors = androidx.compose.material3.ButtonDefaults.buttonColors(
            containerColor = DeltaColors.Red, contentColor = Color.White),
        modifier = Modifier.fillMaxWidth().height(48.dp),
    ) { Text(title, style = DeltaText.Button) }
}
```

## 4. Navigation

Delta has minimal chrome: a 5-tab bottom strip. On Android model it as a `NavigationBar` — no tint pill, active is the brand-blue icon + label.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*

@Composable
fun DeltaBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = DeltaColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Today"    to Icons.Filled.Home,
            "Book"     to Icons.Filled.Flight,
            "Trips"    to Icons.Filled.Receipt,
            "SkyMiles" to Icons.Filled.AccountCircle,
            "More"     to Icons.Filled.MoreHoriz,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = DeltaText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = DeltaColors.BoardingDark,
                    selectedTextColor   = DeltaColors.BoardingDark,
                    unselectedIconColor = DeltaColors.DarkTextTertiary,
                    unselectedTextColor = DeltaColors.DarkTextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill — Delta has none
                ),
            )
        }
    }
}
```

Use a `NavHost` for Today/Book/Trips/SkyMiles/More; a `ModalBottomSheet` for the seat map and fare details; push transitions are a 300ms horizontal slide.

## 5. Motion

Delta motion is calm and functional — never decorative bounce. Shadows signal "the thing you act on".

| Moment | Compose recipe |
|--------|----------------|
| Plane glide (header) | `animateFloatAsState` 0 → 1 `tween(1200, easing = FastOutSlowInEasing)`; offset the plane glyph along the line |
| Status change | pill color `animateColorAsState(tween(250))`; affected step `tween(300)`; soft haptic |
| Active timeline dot | `rememberInfiniteTransition` scale 1 → 1.15 `tween(1600)` reverse |
| Boarding pass open | `AnimatedVisibility` scale-in `spring(dampingRatio = 0.8f)`; raise screen brightness |
| Bag tracker advance | active dot fill `tween(300)` + light haptic on scan event |
| Tab switch | `Crossfade(tween(150))` |
| Page navigation | Nav slide push `tween(300)` |

```kotlin
val planeProgress by animateFloatAsState(if (visible) 1f else 0f,
    animationSpec = tween(1200, easing = FastOutSlowInEasing), label = "planeGlide")

val dotScale by rememberInfiniteTransition(label = "dot").animateFloat(
    initialValue = 1f, targetValue = 1.15f,
    animationSpec = infiniteRepeatable(tween(1600), RepeatMode.Reverse), label = "dotPulse")
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on status change and bag-tracker advance; on gate change use a stronger pattern via `view.performHapticFeedback(HapticFeedbackConstants.CONFIRM)`. Brightness: cache and raise via `WindowManager.LayoutParams.screenBrightness = 1f` while the full-screen pass is shown; restore on dismiss. Auto-refresh is silent.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Today (tab) | `house.fill` | `Icons.Filled.Home` |
| Book (tab) | `airplane` | `Icons.Filled.Flight` |
| Trips (tab) | `rectangle.stack.fill` | `Icons.Filled.Receipt` |
| SkyMiles (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| More (tab) | `ellipsis.circle.fill` | `Icons.Filled.MoreHoriz` |
| In-flight plane | `airplane` | `Icons.Filled.Flight` |
| Departed | `airplane.departure` | `Icons.Filled.FlightTakeoff` |
| Arrived | `airplane.arrival` | `Icons.Filled.FlightLand` |
| Gate change | `exclamationmark.triangle.fill` | `Icons.Filled.Warning` |
| Bag tracker | `suitcase.fill` | `Icons.Filled.Luggage` |
| Add to Wallet | `wallet.pass` | `Icons.Filled.AccountBalanceWallet` |
| Boarding pass | `qrcode` | `Icons.Filled.QrCode2` |
| Seat | `chair.lounge.fill` | `Icons.Filled.EventSeat` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Refresh status | `arrow.clockwise` | `Icons.Filled.Refresh` |
| Medallion tier | `star.circle.fill` | `Icons.Filled.Stars` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (`Popup`, modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the navy trip header draws under the status bar with light-content system icons; the boarding-pass barcode must stay above the navigation-bar inset.
- **Font scaling**: `sp` honors the user's font scale — keep it on route IATA, screen title, section, body, meta. Pin layout-sensitive text (barcode, 10sp cell labels, 10sp tab labels, status-pill text) via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. Data values scale one step then clamp to preserve the 3-cell grid.
- **TalkBack**: label the pass "Boarding pass, {name}, gate {gate}, seat {seat}, group {group}, boards {time}"; each timeline step "{title}, {meta}, {completed/upcoming}"; the status chip announces the full status word; the bag tracker announces the current step + carousel.
- **Touch targets**: Material guidance is 48.dp — primary button ≥ 48.dp; list rows 52.dp full-row tappable; the timeline step rows are full-row tappable for segment detail.
- **Contrast**: `#E8ECF3` on `#0B1320` and `#10243F` on `#FFFFFF` pass WCAG AA; the functional status pairs are validated against their translucent fills — re-check any custom delay/amber pair.
- **Brightness**: raise `WindowManager.LayoutParams.screenBrightness = 1f` while the full-screen pass is visible (gate scanning); restore the user's value on dismiss.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the plane glide and active-dot pulse; keep the status color change as an instant swap (it conveys state).
- **Dark mode**: invert via the `Dark*` palette — `#0B1320`, NOT true black, so the Delta navy survives; the barcode flips to a light bar pattern so it stays machine-scannable. Do **not** enable Material You `dynamicColorScheme()` — Delta's navy + widget-red identity must hold regardless of wallpaper.
