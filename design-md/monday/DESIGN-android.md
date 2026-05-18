# monday.com (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports monday.com's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the colorful board + full-bleed status cells + battery rollup, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (monday.com's deep-indigo canvas, full-bleed saturated status cells, the leading group-color stripe, bold Figtree headers) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `FloatingActionButton`, a `ModalBottomSheet` label picker, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars. No color extraction — monday.com's palette is a fixed multicolor set, so Palette is not needed. monday.com is light-first; a full deep-indigo night scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/MondayColors.kt
import androidx.compose.ui.graphics.Color

object MondayColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF6F7FB)
    val SurfacePressed = Color(0xFFEDEFF5)
    val Divider        = Color(0xFFE6E9F0)
    val CellBorder     = Color(0xFFD0D4E4)

    // Canvas & Surfaces (Night)
    val NightCanvas     = Color(0xFF181B34) // deep indigo — NOT pure black
    val NightSurface1   = Color(0xFF20243F)
    val NightSurface2   = Color(0xFF292F4C)
    val NightDivider    = Color(0xFF353B5C)
    val NightCellBorder = Color(0xFF3D436A)

    // Text
    val TextPrimary       = Color(0xFF323338)
    val TextSecondary     = Color(0xFF676879)
    val TextTertiary      = Color(0xFF9699A6)
    val DarkTextPrimary   = Color(0xFFF5F6F8)
    val DarkTextSecondary = Color(0xFFA7AFC6)
    val DarkTextTertiary  = Color(0xFF6B7394)

    // Action (the only "UI" color amid the data colors)
    val Blue        = Color(0xFF0073EA)
    val BluePressed = Color(0xFF0060C2)

    // Multicolor data palette (full-bleed fills)
    val Red        = Color(0xFFE2445C)
    val Orange     = Color(0xFFFDAB3D)
    val Green      = Color(0xFF00C875)
    val BrightBlue = Color(0xFF579BFC)
    val Purple     = Color(0xFFA25DDC)
    val DarkPurple = Color(0xFF401694)
    val Indigo     = Color(0xFF5559DF)
    val Yellow     = Color(0xFFFFCB00)
    val Teal       = Color(0xFF00D2D2)
    val Pink       = Color(0xFFFF158A)
    val BrightGreen = Color(0xFF9CD326)
    val Navy       = Color(0xFF333D6E)

    // Semantic
    val Success = Color(0xFF00C875)
    val Error   = Color(0xFFE2445C)
    val Warning = Color(0xFFFDAB3D)
}

// On-color text: yellow / teal / bright-green flip to dark
private val DarkOn = setOf(MondayColors.Yellow, MondayColors.Teal, MondayColors.BrightGreen)
fun onColor(bg: Color): Color = if (bg in DarkOn) MondayColors.TextPrimary else Color.White

data class StatusOption(val label: String, val color: Color)
val defaultStatuses = listOf(
    StatusOption("Done",          MondayColors.Green),
    StatusOption("Working on it", MondayColors.Orange),
    StatusOption("Stuck",         MondayColors.Red),
    StatusOption("Not started",   MondayColors.Navy),
)
```

Wire it into both schemes. monday.com is light-first; the night scheme uses the signature `#181B34` deep indigo, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val MondayLight = lightColorScheme(
    primary        = MondayColors.Blue,
    onPrimary      = Color.White,
    background     = MondayColors.Canvas,
    onBackground   = MondayColors.TextPrimary,
    surface        = MondayColors.SurfaceGray,
    onSurface      = MondayColors.TextPrimary,
    surfaceVariant = MondayColors.SurfacePressed,
    outline        = MondayColors.Divider,
    error          = MondayColors.Error,
)

private val MondayNight = darkColorScheme(
    primary        = MondayColors.Blue, // action blue identical in night
    onPrimary      = Color.White,
    background     = MondayColors.NightCanvas,
    onBackground   = MondayColors.DarkTextPrimary,
    surface        = MondayColors.NightSurface1,
    onSurface      = MondayColors.DarkTextPrimary,
    surfaceVariant = MondayColors.NightSurface2,
    outline        = MondayColors.NightDivider,
    error          = MondayColors.Error,
)

