# GroupMe (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports GroupMe's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, an M3 `Typography`, paste-ready `@Composable`s for the colored chat top bar, message bubbles, the signature heart-like pill, the image-gallery block, the composer, plus navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the solid GroupMe-Blue chat header, blue outbound bubbles, the docked heart-like count pill, circular gradient avatars, per-group theme accents) while making everything idiomatic Android — a `TopAppBar` painted GroupMe Blue, a `NavigationBar` instead of a UITabBar, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for group avatars and gallery images. No color extraction — GroupMe's palette is fixed brand blue plus a small theme set.

## 1. Color Tokens

```kotlin
// ui/theme/GMColors.kt
import androidx.compose.ui.graphics.Color

object GMColors {
    // Brand (interactive)
    val Blue        = Color(0xFF00AFF0)
    val BluePressed = Color(0xFF0091C7)
    val BlueDeep    = Color(0xFF0077B5)

    // Canvas & Surfaces (Light)
    val Canvas       = Color(0xFFFFFFFF)
    val SurfaceGray  = Color(0xFFF7F7F8)
    val InboundLight = Color(0xFFF0F0F0)
    val DividerLight = Color(0xFFE4E4E6)

    // Canvas & Surfaces (Dark) — NOT pure black
    val DarkCanvas   = Color(0xFF121212)
    val DarkSurface1 = Color(0xFF1C1C1E)
    val InboundDark  = Color(0xFF2A2A2C)
    val DarkDivider  = Color(0xFF2C2C2E)

    // Text
    val TextPrimary       = Color(0xFF1A1A1A)
    val TextSecondary     = Color(0xFF6A6A6A)
    val TextTertiary      = Color(0xFF9A9A9A)
    val DarkTextPrimary   = Color(0xFFECECEC)
    val DarkTextSecondary = Color(0xFFA0A0A0)
    val OnBlue            = Color(0xFFFFFFFF)

    // Semantic
    val LikeHeart = Color(0xFFFF3B5C)
    val Error     = Color(0xFFF15E6C)
    val Success   = Color(0xFF2ECC71)
    val Warning   = Color(0xFFF1C40F)

    // Per-group theme accents (recolor avatars + sender names; blue chrome constant)
    val ThemeCoral  = Color(0xFFFF6B6B)
    val ThemeGreen  = Color(0xFF2ECC71)
    val ThemePurple = Color(0xFF9B59B6)
    val ThemeTeal   = Color(0xFF1ABC9C)
}

// Avatar generated gradients, assigned per member id
val gmAvatarGradients: List<List<Color>> = listOf(
    listOf(Color(0xFFFF6B6B), Color(0xFFFF8E53)), // warm
    listOf(Color(0xFF2ECC71), Color(0xFF16A085)), // green
    listOf(Color(0xFF9B59B6), Color(0xFF6C5CE7)), // purple
    listOf(Color(0xFF4A90D9), Color(0xFF1E3A5F)), // blue
    listOf(Color(0xFFF39C12), Color(0xFFB5651D)), // amber
)
fun gmGradientForId(id: Int): List<Color> =
    gmAvatarGradients[Math.floorMod(id, gmAvatarGradients.size)]

// Per-group theme accents
data class GMGroupTheme(val accent: Color, val gradient: List<Color>)
val gmGroupThemes = mapOf(
    "default" to GMGroupTheme(GMColors.Blue,        listOf(Color(0xFF00AFF0), Color(0xFF0077B5))),
    "coral"   to GMGroupTheme(GMColors.ThemeCoral,  listOf(Color(0xFFFF6B6B), Color(0xFFFF8E53))),
    "green"   to GMGroupTheme(GMColors.ThemeGreen,  listOf(Color(0xFF2ECC71), Color(0xFF16A085))),
    "purple"  to GMGroupTheme(GMColors.ThemePurple, listOf(Color(0xFF9B59B6), Color(0xFF6C5CE7))),
    "sunset"  to GMGroupTheme(Color(0xFFFF8E53),    listOf(Color(0xFFFF6B6B), Color(0xFF9B59B6))),
    "teal"    to GMGroupTheme(GMColors.ThemeTeal,   listOf(Color(0xFF1ABC9C), Color(0xFF16A085))),
)
```

