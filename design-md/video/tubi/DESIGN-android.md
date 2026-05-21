# Tubi (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Tubi's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, gradient brushes, a `Typography` set, paste-ready `@Composable`s (hero, poster, content row, Live TV EPG), motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Tubi's indigo-black canvas, the purple→magenta gradient as the brand, the dense free-poster rows, the white Play button) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Brush.linearGradient` instead of SwiftUI's `LinearGradient`, `LazyRow` instead of a horizontal `ScrollView`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+`. Tubi is **dark-only** — no light scheme, no Material You dynamic color.

## 1. Color Tokens

```kotlin
// ui/theme/TubiColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.geometry.Offset

object TubiColors {
    // Canvas & Surfaces (dark-only)
    val Canvas   = Color(0xFF0A0A2A)
    val Surface1 = Color(0xFF15153D)
    val Surface2 = Color(0xFF1E1E52)
    val Surface3 = Color(0xFF262666)
    val Divider  = Color(0xFF2A2A5C)

    // Brand gradient stops
    val Purple   = Color(0xFF7408FF)
    val Magenta  = Color(0xFFFF00FF)
    val Violet   = Color(0xFFA12BFF)
    val Pink     = Color(0xFFFF4FD8)

    // CTA / accent
    val PlayWhite   = Color(0xFFFFFFFF)
    val PlayPressed = Color(0xFFE6E6F2)
    val FreeYellow  = Color(0xFFFFD400)
    val OnWhite     = Color(0xFF0A0A2A)
    val FreeText    = Color(0xFF1A0A2A)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFB9B9D6)
    val TextTertiary  = Color(0xFF7A7AA0)

    // Semantic
    val Success = Color(0xFF1FD17B)
    val Error   = Color(0xFFFF4A6E)
    val Live    = Color(0xFFFF3B5C)
    val Rating  = Color(0xFFFFB020)

    // Gradient brushes
    val BrandBrush    = Brush.linearGradient(listOf(Purple, Magenta))
    val ProgressBrush = Brush.horizontalGradient(listOf(Purple, Magenta))
    val HeroScrim     = Brush.verticalGradient(
        0.40f to Color.Transparent,
        1.0f  to Canvas.copy(alpha = 0.95f),
    )
}
```

Wire it into a dark-only scheme. Tubi has no light mode and no brand accent that should harmonize with the wallpaper — never enable `dynamicColorScheme()`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme

private val TubiDark = darkColorScheme(
    primary        = TubiColors.Magenta,
    onPrimary      = TubiColors.TextPrimary,
    background     = TubiColors.Canvas,
    onBackground   = TubiColors.TextPrimary,
    surface        = TubiColors.Surface1,
    onSurface      = TubiColors.TextPrimary,
    surfaceVariant = TubiColors.Surface2,
    outline        = TubiColors.Divider,
    error          = TubiColors.Error,
)

@Composable
fun TubiTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = TubiDark,          // always dark
        typography  = TubiTypography,
        content     = content,
    )
```

## 2. Typography

Tubi's brand face is a tight geometric grotesque; **Inter** at heavy weights is the closest free analog (SIL OFL). Drop the TTFs in `res/font/`. Titles are heavy (800–900), body 400; only badges are ALL-CAPS + positive-tracked.

```kotlin
// ui/theme/TubiType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,    FontWeight.Normal),
    Font(R.font.inter_medium,     FontWeight.Medium),
    Font(R.font.inter_semibold,   FontWeight.SemiBold),
    Font(R.font.inter_bold,       FontWeight.Bold),
    Font(R.font.inter_extrabold,  FontWeight.ExtraBold),
    Font(R.font.inter_black,      FontWeight.Black),
)

