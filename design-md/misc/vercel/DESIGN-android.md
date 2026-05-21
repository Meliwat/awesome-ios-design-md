# Vercel (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Vercel's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the deployments list + build-log viewer, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Vercel's true-black canvas, the deployments list, the status traffic-light, Geist Sans/Mono, hairline borders instead of shadows) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a SwiftUI sheet, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars. No color extraction — Vercel's palette is fixed and monochrome, so Palette is not needed. Vercel is dark-mode-native; a light scheme is provided as a clean inversion.

## 1. Color Tokens

```kotlin
// ui/theme/VercelColors.kt
import androidx.compose.ui.graphics.Color

object VercelColors {
    // Canvas & Surfaces (Dark — default & brand)
    val Canvas   = Color(0xFF000000) // TRUE black — NOT #191919
    val Surface1 = Color(0xFF0A0A0A)
    val Surface2 = Color(0xFF111111)
    val Surface3 = Color(0xFF1A1A1A)
    val Divider  = Color(0xFF2E2E2E)
    val Border   = Color(0xFF333333)

    // Canvas & Surfaces (Light)
    val CanvasLight   = Color(0xFFFFFFFF)
    val SurfaceLight  = Color(0xFFFAFAFA)
    val SurfaceLight2 = Color(0xFFF2F2F2)
    val DividerLight  = Color(0xFFEAEAEA)
    val BorderLight   = Color(0xFFE1E1E1)

    // Text
    val TextPrimary   = Color(0xFFEDEDED)
    val TextSecondary = Color(0xFFA1A1A1)
    val TextTertiary  = Color(0xFF707070)
    val TextOnLight   = Color(0xFF000000)

    // Interactive
    val WhiteFill  = Color(0xFFEDEDED) // primary button fill (dark)
    val WhitePress = Color(0xFFCFCFCF)
    val Blue       = Color(0xFF0070F3)
    val BlueHover  = Color(0xFF3291FF)

    // Status (deployment state — the systemic accent)
    val Ready    = Color(0xFF0CCE6B)
    val Building = Color(0xFFF5A623)
    val ErrorRed = Color(0xFFE5484D)
    val ReadyDim = Color(0xFF0F3D2E)
    val ErrorDim = Color(0xFF3A1416)

    // Highlight (decorative only — avatar gradient / rare marketing)
    val HlPurple = Color(0xFF7928CA)
    val HlCyan   = Color(0xFF50E3C2)
    val HlPink   = Color(0xFFFF0080)
}
```

Wire it into both schemes. Vercel is dark-first (true black); the light scheme is a clean inversion.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val VercelDark = darkColorScheme(
    primary        = VercelColors.WhiteFill,    // white fill — no brand accent
    onPrimary      = VercelColors.Canvas,
    background     = VercelColors.Canvas,        // TRUE black
    onBackground   = VercelColors.TextPrimary,
    surface        = VercelColors.Surface1,
    onSurface      = VercelColors.TextPrimary,
    surfaceVariant = VercelColors.Surface2,
    outline        = VercelColors.Divider,
    error          = VercelColors.ErrorRed,
)

private val VercelLight = lightColorScheme(
    primary        = VercelColors.TextOnLight,
    onPrimary      = VercelColors.CanvasLight,
    background      = VercelColors.CanvasLight,
    onBackground   = VercelColors.TextOnLight,
    surface        = VercelColors.SurfaceLight,
    onSurface      = VercelColors.TextOnLight,
    surfaceVariant = VercelColors.SurfaceLight2,
    outline        = VercelColors.DividerLight,
    error          = VercelColors.ErrorRed,
)

@Composable
fun VercelTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) VercelDark else VercelLight,
    typography = VercelTypography,
    content = content,
)
```

## 2. Typography

Vercel's type system is **Geist Sans** (UI prose) + **Geist Mono** (every URL/branch/commit/env var/log). Both SIL OFL — drop the TTFs in `res/font/`. The split is by content type, never a user setting. Tight negative tracking on headings.

```kotlin
// ui/theme/VercelType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Geist = FontFamily(
    Font(R.font.geist_regular,   FontWeight.Normal),
    Font(R.font.geist_medium,    FontWeight.Medium),
    Font(R.font.geist_semibold,  FontWeight.SemiBold),
    Font(R.font.geist_bold,      FontWeight.Bold),
    Font(R.font.geist_extrabold, FontWeight.ExtraBold),
)
val GeistMono = FontFamily(
    Font(R.font.geist_mono_regular, FontWeight.Normal),
    Font(R.font.geist_mono_medium,  FontWeight.Medium),
)

