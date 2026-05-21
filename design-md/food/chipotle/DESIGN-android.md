# Chipotle (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Chipotle's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Chipotle's cream kraft canvas, single brick-red accent, ALL-CAPS condensed headers, the build-your-burrito ingredient stepper, the rewards ring) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `dp`/`sp` instead of `pt`, Compose haptics instead of `UIImpactFeedbackGenerator`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/ChipotleColors.kt
import androidx.compose.ui.graphics.Color

object ChipotleColors {
    // Canvas & Surfaces
    val Cream     = Color(0xFFFFF5E1)
    val Surface   = Color(0xFFFFFFFF)
    val CreamDeep = Color(0xFFF4E8D0)
    val Divider   = Color(0xFFE8DCC4)

    // Text (brown, never black)
    val TextPrimary   = Color(0xFF451400)
    val TextSecondary = Color(0xFF8A6B4F)
    val TextTertiary  = Color(0xFFB49A82)

    // Brand
    val Red        = Color(0xFFA81612)
    val RedPressed = Color(0xFF8C1210)
    val RedTint    = Color(0xFFF4E2E1)

    // Support / Semantic
    val Tan     = Color(0xFFAC8C5B)
    val Success = Color(0xFF3C7A3C)
    val Spice   = Color(0xFFE07B26)
}
```

Wire it into a Material 3 `lightColorScheme` so ripples, dividers, and default component colors inherit the brand. Chipotle is light-only by design; do not provide a dark scheme unless mirroring system dark with identical brand colors.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val ChipotleScheme = lightColorScheme(
    primary          = ChipotleColors.Red,
    onPrimary        = Color.White,
    background        = ChipotleColors.Cream,
    onBackground      = ChipotleColors.TextPrimary,
    surface           = ChipotleColors.Surface,
    onSurface         = ChipotleColors.TextPrimary,
    surfaceVariant    = ChipotleColors.CreamDeep,
    onSurfaceVariant  = ChipotleColors.TextSecondary,
    outline           = ChipotleColors.Divider,
    error             = ChipotleColors.Red,
)

@Composable
fun ChipotleTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = ChipotleScheme, typography = ChipotleTypography, content = content)
```

## 2. Typography

Headers use Archivo (ALL CAPS); body uses a calm sans (Avenir is iOS-only — substitute the bundled brand body face or fall back to Roboto). Drop the TTFs in `res/font/` (lowercase, snake_case).

```kotlin
// ui/theme/ChipotleType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextStyle as _
import androidx.compose.ui.unit.sp

val Archivo = FontFamily(
    Font(R.font.archivo_bold,      FontWeight.Bold),       // 700
    Font(R.font.archivo_extrabold, FontWeight.ExtraBold),  // 800
)
val BodySans = FontFamily(
    Font(R.font.body_regular, FontWeight.Normal),  // 400 (Avenir substitute)
    Font(R.font.body_bold,    FontWeight.Bold),    // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1). Apply ALL CAPS at call site.
object ChipotleText {
    val Hero        = TextStyle(Archivo,  fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 34.sp, letterSpacing = 0.4.sp)
    val ScreenTitle = TextStyle(Archivo,  fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 29.sp, letterSpacing = 0.3.sp)
    val Section     = TextStyle(Archivo,  fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 21.sp, letterSpacing = 0.6.sp)
    val Button      = TextStyle(Archivo,  fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 18.sp, letterSpacing = 0.8.sp)
    val Points      = TextStyle(Archivo,  fontWeight = FontWeight.ExtraBold, fontSize = 34.sp, lineHeight = 36.sp)
    val Badge       = TextStyle(Archivo,  fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)

    val ItemName = TextStyle(BodySans, fontWeight = FontWeight.Bold,   fontSize = 17.sp, lineHeight = 22.sp)
    val Price    = TextStyle(BodySans, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 19.sp)
    val Body     = TextStyle(BodySans, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 22.sp)
    val Meta     = TextStyle(BodySans, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val Tab      = TextStyle(BodySans, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
    val Caption  = TextStyle(BodySans, fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val ChipotleTypography = Typography(
    headlineLarge = ChipotleText.ScreenTitle,
    headlineSmall = ChipotleText.Section,
    titleMedium   = ChipotleText.ItemName,
    bodyMedium    = ChipotleText.Body,
    labelSmall    = ChipotleText.Tab,
)
```

Render prices, calories, and points with tabular figures via `TextStyle(fontFeatureSettings = "tnum")`. Apply ALL CAPS with `text = title.uppercase()`.

## 3. Signature Components

### Section Header (ALL CAPS)

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

