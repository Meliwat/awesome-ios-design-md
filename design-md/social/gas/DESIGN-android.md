# Gas (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Gas's playful purple visual language to **Android with Jetpack Compose (Material 3)**: a color token object, the brand & choice gradients, a `Typography` set, paste-ready `@Composable`s for the poll card and flame currency, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Gas's full-bleed indigo→purple gradient world, the big white poll card, the 2×2 colorful choice grid, the flame currency, chunky Nunito) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush.linearGradient` for the brand, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. Gas is **light-by-design** — there is no dark scheme; the bright gradient is the identity.

## 1. Color Tokens

```kotlin
// ui/theme/GasColors.kt
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object GasColors {
    // Brand
    val Indigo  = Color(0xFF6C5CE7)
    val Purple  = Color(0xFFA06CFF)
    val Violet  = Color(0xFF7B4DFF) // on-white tint
    val IndigoPressed = Color(0xFF5A4AD1)

    // Surfaces
    val CardWhite   = Color(0xFFFFFFFF)
    val TintSurface = Color(0xFFF4F2FF)
    val Divider     = Color(0xFFECE9FB)

    // Text on white cards
    val TextPrimary   = Color(0xFF1A1530)
    val TextSecondary = Color(0xFF6B6588)
    val TextTertiary  = Color(0xFFA29DB8)

    // Currency
    val Flame     = Color(0xFFFF7A45)
    val FlameGlow = Color(0x26FF7A45) // rgba(255,122,69,0.15)

    // Semantic
    val Success = Color(0xFF2BC48A)
    val Error   = Color(0xFFFF5470)

    // World background (full-bleed, every screen)
    val AppGradient = Brush.verticalGradient(listOf(Indigo, Purple))

    // Fixed festive choice palette (rotates by slot)
    data class ChoiceSlot(val brush: Brush, val text: Color)
    val choiceSlots = listOf(
        ChoiceSlot(Brush.linearGradient(listOf(Color(0xFF6C5CE7), Color(0xFF8B6CFF))), Color.White),     // 1 indigo
        ChoiceSlot(Brush.linearGradient(listOf(Color(0xFFFF6CC8), Color(0xFFFF9CDB))), Color.White),     // 2 pink
        ChoiceSlot(Brush.linearGradient(listOf(Color(0xFFFFC93C), Color(0xFFFFB13C))), Color(0xFF4A3300)),// 3 yellow
        ChoiceSlot(Brush.linearGradient(listOf(Color(0xFF2BC48A), Color(0xFF4FD9A6))), Color.White),     // 4 green
    )

    val PlumShadow = Color(0xFF281450) // rgba(40,20,90) — all elevation is purple-tinted
}
```

Gas is light-by-design — wire a single light scheme. Never enable Material You `dynamicColorScheme()` and never build a dark scheme: the bright indigo→purple gradient IS the brand identity.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.lightColorScheme

private val GasLight = lightColorScheme(
    primary        = GasColors.Indigo,
    onPrimary      = Color.White,
    background      = GasColors.Indigo,        // gradient drawn over this
    onBackground    = Color.White,
    surface         = GasColors.CardWhite,
    onSurface       = GasColors.TextPrimary,
    surfaceVariant  = GasColors.TintSurface,
    outline         = GasColors.Divider,
    error           = GasColors.Error,
)

@Composable
fun GasTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = GasLight, typography = GasTypography, content = content)
```

## 2. Typography

Gas's UI face is **Nunito** (SIL OFL — drop the TTFs in `res/font/`), set HEAVY (ExtraBold/Black).

```kotlin
// ui/theme/GasType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.sp

val Nunito = FontFamily(
    Font(R.font.nunito_semibold,  FontWeight.SemiBold),
    Font(R.font.nunito_bold,      FontWeight.Bold),
    Font(R.font.nunito_extrabold, FontWeight.ExtraBold),
    Font(R.font.nunito_black,     FontWeight.Black),
)

