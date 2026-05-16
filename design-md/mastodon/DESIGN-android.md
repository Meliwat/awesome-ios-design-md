# Mastodon (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Mastodon's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Mastodon's calm reading timeline, purple accent, the boost-spin, content-warning spoilers, federated handles) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `FloatingActionButton` for compose, `sp`/`dp` instead of `pt`. Mastodon is also a first-class Android app, so this maps cleanly.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for images.

## 1. Color Tokens

```kotlin
// ui/theme/MastodonColors.kt
import androidx.compose.ui.graphics.Color

object MastodonColors {
    // Light
    val Canvas    = Color(0xFFFFFFFF)
    val Surface2  = Color(0xFFF2F3F8)
    val Divider   = Color(0xFFC0CDD9)
    val Text      = Color(0xFF000000)
    val TextSec   = Color(0xFF606984)
    val TextTer   = Color(0xFF8B95A8)

    // Dark — blue-gray, NOT true black
    val DarkCanvas   = Color(0xFF191B22)
    val DarkSurface1 = Color(0xFF282C37)
    val DarkSurface2 = Color(0xFF313543)
    val DarkDivider  = Color(0xFF393F4F)
    val DarkTextSec  = Color(0xFF9BAEC8)

    // Brand
    val Purple        = Color(0xFF6364FF)
    val PurplePressed = Color(0xFF563ACC)
    val BoostGreen    = Color(0xFF2DCE89)

    // Semantic
    val FavGold = Color(0xFFCA8F04)
    val Error   = Color(0xFFDF405A)
}
```

Wire both schemes into Material 3. Mastodon ships light **and** dark; provide both, but keep the dark canvas blue-gray (`#191B22`), never `#000000`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val LightScheme = lightColorScheme(
    primary        = MastodonColors.Purple,
    onPrimary      = Color(0xFFFFFFFF),
    background      = MastodonColors.Canvas,
    onBackground    = MastodonColors.Text,
    surface         = MastodonColors.Canvas,
    onSurface       = MastodonColors.Text,
    surfaceVariant  = MastodonColors.Surface2,
    outline         = MastodonColors.Divider,
    error           = MastodonColors.Error,
)

private val DarkScheme = darkColorScheme(
    primary        = MastodonColors.Purple,
    onPrimary      = Color(0xFFFFFFFF),
    background      = MastodonColors.DarkCanvas,    // #191B22, not black
    onBackground    = Color(0xFFFFFFFF),
    surface         = MastodonColors.DarkSurface1,
    onSurface       = Color(0xFFFFFFFF),
    surfaceVariant  = MastodonColors.DarkSurface2,
    outline         = MastodonColors.DarkDivider,
    error           = MastodonColors.Error,
)

@Composable
fun MastodonTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) DarkScheme else LightScheme,
        typography  = MastodonTypography,
        content     = content,
    )
```

## 2. Typography

Mastodon uses the system font at reading sizes; the web client renders **Inter**. For web parity drop Inter TTFs in `res/font/` (lowercase, snake_case); otherwise reference `FontFamily.Default` (Roboto) — both read well.

```kotlin
// ui/theme/MastodonType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

// Optional Inter parity; fall back to FontFamily.Default if you don't bundle it
val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
    Font(R.font.inter_bold,     FontWeight.Bold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, 16sp body at 1.45)
object MastodonText {
    val TitleLarge   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.4).sp)
    val Section      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 27.sp, letterSpacing = (-0.3).sp)
    val DisplayName  = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val Body         = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 23.sp)   // 1.45
    val BodySettings = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val Handle       = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val CW           = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Count        = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 16.sp)
    val Button       = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.2).sp)
    val ShowMore     = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 14.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Tab          = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp)
}

