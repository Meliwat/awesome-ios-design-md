# Raya (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Raya's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the slideshow profile, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Raya's pure-black gallery frame, zero brand color, one rationed white accent, hairline-only depth, the music-scored slideshow) while making everything idiomatic Android — a `NavigationBar` instead of a UITabBar, `ExoPlayer`/`Coil` for slides, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for photo slides, and `media3-exoplayer` for video slides. **No Material You / dynamic color** — Raya's monochrome identity must hold regardless of wallpaper (there is no brand accent to harmonize with one). Raya is permanently dark; there is no light scheme to provide.

## 1. Color Tokens

```kotlin
// ui/theme/RayaColors.kt
import androidx.compose.ui.graphics.Color

object RayaColors {
    // Canvas & Surfaces (permanently dark — the only theme)
    val Canvas   = Color(0xFF000000) // pure black, never warmed
    val Surface1 = Color(0xFF0D0D0D) // sheets, settings rows — first faint lift
    val Surface2 = Color(0xFF161616) // inset fields, pressed rows
    val Divider  = Color(0xFF242424) // hairline dividers between rows
    val Hairline = Color(0xFF2E2E2E) // the 1px border — the ONLY depth cue

    // Text
    val TextPrimary   = Color(0xFFFFFFFF)
    val TextSecondary = Color(0xFF9A9A9A)
    val TextTertiary  = Color(0xFF5E5E5E)

    // Accent (there is, by design, only one)
    val Accent        = Color(0xFFFFFFFF)
    val AccentPressed = Color(0xFFD8D8D8)

    // Semantic (used reluctantly — leave-Raya / report / payment-failed only)
    val Error = Color(0xFFE5484D)

    // On-photo
    val OnPhoto    = Color(0xFFFFFFFF).copy(alpha = 0.92f)
    val OnPhotoDim = Color(0xFFFFFFFF).copy(alpha = 0.60f)
}
```

Wire a single dark scheme. There is no light scheme and no second accent — white *is* the entire brand.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.runtime.Composable

private val RayaScheme = darkColorScheme(
    primary        = RayaColors.Accent,        // white — the only accent
    onPrimary      = RayaColors.Canvas,        // black label on the white button
    background     = RayaColors.Canvas,
    onBackground   = RayaColors.TextPrimary,
    surface        = RayaColors.Surface1,
    onSurface      = RayaColors.TextPrimary,
    surfaceVariant = RayaColors.Surface2,
    outline        = RayaColors.Hairline,
    error          = RayaColors.Error,
)

@Composable
fun RayaTheme(content: @Composable () -> Unit) = MaterialTheme(
    colorScheme = RayaScheme,   // always dark — never branch on isSystemInDarkTheme()
    typography  = RayaTypography,
    content     = content,
)
```

## 2. Typography (Material 3)

One clean low-contrast neo-grotesque does everything — the typographic mirror of the one-color discipline. Drop `Inter` (SIL OFL) TTFs in `res/font/`. No serif, no mono, no display face.

```kotlin
// ui/theme/RayaType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),
    Font(R.font.inter_medium,   FontWeight.Medium),
    Font(R.font.inter_semibold, FontWeight.SemiBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object RayaText {
    val Wordmark    = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 30.sp, lineHeight = 30.sp, letterSpacing = (-0.5).sp)
    val ProfileName = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 28.sp, lineHeight = 31.sp, letterSpacing = (-0.4).sp)
    val Section     = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Subtitle    = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 16.sp, lineHeight = 21.sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 23.sp)
    val Role        = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 13.sp, lineHeight = 17.sp, letterSpacing = 0.4.sp) // render UPPERCASE
    val Music       = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 0.2.sp)
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Eyebrow     = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 11.sp, lineHeight = 11.sp, letterSpacing = 1.6.sp) // render UPPERCASE
    val Button      = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 14.sp, lineHeight = 14.sp, letterSpacing = 0.3.sp)
    val Caption     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 12.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.Medium,   fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.4.sp)
}

