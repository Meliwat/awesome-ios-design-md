# Perplexity (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Perplexity's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the triple-circle brand mark, inline citation chips, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Perplexity's near-black research-notebook canvas, the single cyan-teal accent, inline citations + source cards, the triple-circle mark) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Path`-drawn mark instead of a SwiftUI `Shape`, `LazyRow` source cards, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for source-card favicons and Discover hero images. No color extraction — the accent is a fixed teal scale, so Palette is not needed. Perplexity is dark-first; a light scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/PerplexityColors.kt
import androidx.compose.ui.graphics.Color

object PerplexityColors {
    // Canvas (dark-first)
    val Canvas   = Color(0xFF0A0A0A) // near-black — every screen by default
    val Surface1 = Color(0xFF171717) // input field, source card, modal
    val Surface2 = Color(0xFF1F1F1F) // chips, pressed
    val Surface3 = Color(0xFF2A2A2A) // sheets, sticky headers
    val Divider  = Color(0xFF2A2A2A)

    // Text (on dark)
    val TextPrimary   = Color(0xFFFAFAFA) // off-white — NOT pure white
    val TextSecondary = Color(0xFFA1A1A1)
    val TextTertiary  = Color(0xFF6E6E6E)
    val TextMuted     = Color(0xFF4A4A4A)

    // Perplexity Teal (the single signature accent)
    val Teal       = Color(0xFF20B8CD)
    val TealBright = Color(0xFF3DD6EC) // streaming cursor, hover
    val TealDeep   = Color(0xFF1591A3) // pressed, light-mode teal
    val TealSoft   = Color(0xFF0F3A42) // Pro Steps fill, focused chip bg

    // Code & syntax
    val CodeBg     = Color(0xFF0E0E0E)
    val CodeFg     = Color(0xFFE5E5E5)
    val SyntaxStr  = Color(0xFFA8E063) // lime
    val SyntaxNum  = Color(0xFFF2A65A) // soft orange
    val SyntaxFunc = Color(0xFFB988F2) // soft purple

    // Semantic
    val Success = Color(0xFF22C55E)
    val Warning = Color(0xFFF59E0B)
    val Error   = Color(0xFFEF4444)
    val ProGold = Color(0xFFE0B341)

    // Light mode (supported, but Perplexity defaults dark)
    val LightCanvas    = Color(0xFFFFFFFF)
    val LightSurface1  = Color(0xFFF7F7F7)
    val LightSurface2  = Color(0xFFEFEFEF)
    val LightDivider   = Color(0xFFE5E5E5)
    val LightTextPri   = Color(0xFF111111)
    val LightTextSec   = Color(0xFF555555)
    val TealLight      = Color(0xFF1591A3) // darker teal for WCAG AA on white
}
```

Wire it into both schemes. Perplexity is dark-first; the light scheme preserves all teal accents but darkens the teal to `#1591A3` for WCAG on white.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val PerplexityDark = darkColorScheme(
    primary        = PerplexityColors.Teal,
    onPrimary      = PerplexityColors.Canvas,  // intentional: dark text on teal (WCAG)
    background     = PerplexityColors.Canvas,
    onBackground   = PerplexityColors.TextPrimary,
    surface        = PerplexityColors.Surface1,
    onSurface      = PerplexityColors.TextPrimary,
    surfaceVariant = PerplexityColors.Surface2,
    outline        = PerplexityColors.Divider,
    error          = PerplexityColors.Error,
)

private val PerplexityLight = lightColorScheme(
    primary        = PerplexityColors.TealLight,
    onPrimary      = PerplexityColors.LightCanvas,
    background     = PerplexityColors.LightCanvas,
    onBackground   = PerplexityColors.LightTextPri,
    surface        = PerplexityColors.LightSurface1,
    onSurface      = PerplexityColors.LightTextPri,
    surfaceVariant = PerplexityColors.LightSurface2,
    outline        = PerplexityColors.LightDivider,
    error          = PerplexityColors.Error,
)

