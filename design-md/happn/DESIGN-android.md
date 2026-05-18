# happn (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports happn's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the Crossings timeline + Charm gesture, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (happn's Crossings timeline on a connector spine, the pink Charm heart, the pink→magenta gradient, reserved Crush gold, location+time stamps) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyColumn` for the timeline, Maps Compose markers, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars, and `maps-compose` `4.x` for the Map view. **No Material You** — happn's pink is a fixed brand color and must not be overridden by wallpaper extraction.

## 1. Color Tokens

```kotlin
// ui/theme/HappnColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object HappnColors {
    // Canvas & Surfaces (Dark)
    val Canvas   = Color(0xFF0E0E12) // cool graphite — NOT pure black
    val Surface1 = Color(0xFF18181F)
    val Surface2 = Color(0xFF21212B)
    val Surface3 = Color(0xFF2C2C38)
    val Divider  = Color(0xFF2A2A33) // also the timeline spine

    // Canvas & Surfaces (Light)
    val CanvasLight  = Color(0xFFFFFFFF)
    val Surface1Light = Color(0xFFF6F6F8)
    val Surface2Light = Color(0xFFEDEDF1)
    val DividerLight  = Color(0xFFE6E6EC)

    // Brand
    val Pink      = Color(0xFFFF4865)
    val PinkPress = Color(0xFFE5354F)
    val Magenta   = Color(0xFFE91E63)
    val Rose      = Color(0xFFFF7B93)
    val Gold      = Color(0xFFFFC24B) // Crush / premium ONLY

    // Text
    val TextPrimary      = Color(0xFFF4F4F6)
    val TextSecondary    = Color(0xFFA0A0AE)
    val TextTertiary     = Color(0xFF6C6C7A)
    val TextPrimaryLight = Color(0xFF15151B)
    val OnPink           = Color(0xFFFFFFFF)
    val OnGold           = Color(0xFF1A1A1A)

    // Semantic
    val Success = Color(0xFF4ED9A4)
    val Error   = Color(0xFFFF5C5C)

    // Hero gradient — primary "Say hi" / Crush star / splash
    val HeroGradient = Brush.linearGradient(listOf(Pink, Magenta))
}
```

happn ships both themes; the brand is most recognizable on dark. The brand pink, magenta, gradient, and Crush gold are identical across schemes.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme

private val HappnDark = darkColorScheme(
    primary        = HappnColors.Pink,
    onPrimary      = HappnColors.OnPink,
    secondary      = HappnColors.Magenta,
    background      = HappnColors.Canvas,
    onBackground   = HappnColors.TextPrimary,
    surface        = HappnColors.Surface1,
    onSurface      = HappnColors.TextPrimary,
    surfaceVariant = HappnColors.Surface2,
    outline        = HappnColors.Divider,
    error          = HappnColors.Error,
)

private val HappnLight = lightColorScheme(
    primary        = HappnColors.Pink,
    onPrimary      = HappnColors.OnPink,
    secondary      = HappnColors.Magenta,
    background      = HappnColors.CanvasLight,
    onBackground   = HappnColors.TextPrimaryLight,
    surface        = HappnColors.Surface1Light,
    onSurface      = HappnColors.TextPrimaryLight,
    surfaceVariant = HappnColors.Surface2Light,
    outline        = HappnColors.DividerLight,
    error          = HappnColors.Error,
)

@Composable
fun HappnTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) HappnDark else HappnLight,
    typography  = HappnTypography,
    content     = content,
)
```

## 2. Typography

happn pairs **Poppins** (warmth: wordmark, names, headings, buttons, crossing count) with **Inter** (legibility: body, location, chat). Both SIL OFL — drop the TTFs in `res/font/`. Weights 400–700.

```kotlin
// ui/theme/HappnType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Poppins = FontFamily(
    Font(R.font.poppins_semibold, FontWeight.SemiBold),
    Font(R.font.poppins_bold,     FontWeight.Bold),
)
val Inter = FontFamily(
    Font(R.font.inter_regular, FontWeight.Normal),
    Font(R.font.inter_medium,  FontWeight.Medium),
)