@Composable
fun SectionHeader(title: String, modifier: Modifier = Modifier) {
    Column(modifier.padding(top = 24.dp)) {
        Text(title.uppercase(), style = ChipotleText.Section, color = ChipotleColors.TextPrimary)
        Spacer(Modifier.height(8.dp))
        HorizontalDivider(color = ChipotleColors.Divider, thickness = 1.dp)
    }
}
```

### Build-Your-Burrito Ingredient Row

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.scaleIn
import androidx.compose.animation.scaleOut
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun IngredientRow(
    name: String,
    meta: String?,
    selected: Boolean,
    multiSelect: Boolean = false,
    onClick: () -> Unit,
) {
    val haptics = LocalHapticFeedback.current
    val shape = if (multiSelect) RoundedCornerShape(6.dp) else CircleShape
    val size = if (multiSelect) 26.dp else 28.dp

    Row(
        Modifier
            .fillMaxWidth()
            .heightIn(min = 60.dp)
            .background(if (selected) ChipotleColors.RedTint else ChipotleColors.Surface)
            .then(if (selected) Modifier.drawLeftRule(ChipotleColors.Red) else Modifier)
            .clickable {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                onClick()
            }
            .padding(horizontal = 16.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Column(Modifier.weight(1f)) {
            Text(name, style = ChipotleText.ItemName, color = ChipotleColors.TextPrimary)
            if (meta != null) Text(meta, style = ChipotleText.Meta, color = ChipotleColors.TextSecondary)
        }
        Box(
            Modifier
                .size(size)
                .clip(shape)
                .background(if (selected) ChipotleColors.Red else Color.Transparent)
                .border(2.dp, if (selected) ChipotleColors.Red else ChipotleColors.Divider, shape),
            contentAlignment = Alignment.Center,
        ) {
            AnimatedVisibility(selected, enter = scaleIn(), exit = scaleOut()) {
                Icon(Icons.Filled.Check, contentDescription = null, tint = Color.White,
                    modifier = Modifier.size(16.dp))
            }
        }
    }
}

// Small modifier: draw a 1.5dp red rule on the leading edge of a selected row
fun Modifier.drawLeftRule(color: Color) = drawBehind {
    drawRect(color, size = androidx.compose.ui.geometry.Size(1.5.dp.toPx(), size.height))
}
```

### Primary CTA (ALL CAPS)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.draw.scale

@Composable
fun ChipotleCTA(label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f,
        spring(dampingRatio = 0.8f), label = "ctaScale")
    Box(
        modifier
            .fillMaxWidth()
            .height(54.dp)
            .scale(scale)
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) ChipotleColors.RedPressed else ChipotleColors.Red)
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text(label.uppercase(), style = ChipotleText.Button, color = Color.White)
    }
}
```

### Rewards Points Ring

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke

@Composable
fun RewardsRing(points: Int, goal: Int, modifier: Modifier = Modifier) {
    val target = (points.toFloat() / goal).coerceAtMost(1f)
    val sweep = remember { Animatable(0f) }
    LaunchedEffect(target) { sweep.animateTo(target, tween(700, easing = FastOutSlowInEasing)) }

    Box(modifier.size(120.dp), contentAlignment = Alignment.Center) {
        Canvas(Modifier.fillMaxSize()) {
            val sw = 10.dp.toPx()
            drawArc(ChipotleColors.Divider, 0f, 360f, false,
                style = Stroke(sw))
            drawArc(ChipotleColors.Red, -90f, 360f * sweep.value, false,
                style = Stroke(sw, cap = StrokeCap.Round))
        }
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Text("%,d".format(points),
                style = ChipotleText.Points.copy(fontFeatureSettings = "tnum"),
                color = ChipotleColors.Red)
            Text("POINTS", style = ChipotleText.Badge, color = ChipotleColors.TextSecondary)
        }
    }
}
```

### Step Progress Bar

```kotlin
@Composable
fun StepProgress(total: Int, current: Int, modifier: Modifier = Modifier) {
    Row(
        modifier.fillMaxWidth().padding(horizontal = 16.dp),
        horizontalArrangement = Arrangement.spacedBy(2.dp),
    ) {
        repeat(total) { i ->
            val color by animateColorAsState(
                if (i <= current) ChipotleColors.Red else ChipotleColors.Divider,
                tween(250), label = "seg$i",
            )
            Box(
                Modifier
                    .weight(1f)
                    .height(4.dp)
                    .clip(CircleShape)
                    .background(color),
            )
        }
    }
}
```

## 4. Build-Flow State

