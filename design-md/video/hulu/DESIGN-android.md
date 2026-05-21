# Hulu (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Hulu's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Hulu's near-black canvas, single electric-green accent, 16:9 dense rails, green progress bars) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `sp`/`dp` instead of `pt`, Compose springs instead of UIKit haptics.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/HuluColors.kt
import androidx.compose.ui.graphics.Color

object HuluColors {
    // Canvas & Surfaces
    val Canvas    = Color(0xFF0B0C0F)
    val DeepBlack = Color(0xFF000000)
    val Surface1  = Color(0xFF151619)
    val Surface2  = Color(0xFF1E2024)
    val Surface3  = Color(0xFF2A2D33)
    val Divider   = Color(0xFF2A2D33)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFA0A4AB)
    val TextTertiary  = Color(0xFF6B6F77)

    // Brand
    val Green        = Color(0xFF1CE783)
    val GreenPressed = Color(0xFF15B869)
    val LiveRed      = Color(0xFFF0476A)
    val ErrorRed     = Color(0xFFF0476A)
}
```

Wire it into a Material 3 `darkColorScheme` so ripples, dividers, and default component colors inherit the brand. Hulu is effectively dark-only; do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val HuluScheme = darkColorScheme(
    primary        = HuluColors.Green,
    onPrimary      = HuluColors.Canvas,      // intentional: near-black on bright green
    background     = HuluColors.Canvas,
    onBackground   = HuluColors.TextPrimary,
    surface        = HuluColors.Surface1,
    onSurface      = HuluColors.TextPrimary,
    surfaceVariant = HuluColors.Surface2,
    outline        = HuluColors.Divider,
    error          = HuluColors.ErrorRed,
)

@Composable
fun HuluTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = HuluScheme, typography = HuluTypography, content = content)
```

## 2. Typography

Graphik is a licensed brand typeface. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto) — its grotesque tone is the closest free substitute on Android.

```kotlin
// ui/theme/HuluType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Graphik = FontFamily(
    Font(R.font.graphik_regular,  FontWeight.Normal),   // 400
    Font(R.font.graphik_semibold, FontWeight.SemiBold), // 600
    Font(R.font.graphik_bold,     FontWeight.ExtraBold),// 800
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object HuluText {
    val HeroTitle       = TextStyle(Graphik, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.5).sp)
    val TitleLarge      = TextStyle(Graphik, fontWeight = FontWeight.ExtraBold, fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val RailHeader      = TextStyle(Graphik, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val SectionHeader   = TextStyle(Graphik, fontWeight = FontWeight.ExtraBold, fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.3).sp)
    val EpisodeTitle    = TextStyle(Graphik, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val TileTitle       = TextStyle(Graphik, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val Body            = TextStyle(Graphik, fontWeight = FontWeight.Normal,    fontSize = 15.sp, lineHeight = 22.sp)
    val Meta            = TextStyle(Graphik, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 17.sp)
    val TileSubtitle    = TextStyle(Graphik, fontWeight = FontWeight.Normal,    fontSize = 12.sp, lineHeight = 16.sp)
    val LabelUpper      = TextStyle(Graphik, fontWeight = FontWeight.ExtraBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Button          = TextStyle(Graphik, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = 0.2.sp)
    val ButtonSecondary = TextStyle(Graphik, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 19.sp)
    val Tab             = TextStyle(Graphik, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val Badge           = TextStyle(Graphik, fontWeight = FontWeight.ExtraBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val HuluTypography = Typography(
    headlineLarge = HuluText.TitleLarge,
    headlineSmall = HuluText.RailHeader,
    titleMedium   = HuluText.TileTitle,
    bodyMedium    = HuluText.Body,
    labelSmall    = HuluText.Tab,
)
```

## 3. Signature Components

