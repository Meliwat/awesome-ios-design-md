# ESPN (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports ESPN's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the scores ticker, the game card, the pulsing LIVE badge, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (near-black canvas — not pure black, single ESPN Red accent, the pulsing LIVE badge, the green winning score, tabular score numerals) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyRow` for the ticker, `infiniteRepeatable` for the pulse, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for team logos and story thumbnails. No color extraction — ESPN's palette is a fixed dark+red system, so Palette is not needed. ESPN's scoreboard identity is **dark-first**; pure black breaks card contrast.

## 1. Color Tokens

```kotlin
// ui/theme/ESPNColors.kt
import androidx.compose.ui.graphics.Color

object ESPNColors {
    // Canvas & Surfaces (dark-first, near-black — NOT pure black)
    val Canvas   = Color(0xFF0E0F11)
    val Surface1 = Color(0xFF18191B) // game-card body
    val Surface2 = Color(0xFF212327) // ticker chip / card header
    val Surface3 = Color(0xFF2B2D31) // pressed / FINAL fill
    val Divider  = Color(0xFF2A2C30)

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF9AA0A6)
    val TextTertiary  = Color(0xFF6B7075)

    // Brand & status
    val Red        = Color(0xFFD50A0A) // brand accent
    val RedBright  = Color(0xFFCC0000) // CTA / links / category tags
    val RedPressed = Color(0xFFA50808)
    val Live       = Color(0xFFFF1A1A) // LIVE pill + pulse
    val Win        = Color(0xFF1FAA59) // winning team's score
    val RankGold   = Color(0xFFF2C200)
    val Error      = Color(0xFFE5484D)
}
```

Wire it into a dark-first scheme. ESPN's scoreboard ground is near-black — never pure black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.runtime.Composable

private val ESPNScheme = darkColorScheme(
    primary        = ESPNColors.Red,            // the single brand accent
    onPrimary      = ESPNColors.TextPrimary,
    background     = ESPNColors.Canvas,
    onBackground   = ESPNColors.TextPrimary,
    surface        = ESPNColors.Surface1,
    onSurface      = ESPNColors.TextPrimary,
    surfaceVariant = ESPNColors.Surface2,
    outline        = ESPNColors.Divider,
    error          = ESPNColors.Error,
)

@Composable
fun ESPNTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = ESPNScheme,   // scoreboard identity is dark — don't branch on isSystemInDarkTheme()
        typography  = ESPNTypography,
        content     = content,
    )
```

## 2. Typography

ESPN's brand face is a proprietary condensed sans; the closest free analog is **Archivo** (SIL OFL — free to ship). Drop the TTFs in `res/font/`. Scores/clocks **must** be tabular — use `TextStyle(... , fontFeatureSettings = "tnum")`.

```kotlin
// ui/theme/ESPNType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Archivo = FontFamily(
    Font(R.font.archivo_regular,  FontWeight.Normal),
    Font(R.font.archivo_medium,   FontWeight.Medium),
    Font(R.font.archivo_semibold, FontWeight.SemiBold),
    Font(R.font.archivo_bold,     FontWeight.Bold),
    Font(R.font.archivo_black,    FontWeight.Black),
)

