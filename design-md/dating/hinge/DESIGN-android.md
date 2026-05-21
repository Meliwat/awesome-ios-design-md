# Hinge (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Hinge's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the heart-tap, the prompt card, the Rose CTA, and the match celebration.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Hinge's cream-paper canvas, the warm Hinge Black, the single sacred Rose Gold, and the vertical-scroll-and-tap profile architecture) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `SharedTransitionLayout` instead of `matchedGeometryEffect`, a translucent `Surface` instead of iOS blur, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for profile photos. Hinge does no runtime color extraction, so no Palette dependency is needed.

## 1. Color Tokens

```kotlin
// ui/theme/HingeColors.kt
import androidx.compose.ui.graphics.Color

object HingeColors {
    // Canvas & Surfaces (the cream paper)
    val Cream       = Color(0xFFFDF8F2) // canvas
    val Paper       = Color(0xFFFAF6F0) // cards
    val Sand        = Color(0xFFF2EBE0) // chips / inputs
    val Sand2       = Color(0xFFE8DFD0) // pressed
    val DividerBone = Color(0xFFE0D6C5)

    // Text (warm-tinted greys, never neutral)
    val Black        = Color(0xFF1A1A1A) // primary, the "H" mark
    val BlackPressed = Color(0xFF0A0A0A)
    val Graphite     = Color(0xFF4A4239) // secondary
    val Stone        = Color(0xFF7A7268) // tertiary
    val Bone         = Color(0xFFB0A89C) // disabled

    // Rose Gold (the single accent — Standouts, Roses, premium)
    val Rose      = Color(0xFFE8A04D)
    val RoseDeep  = Color(0xFFC57E2E) // pressed
    val RoseLight = Color(0xFFF5D9A8) // halo / Standouts strip

    // Semantic
    val MatchGreen = Color(0xFF2D7A4B)
    val Warning    = Color(0xFFD88B2E)
    val Error      = Color(0xFFB33A2F)
    val Info       = Color(0xFF5A6273)

    // Shadow base (warm-tinted — never rgba(0,0,0))
    val ShadowWarm = Color(0xFF1C140A)

    // Dark mode (warm dark — preserves paper feel)
    val DarkCanvas   = Color(0xFF16130E)
    val DarkSurface  = Color(0xFF1E1A14)
    val DarkSurface2 = Color(0xFF2A2520)
    val DarkDivider  = Color(0xFF2F2A22)
    val DarkText     = Color(0xFFEFE8DA)
    val DarkTextSec  = Color(0xFFA89E8E)
    val RoseDark     = Color(0xFFF0B05C) // OLED-brightened
}
```

Hinge defaults to the warm cream light surface (dark mode added 2022, also warm). Wire a Material 3 `lightColorScheme` so ripples and default component colors inherit the brand; provide the warm `darkColorScheme` for `isSystemInDarkTheme()`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val HingeLight = lightColorScheme(
    primary        = HingeColors.Black,
    onPrimary      = HingeColors.Paper,        // warm paper-white on black, never pure white
    secondary      = HingeColors.Rose,         // the sacred gold
    background     = HingeColors.Cream,
    onBackground   = HingeColors.Black,
    surface        = HingeColors.Paper,
    onSurface      = HingeColors.Black,
    surfaceVariant = HingeColors.Sand,
    outline        = HingeColors.DividerBone,
    error          = HingeColors.Error,
)

private val HingeDark = darkColorScheme(
    primary        = HingeColors.DarkText,
    onPrimary      = HingeColors.DarkCanvas,
    secondary      = HingeColors.RoseDark,
    background     = HingeColors.DarkCanvas,
    onBackground   = HingeColors.DarkText,
    surface        = HingeColors.DarkSurface,
    onSurface      = HingeColors.DarkText,
    surfaceVariant = HingeColors.DarkSurface2,
    outline        = HingeColors.DarkDivider,
)

@Composable
fun HingeTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) HingeDark else HingeLight,
        typography  = HingeTypography,
        content     = content,
    )
