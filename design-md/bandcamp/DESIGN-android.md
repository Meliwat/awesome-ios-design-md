# Bandcamp (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Bandcamp's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, the big square album art, the buy/support card, the inline teal-waveform player, the tracklist, the fan collection card, navigation, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Bandcamp's paper-white editorial page, big square art, the scarce teal reserved for artist links + the buy price + the collection) while making everything idiomatic Android — a `NavigationBar` instead of a UITabBar, `Card`/`Surface` borders instead of SwiftUI overlays, `dp`/`sp` instead of `pt`, and Coil for artwork.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for album art. Bandcamp is **light-first** (the white editorial page is the identity); a true dark inversion is provided but is NOT the default. No Material You dynamic color — the single teal must hold regardless of wallpaper.

## 1. Color Tokens

```kotlin
// ui/theme/BandcampColors.kt
import androidx.compose.ui.graphics.Color

object BandcampColors {
    // Canvas & Surfaces (Light — Bandcamp's true identity)
    val Paper        = Color(0xFFFFFFFF)
    val PaperGray    = Color(0xFFF4F4F4)
    val HoverGray    = Color(0xFFEDEDED)
    val Divider      = Color(0xFFE2E2E2)
    val TrackDivider = Color(0xFFF0F0F0)

    // Canvas & Surfaces (Dark — true inversion, NOT default)
    val DarkCanvas   = Color(0xFF101417) // cool charcoal, slightly blue-gray
    val DarkSurface1 = Color(0xFF1A2024)
    val DarkSurface2 = Color(0xFF232B30)
    val DarkDivider  = Color(0xFF2C353B)

    // Text
    val Ink           = Color(0xFF1A1A1A) // near-black — NOT pure black
    val TextSecondary = Color(0xFF767676)
    val TextTertiary  = Color(0xFF9A9A9A)
    val DarkInk       = Color(0xFFEDF1F3)

    // Brand
    val Teal      = Color(0xFF1DA0C3) // artist links, buy price, Buy button, active tab
    val TealDeep  = Color(0xFF629AA9)
    val TealPress = Color(0xFF17819E)
    val DarkTeal  = Color(0xFF3DB5D6) // teal shifted lighter for dark-mode contrast

    // Semantic
    val Success = Color(0xFF4CAF50)
    val Error   = Color(0xFFE45858)
    val Warning = Color(0xFFE0A030)
}
```

Wire it into both schemes. Bandcamp is light-first; the dark scheme is a true inversion using cool charcoal `#101417`, never the default.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.runtime.Composable

private val BandcampLight = lightColorScheme(
    primary        = BandcampColors.Teal,
    onPrimary      = BandcampColors.Paper,
    background     = BandcampColors.Paper,
    onBackground   = BandcampColors.Ink,
    surface        = BandcampColors.PaperGray,
    onSurface      = BandcampColors.Ink,
    surfaceVariant = BandcampColors.HoverGray,
    outline        = BandcampColors.Divider,
    error          = BandcampColors.Error,
)

private val BandcampDark = darkColorScheme(
    primary        = BandcampColors.DarkTeal,
    onPrimary      = BandcampColors.DarkCanvas,
    background     = BandcampColors.DarkCanvas,
    onBackground   = BandcampColors.DarkInk,
    surface        = BandcampColors.DarkSurface1,
    onSurface      = BandcampColors.DarkInk,
    surfaceVariant = BandcampColors.DarkSurface2,
    outline        = BandcampColors.DarkDivider,
    error          = BandcampColors.Error,
)

@Composable
fun BandcampTheme(
    dark: Boolean = isSystemInDarkTheme(),   // follow system; light is the identity
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) BandcampDark else BandcampLight,
    typography  = BandcampTypography,
    content     = content,
)
```

## 2. Typography

Bandcamp uses a clean humanist sans; use **DM Sans** as the faithful fallback (SIL OFL — drop the TTFs in `res/font/`). Editorial, record-sleeve rhythm — titles at 700, artist/price at 600–700, body at 400.

```kotlin
// ui/theme/BandcampType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

// Swap dm_sans_* → your licensed Bandcamp face if available.
val DMSans = FontFamily(
    Font(R.font.dm_sans_regular,  FontWeight.Normal),
    Font(R.font.dm_sans_medium,   FontWeight.Medium),
    Font(R.font.dm_sans_semibold, FontWeight.SemiBold),
    Font(R.font.dm_sans_bold,     FontWeight.Bold),
)

