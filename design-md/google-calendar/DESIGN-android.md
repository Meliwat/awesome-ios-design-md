# Google Calendar (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Google Calendar's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (event card, Schedule day banner, FAB, Month grid), `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? Google Calendar *is* a Material app — the iOS build is Material You wearing SF Pro. On Android this guide goes home: a real `FloatingActionButton` at Material elevation, the Material dual-shadow as `tonalElevation`/`shadowElevation`, and `sp`/`dp` instead of `pt`. Because it is Material-native, this is one of the few apps where you should **embrace `dynamicColorScheme()`** (see §8).

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. (No remote images, so Coil is optional.)

## 1. Color Tokens

```kotlin
// ui/theme/GCalColors.kt
import androidx.compose.ui.graphics.Color

object GCalColors {
    // Canvas & Surfaces
    val Canvas       = Color(0xFFFFFFFF)
    val SurfaceGray  = Color(0xFFF1F3F4)
    val SurfaceGray2 = Color(0xFFF8F9FA)
    val Divider      = Color(0xFFDADCE0)

    // Text
    val Ink       = Color(0xFF202124) // Google's warm near-black, not #000
    val Secondary = Color(0xFF5F6368)
    val Tertiary  = Color(0xFF80868B)

    // Brand & Actions
    val Blue        = Color(0xFF1A73E8)
    val BluePressed = Color(0xFF1557B0)
    val BlueTint    = Color(0xFFE8F0FE) // selected rows / Today ring bg
    val BlueDark    = Color(0xFF8AB4F8) // brighter blue for dark mode

    // Event Colors (Material primary set)
    val EventBlue   = Color(0xFF1A73E8)
    val EventRed    = Color(0xFFD93025)
    val EventYellow = Color(0xFFF9AB00)
    val EventGreen  = Color(0xFF188038)

    // 24-color user-selectable calendar palette
    val Tomato     = Color(0xFFD50000)
    val Flamingo   = Color(0xFFE67C73)
    val Tangerine  = Color(0xFFF4511E)
    val Banana     = Color(0xFFF6BF26)
    val Sage       = Color(0xFF33B679)
    val Basil      = Color(0xFF0B8043)
    val Peacock    = Color(0xFF039BE5)
    val Blueberry  = Color(0xFF3F51B5)
    val Lavender   = Color(0xFF7986CB)
    val Grape      = Color(0xFF8E24AA)
    val Graphite   = Color(0xFF616161)
    val Birch      = Color(0xFFA79B8E)
    val Eucalyptus = Color(0xFF16A765)
    val Cobalt     = Color(0xFF0277BD)
    val CherryBlossom = Color(0xFFFAD165)
    val Avocado    = Color(0xFF7CB342)
    val Citron     = Color(0xFFC0CA33)
    val Pistachio  = Color(0xFF9CCC65)
    val Wisteria   = Color(0xFF5C6BC0)
    val Amethyst   = Color(0xFFAB47BC)
    val Rose       = Color(0xFFD81B60)
    val Cocoa      = Color(0xFF795548)
    val Slate      = Color(0xFF9E9E9E)

    // Dark Mode (Google's warm near-black, NOT pure black)
    val DarkCanvas   = Color(0xFF202124)
    val DarkSurface1 = Color(0xFF2D2E30)
    val DarkSurface2 = Color(0xFF3C4043)
    val DarkDivider  = Color(0xFF3C4043)
    val DarkText     = Color(0xFFE8EAED)
    val DarkTextSec  = Color(0xFF9AA0A6)

    // Material warm shadow tint (not pure black — a Google detail)
    val ShadowTint = Color(0xFF3C4043)
}
```

Wire it into a Material 3 scheme. Dark mode is `#202124` charcoal; the blue brightens to `#8AB4F8`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.platform.LocalContext
import android.os.Build

private val GCalLight = lightColorScheme(
    primary           = GCalColors.Blue,
    onPrimary         = Color.White,
    background         = GCalColors.Canvas,
    onBackground       = GCalColors.Ink,
    surface            = GCalColors.Canvas,
    onSurface          = GCalColors.Ink,
    surfaceVariant     = GCalColors.SurfaceGray,
    secondaryContainer = GCalColors.BlueTint, // selected drawer row / Today ring bg
    outline            = GCalColors.Divider,
    error              = GCalColors.EventRed,
)

