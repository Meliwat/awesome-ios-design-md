# Bluesky (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Bluesky's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Bluesky's triple-theme, the bright blue accent, the like-pop, the pinned custom-feed selector, the butterfly) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `FloatingActionButton` for compose, `sp`/`dp` instead of `pt`. Bluesky also ships on Android, so this maps cleanly.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/BlueskyColors.kt
import androidx.compose.ui.graphics.Color

object BlueskyColors {
    // Light
    val Canvas    = Color(0xFFFFFFFF)
    val Surface1  = Color(0xFFF1F3F5)
    val Surface2  = Color(0xFFE2E8F0)
    val Divider   = Color(0xFFE2E8F0)
    val Text      = Color(0xFF0B0F14)
    val TextSec   = Color(0xFF697787)
    val TextTer   = Color(0xFF8B98A5)

    // Dim
    val DimCanvas   = Color(0xFF1E2936)
    val DimSurface1 = Color(0xFF27313E)
    val DimDivider  = Color(0xFF2E4052)
    val DimTextSec  = Color(0xFF9CA6B5)

    // Dark
    val DarkCanvas   = Color(0xFF0B0F14)
    val DarkSurface1 = Color(0xFF161E27)
    val DarkDivider  = Color(0xFF1E2936)
    val DarkTextSec  = Color(0xFF8B98A5)

    // Brand
    val Blue        = Color(0xFF1185FE)
    val BluePressed = Color(0xFF0F6FD6)
    val Like        = Color(0xFFEC4899)
    val Repost      = Color(0xFF2DBE85)

    // Semantic
    val Error   = Color(0xFFE5484D)
    val Warning = Color(0xFFF5A623)
}
```

Bluesky ships **three** themes. Provide a sealed selection and map each to a Material 3 scheme; never collapse Dim into Dark — they are distinct comfort levels.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

enum class BskyTheme { Light, Dim, Dark }

private val LightScheme = lightColorScheme(
    primary = BlueskyColors.Blue, onPrimary = Color.White,
    background = BlueskyColors.Canvas, onBackground = BlueskyColors.Text,
    surface = BlueskyColors.Canvas, onSurface = BlueskyColors.Text,
    surfaceVariant = BlueskyColors.Surface1, outline = BlueskyColors.Divider,
    error = BlueskyColors.Error,
)
private val DimScheme = darkColorScheme(
    primary = BlueskyColors.Blue, onPrimary = Color.White,
    background = BlueskyColors.DimCanvas, onBackground = Color(0xFFF1F3F5),
    surface = BlueskyColors.DimSurface1, onSurface = Color(0xFFF1F3F5),
    surfaceVariant = BlueskyColors.DimSurface1, outline = BlueskyColors.DimDivider,
    error = BlueskyColors.Error,
)
private val DarkScheme = darkColorScheme(
    primary = BlueskyColors.Blue, onPrimary = Color.White,
    background = BlueskyColors.DarkCanvas, onBackground = Color(0xFFF1F3F5),
    surface = BlueskyColors.DarkSurface1, onSurface = Color(0xFFF1F3F5),
    surfaceVariant = BlueskyColors.DarkSurface1, outline = BlueskyColors.DarkDivider,
    error = BlueskyColors.Error,
)

@Composable
fun BlueskyTheme(theme: BskyTheme, content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = when (theme) {
            BskyTheme.Light -> LightScheme
            BskyTheme.Dim   -> DimScheme
            BskyTheme.Dark  -> DarkScheme
        },
        typography = BlueskyTypography,
        content = content,
    )
```

## 2. Typography

Bluesky uses the system font at reading sizes; the web client renders **Inter**. For parity drop Inter TTFs in `res/font/` (lowercase, snake_case); otherwise reference `FontFamily.Default` (Roboto).

```kotlin
// ui/theme/BlueskyType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
    Font(R.font.inter_bold,     FontWeight.Bold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, 15sp body at 1.4)
object BlueskyText {
    val TitleLarge   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.4).sp)
    val Section      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 21.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val DisplayName  = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val Body         = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)   // 1.4
    val Handle       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val FeedTab      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 18.sp, letterSpacing = (-0.1).sp)
    val Count        = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 16.sp)
    val Button       = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 18.sp, letterSpacing = (-0.1).sp)
    val ButtonSec    = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 17.sp)
    val Tab          = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp)
    val ReplyCtx     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
}

val BlueskyTypography = Typography(
    headlineLarge = BlueskyText.TitleLarge,
    headlineSmall = BlueskyText.Section,
    titleMedium   = BlueskyText.DisplayName,
    bodyMedium    = BlueskyText.Body,
    labelSmall    = BlueskyText.Tab,
)
```

## 3. Signature Components

