# Apple Music (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Apple Music's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Apple Music's true-black/true-white canvas, single Apple-Music-Red accent, 12dp softer album corners, album-art-derived Now Playing gradient, time-synced lyrics) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, AndroidX Palette instead of Core Image `CIAreaAverage`, a translucent `Surface` instead of `.regularMaterial` (Android has no live blur), `sp`/`dp` instead of `pt`. Apple Music supports both light and dark, so we wire a full Material 3 light + dark scheme; dark canvas is true `#000000` (not `#121212`).

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for artwork, and `androidx.palette:palette-ktx` for the Now Playing album-color extraction.

## 1. Color Tokens

```kotlin
// ui/theme/AppleMusicColors.kt
import androidx.compose.ui.graphics.Color

object AMColors {
    // Brand
    val Red        = Color(0xFFFA2D48) // Play, heart liked, shuffle active, scrubber fill, tab
    val Coral      = Color(0xFFFC3C44) // warmer marketing variant
    val RedPressed = Color(0xFFD4213B)

    // Canvas & Surfaces (Light)
    val CanvasLight   = Color(0xFFFFFFFF)
    val Surface1Light = Color(0xFFF2F2F7) // grouped list / section cards
    val Surface3Light = Color(0xFFE5E5EA) // pressed rows, input fill
    val DividerLight  = Color(0xFFC6C6C8)

    // Canvas & Surfaces (Dark) — full-contrast true black
    val CanvasDark   = Color(0xFF000000) // unusual for media apps — NOT #121212
    val Surface1Dark = Color(0xFF1C1C1E)
    val Surface2Dark = Color(0xFF2C2C2E)
    val Surface3Dark = Color(0xFF3A3A3C)
    val DividerDark  = Color(0xFF38383A)

    // Text base colors (apply alpha for secondary/tertiary tiers)
    val LabelLight = Color(0xFF000000)        // primary, light
    val LabelDark  = Color(0xFFFFFFFF)        // primary, dark
    val SecLight   = Color(0xFF3C3C43)        // .copy(alpha = 0.60f) = secondary
    val SecDark    = Color(0xFFEBEBF5)        // .copy(alpha = 0.60f) = secondary (dark)

    // Badges
    val AtmosGold     = Color(0xFFD4A857)
    val LosslessSilver = Color(0xFF8E8E93)

    // iOS system colors (prefer for links / status, not the brand red)
    val SystemBlue  = Color(0xFF007AFF) // inline links (artist in description)
    val SystemGreen = Color(0xFF34C759) // download complete
    val SystemRed   = Color(0xFFFF3B30) // playback errors (NOT Apple Music red)
}
```

Apple Music supports light and dark equally — wire both Material 3 schemes. `primary` is Apple Music Red; dark `background` is true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val AMLight = lightColorScheme(
    primary        = AMColors.Red,
    onPrimary      = Color.White,
    background     = AMColors.CanvasLight,
    onBackground   = AMColors.LabelLight,
    surface        = AMColors.CanvasLight,
    onSurface      = AMColors.LabelLight,
    surfaceVariant = AMColors.Surface1Light,
    outline        = AMColors.DividerLight,
    error          = AMColors.SystemRed,
)

private val AMDark = darkColorScheme(
    primary        = AMColors.Red,
    onPrimary      = Color.White,
    background     = AMColors.CanvasDark,        // true #000000
    onBackground   = AMColors.LabelDark,
    surface        = AMColors.Surface1Dark,
    onSurface      = AMColors.LabelDark,
    surfaceVariant = AMColors.Surface2Dark,
    outline        = AMColors.DividerDark,
    error          = AMColors.SystemRed,
)

