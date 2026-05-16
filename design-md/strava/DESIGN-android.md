# Strava (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Strava's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the single Strava-Orange accent, the haloed route polyline, the universal 3-up stat grid, coal-black dark mode) while making everything idiomatic Android — `NavigationBar` with a protruding center FAB instead of a UITabBar, Google Maps Compose `Polyline` instead of `MapPolyline`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, `com.google.maps.android:maps-compose` `4.3+` for the route snapshot, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for athlete avatars. No color extraction — Strava is monochromatically Orange, so `androidx.palette` is not required.

## 1. Color Tokens

```kotlin
// ui/theme/StravaColors.kt
import androidx.compose.ui.graphics.Color

object StravaColors {
    // Strava Orange — the only accent
    val Orange        = Color(0xFFFC4C02)
    val OrangePressed = Color(0xFFD44002)
    val OrangeHalo    = Color(0xFFFC4C02).copy(alpha = 0.3f) // luminance ring under the polyline

    // Canvas, Surface, Divider (Light)
    val Canvas      = Color(0xFFFFFFFF)
    val SurfaceWarm = Color(0xFFF5F4F2)
    val SurfaceCool = Color(0xFFF0F0F0)
    val Divider     = Color(0xFFE5E5E5)
    val MapTile     = Color(0xFFE8E5DD) // warm parchment base — never gray

    // Text (Light)
    val InkPrimary   = Color(0xFF0E0E0E)
    val InkSecondary = Color(0xFF666666)
    val InkTertiary  = Color(0xFF9A9A9A)

    // Dark mode — coal, not pure black
    val DarkCanvas    = Color(0xFF0F0F0F)
    val DarkSurface   = Color(0xFF1A1A1A)
    val DarkSurface2  = Color(0xFF262626)
    val DarkDivider   = Color(0xFF2A2A2A)
    val DarkText      = Color(0xFFF2F2F2)
    val DarkTextSec   = Color(0xFFA0A0A0)
    val DarkMapTile   = Color(0xFF1B1B1B)

    // Achievement & rank
    val PRGold   = Color(0xFFF5C24A)
    val Silver   = Color(0xFFC6C6C6)
    val Bronze   = Color(0xFFCD7F32)
    val KOMCrown = Color(0xFFFFD700)

    // Chart & semantic
    val Success   = Color(0xFF22C55E)
    val HeartRed  = Color(0xFFE74C3C)
    val PaceBlue  = Color(0xFF3B82F6)
    val ElevBrown = Color(0xFF8B6F47)
    val WarningAmber = Color(0xFFF59E0B)
    val ErrorRed  = Color(0xFFEF4444)
}
```

Strava is white-first; supply a Material 3 `lightColorScheme` and a coal `darkColorScheme`. Orange is identical in both — it glows on coal.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val StravaLight = lightColorScheme(
    primary        = StravaColors.Orange,
    onPrimary      = StravaColors.Canvas,
    background     = StravaColors.Canvas,
    onBackground   = StravaColors.InkPrimary,
    surface        = StravaColors.Canvas,
    onSurface      = StravaColors.InkPrimary,
    surfaceVariant = StravaColors.SurfaceCool,
    outline        = StravaColors.Divider,
    error          = StravaColors.ErrorRed,
)

private val StravaDark = darkColorScheme(
    primary        = StravaColors.Orange,
    onPrimary      = StravaColors.Canvas,
    background     = StravaColors.DarkCanvas,   // coal #0F0F0F, not pure black
    onBackground   = StravaColors.DarkText,
    surface        = StravaColors.DarkSurface,
    onSurface      = StravaColors.DarkText,
    surfaceVariant = StravaColors.DarkSurface2,
    outline        = StravaColors.DarkDivider,
    error          = StravaColors.ErrorRed,
)

@Composable
fun StravaTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (darkTheme) StravaDark else StravaLight,
    typography  = StravaTypography,
    content     = content,
)
```

## 2. Typography

Inside the iOS app Strava leans on SF Pro for its numeric weight stack. On Android the system equivalent is Roboto — but the Strava data voice depends on **tabular figures**, so the closest free substitute is `Roboto` with `FontFeatureSetting("tnum")`. Bundle the marketing face (Maison Neue) only if you have a license; otherwise Roboto holds.

```kotlin
// ui/theme/StravaType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

