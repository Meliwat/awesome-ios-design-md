# Cal AI (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Cal AI's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the macro ring, the white capture FAB, and the AI tri-color processing sweep.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Cal AI's near-black OLED canvas, the desaturated macro trio, oversized tabular numerals, the white-on-black CTA, the AI gradient sweep) while making everything idiomatic Android — `drawArc` for macro rings, a custom raised FAB in the `NavigationBar` slot, a `Brush.linearGradient` mask animation for the AI bar, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for meal photos. No color extraction, so no `androidx.palette`.

## 1. Color Tokens

```kotlin
// ui/theme/CalColors.kt
import androidx.compose.ui.graphics.Color

object CalColors {
    // Canvas & Surfaces (dark — primary)
    val Canvas    = Color(0xFF0A0A0A) // near-black, NOT pure #000 — softer on OLED
    val Surface1  = Color(0xFF151515) // meal cards, hero, grouped rows
    val Surface2  = Color(0xFF1E1E1E) // sheets, modals, input fills
    val Surface3  = Color(0xFF2A2A2A) // pressed states, ring track
    val Divider   = Color(0xFF252525) // hairline dividers

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFA1A1A1) // "1 serving", timestamps, macro labels
    val TextTertiary  = Color(0xFF6E6E6E) // placeholder, disabled

    // Macro accents (the ONLY color story — desaturated pastels)
    val Protein = Color(0xFF4DA8FF) // soft blue
    val Carbs   = Color(0xFFFFB54C) // soft amber
    val Fat     = Color(0xFFFF6E87) // soft pink

    // Action & Semantic
    val ActionWhite = Color(0xFFFFFFFF) // primary CTA background
    val ActionText  = Color(0xFF000000) // black text on the white CTA — intentional
    val Success     = Color(0xFF42E17D) // logged-confirmed check
    val Warning     = Color(0xFFFFB54C) // over-budget — soft amber, NEVER red
    val Destructive = Color(0xFFFF5A5A) // delete / clear-all only
    val AIAccent    = Color(0xFF8B8DF5) // AI-confidence indigo

    // Light mode (secondary — rare)
    val LightCanvas   = Color(0xFFFFFFFF)
    val LightSurface1 = Color(0xFFF5F5F5)
    val LightText     = Color(0xFF0A0A0A)
}
```

Wire it into a Material 3 `darkColorScheme` — Cal AI is dark-first; light mode is a secondary experience and reuses the identical macro hexes.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val CalScheme = darkColorScheme(
    primary        = CalColors.ActionWhite,   // CTA is white, not a brand hue
    onPrimary      = CalColors.ActionText,    // black on white
    background     = CalColors.Canvas,
    onBackground   = CalColors.TextPrimary,
    surface        = CalColors.Surface1,
    onSurface      = CalColors.TextPrimary,
    surfaceVariant = CalColors.Surface2,
    outline        = CalColors.Divider,
    error          = CalColors.Destructive,
)

@Composable
fun CalTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = CalScheme, typography = CalTypography, content = content)
```

## 2. Typography

Cal AI uses a clean modern sans (Inter, or SF Pro on iOS). Drop Inter's TTFs in `res/font/` (lowercase, snake_case). Roboto is an acceptable system fallback but Inter is the visual sibling — bundle it. **No light weights** — the floor is 400; thin weights vanish on the near-black canvas.

```kotlin
// ui/theme/CalType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_medium,   FontWeight.Medium),   // 500
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

