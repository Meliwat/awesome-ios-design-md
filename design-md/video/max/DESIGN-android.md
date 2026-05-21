# Max (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Max's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, the brand `Brush`, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Max's deep-purple branded canvas, blue→violet gradient, auto-trailer billboard, Originals badge) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush.linearGradient` instead of CAGradientLayer, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/MaxColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.geometry.Offset

object MaxColors {
    // Canvas & Surfaces
    val Canvas    = Color(0xFF12053A)
    val DeepBlack = Color(0xFF000000)
    val Surface1  = Color(0xFF1B0B4D)
    val Surface2  = Color(0xFF250F5E)
    val Surface3  = Color(0xFF36206E)
    val Divider   = Color(0xFF36206E)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFB0A8D0)
    val TextTertiary  = Color(0xFF7E76A6)

    // Brand
    val GradientStart   = Color(0xFF0046FF)
    val GradientEnd     = Color(0xFF7B2FF7)
    val GradientSolid   = Color(0xFF5A2BE0)  // tab indicator / small toggles
    val GradientPressed = Color(0xFF4A22BD)
    val LiveGold        = Color(0xFFF2C94C)
    val ErrorRed        = Color(0xFFFF5C7A)

    // The Max brand gradient (135°)
    val Brand = Brush.linearGradient(
        colors = listOf(GradientStart, GradientEnd),
        start = Offset(0f, 0f),
        end = Offset(Float.POSITIVE_INFINITY, Float.POSITIVE_INFINITY),
    )
}
```

Wire it into a Material 3 `darkColorScheme` so ripples, dividers, and default components inherit the brand. Max is effectively dark-only; do not provide a light scheme. (Material slots take a single color, so `primary` uses the solid fallback; the gradient is applied explicitly on brand surfaces.)

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val MaxScheme = darkColorScheme(
    primary        = MaxColors.GradientSolid,  // gradient applied explicitly where it matters
    onPrimary      = MaxColors.TextPrimary,    // white on the blue-violet brand
    background     = MaxColors.Canvas,
    onBackground   = MaxColors.TextPrimary,
    surface        = MaxColors.Surface1,
    onSurface      = MaxColors.TextPrimary,
    surfaceVariant = MaxColors.Surface2,
    outline        = MaxColors.Divider,
    error          = MaxColors.ErrorRed,
)

@Composable
fun MaxTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = MaxScheme, typography = MaxTypography, content = content)
```

## 2. Typography

Inter stands in for Max's clean grotesque brand face. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto).

```kotlin
// ui/theme/MaxType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),    // 400
    Font(R.font.inter_semibold,  FontWeight.SemiBold),  // 600
    Font(R.font.inter_extrabold, FontWeight.ExtraBold), // 800
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object MaxText {
    val BillboardTitle  = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.5).sp)
    val TitleLarge      = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val RowHeader       = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val SectionHeader   = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.3).sp)
    val EpisodeTitle    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val TileTitle       = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val Body            = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 22.sp)
    val Meta            = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 17.sp)
    val TileSubtitle    = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 12.sp, lineHeight = 16.sp)
    val LabelUpper      = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Button          = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = 0.2.sp)
    val ButtonSecondary = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 19.sp)
    val Tab             = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val Badge           = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val MaxTypography = Typography(
    headlineLarge = MaxText.TitleLarge,
    headlineSmall = MaxText.RowHeader,
    titleMedium   = MaxText.TileTitle,
    bodyMedium    = MaxText.Body,
    labelSmall    = MaxText.Tab,
)
```

## 3. Signature Components

### Primary Gradient Play Button (with shimmer)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.*
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

