# Notion (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Notion's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the block editor + `/` command palette, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Notion's paper-white canvas, warm `#37352F` text, near-invisible chrome, the `/` palette as the creation gesture) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Popup` instead of a SwiftUI overlay, a `ModalDrawerSheet` instead of a swipe-in sidebar, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for cover images and mention avatars. No color extraction — Notion's palette is fixed pastels, so Palette is not needed. Notion is light-mode-first; a full dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/NotionColors.kt
import androidx.compose.ui.graphics.Color

object NotionColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF7F6F3)
    val SurfacePressed = Color(0xFFEFEEE9)
    val Divider        = Color(0xFFEAECEC)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF191919) // Notion's dark — NOT pure black
    val DarkSurface1 = Color(0xFF202020)
    val DarkSurface2 = Color(0xFF2F2F2F)
    val DarkDivider  = Color(0xFF373737)

    // Text
    val TextPrimary     = Color(0xFF37352F) // warm dark gray — NOT pure black
    val TextSecondary   = Color(0xFF9B9A97)
    val TextTertiary    = Color(0xFFC3C2BF)
    val DarkTextPrimary = Color(0xFFE6E6E4)

    // Brand (interactive)
    val Black    = Color(0xFF000000) // "New page" / primary CTA — no brand accent
    val LinkBlue = Color(0xFF2E75CC)
    val DarkLink = Color(0xFF529CCA)

    // Page background pastels (light) — also callout/highlight fills
    val BgGray   = Color(0xFFF1F1EF)
    val BgBrown  = Color(0xFFF4EEEE)
    val BgOrange = Color(0xFFFAEBDD)
    val BgYellow = Color(0xFFFBF3DB)
    val BgGreen  = Color(0xFFEDF3EC)
    val BgBlue   = Color(0xFFE7F3F8)
    val BgPurple = Color(0xFFF6F3F9)
    val BgPink   = Color(0xFFFAF1F5)
    val BgRed    = Color(0xFFFDEBEC)

    // Callout / highlight text colors (saturated pastels)
    val TextBrown  = Color(0xFF64473A)
    val TextOrange = Color(0xFFD9730D)
    val TextYellow = Color(0xFFDFAB01)
    val TextGreen  = Color(0xFF0F7B6C)
    val TextBlue   = Color(0xFF0B6E99)
    val TextPurple = Color(0xFF6940A5)
    val TextPink   = Color(0xFFAD1A72)
    val TextRed    = Color(0xFFE03E3E)
}
```

Wire it into both schemes. Notion is light-first (paper); the dark scheme uses the signature `#191919` charcoal, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val NotionLight = lightColorScheme(
    primary        = NotionColors.Black,
    onPrimary      = NotionColors.Canvas,
    background     = NotionColors.Canvas,
    onBackground   = NotionColors.TextPrimary,
    surface        = NotionColors.SurfaceGray,
    onSurface      = NotionColors.TextPrimary,
    surfaceVariant = NotionColors.SurfacePressed,
    outline        = NotionColors.Divider,
    error          = NotionColors.TextRed,
)

private val NotionDark = darkColorScheme(
    primary        = NotionColors.DarkTextPrimary, // inverted CTA
    onPrimary      = NotionColors.DarkCanvas,
    background     = NotionColors.DarkCanvas,
    onBackground   = NotionColors.DarkTextPrimary,
    surface        = NotionColors.DarkSurface1,
    onSurface      = NotionColors.DarkTextPrimary,
    surfaceVariant = NotionColors.DarkSurface2,
    outline        = NotionColors.DarkDivider,
    error          = NotionColors.TextRed,
)

@Composable
fun NotionTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) NotionDark else NotionLight,
    typography = NotionTypography,
    content = content,
)
```

## 2. Typography

Notion's type family is **user-switchable**: Inter (default sans), Lora (serif), IBM Plex Mono (mono) — all SIL OFL, drop the TTFs in `res/font/`. Respect the user's choice globally via a `CompositionLocal`; never hard-code Inter. Body 400, headings 700; document rhythm, not UI rhythm.

```kotlin
// ui/theme/NotionType.kt
import androidx.compose.material3.Typography
import androidx.compose.runtime.compositionLocalOf
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
    Font(R.font.inter_bold,     FontWeight.Bold),
)
val Lora = FontFamily(
    Font(R.font.lora_regular, FontWeight.Normal),
    Font(R.font.lora_bold,    FontWeight.Bold),
)
val IBMPlexMono = FontFamily(
    Font(R.font.ibm_plex_mono_regular, FontWeight.Normal),
)

