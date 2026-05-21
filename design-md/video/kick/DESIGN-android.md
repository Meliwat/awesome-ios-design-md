# Kick (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Kick's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the live chat panel, the watch page, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Kick's near-black canvas, the single screaming green, the video + streamer-bar + live-chat watch page, the role-colored usernames and badge chips) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyColumn` for chat, `expo-image`→Coil, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for thumbnails, avatars, and emote sprites. Kick is **dark-only** — no light scheme, no Material You dynamic color.

## 1. Color Tokens

```kotlin
// ui/theme/KickColors.kt
import androidx.compose.ui.graphics.Color

object KickColors {
    // Canvas & Surfaces (dark-only; NOT pure black)
    val Canvas   = Color(0xFF0E0E10)
    val Surface1 = Color(0xFF161618)
    val Surface2 = Color(0xFF1F1F23)
    val Surface3 = Color(0xFF2A2A2E)
    val Divider  = Color(0xFF2D2D31)

    // Brand
    val Green        = Color(0xFF53FC18)
    val GreenPressed = Color(0xFF45D912)
    val GreenDeep    = Color(0xFF00E701)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFA6A6AD)
    val TextTertiary  = Color(0xFF6E6E76)
    val ChatBody      = Color(0xFFE4E4E9)
    val OnGreen       = Color(0xFF0E0E10)

    // Chat roles
    val Mod = Color(0xFF3EA6FF)
    val Sub = Color(0xFFFFC700)
    val VIP = Color(0xFFFF4FD8)

    // Semantic
    val Live  = Color(0xFFFF1F44)
    val Error = Color(0xFFFF4A4A)
}

sealed interface ChatRole {
    data object Mod : ChatRole
    data class Sub(val months: Int) : ChatRole
    data object VIP : ChatRole
    data object OG : ChatRole
    data object Verified : ChatRole
    data class Regular(val color: Color) : ChatRole
}

fun ChatRole.userColor(): Color = when (this) {
    is ChatRole.Mod -> KickColors.Mod
    is ChatRole.Sub -> KickColors.Sub
    is ChatRole.VIP -> KickColors.VIP
    is ChatRole.OG -> KickColors.Green
    is ChatRole.Verified -> KickColors.TextPrimary
    is ChatRole.Regular -> color
}

data class BadgeSpec(val text: String, val bg: Color, val fg: Color)

fun ChatRole.badge(): BadgeSpec? = when (this) {
    is ChatRole.Mod -> BadgeSpec("MOD", KickColors.Mod, Color(0xFF06121F))
    is ChatRole.Sub -> BadgeSpec(months.toString(), KickColors.Sub, Color(0xFF1A1500))
    is ChatRole.VIP -> BadgeSpec("VIP", KickColors.VIP, Color(0xFF2A0022))
    is ChatRole.OG -> BadgeSpec("OG", KickColors.Green, KickColors.OnGreen)
    is ChatRole.Verified -> BadgeSpec("✓", KickColors.Surface3, Color.White)
    is ChatRole.Regular -> null
}
```

Wire it into a dark-only scheme. Kick has no light mode and no brand accent that should harmonize with the wallpaper — never enable `dynamicColorScheme()`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme

private val KickDark = darkColorScheme(
    primary        = KickColors.Green,
    onPrimary      = KickColors.OnGreen,
    background     = KickColors.Canvas,
    onBackground   = KickColors.TextPrimary,
    surface        = KickColors.Surface1,
    onSurface      = KickColors.TextPrimary,
    surfaceVariant = KickColors.Surface2,
    outline        = KickColors.Divider,
    error          = KickColors.Error,
)

@Composable
fun KickTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = KickDark, typography = KickTypography, content = content)  // always dark
```

## 2. Typography

Kick's brand face is a tight modern grotesque; **Inter** at heavy weights is the closest free analog (SIL OFL). Drop the TTFs in `res/font/`. Use `fontFeatureSettings = "tnum"` on viewer counts.

```kotlin
// ui/theme/KickType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),
    Font(R.font.inter_medium,    FontWeight.Medium),
    Font(R.font.inter_semibold,  FontWeight.SemiBold),
    Font(R.font.inter_bold,      FontWeight.Bold),
    Font(R.font.inter_extrabold, FontWeight.ExtraBold),
    Font(R.font.inter_black,     FontWeight.Black),
)

object KickText {
    val Display     = TextStyle(Inter, fontWeight = FontWeight.Black,     fontSize = 32.sp, lineHeight = 34.sp, letterSpacing = (-0.5).sp)
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 24.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Streamer    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 19.sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 23.sp)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 16.sp)
    val ChatUser    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 18.sp)
    val ChatMsg     = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 17.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 15.sp, lineHeight = 15.sp)
    val Viewers     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 11.sp, fontFeatureSettings = "tnum")
    val Badge       = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.5.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

