# Obsidian (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Obsidian's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Obsidian's charcoal workshop, single-purple connective accent, the graph view, backlinks pane, dual sans/mono type) while making everything idiomatic Android — a `ModalNavigationDrawer` instead of a slide-over pane, a Compose `Canvas` graph, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and `androidx.compose.material:material-icons-extended`.

## 1. Color Tokens

```kotlin
// ui/theme/ObsidianColors.kt
import androidx.compose.ui.graphics.Color

object ObsidianColors {
    // Canvas & Surfaces
    val Canvas   = Color(0xFF1E1E1E)
    val Surface1 = Color(0xFF262626)
    val Surface2 = Color(0xFF2D2D2D)
    val Surface3 = Color(0xFF363636)
    val Divider  = Color(0xFF363636)

    // Text
    val TextPrimary   = Color(0xFFDCDDDE)
    val TextSecondary = Color(0xFF999999)
    val TextTertiary  = Color(0xFF6B6B6B)

    // Brand
    val Purple        = Color(0xFF7C3AED)
    val PurpleLink    = Color(0xFFA78BFA)
    val PurplePressed = Color(0xFF6D28D9)
    val PurpleTint    = Color(0x247C3AED) // ~14% alpha

    // Editor & Semantic
    val ExternalLink = Color(0xFF7C9CBF)
    val Success      = Color(0xFF4ADE80)
    val Error        = Color(0xFFF87171)
    val Highlight    = Color(0x40FFD54F) // ~25% alpha
}
```

Wire it into a Material 3 `darkColorScheme` so ripples and stock components inherit the brand. Obsidian is dark-first; a light theme is optional.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val ObsidianScheme = darkColorScheme(
    primary        = ObsidianColors.Purple,
    onPrimary      = Color.White,
    background      = ObsidianColors.Canvas,
    onBackground    = ObsidianColors.TextPrimary,
    surface         = ObsidianColors.Surface1,
    onSurface       = ObsidianColors.TextPrimary,
    surfaceVariant  = ObsidianColors.Surface2,
    outline         = ObsidianColors.Divider,
    error           = ObsidianColors.Error,
)

