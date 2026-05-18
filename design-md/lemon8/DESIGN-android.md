# Lemon8 (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Lemon8's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the two-column masonry feed, the lifestyle card, the pastel tag system, and the post-detail article with motion and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Lemon8's bright magazine-white canvas, two-column staggered masonry, pastel topic tags, sparing yellow accent, soft card elevation) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyVerticalStaggeredGrid` for the masonry, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for cover images. Lemon8 is light-first (a magazine); a full dark scheme is provided. No Material You dynamic color — the magazine-white identity must hold regardless of wallpaper.

## 1. Color Tokens

```kotlin
// ui/theme/Lemon8Colors.kt
import androidx.compose.ui.graphics.Color

object Lemon8Colors {
    // Canvas & Surfaces (Light — core identity)
    val Canvas    = Color(0xFFFFFFFF)
    val Surface1  = Color(0xFFFAFAFA)
    val Surface2  = Color(0xFFF2F2F2)
    val Divider   = Color(0xFFECECEC)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF121212)
    val DarkSurface1 = Color(0xFF1C1C1E)
    val DarkSurface2 = Color(0xFF2A2A2A)
    val DarkDivider  = Color(0xFF2E2E2E)

    // Text
    val Ink            = Color(0xFF1A1A1A)
    val TextSecondary  = Color(0xFF6B6B6B)
    val TextTertiary   = Color(0xFF9A9A9A)
    val TextPrimaryDk  = Color(0xFFF2F2F2)

    // Brand (sparing — ALWAYS paired with Ink text, never white-on-yellow)
    val Yellow        = Color(0xFFFFE600)
    val YellowPressed = Color(0xFFE6CF00)

    // Semantic
    val LikeRed = Color(0xFFFF2E63)
    val Success = Color(0xFF1FB877)
    val Error   = Color(0xFFFF3B5C)

    // Photo chip scrim
    val Scrim   = Color(0x8C000000) // rgba(0,0,0,0.55)
}

// Pastel topic tags — bg + saturated text sibling (light) + dark equivalents
data class TagPair(val bg: Color, val fg: Color, val darkBg: Color, val darkFg: Color)
object Lemon8Tags {
    val Mint     = TagPair(Color(0xFFE4F5EC), Color(0xFF1F8A52), Color(0xFF1F3A2C), Color(0xFF6FD49E))
    val Blush    = TagPair(Color(0xFFFCE8EE), Color(0xFFC13E68), Color(0xFF3A2630), Color(0xFFF09BB6))
    val Sky      = TagPair(Color(0xFFE6F0FB), Color(0xFF2D6FB8), Color(0xFF1F2F42), Color(0xFF8FBEE8))
    val Butter   = TagPair(Color(0xFFFFF6CC), Color(0xFF8A7300), Color(0xFF3A350F), Color(0xFFE6CF66))
    val Lavender = TagPair(Color(0xFFF0EAFB), Color(0xFF7050B0), Color(0xFF2E263C), Color(0xFFC0A8E8))
}
```

Wire it into both schemes. Lemon8 is light-first (a magazine page); the dark scheme uses `#121212`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val Lemon8Light = lightColorScheme(
    primary        = Lemon8Colors.Yellow,
    onPrimary      = Lemon8Colors.Ink,        // ink on yellow — never white
    background     = Lemon8Colors.Canvas,
    onBackground   = Lemon8Colors.Ink,
    surface        = Lemon8Colors.Canvas,
    onSurface      = Lemon8Colors.Ink,
    surfaceVariant = Lemon8Colors.Surface2,
    outline        = Lemon8Colors.Divider,
    error          = Lemon8Colors.Error,
)

