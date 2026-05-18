# Plenty of Fish (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Plenty of Fish's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the match grid + Meet Me card, the tab bar, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (POF's dominant blue, the photo-forward 2-column match grid, Meet Me's yes/maybe/no card, aggressive teal presence, messages-first) while making everything idiomatic Android — a `NavigationBar` with a Material `Badge`, a `LazyVerticalGrid`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for profile photos. No color extraction — POF's palette is fixed. A full dark scheme is provided (cool near-black, never true black).

## 1. Color Tokens

```kotlin
// ui/theme/POFColors.kt
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.Brush

object POFColors {
    // Canvas & Surfaces (Light)
    val White        = Color(0xFFFFFFFF)
    val Background   = Color(0xFFF4F5F7)
    val PressedLight = Color(0xFFE9EBEF)
    val DividerLight = Color(0xFFE4E6EA)

    // Canvas & Surfaces (Dark)
    val CanvasDark = Color(0xFF121417) // cool near-black — NOT pure black
    val Surface1   = Color(0xFF1A1D21)
    val Surface2   = Color(0xFF23272C)
    val Divider    = Color(0xFF2C3036)

    // Text
    val TextPrimary    = Color(0xFFEDEFF2) // dark
    val TextPrimaryLt  = Color(0xFF16181C) // light
    val TextSecondary  = Color(0xFF9BA1AB)
    val TextTertiary   = Color(0xFF6B7079)
    val OnPhoto        = Color(0xFFFFFFFF)

    // Brand
    val Blue        = Color(0xFF0098DB)
    val BlueLight   = Color(0xFF00A6E2)
    val BluePressed = Color(0xFF0079B0)

    // Accent / status
    val Teal = Color(0xFF00C9B7) // online
    val Pink = Color(0xFFFF4F8B) // unread
    val Gold = Color(0xFFFFB23E) // upgrade

    // Semantic
    val Success = Color(0xFF20C997)
    val Error   = Color(0xFFFF5267)

    // The POF brand gradient — logo, primary CTA, the Meet Me "yes" button
    val BrandGradient = Brush.linearGradient(listOf(Blue, BlueLight))
    // Bottom-of-card scrim for white name/meta over photos
    val Scrim = Brush.verticalGradient(listOf(Color.Transparent, Color.Black.copy(alpha = 0.78f)))
}
```

Wire it into both schemes; the dark scheme uses cool near-black `#121417`, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val POFLight = lightColorScheme(
    primary        = POFColors.Blue,
    onPrimary      = POFColors.White,
    background      = POFColors.Background,
    onBackground    = POFColors.TextPrimaryLt,
    surface         = POFColors.White,
    onSurface       = POFColors.TextPrimaryLt,
    surfaceVariant  = POFColors.PressedLight,
    outline         = POFColors.DividerLight,
    error           = POFColors.Error,
)

private val POFDark = darkColorScheme(
    primary        = POFColors.Blue,
    onPrimary      = POFColors.White,
    background      = POFColors.CanvasDark,
    onBackground    = POFColors.TextPrimary,
    surface         = POFColors.Surface1,
    onSurface       = POFColors.TextPrimary,
    surfaceVariant  = POFColors.Surface2,
    outline         = POFColors.Divider,
    error           = POFColors.Error,
)

