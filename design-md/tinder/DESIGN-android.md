# Tinder (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Tinder's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (the swipe-card stack, stamps, the 5-button action bar, the match screen), `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the 3:4 full-bleed swipe card, the pink→orange flame gradient, the five-verb action colors, the rotated LIKE/NOPE stamps) while making everything idiomatic Android — a `NavigationBar` instead of a UITabBar, `pointerInput`/`detectDragGestures` instead of `DragGesture`, `LocalHapticFeedback` instead of `.sensoryFeedback`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for remote profile photos. No color extraction, so `androidx.palette` is not required.

## 1. Color Tokens

```kotlin
// ui/theme/TinderColors.kt
import androidx.compose.ui.graphics.Color

object TinderColors {
    // Canvas (Light)
    val Canvas        = Color(0xFFFFFFFF)
    val SurfaceMuted  = Color(0xFFF5F5F5)
    val SurfaceTint   = Color(0xFFFAFAFA)
    val Divider       = Color(0xFFE5E5E5)

    // Text
    val TextPrimary   = Color(0xFF424242) // intentional: softer than pure black
    val TextSecondary = Color(0xFF737373)
    val TextTertiary  = Color(0xFF9E9E9E)

    // Brand
    val Pink          = Color(0xFFFD267A)
    val Orange        = Color(0xFFFF6036)

    // Action colors (the 5 verbs)
    val NopeRed       = Color(0xFFFF4458)
    val SuperLikeBlue = Color(0xFF5D8DF1)
    val BoostPurple   = Color(0xFFA952FF)
    val RewindGold    = Color(0xFFFFBD3B)
    val LikeStampGreen = Color(0xFF00D68F)

    // Semantic
    val VerifiedBlue  = Color(0xFF29B0FF)
    val MatchGlow     = Color(0xFF00D68F)

    // Dark
    val DarkCanvas    = Color(0xFF121212)
    val DarkSurface1  = Color(0xFF1D1D1D)
    val DarkSurface2  = Color(0xFF2A2A2A)
    val DarkDivider   = Color(0xFF3A3A3A)
    val DarkTextPrimary   = Color(0xFFFFFFFF)
    val DarkTextSecondary = Color(0xFFB8B8B8)
}

// The flame brand gradient — Match screen + premium CTAs ONLY
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.geometry.Offset

val TinderBrandGradient = Brush.linearGradient(
    colors = listOf(TinderColors.Pink, TinderColors.Orange),
    start = Offset(0f, 0f),
    end = Offset(Float.POSITIVE_INFINITY, 0f), // horizontal: leading → trailing
)
```

Tinder is a light-first app (white canvas, photos do the work). Wire a Material 3 `lightColorScheme` so ripples, dividers, and stock component defaults inherit the brand. Provide the dark scheme for night mode.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val TinderLight = lightColorScheme(
    primary        = TinderColors.Pink,
    onPrimary      = Color.White,
    background     = TinderColors.Canvas,
    onBackground   = TinderColors.TextPrimary,
    surface        = TinderColors.SurfaceMuted,
    onSurface      = TinderColors.TextPrimary,
    surfaceVariant = TinderColors.SurfaceTint,
    outline        = TinderColors.Divider,
    error          = TinderColors.NopeRed,
)

private val TinderDark = darkColorScheme(
    primary        = TinderColors.Pink,
    onPrimary      = Color.White,
    background     = TinderColors.DarkCanvas,
    onBackground   = TinderColors.DarkTextPrimary,
    surface        = TinderColors.DarkSurface1,
    onSurface      = TinderColors.DarkTextPrimary,
    surfaceVariant = TinderColors.DarkSurface2,
    outline        = TinderColors.DarkDivider,
    error          = TinderColors.NopeRed,
)

@Composable
fun TinderTheme(darkTheme: Boolean = isSystemInDarkTheme(), content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (darkTheme) TinderDark else TinderLight,
        typography = TinderTypography,
        content = content,
    )
