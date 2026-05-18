# Zalo (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Zalo's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the asymmetric chat bubble (custom `Shape`), the chat thread, the mini-app launcher grid, and the blue header with motion and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Zalo's single blue anchor, asymmetric clipped-tail bubbles, calm chat backdrop, super-app launcher grid, Be Vietnam Pro typography) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a custom `Shape` for the bubble tail, `LazyVerticalGrid` for the launcher, `sp`/`dp` instead of `pt`. Zalo is huge on Android in Vietnam, so this is a first-class target.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars/media. Light-first; a full dark scheme is provided. No Material You dynamic color — the single Zalo Blue must hold regardless of wallpaper.

## 1. Color Tokens

```kotlin
// ui/theme/ZaloColors.kt
import androidx.compose.ui.graphics.Color

object ZaloColors {
    // Brand (single anchor)
    val Blue        = Color(0xFF0068FF)
    val BluePressed = Color(0xFF0052CC)
    val BlueDeep    = Color(0xFF0047B3)
    val BubbleOut   = Color(0xFFDBEBFF)
    val BubbleOutInk = Color(0xFF14223A)

    // Canvas & Surfaces (Light)
    val Canvas    = Color(0xFFFFFFFF)
    val ChatBg    = Color(0xFFE8ECF1)
    val Surface1  = Color(0xFFF4F5F7)
    val Surface2  = Color(0xFFEBEDF0)
    val Divider   = Color(0xFFE4E6EB)

    // Canvas & Surfaces (Dark)
    val DarkCanvas    = Color(0xFF15171A)
    val DarkChatBg    = Color(0xFF101418)
    val DarkBubbleIn  = Color(0xFF1F2329)
    val DarkBubbleOut = Color(0xFF0A3A7A)
    val DarkDivider   = Color(0xFF262A30)

    // Text
    val Ink           = Color(0xFF1A1A1A)
    val TextSecondary = Color(0xFF6B7280)
    val TextTertiary  = Color(0xFF9AA0AA)
    val TextPrimaryDk = Color(0xFFECEDEF)

    // Semantic
    val Notify  = Color(0xFFF5325B)
    val Success = Color(0xFF18A957)
    val Warning = Color(0xFFFF9500)

    // Mini-app tile colors (contained to the launcher)
    val TileGreen  = Color(0xFF18A957)
    val TileOrange = Color(0xFFFF9500)
    val TileRed    = Color(0xFFF5325B)
    val TileViolet = Color(0xFF7B5CFF)
    val TileCyan   = Color(0xFF00B8D4)
}
```

Wire it into both schemes. Zalo is light-first; the dark scheme keeps the blue brand.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val ZaloLight = lightColorScheme(
    primary        = ZaloColors.Blue,
    onPrimary      = Color.White,
    background     = ZaloColors.Canvas,
    onBackground   = ZaloColors.Ink,
    surface        = ZaloColors.Canvas,
    onSurface      = ZaloColors.Ink,
    surfaceVariant = ZaloColors.Surface1,
    outline        = ZaloColors.Divider,
    error          = ZaloColors.Notify,
)

private val ZaloDark = darkColorScheme(
    primary        = ZaloColors.Blue,
    onPrimary      = Color.White,
    background     = ZaloColors.DarkCanvas,
    onBackground   = ZaloColors.TextPrimaryDk,
    surface        = ZaloColors.DarkCanvas,
    onSurface      = ZaloColors.TextPrimaryDk,
    surfaceVariant = ZaloColors.DarkChatBg,
    outline        = ZaloColors.DarkDivider,
    error          = ZaloColors.Notify,
)

@Composable
fun ZaloTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) ZaloDark else ZaloLight,
    typography = ZaloTypography,
    content = content,
)
```

## 2. Typography

Zalo uses **Be Vietnam Pro** — a sans designed for Vietnamese diacritics (SIL OFL). Drop the TTFs in `res/font/`. Give stacked tone marks (ậ ệ ọ ữ) generous line height; never clip the line box.

```kotlin
// ui/theme/ZaloType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val BeVietnamPro = FontFamily(
    Font(R.font.bevietnampro_regular,   FontWeight.Normal),
    Font(R.font.bevietnampro_medium,    FontWeight.Medium),
    Font(R.font.bevietnampro_semibold,  FontWeight.SemiBold),
    Font(R.font.bevietnampro_bold,      FontWeight.Bold),
    Font(R.font.bevietnampro_extrabold, FontWeight.ExtraBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt -> sp 1:1), generous line heights for diacritics
