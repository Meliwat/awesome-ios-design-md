# Messenger (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Messenger's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Messenger's conversation-anchored blue→violet→pink gradient bubble, neutral gray incoming, bouncy reactions popover, big-thumb send, active-now dots) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush.linearGradient` masked over the message stack, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars.

## 1. Color Tokens

```kotlin
// ui/theme/MessengerColors.kt
import androidx.compose.ui.graphics.Color

object MessengerColors {
    // Gradient stops (outgoing bubble only)
    val GradBlue   = Color(0xFF0A7CFF)
    val GradViolet = Color(0xFF9D4EDD)
    val GradPink   = Color(0xFFFF5CA0)

    // UI blue
    val Blue        = Color(0xFF0A7CFF)
    val BluePressed = Color(0xFF0866D6)

    // Canvas & Surface
    val Canvas       = Color(0xFFFFFFFF)
    val CanvasDark   = Color(0xFF000000) // true black — makes the gradient glow
    val Surface      = Color(0xFFF1F1F2)
    val SurfaceDark  = Color(0xFF1C1C1D)
    val Incoming     = Color(0xFFF1F1F2)
    val IncomingDark = Color(0xFF303030)
    val Divider      = Color(0xFFE4E6EB)
    val DividerDark  = Color(0xFF3A3B3C)

    // Text
    val TextPrimary    = Color(0xFF050505)
    val TextPrimaryD   = Color(0xFFE4E6EB)
    val TextSecondary  = Color(0xFF65676B)
    val TextSecondaryD = Color(0xFFB0B3B8)
    val TextTertiary   = Color(0xFF8A8D91)

    // Semantic
    val ActiveGreen = Color(0xFF31D158)
    val Error       = Color(0xFFFA383E)
    val Success     = Color(0xFF31A24C)
}
```

Wire it into Material 3 schemes so ripples, dividers, and stock component colors inherit the brand. Messenger follows the system; the dark scheme uses **pure black** so the gradient luminesces.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val MsgLight = lightColorScheme(
    primary        = MessengerColors.Blue,
    onPrimary      = Color.White,
    background      = MessengerColors.Canvas,
    onBackground   = MessengerColors.TextPrimary,
    surface         = MessengerColors.Canvas,
    onSurface      = MessengerColors.TextPrimary,
    surfaceVariant = MessengerColors.Surface,
    outline        = MessengerColors.Divider,
    error          = MessengerColors.Error,
)

private val MsgDark = darkColorScheme(
    primary        = MessengerColors.Blue,
    onPrimary      = Color.White,
    background      = MessengerColors.CanvasDark, // true black
    onBackground   = MessengerColors.TextPrimaryD,
    surface         = MessengerColors.CanvasDark,
    onSurface      = MessengerColors.TextPrimaryD,
    surfaceVariant = MessengerColors.SurfaceDark,
    outline        = MessengerColors.DividerDark,
    error          = MessengerColors.Error,
)

@Composable
fun MessengerTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) MsgDark else MsgLight,
        typography  = MessengerTypography,
        content     = content,
    )
```

## 2. Typography

Messenger uses **Inter / system sans** at weights 400/600/700. Drop the Inter TTFs in `res/font/`; reactions render as system emoji.

```kotlin
// ui/theme/MessengerType.kt
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

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object MessengerText {
    val LargeTitle  = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.3).sp)
    val ConvoName   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 22.sp)
    val ConvoUnread = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 22.sp)
    val ThreadTitle = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp)
    val MessageBody = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp)
    val Preview     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Timestamp   = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 16.sp)
    val BubbleMeta  = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 14.sp)
    val ReactCount  = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 12.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val ActiveNow   = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 14.sp)
    val System      = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val MessengerTypography = Typography(
    headlineMedium = MessengerText.LargeTitle,
    titleLarge     = MessengerText.ThreadTitle,
    bodyLarge      = MessengerText.MessageBody,
    titleMedium    = MessengerText.ConvoName,
    labelSmall     = MessengerText.Tab,
)
```

## 3. Signature Components

### Conversation-Anchored Gradient Bubble (Signature)

The gradient belongs to the conversation, not the bubble. Render one full-height `Brush.linearGradient` and mask it to the union of outgoing bubble shapes. The simplest robust approach: give every outgoing bubble the SAME `Brush` whose extent is the thread height, translated by the bubble's position so consecutive bubbles continue the ribbon.

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.unit.dp

@Composable
fun OutgoingBubble(
    text: String,
    isLastInRun: Boolean,
    threadHeightPx: Float,
    bubbleTopPx: Float,
    modifier: Modifier = Modifier,
) {
    val tail = if (isLastInRun) 6.dp else 18.dp
    val shape = RoundedCornerShape(
        topStart = 18.dp, topEnd = 18.dp,
        bottomEnd = tail, bottomStart = 18.dp,
    )
    // Gradient anchored to the conversation: start/end span the whole thread,
    // shifted up by this bubble's top so the ribbon is continuous.
    val ribbon = Brush.linearGradient(
        colors = listOf(MessengerColors.GradBlue, MessengerColors.GradViolet, MessengerColors.GradPink),
        start = Offset(0f, -bubbleTopPx),
        end   = Offset(0f, threadHeightPx - bubbleTopPx),
    )

    Row(
        modifier.fillMaxWidth().padding(horizontal = 10.dp, vertical = 1.dp),
        horizontalArrangement = Arrangement.End,
    ) {
        Text(
            text,
            style = MessengerText.MessageBody,
            color = Color.White,
            modifier = Modifier
                .widthIn(max = 270.dp)
                .clip(shape)
                .background(ribbon)
                .padding(horizontal = 14.dp, vertical = 9.dp),
        )
    }
}
```

