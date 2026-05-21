# Duolingo (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Duolingo's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Duolingo's snow-white canvas, saturated branded palette, the 3D "slab" button with its collapsing ledge, the curved learning path, the gamified streak/gem/heart HUD, and Duo the owl as emotion carrier) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `FontFamily(Font(R.font.…))` instead of `Font.custom`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` only if you load remote avatars (Duo illustrations ship as bundled assets — no Palette needed).

## 1. Color Tokens

```kotlin
// ui/theme/DuolingoColors.kt
import androidx.compose.ui.graphics.Color

object DuolingoColors {
    // Brand greens — the verb
    val FeatherGreen     = Color(0xFF58CC02) // primary CTA, correct, completed nodes
    val MaskGreen        = Color(0xFF89E219) // progress fills, button top highlight
    val ButtonGreenLedge = Color(0xFF58A700) // the darker 3D ledge

    // Gamification
    val CardinalRed   = Color(0xFFFF4B4B) // wrong, lost heart, destructive
    val CardinalLedge = Color(0xFFE53030)
    val FoxOrange     = Color(0xFFFF9600) // streak flame
    val BeeYellow     = Color(0xFFFFC800) // gems, rewards
    val MacawBlue     = Color(0xFF1CB0F6) // info, links, gem icon
    val BeetlePurple  = Color(0xFFCE82FF) // Super Duolingo (premium)

    // Canvas & surfaces (light)
    val Snow       = Color(0xFFFFFFFF)
    val Polar      = Color(0xFFF7F7F7)
    val Swan       = Color(0xFFE5E5E5) // tile fill, locked node, progress track
    val SwanLedge  = Color(0xFFC4C4C4)

    // Canvas & surfaces (dark — blue-tinted navy, not true black)
    val DarkCanvas   = Color(0xFF131F24)
    val DarkSurface1 = Color(0xFF1F2C34)
    val DarkSurface2 = Color(0xFF37464F)

    // Text
    val Eel  = Color(0xFF4B4B4B) // primary — never pure #000
    val Hare = Color(0xFFAFAFAF) // secondary
    val Wolf = Color(0xFF777777) // tertiary

    // Tile states
    val TileSelectedFill   = Color(0xFFDDF4FF)
    val TileSelectedBorder = Color(0xFF84D8FF)
    val TileCorrectFill    = Color(0xFFD7FFB8)
    val TileWrongFill      = Color(0xFFFFDFE0)
    val GoldLedge          = Color(0xFFE29F03) // gold-crowned node ledge
}
```

Duolingo is snow-white first; wire a `lightColorScheme` with a blue-tinted-navy `darkColorScheme`. The accent palette keeps its light-mode hex on dark — the brand stays bright against the navy.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.runtime.Composable

private val DuolingoLight = lightColorScheme(
    primary        = DuolingoColors.FeatherGreen,
    onPrimary      = DuolingoColors.Snow,
    background     = DuolingoColors.Snow,
    onBackground   = DuolingoColors.Eel,
    surface        = DuolingoColors.Snow,
    onSurface      = DuolingoColors.Eel,
    surfaceVariant = DuolingoColors.Polar,
    outline        = DuolingoColors.Swan,
    error          = DuolingoColors.CardinalRed,
)

private val DuolingoDark = darkColorScheme(
    primary        = DuolingoColors.FeatherGreen, // stays bright on navy
    onPrimary      = DuolingoColors.Snow,
    background     = DuolingoColors.DarkCanvas,
    onBackground   = DuolingoColors.Snow,
    surface        = DuolingoColors.DarkSurface1,
    onSurface      = DuolingoColors.Snow,
    surfaceVariant = DuolingoColors.DarkSurface2,
    outline        = DuolingoColors.DarkSurface2,
    error          = DuolingoColors.CardinalRed,
)

@Composable
fun DuolingoTheme(useDark: Boolean = false, content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (useDark) DuolingoDark else DuolingoLight,
        typography  = DuolingoTypography,
        content     = content,
    )
