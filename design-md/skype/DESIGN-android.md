# Skype (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Skype's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the bubble + inline call card + video grid, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Skype's cloud-blue brand, the gray/blue bubble pair, the call-first chrome, inline call cards, the gradient video grid, presence dots) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Popup` for reactions, `sp`/`dp`. Skype is light-first; a neutral near-black dark scheme is provided.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars.

## 1. Color Tokens

```kotlin
// ui/theme/SkypeColors.kt
import androidx.compose.ui.graphics.Color

object SkypeColors {
    // Brand Skype Blue
    val Blue        = Color(0xFF00AFF0)
    val BlueDeep    = Color(0xFF0078D4)
    val BluePressed = Color(0xFF0091CC)
    val Cyan        = Color(0xFF34C3FF)
    val InkOnBright = Color(0xFF00343F)

    // Canvas & Surfaces (Light)
    val Canvas      = Color(0xFFFFFFFF)
    val Incoming    = Color(0xFFEBEBEF)
    val SurfaceGray = Color(0xFFF4F4F6)
    val RowPressed  = Color(0xFFE8E8EC)
    val Divider     = Color(0xFFE2E2E6)

    // Canvas & Surfaces (Dark) — neutral near-black
    val DarkCanvas   = Color(0xFF16161A)
    val DarkSurface1 = Color(0xFF1F1F24)
    val DarkSurface2 = Color(0xFF2A2A30)
    val DarkDivider  = Color(0xFF34343C)

    // Text
    val TextPrimary       = Color(0xFF1B1B1F)
    val TextPrimaryDark   = Color(0xFFF2F2F4)
    val OnBlue            = Color(0xFFFFFFFF)
    val TextSecondary     = Color(0xFF6E6E78)
    val TextSecondaryDark = Color(0xFFA4A4AE)
    val TextTertiary      = Color(0xFF9A9AA4)
    val TextTertiaryDark  = Color(0xFF6F6F79)

    // Presence & semantic
    val Green  = Color(0xFF2DC26B) // active / accept
    val Yellow = Color(0xFFFFC400) // away
    val Red    = Color(0xFFE8364F) // end / DND / error
    val Link   = Color(0xFF0078D4)
}
```

Wire it into both schemes. Skype is light-first; dark uses a neutral near-black `#16161A`, never color-cast.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val SkypeLight = lightColorScheme(
    primary        = SkypeColors.Blue,
    onPrimary      = SkypeColors.InkOnBright,   // deep-cyan ink on bright blue (AA)
    secondary      = SkypeColors.BlueDeep,
    background     = SkypeColors.Canvas,
    onBackground   = SkypeColors.TextPrimary,
    surface        = SkypeColors.SurfaceGray,
    onSurface      = SkypeColors.TextPrimary,
    surfaceVariant = SkypeColors.Incoming,
    outline        = SkypeColors.Divider,
    error          = SkypeColors.Red,
)

private val SkypeDark = darkColorScheme(
    primary        = SkypeColors.Blue,
    onPrimary      = SkypeColors.InkOnBright,
    secondary      = SkypeColors.BlueDeep,
    background     = SkypeColors.DarkCanvas,
    onBackground   = SkypeColors.TextPrimaryDark,
    surface        = SkypeColors.DarkSurface1,
    onSurface      = SkypeColors.TextPrimaryDark,
    surfaceVariant = SkypeColors.DarkSurface2,
    outline        = SkypeColors.DarkDivider,
    error          = SkypeColors.Red,
)

@Composable
fun SkypeTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) SkypeDark else SkypeLight,
    typography = SkypeTypography,
    content = content,
)
```

## 2. Typography (M3)

Skype's brand face is Segoe UI (proprietary cross-platform); bundle **Inter** (SIL OFL) in `res/font/` as the closest free substitute. Heavy 700/800 titles/buttons; body 400. pt → sp 1:1.

```kotlin
// ui/theme/SkypeType.kt
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
)

object SkypeText {
    val LargeTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.4).sp)
    val Title1     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Title3     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 27.sp, letterSpacing = (-0.2).sp)
    val Headline   = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.2).sp)
    val Body       = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 20.sp)
    val BodyEmph   = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Preview    = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 14.sp, lineHeight = 19.sp)
    val Footnote   = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 18.sp)
    val Caption    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 15.sp, letterSpacing = 0.1.sp)
    val Button     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab        = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.1.sp)
    val Badge      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 11.sp)
}

