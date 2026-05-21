# Reddit (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Reddit's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the upvote/downvote column, the warm-orange brand vs the complementary vote pair, flat cards, 10.dp comment-indent rules) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Animatable` bounce instead of SwiftUI keyframes, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for post media + subreddit avatars.

## 1. Color Tokens

```kotlin
// ui/theme/RedditColors.kt
import androidx.compose.ui.graphics.Color

object RedditColors {
    // Brand
    val BrandRed        = Color(0xFFFF4500)
    val AlertRed        = Color(0xFFFF585B)
    val BrandRedPressed = Color(0xFFCC3700)

    // Vote semantic pair — complementary, warm vs cool, never both lit at once
    val Upvote           = Color(0xFFFF8717)
    val UpvoteDark       = Color(0xFFFFAA66)
    val Downvote         = Color(0xFF7193FF)
    val DownvoteDark     = Color(0xFF9494FF)
    val VoteInactive     = Color(0xFF878A8C)
    val VoteInactiveDark = Color(0xFF818384)

    // Canvas & Surface (Light)
    val CanvasLight        = Color(0xFFF6F7F8)
    val CanvasClassicLight = Color(0xFFDAE0E6)
    val CardLight          = Color(0xFFFFFFFF)
    val Surface2Light      = Color(0xFFF2F3F5)
    val DividerLight       = Color(0xFFEDEFF1)

    // Canvas & Surface (Dark)
    val CanvasDark   = Color(0xFF1A1A1B)
    val CardDark     = Color(0xFF272729)
    val Surface2Dark = Color(0xFF343536)
    val DividerDark  = Color(0xFF343536)

    // Text
    val TextPrimaryLight   = Color(0xFF1A1A1B)
    val TextSecondaryLight = Color(0xFF7C7C7C)
    val TextTertiaryLight  = Color(0xFFAFAFAF)
    val TextPrimaryDark    = Color(0xFFD7DADC)
    val TextSecondaryDark  = Color(0xFF818384)
    val TextLink           = Color(0xFF0079D3)

    // Semantic
    val SuccessGreen   = Color(0xFF46D160)
    val WarningYellow  = Color(0xFFFFB000)
    val NSFWYellow     = Color(0xFFF3B200)
    val Gold           = Color(0xFFFFB000)
    val PremiumGold    = Color(0xFFFFD635)
    val SubredditDefault = Color(0xFF0079D3)
}
```

Wire a Material 3 `lightColorScheme` / `darkColorScheme`. Reddit's brand red is *not* the upvote color — keep `primary` as the brand red for CTAs and let the vote pair live as explicit tokens (never mapped to `primary`/`secondary`, so a wrong default can never tint a vote arrow).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val RedditLight = lightColorScheme(
    primary        = RedditColors.BrandRed,
    onPrimary      = Color.White,
    background     = RedditColors.CanvasLight,
    onBackground   = RedditColors.TextPrimaryLight,
    surface        = RedditColors.CardLight,
    onSurface      = RedditColors.TextPrimaryLight,
    surfaceVariant = RedditColors.Surface2Light,
    outline        = RedditColors.DividerLight,
    error          = RedditColors.AlertRed,
)

private val RedditDark = darkColorScheme(
    primary        = RedditColors.BrandRed,
    onPrimary      = Color.White,
    background     = RedditColors.CanvasDark,
    onBackground   = RedditColors.TextPrimaryDark,
    surface        = RedditColors.CardDark,
    onSurface      = RedditColors.TextPrimaryDark,
    surfaceVariant = RedditColors.Surface2Dark,
    outline        = RedditColors.DividerDark,
    error          = RedditColors.AlertRed,
)