// Map onto Material 3 slots so stock components inherit the identity
val RayaTypography = Typography(
    headlineLarge = RayaText.ProfileName,
    titleLarge    = RayaText.Section,
    bodyMedium    = RayaText.Body,
    labelSmall    = RayaText.Tab,
)
```

Render role lines and eyebrows uppercase at the call site: `Text(role.uppercase(), style = RayaText.Role)`.

## 3. Signature Components

### Slideshow Profile (the product)

Full-bleed photo/video, segmented story bars, music ticker + equalizer, editorial caption over a black scrim.

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.unit.dp
import coil.compose.AsyncImage

data class RayaProfile(
    val slides: List<String>, val activeIndex: Int, val progress: Float, // 0..1 in active slide
    val name: String, val role: String, val bio: String, val location: String,
    val artist: String, val track: String,
)

@Composable
fun SlideshowProfile(p: RayaProfile, onScrubPrev: () -> Unit, onScrubNext: () -> Unit) {
    BoxWithConstraints(
        Modifier
            .fillMaxSize()
            .background(RayaColors.Canvas)
            .pointerInput(Unit) {
                detectTapGestures { o: Offset ->
                    if (o.x < size.width / 3f) onScrubPrev() else if (o.x > size.width * 2 / 3f) onScrubNext()
                }
            },
    ) {
        AsyncImage(
            model = p.slides[p.activeIndex], contentDescription = null,
            modifier = Modifier.matchParentSize(), contentScale = ContentScale.Crop,
        )
        // Bottom scrim for caption legibility
        Box(
            Modifier
                .fillMaxWidth()
                .fillMaxHeight(0.62f)
                .align(Alignment.BottomCenter)
                .background(
                    Brush.verticalGradient(
                        0f to Color.Transparent,
                        0.55f to Color.Black.copy(alpha = 0.55f),
                        1f to Color.Black.copy(alpha = 0.92f),
                    )
                )
        )

        // Story bars
        Row(
            Modifier.fillMaxWidth().padding(top = 56.dp, start = 16.dp, end = 16.dp),
            horizontalArrangement = Arrangement.spacedBy(5.dp),
        ) {
            p.slides.forEachIndexed { i, _ ->
                Box(Modifier.weight(1f).height(2.5.dp).clip(RoundedCornerShape(2.dp)).background(Color.White.copy(alpha = 0.3f))) {
                    val fill = when { i < p.activeIndex -> 1f; i == p.activeIndex -> p.progress; else -> 0f }
                    Box(Modifier.fillMaxWidth(fill).height(2.5.dp).clip(RoundedCornerShape(2.dp)).background(Color.White))
                }
            }
        }

        // Music ticker
        Row(
            Modifier.fillMaxWidth().padding(top = 76.dp, start = 16.dp, end = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Icon(Icons.Filled.MusicNote, null, tint = Color.White, modifier = Modifier.size(13.dp))
            Text("${p.artist} — ${p.track}", style = RayaText.Music, color = Color.White.copy(alpha = 0.85f))
            Spacer(Modifier.weight(1f))
            Equalizer()
        }

        // Caption
        Column(Modifier.align(Alignment.BottomStart).padding(start = 20.dp, end = 20.dp, bottom = 96.dp)) {
            Text(p.name, style = RayaText.ProfileName, color = Color.White)
            Text(p.role.uppercase(), style = RayaText.Role, color = RayaColors.OnPhoto, modifier = Modifier.padding(top = 8.dp))
            Text(p.bio, style = RayaText.Body, color = Color.White.copy(alpha = 0.72f), modifier = Modifier.padding(top = 10.dp))
            Row(Modifier.padding(top = 10.dp), verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                Icon(Icons.Filled.LocationOn, null, tint = RayaColors.OnPhotoDim, modifier = Modifier.size(12.dp))
                Text(p.location, style = RayaText.Meta, color = RayaColors.OnPhotoDim)
            }
        }
    }
}
```

### Equalizer (animated 4-bar)

```kotlin
import androidx.compose.animation.core.*

@Composable
fun Equalizer() {
    val t = rememberInfiniteTransition(label = "eq")
    val peaks = listOf(0.4f, 0.9f, 0.6f, 1.0f)
    Row(verticalAlignment = Alignment.Bottom, horizontalArrangement = Arrangement.spacedBy(2.dp), modifier = Modifier.height(11.dp)) {
        peaks.forEachIndexed { i, peak ->
            val h by t.animateFloat(
                initialValue = if (i % 2 == 0) 0.4f else peak,
                targetValue  = if (i % 2 == 0) peak else 0.4f,
                animationSpec = infiniteRepeatable(tween(600, easing = LinearEasing), RepeatMode.Reverse),
                label = "bar$i",
            )
            Box(Modifier.width(2.dp).height((11 * h).dp).clip(RoundedCornerShape(1.dp)).background(Color.White))
        }
    }
}
```

### Slideshow Actions (Skip / Heart / Note)

