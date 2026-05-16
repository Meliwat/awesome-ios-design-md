# Google Gemini (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Gemini's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, the brand gradient, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Gemini's document-not-feed canvas, the rationed gradient, gradient sparkle + streaming shimmer + focus ring, drawer-only nav) while making everything idiomatic Android — `ModalNavigationDrawer` instead of a UISplitView, `Brush.linearGradient` for the gradient, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3.

## 1. Color Tokens

```kotlin
// ui/theme/GeminiColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object GeminiColors {
    // Light
    val Canvas  = Color(0xFFFFFFFF)
    val Surface = Color(0xFFF0F4F9)
    val Divider = Color(0xFFE3E3E3)
    val TextPrimary   = Color(0xFF1F1F1F)
    val TextSecondary = Color(0xFF5F6368)
    val TextTertiary  = Color(0xFF9AA0A6)

    // Dark
    val DarkCanvas  = Color(0xFF1E1E1E)
    val DarkSurface = Color(0xFF282A2C)
    val DarkDivider = Color(0xFF3C3C3C)
    val DarkTextPrimary   = Color(0xFFE3E3E3)
    val DarkTextSecondary = Color(0xFF9AA0A6)

    // Brand
    val Blue        = Color(0xFF4285F4)
    val BluePressed = Color(0xFF3367D6)
    val DarkBlue    = Color(0xFF8AB4F8)
    val Violet      = Color(0xFF9B72CB)
    val Coral       = Color(0xFFD96570)

    // Semantic
    val Success = Color(0xFF1E8E3E)
    val Warning = Color(0xFFF9AB00)
    val Error   = Color(0xFFD93025)
}

// The single brand gesture — sparkle, streaming edge, focus ring, chip accent.
fun geminiGradient() = Brush.linearGradient(
    listOf(GeminiColors.Blue, GeminiColors.Violet, GeminiColors.Coral)
)
```

Wire it into Material 3 light + dark schemes so ripples, dividers, and default component colors inherit the brand. `primary` is solid Google Blue (the gradient is structural, never a component fill).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val GeminiLight = lightColorScheme(
    primary        = GeminiColors.Blue,
    onPrimary      = Color.White,
    background     = GeminiColors.Canvas,
    onBackground   = GeminiColors.TextPrimary,
    surface        = GeminiColors.Canvas,
    onSurface      = GeminiColors.TextPrimary,
    surfaceVariant = GeminiColors.Surface,
    outline        = GeminiColors.Divider,
    error          = GeminiColors.Error,
)

private val GeminiDark = darkColorScheme(
    primary        = GeminiColors.DarkBlue,
    onPrimary      = Color(0xFF1E1E1E),
    background     = GeminiColors.DarkCanvas,
    onBackground   = GeminiColors.DarkTextPrimary,
    surface        = GeminiColors.DarkCanvas,
    onSurface      = GeminiColors.DarkTextPrimary,
    surfaceVariant = GeminiColors.DarkSurface,
    outline        = GeminiColors.DarkDivider,
    error          = GeminiColors.Error,
)

