# DeepSeek (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports DeepSeek's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the reasoning trace + two-toggle composer, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (near-black calm, the single DeepSeek-Blue accent, the recessed reasoning trace with a blue left-bar, the dimmed-italic chain-of-thought subordinate to an upright answer, the DeepThink/Search toggle pair) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `AnimatedVisibility` instead of a SwiftUI transition, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. No color extraction — DeepSeek's palette is a fixed near-mono ramp + one accent, so Palette is not needed. DeepSeek ships both light and dark; a full dark scheme is primary. Do **not** enable Material You dynamic color — the single `#4D6BFE` accent must hold regardless of wallpaper.

## 1. Color Tokens

```kotlin
// ui/theme/DSColors.kt
import androidx.compose.ui.graphics.Color

object DSColors {
    // Canvas & Surfaces (Dark)
    val Canvas      = Color(0xFF0E0E10)
    val Surface1    = Color(0xFF1A1B1E)
    val Surface2    = Color(0xFF232428)
    val Surface3    = Color(0xFF2C2D31)
    val ReasoningBg = Color(0xFF16171A) // DARKER than canvas — recessed
    val Divider     = Color(0xFF2E2F33)

    // Canvas & Surfaces (Light)
    val CanvasLight      = Color(0xFFFFFFFF)
    val Surface1Light    = Color(0xFFF5F6F8)
    val ReasoningBgLight = Color(0xFFF7F8FA)
    val DividerLight     = Color(0xFFE4E6EB)

    // Text
    val TextPrimary    = Color(0xFFECECEC)
    val TextSecondary  = Color(0xFF9A9BA0)
    val TextTertiary   = Color(0xFF6A6B70)
    val ReasoningText  = Color(0xFF8A8B90) // dimmed italic chain-of-thought
    val TextPrimaryLight = Color(0xFF1A1B1E)

    // Brand — the single accent
    val Blue        = Color(0xFF4D6BFE)
    val BluePressed = Color(0xFF3B57E0)
    val BlueSoft    = Color(0xFF1E2240)
    val BlueSoftLight = Color(0xFFEBEFFF)
    val BlueToken   = Color(0xFFC5CDFF)

    // Semantic
    val Success = Color(0xFF2EBD85)
    val Error   = Color(0xFFE5484D)
}
```

Wire it into both schemes. DeepSeek is calm and near-mono; the accent is `Blue` in both.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val DSDark = darkColorScheme(
    primary        = DSColors.Blue,           // the single accent
    onPrimary      = Color(0xFFFFFFFF),
    background     = DSColors.Canvas,
    onBackground   = DSColors.TextPrimary,
    surface        = DSColors.Surface1,
    onSurface      = DSColors.TextPrimary,
    surfaceVariant = DSColors.Surface2,
    outline        = DSColors.Divider,
    error          = DSColors.Error,
)

private val DSLight = lightColorScheme(
    primary        = DSColors.Blue,
    onPrimary      = Color(0xFFFFFFFF),
    background     = DSColors.CanvasLight,
    onBackground   = DSColors.TextPrimaryLight,
    surface        = DSColors.Surface1Light,
    onSurface      = DSColors.TextPrimaryLight,
    surfaceVariant = Color(0xFFECEEF2),
    outline        = DSColors.DividerLight,
    error          = DSColors.Error,
)

