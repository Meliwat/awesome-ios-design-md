# Vimeo (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Vimeo's cinematic visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the player + Staff Pick feed, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Vimeo's near-black screening room, the single Vimeo Blue accent, the iconic Staff Pick gold star, the cinematic auto-fading player) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, ExoPlayer/Media3 instead of `AVPlayer`, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for thumbnails, and `androidx.media3` (ExoPlayer) for the player. Vimeo is dark-first; the dark scheme is canonical.

## 1. Color Tokens

```kotlin
// ui/theme/VimeoColors.kt
import androidx.compose.ui.graphics.Color

object VimeoColors {
    // Canvas & Surfaces (Dark — primary)
    val Canvas    = Color(0xFF0D0E12) // near-black blue ink — NOT pure black
    val Surface1  = Color(0xFF16181F)
    val Surface2  = Color(0xFF1F222B)
    val Divider   = Color(0xFF262A34)

    // Canvas & Surfaces (Light — rare parity)
    val LightCanvas   = Color(0xFFFFFFFF)
    val LightSurface1 = Color(0xFFF5F6F8)
    val LightDivider  = Color(0xFFE2E5EA)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFAEB6C2)
    val TextTertiary  = Color(0xFF6B7280)
    val OnBlue        = Color(0xFF04121A)

    // Brand (single accent)
    val Blue        = Color(0xFF00ADEF)
    val BlueLegacy  = Color(0xFF1AB7EA)
    val BluePressed = Color(0xFF008FC4)

    // Accent & curation
    val StaffGold  = Color(0xFFFFD24C) // Staff Pick badge ONLY
    val PlusPurple = Color(0xFF8B5CF6)

    // Semantic
    val Success = Color(0xFF2ECC71)
    val Error   = Color(0xFFFF4D4F)
    val Live    = Color(0xFFFF2D55)

    // Player scrim
    val Scrim = Color(0xD90D151D) // rgba(8,21,29,0.85)
}
```

Wire it into both schemes. Vimeo is dark-first — the dark scheme is the real product; light is parity only. Never enable Material You `dynamicColorScheme()` — the brand has exactly one accent (Vimeo Blue) and it must hold regardless of wallpaper.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme

private val VimeoDark = darkColorScheme(
    primary        = VimeoColors.Blue,
    onPrimary      = VimeoColors.OnBlue,
    background      = VimeoColors.Canvas,
    onBackground    = VimeoColors.TextPrimary,
    surface         = VimeoColors.Surface1,
    onSurface       = VimeoColors.TextPrimary,
    surfaceVariant  = VimeoColors.Surface2,
    onSurfaceVariant = VimeoColors.TextSecondary,
    outline         = VimeoColors.Divider,
    error           = VimeoColors.Error,
)

private val VimeoLight = lightColorScheme(
    primary      = VimeoColors.Blue,
    onPrimary    = VimeoColors.OnBlue,
    background    = VimeoColors.LightCanvas,
    onBackground  = Color(0xFF10131A),
    surface       = VimeoColors.LightSurface1,
    onSurface     = Color(0xFF10131A),
    outline       = VimeoColors.LightDivider,
    error         = VimeoColors.Error,
)

@Composable
fun VimeoTheme(
    dark: Boolean = true, // dark-first; pass isSystemInDarkTheme() only if you support light parity
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) VimeoDark else VimeoLight,
    typography = VimeoTypography,
    content = content,
)
```

## 2. Typography

Vimeo's UI face is **Inter** (SIL OFL — drop the TTFs in `res/font/`). Body weights 400–700; numeric values use tabular figures via a `FontFeatureSetting`.

```kotlin
// ui/theme/VimeoType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextGeometricTransform
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,    FontWeight.Normal),
    Font(R.font.inter_medium,     FontWeight.Medium),
    Font(R.font.inter_semibold,   FontWeight.SemiBold),
    Font(R.font.inter_bold,       FontWeight.Bold),
    Font(R.font.inter_extrabold,  FontWeight.ExtraBold),
)

// Tabular figures for all timecodes / counts
private val tnum = "tnum"

object VimeoText {
    val Display     = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val VideoTitle  = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 17.sp, lineHeight = 23.sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 22.sp)
    val BodyRead    = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 24.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val Creator     = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 18.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp)
    val Pill        = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Overline    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.6.sp)
    val Timecode    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 11.sp, fontFeatureSettings = tnum)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

