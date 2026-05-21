# Discord (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Discord's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Discord's three-gray surface system, single Blurple accent, the rounded-square server rail, role-colored usernames, presence dots) while making everything idiomatic Android — a gesture-driven drawer instead of UIKit swipe panes, `FontFamily(Font(R.font.…))` instead of `Font.custom`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for server icons and avatars.

## 1. Color Tokens

```kotlin
// ui/theme/DiscordColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object DiscordColors {
    // Brand
    val Blurple        = Color(0xFF5865F2)
    val BlurplePressed = Color(0xFF4752C4)
    val BlurpleLegacy  = Color(0xFF7289DA)

    // Three-gray surface system (Dark — the default; the adjacency IS the brand)
    val ServerRail   = Color(0xFF1E1F22) // leftmost, darkest
    val ChannelList  = Color(0xFF2B2D31) // middle pane / modals
    val ChatCanvas   = Color(0xFF313338) // the "document"
    val Surface2     = Color(0xFF383A40) // compose bar — one step lighter than chat
    val Divider      = Color(0xFF3F4147)
    val RowHover     = Color(0xFF2E3035)
    val ActiveChannelBg = Color(0xFF404249)

    // Three-gray surface system (Light — rarely used, ~80% stay on dark)
    val LightCanvas   = Color(0xFFFFFFFF)
    val LightSurface1 = Color(0xFFF2F3F5)
    val LightSurface2 = Color(0xFFEBEDEF)
    val LightDivider  = Color(0xFFE3E5E8)

    // Text
    val TextPrimary   = Color(0xFFF2F3F5)
    val TextSecondary = Color(0xFFB5BAC1)
    val TextMuted     = Color(0xFF949BA4)
    val TextLink      = Color(0xFF00A8FC) // distinct blue from Blurple
    val TextDisabled  = Color(0xFF5D6069)

    // Status (presence dots)
    val OnlineGreen     = Color(0xFF23A55A)
    val IdleYellow      = Color(0xFFF0B232)
    val DNDRed          = Color(0xFFF23F43)
    val OfflineGray     = Color(0xFF80848E)
    val StreamingPurple = Color(0xFF593695)

    // Destructive / brand variants
    val DestructiveRed = Color(0xFFDA373C)
    val BoostPink      = Color(0xFFEB459E)

    // Common default role colors (server owners set arbitrary hex)
    val RoleAdmin   = Color(0xFFED4245)
    val RoleMod     = Color(0xFF57F287)
    val RoleVIPGold = Color(0xFFFEE75C)
    val RoleBotAqua = Color(0xFF23C1D2)
}

// Nitro / Boost gradient
val NitroGradient = Brush.linearGradient(listOf(DiscordColors.Blurple, DiscordColors.BoostPink))
```

Discord is dark-first; wire a `darkColorScheme` and keep the three grays unexaggerated. A light scheme exists but is a straightforward inversion.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.runtime.Composable

private val DiscordScheme = darkColorScheme(
    primary        = DiscordColors.Blurple,
    onPrimary      = DiscordColors.TextPrimary,
    background     = DiscordColors.ChatCanvas,
    onBackground   = DiscordColors.TextPrimary,
    surface        = DiscordColors.ChannelList,
    onSurface      = DiscordColors.TextPrimary,
    surfaceVariant = DiscordColors.Surface2,
    outline        = DiscordColors.Divider,
    error          = DiscordColors.DNDRed,
)