@Composable
fun DSTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(                            // no dynamicColorScheme() — single fixed accent
    colorScheme = if (dark) DSDark else DSLight,
    typography  = DSTypography,
    content     = content,
)
```

## 2. Typography

DeepSeek uses the system sans (Inter, SIL OFL — drop TTFs in `res/font/`, including an italic for the reasoning trace). Body 400, headings 700/800. The reasoning trace is the only italic in the system.

```kotlin
// ui/theme/DSType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),
    Font(R.font.inter_italic,    FontWeight.Normal, FontStyle.Italic),
    Font(R.font.inter_medium,    FontWeight.Medium),
    Font(R.font.inter_semibold,  FontWeight.SemiBold),
    Font(R.font.inter_bold,      FontWeight.Bold),
    Font(R.font.inter_extrabold, FontWeight.ExtraBold),
)
val Mono = FontFamily.Monospace

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object DSText {
    val ScreenTitle   = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val EmptyHeadline = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 33.sp, letterSpacing = (-0.3).sp)
    val Section       = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Body          = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 24.sp)
    val Action        = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 15.sp)
    val CardTitle     = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Reasoning     = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 20.sp, fontStyle = FontStyle.Italic) // dimmed italic
    val Meta          = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Caption       = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp)
    val Tab           = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Placeholder   = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 20.sp)
    val CodeInline    = TextStyle(Mono,  fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val CodeBlock     = TextStyle(Mono,  fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 20.sp)
    val Citation      = TextStyle(Mono,  fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val DSTypography = Typography(
    headlineLarge  = DSText.ScreenTitle,
    headlineMedium = DSText.EmptyHeadline,
    titleMedium    = DSText.Section,
    bodyMedium     = DSText.Body,
    labelSmall     = DSText.Tab,
)
```

## 3. Signature Components

### Reasoning Trace (the signature)

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.animation.expandVertically
import androidx.compose.animation.fadeIn
import androidx.compose.animation.fadeOut
import androidx.compose.animation.shrinkVertically
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.AutoAwesome
import androidx.compose.material.icons.filled.KeyboardArrowDown
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.drawBehind
import androidx.compose.ui.draw.rotate
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.unit.dp

@Composable
fun ReasoningTrace(durationSeconds: Int, thought: String) {
    var collapsed by remember { mutableStateOf(false) }
    val rot by animateFloatAsState(if (collapsed) -90f else 0f, tween(150), label = "chevron")

    Column(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(10.dp))
            .background(DSColors.ReasoningBg)                 // DARKER than canvas
            .drawBehind {                                     // 2dp blue left-bar
                drawRect(DSColors.Blue, size = size.copy(width = 2.dp.toPx()), topLeft = Offset.Zero)
            }
            .padding(vertical = 12.dp, horizontal = 14.dp),
    ) {
        Row(
            Modifier
                .fillMaxWidth()
                .clickableNoRipple { collapsed = !collapsed },
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Icon(Icons.Filled.AutoAwesome, null, tint = DSColors.Blue, modifier = Modifier.size(14.dp))
            Text("Thought for $durationSeconds seconds", style = DSText.Caption, color = DSColors.TextSecondary)
            Text(if (collapsed) "· tap to expand" else "· tap to collapse",
                 style = DSText.Caption, color = DSColors.TextTertiary)
            Spacer(Modifier.weight(1f))
            Icon(Icons.Filled.KeyboardArrowDown, null, tint = DSColors.TextTertiary,
                 modifier = Modifier.size(14.dp).rotate(rot))
        }
        AnimatedVisibility(
            visible = !collapsed,
            enter = expandVertically(tween(200)) + fadeIn(tween(150)),
            exit  = shrinkVertically(tween(200)) + fadeOut(tween(120)),
        ) {
            Text(thought, style = DSText.Reasoning, color = DSColors.ReasoningText,  // dimmed ITALIC
                 modifier = Modifier.padding(top = 8.dp))
        }
    }
}
```

### DeepThink / Search Toggle Pill

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

fun Modifier.clickableNoRipple(onClick: () -> Unit): Modifier = composed {
    clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) { onClick() }
}

@Composable
fun TogglePill(title: String, icon: ImageVector, on: Boolean, onToggle: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(if (on) DSColors.BlueSoft else DSColors.Surface1)
            .border(BorderStroke(1.dp, if (on) DSColors.Blue else DSColors.Divider),
                    RoundedCornerShape(999.dp))
            .clickableNoRipple {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress); onToggle()
            }
            .padding(vertical = 7.dp, horizontal = 13.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(icon, null, tint = if (on) DSColors.Blue else DSColors.TextSecondary,
             modifier = Modifier.size(14.dp))
        Text(title, style = DSText.Action, color = if (on) DSColors.Blue else DSColors.TextSecondary)
    }
}
```

### User Bubble & Assistant Message

```kotlin
import androidx.compose.material.icons.filled.Adjust
import androidx.compose.material.icons.outlined.ContentCopy
import androidx.compose.material.icons.outlined.Refresh
import androidx.compose.material.icons.outlined.ThumbDown
import androidx.compose.material.icons.outlined.ThumbUp

