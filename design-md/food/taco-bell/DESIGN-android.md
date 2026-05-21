# Taco Bell (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Taco Bell's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, the brand `Brush`, a `Typography` set, paste-ready `@Composable`s (box-preview hero, numbered step pills, the customizer option row, the persistent gradient CTA bar), navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the purple→magenta brand gradient, the violet-black dark-first canvas, the build-your-box customizer, the yellow value highlight) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush.linearGradient`, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for food imagery. No color extraction — Taco Bell's palette is a fixed gradient brand set, so Palette is not used. Taco Bell is **dark-first**; a secondary light scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/TBColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object TBColors {
    // Brand
    val Purple        = Color(0xFF702082)
    val PurpleBright  = Color(0xFF8A2BA0)
    val Magenta       = Color(0xFFC72BC8)
    val MagentaPressed= Color(0xFFA8239F)
    val Pink          = Color(0xFFE928A0)

    // Energy / value
    val Yellow = Color(0xFFFFC700)
    val Orange = Color(0xFFFF6A1A)

    // Text on yellow / gradient
    val OnYellow   = Color(0xFF2A1530)
    val OnGradient = Color(0xFFFFFFFF)

    // Canvas & Surfaces (Dark — primary)
    val Canvas   = Color(0xFF0E0A14) // purple-tinted near-black
    val Surface1 = Color(0xFF1A1322)
    val Surface2 = Color(0xFF261B32)
    val Divider  = Color(0xFF34273F)

    // Canvas & Surfaces (Light — secondary)
    val CanvasLight   = Color(0xFFFFFFFF)
    val SurfaceLight1 = Color(0xFFF6F2F8)
    val SurfaceLight2 = Color(0xFFEDE6F1)
    val DividerLight  = Color(0xFFE3D9EA)

    // Text
    val TextPrimaryD   = Color(0xFFF4EFF7)
    val TextSecondaryD = Color(0xFFA99BB6)
    val TextTertiaryD  = Color(0xFF6E6079)
    val TextPrimaryL   = Color(0xFF1F1626)

    // Semantic
    val Success = Color(0xFF36C275)
    val Error   = Color(0xFFFF4D6D)
}

// The brand gradient — the identity on screen
val TBBrandBrush = Brush.linearGradient(listOf(TBColors.Purple, TBColors.Magenta))
val TBBoxHeroBrush = Brush.linearGradient(
    listOf(TBColors.Purple, Color(0xFF4A1559), Color(0xFF2B0C36))
)
```

Wire it into both schemes. Taco Bell is **dark-first** — the default scheme is the violet-black; the light scheme is secondary. The gradient and yellow are identical in both.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val TBDark = darkColorScheme(
    primary        = TBColors.Magenta,
    onPrimary      = Color.White,
    secondary      = TBColors.Yellow,
    onSecondary    = TBColors.OnYellow,
    background      = TBColors.Canvas,
    onBackground    = TBColors.TextPrimaryD,
    surface         = TBColors.Surface1,
    onSurface       = TBColors.TextPrimaryD,
    surfaceVariant  = TBColors.Surface2,
    outline         = TBColors.Divider,
    error           = TBColors.Error,
)

private val TBLight = lightColorScheme(
    primary        = TBColors.Magenta,
    onPrimary      = Color.White,
    secondary      = TBColors.Yellow,
    onSecondary    = TBColors.OnYellow,
    background      = TBColors.CanvasLight,
    onBackground    = TBColors.TextPrimaryL,
    surface         = TBColors.SurfaceLight1,
    onSurface       = TBColors.TextPrimaryL,
    surfaceVariant  = TBColors.SurfaceLight2,
    outline         = TBColors.DividerLight,
    error           = TBColors.Error,
)

@Composable
fun TBTheme(
    dark: Boolean = true, // dark-first by default
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) TBDark else TBLight,
    typography = TBTypography,
    content = content,
)
```

## 2. Typography

Taco Bell uses a clean techy-geometric brand sans — drop the licensed TTFs in `res/font/`. Weight + gradient + yellow carry hierarchy. Numerals are tabular for money/steps.