@Composable
fun AppleMusicTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (darkTheme) AMDark else AMLight,
    typography  = AMTypography,
    content     = content,
)
```

## 2. Typography

Apple Music is 100% SF Pro with automatic Display/Text optical switching. SF Pro is unavailable on Android — use the system font (Roboto) and reserve a bundled `Inter` for a tighter metric match. SF Pro Rounded (Listen Now hero cards) maps to a rounded family if you bundle one; otherwise keep Roboto. Honor font scaling everywhere (Apple Music is a flagship Dynamic Type app).

```kotlin
// ui/theme/AppleMusicType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

private val SF = FontFamily.Default          // swap to FontFamily(Font(R.font.inter_…))
private val SFRounded = FontFamily.Default   // swap to a rounded family for Listen Now heroes

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object AMText {
    val LargeTitle    = TextStyle(SF, fontWeight = FontWeight.Bold,     fontSize = 34.sp, lineHeight = 37.sp, letterSpacing = 0.37.sp)
    val Title1        = TextStyle(SF, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = 0.36.sp)
    val HeroListen    = TextStyle(SFRounded, fontWeight = FontWeight.Bold, fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp)
    val Title2        = TextStyle(SF, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.26).sp)
    val Title3        = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.15).sp)
    val NowPlaying    = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Headline      = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.43).sp)
    val Body          = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 25.sp, letterSpacing = (-0.43).sp)
    val Callout       = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.32).sp)
    val Subheadline   = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.24).sp)
    val Footnote      = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp, letterSpacing = (-0.08).sp)
    val Caption1      = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Caption2      = TextStyle(SF, fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.07.sp)
    val Button        = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 17.sp, letterSpacing = (-0.43).sp)
    val Tab           = TextStyle(SF, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = (-0.24).sp)
    val LyricsCurrent = TextStyle(SF, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.3).sp)
    val LyricsOther   = TextStyle(SF, fontWeight = FontWeight.SemiBold, fontSize = 22.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val AMTypography = Typography(
    headlineLarge  = AMText.LargeTitle,
    headlineMedium = AMText.Title1,
    headlineSmall  = AMText.Title3,
    titleMedium    = AMText.Headline,
    bodyLarge      = AMText.Body,
    bodyMedium     = AMText.Subheadline,
    labelLarge     = AMText.Button,
    labelSmall     = AMText.Tab,
)
```

## 3. Signature Components

### Primary Red Play Button

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Pause
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp

@Composable
fun ApplePlayButton(
    isPlaying: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    size: Dp = 64.dp,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        if (pressed) 0.92f else 1f,
        spring(dampingRatio = 0.75f, stiffness = 600f),
        label = "playScale",
    )
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .size(size)
            .scale(scale)
            .clip(CircleShape)
            .background(AMColors.Red)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS .soft impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            imageVector = if (isPlaying) Icons.Filled.Pause else Icons.Filled.PlayArrow,
            contentDescription = if (isPlaying) "Pause" else "Play",
            tint = Color.White,
            modifier = Modifier.size(size * 0.44f),
        )
    }
}
```

### Pill Button (Play / Shuffle / Join Apple Music)

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun AMPillButton(
    text: String,
    icon: ImageVector,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    tint: Color = AMColors.Red,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "pillScale")
    val haptics = LocalHapticFeedback.current

    Row(
        modifier = modifier
            .scale(scale)
            .clip(RoundedCornerShape(8.dp)) // soft rectangle, NOT a full pill — iOS Music signature
            .background(if (pressed) AMColors.RedPressed else tint)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                onClick()
            }
            .padding(horizontal = 24.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(icon, contentDescription = null, tint = Color.White, modifier = Modifier.size(15.dp))
        Text(text, style = AMText.Button, color = Color.White)
    }
}
```

### Album Tile (signature 12dp corner)

```kotlin
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import coil.compose.AsyncImage

