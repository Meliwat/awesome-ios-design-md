# Klarna (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Klarna's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Klarna's soft bright canvas, near-black structural ink, the soft pink action color, the Pay-in-4 timeline) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `sp`/`dp` instead of `pt`, slightly slower `tween`s for the Klarna glide.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/KlarnaColors.kt
import androidx.compose.ui.graphics.Color

object KlarnaColors {
    // Canvas & Surfaces
    val Canvas         = Color(0xFFFFFFFF)
    val SurfacePink    = Color(0xFFFFF2F5)
    val SurfaceNeutral = Color(0xFFF6F4F8)
    val Divider        = Color(0xFFF0DDE3)
    val Border         = Color(0xFFE2D2D8)

    // Text
    val TextPrimary   = Color(0xFF0B051D)
    val TextSecondary = Color(0xFF6E6878)
    val TextTertiary  = Color(0xFF9B96A3)

    // Brand
    val Pink        = Color(0xFFFFB3C7)
    val PinkPressed = Color(0xFFF49CB4)
    val PinkTint    = Color(0xFFFFF2F5)
    val Black       = Color(0xFF0B051D)
    val BlackHover  = Color(0xFF1A1330)

    // Semantic
    val Success  = Color(0xFF0E8A4F)
    val Upcoming = Color(0xFFA9700E)
    val Error    = Color(0xFFC8102E)
}
```

Wire it into a Material 3 `lightColorScheme`. Klarna is light-first; provide a dark scheme only if you target system dark mode (keep `Pink` unchanged with `Black` content).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val KlarnaScheme = lightColorScheme(
    primary          = KlarnaColors.Pink,
    onPrimary        = KlarnaColors.Black,   // near-black on pink, never white
    primaryContainer = KlarnaColors.PinkTint,
    secondary        = KlarnaColors.Black,
    onSecondary      = Color.White,
    background        = KlarnaColors.Canvas,
    onBackground      = KlarnaColors.TextPrimary,
    surface           = KlarnaColors.Canvas,
    onSurface         = KlarnaColors.TextPrimary,
    surfaceVariant    = KlarnaColors.SurfacePink,
    outline           = KlarnaColors.Border,
    outlineVariant    = KlarnaColors.Divider,
    error             = KlarnaColors.Error,
)

@Composable
fun KlarnaTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = KlarnaScheme, typography = KlarnaTypography, content = content)
```

## 2. Typography

Klarna Text/Display are proprietary. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto); Inter is the closest substitute if bundled.

```kotlin
// ui/theme/KlarnaType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val KlarnaText = FontFamily(
    Font(R.font.klarna_text_regular, FontWeight.Normal),   // 400
    Font(R.font.klarna_text_medium,  FontWeight.Medium),   // 600
    Font(R.font.klarna_text_bold,    FontWeight.Bold),     // 700
)
val KlarnaDisplay = FontFamily(Font(R.font.klarna_display_bold, FontWeight.Bold))

private const val TNUM = "tnum"

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object KlarnaTextStyles {
    val AmountHero = TextStyle(KlarnaDisplay, fontWeight = FontWeight.Bold,   fontSize = 36.sp, lineHeight = 38.sp, letterSpacing = (-0.5).sp, fontFeatureSettings = TNUM)
    val TitleLarge = TextStyle(KlarnaText, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val Section    = TextStyle(KlarnaText, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val PlanAmount = TextStyle(KlarnaText, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp, fontFeatureSettings = TNUM)
    val Subsection = TextStyle(KlarnaText, fontWeight = FontWeight.Medium, fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Amount     = TextStyle(KlarnaText, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 19.sp, fontFeatureSettings = TNUM)
    val Title      = TextStyle(KlarnaText, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 21.sp)
    val Body       = TextStyle(KlarnaText, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 23.sp)
    val Button     = TextStyle(KlarnaText, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 20.sp)
    val Meta       = TextStyle(KlarnaText, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val LabelUpper = TextStyle(KlarnaText, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.5.sp)
    val Tab        = TextStyle(KlarnaText, fontWeight = FontWeight.Medium, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.1.sp)
    val Caption    = TextStyle(KlarnaText, fontWeight = FontWeight.Normal, fontSize = 11.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val KlarnaTypography = Typography(
    headlineLarge = KlarnaTextStyles.TitleLarge,
    headlineSmall = KlarnaTextStyles.Section,
    titleMedium   = KlarnaTextStyles.Title,
    bodyMedium    = KlarnaTextStyles.Body,
    labelSmall    = KlarnaTextStyles.Tab,
)
```

## 3. Signature Components

### Primary CTA (Klarna Pink)

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun KlarnaPrimaryButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, tween(160), label = "ctaScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .scale(scale)
            .clip(RoundedCornerShape(28.dp)) // very soft full pill
            .background(if (pressed) KlarnaColors.PinkPressed else KlarnaColors.Pink)
            .heightIn(min = 56.dp)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS light impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = KlarnaTextStyles.Button, color = KlarnaColors.Black) // near-black, never white
    }
}

