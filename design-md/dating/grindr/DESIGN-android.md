# Grindr (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Grindr's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Grindr's true-black canvas, single-yellow accent, proximity cascade, scrimmed overlay text) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyVerticalGrid` for the cascade, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for profile photography.

## 1. Color Tokens

```kotlin
// ui/theme/GrindrColors.kt
import androidx.compose.ui.graphics.Color

object GrindrColors {
    // Canvas & Surfaces (dark-only)
    val Canvas   = Color(0xFF000000)
    val Surface1 = Color(0xFF1A1A1A)
    val Surface2 = Color(0xFF222222)
    val Surface3 = Color(0xFF2C2C2C)
    val Divider  = Color(0xFF2C2C2C)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF999999)
    val TextTertiary  = Color(0xFF5C5C5C)

    // Brand
    val Yellow        = Color(0xFFFFDE00)
    val YellowPressed = Color(0xFFE6C700)
    val YellowTint    = Color(0xFFFFF2A8)

    // Semantic
    val Online = Color(0xFF4CD964)
    val Error  = Color(0xFFFF3B30)
    val Info   = Color(0xFF3D8BFF)
}
```

Wire it into a Material 3 `darkColorScheme` so ripples, dividers, and default component colors inherit the brand. Grindr is dark-only; do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val GrindrScheme = darkColorScheme(
    primary        = GrindrColors.Yellow,
    onPrimary      = Color.Black,            // intentional: black on yellow
    background     = GrindrColors.Canvas,
    onBackground   = GrindrColors.TextPrimary,
    surface        = GrindrColors.Surface1,
    onSurface      = GrindrColors.TextPrimary,
    surfaceVariant = GrindrColors.Surface2,
    outline        = GrindrColors.Divider,
    error          = GrindrColors.Error,
)

@Composable
fun GrindrTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = GrindrScheme, typography = GrindrTypography, content = content)
```

## 2. Typography

Grindr ships a custom product grotesque. The closest free substitute is **Inter** — drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system grotesque (Roboto) if Inter is unavailable.

```kotlin
// ui/theme/GrindrType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular, FontWeight.Normal), // 400
    Font(R.font.inter_bold,    FontWeight.Bold),   // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object GrindrText {
    val ProfileName = TextStyle(Inter, fontWeight = FontWeight.Bold,   fontSize = 24.sp, lineHeight = 28.sp, letterSpacing = (-0.3).sp)
    val Title       = TextStyle(Inter, fontWeight = FontWeight.Bold,   fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,   fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val TileName    = TextStyle(Inter, fontWeight = FontWeight.Bold,   fontSize = 15.sp, lineHeight = 18.sp)
    val RowTitle    = TextStyle(Inter, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 22.sp)
    val Message     = TextStyle(Inter, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 22.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val Distance    = TextStyle(Inter, fontWeight = FontWeight.Bold,   fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.2.sp)
    val LabelUpper  = TextStyle(Inter, fontWeight = FontWeight.Bold,   fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,   fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = 0.2.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.Bold,   fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val GrindrTypography = Typography(
    headlineLarge = GrindrText.ProfileName,
    headlineSmall = GrindrText.Title,
    titleMedium   = GrindrText.RowTitle,
    bodyMedium    = GrindrText.Body,
    labelSmall    = GrindrText.Tab,
)
```

## 3. Signature Components

### Cascade Grid Tile

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.border
import coil.compose.AsyncImage

@Composable
fun GrindrTile(
    name: String,
    distance: String,
    photoUrl: String,
    online: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        modifier
            .aspectRatio(1f)
            .scale(if (pressed) 0.97f else 1f)
            .clickable(interaction, indication = null, onClick = onClick),
    ) {
        AsyncImage(
            model = photoUrl,
            contentDescription = "$name, $distance${if (online) ", online" else ""}",
            modifier = Modifier.matchParentSize(),
            contentScale = ContentScale.Crop,
        )
        // Bottom scrim for overlay legibility
        Box(
            Modifier
                .matchParentSize()
                .background(
                    Brush.verticalGradient(
                        0.55f to Color.Transparent,
                        1f to Color.Black.copy(alpha = 0.65f),
                    )
                )
        )
        if (online) {
            Box(
                Modifier
                    .align(Alignment.TopEnd).padding(8.dp)
                    .size(10.dp).clip(CircleShape)
                    .background(GrindrColors.Online)
                    .border(2.dp, Color.Black, CircleShape)
            )
        }
        Column(
            Modifier.align(Alignment.BottomStart).padding(8.dp),
            verticalArrangement = Arrangement.spacedBy(2.dp),
        ) {
            Text(name, style = GrindrText.TileName, color = Color.White,
                maxLines = 1, overflow = TextOverflow.Ellipsis)
            Text(distance, style = GrindrText.Distance, color = Color.White)
        }
    }
}
```

### Yellow Tap Button

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.material.icons.filled.LocalFireDepartment
import androidx.compose.material3.Icon
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.Dp

@Composable
fun GrindrTapButton(
    tapped: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    size: Dp = 56.dp,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        if (pressed) 0.92f else 1f,
        spring(dampingRatio = 0.7f, stiffness = 600f), label = "tapScale",
    )
    val haptics = LocalHapticFeedback.current
    Box(
        modifier
            .size(size)
            .scale(scale)
            .clip(CircleShape)
            .then(
                if (tapped) Modifier.border(1.dp, GrindrColors.TextSecondary, CircleShape)
                else Modifier.background(if (pressed) GrindrColors.YellowPressed else GrindrColors.Yellow)
            )
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            if (tapped) Icons.Filled.Check else Icons.Filled.LocalFireDepartment,
            contentDescription = if (tapped) "Tapped" else "Tap",
            tint = if (tapped) Color.White else Color.Black, // intentional: black on yellow
            modifier = Modifier.size(size * 0.42f),
        )
    }
}
```

### Primary Pill (Send / Chat)

```kotlin
enum class PillStyle { Filled, Outline }

@Composable
fun GrindrPillButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    style: PillStyle = PillStyle.Filled,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "pillScale")
    val filled = style == PillStyle.Filled
    Box(
        modifier
            .scale(scale)
            .clip(CircleShape)
            .then(
                if (filled) Modifier.background(if (pressed) GrindrColors.YellowPressed else GrindrColors.Yellow)
                else Modifier.border(1.dp, GrindrColors.TextSecondary, CircleShape)
            )
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = if (filled) 28.dp else 24.dp, vertical = 12.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = GrindrText.Button,
            color = if (filled) Color.Black else GrindrColors.TextPrimary)
    }
}
```

### Chat Bubble

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape

@Composable
fun GrindrBubble(text: String, outgoing: Boolean, modifier: Modifier = Modifier) {
    val shape = RoundedCornerShape(
        topStart = 18.dp, topEnd = 18.dp,
        bottomEnd = if (outgoing) 4.dp else 18.dp,
        bottomStart = if (outgoing) 18.dp else 4.dp,
    )
    Box(
        modifier
            .fillMaxWidth()
            .wrapContentWidth(if (outgoing) Alignment.End else Alignment.Start),
    ) {
        Box(
            Modifier
                .widthIn(max = 280.dp)
                .clip(shape)
                .background(if (outgoing) GrindrColors.Yellow else GrindrColors.Surface2)
                .padding(horizontal = 14.dp, vertical = 10.dp),
        ) {
            Text(text, style = GrindrText.Message,
                color = if (outgoing) Color.Black else Color.White)
        }
    }
}
```

