# Todoist (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Todoist's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (the priority checkbox, the hero task row, the tinted-shadow FAB, the quick-add card), `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the whisper-quiet white canvas, Todoist Red as a strict verb color, the four-tier priority checkbox, and — critically — the **tinted red FAB shadow**) while making everything idiomatic Android — a `NavigationBar`/drawer instead of a UITabBar, `SwipeToDismissBox` instead of `.swipeActions`, `LocalHapticFeedback` instead of `.sensoryFeedback`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. No remote images and no color extraction, so neither Coil nor `androidx.palette` is required.

## 1. Color Tokens

```kotlin
// ui/theme/TodoistColors.kt
import androidx.compose.ui.graphics.Color

object TodoistColors {
    // Canvas & Surfaces
    val Canvas       = Color(0xFFFFFFFF)
    val SurfaceGray  = Color(0xFFFAFAFA)
    val SurfaceGray2 = Color(0xFFF0F0F0)
    val Divider      = Color(0xFFEEEEEE)

    // Text
    val Ink          = Color(0xFF202020) // intentional: warm near-black, never pure #000
    val Secondary    = Color(0xFF808080)
    val Tertiary     = Color(0xFFB0B0B0)

    // Brand & Priorities
    val Red          = Color(0xFFDC4C3E) // FAB, P1, brand mark, primary CTA — verbs only
    val RedPressed   = Color(0xFFB53C30)
    val RedTint      = Color(0xFFFBE5E2)
    val P2Orange     = Color(0xFFEB8909)
    val P3Blue       = Color(0xFF246FE0)
    val P4Gray       = Color(0xFFB0B0B0)

    // Semantic
    val Success      = Color(0xFF058527)
    val Error        = Color(0xFFD1453B)

    // Dark mode
    val DarkCanvas   = Color(0xFF1F1F1F)
    val DarkSurface  = Color(0xFF282828)
    val DarkSurface2 = Color(0xFF363636)
    val DarkDivider  = Color(0xFF3D3D3D)
    val DarkText     = Color(0xFFE8E8E8)
    val DarkTextSec  = Color(0xFFA0A0A0)
    val RedDark      = Color(0xFFE44332) // brightens slightly for dark contrast

    // User-selectable project palette (subset — the colored dots)
    val ProjBerryRed = Color(0xFFB8255F)
    val ProjRed      = Color(0xFFDB4035)
    val ProjOrange   = Color(0xFFFF9933)
    val ProjYellow   = Color(0xFFFAD000)
    val ProjGreen    = Color(0xFF299438)
    val ProjMint     = Color(0xFF6ACCBC)
    val ProjTeal     = Color(0xFF158FAD)
    val ProjSky      = Color(0xFF14AAF5)
    val ProjBlue     = Color(0xFF4073FF)
    val ProjGrape    = Color(0xFF884DFF)
    val ProjViolet   = Color(0xFFAF38EB)
    val ProjMagenta  = Color(0xFFE05194)
}
```

Todoist is light-first (white canvas, content-forward). Wire a Material 3 `lightColorScheme` so ripples, dividers, and stock component defaults inherit the brand; provide the dark scheme (engineered for late-night planning).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val TodoistLight = lightColorScheme(
    primary        = TodoistColors.Red,
    onPrimary      = Color.White,
    background     = TodoistColors.Canvas,
    onBackground   = TodoistColors.Ink,
    surface        = TodoistColors.SurfaceGray,
    onSurface      = TodoistColors.Ink,
    surfaceVariant = TodoistColors.SurfaceGray2,
    outline        = TodoistColors.Divider,
    error          = TodoistColors.Error,
)

private val TodoistDark = darkColorScheme(
    primary        = TodoistColors.RedDark,
    onPrimary      = Color.White,
    background     = TodoistColors.DarkCanvas,
    onBackground   = TodoistColors.DarkText,
    surface        = TodoistColors.DarkSurface,
    onSurface      = TodoistColors.DarkText,
    surfaceVariant = TodoistColors.DarkSurface2,
    outline        = TodoistColors.DarkDivider,
    error          = TodoistColors.Error,
)