object ZaloText {
    val Display     = TextStyle(BeVietnamPro, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 40.sp, letterSpacing = (-0.5).sp)
    val ScreenTitle = TextStyle(BeVietnamPro, fontWeight = FontWeight.Bold,      fontSize = 24.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(BeVietnamPro, fontWeight = FontWeight.Bold,      fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val ListTitle   = TextStyle(BeVietnamPro, fontWeight = FontWeight.SemiBold,  fontSize = 17.sp, lineHeight = 23.sp)
    val Body        = TextStyle(BeVietnamPro, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 22.sp) // body + bubble
    val CellTitle   = TextStyle(BeVietnamPro, fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 19.sp)
    val Preview     = TextStyle(BeVietnamPro, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val Button      = TextStyle(BeVietnamPro, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp)
    val Timestamp   = TextStyle(BeVietnamPro, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 14.sp)
    val Tab         = TextStyle(BeVietnamPro, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp)
    val Badge       = TextStyle(BeVietnamPro, fontWeight = FontWeight.Bold,      fontSize = 9.sp,  lineHeight = 9.sp)
    val MiniLabel   = TextStyle(BeVietnamPro, fontWeight = FontWeight.Medium,    fontSize = 11.sp, lineHeight = 14.sp)
}

val ZaloTypography = Typography(
    headlineLarge = ZaloText.Display,
    headlineMedium = ZaloText.ScreenTitle,
    titleLarge    = ZaloText.Section,
    titleMedium   = ZaloText.ListTitle,
    bodyMedium    = ZaloText.Body,
    labelSmall    = ZaloText.Tab,
)
```

## 3. Signature Components

### Asymmetric Bubble Shape

`RoundedCornerShape` takes per-corner radii directly — the tail is just one small bottom corner.

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.unit.dp

fun bubbleShape(outgoing: Boolean) = RoundedCornerShape(
    topStart = 16.dp,
    topEnd = 16.dp,
    bottomStart = if (outgoing) 16.dp else 5.dp, // tail clipped for incoming
    bottomEnd = if (outgoing) 5.dp else 16.dp,   // tail clipped for outgoing
)
```

### Chat Bubble

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Brush

data class Msg(val text: String, val outgoing: Boolean, val time: String, val showAvatar: Boolean)

@Composable
fun ChatBubble(msg: Msg) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 12.dp),
        horizontalArrangement = if (msg.outgoing) Arrangement.End else Arrangement.Start,
        verticalAlignment = Alignment.Bottom,
    ) {
        if (!msg.outgoing) {
            if (msg.showAvatar) {
                Box(
                    Modifier.size(28.dp).clip(CircleShape)
                        .background(Brush.linearGradient(listOf(Color(0xFF4D9FFF), ZaloColors.BlueDeep)))
                )
            } else {
                Spacer(Modifier.width(28.dp))
            }
            Spacer(Modifier.width(8.dp))
        }
        Column(horizontalAlignment = if (msg.outgoing) Alignment.End else Alignment.Start) {
            val shape = bubbleShape(msg.outgoing)
            Box(
                Modifier
                    .widthIn(max = 260.dp)
                    .then(if (msg.outgoing) Modifier else Modifier.shadow(2.dp, shape, spotColor = Color.Black.copy(alpha = 0.06f)))
                    .clip(shape)
                    .background(if (msg.outgoing) ZaloColors.BubbleOut else ZaloColors.Canvas)
                    .padding(horizontal = 13.dp, vertical = 9.dp)
            ) {
                Text(msg.text, style = ZaloText.Body, color = if (msg.outgoing) ZaloColors.BubbleOutInk else ZaloColors.Ink)
            }
            Text(msg.time, style = ZaloText.Timestamp.copy(fontSize = 10.sp), color = ZaloColors.TextTertiary,
                modifier = Modifier.padding(top = 3.dp))
        }
    }
}
```

### Chat Thread

```kotlin
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items