val SkypeTypography = Typography(
    headlineLarge = SkypeText.LargeTitle,
    headlineMedium = SkypeText.Title1,
    titleMedium   = SkypeText.Headline,
    bodyLarge     = SkypeText.Body,
    labelSmall    = SkypeText.Tab,
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
import androidx.compose.ui.unit.sp

@Composable
fun SkypeBubble(text: String, outgoing: Boolean, tailEnd: Boolean, meta: String, dark: Boolean) {
    val shape = RoundedCornerShape(
        topStart = 14.dp, topEnd = 14.dp,
        bottomStart = if (!outgoing && tailEnd) 4.dp else 14.dp,
        bottomEnd   = if ( outgoing && tailEnd) 4.dp else 14.dp,
    )
    Column(Modifier.fillMaxWidth(), horizontalAlignment = if (outgoing) Alignment.End else Alignment.Start) {
        Box(
            Modifier
                .fillMaxWidth(0.80f)
                .wrapContentWidth(if (outgoing) Alignment.End else Alignment.Start)
                .clip(shape)
                .background(if (outgoing) SkypeColors.BlueDeep else if (dark) SkypeColors.DarkSurface2 else SkypeColors.Incoming)
                .padding(horizontal = 14.dp, vertical = 9.dp)
        ) {
            Text(text, style = SkypeText.Body,
                color = if (outgoing) SkypeColors.OnBlue else if (dark) SkypeColors.TextPrimaryDark else SkypeColors.TextPrimary)
        }
        if (outgoing) {
            Text(meta, fontSize = 10.sp, color = if (dark) SkypeColors.TextTertiaryDark else SkypeColors.TextTertiary,
                modifier = Modifier.padding(top = 4.dp, end = 4.dp))
        }
    }
}
```

### Inline Call Card

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Call
import androidx.compose.material.icons.filled.CallMissed
import androidx.compose.material3.Icon

@Composable
fun CallCard(title: String, subtitle: String, answered: Boolean, dark: Boolean) {
    val tint = if (answered) SkypeColors.Green else SkypeColors.Red
    Row(
        Modifier
            .clip(RoundedCornerShape(14.dp))
            .background(if (dark) SkypeColors.DarkSurface2 else SkypeColors.SurfaceGray)
            .padding(horizontal = 14.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box(
            Modifier.size(30.dp).clip(androidx.compose.foundation.shape.CircleShape)
                .background(tint.copy(alpha = 0.18f)),
            contentAlignment = Alignment.Center,
        ) { Icon(if (answered) Icons.Filled.Call else Icons.Filled.CallMissed, null, tint = tint, modifier = Modifier.size(15.dp)) }
        Column {
            Text(title, fontSize = 13.sp, fontWeight = FontWeight.Bold,
                color = if (dark) SkypeColors.TextPrimaryDark else SkypeColors.TextPrimary)
            Text(subtitle, fontSize = 11.sp,
                color = if (dark) SkypeColors.TextSecondaryDark else SkypeColors.TextSecondary)
        }
    }
}
```

### Video Tile + Control Bar

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.material.icons.filled.*
import androidx.compose.ui.graphics.Brush

@Composable
fun VideoTile(name: String, muted: Boolean, gradient: List<Color>, modifier: Modifier = Modifier) {
    Box(
        modifier.clip(RoundedCornerShape(14.dp)).background(Brush.linearGradient(gradient)),
    ) {
        Box(
            Modifier.align(Alignment.BottomStart).padding(8.dp)
                .clip(RoundedCornerShape(999.dp)).background(Color.Black.copy(alpha = 0.35f))
                .padding(horizontal = 8.dp, vertical = 4.dp)
        ) { Text(name, color = Color.White, fontSize = 11.sp, fontWeight = FontWeight.SemiBold) }
        if (muted) {
            Box(
                Modifier.align(Alignment.TopEnd).padding(8.dp).size(18.dp)
                    .clip(androidx.compose.foundation.shape.CircleShape).background(Color.Black.copy(alpha = 0.45f)),
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Filled.MicOff, null, tint = Color.White, modifier = Modifier.size(10.dp)) }
        }
    }
}