### Primary Green Watch Button

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
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
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun HuluWatchButton(
    title: String = "Watch",
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        targetValue = if (pressed) 0.97f else 1f,
        animationSpec = spring(dampingRatio = 0.7f, stiffness = 600f),
        label = "watchScale",
    )
    val haptics = LocalHapticFeedback.current

    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(52.dp)
            .scale(scale)
            .shadow(24.dp, RoundedCornerShape(8.dp), spotColor = HuluColors.Green.copy(alpha = 0.30f))
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) HuluColors.GreenPressed else HuluColors.Green)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS light impact
                onClick()
            },
        horizontalArrangement = Arrangement.spacedBy(8.dp, Alignment.CenterHorizontally),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(Icons.Filled.PlayArrow, contentDescription = null,
            tint = HuluColors.Canvas, modifier = Modifier.size(18.dp))
        Text(title, style = HuluText.Button, color = HuluColors.Canvas) // near-black, not white
    }
}
```

### Secondary Button (Details / Trailer)

```kotlin
@Composable
fun HuluSecondaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "secScale")

    Box(
        modifier = modifier
            .fillMaxWidth()
            .height(52.dp)
            .scale(scale)
            .clip(RoundedCornerShape(8.dp))
            .background(Color.White.copy(alpha = if (pressed) 0.20f else 0.12f))
            .clickable(interaction, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = HuluText.ButtonSecondary, color = HuluColors.TextPrimary)
    }
}
```

### 16:9 Content Tile (with progress bar)

```kotlin
@Composable
fun HuluContentTile(
    title: String,
    subtitle: String,
    artworkUrl: String,
    progress: Float?,           // 0f..1f or null
    width: Dp,
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
                .height(width * 9 / 16)
                .clip(RoundedCornerShape(6.dp)),
        ) {
            AsyncImage(
                model = artworkUrl,
                contentDescription = title,
                modifier = Modifier.fillMaxSize(),
                contentScale = ContentScale.Crop,
            )
            if (progress != null) {
                Box(
                    Modifier
                        .align(Alignment.BottomStart)
                        .padding(2.dp)
                        .fillMaxWidth()
                        .height(4.dp)
                        .clip(RoundedCornerShape(2.dp))
                        .background(Color.White.copy(alpha = 0.25f)),
                ) {
                    Box(
                        Modifier
                            .fillMaxWidth(progress)
                            .fillMaxHeight()
                            .background(HuluColors.Green),
                    )
                }
            }
        }
        Spacer(Modifier.height(6.dp))
        Text(title, style = HuluText.TileTitle, color = HuluColors.TextPrimary, maxLines = 1, overflow = TextOverflow.Ellipsis)
        Text(subtitle, style = HuluText.TileSubtitle, color = HuluColors.TextSecondary, maxLines = 1, overflow = TextOverflow.Ellipsis)
    }
}
```

### Details Hero (darkened backdrop + green CTA)

```kotlin
@Composable
fun HuluDetailsHero(
    title: String,
    metadata: String,
    backdropUrl: String,
    onWatch: () -> Unit,
) {
    Column(Modifier.fillMaxWidth().background(HuluColors.Canvas)) {
        Box(Modifier.fillMaxWidth().height(280.dp)) {
            AsyncImage(
                model = backdropUrl,
                contentDescription = "$title backdrop",
                modifier = Modifier.fillMaxSize(),
                contentScale = ContentScale.Crop,
            )
            Box(
                Modifier
                    .fillMaxSize()
                    .background(
                        Brush.verticalGradient(
                            0.4f to Color.Transparent,
                            1f to HuluColors.Canvas,
                        )
                    )
            )
            Column(Modifier.align(Alignment.BottomStart).padding(16.dp)) {
                Text(title, style = HuluText.HeroTitle, color = HuluColors.TextPrimary)
                Spacer(Modifier.height(6.dp))
                Text(metadata, style = HuluText.Meta, color = HuluColors.TextSecondary)
            }
        }
        Column(Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(16.dp)) {
            HuluWatchButton(onClick = onWatch)
            Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceAround) {
                HuluIconAction(Icons.Filled.Add, "My Stuff")
                HuluIconAction(Icons.Filled.FileDownload, "Download")
                HuluIconAction(Icons.Filled.IosShare, "Share")
            }
        }
    }
}