@Composable
fun ChatThread(messages: List<Msg>) {
    LazyColumn(
        Modifier.fillMaxSize().background(ZaloColors.ChatBg),
        contentPadding = PaddingValues(vertical = 14.dp),
        verticalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        item {
            Box(Modifier.fillMaxWidth(), contentAlignment = Alignment.Center) {
                Text(
                    "Hôm nay · Today",
                    style = ZaloText.Timestamp,
                    color = ZaloColors.TextSecondary,
                    modifier = Modifier
                        .clip(RoundedCornerShape(999.dp))
                        .background(Color.White.copy(alpha = 0.7f))
                        .padding(horizontal = 12.dp, vertical = 3.dp),
                )
            }
        }
        items(messages) { ChatBubble(it) }
        item {
            Text("Đã xem · Seen 09:27", style = ZaloText.Timestamp, color = ZaloColors.TextTertiary,
                modifier = Modifier.fillMaxWidth().padding(end = 16.dp, top = 2.dp), textAlign = TextAlign.End)
        }
    }
}
```

### Chat Header (Zalo Blue bar)

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ArrowBack
import androidx.compose.material.icons.filled.Call
import androidx.compose.material.icons.filled.Videocam
import androidx.compose.material3.Icon

@Composable
fun ChatHeader(name: String, presence: String) {
    Row(
        Modifier.fillMaxWidth().background(ZaloColors.Blue).padding(start = 16.dp, end = 16.dp, top = 6.dp, bottom = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Icon(Icons.Filled.ArrowBack, "Back", tint = Color.White, modifier = Modifier.size(22.dp))
        Box(Modifier.size(38.dp).clip(CircleShape).background(Brush.linearGradient(listOf(Color(0xFF4D9FFF), ZaloColors.BlueDeep))))
        Column(Modifier.weight(1f)) {
            Text(name, style = ZaloText.Body.copy(fontWeight = FontWeight.SemiBold), color = Color.White)
            Text(presence, style = ZaloText.Timestamp.copy(fontWeight = FontWeight.Normal), color = Color.White.copy(alpha = 0.85f))
        }
        Icon(Icons.Filled.Call, "Call", tint = Color.White, modifier = Modifier.size(20.dp))
        Icon(Icons.Filled.Videocam, "Video call", tint = Color.White, modifier = Modifier.size(20.dp))
    }
}
```

### Message Composer

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.PhotoCamera
import androidx.compose.material.icons.filled.Send
import androidx.compose.material.icons.outlined.EmojiEmotions
import androidx.compose.runtime.*
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun Composer(onSend: (String) -> Unit) {
    var text by remember { mutableStateOf("") }
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier
            .fillMaxWidth()
            .background(ZaloColors.Canvas)
            .padding(start = 14.dp, end = 14.dp, top = 9.dp, bottom = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Icon(Icons.Outlined.EmojiEmotions, "Sticker", tint = ZaloColors.Blue, modifier = Modifier.size(23.dp))
        Box(
            Modifier
                .weight(1f).height(38.dp)
                .clip(RoundedCornerShape(19.dp))
                .background(ZaloColors.Surface1)
                .padding(horizontal = 16.dp),
            contentAlignment = Alignment.CenterStart,
        ) {
            BasicTextField(
                value = text, onValueChange = { text = it },
                textStyle = ZaloText.Preview.copy(color = ZaloColors.Ink),
                decorationBox = { inner ->
                    if (text.isEmpty()) Text("Tin nhắn", style = ZaloText.Preview, color = ZaloColors.TextTertiary)
                    inner()
                },
            )
        }
        if (text.isEmpty()) {
            Icon(Icons.Filled.PhotoCamera, "Camera", tint = ZaloColors.Blue, modifier = Modifier.size(22.dp))
        } else {
            Box(
                Modifier.size(38.dp).clip(CircleShape).background(ZaloColors.Blue)
                    .clickable {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress) // soft impact analog
                        onSend(text); text = ""
                    },
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Filled.Send, "Send", tint = Color.White, modifier = Modifier.size(16.dp)) }
        }
    }
}
```

### Mini-App Launcher Grid

```kotlin
import androidx.compose.foundation.lazy.grid.GridCells
import androidx.compose.foundation.lazy.grid.LazyVerticalGrid
import androidx.compose.foundation.lazy.grid.items
import androidx.compose.ui.graphics.vector.ImageVector

data class MiniApp(val icon: ImageVector, val tile: Color, val label: String)

