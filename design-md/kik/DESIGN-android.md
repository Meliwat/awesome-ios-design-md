# Kik (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Kik's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the bubble + S/D/R receipt + Kik Code, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Kik's modern Blue brand, the gray/blue bubble pair, the single-letter S/D/R receipt, username-first identity, bots, Kik Codes) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `sp`/`dp`. Kik is light-first; a neutral near-black dark scheme is provided.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars / Kik Code art.

## 1. Color Tokens

```kotlin
// ui/theme/KikColors.kt
import androidx.compose.ui.graphics.Color

object KikColors {
    // Brand Kik Blue (modern) + Green (heritage)
    val Blue        = Color(0xFF00B0F0)
    val BlueDeep    = Color(0xFF0093C8)
    val BluePressed = Color(0xFF0086B8)
    val Cyan        = Color(0xFF4FCBF7)
    val GreenLegacy = Color(0xFF82BC23) // heritage accent only
    val InkOnBlue   = Color(0xFF002A36)

    // Canvas & Surfaces (Light)
    val Canvas      = Color(0xFFFFFFFF)
    val Incoming    = Color(0xFFE9EAEC)
    val SurfaceGray = Color(0xFFF3F4F6)
    val RowPressed  = Color(0xFFE7E8EB)
    val Divider     = Color(0xFFE1E2E5)

    // Canvas & Surfaces (Dark) — neutral near-black
    val DarkCanvas   = Color(0xFF121316)
    val DarkSurface1 = Color(0xFF1B1D21)
    val DarkSurface2 = Color(0xFF25282D)
    val DarkDivider  = Color(0xFF31343A)

    // Text
    val TextPrimary       = Color(0xFF16181B)
    val TextPrimaryDark   = Color(0xFFF1F2F4)
    val TextSecondary     = Color(0xFF6C6F77)
    val TextSecondaryDark = Color(0xFF9CA0A8)
    val TextTertiary      = Color(0xFF9A9DA5)
    val TextTertiaryDark  = Color(0xFF686C74)

    // Semantic
    val Error = Color(0xFFF0473E)
    val Link  = Color(0xFF0093C8)
}
```

Wire it into both schemes. Kik is light-first; dark uses a neutral near-black `#121316`, never color-cast.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val KikLight = lightColorScheme(
    primary        = KikColors.Blue,
    onPrimary      = KikColors.InkOnBlue,   // dark-cyan ink on bright blue (AA)
    background     = KikColors.Canvas,
    onBackground   = KikColors.TextPrimary,
    surface        = KikColors.SurfaceGray,
    onSurface      = KikColors.TextPrimary,
    surfaceVariant = KikColors.Incoming,
    outline        = KikColors.Divider,
    error          = KikColors.Error,
)

private val KikDark = darkColorScheme(
    primary        = KikColors.Blue,
    onPrimary      = KikColors.InkOnBlue,
    background      = KikColors.DarkCanvas,
    onBackground   = KikColors.TextPrimaryDark,
    surface        = KikColors.DarkSurface1,
    onSurface      = KikColors.TextPrimaryDark,
    surfaceVariant = KikColors.DarkSurface2,
    outline        = KikColors.DarkDivider,
    error          = KikColors.Error,
)

@Composable
fun KikTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) KikDark else KikLight,
    typography = KikTypography,
    content = content,
)
```

## 2. Typography (M3)

Kik's brand face is proprietary; bundle **Inter** (SIL OFL) in `res/font/` as the closest free substitute. Heavy 700/800 titles & username headline; body 400 (outgoing bumps to 500 for ink crispness on Blue). pt → sp 1:1.

```kotlin
// ui/theme/KikType.kt
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

object KikText {
    val LargeTitle  = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 38.sp, letterSpacing = (-0.4).sp)
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Title3      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 27.sp, letterSpacing = (-0.2).sp)
    val Headline    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.2).sp)
    val RowTitle    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val BodyOut     = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 14.sp, lineHeight = 19.sp)
    val BodyEmph    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 19.sp)
    val Preview     = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 13.sp, lineHeight = 18.sp)
    val Footnote    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 13.sp, lineHeight = 18.sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 15.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Receipt     = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.1.sp)
}