object TubiText {
    val HeroTitle   = TextStyle(Inter, fontWeight = FontWeight.Black,      fontSize = 32.sp, lineHeight = 32.sp, letterSpacing = (-0.6).sp)
    val DetailTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold,  fontSize = 28.sp, lineHeight = 30.sp, letterSpacing = (-0.5).sp)
    val RowHeader   = TextStyle(Inter, fontWeight = FontWeight.ExtraBold,  fontSize = 22.sp, lineHeight = 24.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.Bold,       fontSize = 18.sp, lineHeight = 22.sp, letterSpacing = (-0.2).sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,     fontSize = 16.sp, lineHeight = 24.sp)
    val CardTitle   = TextStyle(Inter, fontWeight = FontWeight.SemiBold,   fontSize = 15.sp, lineHeight = 17.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.ExtraBold,  fontSize = 15.sp, lineHeight = 15.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Medium,     fontSize = 13.sp, lineHeight = 17.sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Medium,     fontSize = 12.sp, lineHeight = 16.sp)
    val Badge       = TextStyle(Inter, fontWeight = FontWeight.Bold,       fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.4.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

val TubiTypography = Typography(
    headlineLarge  = TubiText.HeroTitle,
    headlineMedium = TubiText.RowHeader,
    titleMedium    = TubiText.Section,
    bodyMedium     = TubiText.Body,
    labelSmall     = TubiText.Tab,
)
```

## 3. Signature Components

### Featured Hero

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import coil.compose.AsyncImage

@Composable
fun TubiHero(
    backdropUrl: String,
    badge: String,
    title: String,
    meta: String,
    onPlay: () -> Unit,
    onAdd: () -> Unit,
) {
    Box(
        Modifier
            .padding(horizontal = 14.dp)
            .fillMaxWidth()
            .height(230.dp)
            .clip(RoundedCornerShape(18.dp)),
    ) {
        AsyncImage(
            model = backdropUrl, contentDescription = null,
            modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop,
        )
        Box(Modifier.fillMaxSize().background(TubiColors.HeroScrim))
        Column(
            Modifier.fillMaxSize().padding(16.dp),
            verticalArrangement = Arrangement.SpaceBetween,
        ) {
            Box(
                Modifier
                    .clip(RoundedCornerShape(50))
                    .background(TubiColors.BrandBrush)
                    .padding(horizontal = 8.dp, vertical = 4.dp),
            ) { Text(badge.uppercase(), style = TubiText.Badge.copy(fontSize = 9.sp), color = Color.White) }

            Column {
                Text(title, style = TubiText.HeroTitle, color = TubiColors.TextPrimary)
                Text(meta, style = TubiText.Meta, color = TubiColors.TextSecondary, modifier = Modifier.padding(top = 7.dp))
                Row(Modifier.padding(top = 12.dp), horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                    Button(
                        onClick = onPlay,
                        shape = RoundedCornerShape(8.dp),
                        colors = ButtonDefaults.buttonColors(containerColor = TubiColors.PlayWhite),
                        modifier = Modifier.weight(1f).height(48.dp),
                    ) {
                        Icon(Icons.Filled.PlayArrow, null, tint = TubiColors.OnWhite, modifier = Modifier.size(16.dp))
                        Spacer(Modifier.width(6.dp))
                        Text("Play Free", style = TubiText.Button, color = TubiColors.OnWhite)
                    }
                    FilledIconButton(
                        onClick = onAdd,
                        shape = RoundedCornerShape(8.dp),
                        colors = IconButtonDefaults.filledIconButtonColors(containerColor = Color.White.copy(alpha = 0.16f)),
                        modifier = Modifier.size(44.dp, 48.dp),
                    ) { Icon(Icons.Filled.Add, "Add to My List", tint = Color.White) }
                }
            }
        }
    }
}
```

### Poster Card

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.border
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.clickable
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow

@Composable
fun TubiPoster(
    imageUrl: String,
    title: String,
    progress: Float? = null,   // 0f..1f
    onClick: () -> Unit,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 1.04f else 1f, tween(150), label = "posterScale")

    Box(
        Modifier
            .size(100.dp, 150.dp)
            .scale(scale)
            .shadow(8.dp, RoundedCornerShape(10.dp), spotColor = Color.Black.copy(alpha = 0.45f))
            .clip(RoundedCornerShape(10.dp))
            .then(if (pressed) Modifier.border(BorderStroke(2.dp, TubiColors.BrandBrush), RoundedCornerShape(10.dp)) else Modifier)
            .clickable(interaction, indication = null, onClick = onClick),
    ) {
        AsyncImage(model = imageUrl, contentDescription = title,
            modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)

        // FREE tag
        Box(
            Modifier
                .padding(6.dp)
                .clip(RoundedCornerShape(3.dp))
                .background(TubiColors.FreeYellow)
                .padding(horizontal = 5.dp, vertical = 2.dp),
        ) { Text("FREE", fontWeight = FontWeight.Black, fontSize = 8.sp, color = TubiColors.FreeText) }

        Text(
            title, style = TubiText.Caption.copy(fontWeight = FontWeight.Bold, fontSize = 11.sp),
            color = Color.White, maxLines = 2,
            modifier = Modifier.align(Alignment.BottomStart).padding(6.dp),
        )

        if (progress != null) {
            Box(
                Modifier
                    .align(Alignment.BottomStart)
                    .fillMaxWidth()
                    .height(4.dp)
                    .background(Color.White.copy(alpha = 0.16f)),
            ) {
                Box(
                    Modifier
                        .fillMaxWidth(progress)
                        .fillMaxHeight()
                        .background(TubiColors.ProgressBrush),
                )
            }
        }
    }
}
```

### Content Row

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.items

@Composable
fun <T> TubiRow(
    title: String,
    items: List<T>,
    showSeeAll: Boolean = false,
    key: (T) -> Any,
    card: @Composable (T) -> Unit,
) {
    Column(Modifier.padding(top = 16.dp)) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 0.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text(title, style = TubiText.RowHeader, color = TubiColors.TextPrimary)
            Spacer(Modifier.weight(1f))
            if (showSeeAll) Text("See All ›", style = TubiText.Meta.copy(fontWeight = FontWeight.Bold), color = TubiColors.Pink)
        }
        Spacer(Modifier.height(10.dp))
        LazyRow(
            contentPadding = PaddingValues(horizontal = 16.dp),
            horizontalArrangement = Arrangement.spacedBy(10.dp),
        ) {
            items(items, key = key) { card(it) }
        }
    }
}
```