private val Lemon8Dark = darkColorScheme(
    primary        = Lemon8Colors.Yellow,
    onPrimary      = Lemon8Colors.Ink,
    background     = Lemon8Colors.DarkCanvas,
    onBackground   = Lemon8Colors.TextPrimaryDk,
    surface        = Lemon8Colors.DarkSurface1,
    onSurface      = Lemon8Colors.TextPrimaryDk,
    surfaceVariant = Lemon8Colors.DarkSurface2,
    outline        = Lemon8Colors.DarkDivider,
    error          = Lemon8Colors.Error,
)

@Composable
fun Lemon8Theme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) Lemon8Dark else Lemon8Light,
    typography = Lemon8Typography,
    content = content,
)
```

## 2. Typography

Lemon8 pairs a friendly geometric display face (**Poppins**, headlines/titles) with a clean UI sans (**Inter**, body/cards/meta/tags). Both SIL OFL — drop the TTFs in `res/font/`. Poppins for headlines, Inter for everything else; never set body in Poppins.

```kotlin
// ui/theme/Lemon8Type.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Poppins = FontFamily(
    Font(R.font.poppins_semibold,  FontWeight.SemiBold),
    Font(R.font.poppins_bold,      FontWeight.Bold),
    Font(R.font.poppins_extrabold, FontWeight.ExtraBold),
)
val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
    Font(R.font.inter_bold,     FontWeight.Bold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt -> sp 1:1)
object Lemon8Text {
    val Display     = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.5).sp)
    val ScreenTitle = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 33.sp, letterSpacing = (-0.3).sp)
    val TabHeader   = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 29.sp, letterSpacing = (-0.3).sp)
    val PostTitle   = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 18.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Subheading  = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 17.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 25.sp)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 18.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 15.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val CardByline  = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 11.sp, lineHeight = 14.sp)
    val Tag         = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
}

val Lemon8Typography = Typography(
    headlineLarge = Lemon8Text.Display,
    headlineMedium = Lemon8Text.ScreenTitle,
    titleLarge    = Lemon8Text.TabHeader,
    titleMedium   = Lemon8Text.PostTitle,
    bodyMedium    = Lemon8Text.Body,
    labelSmall    = Lemon8Text.Tab,
)
```

## 3. Signature Components

### Two-Column Masonry Feed

Use `LazyVerticalStaggeredGrid` — the idiomatic Compose masonry.

```kotlin
import androidx.compose.foundation.lazy.staggeredgrid.LazyVerticalStaggeredGrid
import androidx.compose.foundation.lazy.staggeredgrid.StaggeredGridCells
import androidx.compose.foundation.lazy.staggeredgrid.items
import androidx.compose.foundation.layout.PaddingValues
import androidx.compose.ui.unit.dp

data class Post(
    val id: String, val aspect: Float, val gradient: List<Color>,
    val title: String, val tag: TagPair, val tagText: String,
    val creator: String, val likes: String, val photoChip: String?,
)