val MastodonTypography = Typography(
    headlineLarge = MastodonText.TitleLarge,
    headlineSmall = MastodonText.Section,
    titleMedium   = MastodonText.DisplayName,
    bodyMedium    = MastodonText.Body,
    labelSmall    = MastodonText.Tab,
)
```

## 3. Signature Components

### Toot Card (the core unit)

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun TootCard(
    displayName: String,
    handle: String,            // "@Gargron@mastodon.social"
    timestamp: String,
    body: String,
    avatarUrl: String,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .fillMaxWidth()
            .background(MastodonColors.Canvas)
            .padding(16.dp),
    ) {
        Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
            AsyncImage(
                model = avatarUrl,
                contentDescription = null,
                modifier = Modifier.size(46.dp).clip(RoundedCornerShape(8.dp)),  // rounded-square, not circle
            )
            Column(Modifier.weight(1f)) {
                Text(displayName, style = MastodonText.DisplayName, color = MastodonColors.Text)
                Text(handle, style = MastodonText.Handle, color = MastodonColors.TextSec)
            }
            Text(timestamp, style = MastodonText.Count, color = MastodonColors.TextSec)
            Icon(Icons.Filled.MoreHoriz, null, tint = MastodonColors.TextSec, modifier = Modifier.size(18.dp))
        }

        Text(body, style = MastodonText.Body, color = MastodonColors.Text,
            modifier = Modifier.padding(top = 10.dp))

        Row(
            Modifier.fillMaxWidth().padding(top = 10.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            ActionIcon(Icons.AutoMirrored.Filled.Reply, 12, MastodonColors.TextSec)
            BoostButton(count = 34)
            FavouriteButton(count = 211)
            ActionIcon(Icons.Filled.Share, 0, MastodonColors.TextSec)
        }
    }
    HorizontalDivider(thickness = 1.dp, color = MastodonColors.Divider)
}

@Composable
private fun ActionIcon(icon: androidx.compose.ui.graphics.vector.ImageVector, count: Int, tint: Color) {
    Row(verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
        modifier = Modifier.defaultMinSize(minWidth = 44.dp, minHeight = 44.dp)) {
        Icon(icon, null, tint = tint, modifier = Modifier.size(18.dp))
        if (count > 0) Text("$count", style = MastodonText.Count, color = tint)
    }
}
```

### Boost Button (the 360° spin)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.ui.draw.rotate
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun BoostButton(count: Int, modifier: Modifier = Modifier) {
    var boosted by remember { mutableStateOf(false) }
    val angle = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()

    Row(
        modifier
            .defaultMinSize(minWidth = 44.dp, minHeight = 44.dp)
            .clickable(
                interactionSource = remember { MutableInteractionSource() },
                indication = null,
            ) {
                boosted = !boosted
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)  // ≈ success
                scope.launch {
                    angle.animateTo(
                        angle.value + if (boosted) 360f else -360f,
                        tween(400, easing = EaseOut),
                    )
                }
            },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(
            Icons.Filled.Repeat,
            contentDescription = if (boosted) "Boosted" else "Boost",
            tint = if (boosted) MastodonColors.BoostGreen else MastodonColors.TextSec,
            modifier = Modifier.size(18.dp).rotate(angle.value),
        )
        Text(
            "${if (boosted) count + 1 else count}",
            style = MastodonText.Count,
            color = if (boosted) MastodonColors.BoostGreen else MastodonColors.TextSec,
        )
    }
}
```

### Favourite Button (gold bounce)

```kotlin
@Composable
fun FavouriteButton(count: Int, modifier: Modifier = Modifier) {
    var fav by remember { mutableStateOf(false) }
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()

    Row(
        modifier
            .defaultMinSize(minWidth = 44.dp, minHeight = 44.dp)
            .clickable(remember { MutableInteractionSource() }, indication = null) {
                fav = !fav
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                scope.launch {
                    scale.animateTo(1.2f, spring(dampingRatio = 0.5f)); scale.animateTo(1f, spring())
                }
            },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(
            if (fav) Icons.Filled.Star else Icons.Outlined.StarBorder,
            contentDescription = if (fav) "Favourited" else "Favourite",
            tint = if (fav) MastodonColors.FavGold else MastodonColors.TextSec,
            modifier = Modifier.size(18.dp).scale(scale.value),
        )
        Text("${if (fav) count + 1 else count}", style = MastodonText.Count,
            color = if (fav) MastodonColors.FavGold else MastodonColors.TextSec)
    }
}
```

### Content-Warning Spoiler Card (the signature)

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.expandVertically
import androidx.compose.animation.fadeIn
import androidx.compose.animation.fadeOut
import androidx.compose.animation.shrinkVertically

@Composable
fun ContentWarningCard(warning: String, body: String, modifier: Modifier = Modifier) {
    var expanded by remember { mutableStateOf(false) }
    Column(modifier) {
        Row(
            Modifier
                .fillMaxWidth()
                .clip(RoundedCornerShape(8.dp))
                .background(MastodonColors.Surface2)
                .padding(12.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Icon(Icons.Filled.VisibilityOff, null, tint = MastodonColors.Text, modifier = Modifier.size(16.dp))
            Text(warning, style = MastodonText.CW, color = MastodonColors.Text,
                modifier = Modifier.padding(start = 8.dp).weight(1f))
            Text(
                if (expanded) "SHOW LESS" else "SHOW MORE",
                style = MastodonText.ShowMore,
                color = MastodonColors.Purple,
                modifier = Modifier.clickable { expanded = !expanded },
            )
        }
        AnimatedVisibility(
            visible = expanded,
            enter = fadeIn(tween(250)) + expandVertically(tween(250)),
            exit  = fadeOut(tween(250)) + shrinkVertically(tween(250)),
        ) {
            Text(body, style = MastodonText.Body, color = MastodonColors.Text,
                modifier = Modifier.padding(top = 10.dp))
        }
    }
}
```

