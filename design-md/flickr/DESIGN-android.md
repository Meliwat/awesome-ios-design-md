# Flickr (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Flickr's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the justified mosaic (a custom `Layout`), the photo detail page, the EXIF table, and the favorite-star animation with haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Flickr's near-black gallery canvas, justified mosaic, EXIF table, twin Pink/Blue dots, five-point favorite star) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a custom `Layout` for the row-packing mosaic, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for photos. Flickr is dark-first for gallery viewing; a full light scheme is provided. No Material You dynamic color — Flickr's twin-dot brand must hold regardless of wallpaper.

## 1. Color Tokens

```kotlin
// ui/theme/FlickrColors.kt
import androidx.compose.ui.graphics.Color

object FlickrColors {
    // Canvas & Surfaces (Dark — default for gallery viewing)
    val Canvas    = Color(0xFF0C0D0E) // near-black so photos dominate
    val Surface1  = Color(0xFF1A1B1D)
    val Surface2  = Color(0xFF232427)
    val Divider   = Color(0xFF2A2C30)

    // Canvas & Surfaces (Light)
    val CanvasLight    = Color(0xFFFFFFFF)
    val SurfaceLight   = Color(0xFFF5F6F7)
    val SurfacePressed = Color(0xFFEAECEF)
    val DividerLight   = Color(0xFFE2E4E8)

    // Text
    val TextPrimary     = Color(0xFFFFFFFF)
    val TextSecondary   = Color(0xFFB0B3B8)
    val TextTertiary    = Color(0xFF6E7176)
    val TextPrimaryLt   = Color(0xFF1C1F23)
    val TextSecondaryLt = Color(0xFF6B7077)

    // Brand (twin dots)
    val Pink        = Color(0xFFFF0084)
    val PinkPressed = Color(0xFFD60070)
    val Blue        = Color(0xFF0063DC)
    val BluePressed = Color(0xFF0052B8)

    // Accent & semantic
    val ProGold = Color(0xFFFFB200)
    val Success = Color(0xFF00C781)
    val Error   = Color(0xFFFF3B5C)

    // Photo scrim
    val Scrim   = Color(0x73000000) // rgba(0,0,0,0.45)
}
```

Wire it into both schemes. Flickr is dark-first (gallery wall); the light scheme uses pure white.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val FlickrDark = darkColorScheme(
    primary        = FlickrColors.Pink,
    onPrimary      = Color.White,
    secondary      = FlickrColors.Blue,
    background     = FlickrColors.Canvas,
    onBackground   = FlickrColors.TextPrimary,
    surface        = FlickrColors.Surface1,
    onSurface      = FlickrColors.TextPrimary,
    surfaceVariant = FlickrColors.Surface2,
    outline        = FlickrColors.Divider,
    error          = FlickrColors.Error,
)

private val FlickrLight = lightColorScheme(
    primary        = FlickrColors.Pink,
    onPrimary      = Color.White,
    secondary      = FlickrColors.Blue,
    background     = FlickrColors.CanvasLight,
    onBackground   = FlickrColors.TextPrimaryLt,
    surface        = FlickrColors.SurfaceLight,
    onSurface      = FlickrColors.TextPrimaryLt,
    surfaceVariant = FlickrColors.SurfacePressed,
    outline        = FlickrColors.DividerLight,
    error          = FlickrColors.Error,
)

