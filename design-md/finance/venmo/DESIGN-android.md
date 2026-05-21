# Venmo (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Venmo's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (the social feed row, the Pay/Request split pill, the balance card, the amount-entry screen, the animated-checkmark confirmation), `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the **public emoji transaction feed**, the saturated friendly Venmo Blue, the Pay/Request split pill pinned above the tab bar, the Display-cut balance numerals, the stroke-drawing checkmark) while making everything idiomatic Android — a `NavigationBar` with an overlaid pill instead of a UITabBar, a `PathMeasure`-driven checkmark instead of a SwiftUI `Shape.trim`, `LocalHapticFeedback` instead of `.sensoryFeedback`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for feed avatars. No color extraction, so `androidx.palette` is not required.

## 1. Color Tokens

```kotlin
// ui/theme/VenmoColors.kt
import androidx.compose.ui.graphics.Color

object VenmoColors {
    // Canvas (Light)
    val Canvas        = Color(0xFFFFFFFF)
    val SurfaceMuted  = Color(0xFFF7F7F7)
    val Divider       = Color(0xFFF2F2F2)
    val DividerStrong = Color(0xFFE0E0E0)

    // Text
    val TextPrimary   = Color(0xFF2F3033) // intentional: warm charcoal, never pure black
    val TextSecondary = Color(0xFF6B6E76)
    val TextTertiary  = Color(0xFF9AA0A8)

    // Brand
    val Blue      = Color(0xFF008CFF) // saturated, friendly — NOT banking navy
    val BlueDeep  = Color(0xFF0078DE)
    val BlueLight = Color(0xFF3D95CE)
    val BlueTint  = Color(0xFFE6F4FF)

    // Semantic
    val ReceivedGreen = Color(0xFF4BB543)
    val ChargeRed     = Color(0xFFD32E2E)
    val PendingOrange = Color(0xFFF5A623)

    // Dark
    val DarkCanvas   = Color(0xFF1A1A1A)
    val DarkSurface1 = Color(0xFF242424)
    val DarkSurface2 = Color(0xFF2E2E2E)
    val DarkDivider  = Color(0xFF2A2A2A)
    val DarkText     = Color(0xFFFFFFFF)
    val DarkTextSec  = Color(0xFFA0A0A0)
}

// Blue gradient — balance card + payment-sent hero ONLY (never regular CTAs)
import androidx.compose.ui.graphics.Brush

val VenmoBrandGradient = Brush.verticalGradient(
    colors = listOf(VenmoColors.Blue, VenmoColors.BlueDeep),
)
```

Venmo is light-first (white canvas, social feed). Wire a Material 3 `lightColorScheme` so ripples, dividers, and stock component defaults inherit the brand; provide the dark scheme (Venmo Blue unchanged on dark).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val VenmoLight = lightColorScheme(
    primary        = VenmoColors.Blue,
    onPrimary      = Color.White,
    background     = VenmoColors.Canvas,
    onBackground   = VenmoColors.TextPrimary,
    surface        = VenmoColors.SurfaceMuted,
    onSurface      = VenmoColors.TextPrimary,
    surfaceVariant = VenmoColors.BlueTint,
    outline        = VenmoColors.Divider,
    error          = VenmoColors.ChargeRed,
)

private val VenmoDark = darkColorScheme(
    primary        = VenmoColors.Blue, // unchanged on dark
    onPrimary      = Color.White,
    background     = VenmoColors.DarkCanvas,
    onBackground   = VenmoColors.DarkText,
    surface        = VenmoColors.DarkSurface1,
    onSurface      = VenmoColors.DarkText,
    surfaceVariant = VenmoColors.DarkSurface2,
    outline        = VenmoColors.DarkDivider,
    error          = VenmoColors.ChargeRed,
)

@Composable
fun VenmoTheme(darkTheme: Boolean = isSystemInDarkTheme(), content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (darkTheme) VenmoDark else VenmoLight,
        typography = VenmoTypography,
        content = content,
    )
