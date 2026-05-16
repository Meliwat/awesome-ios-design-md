# LINE (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports LINE's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (LINE's periwinkle chat backdrop, pale-green outgoing bubbles, the bubble-less oversized sticker with a bounce-in, the official-account badge, the super-app tab bar) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a custom tail `Shape`, `sp`/`dp` instead of `pt`. (LINE's real Android app is itself Material; this maps the iOS spec onto modern Compose.)

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars and stickers.

## 1. Color Tokens

```kotlin
// ui/theme/LineColors.kt
import androidx.compose.ui.graphics.Color

object LineColors {
    // Chat backdrop & canvas
    val Backdrop    = Color(0xFF8CABD9) // periwinkle (themeable) — NOT white
    val BackdropDk  = Color(0xFF1A2230)
    val Canvas      = Color(0xFFFFFFFF)
    val Surface     = Color(0xFFF7F7F7)
    val Divider     = Color(0xFFE5E5E5)

    // Text
    val TextPrimary   = Color(0xFF000000)
    val TextSecondary = Color(0xFF8C8C8C)
    val TextTertiary  = Color(0xFFB3B3B3)

    // Brand
    val Green        = Color(0xFF06C755)
    val GreenPressed = Color(0xFF05A647)
    val GreenWash    = Color(0xFFF1FBF3)

    // Message
    val Incoming = Color(0xFFFFFFFF)
    val Outgoing = Color(0xFFC5F0B1) // pale chat-green — NOT the brand green

    // Semantic
    val Notif = Color(0xFFFF334B)
    val Link  = Color(0xFF1F8FFF)

    // Dark (system-dark users)
    val DarkCanvas        = Color(0xFF1E1E1E)
    val DarkSurface       = Color(0xFF2A2A2A)
    val DarkIncoming      = Color(0xFF2A2A2A)
    val DarkTextPrimary   = Color(0xFFEDEDED)
    val DarkTextSecondary = Color(0xFF9A9A9A)
}
```

Wire it into Material 3 schemes so ripples, dividers, and stock component colors inherit the brand. LINE is light/themeable-first; provide a dark scheme for system-dark users.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val LineLight = lightColorScheme(
    primary        = LineColors.Green,
    onPrimary      = Color.White,
    background      = LineColors.Canvas,
    onBackground   = LineColors.TextPrimary,
    surface         = LineColors.Canvas,
    onSurface      = LineColors.TextPrimary,
    surfaceVariant = LineColors.Surface,
    outline        = LineColors.Divider,
    error          = LineColors.Notif,
)

private val LineDark = darkColorScheme(
    primary        = LineColors.Green,
    onPrimary      = Color.White,
    background      = LineColors.DarkCanvas,
    onBackground   = LineColors.DarkTextPrimary,
    surface         = LineColors.DarkSurface,
    onSurface      = LineColors.DarkTextPrimary,
    surfaceVariant = LineColors.DarkSurface,
    outline        = Color(0xFF3A3A3A),
    error          = LineColors.Notif,
)

@Composable
fun LineTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) LineDark else LineLight,
        typography  = LineTypography,
        content     = content,
    )
