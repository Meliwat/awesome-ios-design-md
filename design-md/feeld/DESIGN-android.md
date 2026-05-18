# Feeld (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Feeld's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the swipe deck + Desire chips, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Feeld's true-black canvas, the single rationed acid accent, couple/group-first cards, the Desire chip system, "Connections") while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `swipeable`/drag for the deck, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for profile photos and avatars. **No color extraction / Material You** — Feeld's identity is a fixed black + acid; dynamic wallpaper color must never override it. Feeld is **dark-only** — there is no light scheme.

## 1. Color Tokens

```kotlin
// ui/theme/FeeldColors.kt
import androidx.compose.ui.graphics.Color

object FeeldColors {
    // Canvas & Surfaces (dark-native, only mode)
    val Canvas   = Color(0xFF000000) // true black — Feeld is dark-native
    val Surface1 = Color(0xFF121212)
    val Surface2 = Color(0xFF1C1C1E)
    val Surface3 = Color(0xFF262626)
    val Divider  = Color(0xFF2A2A2A)

    // Brand
    val Acid      = Color(0xFFE8FF63) // rationed to ONE primary action / screen
    val AcidPress = Color(0xFFD4EB4F)
    val Pink      = Color(0xFFFF5C8A)
    val PinkDeep  = Color(0xFFE0447A)
    val Lilac     = Color(0xFFC9B8FF)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFA8A8A8)
    val TextTertiary  = Color(0xFF6E6E6E)
    val OnAcid        = Color(0xFF000000)
    val OnPink        = Color(0xFFFFFFFF)

    // Semantic
    val Success = Color(0xFF66E0A3)
    val Error   = Color(0xFFFF6B6B)
}
```

Feeld is dark-only; the "light" scheme below exists solely so Material components don't crash if the system forces light — it still renders the dark identity.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme

private val FeeldScheme = darkColorScheme(
    primary        = FeeldColors.Acid,
    onPrimary      = FeeldColors.OnAcid,
    secondary      = FeeldColors.Pink,
    onSecondary    = FeeldColors.OnPink,
    background      = FeeldColors.Canvas,
    onBackground   = FeeldColors.TextPrimary,
    surface        = FeeldColors.Surface1,
    onSurface      = FeeldColors.TextPrimary,
    surfaceVariant = FeeldColors.Surface2,
    outline        = FeeldColors.Divider,
    error          = FeeldColors.Error,
)

@Composable
fun FeeldTheme(content: @Composable () -> Unit) =
    MaterialTheme(                          // always dark — never isSystemInDarkTheme()
        colorScheme = FeeldScheme,
        typography  = FeeldTypography,
        content     = content,
    )
```

## 2. Typography

Feeld pairs **Space Grotesk** (personality: display, names, sections, buttons) with **Inter** (legibility: body, metadata, snippets). Both SIL OFL — drop the TTFs in `res/font/`. Weights 400–700 only.

```kotlin
// ui/theme/FeeldType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val SpaceGrotesk = FontFamily(
    Font(R.font.space_grotesk_semibold, FontWeight.SemiBold),
    Font(R.font.space_grotesk_bold,     FontWeight.Bold),
)
val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
)

object FeeldText {
    val Display    = TextStyle(SpaceGrotesk, fontWeight = FontWeight.Bold,     fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.5).sp)
    val Name       = TextStyle(SpaceGrotesk, fontWeight = FontWeight.Bold,     fontSize = 26.sp, lineHeight = 30.sp, letterSpacing = (-0.4).sp)
    val Section    = TextStyle(SpaceGrotesk, fontWeight = FontWeight.SemiBold, fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Subsection = TextStyle(SpaceGrotesk, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 24.sp)
    val ListTitle  = TextStyle(SpaceGrotesk, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Button     = TextStyle(SpaceGrotesk, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp)
    val Body       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val Meta       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val Snippet    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val Chip       = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Caption    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Tab        = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp)
}

