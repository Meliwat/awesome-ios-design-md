# Calm (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Calm's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Calm's night-sky gradient, glass surfaces, serif/sans pairing, the breathe bubble) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush.verticalGradient` instead of CAGradientLayer, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for imagery, and Lora + Inter `FontFamily`s in `res/font/`.

## 1. Color Tokens

```kotlin
// ui/theme/CalmColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object CalmColors {
    // Night-sky gradient
    val GradientTop    = Color(0xFF2A6FD6)
    val GradientMid    = Color(0xFF1A4A8F)
    val GradientBottom = Color(0xFF0B1E3F)
    val ScrimNavy      = Color(0xFF091830)

    // Glass surfaces
    val Glass        = Color.White.copy(alpha = 0.08f)
    val GlassRaised  = Color.White.copy(alpha = 0.12f)
    val GlassDivider = Color.White.copy(alpha = 0.12f)
    val GlassHover   = Color.White.copy(alpha = 0.16f)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFB8C4D8)
    val TextTertiary  = Color(0xFF8794A8)

    // Accent
    val CalmBlue        = Color(0xFF2A6FD6)
    val CalmBluePressed = Color(0xFF1F57AB)
    val CalmBlueSoft    = Color(0xFF4A85E0)
    val StreakGold      = Color(0xFFF2C94C)
    val GentleError     = Color(0xFFE08A8A)
    val Success         = Color(0xFF7FB8A0)
}

val CalmSky = Brush.verticalGradient(
    0.0f  to CalmColors.GradientTop,
    0.45f to CalmColors.GradientMid,
    1.0f  to CalmColors.GradientBottom,
)
```

Wire it into a Material 3 `darkColorScheme`. Calm is dark-only; do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val CalmScheme = darkColorScheme(
    primary        = CalmColors.CalmBlue,
    onPrimary      = CalmColors.TextPrimary,
    background     = CalmColors.GradientBottom,
    onBackground   = CalmColors.TextPrimary,
    surface        = CalmColors.GradientMid,
    onSurface      = CalmColors.TextPrimary,
    surfaceVariant = CalmColors.GlassRaised,
    outline        = CalmColors.GlassDivider,
    error          = CalmColors.GentleError,
)

