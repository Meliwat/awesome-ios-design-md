# Apple Fitness (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Apple Fitness's rings-on-black visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the Activity rings + Summary + Fitness+ shelf, navigation, motion, and haptics.

> Why a Compose guide for an Apple app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (true-black canvas, the three immutable ring colors on 22%-opacity tracks, grouped rounded cards, the label-opacity text ramp, cinematic Fitness+ artwork) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas` for the rings, a Material `Surface` instead of a grouped UITableView cell, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for Fitness+ artwork. Apple Fitness is **dark-first**. **Do not** enable Material You dynamic color — the three ring colors are the brand and must never be re-themed to the wallpaper.

## 1. Color Tokens

```kotlin
// ui/theme/AFColors.kt
import androidx.compose.ui.graphics.Color

object AFColors {
    // The three Activity rings (the brand — immutable, theme-invariant)
    val Move       = Color(0xFFFA114F)
    val MoveLabel  = Color(0xFFFF375F)   // brightened on dark for text
    val Exercise   = Color(0xFF92E82A)
    val ExerciseHi = Color(0xFF66FF00)
    val Stand      = Color(0xFF1EE4E1)
    val StandHi    = Color(0xFF00F0FF)

    // Ring tracks = ring color @ 22% opacity
    val MoveTrack     = Move.copy(alpha = 0.22f)
    val ExerciseTrack = Exercise.copy(alpha = 0.22f)
    val StandTrack    = Stand.copy(alpha = 0.22f)

    // Chrome accent (single)
    val Accent        = Color(0xFFFF375F)
    val AccentPressed = Color(0xFFD80E45)
    val FitnessPlus   = Color(0xFFC969E0)

    // Surfaces (dark — primary)
    val Canvas    = Color(0xFF000000)
    val Grouped1  = Color(0xFF1C1C1E)
    val Grouped2  = Color(0xFF2C2C2E)
    val Grouped3  = Color(0xFF3A3A3C)
    val Separator = Color(0xFF38383A)
    val Fill      = Color(0xFF767680).copy(alpha = 0.24f)

    // Surfaces (light — tablet / system light)
    val LightCanvas    = Color(0xFFF2F2F7)
    val LightSurface   = Color(0xFFFFFFFF)
    val LightSeparator = Color(0xFFC6C6C8)

    // Text — Apple label-opacity ramp (white-on-dark)
    val LabelPrimary   = Color(0xFFFFFFFF)
    val LabelSecondary = Color(0xFFEBEBF5).copy(alpha = 0.60f)
    val LabelTertiary  = Color(0xFFEBEBF5).copy(alpha = 0.30f)

    // Semantic
    val Success   = Color(0xFF30D158)
    val Error     = Color(0xFFFF453A)
    val AwardGold = Color(0xFFFFD60A)
}
```

Wire it into a dark-first scheme. The ring colors live outside the M3 scheme on purpose — they are brand constants, not theme roles.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val AFDark = darkColorScheme(
    primary        = AFColors.Accent,        // Move-pink chrome accent
    onPrimary      = AFColors.LabelPrimary,
    background     = AFColors.Canvas,
    onBackground   = AFColors.LabelPrimary,
    surface        = AFColors.Grouped1,
    onSurface      = AFColors.LabelPrimary,
    surfaceVariant = AFColors.Grouped2,
    outline        = AFColors.Separator,
    error          = AFColors.Error,
)

private val AFLight = lightColorScheme(
    primary        = AFColors.Accent,
    onPrimary      = AFColors.LabelPrimary,
    background     = AFColors.LightCanvas,
    onBackground   = Color(0xFF000000),
    surface        = AFColors.LightSurface,
    onSurface      = Color(0xFF000000),
    surfaceVariant = Color(0xFFE5E5EA),
    outline        = AFColors.LightSeparator,
    error          = AFColors.Error,
)

@Composable
fun AFTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) AFDark else AFLight,   // dark is the intended default (phone)
    typography  = AFTypography,
    content     = content,
)
```

## 2. Typography

Apple Fitness uses SF Pro (iOS system). On Android the closest substitute is **Inter** (SIL OFL) — drop the TTFs in `res/font/`. Follow Apple's text-style ramp; numerals tabular; secondary/tertiary text via the label-opacity colors above.

