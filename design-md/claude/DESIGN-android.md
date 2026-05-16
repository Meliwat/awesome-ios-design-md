# Claude (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Claude's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Claude's cream paper canvas, single terracotta accent, Tiempos serif assistant prose) while making everything idiomatic Android — a translucent `Surface` instead of `.regularMaterial`, `FontFamily(Font(R.font.…))` instead of `Font.custom`, `sp`/`dp` instead of `pt`. The signature is text quality: Claude reads like an essayist, and the serif body is the manifest of that intent.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` only if you render remote avatars (Claude's mark is vector — no Palette needed).

## 1. Color Tokens

```kotlin
// ui/theme/ClaudeColors.kt
import androidx.compose.ui.graphics.Color

object ClaudeColors {
    // Canvas (the Claude paper)
    val Cream     = Color(0xFFF8F4ED) // canvas — the most recognizable surface
    val Paper     = Color(0xFFFBF9F4) // elevated: input fill, code badges
    val Surface1  = Color(0xFFF0EAE0) // user pill, callouts
    val Surface2  = Color(0xFFE8E0D2) // pressed / chip fills
    val Sand      = Color(0xFFDDD2BD) // 0.5dp hairline dividers

    // Text (warm-tinted ink — never pure black)
    val Ink       = Color(0xFF2D2520) // primary
    val Graphite  = Color(0xFF5A4F44) // secondary — sender labels
    val Stone     = Color(0xFF8A7E72) // tertiary — placeholder, hints
    val Bone      = Color(0xFFB5AB9E) // disabled

    // Claude Orange (the signature accent — a verb, not a color)
    val Orange        = Color(0xFFD97757)
    val OrangePressed = Color(0xFFBE6242)
    val OrangeSoft    = Color(0xFFF2DDD0) // active chip, "Thinking…" fill

    // Code & Syntax (warm palette — never blue-tinted)
    val CodeBg       = Color(0xFF1F1B16)
    val CodeFg       = Color(0xFFE8E0D2)
    val SyntaxKey    = Color(0xFFD97757) // keywords — Claude Orange
    val SyntaxString = Color(0xFF7FB069) // sage
    val SyntaxNum    = Color(0xFFE8B96F) // gold
    val SyntaxFunc   = Color(0xFF9DA4F2) // periwinkle
    val SyntaxCmt    = Color(0xFF8A7E72) // warm grey

    // Semantic
    val Success = Color(0xFF6B9D5E)
    val Warning = Color(0xFFD49952)
    val Error   = Color(0xFFC16654)
    val Info    = Color(0xFF5A6273)

    // Dark mode (warm dark — preserves the paper feel)
    val DarkCanvas   = Color(0xFF1F1B16)
    val DarkSurface1 = Color(0xFF2A2520)
    val DarkSurface2 = Color(0xFF3A332C)
    val DarkText     = Color(0xFFE8E0D2)
    val DarkTextSec  = Color(0xFFB5AB9E)
    val OrangeSoftDk = Color(0xFF4A352A)
}
```

Claude defaults to the warm cream paper, so wire a `lightColorScheme` into the theme and provide a warm-dark counterpart. Claude Orange is never a Material container fill — it stays a deliberate accent.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.runtime.Composable

private val ClaudeLight = lightColorScheme(
    primary        = ClaudeColors.Orange,
    onPrimary      = ClaudeColors.Paper,    // paper-white glyph on orange
    background     = ClaudeColors.Cream,
    onBackground   = ClaudeColors.Ink,
    surface        = ClaudeColors.Paper,
    onSurface      = ClaudeColors.Ink,
    surfaceVariant = ClaudeColors.Surface1,
    outline        = ClaudeColors.Sand,
    error          = ClaudeColors.Error,
)

private val ClaudeDark = darkColorScheme(
    primary        = ClaudeColors.Orange,   // unchanged — reads well on warm dark
    onPrimary      = ClaudeColors.DarkCanvas,
    background      = ClaudeColors.DarkCanvas,
    onBackground   = ClaudeColors.DarkText,
    surface        = ClaudeColors.DarkSurface1,
    onSurface      = ClaudeColors.DarkText,
    surfaceVariant = ClaudeColors.DarkSurface2,
    outline        = ClaudeColors.DarkSurface2,
    error          = ClaudeColors.Error,
)

