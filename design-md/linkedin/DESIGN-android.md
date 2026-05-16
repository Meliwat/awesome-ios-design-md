# LinkedIn (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports LinkedIn's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the feed card, avatar status rings, the 6-reaction picker, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (LinkedIn's desaturated cream canvas, the single authoritative blue, the six colored reactions, avatar status rings) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a translucent `Surface` instead of iOS blur, `sp`/`dp` instead of `pt`, the reaction-picker stagger via `AnimatedVisibility`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars, post media, and company logos. LinkedIn does no runtime color extraction, so no Palette dependency is needed.

## 1. Color Tokens

```kotlin
// ui/theme/LinkedInColors.kt
import androidx.compose.ui.graphics.Color

object LIColors {
    // Canvas & Surfaces
    val Canvas        = Color(0xFFF3F2EF) // desaturated cream — NOT white
    val CardSurface   = Color(0xFFFFFFFF)
    val Elevated      = Color(0xFFF9F9F9)
    val Divider       = Color(0xFFE0DFDC)
    val DividerSubtle = Color(0xFFEDEDED)

    // Text (alpha-on-black, softer than pure #000)
    val TextPrimary   = Color(0xE6000000) // 90% black
    val TextSecondary = Color(0x99000000) // 60% black
    val TextTertiary  = Color(0x66000000) // 40% black
    val TextOnBlue    = Color(0xFFFFFFFF)

    // Brand
    val Blue        = Color(0xFF0A66C2)
    val BluePressed = Color(0xFF004182)
    val BlueSubtle  = Color(0xFFE7F3FF)
    val DeepNavy    = Color(0xFF00396B)

    // Status / role
    val OpenToWork     = Color(0xFF057642)
    val OpenToWorkDark = Color(0xFF0B5B4C)
    val PremiumGold    = Color(0xFF915907)
    val PremiumGoldHi  = Color(0xFFC37D16)
    val HiringPurple   = Color(0xFF6A3DAA)

    // The six reactions (colored custom icons — never system emoji)
    val ReactLike       = Color(0xFF0A66C2)
    val ReactCelebrate  = Color(0xFFF5BB00)
    val ReactSupport    = Color(0xFFB24020)
    val ReactLove       = Color(0xFFDF704D)
    val ReactInsightful = Color(0xFFE7A33E)
    val ReactFunny      = Color(0xFF00A0DC)

    // Semantic
    val Error   = Color(0xFFCC1016)
    val Success = Color(0xFF0B5B4C)

    // Dark mode (charcoal — NOT true black)
    val DarkCanvas    = Color(0xFF1B1F23)
    val DarkCard      = Color(0xFF1D2226)
    val DarkElevated  = Color(0xFF282E32)
    val DarkDivider   = Color(0xFF38434F)
    val DarkTextPri   = Color(0xE6FFFFFF) // 90% white
    val DarkTextSec   = Color(0x99FFFFFF) // 60% white
    val DarkBlue      = Color(0xFF70B5F9)
}
```

LinkedIn defaults to the cream light surface; dark mode is charcoal (not true black). Wire both Material 3 schemes so ripples and default component colors inherit the brand.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val LILight = lightColorScheme(
    primary        = LIColors.Blue,
    onPrimary      = LIColors.TextOnBlue,
    background     = LIColors.Canvas,        // cream, NOT white — lets white cards float
    onBackground   = LIColors.TextPrimary,
    surface        = LIColors.CardSurface,
    onSurface      = LIColors.TextPrimary,
    surfaceVariant = LIColors.Elevated,
    outline        = LIColors.Divider,
    error          = LIColors.Error,
)

private val LIDark = darkColorScheme(
    primary        = LIColors.DarkBlue,
    onPrimary      = Color.Black,
    background     = LIColors.DarkCanvas,
    onBackground   = LIColors.DarkTextPri,
    surface        = LIColors.DarkCard,
    onSurface      = LIColors.DarkTextPri,
    surfaceVariant = LIColors.DarkElevated,
    outline        = LIColors.DarkDivider,
)

@Composable
fun LinkedInTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) LIDark else LILight,
        typography  = LITypography,
        content     = content,
    )
