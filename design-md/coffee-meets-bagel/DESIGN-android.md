# Coffee Meets Bagel (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Coffee Meets Bagel's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the Today's Bagel card + action trio + match takeover, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (CMB's cozy coffee-house palette, the one-at-a-time curated card, the oversized Like, cream-instead-of-white) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush.verticalGradient` for the photo scrim, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for profile photos. No color extraction — CMB's palette is fixed warm tones.

## 1. Color Tokens

```kotlin
// ui/theme/CmbColors.kt
import androidx.compose.ui.graphics.Color

object CmbColors {
    // Primary action (Bagel Orange)
    val Bagel     = Color(0xFFF4623A)
    val BagelDeep = Color(0xFFD94E2A)

    // Secondary brand (Brew Brown)
    val Brew        = Color(0xFFA0522D)
    val BrewDeep    = Color(0xFF7E3F22)
    val BrewPressed = Color(0xFF693318)

    // Cream (replaces white)
    val Cream     = Color(0xFFF3E4CF)
    val CreamSoft = Color(0xFFE8D4B8)

    // Canvas & Surfaces (Light)
    val Canvas    = Color(0xFFFBF6EF) // warm off-white — NOT pure white
    val Surface1L = Color(0xFFFFFFFF)
    val Surface2L = Color(0xFFF3ECE1)
    val DividerL  = Color(0xFFE8DECF)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF14100D) // roasted brown-black — NOT neutral grey
    val DarkSurface1 = Color(0xFF1F1813)
    val DarkSurface2 = Color(0xFF2A2017)
    val DarkDivider  = Color(0xFF33281D)

    // Text
    val TextPrimaryL   = Color(0xFF3A2A18) // warm dark brown — NOT pure black
    val TextSecondaryL = Color(0xFF8A7458)
    val TextPrimaryD   = Color(0xFFEFE6DA)
    val TextSecondaryD = Color(0xFFB7A48E)
    val TextTertiaryD  = Color(0xFF7C6B57)

    // Status
    val MatchGreen  = Color(0xFF4CC38A)
    val PremiumGold = Color(0xFFE0A82E)
}
```

Wire it into both schemes. CMB is warm-first; the dark scheme uses the signature roasted brown-black `#14100D`, never neutral grey or true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val CmbLight = lightColorScheme(
    primary        = CmbColors.Bagel,
    onPrimary      = Color.White,
    secondary      = CmbColors.Brew,
    onSecondary    = CmbColors.Cream,
    background      = CmbColors.Canvas,
    onBackground    = CmbColors.TextPrimaryL,
    surface         = CmbColors.Surface1L,
    onSurface       = CmbColors.TextPrimaryL,
    surfaceVariant  = CmbColors.Surface2L,
    outline         = CmbColors.DividerL,
    error           = CmbColors.BagelDeep,
)

private val CmbDark = darkColorScheme(
    primary        = CmbColors.Bagel,
    onPrimary      = Color.White,
    secondary      = CmbColors.Brew,
    onSecondary    = CmbColors.Cream,
    background      = CmbColors.DarkCanvas,
    onBackground    = CmbColors.TextPrimaryD,
    surface         = CmbColors.DarkSurface1,
    onSurface       = CmbColors.TextPrimaryD,
    surfaceVariant  = CmbColors.DarkSurface2,
    outline         = CmbColors.DarkDivider,
    error           = CmbColors.BagelDeep,
)

@Composable
fun CmbTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) CmbDark else CmbLight,
    typography = CmbTypography,
    content = content,
)
```

## 2. Typography

CMB's brand face is **Brandon Grotesque** / Gotham-family (licensed). Use **Poppins** (SIL OFL — free) as the closest face: drop the TTFs in `res/font/`. People-first hierarchy; the rounded geometric warmth is the tone — never a cold grotesque.

```kotlin
// ui/theme/CmbType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Poppins = FontFamily(
    Font(R.font.poppins_regular,   FontWeight.Normal),
    Font(R.font.poppins_medium,    FontWeight.Medium),
    Font(R.font.poppins_semibold,  FontWeight.SemiBold),
    Font(R.font.poppins_bold,      FontWeight.Bold),
    Font(R.font.poppins_extrabold, FontWeight.ExtraBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object CmbText {
    val ScreenTitle = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val ProfileName = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 18.sp, lineHeight = 23.sp)
    val Body        = TextStyle(Poppins, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val PromptAns   = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 21.sp)
    val Meta        = TextStyle(Poppins, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Chip        = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 16.sp)
    val Badge       = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.4.sp)
    val Number      = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
}