enum class NotionFontFamily(val family: FontFamily) {
    Sans(Inter), Serif(Lora), Mono(IBMPlexMono)
}
// Provide at the app root; switch from Settings — applies to all pages
val LocalNotionFont = compositionLocalOf { NotionFontFamily.Sans }

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1). Default = Inter.
object NotionText {
    val PageTitle  = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.5).sp)
    val H1         = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 30.sp, letterSpacing = (-0.3).sp)
    val H2         = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val H3         = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val Body       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val BodyDense  = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val Caption    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val Button     = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val Link       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val Mention    = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 16.sp, lineHeight = 24.sp)
    val Quote      = TextStyle(Lora,  fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 26.sp, fontStyle = FontStyle.Italic)
    val CodeInline = TextStyle(IBMPlexMono, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 20.sp)
    val CodeBlock  = TextStyle(IBMPlexMono, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 20.sp)
    val Tab        = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Sidebar    = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 18.sp)
}

// Helper: rebind a base style onto the user's chosen family
fun TextStyle.withNotionFamily(f: NotionFontFamily): TextStyle = copy(fontFamily = f.family)

// Map onto Material 3 slots so stock components inherit the brand
val NotionTypography = Typography(
    headlineLarge = NotionText.PageTitle,
    headlineMedium = NotionText.H1,
    titleMedium   = NotionText.H3,
    bodyMedium    = NotionText.Body,
    labelSmall    = NotionText.Tab,
)
```

## 3. Signature Components

### Page Header (Cover + Emoji + Title)

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun PageHeader(
    coverUrl: String?,
    emoji: String?,
    title: String,
    onTitleChange: (String) -> Unit,
) {
    Column(Modifier.fillMaxWidth()) {
        if (coverUrl != null) {
            AsyncImage(
                model = coverUrl,
                contentDescription = null,
                modifier = Modifier.fillMaxWidth().height(120.dp), // full-bleed, square corners
                contentScale = ContentScale.Crop,
            )
        }
        if (emoji != null) {
            Text(
                emoji,
                fontSize = 64.sp,
                modifier = Modifier
                    .padding(start = 16.dp)
                    .offset(y = if (coverUrl != null) (-32).dp else 0.dp), // overlaps cover
            )
        }
        BasicTextField(
            value = title,
            onValueChange = onTitleChange,
            textStyle = NotionText.PageTitle.copy(color = NotionColors.TextPrimary),
            modifier = Modifier.padding(horizontal = 16.dp).padding(top = 8.dp, bottom = 16.dp),
            decorationBox = { inner ->
                if (title.isEmpty()) Text("Untitled", style = NotionText.PageTitle, color = NotionColors.TextTertiary)
                inner()
            },
        )
    }
}
```