```

## 2. Typography

Sailec is the Hinge brand face (display + prompts). Drop the TTFs in `res/font/` (lowercase, snake_case). Use Inter for UI chrome at compact sizes (12-15sp). Fall back to the system font for both — its grotesque-humanist tone is the closest free substitute on Android.

```kotlin
// ui/theme/HingeType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Sailec = FontFamily(
    Font(R.font.sailec_light,   FontWeight.Light),    // 300
    Font(R.font.sailec_regular, FontWeight.Normal),   // 400
    Font(R.font.sailec_medium,  FontWeight.Medium),   // 500
    Font(R.font.sailec_bold,    FontWeight.Bold),     // 700
    Font(R.font.sailec_black,   FontWeight.Black),    // 900
    Font(R.font.sailec_italic,  FontWeight.Medium, FontStyle.Italic), // italic prompts
)

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object HingeText {
    val Display      = TextStyle(Sailec, fontWeight = FontWeight.Bold,     fontSize = 36.sp, lineHeight = 40.sp, letterSpacing = (-0.6).sp)
    val Name         = TextStyle(Sailec, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 32.sp, letterSpacing = (-0.4).sp)
    val PromptQ      = TextStyle(Sailec, fontWeight = FontWeight.Medium,   fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = (-0.2).sp)
    val PromptA      = TextStyle(Sailec, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 30.sp, letterSpacing = (-0.3).sp)
    val Section      = TextStyle(Sailec, fontWeight = FontWeight.Bold,     fontSize = 18.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val Button       = TextStyle(Sailec, fontWeight = FontWeight.Bold,     fontSize = 16.sp, lineHeight = 16.sp)
    val Body         = TextStyle(Inter,  fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp)
    val BodyBold     = TextStyle(Inter,  fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 22.sp)
    val Chip         = TextStyle(Inter,  fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 18.sp)
    val Meta         = TextStyle(Inter,  fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Caption      = TextStyle(Inter,  fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Tab          = TextStyle(Sailec, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.2.sp)
    val CommentInput = TextStyle(Inter,  fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 22.sp)
    val MatchBanner  = TextStyle(Sailec, fontWeight = FontWeight.Bold,     fontSize = 14.sp, lineHeight = 14.sp, letterSpacing = 0.2.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val HingeTypography = Typography(
    displayMedium = HingeText.Display,
    headlineSmall = HingeText.Name,
    titleLarge    = HingeText.PromptA,
    titleMedium   = HingeText.Section,
    bodyMedium    = HingeText.Body,
    labelSmall    = HingeText.Tab,
)
```

## 3. Signature Components

### Heart Tap (under every prompt and photo — the most-pressed button)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.spring
import androidx.compose.animation.core.Spring
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material3.Icon
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun HeartTap(
    isFilled: Boolean,
    onTap: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val haptics = LocalHapticFeedback.current
    val scale = remember { Animatable(1f) }

    LaunchedEffect(isFilled) {
        if (isFilled) {
            // 1.0 → 0.88 → 1.0 over ~250ms, spring damping 0.6
            scale.animateTo(0.88f, spring(dampingRatio = 0.6f, stiffness = Spring.StiffnessHigh))
            scale.animateTo(1f, spring(dampingRatio = 0.6f, stiffness = Spring.StiffnessMedium))
        }
    }

    Box(
        modifier
            .size(44.dp)
            .scale(scale.value)
            .clip(CircleShape)
            .background(if (isFilled) HingeColors.Black else HingeColors.Paper)
            .border(1.dp, HingeColors.Black, CircleShape)
            .clickable(interactionSource = remember { MutableInteractionSource() }, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove) // ≈ iOS light impact
                onTap()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(
            Icons.Filled.Favorite,
            contentDescription = if (isFilled) "Liked" else "Like",
            tint = if (isFilled) HingeColors.Paper else HingeColors.Black,
            modifier = Modifier.size(18.dp).scale(if (isFilled) 1f else 0.92f),
        )
    }
}
```

### Prompt Card (the hero component)

The canonical reactive surface — tapping anywhere (not just the heart) opens the comment sheet.

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.text.font.FontStyle

@Composable
fun PromptCard(
    question: String,
    answer: String,
    isLiked: Boolean,
    onLikeToggle: () -> Unit,
    onCommentTap: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .fillMaxWidth()
            .shadow(8.dp, RoundedCornerShape(16.dp), spotColor = HingeColors.ShadowWarm.copy(alpha = 0.06f))
            .clip(RoundedCornerShape(16.dp))
            .background(HingeColors.Paper)
            .border(0.5.dp, HingeColors.DividerBone, RoundedCornerShape(16.dp))
            .clickable(interactionSource = remember { MutableInteractionSource() }, indication = null) {
                onCommentTap()
            }
            .padding(24.dp),
    ) {
        Text(
            question,
            style = HingeText.PromptQ.copy(fontStyle = FontStyle.Italic), // Hinge italicizes certain templates
            color = HingeColors.Graphite,
        )
        Spacer(Modifier.height(12.dp))
        Text(answer, style = HingeText.PromptA, color = HingeColors.Black)
        Spacer(Modifier.height(16.dp))
        Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.End) {
            HeartTap(isFilled = isLiked, onTap = { onLikeToggle(); onCommentTap() })
        }
    }
}
```

### Photo Card (4:5 portrait)

```kotlin
import androidx.compose.foundation.layout.aspectRatio
import androidx.compose.ui.layout.ContentScale
import coil.compose.AsyncImage

@Composable
fun PhotoCard(
    photoUrl: String,
    isLiked: Boolean,
    onLikeToggle: () -> Unit,
    onCommentTap: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Box(
        modifier
            .fillMaxWidth()
            .aspectRatio(4f / 5f)
            .shadow(8.dp, RoundedCornerShape(16.dp), spotColor = HingeColors.ShadowWarm.copy(alpha = 0.06f))
            .clip(RoundedCornerShape(16.dp))
            .clickable(interactionSource = remember { MutableInteractionSource() }, indication = null, onClick = onCommentTap),
    ) {
        AsyncImage(
            model = photoUrl,
            contentDescription = "Profile photo",
            modifier = Modifier.matchParentSize(),
            contentScale = ContentScale.Crop,
        )
        HeartTap(
            isFilled = isLiked,
            onTap = { onLikeToggle(); onCommentTap() },
            modifier = Modifier.align(Alignment.BottomEnd).padding(16.dp),
        )
    }
}
```

### Attribute Chip Row (Vitals)

```kotlin
import androidx.compose.material.icons.filled.Verified
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun AttributeChip(
    glyph: ImageVector,
    label: String,
    isVerified: Boolean = false,
) {
    Row(
        Modifier
            .clip(RoundedCornerShape(percent = 50))
            .background(HingeColors.Sand)
            .padding(horizontal = 14.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(glyph, contentDescription = null, tint = HingeColors.Black, modifier = Modifier.size(13.dp))
        Text(label, style = HingeText.Chip, color = HingeColors.Black)
        if (isVerified) {
            Icon(Icons.Filled.Verified, contentDescription = "Verified", tint = HingeColors.MatchGreen, modifier = Modifier.size(12.dp))
        }
    }
}
```

### Standouts Hero Card (the only gold-bordered surface)

```kotlin
import androidx.compose.material.icons.filled.Star
import androidx.compose.ui.graphics.Brush

@Composable
fun StandoutsCard(
    photoUrl: String,
    answer: String,
    onSendRose: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .fillMaxWidth()
            .shadow(12.dp, RoundedCornerShape(20.dp), spotColor = HingeColors.ShadowWarm.copy(alpha = 0.10f))
            .clip(RoundedCornerShape(20.dp))
            .background(HingeColors.Paper)
            .border(
                1.dp,
                Brush.linearGradient(listOf(HingeColors.Rose, HingeColors.RoseLight, HingeColors.Rose)),
                RoundedCornerShape(20.dp),
            ),
    ) {
        Row(
            Modifier
                .fillMaxWidth()
                .height(32.dp)
                .background(HingeColors.RoseLight)
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Icon(Icons.Filled.Star, contentDescription = null, tint = HingeColors.Rose, modifier = Modifier.size(14.dp))
            Text("Standout", style = HingeText.Tab.copy(fontSize = 12.sp), color = HingeColors.Black)
        }
        AsyncImage(
            model = photoUrl,
            contentDescription = "Standout photo",
            modifier = Modifier.fillMaxWidth().aspectRatio(1f),
            contentScale = ContentScale.Crop,
        )
        Column(Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(16.dp)) {
            Text(answer, style = HingeText.PromptA, color = HingeColors.Black)
            RoseCTA(label = "Send a Rose", onClick = onSendRose)
        }
    }
}
```

### Rose CTA (premium — heavy haptic, it costs currency)

```kotlin
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.material.icons.filled.LocalFlorist

@Composable
fun RoseCTA(label: String = "Send a Rose", onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val haptics = LocalHapticFeedback.current

    Row(
        modifier
            .fillMaxWidth()
            .height(56.dp)
            .clip(CircleShape) // full capsule (28.dp radius on 56.dp)
            .background(if (pressed) HingeColors.BlackPressed else HingeColors.Black)
            .border(1.dp, HingeColors.Rose, CircleShape)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ iOS heavy impact
                onClick()
            },
        horizontalArrangement = Arrangement.Center,
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(Icons.Filled.LocalFlorist, contentDescription = null, tint = HingeColors.Rose, modifier = Modifier.size(16.dp))
        Spacer(Modifier.width(10.dp))
        Text(label, style = HingeText.Button, color = HingeColors.Paper)
    }
}
```

### Primary CTA (Send Like / Match / Continue)

```kotlin
@Composable
fun HingePrimaryButton(label: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by androidx.compose.animation.core.animateFloatAsState(if (pressed) 0.97f else 1f, label = "ctaScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier
            .fillMaxWidth()
            .scale(scale)
            .height(56.dp)
            .clip(CircleShape)
            .background(if (pressed) HingeColors.BlackPressed else HingeColors.Black)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ iOS medium impact
                onClick()
            },
        contentAlignment = Alignment.Center,
    ) {
        Text(label, style = HingeText.Button, color = HingeColors.Paper)
    }
}
```

### Comment Sheet (tap-to-comment pattern)

The tapped source pins to the top; the input is a capsule with a circular send button that dims when empty.

```kotlin
import androidx.compose.foundation.text.BasicTextField
import androidx.compose.material.icons.filled.ArrowUpward
import androidx.compose.material3.Surface

@Composable
fun CommentSheet(
    pinnedSource: @Composable () -> Unit,
    onSend: (String) -> Unit,
) {
    var comment by remember { mutableStateOf("") }
    val haptics = LocalHapticFeedback.current

    Surface(color = HingeColors.Paper, shape = RoundedCornerShape(topStart = 24.dp, topEnd = 24.dp)) {
        Column(Modifier.fillMaxWidth().padding(top = 24.dp)) {
            Box(Modifier.weight(1f, fill = false).padding(horizontal = 16.dp)) { pinnedSource() }
            Spacer(Modifier.height(16.dp))
            Row(
                Modifier.fillMaxWidth().padding(horizontal = 16.dp).padding(bottom = 24.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(10.dp),
            ) {
                Box(
                    Modifier
                        .weight(1f)
                        .clip(CircleShape)
                        .background(HingeColors.Sand)
                        .padding(horizontal = 20.dp, vertical = 14.dp),
                ) {
                    BasicTextField(
                        value = comment,
                        onValueChange = { comment = it },
                        textStyle = HingeText.CommentInput.copy(color = HingeColors.Black),
                        decorationBox = { inner ->
                            if (comment.isEmpty()) {
                                Text("Add a comment about her response", style = HingeText.CommentInput, color = HingeColors.Stone)
                            }
                            inner()
                        },
                    )
                }
                Box(
                    Modifier
                        .size(44.dp)
                        .clip(CircleShape)
                        .background(if (comment.isBlank()) HingeColors.Bone else HingeColors.Black)
                        .clickable(enabled = comment.isNotBlank()) {
                            haptics.performHapticFeedback(HapticFeedbackType.TextHandleMove)
                            onSend(comment)
                        },
                    contentAlignment = Alignment.Center,
                ) {
                    Icon(Icons.Filled.ArrowUpward, contentDescription = "Send", tint = HingeColors.Paper, modifier = Modifier.size(16.dp))
                }
            }
        }
    }
}
```

### Match Celebration (Rose Gold confetti)

```kotlin
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.ui.geometry.Offset
import kotlin.random.Random

@Composable
fun MatchCelebration(
    myAvatarUrl: String,
    theirAvatarUrl: String,
    theirName: String,
    onMessage: () -> Unit,
    onKeepBrowsing: () -> Unit,
) {
    val fall = remember { Animatable(0f) }
    val haptics = LocalHapticFeedback.current
    val confetti = remember {
        List(24) { Triple(Random.nextFloat(), Random.nextInt(4, 10).dp, Random.nextInt(0, 40) * 0.001f) }
    }

    LaunchedEffect(Unit) {
        haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ iOS success notification
        fall.animateTo(1f, tween(1800)) // Rose Gold particle fall
    }

    Box(Modifier.fillMaxSize().background(HingeColors.Cream)) {
        Canvas(Modifier.matchParentSize()) {
            confetti.forEach { (xFrac, dotSize, _) ->
                drawCircle(
                    color = HingeColors.Rose,
                    radius = dotSize.toPx() / 2f,
                    center = Offset(xFrac * size.width, fall.value * (size.height + 300f) - 150f),
                    alpha = 1f - fall.value,
                )
            }
        }
        Column(
            Modifier.fillMaxSize(),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.Center,
        ) {
            Row(horizontalArrangement = Arrangement.spacedBy(24.dp)) {
                listOf(myAvatarUrl, theirAvatarUrl).forEach {
                    AsyncImage(
                        model = it,
                        contentDescription = null,
                        modifier = Modifier.size(120.dp).clip(CircleShape),
                        contentScale = ContentScale.Crop,
                    )
                }
            }
            Spacer(Modifier.height(32.dp))
            Text("It's a match!", style = HingeText.Display, color = HingeColors.Black)
            Text("You and $theirName liked each other", style = HingeText.Body, color = HingeColors.Graphite)
            Spacer(Modifier.height(32.dp))
            HingePrimaryButton("Send a message", onMessage, Modifier.padding(horizontal = 24.dp))
            Spacer(Modifier.height(12.dp))
            Text(
                "Keep browsing",
                style = HingeText.Chip,
                color = HingeColors.Graphite,
                modifier = Modifier.clickable(onClick = onKeepBrowsing),
            )
        }
    }
}
```

## 4. Heart-Tap on Reactive Surfaces — the distinctive system

Hinge does no color extraction; its most distinctive interaction is the **44pt heart-tap on every reactive surface** (the implementation lives in §3). The architecture is the brand: a vertical-scroll profile where each photo and prompt is its own independently tappable card. Compose this with a `LazyColumn` — never a swipe stack.

```kotlin
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items

sealed interface ProfileBlock {
    data class Header(val name: String, val age: Int) : ProfileBlock
    data class Vitals(val chips: List<Pair<ImageVector, String>>) : ProfileBlock
    data class Photo(val url: String) : ProfileBlock
    data class Prompt(val question: String, val answer: String) : ProfileBlock
}

@Composable
fun ProfileScroll(blocks: List<ProfileBlock>, onComment: (ProfileBlock) -> Unit) {
    LazyColumn(
        Modifier.fillMaxSize().background(HingeColors.Cream),
        contentPadding = PaddingValues(horizontal = 16.dp, vertical = 16.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp), // the 16dp gap defines the profile rhythm
    ) {
        items(blocks) { block ->
            when (block) {
                is ProfileBlock.Header -> Text("${block.name}, ${block.age}", style = HingeText.Name, color = HingeColors.Black)
                is ProfileBlock.Vitals -> FlowRow(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                    block.chips.forEach { (g, l) -> AttributeChip(g, l) }
                }
                is ProfileBlock.Photo -> {
                    var liked by remember { mutableStateOf(false) }
                    PhotoCard(block.url, liked, { liked = !liked }, { onComment(block) })
                }
                is ProfileBlock.Prompt -> {
                    var liked by remember { mutableStateOf(false) }
                    PromptCard(block.question, block.answer, liked, { liked = !liked }, { onComment(block) })
                }
            }
        }
    }
}
```

There is no swipe gesture — advancement is read-and-tap. Do not add a Tinder-style escalation glyph; Hinge has Roses, full stop.

## 5. Navigation (Bottom Tab Bar)

Use Material 3 `NavigationBar`. Hinge's iOS tab bar is a `.regularMaterial` blur over cream; Android has no first-class live blur, so use a 92%-opaque cream surface. **Hinge uses no accent color on the active tab** — it relies on filled icons + warm Hinge Black, with a small indicator dot below the icon (no Material pill).

```kotlin
import androidx.compose.material.icons.filled.Explore
import androidx.compose.material.icons.filled.FavoriteBorder
import androidx.compose.material.icons.filled.ChatBubbleOutline
import androidx.compose.material.icons.filled.PersonOutline
import androidx.compose.material3.*

@Composable
fun HingeBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    val items = listOf(
        "Discover"  to Icons.Filled.Explore,
        "Likes You" to Icons.Filled.FavoriteBorder,
        "Standouts" to Icons.Filled.Star,
        "Matches"   to Icons.Filled.ChatBubbleOutline,
        "Profile"   to Icons.Filled.PersonOutline,
    )
    val haptics = LocalHapticFeedback.current
    NavigationBar(
        containerColor = HingeColors.Cream.copy(alpha = 0.92f),
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
                            Spacer(Modifier.height(4.dp))
                            Box(Modifier.size(4.dp).clip(CircleShape).background(HingeColors.Black))
                        }
                    }
                },
                label = { Text(label, style = HingeText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = HingeColors.Black,
                    selectedTextColor   = HingeColors.Black,
                    unselectedIconColor = HingeColors.Stone,
                    unselectedTextColor = HingeColors.Stone,
                    indicatorColor      = Color.Transparent, // Hinge has no Material pill
                ),
            )
        }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Heart tap (most-repeated) | `Animatable` 1 → 0.88 → 1 with `spring(dampingRatio = 0.6f)` over ~250ms + `TextHandleMove` haptic |
