# Nextdoor (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Nextdoor's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Nextdoor's warm cream canvas, civic green, hyperlocal map pins, the verified-neighbor badge, and location lines) while making everything idiomatic Android — `NavigationBar` with a center FAB via `Scaffold`, `Maps Compose` instead of MapKit, `sp`/`dp` instead of `pt`. Nextdoor also ships on Android, so this maps cleanly.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+`, and `com.google.maps.android:maps-compose` for the map.

## 1. Color Tokens

```kotlin
// ui/theme/NextdoorColors.kt
import androidx.compose.ui.graphics.Color

object NextdoorColors {
    // Canvas & Surfaces — warm cream, light only
    val Cream    = Color(0xFFFAF9F6)
    val Surface1 = Color(0xFFFFFFFF)
    val Surface2 = Color(0xFFF2F0EB)
    val Divider  = Color(0xFFE4E2DD)

    // Text — warm ink
    val Text     = Color(0xFF221E1F)
    val TextSec  = Color(0xFF6B6864)
    val TextTer  = Color(0xFF9C9893)

    // Brand
    val Green        = Color(0xFF00B246)
    val GreenPressed = Color(0xFF007A30)
    val LinkForest   = Color(0xFF006B3C)

    // Semantic / Map categories
    val AlertAmber = Color(0xFFE58A00)
    val Error      = Color(0xFFD93A2B)
    val EventBlue  = Color(0xFF1B6FB3)
    val ForSale    = Color(0xFF6E4FA3)
}
```

Wire it into a Material 3 `lightColorScheme`. Nextdoor is **light-only** by design — the warm cream is the identity; do **not** provide a dark scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val NextdoorScheme = lightColorScheme(
    primary        = NextdoorColors.Green,
    onPrimary      = Color(0xFFFFFFFF),
    background      = NextdoorColors.Cream,         // warm cream, not white
    onBackground    = NextdoorColors.Text,
    surface         = NextdoorColors.Surface1,
    onSurface       = NextdoorColors.Text,
    surfaceVariant  = NextdoorColors.Surface2,
    outline         = NextdoorColors.Divider,
    error           = NextdoorColors.Error,
)

@Composable
fun NextdoorTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = NextdoorScheme, typography = NextdoorTypography, content = content)
```

## 2. Typography

Nextdoor uses **Lato**. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to `FontFamily.Default` (Roboto) at the same sizes/weights if Lato is unavailable.

```kotlin
// ui/theme/NextdoorType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Lato = FontFamily(
    Font(R.font.lato_regular, FontWeight.Normal),   // 400
    Font(R.font.lato_bold,    FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, 16sp body at 1.45)
object NextdoorText {
    val TitleLarge   = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.3).sp)
    val Section      = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 27.sp, letterSpacing = (-0.2).sp)
    val PostTitle    = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Name         = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 20.sp)
    val Body         = TextStyle(Lato, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 23.sp)   // 1.45
    val BodySettings = TextStyle(Lato, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 21.sp)
    val Meta         = TextStyle(Lato, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val Chip         = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 14.sp, lineHeight = 17.sp)
    val ActionLabel  = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 14.sp, lineHeight = 17.sp)
    val Button       = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 20.sp)
    val Tab          = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 10.sp, lineHeight = 12.sp)
    val PinLabel     = TextStyle(Lato, fontWeight = FontWeight.Bold,   fontSize = 12.sp, lineHeight = 14.sp)
}