private val Sans = FontFamily.Default // Roboto on Android — closest to SF Pro
private const val TNUM = "tnum"        // tabular figures — Strava's column alignment depends on it

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, weights preserved)
object StravaText {
    val HeroStat      = TextStyle(Sans, fontWeight = FontWeight.Black,    fontSize = 44.sp, lineHeight = 44.sp, letterSpacing = (-0.6).sp, fontFeatureSettings = TNUM)
    val HeroUnit      = TextStyle(Sans, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp, letterSpacing = 0.5.sp)
    val LargeNav      = TextStyle(Sans, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val ActivityTitle = TextStyle(Sans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val Athlete       = TextStyle(Sans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 18.sp, letterSpacing = (-0.1).sp)
    val SectionHdr    = TextStyle(Sans, fontWeight = FontWeight.Bold,     fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.5.sp)
    val Body          = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp, letterSpacing = (-0.2).sp)
    val BodySmall     = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp, letterSpacing = (-0.1).sp)
    val StatLabel     = TextStyle(Sans, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.6.sp)
    val StatValue     = TextStyle(Sans, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 19.sp, fontFeatureSettings = TNUM)
    val KudosCount    = TextStyle(Sans, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp, fontFeatureSettings = TNUM)
    val Meta          = TextStyle(Sans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp, letterSpacing = (-0.1).sp)
    val Button        = TextStyle(Sans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 17.sp, letterSpacing = (-0.2).sp)
    val ButtonSm      = TextStyle(Sans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 15.sp, letterSpacing = (-0.1).sp)
    val Tab           = TextStyle(Sans, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp)
    val Rank          = TextStyle(Sans, fontWeight = FontWeight.Black,    fontSize = 13.sp, lineHeight = 13.sp, fontFeatureSettings = TNUM)
}

// Map onto Material 3 slots so stock components inherit the brand
val StravaTypography = Typography(
    headlineMedium = StravaText.LargeNav,
    titleMedium    = StravaText.ActivityTitle,
    bodyMedium     = StravaText.Body,
    labelLarge     = StravaText.Button,
    labelSmall     = StravaText.Tab,
)
```

> Every numeric style sets `fontFeatureSettings = "tnum"`. This is the Compose analog of SwiftUI's `.monospacedDigit()` — without it, pace columns and split tables misalign and Strava users notice.

## 3. Signature Components

### Stat Cell + 3-Up Grid (the universal activity-card primitive)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.dp

@Composable
fun StatCell(label: String, value: String, modifier: Modifier = Modifier) {
    Column(
        modifier = modifier.padding(vertical = 16.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Text(label.uppercase(), style = StravaText.StatLabel, color = StravaColors.InkSecondary)
        Text(value, style = StravaText.StatValue, color = StravaColors.InkPrimary)
    }
}

@Composable
fun StatGrid(stats: List<Pair<String, String>>, modifier: Modifier = Modifier) {
    Row(modifier.fillMaxWidth(), verticalAlignment = Alignment.CenterVertically) {
        stats.forEachIndexed { idx, (label, value) ->
            StatCell(label, value, Modifier.weight(1f))
            if (idx < stats.lastIndex) {
                // 1pt vertical divider only in light mode (DESIGN.md §4)
                Box(
                    Modifier
                        .width(1.dp)
                        .height(40.dp)
                        .background(StravaColors.SurfaceCool)
                )
            }
        }
    }
}
```

### Route Map Snapshot with Polyline Halo

iOS draws two `MapPolyline`s on a `Map`. On Android use Google Maps Compose: draw the **halo polyline first** (width ~12px, `OrangeHalo`), then the **main stroke on top** (width ~8px, `Orange`). Round caps + round joins so switchbacks don't miter.