```kotlin
// ui/theme/AFType.kt
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

private const val TNUM = "tnum"   // tabular figures

object AFText {
    val LargeTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 40.sp, lineHeight = 44.sp, letterSpacing = (-1.0).sp)
    val Date       = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.6).sp)
    val Header     = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 30.sp, letterSpacing = (-0.5).sp)
    val Section    = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.4).sp)
    val Title3     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 20.sp, lineHeight = 25.sp, letterSpacing = (-0.3).sp)
    val Body       = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 17.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val BodyReg    = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 17.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val RingValue  = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 19.sp, lineHeight = 21.sp, fontFeatureSettings = TNUM)
    val TileValue  = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 24.sp, fontFeatureSettings = TNUM)
    val CardTitle  = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val Footnote   = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val Eyebrow    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 15.sp, letterSpacing = 0.4.sp)
    val Button     = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 17.sp, lineHeight = 17.sp, letterSpacing = (-0.2).sp)
    val Tab        = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp)
    val Badge      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.5.sp)
}

val AFTypography = Typography(
    headlineLarge = AFText.LargeTitle,
    titleLarge    = AFText.Section,
    bodyLarge     = AFText.Body,
    labelSmall    = AFText.Tab,
)
```

## 3. Signature Components

### Activity Rings (Canvas)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.Canvas
import androidx.compose.foundation.layout.size
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp

@Composable
fun ActivityRings(
    move: Float, exercise: Float, stand: Float,
    size: Dp = 130.dp, lineWidth: Dp = 14.dp,
) {
    val m = remember { Animatable(0f) }
    val e = remember { Animatable(0f) }
    val s = remember { Animatable(0f) }
    LaunchedEffect(Unit) {
        val spec = tween<Float>(1000, easing = LinearOutSlowInEasing)
        launch { m.animateTo(move.coerceAtMost(1f), spec) }
        launch { delay(80);  e.animateTo(exercise.coerceAtMost(1f), spec) }
        launch { delay(160); s.animateTo(stand.coerceAtMost(1f), spec) }
    }

    Canvas(Modifier.size(size)) {
        val sw = lineWidth.toPx()
        val gap = sw + 3.dp.toPx()
        fun ring(progress: Float, color: Color, track: Color, inset: Float) {
            val d = this.size.minDimension - sw - inset * 2
            val topLeft = Offset(sw / 2 + inset, sw / 2 + inset)
            val arc = Size(d, d)
            drawArc(track, 0f, 360f, false, topLeft, arc, style = Stroke(sw))
            drawArc(color, -90f, 360f * progress, false, topLeft, arc,
                style = Stroke(sw, cap = StrokeCap.Round))
        }
        ring(m.value, AFColors.Move,     AFColors.MoveTrack,     0f)
        ring(e.value, AFColors.Exercise, AFColors.ExerciseTrack, gap)
        ring(s.value, AFColors.Stand,    AFColors.StandTrack,    gap * 2)
    }
}
```

### Ring Legend + Hero Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.ui.draw.clip
import androidx.compose.ui.Alignment

private data class Leg(val name: String, val value: String, val goal: String, val color: Color)

@Composable
fun RingHeroCard() {
    val legs = listOf(
        Leg("Move", "486", "/620 KCAL", AFColors.MoveLabel),
        Leg("Exercise", "38", "/30 MIN", AFColors.Exercise),
        Leg("Stand", "9", "/12 HR", AFColors.Stand),
    )
    Row(
        Modifier
            .padding(horizontal = 16.dp)
            .clip(RoundedCornerShape(18.dp))
            .background(AFColors.Grouped1)
            .padding(22.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(20.dp),
    ) {
        ActivityRings(move = 0.80f, exercise = 1.27f, stand = 0.75f, size = 130.dp)
        Column(verticalArrangement = Arrangement.spacedBy(14.dp), modifier = Modifier.weight(1f)) {
            legs.forEach { l ->
                Column {
                    Text(l.name.uppercase(), style = AFText.Eyebrow, color = l.color)
                    Row(verticalAlignment = Alignment.Bottom) {
                        Text(l.value, style = AFText.RingValue, color = AFColors.LabelPrimary)
                        Text(" ${l.goal}", style = AFText.Footnote.copy(fontSize = 12.sp),
                            color = AFColors.LabelSecondary)
                    }
                }
            }
        }
    }
}
```

### Metric Tile

```kotlin
import androidx.compose.material3.Icon
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun MetricTile(
    icon: ImageVector, tint: Color, name: String,
    value: String, unit: String = "", sub: String,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .clip(RoundedCornerShape(14.dp))
            .background(AFColors.Grouped1)
            .padding(14.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Icon(icon, null, tint = tint, modifier = Modifier.size(16.dp))
            Text(name, style = AFText.Footnote.copy(fontSize = 12.sp), color = AFColors.LabelSecondary)
        }
        Row(verticalAlignment = Alignment.Bottom, modifier = Modifier.padding(top = 8.dp)) {
            Text(value, style = AFText.TileValue, color = AFColors.LabelPrimary)
            if (unit.isNotEmpty())
                Text(" $unit", style = AFText.Footnote, color = AFColors.LabelSecondary)
        }
        Text(sub, style = AFText.Footnote, color = AFColors.LabelTertiary,
            modifier = Modifier.padding(top = 2.dp))
    }
}
```

