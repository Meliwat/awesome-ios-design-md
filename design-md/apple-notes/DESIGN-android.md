# Apple Notes (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Apple Notes's paper-soft aesthetic to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the iconic yellow folder glyph drawn with `Canvas`, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Apple Notes' warm cream-paper canvas, the iconic 3D yellow tab-folder glyph, single Notes-Orange action color, the calmest body type in mobile) while making everything idiomatic Android — hierarchical `NavHost` navigation instead of `NavigationStack`, a `Canvas` `Path` for the folder glyph instead of SwiftUI `Canvas`, a translucent `Surface` instead of `.regularMaterial` (Android has no live blur), `sp`/`dp` instead of `pt`. Notes supports light and dark, so we wire a full Material 3 light + dark scheme; the cream canvas is deliberately *warmer than white*.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. No remote-image or color-extraction libraries are needed — Notes has no photography and no dynamic palette; the only "graphic" is the hand-drawn folder glyph.

## 1. Color Tokens

```kotlin
// ui/theme/NotesColors.kt
import androidx.compose.ui.graphics.Color

object NotesColors {
    // Canvas & Surfaces (Light — warm cream paper)
    val Cream        = Color(0xFFFFFBED) // primary canvas — warmer than white by ~3%
    val CreamSurf1   = Color(0xFFFAF6E3) // search field, focused section
    val CreamSurf2   = Color(0xFFF2EDD6) // pressed rows, chip fills
    val DividerLight = Color(0xFFEDEAD8) // warm hairline dividers

    // Canvas & Surfaces (Dark)
    val DarkPaper   = Color(0xFF1A1A1A) // true dark gray — preserves paper feel, NOT pure black
    val DarkSurf1   = Color(0xFF262626)
    val DarkSurf2   = Color(0xFF333333)
    val DarkDivider = Color(0xFF2A2A2A)

    // Brand
    val Orange          = Color(0xFFF09A38) // FAB, folder stroke, cursor, link, selection accent
    val OrangePressed   = Color(0xFFD87E1F)
    val OrangeTint      = Color(0xFFFFF1DD) // selected-row / chip background
    val FolderYellow    = Color(0xFFF5D773) // the iconic folder glyph fill
    val FolderHighlight = Color(0xFFFAE8A0) // folder top-edge highlight (depth)
    val HighlightYellow = Color(0xFFFFEB78) // text-selection highlight ONLY

    // Text
    val Ink       = Color(0xFF1C1C1E) // primary — titles, body, folder names
    val Slate     = Color(0xFF8E8E93) // secondary — preview, date, metadata
    val Mute      = Color(0xFFC7C7CC) // tertiary — placeholder, disabled
    val SoftWhite = Color(0xFFF2F2F2) // primary text on dark

    // Semantic
    val Success = Color(0xFF34C759)
    val Warning = Color(0xFFFFCC00)
    val Error   = Color(0xFFFF3B30)
    val InfoBlue = Color(0xFF0A84FF)
    val LockYellow = Color(0xFFFFCC00)

    // Highlight palette (text-selection colors inside the note body)
    val HighlightGreen  = Color(0xFFB3E47B)
    val HighlightBlue   = Color(0xFFA0D8FF)
    val HighlightPink   = Color(0xFFFFB3DB)
    val HighlightPurple = Color(0xFFD4B3FF)
}
```

Notes supports light and dark equally — wire both Material 3 schemes. The cream/dark-paper canvas and the single Orange accent carry the brand; the folder yellow is glyph-only and never a scheme role.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val NotesLight = lightColorScheme(
    primary        = NotesColors.Orange,
    onPrimary      = Color.White,
    background     = NotesColors.Cream,          // warm cream, never #FFFFFF
    onBackground   = NotesColors.Ink,
    surface        = NotesColors.Cream,
    onSurface      = NotesColors.Ink,
    surfaceVariant = NotesColors.CreamSurf1,
    outline        = NotesColors.DividerLight,
    error          = NotesColors.Error,
)