```kotlin
// ui/theme/TBType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val TacoBellSans = FontFamily(
    Font(R.font.tacobellsans_regular, FontWeight.Normal),
    Font(R.font.tacobellsans_medium,  FontWeight.Medium),
    Font(R.font.tacobellsans_bold,    FontWeight.Bold),
)

object TBText {
    val ScreenTitle  = TextStyle(TacoBellSans, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.4).sp)
    val BuilderTitle = TextStyle(TacoBellSans, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 33.sp, letterSpacing = (-0.5).sp)
    val Section      = TextStyle(TacoBellSans, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp)
    val CardTitle    = TextStyle(TacoBellSans, fontWeight = FontWeight.ExtraBold, fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.3).sp)
    val Body         = TextStyle(TacoBellSans, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val Option       = TextStyle(TacoBellSans, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 21.sp)
    val Price        = TextStyle(TacoBellSans, fontWeight = FontWeight.ExtraBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Meta         = TextStyle(TacoBellSans, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Eyebrow      = TextStyle(TacoBellSans, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 13.sp, letterSpacing = 1.0.sp)
    val StepPill     = TextStyle(TacoBellSans, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp)
    val Button       = TextStyle(TacoBellSans, fontWeight = FontWeight.ExtraBold, fontSize = 15.sp, lineHeight = 15.sp, letterSpacing = 0.2.sp)
    val Chip         = TextStyle(TacoBellSans, fontWeight = FontWeight.ExtraBold, fontSize = 12.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
    val Caption      = TextStyle(TacoBellSans, fontWeight = FontWeight.Normal,    fontSize = 12.sp, lineHeight = 17.sp)
    val Tab          = TextStyle(TacoBellSans, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

val TBTypography = Typography(
    headlineLarge = TBText.ScreenTitle,
    headlineMedium = TBText.Section,
    titleMedium   = TBText.CardTitle,
    bodyMedium    = TBText.Body,
    labelSmall    = TBText.Tab,
)
```

## 3. Signature Components

### Box-Preview Hero

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

@Composable
fun BoxPreviewHero(boxName: String, itemSummary: String, price: String, modifier: Modifier = Modifier) {
    Box(
        modifier
            .fillMaxWidth()
            .padding(horizontal = 18.dp)
            .height(150.dp)
            .clip(RoundedCornerShape(20.dp))
            .background(TBBoxHeroBrush)
            .border(1.dp, TBColors.Divider, RoundedCornerShape(20.dp)),
    ) {
        // glow blooms
        Box(
            Modifier.align(Alignment.TopEnd).offset(x = 28.dp, y = (-28).dp)
                .size(130.dp).clip(CircleShape)
                .background(Brush.radialGradient(listOf(TBColors.Magenta.copy(alpha = 0.30f), Color.Transparent)))
        )
        Box(
            Modifier.align(Alignment.BottomStart).offset(x = (-30).dp, y = 30.dp)
                .size(120.dp).clip(CircleShape)
                .background(Brush.radialGradient(listOf(TBColors.Yellow.copy(alpha = 0.20f), Color.Transparent)))
        )

        // stylized taco
        Box(Modifier.fillMaxSize(), contentAlignment = Alignment.Center) {
            Box(Modifier.size(width = 96.dp, height = 70.dp)) {
                Box(Modifier.align(Alignment.BottomCenter).padding(bottom = 30.dp)
                    .fillMaxWidth(0.75f).height(26.dp)
                    .clip(RoundedCornerShape(topStart = 40.dp, topEnd = 40.dp))
                    .background(Color(0xFF8FBF4A)))
                Box(Modifier.align(Alignment.BottomCenter)
                    .fillMaxWidth(0.85f).height(44.dp)
                    .clip(RoundedCornerShape(bottomStart = 60.dp, bottomEnd = 60.dp))
                    .background(Color(0xFFE8A53C)))
            }
        }

        Column(Modifier.align(Alignment.BottomStart).padding(14.dp)) {
            Text(boxName, style = TBText.Price, color = Color.White)
            Text(itemSummary, style = TBText.Caption, color = TBColors.Yellow)
        }
        Text(price, style = TBText.BuilderTitle, color = TBColors.Yellow,
            modifier = Modifier.align(Alignment.BottomEnd).padding(14.dp))
    }
}
```

### Numbered Step Pills

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material3.Icon

enum class StepState { Done, Active, Todo }

@Composable
fun StepPill(index: Int, label: String, state: StepState) {
    val bgModifier = if (state == StepState.Active)
        Modifier.background(TBBrandBrush) else Modifier.background(TBColors.Surface1)
    val border = when (state) {
        StepState.Done -> TBColors.Success
        StepState.Active -> Color.Transparent
        StepState.Todo -> TBColors.Divider
    }
    val textColor = when (state) {
        StepState.Active -> Color.White
        StepState.Done -> TBColors.TextPrimaryD
        StepState.Todo -> TBColors.TextSecondaryD
    }
    Row(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .then(bgModifier)
            .border(1.dp, border, RoundedCornerShape(999.dp))
            .height(30.dp)
            .padding(horizontal = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Box(
            Modifier.size(16.dp).clip(CircleShape).background(
                when (state) {
                    StepState.Active -> Color.White.copy(alpha = 0.25f)
                    StepState.Done -> TBColors.Success
                    StepState.Todo -> TBColors.Surface2
                }
            ),
            contentAlignment = Alignment.Center,
        ) {
            if (state == StepState.Done) {
                Icon(Icons.Filled.Check, null, tint = Color(0xFF06210F), modifier = Modifier.size(10.dp))
            } else {
                Text("$index", style = TBText.StepPill.copy(fontSize = 10.sp),
                    color = if (state == StepState.Active) Color.White else TBColors.TextSecondaryD)
            }
        }
        Text(label, style = TBText.StepPill, color = textColor)
    }
}

@Composable
fun StepPillRow() {
    LazyRow(
        horizontalArrangement = Arrangement.spacedBy(8.dp),
        contentPadding = PaddingValues(horizontal = 18.dp),
    ) {
        item { StepPill(1, "Main", StepState.Done) }
        item { StepPill(2, "Side", StepState.Active) }
        item { StepPill(3, "Drink", StepState.Todo) }
        item { StepPill(4, "Sweet", StepState.Todo) }
    }
}
```