val KickTypography = Typography(
    headlineLarge  = KickText.Display,
    headlineMedium = KickText.ScreenTitle,
    titleMedium    = KickText.Section,
    bodyMedium     = KickText.Body,
    labelSmall     = KickText.Tab,
)
```

## 3. Signature Components

### Watch Page (video + streamer bar + chat)

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
fun KickWatchPage(
    videoLayer: @Composable () -> Unit,
    streamer: String,
    category: String,
    viewers: String,
    messages: List<ChatMessage>,
) {
    Column(Modifier.fillMaxSize().background(KickColors.Canvas)) {
        Box(Modifier.fillMaxWidth().aspectRatio(16f / 9f)) {
            videoLayer()
            // LIVE pill
            Row(
                Modifier.align(Alignment.TopStart).padding(12.dp)
                    .clip(RoundedCornerShape(5.dp)).background(KickColors.Live)
                    .padding(horizontal = 9.dp, vertical = 4.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(6.dp),
            ) {
                Box(Modifier.size(6.dp).clip(RoundedCornerShape(50)).background(Color.White))
                Text("LIVE", style = KickText.Badge, color = Color.White)
            }
            // Viewer chip
            Row(
                Modifier.align(Alignment.TopEnd).padding(12.dp)
                    .clip(RoundedCornerShape(5.dp)).background(Color.Black.copy(alpha = 0.55f))
                    .padding(horizontal = 9.dp, vertical = 4.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(5.dp),
            ) {
                Icon(Icons.Filled.Visibility, null, tint = Color.White, modifier = Modifier.size(11.dp))
                Text(viewers, style = KickText.Viewers, color = Color.White)
            }
        }
        StreamerBar(streamer, category, viewers)
        ChatPanel(messages, Modifier.weight(1f))
    }
}
```

### Streamer Bar

```kotlin
import androidx.compose.animation.core.Spring
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.border
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun StreamerBar(name: String, category: String, viewers: String) {
    var following by remember { mutableStateOf(false) }
    val scale by animateFloatAsState(
        if (following) 1.06f else 1f,
        spring(dampingRatio = Spring.DampingRatioMediumBouncy), label = "followScale",
    )
    val haptics = LocalHapticFeedback.current

    Row(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 14.dp, vertical = 12.dp)
            .drawBottomDivider(KickColors.Divider),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box(
            Modifier.size(40.dp).clip(RoundedCornerShape(50))
                .background(KickColors.Green)
                .border(2.dp, KickColors.Green, RoundedCornerShape(50)),
        )
        Column(Modifier.weight(1f)) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                Text(name, style = KickText.Streamer, color = KickColors.TextPrimary)
                Icon(Icons.Filled.Verified, null, tint = KickColors.Green, modifier = Modifier.size(13.dp))
            }
            Text("$category · $viewers watching", style = KickText.Meta.copy(fontSize = 12.sp),
                color = KickColors.TextSecondary, maxLines = 1)
        }
        Button(
            onClick = {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                following = !following
            },
            shape = RoundedCornerShape(6.dp),
            colors = ButtonDefaults.buttonColors(
                containerColor = if (following) KickColors.Surface2 else KickColors.Green,
                contentColor   = if (following) KickColors.TextPrimary else KickColors.OnGreen,
            ),
            border = if (following) BorderStroke(1.dp, KickColors.Surface3) else null,
            contentPadding = PaddingValues(horizontal = 16.dp, vertical = 8.dp),
            modifier = Modifier.scale(scale),
        ) {
            Text(if (following) "Following" else "Follow", style = KickText.Button.copy(fontSize = 12.sp))
        }
    }
}
```

### Live Chat Panel + Message