```kotlin
import com.google.android.gms.maps.model.LatLng
import com.google.maps.android.compose.*
import com.google.android.gms.maps.model.JointType
import com.google.android.gms.maps.model.RoundCap
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.draw.clip

@Composable
fun RouteMapSnapshot(
    route: List<LatLng>,
    modifier: Modifier = Modifier,
) {
    Box(
        modifier
            .fillMaxWidth()
            .aspectRatio(16f / 9f)
            .clip(RoundedCornerShape(6.dp)),
    ) {
        GoogleMap(
            modifier = Modifier.fillMaxSize(),
            uiSettings = MapUiSettings(
                zoomControlsEnabled = false,
                scrollGesturesEnabled = false,
                tiltGesturesEnabled = false,
            ),
            properties = MapProperties(mapStyleOptions = stravaMapStyle()), // warm parchment / coal dark JSON
        ) {
            // halo first
            Polyline(
                points = route,
                color = StravaColors.OrangeHalo,
                width = 12f,
                startCap = RoundCap(), endCap = RoundCap(),
                jointType = JointType.ROUND,
            )
            // main stroke on top — 4dp scaled (≈8px); thins to ≈6px for long activities
            Polyline(
                points = route,
                color = StravaColors.Orange,
                width = 8f,
                startCap = RoundCap(), endCap = RoundCap(),
                jointType = JointType.ROUND,
            )
            route.firstOrNull()?.let {
                MarkerComposable(state = MarkerState(it)) { RouteDot(filled = false) }
            }
            route.lastOrNull()?.let {
                MarkerComposable(state = MarkerState(it)) { RouteDot(filled = true) }
            }
        }
    }
}

@Composable
private fun RouteDot(filled: Boolean) {
    Box(
        Modifier
            .size(12.dp)
            .clip(androidx.compose.foundation.shape.CircleShape)
            .background(if (filled) StravaColors.Orange else StravaColors.Canvas)
            .then(
                if (filled) Modifier.border(2.dp, StravaColors.Canvas, androidx.compose.foundation.shape.CircleShape)
                else Modifier.border(2.dp, StravaColors.Orange, androidx.compose.foundation.shape.CircleShape)
            )
    )
}

// Supply a styled-map JSON in res/raw (warm parchment light, charcoal dark)
private fun stravaMapStyle(): com.google.android.gms.maps.model.MapStyleOptions? = null
```

### Kudos Button (spring bounce + confetti)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.spring
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ThumbUp
import androidx.compose.material.icons.outlined.ThumbUp
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import kotlin.math.cos
import kotlin.math.sin

@Composable
fun KudosButton(
    initialCount: Int,
    modifier: Modifier = Modifier,
) {
    var given by remember { mutableStateOf(false) }
    var count by remember { mutableIntStateOf(initialCount) }
    val scale = remember { Animatable(1f) }
    var burst by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current
    val interaction = remember { MutableInteractionSource() }

    LaunchedEffect(given) {
        if (given) {
            // 1.0 → 1.3 → 1.0 spring (damping 0.6)
            scale.animateTo(1.3f, spring(dampingRatio = 0.6f, stiffness = 500f))
            scale.animateTo(1.0f, spring(dampingRatio = 0.6f))
        }
    }

    Box(modifier) {
        Row(
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
            modifier = Modifier
                .clip(androidx.compose.foundation.shape.CircleShape)
                .clickable(interaction, indication = null) {
                    given = !given
                    count += if (given) 1 else -1
                    burst = given
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS medium impact
                }
                .padding(horizontal = 4.dp, vertical = 8.dp),
        ) {
            Icon(
                imageVector = if (given) Icons.Filled.ThumbUp else Icons.Outlined.ThumbUp,
                contentDescription = if (given) "Kudos given, $count" else "Give kudos, $count",
                tint = if (given) StravaColors.Orange else StravaColors.InkSecondary,
                modifier = Modifier.size(18.dp).scale(scale.value),
            )
            Text("$count", style = StravaText.KudosCount, color = StravaColors.InkPrimary)
        }
        if (burst) ConfettiBurst(onDone = { burst = false })
    }
}

@Composable
private fun ConfettiBurst(onDone: () -> Unit) {
    val rise = remember { Animatable(0f) }
    LaunchedEffect(Unit) {
        rise.animateTo(1f, androidx.compose.animation.core.tween(600))
        onDone()
    }
    Box(Modifier.size(40.dp), contentAlignment = Alignment.Center) {
        repeat(8) { i ->
            val angle = i * Math.PI / 4
            Box(
                Modifier
                    .size(4.dp)
                    .offset(
                        x = (cos(angle) * 30 * rise.value).dp,
                        y = (-sin(angle) * 30 * rise.value).dp,
                    )
                    .clip(androidx.compose.foundation.shape.CircleShape)
                    .background(StravaColors.Orange.copy(alpha = 1f - rise.value))
            )
        }
    }
}
```

### Activity Feed Card (the hero component)

```kotlin
import androidx.compose.material.icons.filled.DirectionsRun
import androidx.compose.material.icons.outlined.ChatBubbleOutline
import androidx.compose.material3.HorizontalDivider
import coil.compose.AsyncImage
import androidx.compose.ui.layout.ContentScale