@Composable
fun POFTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) POFDark else POFLight,
    typography = POFTypography,
    content = content,
)
```

## 2. Typography

POF's UI sans stand-in: **Nunito Sans** (SIL OFL) — drop the TTFs in `res/font/`. Heavy rounded weights (800/900) for names and titles; approachable, unintimidating.

```kotlin
// ui/theme/POFType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Nunito = FontFamily(
    Font(R.font.nunitosans_regular,   FontWeight.Normal),
    Font(R.font.nunitosans_semibold,  FontWeight.SemiBold),
    Font(R.font.nunitosans_bold,      FontWeight.Bold),
    Font(R.font.nunitosans_extrabold, FontWeight.ExtraBold),
    Font(R.font.nunitosans_black,     FontWeight.Black),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object POFText {
    val Display  = TextStyle(Nunito, fontWeight = FontWeight.Black,     fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val Title    = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 31.sp, letterSpacing = (-0.3).sp)
    val Section  = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val Profile  = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Body     = TextStyle(Nunito, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 24.sp)
    val CardName = TextStyle(Nunito, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 19.sp)
    val Button   = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 16.sp, lineHeight = 16.sp)
    val Meta     = TextStyle(Nunito, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 20.sp)
    val CardMeta = TextStyle(Nunito, fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 16.sp)
    val Chip     = TextStyle(Nunito, fontWeight = FontWeight.Bold,      fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.1.sp)
    val Tab      = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Badge    = TextStyle(Nunito, fontWeight = FontWeight.ExtraBold, fontSize = 9.sp,  lineHeight = 11.sp, letterSpacing = 0.2.sp)
    val Bubble   = TextStyle(Nunito, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 21.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val POFTypography = Typography(
    headlineLarge = POFText.Display,
    headlineMedium = POFText.Title,
    titleLarge    = POFText.Section,
    titleMedium   = POFText.Profile,
    bodyMedium    = POFText.Body,
    labelSmall    = POFText.Tab,
)
```

## 3. Signature Components

### Profile Grid Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun ProfileGridCard(
    photoUrl: String, name: String, age: Int, meta: String,
    isOnline: Boolean, onOpen: () -> Unit, onLike: () -> Unit,
) {
    Box(
        Modifier
            .fillMaxWidth()
            .aspectRatio(3f / 4f)
            .clip(RoundedCornerShape(16.dp))
            .clickable(onClick = onOpen),
    ) {
        AsyncImage(photoUrl, null, Modifier.matchParentSize(), contentScale = ContentScale.Crop)

        if (isOnline) {
            Row(
                Modifier
                    .align(Alignment.TopStart)
                    .padding(10.dp)
                    .clip(RoundedCornerShape(999.dp))
                    .background(Color.Black.copy(alpha = 0.42f))
                    .padding(horizontal = 9.dp, vertical = 4.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(5.dp),
            ) {
                Box(Modifier.size(7.dp).clip(CircleShape).background(POFColors.Teal))
                Text("Online", style = POFText.Chip, color = POFColors.OnPhoto)
            }
        }

        Box(Modifier.fillMaxWidth().height(90.dp).align(Alignment.BottomCenter).background(POFColors.Scrim))

        Column(Modifier.align(Alignment.BottomStart).padding(12.dp)) {
            Text("$name, $age", style = POFText.CardName, color = POFColors.OnPhoto)
            Text(meta, style = POFText.CardMeta, color = POFColors.OnPhoto.copy(alpha = 0.85f))
        }

        Box(
            Modifier
                .align(Alignment.BottomEnd)
                .padding(12.dp)
                .size(36.dp)
                .clip(CircleShape)
                .background(POFColors.Blue)
                .clickable(onClick = onLike),
            contentAlignment = Alignment.Center,
        ) { Icon(Icons.Filled.Favorite, "Like $name", tint = POFColors.OnPhoto, modifier = Modifier.size(18.dp)) }
    }
}
```

### Match Grid

```kotlin
import androidx.compose.foundation.lazy.grid.GridCells
import androidx.compose.foundation.lazy.grid.LazyVerticalGrid
import androidx.compose.foundation.lazy.grid.items

@Composable
fun MatchGrid(profiles: List<ProfileUi>) {
    LazyVerticalGrid(
        columns = GridCells.Fixed(2),
        contentPadding = PaddingValues(14.dp),
        horizontalArrangement = Arrangement.spacedBy(10.dp),
        verticalArrangement = Arrangement.spacedBy(10.dp),
        modifier = Modifier.fillMaxSize().background(POFColors.CanvasDark),
    ) {
        items(profiles, key = { it.id }) { p ->
            ProfileGridCard(p.photoUrl, p.name, p.age, p.meta, p.isOnline, p.onOpen, p.onLike)
        }
    }
}
```

### Meet Me Card

```kotlin
import androidx.compose.foundation.border
import androidx.compose.material.icons.filled.Close
import androidx.compose.material.icons.filled.QuestionMark
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun MeetMeCard(
    photoUrl: String, name: String, age: Int,
    onNo: () -> Unit, onMaybe: () -> Unit, onYes: () -> Unit,
) {
    Column(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(20.dp))
            .background(POFColors.Surface1)
            .padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        AsyncImage(
            photoUrl, null,
            Modifier.fillMaxWidth().aspectRatio(1f).clip(RoundedCornerShape(16.dp)),
            contentScale = ContentScale.Crop,
        )
        Text("$name, $age", style = POFText.Profile, color = POFColors.TextPrimary)
        Text("Would you like to meet $name?",
             style = POFText.Meta.copy(fontWeight = FontWeight.Bold), color = POFColors.TextSecondary)
        Row(horizontalArrangement = Arrangement.spacedBy(14.dp)) {
            RoundBtn(Icons.Filled.Close, POFColors.Divider, POFColors.TextSecondary, onNo)
            RoundBtn(Icons.Filled.QuestionMark, POFColors.Gold, POFColors.Gold, onMaybe)
            Box(
                Modifier.size(56.dp).clip(CircleShape).background(POFColors.Blue).clickable(onClick = onYes),
                contentAlignment = Alignment.Center,
            ) { Icon(Icons.Filled.Favorite, "Yes, like $name", tint = POFColors.OnPhoto, modifier = Modifier.size(24.dp)) }
        }
    }
}

@Composable
private fun RoundBtn(icon: ImageVector, borderColor: Color, tint: Color, onClick: () -> Unit) {
    Box(
        Modifier
            .size(56.dp)
            .clip(CircleShape)
            .background(POFColors.Surface1)
            .border(1.5.dp, borderColor, CircleShape)
            .clickable(onClick = onClick),
        contentAlignment = Alignment.Center,
    ) { Icon(icon, null, tint = tint, modifier = Modifier.size(22.dp)) }
}
```

### Buttons + "Online" Pill

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.material.icons.filled.WorkspacePremium

@Composable
fun PrimaryButton(title: String, onClick: () -> Unit) {
    Box(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(POFColors.BrandGradient)
            .clickable(onClick = onClick)
            .padding(horizontal = 28.dp, vertical = 14.dp),
        contentAlignment = Alignment.Center,
    ) { Text(title, style = POFText.Button, color = POFColors.White) }
}

@Composable
fun UpgradeButton(title: String, onClick: () -> Unit) {
    Row(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(POFColors.Gold)
            .clickable(onClick = onClick)
            .padding(horizontal = 18.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(Icons.Filled.WorkspacePremium, null, tint = Color(0xFF2A1B05), modifier = Modifier.size(12.dp))
        Text(title, style = POFText.Button.copy(fontSize = 14.sp), color = Color(0xFF2A1B05))
    }
}

@Composable
fun OnlinePill() {
    val t = rememberInfiniteTransition(label = "pulse")
    val alpha by t.animateFloat(1f, 0.5f, infiniteRepeatable(tween(2000), RepeatMode.Reverse), label = "dot")
    Row(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(Color.Black.copy(alpha = 0.42f))
            .padding(horizontal = 9.dp, vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(5.dp),
    ) {
        Box(Modifier.size(7.dp).clip(CircleShape).background(POFColors.Teal.copy(alpha = alpha)))
        Text("Online", style = POFText.Chip, color = POFColors.OnPhoto)
    }
}
```

## 4. Navigation

5 slots; active is POF Blue (no Material indicator pill — POF has none). The Messages tab uses a Material `Badge` in pink.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun POFBottomBar(selected: Int, unread: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = POFColors.CanvasDark, tonalElevation = 0.dp) {
        val items = listOf(
            "Matches"  to Icons.Filled.GridView,
            "Meet Me"  to Icons.Filled.Favorite,
            "Search"   to Icons.Filled.Search,
            "Messages" to Icons.Filled.Chat,
            "Me"       to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    if (i == 3 && unread > 0) {
                        BadgedBox(badge = { Badge(containerColor = POFColors.Pink) { Text("$unread", style = POFText.Badge, color = POFColors.White) } }) {
                            Icon(icon, label, modifier = Modifier.size(23.dp))
                        }
                    } else {
                        Icon(icon, label, modifier = Modifier.size(23.dp))
                    }
                },
                label = { Text(label, style = POFText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = POFColors.Blue,
                    selectedTextColor = POFColors.Blue,
                    unselectedIconColor = POFColors.TextSecondary,
                    unselectedTextColor = POFColors.TextSecondary,
                    indicatorColor = Color.Transparent, // no Material pill — POF has none
                ),
            )
        }
    }
}
```

The top bar holds the POF logo + wordmark and filter/search glyphs in `Blue`, with a segmented strip (Matches / Meet Me / Search) — active item ExtraBold primary with a 3.dp POF-Blue underline. The profile detail is a full-bleed `HorizontalPager` photo carousel with dots, an overlaid name+age, scrollable bio/interest chips, and a sticky bottom bar with a gradient "Send Message" pill.

## 5. Motion

POF motion is friendly — 180–320ms, soft and confident.

| Moment | Compose recipe |
|--------|----------------|
| Like (grid card) | heart `animateFloatAsState` scale 1 → 1.25 → 1 via `keyframes` (~220ms); light haptic |
| Meet Me decision | card `offsetX` `animateDpAsState` to ±screen `tween(260)` (yes/no); "maybe" `offsetY` up `tween(240)` |
| Meet Me drag | `Modifier.draggable`; `rotationZ` ≈ dragX / 18; commit > 35% width else `spring(dampingRatio = 0.8f)` back |
| Online dot pulse | `rememberInfiniteTransition` alpha 1 ↔ 0.5 `tween(2000)` `Reverse` |
| Tab active pop | selected icon `animateFloatAsState` scale → 1.05 `tween(120)` |
| Profile open | shared-element `AnimatedContent` zoom into the carousel `tween(320)` |
| Message send | bubble `scaleIn(tween(180)) + fadeIn` from the composer |
| Skeleton shimmer | `rememberInfiniteTransition` sweep over 1200ms `LinearEasing` |

```kotlin
// Like heart pop — the canonical POF micro-interaction
val scale by animateFloatAsState(
    targetValue = if (liked) 1f else 1f,
    animationSpec = keyframes { durationMillis = 220; 1.25f at 110; 1f at 220 },
    label = "heartPop",
)
Modifier.graphicsLayer { scaleX = scale; scaleY = scale }
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` for the light tick on like, Meet Me decision and tab switch. Use `HapticFeedbackConstants.CONFIRM` on a new match. Presence is live but quiet — the dot pulse has no haptic.

## 6. Icons

POF ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. Blue = brand/action; teal = online; pink = unread; gold = upgrade.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Matches (tab) | `square.grid.2x2` | `Icons.Filled.GridView` |
| Meet Me (tab) | `heart` | `Icons.Filled.Favorite` |
| Search (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Messages (tab) | `bubble.left.and.bubble.right` | `Icons.Filled.Chat` |
| Me (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Like (card / yes) | `heart.fill` | `Icons.Filled.Favorite` |
| Meet Me — no | `xmark` | `Icons.Filled.Close` |
| Meet Me — maybe | `questionmark` | `Icons.Filled.QuestionMark` |
| Filter | `slider.horizontal.3` | `Icons.Filled.Tune` |
| Upgrade / premium | `crown.fill` | `Icons.Filled.WorkspacePremium` |
| Send message | `arrow.up.circle.fill` | `Icons.Filled.Send` |
| Photo (composer) | `photo` | `Icons.Filled.Image` |
| Block / report | `exclamationmark.shield` | `Icons.Filled.Flag` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Distance | `location.fill` | `Icons.Filled.LocationOn` |
| Verified | `checkmark.seal.fill` | `Icons.Filled.Verified` |
| Online dot | (teal circle) | (teal `Box` + `CircleShape`) |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion + `BadgedBox` comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the cool near-black canvas wants light-content system bars (dark-content on the light theme). The message composer must sit above the IME — use `Modifier.imePadding()`.
- **Font scaling**: `sp` honors the user's font scale — keep it on display, titles, body, meta. Pin layout-sensitive text (card-scrim name/meta, 10sp tab labels, 9sp badge, 12sp status chips) via `dp`-derived sizes or a fixed-`fontScale` `LocalDensity` so they don't break the photo composition.
- **TalkBack**: merge each grid card into one node — `Modifier.semantics(mergeDescendants = true) { contentDescription = "Profile, $name, $age, $meta${if (isOnline) ", Online" else ""}" }`; expose the like button as a separate `onClickLabel` ("Like $name"); Meet Me buttons get clear labels ("No", "Maybe", "Yes, like $name"); the unread `Badge` needs a `stateDescription` ("$unread unread messages").
- **Touch targets**: Material guidance is 48.dp. The grid-card like button is 36.dp — give it a 48.dp hit via padding; Meet Me buttons are 56.dp; the whole card is clickable to open the profile.
- **Contrast**: white name/meta on the `rgba(0,0,0,0.78)` scrim passes WCAG AA; `#16181C` on `#FFFFFF` and `#EDEFF2` on `#121417` pass AA; verify POF Blue keeps ≥4.5:1 behind white button text (it does at bold 16sp — AA-large).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the heart pop, the online-dot pulse, and Meet Me card-fly — substitute a crossfade; keep selection state changes.
- **Dark mode**: invert via the `Dark*` palette — cool near-black `#121417`, NOT true black. Never desaturate profile photos — only chrome dims. Sheets add a faint 1dp `Divider` top border since shadows nearly vanish on dark. Do **not** enable Material You `dynamicColorScheme()` — POF Blue is the brand and must hold regardless of wallpaper.