@Composable
fun CallControlBar(muted: Boolean, videoOn: Boolean, onMute: () -> Unit, onVideo: () -> Unit, onEnd: () -> Unit) {
    @Composable
    fun Ctrl(icon: androidx.compose.ui.graphics.vector.ImageVector, bg: Color, onClick: () -> Unit) {
        Box(
            Modifier.size(56.dp).clip(androidx.compose.foundation.shape.CircleShape).background(bg).clickable(onClick = onClick),
            contentAlignment = Alignment.Center,
        ) { Icon(icon, null, tint = Color.White, modifier = Modifier.size(22.dp)) }
    }
    Row(
        Modifier.clip(RoundedCornerShape(999.dp)).background(Color.Black.copy(alpha = 0.4f)).padding(16.dp),
        horizontalArrangement = Arrangement.spacedBy(14.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Ctrl(if (muted) Icons.Filled.MicOff else Icons.Filled.Mic, SkypeColors.DarkSurface2, onMute)
        Ctrl(if (videoOn) Icons.Filled.Videocam else Icons.Filled.VideocamOff, SkypeColors.DarkSurface2, onVideo)
        Ctrl(Icons.Filled.ScreenShare, SkypeColors.DarkSurface2) {}
        Ctrl(Icons.Filled.CallEnd, SkypeColors.Red, onEnd)
    }
}
```

### Presence Dot

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.CircleShape

enum class Presence { Active, Away, Dnd, Offline, InCall }

@Composable
fun PresenceDot(presence: Presence, dark: Boolean, modifier: Modifier = Modifier) {
    val ring = if (dark) SkypeColors.DarkCanvas else SkypeColors.Canvas
    val color = when (presence) {
        Presence.Active -> SkypeColors.Green
        Presence.Away   -> SkypeColors.Yellow
        Presence.Dnd    -> SkypeColors.Red
        Presence.InCall -> SkypeColors.Blue
        Presence.Offline -> Color.Transparent
    }
    Box(
        modifier.size(11.dp).clip(CircleShape).background(color)
            .border(2.dp, if (presence == Presence.Offline) SkypeColors.TextTertiary else ring, CircleShape)
    )
}
```

## 4. Navigation

Skype is call-first: phone + video in every chat header, dedicated Calls + Contacts tabs. On Android model the strip as a `NavigationBar` with **no tint pill** — active is bright Skype Blue.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun SkypeBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = SkypeColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Chats" to Icons.Filled.ChatBubble,
            "Calls" to Icons.Filled.Call,
            "Contacts" to Icons.Filled.People,
            "Notifications" to Icons.Filled.Notifications,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (label == "Contacts") BadgedBox(badge = { Badge(containerColor = SkypeColors.Red) { Text("5") } }) {
                        Icon(icon, label, Modifier.size(22.dp))
                    } else Icon(icon, label, Modifier.size(22.dp))
                },
                label = { Text(label, style = SkypeText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = SkypeColors.Blue,
                    selectedTextColor = SkypeColors.Blue,
                    unselectedIconColor = SkypeColors.TextTertiary,
                    unselectedTextColor = SkypeColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Skype has none
                ),
            )
        }
    }
}
```

The chat header is a `Row` { back `ChevronLeft` tinted `Blue`, 36.dp gradient avatar with a `PresenceDot` overlay, `Column`(name `Headline`, status `Caption` tinted `Green`), spacer, `Call` 22.dp + `Videocam` 22.dp tinted `Blue` } in a `TopAppBar` — call icons always present (call-first DNA).

## 5. Motion

Skype motion is soft spring, 0.15–0.32s.

| Moment | Compose recipe |
|--------|----------------|
| Outgoing bubble send | new bubble `slideInVertically { it } + scaleIn(initialScale = 0.88f, spring(dampingRatio = 0.8f))` |
| Reactions strip | long-press `detectTapGestures(onLongPress)` → `Popup` `scaleIn(0.7f) + fadeIn` `tween(180)` |
| Call card insert | `AnimatedVisibility` `fadeIn(tween(250)) + slideInHorizontally { -it / 4 }` |
| Video tile join / leave | join `scaleIn(0.9f) + fadeIn` `tween(300)`; leave `fadeOut(tween(300))` + grid reflow `animateItemPlacement()` |
| Call connect | ringing `fadeOut` → grid `fadeIn` `tween(300)`; control bar `slideInVertically { it } + scaleIn(spring())` |
| Presence change | dot color `animateColorAsState` `tween(200)` |
| Tab switch | instant; icon tint `animateColorAsState` `tween(150)` |

```kotlin
// Canonical outgoing-bubble entrance
AnimatedVisibility(
    visible = true,
    enter = slideInVertically(spring(dampingRatio = 0.8f)) { it / 2 } +
            scaleIn(initialScale = 0.88f, animationSpec = spring(dampingRatio = 0.8f)),
) { SkypeBubble(/* … */) }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for send and reaction dock; `HapticFeedbackConstants.CONFIRM` (via `LocalView`) on call connect; `HapticFeedbackConstants.REJECT` on end. Presence transitions are visual only (no haptic).

