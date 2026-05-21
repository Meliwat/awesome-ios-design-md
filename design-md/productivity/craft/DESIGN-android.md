# Craft (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Craft's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, the brand `Brush`, a `Typography` set, paste-ready `@Composable`s, the card-block editor + `/` inserter, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Craft's soft charcoal canvas, the constant blue→purple gradient, card-forward blocks with soft shadows, Daily Note, the `/` inserter) while making everything idiomatic Android — `NavigationBar` with a center FAB instead of a UITabBar, a `Popup` instead of a SwiftUI overlay, a `ModalDrawerSheet` instead of a swipe-in sidebar, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for cover images and link favicons. No color extraction — Craft's palette is fixed (brand gradient + neutrals), so Palette is not needed. Do not enable Material You dynamic color: the blue→purple identity must hold regardless of wallpaper.

## 1. Color Tokens

```kotlin
// ui/theme/CraftColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.geometry.Offset

object CraftColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFCFCFD)
    val SurfaceGray    = Color(0xFFF4F4F6)
    val Card           = Color(0xFFFFFFFF)
    val SurfacePressed = Color(0xFFECECEF)
    val Divider        = Color(0xFFE6E6EA)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF1A1A1E) // Craft's soft charcoal — NOT pure black
    val DarkCard     = Color(0xFF232328)
    val DarkSurface2 = Color(0xFF2C2C32)
    val DarkDivider  = Color(0xFF34343C)

    // Text
    val TextPrimary     = Color(0xFF1C1C22)
    val TextSecondary   = Color(0xFF6A6A78)
    val TextTertiary    = Color(0xFF9B9BA6)
    val DarkTextPrimary = Color(0xFFECECEF)
    val DarkTextTertiary = Color(0xFF67677A)

    // Brand (interactive)
    val Blue        = Color(0xFF2F5BEA)
    val BluePressed = Color(0xFF2347C9)
    val Purple      = Color(0xFF6E56CF)
    val PurpleSoft  = Color(0xFF8B73E8)

    // Accents / semantic
    val Green = Color(0xFF30A46C)
    val Amber = Color(0xFFF0A92B)
    val Red   = Color(0xFFE5484D)
    val Pink  = Color(0xFFD6409F)
    val Teal  = Color(0xFF12A594)

    // The constant Craft brand identity — every primary action
    val BrandGradient = Brush.linearGradient(
        colors = listOf(Blue, Purple),
        start = Offset(0f, 0f),
        end = Offset.Infinite,
    )
}
```

Wire it into both schemes. Craft is soft-light-first; the dark scheme uses the signature `#1A1A1E` charcoal, never true black. Primary is `Blue` (the gradient is applied per-component via `BrandGradient`).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val CraftLight = lightColorScheme(
    primary        = CraftColors.Blue,
    onPrimary      = CraftColors.Card,
    background     = CraftColors.Canvas,
    onBackground   = CraftColors.TextPrimary,
    surface        = CraftColors.Card,
    onSurface      = CraftColors.TextPrimary,
    surfaceVariant = CraftColors.SurfaceGray,
    outline        = CraftColors.Divider,
    error          = CraftColors.Red,
)

private val CraftDark = darkColorScheme(
    primary        = CraftColors.Blue,
    onPrimary      = CraftColors.Card,
    background     = CraftColors.DarkCanvas,
    onBackground   = CraftColors.DarkTextPrimary,
    surface        = CraftColors.DarkCard,
    onSurface      = CraftColors.DarkTextPrimary,
    surfaceVariant = CraftColors.DarkSurface2,
    outline        = CraftColors.DarkDivider,
    error          = CraftColors.Red,
)

@Composable
fun CraftTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) CraftDark else CraftLight,
    typography = CraftTypography,
    content = content,
)
```

## 2. Typography

Craft's UI/document face is **Inter** (drop the TTFs in `res/font/`); Lora is an optional serif reading mode; JetBrains Mono is code-only. Body 400, doc title 800 (the single expressive moment); editorial rhythm, not UI rhythm. `pt → sp` 1:1.

```kotlin
// ui/theme/CraftType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),
    Font(R.font.inter_medium,    FontWeight.Medium),
    Font(R.font.inter_semibold,  FontWeight.SemiBold),
    Font(R.font.inter_bold,      FontWeight.Bold),
    Font(R.font.inter_extrabold, FontWeight.ExtraBold),
)
val Lora = FontFamily(Font(R.font.lora_italic, FontWeight.Normal, FontStyle.Italic))
val JetBrainsMono = FontFamily(Font(R.font.jetbrains_mono_regular, FontWeight.Normal))

