# Threads (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Threads' visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the true-black canvas, the structural thread line, 36dp avatars, the Instagram-coral heart, the calm four-icon action row) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a translucent `Surface` instead of `.regularMaterial` blur, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars and post media. No color extraction — Threads has effectively no accent system, so `androidx.palette` is not required.

## 1. Color Tokens

```kotlin
// ui/theme/ThreadsColors.kt
import androidx.compose.ui.graphics.Color

object ThreadsColors {
    // Canvas & Surfaces (Dark / Default)
    val Canvas    = Color(0xFF000000)
    val Surface1  = Color(0xFF101010)
    val Surface2  = Color(0xFF181818)
    val Divider   = Color(0xFF222222)
    val ThreadLine = Color(0xFF333333) // the signature vertical rule

    // Canvas & Surfaces (Light)
    val LightCanvas    = Color(0xFFFFFFFF)
    val LightSurface1  = Color(0xFFFAFAFA)
    val LightSurface2  = Color(0xFFF5F5F5)
    val LightDivider   = Color(0xFFDBDBDB)
    val LightThreadLine = Color(0xFFD9D9D9)

    // Text
    val TextPrimaryDark   = Color(0xFFF5F5F5) // off-white, warm
    val TextPrimaryLight  = Color(0xFF000000)
    val TextSecondary     = Color(0xFF777777) // shared across modes
    val TextTertiaryDark  = Color(0xFF4D4D4D)
    val TextTertiaryLight = Color(0xFF999999)

    // Brand / Action
    val LinkBlue   = Color(0xFF2D7FF9)
    val LinkPressed = Color(0xFF1A5FD9)
    val LikeCoral  = Color(0xFFFE2C55) // Instagram-coral — NOT red
    val ErrorRed   = Color(0xFFED4956)
    val SuccessGreen = Color(0xFF58C322)
    val IGVerified = Color(0xFF0095F6)
}
```

Threads opens dark (true black) but supports light. Supply a Material 3 `darkColorScheme` as the default and a paper-white `lightColorScheme`. There is no tinted accent — `primary` maps to the primary text color so default components stay monochrome.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val ThreadsDark = darkColorScheme(
    primary        = ThreadsColors.TextPrimaryDark, // stark, not a tinted accent
    onPrimary      = ThreadsColors.Canvas,
    background     = ThreadsColors.Canvas,           // pure black
    onBackground   = ThreadsColors.TextPrimaryDark,
    surface        = ThreadsColors.Surface1,
    onSurface      = ThreadsColors.TextPrimaryDark,
    surfaceVariant = ThreadsColors.Surface2,
    outline        = ThreadsColors.Divider,
    error          = ThreadsColors.ErrorRed,
)

private val ThreadsLight = lightColorScheme(
    primary        = ThreadsColors.TextPrimaryLight,
    onPrimary      = ThreadsColors.LightCanvas,
    background     = ThreadsColors.LightCanvas,
    onBackground   = ThreadsColors.TextPrimaryLight,
    surface        = ThreadsColors.LightSurface1,
    onSurface      = ThreadsColors.TextPrimaryLight,
    surfaceVariant = ThreadsColors.LightSurface2,
    outline        = ThreadsColors.LightDivider,
    error          = ThreadsColors.ErrorRed,
)

