# Quora (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Quora's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Quora's paper-white reading surface, the serif-question / sans-answer split, the single red accent, the vote pill) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `ScrollableTabRow` for the top segments, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars.

## 1. Color Tokens

```kotlin
// ui/theme/QuoraColors.kt
import androidx.compose.ui.graphics.Color

object QuoraColors {
    // Canvas & Surface
    val Canvas  = Color(0xFFFFFFFF)
    val Surface = Color(0xFFF7F7F8)
    val Divider = Color(0xFFE0E0E0)

    // Text
    val TextPrimary   = Color(0xFF282829)
    val TextSecondary = Color(0xFF636466)
    val TextTertiary  = Color(0xFF9A9A9C)

    // Brand
    val Red        = Color(0xFFB92B27)
    val RedPressed = Color(0xFF9E1F1B)
    val RedWash    = Color(0xFFFBEAEA)
    val Upvote     = Color(0xFF2E69FF)
    val UpvoteWash = Color(0xFFEAF0FF)
    val Success    = Color(0xFF1FA463)

    // Dark (system-dark users)
    val DarkCanvas        = Color(0xFF1A1A1B)
    val DarkSurface       = Color(0xFF262627)
    val DarkDivider       = Color(0xFF383839)
    val DarkTextPrimary   = Color(0xFFE8E8E8)
    val DarkTextSecondary = Color(0xFFA0A0A2)
}
```

Wire it into Material 3 schemes so ripples, dividers, and stock component colors inherit the brand. Quora is light-first; provide a dark scheme for system-dark users.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.foundation.isSystemInDarkTheme

private val QuoraLight = lightColorScheme(
    primary        = QuoraColors.Red,
    onPrimary      = Color.White,
    background      = QuoraColors.Canvas,
    onBackground   = QuoraColors.TextPrimary,
    surface         = QuoraColors.Canvas,
    onSurface      = QuoraColors.TextPrimary,
    surfaceVariant = QuoraColors.Surface,
    outline        = QuoraColors.Divider,
    secondary      = QuoraColors.Upvote,
    error          = Color(0xFFD93025),
)

private val QuoraDark = darkColorScheme(
    primary        = QuoraColors.Red,
    onPrimary      = Color.White,
    background      = QuoraColors.DarkCanvas,
    onBackground   = QuoraColors.DarkTextPrimary,
    surface         = QuoraColors.DarkSurface,
    onSurface      = QuoraColors.DarkTextPrimary,
    surfaceVariant = QuoraColors.DarkSurface,
    outline        = QuoraColors.DarkDivider,
    secondary      = QuoraColors.Upvote,
)

@Composable
fun QuoraTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) QuoraDark else QuoraLight,
        typography  = QuoraTypography,
        content     = content,
    )
```

## 2. Typography

The split is the brand: **Georgia for questions** (ship the TTFs in `res/font/` — `georgia.ttf`, `georgia_bold.ttf` — since Georgia is not a system font on Android), **Inter for answers and chrome**.

```kotlin
// ui/theme/QuoraType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

// Georgia — the serif (question) voice
val Georgia = FontFamily(
    Font(R.font.georgia,      FontWeight.Normal),
    Font(R.font.georgia_bold, FontWeight.Bold),
)

