# Grok (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Grok's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Grok's true-black void, monochrome restraint, plain-text streaming, X post citation cards) while making everything idiomatic Android — `NavigationDrawer` instead of a slide-over, `BasicTextField` instead of `UITextField`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars, and an Inter `FontFamily` in `res/font/`.

## 1. Color Tokens

```kotlin
// ui/theme/GrokColors.kt
import androidx.compose.ui.graphics.Color

object GrokColors {
    // Canvas & Surfaces
    val Canvas   = Color(0xFF000000)
    val Surface1 = Color(0xFF16181C)
    val Surface2 = Color(0xFF1E2126)
    val Surface3 = Color(0xFF272A2E)
    val Divider  = Color(0xFF2F3336)

    // Text
    val TextPrimary   = Color(0xFFE7E9EA)
    val TextSecondary = Color(0xFF71767B)
    val TextTertiary  = Color(0xFF4D5156)

    // Functional / Brand
    val AccentWhite  = Color(0xFFFFFFFF)
    val PressedWhite = Color(0xFFD7DBDC)
    val LinkBlue     = Color(0xFF1D9BF0)
    val LinkPressed  = Color(0xFF1A8CD8)
    val Success      = Color(0xFF00BA7C)
    val Error        = Color(0xFFF4212E)
}
```

Wire it into a Material 3 `darkColorScheme` so ripples, dividers, and default component colors inherit the identity. Grok is dark-only; do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val GrokScheme = darkColorScheme(
    primary        = GrokColors.AccentWhite,   // the functional accent is white
    onPrimary      = GrokColors.Canvas,        // black on white (send button)
    background     = GrokColors.Canvas,
    onBackground   = GrokColors.TextPrimary,
    surface        = GrokColors.Surface1,
    onSurface      = GrokColors.TextPrimary,
    surfaceVariant = GrokColors.Surface2,
    outline        = GrokColors.Divider,
    secondary      = GrokColors.LinkBlue,      // links / citations only
    error          = GrokColors.Error,
)

@Composable
fun GrokTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = GrokScheme, typography = GrokTypography, content = content)
```

## 2. Typography

Inter is open-source (SIL OFL). Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Enable the slashed zero via a `FontFeatureSetting` for the technical identity.

```kotlin
// ui/theme/GrokType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

private const val ZERO = "zero" // slashed-zero OpenType feature

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object GrokText {
    val ScreenTitle  = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.4).sp)
    val SectionHead  = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 25.sp, letterSpacing = (-0.3).sp)
    val ConvoTitle   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.2).sp)
    val Body         = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 25.sp)
    val UserMessage  = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 23.sp)
    val PromptInput  = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp)
    val ModePill     = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp)
    val CiteAuthor   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 14.sp, letterSpacing = (-0.1).sp)
    val CiteMeta     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, fontFeatureSettings = ZERO)
    val BodySmall    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 21.sp)
    val Code         = TextStyle(FontFamily.Monospace, fontSize = 13.5.sp, lineHeight = 20.sp, fontFeatureSettings = ZERO)
    val Button       = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp)
    val Caption      = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp)
    val LabelUpper   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 11.sp, letterSpacing = 0.6.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val GrokTypography = Typography(
    headlineLarge = GrokText.ScreenTitle,
    headlineSmall = GrokText.SectionHead,
    titleMedium   = GrokText.ConvoTitle,
    bodyLarge     = GrokText.Body,
    labelSmall    = GrokText.LabelUpper,
)
```

## 3. Signature Components

### User Message Bubble

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

@Composable
fun GrokUserBubble(text: String, modifier: Modifier = Modifier) {
    Row(
        modifier = modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 6.dp),
        horizontalArrangement = Arrangement.End,
    ) {
        Box(
            Modifier
                .widthIn(max = 300.dp) // ~78% on a 390dp screen
                .background(
                    GrokColors.Surface1,
                    RoundedCornerShape(topStart = 20.dp, topEnd = 20.dp,
                                       bottomStart = 20.dp, bottomEnd = 6.dp), // tail
                )
                .padding(horizontal = 16.dp, vertical = 12.dp),
        ) {
            Text(text, style = GrokText.UserMessage, color = GrokColors.TextPrimary)
        }
    }
}
```