object CraftText {
    val DocTitle  = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.5).sp)
    val H1        = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 24.sp, lineHeight = 30.sp, letterSpacing = (-0.3).sp)
    val H2        = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val H3        = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 17.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body      = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 25.sp)
    val BodyBold  = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 25.sp)
    val CardTitle = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val CardSub   = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 12.sp, lineHeight = 16.sp)
    val Meta      = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val Caption   = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 17.sp, letterSpacing = 0.1.sp)
    val Button    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 15.sp)
    val Link      = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 16.sp, lineHeight = 25.sp)
    val Tab       = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Sidebar   = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 14.sp, lineHeight = 18.sp)
    val Quote     = TextStyle(Lora,  fontWeight = FontWeight.Normal,    fontSize = 17.sp, lineHeight = 26.sp, fontStyle = FontStyle.Italic)
    val CodeInline = TextStyle(JetBrainsMono, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 20.sp)
    val CodeBlock  = TextStyle(JetBrainsMono, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 20.sp)
}

val CraftTypography = Typography(
    headlineLarge  = CraftText.DocTitle,
    headlineMedium = CraftText.H1,
    titleMedium    = CraftText.H3,
    bodyMedium     = CraftText.Body,
    labelSmall     = CraftText.Tab,
)
```

## 3. Signature Components

### Document Header (Cover + Emoji + Title)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import androidx.compose.foundation.shape.RoundedCornerShape
import coil.compose.AsyncImage

@Composable
fun CraftDocHeader(coverUrl: String?, emoji: String, title: String, onTitleChange: (String) -> Unit, subtitle: String) {
    Column(Modifier.fillMaxWidth()) {
        if (coverUrl != null) {
            AsyncImage(model = coverUrl, contentDescription = null,
                modifier = Modifier.fillMaxWidth().padding(horizontal = 22.dp).height(96.dp).clip(RoundedCornerShape(14.dp)),
                contentScale = ContentScale.Crop)
        } else {
            Box(Modifier.fillMaxWidth().padding(horizontal = 22.dp).height(96.dp)
                .clip(RoundedCornerShape(14.dp)).background(CraftColors.BrandGradient))
        }
        Text(emoji, fontSize = 40.sp, modifier = Modifier.padding(start = 26.dp).offset(y = if (coverUrl != null) (-38).dp else 0.dp))
        BasicTextField(value = title, onValueChange = onTitleChange,
            textStyle = CraftText.DocTitle.copy(color = CraftColors.TextPrimary),
            modifier = Modifier.padding(horizontal = 22.dp, vertical = 6.dp),
            decorationBox = { inner -> if (title.isEmpty()) Text("Untitled", style = CraftText.DocTitle, color = CraftColors.TextTertiary); inner() })
        Text(subtitle, style = CraftText.Meta, color = CraftColors.TextSecondary,
            modifier = Modifier.padding(horizontal = 22.dp).padding(top = 4.dp, bottom = 18.dp))
    }
}
```

### Card Block (Page / Link) — *signature*

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ChevronRight
import androidx.compose.material3.Icon
import androidx.compose.ui.draw.shadow

@Composable
fun CraftCardBlock(emoji: String, title: String, subtitle: String, tint: Color = CraftColors.Blue, onOpen: () -> Unit) {
    Row(
        Modifier
            .fillMaxWidth()
            .shadow(9.dp, RoundedCornerShape(12.dp), spotColor = Color.Black.copy(alpha = 0.10f))
            .clip(RoundedCornerShape(12.dp))
            .background(CraftColors.Card)
            .clickable { onOpen() }
            .border(1.dp, CraftColors.Divider, RoundedCornerShape(12.dp))
            .padding(14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(36.dp).clip(RoundedCornerShape(9.dp)).background(tint.copy(alpha = 0.12f)),
            contentAlignment = Alignment.Center) { Text(emoji, fontSize = 17.sp) }
        Column(Modifier.weight(1f)) {
            Text(title, style = CraftText.CardTitle, color = CraftColors.TextPrimary)
            Text(subtitle, style = CraftText.CardSub, color = CraftColors.TextSecondary)
        }
        Icon(Icons.Filled.ChevronRight, null, tint = CraftColors.TextTertiary, modifier = Modifier.size(16.dp))
    }
}
```

### To-do Block

```kotlin
import androidx.compose.animation.core.Spring
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.material.icons.filled.Check
import androidx.compose.runtime.*
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.graphicsLayer
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.style.TextDecoration

