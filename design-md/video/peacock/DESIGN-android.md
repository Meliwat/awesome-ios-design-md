# Peacock (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Peacock's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the hero billboard + channels rail + content rows, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the indigo-black canvas, the warm violet hero glow, the white Play CTA, the 5-color feather swoosh) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyRow` instead of horizontal `ScrollView`, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for key art. Peacock is dark-only — no light scheme, no Material You dynamic color (the feather identity must hold regardless of wallpaper).

## 1. Color Tokens

```kotlin
// ui/theme/PeacockColors.kt
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.Brush

object PeacockColors {
    // Canvas & surfaces (dark-only product)
    val Canvas     = Color(0xFF0A0A14) // deep indigo-black — never pure black
    val CanvasWarm = Color(0xFF140E26) // violet glow under hero
    val Surface1   = Color(0xFF1A1530)
    val Surface2   = Color(0xFF241C3D)
    val Divider    = Color(0xFF2C2545)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFB5B2C2)
    val TextTertiary  = Color(0xFF7A7689)

    // Primary CTA (always white)
    val Cta        = Color(0xFFFFFFFF)
    val CtaPressed = Color(0xFFE6E6EA)
    val CtaLabel   = Color(0xFF0A0A14)
    val Glass      = Color(0x24FFFFFF) // ~14% white

    // Feather accent (identity / upsell / selection only)
    val FeatherYellow = Color(0xFFFACC15)
    val FeatherOrange = Color(0xFFF97316)
    val FeatherPink   = Color(0xFFEC4899)
    val FeatherPurple = Color(0xFF8B5CF6)
    val FeatherBlue   = Color(0xFF2563EB)

    // Semantic
    val Live    = Color(0xFFE5142B)
    val Success = Color(0xFF1DB954)
    val ErrorR  = Color(0xFFFF453A)

    val FeatherBrush = Brush.horizontalGradient(
        listOf(FeatherYellow, FeatherOrange, FeatherPink, FeatherPurple, FeatherBlue)
    )
    val PremiumBrush = Brush.horizontalGradient(listOf(FeatherOrange, FeatherPink))
}
```

Peacock is dark-first. Wire a single dark scheme; the light scheme is intentionally a copy (only legal/web views ever appear light, and those are out of scope).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.runtime.Composable

private val PeacockDark = darkColorScheme(
    primary        = PeacockColors.Cta,          // white CTA
    onPrimary      = PeacockColors.CtaLabel,
    background     = PeacockColors.Canvas,
    onBackground   = PeacockColors.TextPrimary,
    surface        = PeacockColors.Surface1,
    onSurface      = PeacockColors.TextPrimary,
    surfaceVariant = PeacockColors.Surface2,
    outline        = PeacockColors.Divider,
    error          = PeacockColors.ErrorR,
)

@Composable
fun PeacockTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = PeacockDark,   // dark-only; do NOT use dynamicDarkColorScheme()
        typography  = PeacockTypography,
        content     = content,
    )
```

## 2. Typography

Peacock Sans is proprietary; ship **Poppins** (SIL OFL) in `res/font/`. Body heavy (600+), hero/header 700-800 — broadcast-promo loudness; `pt → sp` 1:1.

```kotlin
// ui/theme/PeacockType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.unit.sp

val Poppins = FontFamily(
    Font(R.font.poppins_regular,    FontWeight.Normal),
    Font(R.font.poppins_medium,     FontWeight.Medium),
    Font(R.font.poppins_semibold,   FontWeight.SemiBold),
    Font(R.font.poppins_bold,       FontWeight.Bold),
    Font(R.font.poppins_extrabold,  FontWeight.ExtraBold),
)

object PeacockText {
    val ScreenTitle = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.5).sp)
    val HeroTitle   = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 28.sp, lineHeight = 30.sp, letterSpacing = (-0.5).sp)
    val RowHeader   = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 25.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 22.sp)
    val Body        = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 24.sp)
    val BodyReg     = TextStyle(Poppins, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val Caption     = TextStyle(Poppins, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(Poppins, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val PosterCap   = TextStyle(Poppins, fontWeight = FontWeight.Medium,    fontSize = 11.sp, lineHeight = 14.sp)
    val Tab         = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Eyebrow     = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 1.4.sp)
    val Button      = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp)
    val Rank        = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 13.sp, lineHeight = 13.sp)
    val Badge       = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.4.sp)
}

