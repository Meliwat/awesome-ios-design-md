# VSCO (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports VSCO's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the signature hairline slider / film-preset carousel / tool tray, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (VSCO's true-black darkroom, monochrome chrome, hairline bipolar sliders, UPPERCASE wide-tracked labels, the film-preset carousel as the creation gesture) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `pointerInput` drag instead of a SwiftUI `DragGesture`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for photo thumbnails. No color extraction — VSCO's palette is fixed monochrome, so Palette is not needed. **VSCO's editor is dark-native; there is no Material You dynamic color and no light editor theme.**

## 1. Color Tokens

```kotlin
// ui/theme/VSCOColors.kt
import androidx.compose.ui.graphics.Color

object VSCOColors {
    // Editor surfaces (dark — the editor's only mode)
    val Black     = Color(0xFF000000) // true black — NOT a charcoal
    val Surface1  = Color(0xFF0E0E0E)
    val Surface2  = Color(0xFF1A1A1A)
    val Surface3  = Color(0xFF242424)
    val Divider   = Color(0xFF2A2A2A)
    val Track     = Color(0xFF3A3A3A) // unfilled slider track

    // Browsing screens — light theme (NOT the editor)
    val LightCanvas   = Color(0xFFFFFFFF)
    val LightSurface1 = Color(0xFFF2F2F2)
    val LightSurface2 = Color(0xFFE8E8E8)
    val LightDivider  = Color(0xFFDCDCDC)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFB3B3B3)
    val TextTertiary  = Color(0xFF767676)
    val TextOnLight   = Color(0xFF000000)

    // Interactive — white only (VSCO has NO accent color)
    val White        = Color(0xFFFFFFFF)
    val WhitePressed = Color(0xFFDBDBDB)

    // Incidental film tones (preset-preview content only — never UI fills)
    val FilmWarm    = Color(0xFFC8A86B)
    val FilmCool    = Color(0xFF7E8FA1)
    val FilmNeutral = Color(0xFF9A9388)
    val FilmShadow  = Color(0xFF3F382C)

    // Semantic
    val Destructive = Color(0xFFE5484D)
}
```

Wire it into both schemes. VSCO's editor is **always** the dark scheme; the light scheme is only for the Studio / Discover / Profile browse screens.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val VSCODark = darkColorScheme(
    primary        = VSCOColors.White,          // interactive = white, no accent
    onPrimary      = VSCOColors.Black,
    background     = VSCOColors.Black,           // true black
    onBackground   = VSCOColors.TextPrimary,
    surface        = VSCOColors.Surface1,
    onSurface      = VSCOColors.TextPrimary,
    surfaceVariant = VSCOColors.Surface3,
    outline        = VSCOColors.Divider,
    error          = VSCOColors.Destructive,
)

private val VSCOLight = lightColorScheme(
    primary        = VSCOColors.TextOnLight,
    onPrimary      = VSCOColors.White,
    background      = VSCOColors.LightCanvas,
    onBackground   = VSCOColors.TextOnLight,
    surface        = VSCOColors.LightSurface1,
    onSurface      = VSCOColors.TextOnLight,
    surfaceVariant = VSCOColors.LightSurface2,
    outline        = VSCOColors.LightDivider,
    error          = VSCOColors.Destructive,
)

/** Editor flows MUST pass dark = true regardless of system. */
@Composable
fun VSCOTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) VSCODark else VSCOLight,
    typography = VSCOTypography,
    content = content,
)
```

## 2. Typography

VSCO's brand face is the proprietary "VSCO Gothic". Bundle **Inter** (SIL OFL) in `res/font/` as the closest free substitute. The signature is UPPERCASE chrome with wide `letterSpacing` — model that on the `TextStyle`, not the font file.

```kotlin
// ui/theme/VSCOType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextGeometricTransform
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
    Font(R.font.inter_bold,     FontWeight.Bold),
)

