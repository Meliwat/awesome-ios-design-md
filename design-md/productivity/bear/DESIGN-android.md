# Bear (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Bear's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the live-Markdown renderer, paste-ready `@Composable`s, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Bear's calm charcoal canvas, the single red→orange brand gradient, inline #hashtags as the filing system, live in-place Markdown) while making everything idiomatic Android — a `ModalNavigationDrawer` for the tag sidebar, a `FloatingActionButton` for compose, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` if you render embedded images. No color extraction — Bear's palette is fixed. Bear is theme-driven; the default dark "Charcoal" scheme is provided, never true black.

## 1. Color Tokens

```kotlin
// ui/theme/BearColors.kt
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.Brush

object BearColors {
    // Canvas & Surfaces (Light)
    val Canvas       = Color(0xFFFFFFFF)
    val SurfaceLight = Color(0xFFF6F6F4)
    val PressedLight = Color(0xFFECECE8)
    val DividerLight = Color(0xFFE6E6E2)

    // Canvas & Surfaces (Dark — "Charcoal")
    val CanvasDark = Color(0xFF21252B) // Bear's signature charcoal — NOT pure black
    val Surface1   = Color(0xFF282C34)
    val Surface2   = Color(0xFF2F343D)
    val Divider    = Color(0xFF353A42)

    // Text
    val TextPrimary    = Color(0xFFE8EAED) // dark
    val TextPrimaryLt  = Color(0xFF2A2C33) // light — warm near-black, NOT pure black
    val TextSecondary  = Color(0xFF9DA3AD)
    val TextTertiary   = Color(0xFF6B7280)

    // Brand (the single accent)
    val BearRed        = Color(0xFFE0566F)
    val BearOrange     = Color(0xFFFF8A65)
    val BearRedPressed = Color(0xFFC8485E)
    val Link           = Color(0xFF5AAFEF)

    // Tag / highlight accents
    val TagBlue   = Color(0xFF5AAFEF)
    val TagGreen  = Color(0xFF57C98B)
    val TagYellow = Color(0xFFF2C14E)
    val TagPurple = Color(0xFFB08CF0)
    val Highlight = Color(0xFF4A431F) // dark

    // Semantic
    val Error   = Color(0xFFF2545B)
    val Success = Color(0xFF57C98B)

    // The one brand gradient — icon, FAB, checkbox fill, selection
    val BrandGradient = Brush.linearGradient(listOf(BearRed, BearOrange))
}
```

Wire it into both schemes. Bear is theme-driven; the dark scheme uses the signature `#21252B` charcoal, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val BearLight = lightColorScheme(
    primary        = BearColors.BearOrange,   // interactive glyph color
    onPrimary      = Color(0xFFFFFFFF),
    background      = BearColors.Canvas,
    onBackground    = BearColors.TextPrimaryLt,
    surface         = BearColors.SurfaceLight,
    onSurface       = BearColors.TextPrimaryLt,
    surfaceVariant  = BearColors.PressedLight,
    outline         = BearColors.DividerLight,
    error           = BearColors.Error,
)

private val BearDark = darkColorScheme(
    primary        = BearColors.BearOrange,
    onPrimary      = Color(0xFFFFFFFF),
    background      = BearColors.CanvasDark,
    onBackground    = BearColors.TextPrimary,
    surface         = BearColors.Surface1,
    onSurface       = BearColors.TextPrimary,
    surfaceVariant  = BearColors.Surface2,
    outline         = BearColors.Divider,
    error           = BearColors.Error,
)

