# iMessage (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports iMessage's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the bubble + tapback + typing system, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (iMessage's blue/gray bubble pair, the pinched tail, the tapback strip, the pulsing typing dots) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Popup` for the tapback strip, Roboto/SF-substitute via `sp`/`dp`. iMessage is light-mode-first; a true-black dark scheme is provided.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars. No color extraction — iMessage's palette is fixed system colors.

## 1. Color Tokens

```kotlin
// ui/theme/IMessageColors.kt
import androidx.compose.ui.graphics.Color

object IMessageColors {
    // Brand bubble colors (identical light & dark)
    val Blue        = Color(0xFF007AFF)
    val BluePressed = Color(0xFF0062CC)
    val Green       = Color(0xFF34C759)
    val GreenPressed = Color(0xFF248A3D)

    // Canvas & Surfaces (Light)
    val Canvas    = Color(0xFFFFFFFF)
    val Incoming  = Color(0xFFE9E9EB)
    val GroupedBg = Color(0xFFF2F2F7)
    val FieldBorder = Color(0xFFC6C6C8)

    // Canvas & Surfaces (Dark) — true black, NOT charcoal
    val DarkCanvas   = Color(0xFF000000)
    val IncomingDark = Color(0xFF26262A)
    val DarkSurface1 = Color(0xFF1C1C1E)
    val DarkSurface2 = Color(0xFF2C2C2E)
    val DarkBorder   = Color(0xFF38383A)

    // Text
    val TextPrimary     = Color(0xFF000000)
    val TextPrimaryDark = Color(0xFFFFFFFF)
    val OnAccent        = Color(0xFFFFFFFF)
    val TextSecondary     = Color(0x993C3C43) // 60% — secondaryLabel
    val TextSecondaryDark = Color(0x99EBEBF5)
    val TextTertiary      = Color(0x4D3C3C43) // 30% — Delivered / dots
    val TextTertiaryDark  = Color(0x4DEBEBF5)

    // Semantic
    val Red     = Color(0xFFFF3B30) // systemRed
    val RedDark = Color(0xFFFF453A)
    val Link    = Color(0xFF007AFF)
}
```

Wire it into both schemes. iMessage is light-first; dark uses true black `#000000`, never charcoal.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val IMessageLight = lightColorScheme(
    primary        = IMessageColors.Blue,
    onPrimary      = IMessageColors.OnAccent,
    background     = IMessageColors.Canvas,
    onBackground   = IMessageColors.TextPrimary,
    surface        = IMessageColors.GroupedBg,
    onSurface      = IMessageColors.TextPrimary,
    surfaceVariant = IMessageColors.Incoming,
    outline        = IMessageColors.FieldBorder,
    error          = IMessageColors.Red,
)

private val IMessageDark = darkColorScheme(
    primary        = IMessageColors.Blue,    // brand blue holds in dark
    onPrimary      = IMessageColors.OnAccent,
    background     = IMessageColors.DarkCanvas,
    onBackground   = IMessageColors.TextPrimaryDark,
    surface        = IMessageColors.DarkSurface1,
    onSurface      = IMessageColors.TextPrimaryDark,
    surfaceVariant = IMessageColors.IncomingDark,
    outline        = IMessageColors.DarkBorder,
    error          = IMessageColors.RedDark,
)

@Composable
fun IMessageTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) IMessageDark else IMessageLight,
    typography = IMessageTypography,
    content = content,
)
```

## 2. Typography (M3)

iMessage is pure system San Francisco at Dynamic Type sizes. On Android there is no SF Pro license; use the platform default (Roboto) or bundle [Inter](https://rsms.me/inter/) (SIL OFL) as the closest neutral substitute. Body 400, headline/footnote 600, titles 700; pt → sp 1:1.

```kotlin
// ui/theme/IMessageType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

// Use FontFamily.Default (Roboto) or a bundled Inter family for a closer SF match.
val IMsgFamily = FontFamily.Default