val FeeldTypography = Typography(
    headlineLarge  = FeeldText.Display,
    headlineMedium = FeeldText.Name,
    titleLarge     = FeeldText.Section,
    bodyMedium     = FeeldText.Body,
    labelSmall     = FeeldText.Tab,
)
```

## 3. Signature Components

### Discover Card (Couple/Group-aware)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Check
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun DiscoverCard(
    photoUrl: String,
    names: List<String>,           // ["Robin", "Sky"]
    ages: List<Int>,
    distanceKm: Int,
    kind: String,                  // "Couple" / "Single" / "Group"
    desires: List<DesireChipModel>,
    verified: Boolean,
    modifier: Modifier = Modifier,
) {
    Box(
        modifier
            .fillMaxSize()
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(28.dp)),    // signature 28dp card
    ) {
        AsyncImage(
            model = photoUrl, contentDescription = null,
            modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop,
        )
        Box(
            Modifier
                .align(Alignment.BottomCenter)
                .fillMaxWidth().fillMaxHeight(0.56f)
                .background(Brush.verticalGradient(
                    0f to Color.Transparent,
                    0.6f to Color.Black.copy(alpha = 0.72f),
                    1f to Color.Black.copy(alpha = 0.92f),
                )),
        )
        if (verified) {
            Row(
                Modifier
                    .align(Alignment.TopEnd).padding(18.dp)
                    .clip(RoundedCornerShape(999.dp))
                    .background(Color.Black.copy(alpha = 0.5f))   // Android: no live blur — solid scrim
                    .padding(horizontal = 10.dp, vertical = 6.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(5.dp),
            ) {
                Icon(Icons.Filled.Check, null, tint = FeeldColors.Acid, modifier = Modifier.size(12.dp))
                Text("Verified", style = FeeldText.Caption.copy(fontWeight = FontWeight.SemiBold), color = FeeldColors.TextPrimary)
            }
        }
        Column(
            Modifier
                .align(Alignment.BottomStart)
                .padding(horizontal = 22.dp).padding(bottom = 18.dp),
            verticalArrangement = Arrangement.spacedBy(4.dp),
        ) {
            Text(
                names.joinToString(" & "),
                style = FeeldText.Name, color = Color.White,
                maxLines = 2, overflow = TextOverflow.Ellipsis,    // never truncate 2nd name
            )
            Text(
                "$kind · ${ages.joinToString(" & ")} · $distanceKm km away",
                style = FeeldText.Meta, color = Color.White.copy(alpha = 0.78f),
            )
            DesireChipRow(desires, modifier = Modifier.padding(top = 8.dp))
        }
    }
}
```

### Action Row (Pass · Like · Wink)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.Close
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.Star
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import kotlinx.coroutines.launch

@Composable
fun ActionRow(onPass: () -> Unit, onLike: () -> Unit, onWink: () -> Unit) {
    val scope = rememberCoroutineScope()
    val likeScale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current

    Row(
        Modifier.fillMaxWidth().padding(vertical = 8.dp),
        horizontalArrangement = Arrangement.spacedBy(22.dp, Alignment.CenterHorizontally),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        CircleAction(52.dp, FeeldColors.Surface2, true) {
            Icon(Icons.Filled.Close, "Pass", tint = FeeldColors.TextSecondary, modifier = Modifier.size(22.dp))
        }.also { /* attach onPass via clickable below */ }

        Box(
            Modifier
                .size(64.dp).scale(likeScale.value)
                .shadow(15.dp, CircleShape, spotColor = FeeldColors.Acid.copy(alpha = 0.5f))
                .clip(CircleShape).background(FeeldColors.Acid)
                .clickable {
                    scope.launch { likeScale.snapTo(0.92f); likeScale.animateTo(1f, tween(180)) }
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                    onLike()
                },
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Favorite, "Like", tint = FeeldColors.OnAcid, modifier = Modifier.size(28.dp)) }

        Box(
            Modifier.size(52.dp).clip(CircleShape).background(FeeldColors.Surface2)
                .border(1.dp, FeeldColors.Divider, CircleShape).clickable(onClick = onWink),
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Star, "Send a Wink", tint = FeeldColors.Lilac, modifier = Modifier.size(22.dp)) }
    }
}

@Composable
private fun CircleAction(size: Dp, bg: Color, bordered: Boolean, content: @Composable () -> Unit): Unit =
    Box(
        Modifier.size(size).clip(CircleShape).background(bg)
            .then(if (bordered) Modifier.border(1.dp, FeeldColors.Divider, CircleShape) else Modifier),
        contentAlignment = Alignment.Center,
    ) { content() }