object GasText {
    val Display   = TextStyle(Nunito, fontWeight = FontWeight.Black,     fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val Screen    = TextStyle(Nunito, fontWeight = FontWeight.Black,     fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Question  = TextStyle(Nunito, fontWeight = FontWeight.Black,     fontSize = 23.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp, textAlign = TextAlign.Center)
    val CardTitle = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 18.sp, lineHeight = 23.sp)
    val Choice    = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 18.sp, textAlign = TextAlign.Center)
    val Body      = TextStyle(Nunito, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 22.sp)
    val BigNum    = TextStyle(Nunito, fontWeight = FontWeight.Black,     fontSize = 28.sp, lineHeight = 28.sp, letterSpacing = (-0.5).sp)
    val Meta      = TextStyle(Nunito, fontWeight = FontWeight.Bold,      fontSize = 13.sp, lineHeight = 17.sp)
    val Tag       = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.5.sp)
    val Button    = TextStyle(Nunito, fontWeight = FontWeight.Black,     fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Pill      = TextStyle(Nunito, fontWeight = FontWeight.Black,     fontSize = 15.sp, lineHeight = 15.sp)
    val Tab       = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

val GasTypography = Typography(
    headlineLarge = GasText.Display,
    headlineMedium = GasText.Screen,
    titleLarge    = GasText.Question,
    titleMedium   = GasText.CardTitle,
    bodyMedium    = GasText.Body,
    labelSmall    = GasText.Tab,
)
```

## 3. Signature Components

### App Background (full-bleed gradient world)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier

@Composable
fun GasBackground(content: @Composable () -> Unit) {
    androidx.compose.foundation.layout.Box(
        Modifier.fillMaxSize().background(GasColors.AppGradient)
    ) { content() }
}
```

### Poll Card

```kotlin
import androidx.compose.animation.core.Spring
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
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
import androidx.compose.ui.unit.dp

data class PollChoice(val id: String, val name: String)

@Composable
fun PollCard(
    emoji: String,
    question: String,
    choices: List<PollChoice>,   // exactly 4
    onPick: (PollChoice) -> Unit,
    onShuffle: () -> Unit,
) {
    Column(
        Modifier
            .shadow(24.dp, RoundedCornerShape(28.dp), spotColor = GasColors.PlumShadow, ambientColor = GasColors.PlumShadow)
            .clip(RoundedCornerShape(28.dp))
            .background(GasColors.CardWhite)
            .padding(start = 20.dp, end = 20.dp, top = 26.dp, bottom = 24.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Text(emoji, fontSize = 50.sp)
        Text(question, style = GasText.Question, color = GasColors.TextPrimary,
            modifier = Modifier.padding(top = 14.dp))
        Text("ANONYMOUS · BE NICE", style = GasText.Tag, color = GasColors.Violet,
            modifier = Modifier.padding(top = 8.dp))

        // 2×2 grid
        Column(Modifier.padding(top = 22.dp), verticalArrangement = Arrangement.spacedBy(12.dp)) {
            choices.chunked(2).forEachIndexed { rowIdx, row ->
                Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
                    row.forEachIndexed { colIdx, c ->
                        val slotIndex = rowIdx * 2 + colIdx
                        ChoiceButton(c.name, slotIndex, Modifier.weight(1f)) { onPick(c) }
                    }
                }
            }
        }

        Text("🔄 Shuffle names", style = GasText.Body, color = GasColors.TextTertiary,
            modifier = Modifier.padding(top = 20.dp).clickable { onShuffle() })
    }
}

@Composable
fun ChoiceButton(name: String, slotIndex: Int, modifier: Modifier = Modifier, onClick: () -> Unit) {
    val slot = GasColors.choiceSlots[slotIndex]
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        if (pressed) 1.04f else 1f,
        spring(dampingRatio = Spring.DampingRatioMediumBouncy), label = "choiceScale",
    )
    val haptics = LocalHapticFeedback.current
    Box(
        modifier
            .scale(scale)
            .clip(RoundedCornerShape(20.dp))
            .background(slot.brush)
            .clickable(interactionSource = interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            }
            .padding(vertical = 16.dp, horizontal = 10.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(name, style = GasText.Choice, color = slot.text)
    }
}
```

### Flame Pill (currency)

```kotlin
@Composable
fun FlamePill(count: Int, onGradient: Boolean = true, modifier: Modifier = Modifier) {
    Row(
        modifier
            .clip(RoundedCornerShape(50))
            .background(if (onGradient) Color.White.copy(alpha = 0.22f) else GasColors.FlameGlow)
            .padding(horizontal = 14.dp, vertical = 7.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Text("🔥", fontSize = 16.sp)
        Text("$count", style = GasText.Pill, color = if (onGradient) Color.White else GasColors.Flame)
    }
}
```

### Progress Dots

```kotlin
@Composable
fun PollProgress(total: Int, index: Int) {
    Row(horizontalArrangement = Arrangement.spacedBy(6.dp)) {
        repeat(total) { i ->
            val w by animateFloatAsState(if (i == index) 30f else 22f, label = "dotW")
            Box(
                Modifier
                    .width(w.dp).height(5.dp).clip(RoundedCornerShape(3.dp))
                    .background(if (i == index) Color.White else Color.White.copy(alpha = 0.35f))
            )
        }
    }
}
```

### Primary Button

```kotlin
@Composable
fun GasPrimaryButton(title: String, onClick: () -> Unit) {
    Box(
        Modifier
            .shadow(10.dp, RoundedCornerShape(50), spotColor = GasColors.Indigo, ambientColor = GasColors.Indigo)
            .clip(RoundedCornerShape(50))
            .background(GasColors.AppGradient)
            .clickable { onClick() }
            .padding(vertical = 15.dp, horizontal = 30.dp),
    ) {
        Text(title, style = GasText.Button, color = Color.White)
    }
}
```

## 4. Poll Loop Screen

Gas's core is the single-card poll loop: a full-bleed gradient, one centered `PollCard`, progress dots below, a "Skip ›" affordance, and the flame pill in the top corner. There is no feed and no scroll — exactly one card on screen, replaced with a spring transition on each vote.

```kotlin
@Composable
fun PollLoopScreen(polls: List<Poll>) {
    var idx by remember { mutableIntStateOf(0) }
    GasBackground {
        Column(
            Modifier.fillMaxSize().padding(horizontal = 18.dp),
            verticalArrangement = Arrangement.Center,
            horizontalAlignment = Alignment.CenterHorizontally,
        ) {
            val p = polls[idx]
            PollCard(
                emoji = p.emoji, question = p.question, choices = p.choices,
                onPick = { if (idx < polls.lastIndex) idx++ },
                onShuffle = { /* reshuffle names */ },
            )
            Spacer(Modifier.height(18.dp))
            PollProgress(total = polls.size, index = idx)
            Spacer(Modifier.height(16.dp))
            Text("Skip ›", style = GasText.Body, color = Color.White.copy(alpha = 0.85f),
                modifier = Modifier.clickable { if (idx < polls.lastIndex) idx++ })
        }
    }
}

data class Poll(val emoji: String, val question: String, val choices: List<PollChoice>)
```

Wrap `PollCard` in an `AnimatedContent(targetState = idx)` with `slideInVertically + fadeIn` entering and `slideOutHorizontally + fadeOut` exiting to get the iOS "fly out / spring in" feel.

## 5. Navigation

Gas has a frosted 5-slot bottom strip over the gradient with a raised white center "Add" action. On Android model it as a `NavigationBar`; active is solid white (no Material tint pill).

```kotlin
@Composable
fun GasBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = Color.White.copy(alpha = 0.16f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Polls"  to Icons.Filled.ViewAgenda,
            "Inbox"  to Icons.Filled.CheckCircle,
            "Add"    to Icons.Filled.Add,           // center — raised white circle
            "School" to Icons.Filled.Apartment,
            "You"    to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            val isAdd = i == 2
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (isAdd) {
                        Box(
                            Modifier
                                .size(50.dp).offset(y = (-16).dp)
                                .shadow(8.dp, RoundedCornerShape(50), spotColor = GasColors.PlumShadow)
                                .clip(RoundedCornerShape(50)).background(Color.White),
                            contentAlignment = Alignment.Center,
                        ) { Icon(icon, null, tint = GasColors.Indigo, modifier = Modifier.size(26.dp)) }
                    } else {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(23.dp))
                    }
                },
                label = if (isAdd) null else { { Text(label, style = GasText.Tab) } },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = Color.White,
                    selectedTextColor   = Color.White,
                    unselectedIconColor = Color.White.copy(alpha = 0.6f),
                    unselectedTextColor = Color.White.copy(alpha = 0.6f),
                    indicatorColor      = Color.Transparent, // no Material pill — Gas has none
                ),
            )
        }
    }
}
```

The top bar is transparent over the gradient: stacked school label + screen title (white) leading, the `FlamePill` trailing.

## 6. Motion

Gas motion is springy and celebratory — cards bounce, flames count up, confetti pops. Nothing is subtle.

| Moment | Compose recipe |
|--------|----------------|
| Choice press | `animateFloatAsState` scale → 1.04 `spring(DampingRatioMediumBouncy)` |
| Card transition (vote) | `AnimatedContent` exit `slideOutHorizontally + fadeOut(280)`, enter `slideInVertically + fadeIn` `spring(stiffness = Medium)` |
| Flame count-up | `Animatable` int tween 600ms; emit `HapticFeedback` per increment + a flame particle |
| Progress dot | `animateFloatAsState` width 22 → 30 `spring(dampingRatio = 0.7)` |
| Milestone | confetti (e.g. `nl.dionsegijn:konfetti-compose`) + number `scaleIn` `spring(DampingRatioLowBouncy)` |
| Tab switch | bouncy `Crossfade`; active icon `scaleIn` pop 1 → 1.15 → 1 |
| Reveal (who picked you) | shimmer 800ms then `fadeIn` + confetti pop |

```kotlin
// Card transition — the canonical Gas "fly out / spring in"
AnimatedContent(
    targetState = idx,
    transitionSpec = {
        (slideInVertically { it / 4 } + fadeIn(spring(stiffness = Spring.StiffnessMedium))) togetherWith
        (slideOutHorizontally { -it } + fadeOut(tween(280)))
    },
    label = "pollCard",
) { i -> PollCard(/* polls[i] */) }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the medium tap on vote; for the soft per-flame tick use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`; a `HapticFeedbackConstants.CONFIRM` on milestone. Keep haptics on by default — they're part of the joy.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Emoji (🔥, 🔄, 😄) are first-class — render as `Text`, they ARE the brand voice.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Polls (tab) | `rectangle.stack` | `Icons.Filled.ViewAgenda` |
| Inbox (tab) | `checkmark.seal` | `Icons.Filled.CheckCircle` |
| Add (center) | `plus` | `Icons.Filled.Add` (raised white circle) |
| School (tab) | `building.2` | `Icons.Filled.Apartment` |
| You (tab) | `person` | `Icons.Filled.Person` |
| Flame (currency) | 🔥 emoji | 🔥 emoji (or `Icons.Filled.LocalFireDepartment` tinted `Flame`) |
| Shuffle | `shuffle` | 🔄 emoji (or `Icons.Filled.Shuffle`) |
| Skip | `chevron.right` | `Icons.Filled.ChevronRight` |
| Lock (hint) | `lock.fill` | `Icons.Filled.Lock` |
| Share / Invite | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Settings | `gearshape.fill` | `Icons.Filled.Settings` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Close | `xmark` | `Icons.Filled.Close` |
| Sparkle | `sparkles` | `Icons.Filled.AutoAwesome` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; springy motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the gradient is dark enough for light-content system bars. Keep the system nav bar transparent so the gradient world extends fully; the raised center circle must clear gesture-nav insets.
- **Light-by-design**: build ONLY the light scheme; do **not** add a dark scheme and do **not** enable Material You `dynamicColorScheme()` — the bright indigo→purple gradient IS the brand and must hold regardless of system theme/wallpaper.
- **Font scaling**: `sp` honors the user's scale on poll questions, card titles, body. Pin layout-critical text (choice labels — the 2×2 grid, 10sp tab labels, tags, progress dots) — derive from `dp` or fix `fontScale = 1f` for those nodes.
- **The 2×2 grid is sacred**: always render four choices as two rows of two (`chunked(2)`); never a single column/row even on small screens — truncate long names instead.
- **Contrast**: `#1A1530` on `#FFFFFF` passes WCAG AA; white on the indigo/pink/green choice gradients passes; the YELLOW slot MUST use `#4A3300` text (white fails on `#FFC93C`) — this pairing is enforced in `choiceSlots`.
- **Touch targets**: Material guidance is 48.dp. Choice tiles are already ≥ 56.dp tall; give the 23.dp tab icons a 48.dp hit; the raised center circle is 50.dp.
- **TalkBack**: read a poll as "Poll: {question}, four choices"; each choice `Modifier.semantics { role = Role.Button }` labeled "{name}, choice {n} of 4"; the flame pill "{count} flames"; announce flame earns via `LiveRegion`.
- **Reduce motion**: when `ANIMATOR_DURATION_SCALE == 0`, replace the card fly-out/spring with a quick `Crossfade`, skip confetti (static check), and set flame count instantly — but keep the experience cheerful and colorful.
- **Teen safety**: keep all copy positive; never expose real identities in poll context; respect Digital Wellbeing / parental controls.