```

## 2. Typography

LinkedIn uses the system font (SF Pro on iOS → Roboto on Android) — no custom face to bundle. Hierarchy is dense and text-first; no display type, no flourish.

```kotlin
// ui/theme/LinkedInType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

private val Sys = FontFamily.Default // Roboto — LinkedIn dropped its webfont for the native face

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1). Weights 400/600/700 only.
object LIText {
    val ProfileName   = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val ScreenTitle   = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val SectionHeader = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val PostAuthor    = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 18.sp)
    val Headline      = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val PostBody      = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 21.sp) // 1.5
    val Meta          = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 14.sp)
    val ButtonPrimary = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp)
    val ButtonSecond  = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val ActionBar     = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp)
    val Body          = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 20.sp)
    val Tab           = TextStyle(Sys, fontWeight = FontWeight.Medium,   fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.1.sp)
    val Badge         = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.4.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val LITypography = Typography(
    headlineSmall = LIText.ProfileName,
    titleLarge    = LIText.ScreenTitle,
    titleMedium   = LIText.SectionHeader,
    bodyMedium    = LIText.PostBody,
    labelLarge    = LIText.ButtonPrimary,
    labelSmall    = LIText.Tab,
)
```

## 3. Signature Components

### Primary Pill Button (Connect / Follow / Apply)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

enum class LIPillVariant { Filled, Outline }

@Composable
fun LinkedInPillButton(
    title: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    leadingIcon: ImageVector? = null,
    variant: LIPillVariant = LIPillVariant.Filled,
) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "pillScale")
    val haptics = LocalHapticFeedback.current
    val filled = variant == LIPillVariant.Filled

    Row(
        modifier
            .scale(scale)
            .clip(CircleShape)
            .then(
                if (filled) Modifier.background(if (pressed) LIColors.BluePressed else LIColors.Blue)
                else Modifier.background(if (pressed) LIColors.BlueSubtle else Color.Transparent)
                    .border(1.dp, LIColors.Blue, CircleShape)
            )
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // Connect/Apply get a success-y haptic
                onClick()
            }
            .padding(horizontal = 16.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        if (leadingIcon != null) {
            Icon(leadingIcon, contentDescription = null, tint = if (filled) Color.White else LIColors.Blue, modifier = Modifier.size(16.dp))
        }
        Text(
            title,
            style = if (filled) LIText.ButtonPrimary else LIText.ButtonSecond,
            color = if (filled) Color.White else LIColors.Blue,
        )
    }
}
```

### Avatar with Status Rings (Open-to-Work green / Premium gold)

```kotlin
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun AvatarView(
    url: String,
    size: androidx.compose.ui.unit.Dp,
    isPremium: Boolean = false,
    isOpenToWork: Boolean = false,
) {
    Box(Modifier.size(size + 8.dp), contentAlignment = Alignment.Center) {
        // Premium gold gradient frame
        if (isPremium) {
            Box(
                Modifier
                    .size(size + 8.dp)
                    .border(
                        4.dp,
                        Brush.verticalGradient(listOf(LIColors.PremiumGoldHi, LIColors.PremiumGold)),
                        CircleShape,
                    )
            )
        }
        // Open to Work ring — can layer over Premium
        if (isOpenToWork) {
            Box(Modifier.size(size + 8.dp).border(4.dp, LIColors.OpenToWork, CircleShape))
        }
        AsyncImage(
            model = url,
            contentDescription = null,
            modifier = Modifier.size(size).clip(CircleShape),
            contentScale = ContentScale.Crop,
        )
    }
}
```

### Feed Post Card (full-bleed, 0dp radius on phone)