private val GCalDark = darkColorScheme(
    primary           = GCalColors.BlueDark,
    onPrimary         = GCalColors.DarkCanvas,
    background         = GCalColors.DarkCanvas,
    onBackground       = GCalColors.DarkText,
    surface            = GCalColors.DarkSurface1,
    onSurface          = GCalColors.DarkText,
    surfaceVariant     = GCalColors.DarkSurface2,
    secondaryContainer = GCalColors.BlueDark.copy(alpha = 0.24f),
    outline            = GCalColors.DarkDivider,
    error              = GCalColors.EventRed,
)

@Composable
fun GCalTheme(
    dark: Boolean = isSystemInDarkTheme(),
    dynamic: Boolean = true,  // Material-native Google app — Material You ON by default
    content: @Composable () -> Unit,
) {
    val ctx = LocalContext.current
    val scheme = when {
        dynamic && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S ->
            if (dark) dynamicDarkColorScheme(ctx) else dynamicLightColorScheme(ctx)
        dark -> GCalDark
        else -> GCalLight
    }
    MaterialTheme(colorScheme = scheme, typography = GCalTypography, content = content)
}
```

## 2. Typography

**Google Sans** for nav titles and prominent moments; **Roboto** (Android's system default — what iOS deliberately falls back to SF Pro for) for body and rows. Drop Google Sans TTFs in `res/font/`. Calendar avoids Bold — hierarchy is size + color, weights are Regular (400) and Medium (500). Times use tabular figures (`tnum`) — non-negotiable for column alignment.

```kotlin
// ui/theme/GCalType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val GoogleSans = FontFamily(
    Font(R.font.google_sans_regular, FontWeight.Normal),  // 400
    Font(R.font.google_sans_medium,  FontWeight.Medium),  // 500
)
val Roboto = FontFamily(
    Font(R.font.roboto_regular, FontWeight.Normal),
    Font(R.font.roboto_medium,  FontWeight.Medium),
)
const val TNUM = "tnum" // tabular figures — apply to every time / day number

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, line-height = size × ratio)
object GCalText {
    val NavTitle      = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 22.sp, lineHeight = 26.sp)
    val DayNumberLg   = TextStyle(GoogleSans, fontWeight = FontWeight.Normal, fontSize = 36.sp, lineHeight = 36.sp, letterSpacing = (-0.4).sp)
    val DayLabel      = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.8.sp)
    val SectionHeader = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 18.sp)
    val EventTitle    = TextStyle(Roboto,     fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 18.sp)
    val EventDetail   = TextStyle(GoogleSans, fontWeight = FontWeight.Normal, fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.1).sp)
    val EventTime     = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 16.sp, fontFeatureSettings = TNUM)
    val EventLocation = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val BlockTitle    = TextStyle(Roboto,     fontWeight = FontWeight.Medium, fontSize = 12.sp, lineHeight = 16.sp)
    val BlockTime     = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 13.sp, fontFeatureSettings = TNUM)
    val DayNumber     = TextStyle(GoogleSans, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 14.sp, fontFeatureSettings = TNUM)
    val TodayNumber   = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 14.sp)
    val WeekdayHeader = TextStyle(Roboto,     fontWeight = FontWeight.Medium, fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.4.sp)
    val TimeGutter    = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 11.sp, fontFeatureSettings = TNUM)
    val Sidebar       = TextStyle(Roboto,     fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 18.sp)
    val Button        = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 14.sp, letterSpacing = 0.4.sp)
    val TabLabel      = TextStyle(Roboto,     fontWeight = FontWeight.Medium, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Caption       = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val GCalTypography = Typography(
    headlineSmall = GCalText.EventDetail,
    titleLarge    = GCalText.NavTitle,
    titleMedium   = GCalText.SectionHeader,
    bodyMedium    = GCalText.EventTitle,
    labelLarge    = GCalText.Button,
    labelSmall    = GCalText.TabLabel,
)
```

## 3. Signature Components

### Event Card (Schedule View — hero component)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Place
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp

@Composable
fun EventCard(
    title: String,
    timeRange: String,           // "9:00 – 9:30 AM"
    location: String?,
    calendarColor: Color,        // e.g., GCalColors.Blueberry
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Surface(
        modifier = modifier.fillMaxWidth().heightIn(min = 56.dp),
        shape = RoundedCornerShape(4.dp), // Material compact-card radius — never 6dp+
        color = MaterialTheme.colorScheme.surface,
        // Material Level 1 dual-shadow ≈ a soft elevation; warm tint via shadow
        shadowElevation = 1.dp,
        tonalElevation = 1.dp,
        onClick = onClick,
    ) {
        Row(Modifier.fillMaxWidth(), verticalAlignment = Alignment.Top) {
            // 4dp left color bar — the calendar-source indicator
            Box(Modifier.width(4.dp).fillMaxHeight().background(calendarColor))
            Column(
                Modifier.weight(1f).padding(horizontal = 16.dp, vertical = 12.dp),
                verticalArrangement = Arrangement.spacedBy(4.dp),
            ) {
                Text(title, style = GCalText.EventTitle, color = GCalColors.Ink, maxLines = 2, overflow = TextOverflow.Ellipsis)
                Text(timeRange, style = GCalText.EventTime, color = GCalColors.Secondary)
                if (location != null) {
                    Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                        Icon(Icons.Filled.Place, null, tint = GCalColors.Secondary, modifier = Modifier.size(11.dp))
                        Text(location, style = GCalText.EventLocation, color = GCalColors.Secondary, maxLines = 1, overflow = TextOverflow.Ellipsis)
                    }
                }
            }
        }
    }
}
```

### Schedule Day Banner

```kotlin
@Composable
fun DayBanner(
    dayNumber: String,           // "14"
    dayLabel: String,            // "THU · MAY"
    weatherIcon: androidx.compose.ui.graphics.vector.ImageVector?,
    temperature: String?,
    isToday: Boolean,
) {
    Row(
        Modifier.fillMaxWidth().height(80.dp).padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Column(verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(dayLabel, style = GCalText.DayLabel, color = GCalColors.Secondary)
            if (isToday) {
                Box(Modifier.size(56.dp).clip(CircleShape).background(GCalColors.Blue), contentAlignment = Alignment.Center) {
                    Text(dayNumber, style = GCalText.DayNumberLg, color = Color.White)
                }
            } else {
                Text(dayNumber, style = GCalText.DayNumberLg, color = GCalColors.Ink)
            }
        }
        Spacer(Modifier.weight(1f))
        if (weatherIcon != null && temperature != null) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                Icon(weatherIcon, null, tint = GCalColors.EventYellow, modifier = Modifier.size(20.dp))
                Text(temperature, style = GCalText.EventTitle, color = GCalColors.Ink)
            }
        }
    }
}
```

### Floating Action Button (Material elevation)

Use the real Material `FloatingActionButton`. Material does **not** scale the FAB on press — it lifts via elevation. A pressed `FloatingActionButton` already animates `pressedElevation`, so let the component do it.

```kotlin
import androidx.compose.material.icons.filled.Add
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun GCalFab(onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    FloatingActionButton(
        onClick = {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .impact(.medium)
            onClick()
        },
        containerColor = MaterialTheme.colorScheme.primary, // #1A73E8 / #8AB4F8
        contentColor = Color.White,
        shape = CircleShape,
        elevation = FloatingActionButtonDefaults.elevation(
            defaultElevation = 6.dp,  // Material Level 6 — the highest in the app
            pressedElevation = 12.dp, // lifts to Level 12 on press (no scale)
        ),
        modifier = modifier,
    ) {
        Icon(Icons.Filled.Add, contentDescription = "Create", modifier = Modifier.size(24.dp))
    }
}
```

### Month Grid Cell

```kotlin
@Composable
fun MonthCell(
    day: Int,
    isToday: Boolean,
    isCurrentMonth: Boolean,
    eventColors: List<Color>, // up to 3 source-calendar dots
    modifier: Modifier = Modifier,
) {
    Column(
        modifier = modifier
            .fillMaxWidth()
            .heightIn(min = 52.dp)
            .drawBehind {
                drawLine(GCalColors.Divider, androidx.compose.ui.geometry.Offset(0f, 0f), androidx.compose.ui.geometry.Offset(size.width, 0f), 0.5.dp.toPx())
            },
        verticalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        if (isToday) {
            Box(Modifier.padding(2.dp).size(28.dp).clip(CircleShape).background(GCalColors.Blue), contentAlignment = Alignment.Center) {
                Text("$day", style = GCalText.TodayNumber, color = Color.White)
            }
        } else {
            Text(
                "$day",
                style = GCalText.DayNumber,
                color = if (isCurrentMonth) GCalColors.Ink else GCalColors.Tertiary,
                modifier = Modifier.padding(start = 6.dp, top = 6.dp),
            )
        }
        Row(Modifier.padding(start = 6.dp), horizontalArrangement = Arrangement.spacedBy(2.dp)) {
            eventColors.take(3).forEach { c ->
                Box(Modifier.size(4.dp).clip(CircleShape).background(c))
            }
            if (eventColors.size > 3) {
                Text("+${eventColors.size - 3}", style = GCalText.Caption.copy(fontSize = 9.sp), color = GCalColors.Tertiary)
            }
        }
    }
}
```

### Day/Week Tinted Event Block + RSVP Pills

```kotlin
@Composable
fun TintedEventBlock(title: String, timeRange: String, calendarColor: Color, height: androidx.compose.ui.unit.Dp) {
    Row(
        Modifier
            .fillMaxWidth()
            .height(height)
            .clip(RoundedCornerShape(4.dp))
            .background(calendarColor.copy(alpha = 0.20f)), // 20% fill, full-sat title
    ) {
        Box(Modifier.width(3.dp).fillMaxHeight().background(calendarColor))
        Column(Modifier.padding(horizontal = 6.dp, vertical = 4.dp), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(title, style = GCalText.BlockTitle, color = calendarColor, maxLines = 1, overflow = TextOverflow.Ellipsis)
            if (height > 32.dp) Text(timeRange, style = GCalText.BlockTime, color = calendarColor.copy(alpha = 0.85f))
        }
    }
}

@Composable
fun RSVPPills(selected: String, onSelect: (String) -> Unit) {
    Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.spacedBy(8.dp)) {
        listOf("Yes", "No", "Maybe").forEach { option ->
            val isSel = selected == option
            Box(
                Modifier
                    .weight(1f)
                    .height(40.dp)
                    .clip(CircleShape)
                    .background(if (isSel) GCalColors.Blue else Color.Transparent)
                    .border(if (isSel) 0.dp else 1.dp, if (isSel) Color.Transparent else GCalColors.Divider, CircleShape)
                    .clickable { onSelect(option) },
                contentAlignment = Alignment.Center,
            ) {
                Text(option, style = GCalText.EventTitle, color = if (isSel) Color.White else GCalColors.Secondary)
            }
        }
    }
}
```

## 4. Google-Calendar-Specific Feature: FAB Action Menu + Concentric Ripple

Tapping the FAB opens an action menu (Event / Task / Goal) that shares an element with the FAB position. Compose's `FloatingActionButton` already paints the Material ripple from the touch point — that *is* the concentric expansion. The menu rises as a `ModalBottomSheet` morphing from the FAB; on Compose 1.7+ use `SharedTransitionLayout` to morph the FAB into the sheet.

```kotlin
import androidx.compose.material.icons.filled.Event
import androidx.compose.material.icons.filled.TaskAlt
import androidx.compose.material.icons.filled.Flag
import androidx.compose.material3.ExperimentalMaterial3Api
import androidx.compose.material3.ModalBottomSheet
import androidx.compose.material3.rememberModalBottomSheetState

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun FabWithActionMenu(onCreate: (String) -> Unit) {
    var showMenu by remember { mutableStateOf(false) }
    val sheetState = rememberModalBottomSheetState()

    GCalFab(onClick = { showMenu = true })

    if (showMenu) {
        ModalBottomSheet(onDismissRequest = { showMenu = false }, sheetState = sheetState) {
            val actions = listOf(
                Triple("Event", Icons.Filled.Event, GCalColors.EventBlue),
                Triple("Task",  Icons.Filled.TaskAlt, GCalColors.EventYellow),
                Triple("Goal",  Icons.Filled.Flag, GCalColors.EventGreen),
            )
            actions.forEach { (label, icon, tint) ->
                Row(
                    Modifier
                        .fillMaxWidth()
                        .height(48.dp)
                        .clickable { showMenu = false; onCreate(label) }
                        .padding(horizontal = 24.dp),
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(16.dp),
                ) {
                    Icon(icon, contentDescription = null, tint = tint, modifier = Modifier.size(24.dp))
                    Text(label, style = GCalText.Sidebar, color = GCalColors.Ink)
                }
            }
        }
    }
}
```

The **current-time indicator** in Day/Week view is a 2.dp `#D93025` line with a 10.dp leading dot; recompute its `y` offset every 60s from a `LaunchedEffect(Unit) { while (true) { tick = now(); delay(60_000) } }` and animate the position with a 1s `tween`.

## 5. Navigation

Google Calendar's primary navigation on phones is a **left drawer** (Schedule / Day / 3-Day / Week / Month), not a tab bar — use `ModalNavigationDrawer`. The bottom tab bar appears mainly on tablets; when present use Material 3 `NavigationBar`. Android has no live blur, so the sticky app bar is a 92%-opaque canvas `Surface`.

```kotlin
@Composable
fun GCalDrawerRow(icon: androidx.compose.ui.graphics.vector.ImageVector, label: String, selected: Boolean, onClick: () -> Unit) {
    Row(
        Modifier
            .fillMaxWidth()
            .height(48.dp)
            .clip(RoundedCornerShape(0.dp))
            .background(if (selected) MaterialTheme.colorScheme.secondaryContainer else Color.Transparent)
            .clickable(onClick = onClick)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        Icon(icon, contentDescription = null, tint = if (selected) GCalColors.Blue else GCalColors.Secondary, modifier = Modifier.size(24.dp))
        Text(label, style = GCalText.Sidebar, color = if (selected) GCalColors.Blue else GCalColors.Ink)
    }
}

@Composable
fun GCalTabletBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = MaterialTheme.colorScheme.surface, tonalElevation = 0.dp) {
        val items = listOf(
            "Schedule" to Icons.AutoMirrored.Filled.ViewList,
            "Day"      to Icons.Filled.CalendarViewDay,
            "Week"     to Icons.Filled.CalendarViewWeek,
            "Month"    to Icons.Filled.CalendarMonth,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = GCalText.TabLabel) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = GCalColors.Blue,
                    selectedTextColor   = GCalColors.Blue,
                    unselectedIconColor = GCalColors.Secondary,
                    unselectedTextColor = GCalColors.Secondary,
                    indicatorColor      = GCalColors.BlueTint,
                ),
            )
        }
    }
}
```

The drawer "My calendars" rows pair an 18.dp color square checkbox with the calendar name; tapping toggles visibility (filled square = visible, outlined = hidden). Selected view rows use `secondaryContainer` (= `#E8F0FE`).

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| FAB tap | Material ripple from touch point (the concentric expand) + `pressedElevation` lift + `HapticFeedbackType.LongPress` (≈ `.impact(.medium)`) |
| Action menu | `ModalBottomSheet` rises from FAB; `SharedTransitionLayout` morph on Compose 1.7+ |
| Month-day tap | `Animatable` 1.0 → 1.05 → 1.0 `spring`, 250ms; transition to Day via `SharedTransitionLayout` zoom, 350ms |
| Event detail open | `SharedTransitionLayout` + `Modifier.sharedElement(rememberSharedContentState(event.id), …)`, 300ms |
| Current-time line | re-offset every 60s, animate position with 1s `tween` |
| RSVP switch | `animateColorAsState` 200ms ease; no scale |
| Drawer open/close | `ModalNavigationDrawer` 250ms with a `scrim` (`rgba(0,0,0,0.32)`) |

```kotlin
// Month-day tap bounce, then navigate
@Composable
fun MonthCellTappable(content: @Composable () -> Unit, onOpenDay: () -> Unit) {
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()
    Box(
        Modifier.scale(scale.value).clickable {
            scope.launch {
                scale.animateTo(1.05f, spring(stiffness = 500f)); scale.animateTo(1f, spring(stiffness = 500f))
            }
            haptics.performHapticFeedback(HapticFeedbackType.LongPress)
            onOpenDay()
        },
    ) { content() }
}
```

Haptics: prefer `LocalHapticFeedback`. `HapticFeedbackType.LongPress` ≈ iOS `.impact(.medium)`; `TextHandleMove` ≈ `.selection`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+).