@Composable
fun CalmTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = CalmScheme, typography = CalmTypography, content = content)
```

## 2. Typography

Lora (serif headlines) and Inter (sans body) are both open-source (SIL OFL). Drop the TTFs in `res/font/` (lowercase, snake_case) and build two `FontFamily`s.

```kotlin
// ui/theme/CalmType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Lora = FontFamily(
    Font(R.font.lora_regular,  FontWeight.Normal),   // 400
    Font(R.font.lora_semibold, FontWeight.SemiBold), // 600
)
val Inter = FontFamily(
    Font(R.font.inter_light,    FontWeight.Light),    // 300
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_medium,   FontWeight.Medium),   // 500
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object CalmText {
    // Lora — serif, the brand voice
    val Greeting    = TextStyle(Lora, fontWeight = FontWeight.SemiBold, fontSize = 30.sp, lineHeight = 38.sp, letterSpacing = (-0.2).sp)
    val ScreenTitle = TextStyle(Lora, fontWeight = FontWeight.SemiBold, fontSize = 26.sp, lineHeight = 34.sp, letterSpacing = (-0.2).sp)
    val DailyTitle  = TextStyle(Lora, fontWeight = FontWeight.SemiBold, fontSize = 24.sp, lineHeight = 31.sp, letterSpacing = (-0.1).sp)
    val CardTitle   = TextStyle(Lora, fontWeight = FontWeight.SemiBold, fontSize = 19.sp, lineHeight = 26.sp)
    val BreatheCue  = TextStyle(Lora, fontWeight = FontWeight.Normal,   fontSize = 22.sp, letterSpacing = 0.5.sp)

    // Inter — sans, supporting
    val Section   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 16.sp, letterSpacing = 0.2.sp)
    val RowTitle  = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 23.sp)
    val Body      = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 26.sp)
    val Subtitle  = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val Button    = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, letterSpacing = 0.1.sp)
    val Meta      = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 13.sp, letterSpacing = 0.2.sp)
    val Tab       = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, letterSpacing = 0.2.sp)
    val Timer     = TextStyle(Inter, fontWeight = FontWeight.Light,    fontSize = 44.sp)
    val LabelUpper = TextStyle(Inter, fontWeight = FontWeight.Bold,    fontSize = 12.sp, letterSpacing = 1.0.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val CalmTypography = Typography(
    headlineLarge = CalmText.Greeting,
    headlineSmall = CalmText.ScreenTitle,
    titleMedium   = CalmText.RowTitle,
    bodyLarge     = CalmText.Body,
    labelSmall    = CalmText.Tab,
)
```

## 3. Signature Components

### Night-Sky Background

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier

@Composable
fun CalmBackground(content: @Composable BoxScope.() -> Unit) {
    Box(Modifier.fillMaxSize().background(CalmSky), content = content)
}
```

### Breathe Bubble (signature)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import kotlinx.coroutines.delay

@Composable
fun BreatheBubble(modifier: Modifier = Modifier) {
    val anim = remember { Animatable(120f) }
    var cue by remember { mutableStateOf("Breathe in") }

    LaunchedEffect(Unit) {
        while (true) {
            cue = "Breathe in"
            anim.animateTo(220f, tween(4000, easing = EaseIn))   // inhale
            cue = "Hold"
            delay(7000)                                          // hold
            cue = "Breathe out"
            anim.animateTo(120f, tween(8000, easing = EaseOut))  // exhale
        }
    }

    Box(modifier, contentAlignment = Alignment.Center) {
        Box(
            Modifier
                .size(anim.value.dp)
                .shadow(40.dp, CircleShape, spotColor = CalmColors.CalmBlueSoft)
                .background(Color.White.copy(alpha = 0.9f), CircleShape),
            contentAlignment = Alignment.Center,
        ) {
            Text(cue, style = CalmText.BreatheCue, color = CalmColors.GradientBottom)
        }
    }
}
```

### Primary Play Pill

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.Icon
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun CalmPlayPill(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f,
        tween(250, easing = EaseOut), label = "pillScale")
    val haptics = LocalHapticFeedback.current

    Row(
        modifier
            .scale(scale)
            .clip(CircleShape)
            .background(if (pressed) CalmColors.CalmBluePressed else CalmColors.CalmBlue)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS soft
                onClick()
            }
            .padding(horizontal = 40.dp, vertical = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(Icons.Filled.PlayArrow, null, tint = Color.White, modifier = Modifier.size(16.dp))
        Text(title, style = CalmText.Button, color = CalmColors.TextPrimary)
    }
}
```

### Daily Calm Hero Card (signature)

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.graphics.Brush

@Composable
fun DailyCalmHero(
    title: String, subtitle: String, photoUrl: String, onPlay: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Box(
        modifier
            .fillMaxWidth()
            .height(220.dp)
            .clip(RoundedCornerShape(20.dp)),
    ) {
        AsyncImage(model = photoUrl, contentDescription = null,
            contentScale = ContentScale.Crop, modifier = Modifier.fillMaxSize())
        Box(
            Modifier.fillMaxSize().background(
                Brush.verticalGradient(
                    0.4f to Color.Transparent,
                    1f   to CalmColors.ScrimNavy.copy(alpha = 0.85f),
                )
            )
        )
        Row(
            Modifier.align(Alignment.BottomStart).padding(20.dp).fillMaxWidth(),
            verticalAlignment = Alignment.Bottom,
        ) {
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(6.dp)) {
                Text("DAILY CALM", style = CalmText.LabelUpper, color = CalmColors.TextPrimary)
                Text(title, style = CalmText.DailyTitle, color = CalmColors.TextPrimary)
                Text(subtitle, style = CalmText.Subtitle, color = CalmColors.TextSecondary)
            }
            Box(
                Modifier.size(44.dp).clip(CircleShape)
                    .background(CalmColors.GlassHover)
                    .clickable(onClick = onPlay),
                contentAlignment = Alignment.Center,
            ) {
                Icon(Icons.Filled.PlayArrow, "Play",
                    tint = Color.White, modifier = Modifier.size(18.dp))
            }
        }
    }
}
```

### Sleep Story List Row

```kotlin
import androidx.compose.material.icons.outlined.Download

@Composable
fun SleepStoryRow(
    title: String, narrator: String, duration: String, thumbUrl: String,
    onClick: () -> Unit, modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Column {
        Row(
            modifier
                .fillMaxWidth()
                .height(76.dp)
                .background(if (pressed) CalmColors.GlassRaised else Color.Transparent)
                .clickable(interaction, indication = null, onClick = onClick)
                .padding(horizontal = 20.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(14.dp),
        ) {
            AsyncImage(model = thumbUrl, contentDescription = null,
                contentScale = ContentScale.Crop,
                modifier = Modifier.size(56.dp).clip(RoundedCornerShape(12.dp)))
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(title, style = CalmText.RowTitle, color = CalmColors.TextPrimary, maxLines = 1)
                Text("Narrated by $narrator · $duration",
                    style = CalmText.Subtitle, color = CalmColors.TextSecondary, maxLines = 1)
            }
            Icon(Icons.Outlined.Download, "Download",
                tint = Color.White.copy(alpha = 0.8f), modifier = Modifier.size(22.dp))
        }
        HorizontalDivider(color = CalmColors.GlassDivider, thickness = 1.dp)
    }
}
```

### Glass Card

```kotlin
@Composable
fun CalmGlassCard(modifier: Modifier = Modifier, content: @Composable ColumnScope.() -> Unit) {
    Column(
        modifier
            .clip(RoundedCornerShape(20.dp))
            .background(CalmColors.Glass) // Android has no live blur; value-only glass
            .border(1.dp, CalmColors.GlassDivider, RoundedCornerShape(20.dp))
            .padding(20.dp),
        content = content,
    )
}
```

## 4. Bottom Navigation (Tab Bar)

Material 3 `NavigationBar`. Calm's iOS tab bar is `.regularMaterial` glass over the deep navy; Android has no first-class live blur, so use an 88%-opaque navy surface. **Active tint is Calm Blue.**

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun CalmBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = CalmColors.GradientBottom.copy(alpha = 0.88f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Sleep"    to Icons.Filled.Bedtime,
            "Meditate" to Icons.Filled.SelfImprovement,
            "Music"    to Icons.Filled.MusicNote,
            "More"     to Icons.Filled.MoreHoriz,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = CalmText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = CalmColors.CalmBlue,
                    selectedTextColor   = CalmColors.CalmBlue,
                    unselectedIconColor = Color.White.copy(alpha = 0.6f),
                    unselectedTextColor = Color.White.copy(alpha = 0.6f),
                    indicatorColor      = Color.Transparent, // Calm has no Material pill
                ),
            )
        }
    }
}
```