val PeacockTypography = Typography(
    headlineLarge  = PeacockText.ScreenTitle,
    headlineMedium = PeacockText.HeroTitle,
    titleLarge     = PeacockText.RowHeader,
    titleMedium    = PeacockText.CardTitle,
    bodyMedium     = PeacockText.Body,
    labelSmall     = PeacockText.Tab,
)
```

## 3. Signature Components

### Hero Billboard

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun HeroBillboard(
    artUrl: String,
    eyebrow: String,
    title: String,
    meta: String,
    onPlay: () -> Unit,
    onAdd: () -> Unit,
) {
    Box(Modifier.fillMaxWidth().height(360.dp)) {
        AsyncImage(
            model = artUrl, contentDescription = title,
            modifier = Modifier.matchParentSize(),
            contentScale = ContentScale.Crop,
        )
        Box(
            Modifier
                .matchParentSize()
                .background(
                    Brush.verticalGradient(
                        0.38f to Color.Transparent,
                        0.70f to PeacockColors.Canvas.copy(alpha = 0.70f),
                        1.0f  to PeacockColors.Canvas,
                    )
                )
        )
        Column(
            Modifier.align(Alignment.BottomCenter).padding(horizontal = 20.dp, vertical = 14.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Text(eyebrow.uppercase(), style = PeacockText.Eyebrow, color = PeacockColors.FeatherYellow)
            Text(title, style = PeacockText.HeroTitle, color = PeacockColors.TextPrimary, textAlign = TextAlign.Center)
            Text(meta, style = PeacockText.Meta, color = PeacockColors.TextSecondary)
            Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {
                Button(
                    onClick = onPlay,
                    colors = ButtonDefaults.buttonColors(containerColor = PeacockColors.Cta, contentColor = PeacockColors.CtaLabel),
                    shape = RoundedCornerShape(6.dp),
                    contentPadding = PaddingValues(horizontal = 26.dp, vertical = 13.dp),
                ) {
                    Icon(Icons.Filled.PlayArrow, null, modifier = Modifier.size(16.dp))
                    Spacer(Modifier.width(7.dp))
                    Text("Play", style = PeacockText.Button)
                }
                Surface(
                    onClick = onAdd,
                    color = PeacockColors.Glass,
                    shape = RoundedCornerShape(6.dp),
                ) {
                    Row(Modifier.padding(horizontal = 18.dp, vertical = 12.dp), verticalAlignment = Alignment.CenterVertically) {
                        Icon(Icons.Filled.Add, null, tint = PeacockColors.TextPrimary, modifier = Modifier.size(16.dp))
                        Spacer(Modifier.width(6.dp))
                        Text("My Stuff", style = PeacockText.Body, color = PeacockColors.TextPrimary)
                    }
                }
            }
        }
    }
}
```

### Channels Rail

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.items
import androidx.compose.ui.draw.clip

data class Channel(val name: String, val gradient: List<Color>, val live: Boolean = false)

