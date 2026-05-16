# Microsoft Copilot (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Copilot's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, the flourish gradient, an acrylic surface, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Copilot's warm Fluent workspace, acrylic surfaces, flourish + Fluent Blue duality, answer cards, tone selector, drawer-only nav) while making everything idiomatic Android — `ModalNavigationDrawer` instead of a UISplitView, a `RenderEffect` blur (API 31+) for acrylic, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24` (acrylic blur is best on API 31+; graceful tinted fallback below), Material 3.

## 1. Color Tokens

```kotlin
// ui/theme/CopilotColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object CopilotColors {
    // Light
    val Canvas  = Color(0xFFFFFFFF)
    val Surface = Color(0xFFF3F3F3)
    val Divider = Color(0xFFE0E0E0)
    val TextPrimary   = Color(0xFF242424)
    val TextSecondary = Color(0xFF616161)
    val TextTertiary  = Color(0xFF919191)

    // Dark
    val DarkCanvas  = Color(0xFF202020)
    val DarkSurface = Color(0xFF2D2D2D)
    val DarkDivider = Color(0xFF3A3A3A)
    val DarkTextPrimary   = Color(0xFFFFFFFF)
    val DarkTextSecondary = Color(0xFFA6A6A6)

    // Brand
    val Blue        = Color(0xFF0078D4)
    val BluePressed = Color(0xFF005A9E)
    val BlueTint    = Color(0xFFDEECF9)
    val DarkBlue    = Color(0xFF4DA3E0)
    val Coral       = Color(0xFFFF6F61)
    val Gold        = Color(0xFFFFB900)

    // Semantic
    val Success = Color(0xFF107C10)
    val Warning = Color(0xFFF7630C)
    val Error   = Color(0xFFC50F1F)
}

// The single warm brand gesture — flourish mark, streaming edge, focus emphasis.
fun copilotFlourish() = Brush.linearGradient(listOf(CopilotColors.Coral, CopilotColors.Gold))
```

Wire it into Material 3 light + dark schemes. `primary` is solid Fluent Blue (the flourish gradient is structural, never a component fill).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val CopilotLight = lightColorScheme(
    primary        = CopilotColors.Blue,
    onPrimary      = Color.White,
    background     = CopilotColors.Canvas,
    onBackground   = CopilotColors.TextPrimary,
    surface        = CopilotColors.Canvas,
    onSurface      = CopilotColors.TextPrimary,
    surfaceVariant = CopilotColors.Surface,
    outline        = CopilotColors.Divider,
    error          = CopilotColors.Error,
)

private val CopilotDark = darkColorScheme(
    primary        = CopilotColors.DarkBlue,
    onPrimary      = Color(0xFF202020),
    background     = CopilotColors.DarkCanvas,
    onBackground   = CopilotColors.DarkTextPrimary,
    surface        = CopilotColors.DarkCanvas,
    onSurface      = CopilotColors.DarkTextPrimary,
    surfaceVariant = CopilotColors.DarkSurface,
    outline        = CopilotColors.DarkDivider,
    error          = CopilotColors.Error,
)

