# Slack (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Slack's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the workspace-customizable Aubergine sidebar, rounded-square avatars, inline reaction pill chips, first-class threads, the colored mention pills) while making everything idiomatic Android — a `ModalNavigationDrawer` for the sidebar, `NavigationBar` instead of a UITabBar, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for DM/message avatars.

## 1. Color Tokens

```kotlin
// ui/theme/SlackColors.kt
import androidx.compose.ui.graphics.Color

object SlackColors {
    // Sidebar (default Aubergine theme — workspace-customizable)
    val Aubergine        = Color(0xFF4A154B)
    val AubergineDark    = Color(0xFF3F0E40)
    val AubergineActive  = Color(0xFF1164A3) // selected channel row bg
    val SidebarText      = Color(0xFFCFC3CF) // muted lavender-white
    val SidebarActiveText = Color(0xFFFFFFFF)

    // Brand 4-color logo palette (logo + some chrome)
    val LogoYellow = Color(0xFFECB22E)
    val LogoPink   = Color(0xFFE01E5A)
    val LogoGreen  = Color(0xFF2EB67D)
    val LogoBlue   = Color(0xFF36C5F0)

    // Canvas (Light content area)
    val Canvas   = Color(0xFFFFFFFF)
    val Surface  = Color(0xFFF8F8F8)
    val Pressed  = Color(0xFFE6E6E6)
    val Divider  = Color(0xFFDDDDDD)

    // Canvas (Dark content area) — charcoal, not black
    val DarkCanvas   = Color(0xFF1A1D21)
    val DarkSurface1 = Color(0xFF222529)
    val DarkSurface2 = Color(0xFF2C2D30)
    val DarkDivider  = Color(0xFF35373B)

    // Text
    val TextPrimary       = Color(0xFF1D1C1D)
    val TextSecondary     = Color(0xFF616061)
    val TextTertiary      = Color(0xFF868686)
    val DarkTextPrimary   = Color(0xFFD1D2D3)
    val DarkTextSecondary = Color(0xFFABABAD)

    // Semantic & status
    val OnlineGreen = Color(0xFF007A5A) // darker than logo green — dot legibility
    val MentionRed  = Color(0xFFE01E5A)
    val TypingBlue  = Color(0xFF1264A3) // typing + Huddles strip
    val LinkBlue    = Color(0xFF1264A3)

    // Mention pill backgrounds (light)
    val MentionChannelBg = Color(0xFFF5E3BE)
    val MentionHereBg    = Color(0xFFFEF9E7)
    val MentionUserBg    = Color(0xFFF9D5DB)
    val ReactionSelfBg   = Color(0xFFE3F2FC)
}
```

Slack's content area is white (light) / charcoal (dark) — the saturated color lives only in the sidebar. Wire a Material 3 `lightColorScheme`/`darkColorScheme` for the **content canvas**. Aubergine is *not* `primary` — it is the sidebar's color and is passed in as a value (workspace-customizable). Use `OnlineGreen` for send/primary actions.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val SlackLight = lightColorScheme(
    primary        = SlackColors.OnlineGreen, // send / primary actions
    onPrimary      = Color.White,
    background     = SlackColors.Canvas,
    onBackground   = SlackColors.TextPrimary,
    surface        = SlackColors.Canvas,
    onSurface      = SlackColors.TextPrimary,
    surfaceVariant = SlackColors.Surface,
    outline        = SlackColors.Divider,
    error          = SlackColors.MentionRed,
)

private val SlackDark = darkColorScheme(
    primary        = SlackColors.OnlineGreen,
    onPrimary      = Color.White,
    background     = SlackColors.DarkCanvas,
    onBackground   = SlackColors.DarkTextPrimary,
    surface        = SlackColors.DarkSurface1,
    onSurface      = SlackColors.DarkTextPrimary,
    surfaceVariant = SlackColors.DarkSurface2,
    outline        = SlackColors.DarkDivider,
    error          = SlackColors.MentionRed,
)