object IMessageText {
    val LargeTitle = TextStyle(IMsgFamily, fontWeight = FontWeight.Bold,     fontSize = 34.sp, lineHeight = 41.sp, letterSpacing = 0.4.sp)
    val Title1     = TextStyle(IMsgFamily, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = 0.36.sp)
    val Title3     = TextStyle(IMsgFamily, fontWeight = FontWeight.SemiBold, fontSize = 20.sp, lineHeight = 25.sp, letterSpacing = 0.38.sp)
    val Headline   = TextStyle(IMsgFamily, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.4).sp)
    val Body       = TextStyle(IMsgFamily, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.4).sp)
    val Callout    = TextStyle(IMsgFamily, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 21.sp, letterSpacing = (-0.3).sp)
    val Subhead    = TextStyle(IMsgFamily, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val Footnote   = TextStyle(IMsgFamily, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 18.sp, letterSpacing = (-0.08).sp)
    val Caption1   = TextStyle(IMsgFamily, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Caption2   = TextStyle(IMsgFamily, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 15.sp, letterSpacing = 0.06.sp)
    val Receipt    = TextStyle(IMsgFamily, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 16.sp)
}

val IMessageTypography = Typography(
    headlineLarge = IMessageText.LargeTitle,
    headlineMedium = IMessageText.Title1,
    titleMedium   = IMessageText.Headline,
    bodyLarge     = IMessageText.Body,
    labelSmall    = IMessageText.Caption2,
)
```

## 3. Signature Components

### Message Bubble (in / out, with tail)

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

enum class BubbleKind { Incoming, IMessage, Sms }

@Composable
fun ChatBubble(text: String, kind: BubbleKind, tailEnd: Boolean, dark: Boolean) {
    val outgoing = kind != BubbleKind.Incoming
    val bg = when (kind) {
        BubbleKind.IMessage -> IMessageColors.Blue
        BubbleKind.Sms      -> IMessageColors.Green
        BubbleKind.Incoming -> if (dark) IMessageColors.IncomingDark else IMessageColors.Incoming
    }
    val fg = if (kind == BubbleKind.Incoming)
        (if (dark) IMessageColors.TextPrimaryDark else IMessageColors.TextPrimary)
    else IMessageColors.OnAccent

    val big = 19.dp; val small = 6.dp
    val shape = RoundedCornerShape(
        topStart = big, topEnd = big,
        bottomStart = if (!outgoing && tailEnd) small else big,
        bottomEnd   = if ( outgoing && tailEnd) small else big,
    )

    Box(Modifier.fillMaxWidth(), contentAlignment = if (outgoing) Alignment.CenterEnd else Alignment.CenterStart) {
        Box(
            Modifier
                .fillMaxWidth(0.78f)
                .wrapContentWidth(if (outgoing) Alignment.End else Alignment.Start)
                .clip(shape)
                .background(bg)
                .padding(horizontal = 14.dp, vertical = 8.dp)
        ) {
            Text(text, style = IMessageText.Body, color = fg)
        }
    }
}
```

### Tapback Reaction Strip (Popup)

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.material3.Surface
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.window.Popup
import androidx.compose.ui.window.PopupProperties

private val Glyphs = listOf("❤️", "👍", "👎", "😂", "‼️", "❓")

@Composable
fun TapbackStrip(dark: Boolean, onPick: (String) -> Unit, onDismiss: () -> Unit) {
    Popup(onDismissRequest = onDismiss, properties = PopupProperties(focusable = true)) {
        Row(
            Modifier
                .clip(RoundedCornerShape(24.dp))
                .shadow(20.dp, RoundedCornerShape(24.dp), spotColor = Color.Black.copy(alpha = 0.12f))
                .background(if (dark) IMessageColors.DarkSurface2 else IMessageColors.Canvas)
                .then(if (dark) Modifier.border(0.5.dp, IMessageColors.DarkBorder, RoundedCornerShape(24.dp)) else Modifier)
                .padding(horizontal = 14.dp, vertical = 8.dp),
            horizontalArrangement = Arrangement.spacedBy(10.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Glyphs.forEach { g ->
                Box(Modifier.size(44.dp).clickable { onPick(g) }, contentAlignment = Alignment.Center) {
                    Text(g, fontSize = 28.sp)
                }
            }
        }
    }
}

// Docked chip over the owner-near top corner
@Composable
fun TapbackChip(glyph: String, dark: Boolean) {
    Surface(
        shape = RoundedCornerShape(14.dp),
        color = if (dark) IMessageColors.DarkSurface2 else IMessageColors.Canvas,
        border = androidx.compose.foundation.BorderStroke(0.5.dp, if (dark) IMessageColors.DarkBorder else IMessageColors.FieldBorder),
        modifier = Modifier.offset(y = (-16).dp),
    ) { Text(glyph, fontSize = 12.sp, modifier = Modifier.padding(6.dp)) }
}
```

### Typing Indicator

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.shape.RoundedCornerShape

@Composable
fun TypingIndicator(dark: Boolean) {
    val transition = rememberInfiniteTransition(label = "typing")
    Row(
        Modifier
            .clip(RoundedCornerShape(topStart = 19.dp, topEnd = 19.dp, bottomEnd = 19.dp, bottomStart = 6.dp))
            .background(if (dark) IMessageColors.IncomingDark else IMessageColors.Incoming)
            .padding(horizontal = 16.dp, vertical = 12.dp),
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        repeat(3) { i ->
            val scale by transition.animateFloat(
                initialValue = 0.6f, targetValue = 1f,
                animationSpec = infiniteRepeatable(
                    tween(700, delayMillis = i * 200, easing = EaseInOut),
                    RepeatMode.Reverse,
                ), label = "dot$i",
            )
            Box(
                Modifier
                    .size(7.dp)
                    .graphicsLayerScale(scale)
                    .clip(androidx.compose.foundation.shape.CircleShape)
                    .background(if (dark) IMessageColors.TextTertiaryDark else IMessageColors.TextTertiary)
            )
        }
    }
}

private fun Modifier.graphicsLayerScale(s: Float) = this.then(
    Modifier.graphicsLayer { scaleX = s; scaleY = s }
)
```

### Delivery Receipt

```kotlin
@Composable
fun DeliveryReceipt(label: String, dark: Boolean) {
    Box(Modifier.fillMaxWidth().padding(end = 6.dp, top = 2.dp), contentAlignment = Alignment.CenterEnd) {
        Text(label, style = IMessageText.Receipt,
             color = if (dark) IMessageColors.TextSecondaryDark else IMessageColors.TextSecondary)
    }
}
```

### Compose Bar

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material3.Icon
import androidx.compose.runtime.*

@Composable
fun ComposeBar(isIMessage: Boolean, dark: Boolean, onSend: (String) -> Unit) {
    var text by remember { mutableStateOf("") }
    val empty = text.isBlank()
    Row(
        Modifier
            .fillMaxWidth()
            .background((if (dark) IMessageColors.DarkCanvas else IMessageColors.Canvas).copy(alpha = 0.92f))
            .padding(horizontal = 14.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box(
            Modifier.size(30.dp).clip(androidx.compose.foundation.shape.CircleShape)
                .background(if (dark) IMessageColors.DarkSurface2 else IMessageColors.Incoming),
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Add, "Attach", tint = if (dark) IMessageColors.TextSecondaryDark else IMessageColors.TextSecondary, modifier = Modifier.size(16.dp)) }

        Box(
            Modifier.weight(1f).heightIn(min = 36.dp)
                .clip(RoundedCornerShape(18.dp))
                .border(1.dp, if (dark) IMessageColors.DarkBorder else IMessageColors.FieldBorder, RoundedCornerShape(18.dp))
                .padding(horizontal = 14.dp, vertical = 8.dp),
        ) {
            BasicTextField(
                value = text, onValueChange = { text = it },
                textStyle = IMessageText.Body.copy(color = if (dark) IMessageColors.TextPrimaryDark else IMessageColors.TextPrimary),
                decorationBox = { inner ->
                    if (empty) Text(if (isIMessage) "iMessage" else "Text Message",
                        style = IMessageText.Body, color = if (dark) IMessageColors.TextTertiaryDark else IMessageColors.TextTertiary)
                    inner()
                },
            )
        }

        Box(
            Modifier.size(30.dp).clip(androidx.compose.foundation.shape.CircleShape)
                .background(if (empty) Color(0xFF8E8E93) else if (isIMessage) IMessageColors.Blue else IMessageColors.Green)
                .clickable(enabled = !empty) { onSend(text); text = "" },
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.ArrowUpward, "Send", tint = Color.White, modifier = Modifier.size(15.dp)) }
    }
}
```

## 4. Navigation

iMessage's chrome is a centered nav title (avatar + name) and a blur-backed compose bar — not a tab strip. The Messages app shell is effectively single-screen; if you host it in a `NavigationBar` there is no tint pill (active = `systemBlue`).

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.ChatBubble

@Composable
fun IMessageBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = IMessageColors.Canvas, tonalElevation = 0.dp) {
        NavigationBarItem(
            selected = selected == 0,
            onClick = { onSelect(0) },
            icon = { Icon(Icons.Filled.ChatBubble, "Messages", Modifier.size(24.dp)) },
            label = { Text("Messages", style = IMessageText.Caption2) },
            colors = NavigationBarItemDefaults.colors(
                selectedIconColor = IMessageColors.Blue,
                selectedTextColor = IMessageColors.Blue,
                unselectedIconColor = Color(0xFF8E8E93),
                unselectedTextColor = Color(0xFF8E8E93),
                indicatorColor = Color.Transparent, // no Material pill — iMessage has none
            ),
        )
    }
}
```

The chat nav title is a `Column` { 34.dp gradient avatar + Row(name `Caption2`/`Footnote`, `ChevronDown` 9.dp) }, hosted in a `CenterAlignedTopAppBar` `title` slot; trailing `Videocam` for FaceTime, leading `ArrowBack` tinted `Blue`.

## 5. Motion

iMessage motion is quiet spring (0.2–0.35s) outside of message effects.

| Moment | Compose recipe |
|--------|----------------|
| Outgoing bubble send | new bubble `AnimatedVisibility` `scaleIn(initialScale = 0.6f, spring(dampingRatio = 0.75f))` + `slideInVertically { it }` |
| Tapback present | long-press `detectTapGestures(onLongPress)` → `Popup` content `scaleIn(0.7f) + fadeIn` `tween(180)` |
| Tapback dock | chip `scaleIn(spring(dampingRatio = 0.6f))` + soft haptic |
| Typing dots | `rememberInfiniteTransition`, 3 dots `tween(700, delay = i*200)` `RepeatMode.Reverse`, scale 0.6 ↔ 1 |
| Delivered → Read | `Crossfade(targetState = receipt, tween(200))` |
| Message effect (Slam) | `animateFloatAsState` scale 1.3 → 1 `spring(stiffness = Spring.StiffnessHigh)` + screen-shake offset |
| Scroll-to-bottom | new-message chevron pill; `animateScrollToItem` `tween(300)` |

```kotlin
// Canonical outgoing-bubble entrance
AnimatedVisibility(
    visible = true,
    enter = scaleIn(initialScale = 0.6f, animationSpec = spring(dampingRatio = 0.75f, stiffness = Spring.StiffnessMediumLow)) +
            slideInVertically(spring()) { it / 2 },
) { ChatBubble(/* … */) }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` on send and tapback dock; for message effects use `HapticFeedbackConstants.CONFIRM` via `LocalView`. Auto-delivery is silent — only the "Delivered"/"Read" crossfade, no haptic.

## 6. Icons

iMessage uses SF Symbols; the closest first-party Compose set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Contact disclosure | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| FaceTime | `video` | `Icons.Filled.Videocam` |
| Compose (list) | `square.and.pencil` | `Icons.Filled.Edit` |
| Attachment | `plus` | `Icons.Filled.Add` |
| Send | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Failed | `exclamationmark.circle.fill` | `Icons.Filled.Error` |
| Unread dot | `circle.fill` | `Icons.Filled.Circle` |
| Pin | `pin.fill` | `Icons.Filled.PushPin` |
| Mute | `bell.slash.fill` | `Icons.Filled.NotificationsOff` |
| Delete | `trash.fill` | `Icons.Filled.Delete` |
| Camera | `camera.fill` | `Icons.Filled.PhotoCamera` |
| Photos | `photo.on.rectangle` | `Icons.Filled.Photo` |
| Audio message | `mic.fill` | `Icons.Filled.Mic` |
| App strip | `circle.grid.3x3.fill` | `Icons.Filled.Apps` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; `Popup` + spring motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: `enableEdgeToEdge()`; white canvas wants dark system-bar content (light-content in dark mode, which is true black). The compose bar must sit above the IME — apply `Modifier.imePadding()` to the thread `LazyColumn`.
- **Font**: there is no SF Pro on Android — use `FontFamily.Default` (Roboto) or bundle Inter (SIL OFL) as a neutral substitute; keep weights 400/600/700. `sp` honors the user's font scale.
- **Font scaling**: scale message body, headline, list previews; pin layout-sensitive text (receipts, 11sp captions, tab labels, typing dots) via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: label bubbles "You said {text}" / "{Name} said {text}"; reactions "{Name} reacted {glyph}"; expose Tapback via `Modifier.semantics { customActions = listOf(CustomAccessibilityAction("React") { … }) }` so it's reachable without long-press; announce delivery state via `stateDescription`.
- **Touch targets**: Material guidance is 48.dp — the 30.dp send/`+` buttons and 28.dp tapback glyphs already use 44–48.dp hit boxes; bubbles are full-width-press targets for the tapback long-press.
- **Contrast**: white on `#007AFF` and `#34C759` passes WCAG AA; primary text on `#E9E9EB`/`#26262A` passes AA. Direction is reinforced by left/right alignment + tail so meaning survives color-blindness.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the bubble spring-in and message-effect playback (show the static end-state); keep the typing-dot loop subtle or replace with "typing…".
- **Dark mode**: invert via the `Dark*` palette — true black `#000000`, NOT charcoal; brand `#007AFF`/`#34C759` hold; incoming → `#26262A`, text → white. Shadows vanish on black, so add a 0.5dp `DarkBorder` to floating popovers (tapback strip, context menu) as the elevation cue. Do **not** enable Material You `dynamicColorScheme()` — the blue/green bubble identity must hold regardless of wallpaper.
