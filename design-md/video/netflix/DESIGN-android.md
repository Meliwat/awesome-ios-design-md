# Netflix (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Netflix's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Netflix's warm `#141414` canvas, single sacred red, art-as-the-headline) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ExoPlayer` instead of `AVPlayer`, a translucent `Surface` instead of `.regularMaterial`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for poster art, and `androidx.media3:media3-exoplayer` for the hero trailer. No color extraction — Netflix never tints chrome from art, so Palette is not needed.

## 1. Color Tokens

```kotlin
// ui/theme/NetflixColors.kt
import androidx.compose.ui.graphics.Color

object NetflixColors {
    // Brand
    val Red        = Color(0xFFE50914)
    val RedPressed = Color(0xFFB7070F)
    val RedDimmed  = Color(0xFF831010)

    // Canvas & Surfaces
    val Canvas    = Color(0xFF141414) // warm dark — NOT pure black
    val DeepBlack = Color(0xFF000000) // behind hero art only
    val Surface1  = Color(0xFF1F1F1F)
    val Surface2  = Color(0xFF2A2A2A)
    val Surface3  = Color(0xFF3A3A3A)
    val Divider   = Color(0xFF2B2B2B)
    val Input     = Color(0xFF333333)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFAAAAAA)
    val TextTertiary  = Color(0xFF777777)

    // Profile accent rotation (profile picker only — never UI chrome)
    val ProfileRed    = Color(0xFFE50914)
    val ProfileBlue   = Color(0xFF3E3E91)
    val ProfileYellow = Color(0xFFF5D85C)
    val ProfileGreen  = Color(0xFF4B8A3E)
    val KidsOrange    = Color(0xFFF8981D)

    // Semantic
    val Info = Color(0xFF54B9C5) // download arc only — rarely used
}
```

Wire it into a Material 3 `darkColorScheme` so ripples, dividers, and default component colors inherit the brand. Netflix is dark-only and has never shipped a light mode; do not provide a light scheme, and ignore the system light-mode preference.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val NetflixScheme = darkColorScheme(
    primary        = NetflixColors.Red,
    onPrimary      = NetflixColors.TextPrimary,  // white on red Play
    background     = NetflixColors.Canvas,
    onBackground   = NetflixColors.TextPrimary,
    surface        = NetflixColors.Surface1,
    onSurface      = NetflixColors.TextPrimary,
    surfaceVariant = NetflixColors.Surface2,
    outline        = NetflixColors.Divider,
    error          = NetflixColors.Red,          // playback errors reuse brand red
)