@Composable
fun ObsidianTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = ObsidianScheme, typography = ObsidianTypography, content = content)
```

## 2. Typography

Obsidian uses a dual system: Inter for chrome/reading, JetBrains Mono for source/commands. Drop both in `res/font/` (lowercase, snake_case).

```kotlin
// ui/theme/ObsidianType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)
val JBMono = FontFamily(
    Font(R.font.jetbrains_mono_regular,  FontWeight.Normal),   // 400
    Font(R.font.jetbrains_mono_semibold, FontWeight.SemiBold),  // 600
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object ObsidianText {
    val NoteTitle    = TextStyle(Inter,  fontWeight = FontWeight.Bold,     fontSize = 26.sp, lineHeight = 33.sp, letterSpacing = (-0.3).sp)
    val H1           = TextStyle(Inter,  fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 31.sp, letterSpacing = (-0.2).sp)
    val H2           = TextStyle(Inter,  fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val H3           = TextStyle(Inter,  fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val Reading      = TextStyle(Inter,  fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 26.sp)
    val FileRow      = TextStyle(Inter,  fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val PaneTitle    = TextStyle(Inter,  fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Backlink     = TextStyle(Inter,  fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 19.sp)
    val Metadata     = TextStyle(Inter,  fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val TagPill      = TextStyle(Inter,  fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp)
    val Button       = TextStyle(Inter,  fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 15.sp, letterSpacing = (-0.1).sp)
    val NodeLabel    = TextStyle(Inter,  fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp)
    val Source       = TextStyle(JBMono, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 23.sp)
    val CommandRow   = TextStyle(JBMono, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val CommandShort = TextStyle(JBMono, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 12.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val ObsidianTypography = Typography(
    headlineLarge = ObsidianText.NoteTitle,
    headlineMedium = ObsidianText.H1,
    headlineSmall = ObsidianText.H2,
    titleMedium   = ObsidianText.H3,
    bodyMedium    = ObsidianText.Reading,
    labelSmall    = ObsidianText.PaneTitle,
)
```

## 3. Signature Components

### File-Tree Row (the navigation unit)

```kotlin
@Composable
fun FileTreeRow(
    name: String,
    depth: Int,
    isFolder: Boolean = false,
    expanded: Boolean = false,
    isActive: Boolean = false,
    isModified: Boolean = false,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val rot by animateFloatAsState(if (expanded) 90f else 0f, tween(180), label = "chev")
    val bg = when {
        isActive -> ObsidianColors.PurpleTint
        pressed  -> ObsidianColors.Surface2
        else     -> Color.Transparent
    }

    Box {
        Row(
            modifier = modifier
                .fillMaxWidth()
                .height(32.dp)
                .background(bg)
                .clickable(interaction, indication = null, onClick = onClick)
                .padding(horizontal = 12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Spacer(Modifier.width((depth * 16).dp))
            if (isFolder) {
                Icon(Icons.Filled.ChevronRight, null, tint = ObsidianColors.TextSecondary,
                    modifier = Modifier.size(10.dp).graphicsLayer { rotationZ = rot })
            } else Spacer(Modifier.width(10.dp))
            Icon(
                if (isFolder) Icons.Filled.Folder else Icons.Filled.Description,
                null,
                tint = if (isActive) ObsidianColors.PurpleLink else ObsidianColors.TextSecondary,
                modifier = Modifier.size(14.dp),
            )
            Text(name, style = ObsidianText.FileRow, color = ObsidianColors.TextPrimary,
                maxLines = 1, overflow = TextOverflow.Ellipsis, modifier = Modifier.weight(1f))
            if (isModified) Box(Modifier.size(6.dp).clip(CircleShape).background(ObsidianColors.PurpleLink))
        }
        if (isActive) Box(Modifier.align(Alignment.CenterStart).width(2.dp).fillMaxHeight().background(ObsidianColors.Purple))
    }
}
```

### Markdown Source Line (dimmed syntax)

```kotlin
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.withStyle
import androidx.compose.ui.text.style.TextDecoration

enum class TokKind { Syntax, Wikilink, External, Code, Text }
data class Tok(val kind: TokKind, val text: String)

@Composable
fun SourceLine(tokens: List<Tok>) {
    val text = buildAnnotatedString {
        tokens.forEach { t ->
            val color = when (t.kind) {
                TokKind.Syntax   -> ObsidianColors.TextTertiary
                TokKind.Wikilink -> ObsidianColors.PurpleLink
                TokKind.External -> ObsidianColors.ExternalLink
                else             -> ObsidianColors.TextPrimary
            }
            withStyle(SpanStyle(color = color,
                textDecoration = if (t.kind == TokKind.Wikilink) TextDecoration.Underline else null)) {
                append(t.text)
            }
        }
    }
    Text(text, style = ObsidianText.Source)
}
```

### Tag Pill (a connection, not metadata)

```kotlin
@Composable
fun TagPill(tag: String, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        Modifier
            .clip(CircleShape)
            .background(if (pressed) Color(0x3D7C3AED) else ObsidianColors.PurpleTint)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 9.dp, vertical = 3.dp),
    ) {
        Text("#$tag", style = ObsidianText.TagPill, color = ObsidianColors.PurpleLink)
    }
}
```

### Graph View (physics-driven nodes)

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset

data class GNode(val id: String, var x: Float, var y: Float, val r: Float, val active: Boolean = false)
data class GEdge(val a: String, val b: String)

@Composable
fun GraphView(nodesState: List<GNode>, edges: List<GEdge>, modifier: Modifier = Modifier) {
    // Drive `nodesState` from a force-sim coroutine (edge springs + repulsion + centering),
    // recomposing ~60fps; here we just render the current frame.
    Canvas(modifier.fillMaxSize().background(ObsidianColors.Canvas)) {
        fun at(id: String) = nodesState.first { it.id == id }
        edges.forEach { e ->
            val a = at(e.a); val b = at(e.b)
            val bright = a.active || b.active
            drawLine(
                if (bright) ObsidianColors.Purple else ObsidianColors.Divider,
                Offset(a.x, a.y), Offset(b.x, b.y), strokeWidth = 1f,
            )
        }
        nodesState.forEach { n ->
            if (n.active) drawCircle(ObsidianColors.PurpleTint, n.r + 8f, Offset(n.x, n.y))
            drawCircle(if (n.active) ObsidianColors.PurpleLink else ObsidianColors.Purple, n.r, Offset(n.x, n.y))
        }
    }
}
```

### Command Palette

```kotlin
@Composable
fun CommandPalette(rows: List<Pair<String, String>>) {
    var query by remember { mutableStateOf("") }
    val focused = 0

    Column(
        Modifier
            .widthIn(max = 560.dp)
            .fillMaxWidth()
            .clip(RoundedCornerShape(10.dp))
            .background(ObsidianColors.Surface1)
            .border(1.dp, ObsidianColors.Divider, RoundedCornerShape(10.dp)),
    ) {
        Row(
            Modifier.height(44.dp).padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(10.dp),
        ) {
            Text(">", style = ObsidianText.CommandRow, color = ObsidianColors.TextTertiary)
            BasicTextField(
                value = query, onValueChange = { query = it },
                singleLine = true,
                textStyle = ObsidianText.CommandRow.copy(color = ObsidianColors.TextPrimary),
                cursorBrush = SolidColor(ObsidianColors.PurpleLink),
                modifier = Modifier.weight(1f),
                decorationBox = { inner ->
                    if (query.isEmpty()) Text("Type a command…",
                        style = ObsidianText.CommandRow, color = ObsidianColors.TextTertiary)
                    inner()
                },
            )
        }
        HorizontalDivider(color = ObsidianColors.Divider)
        LazyColumn {
            itemsIndexed(rows) { i, (label, shortcut) ->
                Row(
                    Modifier
                        .fillMaxWidth()
                        .height(38.dp)
                        .background(if (i == focused) ObsidianColors.PurpleTint else Color.Transparent)
                        .padding(horizontal = 16.dp),
                    verticalAlignment = Alignment.CenterVertically,
                ) {
                    Text(label, style = ObsidianText.CommandRow, color = ObsidianColors.TextPrimary, modifier = Modifier.weight(1f))
                    Text(shortcut, style = ObsidianText.CommandShort, color = ObsidianColors.TextSecondary)
                }
            }
        }
    }
}
```

### Backlinks Pane

```kotlin
@Composable
fun BacklinksPane(backlinks: List<Pair<String, String>>) {
    Column(Modifier.background(ObsidianColors.Surface1)) {
        Row(
            Modifier.padding(horizontal = 16.dp, vertical = 12.dp),
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Text("LINKED MENTIONS", style = ObsidianText.PaneTitle, color = ObsidianColors.TextSecondary)
            Text("${backlinks.size}", style = ObsidianText.Metadata, color = ObsidianColors.TextSecondary)
        }
        backlinks.forEach { (title, ctx) ->
            Column(Modifier.padding(horizontal = 16.dp, vertical = 10.dp), verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(title, style = ObsidianText.Button, color = ObsidianColors.TextPrimary)
                Text(ctx, style = ObsidianText.Backlink, color = ObsidianColors.TextSecondary, maxLines = 2, overflow = TextOverflow.Ellipsis)
            }
        }
    }
}
```

### Primary Button

```kotlin
@Composable
fun ObsidianPrimaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f,
        spring(dampingRatio = 0.85f, stiffness = 600f), label = "btnScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .scale(scale)
            .height(34.dp)
            .clip(RoundedCornerShape(6.dp))
            .background(if (pressed) ObsidianColors.PurplePressed else ObsidianColors.Purple)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            }
            .padding(horizontal = 16.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = ObsidianText.Button, color = Color.White)
    }
}
```

## 4. Navigation (no bottom bar) — Ribbon + File-Tree Drawer

Obsidian's iOS app is a left ribbon plus a slide-over file-tree pane with **no bottom navigation**. Use a fixed `Row` ribbon and a Material 3 `ModalNavigationDrawer` for the tree.

```kotlin
@Composable
fun ObsidianWorkspace() {
    val drawerState = rememberDrawerState(DrawerValue.Closed)
    Row(Modifier.fillMaxSize().background(ObsidianColors.Canvas)) {
        // Ribbon
        Column(
            Modifier.width(40.dp).fillMaxHeight().background(ObsidianColors.Surface1).padding(vertical = 12.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(4.dp),
        ) {
            listOf(Icons.Filled.BubbleChart, Icons.Filled.Terminal, Icons.Filled.Search, Icons.Filled.EditNote).forEach {
                Icon(it, null, tint = ObsidianColors.TextSecondary,
                    modifier = Modifier.size(40.dp).padding(10.dp))
            }
            Spacer(Modifier.weight(1f))
            Icon(Icons.Filled.Settings, null, tint = ObsidianColors.TextSecondary,
                modifier = Modifier.size(40.dp).padding(10.dp))
        }
        ModalNavigationDrawer(
            drawerState = drawerState,
            scrimColor = Color.Black.copy(alpha = 0.5f),
            drawerContent = {
                ModalDrawerSheet(
                    drawerContainerColor = ObsidianColors.Surface1,
                    modifier = Modifier.width(280.dp),
                ) {
                    // FileTreeRow(...) entries — the vault explorer
                }
            },
        ) {
            // Editor + (backlinks / graph) content
        }
    }
}
```

## 5. Motion

| Moment | Compose recipe |
|--------|----------------|
| Command palette open | `AnimatedVisibility` `fadeIn(tween(130))` + `scaleIn(initialScale = 0.97f, tween(130))` |
| File-tree folder toggle | `AnimatedVisibility` `expandVertically`/`shrinkVertically` `tween(180)` + chevron `rotationZ` |
| Node focus | `animateColorAsState` brighten + halo `tween(250)` (driven from sim state) |
| Reading/source toggle | `Crossfade(targetState = readingMode, animationSpec = tween(200))` |
| Pane slide-over | `ModalNavigationDrawer` default spring, `0.5` scrim |
| Graph physics | a `LaunchedEffect` fixed-timestep loop integrating velocities ~60fps, settling over ~600ms |

```kotlin
// Graph force loop sketch
LaunchedEffect(graphId) {
    while (isActive) {
        withFrameNanos { /* repulsion + edge springs + centering → update node offsets */ }
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For a crisper tick matching iOS `.light` impact use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) on note open, command run, and tag-pill tap.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| File (tree) | `doc.text` | `Icons.Filled.Description` |
| Folder (tree) | `folder.fill` | `Icons.Filled.Folder` |
| Folder chevron | `chevron.right` | `Icons.Filled.ChevronRight` |
| Graph (ribbon) | `circle.hexagongrid` | `Icons.Filled.BubbleChart` |
| Command palette (ribbon) | `command` | `Icons.Filled.Terminal` |
| Search (ribbon) | `magnifyingglass` | `Icons.Filled.Search` |
| New note (ribbon) | `square.and.pencil` | `Icons.Filled.EditNote` |
| Settings (ribbon) | `gearshape` | `Icons.Filled.Settings` |
| Reading/source toggle | `book` / `chevron.left.forwardslash.chevron.right` | `Icons.Filled.MenuBook` / `Icons.Filled.Code` |
| Backlink | `arrow.uturn.backward` | `Icons.AutoMirrored.Filled.Undo` |
| Tag | `number` | `Icons.Filled.Tag` |
| Modified dot | `circle.fill` | `Icons.Filled.Circle` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the charcoal canvas wants light-content system bars. Apply `Scaffold` / `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the editor + keyboard accessory clear the gesture nav.
- **Two type systems**: keep Inter for chrome/reading and JetBrains Mono for source/commands — verify both render with the bundled fonts.
- **Font scaling**: `sp` honors the user scale — keep it on reading body / titles / file rows; keep a 13sp floor on the mono source to preserve the grid; pin graph node labels and 12sp command shortcuts (derive from `dp` or wrap in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`).
- **TalkBack**: the `Canvas` graph is opaque to screen readers — provide a parallel accessible list of nodes ("Note Name, 5 links"); the active node announces "current note"; give wikilinks a link role + label ("Project Plan, internal link"); never rely on the purple color alone.
- **Touch targets**: Material guidance is 48.dp minimum. The 32.dp file-tree row and 20.dp ribbon glyphs must get a 48.dp hit area via padding/hit-slop.
- **Contrast**: `#999999` on `#1E1E1E` passes WCAG AA at 13sp+. `#6B6B6B` syntax markers are intentionally low-contrast — fine for decoration, never for essential text.
- **Reduced motion**: honor the system setting — freeze the graph physics into a static layout and drop the command-palette scale, keeping only the opacity fade.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Obsidian's identity requires the fixed charcoal canvas and single-purple connective accent regardless of wallpaper.
