# Wise (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Wise's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Wise's bright canvas, forest structural ink, bright-green action, the fee-transparency card, the multi-currency stack) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `sp`/`dp` instead of `pt`, `AnimatedContent` for the number roll-up.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/WiseColors.kt
import androidx.compose.ui.graphics.Color

object WiseColors {
    // Canvas & Surfaces
    val Canvas        = Color(0xFFFFFFFF)
    val Surface       = Color(0xFFF7F7F7)
    val SurfaceSunken = Color(0xFFEFEFEF)
    val Divider       = Color(0xFFE5E5E5)
    val Border        = Color(0xFFD2D2D2)

    // Text
    val TextPrimary   = Color(0xFF0E0F0C)
    val TextSecondary = Color(0xFF6B6F66)
    val TextTertiary  = Color(0xFF9A9D95)

    // Brand
    val Bright        = Color(0xFF9FE870)
    val BrightPressed = Color(0xFF8AD45C)
    val BrightTint    = Color(0xFFEAF9DC)
    val Forest        = Color(0xFF163300)
    val ForestHover   = Color(0xFF0E2200)

    // Semantic
    val Success = Color(0xFF2F8F4E)
    val Pending = Color(0xFFB5781E)
    val Error   = Color(0xFFD4332B)
}
```

Wire it into a Material 3 `lightColorScheme`. Wise is light-first; provide a dark scheme only if you target system dark mode (keep `Bright` unchanged with `Forest` content).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val WiseScheme = lightColorScheme(
    primary          = WiseColors.Bright,
    onPrimary        = WiseColors.Forest,   // forest on bright green, never white
    primaryContainer = WiseColors.BrightTint,
    secondary        = WiseColors.Forest,
    onSecondary      = Color.White,
    background        = WiseColors.Canvas,
    onBackground      = WiseColors.TextPrimary,
    surface           = WiseColors.Canvas,
    onSurface         = WiseColors.TextPrimary,
    surfaceVariant    = WiseColors.Surface,
    outline           = WiseColors.Border,
    outlineVariant    = WiseColors.Divider,
    error             = WiseColors.Error,
)

@Composable
fun WiseTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = WiseScheme, typography = WiseTypography, content = content)
```

## 2. Typography

Wise Sans is proprietary. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto); Inter is the closest substitute if bundled.

```kotlin
// ui/theme/WiseType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val WiseSans = FontFamily(
    Font(R.font.wise_sans_regular,   FontWeight.Normal),    // 400
    Font(R.font.wise_sans_semibold,  FontWeight.SemiBold),  // 600
    Font(R.font.wise_sans_extrabold, FontWeight.ExtraBold), // 800
)

private const val TNUM = "tnum"

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object WiseText {
    val Balance    = TextStyle(WiseSans, fontWeight = FontWeight.ExtraBold, fontSize = 40.sp, lineHeight = 42.sp, letterSpacing = (-0.6).sp, fontFeatureSettings = TNUM)
    val TitleLarge = TextStyle(WiseSans, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.5).sp)
    val Section    = TextStyle(WiseSans, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Currency   = TextStyle(WiseSans, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp, fontFeatureSettings = TNUM)
    val Subsection = TextStyle(WiseSans, fontWeight = FontWeight.SemiBold,  fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Amount     = TextStyle(WiseSans, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 19.sp, fontFeatureSettings = TNUM)
    val Title      = TextStyle(WiseSans, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 21.sp)
    val Body       = TextStyle(WiseSans, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 22.sp)
    val Button     = TextStyle(WiseSans, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 20.sp)
    val Meta       = TextStyle(WiseSans, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 17.sp)
    val LabelUpper = TextStyle(WiseSans, fontWeight = FontWeight.ExtraBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Tab        = TextStyle(WiseSans, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.1.sp)
    val Caption    = TextStyle(WiseSans, fontWeight = FontWeight.Normal,    fontSize = 11.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val WiseTypography = Typography(
    headlineLarge = WiseText.TitleLarge,
    headlineSmall = WiseText.Section,
    titleMedium   = WiseText.Title,
    bodyMedium    = WiseText.Body,
    labelSmall    = WiseText.Tab,
)
```

## 3. Signature Components

### Primary CTA (Bright Green)

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun WisePrimaryButton(
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
            .clip(RoundedCornerShape(16.dp))
            .background(if (pressed) WiseColors.BrightPressed else WiseColors.Bright)
            .heightIn(min = 52.dp)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS light impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = WiseText.Button, color = WiseColors.Forest) // forest, never white
    }
}

