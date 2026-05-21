# Audible (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Audible's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, the serif/sans `Typography` pairing, the signature speed-dial + 30s-skip player, the cover progress ring, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Audible's warm charcoal reading room, single-orange accent, serif-headline + sans-body pairing, 30s-skip + speed-dial transport) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Canvas` arc instead of a SwiftUI trimmed `Circle`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/AudibleColors.kt
import androidx.compose.ui.graphics.Color

object AudibleColors {
    // Canvas & Surfaces
    val Canvas   = Color(0xFF1A1A1A)
    val Surface1 = Color(0xFF2A2A2A)
    val Surface2 = Color(0xFF343434)
    val Divider  = Color(0xFF3A3A3A)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFB0B0B0)
    val TextTertiary  = Color(0xFF6E6E6E)

    // Brand
    val Orange        = Color(0xFFFF9900)
    val OrangePressed = Color(0xFFE68A00)
    val OrangeGlow    = Color(0xFFFF9900).copy(alpha = 0.28f)
    val ErrorRed      = Color(0xFFE5484D)
}
```

Wire it into a Material 3 `darkColorScheme`. Audible's player/library are warm-dark by default; do not provide a light scheme for those flows.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val AudibleScheme = darkColorScheme(
    primary        = AudibleColors.Orange,
    onPrimary      = AudibleColors.Canvas,    // dark glyph on orange
    background      = AudibleColors.Canvas,
    onBackground    = AudibleColors.TextPrimary,
    surface         = AudibleColors.Surface1,
    onSurface       = AudibleColors.TextPrimary,
    surfaceVariant  = AudibleColors.Surface2,
    outline         = AudibleColors.Divider,
    error           = AudibleColors.ErrorRed,
)

@Composable
fun AudibleTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = AudibleScheme, typography = AudibleTypography, content = content)
```

## 2. Typography

Two families, strict roles. **Playfair Display** for headings/titles only; **Inter** for body/UI. Both on Google Fonts — drop the TTFs in `res/font/` (lowercase, snake_case). Fall back to serif / system.

```kotlin
// ui/theme/AudibleType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val PlayfairDisplay = FontFamily(Font(R.font.playfair_display_bold, FontWeight.Bold))   // headings ONLY
val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object AudibleText {
    // Headings — Playfair Display (serif) ONLY
    val TitleLarge = TextStyle(PlayfairDisplay, fontWeight = FontWeight.Bold, fontSize = 30.sp, lineHeight = 35.sp)
    val BookTitle  = TextStyle(PlayfairDisplay, fontWeight = FontWeight.Bold, fontSize = 26.sp, lineHeight = 31.sp)
    val Section    = TextStyle(PlayfairDisplay, fontWeight = FontWeight.Bold, fontSize = 22.sp, lineHeight = 26.sp)
    val CardTitle  = TextStyle(PlayfairDisplay, fontWeight = FontWeight.Bold, fontSize = 17.sp, lineHeight = 22.sp)
    val MiniTitle  = TextStyle(PlayfairDisplay, fontWeight = FontWeight.Bold, fontSize = 14.sp, lineHeight = 18.sp)

    // Body / UI — Inter (sans)
    val Author     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val Narrator   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 17.sp, letterSpacing = 0.2.sp)
    val Chapter    = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 21.sp)
    val Body       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 23.sp)
    val Captions   = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 18.sp, lineHeight = 29.sp)
    val Meta       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val LabelUpper = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.8.sp)
    val Button     = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = 0.2.sp)
    val ButtonSec  = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 18.sp)
    val Speed      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 18.sp)
    val Tab        = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val AudibleTypography = Typography(
    headlineLarge = AudibleText.TitleLarge,
    headlineSmall = AudibleText.Section,
    titleMedium   = AudibleText.Chapter,
    bodyMedium    = AudibleText.Body,
    labelSmall    = AudibleText.Tab,
)
```

## 3. Signature Components

### Cover with Orange Progress Ring

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp

@Composable
fun CoverProgressRing(
    artworkUrl: String,
    progress: Float,
    size: Dp = 280.dp,
    ring: Dp = 4.dp,
    modifier: Modifier = Modifier,
) {
    Box(modifier.size(size), contentAlignment = Alignment.Center) {
        Canvas(Modifier.size(size)) {
            val stroke = ring.toPx()
            val arcSize = Size(this.size.width - stroke, this.size.height - stroke)
            val topLeft = Offset(stroke / 2, stroke / 2)
            drawArc(AudibleColors.Divider, 0f, 360f, false, topLeft, arcSize, style = Stroke(stroke))
            drawArc(
                AudibleColors.Orange, -90f, 360f * progress, false,
                topLeft, arcSize, style = Stroke(stroke, cap = StrokeCap.Round),
            )
        }
        AsyncImage(
            model = artworkUrl,
            contentDescription = null,
            modifier = Modifier
                .size(size - ring * 6)
                .shadow(36.dp, RoundedCornerShape(10.dp), spotColor = Color.Black.copy(alpha = 0.5f))
                .clip(RoundedCornerShape(10.dp)),
            contentScale = ContentScale.Crop,
        )
    }
}
```

### Speed-Dial + 30s-Skip Transport (Signature)

```kotlin
@Composable
fun AudiblePlayButton(isPlaying: Boolean, onClick: () -> Unit, size: Dp = 72.dp) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.93f else 1f, spring(dampingRatio = 0.72f), label = "play")
    val haptics = LocalHapticFeedback.current
    Box(
        Modifier
            .size(size).scale(scale)
            .shadow(22.dp, CircleShape, spotColor = AudibleColors.Orange.copy(alpha = 0.28f))
            .clip(CircleShape)
            .background(if (pressed) AudibleColors.OrangePressed else AudibleColors.Orange)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            if (isPlaying) Icons.Filled.Pause else Icons.Filled.PlayArrow,
            contentDescription = if (isPlaying) "Pause" else "Play",
            tint = AudibleColors.Canvas, // dark glyph on orange
            modifier = Modifier.size(size * 0.44f),
        )
    }
}