@Composable
fun AlbumTile(
    artworkUrl: String,
    title: String,
    subtitle: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    width: Dp = 160.dp,
    isDark: Boolean = false,
) {
    val haptics = LocalHapticFeedback.current
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.96f else 1f, label = "tileScale")

    Column(
        modifier = modifier
            .width(width)
            .scale(scale)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS .selection
                onClick()
            },
        verticalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        AsyncImage(
            model = artworkUrl,
            contentDescription = "$title album art",
            modifier = Modifier
                .size(width)
                // generous shadow in light only — dark mode uses surface steps, no shadow
                .then(if (!isDark)
                    Modifier.shadow(16.dp, RoundedCornerShape(12.dp),
                        spotColor = Color.Black.copy(alpha = 0.08f)) else Modifier)
                .clip(RoundedCornerShape(12.dp)), // signature softer-than-Spotify corner
            contentScale = ContentScale.Crop,
        )
        Text(
            title,
            style = AMText.Subheadline.copy(fontWeight = FontWeight.SemiBold, fontSize = 15.sp),
            color = if (isDark) AMColors.LabelDark else AMColors.LabelLight,
            maxLines = 2, overflow = TextOverflow.Ellipsis,
        )
        Text(
            subtitle,
            style = AMText.Footnote,
            color = (if (isDark) AMColors.SecDark else AMColors.SecLight).copy(alpha = 0.60f),
            maxLines = 1, overflow = TextOverflow.Ellipsis,
        )
    }
}
```

### Track Row (with red equalizer + Atmos badge)

```kotlin
import androidx.compose.material.icons.filled.MoreHoriz

@Composable
fun TrackRow(
    title: String,
    artist: String,
    artworkUrl: String,
    isPlaying: Boolean,
    hasAtmos: Boolean,
    explicit: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()

    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(56.dp)
            .background(if (pressed) Color.Black.copy(alpha = 0.05f) else Color.Transparent)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box {
            AsyncImage(
                model = artworkUrl,
                contentDescription = null,
                modifier = Modifier.size(44.dp).clip(RoundedCornerShape(8.dp)),
                contentScale = ContentScale.Crop,
            )
            if (isPlaying) {
                Box(
                    Modifier
                        .size(18.dp)
                        .align(Alignment.Center)
                        .clip(RoundedCornerShape(4.dp))
                        .background(Color.Black.copy(alpha = 0.5f)),
                    contentAlignment = Alignment.Center,
                ) { Equalizer(color = AMColors.Red) } // red, not Spotify green
            }
        }
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Row(verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                if (explicit) {
                    Box(
                        Modifier
                            .clip(RoundedCornerShape(3.dp))
                            .background(Color.Gray)
                            .padding(horizontal = 4.dp),
                    ) { Text("E", style = AMText.Caption2.copy(fontWeight = FontWeight.Bold),
                        color = Color.White) }
                }
                Text(title, style = AMText.Body.copy(fontSize = 17.sp),
                    color = AMColors.LabelLight, maxLines = 1, overflow = TextOverflow.Ellipsis)
                if (hasAtmos) AtmosBadge()
            }
            Text(artist, style = AMText.Subheadline,
                color = AMColors.SecLight.copy(alpha = 0.60f),
                maxLines = 1, overflow = TextOverflow.Ellipsis)
        }
        Icon(Icons.Filled.MoreHoriz, contentDescription = "More",
            tint = AMColors.SecLight.copy(alpha = 0.60f), modifier = Modifier.size(20.dp))
    }
}

@Composable
fun AtmosBadge() {
    Box(
        Modifier
            .clip(CircleShape) // full pill
            .background(androidx.compose.ui.graphics.Brush.verticalGradient(
                listOf(AMColors.AtmosGold, AMColors.AtmosGold.copy(alpha = 0.85f))))
            .padding(horizontal = 8.dp, vertical = 4.dp),
    ) {
        Text("Dolby Atmos",
            style = AMText.Caption2.copy(fontWeight = FontWeight.SemiBold), color = Color.White)
    }
}
```

### Now Playing Hero (album-art-extracted gradient)

```kotlin
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.material.icons.filled.SkipNext
import androidx.compose.material.icons.filled.SkipPrevious
import androidx.compose.ui.graphics.Brush

