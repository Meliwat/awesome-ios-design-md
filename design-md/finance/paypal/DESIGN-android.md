# PayPal (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports PayPal's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the dual-blue P-P wordmark, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (PayPal's calm white canvas, the iconic dual-blue P-P overlap, rounded PayPal Sans, card-wallet elevation) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a sheet presentation, `animateIntAsState` instead of `.contentTransition(.numericText())`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for card-art and recipient avatars. No color extraction — PayPal's palette is a fixed dual-blue system, so Palette is not needed. PayPal is light-first with a cobalt-tinted dark mode (since 2020); both schemes are provided.

## 1. Color Tokens

```kotlin
// ui/theme/PayPalColors.kt
import androidx.compose.ui.graphics.Color

object PayPalColors {
    // Brand
    val Blue       = Color(0xFF003087) // primary CTA, active tab, wordmark front
    val Sky        = Color(0xFF0070BA) // secondary accent, link, wordmark back
    val Cobalt     = Color(0xFF001C64) // splash / illustration bg only — keep reserved
    val BlueDark   = Color(0xFF3B82F6) // dark-mode shifted PayPal Blue

    // Canvas & Surfaces (light)
    val Canvas       = Color(0xFFFFFFFF)
    val SurfaceGray  = Color(0xFFF5F7FA)
    val SurfaceGray2 = Color(0xFFEEF1F4)
    val Divider      = Color(0xFFE5E8ED)

    // Text (light)
    val TextPrimary   = Color(0xFF001435) // almost-black, slight blue tint — NOT pure black
    val TextSecondary = Color(0xFF2C2E2F)
    val TextMuted     = Color(0xFF687173)
    val TextTertiary  = Color(0xFF9DA3A6)

    // Semantic
    val Success   = Color(0xFF1C8B43)
    val SuccessBg = Color(0xFFE4F5EA)
    val Error     = Color(0xFFD20021)
    val ErrorBg   = Color(0xFFFCE5E8)
    val Warning   = Color(0xFFFFB81C)
    val WarningBg = Color(0xFFFFF6E0)
    val PendingFg = Color(0xFFA06B00)

    // Activity icon colors
    val IconSent     = Blue
    val IconReceived = Success
    val IconCard     = Sky
    val IconReward   = Warning

    // Dark mode
    val DarkCanvas   = Color(0xFF0A0E1A) // slight blue tint — NOT pure black
    val DarkSurface1 = Color(0xFF141A2A)
    val DarkSurface2 = Color(0xFF1F2740)
    val DarkDivider  = Color(0xFF2A3142)
    val DarkTextPri  = Color(0xFFFFFFFF)
    val DarkTextSec  = Color(0xFFA8AEC4)
}
```

Wire it into both schemes. PayPal is light-first; the dark scheme keeps a cobalt-tinted near-black so the blue identity survives, and PayPal Blue brightens to `#3B82F6` for contrast.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val PayPalLight = lightColorScheme(
    primary        = PayPalColors.Blue,
    onPrimary      = PayPalColors.Canvas,
    secondary      = PayPalColors.Sky,
    background     = PayPalColors.Canvas,
    onBackground   = PayPalColors.TextPrimary,
    surface        = PayPalColors.Canvas,
    onSurface      = PayPalColors.TextPrimary,
    surfaceVariant = PayPalColors.SurfaceGray,
    outline        = PayPalColors.Divider,
    error          = PayPalColors.Error,
)

private val PayPalDark = darkColorScheme(
    primary        = PayPalColors.BlueDark,
    onPrimary      = PayPalColors.Canvas,
    secondary      = PayPalColors.Sky,
    background     = PayPalColors.DarkCanvas,
    onBackground   = PayPalColors.DarkTextPri,
    surface        = PayPalColors.DarkSurface1,
    onSurface      = PayPalColors.DarkTextPri,
    surfaceVariant = PayPalColors.DarkSurface2,
    outline        = PayPalColors.DarkDivider,
    error          = PayPalColors.Error,
)

