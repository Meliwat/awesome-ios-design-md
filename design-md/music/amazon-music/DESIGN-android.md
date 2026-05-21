# Amazon Music (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Amazon Music's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the full-screen player + X-Ray lyrics panel, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the deep teal-navy `#0C1B22` canvas, the cyan-glow play button, the synced X-Ray lyrics panel, cyan drawn from the canvas hue) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `LazyColumn` lyric list with auto-scroll, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for artwork. No Palette extraction — Amazon Music's palette is a fixed teal system, not art-derived. Amazon Music is dark-first; the dark scheme is canonical.

## 1. Color Tokens

```kotlin
// ui/theme/AmazonColors.kt
import androidx.compose.ui.graphics.Color

object AmazonColors {
    // Canvas & Surfaces (dark — the primary mode)
    val Canvas      = Color(0xFF0C1B22) // deep teal-navy — NOT neutral grey
    val Surface1    = Color(0xFF122A33)
    val Surface2    = Color(0xFF1A3742)
    val Divider     = Color(0xFF234653)
    val GradientTop = Color(0xFF16404C)
    val TabBar      = Color(0xFF081218)

    // Brand
    val Cyan        = Color(0xFF00A8E1) // structural accent — play / scrubber / CTA
    val CyanBright  = Color(0xFF25D1DA) // active lyric, active tab, X-Ray badge
    val CyanPressed = Color(0xFF0086B3)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF9FB6BF) // teal-tinted grey — NOT neutral
    val TextTertiary  = Color(0xFF6B8693)
    val OnBright      = Color(0xFF042730) // dark-on-bright for the X-Ray badge

    // Semantic
    val Success = Color(0xFF2EC4B6)
    val Error   = Color(0xFFFF6B6B)

    // X-Ray panel
    val XRayFill   = Color(0x1200A8E1) // ~7% cyan
    val XRayBorder = Color(0x3825D1DA) // ~22% bright cyan
}

// Player background gradient stops (top → bottom)
val PlayerGradient = listOf(
    Color(0xFF1C4A57), Color(0xFF163A45), Color(0xFF0F2730), AmazonColors.Canvas,
)
```

Wire it into a **dark-first** scheme. Amazon Music is dark-first on iOS; mirror that and keep the tint.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme

private val AmazonScheme = darkColorScheme(
    primary        = AmazonColors.Cyan,        // cyan IS the action color
    onPrimary      = AmazonColors.TextPrimary,
    background      = AmazonColors.Canvas,
    onBackground    = AmazonColors.TextPrimary,
    surface         = AmazonColors.Surface1,
    onSurface       = AmazonColors.TextPrimary,
    surfaceVariant  = AmazonColors.Surface2,
    outline         = AmazonColors.Divider,
    error           = AmazonColors.Error,
    secondary       = AmazonColors.CyanBright, // bright cyan for active states
)

@Composable
fun AmazonMusicTheme(content: @Composable () -> Unit) = MaterialTheme(
    colorScheme = AmazonScheme, // dark-first — never neutralize the teal tint
    typography  = AmazonTypography,
    content     = content,
)
```

## 2. Typography (M3)

Amazon Music uses **Amazon Ember** (Amazon's corporate face, used across Amazon.com / Kindle / Alexa / Prime Video). Drop the Ember TTFs in `res/font/`; the only documented substitute is Inter. Body 400, rows 500, lyric line 600, headers/buttons 700, greeting 800.

```kotlin
// ui/theme/AmazonType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Ember = FontFamily(
    Font(R.font.amazon_ember_regular, FontWeight.Normal),
    Font(R.font.amazon_ember_medium,  FontWeight.Medium),
    Font(R.font.amazon_ember_bold,    FontWeight.Bold),
    Font(R.font.amazon_ember_heavy,   FontWeight.ExtraBold),
)

