# Telegram (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Telegram's visual language to **Android with Jetpack Compose (Material 3)**: a themeable color system, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. Telegram's whole identity is that the *accent is a single user-chosen variable* and the UI rhythm (bubble shape, type scale, gesture language) holds steady no matter what color is poured in. This file keeps that identity — a themeable accent via `CompositionLocal`, the 17dp bubble with a 6dp notch tail, the floating voice pill, Lottie animated emoji — while making everything idiomatic Android: `NavigationBar` instead of a UITabBar, a translucent `Surface` instead of `.regularMaterial` blur, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars, and `com.airbnb.android:lottie-compose` `6.4+` for animated emoji & sticker playback. No color extraction — Telegram's accent comes from the theme, not media, so `androidx.palette` is not required.

## 1. Color Tokens

Telegram's entire color system is themeable — the accent flows through as a single variable. Model it as a `TelegramTheme` data class hoisted into a `CompositionLocal` so every consuming composable re-tints live when the user picks a new accent.

```kotlin
// ui/theme/TelegramColors.kt
import androidx.compose.ui.graphics.Color

object TelegramColors {
    // Default Accent (user-themeable)
    val Accent        = Color(0xFF0088CC)
    val AccentLight   = Color(0xFF40A7E3)
    val AccentPressed = Color(0xFF0071B0)

    // Bubble (default blue theme)
    val BubbleOutgoing     = Color(0xFF2B86FD)
    val BubbleOutgoingTop  = Color(0xFF2B86FD) // gradient top
    val BubbleOutgoingBot  = Color(0xFF61B3FF) // gradient bottom
    val BubbleIncomingLight = Color(0xFFFFFFFF)
    val BubbleIncomingDark  = Color(0xFF2A2A2A)
    val SecretOutgoing      = Color(0xFF4EA53C)

    // Canvas
    val CanvasLight   = Color(0xFFFFFFFF)
    val CanvasDark    = Color(0xFF212121)
    val CanvasOLED    = Color(0xFF000000)
    val ChatBGBlue    = Color(0xFFDBE7F4) // default soft-blue wallpaper
    val Surface1Light = Color(0xFFF7F7F7)
    val Surface2Light = Color(0xFFEFEFF4)
    val Surface1Dark  = Color(0xFF1C1C1C)
    val Surface2Dark  = Color(0xFF2C2C2E)
    val DividerLight  = Color(0xFFC7C7CC)
    val DividerDark   = Color(0xFF383838)
    val OLEDSurface   = Color(0xFF0A0A0A)

    // Text
    val TextPrimary       = Color(0xFF000000)
    val TextSecondary     = Color(0xFF707579)
    val TextTertiary      = Color(0xFFA0A6AD)
    val TextPrimaryDark   = Color(0xFFFFFFFF)
    val TextSecondaryDark = Color(0xFF8D8E93)

    // Semantic
    val ReadBlue    = Color(0xFF0088CC)
    val SentGray    = Color(0xFFB1B1B1)
    val ErrorRed    = Color(0xFFE53935)
    val Destructive = Color(0xFFE35561)
    val OnlineGreen = Color(0xFF4DD364)
    val PremiumA    = Color(0xFFAE6FFD)
    val PremiumB    = Color(0xFFCE6BFF)
}

// Sender color palette (group chats) — assign by stable sender id
object TgSenderColors {
    private val palette = listOf(
        Color(0xFFFC5C51), Color(0xFFFA790F), Color(0xFF895DD5), Color(0xFF0FB297),
        Color(0xFF00C1A6), Color(0xFF3CA5EC), Color(0xFFFF5274),
    )
    fun forSender(id: Int): Color = palette[(id % palette.size + palette.size) % palette.size]
}
```

### Themeable accent via CompositionLocal

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.*
import androidx.compose.ui.graphics.Color