@Composable
fun MasonryFeed(posts: List<Post>) {
    LazyVerticalStaggeredGrid(
        columns = StaggeredGridCells.Fixed(2),
        contentPadding = PaddingValues(10.dp),
        horizontalArrangement = androidx.compose.foundation.layout.Arrangement.spacedBy(10.dp),
        verticalItemSpacing = 12.dp,
        modifier = Modifier.fillMaxSize().background(Lemon8Colors.Canvas),
    ) {
        items(posts, key = { it.id }) { LifestyleCard(it) }
    }
}
```

### Lifestyle Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.outlined.FavoriteBorder
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.layout.ContentScale

@Composable
fun LifestyleCard(post: Post) {
    Column(
        Modifier
            .fillMaxWidth()
            .shadow(3.dp, RoundedCornerShape(14.dp), spotColor = Color.Black.copy(alpha = 0.06f))
            .clip(RoundedCornerShape(14.dp))
            .background(Lemon8Colors.Canvas)
            .clickable { /* open post */ }
    ) {
        Box(
            Modifier
                .fillMaxWidth()
                .aspectRatio(post.aspect)
                .background(Brush.linearGradient(post.gradient))
        ) {
            post.photoChip?.let {
                Text(
                    it,
                    style = Lemon8Text.Tag.copy(fontSize = 10.sp),
                    color = Color.White,
                    modifier = Modifier
                        .align(Alignment.BottomStart)
                        .padding(8.dp)
                        .clip(RoundedCornerShape(6.dp))
                        .background(Lemon8Colors.Scrim)
                        .padding(horizontal = 8.dp, vertical = 4.dp),
                )
            }
        }
        Column(Modifier.padding(11.dp), verticalArrangement = Arrangement.spacedBy(7.dp)) {
            Text(post.title, style = Lemon8Text.CardTitle, color = Lemon8Colors.Ink, maxLines = 2)
            Box(
                Modifier
                    .clip(RoundedCornerShape(5.dp))
                    .background(post.tag.bg)
                    .padding(horizontal = 7.dp, vertical = 3.dp)
            ) {
                Text(post.tagText, style = Lemon8Text.Tag.copy(fontSize = 9.sp), color = post.tag.fg)
            }
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Box(
                    Modifier.size(18.dp).clip(CircleShape)
                        .background(Brush.linearGradient(listOf(Lemon8Colors.Yellow, Lemon8Colors.LikeRed)))
                )
                Text(post.creator, style = Lemon8Text.CardByline, color = Lemon8Colors.TextSecondary, modifier = Modifier.weight(1f))
                Icon(Icons.Outlined.FavoriteBorder, null, tint = Lemon8Colors.TextSecondary, modifier = Modifier.size(12.dp))
                Text(post.likes, style = Lemon8Text.CardByline, color = Lemon8Colors.TextSecondary)
            }
        }
    }
}
```

### Pastel Topic Tag

```kotlin
@Composable
fun TopicTag(text: String, pair: TagPair) {
    Box(
        Modifier
            .clip(RoundedCornerShape(8.dp))
            .background(pair.bg)
            .padding(horizontal = 14.dp, vertical = 7.dp)
    ) {
        Text(text, style = Lemon8Text.Tag, color = pair.fg)
    }
}
// TopicTag("Recipe", Lemon8Tags.Mint)
```

### Top Segmented Tabs

```kotlin
import androidx.compose.runtime.*

@Composable
fun TopTabs(onSelect: (Int) -> Unit) {
    var sel by remember { mutableIntStateOf(1) }
    val titles = listOf("Following", "For You", "Nearby")
    Row(
        Modifier.fillMaxWidth().padding(start = 18.dp, end = 18.dp, top = 4.dp, bottom = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(22.dp),
    ) {
        titles.forEachIndexed { i, t ->
            Column(
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.spacedBy(6.dp),
                modifier = Modifier.clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) {
                    sel = i; onSelect(i)
                },
            ) {
                Text(
                    t,
                    style = Lemon8Text.TabHeader.copy(fontWeight = if (sel == i) FontWeight.Bold else FontWeight.SemiBold),
                    color = if (sel == i) Lemon8Colors.Ink else Lemon8Colors.TextTertiary,
                )
                Box(
                    Modifier
                        .width(20.dp).height(3.dp)
                        .clip(RoundedCornerShape(2.dp))
                        .background(if (sel == i) Lemon8Colors.Yellow else Color.Transparent)
                )
            }
        }
        Spacer(Modifier.weight(1f))
        Icon(Icons.Filled.Search, "Search", tint = Lemon8Colors.Ink, modifier = Modifier.size(22.dp))
    }
}
```

### Like Heart + Buttons

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun LikeHeart() {
    var liked by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current
    val scale = remember { Animatable(1f) }
    LaunchedEffect(liked) {
        if (liked) {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // soft impact analog
            scale.animateTo(1.2f, spring(dampingRatio = 0.6f, stiffness = Spring.StiffnessMediumLow))
            scale.animateTo(1.0f, spring(dampingRatio = 0.6f, stiffness = Spring.StiffnessMediumLow))
        }
    }
    Icon(
        if (liked) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
        contentDescription = if (liked) "Unlike" else "Like",
        tint = if (liked) Lemon8Colors.LikeRed else Lemon8Colors.TextSecondary,
        modifier = Modifier
            .size(44.dp).padding(10.dp).scale(scale.value)
            .clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) { liked = !liked },
    )
}

