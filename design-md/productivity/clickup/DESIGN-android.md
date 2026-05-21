# ClickUp (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports ClickUp's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, the brand `Brush`, a `Typography` set, paste-ready `@Composable`s, the dense custom-status list + squircle gradient FAB + AI bar, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (ClickUp's deep blue-violet canvas, the reserved 3-stop brand gradient, dense custom-status rows, the squircle FAB) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a custom squircle `FloatingActionButton`, a `ModalBottomSheet` status picker, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars. No color extraction — ClickUp's palette is fixed, so Palette is not needed. ClickUp is light-first; a full deep-blue-violet dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/ClickUpColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.geometry.Offset

object ClickUpColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF7F7FB)
    val SurfacePressed = Color(0xFFEDEDF4)
    val Divider        = Color(0xFFE7E7EF)
    val CardBorder     = Color(0xFFDEDEE9)

    // Canvas & Surfaces (Dark)
    val DarkCanvas     = Color(0xFF1B1B2E) // deep blue-violet — NOT pure black
    val DarkSurface1   = Color(0xFF232338)
    val DarkSurface2   = Color(0xFF2C2C44)
    val DarkDivider    = Color(0xFF383852)
    val DarkCardBorder = Color(0xFF42425C)

    // Text
    val TextPrimary       = Color(0xFF1B1B2E)
    val TextSecondary     = Color(0xFF5E5E7A)
    val TextTertiary      = Color(0xFF9292A8)
    val DarkTextPrimary   = Color(0xFFF1F1F6)
    val DarkTextSecondary = Color(0xFFA6A6BC)
    val DarkTextTertiary  = Color(0xFF6E6E88)

    // Brand
    val Purple        = Color(0xFF7B68EE)
    val PurplePressed = Color(0xFF6A57DD)
    val Pink          = Color(0xFFFD71AF)
    val Blue          = Color(0xFF49CCF9)

    // Functional palette
    val Green  = Color(0xFF2ECC71)
    val Red    = Color(0xFFF44E6E)
    val Orange = Color(0xFFFF9F1A)
    val Yellow = Color(0xFFFFCC00)
    val Teal   = Color(0xFF1ECBE1)
    val Gray   = Color(0xFF5B5B79)

    // Semantic
    val Success = Color(0xFF2ECC71)
    val Error   = Color(0xFFF44E6E)
    val Warning = Color(0xFFFF9F1A)

    // The 3-stop brand gradient — FAB / primary CTA / AI ONLY
    val BrandGradient = Brush.linearGradient(
        colors = listOf(Purple, Pink, Blue),
        start = Offset(0f, 0f), end = Offset(Float.POSITIVE_INFINITY, Float.POSITIVE_INFINITY),
    )
    val AiGradient = Brush.horizontalGradient(
        colors = listOf(Purple.copy(alpha = 0.18f), Pink.copy(alpha = 0.16f), Blue.copy(alpha = 0.16f)),
    )
}

// On-solid text: blue / yellow / teal flip to dark
private val DarkOn = setOf(ClickUpColors.Blue, ClickUpColors.Yellow, ClickUpColors.Teal)
fun onColor(bg: Color): Color = if (bg in DarkOn) ClickUpColors.TextPrimary else Color.White

enum class Priority(val color: Color) {
    Urgent(ClickUpColors.Red), High(ClickUpColors.Orange), Normal(ClickUpColors.Blue), Low(ClickUpColors.TextTertiary)
}
```

Wire it into both schemes. ClickUp is light-first; the dark scheme uses `#1B1B2E` blue-violet (complements the gradient), never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val ClickUpLight = lightColorScheme(
    primary        = ClickUpColors.Purple,
    onPrimary      = Color.White,
    background     = ClickUpColors.Canvas,
    onBackground   = ClickUpColors.TextPrimary,
    surface        = ClickUpColors.SurfaceGray,
    onSurface      = ClickUpColors.TextPrimary,
    surfaceVariant = ClickUpColors.SurfacePressed,
    outline        = ClickUpColors.Divider,
    error          = ClickUpColors.Error,
)