```

## 2. Typography

`Feather Bold` (Johnson Banks / Krista Radoeva, 2019 — headlines + numbers) and `DIN Next Rounded Pro` (Monotype — all UI) are proprietary. Drop the TTFs in `res/font/` (lowercase, snake_case). Fall back to **SF Pro Rounded's Android analog** — the system rounded face (Roboto with `FontVariation`, or bundle a rounded substitute); rounded terminals come closest.

```kotlin
// ui/theme/DuolingoType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

// Feather Bold — emotion: titles, hero numbers, Duo-said-so moments
val Feather = FontFamily(Font(R.font.feather_bold, FontWeight.Bold))

// DIN Next Rounded Pro — everything else: buttons, tiles, labels
val DinRounded = FontFamily(
    Font(R.font.din_next_rounded_pro_regular, FontWeight.Normal),
    Font(R.font.din_next_rounded_pro_medium,  FontWeight.Medium),
    Font(R.font.din_next_rounded_pro_bold,    FontWeight.Bold),
    Font(R.font.din_next_rounded_pro_black,   FontWeight.Black), // the "800" button voice
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object DuolingoText {
    val Wordmark      = TextStyle(Feather,    fontWeight = FontWeight.Bold,  fontSize = 34.sp, lineHeight = 34.sp, letterSpacing = (-0.5).sp)
    val HeroNumber    = TextStyle(Feather,    fontWeight = FontWeight.Bold,  fontSize = 56.sp, lineHeight = 56.sp, letterSpacing = (-1.0).sp)
    val ScreenTitle   = TextStyle(Feather,    fontWeight = FontWeight.Bold,  fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val Question      = TextStyle(Feather,    fontWeight = FontWeight.Bold,  fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val HudNumber     = TextStyle(Feather,    fontWeight = FontWeight.Bold,  fontSize = 18.sp, lineHeight = 18.sp, letterSpacing = (-0.1).sp)
    val SectionHeader = TextStyle(DinRounded, fontWeight = FontWeight.Black, fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val SkillLabel    = TextStyle(DinRounded, fontWeight = FontWeight.Black, fontSize = 16.sp, lineHeight = 19.sp)
    val Button        = TextStyle(DinRounded, fontWeight = FontWeight.Black, fontSize = 17.sp, lineHeight = 17.sp, letterSpacing = 0.4.sp)
    val Tile          = TextStyle(DinRounded, fontWeight = FontWeight.Bold,  fontSize = 17.sp, lineHeight = 20.sp)
    val Body          = TextStyle(DinRounded, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 22.sp)
    val Helper        = TextStyle(DinRounded, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 18.sp)
    val Meta          = TextStyle(DinRounded, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 16.sp)
    val Tab           = TextStyle(DinRounded, fontWeight = FontWeight.Bold,  fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
    val LabelUpper    = TextStyle(DinRounded, fontWeight = FontWeight.Black, fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 1.0.sp)
}

val DuolingoTypography = Typography(
    displayLarge  = DuolingoText.HeroNumber,
    headlineLarge = DuolingoText.ScreenTitle,
    titleLarge    = DuolingoText.SectionHeader,
    titleMedium   = DuolingoText.SkillLabel,
    bodyMedium    = DuolingoText.Body,
    labelLarge    = DuolingoText.Button,
    labelSmall    = DuolingoText.Tab,
)
```

## 3. Signature Components

### The 3D Primary Button (the Green Slab)

The single most recognizable Duolingo component: a chunky button on a 4dp darker-tone ledge. On press, the top face translates down 4dp to meet the ledge — the ledge "disappears." Mimics a stiff arcade button.

```kotlin
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.animation.core.spring
import androidx.compose.animation.core.tween
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.clickable
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

enum class DuoButtonVariant { Primary, Neutral, Destructive }

@Composable
fun DuoPrimaryButton(
    title: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    variant: DuoButtonVariant = DuoButtonVariant.Primary,
) {
    val (top, ledge, text) = when (variant) {
        DuoButtonVariant.Primary     -> Triple(DuolingoColors.FeatherGreen, DuolingoColors.ButtonGreenLedge, Color.White)
        DuoButtonVariant.Neutral     -> Triple(DuolingoColors.Snow, DuolingoColors.Swan, DuolingoColors.Eel)
        DuoButtonVariant.Destructive -> Triple(DuolingoColors.CardinalRed, DuolingoColors.CardinalLedge, Color.White)
    }
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val faceOffset by animateDpAsState(
        targetValue = if (pressed) 4.dp else 0.dp,
        animationSpec = if (pressed) tween(80) else spring(dampingRatio = 0.7f, stiffness = 500f),
        label = "slab",
    )
    val haptics = LocalHapticFeedback.current

    Box(modifier.height(60.dp)) { // 56dp face + 4dp ledge reveal
        Box(
            Modifier
                .fillMaxWidth()
                .height(56.dp)
                .align(Alignment.BottomCenter)
                .clip(RoundedCornerShape(16.dp))
                .background(ledge),
        )
        Box(
            Modifier
                .fillMaxWidth()
                .height(56.dp)
                .offset(y = faceOffset)
                .clip(RoundedCornerShape(16.dp))
                .background(top)
                .then(if (variant == DuoButtonVariant.Neutral) Modifier.border(2.dp, DuolingoColors.Swan, RoundedCornerShape(16.dp)) else Modifier)
                .clickable(interaction, indication = null) {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS soft impact
                    onClick()
                },
            contentAlignment = Alignment.Center,
        ) {
            Text(title.uppercase(), style = DuolingoText.Button, color = text)
        }
    }
}
```

### Skill Node (Path Circle)

```kotlin
import androidx.compose.animation.core.FastOutSlowInEasing
import androidx.compose.animation.core.RepeatMode
import androidx.compose.animation.core.animateFloat
import androidx.compose.animation.core.infiniteRepeatable
import androidx.compose.animation.core.rememberInfiniteTransition
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Lock
import androidx.compose.material.icons.filled.Star
import androidx.compose.material.icons.filled.WorkspacePremium
import androidx.compose.material3.Icon
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.unit.dp

enum class SkillState { Locked, Available, Current, Completed, Gold, Legendary }

@Composable
fun SkillNode(
    icon: ImageVector,
    state: SkillState,
    crownCount: Int,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val size = if (state == SkillState.Current) 96.dp else 80.dp
    val top = when (state) {
        SkillState.Locked -> DuolingoColors.Swan
        SkillState.Gold -> DuolingoColors.BeeYellow
        SkillState.Legendary -> DuolingoColors.BeetlePurple
        else -> DuolingoColors.FeatherGreen
    }
    val ledge = when (state) {
        SkillState.Locked -> DuolingoColors.SwanLedge
        SkillState.Gold -> DuolingoColors.GoldLedge
        SkillState.Legendary -> Color(0xFFA05BD6)
        else -> DuolingoColors.ButtonGreenLedge
    }
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val pressScale by androidx.compose.animation.core.animateFloatAsState(if (pressed) 0.96f else 1f, spring(dampingRatio = 0.7f), label = "node")
    val haptics = LocalHapticFeedback.current

    // current node pulse ring
    val ring = if (state == SkillState.Current) {
        rememberInfiniteTransition(label = "pulse").animateFloat(
            1f, 1.12f, infiniteRepeatable(tween(900, easing = FastOutSlowInEasing), RepeatMode.Reverse), label = "ring",
        ).value
    } else 1f

    Box(modifier.size(size + 4.dp), contentAlignment = Alignment.Center) {
        if (state == SkillState.Current) {
            Box(Modifier.size(size).scale(ring).clip(CircleShape).border(4.dp, DuolingoColors.MaskGreen, CircleShape))
        }
        Box(Modifier.size(size).offset(y = 4.dp).clip(CircleShape).background(ledge))
        Box(
            Modifier
                .size(size)
                .scale(pressScale)
                .clip(CircleShape)
                .background(top)
                .clickable(interaction, indication = null) {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                    onClick()
                },
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                if (state == SkillState.Locked) Icons.Filled.Lock else icon,
                contentDescription = null,
                tint = if (state == SkillState.Locked) DuolingoColors.Hare else Color.White,
                modifier = Modifier.size(size * 0.4f),
            )
        }
        if (crownCount > 0 && state != SkillState.Locked) {
            Row(
                Modifier
                    .align(Alignment.TopCenter)
                    .offset(y = (-14).dp)
                    .clip(RoundedCornerShape(50))
                    .background(DuolingoColors.Eel)
                    .padding(horizontal = 8.dp, vertical = 3.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(2.dp),
            ) {
                Icon(Icons.Filled.WorkspacePremium, null, tint = DuolingoColors.BeeYellow, modifier = Modifier.size(12.dp))
                Text("$crownCount", style = DuolingoText.Meta, color = Color.White)
            }
        }
    }
}
```

### Answer Tile

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.ui.draw.absoluteOffset
import kotlinx.coroutines.launch

enum class TileState { Idle, Selected, Correct, Wrong }

@Composable
fun AnswerTile(word: String, state: TileState, onTap: () -> Unit, modifier: Modifier = Modifier) {
    val fill = when (state) {
        TileState.Idle -> DuolingoColors.Snow
        TileState.Selected -> DuolingoColors.TileSelectedFill
        TileState.Correct -> DuolingoColors.TileCorrectFill
        TileState.Wrong -> DuolingoColors.TileWrongFill
    }
    val border = when (state) {
        TileState.Idle -> DuolingoColors.Swan
        TileState.Selected -> DuolingoColors.TileSelectedBorder
        TileState.Correct -> DuolingoColors.FeatherGreen
        TileState.Wrong -> DuolingoColors.CardinalRed
    }
    val textColor = when (state) {
        TileState.Idle, TileState.Selected -> DuolingoColors.Eel
        TileState.Correct -> DuolingoColors.ButtonGreenLedge
        TileState.Wrong -> DuolingoColors.CardinalRed
    }
    val shake = remember { Animatable(0f) }
    val scope = rememberCoroutineScope()
    val haptics = LocalHapticFeedback.current

    LaunchedEffect(state) {
        when (state) {
            TileState.Wrong -> {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS warning
                scope.launch {
                    repeat(4) { shake.animateTo(if (it % 2 == 0) 6f else -6f, tween(50)) }
                    shake.animateTo(0f, tween(50))
                }
            }
            TileState.Correct -> haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS success
            else -> {}
        }
    }

    Box(modifier.absoluteOffset(x = shake.value.dp).height(52.dp)) {
        Box(Modifier.fillMaxWidth().height(48.dp).align(Alignment.BottomCenter).clip(RoundedCornerShape(12.dp)).background(if (state == TileState.Idle) DuolingoColors.Swan else border))
        Box(
            Modifier
                .fillMaxWidth()
                .height(48.dp)
                .clip(RoundedCornerShape(12.dp))
                .background(fill)
                .border(2.dp, border, RoundedCornerShape(12.dp))
                .clickable(onClick = onTap)
                .padding(horizontal = 20.dp),
            contentAlignment = Alignment.Center,
        ) {
            Text(word, style = DuolingoText.Tile, color = textColor)
        }
    }
}
```

### HUD Chip (Streak / Gems / Hearts)

```kotlin
import androidx.compose.material.icons.filled.Diamond
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.LocalFireDepartment

enum class HudKind { Streak, Gems, Hearts }

@Composable
fun DuoHudChip(kind: HudKind, value: Int, modifier: Modifier = Modifier) {
    val (icon, tint) = when (kind) {
        HudKind.Streak -> Icons.Filled.LocalFireDepartment to DuolingoColors.FoxOrange
        HudKind.Gems -> Icons.Filled.Diamond to DuolingoColors.MacawBlue
        HudKind.Hearts -> Icons.Filled.Favorite to DuolingoColors.CardinalRed
    }
    Row(
        modifier.defaultMinSize(minHeight = 32.dp).padding(horizontal = 10.dp, vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(icon, contentDescription = kind.name, tint = tint, modifier = Modifier.size(20.dp))
        Text("$value", style = DuolingoText.HudNumber, color = DuolingoColors.Eel)
    }
}
```

### Celebration Takeover

```kotlin
@Composable
fun DuoCelebration(xpEarned: Int, onContinue: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(Unit) { haptics.performHapticFeedback(HapticFeedbackType.LongPress) } // ~iOS .success

    Box(modifier.fillMaxSize().background(DuolingoColors.FeatherGreen)) {
        Column(
            Modifier.fillMaxSize().padding(top = 80.dp, bottom = 16.dp).padding(horizontal = 16.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(24.dp),
        ) {
            // Image(painterResource(R.drawable.duo_cheering), …) — bundled illustration, ~280dp
            Box(Modifier.size(280.dp)) // placeholder slot for the Duo asset
            Text("LESSON COMPLETE!", style = DuolingoText.ScreenTitle, color = Color.White)
            Text(
                "+$xpEarned XP",
                style = DuolingoText.Question,
                color = Color.White,
                modifier = Modifier
                    .clip(RoundedCornerShape(50))
                    .background(DuolingoColors.BeeYellow.copy(alpha = 0.25f))
                    .border(2.dp, DuolingoColors.BeeYellow, RoundedCornerShape(50))
                    .padding(horizontal = 20.dp, vertical = 8.dp),
            )
            Spacer(Modifier.weight(1f))
            DuoPrimaryButton("Continue", onContinue, variant = DuoButtonVariant.Neutral)
        }
        ConfettiLayer() // §6
    }
}
```

## 4. The Learning Path (the signature dynamic)

Duolingo has no color extraction — its distinctive dynamic system is the **curved vertical learning path** (a left-right ±60dp wave of skill nodes, never a straight list) plus the **mascot/streak gamification** layered on top. Build the wave by offsetting each node by a `sin` phase inside a `LazyColumn`.

```kotlin
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.itemsIndexed
import kotlin.math.sin

data class PathNode(val id: String, val icon: ImageVector, val title: String, val state: SkillState, val crowns: Int)

@Composable
fun LearningPath(nodes: List<PathNode>, onNodeTap: (PathNode) -> Unit, modifier: Modifier = Modifier) {
    LazyColumn(
        modifier.fillMaxSize().background(DuolingoColors.Snow),
        contentPadding = PaddingValues(vertical = 32.dp, horizontal = 16.dp),
        verticalArrangement = Arrangement.spacedBy(32.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        itemsIndexed(nodes, key = { _, n -> n.id }) { index, node ->
            // Alternating ±60dp wave — the recognizable Duolingo signature
            val waveOffset = (sin(index * Math.PI / 3) * 60).dp
            Column(
                Modifier.offset(x = waveOffset),
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.spacedBy(8.dp),
            ) {
                SkillNode(node.icon, node.state, node.crowns, { onNodeTap(node) })
                Text(node.title, style = DuolingoText.SkillLabel, color = DuolingoColors.Eel)
            }
        }
    }
}
```

The **streak flame** is the heart of the habit loop — idle gentle pulse, big confetti burst on a save-moment:

```kotlin
@Composable
fun StreakFlame(count: Int, modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "flame")
    val scale by t.animateFloat(
        1f, 1.05f, infiniteRepeatable(tween(2000, easing = FastOutSlowInEasing), RepeatMode.Reverse), label = "idle",
    )
    Row(modifier, verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
        Icon(
            Icons.Filled.LocalFireDepartment, "Streak",
            tint = DuolingoColors.FoxOrange,
            modifier = Modifier.size(20.dp).scale(scale),
        )
        Text("$count", style = DuolingoText.HudNumber, color = DuolingoColors.FoxOrange)
    }
}
```

## 5. Navigation

Duolingo's root is a **5-tab bottom bar** (Learn / Leaderboard / Quests / Shop / Profile). The current iOS app is icon-only by default with a soft green tint pill behind the active tab. Active tint is Feather Green. Android has no live blur — the bar is an opaque snow surface with a hairline top divider.

```kotlin
import androidx.compose.material.icons.filled.EmojiEvents
import androidx.compose.material.icons.filled.Home
import androidx.compose.material.icons.filled.Person
import androidx.compose.material.icons.filled.ShoppingCart
import androidx.compose.material.icons.filled.Description
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun DuolingoBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = DuolingoColors.Snow, tonalElevation = 0.dp) {
        val items = listOf(
            "Learn" to Icons.Filled.Home,
            "Leaderboard" to Icons.Filled.EmojiEvents,
            "Quests" to Icons.Filled.Description,
            "Shop" to Icons.Filled.ShoppingCart,
            "Profile" to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(28.dp)) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = DuolingoColors.FeatherGreen,
                    unselectedIconColor = DuolingoColors.Hare,
                    indicatorColor = Color(0xFFE5F8D5), // soft green tint pill behind active tab
                ),
            )
        }
    }
}
```

## 6. Motion & Effects

| Moment | Compose recipe |
|--------|----------------|
| Button press | top face `animateDpAsState` 0 → 4dp over 80ms `tween`; release `spring(dampingRatio = 0.7f)`; `HapticFeedbackType.TextHandleMove` (soft) |
| Correct answer | tile → green + checkmark `scale` 0 → 1.2 → 1.0 spring (300ms); success haptic + chime |
| Wrong answer | tile → red + horizontal `Animatable` shake ±6dp for ~400ms; warning haptic + buzz |
| Skill node tap | `scale` 0.96 → 1.0 spring (≈180ms); light haptic; if unlocking, play celebration |
| Level complete | crossfade to green canvas 300ms, Duo slides up `spring` 500ms, confetti 1.2s, success haptic |
| Streak flame | idle infinite `scale` 1.0 ↔ 1.05 over 2s; on save: `Animatable` burst 1.3 → 1.0 + confetti |
| XP ring fill | `Animatable` stroke sweep 0 → target over 800ms `ease-out`, haptic each quarter |

### Confetti Layer

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.drawscope.rotate
import kotlin.random.Random

@Composable
fun ConfettiLayer(modifier: Modifier = Modifier) {
    data class Piece(val x: Float, val color: Color, val delay: Int, val spin: Float)
    val palette = listOf(
        DuolingoColors.FeatherGreen, DuolingoColors.FoxOrange, DuolingoColors.BeeYellow,
        DuolingoColors.MacawBlue, DuolingoColors.CardinalRed, DuolingoColors.BeetlePurple,
    )
    val pieces = remember {
        List(12) { Piece(Random.nextFloat(), palette.random(), Random.nextInt(0, 300), Random.nextFloat() * 720f - 360f) }
    }
    val progress = remember { Animatable(0f) }
    LaunchedEffect(Unit) { progress.animateTo(1f, tween(1200)) }

    Canvas(modifier.fillMaxSize()) {
        pieces.forEach { p ->
            val t = ((progress.value * 1200f - p.delay) / 1200f).coerceIn(0f, 1f)
            val y = t * (size.height + 40f) - 20f
            rotate(p.spin * t, pivot = Offset(p.x * size.width, y)) {
                drawRoundRect(
                    color = p.color,
                    topLeft = Offset(p.x * size.width - 4f, y),
                    size = Size(8.dp.toPx(), 14.dp.toPx()),
                    cornerRadius = androidx.compose.ui.geometry.CornerRadius(2.dp.toPx()),
                )
            }
        }
    }
}
```

### Haptics Summary

```kotlin
// Correct answer  → HapticFeedbackType.LongPress       (~iOS .success)  + chime SFX
// Wrong answer    → HapticFeedbackType.LongPress       (~iOS .warning)  + buzz SFX
// Button press    → HapticFeedbackType.TextHandleMove  (~iOS .soft impact)
// Celebration     → HapticFeedbackType.LongPress       (~iOS .success)  + cheer SFX
// Node unlock     → HapticFeedbackType.LongPress       (~iOS .medium impact)
```

Pair every haptic with an SFX asset via `MediaPlayer`/`SoundPool` — the game feel needs both channels. For finer impact control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+) or a `Vibrator` `VibrationEffect`.

## 7. Icons

Duolingo ships proprietary glyphs (the Duo wordmark, league badges, Duo the owl). Use `androidx.compose.material:material-icons-extended` for the closest semantic match; bundle Duo illustrations + league badges as vector drawables / image assets loaded via `painterResource(R.drawable.…)`.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Streak flame | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Gems | `diamond.fill` | `Icons.Filled.Diamond` |
| Hearts | `heart.fill` | `Icons.Filled.Favorite` |
| Locked skill | `lock.fill` | `Icons.Filled.Lock` |
| Completed skill | `star.fill` / `checkmark` | `Icons.Filled.Star` / `Icons.Filled.Check` |
| Speaking exercise | `waveform` | `Icons.Filled.GraphicEq` |
| Listening exercise | `ear.fill` | `Icons.Filled.Hearing` |
| Close lesson | `xmark` | `Icons.Filled.Close` |
| Continue | `arrow.right` | `Icons.Filled.ArrowForward` |
| Learn tab | `house.fill` | `Icons.Filled.Home` |
| Leaderboard tab | `trophy.fill` | `Icons.Filled.EmojiEvents` |
| Quests tab | `scroll.fill` | `Icons.Filled.Description` |
| Shop tab | `cart.fill` | `Icons.Filled.ShoppingCart` |
| Profile tab | `person.fill` | `Icons.Filled.Person` |
| Crown (mastery) | `crown.fill` | `Icons.Filled.WorkspacePremium` |
| Duo the owl / league badges | (custom) | Vector drawable / image asset — never substitute |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The snow canvas wants dark-content system bars (`isAppearanceLightStatusBars = true`); the HUD aligns chips to the trailing edge inside `windowInsetsPadding`, and the CHECK button sits just above the gesture nav.
- **Font scaling**: `sp` honors the user's font scale — scale the question prompt, body, and lesson explanations. Pin layout-sensitive text (uppercase button labels, HUD numbers, tab labels) by deriving from `dp`; skill-node labels scale to XL only. Uppercase button text is tight — pin it.
- **TalkBack**: label answer tiles with full text + state — `Modifier.semantics { contentDescription = "Answer tile: casa, selected" }`. On correct/wrong announce the toast ("Correct! +10 XP"). Skill nodes announce title + state ("Basics 1, completed, 3 crowns").
- **Touch targets**: Material guidance is 48dp minimum. The 56dp primary button and 80dp skill node clear easily; answer tiles are 48dp; icon buttons need 48dp hit area via padding around the 24dp glyph; HUD chips reach 48dp effective tap.
- **Contrast**: Feather Green `#58CC02` on white clears WCAG AA only at 14sp+ bold — always use **white text on green surfaces** for button labels, never green text on white. Eel `#4B4B4B` on white passes AAA. The real low-contrast pair is **Hare `#AFAFAF` on white** — validate at 13sp meta/helper and darken toward Wolf `#777777` if targeting compliance. Never signal correctness by red/green alone — pair with a checkmark/X glyph and the shake/pop animation.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, disable the confetti layer, the flame idle pulse, and the current-node pulse ring; keep the button ledge collapse (it conveys state) but skip the spring overshoot.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Duolingo's deliberately-bright 6-role palette (green/red/orange/yellow/blue/purple) is brand-load-bearing and must not shift with wallpaper. Strong-brand app: keep the curated scheme.