val CmbTypography = Typography(
    headlineLarge  = CmbText.ScreenTitle,
    headlineMedium = CmbText.ProfileName,
    titleMedium    = CmbText.CardTitle,
    bodyMedium     = CmbText.Body,
    labelSmall     = CmbText.Tab,
)
```

## 3. Signature Components

### Today's Bagel Card (the core atom)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Schedule
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun BagelCard(
    imageUrl: String,
    name: String, age: Int, meta: String, badge: String, interests: List<String>,
) {
    Box(
        Modifier
            .fillMaxWidth()
            .aspectRatio(3f / 4f)
            .shadow(40.dp, RoundedCornerShape(24.dp), spotColor = Color.Black.copy(alpha = 0.7f))
            .clip(RoundedCornerShape(24.dp)),
    ) {
        AsyncImage(model = imageUrl, contentDescription = null, modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)

        // Bottom scrim
        Box(
            Modifier
                .align(Alignment.BottomCenter)
                .fillMaxWidth()
                .fillMaxHeight(0.55f)
                .background(Brush.verticalGradient(listOf(Color.Transparent, Color(0xFF14100D).copy(alpha = 0.95f)))),
        )

        // Badge (top-left)
        Row(
            Modifier
                .align(Alignment.TopStart)
                .padding(16.dp)
                .clip(RoundedCornerShape(999.dp))
                .background(Color(0xFF14100D).copy(alpha = 0.55f))
                .padding(horizontal = 12.dp, vertical = 6.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Icon(Icons.Filled.Schedule, contentDescription = null, tint = CmbColors.Cream, modifier = Modifier.size(12.dp))
            Text(badge.uppercase(), style = CmbText.Badge, color = CmbColors.Cream)
        }

        // Info block (bottom-left)
        Column(
            Modifier
                .align(Alignment.BottomStart)
                .padding(20.dp),
        ) {
            Row {
                Text(name, style = CmbText.ProfileName, color = Color.White)
                Text(" $age", style = CmbText.ProfileName.copy(fontWeight = FontWeight.Medium), color = Color.White)
            }
            Text(meta, style = CmbText.Meta, color = CmbColors.Cream, modifier = Modifier.padding(top = 4.dp))
            Row(Modifier.padding(top = 12.dp), horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                interests.forEach { c ->
                    Box(
                        Modifier
                            .clip(RoundedCornerShape(999.dp))
                            .background(CmbColors.Cream.copy(alpha = 0.18f))
                            .padding(horizontal = 12.dp, vertical = 5.dp),
                    ) { Text(c, style = CmbText.Chip, color = CmbColors.Cream) }
                }
            }
        }
    }
}
```

### Action Trio (Pass / Like / Send a Bagel)

```kotlin
import androidx.compose.animation.core.Spring
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.Close
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.Star
import androidx.compose.runtime.*
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun ActionTrio(onPass: () -> Unit, onLike: () -> Unit, onSend: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    var pop by remember { mutableStateOf(false) }
    val heartScale by animateFloatAsState(if (pop) 1.15f else 1f, spring(Spring.DampingRatioMediumBouncy), label = "heart")

    Row(
        Modifier.fillMaxWidth().padding(vertical = 16.dp),
        horizontalArrangement = Arrangement.spacedBy(28.dp, Alignment.CenterHorizontally),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        // Pass — 60dp
        Box(
            Modifier.size(60.dp).clip(CircleShape).background(CmbColors.DarkSurface2)
                .clickable { haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove); onPass() },
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Close, "Pass", tint = CmbColors.TextSecondaryD, modifier = Modifier.size(24.dp)) }

        // Like — 72dp (largest)
        Box(
            Modifier.size(72.dp).clip(CircleShape).background(CmbColors.Bagel)
                .clickable {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                    pop = true; onLike()
                },
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Favorite, "Like", tint = Color.White, modifier = Modifier.size(32.dp).scale(heartScale)) }

        // Send a Bagel — 60dp
        Box(
            Modifier.size(60.dp).clip(CircleShape).background(CmbColors.Brew)
                .clickable { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onSend() },
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Star, "Send a Bagel", tint = CmbColors.Cream, modifier = Modifier.size(20.dp)) }
    }

    LaunchedEffect(pop) { if (pop) { kotlinx.coroutines.delay(140); pop = false } }
}
```

