# Zoom (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Zoom's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Zoom's dark in-call theater, single-blue accent, the gallery video grid, the floating control bar) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `LazyVerticalGrid` for the gallery, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and `androidx.compose.material:material-icons-extended`.

## 1. Color Tokens

```kotlin
// ui/theme/ZoomColors.kt
import androidx.compose.ui.graphics.Color

object ZoomColors {
    // Dark / in-call theater
    val Canvas   = Color(0xFF1A1A1A)
    val Surface1 = Color(0xFF2D2D2D)
    val Surface2 = Color(0xFF3A3A3A)
    val Divider  = Color(0xFF3A3A3A)

    // Light (outside call)
    val LightCanvas  = Color(0xFFFFFFFF)
    val LightSurface = Color(0xFFF5F5F5)
    val LightDivider = Color(0xFFE5E5E5)

    // Text (dark)
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFB0B0B0)
    val TextTertiary  = Color(0xFF7A7A7A)

    // Brand & Semantic
    val Blue        = Color(0xFF2D8CFF)
    val BluePressed = Color(0xFF1F6FCC)
    val Red         = Color(0xFFE02828)
    val RedPressed  = Color(0xFFB91F1F)
    val HandYellow  = Color(0xFFF5C518)
    val Success     = Color(0xFF0E8A45)
}
```

Wire it into a Material 3 `darkColorScheme` for the in-call theater and a `lightColorScheme` for outside-call surfaces so ripples and default components inherit the brand.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val ZoomDark = darkColorScheme(
    primary       = ZoomColors.Blue,
    onPrimary     = Color.White,
    background     = ZoomColors.Canvas,
    onBackground   = ZoomColors.TextPrimary,
    surface        = ZoomColors.Surface1,
    onSurface      = ZoomColors.TextPrimary,
    surfaceVariant = ZoomColors.Surface2,
    outline        = ZoomColors.Divider,
    error          = ZoomColors.Red,
)

private val ZoomLight = lightColorScheme(
    primary    = ZoomColors.Blue,
    onPrimary  = Color.White,
    background = ZoomColors.LightCanvas,
    surface    = ZoomColors.LightSurface,
    outline    = ZoomColors.LightDivider,
    error      = ZoomColors.Red,
)

@Composable
fun ZoomTheme(inCall: Boolean = false, content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (inCall) ZoomDark else ZoomLight, // in-call is always the dark theater
        typography  = ZoomTypography,
        content     = content,
    )
