# Revolut (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Revolut's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, the brand gradient, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Revolut's cool near-black canvas, the violet→purple brand gradient, the metal-card hero, the spend donut) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush.linearGradient` instead of `LinearGradient`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/RevolutColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object RevolutColors {
    // Canvas & Surfaces
    val Canvas   = Color(0xFF0A0A0F)
    val Surface1 = Color(0xFF16161F)
    val Surface2 = Color(0xFF1E1E2A)
    val Surface3 = Color(0xFF28283A)
    val Divider  = Color(0xFF2A2A38)
    val Border   = Color(0xFF33334A)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF9A9AAA)
    val TextTertiary  = Color(0xFF6A6A7E)

    // Brand
    val GradStart    = Color(0xFF5B6BFF)
    val GradEnd      = Color(0xFF9C6BFF)
    val Brand        = Color(0xFF6B5BFF) // solid fallback
    val BrandPressed = Color(0xFF5648D6)
    val BrandTint    = Color(0xFF1C1B33)

    // Semantic
    val Income = Color(0xFF1FD17B)
    val Spend  = Color(0xFFFF5A6A)
    val Warn   = Color(0xFFFFB23F)
    val Crypto = Color(0xFFF7C948)
}

val RevolutBrandGradient = Brush.linearGradient(
    listOf(RevolutColors.GradStart, RevolutColors.GradEnd)
)
```

Wire it into a Material 3 `darkColorScheme`. Revolut is dark-first; provide a light scheme only if you target system light mode (use `BrandPressed` solid, no gradient ghosting).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val RevolutScheme = darkColorScheme(
    primary          = RevolutColors.Brand,
    onPrimary        = Color.White,
    primaryContainer = RevolutColors.BrandTint,
    background        = RevolutColors.Canvas,
    onBackground      = RevolutColors.TextPrimary,
    surface           = RevolutColors.Surface1,
    onSurface         = RevolutColors.TextPrimary,
    surfaceVariant    = RevolutColors.Surface2,
    outline           = RevolutColors.Border,
    outlineVariant    = RevolutColors.Divider,
    error             = RevolutColors.Spend,
)

@Composable
fun RevolutTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = RevolutScheme, typography = RevolutTypography, content = content)
```

## 2. Typography

Aeonik is proprietary. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto); Inter is the closest substitute if bundled.

```kotlin
// ui/theme/RevolutType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Aeonik = FontFamily(
    Font(R.font.aeonik_regular, FontWeight.Normal),   // 400
    Font(R.font.aeonik_medium,  FontWeight.Medium),   // 500/600
    Font(R.font.aeonik_bold,    FontWeight.Bold),     // 700
)

private const val TNUM = "tnum"

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object RevolutText {
    val Balance     = TextStyle(Aeonik, fontWeight = FontWeight.Bold,     fontSize = 40.sp, lineHeight = 42.sp, letterSpacing = (-0.6).sp, fontFeatureSettings = TNUM)
    val TitleLarge  = TextStyle(Aeonik, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val Section     = TextStyle(Aeonik, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val TileBalance = TextStyle(Aeonik, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp, fontFeatureSettings = TNUM)
    val Subsection  = TextStyle(Aeonik, fontWeight = FontWeight.Medium,   fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Amount      = TextStyle(Aeonik, fontWeight = FontWeight.Medium,   fontSize = 16.sp, lineHeight = 19.sp, fontFeatureSettings = TNUM)
    val Merchant    = TextStyle(Aeonik, fontWeight = FontWeight.Medium,   fontSize = 16.sp, lineHeight = 21.sp)
    val Body        = TextStyle(Aeonik, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Button      = TextStyle(Aeonik, fontWeight = FontWeight.Medium,   fontSize = 16.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(Aeonik, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val LabelUpper  = TextStyle(Aeonik, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Tab         = TextStyle(Aeonik, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val Caption     = TextStyle(Aeonik, fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val RevolutTypography = Typography(
    headlineLarge = RevolutText.TitleLarge,
    headlineSmall = RevolutText.Section,
    titleMedium   = RevolutText.Merchant,
    bodyMedium    = RevolutText.Body,
    labelSmall    = RevolutText.Tab,
)
```

## 3. Signature Components

### Primary Gradient CTA

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.ui.draw.alpha
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun RevPrimaryButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "ctaScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .scale(scale)
            .alpha(if (pressed) 0.85f else 1f)
            .clip(RoundedCornerShape(16.dp))
            .background(RevolutBrandGradient)
            .heightIn(min = 52.dp)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS light impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = RevolutText.Button, color = Color.White)
    }
}

