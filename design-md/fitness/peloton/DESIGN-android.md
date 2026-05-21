# Peloton (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Peloton's studio-black visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for class browse + in-class metrics (class card, LIVE badge, output ring, metric column, real-time leaderboard), navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (pure-black studio canvas, single Peloton-Red accent, cinematic thumbnails, the pulsing LIVE badge, the output ring, the white-chip selected pill, fixed-meaning metric colors) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas` for the rings, `LazyColumn` + `animateItemPlacement` for the live leaderboard, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for class thumbnails and avatars. Peloton is **dark-only** — there is no light scheme. **Do not** enable Material You dynamic color: the identity is the fixed pure-black + single Peloton-Red, and metric colors are semantic.

## 1. Color Tokens

```kotlin
// ui/theme/PLColors.kt
import androidx.compose.ui.graphics.Color

object PLColors {
    // Canvas & Surfaces (Dark — the only mode)
    val Canvas   = Color(0xFF000000)   // pure black studio canvas
    val Surface1 = Color(0xFF121212)
    val Surface2 = Color(0xFF1C1C1E)
    val Surface3 = Color(0xFF262629)
    val Divider  = Color(0xFF2C2C2E)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFFB5B5BA)
    val TextTertiary  = Color(0xFF7A7A80)

    // Brand (single accent)
    val Red        = Color(0xFFDF1E2E)   // fills (CTA, LIVE, output ring, your row)
    val RedOnDark  = Color(0xFFFF4B57)   // text / active on black (AA)
    val RedPressed = Color(0xFFB81825)
    val WhiteChip  = Color(0xFFFFFFFF)   // selected pill / secondary CTA

    // Functional / in-class metrics (fixed meaning — theme-invariant)
    val Cadence    = Color(0xFFE5402A)
    val Resistance = Color(0xFFF0A030)
    val Output     = Color(0xFFDF1E2E)
    val HeartRate  = Color(0xFFFF4B57)
    val Strive     = Color(0xFF3DB8E0)
    val PR         = Color(0xFF2ECC71)

    // Semantic
    val Success = Color(0xFF2ECC71)
    val Warning = Color(0xFFF0A030)
    val Error   = Color(0xFFDF1E2E)
    val Track   = Color(0xFF262629)
}
```

Wire it into a **dark-only** scheme — Peloton has no light mode.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val PLDark = darkColorScheme(
    primary        = PLColors.Red,
    onPrimary      = PLColors.TextPrimary,
    background     = PLColors.Canvas,
    onBackground   = PLColors.TextPrimary,
    surface        = PLColors.Surface1,
    onSurface      = PLColors.TextPrimary,
    surfaceVariant = PLColors.Surface2,
    outline        = PLColors.Divider,
    error          = PLColors.Error,
)

@Composable
fun PLTheme(content: @Composable () -> Unit) = MaterialTheme(
    colorScheme = PLDark,           // always studio-black; no light scheme
    typography  = PLTypography,
    content     = content,
)
```

## 2. Typography

Peloton sets **Inter** heavy and tight. Drop the TTFs (through Black) in `res/font/`. SIL OFL. Numerals tabular; the metric is the visual hero, labels are small uppercase whispers.

```kotlin
// ui/theme/PLType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,    FontWeight.Normal),
    Font(R.font.inter_semibold,   FontWeight.SemiBold),
    Font(R.font.inter_bold,       FontWeight.Bold),
    Font(R.font.inter_extrabold,  FontWeight.ExtraBold),
    Font(R.font.inter_black,      FontWeight.Black),
)

private const val TNUM = "tnum"   // tabular figures

object PLText {
    val OutputNumber = TextStyle(Inter, fontWeight = FontWeight.Black,     fontSize = 44.sp, lineHeight = 44.sp, letterSpacing = (-1.0).sp, fontFeatureSettings = TNUM)
    val ScreenTitle  = TextStyle(Inter, fontWeight = FontWeight.Black,     fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.6).sp)
    val HeroTitle    = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 30.sp, letterSpacing = (-0.5).sp)
    val Section      = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.4).sp)
    val ClassTitle   = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.3).sp)
    val MetricValue  = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 18.sp, lineHeight = 20.sp, fontFeatureSettings = TNUM)
    val LbOutput     = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 14.sp, lineHeight = 14.sp, fontFeatureSettings = TNUM)
    val Body         = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 22.sp)
    val Meta         = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Eyebrow      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 15.sp, letterSpacing = 0.6.sp)
    val MetricLabel  = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
    val Caption      = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 14.sp)
    val Button       = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 15.sp, letterSpacing = 0.2.sp)
    val Tab          = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Badge        = TextStyle(Inter, fontWeight = FontWeight.Black,     fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.6.sp)
}

