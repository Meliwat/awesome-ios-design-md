# Marriott Bonvoy (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Marriott Bonvoy's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the signature Bonvoy points panel, points-aware rate cards, the hotel hero, the Mobile Key, the bottom bar, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Bonvoy's deep navy base, disciplined gold for member value, the points panel, points-aware rate select, the Mobile Key) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` for the date picker, NFC/HCE for the real Mobile Key, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for hero/thumbnails. Bonvoy has a fixed brand palette (no Material You extraction). It is light-mode-capable but renders premium in both; a full dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/BonvoyColors.kt
import androidx.compose.ui.graphics.Color

object BonvoyColors {
    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFFFFFFF)
    val SurfaceCream   = Color(0xFFF6F4EF)
    val SurfacePressed = Color(0xFFECE9E1)
    val Divider        = Color(0xFFE3E1DA)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF121214) // near-black, slightly warm
    val DarkSurface1 = Color(0xFF1A1A1D)
    val DarkSurface2 = Color(0xFF232327)
    val DarkDivider  = Color(0xFF2E2E33)

    // Structure (Navy) — constant across schemes
    val Navy        = Color(0xFF16264A)
    val NearBlack   = Color(0xFF1C1C1C)
    val NavyLine    = Color(0xFF243A66)
    val NavyPressed = Color(0xFF1F3157)

    // Member value (Gold) — used sparingly to mark earned value
    val Gold        = Color(0xFFB3852A)
    val GoldBright  = Color(0xFFD2A23E) // dark-mode points/tier/Book
    val GoldPressed = Color(0xFF8F6A1F)
    val OnGold      = Color(0xFF1C1206)

    // Text
    val TextPrimary       = Color(0xFF1A1A1C) // near-black
    val TextSecondary     = Color(0xFF5C5C63)
    val TextTertiary      = Color(0xFF8E8E96)
    val DarkTextPrimary   = Color(0xFFEDEDEF)
    val DarkTextSecondary = Color(0xFFA0A0A8)

    // Semantic
    val Success = Color(0xFF2E8B57)
    val Error   = Color(0xFFC7453B)
    val Warning = Color(0xFFC8901C)
}

enum class BonvoyTier(val label: String) {
    Member("MEMBER"), Silver("SILVER ELITE"), Gold("GOLD ELITE"),
    Platinum("PLATINUM ELITE"), Titanium("TITANIUM ELITE"), Ambassador("AMBASSADOR ELITE");
}
```

Wire it into both schemes. Navy is the constant structure color; gold marks member value and brightens to `#D2A23E` on dark.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.*
import androidx.compose.runtime.Composable

private val BonvoyLight = lightColorScheme(
    primary        = BonvoyColors.Gold,    // the "Book" CTA / value
    onPrimary      = BonvoyColors.OnGold,
    secondary      = BonvoyColors.Navy,    // structure / secondary CTA
    background      = BonvoyColors.Canvas,
    onBackground   = BonvoyColors.TextPrimary,
    surface        = BonvoyColors.SurfaceCream,
    onSurface      = BonvoyColors.TextPrimary,
    surfaceVariant = BonvoyColors.SurfacePressed,
    outline        = BonvoyColors.Divider,
    error          = BonvoyColors.Error,
)

private val BonvoyDark = darkColorScheme(
    primary        = BonvoyColors.GoldBright,
    onPrimary      = BonvoyColors.OnGold,
    secondary      = BonvoyColors.Navy,
    background      = BonvoyColors.DarkCanvas,
    onBackground   = BonvoyColors.DarkTextPrimary,
    surface        = BonvoyColors.DarkSurface1,
    onSurface      = BonvoyColors.DarkTextPrimary,
    surfaceVariant = BonvoyColors.DarkSurface2,
    outline        = BonvoyColors.DarkDivider,
    error          = BonvoyColors.Error,
)

@Composable
fun BonvoyTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) BonvoyDark else BonvoyLight,
    typography  = BonvoyTypography,
    content     = content,
)
```

## 2. Typography

Marriott Bonvoy ships **no custom typeface** — it uses the platform system font with the OS font-scale. Eyebrows are letter-spaced uppercase small-caps; the points balance is an 800-weight numeral.

```kotlin
// ui/theme/BonvoyType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.unit.sp

private val Sys = FontFamily.Default // system font — no bundled face

