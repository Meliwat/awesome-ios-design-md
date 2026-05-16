# Facebook (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Facebook's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Facebook's soft-gray canvas, white floating cards, single-blue brand, and the 7-emoji Reactions popover) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a translucent `Surface` instead of `.regularMaterial`, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for remote avatars and post media.

## 1. Color Tokens

```kotlin
// ui/theme/FacebookColors.kt
import androidx.compose.ui.graphics.Color

object FacebookColors {
    // Canvas & Surfaces (Light)
    val Canvas       = Color(0xFFF0F2F5)
    val Card         = Color(0xFFFFFFFF)
    val SurfaceTint  = Color(0xFFF7F8FA)
    val Divider      = Color(0xFFE4E6EB)
    val Separator    = Color(0xFFCED0D4)

    // Canvas & Surfaces (Dark)
    val DarkCanvas      = Color(0xFF18191A)
    val DarkCard        = Color(0xFF242526)
    val DarkSurfaceTint = Color(0xFF3A3B3C)
    val DarkDivider     = Color(0xFF3E4042)

    // Text
    val TextPrimaryLight   = Color(0xFF050505)
    val TextPrimaryDark    = Color(0xFFE4E6EB)
    val TextSecondaryLight = Color(0xFF65676B)
    val TextSecondaryDark  = Color(0xFFB0B3B8)
    val TextTertiary       = Color(0xFF8A8D91)

    // Brand
    val Blue        = Color(0xFF1877F2)
    val BluePressed = Color(0xFF0A5FC8)
    val BlueLight   = Color(0xFFE7F3FF)

    // Reactions
    val LikeBlue    = Color(0xFF1877F2) // same as Blue
    val LovePink    = Color(0xFFF3425F)
    val CareYellow  = Color(0xFFF7B928)
    val HahaYellow  = Color(0xFFF7B928)
    val WowYellow   = Color(0xFFF7B928)
    val SadYellow   = Color(0xFFF7B928)
    val AngryOrange = Color(0xFFE9710F)

    // Semantic
    val LiveRed      = Color(0xFFFA3E3E)
    val ErrorRed     = Color(0xFFFA383E)
    val SuccessGreen = Color(0xFF42B72A)
    val PhotoGreen   = Color(0xFF45BD62) // compose-bar photo picker glyph
}
```

Wire it into a Material 3 `lightColorScheme` so ripples, dividers, and default component colors inherit the brand. Facebook is light-first; provide a `darkColorScheme` too since the app ships a true dark mode.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val FacebookLight = lightColorScheme(
    primary        = FacebookColors.Blue,
    onPrimary      = Color.White,
    background     = FacebookColors.Canvas,        // the between-card gray
    onBackground   = FacebookColors.TextPrimaryLight,
    surface        = FacebookColors.Card,          // the white card
    onSurface      = FacebookColors.TextPrimaryLight,
    surfaceVariant = FacebookColors.SurfaceTint,
    outline        = FacebookColors.Divider,
    error          = FacebookColors.ErrorRed,
)

private val FacebookDark = darkColorScheme(
    primary        = FacebookColors.Blue,
    onPrimary      = Color.White,
    background     = FacebookColors.DarkCanvas,
    onBackground   = FacebookColors.TextPrimaryDark,
    surface        = FacebookColors.DarkCard,
    onSurface      = FacebookColors.TextPrimaryDark,
    surfaceVariant = FacebookColors.DarkSurfaceTint,
    outline        = FacebookColors.DarkDivider,
    error          = FacebookColors.ErrorRed,
)

