# Apple Wallet (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Wallet's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, the signature card-stack physics, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Wallet's true-black canvas, 80dp card-peek stack, 10dp universal radius, titanium Apple Card) while making everything idiomatic Android — a `Box` Z-stack instead of `matchedGeometryEffect`, `SharedTransitionLayout` for the expand, translucent `Surface` instead of `.regularMaterial`, `sp`/`dp` instead of `pt`. Wallet has no tab bar, so there is no `NavigationBar` here.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for issuer card artwork / merchant logos. No color extraction, so no `androidx.palette`.

## 1. Color Tokens

```kotlin
// ui/theme/WalletColors.kt
import androidx.compose.ui.graphics.Color

object WalletColors {
    // Canvas & Surfaces
    val Canvas    = Color(0xFF000000) // true black — NOT system #1C1C1E, so cards float
    val Surface1  = Color(0xFF1C1C1E) // expanded-card supplementary section
    val Surface2  = Color(0xFF2C2C2E) // pressed states, inset rows, scrub track
    val Glass     = Color(0x1FFFFFFF) // rgba(255,255,255,0.12) — "+" button, toolbar buttons
    val Hairline  = Color(0xFF262629) // separator lines in detail sheets

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFA0A0A5)
    val TextTertiary  = Color(0xFF636368)

    // Apple Card titanium (the signature object)
    val TitaniumHi  = Color(0xFFE8E8EB) // top-left of 135° gradient
    val TitaniumMid = Color(0xFFA8A8AD) // center
    val TitaniumLo  = Color(0xFF3D3D3F) // bottom-right
    val LogoWhite   = Color(0xFFFFFFFF) //  glyph
    val ChipGold    = Color(0xFFC7AC73) // embossed chip
    val DailyCash   = Color(0xFFFF3B30) // Daily Cash $ chip

    // Semantic (HIG dark-mode tints)
    val SystemBlue  = Color(0xFF0A84FF) // "See All Transactions" links
    val Success     = Color(0xFF30D158) // payment successful
    val Warning     = Color(0xFFFF9F0A) // low transit balance
    val Error       = Color(0xFFFF453A) // declined card

    // Common card brand hints (issuer-controlled, recognizable)
    val ChaseBlue   = Color(0xFF1A2D4F)
    val AmexSilver  = Color(0xFFA7B0B7)
    val VisaNavy    = Color(0xFF1A1F71)
    val MastercardR = Color(0xFFEB001B)
    val MastercardO = Color(0xFFFF5F00)
}
```

Wire it into a Material 3 `darkColorScheme`. Wallet **is** dark mode — it ignores Light Mode entirely because the cards depend on the true-black canvas — so do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val WalletScheme = darkColorScheme(
    primary        = WalletColors.SystemBlue,  // HIG link blue is the only "accent"
    onPrimary      = WalletColors.TextPrimary,
    background     = WalletColors.Canvas,       // true black, not surfaceDim
    onBackground   = WalletColors.TextPrimary,
    surface        = WalletColors.Surface1,
    onSurface      = WalletColors.TextPrimary,
    surfaceVariant = WalletColors.Surface2,
    outline        = WalletColors.Hairline,
    error          = WalletColors.Error,
)

