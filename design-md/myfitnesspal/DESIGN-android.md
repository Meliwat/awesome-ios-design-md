# MyFitnessPal (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports MyFitnessPal's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the calorie ring, macro donut, diary FAB, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (MyFitnessPal's white clinical-but-friendly canvas, MFP Blue heritage + Lake Blue action, the color-flipping calorie ring, the locked macro trio) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a translucent `Surface` instead of iOS blur, `sp`/`dp` instead of `pt`, tabular figures via `fontFeatureSettings = "tnum"`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` if you load remote food thumbnails. MyFitnessPal does no runtime color extraction, so no Palette dependency is needed.

## 1. Color Tokens

```kotlin
// ui/theme/MfpColors.kt
import androidx.compose.ui.graphics.Color

object MfpColors {
    // Canvas & Surfaces
    val Canvas       = Color(0xFFFFFFFF)
    val SurfaceGray  = Color(0xFFF5F7FA)
    val SurfaceGray2 = Color(0xFFE5E9F0)
    val Divider      = Color(0xFFE5E7EB)
    val Hairline     = Color(0xFFEEF1F5)

    // Text
    val Ink        = Color(0xFF1F2937) // primary
    val Slate      = Color(0xFF4B5563) // secondary
    val Mute       = Color(0xFF9CA3AF) // tertiary
    val HeroNumber = Color(0xFF111827) // the big calorie number — even darker than Ink

    // Brand
    val Blue        = Color(0xFF005DAA) // heritage — logomark, Premium gradient
    val LakeBlue    = Color(0xFF0072CE) // current action color, ring fill at on-track
    val LakePressed = Color(0xFF005FA8)
    val SkyBlue     = Color(0xFFE7F0FF) // tinted backgrounds

    // Macro trio — do NOT reorder, do NOT recolor
    val Carbs   = Color(0xFFFF9F1C) // always first slice
    val Fat     = Color(0xFFA463F2) // always second slice
    val Protein = Color(0xFF19C37D) // always third slice

    // Semantic
    val UnderGoal   = Color(0xFF19C37D)
    val Approaching = Color(0xFFF59E0B)
    val OverLimit   = Color(0xFFEF4444)
    val Streak      = Color(0xFFFB923C)
    val PremiumGold = Color(0xFFD4A437)

    // Dark mode (deep, slightly-blue dark — not pure black)
    val DarkCanvas   = Color(0xFF0F1419)
    val DarkSurface1 = Color(0xFF1A1F26)
    val DarkSurface2 = Color(0xFF252B33)
    val DarkDivider  = Color(0xFF2A2F37)
    val DarkTextPri  = Color(0xFFF3F4F6)
    val DarkTextSec  = Color(0xFF9CA3AF)
    val LakeDark     = Color(0xFF3B8FDF)
}
```

MyFitnessPal defaults to the clinical white surface; dark mode is a deep blue-black. Wire both Material 3 schemes so ripples and default component colors inherit the brand. **Never** map the macro trio into the scheme — those colors belong only to the macro context.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val MfpLight = lightColorScheme(
    primary        = MfpColors.LakeBlue,
    onPrimary      = Color.White,
    background     = MfpColors.Canvas,
    onBackground   = MfpColors.Ink,
    surface        = MfpColors.Canvas,
    onSurface      = MfpColors.Ink,
    surfaceVariant = MfpColors.SurfaceGray,
    outline        = MfpColors.Divider,
    error          = MfpColors.OverLimit,
)

private val MfpDark = darkColorScheme(
    primary        = MfpColors.LakeDark,
    onPrimary      = Color.White,
    background     = MfpColors.DarkCanvas,
    onBackground   = MfpColors.DarkTextPri,
    surface        = MfpColors.DarkSurface1,
    onSurface      = MfpColors.DarkTextPri,
    surfaceVariant = MfpColors.DarkSurface2,
    outline        = MfpColors.DarkDivider,
)

@Composable
fun MfpTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) MfpDark else MfpLight,
        typography  = MfpTypography,
        content     = content,
    )
```

## 2. Typography

MyFitnessPal uses the system font (SF Pro on iOS → Roboto on Android; Proxima Nova only on marketing/web). The whole point is the numerals — **tabular figures on every number that summarizes nutrition**. Compose has no `monospacedDigit()`; the equivalent is `fontFeatureSettings = "tnum"`, baked into every numeric style below.

```kotlin
// ui/theme/MfpType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