### Paragraph Block (with long-press handles)

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.fadeIn
import androidx.compose.animation.fadeOut
import androidx.compose.animation.core.tween
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.DragIndicator
import androidx.compose.material3.Icon
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun ParagraphBlock(
    text: String,
    onTextChange: (String) -> Unit,
    onInsertBelow: () -> Unit,
    onOpenMenu: () -> Unit,
    modifier: Modifier = Modifier,
) {
    var handlesVisible by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current

    Row(
        modifier = modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp, vertical = 3.dp)
            .pointerInput(Unit) {
                detectTapGestures(
                    onLongPress = {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress) // soft impact analog
                        handlesVisible = true
                    },
                    onTap = { handlesVisible = false },
                )
            },
        verticalAlignment = Alignment.Top,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        AnimatedVisibility(handlesVisible, enter = fadeIn(tween(120)), exit = fadeOut(tween(120))) {
            Row {
                Icon(
                    Icons.Filled.Add, contentDescription = "Insert block below",
                    tint = NotionColors.TextSecondary,
                    modifier = Modifier.size(20.dp).pointerInput(Unit) { detectTapGestures { onInsertBelow() } },
                )
                Icon(
                    Icons.Filled.DragIndicator, contentDescription = "Block menu", // ⋮⋮⋮
                    tint = NotionColors.TextSecondary,
                    modifier = Modifier.size(20.dp).pointerInput(Unit) { detectTapGestures { onOpenMenu() } },
                )
            }
        }
        BasicTextField(
            value = text,
            onValueChange = onTextChange,
            textStyle = NotionText.Body.copy(color = NotionColors.TextPrimary),
            modifier = Modifier.weight(1f),
            decorationBox = { inner ->
                if (text.isEmpty()) Text("Type '/' for commands", style = NotionText.Body, color = NotionColors.TextSecondary)
                inner()
            },
        )
    }
}
```

### Callout Block

```kotlin
@Composable
fun CalloutBlock(
    emoji: String,
    text: String,
    backgroundColor: Color = NotionColors.BgYellow,
    textColor: Color = NotionColors.TextYellow,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(4.dp)) // no border — pastel bg only
            .background(backgroundColor)
            .padding(16.dp),
        horizontalArrangement = Arrangement.spacedBy(10.dp),
        verticalAlignment = Alignment.Top,
    ) {
        Text(emoji, fontSize = 20.sp)
        Text(text, style = NotionText.Body, color = textColor)
    }
}
```

### Toggle Block

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.expandVertically
import androidx.compose.animation.shrinkVertically
import androidx.compose.material.icons.filled.ChevronRight
import androidx.compose.ui.draw.rotate

@Composable
fun ToggleBlock(
    title: String,
    modifier: Modifier = Modifier,
    content: @Composable () -> Unit,
) {
    var open by remember { mutableStateOf(false) }
    val rotation by animateFloatAsState(if (open) 90f else 0f, tween(150), label = "toggleChevron")
    val haptics = LocalHapticFeedback.current

    Column(modifier.fillMaxWidth()) {
        Row(
            Modifier
                .fillMaxWidth()
                .clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress) // soft
                    open = !open
                }
                .padding(horizontal = 16.dp, vertical = 6.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(4.dp),
        ) {
            Icon(
                Icons.Filled.ChevronRight,
                contentDescription = if (open) "Collapse" else "Expand",
                tint = NotionColors.TextSecondary,
                modifier = Modifier.size(14.dp).rotate(rotation),
            )
            Text(title, style = NotionText.Body.copy(fontWeight = FontWeight.Medium), color = NotionColors.TextPrimary)
        }
        AnimatedVisibility(open, enter = expandVertically(tween(200)) + fadeIn(), exit = shrinkVertically(tween(200)) + fadeOut()) {
            Box(Modifier.padding(start = 24.dp)) { content() } // children indented 24dp
        }
    }
}
```

### Mention Chip (inline)

```kotlin
import androidx.compose.foundation.shape.CircleShape

@Composable
fun MentionChip(name: String, avatarUrl: String?, modifier: Modifier = Modifier) {
    Row(
        modifier
            .clip(RoundedCornerShape(2.dp))
            .background(NotionColors.LinkBlue.copy(alpha = 0.1f)) // 10% blue behind
            .padding(horizontal = 4.dp, vertical = 2.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        if (avatarUrl != null) {
            AsyncImage(
                model = avatarUrl, contentDescription = null,
                modifier = Modifier.size(16.dp).clip(CircleShape),
                contentScale = ContentScale.Crop,
            )
        } else {
            Box(Modifier.size(16.dp).clip(CircleShape).background(NotionColors.LinkBlue.copy(alpha = 0.2f)))
        }
        Text("@$name", style = NotionText.Mention, color = NotionColors.LinkBlue)
    }
}
```

## 4. Block Editor + `/` Command Palette

Notion's creation UX flows through two affordances: the long-press block handles (§3) and the `/` command palette. On iOS it's a cursor-anchored overlay; on Android use a `Popup` so it floats above the page with the right shadow and a tap-outside dismiss.