private val NotesDark = darkColorScheme(
    primary        = NotesColors.Orange,         // unchanged across light/dark
    onPrimary      = Color.White,
    background     = NotesColors.DarkPaper,       // true dark gray, not pure black
    onBackground   = NotesColors.SoftWhite,
    surface        = NotesColors.DarkPaper,
    onSurface      = NotesColors.SoftWhite,
    surfaceVariant = NotesColors.DarkSurf1,
    outline        = NotesColors.DarkDivider,
    error          = NotesColors.Error,
)

@Composable
fun NotesTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (darkTheme) NotesDark else NotesLight,
    typography  = NotesTypography,
    content     = content,
)
```

## 2. Typography

Apple Notes is 100% SF Pro — including the rare SF Pro Display **Heavy (800)** on the large "Notes" nav title and the calm SF Pro Text 17pt/1.5 body. SF Pro is unavailable on Android — use the system font (Roboto), reserving a bundled `Inter` for a tighter metric match. The note body's generous 1.5 line-height (25sp at 17sp) is the centerpiece — preserve it exactly. Honor font scaling everywhere (Notes skews older-demographic).

```kotlin
// ui/theme/NotesType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

private val SF = FontFamily.Default          // swap to FontFamily(Font(R.font.inter_…))
private val SFMono = FontFamily.Monospace    // code-formatted text inside notes

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object NotesText {
    val LargeNav    = TextStyle(SF, fontWeight = FontWeight.Black,    fontSize = 34.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp) // Heavy (800)
    val FolderSec   = TextStyle(SF, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val RowTitle    = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val RowPreview  = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 19.sp)
    val RowDate     = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val FolderRow   = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 22.sp)
    val BodyTitle   = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val BodyHeading = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 24.sp)
    val Body        = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 25.sp) // 1.5× — Apple's calmest body
    val BodyMono    = TextStyle(SFMono, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 22.sp)
    val TagChip     = TextStyle(SF, fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 14.sp)
    val Toolbar     = TextStyle(SF, fontWeight = FontWeight.Medium,   fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 17.sp)
    val SearchHint  = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 17.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val NotesTypography = Typography(
    headlineLarge = NotesText.LargeNav,
    headlineSmall = NotesText.FolderSec,
    titleMedium   = NotesText.RowTitle,
    bodyLarge     = NotesText.Body,
    bodyMedium    = NotesText.RowPreview,
    labelLarge    = NotesText.Button,
    labelSmall    = NotesText.Toolbar,
)
```

## 3. Signature Components

### Yellow Folder Glyph (the iconic mark)

Apple's cross-app yellow tab-folder, drawn with a Compose `Canvas` `Path` — fill `#F5D773`, 1dp `#F09A38` stroke, a 0.5dp warm top-edge highlight, and a subtle 2° forward tilt for the hand-painted depth illusion.

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.size
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.rotate
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp

