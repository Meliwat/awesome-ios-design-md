# Canva (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Canva's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the tab bar with a centered create FAB, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Canva's bright workspace, the cyan→purple brand gradient, rounded everything, the lifted gradient create FAB, gold Pro) while making everything idiomatic Android — a `NavigationBar` with a center `FloatingActionButton`, `ModalBottomSheet` for tool/design pickers, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for template thumbnails. No color extraction — Canva's palette and gradients are fixed. A full dark scheme is provided (warm-neutral, never true black).

## 1. Color Tokens

```kotlin
// ui/theme/CanvaColors.kt
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.Brush

object CanvaColors {
    // Canvas & Surfaces (Light)
    val White        = Color(0xFFFFFFFF)
    val Workspace    = Color(0xFFF7F8FA)
    val PressedLight = Color(0xFFEEF0F4)
    val DividerLight = Color(0xFFE6E8EC)

    // Canvas & Surfaces (Dark)
    val CanvasDark = Color(0xFF18191B) // warm-neutral — NOT pure black
    val Surface1   = Color(0xFF1F2023)
    val Surface2   = Color(0xFF27282C)
    val Divider    = Color(0xFF303135)

    // Text
    val TextPrimary    = Color(0xFFECECEE) // dark
    val TextPrimaryLt  = Color(0xFF0D0E10) // light
    val TextSecondary  = Color(0xFFA4A6AD)
    val TextTertiary   = Color(0xFF6F7178)

    // Brand
    val Cyan          = Color(0xFF00C4CC)
    val Purple        = Color(0xFF7D2AE8)
    val Blue          = Color(0xFF3B5CFF)
    val PurplePressed = Color(0xFF6A1FD0)

    // Semantic
    val ProGold = Color(0xFFFFC24B)
    val Success = Color(0xFF1FC77C)
    val Error   = Color(0xFFFF5163)

    // The Canva brand gradient — logo, create FAB, primary CTA, hero rails
    val BrandGradient = Brush.linearGradient(listOf(Cyan, Purple))
    fun tile(a: Color, b: Color) = Brush.linearGradient(listOf(a, b))
    val TileInstagram    = tile(Cyan, Blue)                       // #00C4CC → #3B5CFF
    val TilePresentation = tile(Purple, Color(0xFFC13AE0))        // #7D2AE8 → #C13AE0
    val TileDoc          = tile(Color(0xFFFF7A59), ProGold)       // #FF7A59 → #FFC24B
    val TileWhiteboard   = tile(Success, Cyan)                    // #1FC77C → #00C4CC
}
```

Wire it into both schemes; the dark scheme uses warm-neutral `#18191B`, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val CanvaLight = lightColorScheme(
    primary        = CanvaColors.Purple,
    onPrimary      = CanvaColors.White,
    secondary      = CanvaColors.Cyan,
    background      = CanvaColors.Workspace,
    onBackground    = CanvaColors.TextPrimaryLt,
    surface         = CanvaColors.White,
    onSurface       = CanvaColors.TextPrimaryLt,
    surfaceVariant  = CanvaColors.PressedLight,
    outline         = CanvaColors.DividerLight,
    error           = CanvaColors.Error,
)

private val CanvaDark = darkColorScheme(
    primary        = CanvaColors.Purple,
    onPrimary      = CanvaColors.White,
    secondary      = CanvaColors.Cyan,
    background      = CanvaColors.CanvasDark,
    onBackground    = CanvaColors.TextPrimary,
    surface         = CanvaColors.Surface1,
    onSurface       = CanvaColors.TextPrimary,
    surfaceVariant  = CanvaColors.Surface2,
    outline         = CanvaColors.Divider,
    error           = CanvaColors.Error,
)

