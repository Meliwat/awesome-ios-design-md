# The League (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports The League's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the embossed prospect card + concierge note, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (The League's near-true-black canvas, the single rationed gold, the no-shadow hairline system, the serif/sans split, the calling-card prospect) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `BorderStroke` instead of a shadow, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for prospect photos. The League's palette is fixed black + one gold — no Palette extraction. **The League is dark-native: there is no light scheme** — force the dark aesthetic regardless of system setting.

## 1. Color Tokens

```kotlin
// ui/theme/LeagueColors.kt
import androidx.compose.ui.graphics.Color

object LeagueColors {
    // Canvas & Surfaces (dark-native — the only theme)
    val Canvas   = Color(0xFF0A0A0A) // near-true black — the club after dark
    val Surface1 = Color(0xFF141414)
    val Surface2 = Color(0xFF1C1A17)
    val CardBase = Color(0xFF100F0C)
    val Hairline = Color(0xFF2A2620) // the 1px gold-tinted border — does ALL depth work

    // Text
    val TextPrimary   = Color(0xFFEDE9E2) // warm off-white — never pure white
    val TextSecondary = Color(0xFF9E988C)
    val TextTertiary  = Color(0xFF6B665C)
    val OnGold        = Color(0xFF1A1408)

    // Accent — the SINGLE accent is gold
    val Gold       = Color(0xFFC8A35A)
    val GoldBright = Color(0xFFDBBA71)
    val GoldDeep   = Color(0xFFA8863F)
    val Champagne  = Color(0xFFE8DCC0) // premium / celebration only

    // Semantic
    val Success = Color(0xFF6FAE8A) // muted sage
    val Error   = Color(0xFFC77A7A) // dusty rose
}
```

Wire it into a **dark-only** scheme. The League has no light mode in spirit — force dark.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme

private val LeagueDark = darkColorScheme(
    primary        = LeagueColors.Gold,
    onPrimary      = LeagueColors.OnGold,
    background     = LeagueColors.Canvas,
    onBackground   = LeagueColors.TextPrimary,
    surface        = LeagueColors.Surface1,
    onSurface      = LeagueColors.TextPrimary,
    surfaceVariant = LeagueColors.Surface2,
    outline        = LeagueColors.Hairline,
    error          = LeagueColors.Error,
)

@Composable
fun LeagueTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = LeagueDark, // always dark — never a light variant
        typography  = LeagueTypography,
        content     = content,
    )