// Named ramp — mirrors DESIGN.md §3 (pt → sp 1:1). Chrome styles carry UPPERCASE intent;
// apply `.copy()` text to uppercase at the call site or use `String.uppercase()`.
object VSCOText {
    // Editorial (sentence-case content screens)
    val Display = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val Title   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp)
    val Section = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 19.sp, lineHeight = 25.sp, letterSpacing = (-0.1).sp)
    val Body    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val Label   = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 20.sp)
    val Caption = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)

    // Live numeric value
    val Value   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)

    // Chrome — UPPERCASE + wide tracking
    val ToolLabel = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, letterSpacing = 1.6.sp)
    val PresetTag = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, letterSpacing = 1.0.sp)
    val NavAction = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, letterSpacing = 1.4.sp)
    val TopTitle  = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 13.sp, letterSpacing = 1.4.sp)
    val TabLabel  = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, letterSpacing = 0.6.sp)
    val Button    = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, letterSpacing = 1.4.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val VSCOTypography = Typography(
    headlineLarge = VSCOText.Display,
    titleLarge    = VSCOText.Title,
    titleMedium   = VSCOText.Section,
    bodyMedium    = VSCOText.Body,
    labelSmall    = VSCOText.TabLabel,
)
```

## 3. Signature Components

### Hairline Bipolar Slider (the core atom)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.background
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.foundation.gestures.detectHorizontalDragGestures
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.layout.onSizeChanged
import androidx.compose.ui.platform.LocalDensity
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.shape.CircleShape
import androidx.compose.ui.unit.dp
import kotlin.math.abs

@Composable
fun HairlineSlider(
    toolName: String,
    value: Float,
    onValueChange: (Float) -> Unit,
    min: Float = -6f,
    max: Float = 6f,
    defaultValue: Float = 0f,
    modifier: Modifier = Modifier,
) {
    var trackPx by remember { mutableFloatStateOf(0f) }
    val density = LocalDensity.current
    val haptics = LocalHapticFeedback.current
    val fraction = (value - min) / (max - min)
    val zeroFraction = (0f - min) / (max - min)
    val animFraction by animateFloatAsState(fraction, tween(0), label = "knob")

    Column(modifier.padding(horizontal = 24.dp)) {
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
            Text(toolName.uppercase(), style = VSCOText.ToolLabel, color = VSCOColors.TextSecondary)
            Text(
                if (value >= 0) "+%.1f".format(value) else "%.1f".format(value),
                style = VSCOText.Value, color = VSCOColors.TextPrimary,
            )
        }
        Spacer(Modifier.height(12.dp))
        Box(
            Modifier
                .fillMaxWidth()
                .height(44.dp)
                .onSizeChanged { trackPx = it.width.toFloat() }
                .pointerInput(Unit) {
                    detectHorizontalDragGestures { change, _ ->
                        val f = (change.position.x / trackPx).coerceIn(0f, 1f)
                        onValueChange(min + f * (max - min))
                    }
                }
                .pointerInput(Unit) {
                    detectTapGestures(onDoubleTap = {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                        onValueChange(defaultValue)
                    })
                },
            contentAlignment = Alignment.CenterStart,
        ) {
            Box(Modifier.fillMaxWidth().height(1.dp).background(VSCOColors.Track))
            // white fill from zero-point to knob
            with(density) {
                val left = minOf(animFraction, zeroFraction) * trackPx
                val widthPx = abs(animFraction - zeroFraction) * trackPx
                Box(
                    Modifier
                        .offset(x = left.toDp())
                        .width(widthPx.toDp())
                        .height(1.dp)
                        .background(VSCOColors.White)
                )
                Box(
                    Modifier
                        .offset(x = (animFraction * trackPx).toDp() - 9.dp)
                        .size(18.dp)
                        .shadow(4.dp, CircleShape, spotColor = Color.Black.copy(alpha = 0.6f))
                        .background(VSCOColors.White, CircleShape)
                )
            }
        }
    }
}
```

### Film-Preset Carousel (the signature)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.horizontalScroll
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush

data class VSCOPreset(val code: String, val preview: List<Color>)