@Composable
fun GeminiTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) GeminiDark else GeminiLight,
    typography  = GeminiTypography,
    content     = content,
)
```

## 2. Typography

Google Sans is Google's product typeface. The closest free substitute is **Inter** — drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Use Roboto Mono for code. Fall back to the system grotesque (Roboto) if Inter is unavailable.

```kotlin
// ui/theme/GeminiType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val GoogleSans = FontFamily(
    Font(R.font.google_sans_regular, FontWeight.Normal), // 400
    Font(R.font.google_sans_medium,  FontWeight.Medium),  // 500
    Font(R.font.google_sans_bold,    FontWeight.Bold),    // 700
)
val RobotoMono = FontFamily(Font(R.font.roboto_mono_regular, FontWeight.Normal))

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object GeminiText {
    val Greeting    = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.2).sp)
    val Title       = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.1).sp)
    val Section     = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 18.sp, lineHeight = 23.sp)
    val AnswerH2    = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 20.sp, lineHeight = 26.sp)
    val AnswerH3    = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 17.sp, lineHeight = 23.sp)
    val AnswerBody  = TextStyle(GoogleSans, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 25.sp) // ≈ 1.55
    val UserTurn    = TextStyle(GoogleSans, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 23.sp)
    val PromptInput = TextStyle(GoogleSans, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 22.sp)
    val Chip        = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 18.sp)
    val Meta        = TextStyle(GoogleSans, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val Code        = TextStyle(RobotoMono, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 21.sp)
    val LabelUpper  = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Button      = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 19.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val GeminiTypography = Typography(
    headlineLarge = GeminiText.Greeting,
    headlineSmall = GeminiText.Title,
    titleMedium   = GeminiText.Section,
    bodyMedium    = GeminiText.AnswerBody,
    labelSmall    = GeminiText.Meta,
)
```

## 3. Signature Components

### Gradient Sparkle

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.size
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp

@Composable
fun GeminiSparkle(size: Dp = 20.dp, modifier: Modifier = Modifier) {
    Canvas(modifier.size(size)) {
        val w = this.size.width; val h = this.size.height
        val cx = w / 2f; val cy = h / 2f
        // Four-point sparkle: concave-diamond star
        val path = Path().apply {
            moveTo(cx, 0f)
            quadraticBezierTo(cx, cy, w, cy)
            quadraticBezierTo(cx, cy, cx, h)
            quadraticBezierTo(cx, cy, 0f, cy)
            quadraticBezierTo(cx, cy, cx, 0f)
            close()
        }
        drawPath(path, brush = geminiGradient())
    }
}
```

### User Turn Chip

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.clip

@Composable
fun GeminiUserTurn(text: String, modifier: Modifier = Modifier) {
    Row(
        modifier.fillMaxWidth().padding(horizontal = 16.dp),
        horizontalArrangement = Arrangement.End,
    ) {
        Text(
            text,
            style = GeminiText.UserTurn,
            color = MaterialTheme.colorScheme.onBackground,
            modifier = Modifier
                .widthIn(max = 320.dp)
                .clip(RoundedCornerShape(topStart = 20.dp, topEnd = 20.dp,
                    bottomStart = 20.dp, bottomEnd = 4.dp))
                .background(MaterialTheme.colorScheme.surfaceVariant)
                .padding(horizontal = 16.dp, vertical = 12.dp),
        )
    }
}
```

### Assistant Turn (plain text, no bubble)

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material.icons.outlined.ContentCopy
import androidx.compose.material3.Icon

@Composable
fun GeminiAssistantTurn(
    text: String,
    streaming: Boolean,
    modifier: Modifier = Modifier,
) {
    Column(modifier.fillMaxWidth().padding(horizontal = 16.dp), verticalArrangement = Arrangement.spacedBy(12.dp)) {
        Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {
            GeminiSparkle(20.dp)
            Text(text, style = GeminiText.AnswerBody,
                color = MaterialTheme.colorScheme.onBackground, modifier = Modifier.weight(1f))
        }
        if (!streaming) {
            Row(
                Modifier.padding(start = 30.dp),
                horizontalArrangement = Arrangement.spacedBy(20.dp),
            ) {
                listOf(
                    Icons.Outlined.ContentCopy to "Copy",
                    Icons.Filled.Refresh to "Regenerate",
                    Icons.Filled.IosShare to "Share",
                    Icons.Filled.MoreHoriz to "More",
                ).forEach { (icon, cd) ->
                    Icon(icon, cd, tint = MaterialTheme.colorScheme.onSurfaceVariant,
                        modifier = Modifier.size(20.dp))
                }
            }
        }
    }
}
```

### Streaming Shimmer + Thinking

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.layout.Box
import androidx.compose.ui.draw.drawWithContent
import androidx.compose.ui.graphics.BlendMode
import androidx.compose.ui.graphics.Color

@Composable
fun GeminiStreamingText(text: String, modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "stream")
    val x by t.animateFloat(
        -120f, 1000f,
        infiniteRepeatable(tween(1100, easing = LinearEasing)),
        label = "sweep",
    )
    Text(
        text,
        style = GeminiText.AnswerBody,
        color = MaterialTheme.colorScheme.onBackground,
        modifier = modifier.drawWithContent {
            drawContent()
            drawRect(
                brush = Brush.horizontalGradient(
                    0f to Color.Transparent,
                    0.5f to GeminiColors.Violet.copy(alpha = 0.28f),
                    1f to Color.Transparent,
                    startX = x, endX = x + 120f,
                ),
                blendMode = BlendMode.Plus,
            )
        },
    )
}