// Named ramp — mirrors DESIGN.md §3 (pt → sp 1:1)
object VercelText {
    val Display    = TextStyle(Geist, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.6).sp)
    val Title      = TextStyle(Geist, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 30.sp, letterSpacing = (-0.4).sp)
    val Section    = TextStyle(Geist, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Subsection = TextStyle(Geist, fontWeight = FontWeight.SemiBold,  fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Body       = TextStyle(Geist, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val Label      = TextStyle(Geist, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val BodyStrong = TextStyle(Geist, fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val Meta       = TextStyle(Geist, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Caption    = TextStyle(Geist, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Tab        = TextStyle(Geist, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button     = TextStyle(Geist, fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 14.sp, letterSpacing = (-0.1).sp)
    // Mono
    val MonoUrl    = TextStyle(GeistMono, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 18.sp)
    val MonoLog    = TextStyle(GeistMono, fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 18.sp)
    val MonoTag    = TextStyle(GeistMono, fontWeight = FontWeight.Medium, fontSize = 10.sp, lineHeight = 12.sp)
    val MonoBadge  = TextStyle(GeistMono, fontWeight = FontWeight.Medium, fontSize = 12.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val VercelTypography = Typography(
    headlineLarge  = VercelText.Display,
    headlineMedium = VercelText.Title,
    titleMedium    = VercelText.Subsection,
    bodyMedium     = VercelText.Body,
    labelSmall     = VercelText.Tab,
)
```

## 3. Signature Components

### Deployment Card (the spine of the app)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.outlined.CallSplit
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

enum class DeployState(val color: Color, val label: String) {
    Ready(VercelColors.Ready, "Ready"),
    Building(VercelColors.Building, "Building"),
    Error(VercelColors.ErrorRed, "Error"),
}

@Composable
fun DeploymentCard(
    state: DeployState,
    env: String,           // "Production" / "Preview"
    url: String,
    branch: String,
    relativeTime: String,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(12.dp))
            .background(VercelColors.Surface1)
            .border(1.dp, VercelColors.Divider, RoundedCornerShape(12.dp)) // border, NOT shadow
            .padding(14.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            // 9dp dot + 3dp halo at 15% of the status color
            Box(
                Modifier.size(15.dp)
                    .clip(androidx.compose.foundation.shape.CircleShape)
                    .background(state.color.copy(alpha = 0.15f)),
                contentAlignment = Alignment.Center,
            ) { Box(Modifier.size(9.dp).clip(androidx.compose.foundation.shape.CircleShape).background(state.color)) }
            Text(
                state.label,
                style = VercelText.BodyStrong,
                color = if (state == DeployState.Ready) VercelColors.TextPrimary else state.color,
            )
            Spacer(Modifier.weight(1f))
            EnvTag(env)
        }
        Text(url, style = VercelText.MonoUrl, color = VercelColors.TextPrimary, maxLines = 1)
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Icon(Icons.Outlined.CallSplit, null, tint = VercelColors.TextTertiary, modifier = Modifier.size(12.dp))
            Text(branch, style = VercelText.MonoUrl, color = VercelColors.TextSecondary)
            Spacer(Modifier.weight(1f))
            Text(relativeTime, style = VercelText.Meta, color = VercelColors.TextTertiary)
        }
    }
}

@Composable
fun EnvTag(label: String) {
    Text(
        label,
        style = VercelText.MonoTag,
        color = VercelColors.TextSecondary,
        modifier = Modifier
            .clip(RoundedCornerShape(5.dp))
            .background(VercelColors.Surface3)
            .border(1.dp, VercelColors.Border, RoundedCornerShape(5.dp))
            .padding(horizontal = 7.dp, vertical = 3.dp),
    )
}
```

### Status Badge (pill)

```kotlin
import androidx.compose.foundation.shape.CircleShape

@Composable
fun StatusBadge(state: DeployState) {
    Row(
        Modifier
            .clip(CircleShape)
            .background(state.color.copy(alpha = 0.08f))
            .border(1.dp, state.color.copy(alpha = 0.4f), CircleShape)
            .padding(horizontal = 10.dp, vertical = 5.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Box(Modifier.size(7.dp).clip(CircleShape).background(state.color))
        Text(state.label, style = VercelText.MonoBadge, color = state.color)
    }
}
```

### Primary / Secondary Buttons

```kotlin
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.OutlinedButton
import androidx.compose.runtime.remember

@Composable
fun VercelPrimaryButton(title: String, onClick: () -> Unit) {
    val src = remember { MutableInteractionSource() }
    val pressed by src.collectIsPressedAsState()
    Button(
        onClick = onClick,
        interactionSource = src,
        shape = RoundedCornerShape(8.dp),
        colors = ButtonDefaults.buttonColors(
            containerColor = if (pressed) VercelColors.WhitePress else VercelColors.WhiteFill,
            contentColor = Color.Black,
        ),
        contentPadding = PaddingValues(horizontal = 20.dp, vertical = 11.dp),
    ) { Text(title, style = VercelText.Button) }
}

@Composable
fun VercelSecondaryButton(title: String, onClick: () -> Unit) {
    OutlinedButton(
        onClick = onClick,
        shape = RoundedCornerShape(8.dp),
        border = androidx.compose.foundation.BorderStroke(1.dp, VercelColors.Border),
        contentPadding = PaddingValues(horizontal = 18.dp, vertical = 10.dp),
    ) { Text(title, style = VercelText.Button.copy(fontWeight = FontWeight.Medium), color = VercelColors.TextPrimary) }
}
```

### Build-Log Viewer

```kotlin
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.foundation.lazy.rememberLazyListState
import androidx.compose.runtime.LaunchedEffect

data class LogLine(val n: Int, val text: String, val kind: Kind) {
    enum class Kind { Normal, Error, Success, Dim }
}

@Composable
fun BuildLogViewer(lines: List<LogLine>) {
    val listState = rememberLazyListState()
    LaunchedEffect(lines.size) { if (lines.isNotEmpty()) listState.animateScrollToItem(lines.lastIndex) }
    fun color(k: LogLine.Kind) = when (k) {
        LogLine.Kind.Normal -> VercelColors.TextPrimary
        LogLine.Kind.Error -> VercelColors.ErrorRed
        LogLine.Kind.Success -> VercelColors.Ready
        LogLine.Kind.Dim -> VercelColors.TextTertiary
    }
    LazyColumn(
        state = listState,
        modifier = Modifier.fillMaxSize().background(VercelColors.Canvas),
        contentPadding = PaddingValues(horizontal = 12.dp, vertical = 10.dp),
    ) {
        items(lines, key = { it.n }) { l ->
            Row(Modifier.padding(vertical = 1.dp), horizontalArrangement = Arrangement.spacedBy(12.dp)) {
                Text("${l.n}", style = VercelText.MonoLog, color = VercelColors.TextTertiary, modifier = Modifier.width(32.dp))
                Text(l.text, style = VercelText.MonoLog, color = color(l.kind))
            }
        }
    }
}
```

### Triangle Logomark

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Path

@Composable
fun VercelTriangle(size: androidx.compose.ui.unit.Dp = 22.dp, fill: Color = VercelColors.TextPrimary) {
    Canvas(Modifier.size(size, size * 0.855f)) {
        val p = Path().apply {
            moveTo(this@Canvas.size.width / 2f, 0f)
            lineTo(this@Canvas.size.width, this@Canvas.size.height)
            lineTo(0f, this@Canvas.size.height)
            close()
        }
        drawPath(p, fill)
    }
}
```

## 4. Navigation

Vercel has minimal chrome: a compact top bar and a 4-tab bottom strip. On Android, model the strip as a `NavigationBar` (no tint pill — active is pure white). The project switcher is a `ModalBottomSheet`.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun VercelBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = VercelColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Overview"    to Icons.Filled.Home,
            "Deployments" to Icons.Filled.List,
            "Analytics"   to Icons.Filled.ShowChart,
            "Settings"    to Icons.Filled.Settings,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, Modifier.size(21.dp)) },
                label = { Text(label, style = VercelText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = Color.White,            // pure white, no accent
                    selectedTextColor = Color.White,
                    unselectedIconColor = VercelColors.TextTertiary,
                    unselectedTextColor = VercelColors.TextTertiary,
                    indicatorColor = Color.Transparent,         // NO Material pill — Vercel has none
                ),
            )
        }
    }
}
```

The project switcher (`ModalBottomSheet`, `Surface1` container, 16.dp top radius) lists projects: name (`Label`) + production domain (`MonoUrl` `TextSecondary`) + trailing chevron, with a pinned search field. `sheetState` gives drag-dismiss for free.

## 5. Motion

Vercel motion is minimal and immediate. Borders + surface lightness signal elevation; there are essentially no shadows.

| Moment | Compose recipe |
|--------|----------------|
| Status transition (Building → Ready) | `animateColorAsState(targetValue = state.color, tween(240))`; soft success haptic |
| Building pulse | halo `scale` via `rememberInfiniteTransition` `tween(1200, easing = LinearEasing)` reverse |
| Button press | `collectIsPressedAsState` → swap container color + `scale 0.98` `tween(120)` |
| Sheet present | `ModalBottomSheet` default slide-up; scrim `scrimColor = Black.copy(alpha = 0.7f)` |
| Tab switch | instant content swap — NO slide (Vercel favors immediacy) |
| Pull-to-refresh | `PullToRefreshBox`; indicator tint `VercelColors.Blue` |
| Log auto-scroll | `LazyListState.animateScrollToItem(lastIndex)` on append |

```kotlin
// Status cross-fade — the canonical Vercel motion
val dotColor by animateColorAsState(targetValue = state.color, animationSpec = tween(240), label = "deployStatus")
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft success on a Ready transition and on destructive confirm. Auto-refresh is silent; only show a snackbar on error.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The Vercel triangle is a custom `Canvas` path, not an icon glyph.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Overview (tab) | `house` | `Icons.Filled.Home` |
| Deployments (tab) | `list.bullet` | `Icons.Filled.List` |
| Analytics (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.ShowChart` |
| Settings (tab) | `gearshape` | `Icons.Filled.Settings` |
| Git branch | `arrow.triangle.branch` | `Icons.Outlined.CallSplit` |
| Commit | `circle.dotted` | `Icons.Filled.Commit` |
| Domain | `globe` | `Icons.Filled.Language` |
| Redeploy | `arrow.clockwise` | `Icons.Filled.Refresh` |
| Visit / open | `arrow.up.right` | `Icons.Filled.OpenInNew` |
| Logs | `terminal` | `Icons.Filled.Terminal` |
| Copy | `doc.on.doc` | `Icons.Filled.ContentCopy` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Reveal env value | `eye` / `eye.slash` | `Icons.Filled.Visibility` / `VisibilityOff` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Success | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Error | `exclamationmark.triangle.fill` | `Icons.Filled.Warning` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `ModalBottomSheet` + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the true-black canvas wants light-content system bars. The compact top bar respects the camera cutout; the build-log and forms must respect the IME — use `Modifier.imePadding()`.
- **True black**: the dark `background` is `#000000`, NOT `#191919`. Do **not** enable Material You `dynamicColorScheme()` — Vercel's monochrome identity must hold regardless of wallpaper (there is no brand accent to harmonize with one).
- **No shadows**: elevation is a lighter surface (`Surface1`) + a 1dp `Divider` border. Set `tonalElevation = 0.dp` everywhere; never add `shadow()` to cards (invisible on black, off-brand).
- **Font scaling**: `sp` honors the user's font scale — keep it on Display/Title/Section/Body/Meta and the build-log Mono (stays monospace as it scales). Pin layout-sensitive text (10sp tab labels, env tags, mono badges, ≤12sp captions) via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: compose the deployment card into one announcement — "Deployment: {state}, {env}, {branch}, {relativeTime}" via `Modifier.semantics(mergeDescendants = true) { contentDescription = ... }`; the status dot is decorative.
- **Color is not the only signal**: every status dot is paired with its text label ("Ready"/"Building"/"Error"), so color-blind users never rely on hue.
- **Touch targets**: Material guidance is 48.dp. The deployment card is full-row tappable (≥64.dp); give icon buttons a 48.dp hit area; primary buttons ≥ 40.dp tall.
- **Contrast**: `#EDEDED` on `#000000` ≈ 18:1 (AAA). `#A1A1A1` on `#000000` passes AA for secondary. Status-on-dim badge pairs (`#0CCE6B` on `rgba(12,206,107,0.08)`) are validated.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the Building-pulse loop and snap the status color (no cross-fade); keep the success haptic and the selection highlight.
- **Logs performance**: `LazyColumn` (never a plain `Column`) for build logs — they can be thousands of lines; `animateScrollToItem(lastIndex)` to follow the tail.
- **Dark mode**: dark IS the brand default. The light scheme is a clean inversion — `#FFFFFF` canvas, `#000000` text, `#FAFAFA` cards, `#EAEAEA` borders; status colors stay identical.
