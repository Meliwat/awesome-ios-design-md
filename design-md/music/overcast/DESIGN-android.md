# Overcast (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Overcast's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (the orange-ring play button, Now Playing player, Smart Speed / Voice Boost rows, playlist rows), core navigation, motion, and haptics.

> Why a Compose guide for an iOS-only app? Overcast is iOS-exclusive, but the DESIGN.md tokens are platform-neutral and its identity ports cleanly: the warm paper-cream canvas, the single Overcast Orange accent, the outlined orange-ring play button, and Smart Speed / Voice Boost as first-class toggles. This file keeps that identity while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Slider` for the scrubber, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for cover images. Overcast is light-first (paper-cream); a full dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/OCColors.kt
import androidx.compose.ui.graphics.Color

object OCColors {
    // Brand (single accent)
    val Orange        = Color(0xFFFC7E0F)
    val OrangePressed = Color(0xFFE06A00)
    val LinkBlue      = Color(0xFF2D7FF9)

    // Canvas & Surfaces (Light — paper)
    val Paper        = Color(0xFFFBFAF6) // warm off-white — NOT pure white
    val Cream        = Color(0xFFF7F5EF)
    val PressedLight = Color(0xFFEDEBE3)
    val DividerLight = Color(0xFFE4E1D8)

    // Canvas & Surfaces (Dark)
    val Canvas    = Color(0xFF121212)
    val Surface1  = Color(0xFF1C1C1E)
    val Surface2  = Color(0xFF2A2A2C)
    val DividerDk = Color(0xFF303032)

    // Text
    val TextPrimaryLt   = Color(0xFF1A1A1A)
    val TextSecondaryLt = Color(0xFF6E6E6E)
    val TextPrimaryDk   = Color(0xFFF2F2F2)
    val TextSecondaryDk = Color(0xFF9A9A9A)
    val TextTertiary    = Color(0xFF9A9A9A)

    // Semantic
    val Success = Color(0xFF34C759)
    val Error   = Color(0xFFFF3B30)
    val Warn    = Color(0xFFFF9F0A)

    // Playlist icon tiles
    val PlAll      = Color(0xFFFC7E0F)
    val PlProgress = Color(0xFF2D7FF9)
    val PlStarred  = Color(0xFF34C759)
    val PlDownload = Color(0xFF5856D6)
}
```

Wire it into both schemes. Overcast is light-first (paper-cream); the dark scheme uses a true `#121212`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val OCLight = lightColorScheme(
    primary        = OCColors.Orange,
    onPrimary      = Color(0xFFFFFFFF),
    background      = OCColors.Paper,
    onBackground    = OCColors.TextPrimaryLt,
    surface         = OCColors.Cream,
    onSurface       = OCColors.TextPrimaryLt,
    surfaceVariant  = OCColors.PressedLight,
    outline         = OCColors.DividerLight,
    error           = OCColors.Error,
)

private val OCDark = darkColorScheme(
    primary        = OCColors.Orange,
    onPrimary      = Color(0xFFFFFFFF),
    background      = OCColors.Canvas,
    onBackground    = OCColors.TextPrimaryDk,
    surface         = OCColors.Surface1,
    onSurface       = OCColors.TextPrimaryDk,
    surfaceVariant  = OCColors.Surface2,
    outline         = OCColors.DividerDk,
    error           = OCColors.Error,
)

@Composable
fun OCTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) OCDark else OCLight,
    typography  = OCTypography,
    content     = content,
)
```

## 2. Typography (M3)

Overcast uses the platform system font (Roboto on Android stands in for SF Pro). Body 400, titles 700/800; timecodes render in a fixed-width box for tabular stability.

```kotlin
// ui/theme/OCType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

