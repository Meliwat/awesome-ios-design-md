# Goodreads (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Goodreads' visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the book detail + star rating, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Goodreads' warm tan paper, literary brown chrome, amber five-star rating, cover-first shelves) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a SwiftUI sheet, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for cover art and avatars. No color extraction — Goodreads' palette is a fixed warm set, so Palette is not needed. Goodreads is light-mode-first (paper); a full dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/GoodreadsColors.kt
import androidx.compose.ui.graphics.Color

object GoodreadsColors {
    // Brand & interactive
    val Brown        = Color(0xFF382110)
    val BrownDeep    = Color(0xFF58371F)
    val Tan          = Color(0xFFF4F1EA)
    val ShelfGreen   = Color(0xFF409D69)
    val ShelfGreenPressed = Color(0xFF348355)
    val LinkTeal     = Color(0xFF00635D)
    val LinkTealDark = Color(0xFF6FB3AD)

    // Rating
    val Amber        = Color(0xFFE9A100) // load-bearing — five-star rating, light AND dark
    val StarRail     = Color(0xFFD8D2C4)
    val StarRailDark = Color(0xFF4A3F2C)

    // Canvas & surfaces (light)
    val Canvas        = Color(0xFFF4F1EA) // warm tan paper — NOT pure white
    val CardWhite     = Color(0xFFFFFFFF)
    val SurfaceSubtle = Color(0xFFEBE6DA)
    val Divider       = Color(0xFFDDD6C7)

    // Canvas & surfaces (dark)
    val DarkCanvas   = Color(0xFF161310) // warm ink-brown — NOT true black
    val DarkSurface1 = Color(0xFF211C16)
    val DarkSurface2 = Color(0xFF2C261D)
    val DarkDivider  = Color(0xFF3A3226)
    val BrownOnDark  = Color(0xFFC9883D) // brown reads muddy on dark → tan-gold accent

    // Text
    val TextPrimary     = Color(0xFF382110) // warm brown-black — NOT pure black
    val TextSecondary   = Color(0xFF6B5E47)
    val TextTertiary    = Color(0xFF988B6F)
    val DarkTextPrimary = Color(0xFFEDE6D8)
    val DarkTextSecondary = Color(0xFFB3A88F)

    // Semantic
    val Error = Color(0xFFD9534F)
}
```

Wire it into both schemes. Goodreads is light-first (paper); the dark scheme uses the signature `#161310` ink-brown, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val GoodreadsLight = lightColorScheme(
    primary        = GoodreadsColors.Brown,
    onPrimary      = GoodreadsColors.Tan,
    secondary      = GoodreadsColors.ShelfGreen,
    onSecondary    = Color(0xFFFFFFFF),
    background      = GoodreadsColors.Canvas,
    onBackground    = GoodreadsColors.TextPrimary,
    surface        = GoodreadsColors.CardWhite,
    onSurface      = GoodreadsColors.TextPrimary,
    surfaceVariant = GoodreadsColors.SurfaceSubtle,
    outline        = GoodreadsColors.Divider,
    error          = GoodreadsColors.Error,
)

private val GoodreadsDark = darkColorScheme(
    primary        = GoodreadsColors.BrownOnDark,
    onPrimary      = GoodreadsColors.DarkCanvas,
    secondary      = GoodreadsColors.ShelfGreen,
    onSecondary    = Color(0xFFFFFFFF),
    background      = GoodreadsColors.DarkCanvas,
    onBackground    = GoodreadsColors.DarkTextPrimary,
    surface        = GoodreadsColors.DarkSurface1,
    onSurface      = GoodreadsColors.DarkTextPrimary,
    surfaceVariant = GoodreadsColors.DarkSurface2,
    outline        = GoodreadsColors.DarkDivider,
    error          = GoodreadsColors.Error,
)