@Composable
fun TodoistTheme(darkTheme: Boolean = isSystemInDarkTheme(), content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (darkTheme) TodoistDark else TodoistLight,
        typography = TodoistTypography,
        content = content,
    )
```

## 2. Typography

Todoist deliberately uses the **platform font** — no custom face — to feel like a respectful native citizen. On Android the platform face is **Roboto**; use `FontFamily.Default` (Compose resolves Roboto / the device system font and picks the right optical variant). This mirrors Todoist's "SF Pro, no exceptions" decision.

```kotlin
// ui/theme/TodoistType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

private val Sys = FontFamily.Default // Roboto / system — Todoist uses the platform face by design

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object TodoistText {
    val LargeNav    = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 34.sp, lineHeight = 39.sp, letterSpacing = (-0.4).sp)
    val InlineNav   = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val SectionHdr  = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.6.sp) // UPPERCASE at call site
    val TaskBody    = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val TaskBold    = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val Subtask     = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val Meta        = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 16.sp)
    val MetaOverdue = TextStyle(Sys, fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 16.sp)
    val SidebarSys  = TextStyle(Sys, fontWeight = FontWeight.Medium,   fontSize = 15.sp, lineHeight = 19.sp)
    val SidebarProj = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 19.sp)
    val QuickAdd    = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 22.sp)
    val Button      = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp)
    val FabGlyph    = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 28.sp, lineHeight = 28.sp)
    val Tab         = TextStyle(Sys, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Caption     = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val KarmaNum    = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val TodoistTypography = Typography(
    headlineLarge = TodoistText.LargeNav,
    titleMedium   = TodoistText.InlineNav,
    bodyLarge     = TodoistText.TaskBody,
    bodyMedium    = TodoistText.Meta,
    labelLarge    = TodoistText.Button,
    labelSmall    = TodoistText.Tab,
)
```

## 3. Signature Components

### Priority Checkbox

The four-tier priority lives on the **checkbox stroke**, never the row background. Mirrors DESIGN-swiftui.md §3.

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

enum class Priority { P1, P2, P3, P4 }

val Priority.color: Color
    get() = when (this) {
        Priority.P1 -> TodoistColors.Red
        Priority.P2 -> TodoistColors.P2Orange
        Priority.P3 -> TodoistColors.P3Blue
        Priority.P4 -> TodoistColors.P4Gray
    }

val Priority.fillTint: Color
    get() = when (this) {
        Priority.P1 -> TodoistColors.Red.copy(alpha = 0.10f)
        Priority.P2 -> TodoistColors.P2Orange.copy(alpha = 0.08f)
        Priority.P3 -> TodoistColors.P3Blue.copy(alpha = 0.08f)
        Priority.P4 -> Color.Transparent
    }

@Composable
fun TodoistCheckbox(
    priority: Priority,
    checked: Boolean,
    onCheckedChange: (Boolean) -> Unit,
    modifier: Modifier = Modifier,
) {
    val haptics = LocalHapticFeedback.current
    val interaction = remember { MutableInteractionSource() }

    Box(
        modifier = modifier
            .size(44.dp) // 44.dp hit area — never compress
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS light impact
                onCheckedChange(!checked)
            },
        contentAlignment = Alignment.Center,
    ) {
        Box(
            Modifier
                .size(22.dp)
                .clip(CircleShape)
                .background(if (checked) priority.color else priority.fillTint)
                .border(1.5.dp, priority.color, CircleShape),
            contentAlignment = Alignment.Center,
        ) {
            if (checked) {
                Icon(Icons.Filled.Check, contentDescription = null, tint = Color.White, modifier = Modifier.size(12.dp))
            }
        }
    }
}
```

### Task Row (The Hero Component)

Edge-to-edge, 52.dp minimum, content + metadata, divider inset 64.dp from the left (aligned with task content, not the checkbox). On complete: strikethrough + fade.

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.automirrored.outlined.Comment
import androidx.compose.material3.Text
import androidx.compose.ui.draw.drawBehind
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.text.style.TextOverflow

sealed interface TaskDate {
    data object Today : TaskDate
    data object Tomorrow : TaskDate
    data class Overdue(val label: String) : TaskDate
    data class Future(val label: String) : TaskDate
}

@Composable
fun TaskRow(
    content: String,
    priority: Priority,
    date: TaskDate?,
    project: Pair<String, Color>?,
    commentCount: Int,
    checked: Boolean,
    onCheckedChange: (Boolean) -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .background(TodoistColors.Canvas)
            .heightIn(min = 52.dp)
            .drawBehind {
                val inset = 64.dp.toPx()
                drawLine(
                    TodoistColors.Divider,
                    Offset(inset, size.height),
                    Offset(size.width, size.height),
                    0.5.dp.toPx(),
                )
            }
            .padding(horizontal = 16.dp, vertical = 12.dp),
        verticalAlignment = Alignment.Top,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        TodoistCheckbox(priority, checked, onCheckedChange)

        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(
                content,
                style = TodoistText.TaskBody,
                color = if (checked) TodoistColors.Tertiary else TodoistColors.Ink,
                textDecoration = if (checked) TextDecoration.LineThrough else TextDecoration.None,
                maxLines = 3,
                overflow = TextOverflow.Ellipsis,
            )
            if (date != null || project != null || commentCount > 0) {
                Row(horizontalArrangement = Arrangement.spacedBy(12.dp), verticalAlignment = Alignment.CenterVertically) {
                    date?.let { DatePill(it) }
                    project?.let { (name, c) -> ProjectTag(name, c) }
                    if (commentCount > 0) CommentBadge(commentCount)
                }
            }
        }
    }
}