### Assistant Response with Streaming Cursor

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.runtime.*

@Composable
fun GrokAssistantResponse(text: String, streaming: Boolean, modifier: Modifier = Modifier) {
    val cursorAlpha by rememberInfiniteTransition(label = "cursor").animateFloat(
        initialValue = 1f, targetValue = 0f,
        animationSpec = infiniteRepeatable(tween(530, easing = LinearEasing), RepeatMode.Reverse),
        label = "blink",
    )
    Column(modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 6.dp)) {
        AsyncImage(
            model = R.drawable.grok_glyph,
            contentDescription = null,
            modifier = Modifier.size(24.dp).padding(bottom = 12.dp),
        )
        Text(
            buildAnnotatedString {
                append(text)
                if (streaming) withStyle(SpanStyle(color = GrokColors.TextPrimary.copy(alpha = cursorAlpha))) {
                    append("▍")
                }
            },
            style = GrokText.Body,
            color = GrokColors.TextPrimary,
        )
    }
}
```

### X Post Citation Card (signature)

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Verified
import androidx.compose.material.icons.outlined.*
import androidx.compose.material3.Icon

@Composable
fun XCitationCard(
    author: String, handle: String, timeAgo: String, verified: Boolean,
    postText: String, replies: Int, reposts: Int, likes: Int,
    onOpen: () -> Unit, modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()

    Column(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp, vertical = 8.dp)
            .clip(RoundedCornerShape(16.dp))
            .background(if (pressed) GrokColors.Surface3 else GrokColors.Surface2)
            .border(
                BorderStroke(1.dp, if (pressed) GrokColors.LinkBlue else GrokColors.Divider),
                RoundedCornerShape(16.dp),
            )
            .clickable(interaction, indication = null, onClick = onOpen)
            .padding(14.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            Box(Modifier.size(28.dp).clip(CircleShape).background(GrokColors.Surface3))
            Text(author, style = GrokText.CiteAuthor, color = GrokColors.TextPrimary)
            if (verified) Icon(Icons.Filled.Verified, null,
                tint = GrokColors.LinkBlue, modifier = Modifier.size(14.dp))
            Text("@$handle · $timeAgo", style = GrokText.CiteMeta, color = GrokColors.TextSecondary)
            Spacer(Modifier.weight(1f))
            Icon(painterResource(R.drawable.ic_x_glyph), "X",
                tint = GrokColors.TextSecondary, modifier = Modifier.size(16.dp))
        }
        Spacer(Modifier.height(10.dp))
        Text(postText, style = GrokText.BodySmall, color = GrokColors.TextPrimary, maxLines = 4)
        Spacer(Modifier.height(10.dp))
        Row(horizontalArrangement = Arrangement.spacedBy(20.dp)) {
            Metric(Icons.Outlined.ChatBubbleOutline, replies)
            Metric(Icons.Outlined.Repeat, reposts)
            Metric(Icons.Outlined.FavoriteBorder, likes)
        }
    }
}

@Composable
private fun Metric(icon: androidx.compose.ui.graphics.vector.ImageVector, n: Int) {
    Row(verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp)) {
        Icon(icon, null, tint = GrokColors.TextSecondary, modifier = Modifier.size(14.dp))
        Text("$n", style = GrokText.Caption, color = GrokColors.TextSecondary)
    }
}
```

### Send Button (state machine)