@Composable
fun PrimaryButton(title: String, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(if (pressed) Lemon8Colors.YellowPressed else Lemon8Colors.Yellow)
            .clickable(interactionSource = interaction, indication = null) { onClick() }
            .padding(horizontal = 28.dp, vertical = 13.dp)
    ) {
        Text(title, style = Lemon8Text.Button, color = Lemon8Colors.Ink) // ink — never white on yellow
    }
}

@Composable
fun FollowPill() {
    var following by remember { mutableStateOf(false) }
    val shape = RoundedCornerShape(999.dp)
    Box(
        Modifier
            .clip(shape)
            .then(if (following) Modifier.border(1.dp, Color(0xFFD8D8D8), shape) else Modifier.background(Lemon8Colors.Yellow))
            .clickable { following = !following }
            .padding(horizontal = 18.dp, vertical = 7.dp)
    ) {
        Text(if (following) "Following" else "Follow",
            style = Lemon8Text.Button.copy(fontSize = 12.sp),
            color = if (following) Lemon8Colors.TextSecondary else Lemon8Colors.Ink)
    }
}
```

## 4. Navigation

Lemon8's bottom bar has 5 slots; the center "Post" is a yellow rounded rectangle, not a Material FAB or icon-only tab. No tint pill — active is just ink.

```kotlin
@Composable
fun Lemon8BottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = Lemon8Colors.Canvas.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home" to Icons.Filled.Home,
            "Discover" to Icons.Filled.Search,
            null,                                  // center Post slot — custom
            "Inbox" to Icons.Filled.ChatBubble,
            "Me" to Icons.Filled.Person,
        )
        items.forEachIndexed { i, item ->
            if (item == null) {
                NavigationBarItem(
                    selected = false,
                    onClick = { onSelect(2) },
                    icon = {
                        Box(
                            Modifier.size(width = 42.dp, height = 30.dp)
                                .clip(RoundedCornerShape(9.dp))
                                .background(Lemon8Colors.Yellow),
                            contentAlignment = Alignment.Center,
                        ) { Icon(Icons.Filled.Add, "Create post", tint = Lemon8Colors.Ink, modifier = Modifier.size(20.dp)) }
                    },
                )
            } else {
                val (label, icon) = item
                NavigationBarItem(
                    selected = selected == i,
                    onClick = { onSelect(i) },
                    icon = { Icon(icon, label, modifier = Modifier.size(23.dp)) },
                    label = { Text(label, style = Lemon8Text.Tab) },
                    colors = NavigationBarItemDefaults.colors(
                        selectedIconColor = Lemon8Colors.Ink,
                        selectedTextColor = Lemon8Colors.Ink,
                        unselectedIconColor = Lemon8Colors.TextTertiary,
                        unselectedTextColor = Lemon8Colors.TextTertiary,
                        indicatorColor = Color.Transparent, // no Material pill — Lemon8 has none
                    ),
                )
            }
        }
    }
}
```

The post detail uses a small top app bar (back + share + overflow) over a `HorizontalPager` photo carousel; the creator card can pin to the bar on scroll.

## 5. Motion

Lemon8 motion is light and editorial — soft, never harsh.

| Moment | Compose recipe |
|--------|----------------|
| Masonry card press | `Modifier.scale` 1.0 → 0.98 via `animateFloatAsState(tween(120))` on press |
| Card -> post detail | shared-element via `SharedTransitionLayout` / `sharedBounds`, 300ms ease-out expand of cover into the carousel |
| Top tab underline | animate a shared underline `Box` offset/width `tween(220, easing = FastOutSlowIn)`; content `Crossfade(tween(180))` |
| Like heart burst | `Animatable` `animateTo(1.2f)` then `1.0f` with `spring(dampingRatio = 0.6f)`; soft haptic; tint -> `LikeRed` |
| New masonry cards | `AnimatedVisibility` `fadeIn() + slideInVertically { it / 8 }` `tween(220)` as items load |
| Photo carousel | `HorizontalPager` paged scroll; animated page dots |
| Post / save success | `Success` checkmark `scaleIn(tween(200))` + snackbar slide-up |

```kotlin
// Like burst — the canonical Lemon8 micro-interaction
LaunchedEffect(liked) {
    if (liked) {
        scale.animateTo(1.2f, spring(dampingRatio = 0.6f, stiffness = Spring.StiffnessMediumLow))
        scale.animateTo(1.0f, spring(dampingRatio = 0.6f, stiffness = Spring.StiffnessMediumLow))
    }
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft like impact; `HapticFeedbackConstants.CLOCK_TICK` (via `LocalView`) for tab switch; `HapticFeedbackConstants.CONFIRM` for follow/save. Post completion shows a green checkmark + snackbar; no aggressive motion.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The like primitive is `Icons.Filled.Favorite` / `Icons.Outlined.FavoriteBorder`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Discover (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Post (tab, center) | `plus` | `Icons.Filled.Add` (ink on yellow rect) |
| Inbox (tab) | `message` / `message.fill` | `Icons.Filled.ChatBubble` |
| Me (tab) | `person` / `person.fill` | `Icons.Filled.Person` |
| Like (inactive) | `heart` | `Icons.Outlined.FavoriteBorder` |
| Like (active) | `heart.fill` | `Icons.Filled.Favorite` (`#FF2E63`) |
| Save | `bookmark` / `bookmark.fill` | `Icons.Outlined.BookmarkBorder` / `Icons.Filled.Bookmark` |
| Share | `square.and.arrow.up` | `Icons.Outlined.IosShare` |
| Comment | `bubble.right` | `Icons.Outlined.ChatBubbleOutline` |
| Search (top) | `magnifyingglass` | `Icons.Filled.Search` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; `LazyVerticalStaggeredGrid` and `SharedTransitionLayout` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. Coil `2.6+` for covers.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the bright white canvas wants dark-content system bars (light-content in dark mode). The feed keeps ~10dp insets; the photo carousel can go full-bleed; the tab bar respects the navigation bar inset.
- **Font scaling**: `sp` honors the user's font scale — keep it on display, screen titles, post title, body. Pin layout-sensitive text (tags, card bylines, 10sp tab labels) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` so the staggered grid stays balanced.
- **TalkBack**: label cards "Post: {title}, by {creator}, {likes} likes"; the like control as a toggle (`Modifier.semantics { role = Role.Switch; stateDescription = if (liked) "Liked" else "Not liked" }`); tags as "Topic: {name}"; the Post FAB as "Create post".
- **Touch targets**: Material guidance is 48.dp — the 12dp card heart and 22dp top-search icon get 48dp hit areas via padding; cards are large; the Post FAB is 42x30dp with a 48dp hit area.
- **Contrast**: Ink `#1A1A1A` on `#FFFFFF` is maximal; each pastel tag is paired with a saturated text sibling validated for WCAG AA (mint/blush/sky/butter). Never put white on `#FFE600` — always Ink.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the like spring/burst and the card->detail shared transition — substitute a `Crossfade`; keep the tab underline as an instant move and keep selection/liked state.
- **Dark mode**: Lemon8 is light-first; the dark scheme flips canvas to `#121212`, cards to `#1C1C1E`, and tags to their `darkBg`/`darkFg` variants so they stay legible without glowing. Yellow is constant and always pairs with Ink. Do **not** enable `dynamicColorScheme()` — the magazine-white identity must hold regardless of wallpaper.