### Fitness+ Card

```kotlin
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.ui.graphics.Brush

@Composable
fun FitnessPlusCard(
    badge: String, type: String, title: String, meta: String,
    gradient: List<Color>,
) {
    Column(Modifier.width(168.dp)) {
        Box(
            Modifier
                .width(168.dp).height(200.dp)
                .clip(RoundedCornerShape(16.dp))
                .background(Brush.linearGradient(gradient)),
        ) {
            // Android has no live blur — translucent dark scrim approximates the frosted badge
            Text(
                badge.uppercase(), style = AFText.Badge, color = Color.White,
                modifier = Modifier
                    .align(Alignment.TopStart).padding(12.dp)
                    .clip(RoundedCornerShape(6.dp))
                    .background(Color.Black.copy(alpha = 0.40f))
                    .padding(horizontal = 8.dp, vertical = 4.dp),
            )
            Box(
                Modifier
                    .align(Alignment.BottomStart).padding(14.dp)
                    .size(34.dp).clip(CircleShape)
                    .background(Color.White.copy(alpha = 0.22f)),
                contentAlignment = Alignment.Center,
            ) {
                Icon(Icons.Filled.PlayArrow, null, tint = Color.White, modifier = Modifier.size(18.dp))
            }
        }
        Text(type.uppercase(), style = AFText.Eyebrow, color = AFColors.MoveLabel,
            modifier = Modifier.padding(top = 10.dp))
        Text(title, style = AFText.CardTitle, color = AFColors.LabelPrimary,
            modifier = Modifier.padding(top = 3.dp))
        Text(meta, style = AFText.Footnote, color = AFColors.LabelSecondary,
            modifier = Modifier.padding(top = 2.dp))
    }
}
```

### Buttons

```kotlin
import androidx.compose.foundation.clickable

@Composable
fun AFPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Box(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(14.dp))
            .background(AFColors.Move)
            .clickable(onClick = onClick)
            .padding(vertical = 15.dp),
        contentAlignment = Alignment.Center,
    ) { Text(title, style = AFText.Button, color = Color.White) }
}

@Composable
fun AFTintedButton(title: String, onClick: () -> Unit) {
    Box(
        Modifier
            .clip(RoundedCornerShape(12.dp))
            .background(AFColors.Move.copy(alpha = 0.18f))
            .clickable(onClick = onClick)
            .padding(horizontal = 22.dp, vertical = 12.dp),
    ) { Text(title, style = AFText.Button.copy(fontSize = 15.sp), color = AFColors.Accent) }
}
```

## 4. Navigation

Apple Fitness has a 3-tab bottom strip with **no tint pill** — active is the Move-pink accent only. The header is a day eyebrow + large date + avatar.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*