@Composable
fun FlickrTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) FlickrDark else FlickrLight,
    typography = FlickrTypography,
    content = content,
)
```

## 2. Typography

Flickr pairs an editorial serif (**Proza Libre**, titles/headers) with a UI sans (**Inter**) and a monospace EXIF readout (**IBM Plex Mono**). All SIL OFL — drop the TTFs in `res/font/`. Serif for curation, sans for function; never set EXIF or tab labels in the serif.

```kotlin
// ui/theme/FlickrType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val ProzaLibre = FontFamily(
    Font(R.font.proza_libre_semibold, FontWeight.SemiBold),
    Font(R.font.proza_libre_bold,     FontWeight.Bold),
)
val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
    Font(R.font.inter_bold,     FontWeight.Bold),
)
val IBMPlexMono = FontFamily(
    Font(R.font.ibm_plex_mono_semibold, FontWeight.SemiBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt -> sp 1:1)
object FlickrText {
    val ScreenTitle = TextStyle(ProzaLibre, fontWeight = FontWeight.Bold,     fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.4).sp)
    val SectionHead = TextStyle(ProzaLibre, fontWeight = FontWeight.Bold,     fontSize = 26.sp, lineHeight = 33.sp, letterSpacing = (-0.3).sp)
    val NavTitle    = TextStyle(ProzaLibre, fontWeight = FontWeight.SemiBold, fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val PhotoTitle  = TextStyle(ProzaLibre, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 15.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val FaveCount   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 11.sp)
    val ExifKey     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 17.sp)
    val TagChip     = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 12.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val ExifValue   = TextStyle(IBMPlexMono, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 16.sp, letterSpacing = 0.3.sp)
}

val FlickrTypography = Typography(
    headlineLarge = FlickrText.ScreenTitle,
    headlineMedium = FlickrText.SectionHead,
    titleLarge    = FlickrText.NavTitle,
    titleMedium   = FlickrText.PhotoTitle,
    bodyMedium    = FlickrText.Body,
    labelSmall    = FlickrText.Tab,
)
```

## 3. Signature Components

### Justified Photo Mosaic (custom Layout)

The heritage row-packing layout. A custom `Layout` measures each photo by its aspect ratio and scales rows to fill width at a target height.

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.verticalScroll
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

data class Photo(val id: String, val aspect: Float, val gradient: List<Color>, val faves: Int?)

private const val GUTTER = 3   // dp
private const val TARGET_ROW = 115 // dp

@Composable
fun JustifiedMosaic(photos: List<Photo>, modifier: Modifier = Modifier) {
    BoxWithConstraints(modifier.fillMaxSize().background(FlickrColors.Canvas)) {
        val widthDp = maxWidth.value
        // Pack rows
        val rows = remember(photos, widthDp) {
            val out = mutableListOf<MutableList<Photo>>()
            var cur = mutableListOf<Photo>(); var sum = 0f
            photos.forEach { p ->
                cur.add(p); sum += p.aspect
                val h = (widthDp - GUTTER * (cur.size - 1)) / sum
                if (h <= TARGET_ROW) { out.add(cur); cur = mutableListOf(); sum = 0f }
            }
            if (cur.isNotEmpty()) out.add(cur)
            out
        }
        Column(Modifier.verticalScroll(rememberScrollState())) {
            rows.forEach { row ->
                val sum = row.sumOf { it.aspect.toDouble() }.toFloat()
                val h = ((widthDp - GUTTER * (row.size - 1)) / sum).dp
                Row(Modifier.fillMaxWidth().padding(bottom = GUTTER.dp)) {
                    row.forEachIndexed { i, p ->
                        Box(
                            Modifier
                                .height(h)
                                .weight(p.aspect)
                                .padding(end = if (i < row.size - 1) GUTTER.dp else 0.dp)
                        ) { MosaicTile(p) }
                    }
                }
            }
        }
    }
}

@Composable
fun MosaicTile(photo: Photo) {
    Box(Modifier.fillMaxSize()) {
        Box(
            Modifier
                .fillMaxSize()
                .background(Brush.linearGradient(photo.gradient))
        )
        if (photo.faves != null) {
            Box(
                Modifier
                    .align(Alignment.BottomCenter)
                    .fillMaxWidth()
                    .fillMaxHeight(0.4f)
                    .background(Brush.verticalGradient(listOf(Color.Transparent, FlickrColors.Scrim)))
            )
            Row(
                Modifier.align(Alignment.BottomStart).padding(8.dp),
                horizontalArrangement = Arrangement.spacedBy(4.dp),
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Icon(Icons.Filled.Star, null, tint = Color.White, modifier = Modifier.size(13.dp))
                Text("${photo.faves}", style = FlickrText.FaveCount, color = Color.White)
            }
        }
    }
}
```