@Composable
fun FacebookTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) FacebookDark else FacebookLight,
    typography  = FacebookTypography,
    content     = content,
)
```

## 2. Typography

Facebook uses **SF Pro** on iOS — the Apple system font. On Android the platform default is **Roboto**, the closest free grotesque. You can ship Roboto Flex from `res/font/` for tighter weight control; the system fallback is automatic if you omit the family.

```kotlin
// ui/theme/FacebookType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val FacebookSans = FontFamily(
    Font(R.font.roboto_regular,  FontWeight.Normal),   // 400
    Font(R.font.roboto_medium,   FontWeight.Medium),   // 500
    Font(R.font.roboto_semibold, FontWeight.SemiBold), // 600
    Font(R.font.roboto_bold,     FontWeight.Bold),     // 700
    Font(R.font.roboto_black,    FontWeight.Black),    // 900 — the "f" logo only
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, line-height = size × ratio)
object FacebookText {
    val FLogo        = TextStyle(FacebookSans, fontWeight = FontWeight.Black,    fontSize = 22.sp, lineHeight = 22.sp)
    val ScreenTitle  = TextStyle(FacebookSans, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val SectionHead  = TextStyle(FacebookSans, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.1).sp)
    val DisplayName  = TextStyle(FacebookSans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val PostBody     = TextStyle(FacebookSans, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 20.sp)
    val PostBodyBig  = TextStyle(FacebookSans, fontWeight = FontWeight.SemiBold, fontSize = 24.sp, lineHeight = 30.sp)
    val CommentBody  = TextStyle(FacebookSans, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 18.sp)
    val CommentName  = TextStyle(FacebookSans, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 17.sp)
    val Timestamp    = TextStyle(FacebookSans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val ReactionCnt  = TextStyle(FacebookSans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val ActionLabel  = TextStyle(FacebookSans, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 15.sp)
    val Cta          = TextStyle(FacebookSans, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 17.sp)
    val TabLabel     = TextStyle(FacebookSans, fontWeight = FontWeight.Medium,   fontSize = 11.sp, lineHeight = 11.sp)
    val Sponsored    = TextStyle(FacebookSans, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val LiveBadge    = TextStyle(FacebookSans, fontWeight = FontWeight.Bold,     fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.5.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val FacebookTypography = Typography(
    headlineMedium = FacebookText.ScreenTitle,
    headlineSmall  = FacebookText.SectionHead,
    titleMedium    = FacebookText.DisplayName,
    bodyMedium     = FacebookText.PostBody,
    labelMedium    = FacebookText.ActionLabel,
    labelSmall     = FacebookText.TabLabel,
)
```

## 3. Signature Components

### Post Card (the feed unit)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

@Composable
fun FBPostCard(
    displayName: String,
    timestamp: String,
    audience: String,          // "Public", "Friends"
    avatarUrl: String,
    body: String,
    mediaUrl: String?,
    modifier: Modifier = Modifier,
) {
    var userReaction by remember { mutableStateOf<FBReaction?>(null) }
    var showReactions by remember { mutableStateOf(false) }

    Column(
        modifier = modifier
            .fillMaxWidth()
            .padding(horizontal = 8.dp, vertical = 4.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(MaterialTheme.colorScheme.surface),
    ) {
        // Header
        Row(
            Modifier.fillMaxWidth().padding(12.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            AsyncImage(
                model = avatarUrl,
                contentDescription = null,
                modifier = Modifier.size(40.dp).clip(CircleShape),
            )
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(displayName, style = FacebookText.DisplayName, color = FacebookColors.TextPrimaryLight)
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    Text(timestamp, style = FacebookText.Timestamp, color = FacebookColors.TextSecondaryLight)
                    Text("·", color = FacebookColors.TextSecondaryLight)
                    Icon(Icons.Filled.Public, null, tint = FacebookColors.TextSecondaryLight, modifier = Modifier.size(12.dp))
                    Text(audience, style = FacebookText.Timestamp, color = FacebookColors.TextSecondaryLight)
                }
            }
            Icon(
                Icons.Filled.MoreHoriz,
                contentDescription = "More options",
                tint = FacebookColors.TextSecondaryLight,
                modifier = Modifier.size(20.dp),
            )
        }

        // Body — full card width, NOT indented after avatar
        Text(
            body,
            style = FacebookText.PostBody,
            color = FacebookColors.TextPrimaryLight,
            modifier = Modifier.padding(horizontal = 12.dp).padding(bottom = 12.dp),
        )

        // Media — edge-to-edge inside card (card already clips corners)
        if (mediaUrl != null) {
            AsyncImage(
                model = mediaUrl,
                contentDescription = null,
                modifier = Modifier.fillMaxWidth(),
                contentScale = ContentScale.FillWidth,
            )
        }

        // Reaction summary
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 12.dp, vertical = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Box {
                Row(horizontalArrangement = Arrangement.spacedBy((-4).dp)) {
                    ReactionDot(FacebookColors.LikeBlue, Icons.Filled.ThumbUp)
                    ReactionDot(FacebookColors.LovePink, Icons.Filled.Favorite)
                }
            }
            Text("You, Sarah, and 45 others", style = FacebookText.ReactionCnt, color = FacebookColors.TextSecondaryLight)
            Spacer(Modifier.weight(1f))
            Text("12 comments · 3 shares", style = FacebookText.ReactionCnt, color = FacebookColors.TextSecondaryLight)
        }

        HorizontalDivider(color = FacebookColors.Divider, thickness = 1.dp)

        // Action row — Like / Comment / Share
        Box {
            Row(Modifier.fillMaxWidth().padding(vertical = 4.dp)) {
                FBActionButton(
                    icon = userReaction?.icon ?: Icons.Outlined.ThumbUp,
                    label = userReaction?.label ?: "Like",
                    color = userReaction?.color ?: FacebookColors.TextSecondaryLight,
                    modifier = Modifier.weight(1f),
                    onTap = { userReaction = if (userReaction == null) FBReaction.Like else null },
                    onLongPress = { showReactions = true },
                )
                FBActionButton(Icons.Outlined.ChatBubbleOutline, "Comment", FacebookColors.TextSecondaryLight, Modifier.weight(1f), {}, {})
                FBActionButton(Icons.Outlined.Share, "Share", FacebookColors.TextSecondaryLight, Modifier.weight(1f), {}, {})
            }
            if (showReactions) {
                ReactionsPopover(
                    onSelect = { userReaction = it; showReactions = false },
                    modifier = Modifier.align(Alignment.TopCenter).offset(y = (-56).dp),
                )
            }
        }
    }
}

@Composable
private fun ReactionDot(color: Color, icon: androidx.compose.ui.graphics.vector.ImageVector) {
    Box(Modifier.size(18.dp).clip(CircleShape).background(color), contentAlignment = Alignment.Center) {
        Icon(icon, null, tint = Color.White, modifier = Modifier.size(9.dp))
    }
}
```

### Action Button (Like / Comment / Share)

```kotlin
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun FBActionButton(
    icon: androidx.compose.ui.graphics.vector.ImageVector,
    label: String,
    color: Color,
    modifier: Modifier = Modifier,
    onTap: () -> Unit,
    onLongPress: () -> Unit,
) {
    val haptics = LocalHapticFeedback.current
    Row(
        modifier = modifier
            .heightIn(min = 40.dp)
            .pointerInput(Unit) {
                detectTapGestures(
                    onTap = {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ iOS .soft impact
                        onTap()
                    },
                    onLongPress = {
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                        onLongPress()
                    },
                )
            },
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp, Alignment.CenterHorizontally),
    ) {
        Icon(icon, contentDescription = null, tint = color, modifier = Modifier.size(20.dp))
        Text(label, style = FacebookText.ActionLabel, color = color)
    }
}
```

### Reactions model + popover

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material.icons.outlined.*

enum class FBReaction(
    val label: String,
    val icon: androidx.compose.ui.graphics.vector.ImageVector,
    val color: Color,
) {
    Like ("Like",  Icons.Filled.ThumbUp,        FacebookColors.LikeBlue),
    Love ("Love",  Icons.Filled.Favorite,       FacebookColors.LovePink),
    Care ("Care",  Icons.Filled.VolunteerActivism, FacebookColors.CareYellow),
    Haha ("Haha",  Icons.Filled.SentimentVerySatisfied, FacebookColors.HahaYellow),
    Wow  ("Wow",   Icons.Filled.SentimentSatisfied,     FacebookColors.WowYellow),
    Sad  ("Sad",   Icons.Filled.SentimentDissatisfied,  FacebookColors.SadYellow),
    Angry("Angry", Icons.Filled.SentimentVeryDissatisfied, FacebookColors.AngryOrange),
}

@Composable
fun ReactionsPopover(
    onSelect: (FBReaction) -> Unit,
    modifier: Modifier = Modifier,
    hoveredIndex: Int? = null,
) {
    Surface(
        modifier = modifier,
        shape = CircleShape,                       // 500pt-radius pill
        color = MaterialTheme.colorScheme.surface,
        shadowElevation = 16.dp,                   // rgba(0,0,0,0.2) 0 4px 16px
    ) {
        Row(
            Modifier.padding(horizontal = 8.dp, vertical = 6.dp),
            horizontalArrangement = Arrangement.spacedBy(4.dp),
        ) {
            FBReaction.entries.forEachIndexed { i, reaction ->
                val scale by animateFloatAsState(
                    if (hoveredIndex == i) 1.3f else 1f,
                    spring(dampingRatio = 0.55f, stiffness = 700f),
                    label = "react$i",
                )
                Box(
                    Modifier
                        .size(48.dp)                // 48dp tap area, 28dp glyph
                        .clickable(
                            interactionSource = remember { MutableInteractionSource() },
                            indication = null,
                        ) { onSelect(reaction) },
                    contentAlignment = Alignment.Center,
                ) {
                    Icon(
                        reaction.icon,
                        contentDescription = "${reaction.label} reaction",
                        tint = reaction.color,
                        modifier = Modifier.size(28.dp).scale(scale),
                    )
                }
            }
        }
    }
}
```

### Top Nav Bar (with blue "f" logo)

Android has no live blur, so the bar is an opaque card surface with a hairline divider — Facebook's iOS top bar is opaque too (no longer the historical blue bar).

```kotlin
@Composable
fun FBTopNav() {
    Surface(color = MaterialTheme.colorScheme.surface) {
        Column {
            Row(
                Modifier.fillMaxWidth().height(56.dp).padding(horizontal = 16.dp),
                verticalAlignment = Alignment.CenterVertically,
            ) {
                // Blue "f" logo in a rounded square
                Box(
                    Modifier.size(36.dp).clip(RoundedCornerShape(8.dp)).background(FacebookColors.Blue),
                    contentAlignment = Alignment.Center,
                ) {
                    Text("f", style = FacebookText.FLogo, color = Color.White, modifier = Modifier.offset(y = (-1).dp))
                }
                Spacer(Modifier.weight(1f))
                Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                    FBCircleIconButton(Icons.Filled.Search, "Search")
                    FBCircleIconButton(Icons.AutoMirrored.Filled.Message, "Messenger")
                }
            }
            HorizontalDivider(color = FacebookColors.Divider, thickness = 1.dp)
        }
    }
}

@Composable
fun FBCircleIconButton(icon: androidx.compose.ui.graphics.vector.ImageVector, label: String) {
    Box(
        Modifier.size(36.dp).clip(CircleShape).background(FacebookColors.Divider)
            .clickable { },
        contentAlignment = Alignment.Center,
    ) {
        Icon(icon, contentDescription = label, tint = FacebookColors.TextPrimaryLight, modifier = Modifier.size(18.dp))
    }
}
```

## 4. Facebook-Specific Feature: Reactions Popover with Drag-Select

The signature Facebook micro-interaction. Long-press the Like button → a floating pill of 7 emoji pops in → the user drags horizontally; the hovered icon scales to 1.3 with a `TextButton`-style tooltip; release selects that reaction. Drive everything from one `pointerInput` so the long-press and the drag share a gesture stream.

```kotlin
import androidx.compose.foundation.gestures.awaitEachGesture
import androidx.compose.foundation.gestures.awaitFirstDown
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.input.pointer.changedToUp
import androidx.compose.ui.layout.onGloballyPositioned
import kotlinx.coroutines.delay

@Composable
fun ReactionsHost(
    onReaction: (FBReaction) -> Unit,
    content: @Composable () -> Unit,
) {
    var show by remember { mutableStateOf(false) }
    var hovered by remember { mutableStateOf<Int?>(null) }
    var rowWidthPx by remember { mutableStateOf(1) }
    val haptics = LocalHapticFeedback.current
    val slot = FBReaction.entries.size

    Box {
        Box(
            Modifier.pointerInput(Unit) {
                awaitEachGesture {
                    val down = awaitFirstDown()
                    // Long-press threshold ≈ 400ms before the popover opens
                    val longPress = try {
                        withTimeout(400) { waitForUpOrCancellation(); false }
                    } catch (_: PointerEventTimeoutCancellationException) { true }
                    if (!longPress) return@awaitEachGesture

                    show = true
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress) // .impact(.light)
                    // Track the finger across the 7 slots
                    do {
                        val event = awaitPointerEvent()
                        val x = event.changes.first().position.x
                        val idx = (x / (rowWidthPx / slot)).toInt().coerceIn(0, slot - 1)
                        if (idx != hovered) {
                            hovered = idx
                            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // .selection
                        }
                    } while (event.changes.none { it.changedToUp() })

                    hovered?.let {
                        onReaction(FBReaction.entries[it])
                        haptics.performHapticFeedback(HapticFeedbackType.LongPress)          // .impact(.medium)
                    }
                    show = false
                    hovered = null
                }
            },
        ) { content() }

        if (show) {
            ReactionsPopover(
                onSelect = { onReaction(it); show = false },
                hoveredIndex = hovered,
                modifier = Modifier
                    .align(Alignment.TopCenter)
                    .offset(y = (-56).dp)
                    .onGloballyPositioned { rowWidthPx = it.size.width },
            )
        }
    }
}
```

Wrap the Like button: `ReactionsHost(onReaction = { vm.setReaction(it) }) { FBActionButton(...) }`. The popover entry uses a `scale 0.5 → 1.0 + fade` (see §6); each emoji springs to 1.3 on hover via the `animateFloatAsState` already in `ReactionsPopover`.

## 5. Bottom Navigation (Tab Bar)

Use Material 3 `NavigationBar`. Facebook shows a label under **every** icon — not icon-only. The iOS bar is an opaque card; Android has no live blur, so use the solid card surface with a top hairline. Active tint is **Facebook Blue**.

```kotlin
@Composable
fun FBBottomBar(selected: Int, onSelect: (Int) -> Unit, notifCount: Int = 0) {
    Column {
        HorizontalDivider(color = FacebookColors.Divider, thickness = 1.dp)
        NavigationBar(
            containerColor = MaterialTheme.colorScheme.surface,
            tonalElevation = 0.dp,
        ) {
            val items = listOf(
                "Home"          to Icons.Filled.Home,
                "Video"         to Icons.Filled.SmartDisplay,
                "Marketplace"   to Icons.Filled.Storefront,
                "Notifications" to Icons.Filled.Notifications,
                "Menu"          to Icons.Filled.Menu,
            )
            items.forEachIndexed { i, (label, icon) ->
                NavigationBarItem(
                    selected = selected == i,
                    onClick = { onSelect(i) },
                    icon = {
                        if (label == "Notifications" && notifCount > 0) {
                            BadgedBox(badge = { Badge(containerColor = FacebookColors.ErrorRed) { Text("$notifCount") } }) {
                                Icon(icon, contentDescription = label, modifier = Modifier.size(26.dp))
                            }
                        } else {
                            Icon(icon, contentDescription = label, modifier = Modifier.size(26.dp))
                        }
                    },
                    label = { Text(label, style = FacebookText.TabLabel) },
                    alwaysShowLabel = true, // Facebook always shows labels
                    colors = NavigationBarItemDefaults.colors(
                        selectedIconColor   = FacebookColors.Blue,
                        selectedTextColor   = FacebookColors.Blue,
                        unselectedIconColor = FacebookColors.TextSecondaryLight,
                        unselectedTextColor = FacebookColors.TextSecondaryLight,
                        indicatorColor      = Color.Transparent, // no Material pill — Facebook has none
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
| Like tap | `Animatable` keyframes 0.9 → 1.15 → 1.0 over 300ms; fill turns `#1877F2`; `HapticFeedbackType.LongPress` (≈ `.impact(.light)`) |
| Reactions open | `AnimatedVisibility` `scaleIn(initialScale = 0.5f) + fadeIn`, 400ms; each emoji springs to 1.3 on hover |
| Reactions drag-select | `HapticFeedbackType.TextHandleMove` on each hover; selected icon bounces 1.5 → 1.0 + `LongPress` haptic |
| Card tap | background darken 3% (`Color.Black.copy(alpha = 0.03f)` overlay) for 150ms on press |
| Stories ring tap | ring `scale 1.0 → 0.9 → 1.0` then open viewer with a crossfade |
| Reaction swap into Like slot | `AnimatedContent` with `fadeIn() togetherWith fadeOut()`, 200ms |

```kotlin
// Like-tap bounce on the thumbs-up
@Composable
fun LikeThumb(liked: Boolean) {
    val scale = remember { Animatable(1f) }
    LaunchedEffect(liked) {
        if (liked) {
            scale.animateTo(1.15f, tween(150))
            scale.animateTo(1f, spring(dampingRatio = 0.5f, stiffness = 500f))
        }
    }
    Icon(
        if (liked) Icons.Filled.ThumbUp else Icons.Outlined.ThumbUp,
        contentDescription = if (liked) "Liked" else "Like",
        tint = if (liked) FacebookColors.Blue else FacebookColors.TextSecondaryLight,
        modifier = Modifier.size(20.dp).scale(scale.value),
    )
}
```

Haptics: prefer `LocalHapticFeedback`. `HapticFeedbackType.LongPress` approximates iOS impact; `HapticFeedbackType.TextHandleMove` approximates `.selection`. For finer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(10, …)`.

## 7. Icons

Facebook's reaction emoji are illustration-style and should ship as **vector drawables** (`ImageVector.vectorResource(R.drawable.…)`) for true parity; the table below maps the rest onto `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Like (outline / fill) | `hand.thumbsup` / `.fill` | `Icons.Outlined.ThumbUp` / `Icons.Filled.ThumbUp` |
| Love | `heart.fill` | `Icons.Filled.Favorite` |
| Care | `hands.sparkles.fill` | `Icons.Filled.VolunteerActivism` |
| Haha / Wow | `face.smiling.fill` / `mouth.fill` | `Icons.Filled.SentimentVerySatisfied` / `SentimentSatisfied` |
| Sad / Angry | `face.dashed.fill` / `flame.fill` | `Icons.Filled.SentimentDissatisfied` / `SentimentVeryDissatisfied` |
| Comment | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Share | `arrowshape.turn.up.right` | `Icons.Outlined.Share` |
| Overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Messenger | `ellipsis.message.fill` | `Icons.AutoMirrored.Filled.Message` |
| Home tab | `house.fill` / `house` | `Icons.Filled.Home` |
| Video tab | `play.rectangle.fill` | `Icons.Filled.SmartDisplay` |
| Marketplace tab | `cart.fill` | `Icons.Filled.Storefront` |
| Notifications tab | `bell.fill` | `Icons.Filled.Notifications` |
| Menu tab | `line.3.horizontal` | `Icons.Filled.Menu` |
| Photo picker | `photo.on.rectangle` | `Icons.Filled.Photo` |
| Live video | `dot.radiowaves.left.and.right` | `Icons.Filled.SensorsOff` (or custom LIVE badge) |
| Audience (Public) | `globe.americas.fill` | `Icons.Filled.Public` |
| Add Friend | `person.badge.plus` | `Icons.Filled.PersonAddAlt1` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion is comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the light canvas wants dark system-bar icons (`WindowCompat` light status bar). Apply `Scaffold` insets so the bottom bar clears the gesture nav and the top nav clears the status bar / camera cutout (the `f` logo stays left of the cutout).
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on post body, comment body, names, headers. Pin layout-sensitive text (the 11sp tab labels, the 13sp reaction count, the 10sp `LIVE` badge) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: make each post card its own container with `Modifier.semantics(mergeDescendants = true)` so the feed reads post-by-post; mark the trailing overflow and the action row as separate buttons. Announce reactions as "Liked by you and 45 others, double-tap to see breakdown".
- **Reactions popover reachability**: long-press is not discoverable for switch/TalkBack users — add a `Modifier.semantics { customActions = listOf(CustomAccessibilityAction("Choose reaction") { … }) }` on the Like button that opens the popover, and label each emoji as a button ("Love reaction").
- **Touch targets**: Material guidance is 48.dp minimum. The reaction icons already use a 48.dp box; ensure the 20.dp action-row glyphs and the 12.dp audience icon sit inside ≥48.dp rows (the action buttons are 40.dp tall — bump to 48.dp for strict compliance).
- **Contrast**: `#050505` on `#FFFFFF` exceeds WCAG AAA. Validate `#65676B` secondary gray on `#FFFFFF` (passes AA at 13sp+) and especially `#8A8D91` "Sponsored" tertiary on white — bump toward `#6B6E73` if your build targets strict AA.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Facebook's brand requires the fixed `#1877F2` blue, the soft-gray canvas, and the seven fixed reaction colors regardless of wallpaper.
