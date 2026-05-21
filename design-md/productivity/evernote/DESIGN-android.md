# Evernote (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Evernote's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the note editor + checklist + FAB, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Evernote's calm neutral canvas, the single brand green, green tag pills, the raised circular FAB, roomy reading rhythm) while making everything idiomatic Android — `NavigationBar` + a center `FloatingActionButton` (docked) instead of a UITabBar with embedded FAB, `SwipeToDismissBox` for swipe actions, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for thumbnails. No Material You dynamic color — Evernote's brand green is fixed; a full dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/EvernoteColors.kt
import androidx.compose.ui.graphics.Color

object EverColors {
    // Brand (interactive)
    val Green        = Color(0xFF00A82D)
    val GreenBright  = Color(0xFF2DBE60) // interactive on dark
    val GreenPressed = Color(0xFF007A20)
    val GreenDeep    = Color(0xFF108A00)
    val Ink          = Color(0xFF1C2B33)

    // Canvas & Surfaces (Light)
    val Canvas   = Color(0xFFFFFFFF)
    val OffWhite = Color(0xFFFAFAFA)
    val Pressed  = Color(0xFFF1F2F2)
    val Divider  = Color(0xFFE4E5E5)
    val Border   = Color(0xFFDCDEDE)

    // Canvas & Surfaces (Dark) — true near-black
    val DarkCanvas   = Color(0xFF1C1C1E)
    val DarkSurface1 = Color(0xFF242426)
    val DarkSurface2 = Color(0xFF2E2E30)
    val DarkSurface3 = Color(0xFF3A3A3C)
    val DarkDivider  = Color(0xFF38383A)

    // Text
    val TextPrimary   = Color(0xFF1C2B33) // Evernote warm ink — NOT pure black
    val TextSecondary = Color(0xFF5C6970)
    val TextTertiary  = Color(0xFF8C969C)
    val DarkTextPrimary   = Color(0xFFE4E4E6) // NOT pure white — reading comfort
    val DarkTextSecondary = Color(0xFF9A9A9E)
    val DarkTextTertiary  = Color(0xFF6E6E72)

    // Tag chips
    val TagBgLight   = Color(0xFFE6F4EA); val TagTextLight = Color(0xFF1F7A33)
    val TagBgDark    = Color(0xFF1C3A24); val TagTextDark  = Color(0xFF5FD68A)

    // Semantic
    val Blue    = Color(0xFF2F80ED)
    val Warn    = Color(0xFFF6A609)
    val Error   = Color(0xFFE5484D)
    val Yellow  = Color(0xFFF6C544)
    val HiLight = Color(0xFFFFF3B0)
    val HiDark  = Color(0xFF5A4A12)

    // Notebook accents
    val NbBlue   = Color(0xFF2F80ED)
    val NbYellow = Color(0xFFF6C544)
    val NbRed    = Color(0xFFE5484D)
    val NbPurple = Color(0xFF7A5AF8)
    val NbTeal   = Color(0xFF1AAE9F)
}
```

Wire it into both schemes. Evernote is light-first; the dark scheme uses a true near-black `#1C1C1E` for reading comfort and brightens interactive green.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val EverLight = lightColorScheme(
    primary        = EverColors.Green,
    onPrimary      = Color.White,
    background      = EverColors.OffWhite,
    onBackground   = EverColors.TextPrimary,
    surface        = EverColors.Canvas,
    onSurface      = EverColors.TextPrimary,
    surfaceVariant = EverColors.Pressed,
    outline        = EverColors.Divider,
    error          = EverColors.Error,
)

private val EverDark = darkColorScheme(
    primary        = EverColors.GreenBright,
    onPrimary      = Color.White,
    background      = EverColors.DarkCanvas,
    onBackground   = EverColors.DarkTextPrimary,
    surface        = EverColors.DarkSurface1,
    onSurface      = EverColors.DarkTextPrimary,
    surfaceVariant = EverColors.DarkSurface2,
    outline        = EverColors.DarkDivider,
    error          = EverColors.Error,
)

@Composable
fun EvernoteTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) EverDark else EverLight,
    typography = EverTypography,
    content = content,
)
```

## 2. Typography

Evernote uses the iOS system font; on Android use **Inter** (SIL OFL) in `res/font/` for cross-platform parity. Body is 16sp at 26sp line-height (≈1.6) — reading comfort first.

```kotlin
// ui/theme/EverType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
    Font(R.font.inter_bold,     FontWeight.Bold),
)

