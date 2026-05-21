# Miro (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Miro's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the gesture-driven infinite canvas, the floating toolbar, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Miro Yellow on ink, the infinite dotted-grid canvas, paper sticky notes, the floating toolbar, Miro Blue selection, live cursors) while making everything idiomatic Android — `pointerInput` transform gestures instead of UIKit gestures, a `NavigationBar` for Boards-home, a `Popup` for tool sub-palettes, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for board thumbnails and image objects. No color extraction — Miro's palette is fixed (yellow/ink/blue + fixed sticky pastels), so Palette is not needed. Do not enable Material You dynamic color: the Miro Yellow + ink identity must hold regardless of wallpaper.

## 1. Color Tokens

```kotlin
// ui/theme/MiroColors.kt
import androidx.compose.ui.graphics.Color

object MiroColors {
    // Board & Chrome (Light)
    val Board          = Color(0xFFF5F5F7)
    val Grid           = Color(0xFFD7D7DE)
    val Chrome         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF1F1F4)
    val SurfacePressed = Color(0xFFE6E6EB)
    val Divider        = Color(0xFFE3E3E8)

    // Board & Chrome (Dark)
    val DarkCanvas   = Color(0xFF1B1B1F)
    val DarkBoard    = Color(0xFF202024)
    val DarkGrid     = Color(0xFF38383F)
    val DarkSurface1 = Color(0xFF26262B)
    val DarkSurface2 = Color(0xFF313138)

    // Text
    val Ink           = Color(0xFF050038) // Miro Ink — primary text on light, glyph on yellow
    val TextSecondary = Color(0xFF6B6B7B)
    val TextTertiary  = Color(0xFF9A9AA4)
    val DarkTextPrimary = Color(0xFFECECEF)
    val NoteInk       = Color(0xFF2A2A33)

    // Brand / interactive
    val Yellow        = Color(0xFFFFD02F)
    val YellowPressed = Color(0xFFE8B800)
    val Blue          = Color(0xFF4262FF) // selection / interaction
    val BluePressed   = Color(0xFF2F4AE0)

    // Sticky note palette — full-color in both modes
    val NoteYellow = Color(0xFFFEF3B6)
    val NotePink   = Color(0xFFF8C8D8)
    val NoteGreen  = Color(0xFFC7E8C7)
    val NoteBlue   = Color(0xFFBCE0F5)
    val NoteOrange = Color(0xFFFCD9B6)
    val NoteViolet = Color(0xFFDCC9F0)

    // Semantic
    val Error   = Color(0xFFE5484D)
    val Success = Color(0xFF2EA56A)
    val Warning = Color(0xFFF0A92B)
}

// Multiplayer cursor colors — round-robin per collaborator
val MiroCursorColors = listOf(
    Color(0xFF4262FF), Color(0xFFF24822), Color(0xFF14AE5C),
    Color(0xFF9747FF), Color(0xFFF2A900), Color(0xFFE5489E),
)
```

Wire it into both schemes. Miro is light-first; the dark scheme uses `#202024` board, never true black. Primary is `Yellow` with `onPrimary` = `Ink`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val MiroLight = lightColorScheme(
    primary        = MiroColors.Yellow,
    onPrimary      = MiroColors.Ink,
    secondary      = MiroColors.Blue,
    background     = MiroColors.Board,
    onBackground   = MiroColors.Ink,
    surface        = MiroColors.Chrome,
    onSurface      = MiroColors.Ink,
    surfaceVariant = MiroColors.SurfaceGray,
    outline        = MiroColors.Divider,
    error          = MiroColors.Error,
)

private val MiroDark = darkColorScheme(
    primary        = MiroColors.Yellow,
    onPrimary      = MiroColors.Ink,
    secondary      = MiroColors.Blue,
    background     = MiroColors.DarkBoard,
    onBackground   = MiroColors.DarkTextPrimary,
    surface        = MiroColors.DarkSurface1,
    onSurface      = MiroColors.DarkTextPrimary,
    surfaceVariant = MiroColors.DarkSurface2,
    outline        = MiroColors.DarkGrid,
    error          = MiroColors.Error,
)