@Composable
fun WiseForestButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        modifier
            .clip(RoundedCornerShape(16.dp))
            .background(if (pressed) WiseColors.ForestHover else WiseColors.Forest)
            .heightIn(min = 52.dp)
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) { Text(text, style = WiseText.Button, color = Color.White) }
}
```

### Forest Account Hero (number roll-up)

```kotlin
import androidx.compose.animation.AnimatedContent
import androidx.compose.animation.slideInVertically
import androidx.compose.animation.slideOutVertically
import androidx.compose.animation.togetherWith

@Composable
fun ForestAccountHero(total: String, modifier: Modifier = Modifier) {
    Column(
        modifier
            .clip(RoundedCornerShape(20.dp))
            .background(WiseColors.ForestHover)
            .padding(24.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Text("TOTAL BALANCE", style = WiseText.LabelUpper, color = WiseColors.Bright)
        AnimatedContent(
            targetState = total,
            transitionSpec = {
                (slideInVertically { it / 2 } togetherWith slideOutVertically { -it / 2 })
            },
            label = "balanceRoll",
        ) { value ->
            Text(value, style = WiseText.Balance, color = Color.White)
        }
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            Icon(Icons.Filled.Add, contentDescription = null, tint = WiseColors.Bright, modifier = Modifier.size(16.dp))
            Text("Add money", style = WiseText.Title, color = WiseColors.Bright)
        }
    }
}
```

### Currency Balance Row

```kotlin
@Composable
fun CurrencyRow(
    flag: String,
    code: String,
    name: String,
    balance: String,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Column {
        Row(
            modifier
                .fillMaxWidth()
                .background(if (pressed) WiseColors.Surface else WiseColors.Canvas)
                .clickable(interaction, indication = null) {}
                .height(64.dp)
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(
                Modifier.size(32.dp).clip(CircleShape).background(WiseColors.Surface),
                contentAlignment = Alignment.Center,
            ) { Text(flag, fontSize = 18.sp) }
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(code, style = WiseText.Title, color = WiseColors.TextPrimary)
                Text(name, style = WiseText.Meta, color = WiseColors.TextSecondary)
            }
            Text(balance, style = WiseText.Currency, color = WiseColors.TextPrimary)
        }
        Divider(color = WiseColors.Divider, thickness = 1.dp)
    }
}
```

### Fee-Transparency Card (signature)

```kotlin
data class FeeLine(val label: String, val value: String, val emphasized: Boolean = false)

@Composable
fun FeeBreakdownCard(lines: List<FeeLine>, modifier: Modifier = Modifier) {
    Column(
        modifier
            .clip(RoundedCornerShape(16.dp))
            .background(WiseColors.Canvas)
            .border(1.dp, WiseColors.Divider, RoundedCornerShape(16.dp))
            .padding(20.dp),
    ) {
        lines.forEachIndexed { i, line ->
            Row(
                Modifier
                    .fillMaxWidth()
                    .then(if (line.emphasized) Modifier.clip(RoundedCornerShape(10.dp)).background(WiseColors.BrightTint) else Modifier)
                    .padding(horizontal = if (line.emphasized) 12.dp else 0.dp, vertical = 12.dp),
                horizontalArrangement = Arrangement.SpaceBetween,
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Text(
                    line.label,
                    style = if (line.emphasized) WiseText.Title else WiseText.Body,
                    color = if (line.emphasized) WiseColors.Forest else WiseColors.TextSecondary,
                )
                Text(
                    line.value,
                    style = if (line.emphasized) WiseText.Subsection else WiseText.Amount,
                    color = if (line.emphasized) WiseColors.Forest else WiseColors.TextPrimary,
                )
            }
            if (i < lines.lastIndex && !line.emphasized) {
                Divider(color = WiseColors.Divider, thickness = 1.dp)
            }
        }
    }
}
```

### Send-Money Stepper (signature)

```kotlin
@Composable
fun SendStepper(steps: List<String>, current: Int, modifier: Modifier = Modifier) {
    Row(modifier.fillMaxWidth(), verticalAlignment = Alignment.CenterVertically) {
        steps.forEachIndexed { i, _ ->
            val done = i < current
            val active = i == current
            Box(
                Modifier
                    .size(28.dp)
                    .clip(CircleShape)
                    .background(if (done) WiseColors.Bright else if (active) WiseColors.Forest else WiseColors.Divider),
                contentAlignment = Alignment.Center,
            ) {
                if (done) {
                    Icon(Icons.Filled.Check, contentDescription = null, tint = WiseColors.Forest, modifier = Modifier.size(14.dp))
                } else {
                    Text(
                        "${i + 1}",
                        style = WiseText.Title.copy(fontSize = 13.sp),
                        color = if (active) Color.White else WiseColors.TextSecondary,
                    )
                }
            }
            if (i < steps.lastIndex) {
                Box(
                    Modifier
                        .weight(1f)
                        .height(2.dp)
                        .background(if (done) WiseColors.Forest else WiseColors.Divider),
                )
            }
        }
    }
}
```

## 4. Number Roll-Up Helper

```kotlin
// Animate a numeric balance with AnimatedContent + vertical slide; ~500ms.
import androidx.compose.animation.core.tween

