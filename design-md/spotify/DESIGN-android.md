# Spotify (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Spotify's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Spotify's true-dark canvas, single-green accent, album-art-as-color) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, Palette instead of Core Image, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for images, and `androidx.palette:palette-ktx` for color extraction.

## 1. Color Tokens

```kotlin
// ui/theme/SpotifyColors.kt
import androidx.compose.ui.graphics.Color

object SpotifyColors {
    // Canvas & Surfaces
    val Canvas    = Color(0xFF121212)
    val DeepBlack = Color(0xFF000000)
    val Surface1  = Color(0xFF181818)
    val Surface2  = Color(0xFF282828)
    val Surface3  = Color(0xFF3E3E3E)
    val Divider   = Color(0xFF2A2A2A)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFB3B3B3)
    val TextTertiary  = Color(0xFF6A6A6A)

    // Brand
    val Green        = Color(0xFF1DB954)
    val GreenPressed = Color(0xFF169C46)
    val LogoGreen    = Color(0xFF1ED760) // logotype only — never UI chrome
    val ErrorRed     = Color(0xFFF15E6C)
}
```

Wire it into a Material 3 `darkColorScheme` so ripples, dividers, and default component colors inherit the brand. Spotify is effectively dark-only; do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val SpotifyScheme = darkColorScheme(
    primary       = SpotifyColors.Green,
    onPrimary     = SpotifyColors.DeepBlack,   // intentional: black on green
    background    = SpotifyColors.Canvas,
    onBackground  = SpotifyColors.TextPrimary,
    surface       = SpotifyColors.Surface1,
    onSurface     = SpotifyColors.TextPrimary,
    surfaceVariant = SpotifyColors.Surface2,
    outline       = SpotifyColors.Divider,
    error         = SpotifyColors.ErrorRed,
)