@Composable
fun LauncherGrid(apps: List<MiniApp>) {
    LazyVerticalGrid(
        columns = GridCells.Fixed(4),
        contentPadding = PaddingValues(16.dp),
        horizontalArrangement = Arrangement.spacedBy(16.dp),
        verticalArrangement = Arrangement.spacedBy(18.dp),
        modifier = Modifier.background(ZaloColors.Canvas),
    ) {
        items(apps) { a ->
            Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(7.dp)) {
                Box(
                    Modifier.size(52.dp).clip(RoundedCornerShape(15.dp)).background(a.tile),
                    contentAlignment = Alignment.Center,
                ) { Icon(a.icon, a.label, tint = Color.White, modifier = Modifier.size(24.dp)) }
                Text(a.label, style = ZaloText.MiniLabel, color = ZaloColors.Ink, maxLines = 2, textAlign = TextAlign.Center)
            }
        }
    }
}
```

### Chat List Row

```kotlin
@Composable
fun ChatListRow(name: String, preview: String, time: String, unread: Int, online: Boolean) {
    Row(
        Modifier
            .fillMaxWidth().height(72.dp).padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box {
            Box(Modifier.size(48.dp).clip(CircleShape).background(Brush.linearGradient(listOf(Color(0xFF4D9FFF), ZaloColors.BlueDeep))))
            if (online) {
                Box(
                    Modifier.size(10.dp).align(Alignment.BottomEnd).clip(CircleShape)
                        .background(ZaloColors.Success).border(2.dp, Color.White, CircleShape)
                )
            }
        }
        Column(Modifier.weight(1f)) {
            Text(name, style = ZaloText.ListTitle.copy(fontWeight = if (unread > 0) FontWeight.Bold else FontWeight.SemiBold), color = ZaloColors.Ink)
            Text(preview, style = ZaloText.Preview, color = if (unread > 0) ZaloColors.Ink else ZaloColors.TextSecondary, maxLines = 1)
        }
        Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(time, style = ZaloText.Timestamp, color = ZaloColors.TextTertiary)
            if (unread > 0) {
                Box(
                    Modifier.defaultMinSize(minWidth = 16.dp, minHeight = 16.dp).clip(RoundedCornerShape(999.dp))
                        .background(ZaloColors.Notify).padding(horizontal = 5.dp),
                    contentAlignment = Alignment.Center,
                ) { Text("$unread", style = ZaloText.Badge, color = Color.White) }
            }
        }
    }
    HorizontalDivider(color = ZaloColors.Divider, thickness = 0.5.dp, modifier = Modifier.padding(start = 76.dp))
}
```

## 4. Navigation

Zalo's bottom bar has 5 Vietnamese-labeled tabs; active is Zalo Blue (filled icon), no Material tint pill.

```kotlin
@Composable
fun ZaloBottomBar(selected: Int, onSelect: (Int) -> Unit, unread: Int) {
    NavigationBar(containerColor = ZaloColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Tin nhắn" to Icons.Filled.ChatBubble,
            "Danh bạ"  to Icons.Filled.People,
            "Khám phá" to Icons.Filled.GridView,
            "Nhật ký"  to Icons.Filled.Schedule,
            "Cá nhân"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (i == 0 && unread > 0) {
                        BadgedBox(badge = { Badge(containerColor = ZaloColors.Notify) { Text("$unread", style = ZaloText.Badge, color = Color.White) } }) {
                            Icon(icon, label, modifier = Modifier.size(23.dp))
                        }
                    } else {
                        Icon(icon, label, modifier = Modifier.size(23.dp))
                    }
                },
                label = { Text(label, style = ZaloText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = ZaloColors.Blue,
                    selectedTextColor = ZaloColors.Blue,
                    unselectedIconColor = ZaloColors.TextTertiary,
                    unselectedTextColor = ZaloColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Zalo has none
                ),
            )
        }
    }
}
```

The chat top bar is the solid blue `ChatHeader` above; it persists across destinations (keep the blue background on the scaffold top region so there's no color flash on navigation).

## 5. Motion

Zalo motion is functional and quick — it's a dense utility super-app.

| Moment | Compose recipe |
|--------|----------------|
| Outgoing bubble send | `AnimatedVisibility` `scaleIn(initialScale = 0.9f, tween(200)) + fadeIn(tween(200))`; soft haptic |
| Incoming bubble | `AnimatedVisibility` `slideInVertically { it / 4 } + fadeIn` `tween(200)`; typing = 3 dots looped `infiniteRepeatable` |
| Read receipt | `AnimatedVisibility` `fadeIn(tween(150))` under the last outgoing bubble |
| Tab change | instant content swap; icon outline→filled + tint handled by `NavigationBarItem` colors |
| Header transition | Nav3/`NavHost` slide push `tween(300)`; keep the blue scaffold region so the bar doesn't flash |
| Launcher tile press | `Modifier.scale` 1.0 → 0.94 via `animateFloatAsState(tween(100))` on press, springs back |
| Reaction picker | long-press bubble -> emoji row `scaleIn(tween(180)) + fadeIn`; soft haptic |
| Zalo Pay confirm | `Success` checkmark `scaleIn(tween(200))` + snackbar |

```kotlin
// Outgoing bubble — the canonical Zalo send motion
AnimatedVisibility(
    visible = true,
    enter = scaleIn(initialScale = 0.9f, animationSpec = tween(200)) + fadeIn(tween(200)),
) { ChatBubble(msg) }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft send impact and reaction-picker open; `HapticFeedbackConstants.CLOCK_TICK` (via `LocalView`) for tab change; `HapticFeedbackConstants.CONFIRM` for Zalo Pay / friend-add. The typing indicator loops via `rememberInfiniteTransition`.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Tin nhắn (tab) | `message` / `message.fill` | `Icons.Filled.ChatBubble` |
| Danh bạ (tab) | `person.2` / `person.2.fill` | `Icons.Filled.People` |
| Khám phá (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Nhật ký (tab) | `clock` / `clock.fill` | `Icons.Filled.Schedule` |
| Cá nhân (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Voice call | `phone.fill` | `Icons.Filled.Call` |
| Video call | `video.fill` | `Icons.Filled.Videocam` |
| Send | `paperplane.fill` | `Icons.Filled.Send` |
| Sticker / emoji | `face.smiling` | `Icons.Outlined.EmojiEmotions` |
| Camera | `camera.fill` | `Icons.Filled.PhotoCamera` |
| Attachment | `plus` / `paperclip` | `Icons.Filled.AttachFile` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Like (timeline) | `heart.fill` | `Icons.Filled.Favorite` (`#F5325B`) |
| Verified (OA) | `checkmark.seal.fill` | `Icons.Filled.Verified` (`#0068FF`) |
| Settings | `gearshape.fill` | `Icons.Filled.Settings` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; Zalo's audience is overwhelmingly Android in Vietnam — keep `minSdk` low but 24 is comfortable for modern motion). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. Coil `2.6+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the Zalo Blue header should draw under the status bar — keep light-content system-bar icons even in dark mode. The composer and tab bar respect the navigation-bar inset; the thread uses `Modifier.imePadding()` so it scrolls clear of the keyboard.
- **Font scaling**: `sp` honors the user's font scale — keep it on display, titles, body, bubbles, previews. Pin layout-sensitive text (timestamps, 9sp badges, 10sp tab labels, mini-app captions) but **never clip Vietnamese diacritics** — let chat rows and bubbles grow vertically; the generous `lineHeight`s above protect stacked tone marks.
- **TalkBack**: label bubbles "{incoming/outgoing} message: {text}, {time}"; chat rows "{name}, {preview}, {unread} unread messages"; the send button "Send message"; launcher tiles "{label}, opens mini-app"; expose day separators as headings via `Modifier.semantics { heading() }`.
- **Touch targets**: Material guidance is 48.dp — the 20-22dp header/composer icons and 23dp tab icons get 48dp hit areas; chat list rows are 72dp; launcher tiles are 52dp with a 64dp effective hit; the send circle is 38dp + padding.
- **Contrast**: white on `#0068FF` passes AA; `#1A1A1A` on `#FFFFFF` and `#14223A` on `#DBEBFF` pass AA for bubble text; the red `#F5325B` badge uses white text and passes AA.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the bubble scale-in (plain fade) and the reaction-picker zoom; replace the looping typing dots with a static "Đang soạn tin…" label; keep selection states.
- **Dark mode**: light-first; the dark scheme flips the chat backdrop to `#101418`, incoming bubbles to `#1F2329`, outgoing to `#0A3A7A` (white text); the asymmetric clipped tail and the blue header/active tab are preserved; drop the incoming-bubble shadow on dark. Do **not** enable `dynamicColorScheme()` — the single Zalo Blue must hold regardless of wallpaper.
