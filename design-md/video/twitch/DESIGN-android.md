# Twitch (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Twitch's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Twitch's near-black canvas, strict purple-vs-live-red split, live thumbnail cards, docked/overlay chat) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `HazeBlur`/scrim instead of UIBlurEffect, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/TwitchColors.kt
import androidx.compose.ui.graphics.Color

object TwitchColors {
    // Canvas & Surfaces
    val Canvas    = Color(0xFF0E0E10)
    val DeepBlack = Color(0xFF000000)
    val Surface1  = Color(0xFF18181B)
    val Surface2  = Color(0xFF1F1F23)
    val Surface3  = Color(0xFF2A2A2D)
    val Divider   = Color(0xFF2A2A2D)

    // Text
    val TextPrimary   = Color(0xFFEFEFF1)   // intentionally off-white, not pure #FFFFFF
    val TextSecondary = Color(0xFFADADB8)
    val TextTertiary  = Color(0xFF6F6F7B)

    // Brand & Liveness — strict split
    val Purple        = Color(0xFF9146FF)   // brand + primary action
    val PurplePressed = Color(0xFF772CE8)
    val LiveRed       = Color(0xFFEB0400)   // liveness ONLY
    val LiveRedPressed = Color(0xFFC20300)
    val OnlineGreen   = Color(0xFF00C16E)
    val ErrorRed      = Color(0xFFEB0400)
}
```

Wire it into a Material 3 `darkColorScheme` so ripples, dividers, and default components inherit the brand. Twitch is effectively dark-only; do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val TwitchScheme = darkColorScheme(
    primary        = TwitchColors.Purple,
    onPrimary      = Color.White,
    background     = TwitchColors.Canvas,
    onBackground   = TwitchColors.TextPrimary,
    surface        = TwitchColors.Surface1,
    onSurface      = TwitchColors.TextPrimary,
    surfaceVariant = TwitchColors.Surface2,
    outline        = TwitchColors.Divider,
    error          = TwitchColors.ErrorRed,
)

@Composable
fun TwitchTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = TwitchScheme, typography = TwitchTypography, content = content)
```

## 2. Typography

Roobert is a licensed brand typeface. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto) — its grotesque tone is the closest free substitute.

```kotlin
// ui/theme/TwitchType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Roobert = FontFamily(
    Font(R.font.roobert_regular,  FontWeight.Normal),   // 400
    Font(R.font.roobert_semibold, FontWeight.SemiBold), // 600
    Font(R.font.roobert_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object TwitchText {
    val TitleLarge     = TextStyle(Roobert, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val ChannelName    = TextStyle(Roobert, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val SectionHeader  = TextStyle(Roobert, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.3).sp)
    val StreamTitle    = TextStyle(Roobert, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val ChannelLabel   = TextStyle(Roobert, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val Body           = TextStyle(Roobert, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val ChatMessage    = TextStyle(Roobert, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 19.sp)
    val ChatUsername   = TextStyle(Roobert, fontWeight = FontWeight.Bold,     fontSize = 14.sp, lineHeight = 19.sp)
    val Meta           = TextStyle(Roobert, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val CardSubtitle   = TextStyle(Roobert, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val LabelUpper     = TextStyle(Roobert, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Button         = TextStyle(Roobert, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 19.sp, letterSpacing = 0.2.sp)
    val ButtonSecondary = TextStyle(Roobert, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 18.sp)
    val Tab            = TextStyle(Roobert, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val Badge          = TextStyle(Roobert, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val TwitchTypography = Typography(
    headlineLarge = TwitchText.TitleLarge,
    headlineSmall = TwitchText.SectionHeader,
    titleMedium   = TwitchText.StreamTitle,
    bodyMedium    = TwitchText.Body,
    labelSmall    = TwitchText.Tab,
)
```

## 3. Signature Components

### Primary Purple Follow / Subscribe Button

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.*
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