// Every numeral display must be tabular — prevents "5 → 50" layout jitter.
private const val TNUM = "tnum"

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, same weights/tracking)
object CalText {
    val Hero        = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 72.sp, lineHeight = 72.sp, letterSpacing = (-1.5).sp, fontFeatureSettings = TNUM)
    val MealCals    = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 40.sp, lineHeight = 40.sp, letterSpacing = (-0.8).sp, fontFeatureSettings = TNUM)
    val Streak      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 48.sp, lineHeight = 48.sp, letterSpacing = (-1.0).sp, fontFeatureSettings = TNUM)
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Nav         = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val MealName    = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.15).sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val MacroChip   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 14.sp, fontFeatureSettings = TNUM)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 17.sp, fontFeatureSettings = TNUM)
    val LabelUpper  = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.8.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = (-0.1).sp)
    val ButtonSec   = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val CalTypography = Typography(
    displayLarge  = CalText.Hero,
    headlineLarge = CalText.ScreenTitle,
    titleLarge    = CalText.Section,
    titleMedium   = CalText.MealName,
    bodyMedium    = CalText.Body,
    labelSmall    = CalText.Tab,
)
```

The `tnum` feature is baked into every numeral style above — this is critical for calorie/macro/weight readouts that update live.

## 3. Signature Components

### Primary CTA (White Pill)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun CalPrimaryButton(
    title: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        targetValue = if (pressed) 0.97f else 1f,
        animationSpec = spring(dampingRatio = 0.7f, stiffness = 700f),
        label = "ctaScale",
    )
    Box(
        modifier = modifier
            .fillMaxWidth()
            .scale(scale)
            .clip(CircleShape) // 28dp pill
            .background(
                when {
                    !enabled -> CalColors.Surface3
                    pressed  -> Color(0xFFE8E8E8)
                    else     -> CalColors.ActionWhite
                }
            )
            .clickable(interaction, indication = null, enabled = enabled, onClick = onClick)
            .padding(vertical = 16.dp, horizontal = 28.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(
            title,
            style = CalText.Button,
            color = if (enabled) CalColors.ActionText else CalColors.TextTertiary, // black on white
        )
    }
}
```

### Capture FAB (the signature element)

A 68dp white circle with a 2dp canvas-colored inner ring and a soft white glow — the only "active light source" on the dark canvas.

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.draw.shadow

@Composable
fun CaptureFAB(onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        targetValue = if (pressed) 0.92f else 1f,
        animationSpec = spring(dampingRatio = 0.7f, stiffness = 600f),
        label = "fabScale",
    )
    val haptics = LocalHapticFeedback.current
    Box(
        modifier = modifier
            .size(68.dp)
            .scale(scale)
            // White glow on dark canvas — Android has no colored blur; a white ambient
            // + spot shadow approximates rgba(255,255,255,0.15) 0 0 40px.
            .shadow(40.dp, CircleShape, ambientColor = Color.White.copy(alpha = 0.15f),
                spotColor = Color.White.copy(alpha = 0.15f))
            .clip(CircleShape)
            .background(CalColors.ActionWhite)
            .border(2.dp, CalColors.Canvas, CircleShape) // ≈ inset inner ring
            .padding(4.dp)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .impact(.medium)
                onClick()
            },
    )
}
```

### Macro Ring

iOS uses `Circle().trim(...)`; Compose uses `Canvas` + `drawArc`. Track is `Surface3`, fill is the macro color with a round cap, sweeping from -90° (12 o'clock).

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.animation.core.tween
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke

enum class Macro(val color: Color, val label: String) {
    Protein(CalColors.Protein, "PROTEIN"),
    Carbs(CalColors.Carbs, "CARBS"),
    Fat(CalColors.Fat, "FAT"),
}

@Composable
fun MacroRing(
    macro: Macro,
    current: Float,
    target: Float,
    modifier: Modifier = Modifier,
    size: androidx.compose.ui.unit.Dp = 80.dp,
) {
    val progress = (current / target.coerceAtLeast(0.001f)).coerceIn(0f, 1f)
    val animated by androidx.compose.animation.core.animateFloatAsState(
        targetValue = progress,
        animationSpec = tween(600), // 0° → target % over 600ms ease-out
        label = "ringFill",
    )
    Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(6.dp)) {
        Box(Modifier.size(size), contentAlignment = Alignment.Center) {
            Canvas(Modifier.fillMaxSize()) {
                val stroke = 6.dp.toPx()
                val inset = stroke / 2f
                val arcSize = Size(size.toPx() - stroke, size.toPx() - stroke)
                drawArc(
                    color = CalColors.Surface3,
                    startAngle = 0f, sweepAngle = 360f, useCenter = false,
                    topLeft = Offset(inset, inset), size = arcSize,
                    style = Stroke(width = stroke),
                )
                drawArc(
                    color = macro.color,
                    startAngle = -90f, sweepAngle = 360f * animated, useCenter = false,
                    topLeft = Offset(inset, inset), size = arcSize,
                    style = Stroke(width = stroke, cap = StrokeCap.Round),
                )
            }
            Row(verticalAlignment = Alignment.Bottom) {
                Text("${current.toInt()}",
                    style = CalText.MacroChip.copy(fontSize = 18.sp, fontWeight = FontWeight.Bold),
                    color = CalColors.TextPrimary)
                Text("g", style = CalText.Meta.copy(fontSize = 9.sp), color = CalColors.TextSecondary)
            }
        }
        Text(macro.label, style = CalText.LabelUpper, color = CalColors.TextSecondary)
    }
}
```

