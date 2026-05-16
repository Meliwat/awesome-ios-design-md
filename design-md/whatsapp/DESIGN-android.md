# WhatsApp (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports WhatsApp's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (WhatsApp's mint-leaf outgoing bubble, single-green verb color, blue double-check read receipts, doodle wallpaper) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a translucent `Surface` instead of `.regularMaterial` blur, `sp`/`dp` instead of `pt`. The app is light-first with a blue-black dark mode; both are wired below.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for contact avatars and media. No color extraction — WhatsApp's palette is fixed brand chrome.

## 1. Color Tokens

```kotlin
// ui/theme/WhatsAppColors.kt
import androidx.compose.ui.graphics.Color

object WhatsAppColors {
    // Brand
    val Green        = Color(0xFF25D366)
    val GreenPressed = Color(0xFF1EBE5D)
    val Teal         = Color(0xFF075E54)
    val MidTeal      = Color(0xFF128C7E)
    val DarkTeal     = Color(0xFF054D44)

    // Chat bubbles
    val OutgoingLight = Color(0xFFD9FDD3) // the iconic mint-leaf
    val OutgoingDark  = Color(0xFF005C4B)
    val IncomingLight = Color(0xFFFFFFFF)
    val IncomingDark  = Color(0xFF1F2C34)
    val ReplyStrip    = Color(0xFF06CF9C)

    // Canvas & surfaces
    val WallpaperLight = Color(0xFFECE5DD) // cream doodle canvas
    val WallpaperDark  = Color(0xFF0B141A)
    val CanvasLight    = Color(0xFFFFFFFF)
    val CanvasDark     = Color(0xFF111B21) // blue-black, NOT pure black
    val Surface1Light  = Color(0xFFF7F8FA)
    val Surface1Dark   = Color(0xFF202C33)
    val Surface2Dark   = Color(0xFF2A3942)
    val DividerLight   = Color(0xFFE9EDEF)
    val DividerDark    = Color(0xFF222D34)
    val NavTintLight   = Color(0xFFF0F2F5) // chat-screen top nav / search pill

    // Text
    val TextPrimary       = Color(0xFF111B21)
    val TextSecondary     = Color(0xFF667781)
    val TextTertiary      = Color(0xFF8696A0)
    val TextPrimaryDark   = Color(0xFFE9EDEF)
    val TextSecondaryDark = Color(0xFF8696A0)

    // Semantic
    val ReadBlue   = Color(0xFF53BDEB) // double blue ticks — most load-bearing color
    val SentGray   = Color(0xFF8696A0)
    val ErrorRed   = Color(0xFFF15C6D)
    val EncBanner  = Color(0xFFFFF5C4) // end-to-end encryption pill (light)
}
```

WhatsApp is light-first with a true dark mode. Provide both schemes; brand greens keep the same hex in either. Active tab tint is brand **green** (unlike iOS Messages), so `primary` carries it.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val WhatsAppLight = lightColorScheme(
    primary        = WhatsAppColors.Green,
    onPrimary      = Color.White,
    background     = WhatsAppColors.CanvasLight,
    onBackground   = WhatsAppColors.TextPrimary,
    surface        = WhatsAppColors.CanvasLight,
    onSurface      = WhatsAppColors.TextPrimary,
    surfaceVariant = WhatsAppColors.Surface1Light,
    outline        = WhatsAppColors.DividerLight,
    error          = WhatsAppColors.ErrorRed,
)

private val WhatsAppDark = darkColorScheme(
    primary        = WhatsAppColors.Green,
    onPrimary      = Color.White,
    background      = WhatsAppColors.CanvasDark,
    onBackground   = WhatsAppColors.TextPrimaryDark,
    surface        = WhatsAppColors.Surface1Dark,
    onSurface      = WhatsAppColors.TextPrimaryDark,
    surfaceVariant = WhatsAppColors.Surface2Dark,
    outline        = WhatsAppColors.DividerDark,
    error          = WhatsAppColors.ErrorRed,
)

@Composable
fun WhatsAppTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (darkTheme) WhatsAppDark else WhatsAppLight,
    typography = WhatsAppTypography,
    content = content,
)
```

## 2. Typography

WhatsApp ships **no** proprietary typeface — it defers entirely to the system font. On Android that is **Roboto** (`FontFamily.Default`), the closest analog to iOS's SF Pro. Do not bundle a custom font; the whole point of WhatsApp's type system is OS deference and full font-scale support.

```kotlin
// ui/theme/WhatsAppType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