```kotlin
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.MoreHoriz
import androidx.compose.material.icons.filled.Public
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun FeedPostCard(
    authorName: String,
    connectionDegree: String,   // "1st", "2nd", "3rd+"
    headline: String,           // job title + company
    timeAgo: String,            // "3d •"
    body: String,
    avatarUrl: String,
    mediaUrl: String? = null,
    isPremium: Boolean = false,
    isOpenToWork: Boolean = false,
) {
    Column(Modifier.fillMaxWidth().background(LIColors.CardSurface)) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 16.dp).padding(top = 12.dp),
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            AvatarView(avatarUrl, 56.dp, isPremium, isOpenToWork)
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    Text(authorName, style = LIText.PostAuthor, color = LIColors.TextPrimary)
                    Text("• $connectionDegree", style = LIText.Meta, color = LIColors.TextSecondary)
                }
                Text(headline, style = LIText.Headline, color = LIColors.TextSecondary, maxLines = 2, overflow = TextOverflow.Ellipsis)
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                    Text(timeAgo, style = LIText.Meta, color = LIColors.TextSecondary)
                    Icon(Icons.Filled.Public, contentDescription = "Public", tint = LIColors.TextSecondary, modifier = Modifier.size(11.dp))
                }
            }
            Box(Modifier.size(44.dp), contentAlignment = Alignment.Center) {
                Icon(Icons.Filled.MoreHoriz, contentDescription = "More", tint = LIColors.TextSecondary, modifier = Modifier.size(20.dp))
            }
        }

        Text(
            body,
            style = LIText.PostBody,
            color = LIColors.TextPrimary,
            modifier = Modifier.padding(horizontal = 16.dp).padding(top = 12.dp),
        )

        if (mediaUrl != null) {
            AsyncImage(
                model = mediaUrl,
                contentDescription = null,
                modifier = Modifier.fillMaxWidth().height(220.dp).padding(top = 12.dp),
                contentScale = ContentScale.Crop,
            )
        }

        ReactionFooterRow(reactionCount = 127, commentCount = 14, modifier = Modifier.padding(horizontal = 16.dp, vertical = 8.dp))
        Box(Modifier.fillMaxWidth().height(1.dp).background(LIColors.DividerSubtle))
        ActionBar()
    }
}
```

### The 6-Reaction Picker (Long-press) — staggered cascade

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.fadeIn
import androidx.compose.animation.fadeOut
import androidx.compose.animation.scaleIn
import androidx.compose.animation.scaleOut
import androidx.compose.animation.core.spring
import androidx.compose.animation.core.tween
import androidx.compose.material.icons.filled.ThumbUp
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.Lightbulb
import androidx.compose.material.icons.filled.SentimentVerySatisfied
import androidx.compose.material.icons.filled.VolunteerActivism
import androidx.compose.material.icons.filled.Celebration
import androidx.compose.material3.Surface
import kotlinx.coroutines.delay

enum class LIReaction(val icon: ImageVector, val color: Color, val label: String) {
    Like(Icons.Filled.ThumbUp, LIColors.ReactLike, "Like"),
    Celebrate(Icons.Filled.Celebration, LIColors.ReactCelebrate, "Celebrate"),
    Support(Icons.Filled.VolunteerActivism, LIColors.ReactSupport, "Support"),
    Love(Icons.Filled.Favorite, LIColors.ReactLove, "Love"),
    Insightful(Icons.Filled.Lightbulb, LIColors.ReactInsightful, "Insightful"),
    Funny(Icons.Filled.SentimentVerySatisfied, LIColors.ReactFunny, "Funny"),
}

@Composable
fun ReactionPicker(visible: Boolean, onSelect: (LIReaction) -> Unit) {
    val haptics = LocalHapticFeedback.current
    var shown by remember { mutableStateOf(false) }
    LaunchedEffect(visible) { shown = visible }

    Surface(
        shape = CircleShape,
        color = LIColors.CardSurface,
        shadowElevation = 16.dp, // ≈ rgba(0,0,0,0.12) 0 4px 16px
    ) {
        Row(
            Modifier.padding(horizontal = 8.dp, vertical = 4.dp),
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            LIReaction.entries.forEachIndexed { i, reaction ->
                var itemVisible by remember { mutableStateOf(false) }
                LaunchedEffect(shown) {
                    if (shown) { delay(i * 40L); itemVisible = true } else itemVisible = false // 40ms stagger
                }
                AnimatedVisibility(
                    visible = itemVisible,
                    enter = scaleIn(spring(dampingRatio = 0.55f), initialScale = 0f) + fadeIn(),
                    exit = scaleOut(tween(120)) + fadeOut(),
                ) {
                    Box(
                        Modifier
                            .size(40.dp)
                            .clip(CircleShape)
                            .clickable {
                                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // success on release
                                onSelect(reaction)
                            },
                        contentAlignment = Alignment.Center,
                    ) {
                        Icon(reaction.icon, contentDescription = "React with ${reaction.label}", tint = reaction.color, modifier = Modifier.size(24.dp))
                    }
                }
            }
        }
    }
}
```

### Reaction Footer Row (overlapping reaction stack)

```kotlin
@Composable
fun ReactionFooterRow(reactionCount: Int, commentCount: Int, modifier: Modifier = Modifier) {
    Row(modifier.fillMaxWidth(), verticalAlignment = Alignment.CenterVertically) {
        Row(horizontalArrangement = Arrangement.spacedBy((-8).dp)) {
            ReactionBubble(LIColors.ReactLike, Icons.Filled.ThumbUp)
            ReactionBubble(LIColors.ReactCelebrate, Icons.Filled.Celebration)
            ReactionBubble(LIColors.ReactLove, Icons.Filled.Favorite)
        }
        Spacer(Modifier.width(4.dp))
        Text("$reactionCount", style = LIText.Meta, color = LIColors.TextSecondary)
        Spacer(Modifier.weight(1f))
        Text("$commentCount comments", style = LIText.Meta, color = LIColors.TextSecondary)
    }
}