@Composable
fun FolderGlyph(modifier: Modifier = Modifier, size: Dp = 24.dp) {
    Canvas(
        modifier
            .size(width = size, height = size * 0.78f)
            .rotate(-2f), // subtle forward tilt — physical-folder feel
    ) {
        val w = this.size.width
        val h = this.size.height
        val tabH = h * 0.18f
        val tabW = w * 0.45f
        val top = tabH

        val folder = Path().apply {
            moveTo(0f, top)
            lineTo(0f, h)
            lineTo(w, h)
            lineTo(w, top - 2f)
            lineTo(tabW + 6f, top - 2f)
            cubicTo(tabW + 4f, 0f, tabW + 2f, 0f, tabW, 0f)
            lineTo(4f, 0f)
            cubicTo(2f, 0f, 0f, 2f, 0f, 4f)
            close()
        }
        drawPath(folder, color = NotesColors.FolderYellow)
        drawPath(folder, color = NotesColors.Orange, style = Stroke(width = 1.dp.toPx()))
        // Top-edge highlight for depth
        drawLine(
            color = NotesColors.FolderHighlight,
            start = Offset(6f, top + 1f),
            end = Offset(w - 6f, top + 1f),
            strokeWidth = 0.5.dp.toPx(),
        )
    }
}
```

### Note Row (the three-line centerpiece)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.PushPin
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.scale
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun NoteRow(
    title: String,
    preview: String,
    date: String,
    isPinned: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by androidx.compose.animation.core.animateFloatAsState(
        if (pressed) 0.99f else 1f, label = "rowScale")
    val haptics = androidx.compose.ui.platform.LocalHapticFeedback.current

    Column(
        modifier = modifier
            .fillMaxWidth()
            .scale(scale)
            .background(if (isPinned) NotesColors.CreamSurf1 else NotesColors.Cream)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(
                    androidx.compose.ui.hapticfeedback.HapticFeedbackType.TextHandleMove) // ~iOS .selection
                onClick()
            },
    ) {
        Column(Modifier.padding(start = 16.dp, end = 16.dp, top = 16.dp).heightIn(min = 68.dp)) {
            Row(verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                if (isPinned) {
                    Icon(Icons.Filled.PushPin, contentDescription = "Pinned",
                        tint = NotesColors.Orange, modifier = Modifier.size(11.dp))
                }
                Text(title, style = NotesText.RowTitle, color = NotesColors.Ink,
                    maxLines = 1, overflow = TextOverflow.Ellipsis)
            }
            Text(preview, style = NotesText.RowPreview, color = NotesColors.Slate,
                maxLines = 1, overflow = TextOverflow.Ellipsis,
                modifier = Modifier.padding(top = 4.dp))
            Text(date, style = NotesText.RowDate, color = NotesColors.Slate,
                modifier = Modifier.padding(top = 2.dp))
        }
        Box(Modifier.fillMaxWidth().height(0.5.dp).background(NotesColors.DividerLight))
    }
}
```

Swipe actions (Pin / Add to Folder / Delete) use Material 3 `SwipeToDismissBox` with a custom background lane: Pin = `NotesColors.Orange`, Add to Folder = `Slate`, Delete = `Error`.

### Folder Row

```kotlin
import androidx.compose.material.icons.automirrored.filled.KeyboardArrowRight
import androidx.compose.material.icons.filled.AutoAwesome

@Composable
fun FolderRow(
    name: String,
    count: Int,
    isSmart: Boolean = false,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(44.dp)
            .background(NotesColors.Cream)
            .clickable(onClick = onClick)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        if (isSmart) {
            Icon(Icons.Filled.AutoAwesome, contentDescription = null,
                tint = NotesColors.Orange, modifier = Modifier.size(14.dp))
        }
        FolderGlyph(size = 24.dp)
        Text(name, style = NotesText.FolderRow, color = NotesColors.Ink,
            modifier = Modifier.weight(1f))
        Text("$count", style = NotesText.FolderRow, color = NotesColors.Slate)
        Icon(Icons.AutoMirrored.Filled.KeyboardArrowRight, contentDescription = null,
            tint = NotesColors.Slate, modifier = Modifier.size(16.dp))
    }
}
```

### New Note FAB (orange-tinted shadow)

The orange-tinted glow (`rgba(240,154,56,0.30) 0 4px 12px`) is non-negotiable brand — set the `Modifier.shadow` `spotColor`/`ambientColor` to Notes Orange, never neutral.

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.Edit
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import kotlinx.coroutines.launch

