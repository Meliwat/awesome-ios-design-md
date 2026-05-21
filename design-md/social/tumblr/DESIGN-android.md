# Tumblr (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Tumblr's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Tumblr's deep-navy dashboard, the blue/green/pink accent trio, the heart-burst, the indented reblog chain, the tag bar) while making everything idiomatic Android — `NavigationBar` with a docked center FAB via `Scaffold`, `Canvas`/`Animatable` particles instead of UIKit, `sp`/`dp` instead of `pt`. Tumblr also ships on Android, so this maps cleanly.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` (with `coil-gif` for animated GIFs).

## 1. Color Tokens

```kotlin
// ui/theme/TumblrColors.kt
import androidx.compose.ui.graphics.Color

object TumblrColors {
    // Canvas & Surfaces — deep navy, the default
    val Canvas   = Color(0xFF001935)
    val Surface1 = Color(0xFF001020)
    val Surface2 = Color(0xFF0B2A45)
    val Surface3 = Color(0xFF13395B)
    val Divider  = Color(0xFF36465D)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF8A9AAE)
    val TextTertiary  = Color(0xFF5C6B80)

    // Brand trio
    val Blue        = Color(0xFF00B8FF)
    val BluePressed = Color(0xFF0090CC)
    val Green       = Color(0xFF00CF35)
    val PinkRed     = Color(0xFFFF4930)

    // Semantic
    val Warning = Color(0xFFFFB300)
}
```

Wire it into a Material 3 `darkColorScheme`. Tumblr's iOS default and brand-defining experience is the deep navy; a light theme exists but is opt-in. Provide the dark scheme by default.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val TumblrScheme = darkColorScheme(
    primary        = TumblrColors.Blue,
    onPrimary      = TumblrColors.Canvas,        // dark navy text on bright blue
    secondary      = TumblrColors.Green,
    background      = TumblrColors.Canvas,
    onBackground    = TumblrColors.TextPrimary,
    surface         = TumblrColors.Surface2,
    onSurface       = TumblrColors.TextPrimary,
    surfaceVariant  = TumblrColors.Surface3,
    outline         = TumblrColors.Divider,
    error           = TumblrColors.PinkRed,
)

@Composable
fun TumblrTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = TumblrScheme, typography = TumblrTypography, content = content)
```

## 2. Typography

Tumblr uses the system font; the web client renders Inter/Helvetica. Reference `FontFamily.Default` (Roboto) — or drop Inter in `res/font/` for parity. Use a serif (`FontFamily.Serif`) for quote-type posts.

```kotlin
// ui/theme/TumblrType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

private val Sys = FontFamily.Default

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, 16sp body at 1.5)
object TumblrText {
    val TitleLarge  = TextStyle(Sys, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.4).sp)
    val Quote       = TextStyle(FontFamily.Serif, fontWeight = FontWeight.Bold, fontSize = 26.sp, lineHeight = 34.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Sys, fontWeight = FontWeight.Bold,   fontSize = 20.sp, lineHeight = 25.sp, letterSpacing = (-0.2).sp)
    val BlogName    = TextStyle(Sys, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 20.sp)
    val Body        = TextStyle(Sys, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 24.sp)   // 1.5
    val Reblog      = TextStyle(Sys, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 22.sp)
    val BodySettings= TextStyle(Sys, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 21.sp)
    val Tag         = TextStyle(Sys, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 18.sp)
    val Notes       = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 17.sp)
    val Button      = TextStyle(Sys, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 20.sp)
    val Tab         = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp)
    val ReblogSrc   = TextStyle(Sys, fontWeight = FontWeight.Bold,   fontSize = 14.sp, lineHeight = 18.sp)
}