```kotlin
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.itemsIndexed
import androidx.compose.material.icons.filled.*
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.unit.IntOffset
import androidx.compose.ui.window.Popup
import androidx.compose.ui.window.PopupProperties

data class BlockOption(val icon: ImageVector, val title: String, val subtitle: String)

val defaultSlashOptions = listOf(
    BlockOption(Icons.Filled.Notes, "Text", "Just start writing with plain text."),
    BlockOption(Icons.Filled.Title, "Heading 1", "Big section heading."),
    BlockOption(Icons.Filled.Title, "Heading 2", "Medium section heading."),
    BlockOption(Icons.Filled.ToggleOn, "Toggle list", "Collapsible content."),
    BlockOption(Icons.Filled.Image, "Image", "Upload or embed with a link."),
    BlockOption(Icons.Filled.Lightbulb, "Callout", "Make writing stand out."),
    BlockOption(Icons.Filled.Code, "Code", "Capture a code snippet."),
    BlockOption(Icons.Filled.TableChart, "Table", "Add a database table."),
)

@Composable
fun SlashCommandPalette(
    options: List<BlockOption> = defaultSlashOptions,
    anchorOffset: IntOffset,
    onPick: (BlockOption) -> Unit,
    onDismiss: () -> Unit,
) {
    var selected by remember { mutableIntStateOf(0) }
    Popup(
        offset = anchorOffset, // cursor-anchored
        onDismissRequest = onDismiss,
        properties = PopupProperties(focusable = true),
    ) {
        Column(
            Modifier
                .width(280.dp)
                .heightIn(max = 360.dp)
                .clip(RoundedCornerShape(6.dp))
                // Android has no live blur — a 1dp border doubles as the dark-mode elevation cue
                .background(NotionColors.Canvas)
                .border(1.dp, NotionColors.Divider, RoundedCornerShape(6.dp))
                .shadow(12.dp, RoundedCornerShape(6.dp), spotColor = Color.Black.copy(alpha = 0.08f))
                .padding(vertical = 6.dp),
        ) {
            LazyColumn {
                itemsIndexed(options) { i, opt ->
                    Row(
                        Modifier
                            .fillMaxWidth()
                            .height(40.dp)
                            .background(if (i == selected) NotionColors.SurfacePressed else Color.Transparent)
                            .clickable { selected = i; onPick(opt) }
                            .padding(horizontal = 12.dp),
                        verticalAlignment = Alignment.CenterVertically,
                        horizontalArrangement = Arrangement.spacedBy(12.dp),
                    ) {
                        Icon(opt.icon, contentDescription = null, tint = NotionColors.TextPrimary, modifier = Modifier.size(20.dp))
                        Column(verticalArrangement = Arrangement.spacedBy(1.dp)) {
                            Text(opt.title, style = NotionText.Button.copy(fontWeight = FontWeight.Medium), color = NotionColors.TextPrimary)
                            Text(opt.subtitle, style = NotionText.Tab.copy(fontSize = 12.sp), color = NotionColors.TextSecondary)
                        }
                    }
                }
            }
        }
    }
}
```

Detect `/` at the start of a block's text and raise the palette; arrow keys move `selected`, tap or Enter calls `onPick`, which replaces the current block with the chosen type. The same `Popup` pattern serves the `@`-mention picker (person/page/date rows).

## 5. Navigation

Notion has minimal chrome: a slide-in left sidebar and a 4-tab bottom strip. On Android, model the sidebar as a Material 3 `ModalNavigationDrawer` (edge-swipe + hamburger) and the strip as a `NavigationBar`. There is no tint pill — active is just the dark text color.

```kotlin
@Composable
fun NotionBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = NotionColors.Canvas,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Search"   to Icons.Filled.Search,
            "Updates"  to Icons.Filled.Notifications,
            "Settings" to Icons.Filled.Settings,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = NotionText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = NotionColors.TextPrimary,   // dark text, no tint
                    selectedTextColor = NotionColors.TextPrimary,
                    unselectedIconColor = NotionColors.TextSecondary,
                    unselectedTextColor = NotionColors.TextSecondary,
                    indicatorColor = Color.Transparent, // no Material pill — Notion has none
                ),
            )
        }
    }
}
```

The sidebar (`ModalDrawerSheet`, 280.dp, `SurfaceGray` background) holds the workspace header, Teamspaces/Shared/Private sections, and 32.dp page rows (leading 16.dp emoji, `Sidebar` text, trailing ⋯). Active page row uses `SurfacePressed`; nested pages indent 24.dp per level with a leading chevron. Material's `drawerState` gives the edge-swipe + 250ms open/close for free.

## 6. Motion

Notion motion is quiet — 150–300ms ease-out, never aggressive. Shadows signal "floating, tap outside to dismiss," nothing more.