@Composable
fun NewNoteFab(onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()
    val scale = remember { Animatable(1f) }

    Box(
        modifier = modifier
            .size(56.dp)
            .scale(scale.value)
            .shadow(
                elevation = 12.dp,
                shape = CircleShape,
                spotColor = NotesColors.Orange.copy(alpha = 0.30f),   // orange-tinted, NOT neutral
                ambientColor = NotesColors.Orange.copy(alpha = 0.30f),
            )
            .clip(CircleShape)
            .background(NotesColors.Orange)
            .clickable {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS .medium impact
                scope.launch {
                    scale.animateTo(0.95f, androidx.compose.animation.core.tween(80))
                    scale.animateTo(1.05f, androidx.compose.animation.core.spring(
                        dampingRatio = 0.5f, stiffness = 500f))
                    scale.animateTo(1f, androidx.compose.animation.core.spring())
                }
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(Icons.Filled.Edit, contentDescription = "New note",
            tint = Color.White, modifier = Modifier.size(24.dp))
    }
}
```

### Primary CTA

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape

@Composable
fun NotesPrimaryButton(label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by androidx.compose.animation.core.animateFloatAsState(
        if (pressed) 0.98f else 1f, label = "ctaScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .fillMaxWidth()
            .heightIn(min = 44.dp)
            .scale(scale)
            .clip(RoundedCornerShape(12.dp))
            .background(if (pressed) NotesColors.OrangePressed else NotesColors.Orange)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS .light impact
                onClick()
            }
            .padding(horizontal = 24.dp, vertical = 12.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = NotesText.Button, color = Color.White)
    }
}
```

### Tag Chip

```kotlin
@Composable
fun TagChip(name: String, isSelected: Boolean, onClick: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    Row(
        modifier = Modifier
            .height(32.dp)
            .clip(RoundedCornerShape(16.dp))
            .background(if (isSelected) NotesColors.Orange else NotesColors.OrangeTint)
            .clickable {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS .selection
                onClick()
            }
            .padding(horizontal = 12.dp, vertical = 6.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Text("#", style = NotesText.TagChip,
            color = if (isSelected) Color.White else NotesColors.Orange)
        Text(name, style = NotesText.TagChip,
            color = if (isSelected) Color.White else NotesColors.Orange)
    }
}
```

### Search Bar

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.Mic
import androidx.compose.material.icons.filled.Search

@Composable
fun NotesSearchBar(value: String, onValueChange: (String) -> Unit) {
    var focused by remember { mutableStateOf(false) }
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .height(36.dp)
            .clip(RoundedCornerShape(10.dp))
            .background(NotesColors.CreamSurf1)
            .border(
                width = if (focused) 2.dp else 0.dp,
                color = if (focused) NotesColors.Orange else Color.Transparent,
                shape = RoundedCornerShape(10.dp),
            )
            .padding(horizontal = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(Icons.Filled.Search, contentDescription = null,
            tint = NotesColors.Slate, modifier = Modifier.size(14.dp))
        Box(Modifier.weight(1f)) {
            if (value.isEmpty()) {
                Text("Search", style = NotesText.SearchHint, color = NotesColors.Slate)
            }
            BasicTextField(
                value = value,
                onValueChange = onValueChange,
                textStyle = NotesText.Body.copy(color = NotesColors.Ink),
                singleLine = true,
                cursorBrush = androidx.compose.ui.graphics.SolidColor(NotesColors.Orange),
                modifier = Modifier.fillMaxWidth()
                    .onFocusChanged { focused = it.isFocused },
            )
        }
        Icon(Icons.Filled.Mic, contentDescription = "Voice search",
            tint = NotesColors.Slate, modifier = Modifier.size(14.dp))
    }
}
```

### Checklist Item

```kotlin
import androidx.compose.material.icons.filled.Check
import androidx.compose.ui.text.style.TextDecoration

@Composable
fun ChecklistItem(text: String, checked: Boolean, onToggle: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    val circleScale = remember { Animatable(if (checked) 1f else 0f) }
    LaunchedEffect(checked) {
        circleScale.animateTo(if (checked) 1f else 0f,
            androidx.compose.animation.core.tween(200))
    }
    val alpha by androidx.compose.animation.core.animateFloatAsState(
        if (checked) 0.6f else 1f,
        androidx.compose.animation.core.tween(300), label = "checkAlpha")

    Row(verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp)) {
        Box(
            Modifier
                .size(24.dp)
                .border(1.5.dp, NotesColors.Slate, CircleShape)
                .clickable(
                    interactionSource = remember { MutableInteractionSource() },
                    indication = null,
                ) {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS .success
                    onToggle()
                },
            contentAlignment = Alignment.Center,
        ) {
            Box(
                Modifier
                    .size(24.dp)
                    .scale(circleScale.value)
                    .clip(CircleShape)
                    .background(NotesColors.Orange),
                contentAlignment = Alignment.Center,
            ) {
                if (circleScale.value > 0.5f) {
                    Icon(Icons.Filled.Check, contentDescription = null,
                        tint = Color.White, modifier = Modifier.size(12.dp))
                }
            }
        }
        Text(
            text,
            style = NotesText.Body,
            color = if (checked) NotesColors.Slate else NotesColors.Ink,
            textDecoration = if (checked) TextDecoration.LineThrough else null,
            modifier = Modifier.androidx.compose.ui.draw.alpha(alpha),
        )
    }
}
```

### Pinned Card (horizontal scroll)

```kotlin
@Composable
fun PinnedCard(title: String, preview: String, onClick: () -> Unit) {
    Column(
        modifier = Modifier
            .size(width = 160.dp, height = 96.dp)
            .clip(RoundedCornerShape(10.dp))
            .background(NotesColors.CreamSurf1)
            .clickable(onClick = onClick)
            .padding(12.dp),
        verticalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Row(verticalAlignment = Alignment.Top) {
            Text(title, style = NotesText.RowTitle.copy(fontSize = 15.sp),
                color = NotesColors.Ink, maxLines = 1, overflow = TextOverflow.Ellipsis,
                modifier = Modifier.weight(1f))
            Icon(Icons.Filled.PushPin, contentDescription = "Pinned",
                tint = NotesColors.Orange, modifier = Modifier.size(11.dp))
        }
        Text(preview, style = NotesText.RowDate.copy(fontSize = 12.sp),
            color = NotesColors.Slate, maxLines = 3, overflow = TextOverflow.Ellipsis)
    }
}
```

## 4. The Note Editor Canvas (the paper)

Apple Notes' calmest, most distinctive surface: a chrome-free editable canvas — no card, no border, just the cream paper with the orange blinking cursor and 1.5-line-height body. iOS uses a `UITextView`; Compose uses a `BasicTextField` with no decoration.

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.ui.graphics.SolidColor
import androidx.compose.ui.text.input.TextFieldValue

@Composable
fun NoteEditor(
    title: TextFieldValue,
    body: TextFieldValue,
    onTitleChange: (TextFieldValue) -> Unit,
    onBodyChange: (TextFieldValue) -> Unit,
) {
    Column(
        Modifier
            .fillMaxSize()
            .background(NotesColors.Cream) // the canvas IS the paper — no card, no border
            .padding(horizontal = 20.dp, vertical = 16.dp),
    ) {
        BasicTextField(
            value = title,
            onValueChange = onTitleChange,
            textStyle = NotesText.BodyTitle.copy(color = NotesColors.Ink),
            cursorBrush = SolidColor(NotesColors.Orange), // orange cursor
            modifier = Modifier.fillMaxWidth(),
        )
        Spacer(Modifier.height(4.dp))
        BasicTextField(
            value = body,
            onValueChange = onBodyChange,
            textStyle = NotesText.Body.copy(color = NotesColors.Ink), // 17sp / 25sp line-height
            cursorBrush = SolidColor(NotesColors.Orange),
            modifier = Modifier.fillMaxSize(),
        )
    }
}
```

