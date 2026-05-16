# OkCupid (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports OkCupid's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (OkCupid's bright canvas, magenta-leads/indigo-supports accents, circular match-% badge, question-driven cards) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas`/`drawArc` for the badge ring, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for profile photography.

## 1. Color Tokens

```kotlin
// ui/theme/OkcColors.kt
import androidx.compose.ui.graphics.Color

object OkcColors {
    // Canvas & Surfaces (Light)
    val Canvas   = Color(0xFFFFFFFF)
    val Surface  = Color(0xFFF2F2F2)
    val Divider  = Color(0xFFE5E5E5)

    // Text
    val TextPrimary   = Color(0xFF14171A)
    val TextSecondary = Color(0xFF6B6B6B)
    val TextTertiary  = Color(0xFF9B9B9B)

    // Brand
    val Magenta        = Color(0xFFE2024F)
    val MagentaPressed = Color(0xFFC00244)
    val MagentaTint    = Color(0xFFFCE3EB)
    val Indigo         = Color(0xFF0500FF)
    val IndigoTint     = Color(0xFFE5E4FF)

    // Match tiers
    val MatchHigh = Color(0xFFE2024F)
    val MatchMid  = Color(0xFFF0578A)
    val MatchLow  = Color(0xFF9B9B9B)

    // Dark
    val DarkCanvas  = Color(0xFF15151A)
    val DarkSurface = Color(0xFF1F1F26)

    // Semantic
    val Success = Color(0xFF1CAA6A)
    val Warning = Color(0xFFF5A623)
    val Error   = Color(0xFFD0021B)
}
```

Wire it into Material 3 light + dark schemes so ripples, dividers, and default component colors inherit the brand.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val OkcLight = lightColorScheme(
    primary        = OkcColors.Magenta,
    onPrimary      = Color.White,
    secondary      = OkcColors.Indigo,
    background     = OkcColors.Canvas,
    onBackground   = OkcColors.TextPrimary,
    surface        = OkcColors.Canvas,
    onSurface      = OkcColors.TextPrimary,
    surfaceVariant = OkcColors.Surface,
    outline        = OkcColors.Divider,
    error          = OkcColors.Error,
)

private val OkcDark = darkColorScheme(
    primary        = OkcColors.Magenta,
    onPrimary      = Color.White,
    secondary      = OkcColors.Indigo,
    background     = OkcColors.DarkCanvas,
    onBackground   = Color.White,
    surface        = OkcColors.DarkSurface,
    onSurface      = Color.White,
    surfaceVariant = OkcColors.DarkSurface,
    outline        = Color(0xFF2C2C33),
    error          = OkcColors.Error,
)