private val ClickUpDark = darkColorScheme(
    primary        = ClickUpColors.Purple, // brand purple identical in dark
    onPrimary      = Color.White,
    background     = ClickUpColors.DarkCanvas,
    onBackground   = ClickUpColors.DarkTextPrimary,
    surface        = ClickUpColors.DarkSurface1,
    onSurface      = ClickUpColors.DarkTextPrimary,
    surfaceVariant = ClickUpColors.DarkSurface2,
    outline        = ClickUpColors.DarkDivider,
    error          = ClickUpColors.Error,
)

@Composable
fun ClickUpTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) ClickUpDark else ClickUpLight,
    typography = ClickUpTypography,
    content = content,
)
```

## 2. Typography

ClickUp uses **Plus Jakarta Sans** — drop the TTFs in `res/font/`. Confident: task names 600, titles 800, status pills 800.

```kotlin
// ui/theme/ClickUpType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val PJS = FontFamily(
    Font(R.font.pjs_regular,   FontWeight.Normal),
    Font(R.font.pjs_medium,    FontWeight.Medium),
    Font(R.font.pjs_semibold,  FontWeight.SemiBold),
    Font(R.font.pjs_bold,      FontWeight.Bold),
    Font(R.font.pjs_extrabold, FontWeight.ExtraBold),
)

