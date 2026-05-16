# Cash App (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Cash App's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the signature $-amount keypad, the matte Cash Card, and the icon-only tab bar.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Cash App's pure-black matte canvas, the single violently-saturated Cash Green, the giant Cash Sans Mono keypad, black-on-green Pay button, icon-only tabs) while making everything idiomatic Android — a `Box`-grid keypad instead of `_onButtonGesture`, `AnimatedContent` for the numeric digit shrink, `NavigationBar` with hidden labels, `sp`/`dp` instead of `pt`. Cash App is flat — no `shadow`, depth is surface-color contrast only.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. No remote images in the core flows and no color extraction, so neither Coil nor `androidx.palette` is required (add Coil only if you render user-uploaded avatars).

## 1. Color Tokens

```kotlin
// ui/theme/CashColors.kt
import androidx.compose.ui.graphics.Color

object CashColors {
    // Canvas & Surfaces — pure black, depth via fill contrast (no shadows)
    val Canvas    = Color(0xFF000000) // every screen
    val Surface1  = Color(0xFF0F0F0F) // sheets, cards, row press
    val Surface2  = Color(0xFF1A1A1A) // input fill, keypad press, Request button
    val Divider   = Color(0xFF1F1F1F) // hairline between rows
    val Hairline  = Color(0xFF2A2A2A) // sheet grabber, sparing dividers

    // Text
    val TextPrimary   = Color(0xFFFFFFFF) // pure white — no off-white
    val TextSecondary = Color(0xFF9E9E9E) // "Sent to", date stamps
    val TextTertiary  = Color(0xFF6E6E6E) // disabled, placeholder
    val TextMutedGreen = Color(0xFF005C16) // faint "$0" ghost when empty

    // Brand
    val Green        = Color(0xFF00D632) // THE accent — Pay CTA, valid amount, active tab
    val GreenPressed = Color(0xFF00B829) // pressed Pay
    val GreenDim     = Color(0xFF008C20) // disabled Pay, secondary green
    val BitcoinOrange = Color(0xFFF7931A) // Bitcoin tab + BTC line only
    val BoostRed     = Color(0xFFFF453A) // declines, Stocks down

    // Semantic
    val Success = Color(0xFF00D632)       // == brand green
    val Error   = Color(0xFFFF453A)
    val Warning = Color(0xFFFFA300)       // pending Activity rows

    // Light mode (accessibility-only — brand lives in the dark)
    val LightCanvas   = Color(0xFFFFFFFF)
    val LightSurface1 = Color(0xFFF5F5F5)
    val LightSurface2 = Color(0xFFEEEEEE)
    val LightText     = Color(0xFF000000)
    val LightTextSec  = Color(0xFF6E6E6E)
    val GreenLight    = Color(0xFF00B829) // darkened for AA on white

    // Random saturated avatar background colors (no-photo fallback) — loud by design
    val AvatarColors = listOf(
        Color(0xFFFF6B9D), // pink
        Color(0xFFFF953C), // orange
        Color(0xFF619AFF), // blue
        Color(0xFF5BD47B), // green
        Color(0xFFAA77FF), // purple
        Color(0xFFFFCA4C), // yellow
    )
}
```

Wire it into a Material 3 `darkColorScheme` — Cash App is dark-first; light mode is accessibility-only and reuses identical mono numerals.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val CashScheme = darkColorScheme(
    primary        = CashColors.Green,
    onPrimary      = Color(0xFF000000),   // intentional: BLACK text on green, not white
    background     = CashColors.Canvas,
    onBackground   = CashColors.TextPrimary,
    surface        = CashColors.Surface1,
    onSurface      = CashColors.TextPrimary,
    surfaceVariant = CashColors.Surface2,
    outline        = CashColors.Divider,
    error          = CashColors.Error,
)

@Composable
fun CashTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = CashScheme, typography = CashTypography, content = content)
```

## 2. Typography

Cash Sans and Cash Sans Mono are proprietary (drawn in-house for Block). Drop the TTFs in `res/font/` (lowercase, snake_case). Fall back to Roboto for the sans and a monospace family for amounts — Inter / JetBrains Mono are closer brand substitutes if you can bundle them. **Mono for money, sans for everything else** — non-negotiable.

```kotlin
// ui/theme/CashType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val CashSans = FontFamily(
    Font(R.font.cash_sans_regular, FontWeight.Normal), // 400
    Font(R.font.cash_sans_medium,  FontWeight.Medium), // 500
    Font(R.font.cash_sans_bold,    FontWeight.Bold),   // 700
    Font(R.font.cash_sans_black,   FontWeight.Black),  // 900
)
val CashSansMono = FontFamily(
    Font(R.font.cash_sans_mono_regular, FontWeight.Normal),
    Font(R.font.cash_sans_mono_medium,  FontWeight.Medium),
    Font(R.font.cash_sans_mono_bold,    FontWeight.Bold),
)