val NextdoorTypography = Typography(
    headlineLarge = NextdoorText.TitleLarge,
    headlineSmall = NextdoorText.Section,
    titleMedium   = NextdoorText.Name,
    bodyMedium    = NextdoorText.Body,
    labelSmall    = NextdoorText.Tab,
)
```

## 3. Signature Components

### Neighborhood Feed Card (the core unit)

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun FeedCard(
    name: String,
    verified: Boolean,
    location: String,            // "0.3 mi · Maple Heights · 2h"
    body: String,
    avatarUrl: String,
    modifier: Modifier = Modifier,
) {
    Card(
        modifier = modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .padding(bottom = 12.dp),
        shape = RoundedCornerShape(16.dp),
        colors = CardDefaults.cardColors(containerColor = NextdoorColors.Surface1),
        border = BorderStroke(1.dp, NextdoorColors.Divider),
        elevation = CardDefaults.cardElevation(defaultElevation = 1.dp),  // faint, warm lift
    ) {
        Column(Modifier.padding(16.dp)) {
            Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
                AsyncImage(model = avatarUrl, contentDescription = null,
                    modifier = Modifier.size(44.dp).clip(CircleShape))
                Column(Modifier.weight(1f)) {
                    Row(verticalAlignment = Alignment.CenterVertically,
                        horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                        Text(name, style = NextdoorText.Name, color = NextdoorColors.Text)
                        if (verified) {
                            Icon(Icons.Filled.Verified, contentDescription = "Verified neighbor",
                                tint = NextdoorColors.Green, modifier = Modifier.size(15.dp))
                        }
                    }
                    Text(location, style = NextdoorText.Meta, color = NextdoorColors.TextSec)
                }
                Icon(Icons.Filled.MoreHoriz, null, tint = NextdoorColors.TextSec, modifier = Modifier.size(20.dp))
            }

            Text(body, style = NextdoorText.Body, color = NextdoorColors.Text,
                modifier = Modifier.padding(top = 10.dp))

            HorizontalDivider(thickness = 1.dp, color = NextdoorColors.Divider,
                modifier = Modifier.padding(vertical = 12.dp))

            Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
                CardAction(Icons.Outlined.ThumbUp, "React")
                CardAction(Icons.Outlined.ChatBubbleOutline, "Reply")
                CardAction(Icons.Filled.Share, "Share")
            }
        }
    }
}

@Composable
private fun CardAction(icon: androidx.compose.ui.graphics.vector.ImageVector, label: String) {
    var active by remember { mutableStateOf(false) }
    val scale = remember { androidx.compose.animation.core.Animatable(1f) }
    val haptics = androidx.compose.ui.platform.LocalHapticFeedback.current
    val scope = rememberCoroutineScope()

    Row(
        Modifier
            .defaultMinSize(minHeight = 44.dp)
            .clickable {
                active = !active
                haptics.performHapticFeedback(androidx.compose.ui.hapticfeedback.HapticFeedbackType.TextHandleMove)
                scope.launch { scale.animateTo(1.12f, tween(120)); scale.animateTo(1f, spring()) }
            },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(icon, null,
            tint = if (active) NextdoorColors.Green else NextdoorColors.TextSec,
            modifier = Modifier.size(20.dp).scale(scale.value))
        Text(label, style = NextdoorText.ActionLabel,
            color = if (active) NextdoorColors.Green else NextdoorColors.TextSec)
    }
}
```

### Primary Button & Center Post FAB

```kotlin
@Composable
fun NDPrimaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier, outline: Boolean = false) {
    if (outline) {
        OutlinedButton(
            onClick = onClick, modifier = modifier,
            shape = RoundedCornerShape(24.dp),
            border = BorderStroke(1.5.dp, NextdoorColors.Green),
            colors = ButtonDefaults.outlinedButtonColors(contentColor = NextdoorColors.Green),
            contentPadding = PaddingValues(horizontal = 20.dp, vertical = 10.dp),
        ) { Text(text, style = NextdoorText.Button) }
    } else {
        Button(
            onClick = onClick, modifier = modifier,
            shape = RoundedCornerShape(24.dp),     // friendly radius, not pill, not sharp
            colors = ButtonDefaults.buttonColors(containerColor = NextdoorColors.Green, contentColor = Color.White),
            contentPadding = PaddingValues(horizontal = 24.dp, vertical = 12.dp),
        ) { Text(text, style = NextdoorText.Button) }
    }
}

@Composable
fun CenterPostFab(onClick: () -> Unit) {
    val haptics = androidx.compose.ui.platform.LocalHapticFeedback.current
    FloatingActionButton(
        onClick = { haptics.performHapticFeedback(androidx.compose.ui.hapticfeedback.HapticFeedbackType.LongPress); onClick() },
        containerColor = NextdoorColors.Green,
        contentColor = Color.White,
        shape = CircleShape,
        modifier = Modifier.size(56.dp),
    ) { Icon(Icons.Filled.Add, contentDescription = "Post", modifier = Modifier.size(26.dp)) }
}
```