@Composable
fun WalletTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = WalletScheme, typography = WalletTypography, content = content)
```

## 2. Typography

Wallet's frame is pure SF Pro (Display ≥20pt, Text ≤17pt) — Apple's documented switchover, applied rigidly. SF Pro is proprietary; drop the TTFs in `res/font/` (lowercase, snake_case). Roboto is the closest free Android substitute. The masked card number is the only SF Mono usage — fall back to Roboto Mono / system monospace.

```kotlin
// ui/theme/WalletType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val SFProDisplay = FontFamily(
    Font(R.font.sf_pro_display_regular,  FontWeight.Normal),   // 400
    Font(R.font.sf_pro_display_medium,   FontWeight.Medium),   // 500
    Font(R.font.sf_pro_display_semibold, FontWeight.SemiBold), // 600
    Font(R.font.sf_pro_display_bold,     FontWeight.Bold),     // 700
)
val SFProText = FontFamily(
    Font(R.font.sf_pro_text_regular,  FontWeight.Normal),
    Font(R.font.sf_pro_text_medium,   FontWeight.Medium),
    Font(R.font.sf_pro_text_semibold, FontWeight.SemiBold),
)
val SFMono = FontFamily(Font(R.font.sf_mono_medium, FontWeight.Medium)) // card last4 only

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, same weights/tracking)
object WalletText {
    val Title          = TextStyle(SFProDisplay, fontWeight = FontWeight.Bold,     fontSize = 34.sp, lineHeight = 37.sp, letterSpacing = 0.36.sp)
    val SheetTitle     = TextStyle(SFProDisplay, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = 0.35.sp)
    val SectionHeader  = TextStyle(SFProText,    fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = (-0.08).sp) // UPPERCASE
    val CardIssuer     = TextStyle(SFProDisplay, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 19.sp)
    val CardLast4      = TextStyle(SFMono,       fontWeight = FontWeight.Medium,   fontSize = 17.sp, lineHeight = 17.sp, letterSpacing = 0.5.sp)
    val CardHolder     = TextStyle(SFProText,    fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
    val BalanceHero    = TextStyle(SFProDisplay, fontWeight = FontWeight.Bold,     fontSize = 40.sp, lineHeight = 40.sp, letterSpacing = 0.4.sp)
    val Body           = TextStyle(SFProText,    fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.4).sp)
    val Footnote       = TextStyle(SFProText,    fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp, letterSpacing = (-0.08).sp)
    val Caption        = TextStyle(SFProText,    fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.07.sp)
    val TxnAmount      = TextStyle(SFProText,    fontWeight = FontWeight.Medium,   fontSize = 17.sp, lineHeight = 17.sp)
    val DailyCash      = TextStyle(SFProDisplay, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 22.sp)
    val Action         = TextStyle(SFProText,    fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 17.sp, letterSpacing = (-0.4).sp)
}

// Map onto Material 3 slots so stock components inherit the system look
val WalletTypography = Typography(
    headlineLarge  = WalletText.Title,
    headlineSmall  = WalletText.SheetTitle,
    titleMedium    = WalletText.CardIssuer,
    bodyMedium     = WalletText.Body,
    labelSmall     = WalletText.Caption,
)
```

Wallet puts numerals everywhere money appears. Pin tabular figures on every balance/amount/last4 with `TextStyle(fontFeatureSettings = "tnum")` so dollar columns align — apply it to `BalanceHero`, `TxnAmount`, `DailyCash`, `CardLast4`.

## 3. Signature Components

### Apple Card (the signature object)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

private val CardRadius = RoundedCornerShape(10.dp) // Wallet's universal radius — every card type

@Composable
fun AppleCardFace(
    cardholder: String,
    dailyCashBalance: String,
    modifier: Modifier = Modifier,
) {
    Box(
        modifier = modifier
            .fillMaxWidth()
            .height(220.dp)
            .shadow(24.dp, CardRadius, spotColor = Color.Black.copy(alpha = 0.5f)) // heavy — black canvas
            .clip(CardRadius)
            .background(
                Brush.linearGradient(            // 135° titanium fade
                    colors = listOf(WalletColors.TitaniumHi, WalletColors.TitaniumMid, WalletColors.TitaniumLo),
                    start = Offset(0f, 0f),
                    end = Offset(Float.POSITIVE_INFINITY, Float.POSITIVE_INFINITY),
                )
            ),
    ) {
        // 0.5dp inner top highlight — paper-thin glass edge
        Box(
            Modifier.fillMaxWidth().height(0.5.dp).align(Alignment.TopCenter)
                .background(Color.White.copy(alpha = 0.08f))
        )
        Column(Modifier.fillMaxSize().padding(16.dp)) {
            Row(Modifier.fillMaxWidth(), verticalAlignment = Alignment.Top) {
                Box(Modifier.size(26.dp, 20.dp).clip(RoundedCornerShape(4.dp)).background(WalletColors.ChipGold))
                Spacer(Modifier.weight(1f))
                Text("", color = WalletColors.LogoWhite, style = WalletText.CardIssuer) //  glyph
            }
            Spacer(Modifier.weight(1f))
            Row(Modifier.fillMaxWidth(), verticalAlignment = Alignment.Bottom) {
                Text(cardholder.uppercase(), style = WalletText.CardHolder, color = Color(0xFF1A1A1A))
                Spacer(Modifier.weight(1f))
                Box(
                    Modifier.size(28.dp).clip(androidx.compose.foundation.shape.CircleShape)
                        .background(WalletColors.DailyCash),
                    contentAlignment = Alignment.Center,
                ) { Text("$", color = Color.White, style = WalletText.CardIssuer) }
            }
        }
    }
}
```

### Standard Credit/Debit Card