@Composable
fun CanvaTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) CanvaDark else CanvaLight,
    typography = CanvaTypography,
    content = content,
)
```

## 2. Typography

Canva Sans stand-in: **Plus Jakarta Sans** (SIL OFL) — drop the TTFs in `res/font/`. App chrome always uses this family; the hundreds of in-editor content fonts are design data, not UI. Heavy display weights (800) with tight negative tracking; friendly, confident.

```kotlin
// ui/theme/CanvaType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val PlusJakarta = FontFamily(
    Font(R.font.pjs_regular,   FontWeight.Normal),
    Font(R.font.pjs_medium,    FontWeight.Medium),
    Font(R.font.pjs_semibold,  FontWeight.SemiBold),
    Font(R.font.pjs_bold,      FontWeight.Bold),
    Font(R.font.pjs_extrabold, FontWeight.ExtraBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object CanvaText {
    val Display    = TextStyle(PlusJakarta, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val Greeting   = TextStyle(PlusJakarta, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section    = TextStyle(PlusJakarta, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Subsection = TextStyle(PlusJakarta, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body       = TextStyle(PlusJakarta, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val Label      = TextStyle(PlusJakarta, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Button     = TextStyle(PlusJakarta, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Meta       = TextStyle(PlusJakarta, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Chip       = TextStyle(PlusJakarta, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.1.sp)
    val Tab        = TextStyle(PlusJakarta, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val ProBadge   = TextStyle(PlusJakarta, fontWeight = FontWeight.ExtraBold, fontSize = 9.sp,  lineHeight = 11.sp, letterSpacing = 0.3.sp)
    val Tool       = TextStyle(PlusJakarta, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 14.sp, letterSpacing = 0.1.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val CanvaTypography = Typography(
    headlineLarge = CanvaText.Display,
    headlineMedium = CanvaText.Greeting,
    titleLarge    = CanvaText.Section,
    titleMedium   = CanvaText.Subsection,
    bodyMedium    = CanvaText.Body,
    labelSmall    = CanvaText.Tab,
)
```

## 3. Signature Components

### Home Header (Greeting + Avatar)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

@Composable
fun HomeHeader(name: String, initial: String) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 18.dp, vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Text("Hi, $name 👋", style = CanvaText.Greeting, color = CanvaColors.TextPrimary)
        Spacer(Modifier.weight(1f))
        Box(
            Modifier.size(34.dp).clip(CircleShape).background(CanvaColors.BrandGradient),
            contentAlignment = Alignment.Center,
        ) { Text(initial, color = CanvaColors.White, style = CanvaText.Label.copy(fontWeight = FontWeight.Bold)) }
    }
}
```

### Search Field

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Search
import androidx.compose.material3.Icon
import androidx.compose.runtime.*

@Composable
fun CanvaSearchField() {
    var q by remember { mutableStateOf("") }
    Row(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 18.dp)
            .height(46.dp)
            .clip(RoundedCornerShape(14.dp))
            .background(CanvaColors.Surface2)
            .padding(horizontal = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(Icons.Filled.Search, null, tint = CanvaColors.TextSecondary, modifier = Modifier.size(17.dp))
        BasicTextField(
            value = q, onValueChange = { q = it },
            textStyle = CanvaText.Label.copy(color = CanvaColors.TextPrimary, fontWeight = FontWeight.Normal),
            modifier = Modifier.weight(1f),
            decorationBox = { inner ->
                if (q.isEmpty()) Text("Search templates, photos…", style = CanvaText.Label, color = CanvaColors.TextSecondary, fontWeight = FontWeight.Normal)
                inner()
            },
        )
    }
}
```

### Design-Type Tile

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun DesignTypeTile(icon: ImageVector, label: String, gradient: Brush, onClick: () -> Unit) {
    Column(
        Modifier.width(76.dp).clickable(onClick = onClick),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(7.dp),
    ) {
        Box(
            Modifier.size(56.dp).clip(RoundedCornerShape(16.dp)).background(gradient),
            contentAlignment = Alignment.Center,
        ) { Icon(icon, null, tint = CanvaColors.White, modifier = Modifier.size(24.dp)) }
        Text(label, style = CanvaText.Chip, color = CanvaColors.TextSecondary, maxLines = 1)
    }
}
```

### Template Thumbnail (with PRO badge)

```kotlin
@Composable
fun TemplateThumb(gradient: Brush, isPro: Boolean, onClick: () -> Unit) {
    Box(
        Modifier
            .fillMaxWidth()
            .aspectRatio(3f / 4f)
            .clip(RoundedCornerShape(12.dp))
            .background(gradient)
            .clickable(onClick = onClick),
    ) {
        if (isPro) {
            Box(
                Modifier
                    .align(Alignment.TopEnd)
                    .padding(7.dp)
                    .clip(RoundedCornerShape(6.dp))
                    .background(CanvaColors.ProGold)
                    .padding(horizontal = 6.dp, vertical = 2.dp),
            ) { Text("PRO", style = CanvaText.ProBadge, color = Color(0xFF1A1205)) }
        }
    }
}
```

### Buttons + Create FAB

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material.icons.filled.Add

@Composable
fun PrimaryButton(title: String, onClick: () -> Unit) {
    Box(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(CanvaColors.BrandGradient)
            .clickable(onClick = onClick)
            .padding(horizontal = 28.dp, vertical = 14.dp),
        contentAlignment = Alignment.Center,
    ) { Text(title, style = CanvaText.Button, color = CanvaColors.White) }
}

@Composable
fun SolidButton(title: String, onClick: () -> Unit) {
    Button(
        onClick = onClick,
        shape = RoundedCornerShape(999.dp),
        colors = ButtonDefaults.buttonColors(containerColor = CanvaColors.Purple, contentColor = CanvaColors.White),
        contentPadding = PaddingValues(horizontal = 22.dp, vertical = 12.dp),
    ) { Text(title, style = CanvaText.Button.copy(fontSize = 15.sp)) }
}

@Composable
fun ProButton(title: String, onClick: () -> Unit) {
    Button(
        onClick = onClick,
        shape = RoundedCornerShape(999.dp),
        colors = ButtonDefaults.buttonColors(containerColor = CanvaColors.ProGold, contentColor = Color(0xFF1A1205)),
        contentPadding = PaddingValues(horizontal = 18.dp, vertical = 10.dp),
    ) { Text(title, style = CanvaText.ProBadge.copy(fontSize = 14.sp)) }
}

@Composable
fun CreateFab(onClick: () -> Unit) {
    Box(
        Modifier
            .offset(y = (-4).dp)
            .size(46.dp)
            .clip(RoundedCornerShape(16.dp))
            .background(CanvaColors.BrandGradient)
            .clickable(onClick = onClick),
        contentAlignment = Alignment.Center,
    ) { Icon(Icons.Filled.Add, "Create a design", tint = CanvaColors.White, modifier = Modifier.size(26.dp)) }
}
```

### Create-Design Bottom Sheet

```kotlin
import androidx.compose.foundation.lazy.grid.GridCells
import androidx.compose.foundation.lazy.grid.LazyVerticalGrid
import androidx.compose.foundation.lazy.grid.items
import androidx.compose.material3.ExperimentalMaterial3Api
import androidx.compose.material3.ModalBottomSheet
import androidx.compose.material3.rememberModalBottomSheetState

data class DType(val icon: ImageVector, val label: String, val grad: Brush)

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun CreateDesignSheet(types: List<DType>, onDismiss: () -> Unit, onPick: (DType) -> Unit) {
    ModalBottomSheet(
        onDismissRequest = onDismiss,
        containerColor = CanvaColors.Surface1,
        shape = RoundedCornerShape(topStart = 24.dp, topEnd = 24.dp),
        sheetState = rememberModalBottomSheetState(skipPartiallyExpanded = false),
    ) {
        Text("Create a design", style = CanvaText.Subsection, color = CanvaColors.TextPrimary,
             modifier = Modifier.padding(start = 20.dp, bottom = 16.dp))
        LazyVerticalGrid(
            columns = GridCells.Fixed(3),
            contentPadding = PaddingValues(horizontal = 20.dp, vertical = 8.dp),
            verticalArrangement = Arrangement.spacedBy(18.dp),
        ) {
            items(types) { t -> DesignTypeTile(t.icon, t.label, t.grad) { onPick(t) } }
        }
    }
}
```

## 4. Navigation

Canva's tab bar has a centered, lifted gradient create FAB — the one elevated element. Model it as a `Scaffold` with a `NavigationBar` and a center-docked `FloatingActionButton`. There is no Material tint pill — active is the primary text color.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun CanvaScaffold(selected: Int, onSelect: (Int) -> Unit, onCreate: () -> Unit, content: @Composable (PaddingValues) -> Unit) {
    Scaffold(
        containerColor = CanvaColors.CanvasDark,
        floatingActionButton = { CreateFab(onCreate) },
        floatingActionButtonPosition = FabPosition.Center,
        bottomBar = {
            NavigationBar(containerColor = CanvaColors.CanvasDark, tonalElevation = 0.dp) {
                val items = listOf(
                    "Home" to Icons.Filled.Home,
                    "Templates" to Icons.Filled.GridView,
                    null to null, // FAB notch
                    "Projects" to Icons.Filled.ViewList,
                    "You" to Icons.Filled.AccountCircle,
                )
                items.forEachIndexed { i, (label, icon) ->
                    if (icon == null) { Spacer(Modifier.weight(1f)); return@forEachIndexed }
                    NavigationBarItem(
                        selected = selected == i,
                        onClick = { onSelect(i) },
                        icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                        label = { Text(label!!, style = CanvaText.Tab) },
                        colors = NavigationBarItemDefaults.colors(
                            selectedIconColor = CanvaColors.TextPrimary,
                            selectedTextColor = CanvaColors.TextPrimary,
                            unselectedIconColor = CanvaColors.TextSecondary,
                            unselectedTextColor = CanvaColors.TextSecondary,
                            indicatorColor = Color.Transparent, // no Material pill — Canva has none
                        ),
                    )
                }
            }
        },
        content = content,
    )
}
```

Tool pickers, the design-type chooser and share flows are all `ModalBottomSheet`s with 24.dp top corners. The editor's contextual element toolbar is a horizontally scrollable `Row` of round controls anchored above the IME (`Modifier.imePadding()`), on a `Surface1` surface with 16.dp top corners.

## 5. Motion

Canva motion is friendly — 220–320ms ease-out, soft and confident.

| Moment | Compose recipe |
|--------|----------------|
| Create FAB tap → sheet | `ModalBottomSheet` slide-up (≈320ms); FAB `scale` `animateFloatAsState` → 0.92 on press |
| Design-type tile tap | `scale` `animateFloatAsState` → 0.96 `tween(120)`, then nav push `tween(300)` |
| Bottom sheet drag-dismiss | `ModalBottomSheet` `sheetState` — finger-tracked, spring release damping ≈ 0.85 |
| Template open | shared-element-style `AnimatedContent` zoom into the customize sheet `tween(320)` |
| Editor element select | contextual toolbar `slideInVertically(tween(220)) + fadeIn` |
| Tab active pop | selected icon `animateFloatAsState` scale → 1.05 `tween(120)` |
| Gradient button press | `graphicsLayer` alpha 0.93 + `scale` 0.98 `tween(100)` |
| Skeleton shimmer | `rememberInfiniteTransition` sweep over 1200ms `LinearEasing` |

```kotlin
// Design-type tile press scale — the canonical Canva micro-interaction
val scale by animateFloatAsState(if (pressed) 0.96f else 1f, tween(120), label = "tileScale")
Modifier.graphicsLayer { scaleX = scale; scaleY = scale }
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for the light tick on FAB tap, tile tap and element select. Use `HapticFeedbackConstants.CONFIRM` on export/publish complete. Auto-save is silent — only a snackbar on manual save or error.

## 6. Icons

Canva ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. The brand gradient marks brand + primary action; Pro is gold.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` | `Icons.Filled.Home` |
| Templates (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Create FAB | `plus` | `Icons.Filled.Add` |
| Projects (tab) | `list.bullet.rectangle` | `Icons.Filled.ViewList` |
| You (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Pro / premium | `crown.fill` | `Icons.Filled.WorkspacePremium` |
| Instagram type | `camera.fill` | `Icons.Filled.PhotoCamera` |
| Presentation type | `play.rectangle` | `Icons.Filled.Slideshow` |
| Doc type | `doc.text` | `Icons.Filled.Description` |
| Whiteboard type | `square.dashed` | `Icons.Filled.Dashboard` |
| Video type | `play.circle.fill` | `Icons.Filled.PlayCircle` |
| Editor — back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Editor — undo / redo | `arrow.uturn.backward` / `.forward` | `Icons.Filled.Undo` / `Icons.Filled.Redo` |
| Editor — share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Tool — Elements | `square.on.circle` | `Icons.Filled.Category` |
| Tool — Text | `textformat` | `Icons.Filled.TextFields` |
| Tool — Uploads | `arrow.up.circle` | `Icons.Filled.CloudUpload` |
| Tool — Photos | `photo.on.rectangle` | `Icons.Filled.Image` |
| Tool — Draw | `pencil.tip` | `Icons.Filled.Brush` |
| Element — color | `paintpalette.fill` | `Icons.Filled.Palette` |
| Element — animate | `sparkles` | `Icons.Filled.AutoAwesome` |
| Element — delete | `trash` | `Icons.Filled.Delete` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `ModalBottomSheet` + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the dark canvas wants light-content system bars (dark-content on the light theme). The editor's contextual element toolbar must sit above the IME — use `Modifier.imePadding()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on greeting, section titles, body, meta. Pin layout-sensitive text (10sp tab labels, 12sp chip labels, 9sp Pro badge, 11sp tool labels) via `dp`-derived sizes or a fixed-`fontScale` `LocalDensity`.
- **App vs content fonts**: app chrome is always Plus Jakarta Sans; the hundreds of in-editor content fonts are design data — load on demand for the canvas only, never for UI.
- **TalkBack**: label the create FAB "Create a design"; design-type tiles "{Label} template, button"; template thumbs "{name}, Pro" when locked; the Pro badge needs a `contentDescription`. Mark section headers as `heading()`.
- **Touch targets**: Material guidance is 48.dp. The create FAB is 46.dp (give it a 48.dp hit via padding); design-type tiles are 76.dp wide and fully tappable; tool-rail items get a 48.dp hit area.
- **Contrast**: `#0D0E10` on `#FFFFFF` and `#ECECEE` on `#18191B` pass WCAG AA. Verify white text/icons over the lighter cyan end of gradients (`#00C4CC`) — add a subtle dark scrim behind label text on light thumbnails.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the FAB/tile scale pops and the skeleton shimmer; keep sheets as a plain fade.
- **Dark mode**: invert via the `Dark*` palette — warm-neutral `#18191B`, NOT true black. Never desaturate content thumbnails — only chrome dims. Sheets add a faint 1dp `Divider` top border since shadows nearly vanish on dark. Do **not** enable Material You `dynamicColorScheme()` — the cyan→purple brand gradient and gold Pro must hold regardless of wallpaper.