// Inter — the sans (answer / UI) voice
val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object QuoraText {
    // Questions — Georgia
    val QuestionDetail  = TextStyle(fontFamily = Georgia, fontWeight = FontWeight.Bold,     fontSize = 26.sp, lineHeight = 34.sp, letterSpacing = (-0.2).sp)
    val QuestionCard    = TextStyle(fontFamily = Georgia, fontWeight = FontWeight.Bold,     fontSize = 19.sp, lineHeight = 26.sp, letterSpacing = (-0.1).sp)
    val QuestionRelated = TextStyle(fontFamily = Georgia, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp)
    val QuestionPrompt  = TextStyle(fontFamily = Georgia, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 23.sp)

    // Answers / UI — Inter
    val AnswerBody  = TextStyle(fontFamily = Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 26.sp)  // 1.6
    val AnswerLead  = TextStyle(fontFamily = Inter, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 27.sp)
    val Credential  = TextStyle(fontFamily = Inter, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 18.sp)
    val AuthorName  = TextStyle(fontFamily = Inter, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 20.sp)
    val Section     = TextStyle(fontFamily = Inter, fontWeight = FontWeight.Bold,     fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.1).sp)
    val Meta        = TextStyle(fontFamily = Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 18.sp)
    val VoteCount   = TextStyle(fontFamily = Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val Button      = TextStyle(fontFamily = Inter, fontWeight = FontWeight.Bold,     fontSize = 15.sp, lineHeight = 15.sp, letterSpacing = 0.1.sp)
    val ButtonSec   = TextStyle(fontFamily = Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val Tab         = TextStyle(fontFamily = Inter, fontWeight = FontWeight.SemiBold, fontSize = 10.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Segment     = TextStyle(fontFamily = Inter, fontWeight = FontWeight.Bold,     fontSize = 14.sp, lineHeight = 14.sp)
    val Caption     = TextStyle(fontFamily = Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val QuoraTypography = Typography(
    headlineMedium = QuoraText.QuestionDetail,
    titleLarge     = QuoraText.QuestionCard,
    bodyLarge      = QuoraText.AnswerBody,
    titleMedium    = QuoraText.AuthorName,
    labelSmall     = QuoraText.Tab,
)
```

## 3. Signature Components

### The Serif/Sans Split — Question Feed Card

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.text.AnnotatedString
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.text.withStyle
import androidx.compose.ui.unit.dp

@Composable
fun QuestionFeedCard(
    authorName: String,
    credential: String,
    question: String,
    answerPreview: String,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .fillMaxWidth()
            .background(QuoraColors.Canvas)
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
            Box(Modifier.size(24.dp).clip(CircleShape).background(QuoraColors.Surface))
            Column {
                Text(authorName, style = QuoraText.AuthorName, color = QuoraColors.TextPrimary)
                Text(credential, style = QuoraText.Credential, color = QuoraColors.TextSecondary)
            }
        }

        // QUESTION — Georgia, always
        Text(question, style = QuoraText.QuestionCard, color = QuoraColors.TextPrimary)

        // ANSWER — Inter, always, 3-line clamp + (more)
        val preview = buildAnnotatedString {
            append(answerPreview)
            withStyle(SpanStyle(color = QuoraColors.Upvote)) { append(" (more)") }
        }
        Text(preview, style = QuoraText.AnswerBody, color = QuoraColors.TextPrimary,
            maxLines = 3, overflow = TextOverflow.Ellipsis)

        Row(horizontalArrangement = Arrangement.spacedBy(24.dp), verticalAlignment = Alignment.CenterVertically) {
            VotePill(count = "1.2K")
            Text("💬 84", style = QuoraText.ButtonSec, color = QuoraColors.TextSecondary)
            Text("Share", style = QuoraText.ButtonSec, color = QuoraColors.TextSecondary)
        }
    }
}
```

### Upvote / Downvote Pill (Signature)

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.material3.Icon
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ArrowDropUp
import androidx.compose.material.icons.filled.ArrowDropDown
import androidx.compose.runtime.*
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

private enum class Vote { None, Up, Down }

@Composable
fun VotePill(count: String, modifier: Modifier = Modifier) {
    var vote by remember { mutableStateOf(Vote.None) }
    val haptics = LocalHapticFeedback.current
    fun tap(next: Vote) {
        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ≈ iOS light impact
        vote = if (vote == next) Vote.None else next
    }
    val upActive = vote == Vote.Up

    Row(
        modifier
            .height(32.dp)
            .clip(CircleShape)
            .border(BorderStroke(1.dp, QuoraColors.Divider), CircleShape),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Row(
            Modifier
                .background(if (upActive) QuoraColors.UpvoteWash else Color.Transparent)
                .clickable(MutableInteractionSource(), indication = null) { tap(Vote.Up) }
                .padding(horizontal = 12.dp).height(32.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Icon(Icons.Filled.ArrowDropUp, contentDescription = "Upvote",
                tint = if (upActive) QuoraColors.Upvote else QuoraColors.TextSecondary)
            Text(count, style = QuoraText.VoteCount,
                color = if (upActive) QuoraColors.Upvote else QuoraColors.TextSecondary)
        }
        Box(Modifier.width(1.dp).height(20.dp).background(QuoraColors.Divider))
        Box(
            Modifier
                .clickable(MutableInteractionSource(), indication = null) { tap(Vote.Down) }
                .padding(horizontal = 12.dp).height(32.dp),
            contentAlignment = Alignment.Center,
        ) {
            // downvote stays neutral — never red
            Icon(Icons.Filled.ArrowDropDown, contentDescription = "Downvote",
                tint = QuoraColors.TextSecondary)
        }
    }
}
```

### Primary "Answer" CTA

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.filled.Edit
import androidx.compose.ui.draw.scale

@Composable
fun QuoraAnswerButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "answerScale")

    Row(
        modifier
            .scale(scale)
            .clip(RoundedCornerShape(6.dp))
            .background(if (pressed) QuoraColors.RedPressed else QuoraColors.Red)
            .clickable(interaction, indication = null, onClick = onClick)
            .padding(horizontal = 24.dp, vertical = 11.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(Icons.Filled.Edit, contentDescription = null, tint = Color.White,
            modifier = Modifier.size(15.dp))
        Text(text, style = QuoraText.Button, color = Color.White)
    }
}
```

### Answer Detail Block (Credential Byline)

```kotlin
import androidx.compose.material3.HorizontalDivider

@Composable
fun AnswerDetailBlock(
    authorName: String,
    credential: String,
    timestamp: String,
    body: String,
) {
    Column(
        Modifier
            .fillMaxWidth()
            .background(QuoraColors.Canvas)
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
            Box(Modifier.size(40.dp).clip(CircleShape).background(QuoraColors.Surface))
            Column(verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(authorName, style = QuoraText.AuthorName, color = QuoraColors.TextPrimary)
                Text(credential, style = QuoraText.Credential, color = QuoraColors.TextSecondary)
                Text(timestamp,  style = QuoraText.Meta, color = QuoraColors.TextSecondary)
            }
        }
        Text(body, style = QuoraText.AnswerBody, color = QuoraColors.TextPrimary)
        Row(horizontalArrangement = Arrangement.spacedBy(24.dp), verticalAlignment = Alignment.CenterVertically) {
            VotePill(count = "3.4K")
            Text("💬 212", style = QuoraText.ButtonSec, color = QuoraColors.TextSecondary)
            Text("Bookmark", style = QuoraText.ButtonSec, color = QuoraColors.TextSecondary)
        }
        HorizontalDivider(color = QuoraColors.Divider)
    }
}
```

## 4. Spaces Carousel (Dynamic Surface)

The only place per-community color enters the otherwise-monochrome feed. Each tile carries its Space banner color, driven by data.

```kotlin
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.items

data class SpaceTile(val name: String, val members: String, val banner: Color)

@Composable
fun SpacesRow(spaces: List<SpaceTile>) {
    Column {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 16.dp, vertical = 12.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Text("Spaces for you", style = QuoraText.Section, color = QuoraColors.TextPrimary)
            Text("See all", style = QuoraText.ButtonSec, color = QuoraColors.Upvote)
        }
        LazyRow(
            contentPadding = PaddingValues(horizontal = 16.dp),
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            items(spaces) { s ->
                Column(
                    Modifier
                        .width(140.dp)
                        .clip(RoundedCornerShape(10.dp))
                        .border(BorderStroke(1.dp, QuoraColors.Divider), RoundedCornerShape(10.dp))
                        .background(QuoraColors.Canvas),
                ) {
                    Box(Modifier.fillMaxWidth().height(64.dp).background(s.banner))
                    Column(Modifier.padding(10.dp), verticalArrangement = Arrangement.spacedBy(4.dp)) {
                        Text(s.name, style = QuoraText.AuthorName, color = QuoraColors.TextPrimary)
                        Text(s.members, style = QuoraText.Credential, color = QuoraColors.TextSecondary)
                    }
                }
            }
        }
    }
}
```

## 5. Navigation

Bottom: Material 3 `NavigationBar` (Home / Following / Answer / Spaces / Notifications) — the center **Answer** glyph is always red. Top: a `ScrollableTabRow` with a red underline indicator. Android has no live blur, so both bars use an opaque white surface with a hairline.

```kotlin
import androidx.compose.material3.*
import androidx.compose.material.icons.filled.*

@Composable
fun QuoraBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = QuoraColors.Canvas, tonalElevation = 0.dp) {
        val items = listOf(
            Triple("Home", Icons.Filled.Home, false),
            Triple("Following", Icons.Filled.People, false),
            Triple("Answer", Icons.Filled.Edit, true),  // red anchor
            Triple("Spaces", Icons.Filled.ViewAgenda, false),
            Triple("Notifications", Icons.Filled.Notifications, false),
        )
        items.forEachIndexed { i, (label, icon, isAnswer) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = {
                    Icon(icon, contentDescription = label,
                        tint = if (isAnswer) QuoraColors.Red
                               else if (selected == i) QuoraColors.TextPrimary
                               else QuoraColors.TextSecondary,
                        modifier = Modifier.size(24.dp))
                },
                label = { Text(label, style = QuoraText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = QuoraColors.TextPrimary,
                    selectedTextColor   = QuoraColors.TextPrimary,
                    unselectedIconColor = QuoraColors.TextSecondary,
                    unselectedTextColor = QuoraColors.TextSecondary,
                    indicatorColor      = Color.Transparent, // Quora has no Material pill
                ),
            )
        }
    }
}

@Composable
fun QuoraTopTabs(selected: Int, onSelect: (Int) -> Unit) {
    val tabs = listOf("For You", "Following", "Answer", "Spaces")
    ScrollableTabRow(
        selectedTabIndex = selected,
        containerColor = QuoraColors.Canvas,
        edgePadding = 16.dp,
        indicator = { pos ->
            TabRowDefaults.SecondaryIndicator(
                Modifier.tabIndicatorOffset(pos[selected]),
                height = 2.dp,
                color = QuoraColors.Red,
            )
        },
        divider = { HorizontalDivider(color = QuoraColors.Divider) },
    ) {
        tabs.forEachIndexed { i, t ->
            Tab(selected = selected == i, onClick = { onSelect(i) }, text = {
                Text(t, style = QuoraText.Segment,
                    color = if (selected == i) QuoraColors.TextPrimary else QuoraColors.TextSecondary)
            })
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Upvote tap | `animateColorAsState` wash + arrow over 200ms `tween`; count via `AnimatedContent`; `HapticFeedbackType.TextHandleMove` |
| Pill press | `animateFloatAsState` 1 → 0.96 → 1, 150ms |
| Card → detail | `AnimatedVisibility` crossfade + `slideInVertically { 8 }`, 280ms |
| Composer present | `ModalBottomSheet` / full-screen `Dialog` with `slideInVertically`, spring (dampingRatio 0.85) |
| Follow toggle | `animateColorAsState` border + `AnimatedContent` label crossfade, 180ms |

```kotlin
// Upvote count roll
import androidx.compose.animation.AnimatedContent
import androidx.compose.animation.core.tween

@Composable
fun VoteCountRoll(count: String, active: Boolean) {
    AnimatedContent(targetState = count, label = "vote",
        transitionSpec = { fadeIn(tween(200)) togetherWith fadeOut(tween(120)) }) { c ->
        Text(c, style = QuoraText.VoteCount,
            color = if (active) QuoraColors.Upvote else QuoraColors.TextSecondary)
    }
}
```

Haptics: prefer `LocalHapticFeedback`. For a crisper tick use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` (API 30+) to approximate iOS's `.light` impact on upvote.

## 7. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`. Export Quora's exact vote chevrons as vector drawables for pixel parity if needed.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Upvote | `arrowtriangle.up` / `.fill` | `Icons.Filled.ArrowDropUp` |
| Downvote | `arrowtriangle.down` / `.fill` | `Icons.Filled.ArrowDropDown` |
| Comment | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Share | `square.and.arrow.up` | `Icons.Filled.Share` |
| Bookmark | `bookmark` / `bookmark.fill` | `Icons.Outlined.BookmarkBorder` / `Icons.Filled.Bookmark` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Answer / Compose | `square.and.pencil` | `Icons.Filled.Edit` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Home (tab) | `house` / `house.fill` | `Icons.Filled.Home` |
| Following (tab) | `person.2` | `Icons.Filled.People` |
| Spaces (tab) | `rectangle.3.group` | `Icons.Filled.ViewAgenda` |
| Notifications (tab) | `bell` / `bell.fill` | `Icons.Filled.Notifications` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the white canvas wants dark system-bar icons (`isAppearanceLightStatusBars = true`). Apply `Scaffold` insets so the bottom nav clears gesture nav.
- **Serif must survive**: Georgia is not a system font on Android — you must bundle it in `res/font/`. Do not let the question fall back to the same family as the answer; the serif/sans split is the entire identity.
- **Font scaling**: `sp` honors the user's font scale — keep it on questions and answer bodies. Pin layout-sensitive text (vote counts, tab labels, segment labels) with a fixed-density wrapper.
- **TalkBack**: announce the credential byline after the author name and before the timestamp — it is the trust signal. Label the vote pill segments separately (`"Upvote, 1,200"`, `"Downvote"`) and never expose a downvote count (Quora hides it). Merge each card's question + answer with `Modifier.semantics(mergeDescendants = true)` but keep the vote pill an independent focus group.
- **Touch targets**: Material guidance is 48.dp minimum. Give each vote-pill segment ≥48.dp effective hit via padding even though the pill is 32.dp tall; the 18.dp footer icons need a 48.dp hit area.
- **Contrast**: `#636466` on `#FFFFFF` passes WCAG AA at 13sp+. `#9A9A9C` is placeholder-only — never essential text. Validate the 10sp tab labels and bump toward `#4A4B4D` if targeting strict compliance.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Quora's identity requires the fixed white canvas and single red accent regardless of wallpaper.