## 5. Motion

| Moment | Compose recipe |
|--------|----------------|
| Breathe bubble | `Animatable` 120 → 220 `tween(4000, EaseIn)`, `delay(7000)`, 220 → 120 `tween(8000, EaseOut)`, looped in `LaunchedEffect` |
| Card / pill tap | `animateFloatAsState` 1 → 0.97 `tween(250, EaseOut)`, `HapticFeedbackType.LongPress` |
| Screen transition | `AnimatedContent` with `fadeIn(tween(550)) togetherWith fadeOut(tween(550))` — Calm fades, never hard-slides |
| Session timer ring | `Animatable` 0 → 1 `tween(sessionMs, LinearEasing)` driving a `drawArc` sweep |
| Session complete | soft sage check `fadeIn(tween(600))` + single `HapticFeedbackType.LongPress` |
| Photo parallax | offset hero ~8.dp from scroll via `Modifier.graphicsLayer { translationY = scroll * 0.1f }` |

```kotlin
@Composable
fun SessionTimerRing(progress: Float) { // progress 0f..1f animated linearly
    Canvas(Modifier.size(220.dp)) {
        drawArc(CalmColors.GlassHover, -90f, 360f, false,
            style = Stroke(3.dp.toPx(), cap = StrokeCap.Round))
        drawArc(CalmColors.CalmBlue, -90f, 360f * progress, false,
            style = Stroke(3.dp.toPx(), cap = StrokeCap.Round))
    }
}
```

Haptics: prefer `LocalHapticFeedback`. Keep them gentle and rare — Calm is serene. For a softer pulse use `Vibrator` `VibrationEffect.createOneShot(8, 60)` rather than the default click.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Favorite | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Download | `arrow.down.circle` | `Icons.Outlined.Download` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Background sound | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Timer | `timer` | `Icons.Filled.Timer` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Streak | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Sleep (tab) | `moon.stars.fill` | `Icons.Filled.Bedtime` |
| Meditate (tab) | `figure.mind.and.body` | `Icons.Filled.SelfImprovement` |
| Music (tab) | `music.note` | `Icons.Filled.MusicNote` |
| More (tab) | `ellipsis` | `Icons.Filled.MoreHoriz` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the night-sky gradient runs full-bleed behind the system bars (light-content). Apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` to content so the tab bar clears the gesture nav, but let the gradient ignore insets.
- **No live blur**: Android lacks a first-class backdrop blur — glass surfaces are value-only (`Color.White.copy(alpha = 0.08f)` over the gradient). Bump alpha slightly vs iOS so cards register without the blur depth cue.
- **Font scaling**: `sp` honors the user's font scale — keep it on greetings, titles, body. Pin layout-sensitive text (tab labels, duration meta, the 44sp timer) by wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: mark the breathe bubble `Modifier.clearAndSetSemantics { }` (decorative) and announce phase changes via a live region; label the play pill ("Play Daily Calm"); merge story-row text with `Modifier.semantics(mergeDescendants = true)`.
- **Reduce Motion**: gate the breathe-bubble animation on `Settings.Global.ANIMATOR_DURATION_SCALE` — render a static circle with a text-only phase cue when animations are off; replace fades with instant swaps.
- **Touch targets**: Material guidance is 48.dp minimum. The 44.dp play buttons are close — give the 22.dp download glyph a `Modifier.size(48.dp)` hit area via padding; rows are already 76.dp.
- **Contrast**: `#B8C4D8` on the mid-gradient passes AA at 15sp+; validate the 13sp meta over the lighter top band and darken the scrim if your build targets accessibility compliance.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Calm's identity requires the fixed night-sky gradient and single Calm Blue accent regardless of wallpaper.
