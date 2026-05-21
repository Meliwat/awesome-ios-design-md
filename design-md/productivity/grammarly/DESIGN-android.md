# Grammarly (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Grammarly's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the suggestion card + score ring + assistant bar, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Grammarly's calm writing canvas, the single green accent, color-coded category underlines, the slide-up suggestion card) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a `Popup`/`Surface` instead of a SwiftUI overlay, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. No color extraction — Grammarly's palette is fixed, so Palette is not needed.

## 1. Color Tokens

```kotlin
// ui/theme/GrammarlyColors.kt
import androidx.compose.ui.graphics.Color

object GmColors {
    // Brand (single accent)
    val Green        = Color(0xFF15C39A)
    val GreenDeep    = Color(0xFF11A683)
    val GreenPressed = Color(0xFF0E8A6D)
    val GreenOnBtn   = Color(0xFF06281F) // text/icon on green fills

    // Suggestion categories (underlines/dots only — never CTAs)
    val Correctness = Color(0xFFE5484D)
    val Clarity     = Color(0xFF3B82F6)
    val Engagement  = Color(0xFF16A34A)
    val Delivery    = Color(0xFF8B5CF6)

    // Premium
    val PremiumGold = Color(0xFFE0A82E)

    // Canvas & Surfaces (Light)
    val Canvas   = Color(0xFFFFFFFF)
    val Surface1 = Color(0xFFF7F8F8)
    val Surface2 = Color(0xFFEEF0F0)
    val Divider  = Color(0xFFE4E6E6)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF121212) // warm charcoal — NOT pure black
    val DarkSurface1 = Color(0xFF1C1C1E)
    val DarkSurface2 = Color(0xFF262629)
    val DarkDivider  = Color(0xFF2C2C2E)

    // Text
    val TextPrimary     = Color(0xFF1A1A1A) // warm near-black — NOT pure black
    val TextSecondary   = Color(0xFF6B6B70)
    val TextTertiary    = Color(0xFF9A9A9F)
    val DarkTextPrimary = Color(0xFFE4E4E4)

    // Dark-mode brightened category hues
    val CorrectnessDark = Color(0xFFF05A5F)
    val ClarityDark     = Color(0xFF5A95F8)
    val EngagementDark  = Color(0xFF22B85A)
    val DeliveryDark    = Color(0xFFA07CF8)
}
```

Wire it into both schemes. Grammarly has a single green accent; the dark scheme uses the signature warm charcoal `#121212`, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val GmLight = lightColorScheme(
    primary        = GmColors.Green,
    onPrimary      = GmColors.GreenOnBtn,
    background      = GmColors.Canvas,
    onBackground    = GmColors.TextPrimary,
    surface         = GmColors.Surface1,
    onSurface       = GmColors.TextPrimary,
    surfaceVariant  = GmColors.Surface2,
    outline         = GmColors.Divider,
    error           = GmColors.Correctness,
)

private val GmDark = darkColorScheme(
    primary        = GmColors.Green,
    onPrimary      = GmColors.GreenOnBtn,
    background      = GmColors.DarkCanvas,
    onBackground    = GmColors.DarkTextPrimary,
    surface         = GmColors.DarkSurface1,
    onSurface       = GmColors.DarkTextPrimary,
    surfaceVariant  = GmColors.DarkSurface2,
    outline         = GmColors.DarkDivider,
    error           = GmColors.CorrectnessDark,
)

@Composable
fun GrammarlyTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) GmDark else GmLight,
    typography = GmTypography,
    content = content,
)
```

## 2. Typography

Grammarly's brand face is **Apercu** (licensed); SF Pro on iOS. Use **Inter** (SIL OFL — free) as the closest face: drop the TTFs in `res/font/`. Body 400, headings 700, screen titles 800; editorial rhythm. Editor body uses a roomy line-height for reading comfort.

```kotlin
// ui/theme/GmType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),
    Font(R.font.inter_medium,    FontWeight.Medium),
    Font(R.font.inter_semibold,  FontWeight.SemiBold),
    Font(R.font.inter_bold,      FontWeight.Bold),
    Font(R.font.inter_extrabold, FontWeight.ExtraBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object GmText {
    val ScreenTitle  = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val DocHeading   = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val DocSubhead   = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val CardTitle    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val Body         = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 26.sp) // ~1.65
    val SuggestTitle = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Swap         = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 22.sp)
    val Explain      = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 21.sp)
    val Action       = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 13.sp)
    val CategoryTag  = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.4.sp)
    val TonePill     = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val ScoreNum     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 17.sp)
    val BarLabel     = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.1.sp)
    val Button       = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
}