```

### Desire Chip + Row

```kotlin
data class DesireChipModel(val label: String, val state: DesireState)
enum class DesireState { Selected, Unselected, Emotional }

@Composable
fun DesireChip(model: DesireChipModel) {
    val (bg, fg, border) = when (model.state) {
        DesireState.Selected   -> Triple(FeeldColors.Acid, FeeldColors.OnAcid, Color.Transparent)
        DesireState.Unselected -> Triple(FeeldColors.Surface2, FeeldColors.TextPrimary, FeeldColors.Divider)
        DesireState.Emotional  -> Triple(FeeldColors.Pink.copy(alpha = 0.16f), FeeldColors.Pink, FeeldColors.Pink.copy(alpha = 0.4f))
    }
    Box(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(bg)
            .border(1.dp, border, RoundedCornerShape(999.dp))
            .padding(horizontal = 15.dp, vertical = 9.dp),
    ) { Text(model.label, style = FeeldText.Chip, color = fg) }
}

@Composable
fun DesireChipRow(desires: List<DesireChipModel>, modifier: Modifier = Modifier) {
    FlowRow(modifier, horizontalArrangement = Arrangement.spacedBy(7.dp), verticalArrangement = Arrangement.spacedBy(7.dp)) {
        desires.forEach { DesireChip(it) }   // wraps, never truncates
    }
}
```

### Connections Row

```kotlin
@Composable
fun ConnectionRow(
    avatar: Brush, name: String, snippet: String, time: String, unread: Boolean,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .padding(vertical = 12.dp)
            .drawBehind {
                drawLine(FeeldColors.Divider, Offset(0f, size.height), Offset(size.width, size.height), 1f)
            },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Box(Modifier.size(48.dp).clip(CircleShape).background(avatar))
        Column(Modifier.weight(1f)) {
            Text(name, style = FeeldText.ListTitle, color = FeeldColors.TextPrimary)
            Text(snippet, style = FeeldText.Snippet, color = FeeldColors.TextSecondary, maxLines = 1, overflow = TextOverflow.Ellipsis)
        }
        Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(6.dp)) {
            Text(time, style = FeeldText.Caption, color = FeeldColors.TextTertiary)
            if (unread) Box(Modifier.size(6.dp).clip(CircleShape).background(FeeldColors.Acid))
        }
    }
}
```

## 4. Navigation

Feeld has minimal chrome: a transparent top bar and a 4-tab bottom strip with **no tint pill** — active is the acid icon + a small dot.

```kotlin
@Composable
fun FeeldBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = Color.Black.copy(alpha = 0.92f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            Icons.Filled.Search       to "Discover",
            Icons.Filled.FavoriteBorder to "Likes",
            Icons.Filled.ChatBubbleOutline to "Connections",
            Icons.Filled.PersonOutline to "Profile",
        )
        items.forEachIndexed { i, (icon, label) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(23.dp)) },
                label = null,                                  // icons-only is valid Feeld
                alwaysShowLabel = false,
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = FeeldColors.Acid,      // acid icon
                    unselectedIconColor = FeeldColors.TextTertiary,
                    indicatorColor = Color.Transparent,        // NO Material pill — Feeld has none
                ),
            )
        }
    }
}
```

A 5dp acid dot under the active icon (draw a small `Box` in the item, or overlay) is the only active-state signal beyond the acid tint. The top bar is a transparent `Row`: leading filters icon (17dp in a 34dp `Surface2` circle), center title in `FeeldText.Section`, trailing settings icon.

## 5. Motion

Feeld motion is confident and physical — card swipe is the hero gesture; the acid Like alone pulses.

| Moment | Compose recipe |
|--------|----------------|
| Card swipe (like/pass) | `Modifier.pointerInput` drag → `offsetX` `Animatable`; `rotationZ = offsetX / cardWidth * 8`; release > 35% width → `animateTo(±800, spring(0.8))`, else `animateTo(0)` |
| Like tap | `likeScale` `snapTo(0.92f)` then `animateTo(1f, tween(180))` + long-press haptic |
| Wink bounce | `winkScale.animateTo(1.25f, tween(130))` then `animateTo(1f, tween(130))` |
| Connection made | full-screen overlay; two avatars `animateOffset` from edges to center `tween(320)` + acid particle burst |
| Chip toggle | instant color swap + `animateFloatAsState` scale 0.96 → 1.0 `tween(120)`; selection haptic |
| Sheet present | `ModalBottomSheet` slide-up (Material default) + scrim |
| Tab switch | active acid dot `animateDpAsState` slides under new icon `tween(200)` |

```kotlin
// Card swipe — the canonical Feeld gesture
val offsetX = remember { Animatable(0f) }
Modifier.pointerInput(Unit) {
    detectDragGestures(
        onDragEnd = {
            scope.launch {
                if (kotlin.math.abs(offsetX.value) > cardWidthPx * 0.35f) {
                    offsetX.animateTo(if (offsetX.value > 0) 800f else -800f, spring(dampingRatio = 0.8f))
                    onSwiped(offsetX.value > 0)
                } else offsetX.animateTo(0f, spring(dampingRatio = 0.8f))
            }
        },
        onDrag = { _, drag -> scope.launch { offsetX.snapTo(offsetX.value + drag.x) } },
    )
}.graphicsLayer { translationX = offsetX.value; rotationZ = offsetX.value / cardWidthPx * 8f }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on Like commit; a lighter `HapticFeedbackConstants.CLOCK_TICK` (via `LocalView`) for chip toggle and Wink; the OS confirm pattern for a new Connection.