@Composable
fun RevSecondaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        modifier
            .clip(RoundedCornerShape(16.dp))
            .background(if (pressed) RevolutColors.Surface3 else RevolutColors.Surface2)
            .heightIn(min = 52.dp)
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) { Text(text, style = RevolutText.Button, color = Color.White) }
}
```

### Currency Balance Tile

```kotlin
@Composable
fun CurrencyTile(
    flag: String,
    code: String,
    name: String,
    balance: String,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier
            .clip(RoundedCornerShape(16.dp))
            .background(RevolutColors.Surface1)
            .border(1.dp, RevolutColors.Divider, RoundedCornerShape(16.dp))
            .height(72.dp)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(
            Modifier.size(28.dp).clip(CircleShape).background(RevolutColors.Surface2),
            contentAlignment = Alignment.Center,
        ) { Text(flag, fontSize = 16.sp) }
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(code, style = RevolutText.Merchant, color = RevolutColors.TextPrimary)
            Text(name, style = RevolutText.Meta, color = RevolutColors.TextSecondary)
        }
        Text(balance, style = RevolutText.TileBalance, color = RevolutColors.TextPrimary)
    }
}
```

### Transaction Row

```kotlin
@Composable
fun TransactionRow(
    merchant: String,
    meta: String,
    amount: String,
    incoming: Boolean,
    logoUrl: String,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Row(
        modifier
            .fillMaxWidth()
            .background(if (pressed) RevolutColors.Surface2 else Color.Transparent)
            .clickable(interaction, indication = null) {}
            .height(64.dp)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        AsyncImage(
            model = logoUrl, contentDescription = null,
            modifier = Modifier.size(40.dp).clip(CircleShape).background(RevolutColors.Surface2),
            contentScale = ContentScale.Crop,
        )
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(merchant, style = RevolutText.Merchant, color = RevolutColors.TextPrimary, maxLines = 1, overflow = TextOverflow.Ellipsis)
            Text(meta, style = RevolutText.Meta, color = RevolutColors.TextSecondary, maxLines = 1, overflow = TextOverflow.Ellipsis)
        }
        Text(amount, style = RevolutText.Amount, color = if (incoming) RevolutColors.Income else RevolutColors.TextPrimary)
    }
}
```

### Spend Analytics Donut

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke

@Composable
fun SpendDonut(total: String, progress: Float, modifier: Modifier = Modifier) {
    val anim = remember { Animatable(0f) }
    LaunchedEffect(progress) { anim.animateTo(progress, tween(700)) }

    Box(
        modifier
            .clip(RoundedCornerShape(20.dp))
            .background(RevolutColors.Surface1)
            .padding(20.dp),
        contentAlignment = Alignment.Center,
    ) {
        Canvas(Modifier.size(180.dp)) {
            val stroke = 14.dp.toPx()
            drawCircle(RevolutColors.Surface3, style = Stroke(stroke), radius = (size.minDimension - stroke) / 2)
            drawArc(
                brush = RevolutBrandGradient,
                startAngle = -90f,
                sweepAngle = 360f * anim.value,
                useCenter = false,
                style = Stroke(stroke, cap = StrokeCap.Round),
                topLeft = Offset(stroke / 2, stroke / 2),
                size = androidx.compose.ui.geometry.Size(size.width - stroke, size.height - stroke),
            )
        }
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Text(total, style = RevolutText.Section, color = RevolutColors.TextPrimary)
            Text("this month", style = RevolutText.Meta, color = RevolutColors.TextSecondary)
        }
    }
}
```

### Metal Card Hero (flip + sheen)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.ui.graphics.graphicsLayer

