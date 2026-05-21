# Asana (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Asana's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the task list + completion circle + status update card, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Asana's calm canvas, the single coral accent, the delightful completion circle, ~16% tinted object pills) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `FloatingActionButton` instead of a custom overlay, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars. No color extraction — Asana's palette is fixed, so Palette is not needed. Asana is light-mode-first; a full charcoal dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/AsanaColors.kt
import androidx.compose.ui.graphics.Color

object AsanaColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF9F8F8)
    val SurfacePressed = Color(0xFFF0EFEF)
    val Divider        = Color(0xFFEDEBE9)
    val CardBorder     = Color(0xFFE8E6E4)

    // Canvas & Surfaces (Dark)
    val DarkCanvas     = Color(0xFF1E1F21) // Asana charcoal — NOT pure black
    val DarkSurface1   = Color(0xFF252628)
    val DarkSurface2   = Color(0xFF2E2F31)
    val DarkDivider    = Color(0xFF35363A)
    val DarkCardBorder = Color(0xFF3A3B3F)

    // Text
    val TextPrimary       = Color(0xFF1E1F21)
    val TextSecondary     = Color(0xFF6D6E6F)
    val TextTertiary      = Color(0xFF9CA3A6)
    val DarkTextPrimary   = Color(0xFFF5F4F2)
    val DarkTextSecondary = Color(0xFFA9A9AA)
    val DarkTextTertiary  = Color(0xFF6F7073)

    // Brand (interactive) — coral is the ONLY accent
    val Coral        = Color(0xFFF06A6A)
    val CoralPressed = Color(0xFFD85B5B)
    val ActionBlue   = Color(0xFF4573D2)

    // Object palette (project / tag / field hues)
    val Plum    = Color(0xFF4573D2)
    val Aqua    = Color(0xFF4ECBC4)
    val Green   = Color(0xFF62D26F)
    val Yellow  = Color(0xFFF8DF72)
    val Orange  = Color(0xFFF1BD6C)
    val Magenta = Color(0xFFF26FD3)
    val Indigo  = Color(0xFF5A3FFF)
    val CoolGray = Color(0xFF8DA3B0)

    // Semantic
    val Success = Color(0xFF62D26F)
    val Error   = Color(0xFFE8384F)
    val Warning = Color(0xFFF8DF72)
}

// Object-hue → tint fill pair (~16% light, ~25% dark)
data class TagStyle(val solid: Color, val tint: Color)
val PlumTag   = TagStyle(AsanaColors.Plum,    AsanaColors.Plum.copy(alpha = 0.16f))
val AquaTag   = TagStyle(AsanaColors.Aqua,    AsanaColors.Aqua.copy(alpha = 0.16f))
val GreenTag  = TagStyle(AsanaColors.Green,   AsanaColors.Green.copy(alpha = 0.16f))
val YellowTag = TagStyle(AsanaColors.Yellow,  AsanaColors.Yellow.copy(alpha = 0.22f))
val OrangeTag = TagStyle(AsanaColors.Orange,  AsanaColors.Orange.copy(alpha = 0.18f))
```

Wire it into both schemes. Asana is light-first; the dark scheme uses the signature `#1E1F21` charcoal, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val AsanaLight = lightColorScheme(
    primary        = AsanaColors.Coral,
    onPrimary      = Color.White,
    background     = AsanaColors.Canvas,
    onBackground   = AsanaColors.TextPrimary,
    surface        = AsanaColors.SurfaceGray,
    onSurface      = AsanaColors.TextPrimary,
    surfaceVariant = AsanaColors.SurfacePressed,
    outline        = AsanaColors.Divider,
    error          = AsanaColors.Error,
)

private val AsanaDark = darkColorScheme(
    primary        = AsanaColors.Coral, // coral identical in dark
    onPrimary      = Color.White,
    background     = AsanaColors.DarkCanvas,
    onBackground   = AsanaColors.DarkTextPrimary,
    surface        = AsanaColors.DarkSurface1,
    onSurface      = AsanaColors.DarkTextPrimary,
    surfaceVariant = AsanaColors.DarkSurface2,
    outline        = AsanaColors.DarkDivider,
    error          = AsanaColors.Error,
)