@Composable
fun CraftTodoBlock(initialDone: Boolean = false, text: String) {
    var done by remember { mutableStateOf(initialDone) }
    val pop by animateFloatAsState(if (done) 1f else 1f, spring(Spring.DampingRatioMediumBouncy), label = "pop")
    val haptics = LocalHapticFeedback.current
    Row(Modifier.fillMaxWidth().clickable {
        haptics.performHapticFeedback(HapticFeedbackType.LongPress); done = !done
    }.padding(vertical = 3.dp), verticalAlignment = Alignment.Top, horizontalArrangement = Arrangement.spacedBy(10.dp)) {
        Box(
            Modifier.size(18.dp).scale(pop).clip(RoundedCornerShape(6.dp))
                .background(if (done) CraftColors.Blue else Color.Transparent)
                .border(1.8.dp, if (done) CraftColors.Blue else CraftColors.TextTertiary, RoundedCornerShape(6.dp)),
            contentAlignment = Alignment.Center,
        ) { if (done) Icon(Icons.Filled.Check, null, tint = Color.White, modifier = Modifier.size(11.dp)) }
        Text(text, style = CraftText.Body,
            color = if (done) CraftColors.TextTertiary else CraftColors.TextPrimary,
            textDecoration = if (done) TextDecoration.LineThrough else null)
    }
}
```

### Primary Button (Gradient)

```kotlin
@Composable
fun CraftPrimaryButton(title: String, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        Modifier
            .graphicsLayer { scaleX = if (pressed) 0.98f else 1f; scaleY = if (pressed) 0.98f else 1f; alpha = if (pressed) 0.92f else 1f }
            .clip(RoundedCornerShape(12.dp))
            .background(CraftColors.BrandGradient)
            .clickable(interaction, indication = null) { onClick() }
            .padding(vertical = 13.dp, horizontal = 26.dp),
        contentAlignment = Alignment.Center,
    ) { Text(title, style = CraftText.Button, color = Color.White) }
}
```

### Callout Block

```kotlin
@Composable
fun CraftCalloutBlock(emoji: String, text: String, tint: Color = CraftColors.Blue) {
    Row(
        Modifier.fillMaxWidth().clip(RoundedCornerShape(12.dp)).background(tint.copy(alpha = 0.08f)).padding(14.dp),
        horizontalArrangement = Arrangement.spacedBy(10.dp), verticalAlignment = Alignment.Top,
    ) {
        Text(emoji, fontSize = 20.sp)
        Text(text, style = CraftText.Body, color = CraftColors.TextPrimary)
    }
}
```

## 4. Block Editor + `/` Inserter

Craft's creation UX flows through three affordances: card-forward blocks, Daily Note, and the `/` inserter. On iOS the inserter is a cursor-anchored overlay; on Android use a `Popup` so it floats with the right soft shadow and a tap-outside dismiss.

```kotlin
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.itemsIndexed
import androidx.compose.ui.unit.IntOffset
import androidx.compose.ui.window.Popup
import androidx.compose.ui.window.PopupProperties

data class CraftBlock(val emoji: String, val title: String, val subtitle: String)

val defaultBlocks = listOf(
    CraftBlock("📝", "Text", "Plain paragraph block"),
    CraftBlock("📄", "Page", "Nested sub-document"),
    CraftBlock("✅", "To-do", "Checkable task line"),
    CraftBlock("🖼️", "Image", "Upload or embed"),
    CraftBlock("💬", "Quote", "Set a passage apart"),
    CraftBlock("</>", "Code", "Capture a snippet"),
    CraftBlock("📊", "Table", "Add a grid"),
    CraftBlock("🔽", "Toggle", "Collapsible content"),
)

