# ChatGPT (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports ChatGPT's visual language to **Android with Jetpack Compose (Material 3)**: light + dark color token objects, a `Typography` set, paste-ready `@Composable`s, the black/white send button, the markdown atoms, and the full-screen voice-mode sphere.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (ChatGPT's monochromatic minimalism, the circular send button, bubble-less assistant text, the pulsing blue voice sphere) while making everything idiomatic Android — a custom asymmetric `Shape` for the user bubble, `ModalNavigationDrawer` for the conversation sidebar, an `Animatable`-driven `Canvas` sphere, `sp`/`dp` instead of `pt`. ChatGPT is radically flat — shadows appear only on ephemeral floating UI.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for attachment thumbnails, and a markdown renderer such as [compose-markdown](https://github.com/jeziellago/compose-markdown) or `Markwon` for assistant content (headings, code, tables, LaTeX). No color extraction, so no `androidx.palette`.

## 1. Color Tokens

ChatGPT ships first-class light **and** dark. Define both palettes, then select per system theme.

```kotlin
// ui/theme/GptColors.kt
import androidx.compose.ui.graphics.Color

object GptColors {
    // Canvas — pure white light / charcoal (NOT true black) dark
    val Canvas      = Color(0xFFFFFFFF)
    val DarkCanvas  = Color(0xFF212121)

    // Sidebar (darker than canvas — signature treatment)
    val SidebarLight     = Color(0xFFF9F9F9)
    val SidebarDark      = Color(0xFF181818)
    val SidebarActive    = Color(0xFFECECEC)
    val SidebarActiveDk  = Color(0xFF2F2F2F)
    val SidebarHover     = Color(0xFFF0F0F0)
    val SidebarHoverDk   = Color(0xFF252525)
    val Divider          = Color(0xFFE5E5E5)
    val DividerDark      = Color(0xFF424242)

    // Text
    val TextPrimary       = Color(0xFF0D0D0D) // softer than pure #000
    val TextSecondary     = Color(0xFF676767) // placeholder, timestamps
    val TextTertiary      = Color(0xFF8E8E8E) // disabled
    val DarkTextPrimary   = Color(0xFFECECEC)
    val DarkTextSecondary = Color(0xFFB4B4B4)
    val DarkTextTertiary  = Color(0xFF707070)

    // User message bubble (assistant text has NO bubble)
    val UserBubbleLight = Color(0xFFF7F7F8)
    val UserBubbleDark  = Color(0xFF2F2F2F)

    // Code
    val CodeBlockLight  = Color(0xFFF7F7F8)
    val CodeBlockDark   = Color(0xFF1E1E1E)
    val CodeInlineLight = Color(0xFFF0F0F0)
    val CodeInlineDark  = Color(0xFF424242)
    val CodeBorderLight = Color(0xFFE5E5E5)
    val CodeBorderDark  = Color(0xFF363636)

    // Send button (inverse of canvas)
    val SendLight       = Color(0xFF0D0D0D)
    val SendDark        = Color(0xFFFFFFFF)
    val SendDisabled    = Color(0xFFCCCCCC)
    val SendDisabledDk  = Color(0xFF4D4D4D)

    // Semantic — monochromatic; blue only on links
    val LinkBlue    = Color(0xFF2A7FFF)
    val LegacyGreen = Color(0xFF10A37F) // largely retired — wordmark / legacy UI only
    val ErrorRed    = Color(0xFFE53E3E)
    val WarningOrange = Color(0xFFD97706)

    // Voice-mode sphere gradient
    val VoiceBlue1 = Color(0xFF3B82F6)
    val VoiceBlue2 = Color(0xFF60A5FA)
    val VoiceBlue3 = Color(0xFF93C5FD)
}
```

