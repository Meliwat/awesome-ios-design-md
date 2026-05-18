# KakaoTalk (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports KakaoTalk's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, an M3 `Typography`, paste-ready `@Composable`s for the rounded-square avatar, the message bubble with its iconic side-docked unread mark, the friends roster, the gifticon card, plus navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Kakao Yellow `#FEE500` outbound bubbles on Kakao Brown text, the side-docked yellow unread "N", rounded-square avatars, the blue-gray chat backdrop, Kakao Friends emoticon stickers, gifticon cards) while making everything idiomatic Android — a `NavigationBar` instead of a UITabBar, `ModalBottomSheet` for the action menu, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars, emoticons, and gifticon images.

## 1. Color Tokens

```kotlin
// ui/theme/KTColors.kt
import androidx.compose.ui.graphics.Color

object KTColors {
    // Brand
    val Yellow        = Color(0xFFFEE500)
    val YellowPressed = Color(0xFFE6CF00)
    val Brown         = Color(0xFF3C1E1E)
    val BrownSoft     = Color(0xFF5A3A3A)

    // Canvas & Surfaces (Light)
    val Canvas       = Color(0xFFFFFFFF)
    val ChatBgLight  = Color(0xFFB2C7DA)
    val SurfaceGray  = Color(0xFFF5F5F5)
    val InboundLight = Color(0xFFFFFFFF)
    val DividerLight = Color(0xFFE6E6E6)

    // Canvas & Surfaces (Dark) — NOT pure black
    val DarkCanvas   = Color(0xFF1A1A1A)
    val ChatBgDark   = Color(0xFF1E2A33)
    val DarkSurface1 = Color(0xFF242424)
    val InboundDark  = Color(0xFF2E2E2E)
    val DarkDivider  = Color(0xFF333333)

    // Text
    val TextPrimary       = Color(0xFF191919)
    val TextSecondary     = Color(0xFF6A6A6A)
    val TextTertiary      = Color(0xFF9A9A9A)
    val DarkTextPrimary   = Color(0xFFEDEDED)
    val DarkTextSecondary = Color(0xFF9A9A9A)
    val OnYellow          = Color(0xFF3C1E1E)

    // Semantic
    val Unread  = Color(0xFFFEE500)
    val Link    = Color(0xFF4B9BFF)
    val Success = Color(0xFF2ECC71)
    val Error   = Color(0xFFF15E6C)
    val Badge   = Color(0xFFFF3B30)
}

// Avatar generated gradients, assigned per friend id
val ktAvatarGradients: List<List<Color>> = listOf(
    listOf(Color(0xFFFFB13C), Color(0xFFFF8E53)), // warm
    listOf(Color(0xFF6DC0F5), Color(0xFF4B9BFF)), // blue
    listOf(Color(0xFFA3D977), Color(0xFF6BBF59)), // green
    listOf(Color(0xFFC792EA), Color(0xFF9B6FD6)), // purple
    listOf(Color(0xFFFEE500), Color(0xFFE6CF00)), // kakao yellow (self / channel)
)
fun ktGradientForId(id: Int): List<Color> =
    ktAvatarGradients[Math.floorMod(id, ktAvatarGradients.size)]
```

Wire it into both schemes. KakaoTalk is light-first; the dark scheme uses `#1A1A1A` (never true black) and a `#1E2A33` chat backdrop. The yellow outbound bubble holds in both.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val KTLight = lightColorScheme(
    primary        = KTColors.Yellow,
    onPrimary      = KTColors.Brown,
    background     = KTColors.Canvas,
    onBackground   = KTColors.TextPrimary,
    surface        = KTColors.SurfaceGray,
    onSurface      = KTColors.TextPrimary,
    surfaceVariant = KTColors.InboundLight,
    outline        = KTColors.DividerLight,
    error          = KTColors.Error,
)

private val KTDark = darkColorScheme(
    primary        = KTColors.Yellow,            // brand yellow holds on dark
    onPrimary      = KTColors.Brown,
    background     = KTColors.DarkCanvas,
    onBackground   = KTColors.DarkTextPrimary,
    surface        = KTColors.DarkSurface1,
    onSurface      = KTColors.DarkTextPrimary,
    surfaceVariant = KTColors.InboundDark,
    outline        = KTColors.DarkDivider,
    error          = KTColors.Error,
)

