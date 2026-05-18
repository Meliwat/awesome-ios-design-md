# Postman (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Postman's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the request builder + response viewer, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Postman's neutral-grey console, the HTTP-method color system, the single orange action accent, Inter/JetBrains Mono, syntax-colored JSON) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a SwiftUI sheet, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. No color extraction — Postman's palette is fixed (one brand orange + a semantic method set), so Palette is not needed. A full light scheme is provided alongside the default dark.

## 1. Color Tokens

```kotlin
// ui/theme/PostmanColors.kt
import androidx.compose.ui.graphics.Color

object PostmanColors {
    // Canvas & Surfaces (Dark — default)
    val Canvas      = Color(0xFF1A1A1A) // neutral grey — not black, not charcoal-blue
    val Surface1    = Color(0xFF212121)
    val Surface2    = Color(0xFF2A2A2A)
    val Surface3    = Color(0xFF333333)
    val CodeSurface = Color(0xFF161616) // JSON body — darker than canvas (data sinks)
    val Divider     = Color(0xFF3A3A3A)
    val Border      = Color(0xFF444444)

    // Canvas & Surfaces (Light)
    val CanvasLight  = Color(0xFFFFFFFF)
    val SurfaceLight = Color(0xFFF4F5F5)
    val CodeLight    = Color(0xFFFAFAFA)
    val DividerLight = Color(0xFFE1E3E3)
    val BorderLight  = Color(0xFFD6D8D8)

    // Text
    val TextPrimary   = Color(0xFFE8E8E8)
    val TextSecondary = Color(0xFFA6A6A6)
    val TextTertiary  = Color(0xFF6E6E6E)
    val TextOnLight   = Color(0xFF212121)

    // Brand (the single accent — actions only)
    val Orange        = Color(0xFFFF6C37)
    val OrangePressed = Color(0xFFE55A2B)
    val OrangeDim     = Color(0xFF3D2417)

    // HTTP method colors (dark — the semantic core)
    val Get    = Color(0xFF6BDD9A)
    val Post   = Color(0xFFFFE47A)
    val Put    = Color(0xFF74AEF6)
    val Patch  = Color(0xFFC0A8E1)
    val Delete = Color(0xFFF79090)
    val Options= Color(0xFFA6A6A6)

    // JSON syntax
    val JsonKey  = Color(0xFF74AEF6)
    val JsonStr  = Color(0xFF6BDD9A)
    val JsonNum  = Color(0xFFC0A8E1)
    val JsonPunc = Color(0xFF6E6E6E)
}

fun methodColor(m: String): Color = when (m.uppercase()) {
    "GET" -> PostmanColors.Get; "POST" -> PostmanColors.Post; "PUT" -> PostmanColors.Put
    "PATCH" -> PostmanColors.Patch; "DELETE" -> PostmanColors.Delete
    else -> PostmanColors.Options
}
fun statusClass(code: Int): Color = when (code) {
    in 200..299 -> PostmanColors.Get; in 300..399 -> PostmanColors.Put
    in 400..499 -> PostmanColors.Post; else -> PostmanColors.Delete
}
```

Wire it into both schemes. Postman is a console — neutral grey dark by default, a clean light scheme provided.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val PostmanDark = darkColorScheme(
    primary        = PostmanColors.Orange,       // the one accent — actions only
    onPrimary      = Color.White,
    background     = PostmanColors.Canvas,
    onBackground   = PostmanColors.TextPrimary,
    surface        = PostmanColors.Surface1,
    onSurface      = PostmanColors.TextPrimary,
    surfaceVariant = PostmanColors.Surface2,
    outline        = PostmanColors.Divider,
    error          = PostmanColors.Delete,
)

private val PostmanLight = lightColorScheme(
    primary        = PostmanColors.Orange,
    onPrimary      = Color.White,
    background      = PostmanColors.CanvasLight,
    onBackground   = PostmanColors.TextOnLight,
    surface        = PostmanColors.SurfaceLight,
    onSurface      = PostmanColors.TextOnLight,
    surfaceVariant = Color(0xFFEBECEC),
    outline        = PostmanColors.DividerLight,
    error          = Color(0xFFE5484D),
)

@Composable
fun PostmanTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) PostmanDark else PostmanLight,
    typography = PostmanTypography,
    content = content,
)
```

## 2. Typography

Postman pairs **Inter** (UI chrome) with **JetBrains Mono** (every URL/header/param/JSON/status/timing). Both SIL OFL — drop the TTFs in `res/font/`. The split is by content type, never a user setting.

```kotlin
// ui/theme/PostmanType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),
    Font(R.font.inter_medium,    FontWeight.Medium),
    Font(R.font.inter_semibold,  FontWeight.SemiBold),
    Font(R.font.inter_bold,      FontWeight.Bold),
    Font(R.font.inter_extrabold, FontWeight.ExtraBold),
)
val JetBrainsMono = FontFamily(
    Font(R.font.jetbrains_mono_regular, FontWeight.Normal),
    Font(R.font.jetbrains_mono_medium,  FontWeight.Medium),
    Font(R.font.jetbrains_mono_bold,    FontWeight.Bold),
)