| Moment | Compose recipe |
|--------|----------------|
| Toggle expand/collapse | chevron `animateFloatAsState` 0 → 90° `tween(150)`; children `expandVertically(tween(200))` + `fadeIn` |
| Block handle reveal | `AnimatedVisibility` `fadeIn(tween(120))` after `detectTapGestures { onLongPress }` |
| `/` palette | `Popup` content `fadeIn` + 4dp `slideInVertically` `tween(150)`; arrow selection instant |
| Block reorder (drag) | `LazyColumn` + `Modifier.draggable`; neighbors shift `animateItemPlacement(tween(200))`; soft haptic on drop |
| Page navigation | Nav3/`NavHost` slide push `tween(300)` |
| Sidebar swipe | `ModalNavigationDrawer` `drawerState` — 1:1 drag, 50% threshold, `spring(dampingRatio = 0.8f)` |
| Text selection | blue `#2E75CC` @ 20% overlay appears instantly (no animation) |

```kotlin
// Toggle children slide — the canonical Notion motion
AnimatedVisibility(
    visible = open,
    enter = expandVertically(animationSpec = tween(200)) + fadeIn(tween(200)),
    exit = shrinkVertically(animationSpec = tween(200)) + fadeOut(tween(150)),
) { /* indented children */ }
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on toggle expand, block drag start, and command-palette open. For a softer tick use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`. Auto-save is silent — no motion, no haptic; only show a "Saved" snackbar on manual save or error.

## 7. Icons

Notion ships custom block-type glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. The block handle (⋮⋮⋮) maps to `DragIndicator`; the page-emoji icon is user text, not an icon.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Updates (tab) | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| Settings (tab) | `gear` | `Icons.Filled.Settings` |
| Hamburger | `sidebar.leading` | `Icons.Filled.Menu` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Block menu (⋮⋮⋮) | `ellipsis.circle` | `Icons.Filled.DragIndicator` |
| Insert below | `plus` | `Icons.Filled.Add` |
| Toggle chevron | `chevron.right` / `chevron.down` | `Icons.Filled.ChevronRight` (rotate 90°) |
| Copy code | `doc.on.doc` | `Icons.Filled.ContentCopy` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Checkbox block | `square` / `checkmark.square.fill` | `Icons.Outlined.CheckBoxOutlineBlank` / `Icons.Filled.CheckBox` |
| Link | `link` | `Icons.Filled.Link` |
| Page icon (default) | `doc.text` | `Icons.Filled.Description` |
| Database table | `tablecells` | `Icons.Filled.TableChart` |
| Database board | `rectangle.3.group` | `Icons.Filled.ViewKanban` |
| Database calendar | `calendar` | `Icons.Filled.CalendarMonth` |
| Database gallery | `square.grid.2x2` | `Icons.Filled.GridView` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Popup` + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; light canvas wants dark-content system bars (and light-content in dark mode). The top nav respects the camera cutout; the `/` palette must position above the IME — use `Modifier.imePadding()` on the editor and place the `Popup` relative to the cursor so it never hides behind the keyboard.
- **Font scaling**: `sp` honors the user's font scale — keep it on page title, headings, body, captions, code (it stays monospace as it scales). Pin layout-sensitive text (block handles, 11sp tab labels, sidebar labels, mention chips, palette option text) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **User font choice**: read `LocalNotionFont` everywhere and apply `style.withNotionFamily(...)` — never hard-code Inter; Lora/IBM Plex Mono must apply globally.
- **TalkBack**: label paragraph blocks "Paragraph: {content}"; toggles "Toggle: {title}, expanded/collapsed"; expose block handles via `Modifier.semantics { customActions = listOf(...) }` (Insert below, Open menu, Move) so they're reachable without the long-press; label `/` palette rows with title + subtitle.
- **Touch targets**: Material guidance is 48.dp. Give the 20.dp block handles and 14.dp toggle chevron a 48.dp hit area via `Modifier.size(48.dp)` padding; sidebar rows are 32.dp tall but full-row tappable; primary buttons ≥ 36.dp.
- **Contrast**: `#37352F` on `#FFFFFF` passes WCAG AA for body at 16sp. The pastel callout pairs (e.g. `#DFAB01` on `#FBF3DB`) are carefully matched — validate any custom pair, especially yellow, with a contrast checker.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the chevron rotation and toggle slide — substitute a plain `Crossfade`; keep the selection highlight (it conveys state).
- **Dark mode**: invert via the `Dark*` palette — `#191919`, NOT true black; `#37352F` text becomes `#E6E6E4`. Shadows are nearly invisible on dark, so the 1dp `DarkDivider` border on floating panels (palette, mention picker, block menu) is the elevation cue. Do **not** enable Material You `dynamicColorScheme()` — Notion's paper-and-warm-text identity must hold regardless of wallpaper (Notion has no brand accent to harmonize with one).