### Live TV EPG Row

```kotlin
data class EpgProgram(val title: String, val timeRange: String, val isOnNow: Boolean)

@Composable
fun EpgRow(logoUrl: String, channelName: String, programs: List<EpgProgram>) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 8.dp),
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        AsyncImage(
            model = logoUrl, contentDescription = null,
            modifier = Modifier.size(56.dp).clip(RoundedCornerShape(10.dp)).background(TubiColors.Surface1),
            contentScale = ContentScale.Crop,
        )
        Column(Modifier.weight(1f)) {
            Text(channelName, style = TubiText.CardTitle, color = TubiColors.TextPrimary)
            Spacer(Modifier.height(6.dp))
            LazyRow(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                items(programs) { p ->
                    Box(
                        Modifier
                            .width(150.dp)
                            .clip(RoundedCornerShape(10.dp))
                            .background(TubiColors.Surface2),
                    ) {
                        if (p.isOnNow) {
                            Box(Modifier.fillMaxHeight().width(3.dp).background(TubiColors.BrandBrush))
                        }
                        Column(Modifier.padding(8.dp)) {
                            if (p.isOnNow) {
                                Row(verticalAlignment = Alignment.CenterVertically,
                                    horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                                    Box(Modifier.size(6.dp).clip(RoundedCornerShape(50)).background(TubiColors.Live))
                                    Text("LIVE", fontWeight = FontWeight.Black, fontSize = 9.sp, color = TubiColors.Live)
                                }
                            }
                            Text(p.title, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, color = TubiColors.TextPrimary)
                            Text(p.timeRange, fontWeight = FontWeight.Medium, fontSize = 11.sp, color = TubiColors.TextSecondary)
                        }
                    }
                }
            }
        }
    }
}
```

## 4. Navigation

Tubi has a 5-tab bottom strip; active is **magenta** with no Material tint pill.