@Composable
fun KTTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) KTDark else KTLight,
    typography = KTTypography,
    content = content,
)
```

## 2. Typography (M3)

KakaoTalk uses the platform system faces. On Android that's Roboto for Latin and the system Korean face (Noto Sans CJK KR) for Hangul — do not bundle a decorative face. Big titles go `FontWeight.Black` (KakaoBig feel). `sp` honors the user's font scale; keep generous line heights for Hangul.

```kotlin
// ui/theme/KTType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

object KTText {
    val LargeTitle  = TextStyle(FontFamily.Default, fontWeight = FontWeight.Black,    fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val Screen      = TextStyle(FontFamily.Default, fontWeight = FontWeight.Bold,     fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val NavTitle    = TextStyle(FontFamily.Default, fontWeight = FontWeight.Black,    fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.3).sp)
    val ProfileName = TextStyle(FontFamily.Default, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 21.sp)
    val Section     = TextStyle(FontFamily.Default, fontWeight = FontWeight.Bold,     fontSize = 18.sp, lineHeight = 23.sp)
    val Body        = TextStyle(FontFamily.Default, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp) // Hangul-friendly
    val RowTitle    = TextStyle(FontFamily.Default, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Status      = TextStyle(FontFamily.Default, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Sender      = TextStyle(FontFamily.Default, fontWeight = FontWeight.Medium,   fontSize = 12.sp, lineHeight = 14.sp)
    val UnreadMark  = TextStyle(FontFamily.Default, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 11.sp)
    val BubbleTime  = TextStyle(FontFamily.Default, fontWeight = FontWeight.Normal,   fontSize = 10.sp, lineHeight = 10.sp)
    val Tab         = TextStyle(FontFamily.Default, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 10.sp)
    val TabBadge    = TextStyle(FontFamily.Default, fontWeight = FontWeight.Bold,     fontSize = 10.sp, lineHeight = 10.sp)
    val Button      = TextStyle(FontFamily.Default, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 16.sp)
}

val KTTypography = Typography(
    headlineLarge = KTText.LargeTitle,
    titleLarge    = KTText.NavTitle,
    titleMedium   = KTText.Section,
    bodyMedium    = KTText.Body,
    labelSmall    = KTText.Tab,
)
```

## 3. Signature Components

### Rounded-Square Avatar (used everywhere — never a circle)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun KTAvatar(gradient: List<Color>, initials: String, size: Dp = 44.dp) {
    Box(
        Modifier
            .size(size)
            .clip(RoundedCornerShape(size * 0.36f))   // rounded square, NOT a circle
            .background(Brush.linearGradient(gradient)),
        contentAlignment = Alignment.Center,
    ) {
        Text(initials, color = Color.White,
            fontSize = (size.value * 0.36f).sp, fontWeight = FontWeight.Bold)
    }
}
```

### Message Bubble + iconic side-docked unread mark

```kotlin
@Composable
fun KTMessageBubble(
    text: String,
    outbound: Boolean,
    time: String = "",
    unread: Int = 0,        // recipients who haven't read (outbound only)
    dark: Boolean = false,
) {
    val bg = if (outbound) KTColors.Yellow else if (dark) KTColors.InboundDark else KTColors.InboundLight
    val fg = if (outbound) KTColors.Brown else if (dark) KTColors.DarkTextPrimary else KTColors.TextPrimary
    val metaColor = if (dark) Color.White.copy(alpha = 0.5f) else KTColors.TextSecondary
    val shape = RoundedCornerShape(
        topStart = if (outbound) 14.dp else 4.dp,
        topEnd = if (outbound) 4.dp else 14.dp,
        bottomStart = 14.dp, bottomEnd = 14.dp,
    )

    Row(
        Modifier.widthIn(max = 270.dp),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(5.dp),
    ) {
        val meta = @Composable {
            Column(horizontalAlignment = if (outbound) Alignment.End else Alignment.Start) {
                if (outbound && unread > 0) Text("$unread", style = KTText.UnreadMark, color = KTColors.Unread)
                if (time.isNotEmpty()) Text(time, style = KTText.BubbleTime, color = metaColor)
            }
        }
        if (outbound) meta()   // meta on the INNER (leading) side of an outbound bubble
        Box(Modifier.clip(shape).background(bg).padding(horizontal = 12.dp, vertical = 9.dp)) {
            Text(text, style = KTText.Body.copy(fontWeight = if (outbound) FontWeight.Medium else FontWeight.Normal), color = fg)
        }
        if (!outbound) meta()
    }
}
```

### Inbound Sender Row (rounded-square avatar + name above a run)

```kotlin
@Composable
fun KTInboundRow(
    senderName: String,
    gradient: List<Color>,
    initials: String,
    bubbles: List<Pair<String, String>>,   // (text, time)
    dark: Boolean = false,
) {
    Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
        KTAvatar(gradient, initials, 36.dp)
        Column(verticalArrangement = Arrangement.spacedBy(3.dp)) {
            Text(
                senderName, style = KTText.Sender,
                color = if (dark) Color.White.copy(alpha = 0.78f) else KTColors.TextSecondary,
                modifier = Modifier.padding(start = 2.dp),
            )
            bubbles.forEach { (t, time) -> KTMessageBubble(t, outbound = false, time = time, dark = dark) }
        }
    }
}
```

### Friend Row (with optional now-playing badge)

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.MusicNote
import androidx.compose.material3.Icon

@Composable
fun KTFriendRow(
    name: String,
    status: String,
    nowPlaying: String?,
    gradient: List<Color>,
    initials: String,
) {
    Row(
        Modifier.fillMaxWidth().height(64.dp).padding(horizontal = 18.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        KTAvatar(gradient, initials, 44.dp)
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(name, style = KTText.RowTitle, color = KTColors.TextPrimary)
            if (nowPlaying != null) {
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    Icon(Icons.Filled.MusicNote, null, tint = KTColors.TextTertiary, modifier = Modifier.size(11.dp))
                    Text(nowPlaying, style = KTText.Status, color = KTColors.TextTertiary, maxLines = 1)
                }
            } else {
                Text(status, style = KTText.Status, color = KTColors.TextSecondary, maxLines = 1)
            }
        }
    }
}
```

### Gifticon Card

```kotlin
import androidx.compose.material.icons.filled.CardGiftcard
import androidx.compose.foundation.clickable

@Composable
fun KTGifticonCard(
    brand: String,
    product: String,
    isRecipient: Boolean,
    onUse: () -> Unit,
    dark: Boolean = false,
) {
    Column(
        Modifier
            .width(188.dp)
            .clip(RoundedCornerShape(16.dp))
            .background(if (dark) KTColors.DarkSurface1 else KTColors.Canvas)
    ) {
        Box(
            Modifier.fillMaxWidth().height(120.dp)
                .background(Brush.linearGradient(listOf(Color(0xFFFFE08A), Color(0xFFFEC84B)))),
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.CardGiftcard, null, tint = KTColors.Brown, modifier = Modifier.size(42.dp)) }

        Column(Modifier.padding(12.dp), verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(brand, style = KTText.BubbleTime.copy(fontSize = 11.sp), color = KTColors.TextTertiary)
            Text(product, fontSize = 13.sp, fontWeight = FontWeight.Bold,
                color = if (dark) KTColors.DarkTextPrimary else KTColors.TextPrimary)
            if (isRecipient) {
                Box(
                    Modifier.fillMaxWidth().clip(RoundedCornerShape(50)).background(KTColors.Yellow)
                        .clickable { onUse() }.padding(vertical = 8.dp),
                    contentAlignment = Alignment.Center,
                ) { Text("사용하기", fontSize = 13.sp, fontWeight = FontWeight.SemiBold, color = KTColors.Brown) }
            }
        }
    }
}
```

### Composer

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material.icons.outlined.SentimentSatisfied
import androidx.compose.material3.IconButton
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.hapticfeedback.HapticFeedbackType

@Composable
fun KTComposer(
    value: String,
    onValueChange: (String) -> Unit,
    onMenu: () -> Unit,
    onEmoticon: () -> Unit,
    onSend: () -> Unit,
    dark: Boolean = false,
) {
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier.fillMaxWidth()
            .background(if (dark) KTColors.DarkCanvas else KTColors.Canvas)
            .padding(horizontal = 14.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        IconButton(onClick = onMenu) {
            Icon(Icons.Filled.Add, "Menu", tint = KTColors.TextSecondary, modifier = Modifier.size(24.dp))
        }
        Row(
            Modifier.weight(1f).heightIn(min = 38.dp).clip(RoundedCornerShape(50))
                .background(if (dark) KTColors.InboundDark else KTColors.SurfaceGray)
                .padding(horizontal = 16.dp, vertical = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Box(Modifier.weight(1f)) {
                BasicTextField(
                    value = value, onValueChange = onValueChange,
                    textStyle = KTText.Body.copy(color = if (dark) KTColors.DarkTextPrimary else KTColors.TextPrimary),
                )
            }
            if (value.isNotBlank()) {
                IconButton(onClick = {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove); onSend()
                }, modifier = Modifier.size(24.dp)) {
                    Icon(Icons.Filled.ArrowUpward, "Send", tint = KTColors.Brown,
                        modifier = Modifier.size(20.dp).clip(RoundedCornerShape(50)).background(KTColors.Yellow))
                }
            }
        }
        IconButton(onClick = onEmoticon) {
            Icon(Icons.Outlined.SentimentSatisfied, "Emoticons", tint = KTColors.TextSecondary, modifier = Modifier.size(24.dp))
        }
    }
}
```

## 4. Navigation

KakaoTalk is a 5-tab super-app; on Android model it as a `NavigationBar`. Active is the primary text color with a filled icon — **no** Material tint pill, **no** yellow tint.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun KTBottomBar(selected: Int, onSelect: (Int) -> Unit, chatUnread: Int = 0) {
    NavigationBar(containerColor = KTColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            Triple("Friends",  Icons.Filled.People,    0),
            Triple("Chats",    Icons.Filled.Chat,      chatUnread),
            Triple("Open",     Icons.Filled.Public,    0),
            Triple("Shopping", Icons.Filled.ShoppingBag, 0),
            Triple("More",     Icons.Filled.Menu,      0),
        )
        items.forEachIndexed { i, (label, icon, badge) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (badge > 0) {
                        BadgedBox(badge = { Badge(containerColor = KTColors.Badge) { Text("$badge", style = KTText.TabBadge, color = Color.White) } }) {
                            Icon(icon, label, modifier = Modifier.size(24.dp))
                        }
                    } else Icon(icon, label, modifier = Modifier.size(24.dp))
                },
                label = { Text(label, style = KTText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = KTColors.TextPrimary,  // primary text, NOT yellow
                    selectedTextColor   = KTColors.TextPrimary,
                    unselectedIconColor = Color(0xFF777777),
                    unselectedTextColor = Color(0xFF777777),
                    indicatorColor      = Color.Transparent,
                ),
            )
        }
    }
}
```

The Friends list is a `LazyColumn` of `KTFriendRow`s with section headers ("Friends · N", "Channels") and a my-profile banner pinned at the top. The chat screen sits over a `Box` background of `ChatBgDark`/`ChatBgLight`. The `+` composer menu is a `ModalBottomSheet` action grid (album, camera, gifticon, pay/송금, location, schedule, voice); the emoticon keyboard is another `ModalBottomSheet` with character tabs.

## 5. Motion

KakaoTalk motion is friendly but restrained — short ease-outs, a soft pop for stickers.

| Moment | Compose recipe |
|--------|----------------|
| Message send | `AnimatedVisibility` `slideInVertically { it } + fadeIn(tween(200))` from the composer |
| Unread countdown | re-key the `Text` and `Crossfade(targetState = unread, tween(200))` |
| New inbound message | `fadeIn(tween(180))` + 6dp `slideInVertically` |
| Emoticon send | sticker `animateFloatAsState` 0.7 → 1.0 `spring(MediumBouncy, StiffnessMediumLow)`; then play the animated loop |
| Action sheet / emoticon keyboard | `ModalBottomSheet` default ~280ms slide-up + scrim |
| Tab switch | instant content swap; icon tint `animateColorAsState(tween(120))`; red badge `scaleIn` |
| Gifticon arrival | card `slideInVertically + fadeIn`; subtle sparkle accent first time |
| Pull-to-refresh | `PullToRefreshContainer` with a Kakao-yellow indicator |

```kotlin
// Emoticon sticker pop — the friendly KakaoTalk motion
val scale by animateFloatAsState(
    targetValue = if (visible) 1f else 0.7f,
    animationSpec = spring(dampingRatio = Spring.DampingRatioMediumBouncy, stiffness = Spring.StiffnessMediumLow),
    label = "stickerPop",
)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for the light tick on send; `HapticFeedbackType.LongPress` for the soft impact on emoticon send. Tab changes use a selection tick via `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`.

