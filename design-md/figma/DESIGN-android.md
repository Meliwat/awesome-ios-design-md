# Figma (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Figma's precise, productivity-tool aesthetic to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s (file card, cube avatar, comment pin), `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Figma's `#1E1E1E` Editor canvas, the five immutable brand cubes, Action Blue CTAs, edge-defined hairline cards) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, a translucent `Surface` instead of `.regularMaterial`, `sp`/`dp` instead of `pt`, `SharedTransitionLayout` instead of `matchedGeometryEffect`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for file thumbnails.

## 1. Color Tokens

```kotlin
// ui/theme/FigmaColors.kt
import androidx.compose.ui.graphics.Color

object FigmaColors {
    // The Five Figma Cubes — brand accents only, NEVER a primary CTA
    val CubeRed    = Color(0xFFF24E1E)
    val CubeOrange = Color(0xFFFF7262)
    val CubePurple = Color(0xFFA259FF)
    val CubeBlue   = Color(0xFF1ABCFE)
    val CubeGreen  = Color(0xFF0ACF83)

    /** The 5-element cube palette for deterministic per-user assignment. */
    val Cubes = listOf(CubeRed, CubeOrange, CubePurple, CubeBlue, CubeGreen)

    // UI Action Colors
    val ActionBlue        = Color(0xFF0D99FF)
    val ActionBluePressed = Color(0xFF0769B5)
    val Blurple           = Color(0xFF5551FF)
    val SelectionBlue     = Color(0xFF5EB6FC)

    // Macaron (softness — illustration / empty-state only)
    val MacaronPink       = Color(0xFFFFC8C8)
    val MacaronPinkBright = Color(0xFFFFA6A6)

    // Light surfaces
    val Canvas        = Color(0xFFFFFFFF)
    val SurfaceLight1 = Color(0xFFF5F5F5)
    val SurfaceLight2 = Color(0xFFE5E5E5)
    val DividerLight  = Color(0xFFE5E5E5)
    val HairlineLight = Color(0xFFEEEEEE)

    // Dark surfaces (the Editor's exact tokens)
    val DarkCanvas   = Color(0xFF1E1E1E)
    val DarkSurface1 = Color(0xFF2C2C2C)
    val DarkSurface2 = Color(0xFF383838)
    val DarkSurface3 = Color(0xFF444444)
    val DarkDivider  = Color(0xFF2C2C2C)

    // Text
    val Ink                = Color(0xFF1E1E1E)
    val TextSecondary      = Color(0xFF757575)
    val TextTertiary       = Color(0xFFB3B3B3)
    val TextDarkPrimary    = Color(0xFFFFFFFF)
    val TextDarkSecondary  = Color(0xFFB3B3B3)

    // Semantic
    val Success = Color(0xFF14AE5C)
    val Warning = Color(0xFFF2AC2A)
    val Error   = Color(0xFFE03E1A)

    /** Deterministic cube from a stable user ID — same user → same cube forever. */
    fun cubeFor(userId: String): Color = Cubes[(userId.hashCode() and Int.MAX_VALUE) % Cubes.size]
}
```

Wire it into a Material 3 scheme. Dark mode is first-class on Figma (most iPhone use is dark), so both schemes are real. `primary` is **Action Blue**, never a cube.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable

private val FigmaLight = lightColorScheme(
    primary        = FigmaColors.ActionBlue,
    onPrimary      = Color.White,
    background     = FigmaColors.Canvas,
    onBackground   = FigmaColors.Ink,
    surface        = FigmaColors.Canvas,
    onSurface      = FigmaColors.Ink,
    surfaceVariant = FigmaColors.SurfaceLight1,
    outline        = FigmaColors.DividerLight,
    error          = FigmaColors.Error,
)