private const val TNUM = "tnum"   // tabular figures — mandatory on scores

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object ESPNText {
    val ScreenTitle   = TextStyle(Archivo, fontWeight = FontWeight.Black,    fontSize = 32.sp, lineHeight = 35.sp, letterSpacing = (-0.5).sp)
    val Headline      = TextStyle(Archivo, fontWeight = FontWeight.Black,    fontSize = 26.sp, lineHeight = 30.sp, letterSpacing = (-0.3).sp)
    val SectionHeader = TextStyle(Archivo, fontWeight = FontWeight.ExtraBold,fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val TeamName      = TextStyle(Archivo, fontWeight = FontWeight.Bold,     fontSize = 18.sp, lineHeight = 22.sp)
    val Body          = TextStyle(Archivo, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 24.sp)
    val StoryTitle    = TextStyle(Archivo, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 20.sp)
    val Meta          = TextStyle(Archivo, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val Record        = TextStyle(Archivo, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val ButtonLabel   = TextStyle(Archivo, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.3.sp)
    val StatusBadge   = TextStyle(Archivo, fontWeight = FontWeight.Black,    fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.5.sp)
    val CategoryTag   = TextStyle(Archivo, fontWeight = FontWeight.Black,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.5.sp)
    val Tab           = TextStyle(Archivo, fontWeight = FontWeight.Bold,     fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    // Scores — tabular, non-negotiable
    val ScoreLarge    = TextStyle(Archivo, fontWeight = FontWeight.Black,    fontSize = 26.sp, lineHeight = 28.sp, fontFeatureSettings = TNUM)
    val ScoreTicker   = TextStyle(Archivo, fontWeight = FontWeight.Bold,     fontSize = 13.sp, lineHeight = 14.sp, fontFeatureSettings = TNUM)
}

// Map onto Material 3 slots so stock components inherit the brand
val ESPNTypography = Typography(
    headlineLarge  = ESPNText.ScreenTitle,
    headlineMedium = ESPNText.Headline,
    titleMedium    = ESPNText.TeamName,
    bodyMedium     = ESPNText.Body,
    labelSmall     = ESPNText.Tab,
)
```

## 3. Signature Components

### Game Card (the core atom)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

data class Team(val logo: Color, val name: String, val record: String, val score: Int, val winning: Boolean)

@Composable
fun GameCard(
    league: String,            // "NBA · WESTERN FINALS · GAME 5"
    isLive: Boolean,
    isFinal: Boolean,
    clock: String,             // "Q3 · 4:12"
    home: Team,
    away: Team,
) {
    fun scoreColor(t: Team) =
        if (isFinal) (if (t.winning) ESPNColors.Win else ESPNColors.TextSecondary)
        else (if (t.winning) ESPNColors.TextPrimary else ESPNColors.TextSecondary)

    @Composable
    fun TeamRow(t: Team) {
        Row(
            Modifier.fillMaxWidth().padding(vertical = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(Modifier.size(30.dp).clip(CircleShape).background(t.logo))
            Column(Modifier.weight(1f)) {
                Text(t.name, style = ESPNText.TeamName, color = ESPNColors.TextPrimary)
                Text(t.record, style = ESPNText.Record, color = ESPNColors.TextSecondary)
            }
            Text("${t.score}", style = ESPNText.ScoreLarge, color = scoreColor(t))
        }
    }

    Column(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(12.dp))
            .background(ESPNColors.Surface1)
            .border(1.dp, ESPNColors.Divider, RoundedCornerShape(12.dp))
    ) {
        // Header
        Row(
            Modifier.fillMaxWidth().background(ESPNColors.Surface2).padding(horizontal = 14.dp, vertical = 10.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            Text(league, style = ESPNText.StatusBadge, color = ESPNColors.TextSecondary)
            StatusBadge(isLive = isLive, isFinal = isFinal, label = if (isLive) "LIVE" else if (isFinal) "FINAL" else clock)
        }
        // Team rows
        Column(Modifier.padding(14.dp)) {
            TeamRow(home)
            HorizontalDivider(color = ESPNColors.Divider, thickness = 1.dp)
            TeamRow(away)
        }
        // Footer
        Row(
            Modifier
                .fillMaxWidth()
                .border(0.dp, Color.Transparent)
                .padding(horizontal = 14.dp, vertical = 10.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            Text(clock, style = ESPNText.TeamName.copy(fontSize = 13.sp), color = ESPNColors.TextPrimary)
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Icon(Icons.Filled.PlayArrow, contentDescription = null, tint = ESPNColors.RedBright, modifier = Modifier.size(12.dp))
                Text("Watch on ESPN+", style = ESPNText.StatusBadge.copy(fontSize = 12.sp), color = ESPNColors.RedBright)
            }
        }
    }
}
```

### Scores Ticker

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.items

data class TickerSide(val abbr: String, val logo: Color, val score: String, val win: Boolean)
data class TickerChip(
    val league: String, val status: String, val isLive: Boolean, val isFinal: Boolean,
    val a: TickerSide, val b: TickerSide,
)

@Composable
fun ScoresTicker(chips: List<TickerChip>) {
    @Composable
    fun Line(s: TickerSide) {
        Row(Modifier.fillMaxWidth(), verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.SpaceBetween) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Box(Modifier.size(16.dp).clip(CircleShape).background(s.logo))
                Text(s.abbr, style = ESPNText.ScoreTicker,
                    color = if (s.win) ESPNColors.TextPrimary else ESPNColors.TextSecondary)
            }
            Text(s.score, style = ESPNText.ScoreTicker,
                color = if (s.win) ESPNColors.TextPrimary else ESPNColors.TextSecondary)
        }
    }

    Column {
        LazyRow(
            horizontalArrangement = Arrangement.spacedBy(8.dp),
            contentPadding = PaddingValues(horizontal = 12.dp),
        ) {
            items(chips) { c ->
                Column(
                    Modifier
                        .width(116.dp)
                        .clip(RoundedCornerShape(8.dp))
                        .background(ESPNColors.Surface2)
                        .padding(10.dp),
                    verticalArrangement = Arrangement.spacedBy(6.dp),
                ) {
                    Row(Modifier.fillMaxWidth(), verticalAlignment = Alignment.CenterVertically,
                        horizontalArrangement = Arrangement.SpaceBetween) {
                        Text(c.league, style = ESPNText.Tab, color = ESPNColors.TextSecondary)
                        StatusBadge(isLive = c.isLive, isFinal = c.isFinal, label = c.status, compact = true)
                    }
                    Line(c.a)
                    Line(c.b)
                }
            }
        }
        Spacer(Modifier.height(12.dp))
        HorizontalDivider(color = ESPNColors.Divider, thickness = 1.dp)
    }
}
```

### Live Status Badge (pulsing dot)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.runtime.getValue

@Composable
fun StatusBadge(isLive: Boolean, isFinal: Boolean, label: String, compact: Boolean = false) {
    val bg = when { isLive -> ESPNColors.Live; isFinal -> ESPNColors.Surface3; else -> Color.Transparent }
    val fg = if (isFinal) ESPNColors.TextSecondary else ESPNColors.TextPrimary

    val infinite = rememberInfiniteTransition(label = "livePulse")
    val dotAlpha by infinite.animateFloat(
        initialValue = 1f, targetValue = 0.4f,
        animationSpec = infiniteRepeatable(tween(1200), RepeatMode.Reverse),
        label = "dotAlpha",
    )

    Row(
        Modifier
            .clip(RoundedCornerShape(3.dp))
            .background(bg)
            .then(if (!isLive && !isFinal) Modifier.border(1.dp, Color.White.copy(alpha = 0.4f), RoundedCornerShape(3.dp)) else Modifier)
            .padding(horizontal = if (compact) 5.dp else 9.dp, vertical = if (compact) 2.dp else 4.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(5.dp),
    ) {
        if (isLive) {
            Box(Modifier.size(6.dp).clip(CircleShape).background(Color.White.copy(alpha = dotAlpha)))
        }
        Text(label.uppercase(), style = ESPNText.StatusBadge.copy(fontSize = if (compact) 9.sp else 11.sp), color = fg)
    }
}
```

### Primary CTA ("Watch Live")

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.runtime.remember

@Composable
fun WatchLiveButton(onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Button(
        onClick = onClick,
        interactionSource = interaction,
        shape = RoundedCornerShape(6.dp),
        colors = ButtonDefaults.buttonColors(
            containerColor = if (pressed) ESPNColors.RedPressed else ESPNColors.Red,
            contentColor = ESPNColors.TextPrimary,
        ),
        modifier = Modifier.fillMaxWidth().height(48.dp),
    ) {
        Icon(Icons.Filled.PlayArrow, contentDescription = null, modifier = Modifier.size(16.dp))
        Spacer(Modifier.width(8.dp))
        Text("Watch Live", style = ESPNText.ButtonLabel)
    }
}
```

### SportsCenter Story Row

```kotlin
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun StoryRow(thumb: Color, category: String, title: String, meta: String) {
    Column {
        Row(
            Modifier.fillMaxWidth().padding(vertical = 14.dp),
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(Modifier.size(96.dp, 72.dp).clip(RoundedCornerShape(8.dp)).background(thumb))
            Column(Modifier.weight(1f)) {
                Text(category.uppercase(), style = ESPNText.CategoryTag, color = ESPNColors.RedBright)
                Spacer(Modifier.height(4.dp))
                Text(title, style = ESPNText.StoryTitle, color = ESPNColors.TextPrimary,
                    maxLines = 2, overflow = TextOverflow.Ellipsis)
                Spacer(Modifier.height(6.dp))
                Text(meta, style = ESPNText.Meta.copy(fontSize = 12.sp), color = ESPNColors.TextSecondary)
            }
        }
        HorizontalDivider(color = ESPNColors.Divider, thickness = 1.dp)
    }
}
```

## 4. Navigation

ESPN marks the active tab with a small red dot under a white icon — not a Material tint pill. Use a `NavigationBar` with the indicator transparent and a custom icon that adds the dot.

```kotlin
import androidx.compose.material.icons.filled.*

@Composable
fun ESPNBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = ESPNColors.Canvas.copy(alpha = 0.96f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"     to Icons.Filled.Home,
            "Scores"   to Icons.Filled.SportsBasketball,
            "Watch"    to Icons.Filled.PlayCircle,
            "ESPN BET" to Icons.Filled.BarChart,
            "More"     to Icons.Filled.MoreHoriz,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(3.dp)) {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp))
                        Box(
                            Modifier.size(4.dp).clip(CircleShape)
                                .background(if (selected == i) ESPNColors.Red else Color.Transparent)
                        )
                    }
                },
                label = { Text(label, style = ESPNText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = ESPNColors.TextPrimary,   // white icon when active
                    selectedTextColor   = ESPNColors.TextPrimary,
                    unselectedIconColor = ESPNColors.TextTertiary,
                    unselectedTextColor = ESPNColors.TextTertiary,
                    indicatorColor      = Color.Transparent,         // no Material pill — ESPN uses a red dot
                ),
            )
        }
    }
}
```

The scores ticker is rendered *outside* the feed `LazyColumn` (pinned below the top app bar) so it stays glanceable while the feed scrolls beneath it. The game detail uses a score header + a `TabRow` (Gamecast / Box Score / Plays / News) with a red `Indicator`.

## 5. Motion

ESPN motion is calm except for the two scoreboard cues: the live pulse and the score pop.

| Moment | Compose recipe |
|--------|----------------|
| Live pulse | `rememberInfiniteTransition` + `animateFloat` 1f → 0.4f, `infiniteRepeatable(tween(1200), Reverse)` on the dot alpha |
| Score update | `animateFloatAsState` scale 1f → 1.06f → 1f (`keyframes`/`Animatable`) + value cross-fade `tween(200)` |
| Game ends (LIVE → FINAL) | swap badge; animate winner score color via `animateColorAsState` → `Win` `tween(300)` |
| Tabbed underline | `TabRow` default `Indicator` recolored `Red`, slides ~`tween(200)` |
| Card press | `animateFloatAsState` scale → 0.98 `tween(150)` |
| Tab switch | instant content swap; active-dot `AnimatedVisibility` `fadeIn(tween(120))` |
| Breaking alert | top banner `slideInVertically` `tween(250)` + a brief red pulse, auto-dismiss |

```kotlin
// Score pop — the scoreboard "tick"
val pop = remember { Animatable(1f) }
LaunchedEffect(score) {
    pop.animateTo(1.06f, tween(100)); pop.animateTo(1f, tween(100))
}
Text("$score", style = ESPNText.ScoreLarge, modifier = Modifier.graphicsLayer { scaleX = pop.value; scaleY = pop.value })
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for a soft tick on Follow toggle and tab change; a stronger `HapticFeedbackConstants.CONFIRM` (via `LocalView`) when a *tracked* team's score changes — the "your team scored" buzz.

