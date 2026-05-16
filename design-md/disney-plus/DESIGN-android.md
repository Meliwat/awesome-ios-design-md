# Disney+ (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Disney+'s visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the starfield billboard, the signature brand-portal tiles, the tile glow+scale focus language, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Disney+'s deep space-navy, starfield billboard, brand-portal tiles, blue focus glow, 16:9 cards) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, an `ExoPlayer`/`Media3` trailer instead of `AVPlayer`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+`, and AndroidX Media3 (`androidx.media3:media3-exoplayer`) for the auto-trailer.

## 1. Color Tokens

```kotlin
// ui/theme/DisneyColors.kt
import androidx.compose.ui.graphics.Color

object DisneyColors {
    // Canvas & Surfaces
    val Canvas   = Color(0xFF0A0E2A)
    val Surface1 = Color(0xFF12152E)
    val Surface2 = Color(0xFF1A1F3D)
    val Divider  = Color(0xFF2A3050)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFA0A6C0)
    val TextTertiary  = Color(0xFF5A6080)

    // Brand
    val Blue        = Color(0xFF0063E5)
    val GlowBlue    = Color(0xFF1A75FF)
    val BluePressed = Color(0xFF0052BD)
    val LiveRed     = Color(0xFFE5484D)

    val FocusGlow   = Color(0xFF1A75FF).copy(alpha = 0.30f)
}
```

Wire it into a Material 3 `darkColorScheme`. Disney+ is dark-only; do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val DisneyScheme = darkColorScheme(
    primary        = DisneyColors.Blue,
    onPrimary      = Color.White,
    background      = DisneyColors.Canvas,
    onBackground    = DisneyColors.TextPrimary,
    surface         = DisneyColors.Surface1,
    onSurface       = DisneyColors.TextPrimary,
    surfaceVariant  = DisneyColors.Surface2,
    outline         = DisneyColors.Divider,
    error           = DisneyColors.LiveRed,
)

