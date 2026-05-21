# Character.AI (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Character.AI's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the asymmetric tuck bubble + roleplay parsing + streaming, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Character.AI's near-black canvas, the chat as the product, the 4dp tuck bubble that differentiates speakers by shape not color, italic `*roleplay*`, the rationed accent blue) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyColumn` for the stream, `AnnotatedString` for roleplay, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for character art. **No Material You** — Character.AI's calm blue is a fixed brand accent and must not be replaced by wallpaper extraction. Dark-first; a light scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/CaiColors.kt
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object CaiColors {
    // Canvas & Surfaces (Dark — primary)
    val Canvas     = Color(0xFF0F0F10) // near-black neutral — NOT pure black
    val Surface1   = Color(0xFF161618)
    val Surface2   = Color(0xFF1E1E21)
    val BubbleAI   = Color(0xFF1C1C1F)
    val BubbleUser = Color(0xFF26262A)
    val Divider    = Color(0xFF2A2A2E)

    // Canvas & Surfaces (Light)
    val CanvasLight     = Color(0xFFFFFFFF)
    val BubbleAILight   = Color(0xFFF1F1F3)
    val BubbleUserLight = Color(0xFFE4E8F2)
    val DividerLight    = Color(0xFFE6E6EA)

    // Brand
    val Accent       = Color(0xFF3A7BFD) // rationed: send/CTA/links/active tab — never a bubble
    val AccentPress  = Color(0xFF2E63D6)
    val AccentSoft   = Color(0xFF1B2A4A)
    val AccentSoftTx = Color(0xFF9FC0FF)
    val Lilac        = Color(0xFF9D7BFF) // avatar gradient only — NOT a control color

    // Text
    val TextPrimary   = Color(0xFFECECEE)
    val TextSecondary = Color(0xFF9A9AA2)
    val TextTertiary  = Color(0xFF66666E)
    val OnAccent      = Color(0xFFFFFFFF)

    // Semantic
    val Success = Color(0xFF4ED99A)
    val Error   = Color(0xFFFF6B6B)

    // Character avatar gradients — the single warm focal point
    fun avatarBlue(r: Float)  = Brush.radialGradient(
        listOf(Color(0xFF6FA0FF), Accent, Color(0xFF274FB0)),
        center = Offset(r * 0.36f, r * 0.30f), radius = r
    )
    fun avatarLilac(r: Float) = Brush.radialGradient(
        listOf(Color(0xFFC7A8FF), Lilac, Color(0xFF6A47C9)),
        center = Offset(r * 0.36f, r * 0.30f), radius = r
    )
}
```

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme

private val CaiDark = darkColorScheme(
    primary        = CaiColors.Accent,
    onPrimary      = CaiColors.OnAccent,
    background      = CaiColors.Canvas,
    onBackground   = CaiColors.TextPrimary,
    surface        = CaiColors.Surface1,
    onSurface      = CaiColors.TextPrimary,
    surfaceVariant = CaiColors.Surface2,
    outline        = CaiColors.Divider,
    error          = CaiColors.Error,
)

private val CaiLight = lightColorScheme(
    primary        = CaiColors.Accent,
    onPrimary      = CaiColors.OnAccent,
    background      = CaiColors.CanvasLight,
    onBackground   = Color(0xFF16161A),
    surface        = Color(0xFFF4F4F6),
    onSurface      = Color(0xFF16161A),
    surfaceVariant = CaiColors.BubbleAILight,
    outline        = CaiColors.DividerLight,
    error          = CaiColors.Error,
)

@Composable
fun CaiTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) CaiDark else CaiLight,
    typography  = CaiTypography,
    content     = content,
)
```

## 2. Typography

Character.AI pairs **Sora** (identity: titles, names, buttons) with **Inter** (reading comfort: all message text, descriptions). Both SIL OFL — drop the TTFs (incl. Inter italic) in `res/font/`. Weights 400–700.