@Composable
fun MiroTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) MiroDark else MiroLight,
    typography = MiroTypography,
    content = content,
)
```

## 2. Typography

Miro's UI face is **Inter** (drop the TTFs in `res/font/`); Caveat is an optional handwriting sticky face; JetBrains Mono is code-embed only. UI hierarchy, not editorial rhythm; board title 800; `pt → sp` 1:1.

```kotlin
// ui/theme/MiroType.kt
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
val Caveat = FontFamily(Font(R.font.caveat_semibold, FontWeight.SemiBold))
val JetBrainsMono = FontFamily(Font(R.font.jetbrains_mono_regular, FontWeight.Normal))

object MiroText {
    val BoardTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.5).sp)
    val H1         = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val FrameLabel = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Sticky     = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 21.sp)
    val Body       = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val Control    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 15.sp)
    val Button     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp)
    val Meta       = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Caption    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val Connector  = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 13.sp, lineHeight = 16.sp)
    val ListRow    = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 16.sp, lineHeight = 21.sp)
    val Tab        = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Avatar     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
}

val MiroTypography = Typography(
    headlineLarge  = MiroText.BoardTitle,
    headlineMedium = MiroText.H1,
    titleMedium    = MiroText.FrameLabel,
    bodyMedium     = MiroText.Body,
    labelSmall     = MiroText.Tab,
)
```

## 3. Signature Components

### Infinite Canvas (transform gestures + dotted grid)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.gestures.detectTransformGestures
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.drawBehind
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.graphicsLayer
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.unit.dp

@Composable
fun MiroCanvas(dark: Boolean, content: @Composable () -> Unit) {
    var scale by remember { mutableFloatStateOf(1f) }
    var offset by remember { mutableStateOf(Offset.Zero) }
    val gridColor = if (dark) MiroColors.DarkGrid else MiroColors.Grid

    Box(
        Modifier
            .fillMaxSize()
            .background(if (dark) MiroColors.DarkBoard else MiroColors.Board)
            .pointerInput(Unit) {
                detectTransformGestures { _, pan, zoom, _ ->
                    scale = (scale * zoom).coerceIn(0.2f, 5f)
                    offset += pan
                }
            }
            .drawBehind {
                val step = 28.dp.toPx() * scale
                if (step >= 6f) {
                    var x = offset.x % step
                    while (x < size.width) {
                        var y = offset.y % step
                        while (y < size.height) { drawCircle(gridColor, radius = 0.75f * scale, center = Offset(x, y)); y += step }
                        x += step
                    }
                }
            },
    ) {
        Box(Modifier.graphicsLayer {
            translationX = offset.x; translationY = offset.y
            scaleX = scale; scaleY = scale
            transformOrigin = androidx.compose.ui.graphics.TransformOrigin(0f, 0f)
        }) { content() }
    }
}
```

### Sticky Note — *signature*

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.foundation.border

