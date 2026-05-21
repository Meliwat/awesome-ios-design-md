# Vero (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Vero's calm, true-black visual language to **Android with Jetpack Compose (Material 3)**: a color token object, the brand gradient, a `Typography` set, paste-ready `@Composable`s for the 7 post-type selector and chronological feed, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Vero's true-black gallery wall, the single teal→blue gradient, the 7 first-class post types, the strictly chronological feed) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush.linearGradient` for the brand, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for media and avatars. Vero is true-black-first; the dark scheme is canonical.

## 1. Color Tokens

```kotlin
// ui/theme/VeroColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.geometry.Offset

object VeroColors {
    // Canvas & Surfaces (Dark — primary)
    val Canvas   = Color(0xFF000000) // PURE true-black — gallery void, OLED-deep
    val Surface1 = Color(0xFF0E0E10) // inset cards only
    val Surface2 = Color(0xFF161618)
    val Divider  = Color(0xFF232325)

    // Canvas & Surfaces (Light — rare parity)
    val LightCanvas   = Color(0xFFFFFFFF)
    val LightSurface1 = Color(0xFFF6F6F7)
    val LightDivider  = Color(0xFFE4E4E6)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF9A9AA0)
    val TextTertiary  = Color(0xFF5E5E64)

    // Brand
    val Teal      = Color(0xFF00C2B8) // flat accent
    val Blue      = Color(0xFF0098E6)
    val GradientA = Color(0xFF00D1C1)
    val GradientB = Color(0xFF0079D3)
    val TealPressed = Color(0xFF00A89F)

    // Ratings
    val Amber   = Color(0xFFE8B23A) // book/film stars ONLY
    val OnAmber = Color(0xFF1A1206)

    // Semantic
    val Success = Color(0xFF2ECC71)
    val Error   = Color(0xFFFF4D4F)

    // The single brand expression — use sparingly
    val BrandGradient = Brush.linearGradient(
        colors = listOf(GradientA, GradientB),
        start = Offset(0f, 0f),
        end = Offset(Float.POSITIVE_INFINITY, Float.POSITIVE_INFINITY),
    )
}
```

Wire it into both schemes. Vero is true-black-first — the dark scheme is the real product. Never enable Material You `dynamicColorScheme()` — the brand is one gradient and it must hold regardless of wallpaper.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme

private val VeroDark = darkColorScheme(
    primary          = VeroColors.Teal,
    onPrimary        = VeroColors.Canvas,
    background        = VeroColors.Canvas,
    onBackground      = VeroColors.TextPrimary,
    surface           = VeroColors.Surface1,
    onSurface         = VeroColors.TextPrimary,
    surfaceVariant    = VeroColors.Surface2,
    onSurfaceVariant  = VeroColors.TextSecondary,
    outline           = VeroColors.Divider,
    error             = VeroColors.Error,
)

private val VeroLight = lightColorScheme(
    primary      = VeroColors.Teal,
    onPrimary    = Color(0xFFFFFFFF),
    background    = VeroColors.LightCanvas,
    onBackground  = Color(0xFF0B0B0C),
    surface       = VeroColors.LightSurface1,
    onSurface     = Color(0xFF0B0B0C),
    outline       = VeroColors.LightDivider,
    error         = VeroColors.Error,
)

@Composable
fun VeroTheme(
    dark: Boolean = true, // true-black-first; pass isSystemInDarkTheme() only if you ship light parity
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) VeroDark else VeroLight,
    typography = VeroTypography,
    content = content,
)
```

## 2. Typography

Vero's UI face is **Manrope** (SIL OFL — drop the TTFs in `res/font/`). Calm, geometric, unhurried.

```kotlin
// ui/theme/VeroType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Manrope = FontFamily(
    Font(R.font.manrope_regular,   FontWeight.Normal),
    Font(R.font.manrope_medium,    FontWeight.Medium),
    Font(R.font.manrope_semibold,  FontWeight.SemiBold),
    Font(R.font.manrope_bold,      FontWeight.Bold),
    Font(R.font.manrope_extrabold, FontWeight.ExtraBold),
)

object VeroText {
    val Display     = TextStyle(Manrope, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val ScreenTitle = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Author      = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 24.sp, letterSpacing = (-0.1).sp)
    val AuthorSm    = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 14.sp, lineHeight = 18.sp)
    val Title       = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Caption     = TextStyle(Manrope, fontWeight = FontWeight.Medium,    fontSize = 14.sp, lineHeight = 21.sp)
    val BodyRead    = TextStyle(Manrope, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 22.sp)
    val Meta        = TextStyle(Manrope, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val Button      = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp, letterSpacing = 0.2.sp)
    val TypeLabel   = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.4.sp)
    val Tab         = TextStyle(Manrope, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp)
}

