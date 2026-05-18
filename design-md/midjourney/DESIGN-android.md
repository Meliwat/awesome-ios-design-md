# Midjourney (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Midjourney's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the job feed + prompt bar + U/V chip grammar, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (true-black canvas, white-as-only-primary, the 2×2 grid as hero, monospace prompts, the U1–U4 / V1–V4 chip grammar) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Popup` for the parameter strip, `Brush` shimmer instead of a SwiftUI gradient, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for grid images and avatars. No color extraction — Midjourney's shell is a fixed monochrome ramp, so Palette is not needed. **Midjourney is dark-only**; there is no light scheme — never enable Material You dynamic color.

## 1. Color Tokens

```kotlin
// ui/theme/MJColors.kt
import androidx.compose.ui.graphics.Color

object MJColors {
    // Canvas & Surfaces (dark-only — true OLED black)
    val Canvas   = Color(0xFF000000)
    val Surface1 = Color(0xFF0E0E0E)
    val Surface2 = Color(0xFF1A1A1A)
    val Surface3 = Color(0xFF242424)
    val Divider  = Color(0xFF2A2A2A)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFA1A1A1)
    val TextTertiary  = Color(0xFF6E6E6E)

    // Primary — white is the ONLY primary (no brand accent)
    val Primary        = Color(0xFFFFFFFF)
    val PrimaryPressed = Color(0xFFDADADA)

    // Heritage / link
    val RateBlue = Color(0xFF2D7FF9)
    val Blurple  = Color(0xFF4D5BCE) // Discord connect only

    // Semantic
    val Success = Color(0xFF2ECC71)
    val Warning = Color(0xFFF5A623)
    val Error   = Color(0xFFE5484D)
}
```

Wire it into the dark scheme only. Midjourney has no light mode; the canvas is true `#000000`, and there is no brand accent — `primary` is white.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val MJDark = darkColorScheme(
    primary        = MJColors.Primary,        // white — the only primary
    onPrimary      = MJColors.Canvas,         // black text on white
    background     = MJColors.Canvas,
    onBackground   = MJColors.TextPrimary,
    surface        = MJColors.Surface1,
    onSurface      = MJColors.TextPrimary,
    surfaceVariant = MJColors.Surface2,
    outline        = MJColors.Divider,
    error          = MJColors.Error,
)

@Composable
fun MJTheme(content: @Composable () -> Unit) =
    MaterialTheme(                            // dark-only — no light scheme, no dynamicColorScheme()
        colorScheme = MJDark,
        typography  = MJTypography,
        content     = content,
    )