@Composable
fun PresetCarousel(
    presets: List<VSCOPreset>,
    selected: Int,
    onSelect: (Int) -> Unit,
) {
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier
            .horizontalScroll(rememberScrollState())
            .padding(horizontal = 20.dp, vertical = 14.dp),
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        presets.forEachIndexed { i, p ->
            val active = i == selected
            Column(
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.spacedBy(7.dp),
                modifier = Modifier.clickable {
                    onSelect(i)
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                },
            ) {
                Box(
                    Modifier
                        .size(56.dp)
                        .clip(RoundedCornerShape(2.dp))
                        .background(Brush.linearGradient(p.preview))
                        .then(
                            if (active) Modifier.border(2.dp, VSCOColors.White, RoundedCornerShape(2.dp))
                            else Modifier
                        )
                )
                Text(
                    p.code.uppercase(),
                    style = VSCOText.PresetTag,
                    color = if (active) VSCOColors.TextPrimary else VSCOColors.TextSecondary,
                )
            }
        }
    }
}
```

### Tool Tray

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.outlined.*
import androidx.compose.material3.Icon
import androidx.compose.ui.graphics.vector.ImageVector

data class VSCOTool(val icon: ImageVector, val label: String)

@Composable
fun ToolTray(tools: List<VSCOTool>, active: Int, onSelect: (Int) -> Unit) {
    Box(
        Modifier
            .fillMaxWidth()
            .height(84.dp)
            .background(VSCOColors.Black)
            .drawTopHairline(VSCOColors.Divider),
    ) {
        Row(
            Modifier
                .horizontalScroll(rememberScrollState())
                .padding(horizontal = 26.dp)
                .fillMaxHeight(),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(30.dp),
        ) {
            tools.forEachIndexed { i, t ->
                val tint = if (i == active) VSCOColors.TextPrimary else VSCOColors.TextSecondary
                Column(
                    horizontalAlignment = Alignment.CenterHorizontally,
                    verticalArrangement = Arrangement.spacedBy(8.dp),
                    modifier = Modifier
                        .widthIn(min = 44.dp)
                        .clickable { onSelect(i) },
                ) {
                    Icon(t.icon, contentDescription = t.label, tint = tint, modifier = Modifier.size(22.dp))
                    Text(t.label.uppercase(), style = VSCOText.ToolLabel, color = tint)
                }
            }
        }
    }
}

// 0.5dp top hairline helper
fun Modifier.drawTopHairline(color: Color) = drawWithContent {
    drawContent()
    drawLine(color, start = Offset(0f, 0f), end = Offset(size.width, 0f), strokeWidth = 1f)
}
```

## 4. Editor Top Bar

Notion-style minimal chrome equivalent: a 52.dp bar with a close glyph, an UPPERCASE tracked title, and a bare "NEXT" text action — no filled button on the bar.

```kotlin
import androidx.compose.material.icons.filled.Close

@Composable
fun EditorTopBar(onClose: () -> Unit, onNext: () -> Unit) {
    Row(
        Modifier
            .fillMaxWidth()
            .height(52.dp)
            .background(VSCOColors.Black)
            .padding(horizontal = 20.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.SpaceBetween,
    ) {
        Icon(
            Icons.Filled.Close, contentDescription = "Close",
            tint = VSCOColors.TextPrimary,
            modifier = Modifier.size(24.dp).clickable { onClose() },
        )
        Text("EDIT", style = VSCOText.TopTitle, color = VSCOColors.TextSecondary)
        Text(
            "NEXT", style = VSCOText.NavAction, color = VSCOColors.TextPrimary,
            modifier = Modifier.clickable { onNext() },
        )
    }
}
```

The Studio grid is a `LazyVerticalGrid(GridCells.Fixed(4))` on `Surface1` with `1.dp` item spacing, square `Coil` thumbnails, and **no corner radius** — a gapless contact sheet. Multi-select adds a `2.dp` white inset border + a white `Icons.Filled.Check`.

## 5. Navigation

VSCO has minimal chrome: a 4-tab bottom strip (icon-only) and full-screen flows. On Android model the strip as a `NavigationBar`. There is no tint pill — active is just white.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun VSCOBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = VSCOColors.Black,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            Icons.Filled.GridView,
            Icons.Filled.ArrowUpward,
            Icons.Filled.PhotoCamera,
            Icons.Filled.Person,
        )
        items.forEachIndexed { i, icon ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = null, modifier = Modifier.size(23.dp)) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = VSCOColors.White,       // white, no tint
                    unselectedIconColor = VSCOColors.TextTertiary,
                    indicatorColor      = Color.Transparent,      // NO Material pill — VSCO has none
                ),
            )
        }
    }
}
```

## 6. Motion

VSCO motion is a quiet 180–300ms cross-fade vocabulary; before/after is instant.

| Moment | Compose recipe |
|--------|----------------|
| Preset apply | two stacked `Image`s; top `graphicsLayer { alpha }` → `animateFloatAsState(tween(250, easing = FastOutSlowInEasing))` |
| Slider drag | 1:1 `detectHorizontalDragGestures`; value text updates live; soft haptic on zero-detent crossing |
| Slider double-tap reset | `onDoubleTap`; value `animateFloatAsState(tween(200))` back to default + soft haptic |
| Tool switch | carousel `slideOutHorizontally(tween(180))` + slider `fadeIn(tween(180))` (8dp offset) |
| Before/after press | `pointerInput` press-down shows original, release returns — `tween(0)` (no animation) |
| Tab switch | `Crossfade(tween(200))` — no slide |
| Sheet present | `ModalBottomSheet` slide up `tween(300)` |
| Multi-select toggle | white inset border + check `scaleIn(tween(150))` per tile |

```kotlin
// Preset cross-dissolve — the canonical VSCO motion
val topAlpha by animateFloatAsState(if (presetApplied) 1f else 0f, tween(250), label = "grade")
Box {
    Image(/* original */)
    Image(/* graded */, modifier = Modifier.graphicsLayer { alpha = topAlpha })
}
```

Haptics: use `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on preset selection, double-tap reset, and multi-select toggle. For the zero-detent tick while dragging, prefer `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`. Save-to-gallery is silent — show a "Saved" snackbar with no motion or haptic.