### Message List Row

```kotlin
@Composable
fun GrindrMessageRow(
    name: String,
    preview: String,
    time: String,
    avatarUrl: String,
    online: Boolean,
    unread: Boolean,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Row(
        modifier
            .fillMaxWidth()
            .height(72.dp)
            .background(if (pressed) GrindrColors.Surface1 else GrindrColors.Canvas)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box {
            AsyncImage(avatarUrl, null,
                Modifier.size(52.dp).clip(CircleShape), contentScale = ContentScale.Crop)
            if (online) {
                Box(
                    Modifier.align(Alignment.TopEnd)
                        .size(12.dp).clip(CircleShape)
                        .background(GrindrColors.Online)
                        .border(2.dp, Color.Black, CircleShape)
                )
            }
        }
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(3.dp)) {
            Text(name, style = GrindrText.RowTitle, color = Color.White,
                maxLines = 1, overflow = TextOverflow.Ellipsis)
            Text(preview, style = GrindrText.Meta,
                color = if (unread) Color.White.copy(alpha = 0.85f) else GrindrColors.TextSecondary,
                maxLines = 1, overflow = TextOverflow.Ellipsis)
        }
        Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(time, style = GrindrText.Meta.copy(fontSize = 12.sp), color = GrindrColors.TextSecondary)
            if (unread) Box(Modifier.size(8.dp).clip(CircleShape).background(GrindrColors.Yellow))
        }
    }
}
```

## 4. Cascade Grid (lazy-load fade)

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.fadeIn
import androidx.compose.animation.core.tween
import androidx.compose.foundation.lazy.grid.GridCells
import androidx.compose.foundation.lazy.grid.LazyVerticalGrid
import androidx.compose.foundation.lazy.grid.items