// WhatsApp uses the system face — Roboto on Android (SF Pro analog).
private val WASans = FontFamily.Default

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object WhatsAppText {
    val LargeTitle      = TextStyle(WASans, fontWeight = FontWeight.Bold,     fontSize = 34.sp, lineHeight = 38.sp, letterSpacing = 0.37.sp)
    val ScreenTitle     = TextStyle(WASans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.4).sp)
    val ContactName     = TextStyle(WASans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.4).sp)
    val MessagePreview  = TextStyle(WASans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val BubbleBody      = TextStyle(WASans, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.4).sp)
    val GroupSender     = TextStyle(WASans, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = (-0.1).sp)
    val TimestampList   = TextStyle(WASans, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 14.sp)
    val TimestampBubble = TextStyle(WASans, fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 11.sp)
    val SectionHeader   = TextStyle(WASans, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = (-0.1).sp)
    val SystemMessage   = TextStyle(WASans, fontWeight = FontWeight.Medium,   fontSize = 12.sp, lineHeight = 16.sp)
    val TabLabel        = TextStyle(WASans, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button          = TextStyle(WASans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 17.sp, letterSpacing = (-0.4).sp)
    val InputPlaceholder = TextStyle(WASans, fontWeight = FontWeight.Normal,  fontSize = 16.sp, lineHeight = 21.sp, letterSpacing = (-0.3).sp)
    val SettingsRow     = TextStyle(WASans, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.4).sp)
    val SettingsDetail  = TextStyle(WASans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
}

// Map onto Material 3 slots so stock components inherit the ramp
val WhatsAppTypography = Typography(
    headlineLarge = WhatsAppText.LargeTitle,
    titleLarge    = WhatsAppText.ScreenTitle,
    titleMedium   = WhatsAppText.ContactName,
    bodyLarge     = WhatsAppText.BubbleBody,
    bodyMedium    = WhatsAppText.MessagePreview,
    labelSmall    = WhatsAppText.TabLabel,
)
```

## 3. Signature Components

### Send / Mic Morphing Button

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Mic
import androidx.compose.material.icons.filled.Send
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.foundation.layout.Box
import androidx.compose.ui.unit.dp

@Composable
fun WASendButton(
    hasText: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        targetValue = if (pressed) 0.92f else 1f,
        animationSpec = spring(dampingRatio = 0.7f, stiffness = 600f),
        label = "sendScale",
    )
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .size(36.dp)
            .scale(scale)
            .clip(CircleShape)
            .background(if (pressed) WhatsAppColors.GreenPressed else WhatsAppColors.Green)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS soft impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        // No SymbolEffect on Android — crossfade the glyph instead (see §6)
        Icon(
            imageVector = if (hasText) Icons.Filled.Send else Icons.Filled.Mic,
            contentDescription = if (hasText) "Send message" else "Record voice message",
            tint = Color.White,
            modifier = Modifier.size(18.dp),
        )
    }
}
```

### Chat Bubble (Outgoing — asymmetric tail + tick row)

Compose has no `UnevenRoundedRectangle`; use `RoundedCornerShape` with the **bottom-end** corner zeroed for the outgoing tail nub.

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.ui.text.style.TextAlign

enum class ReadState { Sent, Delivered, Read }

@Composable
fun WAOutgoingBubble(
    text: String,
    timestamp: String,
    readState: ReadState,
    modifier: Modifier = Modifier,
) {
    Row(modifier.fillMaxWidth().padding(end = 8.dp)) {
        Spacer(Modifier.weight(0.2f)) // 80% max bubble width
        Column(
            modifier = Modifier
                .weight(0.8f, fill = false)
                .clip(
                    RoundedCornerShape(
                        topStart = 12.dp, topEnd = 12.dp,
                        bottomStart = 12.dp, bottomEnd = 0.dp, // tail nub
                    )
                )
                .background(WhatsAppColors.OutgoingLight)
                .padding(horizontal = 12.dp, vertical = 8.dp),
            horizontalAlignment = Alignment.End,
        ) {
            Text(text, style = WhatsAppText.BubbleBody, color = WhatsAppColors.TextPrimary)
            Spacer(Modifier.height(2.dp))
            Row(verticalAlignment = Alignment.CenterVertically) {
                Text(
                    timestamp,
                    style = WhatsAppText.TimestampBubble,
                    color = WhatsAppColors.TextSecondary,
                )
                Spacer(Modifier.width(3.dp))
                TickRow(readState)
            }
        }
    }
}