@Composable
private fun DatePill(d: TaskDate) {
    when (d) {
        TaskDate.Today        -> Text("Today", style = TodoistText.Meta, color = TodoistColors.Success)
        TaskDate.Tomorrow     -> Text("Tomorrow", style = TodoistText.Meta, color = TodoistColors.Ink)
        is TaskDate.Overdue   -> Text(d.label, style = TodoistText.MetaOverdue, color = TodoistColors.Red)
        is TaskDate.Future    -> Text(d.label, style = TodoistText.Meta, color = TodoistColors.Secondary)
    }
}

@Composable
fun ProjectTag(name: String, color: Color) {
    Row(horizontalArrangement = Arrangement.spacedBy(6.dp), verticalAlignment = Alignment.CenterVertically) {
        Box(Modifier.size(8.dp).clip(CircleShape).background(color))
        Text("# $name", style = TodoistText.Meta, color = TodoistColors.Secondary)
    }
}

@Composable
private fun CommentBadge(count: Int) {
    Row(horizontalArrangement = Arrangement.spacedBy(4.dp), verticalAlignment = Alignment.CenterVertically) {
        Icon(Icons.AutoMirrored.Outlined.Comment, null, tint = TodoistColors.Secondary, modifier = Modifier.size(12.dp))
        Text("$count", style = TodoistText.Meta, color = TodoistColors.Secondary)
    }
}
```

### Floating Action Button (Tinted Red Shadow — The Signature)

This is one of the most-copied details in the app: the FAB shadow is **dyed Todoist Red**, not generic black. Compose `Modifier.shadow` accepts `spotColor`/`ambientColor` on API 28+; tint both to red.

```kotlin
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material.icons.filled.Add
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow

