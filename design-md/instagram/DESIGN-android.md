# Instagram (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Instagram's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the story ring + Create gradient, the double-tap heart, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Instagram's pure-monochrome chrome, edge-to-edge content, the rationed brand gradient, true-black dark mode) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a translucent `Surface` instead of iOS blur, `sp`/`dp` instead of `pt`. The gradient is a `Brush`, not a `LinearGradient` view.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for feed photos, avatars, and Reels. Instagram does no runtime color extraction, so no Palette dependency is needed.

## 1. Color Tokens

```kotlin
// ui/theme/InstagramColors.kt
import androidx.compose.ui.graphics.Color

object IGColors {
    // Canvas & surfaces
    val CanvasLight   = Color(0xFFFFFFFF)
    val CanvasDark    = Color(0xFF000000) // true #000000 for OLED
    val ElevatedDark  = Color(0xFF121212)
    val InputLight    = Color(0xFFEFEFEF)
    val InputDark     = Color(0xFF262626)
    val DividerLight  = Color(0xFFDBDBDB)
    val DividerDark   = Color(0xFF262626)
    val PressedRow    = Color(0xFFF5F5F5)

    // Text
    val TextSecondaryLight = Color(0xFF8E8E8E)
    val TextSecondaryDark  = Color(0xFFA8A8A8)

    // Brand / interactive
    val ActionBlue    = Color(0xFF0095F6)
    val ActionPressed = Color(0xFF1877F2)
    val Destructive   = Color(0xFFED4956) // heart-liked + destructive
    val LinkLight     = Color(0xFF00376B)
    val LinkDark      = Color(0xFFE0F1FF)

    // Status
    val OnlineGreen   = Color(0xFF78DE45)
    val CloseFriends  = Color(0xFF2FB825)

    // Brand gradient — 10-stop sweep (bottom-left → top-right)
    val GradYellow       = Color(0xFFFFDC80)
    val GradOrangeYellow = Color(0xFFFCAF45)
    val GradOrange       = Color(0xFFF77737)
    val GradRedOrange    = Color(0xFFF56040)
    val GradRed          = Color(0xFFFD1D1D)
    val GradRose         = Color(0xFFE1306C)
    val GradPurpleRed    = Color(0xFFC13584)
    val GradPurple       = Color(0xFF833AB4)
    val GradPurpleBlue   = Color(0xFF5851DB)
    val GradBlue         = Color(0xFF405DE6)
}
```

The brand gradient is a `Brush`, used **only** for unread story rings, the Create (+) action, the logotype, and the outgoing-DM bubble. Never on regular buttons, text, or chrome — the scarcity is what makes it iconic.

```kotlin
// ui/theme/InstagramGradients.kt
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush

object IGGradients {
    /** Official 10-stop story-ring / brand sweep (bottom-leading → top-trailing). */
    val brand = Brush.linearGradient(
        colors = listOf(
            IGColors.GradYellow, IGColors.GradOrangeYellow, IGColors.GradOrange,
            IGColors.GradRedOrange, IGColors.GradRed, IGColors.GradRose,
            IGColors.GradPurpleRed, IGColors.GradPurple, IGColors.GradPurpleBlue, IGColors.GradBlue,
        ),
        start = Offset(0f, Float.POSITIVE_INFINITY),
        end = Offset(Float.POSITIVE_INFINITY, 0f),
    )

    /** Short 3-stop version often used in compact UI moments. */
    val brandShort = Brush.linearGradient(
        colors = listOf(IGColors.GradPurple, IGColors.GradRed, IGColors.GradOrangeYellow),
        start = Offset(0f, Float.POSITIVE_INFINITY),
        end = Offset(Float.POSITIVE_INFINITY, 0f),
    )

    /** Outgoing DM bubble — the only gradient bubble in messaging. */
    val dmOutgoing = Brush.verticalGradient(listOf(Color(0xFF3797F0), Color(0xFF7644FF)))
}
```