### Today Hero Card

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape

@Composable
fun TodayHeroCard(
    remaining: Int,
    goal: Int,
    protein: Pair<Float, Float>,
    carbs: Pair<Float, Float>,
    fat: Pair<Float, Float>,
    modifier: Modifier = Modifier,
) {
    val fill = ((goal - remaining).toFloat() / goal.coerceAtLeast(1)).coerceIn(0f, 1f)
    Column(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(24.dp))
            .background(CalColors.Surface1)
            .padding(24.dp),
    ) {
        Text("TODAY · REMAINING", style = CalText.LabelUpper, color = CalColors.TextSecondary)
        Spacer(Modifier.height(6.dp))
        Text("%,d".format(remaining), style = CalText.Hero, color = CalColors.TextPrimary)
        Spacer(Modifier.height(4.dp))
        Text("kcal of %,d goal".format(goal), style = CalText.Meta, color = CalColors.TextSecondary)
        Spacer(Modifier.height(20.dp))
        // Progress bar — 6dp, rounded, white fill (no shadow; surface contrast only)
        Box(
            Modifier.fillMaxWidth().height(6.dp).clip(CircleShape).background(CalColors.Surface3),
        ) {
            Box(Modifier.fillMaxWidth(fill).fillMaxHeight().clip(CircleShape).background(Color.White))
        }
        Spacer(Modifier.height(20.dp))
        Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            MacroRing(Macro.Protein, protein.first, protein.second, Modifier.weight(1f))
            MacroRing(Macro.Carbs, carbs.first, carbs.second, Modifier.weight(1f))
            MacroRing(Macro.Fat, fat.first, fat.second, Modifier.weight(1f))
        }
    }
}
```

### Meal Card (Photo + Stats)

```kotlin
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun MealCard(
    title: String,
    time: String,
    servings: String,
    protein: Int,
    carbs: Int,
    fat: Int,
    calories: Int,
    photoUrl: String,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(20.dp))
            .background(CalColors.Surface1), // elevation = surface contrast, NO shadow
    ) {
        Box {
            AsyncImage(
                model = photoUrl,
                contentDescription = title,
                modifier = Modifier
                    .fillMaxWidth()
                    .aspectRatio(16f / 10f)
                    .clip(RoundedCornerShape(topStart = 20.dp, topEnd = 20.dp)),
                contentScale = ContentScale.Crop,
            )
            Box(
                Modifier
                    .align(Alignment.BottomCenter)
                    .fillMaxWidth()
                    .height(60.dp)
                    .background(Brush.verticalGradient(listOf(Color.Transparent, Color.Black.copy(alpha = 0.4f))))
            )
        }
        Column(Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(10.dp)) {
            Column(verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(title, style = CalText.MealName, color = CalColors.TextPrimary)
                Text("$time · $servings", style = CalText.Meta, color = CalColors.TextSecondary)
            }
            Row(verticalAlignment = Alignment.CenterVertically) {
                Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                    MacroChip("P", protein, CalColors.Protein)
                    MacroChip("C", carbs, CalColors.Carbs)
                    MacroChip("F", fat, CalColors.Fat)
                }
                Spacer(Modifier.weight(1f))
                Text("$calories", style = CalText.MealCals, color = CalColors.TextPrimary)
            }
        }
    }
}