@Composable
fun TwitchFollowButton(
    following: Boolean,
    onToggle: () -> Unit,
    subscribe: Boolean = false,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        if (pressed) 0.97f else 1f,
        spring(dampingRatio = 0.7f, stiffness = 600f), label = "followScale",
    )
    val haptics = LocalHapticFeedback.current
    val label = if (following) (if (subscribe) "Subscribed" else "Following")
                else (if (subscribe) "Subscribe" else "Follow")
    val icon = if (following) Icons.Filled.Check else if (subscribe) Icons.Filled.Star else Icons.Filled.Favorite

    Row(
        modifier = modifier
            .height(40.dp)
            .scale(scale)
            .shadow(if (following) 0.dp else 18.dp, RoundedCornerShape(6.dp),
                    spotColor = TwitchColors.Purple.copy(alpha = 0.35f))
            .clip(RoundedCornerShape(6.dp))
            .background(
                if (following) (if (pressed) TwitchColors.Surface3 else TwitchColors.Surface2)
                else (if (pressed) TwitchColors.PurplePressed else TwitchColors.Purple)
            )
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onToggle()
            }
            .padding(horizontal = 16.dp),
        horizontalArrangement = Arrangement.spacedBy(6.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(icon, contentDescription = null,
            tint = if (following) TwitchColors.Purple else Color.White,
            modifier = Modifier.size(14.dp))
        Text(label,
            style = if (following) TwitchText.ButtonSecondary else TwitchText.Button,
            color = if (following) TwitchColors.TextPrimary else Color.White)
    }
}
```

### LIVE Pill (pulsing) + Viewer-Count Pill

```kotlin
@Composable
fun TwitchLivePill(modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "live")
    val s by t.animateFloat(1f, 0.55f, infiniteRepeatable(tween(800), RepeatMode.Reverse), label = "dot")
    Row(
        modifier
            .height(22.dp)
            .clip(RoundedCornerShape(4.dp))
            .background(TwitchColors.LiveRed)
            .padding(horizontal = 8.dp),
        horizontalArrangement = Arrangement.spacedBy(5.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Box(Modifier.size(6.dp).scale(s).alpha(s).clip(CircleShape).background(Color.White))
        Text("LIVE", style = TwitchText.Badge, color = Color.White)
    }
}

@Composable
fun TwitchViewerPill(count: String, modifier: Modifier = Modifier) {
    Row(
        modifier
            .height(22.dp)
            .clip(RoundedCornerShape(4.dp))
            .background(Color.Black.copy(alpha = 0.6f))
            .padding(horizontal = 8.dp),
        horizontalArrangement = Arrangement.spacedBy(4.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(Icons.Filled.Person, contentDescription = null, tint = Color.White, modifier = Modifier.size(9.dp))
        Text(count, style = TwitchText.Badge, color = Color.White)
    }
}
```

### Live Thumbnail Card

```kotlin
@Composable
fun TwitchLiveCard(
    title: String, channel: String, game: String, viewers: String,
    thumbUrl: String, avatarUrl: String, width: Dp,
    onClick: () -> Unit, modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 1.03f else 1f, label = "cardScale")

    Column(
        modifier
            .width(width)
            .scale(scale)
            .clickable(interaction, indication = null, onClick = onClick),
    ) {
        Box(Modifier.width(width).height(width * 9 / 16).clip(RoundedCornerShape(6.dp))) {
            AsyncImage(model = thumbUrl, contentDescription = title,
                modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
            TwitchLivePill(Modifier.align(Alignment.TopStart).padding(8.dp))
            TwitchViewerPill(viewers, Modifier.align(Alignment.BottomStart).padding(8.dp))
        }
        Spacer(Modifier.height(8.dp))
        Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            AsyncImage(model = avatarUrl, contentDescription = null,
                modifier = Modifier.size(32.dp).clip(CircleShape), contentScale = ContentScale.Crop)
            Column(Modifier.weight(1f)) {
                Text(title, style = TwitchText.StreamTitle, color = TwitchColors.TextPrimary,
                    maxLines = 2, overflow = TextOverflow.Ellipsis)
                Text(channel, style = TwitchText.Meta, color = TwitchColors.TextSecondary)
                Text(game, style = TwitchText.CardSubtitle, color = TwitchColors.TextSecondary)
            }
        }
    }
}
```

### Chat Message Row + Channel Live Ring

```kotlin
@Composable
fun TwitchChatRow(username: String, userColor: Color, message: String, mentioned: Boolean = false) {
    Row(
        Modifier
            .fillMaxWidth()
            .then(if (mentioned)
                Modifier.background(TwitchColors.Purple.copy(alpha = 0.20f))
                    .drawBehind { drawRect(TwitchColors.Purple, size = size.copy(width = 2.dp.toPx())) }
                else Modifier)
            .padding(horizontal = 12.dp, vertical = 4.dp),
    ) {
        Text(
            buildAnnotatedString {
                withStyle(SpanStyle(color = userColor, fontWeight = FontWeight.Bold)) { append("$username ") }
                withStyle(SpanStyle(color = TwitchColors.TextPrimary)) { append(message) }
            },
            style = TwitchText.ChatMessage,
        )
    }
}

