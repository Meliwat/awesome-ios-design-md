# Substack (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Substack's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the post reader + Subscribe card, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Substack's clean white reading page, serif prose, the one rationed orange Subscribe, inbox-as-home) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ModalBottomSheet` instead of a SwiftUI sheet, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for publication art and avatars. No color extraction — Substack's palette is a fixed neutral + one orange, so Palette is not needed. Substack is light-mode-first (a magazine page); a full dark scheme is provided.

## 1. Color Tokens

```kotlin
// ui/theme/SubstackColors.kt
import androidx.compose.ui.graphics.Color

object SubstackColors {
    // Brand & conversion — the ONE accent
    val Orange        = Color(0xFFFF6719) // Subscribe / active / unread / like — light AND dark
    val OrangePressed = Color(0xFFE5560E)
    val OrangeTint        = Color(0xFFFFF7F2)
    val OrangeTintBorder  = Color(0xFFFFD9C2)
    val OrangeTintDark        = Color(0xFF2A1C12)
    val OrangeTintBorderDark  = Color(0xFF5C3A24)
    val Link          = Color(0xFF4A6FE3) // inline links only

    // Reading surface (light — "Reader")
    val Paper      = Color(0xFFFFFFFF)
    val ReadingInk = Color(0xFF1F1F1F) // near-black, NOT pure black
    val TitleInk   = Color(0xFF1A1A1A)
    val SubheadInk = Color(0xFF57534E)
    val MetaGrey   = Color(0xFF8A8A8A)
    val Hairline   = Color(0xFFECECEC)

    // App surfaces (light)
    val SurfaceSubtle = Color(0xFFF7F6F4)
    val Divider       = Color(0xFFE7E5E1)

    // Dark
    val DarkCanvas   = Color(0xFF121212) // soft charcoal, NOT pure black
    val DarkSurface1 = Color(0xFF1B1B1B)
    val DarkSurface2 = Color(0xFF262626)
    val DarkDivider  = Color(0xFF2E2E2E)

    // Text
    val TextPrimary     = Color(0xFF1A1A1A)
    val TextSecondary   = Color(0xFF6B6B6B)
    val DarkTextPrimary = Color(0xFFEDEDED)
    val DarkTextSecondary = Color(0xFFA6A6A6)

    // Semantic
    val Error   = Color(0xFFD93025)
    val Success = Color(0xFF1A8917)
}
```

Wire it into both schemes. Substack is light-first (a magazine page); the dark scheme uses soft charcoal `#121212`, never true black. The orange is constant.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val SubstackLight = lightColorScheme(
    primary        = SubstackColors.Orange,
    onPrimary      = Color(0xFFFFFFFF),
    background      = SubstackColors.Paper,
    onBackground    = SubstackColors.ReadingInk,
    surface        = SubstackColors.Paper,
    onSurface      = SubstackColors.TextPrimary,
    surfaceVariant = SubstackColors.SurfaceSubtle,
    outline        = SubstackColors.Divider,
    error          = SubstackColors.Error,
)

private val SubstackDark = darkColorScheme(
    primary        = SubstackColors.Orange,        // accent unchanged across modes
    onPrimary      = Color(0xFFFFFFFF),
    background      = SubstackColors.DarkCanvas,
    onBackground    = SubstackColors.DarkTextPrimary,
    surface        = SubstackColors.DarkSurface1,
    onSurface      = SubstackColors.DarkTextPrimary,
    surfaceVariant = SubstackColors.DarkSurface2,
    outline        = SubstackColors.DarkDivider,
    error          = SubstackColors.Error,
)

