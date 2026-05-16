# Microsoft Teams (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Teams' visual language to **Android with Jetpack Compose (Material 3)**: color token objects, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Teams' Fluent dual-identity surfaces, Teams Purple, presence dots, the Teams → Channels tree, the meeting join bar) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, Compose `darkColorScheme`/`lightColorScheme`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and `androidx.compose.material:material-icons-extended`.

## 1. Color Tokens

```kotlin
// ui/theme/TeamsColors.kt
import androidx.compose.ui.graphics.Color

object TeamsLight {
    val Canvas   = Color(0xFFF5F5F5)
    val Surface1 = Color(0xFFFFFFFF)
    val Surface2 = Color(0xFFF0F0F0)
    val Divider  = Color(0xFFE1E1E1)
    val Text1    = Color(0xFF252423)
    val Text2    = Color(0xFF616161)
    val Text3    = Color(0xFF8A8886)
    val Accent   = Color(0xFF6264A7)
}

object TeamsDark {
    val Canvas   = Color(0xFF1F1F1F)
    val Surface1 = Color(0xFF2D2C2C)
    val Surface2 = Color(0xFF3D3C3C)
    val Divider  = Color(0xFF3D3C3C)
    val Text1    = Color(0xFFFFFFFF)
    val Text2    = Color(0xFFADADAD)
    val Text3    = Color(0xFF7A7A7A)
    val Accent   = Color(0xFF5B5FC7)
}

object TeamsShared {
    val AccentPressed = Color(0xFF4F52B2)
    val AccentTint    = Color(0x1F6264A7) // ~12% alpha
    val Available     = Color(0xFF6BB700)
    val Busy          = Color(0xFFC4314B)
    val Away          = Color(0xFFFFAA44)
    val Offline       = Color(0xFF8A8886)
}
```

Wire both into Material 3 schemes so ripples and stock components inherit the brand in each appearance.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val LightScheme = lightColorScheme(
    primary       = TeamsLight.Accent,
    onPrimary     = Color.White,
    background     = TeamsLight.Canvas,
    onBackground   = TeamsLight.Text1,
    surface        = TeamsLight.Surface1,
    onSurface      = TeamsLight.Text1,
    surfaceVariant = TeamsLight.Surface2,
    outline        = TeamsLight.Divider,
    error          = TeamsShared.Busy,
)

private val DarkScheme = darkColorScheme(
    primary       = TeamsDark.Accent,
    onPrimary     = Color.White,
    background     = TeamsDark.Canvas,
    onBackground   = TeamsDark.Text1,
    surface        = TeamsDark.Surface1,
    onSurface      = TeamsDark.Text1,
    surfaceVariant = TeamsDark.Surface2,
    outline        = TeamsDark.Divider,
    error          = TeamsShared.Busy,
)

@Composable
fun TeamsTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) DarkScheme else LightScheme,
        typography  = TeamsTypography,
        content     = content,
    )