@Composable
fun MaxPlayButton(
    title: String = "Play",
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        targetValue = if (pressed) 0.97f else 1f,
        animationSpec = spring(dampingRatio = 0.7f, stiffness = 600f),
        label = "playScale",
    )
    val haptics = LocalHapticFeedback.current

    val shimmer = rememberInfiniteTransition(label = "shimmer")
    val x by shimmer.animateFloat(
        -1f, 1f, infiniteRepeatable(tween(4000, easing = LinearEasing)), label = "x",
    )

    Box(
        modifier = modifier
            .fillMaxWidth()
            .height(52.dp)
            .scale(scale)
            .shadow(24.dp, RoundedCornerShape(8.dp), spotColor = MaxColors.GradientSolid.copy(alpha = 0.35f))
            .clip(RoundedCornerShape(8.dp))
            .background(MaxColors.Brand)
            .drawWithContent {
                drawContent()
                val w = size.width
                drawRect(
                    brush = Brush.horizontalGradient(
                        listOf(Color.Transparent, Color.White.copy(alpha = 0.18f), Color.Transparent),
                        startX = (x * w),
                        endX = (x * w) + w * 0.35f,
                    )
                )
            }
            .then(if (pressed) Modifier.background(MaxColors.GradientPressed.copy(alpha = 0.55f)) else Modifier)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS light impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Row(horizontalArrangement = Arrangement.spacedBy(8.dp), verticalAlignment = Alignment.CenterVertically) {
            Icon(Icons.Filled.PlayArrow, contentDescription = null, tint = Color.White, modifier = Modifier.size(18.dp))
            Text(title, style = MaxText.Button, color = Color.White)
        }
    }
}
```

### Secondary Button (More Info)

```kotlin
@Composable
fun MaxSecondaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "secScale")

    Box(
        modifier = modifier
            .fillMaxWidth()
            .height(52.dp)
            .scale(scale)
            .clip(RoundedCornerShape(8.dp))
            .background(Color.White.copy(alpha = if (pressed) 0.22f else 0.14f))
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = MaxText.ButtonSecondary, color = MaxColors.TextPrimary)
    }
}
```

### Max Originals Badge

```kotlin
@Composable
fun MaxOriginalsBadge(modifier: Modifier = Modifier) {
    Box(
        modifier
            .clip(RoundedCornerShape(50))
            .background(MaxColors.Brand)        // gradient "border"
            .padding(1.5.dp)
            .clip(RoundedCornerShape(50))
            .background(MaxColors.Canvas.copy(alpha = 0.55f))
            .padding(horizontal = 8.dp, vertical = 4.dp),
    ) {
        Text("Max Originals", style = MaxText.Badge, color = Color.White)
    }
}
```

### Content Tile (with Originals badge)

```kotlin
@Composable
fun MaxContentTile(
    title: String,
    subtitle: String,
    artworkUrl: String,
    isOriginal: Boolean,
    width: Dp,
    aspect: Float = 16f / 9f,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 1.04f else 1f, label = "tileScale")

    Column(
        modifier = modifier
            .width(width)
            .scale(scale)
            .clickable(interaction, indication = null, onClick = onClick),
    ) {
        Box(
            Modifier
                .width(width)
                .height(width / aspect)
                .clip(RoundedCornerShape(6.dp)),
        ) {
            AsyncImage(
                model = artworkUrl,
                contentDescription = title,
                modifier = Modifier.fillMaxSize(),
                contentScale = ContentScale.Crop,
            )
            if (isOriginal) {
                MaxOriginalsBadge(Modifier.align(Alignment.TopStart).padding(8.dp))
            }
        }
        Spacer(Modifier.height(6.dp))
        Text(title, style = MaxText.TileTitle, color = MaxColors.TextPrimary, maxLines = 1, overflow = TextOverflow.Ellipsis)
        Text(subtitle, style = MaxText.TileSubtitle, color = MaxColors.TextSecondary, maxLines = 1, overflow = TextOverflow.Ellipsis)
    }
}
```

### Billboard (auto-trailer hero with crossfade)

```kotlin
@Composable
fun MaxBillboard(items: List<BillboardItem>) {
    var index by remember { mutableIntStateOf(0) }
    LaunchedEffect(items) {
        while (true) {
            delay(20_000)
            index = (index + 1) % items.size
        }
    }

    Box(Modifier.fillMaxWidth().height(460.dp).background(MaxColors.Canvas)) {
        Crossfade(targetState = index, animationSpec = tween(1200), label = "billboard") { i ->
            AsyncImage(
                model = items[i].posterUrl,
                contentDescription = items[i].title,
                modifier = Modifier.fillMaxSize(),
                contentScale = ContentScale.Crop,
            )
        }
        Box(
            Modifier
                .fillMaxSize()
                .background(Brush.verticalGradient(0.4f to Color.Transparent, 1f to MaxColors.Canvas))
        )
        Column(
            Modifier.align(Alignment.BottomStart).padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            MaxOriginalsBadge()
            Text(items[index].title, style = MaxText.BillboardTitle, color = Color.White)
            Text(items[index].meta, style = MaxText.Meta, color = MaxColors.TextSecondary)
            Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
                MaxPlayButton(onClick = {}, modifier = Modifier.weight(1f))
                MaxSecondaryButton("More Info", onClick = {}, modifier = Modifier.weight(1f))
            }
        }
    }
}
```

## 4. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Max's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 94%-opaque purple surface. **Active tint is white with a solid `#5A2BE0` underline** — the full gradient can't render reliably at indicator size, so the solid fallback is used.