```

## 2. Typography

Midjourney has two voices: a quiet sans for UI (Inter, SIL OFL — drop TTFs in `res/font/`) and **monospace for prompts and `--flags`** (`FontFamily.Monospace`). Body 400, headings 700/800; the type system is intentionally generic so it never competes with the grid.

```kotlin
// ui/theme/MJType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,    FontWeight.Normal),
    Font(R.font.inter_medium,     FontWeight.Medium),
    Font(R.font.inter_semibold,   FontWeight.SemiBold),
    Font(R.font.inter_bold,       FontWeight.Bold),
    Font(R.font.inter_extrabold,  FontWeight.ExtraBold),
)
val Mono = FontFamily.Monospace   // prompts & parameters — the "instruction" voice

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object MJText {
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val NavTitle    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 19.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val Action      = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 15.sp)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Placeholder = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Prompt      = TextStyle(Mono,  fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val Param       = TextStyle(Mono,  fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 16.sp)
    val JobChip     = TextStyle(Mono,  fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val MJTypography = Typography(
    headlineLarge  = MJText.ScreenTitle,
    headlineMedium = MJText.NavTitle,
    titleMedium    = MJText.Section,
    bodyMedium     = MJText.Body,
    labelSmall     = MJText.Tab,
)
```

## 3. Signature Components

### Job Card (prompt + 2×2 grid + action row)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.outlined.FavoriteBorder
import androidx.compose.material.icons.outlined.FileDownload
import androidx.compose.material.icons.filled.MoreHoriz
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.withStyle
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun JobCard(
    promptKey: String,
    promptRest: String,
    imageUrls: List<String>,            // 4 urls
    onUpscale: (Int) -> Unit,
    onFavorite: () -> Unit,
    onDownload: () -> Unit,
) {
    Column(
        Modifier.fillMaxWidth().background(MJColors.Canvas)
            .padding(horizontal = 14.dp, vertical = 9.dp)
    ) {
        Text(
            buildAnnotatedString {
                withStyle(SpanStyle(color = MJColors.TextPrimary)) { append(promptKey) }
                append(promptRest)
            },
            style = MJText.Prompt, color = MJColors.TextSecondary, maxLines = 2,
        )
        Spacer(Modifier.height(10.dp))
        Column(
            Modifier.clip(RoundedCornerShape(12.dp)),
            verticalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            repeat(2) { row ->
                Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                    repeat(2) { col ->
                        AsyncImage(
                            model = imageUrls[row * 2 + col],
                            contentDescription = "Image ${row * 2 + col + 1} of 4",
                            contentScale = ContentScale.Crop,
                            modifier = Modifier.weight(1f).aspectRatio(1f),
                        )
                    }
                }
            }
        }
        Spacer(Modifier.height(10.dp))
        Row(
            Modifier.fillMaxWidth(),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(14.dp),
        ) {
            Icon(Icons.Outlined.FavoriteBorder, "Favorite",
                tint = MJColors.TextSecondary,
                modifier = Modifier.size(18.dp).clickableNoRipple(onFavorite))
            Icon(Icons.Outlined.FileDownload, "Download",
                tint = MJColors.TextSecondary,
                modifier = Modifier.size(18.dp).clickableNoRipple(onDownload))
            Icon(Icons.Filled.MoreHoriz, "More",
                tint = MJColors.TextSecondary, modifier = Modifier.size(18.dp))
            Spacer(Modifier.weight(1f))
            (1..4).forEach { n -> UpscaleChip("U$n") { onUpscale(n) } }
        }
    }
}
```

### Upscale / Variation Chips (the Discord-bot grammar)

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.runtime.remember

fun Modifier.clickableNoRipple(onClick: () -> Unit): Modifier = composed {
    clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) { onClick() }
}

@Composable
fun UpscaleChip(label: String, onClick: () -> Unit) {       // white = do-it-now
    Box(
        Modifier
            .clip(RoundedCornerShape(8.dp))
            .background(MJColors.Primary)
            .clickableNoRipple(onClick)
            .padding(horizontal = 14.dp, vertical = 8.dp)
    ) { Text(label, style = MJText.JobChip, color = MJColors.Canvas) }
}

@Composable
fun VariationChip(label: String, onClick: () -> Unit) {     // dark = explore-from
    Box(
        Modifier
            .clip(RoundedCornerShape(8.dp))
            .background(MJColors.Surface2)
            .border(BorderStroke(1.dp, MJColors.Divider), RoundedCornerShape(8.dp))
            .clickableNoRipple(onClick)
            .padding(horizontal = 14.dp, vertical = 8.dp)
    ) { Text(label, style = MJText.JobChip, color = MJColors.TextPrimary) }
}
```

### Prompt Bar (signature input)

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.ArrowForward
import androidx.compose.runtime.*
import androidx.compose.ui.focus.onFocusChanged
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.input.TextFieldValue

@Composable
fun PromptBar(
    value: TextFieldValue,
    onValueChange: (TextFieldValue) -> Unit,
    onImagine: () -> Unit,
) {
    var focused by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current
    val empty = value.text.isBlank()

    Row(
        Modifier
            .padding(horizontal = 14.dp)
            .fillMaxWidth().height(50.dp)
            .clip(RoundedCornerShape(25.dp))
            .background(MJColors.Surface2)
            .border(BorderStroke(1.dp, if (focused) Color(0xFF3A3A3A) else MJColors.Divider),
                    RoundedCornerShape(25.dp))
            .padding(start = 18.dp, end = 7.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box(Modifier.weight(1f)) {
            if (empty) Text("What will you imagine?", style = MJText.Prompt, color = MJColors.TextTertiary)
            BasicTextField(
                value = value, onValueChange = onValueChange,
                textStyle = MJText.Prompt.copy(color = MJColors.TextPrimary),
                cursorBrush = androidx.compose.ui.graphics.SolidColor(MJColors.TextPrimary),
                modifier = Modifier.onFocusChanged { focused = it.isFocused },
            )
        }
        Box(
            Modifier
                .size(36.dp)
                .clip(androidx.compose.foundation.shape.CircleShape)
                .background(if (empty) MJColors.Surface3 else MJColors.Primary)
                .clickableNoRipple {
                    if (!empty) {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                        onImagine()
                    }
                },
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.ArrowForward, "Imagine", tint = MJColors.Canvas, modifier = Modifier.size(16.dp)) }
    }
}
```

### Parameter Helper Strip (Popup-anchored)

```kotlin
import androidx.compose.foundation.horizontalScroll
import androidx.compose.foundation.rememberScrollState

@Composable
fun ParameterStrip(onPick: (String) -> Unit) {
    val flags = listOf("--ar 1:1", "--ar 16:9", "--v 6", "--style raw", "--stylize 250")
    Row(
        Modifier.horizontalScroll(rememberScrollState()).padding(horizontal = 14.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        flags.forEach { f ->
            Box(
                Modifier
                    .clip(RoundedCornerShape(8.dp))
                    .background(MJColors.Surface2)
                    .border(BorderStroke(1.dp, MJColors.Divider), RoundedCornerShape(8.dp))
                    .clickableNoRipple { onPick(f) }
                    .padding(horizontal = 14.dp, vertical = 8.dp)
            ) { Text(f, style = MJText.Param, color = MJColors.TextPrimary) }
        }
    }
}
```

### Render-Progress Placeholder (shimmer)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush

@Composable
fun RenderingGrid(percent: Int) {
    val t = rememberInfiniteTransition(label = "shimmer")
    val shift by t.animateFloat(
        0f, 1000f,
        infiniteRepeatable(tween(1400, easing = LinearEasing), RepeatMode.Reverse),
        label = "shift",
    )
    Box(
        Modifier.fillMaxWidth().aspectRatio(1f).clip(RoundedCornerShape(12.dp)),
        contentAlignment = Alignment.Center,
    ) {
        Canvas(Modifier.matchParentSize()) {
            drawRect(Brush.linearGradient(
                colors = listOf(MJColors.Surface2, MJColors.Surface3, MJColors.Surface2),
                start = Offset(shift, 0f), end = Offset(shift + size.width, size.height),
            ))
        }
        Text("$percent%", style = MJText.Meta, color = MJColors.TextSecondary)
    }
}
```

### Primary Button (Imagine — white is the only primary)

```kotlin
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults

@Composable
fun ImagineButton(title: String, onClick: () -> Unit) {
    Button(
        onClick = onClick,
        shape = RoundedCornerShape(999.dp),
        colors = ButtonDefaults.buttonColors(
            containerColor = MJColors.Primary,   // white
            contentColor   = MJColors.Canvas,    // black label
        ),
        contentPadding = PaddingValues(horizontal = 28.dp, vertical = 14.dp),
    ) { Text(title, style = MJText.Action) }
}
```

## 4. Navigation

Midjourney has minimal chrome: a 5-tab bottom strip and a thin segmented control. On Android, model the strip as a `NavigationBar`. There is no tint pill — active is just white.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun MJBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = MJColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Create"  to Icons.Filled.Home,
            "Explore" to Icons.Filled.GridView,
            "Search"  to Icons.Filled.Search,
            "Library" to Icons.Filled.PhotoLibrary,
            "Profile" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(23.dp)) },
                label = { Text(label, style = MJText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = MJColors.TextPrimary,   // white, no accent
                    selectedTextColor   = MJColors.TextPrimary,
                    unselectedIconColor = MJColors.TextTertiary,
                    unselectedTextColor = MJColors.TextTertiary,
                    indicatorColor      = Color.Transparent,       // NO Material pill
                ),
            )
        }
    }
}

@Composable
fun MJSegmented(items: List<String>, selected: Int, onSelect: (Int) -> Unit) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 14.dp)
            .drawBottomBorder(MJColors.Divider, 0.5.dp),
        horizontalArrangement = Arrangement.spacedBy(22.dp),
    ) {
        items.forEachIndexed { i, label ->
            val active = i == selected
            Column(
                Modifier.clickableNoRipple { onSelect(i) }.padding(bottom = 8.dp),
                horizontalAlignment = Alignment.CenterHorizontally,
            ) {
                Text(label, style = MJText.Action,
                     color = if (active) MJColors.TextPrimary else MJColors.TextTertiary)
            }
        }
    }
}
```

(`drawBottomBorder` is a tiny `Modifier.drawBehind { drawLine(...) }` helper; the active segment also gets a 2dp underline drawn the same way.)

## 5. Motion

Midjourney motion is minimal — the spectacle is the render, not the chrome. Navigation is a silent color swap.

| Moment | Compose recipe |
|--------|----------------|
| Job submit | new card `AnimatedVisibility` `slideInVertically(tween(250)) { -it } + fadeIn`; Imagine button scale `0.96 → 1` `tween(120)` |
| Render shimmer | `rememberInfiniteTransition` linear gradient sweep `tween(1400, Reverse)` (see `RenderingGrid`) |
| Resolve | placeholder → images `Crossfade(tween(250))`, image `scale 0.98 → 1` |
| Upscale → lightbox | shared element via `SharedTransitionLayout` / `Modifier.sharedElement`, ~220ms; chip flashes `Primary → PrimaryPressed` 90ms |
| Lightbox paging | `HorizontalPager` 1:1 drag, `spring(dampingRatio = 0.85f, stiffness = 160f)` snap |
| Tab change | instant — no `AnimatedContent`, just recomposition |
| Parameter strip | `AnimatedVisibility` `fadeIn + slideInVertically { it/2 }` `tween(180)` on prompt focus |

```kotlin
// Job submit — the canonical Midjourney entrance
AnimatedVisibility(
    visible = true,
    enter = slideInVertically(tween(250)) { -it } + fadeIn(tween(250)),
) { JobCard(/* ... */) }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on Imagine submit; for the lighter chip tick use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`. On job completion fire a success cue (`HapticFeedbackConstants.CONFIRM` on API 30+, fallback `KEYBOARD_TAP`).

## 6. Icons

Midjourney's UI is icon-light — the brand lives in the 2×2 grid and the monospace `--flag` grammar, not iconography. Use `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Create (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Explore (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Library (tab) | `photo.on.rectangle` | `Icons.Filled.PhotoLibrary` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Imagine (send) | `arrow.right` | `Icons.Filled.ArrowForward` |
| Favorite | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Download | `square.and.arrow.down` | `Icons.Outlined.FileDownload` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Filter | `line.3.horizontal.decrease` | `Icons.Filled.Tune` |
| Re-roll | `arrow.clockwise` | `Icons.Filled.Refresh` |
| Zoom out | `arrow.down.backward.and.arrow.up.forward` | `Icons.Filled.OpenInFull` |
| Remix | `wand.and.stars` | `Icons.Filled.AutoFixHigh` |
| Vary | `square.on.square` | `Icons.Filled.FilterNone` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Discord connect | `bubble.left.and.bubble.right` | `Icons.Filled.Forum` |

## 7. (reserved — see §4 Navigation)

Navigation patterns (bottom bar, segmented control) are documented in §4. The lightbox is a full-screen `Dialog` (or destination) with `Modifier.background(MJColors.Canvas)`, a `HorizontalPager` of the four quadrants, and a bottom action `Row` (Upscale / Vary / Remix / Zoom Out / Download / Favorite) on a `Color.Black.copy(alpha = 0.7f)` scrim — no card, no elevation; the lightbox *replaces* rather than floats.

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Popup`, shared elements, modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; dark-only means light-content system bars over `#000000`. The prompt bar must sit above the IME — use `Modifier.imePadding()` on the composer and place the parameter-strip `Popup`/sheet above the keyboard.
- **Dark-only**: ship only `MJDark`. The canvas is true `#000000` (OLED black) — do **not** substitute `#0E0E0E`. Do **not** enable `dynamicColorScheme()` (Material You) — Midjourney has no brand accent to harmonize with a wallpaper, and white-as-only-primary must hold regardless.
- **No brand accent**: never add a tinted active-tab indicator or a colored primary button — white (`#FFFFFF`) is the only primary. Reserve `#4D5BCE` blurple strictly for the "Connect Discord" affordance.
- **Monospace prompts**: prompts and `--flags` always use `FontFamily.Monospace` — never the Inter sans face. This is a load-bearing brand signal.
- **Imagery is content**: render generated images via Coil at full color/full-bleed; never apply a `ColorFilter`, tint, or overlay for "theming". The 2×2 grid is the only color in the UI.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen title, body, metadata. Pin layout-sensitive text (10sp tab labels, job chips `U1/V1`, parameter chips, prompt monospace) via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` — the 2×2 grid layout is size-sensitive.
- **Touch targets**: Material guidance is 48.dp. Give the 13sp job chips and 18dp action icons a 48.dp hit area via padding; grid cells are full-cell tappable; the Imagine button is 36.dp with a 48.dp hit.
- **Contrast**: `#FFFFFF` on `#000000` is maximal; `#A1A1A1` on `#000000` passes WCAG AA for metadata at 14sp; `#6E6E6E` placeholder is intentionally low-emphasis (non-essential text only).
- **TalkBack**: label the job card "Job: {prompt}"; each grid cell "Image {n} of 4, double-tap to open"; expose U/V chips via clear `contentDescription` ("Upscale image {n}" / "Variations of image {n}"); the re-roll chip "Re-roll prompt".
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the render shimmer (static `#1A1A1A` placeholder + percentage), the resolve scale, and the lightbox spring — substitute a plain `Crossfade`.
- **Reduce transparency / battery**: the tab bar is solid `#000000` (no blur on Android anyway); the OLED-black canvas is also a deliberate power optimization — keep it pure black.