object EverText {
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 30.sp, lineHeight = 36.sp, letterSpacing = (-0.4).sp)
    val NoteTitle   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 30.sp, letterSpacing = (-0.3).sp)
    val NoteH1      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val NoteH2      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 26.sp) // ≈1.6
    val ListTitle   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Checklist   = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Snippet     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val Tag         = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.1.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 15.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Code        = TextStyle(FontFamily.Monospace, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 21.sp)
}

val EverTypography = Typography(
    headlineLarge = EverText.ScreenTitle,
    headlineMedium = EverText.NoteTitle,
    titleMedium   = EverText.NoteH2,
    bodyMedium    = EverText.Body,
    labelSmall    = EverText.Tab,
)
```

## 3. Signature Components

### Tag Pill

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

@Composable
fun TagPill(text: String, dark: Boolean = isSystemInDarkTheme()) {
    Box(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(if (dark) EverColors.TagBgDark else EverColors.TagBgLight)
            .padding(horizontal = 9.dp, vertical = 3.dp)
    ) {
        Text(text, style = EverText.Tag, color = if (dark) EverColors.TagTextDark else EverColors.TagTextLight)
    }
}
```

### Checklist Item

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.style.TextDecoration

@Composable
fun ChecklistItem(initialDone: Boolean = false, text: String) {
    val dark = isSystemInDarkTheme()
    var done by remember { mutableStateOf(initialDone) }
    val haptics = LocalHapticFeedback.current
    val pop by animateFloatAsState(if (done) 1.12f else 1f, spring(dampingRatio = 0.4f), label = "pop")
    val green = if (dark) EverColors.GreenBright else EverColors.Green

    Row(verticalAlignment = Alignment.Top, horizontalArrangement = Arrangement.spacedBy(10.dp)) {
        Box(
            Modifier
                .size(44.dp)
                .clickable {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                    done = !done
                },
            contentAlignment = Alignment.TopStart,
        ) {
            Box(
                Modifier
                    .size(19.dp)
                    .scale(if (done) pop else 1f)
                    .clip(RoundedCornerShape(5.dp))
                    .then(if (done) Modifier.background(green)
                          else Modifier.border(2.dp, EverColors.TextTertiary, RoundedCornerShape(5.dp))),
                contentAlignment = Alignment.Center,
            ) {
                if (done) Icon(Icons.Filled.Check, contentDescription = null, tint = Color.White, modifier = Modifier.size(11.dp))
            }
        }
        Text(
            text,
            style = EverText.Checklist.copy(textDecoration = if (done) TextDecoration.LineThrough else TextDecoration.None),
            color = if (done) EverColors.TextTertiary
                    else if (dark) EverColors.DarkTextPrimary else EverColors.TextPrimary,
        )
    }
}
```

### Note List Row

```kotlin
import androidx.compose.material3.HorizontalDivider
import coil.compose.AsyncImage

@Composable
fun NoteRow(
    title: String, snippet: String, date: String, notebook: String, thumbUrl: String? = null,
) {
    val dark = isSystemInDarkTheme()
    Column {
        Row(
            Modifier.fillMaxWidth().padding(vertical = 14.dp),
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Column(Modifier.weight(1f)) {
                Text(title, style = EverText.ListTitle, maxLines = 1,
                    color = if (dark) EverColors.DarkTextPrimary else EverColors.TextPrimary)
                Text(snippet, style = EverText.Snippet, maxLines = 2,
                    color = if (dark) EverColors.DarkTextSecondary else EverColors.TextSecondary)
                Text("$date · $notebook", style = EverText.Meta, modifier = Modifier.padding(top = 3.dp),
                    color = if (dark) EverColors.DarkTextTertiary else EverColors.TextTertiary)
            }
            if (thumbUrl != null) {
                AsyncImage(
                    model = thumbUrl, contentDescription = null,
                    modifier = Modifier.size(52.dp).clip(RoundedCornerShape(8.dp))
                        .background(if (dark) EverColors.DarkSurface3 else EverColors.Pressed),
                )
            }
        }
        HorizontalDivider(color = if (dark) EverColors.DarkDivider else EverColors.Divider, thickness = 1.dp)
    }
}
```

### Format Toolbar (bottom accessory)

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun FormatToolbar() {
    val dark = isSystemInDarkTheme()
    val tools = listOf(
        Icons.Filled.FormatBold, Icons.Filled.FormatItalic, Icons.Filled.FormatListBulleted,
        Icons.Filled.CheckBox, Icons.Filled.Title, Icons.Filled.AttachFile, Icons.Filled.Link,
    )
    Row(
        Modifier.fillMaxWidth()
            .background(if (dark) EverColors.DarkSurface2 else EverColors.Canvas)
            .padding(vertical = 12.dp),
        horizontalArrangement = Arrangement.SpaceAround,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        tools.forEach {
            Icon(it, contentDescription = null, modifier = Modifier.size(20.dp),
                tint = if (dark) EverColors.DarkTextSecondary else EverColors.TextSecondary)
        }
    }
}
```