Instagram supports light and full true-black dark. Wire both Material 3 schemes; keep chrome strictly monochrome so ripples and default component colors stay neutral.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val IGLight = lightColorScheme(
    primary        = IGColors.ActionBlue,
    onPrimary      = Color.White,
    background     = IGColors.CanvasLight,
    onBackground   = Color.Black,
    surface        = IGColors.CanvasLight,
    onSurface      = Color.Black,
    surfaceVariant = IGColors.InputLight,
    outline        = IGColors.DividerLight,
    error          = IGColors.Destructive,
)

private val IGDark = darkColorScheme(
    primary        = IGColors.ActionBlue,
    onPrimary      = Color.White,
    background     = IGColors.CanvasDark,   // true black, not Material's gray-black
    onBackground   = Color.White,
    surface        = IGColors.CanvasDark,
    onSurface      = Color.White,
    surfaceVariant = IGColors.InputDark,
    outline        = IGColors.DividerDark,
    error          = IGColors.Destructive,
)

@Composable
fun InstagramTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) IGDark else IGLight,
        typography  = IGTypography,
        content     = content,
    )
```

## 2. Typography

Instagram uses the system font (SF Pro on iOS → Roboto on Android) with zero customization. The only custom face is the logotype (Instagram Sans, Billabong-style script) used solely for the "Instagram" wordmark at the top of the feed — drop a script TTF in `res/font/` for that one string.

```kotlin
// ui/theme/InstagramType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextGeometricTransform
import androidx.compose.ui.unit.sp

// System font (Roboto) — Instagram introduces NO custom body typeface
private val Sys = FontFamily.Default

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1). No display-size type.
object IGText {
    val ScreenTitle   = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val UsernameLarge = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.3).sp)
    val Username      = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 18.sp, letterSpacing = (-0.15).sp)
    val Bio           = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val Caption       = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val Comment       = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val SecondaryMeta = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val ButtonPrimary = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp, letterSpacing = (-0.15).sp)
    val ButtonSmall   = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = (-0.1).sp)
    val CounterLarge  = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 18.sp, letterSpacing = (-0.2).sp)
    val DMBubble      = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 21.sp, letterSpacing = (-0.2).sp)
    val Badge         = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
    val Timestamp     = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 14.sp)
}

// Logotype — register a Billabong-style script font; this is the ONLY custom face
val IGLogotype = TextStyle(
    fontFamily = FontFamily(Font(R.font.instagram_sans)),
    fontSize = 28.sp,
)

// Map onto Material 3 slots so stock components inherit the system look
val IGTypography = Typography(
    titleMedium = IGText.ScreenTitle,
    bodyMedium  = IGText.Caption,
    labelLarge  = IGText.ButtonPrimary,
    labelSmall  = IGText.Timestamp,
)
```

Tabular figures for follower/like counters: apply `fontFeatureSettings = "tnum"` (Compose's equivalent of `monospacedDigit()`) — e.g., `CounterLarge.copy(fontFeatureSettings = "tnum")`.

## 3. Signature Components

### Story Ring (conic brand gradient)

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun StoryRing(
    avatarUrl: String,
    isUnread: Boolean,
    size: Dp = 66.dp,
) {
    Box(Modifier.size(size), contentAlignment = Alignment.Center) {
        if (isUnread) {
            // Sweep (conic) gradient ring — the signature unread state
            Canvas(Modifier.fillMaxSize()) {
                drawCircle(
                    brush = Brush.sweepGradient(
                        listOf(
                            IGColors.GradYellow, IGColors.GradOrangeYellow, IGColors.GradOrange,
                            IGColors.GradRed, IGColors.GradRose, IGColors.GradPurple, IGColors.GradYellow,
                        ),
                        center = Offset(this.size.width / 2f, this.size.height / 2f),
                    ),
                    radius = this.size.minDimension / 2f - 1.25.dp.toPx(),
                    style = Stroke(width = 2.5.dp.toPx(), cap = StrokeCap.Round),
                )
            }
        } else {
            Box(
                Modifier
                    .fillMaxSize()
                    .border(1.dp, IGColors.DividerLight, CircleShape)
            )
        }
        AsyncImage(
            model = avatarUrl,
            contentDescription = null,
            modifier = Modifier
                .size(size - 8.dp) // 3pt inset from ring (≈4dp gap each side)
                .clip(CircleShape)
                .border(2.dp, IGColors.CanvasLight, CircleShape),
            contentScale = androidx.compose.ui.layout.ContentScale.Crop,
        )
    }
}
```