```kotlin
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material.icons.filled.Stop
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

enum class SendState { Disabled, Enabled, Streaming }

@Composable
fun GrokSendButton(state: SendState, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        if (pressed) 0.92f else 1f,
        spring(dampingRatio = 0.7f, stiffness = 600f), label = "sendScale",
    )
    val haptics = LocalHapticFeedback.current
    val bg = if (state == SendState.Enabled) GrokColors.AccentWhite else GrokColors.Surface3

    Box(
        modifier
            .size(32.dp)
            .scale(scale)
            .clip(CircleShape)
            .background(bg)
            .clickable(
                interaction, indication = null,
                enabled = state != SendState.Disabled,
            ) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS soft impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            imageVector = if (state == SendState.Streaming) Icons.Filled.Stop else Icons.Filled.ArrowUpward,
            contentDescription = if (state == SendState.Streaming) "Stop generating" else "Send message",
            tint = when (state) {
                SendState.Enabled   -> GrokColors.Canvas
                SendState.Streaming -> GrokColors.TextPrimary
                else                -> GrokColors.TextSecondary
            },
            modifier = Modifier.size(16.dp),
        )
    }
}
```

### Mode Toggle (Regular / Fun)

```kotlin
@Composable
fun GrokModeToggle(isFun: Boolean, onChange: (Boolean) -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    Row(
        modifier
            .clip(CircleShape)
            .background(GrokColors.Surface1)
            .border(1.dp, GrokColors.Divider, CircleShape)
            .padding(3.dp),
    ) {
        @Composable
        fun seg(label: String, active: Boolean, fun_: Boolean) {
            Box(
                Modifier
                    .clip(CircleShape)
                    .background(
                        if (active) (if (fun_) GrokColors.Surface1 else GrokColors.AccentWhite)
                        else Color.Transparent
                    )
                    .clickable(indication = null,
                        interactionSource = remember { MutableInteractionSource() }) {
                        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                        onChange(fun_)
                    }
                    .padding(horizontal = 14.dp, vertical = 7.dp),
            ) {
                Text(
                    label, style = GrokText.ModePill,
                    color = if (active) (if (fun_) GrokColors.LinkBlue else GrokColors.Canvas)
                            else GrokColors.TextSecondary,
                )
            }
        }
        seg("Regular", !isFun, false)
        seg("Fun", isFun, true)
    }
}
```

### Prompt Bar

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.outlined.AttachFile