val TumblrTypography = Typography(
    headlineLarge = TumblrText.TitleLarge,
    headlineSmall = TumblrText.Section,
    titleMedium   = TumblrText.BlogName,
    bodyMedium    = TumblrText.Body,
    labelSmall    = TumblrText.Tab,
)
```

## 3. Signature Components

### Dashboard Post Card (the core unit)

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

@Composable
fun PostCard(
    blogName: String,
    body: String?,
    tags: List<String>,
    notes: Int,
    media: (@Composable () -> Unit)? = null,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 8.dp)
            .padding(bottom = 8.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(TumblrColors.Surface2),
    ) {
        Row(
            Modifier.padding(12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(10.dp),
        ) {
            Box(Modifier.size(36.dp).clip(RoundedCornerShape(6.dp)).background(TumblrColors.Surface3))
            Text(blogName, style = TumblrText.BlogName, color = TumblrColors.TextPrimary)
            Spacer(Modifier.weight(1f))
            FollowButton()
            Icon(Icons.Filled.MoreHoriz, null, tint = TumblrColors.TextSecondary, modifier = Modifier.size(22.dp))
        }

        media?.invoke()   // full-bleed inside the card, no letterboxing

        if (!body.isNullOrEmpty()) {
            Text(body, style = TumblrText.Body, color = TumblrColors.TextPrimary,
                modifier = Modifier.padding(12.dp))
        }

        TagBar(tags)

        Row(
            Modifier.fillMaxWidth().padding(horizontal = 12.dp).padding(bottom = 12.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text("${"%,d".format(notes)} notes", style = TumblrText.Notes, color = TumblrColors.TextSecondary)
            Spacer(Modifier.weight(1f))
            Icon(Icons.Outlined.ChatBubbleOutline, "Reply", tint = TumblrColors.TextSecondary,
                modifier = Modifier.size(22.dp).padding(end = 0.dp))
            Spacer(Modifier.width(16.dp))
            ReblogButton()
            Spacer(Modifier.width(16.dp))
            LikeButton(notes)
            Spacer(Modifier.width(16.dp))
            Icon(Icons.Filled.Share, "Share", tint = TumblrColors.TextSecondary, modifier = Modifier.size(22.dp))
        }
    }
}
```

### Like Button with Heart-Burst (the signature)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import kotlin.math.cos
import kotlin.math.sin

@Composable
fun LikeButton(notes: Int, modifier: Modifier = Modifier) {
    var liked by remember { mutableStateOf(false) }
    val t = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()
    val particles = 8

    Box(modifier.size(44.dp), contentAlignment = Alignment.Center) {
        // Confetti hearts
        Canvas(Modifier.matchParentSize()) {
            if (t.value > 0f && t.value < 1f) {
                val cx = size.width / 2; val cy = size.height / 2
                repeat(particles) { i ->
                    val a = i.toFloat() / particles * 2f * Math.PI.toFloat()
                    val r = 26.dp.toPx() * t.value
                    val px = cx + cos(a) * r
                    val py = cy + sin(a) * r - 8.dp.toPx() * t.value
                    val s = (1f - 0.7f * t.value) * 6.dp.toPx()
                    val heart = Path().apply {
                        moveTo(px, py + s * 0.3f)
                        cubicTo(px, py - s * 0.3f, px - s, py - s * 0.3f, px - s, py + s * 0.2f)
                        cubicTo(px - s, py + s * 0.6f, px, py + s, px, py + s * 1.1f)
                        cubicTo(px, py + s, px + s, py + s * 0.6f, px + s, py + s * 0.2f)
                        cubicTo(px + s, py - s * 0.3f, px, py - s * 0.3f, px, py + s * 0.3f)
                    }
                    drawPath(heart, color = TumblrColors.PinkRed, alpha = 1f - t.value)
                }
            }
        }
        IconButton(onClick = {
            liked = !liked
            if (liked) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                scope.launch { t.snapTo(0f); t.animateTo(1f, tween(600)) }
            }
        }) {
            Icon(
                if (liked) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
                contentDescription = if (liked) "Liked" else "Like",
                tint = if (liked) TumblrColors.PinkRed else TumblrColors.TextSecondary,
                modifier = Modifier.size(22.dp),
            )
        }
    }
}
```

### Reblog Button

```kotlin
import androidx.compose.animation.core.spring
import androidx.compose.ui.draw.scale

@Composable
fun ReblogButton(modifier: Modifier = Modifier) {
    var reblogged by remember { mutableStateOf(false) }
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()

    IconButton(
        onClick = {
            reblogged = !reblogged
            haptics.performHapticFeedback(HapticFeedbackType.LongPress)
            scope.launch { scale.animateTo(1.15f, spring()); scale.animateTo(1f, spring()) }
        },
        modifier = modifier.size(44.dp),
    ) {
        Icon(
            Icons.Filled.Repeat,
            contentDescription = if (reblogged) "Reblogged" else "Reblog",
            tint = if (reblogged) TumblrColors.Green else TumblrColors.TextSecondary,
            modifier = Modifier.size(22.dp).scale(scale.value),
        )
    }
}
```

### Reblog Chain (the signature structure)

```kotlin
data class ReblogSegment(val sourceBlog: String, val comment: String, val depth: Int) // 0 = original