private val Sys = FontFamily.Default // Roboto on Android; Display behavior is automatic

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1).
// `fontFeatureSettings = "tnum"` == iOS .monospacedDigit() — non-negotiable on numbers.
object MfpText {
    val CalorieHero  = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 56.sp, lineHeight = 56.sp, letterSpacing = (-1.0).sp, fontFeatureSettings = "tnum")
    val CalorieLabel = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val LargeNav     = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val Section      = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Meal         = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 22.sp, letterSpacing = (-0.2).sp)
    val FoodName     = TextStyle(Sys, fontWeight = FontWeight.Medium,   fontSize = 16.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val FoodSub      = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Calorie      = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp, fontFeatureSettings = "tnum")
    val Body         = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val BodySmall    = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val MacroNumber  = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 22.sp, letterSpacing = (-0.3).sp, fontFeatureSettings = "tnum")
    val MacroLabel   = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.8.sp)
    val Button       = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp)
    val Tab          = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp)
    val StreakBadge  = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 12.sp, lineHeight = 12.sp, fontFeatureSettings = "tnum")
}

// Map onto Material 3 slots so stock components inherit the brand
val MfpTypography = Typography(
    headlineLarge = MfpText.LargeNav,
    headlineSmall = MfpText.Section,
    titleMedium   = MfpText.Meal,
    bodyMedium    = MfpText.Body,
    labelLarge    = MfpText.Button,
    labelSmall    = MfpText.Tab,
)
```

## 3. Signature Components

### Calorie Ring (the hero of Dashboard) — semantic color flip

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp

@Composable
fun CalorieRing(
    goal: Int,
    consumed: Int,
    exercise: Int,
    diameter: Dp = 220.dp,
    strokeWidth: Dp = 18.dp,
) {
    val remaining = goal - consumed + exercise
    val target = (consumed.toFloat() / goal.coerceAtLeast(1)).coerceIn(0f, 1f)

    // Color flips semantically with how much of the goal is used
    val ringColor: Color = when (consumed.toFloat() / goal.coerceAtLeast(1)) {
        in 0f..0.6f   -> MfpColors.UnderGoal
        in 0.6f..0.95f -> MfpColors.LakeBlue   // the most common state
        in 0.95f..1.0f -> MfpColors.Approaching
        else           -> MfpColors.OverLimit
    }

    val progress = remember { Animatable(0f) }
    LaunchedEffect(target) { progress.animateTo(target, tween(600)) } // 600ms ease-out fill

    Box(Modifier.size(diameter), contentAlignment = Alignment.Center) {
        Canvas(Modifier.fillMaxSize()) {
            val sw = strokeWidth.toPx()
            drawCircle(MfpColors.SurfaceGray2, style = Stroke(sw))
            drawArc(
                color = ringColor,
                startAngle = -90f,
                sweepAngle = 360f * progress.value,
                useCenter = false,
                style = Stroke(sw, cap = StrokeCap.Round),
            )
        }
        Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text("REMAINING", style = MfpText.CalorieLabel, color = MfpColors.Slate)
            // contentTransition gives the iOS number-flip when a food is added
            androidx.compose.animation.AnimatedContent(
                targetState = remaining,
                transitionSpec = {
                    (androidx.compose.animation.slideInVertically { it / 2 } + androidx.compose.animation.fadeIn())
                        .togetherWith(androidx.compose.animation.slideOutVertically { -it / 2 } + androidx.compose.animation.fadeOut())
                },
                label = "calorieFlip",
            ) { value ->
                Text("%,d".format(value), style = MfpText.CalorieHero, color = MfpColors.HeroNumber)
            }
            Text("calories", style = MfpText.BodySmall, color = MfpColors.Slate)
        }
    }
}
```

### Macro Donut (Carbs / Fat / Protein) — locked order, locked colors