@Composable
fun SlackTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) SlackDark else SlackLight,
    typography  = SlackTypography,
    content     = content,
)
```

## 2. Typography

Slack Lato (proprietary; evolved from Lato) is the voice. Drop the TTFs in `res/font/` and build a `FontFamily`. Fall back to **Lato** (Apache 2.0, ships well as a bundled font) — visually very close; the system font (Roboto) loses Lato's warmth. Use weights 400 / 600 / 700 only — never 300/500/900.

```kotlin
// ui/theme/SlackType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val SlackLato = FontFamily(
    Font(R.font.slack_lato_regular,  FontWeight.Normal),   // 400 — body
    Font(R.font.slack_lato_semibold, FontWeight.SemiBold), // 600 — reactions / threads
    Font(R.font.slack_lato_bold,     FontWeight.Bold),     // 700 — names / headings
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object SlackText {
    val WorkspaceName  = TextStyle(SlackLato, fontWeight = FontWeight.Bold,     fontSize = 18.sp, lineHeight = 22.sp, letterSpacing = (-0.2).sp)
    val ChannelActive  = TextStyle(SlackLato, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 20.sp)
    val ChannelDefault = TextStyle(SlackLato, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp)
    val ChannelHeader  = TextStyle(SlackLato, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 20.sp)
    val ChannelTopic   = TextStyle(SlackLato, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Username       = TextStyle(SlackLato, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 20.sp)
    val Timestamp      = TextStyle(SlackLato, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 14.sp)
    val MessageBody    = TextStyle(SlackLato, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 23.sp)
    val ThreadCount    = TextStyle(SlackLato, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp)
    val ReactionCount  = TextStyle(SlackLato, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 12.sp)
    val MentionText    = TextStyle(SlackLato, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 23.sp)
    val ButtonPrimary  = TextStyle(SlackLato, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 15.sp)
    val ButtonSecondary = TextStyle(SlackLato, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val TabLabel       = TextStyle(SlackLato, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val SectionHeader  = TextStyle(SlackLato, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 1.0.sp)
    val CodeInline     = TextStyle(FontFamily.Monospace, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 18.sp)
}

val SlackTypography = Typography(
    headlineSmall = SlackText.ChannelHeader,
    titleMedium   = SlackText.Username,
    bodyMedium    = SlackText.MessageBody,
    labelMedium   = SlackText.Timestamp,
    labelSmall    = SlackText.TabLabel,
)
```

## 3. Signature Components

### Aubergine Sidebar (workspace-customizable)

Full-height pane in `#4A154B` (or workspace-custom) — workspace header + section headers + channel rows. Channel text is muted lavender by default, bold white for unread, bold white on a blue row when active. On Android the swipe-in sidebar is a `ModalNavigationDrawer`.

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Lock
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

sealed interface ChannelType {
    data object Public : ChannelType
    data object Private : ChannelType
    data class Dm(val avatarUrl: String) : ChannelType
}

data class ChannelRow(
    val type: ChannelType,
    val name: String,
    val isUnread: Boolean,
    val mentionCount: Int,
)

data class SidebarSection(val title: String, val channels: List<ChannelRow>)

@Composable
fun SlackSidebar(
    workspaceName: String,
    sidebarColor: Color,        // pass SlackColors.Aubergine or a workspace-custom hex
    sections: List<SidebarSection>,
    activeChannel: String?,
    onSelect: (ChannelRow) -> Unit,
) {
    Column(
        Modifier
            .fillMaxHeight()
            .width(280.dp)
            .background(sidebarColor),
    ) {
        // Workspace header
        Row(
            Modifier.fillMaxWidth().height(56.dp).padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(
                Modifier.size(32.dp).clip(RoundedCornerShape(4.dp)).background(SlackColors.LogoBlue),
                contentAlignment = Alignment.Center,
            ) { Text(workspaceName.take(1), color = Color.White, style = SlackText.Username) }
            Column(Modifier.weight(1f)) {
                Text(workspaceName, style = SlackText.WorkspaceName, color = Color.White, maxLines = 1)
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    Box(Modifier.size(8.dp).clip(CircleShape).background(SlackColors.OnlineGreen))
                    Text("You", style = SlackText.ChannelTopic, color = SlackColors.SidebarText)
                }
            }
        }

        LazyColumn(Modifier.fillMaxSize()) {
            sections.forEach { section ->
                item {
                    Text(
                        section.title.uppercase(),
                        style = SlackText.SectionHeader,
                        color = SlackColors.SidebarText.copy(alpha = 0.5f),
                        modifier = Modifier.padding(start = 16.dp, end = 16.dp, top = 16.dp, bottom = 4.dp),
                    )
                }
                items(section.channels) { ch ->
                    SidebarChannelRow(ch, isActive = ch.name == activeChannel, onClick = { onSelect(ch) })
                }
            }
        }
    }
}

@Composable
fun SidebarChannelRow(channel: ChannelRow, isActive: Boolean, onClick: () -> Unit) {
    val nameStyle = if (isActive || channel.isUnread) SlackText.ChannelActive else SlackText.ChannelDefault
    val nameColor = if (isActive || channel.isUnread) Color.White else SlackColors.SidebarText

    Row(
        Modifier
            .fillMaxWidth()
            .height(32.dp)
            .background(if (isActive) SlackColors.AubergineActive else Color.Transparent)
            .clickableNoRipple(onClick)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        when (val t = channel.type) {
            ChannelType.Public -> Text("#", color = nameColor, style = SlackText.ChannelDefault, modifier = Modifier.width(20.dp))
            ChannelType.Private -> Icon(Icons.Filled.Lock, null, tint = nameColor, modifier = Modifier.size(14.dp).width(20.dp))
            is ChannelType.Dm -> AsyncImage(
                model = t.avatarUrl, contentDescription = null, contentScale = ContentScale.Crop,
                modifier = Modifier.size(20.dp).clip(RoundedCornerShape(3.dp)),
            )
        }
        Text(channel.name, style = nameStyle, color = nameColor, modifier = Modifier.weight(1f))
        if (channel.mentionCount > 0) {
            Box(
                Modifier.clip(CircleShape).background(SlackColors.MentionRed).padding(horizontal = 6.dp, vertical = 2.dp),
            ) { Text("${channel.mentionCount}", style = SlackText.ReactionCount, color = Color.White) }
        }
    }
}

import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.runtime.remember
import androidx.compose.ui.composed
fun Modifier.clickableNoRipple(onClick: () -> Unit): Modifier = composed {
    clickable(remember { MutableInteractionSource() }, indication = null, onClick = onClick)
}
```

### Message Row

16.dp horizontal padding; 12.dp top margin for a new sender (4.dp for collapsed consecutive). Leading is a **36.dp rounded-square** avatar (4.dp corner — Slack's signature, NOT a circle) with an 8.dp online dot. Body → reaction chips → thread indicator.

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.lazy.LazyRow

data class Reaction(val emoji: String, val count: Int, val youReacted: Boolean)

@Composable
fun SlackMessageRow(
    username: String,
    avatarUrl: String?,
    statusEmoji: String?,
    status: String?,
    isOnline: Boolean,
    timestamp: String,
    body: String,
    reactions: List<Reaction>,
    threadReplyCount: Int?,
    threadLastReply: String?,
    isNewSender: Boolean,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp, vertical = if (isNewSender) 8.dp else 2.dp),
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        if (isNewSender) {
            Box {
                if (avatarUrl != null) {
                    AsyncImage(
                        model = avatarUrl, contentDescription = null, contentScale = ContentScale.Crop,
                        modifier = Modifier.size(36.dp).clip(RoundedCornerShape(4.dp)), // rounded square
                    )
                } else {
                    Box(Modifier.size(36.dp).clip(RoundedCornerShape(4.dp)).background(SlackColors.LogoGreen))
                }
                if (isOnline) {
                    Box(
                        Modifier
                            .align(Alignment.BottomEnd)
                            .size(10.dp)
                            .clip(CircleShape)
                            .background(SlackColors.Canvas)
                            .padding(1.dp)
                    ) {
                        Box(Modifier.fillMaxSize().clip(CircleShape).background(SlackColors.OnlineGreen))
                    }
                }
            }
        } else {
            Spacer(Modifier.width(36.dp))
        }

        Column(verticalArrangement = Arrangement.spacedBy(4.dp)) {
            if (isNewSender) {
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                    Text(username, style = SlackText.Username, color = SlackColors.TextPrimary)
                    statusEmoji?.let { Text(it) }
                    status?.let { Text(it, style = SlackText.ChannelTopic, color = SlackColors.TextSecondary, maxLines = 1) }
                    Text(timestamp, style = SlackText.Timestamp, color = SlackColors.TextSecondary)
                }
            }
            Text(body, style = SlackText.MessageBody, color = SlackColors.TextPrimary)
            if (reactions.isNotEmpty()) {
                LazyRow(horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    items(reactions) { ReactionChip(it) }
                    item { AddReactionChip() }
                }
            }
            if (threadReplyCount != null && threadReplyCount > 0) {
                ThreadIndicator(threadReplyCount, threadLastReply ?: "")
            }
        }
    }
}
```

### Reaction Chip

Pill (full radius), `Surface` default → `ReactionSelfBg` with a 1.dp blue border when you've reacted.

```kotlin
import androidx.compose.material.icons.filled.AddReaction
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun ReactionChip(r: Reaction) {
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier
            .clip(CircleShape)
            .background(if (r.youReacted) SlackColors.ReactionSelfBg else SlackColors.Surface)
            .then(if (r.youReacted) Modifier.border(1.dp, SlackColors.TypingBlue, CircleShape) else Modifier)
            .clickableNoRipple { haptics.performHapticFeedback(HapticFeedbackType.LongPress) }
            .padding(horizontal = 8.dp, vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Text(r.emoji, style = SlackText.MessageBody)
        Text("${r.count}", style = SlackText.ReactionCount, color = SlackColors.TextPrimary)
    }
}

@Composable
fun AddReactionChip() {
    Box(
        Modifier.clip(CircleShape).background(SlackColors.Surface).padding(horizontal = 8.dp, vertical = 4.dp),
    ) { Icon(Icons.Filled.AddReaction, "Add reaction", tint = SlackColors.TextSecondary, modifier = Modifier.size(14.dp)) }
}
```

### Thread Indicator (threads are first-class)

```kotlin
import androidx.compose.material.icons.automirrored.filled.KeyboardArrowRight

@Composable
fun ThreadIndicator(replyCount: Int, lastReply: String, onClick: () -> Unit = {}) {
    Row(
        Modifier.fillMaxWidth().clickableNoRipple(onClick),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        // up to 3 overlapping rounded-square replier avatars
        Box {
            val palette = listOf(SlackColors.LogoYellow, SlackColors.LogoPink, SlackColors.LogoGreen)
            repeat(minOf(3, replyCount)) { i ->
                Box(
                    Modifier
                        .padding(start = (i * 10).dp)
                        .size(16.dp)
                        .clip(RoundedCornerShape(3.dp))
                        .background(palette[i % 3])
                        .border(1.dp, SlackColors.Canvas, RoundedCornerShape(3.dp))
                )
            }
        }
        Text(
            "$replyCount ${if (replyCount == 1) "reply" else "replies"}",
            style = SlackText.ThreadCount,
            color = SlackColors.LinkBlue,
        )
        Text("• $lastReply", style = SlackText.Timestamp, color = SlackColors.TextSecondary)
        Spacer(Modifier.weight(1f))
        Icon(Icons.AutoMirrored.Filled.KeyboardArrowRight, null, tint = SlackColors.TextSecondary, modifier = Modifier.size(12.dp))
    }
}
```

### Mention Pill (inline)

`@channel` green, `@here` yellow, `@user` red — colored background, bold text, 4.dp corner (not a pill).

```kotlin
import androidx.compose.foundation.text.appendInlineContent
import androidx.compose.ui.text.AnnotatedString

sealed interface Mention {
    data object Channel : Mention
    data object Here : Mention
    data class User(val name: String) : Mention
}

@Composable
fun MentionPill(mention: Mention) {
    val (label, bg, fg) = when (mention) {
        Mention.Channel -> Triple("@channel", SlackColors.LogoGreen.copy(alpha = 0.2f), Color(0xFF0F7B6C))
        Mention.Here    -> Triple("@here", SlackColors.LogoYellow.copy(alpha = 0.2f), Color(0xFF665208))
        is Mention.User -> Triple("@${mention.name}", SlackColors.MentionUserBg, Color(0xFF5A2731))
    }
    Text(
        label,
        style = SlackText.MentionText,
        color = fg,
        modifier = Modifier
            .clip(RoundedCornerShape(4.dp))
            .background(bg)
            .padding(horizontal = 4.dp, vertical = 1.dp),
    )
}
```

### Huddles Banner & Message Composer

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Send

@Composable
fun HuddlesBanner(onJoin: () -> Unit) {
    Row(
        Modifier.fillMaxWidth().height(56.dp).background(SlackColors.TypingBlue).padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        // 4-color pinwheel
        Column {
            Row { Box(Modifier.size(8.dp).background(SlackColors.LogoYellow)); Box(Modifier.size(8.dp).background(SlackColors.LogoPink)) }
            Row { Box(Modifier.size(8.dp).background(SlackColors.LogoGreen)); Box(Modifier.size(8.dp).background(SlackColors.LogoBlue)) }
        }
        Text("Huddle in progress", style = SlackText.ButtonPrimary, color = Color.White)
        Spacer(Modifier.weight(1f))
        Box(
            Modifier.clip(RoundedCornerShape(4.dp)).background(Color.White).clickableNoRipple(onJoin)
                .padding(horizontal = 16.dp, vertical = 8.dp),
        ) { Text("Join", style = SlackText.ButtonSecondary, color = SlackColors.TypingBlue) }
    }
}

@Composable
fun MessageComposer(channelName: String, modifier: Modifier = Modifier) {
    var text by remember { mutableStateOf("") }
    val haptics = LocalHapticFeedback.current

    Row(
        modifier = modifier
            .fillMaxWidth()
            .background(SlackColors.Canvas)
            .padding(8.dp),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(Icons.Filled.Add, "Attach", tint = SlackColors.TextSecondary, modifier = Modifier.size(40.dp).padding(8.dp))
        BasicTextField(
            value = text,
            onValueChange = { text = it },
            textStyle = SlackText.MessageBody.copy(color = SlackColors.TextPrimary),
            modifier = Modifier
                .weight(1f)
                .clip(RoundedCornerShape(8.dp))
                .background(SlackColors.Surface)
                .padding(horizontal = 12.dp, vertical = 10.dp),
            decorationBox = { inner ->
                if (text.isEmpty()) Text("Message #$channelName", style = SlackText.MessageBody, color = SlackColors.TextSecondary)
                inner()
            },
        )
        Box(
            Modifier
                .size(32.dp)
                .clip(RoundedCornerShape(4.dp)) // square, 4dp — Slack's send button
                .background(if (text.isEmpty()) SlackColors.TextTertiary else SlackColors.OnlineGreen)
                .clickableNoRipple {
                    if (text.isNotEmpty()) {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                        text = ""
                    }
                },
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.AutoMirrored.Filled.Send, "Send", tint = Color.White, modifier = Modifier.size(16.dp)) }
    }
}
```

## 4. Workspace-Customizable Sidebar Theme

Slack's signature: every workspace can pick its own sidebar color (admins can set any hex). iOS hands a `WorkspaceTheme` per screen; in Compose model it as a `CompositionLocal` and pick readable channel-text color from luminance so any custom hex stays accessible.

```kotlin
import androidx.compose.runtime.compositionLocalOf
import androidx.compose.runtime.CompositionLocalProvider
import androidx.compose.ui.graphics.luminance

data class WorkspaceTheme(
    val sidebarColor: Color = SlackColors.Aubergine,
    val activeChannelColor: Color = SlackColors.AubergineActive,
) {
    companion object {
        val Aubergine = WorkspaceTheme()
        val Monument  = WorkspaceTheme(Color(0xFF2C2D30), Color(0xFF481349))
        val Tangerine = WorkspaceTheme(Color(0xFFCA4400), Color(0xFF6B140C))
        fun custom(sidebar: Color, active: Color) = WorkspaceTheme(sidebar, active)
    }
}

val LocalWorkspaceTheme = compositionLocalOf { WorkspaceTheme.Aubergine }

@Composable
fun ProvideWorkspaceTheme(theme: WorkspaceTheme, content: @Composable () -> Unit) =
    CompositionLocalProvider(LocalWorkspaceTheme provides theme, content = content)

// Channel text must remain legible on any chosen sidebar hex
fun sidebarTextColor(sidebar: Color): Color =
    if (sidebar.luminance() > 0.5f) SlackColors.TextPrimary else SlackColors.SidebarText
```

## 5. Navigation

Slack's sidebar is the `ModalNavigationDrawer` (swipe-right to open, swipe-left to close — matches the spec). The five destinations (Home, DMs, Activity, Later, Search) are a Material 3 `NavigationBar`. Active state is a **bold neutral icon, not a color shift**. Solid surface, no blur. Threads are first-class: on phones the thread "pane" is a full-screen destination that slides in from the right.

```kotlin
import androidx.compose.material.icons.automirrored.filled.Chat
import androidx.compose.material.icons.filled.*

@Composable
fun SlackBottomBar(selected: Int, unreadActivity: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = SlackColors.Canvas, // solid, no blur
        tonalElevation = 0.dp,
    ) {
        data class Tab(val label: String, val icon: androidx.compose.ui.graphics.vector.ImageVector, val badge: Int = 0)
        val tabs = listOf(
            Tab("Home", Icons.Filled.Home),
            Tab("DMs", Icons.AutoMirrored.Filled.Chat),
            Tab("Activity", Icons.Filled.Notifications, badge = unreadActivity),
            Tab("Later", Icons.Filled.BookmarkBorder),
            Tab("Search", Icons.Filled.Search),
        )
        tabs.forEachIndexed { i, t ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (t.badge > 0) {
                        BadgedBox(badge = { Badge(containerColor = SlackColors.MentionRed) { Text("${t.badge}") } }) {
                            Icon(t.icon, t.label, modifier = Modifier.size(24.dp))
                        }
                    } else {
                        Icon(t.icon, t.label, modifier = Modifier.size(24.dp))
                    }
                },
                label = { Text(t.label, style = SlackText.TabLabel) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = SlackColors.TextPrimary, // bold neutral, no color shift
                    selectedTextColor   = SlackColors.TextPrimary,
                    unselectedIconColor = SlackColors.TextSecondary,
                    unselectedTextColor = SlackColors.TextSecondary,
                    indicatorColor      = Color.Transparent,
                ),
            )
        }
    }
}