val VeroTypography = Typography(
    headlineLarge = VeroText.Display,
    headlineMedium = VeroText.ScreenTitle,
    titleLarge    = VeroText.Section,
    titleMedium   = VeroText.Author,
    bodyMedium    = VeroText.Caption,
    labelSmall    = VeroText.Tab,
)
```

## 3. Signature Components

### 7 Post-Type Selector

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.outlined.*
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

enum class VeroPostType(val label: String, val icon: ImageVector) {
    PHOTO("Photo", Icons.Outlined.Image),
    VIDEO("Video", Icons.Outlined.Videocam),
    LINK("Link",   Icons.Outlined.Link),
    MUSIC("Music", Icons.Outlined.MusicNote),
    FILM("Film",   Icons.Outlined.Movie),
    BOOK("Book",   Icons.Outlined.MenuBook),
    PLACE("Place", Icons.Outlined.LocationOn),
}

@Composable
fun PostTypeSelector(selected: VeroPostType, onSelect: (VeroPostType) -> Unit) {
    val haptics = LocalHapticFeedback.current
    FlowRow(
        modifier = Modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.spacedBy(10.dp),
        verticalArrangement = Arrangement.spacedBy(10.dp),
        maxItemsInEachRow = 4,
    ) {
        VeroPostType.entries.forEach { type ->
            val on = type == selected
            Column(
                Modifier
                    .weight(1f)
                    .clip(RoundedCornerShape(12.dp))
                    .background(if (on) VeroColors.Teal.copy(alpha = 0.08f) else VeroColors.Surface1)
                    .border(1.dp, if (on) VeroColors.Teal else VeroColors.Divider, RoundedCornerShape(12.dp))
                    .clickable {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                        onSelect(type)
                    }
                    .padding(vertical = 12.dp, horizontal = 4.dp),
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.spacedBy(7.dp),
            ) {
                Icon(type.icon, contentDescription = type.label,
                    tint = if (on) VeroColors.Teal else VeroColors.TextTertiary,
                    modifier = Modifier.size(20.dp))
                Text(type.label, style = VeroText.TypeLabel,
                    color = if (on) VeroColors.Teal else VeroColors.TextTertiary)
            }
        }
    }
}
```

### Chronological Feed Post

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.Image
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.FavoriteBorder
import androidx.compose.material.icons.filled.MoreHoriz
import androidx.compose.material.icons.outlined.ChatBubbleOutline
import androidx.compose.material.icons.outlined.IosShare
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

@Composable
fun FeedPost(
    author: String, type: VeroPostType, timeAgo: String,
    caption: String, likes: Int, comments: Int,
) {
    var liked by remember { mutableStateOf(false) }
    val heartScale by animateFloatAsState(if (liked) 1f else 1f, label = "heart")
    val haptics = LocalHapticFeedback.current

    Column(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .padding(top = 16.dp, bottom = 14.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(10.dp)) {
            Box(Modifier.size(38.dp).clip(RoundedCornerShape(50)).background(VeroColors.BrandGradient))
            Column(Modifier.weight(1f)) {
                Text(author, style = VeroText.AuthorSm, color = VeroColors.TextPrimary)
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                    Icon(type.icon, null, tint = VeroColors.Teal, modifier = Modifier.size(11.dp))
                    Text(type.label, style = VeroText.TypeLabel.copy(fontWeight = FontWeight.Bold), color = VeroColors.Teal)
                    Text("· $timeAgo", style = VeroText.Meta, color = VeroColors.TextTertiary)
                }
            }
            Icon(Icons.Filled.MoreHoriz, null, tint = VeroColors.TextTertiary)
        }

        Box(
            Modifier
                .fillMaxWidth().aspectRatio(4f / 5f).padding(top = 12.dp)
                .clip(RoundedCornerShape(10.dp))
                .background(Brush.linearGradient(listOf(Color(0xFF1C4A52), Color(0xFF07181B)))),
        )

        Text(caption, style = VeroText.Caption, color = VeroColors.TextSecondary,
            modifier = Modifier.padding(top = 12.dp))

        Row(
            Modifier.padding(top = 14.dp),
            horizontalArrangement = Arrangement.spacedBy(22.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Row(
                Modifier.clickable {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                    liked = !liked
                },
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(6.dp),
            ) {
                Icon(
                    if (liked) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder, null,
                    tint = if (liked) VeroColors.Teal else VeroColors.TextSecondary,
                    modifier = Modifier.size(19.dp).scale(heartScale),
                )
                Text("${likes + if (liked) 1 else 0}",
                    style = VeroText.TypeLabel.copy(fontSize = 12.sp),
                    color = if (liked) VeroColors.Teal else VeroColors.TextSecondary)
            }
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Icon(Icons.Outlined.ChatBubbleOutline, null, tint = VeroColors.TextSecondary, modifier = Modifier.size(19.dp))
                Text("$comments", style = VeroText.TypeLabel.copy(fontSize = 12.sp), color = VeroColors.TextSecondary)
            }
            Icon(Icons.Outlined.IosShare, null, tint = VeroColors.TextSecondary, modifier = Modifier.size(19.dp))
        }
    }
    HorizontalDivider(color = VeroColors.Divider, thickness = 0.5.dp)
}
```

### Book / Film Inner Card

```kotlin
@Composable
fun BookCard(title: String, author: String, rating: Int) {
    Row(
        Modifier
            .clip(RoundedCornerShape(10.dp))
            .background(VeroColors.Surface1)
            .border(1.dp, VeroColors.Divider, RoundedCornerShape(10.dp))
            .padding(12.dp),
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(
            Modifier.size(56.dp, 80.dp).clip(RoundedCornerShape(4.dp))
                .background(Brush.linearGradient(listOf(Color(0xFFC29A3A), Color(0xFF7A5A18)))),
        )
        Column {
            Text(title, style = VeroText.Title, color = VeroColors.TextPrimary)
            Text(author, style = VeroText.Meta, color = VeroColors.TextSecondary,
                modifier = Modifier.padding(top = 3.dp))
            Text("★".repeat(rating) + "☆".repeat(5 - rating),
                style = VeroText.TypeLabel.copy(letterSpacing = 1.sp),
                color = VeroColors.Amber, modifier = Modifier.padding(top = 4.dp))
        }
    }
}
```

### Primary "Post" Button + Wordmark

```kotlin
@Composable
fun VeroPostButton(onClick: () -> Unit) {
    Box(
        Modifier
            .clip(RoundedCornerShape(50))
            .background(VeroColors.BrandGradient)
            .clickable { onClick() }
            .padding(vertical = 13.dp, horizontal = 28.dp),
    ) {
        Text("Post", style = VeroText.Button, color = Color.White)
    }
}