## 6. Icons

ESPN iconography is bold and simple; the closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Scores (tab) | `sportscourt` | `Icons.Filled.SportsBasketball` |
| Watch (tab) | `play.rectangle` | `Icons.Filled.PlayCircle` |
| ESPN BET (tab) | `chart.bar` | `Icons.Filled.BarChart` |
| More (tab) | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Watch / play (CTA) | `play.fill` | `Icons.Filled.PlayArrow` |
| Follow team | `plus` / `checkmark` | `Icons.Filled.Add` / `Icons.Filled.Check` |
| Alerts / bell | `bell` | `Icons.Filled.Notifications` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Box score | `tablecells` | `Icons.Filled.TableChart` |
| Stats | `chart.xyaxis.line` | `Icons.Filled.ShowChart` |
| Cast | `airplayvideo` | `Icons.Filled.Cast` |
| Live dot | (filled `Circle`) | (a `Box` circle, not an icon) |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `LazyRow`, `infiniteRepeatable`, `fontFeatureSettings` are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`. The near-black canvas wants light-content system bars permanently; the top bar + pinned ticker sit below the camera cutout.
- **Dark-first scoreboard**: do **not** branch on `isSystemInDarkTheme()` for the scoreboard surfaces, and do **not** enable Material You `dynamicColorScheme()` — the near-black + ESPN-red identity must hold regardless of wallpaper.
- **Tabular numerals are mandatory**: every score / clock / record uses `fontFeatureSettings = "tnum"` — proportional digits shift columns and break the scoreboard. This is correctness, not style.
- **Font scaling**: `sp` honors the user's font scale — keep it on headlines, section headers, body, story titles, meta. Pin layout-sensitive text (ticker scores/chips, status badges, 10sp tab labels, the pulse dot, the game-card score size) by deriving from `dp` so the fixed-rhythm scoreboard doesn't reflow.
- **TalkBack**: label the game card "{away} {awayScore}, {home} {homeScore}, {status}"; the ticker chip the same compactly; the LIVE badge "Live, {period}"; back the win-green and live-red with text ("FINAL", "LIVE Q3") so state is not color-only.
- **Color-only meaning**: the winning-score green and the live red must be supported by text labels — never convey final/winner or live purely by color.
- **Touch targets**: Material guidance is 48.dp. The CTA is 48.dp; give the 22.dp tab icons a 48.dp hit area; game cards are full-card tappable (≥120.dp); ticker chips ≥56.dp; story rows ≥72.dp.
- **Contrast**: `#FFFFFF` on `#0E0F11` is maximal; `#9AA0A6` meta passes AA at 14sp; the LIVE pill uses white on `#FF1A1A` (passes AA); win green `#1FAA59` on near-black passes AA for the large score.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the live pulse with a static red dot, drop the score pop, and snap the winner color to green (no animation); keep the tabbed underline as a `Crossfade`.
- **Live updates**: drive scores via a websocket/poll worker; on a *tracked* team's score change fire the confirm haptic and the 1.06 score pop.