object BandcampText {
    val ScreenTitle = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val AlbumTitle  = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Section     = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val ArtistLink  = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 23.sp) // color = Teal at call site
    val Subhead     = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body        = TextStyle(DMSans, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 25.sp)
    val Price       = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 22.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val PriceUnit   = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 16.sp)
    val RowTitle    = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Meta        = TextStyle(DMSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 19.sp)
    val TrackName   = TextStyle(DMSans, fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 19.sp)
    val TrackNum    = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 12.sp)
    val TabLabel    = TextStyle(DMSans, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(DMSans, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 15.sp)
}

val BandcampTypography = Typography(
    headlineLarge = BandcampText.ScreenTitle,
    titleLarge    = BandcampText.AlbumTitle,
    titleMedium   = BandcampText.Subhead,
    bodyLarge     = BandcampText.Body,
    labelSmall    = BandcampText.TabLabel,
)
```

## 3. Signature Components

### Big Square Album Art

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.aspectRatio
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun AlbumArt(url: String?, fullBleed: Boolean = true, modifier: Modifier = Modifier) {
    // strictly square, never circular, never tinted, never shadowed — content is sovereign
    AsyncImage(
        model = url,
        contentDescription = null,
        contentScale = ContentScale.Crop,
        modifier = modifier
            .fillMaxWidth()
            .aspectRatio(1f)
            .clip(RoundedCornerShape(if (fullBleed) 0.dp else 4.dp))
            .background(BandcampColors.PaperGray), // flat placeholder, no shimmer
    )
}
```

### Buy / Support Card (signature)

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun BuySupportCard(
    price: String, currency: String, nameYourPrice: Boolean,
    onBuy: () -> Unit, onWishlist: () -> Unit,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val haptics = LocalHapticFeedback.current

    Column(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(6.dp))
            .background(BandcampColors.PaperGray)
            .border(1.dp, BandcampColors.Divider, RoundedCornerShape(6.dp))
            .padding(16.dp),
    ) {
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween, verticalAlignment = Alignment.Bottom) {
            Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Text("$$price", style = BandcampText.Price, color = BandcampColors.Ink)
                Text(currency, style = BandcampText.PriceUnit, color = BandcampColors.TextSecondary)
            }
            if (nameYourPrice) Text("or more", style = BandcampText.PriceUnit, color = BandcampColors.TextSecondary)
        }
        if (nameYourPrice) {
            Text(
                "Name your price · Pay what you want",
                style = BandcampText.PriceUnit.copy(fontWeight = FontWeight.Normal),
                color = BandcampColors.TextSecondary,
                modifier = Modifier.padding(top = 4.dp),
            )
        }

        Box(
            Modifier
                .fillMaxWidth()
                .padding(top = 14.dp)
                .scale(if (pressed) 0.99f else 1f)
                .clip(RoundedCornerShape(4.dp))
                .background(if (pressed) BandcampColors.TealPress else BandcampColors.Teal)
                .clickable(interaction, indication = null) {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                    onBuy()
                }
                .padding(vertical = 13.dp),
            contentAlignment = Alignment.Center,
        ) {
            Text("Buy Digital Album", style = BandcampText.Button, color = Color.White)
        }

        Text(
            "Add to wishlist",
            style = BandcampText.PriceUnit.copy(color = BandcampColors.Teal),
            modifier = Modifier.align(Alignment.CenterHorizontally).padding(top = 10.dp).clickable(onClick = onWishlist),
        )
    }
}
```

### Inline Teal-Waveform Player

```kotlin
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Pause
import androidx.compose.material.icons.filled.PlayArrow
import androidx.compose.material3.Icon

private val WAVE = listOf(6, 13, 9, 16, 7, 11, 8, 14, 10, 12)

