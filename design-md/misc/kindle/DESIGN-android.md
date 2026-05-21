# Kindle (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Kindle's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a reading-theme model, a `Typography` set, paste-ready `@Composable`s, the reader + Aa panel, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Kindle's vanishing chrome, sepia reading page, one-orange accent, cover-grid library with progress bars) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a SwiftUI sheet, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for cover art. No color extraction — Kindle's palette is fixed; Palette is not needed. Kindle has two surface contexts: the **reading surface** obeys the user's chosen theme (OS-independent); the **app chrome** follows system dark.

## 1. Color Tokens

```kotlin
// ui/theme/KindleColors.kt
import androidx.compose.ui.graphics.Color

object KindleColors {
    // Brand & interactive — orange is the ONLY accent
    val Orange        = Color(0xFFFF9900)
    val OrangePressed = Color(0xFFE88B00)
    val Black         = Color(0xFF1A1A1A)
    val Link          = Color(0xFF1A98FF)
    val KindleBlue    = Color(0xFF4FB3D9)

    // App chrome (light)
    val ChromeCanvas  = Color(0xFFFFFFFF)
    val SurfaceSubtle = Color(0xFFF4F2EE)
    val Divider       = Color(0xFFE4E2DD)

    // App chrome (dark)
    val DarkCanvas   = Color(0xFF0E0E0E)
    val DarkSurface1 = Color(0xFF1A1A1A)
    val DarkSurface2 = Color(0xFF242424)
    val DarkDivider  = Color(0xFF2E2E2E)

    // Text (chrome)
    val TextPrimary     = Color(0xFF1A1A1A)
    val TextSecondary   = Color(0xFF6B6B6B)
    val DarkTextPrimary = Color(0xFFE8E8E8)
    val DarkTextSecondary = Color(0xFF9A9A9A)

    // Semantic
    val Error   = Color(0xFFE0533D)
    val Success = Color(0xFF2FAE5F)
}

// Reading themes — the user's choice, NOT the OS
enum class KindleReadingTheme(val page: Color, val ink: Color) {
    White(Color(0xFFFFFFFF), Color(0xFF1A1A1A)),
    Sepia(Color(0xFFFBF0D9), Color(0xFF5F4B32)),   // default
    Green(Color(0xFFC5E1C5), Color(0xFF33492F)),
    Dark (Color(0xFF2A2A2A), Color(0xFFD8D8D8)),
    Black(Color(0xFF000000), Color(0xFFC8C8C8)),   // dimmed ink, not white
}
```

Wire app chrome into both schemes. The reading surface does NOT read the Material scheme — it renders the user's `KindleReadingTheme`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val KindleLight = lightColorScheme(
    primary        = KindleColors.Orange,
    onPrimary      = KindleColors.Black,
    background      = KindleColors.ChromeCanvas,
    onBackground    = KindleColors.TextPrimary,
    surface        = KindleColors.ChromeCanvas,
    onSurface      = KindleColors.TextPrimary,
    surfaceVariant = KindleColors.SurfaceSubtle,
    outline        = KindleColors.Divider,
    error          = KindleColors.Error,
)

private val KindleDark = darkColorScheme(
    primary        = KindleColors.Orange,        // accent unchanged across modes
    onPrimary      = KindleColors.Black,
    background      = KindleColors.DarkCanvas,
    onBackground    = KindleColors.DarkTextPrimary,
    surface        = KindleColors.DarkSurface1,
    onSurface      = KindleColors.DarkTextPrimary,
    surfaceVariant = KindleColors.DarkSurface2,
    outline        = KindleColors.DarkDivider,
    error          = KindleColors.Error,
)

@Composable
fun KindleChromeTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) KindleDark else KindleLight,
    typography = KindleTypography,
    content = content,
)
```

## 2. Typography (Material 3)

Kindle uses a reading serif (**Bookerly**; ship **Bitter** as a free SIL OFL analog) for book text and **Amazon Ember** for chrome (fall back to the system sans for non-Amazon builds). The reading face/size/spacing/margins are user controls.

```kotlin
// ui/theme/KindleType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Bitter = FontFamily(
    Font(R.font.bitter_regular, FontWeight.Normal),
    Font(R.font.bitter_bold,    FontWeight.Bold),
)
// Amazon Ember requires Amazon's license — for non-Amazon builds use FontFamily.SansSerif
val Ember = FontFamily(
    Font(R.font.amazon_ember_regular, FontWeight.Normal),
    Font(R.font.amazon_ember_medium,  FontWeight.Medium),
    Font(R.font.amazon_ember_bold,    FontWeight.Bold),
)