```kotlin
@Composable
fun CreditCardFace(
    issuer: String,
    last4: String,
    cardholder: String,
    networkLogoRes: Int,            // R.drawable.visa / mastercard / amex
    background: Brush,              // issuer-controlled, e.g. Chase Sapphire blue gradient
    modifier: Modifier = Modifier,
) {
    Box(
        modifier = modifier
            .fillMaxWidth()
            .height(220.dp)
            .shadow(24.dp, CardRadius, spotColor = Color.Black.copy(alpha = 0.5f))
            .clip(CardRadius)
            .background(background),
    ) {
        Box(
            Modifier.fillMaxWidth().height(0.5.dp).align(Alignment.TopCenter)
                .background(Color.White.copy(alpha = 0.08f))
        )
        Column(Modifier.fillMaxSize().padding(16.dp)) {
            Row(Modifier.fillMaxWidth(), verticalAlignment = Alignment.Top) {
                Text(issuer, style = WalletText.CardIssuer, color = Color.White)
                Spacer(Modifier.weight(1f))
                androidx.compose.foundation.Image(
                    painter = androidx.compose.ui.res.painterResource(networkLogoRes),
                    contentDescription = null,
                    modifier = Modifier.height(22.dp),
                )
            }
            Spacer(Modifier.weight(1f))
            Row(Modifier.fillMaxWidth(), verticalAlignment = Alignment.Bottom) {
                Text(cardholder.uppercase(), style = WalletText.CardHolder, color = Color.White.copy(alpha = 0.9f))
                Spacer(Modifier.weight(1f))
                Text("•••• $last4", style = WalletText.CardLast4, color = Color.White)
            }
        }
    }
}
```

### Card Stack (the home screen — the hero interaction)

A vertical Z-stack: each card offsets `index * 80.dp` down so only an 80dp peek of each shows. Tap → that card animates to full size while the rest tuck down; supplementary content cross-fades in at +200ms. There is no recycler — a typical user has 4–8 cards, so a plain `Column`/`Box` in a `verticalScroll` is correct.

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.core.Spring
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.animation.core.spring
import androidx.compose.animation.fadeIn
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.verticalScroll
import androidx.compose.runtime.*
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

private const val PEEK = 80 // dp — Wallet's sacred stack interval

@Composable
fun CardStack(
    cards: List<@Composable (expanded: Boolean) -> Unit>,
    modifier: Modifier = Modifier,
) {
    var expanded by remember { mutableStateOf<Int?>(null) }
    val haptics = LocalHapticFeedback.current

    Box(
        modifier
            .fillMaxSize()
            .background(WalletColors.Canvas)
            .verticalScroll(rememberScrollState()),
    ) {
        cards.forEachIndexed { idx, card ->
            val isExpanded = expanded == idx
            val collapsed = expanded == null
            if (collapsed || isExpanded) {
                // Spring: response 0.5 / damping 0.8 → bouncy but contained
                val y by animateDpAsState(
                    targetValue = if (collapsed) (idx * PEEK).dp else 0.dp,
                    animationSpec = spring(dampingRatio = 0.8f, stiffness = Spring.StiffnessMediumLow),
                    label = "cardOffset$idx",
                )
                Column(
                    Modifier
                        .padding(horizontal = 16.dp) // cards never touch the screen edge
                        .offset(y = y)
                        .clickable(
                            interactionSource = remember { MutableInteractionSource() },
                            indication = null,
                        ) {
                            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .impact(.medium)
                            expanded = if (collapsed) idx else null
                        },
                ) {
                    card(isExpanded)
                    AnimatedVisibility(visible = isExpanded, enter = fadeIn(tween(250, delayMillis = 200))) {
                        ExpandedCardDetail() // transactions + balance + toolbar
                    }
                }
            }
        }
    }
}
```

### Wallet Home (title + add button + stack)

Wallet has **no tab bar** — only the large title and a 32dp circular "+". Render directly, no `Scaffold` bottom bar.

```kotlin
@Composable
fun WalletHome(cards: List<@Composable (Boolean) -> Unit>) {
    Column(
        Modifier.fillMaxSize().background(WalletColors.Canvas)
            .windowInsetsPadding(WindowInsets.systemBars),
    ) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text("Wallet", style = WalletText.Title, color = WalletColors.TextPrimary)
            Spacer(Modifier.weight(1f))
            AddCardButton(onClick = { /* open Add Card sheet */ })
        }
        CardStack(cards)
    }
}

