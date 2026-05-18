# Locket (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Locket's warm cream-gold visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s for the capture button, square viewfinder, and friends history grid, plus the home-screen widget (Glance) — the widget IS the product.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Locket's warm cream-gold world, the square 40dp-corner friend photo, the big white capture button with a gold ring, the friends history grid, widget-first) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, **Glance** `GlanceAppWidget` instead of WidgetKit, `dp`/`sp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+`, and `androidx.glance:glance-appwidget` for the home-screen widget. Locket is **warm-by-design** — there is no dark scheme; the cozy cream-gold IS the identity.

## 1. Color Tokens

```kotlin
// ui/theme/LocketColors.kt
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color

object LocketColors {
    // Brand
    val Gold        = Color(0xFFFFB02E)
    val Amber       = Color(0xFFFF9E2C)
    val Deep        = Color(0xFFF08A1D) // on-cream accent
    val GoldPressed = Color(0xFFDC7A12)
    val OnGold      = Color(0xFF3A2400)

    // Surfaces
    val Cream    = Color(0xFFFFF7EC)
    val BgTop    = Color(0xFFFFE9CC)
    val BgBottom = Color(0xFFFFD9A8)
    val White    = Color(0xFFFFFFFF)
    val Surface  = Color(0xFFFFF1DD)
    val Divider  = Color(0xFFF2E2C9)

    // Text
    val TextPrimary   = Color(0xFF2C2014) // warm brown — NOT black
    val TextSecondary = Color(0xFF8A7A63)
    val TextTertiary  = Color(0xFFB8A88E)

    // Accent
    val Coral = Color(0xFFFF7A59)

    // Semantic
    val Success = Color(0xFF38C172)
    val Error   = Color(0xFFFF5A5A)

    // World background (full-bleed, every screen)
    val World = Brush.verticalGradient(listOf(BgTop, BgBottom))
    val AvatarGradient = Brush.linearGradient(listOf(Gold, Coral))

    // Warm honey-tinted shadow — NEVER neutral gray
    val Honey     = Color(0xFFB4781E) // rgba(180,120,30)
    val HoneyDeep = Color(0xFFA06414) // rgba(160,100,20)
}
```

Locket is warm-by-design — wire a single warm scheme. Never enable Material You `dynamicColorScheme()` and never build a dark scheme: the cream-gold identity IS the brand and must hold regardless of the system theme.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.lightColorScheme

private val LocketScheme = lightColorScheme(
    primary        = LocketColors.Gold,
    onPrimary      = LocketColors.OnGold,
    background      = LocketColors.Cream,
    onBackground    = LocketColors.TextPrimary,
    surface         = LocketColors.White,
    onSurface       = LocketColors.TextPrimary,
    surfaceVariant  = LocketColors.Surface,
    outline         = LocketColors.Divider,
    error           = LocketColors.Error,
)

@Composable
fun LocketTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = LocketScheme, typography = LocketTypography, content = content)
```

## 2. Typography

Locket's UI face is **Poppins** (SIL OFL — drop the TTFs in `res/font/`), warm 600–800.

```kotlin
// ui/theme/LocketType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Poppins = FontFamily(
    Font(R.font.poppins_regular,   FontWeight.Normal),
    Font(R.font.poppins_medium,    FontWeight.Medium),
    Font(R.font.poppins_semibold,  FontWeight.SemiBold),
    Font(R.font.poppins_bold,      FontWeight.Bold),
    Font(R.font.poppins_extrabold, FontWeight.ExtraBold),
)

object LocketText {
    val Display   = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val Screen    = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section   = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Sender    = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 24.sp)
    val Caption   = TextStyle(Poppins, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 22.sp)
    val Body      = TextStyle(Poppins, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 22.sp)
    val BigNum    = TextStyle(Poppins, fontWeight = FontWeight.ExtraBold, fontSize = 28.sp, lineHeight = 28.sp, letterSpacing = (-0.5).sp)
    val Meta      = TextStyle(Poppins, fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 17.sp)
    val Label     = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.4.sp)
    val Button    = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.2.sp)
    val Pill      = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 14.sp, lineHeight = 14.sp)
    val Tab       = TextStyle(Poppins, fontWeight = FontWeight.Bold,      fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
}

val LocketTypography = Typography(
    headlineLarge = LocketText.Display,
    headlineMedium = LocketText.Screen,
    titleLarge    = LocketText.Section,
    titleMedium   = LocketText.Sender,
    bodyMedium    = LocketText.Body,
    labelSmall    = LocketText.Tab,
)
```

## 3. Signature Components

### World Background

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier

@Composable
fun LocketWorld(content: @Composable () -> Unit) {
    Box(Modifier.fillMaxSize().background(LocketColors.World)) { content() }
}
```