### Primary Button & Compose FAB

```kotlin
@Composable
fun MastoPrimaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Button(
        onClick = onClick,
        modifier = modifier,
        shape = RoundedCornerShape(8.dp),
        colors = ButtonDefaults.buttonColors(
            containerColor = MastodonColors.Purple, contentColor = Color.White,
        ),
        contentPadding = PaddingValues(horizontal = 24.dp, vertical = 12.dp),
    ) { Text(text, style = MastodonText.Button) }
}

@Composable
fun ComposeFab(onClick: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    FloatingActionButton(
        onClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onClick() },
        containerColor = MastodonColors.Purple,
        contentColor = Color.White,
        shape = androidx.compose.foundation.shape.CircleShape,
        modifier = Modifier.size(56.dp),
    ) { Icon(Icons.Filled.Edit, contentDescription = "Compose", modifier = Modifier.size(24.dp)) }
}
```

## 4. Distinctive System — Federated Timeline Switcher

```kotlin
@Composable
fun TimelineSwitcher(scope: String, onChange: (String) -> Unit, modifier: Modifier = Modifier) {
    val scopes = listOf("Home", "Local", "Federated")
    Row(modifier.fillMaxWidth()) {
        scopes.forEach { s ->
            val active = s == scope
            Column(
                Modifier.weight(1f).clickable { onChange(s) }.padding(top = 12.dp),
                horizontalAlignment = Alignment.CenterHorizontally,
            ) {
                Text(
                    s,
                    style = MastodonText.DisplayName.copy(
                        fontWeight = if (active) FontWeight.SemiBold else FontWeight.Normal,
                    ),
                    color = if (active) MastodonColors.Purple else MastodonColors.TextSec,
                )
                Spacer(Modifier.height(8.dp))
                Box(
                    Modifier
                        .fillMaxWidth()
                        .height(2.dp)
                        .background(if (active) MastodonColors.Purple else Color.Transparent),
                )
            }
        }
    }
}
```

Pair the switch with a 250ms `Crossfade` on the feed and an 8.dp horizontal nudge (`animateDpAsState`).

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Mastodon's iOS tab bar is `.regularMaterial`; Android has no first-class live blur, so use a 94%-opaque canvas. Active tint is **Mastodon Purple**.