```

## 2. Typography

LINE uses **Noto Sans** at weights 400/700 (no semibold tier). Drop the TTFs in `res/font/`; Noto Sans CJK covers East-Asian glyphs.

```kotlin
// ui/theme/LineType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val NotoSans = FontFamily(
    Font(R.font.noto_sans_regular, FontWeight.Normal), // 400
    Font(R.font.noto_sans_bold,    FontWeight.Bold),   // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object LineText {
    val LargeTitle  = TextStyle(NotoSans, fontWeight = FontWeight.Bold,   fontSize = 24.sp, lineHeight = 29.sp)
    val FriendName  = TextStyle(NotoSans, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 21.sp)
    val ThreadTitle = TextStyle(NotoSans, fontWeight = FontWeight.Bold,   fontSize = 17.sp, lineHeight = 21.sp)
    val MessageBody = TextStyle(NotoSans, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 23.sp) // CJK-friendly 1.45
    val Status      = TextStyle(NotoSans, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val Preview     = TextStyle(NotoSans, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 18.sp)
    val Section     = TextStyle(NotoSans, fontWeight = FontWeight.Bold,   fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Timestamp   = TextStyle(NotoSans, fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 14.sp)
    val ReadLabel   = TextStyle(NotoSans, fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 13.sp)
    val Button      = TextStyle(NotoSans, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 16.sp)
    val ButtonText  = TextStyle(NotoSans, fontWeight = FontWeight.Bold,   fontSize = 15.sp, lineHeight = 15.sp)
    val Tab         = TextStyle(NotoSans, fontWeight = FontWeight.Normal, fontSize = 10.sp, lineHeight = 12.sp)
    val Badge       = TextStyle(NotoSans, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 11.sp)
    val StickerPx   = TextStyle(NotoSans, fontWeight = FontWeight.Bold,   fontSize = 13.sp, lineHeight = 13.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val LineTypography = Typography(
    headlineSmall = LineText.LargeTitle,
    titleLarge    = LineText.ThreadTitle,
    bodyLarge     = LineText.MessageBody,
    titleMedium   = LineText.FriendName,
    labelSmall    = LineText.Tab,
)
```

## 3. Signature Components

### Oversized Bubble-less Sticker (Signature)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.layout.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.scale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun StickerMessage(
    stickerUrl: String,
    time: String,
    isOutgoing: Boolean,
    read: Boolean,
) {
    // bounce-in: 0.6 → 1.08 → 1.0
    val scale = remember { Animatable(0.6f) }
    LaunchedEffect(Unit) {
        scale.animateTo(1.08f, spring(dampingRatio = 0.55f, stiffness = 240f))
        scale.animateTo(1f, spring(dampingRatio = 0.7f))
    }

    Row(
        Modifier.fillMaxWidth().padding(horizontal = 12.dp, vertical = 10.dp), // needs air
        horizontalArrangement = if (isOutgoing) Arrangement.End else Arrangement.Start,
        verticalAlignment = Alignment.Bottom,
    ) {
        @Composable fun Meta() = Column(
            horizontalAlignment = if (isOutgoing) Alignment.End else Alignment.Start,
        ) {
            if (isOutgoing && read) Text("Read", style = LineText.ReadLabel, color = LineColors.TextSecondary)
            Text(time, style = LineText.Timestamp, color = LineColors.TextSecondary)
        }

        if (isOutgoing) { Meta(); Spacer(Modifier.width(6.dp)) }
        // bubble-less, oversized — directly on the backdrop
        AsyncImage(
            model = stickerUrl,
            contentDescription = "Sticker", // give a meaningful label in production
            modifier = Modifier.size(140.dp).scale(scale.value),
        )
        if (!isOutgoing) { Spacer(Modifier.width(6.dp)); Meta() }
    }
}
```

### Text Bubble (on the periwinkle backdrop, with tail notch)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.material3.Text

@Composable
fun TextBubble(
    text: String,
    time: String,
    isOutgoing: Boolean,
    read: Boolean,
) {
    // 16dp radius with a 4dp notch on the avatar-side top corner
    val shape = RoundedCornerShape(
        topStart = if (isOutgoing) 16.dp else 4.dp,
        topEnd   = if (isOutgoing) 4.dp else 16.dp,
        bottomStart = 16.dp, bottomEnd = 16.dp,
    )
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 8.dp, vertical = 2.dp),
        horizontalArrangement = if (isOutgoing) Arrangement.End else Arrangement.Start,
        verticalAlignment = Alignment.Bottom,
    ) {
        @Composable fun Meta() = Column(
            horizontalAlignment = if (isOutgoing) Alignment.End else Alignment.Start,
        ) {
            if (isOutgoing && read) Text("Read", style = LineText.ReadLabel, color = LineColors.TextSecondary)
            Text(time, style = LineText.Timestamp, color = LineColors.TextSecondary)
        }
        if (isOutgoing) { Meta(); Spacer(Modifier.width(6.dp)) }
        Text(
            text,
            style = LineText.MessageBody,
            color = LineColors.TextPrimary,
            modifier = Modifier
                .widthIn(max = 260.dp)
                .shadow(1.dp, shape, spotColor = Color.Black.copy(alpha = 0.06f)) // lift off periwinkle
                .clip(shape)
                .background(if (isOutgoing) LineColors.Outgoing else LineColors.Incoming)
                .padding(horizontal = 12.dp, vertical = 9.dp),
        )
        if (!isOutgoing) { Spacer(Modifier.width(6.dp)); Meta() }
    }
}
```

### Friend List Row (official-account badge)

```kotlin
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Verified
import androidx.compose.material3.Icon

@Composable
fun FriendRow(
    name: String,
    status: String,
    time: String,
    official: Boolean,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .height(68.dp)
            .background(LineColors.Canvas)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(52.dp).clip(CircleShape).background(LineColors.Surface))
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(3.dp)) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                Text(name, style = LineText.FriendName, color = LineColors.TextPrimary)
                if (official) {
                    // LINE's verified/business trust primitive
                    Icon(Icons.Filled.Verified, contentDescription = "Official account, verified",
                        tint = LineColors.Green, modifier = Modifier.size(14.dp))
                }
            }
            Text(status, style = LineText.Status, color = LineColors.TextSecondary, maxLines = 1)
        }
        Text(time, style = LineText.Timestamp, color = LineColors.TextSecondary)
    }
}
```

### Sticker-Shop Tile

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.border

@Composable
fun StickerShopTile(
    previewUrl: String,
    title: String,
    author: String,
    price: String, // "120" or "Free"
) {
    Column(
        Modifier
            .clip(RoundedCornerShape(12.dp))
            .background(LineColors.Surface)
            .padding(12.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        AsyncImage(
            model = previewUrl, contentDescription = title,
            modifier = Modifier.fillMaxWidth().height(96.dp)
                .clip(RoundedCornerShape(8.dp)).background(Color.White),
        )
        Text(title, style = LineText.Section, color = LineColors.TextPrimary)
        Text(author, style = LineText.Timestamp, color = LineColors.TextSecondary)
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.End) {
            Box(
                Modifier
                    .clip(CircleShape)
                    .border(BorderStroke(1.dp, LineColors.Green), CircleShape)
                    .padding(horizontal = 14.dp, vertical = 6.dp),
            ) {
                Text(if (price == "Free") "Free" else "🪙 $price",
                    style = LineText.StickerPx, color = LineColors.Green)
            }
        }
    }
}
```