@Composable
fun MondayTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) MondayNight else MondayLight,
    typography = MondayTypography,
    content = content,
)
```

## 2. Typography

monday.com uses **Figtree** (Poppins-class brand sans) — drop the TTFs in `res/font/`. Bold and confident: item names 500, group headers 800.

```kotlin
// ui/theme/MondayType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Figtree = FontFamily(
    Font(R.font.figtree_regular,   FontWeight.Normal),
    Font(R.font.figtree_medium,    FontWeight.Medium),
    Font(R.font.figtree_semibold,  FontWeight.SemiBold),
    Font(R.font.figtree_bold,      FontWeight.Bold),
    Font(R.font.figtree_extrabold, FontWeight.ExtraBold),
)

object MondayText {
    val ScreenTitle = TextStyle(Figtree, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.5).sp)
    val BoardTitle  = TextStyle(Figtree, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val GroupHeader = TextStyle(Figtree, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp) // color per group
    val Section     = TextStyle(Figtree, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val Body        = TextStyle(Figtree, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val ItemName    = TextStyle(Figtree, fontWeight = FontWeight.Medium,    fontSize = 14.sp, lineHeight = 19.sp)
    val ColumnHead  = TextStyle(Figtree, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Meta        = TextStyle(Figtree, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val StatusLabel = TextStyle(Figtree, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val Button      = TextStyle(Figtree, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 15.sp)
    val TextAction  = TextStyle(Figtree, fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 14.sp)
    val PersonInit  = TextStyle(Figtree, fontWeight = FontWeight.Bold,      fontSize = 9.sp,  lineHeight = 9.sp,  letterSpacing = 0.2.sp)
    val Tab         = TextStyle(Figtree, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val MondayTypography = Typography(
    headlineLarge = MondayText.ScreenTitle,
    headlineMedium = MondayText.BoardTitle,
    titleMedium   = MondayText.Section,
    bodyMedium    = MondayText.Body,
    labelSmall    = MondayText.Tab,
)
```

## 3. Signature Components

### Board Item Row (stripe + cells)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp

@Composable
fun StatusCell(label: String, color: Color, modifier: Modifier = Modifier) {
    Box(
        modifier
            .widthIn(min = 88.dp)
            .clip(RoundedCornerShape(4.dp))
            .background(color)
            .padding(horizontal = 10.dp, vertical = 5.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(label.uppercase(), style = MondayText.StatusLabel.copy(color = onColor(color)))
    }
}

@Composable
fun BoardItemRow(
    groupColor: Color,
    name: String,
    personInitials: String,
    personColor: Color,
    status: StatusOption,
    modifier: Modifier = Modifier,
) {
    Column(modifier.fillMaxWidth()) {
        Row(Modifier.fillMaxWidth().height(IntrinsicSize.Min)) {
            Box(Modifier.width(6.dp).fillMaxHeight().background(groupColor)) // group stripe, flush leading
            Row(
                Modifier.weight(1f).padding(horizontal = 14.dp, vertical = 12.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(10.dp),
            ) {
                Text(name, style = MondayText.ItemName.copy(color = MondayColors.TextPrimary),
                     maxLines = 1, overflow = TextOverflow.Ellipsis, modifier = Modifier.weight(1f))
                Box(
                    Modifier.size(24.dp).clip(CircleShape).background(personColor),
                    contentAlignment = Alignment.Center,
                ) { Text(personInitials, style = MondayText.PersonInit.copy(color = Color.White)) }
                StatusCell(status.label, status.color)
            }
        }
        HorizontalDivider(color = MondayColors.Divider, thickness = 1.dp)
    }
}
```

### Group Header (colored, collapsible)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.clickable
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ChevronRight
import androidx.compose.ui.draw.rotate

@Composable
fun GroupHeader(name: String, groupColor: Color, count: Int, expanded: Boolean, onToggle: () -> Unit) {
    val rot by animateFloatAsState(if (expanded) 90f else 0f, tween(150), label = "groupCaret")
    Row(
        Modifier
            .fillMaxWidth()
            .clickable { onToggle() }
            .padding(horizontal = 18.dp).padding(top = 12.dp, bottom = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(Icons.Filled.ChevronRight, null, tint = groupColor, modifier = Modifier.size(14.dp).rotate(rot))
        Text(name, style = MondayText.GroupHeader.copy(color = groupColor))
        Text("$count items", style = MondayText.ColumnHead.copy(color = MondayColors.TextTertiary))
    }
}
```

### Status Label Picker (ModalBottomSheet grid)

```kotlin
import androidx.compose.foundation.layout.FlowRow
import androidx.compose.material3.ModalBottomSheet

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun StatusPickerSheet(
    options: List<StatusOption> = defaultStatuses,
    onPick: (StatusOption) -> Unit,
    onDismiss: () -> Unit,
) {
    ModalBottomSheet(onDismissRequest = onDismiss, containerColor = MondayColors.Canvas) {
        FlowRow(
            Modifier.fillMaxWidth().padding(20.dp),
            horizontalArrangement = Arrangement.spacedBy(10.dp),
            verticalArrangement = Arrangement.spacedBy(10.dp),
        ) {
            options.forEach { opt ->
                Box(
                    Modifier
                        .weight(1f, fill = false)
                        .defaultMinSize(minWidth = 140.dp)
                        .clip(RoundedCornerShape(4.dp))
                        .background(opt.color)
                        .clickable { onPick(opt) }
                        .padding(vertical = 14.dp),
                    contentAlignment = Alignment.Center,
                ) { Text(opt.label.uppercase(), style = MondayText.StatusLabel.copy(color = onColor(opt.color))) }
            }
        }
    }
}
```

### Battery Rollup (group health)

```kotlin
@Composable
fun BatteryRollup(segments: List<Pair<Color, Float>>, modifier: Modifier = Modifier) {
    Row(
        modifier
            .fillMaxWidth()
            .height(24.dp)
            .clip(RoundedCornerShape(4.dp))
            .background(MondayColors.NightSurface2),
    ) {
        segments.forEach { (color, fraction) ->
            val animated by animateFloatAsState(fraction, tween(250), label = "battery")
            Box(Modifier.weight(animated.coerceAtLeast(0.0001f)).fillMaxHeight().background(color))
        }
    }
}
```

### Floating Action Button

```kotlin
import androidx.compose.material.icons.filled.Add

@Composable
fun CreateFab(onClick: () -> Unit) {
    FloatingActionButton(
        onClick = onClick,
        containerColor = MondayColors.Blue,
        contentColor = Color.White,
        shape = CircleShape,
        modifier = Modifier.size(56.dp),
    ) { Icon(Icons.Filled.Add, contentDescription = "Create", modifier = Modifier.size(24.dp)) }
}
```

## 4. Navigation

monday.com has minimal chrome: a 4-tab bottom strip with the action-blue accent (no Material tint pill) and per-board view tabs.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun MondayBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = MondayColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"   to Icons.Filled.Home,
            "Boards" to Icons.Filled.GridView,
            "Inbox"  to Icons.Filled.Notifications,
            "Search" to Icons.Filled.Search,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = MondayText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = MondayColors.Blue,
                    selectedTextColor = MondayColors.Blue,
                    unselectedIconColor = MondayColors.TextTertiary,
                    unselectedTextColor = MondayColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — monday.com has none
                ),
            )
        }
    }
}
```

The per-board view switcher (Main Table / Kanban / Timeline / Calendar / Chart) is a `Row` of clickable labels; the active one gets `TextPrimary` plus a 2.5.dp blue underline via `Modifier.drawBehind`. The Kanban view is a horizontally scrolling `LazyRow` of ~260.dp columns containing draggable cards; dropping a card into a column changes its status and recolors the stripe.

## 5. Motion

monday.com motion is energetic but purposeful — color transitions carry feedback.

| Moment | Compose recipe |
|--------|----------------|
| Status change | `ModalBottomSheet` picker (Material slide-up); on pick, animate cell color via `animateColorAsState(tween(200))`; soft haptic |
| Group collapse/expand | caret `animateFloatAsState` 0 → 90° `tween(150)`; items `AnimatedVisibility(expandVertically(tween(200)) + fadeIn())` |
| FAB → new item | navigate to a full-screen / bottom-sheet item editor; FAB container scale on press |
| Drag item / Kanban card | `LazyColumn`/`LazyRow` + `Modifier.draggable`; neighbors `animateItemPlacement(tween(200))`; soft haptic on drop; stripe + status recolor |
| Battery rollup | each segment `animateFloatAsState(tween(250))` on a status change |
| View tab switch | 2.5.dp blue underline `animateDpAsState` translateX 200ms; board `Crossfade(tween(150))` |
| Confetti | board milestone → a brief `Canvas`-drawn confetti burst (~1s) |
| Page navigation | Nav3/`NavHost` slide push `tween(300)` |

```kotlin
// The canonical monday.com feedback — status cell color crossfade
val cellColor by animateColorAsState(status.color, tween(200), label = "statusCell")
Box(Modifier.background(cellColor)) { /* label */ }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` on status change, drag start, and group toggle. For a softer tick use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`. Auto-save is silent; only show a "Saved" snackbar on manual save or error.

## 6. Icons

monday.com's iconography maps cleanly to `androidx.compose.material:material-icons-extended`. The group stripe and status cell are drawn shapes, not icons.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Boards (tab) | `tablecells` | `Icons.Filled.GridView` |
| Inbox (tab) | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Add / FAB | `plus` | `Icons.Filled.Add` |
| Group caret | `chevron.right` | `Icons.Filled.ChevronRight` (rotate 90°) |
| Person (empty) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Date column | `calendar` | `Icons.Filled.CalendarToday` |
| Timeline column | `chart.bar.xaxis` | `Icons.Filled.Timeline` |
| Numbers column | `number` | `Icons.Filled.Tag` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Filter | `line.3.horizontal.decrease` | `Icons.Filled.FilterList` |
| Sort | `arrow.up.arrow.down` | `Icons.Filled.SwapVert` |
| Updates | `bubble.left` | `Icons.Filled.ChatBubbleOutline` |
| Automation | `bolt` | `Icons.Filled.Bolt` |
| Main Table view | `tablecells` | `Icons.Filled.GridOn` |
| Kanban view | `rectangle.split.3x1` | `Icons.Filled.ViewKanban` |
| Timeline view | `calendar.day.timeline.left` | `Icons.Filled.AccountTree` |
| Chart view | `chart.pie` | `Icons.Filled.PieChart` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; light canvas wants dark-content system bars (light-content in night). The FAB sits above the `NavigationBar` and respects the gesture inset; inline cell edit uses `Modifier.imePadding()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen/board/group titles, body, item names. Pin layout-sensitive text (status labels, 10sp tab labels, person initials, column headers) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. Add `fontFeatureSettings = "tnum"` on Numbers/date/percentage text.
- **TalkBack**: never rely on cell color alone — always render the label text and set `contentDescription = "Status: {label}, double tap to change"`; announce the group stripe via group context ("Group {name}, item {name}"); the battery rollup as "Group progress: {x}% done, {y}% working, {z}% stuck". Status cell is `Role.Button`.
- **Touch targets**: Material guidance is 48.dp — the status cell row is ≥ 48.dp tall and fully tappable; give the 14.dp group caret a 48.dp hit area; the FAB is 56.dp; primary buttons ≥ 36.dp.
- **Contrast**: white on `#00C875` / `#FDAB3D` / `#E2445C` / `#0073EA` passes WCAG AA at 11sp/700. Yellow `#FFCB00`, teal `#00D2D2`, and bright-green `#9CD326` MUST use `#323338` text (white fails) — the `onColor()` helper enforces this; validate any user-remapped color.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the cell-color crossfade and battery-segment animation (snap instead) and skip confetti; keep the soft haptic and selection state.
- **Night mode**: invert chrome via the `Night*` palette — `#181B34` deep indigo, NOT true black; text → `#F5F6F8`. Keep the multicolor data palette **fully saturated and identical** — meaning must never wash out. Shadows vanish on night, so the 1dp `NightCellBorder` on Kanban cards is the elevation cue; the FAB keeps its blue surface. Do **not** enable Material You `dynamicColorScheme()` — monday.com's multicolor identity must hold regardless of wallpaper.