val VimeoTypography = Typography(
    headlineLarge = VimeoText.Display,
    headlineMedium = VimeoText.ScreenTitle,
    titleLarge    = VimeoText.Section,
    titleMedium   = VimeoText.VideoTitle,
    bodyMedium    = VimeoText.Body,
    labelSmall    = VimeoText.Tab,
)
```

## 3. Signature Components

### Cinematic Video Player

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.fadeIn
import androidx.compose.animation.fadeOut
import androidx.compose.animation.core.tween
import androidx.compose.foundation.background
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.unit.dp
import kotlinx.coroutines.delay

@Composable
fun VimeoPlayer(elapsed: String, total: String, progress: Float) {
    var controls by remember { mutableStateOf(true) }
    LaunchedEffect(controls) {
        if (controls) { delay(3000); controls = false }
    }
    Box(
        Modifier
            .fillMaxWidth()
            .aspectRatio(16f / 9f)
            .clip(RoundedCornerShape(14.dp))
            .background(Brush.linearGradient(listOf(Color(0xFF16384A), Color(0xFF08151D))))
            .pointerInput(Unit) { detectTapGestures { controls = !controls } },
    ) {
        AnimatedVisibility(controls, enter = fadeIn(tween(200)), exit = fadeOut(tween(250)),
            modifier = Modifier.align(Alignment.Center)) {
            Box(
                Modifier
                    .size(52.dp).clip(RoundedCornerShape(50))
                    .background(VimeoColors.Canvas.copy(alpha = 0.55f)),
                contentAlignment = Alignment.Center,
            ) {
                Icon(Icons.Filled.PlayArrow, contentDescription = "Play",
                    tint = Color.White, modifier = Modifier.size(22.dp))
            }
        }
        AnimatedVisibility(controls, enter = fadeIn(tween(200)), exit = fadeOut(tween(250)),
            modifier = Modifier.align(Alignment.BottomCenter)) {
            Row(
                Modifier
                    .fillMaxWidth().height(34.dp)
                    .background(Brush.verticalGradient(listOf(Color.Transparent, VimeoColors.Scrim)))
                    .padding(horizontal = 12.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(8.dp),
            ) {
                Text(elapsed, style = VimeoText.Timecode, color = Color.White)
                ScrubBar(progress, Modifier.weight(1f))
                Text(total, style = VimeoText.Timecode, color = Color.White)
                Box(
                    Modifier
                        .clip(RoundedCornerShape(3.dp))
                        .padding(0.dp),
                ) {
                    Text("HD", style = VimeoText.Tab.copy(fontWeight = FontWeight.Bold, fontSize = 9.sp),
                        color = Color.White, modifier = Modifier.padding(horizontal = 4.dp, vertical = 1.dp))
                }
            }
        }
    }
}

@Composable
fun ScrubBar(progress: Float, modifier: Modifier = Modifier) {
    Box(modifier.height(16.dp), contentAlignment = Alignment.CenterStart) {
        Box(Modifier.fillMaxWidth().height(3.dp).clip(RoundedCornerShape(2.dp)).background(Color.White.copy(alpha = 0.22f)))
        Box(Modifier.fillMaxWidth(progress).height(3.dp).clip(RoundedCornerShape(2.dp)).background(VimeoColors.Blue))
        BoxWithConstraints {
            Box(
                Modifier
                    .offset(x = maxWidth * progress - 4.5.dp)
                    .size(9.dp).clip(RoundedCornerShape(50)).background(Color.White),
            )
        }
    }
}
```

Bind the visual to `androidx.media3.exoplayer.ExoPlayer`; render frames into an `AndroidView { PlayerView(it).apply { useController = false } }` so the Compose overlay above owns all chrome. Use `resizeMode = AspectRatioFrameLayout.RESIZE_MODE_FIT` — never crop a filmmaker's frame.

### Staff Pick Badge

```kotlin
import androidx.compose.material.icons.filled.Star

@Composable
fun StaffPickBadge(compact: Boolean = false, modifier: Modifier = Modifier) {
    if (compact) {
        Box(
            modifier.size(16.dp).clip(RoundedCornerShape(50)).background(VimeoColors.StaffGold),
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Star, null, tint = VimeoColors.OnBlue, modifier = Modifier.size(9.dp)) }
    } else {
        Row(
            modifier
                .clip(RoundedCornerShape(50))
                .background(VimeoColors.StaffGold)
                .padding(horizontal = 8.dp, vertical = 3.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(4.dp),
        ) {
            Icon(Icons.Filled.Star, null, tint = VimeoColors.OnBlue, modifier = Modifier.size(10.dp))
            Text("Staff Pick", style = VimeoText.Pill, color = VimeoColors.OnBlue)
        }
    }
}
```

