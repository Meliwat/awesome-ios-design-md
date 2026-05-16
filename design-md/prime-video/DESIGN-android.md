# Prime Video (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Prime Video's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Prime Video's dark-navy canvas, single Prime-Blue accent, hero billboard, the X-Ray cast slide-up) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet`/translate for X-Ray, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/PrimeColors.kt
import androidx.compose.ui.graphics.Color

object PrimeColors {
    // Canvas & Surfaces
    val Canvas    = Color(0xFF0F171E)
    val DeepBlack = Color(0xFF000000)
    val Surface1  = Color(0xFF1A242F)
    val Surface2  = Color(0xFF232F3E)
    val Surface3  = Color(0xFF2E3B47)
    val Divider   = Color(0xFF2E3B47)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFAAB7C4)
    val TextTertiary  = Color(0xFF6E7B89)

    // Brand
    val Blue        = Color(0xFF00A8E1)
    val BluePressed = Color(0xFF008CBD)
    val ImdbYellow  = Color(0xFFF5C518)
    val LiveRed     = Color(0xFFE50914)
    val ErrorRed    = Color(0xFFE50914)
}
```

Wire it into a Material 3 `darkColorScheme` so ripples, dividers, and default components inherit the brand. Prime Video is effectively dark-only; do not provide a light scheme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val PrimeScheme = darkColorScheme(
    primary        = PrimeColors.Blue,
    onPrimary      = PrimeColors.Canvas,     // intentional: dark-navy on bright blue
    background     = PrimeColors.Canvas,
    onBackground   = PrimeColors.TextPrimary,
    surface        = PrimeColors.Surface1,
    onSurface      = PrimeColors.TextPrimary,
    surfaceVariant = PrimeColors.Surface2,
    outline        = PrimeColors.Divider,
    error          = PrimeColors.ErrorRed,
)

@Composable
fun PrimeTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = PrimeScheme, typography = PrimeTypography, content = content)
```

## 2. Typography

Amazon Ember is a licensed brand typeface. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto) — its open grotesque tone is the closest free substitute.

```kotlin
// ui/theme/PrimeType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val AmazonEmber = FontFamily(
    Font(R.font.amazon_ember_regular, FontWeight.Normal),   // 400
    Font(R.font.amazon_ember_medium,  FontWeight.SemiBold), // 600
    Font(R.font.amazon_ember_bold,    FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object PrimeText {
    val DetailsTitle   = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,     fontSize = 30.sp, lineHeight = 33.sp, letterSpacing = (-0.4).sp)
    val TitleLarge     = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val RowHeader      = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val SectionHeader  = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.3).sp)
    val EpisodeTitle   = TextStyle(AmazonEmber, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val TileTitle      = TextStyle(AmazonEmber, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 19.sp, letterSpacing = (-0.1).sp)
    val Body           = TextStyle(AmazonEmber, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Meta           = TextStyle(AmazonEmber, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val TileSubtitle   = TextStyle(AmazonEmber, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val LabelUpper     = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val Button         = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = 0.2.sp)
    val ButtonSecondary = TextStyle(AmazonEmber, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 19.sp)
    val Tab            = TextStyle(AmazonEmber, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val Badge          = TextStyle(AmazonEmber, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val PrimeTypography = Typography(
    headlineLarge = PrimeText.TitleLarge,
    headlineSmall = PrimeText.RowHeader,
    titleMedium   = PrimeText.TileTitle,
    bodyMedium    = PrimeText.Body,
    labelSmall    = PrimeText.Tab,
)
```

## 3. Signature Components

### Primary Blue Play Button

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
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
import androidx.compose.ui.unit.dp

