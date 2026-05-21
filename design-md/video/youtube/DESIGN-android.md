# YouTube (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports YouTube's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the 16:9 video card, the draggable mini-player, the Shorts rail, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral — and YouTube is itself a Google product whose Material roots already show. This file keeps the *visual* identity (the 16:9 hard-edge thumbnail as the atomic unit, single-red brand accent reserved for Subscribe + brand marks, the near-black `#0F0F0F` dark canvas, the persistent draggable mini-player) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `AnchoredDraggable` for the mini-player, a translucent `Surface` (no live blur), `sp`/`dp` instead of `pt`. Light-first with a near-black dark mode; both are wired below.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for thumbnails, channel avatars, and Shorts audio discs. No color extraction — YouTube's palette is fixed brand chrome.

## 1. Color Tokens

```kotlin
// ui/theme/YouTubeColors.kt
import androidx.compose.ui.graphics.Color

object YouTubeColors {
    // Brand
    val Red        = Color(0xFFFF0000) // Subscribe + brand marks ONLY
    val RedPressed = Color(0xFFCC0000)
    val RedHover   = Color(0xFFE60000)

    // Light canvas
    val CanvasLight   = Color(0xFFFFFFFF)
    val Surface1Light = Color(0xFFF9F9F9)
    val Surface2Light = Color(0xFFF2F2F2)
    val DividerLight  = Color(0xFFE5E5E5)

    // Dark canvas
    val CanvasDark   = Color(0xFF0F0F0F) // signature near-black, NOT pure black
    val Surface1Dark = Color(0xFF1F1F1F)
    val Surface2Dark = Color(0xFF272727)
    val Surface3Dark = Color(0xFF3F3F3F)
    val DividerDark  = Color(0xFF303030)

    // Text (light)
    val TextPrimaryLight   = Color(0xFF0F0F0F)
    val TextSecondaryLight = Color(0xFF606060)
    val TextTertiaryLight  = Color(0xFF909090)
    val TextDisabledLight  = Color(0xFFC0C0C0)

    // Text (dark)
    val TextPrimaryDark   = Color(0xFFFFFFFF)
    val TextSecondaryDark = Color(0xFFAAAAAA)
    val TextTertiaryDark  = Color(0xFF717171)

    // Semantic
    val InfoBlue   = Color(0xFF3EA6FF) // links, "Show more", send-arrow active
    val WarningYellow = Color(0xFFFFC400)
    val Scrim      = Color(0xBF000000) // duration tag bg ~rgba(0,0,0,0.75)
    val ShortsTrack = Color(0x4DFFFFFF) // rgba(255,255,255,0.3)
}
```

YouTube is light-first with a near-black dark mode. Provide both schemes; `primary` carries the brand red (used by the Subscribe button only — keep it off generic components). The **active tab tint is achromatic** (`#0F0F0F`/`#FFFFFF`), never red.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val YouTubeLight = lightColorScheme(
    primary        = YouTubeColors.Red,
    onPrimary      = Color.White,
    background     = YouTubeColors.CanvasLight,
    onBackground   = YouTubeColors.TextPrimaryLight,
    surface        = YouTubeColors.CanvasLight,
    onSurface      = YouTubeColors.TextPrimaryLight,
    surfaceVariant = YouTubeColors.Surface2Light,
    outline        = YouTubeColors.DividerLight,
)

private val YouTubeDark = darkColorScheme(
    primary        = YouTubeColors.Red,
    onPrimary      = Color.White,
    background     = YouTubeColors.CanvasDark,
    onBackground   = YouTubeColors.TextPrimaryDark,
    surface        = YouTubeColors.CanvasDark,
    onSurface      = YouTubeColors.TextPrimaryDark,
    surfaceVariant = YouTubeColors.Surface2Dark,
    outline        = YouTubeColors.DividerDark,
)