@Composable
fun ReblogChain(segments: List<ReblogSegment>, maxDepth: Int, modifier: Modifier = Modifier) {
    Column(modifier.padding(12.dp), verticalArrangement = Arrangement.spacedBy(12.dp)) {
        segments.forEach { seg ->
            val indent = ((maxDepth - seg.depth) * 12).dp
            Row(Modifier.padding(start = indent)) {
                if (indent > 0.dp) {
                    Box(Modifier.width(2.dp).fillMaxHeight().background(TumblrColors.Divider))
                    Spacer(Modifier.width(12.dp))
                }
                Column(verticalArrangement = Arrangement.spacedBy(4.dp)) {
                    Text(seg.sourceBlog, style = TumblrText.ReblogSrc, color = TumblrColors.Blue)
                    Text(seg.comment, style = TumblrText.Reblog, color = TumblrColors.TextPrimary)
                }
            }
        }
    }
}
```

### Buttons (Follow / Primary / FAB)

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.shape.CircleShape

@Composable
fun FollowButton(modifier: Modifier = Modifier) {
    var following by remember { mutableStateOf(false) }
    if (following) {
        Button(
            onClick = { following = false }, modifier = modifier,
            shape = RoundedCornerShape(6.dp),
            colors = ButtonDefaults.buttonColors(containerColor = TumblrColors.Green, contentColor = TumblrColors.Canvas),
            contentPadding = PaddingValues(horizontal = 18.dp, vertical = 8.dp),
        ) { Text("Following", style = TumblrText.Button) }
    } else {
        OutlinedButton(
            onClick = { following = true }, modifier = modifier,
            shape = RoundedCornerShape(6.dp),
            border = BorderStroke(1.5.dp, TumblrColors.Green),
            colors = ButtonDefaults.outlinedButtonColors(contentColor = TumblrColors.Green),
            contentPadding = PaddingValues(horizontal = 18.dp, vertical = 8.dp),
        ) { Text("Follow", style = TumblrText.Button) }
    }
}

@Composable
fun TmblrPrimaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Button(
        onClick = onClick, modifier = modifier,
        shape = RoundedCornerShape(6.dp),
        colors = ButtonDefaults.buttonColors(containerColor = TumblrColors.Blue, contentColor = TumblrColors.Canvas),
        contentPadding = PaddingValues(horizontal = 24.dp, vertical = 12.dp),
    ) { Text(text, style = TumblrText.Button) }
}

@Composable
fun NewPostFab(onClick: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    FloatingActionButton(
        onClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onClick() },
        containerColor = TumblrColors.Blue,
        contentColor = TumblrColors.Canvas,
        shape = CircleShape,
        modifier = Modifier.size(52.dp),
    ) { Icon(Icons.Filled.Add, contentDescription = "New post", modifier = Modifier.size(24.dp)) }
}
```

### Tag Bar

```kotlin
import androidx.compose.foundation.layout.FlowRow

@Composable
fun TagBar(tags: List<String>, modifier: Modifier = Modifier) {
    FlowRow(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 12.dp)
            .padding(bottom = 8.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        tags.forEach { tag ->
            Text("#$tag", style = TumblrText.Tag, color = TumblrColors.TextSecondary)
        }
    }
}
```

## 4. Distinctive System — Heart-Burst & Reblog Chain in Compose

The two souls of Tumblr are coded above. `LikeButton` uses a single `Animatable` `t` driving a `Canvas` that draws 8 heart `Path`s on radial vectors, fading and shrinking over 600ms — for double-tap-on-media, overlay the same `Canvas`/`t` centered on the image and reuse the animate call. `ReblogChain` renders progressively-indented attributed segments with a 2.dp `#36465D` left rule, blue source names, newest comment unindented at the bottom; keep the per-level indent at 12.dp.

## 5. Bottom Navigation (Tab Bar with Center FAB)

Material 3 has no built-in center-docked FAB in `NavigationBar`. Compose it via `Scaffold` — a `NavigationBar` with a gap, plus a `FloatingActionButton` placed `FabPosition.Center` so the blue New-post button rises out of the navy bar.