### Curated Batch Header

```kotlin
@Composable
fun BatchHeader(title: String, countText: String, onBeans: () -> Unit) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 20.dp).padding(bottom = 12.dp),
        verticalAlignment = Alignment.Top,
    ) {
        Column(Modifier.weight(1f)) {
            Text(title, style = CmbText.CardTitle.copy(fontSize = 19.sp), color = CmbColors.TextPrimaryD)
            Text(countText, style = CmbText.Badge.copy(fontWeight = FontWeight.SemiBold, letterSpacing = 0.sp), color = CmbColors.TextSecondaryD)
        }
        Box(
            Modifier.size(38.dp).clip(CircleShape).background(CmbColors.DarkSurface2).clickable { onBeans() },
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Spa, contentDescription = "Beans", tint = CmbColors.Cream, modifier = Modifier.size(18.dp)) }
    }
}
```

### Interest Chip

```kotlin
@Composable
fun InterestChip(label: String, selected: Boolean = false, onClick: () -> Unit = {}) {
    Box(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(if (selected) CmbColors.Brew else CmbColors.DarkSurface2)
            .clickable { onClick() }
            .padding(horizontal = 14.dp, vertical = 8.dp),
    ) {
        Text(label, style = CmbText.Chip, color = if (selected) CmbColors.Cream else CmbColors.TextPrimaryD)
    }
}
```

### Match Takeover

```kotlin
import androidx.compose.material.icons.filled.CheckCircle

@Composable
fun MatchTakeover(onSayHi: () -> Unit, onKeep: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    var appear by remember { mutableStateOf(false) }
    val scale by animateFloatAsState(if (appear) 1f else 0.9f, tween(400), label = "match")

    LaunchedEffect(Unit) { haptics.performHapticFeedback(HapticFeedbackType.LongPress); appear = true }

    Box(
        Modifier.fillMaxSize().background(Brush.verticalGradient(listOf(CmbColors.Bagel, CmbColors.Brew))),
        contentAlignment = Alignment.Center,
    ) {
        Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(20.dp), modifier = Modifier.scale(scale)) {
            Text("It's a match!", style = CmbText.ScreenTitle, color = Color.White)
            Icon(Icons.Filled.CheckCircle, contentDescription = null, tint = CmbColors.MatchGreen, modifier = Modifier.size(44.dp))
            Box(
                Modifier.clip(RoundedCornerShape(999.dp)).background(CmbColors.Bagel).clickable { onSayHi() }
                    .padding(horizontal = 40.dp, vertical = 14.dp),
            ) { Text("Say hi", style = CmbText.Button, color = Color.White) }
            Text("Keep browsing", style = CmbText.Chip, color = CmbColors.Cream, modifier = Modifier.clickable { onKeep() })
        }
    }
}
```

## 4. Navigation