@Composable
fun GoodreadsTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) GoodreadsDark else GoodreadsLight,
    typography = GoodreadsTypography,
    content = content,
)
```

## 2. Typography (Material 3)

Goodreads pairs **Merriweather** (editorial: titles, synopses, reviews) with **Lato** (UI chrome). Drop the TTFs in `res/font/`. Serif for what the user *reads*; sans for what the user *acts on*. Body/review line-height ≈ 1.6.

```kotlin
// ui/theme/GoodreadsType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Merriweather = FontFamily(
    Font(R.font.merriweather_regular, FontWeight.Normal),
    Font(R.font.merriweather_bold,    FontWeight.Bold),
    Font(R.font.merriweather_black,   FontWeight.Black),
)
val Lato = FontFamily(
    Font(R.font.lato_regular, FontWeight.Normal),
    Font(R.font.lato_bold,    FontWeight.Bold),
    Font(R.font.lato_black,   FontWeight.Black),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object GoodreadsText {
    val ScreenTitle = TextStyle(Merriweather, fontWeight = FontWeight.Black,  fontSize = 30.sp, lineHeight = 36.sp, letterSpacing = (-0.3).sp)
    val BookTitle   = TextStyle(Merriweather, fontWeight = FontWeight.Black,  fontSize = 23.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Section     = TextStyle(Merriweather, fontWeight = FontWeight.Bold,   fontSize = 19.sp, lineHeight = 25.sp)
    val Subsection  = TextStyle(Merriweather, fontWeight = FontWeight.Bold,   fontSize = 17.sp, lineHeight = 23.sp)
    val Body        = TextStyle(Merriweather, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 24.sp) // ~1.6
    val Review      = TextStyle(Merriweather, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 20.sp)
    val Button      = TextStyle(Lato, fontWeight = FontWeight.Black,  fontSize = 17.sp, lineHeight = 17.sp, letterSpacing = 0.1.sp)
    val Label       = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 14.sp, lineHeight = 18.sp)
    val Meta        = TextStyle(Lato, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 18.sp)
    val Link        = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 13.sp, lineHeight = 18.sp)
    val Caption     = TextStyle(Lato, fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 16.sp)
    val StarCaption = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 12.sp, lineHeight = 14.sp)
    val Overline    = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Tab         = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
}

// Map onto Material 3 slots so stock components inherit the brand voice
val GoodreadsTypography = Typography(
    headlineLarge  = GoodreadsText.ScreenTitle,
    headlineMedium = GoodreadsText.BookTitle,
    titleMedium    = GoodreadsText.Section,
    bodyMedium     = GoodreadsText.Body,
    labelSmall     = GoodreadsText.Tab,
)
```

## 3. Signature Components

### Book Cover

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun BookCover(imageUrl: String?, width: Int = 104, dark: Boolean = false) {
    val w = width.dp
    val h = (width * 1.52f).dp
    Box(
        Modifier
            .size(w, h)
            .shadow(if (dark) 8.dp else 5.dp, RoundedCornerShape(3.dp),
                    spotColor = Color.Black.copy(alpha = if (dark) 0.55f else 0.18f))
            .clip(RoundedCornerShape(3.dp))
    ) {
        AsyncImage(
            model = imageUrl,
            contentDescription = null,
            modifier = Modifier.matchParentSize()
                .background(Brush.linearGradient(listOf(GoodreadsColors.BrownDeep, GoodreadsColors.Brown))),
            contentScale = ContentScale.Crop,
        )
        Box(Modifier.fillMaxHeight().width(6.dp).background(Color.Black.copy(alpha = 0.35f)))
    }
}
```

### Star Rating (display, fractional)

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Icon
import androidx.compose.ui.draw.clipToBounds

@Composable
fun StarRating(value: Float, size: Int = 15, dark: Boolean = false) {
    val rail = if (dark) GoodreadsColors.StarRailDark else GoodreadsColors.StarRail
    Row(horizontalArrangement = Arrangement.spacedBy(1.dp)) {
        repeat(5) { i ->
            val fill = (value - i).coerceIn(0f, 1f)
            Box(Modifier.size(size.dp)) {
                Icon(Icons.Filled.Star, null, tint = rail, modifier = Modifier.matchParentSize())
                Box(Modifier.fillMaxHeight().fillMaxWidth(fill).clipToBounds()) {
                    Icon(Icons.Filled.Star, null, tint = GoodreadsColors.Amber,
                         modifier = Modifier.size(size.dp))
                }
            }
        }
    }
}
```

### Interactive "Rate this Book"

```kotlin
import androidx.compose.animation.core.Spring
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.clickable
import androidx.compose.runtime.*
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.style.TextAlign

@Composable
fun RateThisBook(dark: Boolean = false, onRate: (Int) -> Unit = {}) {
    var rating by remember { mutableIntStateOf(0) }
    val haptics = LocalHapticFeedback.current
    Column(
        Modifier
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(6.dp))
            .background(if (dark) GoodreadsColors.DarkSurface1 else GoodreadsColors.CardWhite)
            .padding(14.dp)
            .fillMaxWidth(),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Text("RATE THIS BOOK", style = GoodreadsText.Overline,
             color = if (dark) GoodreadsColors.DarkTextSecondary else GoodreadsColors.TextSecondary,
             textAlign = TextAlign.Center)
        Spacer(Modifier.height(10.dp))
        Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            (1..5).forEach { i ->
                val s by animateFloatAsState(if (i == rating) 1.2f else 1f,
                    spring(dampingRatio = 0.55f, stiffness = Spring.StiffnessMedium), label = "starPop")
                Icon(
                    Icons.Filled.Star, null,
                    tint = if (i <= rating) GoodreadsColors.Amber
                           else if (dark) GoodreadsColors.StarRailDark else GoodreadsColors.StarRail,
                    modifier = Modifier.size(28.dp).scale(if (i == rating) s else 1f).clickable {
                        rating = i
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                        onRate(i)
                    },
                )
            }
        }
    }
}
```

### Want-to-Read CTA Block

```kotlin
import androidx.compose.foundation.border
import androidx.compose.material.icons.filled.Check
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults

@Composable
fun ShelfCTA() {
    var shelf by remember { mutableStateOf<String?>(null) }
    val haptics = LocalHapticFeedback.current
    Column(Modifier.padding(horizontal = 16.dp), verticalArrangement = Arrangement.spacedBy(8.dp)) {
        Button(
            onClick = { shelf = shelf ?: "Want to Read"; haptics.performHapticFeedback(HapticFeedbackType.LongPress) },
            shape = RoundedCornerShape(4.dp),
            colors = ButtonDefaults.buttonColors(containerColor = GoodreadsColors.ShelfGreen),
            modifier = Modifier.fillMaxWidth().height(44.dp),
        ) {
            if (shelf != null) { Icon(Icons.Filled.Check, null, tint = Color.White, modifier = Modifier.size(16.dp)); Spacer(Modifier.width(8.dp)) }
            Text(shelf ?: "Want to Read", style = GoodreadsText.Button, color = Color.White)
        }
        Row(
            Modifier.fillMaxWidth().border(1.dp, GoodreadsColors.Divider, RoundedCornerShape(4.dp))
                .clip(RoundedCornerShape(4.dp)),
        ) {
            listOf("★ Rate", "Review", "Shelve ▾").forEachIndexed { i, seg ->
                Box(Modifier.weight(1f).height(38.dp), contentAlignment = Alignment.Center) {
                    Text(seg, style = GoodreadsText.StarCaption,
                         color = if (seg == "★ Rate") GoodreadsColors.Amber else GoodreadsColors.TextSecondary)
                }
                if (i < 2) Box(Modifier.fillMaxHeight().width(1.dp).background(GoodreadsColors.Divider))
            }
        }
    }
}
```

### Shelf Status Pill

```kotlin
@Composable
fun ShelfPill(kind: String) { // "read" | "current" | "want"
    data class P(val bg: Color, val fg: Color, val border: Color?)
    val p = when (kind) {
        "read"    -> P(GoodreadsColors.Brown, GoodreadsColors.Tan, null)
        "current" -> P(GoodreadsColors.ShelfGreen.copy(alpha = 0.18f), GoodreadsColors.ShelfGreen, GoodreadsColors.ShelfGreen.copy(alpha = 0.4f))
        else      -> P(GoodreadsColors.SurfaceSubtle, GoodreadsColors.TextSecondary, GoodreadsColors.Divider)
    }
    val label = when (kind) { "read" -> "Read"; "current" -> "Currently Reading"; else -> "Want to Read" }
    Box(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(p.bg)
            .then(if (p.border != null) Modifier.border(1.dp, p.border, RoundedCornerShape(999.dp)) else Modifier)
            .padding(horizontal = 14.dp, vertical = 7.dp),
    ) { Text(label, style = GoodreadsText.Link, color = p.fg) }
}
```

## 4. Navigation

Goodreads has a 5-tab bottom strip and a serif large title that collapses on scroll. On Android, model the strip as a `NavigationBar` (no tint pill — active is glyph fill + color) and the detail screen with a `LargeTopAppBar`.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun GoodreadsBottomBar(selected: Int, onSelect: (Int) -> Unit, dark: Boolean = false) {
    NavigationBar(
        containerColor = if (dark) GoodreadsColors.DarkCanvas else GoodreadsColors.Canvas,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home" to Icons.Filled.Home,
            "My Books" to Icons.Filled.MenuBook,
            "Browse" to Icons.Filled.Search,
            "Updates" to Icons.Filled.Notifications,
            "Profile" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(23.dp)) },
                label = { Text(label, style = GoodreadsText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = if (dark) GoodreadsColors.BrownOnDark else GoodreadsColors.Brown,
                    selectedTextColor = if (dark) GoodreadsColors.BrownOnDark else GoodreadsColors.Brown,
                    unselectedIconColor = if (dark) GoodreadsColors.DarkTextSecondary else GoodreadsColors.TextTertiary,
                    unselectedTextColor = if (dark) GoodreadsColors.DarkTextSecondary else GoodreadsColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Goodreads has none
                ),
            )
        }
    }
}
```