object ClickUpText {
    val ScreenTitle = TextStyle(PJS, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.5).sp)
    val ListTitle   = TextStyle(PJS, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(PJS, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Subsection  = TextStyle(PJS, fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 22.sp)
    val Body        = TextStyle(PJS, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 23.sp)
    val TaskName    = TextStyle(PJS, fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 19.sp)
    val StatusLabel = TextStyle(PJS, fontWeight = FontWeight.ExtraBold, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.6.sp)
    val Meta        = TextStyle(PJS, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val Tag         = TextStyle(PJS, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val Button      = TextStyle(PJS, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp)
    val TextAction  = TextStyle(PJS, fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 14.sp)
    val Assignee    = TextStyle(PJS, fontWeight = FontWeight.Bold,      fontSize = 9.sp,  lineHeight = 9.sp,  letterSpacing = 0.2.sp)
    val Tab         = TextStyle(PJS, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val ClickUpTypography = Typography(
    headlineLarge = ClickUpText.ScreenTitle,
    headlineMedium = ClickUpText.ListTitle,
    titleMedium   = ClickUpText.Subsection,
    bodyMedium    = ClickUpText.Body,
    labelSmall    = ClickUpText.Tab,
)
```

## 3. Signature Components

### Task Row (dense — status box + flag + tags + assignee)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp
import androidx.compose.foundation.Canvas

@Composable
fun PriorityFlag(color: Color) {
    Canvas(Modifier.size(width = 12.dp, height = 10.dp)) {
        val p = Path().apply {
            moveTo(size.width / 2f, 0f)
            lineTo(size.width, size.height)
            lineTo(0f, size.height)
            close()
        }
        drawPath(p, color)
    }
}

@Composable
fun TaskRow(
    name: String,
    statusColor: Color,
    done: Boolean,
    priority: Priority?,
    tags: List<Pair<String, Color>>,
    due: Pair<String, Boolean>?, // text, overdue
    assigneeInitials: String,
    assigneeColor: Color,
    onCycleStatus: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Column(modifier.fillMaxWidth()) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 11.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(10.dp),
        ) {
            Box(
                Modifier
                    .size(18.dp)
                    .clip(RoundedCornerShape(5.dp))
                    .background(if (done) ClickUpColors.Green else Color.Transparent)
                    .border(2.dp, if (done) ClickUpColors.Green else statusColor, RoundedCornerShape(5.dp))
                    .clickable { onCycleStatus() },
                contentAlignment = Alignment.Center,
            ) {
                if (done) Icon(Icons.Filled.Check, null, tint = ClickUpColors.DarkCanvas, modifier = Modifier.size(11.dp))
            }

            Column(Modifier.weight(1f)) {
                Text(
                    name,
                    style = ClickUpText.TaskName.copy(
                        color = if (done) ClickUpColors.TextTertiary else ClickUpColors.TextPrimary,
                        textDecoration = if (done) TextDecoration.LineThrough else null,
                    ),
                    maxLines = 1, overflow = TextOverflow.Ellipsis,
                )
                Row(Modifier.padding(top = 6.dp), verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                    priority?.let { PriorityFlag(it.color) }
                    tags.forEach { (text, hue) ->
                        Box(
                            Modifier.clip(RoundedCornerShape(999.dp)).background(hue.copy(alpha = 0.22f))
                                .padding(horizontal = 7.dp, vertical = 2.dp),
                        ) { Text(text, style = ClickUpText.Tag.copy(color = hue)) }
                    }
                    due?.let {
                        Text(it.first, style = ClickUpText.Meta.copy(color = if (it.second) ClickUpColors.Red else ClickUpColors.TextSecondary))
                    }
                }
            }

            Box(
                Modifier.size(22.dp).clip(CircleShape).background(assigneeColor),
                contentAlignment = Alignment.Center,
            ) { Text(assigneeInitials, style = ClickUpText.Assignee.copy(color = Color.White)) }
        }
        HorizontalDivider(color = ClickUpColors.Divider, thickness = 1.dp)
    }
}
```

### Custom-Status Group Header

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.ChevronRight
import androidx.compose.ui.draw.rotate

@Composable
fun StatusGroupHeader(label: String, color: Color, count: Int, expanded: Boolean, onToggle: () -> Unit) {
    val rot by animateFloatAsState(if (expanded) 90f else 0f, tween(150), label = "statusCaret")
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 16.dp).padding(top = 10.dp, bottom = 6.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Row(
            Modifier.clickable { onToggle() },
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Icon(Icons.Filled.ChevronRight, null, tint = ClickUpColors.TextTertiary, modifier = Modifier.size(13.dp).rotate(rot))
            Box(
                Modifier.clip(RoundedCornerShape(5.dp)).background(color).padding(horizontal = 9.dp, vertical = 4.dp),
            ) { Text(label.uppercase(), style = ClickUpText.StatusLabel.copy(color = onColor(color))) }
            Text("$count", style = ClickUpText.Meta.copy(color = ClickUpColors.TextTertiary))
        }
        Spacer(Modifier.weight(1f))
        Icon(Icons.Filled.Add, "Add task", tint = ClickUpColors.TextTertiary, modifier = Modifier.size(13.dp))
    }
}
```

### Brand-Gradient FAB (squircle) + Primary Button

```kotlin
@Composable
fun CreateFab(onClick: () -> Unit) {
    Box(
        Modifier
            .size(56.dp)
            .clip(RoundedCornerShape(18.dp)) // squircle — NOT a circle
            .background(ClickUpColors.BrandGradient)
            .clickable(onClick = onClick),
        contentAlignment = Alignment.Center,
    ) { Icon(Icons.Filled.Add, "Create", tint = Color.White, modifier = Modifier.size(24.dp)) }
}

@Composable
fun PrimaryButton(title: String, onClick: () -> Unit) {
    Box(
        Modifier
            .clip(RoundedCornerShape(10.dp))
            .background(ClickUpColors.BrandGradient)
            .clickable(onClick = onClick)
            .padding(horizontal = 26.dp, vertical = 13.dp),
        contentAlignment = Alignment.Center,
    ) { Text(title, style = ClickUpText.Button.copy(color = Color.White)) }
}
```

### ClickUp AI Bar

```kotlin
import androidx.compose.material.icons.filled.AutoAwesome

@Composable
fun AiBar(action: String) {
    Row(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(10.dp))
            .background(ClickUpColors.AiGradient)
            .border(1.dp, ClickUpColors.Purple.copy(alpha = 0.4f), RoundedCornerShape(10.dp))
            .padding(horizontal = 14.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(Icons.Filled.AutoAwesome, null, tint = ClickUpColors.Purple, modifier = Modifier.size(16.dp))
        Text(
            buildString { append("ClickUp AI  ·  "); append(action) },
            style = ClickUpText.Meta.copy(color = ClickUpColors.TextSecondary),
        )
    }
}
```

### Board Card

```kotlin
@Composable
fun BoardCard(statusColor: Color, name: String, priority: Priority?, assigneeInitials: String, assigneeColor: Color) {
    Column(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(10.dp))
            .background(ClickUpColors.Canvas)
            .border(1.dp, ClickUpColors.CardBorder, RoundedCornerShape(10.dp)),
    ) {
        Box(Modifier.fillMaxWidth().height(3.dp).background(statusColor)) // status-color top accent
        Column(Modifier.padding(12.dp), verticalArrangement = Arrangement.spacedBy(10.dp)) {
            Text(name, style = ClickUpText.TaskName.copy(color = ClickUpColors.TextPrimary))
            Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween, verticalAlignment = Alignment.CenterVertically) {
                priority?.let { PriorityFlag(it.color) } ?: Spacer(Modifier.size(1.dp))
                Box(
                    Modifier.size(22.dp).clip(CircleShape).background(assigneeColor),
                    contentAlignment = Alignment.Center,
                ) { Text(assigneeInitials, style = ClickUpText.Assignee.copy(color = Color.White)) }
            }
        }
    }
}
```

## 4. Navigation

ClickUp has information-dense content but minimal chrome: a 4-tab bottom strip with the brand-purple accent (no Material tint pill) and a horizontally scrolling per-list view switcher.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun ClickUpBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = ClickUpColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"   to Icons.Filled.Home,
            "Tasks"  to Icons.AutoMirrored.Filled.List,
            "Inbox"  to Icons.Filled.Notifications,
            "Search" to Icons.Filled.Search,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = ClickUpText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = ClickUpColors.Purple,
                    selectedTextColor = ClickUpColors.Purple,
                    unselectedIconColor = ClickUpColors.TextTertiary,
                    unselectedTextColor = ClickUpColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — ClickUp has none
                ),
            )
        }
    }
}
```

The per-list view switcher (List / Board / Calendar / Gantt / Timeline / Doc / … 12+) is a horizontally scrolling `LazyRow` of clickable labels; the active one gets `Purple` text plus a 2.5.dp purple underline via `Modifier.drawBehind`. The Board view is a `LazyRow` of ~270.dp columns with a 3.dp status-color top accent on each card; dropping a card into a column changes its status.

## 5. Motion

ClickUp motion is purposeful; the gradient shimmer marks AI work.

| Moment | Compose recipe |
|--------|----------------|
| Status change | checkbox cycles; border/fill `animateColorAsState(tween(180))`; soft haptic; long-press → `ModalBottomSheet` status picker |
| Status group collapse/expand | caret `animateFloatAsState` 0 → 90° `tween(150)`; tasks `AnimatedVisibility(expandVertically(tween(200)) + fadeIn())` |
| FAB → create | navigate to a full-screen / bottom-sheet task editor; squircle scale on press |
| Drag task / board card | `LazyColumn`/`LazyRow` + `Modifier.draggable`; neighbors `animateItemPlacement(tween(200))`; soft haptic on drop; status accent recolors |
| AI generation | the AI bar's gradient sweeps via an `infiniteRepeatable` brush offset (~1.2s); result `fadeIn(tween(200))` on the gradient backdrop |
| View tab switch | 2.5.dp purple underline `animateDpAsState` translateX 200ms; view `Crossfade(tween(150))` |
| Page navigation / breadcrumb drill | Nav3/`NavHost` slide push `tween(300)` |

```kotlin
// The canonical ClickUp feedback — status checkbox color crossfade
val border by animateColorAsState(if (done) ClickUpColors.Green else statusColor, tween(180), label = "statusBorder")
Box(Modifier.border(2.dp, border, RoundedCornerShape(5.dp))) { /* check */ }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` on status change, drag start, and group toggle. For a softer tick use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`. Auto-save is silent; only show a "Saved" snackbar on manual save or error.

## 6. Icons

ClickUp's iconography maps cleanly to `androidx.compose.material:material-icons-extended`. The priority flag is a `Canvas`-drawn triangle, not an icon.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Tasks (tab) | `list.bullet` | `Icons.AutoMirrored.Filled.List` |
| Inbox (tab) | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Add / FAB | `plus` | `Icons.Filled.Add` |
| AI / sparkle | `sparkles` | `Icons.Filled.AutoAwesome` |
| Status group chevron | `chevron.right` | `Icons.Filled.ChevronRight` (rotate 90°) |
| Task complete | `checkmark` | `Icons.Filled.Check` (in status square) |
| Due date | `calendar` | `Icons.Filled.CalendarToday` |
| Assignee (empty) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Subtask | `arrow.turn.down.right` | `Icons.Filled.SubdirectoryArrowRight` |
| Comment | `bubble.left` | `Icons.Filled.ChatBubbleOutline` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Breadcrumb sep | `chevron.right` | `Icons.Filled.ChevronRight` |
| Filter | `line.3.horizontal.decrease` | `Icons.Filled.FilterList` |
| List view | `list.bullet` | `Icons.AutoMirrored.Filled.List` |
| Board view | `rectangle.split.3x1` | `Icons.Filled.ViewKanban` |
| Calendar view | `calendar` | `Icons.Filled.CalendarMonth` |
| Gantt view | `chart.bar.xaxis` | `Icons.Filled.Timeline` |
| Doc view | `doc.text` | `Icons.Filled.Description` |
| Slash menu | `slash.circle` | `Icons.Filled.Code` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; light canvas wants dark-content system bars (light-content in dark). The squircle FAB sits above the `NavigationBar` and respects the gesture inset; inline cell edit / AI compose use `Modifier.imePadding()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen/list/section titles, body, task names. Pin layout-sensitive text (status pills, 10sp tags, tab labels, assignee initials, status group labels) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. Add `fontFeatureSettings = "tnum"` on due-date/points/count text.
- **TalkBack**: collapse the dense row into one node with a combined label and custom actions (`Modifier.semantics { customActions = listOf(ChangeStatus, SetPriority, OpenTask) }`); announce the priority flag by name ("Priority: Urgent") — never color alone; announce status group pills with the full status word; label AI surfaces "ClickUp AI: {action}".
- **Touch targets**: Material guidance is 48.dp — give the 18.dp status checkbox a 48.dp hit area via padding (separate from row tap); the 13.dp group caret a 48.dp hit; the FAB is 56.dp; primary buttons ≥ 36.dp.
- **Contrast**: white on `#7B68EE` / `#2ECC71` / `#F44E6E` / `#FF9F1A` passes WCAG AA at 10sp/800. Blue `#49CCF9`, yellow `#FFCC00`, teal `#1ECBE1` MUST use dark text — the `onColor()` helper enforces this; validate any user-defined status color. Verify the white `+` on the gradient FAB across all three stops (it passes — all are dark enough at this saturation).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the status-color crossfade and the AI gradient sweep (snap / static tint instead); keep the soft haptic and selection state.
- **Dark mode**: invert chrome via the `Dark*` palette — `#1B1B2E` blue-violet, NOT true black; text → `#F1F1F6`. Keep the brand gradient + functional palette **identical** — the FAB/CTA/AI must stay vivid and status colors carry meaning. Shadows vanish on dark, so the 1dp `DarkCardBorder` + 3dp status-color top accent on board cards is the elevation cue; the FAB keeps its gradient. Do **not** enable Material You `dynamicColorScheme()` — ClickUp's gradient identity must hold regardless of wallpaper.