private val FigmaDark = darkColorScheme(
    primary        = FigmaColors.ActionBlue,
    onPrimary      = Color.White,
    background     = FigmaColors.DarkCanvas,     // exact Editor canvas
    onBackground   = FigmaColors.TextDarkPrimary,
    surface        = FigmaColors.DarkSurface1,
    onSurface      = FigmaColors.TextDarkPrimary,
    surfaceVariant = FigmaColors.DarkSurface2,
    outline        = FigmaColors.DarkDivider,
    error          = FigmaColors.Error,
)

@Composable
fun FigmaTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) FigmaDark else FigmaLight,
    typography  = FigmaTypography,
    content     = content,
)
```

## 2. Typography

Inter is the open-source face Rasmus Andersson originally drew for Figma. Drop the TTFs in `res/font/` (lowercase, snake_case). Enable tabular figures via a `FontFeatureSetting`. Hex codes and pixel dimensions use a monospace family (JetBrains Mono / system mono).

```kotlin
// ui/theme/FigmaType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_medium,   FontWeight.Medium),   // 500
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)
val FigmaMono = FontFamily(Font(R.font.jetbrains_mono_medium, FontWeight.Medium))

// Tabular numerals + slashed zero — apply to every numeric / hex value
const val TNUM = "tnum"
const val SLASHED_ZERO = "zero"

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, line-height = size × ratio)
object FigmaText {
    val Hero          = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = (-0.4).sp)
    val PageSection   = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.2).sp)
    val Subhead       = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val FileName      = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 14.sp, lineHeight = 18.sp, letterSpacing = (-0.1).sp)
    val Body          = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 21.sp)
    val Metadata      = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 17.sp)
    val Caption       = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 11.sp, lineHeight = 14.sp)
    val CommentBody   = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 20.sp)
    val CommentAuthor = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 17.sp)
    val PixelDim      = TextStyle(FigmaMono, fontWeight = FontWeight.Medium, fontSize = 12.sp, lineHeight = 16.sp, fontFeatureSettings = "$TNUM, $SLASHED_ZERO")
    val HexCode       = TextStyle(FigmaMono, fontWeight = FontWeight.Medium, fontSize = 12.sp, lineHeight = 16.sp, fontFeatureSettings = "$TNUM, $SLASHED_ZERO")
    val Button        = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val Tab           = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 11.sp, lineHeight = 11.sp)
    val Avatar24      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp)
    val Avatar32      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 16.sp)
    val Avatar40      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 18.sp, lineHeight = 18.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val FigmaTypography = Typography(
    headlineLarge  = FigmaText.Hero,
    titleLarge     = FigmaText.PageSection,
    titleMedium    = FigmaText.Subhead,
    bodyMedium     = FigmaText.Body,
    labelMedium    = FigmaText.Button,
    labelSmall     = FigmaText.Tab,
)
```

Render hex codes with uppercase letters — `"#FF7262"`, never `"#ff7262"` (a Figma convention): `Text("#$hex".uppercase(), style = FigmaText.HexCode)`.

## 3. Signature Components

### Cube Avatar

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp

@Composable
fun CubeAvatar(
    userId: String,
    initial: String,
    size: Dp = 32.dp,
    modifier: Modifier = Modifier,
) {
    val style = when (size) {
        24.dp -> FigmaText.Avatar24
        40.dp -> FigmaText.Avatar40
        else  -> FigmaText.Avatar32
    }
    Box(
        modifier = modifier.size(size).clip(CircleShape).background(FigmaColors.cubeFor(userId)),
        contentAlignment = Alignment.Center,
    ) {
        Text(initial.take(1).uppercase(), style = style, color = Color.White)
    }
}
```

### Avatar Stack (collaborators)