### Capture Button (the signature interaction)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Surface
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.alpha
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun CaptureButton(onCapture: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.92f else 1f,
        spring(dampingRatio = Spring.DampingRatioMediumBouncy), label = "capScale")
    val pulse = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()

    Box(contentAlignment = Alignment.Center) {
        // expanding gold ring pulse
        Box(
            Modifier
                .size(84.dp)
                .scale(1f + pulse.value * 0.6f)
                .alpha(1f - pulse.value)
                .border(4.dp, LocketColors.Gold, CircleShape)
        )
        Box(
            Modifier
                .size(84.dp).scale(scale)
                .shadow(10.dp, CircleShape, spotColor = LocketColors.Honey, ambientColor = LocketColors.Honey)
                .clip(CircleShape).background(LocketColors.White)
                .border(5.dp, LocketColors.White.copy(alpha = 0.55f), CircleShape)
                .clickable(interactionSource = interaction, indication = null) {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                    scope.launch {
                        pulse.snapTo(0f)
                        pulse.animateTo(1f, tween(250))
                        pulse.snapTo(0f)
                    }
                    onCapture()
                },
            contentAlignment = Alignment.Center,
        ) {
            Box(Modifier.size(66.dp).border(4.dp, LocketColors.Gold, CircleShape))
        }
    }
}
```

### Square Viewfinder / Friend Photo

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun FriendPhoto(imageUrl: String?, sender: String, caption: String?) {
    Box(
        Modifier
            .fillMaxWidth().aspectRatio(1f)
            .shadow(24.dp, RoundedCornerShape(40.dp), spotColor = LocketColors.HoneyDeep, ambientColor = LocketColors.HoneyDeep)
            .clip(RoundedCornerShape(40.dp)),
    ) {
        if (imageUrl != null) {
            AsyncImage(model = imageUrl, contentDescription = "Photo from $sender",
                modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
        } else {
            Box(Modifier.fillMaxSize().background(
                Brush.linearGradient(listOf(Color(0xFFF5B96B), Color(0xFFC56A22)))))
        }

        // sender chip (top-left, warm frosted — emulate blur with a translucent warm scrim)
        Row(
            Modifier
                .align(Alignment.TopStart).padding(16.dp)
                .clip(RoundedCornerShape(50))
                .background(Color(0xFF2C2014).copy(alpha = 0.32f))
                .padding(start = 6.dp, end = 14.dp, top = 6.dp, bottom = 6.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Box(Modifier.size(24.dp).clip(CircleShape).background(LocketColors.AvatarGradient))
            Text("from $sender", style = LocketText.Pill, color = Color.White)
        }

        if (caption != null) {
            Text(
                caption, style = LocketText.Caption, color = Color.White,
                modifier = Modifier
                    .align(Alignment.BottomCenter).padding(bottom = 18.dp)
                    .clip(RoundedCornerShape(16.dp))
                    .background(Color(0xFF2C2014).copy(alpha = 0.36f))
                    .padding(horizontal = 16.dp, vertical = 8.dp),
            )
        }
    }
}
```

### Friends Photo-History Grid

```kotlin
import androidx.compose.foundation.lazy.grid.GridCells
import androidx.compose.foundation.lazy.grid.LazyVerticalGrid
import androidx.compose.foundation.lazy.grid.items

@Composable
fun HistoryGrid(tints: List<Brush>) {
    LazyVerticalGrid(
        columns = GridCells.Fixed(4),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        items(tints) { brush ->
            Box(
                Modifier.aspectRatio(1f).clip(RoundedCornerShape(14.dp)).background(brush),
            ) {
                Box(
                    Modifier
                        .align(Alignment.BottomStart).padding(4.dp)
                        .size(14.dp).clip(CircleShape)
                        .background(LocketColors.TextPrimary.copy(alpha = 0.4f))
                        .border(1.5.dp, Color.White, CircleShape)
                )
            }
        }
    }
}
```

### Friends Pill + Primary Button

