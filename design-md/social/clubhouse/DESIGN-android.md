# Clubhouse (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Clubhouse's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, the serif/sans `Typography` pairing, paste-ready `@Composable`s for the signature speaking-pulse avatar / room stage / raise-hand bar, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Clubhouse's warm cream hallway, serif titles, circular speaker faces, the emerald speaking pulse, the all-pill control language) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a SwiftUI sheet, `infiniteRepeatable` for the pulse, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatar photos. No color extraction — Clubhouse's palette is fixed warm tokens, so Palette is not needed. **Do not enable Material You — the warm cream + emerald identity must hold regardless of wallpaper.**

## 1. Color Tokens

```kotlin
// ui/theme/ClubhouseColors.kt
import androidx.compose.ui.graphics.Color

object ChColors {
    // Light — the Hallway
    val Cream        = Color(0xFFF2EFE4)
    val CreamCard    = Color(0xFFFBF9F1)
    val CreamPressed = Color(0xFFEAE6D8)
    val DividerLight = Color(0xFFDDD8C8)

    // Dark — the Room (warm near-black, NOT slate)
    val Room         = Color(0xFF1A1A1A)
    val DarkSurface1 = Color(0xFF242220)
    val DarkSurface2 = Color(0xFF2E2B27)
    val DividerDark  = Color(0xFF3A372F)

    // Text
    val Ink          = Color(0xFF1A1A1A)
    val InkSecondary = Color(0xFF6B6453)
    val InkTertiary  = Color(0xFF9A9483)
    val CreamText    = Color(0xFFF2EFE4)
    val CreamText2   = Color(0xFFB8B2A0)
    val CreamText3   = Color(0xFF837D6D)

    // Brand — the single accent
    val Emerald      = Color(0xFF38B569)
    val EmeraldDeep  = Color(0xFF2E9A58)
    val EmeraldSoft  = Color(0x2E38B569) // 18% — pulse halo / tinted chip
    val EmeraldInk   = Color(0xFF07210F) // text on emerald

    // Semantic
    val Gold         = Color(0xFFE9C46A) // moderator
    val Leave        = Color(0xFFE5575C)
    val Scrim        = Color(0x80141210) // warm, NOT black

    // Avatar gradient palette (content color)
    val PeachA = Color(0xFFF4C77B); val PeachB = Color(0xFFE0A24B)
    val SageA  = Color(0xFF9FD8B4); val SageB  = Color(0xFF5FB484)
    val LavA   = Color(0xFFC9B6E8); val LavB   = Color(0xFF9C7FD0)
    val CoralA = Color(0xFFF2A9A0); val CoralB = Color(0xFFD9776B)
    val SkyA   = Color(0xFFA9C7E8); val SkyB   = Color(0xFF6E9CD0)
}

val chAvatarPairs = listOf(
    ChColors.PeachA to ChColors.PeachB,
    ChColors.SageA  to ChColors.SageB,
    ChColors.LavA   to ChColors.LavB,
    ChColors.CoralA to ChColors.CoralB,
    ChColors.SkyA   to ChColors.SkyB,
)
fun chAvatarFor(id: Int) = chAvatarPairs[kotlin.math.abs(id) % chAvatarPairs.size]
```

Wire it into both schemes. The light scheme is the warm cream hallway; the dark scheme is the warm Room — never a cold slate.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val ChLight = lightColorScheme(
    primary        = ChColors.Emerald,
    onPrimary      = ChColors.EmeraldInk,
    background     = ChColors.Cream,
    onBackground   = ChColors.Ink,
    surface        = ChColors.CreamCard,
    onSurface      = ChColors.Ink,
    surfaceVariant = ChColors.CreamPressed,
    outline        = ChColors.DividerLight,
    error          = ChColors.Leave,
)

private val ChDark = darkColorScheme(
    primary        = ChColors.Emerald,        // identical accent in both modes
    onPrimary      = ChColors.EmeraldInk,
    background     = ChColors.Room,            // warm near-black
    onBackground   = ChColors.CreamText,
    surface        = ChColors.DarkSurface1,
    onSurface      = ChColors.CreamText,
    surfaceVariant = ChColors.DarkSurface2,
    outline        = ChColors.DividerDark,
    error          = ChColors.Leave,
)