```kotlin
// ui/theme/CaiType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Sora = FontFamily(
    Font(R.font.sora_semibold, FontWeight.SemiBold),
    Font(R.font.sora_bold,     FontWeight.Bold),
)
val Inter = FontFamily(
    Font(R.font.inter_regular, FontWeight.Normal),
    Font(R.font.inter_italic,  FontWeight.Normal, FontStyle.Italic),
    Font(R.font.inter_medium,  FontWeight.Medium),
)

object CaiText {
    val Display      = TextStyle(Sora, fontWeight = FontWeight.Bold,     fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.5).sp)
    val ScreenTitle  = TextStyle(Sora, fontWeight = FontWeight.SemiBold, fontSize = 24.sp, lineHeight = 30.sp, letterSpacing = (-0.3).sp)
    val GreetingName = TextStyle(Sora, fontWeight = FontWeight.SemiBold, fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val HeaderName   = TextStyle(Sora, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 22.sp)
    val CardName     = TextStyle(Sora, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Button       = TextStyle(Sora, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 15.sp)
    val Tab          = TextStyle(Sora, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp)
    val Message      = TextStyle(Inter, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 23.sp)
    val Roleplay     = TextStyle(Inter, fontWeight = FontWeight.Normal, fontStyle = FontStyle.Italic, fontSize = 15.sp, lineHeight = 23.sp)
    val CardDesc     = TextStyle(Inter, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 19.sp)
    val Body         = TextStyle(Inter, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 25.sp)
    val Meta         = TextStyle(Inter, fontWeight = FontWeight.Medium, fontSize = 11.sp, lineHeight = 14.sp)
}

val CaiTypography = Typography(
    headlineLarge  = CaiText.Display,
    headlineMedium = CaiText.ScreenTitle,
    titleMedium    = CaiText.HeaderName,
    bodyMedium     = CaiText.Message,
    labelSmall     = CaiText.Tab,
)
```

## 3. Signature Components

### Asymmetric Tuck Bubble (with roleplay AnnotatedString)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.text.AnnotatedString
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.withStyle
import androidx.compose.ui.unit.dp

private const val TUCK = 4
private const val ROUND = 18

// AI tucks the TOP-START corner (points back at the avatar); user mirrors to TOP-END.
fun bubbleShape(isAi: Boolean) = RoundedCornerShape(
    topStart  = (if (isAi)  TUCK else ROUND).dp,
    topEnd    = (if (!isAi) TUCK else ROUND).dp,
    bottomEnd = ROUND.dp,
    bottomStart = ROUND.dp,
)

// `*roleplay actions*` → italic + secondary. Load-bearing Character.AI signature.
fun parseMessage(raw: String): AnnotatedString = buildAnnotatedString {
    raw.split("*").forEachIndexed { i, seg ->
        if (seg.isEmpty()) return@forEachIndexed
        if (i % 2 == 1) {
            withStyle(SpanStyle(color = CaiColors.TextSecondary, fontStyle = FontStyle.Italic)) { append(seg) }
        } else {
            withStyle(SpanStyle(color = CaiColors.TextPrimary)) { append(seg) }
        }
    }
}

@Composable
fun MessageBubble(isAi: Boolean, text: String, avatar: Brush) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 14.dp),
        horizontalArrangement = if (isAi) Arrangement.Start else Arrangement.End,
        verticalAlignment = Alignment.Top,
    ) {
        if (isAi) {
            Box(Modifier.padding(top = 2.dp, end = 9.dp).size(26.dp).clip(CircleShape).background(avatar))
        }
        Box(
            Modifier
                .widthIn(max = 320.dp)               // ≈ 86% on phone; tune to screen width
                .clip(bubbleShape(isAi))
                .background(if (isAi) CaiColors.BubbleAI else CaiColors.BubbleUser)
                .then(if (isAi) Modifier.border(1.dp, CaiColors.Divider, bubbleShape(true)) else Modifier)
                .padding(vertical = 11.dp, horizontal = 14.dp),
        ) {
            Text(parseMessage(text), style = CaiText.Message)
        }
    }
}
```

### Typing Indicator

```kotlin
import androidx.compose.animation.core.*