## 6. Icons

Feeld iconography is simple line/solid; `androidx.compose.material:material-icons-extended` covers it.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Discover (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Likes (tab) | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Favorite` |
| Connections (tab) | `bubble.left.and.bubble.right` | `Icons.Filled.ChatBubbleOutline` |
| Profile (tab) | `person` / `person.fill` | `Icons.Filled.PersonOutline` |
| Pass | `xmark` | `Icons.Filled.Close` |
| Like | `heart.fill` | `Icons.Filled.Favorite` |
| Wink | `star.fill` | `Icons.Filled.Star` |
| Verified | `checkmark` | `Icons.Filled.Check` |
| Filters | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Send message | `arrow.up.circle.fill` | `Icons.Filled.ArrowCircleUp` |
| Block / report | `exclamationmark.triangle` | `Icons.Filled.ReportGmailerrorred` |
| Add photo | `plus` | `Icons.Filled.Add` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; the drag deck + `FlowRow` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`. `FlowRow` is in `androidx.compose.foundation` (stable from BOM 2024.06).
- **Edge-to-edge**: call `enableEdgeToEdge()`; the canvas is true black so system bars should be transparent with light icons. The Discover card sits below the camera cutout; the action row + bottom bar respect the nav inset.
- **Dark-only**: never call `isSystemInDarkTheme()` to pick a scheme and never enable `dynamicColorScheme()` — Feeld's black + acid identity must hold regardless of system theme or wallpaper (there is no light Feeld and no brand accent to harmonize with Material You).
- **Couple/group names**: set `maxLines = 2` + `overflow = Ellipsis` and consider `Modifier.basicMarquee()` only if a two-name couple still overflows — never silently drop the second name.
- **Font scaling**: `sp` honors the user's font scale — keep it on display, name, section, body, snippet. Pin layout-sensitive text (Desire chips, 10sp tab labels, verified-pill text) via a fixed `Density(fontScale = 1f)` `CompositionLocalProvider` so pills don't reflow.
- **TalkBack**: give the Discover card `Modifier.semantics { contentDescription = "Profile: $names, $kind, ${ages} years, $distanceKm km away, ${desires.size} desires" }`; expose Like/Pass/Wink via `customActions` so the card is operable without the swipe gesture; label the acid dot's tab as selected.
- **Touch targets**: Material guidance is 48dp — the 52/64dp action buttons and 48dp avatars clear it; give Desire chips ≥ 48dp hit height via padding even though the visible pill is shorter; top-bar icons get a 48dp hit area.
- **Contrast**: `#FFFFFF` on `#000000` is maximal; `#000000` on `#E8FF63` passes AA for the acid Like; validate `#FF5C8A` on `#000000` for small text (passes for large/semibold — bump weight for body).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable card rotation and the Connection avatar slide — substitute a `Crossfade`; keep the chip color swap (it conveys state).
- **The acid glow is decorative** — the `Favorite` icon must carry the "this is Like" meaning for users who can't perceive the shadow halo.