@Composable
fun AddCardButton(onClick: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    Box(
        Modifier
            .size(48.dp)                       // 44dp+ tap target around a 32dp visual
            .clickable(
                interactionSource = remember { MutableInteractionSource() },
                indication = null,
            ) { haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove); onClick() },
        contentAlignment = Alignment.Center,
    ) {
        Box(
            Modifier.size(32.dp).clip(androidx.compose.foundation.shape.CircleShape)
                .background(WalletColors.Glass),
            contentAlignment = Alignment.Center,
        ) {
            Icon(Icons.Filled.Add, contentDescription = "Add card", tint = WalletColors.TextPrimary,
                modifier = Modifier.size(16.dp))
        }
    }
}
```

### Transaction Row

```kotlin
import androidx.compose.material.icons.filled.ShoppingCart

@Composable
fun TransactionRow(
    merchant: String,
    date: String,
    amount: String,
    dailyCash: String?,
    modifier: Modifier = Modifier,
) {
    Box(modifier.fillMaxWidth().background(WalletColors.Surface1)) {
        Row(
            Modifier.fillMaxWidth().height(60.dp).padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(
                Modifier.size(32.dp).clip(androidx.compose.foundation.shape.CircleShape)
                    .background(WalletColors.Surface2),
                contentAlignment = Alignment.Center,
            ) {
                Icon(Icons.Filled.ShoppingCart, contentDescription = null,
                    tint = WalletColors.TextPrimary, modifier = Modifier.size(14.dp))
            }
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(merchant, style = WalletText.Body, color = WalletColors.TextPrimary, maxLines = 1)
                Text(date, style = WalletText.Footnote, color = WalletColors.TextSecondary, maxLines = 1)
            }
            Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(amount, style = WalletText.TxnAmount, color = WalletColors.TextPrimary)
                if (dailyCash != null) {
                    Text("Daily Cash $dailyCash", style = WalletText.Caption, color = WalletColors.DailyCash)
                }
            }
        }
        Box(
            Modifier.align(Alignment.BottomStart).padding(start = 16.dp)
                .fillMaxWidth().height(0.5.dp).background(WalletColors.Hairline)
        )
    }
}
```

### Action Toolbar (bottom of an expanded card)

iOS uses `.regularMaterial`; Android has no first-class live blur, so use a 92%-opaque true-black `Surface`. Three 44dp circular glass buttons.

```kotlin
import androidx.compose.material.icons.filled.CreditCard
import androidx.compose.material.icons.filled.MoreHoriz
import androidx.compose.material.icons.automirrored.filled.ListAlt
import androidx.compose.material3.Surface
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun ActionToolbar() {
    Surface(color = WalletColors.Canvas.copy(alpha = 0.92f)) { // ≈ .regularMaterial over #000
        Box {
            Box(Modifier.fillMaxWidth().height(0.5.dp).background(WalletColors.Hairline))
            Row(
                Modifier.fillMaxWidth().height(56.dp).padding(horizontal = 16.dp),
                horizontalArrangement = Arrangement.spacedBy(24.dp, Alignment.CenterHorizontally),
                verticalAlignment = Alignment.CenterVertically,
            ) {
                ToolbarButton(Icons.Filled.CreditCard, "Card details")
                ToolbarButton(Icons.AutoMirrored.Filled.ListAlt, "Transactions")
                ToolbarButton(Icons.Filled.MoreHoriz, "More")
            }
        }
    }
}

@Composable
private fun ToolbarButton(icon: ImageVector, label: String) {
    val haptics = LocalHapticFeedback.current
    Box(
        Modifier
            .size(44.dp)
            .clip(androidx.compose.foundation.shape.CircleShape)
            .background(WalletColors.Glass)
            .clickable(
                interactionSource = remember { MutableInteractionSource() },
                indication = null,
            ) { haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) }, // ≈ .selection
        contentAlignment = Alignment.Center,
    ) { Icon(icon, contentDescription = label, tint = WalletColors.TextPrimary, modifier = Modifier.size(18.dp)) }
}
```

### Pay Now Button (Apple Card payment flow)

```kotlin
@Composable
fun PayNowButton(onPayNow: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by androidx.compose.foundation.interaction.collectIsPressedAsState(interaction).let { it }
    val haptics = LocalHapticFeedback.current
    Box(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .height(54.dp)
            .clip(RoundedCornerShape(14.dp))
            .background(if (pressed) Color(0xFFE5E5E5) else Color.White)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onPayNow()
            },
        contentAlignment = Alignment.Center,
    ) { Text("Pay Now", style = WalletText.Action, color = WalletColors.Canvas) }
}
```

## 4. Apple Pay Invocation (the most distinctive interaction)

Wallet has no color extraction; its signature dynamic moment is the Apple Pay handoff: the card slams to center, the screen turns true black, a biometric glyph appears, then a success check with `.notificationOccurred(.success)`. On Android, the OS double-press is replaced by your in-app trigger; `SharedTransitionLayout` (Compose 1.7+) carries the card from the stack into the overlay.

```kotlin
import androidx.compose.material.icons.filled.CheckCircle
import androidx.compose.material.icons.filled.Fingerprint
import kotlinx.coroutines.delay