@Composable
fun TypingIndicator(avatar: Brush) {
    val t = rememberInfiniteTransition(label = "typing")
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 14.dp),
        verticalAlignment = Alignment.Top,
    ) {
        Box(Modifier.padding(top = 2.dp, end = 9.dp).size(26.dp).clip(CircleShape).background(avatar))
        Row(
            Modifier
                .clip(bubbleShape(true))
                .background(CaiColors.BubbleAI)
                .border(1.dp, CaiColors.Divider, bubbleShape(true))
                .padding(vertical = 14.dp, horizontal = 16.dp),
            horizontalArrangement = Arrangement.spacedBy(5.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            repeat(3) { i ->
                val a by t.animateFloat(
                    0.4f, 1f,
                    infiniteRepeatable(tween(600, delayMillis = i * 160), RepeatMode.Reverse),
                    label = "dot$i",
                )
                Box(Modifier.size(6.dp).clip(CircleShape).background(CaiColors.TextTertiary.copy(alpha = a)))
            }
        }
    }
}
```

### Greeting Header

```kotlin
@Composable
fun GreetingHeader(avatar: Brush, name: String, tagline: String) {
    var shown by remember { mutableStateOf(false) }
    val p by animateFloatAsState(if (shown) 1f else 0f, tween(260), label = "greeting")
    LaunchedEffect(Unit) { shown = true }

    Column(
        Modifier.fillMaxWidth().padding(vertical = 8.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Box(
            Modifier
                .size(64.dp).graphicsLayer { alpha = p; scaleX = 0.92f + 0.08f * p; scaleY = 0.92f + 0.08f * p }
                .clip(CircleShape).background(avatar)
        )
        Text(name, style = CaiText.GreetingName, color = CaiColors.TextPrimary, modifier = Modifier.graphicsLayer { alpha = p })
        Text(tagline, style = CaiText.CardDesc, color = CaiColors.TextTertiary, modifier = Modifier.graphicsLayer { alpha = p })
    }
}
```

### Character Card (Discover)

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ChatBubbleOutline
import androidx.compose.material3.Icon
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun CharacterCard(avatar: Brush, name: String, desc: String, chats: String, onClick: () -> Unit) {
    Column(
        Modifier
            .clip(RoundedCornerShape(16.dp))
            .background(CaiColors.Surface2)
            .border(1.dp, CaiColors.Divider, RoundedCornerShape(16.dp))
            .clickable(onClick = onClick)
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Box(Modifier.size(48.dp).clip(CircleShape).background(avatar))
        Text(name, style = CaiText.CardName, color = CaiColors.TextPrimary)
        Text(desc, style = CaiText.CardDesc, color = CaiColors.TextSecondary, maxLines = 2, overflow = TextOverflow.Ellipsis)
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
            Icon(Icons.Filled.ChatBubbleOutline, null, tint = CaiColors.TextTertiary, modifier = Modifier.size(11.dp))
            Text(chats, style = CaiText.Meta, color = CaiColors.TextTertiary)
        }
    }
}
```

### Composer

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material.icons.filled.Stop

@Composable
fun Composer(
    value: String,
    onValueChange: (String) -> Unit,
    characterName: String,
    streaming: Boolean,
    onSend: () -> Unit,
    onStop: () -> Unit,
) {
    val empty = value.isBlank() && !streaming
    Row(
        Modifier
            .fillMaxWidth()
            .drawBehind { drawLine(CaiColors.Divider, Offset(0f, 0f), Offset(size.width, 0f), 1f) }
            .padding(horizontal = 14.dp, vertical = 12.dp),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box(
            Modifier
                .weight(1f)
                .heightIn(min = 44.dp, max = 132.dp)
                .clip(RoundedCornerShape(22.dp))
                .background(CaiColors.Surface2)
                .border(1.dp, CaiColors.Divider, RoundedCornerShape(22.dp))
                .padding(horizontal = 16.dp, vertical = 12.dp),
        ) {
            BasicTextField(
                value = value, onValueChange = onValueChange,
                textStyle = CaiText.Message.copy(color = CaiColors.TextPrimary),
                cursorBrush = SolidColor(CaiColors.Accent),
                decorationBox = { inner ->
                    if (value.isEmpty()) Text("Message $characterName…", style = CaiText.Message, color = CaiColors.TextTertiary)
                    inner()
                },
            )
        }
        Box(
            Modifier
                .size(40.dp).clip(CircleShape)
                .background(if (empty) CaiColors.BubbleUser else CaiColors.Accent)
                .clickable { if (streaming) onStop() else onSend() },
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                if (streaming) Icons.Filled.Stop else Icons.Filled.ArrowUpward,
                contentDescription = if (streaming) "Stop generating" else "Send",
                tint = if (empty) CaiColors.TextTertiary else CaiColors.OnAccent,
                modifier = Modifier.size(18.dp),
            )
        }
    }
}
```

### Buttons

```kotlin
@Composable
fun CaiPrimaryButton(title: String, onClick: () -> Unit) {
    Box(
        Modifier
            .clip(RoundedCornerShape(999.dp)).background(CaiColors.Accent)
            .clickable(onClick = onClick).padding(horizontal = 26.dp, vertical = 13.dp),
        contentAlignment = Alignment.Center,
    ) { Text(title, style = CaiText.Button, color = CaiColors.OnAccent) }
}