```kotlin
@Composable
fun MacroDonut(
    carbs: Int, fat: Int, protein: Int,
    carbsGoal: Int, fatGoal: Int, proteinGoal: Int,
    diameter: Dp = 96.dp,
    strokeWidth: Dp = 10.dp,
) {
    val total = (carbs + fat + protein).coerceAtLeast(1)
    val carbsFrac = carbs.toFloat() / total
    val fatFrac = fat.toFloat() / total
    val proteinFrac = protein.toFloat() / total
    val goalTotal = (carbsGoal + fatGoal + proteinGoal).coerceAtLeast(1)

    Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(16.dp)) {
        Box(Modifier.size(diameter), contentAlignment = Alignment.Center) {
            Canvas(Modifier.fillMaxSize()) {
                val sw = strokeWidth.toPx()
                fun seg(start: Float, sweep: Float, color: Color) =
                    drawArc(color, -90f + start * 360f, sweep * 360f, false, style = Stroke(sw, cap = StrokeCap.Butt))
                seg(0f, carbsFrac, MfpColors.Carbs)                       // 1st — orange
                seg(carbsFrac, fatFrac, MfpColors.Fat)                    // 2nd — purple
                seg(carbsFrac + fatFrac, proteinFrac, MfpColors.Protein)  // 3rd — green
            }
            Text("${(total.toFloat() / goalTotal * 100).toInt()}%", style = MfpText.MacroNumber, color = MfpColors.Ink)
        }
        Row(horizontalArrangement = Arrangement.spacedBy(24.dp)) {
            MacroLegend(MfpColors.Carbs, "CARBS", carbs, carbsGoal)
            MacroLegend(MfpColors.Fat, "FAT", fat, fatGoal)
            MacroLegend(MfpColors.Protein, "PROTEIN", protein, proteinGoal)
        }
    }
}

@Composable
private fun MacroLegend(color: Color, label: String, grams: Int, goal: Int) {
    Column(verticalArrangement = Arrangement.spacedBy(4.dp)) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Box(
                Modifier
                    .size(8.dp)
                    .clip(androidx.compose.foundation.shape.RoundedCornerShape(2.dp))
                    .background(color)
            )
            Text(label, style = MfpText.MacroLabel, color = MfpColors.Slate)
        }
        Text(
            "${grams}g · ${(grams.toFloat() / goal.coerceAtLeast(1) * 100).toInt()}%",
            style = MfpText.BodySmall.copy(fontFeatureSettings = "tnum"),
            color = MfpColors.Ink,
        )
    }
}
```

### Diary Food Row

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ChevronRight
import androidx.compose.material3.Icon
import androidx.compose.ui.draw.clip
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun FoodRow(name: String, subtitle: String, calories: Int, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()

    Row(
        Modifier
            .fillMaxWidth()
            .heightIn(min = 64.dp)
            .background(if (pressed) MfpColors.SurfaceGray else MfpColors.Canvas) // 100ms fade, no scale
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(name, style = MfpText.FoodName, color = MfpColors.Ink, maxLines = 1, overflow = TextOverflow.Ellipsis)
            Text(subtitle, style = MfpText.FoodSub, color = MfpColors.Slate, maxLines = 1, overflow = TextOverflow.Ellipsis)
        }
        Text("%,d".format(calories), style = MfpText.Calorie, color = MfpColors.Ink)
        Icon(Icons.Filled.ChevronRight, contentDescription = null, tint = MfpColors.Mute, modifier = Modifier.size(12.dp))
    }
}
```

### Meal Section ("Breakfast" / "Lunch" / "Dinner" / "Snacks")

Keep this exact order — it is the cultural pattern users expect.

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.expandVertically
import androidx.compose.animation.shrinkVertically
import androidx.compose.animation.core.tween
import androidx.compose.material.icons.filled.AddCircle

@Composable
fun MealSection(
    title: String,
    totalCalories: Int,
    onAddFood: () -> Unit,
    content: @Composable () -> Unit,
) {
    var expanded by remember { mutableStateOf(true) }

    Column(Modifier.fillMaxWidth().background(MfpColors.Canvas)) {
        Row(
            Modifier
                .fillMaxWidth()
                .height(48.dp)
                .clickable { expanded = !expanded }
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text(title, style = MfpText.Meal, color = MfpColors.Ink)
            Spacer(Modifier.weight(1f))
            Text("%,d".format(totalCalories), style = MfpText.Calorie, color = MfpColors.Ink)
        }
        Box(Modifier.fillMaxWidth().height(0.5.dp).background(MfpColors.Divider))

        AnimatedVisibility(
            visible = expanded,
            enter = expandVertically(tween(250)),
            exit = shrinkVertically(tween(250)),
        ) {
            Column {
                content()
                // "+ Add Food" row — the fastest entry path, never omit it
                Row(
                    Modifier
                        .fillMaxWidth()
                        .height(56.dp)
                        .clickable(onClick = onAddFood)
                        .padding(horizontal = 16.dp),
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(8.dp),
                ) {
                    Icon(Icons.Filled.AddCircle, contentDescription = null, tint = MfpColors.LakeBlue, modifier = Modifier.size(22.dp))
                    Text("Add Food", style = MfpText.Button.copy(fontSize = 15.sp), color = MfpColors.LakeBlue)
                }
            }
        }
    }
}
```

