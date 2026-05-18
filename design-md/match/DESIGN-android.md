# Match (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Match's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the full-bleed swipe card + action dock, the "It's a Match!" celebration, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Match's full-bleed profile photo, the single disciplined Match Red, the floating circular dock, the warm near-black canvas) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Modifier.pointerInput` drag instead of a SwiftUI gesture, `Brush.verticalGradient` for the scrim, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for profile photos and avatars. Match's palette is fixed brand colors — no Palette extraction. Match is dark-capable; a full dark scheme is provided and the photo never dims.

## 1. Color Tokens

```kotlin
// ui/theme/MatchColors.kt
import androidx.compose.ui.graphics.Color

object MatchColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF6F4F4)
    val SurfacePressed = Color(0xFFECE8E8)
    val Divider        = Color(0xFFE7E2E2)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF141414) // warm near-black — NOT pure black
    val DarkSurface1 = Color(0xFF1E1E1E)
    val DarkSurface2 = Color(0xFF2A2424)
    val DarkDivider  = Color(0xFF322C2C)

    // Text
    val TextPrimary        = Color(0xFF1A1A1A)
    val TextSecondary      = Color(0xFF6E6666)
    val TextTertiary       = Color(0xFFA39B9B)
    val DarkTextPrimary    = Color(0xFFF2EDED)
    val DarkTextSecondary  = Color(0xFFA89E9E)

    // Brand
    val Red        = Color(0xFFE92434) // the one brand red
    val RedBright  = Color(0xFFF0203E) // energized — Like button / active tab / dark CTA
    val RedPressed = Color(0xFFC81B2C)
    val Ink        = Color(0xFF1A1A1A)

    // Functional accents (NOT decorative)
    val ActiveGreen = Color(0xFF2ECC71) // presence only
    val SuperBlue   = Color(0xFF1FB6FF) // Super Like / verified only
    val Gold        = Color(0xFFD6A75B) // Rewind / Premium
    val Boost       = Color(0xFFB36BD8) // Boost
    val Error       = Color(0xFFF2545B)

    // On-photo
    val ChipFill   = Color(0x29FFFFFF) // ~16% white
    val ChipBorder = Color(0x47FFFFFF) // ~28% white
}
```

Wire it into both schemes. Match is dark-capable; the dark scheme uses the signature warm `#141414`, never true black, and the brand red brightens to `#F0203E`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val MatchLight = lightColorScheme(
    primary        = MatchColors.Red,
    onPrimary      = Color.White,
    background     = MatchColors.Canvas,
    onBackground   = MatchColors.TextPrimary,
    surface        = MatchColors.SurfaceGray,
    onSurface      = MatchColors.TextPrimary,
    surfaceVariant = MatchColors.SurfacePressed,
    outline        = MatchColors.Divider,
    error          = MatchColors.Error,
)

private val MatchDark = darkColorScheme(
    primary        = MatchColors.RedBright, // brighter on dark photography
    onPrimary      = Color.White,
    background     = MatchColors.DarkCanvas,
    onBackground   = MatchColors.DarkTextPrimary,
    surface        = MatchColors.DarkSurface1,
    onSurface      = MatchColors.DarkTextPrimary,
    surfaceVariant = MatchColors.DarkSurface2,
    outline        = MatchColors.DarkDivider,
    error          = MatchColors.Error,
)

