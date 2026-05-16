# Headspace (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Headspace's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the breathing sphere, the Aurora wash, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Headspace's butter-cream canvas, Marigold accent, the breathing meditation sphere, illustration-companion warmth) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a translucent `Surface` instead of iOS blur, `sp`/`dp` instead of `pt`, and an `Animatable`-driven 4-2-4-2 breath cycle instead of SwiftUI's `withAnimation`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for the illustration assets (PNG @2x/@3x equivalents bundled as drawables or loaded remotely). Headspace does no runtime color extraction, so no Palette dependency is needed.

## 1. Color Tokens

```kotlin
// ui/theme/HeadspaceColors.kt
import androidx.compose.ui.graphics.Color

object HeadspaceColors {
    // Canvas & Surfaces (light / default mode)
    val ButterCream  = Color(0xFFFFF7E7)
    val OffCream     = Color(0xFFFBF1DA)
    val WarmGray     = Color(0xFFE8DFC9)
    val SurfaceWhite = Color(0xFFFFFFFF)

    // Brand — Marigold
    val Marigold        = Color(0xFFFF6B35)
    val MarigoldPressed = Color(0xFFE55A2B)
    val MarigoldLight   = Color(0xFFFFB89E)

    // Aurora gradient stops (signature dawn wash)
    val Aurora1 = Color(0xFFFFB89E) // peachy dawn (top)
    val Aurora2 = Color(0xFFFFD25C) // butter sun (mid)
    val Aurora3 = Color(0xFFFF6B35) // marigold morning (bottom)

    // Secondary accents
    val Butter      = Color(0xFFFFD25C)
    val SageMoss    = Color(0xFF7E9F4B)
    val SageLight   = Color(0xFFB4C68F)
    val CoralFlush  = Color(0xFFF4877E)
    val SkyLavender = Color(0xFFB7B0DC)

    // Text — Ink Brown (warm purple-brown, never harsh black)
    val InkBrown     = Color(0xFF2E1A47)
    val InkBrownSoft = Color(0xFF594675)
    val InkBrownMute = Color(0xFF8E7DA5)

    // Dusk (Sleep mode)
    val DuskCanvas   = Color(0xFF1A1430)
    val DuskSurface1 = Color(0xFF2A2046)
    val DuskSurface2 = Color(0xFF3A2F5C)
    val DuskDivider  = Color(0xFF3F3460)

    // Semantic
    val Success      = Color(0xFF7E9F4B) // same as Sage
    val Warning      = Color(0xFFFF9F4A)
    val ErrorCoral   = Color(0xFFE04646)

    // Milestone
    val MilestoneGold = Color(0xFFE5B85C)
}
```

Headspace's default mode is the warm butter-cream light surface, so wire a Material 3 `lightColorScheme` so ripples, dividers, and default component colors inherit the brand. Provide a separate dusk scheme for Sleep mode rather than relying on Android's `isSystemInDarkTheme()` — the dusk world is a deliberate in-app mode, not a system preference.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val HeadspaceLight = lightColorScheme(
    primary        = HeadspaceColors.Marigold,
    onPrimary      = HeadspaceColors.SurfaceWhite,
    background     = HeadspaceColors.ButterCream,
    onBackground   = HeadspaceColors.InkBrown,
    surface        = HeadspaceColors.OffCream,
    onSurface      = HeadspaceColors.InkBrown,
    surfaceVariant = HeadspaceColors.WarmGray,
    outline        = HeadspaceColors.WarmGray,
    error          = HeadspaceColors.ErrorCoral,
)

private val HeadspaceDusk = darkColorScheme(
    primary        = HeadspaceColors.SageMoss,   // Sage is the Sleep accent, not Marigold
    onPrimary      = HeadspaceColors.ButterCream,
    background     = HeadspaceColors.DuskCanvas,
    onBackground   = HeadspaceColors.ButterCream,
    surface        = HeadspaceColors.DuskSurface1,
    onSurface      = HeadspaceColors.ButterCream,
    surfaceVariant = HeadspaceColors.DuskSurface2,
    outline        = HeadspaceColors.DuskDivider,
)

@Composable
fun HeadspaceTheme(sleepMode: Boolean = false, content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (sleepMode) HeadspaceDusk else HeadspaceLight,
        typography  = HeadspaceTypography,
        content     = content,
    )