### Diary FAB (centered Scan/Add button, blue-tinted shadow)

```kotlin
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.Add
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun DiaryFAB(onClick: () -> Unit, modifier: Modifier = Modifier) {
    val haptics = LocalHapticFeedback.current
    Box(
        modifier
            .size(56.dp)
            .shadow(16.dp, CircleShape, spotColor = MfpColors.Blue.copy(alpha = 0.35f)) // blue-tinted, not neutral
            .clip(CircleShape)
            .background(MfpColors.LakeBlue)
            .clickable(interactionSource = remember { MutableInteractionSource() }, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ iOS medium impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(Icons.Filled.Add, contentDescription = "Add food", tint = Color.White, modifier = Modifier.size(22.dp))
    }
}
```

### Primary CTA

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.draw.scale

@Composable
fun MfpPrimaryButton(label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "ctaScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier
            .fillMaxWidth()
            .scale(scale)
            .heightIn(min = 48.dp)
            .clip(RoundedCornerShape(12.dp)) // never a full pill on the primary CTA
            .background(if (pressed) MfpColors.LakePressed else MfpColors.LakeBlue)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ≈ iOS light impact
                onClick()
            }
            .padding(horizontal = 28.dp, vertical = 14.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = MfpText.Button, color = Color.White)
    }
}
```

### Streak Flame Badge

```kotlin
import androidx.compose.material.icons.filled.LocalFireDepartment

@Composable
fun StreakBadge(days: Int) {
    Row(
        Modifier
            .clip(CircleShape)
            .background(MfpColors.SurfaceGray)
            .padding(horizontal = 10.dp, vertical = 6.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Icon(Icons.Filled.LocalFireDepartment, contentDescription = null, tint = MfpColors.Streak, modifier = Modifier.size(14.dp))
        Text("$days", style = MfpText.StreakBadge, color = MfpColors.Ink)
    }
}
```

### Dashboard Card

```kotlin
import androidx.compose.foundation.border
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun DashboardCard(label: String, icon: ImageVector, content: @Composable () -> Unit) {
    Column(
        Modifier
            .clip(RoundedCornerShape(16.dp))
            .background(MfpColors.Canvas)
            .border(1.dp, MfpColors.Divider, RoundedCornerShape(16.dp)) // no shadow — clinical & flat
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically) {
            Text(label, style = MfpText.MacroLabel, color = MfpColors.Slate)
            Spacer(Modifier.weight(1f))
            Icon(icon, contentDescription = null, tint = MfpColors.Slate, modifier = Modifier.size(14.dp))
        }
        content()
    }
}
```

## 4. The Calorie Ring color-flip — the distinctive system

MyFitnessPal does no color extraction; its distinctive system is the **220pt calorie ring whose fill flips color semantically** as the day's eating accumulates (the implementation lives in §3). The thresholds:

| Consumed vs goal | Ring color | Token |
|------------------|-----------|-------|
| Under 60% | green | `MfpColors.UnderGoal` `#19C37D` |
| 60–95% (on track) | blue | `MfpColors.LakeBlue` `#0072CE` (most common) |
| 95–100% (approaching) | amber | `MfpColors.Approaching` `#F59E0B` |
| Over 100% | red | `MfpColors.OverLimit` `#EF4444` |

When a food is added, the ring's swept arc animates over 600ms (`Animatable.animateTo(target, tween(600))`) and the center number does a quick vertical flip (`AnimatedContent` with slide+fade — Compose's equivalent of iOS `contentTransition(.numericText)`). Never render the ring without tabular figures: `MfpText.CalorieHero` carries `fontFeatureSettings = "tnum"` so the giant number's digits align as it ticks. Never compress the ring below 180.dp (the number stops being legible) and never repurpose the macro trio colors here — the ring uses only the semantic blue/green/amber/red.

## 5. Navigation (Bottom Tab Bar + FAB)