Compose's `BasicTextField` cursor blink (≈500ms) approximates iOS's 600ms orange cursor — the `SolidColor(NotesColors.Orange)` `cursorBrush` is the load-bearing detail. The system text-selection handles are tinted via `LocalTextSelectionColors` to `NotesColors.HighlightYellow` (the highlight color is reserved exclusively for selection — never a UI fill).

## 5. Navigation (no tab bar — hierarchical)

Apple Notes has **no bottom tab bar** — navigation is strictly hierarchical: Folders → Note list → Editor. Do **not** add a Material `NavigationBar`. Use Navigation Compose with a standard slide transition; tint every top-bar action Notes Orange.

```kotlin
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.compose.rememberNavController

@Composable
fun NotesApp() {
    val nav = rememberNavController()
    NavHost(navController = nav, startDestination = "folders") {
        composable("folders")  { FoldersScreen(onOpen = { nav.navigate("notes/$it") }) }
        composable("notes/{folderId}") { NotesListScreen(onOpen = { nav.navigate("editor/$it") }) }
        composable("editor/{noteId}")  { NoteEditorScreen() }
    }
}
```

iOS push/pop maps to `androidx.navigation` enter/exit transitions: `slideInHorizontally { it } + fadeIn()` / `slideOutHorizontally { -it / 4 } + fadeOut()`, ~350ms — the standard platform slide. The collapsing large title ("Notes" 34sp Heavy → 17sp inline) maps to `LargeTopAppBar` + `TopAppBarDefaults.exitUntilCollapsedScrollBehavior()`. When content scrolls under it, swap the bar container to a 92%-opaque cream `Surface` (no live blur on Android).

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Note row tap | `animateFloatAsState` 1 → 0.99; then nav `slideInHorizontally` editor (~350ms); `HapticFeedbackType.TextHandleMove` (~iOS `.selection`) |
| New Note FAB tap | `Animatable` 0.95 → 1.05 → 1 ~250ms `spring(dampingRatio = 0.5f)`; `HapticFeedbackType.LongPress` (~`.medium`) |
| Checklist toggle | circle `Animatable` scale 0 → 1 over 200ms, text strike + alpha 1 → 0.6 over 300ms; `HapticFeedbackType.LongPress` (~`.success`) |
| Pin / Unpin swipe | `SwipeToDismissBox` reveals Pin lane; `HapticFeedbackType.TextHandleMove`; row animates between sections ~400ms `spring` |
| Cursor blink | `BasicTextField` default (~500ms) ≈ iOS 600ms orange cursor |
| Search field focus | `animateDpAsState` border 0 → 2dp Orange; Cancel slides in `slideInHorizontally` ~250ms |
| Folder glyph tilt | static -2° `Modifier.rotate`; optional `SensorManager` parallax (clamp ≤5°) |