@Composable
fun PerplexityTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) PerplexityDark else PerplexityLight,
    typography = PerplexityTypography,
    content = content,
)
```

## 2. Typography

Perplexity is a two-face system: **FK Grotesk Neue** (Florian Karsten — chrome/brand voice) and **Inter** (answer body). Drop both in `res/font/`; FK Grotesk Mono drives citation numerals. Fall back to **Inter** for both (closest geometric-humanist substitute that ships everywhere). Code uses JetBrains Mono.

```kotlin
// ui/theme/PerplexityType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val FKGrotesk = FontFamily(
    Font(R.font.fk_grotesk_neue_regular,  FontWeight.Normal),
    Font(R.font.fk_grotesk_neue_medium,   FontWeight.Medium),
    Font(R.font.fk_grotesk_neue_semibold, FontWeight.SemiBold),
    Font(R.font.fk_grotesk_neue_bold,     FontWeight.Bold),
)
val FKGroteskMono = FontFamily(Font(R.font.fk_grotesk_mono_medium, FontWeight.Medium))
val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
    Font(R.font.inter_bold,     FontWeight.Bold),
)
val JetBrainsMono = FontFamily(
    Font(R.font.jetbrains_mono_regular, FontWeight.Normal),
    Font(R.font.jetbrains_mono_medium,  FontWeight.Medium),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object PerplexityText {
    val Display     = TextStyle(FKGrotesk, fontWeight = FontWeight.Bold,     fontSize = 30.sp, lineHeight = 35.sp, letterSpacing = (-0.5).sp)
    val Question    = TextStyle(FKGrotesk, fontWeight = FontWeight.SemiBold, fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(FKGrotesk, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Subsection  = TextStyle(FKGrotesk, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 21.sp)
    val Body        = TextStyle(Inter,     fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp) // 1.5 — denser than Claude
    val BodyBold    = TextStyle(Inter,     fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 24.sp)
    val ListItem    = TextStyle(Inter,     fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val SourceTitle = TextStyle(Inter,     fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 17.sp)
    val SourceDom   = TextStyle(Inter,     fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 14.sp)
    val Citation    = TextStyle(FKGroteskMono, fontWeight = FontWeight.Medium, fontSize = 11.sp, lineHeight = 11.sp)
    val SearchHint  = TextStyle(Inter,     fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp)
    val Button      = TextStyle(FKGrotesk, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 15.sp)
    val ProBadge    = TextStyle(FKGrotesk, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
    val Tab         = TextStyle(FKGrotesk, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp)
    val Meta        = TextStyle(Inter,     fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Caption     = TextStyle(Inter,     fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 14.sp)
    val SenderLabel = TextStyle(FKGrotesk, fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 17.sp)
    val GroupHeader = TextStyle(FKGrotesk, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
    val CodeBlock   = TextStyle(JetBrainsMono, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 21.sp)
    val CodeInline  = TextStyle(JetBrainsMono, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 20.sp)
    val CodeLang    = TextStyle(JetBrainsMono, fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val PerplexityTypography = Typography(
    headlineLarge = PerplexityText.Display,
    titleLarge    = PerplexityText.Section,
    titleMedium   = PerplexityText.Subsection,
    bodyMedium    = PerplexityText.Body,
    labelSmall    = PerplexityText.Tab,
)
```

## 3. Signature Components

### The Triple-Circle Brand Mark

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp
import kotlin.math.cos
import kotlin.math.sin

@Composable
fun PerplexityMark(
    size: Dp = 20.dp,
    color: Color = PerplexityColors.Teal, // NEVER any color outside the teal scale
    modifier: Modifier = Modifier,
) {
    Canvas(modifier.size(size)) {
        val c = Offset(this.size.width / 2f, this.size.height / 2f)
        val r = minOf(this.size.width, this.size.height) * 0.32f
        val d = r * 0.65f
        // Three overlapping circles in a triangular formation
        val angles = floatArrayOf(-Math.PI.toFloat() / 2f, Math.PI.toFloat() / 6f, 5f * Math.PI.toFloat() / 6f)
        angles.forEach { a ->
            drawCircle(
                color = color,
                radius = r,
                center = Offset(c.x + d * cos(a), c.y + d * sin(a)),
                style = Stroke(width = r * 0.5f),
            )
        }
    }
}
```

### Search Input (the home of the app, with teal focus glow)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material.icons.filled.AttachFile
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.clip
import androidx.compose.ui.focus.onFocusChanged
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun SearchInput(
    text: String,
    onTextChange: (String) -> Unit,
    onSubmit: () -> Unit,
    onAttach: () -> Unit,
    modifier: Modifier = Modifier,
) {
    var focused by remember { mutableStateOf(false) }
    val canSend = text.trim().isNotEmpty()
    val haptics = LocalHapticFeedback.current

    Row(
        modifier = modifier
            .fillMaxWidth()
            // Teal focus glow — the only "glow" in the app (Android has no live blur; a teal
            // halo border is the analog of the iOS rgba(32,184,205,0.25) 4pt ring)
            .then(if (focused) Modifier.border(4.dp, PerplexityColors.Teal.copy(alpha = 0.25f), RoundedCornerShape(28.dp)) else Modifier)
            .clip(RoundedCornerShape(24.dp))
            .background(PerplexityColors.Surface1)
            .border(
                width = if (focused) 1.5.dp else 1.dp,
                color = if (focused) PerplexityColors.Teal else PerplexityColors.Surface3,
                shape = RoundedCornerShape(24.dp),
            )
            .padding(horizontal = 18.dp),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        BasicTextField(
            value = text,
            onValueChange = onTextChange,
            textStyle = PerplexityText.SearchHint.copy(color = PerplexityColors.TextPrimary),
            cursorBrush = androidx.compose.ui.graphics.SolidColor(PerplexityColors.Teal),
            modifier = Modifier
                .weight(1f)
                .padding(vertical = 14.dp)
                .onFocusChanged { focused = it.isFocused },
            maxLines = 8,
            decorationBox = { inner ->
                if (text.isEmpty()) Text("Ask anything…", style = PerplexityText.SearchHint, color = PerplexityColors.TextTertiary)
                inner()
            },
        )
        Icon(
            Icons.Filled.AttachFile, contentDescription = "Attach",
            tint = PerplexityColors.TextSecondary,
            modifier = Modifier.padding(bottom = 14.dp).size(18.dp)
                .clickable(MutableInteractionSource(), indication = null, onClick = onAttach),
        )
        Box(
            Modifier
                .padding(bottom = 10.dp)
                .size(36.dp)
                .clip(CircleShape)
                .background(if (canSend) PerplexityColors.Teal else PerplexityColors.Surface3)
                .clickable(enabled = canSend) {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress) // .medium
                    onSubmit()
                },
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                Icons.Filled.ArrowUpward,
                contentDescription = "Send",
                tint = if (canSend) PerplexityColors.Canvas else PerplexityColors.TextTertiary, // dark on teal
                modifier = Modifier.size(14.dp),
            )
        }
    }
}
```

### Source Card (horizontal scroll above every answer)

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.itemsIndexed
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

data class Source(val number: Int, val faviconUrl: String?, val domain: String, val title: String)

@Composable
fun SourceCard(source: Source, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Column(
        modifier
            .width(200.dp)
            .height(80.dp)
            .clip(RoundedCornerShape(12.dp))
            .background(PerplexityColors.Surface1)
            .border(1.dp, PerplexityColors.Surface3, RoundedCornerShape(12.dp))
            .clickable(onClick = onClick)
            .padding(12.dp),
        verticalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            AsyncImage(
                model = source.faviconUrl,
                contentDescription = null,
                modifier = Modifier.size(16.dp).clip(RoundedCornerShape(3.dp)),
                contentScale = ContentScale.Crop,
            )
            Text(source.domain, style = PerplexityText.SourceDom, color = PerplexityColors.TextSecondary, modifier = Modifier.weight(1f), maxLines = 1)
            Box(
                Modifier.clip(RoundedCornerShape(3.dp)).background(PerplexityColors.Surface2).padding(horizontal = 5.dp, vertical = 1.dp),
            ) {
                Text("${source.number}", style = PerplexityText.Citation.copy(fontWeight = FontWeight.Bold, fontSize = 10.sp), color = PerplexityColors.TextSecondary)
            }
        }
        Text(source.title, style = PerplexityText.SourceTitle, color = PerplexityColors.TextPrimary, maxLines = 2)
    }
}

@Composable
fun SourceCardRow(sources: List<Source>, onSource: (Source) -> Unit, onShowAll: () -> Unit) {
    LazyRow(
        contentPadding = PaddingValues(horizontal = 16.dp),
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        itemsIndexed(sources) { _, s -> SourceCard(s, { onSource(s) }) }
        item {
            Box(
                Modifier
                    .width(120.dp).height(80.dp)
                    .clip(RoundedCornerShape(12.dp))
                    .background(PerplexityColors.Surface2)
                    .clickable(onClick = onShowAll),
                contentAlignment = Alignment.Center,
            ) {
                Text("Show all ${sources.size} sources", style = PerplexityText.SourceDom, color = PerplexityColors.TextSecondary)
            }
        }
    }
}
```

## 4. Inline Citation Chips + Source-Card Scroll-To

Perplexity's defining interaction: numbered `[1]` chips typeset *inline* within the answer prose (never a footer), each tapping through to its matching source card with a teal flash. iOS uses `ScrollViewReader.scrollTo`; Android uses a `LazyListState`/scroll-to-index plus an `Animatable` border flash on the target card.

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween

@Composable
fun CitationChip(
    number: Int,
    onTap: () -> Unit,
    modifier: Modifier = Modifier,
) {
    var focused by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current

    Box(
        modifier
            .padding(2.dp) // expands toward a ~32dp tap target
            .clip(RoundedCornerShape(4.dp))
            .background(if (focused) PerplexityColors.TealSoft else PerplexityColors.Surface2)
            .border(1.dp, if (focused) PerplexityColors.Teal else PerplexityColors.Surface3, RoundedCornerShape(4.dp))
            .clickable {
                focused = true
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // .soft
                onTap()
            }
            .padding(horizontal = 5.dp)
            .heightIn(min = 18.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(
            "$number",
            style = PerplexityText.Citation,
            color = if (focused) PerplexityColors.Teal else PerplexityColors.TextSecondary,
        )
    }
    // Caller resets `focused` ~300ms after the scroll completes
}

// Render answer prose with tappable chips: split markdown into text runs + [n] tokens and
// emit a Text(...) interleaved with CitationChip(...) inside a FlowRow so they wrap inline.
@Composable
fun rememberCitationScroll(): Pair<LazyListState, suspend (Int) -> Unit> {
    val state = androidx.compose.foundation.lazy.rememberLazyListState()
    val flash = remember { Animatable(0f) }
    val scrollTo: suspend (Int) -> Unit = { index ->
        state.animateScrollToItem(index) // 400ms-feel ease handled by LazyList
        flash.snapTo(1f); flash.animateTo(0f, tween(200)) // 200ms teal border flash on arrival
    }
    return state to scrollTo
}
```

The streaming answer ends with a teal caret: a `6.dp × 16.dp` `TealBright` box blinking on a `rememberInfiniteTransition` at a 500ms interval (faster than Claude's 600ms), removed when the answer completes. The "Searching the web…" indicator (3 teal dots, 200ms stagger, 1200ms cycle, `SenderLabel` text trailing) shows immediately on submit and is replaced by `SourceCardRow`.

## 5. Navigation

Perplexity has a 4-tab bottom bar (Home / Discover / Library / Spaces). Use Material 3 `NavigationBar`; the iOS bar matches the canvas with **no blur on scroll**, so an opaque `Canvas` surface is exact (no translucency needed here). Active is off-white `#FAFAFA` with a teal indicator dot under the label; inactive `#6E6E6E`.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun PerplexityBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    val haptics = LocalHapticFeedback.current
    NavigationBar(
        containerColor = PerplexityColors.Canvas,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"     to Icons.Filled.Search,
            "Discover" to Icons.Filled.Explore,
            "Library"  to Icons.Filled.Bookmarks,
            "Spaces"   to Icons.Filled.Workspaces,
        )
        items.forEachIndexed { i, (label, icon) ->
            val active = selected == i
            NavigationBarItem(
                selected = active,
                onClick = {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // .selection
                    onSelect(i)
                },
                icon = {
                    Column(horizontalAlignment = Alignment.CenterHorizontally) {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp))
                        Spacer(Modifier.height(4.dp))
                        Box(
                            Modifier.size(width = 16.dp, height = 2.dp)
                                .background(if (active) PerplexityColors.Teal else Color.Transparent),
                        )
                    }
                },
                label = { Text(label, style = PerplexityText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = PerplexityColors.TextPrimary,   // off-white
                    selectedTextColor = PerplexityColors.TextPrimary,
                    unselectedIconColor = PerplexityColors.TextTertiary, // #6E6E6E
                    unselectedTextColor = PerplexityColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Perplexity uses a teal dot
                ),
            )
        }
    }
}
```

The conversation-history sidebar is a `ModalNavigationDrawer` (`ModalDrawerSheet`, `Canvas` background) with a full-width "+ New thread" button (`Surface1` fill, 1.dp `Surface3` border, 10.dp radius) followed by date-grouped thread rows (`GroupHeader` uppercase headers, 56.dp rows with `Inter` title + timestamp).

## 6. Motion

Perplexity motion is live and information-paced — slightly faster than Claude, restrained but responsive.

| Moment | Compose recipe |
|--------|----------------|
| Send tap | circle scale 1 → 0.94 → 1 `tween(200)` + `HapticFeedbackType.LongPress` (medium); question slides up, "Searching…" fades in `tween(250)` |
| Source cards appear | `LazyRow` items `animateItemPlacement` + per-item `slideInVertically(tween(350))` with 60ms stagger (cascade) |
| Citation → source | `LazyListState.animateScrollToItem` + target card `Animatable` border flash `tween(200)` teal |
| Streaming text | word-by-word `AnimatedVisibility`/`fadeIn` at ~60ms per word |
| Streaming cursor | `rememberInfiniteTransition` alpha 1 → 0, 500ms |
| Pro toggle | indicator `animateDpAsState` slide `tween(200)`, label color cross-fade, `.selection` haptic |
| Pro Steps expand | `AnimatedVisibility(expandVertically(tween(300)))`, 30ms per-step stagger |
| Tab switch | teal dot `fadeIn` `tween(200)`, `.selection` haptic |

```kotlin
// "Searching the web…" — three teal dots, 200ms stagger, 1200ms cycle
@Composable
fun SearchingIndicator() {
    val t = rememberInfiniteTransition(label = "searching")
    Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
        Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            repeat(3) { i ->
                val s by t.animateFloat(
                    1f, 1.3f,
                    infiniteRepeatable(tween(600, delayMillis = i * 200), RepeatMode.Reverse),
                    label = "dot$i",
                )
                Box(
                    Modifier.size(6.dp).graphicsLayer { scaleX = s; scaleY = s }
                        .clip(CircleShape).background(PerplexityColors.Teal),
                )
            }
        }
        Text("Searching the web…", style = PerplexityText.SenderLabel, color = PerplexityColors.TextSecondary)
    }
}
```

Haptics: prefer `LocalHapticFeedback`. Use `HapticFeedbackType.LongPress` for the medium send impact and `HapticFeedbackType.TextHandleMove` for the soft citation tap; for finer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` or a `Vibrator` `VibrationEffect.createOneShot(8, ...)`.

## 7. Icons

Perplexity ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. The triple-circle mark is NOT an icon — draw it via `Canvas` (§3) and never tint it outside the teal scale.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Send | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Attach | `paperclip` | `Icons.Filled.AttachFile` |
| Copy | `doc.on.doc` | `Icons.Filled.ContentCopy` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Save | `bookmark` / `bookmark.fill` | `Icons.Outlined.BookmarkBorder` / `Icons.Filled.Bookmark` |
| Pro sparkles | `sparkles` | `Icons.Filled.AutoAwesome` |
| Step check | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Chevron up/down | `chevron.up` / `chevron.down` | `Icons.Filled.KeyboardArrowUp` / `KeyboardArrowDown` |
| Related arrow | `arrow.right` | `Icons.Filled.ArrowForward` |
| Home (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Discover (tab) | `safari` | `Icons.Filled.Explore` |
| Library (tab) | `books.vertical` | `Icons.Filled.Bookmarks` |
| Spaces (tab) | `square.stack.3d.down.right` | `Icons.Filled.Workspaces` |
| Sidebar | `line.3.horizontal` | `Icons.Filled.Menu` |
| Overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Show all sources | `chevron.right` | `Icons.Filled.ChevronRight` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas` mark + infinite-transition motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the near-black canvas wants light-content system bars (dark-content in light mode). The 56dp header sits below the camera cutout; the search input pins above the IME — use `Modifier.imePadding()` so it never hides behind the keyboard; the tab bar clears the gesture nav via `Scaffold` insets.
- **Font scaling**: `sp` honors the user's font scale — keep it on answer body, all headings, list items, source titles, question titles, related questions. Code scales but cap it (`fontSize` clamp at 18sp) to prevent overflow. Pin layout-sensitive text (citation chips, 11sp source domains, 10sp tab labels, code-language label) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: the answer block announces "Answer from Perplexity" then the body once; citation chips read "Citation 1, button — opens source 1: {title}" via `Modifier.semantics { ... }` + a custom "Open source" action so users reach the source without scrolling; source cards read "Source 1, {domain}, {title}, button". Announce "Generating answer" once on stream start and "Answer complete" on finalize — never per token.
- **Touch targets**: Material guidance is 48.dp minimum. The 36.dp send circle gets a 48–56.dp hit area; the 18.dp citation chip is wrapped to a ~32–48.dp target via padding; source cards and related-question rows are full-row tappable.
- **Contrast**: `#FAFAFA` on `#0A0A0A` exceeds WCAG AAA; `#A1A1A1` meets AA at 14sp+; `#6E6E6E` only at 18sp+ — do not use TextTertiary on small body text. Teal CTAs use Canvas `#0A0A0A` text (reversed from typical) for AA; in light mode the teal darkens to `#1591A3`.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, skip the searching-dot pulse, the source-card cascade, the streaming-cursor blink, and the focus-ring glow — preserve haptics.
- **Dynamic / dark color**: do **not** enable Material You `dynamicColorScheme()` — Perplexity's single teal accent and near-black canvas are brand-load-bearing and must not retint to the user's wallpaper. The triple-circle mark must stay within the teal scale (`#20B8CD` / `#3DD6EC` / `#1591A3`) in every theme. Light mode flips canvas/surface/text and darkens teal to `#1591A3` for WCAG; everything else is preserved.