### Group Chip Row

```kotlin
import androidx.compose.foundation.lazy.LazyRow

@Composable
fun GroupChipRow(groups: List<String>, selected: String, onSelect: (String) -> Unit, modifier: Modifier = Modifier) {
    LazyRow(
        modifier.fillMaxWidth(),
        contentPadding = PaddingValues(horizontal = 16.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        items(groups) { g ->
            val active = g == selected
            Surface(
                onClick = { onSelect(g) },
                shape = CircleShape,
                color = if (active) NextdoorColors.Green else NextdoorColors.Surface2,
            ) {
                Text(g, style = NextdoorText.Chip,
                    color = if (active) Color.White else NextdoorColors.Text,
                    modifier = Modifier.padding(horizontal = 16.dp, vertical = 8.dp))
            }
        }
    }
}
```

## 4. Distinctive System — Hyperlocal Map Pin

```kotlin
enum class PinCategory(val color: Color) {
    Recommendation(NextdoorColors.Green),
    Event(NextdoorColors.EventBlue),
    ForSale(NextdoorColors.ForSale),
    Alert(NextdoorColors.AlertAmber),
}

@Composable
fun MapPin(category: PinCategory, label: String, selected: Boolean, modifier: Modifier = Modifier) {
    val scale by androidx.compose.animation.core.animateFloatAsState(
        if (selected) 1.15f else 1f, tween(200), label = "pinScale",
    )
    Box(
        modifier
            .scale(scale)
            .then(if (selected) Modifier.border(2.dp, NextdoorColors.Green, CircleShape).padding(3.dp) else Modifier)
            .clip(CircleShape)
            .background(category.color)
            .then(if (selected) Modifier.border(2.dp, Color.White, CircleShape) else Modifier)
            .padding(horizontal = 10.dp, vertical = 6.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = NextdoorText.PinLabel, color = Color.White)
    }
}
```

Use as the content of a `MarkerComposable` in `maps-compose`. Tapping a marker raises a bottom peek sheet (`ModalBottomSheet` or a `BottomSheetScaffold` with a partially-expanded detent) showing the selected post.

## 5. Bottom Navigation (Tab Bar with Center FAB)

Material 3 has no built-in center-docked FAB in `NavigationBar`. Compose it via `Scaffold` — a `NavigationBar` plus a `FloatingActionButton` placed with `FabPosition.Center` and `floatingActionButtonPosition` so the green Post button rises out of the bar.

```kotlin
@Composable
fun NextdoorScaffold(selected: Int, onSelect: (Int) -> Unit, onPost: () -> Unit,
                     content: @Composable (PaddingValues) -> Unit) {
    Scaffold(
        containerColor = NextdoorColors.Cream,
        floatingActionButton = { CenterPostFab(onPost) },
        floatingActionButtonPosition = FabPosition.Center,
        bottomBar = {
            NavigationBar(
                containerColor = NextdoorColors.Surface1,
                tonalElevation = 0.dp,
            ) {
                val items = listOf(
                    "Home" to Icons.Filled.Home,
                    "Map"  to Icons.Filled.Map,
                    null   to null,                              // gap for the docked FAB
                    "Notifications" to Icons.Filled.Notifications,
                    "Inbox" to Icons.Filled.MailOutline,
                )
                items.forEachIndexed { i, pair ->
                    if (pair.first == null) {
                        Spacer(Modifier.weight(1f))              // center FAB sits here
                    } else {
                        NavigationBarItem(
                            selected = selected == i,
                            onClick = { onSelect(i) },
                            icon = { Icon(pair.second!!, contentDescription = pair.first, modifier = Modifier.size(26.dp)) },
                            label = { Text(pair.first!!, style = NextdoorText.Tab) },
                            colors = NavigationBarItemDefaults.colors(
                                selectedIconColor   = NextdoorColors.Green,
                                selectedTextColor   = NextdoorColors.Green,
                                unselectedIconColor = NextdoorColors.TextSec,
                                unselectedTextColor = NextdoorColors.TextSec,
                                indicatorColor      = NextdoorColors.Green.copy(alpha = 0.12f),
                            ),
                        )
                    }
                }
            }
        },
        content = content,
    )
}
```