```kotlin
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.draw.scale
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback

@Composable
fun SlideshowActions(onSkip: () -> Unit, onLike: () -> Unit, onNote: () -> Unit) {
    var liked by remember { mutableStateOf(false) }
    val scale by animateFloatAsState(if (liked) 1.15f else 1f, spring(), label = "heart")
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier.fillMaxWidth().padding(bottom = 18.dp),
        horizontalArrangement = Arrangement.spacedBy(56.dp, Alignment.CenterHorizontally),
        verticalAlignment = Alignment.CenterVertically,
    ) {
        Icon(Icons.Filled.Close, "Skip", tint = Color.White.copy(alpha = 0.9f),
            modifier = Modifier.size(26.dp).clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) { onSkip() })
        Box(
            Modifier.size(60.dp).clip(CircleShape).border(1.dp, Color.White, CircleShape)
                .clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress); liked = !liked; onLike()
                },
            contentAlignment = Alignment.Center,
        ) {
            Icon(Icons.Outlined.FavoriteBorder, "Like", tint = Color.White, modifier = Modifier.size(24.dp).scale(scale))
        }
        Icon(Icons.Outlined.ChatBubbleOutline, "Note", tint = Color.White.copy(alpha = 0.9f),
            modifier = Modifier.size(24.dp).clickable(indication = null, interactionSource = remember { MutableInteractionSource() }) { onNote() })
    }
}
```

### Member Chip (selection = black/white inversion)

```kotlin
@Composable
fun MemberChip(label: String, selected: Boolean, onClick: () -> Unit) {
    Box(
        Modifier
            .clip(RoundedCornerShape(999.dp))
            .background(if (selected) Color.White else Color.Transparent)
            .border(1.dp, if (selected) Color.White else RayaColors.Hairline, RoundedCornerShape(999.dp))
            .clickable { onClick() }
            .padding(horizontal = 16.dp, vertical = 8.dp),
    ) {
        Text(label, style = RayaText.Caption.copy(fontWeight = FontWeight.Medium), color = if (selected) Color.Black else Color.White)
    }
}
```

## 4. Core Navigation (Bottom Bar)

Minimal — 3 destinations, opaque true-black (no blur, no tint pill), a 1dp `#2E2E2E` top hairline; active white, inactive `#5E5E5E`.

```kotlin
import androidx.compose.material3.*

@Composable
fun RayaBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    Column {
        HorizontalDivider(thickness = 1.dp, color = RayaColors.Hairline)
        NavigationBar(containerColor = RayaColors.Canvas, tonalElevation = 0.dp) {
            val items = listOf(
                "Discover" to Icons.Outlined.AutoAwesome,
                "Messages" to Icons.Outlined.ChatBubbleOutline,
                "Profile"  to Icons.Outlined.PersonOutline,
            )
            items.forEachIndexed { i, (label, icon) ->
                NavigationBarItem(
                    selected = selected == i,
                    onClick = { onSelect(i) },
                    icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                    label = { Text(label, style = RayaText.Tab) },
                    colors = NavigationBarItemDefaults.colors(
                        selectedIconColor = RayaColors.TextPrimary,   // white, no tint
                        selectedTextColor = RayaColors.TextPrimary,
                        unselectedIconColor = RayaColors.TextTertiary,
                        unselectedTextColor = RayaColors.TextTertiary,
                        indicatorColor = Color.Transparent,           // no Material pill — Raya has none
                    ),
                )
            }
        }
    }
}
```

## 5. Navigation

Raya is effectively chromeless on the slideshow — the story bars *are* the top nav. Use Navigation Compose (`NavHost`) with three destinations (`discover`, `messages`, `profile`). The slideshow itself has no app bar; an optional lowercase `raya` wordmark fades in for 1.5s on cold-open then fades out so the member's media is unobstructed. Settings/profile screens group rows on `Surface1` with 1dp `Divider` separators — no card fill, no row rounding, no shadow.

```kotlin
// Optional cold-open wordmark
@Composable
fun WordmarkCueIfFirstLaunch(show: Boolean) {
    AnimatedVisibility(visible = show, enter = fadeIn(tween(400)), exit = fadeOut(tween(300))) {
        Box(Modifier.fillMaxSize(), contentAlignment = Alignment.Center) {
            Text("raya", style = RayaText.Wordmark, color = Color.White)
        }
    }
}
```

## 6. Motion

Motion, like color, is rationed — nothing pulses or bounces beyond the single heart confirmation.