object AmazonText {
    val Greeting    = TextStyle(Ember, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val PlayerTitle = TextStyle(Ember, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Ember, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(Ember, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Ember, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val RowTitle    = TextStyle(Ember, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 20.sp)
    val LyricLine   = TextStyle(Ember, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 23.sp)
    val Subtitle    = TextStyle(Ember, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val XRayLabel   = TextStyle(Ember, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.6.sp)
    val XRayBadge   = TextStyle(Ember, fontWeight = FontWeight.ExtraBold, fontSize = 9.sp,  lineHeight = 9.sp,  letterSpacing = 0.5.sp)
    val Chip        = TextStyle(Ember, fontWeight = FontWeight.Medium,    fontSize = 13.sp, lineHeight = 13.sp)
    val Timestamp   = TextStyle(Ember, fontWeight = FontWeight.Normal,    fontSize = 11.sp, lineHeight = 11.sp)
    val Tab         = TextStyle(Ember, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(Ember, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp)
}

val AmazonTypography = Typography(
    headlineLarge = AmazonText.Greeting,
    headlineMedium = AmazonText.Section,
    titleMedium   = AmazonText.CardTitle,
    bodyMedium    = AmazonText.Body,
    labelSmall    = AmazonText.Tab,
)
```

## 3. Signature Components

### Full-Screen Player (gradient + art + meta + X-Ray)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import androidx.compose.foundation.shape.RoundedCornerShape
import coil.compose.AsyncImage

@Composable
fun Player(artUrl: String, track: String, artist: String) {
    Column(
        Modifier.fillMaxSize().background(Brush.verticalGradient(PlayerGradient))
    ) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 20.dp, vertical = 6.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Icon(Icons.Filled.KeyboardArrowDown, "Dismiss", tint = AmazonColors.TextPrimary, modifier = Modifier.size(22.dp))
            Text("PLAYING FROM PLAYLIST", style = AmazonText.XRayLabel.copy(letterSpacing = 0.7.sp), color = AmazonColors.TextSecondary)
            Icon(Icons.Filled.MoreVert, "More", tint = AmazonColors.TextPrimary, modifier = Modifier.size(22.dp))
        }

        AsyncImage(
            model = artUrl,
            contentDescription = "Album art",
            modifier = Modifier.size(196.dp).align(Alignment.CenterHorizontally).padding(top = 18.dp).clip(RoundedCornerShape(6.dp)),
            contentScale = ContentScale.Crop,
        )

        Row(
            Modifier.fillMaxWidth().padding(horizontal = 22.dp).padding(top = 16.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.Bottom,
        ) {
            Column {
                Text(track, style = AmazonText.PlayerTitle, color = AmazonColors.TextPrimary)
                Spacer(Modifier.height(3.dp))
                Text(artist, style = AmazonText.Subtitle, color = AmazonColors.TextSecondary)
            }
            Icon(Icons.Filled.Add, "Add to library", tint = AmazonColors.CyanBright, modifier = Modifier.size(26.dp))
        }

        XRayLyricsPanel(
            lines = listOf("Daylight, I wake up feeling like", "But stay woke", "Niggas creepin'", "They gon’ find you"),
            currentIndex = 1,
        )
    }
}
```

### X-Ray Lyrics Panel (Signature, auto-scrolling)

```kotlin
import androidx.compose.animation.animateColorAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.itemsIndexed
import androidx.compose.foundation.lazy.rememberLazyListState
import androidx.compose.runtime.*

@Composable
fun XRayLyricsPanel(lines: List<String>, currentIndex: Int, onSeekLine: (Int) -> Unit = {}) {
    val listState = rememberLazyListState()
    // Auto-scroll so the current line stays centered
    LaunchedEffect(currentIndex) {
        listState.animateScrollToItem(index = currentIndex.coerceAtLeast(0))
    }

    Column(
        Modifier
            .padding(horizontal = 18.dp).padding(top = 16.dp)
            .clip(RoundedCornerShape(10.dp))
            .background(AmazonColors.XRayFill)
            .border(1.dp, AmazonColors.XRayBorder, RoundedCornerShape(10.dp))
            .padding(horizontal = 16.dp, vertical = 14.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(7.dp)) {
            Box(
                Modifier.clip(RoundedCornerShape(4.dp)).background(AmazonColors.CyanBright)
                    .padding(horizontal = 7.dp, vertical = 3.dp)
            ) { Text("X-RAY", style = AmazonText.XRayBadge, color = AmazonColors.OnBright) }
            Text("LYRICS", style = AmazonText.XRayLabel, color = AmazonColors.TextSecondary)
        }
        Spacer(Modifier.height(10.dp))
        LazyColumn(state = listState, modifier = Modifier.heightIn(max = 160.dp)) {
            itemsIndexed(lines) { i, line ->
                val target = when {
                    i == currentIndex -> AmazonColors.CyanBright
                    i == currentIndex + 1 -> AmazonColors.TextSecondary
                    else -> AmazonColors.TextTertiary
                }
                val c by animateColorAsState(target, tween(180), label = "lyricColor")
                Text(
                    line,
                    style = AmazonText.LyricLine,
                    color = c,
                    modifier = Modifier.fillMaxWidth().clickable { onSeekLine(i) }.padding(vertical = 3.dp),
                )
            }
        }
    }
}
```

### Scrubber

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.gestures.detectDragGestures
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.input.pointer.pointerInput

@Composable
fun Scrubber() {
    var progress by remember { mutableFloatStateOf(0.38f) }
    var dragging by remember { mutableStateOf(false) }

    Column(Modifier.padding(horizontal = 22.dp).padding(top = 16.dp)) {
        Canvas(
            Modifier.fillMaxWidth().height(16.dp).pointerInput(Unit) {
                detectDragGestures(
                    onDragStart = { dragging = true },
                    onDragEnd = { dragging = false },
                    onDrag = { c, _ -> progress = (c.position.x / size.width).coerceIn(0f, 1f) },
                )
            }
        ) {
            val cy = size.height / 2
            drawLine(Color.White.copy(alpha = 0.16f), Offset(0f, cy), Offset(size.width, cy), strokeWidth = 4.dp.toPx())
            drawLine(AmazonColors.Cyan, Offset(0f, cy), Offset(size.width * progress, cy), strokeWidth = 4.dp.toPx())
            drawCircle(AmazonColors.Cyan.copy(alpha = 0.2f), (if (dragging) 12 else 10).dp.toPx(), Offset(size.width * progress, cy))
            drawCircle(AmazonColors.Cyan, 6.5.dp.toPx(), Offset(size.width * progress, cy))
        }
        Row(Modifier.fillMaxWidth().padding(top = 7.dp), horizontalArrangement = Arrangement.SpaceBetween) {
            Text("1:54", style = AmazonText.Timestamp, color = AmazonColors.TextSecondary)
            Text("-3:33", style = AmazonText.Timestamp, color = AmazonColors.TextSecondary)
        }
    }
}
```

### Transport Controls (cyan play button with glow)

```kotlin
import androidx.compose.ui.draw.shadow

@Composable
fun Transport() {
    var playing by remember { mutableStateOf(true) }
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 30.dp).padding(top = 12.dp),
        horizontalArrangement = Arrangement.SpaceBetween,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(Icons.Filled.Repeat, "Repeat", tint = AmazonColors.TextSecondary, modifier = Modifier.size(22.dp))
        Icon(Icons.Filled.FastRewind, "Previous", tint = AmazonColors.TextPrimary, modifier = Modifier.size(24.dp))
        Box(
            Modifier
                .size(60.dp)
                .shadow(14.dp, RoundedCornerShape(999.dp), spotColor = AmazonColors.Cyan.copy(alpha = 0.55f))
                .clip(RoundedCornerShape(999.dp))
                .background(AmazonColors.Cyan)
                .clickable { playing = !playing },
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                if (playing) Icons.Filled.Pause else Icons.Filled.PlayArrow,
                contentDescription = if (playing) "Pause" else "Play",
                tint = Color.White, // white glyph on cyan — cyan IS the action
                modifier = Modifier.size(24.dp),
            )
        }
        Icon(Icons.Filled.FastForward, "Next", tint = AmazonColors.TextPrimary, modifier = Modifier.size(24.dp))
        Icon(Icons.Filled.Shuffle, "Shuffle", tint = AmazonColors.TextSecondary, modifier = Modifier.size(22.dp))
    }
}
```

## 4. Core Navigation

Amazon Music has a 4-tab bottom strip (Home / Find / Podcasts / Library) and a full-screen player reached from the mini-player. Active is **bright cyan** `#25D1DA` — no Material tint pill.

```kotlin
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun AmazonBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = AmazonColors.TabBar, tonalElevation = 0.dp) {
        val items = listOf(
            "Home" to Icons.Filled.Home,
            "Find" to Icons.Filled.Search,
            "Podcasts" to Icons.Filled.Mic,
            "Library" to Icons.Filled.LibraryMusic,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = AmazonText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = AmazonColors.CyanBright,  // bright cyan, not the structural cyan
                    selectedTextColor = AmazonColors.CyanBright,
                    unselectedIconColor = AmazonColors.TextTertiary,
                    unselectedTextColor = AmazonColors.TextTertiary,
                    indicatorColor = Color.Transparent,           // no Material pill
                ),
            )
        }
    }
}
```

The mini-player sits above this bar (`Surface2` `#1A3742`, 8.dp corners, a 2.dp `Cyan` progress line at its bottom edge); tapping it pushes up the full-screen player.

## 5. Motion

Amazon Music motion is calm. The player's depth is the gradient + the cyan play-button glow, not Material elevation.

| Moment | Compose recipe |
|--------|----------------|
| Player present | mini-bar → full screen: `AnimatedVisibility` `slideInVertically(tween(300))` from bottom |
| X-Ray lyric recolor | per-line `animateColorAsState(tween(180))` driven by `currentIndex` |
| X-Ray auto-scroll | `LaunchedEffect(currentIndex) { listState.animateScrollToItem(currentIndex) }` |
| X-Ray seek tap | clicked line briefly forced to `CyanBright`, then released to its computed color |
| Scrubber drag | thumb/halo radius keyed off `dragging` state, ~`tween(80)` in / `tween(150)` out |
| Gradient + art change | `Crossfade(targetState = artUrl, animationSpec = tween(350))` |
| Tab switch | instant icon recolor to bright cyan — no slide, no pill |
| Shelf scroll | `LazyRow` momentum with rubber-band overscroll |

```kotlin
// Track-change crossfade for art + gradient seed
Crossfade(targetState = artUrl, animationSpec = tween(350), label = "trackArt") { url ->
    AsyncImage(model = url, contentDescription = null, modifier = Modifier.size(196.dp).clip(RoundedCornerShape(6.dp)))
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for the soft tick on play/pause, X-Ray line tap-to-seek, and scrubber touch-down. Track auto-advance is silent.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. The play button is cyan with a white glyph; the X-Ray badge is a styled `Box`, not an icon.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Find (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Podcasts (tab) | `mic.fill` | `Icons.Filled.Mic` |
| Library (tab) | `books.vertical.fill` | `Icons.Filled.LibraryMusic` |
| Dismiss player | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Overflow | `ellipsis` | `Icons.Filled.MoreVert` |
| Play / Pause | `play.fill` / `pause.fill` | `Icons.Filled.PlayArrow` / `Icons.Filled.Pause` |
| Next / Previous | `forward.fill` / `backward.fill` | `Icons.Filled.FastForward` / `Icons.Filled.FastRewind` |
| Repeat | `repeat` / `repeat.1` | `Icons.Filled.Repeat` / `Icons.Filled.RepeatOne` |
| Shuffle | `shuffle` | `Icons.Filled.Shuffle` |
| Add to library | `plus` / `checkmark` | `Icons.Filled.Add` / `Icons.Filled.Check` |
| Queue | `list.bullet` | `Icons.Filled.QueueMusic` |
| Lyrics toggle | `quote.bubble` | `Icons.Filled.Lyrics` |
| Equalizer / Atmos | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Cast / devices | `airplayaudio` | `Icons.Filled.Cast` |
| Like | `heart.fill` | `Icons.Filled.Favorite` |
| Download | `arrow.down.circle` | `Icons.Filled.Download` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (`compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`). The X-Ray panel uses a `LazyColumn` with `animateScrollToItem` — comfortable from API 24
- **Edge-to-edge**: call `enableEdgeToEdge()`; the deep teal-navy canvas wants light-content system bars. The full-screen player draws behind the status bar (the gradient extends to the top); inset only the top-bar controls
- **Dark-first**: never branch on `isSystemInDarkTheme()` for the palette and never enable `dynamicColorScheme()` — the teal canvas and cyan accent are fixed brand; Material You would harmonize the cyan to wallpaper, which breaks the identity. Keep all surfaces teal-tinted, never neutral grey
- **Font scaling**: `sp` honors the user scale on greeting, section, body, row title, and lyric line; pin the "X-RAY" badge, X-Ray label, 10sp tab labels, and timestamps via `dp`-derived sizing or a fixed-`fontScale` density. The lyric panel must grow taller and keep the current line centered as text scales
- **Ember**: bundle the explicit weights (Regular/Medium/Bold/Heavy); the only documented substitute is Inter (its metrics track Ember) — never a serif
- **TalkBack**: label the play button "Play"/"Pause"; the X-Ray panel as "Lyrics"; each lyric line via `Modifier.semantics { role = Role.Button; onClick(label = "Play from this line") { ...; true } }`; the scrubber via `progressBarRangeInfo` plus Seek-forward / Seek-back custom actions
- **Touch targets**: Material guidance is 48.dp — give the 22–24.dp transport icons and the 13.dp scrubber thumb a 48.dp hit area; the 60.dp play button already exceeds it; lyric lines are full-width and ≥ 44.dp tall
- **Contrast**: `#FFFFFF` and `#9FB6BF` on `#0C1B22` pass WCAG AA; `#6B8693` (dimmed lyric lines) is decorative context — the *current* line is always high-contrast bright cyan. The "X-RAY" badge keeps `#042730` on `#25D1DA` (dark-on-bright) for AA — never invert it
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the gradient/art `Crossfade` and the lyric auto-scroll animation (jump instantly); keep the current-line recolor (it conveys playback position)
- **Player gradient**: use `Brush.verticalGradient(PlayerGradient)`; the cyan play-button glow is a `Modifier.shadow(spotColor = Cyan.copy(alpha = 0.55f))` — it is the focal cue, keep it