object KindleText {
    val ScreenTitle  = TextStyle(Ember,  fontWeight = FontWeight.Bold,   fontSize = 30.sp, lineHeight = 36.sp, letterSpacing = (-0.4).sp)
    val ChapterTitle = TextStyle(Bitter, fontWeight = FontWeight.Bold,   fontSize = 24.sp, lineHeight = 30.sp)
    val BookTitle    = TextStyle(Ember,  fontWeight = FontWeight.Bold,   fontSize = 20.sp, lineHeight = 26.sp)
    val Subtitle     = TextStyle(Ember,  fontWeight = FontWeight.Medium, fontSize = 17.sp, lineHeight = 24.sp)
    val ListTitle    = TextStyle(Ember,  fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 21.sp)
    val Meta         = TextStyle(Ember,  fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 19.sp)
    val Caption      = TextStyle(Ember,  fontWeight = FontWeight.Medium, fontSize = 12.sp, lineHeight = 16.sp)
    val Eyebrow      = TextStyle(Ember,  fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 1.5.sp)
    val Tab          = TextStyle(Ember,  fontWeight = FontWeight.Medium, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Note         = TextStyle(Ember,  fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 20.sp)
    // Reading body is built dynamically from user settings (see ReadingPage)
}

val KindleTypography = Typography(
    headlineLarge  = KindleText.ScreenTitle,
    headlineMedium = KindleText.BookTitle,
    titleMedium    = KindleText.ListTitle,
    bodyMedium     = KindleText.Note,
    labelSmall     = KindleText.Tab,
)

// User reading settings (hoist to a DataStore-backed state)
data class ReadingSettings(
    val fontFamily: FontFamily = Bitter,
    val size: Int = 16,                 // sp
    val lineHeightMultiple: Float = 1.72f,
    val margin: Int = 26,               // dp — Narrow 16 / Standard 26 / Wide 40
    val theme: KindleReadingTheme = KindleReadingTheme.Sepia,
)
```

## 3. Signature Components

### Reading Page

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.verticalScroll
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun ReadingPage(
    chapter: String,
    title: String,
    paragraphs: List<String>,
    percent: Int,
    minsLeft: Int,
    settings: ReadingSettings,
    onToggleChrome: () -> Unit,
) {
    val t = settings.theme
    Box(
        Modifier
            .fillMaxSize()
            .background(t.page)
            .clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) { onToggleChrome() }
    ) {
        Column(
            Modifier
                .fillMaxSize()
                .verticalScroll(rememberScrollState())
                .padding(horizontal = settings.margin.dp, vertical = 20.dp)
        ) {
            Text(chapter, style = KindleText.Eyebrow, color = t.ink.copy(alpha = 0.55f))
            Spacer(Modifier.height(14.dp))
            Text(title, style = KindleText.ChapterTitle, color = t.ink)
            Spacer(Modifier.height(22.dp))
            paragraphs.forEach { p ->
                Text(
                    p,
                    style = TextStyle(
                        fontFamily = settings.fontFamily,
                        fontSize = settings.size.sp,
                        lineHeight = (settings.size * settings.lineHeightMultiple).sp,
                        color = t.ink,
                        textAlign = TextAlign.Justify,   // Compose supports Justify; see note
                    ),
                    modifier = Modifier.padding(bottom = 16.dp),
                )
            }
        }
        // footer whisper + 2dp orange progress hairline
        Column(Modifier.align(Alignment.BottomStart).padding(horizontal = 24.dp).padding(bottom = 14.dp)) {
            Box(Modifier.fillMaxWidth().height(2.dp).background(t.ink.copy(alpha = 0.18f))) {
                Box(Modifier.fillMaxWidth(percent / 100f).fillMaxHeight().background(KindleColors.Orange))
            }
            Spacer(Modifier.height(8.dp))
            Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
                Text("$percent%", style = KindleText.Eyebrow, color = t.ink.copy(alpha = 0.55f))
                Text("$minsLeft min left in chapter", style = KindleText.Eyebrow, color = t.ink.copy(alpha = 0.55f))
            }
        }
    }
}
// NOTE: TextAlign.Justify justifies but does not hyphenate. For Kindle-grade
// justified + hyphenated pagination, use a TextKit-style paginator (e.g. an
// AndroidView over StaticLayout with Layout.HYPHENATION_FREQUENCY_FULL) or a WebView paginator.
```

### Aa Typography Panel

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Slider
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun AaPanel(settings: ReadingSettings, onChange: (ReadingSettings) -> Unit) {
    val t = settings.theme
    val haptics = LocalHapticFeedback.current
    Column(
        Modifier
            .clip(RoundedCornerShape(topStart = 12.dp, topEnd = 12.dp))
            .background(t.page)
            .padding(20.dp)
    ) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(10.dp)) {
            Text("Aa", style = KindleText.ChapterTitle.copy(fontSize = 14.sp), color = t.ink)
            Slider(
                value = settings.size.toFloat(), valueRange = 12f..26f, steps = 27,
                onValueChange = { onChange(settings.copy(size = it.toInt())) },
                modifier = Modifier.weight(1f),
            )
            Text("Aa", style = KindleText.ChapterTitle, color = t.ink)
        }
        Spacer(Modifier.height(14.dp))
        Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
            KindleReadingTheme.entries.forEach { theme ->
                val active = settings.theme == theme
                Box(contentAlignment = Alignment.Center) {
                    if (active) {
                        Box(Modifier.size(38.dp).border(2.dp, KindleColors.Orange, CircleShape))
                    }
                    Box(
                        Modifier
                            .size(30.dp).clip(CircleShape).background(theme.page)
                            .border(1.dp, Color.Black.copy(alpha = 0.18f), CircleShape)
                            .clickable {
                                onChange(settings.copy(theme = theme))
                                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                            }
                    )
                }
            }
        }
    }
}
```

### Library Cover Cell

```kotlin
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun LibraryCover(imageUrl: String?, progress: Float, author: String) {
    Column(verticalArrangement = Arrangement.spacedBy(6.dp)) {
        Box(
            Modifier
                .aspectRatio(2f / 3f)
                .shadow(10.dp, RoundedCornerShape(4.dp), spotColor = Color.Black.copy(alpha = 0.4f))
                .clip(RoundedCornerShape(4.dp))
        ) {
            AsyncImage(model = imageUrl, contentDescription = null,
                modifier = Modifier.matchParentSize(), contentScale = ContentScale.Crop)
            Box(Modifier.align(Alignment.BottomStart).fillMaxWidth().height(3.dp)
                .background(Color.White.copy(alpha = 0.2f))) {
                Box(Modifier.fillMaxWidth(progress).fillMaxHeight().background(KindleColors.Orange))
            }
        }
        Text(
            if (progress >= 1f) "Finished · $author" else "${(progress * 100).toInt()}% · $author",
            style = KindleText.Caption, color = KindleColors.TextSecondary,
        )
    }
}
```

### Reading Progress Bar

```kotlin
@Composable
fun KindleProgress(fraction: Float, caption: String, dark: Boolean = false) {
    Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(12.dp)) {
        Box(
            Modifier.weight(1f).height(4.dp).clip(RoundedCornerShape(2.dp))
                .background(if (dark) KindleColors.DarkSurface2 else KindleColors.Divider)
        ) {
            Box(Modifier.fillMaxWidth(fraction).fillMaxHeight().background(KindleColors.Orange))
        }
        Text(caption, style = KindleText.Caption, modifier = Modifier.width(96.dp))
    }
}
```

## 4. Navigation

Kindle's chrome has a 4-tab bottom strip and a serif large title. On Android, model the strip as a `NavigationBar` (no tint pill — active is the one orange) and the reader top bar as a fade-controlled overlay.

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun KindleBottomBar(selected: Int, onSelect: (Int) -> Unit, dark: Boolean = false) {
    NavigationBar(
        containerColor = if (dark) KindleColors.DarkCanvas else KindleColors.ChromeCanvas,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home" to Icons.Filled.Home,
            "Library" to Icons.Filled.MenuBook,
            "Discover" to Icons.Filled.Search,
            "More" to Icons.Filled.MoreHoriz,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = KindleText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = KindleColors.Orange,
                    selectedTextColor = KindleColors.Orange,
                    unselectedIconColor = if (dark) KindleColors.DarkTextSecondary else KindleColors.TextSecondary,
                    unselectedTextColor = if (dark) KindleColors.DarkTextSecondary else KindleColors.TextSecondary,
                    indicatorColor = Color.Transparent, // no Material pill — Kindle has none
                ),
            )
        }
    }
}
```