@Composable
private fun TickRow(state: ReadState) {
    val tint = if (state == ReadState.Read) WhatsAppColors.ReadBlue else WhatsAppColors.SentGray
    val count = if (state == ReadState.Sent) 1 else 2
    // Overlap the two checks by -4.dp so they read as one glyph
    Row(horizontalArrangement = Arrangement.spacedBy((-4).dp)) {
        repeat(count) {
            Icon(
                imageVector = Icons.Filled.Done,
                contentDescription = null,
                tint = tint,
                modifier = Modifier.size(13.dp),
            )
        }
    }
}
```

### Chat List Row (with status ring)

```kotlin
import coil.compose.AsyncImage
import androidx.compose.foundation.border
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun WAChatListRow(
    avatarUrl: String,
    name: String,
    preview: String,
    timestamp: String,
    unreadCount: Int,
    hasStatusRing: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()

    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(72.dp)
            .background(if (pressed) WhatsAppColors.Surface1Light else Color.Transparent)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(contentAlignment = Alignment.Center) {
            if (hasStatusRing) {
                Box(
                    Modifier
                        .size(54.dp)
                        .border(2.5.dp, WhatsAppColors.Green, CircleShape)
                )
            }
            AsyncImage(
                model = avatarUrl,
                contentDescription = null,
                modifier = Modifier.size(48.dp).clip(CircleShape),
            )
        }
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(3.dp)) {
            Text(name, style = WhatsAppText.ContactName, color = WhatsAppColors.TextPrimary, maxLines = 1, overflow = TextOverflow.Ellipsis)
            Text(preview, style = WhatsAppText.MessagePreview, color = WhatsAppColors.TextSecondary, maxLines = 1, overflow = TextOverflow.Ellipsis)
        }
        Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(
                timestamp,
                style = WhatsAppText.TimestampList,
                color = if (unreadCount > 0) WhatsAppColors.Green else WhatsAppColors.TextSecondary,
            )
            if (unreadCount > 0) {
                Box(
                    Modifier
                        .clip(CircleShape)
                        .background(WhatsAppColors.Green)
                        .padding(horizontal = 7.dp, vertical = 2.dp),
                ) {
                    Text("$unreadCount", style = WhatsAppText.TimestampList, color = Color.White)
                }
            }
        }
    }
}
```

## 4. Voice Waveform Bubble + Blue Double-Check Read Receipts

WhatsApp's two defining interactive systems. The **voice-message waveform** fills its bars green left-to-right synced to playback, with a soft haptic tick as playback crosses each amplitude peak. The **read receipt** crossfades the overlapping checkmarks gray → blue when the recipient opens the message.

```kotlin
import androidx.compose.animation.core.LinearEasing
import androidx.compose.animation.core.tween
import androidx.compose.material.icons.filled.Pause
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.runtime.snapshotFlow
import kotlinx.coroutines.delay
import kotlin.math.max

