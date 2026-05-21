# Dropbox (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Dropbox's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Dropbox's paper-white canvas, single Dropbox-Blue accent, colored file-type rows, determinate upload bar) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `FloatingActionButton` instead of a custom iOS FAB, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for image thumbnails.

## 1. Color Tokens

```kotlin
// ui/theme/DropboxColors.kt
import androidx.compose.ui.graphics.Color

object DropboxColors {
    // Canvas & Surfaces (Light)
    val Canvas   = Color(0xFFFFFFFF)
    val Surface  = Color(0xFFF7F5F2)
    val Divider  = Color(0xFFE6E1DA)

    // Text
    val TextPrimary   = Color(0xFF1E1919)
    val TextSecondary = Color(0xFF6F6A65)
    val TextTertiary  = Color(0xFFA39E98)

    // Brand
    val Blue        = Color(0xFF0061FF)
    val BluePressed = Color(0xFF0050D0)
    val BlueTint    = Color(0xFFE6F0FF)

    // Dark
    val DarkCanvas  = Color(0xFF1E1919)
    val DarkSurface = Color(0xFF2A2424)
    val DarkDivider = Color(0xFF3A3331)
    val DarkBlue    = Color(0xFF3D8BFF)

    // File-type icons
    val PdfRed     = Color(0xFFFA551E)
    val DocBlue    = Color(0xFF0061FF)
    val SheetGreen = Color(0xFF1A8754)
    val ImageTeal  = Color(0xFF00B2A9)
    val FolderSlate = Color(0xFF8C97A8)

    // Semantic
    val Success = Color(0xFF1A8754)
    val Warning = Color(0xFFFFAF00)
    val Error   = Color(0xFFD1180B)
}
```

Wire it into Material 3 light and dark schemes so ripples, dividers, and default component colors inherit the brand.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val DropboxLight = lightColorScheme(
    primary        = DropboxColors.Blue,
    onPrimary      = Color.White,
    background     = DropboxColors.Canvas,
    onBackground   = DropboxColors.TextPrimary,
    surface        = DropboxColors.Canvas,
    onSurface      = DropboxColors.TextPrimary,
    surfaceVariant = DropboxColors.Surface,
    outline        = DropboxColors.Divider,
    error          = DropboxColors.Error,
)

private val DropboxDark = darkColorScheme(
    primary        = DropboxColors.DarkBlue,
    onPrimary      = Color.White,
    background     = DropboxColors.DarkCanvas,
    onBackground   = Color.White,
    surface        = DropboxColors.DarkSurface,
    onSurface      = Color.White,
    surfaceVariant = DropboxColors.DarkSurface,
    outline        = DropboxColors.DarkDivider,
    error          = DropboxColors.Error,
)