```kotlin
@Composable
fun MaxBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = MaxColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"    to Icons.Filled.Home,
            "Search"  to Icons.Filled.Search,
            "My List" to Icons.Filled.VideoLibrary,
        )
        items.forEachIndexed { i, (label, icon) ->
            val isSel = selected == i
            NavigationBarItem(
                selected = isSel,
                onClick = { onSelect(i) },
                icon = {
                    Column(horizontalAlignment = Alignment.CenterHorizontally) {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp))
                        if (isSel) {
                            Spacer(Modifier.height(3.dp))
                            Box(Modifier.width(18.dp).height(3.dp)
                                .clip(RoundedCornerShape(2.dp)).background(MaxColors.GradientSolid))
                        }
                    }
                },
                label = { Text(label, style = MaxText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = MaxColors.TextPrimary,
                    selectedTextColor   = MaxColors.TextPrimary,
                    unselectedIconColor = MaxColors.TextSecondary,
                    unselectedTextColor = MaxColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // custom underline instead of Material pill
                ),
            )
        }
    }
}
```

## 5. Profile Gate

```kotlin
@Composable
fun MaxProfileGate(profiles: List<Profile>, onPick: (Profile) -> Unit) {
    Column(
        Modifier.fillMaxSize().background(MaxColors.Canvas).padding(24.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center,
    ) {
        Text("Who's watching?", style = MaxText.TitleLarge, color = Color.White)
        Spacer(Modifier.height(32.dp))
        LazyVerticalGrid(columns = GridCells.Fixed(2), horizontalArrangement = Arrangement.spacedBy(32.dp),
                         verticalArrangement = Arrangement.spacedBy(32.dp)) {
            items(profiles) { p ->
                val interaction = remember { MutableInteractionSource() }
                val pressed by interaction.collectIsPressedAsState()
                val scale by animateFloatAsState(if (pressed) 0.95f else 1f, label = "avatar")
                Column(horizontalAlignment = Alignment.CenterHorizontally) {
                    AsyncImage(
                        model = p.avatarUrl,
                        contentDescription = p.name,
                        modifier = Modifier
                            .size(96.dp)
                            .scale(scale)
                            .clip(CircleShape)
                            .then(if (pressed) Modifier.border(2.dp, MaxColors.Brand, CircleShape) else Modifier)
                            .clickable(interaction, indication = null) { onPick(p) },
                        contentScale = ContentScale.Crop,
                    )
                    Spacer(Modifier.height(8.dp))
                    Text(p.name, style = MaxText.TileTitle, color = Color.White)
                }
            }
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Tile press scale-up | `animateFloatAsState` 1 → 1.04, `tween(180)` ease-out + focus shadow + gradient ring |
| Play CTA tap | `animateFloatAsState` 1 → 0.97 with `spring(dampingRatio = 0.7f)`, `HapticFeedbackType.LongPress` |
| Billboard crossfade | `Crossfade(targetState = index, tween(1200))`, index advanced every 20s via `LaunchedEffect` |
| CTA gradient shimmer | `rememberInfiniteTransition` driving a translucent band via `drawWithContent`, `tween(4000, LinearEasing)` |
| Add to My List | `Animatable` 1 → 1.15 → 1 over 250ms; `HapticFeedbackType.LongPress` |

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(8, ...)` to approximate iOS's `.light` impact.

## 7. Icons

Max ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Max's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Skip Forward 10 | `goforward.10` | `Icons.Filled.Forward10` |
| Skip Back 10 | `gobackward.10` | `Icons.Filled.Replay10` |
| Add to My List | `plus` / `checkmark` | `Icons.Filled.Add` / `Icons.Filled.Check` |
| Download | `arrow.down.to.line` | `Icons.Filled.FileDownload` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| More Info | `info.circle` | `Icons.Filled.Info` |
| Captions | `captions.bubble` | `Icons.Filled.ClosedCaption` |
| AirPlay | `airplayvideo` | `Icons.Filled.Cast` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| My List (tab) | `checkmark.rectangle.stack.fill` | `Icons.Filled.VideoLibrary` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Crossfade` + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the deep-purple canvas wants `WindowCompat` light-content system bars. Apply `Scaffold` insets so the tab bar clears gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on show titles, episode titles, synopsis. Pin layout-sensitive text (tab labels, scrubber timestamps, tile subtitles) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: set `contentDescription` on tiles ("Play Dune: Prophecy, Max Originals"); merge tile title+subtitle with `Modifier.semantics(mergeDescendants = true)` and fold the Originals badge into the label.
- **Touch targets**: Material guidance is 48.dp minimum. The 52.dp Play CTA, 96.dp profile avatars, and 96.dp episode rows clear it; pad small icon actions to a 48.dp hit area.
- **Contrast**: `#B0A8D0` on `#12053A` passes WCAG AA at 13sp+. White on the blue→violet gradient passes AA across the sweep — if you tune the gradient lighter, re-check the violet end. Validate 11sp badge labels and bump toward `#C4BDDD` for strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Max's brand requires the fixed `#12053A` purple canvas and the blue→violet gradient regardless of wallpaper.