@Composable
fun MetalCardHero(modifier: Modifier = Modifier) {
    var flipped by remember { mutableStateOf(false) }
    val rotation by animateFloatAsState(if (flipped) 180f else 0f, tween(450), label = "flip")
    val sheen = remember { Animatable(-1f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(Unit) { sheen.animateTo(1f, tween(1200)) }

    Box(
        modifier
            .fillMaxWidth()
            .aspectRatio(1.586f)
            .graphicsLayer { rotationY = rotation; cameraDistance = 12f * density }
            .clip(RoundedCornerShape(16.dp))
            .background(Brush.linearGradient(listOf(Color(0xFF2E2E33), Color(0xFF101014))))
            .clickable {
                flipped = !flipped
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
            },
    ) {
        // diagonal sheen band
        Box(
            Modifier
                .matchParentSize()
                .graphicsLayer { translationX = sheen.value * size.width }
                .background(Brush.linearGradient(listOf(Color.Transparent, Color.White.copy(alpha = 0.18f), Color.Transparent))),
        )
        Column(Modifier.fillMaxSize().padding(20.dp), verticalArrangement = Arrangement.SpaceBetween) {
            Text("Revolut", style = RevolutText.Subsection, color = Color.White.copy(alpha = 0.9f))
            Text(if (flipped) "CVV 042" else "•••• 4821", style = RevolutText.Amount, color = Color.White)
        }
    }
}
```

## 4. Brand Gradient Helper

```kotlin
// Tint a glyph with the brand gradient (mask via blend) — or use RevolutColors.Brand
// for tiny icons / focus rings where a gradient is impractical.
import androidx.compose.ui.graphics.BlendMode
import androidx.compose.ui.graphics.graphicsLayer
import androidx.compose.ui.draw.drawWithContent

fun Modifier.brandGradientTint() = this
    .graphicsLayer(alpha = 0.99f)
    .drawWithContent {
        drawContent()
        drawRect(RevolutBrandGradient, blendMode = BlendMode.SrcAtop)
    }
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Revolut's iOS tab bar is `.regularMaterial` blur over a near-black; Android has no first-class live blur, so use a 92%-opaque canvas. **Active tint is the brand color.**

```kotlin
@Composable
fun RevolutBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = RevolutColors.Canvas.copy(alpha = 0.92f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"      to Icons.Filled.Home,
            "Invest"    to Icons.Filled.TrendingUp,
            "Crypto"    to Icons.Filled.CurrencyBitcoin,
            "Lifestyle" to Icons.Filled.AutoAwesome,
            "Cards"     to Icons.Filled.CreditCard,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = RevolutText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = RevolutColors.Brand,
                    selectedTextColor   = RevolutColors.TextPrimary,
                    unselectedIconColor = RevolutColors.TextTertiary,
                    unselectedTextColor = RevolutColors.TextTertiary,
                    indicatorColor      = RevolutColors.BrandTint,
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Card flip | `animateFloatAsState` rotationY 0↔180 `tween(450)`, `graphicsLayer{ cameraDistance }`, `HapticFeedbackType.LongPress` on reveal |
| Card sheen | `Animatable(-1f → 1f)` `tween(1200)` driving `translationX` of a translucent band on appear/flip |
| Gradient CTA press | `animateFloatAsState` scale 1→0.98 + `alpha` 1→0.85, `tween(150)` |
| Donut draw | `Animatable(0f → progress)` `tween(700)` feeding `drawArc` sweep on entry |
| Balance reveal | `animateFloatAsState` blur radius via `Modifier.blur` 0↔12.dp, `tween(250)` |
| Segmented thumb | `animateDpAsState` gradient-pill offset, `tween(220)` ease-in-out |

Haptics: prefer `LocalHapticFeedback`. For a richer card-flip "thunk" use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.LONG_PRESS)` or a `Vibrator` `VibrationEffect.createOneShot(14, …)`.

## 7. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Revolut glyphs as vector drawables and load via `ImageVector.vectorResource(...)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Invest (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Crypto (tab) | `bitcoinsign.circle.fill` | `Icons.Filled.CurrencyBitcoin` |
| Lifestyle (tab) | `sparkles` | `Icons.Filled.AutoAwesome` |
| Cards (tab) | `creditcard.fill` | `Icons.Filled.CreditCard` |
| Add money | `plus` | `Icons.Filled.Add` |
| Exchange | `arrow.left.arrow.right` | `Icons.Filled.SwapHoriz` |
| Freeze card | `snowflake` | `Icons.Filled.AcUnit` |
| Send / pay | `paperplane.fill` | `Icons.Filled.Send` |
| Contactless | `wave.3.right` | `Icons.Filled.Contactless` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Notifications | `bell.fill` | `Icons.Filled.Notifications` |
| Statement | `doc.text` | `Icons.Filled.Description` |
| Income arrow | `arrow.down.left` | `Icons.Filled.SouthWest` |
| Outgoing arrow | `arrow.up.right` | `Icons.Filled.NorthEast` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; gradient + 3D motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the near-black canvas wants light-content system bars (`WindowCompat`). Apply `Scaffold` insets so the tab bar clears gesture nav.
- **Font scaling**: `sp` honors the user's scale — keep it on titles, body, amounts. Pin the hero balance, tab labels, uppercase labels, and currency-tile figures with a fixed `Density` wrapper for column alignment.
- **TalkBack**: announce balances with currency; merge transaction-row text with `Modifier.semantics(mergeDescendants = true)` and state direction ("Sent 9 pounds 99 to Spotify"); label the card hero ("Card, double tap to reveal details").
- **Touch targets**: Material minimum is 48.dp. The 52.dp CTA and 56.dp quick-action circles are clear; give the 24.dp tab glyphs and small icons a 48.dp hit area via padding.
- **Contrast**: `#9A9AAA` on `#0A0A0F` passes WCAG AA at 14sp+; bump 11sp labels toward `#B0B0C0` for strict compliance. White on the gradient passes against both stops.
- **Reduced motion / transparency**: honor `Settings.Global.ANIMATOR_DURATION_SCALE == 0` by skipping the sheen and instant-drawing the donut/flip; if transparency is reduced, use a solid `Canvas.copy(alpha = 0.98f)` tab background.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Revolut's brand requires the fixed `#0A0A0F` canvas and the violet→purple gradient regardless of wallpaper.