@Composable
fun SubstackTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) SubstackDark else SubstackLight,
    typography = SubstackTypography,
    content = content,
)
```

## 2. Typography (Material 3)

Substack sets everything-you-read in a reading serif (**Source Serif 4**; `Spectral` is an equal alternative) and everything-you-operate in **Inter**. Drop the TTFs in `res/font/`. Body 18sp at 1.65 line-height for long reads.

```kotlin
// ui/theme/SubstackType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val SourceSerif = FontFamily(
    Font(R.font.sourceserif4_regular,  FontWeight.Normal),
    Font(R.font.sourceserif4_italic,   FontWeight.Normal, FontStyle.Italic),
    Font(R.font.sourceserif4_semibold, FontWeight.SemiBold),
    Font(R.font.sourceserif4_semibolditalic, FontWeight.SemiBold, FontStyle.Italic),
    Font(R.font.sourceserif4_bold,     FontWeight.Bold),
)
val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
    Font(R.font.inter_bold,     FontWeight.Bold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object SubstackText {
    val ScreenTitle = TextStyle(SourceSerif, fontWeight = FontWeight.Bold, fontSize = 30.sp, lineHeight = 36.sp, letterSpacing = (-0.4).sp)
    val PostTitle   = TextStyle(SourceSerif, fontWeight = FontWeight.Bold, fontSize = 26.sp, lineHeight = 32.sp, letterSpacing = (-0.3).sp)
    val SectionHead = TextStyle(SourceSerif, fontWeight = FontWeight.Bold, fontSize = 22.sp, lineHeight = 29.sp)
    val PullQuote   = TextStyle(SourceSerif, fontWeight = FontWeight.SemiBold, fontStyle = FontStyle.Italic, fontSize = 21.sp, lineHeight = 29.sp)
    val Body        = TextStyle(SourceSerif, fontWeight = FontWeight.Normal, fontSize = 18.sp, lineHeight = 30.sp) // ~1.65
    val Dek         = TextStyle(SourceSerif, fontWeight = FontWeight.Normal, fontStyle = FontStyle.Italic, fontSize = 17.sp, lineHeight = 24.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 15.sp)
    val Label       = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 18.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Eyebrow     = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
}

// Map onto Material 3 slots so stock components inherit the brand voice
val SubstackTypography = Typography(
    headlineLarge  = SubstackText.ScreenTitle,
    headlineMedium = SubstackText.PostTitle,
    titleMedium    = SubstackText.SectionHead,
    bodyMedium     = SubstackText.Body,
    labelSmall     = SubstackText.Tab,
)
```

## 3. Signature Components

### Post Reader

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.verticalScroll
import androidx.compose.material3.Divider
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp

sealed interface PostBlock { data class Para(val t: String) : PostBlock; data class Pull(val t: String) : PostBlock }

@Composable
fun PostReader(
    pubInitials: String, pubName: String, subscribers: String,
    title: String, dek: String, byline: String,
    blocks: List<PostBlock>, readingSize: Int = 18, dark: Boolean = false,
) {
    val ink = if (dark) SubstackColors.DarkTextPrimary else SubstackColors.TitleInk
    val bodyInk = if (dark) SubstackColors.DarkTextPrimary else SubstackColors.ReadingInk
    Column(
        Modifier
            .fillMaxSize()
            .background(if (dark) SubstackColors.DarkCanvas else SubstackColors.Paper)
            .verticalScroll(rememberScrollState())
            .padding(horizontal = 24.dp, vertical = 20.dp)
    ) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(10.dp)) {
            Box(
                Modifier.size(34.dp).clip(RoundedCornerShape(6.dp))
                    .background(Brush.linearGradient(listOf(SubstackColors.Orange, SubstackColors.OrangePressed))),
                contentAlignment = Alignment.Center,
            ) { Text(pubInitials, style = SubstackText.Label, color = Color.White) }
            Column {
                Text(pubName, style = SubstackText.Label, color = ink)
                Text(subscribers, style = SubstackText.Caption, color = SubstackColors.MetaGrey)
            }
        }
        Spacer(Modifier.height(18.dp))
        Text(title, style = SubstackText.PostTitle, color = ink)
        Spacer(Modifier.height(8.dp))
        Text(dek, style = SubstackText.Dek, color = SubstackColors.SubheadInk)
        Spacer(Modifier.height(14.dp))
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            Box(Modifier.size(26.dp).clip(RoundedCornerShape(50)).background(SubstackColors.Hairline))
            Text(byline, style = SubstackText.Meta, color = SubstackColors.MetaGrey)
        }
        Spacer(Modifier.height(16.dp))
        Divider(color = if (dark) SubstackColors.DarkDivider else SubstackColors.Hairline)
        Spacer(Modifier.height(18.dp))
        blocks.forEach { b ->
            when (b) {
                is PostBlock.Para -> {
                    Text(
                        b.t,
                        style = SubstackText.Body.copy(
                            fontSize = readingSize.sp,
                            lineHeight = (readingSize * 1.65f).sp,
                            color = bodyInk,
                        ),
                    )
                    Spacer(Modifier.height(18.dp))
                }
                is PostBlock.Pull -> {
                    Row(Modifier.padding(bottom = 14.dp)) {
                        Box(Modifier.width(3.dp).fillMaxHeight().background(SubstackColors.Orange))
                        Text(b.t, style = SubstackText.PullQuote, color = ink,
                             modifier = Modifier.padding(start = 18.dp, top = 6.dp, bottom = 6.dp))
                    }
                }
            }
        }
    }
}
```

### Subscribe Card / Paywall Break

```kotlin
import androidx.compose.animation.core.Spring
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.foundation.border
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.runtime.*
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.text.style.TextAlign

@Composable
fun SubscribeCard(dark: Boolean = false) {
    var subscribed by remember { mutableStateOf(false) }
    var pressed by remember { mutableStateOf(false) }
    val s by animateFloatAsState(if (pressed) 0.97f else 1f,
        spring(dampingRatio = Spring.DampingRatioMediumBouncy), label = "subBtn")
    val haptics = LocalHapticFeedback.current
    Column(
        Modifier
            .clip(RoundedCornerShape(10.dp))
            .background(if (dark) SubstackColors.OrangeTintDark else SubstackColors.OrangeTint)
            .border(1.dp, if (dark) SubstackColors.OrangeTintBorderDark else SubstackColors.OrangeTintBorder,
                    RoundedCornerShape(10.dp))
            .padding(14.dp)
            .fillMaxWidth(),
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Text("Keep reading with a subscription",
            style = SubstackText.PostTitle.copy(fontSize = 15.sp),
            color = if (dark) SubstackColors.DarkTextPrimary else SubstackColors.TitleInk,
            textAlign = TextAlign.Center)
        Text("Get every post, the full archive, and the private community.",
            style = SubstackText.Caption, color = SubstackColors.MetaGrey,
            textAlign = TextAlign.Center,
            modifier = Modifier.padding(top = 4.dp, bottom = 12.dp))
        Button(
            onClick = {
                pressed = true; subscribed = true
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
            },
            shape = RoundedCornerShape(6.dp),
            colors = ButtonDefaults.buttonColors(containerColor = SubstackColors.Orange),
            modifier = Modifier.fillMaxWidth().height(42.dp).scale(s),
        ) { Text(if (subscribed) "Subscribed ✓" else "Subscribe", style = SubstackText.Button, color = Color.White) }
        Text("$8/month · $80/year · cancel anytime",
            style = SubstackText.Caption.copy(fontSize = 11.sp), color = SubstackColors.MetaGrey,
            modifier = Modifier.padding(top = 8.dp))
    }
}
```

### Pull-Quote

```kotlin
@Composable
fun PullQuote(text: String) {
    Row {
        Box(Modifier.width(3.dp).fillMaxHeight().background(SubstackColors.Orange))
        Text(text, style = SubstackText.PullQuote, color = SubstackColors.TitleInk,
             modifier = Modifier.padding(start = 18.dp, top = 6.dp, bottom = 6.dp))
    }
}
```

### Inbox Row

```kotlin
import androidx.compose.foundation.clickable

@Composable
fun InboxRow(pubName: String, title: String, meta: String, unread: Boolean, dark: Boolean = false, onClick: () -> Unit = {}) {
    Row(
        Modifier
            .fillMaxWidth()
            .clickable { onClick() }
            .padding(vertical = 14.dp),
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Box(
            Modifier.size(56.dp).clip(RoundedCornerShape(6.dp))
                .background(Brush.linearGradient(listOf(SubstackColors.Orange, SubstackColors.OrangePressed)))
        )
        Column(Modifier.weight(1f)) {
            Text(pubName.uppercase(), style = SubstackText.Eyebrow, color = SubstackColors.Orange)
            Text(title, style = SubstackText.PostTitle.copy(fontSize = 15.sp, lineHeight = 20.sp),
                 color = if (dark) SubstackColors.DarkTextPrimary else SubstackColors.TextPrimary,
                 maxLines = 2, modifier = Modifier.padding(vertical = 3.dp))
            Text(meta, style = SubstackText.Caption,
                 color = if (dark) SubstackColors.DarkTextSecondary else SubstackColors.TextSecondary)
        }
        if (unread) Box(Modifier.size(8.dp).clip(RoundedCornerShape(50)).background(SubstackColors.Orange))
    }
    Divider(color = if (dark) SubstackColors.DarkDivider else SubstackColors.Divider)
}
```

## 4. Navigation

Substack has a 4-tab bottom strip (Inbox is home) and a serif large title. On Android, model the strip as a `NavigationBar` (no tint pill — active is the one orange) and the post screen with a slim back/share `TopAppBar`.

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.NavigationBar
import androidx.compose.material3.NavigationBarItem
import androidx.compose.material3.NavigationBarItemDefaults

@Composable
fun SubstackBottomBar(selected: Int, onSelect: (Int) -> Unit, dark: Boolean = false) {
    NavigationBar(
        containerColor = if (dark) SubstackColors.DarkCanvas else SubstackColors.Paper,
        tonalElevation = 0.dp,
    ) {
        val items = listOf(
            "Inbox" to Icons.Filled.Email,
            "Discover" to Icons.Filled.Search,
            "Notes" to Icons.AutoMirrored.Filled.Notes,
            "Profile" to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = SubstackText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = SubstackColors.Orange,
                    selectedTextColor = SubstackColors.Orange,
                    unselectedIconColor = if (dark) SubstackColors.DarkTextSecondary else Color(0xFF9A9A9A),
                    unselectedTextColor = if (dark) SubstackColors.DarkTextSecondary else Color(0xFF9A9A9A),
                    indicatorColor = Color.Transparent, // no Material pill — Substack has none
                ),
            )
        }
    }
}
```

The comment composer and share sheet are `ModalBottomSheet`s (16.dp top radius). The comment input is **Inter**, not the serif — comments are conversation, not the article. Show an orange unread-dot badge on the Inbox `NavigationBarItem` when new posts arrive.

## 5. Motion

Substack motion is quiet and editorial — the reading column barely moves.

| Moment | Compose recipe |
|--------|----------------|
| Subscribe success | button `animateFloatAsState` scale 1→0.97→1 `spring(MediumBouncy)`; label `Crossfade` to "Subscribed ✓"; `HapticFeedbackType.LongPress` |
| Like (heart) | icon `animateFloatAsState` 1→1.3→1 `spring(dampingRatio=0.4f)`; soft haptic; count increments |
| Paywall reveal | locked section `Modifier.blur(animateDpAsState(if locked 6.dp else 0.dp))`; `SubscribeCard` `AnimatedVisibility(slideInVertically + fadeIn, tween(250))` |
| Inbox → post | shared-element via `Modifier.sharedElement` (Compose 1.7) or scale `tween(300)` on the thumbnail |
| Read-progress line | `Canvas`/`Box` width = `scrollState.value / maxValue`, 2.dp, `Orange`, top edge |
| Pull to refresh | `PullToRefreshContainer` with an orange indicator |
| Tab switch | instant; selected glyph `Crossfade(tween(120))`; unread dot scales in |

```kotlin
// Like heart pop — the canonical Substack micro-interaction
val pop by animateFloatAsState(
    targetValue = if (liked) 1.3f else 1f,
    animationSpec = spring(dampingRatio = 0.4f, stiffness = Spring.StiffnessMedium),
    label = "likePop",
)
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft confirm on Subscribe success and the Like pop. Restack shows a non-modal "Restacked to Notes" `Snackbar`. The read-progress line is ambient — no haptic.