```

## 2. Typography

Zoom uses a custom grotesque; Lato (or Inter) is the closest free substitute. Drop the TTFs in `res/font/` (lowercase, snake_case). The call timer and meeting IDs need tabular figures.

```kotlin
// ui/theme/ZoomType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Lato = FontFamily(
    Font(R.font.lato_regular,  FontWeight.Normal),   // 400
    Font(R.font.lato_semibold, FontWeight.SemiBold), // 600
    Font(R.font.lato_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object ZoomText {
    val TitleLarge   = TextStyle(Lato, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.3).sp)
    val MeetingTopic = TextStyle(Lato, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Section      = TextStyle(Lato, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val ListTitle    = TextStyle(Lato, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 21.sp)
    val Body         = TextStyle(Lato, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Button       = TextStyle(Lato, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 17.sp)
    val ControlLabel = TextStyle(Lato, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 12.sp)
    val Metadata     = TextStyle(Lato, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val TileName     = TextStyle(Lato, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp)
    val Timer        = TextStyle(Lato, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val Tab          = TextStyle(Lato, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp)
    val TinyUpper    = TextStyle(Lato, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val ZoomTypography = Typography(
    headlineLarge = ZoomText.TitleLarge,
    headlineSmall = ZoomText.MeetingTopic,
    titleMedium   = ZoomText.ListTitle,
    bodyMedium    = ZoomText.Body,
    labelSmall    = ZoomText.Tab,
)
```

## 3. Signature Components

### Gallery Video Tile (the signature element)

```kotlin
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.animation.core.tween

@Composable
fun GalleryTile(
    name: String,
    isMuted: Boolean,
    isActiveSpeaker: Boolean,
    hasVideo: Boolean = false,
    modifier: Modifier = Modifier,
) {
    val border by animateDpAsState(if (isActiveSpeaker) 3.dp else 0.dp, tween(220), label = "speakerBorder")
    val initials = name.split(" ").take(2).mapNotNull { it.firstOrNull() }.joinToString("")

    Box(
        modifier = modifier
            .aspectRatio(16f / 9f)
            .clip(RoundedCornerShape(8.dp))
            .background(ZoomColors.Surface2)
            .border(border, ZoomColors.Blue, RoundedCornerShape(8.dp)),
        contentAlignment = Alignment.Center,
    ) {
        if (!hasVideo) {
            Box(
                Modifier.size(56.dp).clip(CircleShape).background(ZoomColors.Blue.copy(alpha = 0.25f)),
                contentAlignment = Alignment.Center,
            ) { Text(initials, fontSize = 20.sp, fontWeight = FontWeight.SemiBold, color = Color.White) }
        }
        Row(
            Modifier.align(Alignment.BottomStart).padding(8.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            if (isMuted) {
                Box(
                    Modifier.size(22.dp).clip(CircleShape).background(ZoomColors.Red),
                    contentAlignment = Alignment.Center,
                ) { Icon(Icons.Filled.MicOff, null, tint = Color.White, modifier = Modifier.size(12.dp)) }
            }
            Box(
                Modifier.clip(RoundedCornerShape(4.dp)).background(Color.Black.copy(alpha = 0.45f))
                    .padding(horizontal = 6.dp, vertical = 3.dp),
            ) { Text(name, style = ZoomText.TileName, color = Color.White) }
        }
    }
}
```

### Primary "Join" Button (the signature CTA)

```kotlin
@Composable
fun JoinButton(text: String = "Join", onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f,
        spring(dampingRatio = 0.8f, stiffness = 600f), label = "joinScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .scale(scale)
            .fillMaxWidth()
            .height(48.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) ZoomColors.BluePressed else ZoomColors.Blue)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = ZoomText.Button, color = Color.White)
    }
}
```

### In-Call Control Bar

```kotlin
@Composable
fun ControlBar(
    micOn: Boolean,
    videoOn: Boolean,
    onToggleMic: () -> Unit,
    onToggleVideo: () -> Unit,
    onLeave: () -> Unit,
) {
    Row(
        modifier = Modifier
            .padding(horizontal = 12.dp)
            .fillMaxWidth()
            .height(72.dp)
            .clip(RoundedCornerShape(16.dp))
            .background(ZoomColors.Surface1.copy(alpha = 0.96f))
            .padding(horizontal = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        ControlItem(if (micOn) Icons.Filled.Mic else Icons.Filled.MicOff,
            if (micOn) "Mute" else "Unmute",
            if (micOn) Color.White else ZoomColors.Red, Modifier.weight(1f), onToggleMic)
        ControlItem(if (videoOn) Icons.Filled.Videocam else Icons.Filled.VideocamOff,
            if (videoOn) "Stop Video" else "Start Video", Color.White, Modifier.weight(1f), onToggleVideo)
        ControlItem(Icons.Filled.ScreenShare, "Share", Color.White, Modifier.weight(1f)) {}
        ControlItem(Icons.Filled.People, "Participants", Color.White, Modifier.weight(1f)) {}
        ControlItem(Icons.Filled.EmojiEmotions, "React", Color.White, Modifier.weight(1f)) {}

        Box(
            Modifier
                .padding(start = 4.dp)
                .clip(RoundedCornerShape(8.dp))
                .background(ZoomColors.Red)
                .clickable(onClick = onLeave)
                .padding(horizontal = 18.dp, vertical = 8.dp),
        ) { Text("Leave", color = Color.White, fontWeight = FontWeight.Bold, fontSize = 15.sp) }
    }
}

@Composable
private fun ControlItem(icon: ImageVector, label: String, tint: Color, modifier: Modifier, onClick: () -> Unit) {
    Column(
        modifier.clickable(onClick = onClick),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Icon(icon, label, tint = tint, modifier = Modifier.size(24.dp))
        Text(label, style = ZoomText.ControlLabel, color = tint)
    }
}
```

### Meeting List Row

```kotlin
@Composable
fun MeetingRow(time: String, topic: String, subtitle: String, onJoin: () -> Unit) {
    Row(
        Modifier.fillMaxWidth().height(72.dp).background(ZoomColors.Surface1).padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Text(time, style = ZoomText.Metadata, color = ZoomColors.TextSecondary, modifier = Modifier.width(64.dp))
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(topic, style = ZoomText.ListTitle, color = Color.White)
            Text(subtitle, style = ZoomText.Metadata, color = ZoomColors.TextSecondary)
        }
        Box(
            Modifier.clip(CircleShape).background(ZoomColors.Blue)
                .clickable(onClick = onJoin).padding(horizontal = 18.dp, vertical = 7.dp),
        ) { Text("Join", color = Color.White, fontWeight = FontWeight.Bold, fontSize = 14.sp) }
    }
}
```

### Recording Indicator

```kotlin
@Composable
fun RecordingIndicator() {
    val t = rememberInfiniteTransition(label = "rec")
    val scale by t.animateFloat(1f, 0.7f,
        infiniteRepeatable(tween(1200), RepeatMode.Reverse), label = "recDot")
    Row(
        Modifier.clip(CircleShape).background(Color.Black.copy(alpha = 0.45f))
            .padding(horizontal = 10.dp, vertical = 6.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Box(Modifier.size(10.dp).scale(scale).clip(CircleShape).background(ZoomColors.Red))
        Text("RECORDING", style = ZoomText.TinyUpper, color = Color.White)
    }
}
```

## 4. Gallery Grid Layout

Compute the column count from participant count; animate reflow on join/leave with `animateItemPlacement()`.

```kotlin
@Composable
fun GalleryGrid(participants: List<Participant>) {
    val cols = when {
        participants.size <= 1 -> 1
        participants.size <= 4 -> 2
        else -> 3
    }
    LazyVerticalGrid(
        columns = GridCells.Fixed(cols),
        contentPadding = PaddingValues(4.dp),
        horizontalArrangement = Arrangement.spacedBy(4.dp),
        verticalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        items(participants, key = { it.id }) { p ->
            GalleryTile(
                name = p.name, isMuted = p.isMuted, isActiveSpeaker = p.isSpeaking,
                modifier = Modifier.animateItemPlacement(tween(300)),
            )
        }
    }
}

data class Participant(val id: String, val name: String, val isMuted: Boolean, val isSpeaking: Boolean)
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. The active tint is Zoom Blue. This is the outside-call surface — it follows the light/dark scheme.

```kotlin
@Composable
fun ZoomBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = ZoomColors.Surface1) {
        val items = listOf(
            "Meetings"  to Icons.Filled.Videocam,
            "Team Chat" to Icons.Filled.Chat,
            "Mail"      to Icons.Filled.Email,
            "Phone"     to Icons.Filled.Call,
            "More"      to Icons.Filled.MoreHoriz,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = ZoomText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = ZoomColors.Blue,
                    selectedTextColor = ZoomColors.Blue,
                    unselectedIconColor = ZoomColors.TextSecondary,
                    unselectedTextColor = ZoomColors.TextSecondary,
                    indicatorColor = Color.Transparent,
                ),
            )
        }
    }
}
```

The in-call control bar is **not** part of the `NavigationBar` — it is a floating `Surface` overlaid on the video stage, rendered above the gallery in a `Box` and auto-hidden after inactivity.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Join button tap | `animateFloatAsState` 1 → 0.98 with `spring(dampingRatio = 0.8f)`, `HapticFeedbackType.LongPress` |
| Active-speaker border | `animateDpAsState` 0 → 3.dp `tween(220)`, debounce the input ~300ms |
| Control bar auto-hide | `AnimatedVisibility` slide-down + fade `tween(250)` after 4s idle; restore on stage tap |
| Reactions float | `Animatable` offset y 0 → -120.dp + alpha 1 → 0 over ~3s |
| Grid reflow | `Modifier.animateItemPlacement(tween(300))` on each tile |
| Recording dot | `rememberInfiniteTransition` scale 1.0 ↔ 0.7 `tween(1200)` reverse |

```kotlin
// Control bar auto-hide
AnimatedVisibility(
    visible = !barHidden,
    enter = slideInVertically { it } + fadeIn(tween(200)),
    exit  = slideOutVertically { it } + fadeOut(tween(250)),
) { ControlBar(/* … */) }
```

Haptics: prefer `LocalHapticFeedback`. For a crisper tick matching iOS impact use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) on Join, mute toggle, and Leave-confirm.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Mic on / off | `mic.fill` / `mic.slash.fill` | `Icons.Filled.Mic` / `Icons.Filled.MicOff` |
| Video on / off | `video.fill` / `video.slash.fill` | `Icons.Filled.Videocam` / `Icons.Filled.VideocamOff` |
| Share | `square.and.arrow.up` | `Icons.Filled.ScreenShare` |
| Participants | `person.2.fill` | `Icons.Filled.People` |
| React | `face.smiling` | `Icons.Filled.EmojiEmotions` |
| Leave | `phone.down.fill` | `Icons.Filled.CallEnd` |
| Raise hand | `hand.raised.fill` | `Icons.Filled.BackHand` |
| Camera flip | `arrow.triangle.2.circlepath.camera` | `Icons.Filled.FlipCameraIos` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Meetings (tab) | `video.fill` | `Icons.Filled.Videocam` |
| Team Chat (tab) | `bubble.left.and.bubble.right.fill` | `Icons.Filled.Chat` |
| Mail (tab) | `envelope.fill` | `Icons.Filled.Email` |
| Phone (tab) | `phone.fill` | `Icons.Filled.Call` |
| More (tab) | `ellipsis` | `Icons.Filled.MoreHoriz` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; in-call wants light-content system bars over the dark theater. Apply `Scaffold` / `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the floating control bar clears the gesture nav.
- **Keep screen on**: set `keepScreenOn = true` (e.g. `Modifier` via `View.keepScreenOn` or `WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON`) for the in-call screen.
- **Tabular numerics**: the call timer and meeting IDs should not reflow — prefer a fixed-advance digit font or set `TextStyle(fontFeatureSettings = "tnum")`.
- **Font scaling**: `sp` honors the user scale — keep it on titles/body/metadata. Clamp tile name labels and pin the timer + 11.sp control labels (derive from `dp` or wrap in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`).
- **TalkBack**: announce mic/video state changes ("Microphone muted"); set `contentDescription` on the active-speaker tile ("Alex Rivera, speaking"); keep the control bar in the semantics tree even when visually auto-hidden, and disable auto-hide when a screen reader is active.
- **Touch targets**: Material guidance is 48.dp minimum — the control items are full-bar-height columns, well clear; ensure the 24.dp glyphs get a 48.dp hit area via the column padding.
- **Contrast**: `#B0B0B0` on `#1A1A1A` passes WCAG AA at 13sp+. Validate the 10sp tab and 11sp control labels and bump toward `#C4C4C4` if targeting strict accessibility compliance.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Zoom's identity requires the fixed blue accent and dark theater regardless of wallpaper.
