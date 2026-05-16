# Domino's (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Domino's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics — including the signature five-stage pizza tracker.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Domino's bright white canvas, red-action / blue-info split, the five-stage tracker, the build-your-pizza preview, the domino-square mark) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `dp`/`sp` instead of `pt`, Compose haptics instead of `UIImpactFeedbackGenerator`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/DominosColors.kt
import androidx.compose.ui.graphics.Color

object DominosColors {
    // Canvas & Surfaces
    val Canvas      = Color(0xFFFFFFFF)
    val Surface     = Color(0xFFF4F4F4)
    val SurfaceDeep = Color(0xFFEAEAEA)
    val Divider     = Color(0xFFE2E2E2)

    // Text
    val TextPrimary   = Color(0xFF1F1F1F)
    val TextSecondary = Color(0xFF6E6E6E)
    val TextTertiary  = Color(0xFF9A9A9A)

    // Brand
    val Red         = Color(0xFFE31837)
    val RedPressed  = Color(0xFFC2122E)
    val RedTint     = Color(0xFFFCE7EB)
    val Blue        = Color(0xFF006491)
    val BluePressed = Color(0xFF00547A)
    val BlueTint    = Color(0xFFE0EEF4)

    // Semantic
    val Success = Color(0xFF1E8E3E)
    val Warning = Color(0xFFF5A623)
}
```

Wire it into a Material 3 `lightColorScheme` so ripples, dividers, and default component colors inherit the brand. Domino's is light-only by design; do not provide a dark scheme unless mirroring system dark with identical brand colors.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val DominosScheme = lightColorScheme(
    primary          = DominosColors.Red,
    onPrimary        = Color.White,
    secondary         = DominosColors.Blue,
    onSecondary       = Color.White,
    background        = DominosColors.Canvas,
    onBackground      = DominosColors.TextPrimary,
    surface           = DominosColors.Canvas,
    onSurface         = DominosColors.TextPrimary,
    surfaceVariant    = DominosColors.Surface,
    onSurfaceVariant  = DominosColors.TextSecondary,
    outline           = DominosColors.Divider,
    error             = DominosColors.Red,
)

@Composable
fun DominosTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = DominosScheme, typography = DominosTypography, content = content)
```

## 2. Typography

Domino's uses Archivo. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto) — its grotesque tone is the closest free substitute.

```kotlin
// ui/theme/DominosType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Archivo = FontFamily(
    Font(R.font.archivo_regular, FontWeight.Normal), // 400
    Font(R.font.archivo_bold,    FontWeight.Bold),   // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object DominosText {
    val ScreenTitle  = TextStyle(Archivo, fontWeight = FontWeight.Bold,   fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val TrackerStage = TextStyle(Archivo, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Section      = TextStyle(Archivo, fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val ItemName     = TextStyle(Archivo, fontWeight = FontWeight.Bold,   fontSize = 17.sp, lineHeight = 22.sp)
    val Price        = TextStyle(Archivo, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 19.sp)
    val Body         = TextStyle(Archivo, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 22.sp)
    val Button       = TextStyle(Archivo, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = 0.3.sp)
    val DealPrice    = TextStyle(Archivo, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 30.sp, letterSpacing = (-0.4).sp)
    val Meta         = TextStyle(Archivo, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val Badge        = TextStyle(Archivo, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
    val Tab          = TextStyle(Archivo, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
    val Caption      = TextStyle(Archivo, fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val DominosTypography = Typography(
    headlineLarge = DominosText.ScreenTitle,
    headlineSmall = DominosText.Section,
    titleMedium   = DominosText.ItemName,
    bodyMedium    = DominosText.Body,
    labelSmall    = DominosText.Tab,
)
```

Render prices, ETAs, and deal callouts with tabular figures via `TextStyle(fontFeatureSettings = "tnum")`.

## 3. Signature Components

### Five-Stage Pizza Tracker

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp

enum class TrackerStage(val label: String, val headline: String) {
    Prep("Prep", "We're prepping your order"),
    Bake("Bake", "Your pizza is in the oven!"),
    Box("Box", "Boxing it up"),
    Quality("Quality Check", "Quality check in progress"),
    Out("Out for delivery", "Out for delivery!"),
}

@Composable
fun PizzaTracker(current: TrackerStage, eta: String, modifier: Modifier = Modifier) {
    val stages = TrackerStage.entries
    Column(
        modifier
            .fillMaxWidth()
            .shadow(14.dp, RoundedCornerShape(12.dp), spotColor = DominosColors.TextPrimary.copy(alpha = 0.12f))
            .clip(RoundedCornerShape(12.dp))
            .background(DominosColors.Canvas)
            .padding(16.dp),
    ) {
        Text(current.headline, style = DominosText.TrackerStage, color = DominosColors.TextPrimary)
        Spacer(Modifier.height(16.dp))
        Row(verticalAlignment = Alignment.CenterVertically) {
            stages.forEachIndexed { i, stage ->
                Node(stage, completed = i < current.ordinal, active = i == current.ordinal)
                if (i < stages.lastIndex) {
                    Box(
                        Modifier
                            .weight(1f)
                            .height(4.dp)
                            .background(
                                when {
                                    i < current.ordinal -> DominosColors.Blue
                                    i == current.ordinal -> DominosColors.Red
                                    else -> DominosColors.Divider
                                }
                            ),
                    )
                }
            }
        }
        Spacer(Modifier.height(16.dp))
        Text("Estimated delivery $eta", style = DominosText.Meta, color = DominosColors.TextSecondary)
        Spacer(Modifier.height(8.dp))
        Text("Track on the map",
            style = DominosText.Meta.copy(fontWeight = FontWeight.Bold), color = DominosColors.Blue)
    }
}

