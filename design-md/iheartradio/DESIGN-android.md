# iHeartRadio (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports iHeartRadio's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the heart-logomark station tile, the pulsing LIVE badge, the scanning bar (no scrubber), the circular red play/stop button, the live station row, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (iHeartRadio's warm maroon-black canvas, the scarce red→magenta system, the pulsing LIVE badge, the no-scrubber scanning bar) while making everything idiomatic Android — a `NavigationBar` instead of a UITabBar, `Brush.linearGradient` instead of SwiftUI `LinearGradient`, `dp`/`sp` instead of `pt`, and `LocalHapticFeedback` instead of `sensoryFeedback`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for any remote podcast art. No Material You dynamic color — iHeartRadio's red identity must hold regardless of wallpaper. The player is dark-native; the dark scheme is primary.

## 1. Color Tokens

```kotlin
// ui/theme/IHRColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object IHRColors {
    // Canvas & Surfaces (Dark — the only real theme for the player)
    val Canvas    = Color(0xFF120A0E) // warm maroon-black — NOT pure black
    val Surface1  = Color(0xFF1E1216)
    val Surface2  = Color(0xFF2A171D)
    val Divider   = Color(0xFF341C24)
    val Scrim     = Color(0xD9120A0E)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFB8A0A8)
    val TextTertiary  = Color(0xFF7C6168)

    // Brand
    val Red       = Color(0xFFC6002B)
    val Coral     = Color(0xFFF23A2F) // LIVE signal only
    val Magenta   = Color(0xFFE40A5D)
    val RedPress  = Color(0xFF9E0022)
    val CoralPress = Color(0xFFCC2D24)

    // Semantic
    val Success = Color(0xFF1ED760)
    val Error   = Color(0xFFFF4D5E)
    val Warning = Color(0xFFFFB02E)

    // iHeart system gradient — station logos, brand chips, hero cards
    val SystemBrush = Brush.linearGradient(listOf(Red, Magenta))
    val TileBrush = Brush.linearGradient(
        0f to Surface2, 0.6f to Surface1, 1f to Canvas,
    )
    val ScanBrush = Brush.horizontalGradient(listOf(Color.Transparent, Coral, Color.Transparent))
}
```

Wire it into both schemes. iHeartRadio's player is dark-first; the dark scheme uses the signature `#120A0E` maroon near-black, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.runtime.Composable

private val IHRDark = darkColorScheme(
    primary        = IHRColors.Red,
    onPrimary      = IHRColors.TextPrimary,
    background     = IHRColors.Canvas,
    onBackground   = IHRColors.TextPrimary,
    surface        = IHRColors.Surface1,
    onSurface      = IHRColors.TextPrimary,
    surfaceVariant = IHRColors.Surface2,
    outline        = IHRColors.Divider,
    error          = IHRColors.Error,
)

// Fallback only — the player ships no real light theme.
private val IHRLightFallback = lightColorScheme(
    primary    = IHRColors.Red,
    background = Color(0xFFFFFFFF),
    surface    = Color(0xFFFBF2F4),
    onBackground = Color(0xFF1A0E12),
)

@Composable
fun IHRTheme(content: @Composable () -> Unit) = MaterialTheme(
    colorScheme = IHRDark,              // pin dark — iHeartRadio's player identity
    typography  = IHRTypography,
    content     = content,
)
```

## 2. Typography

iHeartRadio ships **iHeart Sans**; use **Inter** as the faithful fallback (SIL OFL — drop the TTFs in `res/font/`). Body/metadata uses Inter at 400/500; titles use the brand face at 700–800; LIVE tags at 900. Bold by default — the warm-dark canvas needs heavy anchors.

```kotlin
// ui/theme/IHRType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

// Swap iheart_sans_* → inter_* if the brand face isn't licensed.
val IHeartSans = FontFamily(
    Font(R.font.iheart_sans_semibold,  FontWeight.SemiBold),
    Font(R.font.iheart_sans_bold,      FontWeight.Bold),
    Font(R.font.iheart_sans_extrabold, FontWeight.ExtraBold),
    Font(R.font.iheart_sans_black,     FontWeight.Black),
)
val Inter = FontFamily(
    Font(R.font.inter_regular, FontWeight.Normal),
    Font(R.font.inter_medium,  FontWeight.Medium),
)