@Composable
fun CopilotTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) CopilotDark else CopilotLight,
    typography  = CopilotTypography,
    content     = content,
)
```

## 2. Typography

Segoe UI is Microsoft's product typeface. The closest free substitute is **Inter** — drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Use Cascadia Code for code. Fall back to the system grotesque (Roboto) if Inter is unavailable.

```kotlin
// ui/theme/CopilotType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val SegoeUI = FontFamily(
    Font(R.font.segoe_ui_regular,  FontWeight.Normal),   // 400
    Font(R.font.segoe_ui_semibold, FontWeight.SemiBold), // 600
    Font(R.font.segoe_ui_bold,     FontWeight.Bold),     // 700
)
val CascadiaCode = FontFamily(Font(R.font.cascadia_code_regular, FontWeight.Normal))

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object CopilotText {
    val Greeting    = TextStyle(SegoeUI, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.2).sp)
    val Title       = TextStyle(SegoeUI, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.1).sp)
    val Section     = TextStyle(SegoeUI, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 23.sp)
    val AnswerH2    = TextStyle(SegoeUI, fontWeight = FontWeight.SemiBold, fontSize = 20.sp, lineHeight = 26.sp)
    val AnswerH3    = TextStyle(SegoeUI, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 23.sp)
    val AnswerBody  = TextStyle(SegoeUI, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp) // ≈ 1.5
    val UserTurn    = TextStyle(SegoeUI, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 23.sp)
    val PromptInput = TextStyle(SegoeUI, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp)
    val Chip        = TextStyle(SegoeUI, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 18.sp)
    val Tone        = TextStyle(SegoeUI, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.1.sp)
    val Meta        = TextStyle(SegoeUI, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Code        = TextStyle(CascadiaCode, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 21.sp)
    val LabelUpper  = TextStyle(SegoeUI, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Button      = TextStyle(SegoeUI, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 19.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val CopilotTypography = Typography(
    headlineLarge = CopilotText.Greeting,
    headlineSmall = CopilotText.Title,
    titleMedium   = CopilotText.Section,
    bodyMedium    = CopilotText.AnswerBody,
    labelSmall    = CopilotText.Meta,
)
```

## 3. Signature Components

### Acrylic Surface Modifier

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.alpha
import androidx.compose.ui.draw.blur
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp

// Fluent acrylic ≈ a tinted translucent fill + hairline + a blur-in on appear.
// True backdrop blur needs Modifier.graphicsLayer { renderEffect = ... } (API 31+);
// the tint + hairline below reads as acrylic on all supported APIs.
fun Modifier.copilotAcrylic(
    cornerRadius: Dp = 16.dp,
    dark: Boolean = false,
): Modifier = this
    .clip(RoundedCornerShape(cornerRadius))
    .background((if (dark) CopilotColors.DarkSurface else CopilotColors.Surface).copy(alpha = 0.85f))
    .border(
        1.dp,
        (if (dark) CopilotColors.DarkDivider else CopilotColors.Divider).copy(alpha = 0.6f),
        RoundedCornerShape(cornerRadius),
    )

@Composable
fun AcrylicAppear(content: @Composable (Modifier) -> Unit) {
    var shown by remember { mutableStateOf(false) }
    LaunchedEffect(Unit) { shown = true }
    val a by animateFloatAsState(if (shown) 1f else 0f, tween(220), label = "acrylicIn")
    val b by animateFloatAsState(if (shown) 0f else 8f, tween(220), label = "acrylicBlur")
    content(Modifier.alpha(a).blur(b.dp))
}
```

### Flourish Mark

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.size
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Path

@Composable
fun CopilotFlourish(size: Dp = 22.dp, modifier: Modifier = Modifier) {
    Canvas(modifier.size(size)) {
        val w = this.size.width; val h = this.size.height
        // Stylized swirl/flourish — a ribbon arc with a tail
        val p = Path().apply {
            moveTo(w * 0.15f, h * 0.7f)
            cubicTo(w * 0.0f, h * 0.35f, w * 0.4f, h * 0.05f, w * 0.7f, h * 0.25f)
            cubicTo(w * 1.0f, h * 0.45f, w * 0.7f, h * 0.95f, w * 0.4f, h * 0.8f)
            cubicTo(w * 0.25f, h * 0.72f, w * 0.5f, h * 0.5f, w * 0.65f, h * 0.55f)
        }
        drawPath(
            p,
            brush = copilotFlourish(),
            style = androidx.compose.ui.graphics.drawscope.Stroke(width = size.toPx() * 0.16f),
        )
    }
}
```

### Answer Card

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material.icons.outlined.ContentCopy
import androidx.compose.material.icons.outlined.ThumbDownOffAlt
import androidx.compose.material.icons.outlined.ThumbUpOffAlt
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.shadow

@Composable
fun CopilotAnswerCard(
    text: String,
    streaming: Boolean,
    dark: Boolean = false,
    modifier: Modifier = Modifier,
) {
    AcrylicAppear { appearMod ->
        Column(
            modifier
                .fillMaxWidth()
                .padding(horizontal = 16.dp)
                .then(appearMod)
                .shadow(10.dp, RoundedCornerShape(16.dp), spotColor = CopilotColors.TextPrimary.copy(alpha = 0.06f))
                .copilotAcrylic(16.dp, dark)
                .padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {
                CopilotFlourish(22.dp)
                Text(text, style = CopilotText.AnswerBody,
                    color = MaterialTheme.colorScheme.onBackground, modifier = Modifier.weight(1f))
            }
            if (!streaming) {
                Row(
                    Modifier.padding(start = 32.dp),
                    horizontalArrangement = Arrangement.spacedBy(20.dp),
                ) {
                    listOf(
                        Icons.Outlined.ContentCopy to "Copy",
                        Icons.Outlined.ThumbUpOffAlt to "Like",
                        Icons.Outlined.ThumbDownOffAlt to "Dislike",
                        Icons.Filled.Refresh to "Regenerate",
                        Icons.Filled.IosShare to "Share",
                        Icons.Filled.MoreHoriz to "More",
                    ).forEach { (icon, cd) ->
                        Icon(icon, cd, tint = MaterialTheme.colorScheme.onSurfaceVariant,
                            modifier = Modifier.size(18.dp))
                    }
                }
            }
        }
    }
}
```

### User Turn

```kotlin
@Composable
fun CopilotUserTurn(
    text: String,
    neutral: Boolean = false,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier.fillMaxWidth().padding(horizontal = 16.dp),
        horizontalArrangement = Arrangement.End,
    ) {
        Text(
            text,
            style = CopilotText.UserTurn,
            color = if (neutral) MaterialTheme.colorScheme.onBackground else Color.White,
            modifier = Modifier
                .widthIn(max = 320.dp)
                .clip(RoundedCornerShape(topStart = 16.dp, topEnd = 16.dp,
                    bottomStart = 16.dp, bottomEnd = 4.dp))
                .background(if (neutral) MaterialTheme.colorScheme.surfaceVariant else CopilotColors.Blue)
                .padding(horizontal = 16.dp, vertical = 12.dp),
        )
    }
}
```

### Tone Selector

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.CircleShape

@Composable
fun CopilotToneSelector(
    selection: Int,
    onSelect: (Int) -> Unit,
    modifier: Modifier = Modifier,
) {
    val tones = listOf("Creative", "Balanced", "Precise")
    Row(
        modifier
            .clip(CircleShape)
            .background(MaterialTheme.colorScheme.surfaceVariant.copy(alpha = 0.85f))
            .border(1.dp, MaterialTheme.colorScheme.outline.copy(alpha = 0.6f), CircleShape)
            .padding(3.dp),
    ) {
        tones.forEachIndexed { i, t ->
            val sel = selection == i
            Box(
                Modifier
                    .weight(1f)
                    .clip(CircleShape)
                    .background(if (sel) CopilotColors.Blue else Color.Transparent)
                    .clickable { onSelect(i) }
                    .padding(vertical = 9.dp),
                contentAlignment = Alignment.Center,
            ) {
                Text(t, style = CopilotText.Tone,
                    color = if (sel) Color.White else MaterialTheme.colorScheme.onSurfaceVariant)
            }
        }
    }
}
```

### Prompt Bar (acrylic, focus accent)

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsFocusedAsState
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material.icons.filled.Mic
import androidx.compose.material.icons.filled.Stop
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun CopilotPromptBar(
    value: String,
    onValueChange: (String) -> Unit,
    onSend: () -> Unit,
    onStop: () -> Unit,
    streaming: Boolean = false,
    dark: Boolean = false,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val focused by interaction.collectIsFocusedAsState()
    val active = value.isNotEmpty()
    val haptics = LocalHapticFeedback.current

    Row(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(24.dp))
            .background((if (dark) CopilotColors.DarkSurface else CopilotColors.Surface).copy(alpha = 0.85f))
            .border(
                if (focused) 1.5.dp else 1.dp,
                if (focused) CopilotColors.Blue
                else MaterialTheme.colorScheme.outline.copy(alpha = 0.6f),
                RoundedCornerShape(24.dp),
            )
            .padding(horizontal = 14.dp, vertical = 8.dp)
            .heightIn(min = 52.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(Icons.Filled.Add, "Add content",
            tint = MaterialTheme.colorScheme.onSurfaceVariant, modifier = Modifier.size(22.dp))
        BasicTextField(
            value = value, onValueChange = onValueChange,
            interactionSource = interaction,
            textStyle = CopilotText.PromptInput.copy(color = MaterialTheme.colorScheme.onBackground),
            modifier = Modifier.weight(1f),
            decorationBox = { inner ->
                if (value.isEmpty()) Text("Message Copilot", style = CopilotText.PromptInput,
                    color = MaterialTheme.colorScheme.onSurfaceVariant)
                inner()
            },
        )
        Icon(Icons.Filled.Mic, "Voice",
            tint = MaterialTheme.colorScheme.onSurfaceVariant, modifier = Modifier.size(22.dp))
        Box(
            Modifier
                .size(36.dp).clip(CircleShape)
                .background(if (streaming || active) CopilotColors.Blue else Color.Transparent)
                .clickable {
                    if (streaming) onStop()
                    else if (active) { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onSend() }
                },
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                if (streaming) Icons.Filled.Stop else Icons.Filled.ArrowUpward,
                if (streaming) "Stop generating" else "Send",
                tint = if (streaming || active) Color.White else MaterialTheme.colorScheme.onSurfaceVariant,
                modifier = Modifier.size(if (streaming) 16.dp else 18.dp),
            )
        }
    }
}
```

### Suggestion Chip

```kotlin
@Composable
fun CopilotSuggestionChip(
    label: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    featured: Boolean = false,
    dark: Boolean = false,
) {
    Row(
        modifier
            .copilotAcrylic(18.dp, dark)
            .clickable(onClick = onClick)
            .padding(horizontal = 16.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        if (featured) CopilotFlourish(12.dp)
        Text(label, style = CopilotText.Chip, color = MaterialTheme.colorScheme.onBackground)
    }
}
```

### Streaming Shimmer + Thinking

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.ui.draw.drawWithContent
import androidx.compose.ui.graphics.BlendMode

@Composable
fun CopilotStreamingText(text: String, modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "stream")
    val x by t.animateFloat(-120f, 1000f,
        infiniteRepeatable(tween(1100, easing = LinearEasing)), label = "sweep")
    Text(
        text, style = CopilotText.AnswerBody, color = MaterialTheme.colorScheme.onBackground,
        modifier = modifier.drawWithContent {
            drawContent()
            drawRect(
                brush = Brush.horizontalGradient(
                    0f to Color.Transparent,
                    0.45f to CopilotColors.Gold.copy(alpha = 0.28f),
                    0.6f to CopilotColors.Coral.copy(alpha = 0.20f),
                    1f to Color.Transparent,
                    startX = x, endX = x + 120f,
                ),
                blendMode = BlendMode.Plus,
            )
        },
    )
}

@Composable
fun CopilotThinking() {
    val t = rememberInfiniteTransition(label = "thinking")
    Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
        repeat(3) { i ->
            val s by t.animateFloat(0.5f, 1f,
                infiniteRepeatable(tween(500), RepeatMode.Reverse, StartOffset(i * 150)), label = "dot$i")
            Box(Modifier.size(7.dp).scale(s).clip(CircleShape).background(copilotFlourish()))
        }
    }
}
```

## 4. Navigation (no bottom bar — drawer + top bar)

Copilot has **no bottom navigation**. Use Material 3 `ModalNavigationDrawer` as the sole primary nav, with a `CenterAlignedTopAppBar`.

```kotlin
import androidx.compose.material3.*
import kotlinx.coroutines.launch

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun CopilotScaffold() {
    val drawerState = rememberDrawerState(DrawerValue.Closed)
    val scope = rememberCoroutineScope()

    ModalNavigationDrawer(
        drawerState = drawerState,
        scrimColor = Color.Black.copy(alpha = 0.40f),
        drawerContent = {
            ModalDrawerSheet(
                drawerContainerColor = MaterialTheme.colorScheme.background,
                modifier = Modifier.fillMaxWidth(0.80f),
            ) {
                Column(Modifier.fillMaxSize().padding(16.dp), verticalArrangement = Arrangement.spacedBy(8.dp)) {
                    Row(
                        Modifier.clip(RoundedCornerShape(8.dp)).background(CopilotColors.Blue)
                            .clickable { }.padding(horizontal = 16.dp, vertical = 12.dp),
                        verticalAlignment = Alignment.CenterVertically,
                        horizontalArrangement = Arrangement.spacedBy(8.dp),
                    ) {
                        Icon(Icons.Filled.Add, null, tint = Color.White, modifier = Modifier.size(20.dp))
                        Text("New chat", style = CopilotText.Button, color = Color.White)
                    }
                    Text("TODAY", style = CopilotText.LabelUpper,
                        color = MaterialTheme.colorScheme.onSurfaceVariant,
                        modifier = Modifier.padding(top = 16.dp, start = 4.dp))
                    // recents rows: 48.dp; active row gets acrylic highlight + 3.dp leading Blue bar
                }
            }
        },
    ) {
        Scaffold(
            topBar = {
                CenterAlignedTopAppBar(
                    title = { Text("Copilot", style = CopilotText.Button) },
                    navigationIcon = {
                        IconButton(onClick = { scope.launch { drawerState.open() } }) {
                            Icon(Icons.Filled.Menu, "Open sidebar")
                        }
                    },
                    actions = {
                        Icon(Icons.Filled.AccountCircle, "Account",
                            Modifier.size(28.dp).padding(end = 8.dp))
                    },
                    colors = TopAppBarDefaults.centerAlignedTopAppBarColors(
                        containerColor = MaterialTheme.colorScheme.background),
                )
            },
            bottomBar = { /* CopilotPromptBar — NOT a NavigationBar */ },
        ) { pad -> ConversationList(Modifier.padding(pad)) }
    }
}
```

## 5. Code Block (inside an answer card)

```kotlin
@Composable
fun CopilotCodeBlock(language: String, code: String, dark: Boolean = false) {
    Column(
        Modifier.fillMaxWidth()
            .clip(RoundedCornerShape(12.dp))
            .background(if (dark) Color(0xFF1B1B1B) else Color(0xFFEDEDED))
            .border(1.dp, MaterialTheme.colorScheme.outline.copy(alpha = 0.6f), RoundedCornerShape(12.dp)),
    ) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 12.dp, vertical = 8.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            Text(language, style = CopilotText.Meta, color = MaterialTheme.colorScheme.onSurfaceVariant)
            Text("Copy", style = CopilotText.Button, color = CopilotColors.Blue)
        }
        Text(code, style = CopilotText.Code,
            modifier = Modifier.horizontalScroll(rememberScrollState()).padding(16.dp))
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Acrylic blur-in | `AcrylicAppear` ramps `alpha` 0 → 1 and `blur` 8 → 0 dp over 220ms |
| Streaming sweep | `rememberInfiniteTransition` driving a horizontal flourish-gradient `drawWithContent` overlay (BlendMode.Plus) |
| Thinking dots | `rememberInfiniteTransition` 3 flourish dots scaling 0.5 → 1, `StartOffset(i*150)` |
| Send ⇄ Stop | swap the icon; `Crossfade` / `AnimatedContent` on the streaming flag |
| Tone pill | `animateColorAsState` on the selected segment background, ~200ms |
| Prompt focus accent | toggle border width/color; `animateDpAsState` over 180ms |
| Sidebar | `ModalNavigationDrawer` default slide + scrim fade |
| Copy confirm | `AnimatedVisibility(enter = slideInVertically + fadeIn)`, acrylic Snackbar, 1.5s |

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's `.soft` impact on send.

## 7. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. The **flourish is custom** — draw the gradient swirl path (see `CopilotFlourish`) or load a vector drawable with a gradient `<aapt:attr>` fill.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Flourish (brand) | `sparkles` (swirl substitute) | custom gradient `Canvas` path |
| Send | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Stop streaming | `stop.fill` | `Icons.Filled.Stop` |
| Voice / Mic | `mic` | `Icons.Filled.Mic` |
| Add content | `plus` | `Icons.Filled.Add` |
| Copy | `doc.on.doc` | `Icons.Outlined.ContentCopy` |
| Like / Dislike | `hand.thumbsup` / `hand.thumbsdown` | `Icons.Outlined.ThumbUpOffAlt` / `ThumbDownOffAlt` |
| Regenerate | `arrow.clockwise` | `Icons.Filled.Refresh` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Sidebar toggle | `line.3.horizontal` | `Icons.Filled.Menu` |
| Selector chevron | `chevron.down` | `Icons.Filled.ExpandMore` |
| New chat | `square.and.pencil` | `Icons.Filled.Add` / `Icons.Outlined.Edit` |
| Search (sidebar) | `magnifyingglass` | `Icons.Filled.Search` |
| Conversation row | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Account | `person.crop.circle` | `Icons.Filled.AccountCircle` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). True backdrop blur via `Modifier.graphicsLayer { renderEffect = RenderEffect.createBlurEffect(...) }` is **API 31+** — below that, the tinted translucent fill in `copilotAcrylic` is the graceful fallback. `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; light mode uses dark system-bar icons (inverse in dark) via `WindowCompat`. Use `Scaffold` insets + `Modifier.imePadding()` so the prompt bar floats above the keyboard.
- **No bottom navigation**: do not add a `NavigationBar`. The `ModalNavigationDrawer` is the sole primary nav; the prompt bar lives in the `bottomBar` slot but is a composer, not nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on greeting, answers, body. Pin the prompt-bar base height, sidebar day labels, and tone labels via `dp` or a fixed `Density` override.
- **TalkBack**: mark `CopilotFlourish` with `Modifier.semantics { hideFromAccessibility() }` (decorative); the send button announces "Send" / "Stop generating"; expose the tone selector as a single `Modifier.semantics { role = Role.RadioButton }` group; announce stream completion via `LocalView.current.announceForAccessibility(...)`.
- **Reduce transparency / contrast**: respect the system high-contrast setting — when reduced transparency is requested, render `copilotAcrylic` with full-opacity surface so text contrast holds.
- **The flourish gradient is brand, not state**: never rely on it alone to signal focus/streaming — pair the focus accent with a TalkBack focus event and the stream with a live region.
- **Contrast**: `#616161` on `#FFFFFF` and `#A6A6A6` on `#202020` pass WCAG AA at 13sp+. Use `DarkBlue #4DA3E0` for links/send on the dark canvas.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Copilot's brand requires the fixed Fluent surfaces and the exact `#FF6F61 → #FFB900` flourish gradient regardless of wallpaper, identical across light and dark.