@Composable
fun GeminiThinking() {
    val t = rememberInfiniteTransition(label = "thinking")
    Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
        repeat(3) { i ->
            val s by t.animateFloat(
                0.5f, 1f,
                infiniteRepeatable(tween(500), RepeatMode.Reverse, StartOffset(i * 150)),
                label = "dot$i",
            )
            Box(
                Modifier.size(7.dp).scale(s).clip(CircleShape).background(geminiGradient())
            )
        }
    }
}
```

### Prompt Bar (gradient focus ring)

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsFocusedAsState
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material.icons.filled.Mic
import androidx.compose.material.icons.filled.Stop
import androidx.compose.runtime.*
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun GeminiPromptBar(
    value: String,
    onValueChange: (String) -> Unit,
    onSend: () -> Unit,
    onStop: () -> Unit,
    streaming: Boolean = false,
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
            .clip(RoundedCornerShape(26.dp))
            .background(MaterialTheme.colorScheme.surfaceVariant)
            .then(
                if (focused) Modifier.border(1.5.dp, geminiGradient(), RoundedCornerShape(26.dp))
                else Modifier
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
            textStyle = GeminiText.PromptInput.copy(color = MaterialTheme.colorScheme.onBackground),
            modifier = Modifier.weight(1f),
            decorationBox = { inner ->
                if (value.isEmpty()) Text("Ask Gemini", style = GeminiText.PromptInput,
                    color = MaterialTheme.colorScheme.onSurfaceVariant)
                inner()
            },
        )
        Icon(Icons.Filled.Mic, "Voice",
            tint = MaterialTheme.colorScheme.onSurfaceVariant, modifier = Modifier.size(22.dp))
        Box(
            Modifier
                .size(36.dp).clip(CircleShape)
                .background(if (streaming || active) GeminiColors.Blue else Color.Transparent)
                .clickable {
                    if (streaming) onStop()
                    else if (active) { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onSend() }
                },
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                if (streaming) Icons.Filled.Stop else Icons.Filled.ArrowUpward,
                if (streaming) "Stop generating" else "Send",
                tint = if (streaming || active) Color.White else GeminiColors.TextTertiary,
                modifier = Modifier.size(if (streaming) 16.dp else 18.dp),
            )
        }
    }
}
```

### Suggestion Chip

```kotlin
@Composable
fun GeminiSuggestionChip(
    label: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    featured: Boolean = false,
) {
    Row(
        modifier
            .clip(RoundedCornerShape(18.dp))
            .background(MaterialTheme.colorScheme.surfaceVariant)
            .then(
                if (featured) Modifier.border(1.dp, geminiGradient(), RoundedCornerShape(18.dp))
                else Modifier.border(1.dp, MaterialTheme.colorScheme.outline, RoundedCornerShape(18.dp))
            )
            .clickable(onClick = onClick)
            .padding(horizontal = 16.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        if (featured) GeminiSparkle(12.dp)
        Text(label, style = GeminiText.Chip, color = MaterialTheme.colorScheme.onBackground)
    }
}
```

## 4. Navigation (no bottom bar — drawer + top bar)

Gemini has **no bottom navigation**. Use Material 3 `ModalNavigationDrawer` as the sole primary nav, with a `CenterAlignedTopAppBar` whose title is the tappable model selector.

```kotlin
import androidx.compose.material3.*

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun GeminiScaffold() {
    val drawerState = rememberDrawerState(DrawerValue.Closed)
    val scope = rememberCoroutineScope()

    ModalNavigationDrawer(
        drawerState = drawerState,
        scrimColor = Color.Black.copy(alpha = 0.40f),
        drawerContent = {
            ModalDrawerSheet(
                drawerContainerColor = MaterialTheme.colorScheme.background,
                modifier = Modifier.fillMaxWidth(0.82f),
            ) { GeminiDrawerContent() }
        },
    ) {
        Scaffold(
            topBar = {
                CenterAlignedTopAppBar(
                    title = {
                        Row(verticalAlignment = Alignment.CenterVertically) {
                            Text("Gemini", style = GeminiText.Button)
                            Icon(Icons.Filled.ExpandMore, "Switch model", Modifier.size(18.dp))
                        }
                    },
                    navigationIcon = {
                        IconButton(onClick = { scope.launch { drawerState.open() } }) {
                            Icon(Icons.Filled.Menu, "Open drawer")
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
            bottomBar = { /* prompt bar — NOT a NavigationBar */ },
        ) { pad -> ConversationList(Modifier.padding(pad)) }
    }
}
```

```kotlin
@Composable
fun GeminiDrawerContent() {
    Column(Modifier.fillMaxSize().padding(16.dp), verticalArrangement = Arrangement.spacedBy(8.dp)) {
        Row(
            Modifier.clip(CircleShape).background(MaterialTheme.colorScheme.surfaceVariant)
                .clickable { }.padding(horizontal = 16.dp, vertical = 12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Icon(Icons.Filled.Add, null, Modifier.size(20.dp))
            Text("New chat", style = GeminiText.Button)
        }
        Text("TODAY", style = GeminiText.LabelUpper,
            color = MaterialTheme.colorScheme.onSurfaceVariant,
            modifier = Modifier.padding(top = 16.dp, start = 8.dp))
        // recents rows: 48.dp, active row gets a surfaceVariant pill highlight
    }
}
```

## 5. Code Block (inside an answer)

