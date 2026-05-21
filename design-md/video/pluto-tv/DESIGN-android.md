# Pluto TV (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Pluto TV's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the EPG channel-guide grid, the pinned mini-player, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Pluto's navy canvas, the EPG channel guide as the whole product, the yellow-left-border "On Now" cell, the pinned mini-player) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyColumn`/`LazyRow` for the grid, `Brush.linearGradient` for channel logos, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+`. Pluto TV is **dark-only** — no light scheme, no Material You dynamic color.

## 1. Color Tokens

```kotlin
// ui/theme/PlutoColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object PlutoColors {
    // Canvas & Surfaces (dark-only)
    val Canvas   = Color(0xFF0B0F1F)
    val Surface1 = Color(0xFF141A2E)
    val Surface2 = Color(0xFF1C2440)
    val Surface3 = Color(0xFF25304F)
    val Divider  = Color(0xFF2A3556)

    // Brand
    val Blue        = Color(0xFF0048FF)
    val BluePressed = Color(0xFF0039CC)
    val BlueBright  = Color(0xFF2C6BFF)
    val Yellow      = Color(0xFFFFE100)
    val Cyan        = Color(0xFF00C2FF)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF9DA8C7)
    val TextTertiary  = Color(0xFF6B7595)
    val OnYellow      = Color(0xFF0B0F1F)

    // Semantic
    val Live    = Color(0xFFFF3B5C)
    val Success = Color(0xFF21D07A)
    val Error   = Color(0xFFFF4A6E)

    // Example per-channel logo gradients (content, not system tokens)
    val ChActionBrush = Brush.linearGradient(listOf(Color(0xFF0048FF), Color(0xFF00C2FF)))
    val ChMoviesBrush = Brush.linearGradient(listOf(Color(0xFFFF6B2C), Color(0xFFFF2C6B)))
    val ChSportsBrush = Brush.linearGradient(listOf(Color(0xFF21D07A), Color(0xFF00A6A6)))
}
```

Wire it into a dark-only scheme. Pluto has no light mode and no brand accent that should harmonize with the wallpaper — never enable `dynamicColorScheme()`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme

private val PlutoDark = darkColorScheme(
    primary        = PlutoColors.Blue,
    onPrimary      = PlutoColors.TextPrimary,
    background     = PlutoColors.Canvas,
    onBackground   = PlutoColors.TextPrimary,
    surface        = PlutoColors.Surface1,
    onSurface      = PlutoColors.TextPrimary,
    surfaceVariant = PlutoColors.Surface2,
    outline        = PlutoColors.Divider,
    error          = PlutoColors.Error,
)

@Composable
fun PlutoTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = PlutoDark, typography = PlutoTypography, content = content)  // always dark
```

## 2. Typography

Pluto's brand sans is a semi-rounded humanist grotesque; **Manrope** is the closest free analog (SIL OFL). Drop the TTFs in `res/font/`. Channel numbers and times must use tabular figures.

```kotlin
// ui/theme/PlutoType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextGeometricTransform
import androidx.compose.ui.unit.sp

val Manrope = FontFamily(
    Font(R.font.manrope_regular,   FontWeight.Normal),
    Font(R.font.manrope_medium,    FontWeight.Medium),
    Font(R.font.manrope_semibold,  FontWeight.SemiBold),
    Font(R.font.manrope_bold,      FontWeight.Bold),
    Font(R.font.manrope_extrabold, FontWeight.ExtraBold),
)