## 6. Icons

Skype uses a Microsoft/Segoe glyph set; the closest first-party Compose set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Back | `chevron.left` | `Icons.Filled.ChevronLeft` |
| Voice call | `phone` | `Icons.Filled.Call` |
| Video call | `video` | `Icons.Filled.Videocam` |
| End call | `phone.down.fill` | `Icons.Filled.CallEnd` |
| Mute | `mic.fill` / `mic.slash.fill` | `Icons.Filled.Mic` / `Icons.Filled.MicOff` |
| Share screen | `rectangle.on.rectangle` | `Icons.Filled.ScreenShare` |
| Attachment | `plus` | `Icons.Filled.Add` |
| Emoji | `face.smiling` | `Icons.Filled.Mood` |
| Send | `paperplane.fill` | `Icons.Filled.Send` |
| Voice message | `mic.fill` | `Icons.Filled.Mic` |
| Chats (tab) | `bubble.left.fill` | `Icons.Filled.ChatBubble` |
| Calls (tab) | `phone.fill` | `Icons.Filled.Call` |
| Contacts (tab) | `person.2.fill` | `Icons.Filled.People` |
| Notifications (tab) | `bell.fill` | `Icons.Filled.Notifications` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Meet Now | `video.badge.plus` | `Icons.Filled.VideoCall` |
| Reaction | `heart.fill` | `Icons.Filled.Favorite` |
| Add people | `person.badge.plus` | `Icons.Filled.PersonAdd` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; `Popup` + spring comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: `enableEdgeToEdge()`; white canvas wants dark system-bar content (light-content on the neutral dark canvas, and during calls). Compose bar above the IME — `Modifier.imePadding()` on the thread `LazyColumn`.
- **Font**: Skype's Segoe UI is proprietary cross-platform — bundle Inter (SIL OFL) in `res/font/`; keep ExtraBold/Bold for titles/buttons. `sp` honors the user's font scale.
- **Font scaling**: scale titles/headline/body/previews/call-card text; pin layout-sensitive text (timestamps, 11sp tab labels, badges, presence, per-tile mic/name labels) via a fixed-`fontScale` `LocalDensity`.
- **TalkBack**: label outgoing bubbles "You said {text}, {delivery}"; call cards announce title + subtitle; presence dots use `stateDescription` ("Active") and are reinforced by glyph/ring so they survive color-blindness; expose Reactions via `Modifier.semantics { customActions = … }`; tiles label name + muted state.
- **Touch targets**: Material guidance 48.dp — give the 22.dp header call icons, 22.dp compose icons, and 28.dp reaction glyphs a 48.dp hit box; the 56.dp call-control buttons and list rows are already ≥48.dp.
- **Contrast**: white on `#0078D4` passes WCAG AA; deep-cyan ink `#00343F` on bright `#00AFF0` passes AA (do **not** use white on the bright button); primary text on `#EBEBEF`/`#2A2A30` passes AA.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable bubble spring-in (crossfade), tile join/leave scale (fade only), simplify the call-connect crossfade.
- **Dark mode**: invert via the `Dark*` palette — neutral `#16161A`, NOT color-cast; bright `#00AFF0` / deep `#0078D4` hold; incoming → `#2A2A30`, text → `#F2F2F4`. Shadows fade on dark, so add a 1dp `DarkDivider` border to floating menus (reactions, context) as the elevation cue. Do **not** enable Material You `dynamicColorScheme()` — Skype's cloud-blue identity must hold regardless of wallpaper.