| Card → comment sheet | `SharedTransitionLayout` + `Modifier.sharedElement()` on the tapped card (Compose 1.7+); fallback `AnimatedVisibility` + `slideInVertically` |
| Match confetti | `Animatable.animateTo(1f, tween(1800))` driving falling Rose-Gold circles + `LongPress` (success) haptic |
| Rose send pulse halo | `rememberInfiniteTransition` scale 1.0 → 1.05 → 1.0 over 1500ms while the sheet is open |
| Standouts card transition | gold border alpha animates `0.6f → 1.0f` via `animateFloatAsState` on active state |
| Tab switch | 200ms crossfade outlined→filled + indicator dot fade-in + `TextHandleMove` haptic |

```kotlin
// Rose send pulse halo
@Composable
fun RosePulse(content: @Composable () -> Unit) {
    val t = rememberInfiniteTransition(label = "rosePulse")
    val s by t.animateFloat(
        1f, 1.05f,
        infiniteRepeatable(tween(1500, easing = androidx.compose.animation.core.EaseInOut), RepeatMode.Reverse),
        label = "roseScale",
    )
    Box(Modifier.scale(s)) { content() }
}
```

Haptics: prefer `LocalHapticFeedback`. Hinge's vocabulary maps `TextHandleMove` ≈ iOS `.light`/`.selection` (heart-tap, tab switch), `LongPress` ≈ `.medium`/`.heavy` (Send Like, Rose send). Currency gets weight — for a stronger Rose-send cue, use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.LONG_PRESS)` or a `Vibrator` `VibrationEffect.createOneShot(20, 200)`.