@Composable
fun CraftSlashInserter(blocks: List<CraftBlock> = defaultBlocks, anchorOffset: IntOffset, onPick: (CraftBlock) -> Unit, onDismiss: () -> Unit) {
    var selected by remember { mutableIntStateOf(0) }
    Popup(offset = anchorOffset, onDismissRequest = onDismiss, properties = PopupProperties(focusable = true)) {
        Column(
            Modifier.width(300.dp).heightIn(max = 360.dp)
                .shadow(16.dp, RoundedCornerShape(12.dp), spotColor = Color.Black.copy(alpha = 0.14f))
                .clip(RoundedCornerShape(12.dp)).background(CraftColors.Card)
                .border(1.dp, CraftColors.Divider, RoundedCornerShape(12.dp)).padding(vertical = 6.dp),
        ) {
            LazyColumn {
                itemsIndexed(blocks) { i, b ->
                    Row(
                        Modifier.fillMaxWidth().height(44.dp)
                            .background(if (i == selected) CraftColors.Blue.copy(alpha = 0.10f) else Color.Transparent)
                            .clickable { selected = i; onPick(b) }.padding(horizontal = 14.dp),
                        verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(12.dp),
                    ) {
                        Box(Modifier.size(28.dp).clip(RoundedCornerShape(7.dp)).background(CraftColors.SurfaceGray),
                            contentAlignment = Alignment.Center) { Text(b.emoji, fontSize = 14.sp) }
                        Column {
                            Text(b.title, style = CraftText.Caption.copy(fontWeight = FontWeight.SemiBold), color = CraftColors.TextPrimary)
                            Text(b.subtitle, style = CraftText.Caption.copy(fontSize = 11.sp), color = CraftColors.TextSecondary)
                        }
                    }
                }
            }
        }
    }
}
```

Detect `/` at the start of a block's text and raise the `Popup`; arrow keys move `selected`, tap or Enter calls `onPick`, which replaces the current block with the chosen type. The same pattern serves the `@`-link picker (page/Daily-Note rows).

## 5. Navigation

Craft has minimal chrome: a slide-in space/document list and a bottom strip with a center gradient FAB. Model the sidebar as a Material 3 `ModalNavigationDrawer` and the strip as a `NavigationBar` with a center `FloatingActionButton`. There is no tint pill — active is solid `Blue`.

```kotlin
import androidx.compose.material3.*

@Composable
fun CraftScaffold(selected: Int, onSelect: (Int) -> Unit, onAdd: () -> Unit, content: @Composable (PaddingValues) -> Unit) {
    Scaffold(
        floatingActionButton = {
            Box(
                Modifier.size(46.dp).clip(RoundedCornerShape(14.dp)).background(CraftColors.BrandGradient)
                    .clickable { onAdd() }
                    .shadow(10.dp, RoundedCornerShape(14.dp), spotColor = CraftColors.Blue.copy(alpha = 0.6f)),
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Filled.Add, "New", tint = Color.White, modifier = Modifier.size(22.dp)) }
        },
        floatingActionButtonPosition = FabPosition.Center,
        bottomBar = {
            NavigationBar(containerColor = CraftColors.Canvas, tonalElevation = 0.dp) {
                val items = listOf("Home" to Icons.Filled.Home, "Search" to Icons.Filled.Search,
                    "Docs" to Icons.Filled.Description, "Profile" to Icons.Filled.AccountCircle)
                items.forEachIndexed { i, (label, icon) ->
                    if (i == 2) Spacer(Modifier.weight(1f)) // gap for the center FAB
                    NavigationBarItem(
                        selected = selected == i, onClick = { onSelect(i) },
                        icon = { Icon(icon, label, modifier = Modifier.size(21.dp)) },
                        colors = NavigationBarItemDefaults.colors(
                            selectedIconColor = CraftColors.Blue,
                            unselectedIconColor = CraftColors.TextSecondary,
                            indicatorColor = Color.Transparent, // no Material pill — Craft has none
                        ),
                    )
                }
            }
        },
    ) { content(it) }
}
```

The sidebar (`ModalDrawerSheet`, 280.dp, `SurfaceGray` background) holds the workspace header and Spaces / Recent / Daily Notes / Shared sections; 44.dp page rows (leading 20.dp emoji, `Sidebar` text, trailing ⋯). Active row uses `SurfacePressed`; nested pages indent 20.dp with a leading chevron. `drawerState` gives the edge-swipe + 250ms open/close for free.

## 6. Motion

Craft motion is fluid spring physics — slightly bouncy, never linear or abrupt. Shadows are soft and brand-aware (the FAB casts a blue-tinted shadow).

| Moment | Compose recipe |
|--------|----------------|
| Card open | Nav slide push `tween(320)` with subtle parallax |
| To-do complete | checkbox `scale` `spring(dampingRatio = MediumBouncy)` + soft haptic |
| Toggle expand/collapse | chevron `animateFloatAsState` 0 → 90° `tween(180)`; children `expandVertically(spring(stiffness = Medium))` + `fadeIn` |
| `/` inserter | `Popup` content `fadeIn` + 6dp `slideInVertically` `tween(180)`; arrow selection instant |
| Block reorder (drag) | `LazyColumn` + `Modifier.draggable`; lift `scale 1.03` + shadow bloom; neighbors `animateItemPlacement(tween(200))`; soft haptic on drop |
| Sheet present | `ModalBottomSheet` spring `(dampingRatio = 0.82)` |
| FAB / button press | `graphicsLayer` scale 0.98 + alpha 0.92 |
| Text selection | blue `#2F5BEA` @ ~16% overlay appears instantly |

