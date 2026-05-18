# OpenTable (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports OpenTable's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the slot-grid booking spine, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (OpenTable's clean white canvas, single disciplined OpenTable Red, the reservation slot grid as the heartbeat, gold-star/teal-points discipline) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a SwiftUI sheet, `FlowRow` for the slot grid, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for restaurant photography. No color extraction — OpenTable's palette is a fixed disciplined brand set, so Palette is not needed. OpenTable is light-first (clean white); a full neutral dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/OpenTableColors.kt
import androidx.compose.ui.graphics.Color

object OpenTableColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF6F6F7)
    val SurfaceRaised  = Color(0xFFFBFBFC)
    val SurfacePressed = Color(0xFFECECEE)
    val Divider        = Color(0xFFE4E4E7)

    // Canvas & Surfaces (Dark) — clean neutral charcoal, NOT pure black
    val DarkCanvas   = Color(0xFF121212)
    val DarkSurface1 = Color(0xFF1C1C1E)
    val DarkSurface2 = Color(0xFF262629)
    val DarkDivider  = Color(0xFF2E2E31)

    // Text — neutral near-black, NOT pure black
    val TextPrimary       = Color(0xFF1A1A1C)
    val TextSecondary     = Color(0xFF6B6B70)
    val TextTertiary      = Color(0xFF9A9A9F)
    val DarkTextPrimary   = Color(0xFFEDEDED)
    val DarkTextSecondary = Color(0xFFA2A2A6)

    // Brand (single booking color)
    val Red        = Color(0xFFDA3743)
    val RedBright  = Color(0xFFF2545B) // dark-mode CTA / emphasis
    val RedPressed = Color(0xFFB92C37)
    val RedTint    = Color(0xFFFCEBEC)

    // Functional accents
    val GoldStar    = Color(0xFFE8A33D)
    val PointsTeal  = Color(0xFF1F8A8A)
    val DinerGreen  = Color(0xFF2FA86A)
    val NotifyAmber = Color(0xFFE08A2F)

    // Semantic
    val Success   = Color(0xFF2FA86A)
    val Error     = Color(0xFFD6342F)
    val ErrorDark = Color(0xFFF2545B)
}
```

Wire it into both schemes. OpenTable is light-first (clean white); the dark scheme uses neutral `#121212`, never pure black, with `RedBright` as the primary so the Reserve CTA stays legible.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val OpenTableLight = lightColorScheme(
    primary        = OpenTableColors.Red,
    onPrimary      = OpenTableColors.Canvas,
    background     = OpenTableColors.Canvas,
    onBackground   = OpenTableColors.TextPrimary,
    surface        = OpenTableColors.SurfaceGray,
    onSurface      = OpenTableColors.TextPrimary,
    surfaceVariant = OpenTableColors.SurfacePressed,
    outline        = OpenTableColors.Divider,
    error          = OpenTableColors.Error,
)

private val OpenTableDark = darkColorScheme(
    primary        = OpenTableColors.RedBright, // brightened so CTA reads on #121212
    onPrimary      = Color.White,
    background     = OpenTableColors.DarkCanvas,
    onBackground   = OpenTableColors.DarkTextPrimary,
    surface        = OpenTableColors.DarkSurface1,
    onSurface      = OpenTableColors.DarkTextPrimary,
    surfaceVariant = OpenTableColors.DarkSurface2,
    outline        = OpenTableColors.DarkDivider,
    error          = OpenTableColors.ErrorDark,
)