@Composable
fun ClubhouseTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) ChDark else ChLight,
    typography = ChTypography,
    content = content,
)
```

## 2. Typography

Clubhouse pairs a serif display for titles with a sans for UI. Drop **DM Serif Display** (titles) + **Inter** (UI) TTFs in `res/font/` — both SIL OFL.

```kotlin
// ui/theme/ChType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val DMSerif = FontFamily(
    Font(R.font.dmserifdisplay_regular, FontWeight.Normal),
    Font(R.font.dmserifdisplay_italic,  FontWeight.Normal, FontStyle.Italic),
)
val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
    Font(R.font.inter_bold,     FontWeight.Bold),
)

// Named ramp — mirrors DESIGN.md §3 (pt → sp 1:1)
object ChText {
    // Serif display — titles, topics, greetings, quotes
    val RoomTitle   = TextStyle(DMSerif, fontWeight = FontWeight.Normal, fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.2).sp)
    val ScreenTitle = TextStyle(DMSerif, fontWeight = FontWeight.Normal, fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp)
    val Quote       = TextStyle(DMSerif, fontWeight = FontWeight.Normal, fontStyle = FontStyle.Italic, fontSize = 20.sp, lineHeight = 26.sp)

    // Sans (Inter) — every UI text
    val Section    = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Subsection = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 21.sp)
    val Body       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val Name       = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 18.sp)
    val Button     = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 16.sp)
    val Meta       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val RoleTag    = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 14.sp)
    val Overline   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.8.sp)
    val Tab        = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp)
}

// Map onto Material 3 slots
val ChTypography = Typography(
    headlineLarge = ChText.RoomTitle,
    headlineMedium = ChText.ScreenTitle,
    titleMedium   = ChText.Section,
    bodyMedium    = ChText.Body,
    labelSmall    = ChText.Tab,
)
```

## 3. Signature Components

### Speaking-Pulse Avatar (the core atom)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.alpha
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun ChSpeakerAvatar(
    id: Int,
    initials: String,
    name: String,
    role: String? = null,
    isSpeaking: Boolean = false,
    isMuted: Boolean = false,
    isHost: Boolean = false,
    size: Dp = 60.dp,
) {
    val (a, b) = chAvatarFor(id)
    val transition = rememberInfiniteTransition(label = "pulse")
    val haloScale by transition.animateFloat(
        initialValue = 1f, targetValue = if (isSpeaking) 1.12f else 1f,
        animationSpec = infiniteRepeatable(tween(900, easing = LinearEasing), RepeatMode.Reverse),
        label = "haloScale",
    )

    Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(7.dp)) {
        Box(contentAlignment = Alignment.Center, modifier = Modifier.size(size + 16.dp)) {
            if (isSpeaking) {
                Box(
                    Modifier
                        .size(size + 16.dp)
                        .scale(haloScale)
                        .clip(CircleShape)
                        .background(ChColors.EmeraldSoft)
                )
            }
            Box(
                Modifier
                    .size(size)
                    .clip(CircleShape)
                    .background(Brush.linearGradient(listOf(a, b)))
                    .then(if (isSpeaking) Modifier.border(3.dp, ChColors.Emerald, CircleShape) else Modifier)
                    .alpha(if (isMuted) 0.55f else 1f),
                contentAlignment = Alignment.Center,
            ) {
                Text(initials, fontFamily = Inter, fontWeight = FontWeight.Bold,
                    fontSize = (size.value * 0.33f).sp, color = Color(0xFF2A2620))
            }
            if (isMuted) {
                Box(
                    Modifier.align(Alignment.BottomEnd).size(22.dp).clip(CircleShape)
                        .background(ChColors.DarkSurface2).border(2.dp, ChColors.Room, CircleShape),
                    contentAlignment = Alignment.Center,
                ) { Icon(Icons.Filled.MicOff, null, tint = ChColors.CreamText2, modifier = Modifier.size(11.dp)) }
            }
            if (isHost) {
                Box(
                    Modifier.align(Alignment.TopEnd).size(20.dp).clip(CircleShape)
                        .background(ChColors.DarkSurface2).border(2.dp, ChColors.Room, CircleShape),
                    contentAlignment = Alignment.Center,
                ) { Text("🎙️", fontSize = 11.sp) }
            }
        }
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Text(name, style = ChText.Name, maxLines = 1,
                color = if (role == "Moderator") ChColors.Gold else ChColors.CreamText)
            if (role != null) Text(role, fontFamily = Inter, fontWeight = FontWeight.Medium,
                fontSize = 11.sp, color = ChColors.CreamText3)
        }
    }
}
```