@Composable
private fun ReactionBubble(color: Color, icon: ImageVector) {
    Box(
        Modifier
            .size(20.dp)
            .clip(CircleShape)
            .background(color)
            .border(2.dp, LIColors.CardSurface, CircleShape),
        contentAlignment = Alignment.Center,
    ) {
        Icon(icon, contentDescription = null, tint = Color.White, modifier = Modifier.size(10.dp))
    }
}
```

### Action Bar (Like / Comment / Repost / Send)

```kotlin
import androidx.compose.material.icons.outlined.ThumbUp
import androidx.compose.material.icons.outlined.ChatBubbleOutline
import androidx.compose.material.icons.filled.Repeat
import androidx.compose.material.icons.outlined.Send
import androidx.compose.foundation.combinedClickable

@Composable
fun ActionBar() {
    var isLiked by remember { mutableStateOf(false) }
    var pickerVisible by remember { mutableStateOf(false) }
    val haptics = LocalHapticFeedback.current

    Row(Modifier.fillMaxWidth().height(44.dp)) {
        Box(Modifier.weight(1f).fillMaxHeight()) {
            ActionItem(
                icon = if (isLiked) Icons.Filled.ThumbUp else Icons.Outlined.ThumbUp,
                label = "Like",
                tint = if (isLiked) LIColors.Blue else LIColors.TextSecondary,
                modifier = Modifier.combinedClickable(
                    onClick = {
                        isLiked = !isLiked
                        haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // soft
                    },
                    onLongClick = { pickerVisible = true },
                ),
            )
        }
        ActionItem(Icons.Outlined.ChatBubbleOutline, "Comment", LIColors.TextSecondary, Modifier.weight(1f).fillMaxHeight())
        ActionItem(Icons.Filled.Repeat, "Repost", LIColors.TextSecondary, Modifier.weight(1f).fillMaxHeight())
        ActionItem(Icons.Outlined.Send, "Send", LIColors.TextSecondary, Modifier.weight(1f).fillMaxHeight())
    }
}

@Composable
private fun ActionItem(icon: ImageVector, label: String, tint: Color, modifier: Modifier) {
    Row(modifier, verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.Center) {
        Icon(icon, contentDescription = label, tint = tint, modifier = Modifier.size(20.dp))
        Spacer(Modifier.width(6.dp))
        Text(label, style = LIText.ActionBar, color = tint)
    }
}
```

## 4. The 6-Reaction Picker — the distinctive system

LinkedIn does no color extraction; its most recognizable UI moment is the **long-press 6-reaction picker** (Like / Celebrate / Support / Love / Insightful / Funny). The picker implementation lives in §3; this section documents the interaction model that makes it distinctive:

- **Long-press (400ms)** on the Like button pops the picker — six 40.dp circular icon buttons, 12.dp gap, inside a white capsule with `shadowElevation = 16.dp`.
- **Staggered entrance**: each icon scales `0 → 1.1 → 1.0` (spring) with a **40ms stagger** (`delay(index * 40L)`), so the row cascades open left-to-right.
- **Drag-to-hover** (optional refinement): track the pointer with `Modifier.pointerInput`; the hovered icon scales to 1.3 and translates up 8.dp via `animateFloatAsState`, others dim to 60% alpha.
- **Release over an icon**: success haptic (`LongPress`) + the reaction flies into the post's Like slot. Each reaction stays a **colored custom icon** — never a system emoji.

The footer reaction stack (three overlapping 20.dp icons) and the action-bar Like state both reflect the chosen reaction's color. Never collapse this to a single thumbs-up; the six reactions are the brand.

## 5. Navigation (Bottom Tab Bar)

Use Material 3 `NavigationBar`. LinkedIn's iOS tab bar is opaque white with a 1pt top divider (it gains `.regularMaterial` blur on scroll; Android has no live blur, so use an opaque surface). **Active tint is near-black (`#000000E6`), not blue** — blue is reserved for verbs and links. LinkedIn shows an underline bar below the active icon rather than a Material pill.