```kotlin
@Composable
fun AvatarStack(
    collaborators: List<Pair<String, String>>, // userId to initial
    maxVisible: Int = 3,
    size: Dp = 24.dp,
    borderColor: Color = FigmaColors.Canvas,
) {
    Row(horizontalArrangement = Arrangement.spacedBy((-8).dp)) {
        collaborators.take(maxVisible).forEach { (id, initial) ->
            CubeAvatar(
                userId = id,
                initial = initial,
                size = size,
                modifier = Modifier.border(2.dp, borderColor, CircleShape),
            )
        }
        if (collaborators.size > maxVisible) {
            Box(
                Modifier.size(size).clip(CircleShape)
                    .background(FigmaColors.SurfaceLight1)
                    .border(2.dp, borderColor, CircleShape),
                contentAlignment = Alignment.Center,
            ) {
                Text("+${collaborators.size - maxVisible}", style = FigmaText.Caption, color = FigmaColors.Ink)
            }
        }
    }
}
```

### File Card (16:10 thumbnail + name + metadata)

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.layout.aspectRatio
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import coil.compose.AsyncImage

@Composable
fun FileCard(
    id: String,
    name: String,
    metadata: String,            // "Edited 2h ago · 4 collaborators"
    thumbnailUrl: String?,
    collaborators: List<Pair<String, String>>,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val haptics = LocalHapticFeedback.current
    Column(
        modifier = modifier
            .fillMaxWidth()
            .clickable(
                interactionSource = remember { MutableInteractionSource() },
                indication = null,
            ) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress) // .impact(.light)
                onClick()
            },
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        // 16:10 thumbnail with a 1dp hairline (edge-defined, not elevated)
        Box(
            Modifier
                .fillMaxWidth()
                .aspectRatio(16f / 10f)
                .clip(RoundedCornerShape(6.dp))
                .border(1.dp, FigmaColors.DividerLight, RoundedCornerShape(6.dp)),
        ) {
            if (thumbnailUrl != null) {
                AsyncImage(
                    model = thumbnailUrl,
                    contentDescription = null,
                    modifier = Modifier.fillMaxSize(),
                    contentScale = ContentScale.Crop,
                )
            } else {
                // No-preview cover: this file's cube color + first letter
                Box(
                    Modifier.fillMaxSize().background(FigmaColors.cubeFor(id)),
                    contentAlignment = Alignment.Center,
                ) {
                    Text(
                        name.take(1).uppercase(),
                        style = FigmaText.Hero.copy(fontSize = 32.sp),
                        color = Color.White,
                    )
                }
            }
        }
        Column(verticalArrangement = Arrangement.spacedBy(2.dp)) {
            Text(name, style = FigmaText.FileName, color = FigmaColors.Ink, maxLines = 1, overflow = TextOverflow.Ellipsis)
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                Text(metadata, style = FigmaText.Metadata, color = FigmaColors.TextSecondary, maxLines = 1, overflow = TextOverflow.Ellipsis, modifier = Modifier.weight(1f))
                if (collaborators.isNotEmpty()) AvatarStack(collaborators, maxVisible = 3, size = 20.dp)
            }
        }
    }
}
```

### Primary CTA

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.interaction.collectIsPressedAsState
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun FigmaPrimaryButton(text: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.98f else 1f, label = "ctaScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .scale(scale)
            .heightIn(min = 36.dp)
            .clip(RoundedCornerShape(6.dp)) // Figma's exact 6dp button radius
            .background(if (pressed) FigmaColors.ActionBluePressed else FigmaColors.ActionBlue)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onClick()
            }
            .padding(horizontal = 16.dp, vertical = 10.dp),
        contentAlignment = Alignment.Center,
    ) {
        Text(text, style = FigmaText.Button, color = Color.White)
    }
}
```

### Comment Pin + Thread Sheet

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.spring

