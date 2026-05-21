# Threema (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Threema's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the signature trust-level indicator, the QR / verify surface, message bubbles, the bottom bar, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Threema's calm white canvas, the single Threema Green, the red/orange/green trust dots, the QR verify ritual, monospaced identity strings) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a UIKit sheet, CameraX/ML Kit for QR, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, CameraX + ML Kit barcode scanning for QR, and ZXing for QR generation. Threema has a fixed brand palette (no Material You extraction). It is light-mode-first; a full dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/ThreemaColors.kt
import androidx.compose.ui.graphics.Color

object ThreemaColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceGray    = Color(0xFFF4F4F6)
    val SurfacePressed = Color(0xFFE8E8EB)
    val Divider        = Color(0xFFE2E2E5)
    val BubbleIn       = Color(0xFFEBEBED)
    val BubbleOut      = Color(0xFFD6F0DC)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF121212) // calm charcoal — NOT pure black
    val DarkSurface1 = Color(0xFF1C1C1E)
    val DarkSurface2 = Color(0xFF262628)
    val DarkDivider  = Color(0xFF2C2C2E)
    val BubbleOutDk  = Color(0xFF0C5E22)

    // Text
    val TextPrimary       = Color(0xFF111113) // near-black, NOT pure black
    val TextSecondary     = Color(0xFF6B6B70)
    val TextTertiary      = Color(0xFFA6A6AB)
    val DarkTextPrimary   = Color(0xFFECECEC)
    val DarkTextSecondary = Color(0xFF9A9A9F)

    // Brand (the single accent)
    val Green        = Color(0xFF088A29)
    val GreenPressed = Color(0xFF066B20)
    val GreenBright  = Color(0xFF1FA53C) // dark-mode brand tint
    val Link         = Color(0xFF4FB477)

    // Trust levels (the signature roles) — NEVER shift between schemes
    val TrustRed    = Color(0xFFE5453A) // Level 1 — unknown
    val TrustOrange = Color(0xFFEF8B2C) // Level 2 — server-matched
    val TrustGreen  = Color(0xFF15A33A) // Level 3 — verified
    val TrustTrack  = Color(0xFFD2D2D7)

    // Semantic
    val Error   = Color(0xFFE5453A)
    val Success = Color(0xFF15A33A)
    val Warning = Color(0xFFEF8B2C)
}

enum class TrustLevel(val level: Int) {
    Unknown(1), ServerMatched(2), Verified(3);

    val color: Color get() = when (this) {
        Unknown       -> ThreemaColors.TrustRed
        ServerMatched -> ThreemaColors.TrustOrange
        Verified      -> ThreemaColors.TrustGreen
    }
    val label: String get() = when (this) {
        Unknown -> "Unknown"; ServerMatched -> "Server-matched"; Verified -> "Verified"
    }
    val contentDescription: String get() = "Trust level: ${label.lowercase()}"
}
```

Wire it into both schemes. Threema is light-first (calm white); the dark scheme uses `#121212` charcoal, never true black. Brand green brightens to `#1FA53C` on dark; trust colors stay constant.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val ThreemaLight = lightColorScheme(
    primary        = ThreemaColors.Green,
    onPrimary      = ThreemaColors.Canvas,
    background      = ThreemaColors.Canvas,
    onBackground   = ThreemaColors.TextPrimary,
    surface        = ThreemaColors.SurfaceGray,
    onSurface      = ThreemaColors.TextPrimary,
    surfaceVariant = ThreemaColors.SurfacePressed,
    outline        = ThreemaColors.Divider,
    error          = ThreemaColors.Error,
)