### Curated Watch Feed Row

```kotlin
@Composable
fun WatchFeedRow(title: String, creator: String, stats: String, duration: String) {
    Row(
        Modifier.fillMaxWidth().padding(bottom = 14.dp),
        horizontalArrangement = Arrangement.spacedBy(11.dp),
    ) {
        Box(
            Modifier
                .width(124.dp).aspectRatio(16f / 9f)
                .clip(RoundedCornerShape(8.dp))
                .background(Brush.linearGradient(listOf(Color(0xFF4A6B7C), Color(0xFF1E323D)))),
        ) {
            StaffPickBadge(compact = true, Modifier.align(Alignment.TopStart).padding(5.dp))
            Text(
                duration, style = VimeoText.Tab.copy(fontWeight = FontWeight.Bold, fontSize = 9.sp),
                color = Color.White,
                modifier = Modifier
                    .align(Alignment.BottomEnd).padding(5.dp)
                    .clip(RoundedCornerShape(3.dp)).background(Color.Black.copy(alpha = 0.7f))
                    .padding(horizontal = 4.dp, vertical = 1.dp),
            )
        }
        Column {
            Text(title, style = VimeoText.Creator, color = VimeoColors.TextPrimary, maxLines = 2)
            Text("by $creator", style = VimeoText.Meta, color = VimeoColors.TextTertiary,
                modifier = Modifier.padding(top = 4.dp))
            Text(stats, style = VimeoText.Meta, color = VimeoColors.TextTertiary,
                modifier = Modifier.padding(top = 6.dp))
        }
    }
}
```

### Follow Pill

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun FollowPill() {
    var following by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current
    Box(
        Modifier
            .clip(RoundedCornerShape(50))
            .background(if (following) VimeoColors.Surface2 else VimeoColors.Blue)
            .then(if (following) Modifier.border(1.dp, VimeoColors.Divider, RoundedCornerShape(50)) else Modifier)
            .clickable { haptics.performHapticFeedback(HapticFeedbackType.LongPress); following = !following }
            .padding(horizontal = 16.dp, vertical = 7.dp),
    ) {
        Text(
            if (following) "Following" else "Follow",
            style = VimeoText.Pill,
            color = if (following) VimeoColors.TextPrimary else VimeoColors.OnBlue,
        )
    }
}
```

## 4. Video Player Controls + Curated Feed

The player owns its own minimal chrome (center play, scrubber, timecodes, HD chip); the curated feed below is a `LazyColumn` of `WatchFeedRow`s with a "STAFF PICKS" `Overline` header. Keep the player controls in a single `AnimatedVisibility` block so they fade together after 3s idle and return on tap — matching iOS exactly.

```kotlin
@Composable
fun WatchScreen(picks: List<Pick>) {
    LazyColumn(
        Modifier.fillMaxSize().background(VimeoColors.Canvas),
        contentPadding = PaddingValues(horizontal = 16.dp, vertical = 12.dp),
    ) {
        item { VimeoPlayer("1:24", "4:07", 0.42f); Spacer(Modifier.height(14.dp)) }
        item {
            Text("STAFF PICKS", style = VimeoText.Overline, color = VimeoColors.TextSecondary)
            Spacer(Modifier.height(12.dp))
        }
        items(picks) { p -> WatchFeedRow(p.title, p.creator, p.stats, p.duration) }
    }
}