```kotlin
@Composable
fun MastodonBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = MastodonColors.Canvas.copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Home"          to Icons.Filled.Home,
            "Search"        to Icons.Filled.Search,
            "Notifications" to Icons.Filled.Notifications,
            "Profile"       to Icons.Filled.AccountBox,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(26.dp)) },
                label = { Text(label, style = MastodonText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = MastodonColors.Purple,
                    selectedTextColor   = MastodonColors.Purple,
                    unselectedIconColor = MastodonColors.TextSec,
                    unselectedTextColor = MastodonColors.TextSec,
                    indicatorColor      = MastodonColors.Purple.copy(alpha = 0.12f),
                ),
            )
        }
    }
}
```

The persistent compose **FAB** floats above this bar — place it in the `Scaffold` `floatingActionButton` slot with `FabPosition.End`.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Boost spin | `Animatable` `+360f` / `-360f` over `tween(400, EaseOut)`, color → BoostGreen, `HapticFeedbackType.LongPress` |
| Favourite bounce | `Animatable` 1 → 1.2 (`spring(dampingRatio = 0.5f)`) → 1, gold tint, light haptic |
| CW expand/collapse | `AnimatedVisibility` `fadeIn + expandVertically` over `tween(250)`, symmetric exit |
| Timeline switch | `Crossfade(tween(250))` + 8.dp `animateDpAsState` nudge |
| Compose FAB | scale 0.94 on press (`FloatingActionButton` ripple), present compose as a full screen / bottom sheet |

```kotlin
// Boost spin (see BoostButton): angle.animateTo(angle.value + 360f, tween(400, EaseOut))
// Favourite (see FavouriteButton): scale.animateTo(1.2f, spring(dampingRatio = 0.5f))
```

Haptics: prefer `LocalHapticFeedback`. For a richer "success" on boost use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+).

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Reply | `arrowshape.turn.up.left` | `Icons.AutoMirrored.Filled.Reply` |
| Boost | `arrow.2.squarepath` | `Icons.Filled.Repeat` |
| Favourite | `star` / `star.fill` | `Icons.Outlined.StarBorder` / `Icons.Filled.Star` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Content warning | `eye.slash` | `Icons.Filled.VisibilityOff` |
| Compose (FAB) | `square.and.pencil` | `Icons.Filled.Edit` |
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Notifications (tab) | `bell.fill` | `Icons.Filled.Notifications` |
| Profile (tab) | `person.crop.square` | `Icons.Filled.AccountBox` |
| Visibility public | `globe` | `Icons.Filled.Public` |
| Visibility followers | `lock.fill` | `Icons.Filled.Lock` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; flip system-bar icon contrast with the theme (light icons on `#191B22`, dark on `#FFFFFF`). Use `Scaffold` insets so the FAB clears the gesture nav.
- **Light + dark**: provide both schemes via `isSystemInDarkTheme()`; never collapse the dark canvas to `#000000` — keep the `#191B22` blue cast that is Mastodon's identity.
- **Font scaling**: `sp` honors the user's scale — keep it generous on toot body and names (reading app). Pin only tab labels and "SHOW MORE".
- **TalkBack**: merge a toot's name/handle/time/body with `Modifier.semantics(mergeDescendants = true)`; expose boost/favourite `stateDescription` ("Boosted"/"Favourited"); make the CW card a disclosure with a "Show content warning" action and announce the revealed body.
- **Touch targets**: Material guidance is 48.dp minimum. Action icons are 18.dp glyphs — give each a 44–48.dp hit area via `defaultMinSize` (shown above).
- **Contrast**: `#606984` on `#FFFFFF` and `#9BAEC8` on `#191B22` both pass WCAG AA at 14sp+. Validate 10sp tab labels and bump if targeting strict compliance.
- **Dynamic color**: do **not** enable Material You — Mastodon's brand requires the fixed purple accent and the blue-gray dark canvas regardless of wallpaper.