### Room Stage (grouped speaker grid)

```kotlin
import androidx.compose.foundation.lazy.grid.GridCells
import androidx.compose.foundation.lazy.grid.LazyVerticalGrid
import androidx.compose.foundation.lazy.grid.GridItemSpan
import androidx.compose.foundation.lazy.grid.items

data class ChSpeaker(val id: Int, val initials: String, val name: String, val role: String? = null,
                     val speaking: Boolean = false, val muted: Boolean = false, val host: Boolean = false)
data class ChGroup(val label: String, val speakers: List<ChSpeaker>)

@Composable
fun ChRoomStage(groups: List<ChGroup>) {
    LazyVerticalGrid(
        columns = GridCells.Fixed(4),
        modifier = Modifier.fillMaxSize().background(ChColors.Room),
        contentPadding = PaddingValues(horizontal = 16.dp),
        verticalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        groups.forEach { group ->
            item(span = { GridItemSpan(maxLineSpan) }) {
                Text(group.label.uppercase(), style = ChText.Overline, color = ChColors.CreamText3,
                    modifier = Modifier.padding(start = 6.dp, top = 14.dp, bottom = 10.dp))
            }
            items(group.speakers, key = { it.id }) { s ->
                ChSpeakerAvatar(s.id, s.initials, s.name, s.role, s.speaking, s.muted, s.host)
            }
        }
    }
}
```