@Composable
fun BearTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) BearDark else BearLight,
    typography = BearTypography,
    content = content,
)
```

## 2. Typography

Bear's prose family is **user-switchable**: Inter (default sans), Lora (serif), JetBrains Mono (mono theme + all code) — all SIL OFL, drop the TTFs in `res/font/`. Respect the user's choice globally via a `CompositionLocal`; fenced code always renders JetBrains Mono. Body 400, headings 700; reading rhythm, not UI rhythm.

```kotlin
// ui/theme/BearType.kt
import androidx.compose.material3.Typography
import androidx.compose.runtime.compositionLocalOf
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
val Lora = FontFamily(
    Font(R.font.lora_regular, FontWeight.Normal),
    Font(R.font.lora_italic,  FontWeight.Normal, FontStyle.Italic),
    Font(R.font.lora_bold,    FontWeight.Bold),
)
val JetBrainsMono = FontFamily(
    Font(R.font.jetbrains_mono_regular, FontWeight.Normal),
    Font(R.font.jetbrains_mono_medium,  FontWeight.Medium),
)

enum class BearFontFamily(val family: FontFamily) {
    Sans(Inter), Serif(Lora), Mono(JetBrainsMono)
}
// Provide at the app root; switch from Settings — applies to all prose
val LocalBearFont = compositionLocalOf { BearFontFamily.Sans }

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1). Default = Inter.
object BearText {
    val LargeTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.6).sp)
    val NoteTitle  = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 24.sp, lineHeight = 30.sp, letterSpacing = (-0.3).sp)
    val H2         = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 19.sp, lineHeight = 25.sp, letterSpacing = (-0.2).sp)
    val H3         = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Subhead    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 17.sp, lineHeight = 23.sp)
    val Body       = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 25.sp)
    val BodyDense  = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 22.sp)
    val Quote      = TextStyle(Lora,  fontWeight = FontWeight.Normal,    fontSize = 17.sp, lineHeight = 26.sp, fontStyle = FontStyle.Italic)
    val Preview    = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Meta       = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Tag        = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 14.sp, lineHeight = 20.sp)
    val CodeInline = TextStyle(JetBrainsMono, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 20.sp)
    val CodeBlock  = TextStyle(JetBrainsMono, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 20.sp)
    val Caption    = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.1.sp)
    val TabLabel   = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 13.sp, lineHeight = 17.sp, letterSpacing = 0.1.sp)
}

// Helper: rebind a base style onto the user's chosen prose family
fun TextStyle.withBearFamily(f: BearFontFamily): TextStyle = copy(fontFamily = f.family)

// Map onto Material 3 slots so stock components inherit the brand
val BearTypography = Typography(
    headlineLarge = BearText.LargeTitle,
    headlineMedium = BearText.NoteTitle,
    titleMedium   = BearText.Subhead,
    bodyMedium    = BearText.Body,
    labelSmall    = BearText.TabLabel,
)
```

## 3. Signature Components

### Live Markdown Renderer

Bear's signature: render Markdown in-place using an `AnnotatedString`, dimming the syntax marker rather than hiding structure.

```kotlin
import androidx.compose.foundation.text.appendInlineContent
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.text.AnnotatedString
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.withStyle

@Composable
fun MarkdownLine(raw: String) {
    when {
        raw.startsWith("# ")  -> Text(buildAnnotatedString {
            withStyle(SpanStyle(color = BearColors.TextTertiary)) { append("# ") }
            append(raw.drop(2))
        }, style = BearText.NoteTitle, color = BearColors.TextPrimary)

        raw.startsWith("## ") -> Text(buildAnnotatedString {
            withStyle(SpanStyle(color = BearColors.TextTertiary)) { append("## ") }
            append(raw.drop(3))
        }, style = BearText.H2, color = BearColors.TextPrimary)

        raw.startsWith("> ")  -> QuoteBlock(raw.drop(2))
        raw.startsWith("- [") -> CheckboxRow(
            initiallyChecked = raw.startsWith("- [x]"),
            label = raw.replace(Regex("^- \\[.\\]\\s*"), ""),
        )
        else -> Text(inlineStyled(raw), style = BearText.Body, color = BearColors.TextPrimary)
    }
}