@Composable
fun ApplePayOverlay(cardArtworkUrl: String, onAuthenticated: () -> Unit) {
    var authenticated by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current

    LaunchedEffect(authenticated) {
        if (authenticated) {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .notification(.success)
            delay(700)
            onAuthenticated()
        }
    }

    Box(
        Modifier.fillMaxSize().background(WalletColors.Canvas), // screen turns true black
        contentAlignment = Alignment.TopCenter,
    ) {
        Column(
            Modifier.fillMaxSize().padding(top = 64.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(32.dp),
        ) {
            coil.compose.AsyncImage(
                model = cardArtworkUrl,
                contentDescription = "Apple Card",
                modifier = Modifier
                    .fillMaxWidth(0.92f)
                    .height(220.dp)
                    .shadow(24.dp, CardRadius, spotColor = Color.Black.copy(alpha = 0.5f))
                    .clip(CardRadius),
            )
            if (!authenticated) {
                Column(
                    horizontalAlignment = Alignment.CenterHorizontally,
                    verticalArrangement = Arrangement.spacedBy(12.dp),
                ) {
                    Icon(Icons.Filled.Fingerprint, contentDescription = null,
                        tint = WalletColors.TextPrimary, modifier = Modifier.size(64.dp))
                    Text("Confirm with biometrics", style = WalletText.Body, color = WalletColors.TextPrimary)
                }
                LaunchedEffect(Unit) { delay(800); authenticated = true } // demo: BiometricPrompt in prod
            } else {
                Icon(Icons.Filled.CheckCircle, contentDescription = "Confirmed",
                    tint = WalletColors.Success, modifier = Modifier.size(64.dp))
            }
            Spacer(Modifier.weight(1f))
            Text("Hold near reader", style = WalletText.SheetTitle, color = WalletColors.TextPrimary,
                modifier = Modifier.padding(bottom = 48.dp))
        }
    }
}
```

In production, replace the demo `delay` with `androidx.biometric.BiometricPrompt`; keep the `#000000` background and `WalletColors.Success` check so it reads as the system Apple Pay sheet.

## 5. Navigation

Wallet has **no navigation chrome** — no `NavigationBar`, no rail, no `TopAppBar`. The entire app is the card stack and its expanded detail; the only persistent affordances are the "Wallet" title and the 32dp "+" button (see §3 `WalletHome`). Do not add a `Scaffold` `bottomBar`.

Detail sheets (Card Details, Settings) rise as Material 3 `ModalBottomSheet` with a 14dp top radius (matching iOS sheet corners) over the true-black canvas. Use a `Surface(color = WalletColors.Surface1)` for grouped lists, with `Hairline` separators inset 16dp from the leading edge. Where iOS uses `.regularMaterial`, use a translucent `Surface` (`WalletColors.Canvas.copy(alpha = 0.92f)`) — Android has no live blur.

```kotlin
@OptIn(androidx.compose.material3.ExperimentalMaterial3Api::class)
@Composable
fun CardDetailSheet(onDismiss: () -> Unit, content: @Composable () -> Unit) {
    androidx.compose.material3.ModalBottomSheet(
        onDismissRequest = onDismiss,
        containerColor = WalletColors.Surface1,
        shape = RoundedCornerShape(topStart = 14.dp, topEnd = 14.dp),
    ) { content() }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Card tap → expand | `animateDpAsState` peek-offset → 0 with `spring(dampingRatio = 0.8f, stiffness = StiffnessMediumLow)` (≈ response 0.5); `HapticFeedbackType.LongPress` (.impact medium) |
| Card collapse | same spring reversed; release haptic `HapticFeedbackType.TextHandleMove` (≈ .impact soft) |
| Supplementary content reveal | `AnimatedVisibility` + `fadeIn(tween(250, delayMillis = 200))` below the expanded card |
| Daily Cash chip pulse | `rememberInfiniteTransition` scale 1.0 → 1.04 → 1.0 over 1200ms, only on the Apple Card face |
| Transit balance update | `Animatable` scrub old → new over 800ms with `tween(easing = FastOutSlowInEasing)` |
| Boarding-pass flip | `animateFloatAsState` `rotationY` 0 → 180 over 600ms, `graphicsLayer { rotationY = … }` |
| Apple Pay success | `HapticFeedbackType.LongPress` then cross-fade to the green check |

```kotlin
// Daily Cash chip breathing pulse — Apple Card only
@Composable
fun DailyCashChip(amount: String) {
    val t = rememberInfiniteTransition(label = "dailyCash")
    val scale by t.animateFloat(
        initialValue = 1f, targetValue = 1.04f,
        animationSpec = infiniteRepeatable(tween(1200), RepeatMode.Reverse),
        label = "pulse",
    )
    Box(
        Modifier
            .size(28.dp)
            .graphicsLayer { scaleX = scale; scaleY = scale }
            .clip(androidx.compose.foundation.shape.CircleShape)
            .background(WalletColors.DailyCash),
        contentAlignment = Alignment.Center,
    ) { Text("$", color = Color.White, style = WalletText.CardIssuer) }
}
```

For the mini-bar → expanded card transition prefer `SharedTransitionLayout` + `Modifier.sharedElement()` on the card artwork (Compose 1.7+). Haptics: prefer `LocalHapticFeedback`; for richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, ...)` to approximate iOS's `.soft` impact. Honor **Reduce Motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, skip the spring bounce and use a 0.25s linear cross-fade for the card expand.

## 7. Icons

Wallet uses a handful of SF Symbols; the closest first-party set is `androidx.compose.material:material-icons-extended`. The Apple logo glyph and brand network marks (Visa/Mastercard/AmEx) have no Material equivalent — ship them as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Add card "+" | `plus` | `Icons.Filled.Add` |
| Apple logo |  / `applelogo` | custom vector drawable (no Material glyph) |
| Card details | `creditcard.fill` | `Icons.Filled.CreditCard` |
| Transactions list | `list.bullet.rectangle` | `Icons.AutoMirrored.Filled.ListAlt` |
| More menu | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Disclosure | `chevron.right` | `Icons.AutoMirrored.Filled.KeyboardArrowRight` |
| Biometric confirm | `faceid` | `Icons.Filled.Fingerprint` (or custom Face Unlock glyph) |
| Success check | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Transit NFC waves | `wave.3.right` | `Icons.Filled.Contactless` |
| Merchant default | `cart.fill` / `bag.fill` | `Icons.Filled.ShoppingCart` |
| Card network marks | (brand assets) | custom vector drawables (Visa/Mastercard/AmEx) |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `SharedTransitionLayout` comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the true-black canvas wants `WindowCompat` light-content (white) system-bar icons. Apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the title clears the status bar and the last card keeps 24dp clearance above the gesture-nav indicator.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on the Wallet title, detail body, transaction rows. The **card face is artwork, not text** — pin issuer name, last4, holder name, and balance hero so the card never reflows: wrap card composables in `CompositionLocalProvider(LocalDensity provides Density(LocalDensity.current.density, fontScale = 1f))`.
- **TalkBack**: each card is one accessibility element — `Modifier.semantics(mergeDescendants = true) { contentDescription = "Apple Card, ending in 4521, \$1,284.20 balance, collapsed" }`; update to "expanded" on tap. Mark the toolbar glyphs and "+" as separate buttons with their own labels.
- **Touch targets**: Material guidance is 48.dp minimum. The 80dp card peek and 54dp Pay Now clear it; the 32dp "+" and 18dp toolbar glyphs are wrapped in 44–48dp hit areas via padding (see §3).
- **Contrast**: white on the titanium gradient passes WCAG AA at the `#E8E8EB` highlight but is borderline over the `#3D3D3F` shadow end — paint a 30%-black scrim behind any text positioned over the dark end of the gradient. `#A0A0A5` on `#1C1C1E` passes AA at 14sp+; validate the 11sp caption / Daily Cash labels with a contrast checker and bump toward `#B0B0B5` if targeting strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Wallet's identity requires the fixed `#000000` canvas and HIG system-blue links regardless of wallpaper. (Material You is right for Google/Material-first apps; Wallet is a strong-brand exception.) Wallet also ignores Light Mode entirely — stay on the dark scheme even when the system is Light, because the floating-card effect depends on true black.