object BonvoyText {
    val LargeTitle    = TextStyle(Sys, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val PointsBalance = TextStyle(Sys, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val HotelName     = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 27.sp, letterSpacing = (-0.3).sp)
    val Section       = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Body          = TextStyle(Sys, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val RateTitle     = TextStyle(Sys, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp)
    val Price         = TextStyle(Sys, fontWeight = FontWeight.ExtraBold, fontSize = 17.sp, lineHeight = 20.sp)
    val Meta          = TextStyle(Sys, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 19.sp)
    val Eyebrow       = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 1.2.sp)
    val PointsEarn    = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.3.sp)
    val Button        = TextStyle(Sys, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab           = TextStyle(Sys, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Caption       = TextStyle(Sys, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 15.sp)
}

val BonvoyTypography = Typography(
    headlineLarge  = BonvoyText.LargeTitle,
    headlineMedium = BonvoyText.PointsBalance,
    titleMedium    = BonvoyText.HotelName,
    bodyMedium     = BonvoyText.Body,
    labelSmall     = BonvoyText.Tab,
)
```

## 3. Signature Components

### Bonvoy Points Panel (the core component)

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import java.text.NumberFormat

@Composable
fun BonvoyPointsPanel(
    tier: BonvoyTier,
    memberName: String,
    points: Int,
    progress: Float,             // 0f..1f toward next free night
    footnote: String,
) {
    val animPoints by animateIntAsState(points, tween(600, easing = EaseOutCubic), label = "points")
    val animFill by animateFloatAsState(progress, tween(500, easing = EaseOutCubic), label = "fill")
    val fmt = remember { NumberFormat.getInstance() }

    Column(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(14.dp))
            .background(BonvoyColors.Navy)
            .border(1.dp, BonvoyColors.NavyLine, RoundedCornerShape(14.dp))
            .padding(16.dp),
    ) {
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
            Column {
                Text(tier.label, style = BonvoyText.Eyebrow, color = BonvoyColors.GoldBright)
                Text(memberName, fontSize = 13.sp, color = Color.White.copy(alpha = 0.7f),
                    modifier = Modifier.padding(top = 2.dp))
            }
            Column(horizontalAlignment = Alignment.End) {
                Text(fmt.format(animPoints), style = BonvoyText.PointsBalance, color = Color.White)
                Text("POINTS", fontSize = 11.sp, letterSpacing = 0.4.sp, color = Color.White.copy(alpha = 0.6f))
            }
        }
        Box(
            Modifier
                .fillMaxWidth().height(6.dp).padding(top = 14.dp)
        ) {
            Box(Modifier.fillMaxSize().clip(RoundedCornerShape(3.dp)).background(Color.White.copy(alpha = 0.14f)))
            Box(
                Modifier
                    .fillMaxHeight().fillMaxWidth(animFill)
                    .clip(RoundedCornerShape(3.dp))
                    .background(Brush.horizontalGradient(listOf(BonvoyColors.Gold, BonvoyColors.GoldBright)))
            )
        }
        Text(footnote, fontSize = 11.sp, color = Color.White.copy(alpha = 0.6f),
            modifier = Modifier.padding(top = 8.dp))
    }
}
```

### Points-Aware Rate Card

```kotlin
import androidx.compose.foundation.clickable

@Composable
fun RateCard(
    name: String, detail: String, priceText: String, pointsLine: String,
    selected: Boolean, onClick: () -> Unit,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(10.dp))
            .background(if (selected) BonvoyColors.Gold.copy(alpha = 0.10f) else BonvoyColors.DarkSurface1)
            .border(1.dp, if (selected) BonvoyColors.Gold else BonvoyColors.DarkDivider, RoundedCornerShape(10.dp))
            .clickable(onClick = onClick)
            .padding(14.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.SpaceBetween,
    ) {
        Column(Modifier.weight(1f)) {
            Text(name, style = BonvoyText.RateTitle, color = BonvoyColors.DarkTextPrimary)
            Text(detail, fontSize = 12.sp, color = BonvoyColors.DarkTextSecondary,
                modifier = Modifier.padding(top = 3.dp))
        }
        Column(horizontalAlignment = Alignment.End) {
            Text(priceText, style = BonvoyText.Price, color = BonvoyColors.DarkTextPrimary)
            Text(pointsLine, style = BonvoyText.PointsEarn, color = BonvoyColors.GoldBright,
                modifier = Modifier.padding(top = 2.dp))
        }
    }
}
```

### Hotel Hero

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.Icon
import androidx.compose.material3.IconButton
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun HotelHero(
    imageUrl: String, subBrand: String, name: String, location: String,
    saved: Boolean, onBack: () -> Unit, onShare: () -> Unit, onToggleSave: () -> Unit,
) {
    Box(Modifier.fillMaxWidth().height(256.dp)) {
        AsyncImage(model = imageUrl, contentDescription = name,
            modifier = Modifier.fillMaxSize(), contentScale = ContentScale.Crop)
        Box(
            Modifier.align(Alignment.BottomCenter).fillMaxWidth().height(145.dp)
                .background(Brush.verticalGradient(listOf(Color.Transparent, BonvoyColors.DarkCanvas.copy(alpha = 0.7f))))
        )
        Row(
            Modifier.align(Alignment.TopCenter).fillMaxWidth().padding(horizontal = 16.dp).padding(top = 54.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            HeroButton(Icons.Filled.ArrowBack, onBack)
            Row(horizontalArrangement = Arrangement.spacedBy(10.dp)) {
                HeroButton(Icons.Filled.IosShare, onShare)
                HeroButton(if (saved) Icons.Filled.Favorite else Icons.Filled.FavoriteBorder, onToggleSave)
            }
        }
        Column(Modifier.align(Alignment.BottomStart).padding(18.dp)) {
            Text(subBrand.uppercase(), style = BonvoyText.Eyebrow.copy(letterSpacing = 1.4.sp), color = BonvoyColors.GoldBright)
            Text(name, fontSize = 23.sp, fontWeight = FontWeight.ExtraBold, color = Color.White,
                modifier = Modifier.padding(top = 5.dp))
            Text(location, fontSize = 13.sp, color = Color.White.copy(alpha = 0.82f),
                modifier = Modifier.padding(top = 5.dp))
        }
    }
}

@Composable
private fun HeroButton(icon: androidx.compose.ui.graphics.vector.ImageVector, onClick: () -> Unit) {
    IconButton(
        onClick = onClick,
        modifier = Modifier.size(36.dp).clip(CircleShape).background(BonvoyColors.DarkCanvas.copy(alpha = 0.55f)),
    ) { Icon(icon, contentDescription = null, tint = Color.White, modifier = Modifier.size(18.dp)) }
}
```

### Mobile Key

```kotlin
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun MobileKeyCard(room: String, checkIn: String, onUnlock: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(14.dp))
            .background(BonvoyColors.Navy)
            .border(1.dp, BonvoyColors.NavyLine, RoundedCornerShape(14.dp))
            .clickable { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onUnlock() }
            .padding(16.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Box(
            Modifier.size(48.dp).clip(RoundedCornerShape(12.dp)).background(BonvoyColors.GoldBright.copy(alpha = 0.16f)),
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Lock, contentDescription = null, tint = BonvoyColors.GoldBright, modifier = Modifier.size(22.dp)) }
        Column(Modifier.weight(1f)) {
            Text("Mobile Key ready", fontSize = 14.sp, fontWeight = FontWeight.Bold, color = Color.White)
            Text("Skip the front desk · Tap your phone to unlock", fontSize = 12.sp,
                color = Color.White.copy(alpha = 0.65f), modifier = Modifier.padding(top = 3.dp))
            Text("ROOM $room · CHECK-IN $checkIn", style = BonvoyText.Eyebrow.copy(letterSpacing = 0.5.sp),
                color = BonvoyColors.GoldBright, modifier = Modifier.padding(top = 6.dp))
        }
    }
}
```

### Elite Tier Badge + Booking Bar

```kotlin
@Composable
fun TierBadge(tier: BonvoyTier) {
    val (fg, bg, border) = when (tier) {
        BonvoyTier.Member     -> Triple(BonvoyColors.TextSecondary, BonvoyColors.DarkSurface2, Color.White.copy(alpha = 0.06f))
        BonvoyTier.Silver     -> Triple(Color(0xFFC7C7CE), Color(0xFF2A2A2E), Color(0xFF3A3A40))
        BonvoyTier.Gold       -> Triple(BonvoyColors.GoldBright, BonvoyColors.Gold.copy(alpha = 0.16f), BonvoyColors.GoldBright.copy(alpha = 0.40f))
        BonvoyTier.Platinum   -> Triple(Color(0xFF9FB6E0), BonvoyColors.Navy.copy(alpha = 0.40f), BonvoyColors.NavyLine)
        BonvoyTier.Titanium   -> Triple(BonvoyColors.GoldBright, BonvoyColors.Navy, BonvoyColors.NavyLine)
        BonvoyTier.Ambassador -> Triple(BonvoyColors.GoldBright, BonvoyColors.Navy, BonvoyColors.Gold)
    }
    Box(
        Modifier
            .clip(RoundedCornerShape(999.dp)).background(bg)
            .border(1.dp, border, RoundedCornerShape(999.dp))
            .padding(horizontal = 12.dp, vertical = 6.dp),
    ) { Text(tier.label, style = BonvoyText.Eyebrow.copy(letterSpacing = 0.6.sp), color = fg) }
}

@Composable
fun BonvoyBookingBar(price: String, pointsEarned: String, onBook: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier
            .fillMaxWidth().height(76.dp)
            .background(BonvoyColors.DarkCanvas.copy(alpha = 0.97f))
            .padding(horizontal = 18.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.SpaceBetween,
    ) {
        Column {
            Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(price, fontSize = 19.sp, fontWeight = FontWeight.ExtraBold, color = BonvoyColors.DarkTextPrimary)
                Text("/ night", fontSize = 13.sp, color = BonvoyColors.DarkTextSecondary, modifier = Modifier.padding(bottom = 3.dp))
            }
            Text(pointsEarned, style = BonvoyText.PointsEarn, color = BonvoyColors.GoldBright)
        }
        Button(
            onClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress); onBook() },
            shape = RoundedCornerShape(8.dp),
            colors = ButtonDefaults.buttonColors(containerColor = BonvoyColors.Gold, contentColor = BonvoyColors.OnGold),
            contentPadding = PaddingValues(horizontal = 26.dp, vertical = 13.dp),
        ) { Text("Book", style = BonvoyText.Button) }
    }
}
```

## 4. Bottom Navigation Bar

There is no Material tint pill — active is gold (loyalty is the spine of the app).

```kotlin
@Composable
fun BonvoyBottomBar(selected: Int, onSelect: (Int) -> Unit, keyActive: Boolean = false) {
    NavigationBar(containerColor = BonvoyColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Home"       to Icons.Filled.Home,
            "Search"     to Icons.Filled.Search,
            "Trips"      to Icons.Filled.Work,
            "Mobile Key" to Icons.Filled.Key,
            "Account"    to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (i == 3 && keyActive) {
                        BadgedBox(badge = { Badge(containerColor = BonvoyColors.GoldBright) }) {
                            Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp))
                        }
                    } else Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp))
                },
                label = { Text(label, style = BonvoyText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = BonvoyColors.GoldBright,
                    selectedTextColor = BonvoyColors.GoldBright,
                    unselectedIconColor = BonvoyColors.TextTertiary,
                    unselectedTextColor = BonvoyColors.TextTertiary,
                    indicatorColor = Color.Transparent, // no Material pill — Bonvoy has none
                ),
            )
        }
    }
}
```

## 5. Navigation

Bonvoy's loop is Search → hotel detail → rate select → confirmation → Mobile Key. Model it with Navigation-Compose / Nav3: a 300ms slide push; the bottom bar persists on the five primary tabs and is hidden on the hotel detail (which has its own hero overlay nav + sticky booking bar). The date/room picker and filters are `ModalBottomSheet`s. The real Mobile Key uses NFC/HCE (`android.nfc`) — the card in §3 is the trigger surface; the actual unlock goes through the lock vendor SDK.

## 6. Motion

Bonvoy motion is calm and premium — the loudest moment is the points count-up. 180–600ms ease-out.

| Moment | Compose recipe |
|--------|----------------|
| Points count-up | `animateIntAsState(points, tween(600, EaseOutCubic))` |
| Progress bar fill | `animateFloatAsState(progress, tween(500, EaseOutCubic))` → `fillMaxWidth(animFill)` |
| Rate select | gold border/tint switch + `animateColorAsState(tween(180))`; booking bar `AnimatedContent` cross-dissolve 250ms |
| Mobile Key unlock | gold ring `animateFloatAsState` pulse + `HapticFeedbackType.LongPress` / success |
| Hero parallax | `Modifier.graphicsLayer { translationY = scroll * 0.5f }` |
| Card appear | `AnimatedVisibility` `fadeIn(tween(220)) + slideInVertically { it / 6 }`, 60ms delay/item |
| Page navigation | Nav3/`NavHost` slide push `tween(300)` |
| Sheet present | `ModalBottomSheet` default slide-up; scrim fades 250ms |

```kotlin
// Points count-up — the canonical Bonvoy motion
val animPoints by animateIntAsState(points, tween(600, easing = EaseOutCubic), label = "points")
Text(NumberFormat.getInstance().format(animPoints), style = BonvoyText.PointsBalance, color = Color.White)
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on "Book"; a success cue (`HapticFeedbackConstants.CONFIRM` on API 30+ via `LocalView.current.performHapticFeedback(...)`) for Mobile Key unlock and booking confirmed; `HapticFeedbackType.TextHandleMove` for rate select and date endpoints.

