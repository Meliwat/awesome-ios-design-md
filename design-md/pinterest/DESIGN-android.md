# Pinterest (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Pinterest's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Pinterest's stark-white photo canvas, single Pinterest-Red verb, the staggered masonry mosaic) while making everything idiomatic Android — a `LazyVerticalStaggeredGrid` instead of a hand-rolled SwiftUI two-column split, `NavigationBar` instead of a UITabBar, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for the remote pin imagery that defines the feed.

## 1. Color Tokens

```kotlin
// ui/theme/PinterestColors.kt
import androidx.compose.ui.graphics.Color

object PinterestColors {
    // Brand — the single accent, the app's verb
    val Red        = Color(0xFFE60023)
    val RedPressed = Color(0xFFAD081B)
    val RedHover   = Color(0xFFCC0020)

    // Canvas & Surfaces (Light — default)
    val CanvasLight   = Color(0xFFFFFFFF)
    val Surface1Light = Color(0xFFF8F8F8)
    val InputLight    = Color(0xFFEFEFEF)
    val DividerLight  = Color(0xFFE9E9E9)

    // Canvas & Surfaces (Dark)
    val CanvasDark   = Color(0xFF121212)
    val Surface1Dark = Color(0xFF1E1E1E)
    val Surface2Dark = Color(0xFF2A2A2A)
    val DividerDark  = Color(0xFF2E2E2E)

    // Text
    val TextPrimaryLight   = Color(0xFF111111)
    val TextSecondaryLight = Color(0xFF767676)
    val TextTertiaryLight  = Color(0xFFB5B5B5)
    val TextPrimaryDark    = Color(0xFFFFFFFF)
    val TextSecondaryDark  = Color(0xFFAAAAAA)

    // Semantic
    val Success = Color(0xFF008A3C)
    val Info    = Color(0xFF0074E8)
}
```

Pinterest's default is a **light, photo-first canvas** — pins supply all the color, so the chrome must disappear behind stark white. Wire a Material 3 `lightColorScheme` so ripples, dividers, and stock component defaults inherit the brand. `primary` is the Pinterest Red verb; `onPrimary` is white (the Save pill is white-on-red).

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val PinterestLight = lightColorScheme(
    primary        = PinterestColors.Red,
    onPrimary      = Color.White,
    background     = PinterestColors.CanvasLight,
    onBackground   = PinterestColors.TextPrimaryLight,
    surface        = PinterestColors.CanvasLight,
    onSurface      = PinterestColors.TextPrimaryLight,
    surfaceVariant = PinterestColors.Surface1Light,
    outline        = PinterestColors.DividerLight,
    error          = PinterestColors.Red, // brand red doubles as error
)

private val PinterestDark = darkColorScheme(
    primary        = PinterestColors.Red,
    onPrimary      = Color.White,
    background     = PinterestColors.CanvasDark,   // warm dark, not #000000
    onBackground   = PinterestColors.TextPrimaryDark,
    surface        = PinterestColors.Surface1Dark,
    onSurface      = PinterestColors.TextPrimaryDark,
    surfaceVariant = PinterestColors.Surface2Dark,
    outline        = PinterestColors.DividerDark,
    error          = PinterestColors.Red,
)