@Composable
fun RollingBalance(value: String) {
    AnimatedContent(
        targetState = value,
        transitionSpec = {
            slideInVertically(tween(500)) { it } togetherWith slideOutVertically(tween(500)) { -it }
        },
        label = "rollingBalance",
    ) { Text(it, style = WiseText.Balance, color = Color.White) }
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Wise's iOS tab bar is opaque white with a hairline top border. **Active tint is forest**; optionally mark the active tab with a bright-green dot.

```kotlin
@Composable
fun WiseBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = WiseColors.Canvas,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"       to Icons.Filled.Home,
            "Card"       to Icons.Filled.CreditCard,
            "Recipients" to Icons.Filled.People,
            "Payments"   to Icons.Filled.SwapHoriz,
            "Account"    to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = WiseText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = WiseColors.Forest,
                    selectedTextColor   = WiseColors.Forest,
                    unselectedIconColor = WiseColors.TextSecondary,
                    unselectedTextColor = WiseColors.TextSecondary,
                    indicatorColor      = WiseColors.BrightTint,
                ),
            )
        }
    }
}
```

Place a hairline `Divider(color = WiseColors.Divider, thickness = 0.5.dp)` directly above the bar; the send flow's sticky "Continue" sits in the `Scaffold` `bottomBar` slot above it.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Number roll-up | `AnimatedContent` + `slideInVertically`/`slideOutVertically` `tween(500)` on the balance string |
| CTA press | `animateFloatAsState` scale 1→0.98 `tween(150)` + `BrightPressed` fill; `HapticFeedbackType.LongPress` |
| Stepper advance | animate dot background + rail color with `animateColorAsState(tween(200))` on `current` |
| Rate ticker pulse | `rememberInfiniteTransition` driving a small dot's alpha 0.3↔1, `tween(1000)` reverse |
| Fee card reveal | stagger rows with `AnimatedVisibility` + `fadeIn` + per-row `delayMillis = i * 40` |
| Segmented thumb | `animateDpAsState` pill offset, `tween(220)` ease-in-out |

Haptics: prefer `LocalHapticFeedback`. For a softer iOS-like confirmation use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, …)`.

## 7. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Wise glyphs as vector drawables and load via `ImageVector.vectorResource(...)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Card (tab) | `creditcard.fill` | `Icons.Filled.CreditCard` |
| Recipients (tab) | `person.2.fill` | `Icons.Filled.People` |
| Payments (tab) | `arrow.left.arrow.right` | `Icons.Filled.SwapHoriz` |
| Account (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| Send | `paperplane.fill` | `Icons.Filled.Send` |
| Add money | `plus` | `Icons.Filled.Add` |
| Request | `arrow.down.left` | `Icons.Filled.SouthWest` |
| Convert | `arrow.left.arrow.right.circle` | `Icons.Filled.SwapHorizontalCircle` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Notifications | `bell.fill` | `Icons.Filled.Notifications` |
| Statement | `doc.text` | `Icons.Filled.Description` |
| Step complete | `checkmark` | `Icons.Filled.Check` |
| Money in | `arrow.down.left` | `Icons.Filled.SouthWest` |
| Rate up / down | `arrow.up` / `arrow.down` | `Icons.Filled.ArrowUpward` / `ArrowDownward` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `AnimatedContent` and modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the bright canvas wants dark-content system bars (`WindowCompat`). Apply `Scaffold` insets so the send flow's sticky bar clears gesture nav.
- **Font scaling**: `sp` honors the user's scale — keep it on titles, body, amounts. Pin the hero total, tab labels, uppercase labels, and currency-row figures with a fixed `Density` wrapper for column alignment.
- **TalkBack**: announce the total with currency; read each fee line as "label, value" and mark the "Recipient gets" row with `Modifier.semantics { heading() }`; announce stepper state ("Step 2 of 4, Amount").
- **Touch targets**: Material minimum is 48.dp. The 52.dp CTA is clear; give the 24.dp tab glyphs and 28.dp stepper dots a 48.dp hit area via padding.
- **Contrast**: forest `#163300` on bright green `#9FE870` passes WCAG AAA — never white on bright green. `#6B6F66` on white passes AA at 14sp+; bump 11sp labels toward `#5A5E55` for strict compliance.
- **Reduced motion**: honor `Settings.Global.ANIMATOR_DURATION_SCALE == 0` by setting the balance instantly and crossfading the stepper instead of sliding.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Wise's brand requires the fixed forest `#163300` and bright green `#9FE870` regardless of wallpaper.
