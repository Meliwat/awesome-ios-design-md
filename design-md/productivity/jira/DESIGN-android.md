# Jira (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Jira's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the board + drag, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Atlassian's neutral grays, the single Jira Blue action color, semantic status lozenges, the issue card, the horizontal board) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `DropdownMenu` instead of an iOS popover, `LazyRow`/`LazyColumn` for the board, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars. No Material You dynamic color — Jira's brand blue and semantic palette are fixed; a full dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/JiraColors.kt
import androidx.compose.ui.graphics.Color

object JiraColors {
    // Brand (interactive)
    val Blue        = Color(0xFF0052CC)
    val BlueBold    = Color(0xFF1868DB) // interactive blue on dark
    val BluePressed = Color(0xFF09326C)
    val Link        = Color(0xFF0C66E4)
    val LinkDark    = Color(0xFF579DFF)
    val InkNavy     = Color(0xFF172B4D)

    // Canvas & Surfaces (Light)
    val Canvas       = Color(0xFFFFFFFF)
    val Sunken       = Color(0xFFF7F8F9)
    val SurfaceHover = Color(0xFFF1F2F4)
    val Divider      = Color(0x24091E42) // ≈14% navy
    val Border       = Color(0x0F091E42) // ≈6% navy

    // Canvas & Surfaces (Dark) — Atlassian neutral, NOT pure black
    val DarkCanvas   = Color(0xFF1D2125)
    val DarkSurface1 = Color(0xFF22272B)
    val DarkSurface2 = Color(0xFF2C333A)
    val DarkSurface3 = Color(0xFF38414A)
    val DarkDivider  = Color(0xFF38414A)

    // Text
    val TextPrimary    = Color(0xFF172B4D) // Atlassian Navy — NOT pure black
    val TextSubtle     = Color(0xFF44546F)
    val TextSubtlest   = Color(0xFF626F86)
    val DarkTextPrimary  = Color(0xFFC7D1DB)
    val DarkTextSubtle   = Color(0xFF9FADBC)
    val DarkTextSubtlest = Color(0xFF738496)

    // Issue-type accents
    val StoryGreen = Color(0xFF1F845A)
    val BugRed     = Color(0xFFC9372C)
    val TaskBlue   = Color(0xFF1868DB)
    val EpicPurple = Color(0xFF8270DB)
    val SubtaskGray = Color(0xFF5E6C84)

    // Semantic status — light fill/text
    val NeutralBg  = Color(0xFFDCDFE4); val NeutralTx = Color(0xFF44546F)
    val InfoBg     = Color(0xFFE9F2FF); val InfoTx    = Color(0xFF0055CC)
    val SuccessBg  = Color(0xFFDCFFF1); val SuccessTx = Color(0xFF216E4E)
    val WarnBg     = Color(0xFFFFF7D6); val WarnTx    = Color(0xFF7F5F01)
    val DangerBg   = Color(0xFFFFECEB); val DangerTx  = Color(0xFFAE2A19)
    val EpicBg     = Color(0xFFF3F0FF); val EpicTx    = Color(0xFF5E4DB2)

    // Semantic status — dark fill/text
    val NeutralBgD = Color(0xFF38414A); val NeutralTxD = Color(0xFF9FADBC)
    val InfoBgD    = Color(0xFF092957); val InfoTxD    = Color(0xFF8FB8F6)
    val SuccessBgD = Color(0xFF1C3329); val SuccessTxD = Color(0xFF7EE2B8)
    val DangerBgD  = Color(0xFF5D1F1A); val DangerTxD  = Color(0xFFFD9891)