### Skeet Card (the core unit)

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.text.AnnotatedString
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun SkeetCard(
    displayName: String,
    handle: String,            // "pfrazee.com"
    timestamp: String,
    body: String,
    avatarUrl: String,
    replyContext: String? = null,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .fillMaxWidth()
            .background(BlueskyColors.Canvas)
            .padding(horizontal = 16.dp, vertical = 12.dp),
    ) {
        replyContext?.let {
            Text("↩ $it", style = BlueskyText.ReplyCtx, color = BlueskyColors.TextSec,
                modifier = Modifier.padding(bottom = 6.dp))
        }
        Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
            AsyncImage(model = avatarUrl, contentDescription = null,
                modifier = Modifier.size(42.dp).clip(CircleShape))
            Column(Modifier.weight(1f)) {
                Text(
                    buildAnnotatedString {
                        append(displayName); append("  ")
                    },
                    style = BlueskyText.DisplayName, color = BlueskyColors.Text, maxLines = 1,
                )
                Text("@$handle · $timestamp", style = BlueskyText.Handle,
                    color = BlueskyColors.TextSec, maxLines = 1)
            }
            Icon(Icons.Filled.MoreHoriz, null, tint = BlueskyColors.TextSec, modifier = Modifier.size(18.dp))
        }
        Text(body, style = BlueskyText.Body, color = BlueskyColors.Text,
            modifier = Modifier.padding(top = 8.dp))

        Row(
            Modifier.fillMaxWidth().padding(top = 8.dp, end = 16.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            ActionItem(Icons.Outlined.ChatBubbleOutline, 8, BlueskyColors.TextSec)
            RepostButton(count = 14)
            LikeButton(count = 142)
            ActionItem(Icons.Filled.Share, 0, BlueskyColors.TextSec)
        }
    }
    HorizontalDivider(thickness = 1.dp, color = BlueskyColors.Divider)
}

@Composable
private fun ActionItem(icon: androidx.compose.ui.graphics.vector.ImageVector, count: Int, tint: Color) {
    Row(verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
        modifier = Modifier.defaultMinSize(minWidth = 44.dp, minHeight = 44.dp)) {
        Icon(icon, null, tint = tint, modifier = Modifier.size(18.dp))
        if (count > 0) Text("$count", style = BlueskyText.Count, color = tint)
    }
}
```

### Like Button (the pop-scale signature)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.spring
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.foundation.interaction.MutableInteractionSource

@Composable
fun LikeButton(count: Int, modifier: Modifier = Modifier) {
    var liked by remember { mutableStateOf(false) }
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()

    Row(
        modifier
            .defaultMinSize(minWidth = 44.dp, minHeight = 44.dp)
            .clickable(remember { MutableInteractionSource() }, indication = null) {
                liked = !liked
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ≈ light impact
                scope.launch {
                    scale.animateTo(1.25f, spring(dampingRatio = 0.45f, stiffness = 600f))
                    scale.animateTo(1f, spring(dampingRatio = 0.7f))
                }
            },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(
            if (liked) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
            contentDescription = if (liked) "Liked" else "Like",
            tint = if (liked) BlueskyColors.Like else BlueskyColors.TextSec,
            modifier = Modifier.size(18.dp).scale(scale.value),
        )
        Text("${if (liked) count + 1 else count}", style = BlueskyText.Count,
            color = if (liked) BlueskyColors.Like else BlueskyColors.TextSec)
    }
}
```

### Repost Button

```kotlin
@Composable
fun RepostButton(count: Int, modifier: Modifier = Modifier) {
    var reposted by remember { mutableStateOf(false) }
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()

    Row(
        modifier
            .defaultMinSize(minWidth = 44.dp, minHeight = 44.dp)
            .clickable(remember { MutableInteractionSource() }, indication = null) {
                reposted = !reposted
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                scope.launch { scale.animateTo(1.1f, spring()); scale.animateTo(1f, spring()) }
            },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(Icons.Filled.Repeat,
            contentDescription = if (reposted) "Reposted" else "Repost",
            tint = if (reposted) BlueskyColors.Repost else BlueskyColors.TextSec,
            modifier = Modifier.size(18.dp).scale(scale.value))
        Text("${if (reposted) count + 1 else count}", style = BlueskyText.Count,
            color = if (reposted) BlueskyColors.Repost else BlueskyColors.TextSec)
    }
}
```

### Primary Button & Compose FAB

```kotlin
@Composable
fun BskyPrimaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Button(
        onClick = onClick,
        modifier = modifier,
        shape = CircleShape,                       // full pill
        colors = ButtonDefaults.buttonColors(
            containerColor = BlueskyColors.Blue, contentColor = Color.White,
        ),
        contentPadding = PaddingValues(horizontal = 20.dp, vertical = 10.dp),
    ) { Text(text, style = BlueskyText.Button) }
}

@Composable
fun ComposeFab(onClick: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    FloatingActionButton(
        onClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onClick() },
        containerColor = BlueskyColors.Blue,
        contentColor = Color.White,
        shape = CircleShape,
        modifier = Modifier.size(56.dp),
    ) { Icon(Icons.Filled.Edit, contentDescription = "Compose", modifier = Modifier.size(24.dp)) }
}
```

## 4. Distinctive System — Pinned Custom-Feed Selector

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.shape.RoundedCornerShape

