# WeChat (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports WeChat's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (WeChat's gray system, the super-app Discover grouped-list hub, pale-green tailed bubbles, rounded-square avatars, the red-packet card, the universal red dot) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a custom tailed bubble `Shape`, `sp`/`dp` instead of `pt`. (WeChat's real Android app is itself this UI; this maps the iOS spec onto modern Compose.)

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars.

## 1. Color Tokens

```kotlin
// ui/theme/WeChatColors.kt
import androidx.compose.ui.graphics.Color

object WeChatColors {
    // Backdrop & canvas
    val Backdrop   = Color(0xFFEDEDED) // chat + grouped-list base
    val Canvas     = Color(0xFFFFFFFF)
    val Surface    = Color(0xFFF7F7F7)
    val Divider    = Color(0xFFD9D9D9)
    val PressedRow = Color(0xFFECECEC)

    // Text
    val TextPrimary   = Color(0xFF181818)
    val TextSecondary = Color(0xFF888888)
    val TextTertiary  = Color(0xFFB2B2B2)

    // Brand
    val Green        = Color(0xFF07C160)
    val GreenPressed = Color(0xFF06A050)

    // Message
    val Incoming = Color(0xFFFFFFFF)
    val Outgoing = Color(0xFF95EC69) // pale chat-green — NOT the brand green

    // Semantic
    val RedDot   = Color(0xFFFA5151)
    val LinkBlue = Color(0xFF576B95)
    val Gold     = Color(0xFFFBE3B3)

    // Discover icon chips (data-driven)
    val Moments    = Color(0xFF3F7DD5)
    val Scan       = Color(0xFF2EA0F8)
    val Channels   = Color(0xFFFA9D3B)
    val Mini       = Color(0xFF3CC51F)
    val Search     = Color(0xFF5C7CFA)
    val Nearby     = Color(0xFFF76F34)
    val TopStories = Color(0xFFEB6F6F)

    // Dark (system-dark users)
    val DarkCanvas        = Color(0xFF1A1A1A)
    val DarkBackdrop      = Color(0xFF111111)
    val DarkIncoming      = Color(0xFF2C2C2C)
    val DarkOutgoing      = Color(0xFF3EB575)
    val DarkTextPrimary   = Color(0xFFE8E8E8)
    val DarkTextSecondary = Color(0xFF9A9A9A)
}
```

Wire it into Material 3 schemes so ripples, dividers, and stock component colors inherit the brand. WeChat is light-first; provide a dark scheme for system-dark users.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val WeChatLight = lightColorScheme(
    primary        = WeChatColors.Green,
    onPrimary      = Color.White,
    background      = WeChatColors.Backdrop,
    onBackground   = WeChatColors.TextPrimary,
    surface         = WeChatColors.Canvas,
    onSurface      = WeChatColors.TextPrimary,
    surfaceVariant = WeChatColors.Surface,
    outline        = WeChatColors.Divider,
    error          = WeChatColors.RedDot,
)

private val WeChatDark = darkColorScheme(
    primary        = WeChatColors.Green,
    onPrimary      = Color.White,
    background      = WeChatColors.DarkBackdrop,
    onBackground   = WeChatColors.DarkTextPrimary,
    surface         = WeChatColors.DarkCanvas,
    onSurface      = WeChatColors.DarkTextPrimary,
    surfaceVariant = WeChatColors.DarkCanvas,
    outline        = Color(0xFF333333),
    error          = WeChatColors.RedDot,
)

@Composable
fun WeChatTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) WeChatDark else WeChatLight,
        typography  = WeChatTypography,
        content     = content,
    )
```

## 2. Typography

WeChat uses **PingFang SC** (Apple CJK face — not on Android) / **Noto Sans CJK** at weights 400/600. On Android, drop Noto Sans CJK in `res/font/`.

```kotlin
// ui/theme/WeChatType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