    // Avatar / label palette
    val AvPurple  = Color(0xFF5E4DB2)
    val AvTeal    = Color(0xFF1D7F8C)
    val AvMagenta = Color(0xFF943D73)
}
```

Wire it into both schemes. Jira is light-first; the dark scheme uses Atlassian's `#1D2125` charcoal, never true black, and brightens interactive blue.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val JiraLight = lightColorScheme(
    primary        = JiraColors.Blue,
    onPrimary      = Color.White,
    background      = JiraColors.Sunken,
    onBackground   = JiraColors.TextPrimary,
    surface        = JiraColors.Canvas,
    onSurface      = JiraColors.TextPrimary,
    surfaceVariant = JiraColors.SurfaceHover,
    outline        = JiraColors.Divider,
    error          = JiraColors.DangerTx,
)

private val JiraDark = darkColorScheme(
    primary        = JiraColors.BlueBold,
    onPrimary      = Color.White,
    background      = JiraColors.DarkCanvas,
    onBackground   = JiraColors.DarkTextPrimary,
    surface        = JiraColors.DarkSurface1,
    onSurface      = JiraColors.DarkTextPrimary,
    surfaceVariant = JiraColors.DarkSurface2,
    outline        = JiraColors.DarkDivider,
    error          = JiraColors.DangerTxD,
)

@Composable
fun JiraTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) JiraDark else JiraLight,
    typography = JiraTypography,
    content = content,
)
```

## 2. Typography

Jira's family is **Atlassian Sans** (+ **Atlassian Mono** for code). Drop the TTFs in `res/font/`, or substitute **Inter** (SIL OFL). Body 400, card summaries 500, field/subsection labels 600, titles & lozenges 700. Tabular figures via `TextStyle(fontFeatureSettings = "tnum")`.

```kotlin
// ui/theme/JiraType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val AtlassianSans = FontFamily(
    Font(R.font.atlassian_sans_regular,  FontWeight.Normal),
    Font(R.font.atlassian_sans_medium,   FontWeight.Medium),
    Font(R.font.atlassian_sans_semibold, FontWeight.SemiBold),
    Font(R.font.atlassian_sans_bold,     FontWeight.Bold),
)
val AtlassianMono = FontFamily(Font(R.font.atlassian_mono_regular, FontWeight.Normal))

private const val TNUM = "tnum"