@Composable
private fun Node(stage: TrackerStage, completed: Boolean, active: Boolean) {
    val pulse = rememberInfiniteTransition(label = "pulse")
    val scale by pulse.animateFloat(
        1f, 1.06f,
        infiniteRepeatable(tween(800), RepeatMode.Reverse),
        label = "nodeScale",
    )
    val fill = when { completed -> DominosColors.Blue; active -> DominosColors.Red; else -> Color.Transparent }
    val stroke = when { completed -> DominosColors.Blue; active -> DominosColors.Red; else -> DominosColors.Divider }

    Column(horizontalAlignment = Alignment.CenterHorizontally, modifier = Modifier.width(56.dp)) {
        Box(
            Modifier
                .size(24.dp)
                .scale(if (active) scale else 1f)
                .clip(CircleShape)
                .background(fill)
                .then(Modifier.border(2.dp, stroke, CircleShape)),
            contentAlignment = Alignment.Center,
        ) {
            if (completed) Icon(Icons.Filled.Check, contentDescription = null,
                tint = Color.White, modifier = Modifier.size(13.dp))
        }
        Spacer(Modifier.height(6.dp))
        Text(
            stage.label, maxLines = 1,
            style = DominosText.Caption.copy(
                fontWeight = if (completed || active) FontWeight.Bold else FontWeight.Normal),
            color = when {
                active -> DominosColors.Red
                completed -> DominosColors.TextPrimary
                else -> DominosColors.TextTertiary
            },
        )
    }
}
```

### Primary CTA

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState

@Composable
fun DominosCTA(label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f,
        spring(dampingRatio = 0.8f), label = "ctaScale")
    Box(
        modifier
            .fillMaxWidth()
            .height(52.dp)
            .scale(scale)
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) DominosColors.RedPressed else DominosColors.Red)
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = DominosText.Button, color = Color.White)
    }
}
```

### Deal Card + Domino-Square Mark

```kotlin
@Composable
fun DominoMark(modifier: Modifier = Modifier) {
    Row(modifier.size(22.dp).clip(RoundedCornerShape(4.dp))) {
        Box(Modifier.weight(1f).fillMaxHeight().background(DominosColors.Red))
        Box(Modifier.width(1.dp).fillMaxHeight().background(Color.White))
        Box(Modifier.weight(1f).fillMaxHeight().background(DominosColors.Blue))
    }
}

@Composable
fun DealCard(price: String, name: String, finePrint: String, onAdd: () -> Unit) {
    Column(
        Modifier
            .fillMaxWidth()
            .height(150.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(DominosColors.Canvas)
            .border(2.dp, DominosColors.Red, RoundedCornerShape(8.dp))
            .padding(16.dp),
    ) {
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.CenterVertically) {
            Text(price, style = DominosText.DealPrice.copy(fontFeatureSettings = "tnum"),
                color = DominosColors.Red)
            DominoMark()
        }
        Spacer(Modifier.height(6.dp))
        Text(name, style = DominosText.ItemName, color = DominosColors.TextPrimary)
        Text(finePrint, style = DominosText.Meta, color = DominosColors.TextSecondary)
        Spacer(Modifier.weight(1f))
        Box(
            Modifier
                .align(Alignment.End)
                .clip(RoundedCornerShape(8.dp))
                .background(DominosColors.Red)
                .clickable(onClick = onAdd)
                .padding(horizontal = 16.dp, vertical = 8.dp),
        ) { Text("ADD DEAL", style = DominosText.Badge, color = Color.White) }
    }
}
```

### Build-Your-Pizza Live Preview

```kotlin
import androidx.compose.animation.core.animateDpAsState

@Composable
fun PizzaPreview(
    crustWidth: Dp,
    sauceColor: Color,
    hasCheese: Boolean,
    modifier: Modifier = Modifier,
) {
    val ring by animateDpAsState(crustWidth, tween(250), label = "crust")
    Box(modifier.size(160.dp), contentAlignment = Alignment.Center) {
        Box(Modifier.fillMaxSize().clip(CircleShape).background(Color(0xFFEDD9A8))
            .border(ring, Color(0xFFCC9E5C), CircleShape))
        Box(Modifier.padding(ring + 6.dp).fillMaxSize().clip(CircleShape).background(sauceColor))
        AnimatedVisibility(hasCheese, enter = fadeIn() + scaleIn(), exit = fadeOut() + scaleOut()) {
            Box(Modifier.padding(ring + 10.dp).fillMaxSize().clip(CircleShape)
                .background(Color(0x99FADC73)))
        }
        // topping garnishes scattered as small Boxes around the pie
    }
}
```