### Primary Button (Follow / Log In)

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
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color

enum class IGButtonVariant { Primary, Secondary, Destructive }

@Composable
fun IGPrimaryButton(
    title: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    variant: IGButtonVariant = IGButtonVariant.Primary,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, spring(), label = "btnScale")

    val bg = when (variant) {
        IGButtonVariant.Primary     -> if (pressed) IGColors.ActionPressed else IGColors.ActionBlue
        IGButtonVariant.Secondary   -> IGColors.InputLight
        IGButtonVariant.Destructive -> Color.Transparent
    }
    val fg = when (variant) {
        IGButtonVariant.Primary     -> Color.White
        IGButtonVariant.Secondary   -> Color.Black
        IGButtonVariant.Destructive -> IGColors.Destructive
    }

    Box(
        modifier
            .scale(scale)
            .clip(RoundedCornerShape(8.dp))
            .background(bg)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 16.dp, vertical = 7.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = IGText.ButtonPrimary, color = fg)
    }
}
```

### Feed Post (edge-to-edge, double-tap to like)

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.fadeIn
import androidx.compose.animation.fadeOut
import androidx.compose.animation.scaleIn
import androidx.compose.animation.scaleOut
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.FavoriteBorder
import androidx.compose.material.icons.outlined.ChatBubbleOutline
import androidx.compose.material.icons.outlined.Send
import androidx.compose.material.icons.outlined.BookmarkBorder
import androidx.compose.material.icons.filled.MoreVert
import androidx.compose.material3.Icon
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.AnnotatedString
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextOverflow
import kotlinx.coroutines.delay

@Composable
fun FeedPost(
    username: String,
    avatarUrl: String,
    photoUrl: String,
    likes: Int,
    caption: String,
    timestamp: String,
) {
    var isLiked by remember { mutableStateOf(false) }
    var showBurst by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current

    LaunchedEffect(showBurst) {
        if (showBurst) { delay(600); showBurst = false }
    }

    Column(Modifier.fillMaxWidth()) {
        // Header (48dp)
        Row(
            Modifier.fillMaxWidth().height(48.dp).padding(horizontal = 14.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(10.dp),
        ) {
            StoryRing(avatarUrl, isUnread = false, size = 32.dp)
            Text(username, style = IGText.Username)
            Spacer(Modifier.weight(1f))
            Icon(Icons.Filled.MoreVert, contentDescription = "More", modifier = Modifier.size(24.dp))
        }

        // Photo — 1:1, edge-to-edge, NO corner radius, double-tap to like
        Box(
            Modifier
                .fillMaxWidth()
                .aspectRatio(1f)
                .pointerInput(Unit) {
                    detectTapGestures(onDoubleTap = {
                        if (!isLiked) isLiked = true
                        showBurst = true
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ iOS soft impact
                    })
                },
            contentAlignment = Alignment.Center,
        ) {
            AsyncImage(
                model = photoUrl,
                contentDescription = null,
                modifier = Modifier.matchParentSize(),
                contentScale = ContentScale.Crop,
            )
            AnimatedVisibility(
                visible = showBurst,
                enter = scaleIn(initialScale = 0f) + fadeIn(),
                exit = scaleOut(targetScale = 1.4f) + fadeOut(),
            ) {
                Icon(
                    Icons.Filled.Favorite,
                    contentDescription = null,
                    tint = Color.White,
                    modifier = Modifier.size(120.dp).shadow(8.dp),
                )
            }
        }

        // Action bar (48dp)
        Row(
            Modifier.fillMaxWidth().height(48.dp).padding(horizontal = 14.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(16.dp),
        ) {
            Icon(
                if (isLiked) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder,
                contentDescription = if (isLiked) "Unlike" else "Like",
                tint = if (isLiked) IGColors.Destructive else LocalContentColorOrBlack(),
                modifier = Modifier.size(24.dp).clickable { isLiked = !isLiked },
            )
            Icon(Icons.Outlined.ChatBubbleOutline, contentDescription = "Comment", modifier = Modifier.size(24.dp))
            Icon(Icons.Outlined.Send, contentDescription = "Share", modifier = Modifier.size(24.dp))
            Spacer(Modifier.weight(1f))
            Icon(Icons.Outlined.BookmarkBorder, contentDescription = "Save", modifier = Modifier.size(24.dp))
        }

        // Likes + caption + timestamp
        Text("$likes likes", style = IGText.Username.copy(fontSize = 13.sp), modifier = Modifier.padding(horizontal = 14.dp))
        Text(
            buildAnnotatedString {
                withStyle(SpanStyle(fontWeight = FontWeight.SemiBold)) { append(username) }
                append("  "); append(caption)
            },
            style = IGText.Caption,
            maxLines = 2,
            overflow = TextOverflow.Ellipsis,
            modifier = Modifier.padding(horizontal = 14.dp, vertical = 4.dp),
        )
        Text(
            timestamp.uppercase(),
            style = IGText.Timestamp,
            color = IGColors.TextSecondaryLight,
            modifier = Modifier.padding(horizontal = 14.dp, vertical = 4.dp),
        )
    }
}

@Composable
private fun LocalContentColorOrBlack(): Color =
    androidx.compose.material3.MaterialTheme.colorScheme.onBackground
```