@Composable
fun WAVoiceWaveformBubble(
    duration: String,
    amplitudes: List<Float>, // normalized 0f..1f, ~48 samples
    modifier: Modifier = Modifier,
) {
    var isPlaying by remember { mutableStateOf(false) }
    var progress by remember { mutableFloatStateOf(0f) } // 0f..1f played fraction
    val haptics = LocalHapticFeedback.current

    // Advance playback while playing; soft tick on amplitude peaks
    LaunchedEffect(isPlaying) {
        if (!isPlaying) return@LaunchedEffect
        while (isPlaying && progress < 1f) {
            delay(80)
            val prevBar = (progress * amplitudes.size).toInt()
            progress = (progress + 0.02f).coerceAtMost(1f)
            val newBar = (progress * amplitudes.size).toInt()
            if (newBar != prevBar && newBar < amplitudes.size && amplitudes[newBar] > 0.85f) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // subtle peak tick
            }
        }
        if (progress >= 1f) isPlaying = false
    }

    Row(
        modifier = modifier
            .clip(
                RoundedCornerShape(
                    topStart = 12.dp, topEnd = 12.dp,
                    bottomStart = 12.dp, bottomEnd = 0.dp,
                )
            )
            .background(WhatsAppColors.OutgoingLight)
            .padding(horizontal = 12.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box(
            Modifier
                .size(36.dp)
                .clip(CircleShape)
                .background(WhatsAppColors.Green)
                .clickable { isPlaying = !isPlaying },
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                imageVector = if (isPlaying) Icons.Filled.Pause else Icons.Filled.PlayArrow,
                contentDescription = if (isPlaying) "Pause" else "Play voice message",
                tint = Color.White,
                modifier = Modifier.size(16.dp),
            )
        }
        Row(
            modifier = Modifier.weight(1f).height(24.dp),
            horizontalArrangement = Arrangement.spacedBy(2.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            amplitudes.forEachIndexed { i, amp ->
                val played = i.toFloat() / amplitudes.size <= progress
                Box(
                    Modifier
                        .width(2.dp)
                        .height(max(4f, amp * 20f).dp)
                        .clip(CircleShape)
                        .background(if (played) WhatsAppColors.Green else WhatsAppColors.TextTertiary)
                )
            }
        }
        Text(duration, style = WhatsAppText.TimestampBubble, color = WhatsAppColors.TextTertiary)
    }
}

