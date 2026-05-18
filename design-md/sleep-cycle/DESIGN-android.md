# Sleep Cycle (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Sleep Cycle's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the signature hypnogram + score ring as `@Composable`s, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This keeps the *visual* identity (the indigo→night gradient shell, the glowing aqua hypnogram, the single aqua accent, soft floating cards) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `Canvas`/`drawPath` instead of SwiftUI `Path`, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3. No color extraction — Sleep Cycle's palette is a fixed, intentional set, so Palette is not needed. Sleep Cycle is **dark-first**; a Dawn light scheme is provided but the night gradient is the primary experience.

## 1. Color Tokens

```kotlin
// ui/theme/SleepCycleColors.kt
import androidx.compose.ui.graphics.Color

object SCColors {
    // Night gradient & canvas (dark, default)
    val NightTop   = Color(0xFF2A2D5A)
    val NightMid   = Color(0xFF3B4371)
    val CanvasDeep = Color(0xFF14152E) // NOT pure black
    val Surface1   = Color(0xFF1E2046)
    val Surface2   = Color(0xFF292C58)
    val Divider    = Color(0xFF353974)

    // Canvas (light — Dawn)
    val DawnCanvas  = Color(0xFFF4F5FC)
    val DawnSurface = Color(0xFFFFFFFF)
    val DawnDivider = Color(0xFFE3E5F2)

    // Text
    val TextPrimary   = Color(0xFFF2F3FB)
    val TextSecondary = Color(0xFFA6A9D4)
    val TextTertiary  = Color(0xFF6C70A8)
    val TextPrimaryLt = Color(0xFF1B1D3A)

    // Accents
    val Accent        = Color(0xFF6C7BFF)
    val AccentPressed = Color(0xFF5563E6)
    val DeepSleep     = Color(0xFF3D4ABF)
    val Aqua          = Color(0xFF4FD1E6)
    val AquaSoft      = Color(0xFF7FE3F0)

    // Sleep stages (semantic)
    val StageAwake = Color(0xFFFF8FB1)
    val StageLight = Color(0xFF6C7BFF)
    val StageDeep  = Color(0xFF3D4ABF)
    val StageREM   = Color(0xFF4FD1E6)

    // Semantic
    val Success = Color(0xFF4FD1A0)
    val Error   = Color(0xFFFF6B81)
    val Gold    = Color(0xFFFFC56B)
}

fun scoreColor(score: Int): Color = when {
    score < 50 -> SCColors.StageAwake // rose
    score < 75 -> SCColors.Accent     // indigo
    else       -> SCColors.Aqua       // aqua
}
```

Wire it into both schemes. Sleep Cycle is dark-first; the dark scheme is the signature.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val SCDark = darkColorScheme(
    primary        = SCColors.Accent,
    onPrimary      = Color(0xFFFFFFFF),
    background     = SCColors.CanvasDeep,
    onBackground   = SCColors.TextPrimary,
    surface        = SCColors.Surface1,
    onSurface      = SCColors.TextPrimary,
    surfaceVariant = SCColors.Surface2,
    outline        = SCColors.Divider,
    secondary      = SCColors.Aqua,
    error          = SCColors.Error,
)

private val SCLight = lightColorScheme(
    primary        = SCColors.Accent,
    onPrimary      = Color(0xFFFFFFFF),
    background     = SCColors.DawnCanvas,
    onBackground   = SCColors.TextPrimaryLt,
    surface        = SCColors.DawnSurface,
    onSurface      = SCColors.TextPrimaryLt,
    surfaceVariant = SCColors.DawnDivider,
    outline        = SCColors.DawnDivider,
    secondary      = SCColors.Aqua,
    error          = SCColors.Error,
)

@Composable
fun SleepCycleTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) SCDark else SCLight,
    typography = SCTypography,
    content = content,
)
```

## 2. Typography (M3)

Sleep Cycle uses **Nunito** (Google Fonts, SIL OFL). Drop the TTFs in `res/font/`. Numerals heavy (ExtraBold/Black); body light (Normal).

```kotlin
// ui/theme/SCType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Nunito = FontFamily(
    Font(R.font.nunito_regular,   FontWeight.Normal),
    Font(R.font.nunito_medium,    FontWeight.Medium),
    Font(R.font.nunito_semibold,  FontWeight.SemiBold),
    Font(R.font.nunito_bold,      FontWeight.Bold),
    Font(R.font.nunito_extrabold, FontWeight.ExtraBold),
    Font(R.font.nunito_black,     FontWeight.Black),
)

