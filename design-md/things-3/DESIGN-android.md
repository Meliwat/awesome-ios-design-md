# Things 3 (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Things 3's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Things' serene white room, single-blue accent, the Today-yellow star, the circular-checkbox fill, the Magic-Plus) while making everything idiomatic Android — a `ModalNavigationDrawer` instead of a slide-over sidebar, Compose `Canvas` for the checkbox, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and `androidx.compose.material:material-icons-extended`.

## 1. Color Tokens

```kotlin
// ui/theme/ThingsColors.kt
import androidx.compose.ui.graphics.Color

object ThingsColors {
    // Canvas & Surfaces (light — primary)
    val Canvas   = Color(0xFFFFFFFF)
    val Surface1 = Color(0xFFF5F6F8)
    val Surface2 = Color(0xFFECECEC)
    val Divider  = Color(0xFFECECEC)

    // Text
    val TextPrimary   = Color(0xFF1D1D1F)
    val TextSecondary = Color(0xFF8A8A8E)
    val TextTertiary  = Color(0xFFC7C7CC)

    // Brand
    val Blue        = Color(0xFF4F97FF)
    val BluePressed = Color(0xFF3D7FE0)
    val BlueTint    = Color(0x1A4F97FF) // ~10% alpha

    // Today & Semantic
    val Today    = Color(0xFFFFD60A)
    val Deadline = Color(0xFFFF3B30)

    // Dark mode (secondary)
    val DarkCanvas  = Color(0xFF1C1C1E)
    val DarkSurface = Color(0xFF2C2C2E)
}
```

Wire it into a Material 3 `lightColorScheme` (Things is light-first) so ripples and stock components inherit the brand. Provide a `darkColorScheme` for the opt-in dark mode.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.foundation.isSystemInDarkTheme

private val LightScheme = lightColorScheme(
    primary       = ThingsColors.Blue,
    onPrimary     = Color.White,
    background     = ThingsColors.Canvas,
    onBackground   = ThingsColors.TextPrimary,
    surface        = ThingsColors.Canvas,
    onSurface      = ThingsColors.TextPrimary,
    surfaceVariant = ThingsColors.Surface1,
    outline        = ThingsColors.Divider,
    error          = ThingsColors.Deadline,
)

private val DarkScheme = darkColorScheme(
    primary    = ThingsColors.Blue,
    onPrimary  = Color.White,
    background = ThingsColors.DarkCanvas,
    surface    = ThingsColors.DarkSurface,
    outline    = Color(0xFF38383A),
    error      = ThingsColors.Deadline,
)

@Composable
fun ThingsTheme(content: @Composable () -> Unit) =
    MaterialTheme(
        colorScheme = if (isSystemInDarkTheme()) DarkScheme else LightScheme,
        typography  = ThingsTypography,
        content     = content,
    )