Use Material 3 `NavigationBar`. MyFitnessPal's iOS tab bar gains `.regularMaterial` blur on scroll; Android has no first-class live blur, so use a 92%-opaque white surface with a 0.5dp top divider. Five tabs, **active tint Lake Blue**. The Diary tab is special — a 56.dp Lake Blue FAB floats *centered above* the tab bar.

```kotlin
import androidx.compose.material.icons.filled.GridView
import androidx.compose.material.icons.filled.MenuBook
import androidx.compose.material.icons.filled.Assignment
import androidx.compose.material.icons.filled.Groups
import androidx.compose.material.icons.filled.MoreHoriz
import androidx.compose.material3.*

@Composable
fun MfpScaffold(
    selected: Int,
    onSelect: (Int) -> Unit,
    onFab: () -> Unit,
    content: @Composable (PaddingValues) -> Unit,
) {
    val items = listOf(
        "Dashboard" to Icons.Filled.GridView,
        "Diary"     to Icons.Filled.MenuBook,
        "Plans"     to Icons.Filled.Assignment,
        "Community" to Icons.Filled.Groups,
        "More"      to Icons.Filled.MoreHoriz,
    )
    val haptics = LocalHapticFeedback.current

    Scaffold(
        bottomBar = {
            NavigationBar(
                containerColor = MfpColors.Canvas.copy(alpha = 0.92f),
                tonalElevation = 0.dp,
            ) {
                items.forEachIndexed { i, (label, icon) ->
                    NavigationBarItem(
                        selected = selected == i,
                        onClick = {
                            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                            onSelect(i)
                        },
                        icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                        label = { Text(label, style = MfpText.Tab) },
                        colors = NavigationBarItemDefaults.colors(
                            selectedIconColor   = MfpColors.LakeBlue,
                            selectedTextColor   = MfpColors.LakeBlue,
                            unselectedIconColor = MfpColors.Mute,
                            unselectedTextColor = MfpColors.Mute,
                            indicatorColor      = Color.Transparent, // MFP has no Material pill
                        ),
                    )
                }
            }
        },
        floatingActionButton = {
            // Only show the FAB on the Diary tab; it sits centered above the bar
            if (selected == 1) DiaryFAB(onClick = onFab)
        },
        floatingActionButtonPosition = FabPosition.Center,
        content = content,
    )
}
```

The FAB tap opens an Add-Food bottom sheet (`ModalBottomSheet`) with Scan Barcode / Search Food / Quick Add / Recent / My Foods. The barcode scanner is a full-screen `Surface(color = Color.Black)` with four white 3.dp L-bracket corners (drawn via `Canvas`) framing a 280×180.dp window and a 1.dp red scan line pulsing via `rememberInfiniteTransition`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Calorie ring fill | `Animatable.animateTo(target, tween(600))` on the swept arc + `AnimatedContent` slide/fade number flip |
| Food row press | background `Canvas` → `SurfaceGray` over ~100ms (no scale) |
| FAB tap | `animateFloatAsState` 1 → 0.92 → 1 spring ~250ms + `LongPress` (medium) haptic |
| Add-Food sheet | `ModalBottomSheet` slide-up ~350ms; scrim `rgba(0,0,0,0.4)` |
| Barcode laser | `rememberInfiniteTransition` alpha 0.4 → 1.0 → 0.4 every 1200ms; green tick replaces it 800ms on capture |
| Streak confetti | only at 7/30/100/365 milestones — particle burst ~1.4s + success haptic |
| Tab switch | `TextHandleMove` haptic; no animation |

```kotlin
import androidx.compose.animation.core.rememberInfiniteTransition
import androidx.compose.animation.core.infiniteRepeatable
import androidx.compose.animation.core.RepeatMode
import androidx.compose.animation.core.animateFloat

// Barcode scan-line pulse
@Composable
fun BarcodeLaser(modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "laser")
    val alpha by t.animateFloat(
        0.4f, 1f,
        infiniteRepeatable(tween(1200), RepeatMode.Reverse),
        label = "laserAlpha",
    )
    Box(
        modifier
            .fillMaxWidth()
            .height(1.dp)
            .background(MfpColors.OverLimit.copy(alpha = alpha)),
    )
}
```