val PLTypography = Typography(
    headlineLarge = PLText.ScreenTitle,
    titleLarge    = PLText.Section,
    bodyLarge     = PLText.Body,
    labelSmall    = PLText.Tab,
)
```

## 3. Signature Components

### Class Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
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
fun ClassCard(
    discipline: String, title: String, instructor: String,
    duration: String, isLive: Boolean, rating: String,
) {
    Column(
        Modifier
            .clip(RoundedCornerShape(14.dp))
            .background(PLColors.Surface1)
            .border(1.dp, PLColors.Divider, RoundedCornerShape(14.dp)),
    ) {
        Box(
            Modifier
                .fillMaxWidth()
                .height(150.dp)
                .background(Brush.linearGradient(listOf(Color(0xFF4A1014), Color(0xFF160508)))),
        ) {
            if (isLive) LiveBadge(Modifier.align(Alignment.TopStart).padding(14.dp))
            Text(
                duration, style = PLText.Caption, color = PLColors.TextPrimary,
                modifier = Modifier
                    .align(Alignment.TopEnd).padding(14.dp)
                    .clip(RoundedCornerShape(5.dp))
                    .background(Color.Black.copy(alpha = 0.6f))
                    .padding(horizontal = 9.dp, vertical = 5.dp),
            )
        }
        Column(Modifier.padding(horizontal = 16.dp, vertical = 14.dp)) {
            Text(discipline.uppercase(), style = PLText.Eyebrow, color = PLColors.RedOnDark)
            Text(title, style = PLText.ClassTitle, color = PLColors.TextPrimary, modifier = Modifier.padding(top = 4.dp))
            Text(instructor, style = PLText.Body, color = PLColors.TextSecondary, modifier = Modifier.padding(top = 3.dp))
            Row(Modifier.padding(top = 8.dp), verticalAlignment = Alignment.CenterVertically) {
                Icon(Icons.Filled.Star, null, tint = PLColors.Resistance, modifier = Modifier.size(11.dp))
                Text(" $rating", style = PLText.Caption, color = PLColors.TextTertiary)
            }
        }
    }
}
```

### LIVE Badge (pulsing dot)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.Icon

