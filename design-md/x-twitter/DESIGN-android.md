# X (Twitter) (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports X's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, the per-action color micro-animations, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (X's true-black OLED canvas, hairline-separated feed, single-blue structural accent, the three 1:1 action colors, the like-burst micro-interaction) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `HorizontalDivider` hairlines instead of card surfaces, a translucent `Surface` instead of `.regularMaterial` blur, `sp`/`dp` instead of `pt`. The app is true-black-first with dim and light alternates; all three are wired below.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars and post media. No color extraction — X's palette is fixed brand chrome.

## 1. Color Tokens

```kotlin
// ui/theme/XColors.kt
import androidx.compose.ui.graphics.Color

object XColors {
    // Canvas & surfaces (dark / default)
    val Canvas    = Color(0xFF000000) // true-black OLED
    val Surface1  = Color(0xFF16181C)
    val Surface2  = Color(0xFF1E2024)
    val Divider   = Color(0xFF2F3336) // the signature 1pt hairline
    val DimCanvas   = Color(0xFF15202B)
    val DimSurface1 = Color(0xFF192734)
    val DimDivider  = Color(0xFF38444D)

    // Canvas & surfaces (light)
    val LightCanvas   = Color(0xFFFFFFFF)
    val LightSurface1 = Color(0xFFF7F9F9)
    val LightSurface2 = Color(0xFFEFF3F4)

    // Text
    val TextPrimaryDark    = Color(0xFFE7E9EA) // warm off-white, not pure white
    val TextPrimaryLight   = Color(0xFF0F1419)
    val TextSecondaryDark  = Color(0xFF71767B)
    val TextSecondaryLight = Color(0xFF536471)

    // Brand / action
    val Blue        = Color(0xFF1D9BF0) // links, replies, active underline, verified
    val BluePressed = Color(0xFF1A8CD8)
    val RepostGreen = Color(0xFF00BA7C)
    val LikePink    = Color(0xFFF91880) // not red — saturated magenta-pink
    val VerifiedGold = Color(0xFFEAB308)
    val VerifiedGray = Color(0xFF829AAB)
    val ErrorRed    = Color(0xFFF4212E)
    val SpacesPurple = Color(0xFF7856FF)
}
```

X opens **dark (true black)** for new users, with dim and light alternates. Wire the dark scheme as the default; `primary` is the structural blue. Note the **active tab tint is the stark off-white `#E7E9EA`, not blue** — blue is reserved for links/replies/verified/the feed-filter underline.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val XDark = darkColorScheme(
    primary        = XColors.Blue,
    onPrimary      = XColors.TextPrimaryDark,
    background     = XColors.Canvas,
    onBackground   = XColors.TextPrimaryDark,
    surface        = XColors.Surface1,
    onSurface      = XColors.TextPrimaryDark,
    surfaceVariant = XColors.Surface2,
    outline        = XColors.Divider,
    error          = XColors.ErrorRed,
)

private val XLight = lightColorScheme(
    primary        = XColors.Blue,
    onPrimary      = XColors.LightCanvas,
    background     = XColors.LightCanvas,
    onBackground   = XColors.TextPrimaryLight,
    surface        = XColors.LightSurface1,
    onSurface      = XColors.TextPrimaryLight,
    surfaceVariant = XColors.LightSurface2,
    outline        = XColors.LightSurface2,
    error          = XColors.ErrorRed,
)