| Moment | Compose recipe |
|--------|----------------|
| Slideshow advance | drive `progress` 0→1 per slide via a frame loop / `Animatable`; on 1.0 increment `activeIndex` |
| Story bar fill | `Modifier.fillMaxWidth(progress)` — linear with playback, no spring |
| Equalizer | `rememberInfiniteTransition` + `infiniteRepeatable(tween(600, LinearEasing), Reverse)` — implies the song is playing |
| Slide cross-fade | `Crossfade(targetState = activeIndex, animationSpec = tween(220))` |
| Heart press | `animateFloatAsState(if (liked) 1.15f else 1f, spring())` + soft haptic |
| Wordmark cold-open | `fadeIn(tween(400))` → 1.5s hold → `fadeOut(tween(300))` |
| Scrub | tap left/right thirds → instant index change (no animation) |

```kotlin
// The canonical Raya motion — a quiet slide cross-fade, never a flashy transition
Crossfade(targetState = activeIndex, animationSpec = tween(220), label = "slide") { i ->
    AsyncImage(model = slides[i], contentDescription = null, contentScale = ContentScale.Crop, modifier = Modifier.fillMaxSize())
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` for the soft impact on like, scrub-commit, and send — sparingly. Never buzz during the slideshow or equalizer.

## 7. Icons

Use `androidx.compose.material:material-icons-extended`. Everything is an **outline** — Raya has no filled-vs-outline language; never use a filled `Favorite`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Skip / pass | `xmark` | `Icons.Filled.Close` |
| Like (focal action) | `heart` (outline) | `Icons.Outlined.FavoriteBorder` (never `Icons.Filled.Favorite`) |
| Note / message | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Music ticker glyph | `music.note` | `Icons.Filled.MusicNote` |
| Location | `mappin.and.ellipse` | `Icons.Filled.LocationOn` |
| Discover (tab) | `sparkles` | `Icons.Outlined.AutoAwesome` |
| Messages (tab) | `bubble.left` | `Icons.Outlined.ChatBubbleOutline` |
| Profile (tab) | `person` | `Icons.Outlined.PersonOutline` |
| More / actions | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Settings | `gearshape` | `Icons.Outlined.Settings` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (`compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`). `media3-exoplayer` for video slides, `Coil` for photo slides.
- **Edge-to-edge**: call `enableEdgeToEdge()`; force light-content system bars (the canvas is always black). Story bars sit just below the camera cutout — offset by the status-bar inset via `WindowInsets.statusBars`.
- **No light theme, no dynamic color**: never branch on `isSystemInDarkTheme()`; do **not** enable `dynamicColorScheme()` — Raya's pure-black/one-white identity must hold regardless of wallpaper (there is no brand accent to harmonize with one).
- **No shadows**: never set `Modifier.shadow(...)` or `tonalElevation` > 0. Separation is the faint surface lift (`#0D0D0D` vs `#000000`) and the 1dp `#2E2E2E` hairline only.
- **Font scaling**: `sp` honors the user's scale — keep it on name, body, section titles, bio. Pin layout-sensitive text (story-bar/music-ticker, eyebrows, role lines, 10sp tab labels) by deriving from `dp` or `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`.
- **TalkBack**: the 1dp hairline must never be the sole carrier of meaning — pair structural separation with content descriptions; label the slideshow "Profile slideshow, slide X of N", the scrub zones "Previous slide" / "Next slide", the Heart "Like {name}", Skip "Skip", Note "Send a note". Expose the music ticker as "Now playing: {artist} — {track}".
- **Touch targets**: Material guidance is 48dp — give the 26dp Skip and 24dp Note glyphs a 48dp hit via padding; the Heart is a 60dp circle; the story-bar scrub zones span the full left/right thirds.
- **Contrast**: `#FFFFFF` on `#000000` is maximum contrast. On-photo caption text uses `white @ 0.92` over a `→ rgba(0,0,0,0.92)` scrim — verify the scrim is deep enough under bright member photos; deepen the gradient, never lighten the text.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, replace the slide `Crossfade` with an instant swap, freeze the equalizer at a static mid-state, skip the wordmark cold-open, and disable the heart scale — keep the soft haptic.
- **No color, ever**: never introduce a tint or gradient into the chrome. The only color in the product is the member's full-bleed photo/video — never filter, tint, or colored-frame it; the member's name label (not blue-vs-anything) must carry identity for users who can't perceive subtle tonal lifts.