```kotlin
@Composable
fun TubiBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = TubiColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Search"   to Icons.Filled.Search,
            "Live TV"  to Icons.Filled.Tv,
            "My List"  to Icons.Filled.Bookmark,
            "Account"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = TubiText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = TubiColors.Magenta,
                    selectedTextColor   = TubiColors.Magenta,
                    unselectedIconColor = TubiColors.TextTertiary,
                    unselectedTextColor = TubiColors.TextTertiary,
                    indicatorColor      = Color.Transparent,  // no Material pill
                ),
            )
        }
    }
}
```

## 5. Motion

Tubi motion is punchy but brief — 120–400ms. The gradient acts as a "lit-by-brand" focus cue.

| Moment | Compose recipe |
|--------|----------------|
| Poster press/focus | `animateFloatAsState` scale 1f → 1.04f `tween(150)` + 2dp `BrandBrush` border while pressed |
| Hero cross-dissolve | `Crossfade(targetState = featuredIndex, animationSpec = tween(400))`; ~6s auto-advance via `LaunchedEffect` |
| Continue-watching fill | `animateFloatAsState(progress, tween(500))` driving `fillMaxWidth(fraction)` |
| Add to My List | icon `Crossfade` Add→Check + scale spring 1→1.2→1; soft haptic |
| Play transition | `AnimatedContent` cross-fade into player `tween(350)` |
| Tab switch | instant content swap; icon color animates to magenta `tween(200)` |
| Bottom sheet | `ModalBottomSheet` slide-up; scrim `Color(0xFF050510).copy(alpha = 0.7f)` |

```kotlin
// Hero auto cross-dissolve
var idx by remember { mutableIntStateOf(0) }
LaunchedEffect(Unit) {
    while (true) { delay(6000); idx = (idx + 1) % featured.size }
}
Crossfade(targetState = idx, animationSpec = tween(400), label = "hero") { i -> TubiHero(/* featured[i] */) }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on Play, add-to-list confirm, and tab switch. Auto-resume of continue-watching is silent.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Live TV (tab) | `tv` | `Icons.Filled.Tv` |
| My List (tab) | `bookmark.fill` | `Icons.Filled.Bookmark` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Add to list | `plus` → `checkmark` | `Icons.Filled.Add` → `Icons.Filled.Check` |
| Download | `arrow.down.to.line` | `Icons.Filled.Download` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| More like this | `rectangle.stack` | `Icons.Filled.GridView` |
| Cast & crew | `person.2` | `Icons.Filled.People` |
| Live dot | `circle.fill` | `Icons.Filled.Circle` (tint `#FF3B5C`) |
| Restart | `gobackward` | `Icons.Filled.Replay` |
| Settings | `gearshape` | `Icons.Filled.Settings` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24**, `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; force light-content system bars (the app is always dark). The hero backdrop should draw under the status bar; the bottom bar respects the navigation-bar inset.
- **Dark-only**: never call `dynamicColorScheme()` and never build a light scheme — Tubi's indigo-and-gradient identity is fixed regardless of wallpaper, and there is no brand accent to harmonize with one.
- **Font scaling**: `sp` honors the user's font scale — keep it on hero/detail titles, row headers, body, synopsis. Pin layout-sensitive text (badges, 10sp tab labels, progress labels, EPG time header) by deriving from `dp` or a fixed-`fontScale` `LocalDensity`.
- **Touch targets**: Material guidance is 48.dp. The 22dp tab icons sit in full `NavigationBarItem` targets; give the small poster FREE tag and EPG controls 48.dp hit areas where tappable; primary buttons are 48.dp tall.
- **TalkBack**: poster `contentDescription` must include "free to watch" (the yellow FREE tag is core information — never `clearAndSetSemantics {}` it away); continue-watching adds ", {n} minutes left"; EPG on-now block "{program}, live now on {channel}".
- **Contrast**: white on `#0A0A2A` and `#1A0A2A` on `#FFD400` both pass WCAG AA; `#B9B9D6` metadata on the canvas passes AA at 13sp+.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the poster press-scale and hero ken-burns; substitute a plain `Crossfade` and show the progress bar at final width.
- **The gradient is never the sole state signal**: focus also scales (1.04) and adds a border so it's perceivable for color-vision-deficient users.