### Customizer Option Row

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Remove
import androidx.compose.runtime.*
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

sealed interface OptionControl {
    data class Radio(val on: Boolean, val onSelect: () -> Unit) : OptionControl
    data class Stepper(val qty: Int, val onChange: (Int) -> Unit) : OptionControl
}

@Composable
fun OptionRow(
    name: String, subtitle: String, upcharge: String? = null,
    thumbColor: Color, control: OptionControl,
) {
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier
            .fillMaxWidth()
            .padding(vertical = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(Modifier.size(46.dp).clip(RoundedCornerShape(12.dp)).background(thumbColor))
        Column(Modifier.weight(1f)) {
            Text(name, style = TBText.Option, color = TBColors.TextPrimaryD)
            Text(subtitle, style = TBText.Caption, color = TBColors.TextSecondaryD)
        }
        upcharge?.let {
            Text(it, style = TBText.Price.copy(fontSize = 13.sp), color = TBColors.TextSecondaryD,
                modifier = Modifier.padding(end = 4.dp))
        }
        when (control) {
            is OptionControl.Radio -> {
                Box(
                    Modifier
                        .size(24.dp)
                        .clip(CircleShape)
                        .border(2.dp, if (control.on) TBColors.Magenta else TBColors.Divider, CircleShape)
                        .clickable {
                            control.onSelect()
                            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                        },
                    contentAlignment = Alignment.Center,
                ) {
                    if (control.on) Box(Modifier.size(14.dp).clip(CircleShape).background(TBColors.Magenta))
                }
            }
            is OptionControl.Stepper -> {
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(12.dp)) {
                    StepperBtn(Icons.Filled.Remove) { control.onChange((control.qty - 1).coerceAtLeast(0)) }
                    Text("${control.qty}", style = TBText.Price.copy(fontSize = 14.sp), color = TBColors.TextPrimaryD)
                    StepperBtn(Icons.Filled.Add) { control.onChange(control.qty + 1) }
                }
            }
        }
    }
    Box(Modifier.fillMaxWidth().height(1.dp).background(TBColors.Divider))
}

