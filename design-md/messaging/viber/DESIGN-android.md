# Viber (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Viber's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the bubble + three-state receipt + free-call banner, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Viber's single purple brand, the gray/purple bubble pair, the gray→gray→violet check receipt, the call-first chrome, the sticker market) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Popup` for reactions, `sp`/`dp`. Viber is light-first; an aubergine-tinted dark scheme is provided.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars / stickers.

## 1. Color Tokens

```kotlin
// ui/theme/ViberColors.kt
import androidx.compose.ui.graphics.Color

object ViberColors {
    // Brand purple
    val Purple        = Color(0xFF7360F2)
    val PurpleDeep    = Color(0xFF665CAC)
    val PurplePressed = Color(0xFF5B4ACC)
    val Violet        = Color(0xFF8F7DF7)
    val BannerDeep    = Color(0xFF59267C)

    // Canvas & Surfaces (Light)
    val Canvas      = Color(0xFFFFFFFF)
    val Incoming    = Color(0xFFEDEBF5)
    val SurfaceGray = Color(0xFFF4F3F8)
    val RowPressed  = Color(0xFFE9E7F2)
    val Divider     = Color(0xFFE4E2EC)

    // Canvas & Surfaces (Dark) — aubergine-tinted, NOT neutral
    val DarkCanvas   = Color(0xFF121118)
    val DarkSurface1 = Color(0xFF1C1A24)
    val DarkSurface2 = Color(0xFF26232F)
    val DarkDivider  = Color(0xFF322F3C)

    // Text
    val TextPrimary       = Color(0xFF1A1825)
    val TextPrimaryDark   = Color(0xFFF3F1F8)
    val OnPurple          = Color(0xFFFFFFFF)
    val TextSecondary     = Color(0xFF7D7A8C)
    val TextSecondaryDark = Color(0xFF9D98AC)
    val TextTertiary      = Color(0xFFA8A5B5)
    val TextTertiaryDark  = Color(0xFF6C6779)

    // Semantic
    val Green = Color(0xFF46C26A)
    val Red   = Color(0xFFF0506E)
    val Link  = Color(0xFF7360F2)
}
```

Wire it into both schemes. Viber is light-first; dark uses the aubergine-tinted `#121118`, never neutral gray.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val ViberLight = lightColorScheme(
    primary        = ViberColors.Purple,
    onPrimary      = ViberColors.OnPurple,
    background     = ViberColors.Canvas,
    onBackground   = ViberColors.TextPrimary,
    surface        = ViberColors.SurfaceGray,
    onSurface      = ViberColors.TextPrimary,
    surfaceVariant = ViberColors.Incoming,
    outline        = ViberColors.Divider,
    error          = ViberColors.Red,
)

private val ViberDark = darkColorScheme(
    primary        = ViberColors.Purple,   // brand purple holds in dark
    onPrimary      = ViberColors.OnPurple,
    background     = ViberColors.DarkCanvas,
    onBackground   = ViberColors.TextPrimaryDark,
    surface        = ViberColors.DarkSurface1,
    onSurface      = ViberColors.TextPrimaryDark,
    surfaceVariant = ViberColors.DarkSurface2,
    outline        = ViberColors.DarkDivider,
    error          = ViberColors.Red,
)

@Composable
fun ViberTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) ViberDark else ViberLight,
    typography = ViberTypography,
    content = content,
)
```

## 2. Typography (M3)

Viber's brand face is proprietary; bundle **Manrope** (SIL OFL) in `res/font/` as the closest free substitute. Heavy 700/800 titles/buttons give the friendly feel; body stays 400. pt → sp 1:1.

```kotlin
// ui/theme/ViberType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Manrope = FontFamily(
    Font(R.font.manrope_regular,    FontWeight.Normal),
    Font(R.font.manrope_medium,     FontWeight.Medium),
    Font(R.font.manrope_semibold,   FontWeight.SemiBold),
    Font(R.font.manrope_bold,       FontWeight.Bold),
    Font(R.font.manrope_extrabold,  FontWeight.ExtraBold),
)