private val ThreemaDark = darkColorScheme(
    primary        = ThreemaColors.GreenBright, // brightened brand on dark
    onPrimary      = ThreemaColors.Canvas,
    background      = ThreemaColors.DarkCanvas,
    onBackground   = ThreemaColors.DarkTextPrimary,
    surface        = ThreemaColors.DarkSurface1,
    onSurface      = ThreemaColors.DarkTextPrimary,
    surfaceVariant = ThreemaColors.DarkSurface2,
    outline        = ThreemaColors.DarkDivider,
    error          = ThreemaColors.Error,
)

@Composable
fun ThreemaTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) ThreemaDark else ThreemaLight,
    typography  = ThreemaTypography,
    content     = content,
)
```

## 2. Typography

Threema ships **no custom typeface** — it uses the platform system font with the OS font-scale. Identity strings (Threema ID + key fingerprint) use `FontFamily.Monospace` so characters are unambiguous when read aloud during in-person verification.

```kotlin
// ui/theme/ThreemaType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

private val Sys = FontFamily.Default     // system font — no bundled face
private val Mono = FontFamily.Monospace  // identity strings only

object ThreemaText {
    val LargeTitle  = TextStyle(Sys, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val ScreenTitle = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val NavName     = TextStyle(Sys, fontWeight = FontWeight.SemiBold,  fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(Sys, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 22.sp)
    val ListTitle   = TextStyle(Sys, fontWeight = FontWeight.Medium,    fontSize = 15.sp, lineHeight = 20.sp)
    val Secondary   = TextStyle(Sys, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Caption     = TextStyle(Sys, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 15.sp)
    val Button      = TextStyle(Sys, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(Sys, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Badge       = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 12.sp)

    // Identity strings only — monospaced
    val Id      = TextStyle(Mono, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 19.sp, letterSpacing = 1.sp)
    val IdLarge = TextStyle(Mono, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 21.sp, letterSpacing = 1.sp)
}

val ThreemaTypography = Typography(
    headlineLarge  = ThreemaText.LargeTitle,
    headlineMedium = ThreemaText.ScreenTitle,
    titleMedium    = ThreemaText.NavName,
    bodyMedium     = ThreemaText.Body,
    labelSmall     = ThreemaText.Tab,
)
```

## 3. Signature Components

### Trust-Level Indicator (the core atom)

```kotlin
import androidx.compose.animation.animateColorAsState
import androidx.compose.animation.core.tween
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.semantics.contentDescription
import androidx.compose.ui.semantics.semantics
import androidx.compose.ui.unit.dp

@Composable
fun TrustDots(
    level: TrustLevel,
    dotSize: Int = 8,
    showLabel: Boolean = true,
    modifier: Modifier = Modifier,
) {
    val color by animateColorAsState(level.color, tween(200), label = "trust")
    Row(
        modifier.semantics { contentDescription = level.contentDescription },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Row(horizontalArrangement = Arrangement.spacedBy(3.dp)) {
            repeat(3) {
                Box(Modifier.size(dotSize.dp).clip(CircleShape).background(color))
            }
        }
        if (showLabel) {
            Text(level.label, style = ThreemaText.Secondary, color = ThreemaColors.DarkTextSecondary)
        }
    }
}
```

### Threema ID Chip

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ContentCopy
import androidx.compose.material3.Icon
import androidx.compose.material3.IconButton
import androidx.compose.ui.platform.LocalClipboardManager
import androidx.compose.ui.text.AnnotatedString

@Composable
fun ThreemaIdChip(id: String, modifier: Modifier = Modifier) {
    val clipboard = LocalClipboardManager.current
    Row(
        modifier
            .clip(RoundedCornerShape(8.dp))
            .background(ThreemaColors.DarkSurface2)
            .padding(horizontal = 12.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Text(id, style = ThreemaText.IdLarge, color = ThreemaColors.DarkTextPrimary)
        IconButton(onClick = { clipboard.setText(AnnotatedString(id)) }, modifier = Modifier.size(20.dp)) {
            Icon(Icons.Filled.ContentCopy, contentDescription = "Copy Threema ID", tint = ThreemaColors.GreenBright)
        }
    }
}
```

### QR Verify Card + Scanner Reticle

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.Image
import androidx.compose.foundation.border
import androidx.compose.runtime.getValue
import androidx.compose.ui.graphics.asImageBitmap

@Composable
fun QRVerifyCard(threemaID: String, qrBitmap: android.graphics.Bitmap) {
    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(16.dp),
    ) {
        Box(
            Modifier
                .clip(RoundedCornerShape(12.dp))
                .background(Color.White)
                .padding(16.dp)
        ) {
            Image(qrBitmap.asImageBitmap(), contentDescription = "Threema ID QR code",
                modifier = Modifier.size(220.dp))
        }
        Text(threemaID, style = ThreemaText.IdLarge, color = ThreemaColors.DarkTextPrimary)
        Text(
            "Scan in person to reach three green dots — the only fully-verified state.",
            style = ThreemaText.Secondary, color = ThreemaColors.DarkTextSecondary,
            modifier = Modifier.padding(horizontal = 24.dp),
        )
    }
}

@Composable
fun ScannerReticle() {
    val t = rememberInfiniteTransition(label = "reticle")
    val on by t.animateFloat(0f, 1f, infiniteRepeatable(tween(1200), RepeatMode.Reverse), label = "pulse")
    Box(
        Modifier
            .size(240.dp)
            .border(2.5.dp, if (on > 0.5f) ThreemaColors.TrustGreen else Color.White, RoundedCornerShape(16.dp))
    )
}
```

### Message Bubble

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.fadeIn
import androidx.compose.animation.slideInVertically
import androidx.compose.material.icons.filled.DoneAll

enum class BubbleDir { In, Out }

@Composable
fun MessageBubble(text: String, time: String, dir: BubbleDir, read: Boolean = false) {
    val out = dir == BubbleDir.Out
    Row(
        Modifier.fillMaxWidth(),
        horizontalArrangement = if (out) Arrangement.End else Arrangement.Start,
    ) {
        Column(
            Modifier
                .widthIn(max = 280.dp)
                .clip(RoundedCornerShape(
                    topStart = 16.dp, topEnd = 16.dp,
                    bottomStart = if (out) 16.dp else 4.dp,
                    bottomEnd = if (out) 4.dp else 16.dp,
                ))
                .background(if (out) ThreemaColors.BubbleOutDk else ThreemaColors.DarkSurface2)
                .padding(horizontal = 13.dp, vertical = 8.dp),
            horizontalAlignment = Alignment.End,
        ) {
            Text(text, style = ThreemaText.Body,
                color = if (out) Color(0xFFEAFBEF) else ThreemaColors.DarkTextPrimary)
            Row(verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(time, style = ThreemaText.Caption.copy(fontSize = 10.sp),
                    color = if (out) Color.White.copy(alpha = 0.55f) else ThreemaColors.DarkTextSecondary)
                if (out) Icon(Icons.Filled.DoneAll, contentDescription = if (read) "Read" else "Delivered",
                    tint = if (read) Color(0xFF6FD68C) else Color.White.copy(alpha = 0.55f),
                    modifier = Modifier.size(12.dp))
            }
        }
    }
}
```

### Chat Top Bar

```kotlin
import androidx.compose.material.icons.filled.ArrowBack
import androidx.compose.material.icons.filled.Call

@Composable
fun ChatTopBar(
    name: String, threemaID: String, level: TrustLevel,
    onBack: () -> Unit, onCall: () -> Unit,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .height(54.dp)
            .background(ThreemaColors.DarkCanvas.copy(alpha = 0.96f))
            .padding(horizontal = 14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        IconButton(onClick = onBack) {
            Icon(Icons.Filled.ArrowBack, contentDescription = "Back", tint = ThreemaColors.GreenBright)
        }
        Box(Modifier.size(34.dp).clip(CircleShape).background(ThreemaColors.Green),
            contentAlignment = Alignment.Center) {
            Text(name.take(2).uppercase(), color = Color.White, style = ThreemaText.Caption)
        }
        Column(Modifier.weight(1f)) {
            Text(name, style = ThreemaText.NavName, color = ThreemaColors.DarkTextPrimary)
            Row(verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                TrustDots(level, dotSize = 7, showLabel = false)
                Text("${level.label} · $threemaID",
                    style = ThreemaText.Caption.copy(fontSize = 11.sp), color = ThreemaColors.DarkTextSecondary)
            }
        }
        IconButton(onClick = onCall) {
            Icon(Icons.Filled.Call, contentDescription = "Encrypted call", tint = ThreemaColors.GreenBright)
        }
    }
}
```

### Primary Button

```kotlin
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun ThreemaPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val haptics = LocalHapticFeedback.current
    Button(
        onClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onClick() },
        interactionSource = interaction,
        shape = RoundedCornerShape(10.dp),
        colors = ButtonDefaults.buttonColors(
            containerColor = if (pressed) ThreemaColors.GreenPressed else ThreemaColors.Green,
            contentColor = Color.White,
        ),
        modifier = modifier.fillMaxWidth().height(if (pressed) 50.dp else 52.dp),
    ) { Text(title, style = ThreemaText.Button) }
}
```

## 4. Bottom Navigation Bar

There is no Material tint pill — active is just the brand green.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun ThreemaBottomBar(selected: Int, onSelect: (Int) -> Unit, unread: Int = 0) {
    NavigationBar(containerColor = ThreemaColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Chats"    to Icons.Filled.Chat,
            "Contacts" to Icons.Filled.People,
            "Calls"    to Icons.Filled.Call,
            "My ID"    to Icons.Filled.QrCode,
            "Settings" to Icons.Filled.Settings,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (i == 0 && unread > 0) {
                        BadgedBox(badge = { Badge(containerColor = ThreemaColors.Error) { Text("$unread") } }) {
                            Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp))
                        }
                    } else Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp))
                },
                label = { Text(label, style = ThreemaText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = ThreemaColors.GreenBright,
                    selectedTextColor = ThreemaColors.GreenBright,
                    unselectedIconColor = Color(0xFF818185),
                    unselectedTextColor = Color(0xFF818185),
                    indicatorColor = Color.Transparent, // no Material pill — Threema has none
                ),
            )
        }
    }
}
```

## 5. Navigation

Threema has minimal chrome: a 5-tab bottom bar and standard back navigation. The QR scanner is a full-screen destination (CameraX preview + ML Kit barcode analyzer). The share-ID / verify-options surfaces are `ModalBottomSheet`s. Use Navigation-Compose / Nav3 with a 300ms slide push between screens; the bottom bar persists on the primary tabs and is hidden inside a conversation and the scanner.

## 6. Motion

Threema motion is quiet — 120–300ms, never aggressive. Shadows signal "floating, tap outside to dismiss," nothing more; on dark, a 1dp border replaces the (invisible) shadow.

| Moment | Compose recipe |
|--------|----------------|
| Trust upgrade (verify success) | `animateColorAsState(level.color, tween(200))` + `HapticFeedbackType.LongPress` / success haptic |
| QR reticle pulse | `rememberInfiniteTransition` `animateFloat` `infiniteRepeatable(tween(1200), Reverse)` |
| Send message (bubble in) | `AnimatedVisibility(fadeIn() + slideInVertically(tween(200)) { it / 2 })` |
| Incoming bubble | `fadeIn(tween(200))` + 6dp `slideInVertically` |
| Tab switch | instant; icon crossfade `tween(120)` |
| Sheet present | `ModalBottomSheet` default slide-up; scrim fades 250ms |
| Page navigation | Nav3/`NavHost` slide push `tween(300)` |

```kotlin
// Trust upgrade — the canonical Threema motion
val dotColor by animateColorAsState(level.color, tween(200), label = "trustUpgrade")
LaunchedEffect(level) {
    if (level == TrustLevel.Verified) haptics.performHapticFeedback(HapticFeedbackType.LongPress)
}
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on send and button press; for verification success use a success cue (`HapticFeedbackConstants.CONFIRM` on API 30+ via `LocalView.current.performHapticFeedback(...)`). Auto-sync is silent — no motion, no haptic.