@Composable
private fun MacroChip(label: String, value: Int, color: Color) {
    Box(
        Modifier.clip(CircleShape).background(color.copy(alpha = 0.18f))
            .padding(horizontal = 8.dp, vertical = 3.dp),
    ) { Text("$label $value", style = CalText.MacroChip, color = color) }
}
```

### AI Detection Result Card

The AI card is distinguished by a 135° `Surface2 → Surface3` gradient wash plus the tri-color accent line on its top edge.

```kotlin
@Composable
fun AIDetectionCard(
    title: String,
    items: List<Pair<String, Int>>, // name to confidence%
    onSave: () -> Unit,
    onReject: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Box(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(20.dp))
            .background(
                Brush.linearGradient(listOf(CalColors.Surface2, CalColors.Surface3))
            ),
    ) {
        // 2dp tri-color accent line at the top edge
        Box(
            Modifier
                .fillMaxWidth()
                .height(2.dp)
                .background(Brush.horizontalGradient(listOf(CalColors.Protein, CalColors.AIAccent, CalColors.Fat)))
        )
        Column(Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(12.dp)) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Icon(Icons.Filled.AutoAwesome, null, tint = CalColors.AIAccent, modifier = Modifier.size(11.dp))
                Text("AI DETECTED", style = CalText.LabelUpper, color = CalColors.AIAccent)
            }
            Text(title, style = CalText.MealName, color = CalColors.TextPrimary)
            Column(verticalArrangement = Arrangement.spacedBy(4.dp)) {
                items.forEach { (name, confidence) ->
                    Row(horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                        Text(name, style = CalText.Meta, color = CalColors.TextSecondary)
                        Text("$confidence%", style = CalText.Meta, color = CalColors.TextTertiary)
                    }
                }
            }
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(10.dp)) {
                Box(
                    Modifier.clip(CircleShape).background(CalColors.ActionWhite)
                        .clickable { onSave() }.padding(horizontal = 20.dp, vertical = 12.dp),
                ) { Text("Save to today", style = CalText.ButtonSec, color = CalColors.ActionText) }
                Text("Reject", style = CalText.ButtonSec, color = CalColors.Destructive,
                    modifier = Modifier.clickable { onReject() })
            }
        }
    }
}
```

## 4. AI Capture & Processing Flow (the distinctive system)

Cal AI's defining moment is the ~4-second capture → tri-color processing sweep → editable result card. The signature visual is the **2dp gradient bar** (`Protein → AIAccent → Fat`) that sweeps across while the model runs. Port the SwiftUI `AIProcessingBar` mask animation with an offset `Brush` window.

```kotlin
import androidx.compose.animation.core.RepeatMode
import androidx.compose.animation.core.infiniteRepeatable
import androidx.compose.animation.core.rememberInfiniteTransition

@Composable
fun AIProcessingBar(modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "aiSweep")
    val phase by t.animateFloat(
        initialValue = 0f, targetValue = 1f,
        animationSpec = infiniteRepeatable(tween(1200, easing = androidx.compose.animation.core.LinearEasing)),
        label = "phase",
    )
    Canvas(modifier.fillMaxWidth().height(2.dp)) {
        val w = size.width
        val windowW = w * 0.27f                 // ≈ 80dp window over ~300dp track
        val start = -windowW + phase * (w + windowW)
        drawRect(
            brush = Brush.horizontalGradient(
                colors = listOf(CalColors.Protein, CalColors.AIAccent, CalColors.Fat),
                startX = start, endX = start + windowW,
            ),
            size = size,
        )
    }
}

@Composable
fun CaptureScreen(onDetected: () -> Unit) {
    // Full-bleed CameraX preview goes here (PreviewView in an AndroidView).
    var processing by remember { mutableStateOf(false) }
    Box(Modifier.fillMaxSize().background(Color.Black)) {
        // 4-corner reticle centered, animates 1.0 → 1.05 → 1.0 on capture prep
        ReticleOverlay()
        if (processing) {
            // Viewfinder dims to 60% + "Detecting…" + sweeping bar
            Box(Modifier.fillMaxSize().background(Color.Black.copy(alpha = 0.6f))) {
                Column(
                    Modifier.align(Alignment.Center),
                    horizontalAlignment = Alignment.CenterHorizontally,
                    verticalArrangement = Arrangement.spacedBy(12.dp),
                ) {
                    Text("Detecting…", style = CalText.Body, color = CalColors.TextPrimary)
                    AIProcessingBar(Modifier.width(200.dp))
                }
            }
            LaunchedEffect(Unit) {
                kotlinx.coroutines.delay(1000) // 800–1200ms model latency
                onDetected() // then slide up the AIDetectionCard with spring(0.4, 0.7)
            }
        }
        CaptureFAB(
            onClick = { processing = true },
            modifier = Modifier.align(Alignment.BottomCenter).padding(bottom = 24.dp),
        )
    }
}