@Composable
fun PinterestTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) PinterestDark else PinterestLight,
    typography  = PinterestTypography,
    content     = content,
)
```

## 2. Typography

Pinterest Sans (2022 refresh) is proprietary. Drop the TTFs in `res/font/` (lowercase, snake_case) and build a `FontFamily`. Fall back to the system font (Roboto) — its humanist proportions are the closest free substitute on Android; Pinterest Sans only ships 400/500/600/700, so never request thin/light/black.

```kotlin
// ui/theme/PinterestType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val PinterestSans = FontFamily(
    Font(R.font.pinterest_sans_regular,  FontWeight.Normal),   // 400
    Font(R.font.pinterest_sans_medium,   FontWeight.Medium),   // 500 — pin titles
    Font(R.font.pinterest_sans_semibold, FontWeight.SemiBold), // 600 — usernames
    Font(R.font.pinterest_sans_bold,     FontWeight.Bold),     // 700 — headlines / CTAs
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object PinterestText {
    val PinDetailTitle = TextStyle(PinterestSans, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.3).sp)
    val LargeTitle     = TextStyle(PinterestSans, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.2).sp)
    val SectionHeader  = TextStyle(PinterestSans, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 25.sp, letterSpacing = (-0.1).sp)
    val BoardName      = TextStyle(PinterestSans, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 21.sp, letterSpacing = (-0.1).sp)
    val PinTitle       = TextStyle(PinterestSans, fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 18.sp)
    val Username       = TextStyle(PinterestSans, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 18.sp)
    val Body           = TextStyle(PinterestSans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Comment        = TextStyle(PinterestSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val Meta           = TextStyle(PinterestSans, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Button         = TextStyle(PinterestSans, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 16.sp)
    val ButtonSmall    = TextStyle(PinterestSans, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val TabLabel       = TextStyle(PinterestSans, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Micro          = TextStyle(PinterestSans, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val PinterestTypography = Typography(
    headlineLarge = PinterestText.PinDetailTitle,
    headlineSmall = PinterestText.SectionHeader,
    titleMedium   = PinterestText.BoardName,
    bodyMedium    = PinterestText.Body,
    labelSmall    = PinterestText.TabLabel,
)
```

## 3. Signature Components

### Save Button (the red → black "Saved" morph)

The app's verb. Default is the Pinterest-Red pill; on commit it morphs to a black pill reading "Saved" — the moment the user knows the pin is committed.

```kotlin
import androidx.compose.animation.animateColorAsState
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun PinterestSaveButton(
    isSaved: Boolean,
    onToggle: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "saveScale")
    val bg by animateColorAsState(
        targetValue = when {
            isSaved -> PinterestColors.TextPrimaryLight  // black "Saved"
            pressed -> PinterestColors.RedPressed
            else    -> PinterestColors.Red
        },
        animationSpec = spring(dampingRatio = 0.8f, stiffness = 500f), // ~200ms morph
        label = "saveBg",
    )
    val haptics = LocalHapticFeedback.current

    Text(
        text = if (isSaved) "Saved" else "Save",
        style = PinterestText.Button,
        color = Color.White,
        modifier = modifier
            .scale(scale)
            .clip(CircleShape) // 500dp full pill
            .background(bg)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ~iOS success
                onToggle()
            }
            .padding(horizontal = 20.dp, vertical = 10.dp),
    )
}
```

### Primary Pill (Follow / Sign up)

```kotlin
import androidx.compose.foundation.layout.Box
import androidx.compose.ui.Alignment

enum class PinPillStyle { Primary, Secondary }

@Composable
fun PinterestPillButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    style: PinPillStyle = PinPillStyle.Primary,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.97f else 1f, label = "pillScale")
    val primary = style == PinPillStyle.Primary

    Box(
        modifier = modifier
            .scale(scale)
            .clip(CircleShape)
            .background(
                when {
                    primary && pressed -> PinterestColors.RedPressed
                    primary            -> PinterestColors.Red
                    else               -> PinterestColors.InputLight
                }
            )
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 24.dp, vertical = 12.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(
            text = text,
            style = PinterestText.Button,
            color = if (primary) Color.White else PinterestColors.TextPrimaryLight,
        )
    }
}
```

### Pin Tile (the masonry cell)

The tile renders the pin image at its **native aspect ratio** with a 16.dp corner radius — no shadow, no border — then a 2-line title and a creator row below.

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import coil.compose.AsyncImage

@Composable
fun PinTile(
    imageUrl: String,
    title: String,
    creatorName: String,
    creatorAvatarUrl: String,
    aspectRatio: Float, // width / height — e.g. 0.75f for a 3:4 pin
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    var isSaved by remember { mutableStateOf(false) }

    Column(
        modifier = modifier.clickable(onClick = onClick),
        verticalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        Box {
            AsyncImage(
                model = imageUrl,
                contentDescription = title,
                contentScale = ContentScale.Crop,
                modifier = Modifier
                    .fillMaxWidth()
                    .aspectRatio(aspectRatio)
                    .clip(RoundedCornerShape(16.dp)), // signature soft corner — no shadow
            )
            PinterestSaveButton(
                isSaved = isSaved,
                onToggle = { isSaved = !isSaved },
                modifier = Modifier
                    .align(Alignment.TopEnd)
                    .padding(12.dp),
            )
        }
        Text(
            title,
            style = PinterestText.PinTitle,
            color = PinterestColors.TextPrimaryLight,
            maxLines = 2,
            overflow = TextOverflow.Ellipsis,
        )
        Row(
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            AsyncImage(
                model = creatorAvatarUrl,
                contentDescription = null,
                contentScale = ContentScale.Crop,
                modifier = Modifier.size(20.dp).clip(CircleShape),
            )
            Text(
                creatorName,
                style = PinterestText.Meta,
                color = PinterestColors.TextSecondaryLight,
                maxLines = 1,
                overflow = TextOverflow.Ellipsis,
            )
        }
    }
}
```

### Floating Search Bar (Home)

A pill floating over the feed with a soft Level-1 shadow in light mode (`elevation`/`shadow`); on dark it sits on `Surface1Dark` with no shadow.

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Search
import androidx.compose.material3.Icon
import androidx.compose.ui.draw.shadow

@Composable
fun FloatingSearchBar(
    query: String,
    onQueryChange: (String) -> Unit,
    modifier: Modifier = Modifier,
) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp)
            .shadow(8.dp, CircleShape, spotColor = Color.Black.copy(alpha = 0.08f))
            .clip(CircleShape)
            .background(PinterestColors.CanvasLight)
            .padding(horizontal = 18.dp)
            .height(48.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(10.dp),
    ) {
        Icon(
            Icons.Filled.Search,
            contentDescription = null,
            tint = PinterestColors.TextPrimaryLight,
            modifier = Modifier.size(20.dp),
        )
        BasicTextField(
            value = query,
            onValueChange = onQueryChange,
            singleLine = true,
            textStyle = PinterestText.Body.copy(color = PinterestColors.TextPrimaryLight),
            decorationBox = { inner ->
                if (query.isEmpty()) {
                    Text("Search for ideas", style = PinterestText.Body, color = PinterestColors.TextSecondaryLight)
                }
                inner()
            },
            modifier = Modifier.weight(1f),
        )
    }
}
```

## 4. The Masonry Grid (Pinterest's #1 signature)

iOS SwiftUI has no native masonry, so the SwiftUI guide hand-splits items into two columns by cumulative height. **Compose has a first-class answer**: `LazyVerticalStaggeredGrid`. Each item keeps its native aspect ratio; the grid drops each tile into the shorter column automatically — the Tetris fall is built in. Keep the gap tight at 8.dp both directions so it reads as a mosaic.

```kotlin
import androidx.compose.foundation.lazy.staggeredgrid.LazyVerticalStaggeredGrid
import androidx.compose.foundation.lazy.staggeredgrid.StaggeredGridCells
import androidx.compose.foundation.lazy.staggeredgrid.items
import androidx.compose.ui.unit.dp