### Comment Composer

```kotlin
import androidx.compose.foundation.text.BasicTextField

@Composable
fun CommentComposer(onPost: (String) -> Unit) {
    var text by remember { mutableStateOf("") }
    Row(
        Modifier.fillMaxWidth().height(48.dp).padding(horizontal = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Box(Modifier.size(24.dp).clip(CircleShape).background(IGColors.InputLight))
        BasicTextField(
            value = text,
            onValueChange = { text = it },
            textStyle = IGText.Caption,
            modifier = Modifier.weight(1f),
            decorationBox = { inner ->
                if (text.isEmpty()) Text("Add a comment...", style = IGText.Caption, color = IGColors.TextSecondaryLight)
                inner()
            },
        )
        if (text.isNotBlank()) {
            Text(
                "Post",
                style = IGText.Username,
                color = IGColors.ActionBlue,
                modifier = Modifier.clickable { onPost(text); text = "" },
            )
        }
    }
}
```

## 4. Story Ring Gradient + Create Gradient — the distinctive system

Instagram does no color extraction; its distinctive system is the **rationed brand gradient**: the conic sweep on unread story rings (in §3) and the same sweep on the Create (+) tab action. The gradient appears nowhere else in chrome — that scarcity is the brand.

```kotlin
import androidx.compose.ui.graphics.graphicsLayer
import androidx.compose.ui.graphics.BlendMode

/** Tints any monochrome icon with the brand gradient — used ONLY for the Create action. */
@Composable
fun GradientIcon(
    icon: androidx.compose.ui.graphics.vector.ImageVector,
    contentDescription: String?,
    modifier: Modifier = Modifier,
) {
    Box(
        modifier.graphicsLayer(compositingStrategy = androidx.compose.ui.graphics.CompositingStrategy.Offscreen),
    ) {
        Icon(icon, contentDescription = contentDescription, modifier = Modifier.matchParentSize())
        Box(
            Modifier
                .matchParentSize()
                .graphicsLayer { }
                .drawWithContent {
                    drawRect(brush = IGGradients.brandShort, blendMode = BlendMode.SrcIn)
                }
        )
    }
}
```

Reserve `GradientIcon` for the Create (+) tab and unread story rings only. The story ring stroke uses `Brush.sweepGradient` (a conic) per §3; the Create icon uses the 3-stop `brandShort`. If you find yourself tinting a Follow button or a tab label with the gradient — stop; the monochrome chrome is the brand.

## 5. Navigation (Bottom Tab Bar — icon-only, 5 tabs)