@Composable
fun VeroWordmark() {
    Text(
        "VERO",
        style = VeroText.Display.copy(letterSpacing = 2.sp),
        modifier = Modifier.graphicsLayer(alpha = 0.99f)
            .drawWithCache {
                onDrawWithContent {
                    drawContent()
                    drawRect(VeroColors.BrandGradient, blendMode = androidx.compose.ui.graphics.BlendMode.SrcAtop)
                }
            },
    )
}
```

## 4. 7 Post-Type Composer Flow

Vero's creation UX is the 7-type selector → typed composer. Tapping the gradient compose ring opens a `ModalBottomSheet` containing `PostTypeSelector`; choosing a type swaps in that type's composer (caption + a type-specific picker — e.g. a book search for Book, a map for Place, a track search for Music). All seven are co-equal entry points; never default-favor Photo.

```kotlin
@Composable
fun ComposeSheet(onDismiss: () -> Unit) {
    var type by remember { mutableStateOf(VeroPostType.PHOTO) }
    ModalBottomSheet(onDismissRequest = onDismiss, containerColor = VeroColors.Canvas) {
        Column(Modifier.padding(16.dp)) {
            PostTypeSelector(type) { type = it }
            Spacer(Modifier.height(16.dp))
            // Swap the typed composer body on `type`
            when (type) {
                VeroPostType.BOOK  -> Text("Search a book…", style = VeroText.Caption, color = VeroColors.TextTertiary)
                VeroPostType.PLACE -> Text("Add a location…", style = VeroText.Caption, color = VeroColors.TextTertiary)
                else               -> Text("Write a caption…", style = VeroText.Caption, color = VeroColors.TextTertiary)
            }
            Spacer(Modifier.height(16.dp))
            VeroPostButton(onClick = onDismiss)
        }
    }
}
```

## 5. Navigation

Vero has minimal chrome: a gradient wordmark top bar and a 5-slot bottom strip with a gradient-ring center compose action. On Android model the strip as a `NavigationBar`; there is no Material tint pill — active is flat teal.

```kotlin
@Composable
fun VeroBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = VeroColors.Canvas.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Feed"     to Icons.Filled.Home,
            "Discover" to Icons.Filled.Search,
            "Compose"  to Icons.Filled.AddCircleOutline, // center — render the gradient ring
            "Inbox"    to Icons.Filled.Email,
            "Profile"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            val isCompose = i == 2
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (isCompose) ComposeRingIcon()
                    else Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp))
                },
                label = if (isCompose) null else { { Text(label, style = VeroText.Tab) } },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = VeroColors.Teal,
                    selectedTextColor   = VeroColors.Teal,
                    unselectedIconColor = VeroColors.TextTertiary,
                    unselectedTextColor = VeroColors.TextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill — Vero has none
                ),
            )
        }
    }
}