@Composable
fun KlarnaBlackButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        modifier
            .clip(RoundedCornerShape(28.dp))
            .background(if (pressed) KlarnaColors.BlackHover else KlarnaColors.Black)
            .heightIn(min = 56.dp)
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) { Text(text, style = KlarnaTextStyles.Button, color = Color.White) }
}
```

### Pay-in-4 Schedule Card (signature)

```kotlin
enum class InstallmentState { Paid, Next, Upcoming }
data class Installment(val date: String, val amount: String, val state: InstallmentState)

@Composable
fun PayInFourCard(installments: List<Installment>, modifier: Modifier = Modifier) {
    Box(
        modifier
            .clip(RoundedCornerShape(20.dp))
            .background(KlarnaColors.Canvas)
            .border(1.dp, KlarnaColors.Divider, RoundedCornerShape(20.dp))
            .padding(20.dp),
    ) {
        Row(Modifier.fillMaxWidth(), verticalAlignment = Alignment.Top) {
            installments.forEachIndexed { i, item ->
                Column(
                    Modifier.weight(1f),
                    horizontalAlignment = Alignment.CenterHorizontally,
                ) {
                    Box(contentAlignment = Alignment.Center) {
                        // connecting track to the next dot
                        if (i < installments.lastIndex) {
                            Box(
                                Modifier
                                    .offset(x = 28.dp)
                                    .width(64.dp)
                                    .height(2.dp)
                                    .background(if (item.state == InstallmentState.Paid) KlarnaColors.Pink else KlarnaColors.Divider),
                            )
                        }
                        Dot(item.state)
                    }
                    Spacer(Modifier.height(8.dp))
                    Text(item.date, style = KlarnaTextStyles.Meta, color = KlarnaColors.TextSecondary)
                    Text(item.amount, style = KlarnaTextStyles.Meta.copy(fontWeight = FontWeight.Bold), color = KlarnaColors.TextPrimary)
                }
            }
        }
    }
}

@Composable
private fun Dot(state: InstallmentState) {
    when (state) {
        InstallmentState.Paid -> Box(
            Modifier.size(28.dp).clip(CircleShape).background(KlarnaColors.Pink),
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Check, contentDescription = "Paid", tint = KlarnaColors.Black, modifier = Modifier.size(14.dp)) }
        InstallmentState.Next -> Box(Modifier.size(28.dp).clip(CircleShape).background(Color.White).border(2.dp, KlarnaColors.Black, CircleShape))
        InstallmentState.Upcoming -> Box(Modifier.size(28.dp).clip(CircleShape).background(Color.White).border(2.dp, KlarnaColors.Divider, CircleShape))
    }
}
```

### Black Payment-Summary Hero

```kotlin
@Composable
fun KlarnaBlackHero(total: String, nextDate: String, modifier: Modifier = Modifier) {
    Column(
        modifier
            .clip(RoundedCornerShape(24.dp))
            .background(KlarnaColors.Black)
            .padding(24.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Text("TOTAL DUE", style = KlarnaTextStyles.LabelUpper, color = KlarnaColors.Pink)
        Text(total, style = KlarnaTextStyles.AmountHero, color = Color.White)
        Text("Next payment $nextDate", style = KlarnaTextStyles.Meta, color = KlarnaColors.TextTertiary)
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            Icon(Icons.Filled.CreditCard, contentDescription = null, tint = KlarnaColors.Pink, modifier = Modifier.size(15.dp))
            Text("Pay now", style = KlarnaTextStyles.Title, color = KlarnaColors.Pink)
        }
    }
}
```

### Order Row

```kotlin
@Composable
fun OrderRow(
    merchant: String,
    meta: String,
    amount: String,
    status: String,
    statusColor: Color,
    logoUrl: String,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Column {
        Row(
            modifier
                .fillMaxWidth()
                .background(if (pressed) KlarnaColors.SurfacePink else KlarnaColors.Canvas)
                .clickable(interaction, indication = null) {}
                .height(72.dp)
                .padding(horizontal = 20.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            AsyncImage(
                model = logoUrl, contentDescription = null,
                modifier = Modifier.size(44.dp).clip(RoundedCornerShape(12.dp)).background(KlarnaColors.SurfacePink),
                contentScale = ContentScale.Crop,
            )
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(merchant, style = KlarnaTextStyles.Title, color = KlarnaColors.TextPrimary, maxLines = 1, overflow = TextOverflow.Ellipsis)
                Text(meta, style = KlarnaTextStyles.Meta, color = KlarnaColors.TextSecondary, maxLines = 1)
            }
            Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(amount, style = KlarnaTextStyles.Amount, color = KlarnaColors.TextPrimary)
                Text(status, style = KlarnaTextStyles.Caption.copy(fontWeight = FontWeight.Bold), color = statusColor)
            }
        }
        Divider(color = KlarnaColors.Divider, thickness = 1.dp)
    }
}
```

### In-App Shopping Browser Footer (signature)

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.slideInVertically

@Composable
fun PayWithKlarnaFooter(visible: Boolean, onClick: () -> Unit, modifier: Modifier = Modifier) {
    AnimatedVisibility(
        visible = visible,
        enter = slideInVertically(tween(320)) { it },
        modifier = modifier,
    ) {
        val interaction = remember { MutableInteractionSource() }
        val pressed by interaction.collectIsPressedAsState()
        Box(
            Modifier
                .fillMaxWidth()
                .height(56.dp)
                .background(if (pressed) KlarnaColors.PinkPressed else KlarnaColors.Pink)
                .clickable(interaction, indication = null, onClick = onClick),
            contentAlignment = Alignment.Center,
        ) {
            Text("Pay with Klarna", style = KlarnaTextStyles.Button, color = KlarnaColors.Black)
        }
    }
}
```