@Composable
fun FeedSelector(feeds: List<String>, selected: String, onSelect: (String) -> Unit, modifier: Modifier = Modifier) {
    LazyRow(
        modifier.fillMaxWidth(),
        contentPadding = PaddingValues(horizontal = 16.dp),
        horizontalArrangement = Arrangement.spacedBy(20.dp),
    ) {
        items(feeds) { feed ->
            val active = feed == selected
            Column(
                Modifier.clickable { onSelect(feed) }.padding(top = 12.dp),
                horizontalAlignment = Alignment.CenterHorizontally,
            ) {
                Text(feed, style = BlueskyText.FeedTab,
                    color = if (active) BlueskyColors.Blue else BlueskyColors.TextSec)
                Spacer(Modifier.height(8.dp))
                Box(
                    Modifier
                        .fillMaxWidth()
                        .height(3.dp)
                        .clip(RoundedCornerShape(2.dp))
                        .background(if (active) BlueskyColors.Blue else Color.Transparent),
                )
            }
        }
    }
}
```

Pair the switch with a 250ms `Crossfade` on the feed list and a small horizontal nudge (`animateDpAsState`).

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Bluesky's iOS tab bar is `.regularMaterial`; Android has no first-class live blur, so use a 94%-opaque canvas. Active tint is **Bluesky Blue**.

```kotlin
@Composable
fun BlueskyBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = BlueskyColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"          to Icons.Filled.Home,
            "Search"        to Icons.Filled.Search,
            "Notifications" to Icons.Filled.Notifications,
            "Chat"          to Icons.AutoMirrored.Filled.Chat,
            "Profile"       to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(26.dp)) },
                label = { Text(label, style = BlueskyText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = BlueskyColors.Blue,
                    selectedTextColor   = BlueskyColors.Blue,
                    unselectedIconColor = BlueskyColors.TextSec,
                    unselectedTextColor = BlueskyColors.TextSec,
                    indicatorColor      = BlueskyColors.Blue.copy(alpha = 0.12f),
                ),
            )
        }
    }
}
```

The compose **FAB** floats above this bar — place it in the `Scaffold` `floatingActionButton` slot with `FabPosition.End`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Like pop | `Animatable` 1 → 1.25 (`spring(dampingRatio = 0.45f, stiffness = 600f)`) → 1, fill `Like`, `HapticFeedbackType.TextHandleMove` |
| Repost bounce | `Animatable` 1 → 1.1 → 1 `spring()`, tint `Repost`, `HapticFeedbackType.LongPress` |
| Feed switch | `Crossfade(tween(250))` + small `animateDpAsState` horizontal nudge |
| Compose FAB | scale 0.94 on press (FAB ripple), present compose as full screen / bottom sheet |
| Theme switch | recompose with the new scheme; `animateColorAsState` smooths surface transitions ~200ms |

```kotlin
// Like pop (see LikeButton): scale.animateTo(1.25f, spring(dampingRatio = 0.45f, stiffness = 600f))
// Repost (see RepostButton): scale.animateTo(1.1f, spring())
```

Haptics: prefer `LocalHapticFeedback`. For a crisper like tap use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+).

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. For the butterfly logomark, ship a vector drawable and load via `ImageVector.vectorResource(R.drawable.bsky_butterfly)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Reply | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Repost | `arrow.2.squarepath` | `Icons.Filled.Repeat` |
| Like | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Reply context | `arrow.turn.up.left` | `Icons.AutoMirrored.Filled.Reply` |
| Compose (FAB) | `square.and.pencil` | `Icons.Filled.Edit` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Notifications (tab) | `bell.fill` | `Icons.Filled.Notifications` |
| Chat (tab) | `message.fill` | `Icons.AutoMirrored.Filled.Chat` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Feed settings | `slider.horizontal.3` | `Icons.Filled.Tune` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; flip system-bar icon contrast per theme (dark icons on Light, light on Dim/Dark). Use `Scaffold` insets so the FAB clears the gesture nav.
- **Triple theme**: expose Light / Dim / Dark via a persisted setting; never collapse Dim into Dark — Bluesky treats them as distinct comfort levels. Smooth surface transitions with `animateColorAsState` ~200ms.
- **Font scaling**: `sp` honors the user's scale — keep it generous on skeet body and names (conversational app). Pin only tab labels.
- **TalkBack**: merge a skeet's name/handle/time/body with `Modifier.semantics(mergeDescendants = true)`; expose like/repost `stateDescription` ("Liked"/"Reposted"); make the feed selector a tab list and announce the active feed.
- **Touch targets**: Material guidance is 48.dp minimum. Action icons are 18.dp glyphs — give each a 44–48.dp hit area via `defaultMinSize` (shown above).
- **Contrast**: `#697787` on `#FFFFFF`, `#8B98A5` on `#0B0F14`, and `#9CA6B5` on `#1E2936` all pass WCAG AA at 14sp+. Validate 10sp tab labels and bump if targeting strict compliance.
- **Dynamic color**: do **not** enable Material You — Bluesky's brand requires the fixed bright-blue accent and its three defined canvases regardless of wallpaper.