data class TelegramTheme(
    val accent: Color = TelegramColors.Accent,
    val outgoingBubble: Color = TelegramColors.BubbleOutgoing,
    val useGradientBubbles: Boolean = false,
    val isDark: Boolean = false,
    val isOLED: Boolean = false,
) {
    val canvas: Color get() = when {
        isOLED -> TelegramColors.CanvasOLED
        isDark -> TelegramColors.CanvasDark
        else   -> TelegramColors.CanvasLight
    }
    val textPrimary: Color get() = if (isDark) TelegramColors.TextPrimaryDark else TelegramColors.TextPrimary
    val textSecondary: Color get() = if (isDark) TelegramColors.TextSecondaryDark else TelegramColors.TextSecondary
    val incomingBubble: Color get() = if (isDark) TelegramColors.BubbleIncomingDark else TelegramColors.BubbleIncomingLight
}

val LocalTelegramTheme = staticCompositionLocalOf { TelegramTheme() }

@Composable
fun TelegramAppTheme(
    theme: TelegramTheme = TelegramTheme(),
    content: @Composable () -> Unit,
) {
    // Derive a Material 3 scheme from the live accent so ripples/dividers inherit it
    val scheme = if (theme.isDark) {
        darkColorScheme(
            primary = theme.accent,
            background = theme.canvas,
            surface = if (theme.isOLED) TelegramColors.OLEDSurface else TelegramColors.Surface1Dark,
            onSurface = TelegramColors.TextPrimaryDark,
            outline = TelegramColors.DividerDark,
            error = TelegramColors.ErrorRed,
        )
    } else {
        lightColorScheme(
            primary = theme.accent,
            background = theme.canvas,
            surface = TelegramColors.Surface1Light,
            onSurface = TelegramColors.TextPrimary,
            outline = TelegramColors.DividerLight,
            error = TelegramColors.ErrorRed,
        )
    }
    CompositionLocalProvider(LocalTelegramTheme provides theme) {
        MaterialTheme(colorScheme = scheme, typography = TelegramTypography, content = content)
    }
}
```

Change `theme.accent` (e.g. to `TgSenderColors`-style green, rose, or purple) at the app root and every consuming view re-tints live — that *is* the Telegram product.

## 2. Typography

Telegram ships **no proprietary font** on mobile — it uses the platform UI font. On iOS that's SF Pro; the Android system equivalent is Roboto, so use `FontFamily.Default` and `FontFamily.Monospace` for code blocks. Sizes follow Apple HIG / Material parity at 1:1 pt→sp.

```kotlin
// ui/theme/TelegramType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

private val Sans = FontFamily.Default      // Roboto on Android (Telegram uses the system UI font)
private val Mono = FontFamily.Monospace    // code blocks / pre

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, weights preserved)
object TelegramText {
    val LargeTitle      = TextStyle(Sans, fontWeight = FontWeight.Bold,    fontSize = 34.sp, lineHeight = 38.sp, letterSpacing = 0.37.sp)
    val ScreenTitle     = TextStyle(Sans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.4).sp)
    val ContactName     = TextStyle(Sans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.4).sp)
    val BubbleBody      = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.4).sp)
    val GroupSender     = TextStyle(Sans, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 17.sp, letterSpacing = (-0.2).sp)
    val MessagePreview  = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val TimestampList   = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 16.sp)
    val TimestampBubble = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 11.sp)
    val SectionHeader   = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 16.sp)
    val Body            = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 24.sp, letterSpacing = (-0.4).sp)
    val Callout         = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 21.sp, letterSpacing = (-0.3).sp)
    val Footnote        = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp, letterSpacing = (-0.1).sp)
    val Caption         = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val TabLabel        = TextStyle(Sans, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button          = TextStyle(Sans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 17.sp, letterSpacing = (-0.4).sp)
    val Code            = TextStyle(Mono, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val Blockquote      = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontStyle = FontStyle.Italic, fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.4).sp)
}