```

## 2. Typography

Teams uses Segoe UI; Inter is the closest free substitute. Drop the TTFs in `res/font/` (lowercase, snake_case).

```kotlin
// ui/theme/TeamsType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Segoe = FontFamily(
    Font(R.font.segoe_ui_regular,  FontWeight.Normal),   // 400
    Font(R.font.segoe_ui_semibold, FontWeight.SemiBold), // 600
    Font(R.font.segoe_ui_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object TeamsText {
    val TitleLarge = TextStyle(Segoe, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.3).sp)
    val Section    = TextStyle(Segoe, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 25.sp, letterSpacing = (-0.2).sp)
    val TeamName   = TextStyle(Segoe, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 21.sp)
    val ListTitle  = TextStyle(Segoe, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 21.sp)
    val Author     = TextStyle(Segoe, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Body       = TextStyle(Segoe, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Button     = TextStyle(Segoe, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp)
    val Metadata   = TextStyle(Segoe, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val Reaction   = TextStyle(Segoe, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 12.sp)
    val Tab        = TextStyle(Segoe, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp)
    val TinyUpper  = TextStyle(Segoe, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val TeamsTypography = Typography(
    headlineLarge = TeamsText.TitleLarge,
    headlineSmall = TeamsText.Section,
    titleMedium   = TeamsText.ListTitle,
    bodyMedium    = TeamsText.Body,
    labelSmall    = TeamsText.Tab,
)
```

A small helper to resolve the active palette:

```kotlin
@Composable
fun teamsPalette() = if (isSystemInDarkTheme()) Pair(TeamsDark, TeamsLight).first else Pair(TeamsLight, TeamsDark).first
// Or expose individual values; examples below read from `c` = the active object.
```

## 3. Signature Components

### Presence Dot (the loaded semantic color)

```kotlin
enum class Presence { Available, Busy, Dnd, Away, Offline }

@Composable
fun PresenceDot(presence: Presence, size: Dp = 10.dp, ringColor: Color) {
    val animatedFill by animateColorAsState(
        targetValue = when (presence) {
            Presence.Available -> TeamsShared.Available
            Presence.Busy, Presence.Dnd -> TeamsShared.Busy
            Presence.Away -> TeamsShared.Away
            Presence.Offline -> Color.Transparent
        },
        animationSpec = tween(250), label = "presence",
    )
    Box(
        Modifier
            .size(size)
            .clip(CircleShape)
            .background(animatedFill)
            .border(
                if (presence == Presence.Offline) 1.5.dp else 2.dp,
                if (presence == Presence.Offline) TeamsShared.Offline else ringColor,
                CircleShape,
            ),
        contentAlignment = Alignment.Center,
    ) {
        if (presence == Presence.Dnd) {
            Box(Modifier.fillMaxWidth(0.5f).fillMaxHeight(0.18f)
                .clip(RoundedCornerShape(1.dp)).background(Color.White))
        }
    }
}

@Composable
fun AvatarWithPresence(initials: String, presence: Presence, size: Dp = 32.dp, ringColor: Color) {
    Box(Modifier.size(size)) {
        Box(
            Modifier.matchParentSize().clip(CircleShape).background(TeamsLight.Accent.copy(alpha = 0.25f)),
            contentAlignment = Alignment.Center,
        ) { Text(initials, fontSize = (size.value * 0.4f).sp, fontWeight = FontWeight.SemiBold, color = Color.White) }
        Box(Modifier.align(Alignment.BottomEnd)) {
            PresenceDot(presence, size * 0.32f, ringColor)
        }
    }
}
```

### Teams → Channels Tree (the signature element)

```kotlin
data class Team(val name: String, val channels: List<Channel>)
data class Channel(val id: String, val name: String, val unread: Boolean)

@Composable
fun TeamTree(team: Team, activeChannel: String?, onSelect: (String) -> Unit, c: TeamsLight = TeamsLight) {
    var expanded by remember { mutableStateOf(true) }
    val rot by animateFloatAsState(if (expanded) 90f else 0f, tween(200), label = "chev")

    Column {
        Row(
            Modifier
                .fillMaxWidth()
                .height(56.dp)
                .clickable { expanded = !expanded }
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(
                Modifier.size(32.dp).clip(RoundedCornerShape(8.dp)).background(c.Accent.copy(alpha = 0.3f)),
                contentAlignment = Alignment.Center,
            ) { Text(team.name.take(1), fontWeight = FontWeight.Bold, fontSize = 14.sp, color = Color.White) }
            Text(team.name, style = TeamsText.TeamName, color = c.Text1, modifier = Modifier.weight(1f))
            Icon(Icons.Filled.ChevronRight, "Expand", tint = c.Text2,
                modifier = Modifier.size(16.dp).graphicsLayer { rotationZ = rot })
        }
        AnimatedVisibility(expanded, enter = expandVertically(tween(200)), exit = shrinkVertically(tween(200))) {
            Column {
                team.channels.forEach { ch ->
                    val active = ch.id == activeChannel
                    Box {
                        Row(
                            Modifier
                                .fillMaxWidth()
                                .height(44.dp)
                                .background(if (active) TeamsShared.AccentTint else Color.Transparent)
                                .clickable { onSelect(ch.id) }
                                .padding(start = 44.dp, end = 16.dp),
                            verticalAlignment = Alignment.CenterVertically,
                            horizontalArrangement = Arrangement.spacedBy(8.dp),
                        ) {
                            Text("#", fontWeight = FontWeight.SemiBold, fontSize = 16.sp, color = c.Text2)
                            Text(
                                ch.name,
                                style = TeamsText.ListTitle.copy(
                                    fontWeight = if (ch.unread) FontWeight.Bold else FontWeight.SemiBold),
                                color = c.Text1, modifier = Modifier.weight(1f),
                            )
                            if (ch.unread) Box(Modifier.size(8.dp).clip(CircleShape).background(c.Accent))
                        }
                        if (active) Box(Modifier.align(Alignment.CenterStart).width(3.dp).fillMaxHeight().background(c.Accent))
                    }
                }
            }
        }
    }
}
```

### Message Card

```kotlin
@Composable
fun MessageCard(
    author: String, initials: String, presence: Presence,
    timestamp: String, body: String, replyCount: Int, c: TeamsLight = TeamsLight,
) {
    Row(
        Modifier.fillMaxWidth().clip(RoundedCornerShape(8.dp)).background(c.Surface1).padding(12.dp),
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        AvatarWithPresence(initials, presence, ringColor = c.Surface1)
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Row(horizontalArrangement = Arrangement.spacedBy(8.dp), verticalAlignment = Alignment.Bottom) {
                Text(author, style = TeamsText.Author, color = c.Text1)
                Text(timestamp, style = TeamsText.Metadata, color = c.Text2)
            }
            Text(body, style = TeamsText.Body, color = c.Text1)
            Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                ReactionChip("👍", 3, true, c)
                ReactionChip("❤️", 1, false, c)
            }
            if (replyCount > 0) {
                Text("💬 $replyCount replies · Last reply 2h ago", style = TeamsText.Metadata, color = c.Text2)
            }
        }
    }
}

@Composable
fun ReactionChip(emoji: String, count: Int, mine: Boolean, c: TeamsLight) {
    Row(
        Modifier.clip(CircleShape)
            .background(if (mine) TeamsShared.AccentTint else c.Surface2)
            .padding(horizontal = 8.dp, vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Text(emoji, fontSize = 12.sp)
        Text("$count", style = TeamsText.Reaction, color = c.Text2)
    }
}
```

### Meeting Join Bar

```kotlin
@Composable
fun MeetingJoinBar(title: String, onJoin: () -> Unit, c: TeamsLight = TeamsLight) {
    val t = rememberInfiniteTransition(label = "joinPulse")
    val alpha by t.animateFloat(1f, 0.85f,
        infiniteRepeatable(tween(2000), RepeatMode.Reverse), label = "pulse")

    Row(
        Modifier
            .padding(horizontal = 12.dp)
            .fillMaxWidth()
            .height(56.dp)
            .graphicsLayer { this.alpha = alpha }
            .clip(RoundedCornerShape(12.dp))
            .background(c.Accent)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Icon(Icons.Filled.Videocam, null, tint = Color.White, modifier = Modifier.size(18.dp))
        Text(title, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, color = Color.White,
            modifier = Modifier.weight(1f))
        Box(
            Modifier.clip(CircleShape).background(Color.White)
                .clickable(onClick = onJoin).padding(horizontal = 18.dp, vertical = 6.dp),
        ) { Text("Join", fontWeight = FontWeight.SemiBold, fontSize = 14.sp, color = c.Accent) }
    }
}
```

### Primary Button

```kotlin
@Composable
fun TeamsPrimaryButton(text: String, onClick: () -> Unit, c: TeamsLight = TeamsLight, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f,
        spring(dampingRatio = 0.85f, stiffness = 600f), label = "btnScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .scale(scale)
            .height(44.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) TeamsShared.AccentPressed else c.Accent)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            }
            .padding(horizontal = 24.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = TeamsText.Button, color = Color.White)
    }
}
```

## 4. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. The active tint is Teams Purple; this surface follows the active scheme. Activity carries a badge.

```kotlin
@Composable
fun TeamsBottomBar(selected: Int, onSelect: (Int) -> Unit, c: TeamsLight = TeamsLight) {
    NavigationBar(containerColor = c.Surface1) {
        val items = listOf(
            Triple("Activity", Icons.Filled.Notifications, 3),
            Triple("Chat",     Icons.Filled.Chat, 0),
            Triple("Teams",    Icons.Filled.Groups, 0),
            Triple("Calendar", Icons.Filled.CalendarMonth, 0),
            Triple("Calls",    Icons.Filled.Call, 0),
        )
        items.forEachIndexed { i, (label, icon, badge) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    BadgedBox(badge = { if (badge > 0) Badge { Text("$badge") } }) {
                        Icon(icon, label, modifier = Modifier.size(24.dp))
                    }
                },
                label = { Text(label, style = TeamsText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = c.Accent,
                    selectedTextColor = c.Accent,
                    unselectedIconColor = c.Text2,
                    unselectedTextColor = c.Text2,
                    indicatorColor = Color.Transparent,
                ),
            )
        }
    }
}
```

The **Teams → Channels tree** is the Teams tab's full-screen content; on tablets render it as the left pane of a two-pane layout with the conversation on the right.

## 5. Motion

| Moment | Compose recipe |
|--------|----------------|
| Primary button tap | `animateFloatAsState` 1 → 0.98 `spring(dampingRatio = 0.85f)`, `HapticFeedbackType.LongPress` |
| Tree expand/collapse | `AnimatedVisibility` `expandVertically`/`shrinkVertically` `tween(200)` + chevron `rotationZ` |
| Reaction picker (long-press) | `AnimatedVisibility` `scaleIn(0.9f)` + `fadeIn`, `spring(dampingRatio = 0.8f)` |
| Presence change | `animateColorAsState` `tween(250)` on the dot fill |
| Message send | new item `fadeIn` + `slideInVertically` `tween(200)` |
| Join bar | `rememberInfiniteTransition` alpha 1.0 ↔ 0.85 `tween(2000)` reverse; slide+fade on appear |

Haptics: prefer `LocalHapticFeedback`. For a crisper tick matching iOS `.light` impact use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) on primary action, send, and Join.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Activity (tab) | `bell.fill` | `Icons.Filled.Notifications` |
| Chat (tab) | `bubble.left.and.bubble.right.fill` | `Icons.Filled.Chat` |
| Teams (tab) | `person.3.fill` | `Icons.Filled.Groups` |
| Calendar (tab) | `calendar` | `Icons.Filled.CalendarMonth` |
| Calls (tab) | `phone.fill` | `Icons.Filled.Call` |
| Channel marker | `number` | text `#` (or `Icons.Filled.Tag`) |
| Expand chevron | `chevron.right` | `Icons.Filled.ChevronRight` |
| Meeting / join | `video.fill` | `Icons.Filled.Videocam` |
| Send | `paperplane.fill` | `Icons.Filled.Send` |
| Attach | `paperclip` | `Icons.Filled.AttachFile` |
| Emoji | `face.smiling` | `Icons.Filled.EmojiEmotions` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| New chat | `square.and.pencil` | `Icons.Filled.Edit` |
| Reply / thread | `arrowshape.turn.up.left` | `Icons.AutoMirrored.Filled.Reply` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; system bars follow light/dark. Apply `Scaffold` / `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the composer + tab bar clear the gesture nav.
- **Dual identity**: verify every component in both `lightColorScheme` and `darkColorScheme`; do not assume one is primary.
- **Presence as text**: never communicate presence by color alone — set `contentDescription` ("Available", "Busy") on the avatar.
- **Tabular numerics**: call durations and aligned timestamps should not reflow — set `TextStyle(fontFeatureSettings = "tnum")`.
- **Font scaling**: `sp` honors the user scale — keep it on names/body/titles. Pin the 10.dp presence dot and tabular timestamps and 10.sp tab labels (derive from `dp` or wrap in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`).
- **TalkBack**: announce presence as text; mark unread channels/chats with an "unread" state; expose the tree row's expand/collapse via `Modifier.semantics { stateDescription = if (expanded) "expanded" else "collapsed" }`; merge message-card text with `Modifier.semantics(mergeDescendants = true)`.
- **Touch targets**: Material guidance is 48.dp minimum. The 44.dp button/rows are close; ensure the 24.dp tab icons and 16.dp tree chevron get a 48.dp hit area via padding.
- **Contrast**: `#616161` on `#F5F5F5` (light) and `#ADADAD` on `#1F1F1F` (dark) pass WCAG AA at 13sp+. Validate 10sp tab labels and bump toward higher-contrast greys if targeting strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Teams' brand requires the fixed Teams Purple and Fluent surfaces regardless of wallpaper.
