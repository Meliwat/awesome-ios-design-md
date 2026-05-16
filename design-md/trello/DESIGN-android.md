# Trello (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Trello's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (Trello's board-as-backdrop, pale lists of white cards, the navy-tinted card shadow, the drag lift, the functional label palette) while making everything idiomatic Android — `LazyRow`/`LazyColumn` instead of UIScrollView, `sp`/`dp` instead of `pt`, and **no bottom NavigationBar** (Trello has none).

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for board photo backgrounds.

## 1. Color Tokens

```kotlin
// ui/theme/TrelloColors.kt
import androidx.compose.ui.graphics.Color

object TrelloColors {
    // Action & board
    val Action        = Color(0xFF0C66E4)
    val ActionPressed = Color(0xFF0055CC)
    val ActionTint    = Color(0xFFE9F2FF)
    val BoardBlue     = Color(0xFF0079BF) // default backdrop

    // Surfaces & text
    val Card          = Color(0xFFFFFFFF)
    val ListSurface   = Color(0xFFF1F2F4)
    val Sunken        = Color(0xFFEBECF0)
    val Divider       = Color(0xFFDFE1E6)
    val Border        = Color(0xFFC1C7D0)
    val TextPrimary   = Color(0xFF172B4D)
    val TextSecondary = Color(0xFF5E6C84)
    val TextTertiary  = Color(0xFF8993A4)

    // Card label palette (functional)
    val LabelGreen  = Color(0xFF4BCE97)
    val LabelYellow = Color(0xFFF5CD47)
    val LabelOrange = Color(0xFFFEA362)
    val LabelRed    = Color(0xFFF87168)
    val LabelPurple = Color(0xFF9F8FEF)
    val LabelBlue   = Color(0xFF579DFF)

    // Semantic
    val Success = Color(0xFF1F845A)
    val DueSoon = Color(0xFFB65C02)
    val Overdue = Color(0xFFC9372C)

    // Signature navy shadow base
    val ShadowNavy = Color(0xFF091E42)
}
```

Wire it into a Material 3 `lightColorScheme`. Trello supports a real dark mode for UI chrome; the board backdrop still drives the board area regardless.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val TrelloScheme = lightColorScheme(
    primary          = TrelloColors.Action,
    onPrimary        = Color.White,
    primaryContainer = TrelloColors.ActionTint,
    background        = TrelloColors.ListSurface,
    onBackground      = TrelloColors.TextPrimary,
    surface           = TrelloColors.Card,
    onSurface         = TrelloColors.TextPrimary,
    surfaceVariant    = TrelloColors.ListSurface,
    outline           = TrelloColors.Border,
    outlineVariant    = TrelloColors.Divider,
    error             = TrelloColors.Overdue,
)

@Composable
fun TrelloTheme(content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = TrelloScheme, typography = TrelloTypography, content = content)
```

## 2. Typography

Trello uses the system font deliberately (legibility on any backdrop). Compose's default `FontFamily.Default` is Roboto on Android; Inter is the closest substitute if bundled.

```kotlin
// ui/theme/TrelloType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