```kotlin
@Composable
fun FriendsPill(count: Int) {
    Row(
        Modifier
            .shadow(6.dp, RoundedCornerShape(50), spotColor = LocketColors.Honey, ambientColor = LocketColors.Honey)
            .clip(RoundedCornerShape(50)).background(LocketColors.White)
            .padding(start = 10.dp, end = 16.dp, top = 8.dp, bottom = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Row {
            repeat(3) { i ->
                Box(
                    Modifier
                        .offset(x = if (i == 0) 0.dp else (-8 * i).dp)
                        .size(22.dp).clip(CircleShape)
                        .background(LocketColors.AvatarGradient)
                        .border(2.dp, Color.White, CircleShape)
                )
            }
        }
        Text("$count friends", style = LocketText.Pill, color = LocketColors.TextPrimary)
    }
}

@Composable
fun LocketPrimaryButton(title: String, onClick: () -> Unit) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    Box(
        Modifier
            .shadow(8.dp, RoundedCornerShape(50), spotColor = LocketColors.Honey, ambientColor = LocketColors.Honey)
            .clip(RoundedCornerShape(50))
            .background(if (pressed) LocketColors.GoldPressed else LocketColors.Gold)
            .clickable(interactionSource = interaction, indication = null) { onClick() }
            .padding(vertical = 14.dp, horizontal = 28.dp),
    ) {
        Text(title, style = LocketText.Button, color = LocketColors.OnGold)
    }
}
```

## 4. Camera Screen

The home screen is the square viewfinder + capture row + a 4-up history strip. Bind the viewfinder to CameraX `PreviewView` via `AndroidView`, but keep the square 40dp-rounded clip and all chrome in Compose.

```kotlin
@Composable
fun CameraScreen(historyTints: List<Brush>) {
    LocketWorld {
        Column(
            Modifier.fillMaxSize().padding(horizontal = 18.dp),
            verticalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Spacer(Modifier.height(8.dp))
            FriendPhoto(imageUrl = null, sender = "Ava", caption = "golden hour ✨")
            Row(
                Modifier.fillMaxWidth().padding(vertical = 14.dp),
                horizontalArrangement = Arrangement.SpaceAround,
                verticalAlignment = Alignment.CenterVertically,
            ) {
                SideControl(Icons.Filled.Cameraswitch)
                CaptureButton(onCapture = { /* capture + send to friends */ })
                SideControl(Icons.Filled.FlashOn)
            }
            Text("FROM YOUR FRIENDS", style = LocketText.Label, color = LocketColors.TextSecondary)
            HistoryGrid(historyTints.take(4))
        }
    }
}

@Composable
fun SideControl(icon: androidx.compose.ui.graphics.vector.ImageVector) {
    Box(
        Modifier.size(50.dp).clip(CircleShape).background(Color.White.copy(alpha = 0.6f)),
        contentAlignment = Alignment.Center,
    ) { Icon(icon, null, tint = LocketColors.TextPrimary, modifier = Modifier.size(22.dp)) }
}
```

## 5. The Widget (the real product surface — Glance)

The home-screen widget IS Locket. On Android use **Jetpack Glance** (`GlanceAppWidget`). Update it when a friend posts (push → `LocketWidget().updateAll(context)`); store the latest photo + sender in `DataStore`/an app-internal file.

```kotlin
import androidx.glance.GlanceModifier
import androidx.glance.appwidget.GlanceAppWidget
import androidx.glance.appwidget.cornerRadius
import androidx.glance.appwidget.provideContent
import androidx.glance.background
import androidx.glance.layout.*
import androidx.glance.text.Text
import androidx.glance.text.TextStyle as GlanceTextStyle

class LocketWidget : GlanceAppWidget() {
    override suspend fun provideGlance(context: Context, id: GlanceId) {
        provideContent {
            // load latest { sender, bitmap } from storage
            Box(
                GlanceModifier.fillMaxSize().cornerRadius(24.dp)
                    .background(androidx.glance.color.ColorProvider(LocketColors.Cream)),
                contentAlignment = Alignment.BottomStart,
            ) {
                // Image(provider = ImageProvider(bitmap), ...) full-bleed
                Text(
                    "from Ava",
                    style = GlanceTextStyle(color = androidx.glance.color.ColorProvider(Color.White)),
                    modifier = GlanceModifier.padding(8.dp),
                )
            }
        }
    }
}
```

Treat the "Add Locket to your Home Screen" step (`AppWidgetManager.requestPinAppWidget`) as the core onboarding activation — render an in-app 116dp rounded-24 widget mock with a sample photo + "from {name}" chip first.

## 6. Navigation

Locket keeps chrome minimal: a transparent top bar (icon button + friends pill + icon button) and a transparent 3-slot bottom strip over the warm world. Active is deep amber; no Material tint pill.

```kotlin
@Composable
fun LocketBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = Color.Transparent,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Camera"  to Icons.Filled.PhotoCamera,
            "History" to Icons.Filled.GridView,
            "You"     to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                label = { Text(label, style = LocketText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = LocketColors.Deep,
                    selectedTextColor   = LocketColors.Deep,
                    unselectedIconColor = LocketColors.TextTertiary,
                    unselectedTextColor = LocketColors.TextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill — Locket has none
                ),
            )
        }
    }
}
```