data class Pin(
    val id: String,
    val imageUrl: String,
    val aspectRatio: Float, // MUST be known before render so the grid doesn't jump
    val title: String,
    val creatorName: String,
    val creatorAvatarUrl: String,
)

@Composable
fun MasonryFeed(
    pins: List<Pin>,
    onPinClick: (Pin) -> Unit,
    modifier: Modifier = Modifier,
) {
    LazyVerticalStaggeredGrid(
        columns = StaggeredGridCells.Fixed(2), // 2 on phones; bump to 3/4 on tablets via windowSizeClass
        verticalItemSpacing = 8.dp,            // tight mosaic gap
        horizontalArrangement = Arrangement.spacedBy(8.dp),
        contentPadding = PaddingValues(horizontal = 16.dp, vertical = 12.dp),
        modifier = modifier.fillMaxSize(),
    ) {
        items(pins, key = { it.id }) { pin ->
            PinTile(
                imageUrl = pin.imageUrl,
                title = pin.title,
                creatorName = pin.creatorName,
                creatorAvatarUrl = pin.creatorAvatarUrl,
                aspectRatio = pin.aspectRatio,
                onClick = { onPinClick(pin) },
                // fade-up entrance per DESIGN.md §6
                modifier = Modifier.animateItem(),
            )
        }
    }
}
```

**Critical implementation note** (carried from the spec): you MUST know each pin's aspect ratio before rendering. If the server doesn't supply dimensions, render a placeholder box at the eventual aspect (cache it per pin id) so the staggered grid never reflows when images load. Coil's `placeholder`/`error` slots can hold a `Surface1Light` rectangle at that aspect.

## 5. Bottom Navigation (Tab Bar)

Pinterest uses an **icon-only** Material 3 `NavigationBar` with five destinations and a distinctive **red center Create button** — the only non-outlined tab. Active tint is `#111111` (light) / white (dark), never red — red is reserved for the Save verb and CTAs. Android has no live blur; use a solid canvas surface with a 1.dp top hairline.

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material.icons.outlined.*
import androidx.compose.material3.*