// Map onto Material 3 slots so stock components inherit the scale
val TelegramTypography = Typography(
    headlineLarge = TelegramText.LargeTitle,
    titleMedium   = TelegramText.ContactName,
    bodyLarge     = TelegramText.BubbleBody,
    bodyMedium    = TelegramText.MessagePreview,
    labelSmall    = TelegramText.TabLabel,
)
```

## 3. Signature Components

### Compose Bar (with morphing send)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.rotate
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun TgComposeBar(
    onSend: (String) -> Unit,
    onSendSilent: (String) -> Unit,
    onSchedule: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val theme = LocalTelegramTheme.current
    var text by remember { mutableStateOf("") }
    var menuOpen by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current

    Row(
        modifier = modifier
            .fillMaxWidth()
            .background(if (theme.isDark) TelegramColors.Surface1Dark else TelegramColors.CanvasLight)
            .padding(horizontal = 16.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        Icon(
            Icons.Filled.AttachFile, "Attach",
            tint = theme.textSecondary,
            modifier = Modifier.size(22.dp).rotate(45f),
        )
        BasicTextField(
            value = text,
            onValueChange = { text = it },
            textStyle = TelegramText.Callout.copy(color = theme.textPrimary),
            modifier = Modifier.weight(1f),
            decorationBox = { inner ->
                if (text.isEmpty()) Text("Message", style = TelegramText.Callout, color = TelegramColors.TextTertiary)
                inner()
            },
        )
        Icon(Icons.Filled.EmojiEmotions, "Emoji", tint = theme.textSecondary, modifier = Modifier.size(22.dp))

        if (text.isEmpty()) {
            Icon(Icons.Filled.Mic, "Voice message", tint = theme.accent, modifier = Modifier.size(22.dp))
        } else {
            Box {
                Icon(
                    Icons.Filled.Send, "Send",
                    tint = theme.accent,
                    modifier = Modifier
                        .size(22.dp)
                        .combinedClickableSend(
                            onClick = {
                                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // soft impact on send
                                onSend(text); text = ""
                            },
                            onLongClick = { menuOpen = true },
                        ),
                )
                DropdownMenu(menuOpen, onDismissRequest = { menuOpen = false }) {
                    DropdownMenuItem(
                        text = { Text("Send Without Sound") },
                        leadingIcon = { Icon(Icons.Filled.NotificationsOff, null) },
                        onClick = { menuOpen = false; onSendSilent(text); text = "" },
                    )
                    DropdownMenuItem(
                        text = { Text("Schedule Message") },
                        leadingIcon = { Icon(Icons.Filled.Schedule, null) },
                        onClick = { menuOpen = false; onSchedule() },
                    )
                    DropdownMenuItem(
                        text = { Text("Send When Online") },
                        leadingIcon = { Icon(Icons.Filled.Person, null) },
                        onClick = { menuOpen = false; onSend(text); text = "" },
                    )
                }
            }
        }
    }
}

// long-press the send button = the signature silent/scheduled-send gesture
import androidx.compose.foundation.combinedClickable
import androidx.compose.foundation.interaction.MutableInteractionSource
@Composable
private fun Modifier.combinedClickableSend(onClick: () -> Unit, onLongClick: () -> Unit): Modifier =
    this.combinedClickable(
        interactionSource = remember { MutableInteractionSource() },
        indication = null,
        onClick = onClick,
        onLongClick = onLongClick,
    )
```

### Outgoing Bubble (themeable solid + gradient variant, 6dp notch tail)

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.layout.Box
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush

@Composable
fun TgOutgoingBubble(
    text: String,
    timestamp: String,
    isRead: Boolean,
    modifier: Modifier = Modifier,
) {
    val theme = LocalTelegramTheme.current
    // 17dp all corners EXCEPT bottom-trailing = 6dp (subtle notch, not a full point)
    val shape = RoundedCornerShape(topStart = 17.dp, topEnd = 17.dp, bottomStart = 17.dp, bottomEnd = 6.dp)

    Row(modifier.fillMaxWidth().padding(end = 8.dp), horizontalArrangement = Arrangement.End) {
        Spacer(Modifier.weight(0.2f))
        Column(
            modifier = Modifier
                .clip(shape)
                .then(
                    if (theme.useGradientBubbles)
                        Modifier.background(Brush.verticalGradient(listOf(TelegramColors.BubbleOutgoingTop, TelegramColors.BubbleOutgoingBot)))
                    else Modifier.background(theme.outgoingBubble)
                )
                .padding(horizontal = 12.dp, vertical = 8.dp),
            horizontalAlignment = Alignment.End,
        ) {
            Text(text, style = TelegramText.BubbleBody, color = TelegramColors.TextPrimaryDark)
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(timestamp, style = TelegramText.TimestampBubble, color = TelegramColors.TextPrimaryDark.copy(alpha = 0.8f))
                Icon(
                    if (isRead) Icons.Filled.DoneAll else Icons.Filled.Done,
                    contentDescription = if (isRead) "Read" else "Sent",
                    tint = TelegramColors.TextPrimaryDark.copy(alpha = 0.9f),
                    modifier = Modifier.size(12.dp),
                )
            }
        }
    }
}
```

### Incoming Bubble (tail notch mirrored to bottom-leading)

```kotlin
import androidx.compose.foundation.border

@Composable
fun TgIncomingBubble(
    text: String,
    senderName: String?,
    senderId: Int?,
    modifier: Modifier = Modifier,
) {
    val theme = LocalTelegramTheme.current
    val shape = RoundedCornerShape(topStart = 17.dp, topEnd = 17.dp, bottomStart = 6.dp, bottomEnd = 17.dp)

    Row(modifier.fillMaxWidth().padding(start = 8.dp)) {
        Column(
            modifier = Modifier
                .clip(shape)
                .background(theme.incomingBubble)
                .then(if (theme.isDark) Modifier else Modifier.border(1.dp, Color(0xFFE5E5E5), shape))
                .padding(horizontal = 12.dp, vertical = 8.dp),
        ) {
            if (senderName != null && senderId != null) {
                Text(senderName, style = TelegramText.GroupSender, color = TgSenderColors.forSender(senderId))
            }
            Text(text, style = TelegramText.BubbleBody, color = theme.textPrimary)
        }
        Spacer(Modifier.weight(0.2f))
    }
}
```

### Floating Voice Mini-Player

```kotlin
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.draw.shadow

@Composable
fun TgVoiceMiniPlayer(
    title: String,
    progress: Float,
    onClose: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val theme = LocalTelegramTheme.current
    Row(
        modifier = modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .height(40.dp)
            .shadow(16.dp, RoundedCornerShape(22.dp), spotColor = Color.Black.copy(alpha = 0.16f))
            .clip(RoundedCornerShape(22.dp))
            .background(if (theme.isDark) TelegramColors.Surface1Dark else TelegramColors.CanvasLight)
            .padding(horizontal = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Icon(Icons.Filled.Pause, "Pause", tint = theme.accent, modifier = Modifier.size(14.dp))
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(title, style = TelegramText.Footnote.copy(fontWeight = FontWeight.SemiBold), color = theme.textPrimary, maxLines = 1)
            Box(
                Modifier
                    .fillMaxWidth()
                    .height(2.dp)
                    .clip(CircleShape)
                    .background(TelegramColors.DividerLight),
            ) {
                Box(
                    Modifier
                        .fillMaxWidth(progress)
                        .height(2.dp)
                        .clip(CircleShape)
                        .background(theme.accent),
                )
            }
        }
        Icon(
            Icons.Filled.Close, "Close",
            tint = theme.textSecondary,
            modifier = Modifier.size(13.dp).clickableNoRipple(onClose),
        )
    }
}

import androidx.compose.foundation.clickable
@Composable
private fun Modifier.clickableNoRipple(onClick: () -> Unit): Modifier =
    this.clickable(interactionSource = remember { MutableInteractionSource() }, indication = null, onClick = onClick)