> Production approach: wrap the messages `LazyColumn` in a layer that draws a single full-screen gradient and use `Modifier.drawWithContent` + `BlendMode.SrcIn` against a mask layer containing every outgoing bubble's rounded rect (collect rects via `onGloballyPositioned`). That guarantees a perfectly continuous ribbon regardless of bubble heights.

### Reactions Popover (Signature — bouncy)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material3.Icon
import androidx.compose.material3.Surface
import androidx.compose.runtime.*
import androidx.compose.ui.draw.scale
import androidx.compose.ui.unit.dp

private val EMOJI = listOf("👍", "❤️", "😆", "😮", "😢", "😡")

@Composable
fun ReactionsPopover(onPick: (String) -> Unit) {
    var shown by remember { mutableStateOf(false) }
    LaunchedEffect(Unit) { shown = true }

    val capsuleScale by animateFloatAsState(
        targetValue = if (shown) 1f else 0.7f,
        animationSpec = spring(dampingRatio = 0.6f, stiffness = 500f),
        label = "capsule",
    )

    Surface(
        shape = CircleShape,
        color = MessengerColors.Canvas,
        shadowElevation = 14.dp,
        modifier = Modifier.scale(capsuleScale),
    ) {
        Row(
            Modifier.padding(horizontal = 16.dp, vertical = 10.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            EMOJI.forEachIndexed { i, e ->
                val s by animateFloatAsState(
                    targetValue = if (shown) 1f else 0.3f,
                    animationSpec = spring(dampingRatio = 0.55f, stiffness = 600f, visibilityThreshold = 0.001f)
                        .let { it }, // staggered via delayed state below
                    label = "emoji$i",
                )
                // Stagger: delay each emoji's "shown" by i*20ms
                LaunchedEffect(Unit) { kotlinx.coroutines.delay(i * 20L) }
                Text(e, fontSize = 30.sp, modifier = Modifier
                    .scale(s)
                    .clickable { onPick(e) })
            }
            Icon(Icons.Filled.Add, contentDescription = "More reactions",
                tint = MessengerColors.TextSecondary)
        }
    }
}

// Corner reaction badge — lands with a bounce
@Composable
fun ReactionBadge(emoji: String, count: Int) {
    var landed by remember { mutableStateOf(false) }
    LaunchedEffect(Unit) { landed = true }
    val s by animateFloatAsState(
        if (landed) 1f else 1.25f,
        spring(dampingRatio = 0.5f, stiffness = 700f), label = "badge",
    )
    Row(
        Modifier
            .scale(s)
            .clip(CircleShape)
            .background(MessengerColors.Canvas)
            .border(1.dp, MessengerColors.Divider, CircleShape)
            .padding(horizontal = 6.dp, vertical = 3.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(3.dp),
    ) {
        Text(emoji, fontSize = 14.sp)
        if (count > 1) Text("$count", style = MessengerText.ReactCount,
            color = MessengerColors.TextSecondary)
    }
}
```

### Big-Thumb Send / One-Tap Like

```kotlin
import androidx.compose.animation.AnimatedContent
import androidx.compose.animation.fadeIn
import androidx.compose.animation.fadeOut
import androidx.compose.animation.togetherWith
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.Send
import androidx.compose.material.icons.filled.ThumbUp
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun ComposerBar() {
    var text by remember { mutableStateOf("") }
    val hasText = text.trim().isNotEmpty()
    val haptics = LocalHapticFeedback.current

    Row(
        Modifier.fillMaxWidth().padding(horizontal = 10.dp, vertical = 8.dp),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        if (!hasText) {
            Row(horizontalArrangement = Arrangement.spacedBy(16.dp),
                verticalAlignment = Alignment.CenterVertically) {
                listOf(Icons.Filled.CameraAlt, Icons.Filled.Image, Icons.Filled.Mic).forEach {
                    Icon(it, contentDescription = null, tint = MessengerColors.Blue,
                        modifier = Modifier.size(22.dp))
                }
            }
        }
        Row(
            Modifier
                .weight(1f)
                .heightIn(min = 36.dp)
                .clip(RoundedCornerShape(18.dp))
                .background(MessengerColors.Surface)
                .padding(horizontal = 12.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            BasicTextField(
                value = text, onValueChange = { text = it },
                textStyle = MessengerText.MessageBody.copy(color = MessengerColors.TextPrimary),
                modifier = Modifier.weight(1f).padding(vertical = 8.dp),
                decorationBox = { inner ->
                    if (text.isEmpty()) Text("Aa", style = MessengerText.MessageBody,
                        color = MessengerColors.TextTertiary)
                    inner()
                },
            )
            Icon(Icons.Filled.EmojiEmotions, contentDescription = null,
                tint = MessengerColors.TextSecondary, modifier = Modifier.size(20.dp))
        }
        // Big-thumb: 👍 when empty (one-tap like), filled send when text exists
        Box(
            Modifier
                .size(32.dp)
                .clickable {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                    text = ""
                },
            contentAlignment = Alignment.Center,
        ) {
            AnimatedContent(targetState = hasText, label = "thumb",
                transitionSpec = { fadeIn() togetherWith fadeOut() }) { typed ->
                if (typed) {
                    Box(Modifier.size(32.dp).clip(CircleShape).background(MessengerColors.Blue),
                        contentAlignment = Alignment.Center) {
                        Icon(Icons.Filled.Send, contentDescription = "Send",
                            tint = Color.White, modifier = Modifier.size(16.dp))
                    }
                } else {
                    Icon(Icons.Filled.ThumbUp, contentDescription = "Send like",
                        tint = MessengerColors.Blue, modifier = Modifier.size(22.dp))
                }
            }
        }
    }
}
```

### Conversation Row (active-now dot)

```kotlin
@Composable
fun ConversationRow(
    name: String, preview: String, time: String, unread: Boolean, activeNow: Boolean,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .height(72.dp)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box {
            Box(Modifier.size(56.dp).clip(CircleShape).background(MessengerColors.Surface))
            if (activeNow) {
                Box(
                    Modifier
                        .align(Alignment.BottomEnd)
                        .size(14.dp)
                        .clip(CircleShape)
                        .background(MessengerColors.Canvas)
                        .padding(2.dp)
                        .clip(CircleShape)
                        .background(MessengerColors.ActiveGreen),
                )
            }
        }
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(3.dp)) {
            Text(name,
                style = if (unread) MessengerText.ConvoUnread else MessengerText.ConvoName,
                color = MessengerColors.TextPrimary)
            Text(preview, style = MessengerText.Preview,
                color = MessengerColors.TextSecondary, maxLines = 1)
        }
        Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(time, style = MessengerText.Timestamp, color = MessengerColors.TextSecondary)
            if (unread) Box(Modifier.size(8.dp).clip(CircleShape).background(MessengerColors.Blue))
        }
    }
}
```

## 4. Active-Now Presence (Dynamic)

Messenger foregrounds presence. Drive the `activeNow` flag from your realtime presence channel and let the green dot recompose live; on first appearance, pulse it once with an `Animatable` 1 → 1.15 → 1 then leave it static.

## 5. Bottom Navigation

Material 3 `NavigationBar` (Chats / Marketplace / Stories). Android has no live blur; use an opaque canvas surface with a hairline. Active tint is Messenger Blue.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun MessengerBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = MessengerColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Chats"       to Icons.Filled.Chat,
            "Marketplace" to Icons.Filled.Storefront,
            "Stories"     to Icons.Filled.PlayCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(26.dp)) },
                label = { Text(label, style = MessengerText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = MessengerColors.Blue,
                    selectedTextColor   = MessengerColors.Blue,
                    unselectedIconColor = MessengerColors.TextSecondary,
                    unselectedTextColor = MessengerColors.TextSecondary,
                    indicatorColor      = Color.Transparent,
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Reactions popover enter | `animateFloatAsState` capsule 0.7 → 1 `spring(dampingRatio = 0.6f)`; per-emoji 0.3 → 1 with `delay(i*20ms)` |
| Reaction badge land | `animateFloatAsState` 1.25 → 1 `spring(dampingRatio = 0.5f)` |
| Sent bubble pop | `animateFloatAsState` 0.85 → 1 `spring(dampingRatio = 0.65f)` |
| Big-thumb morph | `AnimatedContent` thumb ↔ filled-send with `fadeIn()/fadeOut()` |
| Typing dots | three `Animatable` y-offsets driven by staggered `animateTo` in `repeatable` |
| Tab switch | `Crossfade` |

```kotlin
// Typing dots
@Composable
fun TypingBubble() {
    val ys = List(3) { remember { Animatable(0f) } }
    ys.forEachIndexed { i, a ->
        LaunchedEffect(Unit) {
            kotlinx.coroutines.delay(i * 150L)
            a.animateTo(1f, infiniteRepeatable(tween(450), RepeatMode.Reverse))
        }
    }
    Row(
        Modifier
            .clip(RoundedCornerShape(18.dp))
            .background(MessengerColors.Incoming)
            .padding(horizontal = 14.dp, vertical = 12.dp),
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        ys.forEach { a ->
            Box(
                Modifier
                    .size(7.dp)
                    .graphicsLayer { translationY = -a.value * 3f }
                    .clip(CircleShape)
                    .background(MessengerColors.TextSecondary),
            )
        }
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For the like-tap use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+) to approximate iOS's light impact; long-press "big like" uses `LONG_PRESS`.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Export Messenger's exact glyphs as vector drawables for parity if needed.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Send (text present) | `paperplane.fill` | `Icons.Filled.Send` |
| Like / thumbs-up (empty) | `hand.thumbsup.fill` | `Icons.Filled.ThumbUp` |
| Camera | `camera` | `Icons.Filled.CameraAlt` |
| Photo | `photo` | `Icons.Filled.Image` |
| Mic | `mic` | `Icons.Filled.Mic` |
| Emoji / sticker | `face.smiling` | `Icons.Filled.EmojiEmotions` |
| More reactions | `plus` | `Icons.Filled.Add` |
| Audio call | `phone` | `Icons.Filled.Call` |
| Video call | `video` | `Icons.Filled.Videocam` |
| Compose | `square.and.pencil` | `Icons.Filled.Edit` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Chats (tab) | `message.fill` | `Icons.Filled.Chat` |
| Marketplace (tab) | `storefront.fill` | `Icons.Filled.Storefront` |
| Stories (tab) | `play.circle.fill` | `Icons.Filled.PlayCircle` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; system-bar icon color follows the canvas (dark icons on white, light on black). Apply `Scaffold` insets so the composer clears gesture nav and rises with the IME (`Modifier.imePadding()`).
- **Dark mode is pure black**: `#000000` is intentional — it makes the gradient luminesce. Do **not** enable Material You `dynamicColorScheme()`; the gradient + blue identity is fixed regardless of wallpaper.
- **Conversation-anchored gradient**: use a single full-screen `Brush` masked via `BlendMode.SrcIn` over the union of outgoing bubble rects for a truly continuous ribbon; the shared-`Brush`-with-offset approach is acceptable for short threads.
- **Font scaling**: `sp` honors the user's font scale — keep it on names and message bodies. Pin layout-sensitive text (bubble meta, reaction counts, tab labels) via a fixed-density wrapper.
- **TalkBack**: the gradient is decorative — keep the bubble's content description plain ("You said …"). Expose each popover emoji as a labeled button ("React with heart"); announce the big-thumb send as "Send like" when empty and "Send" when text exists; announce existing reactions as "Reacted heart by 3".
- **Touch targets**: Material guidance is 48.dp minimum. Give popover emoji and the 32.dp send a 48.dp effective hit via padding; conversation rows are a full 72.dp.
- **Contrast**: `#65676B` on `#FFFFFF` passes WCAG AA at 13sp+; white on the gradient passes AA across stops — validate the lightest pink region and add a faint text shadow if compliance-critical.
- **Reduce motion**: gate the popover spring/stagger, badge bounce, and bubble pop behind the system animator scale and fall back to a quick `fadeIn`.