@Composable
fun CaiSoftButton(title: String, onClick: () -> Unit) {
    Box(
        Modifier
            .clip(RoundedCornerShape(999.dp)).background(CaiColors.AccentSoft)
            .clickable(onClick = onClick).padding(horizontal = 26.dp, vertical = 13.dp),
        contentAlignment = Alignment.Center,
    ) { Text(title, style = CaiText.Button, color = CaiColors.AccentSoftTx) }
}
```

## 4. Navigation

Character.AI has a thin header and a 4-tab bottom strip with **no tint pill** — active is the blue icon + blue label.

```kotlin
@Composable
fun CaiBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = CaiColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            Icons.Filled.AutoAwesome to "Discover",
            Icons.Filled.ChatBubbleOutline to "Chats",
            Icons.Filled.AddCircleOutline to "Create",
            Icons.Filled.PersonOutline to "Profile",
        )
        items.forEachIndexed { i, (icon, label) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(23.dp)) },
                label = { Text(label, style = CaiText.Tab) },
                alwaysShowLabel = true,
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = CaiColors.Accent,   // blue icon + blue label
                    selectedTextColor = CaiColors.Accent,
                    unselectedIconColor = CaiColors.TextTertiary,
                    unselectedTextColor = CaiColors.TextTertiary,
                    indicatorColor = Color.Transparent,     // NO Material pill — CAI has none
                ),
            )
        }
    }
}
```

The chat header is a thin `Row` (48dp): back chevron, 40dp avatar `Box` with the radial brush, name `CaiText.HeaderName`, sub "by @lumen · 2.4M chats" `CaiText.Meta`, trailing ⋮; underline = a 0.5dp `Divider`.

## 5. Motion

Character.AI motion is minimal and reading-focused — streaming and the typing dots are the only continuous motion; nothing flashy.

| Moment | Compose recipe |
|--------|----------------|
| Message send | new item `AnimatedVisibility(fadeIn() + slideInVertically({ it / 4 }), tween(120))`; `lazyListState.animateScrollToItem(last)` |
| AI streaming | append tokens to a `mutableStateOf` string in the message model; `LazyColumn` re-measures the bubble (no remount); blink a tail caret via `rememberInfiniteTransition` |
| Typing indicator | per-dot `animateFloat` 0.4 → 1f `infiniteRepeatable(tween(600, delay i*160), Reverse)` |
| Greeting entrance | avatar `animateFloatAsState` alpha + scale 0.92 → 1.0 `tween(260)` |
| Send ↔ Stop | icon + bg swap on `streaming` state, `tween(150)` color/`Crossfade` |
| Tab switch | tint cross-dissolves to blue `tween(180)`; no pill |
| Card press | `Modifier.clickable` ripple + `scale` 0.99 |
| Sheet present | `ModalBottomSheet` slide-up (Material default) + scrim |

```kotlin
// Tail caret while streaming — the only "generating" affordance
val caret = rememberInfiniteTransition(label = "caret")
val caretAlpha by caret.animateFloat(1f, 0f, infiniteRepeatable(tween(500), RepeatMode.Reverse), label = "blink")
// append "▍" with alpha=caretAlpha at the end of the streaming bubble text
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for send tap and discrete long-press actions (Copy/Retry/Rate). **Never** fire haptics during token streaming — it would buzz continuously.

