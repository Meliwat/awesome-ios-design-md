# Apple TV (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports the Apple TV app's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the inset hero card + Up Next rail, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the true-black canvas, the inset rounded floating hero, the Up Next rail with white resume bars, the single system-blue accent) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyRow` instead of horizontal `ScrollView`, `dp`/`sp` instead of `pt`. The Apple TV app exists on Android TV / Google TV, so this is a real surface.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for key art. The browse experience is true-black — no light scheme, no Material You dynamic color (the restrained Apple identity must hold regardless of wallpaper).

## 1. Color Tokens

```kotlin
// ui/theme/AppleTVColors.kt
import androidx.compose.ui.graphics.Color

object AppleTVColors {
    // Canvas & surfaces (true-black product)
    val Canvas   = Color(0xFF000000) // pure true black — OLED pixels off
    val Surface1 = Color(0xFF1C1C1E)
    val Surface2 = Color(0xFF2C2C2E)
    val Divider  = Color(0xFF38383A)

    // Text (iOS dark label ramp)
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF98989F)
    val TextTertiary  = Color(0xFF636366)

    // Primary CTA (always white)
    val Cta        = Color(0xFFFFFFFF)
    val CtaPressed = Color(0xFFE5E5EA)
    val CtaLabel   = Color(0xFF000000)

    // Accent (the only one — iOS dark systemBlue)
    val Blue        = Color(0xFF0A84FF)
    val BluePressed = Color(0xFF0060DF)

    // iOS translucent control fill (secondary buttons, search base)
    val GlassFill = Color(0x5C78787F) // ~36% (120,120,128)

    // Semantic
    val MLS     = Color(0xFFED1A6F) // MLS Season Pass ONLY
    val Live    = Color(0xFFFF453A)
    val Success = Color(0xFF30D158)
    val Gold    = Color(0xFFFFD60A)
}
```

The browse experience is true-black-first. Wire a single dark scheme; the light scheme is intentionally a copy (only embedded Store/account, out of scope here).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.runtime.Composable

private val AppleTVDark = darkColorScheme(
    primary        = AppleTVColors.Cta,         // white CTA
    onPrimary      = AppleTVColors.CtaLabel,
    secondary      = AppleTVColors.Blue,        // the single accent (links / active tab)
    background     = AppleTVColors.Canvas,      // pure black
    onBackground   = AppleTVColors.TextPrimary,
    surface        = AppleTVColors.Surface1,
    onSurface      = AppleTVColors.TextPrimary,
    surfaceVariant = AppleTVColors.Surface2,
    outline        = AppleTVColors.Divider,
    error          = AppleTVColors.Live,
)

@Composable
fun AppleTVTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = AppleTVDark,   // true-black only; do NOT use dynamicDarkColorScheme()
        typography  = AppleTVTypography,
        content     = content,
    )
```

## 2. Typography

The brand face is **SF Pro** (not redistributable). On Android, ship **Inter** (SIL OFL) in `res/font/` as the standard SF substitute — it shares the geometric-grotesque idiom. HIG-canonical ramp; `pt → sp` 1:1.

```kotlin
// ui/theme/AppleTVType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),
    Font(R.font.inter_medium,    FontWeight.Medium),
    Font(R.font.inter_semibold,  FontWeight.SemiBold),
    Font(R.font.inter_bold,      FontWeight.Bold),
    Font(R.font.inter_extrabold, FontWeight.ExtraBold),
)

object AppleTVText {
    val LargeTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 34.sp, lineHeight = 36.sp, letterSpacing = (-0.8).sp)
    val HeroTitle  = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 28.sp, lineHeight = 30.sp, letterSpacing = (-0.6).sp)
    val RowHeader  = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Title3     = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 20.sp, lineHeight = 25.sp, letterSpacing = (-0.2).sp)
    val Body       = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 17.sp, lineHeight = 25.sp)
    val Headline   = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Subhead    = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val Caption    = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 14.sp)
    val Eyebrow    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 1.2.sp)
    val Button     = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 15.sp)
    val ChannelTag = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 9.sp,  lineHeight = 9.sp,  letterSpacing = 0.4.sp)
}