object JiraText {
    val ScreenTitle = TextStyle(AtlassianSans, fontWeight = FontWeight.Bold,     fontSize = 30.sp, lineHeight = 36.sp, letterSpacing = (-0.4).sp)
    val BoardTitle  = TextStyle(AtlassianSans, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val IssueTitle  = TextStyle(AtlassianSans, fontWeight = FontWeight.SemiBold, fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Subsection  = TextStyle(AtlassianSans, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 22.sp)
    val Body        = TextStyle(AtlassianSans, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val CardSummary = TextStyle(AtlassianSans, fontWeight = FontWeight.Medium,   fontSize = 15.sp, lineHeight = 21.sp)
    val Meta        = TextStyle(AtlassianSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 19.sp)
    val FieldLabel  = TextStyle(AtlassianSans, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.4.sp)
    val ColumnHead  = TextStyle(AtlassianSans, fontWeight = FontWeight.Bold,     fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.6.sp)
    val Lozenge     = TextStyle(AtlassianSans, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 12.sp, letterSpacing = 0.4.sp)
    val IssueKey    = TextStyle(AtlassianSans, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp, fontFeatureSettings = TNUM)
    val Tab         = TextStyle(AtlassianSans, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Code        = TextStyle(AtlassianMono, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 20.sp)
}

val JiraTypography = Typography(
    headlineLarge = JiraText.ScreenTitle,
    headlineMedium = JiraText.BoardTitle,
    titleMedium   = JiraText.Subsection,
    bodyMedium    = JiraText.Body,
    labelSmall    = JiraText.Tab,
)
```

## 3. Signature Components

### Status Lozenge

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

enum class JiraStatus(val bg: Color, val tx: Color, val label: String) {
    ToDo(JiraColors.NeutralBg, JiraColors.NeutralTx, "TO DO"),
    InProgress(JiraColors.InfoBg, JiraColors.InfoTx, "IN PROGRESS"),
    Done(JiraColors.SuccessBg, JiraColors.SuccessTx, "DONE"),
    Blocked(JiraColors.DangerBg, JiraColors.DangerTx, "BLOCKED"),
    Epic(JiraColors.EpicBg, JiraColors.EpicTx, "EPIC"),
}

@Composable
fun StatusLozenge(status: JiraStatus, modifier: Modifier = Modifier) {
    Box(
        modifier
            .clip(RoundedCornerShape(3.dp))
            .background(status.bg)
            .padding(horizontal = 8.dp, vertical = 4.dp)
    ) {
        Text(status.label, style = JiraText.Lozenge, color = status.tx)
    }
}
```

### Issue-Type Icon

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.ui.graphics.vector.ImageVector

enum class IssueType(val color: Color, val icon: ImageVector) {
    Story(JiraColors.StoryGreen, Icons.Filled.Bookmark),
    Bug(JiraColors.BugRed, Icons.Filled.BugReport),
    Task(JiraColors.TaskBlue, Icons.Filled.Check),
    Epic(JiraColors.EpicPurple, Icons.Filled.Bolt),
    Subtask(JiraColors.SubtaskGray, Icons.Filled.SubdirectoryArrowRight),
}

@Composable
fun IssueTypeIcon(type: IssueType, size: Int = 16) {
    Box(
        Modifier.size(size.dp).clip(RoundedCornerShape(3.dp)).background(type.color),
        contentAlignment = Alignment.Center,
    ) {
        Icon(type.icon, contentDescription = type.name, tint = Color.White, modifier = Modifier.size((size * 0.62f).dp))
    }
}
```

### Issue Card

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.FlowRow
import androidx.compose.foundation.shape.CircleShape

data class CardLabel(val text: String, val bg: Color, val fg: Color)

@Composable
fun IssueCard(
    type: IssueType,
    issueKey: String,
    summary: String,
    labels: List<CardLabel> = emptyList(),
    points: Int,
    initials: String,
    avatarColor: Color,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(8.dp))
            .background(MaterialTheme.colorScheme.surface)
            .border(1.dp, JiraColors.Border, RoundedCornerShape(8.dp))
            .padding(12.dp)
    ) {
        Text(summary, style = JiraText.CardSummary, color = MaterialTheme.colorScheme.onSurface, maxLines = 2)

        if (labels.isNotEmpty()) {
            FlowRow(Modifier.padding(top = 10.dp), horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                labels.forEach { l ->
                    Box(Modifier.clip(RoundedCornerShape(3.dp)).background(l.bg).padding(horizontal = 7.dp, vertical = 2.dp)) {
                        Text(l.text.uppercase(), style = JiraText.Lozenge.copy(fontSize = 10.sp), color = l.fg)
                    }
                }
            }
        }

        Row(
            Modifier.fillMaxWidth().padding(top = 12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                IssueTypeIcon(type)
                Text(issueKey, style = JiraText.IssueKey, color = JiraColors.TextSubtlest)
            }
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                Box(
                    Modifier.defaultMinSize(minWidth = 18.dp, minHeight = 18.dp)
                        .clip(CircleShape).background(Color(0xFFDDE1E6)).padding(horizontal = 5.dp),
                    contentAlignment = Alignment.Center,
                ) { Text("$points", style = JiraText.IssueKey, color = JiraColors.TextSubtle) }
                Box(Modifier.size(22.dp).clip(CircleShape).background(avatarColor), contentAlignment = Alignment.Center) {
                    Text(initials, style = JiraText.Lozenge, color = Color.White)
                }
            }
        }
    }
}
```

### Board Column

```kotlin
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items

@Composable
fun BoardColumn(name: String, count: Int, cards: List<IssueCardModel>, modifier: Modifier = Modifier) {
    Column(modifier.width(248.dp)) {
        Row(
            Modifier.padding(bottom = 10.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Text(name.uppercase(), style = JiraText.ColumnHead, color = JiraColors.TextSubtlest)
            Box(Modifier.clip(CircleShape).background(JiraColors.SurfaceHover).padding(horizontal = 7.dp, vertical = 1.dp)) {
                Text("$count", style = JiraText.IssueKey, color = JiraColors.TextSubtlest)
            }
        }
        LazyColumn(verticalArrangement = Arrangement.spacedBy(8.dp)) {
            items(cards) { c -> IssueCard(c.type, c.key, c.summary, c.labels, c.points, c.initials, c.avatarColor) }
        }
    }
}
```

### Field Row (issue detail)

```kotlin
import androidx.compose.material3.HorizontalDivider

@Composable
fun FieldRow(label: String, value: @Composable () -> Unit) {
    Column {
        Row(
            Modifier.fillMaxWidth().defaultMinSize(minHeight = 44.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text(label.uppercase(), style = JiraText.FieldLabel, color = JiraColors.TextSubtle, modifier = Modifier.width(110.dp))
            Box(Modifier.weight(1f)) { value() }
        }
        HorizontalDivider(color = JiraColors.Divider, thickness = 1.dp)
    }
}
```

## 4. Navigation

Jira has minimal chrome: a 5-tab bottom strip and a top breadcrumb/filter row. On Android, the strip is a `NavigationBar`; the status-transition picker is a `DropdownMenu`; the board is a horizontally scrolling `LazyRow` of `LazyColumn`s. No tint pill — active is Jira Blue.

```kotlin
@Composable
fun JiraBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = MaterialTheme.colorScheme.surface, tonalElevation = 0.dp) {
        val items = listOf(
            "Boards"        to Icons.Filled.GridView,
            "Backlog"       to Icons.Filled.FormatListBulleted,
            "Search"        to Icons.Filled.Search,
            "Notifications" to Icons.Filled.Notifications,
            "You"           to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = JiraText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = JiraColors.Blue,
                    selectedTextColor = JiraColors.Blue,
                    unselectedIconColor = JiraColors.TextSubtlest,
                    unselectedTextColor = JiraColors.TextSubtlest,
                    indicatorColor = Color.Transparent, // no Material pill — Jira has none
                ),
            )
        }
    }
}
```

The board lives in a `LazyRow` of `BoardColumn`s on a `Sunken`/`DarkCanvas` background. For drag-to-transition, use `Modifier.pointerInput { detectDragGesturesAfterLongPress { ... } }` lifting the dragged card (`graphicsLayer { scaleX = 1.02f; rotationZ = 2f }`), shifting neighbors via `animateItemPlacement(tween(200))`, and on drop firing a medium haptic + status update.

## 5. Motion

Jira motion is functional Atlassian ease-out — 150–300ms, never decorative. Shadows are navy-tinted on light; elevation on dark is a surface step.

| Moment | Compose recipe |
|--------|----------------|
| Card drag | long-press → `graphicsLayer { scaleX/Y = 1.02f; rotationZ = 2f }`; neighbors `animateItemPlacement(tween(200))`; drop snap `tween(150)` + medium haptic |
| Status dropdown | `DropdownMenu` default fade; on select, lozenge color `animateColorAsState(tween(200))` |
| Screen push | Nav3/`NavHost` slide push `tween(300)` |
| Section collapse (backlog) | chevron `animateFloatAsState` 0→90° `tween(150)`; rows `expandVertically(tween(200))` + `fadeIn` |
| Sprint progress segments | each segment width `animateDpAsState(tween(400))` on load |
| Filter pill toggle | instant tint + `tween(100)` scale 0.97 press feedback |
| Pull-to-refresh | `PullToRefreshContainer`, 250ms settle |

```kotlin
// Status lozenge color cross-fade on transition — the canonical Jira motion
val bg by animateColorAsState(status.bg, tween(200), label = "lozengeBg")
val tx by animateColorAsState(status.tx, tween(200), label = "lozengeTx")
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the medium impact on card drop and status change; `HapticFeedbackConstants.CLOCK_TICK` for the light filter-toggle / section-collapse tick. Auto-save is silent — only show a `Snackbar` on explicit save or error.

## 6. Icons

Jira's issue-type glyphs map to filled Material icons inside a rounded color container. Add `androidx.compose.material:material-icons-extended` for the full set.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Boards (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Backlog (tab) | `list.bullet.indent` | `Icons.Filled.FormatListBulleted` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Notifications (tab) | `bell` | `Icons.Filled.Notifications` |
| You (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Story type | `bookmark.fill` | `Icons.Filled.Bookmark` |
| Bug type | `ladybug.fill` | `Icons.Filled.BugReport` |
| Task type | `checkmark` | `Icons.Filled.Check` |
| Epic type | `bolt.fill` | `Icons.Filled.Bolt` |
| Sub-task type | `arrow.turn.down.right` | `Icons.Filled.SubdirectoryArrowRight` |
| Status dropdown | `chevron.down` | `Icons.Filled.ArrowDropDown` |
| Priority Highest | `chevron.up.2` | `Icons.Filled.KeyboardDoubleArrowUp` |
| Priority Medium | `equal` | `Icons.Filled.DragHandle` |
| Filter | `line.3.horizontal.decrease.circle` | `Icons.Filled.FilterList` |
| Overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Watch | `eye` | `Icons.Filled.Visibility` |
| Add / create | `plus` | `Icons.Filled.Add` |
| Flag | `flag.fill` | `Icons.Filled.Flag` |
| Comment | `bubble.left` | `Icons.Filled.ChatBubbleOutline` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; `DropdownMenu`, drag, and modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; light canvas wants dark-content system bars (light-content in dark mode). The top breadcrumb respects the camera cutout; the comment composer / inline edit use `Modifier.imePadding()`.
- **Font scaling**: `sp` honors the user scale — keep it on titles, body, description, comments, and code (stays monospace). Pin layout-sensitive text (status lozenges, label chips, issue keys, 12sp column headers, 10sp tab labels) so the fixed-width board columns don't break — derive from `dp` or wrap in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Tabular figures**: keep `fontFeatureSettings = "tnum"` on issue keys, points, and counts so columns and totals align.
- **TalkBack**: label issue cards "{type}, {key}, {summary}, {points} points, assigned to {name}"; the status lozenge dropdown "Status: {value}, dropdown"; expose card move via `Modifier.semantics { customActions = listOf(CustomAccessibilityAction("Move to column") { … }) }` so drag isn't required.
- **Touch targets**: Material guidance is 48.dp — give the lozenge dropdown, overflow, and icon buttons a 48.dp hit area; issue cards are full-card tappable (≥64.dp tall); field rows full-row 44.dp+.
- **Contrast**: `#172B4D` on `#FFFFFF` and `#C7D1DB` on `#1D2125` pass WCAG AA; every semantic lozenge pair (e.g. `#216E4E` on `#DCFFF1`) is AA-matched — validate any custom pair. Never convey status by color alone: the lozenge text label and the distinct issue-type icon shapes (square / circle / check / lightning) carry it.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the drag tilt and section slide — substitute `Crossfade`; keep the lozenge color change instant.
- **Dark mode**: invert via the `Dark*` palette — `#1D2125`, NOT true black; `#172B4D` text → `#C7D1DB`; interactive blue → `#1868DB`. Shadows are nearly invisible on dark, so signal elevation via the surface step (`#22272B`→`#2C333A`→`#38414A`) plus a 1.dp `DarkDivider` border on cards, dropdowns, and sheets. Do **not** enable `dynamicColorScheme()` — Jira's brand blue and semantic palette must hold regardless of wallpaper.