@Composable
fun TwitchAvatarRing(url: String, live: Boolean, size: Dp = 44.dp) {
    Box(
        Modifier
            .border(2.dp, if (live) TwitchColors.LiveRed else TwitchColors.Purple, CircleShape)
            .padding(2.dp),
    ) {
        AsyncImage(model = url, contentDescription = null,
            modifier = Modifier.size(size).clip(CircleShape), contentScale = ContentScale.Crop)
    }
}
```

### Theater-Mode Chat Overlay

```kotlin
@Composable
fun TwitchTheaterChatOverlay(messages: List<ChatLine>, hidden: Boolean) {
    AnimatedVisibility(
        visible = !hidden,
        enter = slideInHorizontally { it } + fadeIn(),
        exit = slideOutHorizontally { it } + fadeOut(),
        modifier = Modifier.fillMaxHeight().width(320.dp),
    ) {
        Column(Modifier.fillMaxSize().background(TwitchColors.Canvas.copy(alpha = 0.72f))) {
            // Android has no first-class live blur; the 72%-opaque scrim approximates iOS .ultraThinMaterial.
            LazyColumn(Modifier.weight(1f)) {
                items(messages) { m -> TwitchChatRow(m.user, m.color, m.text, m.mentionsMe) }
            }
            BasicTextField(
                value = "", onValueChange = {},
                modifier = Modifier.padding(12.dp).fillMaxWidth()
                    .clip(RoundedCornerShape(8.dp)).background(TwitchColors.Surface2).padding(12.dp),
            )
        }
    }
}
```

## 4. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Twitch's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 94%-opaque canvas surface. **Active tint is Twitch Purple** — purple is the indicator.

```kotlin
@Composable
fun TwitchBottomBar(selected: Int, unreadNotifs: Boolean, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = TwitchColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Following"     to Icons.Filled.Favorite,
            "Browse"        to Icons.Filled.VideoLibrary,
            "Search"        to Icons.Filled.Search,
            "Notifications" to Icons.Filled.Notifications,
            "Profile"       to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (i == 3 && unreadNotifs) {
                        BadgedBox(badge = { Badge(containerColor = TwitchColors.LiveRed) }) {
                            Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp))
                        }
                    } else {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp))
                    }
                },
                label = { Text(label, style = TwitchText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = TwitchColors.Purple,   // purple is the indicator
                    selectedTextColor   = TwitchColors.Purple,
                    unselectedIconColor = TwitchColors.TextSecondary,
                    unselectedTextColor = TwitchColors.TextSecondary,
                    indicatorColor      = Color.Transparent,
                ),
            )
        }
    }
}
```

## 5. Motion

| Moment | Compose recipe |
|--------|----------------|
| LIVE pill pulse | `rememberInfiniteTransition` scale+alpha 1 → 0.55, `tween(800)` `RepeatMode.Reverse` |
| Card press scale-up | `animateFloatAsState` 1 → 1.03, `tween(180)` ease-out + focus shadow + purple ring |
| Follow tap | `animateFloatAsState` 1 → 0.97 with `spring(dampingRatio = 0.7f)`, `HapticFeedbackType.LongPress` |
| Chat autoscroll | new items `animateItemPlacement()`; `LazyColumn` `animateScrollToItem(last)` unless user scrolled up |
| Viewer count | `AnimatedContent` with `slideInVertically`/`slideOutVertically` (odometer feel) |
| Theater toggle | `AnimatedVisibility` + `slideInHorizontally { it }` + `fadeIn()` on the chat overlay |

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(8, ...)` to approximate iOS's `.light` impact.

## 6. Icons

Twitch ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Twitch's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Follow | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Subscribe | `star` / `star.fill` | `Icons.Filled.Star` |
| Followed check | `checkmark` | `Icons.Filled.Check` |
| Viewers | `person.fill` | `Icons.Filled.Person` |
| Play / Pause | `play.fill` / `pause.fill` | `Icons.Filled.PlayArrow` / `Icons.Filled.Pause` |
| Emote picker | `face.smiling` | `Icons.Filled.EmojiEmotions` |
| Send | `paperplane.fill` | `Icons.Filled.Send` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Following (tab) | `heart.fill` | `Icons.Filled.Favorite` |
| Browse (tab) | `play.square.stack.fill` | `Icons.Filled.VideoLibrary` |
| Notifications (tab) | `bell.fill` | `Icons.Filled.Notifications` |
| Profile (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the near-black canvas wants `WindowCompat` light-content system bars. Apply `Scaffold` insets so the tab bar and chat input clear gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on stream titles, channel names, chat. Pin the LIVE pill, viewer pills, and tab labels via `dp`-derived sizes; cap chat growth (`fontScale` clamp) to keep throughput readable.
- **TalkBack**: announce live cards ("Live: Ranked grind by Channel, Just Chatting, 12.4K viewers"); wrap the chat list in `Modifier.semantics { liveRegion = LiveRegionMode.Polite }` so new messages are narrated (offer a mute).
- **Touch targets**: Material guidance is 48.dp minimum. The 40.dp Follow CTA is borderline — add vertical padding to reach 48.dp hit; 64.dp channel rows clear it; pad chat send/emote icons to 48.dp.
- **Color split & color-blindness**: never rely on purple-vs-red hue alone — the LIVE pill always carries the word "LIVE" + dot, the viewer pill carries a person glyph.
- **Contrast**: `#ADADB8` on `#0E0E10` passes WCAG AA at 13sp+. `#EFEFF1` is intentionally off-white and still exceeds AAA. White on `#9146FF` passes AA; white on `#EB0400` passes AA at 11sp bold.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Twitch's brand requires the fixed `#0E0E10` canvas and the strict purple/live-red split regardless of wallpaper.