The shelf picker is a `ModalBottomSheet` (12.dp top radius): exclusive shelves as a radio group (Read / Currently Reading / Want to Read), custom shelves as a checkbox list below a `Divider`, a green "Done" `Button` pinned at the bottom.

## 5. Motion

Goodreads motion is gentle and library-quiet — 180–300ms.

| Moment | Compose recipe |
|--------|----------------|
| Shelve action | green `Button` `animateFloatAsState` scale 1→0.96→1 `spring(0.6)`; success haptic |
| Star commit | per-star `animateFloatAsState` 1→1.2→1 `spring(dampingRatio=0.55f)`, staggered fill; `HapticFeedbackType.LongPress` |
| "…more" expander | `AnimatedVisibility(expandVertically(tween(220)) + fadeIn())` |
| Cover → detail | shared-element via `Modifier.sharedElement` (Compose 1.7) or scale `tween(300)` |
| Reading Challenge ring | `Canvas` arc sweep `animateFloatAsState(target, tween(700))` on first composition |
| Pull to refresh | `PullToRefreshContainer` with brown indicator |
| Tab switch | instant; selected glyph crossfades `tween(120)` |

```kotlin
// Star bounce — the canonical Goodreads rating motion
val pop by animateFloatAsState(
    targetValue = if (i == rating) 1.2f else 1f,
    animationSpec = spring(dampingRatio = 0.55f, stiffness = Spring.StiffnessMedium),
    label = "starPop",
)
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the medium impact on star commit and the soft confirm on shelve. Progress auto-save is silent — show a "Saved" `Snackbar` only on a manual progress update or an error.

## 6. Icons

Goodreads' iconography is conventional; the closest first-party set is `androidx.compose.material:material-icons-extended`. The five-star rating uses `Icons.Filled.Star` (clip a copy for fractional fill); avatars fall back to initials on a brown gradient.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| My Books (tab) | `books.vertical` | `Icons.Filled.MenuBook` |
| Browse (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Updates (tab) | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Star (rating) | `star.fill` | `Icons.Filled.Star` |
| Half star | `star.leadinghalf.filled` | `Icons.AutoMirrored.Filled.StarHalf` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Add to network | `person.2` | `Icons.Filled.GroupAdd` |
| Shelve chevron | `chevron.down` | `Icons.Filled.ExpandMore` |
| Want to Read check | `checkmark` | `Icons.Filled.Check` |
| Progress update | `arrow.triangle.2.circlepath` | `Icons.Filled.Sync` |
| Like (review) | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Icons.Filled.Favorite` |
| Comment | `text.bubble` | `Icons.AutoMirrored.Filled.Comment` |
| Scan ISBN | `barcode.viewfinder` | `Icons.Filled.QrCodeScanner` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the tan light canvas wants dark-content system bars (light-content in dark mode). The reviews feed and cover grids are long — use `LazyVerticalGrid` / `LazyColumn`.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, book titles, body, review prose (reading content must scale). Pin layout-sensitive text (10sp tab labels, overlines, star captions, shelf pills) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Reading content**: never collapse the 1.6 line-height on Body/Review — long reviews must stay comfortable at large font scales.
- **TalkBack**: announce ratings as "Rated 4 of 5 stars, 3,284,991 ratings"; the interactive rate row uses `Modifier.semantics { stateDescription = "$rating of 5 stars"; role = Role.... }` and a custom set action; give every `BookCover` a `contentDescription` of "{title} by {author}".
- **Touch targets**: Material guidance is 48.dp. Give the 28.dp interactive stars a 48.dp hit area via padding; sidebar/list rows are full-row tappable; primary CTA ≥ 44.dp.
- **Contrast**: `#382110` on `#F4F1EA` passes WCAG AA. Amber `#E9A100` is a graphical rating; always pair it with the numeric average so meaning never rests on color alone — never grey-fill a fractional star (clip the amber instead).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the star pop and the ring sweep — substitute a plain `Crossfade`; keep the fill state (it conveys the rating).
- **Dark mode**: invert via the `Dark*` palette — `#161310`, NOT true black; `#382110` text becomes `#EDE6D8`; brand brown becomes tan-gold `#C9883D` for active tabs; link teal lightens to `#6FB3AD`. Cover shadows deepen to 0.55 alpha so jackets still detach. Do **not** enable Material You `dynamicColorScheme()` — Goodreads' paper-and-ink identity and the fixed amber rating must hold regardless of wallpaper.