```

This pill should be rendered just below the top app bar in the chat `Scaffold` and survive chat navigation (hoist its state into a shared `ViewModel`) — persistence across chats is the Telegram differentiator.

### Swipe-from-Edge to Reply

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.gestures.detectHorizontalDragGestures
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.platform.LocalDensity
import kotlin.math.roundToInt

@Composable
fun TgSwipeToReply(
    onReply: () -> Unit,
    content: @Composable () -> Unit,
) {
    val theme = LocalTelegramTheme.current
    val haptics = LocalHapticFeedback.current
    val density = LocalDensity.current
    val thresholdPx = with(density) { 60.dp.toPx() }
    var offset by remember { mutableFloatStateOf(0f) }
    var ticked by remember { mutableStateOf(false) }
    val animated by animateFloatAsState(offset, spring(dampingRatio = 0.8f, stiffness = 600f), label = "replyOffset")

    Box(
        Modifier.pointerInput(Unit) {
            detectHorizontalDragGestures(
                onDragEnd = {
                    if (offset >= thresholdPx) onReply()
                    offset = 0f; ticked = false
                },
            ) { _, dragAmount ->
                offset = (offset + dragAmount).coerceIn(0f, thresholdPx + 20f)
                if (offset >= thresholdPx && !ticked) {
                    ticked = true
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // soft tick at threshold
                }
            }
        },
    ) {
        Icon(
            Icons.Filled.Reply, null,
            tint = theme.accent,
            modifier = Modifier
                .align(Alignment.CenterStart)
                .offset { IntOffset((-32 + animated * 0.3f).roundToInt(), 0) }
                .graphicsAlpha((animated / thresholdPx).coerceIn(0f, 1f)),
        )
        Box(Modifier.offset { IntOffset(animated.roundToInt(), 0) }) { content() }
    }
}

import androidx.compose.ui.draw.alpha
import androidx.compose.ui.unit.IntOffset
private fun Modifier.graphicsAlpha(a: Float) = this.alpha(a)
```

### Animated Emoji / Sticker (Lottie)

Telegram's crown jewel: a single-emoji message renders large and plays a vector animation once on arrival. iOS uses `lottie-ios`; Compose uses `lottie-compose`.

```kotlin
import com.airbnb.lottie.compose.*

@Composable
fun TgAnimatedEmoji(
    assetName: String,         // e.g. "emoji/heart.json" in assets/
    sizeDp: Int = 72,          // single emoji renders at 72dp
    modifier: Modifier = Modifier,
) {
    val composition by rememberLottieComposition(LottieCompositionSpec.Asset(assetName))
    // play once on arrival; bump iterations to loop "interactive" stickers
    val progress by animateLottieCompositionAsState(
        composition,
        iterations = 1,
        speed = 1f,
    )
    LottieAnimation(
        composition = composition,
        progress = { progress },
        modifier = modifier.size(sizeDp.dp),
    )
}
```

For "interactive" emoji (heart/rocket) that play full-screen across both chats, render a `LottieAnimation` at ~1024dp in a `Box` overlay (`Popup` or top-level `ZStack`) and auto-dismiss on `progress == 1f`.

## 4. Chat List Row