### Raise-Hand Bar (footer)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Mic
import androidx.compose.material.icons.filled.MicOff
import androidx.compose.material3.Icon
import androidx.compose.ui.draw.rotate
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun ChRaiseHandBar() {
    var raised by remember { mutableStateOf(false) }
    var waveTrigger by remember { mutableIntStateOf(0) }
    val haptics = LocalHapticFeedback.current

    val wave by animateFloatAsState(
        targetValue = 0f,
        animationSpec = keyframes {
            durationMillis = 600
            (-15f) at 100; 15f at 250; (-15f) at 400; 15f at 500; 0f at 600
        },
        label = "wave-$waveTrigger",
    )

    Row(
        Modifier
            .fillMaxWidth()
            .background(ChColors.Room)
            .drawTopHairline(ChColors.DividerDark)
            .padding(horizontal = 20.dp, vertical = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.SpaceBetween,
    ) {
        Row(
            Modifier
                .clip(RoundedCornerShape(50))
                .background(if (raised) ChColors.EmeraldSoft else ChColors.DarkSurface2)
                .clickable {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                    if (!raised) waveTrigger++
                    raised = !raised
                }
                .padding(horizontal = 20.dp, vertical = 12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Text("✋", fontSize = 18.sp, modifier = Modifier.rotate(if (raised) 0f else wave))
            Text(if (raised) "Hand raised" else "Raise hand", style = ChText.Button,
                color = if (raised) ChColors.Emerald else ChColors.CreamText)
        }

        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(22.dp)) {
            Box(Modifier.size(24.dp).clip(CircleShape).background(ChColors.EmeraldSoft), Alignment.Center) {
                Box(Modifier.size(14.dp).clip(CircleShape).background(ChColors.Emerald))
            }
            Icon(Icons.Filled.Mic, "Mic", tint = ChColors.CreamText2, modifier = Modifier.size(22.dp))
        }
    }
}

fun Modifier.drawTopHairline(color: Color) = drawWithContent {
    drawContent()
    drawLine(color, androidx.compose.ui.geometry.Offset(0f, 0f),
        androidx.compose.ui.geometry.Offset(size.width, 0f), strokeWidth = 1f)
}
```

### Hallway Room Card

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Card
import androidx.compose.material3.CardDefaults

@Composable
fun ChHallwayCard(club: String, isLive: Boolean, title: String,
                  avatars: List<Pair<Int, String>>, count: Int) {
    Card(
        shape = RoundedCornerShape(16.dp),
        colors = CardDefaults.cardColors(containerColor = ChColors.CreamCard),
        elevation = CardDefaults.cardElevation(defaultElevation = 2.dp),
    ) {
        Column(Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(10.dp)) {
            Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween,
                verticalAlignment = Alignment.CenterVertically) {
                Text(club, style = ChText.Meta.copy(fontWeight = FontWeight.SemiBold), color = ChColors.InkSecondary)
                Box(
                    Modifier.clip(RoundedCornerShape(50))
                        .background(if (isLive) ChColors.EmeraldSoft else ChColors.CreamPressed)
                        .padding(horizontal = 10.dp, vertical = 5.dp)
                ) {
                    Text(if (isLive) "● Open" else "Upcoming", style = ChText.RoleTag,
                        color = if (isLive) ChColors.Emerald else ChColors.InkSecondary)
                }
            }
            Text(title, style = ChText.RoomTitle.copy(fontSize = 20.sp), color = ChColors.Ink)
            Row(verticalAlignment = Alignment.CenterVertically) {
                avatars.take(5).forEachIndexed { i, (id, ini) ->
                    val (c1, c2) = chAvatarFor(id)
                    Box(
                        Modifier
                            .offset(x = if (i == 0) 0.dp else (-10 * i).dp)
                            .size(32.dp).clip(CircleShape)
                            .background(Brush.linearGradient(listOf(c1, c2)))
                            .border(2.dp, ChColors.CreamCard, CircleShape),
                        contentAlignment = Alignment.Center,
                    ) { Text(ini, fontFamily = Inter, fontWeight = FontWeight.Bold, fontSize = 11.sp, color = Color.White) }
                }
                Text("$count in the room", style = ChText.Meta,
                    modifier = Modifier.offset(x = (-10 * (avatars.size.coerceAtMost(5) - 1) + 16).dp))
            }
        }
    }
}
```

## 4. Primary Pill Button

```kotlin
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults

@Composable
fun ChPrimaryButton(text: String, onClick: () -> Unit) {
    Button(
        onClick = onClick,
        shape = RoundedCornerShape(50),               // full pill — every Clubhouse button
        colors = ButtonDefaults.buttonColors(
            containerColor = ChColors.Emerald,
            contentColor = ChColors.EmeraldInk,
        ),
        contentPadding = PaddingValues(horizontal = 28.dp, vertical = 14.dp),
    ) {
        Text(text, style = ChText.Button)
    }
}
```

## 5. Navigation

Clubhouse has a 4-tab bottom strip and a full-screen modal Room. On Android model the strip as a `NavigationBar` (no colored pill — active is just ink/cream) and the Room as a full-screen destination that collapses to a persistent mini-bar.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.outlined.*

@Composable
fun ChBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = ChColors.Cream, tonalElevation = 0.dp) {
        val items = listOf(
            "Hallway"  to Icons.Outlined.Home,
            "Explore"  to Icons.Outlined.Explore,
            "Activity" to Icons.Outlined.Notifications,
            "Profile"  to Icons.Outlined.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = ChText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = ChColors.Ink,        // ink, no colored tint
                    selectedTextColor   = ChColors.Ink,
                    unselectedIconColor = ChColors.InkTertiary,
                    unselectedTextColor = ChColors.InkTertiary,
                    indicatorColor      = Color.Transparent,   // NO Material pill — Clubhouse has none
                ),
            )
        }
    }
}
```

The collapsed Room mini-bar docks just above the `NavigationBar` as a `Surface` (`DarkSurface1`, 16.dp radius, 4.dp elevation) showing the truncated serif room title, the active speaker's 28.dp circular avatar with its live `Emerald` pulse still breathing, and a trailing leave ✕ in `Leave`. Tapping re-expands the full-screen Room.

## 6. Motion

Clubhouse motion is calm — the speaking pulse is a slow ~0.9s breath; sheets slide; leaving is undramatic.

| Moment | Compose recipe |
|--------|----------------|
| Speaking pulse | `rememberInfiniteTransition` → `animateFloat(1f→1.12f, infiniteRepeatable(tween(900), RepeatMode.Reverse))` on the halo scale |
| New speaker joins | `AnimatedVisibility` `scaleIn(initialScale = 0.8f, tween(200)) + fadeIn()`; grid reflows `animateItemPlacement(tween(200))` |
| Raise hand | ✋ `animateFloat` via `keyframes` (±15° ×2 over 600ms) + soft haptic; pill cross-fades to "Hand raised" emerald-tinted |
| Invited to speak | celebratory `ModalBottomSheet` slide-up + medium haptic; avatar animates between groups |
| Room enter / collapse | full-screen route slide-up `tween(300)`; collapse slide-down to docked mini-bar `tween(280)` (pulse continues) |
| Tab switch | `Crossfade(tween(200))` |
| Audience pill change | instant select + selection haptic |

```kotlin
// Speaking pulse — the canonical Clubhouse motion
val t = rememberInfiniteTransition(label = "pulse")
val s by t.animateFloat(1f, 1.12f,
    infiniteRepeatable(tween(900, easing = LinearEasing), RepeatMode.Reverse), label = "halo")