A 0.5.dp `HorizontalDivider(color = NextdoorColors.Divider)` above the bar provides the hairline; the white bar sits on the cream canvas.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| FAB press | FAB ripple + scale-down feel; `HapticFeedbackType.LongPress`; compose sheet rises with `slideInVertically(tween(300, easing = EaseOut))` |
| Reaction tap | `Animatable` 1 → 1.12 (`tween(120)`) → 1 `spring()`, tint → Green, light haptic |
| Card appear | `AnimatedVisibility` `fadeIn + slideInVertically { 8.dp }` over `tween(250)`, no bounce |
| Map pin select | `animateFloatAsState` 1 → 1.15 over `tween(200)` + green ring; peek sheet `tween(300)` ease-out |
| Chip switch | `Crossfade(tween(200))` on the feed list |

```kotlin
// Reaction (see CardAction): scale.animateTo(1.12f, tween(120)); scale.animateTo(1f, spring())
// Card appear: AnimatedVisibility(enter = fadeIn(tween(250)) + slideInVertically(tween(250)) { it / 6 })
```

Haptics: prefer `LocalHapticFeedback`. For the soft FAB feel use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a short `VibrationEffect.createOneShot(12, 180)`.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| React | `hand.thumbsup` | `Icons.Outlined.ThumbUp` / `Icons.Filled.ThumbUp` |
| Reply | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Verified neighbor | `checkmark.seal.fill` | `Icons.Filled.Verified` |
| Post (center FAB) | `plus` | `Icons.Filled.Add` |
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Map (tab) | `map` / `map.fill` | `Icons.Filled.Map` |
| Notifications (tab) | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| Inbox (tab) | `envelope` / `envelope.fill` | `Icons.Filled.MailOutline` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Map recenter | `location.fill` | `Icons.Filled.MyLocation` |
| Event pin | `calendar` | `Icons.Filled.Event` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `maps-compose` + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the warm cream canvas wants dark-content system bars via `WindowCompat`. Use `Scaffold` insets so the docked FAB and bar clear the gesture nav.
- **Light only**: do **not** provide a dark scheme — keep the `#FAF9F6` cream regardless of the system setting; it is the brand identity.
- **Fonts**: ship Lato in `res/font/`; if absent, the system Roboto renders the same ramp at aligned sizes/weights.
- **Font scaling**: `sp` honors the user's scale — keep it generous on post body, names, and headlines (reading app). Pin only tab labels and map-pin labels (layout-critical).
- **TalkBack**: merge a card's name/location/body with `Modifier.semantics(mergeDescendants = true)`; give the verified seal `contentDescription = "Verified neighbor"`; announce map pins as category + label ("For sale, $45, double-tap to view").
- **Touch targets**: Material guidance is 48.dp minimum. Card actions are 20.dp glyphs — give each a 44–48.dp hit area via `defaultMinSize`; ensure map pins have ≥44.dp effective touch with padding.
- **Contrast**: `#6B6864` on `#FAF9F6` passes WCAG AA at 13sp+; verify `#006B3C` link green on cream meets AA at body sizes (it does). Validate 10sp tab labels and bump if targeting strict compliance.
- **Dynamic color**: do **not** enable Material You — Nextdoor's brand requires the fixed civic green and warm cream canvas regardless of wallpaper.