@Composable
fun YouTubeTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (darkTheme) YouTubeDark else YouTubeLight,
    typography = YouTubeTypography,
    content = content,
)
```

## 2. Typography

YouTube pairs **YouTube Sans** (proprietary display, 2017) with **Roboto** (body). Roboto *is* the Android system font (`FontFamily.Default`) — use it directly for body. Drop YouTube Sans TTFs in `res/font/` for display moments; fall back to Roboto if absent. Keep the two families in their lanes.

```kotlin
// ui/theme/YouTubeType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val YouTubeSans = FontFamily(
    Font(R.font.youtube_sans_medium, FontWeight.Medium),
    Font(R.font.youtube_sans_bold,   FontWeight.Bold),
)
val Roboto = FontFamily.Default // Roboto is Android's system face — exact body match

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object YouTubeText {
    val ScreenTitle      = TextStyle(YouTubeSans, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.1).sp)
    val VideoDetailTitle = TextStyle(Roboto,      fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val VideoTitle       = TextStyle(Roboto,      fontWeight = FontWeight.Medium,   fontSize = 16.sp, lineHeight = 21.sp) // most common moment, 2-line clamp
    val ChannelName      = TextStyle(Roboto,      fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 18.sp)
    val Metadata         = TextStyle(Roboto,      fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val CommentBody      = TextStyle(Roboto,      fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val CommentAuthor    = TextStyle(Roboto,      fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 17.sp)
    val Body             = TextStyle(Roboto,      fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val Button           = TextStyle(Roboto,      fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 14.sp)
    val Chip             = TextStyle(Roboto,      fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 14.sp)
    val TabLabel         = TextStyle(Roboto,      fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val DurationTag      = TextStyle(Roboto,      fontWeight = FontWeight.Medium,   fontSize = 11.sp, lineHeight = 11.sp)
    val Timestamp        = TextStyle(Roboto,      fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 12.sp)
    val LikeCount        = TextStyle(Roboto,      fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 13.sp)
    val ShortsCaption    = TextStyle(YouTubeSans, fontWeight = FontWeight.Medium,   fontSize = 15.sp, lineHeight = 20.sp)
}

// Map onto Material 3 slots so stock components inherit the ramp
val YouTubeTypography = Typography(
    headlineLarge = YouTubeText.ScreenTitle,
    titleLarge    = YouTubeText.VideoDetailTitle,
    titleMedium   = YouTubeText.VideoTitle,
    bodyMedium    = YouTubeText.Body,
    labelSmall    = YouTubeText.TabLabel,
)
```

## 3. Signature Components

### Subscribe Button (the iconic red → gray morph)

YouTube's signature button shape is an **18dp rounded rectangle** — never a full pill. On subscribe, the red morphs to gray and a bell appears, with a light haptic.

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.animateColorAsState
import androidx.compose.animation.core.spring
import androidx.compose.animation.fadeIn
import androidx.compose.animation.fadeOut
import androidx.compose.animation.slideInHorizontally
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Notifications
import androidx.compose.material.icons.outlined.NotificationsNone
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun SubscribeButton(
    subscribed: Boolean,
    onToggle: () -> Unit,
    modifier: Modifier = Modifier,
    darkTheme: Boolean = false,
) {
    val haptics = LocalHapticFeedback.current
    val subscribedBg = if (darkTheme) YouTubeColors.Surface2Dark else YouTubeColors.Surface2Light
    val subscribedFg = if (darkTheme) YouTubeColors.TextPrimaryDark else YouTubeColors.TextPrimaryLight
    val bg by animateColorAsState(
        targetValue = if (subscribed) subscribedBg else YouTubeColors.Red,
        animationSpec = spring(dampingRatio = 0.8f, stiffness = 700f), // ~200ms morph
        label = "subscribeBg",
    )
    var bellOn by remember { mutableStateOf(false) }

    Row(modifier, verticalAlignment = Alignment.CenterVertically) {
        Box(
            Modifier
                .clip(RoundedCornerShape(18.dp)) // signature rounded-rect, NOT full pill
                .background(bg)
                .clickable {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress) // light/success
                    onToggle()
                }
                .padding(horizontal = 16.dp, vertical = 8.dp),
            contentAlignment = Alignment.Center,
        ) {
            Text(
                if (subscribed) "Subscribed" else "Subscribe",
                style = YouTubeText.Button,
                color = if (subscribed) subscribedFg else Color.White,
            )
        }
        AnimatedVisibility(
            visible = subscribed,
            enter = slideInHorizontally { it } + fadeIn(),
            exit = fadeOut(),
        ) {
            Box(
                Modifier
                    .padding(start = 4.dp)
                    .clip(RoundedCornerShape(18.dp))
                    .background(subscribedBg)
                    .clickable { bellOn = !bellOn; haptics.performHapticFeedback(HapticFeedbackType.LongPress) }
                    .padding(horizontal = 12.dp, vertical = 8.dp),
            ) {
                Icon(
                    if (bellOn) Icons.Filled.Notifications else Icons.Outlined.NotificationsNone,
                    contentDescription = if (bellOn) "Notifications on" else "Notifications off",
                    tint = subscribedFg,
                    modifier = Modifier.size(16.dp),
                )
            }
        }
    }
}
```

### Video Card (16:9 hard-edge thumbnail — the atomic unit)

The thumbnail bleeds edge-to-edge with **no corner radius** — the hard edge is load-bearing. The duration tag sits absolute bottom-right; a live video shows a red LIVE pill top-left instead.

```kotlin
import androidx.compose.foundation.layout.aspectRatio
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.MoreVert
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import coil.compose.AsyncImage

@Composable
fun VideoCard(
    thumbnailUrl: String,
    duration: String,        // "12:34"
    isLive: Boolean,
    title: String,
    channelAvatarUrl: String,
    channelName: String,
    viewCount: String,       // "1.2M views"
    uploadedAgo: String,     // "3 days ago"
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Column(modifier.fillMaxWidth().clickable(onClick = onClick)) {
        Box {
            AsyncImage(
                model = thumbnailUrl,
                contentDescription = title,
                contentScale = ContentScale.Crop,
                modifier = Modifier
                    .fillMaxWidth()
                    .aspectRatio(16f / 9f), // NO clip — hard edges are the signature
            )
            if (isLive) {
                Row(
                    Modifier
                        .padding(6.dp)
                        .clip(RoundedCornerShape(4.dp))
                        .background(YouTubeColors.Red)
                        .padding(horizontal = 6.dp, vertical = 4.dp)
                        .align(Alignment.TopStart),
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(4.dp),
                ) {
                    Box(Modifier.size(6.dp).clip(CircleShape).background(Color.White))
                    Text("LIVE", style = YouTubeText.DurationTag.copy(fontWeight = FontWeight.Bold), color = Color.White)
                }
            } else {
                Text(
                    duration,
                    style = YouTubeText.DurationTag,
                    color = Color.White,
                    modifier = Modifier
                        .padding(6.dp)
                        .align(Alignment.BottomEnd)
                        .clip(RoundedCornerShape(4.dp))
                        .background(YouTubeColors.Scrim)
                        .padding(horizontal = 6.dp, vertical = 4.dp),
                )
            }
        }
        Row(
            Modifier.fillMaxWidth().padding(start = 16.dp, end = 8.dp, top = 12.dp, bottom = 16.dp),
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            AsyncImage(
                model = channelAvatarUrl,
                contentDescription = null,
                modifier = Modifier.size(28.dp).clip(CircleShape),
                contentScale = ContentScale.Crop,
            )
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(title, style = YouTubeText.VideoTitle, maxLines = 2, overflow = TextOverflow.Ellipsis)
                Text(
                    "$channelName · $viewCount · $uploadedAgo",
                    style = YouTubeText.Metadata,
                    color = YouTubeColors.TextSecondaryLight,
                    maxLines = 1,
                    overflow = TextOverflow.Ellipsis,
                )
            }
            Icon(Icons.Filled.MoreVert, contentDescription = "More", modifier = Modifier.size(20.dp))
        }
    }
}
```

### Action Pill (Like / Dislike / Share — 18dp rounded rect)

```kotlin
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun ActionPill(
    icon: ImageVector,
    label: String?, // null for Dislike (count hidden per 2021 decision)
    active: Boolean,
    onClick: () -> Unit,
    darkTheme: Boolean = false,
) {
    val bg = if (darkTheme) YouTubeColors.Surface2Dark else YouTubeColors.Surface2Light
    val fg = if (darkTheme) YouTubeColors.TextPrimaryDark else YouTubeColors.TextPrimaryLight
    Row(
        Modifier
            .clip(RoundedCornerShape(18.dp))
            .background(bg)
            .clickable(onClick = onClick)
            .padding(horizontal = 12.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(icon, contentDescription = null, tint = fg, modifier = Modifier.size(20.dp))
        if (label != null) Text(label, style = YouTubeText.LikeCount, color = fg)
    }
}
```

### Filter Chip Row (full color inversion when selected)

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.items

@Composable
fun FilterChipRow(
    chips: List<String>,
    selected: String,
    onSelect: (String) -> Unit,
    darkTheme: Boolean = false,
) {
    val defaultBg = if (darkTheme) YouTubeColors.Surface2Dark else YouTubeColors.Surface2Light
    val defaultFg = if (darkTheme) YouTubeColors.TextPrimaryDark else YouTubeColors.TextPrimaryLight
    val activeBg  = if (darkTheme) YouTubeColors.TextPrimaryDark else YouTubeColors.TextPrimaryLight
    val activeFg  = if (darkTheme) YouTubeColors.CanvasDark else YouTubeColors.CanvasLight

    LazyRow(
        contentPadding = PaddingValues(horizontal = 12.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        items(chips) { chip ->
            val isSel = chip == selected
            Box(
                Modifier
                    .clip(RoundedCornerShape(percent = 50)) // full pill — chips only
                    .background(if (isSel) activeBg else defaultBg)
                    .clickable { onSelect(chip) }
                    .padding(horizontal = 12.dp, vertical = 8.dp),
            ) {
                Text(chip, style = YouTubeText.Chip, color = if (isSel) activeFg else defaultFg)
            }
        }
    }
}
```

## 4. 16:9 Thumbnails · Mini-Player Drag-Down · Shorts Rail

YouTube's three most distinctive systems. The **16:9 thumbnail** (above) is the atomic unit. The **mini-player** is YouTube's signature navigation pattern — the full player collapses into a persistent 72dp bar that survives navigation; drag it down past a threshold to dismiss. The **Shorts rail** is a right-edge action stack over a full-bleed 9:16 video.

```kotlin
import androidx.compose.foundation.gestures.AnchoredDraggableState
import androidx.compose.foundation.gestures.DraggableAnchors
import androidx.compose.foundation.gestures.Orientation
import androidx.compose.foundation.gestures.anchoredDraggable
import androidx.compose.material.icons.filled.Close
import androidx.compose.material.icons.filled.Pause
import androidx.compose.material.icons.filled.PlayArrow
import kotlin.math.roundToInt
import androidx.compose.ui.unit.IntOffset

enum class PlayerState { Full, Mini, Dismissed }

/**
 * Mini-player: a 72dp bar above the bottom bar that persists across navigation.
 * Drag up → expand to full player (use SharedTransitionLayout on the thumbnail).
 * Drag down past threshold → dismiss (video stops).
 */
@OptIn(ExperimentalFoundationApi::class)
@Composable
fun MiniPlayer(
    thumbnailUrl: String,
    title: String,
    channelName: String,
    isPlaying: Boolean,
    onPlayPause: () -> Unit,
    onExpand: () -> Unit,
    onDismiss: () -> Unit,
    dragState: AnchoredDraggableState<PlayerState>,
    modifier: Modifier = Modifier,
    darkTheme: Boolean = false,
) {
    val bg = if (darkTheme) YouTubeColors.Surface1Dark else YouTubeColors.CanvasLight
    val fg = if (darkTheme) YouTubeColors.TextPrimaryDark else YouTubeColors.TextPrimaryLight

    Row(
        modifier = modifier
            .offset { IntOffset(0, dragState.requireOffset().roundToInt()) }
            .anchoredDraggable(dragState, Orientation.Vertical)
            .fillMaxWidth()
            .height(72.dp)
            .background(bg)
            .clickable(onClick = onExpand),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        AsyncImage(
            model = thumbnailUrl,
            contentDescription = null,
            contentScale = ContentScale.Crop,
            modifier = Modifier.width(128.dp).height(72.dp), // 16:9, hard edges
        )
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(title, style = YouTubeText.ChannelName, color = fg, maxLines = 1, overflow = TextOverflow.Ellipsis)
            Text(channelName, style = YouTubeText.Timestamp, color = YouTubeColors.TextSecondaryLight, maxLines = 1)
        }
        Icon(
            if (isPlaying) Icons.Filled.Pause else Icons.Filled.PlayArrow,
            contentDescription = if (isPlaying) "Pause" else "Play",
            tint = fg,
            modifier = Modifier.size(20.dp).clickable(onClick = onPlayPause),
        )
        Icon(
            Icons.Filled.Close,
            contentDescription = "Close",
            tint = fg,
            modifier = Modifier.padding(end = 8.dp).size(20.dp).clickable(onClick = onDismiss),
        )
    }
}
```

### Shorts Right-Rail (over full-bleed 9:16 video)

```kotlin
import androidx.compose.material.icons.filled.AddCircle
import androidx.compose.material.icons.filled.ThumbDown
import androidx.compose.material.icons.filled.ThumbUp
import androidx.compose.material.icons.outlined.Comment
import androidx.compose.material.icons.outlined.Reply
import androidx.compose.material.icons.outlined.ThumbDownOffAlt
import androidx.compose.material.icons.outlined.ThumbUpOffAlt
import androidx.compose.foundation.border
import androidx.compose.ui.draw.shadow

@Composable
fun ShortsActionRail(
    creatorAvatarUrl: String,
    likeCount: String,
    commentCount: String,
    liked: Boolean,
    onLike: () -> Unit,
    modifier: Modifier = Modifier,
) {
    // White glyphs with a soft shadow for legibility over arbitrary video
    @Composable
    fun RailIcon(icon: ImageVector, label: String?, cd: String, onClick: () -> Unit) {
        Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Icon(
                icon, contentDescription = cd, tint = Color.White,
                modifier = Modifier
                    .size(32.dp)
                    .shadow(2.dp, CircleShape, ambientColor = Color.Black.copy(alpha = 0.4f))
                    .clickable(onClick = onClick),
            )
            if (label != null) Text(label, style = YouTubeText.LikeCount, color = Color.White)
        }
    }

    Column(
        modifier = modifier,
        verticalArrangement = Arrangement.spacedBy(20.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Box(contentAlignment = Alignment.BottomEnd) {
            AsyncImage(
                model = creatorAvatarUrl,
                contentDescription = "Channel",
                contentScale = ContentScale.Crop,
                modifier = Modifier.size(44.dp).clip(CircleShape).border(2.dp, Color.White, CircleShape),
            )
            Icon( // Subscribe shows as a red "+" badge on the avatar
                Icons.Filled.AddCircle,
                contentDescription = "Subscribe",
                tint = YouTubeColors.Red,
                modifier = Modifier.size(20.dp).offset(x = 4.dp, y = 4.dp),
            )
        }
        RailIcon(if (liked) Icons.Filled.ThumbUp else Icons.Outlined.ThumbUpOffAlt, likeCount, "Like", onLike)
        RailIcon(Icons.Outlined.ThumbDownOffAlt, null, "Dislike") {} // count hidden
        RailIcon(Icons.Outlined.Comment, commentCount, "Comments") {}
        RailIcon(Icons.Outlined.Reply, null, "Share") {}
    }
}
```

The Shorts progress scrubber at the bottom is **white, not red** (kept minimal): a 2dp track of `YouTubeColors.ShortsTrack` with a `Color.White` fill — never the brand red on Shorts.

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar` with the five canonical tabs (Home, Shorts, Create, Subscriptions, You). YouTube uses a 1dp top hairline rather than a shadow; Android has no live blur, so use an opaque canvas surface with a top divider. **Active tint is achromatic** (`#0F0F0F`/`#FFFFFF`) — never red. The Create tab opens a modal sheet ("Create a Short", "Upload video", "Go live").

```kotlin
import androidx.compose.material.icons.filled.AddCircleOutline
import androidx.compose.material.icons.filled.Home
import androidx.compose.material.icons.filled.PlayCircleFilled
import androidx.compose.material.icons.filled.Subscriptions
import androidx.compose.material.icons.outlined.HomeOutlined
import androidx.compose.material.icons.outlined.PlayCircleOutline
import androidx.compose.material.icons.outlined.SubscriptionsOutlined
import androidx.compose.material.icons.outlined.AccountCircle
import androidx.compose.material3.HorizontalDivider
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun YouTubeBottomBar(selected: Int, onSelect: (Int) -> Unit, darkTheme: Boolean = false) {
    val container = if (darkTheme) YouTubeColors.CanvasDark else YouTubeColors.CanvasLight
    val divider   = if (darkTheme) YouTubeColors.DividerDark else YouTubeColors.DividerLight
    val active    = if (darkTheme) YouTubeColors.TextPrimaryDark else YouTubeColors.TextPrimaryLight
    val inactive  = if (darkTheme) YouTubeColors.TextSecondaryDark else YouTubeColors.TextSecondaryLight

    Column {
        HorizontalDivider(thickness = 1.dp, color = divider) // hairline, not a shadow
        NavigationBar(containerColor = container, tonalElevation = 0.dp) {
            data class Tab(val on: ImageVector, val off: ImageVector, val label: String)
            val tabs = listOf(
                Tab(Icons.Filled.Home, Icons.Outlined.HomeOutlined, "Home"),
                Tab(Icons.Filled.PlayCircleFilled, Icons.Outlined.PlayCircleOutline, "Shorts"),
                Tab(Icons.Filled.AddCircleOutline, Icons.Filled.AddCircleOutline, "Create"),
                Tab(Icons.Filled.Subscriptions, Icons.Outlined.SubscriptionsOutlined, "Subscriptions"),
                Tab(Icons.Outlined.AccountCircle, Icons.Outlined.AccountCircle, "You"),
            )
            tabs.forEachIndexed { i, tab ->
                val isSel = selected == i
                NavigationBarItem(
                    selected = isSel,
                    onClick = { onSelect(i) },
                    icon = { Icon(if (isSel) tab.on else tab.off, contentDescription = tab.label, modifier = Modifier.size(24.dp)) },
                    label = { Text(tab.label, style = YouTubeText.TabLabel) },
                    alwaysShowLabel = true,
                    colors = NavigationBarItemDefaults.colors(
                        selectedIconColor   = active, // achromatic, NOT red
                        selectedTextColor   = active,
                        unselectedIconColor = inactive,
                        unselectedTextColor = inactive,
                        indicatorColor      = Color.Transparent, // no Material pill — YouTube has none
                    ),
                )
            }
        }
    }
}
```

The **mini-player** renders in the `Scaffold` `bottomBar` slot stacked directly above `YouTubeBottomBar` while playback is active.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Subscribe morph | `animateColorAsState` red ↔ gray with `spring(dampingRatio = 0.8f)` (~200ms); bell `AnimatedVisibility` slide+fade; `HapticFeedbackType.LongPress` |
| Mini-player ↔ full | `SharedTransitionLayout` + `Modifier.sharedElement()` on the 16:9 thumbnail, `spring(dampingRatio = 0.8f, stiffness = 250f)` |
| Mini-player dismiss | `AnchoredDraggable` past the dismiss anchor → `PlayerState.Dismissed` |
| Like tap | `Animatable` scale 1 → 1.15 → 1 over 300ms; `HapticFeedbackType.TextHandleMove` |
| Scrubber drag | thumb scale 1 → 1.3 while dragging; `HapticFeedbackType.SegmentTick` every 10% |
| Shorts swipe | `VerticalPager` 60fps, `spring(dampingRatio = 0.85f)` |
| Double-tap seek | tap-zone `pointerInput`; ±10s with an expanding arc-ripple overlay + "−10"/"+10" label |
| Filter chip select | `animateColorAsState` 200ms inversion; `HapticFeedbackType.SegmentTick` |

```kotlin
// Like bounce — thumbs-up scales 1 → 1.15 → 1 with a light haptic
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween
import androidx.compose.ui.draw.scale

@Composable
fun LikeButton(initiallyLiked: Boolean = false) {
    var liked by remember { mutableStateOf(initiallyLiked) }
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(liked) {
        if (liked) {
            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS .impact(.light)
            scale.animateTo(1.15f, spring(dampingRatio = 0.6f, stiffness = 600f))
            scale.animateTo(1f, tween(150))
        }
    }
    Icon(
        imageVector = if (liked) Icons.Filled.ThumbUp else Icons.Outlined.ThumbUpOffAlt,
        contentDescription = "Like",
        modifier = Modifier.size(20.dp).scale(scale.value).clickable { liked = !liked },
    )
}
```

Haptics: prefer `LocalHapticFeedback`. `HapticFeedbackType.SegmentTick` ≈ iOS `.selection` for filter chips and scrubber ticks; `HapticFeedbackType.LongPress` ≈ `.impact(.light)`/`.success` for Subscribe. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+) on the Subscribe success, or a `Vibrator` `VibrationEffect.createOneShot(10, 70)`.

## 7. Icons

YouTube ships custom glyphs (the logomark, the Shorts/Subscriptions marks); the closest first-party set is `androidx.compose.material:material-icons-extended`. The YouTube logomark (red play-button square) and the bespoke Shorts glyph should ship as vector drawables loaded via `ImageVector.vectorResource(R.drawable.…)` — the logomark's red is brand, never a UI accent.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Play / Pause (player) | `play.fill` / `pause.fill` | `Icons.Filled.PlayArrow` / `Icons.Filled.Pause` |
| Next / Previous | `forward.end.fill` / `backward.end.fill` | `Icons.Filled.SkipNext` / `Icons.Filled.SkipPrevious` |
| Like | `hand.thumbsup` / `.fill` | `Icons.Outlined.ThumbUpOffAlt` / `Icons.Filled.ThumbUp` |
| Dislike | `hand.thumbsdown` | `Icons.Outlined.ThumbDownOffAlt` |
| Share | `arrowshape.turn.up.right` | `Icons.Outlined.Reply` (mirrored) / `Icons.Filled.Share` |
| Download | `arrow.down.circle` | `Icons.Outlined.FileDownload` |
| Save (playlist) | `plus.rectangle.on.folder` | `Icons.Outlined.PlaylistAdd` |
| Comment | `bubble.right` | `Icons.Outlined.Comment` |
| Subscribe bell | `bell` / `bell.fill` | `Icons.Outlined.NotificationsNone` / `Icons.Filled.Notifications` |
| Cast | `airplayvideo` | `Icons.Filled.Cast` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house` / `.fill` | `Icons.Outlined.HomeOutlined` / `Icons.Filled.Home` |
| Shorts (tab) | `play.rectangle` / `.fill` | `Icons.Outlined.PlayCircleOutline` (or vector drawable) |
| Create (tab) | `plus.circle.fill` | `Icons.Filled.AddCircleOutline` |
| Subscriptions (tab) | `play.square.stack` | `Icons.Filled.Subscriptions` |
| You (tab) | `person.crop.circle` | `Icons.Outlined.AccountCircle` |
| More | `ellipsis` | `Icons.Filled.MoreVert` |
| Close | `xmark` | `Icons.Filled.Close` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `AnchoredDraggable`, `SharedTransitionLayout` (Compose 1.7+), and `VerticalPager` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; system-bar icon color follows the theme. Let Shorts video extend under the system bars while keeping overlays inside `WindowInsets.systemBars`; apply `Scaffold` insets so the mini-player + tab bar clear the gesture nav. Video detail should support landscape fullscreen (let the player ignore insets, controls respect them).
- **Font scaling**: `sp` honors the user's font scale — keep it on video titles, descriptions, and comments. **Pin** the duration tag (11sp), Subscribe button text (14sp), tab labels (10sp), and Shorts captions by deriving them from a fixed-`fontScale` `LocalDensity` — these are layout-critical (the tag overlays the thumbnail corner; the chip/tab rows must not wrap).
- **TalkBack**: merge each video card into one node with `Modifier.semantics(mergeDescendants = true)` and a readable label (`"<title>, <channel>, <views>, <age>"`); expose Subscribe and the overflow as separate buttons. Shorts rail icons are independent buttons with state-aware descriptions.
- **Touch targets**: Material guidance is 48.dp minimum. The Subscribe pill is 36dp tall — expand the hit area to 48dp via padding. The 20dp overflow and 20dp mini-player controls need a 44–48dp hit area; Shorts 32dp glyphs need a 48dp hit area.
- **Reduce Motion**: respect the system "remove animations" setting — fall back the Subscribe morph to an instant cross-fade, skip the double-tap-seek ripple and the like bounce, and make the mini-player transition a snap.
- **Contrast**: `#606060` on `#FFFFFF` meets WCAG AA at 13sp+; `#AAAAAA` on `#0F0F0F` passes. Validate the lowest-emphasis tertiary (`#909090`/`#717171`) at the 11–12sp tag/timestamp sizes; Shorts captions over arbitrary video must keep the `rgba(0,0,0,0.5)` text shadow for legibility regardless of frame content.
- **Dynamic color**: YouTube is a Google product, but its brand red and the achromatic chrome are fixed — do **not** enable Material You `dynamicColorScheme()`; the Subscribe-red and near-black canvas must hold regardless of wallpaper. Keep the hand-built light/dark schemes above. Honor the device mute switch on Shorts autoplay and surface an audio-state indicator.