/** Tints #hashtags red and [[wiki]] links blue inline. */
fun inlineStyled(s: String): AnnotatedString = buildAnnotatedString {
    val token = Regex("#[\\w/]+|\\[\\[[^\\]]+\\]\\]")
    var last = 0
    token.findAll(s).forEach { m ->
        append(s.substring(last, m.range.first))
        val t = m.value
        val style = if (t.startsWith("#")) SpanStyle(color = BearColors.BearRed)
                    else SpanStyle(color = BearColors.Link)
        withStyle(style) { append(t) }
        last = m.range.last + 1
    }
    append(s.substring(last))
}
```

### Note Editor (Title + Body)

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material3.Text
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

@Composable
fun NoteEditor(title: String, onTitleChange: (String) -> Unit, lines: List<String>) {
    LazyColumn(
        Modifier.fillMaxSize(),
        contentPadding = PaddingValues(horizontal = 22.dp, vertical = 4.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        item {
            BasicTextField(
                value = title, onValueChange = onTitleChange,
                textStyle = BearText.NoteTitle.copy(color = BearColors.TextPrimary),
                decorationBox = { inner ->
                    if (title.isEmpty()) Text("Title", style = BearText.NoteTitle, color = BearColors.TextTertiary)
                    inner()
                },
            )
        }
        items(lines) { MarkdownLine(it) }
    }
}
```

### Checkbox Row

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.fadeIn
import androidx.compose.animation.fadeOut
import androidx.compose.animation.core.tween
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.clip
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.style.TextDecoration

@Composable
fun CheckboxRow(initiallyChecked: Boolean, label: String) {
    var checked by remember { mutableStateOf(initiallyChecked) }
    val haptics = LocalHapticFeedback.current

    Row(
        Modifier
            .fillMaxWidth()
            .clickable {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                checked = !checked
            },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box(
            Modifier
                .size(19.dp)
                .clip(RoundedCornerShape(5.dp))
                .then(if (!checked) Modifier.border(2.dp, BearColors.TextTertiary, RoundedCornerShape(5.dp)) else Modifier),
            contentAlignment = Alignment.Center,
        ) {
            AnimatedVisibility(checked, enter = fadeIn(tween(150)), exit = fadeOut(tween(150))) {
                Box(Modifier.matchParentSize().background(BearColors.BrandGradient), contentAlignment = Alignment.Center) {
                    Icon(Icons.Filled.Check, null, tint = Color(0xFFFFFFFF), modifier = Modifier.size(11.dp))
                }
            }
        }
        Text(
            label,
            style = BearText.Body,
            color = if (checked) BearColors.TextTertiary else BearColors.TextPrimary,
            textDecoration = if (checked) TextDecoration.LineThrough else TextDecoration.None,
        )
    }
}
```

### Quote Block

```kotlin
@Composable
fun QuoteBlock(text: String) {
    Row(Modifier.fillMaxWidth()) {
        Box(Modifier.width(3.dp).fillMaxHeight().background(BearColors.BearOrange))
        Text(
            text,
            style = BearText.Quote,
            color = BearColors.TextSecondary,
            modifier = Modifier.padding(start = 14.dp, top = 2.dp, bottom = 2.dp),
        )
    }
}
```

### Code Block

```kotlin
import androidx.compose.foundation.horizontalScroll
import androidx.compose.foundation.rememberScrollState

@Composable
fun CodeBlock(language: String?, code: String) {
    Column(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(8.dp))
            .background(BearColors.Surface1),
    ) {
        if (language != null) {
            Text(
                language, style = BearText.Caption, color = BearColors.TextTertiary,
                modifier = Modifier.fillMaxWidth().padding(horizontal = 14.dp, top = 8.dp),
            )
        }
        Text(
            code,
            style = BearText.CodeBlock,
            color = BearColors.TextPrimary,
            modifier = Modifier.horizontalScroll(rememberScrollState()).padding(14.dp),
        )
    }
}
```

### Tag Sidebar Row + Compose FAB

```kotlin
import androidx.compose.material.icons.filled.Tag
import androidx.compose.material.icons.filled.Edit
import androidx.compose.material3.FloatingActionButton

