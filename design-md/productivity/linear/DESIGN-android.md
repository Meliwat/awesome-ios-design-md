# Linear (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Linear's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Linear's near-OLED canvas, single-purple accent, dense issue rows, the Cmd+K command menu) while making everything idiomatic Android — a `ModalNavigationDrawer` instead of a slide-over sidebar, `MutableInteractionSource` press states, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and `androidx.compose.material:material-icons-extended`.

## 1. Color Tokens

```kotlin
// ui/theme/LinearColors.kt
import androidx.compose.ui.graphics.Color

object LinearColors {
    // Canvas & Surfaces
    val Canvas   = Color(0xFF08090A)
    val Surface1 = Color(0xFF141516)
    val Surface2 = Color(0xFF1C1D1F)
    val Surface3 = Color(0xFF232428)
    val Divider  = Color(0xFF23252A)

    // Text
    val TextPrimary   = Color(0xFFF7F8F8)
    val TextSecondary = Color(0xFF8A8F98)
    val TextTertiary  = Color(0xFF5C5F6A)

    // Brand
    val Purple        = Color(0xFF5E6AD2)
    val PurplePressed = Color(0xFF4F58B8)
    val PurpleTint    = Color(0x245E6AD2) // ~14% alpha

    // Status & Semantic
    val Progress = Color(0xFFF2C94C)
    val Success  = Color(0xFF4CB782)
    val Error    = Color(0xFFEB5757)
}
```

Wire it into a Material 3 `darkColorScheme` so ripples, dividers, and default component colors inherit the brand. Linear is dark-first; a light scheme is optional and opt-in.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val LinearScheme = darkColorScheme(
    primary        = LinearColors.Purple,
    onPrimary      = Color.White,
    background     = LinearColors.Canvas,
    onBackground   = LinearColors.TextPrimary,
    surface        = LinearColors.Surface1,
    onSurface      = LinearColors.TextPrimary,
    surfaceVariant = LinearColors.Surface2,
    outline        = LinearColors.Divider,
    error          = LinearColors.Error,
)