@Composable
fun CommentPin(userId: String, number: Int, dark: Boolean, onClick: () -> Unit) {
    val scale = remember { Animatable(1f) }
    Box(
        Modifier
            .size(28.dp)
            .scale(scale.value)
            .clip(CircleShape)
            .background(FigmaColors.cubeFor(userId))
            .border(2.dp, if (dark) FigmaColors.DarkCanvas else FigmaColors.Canvas, CircleShape)
            .clickable(remember { MutableInteractionSource() }, indication = null, onClick = onClick),
        contentAlignment = Alignment.Center,
    ) {
        Text("$number", style = FigmaText.CommentAuthor.copy(fontWeight = FontWeight.Bold), color = Color.White)
    }
    LaunchedEffect(number) {
        scale.animateTo(1.15f, spring(dampingRatio = 0.6f, stiffness = 500f))
        scale.animateTo(1f, spring(dampingRatio = 0.6f, stiffness = 500f))
    }
}

@Composable
fun CommentRow(userId: String, initial: String, author: String, body: String, timestamp: String, indented: Boolean = false) {
    Row(
        Modifier.padding(start = if (indented) 12.dp else 0.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        CubeAvatar(userId, initial, 32.dp)
        Column(verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                Text(author, style = FigmaText.CommentAuthor, color = FigmaColors.Ink)
                Text(timestamp, style = FigmaText.Metadata, color = FigmaColors.TextSecondary)
            }
            Text(body, style = FigmaText.CommentBody, color = FigmaColors.Ink)
        }
    }
}
```

### Comment Composer

```kotlin
@Composable
fun CommentComposer(value: String, onValueChange: (String) -> Unit, onSend: () -> Unit) {
    Row(
        Modifier
            .fillMaxWidth()
            .heightIn(min = 36.dp, max = 120.dp)
            .clip(RoundedCornerShape(18.dp))      // half-height pill
            .background(FigmaColors.SurfaceLight1)
            .padding(horizontal = 12.dp, vertical = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        Icon(Icons.Filled.AlternateEmail, "Mention", tint = FigmaColors.TextSecondary, modifier = Modifier.size(14.dp))
        BasicTextField(
            value = value,
            onValueChange = onValueChange,
            textStyle = FigmaText.CommentBody.copy(color = FigmaColors.Ink),
            modifier = Modifier.weight(1f),
            maxLines = 4,
        )
        if (value.isNotEmpty()) {
            Box(
                Modifier.size(28.dp).clip(CircleShape).background(FigmaColors.ActionBlue)
                    .clickable(onClick = onSend),
                contentAlignment = Alignment.Center,
            ) {
                Icon(Icons.AutoMirrored.Filled.Send, "Send", tint = Color.White, modifier = Modifier.size(14.dp))
            }
        }
    }
}
```

## 4. Figma-Specific Feature: Brand-Cube Avatars & Trailing Cursors

The cube **is** the user. Color is deterministically derived from a stable user ID and persisted forever — never random per session. In the mirror view, each collaborator gets a cube-colored cursor with a name tag, and the cursor *trails* its target position with an ~80ms ease-out lag (Figma's signature "alive" feel from desktop, preserved on mobile). Animate cursor position with a low-stiffness spring so it visibly chases the live coordinate.

```kotlin
import androidx.compose.animation.core.animateOffsetAsState
import androidx.compose.foundation.layout.offset
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.NearMe
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.unit.IntOffset
import kotlin.math.roundToInt

data class LiveCursor(val userId: String, val name: String, val target: Offset)

@Composable
fun CollaboratorCursor(cursor: LiveCursor) {
    // The trailing lag: a soft spring chases the live coordinate (~80ms feel)
    val pos by animateOffsetAsState(
        targetValue = cursor.target,
        animationSpec = spring(dampingRatio = 0.9f, stiffness = 350f),
        label = "cursorTrail",
    )
    val color = FigmaColors.cubeFor(cursor.userId)

    Box(Modifier.offset { IntOffset(pos.x.roundToInt(), pos.y.roundToInt()) }) {
        Icon(Icons.Filled.NearMe, contentDescription = null, tint = color, modifier = Modifier.size(12.dp))
        Box(
            Modifier
                .offset(x = 10.dp, y = 10.dp)
                .clip(RoundedCornerShape(4.dp))      // Figma's 4dp name-tag radius
                .background(color)
                .padding(horizontal = 4.dp, vertical = 2.dp),
        ) {
            Text(cursor.name, style = FigmaText.Caption, color = Color.White)
        }
    }
}
```

Render all live cursors over the mirrored frame: `liveCursors.forEach { CollaboratorCursor(it) }`. For the file-card → detail morph (the thumbnail that grows into the detail header) use `SharedTransitionLayout` + `Modifier.sharedElement(rememberSharedContentState(file.id), …)` (Compose 1.7+) instead of `matchedGeometryEffect`; fallback to `AnimatedContent` with a 350ms `tween`.

## 5. Navigation

Use Material 3 `NavigationBar` — 4 tabs: Recents, Drafts, Notifications, Profile. Figma's tab is **not** a brand color; the active state is a weight/fill + tint swap to Ink (light) / white (dark). Android has no live blur, so the bar is an opaque canvas surface with a 1dp top hairline. The trailing nav avatar uses the *user's own* cube.

```kotlin
@Composable
fun FigmaBottomBar(selected: Int, onSelect: (Int) -> Unit, dark: Boolean) {
    val activeTint = if (dark) FigmaColors.TextDarkPrimary else FigmaColors.Ink
    Column {
        HorizontalDivider(color = if (dark) FigmaColors.DarkDivider else FigmaColors.DividerLight, thickness = 1.dp)
        NavigationBar(
            containerColor = if (dark) FigmaColors.DarkCanvas else FigmaColors.Canvas,
            tonalElevation = 0.dp,
        ) {
            val items = listOf(
                "Recents"       to Icons.Filled.Schedule,
                "Drafts"        to Icons.Filled.Description,
                "Notifications" to Icons.Filled.Notifications,
                "Profile"       to Icons.Filled.Person,
            )
            items.forEachIndexed { i, (label, icon) ->
                NavigationBarItem(
                    selected = selected == i,
                    onClick = { onSelect(i) },
                    icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                    label = { Text(label, style = FigmaText.Tab) },
                    colors = NavigationBarItemDefaults.colors(
                        selectedIconColor   = activeTint,   // weight/fill swap, not a brand color
                        selectedTextColor   = activeTint,
                        unselectedIconColor = FigmaColors.TextSecondary,
                        unselectedTextColor = FigmaColors.TextSecondary,
                        indicatorColor      = Color.Transparent, // no Material pill — Figma has none
                    ),
                )
            }
        }
    }
}
```

The **Editor bottom bar** (when viewing a file) is a floating capsule that stays dark even in light mode (a tool surface): a 56.dp `Surface(color = FigmaColors.DarkCanvas, shape = RoundedCornerShape(28.dp), shadowElevation = 12.dp)` holding Comment / Mirror / Share icon buttons.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| File card tap | `scale 1.0 → 0.98` over 100ms; opens detail via `SharedTransitionLayout` (thumbnail → header), 350ms |
| Comment pin tap | `Animatable` 1.0 → 1.15 → 1.0, `spring(stiffness = 500f, dampingRatio = 0.6f)`; then sheet rises 400ms |
| Cursor trailing | `animateOffsetAsState` with `spring(dampingRatio = 0.9f, stiffness = 350f)` — the ~80ms lag |
| Avatar stack join | new avatar `slideInHorizontally { it }` + `spring`, 250ms |
| Reply send | composer text `fadeOut`, send button `scale 1.0 → 0.9 → 1.0`, `HapticFeedbackType.LongPress` (≈ success) |
| Tab switch | `HapticFeedbackType.TextHandleMove`; icon weight/fill swap, no animation |

```kotlin
// Reply send: button bounce + success haptic
@Composable
fun SendButton(enabled: Boolean, onSend: () -> Unit) {
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()
    Box(
        Modifier.size(28.dp).scale(scale.value).clip(CircleShape).background(FigmaColors.ActionBlue)
            .clickable(enabled = enabled) {
                scope.launch {
                    scale.animateTo(0.9f, tween(80)); scale.animateTo(1f, spring(dampingRatio = 0.5f))
                }
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onSend()
            },
        contentAlignment = Alignment.Center,
    ) {
        Icon(Icons.AutoMirrored.Filled.Send, "Send", tint = Color.White, modifier = Modifier.size(14.dp))
    }
}
```

Haptics: prefer `LocalHapticFeedback`. `HapticFeedbackType.TextHandleMove` ≈ iOS `.selection` for tab switches; `LongPress` ≈ `.impact(.light)`/`.success`. For finer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK)` (API 30+).

## 7. Icons

Figma ships custom glyphs; the closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Figma's glyphs as vector drawables and load via `ImageVector.vectorResource(R.drawable.…)`. The Figma "F" lockup (the five colored shapes) is an `R.drawable` shown only on the About screen — never as an in-app logomark.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Recents tab | `clock` / `clock.fill` | `Icons.Filled.Schedule` |
| Drafts tab | `doc` / `doc.fill` | `Icons.Filled.Description` |
| Notifications tab | `bell` / `bell.fill` | `Icons.Filled.Notifications` |
| Profile tab | `person` / `person.fill` | `Icons.Filled.Person` |
| Comment | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Mirror | `iphone` | `Icons.Filled.PhoneIphone` |
| Share | `square.and.arrow.up` | `Icons.Filled.IosShare` |
| Mention | `at` | `Icons.Filled.AlternateEmail` |
| Send | `paperplane.fill` | `Icons.AutoMirrored.Filled.Send` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Row chevron | `chevron.right` | `Icons.AutoMirrored.Filled.KeyboardArrowRight` |
| Plus | `plus` | `Icons.Filled.Add` |
| Filter | `line.3.horizontal.decrease` | `Icons.Filled.FilterList` |
| Cursor | (custom arrow) | `Icons.Filled.NearMe` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `SharedTransitionLayout` and modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`. Figma is heavily used in dark mode — set system-bar icon contrast per `colorScheme` (light icons on `#1E1E1E`). Apply `Scaffold` insets so the bottom bar clears the gesture nav and the large nav title starts at status-bar inset + 16.dp.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on comment bodies, file names, metadata, the large nav title. Pin layout-sensitive text (11sp tab labels, 12sp hex/dimension chips, avatar initials) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` so the 2-col grid and avatar circles never break.
- **TalkBack**: read a file card as `"Onboarding flows, edited 2 hours ago, 4 collaborators, opens file"` via `Modifier.semantics(mergeDescendants = true) { contentDescription = … }`; a comment pin as `"Comment 1 by Sarah Chen, double-tap to open thread"`; the avatar stack as `"4 collaborators: Sarah, Jordan, Alex and 1 more"`.
- **Touch targets**: Material guidance is 48.dp minimum. The 28.dp comment pin needs ≥48.dp hit slop (wrap in a padded `Box`); the 20.dp action glyphs and tab items must sit in ≥48.dp rows.
- **Contrast**: Ink `#1E1E1E` on white meets AAA. `#757575` Text Secondary on white meets AA only at 14sp+ — do not use it below 14sp; for the 11sp caption / 12sp metadata on white, validate with a contrast checker and darken toward `#6B6B6B` if your build targets strict AA. In dark mode the 1dp `#2C2C2C` hairlines carry definition since shadows lose contrast on `#1E1E1E`.
- **Cube persistence**: derive cube color from a stable user ID hash (`FigmaColors.cubeFor`) and store it in user metadata — it must never change session-to-session, on any surface.
- **Dynamic color**: do **not** enable Material You `dynamicColorScheme()` — Figma's identity requires the fixed five cubes, Action Blue, and the exact `#1E1E1E` Editor canvas regardless of wallpaper.