### Favorite Star (the like primitive)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Star
import androidx.compose.material.icons.outlined.StarBorder
import androidx.compose.runtime.*
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun FavoriteStar(initial: Boolean = false, count: Int) {
    var faved by remember { mutableStateOf(initial) }
    val haptics = LocalHapticFeedback.current
    val scale = remember { Animatable(1f) }

    LaunchedEffect(faved) {
        if (faved) {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // soft impact analog
            scale.animateTo(1.25f, spring(dampingRatio = 0.6f, stiffness = Spring.StiffnessMediumLow))
            scale.animateTo(1.0f,  spring(dampingRatio = 0.6f, stiffness = Spring.StiffnessMediumLow))
        }
    }

    Icon(
        imageVector = if (faved) Icons.Filled.Star else Icons.Outlined.StarBorder,
        contentDescription = if (faved) "Remove favorite" else "Favorite",
        tint = if (faved) FlickrColors.Pink else FlickrColors.TextPrimary,
        modifier = Modifier
            .size(44.dp)
            .padding(11.dp)
            .scale(scale.value)
            .clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) { faved = !faved },
    )
}
```

### Photo Detail Page

```kotlin
@Composable
fun PhotoDetail(photo: Photo) {
    Column(
        Modifier
            .fillMaxSize()
            .background(FlickrColors.Canvas)
            .verticalScroll(rememberScrollState())
    ) {
        Box(
            Modifier
                .fillMaxWidth()
                .aspectRatio(photo.aspect)
                .background(Brush.linearGradient(photo.gradient))
        )
        Text("Aurora over Vestrahorn", style = FlickrText.PhotoTitle, color = FlickrColors.TextPrimary,
            modifier = Modifier.padding(start = 16.dp, end = 16.dp, top = 16.dp))
        Row(
            Modifier.fillMaxWidth().padding(start = 16.dp, end = 16.dp, top = 12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Box(Modifier.size(30.dp).clip(CircleShape).background(Brush.linearGradient(listOf(FlickrColors.Pink, FlickrColors.Blue))))
            Column(Modifier.weight(1f)) {
                Text("Sofia Marent", style = FlickrText.Meta.copy(fontWeight = FontWeight.SemiBold), color = FlickrColors.TextPrimary)
                Text("312 faves · 2d ago", style = FlickrText.Caption, color = FlickrColors.TextSecondary)
            }
            FollowPill()
        }
        PhotoActionBar()
        HorizontalDivider(color = FlickrColors.Divider, thickness = 0.5.dp)
        Text("Shot on a 30-second exposure just after the storm cleared the ridge.",
            style = FlickrText.Body, color = FlickrColors.TextPrimary, modifier = Modifier.padding(16.dp))
        ExifTable()
    }
}

@Composable
fun FollowPill() {
    var following by remember { mutableStateOf(false) }
    val shape = RoundedCornerShape(999.dp)
    Box(
        Modifier
            .clip(shape)
            .then(if (following) Modifier.border(1.dp, FlickrColors.TextSecondary, shape) else Modifier.background(FlickrColors.Pink))
            .clickable { following = !following }
            .padding(horizontal = 18.dp, vertical = 8.dp)
    ) {
        Text(if (following) "Following" else "Follow",
            style = FlickrText.Button.copy(fontSize = 13.sp),
            color = if (following) FlickrColors.TextSecondary else Color.White)
    }
}
```

### Photo Action Bar

```kotlin
import androidx.compose.material.icons.outlined.*

@Composable
fun PhotoActionBar() {
    var faved by remember { mutableStateOf(false) }
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 12.dp),
        horizontalArrangement = Arrangement.spacedBy(22.dp),
    ) {
        ActionItem(if (faved) Icons.Filled.Star else Icons.Outlined.StarBorder, "Fave",
            if (faved) FlickrColors.Pink else FlickrColors.TextSecondary) { faved = !faved }
        ActionItem(Icons.Outlined.ChatBubbleOutline, "Comment", FlickrColors.TextSecondary) {}
        ActionItem(Icons.Outlined.IosShare,          "Share",   FlickrColors.TextSecondary) {}
        ActionItem(Icons.Outlined.LibraryAdd,        "Add",     FlickrColors.TextSecondary) {}
    }
}