Haptics: `LocalHapticFeedback` — `TextHandleMove` ≈ iOS `.light`/`.selection` (primary CTA, tab switch), `LongPress` ≈ `.medium`/`.success` (FAB, Complete Diary, barcode capture, streak milestone). For a closer match to iOS impact use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(12, 100)`.

## 7. Icons

The SF Symbols map to Material Icons (`androidx.compose.material:material-icons-extended`). The M-square logomark is a custom asset — ship as a vector drawable.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| FAB add | `plus` | `Icons.Filled.Add` |
| Add-food row | `plus.circle.fill` | `Icons.Filled.AddCircle` |
| Barcode scan | `barcode.viewfinder` | `Icons.Filled.QrCodeScanner` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Streak | `flame.fill` | `Icons.Filled.LocalFireDepartment` (Streak Orange) |
| Steps | `figure.walk` | `Icons.Filled.DirectionsWalk` |
| Weight | `scalemass` | `Icons.Filled.MonitorWeight` |
| Water | `drop.fill` | `Icons.Filled.WaterDrop` |
| Dashboard tab | `square.grid.2x2` / `.fill` | `Icons.Filled.GridView` |
| Diary tab | `book.closed` / `.fill` | `Icons.Filled.MenuBook` |
| Plans tab | `list.clipboard` | `Icons.Filled.Assignment` |
| Community tab | `person.3` / `.fill` | `Icons.Filled.Groups` |
| More tab | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Premium gate | `lock.fill` | `Icons.Filled.Lock` (Premium Gold) |
| Chevron (row) | `chevron.right` | `Icons.Filled.ChevronRight` |
| Date picker (Diary) | `chevron.down` | `Icons.Filled.ExpandMore` |

The MFP heritage mark (rounded `#005DAA` square with a white serif "M") has no Material equivalent — bundle it as a vector drawable and render with `Image(painterResource(...))`. Never recolor it to Lake Blue; it stays heritage MFP Blue `#005DAA`.

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `AnimatedContent` number flip, `rememberInfiniteTransition`, modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The white canvas wants dark-content system bars (`isAppearanceLightStatusBars = true`); the blue-black dark mode flips to light-content. The large nav title starts at safe-area top + 16.dp; `Scaffold` insets keep the tab bar + FAB clear of the gesture nav (FAB ~88.dp above the bottom safe area on Diary).
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on food names, meal titles, body. **Pin** layout-sensitive numbers (the 56.sp calorie hero, macro numbers, 10.sp tab labels, streak badge) by wrapping in `CompositionLocalProvider(LocalDensity provides Density(LocalDensity.current.density, fontScale = 1f))` — the ring layout breaks if the giant number scales.
- **Tabular figures**: `fontFeatureSettings = "tnum"` is baked into every numeric `TextStyle` (`CalorieHero`, `Calorie`, `MacroNumber`, `StreakBadge`) — this is the Compose equivalent of iOS `.monospacedDigit()` and is **non-negotiable**: non-aligned digits make the giant number look broken.
- **TalkBack**: read the calorie ring as one element — `Modifier.semantics { contentDescription = "1,247 calories remaining out of a 2,000 calorie goal; 753 consumed, 0 from exercise" }`. Read the macro donut as "187 grams of carbs (65 percent), 64 grams of fat (88 percent), 92 grams of protein (74 percent)". Group food rows with `Modifier.semantics(mergeDescendants = true)`.
- **Touch targets**: Material guidance is 48.dp minimum. The FAB (56.dp) clears it; the 64.dp food row and 56.dp Add-Food row are generous; the 12.dp row chevron gets a 48.dp hit area via the full-row clickable. Tab items fill the 56.dp bar row.
- **Contrast**: Ink `#1F2937` on white meets WCAG AAA at all sizes; Slate `#4B5563` meets AA at 14sp+; Mute `#9CA3AF` is placeholder/disabled only. Lake Blue `#0072CE` on white passes AA for 16sp SemiBold — do not use a brighter blue on light mode (it loses contrast).
- **Reduce Motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, snap the ring fill instantly (skip the `Animatable` tween), drop the number-flip to an instant swap, and disable streak confetti.
- **Material You**: do **not** enable `dynamicColorScheme()` — the macro trio (orange/purple/green) and the semantic ring colors are fixed brand language and must never bend to wallpaper. Dark mode is the deliberate blue-black `#0F1419`; Lake Blue lifts to `#3B8FDF` for OLED legibility.
