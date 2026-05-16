# Starbucks (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Starbucks' visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Siren Green CTAs, the warm House Green dark canvas, the gold Stars dashboard ring, the Short→Trenta size selector) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `drawArc` Stars ring instead of a SwiftUI `trim`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for the category-card photography. No color extraction — the Stars ring uses a fixed gold gradient brush, so `androidx.palette` is not required.

## 1. Color Tokens

```kotlin
// ui/theme/StarbucksColors.kt
import androidx.compose.ui.graphics.Color

object StarbucksColors {
    // Canvas (Light)
    val Canvas       = Color(0xFFF9F9F9) // Warm White — softer than pure white
    val CardSurface  = Color(0xFFFFFFFF)
    val SurfaceMuted = Color(0xFFF1F1F1)
    val Divider      = Color(0xFFE8E8E8)

    // Text
    val TextPrimary   = Color(0xFF000000)
    val TextSecondary = Color(0xFF595959)
    val TextTertiary  = Color(0xFF8E8E8E)

    // Brand
    val SirenGreen   = Color(0xFF00704A)
    val GreenPressed = Color(0xFF00563A)
    val HouseGreen   = Color(0xFF1E3932) // dark canvas — NOT black, deep forest green
    val Mint         = Color(0xFFD4E9E2)

    // Gold (Rewards)
    val GoldStar  = Color(0xFFCBA258)
    val GoldAlt   = Color(0xFFC1A265)
    val GoldLight = Color(0xFFE4C896)

    // Semantic
    val SuccessGreen = Color(0xFF00754A)
    val AlertRed     = Color(0xFFDD3333)
    val InfoBlue     = Color(0xFF006FCF)
    val WarningAmber = Color(0xFFF5A623)

    // Dark
    val DarkCanvas   = Color(0xFF1E3932) // House Green
    val DarkSurface1 = Color(0xFF2D4A43)
    val DarkSurface2 = Color(0xFF3B5852)
    val DarkDivider  = Color(0xFF3A4F48)
    val DarkTextPrimary   = Color(0xFFFFFFFF)
    val DarkTextSecondary = Color(0xFFC9CDCB)
}

// The gold sheen used on the Stars ring stroke and the Scan-to-Pay header
val GoldRingBrush = androidx.compose.ui.graphics.Brush.horizontalGradient(
    listOf(StarbucksColors.GoldStar, StarbucksColors.GoldLight, StarbucksColors.GoldStar)
)
```

Starbucks is a light-first brand (Warm White canvas), so wire a Material 3 `lightColorScheme` and supply a House-Green `darkColorScheme` for the night theme. Note `onPrimary` is intentionally pure white on Siren Green.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val StarbucksLight = lightColorScheme(
    primary        = StarbucksColors.SirenGreen,
    onPrimary      = StarbucksColors.CardSurface,
    secondary      = StarbucksColors.Mint,
    background     = StarbucksColors.Canvas,
    onBackground   = StarbucksColors.TextPrimary,
    surface        = StarbucksColors.CardSurface,
    onSurface      = StarbucksColors.TextPrimary,
    surfaceVariant = StarbucksColors.SurfaceMuted,
    outline        = StarbucksColors.Divider,
    error          = StarbucksColors.AlertRed,
)

private val StarbucksDark = darkColorScheme(
    primary        = StarbucksColors.SirenGreen,
    onPrimary      = StarbucksColors.CardSurface,
    background     = StarbucksColors.DarkCanvas,   // House Green, not black
    onBackground   = StarbucksColors.DarkTextPrimary,
    surface        = StarbucksColors.DarkSurface1,
    onSurface      = StarbucksColors.DarkTextPrimary,
    surfaceVariant = StarbucksColors.DarkSurface2,
    outline        = StarbucksColors.DarkDivider,
    error          = StarbucksColors.AlertRed,
)