Box(Modifier.scale(s).background(ChColors.EmeraldSoft, CircleShape))
```

Haptics: use `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on raise-hand, ping, and new-speaker; for "invited to speak" use a stronger pattern via `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)`. Audience-pill change uses `HapticFeedbackConstants.CLOCK_TICK`. Leaving a room is silent — no haptic, no alarm; the voice is calm.

## 7. Icons

Clubhouse uses circular illustrated avatars (not icons) for people; the host pin is the 🎙️ emoji; "speaking" is never an icon — it is the emerald ring + halo. The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Collapse room | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Add people | `person.badge.plus` | `Icons.Filled.PersonAdd` |
| Share / more | `square.and.arrow.up` / `ellipsis` | `Icons.Filled.IosShare` / `Icons.Filled.MoreHoriz` |
| Mic on | `mic.fill` | `Icons.Filled.Mic` |
| Mic off / muted badge | `mic.slash.fill` | `Icons.Filled.MicOff` |
| Hallway (tab) | `house` | `Icons.Outlined.Home` |
| Explore (tab) | `safari` | `Icons.Outlined.Explore` |
| Activity (tab) | `bell` | `Icons.Outlined.Notifications` |
| Profile (tab) | `person.circle` | `Icons.Outlined.AccountCircle` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Reminder | `bell.badge` | `Icons.Filled.NotificationsActive` |
| Start a room (+) | `plus` | `Icons.Filled.Add` |
| Leave | `xmark` | `Icons.Filled.Close` |
| Host pin | 🎙️ (emoji) | "🎙️" text glyph (not an icon) |
| Speaking | (no icon — emerald ring + halo) | (no icon) |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; sheets + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the cream hallway wants dark-content system bars (light-content in the dark Room). The room footer / hand bar and the `NavigationBar` need `Modifier.windowInsetsPadding(WindowInsets.navigationBars)`; the collapsed mini-bar docks within the safe area above the nav bar.
- **No Material You**: do **not** enable `dynamicColorScheme()` — Clubhouse's warm cream + emerald identity must hold regardless of wallpaper; there is one fixed accent and it means "this person has the floor".
- **Warm dark mode**: the dark scheme is the warm Room (`#1A1A1A` / `#242220` / `#2E2B27`) — never let it drift to a neutral/blue `#191919`.
- **Font scaling**: `sp` honors the user's font scale — keep it on serif titles, section headers, body, speaker names. Pin layout-critical text (role tags, 11sp group overlines, 10sp tab labels, the "+N" cluster chip) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. Truncate names to 1 line.
- **TalkBack**: a speaking avatar needs `Modifier.semantics { contentDescription = "$name, speaking" }` (the pulse must NOT be the only cue); muted "{name}, muted"; host "{name}, host"; the raise-hand pill toggles its description "Raise hand" ↔ "Hand raised"; group overlines are `heading()`.
- **Touch targets**: Material guidance is 48.dp. Speaker avatars are ≥60.dp (fine); give the mic toggle (22.dp) and top-bar icons (20.dp) a 48.dp hit area via padding; the raise-hand pill is ≥48.dp tall; the whole Hallway card is clickable.
- **Contrast**: `#1A1A1A` on `#F2EFE4` and `#F2EFE4` on `#1A1A1A` pass AA; `#07210F` on `#38B569` passes AA for the CTA; the 3dp emerald ring is supplemented by the TalkBack "speaking" string for non-color users.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the breathing speaking pulse with a static 3dp `Emerald` ring (no infinite scale); sheet slides become `Crossfade`; skip the hand-wave (just cross-fade the pill).
- **Warm scrim**: `ModalBottomSheet` / dialog scrims use `ChColors.Scrim` (`0x80141210`, warm), never pure black — preserve the hallway warmth.
- **Dark mode**: invert cream → warm Room; the Emerald `#38B569` is identical in both schemes. Shadows are soft — Hallway cards use a barely-there 2.dp elevation on cream; in dark, separation comes from the warm `#242220` surface, not shadow.