@Composable
private fun ReticleOverlay() {
    val t = rememberInfiniteTransition(label = "reticle")
    val s by t.animateFloat(1f, 1.05f, infiniteRepeatable(tween(900), RepeatMode.Reverse), label = "pulse")
    Box(Modifier.fillMaxSize(), contentAlignment = Alignment.Center) {
        Box(Modifier.size(220.dp).scale(s)) {
            // Draw 4 corner brackets (20dp, 2dp white stroke) at the box corners
            Canvas(Modifier.fillMaxSize()) {
                val len = 20.dp.toPx(); val sw = 2.dp.toPx(); val w = size.width; val h = size.height
                listOf(
                    Offset(0f, 0f) to listOf(Offset(len, 0f), Offset(0f, len)),
                    Offset(w, 0f) to listOf(Offset(w - len, 0f), Offset(w, len)),
                    Offset(0f, h) to listOf(Offset(len, h), Offset(0f, h - len)),
                    Offset(w, h) to listOf(Offset(w - len, h), Offset(w, h - len)),
                ).forEach { (c, ends) -> ends.forEach { drawLine(Color.White, c, it, sw) } }
            }
        }
    }
}
```

The result card slides up with `spring(dampingRatio = 0.7f, stiffness = StiffnessMediumLow)` (≈ response 0.4) once `onDetected` fires.

## 5. Navigation

Cal AI has 5 slots — Home / History / **[Capture FAB]** / Insights / Profile — with the FAB raised 10dp above the bar in the center slot. Material 3 `NavigationBar` has no raised-center affordance, so reserve the middle slot empty and overlay the FAB in a `Scaffold` `Box`. The iOS tab bar is `.regularMaterial`; Android has no live blur, so use a 92%-opaque canvas surface. The top nav is an opaque `#0A0A0A` large title (no blur).

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Home
import androidx.compose.material.icons.filled.CalendarMonth
import androidx.compose.material.icons.filled.QueryStats
import androidx.compose.material.icons.filled.Person
import androidx.compose.material3.*