```kotlin
@Composable
fun GeminiCodeBlock(language: String, code: String) {
    Column(
        Modifier.fillMaxWidth()
            .clip(RoundedCornerShape(12.dp))
            .background(MaterialTheme.colorScheme.surfaceVariant)
            .border(1.dp, MaterialTheme.colorScheme.outline, RoundedCornerShape(12.dp)),
    ) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 12.dp, vertical = 8.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            Text(language, style = GeminiText.Meta, color = MaterialTheme.colorScheme.onSurfaceVariant)
            Text("Copy", style = GeminiText.Button)
        }
        Text(code, style = GeminiText.Code,
            modifier = Modifier.horizontalScroll(rememberScrollState()).padding(16.dp))
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Streaming sweep | `rememberInfiniteTransition` driving a horizontal gradient `drawWithContent` overlay (BlendMode.Plus) |
| Thinking dots | `rememberInfiniteTransition` 3 gradient dots scaling 0.5 → 1 with `StartOffset(i*150)` |
| Send ⇄ Stop | swap the icon; `Crossfade` or `AnimatedContent` on the streaming flag |
| Prompt focus ring | toggle `Modifier.border(geminiGradient())`; `animateDpAsState` on width over 180ms |
| Drawer | `ModalNavigationDrawer` default slide + scrim fade |
| Suggestion chip tap | `scale` 1 → 0.98; cross-fade label into the prompt field |
| Copy confirm | `AnimatedVisibility(enter = slideInVertically + fadeIn)`, gradient-edged Snackbar, 1.5s |

```kotlin
// Idle sparkle shimmer (empty-state hero) — animate gradient start offset slowly
@Composable
fun ShimmerSparkle(size: Dp) {
    val t = rememberInfiniteTransition(label = "sparkle")
    val o by t.animateFloat(0f, 1f,
        infiniteRepeatable(tween(2600, easing = LinearEasing)), label = "shimmer")
    Canvas(Modifier.size(size)) {
        val brush = Brush.linearGradient(
            listOf(GeminiColors.Blue, GeminiColors.Violet, GeminiColors.Coral),
            start = Offset(this.size.width * (o - 1f), 0f),
            end   = Offset(this.size.width * (o + 1f), this.size.height),
        )
        // draw the sparkle path with `brush` (see GeminiSparkle)
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's `.soft` impact on send.

## 7. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. The **sparkle is custom** — draw the gradient path (see `GeminiSparkle`) or load a vector drawable with a gradient `<aapt:attr>` fill.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Sparkle (brand) | `sparkle` | custom gradient `Canvas` path |
| Send | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Stop streaming | `stop.fill` | `Icons.Filled.Stop` |
| Mic | `mic` | `Icons.Filled.Mic` |
| Add content | `plus` | `Icons.Filled.Add` |
| Copy | `doc.on.doc` | `Icons.Outlined.ContentCopy` |
| Regenerate | `arrow.clockwise` | `Icons.Filled.Refresh` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Drawer toggle | `line.3.horizontal` | `Icons.Filled.Menu` |
| Model chevron | `chevron.down` | `Icons.Filled.ExpandMore` |
| New chat | `square.and.pencil` | `Icons.Filled.Add` / `Icons.Outlined.Edit` |
| Search (drawer) | `magnifyingglass` | `Icons.Filled.Search` |
| Conversation row | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Account | `person.crop.circle` | `Icons.Filled.AccountCircle` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; in light mode use dark system-bar icons (and the inverse in dark) via `WindowCompat`. Use `Scaffold` insets + `Modifier.imePadding()` so the prompt bar floats above the keyboard.
- **No bottom navigation**: do not add a `NavigationBar`. The `ModalNavigationDrawer` is the sole primary nav; the prompt bar lives in the `bottomBar` slot but is a composer, not nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on greeting, answers, body. Pin the prompt-bar base height and drawer day labels via `dp` or a fixed `Density` override.
- **TalkBack**: mark `GeminiSparkle` with `Modifier.semantics { hideFromAccessibility() }` (decorative); the send button announces "Send" / "Stop generating"; announce stream completion with `LocalView.current.announceForAccessibility(...)`.
- **The gradient is brand, not state**: never rely on it alone to signal focus/streaming — pair the focus ring with a TalkBack focus event and the stream with a live region.
- **Contrast**: `#5F6368` on `#FFFFFF` and `#9AA0A6` on `#1E1E1E` pass WCAG AA at 13sp+. Use `DarkBlue #8AB4F8` for links/send on the dark canvas.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Gemini's brand requires the fixed canvas/surface and the exact `#4285F4 → #9B72CB → #D96570` gradient regardless of wallpaper, identical across light and dark.