val AppleTVTypography = Typography(
    headlineLarge  = AppleTVText.LargeTitle,
    headlineMedium = AppleTVText.HeroTitle,
    titleLarge     = AppleTVText.RowHeader,
    titleMedium    = AppleTVText.Title3,
    bodyLarge      = AppleTVText.Body,
    labelSmall     = AppleTVText.Caption,
)
```

## 3. Signature Components

### Inset Rounded Hero Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun HeroCard(
    artUrl: String,
    eyebrow: String,    // "Apple TV+ · New Episode"
    title: String,
    meta: String,
    onPlay: () -> Unit,
    onAdd: () -> Unit,
) {
    Box(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 14.dp)   // inset — the floating-gallery treatment
            .height(380.dp)
            .clip(RoundedCornerShape(16.dp))
    ) {
        AsyncImage(
            model = artUrl, contentDescription = title,
            modifier = Modifier.matchParentSize(), contentScale = ContentScale.Crop,
        )
        Box(
            Modifier.matchParentSize().background(
                Brush.verticalGradient(
                    0.40f to Color.Transparent,
                    0.72f to Color.Black.copy(alpha = 0.65f),
                    1.0f  to Color.Black.copy(alpha = 0.92f),
                )
            )
        )
        Column(
            Modifier.align(Alignment.BottomStart).padding(18.dp),
            verticalArrangement = Arrangement.spacedBy(7.dp),
        ) {
            Text(eyebrow.uppercase(), style = AppleTVText.Eyebrow, color = AppleTVColors.TextSecondary)
            Text(title, style = AppleTVText.HeroTitle, color = AppleTVColors.TextPrimary)
            Text(meta, style = AppleTVText.Caption, color = AppleTVColors.TextSecondary)
            Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {
                Button(
                    onClick = onPlay,
                    colors = ButtonDefaults.buttonColors(containerColor = AppleTVColors.Cta, contentColor = AppleTVColors.CtaLabel),
                    shape = RoundedCornerShape(12.dp),
                    contentPadding = PaddingValues(horizontal = 30.dp, vertical = 13.dp),
                ) {
                    Icon(Icons.Filled.PlayArrow, null, modifier = Modifier.size(14.dp))
                    Spacer(Modifier.width(7.dp))
                    Text("Play", style = AppleTVText.Button)
                }
                Surface(
                    onClick = onAdd,
                    color = AppleTVColors.GlassFill,
                    shape = CircleShape,
                    modifier = Modifier.size(44.dp),
                ) {
                    Box(contentAlignment = Alignment.Center) {
                        Icon(Icons.Filled.Add, null, tint = Color.White, modifier = Modifier.size(20.dp))
                    }
                }
            }
        }
    }
}
```

### Up Next Thumbnail (16:9 + resume bar)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.draw.scale

@Composable
fun UpNextThumb(
    artUrl: String,
    title: String,
    subhead: String,            // "S3 E8 · 24 min left"
    progress: Float = 0f,       // 0..1; 0 hides the bar
    channelTag: String? = "Apple TV+",
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val s by animateFloatAsState(if (pressed) 0.97f else 1f, label = "thumb")

    Column(Modifier.width(196.dp)) {
        Box(
            Modifier.size(196.dp, 110.dp).scale(s).clip(RoundedCornerShape(10.dp))
        ) {
            AsyncImage(model = artUrl, contentDescription = title,
                modifier = Modifier.matchParentSize(), contentScale = ContentScale.Crop)
            if (channelTag != null) {
                Box(
                    Modifier.align(Alignment.TopStart).padding(8.dp)
                        .clip(RoundedCornerShape(5.dp))
                        .background(Color.Black.copy(alpha = 0.40f)) // Android has no live blur; tinted scrim
                        .padding(horizontal = 6.dp, vertical = 3.dp)
                ) { Text(channelTag, style = AppleTVText.ChannelTag, color = Color.White) }
            }
            if (progress > 0f) {
                Box(
                    Modifier.align(Alignment.BottomStart).fillMaxWidth().height(4.dp)
                        .background(Color.White.copy(alpha = 0.28f))
                ) {
                    Box(Modifier.fillMaxWidth(progress).fillMaxHeight().background(Color.White))
                }
            }
        }
        Spacer(Modifier.height(8.dp))
        Text(title, style = AppleTVText.Headline, color = AppleTVColors.TextPrimary, maxLines = 1)
        Spacer(Modifier.height(2.dp))
        Text(subhead, style = AppleTVText.Subhead, color = AppleTVColors.TextSecondary, maxLines = 1)
    }
}
```

### Live Badge & MLS Chip

```kotlin
import androidx.compose.animation.core.*