@Composable
fun ChannelsRail(channels: List<Channel>) {
    Column(Modifier.padding(vertical = 6.dp)) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 18.dp).padding(bottom = 10.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            Text("Channels", style = PeacockText.RowHeader, color = PeacockColors.TextPrimary)
            Text("See All", style = PeacockText.Caption, color = PeacockColors.TextSecondary)
        }
        LazyRow(
            contentPadding = PaddingValues(horizontal = 18.dp),
            horizontalArrangement = Arrangement.spacedBy(10.dp),
        ) {
            items(channels) { ch ->
                Box {
                    Box(
                        Modifier
                            .size(60.dp)
                            .clip(RoundedCornerShape(14.dp))
                            .background(Brush.linearGradient(ch.gradient)),
                        contentAlignment = Alignment.Center,
                    ) { Text(ch.name, style = PeacockText.Badge, color = Color.White) }
                    if (ch.live) {
                        Box(
                            Modifier.align(Alignment.TopEnd).padding(4.dp)
                                .clip(RoundedCornerShape(3.dp))
                                .background(PeacockColors.Live)
                                .padding(horizontal = 4.dp, vertical = 2.dp)
                        ) { Text("LIVE", style = PeacockText.Badge.copy(fontSize = 8.sp), color = Color.White) }
                    }
                }
            }
        }
    }
}
```

### Poster Card (rank + Top 10)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.draw.scale
import androidx.compose.ui.input.pointer.pointerInput

@Composable
fun PosterCard(artUrl: String, title: String, rank: Int? = null, top10: Boolean = false) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "posterScale")

    Column(Modifier.width(112.dp)) {
        Box(
            Modifier
                .size(112.dp, 168.dp)
                .scale(scale)
                .clip(RoundedCornerShape(8.dp))
        ) {
            AsyncImage(model = artUrl, contentDescription = title,
                modifier = Modifier.matchParentSize(), contentScale = ContentScale.Crop)
            if (top10) {
                Box(
                    Modifier.align(Alignment.TopStart).padding(6.dp)
                        .clip(RoundedCornerShape(4.dp))
                        .background(PeacockColors.PremiumBrush)
                        .padding(horizontal = 6.dp, vertical = 3.dp)
                ) { Text("TOP 10", style = PeacockText.Badge, color = Color.White) }
            }
            if (rank != null) {
                Box(
                    Modifier.align(Alignment.BottomStart).padding(6.dp)
                        .clip(RoundedCornerShape(6.dp))
                        .background(Color.Black.copy(alpha = 0.45f))
                        .padding(horizontal = 7.dp, vertical = 2.dp)
                ) { Text("$rank", style = PeacockText.Rank, color = Color.White) }
            }
        }
        Spacer(Modifier.height(6.dp))
        Text(title, style = PeacockText.PosterCap, color = PeacockColors.TextSecondary, maxLines = 1)
    }
}
```

### Live Badge (pulsing dot)

```kotlin
import androidx.compose.animation.core.*

@Composable
fun LiveBadge() {
    val t = rememberInfiniteTransition(label = "live")
    val alpha by t.animateFloat(
        1f, 0.4f,
        infiniteRepeatable(tween(1200), RepeatMode.Reverse), label = "dot"
    )
    Row(
        Modifier.clip(RoundedCornerShape(4.dp)).background(PeacockColors.Live)
            .padding(horizontal = 10.dp, vertical = 5.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Box(Modifier.size(7.dp).clip(CircleShape).background(Color.White.copy(alpha = alpha)))
        Text("LIVE", style = PeacockText.Badge.copy(letterSpacing = 0.6.sp), color = Color.White)
    }
}
```

### Continue Watching Progress + Premium Button

```kotlin
@Composable
fun WatchProgress(fraction: Float, label: String) {
    val w by animateFloatAsState(fraction, tween(400), label = "progress")
    Column(verticalArrangement = Arrangement.spacedBy(6.dp)) {
        Box(Modifier.fillMaxWidth().height(4.dp).clip(RoundedCornerShape(2.dp)).background(PeacockColors.Surface2)) {
            Box(Modifier.fillMaxWidth(w).height(4.dp).clip(RoundedCornerShape(2.dp)).background(PeacockColors.PremiumBrush))
        }
        Text(label, style = PeacockText.PosterCap, color = PeacockColors.TextSecondary)
    }
}

@Composable
fun PremiumButton(title: String, onClick: () -> Unit) {
    Surface(onClick = onClick, shape = RoundedCornerShape(6.dp), color = Color.Transparent) {
        Box(
            Modifier.background(PeacockColors.FeatherBrush, RoundedCornerShape(6.dp))
                .padding(horizontal = 22.dp, vertical = 12.dp)
        ) { Text(title, style = PeacockText.Button, color = Color.White) }
    }
}
```

## 4. Navigation