```

## 2. Typography

Things uses SF Pro (Apple system). On Android, Inter is the closest free substitute — its humanist tone is near. Drop the TTFs in `res/font/` (lowercase, snake_case).

```kotlin
// ui/theme/ThingsType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,  FontWeight.Normal),   // 400
    Font(R.font.inter_semibold, FontWeight.SemiBold), // 600
    Font(R.font.inter_bold,     FontWeight.Bold),     // 700
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object ThingsText {
    val ListTitle    = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 28.sp, lineHeight = 34.sp, letterSpacing = 0.3.sp)
    val ProjectTitle = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = 0.3.sp)
    val Heading      = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.2).sp)
    val TaskTitle    = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 17.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Body         = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Subtitle     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 14.sp, lineHeight = 19.sp)
    val Metadata     = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val Button       = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 17.sp, lineHeight = 17.sp)
    val Sidebar      = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 16.sp, lineHeight = 21.sp)
    val DatePill     = TextStyle(Inter, fontWeight = FontWeight.SemiBold, fontSize = 13.sp, lineHeight = 13.sp)
    val Count        = TextStyle(Inter, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 15.sp)
    val TinyUpper    = TextStyle(Inter, fontWeight = FontWeight.Bold,     fontSize = 12.sp, lineHeight = 14.sp, letterSpacing = 0.5.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val ThingsTypography = Typography(
    headlineLarge = ThingsText.ListTitle,
    headlineSmall = ThingsText.ProjectTitle,
    titleMedium   = ThingsText.Heading,
    bodyMedium    = ThingsText.Body,
    labelSmall    = ThingsText.TinyUpper,
)
```

## 3. Signature Components

### Circular Checkbox + Fill Animation (the signature delight)

```kotlin
import androidx.compose.animation.core.Animatable
import androidx.compose.animation.core.spring
import androidx.compose.animation.core.tween
import androidx.compose.foundation.Canvas
import androidx.compose.ui.graphics.StrokeCap
import androidx.compose.ui.graphics.drawscope.Stroke

@Composable
fun ThingsCheckbox(done: Boolean, onToggle: () -> Unit, size: Dp = 22.dp) {
    val fill = remember { Animatable(if (done) 1f else 0f) }
    val check = remember { Animatable(if (done) 1f else 0f) }
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()

    LaunchedEffect(done) {
        launch { fill.animateTo(if (done) 1f else 0f, tween(180)) }
        launch { check.animateTo(if (done) 1f else 0f, spring(dampingRatio = 0.6f, stiffness = 500f)) }
    }

    Box(
        Modifier
            .size(size)
            .clickable(
                interactionSource = remember { MutableInteractionSource() },
                indication = null,
            ) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onToggle()
            },
        contentAlignment = Alignment.Center,
    ) {
        Canvas(Modifier.size(size)) {
            val r = size.toPx() / 2
            if (fill.value < 1f) {
                drawCircle(ThingsColors.TextTertiary, radius = r - 1f,
                    style = Stroke(1.5.dp.toPx()), alpha = 1f - fill.value)
            }
            drawCircle(ThingsColors.Blue, radius = r * fill.value)
        }
        Icon(
            Icons.Filled.Check, null, tint = Color.White,
            modifier = Modifier
                .size(size * 0.6f)
                .graphicsLayer {
                    alpha = check.value
                    scaleX = 0.3f + check.value * 0.7f
                    scaleY = 0.3f + check.value * 0.7f
                },
        )
    }
}
```

### To-Do Row

```kotlin
@Composable
fun ToDoRow(
    title: String,
    tag: String? = null,
    datePill: String? = null,
    selected: Boolean = false,
) {
    var done by remember { mutableStateOf(false) }
    val alpha by animateFloatAsState(
        targetValue = if (done) 0f else 1f,
        animationSpec = tween(250, delayMillis = if (done) 250 else 0),
        label = "rowFade",
    )

    Row(
        modifier = Modifier
            .fillMaxWidth()
            .graphicsLayer { this.alpha = alpha }
            .clip(RoundedCornerShape(8.dp))
            .background(if (selected) ThingsColors.BlueTint else Color.Transparent)
            .padding(horizontal = 20.dp, vertical = 12.dp)
            .heightIn(min = 44.dp),
        horizontalArrangement = Arrangement.spacedBy(12.dp),
    ) {
        ThingsCheckbox(done = done, onToggle = { done = !done })
        Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(4.dp)) {
            Text(
                title,
                style = ThingsText.TaskTitle,
                color = if (done) ThingsColors.TextTertiary else ThingsColors.TextPrimary,
                textDecoration = if (done) TextDecoration.LineThrough else null,
            )
            if (tag != null || datePill != null) {
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
                    tag?.let {
                        Box(
                            Modifier.clip(CircleShape).background(ThingsColors.Surface1)
                                .padding(horizontal = 8.dp, vertical = 3.dp),
                        ) { Text(it, style = ThingsText.Metadata, color = ThingsColors.TextSecondary) }
                    }
                    datePill?.let { Text(it, style = ThingsText.DatePill, color = ThingsColors.Blue) }
                }
            }
        }
    }
}
```

### Magic-Plus Button (the signature control)

```kotlin
@Composable
fun MagicPlus(onAdd: () -> Unit, modifier: Modifier = Modifier) {
    val interaction = remember { MutableInteractionSource() }
    val pressed by interaction.collectIsPressedAsState()
    val scale by animateFloatAsState(if (pressed) 0.94f else 1f,
        spring(dampingRatio = 0.7f, stiffness = 600f), label = "plusScale")
    val haptics = LocalHapticFeedback.current

    Box(
        modifier = modifier
            .scale(scale)
            .size(56.dp)
            .shadow(20.dp, CircleShape, spotColor = ThingsColors.Blue.copy(alpha = 0.35f))
            .clip(CircleShape)
            .background(if (pressed) ThingsColors.BluePressed else ThingsColors.Blue)
            .clickable(interaction, indication = null) {
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onAdd()
            },
        // For press-and-drag insertion: detect a long-press then a drag via
        // Modifier.pointerInput; render a #4F97FF insertion line at the nearest
        // LazyColumn item boundary and insert an editable row on release.
        contentAlignment = Alignment.Center,
    ) {
        Icon(Icons.Filled.Add, "Add to-do", tint = Color.White, modifier = Modifier.size(26.dp))
    }
}
```

### List Title Header

```kotlin
@Composable
fun ListTitleHeader(title: String, isToday: Boolean = false) {
    Row(
        Modifier.fillMaxWidth().padding(start = 20.dp, end = 20.dp, top = 24.dp, bottom = 8.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        if (isToday) Icon(Icons.Filled.Star, null, tint = ThingsColors.Today, modifier = Modifier.size(22.dp))
        Text(title, style = ThingsText.ListTitle, color = ThingsColors.TextPrimary)
    }
}
```

### Section Heading + Hairline Divider

```kotlin
@Composable
fun SectionHeading(title: String, collapsed: Boolean, onToggle: () -> Unit) {
    val rot by animateFloatAsState(if (collapsed) -90f else 0f, tween(200), label = "chev")
    Column {
        Row(
            Modifier
                .fillMaxWidth()
                .clickable(onClick = onToggle)
                .padding(start = 20.dp, end = 20.dp, top = 24.dp, bottom = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Text(title, style = ThingsText.Heading, color = ThingsColors.TextPrimary)
            Icon(Icons.Filled.KeyboardArrowDown, "Toggle", tint = ThingsColors.TextSecondary,
                modifier = Modifier.size(14.dp).graphicsLayer { rotationZ = rot })
        }
        HorizontalDivider(color = ThingsColors.Divider, modifier = Modifier.padding(horizontal = 20.dp))
    }
}
```

### Project Pie-Progress Ring

```kotlin
@Composable
fun PieProgress(fraction: Float, size: Dp = 20.dp) {
    val f by animateFloatAsState(fraction, tween(300), label = "pie")
    Canvas(Modifier.size(size)) {
        val stroke = 2.dp.toPx()
        val d = this.size.minDimension - stroke
        drawArc(ThingsColors.Divider, 0f, 360f, false,
            topLeft = androidx.compose.ui.geometry.Offset(stroke / 2, stroke / 2),
            size = androidx.compose.ui.geometry.Size(d, d), style = Stroke(stroke))
        drawArc(ThingsColors.Blue, -90f, 360f * f, false,
            topLeft = androidx.compose.ui.geometry.Offset(stroke / 2, stroke / 2),
            size = androidx.compose.ui.geometry.Size(d, d),
            style = Stroke(stroke, cap = StrokeCap.Round))
    }
}
```

## 4. Navigation (no bottom bar) — Sidebar via ModalNavigationDrawer

Things' iOS app is a slide-over sidebar with **no bottom navigation**. Use Material 3 `ModalNavigationDrawer` with an intentionally soft scrim.

```kotlin
@Composable
fun ThingsScaffold() {
    val drawerState = rememberDrawerState(DrawerValue.Closed)
    ModalNavigationDrawer(
        drawerState = drawerState,
        scrimColor = Color.Black.copy(alpha = 0.2f), // soft — Things never feels heavy
        drawerContent = {
            ModalDrawerSheet(
                drawerContainerColor = ThingsColors.Canvas,
                modifier = Modifier.width(300.dp),
            ) {
                data class Item(val icon: ImageVector, val label: String, val count: Int?, val active: Boolean, val tint: Color)
                listOf(
                    Item(Icons.Filled.Inbox, "Inbox", 2, false, ThingsColors.TextSecondary),
                    Item(Icons.Filled.Star, "Today", 5, true, ThingsColors.Today),
                    Item(Icons.Filled.CalendarMonth, "Upcoming", null, false, ThingsColors.TextSecondary),
                    Item(Icons.Filled.Layers, "Anytime", null, false, ThingsColors.TextSecondary),
                    Item(Icons.Filled.Archive, "Someday", null, false, ThingsColors.TextSecondary),
                ).forEach { item ->
                    NavigationDrawerItem(
                        icon = { Icon(item.icon, null, tint = if (item.active) ThingsColors.Blue else item.tint, modifier = Modifier.size(18.dp)) },
                        label = {
                            Row(Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween) {
                                Text(item.label, style = ThingsText.Sidebar,
                                    fontWeight = if (item.active) FontWeight.SemiBold else FontWeight.Normal,
                                    color = ThingsColors.TextPrimary)
                                item.count?.let { Text("$it", style = ThingsText.Count, color = ThingsColors.TextSecondary) }
                            }
                        },
                        selected = item.active,
                        onClick = {},
                        colors = NavigationDrawerItemDefaults.colors(
                            selectedContainerColor = ThingsColors.BlueTint,
                            unselectedContainerColor = Color.Transparent,
                        ),
                        modifier = Modifier.height(40.dp),
                    )
                }
            }
        },
    ) {
        // Today list content + a Box-aligned MagicPlus bottom-end
    }
}
```

## 5. Motion

| Moment | Compose recipe |
|--------|----------------|
| Checkbox completion | `Animatable` fill `tween(180)` center-out + check `spring(dampingRatio = 0.6f)`, `HapticFeedbackType.LongPress`; then row `alpha` `tween(250, delay 250)` fade-out |
| Magic-Plus tap | `animateFloatAsState` 1 → 0.94 `spring(dampingRatio = 0.7f)`, `HapticFeedbackType.LongPress` |
| Heading collapse/expand | `AnimatedVisibility` `expandVertically`/`shrinkVertically` `tween(200)` + chevron `rotationZ` |
| Pie-progress | `animateFloatAsState` fraction `tween(300)` driving the sweep arc |
| Sidebar slide-over | `ModalNavigationDrawer` default spring, soft `0.2` scrim |

Haptics: prefer `LocalHapticFeedback`. For a softer tick matching iOS `.soft`/`.medium` impact, use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)` (gentle) or `CONTEXT_CLICK` (API 30+) on checkbox toggle and Magic-Plus.

## 6. Icons

The closest first-party set is `androidx.compose.material:material-icons-extended`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Unchecked checkbox | drawn circle | drawn `Canvas` stroke |
| Completed check | `checkmark` | `Icons.Filled.Check` |
| Magic-Plus | `plus` | `Icons.Filled.Add` |
| Today / scheduled | `star.fill` | `Icons.Filled.Star` |
| Inbox (sidebar) | `tray.fill` | `Icons.Filled.Inbox` |
| Upcoming (sidebar) | `calendar` | `Icons.Filled.CalendarMonth` |
| Anytime (sidebar) | `square.stack` | `Icons.Filled.Layers` |
| Someday (sidebar) | `archivebox` | `Icons.Filled.Archive` |
| Logbook (sidebar) | `book.closed` | `Icons.Filled.Book` |
| Heading chevron | `chevron.down` | `Icons.Filled.KeyboardArrowDown` |
| Deadline flag | `flag.fill` | `Icons.Filled.Flag` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| More | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Tag | `tag` | `Icons.Filled.LocalOffer` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the white canvas wants dark-content system bars. Apply `Scaffold` / `Modifier.windowInsetsPadding(WindowInsets.systemBars)` so the Magic-Plus clears the gesture nav.
- **Light-first**: verify the white-room calm at the default scheme; the dark scheme is opt-in — keep Blue + Today yellow identical across both (they read on both).
- **Whitespace**: preserve the 20.dp horizontal margins and roomy 12.dp row padding — do not compress for density.
- **Font scaling**: this is a reading surface — keep `sp` scaling on titles/tasks/notes; pin only the 22.dp checkbox and tabular counts (derive from `dp` or wrap in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))`).
- **TalkBack**: set `Modifier.semantics { role = Role.Checkbox; toggleableState = ... }` on the checkbox with a label ("Email the design feedback, to-do"); announce completion; communicate "scheduled today" as text alongside the star, never color alone; expose heading collapse state via `stateDescription`.
- **Touch targets**: Material guidance is 48.dp minimum. The 22.dp checkbox must get a 48.dp hit area via padding; the 56.dp Magic-Plus is well clear.
- **Contrast**: `#8A8A8E` on `#FFFFFF` passes WCAG AA at 13sp+. `#C7C7CC` is intentionally low-contrast for completed/placeholder text — never use it for essential content.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Things' identity requires the fixed white canvas, single blue accent, and the one Today-yellow star regardless of wallpaper.