@Composable
fun ActivityCard(
    athleteAvatarUrl: String,
    athleteName: String,
    timestamp: String,
    activityTitle: String,
    route: List<LatLng>,
    distance: String,
    elapsed: String,
    pace: String,
    kudosCount: Int,
    commentCount: Int,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier = modifier
            .fillMaxWidth()
            .background(StravaColors.Canvas) // flat — no shadow, no border
            .padding(horizontal = 16.dp, vertical = 16.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(12.dp)) {
            AsyncImage(
                model = athleteAvatarUrl,
                contentDescription = null,
                modifier = Modifier.size(40.dp).clip(androidx.compose.foundation.shape.CircleShape),
                contentScale = ContentScale.Crop,
            )
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(athleteName, style = StravaText.Athlete, color = StravaColors.InkPrimary)
                Text(timestamp, style = StravaText.Meta, color = StravaColors.InkSecondary)
            }
            Icon(Icons.Filled.DirectionsRun, null, tint = StravaColors.Orange, modifier = Modifier.size(18.dp))
        }

        Text(activityTitle, style = StravaText.ActivityTitle, color = StravaColors.InkPrimary, maxLines = 2)

        RouteMapSnapshot(route)

        StatGrid(listOf("Distance" to distance, "Time" to elapsed, "Pace" to pace))

        HorizontalDivider(color = StravaColors.Divider)

        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(16.dp)) {
            KudosButton(initialCount = kudosCount)
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Icon(Icons.Outlined.ChatBubbleOutline, null, tint = StravaColors.InkSecondary, modifier = Modifier.size(16.dp))
                Text("$commentCount", style = StravaText.KudosCount, color = StravaColors.InkSecondary)
            }
            Spacer(Modifier.weight(1f))
            Text("View Activity", style = StravaText.ButtonSm, color = StravaColors.Orange)
        }
    }
}
```

### Segment Leaderboard Row

```kotlin
@Composable
fun LeaderboardRow(
    rank: Int,
    avatarUrl: String,
    name: String,
    date: String,
    time: String,
    gap: String,
    modifier: Modifier = Modifier,
) {
    val rankBg = when (rank) {
        1 -> StravaColors.PRGold
        2 -> StravaColors.Silver
        3 -> StravaColors.Bronze
        else -> StravaColors.SurfaceCool
    }
    val rankFg = if (rank <= 3) StravaColors.Canvas else StravaColors.InkPrimary

    Column(modifier.fillMaxWidth()) {
        Row(
            Modifier.height(56.dp).padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Box(
                Modifier.size(32.dp).clip(androidx.compose.foundation.shape.CircleShape).background(rankBg),
                contentAlignment = Alignment.Center,
            ) { Text("$rank", style = StravaText.Rank, color = rankFg) }
            AsyncImage(
                model = avatarUrl, contentDescription = null,
                modifier = Modifier.size(36.dp).clip(androidx.compose.foundation.shape.CircleShape),
                contentScale = ContentScale.Crop,
            )
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(name, style = StravaText.Athlete, color = StravaColors.InkPrimary)
                Text(date, style = StravaText.Meta, color = StravaColors.InkSecondary)
            }
            Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(time, style = StravaText.StatValue, color = StravaColors.InkPrimary)
                Text(gap, style = StravaText.Meta, color = StravaColors.InkSecondary)
            }
        }
        HorizontalDivider(color = StravaColors.Divider, thickness = 0.5.dp)
    }
}
```

### Achievement Badge

```kotlin
import androidx.compose.material.icons.filled.EmojiEvents