## 6. Icons

Substack's chrome iconography is conventional; the closest first-party set is `androidx.compose.material:material-icons-extended`. Publication tiles fall back to an orange gradient when no art is set; the unread dot is a colored circle.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Inbox (tab) | `tray` / `tray.full` | `Icons.Filled.Email` |
| Discover (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Notes (tab) | `text.alignleft` | `Icons.AutoMirrored.Filled.Notes` |
| Profile (tab) | `person.crop.circle` | `Icons.Filled.AccountCircle` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Overflow | `ellipsis` | `Icons.Filled.MoreVert` |
| Like | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Icons.Filled.Favorite` |
| Comment | `bubble.left` | `Icons.AutoMirrored.Filled.Comment` |
| Restack | `arrow.2.squarepath` | `Icons.Filled.Repeat` |
| Save / Bookmark | `bookmark` / `bookmark.fill` | `Icons.Filled.BookmarkBorder` / `Icons.Filled.Bookmark` |
| Mute publication | `bell.slash` | `Icons.Filled.NotificationsOff` |
| Unread dot | `circle.fill` (8pt, orange) | colored `Box`/`Canvas` circle |
| Audio (listen) | `headphones` | `Icons.Filled.Headphones` |
| Subscribed check | `checkmark` | `Icons.Filled.Check` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the white reading surface wants dark-content system bars (light-content in dark mode). The read-progress line hugs the top inset; the sticky Subscribe bar sits above the navigation bar inset.
- **Font scaling**: `sp` honors the user's font scale — keep it on the post title, dek, body, section heads (reading content must scale), and apply the in-app reading-size control as an additional multiplier. Pin layout-sensitive chrome (10sp tab labels, eyebrow, captions) by deriving from `dp`.
- **Reading content**: never collapse the 1.65 body line-height or the 24.dp measure at large font scales — long reads must stay comfortable.
- **TalkBack**: the title + dek + byline form a header group; the Subscribe button announces "Subscribe, $8 per month, button"; the like announces state + count; inbox rows announce "{pubName}, {title}, {meta}, unread"; give publication tiles a `contentDescription` of the publication name.
- **Touch targets**: Material guidance is 48.dp. The Subscribe button is ≥ 44.dp (most important target); action-bar items get a 48.dp hit area via padding; inbox rows are full-row tappable.
- **Contrast**: `#1F1F1F` on `#FFFFFF` passes WCAG AA at 18sp; white-on-orange Subscribe passes AA at 15sp bold; pair the unread dot with row text so meaning never rests on color alone.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, drop the like pop and Subscribe spring, make the paywall blur transition instant — keep the state changes; substitute `Crossfade`.
- **Comments are sans**: the comment composer and Notes use **Inter**, not the serif body — they are conversation, not the article. Do not "fix" them to serif.
- **Dark mode**: invert via the `Dark*` palette — soft charcoal `#121212`, NOT true black; `#1F1F1F` body becomes `#EDEDED`. The Subscribe card uses the dark orange-tint wash and relies on its 1.dp border (shadows are invisible on dark). The orange `#FF6719` is constant in every mode — it is the single conversion signal. Do **not** enable Material You `dynamicColorScheme()` — Substack's paper-and-ink editorial identity and the one rationed orange must hold regardless of wallpaper.