@Composable
fun PayPalTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) PayPalDark else PayPalLight,
    typography = PayPalTypography,
    content = content,
)
```

## 2. Typography

PayPal Sans Big (display) and PayPal Sans Small (body) are proprietary (Monotype, ~2018). Drop the TTFs in `res/font/` (lowercase, snake_case) and build two families. Fall back to **Inter** (closest free match) — its humanist roundness is the nearest substitute on Android. Tabular figures on every amount: `fontFeatureSettings = "tnum"`.

```kotlin
// ui/theme/PayPalType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val PayPalSansBig = FontFamily(
    Font(R.font.paypal_sans_big_regular, FontWeight.Normal),
    Font(R.font.paypal_sans_big_medium,  FontWeight.Medium),
    Font(R.font.paypal_sans_big_bold,    FontWeight.Bold),
)
val PayPalSansSmall = FontFamily(
    Font(R.font.paypal_sans_small_regular, FontWeight.Normal),
    Font(R.font.paypal_sans_small_medium,  FontWeight.Medium),
    Font(R.font.paypal_sans_small_bold,    FontWeight.Bold),
)

private const val TNUM = "tnum" // tabular — the Activity column must align

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object PayPalText {
    val BalanceHero    = TextStyle(PayPalSansBig,   fontWeight = FontWeight.Bold,   fontSize = 36.sp, lineHeight = 36.sp, letterSpacing = (-0.5).sp, fontFeatureSettings = TNUM)
    val SendAmountHero = TextStyle(PayPalSansBig,   fontWeight = FontWeight.Bold,   fontSize = 56.sp, lineHeight = 56.sp, letterSpacing = (-1.0).sp, fontFeatureSettings = TNUM)
    val ScreenTitle    = TextStyle(PayPalSansBig,   fontWeight = FontWeight.Bold,   fontSize = 24.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val SectionHeader  = TextStyle(PayPalSansBig,   fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val CardTitle      = TextStyle(PayPalSansBig,   fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 21.sp)
    val ActivityAmount = TextStyle(PayPalSansBig,   fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 19.sp, fontFeatureSettings = TNUM)
    val Body           = TextStyle(PayPalSansSmall, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 23.sp)
    val BodySmall      = TextStyle(PayPalSansSmall, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 20.sp)
    val ActivityTitle  = TextStyle(PayPalSansSmall, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 20.sp)
    val ActivitySub    = TextStyle(PayPalSansSmall, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val Meta           = TextStyle(PayPalSansSmall, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val Link           = TextStyle(PayPalSansSmall, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 21.sp)
    val Tab            = TextStyle(PayPalSansSmall, fontWeight = FontWeight.Medium, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Chip           = TextStyle(PayPalSansSmall, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 13.sp)
    val Button         = TextStyle(PayPalSansBig,   fontWeight = FontWeight.Bold,   fontSize = 17.sp, lineHeight = 17.sp)
    val ButtonSmall    = TextStyle(PayPalSansSmall, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 15.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val PayPalTypography = Typography(
    headlineLarge = PayPalText.ScreenTitle,
    titleLarge    = PayPalText.SectionHeader,
    titleMedium   = PayPalText.CardTitle,
    bodyMedium    = PayPalText.Body,
    labelSmall    = PayPalText.Tab,
)
```

## 3. Signature Components

### Balance Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import java.text.NumberFormat
import java.util.Locale

@Composable
fun BalanceCard(
    balance: Double,
    onAddMoney: () -> Unit,
    onTransfer: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val usd = remember { NumberFormat.getCurrencyInstance(Locale.US) }
    Column(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .shadow(8.dp, RoundedCornerShape(16.dp), spotColor = Color.Black.copy(alpha = 0.06f)) // gentle card-wallet lift
            .clip(RoundedCornerShape(16.dp))
            .background(PayPalColors.Canvas)
            .border(1.dp, PayPalColors.Divider, RoundedCornerShape(16.dp))
            .padding(20.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Text("PayPal balance", style = PayPalText.ActivitySub, color = PayPalColors.TextMuted)
        AnimatedBalance(balance, usd) // numeric roll — see §4
        Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
            PayPalPrimaryButton("Add Money", onAddMoney, Modifier.weight(1f), height = 44.dp)
            PayPalSecondaryButton("Transfer", onTransfer, Modifier.weight(1f))
        }
    }
}
```

### Activity Row

```kotlin
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.ui.graphics.vector.ImageVector

enum class ActivityKind { Sent, Received, Card, Reward }

@Composable
fun ActivityRow(
    name: String,
    subtitle: String,
    amount: Double,
    kind: ActivityKind,
    received: Boolean,
    modifier: Modifier = Modifier,
) {
    val usd = remember { NumberFormat.getCurrencyInstance(Locale.US) }
    val (iconColor, glyph) = when (kind) {
        ActivityKind.Sent     -> PayPalColors.IconSent to Icons.Filled.ArrowOutward
        ActivityKind.Received -> PayPalColors.IconReceived to Icons.Filled.SouthWest
        ActivityKind.Card     -> PayPalColors.IconCard to Icons.Filled.CreditCard
        ActivityKind.Reward   -> PayPalColors.IconReward to Icons.Filled.Redeem
    }
    Box(modifier.fillMaxWidth().background(PayPalColors.Canvas)) {
        Row(
            Modifier.fillMaxWidth().heightIn(min = 68.dp).padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(
                Modifier.size(40.dp).clip(CircleShape).background(iconColor),
                contentAlignment = Alignment.Center,
            ) {
                // shape carries direction — never rely on color alone
                Icon(glyph, contentDescription = null, tint = Color.White, modifier = Modifier.size(18.dp))
            }
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(name, style = PayPalText.ActivityTitle, color = PayPalColors.TextPrimary)
                Text(subtitle, style = PayPalText.ActivitySub, color = PayPalColors.TextMuted)
            }
            Text(
                (if (received) "+" else "") + usd.format(amount),
                style = PayPalText.ActivityAmount,
                color = if (received) PayPalColors.Success else PayPalColors.TextPrimary,
            )
        }
        Box(Modifier.align(Alignment.BottomCenter).fillMaxWidth().height(0.5.dp).background(PayPalColors.Divider))
    }
}
```

### Primary / Secondary CTA (soft pill, radius = half-height)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun PayPalPrimaryButton(
    label: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    height: Dp = 48.dp,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        targetValue = if (pressed) 0.98f else 1f,
        animationSpec = spring(dampingRatio = 0.7f, stiffness = 700f),
        label = "ctaScale",
    )
    val haptics = LocalHapticFeedback.current
    Box(
        modifier
            .fillMaxWidth()
            .height(height)
            .scale(scale)
            .clip(RoundedCornerShape(percent = 50)) // radius = half-height → soft pill
            .background(if (pressed) PayPalColors.Cobalt else PayPalColors.Blue)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = PayPalText.Button, color = Color.White)
    }
}

@Composable
fun PayPalSecondaryButton(
    label: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    height: Dp = 48.dp,
) {
    Box(
        modifier
            .fillMaxWidth()
            .height(height)
            .clip(RoundedCornerShape(percent = 50))
            .border(1.5.dp, PayPalColors.Blue, RoundedCornerShape(percent = 50))
            .clickable(onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = PayPalText.ButtonSmall, color = PayPalColors.Blue)
    }
}
```

### Activity Filter Chip & Status Pill

```kotlin
@Composable
fun ActivityFilterChip(label: String, selected: Boolean, onClick: () -> Unit) {
    Box(
        Modifier
            .clip(RoundedCornerShape(16.dp))
            .background(if (selected) PayPalColors.Blue else PayPalColors.SurfaceGray2)
            .clickable(onClick = onClick)
            .padding(horizontal = 16.dp, vertical = 8.dp),
    ) {
        Text(label, style = PayPalText.Chip, color = if (selected) Color.White else PayPalColors.TextMuted)
    }
}

enum class PayPalStatus { Completed, Pending, Failed, Refunded }

@Composable
fun StatusPill(status: PayPalStatus) {
    val (bg, fg, label) = when (status) {
        PayPalStatus.Completed -> Triple(PayPalColors.SuccessBg, PayPalColors.Success, "Completed")
        PayPalStatus.Pending   -> Triple(PayPalColors.WarningBg, PayPalColors.PendingFg, "Pending")
        PayPalStatus.Failed    -> Triple(PayPalColors.ErrorBg, PayPalColors.Error, "Failed")
        PayPalStatus.Refunded  -> Triple(PayPalColors.SurfaceGray2, PayPalColors.TextMuted, "Refunded")
    }
    Box(
        Modifier.clip(RoundedCornerShape(4.dp)).background(bg).padding(horizontal = 10.dp, vertical = 4.dp),
    ) {
        Text(label, style = PayPalText.Chip.copy(fontSize = 12.sp), color = fg)
    }
}
```

## 4. Dual-Blue P-P Wordmark + Live Balance Roll

PayPal's two brand-load-bearing dynamics: the **dual-blue P-P logomark** (never a single color) and the **balance number that rolls** to its new value after a Send. iOS uses `.contentTransition(.numericText())`; Android's analog is `animateIntAsState` on the cents (rendered with `AnimatedContent` per character for the roll feel).

### P-P Wordmark

```kotlin
import androidx.compose.ui.graphics.graphicsLayer
import androidx.compose.ui.unit.TextUnit

@Composable
fun PayPalWordmark(size: TextUnit = 32.sp, modifier: Modifier = Modifier) {
    Box(modifier, contentAlignment = Alignment.Center) {
        // Back P — PayPal Sky, italic, shifted left
        Text(
            "P",
            style = PayPalText.Button.copy(fontFamily = PayPalSansBig, fontSize = size, fontWeight = FontWeight.Bold),
            color = PayPalColors.Sky,
            modifier = Modifier.graphicsLayer { translationX = -size.toPx() * 0.18f; rotationZ = -8f },
        )
        // Front P — PayPal Blue, italic, shifted right; the overlap darkens to a blue blend
        Text(
            "P",
            style = PayPalText.Button.copy(fontFamily = PayPalSansBig, fontSize = size, fontWeight = FontWeight.Bold),
            color = PayPalColors.Blue,
            modifier = Modifier.graphicsLayer { translationX = size.toPx() * 0.10f; rotationZ = -8f },
        )
    }
}
```

> NEVER render the wordmark in a single color — the Sky-behind / Blue-front overlap with the darker blended middle zone IS the PayPal brand. For pixel-exact parity ship the official two-tone mark as a vector drawable and load via `ImageVector.vectorResource(R.drawable.paypal_pp)`.

### Live balance roll

```kotlin
import androidx.compose.animation.AnimatedContent
import androidx.compose.animation.core.tween
import androidx.compose.animation.slideInVertically
import androidx.compose.animation.slideOutVertically
import androidx.compose.animation.togetherWith

@Composable
fun AnimatedBalance(balance: Double, formatter: NumberFormat) {
    val target = formatter.format(balance)
    AnimatedContent(
        targetState = target,
        transitionSpec = {
            (slideInVertically(tween(400)) { it } togetherWith slideOutVertically(tween(400)) { -it })
        },
        label = "balanceRoll",
    ) { value ->
        Text(value, style = PayPalText.BalanceHero, color = PayPalColors.TextPrimary)
    }
}
```

The same `BalanceHero`/`SendAmountHero` styles drive the Send Money fullscreen: a calm centered `$` (muted) + 56.sp tabular amount, a "USD" `Meta` chip below, and a live `PayPalPrimaryButton("Send ${'$'}X.XX")` whose label recomposes with the entered amount.

## 5. Navigation

PayPal has a 5-tab bottom bar (Home / Send / Wallet / Activity / Finances). Use Material 3 `NavigationBar`; the iOS bar is an opaque white surface with a 0.5pt hairline — no blur involved, so the opaque `Canvas` surface is exact. Active is PayPal Blue (icon + label), inactive `#687173`. There is no Material tint pill.

```kotlin
@Composable
fun PayPalBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    val haptics = LocalHapticFeedback.current
    NavigationBar(
        containerColor = PayPalColors.Canvas,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Send"     to Icons.Filled.Send,
            "Wallet"   to Icons.Filled.AccountBalanceWallet,
            "Activity" to Icons.Filled.Schedule,
            "Finances" to Icons.Filled.ShowChart,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // .selection
                    onSelect(i)
                },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = PayPalText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = PayPalColors.Blue,
                    selectedTextColor = PayPalColors.Blue,
                    unselectedIconColor = PayPalColors.TextMuted,
                    unselectedTextColor = PayPalColors.TextMuted,
                    indicatorColor = Color.Transparent, // no Material pill — PayPal has none
                ),
            )
        }
    }
}
```

The top nav carries the `PayPalWordmark()` (24–32.sp) leading, with a notification bell + help "?" trailing, over an opaque `Canvas` surface with a 0.5dp `Divider` bottom hairline. Confirm-Send / Filters use `ModalBottomSheet` (16.dp top corners, 4×32.dp drag handle, `Color.Black.copy(alpha = 0.6f)` scrim).

## 6. Motion

PayPal motion is restrained and reassuring — gentle springs, no confetti (it's institutional).

| Moment | Compose recipe |
|--------|----------------|
| Send tap | scale 1 → 0.97 → 1 `spring(dampingRatio = 0.7f, stiffness = 700f)` + `HapticFeedbackType.LongPress` (medium) |
| Send success | success screen `slideInVertically(tween(350))`; checkmark `Animatable` scale 0.5 → 1 with 100ms start delay + `HapticFeedbackType.Confirm` |
| Activity row press | background `animateColorAsState` to `#F5F7FA` `tween(150)`, no scale |
| Tab switch | icon outline→filled cross-fade `tween(200)`, label color `animateColorAsState` `#687173` → `#003087`, `.selection` haptic |
| Balance update | `AnimatedContent` `slideIn/OutVertically(tween(400))` per §4 |
| Modal sheet | `ModalBottomSheet` velocity-based drag-to-dismiss (Material default) |

```kotlin
// Send success checkmark — scale-in with a short delay
@Composable
fun SendSuccessCheck(visible: Boolean) {
    val scale = remember { Animatable(0.5f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(visible) {
        if (visible) {
            haptics.performHapticFeedback(HapticFeedbackType.Confirm) // .success
            kotlinx.coroutines.delay(100)
            scale.animateTo(1f, spring(dampingRatio = 0.7f, stiffness = 500f))
        }
    }
    Box(
        Modifier.size(80.dp).scale(scale.value).clip(CircleShape).background(PayPalColors.Blue),
        contentAlignment = Alignment.Center,
    ) {
        Icon(Icons.Filled.Check, contentDescription = "Sent", tint = Color.White, modifier = Modifier.size(40.dp))
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For the medium Send impact use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(15, VibrationEffect.DEFAULT_AMPLITUDE)`.

## 7. Icons

PayPal ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. The dual-blue P-P wordmark is NOT an icon — compose two text Ps (§4) or ship the official two-tone vector drawable.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Send (tab) | `paperplane` / `.fill` | `Icons.Filled.Send` |
| Wallet (tab) | `wallet.pass` / `.fill` | `Icons.Filled.AccountBalanceWallet` |
| Activity (tab) | `clock` / `.fill` | `Icons.Filled.Schedule` |
| Finances (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.ShowChart` |
| Activity: Sent | `arrow.up.right` | `Icons.Filled.ArrowOutward` |
| Activity: Received | `arrow.down.left` | `Icons.Filled.SouthWest` |
| Activity: Card | `creditcard.fill` | `Icons.Filled.CreditCard` |
| Activity: Reward | `gift.fill` | `Icons.Filled.Redeem` |
| Notifications | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| Help | `questionmark.circle` | `Icons.Filled.HelpOutline` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Success | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Failed | `xmark.circle.fill` | `Icons.Filled.Cancel` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `AnimatedContent` + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the white canvas wants dark-content system bars (light-content in dark mode). The top nav respects the camera cutout; the bottom bar pins above the gesture nav via `Scaffold`/`windowInsetsPadding`; `ModalBottomSheet` respects the bottom inset automatically.
- **Tabular digits**: keep `fontFeatureSettings = "tnum"` on balance, activity amounts, and percentages so the Activity feed's $-column aligns perfectly.
- **Font scaling**: `sp` honors the user's font scale — scale body, activity subtitles, secondary labels. Clamp the balance hero (36sp → 48sp max) and Send amount (56sp → 72sp max); pin 11sp tab labels and 13sp filter chips by deriving from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: merge each activity row with `Modifier.semantics(mergeDescendants = true)` and label it "Sent to Sarah Kim, $24.50, yesterday at 3:42 PM, completed"; label the balance card "PayPal balance, $1,247 and 92 cents". The directional icon shape (out-arrow vs SouthWest) carries meaning for color-blind users — never rely on color alone.
- **Touch targets**: Material guidance is 48.dp minimum. The 48.dp primary CTA and 68.dp activity rows clear it; give 32.dp filter chips a 48.dp hit width and icon-only nav buttons full `IconButton` padding.
- **Contrast**: `#001435` on `#FFFFFF` exceeds WCAG AA at all sizes; `#687173` on white meets AA at 14sp+; PayPal Blue `#003087` on white is AAA. Validate the 11sp tab labels and tinted status pills (e.g. `#A06B00` on `#FFF6E0`) with a contrast checker.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, skip the modal-sheet bounce and the checkmark scale-in (cross-fade instead); render the balance change instantly without the roll.
- **Dark mode**: switch to `#0A0E1A` (cobalt-tinted, NOT pure black) and brighten PayPal Blue to `#3B82F6` for contrast — the blue identity must survive in dark. Do **not** enable Material You `dynamicColorScheme()` — PayPal's dual-blue brand is fixed and trust-load-bearing; it must not retint to the user's wallpaper. Keep Cobalt `#001C64` reserved for splash/illustration only.