```

## 2. Typography

Apercu is licensed via Colophon Foundry. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to `Nunito` (the closest soft humanist sans on Google Fonts, sharing Apercu's curled 'a'); the system default Roboto is a colder last resort.

```kotlin
// ui/theme/HeadspaceType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Apercu = FontFamily(
    Font(R.font.apercu_light,   FontWeight.Light),    // 300
    Font(R.font.apercu_regular, FontWeight.Normal),   // 400
    Font(R.font.apercu_medium,  FontWeight.Medium),   // 500
    Font(R.font.apercu_bold,    FontWeight.Bold),     // 700
    Font(R.font.apercu_black,   FontWeight.Black),    // 900
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, title case only)
object HeadspaceText {
    val GreetingHero = TextStyle(Apercu, fontWeight = FontWeight.Bold,   fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.3).sp)
    val ScreenTitle  = TextStyle(Apercu, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.3).sp)
    val MedTitle     = TextStyle(Apercu, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp)
    val SectionHdr   = TextStyle(Apercu, fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val CardTitle    = TextStyle(Apercu, fontWeight = FontWeight.Medium, fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val Body         = TextStyle(Apercu, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 24.sp) // generous 1.5
    val BodyBold     = TextStyle(Apercu, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 24.sp)
    val Guidance     = TextStyle(Apercu, fontWeight = FontWeight.Normal, fontSize = 22.sp, lineHeight = 31.sp) // play-screen breath text
    val Meta         = TextStyle(Apercu, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val TagPill      = TextStyle(Apercu, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
    val Caption      = TextStyle(Apercu, fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 14.sp)
    val Tab          = TextStyle(Apercu, fontWeight = FontWeight.Medium, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Button       = TextStyle(Apercu, fontWeight = FontWeight.Medium, fontSize = 17.sp, lineHeight = 17.sp) // Medium, not Bold
    val TimerLarge   = TextStyle(Apercu, fontWeight = FontWeight.Bold,   fontSize = 56.sp, lineHeight = 56.sp, letterSpacing = (-0.5).sp)
    val StreakNumber = TextStyle(Apercu, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 22.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val HeadspaceTypography = Typography(
    headlineLarge  = HeadspaceText.GreetingHero,
    headlineMedium = HeadspaceText.ScreenTitle,
    titleLarge     = HeadspaceText.MedTitle,
    titleMedium    = HeadspaceText.CardTitle,
    bodyMedium     = HeadspaceText.Body,
    labelSmall     = HeadspaceText.Tab,
)
```

Set `TimerLarge` and `StreakNumber` digits to align by wrapping the host in `CompositionLocalProvider(LocalTextStyle …)` with `TextStyle(fontFeatureSettings = "tnum")` — Compose has no `monospacedDigit()` shorthand; `fontFeatureSettings = "tnum"` is the equivalent.

## 3. Signature Components

### Breathing Sphere (the hero component)

Headspace's signature move. Drive the 4-2-4-2 cadence with a coroutine + `Animatable` rather than `rememberInfiniteTransition` so the hold phases are exact. **Pure tween easing — never a spring** (springs feel digital and break the meditative tone).

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween
import androidx.compose.animation.core.EaseInOut
import androidx.compose.foundation.Canvas
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp

@Composable
fun BreathingSphere(
    diameter: Dp,
    modifier: Modifier = Modifier,
    animate: Boolean = true, // pass false when Reduce Motion is on
) {
    val scale = remember { Animatable(1f) }

    LaunchedEffect(animate) {
        if (!animate) { scale.snapTo(1f); return@LaunchedEffect }
        while (true) {
            scale.animateTo(1.04f, tween(4000, easing = EaseInOut)) // 4s inhale
            kotlinx.coroutines.delay(2000)                          // 2s hold high
            scale.animateTo(0.96f, tween(4000, easing = EaseInOut)) // 4s exhale
            scale.snapTo(1f)
            kotlinx.coroutines.delay(2000)                          // 2s hold neutral
        }
    }

    Canvas(
        modifier
            .size(diameter)
            .graphicsLayer { scaleX = scale.value; scaleY = scale.value }
    ) {
        val r = size.minDimension / 2f
        drawCircle(
            brush = Brush.radialGradient(
                colorStops = arrayOf(
                    0.0f to HeadspaceColors.Aurora1,        // top-left highlight
                    0.70f to HeadspaceColors.Marigold,
                    1.0f to HeadspaceColors.MarigoldPressed, // bottom-right shadow
                ),
                center = Offset(size.width * 0.32f, size.height * 0.32f),
                radius = r * 1.4f,
            ),
            radius = r,
        )
        drawCircle(
            color = HeadspaceColors.InkBrown.copy(alpha = 0.08f),
            radius = r,
            style = androidx.compose.ui.graphics.drawscope.Stroke(width = 1.dp.toPx()),
        )
    }
}
```

### Aurora Gradient Background (Today hero)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*

@Composable
fun AuroraBackground(modifier: Modifier = Modifier, content: @Composable BoxScope.() -> Unit) {
    Box(
        modifier.background(
            Brush.verticalGradient(
                listOf(HeadspaceColors.Aurora1, HeadspaceColors.Aurora2, HeadspaceColors.Aurora3)
            )
        ),
        content = content,
    )
}
```

### Today Hero

```kotlin
import androidx.compose.material3.Text

@Composable
fun TodayHero(greeting: String, vibe: String) {
    AuroraBackground(Modifier.fillMaxWidth().height(280.dp)) {
        Column(
            Modifier
                .align(Alignment.TopStart)
                .statusBarsPadding()
                .padding(start = 24.dp, end = 24.dp, top = 16.dp),
            verticalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Text(greeting, style = HeadspaceText.GreetingHero, color = HeadspaceColors.InkBrown)
            Text(
                "Today's vibe: $vibe",
                style = HeadspaceText.Body,
                color = HeadspaceColors.InkBrown.copy(alpha = 0.8f),
            )
        }
    }
}
```

### Primary Action Button (gentle warm glow)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun HeadspaceCTAButton(
    title: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "ctaScale")
    val haptics = LocalHapticFeedback.current
    val shape = RoundedCornerShape(28.dp) // full pill on a 56.dp button

    Box(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 24.dp)
            .scale(scale)
            .height(56.dp)
            .shadow(16.dp, shape, spotColor = HeadspaceColors.Marigold.copy(alpha = 0.25f)) // sunrise glow
            .clip(shape)
            .background(if (pressed) HeadspaceColors.MarigoldPressed else HeadspaceColors.Marigold)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ iOS soft impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = HeadspaceText.Button, color = HeadspaceColors.SurfaceWhite)
    }
}
```

### Meditation Session Card

```kotlin
import androidx.compose.foundation.layout.Box
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import coil.compose.AsyncImage

@Composable
fun MeditationCard(
    illustrationUrl: String,
    tag: String,
    title: String,
    host: String,
    duration: String,
    modifier: Modifier = Modifier,
) {
    Box(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 24.dp)
            .height(200.dp)
            .shadow(16.dp, RoundedCornerShape(24.dp), spotColor = HeadspaceColors.InkBrown.copy(alpha = 0.08f))
            .clip(RoundedCornerShape(24.dp)),
    ) {
        AsyncImage(
            model = illustrationUrl,
            contentDescription = null,
            modifier = Modifier.matchParentSize(),
            contentScale = ContentScale.Crop,
        )
        Box(
            Modifier.matchParentSize().background(
                Brush.verticalGradient(listOf(Color.Transparent, HeadspaceColors.InkBrown.copy(alpha = 0.6f)))
            )
        )
        Column(
            Modifier.align(Alignment.BottomStart).padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Box(
                Modifier
                    .clip(RoundedCornerShape(percent = 50))
                    .background(Color.White.copy(alpha = 0.25f))
                    .padding(horizontal = 12.dp, vertical = 4.dp)
            ) {
                Text(tag, style = HeadspaceText.TagPill, color = Color.White)
            }
            Text(title, style = HeadspaceText.MedTitle, color = Color.White)
            Text(
                "$host · $duration",
                style = HeadspaceText.Meta,
                color = Color.White.copy(alpha = 0.85f),
                maxLines = 1, overflow = TextOverflow.Ellipsis,
            )
        }
    }
}
```

### Quick Action Tile

```kotlin
@Composable
fun QuickActionTile(
    tileColor: Color,
    illustrationUrl: String,
    title: String,
    subtitle: String,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "tileScale")
    val haptics = LocalHapticFeedback.current

    Column(
        Modifier
            .scale(scale)
            .size(width = 140.dp, height = 180.dp)
            .clip(RoundedCornerShape(20.dp))
            .background(tileColor)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
            }
            .padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        AsyncImage(
            model = illustrationUrl,
            contentDescription = null,
            modifier = Modifier.size(64.dp),
        )
        Text(title, style = HeadspaceText.CardTitle, color = HeadspaceColors.InkBrown)
        Text(subtitle, style = HeadspaceText.Meta, color = HeadspaceColors.InkBrown.copy(alpha = 0.7f))
    }
}
```

### Player Controls

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Pause
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material.icons.filled.Replay10
import androidx.compose.material.icons.filled.Forward10
import androidx.compose.material3.Icon

@Composable
fun PlayerControls(
    isPlaying: Boolean,
    onPlayPause: () -> Unit,
    onSkipBack: () -> Unit,
    onSkipForward: () -> Unit,
) {
    val haptics = LocalHapticFeedback.current
    Row(
        horizontalArrangement = Arrangement.spacedBy(32.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        CircleControl(Icons.Filled.Replay10, "Skip back 15 seconds", onSkipBack)
        Box(
            Modifier
                .size(72.dp)
                .clip(CircleShape)
                .background(HeadspaceColors.Marigold)
                .shadow(16.dp, CircleShape, spotColor = HeadspaceColors.Marigold.copy(alpha = 0.25f))
                .clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                    onPlayPause()
                },
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                if (isPlaying) Icons.Filled.Pause else Icons.Filled.PlayArrow,
                contentDescription = if (isPlaying) "Pause" else "Play",
                tint = HeadspaceColors.SurfaceWhite,
                modifier = Modifier.size(28.dp),
            )
        }
        CircleControl(Icons.Filled.Forward10, "Skip forward 15 seconds", onSkipForward)
    }
}

@Composable
private fun CircleControl(
    icon: androidx.compose.ui.graphics.vector.ImageVector,
    label: String,
    onClick: () -> Unit,
) {
    Box(
        Modifier
            .size(48.dp)
            .clip(CircleShape)
            .background(HeadspaceColors.OffCream)
            .clickable(onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Icon(icon, contentDescription = label, tint = HeadspaceColors.InkBrown, modifier = Modifier.size(22.dp))
    }
}
```

### Streak Ring (Profile)

```kotlin
@Composable
fun StreakRing(currentStreak: Int, goal: Int = 30, modifier: Modifier = Modifier) {
    val target = (currentStreak.toFloat() / goal).coerceIn(0f, 1f)
    val progress = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current

    LaunchedEffect(target) {
        progress.animateTo(target, tween(1200, easing = EaseInOut))
        haptics.performHapticFeedback(HapticFeedbackType.LongPress) // soft success
    }

    Box(Modifier.size(120.dp), contentAlignment = Alignment.Center) {
        Canvas(Modifier.fillMaxSize()) {
            val sw = 8.dp.toPx()
            drawCircle(HeadspaceColors.OffCream, style = androidx.compose.ui.graphics.drawscope.Stroke(sw))
            drawArc(
                color = HeadspaceColors.Marigold,
                startAngle = -90f,
                sweepAngle = 360f * progress.value,
                useCenter = false,
                style = androidx.compose.ui.graphics.drawscope.Stroke(sw, cap = androidx.compose.ui.graphics.StrokeCap.Round),
            )
        }
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Text("$currentStreak", style = HeadspaceText.TimerLarge, color = HeadspaceColors.InkBrown)
            Text("days", style = HeadspaceText.Meta, color = HeadspaceColors.InkBrownSoft)
        }
    }
}
```

### Mood Tag Row

```kotlin
import androidx.compose.animation.core.spring
import androidx.compose.animation.core.Spring

@Composable
fun MoodTag(emoji: String, label: String, selected: Boolean, onToggle: () -> Unit) {
    val scale by animateFloatAsState(
        if (selected) 1.05f else 1f,
        spring(dampingRatio = 0.7f, stiffness = Spring.StiffnessMedium),
        label = "moodScale",
    )
    val haptics = LocalHapticFeedback.current

    Column(
        Modifier
            .scale(scale)
            .size(width = 80.dp, height = 88.dp)
            .clip(RoundedCornerShape(16.dp))
            .background(if (selected) HeadspaceColors.Butter else HeadspaceColors.OffCream)
            .clickable {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ≈ iOS selection
                onToggle()
            },
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center,
    ) {
        Text(emoji, fontSize = 32.sp)
        Spacer(Modifier.height(6.dp))
        Text(label, style = HeadspaceText.Meta, color = HeadspaceColors.InkBrown)
    }
}
```

## 4. Breathing Meditation Sphere — the distinctive system

Headspace does no color extraction; its most distinctive interaction is the **234pt breathing sphere on a 12-second 4-2-4-2 cycle** (≈234.dp on a 390.dp-wide phone — 60% of screen width). The implementation lives in §3 (`BreathingSphere`). The full play screen composes it with guidance text above and the countdown below, on a butter-cream canvas with 80.dp of clearance so the sphere feels uncrowded.

```kotlin
@Composable
fun PlayScreen(
    guidanceText: String, // "Take a deep breath in..."
    remaining: String,    // "10:00"
    isPlaying: Boolean,
    onPlayPause: () -> Unit,
    reduceMotion: Boolean = false,
) {
    Column(
        Modifier
            .fillMaxSize()
            .background(HeadspaceColors.ButterCream)
            .padding(horizontal = 24.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center,
    ) {
        Text(
            guidanceText,
            style = HeadspaceText.Guidance,
            color = HeadspaceColors.InkBrown,
            modifier = Modifier.padding(bottom = 80.dp),
        )
        BreathingSphere(diameter = 234.dp, animate = !reduceMotion)
        Spacer(Modifier.height(80.dp))
        Text(remaining, style = HeadspaceText.TimerLarge, color = HeadspaceColors.InkBrown)
        Spacer(Modifier.height(48.dp))
        PlayerControls(isPlaying, onPlayPause, {}, {})
    }
}
```

A thin progress arc renders at the very top of the screen (`Canvas` `drawArc`, Sage `#7E9F4B` fill over a Warm Gray `#E8DFC9` track, 2.dp stroke, round caps) — overlay it in a `Box` above the `Column`.

## 5. Navigation (Bottom Tab Bar)

Use Material 3 `NavigationBar`. Headspace's iOS tab bar is a butter-cream surface (Sleep mode switches to dusk); Android has no first-class live blur, so use a fully opaque canvas surface. **The active tint is Marigold on Today/Meditate/Move/Profile, but Sage `#7E9F4B` on the Sleep tab** — the only tab with its own color.

```kotlin
import androidx.compose.material.icons.filled.WbSunny
import androidx.compose.material.icons.filled.Spa
import androidx.compose.material.icons.filled.Bedtime
import androidx.compose.material.icons.filled.DirectionsWalk
import androidx.compose.material.icons.filled.Person
import androidx.compose.material3.*

@Composable
fun HeadspaceBottomBar(selected: Int, onSelect: (Int) -> Unit, sleepMode: Boolean) {
    val items = listOf(
        "Today"    to Icons.Filled.WbSunny,
        "Meditate" to Icons.Filled.Spa,
        "Sleep"    to Icons.Filled.Bedtime,
        "Move"     to Icons.Filled.DirectionsWalk,
        "Profile"  to Icons.Filled.Person,
    )
    val haptics = LocalHapticFeedback.current
    NavigationBar(
        containerColor = if (sleepMode) HeadspaceColors.DuskCanvas else HeadspaceColors.ButterCream,
        tonalElevation = 0.dp,
    ) {
        items.forEachIndexed { i, (label, icon) ->
            val isSleepTab = i == 2
            val activeTint = if (isSleepTab) HeadspaceColors.SageMoss else HeadspaceColors.Marigold
            NavigationBarItem(
                selected = selected == i,
                onClick = {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                    onSelect(i)
                },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = HeadspaceText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = activeTint,
                    selectedTextColor   = activeTint,
                    unselectedIconColor = HeadspaceColors.InkBrownMute,
                    unselectedTextColor = HeadspaceColors.InkBrownMute,
                    indicatorColor      = Color.Transparent, // Headspace has no Material pill
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Breathing sphere | `Animatable` + coroutine: `tween(4000, EaseInOut)` to 1.04, `delay(2000)`, `tween(4000, EaseInOut)` to 0.96, `delay(2000)` — **no spring** |
| Primary CTA tap | `animateFloatAsState` 1 → 0.98 + `HapticFeedbackType.LongPress`; Marigold glow shadow |
| Mood tag select | `animateFloatAsState` 1 → 1.05 with `spring(dampingRatio = 0.7f)` + Butter fill + `TextHandleMove` haptic |
| Streak ring reveal | `Animatable.animateTo(progress, tween(1200, EaseInOut))` + soft success haptic |
| Tab switch | `TextHandleMove` haptic + Material crossfade between outlined/filled icons |
| Sleep mode transition | Crossfade the canvas `Color` from `ButterCream` to `DuskCanvas` over 600ms |

```kotlin
// Sleep mode canvas crossfade — visceral, like dusk falling
@Composable
fun rememberSleepCanvas(sleepMode: Boolean): Color {
    val color by animateColorAsState(
        targetValue = if (sleepMode) HeadspaceColors.DuskCanvas else HeadspaceColors.ButterCream,
        animationSpec = tween(600, easing = EaseInOut),
        label = "sleepCanvas",
    )
    return color
}
```

Haptics: Headspace's vocabulary is always soft. Prefer `LocalHapticFeedback` (`LongPress` ≈ iOS `.soft` impact, `TextHandleMove` ≈ `.selection`). For a closer match to the iOS soft impact use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, 60)`. **Never use a heavy/long vibration** — it breaks the calm.

## 7. Icons

The SF Symbols map to Material Icons (`androidx.compose.material:material-icons-extended`). The illustration companions are bitmap assets, not glyphs — ship as drawables and render with `Image(painterResource(...))`.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Today tab | `sun.max` / `sun.max.fill` | `Icons.Filled.WbSunny` |
| Meditate tab | `leaf` / `leaf.fill` | `Icons.Filled.Spa` |
| Sleep tab | `moon` / `moon.fill` | `Icons.Filled.Bedtime` |
| Move tab | `figure.walk` / `figure.walk.motion` | `Icons.Filled.DirectionsWalk` |
| Profile tab | `person` / `person.fill` | `Icons.Filled.Person` |
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Skip back 15s | `gobackward.15` | `Icons.Filled.Replay10` (custom 15s vector preferred) |
| Skip forward 15s | `goforward.15` | `Icons.Filled.Forward10` (custom 15s vector preferred) |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Favorite | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Streak flame | `flame.fill` | `Icons.Filled.LocalFireDepartment` (Marigold) |
| Notifications | `bell` | `Icons.Filled.Notifications` |
| Milestone trophy | `trophy.fill` | `Icons.Filled.EmojiEvents` (Milestone Gold) |

The Headspace illustration companions (round-headed pastel characters) have no Material equivalent — bundle the PNG/vector assets and size them 80.dp inline, 240.dp for milestone reveals, 64.dp for quick-action thumbnails.

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `Animatable` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The butter-cream canvas wants dark-content (`WindowCompat` `isAppearanceLightStatusBars = true`); in Sleep mode flip to light-content. Apply `Modifier.statusBarsPadding()` to the Today greeting and `Scaffold` insets so the tab bar clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on the greeting, body, guidance, and meditation titles. Pin layout-sensitive text (the 56.sp timer, 11.sp tab labels, tag pills, the streak number) by wrapping in `CompositionLocalProvider(LocalDensity provides Density(LocalDensity.current.density, fontScale = 1f))`.
- **TalkBack**: the breathing sphere is one element — `Modifier.semantics { contentDescription = "Breathing meditation. Currently exhaling. 10 minutes remaining." }`, updating the description each phase so TalkBack users can follow the breath. Merge meditation-card text with `Modifier.semantics(mergeDescendants = true)`.
- **Touch targets**: Material guidance is 48.dp minimum. The 72.dp play button is well clear; the 48.dp skip circles already meet it; mood tags (80×88.dp) and tag pills are generous.
- **Contrast**: Ink Brown `#2E1A47` on Butter Cream `#FFF7E7` passes WCAG AAA at all sizes. Marigold `#FF6B35` on Butter Cream passes AA at 17sp+ Medium — validate any small Marigold text and bump to Marigold Pressed `#E55A2B` if targeting strict compliance. Inverted cream on dusk surfaces passes AAA.
- **Reduce Motion**: pass `animate = false` to `BreathingSphere` to render it static at scale 1.0; replace the streak-ring animate-in with an instant draw. Honor `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`.
- **Material You**: do **not** enable `dynamicLightColorScheme()` — Headspace's brand requires the fixed butter-cream canvas, Marigold accent, and the deliberate dusk Sleep palette regardless of wallpaper.