## 4. Navigation

Evernote has a 5-slot bottom strip with a raised green circular new-note button in the center. On Android, model this as a `Scaffold` with a `BottomAppBar`/`NavigationBar` plus a center-docked `FloatingActionButton`. No tint pill — active is brand green.

```kotlin
import androidx.compose.material3.*

@Composable
fun EvernoteBottomBar(selected: Int, onSelect: (Int) -> Unit, onNewNote: () -> Unit) {
    val dark = isSystemInDarkTheme()
    Box {
        NavigationBar(containerColor = MaterialTheme.colorScheme.surface, tonalElevation = 0.dp) {
            val items = listOf(
                "Home"      to Icons.Filled.Home,
                "Notes"     to Icons.Filled.Description,
                null        to null, // FAB gap
                "Notebooks" to Icons.Filled.LibraryBooks,
                "Search"    to Icons.Filled.Search,
            )
            items.forEachIndexed { i, pair ->
                if (pair.first == null) {
                    NavigationBarItem(selected = false, onClick = {}, enabled = false, icon = {}, label = {})
                } else {
                    NavigationBarItem(
                        selected = selected == i,
                        onClick = { onSelect(i) },
                        icon = { Icon(pair.second!!, contentDescription = pair.first, modifier = Modifier.size(24.dp)) },
                        label = { Text(pair.first!!, style = EverText.Tab) },
                        colors = NavigationBarItemDefaults.colors(
                            selectedIconColor = if (dark) EverColors.GreenBright else EverColors.Green,
                            selectedTextColor = if (dark) EverColors.GreenBright else EverColors.Green,
                            unselectedIconColor = EverColors.TextTertiary,
                            unselectedTextColor = EverColors.TextTertiary,
                            indicatorColor = Color.Transparent, // no Material pill
                        ),
                    )
                }
            }
        }
        FloatingActionButton(
            onClick = onNewNote,
            containerColor = EverColors.Green,
            contentColor = Color.White,
            shape = CircleShape,
            modifier = Modifier.align(Alignment.TopCenter).offset(y = (-22).dp).size(52.dp),
        ) { Icon(Icons.Filled.Add, contentDescription = "New note") }
    }
}
```

Tapping the FAB opens a `ModalBottomSheet` (20.dp top radius) with capture rows: Text Note, Camera, Photo, Audio, Reminder — each a tall row with a leading green icon. The notebooks screen lists notebook rows (spine icon + name + count) and stacks as expandable group headers.

## 5. Motion

Evernote motion is calm — 150–300ms ease-out / soft spring, never aggressive.

| Moment | Compose recipe |
|--------|----------------|
| Checkbox toggle | box `scale` via `animateFloatAsState(spring(dampingRatio = 0.4f))` + strikethrough applied; light haptic |
| Note open (list → editor) | Nav3/`NavHost` slide push `tween(300)` |
| FAB → capture sheet | `ModalBottomSheet` default slide-up (≈300ms); FAB press scale 0.96 via `interactionSource` |
| Sync toast | `Snackbar` / `AnimatedVisibility` `fadeIn + slideInVertically(tween(200))`, auto-dismiss 2s |
| Stack expand | chevron `animateFloatAsState` 0→90° `tween(150)`; rows `expandVertically(tween(200))` + `fadeIn` |
| Tag add | `AnimatedVisibility` `scaleIn(initialScale = 0.8f, tween(150)) + fadeIn` |
| Swipe actions | `SwipeToDismissBox` — leading pin/move, trailing delete; medium haptic on commit |