@Composable
fun CalScaffold(selected: Int, onSelect: (Int) -> Unit, onCapture: () -> Unit, content: @Composable (PaddingValues) -> Unit) {
    Scaffold(
        containerColor = CalColors.Canvas,
        bottomBar = {
            Box(contentAlignment = Alignment.TopCenter) {
                NavigationBar(
                    containerColor = CalColors.Canvas.copy(alpha = 0.92f), // ≈ .regularMaterial
                    tonalElevation = 0.dp,
                ) {
                    val items = listOf(
                        "Home"     to Icons.Filled.Home,
                        "History"  to Icons.Filled.CalendarMonth,
                        null       to null, // reserved center slot for the FAB
                        "Insights" to Icons.Filled.QueryStats,
                        "Profile"  to Icons.Filled.Person,
                    )
                    items.forEachIndexed { i, (label, icon) ->
                        if (icon == null) {
                            NavigationBarItem(false, {}, icon = { Spacer(Modifier.size(22.dp)) }, enabled = false)
                        } else {
                            NavigationBarItem(
                                selected = selected == i,
                                onClick = { onSelect(i) },
                                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                                label = { Text(label!!, style = CalText.Tab) },
                                colors = NavigationBarItemDefaults.colors(
                                    selectedIconColor   = CalColors.TextPrimary,
                                    selectedTextColor   = CalColors.TextPrimary,
                                    unselectedIconColor = CalColors.TextTertiary,
                                    unselectedTextColor = CalColors.TextTertiary,
                                    indicatorColor      = Color.Transparent, // no Material pill
                                ),
                            )
                        }
                    }
                }
                CaptureFAB(onCapture, Modifier.offset(y = (-10).dp)) // raised above the bar
            }
        },
        content = content,
    )
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Capture tap | `animateFloatAsState` scale 1 → 0.92 `spring(dampingRatio = 0.7f)`; `HapticFeedbackType.LongPress` (.impact medium) |
| AI processing sweep | `rememberInfiniteTransition` phase 0→1 over 1200ms `LinearEasing`, offset gradient window |
| Result slide-up | `AnimatedVisibility` + `slideInVertically` with `spring(dampingRatio = 0.7f, stiffness = MediumLow)` (≈ response 0.4) |
| Ring fill | `animateFloatAsState` 0 → progress over 600ms `tween` |
| Log confirmation | 150ms white edge-flash via `Animatable` alpha; `HapticFeedbackType.Confirm` (success) |
| Streak increment | amber flame `Animatable` scale 1.0 → 1.15 → 1.0 over 600ms `spring(dampingRatio = 0.5f)`; success haptic |
| Swipe to delete | `SwipeToDismissBox` revealing a `Destructive` pill at -80dp threshold |

```kotlin
// Streak flame increment
@Composable
fun StreakFlame(triggerCount: Int) {
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(triggerCount) {
        if (triggerCount > 0) {
            haptics.performHapticFeedback(HapticFeedbackType.Confirm) // success-equivalent
            scale.animateTo(1.15f, spring(dampingRatio = 0.5f, stiffness = 400f))
            scale.animateTo(1f, spring(dampingRatio = 0.5f, stiffness = 400f))
        }
    }
    Icon(
        Icons.Filled.LocalFireDepartment, contentDescription = "Streak",
        tint = CalColors.Carbs, // amber flame
        modifier = Modifier.size(32.dp).scale(scale.value),
    )
}
```

Motion is deliberately restrained — no confetti, no mascot bounces, no >1.0 overshoots beyond the documented streak/reticle pulses. Honor **Reduce Motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, freeze the AI sweep and ring fill (snap to final), keep haptics. `HapticFeedbackType.Confirm` requires API 34+; gate it and fall back to `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` or a `Vibrator` `VibrationEffect.createOneShot(12, ...)` on older devices.

## 7. Icons

Cal AI uses a small set of SF Symbols; the closest first-party set is `androidx.compose.material:material-icons-extended`. The capture-ring glyph is bespoke (the FAB is a pure circle, no glyph) — no Material equivalent needed there.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Capture | `camera.fill` / custom ring | pure white circle (no glyph) — see `CaptureFAB` |
| AI sparkles | `sparkles` | `Icons.Filled.AutoAwesome` |
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| History (tab) | `calendar` | `Icons.Filled.CalendarMonth` |
| Insights (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.QueryStats` |
| Profile (tab) | `person` / `person.fill` | `Icons.Filled.Person` |
| Streak | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Add | `plus` | `Icons.Filled.Add` |
| Edit | `pencil` | `Icons.Filled.Edit` |
| Delete | `trash` | `Icons.Filled.Delete` |
| Barcode | `barcode.viewfinder` | `Icons.Filled.QrCodeScanner` |
| Library | `photo.on.rectangle` | `Icons.Filled.PhotoLibrary` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; CameraX + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. Note `HapticFeedbackType.Confirm` / `HapticFeedbackConstants.CONFIRM` are API 34+ — gate and fall back on older devices.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the near-black canvas wants `WindowCompat` light-content (white) system-bar icons. Use `Scaffold` insets so the capture FAB clears the gesture-nav indicator and the large title clears the status bar.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on body, titles, meal names. Display numerals must not break the hero layout: cap the 72sp `Hero` at ~140% and the 80dp ring numerals at 140% by wrapping those composables in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = fontScale.coerceAtMost(1.4f)))`.
- **TalkBack**: label each macro ring with `Modifier.semantics { contentDescription = "Protein, 112 of 140 grams, 80 percent" }`; merge the meal-card text with `Modifier.semantics(mergeDescendants = true)` and expose swipe-to-delete as a custom accessibility action. Announce AI confidence per item.
- **Touch targets**: Material guidance is 48.dp minimum. The 68dp capture FAB and 52dp CTAs clear it; ensure 32dp chip/stepper buttons are wrapped in 48dp hit areas via padding.
- **Contrast**: `#A1A1A1` on `#0A0A0A` passes WCAG AA at 13sp+. Validate `#6E6E6E` tertiary at the 11sp uppercase labels and bump toward `#808080` if targeting strict compliance. The desaturated macro accents on `#151515` pass for graphical (ring) use; do not use them for small body text.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Cal AI's identity is the fixed near-black canvas, the white CTA, and the specific desaturated macro trio regardless of wallpaper. (Material You suits Google/Material-first apps; Cal AI is a strong-brand exception.) Light mode is a secondary experience that reuses the identical macro hexes — swap only canvas/surface/text tokens.