@Composable
fun MiroStickyNote(text: String, fill: Color = MiroColors.NoteYellow, selectedInit: Boolean = false) {
    var selected by remember { mutableStateOf(selectedInit) }
    Box(Modifier.size(96.dp).clickable { selected = !selected }) {
        Box(
            Modifier.size(96.dp)
                .shadow(9.dp, RoundedCornerShape(4.dp), spotColor = Color.Black.copy(alpha = 0.18f))
                .clip(RoundedCornerShape(4.dp)).background(fill).padding(10.dp),
            contentAlignment = Alignment.Center,
        ) {
            Text(text, style = MiroText.Sticky, color = MiroColors.NoteInk, textAlign = TextAlign.Center, maxLines = 5)
            // For true auto-fit, measure with TextMeasurer and shrink fontSize until it fits.
        }
        if (selected) {
            Box(Modifier.matchParentSize().padding((-4).dp).border(2.dp, MiroColors.Blue, RoundedCornerShape(6.dp)))
            Box(Modifier.align(Alignment.BottomCenter).offset(y = 8.dp)
                .size(width = 30.dp, height = 4.dp).clip(RoundedCornerShape(3.dp)).background(MiroColors.Blue))
        }
    }
}
```

### Floating Toolbar

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

private val toolIcons = listOf(
    Icons.Filled.NearMe, Icons.Filled.StickyNote2, Icons.Filled.Edit,
    Icons.Filled.Category, Icons.Filled.TextFields, Icons.Filled.CropFree,
    Icons.Filled.ChatBubbleOutline, Icons.Filled.Add,
)

@Composable
fun MiroToolbar(active: Int, onSelect: (Int) -> Unit) {
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier
            .shadow(16.dp, RoundedCornerShape(16.dp), spotColor = Color.Black.copy(alpha = 0.22f))
            .clip(RoundedCornerShape(16.dp))
            .background(MiroColors.Chrome)
            .border(1.dp, MiroColors.Divider, RoundedCornerShape(16.dp))
            .padding(8.dp),
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        toolIcons.forEachIndexed { i, icon ->
            Box(
                Modifier.size(40.dp).clip(RoundedCornerShape(11.dp))
                    .background(if (i == active) MiroColors.Yellow else Color.Transparent)
                    .clickable { haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove); onSelect(i) },
                contentAlignment = Alignment.Center,
            ) {
                Icon(icon, null, tint = if (i == active) MiroColors.Ink else MiroColors.TextSecondary, modifier = Modifier.size(20.dp))
            }
        }
    }
}
```

### Primary Button (Yellow)

```kotlin
@Composable
fun MiroPrimaryButton(title: String, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        Modifier
            .graphicsLayer { val s = if (pressed) 0.98f else 1f; scaleX = s; scaleY = s }
            .clip(RoundedCornerShape(10.dp))
            .background(if (pressed) MiroColors.YellowPressed else MiroColors.Yellow)
            .clickable(interaction, indication = null) { onClick() }
            .padding(vertical = 13.dp, horizontal = 26.dp),
        contentAlignment = Alignment.Center,
    ) { Text(title, style = MiroText.Button, color = MiroColors.Ink) }
}
```

### Multiplayer Cursor

```kotlin
import androidx.compose.animation.core.animateOffsetAsState
import androidx.compose.animation.core.tween
import androidx.compose.ui.draw.rotate

@Composable
fun MiroCursor(name: String, colorIndex: Int, target: Offset) {
    val color = MiroCursorColors[colorIndex % MiroCursorColors.size]
    // Interpolate between network updates so the cursor glides, never jumps
    val pos by animateOffsetAsState(target, tween(50), label = "cursor")
    Row(
        Modifier.graphicsLayer { translationX = pos.x; translationY = pos.y },
        verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp),
    ) {
        Icon(Icons.Filled.NearMe, null, tint = color, modifier = Modifier.size(14.dp).rotate(-35f))
        Box(Modifier.clip(RoundedCornerShape(10.dp)).background(color).padding(horizontal = 7.dp, vertical = 2.dp)) {
            Text(name, style = MiroText.Avatar, color = Color.White)
        }
    }
}
```

## 4. Board Editor + Zoom / Tool Sub-palette

Miro's creation UX flows through the floating toolbar; long-pressing a tool opens its sub-palette. On Android use a `Popup` so it floats above the canvas with the right shadow and a tap-outside dismiss.