## 4. Smooth Transition Helper

```kotlin
// Klarna's signature glide — slightly slower easing for nav transitions.
import androidx.compose.animation.core.tween
import androidx.compose.animation.slideInHorizontally
import androidx.compose.animation.slideOutHorizontally

val KlarnaEnter = slideInHorizontally(tween(340)) { it }
val KlarnaExit  = slideOutHorizontally(tween(340)) { -it }
// Apply via NavHost composable(enterTransition = { KlarnaEnter }, exitTransition = { KlarnaExit })
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Klarna's iOS tab bar is opaque white with a hairline warm-grey top border. **Active tint is near-black**; optionally mark the active tab with a soft pink dot.

```kotlin
@Composable
fun KlarnaBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = KlarnaColors.Canvas,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Shop"     to Icons.Filled.ShoppingBag,
            "Payments" to Icons.Filled.CreditCard,
            "Rewards"  to Icons.Filled.CardGiftcard,
            "Profile"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = KlarnaTextStyles.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = KlarnaColors.Black,
                    selectedTextColor   = KlarnaColors.Black,
                    unselectedIconColor = KlarnaColors.TextTertiary,
                    unselectedTextColor = KlarnaColors.TextTertiary,
                    indicatorColor      = KlarnaColors.PinkTint,
                ),
            )
        }
    }
}
```

Place a hairline `Divider(color = KlarnaColors.Divider, thickness = 0.5.dp)` directly above the bar; the checkout sheet and pink footer present above it.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Slide transitions | `slideInHorizontally`/`slideOutHorizontally` `tween(340)` — the unhurried Klarna glide |
| CTA press | `animateFloatAsState` scale 1→0.98 `tween(160)` + `PinkPressed` fill; `HapticFeedbackType.LongPress` |
| Schedule fill | `animateColorAsState(tween(260))` dot background outlined→pink + the preceding track segment |
| Footer reveal | `AnimatedVisibility` + `slideInVertically(tween(320))` from the bottom |
| Amount roll | `AnimatedContent` crossfade on the total string, ~300ms |
| Segmented thumb | `animateDpAsState` pill offset, `tween(260)` ease — deliberately unhurried |

Haptics: prefer `LocalHapticFeedback`. For a softer iOS-like confirmation use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, …)`.

## 7. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Klarna glyphs as vector drawables and load via `ImageVector.vectorResource(...)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Shop (tab) | `bag.fill` | `Icons.Filled.ShoppingBag` |
| Payments (tab) | `creditcard.fill` | `Icons.Filled.CreditCard` |
| Rewards (tab) | `gift.fill` | `Icons.Filled.CardGiftcard` |
| Profile (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| Pay now | `creditcard.fill` | `Icons.Filled.CreditCard` |
| Browse | `magnifyingglass` | `Icons.Filled.Search` |
| Schedule paid | `checkmark` | `Icons.Filled.Check` |
| Order delivered | `shippingbox.fill` | `Icons.Filled.Inventory2` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Notifications | `bell.fill` | `Icons.Filled.Notifications` |
| Help | `questionmark.circle` | `Icons.Filled.HelpOutline` |
| Chevron | `chevron.right` | `Icons.Filled.ChevronRight` |
| Due reminder | `clock` | `Icons.Filled.Schedule` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `AnimatedContent`/`AnimatedVisibility` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the bright canvas wants dark-content system bars (`WindowCompat`). Apply `Scaffold` insets so the checkout sheet/footer clears gesture nav.
- **Font scaling**: `sp` honors the user's scale — keep it on titles, body, amounts. Pin the hero amount, tab labels, uppercase labels, and schedule date/amount text with a fixed `Density` wrapper for alignment under the dots.
- **TalkBack**: announce the total with currency; describe the schedule fully ("Payment 2 of 4, next, due 14 June, 32 pounds 10"); mark "TOTAL DUE" with `Modifier.semantics { heading() }`.
- **Touch targets**: Material minimum is 48.dp. The 56.dp CTA is clear; give the 24.dp tab glyphs and 28.dp schedule dots a 48.dp hit area via padding.
- **Contrast**: near-black `#0B051D` on Klarna Pink `#FFB3C7` passes WCAG AAA — never white on pink. `#6E6878` on white passes AA at 14sp+; bump 11sp labels toward `#5D5866` for strict compliance.
- **Reduced motion**: honor `Settings.Global.ANIMATOR_DURATION_SCALE == 0` by crossfading instead of sliding and setting the schedule/total instantly.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Klarna's brand requires the fixed soft pink `#FFB3C7` and near-black `#0B051D` regardless of wallpaper.