@Composable
private fun ActionItem(icon: androidx.compose.ui.graphics.vector.ImageVector, label: String, tint: Color, onClick: () -> Unit) {
    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(6.dp),
        modifier = Modifier.clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) { onClick() },
    ) {
        Icon(icon, label, tint = tint, modifier = Modifier.size(18.dp))
        Text(label, style = FlickrText.Caption, color = tint)
    }
}
```

### EXIF / Camera Detail Table

```kotlin
@Composable
fun ExifTable() {
    data class Row(val k: String, val v: String, val tappable: Boolean)
    val rows = listOf(
        Row("Camera", "Sony ILCE-7M4", true),
        Row("Lens", "FE 35mm F1.4 GM", true),
        Row("Exposure", "1/250 sec", false),
        Row("Aperture", "f/2.8", false),
        Row("Focal length", "35 mm", false),
        Row("ISO", "100", false),
        Row("Date taken", "Jan 14, 2026", false),
    )
    Column(
        Modifier
            .padding(16.dp)
            .clip(RoundedCornerShape(12.dp))
            .background(FlickrColors.Surface1)
    ) {
        Text("CAMERA DETAILS",
            style = FlickrText.Tab.copy(fontWeight = FontWeight.Bold, letterSpacing = 0.8.sp),
            color = FlickrColors.TextTertiary,
            modifier = Modifier.padding(start = 16.dp, end = 16.dp, top = 16.dp, bottom = 10.dp))
        rows.forEachIndexed { i, r ->
            Row(
                Modifier
                    .fillMaxWidth()
                    .then(if (r.tappable) Modifier.clickable { /* search this gear */ } else Modifier)
                    .padding(horizontal = 16.dp, vertical = 7.dp),
                horizontalArrangement = Arrangement.SpaceBetween,
            ) {
                Text(r.k, style = FlickrText.ExifKey, color = FlickrColors.TextSecondary)
                Text(r.v, style = FlickrText.ExifValue, color = FlickrColors.TextPrimary)
            }
            if (i < rows.size - 1) HorizontalDivider(color = FlickrColors.Divider, thickness = 0.5.dp, modifier = Modifier.padding(start = 16.dp))
        }
    }
}
```

## 4. Navigation

Flickr has minimal chrome: a bottom tab strip (Feed / Search / Camera / Notify / You) with an emphasized center camera. There is no tint pill — active is just white.

```kotlin
@Composable
fun FlickrBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = FlickrColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Feed"   to Icons.Filled.Home,
            "Search" to Icons.Filled.Search,
            "Camera" to Icons.Filled.PhotoCamera,   // center, emphasized
            "Notify" to Icons.Filled.Notifications,
            "You"    to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            val isCamera = i == 2
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    Icon(icon, contentDescription = label,
                        tint = if (isCamera) FlickrColors.Pink else LocalContentColor.current,
                        modifier = Modifier.size(if (isCamera) 27.dp else 23.dp))
                },
                label = if (isCamera) null else { { Text(label, style = FlickrText.Tab) } },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = FlickrColors.TextPrimary,
                    selectedTextColor = FlickrColors.TextPrimary,
                    unselectedIconColor = FlickrColors.TextTertiary,
                    unselectedTextColor = FlickrColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Flickr has none
                ),
            )
        }
    }
}
```

The top app bar uses a `LargeTopAppBar` whose collapsed title is `FlickrText.NavTitle` (Proza Libre 22sp). Photo detail uses a small top bar with a back arrow and an overflow (Report, Download, Copy link).

## 5. Motion

Flickr motion is photographic — quiet, never competing with the image.

| Moment | Compose recipe |
|--------|----------------|
| Fave star tap | `Animatable` `animateTo(1.25f)` then `1.0f` with `spring(dampingRatio = 0.6f)`; soft haptic; optional `#FF0084` particle overlay |
| Grid -> detail | shared-element via `androidx.compose.animation` `SharedTransitionLayout` / `sharedBounds`, 320ms ease-out expand of the tile into the full-width image |
| Lightbox open | photo `scale`/`offset` from grid position to full screen `tween(300, easing = FastOutSlowIn)`; backdrop animates to `Scrim` (0x73000000) |
| New justified rows | `AnimatedVisibility` `fadeIn(tween(200))` as rows pack during scroll |
| Photo detail parallax | image `Modifier.offset { IntOffset(0, (scrollState.value * 0.5f).toInt()) }` |
| Tab change | instant content swap (gallery feel — no cross-fade) |
| Upload progress | determinate `CircularProgressIndicator` tinted `Pink`; success -> `Success` checkmark `scaleIn(tween(200))` |