@Composable
fun ClaudeTheme(useDark: Boolean = false, content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (useDark) ClaudeDark else ClaudeLight,
        typography  = ClaudeTypography,
        content     = content,
    )
```

## 2. Typography

Tiempos and Styrene are proprietary (Klim Type Foundry). Drop the TTFs in `res/font/` (lowercase, snake_case) and build two families. Fall back to **Source Serif** for the serif and the system sans (Roboto) — the closest free pairing on Android.

```kotlin
// ui/theme/ClaudeType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

// Assistant body & headings — the central typographic choice
val Tiempos = FontFamily(
    Font(R.font.tiempos_text_regular,      FontWeight.Normal),
    Font(R.font.tiempos_text_italic,       FontWeight.Normal, FontStyle.Italic),
    Font(R.font.tiempos_text_semibold,     FontWeight.SemiBold),
    Font(R.font.tiempos_headline_semibold, FontWeight.Bold), // headings ramp
)

// UI chrome & user messages
val Styrene = FontFamily(
    Font(R.font.styrene_a_regular,  FontWeight.Normal),
    Font(R.font.styrene_a_medium,   FontWeight.Medium),
    Font(R.font.styrene_a_semibold, FontWeight.SemiBold),
    Font(R.font.styrene_a_bold,     FontWeight.Bold),
)