@Composable
fun AchievementBadge(text: String, modifier: Modifier = Modifier) {
    Row(
        modifier
            .clip(RoundedCornerShape(50))
            .background(StravaColors.Orange.copy(alpha = 0.08f))
            .padding(horizontal = 10.dp, vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Icon(Icons.Filled.EmojiEvents, null, tint = StravaColors.Orange, modifier = Modifier.size(14.dp))
        Text(text, style = StravaText.KudosCount, color = StravaColors.InkPrimary)
    }
}
```

## 4. The Center Record Button (the signature elevated control)

Strava's only elevated element is the **center Record button** — it protrudes 8dp above a 5-item bottom bar with a glowing orange shadow and fires the *heaviest* haptic in the app. Compose has no center-FAB `NavigationBar` slot, so compose a `Box` overlay above a `NavigationBar` (the iOS pattern is a placeholder center tab + a floating button).

```kotlin
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.draw.scale
import androidx.compose.animation.core.animateFloatAsState

@Composable
fun StravaRecordButton(
    onRecord: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.95f else 1f, label = "recordScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .offset(y = (-8).dp)      // protrudes 8dp above the bar
            .size(56.dp)
            .scale(scale)
            // glowing orange aura — rgba(252,76,2,0.4) 0 8px 16px
            .shadow(16.dp, CircleShape, spotColor = StravaColors.Orange.copy(alpha = 0.4f))
            .clip(CircleShape)
            .background(StravaColors.Orange)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // heaviest tap in the app
                onRecord()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(Icons.Filled.RadioButtonChecked, "Record", tint = StravaColors.Canvas, modifier = Modifier.size(22.dp))
    }
}

@Composable
fun StravaScaffold(content: @Composable (PaddingValues) -> Unit) {
    var tab by remember { mutableIntStateOf(0) }
    Scaffold(
        bottomBar = {
            Box(contentAlignment = Alignment.TopCenter) {
                NavigationBar(containerColor = StravaColors.Canvas, tonalElevation = 0.dp) {
                    val items = listOf(
                        "Home" to Icons.Filled.Home,
                        "Maps" to Icons.Filled.Map,
                        null to null,                       // placeholder slot for Record
                        "Groups" to Icons.Filled.Groups,
                        "You" to Icons.Filled.Person,
                    )
                    items.forEachIndexed { i, (label, icon) ->
                        if (icon == null) {
                            NavigationBarItem(false, onClick = {}, enabled = false, icon = { Spacer(Modifier.size(24.dp)) })
                        } else {
                            NavigationBarItem(
                                selected = tab == i,
                                onClick = { tab = i },
                                icon = { Icon(icon, label, modifier = Modifier.size(24.dp)) },
                                label = { Text(label!!, style = StravaText.Tab) },
                                colors = NavigationBarItemDefaults.colors(
                                    selectedIconColor   = StravaColors.Orange,
                                    selectedTextColor   = StravaColors.Orange,
                                    unselectedIconColor = StravaColors.InkSecondary,
                                    unselectedTextColor = StravaColors.InkSecondary,
                                    indicatorColor      = Color.Transparent,
                                ),
                            )
                        }
                    }
                }
                StravaRecordButton(onRecord = { /* present record sheet */ })
            }
        },
        content = content,
    )
}
```

Note: Android has no first-class live blur. Strava's iOS bottom bar is already a solid `#FFFFFF`/`#0F0F0F` with a 0.5pt top divider (no blur), so an opaque `containerColor` is exact — add a `HorizontalDivider(thickness = 0.5.dp)` above the bar.

## 5. Navigation

Strava's nav model is a 5-tab bottom bar with the protruding center Record button (see §4) plus per-screen top app bars (large `SF Pro Display 28pt Bold` titles → `StravaText.LargeNav`, trailing 22dp action icons). No rail, no drawer. The active tab tint is Strava Orange; inactive is `#666666`. Because there is no Material pill indicator in Strava's design, set `indicatorColor = Color.Transparent`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Kudos tap | `Animatable` 1 → 1.3 → 1.0 `spring(dampingRatio=0.6f)` + 8-particle `ConfettiBurst` over `tween(600)`; `HapticFeedbackType.LongPress` (medium) |
| Record button tap | `animateFloatAsState` 1 → 0.95 + heaviest haptic, then record sheet rises `tween(350)` |
| Activity card tap | very subtle scale 1 → 0.99, then nav push (`tween(280)`) |
| Tab switch | recolor outlined → Orange over ~200ms; selection haptic |
| Pace chart scrub | real-time, no haptic — value badge follows finger |
| PR celebration | trophy `Animatable` 1 → 1.1 → 1.0 `spring` (600ms feel); `HapticFeedbackType.LongPress` (success) |
| Pull-to-refresh | Material `PullToRefreshBox` tinted `StravaColors.Orange` |