@Composable
fun MatchTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) MatchDark else MatchLight,
    typography = MatchTypography,
    content = content,
)
```

## 2. Typography

Match ships a custom geometric grotesque (Proxima/Poppins family). Use **Poppins** (SIL OFL) in `res/font/` as the closest free substitute. Names bold, ages a step lighter beside them — the signature Match rhythm.

```kotlin
// ui/theme/MatchType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Poppins = FontFamily(
    Font(R.font.poppins_regular,    FontWeight.Normal),
    Font(R.font.poppins_medium,     FontWeight.Medium),
    Font(R.font.poppins_semibold,   FontWeight.SemiBold),
    Font(R.font.poppins_bold,       FontWeight.Bold),
    Font(R.font.poppins_extrabold,  FontWeight.ExtraBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object MatchText {
    val Wordmark    = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 28.sp, letterSpacing = (-0.4).sp)
    val ProfileName = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 26.sp, letterSpacing = (-0.3).sp)
    val ProfileAge  = TextStyle(Poppins, fontWeight = FontWeight.Medium,    fontSize = 22.sp, letterSpacing = (-0.2).sp)
    val MatchTitle  = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, letterSpacing = 0.5.sp)
    val ScreenTitle = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 22.sp, letterSpacing = (-0.2).sp)
    val Section     = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 18.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Poppins, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val ListTitle   = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp)
    val Button      = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 16.sp, letterSpacing = 0.2.sp)
    val Meta        = TextStyle(Poppins, fontWeight = FontWeight.Normal,    fontSize = 14.sp)
    val Subtitle    = TextStyle(Poppins, fontWeight = FontWeight.Medium,    fontSize = 14.sp)
    val Chip        = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, letterSpacing = 0.1.sp)
    val Tab         = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, letterSpacing = 0.1.sp)
    val Timestamp   = TextStyle(Poppins, fontWeight = FontWeight.Normal,    fontSize = 12.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val MatchTypography = Typography(
    headlineLarge = MatchText.ProfileName,
    headlineMedium = MatchText.ScreenTitle,
    titleMedium   = MatchText.Section,
    bodyMedium    = MatchText.Body,
    labelSmall    = MatchText.Tab,
)
```

## 3. Signature Components

### Full-Bleed Profile Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Verified
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun ProfileCard(
    photoUrl: String,
    name: String,
    age: Int,
    job: String,
    distance: String,
    interests: List<String>,
    photoCount: Int,
    activeIndex: Int,
    verified: Boolean,
    modifier: Modifier = Modifier,
) {
    Box(
        modifier
            .fillMaxSize()
            .clip(RoundedCornerShape(20.dp)),
    ) {
        AsyncImage(
            model = photoUrl,
            contentDescription = "$name, $age",
            modifier = Modifier.fillMaxSize(),
            contentScale = ContentScale.Crop,
        )
        // Bottom scrim for legibility
        Box(
            Modifier
                .fillMaxSize()
                .background(
                    Brush.verticalGradient(
                        0.45f to Color.Transparent,
                        1f to Color(0xF2141414), // ~95% #141414
                    )
                )
        )
        // Story progress bars
        Row(
            Modifier.fillMaxWidth().align(Alignment.TopCenter).padding(12.dp),
            horizontalArrangement = Arrangement.spacedBy(4.dp),
        ) {
            repeat(photoCount) { i ->
                Box(
                    Modifier
                        .weight(1f).height(3.dp)
                        .clip(RoundedCornerShape(2.dp))
                        .background(if (i == activeIndex) Color.White else Color.White.copy(alpha = 0.35f))
                )
            }
        }
        // Meta block — clears the dock
        Column(
            Modifier.align(Alignment.BottomStart).padding(horizontal = 18.dp).padding(bottom = 92.dp),
            verticalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                Text(name, style = MatchText.ProfileName, color = Color.White)
                Text("$age", style = MatchText.ProfileAge, color = Color.White)
                if (verified) Icon(Icons.Filled.Verified, null, tint = MatchColors.SuperBlue, modifier = Modifier.size(17.dp))
            }
            Text(job, style = MatchText.Subtitle, color = Color.White.copy(alpha = 0.9f))
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                Box(Modifier.size(7.dp).clip(RoundedCornerShape(50)).background(MatchColors.ActiveGreen))
                Text(distance, style = MatchText.Meta, color = Color.White.copy(alpha = 0.75f))
            }
            FlowRow(horizontalArrangement = Arrangement.spacedBy(6.dp), verticalArrangement = Arrangement.spacedBy(6.dp)) {
                interests.take(4).forEach { tag ->
                    Text(
                        tag, style = MatchText.Chip, color = Color.White,
                        modifier = Modifier
                            .clip(RoundedCornerShape(50))
                            .background(MatchColors.ChipFill)
                            .border(1.dp, MatchColors.ChipBorder, RoundedCornerShape(50))
                            .padding(horizontal = 11.dp, vertical = 5.dp),
                    )
                }
            }
        }
    }
}
```

### Action Dock

```kotlin
import androidx.compose.foundation.Indication
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.material.icons.filled.*
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun ActionDock(
    onRewind: () -> Unit, onPass: () -> Unit, onSuper: () -> Unit,
    onLike: () -> Unit, onBoost: () -> Unit,
) {
    Row(
        horizontalArrangement = Arrangement.spacedBy(16.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        DockButton(Icons.Filled.Replay, 44.dp, MatchColors.Gold, onClick = onRewind)
        DockButton(Icons.Filled.Close, 56.dp, MatchColors.DarkTextSecondary, onClick = onPass)
        DockButton(Icons.Filled.Star, 48.dp, MatchColors.SuperBlue, onClick = onSuper)
        DockButton(Icons.Filled.Favorite, 56.dp, Color.White,
            fill = MatchColors.RedBright, bordered = false, onClick = onLike)
        DockButton(Icons.Filled.Bolt, 44.dp, MatchColors.Boost, onClick = onBoost)
    }
}

@Composable
private fun DockButton(
    icon: ImageVector, size: Dp, tint: Color,
    fill: Color = MatchColors.DarkSurface1, bordered: Boolean = true, onClick: () -> Unit,
) {
    val haptics = LocalHapticFeedback.current
    Box(
        Modifier
            .size(size)
            .shadow(16.dp, RoundedCornerShape(50), spotColor = Color.Black.copy(alpha = 0.5f))
            .clip(RoundedCornerShape(50))
            .background(fill)
            .then(if (bordered) Modifier.border(1.dp, MatchColors.DarkDivider, RoundedCornerShape(50)) else Modifier)
            .clickable {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // soft impact analog
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(icon, contentDescription = null, tint = tint, modifier = Modifier.size(size * 0.42f))
    }
}
```

### "It's a Match!" Celebration

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.OutlinedButton

@Composable
fun MatchCelebration(
    myAvatar: String, theirAvatar: String, theirName: String,
    onMessage: () -> Unit, onKeep: () -> Unit,
) {
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(Unit) { haptics.performHapticFeedback(HapticFeedbackType.LongPress) }

    Box(
        Modifier
            .fillMaxSize()
            .background(Brush.verticalGradient(listOf(Color(0xFF2A1418), Color(0xFF1A1A1A)))),
        contentAlignment = Alignment.Center,
    ) {
        Column(
            Modifier.padding(horizontal = 40.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(16.dp),
        ) {
            Text("It's a Match!", style = MatchText.MatchTitle, color = MatchColors.RedBright)
            Text("You and $theirName liked each other", style = MatchText.Timestamp, color = MatchColors.DarkTextSecondary)
            Row(horizontalArrangement = Arrangement.spacedBy((-14).dp)) {
                AsyncImage(myAvatar, null, Modifier.size(56.dp).clip(RoundedCornerShape(50)).border(3.dp, MatchColors.Ink, RoundedCornerShape(50)), contentScale = ContentScale.Crop)
                AsyncImage(theirAvatar, null, Modifier.size(56.dp).clip(RoundedCornerShape(50)).border(3.dp, MatchColors.Ink, RoundedCornerShape(50)), contentScale = ContentScale.Crop)
            }
            Button(
                onClick = onMessage,
                modifier = Modifier.fillMaxWidth(),
                shape = RoundedCornerShape(50),
                colors = ButtonDefaults.buttonColors(containerColor = MatchColors.RedBright),
            ) { Text("Send a Message", style = MatchText.Button, color = Color.White) }
            OutlinedButton(
                onClick = onKeep,
                modifier = Modifier.fillMaxWidth(),
                shape = RoundedCornerShape(50),
            ) { Text("Keep Swiping", style = MatchText.Button, color = MatchColors.DarkTextPrimary) }
        }
    }
}
```

### Likes You Tile

```kotlin
import androidx.compose.ui.draw.blur

@Composable
fun LikesYouTile(photoUrl: String, unlocked: Boolean) {
    Box(
        Modifier.size(96.dp, 128.dp).clip(RoundedCornerShape(14.dp)),
    ) {
        AsyncImage(
            photoUrl, null,
            modifier = Modifier
                .fillMaxSize()
                .then(if (unlocked) Modifier else Modifier.blur(7.dp)),
            contentScale = ContentScale.Crop,
        )
        if (!unlocked) {
            Text(
                "Liked you", style = MatchText.Timestamp, color = Color.White,
                modifier = Modifier
                    .align(Alignment.BottomCenter).padding(bottom = 10.dp)
                    .clip(RoundedCornerShape(50)).background(Color.Black.copy(alpha = 0.5f))
                    .padding(horizontal = 8.dp, vertical = 4.dp),
            )
        } else {
            Box(
                Modifier
                    .align(Alignment.TopEnd).padding(8.dp)
                    .size(22.dp).clip(RoundedCornerShape(50)).background(MatchColors.Red),
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Filled.Favorite, null, tint = Color.White, modifier = Modifier.size(12.dp)) }
        }
    }
}
```

## 4. Navigation

Match has minimal chrome: a 4-tab bottom strip and a lightweight top bar (wordmark + Discover/Standouts segmented control + filter). On Android, model the strip as a `NavigationBar`. There is no Material tint pill — active is the brand red itself.

```kotlin
@Composable
fun MatchBottomBar(selected: Int, newLikes: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = MatchColors.DarkCanvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            Triple("Discover", Icons.Filled.Dashboard, 0),
            Triple("Likes",    Icons.Filled.Favorite,  newLikes),
            Triple("Messages", Icons.Filled.ChatBubble, 0),
            Triple("Profile",  Icons.Filled.Person,    0),
        )
        items.forEachIndexed { i, (label, icon, badge) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (badge > 0) BadgedBox(badge = { Badge { Text("$badge") } }) {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(23.dp))
                    } else Icon(icon, contentDescription = label, modifier = Modifier.size(23.dp))
                },
                label = { Text(label, style = MatchText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = MatchColors.RedBright,
                    selectedTextColor = MatchColors.RedBright,
                    unselectedIconColor = MatchColors.DarkTextSecondary,
                    unselectedTextColor = MatchColors.DarkTextSecondary,
                    indicatorColor = Color.Transparent, // Match has no Material pill
                ),
            )
        }
    }
}
```

## 5. Motion

Match motion is physical and playful — the swipe card behaves like a thrown object; the match celebration is the emotional peak.

| Moment | Compose recipe |
|--------|----------------|
| Card swipe | `Modifier.pointerInput` drag → `Modifier.offset` + `graphicsLayer { rotationZ = offsetX / 18 }`; commit past 110.dp → `animateTo` ±2000.dp with `spring(dampingRatio = 0.78f)` |
| Like tap | heart `animateFloatAsState` scale `1f → 1.25f → 1f`, `tween(140)` each leg |
| It's a Match! entrance | overlay `fadeIn(200)`; avatars `slideInHorizontally` from opposite edges with `spring(dampingRatio = 0.7f)`; confetti burst |
| Story-bar tap | instant photo swap via `Crossfade(targetState = photoIndex, animationSpec = tween(120))` |
| Tab switch | active icon `scaleIn` pop `tween(150)`; no shared-axis |
| Likes grid unlock | blurred tiles `Crossfade` to clear `tween(400)`, 40ms stagger per tile via `delayMillis` |
| Boost active | pulsing `infiniteRepeatable` scale `1f ↔ 1.04f`, `tween(1600, easing = EaseInOut)`, reverse |

```kotlin
// Swipe card — the canonical Match motion
val offsetX = remember { Animatable(0f) }
Modifier
    .offset { IntOffset(offsetX.value.roundToInt(), 0) }
    .graphicsLayer { rotationZ = offsetX.value / 18f }
    .pointerInput(Unit) {
        detectHorizontalDragGestures(
            onDragEnd = {
                scope.launch {
                    if (abs(offsetX.value) > 110.dp.toPx()) {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                        offsetX.animateTo(if (offsetX.value > 0) 2000f else -2000f, spring(dampingRatio = 0.78f))
                    } else offsetX.animateTo(0f, spring(dampingRatio = 0.7f))
                }
            },
            onHorizontalDrag = { _, dx -> scope.launch { offsetX.snapTo(offsetX.value + dx) } },
        )
    }
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on swipe-commit and dock tap. For the match success, fire it once on the celebration's `LaunchedEffect`. For the lighter story-bar tick use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`.