@Composable
fun UserBubble(text: String) {
    Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.End) {
        Box(
            Modifier
                .widthIn(max = 280.dp)
                .clip(RoundedCornerShape(topStart = 18.dp, topEnd = 18.dp,
                                         bottomEnd = 4.dp, bottomStart = 18.dp)) // 4dp points at sender
                .background(DSColors.BlueSoft)
                .border(BorderStroke(1.dp, Color(0xFF2A3360)),
                        RoundedCornerShape(topStart = 18.dp, topEnd = 18.dp,
                                           bottomEnd = 4.dp, bottomStart = 18.dp))
                .padding(vertical = 12.dp, horizontal = 16.dp)
        ) { Text(text, style = DSText.Body, color = DSColors.TextPrimary) }
    }
}

@Composable
fun AssistantMessage(model: String, trace: (@Composable () -> Unit)?, answer: String) {
    Column(Modifier.fillMaxWidth()) {
        Row(verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(7.dp)) {
            Icon(Icons.Filled.Adjust, null, tint = DSColors.Blue, modifier = Modifier.size(18.dp))
            Text(model, style = DSText.Caption, color = DSColors.TextSecondary)
        }
        Spacer(Modifier.height(8.dp))
        trace?.invoke()
        if (trace != null) Spacer(Modifier.height(10.dp))
        Text(answer, style = DSText.Body, color = DSColors.TextPrimary)
        Spacer(Modifier.height(10.dp))
        Row(horizontalArrangement = Arrangement.spacedBy(16.dp)) {
            listOf(Icons.Outlined.ContentCopy, Icons.Outlined.ThumbUp,
                   Icons.Outlined.ThumbDown, Icons.Outlined.Refresh).forEach {
                Icon(it, null, tint = DSColors.TextTertiary, modifier = Modifier.size(16.dp))
            }
        }
    }
}
```

### Composer (toggle row + input pill)

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material.icons.filled.Search
import androidx.compose.material.icons.filled.Stop
import androidx.compose.ui.graphics.SolidColor
import androidx.compose.ui.text.input.TextFieldValue

@Composable
fun Composer(
    value: TextFieldValue,
    onValueChange: (TextFieldValue) -> Unit,
    deepThink: Boolean, search: Boolean, isStreaming: Boolean,
    onToggleDeepThink: () -> Unit, onToggleSearch: () -> Unit,
    onSend: () -> Unit, onStop: () -> Unit,
) {
    val haptics = LocalHapticFeedback.current
    Column(Modifier.padding(horizontal = 14.dp)) {
        Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            TogglePill("DeepThink (R1)", Icons.Filled.AutoAwesome, deepThink, onToggleDeepThink)
            TogglePill("Search", Icons.Filled.Search, search, onToggleSearch)
        }
        Spacer(Modifier.height(8.dp))
        Row(
            Modifier
                .fillMaxWidth().height(50.dp)
                .clip(RoundedCornerShape(25.dp))
                .background(DSColors.Surface1)
                .border(BorderStroke(1.dp, DSColors.Divider), RoundedCornerShape(25.dp))
                .padding(start = 18.dp, end = 7.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(10.dp),
        ) {
            Box(Modifier.weight(1f)) {
                if (value.text.isEmpty())
                    Text("Message DeepSeek", style = DSText.Placeholder, color = DSColors.TextTertiary)
                BasicTextField(
                    value = value, onValueChange = onValueChange,
                    textStyle = DSText.Body.copy(color = DSColors.TextPrimary),
                    cursorBrush = SolidColor(DSColors.Blue),
                )
            }
            Box(
                Modifier
                    .size(36.dp)
                    .clip(androidx.compose.foundation.shape.CircleShape)
                    .background(if (isStreaming) DSColors.Error else DSColors.Blue)
                    .clickableNoRipple {
                        if (isStreaming) onStop()
                        else { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onSend() }
                    },
                contentAlignment = Alignment.Center,
            ) {
                Icon(if (isStreaming) Icons.Filled.Stop else Icons.Filled.ArrowUpward,
                     null, tint = Color.White, modifier = Modifier.size(16.dp))
            }
        }
    }
}
```