@Composable
fun LiveBadge() {
    val t = rememberInfiniteTransition(label = "live")
    val a by t.animateFloat(1f, 0.4f, infiniteRepeatable(tween(1200), RepeatMode.Reverse), label = "dot")
    Row(
        Modifier.clip(RoundedCornerShape(6.dp)).background(AppleTVColors.Live)
            .padding(horizontal = 10.dp, vertical = 5.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Box(Modifier.size(7.dp).clip(CircleShape).background(Color.White.copy(alpha = a)))
        Text("LIVE", style = AppleTVText.Eyebrow.copy(letterSpacing = 0.6.sp), color = Color.White)
    }
}

@Composable
fun MLSChip(title: String) {
    // #ED1A6F is the ONLY non-system brand color — MLS Season Pass surfaces only
    Box(
        Modifier.clip(RoundedCornerShape(10.dp)).background(AppleTVColors.MLS)
            .padding(horizontal = 14.dp, vertical = 7.dp)
    ) { Text(title, style = AppleTVText.Caption.copy(fontWeight = FontWeight.Bold), color = Color.White) }
}
```

### Shelf Header

```kotlin
import androidx.compose.material.icons.filled.ChevronRight

@Composable
fun ShelfHeader(title: String, accessory: (@Composable () -> Unit)? = null) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 18.dp).padding(bottom = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Text(title, style = AppleTVText.RowHeader, color = AppleTVColors.TextPrimary)
        accessory?.invoke()
        Spacer(Modifier.weight(1f))
        Icon(Icons.Filled.ChevronRight, null, tint = AppleTVColors.TextTertiary, modifier = Modifier.size(16.dp))
    }
}
```

## 4. Navigation

The Apple TV app has a 4-tab bottom strip and a large title. On Android, model the strip as a `NavigationBar` with a translucent surface; there is no tint pill — active is the system-blue icon.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun AppleTVBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = AppleTVColors.Canvas.copy(alpha = 0.92f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Watch Now" to Icons.Filled.PlayArrow,
            "TV+"       to Icons.Filled.Tv,
            "Store"     to Icons.Filled.ShoppingBag,
            "Search"    to Icons.Filled.Search,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = AppleTVText.Caption) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = AppleTVColors.Blue,   // system blue, no pill
                    selectedTextColor   = AppleTVColors.Blue,
                    unselectedIconColor = AppleTVColors.TextTertiary,
                    unselectedTextColor = AppleTVColors.TextTertiary,
                    indicatorColor      = Color.Transparent,     // no Material pill — Apple TV has none
                ),
            )
        }
    }
}
```

The large title is a `Text` in `AppleTVText.LargeTitle` with a trailing 30.dp circular avatar; collapse it to an inline `TopAppBar` on scroll via a `nestedScroll` connection. Content shelves are `LazyRow`s with `contentPadding = PaddingValues(horizontal = 18.dp)` so the last thumbnail clips off the trailing edge. The detail page is a standard pushed screen with the inset hero and the white Play CTA.

## 5. Motion

Apple TV motion is system-canonical and quiet — springs and standard transitions, no custom flourishes.

| Moment | Compose recipe |
|--------|----------------|
| Thumbnail press | `collectIsPressedAsState()` → `animateFloatAsState(0.97f, tween(120))` on `Modifier.scale` |
| Live dot pulse | `rememberInfiniteTransition` → `animateFloat(1f, 0.4f, infiniteRepeatable(tween(1200), Reverse))` |
| Hero cross-fade (~10s, if multiple) | `Crossfade(targetState = featuredIndex, animationSpec = tween(400))` |
| Large title collapse | `TopAppBarDefaults.enterAlwaysScrollBehavior()` / custom `nestedScroll` tied to offset |
| Detail open | `NavHost` slide push `tween(350)` (system-feeling) |
| Resume bar | rendered at value, no animation (it is state, not feedback) |
| Shelf scroll | `LazyRow` native fling — free scroll, no snap |