@Composable
fun ThreadsTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (darkTheme) ThreadsDark else ThreadsLight,
    typography  = ThreadsTypography,
    content     = content,
)
```

## 2. Typography

Instagram Sans is proprietary to Meta (2022) — narrow, tall x-height, warm. Drop the TTFs in `res/font/` (lowercase, snake_case). Fall back to the system font (Roboto) — its tall x-height is the closest free substitute on Android. Threads ships **no** Display/Text optical split: one family, all sizes.

```kotlin
// ui/theme/ThreadsType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val InstagramSans = FontFamily(
    Font(R.font.instagramsans_regular,  FontWeight.Normal),   // 400
    Font(R.font.instagramsans_semibold, FontWeight.SemiBold), // 600
    Font(R.font.instagramsans_bold,     FontWeight.Bold),     // 700
)
// No 500 — Instagram Sans on Threads ships only 400 / 600 / 700.

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, weights preserved)
object ThreadsText {
    val ScreenTitle   = TextStyle(InstagramSans, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val DisplayName   = TextStyle(InstagramSans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val PostBody      = TextStyle(InstagramSans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp) // 1.4 line height
    val QuotedBody    = TextStyle(InstagramSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 19.sp)
    val Handle        = TextStyle(InstagramSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val ActionCount   = TextStyle(InstagramSans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 16.sp)
    val FollowerCount = TextStyle(InstagramSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val ProfileBio    = TextStyle(InstagramSans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val Button        = TextStyle(InstagramSans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 15.sp)
    val SecondaryBtn  = TextStyle(InstagramSans, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val ComposePH     = TextStyle(InstagramSans, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 22.sp) // larger than feed body
    val DMBody        = TextStyle(InstagramSans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp)
    val FilterChip    = TextStyle(InstagramSans, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val ThreadsTypography = Typography(
    titleMedium = ThreadsText.ScreenTitle,
    bodyMedium  = ThreadsText.PostBody,
    labelLarge  = ThreadsText.Button,
)
```

## 3. Signature Components

### Thread Post Row (with the thread line)

The structural signature: a 1dp vertical rule from 4dp below the avatar to 4dp above the action row, present on any post that roots a thread.

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.outlined.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun ThreadPostRow(
    displayName: String,
    handle: String,
    timestamp: String,
    avatarUrl: String,
    body: String,
    hasReplies: Boolean, // render the thread line if true
    likeCount: Int = 0,
    commentCount: Int = 0,
    modifier: Modifier = Modifier,
) {
    var liked by remember { mutableStateOf(false) }
    var reposted by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current

    Column(modifier.fillMaxWidth().background(ThreadsColors.Canvas)) {
        Row(
            Modifier.padding(start = 16.dp, end = 16.dp, top = 16.dp, bottom = 12.dp),
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            // Avatar + thread-line column
            Column(horizontalAlignment = Alignment.CenterHorizontally) {
                AsyncImage(
                    model = avatarUrl,
                    contentDescription = "$displayName avatar",
                    modifier = Modifier.size(36.dp).clip(CircleShape), // 36dp — larger than X's 32
                    contentScale = ContentScale.Crop,
                )
                if (hasReplies) {
                    Box(
                        Modifier
                            .padding(top = 4.dp)
                            .width(1.dp)
                            .weight(1f)
                            .background(ThreadsColors.ThreadLine),
                    )
                }
            }

            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(6.dp)) {
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    Text(displayName, style = ThreadsText.DisplayName, color = ThreadsColors.TextPrimaryDark, maxLines = 1)
                    Text("@$handle", style = ThreadsText.Handle, color = ThreadsColors.TextSecondary, maxLines = 1)
                    Text("·", style = ThreadsText.Handle, color = ThreadsColors.TextSecondary)
                    Text(timestamp, style = ThreadsText.Handle, color = ThreadsColors.TextSecondary)
                    Spacer(Modifier.weight(1f))
                    Icon(Icons.Filled.MoreHoriz, "More", tint = ThreadsColors.TextSecondary, modifier = Modifier.size(18.dp))
                }

                Text(body, style = ThreadsText.PostBody, color = ThreadsColors.TextPrimaryDark)

                // FOUR icons only — heart, comment, repost, share (no bookmark, no views)
                Row(
                    Modifier.padding(top = 4.dp),
                    horizontalArrangement = Arrangement.spacedBy(20.dp),
                ) {
                    ActionBtn(
                        icon = if (liked) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
                        count = likeCount + if (liked) 1 else 0,
                        tint = if (liked) ThreadsColors.LikeCoral else ThreadsColors.TextSecondary,
                    ) {
                        liked = !liked
                        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // soft impact
                    }
                    ActionBtn(Icons.Outlined.ChatBubbleOutline, commentCount, ThreadsColors.TextSecondary) {}
                    // repost: outline → filled cross-fade, NO color change (stays gray)
                    ActionBtn(
                        icon = if (reposted) Icons.Filled.Repeat else Icons.Outlined.Repeat,
                        count = null,
                        tint = ThreadsColors.TextSecondary,
                    ) { reposted = !reposted }
                    ActionBtn(Icons.Outlined.Send, null, ThreadsColors.TextSecondary) {}
                }
            }
        }
        HorizontalDivider(color = ThreadsColors.Divider, thickness = 1.dp)
    }
}

@Composable
private fun ActionBtn(icon: ImageVector, count: Int?, tint: Color, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    Row(
        modifier = Modifier
            .clip(CircleShape)
            .clickable(interaction, indication = null, onClick = onClick)
            .defaultMinSize(minWidth = 44.dp, minHeight = 44.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(icon, null, tint = tint, modifier = Modifier.size(22.dp))
        if (count != null && count > 0) Text("$count", style = ThreadsText.ActionCount, color = tint)
    }
}
```

### Primary Post Pill (intentional color inversion)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.draw.alpha

@Composable
fun ThreadsPostPill(
    title: String,
    enabled: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    // dark mode: off-white fill, black text (intentional inversion); light mode flips
    Box(
        modifier = modifier
            .alpha(if (!enabled) 0.3f else if (pressed) 0.8f else 1f)
            .clip(RoundedCornerShape(50))
            .background(ThreadsColors.TextPrimaryDark)
            .clickable(interaction, indication = null, enabled = enabled, onClick = onClick)
            .padding(horizontal = 20.dp, vertical = 8.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = ThreadsText.Button, color = ThreadsColors.Canvas)
    }
}
```

### Follow Pill

```kotlin
import androidx.compose.foundation.border

@Composable
fun ThreadsFollowPill(
    isFollowing: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Box(
        modifier = modifier
            .widthIn(min = 92.dp)
            .clip(RoundedCornerShape(50))
            .then(
                if (isFollowing) Modifier.border(1.dp, ThreadsColors.TextSecondary, RoundedCornerShape(50))
                else Modifier.background(ThreadsColors.TextPrimaryDark)
            )
            .clickable(onClick = onClick)
            .padding(horizontal = 20.dp, vertical = 8.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(
            if (isFollowing) "Following" else "Follow",
            style = ThreadsText.SecondaryBtn,
            color = if (isFollowing) ThreadsColors.TextPrimaryDark else ThreadsColors.Canvas,
        )
    }
}
```

### Activity Filter Chip Row

```kotlin
import androidx.compose.foundation.horizontalScroll
import androidx.compose.foundation.rememberScrollState
import androidx.compose.animation.animateColorAsState

@Composable
fun ActivityFilterRow(
    selected: String,
    onSelect: (String) -> Unit,
    modifier: Modifier = Modifier,
) {
    val chips = listOf("All", "Follows", "Replies", "Mentions", "Quotes", "Reposts", "Verified")
    Row(
        modifier.horizontalScroll(rememberScrollState()).padding(horizontal = 16.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        chips.forEach { label ->
            val on = selected == label
            val bg by animateColorAsState(if (on) ThreadsColors.TextPrimaryDark else Color.Transparent, label = "chipBg")
            Box(
                Modifier
                    .heightIn(min = 36.dp)
                    .clip(RoundedCornerShape(50))
                    .background(bg)
                    .then(if (on) Modifier else Modifier.border(1.dp, ThreadsColors.ThreadLine, RoundedCornerShape(50)))
                    .clickable { onSelect(label) }
                    .padding(horizontal = 16.dp, vertical = 8.dp),
                contentAlignment = Alignment.Center,
            ) {
                Text(label, style = ThreadsText.FilterChip, color = if (on) ThreadsColors.Canvas else ThreadsColors.TextPrimaryDark)
            }
        }
    }
}
```

## 4. Multi-Post Thread Composer (the signature interaction)

Threads' defining flow is the full-screen composer where the **thread line flows between chained posts** and a "+ Add to thread" dot extends it. iOS uses a `NavigationStack` modal; Compose uses a full-screen `Scaffold` with the thread line drawn as a 1dp rule that runs through the avatar gutter and stretches to the next draft.

```kotlin
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.foundation.verticalScroll
import androidx.compose.material.icons.filled.Add

@Composable
fun ThreadsComposer(
    onCancel: () -> Unit,
    onPost: (List<String>) -> Unit,
) {
    val drafts = remember { mutableStateListOf("") }

    Scaffold(
        containerColor = ThreadsColors.Canvas,
        topBar = {
            Row(
                Modifier
                    .fillMaxWidth()
                    .background(ThreadsColors.Canvas)
                    .padding(horizontal = 16.dp, vertical = 12.dp),
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Text(
                    "Cancel",
                    style = ThreadsText.Handle,
                    color = ThreadsColors.TextPrimaryDark,
                    modifier = Modifier.clickable(onClick = onCancel),
                )
                Spacer(Modifier.weight(1f))
                ThreadsPostPill(
                    title = "Post",
                    enabled = drafts.any { it.isNotBlank() },
                    onClick = { onPost(drafts.filter { it.isNotBlank() }) },
                )
            }
        },
    ) { inner ->
        Column(
            Modifier
                .padding(inner)
                .fillMaxSize()
                .verticalScroll(rememberScrollState()),
        ) {
            drafts.forEachIndexed { i, _ ->
                Row(
                    Modifier.padding(horizontal = 16.dp, vertical = 12.dp),
                    horizontalArrangement = Arrangement.spacedBy(12.dp),
                ) {
                    Column(horizontalAlignment = Alignment.CenterHorizontally) {
                        Box(Modifier.size(36.dp).clip(CircleShape).background(ThreadsColors.Surface2))
                        // thread line flows down to the next draft
                        if (i < drafts.lastIndex || drafts[i].isNotEmpty()) {
                            Box(
                                Modifier
                                    .padding(top = 4.dp)
                                    .width(1.dp)
                                    .weight(1f)
                                    .heightIn(min = 24.dp)
                                    .background(ThreadsColors.ThreadLine),
                            )
                        }
                    }
                    Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(8.dp)) {
                        Text("you", style = ThreadsText.DisplayName, color = ThreadsColors.TextPrimaryDark)
                        BasicTextField(
                            value = drafts[i],
                            onValueChange = { drafts[i] = it },
                            textStyle = ThreadsText.ComposePH.copy(color = ThreadsColors.TextPrimaryDark),
                            cursorBrush = androidx.compose.ui.graphics.SolidColor(ThreadsColors.TextPrimaryDark),
                            decorationBox = { f ->
                                if (drafts[i].isEmpty()) Text("Start a thread...", style = ThreadsText.ComposePH, color = ThreadsColors.TextSecondary)
                                f()
                            },
                        )
                    }
                }
            }

            // "+ Add to thread" dot — extends the thread line to a new draft
            Row(
                Modifier.padding(top = 8.dp, start = 24.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(12.dp),
            ) {
                Box(
                    Modifier
                        .size(20.dp)
                        .clip(CircleShape)
                        .border(1.dp, ThreadsColors.ThreadLine, CircleShape),
                    contentAlignment = Alignment.Center,
                ) {
                    Icon(Icons.Filled.Add, null, tint = ThreadsColors.TextSecondary, modifier = Modifier.size(10.dp))
                }
                Text(
                    "Add to thread",
                    style = ThreadsText.Handle,
                    color = ThreadsColors.TextSecondary,
                    modifier = Modifier.clickable { drafts.add("") },
                )
            }
        }
    }
}
```

## 5. Navigation (Tab Bar)

Threads' iOS tab bar is **icon-only (no labels)**, 5 tabs, with the active icon as a filled variant and the inactive as outlined — over a `.regularMaterial` blur at 90% opacity. Android has no first-class live blur — use a translucent `NavigationBar` (`containerColor` at ~90% alpha). Active tint is the stark primary text color (no tinted accent); inactive is `#777777`. Hide labels and the Material pill indicator (Threads has neither).

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun ThreadsBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = ThreadsColors.Canvas.copy(alpha = 0.9f), // translucent — no live blur on Android
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            Icons.Filled.Home,
            Icons.Filled.Search,
            Icons.Filled.AddBox,       // compose is a tab, not a FAB
            Icons.Filled.FavoriteBorder,
            Icons.Filled.Person,
        )
        items.forEachIndexed { i, icon ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = null, modifier = Modifier.size(26.dp)) },
                alwaysShowLabel = false, // icon-only — no labels
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = ThreadsColors.TextPrimaryDark, // stark, not tinted
                    unselectedIconColor = ThreadsColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // Threads has no Material pill
                ),
            )
        }
    }
}
```

The top nav centers the monochrome `@` logo (28dp, primary text color, never colored/animated) — ship it as a vector drawable and shift it clear of the camera cutout via `Modifier.windowInsetsPadding(WindowInsets.displayCutout)`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Like tap | heart `Animatable` 0.85 → 1.15 → 1.0 `spring`, ~300ms; outline → coral fill; `HapticFeedbackType.TextHandleMove` (soft); count ticks up |
| Comment tap | icon `0.9 → 1.05 → 1.0`, then slide-up compose-reply |
| Repost tap | outline → filled `Crossfade` over ~200ms, **no color change**, soft haptic |
| Share tap | open Android system share `Intent` (`ACTION_SEND`) |
| Compose open | full-screen slide-up ~320ms `spring`; thread line `Animatable` 0 → full height over ~400ms |
| Pull-to-refresh | the `@` logo pulses + subtly rotates as the user pulls |
| Profile tab strip | underline indicator slides horizontally ~250ms ease-out; content `Crossfade` |
| Tab switch | instant `Crossfade`, no slide direction |

```kotlin
// Like tap — 0.85 → 1.15 → 1.0 bounce, coral fill, soft haptic
@Composable
fun ThreadsLikeIcon(liked: Boolean, onToggle: () -> Unit) {
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(liked) {
        if (liked) {
            scale.snapTo(0.85f)
            scale.animateTo(1.15f, androidx.compose.animation.core.spring(dampingRatio = 0.5f, stiffness = 600f))
            scale.animateTo(1.0f, androidx.compose.animation.core.spring(dampingRatio = 0.6f))
        }
    }
    Icon(
        if (liked) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
        contentDescription = if (liked) "Unlike" else "Like",
        tint = if (liked) ThreadsColors.LikeCoral else ThreadsColors.TextSecondary,
        modifier = Modifier
            .size(22.dp)
            .androidx.compose.ui.draw.scale(scale.value)
            .clickable {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                onToggle()
            },
    )
}
```

Threads' motion is deliberately calm (250–400ms) to match the slower scroll — there is **no particle burst** on like (unlike X). Haptics: `LocalHapticFeedback` `TextHandleMove` approximates iOS `.impact(.light)`. For a softer cue use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` (API 30+).

## 7. Icons

Threads uses outlined/filled SF Symbol pairs plus the custom `@` logo glyph. The closest first-party set is `androidx.compose.material:material-icons-extended`; ship the `@` logo and the IG-verified seal as vector drawables (`ImageVector.vectorResource(R.drawable.…)`).

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Like (outline / filled) | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Comment | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Repost (outline / filled) | `arrow.2.squarepath` | `Icons.Outlined.Repeat` / `Icons.Filled.Repeat` |
| Share | `paperplane` | `Icons.Outlined.Send` |
| Overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Home tab | `house` / `house.fill` | `Icons.Outlined.Home` / `Icons.Filled.Home` |
| Search tab | `magnifyingglass` | `Icons.Filled.Search` |
| Compose tab | `plus.square` | `Icons.Filled.AddBox` |
| Activity tab | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Profile tab | `person.circle.fill` / avatar | `Icons.Filled.Person` or Coil avatar |
| Verified (IG-inherited) | `checkmark.seal.fill` `#0095F6` | `Icons.Filled.Verified` tinted `IGVerified` |
| Add-to-thread plus | `plus` in 20pt circle | `Icons.Filled.Add` in a 20dp bordered circle |
| `@` logo | custom Threads glyph | vector drawable (monochrome, never animated) |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; the thread-line `Box` rule and motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The true-black canvas wants light system-bar icons in dark mode (and dark icons in light). Shift the `@` logo clear of the camera cutout with `Modifier.windowInsetsPadding(WindowInsets.displayCutout)`; apply `Scaffold` insets so the icon-only tab bar clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale — scale post body, bio, DM body, and the 17sp compose placeholder (it should grow so "Start a thread..." stays legible). Pin the **13sp action counts** (layout-sensitive) and cap display name/handle/timestamp at a max scale to preserve the threaded layout.
- **TalkBack**: group the post row as one node — `Modifier.semantics(mergeDescendants = true)` — with the like/repost as separately focusable buttons. The **thread line is purely structural**: mark it `Modifier.clearAndSetSemantics {}` (or `semantics { hideFromAccessibility() }`) so TalkBack skips it.
- **Touch targets**: Material guidance is 48.dp; the 22dp action glyphs sit on a 44dp `defaultMinSize` target — bump to 48dp via `Modifier.minimumInteractiveComponentSize()` for strict compliance. The 26dp tab icons fill the full bar height.
- **Contrast**: off-white `#F5F5F5` on true black meets WCAG AAA; secondary `#777777` on black is borderline at 14sp — keep handles/timestamps at **14sp or larger only**, never smaller.
- **Reduce motion**: detect `Settings.Global.ANIMATOR_DURATION_SCALE == 0f` (or a stored pref) — skip the `@`-logo pull-to-refresh pulse and the thread-line grow-in, but still fire the like haptic and the coral color change.
- **Reduce transparency**: when the system "remove animations"/high-contrast preference is set, drop the translucent `containerColor` alpha on the top nav and tab bar in favor of a solid canvas.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Threads is intentionally monochrome (off-white/black text, no tinted accent except the inherited coral heart and link blue); a wallpaper-derived palette would break the brand's deliberate quiet.
