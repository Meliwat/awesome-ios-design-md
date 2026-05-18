# Resy (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Resy's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the red-outline slot grid + Notify spine, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Resy's pure-black editorial canvas, the display-serif restaurant name vs sans UI split, the single Resy Red, the red-outlined slot system, amber Notify) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a SwiftUI sheet, `FlowRow` for the slot grid, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for the hero photo carousel. No color extraction — Resy's palette is a fixed editorial set, so Palette is not needed. Resy is dark-first (pure black); a light inversion is provided as secondary.

## 1. Color Tokens

```kotlin
// ui/theme/ResyColors.kt
import androidx.compose.ui.graphics.Color

object ResyColors {
    // Canvas & Surfaces (Dark — primary brand)
    val Black     = Color(0xFF000000) // pure black — the brand
    val Surface1  = Color(0xFF121212)
    val Surface2  = Color(0xFF1C1C1C)
    val Surface3  = Color(0xFF242424)
    val Divider   = Color(0xFF262626)

    // Canvas & Surfaces (Light — secondary inversion)
    val CanvasLight    = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF4F4F4)
    val SurfacePressed = Color(0xFFEAEAEA)
    val DividerLight   = Color(0xFFE2E2E2)

    // Text
    val TextPrimary     = Color(0xFFF5F5F5) // near-white, NOT pure white (editorial)
    val TextSecondary   = Color(0xFF9E9E9E)
    val TextTertiary    = Color(0xFF6A6A6A)
    val TextPrimaryLt   = Color(0xFF101010)
    val TextSecondaryLt = Color(0xFF6B6B6B)

    // Brand (single reservation color)
    val Red        = Color(0xFFC73E3A)
    val RedBright  = Color(0xFFE2504B) // active tab / emphasis on pure black
    val RedPressed = Color(0xFFA8302D)

    // Functional accents
    val NotifyAmber = Color(0xFFD99A2B)
    val HitGold     = Color(0xFFC9A24B)
    val Confirmed   = Color(0xFF4FA773)

    // Semantic
    val Error = Color(0xFFE2504B)
}
```

Wire it into both schemes. Resy is dark-first — the dark scheme is the brand (pure black). The light inversion is secondary; do not treat it as the default.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val ResyDark = darkColorScheme(
    primary        = ResyColors.Red,
    onPrimary      = Color.White,
    background     = ResyColors.Black,
    onBackground   = ResyColors.TextPrimary,
    surface        = ResyColors.Surface1,
    onSurface      = ResyColors.TextPrimary,
    surfaceVariant = ResyColors.Surface2,
    outline        = ResyColors.Divider,
    error          = ResyColors.Error,
)

private val ResyLight = lightColorScheme(
    primary        = ResyColors.Red,
    onPrimary      = Color.White,
    background     = ResyColors.CanvasLight,
    onBackground   = ResyColors.TextPrimaryLt,
    surface        = ResyColors.SurfaceGray,
    onSurface      = ResyColors.TextPrimaryLt,
    surfaceVariant = ResyColors.SurfacePressed,
    outline        = ResyColors.DividerLight,
    error          = ResyColors.Red,
)