object OCText {
    val ScreenTitle = TextStyle(fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.4).sp)
    val NowPlaying  = TextStyle(fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Episode     = TextStyle(fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val RowTitle    = TextStyle(fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Body        = TextStyle(fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val ShowName    = TextStyle(fontWeight = FontWeight.Medium,    fontSize = 14.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val PlDesc      = TextStyle(fontWeight = FontWeight.Normal,    fontSize = 12.sp, lineHeight = 16.sp)
    val Button      = TextStyle(fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 16.sp)
    val Toggle      = TextStyle(fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Chip        = TextStyle(fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Tab         = TextStyle(fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Count       = TextStyle(fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 16.sp)
    val SkipLabel   = TextStyle(fontWeight = FontWeight.Bold,      fontSize = 9.sp,  lineHeight = 10.sp)
    val StatCaption = TextStyle(fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.4.sp)
    val Timecode    = TextStyle(fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 14.sp)
}

val OCTypography = Typography(
    headlineLarge  = OCText.ScreenTitle,
    headlineMedium = OCText.NowPlaying,
    titleMedium    = OCText.Episode,
    bodyMedium     = OCText.Body,
    labelSmall     = OCText.Tab,
)
```

> For tabular timecodes, render durations/timecodes inside a fixed-width `Box` (or use the `tnum` font feature on API 26+) so digits never shift while scrubbing.

## 3. Signature Components

### Orange-Ring Play Button (the signature control)

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Pause
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

@Composable
fun OCPlayRing(playing: Boolean, onToggle: () -> Unit, diameter: androidx.compose.ui.unit.Dp = 66.dp) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val ring = if (pressed) OCColors.OrangePressed else OCColors.Orange

    Box(
        Modifier
            .size(diameter)
            .border(2.5.dp, ring, CircleShape) // outlined ring — never a filled disc
            .clickable(interaction, indication = null) { onToggle() },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            if (playing) Icons.Filled.Pause else Icons.Filled.PlayArrow,
            contentDescription = if (playing) "Pause" else "Play",
            tint = OCColors.Orange,
            modifier = Modifier.size(diameter * 0.36f),
        )
    }
}
```

### Skip Button (chevron + interval label)

```kotlin
import androidx.compose.material.icons.filled.FastRewind
import androidx.compose.material.icons.filled.FastForward
import androidx.compose.material3.Text

@Composable
fun OCSkipButton(forward: Boolean, seconds: Int, primary: Color, secondary: Color, onClick: () -> Unit) {
    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(2.dp),
        modifier = Modifier.clickable { onClick() },
    ) {
        Icon(
            if (forward) Icons.Filled.FastForward else Icons.Filled.FastRewind,
            contentDescription = if (forward) "Skip forward $seconds seconds" else "Skip back $seconds seconds",
            tint = primary, modifier = Modifier.size(27.dp),
        )
        Text("$seconds", style = OCText.SkipLabel, color = secondary)
    }
}
```

### Now Playing Player

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.automirrored.filled.KeyboardArrowLeft
import androidx.compose.material.icons.automirrored.filled.List
import androidx.compose.material3.Slider
import androidx.compose.material3.SliderDefaults
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush

@Composable
fun OCNowPlaying(episodeTitle: String, showName: String, dark: Boolean) {
    var progress by remember { mutableFloatStateOf(0.38f) }
    var playing by remember { mutableStateOf(true) }
    val canvas = if (dark) OCColors.Canvas else OCColors.Paper
    val primary = if (dark) OCColors.TextPrimaryDk else OCColors.TextPrimaryLt
    val secondary = if (dark) OCColors.TextSecondaryDk else OCColors.TextSecondaryLt
    val track = if (dark) OCColors.Surface2 else OCColors.DividerLight

    Column(Modifier.fillMaxSize().background(canvas)) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 18.dp, vertical = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            Icon(Icons.AutoMirrored.Filled.KeyboardArrowLeft, "Collapse", tint = OCColors.Orange)
            Text("Now Playing", style = OCText.RowTitle.copy(fontSize = 13.sp), color = primary)
            Icon(Icons.AutoMirrored.Filled.List, "Queue", tint = primary)
        }

        Column(
            Modifier.padding(horizontal = 34.dp, vertical = 6.dp)
                .clip(RoundedCornerShape(10.dp)),
        ) {
            Box(
                Modifier.fillMaxWidth().aspectRatio(1f)
                    .background(Brush.linearGradient(listOf(Color(0xFF2B3A55), Color(0xFF121A2E))))
            )
            Box(Modifier.fillMaxWidth().height(5.dp).background(OCColors.Orange))
        }

        Column(Modifier.padding(horizontal = 34.dp)) {
            Text(episodeTitle, style = OCText.NowPlaying, color = primary)
            Text(showName, style = OCText.ShowName, color = secondary, modifier = Modifier.padding(top = 4.dp))
        }

        Column(Modifier.padding(horizontal = 30.dp, vertical = 4.dp)) {
            Slider(
                value = progress, onValueChange = { progress = it },
                colors = SliderDefaults.colors(
                    thumbColor = OCColors.Orange,
                    activeTrackColor = OCColors.Orange,
                    inactiveTrackColor = track,
                ),
            )
            Row(Modifier.fillMaxWidth().padding(horizontal = 6.dp), horizontalArrangement = Arrangement.SpaceBetween) {
                Text("48:12", style = OCText.Timecode, color = secondary)
                Text("-1:22:40", style = OCText.Timecode, color = secondary)
            }
        }

        Row(
            Modifier.fillMaxWidth().padding(horizontal = 50.dp, vertical = 14.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            OCSkipButton(false, 30, primary, secondary) {}
            OCPlayRing(playing, { playing = !playing })
            OCSkipButton(true, 30, primary, secondary) {}
        }

        Row(
            Modifier.fillMaxWidth().padding(horizontal = 26.dp).padding(bottom = 14.dp),
            horizontalArrangement = Arrangement.SpaceAround,
        ) {
            Effect(Icons.Filled.Speed, "1.3×", secondary, active = true)
            Effect(Icons.Filled.GraphicEq, "Smart Speed", secondary, active = true)
            Effect(Icons.Filled.VolumeUp, "Voice Boost", secondary, active = true)
            Effect(Icons.Filled.StarBorder, "Star", secondary, active = false)
        }
    }
}

@Composable
private fun Effect(icon: androidx.compose.ui.graphics.vector.ImageVector, label: String, secondary: Color, active: Boolean) {
    val c = if (active) OCColors.Orange else secondary
    Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(5.dp)) {
        Icon(icon, label, tint = c, modifier = Modifier.size(19.dp))
        Text(label, style = OCText.Chip, color = c)
    }
}
```

### Smart Speed / Voice Boost Toggle Row

```kotlin
import androidx.compose.material3.HorizontalDivider
import androidx.compose.material3.Switch
import androidx.compose.material3.SwitchDefaults

@Composable
fun OCEffectToggleRow(
    title: String, subtitle: String, checked: Boolean, onCheckedChange: (Boolean) -> Unit,
    dark: Boolean,
) {
    val primary = if (dark) OCColors.TextPrimaryDk else OCColors.TextPrimaryLt
    val secondary = if (dark) OCColors.TextSecondaryDk else OCColors.TextSecondaryLt
    val divider = if (dark) OCColors.DividerDk else OCColors.DividerLight
    val track = if (dark) OCColors.Surface2 else OCColors.DividerLight

    Row(
        Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 15.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.SpaceBetween,
    ) {
        Column(Modifier.weight(1f)) {
            Text(title, style = OCText.Toggle, color = primary)
            Text(subtitle, style = OCText.PlDesc, color = secondary)
        }
        Switch(
            checked = checked, onCheckedChange = onCheckedChange,
            colors = SwitchDefaults.colors(
                checkedThumbColor = Color.White,
                checkedTrackColor = OCColors.Orange,
                uncheckedThumbColor = Color.White,
                uncheckedTrackColor = track,
            ),
        )
    }
    HorizontalDivider(color = divider)
}
```

### Playlist Row

```kotlin
@Composable
fun OCPlaylistRow(
    icon: androidx.compose.ui.graphics.vector.ImageVector,
    tile: Color, name: String, desc: String, count: Int, dark: Boolean,
) {
    val primary = if (dark) OCColors.TextPrimaryDk else OCColors.TextPrimaryLt
    val secondary = if (dark) OCColors.TextSecondaryDk else OCColors.TextSecondaryLt
    val divider = if (dark) OCColors.DividerDk else OCColors.DividerLight

    Row(
        Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Box(
            Modifier.size(38.dp).clip(RoundedCornerShape(9.dp)).background(tile),
            contentAlignment = Alignment.Center,
        ) { Icon(icon, name, tint = Color.White, modifier = Modifier.size(18.dp)) }
        Column(Modifier.weight(1f)) {
            Text(name, style = OCText.RowTitle, color = primary)
            Text(desc, style = OCText.PlDesc, color = secondary, maxLines = 1)
        }
        Text("$count", style = OCText.Count, color = secondary)
    }
    HorizontalDivider(color = divider)
}
```

### Buttons

```kotlin
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.OutlinedButton

@Composable
fun OCPrimaryButton(title: String, onClick: () -> Unit) {
    Button(
        onClick = onClick,
        shape = RoundedCornerShape(10.dp),
        colors = ButtonDefaults.buttonColors(containerColor = OCColors.Orange, contentColor = Color.White),
        contentPadding = PaddingValues(horizontal = 26.dp, vertical = 14.dp),
    ) { Text(title, style = OCText.Button) }
}

@Composable
fun OCOutlineButton(title: String, onClick: () -> Unit) {
    OutlinedButton(
        onClick = onClick,
        shape = RoundedCornerShape(10.dp),
        border = BorderStroke(1.5.dp, OCColors.Orange),
        colors = ButtonDefaults.outlinedButtonColors(contentColor = OCColors.Orange),
        contentPadding = PaddingValues(horizontal = 22.dp, vertical = 12.dp),
    ) { Text(title, style = OCText.RowTitle) }
}
```

## 4. Core Navigation (Bottom Bar)

Overcast has a 4-tab bottom strip. No tint pill — active is just the single orange accent.

```kotlin
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults
import androidx.compose.material.icons.automirrored.filled.List
import androidx.compose.material.icons.filled.GridView
import androidx.compose.material.icons.filled.Search
import androidx.compose.material.icons.filled.Settings

@Composable
fun OCBottomBar(selected: Int, onSelect: (Int) -> Unit, dark: Boolean) {
    NavigationBar(
        containerColor = if (dark) OCColors.Canvas else OCColors.Paper,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Playlists" to Icons.AutoMirrored.Filled.List,
            "Podcasts"  to Icons.Filled.GridView,
            "Search"    to Icons.Filled.Search,
            "Settings"  to Icons.Filled.Settings,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = OCText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = OCColors.Orange,
                    selectedTextColor = OCColors.Orange,
                    unselectedIconColor = OCColors.TextTertiary,
                    unselectedTextColor = OCColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Overcast has none
                ),
            )
        }
    }
}
```

## 5. Navigation

Use Navigation Compose / Nav3 with four destinations (Playlists, Podcasts, Search, Settings) plus a modal `NowPlaying` route presented as a bottom sheet that expands from the mini-player. Page transitions slide horizontally `tween(300)`; the mini-player → full-player transition is a `slideInVertically` from the bottom `tween(300)` with the artwork as a shared element transitioning into the cover.

## 6. Motion

Overcast motion is quiet and editorial — 150–300ms ease ranges. Only the player cover gets a soft shadow.

| Moment | Compose recipe |
|--------|----------------|
| Play/pause | icon swap `Crossfade(playing, tween(200))`; ring press scale `0.96` via `animateFloatAsState` |
| Scrubber drag | `Slider` thumb; grow via `interactionSource` pressed → thumb radius animate |
| Toggle flip | `Switch` animates track cross-fade to `#FC7E0F` implicitly |
| Mini → full player | `slideInVertically(tween(300))` + shared-element artwork |
| Playlist row swipe | `SwipeToDismissBox` 1:1 reveal, 50% commit, `spring(dampingRatio = 0.85f)` |
| Theme switch | `animateColorAsState` on canvas/text `tween(250)` — cross-fade, no hard cut |
| Page navigation | `NavHost` slide push `tween(300)` |

```kotlin
// Ring press — the canonical Overcast micro-motion
val scale by animateFloatAsState(if (pressed) 0.96f else 1f, tween(120), label = "ringScale")
```

Haptics: use `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on play, toggle flip, scrubber boundary, and the recommend action. The Smart Speed time-saved stat updates silently — no haptic.

## 7. Icons

Overcast uses simple stroked glyphs; the play control is an orange ring (a bordered `Box`, not an icon). `androidx.compose.material:material-icons-extended` covers the rest.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Playlists (tab) | `list.bullet` | `Icons.AutoMirrored.Filled.List` |
| Podcasts (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Settings (tab) | `gearshape` | `Icons.Filled.Settings` |
| Play / Pause | `play.fill` / `pause.fill` | `Icons.Filled.PlayArrow` / `Icons.Filled.Pause` |
| Skip back / forward | `chevron.left.2` / `chevron.right.2` | `Icons.Filled.FastRewind` / `Icons.Filled.FastForward` |
| Smart Speed | `waveform.path` | `Icons.Filled.GraphicEq` |
| Voice Boost | `speaker.wave.2.fill` | `Icons.Filled.VolumeUp` |
| Playback speed | `speedometer` | `Icons.Filled.Speed` |
| Star | `star` / `star.fill` | `Icons.Filled.StarBorder` / `Icons.Filled.Star` |
| In Progress (playlist) | `clock.arrow.circlepath` | `Icons.Filled.History` |
| Downloaded | `arrow.down.circle.fill` | `Icons.Filled.DownloadDone` |
| Collapse player | `chevron.left` | `Icons.AutoMirrored.Filled.KeyboardArrowLeft` |
| Queue / list | `list.bullet` | `Icons.AutoMirrored.Filled.List` |
| Recommend | `hand.thumbsup` | `Icons.Filled.ThumbUp` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Add podcast | `plus` | `Icons.Filled.Add` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Slider`, modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the paper-cream light theme wants dark-content system bars (light-content in dark mode). The mini-player sits directly above the `NavigationBar` inside the navigation-bar inset.
- **Paper canvas**: use `#FBFAF6` (surfaces `#F7F5EF`) for light — never pure white. Do **not** enable Material You `dynamicColorScheme()`; the warm paper + single-orange identity must hold regardless of wallpaper.
- **Tabular timecodes**: render durations/timecodes in a fixed-width `Box` (or `tnum` font feature on API 26+) so digits never shift while scrubbing.
- **Orange ring**: the play button is a `Box` with `Modifier.border(2.5.dp, OCColors.Orange, CircleShape)` — never a filled circle.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen titles, episode titles, body, metadata. Pin layout-sensitive text (10sp tab labels, effect chips, timecodes, counts, skip-interval labels) via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: label the play ring "Play" / "Pause, {episode}"; the `Slider` exposes a percentage `stateDescription` — add custom actions for "Skip forward {n} seconds" / "Skip back {n} seconds"; playlist rows announce "Playlist: {name}, {count} episodes, {description}".
- **Touch targets**: Material guidance is 48.dp. The 66.dp play ring is fine; give 27.dp skip glyphs a 48.dp hit via padding; playlist rows are full-row tappable ≥56.dp; tab items are 48.dp by default.
- **Contrast**: `#FC7E0F` keeps ≥3:1 against both `#FBFAF6` and `#121212` for the ring/icons; `#1A1A1A` on `#FBFAF6` passes WCAG AAA for body. Never rely on orange alone for active effects — pair Smart Speed / Voice Boost ON with the labeled toggle + time-saved stat.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the scrubber-thumb grow, the play/pause crossfade, and the theme cross-fade; keep state changes.
- **Dark mode**: invert via the dark token set — true `#121212`, `#F2F2F2` text; the single orange accent is unchanged.