## 6. Icons

`androidx.compose.material:material-icons-extended` covers Character.AI's simple iconography.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Discover (tab) | `sparkles` | `Icons.Filled.AutoAwesome` |
| Chats (tab) | `bubble.left.and.bubble.right` | `Icons.Filled.ChatBubbleOutline` |
| Create (tab) | `plus.circle` | `Icons.Filled.AddCircleOutline` |
| Profile (tab) | `person` | `Icons.Filled.PersonOutline` |
| Send | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Stop generation | `stop.fill` | `Icons.Filled.Stop` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Overflow / chat menu | `ellipsis` | `Icons.Filled.MoreVert` |
| Chat-count (card) | `bubble.left.and.bubble.right` | `Icons.Filled.ChatBubbleOutline` |
| New chat | `square.and.pencil` | `Icons.Filled.EditNote` |
| Retry | `arrow.clockwise` | `Icons.Filled.Refresh` |
| Copy | `doc.on.doc` | `Icons.Filled.ContentCopy` |
| Rate up / down | `hand.thumbsup` / `.thumbsdown` | `Icons.Filled.ThumbUpOffAlt` / `ThumbDownOffAlt` |
| Settings | `gearshape` | `Icons.Filled.Settings` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `LazyColumn` streaming + `AnnotatedString` comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the near-black canvas wants light system-bar icons on dark, dark on light. The header sits below the camera cutout; the composer uses `Modifier.imePadding()` so it rises with the IME and the message list scrolls clear.
- **Dark-first, light available**: drive scheme by `isSystemInDarkTheme()`; only canvas/surface/text + bubble greys swap. Keep `Accent`, `Lilac`, and the avatar gradients **identical** across schemes. Do **not** enable `dynamicColorScheme()` — Character.AI's calm blue is a fixed brand accent and must never become a wallpaper color (and must never be a bubble fill in either theme).
- **Roleplay parsing**: `parseMessage` builds an `AnnotatedString` where `*…*` spans are italic + `TextSecondary`. This is a load-bearing signature — keep it everywhere AI text renders, including previews and notifications.
- **Font scaling**: `sp` honors the user's scale — keep it on display/titles/names/message/body so long AI replies grow comfortably; pin only the 10sp tab labels and "by @creator · N chats" meta via a fixed `Density(fontScale = 1f)` provider.
- **Streaming**: mutate the message model's text `State` and let `LazyColumn` re-measure the bubble; never key the bubble on text (it would remount and flicker). The blinking tail caret is the only "generating" cue.
- **TalkBack**: give AI bubbles `Modifier.semantics { contentDescription = "Aria said: $speech. Action: $roleplay" }` so the *narration vs speech* distinction the italics encode is also spoken; label the typing indicator "Aria is typing"; the send button "Send"/"Stop generating". The `*action*` semantics must survive to screen readers — don't strip them.
- **Touch targets**: Material guidance is 48dp — give the 40dp send button and header back/overflow a 48dp hit via padding; the full character card and full bubble (long-press for Copy/Retry/Rate) are the targets.
- **Contrast**: `#ECECEE` on `#0F0F10` is strong; `#FFFFFF` on `#3A7BFD` passes AA for send/CTAs; the italic roleplay `#9A9AA2` on `#1C1C1F` passes AA at 15sp — keep the size.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, show three static typing dots, skip the greeting scale, and make send↔stop an instant swap; keep token streaming (it's content, not decoration).
- **No streaming haptics**: only fire haptics on send and discrete actions — continuous streaming buzz is hostile. The avatar gradient is the only warmth; never rely on blue-vs-lilac alone to identify a character — the name label must carry identity.