## 7. Icons

Google Calendar uses Material Symbols natively; `androidx.compose.material:material-icons-extended` is the first-party match. The Google Calendar logo and any custom glyphs ship as vector drawables (`ImageVector.vectorResource(R.drawable.…)`).

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| FAB plus | `plus` | `Icons.Filled.Add` |
| Schedule view | `list.bullet` | `Icons.AutoMirrored.Filled.ViewList` |
| Day view | `calendar.day.timeline.left` | `Icons.Filled.CalendarViewDay` |
| Week view | `calendar` | `Icons.Filled.CalendarViewWeek` |
| Month view | `calendar` | `Icons.Filled.CalendarMonth` |
| Location pin | `mappin.and.ellipse` | `Icons.Filled.Place` |
| Reminder | `bell.fill` | `Icons.Filled.Notifications` |
| Drawer menu | `line.horizontal.3` | `Icons.Filled.Menu` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Today | `calendar.badge.clock` | `Icons.Filled.Today` |
| Weather sun | `sun.max.fill` | `Icons.Filled.WbSunny` |
| Google Meet | `video.fill` | `Icons.Filled.Videocam` |
| Add guest | `person.badge.plus` | `Icons.Filled.PersonAddAlt1` |
| Add description | `text.alignleft` | `Icons.AutoMirrored.Filled.Notes` |
| Event (action menu) | `calendar` | `Icons.Filled.Event` |
| Task (action menu) | `checkmark.circle` | `Icons.Filled.TaskAlt` |
| Goal (action menu) | `flag` | `Icons.Filled.Flag` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `SharedTransitionLayout` and modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the white canvas wants dark status-bar icons (charcoal `#202124` in dark wants light icons). Use `Scaffold` insets so the FAB floats 16.dp above the gesture nav and the app bar clears the camera cutout. The Month grid is edge-to-edge (0.dp horizontal margin).
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on event titles, body, nav titles, sidebar items. Pin layout-sensitive text (Month-grid day numbers, 11sp time gutter, 12sp weekday headers, 10sp tab labels) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` so the uniform grid cells and timeline columns never break. Clamp the 36sp Schedule day number at ~44sp max.
- **Tabular figures**: keep `fontFeatureSettings = "tnum"` on every time / day-number style — column alignment ("9:00 AM / 10:30 AM") is layout-critical and breaks without it.
- **TalkBack**: collapse the event card into one node with `Modifier.semantics(mergeDescendants = true)` — "Stand-up, 9 to 9:30 AM, Conference Room B, in Work calendar, double-tap to open." Announce the Today cell as "Today, May 14".
- **Touch targets**: Material guidance is 48.dp minimum. The FAB (56.dp) and drawer rows (48.dp) are clear; ensure Month-grid cells expose a ≥48.dp tap zone and the 11.dp location glyph sits inside the ≥48.dp row.
- **Contrast**: `#202124` on `#FFFFFF` exceeds AAA. `#5F6368` secondary meets AA only at 14sp+ Medium — the 13sp event-time text sits right at that boundary, so boost it toward `#3C4043` when the system "increase contrast" setting (`isHighTextContrastEnabled`) is on. In dark mode, swap the Material dual-shadow for a `surfaceColorAtElevation()` tonal tint since shadows vanish on `#202124`.
- **Dynamic color**: **RECOMMENDED — embrace `dynamicColorScheme()`.** Google Calendar is a Material-native Google app; on Android 12+ it should adopt Material You and tint chrome (surfaces, the selected-row container, app bar) from the user's wallpaper. Keep only the *semantic* anchors fixed: the four event colors (Blue/Red/Yellow/Green), the full 24-swatch user calendar palette, and the red current-time indicator — these carry calendar-source meaning and must never drift with the wallpaper. The blue FAB may flow to the dynamic `primary` since it is the generic "create" action.