## 6. Icons

Match's iconography is simple; the closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Discover (tab) | `square.stack` | `Icons.Filled.Dashboard` |
| Likes (tab) | `heart` / `heart.fill` | `Icons.Filled.Favorite` |
| Messages (tab) | `bubble.left` | `Icons.Filled.ChatBubble` |
| Profile (tab) | `person` / `person.fill` | `Icons.Filled.Person` |
| Like (dock) | `heart.fill` | `Icons.Filled.Favorite` |
| Pass (dock) | `xmark` | `Icons.Filled.Close` |
| Super Like (dock) | `star.fill` | `Icons.Filled.Star` |
| Rewind (dock) | `arrow.uturn.backward` | `Icons.Filled.Replay` |
| Boost (dock) | `bolt.fill` | `Icons.Filled.Bolt` |
| Verified | `checkmark.seal.fill` | `Icons.Filled.Verified` |
| Filter / preferences | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Send message | `paperplane.fill` | `Icons.Filled.Send` |
| Unmatch / report | `flag` | `Icons.Filled.Flag` |
| Premium | `crown.fill` | `Icons.Filled.WorkspacePremium` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Modifier.blur` for the Likes-You paywall requires API 31 — below that, fall back to a dark scrim + lock icon). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the profile photo is full-bleed and extends behind the system bars (light icons over dark photography). Keep the meta block and dock inside `WindowInsets.safeContent`; the dock floats above the navigation bar.
- **Font scaling**: `sp` honors the user's font scale — keep it on profile name, body, section headers, message body. Pin layout-sensitive text (10sp tab labels, 12sp interest chips, story-bar captions, timestamps, the distance line) by deriving from `dp` or fixing `fontScale = 1f` via a local `Density`.
- **TalkBack**: the profile card is one focusable element announcing "{name}, {age}, {job}, {distance}, photo {i} of {n}"; expose previous/next photo as custom actions; give every dock button a `contentDescription` ("Like", "Pass", "Super Like", "Rewind", "Boost"); the "It's a Match!" overlay announces "It's a match with {name}" and is `Modifier.semantics { paneTitle = ... }`.
- **Paywall clarity**: locked Likes-You tiles announce "Someone liked you — subscribe to see" so the blur is screen-reader explicit.
- **Touch targets**: Material guidance is 48.dp — the Like / Pass circles are 56.dp (good); pad the 44–48.dp Super Like / Rewind / Boost to a 48.dp hit area; story-bar tap zones span the full left/right thirds of the card; primary buttons ≥ 48.dp.
- **Contrast**: on-photo text always sits on the bottom gradient and uses white; `#E92434` on white passes WCAG AA for the CTA at 16sp bold; the green presence dot is always paired with the "Active now" text label (never color-only).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, drop the card rotation (straight slide), replace the avatar spring with a `Crossfade`, and disable the Boost pulse — keep the success haptic.
- **Dark mode**: invert via the `Dark*` palette — `#141414`, NOT true black; `#1A1A1A` text becomes `#F2EDED`; Match Red brightens to `#F0203E` for the Like button and active tab so it holds vibrance over dark photography; dock circles add a 1dp `DarkDivider` border as the elevation cue. Do **not** enable Material You `dynamicColorScheme()` — Match's single brand red must hold regardless of wallpaper (there is no second accent to harmonize).