Use Material 3 `NavigationBar`. Instagram's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 92%-opaque canvas surface. **No text labels** (Instagram removed them in 2020). Active = filled icon; the Profile tab uses the user's avatar; the Create tab is gradient-tinted.

```kotlin
import androidx.compose.material.icons.filled.Home
import androidx.compose.material.icons.outlined.Home
import androidx.compose.material.icons.outlined.Search
import androidx.compose.material.icons.filled.Movie
import androidx.compose.material.icons.outlined.AddBox
import androidx.compose.material3.*

@Composable
fun IGBottomBar(selected: Int, onSelect: (Int) -> Unit, avatarUrl: String) {
    val haptics = LocalHapticFeedback.current
    val canvas = MaterialTheme.colorScheme.background
    NavigationBar(
        containerColor = canvas.copy(alpha = 0.92f),
        tonalElevation = 0.dp,
    ) {
        // Home
        NavigationBarItem(
            selected = selected == 0,
            onClick = { haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove); onSelect(0) },
            icon = {
                Icon(
                    if (selected == 0) Icons.Filled.Home else Icons.Outlined.Home,
                    contentDescription = "Home", modifier = Modifier.size(24.dp),
                )
            },
            colors = igBarColors(),
        )
        // Search
        NavigationBarItem(
            selected = selected == 1,
            onClick = { onSelect(1) },
            icon = { Icon(Icons.Outlined.Search, contentDescription = "Search", modifier = Modifier.size(24.dp)) },
            colors = igBarColors(),
        )
        // Reels
        NavigationBarItem(
            selected = selected == 2,
            onClick = { onSelect(2) },
            icon = { Icon(Icons.Filled.Movie, contentDescription = "Reels", modifier = Modifier.size(24.dp)) },
            colors = igBarColors(),
        )
        // Create (the gradient action)
        NavigationBarItem(
            selected = selected == 3,
            onClick = { onSelect(3) },
            icon = { GradientIcon(Icons.Outlined.AddBox, "Create", Modifier.size(24.dp)) },
            colors = igBarColors(),
        )
        // Profile (the user's avatar)
        NavigationBarItem(
            selected = selected == 4,
            onClick = { onSelect(4) },
            icon = {
                AsyncImage(
                    model = avatarUrl,
                    contentDescription = "Profile",
                    modifier = Modifier
                        .size(24.dp)
                        .clip(CircleShape)
                        .border(if (selected == 4) 2.dp else 0.dp, MaterialTheme.colorScheme.onBackground, CircleShape),
                    contentScale = ContentScale.Crop,
                )
            },
            colors = igBarColors(),
        )
    }
}

@Composable
private fun igBarColors() = NavigationBarItemDefaults.colors(
    selectedIconColor   = MaterialTheme.colorScheme.onBackground, // monochrome — no accent
    unselectedIconColor = MaterialTheme.colorScheme.onBackground,
    indicatorColor      = Color.Transparent, // Instagram has no Material pill
)
```

Long-press the Home tab to open the account switcher (medium haptic) — wrap the Home item in `Modifier.combinedClickable(onLongClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress); openSwitcher() })`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Double-tap to like | `AnimatedVisibility` heart `scaleIn(0f) + fadeIn()` → `scaleOut(1.4f) + fadeOut()` over ~600ms + `LongPress` (soft) haptic |
| Like-icon toggle | `animateFloatAsState` 0.9 → 1.15 → 1.0 with `spring(dampingRatio = 0.6f)` + color to `#ED4956` |
| Tab tap | `TextHandleMove` haptic; no transition |
| Home tab long-press | `LongPress` haptic → account-switcher sheet (`ModalBottomSheet`) |
| Story tap-and-hold | chrome alpha → 0.2f over 150ms via `animateFloatAsState` |
| Reels swipe | `VerticalPager` with default snap + rubber-band overscroll |