## 7. Icons

VSCO ships custom thin-line tool glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended` (Outlined variants). There is no brand-accent tint — every icon is `TextPrimary` (active) or `TextSecondary` (inactive).

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Close editor | `xmark` | `Icons.Filled.Close` |
| Filters tool | `camera.filters` | `Icons.Outlined.FilterVintage` |
| Exposure tool | `sun.max` | `Icons.Outlined.LightMode` |
| Contrast tool | `circle.lefthalf.filled` | `Icons.Outlined.Contrast` |
| Saturation tool | `drop` | `Icons.Outlined.WaterDrop` |
| Sharpen tool | `triangle` | `Icons.Outlined.Details` |
| Crop tool | `crop` | `Icons.Outlined.Crop` |
| Straighten tool | `crop.rotate` | `Icons.Outlined.Straighten` |
| Grain tool | `circle.grid.3x3` | `Icons.Outlined.Grain` |
| HSL tool | `paintpalette` | `Icons.Outlined.Palette` |
| Studio (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Feed (tab) | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Discover (tab) | `camera` | `Icons.Filled.PhotoCamera` |
| Profile (tab) | `person` | `Icons.Filled.Person` |
| Import | `square.and.arrow.down` | `Icons.Filled.FileDownload` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Recipe | `plus.rectangle.on.rectangle` | `Icons.Filled.AddBox` |
| Multi-select check | `checkmark` | `Icons.Filled.Check` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; gesture + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the editor wants light-content system bars on its true-black canvas. The photo stage must shrink to fit inside the system insets — apply `Modifier.windowInsetsPadding(WindowInsets.safeDrawing)` to the editor scaffold so the photo is never occluded by the top bar, tool tray, or gesture nav.
- **The editor is dark-native**: never pass `dark = false` to `VSCOTheme` for editing flows — a light editor breaks color judgement. Only Studio/Discover/Profile may follow `isSystemInDarkTheme()`.
- **No Material You**: do **not** enable `dynamicColorScheme()` — VSCO's monochrome darkroom identity must hold regardless of wallpaper; there is no brand accent to harmonize with one.
- **Font scaling**: `sp` honors the user's font scale — keep it on editorial titles, sections, body, captions. Pin layout-sensitive chrome (tool labels, preset codes, slider value, nav actions, 10sp tab labels) by deriving from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: give the slider `Modifier.semantics { role = Role.Slider; stateDescription = "$value"; setProgress { ... } }` so it is adjustable without the drag; preset tiles get `selected = active` semantics; the before/after press needs a `customActions = listOf(CustomAccessibilityAction("Show original", ...))`.
- **Touch targets**: Material guidance is 48.dp. The 18.dp slider knob, 22.dp tool glyphs, and 23.dp tab icons all need a 48.dp hit area via padding/`sizeIn`; Studio thumbnails are fully tappable; the top-bar "NEXT" action needs a 48.dp tap region.
- **Contrast**: `#FFFFFF` on `#000000` is maximal (AAA). Secondary `#B3B3B3` on black passes AA for the small UPPERCASE chrome at 11–12sp; reserve `#767676` for tertiary/disabled only.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace preset cross-fades and the tool-switch slide with an instant `Crossfade`/cut; the slider reset jumps; keep the white selection ring (it conveys state, not motion).
- **Slider knob shadow**: keep the `shadow(4.dp, ..., spotColor = Color.Black.copy(alpha = 0.6f))` — it is functional so the white knob stays visible over bright photo regions, not decorative.
- **Dark mode**: the editor is permanently `#000000` with `#FFFFFF` text/controls; only the browse theme inverts to `#FFFFFF` / `#000000`. Shadows are nearly invisible on true black — separation comes from the lighter `#1A1A1A` sheet surface, not elevation.