## 6. Icons

KakaoTalk's iconography is system-standard; the closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Friends (tab) | `person.2.fill` | `Icons.Filled.People` |
| Chats (tab) | `bubble.left.fill` | `Icons.Filled.Chat` |
| Open Chat (tab) | `globe` | `Icons.Filled.Public` |
| Shopping (tab) | `bag.fill` | `Icons.Filled.ShoppingBag` |
| More (tab) | `line.3.horizontal` | `Icons.Filled.Menu` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Add friend | `person.badge.plus` | `Icons.Filled.PersonAddAlt` |
| Settings / menu | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Composer menu (+) | `plus` | `Icons.Filled.Add` |
| Emoticon keyboard | `face.smiling` | `Icons.Outlined.SentimentSatisfied` |
| Send | `arrow.up.circle.fill` | `Icons.Filled.ArrowUpward` |
| Now-playing badge | `music.note` | `Icons.Filled.MusicNote` |
| Gifticon | `gift.fill` | `Icons.Filled.CardGiftcard` |
| Album / photo | `photo.on.rectangle` | `Icons.Filled.Image` |
| Camera | `camera.fill` | `Icons.Filled.PhotoCamera` |
| Pay / 송금 | `wonsign.circle.fill` | `Icons.Filled.AccountBalanceWallet` |
| Location | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Schedule | `calendar` | `Icons.Filled.Event` |
| Voice | `mic.fill` | `Icons.Filled.Mic` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `ModalBottomSheet` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`. Nav bars draw under the status bar — apply `Modifier.statusBarsPadding()` to their content; over the chat thread set status-icon contrast to match the backdrop (dark icons on the light blue-gray, light icons on `#1E2A33`). The composer must sit above the IME — use `Modifier.imePadding()`.
- **No brand font**: KakaoTalk is system-font on purpose — use `FontFamily.Default` (Roboto + Noto Sans CJK KR for Hangul). Keep `lineHeight` generous (`fontSize × ~1.33`) on body/status so Hangul breathes; never tighten below ~1.3.
- **Pin layout-sensitive text**: keep tab labels (10sp), the unread mark (11sp), bubble times (10sp), and tab badges (10sp) fixed by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` — bubble run-grouping is alignment-sensitive.
- **TalkBack**: label the unread mark with its meaning ("{n} people have not read this message"); label outbound bubbles "You said: {text}"; inbound "{sender} said: {text}"; label the gifticon "{brand} {product} gift, double-tap to use"; expose the `+` action sheet + emoticon keyboard as labeled buttons.
- **Touch targets**: Material guidance is 48.dp — give the 24dp tab/composer icons a 48.dp hit area via padding; friend rows are 64.dp (full-row tappable); the gifticon "Use" pill is ≥ 36.dp.
- **Contrast**: `#3C1E1E` on `#FEE500` passes WCAG AA comfortably; `#6A6A6A` on `#FFFFFF` passes AA for metadata; verify any text drawn over the `#B2C7DA` light thread backdrop.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the emoticon scale-pop and bubble slide-in with a plain `Crossfade`; keep the unread-count crossfade (it conveys state).
- **Dark mode**: invert via the `Dark*` palette — `#1A1A1A` canvas (NOT true black) + `#1E2A33` chat backdrop; `#191919` text becomes `#EDEDED`, inbound bubbles `#2E2E2E`. Outbound bubbles stay Kakao Yellow `#FEE500` with Kakao Brown `#3C1E1E` text regardless of scheme. The gifticon card switches to a `#242424` fill and floating panels gain a 1dp `#333333` border as the dark elevation cue. Do **not** enable Material You `dynamicColorScheme()` — KakaoTalk's yellow-and-brown identity must hold regardless of wallpaper. Active tab is the primary text color, never a yellow tint.