The Aa panel, X-Ray, and in-book search are `ModalBottomSheet`s (12.dp top radius), Aa panel tinted to the active reading theme. On Dark/Black themes give sheets a 1.dp theme-divider border since shadows are invisible there.

## 5. Motion

Kindle motion is quiet and fast — the reading surface barely moves.

| Moment | Compose recipe |
|--------|----------------|
| Chrome fade on book open / center-tap | `AnimatedVisibility(fadeIn(tween(200)), fadeOut(tween(250)))` over nav + tab |
| Theme swatch select | ring `animateFloatAsState` 1→1.15→1 `spring`; page `animateColorAsState(tween(150))`; `HapticFeedbackType.LongPress` |
| Library progress fill | `animateFloatAsState(progress, tween(500))` on first composition |
| Continue-reading cover | scale `animateFloatAsState(1f, tween(250))` from 0.96 |
| Highlight commit | selected range background `animateColorAsState(tween(120))`; soft haptic |
| Page turn | horizontal `AnimatedContent` slide `tween(220)`, or a `HorizontalPager`; curl via custom `graphicsLayer` |
| Sync banner | `AnimatedVisibility(slideInVertically + fadeIn, tween(250))` non-modal |

```kotlin
// Theme cross-fade — the canonical Kindle reading-surface motion
val page by animateColorAsState(settings.theme.page, tween(150), label = "page")
val ink  by animateColorAsState(settings.theme.ink,  tween(150), label = "ink")
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on theme select and highlight commit. Page turn has **no** haptic by default. Sync ("furthest page read") is a non-modal banner, no haptic.

## 6. Icons

Kindle's chrome iconography is conventional; the closest first-party set is `androidx.compose.material:material-icons-extended`. The Aa control is text, not an icon; theme swatches are colored circles.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Library (tab) | `books.vertical` | `Icons.Filled.MenuBook` |
| Discover (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| More (tab) | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Back to library | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Aa panel | (text "Aa") | (text "Aa") |
| Brightness | `sun.max` | `Icons.Filled.LightMode` |
| Bookmark | `bookmark` / `bookmark.fill` | `Icons.Filled.BookmarkBorder` / `Icons.Filled.Bookmark` |
| Search in book | `magnifyingglass` | `Icons.Filled.Search` |
| Contents / Go To | `list.bullet` | `Icons.AutoMirrored.Filled.List` |
| X-Ray | `person.text.rectangle` | `Icons.Filled.People` |
| Sync furthest | `arrow.triangle.2.circlepath` | `Icons.Filled.Sync` |
| Downloaded | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Not downloaded | `arrow.down.circle` | `Icons.Filled.CloudDownload` |
| Highlight | `highlighter` | `Icons.Filled.BorderColor` |
| Note | `note.text` | `Icons.Filled.EditNote` |
| Dictionary / Lookup | `character.book.closed` | `Icons.Filled.Book` |
| Translate | `globe` | `Icons.Filled.Translate` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`. In the reader, set system bars to match the active reading theme (dark icons on White/Sepia/Green, light on Dark/Black) and hide them while chrome is hidden.
- **Justified reading body**: `TextAlign.Justify` justifies but won't hyphenate — for Kindle-grade pagination use an `AndroidView` over `StaticLayout` with `Layout.HYPHENATION_FREQUENCY_FULL` and `JustificationMode.INTER_WORD`, or a WebView paginator.
- **Two surface contexts**: the `ReadingPage` renders the user's `KindleReadingTheme` and must NOT read the Material scheme; app chrome uses `isSystemInDarkTheme()`. Keep them decoupled.
- **Black theme ink**: `#C8C8C8`, never white — reduces halation.
- **Font scaling**: chrome `sp` honors the user's font scale (titles, list rows, captions). The reading body uses the **in-app** size control; honor system scale only when "Match system size" is on. Pin layout-sensitive chrome (10sp tab labels, eyebrow, footer whisper) by deriving from `dp`.
- **TalkBack**: expose reading paragraphs as ordered accessible text; the footer whisper as "38 percent, 14 minutes left in chapter"; theme swatches as "Sepia theme, selected"; give every `LibraryCover` a `contentDescription` of "{title} by {author}, {percent}% read"; provide explicit Next/Previous-page custom actions in addition to tap zones.
- **Touch targets**: Material guidance is 48.dp — give the 30.dp theme swatches and 22.dp tab glyphs a 48.dp hit area; page-turn zones span the page thirds.
- **Contrast**: every reading theme's ink/page pair is tuned to pass WCAG AA at reading size; the Black theme intentionally trades pure-white contrast for reduced halation.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, make the chrome fade instant, drop the theme ring pop and progress fill — keep the state changes; substitute `Crossfade` for page turns.
- **Dark mode**: app chrome inverts via the `Dark*` palette (`#0E0E0E` canvas, `#1A1A1A` surfaces); the orange `#FF9900` accent is constant in every theme and every mode. Do **not** enable Material You `dynamicColorScheme()` — Kindle's neutral chrome + single orange accent and the user-chosen reading themes must hold regardless of wallpaper.