// Named ramp — mirrors DESIGN.md §3 (pt → sp 1:1)
object PostmanText {
    val Display    = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.5).sp)
    val Title      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Subsection = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body       = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val Label      = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 20.sp)
    val Meta       = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Caption    = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.5.sp)
    val Tab        = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 14.sp, lineHeight = 14.sp)
    // Mono
    val Method     = TextStyle(JetBrainsMono, fontWeight = FontWeight.Bold,   fontSize = 13.sp, lineHeight = 14.sp, letterSpacing = 0.3.sp)
    val MonoData   = TextStyle(JetBrainsMono, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 18.sp)
    val MonoJson   = TextStyle(JetBrainsMono, fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 19.sp)
    val StatusCode = TextStyle(JetBrainsMono, fontWeight = FontWeight.Bold,   fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val PostmanTypography = Typography(
    headlineLarge  = PostmanText.Display,
    headlineMedium = PostmanText.Title,
    titleMedium    = PostmanText.Subsection,
    bodyMedium     = PostmanText.Body,
    labelSmall     = PostmanText.Tab,
)
```

## 3. Signature Components

### Method-Colored URL / Request Bar

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.SolidColor
import androidx.compose.ui.unit.dp

@Composable
fun RequestBar(
    method: String,
    url: String,
    onUrlChange: (String) -> Unit,
    onSend: () -> Unit,
    onPickMethod: () -> Unit,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .height(44.dp)
            .clip(RoundedCornerShape(10.dp))
            .background(PostmanColors.Surface1)
            .border(1.dp, PostmanColors.Border, RoundedCornerShape(10.dp)),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Box(
            Modifier
                .fillMaxHeight()
                .clickable { onPickMethod() }
                .padding(horizontal = 12.dp),
            contentAlignment = Alignment.Center,
        ) { Text(method, style = PostmanText.Method, color = methodColor(method)) }

        Box(Modifier.width(1.dp).fillMaxHeight().background(PostmanColors.Border))

        BasicTextField(
            value = url,
            onValueChange = onUrlChange,
            singleLine = true,
            textStyle = PostmanText.MonoData.copy(color = PostmanColors.TextPrimary),
            cursorBrush = SolidColor(PostmanColors.Orange),
            modifier = Modifier.weight(1f).padding(horizontal = 12.dp),
            decorationBox = { inner ->
                if (url.isEmpty()) Text("Enter request URL", style = PostmanText.MonoData, color = PostmanColors.TextTertiary)
                inner()
            },
        )

        Box(
            Modifier
                .fillMaxHeight()
                .clickable { onSend() }
                .background(PostmanColors.Orange)
                .padding(horizontal = 16.dp),
            contentAlignment = Alignment.Center,
        ) { Text("Send", style = PostmanText.Button, color = Color.White) }
    }
}
```

### HTTP Method Chip

```kotlin
@Composable
fun MethodChip(method: String) {
    val c = methodColor(method)
    Text(
        method,
        style = PostmanText.Method,
        color = c,
        modifier = Modifier
            .clip(RoundedCornerShape(6.dp))
            .background(c.copy(alpha = 0.12f))
            .padding(horizontal = 12.dp, vertical = 6.dp),
    )
}
```

### Status Code Chip

```kotlin
import androidx.compose.foundation.shape.CircleShape

@Composable
fun StatusChip(code: Int, label: String) {
    val c = statusClass(code)
    Text(
        "$code $label",
        style = PostmanText.StatusCode,
        color = c,
        modifier = Modifier
            .clip(CircleShape)
            .background(c.copy(alpha = 0.08f))
            .border(1.dp, c.copy(alpha = 0.4f), CircleShape)
            .padding(horizontal = 10.dp, vertical = 5.dp),
    )
}
```

### Key-Value Editor Row

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.CheckBox
import androidx.compose.material.icons.outlined.CheckBoxOutlineBlank
import androidx.compose.material3.Icon

@Composable
fun KeyValueRow(
    k: String, v: String, enabled: Boolean,
    onK: (String) -> Unit, onV: (String) -> Unit, onToggle: () -> Unit,
) {
    @Composable
    fun cell(value: String, onChange: (String) -> Unit, hint: String, color: Color, mod: Modifier) {
        BasicTextField(
            value = value, onValueChange = onChange, singleLine = true,
            textStyle = PostmanText.MonoData.copy(color = color),
            cursorBrush = SolidColor(PostmanColors.Orange),
            modifier = mod
                .clip(RoundedCornerShape(6.dp))
                .background(PostmanColors.Surface1)
                .border(1.dp, PostmanColors.Divider, RoundedCornerShape(6.dp))
                .padding(horizontal = 10.dp, vertical = 9.dp),
            decorationBox = { inner ->
                if (value.isEmpty()) Text(hint, style = PostmanText.MonoData, color = PostmanColors.TextTertiary)
                inner()
            },
        )
    }
    Row(
        Modifier.fillMaxWidth().padding(bottom = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(
            if (enabled) Icons.Filled.CheckBox else Icons.Outlined.CheckBoxOutlineBlank,
            contentDescription = if (enabled) "Disable row" else "Enable row",
            tint = if (enabled) PostmanColors.Orange else PostmanColors.TextTertiary,
            modifier = Modifier.size(20.dp).clickable { onToggle() },
        )
        cell(k, onK, "key", PostmanColors.JsonKey, Modifier.width(120.dp))
        cell(v, onV, "value", PostmanColors.TextPrimary, Modifier.weight(1f))
    }
}
```

### Response Viewer with Syntax-Highlighted JSON

```kotlin
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.verticalScroll
import androidx.compose.ui.text.AnnotatedString
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.withStyle

data class JsonTok(val text: String, val kind: Kind) { enum class Kind { Key, Str, Num, Punc, Plain } }

fun tokColor(k: JsonTok.Kind) = when (k) {
    JsonTok.Kind.Key -> PostmanColors.JsonKey
    JsonTok.Kind.Str -> PostmanColors.JsonStr
    JsonTok.Kind.Num -> PostmanColors.JsonNum
    JsonTok.Kind.Punc -> PostmanColors.JsonPunc
    JsonTok.Kind.Plain -> PostmanColors.TextPrimary
}

@Composable
fun ResponseViewer(code: Int, label: String, durationMs: Int, sizeText: String, tokens: List<JsonTok>) {
    Column {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 18.dp, vertical = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(10.dp),
        ) {
            StatusChip(code, label)
            Text("$durationMs ms", style = PostmanText.MonoJson, color = PostmanColors.TextSecondary)
            Text(sizeText, style = PostmanText.MonoJson, color = PostmanColors.TextSecondary)
        }
        val annotated: AnnotatedString = buildAnnotatedString {
            tokens.forEach { withStyle(SpanStyle(color = tokColor(it.kind))) { append(it.text) } }
        }
        Box(
            Modifier
                .fillMaxWidth()
                .padding(horizontal = 14.dp)
                .padding(bottom = 14.dp)
                .clip(RoundedCornerShape(8.dp))
                .background(PostmanColors.CodeSurface)            // data sinks darker
                .border(1.dp, PostmanColors.Divider, RoundedCornerShape(8.dp))
                .verticalScroll(rememberScrollState())
                .padding(12.dp),
        ) { Text(annotated, style = PostmanText.MonoJson) }
    }
}
```

## 4. Navigation

Postman has a 4-tab bottom strip and a request-editor tab strip. On Android, model the bottom strip as a `NavigationBar` (active = Postman Orange, no Material pill). The method picker and environment switcher are `ModalBottomSheet`s.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun PostmanBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = PostmanColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Request"      to Icons.Filled.SwapVert,
            "Collections"  to Icons.Filled.Folder,
            "History"      to Icons.Filled.History,
            "Environments" to Icons.Filled.FormatListBulleted,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, Modifier.size(21.dp)) },
                label = { Text(label, style = PostmanText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = PostmanColors.Orange,    // the one accent
                    selectedTextColor = PostmanColors.Orange,
                    unselectedIconColor = PostmanColors.TextTertiary,
                    unselectedTextColor = PostmanColors.TextTertiary,
                    indicatorColor = Color.Transparent,          // no Material pill — orange text is enough
                ),
            )
        }
    }
}
```

The request-editor strip (Params / Headers / Body / Auth / Pre-request / Tests) is a `ScrollableTabRow` with `indicator` a 2.dp `PostmanColors.Orange` underline; selected label `TextPrimary` w600, unselected `TextSecondary` w500; a small dot on a tab signals it has content.

## 5. Motion

Postman motion is functional — slide/cross-fade, 150–280ms. Color does the heavy lifting; animation only confirms state.

| Moment | Compose recipe |
|--------|----------------|
| Send → response | Send label `Crossfade` to a 14.dp `CircularProgressIndicator`; on result the response `Column` `slideInVertically(tween(220)) + fadeIn` |
| Status-chip pop | `animateFloatAsState` scale 0.9 → 1.0 `spring(dampingRatio = 0.7f, stiffness = 180f)` |
| Tab-strip underline | `ScrollableTabRow` default indicator slide `tween(200)` |
| Method picker → URL pill | `animateColorAsState(methodColor(method), tween(180))` on the pill text |
| JSON node collapse | chevron `animateFloatAsState` 0 → 90° `tween(150)`; children `expandVertically(tween(180))` |
| Pull-to-refresh | `PullToRefreshBox`; indicator tint `PostmanColors.Orange` |
| Test results | each row `AnimatedVisibility` staggered 40ms, `fadeIn() + scaleIn(initialScale = 0.9f)` |

```kotlin
// URL-pill method color cross-fade — the canonical Postman tell
val pillColor by animateColorAsState(targetValue = methodColor(method), animationSpec = tween(180), label = "methodColor")
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for the selection tick on method/tab change; `HapticFeedbackType.LongPress` for the Send tap; on response completion, fire a success/error pattern via `view.performHapticFeedback(HapticFeedbackConstants.CONFIRM / REJECT)`.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The Postman roundel is a custom shape (a `Box` + rotated bar, or a vector asset), not an icon glyph.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Request (tab) | `arrow.up.arrow.down` | `Icons.Filled.SwapVert` |
| Collections (tab) | `folder` | `Icons.Filled.Folder` |
| History (tab) | `clock` | `Icons.Filled.History` |
| Environments (tab) | `list.bullet.indent` | `Icons.Filled.FormatListBulleted` |
| Send | `paperplane.fill` | `Icons.Filled.Send` |
| Save | `square.and.arrow.down` | `Icons.Filled.SaveAlt` |
| Add request | `plus` | `Icons.Filled.Add` |
| Method chevron | `chevron.down` | `Icons.Filled.ExpandMore` |
| JSON node | `chevron.right` | `Icons.Filled.ChevronRight` (rotate 90°) |
| Copy | `doc.on.doc` | `Icons.Filled.ContentCopy` |
| Enable row | `checkmark.square.fill` / `square` | `Icons.Filled.CheckBox` / `Icons.Outlined.CheckBoxOutlineBlank` |
| Delete row | `trash` | `Icons.Filled.DeleteOutline` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Environment | `globe` | `Icons.Filled.Language` |
| Test pass | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Test fail | `xmark.circle.fill` | `Icons.Filled.Cancel` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `ModalBottomSheet` + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the neutral-grey canvas wants light-content system bars (dark-content in light mode). The request builder and key-value editors must respect the IME — use `Modifier.imePadding()`; the response pane collapses to make room.
- **Neutral grey, not Material You**: do **not** enable `dynamicColorScheme()` — Postman's identity is neutral grey + one orange + the fixed method palette; wallpaper-tinted surfaces would break the semantic color system. The canvas is `#1A1A1A`, NOT black or blue-charcoal.
- **Code surface sinks**: the response JSON `Box` uses `CodeSurface` (`#161616`) in both schemes — data sinks darker, controls float lighter (the depth inversion is a Postman tell). Don't elevate it.
- **Font scaling**: `sp` honors the user's font scale — keep it on Display/Title/Section/Body/Label/Meta and the JSON Mono (stays monospace as it scales, columns stay aligned). Pin layout-sensitive text (method pills, status chips, 10sp tab labels, ≤12sp mono) via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: announce a request row as "{method} request, {name}"; the method color is decorative — the method word carries the meaning. Announce a response as "Status {code} {label}, {duration} milliseconds, {size}". Merge the response header into one node via `Modifier.semantics(mergeDescendants = true)`.
- **Color is not the only signal**: method is always shown as text (`GET`/`POST`), status as the numeric code — never hue alone. This matters for the POST yellow `#FFE47A`, which only meets contrast on its chip fill, never as bare text on canvas — always render methods as chips.
- **Touch targets**: Material guidance is 48.dp. The method pill, Send button, and key-value cells are ≥44.dp tall; tab-strip items get a 48.dp hit area; the row enable checkbox gets 48.dp padding.
- **Contrast**: `#E8E8E8` on `#1A1A1A` ≈ 13:1 (AAA). Method hues on their 12% chip fills are validated; `#FFE47A` POST requires the chip fill to pass — see above.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the response slide-up and status-chip pop (snap/crossfade instead); keep the method-color change without the cross-fade duration.
- **JSON performance**: tokenize once into an `AnnotatedString`; for very large bodies render line-by-line in a `LazyColumn` rather than one giant `Text`.
- **Dark mode**: neutral grey is the brand default. The light scheme is a clean swap — `#FFFFFF` canvas, `#212121` text, `#F4F5F5` cards; switch method hues to the saturated light set (`#0CBB52` GET, `#1A73E8` PUT, `#7D4FC4` PATCH, `#E5484D` DELETE) but keep hue identity. Postman Orange `#FF6C37` is identical in both modes.