@Composable
fun HuluIconAction(icon: ImageVector, label: String, active: Boolean = false) {
    Column(
        Modifier.widthIn(min = 44.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(icon, contentDescription = label,
            tint = if (active) HuluColors.Green else HuluColors.TextPrimary,
            modifier = Modifier.size(22.dp))
        Text(label, style = HuluText.TileSubtitle, color = HuluColors.TextSecondary)
    }
}
```

## 4. Live Badge

```kotlin
@Composable
fun HuluLiveBadge() {
    val t = rememberInfiniteTransition(label = "live")
    val alpha by t.animateFloat(
        1f, 0.4f,
        infiniteRepeatable(tween(700), RepeatMode.Reverse),
        label = "dot",
    )
    Row(
        Modifier
            .clip(RoundedCornerShape(50))
            .background(HuluColors.LiveRed)
            .padding(horizontal = 8.dp, vertical = 4.dp),
        horizontalArrangement = Arrangement.spacedBy(5.dp),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Box(Modifier.size(6.dp).alpha(alpha).clip(CircleShape).background(Color.White))
        Text("LIVE", style = HuluText.Badge, color = Color.White)
    }
}
```

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Hulu's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 94%-opaque canvas surface. **Active tint is Hulu Green** — unlike many streamers, green IS the tab indicator here.

```kotlin
@Composable
fun HuluBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = HuluColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Hubs"     to Icons.Filled.GridView,
            "My Stuff" to Icons.Filled.Bookmark,
            "Search"   to Icons.Filled.Search,
            "Account"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = HuluText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = HuluColors.Green,   // green IS the indicator
                    selectedTextColor   = HuluColors.Green,
                    unselectedIconColor = HuluColors.TextSecondary,
                    unselectedTextColor = HuluColors.TextSecondary,
                    indicatorColor      = Color.Transparent,   // no Material pill — Hulu has none
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Tile press scale-up | `animateFloatAsState` 1 → 1.04, `tween(180)` ease-out + focus shadow + green ring |
| Watch CTA tap | `animateFloatAsState` 1 → 0.97 with `spring(dampingRatio = 0.7f)`, `HapticFeedbackType.LongPress` |
| Add to My Stuff | `Animatable` 1 → 1.15 → 1 over 250ms; `HapticFeedbackType.LongPress` |
| LIVE dot pulse | `rememberInfiniteTransition` alpha 1 → 0.4, `tween(700)` `RepeatMode.Reverse` |
| Details enter | `AnimatedVisibility` + `slideInVertically { it / 8 }` + `fadeIn(tween(250))` on rails |

```kotlin
// Add-to-My-Stuff bounce
@Composable
fun MyStuffToggle(inList: Boolean, onToggle: () -> Unit) {
    val scale = remember { Animatable(1f) }
    LaunchedEffect(inList) {
        scale.animateTo(1.15f, tween(120)); scale.animateTo(1f, tween(130))
    }
    Icon(
        imageVector = if (inList) Icons.Filled.Check else Icons.Filled.Add,
        contentDescription = if (inList) "In My Stuff" else "Add to My Stuff",
        tint = if (inList) HuluColors.Green else HuluColors.TextPrimary,
        modifier = Modifier.size(22.dp).scale(scale.value)
            .clickable(remember { MutableInteractionSource() }, indication = null, onClick = onToggle),
    )
}
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(8, ...)` to approximate iOS's `.light` impact.

## 7. Icons

Hulu ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Hulu's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Play / Watch | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Skip Forward 10 | `goforward.10` | `Icons.Filled.Forward10` |
| Skip Back 10 | `gobackward.10` | `Icons.Filled.Replay10` |
| Add to My Stuff | `plus` / `checkmark` | `Icons.Filled.Add` / `Icons.Filled.Check` |
| Download | `arrow.down.to.line` | `Icons.Filled.FileDownload` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Captions | `captions.bubble` | `Icons.Filled.ClosedCaption` |
| AirPlay | `airplayvideo` | `Icons.Filled.Cast` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Hubs (tab) | `square.grid.2x2.fill` | `Icons.Filled.GridView` |
| My Stuff (tab) | `bookmark.fill` | `Icons.Filled.Bookmark` |
| Account (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the near-black canvas wants `WindowCompat` light-content system bars. Apply `Scaffold` insets so the tab bar clears gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on show titles, episode titles, synopsis. Pin layout-sensitive text (tab labels, scrubber timestamps, tile subtitles in dense rails) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: set `contentDescription` on tiles ("Watch The Bear, Season 3 Episode 4, 24 minutes left"); merge tile title+subtitle with `Modifier.semantics(mergeDescendants = true)` and expose progress via `stateDescription`.
- **Touch targets**: Material guidance is 48.dp minimum. The 52.dp Watch CTA and 96.dp episode rows clear it; ensure 24.dp icon actions have `Modifier.size(48.dp)` hit area via padding.
- **Contrast**: `#A0A4AB` on `#0B0C0F` passes WCAG AA at 13sp+. The bright green (`#1CE783`) with near-black (`#0B0C0F`) text exceeds AAA — keep that pairing; never white-on-green. Validate 11sp badge labels and bump toward `#B6BAC0` for strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Hulu's brand requires the fixed `#0B0C0F` canvas and single electric-green accent regardless of wallpaper.