```kotlin
@Composable
fun TumblrScaffold(selected: Int, onSelect: (Int) -> Unit, onNewPost: () -> Unit,
                   content: @Composable (PaddingValues) -> Unit) {
    Scaffold(
        containerColor = TumblrColors.Canvas,
        floatingActionButton = { NewPostFab(onNewPost) },
        floatingActionButtonPosition = FabPosition.Center,
        bottomBar = {
            NavigationBar(containerColor = TumblrColors.Surface1, tonalElevation = 0.dp) {
                val items = listOf(
                    "Home"   to Icons.Filled.Home,
                    "Search" to Icons.Filled.Search,
                    null     to null,                          // gap for the docked FAB
                    "Activity" to Icons.Filled.Notifications,
                    "Account"  to Icons.Filled.AccountBox,
                )
                items.forEachIndexed { i, pair ->
                    if (pair.first == null) {
                        Spacer(Modifier.weight(1f))
                    } else {
                        NavigationBarItem(
                            selected = selected == i,
                            onClick = { onSelect(i) },
                            icon = { Icon(pair.second!!, contentDescription = pair.first, modifier = Modifier.size(26.dp)) },
                            label = { Text(pair.first!!, style = TumblrText.Tab) },
                            colors = NavigationBarItemDefaults.colors(
                                selectedIconColor   = TumblrColors.Blue,
                                selectedTextColor   = TumblrColors.Blue,
                                unselectedIconColor = TumblrColors.TextSecondary,
                                unselectedTextColor = TumblrColors.TextSecondary,
                                indicatorColor      = TumblrColors.Blue.copy(alpha = 0.12f),
                            ),
                        )
                    }
                }
            }
        },
        content = content,
    )
}
```

A 0.5.dp `HorizontalDivider(color = TumblrColors.Divider)` above the bar gives the hairline; the `#001020` bar sits on the navy canvas.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Heart-burst | one `Animatable` `t` 0 → 1 over `tween(600)` drives a `Canvas` of 8 heart paths on radial vectors, fading + shrinking; `HapticFeedbackType.TextHandleMove` |
| Reblog | `Animatable` 1 → 1.15 → 1 `spring()`, tint → Green, `HapticFeedbackType.LongPress` |
| Double-tap media | overlay the same burst `Canvas` centered on the image, reuse the animate call |
| FAB press | FAB ripple + soft haptic; post-type picker via `ModalBottomSheet` rising ~300ms |
| Tag press | brief tint flash to `Blue` then navigate to the tag route |

```kotlin
// Heart-burst (see LikeButton): t.snapTo(0f); t.animateTo(1f, tween(600))
// Reblog (see ReblogButton): scale.animateTo(1.15f, spring()); scale.animateTo(1f, spring())
```

Haptics: prefer `LocalHapticFeedback`. For the light like-tap use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+); for reblog use `CONFIRM`.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. For the Tumblr "t" wordmark, ship a vector drawable and load via `ImageVector.vectorResource(R.drawable.tumblr_t)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Reply | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Reblog | `arrow.2.squarepath` | `Icons.Filled.Repeat` |
| Like | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| New post (center FAB) | `plus` | `Icons.Filled.Add` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Activity (tab) | `bell.fill` | `Icons.Filled.Notifications` |
| Account (tab) | `person.crop.square` | `Icons.Filled.AccountBox` |
| Messaging | `paperplane.fill` | `Icons.AutoMirrored.Filled.Send` |
| Mature label | `exclamationmark.triangle.fill` | `Icons.Filled.Warning` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas` particles, `coil-gif`, modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. `FlowRow` is in `androidx.compose.foundation.layout` (stable in recent BOMs).
- **Edge-to-edge**: call `enableEdgeToEdge()`; the deep-navy canvas wants light-content system bars via `WindowCompat`. Use `Scaffold` insets so the docked FAB and bar clear the gesture nav.
- **Dark default**: provide the navy `darkColorScheme` by default; a light theme exists but should be a user-opt-in setting — the brand experience is the midnight dashboard.
- **GIFs**: add `coil-gif` and use `AsyncImage` with an `ImageLoader` that has `GifDecoder`/`ImageDecoderDecoder` — Tumblr is GIF-heavy; render media full-bleed inside the clipped card (no letterboxing).
- **Font scaling**: `sp` honors the user's scale — keep it generous on post body and quote posts (long-form). Pin only tab labels and the reblog-source minimum.
- **TalkBack**: mark the burst `Canvas` `Modifier.clearAndSetSemantics {}` (decorative); expose like `stateDescription` ("Liked"); announce reblog-chain segments as "<source> reblogged: <comment>"; keep the bright-blue primary button's dark navy text for contrast.
- **Touch targets**: Material guidance is 48.dp minimum. Post action icons are 22.dp glyphs inside 44.dp `IconButton`s — ensure each tag has ≥32.dp effective touch height if tappable.
- **Contrast**: `#8A9AAE` on `#001935` passes WCAG AA at 13sp+; the bright accent trio on navy is high-contrast. Validate 10sp tab labels and bump if targeting strict compliance.
- **Dynamic color**: do **not** enable Material You — Tumblr's identity is the fixed deep-navy canvas and the blue/green/pink accent trio regardless of wallpaper.