```kotlin
// Checkbox pop — the canonical Evernote micro-motion
val pop by animateFloatAsState(if (done) 1.12f else 1f, spring(dampingRatio = 0.4f), label = "pop")
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the light tick on checkbox toggle and tab change; medium-equivalent on swipe-action commit and note delete; `HapticFeedbackConstants.CLOCK_TICK` for the soft FAB long-press. Auto-save is silent — only show a `Snackbar` on explicit save or sync error.

## 6. Icons

Evernote's UI maps to filled Material icons; brand green carries state. Add `androidx.compose.material:material-icons-extended` for the format set.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` | `Icons.Filled.Home` |
| Notes (tab) | `note.text` | `Icons.Filled.Description` |
| FAB / new note | `plus` | `Icons.Filled.Add` |
| Notebooks (tab) | `books.vertical` | `Icons.Filled.LibraryBooks` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Note info | `info.circle` | `Icons.Filled.InfoOutline` |
| Checkbox empty | `square` | `Icons.Outlined.CheckBoxOutlineBlank` |
| Checkbox done | `checkmark.square.fill` | `Icons.Filled.CheckBox` |
| Bold | `bold` | `Icons.Filled.FormatBold` |
| Italic | `italic` | `Icons.Filled.FormatItalic` |
| List | `list.bullet` | `Icons.Filled.FormatListBulleted` |
| Checklist | `checklist` | `Icons.Filled.CheckBox` |
| Heading | `textformat.size` | `Icons.Filled.Title` |
| Attach | `paperclip` | `Icons.Filled.AttachFile` |
| Camera / scan | `doc.viewfinder` | `Icons.Filled.DocumentScanner` |
| Audio | `mic` | `Icons.Filled.Mic` |
| Link | `link` | `Icons.Filled.Link` |
| Highlight | `highlighter` | `Icons.Filled.FormatColorFill` |
| Tag | `tag` | `Icons.Filled.LocalOffer` |
| Pin | `pin` | `Icons.Filled.PushPin` |
| Reminder | `clock` | `Icons.Filled.Schedule` |
| Notebook | `book.closed` | `Icons.Filled.MenuBook` |
| Stack | `square.stack` | `Icons.Filled.Layers` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; `ModalBottomSheet`, `SwipeToDismissBox`, modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; light canvas wants dark-content system bars (light-content in dark mode). The docked FAB must clear the gesture nav inset.
- **Font scaling**: `sp` honors the user's scale — keep it on titles, headings, body, snippets, checklist (it stays readable as it scales). Pin layout-sensitive text (10sp tab labels, tag pills, sync-toast) so the bottom strip doesn't break.
- **Reading comfort**: keep `Body.lineHeight = 26.sp` (≈1.6) and 22.dp horizontal editor padding — do not tighten; this is the brand's core feel.
- **TalkBack**: label note rows "{title}, {snippet}, {date}, {notebook}"; checklist items "{text}, checked/unchecked" with a toggle action; the FAB "New note, button"; tag pills "Tag: {name}, filters notes". Never convey checklist done-state by color alone — strikethrough + dim is the secondary cue.
- **Touch targets**: Material guidance is 48.dp — the checkbox sits in a 44–48.dp hit `Box`; note rows are full-row tappable (≥64.dp); FAB is 52.dp; format-toolbar icons get 48.dp hit padding.
- **Contrast**: `#1C2B33` on `#FFFFFF` and `#E4E4E6` on `#1C1C1E` pass WCAG AA for body; use `#2DBE60` (not `#00A82D`) for small interactive green on the dark canvas to clear AA.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the checkbox pop and stack slide — substitute `Crossfade`; keep instant state changes.
- **Dark mode**: invert via the `Dark*` palette — the dark canvas is a **true near-black `#1C1C1E`**, NOT charcoal-gray; `#1C2B33` text → `#E4E4E6` (not pure white). Shadows nearly vanish on dark, so signal elevation via the surface step (`#242426`→`#2E2E30`→`#3A3A3C`) + a 1.dp `DarkDivider` border on menus and sheets; the FAB keeps its green-tinted glow. Do **not** enable `dynamicColorScheme()` — Evernote's single brand green must hold regardless of wallpaper.