Wire both into Material 3 schemes and switch on the system setting. ChatGPT supports both modes equally, so unlike most strong-brand apps this one *does* provide a real `lightColorScheme` and `darkColorScheme`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val GptLight = lightColorScheme(
    primary        = GptColors.SendLight,    // monochrome — the "accent" is ink
    onPrimary      = GptColors.Canvas,
    background     = GptColors.Canvas,
    onBackground   = GptColors.TextPrimary,
    surface        = GptColors.Canvas,
    onSurface      = GptColors.TextPrimary,
    surfaceVariant = GptColors.UserBubbleLight,
    outline        = GptColors.Divider,
    error          = GptColors.ErrorRed,
)

private val GptDark = darkColorScheme(
    primary        = GptColors.SendDark,
    onPrimary      = GptColors.TextPrimary,
    background     = GptColors.DarkCanvas,
    onBackground   = GptColors.DarkTextPrimary,
    surface        = GptColors.DarkCanvas,
    onSurface      = GptColors.DarkTextPrimary,
    surfaceVariant = GptColors.UserBubbleDark,
    outline        = GptColors.DividerDark,
    error          = GptColors.ErrorRed,
)

@Composable
fun GptTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) GptDark else GptLight,
        typography = GptTypography,
        content = content,
    )
```

## 2. Typography

ChatGPT uses **Söhne** (Klim Type Foundry, commercially licensed), falling back to Inter, then SF Pro. Drop Söhne/Inter TTFs in `res/font/` (lowercase, snake_case); Roboto is the Android system fallback. Weights are concentrated at 400/500/600 — no bold, no thin. Code is always monospace (Menlo / SF Mono on iOS → bundle JetBrains Mono / use the platform monospace family).

```kotlin
// ui/theme/GptType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Sohne = FontFamily(
    Font(R.font.sohne_buch,     FontWeight.Normal),   // 400 "Buch"
    Font(R.font.sohne_kraftig,  FontWeight.Medium),   // 500 "Kräftig"
    Font(R.font.sohne_halbfett, FontWeight.SemiBold), // 600 "Halbfett"
)
val GptMono = FontFamily(Font(R.font.jetbrains_mono_regular, FontWeight.Normal)) // code only

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, same weights/tracking)
object GptText {
    val Body          = TextStyle(Sohne,   fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val BodyCompact   = TextStyle(Sohne,   fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val H1            = TextStyle(Sohne,   fontWeight = FontWeight.SemiBold, fontSize = 24.sp, lineHeight = 30.sp, letterSpacing = (-0.2).sp)
    val H2            = TextStyle(Sohne,   fontWeight = FontWeight.SemiBold, fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.15).sp)
    val H3            = TextStyle(Sohne,   fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val H4            = TextStyle(Sohne,   fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 22.sp)
    val ModelChip     = TextStyle(Sohne,   fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 14.sp)
    val SidebarTitle  = TextStyle(Sohne,   fontWeight = FontWeight.Medium,   fontSize = 15.sp, lineHeight = 20.sp)
    val SidebarSection = TextStyle(Sohne,  fontWeight = FontWeight.Medium,   fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val Button        = TextStyle(Sohne,   fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 14.sp)
    val Meta          = TextStyle(Sohne,   fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 16.sp)
    val Placeholder   = TextStyle(Sohne,   fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp)
    val LinkText      = TextStyle(Sohne,   fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val CodeInline    = TextStyle(GptMono, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val CodeBlock     = TextStyle(GptMono, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 20.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val GptTypography = Typography(
    headlineLarge = GptText.H1,
    headlineSmall = GptText.H2,
    titleMedium   = GptText.H3,
    bodyMedium    = GptText.Body,
    labelSmall    = GptText.SidebarSection,
)
```

Typography follows **document rhythm, not UI rhythm** — 1.5 line-height on body (`24.sp / 16.sp`), markdown headings stepping down. Code styles must never be substituted away from monospace.

## 3. Signature Components

### Send Button (the black/white circle)

The single most recognizable ChatGPT component — a 32dp circle, ink-on-canvas inverse, single up-arrow. Becomes a square "stop" during streaming.

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material.icons.filled.Stop
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun SendButton(
    enabled: Boolean,
    generating: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val dark = isSystemInDarkTheme()
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        targetValue = if (pressed) 0.94f else 1f,
        animationSpec = spring(dampingRatio = 0.7f, stiffness = 700f),
        label = "sendScale",
    )
    val circle = when {
        !enabled && !generating -> if (dark) GptColors.SendDisabledDk else GptColors.SendDisabled
        dark -> GptColors.SendDark
        else -> GptColors.SendLight
    }
    val iconTint = if (dark) GptColors.SendLight else GptColors.SendDark
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .size(32.dp)
            .scale(scale)
            .clip(CircleShape)
            .background(circle)
            .clickable(interaction, indication = null, enabled = enabled || generating) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .impact solid
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            imageVector = if (generating) Icons.Filled.Stop else Icons.Filled.ArrowUpward,
            contentDescription = if (generating) "Stop generating" else "Send",
            tint = iconTint,
            modifier = Modifier.size(16.dp),
        )
    }
}
```

### User Message Bubble (asymmetric corners)

Right-aligned, max-width ~80%, soft-gray fill, with the "pointer" corner flattened to 4dp on the bottom-trailing edge.

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text

private val UserBubbleShape = RoundedCornerShape(
    topStart = 18.dp, topEnd = 18.dp, bottomStart = 18.dp, bottomEnd = 4.dp,
)

@Composable
fun UserMessageBubble(text: String, modifier: Modifier = Modifier) {
    val dark = isSystemInDarkTheme()
    Row(modifier.fillMaxWidth().padding(horizontal = 16.dp)) {
        Spacer(Modifier.weight(0.2f)) // ~80% max width
        Box(
            Modifier
                .weight(0.8f, fill = false)
                .clip(UserBubbleShape)
                .background(if (dark) GptColors.UserBubbleDark else GptColors.UserBubbleLight)
                .padding(horizontal = 14.dp, vertical = 10.dp),
        ) {
            Text(
                text,
                style = GptText.Body,
                color = if (dark) GptColors.DarkTextPrimary else GptColors.TextPrimary,
            )
        }
    }
}
```

### Assistant Message (no bubble + feedback row)

Plain markdown-rendered content flowing inline — **no bubble, no box** — with the canonical 4-icon feedback row beneath.

```kotlin
import androidx.compose.material.icons.filled.ContentCopy
import androidx.compose.material.icons.filled.Refresh
import androidx.compose.material.icons.filled.ThumbDown
import androidx.compose.material.icons.filled.ThumbUp
import androidx.compose.material.icons.filled.AutoAwesome
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun AssistantMessage(
    markdown: String,
    onRegenerate: () -> Unit,
    onCopy: () -> Unit,
    onThumbUp: () -> Unit,
    onThumbDown: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val dark = isSystemInDarkTheme()
    val textColor = if (dark) GptColors.DarkTextPrimary else GptColors.TextPrimary
    Column(
        modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 8.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            Icon(Icons.Filled.AutoAwesome, null, tint = textColor, modifier = Modifier.size(24.dp))
            // Replace with a real markdown renderer (Markwon / compose-markdown):
            // renders H1/H2/H3, lists, inline code, code blocks, tables, LaTeX.
            Text(markdown, style = GptText.Body, color = textColor)
        }
        Row(horizontalArrangement = Arrangement.spacedBy(8.dp), modifier = Modifier.padding(start = 32.dp)) {
            FeedbackIcon(Icons.Filled.Refresh, "Regenerate", onRegenerate)
            FeedbackIcon(Icons.Filled.ContentCopy, "Copy", onCopy)
            FeedbackIcon(Icons.Filled.ThumbUp, "Good response", onThumbUp)
            FeedbackIcon(Icons.Filled.ThumbDown, "Bad response", onThumbDown)
        }
    }
}

@Composable
private fun FeedbackIcon(icon: ImageVector, label: String, onClick: () -> Unit) {
    val dark = isSystemInDarkTheme()
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 1.15f else 1f, label = "thumbScale")
    val haptics = LocalHapticFeedback.current
    Box(
        Modifier
            .size(32.dp) // tight per iOS spec — see §8 (bump to 48dp for strict a11y)
            .scale(scale)
            .clip(CircleShape)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ≈ .impact soft
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(icon, contentDescription = label,
            tint = if (dark) GptColors.DarkTextSecondary else GptColors.TextSecondary,
            modifier = Modifier.size(16.dp))
    }
}
```

### Code Block (with Copy button)

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.horizontalScroll
import androidx.compose.foundation.rememberScrollState
import androidx.compose.material.icons.filled.Check
import kotlinx.coroutines.delay

@Composable
fun CodeBlock(language: String, code: String, modifier: Modifier = Modifier) {
    val dark = isSystemInDarkTheme()
    val bg = if (dark) GptColors.CodeBlockDark else GptColors.CodeBlockLight
    val border = if (dark) GptColors.CodeBorderDark else GptColors.CodeBorderLight
    val secondary = if (dark) GptColors.DarkTextSecondary else GptColors.TextSecondary
    var copied by remember { mutableStateOf(false) }
    val clipboard = androidx.compose.ui.platform.LocalClipboardManager.current

    LaunchedEffect(copied) { if (copied) { delay(1000); copied = false } }

    Column(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(bg)
            .border(1.dp, border, RoundedCornerShape(8.dp)),
    ) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 12.dp, vertical = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text(language, style = GptText.SidebarSection, color = secondary)
            Spacer(Modifier.weight(1f))
            Row(
                Modifier.clip(RoundedCornerShape(6.dp)).clickable {
                    clipboard.setText(androidx.compose.ui.text.AnnotatedString(code)); copied = true
                }.padding(4.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(4.dp),
            ) {
                Icon(if (copied) Icons.Filled.Check else Icons.Filled.ContentCopy, null,
                    tint = secondary, modifier = Modifier.size(12.dp))
                Text(if (copied) "Copied!" else "Copy", style = GptText.Button, color = secondary)
            }
        }
        Box(Modifier.fillMaxWidth().height(1.dp).background(border))
        Box(Modifier.horizontalScroll(rememberScrollState()).padding(12.dp)) {
            Text(code, style = GptText.CodeBlock,
                color = if (dark) GptColors.DarkTextPrimary else GptColors.TextPrimary)
        }
    }
}
```

### Model Selector Chip

```kotlin
import androidx.compose.material.icons.filled.KeyboardArrowDown

@Composable
fun ModelSelectorChip(modelName: String, onTap: () -> Unit, modifier: Modifier = Modifier) {
    val dark = isSystemInDarkTheme()
    val text = if (dark) GptColors.DarkTextPrimary else GptColors.TextPrimary
    val border = if (dark) GptColors.DividerDark else GptColors.Divider
    Row(
        modifier
            .clip(CircleShape)
            .border(1.dp, border, CircleShape)
            .clickable { onTap() }
            .padding(horizontal = 10.dp, vertical = 6.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(Icons.Filled.AutoAwesome, null, tint = text, modifier = Modifier.size(14.dp))
        Text(modelName, style = GptText.ModelChip, color = text)
        Icon(Icons.Filled.KeyboardArrowDown, "Choose model",
            tint = if (dark) GptColors.DarkTextSecondary else GptColors.TextSecondary,
            modifier = Modifier.size(10.dp))
    }
}
```

### Message Composer

Bottom-docked rounded container. When empty: paperclip + globe + mic. When text entered: the send button replaces the trailing icons.

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Mic
import androidx.compose.material.icons.filled.Public
import androidx.compose.ui.text.input.TextFieldValue

@Composable
fun Composer(
    value: TextFieldValue,
    onValueChange: (TextFieldValue) -> Unit,
    onSend: () -> Unit,
    onVoice: () -> Unit,
    onAttach: () -> Unit,
    onWebSearch: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val dark = isSystemInDarkTheme()
    val empty = value.text.isBlank()
    val border = if (dark) GptColors.DividerDark else GptColors.Divider
    val secondary = if (dark) GptColors.DarkTextSecondary else GptColors.TextSecondary

    Row(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp, vertical = 8.dp)
            .clip(RoundedCornerShape(24.dp))
            .background(if (dark) GptColors.DarkCanvas else GptColors.Canvas)
            .border(1.dp, border, RoundedCornerShape(24.dp))
            .padding(horizontal = 8.dp, vertical = 8.dp),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        IconHit(Icons.Filled.Add, "Attach", secondary, onAttach)
        Box(Modifier.weight(1f).padding(vertical = 10.dp)) {
            if (value.text.isEmpty()) {
                Text("Message ChatGPT…", style = GptText.Placeholder, color = secondary)
            }
            BasicTextField(
                value = value, onValueChange = onValueChange,
                textStyle = GptText.Body.copy(
                    color = if (dark) GptColors.DarkTextPrimary else GptColors.TextPrimary),
                maxLines = 6,
            )
        }
        if (empty) {
            IconHit(Icons.Filled.Public, "Web search", secondary, onWebSearch)
            Box(
                Modifier.size(32.dp).clip(CircleShape).border(1.dp, border, CircleShape)
                    .clickable { onVoice() },
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Filled.Mic, "Voice", tint = secondary, modifier = Modifier.size(18.dp)) }
        } else {
            SendButton(enabled = true, generating = false, onClick = onSend)
        }
    }
}

@Composable
private fun IconHit(icon: ImageVector, label: String, tint: Color, onClick: () -> Unit) {
    Box(
        Modifier.size(40.dp).clip(RoundedCornerShape(8.dp)).clickable { onClick() },
        contentAlignment = Alignment.Center,
    ) { Icon(icon, contentDescription = label, tint = tint, modifier = Modifier.size(20.dp)) }
}
```

### Typing Indicator (3-dot pulse)

```kotlin
import androidx.compose.animation.core.RepeatMode
import androidx.compose.animation.core.infiniteRepeatable
import androidx.compose.animation.core.rememberInfiniteTransition
import androidx.compose.animation.core.tween
import androidx.compose.ui.draw.alpha

@Composable
fun TypingIndicator(modifier: Modifier = Modifier) {
    val dark = isSystemInDarkTheme()
    val color = if (dark) GptColors.DarkTextSecondary else GptColors.TextSecondary
    val t = rememberInfiniteTransition(label = "typing")
    Row(modifier, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
        repeat(3) { i ->
            val a by t.animateFloat(
                initialValue = 0.3f, targetValue = 1f,
                animationSpec = infiniteRepeatable(
                    tween(600, delayMillis = i * 200), RepeatMode.Reverse),
                label = "dot$i",
            )
            Box(Modifier.size(8.dp).alpha(a).clip(CircleShape).background(color))
        }
    }
}
```

## 4. Voice Mode (the distinctive system)

ChatGPT has no color extraction; its flagship dynamic moment is **voice mode** — a full-screen pulsing blue gradient sphere that morphs as you speak. Port the SwiftUI radial-gradient sphere with an `Animatable` scale pulse plus a subtle organic deformation. This is the product's most-recognized animation.

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.LinearEasing
import androidx.compose.foundation.Canvas
import androidx.compose.material.icons.filled.Close
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import kotlin.math.sin

@Composable
fun VoiceModeOverlay(onEnd: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    val pulse = remember { Animatable(1f) }
    val morph = rememberInfiniteTransition(label = "morph")
    val wobble by morph.animateFloat(
        initialValue = 0f, targetValue = (2 * Math.PI).toFloat(),
        animationSpec = infiniteRepeatable(tween(3000, easing = LinearEasing)),
        label = "wobble",
    )

    LaunchedEffect(Unit) {
        haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .impact solid on entry
        // Continuous organic pulse: scale 0.95 ↔ 1.05 over 2s ease-in-out
        while (true) {
            pulse.animateTo(1.05f, tween(2000))
            pulse.animateTo(0.95f, tween(2000))
        }
    }

    Box(Modifier.fillMaxSize().background(Color.Black), contentAlignment = Alignment.Center) {
        Canvas(Modifier.size(280.dp).scale(pulse.value)) {
            // Perlin-ish deformation: radius modulated by a couple of sine terms
            val baseR = size.minDimension / 2f
            val r = baseR * (1f + 0.03f * sin(wobble) + 0.02f * sin(wobble * 2.3f))
            drawCircle(
                brush = Brush.radialGradient(
                    colors = listOf(GptColors.VoiceBlue1, GptColors.VoiceBlue2, GptColors.VoiceBlue3),
                    center = center,
                    radius = r,
                ),
                radius = r,
                center = center,
            )
            // Glow: a soft larger translucent ring (Android has no blur shadow on Canvas)
            drawCircle(
                color = GptColors.VoiceBlue1.copy(alpha = 0.25f),
                radius = r * 1.25f,
                center = center,
            )
        }
        Column(
            Modifier.align(Alignment.BottomCenter).fillMaxWidth().padding(bottom = 40.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(16.dp),
        ) {
            Text("ChatGPT is listening…", style = GptText.Meta, color = Color.White.copy(alpha = 0.7f))
            Row(
                Modifier.fillMaxWidth().padding(horizontal = 24.dp),
                horizontalArrangement = Arrangement.SpaceBetween,
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Text("Mute", style = GptText.Button, color = Color.White,
                    modifier = Modifier.clickable { /* toggle mute */ })
                Box(
                    Modifier.size(44.dp).clip(CircleShape).background(Color.White.copy(alpha = 0.2f))
                        .clickable { onEnd() },
                    contentAlignment = Alignment.Center,
                ) { Icon(Icons.Filled.Close, "End voice mode", tint = Color.White,
                    modifier = Modifier.size(18.dp)) }
            }
        }
    }
}
```

Present this as a full-screen destination with an `enter = scaleIn(initialScale = 0.9f) + fadeIn(tween(400))` transition. Drive an `AVAudioSession`-equivalent via `AudioManager`/`MediaRecorder`; request the `RECORD_AUDIO` runtime permission before entering. It is portrait-locked and ignores window insets (full-bleed sphere).

## 5. Navigation

ChatGPT has **no bottom tab bar**. The conversation history is a left drawer — use Material 3 `ModalNavigationDrawer` (edge-swipe with `spring(dampingRatio = 0.8f)` on release). The chat "top nav" is a transparent 52dp row: sidebar toggle (leading) + model chip (center-leading) + "New chat" (trailing). The sidebar groups chats by time (Today / Yesterday / Previous 7 Days / Previous 30 Days / Month Year) and is darker than the canvas — the signature treatment.

```kotlin
import androidx.compose.material.icons.filled.Edit
import androidx.compose.material.icons.filled.Menu
import androidx.compose.material.icons.filled.Search
import androidx.compose.material3.*

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun GptScaffold(
    drawerState: DrawerState,
    sections: List<Pair<String, List<String>>>, // section title to chat titles
    onNewChat: () -> Unit,
    chat: @Composable () -> Unit,
) {
    val dark = isSystemInDarkTheme()
    ModalNavigationDrawer(
        drawerState = drawerState,
        drawerContent = {
            ModalDrawerSheet(
                drawerContainerColor = if (dark) GptColors.SidebarDark else GptColors.SidebarLight,
                modifier = Modifier.width(300.dp),
            ) {
                Column {
                    SidebarButton(Icons.Filled.Edit, "New chat", onNewChat)
                    SidebarButton(Icons.Filled.Search, "Search chats") {}
                    LazyColumn {
                        sections.forEach { (title, chats) ->
                            item {
                                Text(title, style = GptText.SidebarSection,
                                    color = if (dark) GptColors.DarkTextSecondary else GptColors.TextSecondary,
                                    modifier = Modifier.padding(start = 12.dp, top = 16.dp, bottom = 4.dp))
                            }
                            items(chats) { c ->
                                Box(
                                    Modifier.fillMaxWidth().height(40.dp)
                                        .padding(horizontal = 12.dp),
                                    contentAlignment = Alignment.CenterStart,
                                ) {
                                    Text(c, style = GptText.SidebarTitle, maxLines = 1,
                                        color = if (dark) GptColors.DarkTextPrimary else GptColors.TextPrimary)
                                }
                            }
                        }
                    }
                }
            }
        },
    ) {
        Scaffold(
            containerColor = if (dark) GptColors.DarkCanvas else GptColors.Canvas,
            topBar = {
                Row(
                    Modifier.fillMaxWidth().padding(horizontal = 12.dp, vertical = 8.dp),
                    verticalAlignment = Alignment.CenterVertically,
                ) {
                    Icon(Icons.Filled.Menu, "Open conversations",
                        tint = if (dark) GptColors.DarkTextPrimary else GptColors.TextPrimary,
                        modifier = Modifier.size(24.dp))
                    Spacer(Modifier.width(8.dp))
                    ModelSelectorChip("GPT-4o", onTap = {})
                    Spacer(Modifier.weight(1f))
                    Icon(Icons.Filled.Edit, "New chat",
                        tint = if (dark) GptColors.DarkTextPrimary else GptColors.TextPrimary,
                        modifier = Modifier.size(24.dp).clickable { onNewChat() })
                }
            },
        ) { padding -> Box(Modifier.padding(padding)) { chat() } }
    }
}

@Composable
private fun SidebarButton(icon: ImageVector, label: String, onClick: () -> Unit) {
    val dark = isSystemInDarkTheme()
    Row(
        Modifier.padding(12.dp).fillMaxWidth().clip(RoundedCornerShape(8.dp))
            .background(if (dark) GptColors.SidebarActiveDk else GptColors.SidebarActive)
            .clickable { onClick() }.padding(horizontal = 12.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(icon, null, tint = if (dark) GptColors.DarkTextPrimary else GptColors.TextPrimary,
            modifier = Modifier.size(18.dp))
        Text(label, style = GptText.Button,
            color = if (dark) GptColors.DarkTextPrimary else GptColors.TextPrimary)
    }
}
```

Bottom sheets (model picker, attachment sheet) use Material 3 `ModalBottomSheet`. Android has no live blur; ChatGPT's sheets use a plain dim scrim anyway, so use `scrimColor` directly — nothing to approximate.

## 6. Motion

ChatGPT is restrained and editorial — streaming text has **no animation** (append tokens character-by-character).

| Moment | Compose recipe |
|--------|----------------|
| Message send | user bubble `slideInVertically` + `fadeIn` 250ms ease-out; send button scale 0.9 → 1.0 `spring` |
| Typing indicator | `rememberInfiniteTransition` 3 dots, alpha 0.3 ↔ 1.0 over 600ms, staggered 200ms |
| Assistant streaming | none — append to the string as tokens arrive |
| Regenerate | response `fadeOut` 200ms, new response streams in |
| Voice mode entry | `scaleIn(0.9f) + fadeIn(tween(400))` spring; `HapticFeedbackType.LongPress` (.impact solid) |
| Voice sphere | `Animatable` scale 0.95 ↔ 1.05 over 2s + sine deformation on the `Canvas` radius |
| Drawer slide | `ModalNavigationDrawer` edge-swipe; `spring(dampingRatio = 0.8f)` on release |
| Copy button | swap icon + label to "Copied!" for ~1s then revert |
| Thumbs feedback | fill icon + `scale 1.0 → 1.15 → 1.0` `spring`; `HapticFeedbackType.TextHandleMove` (.soft) |

```kotlin
// Message-send bubble entrance
AnimatedVisibility(
    visible = true,
    enter = slideInVertically(tween(250)) { it / 2 } + fadeIn(tween(250)),
) { UserMessageBubble(text) }
```

Honor **Reduce Motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, render the voice sphere static (no pulse/morph), the typing indicator as 3 static dots with a "Generating…" label, and skip the send-button scale — keep haptics. For richer haptics use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(8, ...)` to approximate iOS's `.soft` thumbs feedback.

## 7. Icons

ChatGPT uses simple glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. The **ChatGPT sparkle/blossom logomark** is proprietary — ship it as a vector drawable and load via `ImageVector.vectorResource(R.drawable.…)`; `Icons.Filled.AutoAwesome` is a reasonable stand-in for the assistant avatar in prototypes.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Send | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Stop generating | `stop.fill` | `Icons.Filled.Stop` |
| Voice (mic) | `mic.fill` / `waveform` | `Icons.Filled.Mic` |
| Attach (+) | `plus` | `Icons.Filled.Add` |
| Web search | `globe` | `Icons.Filled.Public` |
| New chat | `square.and.pencil` | `Icons.Filled.Edit` |
| Sidebar toggle | `sidebar.leading` | `Icons.Filled.Menu` |
| Regenerate | `arrow.triangle.2.circlepath` | `Icons.Filled.Refresh` |
| Copy | `doc.on.doc` | `Icons.Filled.ContentCopy` |
| Thumbs up | `hand.thumbsup` / `.fill` | `Icons.Filled.ThumbUp` |
| Thumbs down | `hand.thumbsdown` / `.fill` | `Icons.Filled.ThumbDown` |
| Sparkle (assistant avatar) | `sparkles` | `Icons.Filled.AutoAwesome` (or custom logomark) |
| Model chip chevron | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Close (X) | `xmark` | `Icons.Filled.Close` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Copied confirmation | `checkmark` | `Icons.Filled.Check` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + audio comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. Söhne requires a commercial license from Klim Type Foundry — Inter (OFL) or Roboto (system) are acceptable free fallbacks.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; system-bar icon contrast must follow the theme (dark icons on the light canvas, light icons on `#212121`). The composer rises with the IME via `Modifier.imePadding()`; voice mode deliberately ignores insets (full-bleed sphere).
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on chat body, markdown headings, user-bubble text, code blocks (code stays monospace at XL). Pin layout-sensitive text (model chip, sidebar section titles, timestamps, code-block language label) by deriving from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: label user turns "You: {content}" and assistant turns "ChatGPT: {content}"; announce voice mode as "Voice conversation active, ChatGPT is listening". Expose the feedback row via `Modifier.semantics` custom actions ("Regenerate", "Copy", "Good response", "Bad response"). Every icon-only control needs a `contentDescription`.
- **Touch targets**: Material guidance is 48.dp minimum. The send/voice 32dp circles sit in 40–48dp composer hit areas; the **feedback-row icons are a tight 32dp per the iOS spec** — for strict accessibility compliance bump their hit area to 48dp via padding while keeping the 16dp glyph.
- **Contrast**: `#0D0D0D` on `#FFFFFF` and `#ECECEC` on `#212121` exceed WCAG AA at all sizes. `#676767` secondary on white meets AA at 14sp+; in High Contrast mode boost it toward `#3E3E3E`. The link blue `#2A7FFF` on white passes AA for text.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — ChatGPT's identity is the deliberate monochrome (white/charcoal canvas, ink text, blue only on links and the voice sphere) regardless of wallpaper. (Material You suits Google/Material-first apps; ChatGPT is a strong-brand exception.) Unlike most strong-brand apps, ChatGPT *does* support both light and dark equally — switch on `isSystemInDarkTheme()`, never tint from the wallpaper.