```kotlin
@Composable
fun TgChatListRow(
    avatarUrl: String,
    name: String,
    preview: String,
    timestamp: String,
    unreadCount: Int,
    isPinned: Boolean,
    isMuted: Boolean,
    modifier: Modifier = Modifier,
) {
    val theme = LocalTelegramTheme.current
    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(76.dp)
            .background(if (isPinned) TelegramColors.Surface1Light else theme.canvas)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        coil.compose.AsyncImage(
            model = avatarUrl, contentDescription = null,
            modifier = Modifier.size(54.dp).clip(CircleShape),
            contentScale = androidx.compose.ui.layout.ContentScale.Crop,
        )
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(name, style = TelegramText.ContactName, color = theme.textPrimary, maxLines = 1)
                if (isMuted) Icon(Icons.Filled.NotificationsOff, null, tint = theme.textSecondary, modifier = Modifier.size(11.dp))
            }
            Text(preview, style = TelegramText.MessagePreview, color = theme.textSecondary, maxLines = 1)
        }
        Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(timestamp, style = TelegramText.TimestampList, color = theme.textSecondary)
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                if (isPinned) Icon(Icons.Filled.PushPin, null, tint = theme.textSecondary, modifier = Modifier.size(11.dp))
                if (unreadCount > 0) {
                    Box(
                        Modifier
                            .clip(RoundedCornerShape(10.dp))
                            .background(if (isMuted) TelegramColors.TextTertiary else theme.accent)
                            .padding(horizontal = 7.dp, vertical = 2.dp),
                    ) {
                        Text("$unreadCount", style = TelegramText.TimestampList.copy(fontWeight = FontWeight.SemiBold), color = Color.White)
                    }
                }
            }
        }
    }
}
```

## 5. Navigation (Tab Bar)

Telegram's iOS tab bar is a `.regularMaterial` blur over the canvas with **custom outlined glyphs** (not SF Symbols everywhere). Android has no first-class live blur — use a translucent `NavigationBar` (`containerColor` at ~92% alpha) and ship the brand-specific glyphs (the Telegram plane, the channel megaphone) as vector drawables. The active tint reads from the live themed accent.

```kotlin
@Composable
fun TgBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    val theme = LocalTelegramTheme.current
    NavigationBar(
        containerColor = theme.canvas.copy(alpha = 0.92f), // translucent — no live blur on Android
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Contacts" to Icons.Filled.Contacts,
            "Calls"    to Icons.Filled.Call,
            "Chats"    to Icons.Filled.ChatBubble,   // swap for custom Telegram bubble drawable
            "Settings" to Icons.Filled.Settings,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(28.dp)) },
                label = { Text(label, style = TelegramText.TabLabel) },
                alwaysShowLabel = true,
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = theme.accent,
                    selectedTextColor   = theme.accent,
                    unselectedIconColor = TelegramColors.TextSecondaryDark,
                    unselectedTextColor = TelegramColors.TextSecondaryDark,
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
| Bubble arrival | enter `slideInVertically` + slight overshoot `spring(dampingRatio = 0.6f)` |
| Animated emoji | Lottie `iterations = 1`, plays once on arrival; interactive emoji full-screen overlay |
| Swipe-to-reply | `detectHorizontalDragGestures`, soft haptic tick at 60dp threshold |
| Silent-send long-press | `combinedClickable(onLongClick)` → `DropdownMenu` (Android long-press ~500ms) |
| Voice mini-player entry | `AnimatedVisibility` + `slideInVertically(initialOffsetY = { -it })` over ~200ms |
| Reactions palette | `scaleIn() + fadeIn()` from bubble center, `spring`, ~300ms |
| Context menu open | bubble `scale 1.0 → 1.05`, translucent scrim, menu fans below |

```kotlin
// Bubble arrival — slide up + overshoot
@Composable
fun BubbleAppear(visible: Boolean, content: @Composable () -> Unit) {
    androidx.compose.animation.AnimatedVisibility(
        visible = visible,
        enter = androidx.compose.animation.slideInVertically(
            animationSpec = spring(dampingRatio = 0.6f, stiffness = 400f),
            initialOffsetY = { it / 3 },
        ) + androidx.compose.animation.fadeIn(),
    ) { content() }
}
```

Haptics: prefer `LocalHapticFeedback`. For a true *soft* impact on send (matching iOS `.impact(flexibility: .soft)`), use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(8, 80)` — light and brief, like Telegram's send tick.

## 7. Icons