@Composable
fun StarbucksTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (darkTheme) StarbucksDark else StarbucksLight,
    typography  = StarbucksTypography,
    content     = content,
)
```

## 2. Typography

SoDo Sans is proprietary (licensed from Dalton Maag, named after Seattle's SoDo neighborhood). Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto) — its humanist tone is the closest free substitute on Android; for a closer match bundle Lato.

```kotlin
// ui/theme/StarbucksType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val SoDoSans = FontFamily(
    Font(R.font.sodosans_regular,  FontWeight.Normal),    // 400
    Font(R.font.sodosans_semibold, FontWeight.SemiBold),  // 600
    Font(R.font.sodosans_bold,     FontWeight.Bold),      // 700
    Font(R.font.sodosans_extrabold, FontWeight.ExtraBold), // 800 — Stars + hero only
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, weights preserved)
object StarbucksText {
    val StarsHero    = TextStyle(SoDoSans, fontWeight = FontWeight.ExtraBold, fontSize = 48.sp, lineHeight = 48.sp, letterSpacing = (-0.5).sp)
    val ScreenTitle  = TextStyle(SoDoSans, fontWeight = FontWeight.Bold,      fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.3).sp)
    val SheetTitle   = TextStyle(SoDoSans, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Section      = TextStyle(SoDoSans, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val CardTitle    = TextStyle(SoDoSans, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 21.sp)
    val DrinkTitle   = TextStyle(SoDoSans, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Body         = TextStyle(SoDoSans, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 22.sp)
    val Meta         = TextStyle(SoDoSans, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 17.sp)
    val Price        = TextStyle(SoDoSans, fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val StarsInline  = TextStyle(SoDoSans, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 22.sp)
    val Button       = TextStyle(SoDoSans, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val ButtonSmall  = TextStyle(SoDoSans, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 15.sp)
    val SizePill     = TextStyle(SoDoSans, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 0.2.sp)
    val Tab          = TextStyle(SoDoSans, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.1.sp)
    val Chip         = TextStyle(SoDoSans, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.3.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val StarbucksTypography = Typography(
    headlineLarge = StarbucksText.ScreenTitle,
    headlineSmall = StarbucksText.SheetTitle,
    titleMedium   = StarbucksText.CardTitle,
    bodyMedium    = StarbucksText.Body,
    labelSmall    = StarbucksText.Tab,
)
```

## 3. Signature Components

### Primary CTA (Siren Green Pill)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.foundation.clickable
import androidx.compose.ui.unit.dp

@Composable
fun StarbucksPrimaryButton(
    title: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        targetValue = if (pressed) 0.98f else 1f,
        animationSpec = spring(dampingRatio = 0.8f, stiffness = 600f),
        label = "ctaScale",
    )
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .fillMaxWidth()
            .heightIn(min = 52.dp)
            .scale(scale)
            // branded green glow — rgba(0,112,74,0.2) 0 4px 12px
            .shadow(12.dp, RoundedCornerShape(50), spotColor = StarbucksColors.SirenGreen.copy(alpha = 0.2f))
            .clip(RoundedCornerShape(50))
            .background(if (pressed) StarbucksColors.GreenPressed else StarbucksColors.SirenGreen)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                onClick()
            }
            .padding(horizontal = 24.dp, vertical = 16.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = StarbucksText.Button, color = StarbucksColors.CardSurface)
    }
}
```

### Category Card (Order Grid)

```kotlin
import androidx.compose.foundation.layout.aspectRatio
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun StarbucksCategoryCard(
    title: String,
    imageUrl: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, spring(dampingRatio = 0.8f), label = "cardScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .aspectRatio(2f / 3f)
            .scale(scale)
            .shadow(8.dp, RoundedCornerShape(16.dp), spotColor = Color.Black.copy(alpha = 0.08f))
            .clip(RoundedCornerShape(16.dp))
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS light impact
                onClick()
            },
        contentAlignment = Alignment.BottomStart,
    ) {
        AsyncImage(
            model = imageUrl,
            contentDescription = title,
            modifier = Modifier.fillMaxSize(),
            contentScale = ContentScale.Crop,
        )
        // bottom 40% darken for label legibility
        Box(
            Modifier
                .fillMaxSize()
                .background(Brush.verticalGradient(0.5f to Color.Transparent, 1f to Color.Black.copy(alpha = 0.5f)))
        )
        Text(
            title,
            style = StarbucksText.CardTitle,
            color = StarbucksColors.CardSurface,
            modifier = Modifier.padding(16.dp),
        )
    }
}
```

### Syrup Pump Stepper

```kotlin
import androidx.compose.foundation.border
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Remove
import androidx.compose.material3.Icon

@Composable
fun StarbucksPumpStepper(
    label: String,
    count: Int,
    onCountChange: (Int) -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .padding(vertical = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Text(label, style = StarbucksText.ButtonSmall, color = StarbucksColors.TextPrimary)
        Spacer(Modifier.weight(1f))
        StepButton(Icons.Filled.Remove, "Decrease $label") { if (count > 0) onCountChange(count - 1) }
        Text(
            "$count",
            style = StarbucksText.Price,
            color = StarbucksColors.TextPrimary,
            modifier = Modifier.widthIn(min = 44.dp),
            textAlign = androidx.compose.ui.text.style.TextAlign.Center,
        )
        StepButton(Icons.Filled.Add, "Increase $label") { onCountChange(count + 1) }
    }
}

@Composable
private fun StepButton(
    icon: androidx.compose.ui.graphics.vector.ImageVector,
    contentDescription: String,
    onClick: () -> Unit,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.9f else 1f, spring(stiffness = 700f), label = "stepScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = Modifier
            .size(36.dp)
            .scale(scale)
            .clip(CircleShape)
            .border(1.5.dp, StarbucksColors.Divider, CircleShape)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(icon, contentDescription, tint = StarbucksColors.TextPrimary, modifier = Modifier.size(14.dp))
    }
}
```

### Scan-to-Pay Barcode Card

```kotlin
import androidx.compose.material.icons.filled.Star

@Composable
fun StarbucksScanPayCard(
    stars: Int,
    balance: Double,
    barcodeNumber: String, // "6241 1234 5678 9012"
    barcode: @Composable () -> Unit, // supply a real Code128 renderer (e.g. ZXing bitmap)
    modifier: Modifier = Modifier,
) {
    Column(
        modifier = modifier.fillMaxWidth().padding(horizontal = 16.dp),
        verticalArrangement = Arrangement.spacedBy(20.dp),
    ) {
        // Stars summary — gold-gradient header
        Row(
            Modifier
                .fillMaxWidth()
                .clip(RoundedCornerShape(16.dp))
                .background(GoldRingBrush)
                .padding(16.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Icon(Icons.Filled.Star, null, tint = StarbucksColors.CardSurface, modifier = Modifier.size(20.dp))
            Spacer(Modifier.width(8.dp))
            Text("$stars Stars", style = StarbucksText.StarsInline, color = StarbucksColors.CardSurface)
            Spacer(Modifier.weight(1f))
            Text("GOLD MEMBER", style = StarbucksText.Chip, color = StarbucksColors.CardSurface)
        }

        // Barcode
        Column(
            Modifier
                .fillMaxWidth()
                .clip(RoundedCornerShape(20.dp))
                .background(StarbucksColors.CardSurface)
                .padding(20.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(Modifier.height(80.dp), contentAlignment = Alignment.Center) { barcode() }
            Text(barcodeNumber, style = StarbucksText.Meta.copy(letterSpacing = 2.sp), color = StarbucksColors.TextPrimary)
        }

        // Balance + Reload
        Row(verticalAlignment = Alignment.CenterVertically) {
            Column(verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Text("Balance", style = StarbucksText.Meta, color = StarbucksColors.TextSecondary)
                Text("$%.2f".format(balance), style = StarbucksText.DrinkTitle, color = StarbucksColors.TextPrimary)
            }
            Spacer(Modifier.weight(1f))
            StarbucksPrimaryButton("Reload", onClick = {}, modifier = Modifier.widthIn(max = 140.dp))
        }
    }
}
```

## 4. Stars Dashboard Ring + Size Selector (the signature dynamic system)

Starbucks does no color extraction. Its two defining interactions are the **gold Stars progress ring** that fills on appearance and the **Short→Trenta size selector** whose green pill slides between sizes. iOS draws the ring with a SwiftUI `Circle().trim`; in Compose use `Canvas` + `drawArc` with an `Animatable` sweep.

### Stars Progress Ring

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.semantics.contentDescription
import androidx.compose.ui.semantics.semantics

@Composable
fun StarbucksStarsRing(
    currentStars: Int,
    targetStars: Int,
    modifier: Modifier = Modifier,
) {
    val target = (currentStars.toFloat() / targetStars).coerceIn(0f, 1f)
    val progress = remember { Animatable(0f) }
    LaunchedEffect(target) {
        // 1.5s ease-out fill on appearance (DESIGN.md §6)
        progress.animateTo(target, tween(durationMillis = 1500))
    }
    val remaining = (targetStars - currentStars).coerceAtLeast(0)

    Column(
        modifier = modifier.semantics {
            contentDescription = "$currentStars Stars earned, $remaining more Stars until free reward"
        },
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        Box(contentAlignment = Alignment.Center) {
            Canvas(Modifier.size(180.dp)) {
                val stroke = 12.dp.toPx()
                val inset = stroke / 2
                val arcSize = Size(size.width - stroke, size.height - stroke)
                // track
                drawArc(
                    color = StarbucksColors.Divider,
                    startAngle = 0f, sweepAngle = 360f, useCenter = false,
                    topLeft = androidx.compose.ui.geometry.Offset(inset, inset),
                    size = arcSize,
                    style = Stroke(width = stroke),
                )
                // gold fill — starts at 12 o'clock (-90°), clockwise
                drawArc(
                    brush = GoldRingBrush,
                    startAngle = -90f,
                    sweepAngle = 360f * progress.value,
                    useCenter = false,
                    topLeft = androidx.compose.ui.geometry.Offset(inset, inset),
                    size = arcSize,
                    style = Stroke(width = stroke, cap = StrokeCap.Round),
                )
            }
            Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Text("$currentStars", style = StarbucksText.StarsHero, color = StarbucksColors.GoldStar)
                Text("Stars", style = StarbucksText.Meta.copy(fontWeight = FontWeight.SemiBold), color = StarbucksColors.TextSecondary)
            }
        }
        Text("$remaining Stars until free reward", style = StarbucksText.Body, color = StarbucksColors.TextSecondary)
    }
}
```

### Size Selector (Short / Tall / Grande / Venti / Trenta)

```kotlin
import androidx.compose.material.icons.filled.Coffee

enum class CoffeeSize(val label: String, val cupScale: Float) {
    SHORT("Short", 0.55f), TALL("Tall", 0.7f), GRANDE("Grande", 0.85f),
    VENTI("Venti", 1.0f), TRENTA("Trenta", 1.15f),
}

@Composable
fun StarbucksSizeSelector(
    selected: CoffeeSize,
    onSelect: (CoffeeSize) -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(modifier, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
        CoffeeSize.entries.forEach { size ->
            SizePill(size = size, isSelected = selected == size, onClick = { onSelect(size) })
        }
    }
}

@Composable
private fun SizePill(size: CoffeeSize, isSelected: Boolean, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val haptics = LocalHapticFeedback.current
    // 250ms ease as the green background "slides" to the new size
    val bg by animateColorAsState(
        if (isSelected) StarbucksColors.SirenGreen else Color.Transparent,
        tween(250), label = "pillBg",
    )
    val fg = if (isSelected) StarbucksColors.CardSurface else StarbucksColors.TextPrimary

    Column(
        modifier = Modifier
            .heightIn(min = 36.dp)
            .clip(RoundedCornerShape(50))
            .background(bg)
            .then(if (isSelected) Modifier else Modifier.border(1.5.dp, StarbucksColors.Divider, RoundedCornerShape(50)))
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                onClick()
            }
            .padding(horizontal = 14.dp, vertical = 6.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Icon(Icons.Filled.Coffee, null, tint = fg, modifier = Modifier.size((20 * size.cupScale).dp))
        Text(size.label, style = StarbucksText.SizePill, color = fg)
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Starbucks' iOS tab bar is an opaque white bar (no blur) with **Scan** as the central in-store anchor. Use Material 3 `NavigationBar`; active tint is Siren Green (filled variant), inactive is `#595959`. Android has no live blur — but Starbucks doesn't use one here anyway, so an opaque `CardSurface` container is exact.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun StarbucksBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = StarbucksColors.CardSurface,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"   to Icons.Filled.Home,
            "Scan"   to Icons.Filled.QrCode,   // the in-store anchor
            "Order"  to Icons.Filled.Coffee,
            "Gift"   to Icons.Filled.CardGiftcard,
            "Offers" to Icons.Filled.LocalOffer,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = StarbucksText.Tab) },
                alwaysShowLabel = true,
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = StarbucksColors.SirenGreen,
                    selectedTextColor   = StarbucksColors.SirenGreen,
                    unselectedIconColor = StarbucksColors.TextSecondary,
                    unselectedTextColor = StarbucksColors.TextSecondary,
                    indicatorColor      = StarbucksColors.Mint.copy(alpha = 0.4f),
                ),
            )
        }
    }
}
```

Pair with a 0.5.dp top hairline (`StarbucksColors.Divider`) — render it as a `HorizontalDivider` above the bar inside the `Scaffold` `bottomBar` slot.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Stars ring fill | `Animatable` 0 → progress over `tween(1500)` ease-out, once on appearance |
| Size pill slide | `animateColorAsState` on the green fill with `tween(250)`; `HapticFeedbackType.TextHandleMove` |
| Stepper press | `animateFloatAsState` 1 → 0.9 `spring(stiffness=700f)`, light haptic, 80ms count bump |
| Category card tap | `animateFloatAsState` 1 → 0.98 `spring(dampingRatio=0.8f)`, light haptic, then navigate |
| Stars earned tick-up | `LaunchedEffect` loop incrementing `currentStars` with 80ms `delay`; success haptic on last |
| Order placed | checkmark `Animatable` 0 → 1.2 → 1.0 `spring(dampingRatio=0.55f)`; success haptic |
| Primary CTA press | `animateFloatAsState` 1 → 0.98 + color → `GreenPressed`; `HapticFeedbackType.LongPress` |

```kotlin
// Stars earned — tick the count up one Star at a time, then fire success haptic
@Composable
fun rememberStarsTicker(from: Int, to: Int): State<Int> {
    val count = remember { mutableIntStateOf(from) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(to) {
        for (s in (from + 1)..to) {
            kotlinx.coroutines.delay(80)
            count.intValue = s
        }
        haptics.performHapticFeedback(HapticFeedbackType.LongPress) // success-like confirmation
    }
    return count
}
```

Haptics: prefer `LocalHapticFeedback`. For a richer "success" cue on order-placed, use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(12, ...)` to approximate iOS's medium impact.

## 7. Icons

Starbucks ships a few custom glyphs (the Siren, the cup silhouettes scaled by size). The closest first-party set is `androidx.compose.material:material-icons-extended`; for exact parity (the Siren store pin, the size-scaled cups) export them as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Cup / size icon | `cup.and.saucer.fill` | `Icons.Filled.Coffee` |
| Star (rewards) | `star.fill` | `Icons.Filled.Star` |
| QR / Scan | `qrcode` | `Icons.Filled.QrCode` |
| Plus / Minus (stepper) | `plus` / `minus` | `Icons.Filled.Add` / `Icons.Filled.Remove` |
| Cart | `cart.fill` | `Icons.Filled.ShoppingCart` |
| Store / pin | `mappin.circle.fill` | `Icons.Filled.Place` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Order (tab) | `cup.and.saucer.fill` | `Icons.Filled.Coffee` |
| Gift (tab) | `gift.fill` | `Icons.Filled.CardGiftcard` |
| Offers (tab) | `tag.fill` | `Icons.Filled.LocalOffer` |
| Checkmark (order placed) | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Info | `info.circle` | `Icons.Filled.Info` |
| Clock (ready in) | `clock.fill` | `Icons.Filled.Schedule` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; the `Canvas` ring and motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The Warm White canvas wants dark system-bar icons in light mode (`WindowCompat` light status bar); House Green dark mode wants light icons. Apply `Scaffold` insets so the tab bar clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on drink names, body, descriptions. Pin layout-sensitive text: the **48.sp Stars count** (clamp at fixed size by deriving from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`), the **13.sp size-pill labels** (pill widths are tight), and **11.sp tab labels**.
- **TalkBack**: the Stars ring exposes a single `contentDescription` ("47 Stars earned, 75 more Stars until free reward"); each size pill announces "[Size] selected" / "[Size], double-tap to select" via `Modifier.semantics`; the barcode card announces the balance and that the barcode is displayed for scanning.
- **Touch targets**: Material guidance is 48.dp minimum. The 52.dp CTA and 180.dp ring are clear; the 36.dp stepper/size pills have a 44–48.dp effective target via generous horizontal padding — bump the visual hit area with `Modifier.size(48.dp)` if your build targets strict compliance.
- **Contrast**: Gold `#CBA258` on Warm White `#F9F9F9` passes WCAG AA only at the 22sp+ ExtraBold Stars treatment — for any 13–14sp gold text use `GoldAlt #C1A265` or darker. `#595959` secondary on `#F9F9F9` passes AA at 13sp+.
- **Reduce motion**: detect `Settings.Global.ANIMATOR_DURATION_SCALE == 0f` (or a stored user pref) and snap the Stars ring to `target` immediately and disable the size-pill slide.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` / `dynamicDarkColorScheme()` — Starbucks' brand requires fixed Siren Green, the House Green dark canvas, and gold Stars regardless of wallpaper.