## 4. Tracker State Progression

```kotlin
// Hold `current: TrackerStage` in a ViewModel as state. Advancing recomputes
// node/rail colors; the rail segment color change animates implicitly. For the
// ~600ms red-fill hand-off, drive a per-segment Animatable width or color and
// fire LocalHapticFeedback (soft) when the stage flips.
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Domino's iOS tab bar is an opaque white surface with a hairline divider and a **red active tint**.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun DominosBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = DominosColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Order"   to Icons.Filled.Restaurant,
            "Tracker" to Icons.Filled.LocationOn,
            "Deals"   to Icons.Filled.LocalOffer,
            "Account" to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = DominosText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = DominosColors.Red,
                    selectedTextColor = DominosColors.Red,
                    unselectedIconColor = DominosColors.TextSecondary,
                    unselectedTextColor = DominosColors.TextSecondary,
                    indicatorColor = DominosColors.RedTint,
                ),
            )
        }
    }
}
```

The persistent **order footer** (running total + red CTA) sits above this bar — render `DominosCTA` in the `Scaffold` `bottomBar` slot stacked above `DominosBottomBar`, on a `DominosColors.Canvas` band with a top hairline.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Tracker stage advance | per-segment rail color/width `Animatable` `tween(600, FastOutSlowInEasing)`; `HapticFeedbackType.LongPress` (~soft) |
| Active node pulse | `rememberInfiniteTransition` `animateFloat` 1 → 1.06 `infiniteRepeatable(tween(800), Reverse)` |
| Build-pizza layer add | crust `animateDpAsState(tween(250))`; cheese `AnimatedVisibility` `fadeIn()+scaleIn()` |
| Add to Order | CTA `Modifier.scale` 1 → 0.98; cart badge `spring` 1 → 1.2 → 1; success haptic |
| Card tap | `Modifier.scale` 1 → 0.98 via `collectIsPressedAsState()` |
| Sheet present | `ModalBottomSheet` default slide; scrim `DominosColors.TextPrimary.copy(alpha = 0.45f)` |

```kotlin
// Cart-count badge bounce after add
val badge = remember { Animatable(1f) }
LaunchedEffect(cartCount) {
    if (cartCount > 0) { badge.animateTo(1.2f, spring(dampingRatio = 0.5f)); badge.animateTo(1f, spring()) }
}
```

Haptics: prefer `LocalHapticFeedback`. For the tracker stage hand-off, `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+) approximates iOS `.soft` impact.

## 7. Icons

Domino's ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Domino's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Completed stage check | `checkmark` | `Icons.Filled.Check` |
| Order (tab) | `menucard` | `Icons.Filled.Restaurant` |
| Tracker (tab) | `location.circle` | `Icons.Filled.LocationOn` |
| Deals (tab) | `tag` | `Icons.Filled.LocalOffer` |
| Account (tab) | `person` | `Icons.Filled.Person` |
| Cart | `cart` | `Icons.Filled.ShoppingCart` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Store chevron | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Quantity minus / plus | `minus` / `plus` | `Icons.Filled.Remove` / `Icons.Filled.Add` |
| Map / track | `map` | `Icons.Filled.Map` |
| Time / ETA | `clock` | `Icons.Filled.Schedule` |
| Back | `chevron.left` | `Icons.Filled.ArrowBackIosNew` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + drawArc-style work are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the white canvas wants dark system-bar icons via `WindowCompat`. Apply `Scaffold` insets so the order footer clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on item names and body. The tracker headline scales but caps at 2 lines; pin tab labels and tracker node labels (abbreviate on small widths) via `dp`-derived sizing.
- **TalkBack**: expose the tracker as a single node — `Modifier.semantics { role = Role.Image; contentDescription = "Order status: Bake, stage 2 of 5, ETA 7:45 PM" }` — and skip individual nodes; the CTA announces "Add to order, total $X".
- **Touch targets**: Material guidance is 48.dp minimum. Build option rows are ~56.dp (clear); ensure the quantity − / + glyphs get 48.dp hit areas via padding; the "Track on the map" link needs a 48.dp tappable area.
- **Contrast**: `#1F1F1F` on `#FFFFFF` is maximal. `#6E6E6E` on white passes WCAG AA at 13sp+. Domino's Red `#E31837` text on white passes AA for bold 16sp+ — keep small red text bold.
- **Color is not the only signal**: tracker completed nodes carry a check glyph and labels switch to bold, in addition to the blue/red/gray coding. Do **not** enable Material You `dynamicLightColorScheme()` — the red/blue domino identity must be fixed regardless of wallpaper.