@Composable
fun TagRow(title: String, indent: Int = 0, active: Boolean = false) {
    Row(
        Modifier
            .fillMaxWidth()
            .height(36.dp)
            .background(if (active) BearColors.Surface2 else Color.Transparent)
            .padding(start = (14 + indent * 18).dp, end = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(Icons.Filled.Tag, null, tint = BearColors.BearRed, modifier = Modifier.size(16.dp))
        Text(title, style = BearText.Tag, color = BearColors.TextPrimary)
    }
}

@Composable
fun ComposeFab(onClick: () -> Unit) {
    FloatingActionButton(
        onClick = onClick,
        containerColor = Color.Transparent,
        contentColor = Color(0xFFFFFFFF),
        modifier = Modifier
            .size(56.dp)
            .clip(RoundedCornerShape(28.dp))
            .background(BearColors.BrandGradient),
    ) {
        Icon(Icons.Filled.Edit, contentDescription = "New note", modifier = Modifier.size(24.dp))
    }
}
```

## 4. Navigation

Bear has minimal chrome: a slide-in tag sidebar and a bottom info bar (no Material tab bar). Model the sidebar as a `ModalNavigationDrawer` (`SurfaceGray`, 280.dp) and the editor's bottom strip as a custom `BottomAppBar`. There is no tint pill — interactive glyphs are Bear Orange.

```kotlin
import androidx.compose.material3.BottomAppBar
import androidx.compose.material.icons.filled.*

@Composable
fun EditorInfoBar(words: Int, chars: Int) {
    BottomAppBar(
        containerColor = BearColors.Surface1.copy(alpha = 0.92f),
        tonalElevation = 0.dp,
    ) {
        Text(
            "$words words · $chars chars",
            style = BearText.Caption,
            color = BearColors.TextTertiary,
            modifier = Modifier.padding(start = 22.dp),
        )
        Spacer(Modifier.weight(1f))
        Row(horizontalArrangement = Arrangement.spacedBy(22.dp), modifier = Modifier.padding(end = 22.dp)) {
            listOf(Icons.Filled.GridOn, Icons.Filled.Visibility, Icons.Filled.IosShare, Icons.Filled.Info)
                .forEach { Icon(it, null, tint = BearColors.BearOrange, modifier = Modifier.size(20.dp)) }
        }
    }
}
```

The tag sidebar (`ModalDrawerSheet`, 280.dp, `Surface1` background) holds smart filters (Notes, Untagged, Todo, Today, Archive, Trash) as 40.dp rows, then the #hashtag tree — each tag row 36.dp with a leading 16.dp `Tag` glyph tinted `BearRed`, nested children indented 18.dp behind a disclosure chevron. Active row uses `Surface2`. Material's `drawerState` gives the edge-swipe + 250ms open/close for free.

## 5. Motion

Bear motion is quiet — 120–300ms ease-out, never aggressive. The Markdown marker fade never causes a layout jump.

| Moment | Compose recipe |
|--------|----------------|
| Checkbox toggle | `AnimatedVisibility` of the gradient fill `fadeIn(tween(150))`; label `TextDecoration.LineThrough` flips with it |
| Markdown marker fade | recompose `AnnotatedString`; animate marker `SpanStyle` alpha via `animateColorAsState(tween(120))` — opacity only |
| Tag tap → list filter | swap list state inside `Crossfade(tween(200))` |
| Nested tag disclosure | chevron `animateFloatAsState` 0 → 90° `tween(150)`; children `expandVertically(tween(200)) + fadeIn` |
| Compose FAB | press `scale` `animateFloatAsState` → 0.92; then nav push `tween(280)` |
| Page navigation | Nav `NavHost` slide push `tween(300)` |
| Sidebar swipe | `ModalNavigationDrawer` `drawerState` — 1:1 drag, 50% threshold, `spring(dampingRatio = 0.8f)` |
| Text selection | brand-red @ ~15% overlay appears instantly (no animation) |

```kotlin
// Checkbox fill — the canonical Bear toggle
AnimatedVisibility(
    visible = checked,
    enter = fadeIn(tween(150)),
    exit = fadeOut(tween(150)),
) { /* gradient box + check */ }
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on checkbox toggle, tag tap, and FAB press. Use `HapticFeedbackConstants.CONFIRM` for export-complete. Auto-save is silent — no motion, no haptic; only a "Saved" snackbar on manual save or error.

## 6. Icons

Bear ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. The #hashtag glyph maps to `Tag`; the page-emoji concept does not exist in Bear (it is text-only Markdown).

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Back ("Notes") | `chevron.left` | `Icons.Filled.ArrowBack` |
| Info (top nav) | `info.circle` | `Icons.Filled.Info` |
| Format / Markdown | `textformat` | `Icons.Filled.TextFields` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Compose FAB | `square.and.pencil` | `Icons.Filled.Edit` |
| Checkbox (checked) | `checkmark` | `Icons.Filled.Check` |
| Tag / #hashtag | `number` | `Icons.Filled.Tag` |
| Smart filter — Notes | `note.text` | `Icons.Filled.Description` |
| Smart filter — Todo | `checkmark.circle` | `Icons.Filled.CheckCircle` |
| Smart filter — Today | `calendar` | `Icons.Filled.CalendarToday` |
| Smart filter — Archive | `archivebox` | `Icons.Filled.Archive` |
| Smart filter — Trash | `trash` | `Icons.Filled.Delete` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Info-bar — Table | `tablecells` | `Icons.Filled.GridOn` |
| Info-bar — Preview | `eye` | `Icons.Filled.Visibility` |
| Info-bar — Export | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Disclosure (nested tag) | `chevron.right` | `Icons.Filled.ChevronRight` (rotate 90°) |
| Pin note | `pin` | `Icons.Filled.PushPin` |
| Link / wiki-link | `link` | `Icons.Filled.Link` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the dark "Charcoal" canvas wants light-content system bars (dark-content on the light theme). The editor must keep the keyboard accessory row visible — use `Modifier.imePadding()` on the editor `LazyColumn`.
- **Font scaling**: `sp` honors the user's font scale — keep it on note title, headings, body, quote, preview, meta, code (it stays monospace as it scales). Pin layout-sensitive text (tag tokens, 12sp info-bar caption, sidebar filter labels, 13sp tab labels) via `dp`-derived sizes or a fixed-`fontScale` `LocalDensity`.
- **User font choice**: read `LocalBearFont` everywhere for prose and apply `style.withBearFamily(...)` — never hard-code Inter; Lora/JetBrains Mono must apply globally. Fenced code is always JetBrains Mono.
- **TalkBack**: label checkbox rows with `Modifier.semantics { role = Role.Checkbox; stateDescription = if (checked) "Completed" else "Not completed" }`; label #hashtag spans "Tag: {name}, double-tap to filter" via `ClickableText` + `LinkAnnotation`; mark note titles as `heading()`. The marker dim is visual only — expose the styled text, not the raw `#`/`**`.
- **Touch targets**: Material guidance is 48.dp. Give the 19.dp checkbox, 16.dp tag glyph, and 14.dp disclosure chevron a 48.dp hit area via padding; sidebar rows are 36.dp tall but full-row tappable; the FAB is 56.dp.
- **Contrast**: `#2A2C33` on `#FFFFFF` and `#E8EAED` on `#21252B` pass WCAG AA for body at 16sp; `#E0566F` hashtags pass AA on canvas. Validate any custom tag color against its surface.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the disclosure rotation and checkbox cross-fade — substitute an instant state change; keep the selection highlight (it conveys state).
- **Dark mode**: invert via the `Dark*` palette — `#21252B`, NOT true black; `#2A2C33` text becomes `#E8EAED`. Shadows are nearly invisible on dark, so floating panels add a 1dp `Divider` border as the elevation cue. Do **not** enable Material You `dynamicColorScheme()` — Bear's calm-canvas + single-red-gradient identity must hold regardless of wallpaper. Model each shipped Bear theme as a token object; the brand gradient is the one constant across all of them.