val GmTypography = Typography(
    headlineLarge  = GmText.ScreenTitle,
    headlineMedium = GmText.DocHeading,
    titleMedium    = GmText.CardTitle,
    bodyMedium     = GmText.Body,
    labelSmall     = GmText.BarLabel,
)
```

## 3. Signature Components

### Suggestion Category Model

```kotlin
enum class SuggestionCategory(val label: String) {
    Correctness("Correctness"), Clarity("Clarity"), Engagement("Engagement"), Delivery("Delivery");

    fun color(dark: Boolean) = when (this) {
        Correctness -> if (dark) GmColors.CorrectnessDark else GmColors.Correctness
        Clarity     -> if (dark) GmColors.ClarityDark     else GmColors.Clarity
        Engagement  -> if (dark) GmColors.EngagementDark  else GmColors.Engagement
        Delivery    -> if (dark) GmColors.DeliveryDark    else GmColors.Delivery
    }
}
```

### Suggestion Card (core atom)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Close
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.unit.dp

@Composable
fun SuggestionCard(
    category: SuggestionCategory,
    oldText: String,
    newText: String,
    explanation: String,
    onAccept: () -> Unit,
    onDismiss: () -> Unit,
    dark: Boolean = true,
) {
    val catColor = category.color(dark)
    Column(
        Modifier
            .padding(horizontal = 16.dp)
            .shadow(24.dp, RoundedCornerShape(16.dp), spotColor = Color.Black.copy(alpha = 0.5f))
            .clip(RoundedCornerShape(16.dp))
            .background(GmColors.DarkSurface1)
            .border(1.dp, GmColors.DarkDivider, RoundedCornerShape(16.dp))
            .padding(16.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            Box(Modifier.size(10.dp).clip(CircleShape).background(catColor))
            Text(category.label.uppercase(), style = GmText.CategoryTag, color = GmColors.TextSecondary)
        }
        Spacer(Modifier.height(10.dp))

        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(oldText, style = GmText.Swap.copy(textDecoration = TextDecoration.LineThrough), color = catColor)
            Text("→", style = GmText.Swap, color = GmColors.TextTertiary)
            Text(newText, style = GmText.Swap, color = GmColors.Green)
        }
        Spacer(Modifier.height(6.dp))

        Text(explanation, style = GmText.Explain, color = GmColors.TextSecondary, maxLines = 2)
        Spacer(Modifier.height(14.dp))

        Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {
            Box(
                Modifier
                    .weight(1f)
                    .clip(RoundedCornerShape(999.dp))
                    .background(GmColors.Green)
                    .clickable { onAccept() }
                    .padding(vertical = 10.dp),
                contentAlignment = Alignment.Center,
            ) { Text("Accept", style = GmText.Action, color = GmColors.GreenOnBtn) }

            Box(
                Modifier
                    .size(width = 44.dp, height = 40.dp)
                    .clip(RoundedCornerShape(999.dp))
                    .border(1.dp, GmColors.DarkDivider, RoundedCornerShape(999.dp))
                    .clickable { onDismiss() },
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Filled.Close, contentDescription = "Dismiss", tint = GmColors.TextTertiary, modifier = Modifier.size(16.dp)) }
        }
    }
}
```

### Document Score Ring

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.size
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke

@Composable
fun ScoreRing(score: Int, size: androidx.compose.ui.unit.Dp = 38.dp) {
    val tint = when {
        score >= 90 -> GmColors.Green
        score >= 60 -> GmColors.PremiumGold
        else        -> GmColors.Correctness
    }
    val sweep by animateFloatAsState(score / 100f * 360f, tween(600), label = "scoreRing")

    Box(Modifier.size(size), contentAlignment = Alignment.Center) {
        Canvas(Modifier.fillMaxSize()) {
            val sw = 3.dp.toPx()
            drawArc(GmColors.DarkDivider, 0f, 360f, false, style = Stroke(sw), size = this.size)
            drawArc(tint, -90f, sweep, false, style = Stroke(sw, cap = StrokeCap.Round), size = this.size)
        }
        Text("$score", style = if (size.value > 48) GmText.ScoreNum else GmText.Action, color = tint)
    }
}
```

### Colored Underline (drawBehind)

```kotlin
import androidx.compose.ui.draw.drawBehind
import androidx.compose.ui.geometry.Offset