@Composable
fun PrimePlayButton(
    title: String = "Play",
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(
        if (pressed) 0.97f else 1f,
        spring(dampingRatio = 0.7f, stiffness = 600f), label = "playScale",
    )
    val haptics = LocalHapticFeedback.current

    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(52.dp)
            .scale(scale)
            .shadow(24.dp, RoundedCornerShape(8.dp), spotColor = PrimeColors.Blue.copy(alpha = 0.32f))
            .clip(RoundedCornerShape(8.dp))
            .background(if (pressed) PrimeColors.BluePressed else PrimeColors.Blue)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS light impact
                onClick()
            },
        horizontalArrangement = Arrangement.spacedBy(8.dp, Alignment.CenterHorizontally),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(Icons.Filled.PlayArrow, contentDescription = null,
            tint = PrimeColors.Canvas, modifier = Modifier.size(18.dp))
        Text(title, style = PrimeText.Button, color = PrimeColors.Canvas) // dark-navy, not white
    }
}
```

### Watchlist Toggle

```kotlin
@Composable
fun PrimeWatchlistButton(added: Boolean, onToggle: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val bump = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(added) {
        if (added) { bump.animateTo(1.15f, spring()); bump.animateTo(1f, spring()) }
    }

    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(52.dp)
            .scale(bump.value)
            .clip(RoundedCornerShape(8.dp))
            .background(Color.White.copy(alpha = if (pressed) 0.22f else 0.14f))
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onToggle()
            },
        horizontalArrangement = Arrangement.spacedBy(8.dp, Alignment.CenterHorizontally),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(
            imageVector = if (added) Icons.Filled.Check else Icons.Filled.Add,
            contentDescription = null,
            tint = if (added) PrimeColors.Blue else Color.White,
            modifier = Modifier.size(16.dp),
        )
        Text(
            if (added) "Watchlisted" else "Watchlist",
            style = PrimeText.ButtonSecondary,
            color = if (added) PrimeColors.Blue else Color.White,
        )
    }
}
```

### Content Tile (2:3 poster with Prime ribbon)

```kotlin
@Composable
fun PrimeContentTile(
    title: String,
    artworkUrl: String,
    includedWithPrime: Boolean,
    progress: Float?,
    width: Dp,
    aspect: Float = 2f / 3f,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 1.04f else 1f, label = "tileScale")

    Column(
        modifier
            .width(width)
            .scale(scale)
            .clickable(interaction, indication = null, onClick = onClick),
    ) {
        Box(Modifier.width(width).height(width / aspect).clip(RoundedCornerShape(6.dp))) {
            AsyncImage(model = artworkUrl, contentDescription = title,
                modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
            if (progress != null) {
                Box(
                    Modifier
                        .align(Alignment.BottomStart)
                        .fillMaxWidth()
                        .height(3.dp)
                        .background(Color.White.copy(alpha = 0.25f)),
                ) {
                    Box(Modifier.fillMaxWidth(progress).fillMaxHeight().background(PrimeColors.Blue))
                }
            }
        }
        Spacer(Modifier.height(6.dp))
        Text(title, style = PrimeText.TileTitle, color = PrimeColors.TextPrimary,
            maxLines = 1, overflow = TextOverflow.Ellipsis)
        if (includedWithPrime) {
            Text("Included with Prime", style = PrimeText.TileSubtitle, color = PrimeColors.Blue)
        }
    }
}
```

### Hero Billboard

```kotlin
@Composable
fun PrimeBillboard(
    title: String,
    metaLeading: String,   // "2024 · 16+ · 1 Season · "
    imdb: String,          // "★ 8.4 IMDb"
    stillUrl: String,
    watchlisted: Boolean,
    onPlay: () -> Unit,
    onToggleWatchlist: () -> Unit,
) {
    Box(Modifier.fillMaxWidth().height(440.dp).background(PrimeColors.Canvas)) {
        AsyncImage(model = stillUrl, contentDescription = title,
            modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
        Box(
            Modifier
                .fillMaxSize()
                .background(Brush.verticalGradient(0.4f to Color.Transparent, 1f to PrimeColors.Canvas))
        )
        Column(
            Modifier.align(Alignment.BottomStart).padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Text(title, style = PrimeText.DetailsTitle, color = Color.White)
            Text(
                buildAnnotatedString {
                    withStyle(SpanStyle(color = PrimeColors.TextSecondary)) { append(metaLeading) }
                    withStyle(SpanStyle(color = PrimeColors.ImdbYellow)) { append(imdb) }
                },
                style = PrimeText.Meta,
            )
            Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
                PrimePlayButton(onClick = onPlay, modifier = Modifier.weight(1f))
                PrimeWatchlistButton(watchlisted, onToggleWatchlist, modifier = Modifier.weight(1f))
            }
        }
    }
}
```

### X-Ray Cast Overlay

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun PrimeXRayOverlay(
    cast: List<CastMember>,
    nowPlaying: String?,
    onDismiss: () -> Unit,
) {
    // Slides up over the lower video; Android has no live blur, so a 96%-opaque navy surface approximates iOS material.
    AnimatedVisibility(
        visible = true,
        enter = slideInVertically { it } ,
        exit = slideOutVertically { it },
        modifier = Modifier.fillMaxWidth(),
    ) {
        Column(
            Modifier
                .fillMaxWidth()
                .clip(RoundedCornerShape(topStart = 16.dp, topEnd = 16.dp))
                .background(PrimeColors.Surface2.copy(alpha = 0.96f))
                .padding(20.dp),
            verticalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Row(verticalAlignment = Alignment.CenterVertically) {
                Text("IN THIS SCENE", style = PrimeText.LabelUpper,
                    color = PrimeColors.TextSecondary, modifier = Modifier.weight(1f))
                IconButton(onClick = onDismiss) {
                    Icon(Icons.Filled.KeyboardArrowDown, contentDescription = "Close X-Ray",
                        tint = PrimeColors.TextSecondary)
                }
            }
            LazyRow(horizontalArrangement = Arrangement.spacedBy(16.dp)) {
                items(cast) { m ->
                    Column(Modifier.width(80.dp), horizontalAlignment = Alignment.CenterHorizontally,
                           verticalArrangement = Arrangement.spacedBy(6.dp)) {
                        AsyncImage(model = m.headshotUrl, contentDescription = m.name,
                            modifier = Modifier.size(56.dp).clip(CircleShape), contentScale = ContentScale.Crop)
                        Text(m.name, style = PrimeText.TileSubtitle, color = Color.White, maxLines = 1)
                        Text(m.role, style = PrimeText.TileSubtitle, color = PrimeColors.TextSecondary, maxLines = 1)
                    }
                }
            }
            if (nowPlaying != null) {
                Row(verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                    Icon(Icons.Filled.MusicNote, contentDescription = null, tint = PrimeColors.Blue,
                        modifier = Modifier.size(16.dp))
                    Text("Now playing: $nowPlaying", style = PrimeText.Meta, color = Color.White)
                }
            }
        }
    }
}
```