@Composable
fun DisneyTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = DisneyScheme, typography = DisneyTypography, content = content)
```

## 2. Typography

Disney's product sans is an Avenir-family humanist. Avenir is not a system font on Android — bundle the brand/Avenir TTFs in `res/font/` (lowercase, snake_case), or fall back to the bundled Inter, then the system font (Roboto).

```kotlin
// ui/theme/DisneyType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val DisneySans = FontFamily(
    Font(R.font.avenir_next_regular,  FontWeight.Normal),   // 400
    Font(R.font.avenir_next_demibold, FontWeight.SemiBold), // 600
    Font(R.font.avenir_next_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object DisneyText {
    val Billboard   = TextStyle(DisneySans, fontWeight = FontWeight.Bold,     fontSize = 30.sp, lineHeight = 33.sp, letterSpacing = (-0.3).sp)
    val DetailTitle = TextStyle(DisneySans, fontWeight = FontWeight.Bold,     fontSize = 26.sp, lineHeight = 30.sp, letterSpacing = (-0.2).sp)
    val Section     = TextStyle(DisneySans, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.1).sp)
    val RowHeader   = TextStyle(DisneySans, fontWeight = FontWeight.Bold,     fontSize = 18.sp, lineHeight = 22.sp)
    val CardTitle   = TextStyle(DisneySans, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 18.sp)
    val Synopsis    = TextStyle(DisneySans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 23.sp)
    val MetaStrip   = TextStyle(DisneySans, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 17.sp, letterSpacing = 0.3.sp)
    val Subtitle    = TextStyle(DisneySans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val Meta        = TextStyle(DisneySans, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Badge       = TextStyle(DisneySans, fontWeight = FontWeight.Bold,     fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 1.0.sp)
    val Button      = TextStyle(DisneySans, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = 0.3.sp)
    val ButtonSec   = TextStyle(DisneySans, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 18.sp)
    val Tab         = TextStyle(DisneySans, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val DisneyTypography = Typography(
    headlineLarge = DisneyText.Billboard,
    headlineSmall = DisneyText.Section,
    titleMedium   = DisneyText.CardTitle,
    bodyMedium    = DisneyText.Synopsis,
    labelSmall    = DisneyText.Tab,
)
```

## 3. Signature Components

### Starfield + Billboard with Auto-Trailer

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import kotlin.random.Random

@Composable
fun Starfield(count: Int = 90, modifier: Modifier = Modifier) {
    val stars = remember {
        List(count) { Triple(Random.nextFloat(), Random.nextFloat(), Random.nextFloat() * 0.5f) }
    }
    Canvas(modifier.fillMaxSize()) {
        stars.forEach { (x, y, a) ->
            drawCircle(
                color = Color.White.copy(alpha = a),
                radius = (1f + Random(x.hashCode()).nextFloat()) ,
                center = Offset(x * size.width, y * size.height),
            )
        }
    }
}

@Composable
fun HeroBillboard(
    keyArtUrl: String,
    logoArtUrl: String,
    metadata: String,                   // "Action · PG-13 · 2024 · 2h 11m"
    modifier: Modifier = Modifier,
) {
    var trailer by remember { mutableStateOf(false) }
    LaunchedEffect(Unit) { kotlinx.coroutines.delay(3000); trailer = true }

    BoxWithConstraints(
        modifier
            .fillMaxWidth()
            .height((LocalConfiguration.current.screenHeightDp * 0.62f).dp)
            .background(DisneyColors.Canvas),
    ) {
        AsyncImage(model = keyArtUrl, contentDescription = null,
            modifier = Modifier.matchParentSize(), contentScale = ContentScale.Crop)
        Starfield(modifier = Modifier.matchParentSize())
        Box(
            Modifier
                .matchParentSize()
                .background(Brush.verticalGradient(listOf(Color.Transparent, Color.Transparent, DisneyColors.Canvas))),
        )
        // When `trailer`, overlay a muted looping Media3 PlayerView (see §4)

        Column(
            Modifier.fillMaxSize().padding(bottom = 24.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.Bottom,
        ) {
            AsyncImage(model = logoArtUrl, contentDescription = null,
                modifier = Modifier.height(110.dp).widthIn(max = 240.dp), contentScale = ContentScale.Fit)
            Spacer(Modifier.height(14.dp))
            Text(metadata, style = DisneyText.MetaStrip, color = DisneyColors.TextSecondary)
            Spacer(Modifier.height(14.dp))
            Row(Modifier.padding(horizontal = 16.dp), horizontalArrangement = Arrangement.spacedBy(12.dp)) {
                DPPlayButton("Play", Modifier.weight(1f)) { }
                DPSecondaryButton(Icons.Filled.Add, "Watchlist", Modifier.weight(1f)) { }
            }
        }
    }
}
```

### Primary / Secondary Buttons

```kotlin
@Composable
fun DPPlayButton(label: String, modifier: Modifier = Modifier, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, spring(dampingRatio = 0.8f), label = "play")
    val haptics = LocalHapticFeedback.current
    Row(
        modifier
            .height(48.dp)
            .scale(scale)
            .shadow(22.dp, RoundedCornerShape(8.dp), spotColor = DisneyColors.GlowBlue.copy(alpha = 0.30f))
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) Color(0xFFE8EAF2) else Color.White)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium
                onClick()
            },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.Center,
    ) {
        Icon(Icons.Filled.PlayArrow, null, tint = DisneyColors.Canvas, modifier = Modifier.size(20.dp))
        Spacer(Modifier.width(8.dp))
        Text(label, style = DisneyText.Button, color = DisneyColors.Canvas)
    }
}

@Composable
fun DPSecondaryButton(icon: androidx.compose.ui.graphics.vector.ImageVector, label: String, modifier: Modifier = Modifier, onClick: () -> Unit) {
    Row(
        modifier
            .height(48.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(Color.White.copy(alpha = 0.12f))
            .border(1.dp, Color.White.copy(alpha = 0.24f), RoundedCornerShape(8.dp))
            .clickable(onClick = onClick),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.Center,
    ) {
        Icon(icon, null, tint = Color.White, modifier = Modifier.size(20.dp))
        Spacer(Modifier.width(8.dp))
        Text(label, style = DisneyText.ButtonSec, color = Color.White)
    }
}
```

### Brand-Portal Tile (Signature) + the Focus Glow Language

```kotlin
@Composable
fun BrandPortalTile(
    logoUrl: String,
    gradient: List<Color>,              // brand-tinted
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val focused by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (focused) 1.04f else 1f, tween(180, easing = EaseOut), label = "portal")

    Box(
        modifier
            .size(160.dp, 96.dp)
            .scale(scale)
            .shadow(if (focused) 24.dp else 0.dp, RoundedCornerShape(10.dp), spotColor = DisneyColors.GlowBlue.copy(alpha = 0.35f))
            .clip(RoundedCornerShape(10.dp))
            .background(Brush.linearGradient(gradient))
            .border(
                if (focused) 2.dp else 1.dp,
                if (focused) DisneyColors.GlowBlue else DisneyColors.Divider,
                RoundedCornerShape(10.dp),
            )
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        AsyncImage(model = logoUrl, contentDescription = "Brand universe",
            modifier = Modifier.padding(20.dp).fillMaxSize(), contentScale = ContentScale.Fit)
    }
}

// The unified Disney+ selection language as a reusable Modifier
@Composable
fun Modifier.dpFocusable(interaction: MutableInteractionSource, radius: Dp = 6.dp): Modifier {
    val focused by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (focused) 1.04f else 1f, tween(180, easing = EaseOut), label = "focus")
    return this
        .scale(scale)
        .shadow(if (focused) 24.dp else 0.dp, RoundedCornerShape(radius), spotColor = DisneyColors.GlowBlue.copy(alpha = 0.30f))
        .then(
            if (focused) Modifier.border(2.dp, DisneyColors.GlowBlue, RoundedCornerShape(radius))
            else Modifier
        )
}
```

### 16:9 Content Card

```kotlin
@Composable
fun ContentCard16x9(keyArtUrl: String, progress: Float? = null, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    Box(
        Modifier
            .size(220.dp, 124.dp)
            .dpFocusable(interaction, 6.dp)
            .clip(RoundedCornerShape(6.dp))
            .clickable(interaction, indication = null, onClick = onClick),
    ) {
        AsyncImage(model = keyArtUrl, contentDescription = null,
            modifier = Modifier.matchParentSize(), contentScale = ContentScale.Crop)
        if (progress != null) {
            Box(
                Modifier
                    .align(Alignment.BottomStart)
                    .fillMaxWidth()
                    .height(3.dp)
                    .background(Color.White.copy(alpha = 0.25f)),
            ) {
                Box(Modifier.fillMaxWidth(progress).fillMaxHeight().background(DisneyColors.Blue))
            }
        }
    }
}
```

### Episode Row

```kotlin
@Composable
fun EpisodeRow(thumbUrl: String, title: String, synopsis: String, runtime: String, progress: Float? = null) {
    Row(
        Modifier
            .clip(RoundedCornerShape(8.dp))
            .background(DisneyColors.Surface1)
            .padding(12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(160.dp, 90.dp).clip(RoundedCornerShape(6.dp))) {
            AsyncImage(model = thumbUrl, contentDescription = null,
                modifier = Modifier.matchParentSize(), contentScale = ContentScale.Crop)
            Icon(Icons.Filled.PlayCircle, null, tint = Color.White.copy(alpha = 0.9f),
                modifier = Modifier.align(Alignment.Center).size(28.dp))
            if (progress != null) {
                Box(Modifier.align(Alignment.BottomStart).fillMaxWidth().height(3.dp).background(Color.White.copy(alpha = 0.25f))) {
                    Box(Modifier.fillMaxWidth(progress).fillMaxHeight().background(DisneyColors.Blue))
                }
            }
        }
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(title, style = DisneyText.CardTitle, color = Color.White)
            Text(synopsis, style = DisneyText.Subtitle.copy(fontSize = 13.sp), color = DisneyColors.TextSecondary, maxLines = 2, overflow = TextOverflow.Ellipsis)
            Text(runtime, style = DisneyText.Meta, color = DisneyColors.TextSecondary)
        }
        Icon(Icons.Outlined.FileDownload, "Download", tint = DisneyColors.TextSecondary, modifier = Modifier.size(22.dp))
    }
}
```

## 4. Hero Auto-Trailer (Media3 / ExoPlayer)

```kotlin
import androidx.media3.exoplayer.ExoPlayer
import androidx.media3.common.MediaItem
import androidx.media3.common.Player
import androidx.media3.ui.PlayerView
import androidx.compose.ui.viewinterop.AndroidView

@Composable
fun AutoTrailer(url: String, modifier: Modifier = Modifier) {
    val context = LocalContext.current
    val player = remember {
        ExoPlayer.Builder(context).build().apply {
            setMediaItem(MediaItem.fromUri(url))
            repeatMode = Player.REPEAT_MODE_ONE
            volume = 0f          // muted
            playWhenReady = true
            prepare()
        }
    }
    DisposableEffect(Unit) { onDispose { player.release() } }
    AndroidView(
        factory = { PlayerView(it).apply { useController = false; player = this@AutoTrailer.player } },
        modifier = modifier,
    )
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Disney+'s iOS tab bar is `.regularMaterial` blur over deep navy; Android has no first-class live blur, so use a 96%-opaque navy surface. **Active tint is white with a `#0063E5` indicator dot.**

```kotlin
@Composable
fun DisneyBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = DisneyColors.Canvas.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"      to Icons.Filled.Home,
            "Search"    to Icons.Filled.Search,
            "Watchlist" to Icons.Filled.AddToQueue,
            "Downloads" to Icons.Filled.FileDownload,
            "Profile"   to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = DisneyText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = Color.White,
                    selectedTextColor   = Color.White,
                    unselectedIconColor = DisneyColors.TextSecondary,
                    unselectedTextColor = DisneyColors.TextSecondary,
                    indicatorColor      = DisneyColors.Blue.copy(alpha = 0.0f), // use a custom dot if desired
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Tile glow + scale (signature) | `animateFloatAsState` 1 → 1.04 `tween(180, EaseOut)` + `#1A75FF` border + `FocusGlow` shadow |
| Hero auto-trailer | `LaunchedEffect { delay(3000) }` → overlay Media3 player with a `Crossfade`/`AnimatedVisibility(fadeIn 600ms)` |
| Play tap | `animateFloatAsState` 1 → 0.98 `spring(dampingRatio = 0.8f)`, `HapticFeedbackType.LongPress` |
| Brand-portal open | `SharedTransitionLayout` tile → hub (Compose 1.7+), `spring(stiffness = 300)` |
| Detail open | `SharedTransitionLayout` key-art rise; scrim `fadeIn(300)` |
| Row scroll | `LazyRow` default fling; no extra bounce |
| Starfield | optional very slow `rememberInfiniteTransition` sub-pixel translate — ambient |

```kotlin
// Auto-trailer cross-fade
Crossfade(targetState = trailer, animationSpec = tween(600), label = "trailer") { showVideo ->
    if (showVideo) AutoTrailer(trailerUrl, Modifier.matchParentSize())
    else AsyncImage(model = keyArtUrl, contentDescription = null, modifier = Modifier.matchParentSize(), contentScale = ContentScale.Crop)
}
```

Haptics: prefer `LocalHapticFeedback`. For the Play tap, `LocalView.current.performHapticFeedback(HapticFeedbackConstants.LONG_PRESS)`.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Disney+'s glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Play overlay (card) | `play.circle.fill` | `Icons.Filled.PlayCircle` |
| Watchlist | `plus` / `checkmark` | `Icons.Filled.Add` / `Icons.Filled.Check` |
| Download | `arrow.down.circle` | `Icons.Outlined.FileDownload` / `Icons.Filled.DownloadDone` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Rate | `hand.thumbsup` | `Icons.Outlined.ThumbUp` |
| Mute toggle | `speaker.slash.fill` / `speaker.wave.2.fill` | `Icons.Filled.VolumeOff` / `Icons.Filled.VolumeUp` |
| Back | `chevron.left` | `Icons.Filled.ChevronLeft` |
| Cast / AirPlay | `airplayvideo` | `Icons.Filled.Cast` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Watchlist (tab) | `plus.rectangle.on.rectangle` | `Icons.Filled.AddToQueue` |
| Downloads (tab) | `arrow.down.circle` | `Icons.Filled.FileDownload` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; the `Canvas` starfield + Media3 trailer are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the deep navy wants light-content system bars. The billboard bleeds full-bleed under the status bar; apply `Scaffold` insets so the tab bar clears gesture nav.
- **Font scaling**: `sp` honors the user's font scale — keep it on titles, synopsis, metadata, episode titles. Pin badges and tab labels by deriving size from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Auto-trailer**: gate the Media3 swap behind an "Auto-Play Previews" setting and `Settings.Global.ANIMATOR_DURATION_SCALE` / a reduce-motion flag; always start muted; release the player in `onDispose`.
- **TalkBack**: billboard `contentDescription` = "<Series>, <metadata>"; brand tiles = "Disney universe, button"; mirror continue-watching with a progress `contentDescription` ("42 percent watched"); mark the starfield decorative (`Modifier.clearAndSetSemantics {}`).
- **Touch targets**: the 48.dp play pill and 160×96.dp portal tiles clear the 48.dp minimum; give 22.dp action icons a 48.dp hit area via padding.
- **Contrast**: `#A0A6C0` on `#0A0E2A` passes WCAG AA at 14sp+; validate the 12-13sp metadata and lighten toward `#B4BAD0` if targeting strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Disney+'s brand requires the fixed space-navy canvas and the Disney/glow-blue accents regardless of wallpaper. There is no light theme.