@Composable
fun OpenTableTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) OpenTableDark else OpenTableLight,
    typography = OpenTableTypography,
    content = content,
)
```

## 2. Typography (M3)

OpenTable ships a custom grotesque sans ("OpenTable Sans"); **Inter** (SIL OFL) is the closest free analog — drop the TTFs in `res/font/`. One family, many weights: body 400, subtitles 600, restaurant names and CTAs 700–800. Utility-forward, trustworthy, scannable.

```kotlin
// ui/theme/OpenTableType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),
    Font(R.font.inter_medium,    FontWeight.Medium),
    Font(R.font.inter_semibold,  FontWeight.SemiBold),
    Font(R.font.inter_bold,      FontWeight.Bold),
    Font(R.font.inter_extrabold, FontWeight.ExtraBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object OpenTableText {
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.5).sp)
    val RestName    = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 29.sp, letterSpacing = (-0.3).sp)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val ListSub     = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val SlotTime    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 14.sp, lineHeight = 17.sp)
    val PointsTag   = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.3.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 18.sp)
    val Pill        = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 17.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val OpenTableTypography = Typography(
    headlineLarge  = OpenTableText.ScreenTitle,
    headlineMedium = OpenTableText.RestName,
    titleLarge     = OpenTableText.Section,
    titleMedium    = OpenTableText.CardTitle,
    bodyMedium     = OpenTableText.Body,
    labelSmall     = OpenTableText.Tab,
)
```

## 3. Signature Components

### Reservation Time-Slot Grid

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
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

data class Slot(val id: String, val time: String, val points: Int? = null, val recommended: Boolean = false)

@OptIn(ExperimentalLayoutApi::class)
@Composable
fun SlotGrid(slots: List<Slot>, selectedId: String?, onSelect: (String) -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    FlowRow(
        modifier = modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.spacedBy(9.dp),
        verticalArrangement = Arrangement.spacedBy(9.dp),
        maxItemsInEachRow = 4,
    ) {
        slots.forEach { s ->
            val sel = s.id == selectedId || (selectedId == null && s.recommended)
            Column(
                Modifier
                    .weight(1f)
                    .height(52.dp)
                    .clip(RoundedCornerShape(10.dp))
                    .background(if (sel) OpenTableColors.Red else OpenTableColors.SurfaceGray)
                    .then(if (sel) Modifier else Modifier.border(1.dp, OpenTableColors.Divider, RoundedCornerShape(10.dp)))
                    .clickable {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                        onSelect(s.id)
                    },
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.Center,
            ) {
                Text(s.time, style = OpenTableText.SlotTime, color = if (sel) Color.White else OpenTableColors.TextPrimary)
                if (s.points != null) {
                    Text(
                        "+${"%,d".format(s.points)}",
                        style = OpenTableText.PointsTag.copy(fontSize = 9.sp),
                        color = if (sel) Color.White.copy(alpha = 0.85f) else OpenTableColors.PointsTeal,
                    )
                }
            }
        }
    }
}
```

### Star Rating

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Star
import androidx.compose.material.icons.filled.StarHalf
import androidx.compose.material.icons.outlined.StarOutline
import androidx.compose.material3.Icon