```

## 2. Typography

Tinder Sans is proprietary (licensed from the brand team). Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto) — its humanist proportions are the closest free substitute; the match-hero italic display cut falls back to a bold italic.

```kotlin
// ui/theme/TinderType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val TinderSans = FontFamily(
    Font(R.font.tinder_sans_regular,  FontWeight.Normal),                    // 400
    Font(R.font.tinder_sans_medium,   FontWeight.Medium),                    // 500
    Font(R.font.tinder_sans_bold,     FontWeight.Bold),                      // 700
    Font(R.font.tinder_sans_black,    FontWeight.Black),                     // 800
    Font(R.font.tinder_sans_black_italic, FontWeight.Black, FontStyle.Italic), // 800 italic — match hero + stamps
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object TinderText {
    val MatchHero    = TextStyle(TinderSans, fontWeight = FontWeight.Black,  fontStyle = FontStyle.Italic, fontSize = 48.sp, lineHeight = 53.sp, letterSpacing = (-0.5).sp)
    val ScreenTitle  = TextStyle(TinderSans, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.3).sp)
    val ProfileName  = TextStyle(TinderSans, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.3).sp)
    val ProfileAge   = TextStyle(TinderSans, fontWeight = FontWeight.Normal, fontSize = 24.sp, lineHeight = 28.sp)
    val Section      = TextStyle(TinderSans, fontWeight = FontWeight.Bold,   fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Button       = TextStyle(TinderSans, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val ButtonSmall  = TextStyle(TinderSans, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 15.sp)
    val Body         = TextStyle(TinderSans, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 22.sp)
    val CardMeta     = TextStyle(TinderSans, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 18.sp)
    val Chat         = TextStyle(TinderSans, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 20.sp)
    val ChatTime     = TextStyle(TinderSans, fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 13.sp)
    val Stamp        = TextStyle(TinderSans, fontWeight = FontWeight.Black,  fontStyle = FontStyle.Italic, fontSize = 36.sp, lineHeight = 36.sp, letterSpacing = 2.sp)
    val SuperStamp   = TextStyle(TinderSans, fontWeight = FontWeight.Black,  fontStyle = FontStyle.Italic, fontSize = 32.sp, lineHeight = 32.sp, letterSpacing = 2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val TinderTypography = Typography(
    headlineLarge = TinderText.ScreenTitle,
    headlineSmall = TinderText.Section,
    titleMedium   = TinderText.ProfileName,
    bodyMedium    = TinderText.Body,
    labelLarge    = TinderText.Button,
)
```

## 3. Signature Components

### Swipe Card (drag + rotation + stamps)

The hero. Compose drags via `Modifier.pointerInput` + `detectDragGestures`; rotation is `(offsetX / 12)` clamped to ±15°, exactly mirroring the SwiftUI guide.

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.spring
import androidx.compose.animation.core.tween
import androidx.compose.foundation.gestures.detectDragGestures
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.foundation.layout.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.graphicsLayer
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.background
import androidx.compose.foundation.Image
import androidx.compose.material3.Text
import androidx.compose.ui.text.style.TextOverflow
import coil.compose.AsyncImage
import kotlin.math.abs
import kotlinx.coroutines.launch

enum class SwipeDirection { LEFT, RIGHT, UP }

@Composable
fun TinderSwipeCard(
    name: String,
    age: Int,
    distance: String,
    occupation: String,
    photoUrls: List<String>,
    modifier: Modifier = Modifier,
    onSwipe: (SwipeDirection) -> Unit = {},
) {
    val offsetX = remember { Animatable(0f) }
    val offsetY = remember { Animatable(0f) }
    var currentPhoto by remember { mutableIntStateOf(0) }
    val scope = rememberCoroutineScope()
    val haptics = LocalHapticFeedback.current
    val threshold = 150f

    val rotation = (offsetX.value / 12f).coerceIn(-15f, 15f)
    val likeOpacity  = (offsetX.value / 160f).coerceIn(0f, 1f)
    val nopeOpacity  = (-offsetX.value / 160f).coerceIn(0f, 1f)
    val superOpacity = (-offsetY.value / 160f).coerceIn(0f, 1f)

    Box(
        modifier = modifier
            .fillMaxWidth()
            .aspectRatio(3f / 4f) // load-bearing — never change
            .graphicsLayer {
                translationX = offsetX.value
                translationY = offsetY.value
                rotationZ = rotation
            }
            .clip(RoundedCornerShape(16.dp))
            .pointerInput(Unit) {
                detectDragGestures(
                    onDrag = { change, drag ->
                        change.consume()
                        scope.launch { offsetX.snapTo(offsetX.value + drag.x) }
                        scope.launch { offsetY.snapTo(offsetY.value + drag.y) }
                    },
                    onDragEnd = {
                        when {
                            offsetX.value > threshold -> commit(scope, offsetX, offsetY, 1600f, 0f) {
                                haptics.performHapticFeedback(HapticFeedbackType.LongPress); onSwipe(SwipeDirection.RIGHT)
                            }
                            offsetX.value < -threshold -> commit(scope, offsetX, offsetY, -1600f, 0f) {
                                haptics.performHapticFeedback(HapticFeedbackType.LongPress); onSwipe(SwipeDirection.LEFT)
                            }
                            offsetY.value < -threshold -> commit(scope, offsetX, offsetY, 0f, -2000f) {
                                haptics.performHapticFeedback(HapticFeedbackType.LongPress); onSwipe(SwipeDirection.UP)
                            }
                            else -> { // snap back, spring damping ~0.7
                                scope.launch { offsetX.animateTo(0f, spring(0.7f, 200f)) }
                                scope.launch { offsetY.animateTo(0f, spring(0.7f, 200f)) }
                            }
                        }
                    },
                )
            }
            .pointerInput(photoUrls.size) {
                detectTapGestures { tap ->
                    currentPhoto =
                        if (tap.x < size.width / 2) (currentPhoto - 1).coerceAtLeast(0)
                        else (currentPhoto + 1).coerceAtMost(photoUrls.lastIndex)
                }
            },
        contentAlignment = Alignment.BottomStart,
    ) {
        AsyncImage(
            model = photoUrls[currentPhoto],
            contentDescription = "$name, $age",
            modifier = Modifier.fillMaxSize(),
            contentScale = ContentScale.Crop,
        )
        // Bottom legibility gradient over the lower ~50%
        Box(
            Modifier
                .fillMaxSize()
                .background(
                    Brush.verticalGradient(
                        0.5f to Color.Transparent,
                        1f to Color.Black.copy(alpha = 0.7f),
                    )
                )
        )
        // Photo paginator — thin white segments at top
        Row(
            Modifier.fillMaxWidth().align(Alignment.TopCenter).padding(horizontal = 16.dp, vertical = 8.dp),
            horizontalArrangement = Arrangement.spacedBy(3.dp),
        ) {
            repeat(photoUrls.size) { i ->
                Box(
                    Modifier
                        .weight(1f)
                        .height(3.dp)
                        .clip(RoundedCornerShape(1.5.dp))
                        .background(if (i == currentPhoto) Color.White else Color.White.copy(alpha = 0.4f))
                )
            }
        }
        // Name + age + meta
        Column(Modifier.padding(20.dp)) {
            Row(verticalAlignment = Alignment.Bottom) {
                Text(name, style = TinderText.ProfileName, color = Color.White)
                Text(", $age", style = TinderText.ProfileAge, color = Color.White)
            }
            Text(
                "$distance · $occupation",
                style = TinderText.CardMeta,
                color = Color.White.copy(alpha = 0.9f),
                maxLines = 1, overflow = TextOverflow.Ellipsis,
            )
        }
        // Stamps — rotated, opacity tied to drag progress
        TinderStamp("LIKE", TinderColors.LikeStampGreen, -15f, Modifier.align(Alignment.TopStart).padding(32.dp).graphicsLayer { alpha = likeOpacity })
        TinderStamp("NOPE", TinderColors.NopeRed, 15f, Modifier.align(Alignment.TopEnd).padding(32.dp).graphicsLayer { alpha = nopeOpacity })
        TinderStamp("SUPER LIKE", TinderColors.SuperLikeBlue, 0f, Modifier.align(Alignment.TopCenter).padding(top = 64.dp).graphicsLayer { alpha = superOpacity }, isSuper = true)
    }
}

private fun commit(
    scope: kotlinx.coroutines.CoroutineScope,
    x: Animatable<Float, *>,
    y: Animatable<Float, *>,
    tx: Float, ty: Float,
    onDone: () -> Unit,
) {
    onDone()
    scope.launch { x.animateTo(tx, tween(400)) }
    scope.launch { y.animateTo(ty, tween(400)) }
}
```

### Swipe Stamp

```kotlin
import androidx.compose.foundation.border

@Composable
fun TinderStamp(
    text: String,
    color: Color,
    rotation: Float,
    modifier: Modifier = Modifier,
    isSuper: Boolean = false,
) {
    Text(
        text = text,
        style = if (isSuper) TinderText.SuperStamp else TinderText.Stamp,
        color = color,
        modifier = modifier
            .graphicsLayer { rotationZ = rotation }
            .border(4.dp, color, RoundedCornerShape(6.dp))
            .padding(horizontal = 12.dp, vertical = 6.dp),
    )
}
```

### Five Action Buttons Row

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.clickable
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.unit.Dp

@Composable
fun TinderActionBar(
    onRewind: () -> Unit,
    onNope: () -> Unit,
    onSuper: () -> Unit,
    onLike: () -> Unit,
    onBoost: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier = modifier,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        TinderActionButton(48.dp, TinderColors.RewindGold,     Icons.Filled.Replay,    "Rewind",     onRewind)
        TinderActionButton(56.dp, TinderColors.NopeRed,        Icons.Filled.Close,     "Pass",       onNope)
        TinderActionButton(40.dp, TinderColors.SuperLikeBlue,  Icons.Filled.Star,      "Super Like", onSuper)
        TinderActionButton(56.dp, TinderColors.LikeStampGreen, Icons.Filled.Favorite,  "Like",       onLike)
        TinderActionButton(48.dp, TinderColors.BoostPurple,    Icons.Filled.Bolt,      "Boost",      onBoost)
    }
}

@Composable
fun TinderActionButton(
    size: Dp,
    color: Color,
    icon: ImageVector,
    label: String,
    onClick: () -> Unit,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.92f else 1f, spring(0.5f, 600f), label = "actionScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = Modifier
            .size(size)
            .scale(scale)
            .clip(CircleShape)
            .background(TinderColors.Canvas)
            .border(2.dp, color, CircleShape)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                onClick()
            }
            .semantics { contentDescription = label },
        contentAlignment = Alignment.Center,
    ) {
        Icon(icon, contentDescription = null, tint = color, modifier = Modifier.size(size * 0.42f))
    }
}
```

### Primary Brand CTA (flame gradient)

```kotlin
@Composable
fun TinderBrandPillButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "ctaScale")

    Box(
        modifier = modifier
            .scale(scale)
            .fillMaxWidth()
            .heightIn(min = 52.dp)
            .clip(CircleShape)
            .background(TinderBrandGradient) // pink→orange, branded
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 32.dp, vertical = 14.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = TinderText.Button, color = Color.White)
    }
}
```

### Match Screen

```kotlin
@Composable
fun TinderMatchScreen(
    yourPhotoUrl: String,
    theirPhotoUrl: String,
    theirName: String,
    onSendMessage: () -> Unit,
    onKeepPlaying: () -> Unit,
) {
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(Unit) { haptics.performHapticFeedback(HapticFeedbackType.LongPress) } // ~success

    Box(
        Modifier.fillMaxSize().background(TinderBrandGradient), // full-screen flame gradient
        contentAlignment = Alignment.Center,
    ) {
        Column(
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(24.dp),
        ) {
            Text("It's a Match!", style = TinderText.MatchHero, color = Color.White)
            Text("You and $theirName liked each other", style = TinderText.Body, color = Color.White.copy(alpha = 0.8f))
            Row(horizontalArrangement = Arrangement.spacedBy(20.dp)) {
                MatchAvatar(yourPhotoUrl)
                MatchAvatar(theirPhotoUrl)
            }
            Column(
                Modifier.fillMaxWidth().padding(horizontal = 24.dp),
                verticalArrangement = Arrangement.spacedBy(12.dp),
            ) {
                // White pill with gradient text
                Box(
                    Modifier.fillMaxWidth().clip(CircleShape).background(Color.White)
                        .clickable(onClick = onSendMessage).padding(vertical = 14.dp),
                    contentAlignment = Alignment.Center,
                ) { Text("Send Message", style = TinderText.Button, color = TinderColors.Pink) }
                // Outline pill
                Box(
                    Modifier.fillMaxWidth().clip(CircleShape).border(2.dp, Color.White, CircleShape)
                        .clickable(onClick = onKeepPlaying).padding(vertical = 14.dp),
                    contentAlignment = Alignment.Center,
                ) { Text("Keep Playing", style = TinderText.Button, color = Color.White) }
            }
        }
    }
}