## 4. Navigation

DeepSeek has minimal chrome: a 3-tab bottom strip and a thin top bar with the whale wordmark. On Android, model the strip as a `NavigationBar`. There is no tint pill — active is just `Blue`.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.ChatBubbleOutline
import androidx.compose.material.icons.filled.History
import androidx.compose.material.icons.filled.Settings

@Composable
fun DSBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = DSColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Chat"     to Icons.Filled.ChatBubbleOutline,
            "History"  to Icons.Filled.History,
            "Settings" to Icons.Filled.Settings,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = DSText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = DSColors.Blue,        // the single accent
                    selectedTextColor   = DSColors.Blue,
                    unselectedIconColor = DSColors.TextTertiary,
                    unselectedTextColor = DSColors.TextTertiary,
                    indicatorColor      = Color.Transparent,     // NO Material pill
                ),
            )
        }
    }
}
```

The top bar is a `CenterAlignedTopAppBar` (`containerColor = DSColors.Canvas`) with a leading history/hamburger `IconButton`, a centered title row (custom whale `Icon` 22dp `Blue` + "DeepSeek" where "Seek" is a `Blue` span via `buildAnnotatedString`), and a trailing new-chat `IconButton`. History is a `LazyColumn` of conversation rows grouped by date `Section` headers.

## 5. Motion

DeepSeek motion is quiet — 150–300ms ease. The trace recedes (darker fill), it never elevates.

| Moment | Compose recipe |
|--------|----------------|
| Toggle on/off | fill/border/content recolor on recompose; wrap in `animateColorAsState(tween(150))` for an explicit crossfade |
| Trace collapse/expand | chevron `animateFloatAsState` 0 → -90° `tween(150)`; body `expandVertically(tween(200)) + fadeIn` / `shrinkVertically + fadeOut` |
| Reasoning stream | append tokens to `thought` on a coroutine; live duration counter; a `#4D6BFE` caret blinks via `rememberInfiniteTransition` `tween(530, Reverse)` |
| Send → stop | swap `ArrowUpward` ↔ `Stop`, container `Blue` ↔ `Error` on `isStreaming` |
| Message append | new bubble `AnimatedVisibility` `slideInVertically { it } + fadeIn` `tween(200)` |
| Citation tap | `ModalBottomSheet` source list — Material default slide-up |
| Tab change | instant — no `AnimatedContent` |