val JetBrainsMono = FontFamily(
    Font(R.font.jetbrains_mono_regular, FontWeight.Normal),
    Font(R.font.jetbrains_mono_medium,  FontWeight.Medium),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object ClaudeText {
    val Display       = TextStyle(Tiempos, fontWeight = FontWeight.SemiBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.4).sp)
    val ConvTitle     = TextStyle(Styrene, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val H1            = TextStyle(Tiempos, fontWeight = FontWeight.SemiBold, fontSize = 24.sp, lineHeight = 31.sp, letterSpacing = (-0.2).sp)
    val H2            = TextStyle(Tiempos, fontWeight = FontWeight.SemiBold, fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.1).sp)
    val H3            = TextStyle(Tiempos, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 23.sp)
    val Body          = TextStyle(Tiempos, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 25.sp) // 1.55× — non-negotiable
    val BodyBold      = TextStyle(Tiempos, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 25.sp)
    val BodyItalic    = TextStyle(Tiempos, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 25.sp, fontStyle = FontStyle.Italic)
    val User          = TextStyle(Styrene, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val Action        = TextStyle(Styrene, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 15.sp)
    val Chip          = TextStyle(Styrene, fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 13.sp)
    val SenderLabel   = TextStyle(Styrene, fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 13.sp)
    val Meta          = TextStyle(Styrene, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Caption       = TextStyle(Styrene, fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 14.sp)
    val GroupHeader   = TextStyle(Styrene, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
    val CodeInline    = TextStyle(JetBrainsMono, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 20.sp)
    val CodeBlock     = TextStyle(JetBrainsMono, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 21.sp)
    val CodeLang      = TextStyle(JetBrainsMono, fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 13.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val ClaudeTypography = Typography(
    displaySmall  = ClaudeText.Display,
    headlineLarge = ClaudeText.H1,
    headlineSmall = ClaudeText.H2,
    titleMedium   = ClaudeText.ConvTitle,
    bodyMedium    = ClaudeText.Body,
    labelMedium   = ClaudeText.Chip,
)
```

## 3. Signature Components

### Asterisk-Star Logomark (Claude's avatar)

The 6-point asterisk-meets-star mark. Draw it on a Compose `Canvas` — never tint it anything but Claude Orange (or Bone for the disabled model-picker state).

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp
import kotlin.math.cos
import kotlin.math.sin

@Composable
fun ClaudeMark(
    size: Dp = 18.dp,
    color: Color = ClaudeColors.Orange,
    modifier: Modifier = Modifier,
) {
    Canvas(modifier.size(size)) {
        val cx = this.size.width / 2f
        val cy = this.size.height / 2f
        val r = minOf(cx, cy)
        val inner = r * 0.18f
        val path = Path()
        repeat(6) { i ->
            val a = (Math.PI / 3 * i - Math.PI / 2).toFloat()
            val tip = Offset(cx + r * cos(a), cy + r * sin(a))
            val la = a + (Math.PI / 2).toFloat()
            val lBase = Offset(cx + inner * cos(la), cy + inner * sin(la))
            val rBase = Offset(cx - inner * cos(la), cy - inner * sin(la))
            path.moveTo(lBase.x, lBase.y)
            path.quadraticBezierTo((lBase.x + tip.x) / 2 + 1, (lBase.y + tip.y) / 2 + 1, tip.x, tip.y)
            path.quadraticBezierTo((tip.x + rBase.x) / 2 - 1, (tip.y + rBase.y) / 2 - 1, rBase.x, rBase.y)
            path.close()
        }
        drawPath(path, color)
    }
}
```

### Assistant Message Block (the conversation hero)

No bubble, no container — just type flowing across the cream canvas, the mark leading.

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment

@Composable
fun AssistantMessage(
    modelName: String,            // "Claude Opus 4.5"
    body: androidx.compose.ui.text.AnnotatedString, // pre-rendered markdown
    isStreaming: Boolean,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 4.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Row(
            horizontalArrangement = Arrangement.spacedBy(8.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            ClaudeMark(size = 18.dp)
            Text("Claude", style = ClaudeText.SenderLabel, color = ClaudeColors.Graphite)
            Text("·", color = ClaudeColors.Stone)
            Text(modelName, style = ClaudeText.Chip, color = ClaudeColors.Stone)
        }
        Text(body, style = ClaudeText.Body, color = ClaudeColors.Ink) // 1.55× leading baked in
        if (isStreaming) StreamingCursor()
    }
}
```

### Streaming Response Cursor (the orange caret)

8dp × 18dp, blinks 600ms (300 on / 300 off). Pauses while a token arrives.

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.draw.alpha
import androidx.compose.ui.draw.clip
import androidx.compose.ui.semantics.contentDescription
import androidx.compose.ui.semantics.semantics

@Composable
fun StreamingCursor(modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "cursor")
    val a by t.animateFloat(
        initialValue = 1f, targetValue = 0f,
        animationSpec = infiniteRepeatable(tween(300, easing = LinearEasing), RepeatMode.Reverse),
        label = "blink",
    )
    Box(
        modifier
            .size(width = 8.dp, height = 18.dp)
            .clip(RoundedCornerShape(1.dp))
            .alpha(a)
            .background(ClaudeColors.Orange)
            .semantics { contentDescription = "Claude is responding" },
    )
}
```

### User Message Pill

A soft pill on Surface Warm 1 — never a bubble with a tail. Right-aligned, max 80% width.

```kotlin
import androidx.compose.foundation.layout.BoxWithConstraints

@Composable
fun UserMessage(text: String, modifier: Modifier = Modifier) {
    Row(modifier.fillMaxWidth().padding(horizontal = 4.dp), horizontalArrangement = Arrangement.End) {
        BoxWithConstraints {
            Text(
                text = text,
                style = ClaudeText.User,
                color = ClaudeColors.Ink,
                modifier = Modifier
                    .widthIn(max = maxWidth * 0.8f)
                    .clip(RoundedCornerShape(18.dp))
                    .background(ClaudeColors.Surface1)
                    .padding(horizontal = 16.dp, vertical = 12.dp),
            )
        }
    }
}
```

### Chat Input (auto-grow + orange send circle)

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.clickable
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.SolidColor
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.foundation.shape.CircleShape

@Composable
fun ChatInput(
    value: String,
    onValueChange: (String) -> Unit,
    onSend: () -> Unit,
    onAttach: () -> Unit,
    modifier: Modifier = Modifier,
) {
    var focused by remember { mutableStateOf(false) }
    val canSend = value.trim().isNotEmpty()
    val haptics = LocalHapticFeedback.current
    val sendInteraction = remember { MutableInteractionSource() }
    val sendPressed by sendInteraction.collectIsPressedAsState()
    val sendScale by animateFloatAsState(if (sendPressed) 0.94f else 1f, label = "send")

    Row(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp, vertical = 8.dp)
            .clip(RoundedCornerShape(24.dp))
            .background(ClaudeColors.Paper)
            .border(
                width = if (focused) 1.5.dp else 1.dp,
                color = if (focused) ClaudeColors.Orange else ClaudeColors.Sand,
                shape = RoundedCornerShape(24.dp),
            )
            .padding(horizontal = 12.dp, vertical = 6.dp),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(
            Icons.Filled.Add, contentDescription = "Attach",
            tint = ClaudeColors.Graphite,
            modifier = Modifier.size(28.dp).padding(bottom = 8.dp).clickable(onClick = onAttach),
        )
        BasicTextField(
            value = value,
            onValueChange = onValueChange,
            textStyle = ClaudeText.User.copy(color = ClaudeColors.Ink),
            cursorBrush = SolidColor(ClaudeColors.Orange),
            maxLines = 8,
            modifier = Modifier
                .weight(1f)
                .padding(vertical = 14.dp)
                .onFocusChanged { focused = it.isFocused },
            decorationBox = { inner ->
                if (value.isEmpty()) Text("Reply to Claude…", style = ClaudeText.User, color = ClaudeColors.Stone)
                inner()
            },
        )
        Box(
            Modifier
                .padding(bottom = 6.dp)
                .size(40.dp)
                .scale(sendScale)
                .clip(CircleShape)
                .background(if (canSend) (if (sendPressed) ClaudeColors.OrangePressed else ClaudeColors.Orange) else ClaudeColors.Surface2)
                .clickable(sendInteraction, indication = null, enabled = canSend) {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                    onSend()
                },
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                Icons.Filled.ArrowUpward,
                contentDescription = "Send",
                tint = if (canSend) ClaudeColors.Paper else ClaudeColors.Stone,
                modifier = Modifier.size(16.dp),
            )
        }
    }
}
```

### Model Picker Chip

```kotlin
import androidx.compose.material.icons.filled.KeyboardArrowDown

@Composable
fun ModelPickerChip(modelName: String, onTap: () -> Unit, modifier: Modifier = Modifier) {
    Row(
        modifier
            .clip(RoundedCornerShape(16.dp))
            .background(ClaudeColors.Surface1)
            .clickable(onClick = onTap)
            .padding(horizontal = 12.dp, vertical = 6.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        ClaudeMark(size = 14.dp)
        Text(modelName, style = ClaudeText.Chip, color = ClaudeColors.Ink)
        Icon(Icons.Filled.KeyboardArrowDown, contentDescription = null, tint = ClaudeColors.Stone, modifier = Modifier.size(10.dp))
    }
}
```

### Code Block (warm dark, never blue)

```kotlin
import androidx.compose.foundation.horizontalScroll
import androidx.compose.foundation.rememberScrollState
import androidx.compose.material.icons.filled.Check
import androidx.compose.material.icons.filled.ContentCopy
import androidx.compose.ui.platform.LocalClipboardManager
import androidx.compose.ui.text.AnnotatedString
import kotlinx.coroutines.delay

@Composable
fun CodeBlock(
    language: String,
    code: AnnotatedString,           // pre-syntax-highlighted with the warm palette
    modifier: Modifier = Modifier,
) {
    var copied by remember { mutableStateOf(false) }
    val clipboard = LocalClipboardManager.current
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(copied) { if (copied) { delay(1200); copied = false } }

    Column(
        modifier
            .clip(RoundedCornerShape(12.dp))
            .background(ClaudeColors.CodeBg)
            .border(1.dp, ClaudeColors.Sand, RoundedCornerShape(12.dp)),
    ) {
        Row(
            Modifier
                .fillMaxWidth()
                .background(ClaudeColors.DarkSurface2.copy(alpha = 0.4f))
                .padding(horizontal = 12.dp, vertical = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text(language, style = ClaudeText.CodeLang, color = ClaudeColors.Stone)
            Spacer(Modifier.weight(1f))
            Icon(
                imageVector = if (copied) Icons.Filled.Check else Icons.Filled.ContentCopy,
                contentDescription = "Copy code",
                tint = ClaudeColors.CodeFg,
                modifier = Modifier
                    .size(32.dp)
                    .clip(CircleShape)
                    .clickable {
                        clipboard.setText(AnnotatedString(code.text))
                        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS light impact
                        copied = true
                    }
                    .padding(8.dp),
            )
        }
        Box(Modifier.horizontalScroll(rememberScrollState()).padding(16.dp)) {
            Text(code, style = ClaudeText.CodeBlock, color = ClaudeColors.CodeFg, softWrap = false)
        }
    }
}
```

## 4. Streaming Word-by-Word Animation (the signature dynamic)

Claude has no color extraction — its distinctive dynamic system is the **word-by-word streaming with the blinking orange caret** and the **"Thinking…" pulse**. Stream prose by appending tokens and fading each word in (never character-by-character — readability over typewriter drama).

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.fadeIn
import androidx.compose.animation.core.tween
import androidx.compose.foundation.layout.FlowRow

@OptIn(androidx.compose.foundation.layout.ExperimentalLayoutApi::class)
@Composable
fun StreamedProse(words: List<String>, isStreaming: Boolean, modifier: Modifier = Modifier) {
    FlowRow(modifier) {
        words.forEachIndexed { i, w ->
            AnimatedVisibility(visible = true, enter = fadeIn(tween(80))) { // 80ms per-word fade
                Text("$w ", style = ClaudeText.Body, color = ClaudeColors.Ink)
            }
        }
        if (isStreaming) StreamingCursor(Modifier.padding(start = 2.dp))
    }
}

// "Thinking…" indicator — asterisk pulses 1.0 → 1.15 → 1.0 every 1200ms
@Composable
fun ThinkingIndicator(elapsedSeconds: Int? = null, modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "think")
    val pulse by t.animateFloat(
        1f, 1.15f,
        infiniteRepeatable(tween(1200, easing = FastOutSlowInEasing), RepeatMode.Reverse),
        label = "pulse",
    )
    Row(
        modifier
            .clip(RoundedCornerShape(12.dp))
            .background(ClaudeColors.OrangeSoft)
            .padding(horizontal = 12.dp, vertical = 6.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        ClaudeMark(size = 14.dp, modifier = Modifier.scale(pulse))
        Text(elapsedSeconds?.let { "Thought for ${it}s" } ?: "Thinking…", style = ClaudeText.Chip, color = ClaudeColors.Graphite)
    }
}
```

## 5. Navigation

Claude has **no bottom tab bar** — it is a single-purpose chat with sidebar navigation. Use a `ModalNavigationDrawer` for conversation history; the top header is a slim 52dp `Row` over the cream canvas (no app bar elevation — content scrolls naturally underneath). Android has no live blur, so the header is just the opaque `Cream` surface.

```kotlin
import androidx.compose.material.icons.filled.Menu
import androidx.compose.material.icons.filled.MoreHoriz
import androidx.compose.material3.ModalDrawerSheet
import androidx.compose.material3.ModalNavigationDrawer
import androidx.compose.material3.rememberDrawerState
import androidx.compose.material3.DrawerValue

@Composable
fun ConversationScaffold(modelName: String, content: @Composable () -> Unit) {
    val drawer = rememberDrawerState(DrawerValue.Closed)
    ModalNavigationDrawer(
        drawerState = drawer,
        drawerContent = {
            ModalDrawerSheet(drawerContainerColor = ClaudeColors.Surface1) {
                // "+ New chat" Paper button + date-grouped conversation rows
            }
        },
    ) {
        Column(Modifier.fillMaxSize().background(ClaudeColors.Cream)) {
            Row(
                Modifier.fillMaxWidth().height(52.dp).padding(horizontal = 16.dp),
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Icon(Icons.Filled.Menu, "Open history", tint = ClaudeColors.Ink, modifier = Modifier.size(18.dp))
                Spacer(Modifier.weight(1f))
                Text("Untitled chat", style = ClaudeText.ConvTitle, color = ClaudeColors.Ink)
                Spacer(Modifier.weight(1f))
                Icon(Icons.Filled.MoreHoriz, "Menu", tint = ClaudeColors.Ink, modifier = Modifier.size(18.dp))
            }
            content()
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Send tap | `animateFloatAsState` 1 → 0.94 with `spring`; `HapticFeedbackType.LongPress` (~iOS medium); then user pill slides up |
| Streaming text | per-word `fadeIn(tween(80))` — never character-by-character |
| Streaming cursor blink | `rememberInfiniteTransition` 1 → 0 over 300ms, `RepeatMode.Reverse` (600ms cycle) |
| "Thinking…" pulse | infinite `scale` 1.0 → 1.15 over 1200ms, `FastOutSlowInEasing` |
| Artifact card → modal | `SharedTransitionLayout` + `Modifier.sharedElement()` (Compose 1.7+); fallback `AnimatedVisibility` + `slideInVertically` |
| Copy confirmation | icon `ContentCopy` → `Check` over 150ms, "Copied" toast slides up, auto-dismiss 1200ms |

```kotlin
// User message slide-up after send
import androidx.compose.animation.slideInVertically
AnimatedVisibility(
    visible = sent,
    enter = slideInVertically(animationSpec = tween(300)) { it / 2 } + fadeIn(tween(300)),
) {
    UserMessage(text = lastSent)
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(12, …)` to approximate iOS's `.medium` send impact and `.light` copy tick.

## 7. Icons

Claude ships a custom asterisk-star mark (drawn on `Canvas` in §3). The rest map to `androidx.compose.material:material-icons-extended`; export any bespoke glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Send (arrow) | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Attach (plus) | `plus.circle` | `Icons.Filled.Add` |
| Copy | `doc.on.doc` | `Icons.Filled.ContentCopy` |
| Copied confirmation | `checkmark` | `Icons.Filled.Check` |
| Hamburger / sidebar | `line.3.horizontal` | `Icons.Filled.Menu` |
| Overflow menu | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Model picker chevron | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Close modal | `xmark.circle.fill` | `Icons.Filled.Cancel` |
| Artifact — document | `doc.text` | `Icons.Filled.Description` |
| Artifact — code | `chevron.left.forwardslash.chevron.right` | `Icons.Filled.Code` |
| Artifact — chart | `chart.bar` | `Icons.Filled.BarChart` |
| Artifact — open arrow | `arrow.up.right` | `Icons.Filled.NorthEast` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| New chat | `plus` | `Icons.Filled.Add` |
| Asterisk-star mark | (custom) | Custom `Canvas` `ClaudeMark` — never substitute |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The cream canvas wants dark-content system bars (`WindowCompat` + `isAppearanceLightStatusBars = true`); apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the pinned input clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on assistant body, headings, user messages, list items, blockquotes. Pin layout-sensitive text (model chip, timestamps, the 16dp send glyph, code language label) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. Cap code-block scaling at 18sp to prevent overflow.
- **TalkBack**: make the assistant message a single node with `Modifier.semantics(mergeDescendants = true)` — announce model name then body; the streaming cursor announces "Claude is responding". Give the copy button `contentDescription = "Copy code"` and a separate role.
- **Touch targets**: Material guidance is 48dp minimum. The 40dp send circle needs `Modifier.size(48.dp)` hit area via padding; the 10dp chevron and 32dp copy glyph likewise expand their hit area.
- **Contrast**: Ink (`#2D2520`) on Cream (`#F8F4ED`) clears WCAG AAA at all sizes. The real low-contrast pair is **Stone `#8A7E72` on Cream** and Bone on Cream — validate the 11sp caption/group-header with a contrast checker and darken toward Graphite `#5A4F44` if your build targets compliance. Never put Claude Orange on small body text.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, skip the cursor blink and "Thinking…" pulse — cursor stays solid; word fade collapses to instant.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Claude's brand requires the fixed `#F8F4ED` cream paper and the single terracotta accent regardless of wallpaper. Strong-brand app: keep the curated scheme.