object SCText {
    val ScreenTitle = TextStyle(Nunito, fontWeight = FontWeight.Black,     fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val HeroStat    = TextStyle(Nunito, fontWeight = FontWeight.Black,     fontSize = 34.sp, lineHeight = 37.sp, letterSpacing = (-0.5).sp)
    val Section     = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val CardTitle   = TextStyle(Nunito, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp)
    val Body        = TextStyle(Nunito, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val ListItem    = TextStyle(Nunito, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 21.sp)
    val Meta        = TextStyle(Nunito, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val Eyebrow     = TextStyle(Nunito, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 1.4.sp)
    val Button      = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Pill        = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
    val Axis        = TextStyle(Nunito, fontWeight = FontWeight.Bold,      fontSize = 9.sp,  lineHeight = 9.sp)
    val Tab         = TextStyle(Nunito, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

val SCTypography = Typography(
    headlineLarge = SCText.ScreenTitle,
    headlineMedium = SCText.Section,
    titleMedium   = SCText.CardTitle,
    bodyMedium    = SCText.Body,
    labelSmall    = SCText.Tab,
)
```

## 3. Signature Components

### Night Shell (gradient background)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush

@Composable
fun NightShell(content: @Composable BoxScope.() -> Unit) {
    Box(
        Modifier
            .fillMaxSize()
            .background(
                Brush.verticalGradient(
                    0.0f to SCColors.NightTop,
                    0.3f to SCColors.NightMid,
                    1.0f to SCColors.CanvasDeep,
                )
            ),
        content = content,
    )
}
```

### Sleep-Analysis Graph (Hypnogram)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.Canvas
import androidx.compose.runtime.*
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.*
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.unit.dp

/** points: normalized x:0..1 (time), y:0..1 (0=awake/top, 1=deep/bottom) */
@Composable
fun HypnogramCard(points: List<Offset>, modifier: Modifier = Modifier) {
    val progress = remember { Animatable(0f) }
    LaunchedEffect(Unit) {
        progress.animateTo(1f, tween(900, easing = EaseOutCubic))
    }

    Column(
        modifier
            .fillMaxWidth()
            .shadow(24.dp, RoundedCornerShape(20.dp), spotColor = Color.Black.copy(alpha = 0.35f))
            .clip(RoundedCornerShape(20.dp))
            .background(SCColors.Surface1)
            .border(1.dp, SCColors.Divider, RoundedCornerShape(20.dp))
            .padding(16.dp),
    ) {
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
            Text("Sleep stages", style = SCText.CardTitle.copy(fontSize = 13.sp), color = SCColors.TextPrimary)
            Text("23:42 — 07:18", style = SCText.Axis.copy(fontSize = 11.sp), color = SCColors.TextTertiary)
        }
        Spacer(Modifier.height(12.dp))

        Canvas(Modifier.fillMaxWidth().height(110.dp)) {
            val w = size.width; val h = size.height
            fun pt(o: Offset) = Offset(o.x * w, 8.dp.toPx() + o.y * (h - 16.dp.toPx()))

            val line = Path().apply {
                moveTo(pt(points.first()).x, pt(points.first()).y)
                for (i in 1 until points.size) {
                    val prev = pt(points[i - 1]); val cur = pt(points[i])
                    val mid = Offset((prev.x + cur.x) / 2, (prev.y + cur.y) / 2)
                    quadraticBezierTo(prev.x, prev.y, mid.x, mid.y)
                    quadraticBezierTo(cur.x, cur.y, cur.x, cur.y)
                }
            }
            // Gradient fill under the wave
            val fill = Path().apply {
                addPath(line); lineTo(w, h); lineTo(0f, h); close()
            }
            drawPath(fill, Brush.verticalGradient(
                0f to SCColors.Accent.copy(alpha = 0.45f),
                1f to SCColors.Accent.copy(alpha = 0.02f),
            ), alpha = progress.value)
            // Glowing aqua wave — clip-reveal left→right
            clipRect(right = w * progress.value) {
                drawPath(line, SCColors.AquaSoft, style = Stroke(width = 2.5.dp.toPx(), cap = StrokeCap.Round, join = StrokeJoin.Round))
            }
        }

        Spacer(Modifier.height(6.dp))
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
            listOf("00", "02", "04", "06", "07").forEach { Text(it, style = SCText.Axis, color = SCColors.TextTertiary) }
        }
        Spacer(Modifier.height(12.dp))
        Row(horizontalArrangement = Arrangement.spacedBy(14.dp)) {
            listOf("Awake" to SCColors.StageAwake, "Light" to SCColors.StageLight,
                   "Deep" to SCColors.StageDeep, "REM" to SCColors.StageREM).forEach { (label, c) ->
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                    Box(Modifier.size(9.dp).clip(RoundedCornerShape(3.dp)).background(c))
                    Text(label, style = SCText.Tab, color = SCColors.TextSecondary)
                }
            }
        }
    }
}
```

### Sleep-Quality Ring

```kotlin
@Composable
fun ScoreRing(score: Int, size: androidx.compose.ui.unit.Dp = 92.dp) {
    val progress = remember { Animatable(0f) }
    LaunchedEffect(score) { progress.animateTo(1f, tween(800, easing = EaseOutCubic)) }
    val arcColor = scoreColor(score)

    Box(Modifier.size(size), contentAlignment = Alignment.Center) {
        Canvas(Modifier.fillMaxSize()) {
            val sw = 9.dp.toPx()
            val d = size.toPx() - sw
            drawArc(SCColors.Surface2, 0f, 360f, false,
                topLeft = Offset(sw / 2, sw / 2), size = androidx.compose.ui.geometry.Size(d, d),
                style = Stroke(sw))
            drawArc(arcColor, -90f, 360f * progress.value, false,
                topLeft = Offset(sw / 2, sw / 2), size = androidx.compose.ui.geometry.Size(d, d),
                style = Stroke(sw, cap = StrokeCap.Round))
        }
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Text("${(score * progress.value).toInt()}", style = SCText.HeroStat.copy(fontSize = 26.sp), color = SCColors.TextPrimary)
            Text("SLEEP QUALITY", style = SCText.Tab.copy(fontSize = 9.sp), color = SCColors.TextSecondary)
        }
    }
}
```

### Smart-Alarm Card

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Alarm
import androidx.compose.material3.Icon

@Composable
fun SmartAlarmCard(time: String = "07:30", modifier: Modifier = Modifier) {
    Row(
        modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(18.dp))
            .background(SCColors.Surface1)
            .border(1.dp, SCColors.Divider, RoundedCornerShape(18.dp))
            .padding(14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Box(
            Modifier.size(38.dp).clip(RoundedCornerShape(12.dp))
                .background(Brush.linearGradient(listOf(SCColors.Accent, SCColors.DeepSleep))),
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Alarm, null, tint = Color.White, modifier = Modifier.size(20.dp)) }
        Column(Modifier.weight(1f)) {
            Text("Smart alarm", style = SCText.ListItem.copy(fontWeight = FontWeight.ExtraBold), color = SCColors.TextPrimary)
            Text("Wakes you in your lightest sleep", style = SCText.Tab.copy(fontSize = 11.sp), color = SCColors.TextSecondary)
        }
        Text(time, style = SCText.HeroStat.copy(fontSize = 19.sp), color = SCColors.TextPrimary)
    }
}
```

### Primary Button

```kotlin
@Composable
fun SCPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Box(
        modifier
            .fillMaxWidth()
            .shadow(20.dp, RoundedCornerShape(16.dp), spotColor = SCColors.Accent.copy(alpha = 0.35f))
            .clip(RoundedCornerShape(16.dp))
            .background(Brush.linearGradient(listOf(SCColors.Accent, SCColors.DeepSleep)))
            .clickable(onClick = onClick)
            .padding(vertical = 15.dp, horizontal = 30.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(title, style = SCText.Button, color = Color.White)
    }
}
```

## 4. Navigation

Sleep Cycle has minimal chrome: a 5-tab bottom strip and no opaque top bar (the title lives in scroll content). Model the strip as a `NavigationBar`; active state is just the aqua color — **no Material tint pill**.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun SCBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = SCColors.CanvasDeep.copy(alpha = 0.92f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Sleep"   to Icons.Filled.MonitorHeart,
            "Journal" to Icons.Filled.CalendarMonth,
            "Alarm"   to Icons.Filled.Alarm,
            "Sounds"  to Icons.Filled.QueueMusic,
            "Profile" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, Modifier.size(22.dp)) },
                label = { Text(label, style = SCText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = SCColors.Aqua,    // aqua, no pill
                    selectedTextColor = SCColors.Aqua,
                    unselectedIconColor = SCColors.TextTertiary,
                    unselectedTextColor = SCColors.TextTertiary,
                    indicatorColor = Color.Transparent,   // Sleep Cycle has no tint pill
                ),
            )
        }
    }
}
```

The screen header is content, not a `TopAppBar`: an eyebrow ("SLEEP ANALYSIS", `SCText.Eyebrow` in `Aqua`, 1.4sp tracking, uppercase), the title ("Last night", `SCText.ScreenTitle`), and a date line (`SCText.Meta` in `TextSecondary`), all over the night gradient.

## 5. Motion

Sleep Cycle motion is slow and calming — 200–900ms ease-out, never aggressive. The primary button glows (colored shadow), it does not bounce.

| Moment | Compose recipe |
|--------|----------------|
| Hypnogram draw-in | `Animatable` 0→1 `tween(900, EaseOutCubic)`; `clipRect(right = w * progress)` reveal + fill `alpha = progress` |
| Score ring sweep | `drawArc(sweepAngle = 360f * progress)` with `Animatable` `tween(800, EaseOutCubic)`; center number counts via `(score * progress).toInt()` |
| Weekly bars grow | each bar `animateFloatAsState` with `tween(500)` + staggered `delayMillis = i * 40` |
| Segmented control | indicator `animateDpAsState(tween(220, EaseOutCubic))` slides between segments |
| Sheet present | `ModalBottomSheet` default slide-up (~300ms); scrim fades in |
| Toggle | thumb `animateDpAsState(tween(200))` + `HapticFeedbackType.LongPress` |
| Tab switch | `Crossfade(tween(200))` between destinations |
| Session start | moon `infiniteRepeatable(tween(1200), RepeatMode.Reverse)` scale 1→1.06, then ramp screen brightness down |

```kotlin
// Canonical hypnogram reveal
val progress = remember { Animatable(0f) }
LaunchedEffect(Unit) { progress.animateTo(1f, tween(900, easing = EaseOutCubic)) }
// in Canvas: clipRect(right = size.width * progress.value) { drawPath(wave, ...) }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on toggle and session start. Session auto-dim uses `window.attributes.screenBrightness` — request to lower it during tracking, never below `0.05f`, and **always restore** the prior value on session end and `onDispose`.