val KikTypography = Typography(
    headlineLarge = KikText.LargeTitle,
    headlineMedium = KikText.ScreenTitle,
    titleMedium   = KikText.Headline,
    bodyLarge     = KikText.Body,
    labelSmall    = KikText.Tab,
)
```

## 3. Signature Components

### Message Bubble (in / out, with tail)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

enum class SDR { Sending, Sent, Delivered, Read, Failed }

@Composable
fun KikBubble(text: String, outgoing: Boolean, tailEnd: Boolean, timestamp: String, receipt: SDR, dark: Boolean) {
    val shape = RoundedCornerShape(
        topStart = 16.dp, topEnd = 16.dp,
        bottomStart = if (!outgoing && tailEnd) 5.dp else 16.dp,
        bottomEnd   = if ( outgoing && tailEnd) 5.dp else 16.dp,
    )
    Column(Modifier.fillMaxWidth(), horizontalAlignment = if (outgoing) Alignment.End else Alignment.Start) {
        Box(
            Modifier
                .fillMaxWidth(0.80f)
                .wrapContentWidth(if (outgoing) Alignment.End else Alignment.Start)
                .clip(shape)
                .background(if (outgoing) KikColors.Blue else if (dark) KikColors.DarkSurface2 else KikColors.Incoming)
                .padding(horizontal = 13.dp, vertical = 8.dp)
        ) {
            Text(text, style = if (outgoing) KikText.BodyOut else KikText.Body,
                color = if (outgoing) KikColors.InkOnBlue else if (dark) KikColors.TextPrimaryDark else KikColors.TextPrimary)
        }
        if (outgoing) {
            Row(Modifier.padding(top = 3.dp, end = 4.dp), verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                Text(timestamp, fontSize = 10.sp, color = if (dark) KikColors.TextTertiaryDark else KikColors.TextTertiary)
                SDRReceipt(receipt, dark)
            }
        }
    }
}
```

### S / D / R Receipt

```kotlin
@Composable
fun SDRReceipt(state: SDR, dark: Boolean) {
    val letter = when (state) {
        SDR.Sending -> "…"; SDR.Sent -> "S"; SDR.Delivered -> "D"; SDR.Read -> "R"; SDR.Failed -> "!"
    }
    val color = when (state) {
        SDR.Sending, SDR.Sent -> if (dark) KikColors.TextTertiaryDark else KikColors.TextTertiary
        SDR.Delivered -> if (dark) KikColors.TextSecondaryDark else KikColors.TextSecondary
        SDR.Read -> KikColors.Blue   // the one color moment
        SDR.Failed -> KikColors.Error
    }
    // No animation — instant glyph swap; only the R recolor reads as an event
    Text(letter, style = KikText.Receipt, color = color)
}
```

### Conversation Row (person vs bot)

```kotlin
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.graphics.Brush

@Composable
fun KikConvRow(
    name: String, preview: String, time: String, isBot: Boolean,
    unread: Int?, receipt: SDR?, gradient: List<Color>, dark: Boolean,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp, vertical = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(
            Modifier.size(48.dp)
                .clip(if (isBot) RoundedCornerShape(14.dp) else CircleShape)
                .background(Brush.linearGradient(gradient)),
            contentAlignment = Alignment.Center,
        ) { Text(name.take(1), color = Color.White, fontSize = 16.sp, fontWeight = FontWeight.Bold) }

        Column(Modifier.weight(1f)) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                Text(name, style = KikText.RowTitle, maxLines = 1,
                    color = if (dark) KikColors.TextPrimaryDark else KikColors.TextPrimary)
                if (isBot) {
                    Box(
                        Modifier.clip(RoundedCornerShape(4.dp))
                            .background(if (dark) KikColors.DarkSurface2 else KikColors.SurfaceGray)
                            .padding(horizontal = 5.dp, vertical = 1.dp)
                    ) { Text("BOT", fontSize = 9.sp, fontWeight = FontWeight.Bold,
                        color = if (dark) KikColors.TextSecondaryDark else KikColors.TextSecondary) }
                }
            }
            Text(preview, style = KikText.Preview, maxLines = 1,
                color = if (dark) KikColors.TextSecondaryDark else KikColors.TextSecondary)
        }

        Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(5.dp)) {
            Text(time, fontSize = 11.sp, color = if (dark) KikColors.TextTertiaryDark else KikColors.TextTertiary)
            when {
                unread != null -> Box(
                    Modifier.clip(CircleShape).background(KikColors.Blue).defaultMinSize(18.dp, 18.dp).padding(horizontal = 5.dp),
                    contentAlignment = Alignment.Center,
                ) { Text("$unread", fontSize = 11.sp, fontWeight = FontWeight.Bold, color = KikColors.InkOnBlue) }
                receipt != null -> SDRReceipt(receipt, dark)
            }
        }
    }
}
```