@Composable
fun DropboxTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) DropboxDark else DropboxLight,
    typography  = DropboxTypography,
    content     = content,
)
```

## 2. Typography

Sharp Grotesk is Dropbox's licensed brand typeface. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. The closest free substitute is **Inter** — load that if Sharp Grotesk is unavailable; otherwise fall back to the system grotesque (Roboto).

```kotlin
// ui/theme/DropboxType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Sharp = FontFamily(
    Font(R.font.sharp_grotesk_regular,  FontWeight.Normal),   // 400
    Font(R.font.sharp_grotesk_semibold, FontWeight.SemiBold), // 600
    Font(R.font.sharp_grotesk_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object DropboxText {
    val TitleLarge = TextStyle(Sharp, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val Section    = TextStyle(Sharp, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val SheetTitle = TextStyle(Sharp, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val CardTitle  = TextStyle(Sharp, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val RowName    = TextStyle(Sharp, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val Body       = TextStyle(Sharp, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Meta       = TextStyle(Sharp, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val Caption    = TextStyle(Sharp, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val LabelUpper = TextStyle(Sharp, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Button     = TextStyle(Sharp, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp)
    val Tab        = TextStyle(Sharp, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val DropboxTypography = Typography(
    headlineLarge = DropboxText.TitleLarge,
    headlineSmall = DropboxText.Section,
    titleMedium   = DropboxText.RowName,
    bodyMedium    = DropboxText.Body,
    labelSmall    = DropboxText.Tab,
)
```

For tabular figures on file sizes/dates, apply `TextStyle(fontFeatureSettings = "tnum")` to `Meta`/`Caption` usages.

## 3. Signature Components

### Primary Button

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.graphicsLayer
import androidx.compose.ui.unit.dp

@Composable
fun DropboxPrimaryButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "btnScale")

    Box(
        modifier = modifier
            .fillMaxWidth()
            .scale(scale)
            .graphicsLayer { alpha = if (enabled) 1f else 0.4f }
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) DropboxColors.BluePressed else DropboxColors.Blue)
            .clickable(interaction, indication = null, enabled = enabled, onClick = onClick)
            .padding(vertical = 14.dp, horizontal = 24.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = DropboxText.Button, color = Color.White)
    }
}
```

### Upload / Create FAB

```kotlin
import androidx.compose.material3.FloatingActionButton
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material3.Icon
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun DropboxUploadFab(onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.92f else 1f, label = "fabScale")

    FloatingActionButton(
        onClick = {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS soft impact
            onClick()
        },
        modifier = modifier.size(56.dp).scale(scale),
        shape = CircleShape,
        containerColor = if (pressed) DropboxColors.BluePressed else DropboxColors.Blue,
        contentColor = Color.White,
        interactionSource = interaction,
    ) {
        Icon(Icons.Filled.Add, contentDescription = "Upload or create", modifier = Modifier.size(24.dp))
    }
}
```

### File / Folder Row

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.text.style.TextOverflow
import coil.compose.AsyncImage

enum class FileKind { Pdf, Doc, Sheet, Image, Folder }

@Composable
fun DropboxFileRow(
    name: String,
    meta: String,
    kind: FileKind,
    selected: Boolean = false,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()

    val (icon, tint) = when (kind) {
        FileKind.Pdf    -> Icons.Filled.PictureAsPdf to DropboxColors.PdfRed
        FileKind.Doc    -> Icons.Filled.Description   to DropboxColors.DocBlue
        FileKind.Sheet  -> Icons.Filled.GridOn        to DropboxColors.SheetGreen
        FileKind.Image  -> Icons.Filled.Image         to DropboxColors.ImageTeal
        FileKind.Folder -> Icons.Filled.Folder        to DropboxColors.FolderSlate
    }
    val bg = when {
        selected -> DropboxColors.BlueTint
        pressed  -> DropboxColors.Surface
        else     -> DropboxColors.Canvas
    }

    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(60.dp)
            .background(bg)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        if (selected) {
            Icon(Icons.Filled.CheckCircle, contentDescription = "Selected",
                tint = DropboxColors.Blue, modifier = Modifier.size(40.dp))
        } else {
            Box(
                Modifier.size(40.dp).clip(RoundedCornerShape(8.dp))
                    .background(tint.copy(alpha = 0.14f)),
                contentAlignment = Alignment.Center,
            ) {
                Icon(icon, contentDescription = null, tint = tint, modifier = Modifier.size(20.dp))
            }
        }
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(name, style = DropboxText.RowName, color = DropboxColors.TextPrimary,
                maxLines = 1, overflow = TextOverflow.Ellipsis)
            Text(meta, style = DropboxText.Meta.copy(fontFeatureSettings = "tnum"),
                color = DropboxColors.TextSecondary, maxLines = 1, overflow = TextOverflow.Ellipsis)
        }
        Icon(
            if (kind == FileKind.Folder) Icons.Filled.ChevronRight else Icons.Filled.MoreHoriz,
            contentDescription = if (kind == FileKind.Folder) "Open" else "More",
            tint = DropboxColors.TextSecondary, modifier = Modifier.size(18.dp),
        )
    }
}
```

### Upload Progress Bar

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.progressSemantics
import androidx.compose.ui.draw.drawBehind

@Composable
fun DropboxUploadBar(
    label: String,
    progress: Float, // 0f..1f real byte ratio
    done: Boolean = false,
    modifier: Modifier = Modifier,
) {
    val animated by animateFloatAsState(progress, tween(200), label = "uploadProgress")
    Column(
        modifier
            .fillMaxWidth()
            .background(DropboxColors.Canvas)
            .padding(horizontal = 16.dp, vertical = 12.dp),
        verticalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically) {
            Text(label, style = DropboxText.Meta.copy(fontWeight = FontWeight.SemiBold),
                color = DropboxColors.TextPrimary, modifier = Modifier.weight(1f))
            if (done) Icon(Icons.Filled.CheckCircle, "Done", tint = DropboxColors.Success,
                modifier = Modifier.size(18.dp))
        }
        Box(
            Modifier
                .fillMaxWidth().height(3.dp)
                .clip(RoundedCornerShape(2.dp))
                .background(DropboxColors.Divider)
                .progressSemantics(animated)
                .drawBehind {
                    drawRect(
                        color = DropboxColors.Blue,
                        size = size.copy(width = size.width * animated),
                    )
                }
        )
    }
}
```

### Recent File Card

```kotlin
@Composable
fun DropboxRecentCard(
    name: String,
    meta: String,
    thumbUrl: String?,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .size(width = 140.dp, height = 160.dp)
            .clip(RoundedCornerShape(12.dp))
            .border(1.dp, DropboxColors.Divider, RoundedCornerShape(12.dp))
            .background(DropboxColors.Canvas)
            .padding(12.dp),
    ) {
        Box(
            Modifier.fillMaxWidth().height(96.dp).clip(RoundedCornerShape(4.dp))
                .background(DropboxColors.Surface),
            contentAlignment = Alignment.Center,
        ) {
            if (thumbUrl != null) {
                AsyncImage(thumbUrl, null, Modifier.fillMaxSize())
            } else {
                Icon(Icons.Filled.Description, null, tint = DropboxColors.Blue,
                    modifier = Modifier.size(28.dp))
            }
        }
        Spacer(Modifier.height(8.dp))
        Text(name, style = DropboxText.CardTitle, color = DropboxColors.TextPrimary,
            maxLines = 2, overflow = TextOverflow.Ellipsis)
        Text(meta, style = DropboxText.Caption.copy(fontFeatureSettings = "tnum"),
            color = DropboxColors.TextSecondary)
    }
}
```

## 4. Photo Grid (contact-sheet)

```kotlin
import androidx.compose.foundation.lazy.grid.GridCells
import androidx.compose.foundation.lazy.grid.LazyVerticalGrid
import androidx.compose.foundation.lazy.grid.items

@Composable
fun DropboxPhotoGrid(urls: List<String>) {
    var selected by remember { mutableStateOf(setOf<Int>()) }
    LazyVerticalGrid(
        columns = GridCells.Fixed(3),
        horizontalArrangement = Arrangement.spacedBy(2.dp),
        verticalArrangement = Arrangement.spacedBy(2.dp),
    ) {
        itemsIndexed(urls) { i, url ->
            Box(
                Modifier
                    .aspectRatio(1f)
                    .combinedClickable(
                        onClick = {},
                        onLongClick = {
                            selected = if (i in selected) selected - i else selected + i
                        },
                    ),
            ) {
                AsyncImage(url, null, Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
                if (i in selected) {
                    Box(Modifier.matchParentSize().border(4.dp, DropboxColors.Blue))
                    Icon(Icons.Filled.CheckCircle, "Selected", tint = DropboxColors.Blue,
                        modifier = Modifier.align(Alignment.TopEnd).padding(6.dp).size(20.dp))
                }
            }
        }
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Dropbox's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 94%-opaque canvas surface. **Active tint is Dropbox Blue.**

```kotlin
@Composable
fun DropboxBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = DropboxColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"    to Icons.Filled.Home,
            "Files"   to Icons.Filled.Folder,
            "Photos"  to Icons.Filled.PhotoLibrary,
            "Offline" to Icons.Filled.CloudDownload,
            "Account" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = DropboxText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = DropboxColors.Blue,
                    selectedTextColor   = DropboxColors.Blue,
                    unselectedIconColor = DropboxColors.TextSecondary,
                    unselectedTextColor = DropboxColors.TextSecondary,
                    indicatorColor      = DropboxColors.BlueTint,
                ),
            )
        }
    }
}
```

Anchor `DropboxUploadFab` in the `Scaffold` `floatingActionButton` slot with `FabPosition.End` so it floats bottom-right above the bar.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| FAB tap | `animateFloatAsState` 1 → 0.92 spring; `HapticFeedbackType.LongPress` (~iOS soft impact) |
| Primary button press | `animateFloatAsState` 1 → 0.98 |
| Upload progress | `animateFloatAsState(progress, tween(200))` — driven by real byte ratio, no shimmer |
| Star toggle | `Animatable` keyframes 1 → 1.12 → 1 over 240ms; `HapticFeedbackType.LongPress`; tint flips to Blue |
| Thumbnail decode | Coil `crossfade(200)` fade-in as tiles load |
| Sheet present | `ModalBottomSheet` default spring; scrim `Color(0xFF1E1919).copy(alpha = 0.40f)` |

```kotlin
// Star toggle bounce
@Composable
fun StarToggle(starred: Boolean, onToggle: () -> Unit) {
    val scale = remember { Animatable(1f) }
    LaunchedEffect(starred) {
        if (starred) {
            scale.animateTo(1.12f, tween(120)); scale.animateTo(1f, tween(120))
        }
    }
    val haptics = LocalHapticFeedback.current
    Icon(
        if (starred) Icons.Filled.Star else Icons.Outlined.StarBorder,
        contentDescription = "Star",
        tint = if (starred) DropboxColors.Blue else DropboxColors.TextSecondary,
        modifier = Modifier.size(22.dp).scale(scale.value).clickable {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress); onToggle()
        },
    )
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's `.soft` impact.

## 7. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Dropbox's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Upload / Create | `plus` | `Icons.Filled.Add` |
| PDF file | `doc.richtext.fill` | `Icons.Filled.PictureAsPdf` |
| Doc file | `doc.text.fill` | `Icons.Filled.Description` |
| Sheet file | `tablecells.fill` | `Icons.Filled.GridOn` |
| Image file | `photo.fill` | `Icons.Filled.Image` |
| Folder | `folder.fill` | `Icons.Filled.Folder` |
| Selected | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Folder chevron | `chevron.right` | `Icons.Filled.ChevronRight` |
| Star | `star` / `star.fill` | `Icons.Outlined.StarBorder` / `Icons.Filled.Star` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Download / Offline | `arrow.down.circle` | `Icons.Filled.CloudDownload` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house` | `Icons.Filled.Home` |
| Files (tab) | `folder` | `Icons.Filled.Folder` |
| Photos (tab) | `photo.on.rectangle` | `Icons.Filled.PhotoLibrary` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + Coil are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the light canvas wants dark system-bar icons via `WindowCompat`. Use `Scaffold` insets so the FAB clears the gesture nav and the upload bar clears the bottom bar.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on file names, metadata, body. Pin layout-sensitive text (tab labels, the 3.dp progress bar, the 56.dp FAB glyph) via `dp` or a fixed `Density` override.
- **Tabular numerals**: apply `fontFeatureSettings = "tnum"` to `Meta`/`Caption` so file sizes and dates align in columns.
- **TalkBack**: set `contentDescription` on the FAB (`"Upload or create"`); merge file-row name + metadata with `Modifier.semantics(mergeDescendants = true)` and mark the trailing overflow/chevron as a separate button. Add `progressSemantics()` to the upload bar.
- **Touch targets**: Material guidance is 48.dp minimum. The 56.dp FAB and 60.dp rows are clear; give the 18.dp ellipsis a 48.dp hit area via padding.
- **Contrast**: `#6F6A65` on `#FFFFFF` passes WCAG AA at 14sp+. On the dark canvas use `DarkBlue #3D8BFF` for blue text/icons over `#1E1919`. Validate 11sp tab/label text and bump toward `#5A554F` if targeting strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Dropbox's brand requires the fixed paper-white canvas and single Dropbox-Blue accent regardless of wallpaper.