@Composable
fun SpotifyTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = SpotifyScheme, typography = SpotifyTypography, content = content)
```

## 2. Typography

Spotify Mix is proprietary. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto) — its grotesque tone is the closest free substitute on Android.

```kotlin
// ui/theme/SpotifyType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val SpotifyMix = FontFamily(
    Font(R.font.spotify_mix_regular,  FontWeight.Normal),   // 400
    Font(R.font.spotify_mix_semibold, FontWeight.SemiBold), // 600
    Font(R.font.spotify_mix_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object SpotifyText {
    val TitleLarge   = TextStyle(SpotifyMix, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val Title        = TextStyle(SpotifyMix, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val PlaylistHero = TextStyle(SpotifyMix, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.3).sp)
    val TrackTitle   = TextStyle(SpotifyMix, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val CardTitle    = TextStyle(SpotifyMix, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val Subtitle     = TextStyle(SpotifyMix, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val Body         = TextStyle(SpotifyMix, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val Meta         = TextStyle(SpotifyMix, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val LabelUpper   = TextStyle(SpotifyMix, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.5.sp)
    val Button       = TextStyle(SpotifyMix, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 20.sp)
    val Tab          = TextStyle(SpotifyMix, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val SpotifyTypography = Typography(
    headlineLarge = SpotifyText.TitleLarge,
    headlineSmall = SpotifyText.Title,
    titleMedium   = SpotifyText.TrackTitle,
    bodyMedium    = SpotifyText.Body,
    labelSmall    = SpotifyText.Tab,
)
```

## 3. Signature Components

### Primary Green Play Button

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Pause
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun SpotifyPlayButton(
    isPlaying: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    size: Dp = 72.dp,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        targetValue = if (pressed) 0.92f else 1f,
        animationSpec = spring(dampingRatio = 0.7f, stiffness = 600f),
        label = "playScale",
    )
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .size(size)
            .scale(scale)
            .clip(CircleShape)
            .background(if (pressed) SpotifyColors.GreenPressed else SpotifyColors.Green)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS soft impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            imageVector = if (isPlaying) Icons.Filled.Pause else Icons.Filled.PlayArrow,
            contentDescription = if (isPlaying) "Pause" else "Play",
            tint = SpotifyColors.DeepBlack, // intentional: black on green, not white
            modifier = Modifier.size(size * 0.45f),
        )
    }
}
```

### Primary Pill Button (Follow / Premium)

```kotlin
enum class PillStyle { Filled, Outline }

@Composable
fun SpotifyPillButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    style: PillStyle = PillStyle.Filled,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "pillScale")
    val filled = style == PillStyle.Filled

    Box(
        modifier = modifier
            .scale(scale)
            .clip(CircleShape) // full pill
            .then(
                if (filled) Modifier.background(if (pressed) SpotifyColors.GreenPressed else SpotifyColors.Green)
                else Modifier.border(1.dp, SpotifyColors.TextSecondary, CircleShape)
            )
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 32.dp, vertical = 10.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(
            text = text,
            style = SpotifyText.Button,
            color = if (filled) SpotifyColors.DeepBlack else SpotifyColors.TextPrimary,
        )
    }
}
```

### Track Row

```kotlin
@Composable
fun TrackRow(
    title: String,
    artist: String,
    artworkUrl: String,
    isPlaying: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()

    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(56.dp)
            .background(if (pressed) SpotifyColors.Surface2 else Color.Transparent)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        AsyncImage(
            model = artworkUrl,
            contentDescription = null,
            modifier = Modifier.size(44.dp).clip(RoundedCornerShape(4.dp)),
            contentScale = ContentScale.Crop,
        )
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(
                title,
                style = SpotifyText.TrackTitle,
                color = if (isPlaying) SpotifyColors.Green else SpotifyColors.TextPrimary,
                maxLines = 1, overflow = TextOverflow.Ellipsis,
            )
            Text(
                artist,
                style = SpotifyText.Subtitle,
                color = if (isPlaying) SpotifyColors.Green else SpotifyColors.TextSecondary,
                maxLines = 1, overflow = TextOverflow.Ellipsis,
            )
        }
        Icon(
            Icons.Filled.MoreHoriz,
            contentDescription = "More",
            tint = SpotifyColors.TextSecondary,
            modifier = Modifier.size(20.dp),
        )
    }
}
```

### Now Playing Hero (dynamic gradient)

```kotlin
@Composable
fun NowPlayingScreen(
    trackTitle: String,
    artist: String,
    artworkUrl: String,
    dominantColor: Color, // extracted via Palette — see §4
) {
    Box(
        Modifier
            .fillMaxSize()
            .background(Brush.verticalGradient(listOf(dominantColor, SpotifyColors.Canvas))),
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
                    .shadow(32.dp, RoundedCornerShape(8.dp), spotColor = Color.Black.copy(alpha = 0.5f))
                    .clip(RoundedCornerShape(8.dp)),
                contentScale = ContentScale.Crop,
            )
            Spacer(Modifier.height(32.dp))
            Text(trackTitle, style = SpotifyText.Title, color = SpotifyColors.TextPrimary)
            Text(artist, style = SpotifyText.Subtitle, color = SpotifyColors.TextSecondary)
            // Scrubber + transport controls omitted for brevity
        }
    }
}
```

## 4. Dynamic Album Color Extraction

Android's analog to iOS Core Image `CIAreaAverage` is the **AndroidX Palette** library. Load the bitmap with Coil (disable hardware bitmaps so Palette can read pixels), then pull a dark, muted swatch that matches Spotify's gradient feel.

```kotlin
// build.gradle: implementation("androidx.palette:palette-ktx:1.0.0")
import androidx.palette.graphics.Palette
import coil.imageLoader
import coil.request.ImageRequest

suspend fun dominantColor(context: Context, url: String): Color {
    val request = ImageRequest.Builder(context)
        .data(url)
        .allowHardware(false) // Palette needs a software bitmap
        .build()
    val bitmap = (context.imageLoader.execute(request).drawable as? BitmapDrawable)?.bitmap
        ?: return SpotifyColors.Canvas
    val palette = Palette.from(bitmap).generate()
    val swatch = palette.darkVibrantSwatch
        ?: palette.darkMutedSwatch
        ?: palette.dominantSwatch
    return swatch?.let { Color(it.rgb) } ?: SpotifyColors.Canvas
}
```

Hoist this into a `ViewModel` and expose the color as state so the gradient recomposes per track:

```kotlin
val dominant by produceState(SpotifyColors.Canvas, artworkUrl) {
    value = dominantColor(context, artworkUrl)
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Spotify's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 92%-opaque canvas surface. **Active tint is white, not green** — green is reserved for structural primary actions.

```kotlin
@Composable
fun SpotifyBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = SpotifyColors.Canvas.copy(alpha = 0.92f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"    to Icons.Filled.Home,
            "Search"  to Icons.Filled.Search,
            "Library" to Icons.Filled.LibraryMusic,
            "Premium" to Icons.Filled.AutoAwesome,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = SpotifyText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = SpotifyColors.TextPrimary,   // white, not green
                    selectedTextColor = SpotifyColors.TextPrimary,
                    unselectedIconColor = SpotifyColors.TextSecondary,
                    unselectedTextColor = SpotifyColors.TextSecondary,
                    indicatorColor = Color.Transparent, // no Material pill — Spotify has none
                ),
            )
        }
    }
}
```

The persistent **Now Playing mini-bar** sits directly above this bar: a 56.dp `Surface(color = SpotifyColors.Surface2)` row, full width, 40.dp artwork + scrolling title — render it in the `Scaffold` `bottomBar` slot stacked above `SpotifyBottomBar`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Play/pause tap | `animateFloatAsState` 1 → 0.92 with `spring(dampingRatio = 0.7f)`, `HapticFeedbackType.LongPress` |
| Heart like | `Animatable` keyframes 1 → 1.15 → 1 over 300ms; `HapticFeedbackType.LongPress` |
| Equalizer (now-playing row) | `rememberInfiniteTransition` driving 3 bars' `scaleY`, frozen when paused |
| Mini-bar → full player | `SharedTransitionLayout` + `Modifier.sharedElement()` on the artwork (Compose 1.7+); fallback: `AnimatedVisibility` + `slideInVertically` |

```kotlin
// Equalizer bars on the currently-playing track
@Composable
fun Equalizer(playing: Boolean) {
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
                    .background(SpotifyColors.Green),
            )
        }
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's `.soft` impact.

## 7. Icons

Spotify ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Spotify's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Next / Previous | `forward.end.fill` / `backward.end.fill` | `Icons.Filled.SkipNext` / `SkipPrevious` |
| Shuffle | `shuffle` | `Icons.Filled.Shuffle` |
| Repeat | `repeat` | `Icons.Filled.Repeat` |
| Heart | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Download | `arrow.down.circle` | `Icons.Filled.DownloadForOffline` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Library (tab) | `books.vertical.fill` | `Icons.Filled.LibraryMusic` |
| Device | `hifispeaker` | `Icons.Filled.Speaker` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; Palette + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the dark canvas wants `WindowCompat` light-content system bars. Apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` or `Scaffold` insets so the mini-bar clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on track titles, artist names, headers. Pin layout-sensitive text (`72.dp` play glyph, scrubber timestamps, tab labels) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: set `contentDescription` on the play button (`"Play Blinding Lights by The Weeknd"`); merge track-row text with `Modifier.semantics(mergeDescendants = true)` and mark the trailing overflow as a separate button.
- **Touch targets**: Material guidance is 48.dp minimum. The 72.dp play button is well clear; ensure the 20.dp ellipsis has `Modifier.size(48.dp)` hit area via padding even though the glyph is small.
- **Contrast**: `#B3B3B3` on `#121212` passes WCAG AA at 14sp+. Validate the 11sp tab/meta labels with a contrast checker and bump toward `#C7C7C7` if your build targets accessibility compliance.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Spotify's brand requires the fixed `#121212` canvas and single-green accent regardless of wallpaper.