@Composable
fun TodoistFAB(onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.94f else 1f, label = "fabScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .size(56.dp)
            .scale(scale)
            .shadow(
                elevation = if (pressed) 4.dp else 16.dp,
                shape = CircleShape,
                spotColor = TodoistColors.Red,    // SIGNATURE: tinted red, not black
                ambientColor = TodoistColors.Red,
            )
            .clip(CircleShape)
            .background(if (pressed) TodoistColors.RedPressed else TodoistColors.Red)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                onClick()
            }
            .semantics { contentDescription = "Add task" },
        contentAlignment = Alignment.Center,
    ) {
        Icon(Icons.Filled.Add, contentDescription = null, tint = Color.White, modifier = Modifier.size(28.dp))
    }
}
```

> Pre-API-28, `spotColor` is ignored (shadows are always black). Fall back to drawing a soft red radial glow behind the circle with `Modifier.drawBehind` so the signature reads on every device.

### Quick-Add Card (Natural-Language Input)

Bottom-sheet, natural-language entry with inline smart-parsing chips. Use a Material 3 `ModalBottomSheet` for the container.

```kotlin
import androidx.compose.foundation.horizontalScroll
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.HorizontalDivider
import androidx.compose.material3.Surface
import androidx.compose.ui.graphics.SolidColor
import androidx.compose.ui.text.input.TextFieldValue

@Composable
fun QuickAddCard(
    onAdd: (String) -> Unit,
    modifier: Modifier = Modifier,
) {
    var text by remember { mutableStateOf(TextFieldValue("")) }
    var selectedDate by remember { mutableStateOf<String?>("Today") }
    var selectedPriority by remember { mutableStateOf(Priority.P4) }
    val haptics = LocalHapticFeedback.current

    Surface(
        color = TodoistColors.Canvas,
        shape = RoundedCornerShape(topStart = 16.dp, topEnd = 16.dp),
        shadowElevation = 4.dp,
        modifier = modifier,
    ) {
        Column(Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(16.dp)) {
            Text("Add task", style = TodoistText.InlineNav, color = TodoistColors.Ink)

            BasicTextField(
                value = text,
                onValueChange = { text = it },
                textStyle = TodoistText.QuickAdd.copy(color = TodoistColors.Ink),
                cursorBrush = SolidColor(TodoistColors.Red),
                modifier = Modifier.fillMaxWidth(),
                decorationBox = { inner ->
                    if (text.text.isEmpty()) {
                        Text("Take out the trash today p1 #home", style = TodoistText.QuickAdd, color = TodoistColors.Tertiary)
                    }
                    inner()
                },
            )

            if (text.text.isNotEmpty()) {
                Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                    selectedDate?.let { SmartChip(it, TodoistColors.Success) }
                    if (selectedPriority != Priority.P4) {
                        SmartChip("P${selectedPriority.ordinal + 1}", selectedPriority.color)
                    }
                }
            }

            HorizontalDivider(color = TodoistColors.Divider)

            Row(
                Modifier.horizontalScroll(rememberScrollState()),
                horizontalArrangement = Arrangement.spacedBy(8.dp),
            ) {
                ActionChip(Icons.Filled.CalendarToday, selectedDate ?: "Date")
                ActionChip(Icons.Outlined.Flag, "Priority")
                ActionChip(Icons.Filled.Tag, "Project")
                ActionChip(Icons.Filled.Label, "Labels")
                ActionChip(Icons.Filled.Notifications, "Reminders")
            }

            Box(Modifier.fillMaxWidth(), contentAlignment = Alignment.CenterEnd) {
                val enabled = text.text.isNotEmpty()
                Box(
                    Modifier
                        .clip(RoundedCornerShape(8.dp))
                        .background(if (enabled) TodoistColors.Red else TodoistColors.SurfaceGray2)
                        .clickable(enabled = enabled) {
                            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS success
                            onAdd(text.text)
                        }
                        .padding(horizontal = 24.dp, vertical = 12.dp),
                ) {
                    Text("Add task", style = TodoistText.Button, color = if (enabled) Color.White else TodoistColors.Tertiary)
                }
            }
        }
    }
}

@Composable
private fun SmartChip(label: String, color: Color) {
    Box(
        Modifier.clip(RoundedCornerShape(6.dp)).background(color.copy(alpha = 0.12f))
            .padding(horizontal = 8.dp, vertical = 4.dp)
    ) {
        Text(label, style = TodoistText.Meta.copy(fontWeight = FontWeight.Medium), color = color)
    }
}