### Composer (sticker keyboard is primary)

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material.icons.filled.Mic
import androidx.compose.material.icons.outlined.EmojiEmotions
import androidx.compose.material.icons.outlined.PhotoCamera
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun ComposerBar() {
    var text by remember { mutableStateOf("") }
    val hasText = text.trim().isNotEmpty()
    val haptics = LocalHapticFeedback.current

    Row(
        Modifier.fillMaxWidth().background(LineColors.Backdrop).padding(8.dp),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(Icons.Filled.Add, contentDescription = "More", tint = Color.White,
            modifier = Modifier.size(24.dp))
        Row(
            Modifier
                .weight(1f)
                .heightIn(min = 36.dp)
                .clip(RoundedCornerShape(18.dp))
                .background(Color.White)
                .padding(horizontal = 12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            BasicTextField(
                value = text, onValueChange = { text = it },
                textStyle = LineText.MessageBody.copy(color = LineColors.TextPrimary),
                modifier = Modifier.weight(1f).padding(vertical = 8.dp),
                decorationBox = { inner ->
                    if (text.isEmpty()) Text("Aa", style = LineText.MessageBody,
                        color = LineColors.TextTertiary)
                    inner()
                },
            )
            // sticker keyboard — a primary input mode in LINE
            Icon(Icons.Outlined.EmojiEmotions, contentDescription = "Stickers",
                tint = LineColors.TextSecondary, modifier = Modifier.size(22.dp))
            Icon(Icons.Outlined.PhotoCamera, contentDescription = "Camera",
                tint = LineColors.TextSecondary, modifier = Modifier.size(20.dp))
        }
        Box(
            Modifier.size(30.dp).clickable {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                text = ""
            },
            contentAlignment = Alignment.Center,
        ) {
            if (hasText) {
                Box(Modifier.size(30.dp).clip(CircleShape).background(LineColors.Green),
                    contentAlignment = Alignment.Center) {
                    Icon(Icons.Filled.ArrowUpward, contentDescription = "Send",
                        tint = Color.White, modifier = Modifier.size(16.dp))
                }
            } else {
                Icon(Icons.Filled.Mic, contentDescription = "Voice message",
                    tint = Color.White, modifier = Modifier.size(22.dp))
            }
        }
    }
}
```

## 4. Chat Backdrop

```kotlin
@Composable
fun ChatScreen(content: @Composable ColumnScope.() -> Unit) {
    androidx.compose.foundation.lazy.LazyColumn(
        Modifier.fillMaxSize().background(LineColors.Backdrop), // periwinkle — never white
        contentPadding = PaddingValues(vertical = 12.dp),
    ) {
        item { Column(content = content) }
    }
}
```

## 5. Bottom Navigation (Super-App)

Material 3 `NavigationBar` with five destinations. Android has no live blur; use an opaque white surface with a hairline. Active tint is LINE Green.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun LineBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = LineColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"   to Icons.Filled.Home,
            "Talk"   to Icons.Filled.ChatBubble,
            "VOOM"   to Icons.Filled.PlayCircle,
            "News"   to Icons.Filled.Article,
            "Wallet" to Icons.Filled.AccountBalanceWallet,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = LineText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = LineColors.Green,
                    selectedTextColor   = LineColors.Green,
                    unselectedIconColor = LineColors.TextSecondary,
                    unselectedTextColor = LineColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // LINE has no Material pill
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Sticker bounce-in (signature) | `Animatable` 0.6 → 1.08 (`spring(dampingRatio = 0.55f)`) → 1.0 |
| Animated sticker replay | reset `Animatable` to 0.6f and re-run the sequence on tap |
| Text bubble enter | `AnimatedVisibility` + `fadeIn() + slideInVertically { 6 }`, 220ms |
| Send press | `animateFloatAsState` scale 1 → 0.92, `HapticFeedbackType.TextHandleMove` |
| Sticker keyboard present | `AnimatedVisibility` + `slideInVertically { it }`, 280ms |
| Tab switch | `Crossfade` |

```kotlin
// Animated sticker replay on tap
@Composable
fun ReplayableSticker(url: String) {
    val scale = remember { Animatable(1f) }
    val scope = rememberCoroutineScope()
    AsyncImage(
        model = url, contentDescription = "Sticker",
        modifier = Modifier
            .size(140.dp)
            .scale(scale.value)
            .clickable {
                scope.launch {
                    scale.snapTo(0.6f)
                    scale.animateTo(1.08f, spring(dampingRatio = 0.55f))
                    scale.animateTo(1f, spring(dampingRatio = 0.7f))
                }
            },
    )
}
```

Haptics: prefer `LocalHapticFeedback`. For the send tap use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) to approximate iOS's light impact.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Stickers are remote image assets (Coil), not icon fonts.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Send (text present) | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Voice (empty composer) | `mic.fill` | `Icons.Filled.Mic` |
| Sticker keyboard | `face.smiling` | `Icons.Outlined.EmojiEmotions` |
| Plus / more | `plus` | `Icons.Filled.Add` |
| Camera | `camera` | `Icons.Outlined.PhotoCamera` |
| Official-account badge | `checkmark.seal.fill` | `Icons.Filled.Verified` |
| Audio call | `phone` | `Icons.Filled.Call` |
| Video call | `video` | `Icons.Filled.Videocam` |
| Menu (chat) | `line.3.horizontal` | `Icons.Filled.Menu` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Talk (tab) | `bubble.left.fill` | `Icons.Filled.ChatBubble` |
| VOOM (tab) | `play.rectangle.fill` | `Icons.Filled.PlayCircle` |
| News (tab) | `newspaper.fill` | `Icons.Filled.Article` |
| Wallet (tab) | `wallet.pass.fill` | `Icons.Filled.AccountBalanceWallet` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`. The chat backdrop is colored (periwinkle), so pick system-bar icon contrast against the *header* (white) rather than the backdrop. Apply `Scaffold` insets so the composer clears gesture nav and rises with the IME (`Modifier.imePadding()`).
- **Bubble-less stickers**: render the sticker as a plain `AsyncImage` — never wrap it in a `Surface`/bubble or clamp it to the 70% bubble width.
- **Periwinkle backdrop is user-themeable**: if you support custom chat backgrounds, re-check bubble + meta contrast per theme; the faint 1dp bubble shadow exists specifically so white/green read on periwinkle.
- **Font scaling**: `sp` honors the user's font scale — keep it on names and message bodies. Pin layout-sensitive text (tab labels, badge counts, sticker prices) via a fixed-density wrapper; stickers are image assets and don't scale with type.
- **TalkBack**: a sticker MUST have a meaningful `contentDescription` (its name/emotion) — there is no text fallback. Announce the official-account badge as "Official account, verified" after the name.
- **Touch targets**: Material guidance is 48.dp minimum. Give the 30.dp send and the composer sticker glyph a 48.dp effective hit via padding; the full 140.dp sticker is tappable to replay.
- **Contrast**: `#000000` text on `#C5F0B1` and `#FFFFFF` passes WCAG AAA. `#8C8C8C` meta floats on the `#8CABD9` backdrop — validate per theme and add a subtle plate behind meta for low-contrast custom backgrounds.
- **Reduce motion**: gate the sticker bounce-in behind the system animator scale and fall back to a quick fade-to-scale-1.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — LINE Green and the pale-green bubble are fixed brand colors regardless of wallpaper.