// fontFeatureSettings = "tnum" gives tabular figures (grid alignment)
object PlutoText {
    val Display     = TextStyle(Manrope, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 36.sp, letterSpacing = (-0.5).sp)
    val ScreenTitle = TextStyle(Manrope, fontWeight = FontWeight.ExtraBold, fontSize = 24.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val NowPlaying  = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.2).sp)
    val Body        = TextStyle(Manrope, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 23.sp)
    val Channel     = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 16.sp)
    val Program     = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 16.sp)
    val Meta        = TextStyle(Manrope, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp)
    val ChNumber    = TextStyle(Manrope, fontWeight = FontWeight.ExtraBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp, fontFeatureSettings = "tnum")
    val TimeLabel   = TextStyle(Manrope, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 10.sp, fontFeatureSettings = "tnum")
    val Badge       = TextStyle(Manrope, fontWeight = FontWeight.ExtraBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.5.sp)
    val Tab         = TextStyle(Manrope, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

val PlutoTypography = Typography(
    headlineLarge  = PlutoText.Display,
    headlineMedium = PlutoText.ScreenTitle,
    titleMedium    = PlutoText.Section,
    bodyMedium     = PlutoText.Body,
    labelSmall     = PlutoText.Tab,
)
```

## 3. Signature Components

### EPG Grid (the centerpiece)

A frozen channel column + frozen time-bar with a horizontally-scrolling program timeline. Share one horizontal `ScrollState` across the time-bar and every row so they move in lockstep.

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.horizontalScroll
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.unit.dp

private val SlotWidth = 96.dp
private val ChannelColW = 86.dp
private val RowH = 64.dp

data class EpgProgram(val title: String, val timeLabel: String, val widthSlots: Float, val isOnNow: Boolean)
data class EpgChannel(val number: String, val abbrev: String, val logo: Brush, val programs: List<EpgProgram>)

@Composable
fun EpgGuide(timeLabels: List<String>, channels: List<EpgChannel>) {
    val sharedScroll = rememberScrollState()  // one offset for time-bar + all rows

    Column(Modifier.fillMaxSize().background(PlutoColors.Canvas)) {
        // Sticky time-bar
        Row(
            Modifier
                .fillMaxWidth()
                .background(PlutoColors.Canvas)
                .drawBottomDivider(),
        ) {
            Spacer(Modifier.width(ChannelColW))
            Row(Modifier.horizontalScroll(sharedScroll)) {
                timeLabels.forEach { t ->
                    Text(
                        t, style = PlutoText.TimeLabel, color = PlutoColors.TextTertiary,
                        modifier = Modifier.width(SlotWidth).padding(vertical = 6.dp, start = 8.dp),
                    )
                }
            }
        }

        LazyColumn {
            items(channels) { ch -> EpgRow(ch, sharedScroll) }
        }
    }
}

@Composable
private fun EpgRow(channel: EpgChannel, sharedScroll: androidx.compose.foundation.ScrollState) {
    Row(Modifier.height(RowH).fillMaxWidth().drawBottomDivider()) {
        // Frozen channel column
        Column(
            Modifier
                .width(ChannelColW)
                .fillMaxHeight()
                .background(PlutoColors.Surface1)
                .drawEndDivider(),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.Center,
        ) {
            Text(channel.number, style = PlutoText.ChNumber, color = PlutoColors.TextTertiary)
            Spacer(Modifier.height(3.dp))
            Box(
                Modifier.size(40.dp, 28.dp).clip(RoundedCornerShape(5.dp)).background(channel.logo),
                contentAlignment = Alignment.Center,
            ) { Text(channel.abbrev, style = PlutoText.Badge.copy(fontSize = 9.sp), color = Color.White) }
        }

        // Program timeline — shared horizontal scroll
        Row(Modifier.horizontalScroll(sharedScroll)) {
            channel.programs.forEach { p ->
                Box(
                    Modifier
                        .width(SlotWidth * p.widthSlots)
                        .fillMaxHeight()
                        .background(if (p.isOnNow) PlutoColors.Surface2 else PlutoColors.Surface1),
                ) {
                    if (p.isOnNow) {
                        Box(Modifier.fillMaxHeight().width(3.dp).background(PlutoColors.Yellow))
                    }
                    Column(
                        Modifier.fillMaxSize().padding(horizontal = 10.dp, vertical = 8.dp),
                        verticalArrangement = Arrangement.Center,
                    ) {
                        Text(
                            p.title, style = PlutoText.Program, maxLines = 1,
                            color = if (p.isOnNow) PlutoColors.TextPrimary else Color.White.copy(alpha = 0.85f),
                        )
                        Text(p.timeLabel, style = PlutoText.Meta, color = PlutoColors.TextTertiary)
                    }
                    Box(Modifier.align(Alignment.CenterEnd).fillMaxHeight().width(1.dp).background(PlutoColors.Canvas))
                }
            }
        }
    }
}

// Hairline grid lines
fun Modifier.drawBottomDivider() = this.then(Modifier.drawWithContent {
    drawContent(); drawRect(PlutoColors.Divider, topLeft = androidx.compose.ui.geometry.Offset(0f, size.height - 0.5.dp.toPx()),
        size = androidx.compose.ui.geometry.Size(size.width, 0.5.dp.toPx()))
})
fun Modifier.drawEndDivider() = this.then(Modifier.drawWithContent {
    drawContent(); drawRect(PlutoColors.Divider, topLeft = androidx.compose.ui.geometry.Offset(size.width - 0.5.dp.toPx(), 0f),
        size = androidx.compose.ui.geometry.Size(0.5.dp.toPx(), size.height))
})
```

### Mini-Player (pinned top)

```kotlin
@Composable
fun MiniPlayer(
    videoLayer: @Composable () -> Unit,
    channelLine: String,
    programTitle: String,
    elapsed: Float,   // 0f..1f
) {
    Box(Modifier.fillMaxWidth().height(150.dp)) {
        videoLayer()
        Box(
            Modifier
                .fillMaxSize()
                .background(
                    Brush.verticalGradient(0.4f to Color.Transparent, 1f to Color.Black.copy(alpha = 0.55f))
                )
                .padding(14.dp),
        ) {
            // LIVE chip
            Row(
                Modifier
                    .align(Alignment.TopStart)
                    .clip(RoundedCornerShape(5.dp))
                    .background(Color.Black.copy(alpha = 0.4f))
                    .padding(horizontal = 9.dp, vertical = 4.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(6.dp),
            ) {
                Box(Modifier.size(7.dp).clip(RoundedCornerShape(50)).background(PlutoColors.Live))
                Text("LIVE", style = PlutoText.Badge, color = Color.White)
            }

            Column(Modifier.align(Alignment.BottomStart)) {
                Text(channelLine, style = PlutoText.Channel.copy(fontWeight = FontWeight.Bold), color = PlutoColors.Cyan)
                Text(programTitle, style = PlutoText.NowPlaying.copy(fontSize = 18.sp, fontWeight = FontWeight.ExtraBold), color = Color.White)
                Box(
                    Modifier
                        .padding(top = 8.dp)
                        .fillMaxWidth()
                        .height(3.dp)
                        .clip(RoundedCornerShape(50))
                        .background(Color.White.copy(alpha = 0.2f)),
                ) {
                    Box(Modifier.fillMaxWidth(elapsed).fillMaxHeight().clip(RoundedCornerShape(50)).background(PlutoColors.Yellow))
                }
            }
        }
    }
}
```

### Category Pill Strip

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.items
import androidx.compose.foundation.clickable

@Composable
fun CategoryPills(categories: List<String>, selected: String, onSelect: (String) -> Unit) {
    LazyRow(
        contentPadding = PaddingValues(horizontal = 16.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        items(categories) { c ->
            val active = c == selected
            Box(
                Modifier
                    .clip(RoundedCornerShape(50))
                    .background(if (active) PlutoColors.Blue else PlutoColors.Surface2)
                    .clickable { onSelect(c) }
                    .padding(horizontal = 12.dp, vertical = 6.dp),
            ) {
                Text(c, style = PlutoText.Channel.copy(fontSize = 11.sp),
                    color = if (active) Color.White else PlutoColors.TextSecondary)
            }
        }
    }
}
```

## 4. Navigation

Pluto has a 5-tab bottom strip; active is **yellow** with no Material tint pill.

```kotlin
@Composable
fun PlutoBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = PlutoColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Live TV"   to Icons.Filled.Tv,
            "On Demand" to Icons.Filled.PlayCircle,
            "Search"    to Icons.Filled.Search,
            "My Pluto"  to Icons.Filled.Bookmark,
            "Account"   to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = PlutoText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = PlutoColors.Yellow,
                    selectedTextColor   = PlutoColors.Yellow,
                    unselectedIconColor = PlutoColors.TextTertiary,
                    unselectedTextColor = PlutoColors.TextTertiary,
                    indicatorColor      = Color.Transparent,  // no Material pill
                ),
            )
        }
    }
}
```

## 5. Motion

Pluto motion is fast and quiet — channel surfing should feel instant; 200–300ms ranges. The grid itself barely animates (it's a broadcast surface).

| Moment | Compose recipe |
|--------|----------------|
| Channel surf (tune-in) | `Crossfade(currentChannelId, animationSpec = tween(300))` on the mini-player video |
| Time-bar / timeline sync | one shared `ScrollState` via `Modifier.horizontalScroll(sharedScroll)` on the time-bar + every row |
| Mini-player elapsed bar | `animateFloatAsState(elapsed, tween(300))` driving `fillMaxWidth(fraction)` |
| Category filter swap | `AnimatedContent` rows `fadeIn/out tween(200)`; pill fill `animateColorAsState tween(150)` |
| On Now border pulse (optional) | `rememberInfiniteTransition` alpha 0.85↔1.0 `tween(2000)` reverse — keep subtle |
| Set Reminder confirm | icon `Crossfade` Bell→Check + spring scale 1→1.2→1; soft haptic |
| Sheet present | `ModalBottomSheet` slide-up; scrim `Color(0xFF04060F).copy(alpha = 0.7f)` |
| Tab switch | instant content swap; icon color `animateColorAsState` to Yellow `tween(200)` |

```kotlin
// Mini-player elapsed bar
val w by animateFloatAsState(elapsed, tween(300), label = "elapsed")
Box(Modifier.fillMaxWidth(w).height(3.dp).background(PlutoColors.Yellow))
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on tune-in and reminder-set; a lighter `HapticFeedbackConstants.CLOCK_TICK` for channel-cell focus while surfing with a remote.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Live TV (tab) | `tv` | `Icons.Filled.Tv` |
| On Demand (tab) | `play.rectangle` | `Icons.Filled.PlayCircle` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| My Pluto (tab) | `bookmark.fill` | `Icons.Filled.Bookmark` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Play (CTA) | `play.fill` | `Icons.Filled.PlayArrow` |
| Live dot | `circle.fill` | `Icons.Filled.Circle` (tint `#FF3B5C`) |
| Add to watchlist | `plus` → `checkmark` | `Icons.Filled.Add` → `Icons.Filled.Check` |
| Set reminder | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Fullscreen | `arrow.up.left.and.arrow.down.right` | `Icons.Filled.Fullscreen` |
| Cast | `airplayvideo` | `Icons.Filled.Cast` |
| Full schedule | `calendar` | `Icons.Filled.CalendarMonth` |
| Mute / volume | `speaker.slash.fill` / `speaker.wave.2.fill` | `Icons.Filled.VolumeOff` / `Icons.Filled.VolumeUp` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24**, `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; force light-content system bars (always dark app). The mini-player video draws under the status bar; the sticky time-bar sits below it; the bottom bar respects the navigation-bar inset.
- **Dark-only**: never call `dynamicColorScheme()` and never build a light scheme — Pluto's navy-blue-yellow broadcast identity is fixed regardless of wallpaper.
- **Tabular figures are mandatory**: `fontFeatureSettings = "tnum"` on channel numbers and EPG times — otherwise the frozen channel column and time-bar drift out of alignment with the rows as the grid scrolls.
- **EPG scroll sync**: share one `ScrollState` across the time-bar and every row (`Modifier.horizontalScroll(sharedScroll)`); virtualize the vertical channel list with `LazyColumn` for hundreds of channels.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, body, synopsis, now-playing title. Pin layout-locked text (EPG cells, channel numbers, 10sp time-bar labels, badges, tab labels) via `dp`-derived sizes or a fixed-`fontScale` `LocalDensity`, or the grid columns break.
- **Touch targets**: Material guidance is 48.dp. EPG program cells are ≥64.dp tall (fine); give small controls 48.dp hit areas; category pills ≥32.dp; primary buttons ≥48.dp.
- **TalkBack**: the On Now cell's `contentDescription` must include "on now, live" — the yellow left border is meaningful state, never `clearAndSetSemantics {}`-away — and pair it with the LIVE text so the cue is not color-only; mini-player announces "Live: {program} on {channel}".
- **Contrast**: white on `#0B0F1F`/`#141A2E`/`#1C2440` passes WCAG AA; `#9DA8C7` on the canvas passes AA at 12sp+; `#0B0F1F` on `#FFE100` is high-contrast for the yellow CTA.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the optional On Now border pulse and jump the elapsed bar to its value; category swap becomes an instant cut.
- **Focus (TV/remote on Android TV-style builds)**: the EPG must be DPAD-navigable cell-by-cell; the focused cell gets a `#2C6BFF` border in addition to color so focus is perceivable without color vision.