```kotlin
data class OrderBuild(
    val rice: String? = null,             // single-select
    val beans: String? = null,            // single-select
    val protein: String? = null,          // single-select
    val toppings: Set<String> = emptySet(), // multi-select
    val salsas: Set<String> = emptySet(),   // multi-select
    val extras: Set<String> = emptySet(),   // each adds a "+$2.00" meta tag
) {
    val runningTotal: Double get() = 7.65 + extras.size * 2.0  // base + extras
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Chipotle's iOS tab bar is an opaque white surface with a hairline divider and a **red active tint**.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun ChipotleBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = ChipotleColors.Surface, tonalElevation = 0.dp) {
        val items = listOf(
            "Order"    to Icons.Filled.Restaurant,
            "Scan"     to Icons.Filled.QrCode,
            "Delivery" to Icons.Filled.DirectionsBike,
            "More"     to Icons.Filled.MoreHoriz,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label.uppercase(), style = ChipotleText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = ChipotleColors.Red,
                    selectedTextColor = ChipotleColors.Red,
                    unselectedIconColor = ChipotleColors.TextSecondary,
                    unselectedTextColor = ChipotleColors.TextSecondary,
                    indicatorColor = ChipotleColors.RedTint,
                ),
            )
        }
    }
}
```

The persistent **"ADD TO BAG" footer** sits above the bottom bar during the build flow — render `ChipotleCTA` (with the running total appended) in the `Scaffold` `bottomBar` slot stacked above `ChipotleBottomBar`, on a `ChipotleColors.Surface` band with a top hairline.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Ingredient select | check `AnimatedVisibility` `scaleIn()/scaleOut()`; row bg crossfade; `HapticFeedbackType.TextHandleMove` |
| Step progress slide | `animateColorAsState(tween(250))` per segment as `current` advances |
| Rewards ring fill | `Animatable` 0 → target `tween(700, FastOutSlowInEasing)` driving `drawArc` sweep; number counts |
| Add to Bag | CTA press `Modifier.scale` 1 → 0.98; bag badge `spring` 1 → 1.2 → 1; success haptic |
| Card tap | `Modifier.scale` 1 → 0.98 via `collectIsPressedAsState()` |
| Sheet present | `ModalBottomSheet` default slide; scrim `ChipotleColors.TextPrimary.copy(alpha = 0.40f)` |

```kotlin
// Bag-count badge bounce after add
val badge = remember { Animatable(1f) }
LaunchedEffect(bagCount) {
    if (bagCount > 0) { badge.animateTo(1.2f, spring(dampingRatio = 0.5f)); badge.animateTo(1f, spring()) }
}
```

Haptics: prefer `LocalHapticFeedback`. For a softer "select" tick on ingredient taps, `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` (API 30+) approximates iOS `.light` impact.

## 7. Icons

Chipotle ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Chipotle's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Selected check | `checkmark` | `Icons.Filled.Check` |
| Order (tab) | `fork.knife` | `Icons.Filled.Restaurant` |
| Scan (tab) | `qrcode` | `Icons.Filled.QrCode` |
| Delivery (tab) | `bicycle` | `Icons.Filled.DirectionsBike` |
| More (tab) | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Bag | `bag` / `bag.fill` | `Icons.Filled.ShoppingBag` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Location chevron | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Quantity minus / plus | `minus` / `plus` | `Icons.Filled.Remove` / `Icons.Filled.Add` |
| Rewards / star | `star.fill` | `Icons.Filled.Star` |
| Spicy | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Back | `chevron.left` | `Icons.Filled.ArrowBackIosNew` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + drawArc are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the cream canvas wants dark system-bar icons via `WindowCompat`. Apply `Scaffold` insets so the "ADD TO BAG" footer clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on item names and body. ALL-CAPS condensed headers absorb growth; pin tab labels and step segments via `dp`-derived sizing or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: ingredient rows use `Modifier.semantics { role = Role.Checkbox /* or RadioButton */; stateDescription = if (selected) "Selected" else "Not selected" }`; merge the name + meta; the CTA announces "Add to bag, total $X".
- **Touch targets**: Material guidance is 48.dp minimum. Ingredient rows are 60.dp (clear); ensure the quantity − / + glyphs get 48.dp hit areas via padding.
- **Contrast**: espresso `#451400` on cream `#FFF5E1` is very high contrast. `#8A6B4F` on cream passes WCAG AA at 13sp+ — validate 11sp captions and darken toward `#6E5238` for strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Chipotle's brand requires the fixed cream canvas, brick-red accent, and brown text regardless of wallpaper.
