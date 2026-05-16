# TikTok (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports TikTok's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the true-black canvas, chromatic-aberration logo/Create button, rose-as-the-verb, the full-bleed 9:16 feed with a floating right-side action rail) while making everything idiomatic Android — a custom bottom bar instead of a UITabBar, an `AccelerateDecelerate` `VerticalPager` instead of a paged `TabView`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars/artwork, and `androidx.media3:media3-exoplayer` for the video feed (wrap a `PlayerView` in `AndroidView`). No color extraction — TikTok is monochromatically cyan + rose on black, so `androidx.palette` is not required.

## 1. Color Tokens

```kotlin
// ui/theme/TikTokColors.kt
import androidx.compose.ui.graphics.Color

object TikTokColors {
    // Canvas & Surfaces
    val Canvas     = Color(0xFF010101) // true black — a hair warmer than #000000
    val Surface    = Color(0xFF161823) // DMs, sheets
    val InputField = Color(0xFF2F2F2F) // comment compose, search

    // Brand
    val Rose = Color(0xFFFE2C55) // the verb — Like, Follow, scrubber fill
    val Cyan = Color(0xFF25F4EE) // the accent — Create button, chromatic aberration

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFE5E5E5)               // hashtag counts
    val TextTertiary  = Color(0xFFFFFFFF).copy(alpha = 0.6f) // placeholder, meta
    val TextDisabled  = Color(0xFFFFFFFF).copy(alpha = 0.3f)

    // Overlays
    val FollowerGray  = Color(0xFFFFFFFF).copy(alpha = 0.15f) // "Following" pill bg
    val ScrimLight    = Color(0xFF000000).copy(alpha = 0.25f) // icon shadow
    val ScrimMedium   = Color(0xFF000000).copy(alpha = 0.4f)  // text-on-video shadow
    val ScrimHeavy    = Color(0xFF000000).copy(alpha = 0.6f)  // sheet dim
    val ScrubberTrack = Color(0xFFFFFFFF).copy(alpha = 0.3f)
}
```

TikTok has **no light mode** — every screen is dark. Supply only a Material 3 `darkColorScheme`. `primary` is rose (the verb); `secondary` is cyan (the Create accent).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val TikTokScheme = darkColorScheme(
    primary        = TikTokColors.Rose,
    onPrimary      = TikTokColors.TextPrimary,
    secondary      = TikTokColors.Cyan,
    background     = TikTokColors.Canvas,
    onBackground   = TikTokColors.TextPrimary,
    surface        = TikTokColors.Surface,
    onSurface      = TikTokColors.TextPrimary,
    surfaceVariant = TikTokColors.InputField,
    error          = TikTokColors.Rose, // TikTok reuses rose for error
)

@Composable
fun TikTokTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = TikTokScheme, typography = TikTokTypography, content = content)
// Dark-only — never provide a lightColorScheme.
```

## 2. Typography

Proxima Nova is a licensed third-party face (Mark Simonson). Drop the TTF/OTF files in `res/font/` (lowercase, snake_case). Fall back to the system font (Roboto) — its geometric-humanist tone is the closest free substitute on Android.

```kotlin
// ui/theme/TikTokType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val ProximaNova = FontFamily(
    Font(R.font.proximanova_regular,  FontWeight.Normal),   // 400
    Font(R.font.proximanova_medium,   FontWeight.Medium),   // 500
    Font(R.font.proximanova_semibold, FontWeight.SemiBold), // 600
    Font(R.font.proximanova_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, weights preserved)