```kotlin
// Thumbnail press — the canonical quiet Apple TV motion
val s by animateFloatAsState(if (pressed) 0.97f else 1f, tween(120), label = "press")
Modifier.scale(s)
```

Haptics: keep it sparing and system-like — `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` (a light selection tick) on thumbnail tap; nothing custom. Playback start/stop is silent. Apple's restraint extends to haptics — do not over-buzz.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended` (its glyphs read close to SF Symbols). The Apple TV logomark must be a vector drawable / `ImageVector` — it is not an icon-font glyph.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Watch Now (tab) | `play.tv` / `play.tv.fill` | `Icons.Filled.PlayArrow` |
| TV+ (tab) | `play.rectangle.on.rectangle` | `Icons.Filled.Tv` |
| Store (tab) | `bag` / `bag.fill` | `Icons.Filled.ShoppingBag` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| MLS (when subscribed) | `sportscourt` / `soccerball` | `Icons.Filled.SportsSoccer` |
| Play (CTA) | `play.fill` | `Icons.Filled.PlayArrow` |
| Add | `plus` / `checkmark` | `Icons.Filled.Add` / `Icons.Filled.Check` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| More / disclosure | `chevron.right` | `Icons.Filled.ChevronRight` |
| Download | `arrow.down.circle` | `Icons.Filled.Download` |
| Cast / AirPlay | `airplayvideo` | `Icons.Filled.Cast` |
| Profile | (avatar) / `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Live | `dot.radiowaves.left.and.right` | `Icons.Filled.SensorsOutlined` |
| Trailer | `play.rectangle` | `Icons.Filled.SmartDisplay` |
| Info | `info.circle` | `Icons.Filled.InfoOutline` |
| Settings | `gearshape` | `Icons.Filled.Settings` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the true-black canvas wants light-content system bars in all states. The hero card is inset (so it never reaches the status bar); the bottom bar respects the navigation bar inset.
- **True-black only**: ship only `AppleTVDark`; do **not** call `dynamicDarkColorScheme()` — Apple TV's restrained identity must hold regardless of wallpaper. Ignore `isSystemInDarkTheme()` for browse.
- **Pure `#000000` matters**: the inset hero's "floating" effect depends on a true-black canvas (OLED pixels off). Do not substitute a charcoal `Surface`.
- **Font scaling**: `sp` honors the user's font scale — keep it on Large Title, hero, row headers, body, subhead. Pin layout-sensitive text (tab labels, channel tags, eyebrows, "LIVE") via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` so it never overflows thumbnail art.
- **TalkBack**: label Up Next thumbnails "{title}, {subhead}, {progress} percent watched"; the white CTA "Play {title}"; live cards "Live now"; group hero eyebrow+title+meta into one `Modifier.semantics(mergeDescendants = true)` node; mark the pulsing live dot `Modifier.clearAndSetSemantics {}` (decorative).
- **Touch targets**: Material guidance is 48.dp. Up Next thumbnails far exceed it; give the 24.dp tab icons a 48.dp hit area (`NavigationBarItem` handles this); the Play button is ≥ 44.dp tall; the round glass add is 44.dp.
- **Contrast**: white `#FFFFFF` and `#98989F` on `#000000` pass WCAG AA; the white CTA with black label passes AAA; `#0A84FF` is the system link color (AA on black); MLS pink `#ED1A6F` with white passes AA. Never place body text on the blue accent.
- **Reduce motion / transparency**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the hero `Crossfade` with an instant swap and stop the live-dot pulse (static dot); keep the press scale subtle. The Android tab background is already a tinted scrim (no live blur), so no transparency fallback is needed — but keep it ≥ 0.92 opacity for contrast.
- **Restraint is the brand**: do not add extra elevation, shadows, badges, or accent colors to "improve" the design — Apple TV's value is its minimalism. MLS pink `#ED1A6F` is the ONLY non-system brand color, used on MLS Season Pass surfaces only.