data class Pick(val title: String, val creator: String, val stats: String, val duration: String)
```

## 5. Navigation

Vimeo has minimal chrome: a left-aligned wordmark top bar and a 5-slot bottom strip with an emphasized center Upload action. On Android model the strip as a `NavigationBar`; there is no Material tint pill — active is just Vimeo Blue.

```kotlin
@Composable
fun VimeoBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = VimeoColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"    to Icons.Filled.Home,
            "Search"  to Icons.Filled.Search,
            "Upload"  to Icons.Filled.PlayCircle,   // center, emphasized
            "Inbox"   to Icons.Filled.Email,
            "Profile" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            val isUpload = i == 2
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    Icon(icon, contentDescription = label,
                        tint = if (isUpload) VimeoColors.Blue else androidx.compose.ui.graphics.Color.Unspecified,
                        modifier = Modifier.size(if (isUpload) 26.dp else 22.dp))
                },
                label = if (isUpload) null else { { Text(label, style = VimeoText.Tab) } },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = VimeoColors.Blue,
                    selectedTextColor   = VimeoColors.Blue,
                    unselectedIconColor = VimeoColors.TextTertiary,
                    unselectedTextColor = VimeoColors.TextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill — Vimeo has none
                ),
            )
        }
    }
}
```

The top bar is a `TopAppBar` with the Vimeo wordmark as a leading `title` (NOT centered), a trailing search `IconButton`, and a notifications `IconButton`; `containerColor = Color.Transparent` over the player, opaque `Canvas` once content scrolls under it.

## 6. Motion

Vimeo motion is restrained and cinematic — controls breathe in and out of the frame; nothing bounces gratuitously.

| Moment | Compose recipe |
|--------|----------------|
| Player controls fade | `AnimatedVisibility` `fadeIn(tween(200))` / `fadeOut(tween(250))`; auto-hide via `LaunchedEffect { delay(3000); controls = false }` |
| Scrub knob grow | `animateDpAsState` 9.dp → 16.dp `tween(120)` on drag-start |
| Follow toggle | `animateColorAsState` background `tween(200)` + `Crossfade` label |
| Like heart | `animateFloatAsState` scale 1f → 1.25f → 1f via keyframes `tween(260)` |
| Thumbnail load | Coil `crossfade(220)` in `ImageRequest` |
| Tab switch | content `Crossfade(tween(200))`; active tint changes instantly |
| Sheet present | `ModalBottomSheet` default slide; 20.dp top corners |
| Pull to refresh | `PullToRefreshContainer` tinted `VimeoColors.Blue` |

```kotlin
// Like heart — the only spring-ish moment
val scale by animateFloatAsState(
    targetValue = if (liked) 1f else 1f,
    animationSpec = keyframes { durationMillis = 260; 1.25f at 130 },
    label = "likePulse",
)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft tick on Follow, scrub-start, and Like. Use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` for tab selection. Auto-save / buffering is silent — no motion, no haptic.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Ship the Vimeo "v" wordmark as a vector drawable (`res/drawable/ic_vimeo_wordmark.xml`); the Staff Pick mark is `Icons.Filled.Star` tinted `StaffGold`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Upload (center) | `play.circle.fill` | `Icons.Filled.PlayCircle` |
| Inbox (tab) | `envelope` | `Icons.Filled.Email` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Staff Pick | `star.fill` | `Icons.Filled.Star` |
| Notifications | `bell` | `Icons.Filled.Notifications` |
| Like | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Icons.Filled.Favorite` |
| Comment | `bubble.left` | `Icons.Filled.ChatBubbleOutline` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Watch Later | `clock` | `Icons.Filled.Schedule` |
| Fullscreen | `arrow.up.left.and.arrow.down.right` | `Icons.Filled.Fullscreen` |
| Captions | `captions.bubble` | `Icons.Filled.ClosedCaption` |
| Settings (player) | `gearshape` | `Icons.Filled.Settings` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; Media3/ExoPlayer + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the dark canvas wants light-content system bars. In landscape, hide system bars and the nav bar — go full-bleed cinema; the player controls must inset for the camera cutout and gesture nav.
- **Font scaling**: `sp` honors the user's scale on titles, body, descriptions. Pin layout-critical text (timecodes, 10sp tab labels, overlines, pill text, duration/quality chips) — derive from `dp` or fix `fontScale = 1f` for those nodes. Apply `fontFeatureSettings = "tnum"` to every timecode and count.
- **TalkBack**: label the player node "Video player, {title}, {elapsed} of {total}"; expose the scrubber via `Modifier.semantics { progressBarRangeInfo = ...; setProgress { ... } }` so it's adjustable; label the Staff Pick badge "Staff Pick, editor selected"; the center Upload item gets `contentDescription = "Upload a video"`.
- **Touch targets**: Material guidance is 48.dp. Give the scrubber knob a 48.dp tall hit band, the 22.dp tab icons a 48.dp hit, the Follow pill ≥ 36.dp height.
- **Contrast**: `#FFFFFF` and `#AEB6C2` on `#0D0E12` pass WCAG AA; `#04121A` on `#00ADEF` and on `#FFD24C` pass AA — never put white text on the gold Staff Pick badge.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the controls fade (instant toggle) and the like pulse (use a plain state change); keep the scrubber feedback.
- **Dark mode**: the dark scheme is canonical (`#0D0E12`, NOT pure black). Do **not** enable Material You `dynamicColorScheme()` — Vimeo's single-accent identity (Vimeo Blue + the sacred Staff Gold) must hold regardless of the device wallpaper.
- **Media**: enable background playback + PiP via a `MediaSessionService`; `RESIZE_MODE_FIT` always — Vimeo's color-critical audience must never see a cropped or tinted frame.