@Composable
fun OkcTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) OkcDark else OkcLight,
    typography  = OkcTypography,
    content     = content,
)
```

## 2. Typography

Larsseit is OkCupid's licensed brand typeface. The closest free substitute is **Inter** — drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. If unavailable, fall back to the system grotesque (Roboto); prefer a rounded family to keep the friendly tone.

```kotlin
// ui/theme/OkcType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Larsseit = FontFamily(
    Font(R.font.larsseit_regular,  FontWeight.Normal),   // 400
    Font(R.font.larsseit_medium,   FontWeight.SemiBold), // 600
    Font(R.font.larsseit_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object OkcText {
    val TitleLarge  = TextStyle(Larsseit, fontWeight = FontWeight.Bold,     fontSize = 30.sp, lineHeight = 35.sp, letterSpacing = (-0.4).sp)
    val ProfileName = TextStyle(Larsseit, fontWeight = FontWeight.Bold,     fontSize = 26.sp, lineHeight = 30.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Larsseit, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val MatchPct    = TextStyle(Larsseit, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 20.sp)
    val Question    = TextStyle(Larsseit, fontWeight = FontWeight.SemiBold, fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.1).sp)
    val CardTitle   = TextStyle(Larsseit, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Larsseit, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val Answer      = TextStyle(Larsseit, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 21.sp)
    val Meta        = TextStyle(Larsseit, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 19.sp)
    val Caption     = TextStyle(Larsseit, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val LabelUpper  = TextStyle(Larsseit, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Button      = TextStyle(Larsseit, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = 0.2.sp)
    val Tab         = TextStyle(Larsseit, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val OkcTypography = Typography(
    headlineLarge = OkcText.TitleLarge,
    headlineSmall = OkcText.Section,
    titleMedium   = OkcText.CardTitle,
    bodyMedium    = OkcText.Body,
    labelSmall    = OkcText.Tab,
)
```

## 3. Signature Components

### Match-% Circular Badge

```kotlin
import androidx.compose.animation.core.animateIntAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp

@Composable
fun OkcMatchBadge(percent: Int, diameter: Dp = 48.dp) {
    val tier = when {
        percent >= 90 -> OkcColors.MatchHigh
        percent >= 70 -> OkcColors.MatchMid
        else          -> OkcColors.MatchLow
    }
    var target by remember { mutableIntStateOf(0) }
    LaunchedEffect(percent) { target = percent }
    val display by animateIntAsState(target, tween(600), label = "matchCountUp")

    Box(
        Modifier
            .size(diameter)
            .clip(CircleShape)
            .background(Color.White.copy(alpha = 0.96f)),
        contentAlignment = Alignment.Center,
    ) {
        Canvas(Modifier.fillMaxSize()) {
            drawCircle(
                color = tier,
                style = Stroke(width = 3.dp.toPx()),
                radius = size.minDimension / 2 - 1.5.dp.toPx(),
            )
        }
        Text("$display%", style = OkcText.MatchPct, color = tier)
    }
}
```

### Like Button (Magenta Heart)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.outlined.FavoriteBorder
import androidx.compose.material3.Icon
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun OkcLikeButton(
    liked: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    size: Dp = 64.dp,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val bounce = remember { Animatable(1f) }
    LaunchedEffect(liked) {
        if (liked) { bounce.animateTo(1.2f, tween(120)); bounce.animateTo(1f, tween(120)) }
    }
    val haptics = LocalHapticFeedback.current
    Box(
        modifier
            .size(size)
            .scale((if (pressed) 0.90f else 1f) * bounce.value)
            .shadow(14.dp, CircleShape, spotColor = OkcColors.TextPrimary.copy(alpha = 0.16f))
            .clip(CircleShape)
            .background(if (liked) OkcColors.MagentaTint else Color.White)
            .border(1.dp, OkcColors.Divider, CircleShape)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            if (liked) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
            contentDescription = "Like",
            tint = OkcColors.Magenta,
            modifier = Modifier.size(size * 0.44f),
        )
    }
}
```

### Pass Button (Indigo X)

```kotlin
import androidx.compose.material.icons.filled.Close

@Composable
fun OkcPassButton(onClick: () -> Unit, modifier: Modifier = Modifier, size: Dp = 56.dp) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val haptics = LocalHapticFeedback.current
    Box(
        modifier
            .size(size)
            .scale(if (pressed) 0.90f else 1f)
            .shadow(14.dp, CircleShape, spotColor = OkcColors.TextPrimary.copy(alpha = 0.16f))
            .clip(CircleShape)
            .background(Color.White)
            .border(1.dp, OkcColors.Divider, CircleShape)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(Icons.Filled.Close, "Pass", tint = OkcColors.Indigo, modifier = Modifier.size(size * 0.42f))
    }
}
```

### Primary Pill

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.graphics.graphicsLayer

@Composable
fun OkcPrimaryPill(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "pillScale")
    Box(
        modifier
            .fillMaxWidth()
            .scale(scale)
            .graphicsLayer { alpha = if (enabled) 1f else 0.35f }
            .clip(CircleShape)
            .background(if (pressed) OkcColors.MagentaPressed else OkcColors.Magenta)
            .clickable(interaction, indication = null, enabled = enabled, onClick = onClick)
            .padding(vertical = 15.dp, horizontal = 32.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = OkcText.Button, color = Color.White)
    }
}
```

### Match-Question Card

```kotlin
@Composable
fun OkcQuestionCard(
    prompt: String,
    options: List<String>,
    onAnswer: () -> Unit,
    modifier: Modifier = Modifier,
) {
    var selected by remember { mutableStateOf<Int?>(null) }
    var importance by remember { mutableIntStateOf(1) }
    val imp = listOf("A little", "Somewhat", "Very")

    Column(
        modifier
            .fillMaxWidth()
            .shadow(8.dp, RoundedCornerShape(20.dp), spotColor = OkcColors.TextPrimary.copy(alpha = 0.06f))
            .clip(RoundedCornerShape(20.dp))
            .background(Color.White)
            .padding(20.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        Text(prompt, style = OkcText.Question, color = OkcColors.TextPrimary)
        options.forEachIndexed { i, o ->
            val sel = selected == i
            Box(
                Modifier
                    .fillMaxWidth()
                    .clip(CircleShape)
                    .background(if (sel) OkcColors.Magenta else Color.White)
                    .then(if (sel) Modifier else Modifier.border(1.5.dp, OkcColors.Divider, CircleShape))
                    .clickable { selected = i }
                    .padding(vertical = 16.dp),
                contentAlignment = Alignment.Center,
            ) {
                Text(o, style = OkcText.Answer,
                    color = if (sel) Color.White else OkcColors.TextPrimary)
            }
        }
        Row(
            Modifier.clip(CircleShape).border(1.5.dp, OkcColors.Divider, CircleShape),
        ) {
            imp.forEachIndexed { i, label ->
                Box(
                    Modifier.weight(1f)
                        .background(if (importance == i) OkcColors.Magenta else OkcColors.Surface)
                        .clickable { importance = i }
                        .padding(vertical = 10.dp),
                    contentAlignment = Alignment.Center,
                ) {
                    Text(label, style = OkcText.Caption.copy(fontWeight = FontWeight.SemiBold),
                        color = if (importance == i) Color.White else OkcColors.TextSecondary)
                }
            }
        }
        OkcPrimaryPill("Answer", onAnswer, enabled = selected != null)
    }
}
```

## 4. DoubleTake Profile Card

```kotlin
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun OkcProfileCard(
    name: String, age: Int, percent: Int, heroUrl: String,
    modifier: Modifier = Modifier,
) {
    Box(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 8.dp)
            .shadow(24.dp, RoundedCornerShape(24.dp), spotColor = OkcColors.TextPrimary.copy(alpha = 0.12f))
            .clip(RoundedCornerShape(24.dp)),
    ) {
        AsyncImage(heroUrl, "$name profile photo",
            Modifier.fillMaxWidth().aspectRatio(4f / 5f), contentScale = ContentScale.Crop)
        Box(
            Modifier.matchParentSize().background(
                Brush.verticalGradient(0.6f to Color.Transparent,
                    1f to OkcColors.TextPrimary.copy(alpha = 0.45f))))
        Row(
            Modifier.align(Alignment.BottomStart).fillMaxWidth().padding(16.dp),
            verticalAlignment = Alignment.Bottom,
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            Text("$name, $age", style = OkcText.ProfileName, color = Color.White)
            OkcMatchBadge(percent)
        }
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. OkCupid's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 96%-opaque canvas surface. **Active tint is OkCupid Magenta.**

```kotlin
@Composable
fun OkcBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = OkcColors.Canvas.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Discover" to Icons.Filled.Dashboard,
            "Likes"    to Icons.Filled.Favorite,
            "Matches"  to Icons.Filled.AutoAwesome,
            "Messages" to Icons.Filled.ChatBubble,
            "Profile"  to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(26.dp)) },
                label = { Text(label, style = OkcText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = OkcColors.Magenta,
                    selectedTextColor   = OkcColors.Magenta,
                    unselectedIconColor = OkcColors.TextSecondary,
                    unselectedTextColor = OkcColors.TextSecondary,
                    indicatorColor      = OkcColors.MagentaTint,
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Like tap | `scale` 1 → 0.90 + `Animatable` bounce 1 → 1.2 → 1; `HapticFeedbackType.LongPress` (≈ iOS success) |
| Pass tap | `scale` 1 → 0.90; card `offsetX` animates `-screenWidth` with slight rotation, ease-out 300ms |
| Match-% count-up | `animateIntAsState(target, tween(600))` first time the badge composes |
| Card swipe | `draggable` + Reanimated-style spring snap-back; commit on threshold |
| It's a Match | full-screen `AnimatedVisibility(enter = scaleIn(initialScale = 0.85f) + fadeIn())`, light confetti |
| Pull-to-refresh | `PullToRefreshContainer` tinted `OkcColors.Magenta` |

```kotlin
// Pass card slide-out
val offsetX = remember { Animatable(0f) }
suspend fun passOut(screenWidthPx: Float) {
    offsetX.animateTo(-screenWidthPx, tween(300))
}
// apply: Modifier.offset { IntOffset(offsetX.value.roundToInt(), 0) }
//                .graphicsLayer { rotationZ = (offsetX.value / 40f).coerceIn(-8f, 0f) }
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(12, ...)` to approximate iOS's success notification.

## 7. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export OkCupid's glyphs (wordmark, illustrations) as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Like | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Pass | `xmark` | `Icons.Filled.Close` |
| Star / Boost | `star.fill` | `Icons.Filled.Star` |
| Match (tab) | `sparkles` | `Icons.Filled.AutoAwesome` |
| Message send | `arrow.up.circle.fill` | `Icons.Filled.ArrowCircleUp` |
| Filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Settings | `gearshape.fill` | `Icons.Filled.Settings` |
| Verified | `checkmark.seal.fill` | `Icons.Filled.Verified` |
| Discover (tab) | `rectangle.stack.fill` | `Icons.Filled.Dashboard` |
| Likes (tab) | `heart.fill` | `Icons.Filled.Favorite` |
| Messages (tab) | `bubble.left.fill` | `Icons.Filled.ChatBubble` |
| Profile (tab) | `person.fill` | `Icons.Filled.Person` |
| Question | `text.bubble.fill` | `Icons.Filled.QuestionAnswer` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; Coil + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the bright canvas wants dark system-bar icons via `WindowCompat`. Use `Scaffold` insets so the DoubleTake action bar clears the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on titles, question prompts, body. Pin the match-% badge number and tab labels (fixed ornaments) via `dp` or a fixed `Density` override.
- **TalkBack**: announce `OkcMatchBadge` with a `Modifier.semantics { contentDescription = "$percent percent match" }`; the Like button announces "Like {name}", Pass announces "Pass"; merge question prompt + options with `Modifier.semantics(mergeDescendants = true)`.
- **Touch targets**: Material guidance is 48.dp minimum. The 64.dp Like and 56.dp Pass are clear; answer pills are full-width ≥ 52.dp.
- **Contrast**: `#6B6B6B` on `#FFFFFF` passes WCAG AA at 14sp+. The match-low `#9B9B9B` is intentionally de-emphasized; reserve magenta for high-match emphasis. In dark mode magenta `#E2024F` stays — it reads well on `#15151A`.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — OkCupid's brand requires the fixed magenta-leads / indigo-supports identity regardless of wallpaper.