@Composable
private fun ActionChip(icon: androidx.compose.ui.graphics.vector.ImageVector, label: String) {
    Row(
        Modifier.clip(RoundedCornerShape(50)).border(1.dp, TodoistColors.Divider, RoundedCornerShape(50))
            .padding(horizontal = 12.dp, vertical = 8.dp),
        horizontalArrangement = Arrangement.spacedBy(6.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(icon, null, tint = TodoistColors.Ink, modifier = Modifier.size(12.dp))
        Text(label, style = TodoistText.Meta.copy(fontWeight = FontWeight.Medium), color = TodoistColors.Ink)
    }
}
```

### Section Header & Sidebar Row

```kotlin
import androidx.compose.material.icons.filled.ChevronRight
import androidx.compose.material.icons.filled.ExpandMore

@Composable
fun TodoistSectionHeader(title: String, isOverdue: Boolean, collapsed: Boolean, onToggle: () -> Unit) {
    Row(
        Modifier
            .fillMaxWidth()
            .background(TodoistColors.Canvas)
            .height(36.dp)
            .drawBehind { drawLine(TodoistColors.Divider, Offset(0f, size.height), Offset(size.width, size.height), 0.5.dp.toPx()) }
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Text(
            title.uppercase(),
            style = TodoistText.SectionHdr,
            color = if (isOverdue) TodoistColors.Red else TodoistColors.Secondary,
        )
        Spacer(Modifier.weight(1f))
        Icon(
            if (collapsed) Icons.Filled.ChevronRight else Icons.Filled.ExpandMore,
            contentDescription = if (collapsed) "Expand" else "Collapse",
            tint = if (isOverdue) TodoistColors.Red else TodoistColors.Secondary,
            modifier = Modifier.size(48.dp).clickable(onClick = onToggle).padding(14.dp),
        )
    }
}

@Composable
fun SidebarRow(
    icon: androidx.compose.ui.graphics.vector.ImageVector,
    label: String,
    count: Int?,
    selected: Boolean,
    overdue: Boolean,
    onClick: () -> Unit,
) {
    Box {
        Row(
            Modifier
                .fillMaxWidth()
                .height(44.dp)
                .background(if (selected) TodoistColors.Red.copy(alpha = 0.08f) else Color.Transparent)
                .clickable(onClick = onClick)
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Icon(icon, null, tint = TodoistColors.Ink, modifier = Modifier.size(20.dp))
            Text(label, style = TodoistText.SidebarSys, color = TodoistColors.Ink)
            Spacer(Modifier.weight(1f))
            if (count != null && count > 0) {
                Box(
                    Modifier
                        .then(if (overdue) Modifier.clip(CircleShape).background(TodoistColors.Red) else Modifier)
                        .padding(horizontal = 8.dp, vertical = 2.dp)
                ) {
                    Text("$count", style = TodoistText.Meta, color = if (overdue) Color.White else TodoistColors.Secondary)
                }
            }
        }
        if (selected) {
            Box(Modifier.width(3.dp).fillMaxHeight().background(TodoistColors.Red))
        }
    }
}
```

## 4. Swipe Actions & Task-Completion Collapse (the distinctive interaction)

Todoist has no color extraction. Its defining dynamic moves are **swipe-to-schedule** and the **strikethrough → collapse-upward** completion. On Android, swipe uses Material 3 `SwipeToDismissBox`; the collapse uses `AnimatedVisibility` + `shrinkVertically`.

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.fadeOut
import androidx.compose.animation.shrinkVertically
import androidx.compose.animation.core.tween
import androidx.compose.material3.SwipeToDismissBox
import androidx.compose.material3.SwipeToDismissBoxValue
import androidx.compose.material3.rememberSwipeToDismissBoxState

@Composable
fun SwipeableTaskRow(
    visible: Boolean,
    onScheduleToday: () -> Unit,
    onPostpone: () -> Unit,
    rowContent: @Composable () -> Unit,
) {
    // Strikethrough already lives in TaskRow; here we collapse the row upward on complete (~250ms)
    AnimatedVisibility(
        visible = visible,
        exit = shrinkVertically(animationSpec = tween(250)) + fadeOut(tween(250)),
    ) {
        val dismissState = rememberSwipeToDismissBoxState(
            confirmValueChange = {
                when (it) {
                    SwipeToDismissBoxValue.StartToEnd -> { onScheduleToday(); false } // swipe right → schedule (green)
                    SwipeToDismissBoxValue.EndToStart -> { onPostpone(); false }       // swipe left → postpone (orange)
                    else -> false
                }
            }
        )
        SwipeToDismissBox(
            state = dismissState,
            backgroundContent = {
                val toEnd = dismissState.dismissDirection == SwipeToDismissBoxValue.StartToEnd
                Box(
                    Modifier
                        .fillMaxSize()
                        .background(if (toEnd) TodoistColors.Success else TodoistColors.P2Orange)
                        .padding(horizontal = 20.dp),
                    contentAlignment = if (toEnd) Alignment.CenterStart else Alignment.CenterEnd,
                ) {
                    Icon(
                        if (toEnd) Icons.Filled.CalendarToday else Icons.Filled.Schedule,
                        contentDescription = null, tint = Color.White, modifier = Modifier.size(20.dp),
                    )
                }
            },
        ) { rowContent() }
    }
}
```

Hoist `visible` and the completion logic into a `ViewModel`: tapping the checkbox flips `checked` (animating fill + check ~200ms), then after an 80ms hold sets `visible = false`, collapsing the row upward while the list below slides up.

## 5. Navigation (sidebar drawer + iPad rail)

On iPhone Todoist uses a full-screen **drawer** (hamburger), not a tab bar; the iPad tab bar maps to a `NavigationBar`. Use a Material 3 `ModalNavigationDrawer` for phones. Android has no live blur — the large-title nav uses a 92%-opaque canvas `Surface` when content scrolls beneath, instead of `.regularMaterial`.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun TodoistTabBar(selected: Int, onSelect: (Int) -> Unit) { // iPad / large-screen only
    NavigationBar(containerColor = TodoistColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Today"    to Icons.Filled.CalendarToday,
            "Upcoming" to Icons.Filled.EventNote,
            "Search"   to Icons.Filled.Search,
            "Browse"   to Icons.Filled.GridView,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = TodoistText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = TodoistColors.Red,
                    selectedTextColor   = TodoistColors.Red,
                    unselectedIconColor = TodoistColors.Secondary,
                    unselectedTextColor = TodoistColors.Secondary,
                    indicatorColor      = TodoistColors.Red.copy(alpha = 0.08f),
                ),
            )
        }
    }
}
```

The FAB sits 24.dp above the bottom bar (or 88.dp above the home indicator when there is no bar) at the trailing edge — render it in the `Scaffold` `floatingActionButton` slot.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Checkbox tap | fill `animateColorAsState` transparent → priority color over 200ms `tween`; check icon fades in; `HapticFeedbackType.TextHandleMove` |
| Task complete | 80ms hold → `AnimatedVisibility` `shrinkVertically(tween(250))` + `fadeOut`; list below slides up |
| Task uncomplete | reverse — `expandVertically` + `fadeIn` |
| FAB tap | `animateFloatAsState(0.94f)` + shadow `16.dp → 4.dp`; `HapticFeedbackType.LongPress` |
| Quick-add sheet | Material 3 `ModalBottomSheet` (spring); commit → success-style haptic |
| Smart-parsing chip | `AnimatedVisibility` scale 0.8 → 1.0 + fade over 200ms; `HapticFeedbackType.TextHandleMove` per chip |
| Karma flame pulse | `rememberInfiniteTransition` alpha 0.7 → 1.0, 800ms `RepeatMode.Reverse` |

```kotlin
// Karma flame ember pulse (DESIGN.md §4 Karma Flame)
@Composable
fun KarmaFlame() {
    val t = rememberInfiniteTransition(label = "karma")
    val a by t.animateFloat(0.7f, 1f, infiniteRepeatable(tween(800), RepeatMode.Reverse), label = "ember")
    Icon(
        Icons.Filled.LocalFireDepartment,
        contentDescription = "Karma streak",
        tint = TodoistColors.Red,
        modifier = Modifier.size(14.dp).graphicsLayer { alpha = a },
    )
}
```

Haptics: prefer `LocalHapticFeedback`. For the lighter checkbox tick use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`; for the quick-add success a `Vibrator` `VibrationEffect.createOneShot(10, ...)` approximates iOS `.notificationOccurred(.success)`.