```kotlin
// Like-icon spring pop (action-bar heart)
@Composable
fun LikeIcon(isLiked: Boolean, onToggle: () -> Unit) {
    val scale = remember { Animatable(1f) }
    LaunchedEffect(isLiked) {
        if (isLiked) {
            scale.animateTo(1.15f, spring(dampingRatio = 0.6f))
            scale.animateTo(1f, spring(dampingRatio = 0.6f))
        }
    }
    Icon(
        if (isLiked) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder,
        contentDescription = if (isLiked) "Unlike" else "Like",
        tint = if (isLiked) IGColors.Destructive else MaterialTheme.colorScheme.onBackground,
        modifier = Modifier.size(24.dp).scale(scale.value).clickable { onToggle() },
    )
}
```

Keep motion subtle (0.15–0.3s, spring damping ≥0.6). Haptics: `LocalHapticFeedback` — `LongPress` ≈ iOS `.soft` (double-tap like, tab long-press), `TextHandleMove` ≈ `.selection` (tab tap). For a softer like cue use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+).

## 7. Icons

The SF Symbols map to Material Icons (`androidx.compose.material:material-icons-extended`). Use Material Icons first; ship custom vector drawables only where Material lacks the exact glyph (e.g., the Reels chevron-play).

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home | `house` / `house.fill` | `Icons.Outlined.Home` / `Icons.Filled.Home` |
| Explore | `magnifyingglass` | `Icons.Outlined.Search` |
| Reels | `play.rectangle` | `Icons.Filled.Movie` (custom chevron-play vector preferred) |
| Create | `plus.app` / `plus.app.fill` | `Icons.Outlined.AddBox` (gradient-tinted) |
| Heart | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Icons.Filled.Favorite` |
| Comment | `message` / `message.fill` | `Icons.Outlined.ChatBubbleOutline` |
| Share / DM | `paperplane` / `paperplane.fill` | `Icons.Outlined.Send` |
| Save | `bookmark` / `bookmark.fill` | `Icons.Outlined.BookmarkBorder` / `Icons.Filled.Bookmark` |
| More | `ellipsis` | `Icons.Filled.MoreVert` (or `MoreHoriz`) |
| Back | `chevron.backward` | `Icons.Filled.ChevronLeft` |
| Activity (top nav) | `heart` | `Icons.Filled.FavoriteBorder` |
| DMs (top nav) | `paperplane` | `Icons.Outlined.Send` |

The "Instagram" logotype is not an icon — it is text rendered with the registered script font (`IGLogotype`), used only at the top of the Home feed.

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `VerticalPager`, gradient brushes, modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. Media bleeds to the screen edges (corner radius 0 — sacred); chrome insets via `Scaffold`. In dark mode the true-black canvas wants light-content system bars; in light mode dark-content. The tab bar's translucent surface should clear the gesture nav with `Scaffold` insets.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on usernames, captions, comments, DM bubbles. Pin layout-sensitive text (the 28.sp logotype, 24.dp tab icons, 11.sp timestamp) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(LocalDensity.current.density, fontScale = 1f))`. Nav titles cap at 20.sp to stay within the 44.dp nav bar.
- **TalkBack**: every action icon needs a `contentDescription` ("Like post by username", "Comment", "Share", "Save"). The double-tap-to-like image needs a `Modifier.semantics { onClick(label = "Like") { … } }` custom action since TalkBack users cannot double-tap-gesture the photo. Group the caption username + text as one element.
- **Touch targets**: Material guidance is 48.dp minimum. The 24.dp action glyphs need 48.dp hit areas — wrap each in `Modifier.size(48.dp)` with the icon centered, even though the glyph stays 24.dp. Story rings (66.dp) and grid tiles are generous.
- **Contrast**: pure black/white chrome is maximal contrast. `#8E8E8E` secondary on white passes WCAG AA at 14sp+; validate the 11sp timestamp and bump toward `#737373` if targeting strict compliance. `#0095F6` action blue on white passes AA for 14sp SemiBold.
- **Reduce Motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, skip the double-tap heart burst (snap the like state) and disable the like-icon spring pop.
- **Material You**: do **not** enable `dynamicColorScheme()` — Instagram's brand requires strictly monochrome chrome and the fixed rationed gradient regardless of wallpaper. Dark mode is true `#000000` for OLED, not Material's default gray-black.