@Composable
fun LinearTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = LinearScheme, typography = LinearTypography, content = content)
```

## 2. Typography

Linear uses Inter. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Identifiers and shortcuts use a monospace family. Fall back to the system font (Roboto).

```kotlin
// ui/theme/LinearType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextGeometricTransform
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_medium,   FontWeight.Medium),   // 500
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
)
val LinearMono = FontFamily.Monospace

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object LinearText {
    val TitleLarge   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.4).sp)
    val ViewTitle    = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val Section      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.2).sp)
    val IssueTitle   = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val Body         = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 23.sp)
    val Metadata     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val LabelPill    = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 12.sp, lineHeight = 12.sp)
    val CommandRow   = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 18.sp, letterSpacing = (-0.1).sp)
    val Button       = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp, letterSpacing = (-0.1).sp)
    val Tiny         = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
    val MonoId       = TextStyle(LinearMono, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 17.sp)
    val MonoShortcut = TextStyle(LinearMono, fontWeight = FontWeight.Medium, fontSize = 12.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val LinearTypography = Typography(
    headlineLarge = LinearText.TitleLarge,
    headlineSmall = LinearText.ViewTitle,
    titleMedium   = LinearText.Section,
    bodyMedium    = LinearText.Body,
    labelSmall    = LinearText.Tiny,
)
```

## 3. Signature Components

### Status Glyph

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.PathEffect
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.Dp

enum class IssueStatus { Backlog, Todo, InProgress, InReview, Done, Canceled }

@Composable
fun StatusGlyph(status: IssueStatus, size: Dp = 16.dp) {
    Canvas(Modifier.size(size)) {
        val s = size.toPx()
        val r = s / 2 - 1.5f
        when (status) {
            IssueStatus.Backlog, IssueStatus.Todo -> drawCircle(
                color = LinearColors.TextSecondary, radius = r, style = Stroke(
                    width = 1.5f,
                    pathEffect = if (status == IssueStatus.Backlog)
                        PathEffect.dashPathEffect(floatArrayOf(2f, 2f)) else null,
                ),
            )
            IssueStatus.InProgress -> {
                drawCircle(LinearColors.Progress, r, style = Stroke(1.5f))
                drawArc(LinearColors.Progress, -90f, 180f, true,
                    topLeft = Offset(s / 2 - r, s / 2 - r),
                    size = androidx.compose.ui.geometry.Size(r * 2, r * 2))
            }
            IssueStatus.InReview -> {
                drawCircle(LinearColors.Purple, r, style = Stroke(1.5f))
                drawArc(LinearColors.Purple, -90f, 240f, false,
                    topLeft = Offset(s / 2 - r, s / 2 - r),
                    size = androidx.compose.ui.geometry.Size(r * 2, r * 2),
                    style = Stroke(1.5f))
            }
            IssueStatus.Done -> {
                drawCircle(LinearColors.Purple, s / 2)
                drawLine(Color.White, Offset(s * 0.3f, s * 0.52f), Offset(s * 0.44f, s * 0.66f), 1.8f, StrokeCap.Round)
                drawLine(Color.White, Offset(s * 0.44f, s * 0.66f), Offset(s * 0.7f, s * 0.36f), 1.8f, StrokeCap.Round)
            }
            IssueStatus.Canceled -> {
                drawCircle(LinearColors.TextTertiary, s / 2)
                drawLine(Color.White, Offset(s * 0.34f, s * 0.34f), Offset(s * 0.66f, s * 0.66f), 1.8f, StrokeCap.Round)
                drawLine(Color.White, Offset(s * 0.66f, s * 0.34f), Offset(s * 0.34f, s * 0.66f), 1.8f, StrokeCap.Round)
            }
        }
    }
}
```

### Issue Row (the signature element)

```kotlin
@Composable
fun IssueRow(
    identifier: String,
    title: String,
    status: IssueStatus,
    priority: Int,
    assignee: String,
    selected: Boolean = false,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val bg = when {
        selected -> LinearColors.PurpleTint
        pressed  -> LinearColors.Surface2
        else     -> Color.Transparent
    }

    Box {
        Row(
            modifier = modifier
                .fillMaxWidth()
                .height(44.dp)
                .background(bg)
                .clickable(interaction, indication = null, onClick = onClick)
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(10.dp),
        ) {
            StatusGlyph(status)
            Text(identifier, style = LinearText.MonoId, color = LinearColors.TextSecondary)
            Text(
                title, style = LinearText.IssueTitle, color = LinearColors.TextPrimary,
                maxLines = 1, overflow = TextOverflow.Ellipsis, modifier = Modifier.weight(1f),
            )
            PriorityBars(priority)
            Box(
                Modifier.size(20.dp).clip(CircleShape).background(LinearColors.Surface3),
                contentAlignment = Alignment.Center,
            ) {
                Text(assignee.take(1), fontSize = 10.sp, fontWeight = FontWeight.SemiBold, color = LinearColors.TextSecondary)
            }
        }
        if (selected) {
            Box(Modifier.align(Alignment.CenterStart).width(2.dp).fillMaxHeight().background(LinearColors.Purple))
        }
    }
}

@Composable
fun PriorityBars(level: Int) {
    if (level == 4) {
        Box(
            Modifier.size(14.dp).clip(RoundedCornerShape(2.dp)).background(LinearColors.Progress),
            contentAlignment = Alignment.Center,
        ) { Text("!", fontSize = 10.sp, fontWeight = FontWeight.Bold, color = Color.Black) }
    } else {
        Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(2.dp),
            modifier = Modifier.size(14.dp)) {
            repeat(3) { i ->
                Box(
                    Modifier
                        .width(3.dp)
                        .height((6 + i * 4).dp)
                        .clip(RoundedCornerShape(1.dp))
                        .background(if (i < level) LinearColors.TextSecondary else LinearColors.TextTertiary.copy(alpha = 0.4f)),
                )
            }
        }
    }
}
```

### Primary Button

```kotlin
@Composable
fun LinearPrimaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f,
        spring(dampingRatio = 0.85f, stiffness = 700f), label = "btnScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .scale(scale)
            .height(32.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) LinearColors.PurplePressed else LinearColors.Purple)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            }
            .padding(horizontal = 14.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = LinearText.Button, color = Color.White)
    }
}
```

### Command Menu (Cmd+K)

```kotlin
@Composable
fun CommandMenu(rows: List<Triple<ImageVector, String, String>>) {
    var query by remember { mutableStateOf("") }
    val focused = 0

    Column(
        Modifier
            .widthIn(max = 560.dp)
            .fillMaxWidth()
            .clip(RoundedCornerShape(12.dp))
            .background(LinearColors.Surface1)
            .border(1.dp, LinearColors.Divider, RoundedCornerShape(12.dp)),
    ) {
        Row(
            Modifier.height(44.dp).padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(10.dp),
        ) {
            Icon(Icons.Filled.Search, null, tint = LinearColors.TextSecondary, modifier = Modifier.size(16.dp))
            BasicTextField(
                value = query, onValueChange = { query = it },
                singleLine = true,
                textStyle = LinearText.CommandRow.copy(color = LinearColors.TextPrimary),
                cursorBrush = SolidColor(LinearColors.Purple),
                modifier = Modifier.weight(1f),
                decorationBox = { inner ->
                    if (query.isEmpty()) Text("Type a command or search…",
                        style = LinearText.CommandRow, color = LinearColors.TextTertiary)
                    inner()
                },
            )
        }
        HorizontalDivider(color = LinearColors.Divider)
        LazyColumn {
            itemsIndexed(rows) { i, (icon, label, shortcut) ->
                Row(
                    Modifier
                        .fillMaxWidth()
                        .height(40.dp)
                        .background(if (i == focused) LinearColors.PurpleTint else Color.Transparent)
                        .padding(horizontal = 16.dp),
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(10.dp),
                ) {
                    Icon(icon, null, tint = LinearColors.TextSecondary, modifier = Modifier.size(15.dp))
                    Text(label, style = LinearText.CommandRow, color = LinearColors.TextPrimary, modifier = Modifier.weight(1f))
                    Text(shortcut, style = LinearText.MonoShortcut, color = LinearColors.TextSecondary)
                }
            }
        }
    }
}
```

### Cycle Progress Bar

```kotlin
@Composable
fun CycleProgressBar(completed: Int, total: Int, daysLeft: Int) {
    Column(verticalArrangement = Arrangement.spacedBy(6.dp)) {
        Box(
            Modifier.fillMaxWidth().height(6.dp).clip(RoundedCornerShape(3.dp)).background(LinearColors.Surface2),
        ) {
            Box(
                Modifier
                    .fillMaxWidth(completed.toFloat() / total.coerceAtLeast(1))
                    .fillMaxHeight()
                    .clip(RoundedCornerShape(3.dp))
                    .background(LinearColors.Purple),
            )
        }
        Text("$completed of $total completed · $daysLeft days left",
            style = LinearText.Metadata, color = LinearColors.TextSecondary)
    }
}
```

## 4. The Command Menu as the App's Core

Linear has no tab bar and a minimal toolbar — the **command menu** is how power users move. On Android, trigger it from a toolbar action and from the hardware `Ctrl+K` (Android's `Cmd` analog) via `onKeyEvent`. Fuzzy-filter all actions; arrow keys move the purple focus row; Enter executes; Back dismisses. Render it as a `Dialog` with `usePlatformDefaultWidth = false` so it stays a fixed 560.dp centered sheet rather than full-bleed.

```kotlin
Dialog(onDismissRequest = onDismiss, properties = DialogProperties(usePlatformDefaultWidth = false)) {
    CommandMenu(rows)
}
```

## 5. Navigation (no bottom bar)

Linear's iOS app is a slide-over sidebar with **no bottom navigation**. Use Material 3 `ModalNavigationDrawer`. Android has no first-class live blur for the scrim; use a 50%-black scrim. **The accent is purple, used only for the active item, primary action, and selection.**

```kotlin
@Composable
fun LinearScaffold() {
    val drawerState = rememberDrawerState(DrawerValue.Closed)
    ModalNavigationDrawer(
        drawerState = drawerState,
        scrimColor = Color.Black.copy(alpha = 0.5f),
        drawerContent = {
            ModalDrawerSheet(
                drawerContainerColor = LinearColors.Canvas,
                modifier = Modifier.width(280.dp),
            ) {
                listOf(
                    Triple(Icons.Filled.Inbox, "Inbox", true),
                    Triple(Icons.Outlined.Circle, "My Issues", false),
                    Triple(Icons.Filled.Group, "Engineering", false),
                ).forEach { (icon, label, active) ->
                    NavigationDrawerItem(
                        icon = { Icon(icon, null, modifier = Modifier.size(16.dp)) },
                        label = { Text(label, style = LinearText.CommandRow) },
                        selected = active,
                        onClick = {},
                        colors = NavigationDrawerItemDefaults.colors(
                            selectedContainerColor = LinearColors.PurpleTint,
                            selectedIconColor = LinearColors.Purple,
                            selectedTextColor = LinearColors.TextPrimary,
                            unselectedIconColor = LinearColors.TextSecondary,
                            unselectedTextColor = LinearColors.TextSecondary,
                        ),
                        modifier = Modifier.height(32.dp),
                    )
                }
            }
        },
    ) {
        // Issue list content
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Command menu open | `AnimatedVisibility` with `fadeIn(tween(120))` + `scaleIn(initialScale = 0.96f, tween(120))` |
| Row selection | `animateColorAsState` background cross-fade `tween(90)`, no offset |
| Group collapse / expand | `animateContentSize(spring(dampingRatio = 0.85f, stiffness = Spring.StiffnessMedium))` |
| Status change | `Crossfade(targetState = status, animationSpec = tween(150))` on the glyph |
| Sidebar slide-over | `ModalNavigationDrawer`'s default spring (≈ `spring(stiffness = Medium)`) |

```kotlin
// Command menu enter/exit
AnimatedVisibility(
    visible = open,
    enter = fadeIn(tween(120)) + scaleIn(initialScale = 0.96f, animationSpec = tween(120)),
    exit  = fadeOut(tween(90)) + scaleOut(targetScale = 0.96f, animationSpec = tween(90)),
) { CommandMenu(rows) }
```

Haptics: prefer `LocalHapticFeedback`. For a crisper tick matching iOS `.light` impact, use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) on primary action commit, status change, and command execute.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Status glyphs are custom-drawn (see §3) — Material has no backlog/half-pie equivalents.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Todo status | `circle` | `Icons.Outlined.Circle` |
| Done status | `checkmark` (filled) | `Icons.Filled.CheckCircle` |
| Canceled | `xmark` (filled) | `Icons.Filled.Cancel` |
| Command search | `magnifyingglass` | `Icons.Filled.Search` |
| New issue | `square.and.pencil` | `Icons.Filled.Edit` |
| Filter | `line.3.horizontal.decrease` | `Icons.Filled.FilterList` |
| Display options | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Sidebar / menu | `sidebar.left` | `Icons.Filled.Menu` |
| Inbox | `tray.fill` | `Icons.Filled.Inbox` |
| My Issues | `circle.dashed` | `Icons.Outlined.Circle` |
| Team | `person.2.fill` | `Icons.Filled.Group` |
| Cycle | `arrow.triangle.2.circlepath` | `Icons.Filled.Sync` |
| Project | `cube` | `Icons.Filled.ViewInAr` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + drawer comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the near-black canvas wants light-content system bars. Apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` or `Scaffold` insets so the list/composer clear the gesture nav.
- **Tabular numerics**: identifiers and counts should not jitter — wrap mono `Text` in `Modifier.semantics {}` and prefer a fixed-width digit font; `FontFamily.Monospace` digits are already fixed-advance.
- **Font scaling**: `sp` honors the user scale — keep it on titles/body/metadata. Pin the 16.dp/14.dp status & priority glyphs and 12.sp mono shortcuts (derive from `dp` or wrap in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`).
- **TalkBack**: merge issue-row text with `Modifier.semantics(mergeDescendants = true)` and a combined description ("ENG-1423, Fix flaky auth redirect, In Progress, High priority, Sam"); mark the trailing avatar as decorative.
- **Touch targets**: Material guidance is 48.dp minimum. The 44.dp row is close; ensure the 16.dp status glyph and 18.dp toolbar icons get a 48.dp hit area via padding.
- **Contrast**: `#8A8F98` on `#08090A` passes WCAG AA at 13sp+. Validate the 11–12sp label/shortcut sizes and bump toward `#A0A4AD` if targeting strict accessibility compliance.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Linear's identity requires the fixed `#08090A` canvas and single-purple accent regardless of wallpaper.