@Composable
private fun MatchAvatar(url: String) {
    AsyncImage(
        model = url,
        contentDescription = null,
        modifier = Modifier.size(120.dp).clip(CircleShape).border(3.dp, Color.White, CircleShape),
        contentScale = ContentScale.Crop,
    )
}
```

### Chat Bubble (gradient sender / muted self)

```kotlin
enum class ChatSender { ME, THEM }

@Composable
fun TinderChatBubble(text: String, sender: ChatSender, modifier: Modifier = Modifier) {
    val isThem = sender == ChatSender.THEM
    Row(
        modifier = modifier.fillMaxWidth().padding(horizontal = 16.dp),
        horizontalArrangement = if (isThem) Arrangement.Start else Arrangement.End,
    ) {
        Box(
            Modifier
                .widthIn(max = 280.dp)
                .clip(RoundedCornerShape(20.dp))
                .then(
                    if (isThem) Modifier.background(TinderBrandGradient) // matched sender = flame gradient
                    else Modifier.background(TinderColors.SurfaceMuted)
                )
                .padding(horizontal = 14.dp, vertical = 10.dp)
        ) {
            Text(text, style = TinderText.Chat, color = if (isThem) Color.White else TinderColors.TextPrimary)
        }
    }
}
```

## 4. Swipe Card Stack (the distinctive interaction)

Tinder has no color extraction; its defining dynamic system is the **peeking card stack**. The next card sits behind at `scale 0.95` and rises to `1.0` over 250ms as the top card commits off-screen — mirroring DESIGN.md §5 ("subsequent cards peek behind, slight scale offset 0.95").

```kotlin
@Composable
fun <T> SwipeCardStack(
    items: List<T>,
    modifier: Modifier = Modifier,
    onSwiped: (T, SwipeDirection) -> Unit,
    card: @Composable (T) -> Unit,
) {
    var topIndex by remember { mutableIntStateOf(0) }

    Box(modifier, contentAlignment = Alignment.Center) {
        // Render up to two cards: the peeking next card, then the active top card
        val nextIndex = topIndex + 1
        if (nextIndex <= items.lastIndex) {
            val rise by animateFloatAsState(0.95f, spring(0.8f, 300f), label = "nextRise")
            Box(Modifier.graphicsLayer { scaleX = rise; scaleY = rise }) {
                card(items[nextIndex])
            }
        }
        if (topIndex <= items.lastIndex) {
            key(topIndex) {
                Box {
                    // TinderSwipeCard is the active card; on commit, advance the stack
                    card(items[topIndex])
                }
            }
        }
    }
    // Caller wires TinderSwipeCard's onSwipe to: onSwiped(items[topIndex], dir); topIndex++
}
```

Hoist `topIndex` and the per-card swipe callback into a `ViewModel`; the action bar's Like/Nope/Super buttons drive the same `commit(...)` path so users who cannot drag still have full parity.

## 5. Bottom Navigation (icon-only)

Tinder's iOS tab bar is icon-only (Flame | Star | Chat | Profile), no labels, taller than typical, with the **active tint = flame gradient**. Material 3's `NavigationBar` always reserves label space, so we render a custom row to keep it spartan. Android has no live blur — use a 96%-opaque canvas surface with a hairline top border.

```kotlin
import androidx.compose.foundation.layout.WindowInsets
import androidx.compose.foundation.layout.navigationBars
import androidx.compose.foundation.layout.windowInsetsPadding
import androidx.compose.ui.draw.drawBehind