@Composable
fun XTheme(
    darkTheme: Boolean = isSystemInDarkTheme(), // default-dark; expose dim as a third option in-app
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (darkTheme) XDark else XLight,
    typography = XTypography,
    content = content,
)
```

## 2. Typography

X licenses **Chirp** (Swiss Typefaces & Grilli Type, 2021) — a grotesque sans shipped in UI and Display optical sizes. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to **Roboto** (`FontFamily.Default`) — the closest grotesque on Android. The app switches optical size at the 17sp threshold; model it with two families.

```kotlin
// ui/theme/XType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val ChirpUI = FontFamily(
    Font(R.font.chirp_regular,  FontWeight.Normal),   // 400
    Font(R.font.chirp_medium,   FontWeight.Medium),   // 500
    Font(R.font.chirp_bold,     FontWeight.Bold),     // 700
)
val ChirpDisplay = FontFamily( // more open — used at 17sp+
    Font(R.font.chirp_display_bold, FontWeight.Bold),
    Font(R.font.chirp_regular,      FontWeight.Normal),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object XText {
    val ScreenTitle   = TextStyle(ChirpDisplay, fontWeight = FontWeight.Bold,   fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val SectionHeader = TextStyle(ChirpDisplay, fontWeight = FontWeight.Bold,   fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val DisplayName   = TextStyle(ChirpDisplay, fontWeight = FontWeight.Bold,   fontSize = 15.sp, lineHeight = 20.sp)
    val PostBody      = TextStyle(ChirpUI,      fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 20.sp)
    val QuotedBody    = TextStyle(ChirpUI,      fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 18.sp)
    val Handle        = TextStyle(ChirpUI,      fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 20.sp)
    val ActionCount   = TextStyle(ChirpUI,      fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 16.sp)
    val TrendingTopic = TextStyle(ChirpDisplay, fontWeight = FontWeight.Bold,   fontSize = 15.sp, lineHeight = 19.sp)
    val TrendingMeta  = TextStyle(ChirpUI,      fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 16.sp)
    val Button        = TextStyle(ChirpUI,      fontWeight = FontWeight.Bold,   fontSize = 15.sp, lineHeight = 15.sp)
    val DMBody        = TextStyle(ChirpUI,      fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 19.sp)
    val DMTimestamp   = TextStyle(ChirpUI,      fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 13.sp)
}

// Map onto Material 3 slots so stock components inherit the ramp
val XTypography = Typography(
    headlineLarge = XText.ScreenTitle,
    titleLarge    = XText.SectionHeader,
    titleMedium   = XText.DisplayName,
    bodyLarge     = XText.PostBody,
    bodyMedium    = XText.QuotedBody,
    labelLarge    = XText.Button,
)
```

## 3. Signature Components

### Post Row (hairline-separated, column-centered feed)

The feed is a flat river of full-bleed posts joined by 1dp `Divider`s — never cards, never shadows. Post content begins at a 68dp leading indent (16dp margin + 32dp avatar + 12dp gutter) and the action row aligns to that same indent.

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.MoreHoriz
import androidx.compose.material.icons.filled.Verified
import androidx.compose.material3.HorizontalDivider
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun XPostRow(
    avatarUrl: String,
    displayName: String,
    handle: String,
    timestamp: String,
    isVerified: Boolean,
    body: String,
    replyCount: Int,
    repostCount: Int,
    likeCount: Int,
    viewCount: Int,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    var isLiked by remember { mutableStateOf(false) }
    var isReposted by remember { mutableStateOf(false) }
    var isBookmarked by remember { mutableStateOf(false) }

    Column(
        modifier
            .fillMaxWidth()
            .background(XColors.Canvas)
            .clickable(onClick = onClick)
    ) {
        Row(
            Modifier.padding(horizontal = 16.dp, vertical = 12.dp),
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            AsyncImage(
                model = avatarUrl,
                contentDescription = "$displayName avatar",
                modifier = Modifier.size(32.dp).clip(CircleShape),
            )
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Row(verticalAlignment = Alignment.CenterVertically) {
                    Text(displayName, style = XText.DisplayName, color = XColors.TextPrimaryDark, maxLines = 1)
                    if (isVerified) {
                        Spacer(Modifier.width(2.dp))
                        Icon(Icons.Filled.Verified, contentDescription = "Verified", tint = XColors.Blue, modifier = Modifier.size(16.dp))
                    }
                    Spacer(Modifier.width(4.dp))
                    Text(
                        "@$handle · $timestamp",
                        style = XText.Handle,
                        color = XColors.TextSecondaryDark,
                        maxLines = 1,
                        overflow = TextOverflow.Ellipsis,
                        modifier = Modifier.weight(1f, fill = false),
                    )
                    Spacer(Modifier.weight(1f))
                    Icon(Icons.Filled.MoreHoriz, contentDescription = "More", tint = XColors.TextSecondaryDark, modifier = Modifier.size(18.dp))
                }
                Text(body, style = XText.PostBody, color = XColors.TextPrimaryDark)
                Row(
                    Modifier.fillMaxWidth().padding(top = 8.dp),
                    horizontalArrangement = Arrangement.SpaceBetween,
                ) {
                    XActionIcon(ActionType.Reply, replyCount, active = false) { /* open composer */ }
                    XActionIcon(ActionType.Repost, repostCount, active = isReposted) { isReposted = !isReposted }
                    XActionIcon(ActionType.Like, likeCount, active = isLiked) { isLiked = !isLiked }
                    XActionIcon(ActionType.Views, viewCount, active = false) {}
                    XActionIcon(ActionType.Bookmark, 0, active = isBookmarked) { isBookmarked = !isBookmarked }
                }
            }
        }
        HorizontalDivider(thickness = 1.dp, color = XColors.Divider)
    }
}
```

### Action Icon (per-action color micro-animation — see §4)

```kotlin
import androidx.compose.material.icons.filled.BarChart
import androidx.compose.material.icons.filled.Bookmark
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.Repeat
import androidx.compose.material.icons.outlined.BookmarkBorder
import androidx.compose.material.icons.outlined.ChatBubbleOutline
import androidx.compose.material.icons.outlined.FavoriteBorder

enum class ActionType { Reply, Repost, Like, Views, Bookmark }

@Composable
fun XActionIcon(
    type: ActionType,
    count: Int,
    active: Boolean,
    onClick: () -> Unit,
) {
    val activeColor = when (type) {
        ActionType.Reply, ActionType.Bookmark -> XColors.Blue
        ActionType.Repost -> XColors.RepostGreen
        ActionType.Like -> XColors.LikePink
        ActionType.Views -> XColors.TextSecondaryDark
    }
    val tint = if (active) activeColor else XColors.TextSecondaryDark
    val icon = when (type) {
        ActionType.Reply -> Icons.Outlined.ChatBubbleOutline
        ActionType.Repost -> Icons.Filled.Repeat
        ActionType.Like -> if (active) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder
        ActionType.Views -> Icons.Filled.BarChart
        ActionType.Bookmark -> if (active) Icons.Filled.Bookmark else Icons.Outlined.BookmarkBorder
    }
    Row(
        Modifier
            .sizeIn(minWidth = 44.dp, minHeight = 44.dp)
            .clickable(onClick = onClick),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Icon(icon, contentDescription = type.name, tint = tint, modifier = Modifier.size(18.75.dp))
        if (count > 0) Text(formatCount(count), style = XText.ActionCount, color = tint)
    }
}

fun formatCount(n: Int): String = when {
    n >= 1_000_000 -> "%.1fM".format(n / 1_000_000f)
    n >= 1_000 -> "%.1fK".format(n / 1_000f)
    else -> "$n"
}
```

### Floating Post Button (inverted FAB)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material.icons.filled.Create
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun XPostFab(onClick: () -> Unit, modifier: Modifier = Modifier, darkTheme: Boolean = true) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.95f else 1f, label = "fabScale")
    val haptics = LocalHapticFeedback.current
    // Inverted from canvas: white-on-black (dark) / black-on-white (light)
    val bg = if (darkTheme) XColors.LightCanvas else XColors.TextPrimaryLight
    val fg = if (darkTheme) XColors.Canvas else XColors.LightCanvas

    Box(
        modifier = modifier
            .padding(end = 16.dp, bottom = 16.dp)
            .size(56.dp)
            .scale(scale)
            .shadow(12.dp, CircleShape, ambientColor = Color.Black.copy(alpha = 0.4f))
            .clip(CircleShape)
            .background(bg)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(Icons.Filled.Create, contentDescription = "Compose post", tint = fg, modifier = Modifier.size(24.dp))
    }
}
```

### Follow Pill

```kotlin
import androidx.compose.foundation.border

@Composable
fun XFollowPill(isFollowing: Boolean, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Box(
        modifier = modifier
            .widthIn(min = 80.dp)
            .clip(CircleShape)
            .then(
                if (isFollowing) Modifier.border(1.dp, XColors.TextSecondaryDark, CircleShape)
                else Modifier.background(XColors.TextPrimaryDark)
            )
            .clickable(onClick = onClick)
            .padding(horizontal = 16.dp, vertical = 8.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(
            if (isFollowing) "Following" else "Follow", // sentence case — never uppercase
            style = XText.Button,
            color = if (isFollowing) XColors.TextPrimaryDark else XColors.Canvas,
        )
    }
}
```

## 4. Per-Action Color Micro-Animations + Column-Centered Feed

X's most recognizable micro-interaction: each action icon scale-bounces, snaps to its role color, the count ticks up, and a `.impact(.light)` haptic fires. Like adds a 6-particle hexagonal burst; repost spins the glyph 360°.

```kotlin
import androidx.compose.animation.AnimatedContent
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween
import androidx.compose.animation.slideInVertically
import androidx.compose.animation.slideOutVertically
import androidx.compose.animation.fadeIn
import androidx.compose.animation.fadeOut
import androidx.compose.animation.togetherWith
import androidx.compose.ui.graphics.graphicsLayer
import kotlin.math.cos
import kotlin.math.sin

/** Like icon: scale 0.85 → 1.2 → 1, 6-particle hexagonal burst, count tick-up, light haptic. */
@Composable
fun XLikeButton(initialCount: Int) {
    var liked by remember { mutableStateOf(false) }
    var count by remember { mutableIntStateOf(initialCount) }
    val scale = remember { Animatable(1f) }
    val burst = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current

    LaunchedEffect(liked) {
        if (liked) {
            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS .impact(.light)
            scale.snapTo(0.85f)
            burst.snapTo(0f)
            kotlinx.coroutines.coroutineScope {
                launch { scale.animateTo(1.2f, spring(dampingRatio = 0.55f, stiffness = 600f)); scale.animateTo(1f) }
                launch { burst.animateTo(1f, tween(400)) }
            }
        }
    }

    Row(
        Modifier
            .sizeIn(minWidth = 44.dp, minHeight = 44.dp)
            .clickable { liked = !liked; count += if (liked) 1 else -1 },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Box(contentAlignment = Alignment.Center) {
            // Hexagonal particle burst
            if (burst.value > 0f && burst.value < 1f) {
                repeat(6) { i ->
                    val angle = i * Math.PI / 3
                    Box(
                        Modifier
                            .size(4.dp)
                            .graphicsLayer {
                                translationX = (cos(angle) * 18 * burst.value).toFloat() * density
                                translationY = (sin(angle) * 18 * burst.value).toFloat() * density
                                alpha = 1f - burst.value
                            }
                            .clip(CircleShape)
                            .background(XColors.LikePink)
                    )
                }
            }
            Icon(
                imageVector = if (liked) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
                contentDescription = "Like",
                tint = if (liked) XColors.LikePink else XColors.TextSecondaryDark,
                modifier = Modifier.size(18.75.dp).scale(scale.value),
            )
        }
        // Count tick-up: old value slides up + fades, new value slides in from below
        AnimatedContent(
            targetState = count,
            transitionSpec = {
                (slideInVertically { it } + fadeIn()) togetherWith (slideOutVertically { -it } + fadeOut())
            },
            label = "likeCount",
        ) { c ->
            Text(formatCount(c), style = XText.ActionCount, color = if (liked) XColors.LikePink else XColors.TextSecondaryDark)
        }
    }
}

/** Repost icon spins 0 → 360° as it fills green. */
@Composable
fun XRepostButton(initialCount: Int) {
    var reposted by remember { mutableStateOf(false) }
    val rotation = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(reposted) {
        if (reposted) {
            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
            rotation.animateTo(rotation.value + 360f, tween(400))
        }
    }
    Icon(
        Icons.Filled.Repeat,
        contentDescription = "Repost",
        tint = if (reposted) XColors.RepostGreen else XColors.TextSecondaryDark,
        modifier = Modifier
            .size(18.75.dp)
            .graphicsLayer { rotationZ = rotation.value }
            .clickable { reposted = !reposted },
    )
}
```

The **column-centered feed** is a single `LazyColumn` of `XPostRow`s separated only by their built-in `HorizontalDivider` — set `verticalArrangement = Arrangement.spacedBy(0.dp)` (posts touch; the hairline is the only separator). On wide breakpoints (`> 840.dp`), constrain the feed column to a max width and center it, mirroring iPad split view.

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`, **icon-only with no labels** — X's signature pattern. The iOS bar is `.regularMaterial` blur at ~85% over canvas; Android has no first-class live blur, so use an 85%-opaque canvas surface. **Active tint is the stark off-white `#E7E9EA` (not blue)**; active icons are filled, inactive outlined.

```kotlin
import androidx.compose.material.icons.filled.Home
import androidx.compose.material.icons.filled.Mail
import androidx.compose.material.icons.filled.Notifications
import androidx.compose.material.icons.filled.People
import androidx.compose.material.icons.filled.Search
import androidx.compose.material.icons.outlined.HomeOutlined
import androidx.compose.material.icons.outlined.MailOutline
import androidx.compose.material.icons.outlined.NotificationsNone
import androidx.compose.material.icons.outlined.PeopleOutline
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun XBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = XColors.Canvas.copy(alpha = 0.85f),
        tonalElevation = 0.dp,
    ) {
        data class Tab(val filled: androidx.compose.ui.graphics.vector.ImageVector, val outlined: androidx.compose.ui.graphics.vector.ImageVector, val cd: String)
        val tabs = listOf(
            Tab(Icons.Filled.Home, Icons.Outlined.HomeOutlined, "Home"),
            Tab(Icons.Filled.Search, Icons.Filled.Search, "Search"),
            Tab(Icons.Filled.People, Icons.Outlined.PeopleOutline, "Communities"),
            Tab(Icons.Filled.Notifications, Icons.Outlined.NotificationsNone, "Notifications"),
            Tab(Icons.Filled.Mail, Icons.Outlined.MailOutline, "Messages"),
        )
        tabs.forEachIndexed { i, tab ->
            val isSel = selected == i
            NavigationBarItem(
                selected = isSel,
                onClick = { onSelect(i) },
                icon = {
                    Icon(
                        if (isSel) tab.filled else tab.outlined,
                        contentDescription = tab.cd,
                        modifier = Modifier.size(26.dp),
                    )
                },
                label = null, // icon-only — X's signature
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = XColors.TextPrimaryDark, // off-white, NOT blue
                    unselectedIconColor = XColors.TextSecondaryDark,
                    indicatorColor      = Color.Transparent, // no Material pill — X has none
                ),
            )
        }
    }
}
```

The **feed filter** ("For you" / "Following") sits below the top nav: two Chirp 15sp/700 labels; the active one gets a 40dp × 4dp `#1D9BF0` pill underline that slides between positions via an `animateDpAsState` offset (the Compose analog to `matchedGeometryEffect`).

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Like tap | `Animatable` scale 0.85 → 1.2 → 1 `spring(dampingRatio = 0.55f)`; 6-particle `graphicsLayer` burst over 400ms; `AnimatedContent` count tick-up; `HapticFeedbackType.TextHandleMove` |
| Repost tap | `Animatable` `rotationZ` += 360 over 400ms `tween` as tint → green; light haptic |
| Reply tap | icon scale 0.9 → 1.05 → 1 `spring`; navigate to compose (`slideInVertically`) |
| Feed swipe For You ↔ Following | `HorizontalPager` 280ms spring; underline `animateDpAsState` offset (≈ `matchedGeometryEffect`) |
| Pull-to-refresh | custom: a line that expands into the X glyph as overscroll grows, then spins |
| Tab switch | instant `Crossfade` (no slide) |
| Compose open | `ModalBottomSheet` / full-screen route `slideInVertically` 320ms spring + scrim fade |

```kotlin
// Feed filter underline — Android's analog to iOS .matchedGeometryEffect
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.foundation.layout.offset

@Composable
fun XFeedFilter(selected: Int, onSelect: (Int) -> Unit) {
    val labels = listOf("For you", "Following")
    BoxWithConstraints(Modifier.fillMaxWidth().height(48.dp)) {
        val tabWidth = maxWidth / 2
        val indicatorX by animateDpAsState(
            targetValue = tabWidth * selected + (tabWidth - 40.dp) / 2,
            animationSpec = spring(dampingRatio = 0.8f, stiffness = 400f),
            label = "filterUnderline",
        )
        Row(Modifier.fillMaxSize()) {
            labels.forEachIndexed { i, label ->
                Box(
                    Modifier.weight(1f).fillMaxHeight().clickable { onSelect(i) },
                    contentAlignment = Alignment.Center,
                ) {
                    Text(
                        label,
                        style = XText.Button,
                        color = if (selected == i) XColors.TextPrimaryDark else XColors.TextSecondaryDark,
                    )
                }
            }
        }
        Box(
            Modifier
                .offset(x = indicatorX)
                .align(Alignment.BottomStart)
                .width(40.dp)
                .height(4.dp)
                .clip(CircleShape)
                .background(XColors.Blue)
        )
        HorizontalDivider(Modifier.align(Alignment.BottomCenter), thickness = 1.dp, color = XColors.Divider)
    }
}
```

Haptics: prefer `LocalHapticFeedback`. `HapticFeedbackType.TextHandleMove` is the lightest stock effect ≈ iOS `.impact(.light)` for every action tap. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(8, 60)`.

## 7. Icons

X ships custom monochrome glyphs (the X logo most notably); the closest first-party set is `androidx.compose.material:material-icons-extended`. The X logotype, the precise Chirp-styled action glyphs, and the Grok sparkle should ship as vector drawables loaded via `ImageVector.vectorResource(R.drawable.…)` — the X logo is **monochrome, never tinted**.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Reply | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Repost | `arrow.2.squarepath` | `Icons.Filled.Repeat` |
| Like (outline / fill) | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Views | `chart.bar` | `Icons.Filled.BarChart` |
| Bookmark (outline / fill) | `bookmark` / `bookmark.fill` | `Icons.Outlined.BookmarkBorder` / `Icons.Filled.Bookmark` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Verified | `checkmark.seal.fill` | `Icons.Filled.Verified` |
| Overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Compose (FAB) | `pencil.and.outline` | `Icons.Filled.Create` |
| Home tab | `house.fill` | `Icons.Filled.Home` / `Icons.Outlined.HomeOutlined` |
| Search tab | `magnifyingglass` | `Icons.Filled.Search` |
| Communities tab | `person.3.fill` | `Icons.Filled.People` / `Icons.Outlined.PeopleOutline` |
| Notifications tab | `bell.fill` | `Icons.Filled.Notifications` / `Icons.Outlined.NotificationsNone` |
| Messages tab | `envelope.fill` | `Icons.Filled.Mail` / `Icons.Outlined.MailOutline` |
| Grok | `sparkles` | `Icons.Filled.AutoAwesome` (or vector drawable) |
| X logo | (custom glyph) | vector drawable — monochrome, never tinted |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `AnimatedContent`, `graphicsLayer` bursts, and `HorizontalPager` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity` with light-content system bars (the canvas is true black). The FAB floats 16.dp above the bottom bar; apply `Scaffold` insets so both clear the gesture nav, and let the top nav draw under the status bar with its translucent surface.
- **Font scaling**: `sp` honors the user's font scale — keep it on post body, quoted body, and DM body. **Pin** display name, handle, timestamp, and the 13sp action counts (layout-critical for the post-row header and action row) by deriving them from a fixed-`fontScale` `LocalDensity`.
- **TalkBack**: merge each post into a single node with `Modifier.semantics(mergeDescendants = true)` and a readable label; expose action icons as separate buttons with state-aware descriptions (`"Like post, 1.2 thousand likes"` / `"Liked"`). The avatar and overflow are independent buttons.
- **Touch targets**: Material guidance is 48.dp minimum. Action glyphs render at 18.75.dp — each is already wrapped in a `sizeIn(minWidth = 44.dp, minHeight = 44.dp)` row; bump to 48.dp if targeting strict compliance. The 32dp avatar and 20dp overflow need a 44–48dp hit area via padding.
- **Reduce Motion**: respect the system "remove animations" setting — skip the like-burst particles and the repost spin, but keep the color change, the count update, and the haptic so the action still confirms.
- **Contrast**: off-white `#E7E9EA` on `#000000` exceeds WCAG AA. `#71767B` secondary on true black is borderline at small sizes — keep handles/timestamps at 15sp and action counts at 13sp minimum; do not dim secondary text further. Like-pink `#F91880` and repost-green `#00BA7C` on black pass AA for the icon/count sizes used.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — X's identity is the fixed true-black canvas, the structural blue, and the three 1:1 action colors regardless of wallpaper. Keep the hand-built dark/light schemes (and offer dim as an explicit in-app third theme).