@Composable
fun AFBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = AFColors.Grouped1, tonalElevation = 0.dp) {
        val items = listOf(
            "Summary"  to Icons.Filled.RadioButtonChecked,
            "Fitness+" to Icons.Filled.PlayCircleFilled,
            "Sharing"  to Icons.Filled.Group,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = AFText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = AFColors.Accent,    // Move-pink, no pill
                    selectedTextColor   = AFColors.Accent,
                    unselectedIconColor = AFColors.LabelTertiary,
                    unselectedTextColor = AFColors.LabelTertiary,
                    indicatorColor      = Color.Transparent,  // no Material pill — Apple uses tint only
                ),
            )
        }
    }
}
```

The Summary header is a `Column` with a day eyebrow (`AFText.Eyebrow` in `MoveLabel`), the date (`AFText.Date`), and a trailing 32.dp avatar; the "Summary" large title collapses with a `TopAppBar` + nested scroll. The Fitness+ shelf is a `LazyRow` of `FitnessPlusCard`s with `snapFlingBehavior`.

## 5. Motion

Apple Fitness motion: the rings carry the energy; chrome stays calm. 200–1000ms ease-out.

| Moment | Compose recipe |
|--------|----------------|
| Rings sweep from 0 | three `Animatable` 0→value `tween(1000, LinearOutSlowInEasing)`, staggered 80ms (Move→Exercise→Stand) |
| Ring close | particle burst (a `Canvas`/`InfiniteTransition` emitter in the ring color) + success haptic at progress ≥ 1 |
| All rings closed | larger celebration + stronger haptic |
| Card press | `Modifier.scale` via `animateFloatAsState(spring(dampingRatio = 0.7f))` 1→0.97 on press |
| Tab switch | `Crossfade(tween(200))`; glyph tint tween 150ms |
| Fitness+ shelf | `LazyRow` + `rememberSnapFlingBehavior`; parallax via `lazyListState` item offset |
| Sheet present | `ModalBottomSheet` slide-up (Material default ~300ms) + scrim |
| Live HUD (in-workout) | rings `animateFloatAsState(tween(300))` as metrics change |
| Award flip | `graphicsLayer { rotationY }` `animateFloatAsState(tween(500))` |

```kotlin
// Canonical Apple Fitness motion — staggered ring sweep
LaunchedEffect(Unit) {
    val spec = tween<Float>(1000, easing = LinearOutSlowInEasing)
    launch { move.animateTo(targetMove, spec) }
    launch { delay(80);  exercise.animateTo(targetExercise, spec) }
    launch { delay(160); stand.animateTo(targetStand, spec) }
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` (selection analog) on tab/segment change; a success pattern on ring close / all-rings-closed; `HapticFeedbackType.LongPress` (medium impact analog) on Start Workout. Reduce motion replaces the sweep with an instant final value via `Crossfade`.

## 6. Icons

Apple Fitness uses SF Symbols; the closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Summary (tab) | `circle.circle.fill` | `Icons.Filled.RadioButtonChecked` |
| Fitness+ (tab) | `play.rectangle.fill` | `Icons.Filled.PlayCircleFilled` |
| Sharing (tab) | `person.2.fill` | `Icons.Filled.Group` |
| Steps | `figure.walk` | `Icons.Filled.DirectionsWalk` |
| Distance | (curved path) | `Icons.Filled.Straighten` |
| Heart Rate | `heart.fill` | `Icons.Filled.Favorite` |
| Workouts | `figure.run` | `Icons.Filled.DirectionsRun` |
| Play (card) | `play.fill` | `Icons.Filled.PlayArrow` |
| Move detail | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Exercise detail | `figure.run` | `Icons.Filled.DirectionsRun` |
| Stand detail | `figure.stand` | `Icons.Filled.Accessibility` |
| Award | `medal.fill` | `Icons.Filled.EmojiEvents` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| See All | `chevron.right` | `Icons.Filled.ChevronRight` |
| Trends up / down | `arrow.up.right` / `arrow.down.right` | `Icons.Filled.TrendingUp` / `Icons.Filled.TrendingDown` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas` rings + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; true-black canvas wants light-content system bars in dark mode. The bottom bar respects the navigation inset; the in-workout HUD must respect all insets over video.
- **Tabular numerals**: every numeric `TextStyle` sets `fontFeatureSettings = "tnum"` so the ring legend, metric tiles, and live HUD don't jitter.
- **No live blur on Android**: Apple's frosted badges/play buttons and blurred tab bar are approximated with translucent scrims (`Color.Black.copy(alpha = 0.40f)` / `Color.White.copy(alpha = 0.22f)`) or a `RenderEffect.createBlurEffect` on API 31+; provide the scrim fallback below 31 and when reduce-transparency is requested.
- **Font scaling**: `sp` honors the user's font scale — keep it on titles/body/footnotes. Pin layout-sensitive text (ring legend numerals, in-workout HUD, 10sp tab labels, frosted badges) via `dp` or a fixed-density `CompositionLocalProvider`; ring `size` scales with container width, not font scale.
- **TalkBack**: announce rings via `Modifier.semantics(mergeDescendants = true) { contentDescription = "Move ring, 486 of 620 kilocalories, 78 percent" }`; metric tiles "Steps, 8214, average 7540"; Fitness+ cards "20 minute HIIT with Bakari, Pop Anthems playlist".
- **Color is never the only signal**: ring legend rows carry the text label + value beside the colored ring; rings are also distinguishable by radius/position; trend arrows pair color with an up/down glyph.
- **Touch targets**: Material guidance is 48.dp — metric tiles full-tile tappable (≥ 72.dp), Fitness+ cards full-card (≥ 280.dp), buttons ≥ 48.dp, tab icons 24.dp glyph in the 56.dp bar.
- **Contrast**: white on `#000000` is maximal; `#FF375F` on `#000000` passes WCAG AA for the accent; keep ring *labels* at the brightened variants for text contrast (raw `#FA114F` text on black is borderline).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, set the rings to their final value via `Crossfade`, drop the particle burst (static "closed" check), no card scale, no shelf parallax, awards crossfade instead of 3D flip.
- **Dark mode**: dark is the default (phone); surfaces step `#000000 → #1C1C1E → #2C2C2E`; light is allowed on tablet via the M3 light scheme. **The three ring colors and their 22%-opacity tracks NEVER change between modes — they are the brand.** **Do not** enable Material You `dynamicColorScheme()` — the rings must never re-theme to the wallpaper.