@Composable
fun NowPlayingScreen(
    trackTitle: String,
    artist: String,
    artworkUrl: String,
    dominantColor: Color,       // from Palette — see §4
    complementaryColor: Color,  // darker derived — see §4
    isPlaying: Boolean,
    progress: Float,
    onPlayPause: () -> Unit,
) {
    Box(
        Modifier
            .fillMaxSize()
            .background(Brush.verticalGradient(listOf(dominantColor, complementaryColor))),
    ) {
        Column(
            Modifier.fillMaxSize().padding(horizontal = 24.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.Center,
        ) {
            AsyncImage(
                model = artworkUrl,
                contentDescription = "$trackTitle album art",
                modifier = Modifier
                    .size(340.dp)
                    .shadow(40.dp, RoundedCornerShape(16.dp),
                        spotColor = Color.Black.copy(alpha = 0.3f))
                    .clip(RoundedCornerShape(16.dp)),
                contentScale = ContentScale.Crop,
            )
            Spacer(Modifier.height(24.dp))
            Text(trackTitle, style = AMText.NowPlaying, color = Color.White, maxLines = 1)
            Text(artist, style = AMText.Subheadline,
                color = Color.White.copy(alpha = 0.7f), maxLines = 1)
            Spacer(Modifier.height(24.dp))
            AMScrubber(progress = progress)
            Spacer(Modifier.height(24.dp))
            Row(verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(32.dp)) {
                Icon(Icons.Filled.SkipPrevious, "Previous",
                    tint = Color.White, modifier = Modifier.size(32.dp))
                ApplePlayButton(isPlaying = isPlaying, onClick = onPlayPause, size = 64.dp)
                Icon(Icons.Filled.SkipNext, "Next",
                    tint = Color.White, modifier = Modifier.size(32.dp))
            }
        }
    }
}

@Composable
fun AMScrubber(progress: Float) {
    Box(Modifier.fillMaxWidth().height(16.dp), contentAlignment = Alignment.CenterStart) {
        Box(Modifier.fillMaxWidth().height(4.dp).clip(CircleShape)
            .background(Color.White.copy(alpha = 0.2f)))
        Box(Modifier.fillMaxWidth(progress).height(4.dp).clip(CircleShape)
            .background(AMColors.Red)) // Apple Music Red fill
        Box(
            Modifier
                .fillMaxWidth(progress)
                .height(16.dp),
            contentAlignment = Alignment.CenterEnd,
        ) {
            Box(Modifier.size(16.dp).clip(CircleShape).background(Color.White)
                .shadow(2.dp, CircleShape, spotColor = Color.Black.copy(alpha = 0.2f)))
        }
    }
}
```

### Mini-Player (translucent surface — no live blur on Android)

```kotlin
import androidx.compose.material.icons.filled.SkipNext
import androidx.compose.material3.Surface

@Composable
fun MiniPlayer(
    trackTitle: String,
    artist: String,
    artworkUrl: String,
    isPlaying: Boolean,
    onPlayPause: () -> Unit,
    onExpand: () -> Unit,
    isDark: Boolean = false,
) {
    Surface(
        color = (if (isDark) AMColors.Surface1Dark else AMColors.CanvasLight).copy(alpha = 0.80f),
        tonalElevation = 0.dp,
        modifier = Modifier.fillMaxWidth().height(64.dp).clickable(onClick = onExpand),
    ) {
        Row(
            Modifier.padding(start = 12.dp, end = 12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            AsyncImage(
                model = artworkUrl, contentDescription = null,
                modifier = Modifier.size(44.dp).clip(RoundedCornerShape(8.dp)),
                contentScale = ContentScale.Crop,
            )
            Column(Modifier.weight(1f)) {
                Text(trackTitle,
                    style = AMText.Subheadline.copy(fontWeight = FontWeight.Medium, fontSize = 15.sp),
                    color = if (isDark) AMColors.LabelDark else AMColors.LabelLight, maxLines = 1)
                Text(artist, style = AMText.Footnote,
                    color = (if (isDark) AMColors.SecDark else AMColors.SecLight).copy(alpha = 0.60f),
                    maxLines = 1)
            }
            Icon(
                if (isPlaying) Icons.Filled.Pause else Icons.Filled.PlayArrow,
                contentDescription = if (isPlaying) "Pause" else "Play",
                tint = if (isDark) AMColors.LabelDark else AMColors.LabelLight,
                modifier = Modifier.size(22.dp).clickable(onClick = onPlayPause),
            )
            Icon(Icons.Filled.SkipNext, contentDescription = "Next",
                tint = if (isDark) AMColors.LabelDark else AMColors.LabelLight,
                modifier = Modifier.size(22.dp))
        }
    }
}
```

## 4. Time-Synced Lyrics + Album-Art Color Extraction

Apple Music's defining feature is the **time-synced lyrics view** (current line scales up + brightens, auto-scrolls to playback). iOS uses `ScrollViewReader`; Compose uses `LazyColumn` + `LaunchedEffect(currentTimeMs)` + `animateScrollToItem`. Line size/opacity animate via `animateFloatAsState`.

```kotlin
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.itemsIndexed
import androidx.compose.foundation.lazy.rememberLazyListState

data class LyricLine(val startMs: Long, val endMs: Long, val text: String)

@Composable
fun LyricsView(lines: List<LyricLine>, currentTimeMs: Long, gradient: Brush) {
    val listState = rememberLazyListState()
    val currentIndex = lines.indexOfLast { currentTimeMs >= it.startMs }

    LaunchedEffect(currentIndex) {
        if (currentIndex >= 0) listState.animateScrollToItem(
            index = currentIndex.coerceAtLeast(0),
            scrollOffset = -300, // keep the active line centered
        )
    }

    LazyColumn(
        state = listState,
        modifier = Modifier.fillMaxSize().background(gradient),
        contentPadding = PaddingValues(horizontal = 24.dp, vertical = 80.dp),
        verticalArrangement = Arrangement.spacedBy(24.dp),
    ) {
        itemsIndexed(lines) { i, line ->
            val isCurrent = i == currentIndex
            val isPast = i < currentIndex
            val targetAlpha = when {
                isCurrent -> 1f
                isPast    -> 0.3f   // faded above
                else      -> 0.6f   // upcoming below
            }
            val alpha by animateFloatAsState(targetAlpha,
                spring(dampingRatio = 0.85f), label = "lyricAlpha")
            Text(
                line.text,
                style = if (isCurrent) AMText.LyricsCurrent else AMText.LyricsOther,
                color = Color.White.copy(alpha = alpha),
            )
        }
    }
}
```

Album-art color extraction (the Now Playing / Lyrics gradient): Android's analog to iOS Core Image `CIAreaAverage` is **AndroidX Palette**. Load with Coil (software bitmap), pull a vibrant swatch as the dominant, and derive a darker complementary by scaling RGB — exactly Apple Music's "primary at top, darker complementary at bottom".

```kotlin
// build.gradle: implementation("androidx.palette:palette-ktx:1.0.0")
import android.content.Context
import android.graphics.drawable.BitmapDrawable
import androidx.palette.graphics.Palette
import coil.imageLoader
import coil.request.ImageRequest

suspend fun extractGradient(context: Context, url: String): Pair<Color, Color> {
    val request = ImageRequest.Builder(context)
        .data(url)
        .allowHardware(false) // Palette needs a software bitmap
        .build()
    val bitmap = (context.imageLoader.execute(request).drawable as? BitmapDrawable)?.bitmap
        ?: return AMColors.CanvasDark to AMColors.CanvasDark
    val palette = Palette.from(bitmap).generate()
    val rgb = (palette.vibrantSwatch ?: palette.dominantSwatch)?.rgb
        ?: return AMColors.CanvasDark to AMColors.CanvasDark
    val dominant = Color(rgb)
    val complementary = Color( // darker derived variant
        red = dominant.red * 0.4f,
        green = dominant.green * 0.4f,
        blue = dominant.blue * 0.4f,
    )
    return dominant to complementary
}
```

Hoist into a `ViewModel` and cache per track so scrubbing never re-computes:

```kotlin
val gradient by produceState(AMColors.CanvasDark to AMColors.CanvasDark, artworkUrl) {
    value = extractGradient(context, artworkUrl)
}
```

The mini-player → Now Playing artwork morph is Compose `SharedTransitionLayout` + `Modifier.sharedElement()` (Compose 1.7+) on the 44dp ↔ 340dp artwork; fall back to `AnimatedVisibility` + `scaleIn` over a 0.35s `spring(dampingRatio = 0.8f)`.

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Apple Music's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use an 80%-opaque canvas surface. **Active tint is Apple Music Red** (distinctive — most apps use the system accent for the tab indicator). Five tabs, always labeled.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun AppleMusicBottomBar(selected: Int, onSelect: (Int) -> Unit, isDark: Boolean = false) {
    NavigationBar(
        containerColor = (if (isDark) AMColors.CanvasDark else AMColors.CanvasLight)
            .copy(alpha = 0.80f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"    to Icons.Filled.Home,
            "New"     to Icons.Filled.LibraryMusic,
            "Radio"   to Icons.Filled.Podcasts,
            "Library" to Icons.Filled.LibraryBooks,
            "Search"  to Icons.Filled.Search,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(25.dp)) },
                label = { Text(label, style = AMText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = AMColors.Red,   // Apple Music Red, not system accent
                    selectedTextColor = AMColors.Red,
                    unselectedIconColor = (if (isDark) AMColors.SecDark else AMColors.SecLight)
                        .copy(alpha = 0.60f),
                    unselectedTextColor = (if (isDark) AMColors.SecDark else AMColors.SecLight)
                        .copy(alpha = 0.60f),
                    indicatorColor = Color.Transparent, // no Material pill — Apple Music has none
                ),
            )
        }
    }
}
```

The persistent **mini-player** sits directly above this bar — render it in the `Scaffold` `bottomBar` slot stacked above `AppleMusicBottomBar`. The collapsing iOS large-title nav maps to `LargeTopAppBar` + `exitUntilCollapsedScrollBehavior()` (34sp → 17sp on scroll).

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Play button tap | `animateFloatAsState` 1 → 0.92 `spring(dampingRatio = 0.75f)`; `HapticFeedbackType.TextHandleMove` (~iOS `.soft`) |
| Track change on Now Playing | `Crossfade` artwork with scale 0.98 → 1 over 400ms |
| Mini-player → full Now Playing | `SharedTransitionLayout` 44 → 340dp, 0.35s `spring(dampingRatio = 0.8f)`; fallback `scaleIn` |
| Equalizer bars | `rememberInfiniteTransition` 3 bars' `fillMaxHeight`, frozen when paused |
| Lyrics scroll | active line `animateFloatAsState` alpha 0.6 → 1, size 22 → 28sp, ~300ms, sync to audio |
| Heart like | `Animatable` 1 → 1.2 → 1 over 300ms, fill → Red; `HapticFeedbackType.LongPress` (~`.success`) |
| Shuffle toggle | `animateColorAsState` secondary → Red, 200ms; selection haptic |
| Long-press tile | scale → 0.96 + haptic; context menu via `DropdownMenu` |

```kotlin
// Equalizer — red bars on the currently-playing row
@Composable
fun Equalizer(color: Color, playing: Boolean = true) {
    val t = rememberInfiniteTransition(label = "eq")
    Row(horizontalArrangement = Arrangement.spacedBy(2.dp)) {
        repeat(3) { i ->
            val h by t.animateFloat(
                0.3f, 1f,
                infiniteRepeatable(tween(400 + i * 120), RepeatMode.Reverse),
                label = "bar$i",
            )
            Box(
                Modifier
                    .width(3.dp)
                    .fillMaxHeight(if (playing) h else 0.3f)
                    .background(color),
            )
        }
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) — map `.soft` (Play) to `VibrationEffect.createOneShot(10, ...)`, `.success` (heart like) to a double-buzz `VibrationEffect.createWaveform(longArrayOf(0, 40, 60, 40), -1)`, and `.selection` (shuffle / tab / chip) to a short tick.

## 7. Icons

Apple Music uses SF Symbols exclusively; the closest first-party set is `androidx.compose.material:material-icons-extended`. The explicit `E` badge is a custom rounded-rect composable (shown in §3); ship any non-matching glyph as a vector drawable via `ImageVector.vectorResource(R.drawable.…)`.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Forward / Previous | `forward.fill` / `backward.fill` | `Icons.Filled.SkipNext` / `SkipPrevious` |
| Shuffle | `shuffle` | `Icons.Filled.Shuffle` |
| Repeat | `repeat` / `repeat.1` | `Icons.Filled.Repeat` / `RepeatOne` |
| Heart | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Download | `arrow.down.circle` | `Icons.Filled.DownloadForOffline` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (Listen Now) | `house` / `house.fill` | `Icons.Filled.Home` |
| New (Browse) | `music.note.list` | `Icons.Filled.LibraryMusic` |
| Radio | `dot.radiowaves.left.and.right` | `Icons.Filled.Podcasts` |
| Library | `square.stack` | `Icons.Filled.LibraryBooks` |
| Lyrics | `quote.bubble` | `Icons.Filled.Lyrics` |
| AirPlay | `airplayaudio` | `Icons.Filled.Cast` |
| Queue | `list.bullet` | `Icons.AutoMirrored.Filled.QueueMusic` |
| Chevron | `chevron.right` | `Icons.AutoMirrored.Filled.KeyboardArrowRight` |
| Explicit | custom 'E' in rounded rect | custom composable (see §3) |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; Palette + `SharedTransitionLayout` + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. Light canvas wants dark-content (dark icons) system bars; the true-black dark canvas wants light-content. Now Playing's full-bleed gradient should draw under the system bars. Apply `Scaffold` insets so the mini-player clears the gesture nav.
- **Font scaling**: Apple Music is a flagship Dynamic Type app — `sp` honors the user's font scale; keep it on track titles, artist names, headers, body, and lyrics (current line scales up to ~36sp). Pin layout-sensitive geometry (the 64dp play glyph, scrubber thumb, mini-player frame, 10sp tab labels) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. At the largest scales the mini-player drops text and shows only artwork + play.
- **TalkBack**: the Play button reads `"Play Blinding Lights by The Weeknd"`; the heart reads `"Add to Favorites"` / `"Remove from Favorites"`. Merge track-row text with `Modifier.semantics(mergeDescendants = true)` and mark the trailing ellipsis as a separate button. Lyrics lines expose their text; the current line can announce via `liveRegion`.
- **Touch targets**: Material guidance is 48.dp minimum. The 64dp Now Playing play button is well clear; inline play buttons and tab items are 44–49dp — bump the 20dp ellipsis and 22dp mini-player controls to a 48.dp hit box via padding.
- **Contrast**: pure black on white (and white on true black) is WCAG AAA. Secondary at 60% (`#3C3C43`@60% light / `#EBEBF5`@60% dark) passes AA at ≥14sp Medium — acceptable for artist/metadata, not for primary information at small sizes.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, skip the mini-player spring expansion (use a `Crossfade`), freeze the equalizer, and render the current lyric line plainly without the scale animation.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Apple Music Red `#FA2D48`, the true-black `#000000` dark canvas, and the 12dp album corner are signature, intentionally system-Apple choices that must not recolor to the user's wallpaper. The Now Playing gradient comes from album art (Palette), never from the system accent.