@Composable
fun TinderBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    val items = listOf(
        "Discover" to Icons.Filled.Whatshot,
        "Top Picks" to Icons.Filled.Star,
        "Matches"   to Icons.Filled.ChatBubble,
        "Profile"   to Icons.Filled.Person,
    )
    Row(
        Modifier
            .fillMaxWidth()
            .background(TinderColors.Canvas.copy(alpha = 0.96f))
            .drawBehind {
                drawLine(TinderColors.Divider, androidx.compose.ui.geometry.Offset(0f, 0f),
                    androidx.compose.ui.geometry.Offset(size.width, 0f), 0.5.dp.toPx())
            }
            .windowInsetsPadding(WindowInsets.navigationBars)
            .height(64.dp),
        horizontalArrangement = Arrangement.SpaceEvenly,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        items.forEachIndexed { i, (label, icon) ->
            val active = selected == i
            Box(
                Modifier.size(48.dp).clip(CircleShape).clickable { onSelect(i) }
                    .semantics { contentDescription = label },
                contentAlignment = Alignment.Center,
            ) {
                if (active) {
                    Icon(icon, null, modifier = Modifier.size(28.dp)
                        .graphicsLayer(compositingStrategy = androidx.compose.ui.graphics.CompositingStrategy.Offscreen)
                        .drawWithContent {
                            drawContent()
                            drawRect(TinderBrandGradient, blendMode = androidx.compose.ui.graphics.BlendMode.SrcAtop)
                        }, tint = Color.Unspecified)
                } else {
                    Icon(icon, null, tint = TinderColors.TextTertiary, modifier = Modifier.size(28.dp))
                }
            }
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Swipe drag | `graphicsLayer { translationX/Y; rotationZ = (dx/12).coerceIn(-15f,15f) }` driven by `detectDragGestures` + `Animatable.snapTo` |
| Snap back | `Animatable.animateTo(0f, spring(dampingRatio = 0.7f, stiffness = 200f))` over ~400ms |
| Swipe commit | `animateTo(±1600f, tween(400))` off-screen + `HapticFeedbackType.LongPress` |
| Next card rise | `animateFloatAsState(1f, spring(dampingRatio = 0.8f))` 0.95 → 1.0 over ~250ms |
| Action button press | `animateFloatAsState(0.92f, spring(dampingRatio = 0.5f, stiffness = 600f))` + glyph fill flash |
| Match reveal | full-screen gradient + confetti via `Animatable` keyframes (see below); success-style haptic |
| New-match glow | `rememberInfiniteTransition` ring scale 1.0 → 1.2, alpha 1 → 0, 1.5s `RepeatMode.Restart` |

```kotlin
// Pulsing green "new match" ring around an avatar (DESIGN.md §4 New Match Glow)
@Composable
fun NewMatchGlow(content: @Composable () -> Unit) {
    val t = rememberInfiniteTransition(label = "glow")
    val scale by t.animateFloat(1f, 1.2f, infiniteRepeatable(tween(1500), RepeatMode.Restart), label = "glowScale")
    val alpha by t.animateFloat(1f, 0f, infiniteRepeatable(tween(1500), RepeatMode.Restart), label = "glowAlpha")
    Box(contentAlignment = Alignment.Center) {
        Box(
            Modifier.matchParentSize()
                .graphicsLayer { scaleX = scale; scaleY = scale; this.alpha = alpha }
                .border(2.dp, TinderColors.MatchGlow, CircleShape)
        )
        content()
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control on commit/match use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(12, ...)` to approximate the iOS `.medium` impact; chain two effects for the Boost activation's heavy+medium pattern.

## 7. Icons

Tinder ships custom glyphs (the flame logo especially); the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Tinder's flame logo and the rounded action glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Flame (Discover tab) | `flame.fill` | `Icons.Filled.Whatshot` (or custom flame drawable) |
| Star (Top Picks / Super Like) | `star.fill` | `Icons.Filled.Star` |
| Chat (tab) | `bubble.left.fill` | `Icons.Filled.ChatBubble` |
| Profile (tab) | `person.fill` | `Icons.Filled.Person` |
| Rewind (action) | `arrow.counterclockwise` | `Icons.Filled.Replay` |
| Nope (action) | `xmark` | `Icons.Filled.Close` |
| Like (action) | `heart.fill` | `Icons.Filled.Favorite` |
| Boost (action) | `bolt.fill` | `Icons.Filled.Bolt` |
| Verified | `checkmark.seal.fill` | `Icons.Filled.Verified` |
| Info (card ⓘ) | `info.circle.fill` | `Icons.Filled.Info` |
| Send | `arrow.up` | `Icons.AutoMirrored.Filled.Send` |
| GIF picker | `photo.on.rectangle.angled` | `Icons.Filled.Gif` |
| Settings | `gearshape.fill` | `Icons.Filled.Settings` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; gesture + spring motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The swipe screen extends under the status bar with a transparent top; apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the card's 16.dp margins and the action bar clear the gesture nav and the home indicator.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on the profile name/age, bio, and chat. Pin layout-sensitive text: the rotated `LIKE`/`NOPE`/`SUPER LIKE` stamps and the photo paginator are glance-/layout-critical — derive their size from `dp` or wrap in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. Tab area has no labels, so nothing to pin there.
- **TalkBack**: the swipe card needs `Modifier.semantics { contentDescription = "$name, $age, $distance, $occupation. Double tap for profile." }` and custom accessibility actions for Like/Pass/Super so non-drag users have parity (the action-bar buttons already expose discrete targets with clear `contentDescription`s — "Rewind", "Pass", "Super Like", "Like", "Boost").
- **Touch targets**: Material guidance is 48.dp minimum. The 56.dp Like/Nope buttons clear it; the 40.dp Super Like and the 32.dp card info button must carry a 48.dp hit area via padding even though the glyph is small. Card drag area is the full 3:4 surface.
- **Contrast**: white name/age sits on a `rgba(0,0,0,0.7)` legibility gradient — always keep the gradient under text over photos. The flame-gradient CTAs use pure white text; validate the lighter `#FF6036` end against white at 16sp Bold (it passes WCAG AA at that weight). `#737373` meta on white passes AA at 14sp+.
- **Reduce data / motion**: respect a reduced-motion preference by skipping the confetti burst and the card rotation during drag (keep translation only), and replacing the match-screen entrance with a plain fade.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` / `dynamicDarkColorScheme()` — Tinder is a strong-brand app: the flame pink→orange gradient and the fixed five-verb action colors must not shift with the user's wallpaper.