## 7. Motion

Locket motion is warm and gentle — soft springs, no harsh bounces. The capture is the one tactile peak.

| Moment | Compose recipe |
|--------|----------------|
| Capture press | `animateFloatAsState` scale → 0.92 `spring(MediumBouncy)`; gold ring `Animatable` 0→1 `tween(250)` then snap 0 |
| Send | photo `AnimatedVisibility` exit `slideOutVertically { -it } + fadeOut(320)` |
| Incoming photo | `Crossfade(tween(280))` + gentle scale 1.03 → 1.0 |
| History open | shared-element-style scale + fade `spring(dampingRatio = 0.7)` 300ms |
| Reaction | emoji `offset` up 40.dp + `fadeOut` over 500ms; coral heart pulse |
| Friends pill update | avatars stagger `scaleIn` pop |
| Tab switch | warm `Crossfade(tween(220))`; active eases to `Deep` |

```kotlin
// Capture ring pulse — the signature
val pulse = remember { Animatable(0f) }
LaunchedEffect(captureTrigger) {
    pulse.snapTo(0f); pulse.animateTo(1f, tween(250)); pulse.snapTo(0f)
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the satisfying shutter on capture; `HapticFeedbackConstants.CLOCK_TICK` for reactions; `HapticFeedbackConstants.CONFIRM` on "Sent". Keep them gentle and warm.

## 8. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Emoji reactions (❤️ 😂 🔥 😮) render as `Text`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Camera (tab) | `camera` / `camera.fill` | `Icons.Filled.PhotoCamera` |
| History (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| You (tab) | `person` / `person.fill` | `Icons.Filled.Person` |
| Flip camera | `arrow.triangle.2.circlepath.camera` | `Icons.Filled.Cameraswitch` |
| Flash / effects | `bolt` / `sparkles` | `Icons.Filled.FlashOn` / `Icons.Filled.AutoAwesome` |
| Add friend | `person.badge.plus` | `Icons.Filled.PersonAdd` |
| Messages / chat | `bubble.left.fill` | `Icons.Filled.ChatBubble` |
| Send | `paperplane.fill` | `Icons.Filled.Send` |
| Reaction heart | `heart.fill` | `Icons.Filled.Favorite` (tint `Coral`) |
| See all | `chevron.right` | `Icons.Filled.ChevronRight` |
| Add widget | `plus.square.on.square` | `Icons.Filled.Widgets` |
| Settings | `gearshape.fill` | `Icons.Filled.Settings` |
| Close | `xmark` | `Icons.Filled.Close` |

## 9. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; Glance widget + CameraX comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`, `androidx.glance:glance-appwidget`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the warm cream world is light → dark-content system bars. Keep the system nav bar transparent so the warm gradient extends; the capture button must clear gesture-nav insets.
- **Warm-by-design**: build ONLY the warm scheme; do **not** add a dark scheme and do **not** enable Material You `dynamicColorScheme()` — the cream-gold identity IS the brand and must hold regardless of system theme (including the Glance widget background, which stays `#FFF7EC`).
- **Camera**: use CameraX in an `AndroidView`; the viewfinder is always SQUARE (1:1) clipped to a 40dp radius — never rectangular; request `CAMERA` permission only at first capture.
- **Font scaling**: `sp` honors the user's scale on sender names, captions, body. Pin layout-critical text (tab labels, uppercase labels, the capture-button chrome, history-cell dots) — derive from `dp` or fix `fontScale = 1f` for those nodes.
- **TalkBack**: label the viewfinder "Photo from {sender}{, caption}"; the capture button "Take a photo, sends to your {n} friends"; history cells "Photo from {sender}, {time}"; the Glance widget exposes `contentDescription` with sender + "Locket photo".
- **Touch targets**: the capture button is 84.dp (generous — it's the primary action); give 24.dp tab icons a 48.dp hit; side controls are 50.dp.
- **Contrast**: `#2C2014` on `#FFF7EC` passes WCAG AA; `#3A2400` on `#FFB02E` passes; white on the dark warm chips passes — use `#F08A1D` (not `#FFB02E`) for small accent text on cream.
- **Reduce motion**: when `ANIMATOR_DURATION_SCALE == 0`, replace the capture ring pulse + send slide with a quick `Crossfade`; keep it warm and gentle.
- **Privacy**: photos go only to a tiny confirmed circle; never auto-capture; no public feed — keep the intimate model intact. The widget needs background data update on push only.