## 7. Icons

Use `androidx.compose.material:material-icons-extended`. The points progress bar and tier badges are drawn with `Box`+shape; the gold sub-brand eyebrow is text, not a glyph.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Home (tab) | `house` | `Icons.Filled.Home` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Trips (tab) | `suitcase` | `Icons.Filled.Work` |
| Mobile Key (tab) | `key` | `Icons.Filled.Key` |
| Account (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Back (hero) | `chevron.left` | `Icons.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Save | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Favorite` |
| Rating star | `star.fill` | `Icons.Filled.Star` |
| Mobile Key glyph | `lock.fill` / `key.fill` | `Icons.Filled.Lock` / `Icons.Filled.Key` |
| Points / rewards | `star.circle.fill` | `Icons.Filled.Stars` |
| Free night | `moon.stars.fill` | `Icons.Filled.Nightlight` |
| Member tier | `crown.fill` | `Icons.Filled.WorkspacePremium` |
| Calendar / dates | `calendar` | `Icons.Filled.CalendarMonth` |
| Room & guests | `person.2` | `Icons.Filled.Group` |
| Location | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Amenities | `checkmark.circle` | `Icons.Filled.CheckCircle` |
| Filters | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Confirmed | `checkmark.seal.fill` | `Icons.Filled.Verified` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; NFC/HCE for Mobile Key needs API 19+, comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the hero extends under the status bar with white overlay controls (light-content system bars while a hero is visible). The booking bar respects the navigation bar inset.
- **Font scaling**: `sp` honors the user's font scale — keep it on titles, points balance, hotel name, headers, body. Pin layout-sensitive text (eyebrows, 10sp tab labels, points-earn lines, progress legends) via a fixed-density wrapper: `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **No bundled font**: use `FontFamily.Default` (system) everywhere. Do not ship a custom typeface.
- **TalkBack**: announce the member panel "Titanium Elite, 128,450 points, 64% toward your next free night"; rate cards "{name}, {detail}, {price}, earn {points} points, double-tap to select"; the Mobile Key "Mobile Key ready, Room 1408, double-tap to unlock"; tier badges carry text labels (not color alone).
- **Touch targets**: Material guidance is 48.dp. Give the 36.dp hero overlay buttons a ≥ 48.dp hit area; rate cards and the Mobile Key card are fully tappable; the "Book" button is ≥ 48.dp tall.
- **Contrast**: `#1A1A1C` on `#FFFFFF` and `#EDEDEF` on `#121214` pass WCAG AA. Gold-on-navy and the `#1C1206`-on-gold CTA are carefully paired; the gold sub-brand eyebrow is paired with the hotel name (not the sole channel).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, set the points value and bar fill directly (no count-up); cross-dissolve rate/price changes; remove hero parallax.
- **Dark mode**: navy `#16264A` stays constant (panels, secondary CTA, brand frame); gold brightens to `#D2A23E` for points/tier/Book; canvas is `#121214`, NOT pure black; `#1A1A1C` text becomes `#EDEDEF`. Shadows nearly vanish on dark — navy panels already use a 1dp `NavyLine` border; floating panels add a 1dp `DarkDivider` border. Do **not** enable Material You `dynamicColorScheme()`: Bonvoy's navy + disciplined-gold identity must hold regardless of wallpaper.
- **Loyalty/security**: never log full membership numbers or Mobile Key tokens; treat them as sensitive. The Mobile Key unlock must require an explicit user action (no auto-unlock); gate the real unlock behind device authentication where the lock SDK requires it.