@Composable
fun GrindrCascade(profiles: List<Profile>, onOpen: (Profile) -> Unit) {
    LazyVerticalGrid(
        columns = GridCells.Fixed(3),
        horizontalArrangement = Arrangement.spacedBy(2.dp),
        verticalArrangement = Arrangement.spacedBy(2.dp),
        modifier = Modifier.background(GrindrColors.Canvas),
    ) {
        items(profiles, key = { it.id }) { p ->
            var visible by remember { mutableStateOf(false) }
            LaunchedEffect(Unit) { visible = true }
            AnimatedVisibility(visible, enter = fadeIn(tween(180))) {
                GrindrTile(p.name, p.distance, p.photoUrl, p.online, { onOpen(p) })
            }
        }
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Grindr's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 94%-opaque black surface. **Active tint is Grindr Yellow.**

```kotlin
@Composable
fun GrindrBottomBar(selected: Int, onSelect: (Int) -> Unit, unreadTaps: Int = 0, unreadMsgs: Int = 0) {
    NavigationBar(
        containerColor = GrindrColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        data class Item(val label: String, val icon: ImageVector, val badge: Int)
        val items = listOf(
            Item("Browse",   Icons.Filled.GridView,            0),
            Item("Taps",     Icons.Filled.LocalFireDepartment, unreadTaps),
            Item("Messages", Icons.Filled.ChatBubble,          unreadMsgs),
            Item("Profile",  Icons.Filled.Person,              0),
        )
        items.forEachIndexed { i, it ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (it.badge > 0) {
                        BadgedBox(badge = {
                            Badge(containerColor = GrindrColors.Yellow, contentColor = Color.Black) {
                                Text("${it.badge}", style = GrindrText.Tab)
                            }
                        }) { Icon(it.icon, it.label, Modifier.size(26.dp)) }
                    } else {
                        Icon(it.icon, it.label, Modifier.size(26.dp))
                    }
                },
                label = { Text(it.label, style = GrindrText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = GrindrColors.Yellow,
                    selectedTextColor   = GrindrColors.Yellow,
                    unselectedIconColor = GrindrColors.TextSecondary,
                    unselectedTextColor = GrindrColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // Grindr has no Material pill
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Tap send | `animateFloatAsState` 1 → 0.92 `spring(dampingRatio = 0.7f)`; `HapticFeedbackType.LongPress` (≈ iOS success) |
| Cascade lazy-load | `AnimatedVisibility(enter = fadeIn(tween(180)))` per tile as it composes |
| Profile sheet present | `SharedTransitionLayout` + `Modifier.sharedElement()` on the tile photo → hero (Compose 1.7+); fallback `slideInVertically` |
| Photo carousel | `HorizontalPager` with a `PagerState`; dot indicator from `currentPage` |
| Message send | outgoing bubble `AnimatedVisibility(enter = scaleIn(initialScale = 0.9f) + fadeIn())` |
| Pull-to-refresh | `PullToRefreshContainer` tinted `GrindrColors.Yellow` |

```kotlin
// Tap success "flame pulse" — a brief expanding ring behind the button
@Composable
fun FlamePulse(trigger: Boolean) {
    val r = remember { Animatable(0f) }
    LaunchedEffect(trigger) {
        if (trigger) { r.snapTo(0f); r.animateTo(1f, tween(360)) }
    }
    Box(
        Modifier
            .size(56.dp)
            .scale(1f + r.value * 0.6f)
            .clip(CircleShape)
            .background(GrindrColors.Yellow.copy(alpha = (1f - r.value) * 0.5f))
    )
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(12, ...)` to approximate iOS's success notification.

## 7. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Grindr's glyphs (mask logomark, tap flame) as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Tap action | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Tapped (sent) | `checkmark` | `Icons.Filled.Check` |
| Favorite | `star` / `star.fill` | `Icons.Outlined.StarBorder` / `Icons.Filled.Star` |
| Block | `nosign` | `Icons.Filled.Block` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Send arrow | `arrow.up.circle.fill` | `Icons.Filled.ArrowCircleUp` |
| Photo / camera | `camera.fill` | `Icons.Filled.PhotoCamera` |
| Filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Settings | `gearshape.fill` | `Icons.Filled.Settings` |
| Browse (tab) | `square.grid.3x3.fill` | `Icons.Filled.GridView` |
| Taps (tab) | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Messages (tab) | `bubble.left.fill` | `Icons.Filled.ChatBubble` |
| Profile (tab) | `person.fill` | `Icons.Filled.Person` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; Coil + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the true-black canvas wants light system-bar icons via `WindowCompat`. The cascade goes edge-to-edge horizontally; use `Scaffold` insets so the tab bar clears the gesture nav and the chat composer clears the IME (`Modifier.imePadding()`).
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on chat text, message-list names, profile body. Pin layout-locked text (grid overlay name/distance, tab labels) by deriving size from `dp` or a fixed `Density` override so the cascade overlay never reflows over photos.
- **TalkBack**: each tile is one node — set a combined `contentDescription` ("Alex, 220 feet, online"); the Tap button announces its sent state; merge message-row name + preview with `Modifier.semantics(mergeDescendants = true)`.
- **Touch targets**: Material guidance is 48.dp minimum. The 56.dp Tap button and 72.dp rows are clear; the full cascade tile (~129.dp) is the tap target.
- **Contrast**: overlay text is white weight 700 over a `Color.Black.copy(alpha = 0.65f)` scrim — validate over the brightest photos and raise the scrim to 0.7 if it fails WCAG AA. Black on `#FFDE00` passes AAA — never use white text on the yellow button.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Grindr's brand requires the fixed true-black canvas and single-yellow accent regardless of wallpaper.