### Kik Code

```kotlin
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.PathEffect
import androidx.compose.ui.graphics.drawscope.Stroke

@Composable
fun KikCode(modifier: Modifier = Modifier) {
    // Flat, high-contrast — NO shadow (must scan reliably)
    Box(
        modifier.size(120.dp).clip(RoundedCornerShape(24.dp)).background(KikColors.Blue),
        contentAlignment = Alignment.Center,
    ) {
        Canvas(Modifier.size(96.dp)) {
            drawCircle(
                color = Color.White,
                radius = size.minDimension / 2 - 3.dp.toPx(),
                center = center,
                style = Stroke(width = 6.dp.toPx(), pathEffect = PathEffect.dashPathEffect(floatArrayOf(2.dp.toPx(), 6.dp.toPx()))),
            )
        }
        Box(Modifier.size(44.dp).clip(RoundedCornerShape(12.dp)).background(Color.White))
    }
}
```

### Compose Bar (text "Send" label)

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material3.Icon
import androidx.compose.runtime.*

@Composable
fun KikComposeBar(dark: Boolean, onSend: (String) -> Unit) {
    var text by remember { mutableStateOf("") }
    val empty = text.isBlank()
    Row(
        Modifier.fillMaxWidth()
            .background(if (dark) KikColors.DarkSurface1 else KikColors.Canvas)
            .padding(horizontal = 14.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(Icons.Filled.Add, "Attach", tint = if (dark) KikColors.TextSecondaryDark else KikColors.TextSecondary, modifier = Modifier.size(22.dp))
        Box(
            Modifier.weight(1f).heightIn(min = 36.dp).clip(RoundedCornerShape(18.dp))
                .background(if (dark) KikColors.DarkSurface2 else KikColors.SurfaceGray)
                .padding(horizontal = 14.dp, vertical = 8.dp),
        ) {
            BasicTextField(
                value = text, onValueChange = { text = it },
                textStyle = KikText.Body.copy(color = if (dark) KikColors.TextPrimaryDark else KikColors.TextPrimary),
                decorationBox = { inner ->
                    if (empty) Text("Type a message", style = KikText.Body, color = if (dark) KikColors.TextTertiaryDark else KikColors.TextTertiary)
                    inner()
                },
            )
        }
        Text(
            "Send",
            style = KikText.Receipt.copy(fontSize = 14.sp),
            color = if (empty) (if (dark) KikColors.TextTertiaryDark else KikColors.TextTertiary) else KikColors.Blue,
            modifier = Modifier.clickable(enabled = !empty) { onSend(text); text = "" }.padding(horizontal = 6.dp),
        )
    }
}
```

## 4. Navigation

Kik is text/bot-first — **no call/video tab**. On Android model the strip as a `NavigationBar` with **no tint pill** — active is bright Kik Blue.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun KikBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = KikColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Chats" to Icons.Filled.ChatBubble,
            "People" to Icons.Filled.People,
            "Discover" to Icons.Filled.AutoAwesome,
            "Kik Code" to Icons.Filled.QrCode,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, label, Modifier.size(22.dp)) },
                label = { Text(label, style = KikText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = KikColors.Blue,
                    selectedTextColor = KikColors.Blue,
                    unselectedIconColor = KikColors.TextTertiary,
                    unselectedTextColor = KikColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Kik has none
                ),
            )
        }
    }
}
```

The chat header is a `CenterAlignedTopAppBar`: leading back `ChevronLeft` tinted `Blue`, centered `@username` `Headline`, trailing 32.dp gradient avatar (tap → profile / Kik Code). No call/video icons.

## 5. Motion

Kik motion is soft spring, 0.15–0.32s.

| Moment | Compose recipe |
|--------|----------------|
| Outgoing bubble send | new bubble `slideInVertically { it } + scaleIn(initialScale = 0.88f, spring(dampingRatio = 0.8f))` |
| S/D/R receipt | **no animation** — instant glyph swap; the R-to-Blue recolor is the only "event" |
| List row reorder | `LazyColumn` items `animateItemPlacement(tween(250))` when a chat bumps to the top |
| Bot suggested-reply chips | `AnimatedVisibility` `fadeIn(tween(200)) + slideInVertically { it / 4 }` |
| Kik Code match | plate `animateFloatAsState` scale 1 → 1.1 → 1 pulse, then nav push |
| Tab switch | instant; icon tint `animateColorAsState` `tween(150)` |

```kotlin
// Canonical outgoing-bubble entrance
AnimatedVisibility(
    visible = true,
    enter = slideInVertically(spring(dampingRatio = 0.8f)) { it / 2 } +
            scaleIn(initialScale = 0.88f, animationSpec = spring(dampingRatio = 0.8f)),
) { KikBubble(/* … */) }
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for send and bot-button tap; `HapticFeedbackConstants.CONFIRM` (via `LocalView`) on Kik Code match / friend added. The receipt change is visual only (no haptic, no animation).

## 6. Icons

Kik uses a custom brand glyph set; the closest first-party Compose set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Back | `chevron.left` | `Icons.Filled.ChevronLeft` |
| New chat | `plus` | `Icons.Filled.Add` |
| Scan / camera | `qrcode.viewfinder` | `Icons.Filled.QrCodeScanner` |
| Attachment | `plus` | `Icons.Filled.Add` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Chats (tab) | `bubble.left.fill` | `Icons.Filled.ChatBubble` |
| People (tab) | `person.2.fill` | `Icons.Filled.People` |
| Discover (tab) | `sparkles` | `Icons.Filled.AutoAwesome` |
| Kik Code (tab) | `qrcode` | `Icons.Filled.QrCode` |
| GIF | `photo.stack` | `Icons.Filled.Gif` |
| Web content | `globe` | `Icons.Filled.Public` |
| Failed message | `exclamationmark.circle.fill` | `Icons.Filled.ErrorOutline` |
| Group | `person.3.fill` | `Icons.Filled.Groups` |
| Block / report | `hand.raised.fill` | `Icons.Filled.Block` |
| Settings | `gearshape.fill` | `Icons.Filled.Settings` |
| Share Kik Code | `square.and.arrow.up` | `Icons.Filled.Share` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; spring + `Canvas` comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: `enableEdgeToEdge()`; white canvas wants dark system-bar content (light-content on the neutral dark canvas). Compose bar above the IME — `Modifier.imePadding()` on the thread `LazyColumn`.
- **Font**: Kik's brand face is proprietary — bundle Inter (SIL OFL) in `res/font/`; keep ExtraBold/Bold for titles and the username headline. `sp` honors the user's font scale.
- **Font scaling**: scale titles/headline/row-title/body/previews; pin layout-sensitive text (timestamps, 11sp tab labels, the S/D/R glyph, "BOT" tag) via a fixed-`fontScale` `LocalDensity`; keep the Kik Code plate fixed.
- **TalkBack**: spell receipts as values ("Sent"/"Delivered"/"Read") via `stateDescription`, not the bare letter; bot rows announce "{name}, bot"; usernames read with leading "at"; the Kik Code uses `contentDescription = "Kik Code, double-tap to share"`. The Read-Blue receipt is reinforced by the explicit "R" so it survives color-blindness.
- **Touch targets**: Material guidance 48.dp — give the 22.dp attachment icon and the "Send" text label a 48.dp hit box; list rows / tabs / suggested-reply chips are full-press; the Kik Code plate is fully tappable.
- **Contrast**: dark-cyan ink `#002A36` on bright `#00B0F0` passes WCAG AA — never white on the bright Blue; primary text on `#E9EAEC`/`#25282D` passes AA.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the bubble spring-in (crossfade) and the Kik Code pulse; the receipt has no animation to disable.
- **Dark mode**: invert via the `Dark*` palette — neutral `#121316`, NOT color-cast; Kik Blue `#00B0F0` holds; incoming → `#25282D`, text → `#F1F2F4`; the R receipt stays `#00B0F0` in both modes. Shadows fade on dark, so add a 1dp `DarkDivider` border to floating menus as the elevation cue; keep the Kik Code plate flat & high-contrast for reliable scanning. Do **not** enable Material You `dynamicColorScheme()` — Kik's modern-Blue identity must hold regardless of wallpaper.