@Composable
fun ComposeRingIcon() {
    Box(Modifier.size(27.dp), contentAlignment = Alignment.Center) {
        Box(Modifier.size(27.dp).clip(RoundedCornerShape(50)).background(VeroColors.BrandGradient))
        Box(Modifier.size(23.dp).clip(RoundedCornerShape(50)).background(VeroColors.Canvas))
        Icon(Icons.Filled.Add, null, tint = VeroColors.Teal, modifier = Modifier.size(15.dp))
    }
}
```

The top bar is a `TopAppBar` with the gradient `VeroWordmark` as a leading title, trailing search + notifications `IconButton`s; `containerColor = VeroColors.Canvas`, a 0.5dp `Divider` only when content scrolls under it.

## 6. Motion

Vero motion is calm — short ease-out, no aggressive springs, never reshuffle the feed.

| Moment | Compose recipe |
|--------|----------------|
| Post-type select | border/content `animateColorAsState` `tween(160)`; soft haptic on tap |
| Like heart | `animateFloatAsState` scale via `keyframes { 1.2f at 120 }` `tween(240)`; fills `Teal` |
| Compose sheet | `ModalBottomSheet` default slide-up (~300ms); scrim fades in parallel |
| New post insert | `LazyColumn` item `animateItemPlacement()`; new items `fadeIn(tween(220))` at top — never re-sort |
| Tab switch | content `Crossfade(tween(200))`; active tint instant |
| Image load | Coil `crossfade(220)` from `Surface1` placeholder |
| Connect/Follow | `Crossfade` label `tween(180)`; outline → filled |

```kotlin
// Like heart — the only spring-ish moment, kept gentle
val scale by animateFloatAsState(
    targetValue = if (liked) 1f else 1f,
    animationSpec = keyframes { durationMillis = 240; 1.2f at 120 },
    label = "likePulse",
)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft tick on post-type select, like, and connect. Use `HapticFeedbackConstants.CLOCK_TICK` for tab selection. Keep haptics sparse — Vero's ethos is calm.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The gradient "VERO" wordmark is drawn text with a `SrcAtop` gradient overlay; the compose ring is a gradient `Box` with a punched-out center.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Feed (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Discover (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Compose (center) | `plus.circle` | gradient ring + `Icons.Filled.Add` |
| Inbox (tab) | `envelope` | `Icons.Filled.Email` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Post-type · Photo | `photo` | `Icons.Outlined.Image` |
| Post-type · Video | `video` | `Icons.Outlined.Videocam` |
| Post-type · Link | `link` | `Icons.Outlined.Link` |
| Post-type · Music | `music.note` | `Icons.Outlined.MusicNote` |
| Post-type · Film | `film` | `Icons.Outlined.Movie` |
| Post-type · Book | `book.closed` | `Icons.Outlined.MenuBook` |
| Post-type · Place | `mappin.and.ellipse` | `Icons.Outlined.LocationOn` |
| Like | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Icons.Filled.Favorite` |
| Comment | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Share | `square.and.arrow.up` | `Icons.Outlined.IosShare` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Notifications | `bell` | `Icons.Filled.Notifications` |
| Rating star | `star.fill` | `Icons.Filled.Star` (tint `Amber`) |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the true-black canvas wants light-content system bars. Keep the system nav bar transparent so the gallery void extends edge-to-edge.
- **Font scaling**: `sp` honors the user's scale on titles, author names, captions, body. Pin layout-critical text (post-type labels, 10sp tab labels, star ratings, type chips) — derive from `dp` or fix `fontScale = 1f` for those nodes.
- **TalkBack**: label a post "{author}, {type} post, {timeAgo}, {caption}"; expose the 7-type selector as a single group with each cell `Modifier.semantics { role = Role.RadioButton; selected = ... }`; the like control toggles "Liked / Not liked"; the compose ring gets `contentDescription = "Create a post"`.
- **Touch targets**: Material guidance is 48.dp. Each post-type cell is ≥ 56.dp tall already; give the 19.dp action icons a 48.dp hit, the 22.dp tab icons a 48.dp hit.
- **Contrast**: `#FFFFFF` and `#9A9AA0` on `#000000` pass WCAG AA; teal `#00C2B8` on black passes for icons/large text — validate small teal text; `#1A1206` on `#E8B23A` passes for amber.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the like pulse (instant fill) and the post-type tint animation (instant); keep new-post fade subtle.
- **True-black is intentional**: the canvas is pure `#000000` for OLED — do **not** substitute a charcoal in dark mode; only inset cards use `#0E0E10`. Do **not** enable Material You `dynamicColorScheme()` — the single teal→blue gradient identity must hold regardless of wallpaper.
- **Chronological guarantee**: back the feed with a strictly time-sorted source; new items prepend with `animateItemPlacement` + `fadeIn` — never re-sort by engagement or inject "suggested" items.