```kotlin
// Trace expand — the canonical DeepSeek motion
AnimatedVisibility(
    visible = !collapsed,
    enter = expandVertically(animationSpec = tween(200)) + fadeIn(tween(150)),
    exit  = shrinkVertically(animationSpec = tween(200)) + fadeOut(tween(120)),
) { /* dimmed italic chain-of-thought */ }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on toggle engage and send. On copy, fire a confirm cue (`HapticFeedbackConstants.CONFIRM` on API 30+, fallback `KEYBOARD_TAP`). Streaming start/stop is silent except the button morph.

## 6. Icons

The whale logomark should be a bundled vector (`ImageVector` from an XML/SVG asset) for brand fidelity; everything else maps to `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Chat (tab) | `bubble.left.and.bubble.right` | `Icons.Filled.ChatBubbleOutline` |
| History (tab) | `clock.arrow.circlepath` | `Icons.Filled.History` |
| Settings (tab) | `gearshape` | `Icons.Filled.Settings` |
| DeepThink toggle | `brain` | `Icons.Filled.AutoAwesome` |
| Search toggle | `magnifyingglass` | `Icons.Filled.Search` |
| Whale / model glyph | custom whale asset | custom `ImageVector` (fallback `Icons.Filled.Adjust`) |
| Send | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Stop streaming | `stop.fill` | `Icons.Filled.Stop` |
| New chat | `square.and.pencil` | `Icons.Filled.EditNote` |
| Hamburger / history | `line.3.horizontal` | `Icons.Filled.Menu` |
| Copy | `doc.on.doc` | `Icons.Outlined.ContentCopy` |
| Thumbs up / down | `hand.thumbsup` / `hand.thumbsdown` | `Icons.Outlined.ThumbUp` / `Icons.Outlined.ThumbDown` |
| Regenerate | `arrow.clockwise` | `Icons.Outlined.Refresh` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Trace collapse | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Citation source | `link` | `Icons.Filled.Link` |
| Attach | `paperclip` | `Icons.Filled.AttachFile` |

## 7. (reserved — see §4 Navigation)

Navigation patterns (bottom bar, top bar, History list) are documented in §4. The source sheet for web-search citations is a Material `ModalBottomSheet` with `containerColor = DSColors.Surface1`, a 12dp top corner radius, and rows of `[n]` title + domain — opened when a `[n]` citation chip (`DSText.Citation`, `Blue` on a faint `BlueSoft` pill) is tapped.

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; per-corner `RoundedCornerShape`, `AnimatedVisibility`, modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; dark wants light-content system bars over `#0E0E10` (and dark-content in light mode). The composer (toggle row + input) must sit above the IME — use `Modifier.imePadding()` and `Modifier.navigationBarsPadding()`.
- **Both schemes**: ship `DSDark` (primary) and `DSLight`. The invariant in both: the reasoning-trace background is *recessed* relative to the canvas (`#16171A` on `#0E0E10`; `#F7F8FA` on `#FFFFFF`) — recession, not elevation. Do **not** enable `dynamicColorScheme()` (Material You) — the single `#4D6BFE` accent must hold regardless of wallpaper.
- **Single accent**: `#4D6BFE` is the only chrome color. Never add a second accent or a tinted active-tab indicator (active = `Blue` glyph+label, `indicatorColor = Transparent`).
- **Reasoning is italic**: the chain-of-thought always uses `FontStyle.Italic` + `ReasoningText`. Never render it upright or in `TextPrimary`.
- **Font scaling**: `sp` honors the user's font scale — keep it on message body, headings, reasoning text. Pin layout-sensitive text (10sp tab labels, citation chips, toggle text) via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Touch targets**: Material guidance is 48.dp. Give toggle pills, the trace header, and the 16dp action icons a 48.dp hit area via padding; the send button is 36.dp with a 48.dp hit; History rows are full-row tappable.
- **Contrast**: `#ECECEC` on `#0E0E10` is high; `#8A8B90` on `#16171A` is intentionally low-emphasis (reasoning) but still passes WCAG AA at 13sp; never rely on italic alone — the panel, blue left-bar, and TalkBack prefix carry "this is reasoning" too.
- **TalkBack**: label the trace "Reasoning, thought for {N} seconds, {collapsed/expanded}, double-tap to toggle"; read the chain-of-thought as one block prefixed "Model reasoning:"; toggles "DeepThink, {on/off}" / "Search, {on/off}"; while streaming, expose the live duration as `stateDescription`, not repeated announcements.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the trace expand/collapse animation and chevron rotation (plain `Crossfade`), and the caret blink; the send→stop morph becomes an instant swap.
- **Streaming state**: while the answer streams, the send control is a `#E5484D` stop button with `contentDescription = "Stop generating"`.