// Sidebar wrapper
@Composable
fun SlackScaffold(drawerState: DrawerState, sidebar: @Composable () -> Unit, content: @Composable () -> Unit) {
    ModalNavigationDrawer(
        drawerState = drawerState,
        drawerContent = { ModalDrawerSheet(drawerContainerColor = Color.Transparent) { sidebar() } },
        content = content,
    )
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Sidebar open/close | `ModalNavigationDrawer` 1:1 drag; commit `spring(dampingRatio = 0.8f, stiffness = 600f)` ~250ms + `HapticFeedbackType.TextHandleMove` |
| Message send | send button `animateFloatAsState` 1 → 0.9 → 1 over 200ms; message enters via `AnimatedVisibility` `scaleIn(0.96f) + fadeIn()` 250ms |
| Reaction add | chip `Animatable` 0 → 1.15 → 1 `spring(stiffness = 500f)` ~300ms + soft haptic |
| Thread pane open | nav transition `slideInHorizontally { it }` (from right) 250ms ease-out |
| Huddle join | full-screen `fadeIn` + `HapticFeedbackType.Confirm` (success) |
| Typing indicator | `rememberInfiniteTransition` opacity 1.0 ↔ 0.6 over 1.2s |

```kotlin
// Reaction chip pop-in
@Composable
fun rememberChipPop(trigger: Any): Float {
    val scale = remember { Animatable(0f) }
    LaunchedEffect(trigger) {
        scale.animateTo(1.15f, spring(dampingRatio = 0.6f, stiffness = 500f))
        scale.animateTo(1f, spring(dampingRatio = 0.7f))
    }
    return scale.value
}
```

Keep reactions gentle — a single spring scale, never elaborate. Haptics: prefer `LocalHapticFeedback`; soft on send/reaction (`LongPress`), `Confirm` on huddle join, `TextHandleMove` on workspace switch. Honor reduce-motion by skipping the sidebar spring and chip pop while still firing haptics.

## 7. Icons

Slack ships custom glyphs (4-color pinwheel, channel `#`); the closest first-party set is `androidx.compose.material:material-icons-extended`. The public-channel `#` renders as a **text character** (universal glyph), not an icon — icons only for private/muted. The pinwheel ships as a vector drawable.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| DMs (tab) | `bubble.left.and.bubble.right` | `Icons.AutoMirrored.Filled.Chat` |
| Activity (tab) | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| Later (tab) | `bookmark` / `bookmark.fill` | `Icons.Filled.BookmarkBorder` / `Icons.Filled.Bookmark` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Channel (public) | `number` | text `#` (universal glyph) |
| Channel (private) | `lock.fill` | `Icons.Filled.Lock` |
| Mute | `bell.slash` | `Icons.Filled.NotificationsOff` |
| Huddle start | `headphones` | `Icons.Filled.Headphones` |
| Channel info | `info.circle` | `Icons.Filled.Info` |
| Send | `paperplane.fill` | `Icons.AutoMirrored.Filled.Send` |
| Attach | `plus` | `Icons.Filled.Add` |
| Emoji picker | `face.smiling` | `Icons.Filled.AddReaction` |
| Mention | `at` | `Icons.Filled.AlternateEmail` |
| Voice note | `mic` | `Icons.Filled.Mic` |
| Formatting | `textformat` | `Icons.Filled.TextFormat` |
| Thread chevron | `chevron.right` | `Icons.AutoMirrored.Filled.KeyboardArrowRight` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24**. `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; system-bar icon color follows light/dark. Apply `Scaffold` insets so the top nav clears the status bar and the tab bar clears gesture nav; the composer must rise above the IME (`Modifier.imePadding()`). The sidebar (`ModalNavigationDrawer`) is edge-to-edge.
- **Font scaling**: `sp` honors user scale on message body, username, channel name, thread indicator. **Pin timestamps, reaction counts, tab labels, and section headers to fixed sizes** (layout-sensitive) via a fixed-`fontScale` `LocalDensity`. Code inline scales in the monospace family.
- **TalkBack**: merge a message row into one element — `Modifier.semantics(mergeDescendants = true)` with a combined label: "Sarah Chen, online, status on vacation, 10:42 AM. {body}. 3 reactions. 5 replies in thread." The reaction chips and thread indicator are nested actions.
- **Touch targets**: reaction chips are ~28.dp tall — give them a 44.dp effective hit via padding; send button is 32.dp visual / 44.dp hit; sidebar rows are 32.dp full-row tappable.
- **Contrast**: `#1D1C1D` on `#FFFFFF` passes WCAG AAA; `#CFC3CF` on `#4A154B` passes AA at 15sp. Mention pills have carefully-paired bg/fg. For a workspace-custom sidebar hex, run `sidebarTextColor()` (luminance split) so channel text never fails contrast.
- **RTL**: the sidebar drawer flips to the right edge automatically with `ModalNavigationDrawer` under an RTL `LayoutDirection`; the thread pane slide direction flips with it.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` for the content canvas — Slack's identity is the fixed white/charcoal canvas plus the aubergine sidebar. The sidebar's color IS the customization point, and it is workspace-data-driven (any admin hex), not wallpaper-driven — that is the intentional theming surface.