Wire it into both schemes. GroupMe is light-first; the dark scheme uses the signature `#121212`, never true black. The blue chrome holds in both.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val GMLight = lightColorScheme(
    primary        = GMColors.Blue,
    onPrimary      = GMColors.OnBlue,
    background     = GMColors.Canvas,
    onBackground   = GMColors.TextPrimary,
    surface        = GMColors.SurfaceGray,
    onSurface      = GMColors.TextPrimary,
    surfaceVariant = GMColors.InboundLight,
    outline        = GMColors.DividerLight,
    error          = GMColors.Error,
)

private val GMDark = darkColorScheme(
    primary        = GMColors.Blue,             // brand blue holds on dark
    onPrimary      = GMColors.OnBlue,
    background     = GMColors.DarkCanvas,
    onBackground   = GMColors.DarkTextPrimary,
    surface        = GMColors.DarkSurface1,
    onSurface      = GMColors.DarkTextPrimary,
    surfaceVariant = GMColors.InboundDark,
    outline        = GMColors.DarkDivider,
    error          = GMColors.Error,
)

@Composable
fun GMTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) GMDark else GMLight,
    typography = GMTypography,
    content = content,
)
```

## 2. Typography (M3)

GroupMe ships no brand typeface — it uses the system face. On Android that's Roboto; do not bundle a decorative face. `sp` honors the user's font scale.

```kotlin
// ui/theme/GMType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