fun Modifier.categoryUnderline(category: SuggestionCategory, dark: Boolean): Modifier {
    val c = category.color(dark)
    return when (category) {
        // Clarity = 18% fill + solid underline; others = solid underline
        // (wavy/dotted: draw a custom Path in drawBehind for production parity)
        SuggestionCategory.Clarity -> this
            .background(c.copy(alpha = 0.18f), RoundedCornerShape(2.dp))
            .drawBehind { drawLine(c, Offset(0f, size.height), Offset(size.width, size.height), 2.dp.toPx()) }
        else -> this.drawBehind {
            drawLine(c, Offset(0f, size.height), Offset(size.width, size.height), 2.dp.toPx())
        }
    }
}
```

### Tone Detector Row

```kotlin
@Composable
fun ToneRow(emoji: String, tones: List<String>) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 20.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Text(emoji, fontSize = 18.sp)
        Text("Tone sounds", style = GmText.TonePill, color = GmColors.TextSecondary)
        tones.forEach { t ->
            Box(
                Modifier
                    .clip(RoundedCornerShape(999.dp))
                    .background(GmColors.DarkSurface2)
                    .border(1.dp, GmColors.DarkDivider, RoundedCornerShape(999.dp))
                    .padding(horizontal = 12.dp, vertical = 5.dp),
            ) { Text(t, style = GmText.TonePill, color = GmColors.DarkTextPrimary) }
        }
    }
}
```

### Assistant Bar

```kotlin
@Composable
fun AssistantBar(total: Int, breakdown: String, onReview: () -> Unit) {
    Row(
        Modifier
            .fillMaxWidth()
            .background(GmColors.DarkCanvas.copy(alpha = 0.96f))
            .drawBehind { drawLine(GmColors.DarkDivider, Offset(0f, 0f), Offset(size.width, 0f), 0.5.dp.toPx()) }
            .padding(horizontal = 18.dp)
            .height(64.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Box(Modifier.size(36.dp).clip(CircleShape).background(GmColors.Green), contentAlignment = Alignment.Center) {
            Icon(Icons.Filled.AutoAwesome, contentDescription = null, tint = Color.White, modifier = Modifier.size(20.dp))
        }
        Column(Modifier.weight(1f)) {
            Text(if (total == 0) "All clear" else "$total suggestions",
                style = GmText.BarLabel.copy(fontWeight = FontWeight.Bold), color = GmColors.DarkTextPrimary)
            if (total > 0) Text(breakdown, style = GmText.BarLabel, color = GmColors.TextTertiary)
        }
        if (total > 0) {
            Box(
                Modifier.clip(RoundedCornerShape(999.dp)).background(GmColors.Green)
                    .clickable { onReview() }.padding(horizontal = 16.dp, vertical = 9.dp),
            ) { Text("Review", style = GmText.Action, color = GmColors.GreenOnBtn) }
        }
    }
}
```

## 4. Navigation

Grammarly has minimal chrome: a top bar (back + doc title + score ring) and a 3-tab bottom strip (Documents / Assistant / Account). On Android use a `NavigationBar`. There is no tint pill — active is the green brand color on icon + label.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun GmBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = GmColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Documents" to Icons.Filled.Description,
            "Assistant" to Icons.Filled.AutoAwesome,   // stand-in for rotated-G orb
            "Account"   to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = GmText.BarLabel) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = GmColors.Green,   // green, no tint pill
                    selectedTextColor = GmColors.Green,
                    unselectedIconColor = GmColors.TextTertiary,
                    unselectedTextColor = GmColors.TextTertiary,
                    indicatorColor = Color.Transparent,   // Grammarly has no Material pill
                ),
            )
        }
    }
}
```

The suggestion card is presented as a bottom-anchored `Surface` (or `ModalBottomSheet` for the Review walkthrough) so it floats above the editor with the right shadow and tap-outside dismiss. Only one card is visible at a time; the assistant bar walks the user serially.

## 5. Motion

Grammarly motion is calm — 200–600ms ease-out, reassuring, never aggressive. Shadow signals "the assistant is speaking — act or dismiss."

| Moment | Compose recipe |
|--------|----------------|
| Suggestion card present | `AnimatedVisibility` `slideInVertically(tween(280)) { it } + fadeIn()`; the tapped span scrolls to center in parallel |
| Suggestion card dismiss | `slideOutVertically(tween(200)) { it } + fadeOut()` |
| Accept | crossfade old→new span text `tween(200)`; green wash `animateColorAsState` flash fading over 600ms; decrement count |
| Score ring fill | `animateFloatAsState(target, tween(600))` on open; `tween(400)` on accept; soft tick haptic at end |
| Assistant-bar count | `animatedContent` / odometer roll `tween(250)` |
| Tone pills | `fadeIn(tween(200)) + slideInVertically { 4.dp }` when recomputed (debounce 800ms) |
| Tab switch | `Crossfade(tween(200))`; center orb gentle 1.0→1.06→1.0 pulse on new suggestions |

```kotlin
// Score ring fill — canonical Grammarly motion
val sweep by animateFloatAsState(
    targetValue = score / 100f * 360f,
    animationSpec = tween(durationMillis = 600, easing = FastOutSlowInEasing),
    label = "scoreRing",
)
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft success on Accept, and `HapticFeedbackConstants.CLOCK_TICK` via `LocalView` for the light tick when the score ring finishes filling. Auto-analysis is silent — no motion, no haptic; only show a snackbar on sync error.

## 6. Icons

Grammarly's rotated-G orb is a brand mark — render it as a bundled `ImageVector` / `VectorDrawable` for fidelity. The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Documents (tab) | `doc.text` | `Icons.Filled.Description` |
| Assistant (tab / orb) | rotated-G logomark | bundled vector (stand-in `Icons.Filled.AutoAwesome`) |
| Account (tab) | `person.circle` | `Icons.Filled.AccountCircle` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Dismiss suggestion | `xmark` | `Icons.Filled.Close` |
| Premium lock | `lock.fill` | `Icons.Filled.Lock` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| New document | `plus` | `Icons.Filled.Add` |
| Plagiarism flag | `exclamationmark.triangle.fill` | `Icons.Filled.Warning` |
| Insights | `chart.bar.fill` | `Icons.Filled.BarChart` |
| Tone sentiment | emoji glyphs | emoji `Text` (😊🙂😐😟), not an icon |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `Popup` comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; warm-charcoal dark wants light-content system bars, white light mode wants dark-content. The suggestion card and assistant bar must sit above the IME — use `Modifier.imePadding()` and `navigationBarsPadding()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on document heading, body, suggestion title, explanation. Pin layout-sensitive text (category tags, tone pills, score number, bar/tab labels) by deriving from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Brand font**: use Inter (SIL OFL — free) as the closest face; do not redistribute Apercu (Grammarly's licensed brand grotesque).
- **Wavy / dotted underlines**: Compose has no built-in wavy/dotted text decoration — draw a custom `Path` in `Modifier.drawBehind` measured from the text layout for Correctness (wavy) and Engagement (dotted) parity.
- **TalkBack**: announce a marked span as "{category} suggestion: replace {old} with {new}. {explanation}"; expose `customActions` ("Accept suggestion", "Dismiss suggestion") on the span so the card need not be opened; the card's Accept/Dismiss are first-class buttons.
- **Score ring**: `Modifier.semantics { progressBarRangeInfo = ProgressBarRangeInfo(score/100f, 0f..1f) }` plus a content description "Document score {n} out of 100".
- **Touch targets**: Material guidance is 48.dp. The 44×40 dismiss button is close — pad to a 48.dp hit; tone pills and category dots are decorative (tap goes to the card). Accept pill is full-width and tall enough.
- **Contrast**: `#1A1A1A` on `#FFFFFF` and `#E4E4E4` on `#121212` pass WCAG AA for 16sp body; `#06281F` on `#15C39A` passes AA for button text. Each category also carries a distinct underline *style* + a text label so color-blind users can differentiate without relying on hue.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the card slide with a `Crossfade`, snap the score ring to value; keep the green accept-flash (conveys state) but shorten to 200ms.
- **Dark mode**: invert via the `Dark*` palette — `#121212`, NOT true black; `#1A1A1A` text becomes `#E4E4E4`; category hues brighten to the `*Dark` variants. Do **not** enable Material You `dynamicColorScheme()` — Grammarly's single-green identity must hold regardless of wallpaper.