@Composable
fun RedditTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) RedditDark else RedditLight,
    typography  = RedditTypography,
    content     = content,
)
```

## 2. Typography

Reddit Sans (2023+) is proprietary. Drop the TTFs in `res/font/` and build a `FontFamily`; fall back to the system font (Roboto) — a humanist sans tuned for long reading sessions is the closest free substitute. Use Reddit Mono / `FontFamily.Monospace` for code.

```kotlin
// ui/theme/RedditType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val RedditSans = FontFamily(
    Font(R.font.reddit_sans_regular,  FontWeight.Normal),   // 400 — body workhorse
    Font(R.font.reddit_sans_medium,   FontWeight.Medium),   // 500 — usernames
    Font(R.font.reddit_sans_semibold, FontWeight.SemiBold), // 600 — post titles
    Font(R.font.reddit_sans_bold,     FontWeight.Bold),     // 700 — headlines / karma
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object RedditText {
    val LargeTitle    = TextStyle(RedditSans, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.3).sp)
    val PostTitle     = TextStyle(RedditSans, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Body          = TextStyle(RedditSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val Metadata      = TextStyle(RedditSans, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Karma         = TextStyle(RedditSans, fontWeight = FontWeight.Bold,     fontSize = 12.sp, lineHeight = 12.sp)
    val SubredditPill = TextStyle(RedditSans, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 12.sp)
    val FlairPill     = TextStyle(RedditSans, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
    val Username      = TextStyle(RedditSans, fontWeight = FontWeight.Medium,   fontSize = 12.sp, lineHeight = 14.sp)
    val Button        = TextStyle(RedditSans, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val TabLabel      = TextStyle(RedditSans, fontWeight = FontWeight.Medium,   fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val NavTitle      = TextStyle(RedditSans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val SectionHeader = TextStyle(RedditSans, fontWeight = FontWeight.Bold,     fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.5.sp)
    val Code          = TextStyle(FontFamily.Monospace, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 20.sp)
}

val RedditTypography = Typography(
    headlineSmall = RedditText.LargeTitle,
    titleMedium   = RedditText.PostTitle,
    bodyMedium    = RedditText.Body,
    labelMedium   = RedditText.Metadata,
    labelSmall    = RedditText.TabLabel,
)
```

## 3. Signature Components

### Vote Column (the signature control)

Up-chevron + karma number + down-chevron, on **every** post and comment. Upvote is warm orange, downvote is cool blue-purple — only one can be lit. Tap = state change + scale bounce + selection haptic + karma tick.

```kotlin
import androidx.compose.animation.animateColorAsState
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.spring
import androidx.compose.foundation.layout.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ArrowDownward
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp
import kotlinx.coroutines.launch

enum class VoteState { NONE, UP, DOWN }

@Composable
fun RedditVoteColumn(
    state: VoteState,
    baseKarma: Int,
    onVote: (VoteState) -> Unit,
    modifier: Modifier = Modifier,
) {
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()
    val upScale = remember { Animatable(1f) }
    val downScale = remember { Animatable(1f) }

    val displayKarma = when (state) {
        VoteState.UP -> baseKarma + 1
        VoteState.DOWN -> baseKarma - 1
        VoteState.NONE -> baseKarma
    }
    val accent by animateColorAsState(
        when (state) {
            VoteState.UP -> RedditColors.Upvote
            VoteState.DOWN -> RedditColors.Downvote
            VoteState.NONE -> RedditColors.VoteInactive
        },
        label = "voteColor",
    )

    suspend fun bounce(a: Animatable<Float, *>) {
        a.animateTo(1.25f, spring(dampingRatio = 0.55f, stiffness = 900f))
        a.animateTo(1f, spring(dampingRatio = 0.7f, stiffness = 700f))
    }

    Column(
        modifier = modifier.width(44.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Icon(
            Icons.Filled.ArrowUpward,
            contentDescription = "Upvote",
            tint = if (state == VoteState.UP) RedditColors.Upvote else RedditColors.VoteInactive,
            modifier = Modifier
                .size(18.dp)
                .scale(upScale.value)
                .then(Modifier.clickableNoRipple {
                    onVote(if (state == VoteState.UP) VoteState.NONE else VoteState.UP)
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~selection
                    scope.launch { bounce(upScale) }
                }),
        )
        Text("$displayKarma", style = RedditText.Karma, color = accent)
        Icon(
            Icons.Filled.ArrowDownward,
            contentDescription = "Downvote",
            tint = if (state == VoteState.DOWN) RedditColors.Downvote else RedditColors.VoteInactive,
            modifier = Modifier
                .size(18.dp)
                .scale(downScale.value)
                .then(Modifier.clickableNoRipple {
                    onVote(if (state == VoteState.DOWN) VoteState.NONE else VoteState.DOWN)
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                    scope.launch { bounce(downScale) }
                }),
        )
    }
}

// Small helper used across components: clickable with no ripple
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.runtime.Composable
fun Modifier.clickableNoRipple(onClick: () -> Unit): Modifier = composed {
    clickable(
        interactionSource = remember { MutableInteractionSource() },
        indication = null,
        onClick = onClick,
    )
}
```

### Post Card (the signature container)

Flat — 16.dp corner radius in card view (or 0.dp full-bleed classic), **no shadow**. Header row (subreddit avatar + `r/name` pill + timestamp) → title → optional flair → media → action row with the vote column on the left.

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import coil.compose.AsyncImage

data class Flair(val text: String, val bg: Color, val fg: Color, val emoji: String? = null)

@Composable
fun RedditPostCard(
    subredditName: String,
    subredditAvatarUrl: String?,
    subredditAccent: Color,
    timestamp: String,
    commentCount: Int,
    title: String,
    flairs: List<Flair>,
    mediaUrl: String?,
    baseKarma: Int,
    modifier: Modifier = Modifier,
) {
    var vote by remember { mutableStateOf(VoteState.NONE) }

    Column(
        modifier = modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp, vertical = 4.dp)
            .clip(RoundedCornerShape(16.dp)) // flat — no shadow
            .background(RedditColors.CardLight)
            .padding(12.dp),
        verticalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        // Header
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            AsyncImage(
                model = subredditAvatarUrl,
                contentDescription = null,
                contentScale = ContentScale.Crop,
                modifier = Modifier.size(20.dp).clip(CircleShape),
            )
            Text("r/$subredditName", style = RedditText.SubredditPill, color = RedditColors.TextPrimaryLight)
            Text("•", style = RedditText.Metadata, color = RedditColors.TextSecondaryLight)
            Text(timestamp, style = RedditText.Metadata, color = RedditColors.TextSecondaryLight)
            Spacer(Modifier.weight(1f))
            RedditJoinButton(accent = subredditAccent)
        }

        Text(title, style = RedditText.PostTitle, color = RedditColors.TextPrimaryLight, maxLines = 3, overflow = TextOverflow.Ellipsis)

        if (flairs.isNotEmpty()) {
            LazyRow(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                items(flairs) { RedditFlairPill(it) }
            }
        }

        if (mediaUrl != null) {
            AsyncImage(
                model = mediaUrl,
                contentDescription = null,
                contentScale = ContentScale.Crop,
                modifier = Modifier
                    .fillMaxWidth()
                    .heightIn(max = 300.dp)
                    .clip(RoundedCornerShape(8.dp)),
            )
        }

        // Action row — vote column LEFT
        Row(verticalAlignment = Alignment.CenterVertically) {
            RedditVoteColumn(state = vote, baseKarma = baseKarma, onVote = { vote = it })
            Spacer(Modifier.weight(1f))
            Row(horizontalArrangement = Arrangement.spacedBy(16.dp), verticalAlignment = Alignment.CenterVertically) {
                ActionIcon(Icons.AutoMirrored.Filled.Comment, "$commentCount")
                ActionIcon(Icons.Filled.IosShare, null)
                ActionIcon(Icons.Filled.BookmarkBorder, null)
                ActionIcon(Icons.Filled.MoreHoriz, null)
            }
        }
    }
}

@Composable
private fun ActionIcon(icon: androidx.compose.ui.graphics.vector.ImageVector, label: String?) {
    Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
        Icon(icon, contentDescription = label, tint = RedditColors.TextSecondaryLight, modifier = Modifier.size(18.dp))
        if (label != null) Text(label, style = RedditText.Metadata, color = RedditColors.TextSecondaryLight)
    }
}
```

### Flair Pill

4.dp rounded rectangle (not a full pill) with per-subreddit or per-system colors.

```kotlin
@Composable
fun RedditFlairPill(flair: Flair) {
    Row(
        modifier = Modifier
            .clip(RoundedCornerShape(4.dp))
            .background(flair.bg)
            .padding(horizontal = 6.dp, vertical = 3.dp),
        horizontalArrangement = Arrangement.spacedBy(4.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        flair.emoji?.let { Text(it, style = RedditText.FlairPill) }
        Text(flair.text, style = RedditText.FlairPill, color = flair.fg)
    }
}

// Common system flairs
val FlairNSFW    = Flair("NSFW",    RedditColors.NSFWYellow,   Color.Black)
val FlairSpoiler = Flair("SPOILER", Color.Black,               Color.White)
val FlairOC      = Flair("OC",      RedditColors.SuccessGreen, Color.White)
```

### Comment Row (indentation rule)

Each reply level indents 10.dp with a 2.dp vertical rule on the leading edge. Collapse via a `[-]`/`[+]` toggle.

```kotlin
data class RedditComment(
    val username: String,
    val karma: Int,
    val timestamp: String,
    val body: String,
    val depth: Int,
)

@Composable
fun RedditCommentRow(comment: RedditComment, modifier: Modifier = Modifier) {
    var collapsed by remember { mutableStateOf(false) }

    Row(modifier = modifier.fillMaxWidth()) {
        // One 2.dp rule per depth level, 10.dp apart
        repeat(comment.depth) {
            Spacer(
                Modifier
                    .padding(start = 10.dp)
                    .width(2.dp)
                    .fillMaxHeight()
                    .background(RedditColors.DividerLight)
            )
        }
        Column(
            modifier = Modifier
                .padding(start = 8.dp, top = 10.dp, bottom = 10.dp)
                .weight(1f),
            verticalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Text(
                    if (collapsed) "[+]" else "[−]",
                    style = RedditText.Metadata,
                    color = RedditColors.TextSecondaryLight,
                    modifier = Modifier.clickableNoRipple { collapsed = !collapsed },
                )
                Text("u/${comment.username}", style = RedditText.Username, color = RedditColors.TextPrimaryLight)
                Text("•", style = RedditText.Metadata, color = RedditColors.TextSecondaryLight)
                Text(comment.timestamp, style = RedditText.Metadata, color = RedditColors.TextSecondaryLight)
                Text("•", style = RedditText.Metadata, color = RedditColors.TextSecondaryLight)
                Text("${comment.karma}", style = RedditText.Username, color = RedditColors.TextSecondaryLight)
            }
            if (!collapsed) {
                Text(comment.body, style = RedditText.Body, color = RedditColors.TextPrimaryLight)
            }
        }
    }
}
```

### Subreddit Join Button (subreddit-accent tinted)

```kotlin
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Check
import androidx.compose.foundation.border

@Composable
fun RedditJoinButton(accent: Color, modifier: Modifier = Modifier) {
    var joined by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current

    Row(
        modifier = modifier
            .clip(CircleShape)
            .then(if (joined) Modifier.border(1.dp, accent, CircleShape) else Modifier.background(accent))
            .clickableNoRipple {
                joined = !joined
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
            }
            .padding(horizontal = 14.dp, vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Icon(
            if (joined) Icons.Filled.Check else Icons.Filled.Add,
            contentDescription = null,
            tint = if (joined) accent else Color.White,
            modifier = Modifier.size(14.dp),
        )
        Text(if (joined) "Joined" else "Join", style = RedditText.Button, color = if (joined) accent else Color.White)
    }
}
```

## 4. Subreddit Accent Color (Dynamic Theming)

Subreddits set their own accent color (Join button, sidebar, banner tint) — Reddit's signature per-community customization. iOS passes a `Color` per screen; in Compose model it as a `CompositionLocal` so any nested component (Join button, banner) inherits without prop-drilling, and validate the user-chosen hex's contrast at the boundary.

```kotlin
import androidx.compose.runtime.compositionLocalOf
import androidx.compose.runtime.CompositionLocalProvider
import androidx.compose.ui.graphics.luminance

data class SubredditContext(
    val accent: Color = RedditColors.SubredditDefault, // #0079D3
    val displayName: String = "",
)

val LocalSubreddit = compositionLocalOf { SubredditContext() }

@Composable
fun ProvideSubreddit(ctx: SubredditContext, content: @Composable () -> Unit) =
    CompositionLocalProvider(LocalSubreddit provides ctx, content = content)

// On the boundary: pick readable on-accent text from the chosen hex's luminance
fun onAccentColor(accent: Color): Color =
    if (accent.luminance() > 0.5f) RedditColors.TextPrimaryLight else Color.White
```

## 5. Bottom Navigation (Tab Bar)

Five destinations in a Material 3 `NavigationBar`: Home, Communities, Create (center, FAB-ish), Chat, Inbox. Active tint is brand red or neutral (both ship in production — pick one and keep it). Solid surface, no blur. The Inbox tab carries a red mention badge.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun RedditBottomBar(selected: Int, unreadInbox: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = RedditColors.CardLight, // solid, no blur
        tonalElevation = 0.dp,
    ) {
        data class Tab(val label: String, val icon: androidx.compose.ui.graphics.vector.ImageVector, val badge: Int = 0)
        val tabs = listOf(
            Tab("Home", Icons.Filled.Home),
            Tab("Communities", Icons.Filled.Groups),
            Tab("Create", Icons.Filled.AddCircle),
            Tab("Chat", Icons.AutoMirrored.Filled.Chat),
            Tab("Inbox", Icons.Filled.Notifications, badge = unreadInbox),
        )
        tabs.forEachIndexed { i, t ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (t.badge > 0) {
                        BadgedBox(badge = { Badge(containerColor = RedditColors.AlertRed) { Text("${t.badge}") } }) {
                            Icon(t.icon, t.label, modifier = Modifier.size(if (i == 2) 26.dp else 22.dp))
                        }
                    } else {
                        Icon(t.icon, t.label, modifier = Modifier.size(if (i == 2) 26.dp else 22.dp))
                    }
                },
                label = { Text(t.label, style = RedditText.TabLabel) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = RedditColors.BrandRed,
                    selectedTextColor   = RedditColors.BrandRed,
                    unselectedIconColor = RedditColors.VoteInactive,
                    unselectedTextColor = RedditColors.VoteInactive,
                    indicatorColor      = Color.Transparent,
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Vote tap | `Animatable` 1 → 1.25 → 1 with `spring(dampingRatio = 0.55f)`; karma color cross-fades via `animateColorAsState`; `HapticFeedbackType.TextHandleMove` (selection) |
| Karma number tick | `AnimatedContent` with `slideInVertically`/`slideOutVertically` on the count `Text` |
| Card press | `animateFloatAsState` scale → 0.99 + background tint to `Surface2Light` over ~120ms |
| Comment collapse | `animateContentSize()` on the comment `Column` (200ms) |
| Join success | `animateColorAsState` accent fill → outline + `HapticFeedbackType.LongPress` |
| Infinite scroll | new cards fade + 8.dp slide-up via `Modifier.animateItem()` |
| Pull-to-refresh | `rememberInfiniteTransition` rotating the red Snoo drawable |

```kotlin
// Karma number ticker — number slides up/down on change
@Composable
fun KarmaTicker(value: Int, color: Color) {
    AnimatedContent(
        targetState = value,
        transitionSpec = {
            if (targetState > initialState)
                (slideInVertically { it } + fadeIn()) togetherWith (slideOutVertically { -it } + fadeOut())
            else
                (slideInVertically { -it } + fadeIn()) togetherWith (slideOutVertically { it } + fadeOut())
        },
        label = "karma",
    ) { v -> Text("$v", style = RedditText.Karma, color = color) }
}
```

Keep vote feedback subtle — a single bounce + color swap, never an elaborate cascade. Haptics: prefer `LocalHapticFeedback`; for the per-vote selection tick use `HapticFeedbackType.TextHandleMove`, or `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` on API 30+. Honor `LocalAccessibilityManager` reduce-motion by skipping the bounce.

## 7. Icons

Reddit ships custom up/down arrow glyphs and the Snoo mascot; the closest first-party set is `androidx.compose.material:material-icons-extended`. Snoo (empty states + pull-to-refresh) ships as a vector drawable with light + dark variants via `ImageVector.vectorResource(R.drawable.ic_snoo)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Upvote | `arrow.up` / `arrow.up.square.fill` | `Icons.Filled.ArrowUpward` |
| Downvote | `arrow.down` / `arrow.down.square.fill` | `Icons.Filled.ArrowDownward` |
| Comment count | `text.bubble` | `Icons.AutoMirrored.Filled.Comment` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Save | `bookmark` / `bookmark.fill` | `Icons.Filled.BookmarkBorder` / `Icons.Filled.Bookmark` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Reply | `arrowshape.turn.up.left` | `Icons.AutoMirrored.Filled.Reply` |
| Join (state) | `plus` / `checkmark` | `Icons.Filled.Add` / `Icons.Filled.Check` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Communities (tab) | `person.3.fill` | `Icons.Filled.Groups` |
| Create (tab) | `plus.circle.fill` | `Icons.Filled.AddCircle` |
| Chat (tab) | `bubble.left.and.bubble.right.fill` | `Icons.AutoMirrored.Filled.Chat` |
| Inbox (tab) | `bell.fill` | `Icons.Filled.Notifications` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Collapse comment | `minus.square` / `plus.square` | text `[−]` / `[+]` (matches Reddit's literal glyphs) |
| Sort | `arrow.up.arrow.down` | `Icons.Filled.SwapVert` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24**. `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; system-bar icon color flips with light/dark. Apply `Scaffold` insets so cards clear the status bar and the tab bar clears gesture nav; the comment compose bar must rise above the IME (`Modifier.imePadding()`).
- **Font scaling**: `sp` honors user scale on post title, body, comment body, metadata. **Pin the karma number, vote arrow sizing, flair pills, and tab labels to fixed sizes** (layout-sensitive) by deriving from `dp` or wrapping in a fixed-`fontScale` `LocalDensity`.
- **TalkBack**: merge the vote column into one element — `Modifier.semantics(mergeDescendants = true) { contentDescription = "$displayKarma karma, upvote, downvote" }`. The vote arrows are iconographic (up vs down) so they stay semantic without color; ensure NSFW/OC flair text is readable, not color-only.
- **Touch targets**: each vote arrow needs a 36–48.dp hit area (glyph is 18.dp) via `Modifier.size()`/padding; action-row icons need 44–48.dp; the comment collapse `[−]` needs ≥32.dp.
- **Contrast**: `#7C7C7C` on `#FFFFFF` passes WCAG AA at 12sp+; validate dark-mode `#818384` on `#272729`. Subreddit accent colors are user-chosen — run `onAccentColor()` (luminance split) and bump to a darker variant if the Join-button white fails contrast.
- **Markdown**: post/comment bodies render full Markdown. Parse `**bold**`, `*italic*`, `> quote`, `[text](url)`, `` `code` ``, fenced blocks via `buildAnnotatedString` or a Markdown-to-`AnnotatedString` library; links use `#0079D3` (the link color — never the brand red).
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — the brand red, the warm/cool vote pair, and the per-subreddit accent are all semantic and must not shift with wallpaper. Subreddit accent is the only "dynamic" color, and it is data-driven, not wallpaper-driven.