## 6. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Sleep (tab) | `waveform.path.ecg` | `Icons.Filled.MonitorHeart` |
| Journal (tab) | `calendar` | `Icons.Filled.CalendarMonth` |
| Alarm (tab) | `alarm` / `alarm.fill` | `Icons.Filled.Alarm` |
| Sounds (tab) | `music.note.list` | `Icons.Filled.QueueMusic` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Smart alarm tile | `alarm` | `Icons.Filled.Alarm` |
| Moon / session | `moon.stars.fill` | `Icons.Filled.Bedtime` |
| Play / pause | `play.fill` / `pause.fill` | `Icons.Filled.PlayArrow` / `Icons.Filled.Pause` |
| Trend up | `arrow.up.right` | `Icons.Filled.TrendingUp` |
| Streak | `flame.fill` | `Icons.Filled.LocalFireDepartment` |
| Settings | `gearshape` | `Icons.Filled.Settings` |
| Back / close | `chevron.left` / `xmark` | `Icons.Filled.ArrowBack` / `Icons.Filled.Close` |
| Snooze | `zzz` | `Icons.Filled.Snooze` |
| Heart rate | `heart.fill` | `Icons.Filled.Favorite` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `Canvas` paths + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the night gradient must extend under the status bar and nav bar — use light-content system bars on dark (the default), dark-content on the Dawn theme. Add bottom content padding for the `NavigationBar` height + system insets.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen title, hero stat, section, card title, body, list item; pin layout-sensitive text (axis labels, 10sp tab labels, pill text, ring sublabel) via `dp`-derived sizing or a fixed-`fontScale` `LocalDensity`. Cap the hero stat scale so the ring/stat row doesn't break.
- **TalkBack**: expose the hypnogram and score ring as single elements with a summarizing `contentDescription` ("Slept 7 hours 36 minutes, sleep quality 78 of 100, 2 awakenings") instead of the raw path; mark the legend swatches `Modifier.semantics { hideFromAccessibility() }` and read the stage names in the summary.
- **Touch targets**: Material guidance is 48.dp — give 22.dp tab icons and 14.dp chevrons a 48.dp hit area; primary button ≥ 50.dp tall; toggle uses Material `Switch` sizing.
- **Contrast**: `#F2F3FB` on `#1E2046` and on the gradient passes WCAG AA. Stage chips pair fills with dark ink (Awake on `#3A1430`, REM on `#07303A`) — validate any custom pairing, especially the rose Awake.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, render the hypnogram and ring at final value (no draw-in/sweep), substitute a `Crossfade`, and disable the moon pulse — keep the score color band (it conveys state).
- **Dark mode**: invert via the Dawn `lightColorScheme` — `#F4F5FC` field, white cards; accent `#6C7BFF`, aqua `#4FD1E6`, and all stage colors stay identical in both modes. Do **not** enable Material You `dynamicColorScheme()` — Sleep Cycle's calm indigo identity must hold regardless of wallpaper (the single aqua accent has no wallpaper to harmonize with).
- **Brightness**: session auto-dim via `window.attributes.screenBrightness` — gate behind a user setting, never below `0.05f`, always restore on session end and `DisposableEffect.onDispose`.