@Composable
fun LiveBadge(modifier: Modifier = Modifier) {
    val t = rememberInfiniteTransition(label = "live")
    val o by t.animateFloat(
        1f, 0.4f,
        infiniteRepeatable(tween(700), RepeatMode.Reverse), label = "dot",
    )
    Row(
        modifier
            .clip(RoundedCornerShape(5.dp))
            .background(PLColors.Red)
            .padding(horizontal = 9.dp, vertical = 5.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Box(Modifier.size(6.dp).clip(CircleShape)
            .background(Color.White.copy(alpha = o)))
        Text("LIVE", style = PLText.Badge, color = Color.White)
    }
}
```

### Output Ring (Canvas)

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.runtime.*
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke

@Composable
fun OutputRing(progress: Float, totalKJ: Int) {
    val p = remember { Animatable(0f) }
    LaunchedEffect(progress) {
        p.animateTo(progress, tween(600, easing = LinearOutSlowInEasing))
    }
    Box(Modifier.size(96.dp), contentAlignment = Alignment.Center) {
        Canvas(Modifier.fillMaxSize()) {
            val sw = 9.dp.toPx()
            drawArc(PLColors.Track, 0f, 360f, false, style = Stroke(sw))
            drawArc(PLColors.Red, -90f, 360f * p.value, false,
                style = Stroke(sw, cap = StrokeCap.Round))
        }
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Text("$totalKJ", style = PLText.OutputNumber.copy(fontSize = 26.sp),
                color = PLColors.TextPrimary)
            Text("TOTAL kJ", style = PLText.Badge.copy(fontSize = 9.sp),
                color = PLColors.TextTertiary, modifier = Modifier.padding(top = 3.dp))
        }
    }
}
```

### In-Class Metric Column

```kotlin
data class Metric(val label: String, val value: String, val unit: String = "", val color: Color)

@Composable
fun MetricColumn(metrics: List<Metric>, modifier: Modifier = Modifier) {
    Column(modifier.fillMaxWidth()) {
        metrics.forEachIndexed { i, m ->
            Row(
                Modifier.fillMaxWidth().padding(vertical = 7.dp),
                horizontalArrangement = Arrangement.SpaceBetween,
                verticalAlignment = Alignment.Bottom,
            ) {
                Text(m.label.uppercase(), style = PLText.MetricLabel, color = PLColors.TextSecondary)
                Row(verticalAlignment = Alignment.Bottom) {
                    Text(m.value, style = PLText.MetricValue, color = m.color)
                    if (m.unit.isNotEmpty())
                        Text(" ${m.unit}", style = PLText.Caption, color = PLColors.TextTertiary)
                }
            }
            if (i < metrics.lastIndex)
                Box(Modifier.fillMaxWidth().height(1.dp).background(PLColors.Divider))
        }
    }
}
```

### Real-Time Leaderboard

```kotlin
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.ui.text.style.TextAlign

data class Rider(val id: Long, val rank: Int, val name: String, val output: Int, val isMe: Boolean)

@Composable
fun Leaderboard(riders: List<Rider>, total: Int) {
    Column(
        Modifier
            .clip(RoundedCornerShape(14.dp))
            .background(PLColors.Surface1)
            .border(1.dp, PLColors.Divider, RoundedCornerShape(14.dp)),
    ) {
        Text(
            "LEADERBOARD · $total RIDERS", style = PLText.MetricLabel,
            color = PLColors.TextSecondary,
            modifier = Modifier.padding(start = 16.dp, top = 14.dp, bottom = 10.dp),
        )
        LazyColumn(Modifier.heightIn(max = 600.dp)) {
            items(riders, key = { it.id }) { r ->
                Row(
                    Modifier
                        .fillMaxWidth()
                        .animateItemPlacement(tween(250))     // live reorder
                        .background(if (r.isMe) PLColors.Red.copy(alpha = 0.12f) else Color.Transparent)
                        .padding(horizontal = 16.dp, vertical = 10.dp),
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(12.dp),
                ) {
                    Text("${r.rank}", style = PLText.LbOutput,
                        color = if (r.isMe) PLColors.RedOnDark else PLColors.TextSecondary,
                        modifier = Modifier.width(26.dp))
                    Box(Modifier.size(30.dp).clip(CircleShape)
                        .background(Brush.linearGradient(listOf(Color(0xFF5A2B82), PLColors.Red))))
                    Text(r.name, style = PLText.Body, color = Color.White, modifier = Modifier.weight(1f))
                    Text("${r.output}", style = PLText.LbOutput, color = Color.White,
                        textAlign = TextAlign.End)
                }
                Box(Modifier.fillMaxWidth().height(1.dp).background(PLColors.Divider))
            }
        }
    }
}
```

### Filter Pill (white-chip) + Primary Button

```kotlin
import androidx.compose.foundation.clickable

@Composable
fun FilterPill(title: String, selected: Boolean, onClick: () -> Unit) {
    Box(
        Modifier
            .clip(RoundedCornerShape(500.dp))
            .background(if (selected) PLColors.WhiteChip else PLColors.Surface2)
            .then(if (selected) Modifier else Modifier.border(1.dp, PLColors.Divider, RoundedCornerShape(500.dp)))
            .clickable(onClick = onClick)
            .padding(horizontal = 16.dp, vertical = 8.dp),
    ) {
        Text(title,
            style = PLText.Body.copy(fontSize = 13.sp),
            color = if (selected) Color.Black else PLColors.TextSecondary)
    }
}

@Composable
fun PLButton(
    title: String,
    variant: PLButtonVariant = PLButtonVariant.Red,
    onClick: () -> Unit,
) {
    val bg = when (variant) {
        PLButtonVariant.Red -> PLColors.Red
        PLButtonVariant.White -> Color.White
        PLButtonVariant.Outline -> Color.Transparent
    }
    Box(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(500.dp))
            .background(bg)
            .then(if (variant == PLButtonVariant.Outline)
                Modifier.border(1.5.dp, Color.White.copy(alpha = 0.4f), RoundedCornerShape(500.dp)) else Modifier)
            .clickable(onClick = onClick)
            .padding(vertical = 15.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = PLText.Button,
            color = if (variant == PLButtonVariant.White) Color.Black else Color.White)
    }
}
enum class PLButtonVariant { Red, White, Outline }
```

## 4. Navigation

Peloton has a 4-tab bottom strip with **no tint pill** — active is just white. The header is a large title or back chevron.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*

@Composable
fun PLBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = PLColors.Surface1, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Classes"  to Icons.Filled.PlayCircleFilled,
            "Schedule" to Icons.Filled.BarChart,
            "Profile"  to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = PLText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = Color.White,        // active is white
                    selectedTextColor   = Color.White,
                    unselectedIconColor = PLColors.TextTertiary,
                    unselectedTextColor = PLColors.TextTertiary,
                    indicatorColor      = Color.Transparent,  // no Material pill — Peloton has none
                ),
            )
        }
    }
}
```

The class-detail screen uses a full-bleed instructor image with a black gradient foot, the discipline eyebrow + 26sp/Black title, and a sticky red `PLButton` "Take Class" pinned above the navigation-bar inset.

## 5. Motion

Peloton motion is the LIVE pulse, the ring fill, and the leaderboard reorder; quiet elsewhere.

| Moment | Compose recipe |
|--------|----------------|
| LIVE dot pulse | `rememberInfiniteTransition` `animateFloat` 1→0.4 `infiniteRepeatable(tween(700), Reverse)` |
| Output ring fill | `Animatable` 0→progress `tween(600, LinearOutSlowInEasing)` at start; live `tween(300)` |
| Card tap | `Modifier.scale` 1→0.98 `animateFloatAsState(tween(120))` on press |
| Leaderboard reorder | `LazyColumn` items keyed by id + `Modifier.animateItemPlacement(tween(250))` |
| Pill select | `Crossfade`/`animateColorAsState(tween(150))` on the white fill |
| PR burst | `AnimatedVisibility` `scaleIn(spring(dampingRatio = 0.6f)) + fadeIn`, 1.2s, then auto-dismiss |
| Tab switch | `Crossfade(tween(150))`; glyph color tween 120ms |
| Filter sheet | `ModalBottomSheet` slide-up (Material default ~300ms) |

```kotlin
// Canonical Peloton motion — output ring filling from zero at class start
val p = remember { Animatable(0f) }
LaunchedEffect(start) { p.animateTo(target, tween(600, easing = LinearOutSlowInEasing)) }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` (selection analog) on pill select / tab change; `HapticFeedbackType.LongPress` (medium impact analog) on "Take Class"; a success pattern on PR / class complete. The LIVE pulse is silent (visual only).

## 6. Icons

Peloton ships custom metric glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Classes (tab) | `play.rectangle.fill` | `Icons.Filled.PlayCircleFilled` |
| Schedule (tab) | `chart.bar.fill` | `Icons.Filled.BarChart` |
| Profile (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Filter | `line.3.horizontal.decrease` | `Icons.Filled.Tune` |
| Play (thumbnail) | `play.fill` | `Icons.Filled.PlayArrow` |
| Rating star | `star.fill` | `Icons.Filled.Star` |
| Add to Stack | `plus.circle` | `Icons.Filled.AddCircleOutline` |
| Cadence | `metronome.fill` | `Icons.Filled.Speed` |
| Resistance | `dial.medium.fill` | `Icons.Filled.Tune` |
| Output / power | `bolt.fill` | `Icons.Filled.Bolt` |
| Heart rate | `heart.fill` | `Icons.Filled.Favorite` |
| Leaderboard | `list.number` | `Icons.Filled.FormatListNumbered` |
| PR / trophy | `trophy.fill` | `Icons.Filled.EmojiEvents` |
| Music | `music.note` | `Icons.Filled.MusicNote` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas` rings + `animateItemPlacement` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; pure-black canvas wants light-content system bars. The sticky "Take Class" pill respects the navigation-bar inset via `Modifier.navigationBarsPadding()`; the in-class metric overlay must respect all insets over the video.
- **Tabular numerals**: every metric `TextStyle` sets `fontFeatureSettings = "tnum"` so the output number, metric column, and leaderboard don't shift as values tick and rows reorder.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen/hero/section titles, body, meta. Pin layout-sensitive text (output number, metric values/labels, leaderboard, 10sp tab labels, badges) via `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: announce the LIVE badge as "Live class"; metric rows merged as "Cadence 94"; leaderboard rows as "Rank 3, You, 312 output" via `Modifier.semantics(mergeDescendants = true)`; the output `Canvas` as "Total energy 312 kilojoules".
- **Color is never the only signal**: the LIVE badge carries the word "LIVE"; metrics carry text labels beside the colored value; your leaderboard row is named "You" plus the red wash.
- **Touch targets**: Material guidance is 48.dp — class card full-card tappable (≥ 200.dp), filter pills ≥ 36.dp with ≥ 48.dp hit, leaderboard rows full-row tappable (≥ 50.dp), buttons ≥ 48.dp.
- **Contrast**: white on `#000000` is maximal; `#FF4B57` on `#000000` passes WCAG AA — never render small text in raw `#DF1E2E` on black; the white-chip pill is black-on-white (AA+).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, hold the LIVE dot solid, set the output ring to final value via `Crossfade`, drop the card scale, reorder the leaderboard instantly, and show the PR as a static badge.
- **Dark mode**: the **only** mode — never provide a light scheme; surfaces step `#000000 → #121212 → #1C1C1E`; floating layers (filter sheet, dialogs) get `#1C1C1E` + a soft `0.7` black shadow. **Do not** enable Material You `dynamicColorScheme()` — the pure-black + Peloton-Red identity and semantic metric colors must hold regardless of wallpaper.