object HappnText {
    val Display    = TextStyle(Poppins, fontWeight = FontWeight.Bold,     fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.5).sp)
    val Name       = TextStyle(Poppins, fontWeight = FontWeight.Bold,     fontSize = 26.sp, lineHeight = 30.sp, letterSpacing = (-0.4).sp)
    val Section    = TextStyle(Poppins, fontWeight = FontWeight.SemiBold, fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Subsection = TextStyle(Poppins, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 24.sp)
    val CardName   = TextStyle(Poppins, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Button     = TextStyle(Poppins, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp)
    val Count      = TextStyle(Poppins, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 16.sp)
    val Tab        = TextStyle(Poppins, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp)
    val Body       = TextStyle(Inter, fontWeight = FontWeight.Normal, fontSize = 16.sp, lineHeight = 24.sp)
    val BodyMeta   = TextStyle(Inter, fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 20.sp)
    val Location   = TextStyle(Inter, fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 16.sp)
    val Caption    = TextStyle(Inter, fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 16.sp)
}

val HappnTypography = Typography(
    headlineLarge  = HappnText.Display,
    headlineMedium = HappnText.Name,
    titleLarge     = HappnText.Section,
    bodyMedium     = HappnText.Body,
    labelSmall     = HappnText.Tab,
)
```

## 3. Signature Components

### Crossings Timeline

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.LocationOn
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.drawBehind
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

data class Crossing(
    val id: String,
    val avatarUrl: String,
    val name: String,        // "Camille, 27"
    val place: String,       // "Le Marais"
    val timeAgo: String,     // "11 min ago"
    val crossCount: Int,
    val charmed: Boolean,
)

@Composable
fun CrossingsTimeline(items: List<Crossing>, onCharm: (String) -> Unit) {
    LazyColumn(
        modifier = Modifier.fillMaxSize().background(HappnColors.Canvas),
        contentPadding = PaddingValues(horizontal = 16.dp, vertical = 4.dp),
        verticalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        items(items, key = { it.id }) { c -> CrossingRow(c) { onCharm(c.id) } }
    }
}

@Composable
fun CrossingRow(c: Crossing, onCharm: () -> Unit) {
    Row(Modifier.fillMaxWidth()) {
        // Connector spine + dot
        Box(Modifier.width(18.dp).fillMaxHeight(), contentAlignment = Alignment.TopCenter) {
            Box(
                Modifier
                    .width(2.dp).fillMaxHeight().padding(top = 6.dp)
                    .background(HappnColors.Divider)
            )
            Box(
                Modifier
                    .padding(top = 6.dp).size(12.dp)
                    .clip(CircleShape).background(HappnColors.Pink)
                    .border(4.dp, HappnColors.Pink.copy(alpha = 0.18f), CircleShape)
            )
        }

        // Card
        Row(
            Modifier
                .weight(1f).padding(start = 8.dp)
                .clip(RoundedCornerShape(18.dp))
                .background(HappnColors.Surface1)
                .border(1.dp, HappnColors.Divider, RoundedCornerShape(18.dp))
                .padding(12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            AsyncImage(
                model = c.avatarUrl, contentDescription = null,
                modifier = Modifier.size(56.dp).clip(RoundedCornerShape(16.dp)),
            )
            Column(Modifier.weight(1f)) {
                Text(c.name, style = HappnText.CardName, color = HappnColors.TextPrimary,
                    maxLines = 1, overflow = TextOverflow.Ellipsis)
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                    Icon(Icons.Filled.LocationOn, null, tint = HappnColors.TextTertiary, modifier = Modifier.size(11.dp))
                    Text("${c.place} · ${c.timeAgo}", style = HappnText.Location, color = HappnColors.TextSecondary,
                        maxLines = 1, overflow = TextOverflow.Ellipsis)
                }
                Spacer(Modifier.height(5.dp))
                Text(
                    if (c.crossCount == 1) "You crossed paths once" else "You crossed paths ${c.crossCount} times",
                    style = HappnText.Count, color = HappnColors.Pink,
                )
            }
            CharmButton(charmed = c.charmed, onCharm = onCharm, size = 40.dp)
        }
    }
}
```

### Charm Button (heart)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.outlined.FavoriteBorder
import androidx.compose.runtime.rememberCoroutineScope
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.Dp
import kotlinx.coroutines.launch

@Composable
fun CharmButton(charmed: Boolean, onCharm: () -> Unit, size: Dp = 40.dp) {
    val scope = rememberCoroutineScope()
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current

    Box(
        Modifier
            .size(size)
            .then(if (charmed) Modifier.shadow(8.dp, CircleShape, spotColor = HappnColors.Pink.copy(alpha = 0.5f)) else Modifier)
            .scale(scale.value)
            .clip(CircleShape)
            .background(if (charmed) HappnColors.Pink else HappnColors.Surface2)
            .then(if (charmed) Modifier else Modifier.border(1.dp, HappnColors.Divider, CircleShape))
            .clickable {
                scope.launch {
                    scale.animateTo(1.25f, spring(dampingRatio = 0.45f))
                    scale.animateTo(1f, spring(dampingRatio = 0.7f))
                }
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onCharm()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            if (charmed) Icons.Filled.Favorite else Icons.Outlined.FavoriteBorder,
            contentDescription = if (charmed) "Charm sent" else "Send a Charm",
            tint = if (charmed) HappnColors.OnPink else HappnColors.TextSecondary,
            modifier = Modifier.size(size * 0.45f),
        )
    }
}
```

### Crush Celebration

```kotlin
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.animation.core.tween

@Composable
fun CrushCelebration(
    leftAvatar: Brush, rightAvatar: Brush, onStartChat: () -> Unit,
) {
    var together by remember { mutableStateOf(false) }
    val offset by animateDpAsState(if (together) 28.dp else 160.dp, tween(360), label = "crush")
    val haptics = LocalHapticFeedback.current

    LaunchedEffect(Unit) {
        together = true
        haptics.performHapticFeedback(HapticFeedbackType.LongPress) // success analog
    }

    Box(Modifier.fillMaxSize().background(Color.Black.copy(alpha = 0.92f)), contentAlignment = Alignment.Center) {
        Column(horizontalAlignment = Alignment.CenterHorizontally, verticalArrangement = Arrangement.spacedBy(28.dp)) {
            Row {
                Box(Modifier.offset(x = -offset).size(96.dp).clip(CircleShape).background(leftAvatar))
                Box(Modifier.offset(x = offset).size(96.dp).clip(CircleShape).background(rightAvatar))
            }
            Text("It's a Crush!", style = HappnText.Display, color = HappnColors.TextPrimary)
            // Gold sparkle layer here — the ONLY place HappnColors.Gold appears (besides premium)
            Box(
                Modifier
                    .clip(RoundedCornerShape(999.dp))
                    .background(HappnColors.HeroGradient)
                    .clickable(onClick = onStartChat)
                    .padding(horizontal = 30.dp, vertical = 15.dp),
            ) { Text("Start chatting", style = HappnText.Button, color = HappnColors.OnPink) }
        }
    }
}
```

### Map Crossing Pin (teardrop)

```kotlin
import androidx.compose.ui.draw.rotate

@Composable
fun CrossingPin(kind: PinKind) {
    Box(
        Modifier
            .size(44.dp)
            .rotate(45f)
            .clip(RoundedCornerShape(topStart = 22.dp, topEnd = 22.dp, bottomEnd = 22.dp, bottomStart = 6.dp))
            .then(
                when (kind) {
                    PinKind.Standard -> Modifier.background(HappnColors.Pink)
                    PinKind.Mutual   -> Modifier.background(HappnColors.HeroGradient)
                    PinKind.Ghost    -> Modifier.background(HappnColors.Surface2).border(1.dp, HappnColors.Divider, RoundedCornerShape(topStart = 22.dp, topEnd = 22.dp, bottomEnd = 22.dp, bottomStart = 6.dp))
                }
            ),
        contentAlignment = Alignment.Center,
    ) {
        Box(
            Modifier
                .size(30.dp).rotate(-45f).clip(CircleShape)
                .background(if (kind == PinKind.Ghost) HappnColors.TextTertiary else Color.White)
        )
    }
}

enum class PinKind { Standard, Mutual, Ghost }
```

Use as a `maps-compose` `MarkerComposable` content; keep the +45° body / -45° inner counter-rotation so the dot stays upright.

## 4. Navigation

happn has a transparent top bar and a 4-tab bottom strip with **no tint pill** — active is the pink icon + pink label.

```kotlin
@Composable
fun HappnBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = Color(0xFF0E0E12).copy(alpha = 0.94f),
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            Icons.Filled.Favorite to "Timeline",
            Icons.Filled.Map to "Map",
            Icons.Filled.ChatBubble to "Chats",
            Icons.Filled.Person to "Profile",
        )
        items.forEachIndexed { i, (icon, label) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, modifier = Modifier.size(23.dp)) },
                label = { Text(label, style = HappnText.Tab) },
                alwaysShowLabel = true,
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = HappnColors.Pink,       // pink icon + pink label
                    selectedTextColor = HappnColors.Pink,
                    unselectedIconColor = HappnColors.TextTertiary,
                    unselectedTextColor = HappnColors.TextTertiary,
                    indicatorColor = Color.Transparent,         // NO Material pill — happn has none
                ),
            )
        }
    }
}
```

The top bar is a transparent `Row`: leading wordmark "happ**n**" (Poppins 22sp Bold, trailing "n" in `HappnColors.Pink`), trailing search icon (17dp in a 34dp `Surface2` circle).

## 5. Motion

happn motion is warm and emotional — the Charm bounce and the gold Crush takeover are the signatures.

| Moment | Compose recipe |
|--------|----------------|
| Charm tap | `scale.animateTo(1.25f, spring(0.45f))` then `animateTo(1f, spring(0.7f))` + long-press haptic; ghost → filled |
| Crush reveal | avatars `animateDpAsState` from edge offset to center `tween(360)`; gold sparkle layer; success haptic |
| Timeline new crossing | `LazyColumn` item `animateItemPlacement()` + `AnimatedVisibility(slideInVertically + fadeIn, tween(240))` |
| Dot pulse (fresh) | halo `Animatable` scale 1 → 1.4 → 1 once, `tween(300)` each leg |
| Card press | `Modifier.scale` 0.98 via `interactionSource`, `tween(80)` |
| Tab switch | icon tint cross-dissolves to pink `tween(180)`; no pill slide |
| Map pin tap | pin `offset` y -6dp + `spring`, then profile `ModalBottomSheet` up |
| Sheet present | `ModalBottomSheet` slide-up (Material default) + scrim |

```kotlin
// Charm bounce — the canonical happn motion
scope.launch {
    scale.animateTo(1.25f, spring(dampingRatio = 0.45f))
    scale.animateTo(1f,    spring(dampingRatio = 0.70f))
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for Charm sent and the Crush moment; a lighter `HapticFeedbackConstants.CLOCK_TICK` (via `LocalView`) for tab switch and map-pin tap.

## 6. Icons

happn iconography is rounded/simple; `androidx.compose.material:material-icons-extended` covers it.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Timeline (tab) | `heart.text.square` | `Icons.Filled.Favorite` |
| Map (tab) | `map` | `Icons.Filled.Map` |
| Chats (tab) | `bubble.left.and.bubble.right` | `Icons.Filled.ChatBubble` |
| Profile (tab) | `person` | `Icons.Filled.Person` |
| Charm (heart) | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Crush (star) | `star.fill` | `Icons.Filled.Star` |
| Location stamp | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Send message | `arrow.up.circle.fill` | `Icons.Filled.ArrowCircleUp` |
| FlashNote | `text.bubble` | `Icons.Filled.ChatBubbleOutline` |
| Block / report | `exclamationmark.shield` | `Icons.Filled.Shield` |
| Settings | `gearshape` | `Icons.Filled.Settings` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `LazyColumn` `animateItemPlacement`, `maps-compose` markers comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the graphite canvas wants light system-bar icons on dark, dark on light. The wordmark + first card sit below the camera cutout; the last card scrolls clear of the nav inset.
- **Both themes**: drive scheme by `isSystemInDarkTheme()`; the brand is most recognizable on dark. Keep `Pink`, `Magenta`, `HeroGradient`, and `Gold` identical across schemes — only canvas/surface/text swap. Do **not** enable `dynamicColorScheme()` — happn pink is a fixed brand color and must hold regardless of wallpaper.
- **Font scaling**: `sp` honors the user's scale — keep it on display, name, section, body. Pin the crossing count, 10sp tab labels, and location/time meta via a fixed `Density(fontScale = 1f)` `CompositionLocalProvider` so the timeline row doesn't reflow and misalign the connector dot.
- **Card name**: `maxLines = 1` + `overflow = Ellipsis` so a long name never pushes the card to 2 lines and breaks dot alignment.
- **TalkBack**: give each `CrossingRow` `Modifier.semantics { contentDescription = "Crossed paths with ${c.name}, near ${c.place}, ${c.timeAgo}, ${c.crossCount} times" }`; label the Charm button "Send a Charm to {name}" / "Charm sent"; the Crush screen announces "It's a Crush with {name}". The crossing count must be exposed to screen readers — it is meaning, not decoration.
- **Touch targets**: Material guidance is 48dp — give the 40dp Charm a 48dp hit via padding; the 44dp map pin a 48dp hit; top-bar search a 48dp hit; the full crossing card is tappable (→ profile).
- **Contrast**: `#F4F4F6` on `#0E0E12` is strong; `#FFFFFF` on `#FF4865` passes AA for the Charm; the pink crossing count (`#FF4865` on `#18181F`) passes for 12sp semibold — keep the weight.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the dot pulse, the Charm 1.25 overshoot (use a fill `Crossfade`), and the Crush avatar slide; keep the haptic.
- **Gold discipline**: `HappnColors.Gold` is reserved for the Crush celebration and premium upsell only — never use it as a status, accent, or TalkBack highlight color elsewhere.