@Composable
fun InlinePlayer(
    playing: Boolean, track: String, elapsed: String, total: String,
    progress: Float, onToggle: () -> Unit,
) {
    Row(
        Modifier
            .fillMaxWidth()
            .border(BorderStroke(0.dp, Color.Transparent))   // banded strip — hairline top+bottom below
            .drawBehind {
                drawLine(BandcampColors.Divider, androidx.compose.ui.geometry.Offset(0f, 0f), androidx.compose.ui.geometry.Offset(size.width, 0f), 1f)
                drawLine(BandcampColors.Divider, androidx.compose.ui.geometry.Offset(0f, size.height), androidx.compose.ui.geometry.Offset(size.width, size.height), 1f)
            }
            .padding(horizontal = 20.dp, vertical = 12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(
            Modifier.size(36.dp).clip(CircleShape).background(BandcampColors.Ink)
                .clickable(onClick = onToggle),
            contentAlignment = Alignment.Center,
        ) {
            Icon(
                if (playing) Icons.Filled.Pause else Icons.Filled.PlayArrow,
                contentDescription = if (playing) "Pause" else "Play",
                tint = Color.White, modifier = Modifier.size(14.dp),
            )
        }
        Column(Modifier.weight(1f)) {
            Text(track, style = BandcampText.RowTitle.copy(fontSize = 13.sp), color = BandcampColors.Ink)
            Text("$elapsed / $total", style = BandcampText.PriceUnit.copy(fontWeight = FontWeight.Normal, fontSize = 11.sp), color = BandcampColors.TextSecondary)
        }
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(2.dp), modifier = Modifier.height(18.dp)) {
            WAVE.forEachIndexed { i, h ->
                Box(
                    Modifier
                        .width(2.5.dp)
                        .height(h.dp)
                        .clip(RoundedCornerShape(1.dp))
                        .background(BandcampColors.Teal.copy(alpha = if (i.toFloat() / WAVE.size < progress) 1f else 0.35f))
                )
            }
        }
    }
}
```

### Tracklist Row

```kotlin
@Composable
fun TrackRow(number: Int, name: String, duration: String, isPlaying: Boolean) {
    Column(Modifier.fillMaxWidth().clickable {}.padding(horizontal = 20.dp)) {
        Row(
            Modifier.fillMaxWidth().padding(vertical = 9.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            Text("$number", style = BandcampText.TrackNum, color = BandcampColors.TextTertiary, modifier = Modifier.width(18.dp))
            Text(
                name,
                style = if (isPlaying) BandcampText.TrackName.copy(fontWeight = FontWeight.Bold) else BandcampText.TrackName,
                color = if (isPlaying) BandcampColors.Teal else BandcampColors.Ink,
                modifier = Modifier.weight(1f),
            )
            Text(duration, style = BandcampText.Meta.copy(fontSize = 12.sp), color = BandcampColors.TextSecondary)
        }
        Box(Modifier.fillMaxWidth().height(1.dp).background(BandcampColors.TrackDivider))
    }
}
```

### Fan Collection Card

```kotlin
@Composable
fun CollectionCard(artUrl: String?, title: String, artist: String, modifier: Modifier = Modifier) {
    Column(modifier) {
        AlbumArt(url = artUrl, fullBleed = false)   // 4dp radius square
        Text(title, style = BandcampText.RowTitle.copy(fontSize = 13.sp), color = BandcampColors.Ink, maxLines = 1, modifier = Modifier.padding(top = 8.dp))
        Text(artist, style = BandcampText.Meta.copy(fontSize = 12.sp), color = BandcampColors.TextSecondary, maxLines = 1, modifier = Modifier.padding(top = 2.dp))
    }
}

// Feed wrapper: "{Fan} bought this" + optional note above the card
@Composable
fun FeedItem(fan: String, note: String?, content: @Composable () -> Unit) {
    Column(Modifier.padding(horizontal = 20.dp), verticalArrangement = Arrangement.spacedBy(8.dp)) {
        Text("$fan bought this", style = BandcampText.RowTitle.copy(fontSize = 13.sp), color = BandcampColors.Ink)
        if (note != null) Text(note, style = BandcampText.Body, color = BandcampColors.TextSecondary)
        content()
    }
}
```

## 4. Navigation

Bandcamp has a 4-tab bottom strip with a teal active tint — no Material pill indicator. The top app bar is a clean surface with a hairline bottom border on scroll.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun BandcampBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = BandcampColors.Paper,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Feed"       to Icons.Filled.Home,
            "Discover"   to Icons.Filled.Search,
            "Collection" to Icons.Filled.GridView,
            "Profile"    to Icons.Filled.Person,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = BandcampText.TabLabel) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = BandcampColors.Teal,
                    selectedTextColor = BandcampColors.Teal,
                    unselectedIconColor = BandcampColors.TextTertiary,
                    unselectedTextColor = BandcampColors.TextTertiary,
                    indicatorColor = Color.Transparent,   // no Material pill — Bandcamp is flat & editorial
                ),
            )
        }
    }
}

@Composable
fun AlbumTopBar(onBack: () -> Unit, onMore: () -> Unit, scrolled: Boolean) {
    Surface(color = BandcampColors.Paper, tonalElevation = 0.dp) {
        Column {
            Row(
                Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 10.dp),
                verticalAlignment = Alignment.CenterVertically,
            ) {
                Icon(Icons.Filled.ArrowBack, "Back", tint = BandcampColors.Ink,
                    modifier = Modifier.size(22.dp).clickable(onClick = onBack))
                Spacer(Modifier.weight(1f))
                Text("Album", style = BandcampText.RowTitle.copy(fontSize = 13.sp), color = BandcampColors.Ink)
                Spacer(Modifier.weight(1f))
                Icon(Icons.Filled.MoreHoriz, "More", tint = BandcampColors.Ink,
                    modifier = Modifier.size(22.dp).clickable(onClick = onMore))
            }
            if (scrolled) Box(Modifier.fillMaxWidth().height(1.dp).background(BandcampColors.Divider))
        }
    }
}
```

## 5. Motion

Bandcamp motion is restrained and editorial — no springs, no big gradients; the page behaves like paper.

| Moment | Compose recipe |
|--------|----------------|
| Buy button press | `scale 1 → 0.99` + bg `Teal → TealPress` `tween(100)` + `HapticFeedbackType.LongPress` |
| Add to collection / wishlist | quick heart/check `animateFloatAsState` scale or alpha `tween(150)` + soft haptic; snackbar "Added to collection" |
| Page navigation | Nav3/`NavHost` slide push `tween(300)`; optional shared-element zoom of album art from feed tile |
| Player progress | linear — bar alpha `if (i/len < progress) 1f else 0.35f`, no animation spec |
| Tracklist row tap | ripple highlight `HoverGray`, then track name recolors `Ink → Teal` |
| Bottom sheet (checkout/share) | `ModalBottomSheet` slide-up `tween(300)` + scrim fade |
| Tab switch | instant content swap; active icon tints `Teal` over `tween(120)` |

```kotlin
// Add-to-collection confirm — the canonical Bandcamp micro-motion
val scale by animateFloatAsState(if (added) 1f else 0.8f, tween(150), label = "collect")
Icon(Icons.Filled.CheckCircle, null, tint = BandcampColors.Teal, modifier = Modifier.scale(scale))
```

Haptics: use `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on Buy tap, add-to-collection, and wishlist toggle. Purchase success shows a snackbar, not a modal takeover.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Bandcamp's iconography is simple/outline-leaning; the waveform is a drawn bar set.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Overflow menu | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Play | `play.fill` | `Icons.Filled.PlayArrow` |
| Pause | `pause.fill` | `Icons.Filled.Pause` |
| Next track | `forward.fill` | `Icons.Filled.SkipNext` |
| Previous track | `backward.fill` | `Icons.Filled.SkipPrevious` |
| Wishlist (add) | `heart` | `Icons.Outlined.FavoriteBorder` |
| Wishlist (added) | `heart.fill` | `Icons.Filled.Favorite` |
| In collection | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |
| Gift this | `gift` | `Icons.Filled.CardGiftcard` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Feed (tab) | `house.fill` | `Icons.Filled.Home` |
| Discover (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Collection (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Profile (tab) | `person.fill` | `Icons.Filled.Person` |
| Download | `arrow.down.circle` | `Icons.Filled.DownloadForOffline` |
| Buy / cart | `bag` | `Icons.Filled.ShoppingBag` |
| Merch | `tshirt` | `Icons.Filled.Checkroom` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the white canvas wants dark-content system bars in light mode (and light-content in dark). Album art may extend full-bleed under the status bar with the top bar floating.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen/album titles, artist link, body, prices, track names. Pin layout-sensitive text (tab labels, track numbers, captions, price unit) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **Font choice**: bundle the Bandcamp face if licensed; otherwise ship **DM Sans** (SIL OFL) — never leave titles to the system default.
- **TalkBack**: label album art "{Album} by {Artist}, album artwork"; expose the buy card as ONE element announcing "{price} {currency} or more, Buy Digital Album"; tracklist rows as "Track {n}, {name}, {duration}{, now playing}". The price is critical — never hide it; convey the playing track in the row label, not teal color alone.
- **Touch targets**: Material guidance is 48.dp. The Buy button is full-width (≥48.dp tall); give the 36.dp play button and 22.dp tab icons a 48.dp hit area; tracklist rows are full-row tappable (~42.dp).
- **Contrast**: `#1A1A1A` on `#FFFFFF` passes WCAG AAA; `#1DA0C3` on white passes AA for ≥14sp and white-on-teal passes AA for the Buy button; `#767676` on white passes AA for ≥14sp. In dark mode validate `#3DB5D6` on `#101417`.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the album-art zoom and the add-to-collection scale (instant state); player progress is functional, keep it.
- **Dark mode**: a true inversion to `#101417` / `#EDF1F3` with teal → `#3DB5D6` — but it is NOT the default; follow `isSystemInDarkTheme()`. Do **not** enable Material You `dynamicColorScheme()` — Bandcamp's single teal (and the paper-editorial identity) must hold regardless of wallpaper. **Never** tint, dim, or shadow album art — the artwork the artist uploaded must always render full-color and untouched.