@Composable
fun NetflixTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = NetflixScheme, typography = NetflixTypography, content = content)
```

## 2. Typography

Netflix Sans is proprietary (Dalton Maag, 2018, replacing Gotham). Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto) — its humanist-geometric tone is the closest free substitute on Android; Graphik is the brand's secondary if licensed.

```kotlin
// ui/theme/NetflixType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val NetflixSans = FontFamily(
    Font(R.font.netflix_sans_regular, FontWeight.Normal),   // 400
    Font(R.font.netflix_sans_medium,  FontWeight.Medium),   // 500
    Font(R.font.netflix_sans_bold,    FontWeight.Bold),     // 700
    Font(R.font.netflix_sans_black,   FontWeight.Black),    // 900 — Top 10 numerals
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object NetflixText {
    val TitleHero     = TextStyle(NetflixSans, fontWeight = FontWeight.Bold,   fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.4).sp)
    val ScreenTitle   = TextStyle(NetflixSans, fontWeight = FontWeight.Bold,   fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val RowHeader     = TextStyle(NetflixSans, fontWeight = FontWeight.Bold,   fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val EpisodeTitle  = TextStyle(NetflixSans, fontWeight = FontWeight.Medium, fontSize = 17.sp, lineHeight = 20.sp)
    val Body          = TextStyle(NetflixSans, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 20.sp)
    val Metadata      = TextStyle(NetflixSans, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val MetadataSm    = TextStyle(NetflixSans, fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 16.sp)
    val ButtonPlay    = TextStyle(NetflixSans, fontWeight = FontWeight.Bold,   fontSize = 17.sp, lineHeight = 17.sp)
    val ButtonSecond  = TextStyle(NetflixSans, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 15.sp)
    val Badge         = TextStyle(NetflixSans, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.4.sp)
    val TabLabel      = TextStyle(NetflixSans, fontWeight = FontWeight.Medium, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp)
    val Cert          = TextStyle(NetflixSans, fontWeight = FontWeight.Bold,   fontSize = 12.sp, lineHeight = 12.sp)
    val ProgressTime  = TextStyle(NetflixSans, fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 12.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val NetflixTypography = Typography(
    headlineLarge = NetflixText.TitleHero,
    titleLarge    = NetflixText.ScreenTitle,
    titleMedium   = NetflixText.RowHeader,
    bodyMedium    = NetflixText.Body,
    labelSmall    = NetflixText.TabLabel,
)
```

## 3. Signature Components

### Primary Play Button (The Iconic Red 4pt-Corner Pill)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun NetflixPlayButton(
    title: String,           // "Play" or "Resume"
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        targetValue = if (pressed) 0.98f else 1f,
        animationSpec = spring(dampingRatio = 0.8f, stiffness = 600f),
        label = "playScale",
    )
    val haptics = LocalHapticFeedback.current

    Row(
        modifier = modifier
            .fillMaxWidth()
            .scale(scale)
            .clip(RoundedCornerShape(4.dp)) // signature soft corner — NOT a full pill
            .background(if (pressed) NetflixColors.RedPressed else NetflixColors.Red)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS rigid impact
                onClick()
            }
            .padding(vertical = 14.dp, horizontal = 24.dp),
        horizontalArrangement = Arrangement.Center,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(
            Icons.Filled.PlayArrow,
            contentDescription = null,
            tint = NetflixColors.TextPrimary,
            modifier = Modifier.size(20.dp),
        )
        Spacer(Modifier.width(8.dp))
        Text(title, style = NetflixText.ButtonPlay, color = NetflixColors.TextPrimary)
    }
}
```

### Secondary Button (My List / Info / Trailer)

```kotlin
@Composable
fun NetflixSecondaryButton(
    icon: ImageVector,
    title: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()

    Row(
        modifier = modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(4.dp))
            .background(Color.White.copy(alpha = if (pressed) 0.25f else 0.15f))
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(vertical = 12.dp, horizontal = 20.dp),
        horizontalArrangement = Arrangement.Center,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(icon, contentDescription = null, tint = NetflixColors.TextPrimary, modifier = Modifier.size(16.dp))
        Spacer(Modifier.width(8.dp))
        Text(title, style = NetflixText.ButtonSecond, color = NetflixColors.TextPrimary)
    }
}
```

### Poster Tile (2:3 portrait, optional Continue Watching bar)

```kotlin
import coil.compose.AsyncImage
import androidx.compose.ui.layout.ContentScale

@Composable
fun PosterTile(
    imageUrl: String,
    width: Dp,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    progress: Float? = null, // 0f–1f — pass for Continue Watching
) {
    Box(
        modifier = modifier
            .width(width)
            .height(width * 1.5f) // 2:3 portrait
            .clip(RoundedCornerShape(4.dp)) // Netflix's signature soft corner — no shadow
            .background(NetflixColors.Surface1)
            .clickable(onClick = onClick),
        contentAlignment = Alignment.BottomCenter,
    ) {
        AsyncImage(
            model = imageUrl,
            contentDescription = null,
            modifier = Modifier.fillMaxSize(),
            contentScale = ContentScale.Crop,
        )
        if (progress != null) {
            Box(
                Modifier
                    .fillMaxWidth()
                    .padding(horizontal = 4.dp)
                    .padding(bottom = 4.dp)
                    .height(2.dp)
                    .background(Color.White.copy(alpha = 0.3f)),
            ) {
                Box(
                    Modifier
                        .fillMaxHeight()
                        .fillMaxWidth(progress.coerceIn(0f, 1f))
                        .background(NetflixColors.Red),
                )
            }
        }
    }
}
```

### Horizontal Poster Row

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.items

@Composable
fun PosterRow(
    title: String,
    posters: List<String>,
    onPoster: (String) -> Unit,
    modifier: Modifier = Modifier,
    tileWidth: Dp = 130.dp,
) {
    Column(modifier, verticalArrangement = Arrangement.spacedBy(8.dp)) {
        Text(
            title,
            style = NetflixText.RowHeader,
            color = NetflixColors.TextPrimary,
            modifier = Modifier.padding(horizontal = 16.dp, vertical = 4.dp),
        )
        LazyRow(
            horizontalArrangement = Arrangement.spacedBy(8.dp),
            contentPadding = PaddingValues(horizontal = 16.dp),
        ) {
            items(posters) { url ->
                PosterTile(imageUrl = url, width = tileWidth, onClick = { onPoster(url) })
            }
        }
    }
}
```

## 4. Hero Trailer Auto-Play + Top 10 Numeral Row

Netflix's two signature compositions: the muted hero trailer that fades in after a 2s idle, and the giant outlined numerals behind the Top 10 row.

### Hero with ExoPlayer trailer

iOS uses `AVPlayer` with a 2s delay; Android's analog is `media3` ExoPlayer behind an `AndroidView`. Start muted, cross-fade from key art, pause when off-screen.

```kotlin
// build.gradle: implementation("androidx.media3:media3-exoplayer:1.4.1"); media3-ui
import androidx.compose.animation.Crossfade
import androidx.compose.foundation.background
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.viewinterop.AndroidView
import androidx.media3.common.MediaItem
import androidx.media3.exoplayer.ExoPlayer
import androidx.media3.ui.PlayerView
import kotlinx.coroutines.delay

@Composable
fun HeroCard(
    keyArtUrl: String,
    trailerUrl: String?,
    titleLogoUrl: String,
    genres: List<String>,
    onPlay: () -> Unit,
    onMyList: () -> Unit,
    onInfo: () -> Unit,
) {
    val context = LocalContext.current
    var showTrailer by remember { mutableStateOf(false) }
    val player = remember {
        trailerUrl?.let {
            ExoPlayer.Builder(context).build().apply {
                setMediaItem(MediaItem.fromUri(it))
                volume = 0f // muted — never auto-play with sound
                repeatMode = ExoPlayer.REPEAT_MODE_ONE
                prepare()
            }
        }
    }
    LaunchedEffect(trailerUrl) {
        if (player != null) { delay(2_000); showTrailer = true; player.play() }
    }
    DisposableEffect(Unit) { onDispose { player?.release() } }

    Box(
        Modifier
            .fillMaxWidth()
            .height(440.dp)
            .background(NetflixColors.Canvas),
        contentAlignment = Alignment.BottomCenter,
    ) {
        Crossfade(showTrailer && player != null, label = "heroTrailer") { trailing ->
            if (trailing && player != null) {
                AndroidView(
                    factory = { PlayerView(it).apply { useController = false; this.player = player } },
                    modifier = Modifier.fillMaxSize(),
                )
            } else {
                AsyncImage(
                    model = keyArtUrl,
                    contentDescription = null,
                    modifier = Modifier.fillMaxSize(),
                    contentScale = ContentScale.Crop,
                )
            }
        }
        // Vertical gradient eats the lower 40% into the canvas
        Box(
            Modifier
                .fillMaxWidth()
                .height(200.dp)
                .background(Brush.verticalGradient(listOf(Color.Transparent, NetflixColors.Canvas))),
        )
        Column(
            Modifier.fillMaxWidth().padding(bottom = 16.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            AsyncImage(
                model = titleLogoUrl,
                contentDescription = "Title",
                modifier = Modifier.fillMaxWidth(0.65f).height(80.dp),
                contentScale = ContentScale.Fit,
            )
            Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                genres.forEachIndexed { i, g ->
                    Text(g, style = NetflixText.Metadata, color = NetflixColors.TextPrimary)
                    if (i < genres.lastIndex) Text("•", style = NetflixText.Metadata, color = NetflixColors.TextSecondary)
                }
            }
            Row(
                Modifier.padding(horizontal = 16.dp),
                horizontalArrangement = Arrangement.spacedBy(12.dp),
            ) {
                NetflixSecondaryButton(Icons.Filled.Add, "My List", onMyList, Modifier.weight(1f))
                NetflixPlayButton("Play", onPlay, Modifier.weight(1f))
                NetflixSecondaryButton(Icons.Filled.Info, "Info", onInfo, Modifier.weight(1f))
            }
        }
    }
}
```

### Top 10 Row (giant outlined numerals)

True outline type needs a stroked draw. Use `Canvas` + `Paint.Style.STROKE` on the native paint so the numeral is a hollow `#141414` glyph the poster lifts in front of.

```kotlin
import android.graphics.Paint
import android.graphics.Typeface
import androidx.compose.foundation.Canvas
import androidx.compose.ui.graphics.toArgb
import androidx.compose.ui.graphics.nativeCanvas

@Composable
fun Top10Numeral(index: Int, modifier: Modifier = Modifier) {
    val stroke = remember {
        Paint().apply {
            style = Paint.Style.STROKE
            strokeWidth = 4f               // ~2pt stroke, scaled
            color = NetflixColors.TextTertiary.toArgb()
            textSize = 220f                // ~160pt compressed glyph
            typeface = Typeface.create(Typeface.DEFAULT, Typeface.BOLD)
        }
    }
    Canvas(modifier.size(width = 110.dp, height = 195.dp)) {
        drawIntoCanvas {
            it.nativeCanvas.drawText("$index", 0f, size.height * 0.92f, stroke)
        }
    }
}

@Composable
fun Top10Row(posters: List<String>, onPoster: (String) -> Unit) {
    Column(verticalArrangement = Arrangement.spacedBy(8.dp)) {
        Text(
            "Top 10 TV Shows Today",
            style = NetflixText.RowHeader,
            color = NetflixColors.TextPrimary,
            modifier = Modifier.padding(horizontal = 16.dp),
        )
        LazyRow(contentPadding = PaddingValues(horizontal = 16.dp)) {
            itemsIndexed(posters.take(10)) { i, url ->
                Row(verticalAlignment = Alignment.Bottom) {
                    Top10Numeral(i + 1)
                    PosterTile(
                        imageUrl = url,
                        width = 120.dp,
                        onClick = { onPoster(url) },
                        modifier = Modifier.offset(x = (-24).dp), // poster covers numeral's inner edge
                    )
                    Spacer(Modifier.width(8.dp))
                }
            }
        }
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Netflix's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 92%-opaque canvas surface with a 1dp top hairline. 4 tabs only — Netflix is view-only, no center Create. Active tint is **white** (filled icon), inactive is `#777777`.

```kotlin
@Composable
fun NetflixBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = NetflixColors.Canvas.copy(alpha = 0.92f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"       to Icons.Filled.Home,
            "New & Hot"  to Icons.Filled.PlayCircle,
            "My Netflix" to Icons.Filled.AccountCircle,
            "Downloads"  to Icons.Filled.DownloadForOffline,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = NetflixText.TabLabel) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = NetflixColors.TextPrimary,   // white
                    selectedTextColor = NetflixColors.TextPrimary,
                    unselectedIconColor = NetflixColors.TextTertiary, // #777777
                    unselectedTextColor = NetflixColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Netflix has none
                ),
            )
        }
    }
}
```

The **NETFLIX wordmark** is the only Home header — ship the red vector as `R.drawable.netflix_wordmark` and render it 22.dp tall in a transparent top app bar that fades to `Canvas.copy(alpha = 0.92f)` on scroll. No title text, no search field here.

## 6. Motion

Netflix motion is subtle — 250–400ms springs, never aggressive.

| Moment | Compose recipe |
|--------|----------------|
| Play tap | `animateFloatAsState` 1 → 0.98 with `spring(dampingRatio = 0.8f)`, `HapticFeedbackType.LongPress` |
| Poster → Detail | `SharedTransitionLayout` + `Modifier.sharedElement()` on the poster (Compose 1.7+), `spring(0.35f, 0.85f)`; fallback `AnimatedVisibility` cross-fade |
| Hero trailer | `Crossfade` from key art to `PlayerView` after a 2_000ms `delay` |
| My List add | `Animatable` rotation 0 → 90° + plus↔check cross-fade over 200ms, `HapticFeedbackType.Confirm` |
| Profile select | scale 1 → 1.1 → 1 `keyframes` spring, then full-screen `Crossfade` to Home |
| Rating thumb | scale 1 → 1.2 → 1 bounce, heavy haptic on double-thumb "Love" |

```kotlin
// My List add/remove — plus rotates and cross-fades to check
@Composable
fun MyListToggle(added: Boolean, onToggle: () -> Unit) {
    val rotation by animateFloatAsState(
        if (added) 90f else 0f,
        spring(dampingRatio = 0.7f, stiffness = 600f),
        label = "myListRot",
    )
    val haptics = LocalHapticFeedback.current
    IconButton(onClick = {
        haptics.performHapticFeedback(HapticFeedbackType.Confirm)
        onToggle()
    }) {
        Icon(
            if (added) Icons.Filled.Check else Icons.Filled.Add,
            contentDescription = if (added) "Remove from My List" else "Add to My List",
            tint = NetflixColors.TextPrimary, // plain white — never green
            modifier = Modifier.size(24.dp).graphicsLayer { rotationZ = rotation },
        )
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For the heavy double-thumbs "Love this!" use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.LONG_PRESS)` or a `Vibrator` `VibrationEffect.createOneShot(20, ...)`.

## 7. Icons

Netflix ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. The NETFLIX wordmark and Top 10 numerals are NOT icons — ship the wordmark as a vector drawable and stroke the numerals (§4).

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Plus (My List) | `plus` | `Icons.Filled.Add` |
| Checkmark (Added) | `checkmark` | `Icons.Filled.Check` |
| Info | `info.circle` | `Icons.Filled.Info` |
| Trailer | `play.rectangle` | `Icons.Filled.Movie` |
| Download | `arrow.down.to.line` | `Icons.Filled.Download` |
| Downloaded | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` (tint `#AAAAAA`, never green) |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Thumb up / down | `hand.thumbsup.fill` / `.thumbsdown.fill` | `Icons.Filled.ThumbUp` / `Icons.Filled.ThumbDown` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| New & Hot (tab) | `play.rectangle.on.rectangle.fill` | `Icons.Filled.PlayCircle` |
| My Netflix (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| Downloads (tab) | `arrow.down.circle.fill` | `Icons.Filled.DownloadForOffline` |
| Speaker (audio toggle) | `speaker.slash.fill` / `speaker.wave.2.fill` | `Icons.Filled.VolumeOff` / `Icons.Filled.VolumeUp` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Close (modal) | `xmark` | `Icons.Filled.Close` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; media3 + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the warm-dark canvas wants `WindowCompat` light-content system bars. The hero extends under the status bar (content behind the cutout); apply `Scaffold` insets so the tab bar clears the gesture nav and the wordmark sits below the camera cutout.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on synopsis, episode titles, settings. Pin layout-sensitive text (10sp tab labels, 11sp badges, Continue-Watching poster titles, scrubber timestamps) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: group each poster as one node with `Modifier.semantics(mergeDescendants = true)`; label it with the title and expose "Play {title}" as the primary action, "Add to My List" / "Info" as custom actions. Announce download state explicitly ("Downloaded", "Downloading 45%").
- **Touch targets**: Material guidance is 48.dp minimum. The 48.dp Play button clears it; ensure the 16dp/24dp icon-only buttons (download, more, thumbs) carry a 48.dp hit area via `IconButton` padding.
- **Contrast**: `#AAAAAA` on `#141414` passes WCAG AA at 13sp+. Validate the 10sp tab labels and 11sp `Badge` with a contrast checker and bump toward `#C7C7C7` if your build targets accessibility compliance.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, skip the 2s trailer auto-play and the hero cross-fade; substitute shared-element poster transitions with a plain `Crossfade`.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Netflix's brand requires the fixed `#141414` canvas and the single sacred `#E50914` red regardless of wallpaper. Kids profile is the only palette swap (brighter saturated tones, +4.dp touch targets).