@Composable
fun ResyTheme(
    // dark-first: default to dark regardless of system unless user opts into light
    dark: Boolean = true,
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) ResyDark else ResyLight,
    typography = ResyTypography,
    content = content,
)
```

## 2. Typography (M3)

Resy's brand voice is a high-contrast display serif for restaurant names + editorial titles; everything functional is a clean sans. **Playfair Display** (SIL OFL) is the closest free analog to Resy's serif; **Inter** (SIL OFL) is the UI sans. Drop both in `res/font/`. The serif/sans split *is* the brand — never set names in the sans.

```kotlin
// ui/theme/ResyType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Playfair = FontFamily(
    Font(R.font.playfair_bold,      FontWeight.Bold),
    Font(R.font.playfair_extrabold, FontWeight.ExtraBold),
    Font(R.font.playfair_italic,    FontWeight.Normal, FontStyle.Italic),
)
val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
    Font(R.font.inter_bold,     FontWeight.Bold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object ResyText {
    val DisplayTitle = TextStyle(Playfair, fontWeight = FontWeight.ExtraBold, fontSize = 34.sp, lineHeight = 39.sp, letterSpacing = (-0.5).sp)
    val RestName     = TextStyle(Playfair, fontWeight = FontWeight.Bold,      fontSize = 27.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Editorial    = TextStyle(Playfair, fontWeight = FontWeight.Normal, fontStyle = FontStyle.Italic, fontSize = 15.sp, lineHeight = 23.sp)
    val Section      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp)
    val CardTitle    = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 23.sp)
    val Body         = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val Meta         = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 19.sp)
    val SlotTime     = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 14.sp, lineHeight = 17.sp)
    val Seating      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 9.sp,  lineHeight = 11.sp, letterSpacing = 0.3.sp)
    val Eyebrow      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.8.sp)
    val Button       = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 18.sp, letterSpacing = 0.2.sp)
    val Tab          = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Caption      = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 12.sp, lineHeight = 17.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val ResyTypography = Typography(
    headlineLarge  = ResyText.DisplayTitle,
    headlineMedium = ResyText.RestName,
    titleLarge     = ResyText.Section,
    titleMedium    = ResyText.CardTitle,
    bodyMedium     = ResyText.Body,
    labelSmall     = ResyText.Tab,
)
```

## 3. Signature Components

### Reservation Slot Grid (red-outline system)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.graphics.drawscope.dashPathEffect
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

enum class SlotKind { Available, Primary, Notify }
data class ResySlot(val id: String, val label: String, val seating: String, val kind: SlotKind)

@OptIn(ExperimentalLayoutApi::class)
@Composable
fun SlotGrid(slots: List<ResySlot>, selectedId: String?, onSelect: (String) -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    FlowRow(
        modifier = modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.spacedBy(9.dp),
        verticalArrangement = Arrangement.spacedBy(9.dp),
        maxItemsInEachRow = 4,
    ) {
        slots.forEach { s ->
            val isSel = s.id == selectedId
            val solid = s.kind == SlotKind.Primary || (isSel && s.kind == SlotKind.Available)
            val notify = s.kind == SlotKind.Notify
            val timeColor = if (solid) Color.White else if (notify) ResyColors.NotifyAmber else ResyColors.TextPrimary
            val seatColor = if (solid) Color.White.copy(alpha = 0.82f) else if (notify) ResyColors.NotifyAmber else ResyColors.TextSecondary

            Column(
                Modifier
                    .weight(1f)
                    .height(56.dp)
                    .clip(RoundedCornerShape(10.dp))
                    .background(if (solid) ResyColors.Red else Color.Transparent)
                    .then(
                        if (solid) Modifier
                        else if (notify) Modifier.drawDashedBorder(ResyColors.Divider)
                        else Modifier.border(1.dp, ResyColors.Red, RoundedCornerShape(10.dp))
                    )
                    .clickable(enabled = !notify) {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                        onSelect(s.id)
                    },
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.Center,
            ) {
                Text(s.label, style = if (notify) ResyText.SlotTime.copy(fontSize = 12.sp) else ResyText.SlotTime, color = timeColor)
                Text(s.seating.uppercase(), style = ResyText.Seating, color = seatColor)
            }
        }
    }
}

// 1dp dashed rounded border for the Notify slot
fun Modifier.drawDashedBorder(color: Color) = this.then(
    Modifier.drawWithContent {
        drawContent()
        drawRoundRect(
            color = color,
            style = Stroke(width = 1.dp.toPx(), pathEffect = dashPathEffect(floatArrayOf(8f, 6f))),
            cornerRadius = androidx.compose.ui.geometry.CornerRadius(10.dp.toPx()),
        )
    }
)
```

### Reservation Date Strip

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.items

data class DateCell(val id: String, val dow: String, val num: String)