```kotlin
// Toggle children expand — the canonical Craft motion
AnimatedVisibility(
    visible = open,
    enter = expandVertically(spring(stiffness = Spring.StiffnessMedium)) + fadeIn(),
    exit = shrinkVertically(spring(stiffness = Spring.StiffnessMedium)) + fadeOut(),
) { Box(Modifier.padding(start = 20.dp)) { content() } }
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on to-do complete, toggle expand, block drag start, and `/` open. Auto-save is silent — no motion, no haptic; show a "Saved"/"Syncing" snackbar only on conflict or manual save.

## 7. Icons

Use `androidx.compose.material:material-icons-extended`. The block-card icon chips hold user emoji (text, not icons); the gradient is applied via `CraftColors.BrandGradient`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Documents (tab) | `doc.text` | `Icons.Filled.Description` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Add (FAB) | `plus` | `Icons.Filled.Add` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Page actions | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Card chevron | `chevron.right` | `Icons.Filled.ChevronRight` |
| To-do checked | `checkmark` | `Icons.Filled.Check` |
| Toggle | `chevron.right` (rotate 90°) | `Icons.Filled.ChevronRight` |
| Copy code | `doc.on.doc` | `Icons.Filled.ContentCopy` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Link to page | `link` | `Icons.Filled.Link` |
| Daily Note | `calendar` | `Icons.Filled.CalendarMonth` |
| Page block | `doc` | `Icons.Filled.InsertDriveFile` |
| Image block | `photo` | `Icons.Filled.Image` |
| Style sheet | `paintbrush` | `Icons.Filled.Brush` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Popup` + spring motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the soft canvas wants dark-content system bars (light-content in dark mode). Top bar respects the camera cutout; the `/` inserter must position above the IME — use `Modifier.imePadding()` on the editor and place the `Popup` relative to the cursor.
- **Font scaling**: `sp` honors the user's font scale — keep it on doc title, headings, body, quote, captions, code (stays monospace as it scales). Pin layout-sensitive text (card subtitles, 10sp tab labels, slash-menu rows, sidebar labels) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: label card blocks "Page: {title}, {subtitle}"; to-dos "To-do: {text}, checked/unchecked"; toggles "Toggle: {title}, expanded/collapsed"; expose block actions via `Modifier.semantics { customActions = listOf(...) }` (Style, Turn into, Move, Link to, Delete) so they're reachable without the long-press.
- **Touch targets**: Material guidance is 48.dp. Give the 18.dp to-do checkbox and 14.dp toggle chevron a 48.dp hit area via padding; sidebar rows are 44.dp tall, full-row tappable; the FAB is 46.dp; primary buttons ≥ 44.dp.
- **Contrast**: `#1C1C22` on `#FCFCFD` passes WCAG AA for body at 16sp; gradient-on-white button text `#FFFFFF` passes AA; validate amber callout pairs with a contrast checker.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the chevron rotation, to-do scale-pop, and toggle slide — substitute a plain `Crossfade`; keep the selection highlight.
- **Dark mode**: invert via the `Dark*` palette — `#1A1A1E`, NOT true black; `#1C1C22` text becomes `#ECECEF`. The brand gradient is unchanged. Shadows are nearly invisible on dark, so a 1dp `DarkDivider` border on floating panels (`/` inserter, block menu) is the elevation cue. Do **not** enable `dynamicColorScheme()` — Craft's blue→purple identity must hold regardless of wallpaper.