@Composable
fun PinterestBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(
        containerColor = PinterestColors.CanvasLight,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            Triple("Home", Icons.Filled.Home, Icons.Outlined.Home),
            Triple("Search", Icons.Filled.Search, Icons.Outlined.Search),
            null, // Create — rendered specially below
            Triple("Notifications", Icons.Filled.Notifications, Icons.Outlined.Notifications),
            Triple("Profile", Icons.Filled.Person, Icons.Outlined.Person),
        )
        items.forEachIndexed { i, item ->
            if (item == null) {
                // Red Create button — 48dp rounded-rect, brand red, white +
                NavigationBarItem(
                    selected = selected == i,
                    onClick = { onSelect(i) },
                    icon = {
                        Box(
                            Modifier
                                .size(48.dp)
                                .clip(RoundedCornerShape(14.dp))
                                .background(PinterestColors.Red),
                            contentAlignment = Alignment.Center,
                        ) {
                            Icon(Icons.Filled.Add, "Create", tint = Color.White, modifier = Modifier.size(22.dp))
                        }
                    },
                    colors = NavigationBarItemDefaults.colors(indicatorColor = Color.Transparent),
                )
            } else {
                val (label, filled, outlined) = item
                NavigationBarItem(
                    selected = selected == i,
                    onClick = { onSelect(i) },
                    icon = {
                        Icon(
                            if (selected == i) filled else outlined,
                            contentDescription = label,
                            modifier = Modifier.size(26.dp),
                        )
                    },
                    // no labels — Pinterest's house style
                    colors = NavigationBarItemDefaults.colors(
                        selectedIconColor   = PinterestColors.TextPrimaryLight, // not red
                        unselectedIconColor = PinterestColors.TextSecondaryLight,
                        indicatorColor      = Color.Transparent, // no Material pill
                    ),
                )
            }
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Save morph | `animateColorAsState` red → black with `spring(dampingRatio = 0.8f)` (~200ms); `HapticFeedbackType.LongPress` |
| Pin tap → detail | `SharedTransitionLayout` + `Modifier.sharedElement()` on the pin image (Compose 1.7+); fallback `AnimatedVisibility` + `scaleIn` from grid position |
| Masonry entrance | `Modifier.animateItem()` on each staggered cell — fade + 8.dp slide-up as pins stream in |
| Follow button | `animateColorAsState` red → gray "Following" over 250ms cross-fade + success haptic |
| Pull-to-refresh | `rememberInfiniteTransition` rotating the 28.dp red "P" logomark |
| Long-press pin | `animateFloatAsState` scale → 0.96 + haptic, then context-menu `AnimatedVisibility` scale-in 300ms |

```kotlin
// Shared-element pin → detail (Compose 1.7+ SharedTransitionLayout)
@OptIn(ExperimentalSharedTransitionApi::class)
@Composable
fun SharedTransitionScope.SharedPinImage(
    imageUrl: String,
    key: String,
    animatedScope: AnimatedVisibilityScope,
) {
    AsyncImage(
        model = imageUrl,
        contentDescription = null,
        contentScale = ContentScale.Crop,
        modifier = Modifier
            .sharedElement(
                rememberSharedContentState(key = "pin-$key"),
                animatedVisibilityScope = animatedScope,
            )
            .clip(RoundedCornerShape(16.dp)),
    )
}
```

Springs stay subtle (0.2–0.35s range) — Pinterest never animates aggressively. Haptics: prefer `LocalHapticFeedback`; for the Save success use `HapticFeedbackType.LongPress`, or a `Vibrator` `VibrationEffect.createOneShot(12, ...)` to approximate iOS's success notification.

## 7. Icons

Pinterest ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. The red "P" pull-to-refresh logomark ships as a vector drawable loaded via `ImageVector.vectorResource(R.drawable.ic_pinterest_p)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house` / `house.fill` | `Icons.Outlined.Home` / `Icons.Filled.Home` |
| Notifications (tab) | `bell` / `bell.fill` | `Icons.Outlined.Notifications` / `Icons.Filled.Notifications` |
| Profile (tab) | `person` / `person.fill` | `Icons.Outlined.Person` / `Icons.Filled.Person` |
| Create (center) | `plus` | `Icons.Filled.Add` |
| Heart (react) | `heart` / `heart.fill` | `Icons.Outlined.FavoriteBorder` / `Icons.Filled.Favorite` |
| Comment | `bubble.right` | `Icons.AutoMirrored.Filled.Comment` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Shop tag | `tag.fill` | `Icons.Filled.LocalOffer` |
| Idea Pin | `sparkles` | `Icons.Filled.AutoAwesome` |
| Save confirm | `checkmark` | `Icons.Filled.Check` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; staggered grid + modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The light canvas wants dark-content system bars; apply `Modifier.windowInsetsPadding(WindowInsets.systemBars)` or `Scaffold` insets so the floating search clears the status bar and the tab bar clears gesture nav.
- **Font scaling**: `sp` honors the user's font scale. Keep it on pin-detail title, descriptions, comments. **Pin caption titles in the masonry feed must be a fixed size** — the staggered layout computes tile heights from text + aspect, so wrap captions in `CompositionLocalProvider(LocalDensity provides Density(LocalDensity.current.density, fontScale = 1f))` to keep the grid stable. Save-button text is also pinned (pill geometry).
- **TalkBack**: merge a pin tile's contents with `Modifier.semantics(mergeDescendants = true)` so the user hears "Save {title} by {creator}" as one element; expose the Save button as a separate nested action.
- **Touch targets**: Material guidance is 48.dp minimum. The Save pill clears it; the 20.dp creator avatar and small icon buttons need a 44–48.dp hit area via padding even though the glyph is small. The Create button is 48.dp by design.
- **Contrast**: `#767676` on `#FFFFFF` passes WCAG AA at 14sp+; validate the 12sp metadata and 11sp micro-labels with a contrast checker and bump toward `#5F5F5F` if targeting strict AA at small sizes. Dark-mode `#AAAAAA` on `#121212` also passes.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Pinterest's identity depends on the fixed stark `#FFFFFF` canvas and the single Pinterest-Red verb regardless of wallpaper. Pins are the color story; the chrome must stay neutral.