```

## 2. Typography

The League pairs a high-contrast display serif (**Cormorant Garamond**) with a clean geometric sans (**Jost**) — both SIL OFL, drop the TTFs in `res/font/`. The serif/sans split IS the hierarchy: serif = the person & institution, sans = the machine & metadata. Never collapse to one family.

```kotlin
// ui/theme/LeagueType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Cormorant = FontFamily(
    Font(R.font.cormorant_regular,  FontWeight.Normal),
    Font(R.font.cormorant_medium,   FontWeight.Medium),
    Font(R.font.cormorant_semibold, FontWeight.SemiBold),
    Font(R.font.cormorant_bold,     FontWeight.Bold),
    Font(R.font.cormorant_medium_italic, FontWeight.Medium, FontStyle.Italic),
)
val Jost = FontFamily(
    Font(R.font.jost_regular,  FontWeight.Normal),
    Font(R.font.jost_medium,   FontWeight.Medium),
    Font(R.font.jost_semibold, FontWeight.SemiBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object LeagueText {
    val Wordmark    = TextStyle(Cormorant, fontWeight = FontWeight.Bold,     fontSize = 34.sp, letterSpacing = 0.2.sp)
    val Name        = TextStyle(Cormorant, fontWeight = FontWeight.Bold,     fontSize = 26.sp, letterSpacing = 0.2.sp)
    val AgeInline   = TextStyle(Cormorant, fontWeight = FontWeight.Normal,   fontSize = 26.sp)
    val ScreenTitle = TextStyle(Cormorant, fontWeight = FontWeight.SemiBold, fontSize = 21.sp, letterSpacing = 0.2.sp)
    val Concierge   = TextStyle(Cormorant, fontWeight = FontWeight.Medium,   fontSize = 16.sp, fontStyle = FontStyle.Italic, lineHeight = 26.sp)
    val Stat        = TextStyle(Cormorant, fontWeight = FontWeight.SemiBold, fontSize = 21.sp)
    val Subtitle    = TextStyle(Jost, fontWeight = FontWeight.Medium,   fontSize = 16.sp, letterSpacing = 0.3.sp)
    val Body        = TextStyle(Jost, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 24.sp)
    val Credential  = TextStyle(Jost, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 22.sp)
    val Eyebrow     = TextStyle(Jost, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, letterSpacing = 2.sp)
    val Button      = TextStyle(Jost, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, letterSpacing = 1.6.sp)
    val Caption     = TextStyle(Jost, fontWeight = FontWeight.Normal,   fontSize = 12.sp)
    val Tab         = TextStyle(Jost, fontWeight = FontWeight.Medium,   fontSize = 9.sp,  letterSpacing = 1.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val LeagueTypography = Typography(
    headlineLarge = LeagueText.Name,
    headlineMedium = LeagueText.ScreenTitle,
    titleMedium   = LeagueText.Subtitle,
    bodyMedium    = LeagueText.Body,
    labelSmall    = LeagueText.Tab,
)
```

> Compose has no automatic `text-transform`. For uppercase eyebrows / buttons / tabs, call `.uppercase()` on the string — never bake casing into the font.

## 3. Signature Components

### Prospect Card (the embossed calling card)

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Card
import androidx.compose.material3.CardDefaults
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Star
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun ProspectCard(
    photoUrl: String,
    name: String,
    age: Int,
    title: String,     // "VP Strategy · Goldman Sachs"
    school: String,    // "Harvard Business School"
    location: String,  // "2 mi · Tribeca, New York"
    verified: Boolean,
    modifier: Modifier = Modifier,
) {
    Card(
        modifier = modifier.fillMaxWidth(),
        shape = RoundedCornerShape(6.dp),
        colors = CardDefaults.cardColors(containerColor = LeagueColors.CardBase),
        border = BorderStroke(1.dp, LeagueColors.Hairline), // NO elevation — depth is the hairline
        elevation = CardDefaults.cardElevation(defaultElevation = 0.dp),
    ) {
        Box {
            AsyncImage(
                model = photoUrl,
                contentDescription = "$name, $age",
                modifier = Modifier.fillMaxWidth().height(280.dp),
                contentScale = ContentScale.Crop,
            )
            if (verified) {
                Box(
                    Modifier
                        .align(Alignment.TopEnd).padding(14.dp)
                        .size(30.dp).clip(RoundedCornerShape(50))
                        .background(Color.Black.copy(alpha = 0.6f))
                        .border(1.dp, LeagueColors.Gold, RoundedCornerShape(50)),
                    contentAlignment = Alignment.Center,
                ) { Icon(Icons.Filled.Star, null, tint = LeagueColors.Gold, modifier = Modifier.size(15.dp)) }
            }
        }
        Column(Modifier.padding(horizontal = 18.dp, vertical = 14.dp)) {
            Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Text(name, style = LeagueText.Name, color = LeagueColors.TextPrimary)
                Text("$age", style = LeagueText.AgeInline, color = LeagueColors.TextSecondary)
            }
            Box(Modifier.padding(vertical = 8.dp).width(28.dp).height(1.dp).background(LeagueColors.Gold))
            Credential(title)
            Credential(school)
            Credential(location)
        }
    }
}

@Composable
private fun Credential(text: String) {
    Row(Modifier.padding(vertical = 2.dp), horizontalArrangement = Arrangement.spacedBy(6.dp)) {
        Text("◦", style = LeagueText.Credential, color = LeagueColors.Gold)
        Text(text, style = LeagueText.Credential, color = LeagueColors.TextSecondary, modifier = Modifier.weight(1f))
    }
}
```

### Card Action Row

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.material.icons.filled.Close
import androidx.compose.material.icons.outlined.ChatBubbleOutline
import androidx.compose.material.icons.outlined.FavoriteBorder
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun CardActions(onPass: () -> Unit, onHeart: () -> Unit, onMessage: () -> Unit) {
    Row(
        Modifier.fillMaxWidth().padding(horizontal = 18.dp),
        horizontalArrangement = Arrangement.SpaceBetween,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        CircleAction(Icons.Filled.Close, 48.dp, LeagueColors.TextSecondary, LeagueColors.Hairline, onPass)
        // Heart is the focal action — gold OUTLINE, not a fill
        CircleAction(Icons.Outlined.FavoriteBorder, 56.dp, LeagueColors.Gold, LeagueColors.Gold, onHeart)
        CircleAction(Icons.Outlined.ChatBubbleOutline, 48.dp, LeagueColors.Gold, LeagueColors.Hairline, onMessage)
    }
}

@Composable
private fun CircleAction(icon: ImageVector, size: Dp, tint: Color, border: Color, onClick: () -> Unit) {
    val haptics = LocalHapticFeedback.current
    Box(
        Modifier
            .size(size).clip(RoundedCornerShape(50))
            .background(LeagueColors.Surface1)
            .border(1.dp, border, RoundedCornerShape(50))
            .clickable {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // soft analog
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) { Icon(icon, contentDescription = null, tint = tint, modifier = Modifier.size(size * 0.4f)) }
}
```

### Concierge Note

```kotlin
import androidx.compose.animation.core.*
import androidx.compose.runtime.*
import androidx.compose.ui.draw.scale

@Composable
fun ConciergeNote(message: String, onIntroduce: (() -> Unit)? = null) {
    val ruleScale = remember { Animatable(0f) }
    val textAlpha = remember { Animatable(0f) }
    LaunchedEffect(Unit) {
        ruleScale.animateTo(1f, tween(200))
        textAlpha.animateTo(1f, tween(180))
    }

    Row(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(4.dp))
            .background(LeagueColors.Surface2)
            .border(1.dp, LeagueColors.Hairline, RoundedCornerShape(4.dp)),
    ) {
        Box(
            Modifier
                .width(2.dp).fillMaxHeight()
                .graphicsLayer { scaleY = ruleScale.value; transformOrigin = TransformOrigin(0f, 0f) }
                .background(LeagueColors.Gold)
        )
        Column(Modifier.padding(18.dp).graphicsLayer { alpha = textAlpha.value }) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                Icon(Icons.Filled.Star, null, tint = LeagueColors.Gold, modifier = Modifier.size(12.dp))
                Text("YOUR CONCIERGE", style = LeagueText.Eyebrow, color = LeagueColors.Gold)
            }
            Spacer(Modifier.height(8.dp))
            Text(message, style = LeagueText.Concierge, color = LeagueColors.TextPrimary)
            if (onIntroduce != null) {
                Spacer(Modifier.height(12.dp))
                Text(
                    "MAKE THE INTRODUCTION →",
                    style = LeagueText.Button, color = LeagueColors.Gold,
                    modifier = Modifier.clickable { onIntroduce() },
                )
            }
        }
    }
}
```

### Batch Banner

```kotlin
@Composable
fun BatchBanner(count: Int) {
    Row(
        Modifier
            .fillMaxWidth()
            .clip(RoundedCornerShape(4.dp))
            .background(LeagueColors.Surface1)
            .border(1.dp, LeagueColors.Hairline, RoundedCornerShape(4.dp))
            .padding(horizontal = 14.dp, vertical = 10.dp),
        horizontalArrangement = Arrangement.SpaceBetween,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Text("TODAY'S BATCH", style = LeagueText.Eyebrow, color = LeagueColors.TextSecondary)
        Text("$count Prospects", style = LeagueText.Stat, color = LeagueColors.Gold)
    }
}
```

### Buttons

```kotlin
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.OutlinedButton

@Composable
fun LeaguePrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Button(
        onClick = onClick,
        modifier = modifier.fillMaxWidth(),
        shape = RoundedCornerShape(3.dp), // sharp engraved-plate geometry
        colors = ButtonDefaults.buttonColors(
            containerColor = LeagueColors.Gold,
            contentColor = LeagueColors.OnGold,
        ),
        contentPadding = PaddingValues(vertical = 15.dp),
    ) { Text(title.uppercase(), style = LeagueText.Button) }
}

@Composable
fun LeagueOutlineButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    OutlinedButton(
        onClick = onClick,
        modifier = modifier.fillMaxWidth(),
        shape = RoundedCornerShape(3.dp),
        border = BorderStroke(1.dp, LeagueColors.Gold),
    ) { Text(title.uppercase(), style = LeagueText.Button, color = LeagueColors.Gold) }
}
```

## 4. Navigation

The League has minimal chrome: a 4-tab bottom strip and a centered serif wordmark top bar. On Android, model the strip as a `NavigationBar`. There is no Material tint pill — the gold tint alone signals selection, and the top "border" is a 1dp hairline, never a shadow.

```kotlin
@Composable
fun LeagueBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    Column {
        // 1dp hairline instead of a NavigationBar shadow
        Box(Modifier.fillMaxWidth().height(1.dp).background(LeagueColors.Hairline))
        NavigationBar(
            containerColor = LeagueColors.Canvas, // opaque black — no blur
            tonalElevation = 0.dp,
        ) {
            val items = listOf(
                "Prospects" to Icons.Outlined.ViewAgenda,
                "Matches"   to Icons.Outlined.FavoriteBorder,
                "Concierge" to Icons.Outlined.ChatBubbleOutline,
                "Profile"   to Icons.Outlined.PersonOutline,
            )
            items.forEachIndexed { i, (label, icon) ->
                NavigationBarItem(
                    selected = selected == i,
                    onClick = { onSelect(i) },
                    icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(21.dp)) },
                    label = { Text(label.uppercase(), style = LeagueText.Tab) },
                    colors = NavigationBarItemDefaults.colors(
                        selectedIconColor = LeagueColors.Gold,
                        selectedTextColor = LeagueColors.Gold,
                        unselectedIconColor = LeagueColors.TextTertiary,
                        unselectedTextColor = LeagueColors.TextTertiary,
                        indicatorColor = Color.Transparent, // The League has no Material pill
                    ),
                )
            }
        }
    }
}
```

## 5. Motion

The League's motion is restrained — fades and draws, never a thrown swipe or confetti. Celebrations are a drawn wax seal.

| Moment | Compose recipe |
|--------|----------------|
| Card advance | current card `animateFloatAsState` alpha `1f → 0f` + `offset y 0 → -12.dp`, `tween(280, easing = EaseOut)`; next `Crossfade` in |
| Heart send | outline `Icon` swaps to filled + scale `1f → 1.12f → 1f` via `animateFloatAsState` `tween(120)` legs; single soft haptic |
| Concierge arrival | gold rule `graphicsLayer { scaleY }` `0f → 1f` `tween(200)`, then text `alpha 0f → 1f` `tween(180)` |
| Batch count | `AnimatedContent(targetState = count, transitionSpec = { fadeIn() togetherWith fadeOut() })` (restrained — no odometer) |
| Match celebration | wax-seal `Canvas` `drawArc`/`Path` with animated `PathEffect`/trim `0f → 1f` `tween(600)`; NO confetti |
| Tab switch | instant; tint cross-fades via `NavigationBarItemDefaults` (no shared-axis) |
| Field focus | bottom border color `animateColorAsState` `Transparent → Gold` `tween(160)` |
| Page push | Nav3/`NavHost` slide push `tween(300)`; backgrounds stay true-black for seamlessness |

```kotlin
// Concierge rule-draw — the canonical League motion
val ruleScale = remember { Animatable(0f) }
LaunchedEffect(Unit) { ruleScale.animateTo(1f, tween(200, easing = EaseOut)) }
Box(
    Modifier
        .width(2.dp).fillMaxHeight()
        .graphicsLayer { scaleY = ruleScale.value; transformOrigin = TransformOrigin(0f, 0f) }
        .background(LeagueColors.Gold)
)
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the single soft impact on heart-send and introduction-accepted. For the lighter tab tick use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`. Nothing buzzy — restraint extends to touch; never use a long/heavy vibration.

## 6. Icons

The League's iconography is fine-line, never filled (except the gold star seal). The closest first-party set is `androidx.compose.material:material-icons-extended` — prefer the `Outlined` variants.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Prospects (tab) | `rectangle.portrait.on.rectangle.portrait` | `Icons.Outlined.ViewAgenda` |
| Matches (tab) | `heart` | `Icons.Outlined.FavoriteBorder` |
| Concierge (tab) | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Profile (tab) | `person` | `Icons.Outlined.PersonOutline` |
| Heart (card action) | `heart` | `Icons.Outlined.FavoriteBorder` |
| Pass (card action) | `xmark` | `Icons.Filled.Close` |
| Message (card action) | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Verification seal | `star.fill` | `Icons.Filled.Star` (gold) |
| Concierge mark | `star.fill` | `Icons.Filled.Star` (gold, 12dp) |
| Search | `magnifyingglass` | `Icons.Outlined.Search` |
| Settings | `gearshape` | `Icons.Outlined.Settings` |
| Membership / premium | `crown` | `Icons.Outlined.WorkspacePremium` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Credential mark | `circle` | text `◦` (not an icon) |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; nothing here needs a higher API — no blur, no shadow elevation). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Dark-native**: The League has no light mode. Always pass `LeagueDark`; never branch on `isSystemInDarkTheme()`. `enableEdgeToEdge()` with light system-bar content over the near-true-black canvas.
- **No shadows**: never set `elevation`/`shadow` — separation is the 1dp `Hairline` `BorderStroke` plus the tonal surface lift (`#141414` vs `#0A0A0A`). A drop shadow breaks the engraved, black-tie identity.
- **No gradients, no blur**: surfaces are flat; never reach for `Brush` gradients or `Modifier.blur` — depth is line, not light.
- **Font scaling**: `sp` honors the user's font scale — keep it on serif names, screen titles, body, credentials, concierge notes. Pin layout-sensitive text (11sp eyebrows, 13sp button labels, 9sp tab labels, the batch count) by deriving from `dp` or fixing `fontScale = 1f` via a local `Density`.
- **Uppercase**: call `String.uppercase()` for eyebrows / buttons / tabs — never bake casing into the font; TalkBack should still read the natural-case word.
- **Serif/sans split**: the split is the hierarchy — never substitute Jost for a name or Cormorant for a tab label, even under font-loading failure (provide a serif and a sans system fallback respectively).
- **TalkBack**: the prospect card is one focusable node announcing "{name}, {age}, {title}, {school}, {location}, verified member"; card actions get `contentDescription`s ("Pass", "Send heart", "Message"); the concierge note announces "Concierge: {message}"; the batch banner announces "{count} prospects in today's batch". Ensure the gold hairline is never the sole carrier of meaning — pair structural borders with text.
- **Touch targets**: Material guidance is 48.dp — the Heart action is 56.dp (good); Pass / Message are 48.dp (good); pad the 21.dp tab icons to a 48.dp hit; the concierge inline CTA ≥ 48.dp row.
- **Contrast**: `#EDE9E2` on `#0A0A0A` far exceeds WCAG AA; `#C8A35A` on `#0A0A0A` passes AA for gold UI text; the CTA (`#1A1408` on `#C8A35A`) passes AA at 13sp bold — re-check if you tweak the gold.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the card drift with an instant `Crossfade`, show the concierge rule + text immediately, and render the completed wax seal without the draw — keep the success haptic.
- **No dynamic color**: do **not** enable Material You `dynamicColorScheme()` — The League's black-and-single-gold identity must hold regardless of wallpaper; there is no second accent to harmonize, and a tinted scheme would destroy the engraved members-club aesthetic.