@Composable
fun StarRating(rating: Double, count: Int, modifier: Modifier = Modifier) {
    Row(modifier, verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
        Row(horizontalArrangement = Arrangement.spacedBy(1.dp)) {
            (0 until 5).forEach { i ->
                val icon = when {
                    i + 0.5 < rating -> Icons.Filled.Star
                    i < rating       -> Icons.Filled.StarHalf
                    else             -> Icons.Outlined.StarOutline
                }
                Icon(icon, null, tint = OpenTableColors.GoldStar, modifier = Modifier.size(13.dp))
            }
        }
        Text("%.1f".format(rating), style = OpenTableText.Meta.copy(fontWeight = FontWeight.SemiBold), color = OpenTableColors.TextPrimary)
        Text("($count)", style = OpenTableText.Meta, color = OpenTableColors.TextSecondary)
    }
}
```

### Social-Proof Pill

```kotlin
@Composable
fun BookedPill(count: Int, modifier: Modifier = Modifier) {
    Row(
        modifier
            .clip(RoundedCornerShape(50))
            .background(OpenTableColors.DinerGreen.copy(alpha = 0.16f))
            .padding(horizontal = 10.dp, vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(5.dp),
    ) {
        Text("🔥")
        Text("Booked $count times today",
            style = OpenTableText.PointsTag.copy(fontSize = 11.sp), color = OpenTableColors.DinerGreen)
    }
}
```

### Party-Size / Date Selector Chip

```kotlin
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun SelectorChip(icon: ImageVector, label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Row(
        modifier
            .height(40.dp)
            .clip(RoundedCornerShape(50))
            .background(OpenTableColors.SurfaceGray)
            .border(1.dp, OpenTableColors.Divider, RoundedCornerShape(50))
            .clickable { onClick() }
            .padding(horizontal = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(icon, null, tint = OpenTableColors.TextSecondary, modifier = Modifier.size(14.dp))
        Text(label, style = OpenTableText.Pill, color = OpenTableColors.TextPrimary)
    }
}
```

### Sticky Reserve Bar

```kotlin
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.Surface

@Composable
fun ReserveBar(timeLabel: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    Surface(color = OpenTableColors.Canvas, shadowElevation = 12.dp, modifier = modifier.fillMaxWidth()) {
        Column {
            HorizontalDivider(thickness = 0.5.dp, color = OpenTableColors.Divider)
            Button(
                onClick = { haptics.performHapticFeedback(HapticFeedbackType.Confirm); onClick() },
                shape = RoundedCornerShape(50),
                colors = ButtonDefaults.buttonColors(containerColor = OpenTableColors.Red, contentColor = Color.White),
                modifier = Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 12.dp).height(50.dp),
            ) {
                Text("Reserve for $timeLabel", style = OpenTableText.Button)
            }
        }
    }
}
```

### Points / Rewards Chip

```kotlin
@Composable
fun PointsChip(text: String, modifier: Modifier = Modifier) {
    Row(
        modifier
            .clip(RoundedCornerShape(50))
            .background(OpenTableColors.PointsTeal.copy(alpha = 0.16f))
            .border(1.dp, OpenTableColors.PointsTeal.copy(alpha = 0.4f), RoundedCornerShape(50))
            .padding(horizontal = 14.dp, vertical = 7.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(7.dp),
    ) {
        Icon(Icons.Filled.Star, null, tint = OpenTableColors.PointsTeal, modifier = Modifier.size(12.dp))
        Text(text, style = OpenTableText.Pill.copy(fontWeight = FontWeight.Bold), color = OpenTableColors.PointsTeal)
    }
}
```

## 4. Navigation

OpenTable has minimal chrome: a 5-tab bottom bar over a photo-and-slot-driven content area. On Android, model it as a `NavigationBar`. There is no Material tint pill — active is OpenTable Red; in dark mode use `RedBright` so it reads on `#121212`.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun OpenTableBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = OpenTableColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Discover"     to Icons.Filled.Search,
            "Saved"        to Icons.Filled.FavoriteBorder,
            "Reservations" to Icons.Filled.CalendarMonth,
            "Rewards"      to Icons.Filled.Star,
            "Profile"      to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(21.dp)) },
                label = { Text(label, style = OpenTableText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = OpenTableColors.Red,   // RedBright in dark scheme
                    selectedTextColor = OpenTableColors.Red,
                    unselectedIconColor = OpenTableColors.TextTertiary,
                    unselectedTextColor = OpenTableColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — OpenTable has none
                ),
            )
        }
    }
}
```

The party-size and date pickers are `ModalBottomSheet`s (rounded 20.dp top) — the party sheet has a − / + stepper, count in `RestName`-scale text, quick chips 1–6 and "7+", and a pinned full-width red "Apply" button; the date sheet is a calendar grid with the selected date as a filled red circle.

## 5. Motion

OpenTable motion is clean and restrained — 150–300ms ease-out, never showy. Shadows lift cards and the sticky CTA, nothing more.

| Moment | Compose recipe |
|--------|----------------|
| Slot select | fill `animateColorAsState(if (sel) Red else SurfaceGray, tween(150))`; scale 1 → 0.97 bounce on press; soft haptic; Reserve bar label updates |
| Slot grid load | `AnimatedVisibility` per chip `fadeIn(tween(200)) + slideInVertically` staggered 30ms |
| Filter chip toggle | border/fill `animateColorAsState(tween(150))`; result list `Crossfade` |
| Restaurant detail open | shared element via `LookaheadScope` / Nav3 transition `tween(300)` |
| Party/date sheet | `ModalBottomSheet` default slide; backdrop scrim auto |
| Search focus | search field `animateContentSize` reveals recent searches `tween(200)` |
| Pull to refresh | `PullToRefreshBox` with an OpenTable-mark indicator |

```kotlin
// Slot fill — the canonical OpenTable booking motion
val fill by animateColorAsState(
    targetValue = if (selected) OpenTableColors.Red else OpenTableColors.SurfaceGray,
    animationSpec = tween(150),
    label = "slotFill",
)
Box(Modifier.clip(RoundedCornerShape(10.dp)).background(fill)) { /* time + points */ }
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on slot select, filter toggle, and party stepper. For the "reservation confirmed" success use `HapticFeedbackType.Confirm` (or `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)`). Search and list updates are silent; only show a snackbar on a booking error.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Discover (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Saved (tab) | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Icons.Filled.Favorite` |
| Reservations (tab) | `calendar` | `Icons.Filled.CalendarMonth` |
| Rewards (tab) | `star` / `star.fill` | `Icons.Filled.Star` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Save (heart) | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Review star | `star.fill` / `star.leadinghalf.filled` | `Icons.Filled.Star` / `Icons.Filled.StarHalf` |
| Points | `star.fill` | `Icons.Filled.Star` |
| Party size | `person.2.fill` | `Icons.Filled.People` |
| Date | `calendar` | `Icons.Filled.CalendarMonth` |
| Time | `clock` | `Icons.Filled.Schedule` |
| Notify / waitlist | `bell` | `Icons.Filled.NotificationsNone` |
| Location | `mappin.and.ellipse` | `Icons.Filled.Place` |
| Menu | `menucard` | `Icons.Filled.RestaurantMenu` |
| Confirmed | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `FlowRow` and modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the clean white canvas wants dark-content system bars (light-content in dark mode). The full-bleed restaurant hero photo wants light-content status icons with a top scrim; the sticky Reserve bar must sit above the navigation bar inset (`Modifier.navigationBarsPadding()`).
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, restaurant names, body, metadata. Pin layout-sensitive text (10sp tab labels, points tags, 14sp slot-time text) by deriving from `dp` or a fixed-`fontScale` density; let the `FlowRow` slot grid wrap to more rows rather than overflow.
- **TalkBack**: label slot chips "{time}, earns {points} points, double-tap to select"; the Reserve bar announces "Reserve a table for {time}"; `StarRating` exposes a single `contentDescription` "{rating} stars, {count} reviews"; the booked pill announces the social-proof string.
- **Touch targets**: Material guidance is 48.dp. Slot chips are ≥ 64.dp × 52.dp (full-chip clickable); selector chips are 40.dp tall but full-width tappable; the Reserve button is 50.dp; tab icons get a 48.dp hit area.
- **Contrast**: `#1A1A1C` on `#FFFFFF` passes WCAG AA at body sizes; white on `#DA3743` passes AA for the Reserve button. Teal `#1F8A8A` points text on its 16% tint and the gold `#E8A33D` star are functional/iconographic — verify any points text size for AA. In dark mode use `#F2545B` so the CTA holds contrast on `#121212`.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the slot-select scale bounce and the staggered grid fade-in — snap chips and fills; keep the selected fill color (it conveys state).
- **Dark mode**: invert via the `Dark*` palette — `#121212`, NOT pure black; `#1A1A1C` text becomes `#EDEDED`; the booking action becomes `RedBright`. Do **not** enable Material You `dynamicColorScheme()` — OpenTable's clean-white-and-disciplined-red identity must hold regardless of wallpaper (the single red booking color is the brand).