## 7. Icons

Todoist uses standard system glyphs (no custom typeface, minimal custom iconography). The closest first-party set is `androidx.compose.material:material-icons-extended`; the flag and karma flame are good vector-drawable candidates if you want pixel parity.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Checkbox empty | `circle` | `Icons.Outlined.Circle` |
| Checkbox complete | `circle.fill` + `checkmark` | filled circle + `Icons.Filled.Check` |
| Priority flag | `flag.fill` / `flag` | `Icons.Filled.Flag` / `Icons.Outlined.Flag` |
| FAB | `plus` | `Icons.Filled.Add` |
| Today filter | `calendar` | `Icons.Filled.CalendarToday` |
| Upcoming | `calendar.badge.clock` | `Icons.Filled.EventNote` |
| Filters & Labels | `magnifyingglass` | `Icons.Filled.Search` |
| Project (sidebar) | color dot | colored `Box` + `CircleShape` |
| Comment count | `bubble.right` | `Icons.AutoMirrored.Outlined.Comment` |
| Sort / filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Menu (drawer) | `line.horizontal.3` | `Icons.Filled.Menu` |
| Karma flame | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Schedule (swipe) | `calendar` | `Icons.Filled.CalendarToday` |
| Reminders (chip) | `bell` | `Icons.Filled.Notifications` |
| Labels (chip) | `tag` | `Icons.Filled.Label` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; the tinted-shadow FAB needs API 28+ for `spotColor` — see fallback in §3). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. Apply `Scaffold` insets or `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the FAB sits 24.dp above the gesture nav and edge-to-edge task rows aren't clipped by the cutout.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on task content, sidebar items, and the large nav title. Pin layout-sensitive text: the 13sp UPPERCASE section headers, 13sp date metadata, the 28sp FAB glyph, and tab labels are layout-critical — derive from `dp` or wrap in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. The 22sp checkbox hit area must stay 44.dp regardless.
- **TalkBack**: the task row should merge into one element with `Modifier.semantics(mergeDescendants = true) { contentDescription = "$priority, $content, $dateText, $project, $commentCount comments" }`, and the checkbox must be a **separate** focusable element ("Mark complete" / "Mark incomplete"). Priority is non-decorative meaning — it must be in the spoken label, never implied by color alone.
- **Touch targets**: Material guidance is 48.dp minimum. The 56.dp FAB clears it; the 22.dp checkbox carries a 44.dp hit area via padding (do not compress even if the row visually looks smaller); the section-header chevron carries a 48.dp hit area.
- **Contrast**: Ink `#202020` on white passes WCAG AAA at all sizes. Secondary `#808080` on white only passes AA at 14sp+ Medium or 18sp+ — used carefully for 13sp metadata; if targeting strict compliance, bump metadata toward `#6E6E6E`. The P2 orange `#EB8909` on white is a known low-contrast pair for the date pill — it passes AA only at 16sp+ Bold, so keep the overdue/today states (red/green) for anything smaller.
- **Reduce motion**: when a reduced-motion preference is set, skip the 250ms collapse-upward (instant fade out) and the smart-chip scale-in.
- **Dynamic color**: Todoist *could* embrace Material You for a system-blended feel, but the brand depends on the exact carmine `#DC4C3E` (FAB, P1, brand mark) — keep `dynamicColorScheme` **disabled** so the verb-red and the four-tier priority colors never shift with the user's wallpaper.