```kotlin
import androidx.compose.material.icons.filled.Home
import androidx.compose.material.icons.filled.People
import androidx.compose.material.icons.filled.AddBox
import androidx.compose.material.icons.filled.Notifications
import androidx.compose.material.icons.filled.BusinessCenter
import androidx.compose.material3.*

@Composable
fun LinkedInBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    val items = listOf(
        "Home"          to Icons.Filled.Home,
        "My Network"    to Icons.Filled.People,
        "Post"          to Icons.Filled.AddBox,
        "Notifications" to Icons.Filled.Notifications,
        "Jobs"          to Icons.Filled.BusinessCenter,
    )
    val haptics = LocalHapticFeedback.current
    NavigationBar(
        containerColor = LIColors.CardSurface,
        tonalElevation = 0.dp,
    ) {
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = {
                    haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                    onSelect(i)
                },
                icon = {
                    Column(horizontalAlignment = Alignment.CenterHorizontally) {
                        Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp))
                        if (selected == i) {
                            Spacer(Modifier.height(2.dp))
                            Box(Modifier.size(width = 24.dp, height = 2.dp).background(LIColors.TextPrimary))
                        }
                    }
                },
                label = { Text(label, style = LIText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = LIColors.TextPrimary,  // near-black, NOT blue
                    selectedTextColor   = LIColors.TextPrimary,
                    unselectedIconColor = LIColors.TextSecondary,
                    unselectedTextColor = LIColors.TextSecondary,
                    indicatorColor      = Color.Transparent,     // LinkedIn uses an underline, no pill
                ),
            )
        }
    }
}
```

The top nav (28.dp avatar leading, pill search field center, messaging icon trailing) is a plain `Row` on `LIColors.CardSurface` with a 1.dp `Divider` that appears on scroll — Android has no first-class scroll-edge nav material, so toggle the divider via scroll state.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Button press | `animateFloatAsState` 1 → 0.98 + 120ms ease background shift; haptic only on Connect/Apply/Send |
| Like tap | `Animatable` 1.0 → 1.3 → 1.0 `spring(dampingRatio = 0.6f)` + fill color + `TextHandleMove` (soft) haptic |
| Long-press → reaction picker | 400ms hold → picker `AnimatedVisibility` with 40ms-stagger spring cascade |
| Pull-to-refresh | indeterminate then determinate linear blue progress bar at top of nav |
| Message send | arrow rotates -90° + slides up 200ms + bubble flies into chat `spring` 350ms |
| Connection accepted | confetti emitter (Connections only, not Follows) + success haptic |
| Tab switch | instant; underline bar slides between tabs in 200ms ease-out |

```kotlin
// Like-icon spring pop
@Composable
fun LikeButton(isLiked: Boolean, onToggle: () -> Unit, onLongPress: () -> Unit) {
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    LaunchedEffect(isLiked) {
        if (isLiked) {
            scale.animateTo(1.3f, spring(dampingRatio = 0.6f))
            scale.animateTo(1f, spring(dampingRatio = 0.6f))
        }
    }
    Icon(
        if (isLiked) Icons.Filled.ThumbUp else Icons.Outlined.ThumbUp,
        contentDescription = if (isLiked) "Liked" else "Like",
        tint = if (isLiked) LIColors.Blue else LIColors.TextSecondary,
        modifier = Modifier
            .size(20.dp)
            .scale(scale.value)
            .combinedClickable(
                onClick = { onToggle(); haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) },
                onLongClick = onLongPress,
            ),
    )
}
```