```kotlin
import androidx.compose.ui.window.Popup
import androidx.compose.ui.window.PopupProperties

@Composable
fun MiroToolSubPalette(swatches: List<Color>, onPick: (Color) -> Unit, onDismiss: () -> Unit) {
    Popup(onDismissRequest = onDismiss, properties = PopupProperties(focusable = true)) {
        Row(
            Modifier
                .shadow(14.dp, RoundedCornerShape(12.dp), spotColor = Color.Black.copy(alpha = 0.22f))
                .clip(RoundedCornerShape(12.dp)).background(MiroColors.Chrome)
                .border(1.dp, MiroColors.Divider, RoundedCornerShape(12.dp))
                .padding(10.dp),
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            swatches.forEach { c ->
                Box(Modifier.size(24.dp).clip(androidx.compose.foundation.shape.CircleShape).background(c).clickable { onPick(c) })
            }
        }
    }
}
```

### Zoom Pill

```kotlin
@Composable
fun MiroZoomPill(percent: Int, onZoom: (Int) -> Unit) {
    Column(
        Modifier
            .shadow(10.dp, RoundedCornerShape(12.dp), spotColor = Color.Black.copy(alpha = 0.16f))
            .clip(RoundedCornerShape(12.dp)).background(MiroColors.Chrome),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Box(Modifier.size(38.dp).clickable { onZoom(25) }, contentAlignment = Alignment.Center) {
            Icon(Icons.Filled.Add, "Zoom in", tint = MiroColors.Ink, modifier = Modifier.size(18.dp))
        }
        Text("$percent%", style = MiroText.Caption, color = MiroColors.TextSecondary,
            modifier = Modifier.padding(vertical = 4.dp))
        Box(Modifier.size(38.dp).clickable { onZoom(-25) }, contentAlignment = Alignment.Center) {
            Icon(Icons.Filled.Remove, "Zoom out", tint = MiroColors.Ink, modifier = Modifier.size(18.dp))
        }
    }
}
```

## 5. Navigation

Inside a board there is no bottom bar (the toolbar floats over the canvas). The Boards-home shell uses a `NavigationBar`; active is `Ink`, no tint pill.

```kotlin
import androidx.compose.material3.*

@Composable
fun MiroHomeBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = MiroColors.Chrome, tonalElevation = 0.dp) {
        val items = listOf(
            "Boards"    to Icons.Filled.GridView,
            "Templates" to Icons.Filled.Dashboard,
            "Activity"  to Icons.Filled.Notifications,
            "Profile"   to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i, onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = MiroText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = MiroColors.Ink,
                    unselectedIconColor = MiroColors.TextSecondary,
                    indicatorColor = Color.Transparent, // no Material pill — Miro has none
                ),
            )
        }
    }
}
```

## 6. Motion

Miro motion is springy and tactile — objects feel like physical paper; remote cursors glide.

| Moment | Compose recipe |
|--------|----------------|
| Pan / zoom | `detectTransformGestures`; clamp scale 0.2–5; offset accumulates |
| Zoom-to-fit (double-tap) | `animateFloatAsState` scale → 1 + `animateOffsetAsState` offset → 0 `tween(350)` |
| Object create (sticky) | `AnimatedVisibility` scale-in from 0.85 `spring(dampingRatio = MediumBouncy)` + soft haptic |
| Object drag | follow finger; `scale 1.03` + shadow bloom; settle `spring(dampingRatio = 0.82)`; soft haptic on drop |
| Connector draw | rubber-band line follows pointer; magnet-snap to anchor + rigid haptic |
| Tool select | active fill `Crossfade`/`animateColorAsState` ~120ms; sub-palette `Popup` spring pop |
| Selection box | `AnimatedVisibility` `fadeIn(tween(120))` when selected |
| Multiplayer cursor | `animateOffsetAsState(target, tween(50))` so it glides between updates |
| Sheet present | `ModalBottomSheet` spring `(dampingRatio = 0.82)`; ink-tinted scrim |