@Composable
fun SkipButton(forward: Boolean, onClick: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    var flash by remember { mutableStateOf(false) }
    val tint by animateColorAsState(if (flash) AudibleColors.Orange else Color.White, tween(180), label = "flash")
    val scope = rememberCoroutineScope()
    Box(
        Modifier
            .size(44.dp)
            .clickable(remember { MutableInteractionSource() }, indication = null) {
                flash = true
                scope.launch { kotlinx.coroutines.delay(200); flash = false }
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        // Replace30 / Forward30 carry the literal "30" — Audible's signature transport
        Icon(
            if (forward) Icons.Filled.Forward30 else Icons.Filled.Replay30,
            contentDescription = if (forward) "Skip forward 30 seconds" else "Skip back 30 seconds",
            tint = tint,
            modifier = Modifier.size(30.dp),
        )
    }
}
```

### Speed Dial Sheet

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun SpeedDialSheet(speed: Float, onSpeed: (Float) -> Unit, onDismiss: () -> Unit) {
    val sheet = rememberModalBottomSheetState()
    val haptics = LocalHapticFeedback.current
    var lastTick by remember { mutableStateOf(speed) }
    val presets = listOf(1.0f, 1.25f, 1.5f, 2.0f)

    ModalBottomSheet(
        onDismissRequest = onDismiss,
        sheetState = sheet,
        containerColor = AudibleColors.Surface1,
        shape = RoundedCornerShape(topStart = 16.dp, topEnd = 16.dp),
    ) {
        Column(
            Modifier.padding(20.dp).fillMaxWidth(),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(24.dp),
        ) {
            Text(
                "%.2f×".format(speed).trimEnd('0').trimEnd('.') + "×".takeIf { false }.orEmpty(),
                style = AudibleText.Speed.copy(fontSize = 22.sp),
                color = AudibleColors.Orange,
            )
            Slider(
                value = speed, onValueChange = {
                    if (Math.round(it * 4) != Math.round(lastTick * 4)) {
                        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                        lastTick = it
                    }
                    onSpeed(it)
                },
                valueRange = 0.5f..3.5f, steps = ((3.5f - 0.5f) / 0.05f).toInt() - 1,
                colors = SliderDefaults.colors(
                    thumbColor = AudibleColors.Orange,
                    activeTrackColor = AudibleColors.Orange,
                    inactiveTrackColor = AudibleColors.Divider,
                ),
            )
            Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {
                presets.forEach { p ->
                    val sel = speed == p
                    Box(
                        Modifier
                            .clip(RoundedCornerShape(8.dp))
                            .background(if (sel) AudibleColors.Orange else AudibleColors.Surface2)
                            .clickable { onSpeed(p) }
                            .padding(horizontal = 16.dp, vertical = 8.dp),
                    ) {
                        Text("${p}×", style = AudibleText.Speed, color = if (sel) AudibleColors.Canvas else Color.White)
                    }
                }
            }
            Spacer(Modifier.height(8.dp))
        }
    }
}
```

### Continue-Listening Row

```kotlin
@Composable
fun ContinueRow(title: String, author: String, remaining: String, artworkUrl: String, progress: Float) {
    Row(
        Modifier
            .clip(RoundedCornerShape(12.dp))
            .background(AudibleColors.Surface1)
            .padding(14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        CoverProgressRing(artworkUrl, progress, size = 72.dp, ring = 3.dp)
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(3.dp)) {
            Text(title, style = AudibleText.CardTitle, color = Color.White, maxLines = 1, overflow = TextOverflow.Ellipsis)
            Text(author, style = AudibleText.Author.copy(fontSize = 13.sp), color = AudibleColors.TextSecondary, maxLines = 1)
            Text(remaining, style = AudibleText.Meta.copy(fontSize = 12.sp), color = AudibleColors.TextSecondary)
        }
        Box(
            Modifier.size(56.dp).clip(CircleShape).background(AudibleColors.Orange),
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.PlayArrow, "Play", tint = AudibleColors.Canvas, modifier = Modifier.size(22.dp)) }
    }
}
```

### Chapter List Sheet

```kotlin
enum class ChapterState { Playing, Finished, Upcoming }

@Composable
fun ChapterRow(n: Int, title: String, dur: String, state: ChapterState) {
    Row(
        Modifier.fillMaxWidth().height(56.dp).padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.width(3.dp).height(28.dp).background(if (state == ChapterState.Playing) AudibleColors.Orange else Color.Transparent))
        Text("$n.", style = AudibleText.Chapter, color = AudibleColors.TextSecondary)
        Text(
            title,
            style = AudibleText.Chapter,
            color = if (state == ChapterState.Playing) AudibleColors.Orange else Color.White,
            modifier = Modifier.weight(1f),
        )
        if (state == ChapterState.Finished) {
            Icon(Icons.Filled.Check, "Finished", tint = AudibleColors.Orange, modifier = Modifier.size(14.dp))
        }
        Text(dur, style = AudibleText.Meta, color = AudibleColors.TextSecondary)
    }
}
```

## 4. Captions (Synced Text)

```kotlin
@Composable
fun Captions(line: String, activeWord: Int) {
    FlowRow(
        Modifier
            .clip(RoundedCornerShape(12.dp))
            .background(AudibleColors.Surface1)
            .padding(16.dp),
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        line.split(" ").forEachIndexed { i, word ->
            val c by animateColorAsState(
                if (i == activeWord) Color.White else AudibleColors.TextSecondary, tween(180), label = "w$i",
            )
            Text(word, style = AudibleText.Captions, color = c)
        }
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Audible's iOS tab bar is `.regularMaterial` blur over warm charcoal; Android has no first-class live blur, so use a 96%-opaque charcoal surface. **Active tint is Audible Orange.**

```kotlin
@Composable
fun AudibleBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = AudibleColors.Canvas.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Library"  to Icons.Filled.LibraryBooks,
            "Discover" to Icons.Filled.Search,
            "Profile"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = AudibleText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = AudibleColors.Orange,
                    selectedTextColor   = AudibleColors.Orange,
                    unselectedIconColor = AudibleColors.TextSecondary,
                    unselectedTextColor = AudibleColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // Audible has no Material pill
                ),
            )
        }
    }
}
```

The persistent **Now Playing mini-bar** sits directly above this bar: a 56.dp `Surface(color = AudibleColors.Surface1)` row with a 3.dp `AudibleColors.Orange` progress line pinned to its top edge, a Playfair-Display title + Inter author — render it in the `Scaffold` `bottomBar` slot stacked above `AudibleBottomBar`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Play tap | `animateFloatAsState` 1 → 0.93 `spring(dampingRatio = 0.72f)`, `HapticFeedbackType.LongPress` |
| 30s skip | `animateColorAsState` white → orange over `tween(180)`; `HapticFeedbackType.TextHandleMove`; ring jumps 30s |
| Speed dial | each .25 tick → `HapticFeedbackType.TextHandleMove`; big number `AnimatedContent` cross-fade |
| Cover progress ring | animate the arc sweep linearly from playback position (no easing) |
| Chapter change | animate the orange leading bar offset with `tween(220)` |
| Mini-bar → full player | `SharedTransitionLayout` + `Modifier.sharedElement()` on the cover (Compose 1.7+); fallback `AnimatedVisibility` + `slideInVertically` |
| Captions | per-word `animateColorAsState` over `tween(180)` |

```kotlin
// Live cover-ring sweep from the player position
val progress by produceState(0f) {
    player.positionFlow.collect { value = (it.position.toFloat() / it.duration).coerceIn(0f, 1f) }
}
```

Haptics: prefer `LocalHapticFeedback`. For speed-dial detents and skip, `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` (API 30+) approximates iOS selection feedback.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Audible's 30-second skip maps to `Icons.Filled.Replay30` / `Icons.Filled.Forward30` (they carry the literal "30").

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Skip back 30s | `gobackward.30` | `Icons.Filled.Replay30` |
| Skip forward 30s | `goforward.30` | `Icons.Filled.Forward30` |
| Sleep timer | `moon.zzz` | `Icons.Filled.Bedtime` |
| Bookmark | `bookmark` / `bookmark.fill` | `Icons.Outlined.BookmarkBorder` / `Icons.Filled.Bookmark` |
| Chapters | `list.bullet` | `Icons.Filled.FormatListBulleted` |
| Car mode | `car` | `Icons.Filled.DirectionsCar` |
| Captions | `captions.bubble` | `Icons.Filled.ClosedCaption` |
| Finished check | `checkmark` | `Icons.Filled.Check` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Library (tab) | `books.vertical.fill` | `Icons.Filled.LibraryBooks` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; the `Canvas` ring + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the warm charcoal wants light-content system bars. Apply `Scaffold` insets so the mini-bar clears gesture nav.
- **Font scaling**: `sp` honors the user's font scale — keep it on titles, authors, body, chapters, and **captions especially** (readability while listening). Pin the speed value and tab labels by deriving size from `dp`.
- **Serif discipline**: never apply `PlayfairDisplay` to body, metadata, or captions — its high stroke contrast thins at small sizes; keep it at 17sp+.
- **TalkBack**: label skip buttons "Skip back/forward 30 seconds"; expose speed via `Modifier.semantics { progressBarRangeInfo = ProgressBarRangeInfo(speed, 0.5f..3.5f) }` and a set action; mirror the ring with a `contentDescription` ("34 percent, 8 hours 14 minutes remaining").
- **Touch targets**: the 72.dp play button clears the 48.dp minimum; give the 30.dp skip glyphs a 48.dp hit area via the 44.dp box + padding.
- **Contrast**: `#B0B0B0` on `#1A1A1A` passes WCAG AA at 14sp+; validate the 12-13sp meta and lighten toward `#BDBDBD` if targeting strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Audible's brand requires the fixed warm charcoal canvas and single-orange accent regardless of wallpaper.
