# Signal (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Signal's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Signal's privacy-minimal calm, white/soft-black canvas, single-blue accent, blue outgoing bubbles, slide-up send) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, per-corner `RoundedCornerShape` for the same-sender tail, `sp`/`dp` instead of `pt`. (Signal's real Android app is itself Material; this maps the iOS spec onto modern Compose.)

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars.

## 1. Color Tokens

```kotlin
// ui/theme/SignalColors.kt
import androidx.compose.ui.graphics.Color

object SignalColors {
    // Canvas & Surface
    val Canvas        = Color(0xFFFFFFFF)
    val CanvasDark    = Color(0xFF1B1B1B)
    val Surface       = Color(0xFFF5F5F5)
    val SurfaceDark   = Color(0xFF2A2A2A)
    val Divider       = Color(0xFFE5E5E5)
    val DividerDark   = Color(0xFF3A3A3A)

    // Text
    val TextPrimary    = Color(0xFF000000)
    val TextPrimaryD   = Color(0xFFFFFFFF)
    val TextSecondary  = Color(0xFF6B6B6B)
    val TextSecondaryD = Color(0xFF9A9A9A)
    val TextTertiary   = Color(0xFF9A9A9A)

    // Brand
    val Blue        = Color(0xFF3A76F0)
    val BluePressed = Color(0xFF2F5FCC)
    val BlueTint    = Color(0xFFE7EEFD)

    // Message
    val Incoming     = Color(0xFFE9E9EB)
    val IncomingDark = Color(0xFF2A2A2A)
    val OutMeta      = Color(0xFFCBD9F9)

    // Semantic
    val Error   = Color(0xFFD7263D)
    val Success = Color(0xFF3AB54A)
}
```

Wire it into Material 3 schemes so ripples, dividers, and stock component colors inherit the brand. Signal follows the system; the dark scheme uses a soft `#1B1B1B`, never pure black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val SignalLight = lightColorScheme(
    primary        = SignalColors.Blue,
    onPrimary      = Color.White,
    background      = SignalColors.Canvas,
    onBackground   = SignalColors.TextPrimary,
    surface         = SignalColors.Canvas,
    onSurface      = SignalColors.TextPrimary,
    surfaceVariant = SignalColors.Surface,
    outline        = SignalColors.Divider,
    error          = SignalColors.Error,
)

private val SignalDark = darkColorScheme(
    primary        = SignalColors.Blue,
    onPrimary      = Color.White,
    background      = SignalColors.CanvasDark,
    onBackground   = SignalColors.TextPrimaryD,
    surface         = SignalColors.CanvasDark,
    onSurface      = SignalColors.TextPrimaryD,
    surfaceVariant = SignalColors.SurfaceDark,
    outline        = SignalColors.DividerDark,
    error          = SignalColors.Error,
)

@Composable
fun SignalTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) SignalDark else SignalLight,
        typography  = SignalTypography,
        content     = content,
    )