## 7. Icons

The SF Symbols map to Material Icons (`androidx.compose.material:material-icons-extended`). The proprietary "H" mark and the rose glyph are custom — ship as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`.

| Component | SF Symbol (iOS) | Material Icon (Compose) |
|-----------|-----------------|--------------------------|
| Heart-tap (filled) | `heart.fill` | `Icons.Filled.Favorite` |
| X decline | `xmark` | `Icons.Filled.Close` |
| Send arrow (comment) | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Standouts star | `star.fill` | `Icons.Filled.Star` |
| Rose currency | proprietary rose glyph | `Icons.Filled.LocalFlorist` (custom vector preferred) |
| Verified attribute | `checkmark.seal.fill` | `Icons.Filled.Verified` (Match Green) |
| Attribute — height | `ruler` | `Icons.Filled.Straighten` |
| Attribute — job | `briefcase.fill` | `Icons.Filled.Work` |
| Attribute — location | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Attribute — education | `graduationcap.fill` | `Icons.Filled.School` |
| Attribute — religion | `book.closed.fill` | `Icons.Filled.MenuBook` |
| Discover tab | `safari` / `safari.fill` | `Icons.Filled.Explore` |
| Likes You tab | `heart` / `heart.fill` | `Icons.Filled.FavoriteBorder` / `Favorite` |
| Standouts tab | `star` / `star.fill` | `Icons.Outlined.StarBorder` / `Icons.Filled.Star` |
| Matches tab | `bubble.left` / `bubble.left.fill` | `Icons.Filled.ChatBubbleOutline` |
| Profile tab | `person` / `person.fill` | `Icons.Filled.PersonOutline` / `Person` |
| Back | `chevron.left` | `Icons.Filled.ChevronLeft` |
| Settings | `gearshape` | `Icons.Filled.Settings` |

The "H" logomark renders only in Hinge Black `#1A1A1A` on cream, or Paper White on a Hinge Black circle — never gold, never any other color (except the rare Standouts launch-screen variant).

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `SharedTransitionLayout`, `Animatable`, modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. The cream canvas wants dark-content system bars (`isAppearanceLightStatusBars = true`); warm dark mode flips to light-content. Apply `Scaffold` insets so the sticky bottom action bar and tab bar clear the gesture nav.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on prompt question, prompt answer, name, body, comment input. Pin layout-sensitive text (attribute chips, 10.sp tab labels, the "H" mark) by wrapping in `CompositionLocalProvider(LocalDensity provides Density(LocalDensity.current.density, fontScale = 1f))`.
- **TalkBack**: group the prompt card as one element — `Modifier.semantics(mergeDescendants = true) { contentDescription = "Prompt: Two truths and a lie. Answer: I have never seen Star Wars. Double-tap to comment." }`. The heart-tap is a separate node with state-dependent `contentDescription` ("Like" / "Liked"). Announce the remaining Rose count after a send.
- **Touch targets**: Material guidance is 48.dp minimum. The 44.dp heart-tap is below it — give it 8.dp of hit-slop padding (or wrap in a 48.dp clickable `Box`) even though the visual circle stays 44.dp. The 56.dp CTAs and Rose CTA are clear.
- **Contrast**: Hinge Black `#1A1A1A` on cream `#FDF8F2` meets WCAG AAA at all sizes; Hinge Graphite `#4A4239` on cream meets AAA at body sizes and above. Rose Gold `#E8A04D` is decorative/icon only — never small body text on cream without verification.
- **Reduce Motion**: skip the match-celebration confetti when `Settings.Global.ANIMATOR_DURATION_SCALE == 0f` — fall back to a 200ms crossfade, preserving the success haptic. Skip the Rose pulse halo.
- **Material You**: do **not** enable `dynamicColorScheme()` — Hinge's brand requires the fixed cream canvas, warm Hinge Black, and the single sacred Rose Gold regardless of wallpaper. Dark mode is the deliberate warm `#16130E`, not a neutral dark.