// Noto Sans CJK is the Android stand-in for PingFang SC
val WeChatSans = FontFamily(
    Font(R.font.noto_sans_cjk_regular,  FontWeight.Normal),   // 400
    Font(R.font.noto_sans_cjk_medium,   FontWeight.SemiBold),  // 600 (Noto "Medium")
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object WeChatText {
    val NavTitle    = TextStyle(WeChatSans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 22.sp)
    val ContactName = TextStyle(WeChatSans, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 22.sp)
    val RowTitle    = TextStyle(WeChatSans, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 22.sp)
    val MessageBody = TextStyle(WeChatSans, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 24.sp) // CJK-friendly 1.4
    val Section     = TextStyle(WeChatSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 17.sp)
    val Preview     = TextStyle(WeChatSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val Timestamp   = TextStyle(WeChatSans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 16.sp)
    val TimeSep     = TextStyle(WeChatSans, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 14.sp)
    val Button      = TextStyle(WeChatSans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 17.sp)
    val ButtonSec   = TextStyle(WeChatSans, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(WeChatSans, fontWeight = FontWeight.Normal,   fontSize = 10.sp, lineHeight = 12.sp)
    val Badge       = TextStyle(WeChatSans, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 11.sp)
    val MomentsName = TextStyle(WeChatSans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Caption     = TextStyle(WeChatSans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val WeChatTypography = Typography(
    titleLarge  = WeChatText.NavTitle,
    bodyLarge   = WeChatText.MessageBody,
    titleMedium = WeChatText.ContactName,
    bodyMedium  = WeChatText.Preview,
    labelSmall  = WeChatText.Tab,
)
```

## 3. Signature Components

### Discover Grouped-List Hub (Signature — the super-app)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ChevronRight
import androidx.compose.material3.HorizontalDivider
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.unit.dp

@Composable
fun DiscoverRow(
    icon: ImageVector,
    chipColor: Color,
    title: String,
    redDot: Boolean = false,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Row(
        Modifier
            .fillMaxWidth()
            .height(56.dp)
            .background(if (pressed) WeChatColors.PressedRow else WeChatColors.Canvas)
            .clickable(interaction, indication = null) { }
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(
            Modifier.size(28.dp).clip(RoundedCornerShape(8.dp)).background(chipColor),
            contentAlignment = Alignment.Center,
        ) { Icon(icon, contentDescription = null, tint = Color.White, modifier = Modifier.size(15.dp)) }
        Text(title, style = WeChatText.RowTitle, color = WeChatColors.TextPrimary)
        Spacer(Modifier.weight(1f))
        if (redDot) Box(Modifier.size(8.dp).clip(RoundedCornerShape(4.dp)).background(WeChatColors.RedDot))
        Spacer(Modifier.width(8.dp))
        Icon(Icons.Filled.ChevronRight, contentDescription = null,
            tint = WeChatColors.TextTertiary, modifier = Modifier.size(18.dp))
    }
}

@Composable
fun DiscoverScreen() {
    LazyColumn(
        Modifier.fillMaxSize().background(WeChatColors.Backdrop),
        contentPadding = PaddingValues(vertical = 12.dp),
    ) {
        item { Cluster { DiscoverRow(Icons.Filled.PhotoLibrary, WeChatColors.Moments, "Moments", redDot = true) } }
        item {
            Cluster {
                DiscoverRow(Icons.Filled.PlayCircle, WeChatColors.Channels, "Channels")
                RowDivider()
                DiscoverRow(Icons.Filled.QrCodeScanner, WeChatColors.Scan, "Scan")
                RowDivider()
                DiscoverRow(Icons.Filled.Article, WeChatColors.TopStories, "Top Stories")
            }
        }
        item {
            Cluster {
                DiscoverRow(Icons.Filled.Search, WeChatColors.Search, "Search")
                RowDivider()
                DiscoverRow(Icons.Filled.LocationOn, WeChatColors.Nearby, "People Nearby")
            }
        }
        item { Cluster { DiscoverRow(Icons.Filled.GridView, WeChatColors.Mini, "Mini Programs") } }
    }
}

@Composable
private fun Cluster(content: @Composable ColumnScope.() -> Unit) {
    Column(
        Modifier.fillMaxWidth().background(WeChatColors.Canvas).padding(bottom = 0.dp),
        content = content,
    )
    Spacer(Modifier.height(12.dp)) // gray gutter between clusters
}

@Composable
private fun RowDivider() =
    HorizontalDivider(Modifier.padding(start = 56.dp), color = WeChatColors.Divider)
```

### Tailed Message Bubble + Rounded-Square Avatar (Signature)

```kotlin
import androidx.compose.foundation.shape.GenericShape
import androidx.compose.ui.unit.Density
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.unit.LayoutDirection

@Composable
fun WCBubble(text: String, isOutgoing: Boolean) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 8.dp, vertical = 2.dp),
        horizontalArrangement = if (isOutgoing) Arrangement.End else Arrangement.Start,
        verticalAlignment = Alignment.Top,
    ) {
        if (!isOutgoing) { Avatar(); Spacer(Modifier.width(8.dp)) }
        Text(
            text,
            style = WeChatText.MessageBody,
            color = WeChatColors.TextPrimary,
            modifier = Modifier
                .widthIn(max = 270.dp)
                .clip(bubbleTailShape(isOutgoing))
                .background(if (isOutgoing) WeChatColors.Outgoing else WeChatColors.Incoming)
                .padding(
                    start = if (isOutgoing) 12.dp else 16.dp, // extra room for the leading tail
                    end   = if (isOutgoing) 16.dp else 12.dp,
                    top = 9.dp, bottom = 9.dp,
                ),
        )
        if (isOutgoing) { Spacer(Modifier.width(8.dp)); Avatar() }
    }
}

// Rounded-SQUARE avatar — WeChat's constant brand tell (never a circle)
@Composable
private fun Avatar() =
    Box(Modifier.size(40.dp).clip(RoundedCornerShape(8.dp)).background(WeChatColors.Surface))

// 6dp body radius + a small solid triangular tail toward the sender's avatar
private fun bubbleTailShape(isOutgoing: Boolean) = GenericShape { size, _ ->
    val r = 6f; val tail = 12f; val ty = 22f
    if (isOutgoing) {
        addRoundRect(androidx.compose.ui.geometry.RoundRect(
            0f, 0f, size.width - tail, size.height,
            androidx.compose.ui.geometry.CornerRadius(r)))
        moveTo(size.width - tail, ty - 6f)
        lineTo(size.width, ty)
        lineTo(size.width - tail, ty + 6f)
        close()
    } else {
        addRoundRect(androidx.compose.ui.geometry.RoundRect(
            tail, 0f, size.width, size.height,
            androidx.compose.ui.geometry.CornerRadius(r)))
        moveTo(tail, ty - 6f)
        lineTo(0f, ty)
        lineTo(tail, ty + 6f)
        close()
    }
}
```

### Chat List Row (rounded-square avatar + red badge)

```kotlin
@Composable
fun ChatRow(name: String, preview: String, time: String, unread: Int) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Row(
        Modifier
            .fillMaxWidth()
            .height(64.dp)
            .background(if (pressed) WeChatColors.PressedRow else WeChatColors.Canvas)
            .clickable(interaction, indication = null) { }
            .padding(horizontal = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(48.dp).clip(RoundedCornerShape(8.dp)).background(WeChatColors.Surface)) // rounded square
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(name, style = WeChatText.ContactName, color = WeChatColors.TextPrimary)
            Text(preview, style = WeChatText.Preview, color = WeChatColors.TextSecondary, maxLines = 1)
        }
        Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(time, style = WeChatText.Timestamp, color = WeChatColors.TextSecondary)
            if (unread > 0) {
                Box(
                    Modifier.defaultMinSize(18.dp, 18.dp).clip(RoundedCornerShape(9.dp))
                        .background(WeChatColors.RedDot).padding(horizontal = 5.dp),
                    contentAlignment = Alignment.Center,
                ) { Text("$unread", style = WeChatText.Badge, color = Color.White) }
            }
        }
    }
}
```

### Red Packet (Hóngbāo) Card (Signature)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.MailOutline
import androidx.compose.material.icons.filled.Paid
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.graphicsLayer

@Composable
fun RedPacketCard(sender: String) {
    var opened by remember { mutableStateOf(false) }
    val flip by animateFloatAsState(if (opened) 360f else 0f, tween(600), label = "seal")

    Column(
        Modifier
            .width(260.dp)
            .shadow(4.dp, RoundedCornerShape(8.dp), spotColor = Color.Black.copy(alpha = 0.12f))
            .clip(RoundedCornerShape(8.dp))
            .background(Brush.verticalGradient(listOf(WeChatColors.RedDot, Color(0xFFC72E29))))
            .clickable { opened = true }
            .padding(vertical = 22.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box(
            Modifier.size(44.dp).clip(CircleShape).background(WeChatColors.Gold)
                .graphicsLayer { rotationY = flip },
            contentAlignment = Alignment.Center,
        ) {
            Icon(if (opened) Icons.Filled.MailOutline else Icons.Filled.Paid,
                contentDescription = "Red packet from $sender, open",
                tint = Color(0xFFC72E29), modifier = Modifier.size(22.dp))
        }
        Text("Best wishes!", style = WeChatText.Button, color = Color.White)
        Text("From $sender", style = WeChatText.Caption, color = Color.White.copy(alpha = 0.85f))
    }
}
```

### Composer

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.GraphicEq
import androidx.compose.material.icons.outlined.EmojiEmotions
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun ComposerBar() {
    var text by remember { mutableStateOf("") }
    val hasText = text.trim().isNotEmpty()
    val haptics = LocalHapticFeedback.current

    Row(
        Modifier.fillMaxWidth().background(WeChatColors.Backdrop).padding(8.dp),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(Icons.Filled.GraphicEq, contentDescription = "Voice",
            tint = WeChatColors.TextPrimary, modifier = Modifier.size(26.dp))
        BasicTextField(
            value = text, onValueChange = { text = it },
            textStyle = WeChatText.MessageBody.copy(color = WeChatColors.TextPrimary),
            modifier = Modifier
                .weight(1f)
                .heightIn(min = 36.dp)
                .clip(RoundedCornerShape(6.dp))
                .background(WeChatColors.Canvas)
                .padding(horizontal = 10.dp, vertical = 8.dp),
        )
        Icon(Icons.Outlined.EmojiEmotions, contentDescription = "Stickers",
            tint = WeChatColors.TextPrimary, modifier = Modifier.size(26.dp))
        if (hasText) {
            Box(
                Modifier
                    .clip(RoundedCornerShape(4.dp))
                    .background(WeChatColors.Green)
                    .clickable {
                        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                        text = ""
                    }
                    .padding(horizontal = 14.dp, vertical = 8.dp),
            ) { Text("Send", style = WeChatText.ButtonSec, color = Color.White) }
        } else {
            Icon(Icons.Filled.Add, contentDescription = "More",
                tint = WeChatColors.TextPrimary, modifier = Modifier.size(26.dp))
        }
    }
}
```

## 4. Time Separator

```kotlin
@Composable
fun TimeSeparator(label: String) {
    Box(Modifier.fillMaxWidth().padding(vertical = 8.dp), contentAlignment = Alignment.Center) {
        Text(label, style = WeChatText.TimeSep, color = WeChatColors.TextSecondary)
    }
}
```

## 5. Bottom Navigation

Material 3 `NavigationBar` (Chats / Contacts / Discover / Me). Android has no live blur; use a very-light `#F7F7F7` surface with a hairline. Active tint is WeChat Green.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun WeChatBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = WeChatColors.Surface, tonalElevation = 0.dp) {
        val items = listOf(
            "Chats"    to Icons.Filled.Chat,
            "Contacts" to Icons.Filled.Contacts,
            "Discover" to Icons.Filled.Explore,
            "Me"       to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(26.dp)) },
                label = { Text(label, style = WeChatText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = WeChatColors.Green,
                    selectedTextColor   = WeChatColors.Green,
                    unselectedIconColor = Color(0xFF999999),
                    unselectedTextColor = Color(0xFF999999),
                    indicatorColor      = Color.Transparent, // WeChat has no Material pill
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Red-packet open (the one flourish) | `animateFloatAsState` `rotationY` 0 → 360 over `tween(600)` |
| Send (text) | `AnimatedVisibility` + `fadeIn() + scaleIn(initialScale = 0.98f)`, 150ms |
| Press states | `collectIsPressedAsState` toggling row/button background, ~120ms |
| Attachment grid present | `AnimatedVisibility` + `slideInVertically { it }`, 260ms; "+" `rotate` to 45° |
| Tab switch | none (instant) |
| Red dot | render/unrender with **no** animation — authoritative, not playful |

```kotlin
// Attachment "+" → "×" rotation
val rot by animateFloatAsState(if (gridOpen) 45f else 0f, tween(260), label = "plusRot")
Icon(Icons.Filled.Add, contentDescription = "More",
    modifier = Modifier.size(26.dp).graphicsLayer { rotationZ = rot },
    tint = WeChatColors.TextPrimary)
```

Haptics: prefer `LocalHapticFeedback`. For the send tap use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) to approximate iOS's light impact. Keep haptics sparse — WeChat's restraint extends to feedback.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Export WeChat's exact Discover glyphs as vector drawables for parity if needed.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Voice | `waveform.circle` | `Icons.Filled.GraphicEq` |
| Sticker | `face.smiling` | `Icons.Outlined.EmojiEmotions` |
| Plus / attachment | `plus.circle` | `Icons.Filled.Add` |
| Moments (Discover) | `photo.on.rectangle` | `Icons.Filled.PhotoLibrary` |
| Scan (Discover) | `qrcode.viewfinder` | `Icons.Filled.QrCodeScanner` |
| Channels (Discover) | `play.rectangle.fill` | `Icons.Filled.PlayCircle` |
| Mini Programs (Discover) | `square.grid.2x2.fill` | `Icons.Filled.GridView` |
| Search (Discover) | `magnifyingglass` | `Icons.Filled.Search` |
| Nearby (Discover) | `location.fill` | `Icons.Filled.LocationOn` |
| Chevron (rows) | `chevron.right` | `Icons.Filled.ChevronRight` |
| Red packet | `yensign.circle.fill` / `envelope.open.fill` | `Icons.Filled.Paid` / `Icons.Filled.MailOutline` |
| Chats (tab) | `bubble.left.fill` | `Icons.Filled.Chat` |
| Contacts (tab) | `person.crop.square.fill` | `Icons.Filled.Contacts` |
| Discover (tab) | `safari.fill` | `Icons.Filled.Explore` |
| Me (tab) | `person.fill` | `Icons.Filled.Person` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the gray/white system wants dark system-bar icons. Apply `Scaffold` insets so the composer clears gesture nav and rises with the IME (`Modifier.imePadding()`).
- **Discover is a grouped list**, not a dashboard — white rows on the `#EDEDED` base, 12dp gray gutters between clusters, hairline dividers within. This screen is the identity.
- **Rounded-square avatars**: every avatar is `RoundedCornerShape(8.dp)` on a square — never `CircleShape`. A constant brand tell.
- **CJK**: PingFang SC is iOS-only; on Android bundle **Noto Sans CJK** in `res/font/` and keep `lineHeight` generous (≈1.4 on 17sp body) so dense CJK isn't cramped.
- **Font scaling**: `sp` honors the user's font scale — keep it on names, rows, and bodies (they share the 17sp base). Pin layout-sensitive text (tab labels, badge counts, time separators) via a fixed-density wrapper.
- **TalkBack**: announce the Discover row's red dot as "has new updates"; each row is one button. Label the red-packet seal "Red packet from <sender>, double-tap to open". Merge a chat row's name + preview with `Modifier.semantics(mergeDescendants = true)`.
- **Touch targets**: Material guidance is 48.dp minimum. Give the compact "Send" button and composer glyphs a 48.dp effective hit via padding; Discover rows are a full 56.dp and chat rows 64.dp.
- **Contrast**: `#181818` on `#95EC69`, `#FFFFFF`, and `#EDEDED` passes WCAG AAA; `#888888` on white passes AA at 14sp+ — validate `#888888` time separators on the `#EDEDED` backdrop.
- **Reduce motion**: gate the red-packet `rotationY` behind the system animator scale and crossfade to the opened state; the red dot must never animate.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — WeChat Green and the pale-green bubble are fixed brand colors regardless of wallpaper.