@Composable
fun AsanaTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) AsanaDark else AsanaLight,
    typography = AsanaTypography,
    content = content,
)
```

## 2. Typography

Asana uses **Inter** across product surfaces — drop the TTFs in `res/font/`. Body 400, task rows 500, headings 700-800; quiet and legible.

```kotlin
// ui/theme/AsanaType.kt
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

object AsanaText {
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.5).sp)
    val NavTitle    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Subsection  = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 18.sp, lineHeight = 23.sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val TaskRow     = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 20.sp)
    val ListLabel   = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Pill        = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 15.sp)
    val TextAction  = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 14.sp)
    val Avatar      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val AsanaTypography = Typography(
    headlineLarge = AsanaText.ScreenTitle,
    headlineMedium = AsanaText.NavTitle,
    titleMedium   = AsanaText.Subsection,
    bodyMedium    = AsanaText.Body,
    labelSmall    = AsanaText.Tab,
)
```

## 3. Signature Components

### Task Row (with Completion Circle)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.unit.dp

@Composable
fun TaskRow(
    name: String,
    projectTag: Pair<String, TagStyle>?,
    due: Triple<String, Boolean, Unit>?, // text, overdue
    assigneeInitials: String,
    assigneeColor: Color,
    modifier: Modifier = Modifier,
) {
    var done by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current
    val scale = remember { Animatable(1f) }

    LaunchedEffect(done) {
        if (done) {
            scale.animateTo(0.8f, tween(80)); scale.animateTo(1.1f, tween(80)); scale.animateTo(1f, tween(60))
        }
    }

    Row(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 18.dp, vertical = 11.dp),
        verticalAlignment = Alignment.Top,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(
            Modifier
                .size(20.dp)
                .scale(scale.value)
                .clip(CircleShape)
                .background(if (done) AsanaColors.Success else Color.Transparent)
                .border(1.5.dp, if (done) AsanaColors.Success else AsanaColors.TextTertiary, CircleShape)
                .clickable {
                    done = !done
                    if (done) haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                },
            contentAlignment = Alignment.Center,
        ) {
            if (done) Icon(Icons.Filled.Check, null, tint = AsanaColors.DarkCanvas, modifier = Modifier.size(12.dp))
        }

        Column(Modifier.weight(1f)) {
            Text(
                name,
                style = AsanaText.TaskRow.copy(
                    color = if (done) AsanaColors.TextTertiary else AsanaColors.TextPrimary,
                    textDecoration = if (done) TextDecoration.LineThrough else null,
                ),
            )
            Row(Modifier.padding(top = 6.dp), horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                projectTag?.let { Pill(it.first, it.second) }
                due?.let {
                    val style = if (it.second) TagStyle(AsanaColors.Error, AsanaColors.Error.copy(alpha = 0.18f))
                                else TagStyle(AsanaColors.Orange, AsanaColors.Orange.copy(alpha = 0.18f))
                    Pill(it.first, style)
                }
            }
        }

        Box(
            Modifier.size(22.dp).clip(CircleShape).background(assigneeColor),
            contentAlignment = Alignment.Center,
        ) { Text(assigneeInitials, style = AsanaText.Avatar.copy(color = Color.White)) }
    }
    HorizontalDivider(color = AsanaColors.Divider, thickness = 1.dp)
}

@Composable
fun Pill(text: String, style: TagStyle) {
    Box(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(style.tint)
            .padding(horizontal = 9.dp, vertical = 3.dp),
    ) { Text(text, style = AsanaText.Pill.copy(color = style.solid)) }
}
```

### Section Header (collapsible)