private const val TNUM = "tnum" // tabular numerals — every amount aligns vertically

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, same weights/tracking)
object CashText {
    val AmountHero    = TextStyle(CashSansMono, fontWeight = FontWeight.Bold,   fontSize = 96.sp, lineHeight = 96.sp, letterSpacing = (-2).sp,   fontFeatureSettings = TNUM)
    val AmountReceive = TextStyle(CashSansMono, fontWeight = FontWeight.Bold,   fontSize = 64.sp, lineHeight = 64.sp, letterSpacing = (-1.5).sp, fontFeatureSettings = TNUM)
    val Balance       = TextStyle(CashSansMono, fontWeight = FontWeight.Bold,   fontSize = 48.sp, lineHeight = 48.sp, letterSpacing = (-1).sp,   fontFeatureSettings = TNUM)
    val ScreenTitle   = TextStyle(CashSans,     fontWeight = FontWeight.Black,  fontSize = 28.sp, lineHeight = 31.sp, letterSpacing = (-0.5).sp)
    val SectionHeader = TextStyle(CashSans,     fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 25.sp, letterSpacing = (-0.3).sp)
    val CashtagLarge  = TextStyle(CashSansMono, fontWeight = FontWeight.Medium, fontSize = 22.sp, lineHeight = 26.sp, fontFeatureSettings = TNUM)
    val CardLabel     = TextStyle(CashSans,     fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 21.sp)
    val Body          = TextStyle(CashSans,     fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 21.sp)
    val Meta          = TextStyle(CashSans,     fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val Button        = TextStyle(CashSans,     fontWeight = FontWeight.Bold,   fontSize = 17.sp, lineHeight = 17.sp)
    val ButtonSmall   = TextStyle(CashSans,     fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 15.sp)
    val AllCaps       = TextStyle(CashSans,     fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 1.5.sp)
    val AmountRow     = TextStyle(CashSansMono, fontWeight = FontWeight.Medium, fontSize = 17.sp, lineHeight = 17.sp, fontFeatureSettings = TNUM)
    val CashtagInline = TextStyle(CashSansMono, fontWeight = FontWeight.Medium, fontSize = 15.sp, lineHeight = 20.sp, fontFeatureSettings = TNUM)
    val KeypadDigit   = TextStyle(CashSansMono, fontWeight = FontWeight.Normal, fontSize = 32.sp, lineHeight = 32.sp, fontFeatureSettings = TNUM)
    val AvatarInit    = TextStyle(CashSans,     fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 22.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val CashTypography = Typography(
    displayLarge  = CashText.Balance,
    headlineLarge = CashText.ScreenTitle,
    titleLarge    = CashText.SectionHeader,
    titleMedium   = CashText.CardLabel,
    bodyMedium    = CashText.Body,
    labelSmall    = CashText.AllCaps,
)
```

The `tnum` feature is baked into every monetary style so amounts align in a vertical list — non-negotiable for the Activity feed and keypad.

## 3. Signature Components

### The $-Amount Keypad (Pay screen — the hero)

This is the single most important screen. Giant `$` + amount fills the upper ~40%, auto-shrinking as digits add; a 3×4 keypad fills the lower half; Request (gray) + Pay (green, black text) sit below.

```kotlin
import androidx.compose.animation.AnimatedContent
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.animation.togetherWith
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.automirrored.filled.Backspace
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun AmountKeypad(
    amount: String,                 // raw, e.g. "12.50"
    onAmountChange: (String) -> Unit,
    onPay: () -> Unit,
    onRequest: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val display = amount.ifEmpty { "0" }
    // Auto-shrink as more digits appear (96 → 80 → 64 → 52)
    val heroSize = when (display.length) {
        in 0..3 -> 96.sp
        4 -> 80.sp
        5 -> 64.sp
        else -> 52.sp
    }
    val valid = amount.isNotEmpty()
    val haptics = LocalHapticFeedback.current

    Column(modifier.fillMaxSize().background(CashColors.Canvas)) {
        // Top: giant amount with numeric content transition
        Row(
            Modifier.fillMaxWidth().padding(top = 64.dp),
            horizontalArrangement = Arrangement.Center,
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text("$",
                style = CashText.AmountHero.copy(fontSize = heroSize * 0.67f),
                color = CashColors.TextSecondary)
            Spacer(Modifier.width(4.dp))
            AnimatedContent(
                targetState = display,
                transitionSpec = { (fadeIn() + scaleIn(initialScale = 0.9f)) togetherWith fadeOut() },
                label = "amount",
            ) { value ->
                Text(value, style = CashText.AmountHero.copy(fontSize = heroSize),
                    color = CashColors.TextPrimary)
            }
        }
        // Subtle Cash-Green "glow" when valid (Android has no text-shadow blur — emulate
        // with a faint behind-text green halo via a blurred copy if exact parity is needed).

        Spacer(Modifier.weight(1f))

        // Keypad: 3 cols, "0" double-width on the last row
        val rows = listOf(
            listOf("1", "2", "3"),
            listOf("4", "5", "6"),
            listOf("7", "8", "9"),
        )
        Column(
            Modifier.fillMaxWidth().padding(horizontal = 24.dp),
            verticalArrangement = Arrangement.spacedBy(4.dp),
        ) {
            rows.forEach { row ->
                Row(horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    row.forEach { d ->
                        KeypadButton(d, Modifier.weight(1f)) { onAmountChange(amount + d) }
                    }
                }
            }
            Row(horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                KeypadButton(".", Modifier.weight(1f)) { if (!amount.contains('.')) onAmountChange("$amount.") }
                KeypadButton("0", Modifier.weight(2f)) { onAmountChange(amount + "0") } // double-width
                KeypadButton(null, Modifier.weight(1f), icon = Icons.AutoMirrored.Filled.Backspace) {
                    if (amount.isNotEmpty()) onAmountChange(amount.dropLast(1))
                }
            }
        }

        // Pay / Request
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 24.dp, vertical = 0.dp)
                .padding(top = 16.dp, bottom = 24.dp),
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(
                Modifier.weight(1f).height(60.dp).clip(RoundedCornerShape(30.dp))
                    .background(CashColors.Surface2).clickable { onRequest() },
                contentAlignment = Alignment.Center,
            ) { Text("Request", style = CashText.Button, color = CashColors.TextPrimary) }
            Box(
                Modifier.weight(1f).height(60.dp).clip(RoundedCornerShape(30.dp))
                    .background(if (valid) CashColors.Green else CashColors.Surface2)
                    .clickable(enabled = valid) {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .notification(.success)
                        onPay()
                    },
                contentAlignment = Alignment.Center,
            ) {
                Text("Pay", style = CashText.Button,
                    color = if (valid) Color.Black else CashColors.TextTertiary) // black on green
            }
        }
    }
}

@Composable
private fun KeypadButton(
    label: String?,
    modifier: Modifier = Modifier,
    icon: androidx.compose.ui.graphics.vector.ImageVector? = null,
    onClick: () -> Unit,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.96f else 1f, label = "keyScale")
    val haptics = LocalHapticFeedback.current
    Box(
        modifier = modifier
            .height(64.dp)
            .scale(scale)
            .clip(RoundedCornerShape(12.dp))
            .background(if (pressed) CashColors.Surface2 else Color.Transparent)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ≈ .impact(.light)
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        when {
            label != null -> Text(label, style = CashText.KeypadDigit, color = CashColors.TextPrimary,
                textAlign = TextAlign.Center)
            icon != null -> Icon(icon, "Backspace", tint = CashColors.TextPrimary,
                modifier = Modifier.size(28.dp))
        }
    }
}
```

### Cash Card Render

1.586:1 matte-black card, hairline border, `$Cashtag` etched in the user's accent. **No shadow** — the card sits flat; the border is the only depth cue.

```kotlin
import androidx.compose.foundation.border