```kotlin
// Zoom-to-fit — the canonical reset
val animScale by animateFloatAsState(targetScale, tween(350), label = "fit")
val animOffset by animateOffsetAsState(targetOffset, tween(350), label = "fitOffset")
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for soft impact on object create / drag drop; use `HapticFeedbackType.TextHandleMove` for the lighter tool-select tick; a rigid pulse on connector snap. Auto-save is silent; show a "Saved"/"Syncing…" snackbar only on conflict or offline.

## 7. Icons

Use `androidx.compose.material:material-icons-extended`. Sticky-note text is user content (not an icon); the active tool fills `MiroColors.Yellow` with an `Ink` glyph.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Select tool | `cursorarrow` | `Icons.Filled.NearMe` |
| Sticky note tool | `square.fill` | `Icons.Filled.StickyNote2` |
| Pen tool | `pencil.tip` | `Icons.Filled.Edit` |
| Shapes tool | `square.on.circle` | `Icons.Filled.Category` |
| Text tool | `textformat` | `Icons.Filled.TextFields` |
| Frame tool | `rectangle.dashed` | `Icons.Filled.CropFree` |
| Comment tool | `bubble.left` | `Icons.Filled.ChatBubbleOutline` |
| More tool | `plus` | `Icons.Filled.Add` |
| Zoom in / out | `plus` / `minus` | `Icons.Filled.Add` / `Icons.Filled.Remove` |
| Fit / minimap | `arrow.up.left.and.arrow.down.right` | `Icons.Filled.OpenInFull` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Board menu | `chevron.down` | `Icons.Filled.ExpandMore` |
| Board actions | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Collaborators | `person.2.fill` | `Icons.Filled.People` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Cursor (remote) | `cursorarrow.rays` | `Icons.Filled.NearMe` |
| Boards (home) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Templates (home) | `rectangle.on.rectangle` | `Icons.Filled.Dashboard` |
| Activity (home) | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| Profile (home) | `person.crop.circle` | `Icons.Filled.AccountCircle` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; transform gestures + `Popup` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the board top bar is translucent over the canvas (light-content system bars on the light board, dark-content in dark mode if the chrome is light there). Float toolbar + zoom pill above the gesture nav bar.
- **Canvas perf**: drive pan/zoom with a single `graphicsLayer` transform; draw the grid in `drawBehind` (skip when `step < 6f`); virtualize off-screen objects on large boards; offload the realtime layer off the main thread.
- **Font scaling**: `sp` honors the user's font scale — keep it on board title, H1, body, list rows; pin layout-sensitive text (toolbar labels, 12sp zoom %, avatar initials, connector labels, 10sp tab labels) via `dp`-derived sizes or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. Sticky-note text should auto-fit its box (measure + shrink), independent of font scale.
- **TalkBack**: the infinite canvas is hard to navigate non-visually — expose objects as an accessible list with a "Find on board" search; label sticky notes "Sticky note: {text}, {color}"; toolbar tools labeled by name + selected state; announce remote presence sparingly ("{name} is editing").
- **Touch targets**: Material guidance is 48.dp. Give 40.dp toolbar tiles, 38.dp zoom buttons, and resize handles a 48.dp hit area via padding; list rows/buttons ≥ 48.dp.
- **Contrast**: `#050038` on `#FFD02F` passes WCAG AA for the primary button; `#2A2A33` on every sticky pastel passes AA; verify `#4262FF` selection chrome against both board colors.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable pan inertia, the object scale-in, and zoom-to-fit animation (jump instead); keep the selection box; freeze cursor interpolation to discrete updates.
- **Dark mode**: invert chrome to `#26262B` and board to `#202024` — **sticky notes stay full-color** (paper doesn't go dark); Miro Yellow / Blue selection unchanged; cursor colors unchanged. Shadows are nearly invisible on dark, so a 1dp `DarkGrid` border on the floating toolbar/popovers is the elevation cue. Do **not** enable `dynamicColorScheme()` — the Miro Yellow + ink identity must hold regardless of wallpaper.