```kotlin
import androidx.compose.material.icons.filled.ChevronRight
import androidx.compose.ui.draw.rotate

@Composable
fun SectionHeader(title: String, count: Int, expanded: Boolean, onToggle: () -> Unit) {
    val rot by animateFloatAsState(if (expanded) 90f else 0f, tween(150), label = "caret")
    Row(
        Modifier
            .fillMaxWidth()
            .clickable { onToggle() }
            .padding(horizontal = 18.dp).padding(top = 14.dp, bottom = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(Icons.Filled.ChevronRight, null, tint = AsanaColors.TextSecondary, modifier = Modifier.size(14.dp).rotate(rot))
        Text(title, style = AsanaText.ListLabel.copy(color = AsanaColors.TextPrimary))
        Text("$count", style = AsanaText.Pill.copy(color = AsanaColors.TextTertiary))
    }
}
```

### Status Update Card

```kotlin
enum class ProjectStatus(val label: String, val solid: Color) {
    OnTrack("On track", AsanaColors.Aqua),
    AtRisk("At risk",  AsanaColors.Yellow),
    OffTrack("Off track", AsanaColors.Error),
    Complete("Complete", AsanaColors.Success),
}

@Composable
fun StatusUpdateCard(status: ProjectStatus, postedDate: String, title: String, body: String) {
    Column(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(12.dp))
            .background(AsanaColors.Canvas)
            .border(1.dp, AsanaColors.Divider, RoundedCornerShape(12.dp))
            .padding(18.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween, verticalAlignment = Alignment.CenterVertically) {
            Box(
                Modifier.clip(RoundedCornerShape(999.dp)).background(status.solid.copy(alpha = 0.18f))
                    .padding(horizontal = 14.dp, vertical = 6.dp),
            ) { Text(status.label, style = AsanaText.Pill.copy(color = status.solid, fontWeight = FontWeight.Bold)) }
            Text(postedDate, style = AsanaText.Meta.copy(color = AsanaColors.TextSecondary))
        }
        Text(title, style = AsanaText.Section.copy(color = AsanaColors.TextPrimary))
        Text(body, style = AsanaText.Body.copy(color = AsanaColors.TextPrimary))
    }
}
```

### Floating Action Button

```kotlin
import androidx.compose.material.icons.filled.Add

@Composable
fun CreateTaskFab(onClick: () -> Unit) {
    FloatingActionButton(
        onClick = onClick,
        containerColor = AsanaColors.Coral,
        contentColor = Color.White,
        shape = CircleShape,
        modifier = Modifier.size(56.dp),
    ) {
        Icon(Icons.Filled.Add, contentDescription = "Create task", modifier = Modifier.size(24.dp))
    }
}
```

## 4. Navigation