@Composable
fun GrokPromptBar(
    value: String, onValueChange: (String) -> Unit,
    sendState: SendState, onSend: () -> Unit, modifier: Modifier = Modifier,
) {
    var focused by remember { mutableStateOf(false) }
    Row(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(24.dp))
            .background(GrokColors.Surface1)
            .border(1.dp, if (focused) Color(0xFF3E4146) else GrokColors.Divider,
                    RoundedCornerShape(24.dp))
            .padding(horizontal = 12.dp, vertical = 8.dp),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(Icons.Outlined.AttachFile, "Attach",
            tint = GrokColors.TextSecondary, modifier = Modifier.size(18.dp))
        BasicTextField(
            value = value,
            onValueChange = onValueChange,
            textStyle = GrokText.PromptInput.copy(color = GrokColors.TextPrimary),
            cursorBrush = SolidColor(GrokColors.TextPrimary),
            maxLines = 5,
            modifier = Modifier.weight(1f).onFocusChanged { focused = it.isFocused },
            decorationBox = { inner ->
                if (value.isEmpty()) Text("Ask Grok anything",
                    style = GrokText.PromptInput, color = GrokColors.TextSecondary)
                inner()
            },
        )
        GrokSendButton(sendState, onSend)
    }
}
```

## 4. Navigation (no bottom bar)

Grok is single-surface — there is no `NavigationBar`. Use a `Scaffold` with a slim `topBar` hosting the Mode Toggle centered, and a `ModalNavigationDrawer` for conversation history (Android's analog to the iOS slide-over).

```kotlin
@Composable
fun GrokScaffold() {
    var isFun by remember { mutableStateOf(false) }
    Scaffold(
        containerColor = GrokColors.Canvas,
        topBar = {
            Row(
                Modifier.fillMaxWidth().height(44.dp).padding(horizontal = 16.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.SpaceBetween,
            ) {
                Icon(Icons.Filled.Menu, "History", tint = GrokColors.TextPrimary)
                GrokModeToggle(isFun) { isFun = it }
                Icon(Icons.Outlined.Edit, "New chat", tint = GrokColors.TextPrimary)
            }
        },
    ) { pad ->
        Column(Modifier.padding(pad)) {
            // LazyColumn of bubbles/responses/citations; GrokPromptBar pinned above ime padding
        }
    }
}
```

## 5. Motion

| Moment | Compose recipe |
|--------|----------------|
| Streaming cursor | `rememberInfiniteTransition` → `▍` alpha 1 → 0, `tween(530, LinearEasing)`, `RepeatMode.Reverse` |
| Send → Stop | `Crossfade(targetState = sendState, animationSpec = tween(200))` over the icon |
| Mode toggle | `animateColorAsState` on segment bg + `HapticFeedbackType.TextHandleMove` |
| Copy confirm | icon swap to check, `animateColorAsState` to `GrokColors.Success`, revert after 1200ms |
| Searching X pulse | `rememberInfiniteTransition` driving a `globe` glyph alpha 0.4 ↔ 1 over 900ms |
| Send tap | `animateFloatAsState` 1 → 0.92 `spring(dampingRatio = 0.7f)` |

```kotlin
@Composable
fun SearchingXPulse() {
    val a by rememberInfiniteTransition(label = "searchX").animateFloat(
        0.4f, 1f, infiniteRepeatable(tween(900), RepeatMode.Reverse), label = "pulse",
    )
    Row(verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp)) {
        Icon(Icons.Outlined.Public, null,
            tint = GrokColors.TextSecondary.copy(alpha = a), modifier = Modifier.size(16.dp))
        Text("Searching X…", style = GrokText.Caption, color = GrokColors.TextSecondary)
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's `.soft` impact on send.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The X glyph has no Material equivalent — export it as a vector drawable and load via `painterResource(R.drawable.ic_x_glyph)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Send | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Stop generation | `stop.fill` | `Icons.Filled.Stop` |
| Attach | `paperclip` | `Icons.Outlined.AttachFile` |
| New chat | `square.and.pencil` | `Icons.Outlined.Edit` |
| History / menu | `line.3.horizontal` | `Icons.Filled.Menu` |
| Verified (citation) | `checkmark.seal.fill` | `Icons.Filled.Verified` |
| Reply | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Repost | `arrow.2.squarepath` | `Icons.Outlined.Repeat` |
| Like | `heart` | `Icons.Outlined.FavoriteBorder` |
| Copy | `doc.on.doc` | `Icons.Outlined.ContentCopy` → `Icons.Filled.Check` |
| Regenerate | `arrow.clockwise` | `Icons.Filled.Refresh` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Searching X | `globe` | `Icons.Outlined.Public` |
| X logo | (custom) | `R.drawable.ic_x_glyph` (vector) |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the true-black canvas wants `WindowCompat` light-content system bars. Apply `Modifier.imePadding()` so the prompt bar rises with the keyboard and `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so it clears the gesture nav.
- **Slashed zero**: set `fontFeatureSettings = "zero"` on numeral/code styles (already in `GrokText`); confirm the bundled Inter build ships the `zero` feature.
- **Font scaling**: `sp` honors the user's font scale — keep it on body, user message, titles. Pin layout-sensitive text (mode-pill label, Send glyph derived from `dp`, citation meta) by wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: state-aware `contentDescription` on the Send button ("Send message" / "Stop generating"); merge the citation card with `Modifier.semantics(mergeDescendants = true) { role = Role.Button }` and announce "Opens post on X"; push a live-region update when streaming completes.
- **Reduce Motion**: gate the cursor blink and token reveal on `Settings.Global.ANIMATOR_DURATION_SCALE` — when animations are off, render the completed response at once with no cursor.
- **Touch targets**: Material guidance is 48.dp minimum. The 32.dp Send button and 28.dp citation avatar need `Modifier.size(48.dp)` hit areas via padding even though the glyphs are small; citation cards are already large single targets.
- **Contrast**: `#71767B` on `#000000` is borderline at 13sp — validate with a contrast checker and bump secondary text toward `#8B8F94` when the build targets accessibility compliance.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Grok's identity requires the fixed `#000000` canvas and monochrome restraint regardless of wallpaper.