## 7. Icons

Use `androidx.compose.material:material-icons-extended`. The trust-dot indicator is drawn with `Box`+`CircleShape` (not an icon); the Threema ID is monospaced text, not a glyph.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Chats (tab) | `bubble.left` | `Icons.Filled.Chat` |
| Contacts (tab) | `person.2` | `Icons.Filled.People` |
| Calls (tab) | `phone` | `Icons.Filled.Call` |
| My ID (tab) | `qrcode` | `Icons.Filled.QrCode` |
| Settings (tab) | `gearshape` | `Icons.Filled.Settings` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Call (top bar) | `phone` | `Icons.Filled.Call` |
| Send | `paperplane.fill` | `Icons.Filled.Send` |
| Attach (+) | `plus.circle` | `Icons.Filled.AddCircleOutline` |
| Copy ID | `doc.on.doc` | `Icons.Filled.ContentCopy` |
| Scan QR | `qrcode.viewfinder` | `Icons.Filled.QrCodeScanner` |
| Verified badge | `checkmark.seal.fill` | `Icons.Filled.VerifiedUser` |
| Delivered / read | `checkmark` (double) | `Icons.Filled.DoneAll` |
| Encrypted | `lock.fill` | `Icons.Filled.Lock` |
| Block / revoke | `nosign` | `Icons.Filled.Block` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Add contact | `person.badge.plus` | `Icons.Filled.PersonAdd` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; CameraX + ML Kit barcode are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the calm white canvas wants dark-content system bars (light-content in dark mode). The chat composer must sit above the IME — use `Modifier.imePadding()`; the QR scanner renders full-bleed with controls inset to the system bars.
- **Font scaling**: `sp` honors the user's font scale — keep it on titles, names, body, captions. Pin layout-sensitive text (the trust-dot label, 10sp tab labels, the monospaced ID string) via a fixed-density wrapper: `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **No bundled font**: use `FontFamily.Default` (system) everywhere; identity strings use `FontFamily.Monospace`. Do not ship a custom typeface.
- **TalkBack**: trust state must never be color-only — `TrustDots` sets `contentDescription = "Trust level: verified"`; label bubbles "You said {text}, read" / "{name} said {text}"; label the QR card "Your Threema ID {id}, scan to verify"; expose the scan action with a clear `contentDescription`.
- **Touch targets**: Material guidance is 48.dp. Give the 7–8.dp trust dots a full-row tap target (open contact detail), the 22.dp tab icons a 48.dp hit area, the 36.dp send button ≥ 48.dp hit; list rows are 64.dp tall and fully tappable.
- **Contrast**: `#111113` on `#FFFFFF` and `#ECECEC` on `#121212` pass WCAG AA. The trust palette (red/orange/green) is always paired with a text label so color is never the sole channel.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the reticle pulse and bubble slide; substitute a plain `Crossfade` for the trust upgrade (keep the success haptic).
- **Dark mode**: invert via the `Dark*` palette — `#121212`, NOT true black; `#111113` text becomes `#ECECEC`; brand green brightens to `#1FA53C`. Trust-dot colors NEVER change between schemes — verification state must read identically. Do **not** enable Material You `dynamicColorScheme()`: Threema's calm white + single-green identity must hold regardless of wallpaper (there is no second accent to harmonize).
- **Privacy**: the Threema ID is the only identifier — never collect or display a phone number / email as account identity; never log the ID or fingerprint (treat as sensitive).