Peacock has minimal chrome: a 4-tab bottom strip and a transparent top bar. On Android, model the strip as a `NavigationBar`. There is no tint pill — active is just the white icon.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun PeacockBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = PeacockColors.Canvas.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Channels" to Icons.Filled.Tv,
            "Search"   to Icons.Filled.Search,
            "My Stuff" to Icons.Filled.Bookmark,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = PeacockText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = PeacockColors.TextPrimary,
                    selectedTextColor   = PeacockColors.TextPrimary,
                    unselectedIconColor = PeacockColors.TextTertiary,
                    unselectedTextColor = PeacockColors.TextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill — Peacock has none
                ),
            )
        }
    }
}
```

The top bar is a transparent `Row` over the hero (feather logomark SVG + "peacock" wordmark leading; search + profile trailing) that gains a `Canvas` background on scroll. Content rows are `LazyRow`s with `contentPadding = PaddingValues(horizontal = 18.dp)` so the last card clips off the trailing edge — the "more content" affordance. The detail page is a full-bleed `AsyncImage` with a back chevron on a 40%-black circle.

## 5. Motion

Peacock motion is cinematic and quiet — 120-400ms ease-out, never bouncy.

| Moment | Compose recipe |
|--------|----------------|
| Poster press | `collectIsPressedAsState()` → `animateFloatAsState(0.97f, tween())` on `Modifier.scale` |
| Live dot pulse | `rememberInfiniteTransition` → `animateFloat(1f, 0.4f, infiniteRepeatable(tween(1200), Reverse))` |
| Hero cross-fade (~8s) | `Crossfade(targetState = featuredIndex, animationSpec = tween(400))` |
| Detail open | shared-element via `androidx.compose.animation` `SharedTransitionLayout` (Compose 1.7+) or `AnimatedContent` slide `tween(300)` |
| Progress fill | `animateFloatAsState(fraction, tween(400))` on `fillMaxWidth(w)` |
| Sheet present | `ModalBottomSheet` (Material 3) — slide-up 300ms; scrim `Color.Black.copy(alpha = 0.6f)` |
| Row scroll | `LazyRow` native fling — free scroll, no snap |

```kotlin
// Hero cross-fade — the canonical Peacock billboard motion
Crossfade(targetState = featuredIndex, animationSpec = tween(400), label = "hero") { idx ->
    HeroBillboard(/* featured[idx] */)
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft confirm on add-to-My-Stuff; a lighter `HapticFeedbackConstants.CLOCK_TICK` (via `LocalView`) for poster selection. Playback start/stop is silent.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. The 5-quill feather logomark must be a vector drawable / `ImageVector` — it is not an icon-font glyph.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Channels (tab) | `tv` | `Icons.Filled.Tv` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| My Stuff (tab) | `bookmark` / `bookmark.fill` | `Icons.Filled.Bookmark` |
| Play (CTA) | `play.fill` | `Icons.Filled.PlayArrow` |
| Add | `plus` / `checkmark` | `Icons.Filled.Add` / `Icons.Filled.Check` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| More info | `info.circle` | `Icons.Filled.InfoOutline` |
| Download | `arrow.down.circle` | `Icons.Filled.Download` |
| Cast | `airplayvideo` | `Icons.Filled.Cast` |
| Profile | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Live | `dot.radiowaves.left.and.right` | `Icons.Filled.SensorsOutlined` |
| Trailer | `play.rectangle` | `Icons.Filled.SmartDisplay` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `ModalBottomSheet` + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the indigo canvas wants light-content system bars in all states (the app is dark-only). Hero art bleeds under the status bar; the bottom bar respects the navigation bar inset.
- **Dark-only**: ship only `PeacockDark`; do **not** call `dynamicDarkColorScheme()` — the feather identity and warm violet glow must hold regardless of wallpaper. Ignore `isSystemInDarkTheme()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen/hero titles, row headers, body, captions. Pin layout-sensitive text (tab labels, 11sp eyebrows, badges, rank numerals, "LIVE") via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` so it never overflows poster art.
- **TalkBack**: label poster cards "{title}, rank {n}"; the white CTA "Play {title}"; live cards "Live now"; group hero eyebrow+title+meta into one `Modifier.semantics(mergeDescendants = true)` node; mark the pulsing live dot `Modifier.clearAndSetSemantics {}` (decorative — the "LIVE" text carries meaning).
- **Touch targets**: Material guidance is 48.dp. Channel tiles (60.dp) and poster cards exceed it; give the 22.dp tab icons a 48.dp hit area (`NavigationBarItem` handles this); the Play button is ≥ 44.dp tall.
- **Contrast**: white `#FFFFFF` and `#B5B2C2` on `#0A0A14` pass WCAG AA; the white CTA with `#0A0A14` label passes AAA. Never place body text on the feather gradient or over un-scrimmed art — always keep the bottom hero scrim.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the hero `Crossfade` with an instant swap and stop the live-dot pulse (show a static dot); keep the press scale subtle or disable.
- **Elevation cues**: shadows are nearly invisible on the near-black canvas — use the warm `#140E26` hero glow and a 1.dp `Divider` border on `ModalBottomSheet` / context menus as the floating cue, mirroring the iOS behavior.