```kotlin
// Fave burst — the canonical Flickr motion
LaunchedEffect(faved) {
    if (faved) {
        scale.animateTo(1.25f, spring(dampingRatio = 0.6f, stiffness = Spring.StiffnessMediumLow))
        scale.animateTo(1.0f,  spring(dampingRatio = 0.6f, stiffness = Spring.StiffnessMediumLow))
    }
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft fave impact; `HapticFeedbackConstants.CONFIRM` (via `LocalView`) for add-to-album; a light `CLOCK_TICK` for tab change. Upload completion is silent visually except the green checkmark.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The favorite primitive is `Icons.Filled.Star` / `Icons.Outlined.StarBorder` — a five-point star, never a heart.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Feed (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Camera (tab, center) | `camera` | `Icons.Filled.PhotoCamera` (tint `#FF0084`) |
| Notify (tab) | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| You (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Favorite (inactive) | `star` | `Icons.Outlined.StarBorder` |
| Favorite (active) | `star.fill` | `Icons.Filled.Star` (`#FF0084`) |
| Comment | `bubble.right` | `Icons.Outlined.ChatBubbleOutline` |
| Share | `square.and.arrow.up` | `Icons.Outlined.IosShare` |
| Add to album | `rectangle.stack.badge.plus` | `Icons.Outlined.LibraryAdd` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Download | `arrow.down.circle` | `Icons.Outlined.FileDownload` |
| Pro badge | `star.circle.fill` | `Icons.Filled.Stars` (`#FFB200`) |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; `SharedTransitionLayout` needs a recent Compose animation artifact). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. Coil `2.6+` for photos.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the near-black canvas wants light-content system bars. The photo grid is full-bleed (draw under the status bar); text content keeps 16dp insets; the tab bar respects the navigation bar inset.
- **Font scaling**: `sp` honors the user's font scale — keep it on photo titles, body, comments, metadata. Pin layout-sensitive text (EXIF values, fave-count overlays, 10sp tab labels) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` so the justified grid math stays stable.
- **TalkBack**: label tiles "Photo by {author}, {n} favorites"; the fave control as a toggle (`Modifier.semantics { role = Role.Switch; stateDescription = if (faved) "Favorited" else "Not favorited" }`); EXIF rows "{key}: {value}"; expose Camera/Lens "see more shot with this gear" via `Modifier.semantics { customActions = ... }`.
- **Touch targets**: Material guidance is 48.dp — the 22dp fave star and 18dp action icons get 48dp hit areas via padding; grid tiles are large enough; EXIF Camera/Lens rows are full-row tappable.
- **Contrast**: `#FFFFFF` on `#0C0D0E` is maximal; `#B0B3B8` secondary passes AA on canvas; the fave-count overlay always sits on the `Scrim` so it stays legible over any photo.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the fave spring/burst and the grid->detail shared transition — substitute a `Crossfade`; keep selection/favorited state.
- **Dark mode**: Flickr is dark-first; the light scheme flips canvas to `#FFFFFF`, text to `#1C1F23`, EXIF container to `#F5F6F7`. **Photos never dim** — only chrome and EXIF tables invert. Do **not** enable `dynamicColorScheme()` — the twin Pink/Blue brand must hold regardless of wallpaper.