```kotlin
// FAB bounce — exact 0.95 → 1.05 → 1
@Composable
fun rememberFabBounce(): Pair<Animatable<Float, *>, suspend () -> Unit> {
    val scale = remember { Animatable(1f) }
    val play: suspend () -> Unit = {
        scale.animateTo(0.95f, tween(80))
        scale.animateTo(1.05f, spring(dampingRatio = 0.5f, stiffness = 500f))
        scale.animateTo(1f, spring())
    }
    return scale to play
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) — map `.light` (CTA) to `VibrationEffect.createOneShot(8, ...)`, `.medium` (FAB) to `VibrationEffect.createOneShot(15, ...)`, `.selection` (tag chip, row tap, pin swipe) to a short tick, and `.success` (checklist complete) to a double-buzz `VibrationEffect.createWaveform(longArrayOf(0, 40, 60, 40), -1)`, mirroring iOS `.notificationOccurred(.success)`.

## 7. Icons

Apple Notes uses SF Symbols; the closest first-party set is `androidx.compose.material:material-icons-extended`. The iconic **yellow folder glyph** and the **checklist circle** are hand-drawn (see §3) rather than icons. Ship any non-matching glyph as a vector drawable via `ImageVector.vectorResource(R.drawable.…)`.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| New Note FAB | `square.and.pencil` | `Icons.Filled.Edit` |
| Pin indicator | `pin.fill` | `Icons.Filled.PushPin` |
| Smart folder | `sparkles` | `Icons.Filled.AutoAwesome` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Voice search | `mic.fill` | `Icons.Filled.Mic` |
| Clear search | `xmark.circle.fill` | `Icons.Filled.Cancel` |
| Checklist (empty) | custom circle (1.5dp stroke) | custom composable (see §3) |
| Checklist (filled) | `checkmark` over filled circle | `Icons.Filled.Check` over circle |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| More menu | `ellipsis.circle` | `Icons.Filled.MoreHoriz` |
| Back chevron | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBackIos` |
| Row chevron | `chevron.right` | `Icons.AutoMirrored.Filled.KeyboardArrowRight` |
| Photo (toolbar) | `camera` | `Icons.Filled.PhotoCamera` |
| Checklist (toolbar) | `checklist` | `Icons.Filled.Checklist` |
| Table (toolbar) | `tablecells` | `Icons.Filled.TableChart` |
| Format (toolbar) | `textformat` | `Icons.Filled.TextFormat` |
| Lock | `lock.fill` | `Icons.Filled.Lock` |
| Folder glyph | — | custom `Canvas` path (see §3), yellow `#F5D773` + orange `#F09A38` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas` folder glyph + `BasicTextField` + Navigation Compose are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The cream canvas wants dark-content (dark icons) system bars; the dark-paper canvas wants light-content. Apply `Scaffold` insets / `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the FAB sits 16.dp above the gesture nav and the large title clears the status bar / display cutout.
- **Font scaling**: Notes is the most Dynamic-Type-sensitive app on the platform (older-demographic skew) — `sp` honors the user's font scale; keep it on note titles, body, preview, date, and folder names. The body must stay at `17sp × scale` with the 1.5 line-height intact at every size (the calmness scales up, never breaks). Even tag chips should scale (wrap horizontally rather than truncate). Only pin truly layout-locked text (11sp toolbar labels, 17sp search placeholder).
- **TalkBack**: read a note row as one node — `Modifier.semantics(mergeDescendants = true) { contentDescription = "$title, $preview, $date" }`; a folder row as `"$name folder, $count notes"`; a checklist item as `"${if (checked) "Checked" else "Unchecked"}: $text, double tap to toggle"`. The folder glyph is decorative — `contentDescription = null` (the row label carries meaning).
- **Touch targets**: Material guidance is 48.dp minimum. The note row (84dp) and FAB (56dp) are well clear; bump the 24.dp checklist circle, 32.dp tag chips, and 14–16.dp toolbar/chevron glyphs to a 48.dp hit box via padding.
- **Contrast**: Ink `#1C1C1E` on cream `#FFFBED` meets WCAG AAA. Slate `#8E8E93` on cream meets AA only at ≥18sp — for the 12sp date and 14sp preview, this is below AA; raise to Medium (500) weight if strict AA compliance is required (Apple ships it at Regular by design).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, skip the FAB scale bounce, the checklist circle-fill scale, and the folder-glyph parallax tilt — apply the end state instantly.
- **Locked notes**: implement biometric unlock with `androidx.biometric:biometric` (`BiometricPrompt`); fall back to device credential after 3 failed attempts. Render the locked row with a 24.dp lock glyph + "This note is locked." in `NotesText.RowPreview` italic instead of the preview.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — the cream `#FFFBED` paper canvas, Notes Orange `#F09A38`, and the iconic Folder Yellow `#F5D773` are signature cross-app Apple brand assets (Finder, iCloud Drive, Files share the same folder) and must not recolor to the user's wallpaper.