```

## 2. Typography

Signal uses **Inter** everywhere. Drop the TTFs in `res/font/` (lowercase, snake_case); the safety number uses a monospace family.

```kotlin
// ui/theme/SignalType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),  // 400
    Font(R.font.inter_medium,   FontWeight.Medium),  // 500
    Font(R.font.inter_semibold, FontWeight.SemiBold),// 600
    Font(R.font.inter_bold,     FontWeight.Bold),    // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object SignalText {
    val LargeTitle    = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.3).sp)
    val ConvoName     = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 17.sp, lineHeight = 22.sp)
    val ThreadTitle   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 21.sp)
    val MessageBody   = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp)
    val Preview       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp)
    val SectionHeader = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.4.sp)
    val Timestamp     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 16.sp)
    val BubbleMeta    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 14.sp)
    val Button        = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp)
    val ButtonText    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 16.sp)
    val Tab           = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val TimerChip     = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 12.sp, lineHeight = 12.sp)
    val SystemNote    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val SafetyNumber  = TextStyle(FontFamily.Monospace, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 22.sp, letterSpacing = 0.5.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val SignalTypography = Typography(
    headlineMedium = SignalText.LargeTitle,
    titleLarge     = SignalText.ThreadTitle,
    bodyLarge      = SignalText.MessageBody,
    titleMedium    = SignalText.ConvoName,
    labelSmall     = SignalText.Tab,
)
```

## 3. Signature Components

### Message Bubble (Outgoing + Incoming with same-sender tail)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

@Composable
fun MessageBubble(
    text: String,
    time: String,
    isOutgoing: Boolean,
    isLastInRun: Boolean,
    modifier: Modifier = Modifier,
) {
    val tail = if (isLastInRun) 6.dp else 18.dp
    val shape = RoundedCornerShape(
        topStart = 18.dp, topEnd = 18.dp,
        bottomEnd   = if (isOutgoing) tail else 18.dp,
        bottomStart = if (isOutgoing) 18.dp else tail,
    )
    Row(
        modifier.fillMaxWidth().padding(horizontal = 12.dp, vertical = 1.dp),
        horizontalArrangement = if (isOutgoing) Arrangement.End else Arrangement.Start,
    ) {
        Column(
            Modifier
                .widthIn(max = 280.dp)
                .clip(shape)
                .background(if (isOutgoing) SignalColors.Blue else SignalColors.Incoming)
                .padding(horizontal = 13.dp, vertical = 9.dp),
            horizontalAlignment = Alignment.End,
        ) {
            Text(text, style = SignalText.MessageBody,
                color = if (isOutgoing) Color.White else SignalColors.TextPrimary)
            Row(horizontalArrangement = Arrangement.spacedBy(3.dp)) {
                Text(time, style = SignalText.BubbleMeta,
                    color = if (isOutgoing) SignalColors.OutMeta else SignalColors.TextSecondary)
                if (isOutgoing) {
                    Text("✓✓", style = SignalText.BubbleMeta, color = SignalColors.OutMeta)
                }
            }
        }
    }
}
```

### Slide-Up Send Button (Signature)

```kotlin
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material.icons.filled.Mic
import androidx.compose.material.icons.outlined.PhotoCamera
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.graphicsLayer
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun ComposerBar() {
    var text by remember { mutableStateOf("") }
    val hasText = text.trim().isNotEmpty()
    val haptics = LocalHapticFeedback.current

    val sendOffset by animateDpAsState(
        if (hasText) 0.dp else 4.dp,
        spring(dampingRatio = 0.8f, stiffness = 600f), label = "sendY",
    )
    val sendAlpha by animateFloatAsState(if (hasText) 1f else 0.9f, label = "sendA")

    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val pressScale by animateFloatAsState(if (pressed) 0.90f else 1f, label = "sendScale")

    Row(
        Modifier.fillMaxWidth().padding(horizontal = 12.dp, vertical = 8.dp),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Row(
            Modifier
                .weight(1f)
                .heightIn(min = 36.dp)
                .clip(RoundedCornerShape(18.dp))
                .background(SignalColors.Surface)
                .padding(horizontal = 12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Icon(Icons.Filled.Add, contentDescription = "Attach", tint = SignalColors.TextSecondary)
            BasicTextField(
                value = text, onValueChange = { text = it },
                textStyle = SignalText.MessageBody.copy(color = SignalColors.TextPrimary),
                modifier = Modifier.weight(1f).padding(vertical = 8.dp),
                decorationBox = { inner ->
                    if (text.isEmpty()) Text("Signal message", style = SignalText.MessageBody,
                        color = SignalColors.TextTertiary)
                    inner()
                },
            )
            Icon(Icons.Outlined.PhotoCamera, contentDescription = "Camera", tint = SignalColors.TextSecondary)
        }
        Box(
            Modifier
                .offset(y = sendOffset)
                .graphicsLayer { alpha = sendAlpha }
                .scale(pressScale)
                .size(32.dp)
                .clip(CircleShape)
                .background(if (pressed) SignalColors.BluePressed else SignalColors.Blue)
                .clickable(interaction, indication = null) {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                    text = ""
                },
            contentAlignment = Alignment.Center,
        ) {
            // up-arrow when text exists, mic when empty
            Icon(if (hasText) Icons.Filled.ArrowUpward else Icons.Filled.Mic,
                contentDescription = if (hasText) "Send" else "Voice message",
                tint = Color.White, modifier = Modifier.size(16.dp))
        }
    }
}
```

### Conversation Row

```kotlin
@Composable
fun ConversationRow(
    name: String,
    preview: String,
    time: String,
    unread: Int,
    disappearing: Boolean,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Row(
        Modifier
            .fillMaxWidth()
            .height(72.dp)
            .background(if (pressed) SignalColors.Surface else SignalColors.Canvas)
            .clickable(interaction, indication = null) { }
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(48.dp).clip(CircleShape).background(SignalColors.Surface))
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(3.dp)) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                Text(name, style = SignalText.ConvoName, color = SignalColors.TextPrimary)
                if (disappearing) Icon(Icons.Filled.Timer, contentDescription = null,
                    tint = SignalColors.TextSecondary, modifier = Modifier.size(13.dp))
            }
            Text(preview, style = SignalText.Preview, color = SignalColors.TextSecondary, maxLines = 1)
        }
        Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(time, style = SignalText.Timestamp, color = SignalColors.TextSecondary)
            if (unread > 0) {
                Box(
                    Modifier.defaultMinSize(20.dp, 20.dp).clip(CircleShape)
                        .background(SignalColors.Blue).padding(horizontal = 6.dp),
                    contentAlignment = Alignment.Center,
                ) { Text("$unread", style = SignalText.BubbleMeta, color = Color.White) }
            }
        }
    }
}
```

### Timer Chip + Encryption Note

```kotlin
import androidx.compose.material.icons.filled.Lock
import androidx.compose.material.icons.filled.Timer

@Composable
fun TimerChip(duration: String) {
    Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
        // privacy is quiet — neutral gray, never colored
        Icon(Icons.Filled.Timer, contentDescription = "Disappearing messages",
            tint = SignalColors.TextSecondary, modifier = Modifier.size(13.dp))
        Text(duration, style = SignalText.TimerChip, color = SignalColors.TextSecondary)
    }
}

@Composable
fun EncryptionNote() {
    Row(
        Modifier.fillMaxWidth().padding(vertical = 12.dp),
        horizontalArrangement = Arrangement.Center,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(Icons.Filled.Lock, contentDescription = null, tint = SignalColors.TextSecondary,
            modifier = Modifier.size(12.dp))
        Spacer(Modifier.width(5.dp))
        Text("Messages and calls are end-to-end encrypted.",
            style = SignalText.SystemNote, color = SignalColors.TextSecondary)
    }
}
```

## 4. Privacy-Minimal Stance in Compose

There is intentionally **no** ad slot, no algorithmic feed surface, no streak counter, and no like-count composable in this design. Every screen is a `LazyColumn` of rows, a thread of bubbles, or a grouped settings list. Keep it that way — the restraint is the brand. The only "dynamic" surface is the disappearing-timer ring that drains over a message's lifetime (see §6).

## 5. Bottom Navigation

Material 3 `NavigationBar`. Signal's tab bar is flat; Android has no live blur, so use an opaque canvas surface with a hairline. Active tint is Signal Blue.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*
import androidx.compose.material.icons.outlined.Circle

@Composable
fun SignalBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = SignalColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Chats"    to Icons.Filled.Chat,
            "Calls"    to Icons.Filled.Call,
            "Stories"  to Icons.Outlined.Circle,
            "Settings" to Icons.Filled.Settings,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = SignalText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = SignalColors.Blue,
                    selectedTextColor   = SignalColors.Blue,
                    unselectedIconColor = SignalColors.TextSecondary,
                    unselectedTextColor = SignalColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // Signal has no Material pill
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Send button slide-up | `animateDpAsState` offset 4 → 0 + `animateFloatAsState` alpha 0.9 → 1, `spring(dampingRatio = 0.8f)` |
| Send press | `animateFloatAsState` scale 1 → 0.90, `HapticFeedbackType.TextHandleMove` |
| Outgoing bubble enter | `AnimatedVisibility` + `slideInVertically { it } + fadeIn()`, 250ms |
| Reaction picker | `AnimatedVisibility` + `scaleIn(spring(dampingRatio = 0.7f))` above the bubble |
| Disappearing timer ring | `rememberInfiniteTransition`-free: `animateFloatAsState` 1 → 0 over the message lifetime driving a `Canvas` arc sweep |
| Tab switch | `Crossfade` (no slide) |

```kotlin
// Disappearing timer ring — drains over the message lifetime
@Composable
fun TimerRing(progress: Float) { // 1f full → 0f gone
    androidx.compose.foundation.Canvas(Modifier.size(14.dp)) {
        drawArc(
            color = SignalColors.TextSecondary,
            startAngle = -90f,
            sweepAngle = 360f * progress,
            useCenter = false,
            style = androidx.compose.ui.graphics.drawscope.Stroke(width = 3f),
        )
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For a crisper send tick use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) to approximate iOS's `.light` impact.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Export Signal's exact glyphs as vector drawables for pixel parity if needed.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Send | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Voice (empty composer) | `mic.fill` | `Icons.Filled.Mic` |
| Attach | `plus` | `Icons.Filled.Add` |
| Camera | `camera` | `Icons.Outlined.PhotoCamera` |
| Audio call | `phone` | `Icons.Filled.Call` |
| Video call | `video` | `Icons.Filled.Videocam` |
| Compose | `square.and.pencil` | `Icons.Filled.Edit` |
| Disappearing timer | `timer` | `Icons.Filled.Timer` |
| Sealed sender | `lock.fill` | `Icons.Filled.Lock` |
| Safety shield | `checkmark.shield.fill` | `Icons.Filled.VerifiedUser` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Chats (tab) | `message.fill` | `Icons.Filled.Chat` |
| Calls (tab) | `phone.fill` | `Icons.Filled.Call` |
| Stories (tab) | `circle.dashed` | `Icons.Outlined.Circle` |
| Settings (tab) | `gearshape.fill` | `Icons.Filled.Settings` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; system-bar icon color follows the canvas (dark icons on white, light on `#1B1B1B`). Apply `Scaffold` insets so the composer clears the gesture nav and rises with the IME (`Modifier.imePadding()`).
- **Dark mode**: the dark canvas is `#1B1B1B` — never pure black. Do not enable Material You `dynamicDarkColorScheme()`; the single-blue identity is fixed regardless of wallpaper.
- **Font scaling**: `sp` honors the user's font scale — keep it on names and message bodies. Pin layout-sensitive text (bubble meta, timestamps, tab labels) via a fixed-density wrapper; keep the safety-number grouping aligned.
- **TalkBack**: announce bubble direction before the text ("You said…" / "<Name> said…"), then time and read state. Hide the send button from accessibility when it is a mic (announce "Voice message" only when it actually sends). Expose the timer chip as "Disappearing messages, <duration>".
- **Touch targets**: Material guidance is 48.dp minimum. The 32.dp send circle needs a 48.dp effective hit via padding; conversation rows are a full 72.dp.
- **Contrast**: `#6B6B6B` on `#FFFFFF` passes WCAG AA at 13sp+; outgoing white on `#3A76F0` passes AA. `#CBD9F9` bubble meta on `#3A76F0` is intentionally low-emphasis — bump toward `#DCE6FB` if targeting strict compliance.
- **Keep it minimal for assistive tech too**: no decorative composables that add TalkBack noise — the absence of clutter is part of the accessibility story.