private val Sys = FontFamily.Default

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object TrelloText {
    val SheetTitle = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.3).sp)
    val BoardName  = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = (-0.2).sp)
    val Section    = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 17.sp, lineHeight = 22.sp, letterSpacing = (-0.1).sp)
    val ListTitle  = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val CardTitle  = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 15.sp, lineHeight = 20.sp)
    val Body       = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 22.sp)
    val Button     = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 20.sp)
    val Badge      = TextStyle(Sys, fontWeight = FontWeight.SemiBold, fontSize = 12.sp, lineHeight = 14.sp)
    val Subtitle   = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 13.sp, lineHeight = 17.sp)
    val LabelUpper = TextStyle(Sys, fontWeight = FontWeight.Bold,     fontSize = 11.sp, lineHeight = 13.sp, letterSpacing = 0.4.sp)
    val Composer   = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 15.sp, lineHeight = 21.sp)
    val Caption    = TextStyle(Sys, fontWeight = FontWeight.Normal,   fontSize = 11.sp, lineHeight = 14.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val TrelloTypography = Typography(
    headlineLarge = TrelloText.SheetTitle,
    headlineSmall = TrelloText.Section,
    titleMedium   = TrelloText.CardTitle,
    bodyMedium    = TrelloText.Body,
    labelSmall    = TrelloText.Badge,
)
```

## 3. Signature Components

### Card (with drag lift)

```kotlin
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.animation.core.animateFloatAsState
import androidx.compose.animation.core.spring
import androidx.compose.ui.draw.scale
import androidx.compose.ui.draw.shadow

@Composable
fun TrelloCard(
    title: String,
    labels: List<Color>,
    due: String? = null,
    checklist: String? = null,
    isDragging: Boolean = false,
    modifier: Modifier = Modifier,
) {
    val scale by animateFloatAsState(if (isDragging) 1.03f else 1f, spring(), label = "cardScale")
    val elevation by animateDpAsState(if (isDragging) 24.dp else 1.dp, label = "cardElev")

    Column(
        modifier
            .scale(scale)
            .shadow(
                elevation = elevation,
                shape = RoundedCornerShape(8.dp),
                ambientColor = TrelloColors.ShadowNavy,
                spotColor = TrelloColors.ShadowNavy,
            )
            .clip(RoundedCornerShape(8.dp))
            .background(TrelloColors.Card)
            .padding(horizontal = 12.dp, vertical = 8.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        if (labels.isNotEmpty()) {
            Row(horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                labels.forEach { c ->
                    Box(Modifier.size(width = 36.dp, height = 8.dp).clip(RoundedCornerShape(4.dp)).background(c))
                }
            }
        }
        Text(title, style = TrelloText.CardTitle, color = TrelloColors.TextPrimary, maxLines = 4, overflow = TextOverflow.Ellipsis)
        if (due != null || checklist != null) {
            Row(horizontalArrangement = Arrangement.spacedBy(8.dp), verticalAlignment = Alignment.CenterVertically) {
                if (due != null) {
                    Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                        Icon(Icons.Filled.Schedule, null, tint = TrelloColors.DueSoon, modifier = Modifier.size(12.dp))
                        Text(due, style = TrelloText.Badge, color = TrelloColors.DueSoon)
                    }
                }
                if (checklist != null) {
                    Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
                        Icon(Icons.Filled.CheckBox, null, tint = TrelloColors.TextSecondary, modifier = Modifier.size(12.dp))
                        Text(checklist, style = TrelloText.Badge, color = TrelloColors.TextSecondary)
                    }
                }
            }
        }
    }
}
```

### List / Column

```kotlin
@Composable
fun TrelloListColumn(
    title: String,
    cards: List<CardModel>,
    modifier: Modifier = Modifier,
) {
    var composing by remember { mutableStateOf(false) }
    var draft by remember { mutableStateOf("") }

    Column(
        modifier
            .width(272.dp)
            .shadow(2.dp, RoundedCornerShape(12.dp), ambientColor = TrelloColors.ShadowNavy, spotColor = TrelloColors.ShadowNavy)
            .clip(RoundedCornerShape(12.dp))
            .background(TrelloColors.ListSurface),
    ) {
        Row(
            Modifier.fillMaxWidth().padding(start = 8.dp, end = 8.dp, top = 8.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(6.dp),
        ) {
            Text(title, style = TrelloText.ListTitle, color = TrelloColors.TextPrimary)
            Text("${cards.size}", style = TrelloText.Badge, color = TrelloColors.TextSecondary)
            Spacer(Modifier.weight(1f))
            Icon(Icons.Filled.MoreHoriz, "More", tint = TrelloColors.TextSecondary, modifier = Modifier.size(16.dp))
        }

        LazyColumn(
            Modifier.heightIn(max = 520.dp).padding(8.dp),
            verticalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            items(cards) { c -> TrelloCard(c.title, c.labels, c.due, c.checklist) }
        }

        if (composing) {
            Column(Modifier.padding(8.dp), verticalArrangement = Arrangement.spacedBy(8.dp)) {
                BasicTextField(
                    value = draft, onValueChange = { draft = it },
                    textStyle = TrelloText.Composer.copy(color = TrelloColors.TextPrimary),
                    modifier = Modifier
                        .fillMaxWidth()
                        .clip(RoundedCornerShape(8.dp))
                        .background(TrelloColors.Card)
                        .border(2.dp, TrelloColors.Action, RoundedCornerShape(8.dp))
                        .padding(8.dp),
                )
                Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                    Box(
                        Modifier.clip(RoundedCornerShape(8.dp)).background(TrelloColors.Action)
                            .height(36.dp).padding(horizontal = 16.dp).clickable { },
                        contentAlignment = Alignment.Center,
                    ) { Text("Add card", style = TrelloText.Button, color = Color.White) }
                    Icon(Icons.Filled.Close, "Cancel", tint = TrelloColors.TextSecondary,
                        modifier = Modifier.size(20.dp).clickable { composing = false })
                }
            }
        } else {
            Row(
                Modifier.fillMaxWidth().clickable { composing = true }.padding(12.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(8.dp),
            ) {
                Icon(Icons.Filled.Add, null, tint = TrelloColors.TextSecondary, modifier = Modifier.size(16.dp))
                Text("Add a card", style = TrelloText.Body, color = TrelloColors.TextSecondary)
            }
        }
    }
}
```

### Board (horizontal scroll, backdrop)

```kotlin
@Composable
fun TrelloBoard(
    boardName: String,
    lists: List<ListModel>,
    backdrop: Color = TrelloColors.BoardBlue, // or AsyncImage for a photo board + scrim
) {
    Box(Modifier.fillMaxSize().background(backdrop)) {
        // For a photo backdrop: AsyncImage(...) then Box(Modifier.matchParentSize().background(Color.Black.copy(alpha = 0.16f)))
        Column {
            Row(
                Modifier.fillMaxWidth().height(44.dp).padding(horizontal = 12.dp),
                verticalAlignment = Alignment.CenterVertically,
                horizontalArrangement = Arrangement.spacedBy(12.dp),
            ) {
                Icon(Icons.Filled.ArrowBack, "Back", tint = Color.White)
                Text(boardName, style = TrelloText.BoardName, color = Color.White)
                Spacer(Modifier.weight(1f))
                Row(
                    Modifier.clip(RoundedCornerShape(8.dp)).background(Color.White.copy(alpha = 0.16f))
                        .height(32.dp).padding(horizontal = 12.dp),
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(6.dp),
                ) {
                    Icon(Icons.Filled.FilterList, null, tint = Color.White, modifier = Modifier.size(14.dp))
                    Text("Filter", style = TrelloText.Badge, color = Color.White)
                }
                Icon(Icons.Filled.MoreHoriz, "Menu", tint = Color.White)
            }

            LazyRow(
                contentPadding = PaddingValues(8.dp),
                horizontalArrangement = Arrangement.spacedBy(8.dp),
                verticalAlignment = Alignment.Top,
            ) {
                items(lists) { l -> TrelloListColumn(l.title, l.cards) }
                item {
                    Box(
                        Modifier.width(272.dp).height(44.dp)
                            .clip(RoundedCornerShape(12.dp)).background(Color.White.copy(alpha = 0.16f))
                            .padding(horizontal = 12.dp),
                        contentAlignment = Alignment.CenterStart,
                    ) { Text("+ Add list", style = TrelloText.Body, color = Color.White) }
                }
            }
        }
    }
}
```

### Card Detail Sheet

```kotlin
import androidx.compose.material3.ModalBottomSheet
import androidx.compose.material3.rememberModalBottomSheetState

@Composable
fun CardDetailSheet(title: String, progress: Float, onDismiss: () -> Unit) {
    ModalBottomSheet(
        onDismissRequest = onDismiss,
        sheetState = rememberModalBottomSheetState(skipPartiallyExpanded = true),
        containerColor = TrelloColors.Card,
        shape = RoundedCornerShape(topStart = 16.dp, topEnd = 16.dp),
    ) {
        Column(Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(20.dp)) {
            Text(title, style = TrelloText.SheetTitle, color = TrelloColors.TextPrimary)
            Text("CHECKLIST", style = TrelloText.LabelUpper, color = TrelloColors.TextSecondary)
            LinearProgressIndicator(
                progress = { progress },
                color = TrelloColors.Success,
                trackColor = TrelloColors.Sunken,
                modifier = Modifier.fillMaxWidth().height(6.dp).clip(RoundedCornerShape(3.dp)),
            )
            Text("ACTIVITY", style = TrelloText.LabelUpper, color = TrelloColors.TextSecondary)
            // comment rows…
        }
    }
}
```

## 4. Drag-and-Drop Lift

```kotlin
// Use Modifier.pointerInput(detectDragGesturesAfterLongPress) to set isDragging.
// Visual contract: scale 1.03 + navy-tinted Level-3 shadow + a placeholder gap.
@Composable
fun DropPlaceholder() {
    Box(
        Modifier
            .fillMaxWidth()
            .height(48.dp)
            .clip(RoundedCornerShape(8.dp))
            .background(TrelloColors.ShadowNavy.copy(alpha = 0.14f)),
    )
}
```

## 5. Navigation (no bottom bar)

```kotlin
// Trello has NO bottom NavigationBar. The board is the primary surface.
// Use a Navigation graph: a boards list -> a board route that owns the screen
// and scrolls horizontally. Do NOT add Material 3 NavigationBar.

@Composable
fun TrelloNavHost(nav: NavHostController) {
    NavHost(nav, startDestination = "boards") {
        composable("boards") { BoardsListScreen(onOpen = { nav.navigate("board/$it") }) }
        composable("board/{id}") { TrelloBoard(boardName = "Sprint 12", lists = demoLists()) }
    }
}
```

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| Card lift on drag | `animateFloatAsState` scale 1→1.03 `spring()` + `animateDpAsState` elevation 1→24.dp (navy ambient/spot color) |
| Card drop | spring settle back to scale 1 / 1.dp elevation |
| Placeholder gap | `AnimatedVisibility` expand of the `DropPlaceholder` over ~160ms |
| Card detail present | `ModalBottomSheet` default slide (~300ms) over a scrim |
| Composer expand | `animateContentSize()` on the list footer, ~180ms |
| Checklist progress | `animateFloatAsState` feeding `LinearProgressIndicator`, ~200ms |

Haptics: `LocalHapticFeedback.current.performHapticFeedback(HapticFeedbackType.LongPress)` when a card is picked up; `HapticFeedbackType.TextHandleMove` (or a short `Vibrator` one-shot) when it snaps into a new slot.

## 7. Icons

Closest first-party set is `androidx.compose.material:material-icons-extended`. For exact parity, export Trello glyphs as vector drawables and load via `ImageVector.vectorResource(...)`.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Board overflow | `ellipsis` | `Icons.Filled.MoreHoriz` |
| Filter (on board) | `line.3.horizontal.decrease` | `Icons.Filled.FilterList` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Add card / list | `plus` | `Icons.Filled.Add` |
| Close composer | `xmark` | `Icons.Filled.Close` |
| Due date badge | `clock` | `Icons.Filled.Schedule` |
| Checklist badge | `checklist` | `Icons.Filled.CheckBox` |
| Comment badge | `text.bubble` | `Icons.Filled.ChatBubbleOutline` |
| Member | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| Attachment | `paperclip` | `Icons.Filled.AttachFile` |
| Move card | `arrow.right.square` | `Icons.Filled.DriveFileMove` |
| Checklist item | `checkmark.square` / `square` | `Icons.Filled.CheckBox` / `Icons.Outlined.CheckBoxOutlineBlank` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `ModalBottomSheet` + drag gestures are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; on a colored board use light-content system bars (`WindowCompat`), on white screens use dark-content. No bottom bar to inset — the board scrolls beneath the on-board header.
- **Font scaling**: `sp` honors the user's scale — cards grow taller to fit titles. Pin badges and label chips with a fixed `Density` wrapper where layout is rigid.
- **TalkBack**: a card's `contentDescription` should read "Card, <title>, 2 labels, due tomorrow, checklist 3 of 8"; expose "Move card" via `Modifier.semantics { customActions = ... }` since drag is hard for screen readers; mark the board name with `Modifier.semantics { heading() }`.
- **Touch targets**: Material minimum is 48.dp. The whole card is the drag/tap target; give 32.dp on-board chips and 16–24.dp icons a 48.dp hit area via padding; checklist boxes get 48.dp.
- **Contrast**: navy `#172B4D` on white cards passes WCAG AAA; on-board white text requires the scrim — never white on a light photo without `Color.Black.copy(alpha = 0.16f+)` behind it. `#5E6C84` on white passes AA at 12sp+ (validate badges).
- **Color independence**: label colors must not be the only signal — show label text in expanded mode so color-blind users can distinguish them.
- **Reduced motion**: honor `Settings.Global.ANIMATOR_DURATION_SCALE == 0` by replacing the drag scale with an alpha change and instant placeholder.
- **Dynamic color**: do **not** enable Material You `dynamicLightColorScheme()` — Trello's action blue `#0C66E4` and the functional label palette must stay fixed; the board background is user-chosen, not wallpaper-derived.