object TikTokText {
    val DisplayName   = TextStyle(ProximaNova, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp)
    val SheetTitle    = TextStyle(ProximaNova, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val Username      = TextStyle(ProximaNova, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 19.sp)
    val Caption       = TextStyle(ProximaNova, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp)
    val CaptionHashtag = TextStyle(ProximaNova, fontWeight = FontWeight.Bold,    fontSize = 15.sp, lineHeight = 20.sp)
    val UsernameList  = TextStyle(ProximaNova, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 19.sp)
    val ActionCount   = TextStyle(ProximaNova, fontWeight = FontWeight.Bold,     fontSize = 13.sp, lineHeight = 16.sp)
    val Music         = TextStyle(ProximaNova, fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 16.sp)
    val Body          = TextStyle(ProximaNova, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val Meta          = TextStyle(ProximaNova, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Tab           = TextStyle(ProximaNova, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val ButtonPrimary = TextStyle(ProximaNova, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 16.sp)
    val ButtonSecondary = TextStyle(ProximaNova, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val Follow        = TextStyle(ProximaNova, fontWeight = FontWeight.Bold,     fontSize = 14.sp, lineHeight = 14.sp)
    val Chip          = TextStyle(ProximaNova, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val TikTokTypography = Typography(
    headlineMedium = TikTokText.DisplayName,
    titleMedium    = TikTokText.SheetTitle,
    bodyMedium     = TikTokText.Body,
    labelLarge     = TikTokText.ButtonPrimary,
    labelSmall     = TikTokText.Tab,
)
```

### Text-on-Video Shadow Modifier

Every label overlaid on a video needs this — it is non-negotiable for legibility over bright frames.

```kotlin
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Shadow
import androidx.compose.ui.text.TextStyle

// For Text, fold the shadow into the TextStyle so it renders per-glyph
fun TextStyle.onVideo(): TextStyle = copy(
    shadow = Shadow(color = TikTokColors.ScrimMedium, offset = Offset(0f, 1f), blurRadius = 4f),
)

// For icons / Boxes, use a drop shadow modifier
fun Modifier.tiktokIconShadow(): Modifier =
    this.shadow(elevation = 3.dp, clip = false, ambientColor = TikTokColors.ScrimLight, spotColor = TikTokColors.ScrimLight)
```

## 3. Signature Components

### Follow Button (the iconic rose pill — 4dp corner, not full pill)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun TikTokFollowButton(
    isFollowing: Boolean,
    onToggle: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.95f else 1f, spring(dampingRatio = 0.7f), label = "followScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .heightIn(min = 28.dp)
            .scale(scale)
            .clip(RoundedCornerShape(4.dp)) // TikTok-signature "almost-square", NOT a full pill
            .background(if (isFollowing) TikTokColors.FollowerGray else TikTokColors.Rose)
            .clickable(interaction, indication = null) {
                // success haptic on follow, soft on unfollow
                haptics.performHapticFeedback(
                    if (!isFollowing) HapticFeedbackType.LongPress else HapticFeedbackType.TextHandleMove
                )
                onToggle()
            }
            .padding(horizontal = 16.dp, vertical = 6.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(
            if (isFollowing) "Following" else "Follow",
            style = if (isFollowing) TikTokText.ButtonSecondary else TikTokText.Follow,
            color = TikTokColors.TextPrimary,
        )
    }
}
```

### Chromatic Aberration Modifier (the brand thesis)

iOS stacks three offset copies. Compose: render the same content three times — cyan offset -x, rose offset +x, white core on top — in a `Box`.

```kotlin
import androidx.compose.ui.graphics.graphicsLayer

@Composable
fun ChromaticAberration(
    offsetDp: Float = 3f,           // scales with size: 1 @16sp, 3 @44dp, 6 @96dp
    content: @Composable (color: androidx.compose.ui.graphics.Color) -> Unit,
) {
    Box {
        Box(Modifier.graphicsLayer { translationX = -offsetDp * density }) { content(TikTokColors.Cyan) }
        Box(Modifier.graphicsLayer { translationX = offsetDp * density }) { content(TikTokColors.Rose) }
        content(TikTokColors.TextPrimary) // white core on top
    }
}

// Usage — the glitch wordmark:
// ChromaticAberration(offsetDp = 6f) { c ->
//     Text("d", style = TextStyle(fontSize = 96.sp, fontWeight = FontWeight.Bold), color = c)
// }
```

### Create Tab Button (chromatic, the centered anchor)

```kotlin
import androidx.compose.foundation.layout.size
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material3.Icon

@Composable
fun TikTokCreateButton(
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.94f else 1f, label = "createScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .size(width = 44.dp, height = 30.dp)
            .scale(scale)
            .clip(RoundedCornerShape(8.dp))
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // heavy
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        // cyan left edge, rose right edge, white through the middle
        Box(Modifier.matchParentSize().graphicsLayer { translationX = -3 * density }.background(TikTokColors.Cyan))
        Box(Modifier.matchParentSize().graphicsLayer { translationX = 3 * density }.background(TikTokColors.Rose))
        Box(Modifier.matchParentSize().background(TikTokColors.TextPrimary))
        Icon(Icons.Filled.Add, "Create", tint = TikTokColors.Canvas, modifier = Modifier.size(18.dp))
    }
}
```

### Right-Side Action Rail (the load-bearing feed UX)

```kotlin
import androidx.compose.animation.core.LinearEasing
import androidx.compose.animation.core.RepeatMode
import androidx.compose.animation.core.infiniteRepeatable
import androidx.compose.animation.core.rememberInfiniteTransition
import androidx.compose.animation.core.tween
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.*
import androidx.compose.ui.draw.rotate
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun ActionRail(
    avatarUrl: String,
    isFollowed: Boolean,
    onFollow: () -> Unit,
    likeCount: Int,
    isLiked: Boolean,
    onLike: () -> Unit,
    commentCount: Int,
    bookmarkCount: Int,
    shareCount: Int,
    musicArtworkUrl: String,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier = modifier.padding(end = 12.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(24.dp),
    ) {
        // Avatar + follow badge
        Box(contentAlignment = Alignment.BottomCenter) {
            AsyncImage(
                model = avatarUrl, contentDescription = null,
                modifier = Modifier.size(48.dp).clip(CircleShape).border(2.dp, TikTokColors.TextPrimary, CircleShape),
                contentScale = ContentScale.Crop,
            )
            if (!isFollowed) {
                Box(
                    Modifier
                        .offset(y = 9.dp)
                        .size(18.dp)
                        .clip(CircleShape)
                        .background(TikTokColors.Rose)
                        .clickable(onClick = onFollow),
                    contentAlignment = Alignment.Center,
                ) { Icon(Icons.Filled.Add, "Follow", tint = TikTokColors.TextPrimary, modifier = Modifier.size(10.dp)) }
            }
        }
        RailIcon(if (isLiked) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder, likeCount, if (isLiked) TikTokColors.Rose else TikTokColors.TextPrimary, onLike)
        RailIcon(Icons.Filled.Comment, commentCount, TikTokColors.TextPrimary) {}
        RailIcon(Icons.Filled.Bookmark, bookmarkCount, TikTokColors.TextPrimary) {}
        RailIcon(Icons.Filled.Reply, shareCount, TikTokColors.TextPrimary) {}
        SpinningMusicDisc(musicArtworkUrl)
    }
}

@Composable
private fun RailIcon(icon: ImageVector, count: Int, tint: Color, onClick: () -> Unit) {
    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(4.dp),
        modifier = Modifier.defaultMinSize(minWidth = 48.dp, minHeight = 48.dp).clickable(onClick = onClick),
    ) {
        Icon(icon, null, tint = tint, modifier = Modifier.size(30.dp).tiktokIconShadow())
        Text(formatCount(count), style = TikTokText.ActionCount.onVideo(), color = TikTokColors.TextPrimary)
    }
}

// Counts abbreviate — never raw integers above 1,000
fun formatCount(n: Int): String = when {
    n < 1_000 -> "$n"
    n < 1_000_000 -> "%.1fK".format(n / 1_000.0).removeSuffix(".0K").let { if (it.endsWith("K")) it else it + "K" }
    else -> "%.1fM".format(n / 1_000_000.0).removeSuffix(".0M").let { if (it.endsWith("M")) it else it + "M" }
}

@Composable
private fun SpinningMusicDisc(artworkUrl: String) {
    val t = rememberInfiniteTransition(label = "disc")
    val angle by t.animateFloat(
        0f, 360f,
        infiniteRepeatable(tween(6000, easing = LinearEasing), RepeatMode.Restart), // 360° / 6s
        label = "discAngle",
    )
    Box(
        Modifier.size(44.dp).rotate(angle).clip(CircleShape).background(TikTokColors.Canvas),
        contentAlignment = Alignment.Center,
    ) {
        AsyncImage(
            model = artworkUrl, contentDescription = null,
            modifier = Modifier.size(28.dp).clip(CircleShape),
            contentScale = ContentScale.Crop,
        )
    }
}
```

### Feed Caption Overlay (bold hashtags inline)

```kotlin
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.withStyle
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun CaptionOverlay(
    username: String,
    caption: String,     // "sunset loop #fyp #bayarea"
    musicTitle: String,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier = modifier.padding(start = 16.dp, end = 80.dp), // leave room for the action rail
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Text("@$username", style = TikTokText.Username.onVideo(), color = TikTokColors.TextPrimary)
        Text(
            buildAnnotatedString {
                caption.split(" ").forEachIndexed { i, token ->
                    if (i > 0) append(" ")
                    if (token.startsWith("#")) {
                        withStyle(SpanStyle(fontWeight = FontWeight.Bold)) { append(token) } // hashtags bold inline
                    } else append(token)
                }
            },
            style = TikTokText.Caption.onVideo(),
            color = TikTokColors.TextPrimary,
            maxLines = 2,
            overflow = TextOverflow.Ellipsis,
        )
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Icon(Icons.Filled.MusicNote, null, tint = TikTokColors.TextPrimary, modifier = Modifier.size(12.dp))
            Text(musicTitle, style = TikTokText.Music.onVideo(), color = TikTokColors.TextPrimary, maxLines = 1)
        }
    }
}
```

### Video Progress Scrubber

```kotlin
@Composable
fun VideoScrubber(progress: Float, isScrubbing: Boolean, modifier: Modifier = Modifier) {
    val height by animateFloatAsState(if (isScrubbing) 4f else 2f, label = "scrubH")
    Box(
        modifier
            .fillMaxWidth()
            .height(height.dp)
            .background(TikTokColors.ScrubberTrack),
    ) {
        Box(Modifier.fillMaxWidth(progress).height(height.dp).background(TikTokColors.Rose))
    }
}
```

## 4. Full-Bleed Vertical Feed + Double-Tap Burst (the signature system)

TikTok's thesis: a 9:16 video fills the screen edge-to-edge (ignoring safe areas for *content*), UI floats on top, and a double-tap anywhere spawns a 120dp rose heart. iOS uses a rotated paged `TabView` + a tap overlay; Compose uses a vertical `Pager` with the video drawn behind the overlays, plus `detectTapGestures(onDoubleTap)`.

```kotlin
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.foundation.pager.VerticalPager
import androidx.compose.foundation.pager.rememberPagerState
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.unit.IntOffset
import kotlinx.coroutines.delay
import kotlin.math.roundToInt

@Composable
fun TikTokFeed(
    pageCount: Int,
    videoContent: @Composable (page: Int) -> Unit, // wrap ExoPlayer PlayerView in AndroidView
    overlayContent: @Composable (page: Int) -> Unit,
) {
    val pagerState = rememberPagerState(pageCount = { pageCount })
    VerticalPager(
        state = pagerState,
        modifier = Modifier.fillMaxSize().background(TikTokColors.Canvas),
    ) { page ->
        Box(Modifier.fillMaxSize()) {
            // video ignores safe areas and fills edge-to-edge
            Box(Modifier.fillMaxSize()) { videoContent(page) }
            DoubleTapLikeLayer { /* register like */ }
            overlayContent(page)
        }
    }
}

@Composable
fun DoubleTapLikeLayer(onLike: () -> Unit) {
    val hearts = remember { mutableStateListOf<HeartBurst>() }
    val haptics = LocalHapticFeedback.current

    Box(
        Modifier
            .fillMaxSize()
            .pointerInput(Unit) {
                detectTapGestures(
                    onDoubleTap = { pos ->
                        val id = System.nanoTime()
                        hearts.add(HeartBurst(id, pos))
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress) // heavy on first tap
                        onLike()
                    },
                )
            },
    ) {
        hearts.forEach { h -> key(h.id) { HeartBurstView(h) { hearts.remove(h) } } }
    }
}

data class HeartBurst(val id: Long, val pos: Offset)

@Composable
private fun HeartBurstView(burst: HeartBurst, onDone: () -> Unit) {
    val scale = remember { androidx.compose.animation.core.Animatable(0f) }
    val alpha = remember { androidx.compose.animation.core.Animatable(1f) }
    LaunchedEffect(burst.id) {
        // 0 → 1.4 → 1.0 over 400ms spring, then fade 1 → 0 over 200ms
        scale.animateTo(1.4f, spring(dampingRatio = 0.55f, stiffness = 500f))
        scale.animateTo(1.0f, spring(dampingRatio = 0.7f))
        alpha.animateTo(0f, tween(200))
        onDone()
    }
    Icon(
        Icons.Filled.Favorite, null,
        tint = TikTokColors.Rose,
        modifier = Modifier
            .offset { IntOffset((burst.pos.x - 60.dp.toPx()).roundToInt(), (burst.pos.y - 60.dp.toPx()).roundToInt()) }
            .size(120.dp)
            .scale(scale.value)
            .androidx.compose.ui.draw.alpha(alpha.value),
    )
}
```

### Chromatic Loading Spinner

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.geometry.Offset as GOffset

@Composable
fun TikTokLoadingSpinner(modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "spinner")
    val angle by t.animateFloat(0f, 360f, infiniteRepeatable(tween(1000, easing = LinearEasing)), label = "spin")
    Canvas(modifier.size(32.dp).rotate(angle)) {
        val sw = 3.dp.toPx()
        fun arc(color: androidx.compose.ui.graphics.Color, dx: Float) = drawArc(
            color = color, startAngle = 0f, sweepAngle = 270f, useCenter = false,
            topLeft = GOffset(dx, 0f), size = androidx.compose.ui.geometry.Size(size.width, size.height),
            style = Stroke(width = sw),
        )
        arc(TikTokColors.Cyan, -3.dp.toPx())
        arc(TikTokColors.Rose, 3.dp.toPx())
        arc(TikTokColors.TextPrimary, 0f)
    }
}
```

## 5. Navigation (Tab Bar)

TikTok's iOS tab bar is **transparent over the feed** (only text-shadowed glyphs) and a `.regularMaterial` blur over `rgba(1,1,1,0.92)` on every other screen, with the centered chromatic Create button. Android has no first-class live blur — use a translucent `Surface` (`Canvas` at ~92% alpha) on non-feed screens and full transparency on the feed. Build a custom row (not `NavigationBar`) so you can swap materials and embed the Create button.

```kotlin
import androidx.compose.foundation.layout.Spacer
import androidx.compose.material3.HorizontalDivider

enum class TikTokTab { Home, Discover, Inbox, Profile }

@Composable
fun TikTokBottomBar(
    selected: TikTokTab,
    onSelect: (TikTokTab) -> Unit,
    onCreate: () -> Unit,
    isOnFeed: Boolean,
    modifier: Modifier = Modifier,
) {
    Box(modifier.fillMaxWidth()) {
        Column {
            if (!isOnFeed) HorizontalDivider(color = TikTokColors.TextPrimary.copy(alpha = 0.08f), thickness = 0.5.dp)
            Row(
                Modifier
                    .fillMaxWidth()
                    .height(48.dp)
                    .background(if (isOnFeed) Color.Transparent else TikTokColors.Canvas.copy(alpha = 0.92f))
                    .padding(horizontal = 8.dp),
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Tab(TikTokTab.Home, Icons.Filled.Home, "Home", selected, isOnFeed, onSelect, Modifier.weight(1f))
                Tab(TikTokTab.Discover, Icons.Filled.Search, "Discover", selected, isOnFeed, onSelect, Modifier.weight(1f))
                Spacer(Modifier.width(56.dp)) // room for Create
                Tab(TikTokTab.Inbox, Icons.Filled.Inbox, "Inbox", selected, isOnFeed, onSelect, Modifier.weight(1f))
                Tab(TikTokTab.Profile, Icons.Filled.Person, "Profile", selected, isOnFeed, onSelect, Modifier.weight(1f))
            }
        }
        TikTokCreateButton(onClick = onCreate, modifier = Modifier.align(Alignment.Center))
    }
}

@Composable
private fun Tab(
    tab: TikTokTab, icon: ImageVector, label: String,
    selected: TikTokTab, isOnFeed: Boolean, onSelect: (TikTokTab) -> Unit, modifier: Modifier,
) {
    Column(
        modifier = modifier.clickable { onSelect(tab) },
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(3.dp),
    ) {
        val tint = if (selected == tab) TikTokColors.TextPrimary else TikTokColors.TextPrimary.copy(alpha = 0.5f)
        Icon(icon, label, tint = tint, modifier = Modifier.size(24.dp).then(if (isOnFeed) Modifier.tiktokIconShadow() else Modifier))
        Text(label, style = if (isOnFeed) TikTokText.Tab.onVideo() else TikTokText.Tab, color = tint)
    }
}
```

The top feed tabs ("Following" / "For You") are just two text labels floating on the video with a 2dp animated underline — no container; render them in a `Row` above the feed with `Modifier.windowInsetsPadding(WindowInsets.statusBars)`.

## 6. Motion & Haptics

| Moment | Compose recipe |
|--------|----------------|
| Swipe up for next video | `VerticalPager`, ~300ms `spring(dampingRatio = 0.85f)` paging |
| Swipe right/left (profile / For You) | horizontal `Pager` with destination parallax (`graphicsLayer`) |
| Double-tap like | heart `Animatable` 0 → 1.4 → 1.0 over 400ms `spring`, fade 1 → 0 `tween(200)`; `HapticFeedbackType.LongPress` (heavy) |
| Heart icon tap | `Animatable` 1 → 1.3 → 1.0 `spring`, rose fill swap, heavy haptic |
| Follow button | pill recolors rose → translucent over ~200ms, scale 0.95 → 1.0; success haptic on follow |
| Spinning music disc | `rememberInfiniteTransition` 360° / 6s `LinearEasing`, pauses with video |
| Comment sheet | `ModalBottomSheet` slide-up ~350ms `spring(dampingRatio = 0.85f)` |
| Create tap | tab bar dims ~0.8s, camera UI fades in `tween(400)` |

```kotlin
// Heart single-tap — 1.0 → 1.3 → 1.0 bounce + heavy haptic + rose swap
@Composable
fun TikTokHeart(isLiked: Boolean, onToggle: () -> Unit) {
    val scale = remember { androidx.compose.animation.core.Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(isLiked) {
        if (isLiked) {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // heavy
            scale.animateTo(1.3f, spring(dampingRatio = 0.5f, stiffness = 600f))
            scale.animateTo(1.0f, spring(dampingRatio = 0.7f))
        }
    }
    Icon(
        if (isLiked) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder,
        contentDescription = if (isLiked) "Unlike" else "Like",
        tint = if (isLiked) TikTokColors.Rose else TikTokColors.TextPrimary,
        modifier = Modifier.size(30.dp).scale(scale.value).tiktokIconShadow().clickable(onClick = onToggle),
    )
}
```

Haptics: `LocalHapticFeedback` `LongPress` approximates iOS heavy/success; `TextHandleMove` approximates soft. For a true graded *heavy* cue on double-tap-like, use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.LONG_PRESS)` or a `Vibrator` `VibrationEffect.createOneShot(18, 255)`.

## 7. Icons

TikTok uses custom glyphs (the chromatic wordmark especially). The closest first-party set is `androidx.compose.material:material-icons-extended`; ship the chromatic wordmark and any brand-exact glyphs as vector drawables loaded via `ImageVector.vectorResource(R.drawable.…)`.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Heart (idle / active) | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Icons.Filled.Favorite` |
| Comment | `ellipsis.bubble.fill` | `Icons.Filled.Comment` |
| Bookmark | `bookmark.fill` | `Icons.Filled.Bookmark` |
| Share | `arrowshape.turn.up.right.fill` | `Icons.Filled.Reply` |
| Music note | `music.note` | `Icons.Filled.MusicNote` |
| Create plus | `plus` | `Icons.Filled.Add` |
| Home tab | `house.fill` | `Icons.Filled.Home` |
| Discover tab | `magnifyingglass` | `Icons.Filled.Search` |
| Inbox tab | `tray.fill` | `Icons.Filled.Inbox` |
| Profile tab | `person.fill` | `Icons.Filled.Person` |
| Back | `chevron.backward` | `Icons.Filled.ArrowBackIosNew` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Send | `paperplane.fill` | `Icons.Filled.Send` |
| Live | `dot.radiowaves.left.and.right` | `Icons.Filled.SensorsOff` (or custom live drawable) |
| Wordmark "d" | custom chromatic glyph | vector drawable via `ChromaticAberration` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `VerticalPager`, ExoPlayer, and the chromatic motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The **video content draws under the system bars** (the 9:16 frame fills edge-to-edge, including under the camera cutout); only the *UI overlays* respect insets via `Modifier.windowInsetsPadding(WindowInsets.safeDrawing)`. Force a light status bar (white over video) since there is no light mode.
- **Font scaling**: `sp` honors the user's font scale — sheet content (comments, inbox, settings) scales fully. **Pin** the feed overlays (caption, username, action counts) and the 10sp tab labels via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` — the 9:16 layout is tight and scaling breaks it.
- **TalkBack**: label the heart "Like video by @username, 24.5K likes" and the Follow button "Follow @username" via `Modifier.semantics`. Mark the spinning music disc decorative unless it opens the audio page (then expose it as a button). The double-tap layer should also expose a single-tap accessible "Like" action.
- **Touch targets**: Material guidance is 48.dp. The right-rail glyphs are 30dp on a 48dp `defaultMinSize` target; the Create button is 44×30dp with a ~56dp effective hit area — verify with `Modifier.minimumInteractiveComponentSize()`.
- **Contrast**: white text on video is reinforced by the `rgba(0,0,0,0.4)` shadow folded into `TextStyle.onVideo()` — validate on bright-frame samples; never render white text on a gray surface without it.
- **Reduce motion**: detect `Settings.Global.ANIMATOR_DURATION_SCALE == 0f` (or a stored pref) — freeze the spinning music disc and skip the double-tap burst spring (fall back to an instant heart fade), but keep the heavy haptic.
- **Orientation**: lock the feed to portrait (`android:screenOrientation="portrait"` on the feed Activity, or `requestedOrientation`) — the 9:16 video is the entire thesis. The video-player modal from the profile grid may rotate to landscape.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — TikTok's brand is the fixed rose/cyan-on-`#010101` chromatic identity; a wallpaper-derived palette would destroy the aberration treatment and the verb/accent split.