## 4. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Prime Video's iOS tab bar is `.regularMaterial` blur; Android has no first-class live blur, so use a 94%-opaque navy surface. **Active tint is Prime Blue** — blue is the indicator.

```kotlin
@Composable
fun PrimeBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = PrimeColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"      to Icons.Filled.Home,
            "Store"     to Icons.Filled.ShoppingBag,
            "Live"      to Icons.Filled.Sensors,
            "Find"      to Icons.Filled.Search,
            "Downloads" to Icons.Filled.DownloadForOffline,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = PrimeText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = PrimeColors.Blue,   // blue is the indicator
                    selectedTextColor   = PrimeColors.Blue,
                    unselectedIconColor = PrimeColors.TextSecondary,
                    unselectedTextColor = PrimeColors.TextSecondary,
                    indicatorColor      = Color.Transparent,
                ),
            )
        }
    }
}
```

## 5. Motion

| Moment | Compose recipe |
|--------|----------------|
| Tile press scale-up | `animateFloatAsState` 1 → 1.04, `tween(180)` ease-out + focus shadow + blue ring |
| Play CTA tap | `animateFloatAsState` 1 → 0.97 with `spring(dampingRatio = 0.7f)`, `HapticFeedbackType.LongPress` |
| Watchlist add | `Animatable` 1 → 1.15 → 1 (`spring`); `HapticFeedbackType.LongPress` |
| X-Ray slide-up | `AnimatedVisibility` + `slideInVertically { it }` / `slideOutVertically { it }` (~280ms) |
| Row snap | `LazyRow` + `rememberSnapFlingBehavior(lazyListState)` for paged deceleration |

```kotlin
// Snap-paginating content row
val state = rememberLazyListState()
LazyRow(
    state = state,
    flingBehavior = rememberSnapFlingBehavior(lazyListState = state),
    horizontalArrangement = Arrangement.spacedBy(12.dp),
    contentPadding = PaddingValues(horizontal = 16.dp),
) { /* tiles */ }
```

Haptics: prefer `LocalHapticFeedback`. For richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(8, ...)` to approximate iOS's `.light` impact.

## 6. Icons

Prime Video ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Prime's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Play / Resume | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Skip Forward 10 | `goforward.10` | `Icons.Filled.Forward10` |
| Skip Back 10 | `gobackward.10` | `Icons.Filled.Replay10` |
| Watchlist add | `plus` / `checkmark` | `Icons.Filled.Add` / `Icons.Filled.Check` |
| X-Ray | `info.circle` | `Icons.Filled.Info` |
| Download | `arrow.down.to.line` | `Icons.Filled.FileDownload` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Audio / subtitles | `captions.bubble` | `Icons.Filled.ClosedCaption` |
| AirPlay | `airplayvideo` | `Icons.Filled.Cast` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Store (tab) | `bag.fill` | `Icons.Filled.ShoppingBag` |
| Live (tab) | `dot.radiowaves.left.and.right` | `Icons.Filled.Sensors` |
| Downloads (tab) | `arrow.down.circle.fill` | `Icons.Filled.DownloadForOffline` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; snap fling + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the dark-navy canvas wants `WindowCompat` light-content system bars. Apply `Scaffold` insets so the tab bar and X-Ray panel clear gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on titles, episode titles, synopsis. Pin layout-sensitive text (tab labels, scrubber timestamps, tile subtitles) via `dp`-derived sizes.
- **TalkBack**: announce tiles ("Play The Citadel Files, included with Prime"); in the X-Ray panel merge each member as "Name as Role" and mark the dismiss chevron as a button.
- **Touch targets**: Material guidance is 48.dp minimum. The 52.dp Play CTA and watchlist button clear it; pad the X-Ray dismiss icon and small actions to 48.dp.
- **Contrast**: `#AAB7C4` on `#0F171E` passes WCAG AA at 13sp+. The bright blue (`#00A8E1`) with dark-navy (`#0F171E`) text exceeds AAA — keep that pairing; never white-on-blue. IMDb yellow (`#F5C518`) on navy passes AA at 13sp+ as a badge.
- **Dynamic color**: do **not** enable Material You `dynamicDarkColorScheme()` — Prime Video's brand requires the fixed `#0F171E` navy canvas and single Prime-Blue accent regardless of wallpaper.