object GMText {
    val LargeNav   = TextStyle(FontFamily.Default, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val Screen     = TextStyle(FontFamily.Default, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val GroupName  = TextStyle(FontFamily.Default, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Section    = TextStyle(FontFamily.Default, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val Body       = TextStyle(FontFamily.Default, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 22.sp)
    val RowTitle   = TextStyle(FontFamily.Default, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Preview    = TextStyle(FontFamily.Default, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 18.sp)
    val Meta       = TextStyle(FontFamily.Default, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 18.sp)
    val SenderName = TextStyle(FontFamily.Default, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.1.sp)
    val LikeCount  = TextStyle(FontFamily.Default, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 10.sp)
    val Tab        = TextStyle(FontFamily.Default, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp)
    val Unread     = TextStyle(FontFamily.Default, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp)
    val Button     = TextStyle(FontFamily.Default, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
}

val GMTypography = Typography(
    headlineLarge = GMText.LargeNav,
    titleLarge    = GMText.GroupName,
    titleMedium   = GMText.Section,
    bodyMedium    = GMText.Body,
    labelSmall    = GMText.Tab,
)
```

## 3. Signature Components

### Chat Top Bar (the colored header)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.automirrored.filled.ArrowBack
import androidx.compose.material.icons.outlined.Info
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun GMChatTopBar(
    groupName: String,
    subline: String,
    gradient: List<Color>,
    initials: String,
    onBack: () -> Unit,
    onInfo: () -> Unit,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .background(GMColors.Blue)        // the signature solid-blue header
            .statusBarsPadding()
            .height(56.dp)
            .padding(horizontal = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        IconButton(onClick = onBack) {
            Icon(Icons.AutoMirrored.Filled.ArrowBack, "Back", tint = GMColors.OnBlue)
        }
        Box(
            Modifier.size(34.dp).clip(CircleShape)
                .background(Brush.linearGradient(gradient)),
            contentAlignment = Alignment.Center,
        ) { Text(initials, color = Color.White, fontSize = 14.sp, fontWeight = FontWeight.Bold) }

        Column(Modifier.weight(1f)) {
            Text(groupName, style = GMText.GroupName, color = GMColors.OnBlue, maxLines = 1)
            Text(subline, color = GMColors.OnBlue.copy(alpha = 0.82f), fontSize = 11.sp, maxLines = 1)
        }
        IconButton(onClick = onInfo) {
            Icon(Icons.Outlined.Info, "Group info", tint = GMColors.OnBlue)
        }
    }
}
```

### Message Bubble + Like Pill

```kotlin
import androidx.compose.animation.core.Spring
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.runtime.*
import androidx.compose.ui.draw.scale

@Composable
fun GMMessageBubble(
    text: String,
    outbound: Boolean,
    likeCount: Int = 0,
    dark: Boolean = false,
) {
    val bg = if (outbound) GMColors.Blue else if (dark) GMColors.InboundDark else GMColors.InboundLight
    val fg = if (outbound) Color.White else if (dark) GMColors.DarkTextPrimary else GMColors.TextPrimary
    val shape = RoundedCornerShape(
        topStart = 18.dp, topEnd = 18.dp,
        bottomStart = if (outbound) 18.dp else 5.dp,
        bottomEnd = if (outbound) 5.dp else 18.dp,
    )

    Box(
        contentAlignment = if (outbound) Alignment.BottomEnd else Alignment.BottomStart,
        modifier = Modifier.widthIn(max = 260.dp),
    ) {
        Box(
            Modifier.clip(shape).background(bg).padding(horizontal = 13.dp, vertical = 9.dp)
        ) {
            Text(text, style = GMText.Body, color = fg)
        }
        if (likeCount > 0) {
            GMLikePill(
                likeCount, dark,
                Modifier.offset(x = if (outbound) (-8).dp else 8.dp, y = 10.dp)
            )
        }
    }
}

@Composable
fun GMLikePill(count: Int, dark: Boolean, modifier: Modifier = Modifier) {
    var visible by remember { mutableStateOf(false) }
    val scale by animateFloatAsState(
        if (visible) 1f else 0.6f,
        spring(dampingRatio = Spring.DampingRatioMediumBouncy, stiffness = Spring.StiffnessMediumLow),
        label = "likePop",
    )
    LaunchedEffect(Unit) { visible = true }

    Row(
        modifier
            .scale(scale)
            .clip(RoundedCornerShape(50))
            .background(if (dark) GMColors.DarkSurface1 else GMColors.Canvas)
            .border(1.dp, if (dark) GMColors.DarkDivider else GMColors.DividerLight, RoundedCornerShape(50))
            .padding(start = 5.dp, end = 7.dp, top = 2.dp, bottom = 2.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(3.dp),
    ) {
        Icon(Icons.Filled.Favorite, null, tint = GMColors.LikeHeart, modifier = Modifier.size(11.dp))
        Text("$count", style = GMText.LikeCount,
            color = if (dark) GMColors.DarkTextSecondary else GMColors.TextSecondary)
    }
}
```

### Inbound Sender Row (avatar + colored name above a run)

```kotlin
@Composable
fun GMInboundRow(
    senderName: String,
    gradient: List<Color>,
    initials: String,
    accent: Color,                 // group theme accent
    bubbles: List<String>,
    dark: Boolean = false,
) {
    Row(
        Modifier.fillMaxWidth(),
        verticalAlignment = Alignment.Bottom,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Box(
            Modifier.size(30.dp).clip(CircleShape).background(Brush.linearGradient(gradient)),
            contentAlignment = Alignment.Center,
        ) { Text(initials, color = Color.White, fontSize = 12.sp, fontWeight = FontWeight.Bold) }

        Column(verticalArrangement = Arrangement.spacedBy(3.dp)) {
            Text(senderName, style = GMText.SenderName, color = accent, modifier = Modifier.padding(start = 4.dp))
            bubbles.forEach { GMMessageBubble(it, outbound = false, dark = dark) }
        }
    }
}
```

### Image Gallery Block

```kotlin
import androidx.compose.foundation.layout.FlowRow
import coil.compose.AsyncImage

@OptIn(androidx.compose.foundation.layout.ExperimentalLayoutApi::class)
@Composable
fun GMGalleryBlock(urls: List<String>, onOpen: () -> Unit) {
    val shown = urls.take(4)
    val overflow = urls.size - 4
    Box(
        Modifier
            .clip(RoundedCornerShape(topStart = 18.dp, topEnd = 18.dp, bottomStart = 5.dp, bottomEnd = 18.dp))
            .background(GMColors.InboundLight)
            .padding(4.dp)
            .clickable { onOpen() }
    ) {
        FlowRow(
            maxItemsInEachRow = 2,
            horizontalArrangement = Arrangement.spacedBy(3.dp),
            verticalArrangement = Arrangement.spacedBy(3.dp),
            modifier = Modifier.width(187.dp),
        ) {
            shown.forEachIndexed { i, url ->
                Box(Modifier.size(92.dp).clip(RoundedCornerShape(8.dp))) {
                    AsyncImage(model = url, contentDescription = null, modifier = Modifier.matchParentSize())
                    if (i == 3 && overflow > 0) {
                        Box(Modifier.matchParentSize().background(Color.Black.copy(alpha = 0.5f)),
                            contentAlignment = Alignment.Center) {
                            Text("+$overflow", color = Color.White, fontSize = 16.sp, fontWeight = FontWeight.Bold)
                        }
                    }
                }
            }
        }
    }
}
```

### Composer

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.automirrored.filled.Send
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.hapticfeedback.HapticFeedbackType

@Composable
fun GMComposer(
    value: String,
    onValueChange: (String) -> Unit,
    onAttach: () -> Unit,
    onSend: () -> Unit,
    dark: Boolean = false,
) {
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier
            .fillMaxWidth()
            .background(if (dark) GMColors.DarkSurface1 else GMColors.Canvas)
            .padding(horizontal = 14.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        IconButton(onClick = onAttach) {
            Icon(Icons.Filled.Add, "Attach", tint = GMColors.Blue, modifier = Modifier.size(26.dp))
        }
        Box(
            Modifier.weight(1f).heightIn(min = 38.dp).clip(RoundedCornerShape(50))
                .background(if (dark) GMColors.InboundDark else GMColors.InboundLight)
                .padding(horizontal = 16.dp, vertical = 9.dp),
            contentAlignment = Alignment.CenterStart,
        ) {
            BasicTextField(
                value = value, onValueChange = onValueChange,
                textStyle = GMText.Body.copy(color = if (dark) GMColors.DarkTextPrimary else GMColors.TextPrimary),
                decorationBox = { inner ->
                    if (value.isEmpty()) Text("Send a message", style = GMText.Body, color = GMColors.TextTertiary)
                    inner()
                },
            )
        }
        val enabled = value.isNotBlank()
        Box(
            Modifier.size(38.dp).clip(CircleShape)
                .background(if (enabled) GMColors.Blue else Color(0xFFC8C8CC))
                .clickable(enabled = enabled) {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                    onSend()
                },
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.AutoMirrored.Filled.Send, "Send", tint = Color.White, modifier = Modifier.size(16.dp)) }
    }
}
```

### Chats List Row

```kotlin
@Composable
fun GMChatRow(
    groupName: String, preview: String, time: String, unread: Int,
    gradient: List<Color>, initials: String, onClick: () -> Unit,
) {
    Row(
        Modifier.fillMaxWidth().height(72.dp).clickable { onClick() }
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(
            Modifier.size(52.dp).clip(CircleShape).background(Brush.linearGradient(gradient)),
            contentAlignment = Alignment.Center,
        ) { Text(initials, color = Color.White, fontSize = 18.sp, fontWeight = FontWeight.Bold) }
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(3.dp)) {
            Text(groupName, style = GMText.RowTitle, color = GMColors.TextPrimary)
            Text(preview, style = GMText.Preview, color = GMColors.TextSecondary, maxLines = 1)
        }
        Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(time, fontSize = 12.sp, color = GMColors.TextTertiary)
            if (unread > 0) {
                Box(
                    Modifier.clip(RoundedCornerShape(50)).background(GMColors.Blue)
                        .padding(horizontal = 7.dp, vertical = 3.dp)
                ) { Text("$unread", style = GMText.Unread, color = Color.White) }
            }
        }
    }
}
```

## 4. Navigation

GroupMe uses a 4-tab bottom strip; on Android model it as a `NavigationBar`. Active is GroupMe Blue — no Material tint pill.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun GMBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = GMColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Chats"    to Icons.Filled.Chat,
            "People"   to Icons.Filled.People,
            "Discover" to Icons.Filled.Search,
            "Profile"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = GMText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = GMColors.Blue,   // brand blue, no tint pill
                    selectedTextColor   = GMColors.Blue,
                    unselectedIconColor = Color(0xFF888888),
                    unselectedTextColor = Color(0xFF888888),
                    indicatorColor      = Color.Transparent,
                ),
            )
        }
    }
}
```

The Chats list is a `LazyColumn` of `GMChatRow`s; the group settings sheet (theme picker, mute, members) is a `ModalBottomSheet`. The per-group theme picker is a `Row` of 44.dp color-swatch circles; selecting one drives `gmGroupThemes[key]` and cross-fades visible avatar gradients + sender-name colors while the blue chrome holds.

## 5. Motion

GroupMe motion is light and fun — quick springs, short ease-outs.

| Moment | Compose recipe |
|--------|----------------|
| Like pill pop | `animateFloatAsState` 0.6 → 1.0 with `spring(MediumBouncy, StiffnessMediumLow)`; soft haptic |
| Outbound send | `AnimatedVisibility` `slideInVertically { it } + fadeIn(tween(200))` |
| New inbound message | `fadeIn(tween(180))` + 6dp `slideInVertically` |
| Theme change | `animateColorAsState(tween(250))` on accent → recolor avatars + sender names |
| Attachment sheet | `ModalBottomSheet` default 300ms slide-up + scrim |
| Tab switch | instant content swap; icon tint `animateColorAsState(tween(120))` |
| Typing indicator | three dots `infiniteRepeatable` bounce, 1.2s loop, inside an inbound-style bubble |
| Pull-to-refresh | `PullToRefreshContainer` with `GMColors.Blue` indicator |

```kotlin
// Like pop — the canonical GroupMe motion
val scale by animateFloatAsState(
    targetValue = if (visible) 1f else 0.6f,
    animationSpec = spring(dampingRatio = Spring.DampingRatioMediumBouncy, stiffness = Spring.StiffnessMediumLow),
    label = "likePop",
)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on a like; `HapticFeedbackType.TextHandleMove` for the light tick on send. Tab changes use a selection tick via `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`.

## 6. Icons

GroupMe's iconography is system-standard; the closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Chats (tab) | `bubble.left.and.bubble.right.fill` | `Icons.Filled.Chat` |
| People (tab) | `person.2.fill` | `Icons.Filled.People` |
| Discover (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Group info | `info.circle` | `Icons.Outlined.Info` |
| Like | `heart.fill` / `heart` | `Icons.Filled.Favorite` / `Icons.Outlined.FavoriteBorder` |
| Attach | `plus.circle` | `Icons.Filled.Add` |
| Send | `paperplane.fill` | `Icons.AutoMirrored.Filled.Send` |
| Camera | `camera.fill` | `Icons.Filled.PhotoCamera` |
| Photo library | `photo.on.rectangle` | `Icons.Filled.Image` |
| GIF | `face.smiling` | `Icons.Filled.Gif` |
| Poll | `chart.bar.fill` | `Icons.Filled.BarChart` |
| Event / Calendar | `calendar` | `Icons.Filled.Event` |
| Location | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Mute | `bell.slash.fill` | `Icons.Filled.NotificationsOff` |
| Gallery (per group) | `square.grid.2x2.fill` | `Icons.Filled.GridView` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `FlowRow` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`. The solid-blue chat top bar should draw under the status bar — use `Modifier.statusBarsPadding()` on its content and set light-content status icons while a thread is open (the bar is GroupMe Blue). The composer must sit above the IME — use `Modifier.imePadding()`.
- **No brand font**: GroupMe is system-font on purpose — use `FontFamily.Default` (Roboto). `sp` honors the user's font scale for message body, list titles, previews.
- **Pin layout-sensitive text**: keep tab labels (10sp), like-count numbers (10sp), and unread pill numbers (12sp) fixed by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` — bubble run-grouping is alignment-sensitive.
- **TalkBack**: label inbound bubbles "{sender} said: {text}"; label the like pill "Liked by {count}"; expose double-tap-to-like via `Modifier.semantics { customActions = listOf(CustomAccessibilityAction("Like") { …; true }) }` so it's reachable without the gesture; label the gallery block "{n} photos".
- **Touch targets**: Material guidance is 48.dp — give the 22dp tab icons, 11dp like heart, and 26dp attach button a 48.dp hit area via padding; list rows are 72.dp (full-row tappable); the send button is 38dp visual / 48dp hit.
- **Contrast**: white on `#00AFF0` passes WCAG AA for the 22sp+ nav title and 16sp bubble text; `#6A6A6A` on `#FFFFFF` passes AA for metadata. Validate any custom per-group accent used for sender names against its background.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the like-pill spring pop and bubble slide-in with a plain `Crossfade`; keep the like state visible (it conveys meaning).
- **Dark mode**: invert via the `Dark*` palette — `#121212`, NOT true black; `#1A1A1A` text becomes `#ECECEC`, inbound bubbles `#2A2A2C`. The chat top bar stays solid `#00AFF0` and outbound bubbles stay brand blue regardless of scheme. The like pill switches to a `#1C1C1E` fill with a `#2C2C2E` border as the dark elevation cue. Do **not** enable Material You `dynamicColorScheme()` — GroupMe's bright-blue identity must hold regardless of wallpaper.