@Composable
private fun StepperBtn(icon: androidx.compose.ui.graphics.vector.ImageVector, onClick: () -> Unit) {
    Box(
        Modifier.size(26.dp).clip(CircleShape)
            .border(1.5.dp, TBColors.Magenta, CircleShape)
            .clickable(onClick = onClick),
        contentAlignment = Alignment.Center,
    ) { Icon(icon, null, tint = TBColors.Magenta, modifier = Modifier.size(13.dp)) }
}
```

### Buttons

```kotlin
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.interaction.MutableInteractionSource

@Composable
fun TBPrimaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val src = remember { MutableInteractionSource() }
    val pressed by src.collectIsPressedAsState()
    Box(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(14.dp))
            .background(TBBrandBrush)
            .clickable(interactionSource = src, indication = null, onClick = onClick)
            .padding(vertical = 15.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = TBText.Button, color = Color.White,
            modifier = Modifier.alpha(if (pressed) 0.92f else 1f))
    }
}

@Composable
fun TBValueButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Box(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(14.dp))
            .background(TBColors.Yellow)
            .clickable(onClick = onClick)
            .padding(vertical = 15.dp),
        contentAlignment = Alignment.Center,
    ) { Text(text, style = TBText.Button, color = TBColors.OnYellow) }
}
```

### Persistent Bottom CTA Bar

```kotlin
@Composable
fun CtaBar(total: String, onAdd: () -> Unit) {
    Row(
        Modifier
            .fillMaxWidth()
            .background(TBColors.Canvas.copy(alpha = 0.96f))
            .padding(horizontal = 18.dp, vertical = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Column {
            Text("Box total", style = TBText.Caption, color = TBColors.TextSecondaryD)
            Text(total, style = TBText.Price.copy(fontSize = 19.sp), color = TBColors.TextPrimaryD)
        }
        Box(Modifier.weight(1f)) { TBPrimaryButton("Add to Order", onAdd) }
    }
}
```

## 4. Navigation

Taco Bell has a 5-tab bottom strip (Home / Menu / Rewards / Bag / Account). On Android, model it as a `NavigationBar`. Active is a filled glyph in Electric Magenta — there is **no** Material tint pill.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun TBBottomBar(selected: Int, bagCount: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = TBColors.Canvas, tonalElevation = 0.dp) {
        data class Item(val label: String, val icon: androidx.compose.ui.graphics.vector.ImageVector, val badge: Int = 0)
        val items = listOf(
            Item("Home", Icons.Filled.Home),
            Item("Menu", Icons.Filled.RestaurantMenu),
            Item("Rewards", Icons.Filled.Schedule),
            Item("Bag", Icons.Filled.ShoppingBag, bagCount),
            Item("Account", Icons.Filled.AccountCircle),
        )
        items.forEachIndexed { i, it ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (it.badge > 0) {
                        BadgedBox(badge = { Badge(containerColor = TBColors.Magenta) { Text("${it.badge}") } }) {
                            Icon(it.icon, it.label, Modifier.size(22.dp))
                        }
                    } else Icon(it.icon, it.label, Modifier.size(22.dp))
                },
                label = { Text(it.label, style = TBText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = TBColors.Magenta,
                    selectedTextColor = TBColors.Magenta,
                    unselectedIconColor = Color(0xFF7A6C86),
                    unselectedTextColor = Color(0xFF7A6C86),
                    indicatorColor = Color.Transparent, // no Material pill — Taco Bell has none
                ),
            )
        }
    }
}
```

The builder header (yellow eyebrow `Eyebrow` + builder title `BuilderTitle` + a `Caption` sub-line) sits above the box-preview hero. Keep the `CtaBar` pinned via a `Scaffold` `bottomBar` slot stacked above the `NavigationBar`, or as a sticky footer inside the build screen.

## 5. Motion

Taco Bell motion is punchy and quick — 150–300ms, tied to building/ordering. Depth is glow, not heavy shadow.

| Moment | Compose recipe |
|--------|----------------|
| Add to Order | gradient CTA brightness flash via an overlay `alpha` `keyframes` `0 → 0.12 → 0`; box total `animateIntAsState` roll; soft haptic |
| Step advance | step pill `Crossfade(targetState = state, tween(250))` default → gradient → green; radio dot `scaleIn(tween(150))` |
| Box-preview morph | contents `Crossfade(tween(200))` + a subtle glow `alpha` pulse |
| Cravings "Add" → customizer | shared-element `SharedTransitionLayout` gradient-image zoom `tween(300)` |
| Combo / sauce sheet | `ModalBottomSheet`, 28.dp top corners, `tween(300)` |
| Reward redeem | yellow chip `scaleIn(spring(dampingRatio = 0.5f))`; success haptic |
| Pull-to-refresh | `PullToRefreshContainer`, gradient indicator, tick haptic on release |

```kotlin
// Step pill advancing — the canonical Taco Bell build feedback
Crossfade(targetState = stepState, animationSpec = tween(250), label = "stepAdvance") { s ->
    StepPill(index = idx, label = label, state = s)
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for the light tick on radio/step/sauce select; `HapticFeedbackType.LongPress` (soft) on add-to-order; a stronger confirm (`VibrationEffect.EFFECT_HEAVY_CLICK`) on order-placed / reward-redeemed.

## 6. Icons

Taco Bell uses the proprietary bell + standard glyphs; closest first-party set is `androidx.compose.material:material-icons-extended`. Bundle the bell as a vector drawable; render the brand gradient with `Brush.linearGradient`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Menu (tab) | `menucard` | `Icons.Filled.RestaurantMenu` |
| Rewards (tab) | `clock` / `clock.fill` | `Icons.Filled.Schedule` |
| Bag (tab) | `bag` / `bag.fill` | `Icons.Filled.ShoppingBag` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Step done | `checkmark` | `Icons.Filled.Check` |
| Stepper minus / plus | `minus` / `plus` | `Icons.Filled.Remove` / `Icons.Filled.Add` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Sauce / heat | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Reward | `gift.fill` | `Icons.Filled.CardGiftcard` |
| Store / location | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Combo upsell | `plus.circle.fill` | `Icons.Filled.AddCircle` |
| Close | `xmark` | `Icons.Filled.Close` |
| Customize | `slider.horizontal.3` | `Icons.Filled.Tune` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; gradients, `ModalBottomSheet`, and modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the violet-black canvas wants light-content system bars. The persistent CTA bar must sit above the `NavigationBar` and the gesture inset — pad with `WindowInsets.navigationBars`.
- **Gradient is the brand**: render every primary CTA, the active step pill, and the box hero with `Brush.linearGradient(listOf(Purple, Magenta))` — never a flat purple where the gradient belongs.
- **Dark-first**: default `TBTheme(dark = true)`; the light scheme is secondary. Text on yellow is always `#2A1530`, text on the gradient always white — never themed away.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen/builder titles, section headers, body, option copy. Pin layout-critical text (step pills, eyebrows, chips/badges, 10sp tab labels, the box-hero price) via `dp` or a fixed-`fontScale` `CompositionLocalProvider`.
- **Tabular numerals**: prices, the running box total, and step counts must not reflow while rolling — use a monospaced-digit feature or fixed number slots.
- **TalkBack**: box hero → "{boxName}, {itemSummary}, {price}"; step pills → "Step {n}, {label}, completed/current/not started"; option rows → "{name}, {subtitle}, selected/quantity {n}"; CTA → "Add to Order, box total {total}".
- **Touch targets**: Material guidance is 48.dp. The 24–26.dp radios/steppers and 22.dp tab icons need a 48.dp hit area via padding; the whole option row should also toggle the radio; the gradient CTA ≥ 48.dp tall.
- **Contrast**: white on the `#702082 → #C72BC8` gradient passes WCAG AA across its span; `#2A1530` on `#FFC700` passes AA (why white-on-yellow is forbidden); `#F4EFF7` on `#0E0A14` passes AA.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, skip the gradient-CTA flash and box-hero morph (set final state directly); make the step-pill state change an instant color swap; keep the radio fill immediate.
- **Dark mode**: the dark theme is the primary design — `#0E0A14`, a purple-tinted near-black (not neutral gray). In the secondary light theme, soften the glow blooms and replace them with a soft drop shadow on the box hero/sheets; keep the gradient + yellow + on-yellow/on-gradient rules intact. Do **not** enable Material You `dynamicColorScheme()` — the purple→magenta gradient must hold regardless of wallpaper.