LinkedIn's motion is minimal and functional. Haptics: `LocalHapticFeedback` — `TextHandleMove` ≈ iOS `.soft` (Like, tab switch), `LongPress` ≈ `.success` (Connect, Apply, reaction release). Most plain buttons get **no** haptic — LinkedIn pairs feedback only with Connect/Apply/Send and reactions.

## 7. Icons

The SF Symbols map to Material Icons (`androidx.compose.material:material-icons-extended`). LinkedIn's six reactions are brand-specific colored glyphs — Material approximations are listed; ship custom vector drawables for exact parity.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Home (tab) | `house.fill` | `Icons.Filled.Home` |
| Network (tab) | `person.2.fill` | `Icons.Filled.People` |
| Post (tab) | `plus.app.fill` | `Icons.Filled.AddBox` |
| Notifications (tab) | `bell.fill` | `Icons.Filled.Notifications` |
| Jobs (tab) | `briefcase.fill` | `Icons.Filled.BusinessCenter` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Messaging | `message` / `message.fill` | `Icons.Outlined.ChatBubbleOutline` |
| Ellipsis / More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Like | `hand.thumbsup` / `.fill` | `Icons.Outlined.ThumbUp` / `Icons.Filled.ThumbUp` |
| Celebrate | `hands.clap.fill` | `Icons.Filled.Celebration` (custom clapping vector preferred) |
| Support | `heart.circle.fill` | `Icons.Filled.VolunteerActivism` |
| Love | `heart.fill` | `Icons.Filled.Favorite` |
| Insightful | `lightbulb.fill` | `Icons.Filled.Lightbulb` |
| Funny | `face.smiling.fill` | `Icons.Filled.SentimentVerySatisfied` |
| Comment | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Repost | `arrow.2.squarepath` | `Icons.Filled.Repeat` |
| Send | `paperplane` / `.fill` | `Icons.Outlined.Send` |
| Connect | `person.badge.plus` | `Icons.Filled.PersonAddAlt` |
| Globe (public post) | `globe.americas.fill` | `Icons.Filled.Public` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `combinedClickable` long-press, modern motion, `AnimatedVisibility` stagger are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The cream canvas wants dark-content system bars (`isAppearanceLightStatusBars = true`); charcoal dark mode flips to light-content. Feed cards are full-bleed (0.dp radius) on phone; apply 8.dp radius + 16.dp margin only on large/tablet widths. `Scaffold` insets keep the tab bar clear of the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on post body, comments, profile name. Pin layout-sensitive text (timestamps, connection degree, 11.sp tab labels, badge labels) by wrapping in `CompositionLocalProvider(LocalDensity provides Density(LocalDensity.current.density, fontScale = 1f))`.
- **TalkBack**: each of the six reactions needs a distinct `contentDescription` ("React with Like", "React with Celebrate", …). Group the author row into one element — "Sarah Chen, 1st connection, Principal Designer at Figma, posted 3 days ago" — via `Modifier.semantics(mergeDescendants = true)`. The long-press picker needs a TalkBack-reachable alternative: expose the six reactions as `Modifier.semantics { customActions = … }` on the Like button.
- **Touch targets**: Material guidance is 48.dp minimum. Reaction-picker icons are 40.dp visual — give them 48.dp hit areas via padding. Action-bar items already fill a 44.dp row; the post `MoreHoriz` glyph gets a 44.dp box. Feed avatar (56.dp) is full-row tappable for the profile.
- **Contrast**: `#000000E6` (90% black) on both cream `#F3F2EF` and white `#FFFFFF` meets WCAG AA for 14sp body; `#00000099` (60% black) also passes AA at body sizes. `#0A66C2` blue on white passes AA for 16sp SemiBold.
- **Reduce Motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f`, disable the picker's scale-and-stagger entrance (fade-in only) and skip the like-icon spring pop and connection confetti.
- **Material You**: do **not** enable `dynamicColorScheme()` — LinkedIn's restraint (cream canvas + single authoritative blue) is professional signaling and must hold regardless of wallpaper. Dark mode is the deliberate charcoal `#1B1F23`, not true black; blue shifts to `#70B5F9` for contrast.