Telegram uses a **mix** of system glyphs and a proprietary outlined set. Use Material Icons (`material-icons-extended`) where semantically equivalent; ship brand-specific glyphs (the Telegram plane, channel megaphone, sticker face, premium gradient star) as vector drawables loaded via `ImageVector.vectorResource(R.drawable.…)`.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Send | `paperplane.fill` | `Icons.Filled.Send` (or custom Telegram plane drawable) |
| Mic | `mic.fill` | `Icons.Filled.Mic` |
| Attach | `paperclip` (rotated 45°) | `Icons.Filled.AttachFile` (`.rotate(45f)`) |
| Emoji | `face.smiling` | `Icons.Filled.EmojiEmotions` |
| Sticker | custom outlined sticker | vector drawable |
| Read ticks | `checkmark` / `checkmark.circle.fill` | `Icons.Filled.Done` / `Icons.Filled.DoneAll` |
| Phone | `phone.fill` | `Icons.Filled.Call` |
| Video | `video.fill` | `Icons.Filled.Videocam` |
| Lock (secret chat) | `lock.fill` | `Icons.Filled.Lock` |
| Pinned | `pin.fill` | `Icons.Filled.PushPin` |
| Muted | `speaker.slash.fill` | `Icons.Filled.NotificationsOff` |
| Chats tab | `bubble.left.and.bubble.right.fill` | `Icons.Filled.ChatBubble` (or custom) |
| Contacts tab | `person.2.fill` | `Icons.Filled.Contacts` |
| Calls tab | `phone.fill` | `Icons.Filled.Call` |
| Settings tab | `gearshape.fill` | `Icons.Filled.Settings` |
| Channel broadcast | custom megaphone | vector drawable |
| Premium star | custom gradient star | vector drawable |
| Reply | `arrowshape.turn.up.left.fill` | `Icons.Filled.Reply` |
| Schedule | `calendar` | `Icons.Filled.Schedule` |
| Silent | `speaker.slash` | `Icons.Filled.NotificationsOff` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; Lottie + `RoundedCornerShape` per-corner + motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. System-bar icon contrast must follow the live theme (dark icons on the white canvas, light icons on `#212121`/OLED). Apply `Scaffold` insets so the compose bar hugs the keyboard/gesture inset; render the voice mini-player just below the top app bar inside the insets.
- **Themeable accent**: the entire system reads `LocalTelegramTheme.current.accent`. Provide a new `TelegramTheme` at the app root and **every** consuming composable recomposes and re-tints live — this is the core product behavior, so never hardcode a blue.
- **OLED mode**: expose `isOLED` as a stored user preference distinct from system dark; when true the canvas becomes pure `#000000` and surfaces lift to `#0A0A0A`.
- **Font scaling**: `sp` honors the user's font scale — scale bubble body, contact names, previews. Pin the **11sp in-bubble timestamp** and **10sp tab labels** (clamp via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`).
- **TalkBack**: merge bubble text + timestamp + read state into one node — `Modifier.semantics(mergeDescendants = true) { contentDescription = "$text, $timestamp, ${if (isRead) "read" else "sent"}" }`. The swipe-to-reply gesture and decorative tail notch should be `Modifier.clearAndSetSemantics {}`.
- **RTL**: `RoundedCornerShape(topStart=…, bottomStart=…)` auto-mirrors under `LayoutDirection.Rtl`, so the bubble tail notch flips correctly for Arabic/Persian/Hebrew with no extra code.
- **Reduce motion**: detect `Settings.Global.ANIMATOR_DURATION_SCALE == 0f` (or a stored pref) — render Lottie emoji as a static first frame (`progress = { 0f }`) and skip the bubble-arrival overshoot.
- **Inline Markdown**: parse `**bold**`, `__italic__`, `||spoiler||`, `` `code` `` into an `AnnotatedString` with `SpanStyle`s; render spoiler as a blurred/`drawWithContent`-masked span that clears on tap.
- **Dynamic color**: Telegram already *is* a user-theming system. Do **not** layer Material You `dynamicColorScheme()` on top — the accent must come from the user's explicit Telegram theme choice, not the wallpaper, or two competing theming systems fight.