@Composable
fun DateStrip(days: List<DateCell>, selectedId: String?, onSelect: (String) -> Unit, modifier: Modifier = Modifier) {
    LazyRow(modifier.fillMaxWidth(), horizontalArrangement = Arrangement.spacedBy(8.dp)) {
        items(days) { d ->
            val sel = d.id == selectedId
            Column(
                Modifier
                    .widthIn(min = 56.dp).height(60.dp)
                    .clip(RoundedCornerShape(10.dp))
                    .background(if (sel) ResyColors.Red else ResyColors.Surface1)
                    .border(1.dp, if (sel) ResyColors.Red else ResyColors.Divider, RoundedCornerShape(10.dp))
                    .clickable { onSelect(d.id) }
                    .padding(horizontal = 12.dp),
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.Center,
            ) {
                Text(d.dow.uppercase(), style = ResyText.Tab, color = if (sel) Color.White else ResyColors.TextSecondary)
                Text(d.num, style = ResyText.SlotTime.copy(fontSize = 17.sp), color = if (sel) Color.White else ResyColors.TextPrimary)
            }
        }
    }
}
```

### Editorial Restaurant Header

```kotlin
@Composable
fun RestaurantHeader(name: String, rating: String, meta: String, tagline: String, modifier: Modifier = Modifier) {
    Column(modifier) {
        Text(name, style = ResyText.RestName, color = ResyColors.TextPrimary)
        Row(
            Modifier.padding(top = 9.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Text("★ $rating", style = ResyText.SlotTime.copy(fontSize = 13.sp), color = ResyColors.HitGold)
            Text("·", color = ResyColors.TextTertiary)
            Text(meta, style = ResyText.Meta, color = ResyColors.TextSecondary)
        }
        Text(tagline, style = ResyText.Editorial, color = ResyColors.TextSecondary, modifier = Modifier.padding(top = 12.dp))
    }
}
```

### Notify Chip

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Notifications
import androidx.compose.material3.Icon

@Composable
fun NotifyChip(onList: Boolean, onToggle: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    Row(
        modifier
            .clip(RoundedCornerShape(50))
            .background(if (onList) ResyColors.NotifyAmber else Color.Transparent)
            .then(if (onList) Modifier else Modifier.border(1.dp, ResyColors.NotifyAmber, RoundedCornerShape(50)))
            .clickable { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onToggle() }
            .padding(horizontal = 16.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(7.dp),
    ) {
        Icon(Icons.Filled.Notifications, null, tint = if (onList) Color.White else ResyColors.NotifyAmber, modifier = Modifier.size(12.dp))
        Text(
            if (onList) "On the Notify list" else "Notify when available",
            style = ResyText.SlotTime.copy(fontSize = 13.sp),
            color = if (onList) Color.White else ResyColors.NotifyAmber,
        )
    }
}
```

### Primary Button

```kotlin
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults

@Composable
fun ResyPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    Button(
        onClick = { haptics.performHapticFeedback(HapticFeedbackType.Confirm); onClick() },
        shape = RoundedCornerShape(8.dp),   // crisp, NOT pill
        colors = ButtonDefaults.buttonColors(containerColor = ResyColors.Red, contentColor = Color.White),
        modifier = modifier.fillMaxWidth().height(52.dp),
    ) {
        Text(title, style = ResyText.Button)
    }
}
```

## 4. Navigation

Resy has minimal chrome on pure black: a 5-tab bottom bar over an editorial photo-and-slot content area. On Android, model it as a `NavigationBar`. There is no Material tint pill — active is the brighter Resy Red so it reads on `#000000`.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun ResyBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = ResyColors.Black, tonalElevation = 0.dp) {
        val items = listOf(
            "Search"       to Icons.Filled.Search,
            "Hit List"     to Icons.Filled.BookmarkBorder,
            "Reservations" to Icons.Filled.CalendarMonth,
            "Notify"       to Icons.Filled.NotificationsNone,
            "Account"      to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(21.dp)) },
                label = { Text(label, style = ResyText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = ResyColors.RedBright,   // bright red — contrast on pure black
                    selectedTextColor = ResyColors.RedBright,
                    unselectedIconColor = ResyColors.TextTertiary,
                    unselectedTextColor = ResyColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Resy has none
                ),
            )
        }
    }
}
```

The guest, date, and Notify pickers are `ModalBottomSheet`s (rounded 16.dp top, `Surface1` background, 1.dp `Divider` top border as the elevation cue since shadows are invisible on black). The guest sheet has a − / + stepper; the date sheet is a month grid with the selected date a filled `Red` circle; each pins a full-width red "Apply" button.

## 5. Motion

Resy motion is restrained and editorial — 150–300ms ease-out, never showy. On pure black, depth is a border, not a shadow.

| Moment | Compose recipe |
|--------|----------------|
| Slot select | outlined → solid `animateColorAsState(if (sel) Red else Transparent, tween(150))`; scale 1 → 0.97 bounce; soft haptic; Book button enables |
| Date select | cell fill `animateColorAsState(tween(150))`; slot grid `Crossfade` to that day |
| Slot grid load | `AnimatedVisibility` per chip `fadeIn(tween(200)) + slideInVertically` staggered 30ms |
| Notify toggle | dashed → amber fill `animateColorAsState(tween(150))` + soft haptic |
| Restaurant detail open | hero carousel shared element via `LookaheadScope` / Nav3 `tween(300)`; serif name `fadeIn` + slide up |
| Guest / calendar sheet | `ModalBottomSheet` default slide; backdrop scrim to ~0.7 |
| Carousel | `HorizontalPager` 1:1 drag; page dots `animateColorAsState` |

```kotlin
// Slot fill — the canonical Resy reservation motion
val fill by animateColorAsState(
    targetValue = if (solid) ResyColors.Red else Color.Transparent,
    animationSpec = tween(150),
    label = "slotFill",
)
Box(Modifier.clip(RoundedCornerShape(10.dp)).background(fill)) { /* time + seating */ }
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on slot select, date select, Notify toggle, and guest stepper. For "reservation confirmed" use `HapticFeedbackType.Confirm` (or `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)`). Editorial scrolling is silent; only show a snackbar on a booking error.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Hit List (tab) | `list.star` / `bookmark.fill` | `Icons.Filled.BookmarkBorder` / `Icons.Filled.Bookmark` |
| Reservations (tab) | `calendar` | `Icons.Filled.CalendarMonth` |
| Notify (tab) | `bell` / `bell.fill` | `Icons.Filled.NotificationsNone` / `Icons.Filled.Notifications` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Hit List (save) | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Rating star | `star.fill` | `Icons.Filled.Star` |
| Notify bell | `bell.fill` | `Icons.Filled.Notifications` |
| Guests | `person.2.fill` | `Icons.Filled.People` |
| Date | `calendar` | `Icons.Filled.CalendarMonth` |
| Daypart | `sun.max` / `moon.stars` | `Icons.Filled.WbSunny` / `Icons.Filled.NightsStay` |
| Confirmed | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Menu | `menucard` | `Icons.Filled.RestaurantMenu` |
| Location | `mappin.and.ellipse` | `Icons.Filled.Place` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `FlowRow`, `HorizontalPager`, and dashed-border draw are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; Resy is dark-first so system bars want light content over the pure-black canvas. The full-bleed hero photo carousel wants light-content status icons with a top scrim; the tab bar (and any sticky Book button) must sit above the navigation bar inset (`Modifier.navigationBarsPadding()`).
- **Font scaling**: `sp` honors the user's font scale — keep it on display titles, restaurant names (serif), body, metadata. Pin layout-sensitive text (10sp tab labels, 9sp seating labels, 14sp slot-time text) by deriving from `dp` or a fixed-`fontScale` density; let the `FlowRow` slot grid wrap rather than overflow.
- **TalkBack**: label slot chips "{time}, {seating}, available, double-tap to select" / "{time} primary slot" / "Sold out, Notify me for {window}"; the Notify chip toggles "Add to Notify list" / "On the Notify list"; the rating exposes "Rated {n} out of 10"; serif restaurant names get `Modifier.semantics { heading() }`.
- **Touch targets**: Material guidance is 48.dp. Slot chips are ≥ 70.dp × 56.dp (full-chip clickable); date cells ≥ 56.dp × 60.dp; the Book button is 52.dp; tab icons get a 48.dp hit area.
- **Contrast**: `#F5F5F5` on `#000000` passes WCAG AAA; white on `#C73E3A` passes AA for the Book button. The red `#C73E3A` slot border on black and amber `#D99A2B` Notify text are functional/iconographic — verify any small text size for AA; the active tab uses brighter `#E2504B` for legibility on pure black.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the slot-select scale bounce and the staggered grid fade-in — snap chips/fills; keep the selected fill color (it conveys state).
- **Dark mode**: this IS the brand — pure black `#000000`, near-white `#F5F5F5` text, serif restaurant names; default `ResyTheme(dark = true)` and only invert to the light scheme on explicit user opt-in. On black, hairline `#262626` borders are the elevation cue since shadows are invisible. Do **not** enable Material You `dynamicColorScheme()` — Resy's black-and-editorial-serif identity (and the single Resy Red) must hold regardless of wallpaper.