object IHRText {
    val ScreenTitle = TextStyle(IHeartSans, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val StationName = TextStyle(IHeartSans, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section     = TextStyle(IHeartSans, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Subhead     = TextStyle(IHeartSans, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Inter,      fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val RowTitle    = TextStyle(IHeartSans, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val NowAiring   = TextStyle(Inter,      fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(Inter,      fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val LiveTag     = TextStyle(IHeartSans, fontWeight = FontWeight.Black,     fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.8.sp)
    val Freq        = TextStyle(IHeartSans, fontWeight = FontWeight.ExtraBold, fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.3.sp)
    val TabLabel    = TextStyle(IHeartSans, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(IHeartSans, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Chip        = TextStyle(IHeartSans, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
}

val IHRTypography = Typography(
    headlineLarge  = IHRText.ScreenTitle,
    headlineMedium = IHRText.StationName,
    titleLarge     = IHRText.Section,
    bodyLarge      = IHRText.Body,
    labelSmall     = IHRText.TabLabel,
)
```

## 3. Signature Components

### Heart-Logomark Station Tile (with pulsing LIVE badge + frequency chip)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

@Composable
fun StationTile(frequency: String, isLive: Boolean = true, modifier: Modifier = Modifier) {
    Box(
        modifier
            .fillMaxWidth()
            .aspectRatio(1f)
            .shadow(24.dp, RoundedCornerShape(14.dp), spotColor = IHRColors.Red, ambientColor = IHRColors.Red)
            .clip(RoundedCornerShape(14.dp))
            .background(IHRColors.TileBrush),
        contentAlignment = Alignment.Center,
    ) {
        // warm red radial bloom
        Box(
            Modifier.matchParentSize().background(
                Brush.radialGradient(
                    colors = listOf(IHRColors.Red.copy(alpha = 0.35f), Color.Transparent),
                    center = Offset(0.5f, 0.42f), radius = 620f,
                )
            )
        )

        // iHeart heart-check logomark as artwork
        Box(contentAlignment = Alignment.Center) {
            Icon(Icons.Filled.Favorite, null, tint = IHRColors.Red, modifier = Modifier.size(96.dp))
            Icon(Icons.Filled.Check, null, tint = Color.White, modifier = Modifier.size(40.dp))
        }

        if (isLive) {
            Box(Modifier.align(Alignment.TopStart).padding(16.dp)) { LiveBadge() }
        }

        Box(
            Modifier
                .align(Alignment.BottomEnd)
                .padding(16.dp)
                .clip(RoundedCornerShape(8.dp))
                .background(IHRColors.Canvas.copy(alpha = 0.7f))
                .padding(vertical = 6.dp, horizontal = 12.dp),
        ) {
            Text(frequency, style = IHRText.Freq, color = Color.White)
        }
    }
}
```

### Pulsing LIVE Badge

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.draw.scale

@Composable
fun LiveBadge() {
    val transition = rememberInfiniteTransition(label = "livePulse")
    val p by transition.animateFloat(
        initialValue = 0f, targetValue = 1f,
        animationSpec = infiniteRepeatable(tween(1600, easing = LinearOutSlowInEasing), RepeatMode.Restart),
        label = "pulse",
    )
    Row(
        Modifier
            .clip(CircleShape)
            .background(IHRColors.Coral)
            .padding(vertical = 6.dp).padding(start = 10.dp, end = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(7.dp),
    ) {
        Box(Modifier.size(7.dp), contentAlignment = Alignment.Center) {
            Box(
                Modifier
                    .size(7.dp)
                    .scale(1f + p * 2f)
                    .border(1.5.dp, Color.White.copy(alpha = 0.7f * (1f - p)), CircleShape)
            )
            Box(Modifier.size(7.dp).clip(CircleShape).background(Color.White))
        }
        Text("LIVE", style = IHRText.LiveTag, color = Color.White)
    }
}
```

### Scanning Bar (replaces the scrubber — live radio can't seek)

```kotlin
import androidx.compose.ui.layout.onSizeChanged
import androidx.compose.ui.unit.IntOffset
import androidx.compose.material.icons.filled.Circle

@Composable
fun ScanningBar() {
    var widthPx by remember { mutableStateOf(0) }
    val transition = rememberInfiniteTransition(label = "scan")
    val t by transition.animateFloat(
        initialValue = 0f, targetValue = 1f,
        animationSpec = infiniteRepeatable(tween(2200, easing = FastOutSlowInEasing), RepeatMode.Restart),
        label = "sweep",
    )
    Column {
        Box(
            Modifier
                .fillMaxWidth()
                .height(4.dp)
                .clip(RoundedCornerShape(2.dp))
                .background(IHRColors.Surface2)
                .onSizeChanged { widthPx = it.width },
        ) {
            Box(
                Modifier
                    .fillMaxWidth(0.38f)
                    .fillMaxHeight()
                    .offset { IntOffset(((t * (widthPx * 1.38f)) - widthPx * 0.38f).toInt(), 0) }
                    .background(IHRColors.ScanBrush)
            )
        }
        Row(Modifier.padding(top = 10.dp), verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Box(Modifier.size(6.dp).clip(CircleShape).background(IHRColors.Coral))
            Text("STREAMING LIVE", style = IHRText.LiveTag.copy(fontFamily = Inter, fontWeight = FontWeight.Bold, fontSize = 11.sp, letterSpacing = 0.4.sp), color = IHRColors.Coral)
        }
    }
}
```

### Circular Red Play/Stop Button (no pause — live)

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material.icons.filled.Stop
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun PlayStopButton(playing: Boolean, onToggle: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.94f else 1f, tween(120), label = "playScale")
    val haptics = LocalHapticFeedback.current

    Box(
        Modifier
            .size(68.dp)
            .scale(scale)
            .shadow(14.dp, CircleShape, spotColor = IHRColors.Red, ambientColor = IHRColors.Red)
            .clip(CircleShape)
            .background(IHRColors.Red)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onToggle()
            },
        contentAlignment = Alignment.Center,
    ) {
        // live = STOP, not pause; stopped = play
        Icon(
            if (playing) Icons.Filled.Stop else Icons.Filled.PlayArrow,
            contentDescription = if (playing) "Stop" else "Play",
            tint = Color.White,
            modifier = Modifier.size(26.dp).offset(x = if (playing) 0.dp else 2.dp),
        )
    }
}
```

### Live Station Row

```kotlin
@Composable
fun LiveStationRow(callSign: String, name: String, genre: String) {
    Row(
        Modifier.fillMaxWidth().height(68.dp).padding(horizontal = 24.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(
            Modifier.size(48.dp).clip(RoundedCornerShape(8.dp)).background(IHRColors.SystemBrush),
            contentAlignment = Alignment.Center,
        ) { Text(callSign, style = IHRText.Chip.copy(fontWeight = FontWeight.Black, fontSize = 12.sp), color = Color.White) }

        Column(Modifier.weight(1f)) {
            Text(name, style = IHRText.RowTitle, color = IHRColors.TextPrimary)
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    Box(Modifier.size(5.dp).clip(CircleShape).background(IHRColors.Coral))
                    Text("LIVE", style = IHRText.LiveTag.copy(fontSize = 10.sp, letterSpacing = 0.5.sp), color = IHRColors.Coral)
                }
                Text(genre, style = IHRText.Meta, color = IHRColors.TextSecondary)
            }
        }
        Icon(Icons.Outlined.PlayCircle, contentDescription = "Play", tint = IHRColors.Coral, modifier = Modifier.size(28.dp))
    }
}
```

## 4. Navigation

iHeartRadio has a 5-tab bottom strip with a coral active tint — no Material pill indicator. Model the player top bar as a custom row (collapse chevron, two-line context, overflow).

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*
import androidx.compose.material.icons.outlined.PlayCircle

@Composable
fun IHRBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = IHRColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "For You"  to Icons.Filled.Home,
            "Radio"    to Icons.Filled.Radio,
            "Podcasts" to Icons.Filled.Mic,
            "Search"   to Icons.Filled.Search,
            "Library"  to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = IHRText.TabLabel) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = IHRColors.Coral,    // coral = the LIVE/active color
                    selectedTextColor = IHRColors.Coral,
                    unselectedIconColor = IHRColors.TextTertiary,
                    unselectedTextColor = IHRColors.TextTertiary,
                    indicatorColor = Color.Transparent,     // no Material pill — iHeartRadio has none
                ),
            )
        }
    }
}

@Composable
fun PlayerTopBar(onCollapse: () -> Unit, onMore: () -> Unit) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 24.dp, vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(Icons.Filled.KeyboardArrowDown, "Collapse", tint = IHRColors.TextPrimary,
            modifier = Modifier.size(22.dp).clickable(onClick = onCollapse))
        Spacer(Modifier.weight(1f))
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Text("LIVE STATION", style = IHRText.LiveTag, color = IHRColors.TextSecondary)
            Text("For You", style = IHRText.Body.copy(fontSize = 13.sp, fontWeight = FontWeight.Bold), color = IHRColors.TextPrimary)
        }
        Spacer(Modifier.weight(1f))
        Icon(Icons.Filled.MoreHoriz, "More", tint = IHRColors.TextPrimary,
            modifier = Modifier.size(22.dp).clickable(onClick = onMore))
    }
}
```

The mini player (`Surface1`, 56.dp) sits above the `NavigationBar` with a thin coral scanning sweep at its top edge (no progress fill — it's live). The slide-in left drawer is a Material 3 `ModalNavigationDrawer` for account/settings.

## 5. Motion

iHeartRadio motion centers on two continuous "this is live" loops — the LIVE pulse and the scanning sweep — both of which must stop the instant the stream stops.

| Moment | Compose recipe |
|--------|----------------|
| LIVE badge pulse | `rememberInfiniteTransition` ring `scale 1 → 3` + alpha `0.7 → 0` over `tween(1600, LinearOutSlowInEasing)`, `RepeatMode.Restart` |
| Scanning bar sweep | `rememberInfiniteTransition` band `offset -38% → 138%` over `tween(2200, FastOutSlowInEasing)`, `RepeatMode.Restart` |
| Play/stop | `animateFloatAsState` scale 1 → 0.94 `tween(120)` + `HapticFeedbackType.LongPress`; ▶/■ icon swap (no pause) |
| Player expand/collapse | bottom-sheet / shared-element `spring(dampingRatio = 0.82f, stiffness = 380f)` (~320ms) |
| Station skip | tile `AnimatedContent` slide horizontally `tween(220)` + metadata crossfade |
| Live-row tag pulse | small coral dot subtle scale pulse in sync (~1.6s) |
| Tab switch | instant content swap; active icon tints `Coral` over `tween(120)` |

```kotlin
// The canonical iHeartRadio motion: loops that MUST stop when the stream stops
val livePulse = rememberInfiniteTransition(label = "live")
val ring by livePulse.animateFloat(
    initialValue = 0f, targetValue = 1f,
    animationSpec = infiniteRepeatable(tween(1600, easing = LinearOutSlowInEasing), RepeatMode.Restart),
    label = "ring",
)
// When isStreaming == false: do NOT compose these infinite transitions —
// render a static LIVE badge and a static coral fill at the track's left edge.
```

Haptics: use `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on play/stop, favorite toggle, and station skip. Only show a snackbar on errors (stream failed / offline).

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The heart logomark is `Favorite` + a `Check` overlay; the scanning bar is a drawn gradient band.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Collapse player | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Overflow menu | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Stop (live — no pause) | `stop.fill` | `Icons.Filled.Stop` |
| Station skip back | `backward.end.fill` | `Icons.Filled.SkipPrevious` |
| Station skip fwd | `forward.end.fill` | `Icons.Filled.SkipNext` |
| Player settings | `gearshape.fill` | `Icons.Filled.Settings` |
| Favorite (filled) | `heart.fill` | `Icons.Filled.Favorite` |
| Heart logomark | `heart.fill`+`checkmark` | `Icons.Filled.Favorite` + `Icons.Filled.Check` |
| Row play | `play.circle` | `Icons.Outlined.PlayCircle` |
| For You (tab) | `house.fill` | `Icons.Filled.Home` |
| Radio (tab) | `dot.radiowaves.left.and.right` | `Icons.Filled.Radio` |
| Podcasts (tab) | `list.bullet.below.rectangle` | `Icons.Filled.Mic` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Library (tab) | `person.fill` | `Icons.Filled.Person` |
| Add to library | `plus` | `Icons.Filled.Add` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Sleep timer | `alarm` | `Icons.Filled.Alarm` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; gradients + infinite transitions comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the warm-dark canvas wants light-content system bars always (the player is dark-native). The player top bar respects the camera cutout; the mini player sits above the `NavigationBar` with the coral scanning sweep at its top edge.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen/station titles, section headers, body, row text. Pin layout-sensitive text (tab labels, LIVE tags, frequency chip, overline, chip text) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Font choice**: bundle **iHeart Sans** if licensed; otherwise ship **Inter** (SIL OFL) — never leave brand titles to the system default.
- **TalkBack**: label the play button "Play"/"Stop" (never "Pause" — it's a live stream); announce the station tile as "{station}, live, now airing {track}". The scanning bar is decorative — `Modifier.clearAndSetSemantics {}`; do NOT expose it as a progress bar (a live stream has no position). Convey "live" via `stateDescription = "Live"` on the station/rows, not coral color or the pulse alone.
- **Touch targets**: Material guidance is 48.dp. The 68.dp play button is fine; give the 22.dp tab icons and 28.dp row play a 48.dp hit area; rows are 68.dp (full-row tappable); the favorite heart gets a 48.dp hit area.
- **Contrast**: `#FFFFFF` on `#120A0E` and white on `#C6002B`/`#F23A2F` pass WCAG AA; `#B8A0A8` on `#120A0E` passes AA for ≥14sp. Coral on canvas for the LIVE tag passes AA at 12sp black weight.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, do not compose the LIVE pulse or scanning sweep — render a static LIVE badge and a static coral fill at the track's left edge; keep the play/stop feedback (state-critical).
- **Dark mode**: pin the `Dark*` palette — `#120A0E`, NOT true black. Do **not** enable Material You `dynamicColorScheme()` — iHeartRadio's scarce red→magenta identity (and the unambiguous coral "LIVE" signal) must hold regardless of wallpaper. Never render a seekable scrubber or timestamps for a live stream, and never show a pause control — it is play/STOP only (a correctness and accessibility requirement).