object ViberText {
    val LargeTitle = TextStyle(Manrope, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.4).sp)
    val Title1     = TextStyle(Manrope, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Title3     = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 27.sp, letterSpacing = (-0.2).sp)
    val Headline   = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.2).sp)
    val Body       = TextStyle(Manrope, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 20.sp)
    val BodyEmph   = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Preview    = TextStyle(Manrope, fontWeight = FontWeight.Medium,    fontSize = 14.sp, lineHeight = 19.sp)
    val Footnote   = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 18.sp)
    val Caption    = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 15.sp, letterSpacing = 0.1.sp)
    val Button     = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab        = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.1.sp)
    val Badge      = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 11.sp)
}

val ViberTypography = Typography(
    headlineLarge = ViberText.LargeTitle,
    headlineMedium = ViberText.Title1,
    titleMedium   = ViberText.Headline,
    bodyLarge     = ViberText.Body,
    labelSmall    = ViberText.Tab,
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

enum class ReceiptState { Sending, Sent, Delivered, Seen, Failed }

@Composable
fun ViberBubble(text: String, outgoing: Boolean, tailEnd: Boolean, timestamp: String, receipt: ReceiptState, dark: Boolean) {
    val shape = RoundedCornerShape(
        topStart = 16.dp, topEnd = 16.dp,
        bottomStart = if (!outgoing && tailEnd) 5.dp else 16.dp,
        bottomEnd   = if ( outgoing && tailEnd) 5.dp else 16.dp,
    )
    Column(
        Modifier.fillMaxWidth(),
        horizontalAlignment = if (outgoing) Alignment.End else Alignment.Start,
    ) {
        Box(
            Modifier
                .fillMaxWidth(0.80f)
                .wrapContentWidth(if (outgoing) Alignment.End else Alignment.Start)
                .clip(shape)
                .background(if (outgoing) ViberColors.Purple else if (dark) ViberColors.DarkSurface2 else ViberColors.Incoming)
                .padding(horizontal = 14.dp, vertical = 9.dp)
        ) {
            Text(text, style = ViberText.Body,
                color = if (outgoing) ViberColors.OnPurple else if (dark) ViberColors.TextPrimaryDark else ViberColors.TextPrimary)
        }
        if (outgoing) {
            Row(Modifier.padding(top = 3.dp, end = 4.dp), verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(timestamp, fontSize = 10.sp, color = if (dark) ViberColors.TextSecondaryDark else ViberColors.TextSecondary)
                ReceiptCheck(receipt, dark)
            }
        }
    }
}
```

### Three-State Check Receipt

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material.icons.filled.DoneAll
import androidx.compose.material.icons.filled.Schedule
import androidx.compose.material.icons.filled.ErrorOutline
import androidx.compose.material3.Icon

@Composable
fun ReceiptCheck(state: ReceiptState, dark: Boolean) {
    val color = when (state) {
        ReceiptState.Sending, ReceiptState.Sent -> if (dark) ViberColors.TextTertiaryDark else ViberColors.TextTertiary
        ReceiptState.Delivered -> if (dark) ViberColors.TextSecondaryDark else ViberColors.TextSecondary
        ReceiptState.Seen -> ViberColors.Violet
        ReceiptState.Failed -> ViberColors.Red
    }
    val icon = when (state) {
        ReceiptState.Sending -> Icons.Filled.Schedule
        ReceiptState.Sent -> Icons.Filled.Check
        ReceiptState.Delivered, ReceiptState.Seen -> Icons.Filled.DoneAll
        ReceiptState.Failed -> Icons.Filled.ErrorOutline
    }
    Icon(icon, contentDescription = state.name, tint = color, modifier = Modifier.size(if (state == ReceiptState.Delivered || state == ReceiptState.Seen) 16.dp else 13.dp))
}
```

### Free Viber Call Banner

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Brush
import androidx.compose.material.icons.filled.Call
import androidx.compose.foundation.clickable

@Composable
fun FreeCallBanner(name: String, onCall: () -> Unit) {
    Row(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 14.dp)
            .padding(top = 10.dp)
            .shadow(8.dp, RoundedCornerShape(14.dp), spotColor = ViberColors.Purple.copy(alpha = 0.10f))
            .clip(RoundedCornerShape(14.dp))
            .background(Brush.linearGradient(listOf(ViberColors.Purple, ViberColors.BannerDeep)))
            .padding(12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(Icons.Filled.Call, null, tint = Color(0xFFFFFFFF), modifier = Modifier.size(18.dp))
        Column(Modifier.weight(1f)) {
            Text("Free Viber Call", color = Color(0xFFFFFFFF), fontSize = 13.sp, fontWeight = FontWeight.Bold)
            Text("Call $name free over Viber-to-Viber", color = Color(0xCCFFFFFF), fontSize = 11.sp)
        }
        Box(
            Modifier.clip(RoundedCornerShape(999.dp)).background(Color(0x38FFFFFF)).clickable { onCall() }
                .padding(horizontal = 12.dp, vertical = 6.dp)
        ) { Text("Call", color = Color(0xFFFFFFFF), fontSize = 12.sp, fontWeight = FontWeight.Bold) }
    }
}
```

### In-Thread Sticker

```kotlin
@Composable
fun StickerMessage(outgoing: Boolean, timestamp: String, receipt: ReceiptState, dark: Boolean) {
    Column(Modifier.fillMaxWidth(), horizontalAlignment = if (outgoing) Alignment.End else Alignment.Start) {
        // Replace with Coil GIF / Lottie animated sticker
        Box(
            Modifier.size(96.dp).clip(RoundedCornerShape(20.dp))
                .background(Brush.radialGradient(listOf(ViberColors.Violet, ViberColors.BannerDeep)))
        )
        if (outgoing) {
            Row(Modifier.padding(top = 3.dp, end = 4.dp), horizontalArrangement = Arrangement.spacedBy(4.dp), verticalAlignment = Alignment.CenterVertically) {
                Text(timestamp, fontSize = 10.sp, color = if (dark) ViberColors.TextSecondaryDark else ViberColors.TextSecondary)
                ReceiptCheck(receipt, dark)
            }
        }
    }
}
```

## 4. Navigation

Viber is call-first: phone + video in every chat header, a dedicated Calls tab. On Android model the strip as a `NavigationBar` with **no tint pill** — active is brand purple.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun ViberBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = ViberColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Chats" to Icons.Filled.ChatBubble,
            "Calls" to Icons.Filled.Call,
            "Explore" to Icons.Filled.Mood,
            "More" to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (label == "Explore") BadgedBox(badge = { Badge(containerColor = ViberColors.Red) { Text("3") } }) {
                        Icon(icon, label, Modifier.size(22.dp))
                    } else Icon(icon, label, Modifier.size(22.dp))
                },
                label = { Text(label, style = ViberText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = ViberColors.Purple,
                    selectedTextColor = ViberColors.Purple,
                    unselectedIconColor = ViberColors.TextTertiary,
                    unselectedTextColor = ViberColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Viber has none
                ),
            )
        }
    }
}
```

The chat header is a `Row` { back `ChevronLeft` tinted `Purple`, 36.dp gradient avatar, `Column`(name `Headline`, "online" `Caption` tinted `Green`), spacer, `Call` 22.dp + `Videocam` 22.dp tinted `Purple` } in a `TopAppBar` — call icons always present (call-first DNA).

## 5. Motion

Viber motion is soft spring, 0.2–0.32s.

| Moment | Compose recipe |
|--------|----------------|
| Outgoing bubble send | new bubble `slideInVertically { it } + scaleIn(initialScale = 0.85f, spring(dampingRatio = 0.78f))` |
| Receipt sent → delivered → seen | `Crossfade(receipt, tween(200))`; on `Seen` add `animateFloatAsState` scale 1 → 1.15 → 1 pulse + recolor to `Violet` |
| Reactions strip | long-press `detectTapGestures(onLongPress)` → `Popup` `scaleIn(0.7f) + fadeIn` `tween(180)` |
| Sticker play | Coil `ImageDecoderDecoder` GIF / Lottie auto-loop ~2s |
| Sticker market open | `AnimatedVisibility` `slideInVertically { it } + fadeIn` `tween(300)` |
| Incoming call | full purple `Brush.linearGradient` `fadeIn(300)`; accept/decline `scaleIn(spring())` |
| Tab switch | instant; icon tint `animateColorAsState` `tween(150)` |

```kotlin
// Canonical outgoing-bubble entrance
AnimatedVisibility(
    visible = true,
    enter = slideInVertically(spring(dampingRatio = 0.78f)) { it / 2 } +
            scaleIn(initialScale = 0.85f, animationSpec = spring(dampingRatio = 0.78f)),
) { ViberBubble(/* … */) }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for send and reaction dock; `HapticFeedbackConstants.CONFIRM` (via `LocalView`) on call connect; `HapticFeedbackConstants.REJECT` on decline. Receipt transitions are silent (visual only).

## 6. Icons

Viber uses a custom brand glyph set; the closest first-party Compose set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Back | `chevron.left` | `Icons.Filled.ChevronLeft` |
| Voice call | `phone` | `Icons.Filled.Call` |
| Video call | `video` | `Icons.Filled.Videocam` |
| Attachment | `plus` | `Icons.Filled.Add` |
| Emoji / sticker | `face.smiling` | `Icons.Filled.Mood` |
| Send | `paperplane.fill` | `Icons.Filled.Send` |
| Voice message | `mic.fill` | `Icons.Filled.Mic` |
| Sent | `checkmark` | `Icons.Filled.Check` |
| Delivered / Seen | `checkmark` ×2 | `Icons.Filled.DoneAll` |
| Sending | `clock` | `Icons.Filled.Schedule` |
| Failed | `exclamationmark.circle.fill` | `Icons.Filled.ErrorOutline` |
| Chats (tab) | `bubble.left.fill` | `Icons.Filled.ChatBubble` |
| Calls (tab) | `phone.fill` | `Icons.Filled.Call` |
| Explore (tab) | `smiley` | `Icons.Filled.Mood` |
| More (tab) | `person.crop.circle` | `Icons.Filled.Person` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Reaction | `hand.thumbsup.fill` | `Icons.Filled.ThumbUp` |
| Secret message | `timer` | `Icons.Filled.Timer` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; `Popup` + spring comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: `enableEdgeToEdge()`; white canvas wants dark system-bar content (light-content on the aubergine dark canvas). Compose bar above the IME — `Modifier.imePadding()` on the thread `LazyColumn`.
- **Font**: Viber's brand face is proprietary — bundle Manrope (SIL OFL) in `res/font/`; keep ExtraBold/Bold for titles/buttons. `sp` honors the user's font scale.
- **Font scaling**: scale titles/headline/body/previews; pin layout-sensitive text (timestamps, receipt glyphs, 11sp tab labels, badges, presence, sticker artwork) via a fixed-`fontScale` `LocalDensity`.
- **TalkBack**: label outgoing bubbles "You said {text}, {receipt}"; the receipt is conveyed by glyph shape (clock / 1 check / 2 checks) AND color, so it survives color-blindness; expose Reactions via `Modifier.semantics { customActions = … }`; label stickers by pack/emoji name.
- **Touch targets**: Material guidance 48.dp — give the 22.dp header call icons, 22.dp compose icons, and 28.dp reaction glyphs a 48.dp hit box; the 36.dp send button and list rows are full-press.
- **Contrast**: white on `#7360F2` passes WCAG AA; primary text on `#EDEBF5`/`#26232F` passes AA; the violet seen-check is paired with the double-check shape.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable bubble spring-in (crossfade), stop sticker auto-loop (first frame, play on tap), disable the seen-pulse.
- **Dark mode**: invert via the `Dark*` palette — aubergine `#121118`, NOT neutral gray; brand `#7360F2` holds; incoming → `#26232F`, text → `#F3F1F8`. Shadows fade on dark, so add a 1dp `DarkDivider` border to floating menus (reactions, context) as the elevation cue. Do **not** enable Material You `dynamicColorScheme()` — Viber's single-purple identity must hold regardless of wallpaper.