```kotlin
// PR celebration pulse on first view of an activity with a PR
@Composable
fun PRTrophy(seen: Boolean) {
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(seen) {
        if (seen) {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress)
            scale.animateTo(1.1f, spring(dampingRatio = 0.7f, stiffness = 400f))
            scale.animateTo(1.0f, spring(dampingRatio = 0.7f))
        }
    }
    Icon(
        Icons.Filled.EmojiEvents, "Personal Record",
        tint = StravaColors.PRGold,
        modifier = Modifier.size(28.dp).scale(scale.value),
    )
}
```

Haptics: `LocalHapticFeedback` covers light/medium/heavy needs via `TextHandleMove` / `LongPress`. For a true graded heavy cue on Record, use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.LONG_PRESS)` or a `Vibrator` `VibrationEffect.createOneShot(20, 255)` — Record is the most serious tap in the app.

## 7. Icons

Strava ships custom activity-type glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity (the Strava trophy, the activity-type strip) export them as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Kudos (un-given / given) | `hand.thumbsup` / `hand.thumbsup.fill` | `Icons.Outlined.ThumbUp` / `Icons.Filled.ThumbUp` |
| Comment | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Activity — Run | `figure.run` | `Icons.Filled.DirectionsRun` |
| Activity — Ride | `figure.outdoor.cycle` | `Icons.Filled.DirectionsBike` |
| Activity — Swim | `figure.pool.swim` | `Icons.Filled.Pool` |
| Activity — Hike | `figure.hiking` | `Icons.Filled.Hiking` |
| Activity — Walk | `figure.walk` | `Icons.Filled.DirectionsWalk` |
| Record (center) | `record.circle` | `Icons.Filled.RadioButtonChecked` |
| Trophy / PR | `trophy.fill` | `Icons.Filled.EmojiEvents` |
| Crown / KOM | `crown.fill` | `Icons.Filled.WorkspacePremium` |
| Maps tab | `map` | `Icons.Filled.Map` |
| Groups tab | `person.3` | `Icons.Filled.Groups` |
| Profile tab | `person.crop.circle` | `Icons.Filled.Person` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Heart rate | `heart.fill` | `Icons.Filled.Favorite` |
| Elevation | `mountain.2.fill` | `Icons.Filled.Terrain` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; Maps Compose + motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. Light mode wants dark status-bar icons over the white canvas; coal dark wants light icons. The Record button's glow extends below the gesture-nav inset — let it draw into the bottom inset, but keep the tappable bar inside `Scaffold` insets.
- **Font scaling**: `sp` honors the user's font scale automatically — scale activity titles, body, metadata. **Pin** the layout-sensitive trio: the **44sp Black hero stats**, the **11sp/17sp 3-up stat grid** (clamp via `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` around the grid), tab labels, and rank chips — the column alignment breaks if they scale.
- **TalkBack**: group the activity card as one element — `Modifier.semantics(mergeDescendants = true) { contentDescription = "Casey Reardon's run, Tuesday Morning Run, 8.2 miles in 1:14:23, 9:03 per mile, 27 kudos, 12 comments, 2 hours ago" }`. The Kudos button is a separate node ("Give kudos, 27" / "Kudos given, 28").
- **Touch targets**: Material guidance is 48.dp. The 56.dp Record button is clear; the kudos thumb is a 36.dp glyph on a 44–48.dp target via padding — verify with `Modifier.minimumInteractiveComponentSize()`.
- **Contrast**: Strava Orange `#FC4C02` on white passes WCAG AA at 17sp+ SemiBold — do not use it for small body text; keep it on the 17sp `Button` / `View Activity` link and the 15sp `ButtonSm`.
- **Tabular figures**: `fontFeatureSettings = "tnum"` everywhere there's a stat (pace columns, splits, times) — the Compose equivalent of `.monospacedDigit()`.
- **Reduce motion**: detect `Settings.Global.ANIMATOR_DURATION_SCALE == 0f` (or a stored pref) — skip the kudos scale bounce + confetti and fall back to an instant color change.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` / `dynamicDarkColorScheme()` — Strava's discipline is a single fixed Orange accent and the coal `#0F0F0F` dark canvas regardless of wallpaper.