```

## 2. Typography

Venmo Sans and Venmo Display are proprietary. Drop the TTFs in `res/font/`. Fall back to the system font (Roboto) for Sans; the Display cut (big balance/confirm numerals) falls back to a bold weight — Source Sans Pro / Brown Std are the closest near-free substitutes if bundled.

```kotlin
// ui/theme/VenmoType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val VenmoSans = FontFamily(
    Font(R.font.venmo_sans_regular,  FontWeight.Normal),   // 400
    Font(R.font.venmo_sans_semibold, FontWeight.SemiBold), // 600
    Font(R.font.venmo_sans_bold,     FontWeight.Bold),     // 700
)
val VenmoDisplay = FontFamily(
    Font(R.font.venmo_display_bold, FontWeight.Bold),      // 700 — numbers + hero moments only
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object VenmoText {
    val BalanceDisplay = TextStyle(VenmoDisplay, fontWeight = FontWeight.Bold,     fontSize = 56.sp, lineHeight = 56.sp, letterSpacing = (-0.8).sp)
    val ConfirmDisplay = TextStyle(VenmoDisplay, fontWeight = FontWeight.Bold,     fontSize = 44.sp, lineHeight = 44.sp, letterSpacing = (-0.5).sp)
    val SignupDisplay  = TextStyle(VenmoDisplay, fontWeight = FontWeight.Bold,     fontSize = 36.sp, lineHeight = 40.sp, letterSpacing = (-0.4).sp)
    val AmountEntry    = TextStyle(VenmoDisplay, fontWeight = FontWeight.Bold,     fontSize = 72.sp, lineHeight = 72.sp, letterSpacing = (-1.0).sp)
    val ScreenTitle    = TextStyle(VenmoSans,    fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val FeedName       = TextStyle(VenmoSans,    fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 21.sp)
    val FeedBody       = TextStyle(VenmoSans,    fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val FeedMemo       = TextStyle(VenmoSans,    fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val Timestamp      = TextStyle(VenmoSans,    fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Comment        = TextStyle(VenmoSans,    fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val Amount         = TextStyle(VenmoSans,    fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val Button         = TextStyle(VenmoSans,    fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val ButtonSmall    = TextStyle(VenmoSans,    fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 15.sp)
    val Input          = TextStyle(VenmoSans,    fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 21.sp)
    val Tab            = TextStyle(VenmoSans,    fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Meta           = TextStyle(VenmoSans,    fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val VenmoTypography = Typography(
    headlineLarge = VenmoText.SignupDisplay,
    headlineSmall = VenmoText.ScreenTitle,
    titleMedium   = VenmoText.FeedName,
    bodyMedium    = VenmoText.FeedBody,
    labelLarge    = VenmoText.Button,
    labelSmall    = VenmoText.Tab,
)
```

## 3. Signature Components

### Feed Transaction Row (THE Signature Component)

The defining component: an Instagram-style row with mixed-weight inline text (actor bold, verb regular, recipient bold), an emoji-rich memo, and a like/comment row. **No amount is shown** (privacy model). Compose mixes weights inline via `buildAnnotatedString` + `SpanStyle`.

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.FavoriteBorder
import androidx.compose.material.icons.automirrored.outlined.Comment
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.drawBehind
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.withStyle
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun VenmoFeedRow(
    actorName: String,
    verb: String,            // "paid" or "charged"
    recipientName: String,
    timestamp: String,
    memo: String,
    avatarUrl: String?,
    likeCount: Int,
    commentCount: Int,
    modifier: Modifier = Modifier,
) {
    var liked by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current

    Column(
        modifier = modifier
            .fillMaxWidth()
            .background(VenmoColors.Canvas)
            .drawBehind {
                drawLine(VenmoColors.Divider, Offset(0f, size.height), Offset(size.width, size.height), 1.dp.toPx())
            }
            .padding(horizontal = 16.dp, vertical = 12.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Row(horizontalArrangement = Arrangement.spacedBy(12.dp), verticalAlignment = Alignment.Top) {
            AsyncImage(
                model = avatarUrl, contentDescription = null,
                modifier = Modifier.size(40.dp).clip(CircleShape).background(VenmoColors.SurfaceMuted),
                contentScale = ContentScale.Crop,
            )
            Column(verticalArrangement = Arrangement.spacedBy(4.dp)) {
                // Mixed-weight transaction line — actor bold, verb regular, recipient bold, timestamp gray
                Text(
                    buildAnnotatedString {
                        withStyle(SpanStyle(fontWeight = FontWeight.SemiBold)) { append(actorName) }
                        append(" $verb ")
                        withStyle(SpanStyle(fontWeight = FontWeight.SemiBold)) { append(recipientName) }
                        withStyle(SpanStyle(color = VenmoColors.TextSecondary)) { append("  ·  $timestamp") }
                    },
                    style = VenmoText.FeedBody,
                    color = VenmoColors.TextPrimary,
                )
                Text(memo, style = VenmoText.FeedMemo, color = VenmoColors.TextPrimary, maxLines = 3, overflow = TextOverflow.Ellipsis)
            }
        }
        // Like / comment row, aligned under the avatar column
        Row(
            Modifier.padding(start = 52.dp),
            horizontalArrangement = Arrangement.spacedBy(16.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Row(
                Modifier.clickable {
                    liked = !liked
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS light impact
                },
                horizontalArrangement = Arrangement.spacedBy(4.dp),
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Icon(
                    if (liked) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder,
                    contentDescription = if (liked) "Unlike" else "Like",
                    tint = if (liked) VenmoColors.Blue else VenmoColors.TextSecondary,
                    modifier = Modifier.size(18.dp),
                )
                Text("${likeCount + if (liked) 1 else 0}", style = VenmoText.Comment, color = VenmoColors.TextSecondary)
            }
            Row(horizontalArrangement = Arrangement.spacedBy(4.dp), verticalAlignment = Alignment.CenterVertically) {
                Icon(Icons.AutoMirrored.Outlined.Comment, "Comment", tint = VenmoColors.TextSecondary, modifier = Modifier.size(18.dp))
                Text("$commentCount", style = VenmoText.Comment, color = VenmoColors.TextSecondary)
            }
        }
    }
}
```

### Pay / Request Split Pill (The Anchor CTA)

Blue pill, 50.dp tall, two equal halves divided by a 1.dp white line, branded blue shadow. Pinned above the tab bar.

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.filled.ArrowDownward
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun VenmoPayRequestPill(
    onPay: () -> Unit,
    onRequest: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .height(50.dp)
            .shadow(12.dp, CircleShape, spotColor = VenmoColors.Blue, ambientColor = VenmoColors.Blue) // branded blue glow
            .clip(CircleShape)
            .background(VenmoColors.Blue),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        SplitHalf("Pay", Icons.Filled.ArrowUpward, Modifier.weight(1f), onPay)
        Box(Modifier.width(1.dp).height(30.dp).background(Color.White.copy(alpha = 0.6f)))
        SplitHalf("Request", Icons.Filled.ArrowDownward, Modifier.weight(1f), onRequest)
    }
}

@Composable
private fun SplitHalf(title: String, icon: ImageVector, modifier: Modifier, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val haptics = LocalHapticFeedback.current

    Row(
        modifier = modifier
            .fillMaxHeight()
            .clip(CircleShape)
            .background(if (pressed) VenmoColors.BlueDeep else Color.Transparent)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                onClick()
            },
        horizontalArrangement = Arrangement.Center,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(icon, contentDescription = null, tint = Color.White, modifier = Modifier.size(16.dp))
        Spacer(Modifier.width(6.dp))
        Text(title, style = VenmoText.Button, color = Color.White)
    }
}
```

### Balance Card (Me Tab Hero)

Gradient card, big Display balance with tabular digits, white action pills. Gradient is reserved for this + the confirmation hero.

```kotlin
@Composable
fun VenmoBalanceCard(
    balance: String,            // pre-formatted "$124.50"
    onTransfer: () -> Unit,
    onAddMoney: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier = modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .height(180.dp)
            .clip(RoundedCornerShape(20.dp))
            .background(VenmoBrandGradient)
            .padding(20.dp),
        verticalArrangement = Arrangement.spacedBy(20.dp),
    ) {
        Text("Venmo balance", style = VenmoText.ButtonSmall, color = Color.White.copy(alpha = 0.8f))
        Text(
            balance,
            style = VenmoText.BalanceDisplay.copy(
                fontFeatureSettings = "tnum" // tabular digits so columns align
            ),
            color = Color.White,
        )
        Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
            WhitePill("Transfer to bank", onTransfer)
            WhitePill("Add money", onAddMoney)
        }
    }
}

@Composable
private fun WhitePill(title: String, onClick: () -> Unit) {
    Box(
        Modifier.clip(CircleShape).background(Color.White).clickable(onClick = onClick)
            .padding(horizontal = 16.dp, vertical = 10.dp)
    ) { Text(title, style = VenmoText.ButtonSmall, color = VenmoColors.Blue) }
}
```

### Amount Input Screen (Pay Flow)

Giant centered Display amount, flat memo input, horizontal emoji picker (system emoji — the brand depends on it), fixed blue Pay pill.

```kotlin
import androidx.compose.foundation.horizontalScroll
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.ui.graphics.SolidColor
import androidx.compose.ui.text.input.TextFieldValue
import androidx.compose.ui.text.style.TextAlign

@Composable
fun VenmoAmountEntry(
    displayAmount: String,      // pre-formatted "$24.50"
    onPay: () -> Unit,
    modifier: Modifier = Modifier,
) {
    var memo by remember { mutableStateOf(TextFieldValue("")) }
    val quickEmojis = listOf("🍕", "🍺", "🚗", "💰", "🎉", "☕", "🏠", "🎁", "🛒", "✈️", "🎬", "💵")
    val haptics = LocalHapticFeedback.current

    Column(
        modifier = modifier.fillMaxSize(),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(20.dp),
    ) {
        Spacer(Modifier.weight(1f))
        Text(
            displayAmount,
            style = VenmoText.AmountEntry.copy(fontFeatureSettings = "tnum"), // dollar sign glued, tabular
            color = VenmoColors.TextPrimary,
        )
        BasicTextField(
            value = memo,
            onValueChange = { memo = it },
            textStyle = VenmoText.Input.copy(color = VenmoColors.TextPrimary, textAlign = TextAlign.Center),
            cursorBrush = SolidColor(VenmoColors.Blue),
            modifier = Modifier.fillMaxWidth().padding(horizontal = 40.dp),
            decorationBox = { inner ->
                if (memo.text.isEmpty()) {
                    Box(Modifier.fillMaxWidth(), contentAlignment = Alignment.Center) {
                        Text("What's it for?", style = VenmoText.Input, color = VenmoColors.TextTertiary)
                    }
                }
                inner()
            },
        )
        Row(
            Modifier.horizontalScroll(rememberScrollState()).padding(horizontal = 16.dp),
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            quickEmojis.forEach { e ->
                Box(
                    Modifier
                        .size(40.dp, 32.dp)
                        .clip(CircleShape)
                        .background(VenmoColors.SurfaceMuted)
                        .clickable {
                            memo = memo.copy(text = memo.text + e)
                            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ~iOS selection
                        },
                    contentAlignment = Alignment.Center,
                ) { Text(e, style = VenmoText.Input) } // system emoji font — never re-render with custom art
            }
        }
        Spacer(Modifier.weight(1f))
        Box(
            Modifier
                .fillMaxWidth()
                .padding(horizontal = 16.dp, vertical = 24.dp)
                .height(52.dp)
                .clip(CircleShape)
                .background(VenmoColors.Blue)
                .clickable(onClick = onPay),
            contentAlignment = Alignment.Center,
        ) { Text("Pay $displayAmount", style = VenmoText.Button, color = Color.White) }
    }
}
```

### Payment-Sent Confirmation (Animated Checkmark)

Full-screen gradient, a stroke-drawing checkmark inside a blue circle, confetti burst, success haptic. Compose draws the check progressively via `PathMeasure` + an `Animatable` 0→1.

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.spring
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.PathMeasure
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.Dp

@Composable
fun VenmoSentConfirmation(
    amount: String,             // "$24.50"
    recipientName: String,
    onDone: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val circleScale = remember { Animatable(0f) }
    val checkProgress = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current

    LaunchedEffect(Unit) {
        circleScale.animateTo(1f, spring(dampingRatio = 0.7f, stiffness = 400f)) // 0 → 1.0 spring
        haptics.performHapticFeedback(HapticFeedbackType.LongPress)               // ~iOS success
        checkProgress.animateTo(1f, tween(350))                                   // stroke draws in
    }

    Box(
        modifier = modifier.fillMaxSize().background(VenmoBrandGradient),
        contentAlignment = Alignment.Center,
    ) {
        Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(24.dp)) {
            Box(contentAlignment = Alignment.Center) {
                Box(Modifier.size(160.dp).clip(CircleShape).background(Color.White.copy(alpha = 0.15f)))
                Box(
                    Modifier.size(80.dp).graphicsLayer { scaleX = circleScale.value; scaleY = circleScale.value }
                        .clip(CircleShape).background(VenmoColors.Blue)
                )
                AnimatedCheck(progress = checkProgress.value, size = 44.dp)
            }
            Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(8.dp)) {
                Text("Sent", style = VenmoText.SignupDisplay, color = Color.White)
                Text(amount, style = VenmoText.ConfirmDisplay.copy(fontFeatureSettings = "tnum"), color = Color.White)
                Text("To $recipientName", style = VenmoText.Input, color = Color.White.copy(alpha = 0.8f))
            }
            WhitePill("Done", onDone)
        }
    }
}

@Composable
private fun AnimatedCheck(progress: Float, size: Dp) {
    Canvas(Modifier.size(size)) {
        val w = this.size.width
        val h = this.size.height
        val full = Path().apply {
            moveTo(0f, h * 0.5f)
            lineTo(w * 0.35f, h)
            lineTo(w, 0f)
        }
        val measure = PathMeasure().apply { setPath(full, false) }
        val drawn = Path()
        measure.getSegment(0f, measure.length * progress, drawn, true)
        drawPath(
            drawn,
            color = Color.White,
            style = Stroke(width = 6.dp.toPx(), cap = StrokeCap.Round),
        )
    }
}
```

> The confetti burst (8–12 white/blue particles, ~800ms) is an extra `Canvas` layer driven by an `Animatable` 0→1 mapping particle radius/alpha over time — keep it skippable for reduced-motion.

## 4. Tab Bar with Overlaid Pay/Request Pill (the distinctive interaction)

Venmo's defining structural move: a 5-slot bottom nav where the **center is the Pay/Request split pill overlapping the bar**, not a tab. Compose stacks the `NavigationBar` and the pill in a `Box`.

```kotlin
import androidx.compose.foundation.layout.Box
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun VenmoRootScaffold(
    selected: Int,
    onSelect: (Int) -> Unit,
    onPay: () -> Unit,
    onRequest: () -> Unit,
    content: @Composable (PaddingValues) -> Unit,
) {
    Scaffold(
        bottomBar = {
            Box {
                NavigationBar(
                    containerColor = VenmoColors.Canvas.copy(alpha = 0.96f), // Android has no live blur
                    tonalElevation = 0.dp,
                ) {
                    val items = listOf(
                        "Home"       to Icons.Filled.Home,
                        "Incomplete" to Icons.Filled.Schedule,
                        null         to null,                       // spacer slot for the pill
                        "Cards"      to Icons.Filled.CreditCard,
                        "Me"         to Icons.Filled.Person,
                    )
                    items.forEachIndexed { i, (label, icon) ->
                        if (label == null || icon == null) {
                            NavigationBarItem(selected = false, onClick = {}, enabled = false, icon = {})
                        } else {
                            NavigationBarItem(
                                selected = selected == i,
                                onClick = { onSelect(i) },
                                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                                label = { Text(label, style = VenmoText.Tab) },
                                colors = NavigationBarItemDefaults.colors(
                                    selectedIconColor   = VenmoColors.Blue,
                                    selectedTextColor   = VenmoColors.Blue,
                                    unselectedIconColor = VenmoColors.TextSecondary,
                                    unselectedTextColor = VenmoColors.TextSecondary,
                                    indicatorColor      = Color.Transparent,
                                ),
                            )
                        }
                    }
                }
                VenmoPayRequestPill(
                    onPay = onPay, onRequest = onRequest,
                    modifier = Modifier.align(Alignment.TopCenter).fillMaxWidth(0.62f).padding(top = 6.dp),
                )
            }
        },
        content = content,
    )
}
```

## 5. Navigation

Venmo's nav is the bottom tab bar above (Home feed | Incomplete | [Pay/Request pill] | Cards | Me), labels always shown, active `#008CFF`. Screen headers use a left-aligned title (`Activity`, `Me`) in `VenmoText.ScreenTitle` with a 0.5.dp `#F2F2F2` bottom divider on scroll. There is no live blur on Android — use the 96%-opaque canvas surface shown above. The QR scanner is a full-screen `Surface(color = Color.Black)` with blue corner brackets.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Pay/Request half press | `collectIsPressedAsState` → half background → `#0078DE`; `HapticFeedbackType.LongPress` |
| Amount typing | per-character `animateFloatAsState` 1.0 → 1.05 → 1.0 scale bump (~50ms) |
| Payment-sent checkmark | `Animatable` circle scale `spring(0.7)` 0→1, then `PathMeasure` segment via `Animatable` `tween(350)` 0→1; confetti `Canvas` 800ms; success haptic |
| Feed row like | heart `Animatable` keyframes 1.0 → 1.2 → 1.0 over 300ms, tint → blue, `HapticFeedbackType.TextHandleMove` |
| Balance reveal | animate a `Float` 0 → balance over 400ms `tween`, format per frame (tabular digits) |
| Pull to refresh | Material 3 `PullToRefreshBox` tinted Venmo Blue |

```kotlin
// Feed-row heart bounce (DESIGN.md §6) — 1.0 → 1.2 → 1.0
@Composable
fun rememberLikeBounce(liked: Boolean): Float {
    val scale = remember { Animatable(1f) }
    LaunchedEffect(liked) {
        if (liked) {
            scale.animateTo(1.2f, spring(dampingRatio = 0.6f, stiffness = 600f))
            scale.animateTo(1.0f, tween(120))
        }
    }
    return scale.value
}
```

Haptics: prefer `LocalHapticFeedback`. For the payment-sent success use a `Vibrator` `VibrationEffect.createOneShot(20, ...)`; emoji-chip taps map to `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`. Motion stays calm overall (spring dampings 0.6–0.85) — Venmo is not an aggressive-animation app.

## 7. Icons

Venmo uses mostly standard glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. The checkmark is a custom `Canvas` path (above), and the QR viewfinder brackets are custom-drawn — neither has a clean SF Symbol equivalent.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Pay arrow | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Request arrow | `arrow.down` | `Icons.Filled.ArrowDownward` |
| Like | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Icons.Filled.Favorite` |
| Comment | `bubble.left` | `Icons.AutoMirrored.Outlined.Comment` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Send (comment) | `arrow.up.circle.fill` | `Icons.AutoMirrored.Filled.Send` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| QR scan | `qrcode.viewfinder` | `Icons.Filled.QrCodeScanner` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Incomplete (tab) | `clock.fill` | `Icons.Filled.Schedule` |
| Cards (tab) | `creditcard.fill` | `Icons.Filled.CreditCard` |
| Me (tab) | `person.fill` | `Icons.Filled.Person` |
| Checkmark animation | custom Shape | custom `Canvas` + `PathMeasure` |
| Verified badge | `checkmark.seal.fill` | `Icons.Filled.Verified` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; the branded blue pill shadow needs API 28+ for `spotColor` — fall back to a soft blue radial glow drawn behind the pill on older devices). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`. The Pay/Request pill + tab bar extend to the home indicator; pad the pill ~24.dp above `WindowInsets.navigationBars` so it clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on feed body, memo, comments. Pin layout-sensitive Display text: the 72sp amount-entry numeral is layout-critical (fixed); the 56sp balance can scale but caps at 72sp then ellipsizes; tab labels are fixed at 11sp. Pin via deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: merge the feed row into one element with `Modifier.semantics(mergeDescendants = true) { contentDescription = "$actorName $verb $recipientName, $timestamp. Memo: $memo. $likeCount likes, $commentCount comments" }`; the like/comment buttons are separate focusable children with their own labels. The balance card reads "Venmo balance, $124.50". Announce "Payment sent successfully" after the checkmark animation completes.
- **Touch targets**: Material guidance is 48.dp minimum. Both halves of the 50.dp pill clear it; the 18.dp like/comment glyphs need a 32–48.dp hit area via padding; the 40.dp avatar carries a 44.dp+ hit area; emoji chips are 32–40.dp and should expand to 44.dp vertically.
- **Contrast**: `#6B6E76` secondary on `#FFFFFF` passes WCAG AA at 13sp+. `#9AA0A8` tertiary is decorative/placeholder only — never use it for content text. White on Venmo Blue `#008CFF` passes AA at 16sp Bold (the pill/buttons). On the blue gradient card, the 80%-opacity white "Venmo balance" label is a known low-contrast pair against the lighter `#008CFF` stop — keep that label ≥14sp.
- **Reduce motion**: when a reduced-motion preference is set, skip the confetti burst and the checkmark stroke-draw (show the filled check immediately), and replace the balance tick-up with the final value.
- **Emoji rendering**: rely on the **system emoji font** — never re-render memo emojis with custom art. Venmo's playful emoji-memo culture (🍕🍺 Pizza Friday) depends on native OS emoji; substituting an icon set breaks the brand signal.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme` — Venmo's identity is the single saturated `#008CFF` brand blue (pill, links, gradient). Letting it shift toward the user's wallpaper would dull the "friendly, not banking-navy" signature and desaturate the gradient hero.