```kotlin
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.foundation.lazy.rememberLazyListState
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.foundation.layout.FlowRow
import coil.compose.AsyncImage

sealed interface Segment {
    data class Txt(val value: String) : Segment
    data class Emote(val url: String) : Segment
}
data class ChatMessage(val id: String, val role: ChatRole, val username: String,
                        val segments: List<Segment>, val mentionsMe: Boolean)

@Composable
fun ChatPanel(messages: List<ChatMessage>, modifier: Modifier = Modifier) {
    val listState = rememberLazyListState()
    var draft by remember { mutableStateOf("") }

    LaunchedEffect(messages.size) {
        if (messages.isNotEmpty()) listState.animateScrollToItem(messages.lastIndex)
    }

    Column(modifier.background(KickColors.Canvas)) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 14.dp).padding(top = 10.dp, bottom = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text("STREAM CHAT", style = KickText.Badge.copy(fontSize = 12.sp), color = KickColors.TextSecondary)
            Spacer(Modifier.weight(1f))
            Icon(Icons.Filled.Settings, "Chat settings", tint = KickColors.TextSecondary, modifier = Modifier.size(16.dp))
        }

        LazyColumn(
            state = listState,
            modifier = Modifier.weight(1f),
            contentPadding = PaddingValues(horizontal = 14.dp, vertical = 4.dp),
            verticalArrangement = Arrangement.spacedBy(9.dp),
        ) {
            items(messages, key = { it.id }) { ChatRow(it) }
        }

        Row(
            Modifier
                .fillMaxWidth()
                .drawTopDivider(KickColors.Divider)
                .padding(horizontal = 12.dp, vertical = 10.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Box(
                Modifier.weight(1f).height(38.dp).clip(RoundedCornerShape(8.dp))
                    .background(KickColors.Surface2).padding(horizontal = 12.dp),
                contentAlignment = Alignment.CenterStart,
            ) {
                BasicTextField(
                    value = draft, onValueChange = { draft = it },
                    textStyle = KickText.ChatMsg.copy(color = Color.White),
                    decorationBox = { inner ->
                        if (draft.isEmpty()) Text("Send a message", style = KickText.ChatMsg, color = KickColors.TextTertiary)
                        inner()
                    },
                )
            }
            Box(
                Modifier.size(38.dp).clip(RoundedCornerShape(8.dp))
                    .background(if (draft.isBlank()) KickColors.Surface2 else KickColors.Green),
                contentAlignment = Alignment.Center,
            ) {
                Icon(Icons.AutoMirrored.Filled.Send, "Send",
                    tint = if (draft.isBlank()) KickColors.TextTertiary else KickColors.OnGreen,
                    modifier = Modifier.size(16.dp))
            }
        }
    }
}

@Composable
fun ChatRow(message: ChatMessage) {
    val badge = message.role.badge()
    val userColor = message.role.userColor()
    FlowRow(
        Modifier
            .then(
                if (message.mentionsMe)
                    Modifier
                        .clip(RoundedCornerShape(4.dp))
                        .background(KickColors.Green.copy(alpha = 0.12f))
                        .drawStartAccent(KickColors.Green)
                        .padding(horizontal = 6.dp, vertical = 4.dp)
                else Modifier
            ),
        verticalArrangement = Arrangement.Center,
        horizontalArrangement = Arrangement.spacedBy(2.dp),
    ) {
        if (badge != null) {
            Box(
                Modifier.clip(RoundedCornerShape(3.dp)).background(badge.bg)
                    .padding(horizontal = 4.dp, vertical = 1.dp),
            ) { Text(badge.text, style = KickText.Badge.copy(fontSize = 9.sp), color = badge.fg) }
            Spacer(Modifier.width(4.dp))
        }
        Text("${message.username}: ", style = KickText.ChatUser, color = userColor)
        message.segments.forEach { seg ->
            when (seg) {
                is Segment.Txt -> Text(seg.value, style = KickText.ChatMsg, color = KickColors.ChatBody)
                is Segment.Emote -> AsyncImage(
                    model = seg.url, contentDescription = "emote",
                    modifier = Modifier.size(18.dp).clip(RoundedCornerShape(3.dp)),
                )
            }
        }
    }
}

// Hairline helpers (drawWithContent rects) — bottom/top/start accents
fun Modifier.drawBottomDivider(c: Color) = drawWithContent { drawContent(); drawRect(c,
    topLeft = androidx.compose.ui.geometry.Offset(0f, size.height - 0.5.dp.toPx()),
    size = androidx.compose.ui.geometry.Size(size.width, 0.5.dp.toPx())) }
fun Modifier.drawTopDivider(c: Color) = drawWithContent { drawContent(); drawRect(c,
    size = androidx.compose.ui.geometry.Size(size.width, 0.5.dp.toPx())) }
fun Modifier.drawStartAccent(c: Color) = drawWithContent { drawContent(); drawRect(c,
    size = androidx.compose.ui.geometry.Size(2.dp.toPx(), size.height)) }
```

## 4. Navigation

Kick has a 5-tab bottom strip; active is **green** with no Material tint pill.

```kotlin
@Composable
fun KickBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = KickColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"      to Icons.Filled.Home,
            "Browse"    to Icons.Filled.Search,
            "Following" to Icons.Filled.Sensors,   // "live radio" feel
            "Clips"     to Icons.Filled.Movie,
            "Profile"   to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = KickText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = KickColors.Green,
                    selectedTextColor   = KickColors.Green,
                    unselectedIconColor = KickColors.TextTertiary,
                    unselectedTextColor = KickColors.TextTertiary,
                    indicatorColor      = Color.Transparent,  // no Material pill
                ),
            )
        }
    }
}
```

## 5. Motion