/** Read receipt that crossfades gray → blue when [readState] flips to Read. */
@Composable
fun AnimatedTickRow(readState: ReadState) {
    val targetTint = if (readState == ReadState.Read) WhatsAppColors.ReadBlue else WhatsAppColors.SentGray
    val tint by androidx.compose.animation.animateColorAsState(
        targetValue = targetTint,
        animationSpec = tween(200, easing = LinearEasing),
        label = "tickColor",
    )
    val count = if (readState == ReadState.Sent) 1 else 2
    Row(horizontalArrangement = Arrangement.spacedBy((-4).dp)) {
        repeat(count) {
            Icon(Icons.Filled.Done, contentDescription = null, tint = tint, modifier = Modifier.size(13.dp))
        }
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. WhatsApp's iOS tab bar is `.regularMaterial` blur over canvas; Android has no first-class live blur, so use a 92%-opaque canvas surface. **Active tint is brand green** — unlike iOS Messages, WhatsApp uses `#25D366` as the selected tab tint. The "new chat" FAB is *not* in the tab bar — it lives as a nav-bar trailing action on the Chats screen.

```kotlin
import androidx.compose.material.icons.filled.Chat
import androidx.compose.material.icons.filled.Groups
import androidx.compose.material.icons.filled.Phone
import androidx.compose.material.icons.filled.Settings
import androidx.compose.material.icons.outlined.DonutLarge
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun WABottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = WhatsAppColors.CanvasLight.copy(alpha = 0.92f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Updates"     to Icons.Outlined.DonutLarge,
            "Calls"       to Icons.Filled.Phone,
            "Communities" to Icons.Filled.Groups,
            "Chats"       to Icons.Filled.Chat,
            "Settings"    to Icons.Filled.Settings,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = WhatsAppText.TabLabel) },
                alwaysShowLabel = true,
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = WhatsAppColors.Green, // brand-green active
                    selectedTextColor   = WhatsAppColors.Green,
                    unselectedIconColor = WhatsAppColors.TextTertiary,
                    unselectedTextColor = WhatsAppColors.TextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill — WhatsApp has none
                ),
            )
        }
    }
}
```

The **compose bar** sits in the `Scaffold` `bottomBar` slot on the chat screen (not the tab bar): a `Surface(color = Surface1Light)` row holding a pill `TextField` plus the `WASendButton`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Send button press | `animateFloatAsState` 1 → 0.92 with `spring(dampingRatio = 0.7f)`, `HapticFeedbackType.LongPress` |
| Send/mic glyph morph | `Crossfade(targetState = hasText)` (no iOS `symbolEffect`) |
| Read receipt → blue | `animateColorAsState` gray → `#53BDEB` over 200ms `tween` |
| Voice playback fill | bars recolor as `progress` advances; `HapticFeedbackType.TextHandleMove` on amplitude peaks |
| Mic record hold | outer ring `scale = 1f + amplitude * 0.2f` driven by decibel state |
| Swipe-to-reply | `Modifier.pointerInput` horizontal drag; `HapticFeedbackType.TextHandleMove` at threshold |
| Reaction tap | `Animatable` keyframes 1 → 1.2 → 1; `HapticFeedbackType.LongPress` |

```kotlin
// Send/mic glyph morph — Android's analog to iOS .contentTransition(.symbolEffect(.replace))
import androidx.compose.animation.Crossfade

@Composable
fun MorphingSendGlyph(hasText: Boolean) {
    Crossfade(targetState = hasText, label = "sendMorph") { showSend ->
        Icon(
            imageVector = if (showSend) Icons.Filled.Send else Icons.Filled.Mic,
            contentDescription = if (showSend) "Send message" else "Record voice message",
            tint = Color.White,
            modifier = Modifier.size(18.dp),
        )
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For the soft per-peak tick during voice playback, `HapticFeedbackType.TextHandleMove` is the lightest stock effect; for richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(8, 40)` to approximate iOS's `.soft` impact.

## 7. Icons

WhatsApp uses near-stock iconography; the closest first-party set is `androidx.compose.material:material-icons-extended`. The Updates "circle.dashed" glyph has no exact Material twin — ship it (and the WhatsApp logomark) as a vector drawable loaded via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Send | `paperplane.fill` | `Icons.Filled.Send` |
| Microphone | `mic.fill` | `Icons.Filled.Mic` |
| Attach | `paperclip` | `Icons.Filled.AttachFile` |
| Camera | `camera.fill` | `Icons.Filled.PhotoCamera` |
| Emoji | `face.smiling` | `Icons.Filled.EmojiEmotions` |
| Read ticks | `checkmark` | `Icons.Filled.Done` |
| Phone (answer) | `phone.fill` | `Icons.Filled.Call` |
| Phone (decline) | `phone.down.fill` | `Icons.Filled.CallEnd` |
| Video | `video.fill` | `Icons.Filled.Videocam` |
| Lock (E2E) | `lock.fill` | `Icons.Filled.Lock` |
| Chats tab | `message.fill` | `Icons.Filled.Chat` |
| Calls tab | `phone.fill` | `Icons.Filled.Phone` |
| Communities tab | `person.3.fill` | `Icons.Filled.Groups` |
| Updates tab | `circle.dashed` | `Icons.Outlined.DonutLarge` (or vector drawable) |
| Settings tab | `gearshape.fill` | `Icons.Filled.Settings` |
| New chat | `square.and.pencil` | `Icons.Filled.Edit` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Ellipsis | `ellipsis` | `Icons.Filled.MoreVert` |
| Play / Pause (voice) | `play.fill` / `pause.fill` | `Icons.Filled.PlayArrow` / `Icons.Filled.Pause` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The chat wallpaper should full-bleed behind the system bars; apply `Scaffold` insets so the compose bar clears the gesture nav and the bottom tab bar clears the home indicator.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on bubble body, contact names, and previews so WhatsApp's Dynamic-Type-first promise carries over. Pin inline timestamps (`11sp` in-bubble, `12sp` list) and the `10sp` tab labels by deriving them from a fixed-`fontScale` `LocalDensity` so the inline time/tick row never breaks layout.
- **TalkBack**: label the send button dynamically (`"Send message"` vs `"Record voice message"`); merge bubble text + timestamp + read-state into one node with `Modifier.semantics(mergeDescendants = true)` and announce read state as content (`"Read"` / `"Delivered"`). Do **not** hide the end-to-end encryption banner — expose it as static text.
- **Touch targets**: Material guidance is 48.dp minimum. The 36.dp send/mic and play circles need a 48.dp hit area via surrounding padding even though the glyph is small; chat-list rows at 72.dp are well clear.
- **RTL**: the asymmetric bubble tail must flip for Arabic/Hebrew — use `RoundedCornerShape` with `topStart/bottomEnd` (logical corners) rather than absolute left/right so Compose mirrors it automatically. Test with a forced RTL locale.
- **Contrast**: `#D9FDD3` outgoing bubble + `#111B21` text far exceeds WCAG AA. Validate the dark-mode `#005C4B` bubble + `#E9EDEF` text (passes AA) and the `#8696A0` secondary text on `#FFFFFF` — the latter is borderline at the 11–12sp timestamp size; bump toward `#667781` if your build targets strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — WhatsApp's brand requires the fixed green verb color and the mint-leaf bubble regardless of wallpaper. Keep the hand-built light/dark schemes above.