@Composable
fun DiscordTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = DiscordScheme, typography = DiscordTypography, content = content)
```

## 2. Typography

`gg sans` (proprietary, replaced Whitney in 2023) and `gg mono` are proprietary. Drop the TTFs in `res/font/` (lowercase, snake_case). Fall back to the system sans (Roboto) and system monospace — closest free pairing. Discord's working range is compact: 14–20sp.

```kotlin
// ui/theme/DiscordType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val GgSans = FontFamily(
    Font(R.font.gg_sans_regular,  FontWeight.Normal),
    Font(R.font.gg_sans_medium,   FontWeight.Medium),
    Font(R.font.gg_sans_semibold, FontWeight.SemiBold),
    Font(R.font.gg_sans_bold,     FontWeight.Bold),
)
val GgMono = FontFamily(Font(R.font.gg_mono_regular, FontWeight.Normal))

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object DiscordText {
    val ScreenTitle     = TextStyle(GgSans, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val ChannelActive   = TextStyle(GgSans, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val ChannelInactive = TextStyle(GgSans, fontWeight = FontWeight.Medium,   fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val Username        = TextStyle(GgSans, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val MessageBody     = TextStyle(GgSans, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val Timestamp       = TextStyle(GgSans, fontWeight = FontWeight.Medium,   fontSize = 12.sp, lineHeight = 14.sp)
    val ReplyContext    = TextStyle(GgSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp, letterSpacing = (-0.1).sp)
    val SystemMessage   = TextStyle(GgSans, fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 18.sp)
    val SectionLabel    = TextStyle(GgSans, fontWeight = FontWeight.Bold,     fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.5.sp)
    val MemberName      = TextStyle(GgSans, fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 17.sp, letterSpacing = (-0.1).sp)
    val Button          = TextStyle(GgSans, fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 14.sp)
    val Tab             = TextStyle(GgSans, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Code            = TextStyle(GgMono, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val Blockquote      = TextStyle(GgSans, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
}

val DiscordTypography = Typography(
    headlineSmall = DiscordText.ScreenTitle,
    titleMedium   = DiscordText.Username,
    bodyMedium    = DiscordText.MessageBody,
    labelSmall    = DiscordText.Tab,
)
```

## 3. Signature Components

### Server Rail (the defining Discord component)

48dp rounded **squares** — corner radius morphs 16dp → 12dp on active (the "squircle-er" effect) with a 4dp × 40dp white leading indicator bar.

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.verticalScroll
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Explore
import androidx.compose.material.icons.filled.Home
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

data class Server(
    val id: String, val name: String, val imageUrl: String?,
    val initials: String, val unreadCount: Int, val mentionCount: Int,
)

@Composable
fun DCServerRail(servers: List<Server>, activeId: String?, onSelect: (String) -> Unit, modifier: Modifier = Modifier) {
    Column(
        modifier
            .width(72.dp)
            .fillMaxHeight()
            .background(DiscordColors.ServerRail)
            .verticalScroll(rememberScrollState())
            .padding(vertical = 8.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Box(Modifier.size(48.dp).clip(RoundedCornerShape(16.dp)).background(NitroGradient), contentAlignment = Alignment.Center) {
            Icon(Icons.Filled.Home, "Home", tint = Color.White, modifier = Modifier.size(22.dp))
        }
        Box(Modifier.width(32.dp).height(2.dp).background(DiscordColors.Divider))
        servers.forEach { s -> DCServerIcon(s, s.id == activeId) { onSelect(s.id) } }
        Box(Modifier.size(48.dp).clip(CircleShape).background(DiscordColors.ChannelList), contentAlignment = Alignment.Center) {
            Icon(Icons.Filled.Add, "Add a server", tint = DiscordColors.OnlineGreen, modifier = Modifier.size(22.dp))
        }
        Box(Modifier.size(48.dp).clip(CircleShape).background(DiscordColors.ChannelList), contentAlignment = Alignment.Center) {
            Icon(Icons.Filled.Explore, "Explore servers", tint = DiscordColors.OnlineGreen, modifier = Modifier.size(22.dp))
        }
    }
}

@Composable
fun DCServerIcon(server: Server, isActive: Boolean, onClick: () -> Unit) {
    val corner by animateDpAsState(if (isActive) 12.dp else 16.dp, spring(dampingRatio = 0.7f, stiffness = 380f), label = "corner")
    val barHeight by animateDpAsState(
        if (isActive) 40.dp else if (server.unreadCount > 0) 8.dp else 0.dp,
        spring(dampingRatio = 0.7f, stiffness = 520f), label = "bar",
    )
    Row(verticalAlignment = Alignment.CenterVertically) {
        Box(Modifier.width(4.dp).height(barHeight).clip(RoundedCornerShape(2.dp)).background(DiscordColors.TextPrimary))
        Spacer(Modifier.width(8.dp))
        Box(contentAlignment = Alignment.BottomEnd) {
            Box(
                Modifier.size(48.dp).clip(RoundedCornerShape(corner)).background(DiscordColors.Blurple).clickable(onClick = onClick),
                contentAlignment = Alignment.Center,
            ) {
                if (server.imageUrl != null) {
                    AsyncImage(server.imageUrl, server.name, Modifier.matchParentSize())
                } else {
                    Text(server.initials, style = DiscordText.Button, color = Color.White)
                }
            }
            if (server.mentionCount > 0) {
                Box(
                    Modifier
                        .offset(x = 6.dp, y = 6.dp)
                        .defaultMinSize(18.dp, 18.dp)
                        .clip(CircleShape)
                        .background(DiscordColors.DNDRed)
                        .padding(horizontal = 5.dp),
                    contentAlignment = Alignment.Center,
                ) {
                    Text("${minOf(server.mentionCount, 99)}", style = DiscordText.Timestamp, color = Color.White)
                }
            }
        }
        Spacer(Modifier.width(12.dp))
    }
}
```

### Message Row (role color + presence dot)

```kotlin
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.foundation.layout.Box

enum class Presence { Online, Idle, DND, Offline, Streaming }

@Composable
fun DCMessageRow(
    avatarUrl: String,
    username: String,
    roleColor: Color,            // user's highest-priority role; fallback TextPrimary
    timestamp: String,
    message: String,
    presence: Presence,
    isGrouped: Boolean,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp, vertical = if (isGrouped) 2.dp else 8.dp),
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        if (isGrouped) {
            Spacer(Modifier.width(40.dp)) // align to the 52dp avatar gutter
        } else {
            Box(contentAlignment = Alignment.BottomEnd) {
                AsyncImage(avatarUrl, null, Modifier.size(40.dp).clip(CircleShape))
                val dot = when (presence) {
                    Presence.Online -> DiscordColors.OnlineGreen
                    Presence.Idle -> DiscordColors.IdleYellow
                    Presence.DND -> DiscordColors.DNDRed
                    Presence.Offline -> DiscordColors.OfflineGray
                    Presence.Streaming -> DiscordColors.StreamingPurple
                }
                Box(
                    Modifier
                        .size(12.dp)
                        .clip(CircleShape)
                        .background(DiscordColors.ChatCanvas) // 2dp canvas-colored ring punches through
                        .padding(2.dp)
                        .clip(CircleShape)
                        .background(dot),
                )
            }
        }
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            if (!isGrouped) {
                Row(horizontalArrangement = Arrangement.spacedBy(6.dp), verticalAlignment = Alignment.CenterVertically) {
                    Text(username, style = DiscordText.Username, color = roleColor)
                    Text(timestamp, style = DiscordText.Timestamp, color = DiscordColors.TextMuted)
                }
            }
            Text(message, style = DiscordText.MessageBody, color = DiscordColors.TextPrimary, overflow = TextOverflow.Clip)
        }
    }
}
```

### Compose Bar

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.AddCircle
import androidx.compose.material.icons.filled.CardGiftcard
import androidx.compose.material.icons.filled.EmojiEmotions
import androidx.compose.material.icons.filled.Gif
import androidx.compose.material.icons.filled.Send
import androidx.compose.ui.focus.onFocusChanged
import androidx.compose.ui.graphics.SolidColor
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun DCComposeBar(channelName: String, modifier: Modifier = Modifier) {
    var text by remember { mutableStateOf("") }
    var focused by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current

    Row(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 12.dp, vertical = 8.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(DiscordColors.Surface2) // intentionally one step lighter than chat canvas
            .border(2.dp, if (focused) DiscordColors.Blurple else Color.Transparent, RoundedCornerShape(8.dp))
            .padding(horizontal = 12.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(Icons.Filled.AddCircle, "Attach", tint = DiscordColors.TextSecondary, modifier = Modifier.size(22.dp))
        BasicTextField(
            value = text,
            onValueChange = { text = it },
            textStyle = DiscordText.MessageBody.copy(color = DiscordColors.TextPrimary),
            cursorBrush = SolidColor(DiscordColors.Blurple),
            maxLines = 6,
            modifier = Modifier.weight(1f).onFocusChanged { focused = it.isFocused },
            decorationBox = { inner ->
                if (text.isEmpty()) Text("Message $channelName", style = DiscordText.MessageBody, color = DiscordColors.TextMuted)
                inner()
            },
        )
        if (text.isEmpty()) {
            Icon(Icons.Filled.CardGiftcard, "Gift", tint = DiscordColors.TextSecondary, modifier = Modifier.size(20.dp))
            Icon(Icons.Filled.Gif, "GIF", tint = DiscordColors.TextSecondary, modifier = Modifier.size(20.dp))
            Icon(Icons.Filled.EmojiEmotions, "Emoji", tint = DiscordColors.TextSecondary, modifier = Modifier.size(20.dp))
        } else {
            Box(
                Modifier.size(32.dp).clip(CircleShape).background(DiscordColors.Blurple).clickable {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS soft impact
                    text = ""
                },
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Filled.Send, "Send", tint = Color.White, modifier = Modifier.size(18.dp)) }
        }
    }
}
```

### Channel List Row

```kotlin
import androidx.compose.material.icons.filled.Campaign
import androidx.compose.material.icons.filled.Tag
import androidx.compose.material.icons.filled.VolumeUp

enum class ChannelType { Text, Voice, Announcement }

@Composable
fun DCChannelRow(name: String, type: ChannelType, isActive: Boolean, unread: Boolean, mentionCount: Int, modifier: Modifier = Modifier) {
    val icon = when (type) {
        ChannelType.Text -> Icons.Filled.Tag
        ChannelType.Voice -> Icons.Filled.VolumeUp
        ChannelType.Announcement -> Icons.Filled.Campaign
    }
    val textColor = if (isActive || unread || mentionCount > 0) DiscordColors.TextPrimary else DiscordColors.TextMuted
    Box(modifier.fillMaxWidth().height(36.dp)) {
        if (isActive) Box(Modifier.width(3.dp).fillMaxHeight().background(DiscordColors.Blurple))
        Row(
            Modifier
                .fillMaxSize()
                .padding(horizontal = 8.dp)
                .clip(RoundedCornerShape(4.dp))
                .background(if (isActive) DiscordColors.ActiveChannelBg else Color.Transparent)
                .padding(horizontal = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Icon(icon, null, tint = DiscordColors.TextMuted, modifier = Modifier.size(16.dp))
            Text(name, style = if (isActive) DiscordText.ChannelActive else DiscordText.ChannelInactive, color = textColor)
            Spacer(Modifier.weight(1f))
            if (mentionCount > 0) {
                Box(
                    Modifier.defaultMinSize(18.dp, 16.dp).clip(RoundedCornerShape(8.dp)).background(DiscordColors.DNDRed).padding(horizontal = 6.dp),
                    contentAlignment = Alignment.Center,
                ) { Text("$mentionCount", style = DiscordText.Timestamp, color = Color.White) }
            }
        }
    }
}
```

### Voice Channel Speaking Ring

```kotlin
@Composable
fun DCSpeakingRing(isActiveSpeaker: Boolean, modifier: Modifier = Modifier, avatar: @Composable () -> Unit) {
    val pulse by if (isActiveSpeaker) {
        rememberInfiniteTransition(label = "speak").animateFloat(
            1f, 1.12f, infiniteRepeatable(tween(300, easing = FastOutSlowInEasing), RepeatMode.Reverse), label = "ring",
        )
    } else remember { mutableStateOf(1f) }
    Box(modifier, contentAlignment = Alignment.Center) {
        avatar()
        if (isActiveSpeaker) {
            Box(
                Modifier
                    .matchParentSize()
                    .scale(pulse)
                    .clip(CircleShape)
                    .border(2.5.dp, DiscordColors.OnlineGreen, CircleShape),
            )
        }
    }
}
```

### Emoji Reaction Chip

```kotlin
@Composable
fun DCReactionChip(emoji: String, count: Int, didReact: Boolean, onTap: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    Row(
        modifier
            .clip(RoundedCornerShape(8.dp))
            .background(if (didReact) DiscordColors.Blurple.copy(alpha = 0.3f) else DiscordColors.ChannelList)
            .border(1.dp, if (didReact) DiscordColors.Blurple else DiscordColors.Divider, RoundedCornerShape(8.dp))
            .clickable { haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove); onTap() }
            .padding(horizontal = 8.dp, vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Text(emoji, style = DiscordText.MessageBody)
        Text("$count", style = DiscordText.Timestamp.copy(fontSize = 13.sp), color = DiscordColors.TextPrimary)
    }
}
```

## 4. Three-Pane Swipe Navigation (the signature dynamic)

Discord has no color extraction — its distinctive dynamic system is the **three-pane horizontal-swipe architecture**: on mobile the server rail + channel list is a left drawer; swipe right to open, swipe left to close. Build it with a gesture-driven offset rather than `ModalNavigationDrawer` so the rail + channel list animate together as a unit.

```kotlin
import androidx.compose.foundation.gestures.detectHorizontalDragGestures
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.platform.LocalDensity
import androidx.compose.ui.unit.IntOffset
import kotlin.math.roundToInt

@Composable
fun DCMobileShell(
    servers: List<Server>,
    chat: @Composable () -> Unit,
    channelList: @Composable () -> Unit,
) {
    BoxWithConstraints(Modifier.fillMaxSize()) {
        val density = LocalDensity.current
        val drawerWidthPx = with(density) { (maxWidth * 0.85f).toPx() }
        var open by remember { mutableStateOf(true) }
        val offsetPx by animateFloatAsState(
            targetValue = if (open) 0f else -drawerWidthPx,
            animationSpec = spring(dampingRatio = 0.85f, stiffness = 380f),
            label = "drawer",
        )
        // Chat canvas — pushed right when drawer is open
        Box(
            Modifier
                .fillMaxSize()
                .offset { IntOffset((drawerWidthPx + offsetPx).roundToInt(), 0) }
                .background(DiscordColors.ChatCanvas)
                .pointerInput(Unit) {
                    detectHorizontalDragGestures(onDragEnd = { /* settle handled below */ }) { _, drag ->
                        if (drag > 8) open = true else if (drag < -8) open = false
                    }
                },
        ) { chat() }
        // Rail + channel list, animating as one unit
        Row(
            Modifier
                .width(maxWidth * 0.85f)
                .fillMaxHeight()
                .offset { IntOffset(offsetPx.roundToInt(), 0) },
        ) {
            DCServerRail(servers, activeId = servers.firstOrNull()?.id, onSelect = {})
            Box(Modifier.weight(1f).fillMaxHeight().background(DiscordColors.ChannelList)) { channelList() }
        }
    }
}
```

## 5. Navigation

Discord's mobile root is a **4-tab bottom bar** (Servers / Messages / Notifications / You) — *not* a tab bar for channels (channels live in the swipe drawer above). The bar sits on `ServerRail` gray. Active tint is white (Blurple is structural, not for chrome). Discord is aggressively flat — Android has no live blur, so use an opaque rail-colored surface, no elevation.

```kotlin
import androidx.compose.material.icons.filled.GridView
import androidx.compose.material.icons.filled.Notifications
import androidx.compose.material.icons.filled.Forum
import androidx.compose.material.icons.filled.AccountCircle
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun DCBottomBar(selected: Int, mentionCount: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = DiscordColors.ServerRail, tonalElevation = 0.dp) {
        val items = listOf(
            "Servers" to Icons.Filled.GridView,
            "Messages" to Icons.Filled.Forum,
            "Notifications" to Icons.Filled.Notifications,
            "You" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    Box(contentAlignment = Alignment.TopEnd) {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp))
                        if (label == "Notifications" && mentionCount > 0) {
                            Box(
                                Modifier.offset(x = 8.dp, y = (-4).dp).defaultMinSize(16.dp, 16.dp)
                                    .clip(CircleShape).background(DiscordColors.DNDRed).padding(horizontal = 4.dp),
                                contentAlignment = Alignment.Center,
                            ) { Text("$mentionCount", style = DiscordText.Tab, color = Color.White) }
                        }
                    }
                },
                label = { Text(label, style = DiscordText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = DiscordColors.TextPrimary,    // white, not Blurple
                    selectedTextColor = DiscordColors.TextPrimary,
                    unselectedIconColor = DiscordColors.TextMuted,
                    unselectedTextColor = DiscordColors.TextMuted,
                    indicatorColor = Color.Transparent, // Discord has no Material pill
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Server icon active | `animateDpAsState` corner 16 → 12 with `spring(dampingRatio = 0.7f)` (squircle morph) |
| Active indicator bar | `animateDpAsState` height 0/8 → 40 with `spring` |
| Speaking ring pulse | `rememberInfiniteTransition` `scale` 1.0 → 1.12 over 300ms `RepeatMode.Reverse` |
| Message arrival | `AnimatedVisibility` + `slideInVertically { it / 6 }` + `fadeIn` (≈150ms) |
| Reaction add | chip `scale` 1.0 → 1.2 → 1.0; `HapticFeedbackType.TextHandleMove` (soft) |
| Drawer open/close | gesture-driven `offset { IntOffset(...) }` + settle `spring(dampingRatio = 0.85f)` |
| Typing indicator | three `Circle`s with staggered `alpha` via `rememberInfiniteTransition` (300ms cycle each) |
| Spoiler reveal | `Crossfade` blur → clear over 200ms |

```kotlin
// Server-icon squircle morph (already in DCServerIcon §3)
val corner by animateDpAsState(if (isActive) 12.dp else 16.dp, spring(dampingRatio = 0.7f, stiffness = 380f), label = "corner")
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's soft `.impact` on send/react.

## 7. Icons

Discord ships many proprietary glyphs (Discord logo, Nitro star, Wumpus, channel-type icons). Use `androidx.compose.material:material-icons-extended` for the closest semantic match; bundle brand glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Text channel | `number` | `Icons.Filled.Tag` |
| Voice channel | `speaker.wave.2.fill` | `Icons.Filled.VolumeUp` |
| Announcement channel | `megaphone.fill` | `Icons.Filled.Campaign` |
| Home / Discover | `house.fill` | `Icons.Filled.Home` (or custom logo) |
| Add server | `plus` | `Icons.Filled.Add` (green) |
| Explore servers | `safari.fill` | `Icons.Filled.Explore` |
| Compose attach | `plus.circle.fill` | `Icons.Filled.AddCircle` |
| Gift | `gift` | `Icons.Filled.CardGiftcard` |
| GIF picker | `photo.stack` | `Icons.Filled.Gif` |
| Emoji | `face.smiling` | `Icons.Filled.EmojiEmotions` |
| Send | `paperplane.fill` | `Icons.Filled.Send` |
| Pin | `pin.fill` | `Icons.Filled.PushPin` |
| Member list | `person.2.fill` | `Icons.Filled.People` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Servers tab | (custom grid) | `Icons.Filled.GridView` |
| Messages tab | `bubble.left.and.bubble.right.fill` | `Icons.Filled.Forum` |
| Notifications tab | `bell.fill` | `Icons.Filled.Notifications` |
| Profile tab | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| Mic | `mic.fill` / `mic.slash.fill` | `Icons.Filled.Mic` / `Icons.Filled.MicOff` |
| Nitro / Boost | (custom gradient glyph) | Vector drawable + `NitroGradient` brush |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The dark canvas wants light-content system bars; the Dynamic Island analog (status bar) sits over the `ServerRail` gray. Apply `Scaffold`/`windowInsetsPadding` so the compose bar and tab bar clear gesture nav.
- **Font scaling**: `sp` honors the user's font scale — keep it on message body, username, channel names. Pin layout-sensitive text (timestamps, tab labels) and the presence-dot size by deriving from `dp`. Cap code blocks at 18sp.
- **TalkBack**: merge username + role + message into one node with `Modifier.semantics(mergeDescendants = true)`; announce presence separately ("Online", "Do Not Disturb") — never rely on the dot color alone. Server icons get `contentDescription = server.name`.
- **Touch targets**: Material guidance is 48dp minimum. Server icons are 48dp (clear); the 36dp channel row and 32dp send circle need 48dp hit area via padding; avatar tap gets +8dp hitSlop.
- **Contrast**: the three-gray system with `#F2F3F5` text passes WCAG AA at 16sp. The real low-contrast pairs are **`#949BA4` TextMuted on `#313338`** (timestamps) and **`#5D6069` TextDisabled** — validate at 12sp and strengthen toward `#B5BAC1` where needed. If a server's arbitrary role color fails contrast against the chat canvas, fall back to `#F2F3F5`.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, disable the server-icon squircle morph and the speaking-ring pulse.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Discord's three-gray surface system and the single Blurple accent are brand-load-bearing and must not shift with wallpaper. Strong-brand app: keep the curated scheme.