Kick motion is fast and minimal — the chat must stay legible while moving quickly; 120–300ms.

| Moment | Compose recipe |
|--------|----------------|
| New chat message | item `enter = fadeIn(tween(120)) + slideInVertically { it / 4 }`; `LazyColumn.animateScrollToItem(last)` unless scrolled up |
| Follow → Following | `animateFloatAsState` scale spring to 1.06→1; container color swap; soft haptic |
| Live pill pulse | `rememberInfiniteTransition` alpha 0.6↔1.0 `tween(1500)` reverse |
| Player controls | `AnimatedVisibility` fade `tween(200)`; auto-hide after 3s idle |
| Stream join | `AnimatedContent` cross-fade into watch page `tween(300)` |
| Card press | `animateFloatAsState` scale 1→0.98 `tween(120)` on press |
| Tab switch | instant content swap; icon color `animateColorAsState` to Green `tween(200)` |
| "↓ More messages" pill | `AnimatedVisibility` slide+fade; tap → `animateScrollToItem(last)` |

```kotlin
// New chat message
LaunchedEffect(messages.size) {
    if (atBottom) listState.animateScrollToItem(messages.lastIndex)
}
// each row: Modifier.animateItemPlacement() + AnimatedVisibility(enter = fadeIn(tween(120)) + slideInVertically())
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on Follow/Subscribe confirm and chat send; a lighter `HapticFeedbackConstants.CLOCK_TICK` for tab switches.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Browse (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Following (tab) | `circle.circle.fill` | `Icons.Filled.Sensors` |
| Clips (tab) | `film.fill` | `Icons.Filled.Movie` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Verified check | `checkmark.seal.fill` | `Icons.Filled.Verified` |
| Live (eye) | `eye.fill` | `Icons.Filled.Visibility` |
| Play / pause | `play.fill` / `pause.fill` | `Icons.Filled.PlayArrow` / `Icons.Filled.Pause` |
| Volume | `speaker.wave.2.fill` / `speaker.slash.fill` | `Icons.Filled.VolumeUp` / `Icons.Filled.VolumeOff` |
| Fullscreen | `arrow.up.left.and.arrow.down.right` | `Icons.Filled.Fullscreen` |
| Chat send | `paperplane.fill` | `Icons.AutoMirrored.Filled.Send` |
| Chat settings | `gearshape` | `Icons.Filled.Settings` |
| Emote picker | `face.smiling` | `Icons.Filled.EmojiEmotions` |
| Share / clip | `square.and.arrow.up` / `scissors` | `Icons.Filled.Share` / `Icons.Filled.ContentCut` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Subscribe | `star.fill` / `crown.fill` | `Icons.Filled.Star` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24**, `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; force light-content system bars (always dark app). The video draws under the status bar; the chat input dock + bottom bar respect the navigation-bar inset; use `Modifier.imePadding()` so the chat input rises above the keyboard.
- **Dark-only**: never call `dynamicColorScheme()` and never build a light scheme — Kick's near-black + green identity is fixed regardless of wallpaper.
- **Canvas is `#0E0E10`, never `#000000`**: `#53FC18` on true black vibrates/strobes for some users — keep the slight lift (an accessibility decision, not only aesthetic).
- **Chat rendering**: render rows as `FlowRow` of views (badge = small `Box`, emote = Coil `AsyncImage` 18.dp) — not one concatenated string — so badges/emotes lay out. Cap retained messages (~200) and use a `LazyColumn` with stable keys for a fast chat.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, body, descriptions, streamer name; pin LIVE/role badges, viewer counts, and 10sp tab labels via `dp`-derived sizes or a fixed-`fontScale` `LocalDensity`; chat text may scale to L.
- **Touch targets**: Material guidance is 48.dp. Give the 38.dp send button and small controls a 48.dp hit area; chat rows are full-width long-pressable; Follow/Subscribe ≥44.dp; tab items full `NavigationBarItem` targets.
- **TalkBack**: chat row `contentDescription` must speak the role ("Moderator {username} says: {message}") — role color is never the only cue and is always paired with the badge chip (never `clearAndSetSemantics {}` the badge away); emote `contentDescription` = emote name; @-mention rows prefix "Mentions you".
- **Contrast**: white/`#E4E4E9` on `#0E0E10`/`#161618` passes WCAG AA; `#A6A6AD` on the canvas passes AA at 13sp+; `#0E0E10` on `#53FC18` is very high contrast for the green CTA.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the live-pill pulse and chat slide-in (plain crossfade); keep auto-scroll but make it a `scrollToItem` (instant).
- **Performance**: a fast live chat appends many messages/second — throttle list updates, recycle aggressively, and avoid recomposing the whole list (stable keys + `derivedStateOf` for the at-bottom check).