CMB has a top bar (title + circular beans/filter button) and a 4-tab bottom strip (Suggested / Discover / Chats / Profile). On Android use a `NavigationBar`. There is no tint pill — active icon fills and the label goes Bagel Orange.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun CmbBottomBar(selected: Int, unread: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = CmbColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Suggested" to Icons.Filled.Lens,        // stand-in for bagel logomark
            "Discover"  to Icons.Filled.Search,
            "Chats"     to Icons.Filled.ChatBubble,
            "Profile"   to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    BadgedBox(badge = { if (i == 2 && unread > 0) Badge(containerColor = CmbColors.Bagel) }) {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp))
                    }
                },
                label = { Text(label, style = CmbText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = CmbColors.Bagel,    // orange, no tint pill
                    selectedTextColor = CmbColors.Bagel,
                    unselectedIconColor = CmbColors.TextTertiaryD,
                    unselectedTextColor = CmbColors.TextTertiaryD,
                    indicatorColor = Color.Transparent,     // CMB has no Material pill
                ),
            )
        }
    }
}
```

The bagel card is shown one at a time (preload the next behind it for the cross-fade); the curated batch is finite, never an infinite `LazyColumn`. The profile detail is a separate scrollable screen reached via a shared-element transition on the hero photo.

## 5. Motion

CMB motion is warm and gentle — 180–400ms ease-out with soft haptics; shadows are brown-warm like café lamplight.

| Moment | Compose recipe |
|--------|----------------|
| Like heart pop | `animateFloatAsState` 1.0 → 1.15 → 1.0 `spring(DampingRatioMediumBouncy)` + soft haptic |
| Just-liked card wash | orange overlay `animateColorAsState` alpha 0.12 → 0 over 500ms |
| Card advance (like) | liked card `animateOffsetAsState` up-right + `rotate(12f)` `tween(320)`; next card `Crossfade` + scale 0.96 → 1.0 `tween(260)` |
| Pass | passed card slides down-left `tween(280)`; light tick haptic |
| Match takeover | gradient `scale(0.9 → 1.0)` + fade `tween(400)`; bagel-glyph confetti `tween(1200)`; medium haptic |
| Chip select | background `animateColorAsState` `Surface2` → `Brew` `tween(180)`; scale 0.95 tap-down |
| Tab switch | `Crossfade(tween(200))`; active icon gentle 1.0→1.1→1.0 pop |
| Profile open | shared-element hero photo + `AnimatedContent` slide push `tween(300)` |

```kotlin
// Like heart pop — the canonical CMB motion
val heartScale by animateFloatAsState(
    targetValue = if (pop) 1.15f else 1f,
    animationSpec = spring(dampingRatio = Spring.DampingRatioMediumBouncy, stiffness = Spring.StiffnessMedium),
    label = "heartPop",
)
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft success on Like / Send / match takeover, and `HapticFeedbackType.TextHandleMove` (or `HapticFeedbackConstants.CLOCK_TICK` via `LocalView`) for the light tick on Pass and chip select.

## 6. Icons

CMB's bagel logomark is a brand mark — render it as a bundled `ImageVector` / `VectorDrawable` (a ring) for fidelity. The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Suggested (tab) | bagel logomark | bundled vector (stand-in `Icons.Filled.Lens`) |
| Discover (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Chats (tab) | `bubble.left.fill` | `Icons.Filled.ChatBubble` |
| Profile (tab) | `person.fill` | `Icons.Filled.Person` |
| Pass | `xmark` | `Icons.Filled.Close` |
| Like | `heart.fill` | `Icons.Filled.Favorite` |
| Send a Bagel | `star.fill` | `Icons.Filled.Star` |
| Beans / currency | `leaf.fill` | `Icons.Filled.Spa` (stand-in for coffee bean) |
| Active badge | `clock.fill` | `Icons.Filled.Schedule` |
| Match check | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Send message | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Report / overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Premium / Boost | `bolt.fill` | `Icons.Filled.Bolt` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + shared-element comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the warm-charcoal dark canvas wants light-content system bars (dark-content on the warm light canvas). The action trio + tab bar need `navigationBarsPadding()`; the chat composer needs `imePadding()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on profile name, section, body, prompt answer. Pin layout-sensitive text (chips, badges, bean numbers, tab labels) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Brand font**: use Poppins (SIL OFL — free) as the closest face; do not redistribute Brandon Grotesque (CMB's licensed brand grotesque). Never substitute a cold grotesque — the rounded warmth is the tone.
- **TalkBack**: the bagel card is one node ("Profile: {name}, {age}, {meta}, {interests}"); the action buttons read "Like {name}" / "Pass on {name}" / "Send a Bagel to {name}"; expose `customActions` (Like, Pass) on the card so users need not reach the buttons.
- **Touch targets**: Like is 72.dp, Pass/Send 60.dp (all exceed the 48.dp guidance); chips ≥ 32.dp tall, full-chip tappable; tab icons 22.dp glyph with 48.dp hit via the NavigationBar.
- **Color is not the only signal**: Like is also the *largest* control and uses a filled heart; Pass uses ×; Send uses ★ — shape distinguishes them for color-blind users.
- **Contrast**: white/cream over the photo scrim passes WCAG AA at rendered sizes; `#3A2A18` on `#FBF6EF` and `#EFE6DA` on `#14100D` pass AA for 16sp body; white on `#F4623A` passes AA for button text.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the card fly-out with a `Crossfade`, snap the heart (no pop), keep the orange like-flash (conveys state) shortened to 250ms; the match takeover fades instead of scaling.
- **Dark mode**: invert via the `Dark*` palette — roasted brown-black `#14100D`, NOT neutral grey or true black; text becomes warm cream `#EFE6DA`. Do **not** enable Material You `dynamicColorScheme()` — CMB's coffee-house identity must hold regardless of wallpaper.