Asana has minimal chrome: a 4-tab bottom strip with the coral accent (no Material tint pill) and per-screen view tabs.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun AsanaBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = AsanaColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "My Tasks" to Icons.Filled.CheckCircle,
            "Inbox"    to Icons.Filled.Notifications,
            "Search"   to Icons.Filled.Search,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = AsanaText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = AsanaColors.Coral,
                    selectedTextColor = AsanaColors.Coral,
                    unselectedIconColor = AsanaColors.TextTertiary,
                    unselectedTextColor = AsanaColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Asana has none
                ),
            )
        }
    }
}
```

The per-screen view switcher (List / Board / Calendar / Timeline / Files) is a `Row` of clickable labels; the active one gets `TextPrimary` color plus a 2.dp coral underline drawn with `Modifier.drawBehind`. The board view is a horizontally scrolling `LazyRow` of 280.dp columns (`SurfaceGray`, 12.dp radius) containing draggable cards.

## 5. Motion

Asana motion is purposeful and warm — the completion circle is the signature moment.

| Moment | Compose recipe |
|--------|----------------|
| Task complete | circle `Animatable` scale `0.8 → 1.1 → 1.0` (`tween(80/80/60)`); checkmark fades in; `HapticFeedbackType.LongPress` (success analog); name → tertiary + line-through over 150ms |
| Celebration creature | section fully done → `Animatable` translateX `-80 → screenWidth+80` over 1200ms, then `alpha → 0` |
| Section collapse/expand | caret `animateFloatAsState` 0 → 90° `tween(150)`; children `AnimatedVisibility(expandVertically(tween(200)) + fadeIn())` |
| FAB → new task | navigate to a bottom-sheet/full-screen task editor; FAB container scale on press |
| Board card drag | `LazyColumn`/`LazyRow` + `Modifier.draggable`; neighbors `animateItemPlacement(tween(200))`; soft haptic on drop |
| View tab switch | 2.dp coral underline `animateDpAsState` translateX 200ms; content `Crossfade(tween(150))` |
| Inbox read | unread coral dot `animateFloatAsState` alpha → 0 over 120ms on tap |
| Page navigation | Nav3/`NavHost` slide push `tween(300)` |

```kotlin
// The canonical Asana moment — completion circle bounce
val scale = remember { Animatable(1f) }
LaunchedEffect(done) {
    if (done) { scale.animateTo(0.8f, tween(80)); scale.animateTo(1.1f, tween(80)); scale.animateTo(1f, tween(60)) }
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` on task complete (success analog), block/card drag start, and section toggle. For a softer tick use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`. Auto-save is silent; only show a "Saved" snackbar on manual save or error.

## 6. Icons

Asana's iconography maps cleanly to `androidx.compose.material:material-icons-extended`. The completion circle is custom-drawn (a `CircleShape` border + `Check`), not a stock icon.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| My Tasks (tab) | `checkmark.circle` | `Icons.Filled.CheckCircle` |
| Inbox (tab) | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Task complete | `checkmark.circle.fill` | `Icons.Filled.Check` (in circle) |
| Add / FAB | `plus` | `Icons.Filled.Add` |
| Section caret | `chevron.right` | `Icons.Filled.ChevronRight` (rotate 90°) |
| Due date | `calendar` | `Icons.Filled.CalendarToday` |
| Assignee (empty) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Subtask | `arrow.turn.down.right` | `Icons.Filled.SubdirectoryArrowRight` |
| Attachment | `paperclip` | `Icons.Filled.AttachFile` |
| Comment | `bubble.left` | `Icons.Filled.ChatBubbleOutline` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Filter | `line.3.horizontal.decrease` | `Icons.Filled.FilterList` |
| Board view | `rectangle.split.3x1` | `Icons.Filled.ViewKanban` |
| Calendar view | `calendar` | `Icons.Filled.CalendarMonth` |
| Timeline view | `chart.bar.xaxis` | `Icons.Filled.Timeline` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; light canvas wants dark-content system bars (light-content in dark mode). The FAB sits above the `NavigationBar` and respects the gesture inset; the task editor uses `Modifier.imePadding()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen title, headings, body, task rows. Pin layout-sensitive text (pills, 10sp tab labels, list section labels, avatar initials) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. Add `fontFeatureSettings = "tnum"` (via `TextStyle`) on due-dates/counts.
- **TalkBack**: the completion circle and the row body are distinct nodes — circle `contentDescription = "Mark complete, {task}"` (and "Completed" when done); row `Modifier.semantics { role = Role.Button }` opening detail. Expose swipe actions via `Modifier.semantics { customActions = listOf(...) }`. Label status badges with their full status word.
- **Touch targets**: Material guidance is 48.dp — give the 20.dp completion circle and 14.dp caret a 48.dp hit area via padding; task rows are full-row tappable; the FAB is 56.dp; primary buttons ≥ 36.dp.
- **Contrast**: `#1E1F21` on `#FFFFFF` and `#F5F4F2` on `#1E1F21` pass WCAG AA. Tag pills use saturated text on a ~16% tint — validate yellow (`#F8DF72` on a light yellow tint may fail; use a deeper tint or darker text for the yellow/at-risk badge).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the completion-circle bounce and the celebration creature — substitute a plain `Crossfade` fill; keep the success haptic and the selection state.
- **Dark mode**: invert via the `Dark*` palette — `#1E1F21`, NOT true black; `#1E1F21` text becomes `#F5F4F2`; bump object-tag tint alpha to ~25%. Shadows are nearly invisible on dark, so the 1dp `DarkCardBorder` on board cards is the elevation cue; the FAB keeps its coral surface. Do **not** enable Material You `dynamicColorScheme()` — Asana's single-coral identity must hold regardless of wallpaper.