@Composable
fun CashCardView(
    cashtag: String,                // "$johndoe"
    accent: Color = CashColors.Green,
    modifier: Modifier = Modifier,
) {
    Box(
        modifier = modifier
            .fillMaxWidth()
            .aspectRatio(1.586f)
            .clip(RoundedCornerShape(12.dp))
            .background(CashColors.Canvas)        // matte black, NO shadow
            .border(1.dp, CashColors.Divider, RoundedCornerShape(12.dp)),
    ) {
        Row(
            Modifier.align(Alignment.BottomStart).fillMaxWidth().padding(24.dp),
            verticalAlignment = Alignment.Bottom,
        ) {
            Text(cashtag, style = CashText.CashtagLarge.copy(fontWeight = FontWeight.Bold, fontSize = 24.sp),
                color = accent)
            Spacer(Modifier.weight(1f))
            Text("$", style = CashText.ScreenTitle.copy(fontSize = 32.sp), color = accent)
        }
    }
}
```

### Activity Row

```kotlin
enum class Direction { Sent, Received }

@Composable
fun ActivityRow(
    name: String,
    direction: Direction,
    subtitle: String,                // "Sent to John" / "From Sarah"
    amount: String,                  // "20.00"
    modifier: Modifier = Modifier,
    avatarColor: Color = remember(name) { CashColors.AvatarColors.random() },
) {
    val received = direction == Direction.Received
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        modifier
            .fillMaxWidth()
            .height(64.dp)
            .background(if (pressed) CashColors.Surface1 else CashColors.Canvas)
            .clickable(interaction, indication = null) {},
    ) {
        Row(
            Modifier.fillMaxSize().padding(horizontal = 24.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(
                Modifier.size(40.dp).clip(androidx.compose.foundation.shape.CircleShape).background(avatarColor),
                contentAlignment = Alignment.Center,
            ) { Text(name.take(1), style = CashText.AvatarInit, color = Color.White) }
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(name, style = CashText.CardLabel, color = CashColors.TextPrimary, maxLines = 1)
                Text(subtitle, style = CashText.Meta, color = CashColors.TextSecondary, maxLines = 1)
            }
            Text(
                (if (received) "+$" else "-$") + amount,
                style = CashText.AmountRow,
                color = if (received) CashColors.Green else CashColors.TextPrimary,
            )
        }
        Box(
            Modifier.align(Alignment.BottomCenter).fillMaxWidth().height(0.5.dp)
                .background(CashColors.Divider)
        )
    }
}
```

### Balance Tile (Money tab top)

```kotlin
@Composable
fun BalanceTile(dollars: Int, cents: Int, modifier: Modifier = Modifier) {
    Column(
        modifier.fillMaxWidth().background(CashColors.Canvas)
            .padding(horizontal = 24.dp, vertical = 24.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Text("CASH BALANCE", style = CashText.AllCaps, color = CashColors.TextSecondary)
        Row(verticalAlignment = Alignment.Bottom) {
            Text("$%,d".format(dollars), style = CashText.Balance, color = CashColors.TextPrimary)
            Text(".%02d".format(cents),
                style = CashText.Balance.copy(fontSize = 24.sp), color = CashColors.TextPrimary)
        }
        Row(horizontalArrangement = Arrangement.spacedBy(8.dp), modifier = Modifier.padding(top = 12.dp)) {
            PillButton("Add Cash") {}
            PillButton("Cash Out") {}
        }
    }
}

@Composable
private fun PillButton(label: String, onClick: () -> Unit) {
    Box(
        Modifier.clip(androidx.compose.foundation.shape.CircleShape).background(CashColors.Surface2)
            .clickable { onClick() }.padding(horizontal = 18.dp, vertical = 10.dp),
    ) { Text(label, style = CashText.ButtonSmall, color = CashColors.TextPrimary) }
}
```

### Screen Title (Money / Card / Activity headers)

Cash App has no top nav bar — the title is a 28sp Black-weight word in the content area with a profile avatar trailing.

```kotlin
import androidx.compose.material.icons.filled.Person

@Composable
fun CashScreenTitle(title: String, modifier: Modifier = Modifier) {
    Row(
        modifier.fillMaxWidth().padding(horizontal = 24.dp, top = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Text(title, style = CashText.ScreenTitle, color = CashColors.TextPrimary)
        Spacer(Modifier.weight(1f))
        Box(
            Modifier.size(36.dp).clip(androidx.compose.foundation.shape.CircleShape).background(CashColors.Surface2),
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Person, "Profile", tint = CashColors.TextPrimary, modifier = Modifier.size(20.dp)) }
    }
}
```

## 4. The $-Amount Entry System (the distinctive system)

Cash App has no color extraction; its defining dynamic system is the **keypad amount engine** — type a number, watch it auto-shrink and glow green when valid, then commit with a success flash. The amount-shrink + numeric transition is implemented above in `AmountKeypad`. Here is the **send-confirmation flash** that follows a valid Pay tap: the screen pulses Cash Green for ~300ms before the confirmation sheet slides up.

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.tween
import androidx.compose.ui.draw.alpha

@Composable
fun PaySuccessFlash(triggerCount: Int, onComplete: () -> Unit) {
    val flash = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(triggerCount) {
        if (triggerCount > 0) {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .notification(.success)
            flash.animateTo(0.85f, tween(120))
            flash.animateTo(0f, tween(180))
            onComplete() // then present the confirmation ModalBottomSheet ($-amount in AmountReceive 64sp)
        }
    }
    if (flash.value > 0f) {
        Box(Modifier.fillMaxSize().alpha(flash.value).background(CashColors.Green))
    }
}
```

The confirmation sheet itself is a Material 3 `ModalBottomSheet` (24dp top radius, `Surface1` container, `Hairline` grabber) showing the amount in `CashText.AmountReceive`. Keep the digit-shrink rule (`heroSize` ladder) identical anywhere an editable amount appears so the typographic behavior is consistent across the keypad and any inline amount editors.

## 5. Navigation

5 tabs — Money / Card / Pay / Activity / Bitcoin — **icons only, no labels, ever**. The Pay tab is the center anchor and renders larger (32dp vs 28dp). Active glyph fills Cash Green; inactive glyphs stay **full white** (Cash App does not dim inactive tabs). Use Material 3 `NavigationBar` with labels suppressed and the Material indicator pill removed.

```kotlin
import androidx.compose.material.icons.filled.AttachMoney
import androidx.compose.material.icons.filled.CreditCard
import androidx.compose.material.icons.filled.CurrencyBitcoin
import androidx.compose.material.icons.filled.History
import androidx.compose.material.icons.filled.Paid
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun CashBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = CashColors.Canvas, tonalElevation = 0.dp) {
        // Money, Card, Pay (center, larger), Activity, Bitcoin
        val items = listOf(
            Icons.Filled.Paid to 28.dp,           // Money
            Icons.Filled.CreditCard to 28.dp,     // Card
            Icons.Filled.AttachMoney to 32.dp,    // Pay — larger anchor
            Icons.Filled.History to 28.dp,        // Activity
            Icons.Filled.CurrencyBitcoin to 28.dp // Bitcoin
        )
        val labels = listOf("Money", "Card", "Pay", "Activity", "Bitcoin")
        items.forEachIndexed { i, (icon, size) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    Icon(
                        icon,
                        contentDescription = "${labels[i]} tab", // TalkBack only — no visible label
                        modifier = Modifier.size(size),
                    )
                },
                label = null,                // NO LABELS — hard rule
                alwaysShowLabel = false,
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = CashColors.Green,
                    unselectedIconColor = CashColors.TextPrimary, // full white, NOT dimmed
                    indicatorColor      = Color.Transparent,      // no Material pill — Cash App has none
                ),
            )
        }
    }
}
```

Home screens have no `TopAppBar` — use `CashScreenTitle` in the content. Modal sheets slide up over an `rgba(0,0,0,0.85)` scrim as Material 3 `ModalBottomSheet` (24dp top radius). Android has no live blur, but Cash App uses a solid dim anyway, so there is nothing to approximate — use a plain `scrimColor`.

## 6. Motion

Cash App is flat and disciplined — no shadows, no bouncy mascots. Motion is the digit engine, the green flash, and the card flip.

| Moment | Compose recipe |
|--------|----------------|
| Keypad digit tap | digit drops via `AnimatedContent` (`fadeIn + scaleIn 0.9`); existing digits shift; `spring(dampingRatio = 0.7f, stiffness ≈ 500f)` (≈ response 0.3); `HapticFeedbackType.TextHandleMove` (.impact light) |
| Pay send | scale 1.0 → 0.97 → 1.0 250ms `spring`, then full-screen Green flash ~300ms; `HapticFeedbackType.LongPress` (.success) |
| Cash Card flip | `animateFloatAsState` `rotationY` 0 → 180 over 600ms `spring(dampingRatio = 0.65f)`; `graphicsLayer { rotationY = …; cameraDistance = 12 * density }` |
| Activity row press | background → `Surface1` over 150ms; no scale |
| Tab switch | glyph color tween to Green over 200ms; `HapticFeedbackType.TextHandleMove` (.selection) |
| Boost activation | `Animatable` scale 1.0 → 1.05 → 1.0 `spring` + particle burst; `HapticFeedbackType.LongPress` (.success) |

```kotlin
// Cash Card flip (tap to reveal card back)
@Composable
fun FlippableCashCard(cashtag: String, accent: Color) {
    var flipped by remember { mutableStateOf(false) }
    val rotation by animateFloatAsState(
        targetValue = if (flipped) 180f else 0f,
        animationSpec = spring(dampingRatio = 0.65f, stiffness = 300f),
        label = "cardFlip",
    )
    Box(
        Modifier
            .fillMaxWidth()
            .aspectRatio(1.586f)
            .graphicsLayer {
                rotationY = rotation
                cameraDistance = 12f * density
            }
            .clickable { flipped = !flipped },
    ) {
        if (rotation <= 90f) {
            CashCardView(cashtag, accent, Modifier.fillMaxSize())
        } else {
            Box(
                Modifier.fillMaxSize().graphicsLayer { rotationY = 180f }
                    .clip(RoundedCornerShape(12.dp)).background(CashColors.Canvas)
                    .border(1.dp, CashColors.Divider, RoundedCornerShape(12.dp)),
            ) { /* card back: card number, CVV, etc. */ }
        }
    }
}
```

Honor **Reduce Motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, disable the amount-glow and digit-shrink animation (snap to a fixed size sized for the longest expected digit count) and skip the Pay flash — keep the success haptic. For richer haptics use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(8, ...)` to approximate iOS's `.light` keypad tap.

## 7. Icons

Cash App's icons are mostly bespoke; the closest first-party set is `androidx.compose.material:material-icons-extended`. The **Boost glyphs are commissioned hand-drawn artwork** — never substitute Material icons; ship them as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Money tab | `dollarsign.circle` / `.fill` | `Icons.Filled.Paid` |
| Card tab | `creditcard` / `.fill` | `Icons.Filled.CreditCard` |
| Pay tab (center, larger) | `dollarsign` | `Icons.Filled.AttachMoney` (32dp) |
| Activity tab | `clock` / `.fill` | `Icons.Filled.History` |
| Bitcoin tab | `bitcoinsign.circle` / `.fill` | `Icons.Filled.CurrencyBitcoin` |
| Backspace (keypad) | `delete.left` | `Icons.AutoMirrored.Filled.Backspace` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Avatar fallback | `person.fill` | `Icons.Filled.Person` |
| Boost (Cash Card) | commissioned art | custom vector drawables — NOT Material icons |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the pure-black canvas wants `WindowCompat` light-content (white) system-bar icons. Use `Scaffold` insets so the keypad sits ~8dp above the tab bar and the tab bar clears the gesture-nav indicator. The screen title gets a 16dp inset below the status bar.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on titles, body, and Activity rows (clamp Activity amounts at 22sp). The **Pay-screen amount must NOT scale** — its size is driven by digit count, layout is critical: wrap the keypad amount in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`. Tab glyphs are fixed dp.
- **TalkBack**: tab labels are invisible — every tab `Icon` must carry a `contentDescription` ("Money tab", "Pay tab", …) since there is no visible text. Announce the keypad as "Amount entry", read it back as "Sending $12.50 to John Doe", and set the Pay button's content description to include the amount ("Pay $12.50"). Merge Activity-row text with `Modifier.semantics(mergeDescendants = true)`.
- **Touch targets**: Material guidance is 48.dp minimum. The 64dp keypad keys and 60dp Pay/Request buttons clear it; the 28–32dp tab glyphs sit in `NavigationBar`'s 48dp+ item slots; the 36dp profile avatar should be wrapped in a 48dp hit area.
- **Contrast**: white on `#000000` exceeds WCAG AA at all sizes; `#9E9E9E` on black meets AA at 14sp+ — avoid `#6E6E6E` for body text (use it only for disabled/placeholder). Green `#00D632` as the Pay fill carries black text by design — that pairing passes AA.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Cash App's identity is the fixed pure-black canvas and the single Cash Green regardless of wallpaper. (Material You suits Google/Material-first apps; Cash App is a strong-brand exception.) Light mode is accessibility-only — swap canvas/surface/text and darken Green to `#00B829` for AA on white; the mono numerals and tabular figures stay identical.
