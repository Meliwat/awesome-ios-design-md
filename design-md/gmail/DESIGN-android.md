# Gmail (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Gmail's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, `Modifier`s, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? Gmail *is* a Material app — the iOS build is Material You ported onto SF Pro. On Android this guide goes home: a true `FloatingActionButton` and `ExtendedFloatingActionButton`, `SwipeToDismissBox` for archive/delete, a `NavigationBar` with the real Material active-indicator pill, and `sp`/`dp` instead of `pt`. Because it is Material-native, this is one of the few apps where you should **embrace `dynamicColorScheme()`** (see §8).

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` for sender avatar photos.

## 1. Color Tokens

```kotlin
// ui/theme/GmailColors.kt
import androidx.compose.ui.graphics.Color

object GmailColors {
    // Canvas & Surfaces
    val Canvas         = Color(0xFFFFFFFF)
    val Surface        = Color(0xFFF6F8FC)
    val SurfacePressed = Color(0xFFF1F3F4)
    val Divider        = Color(0xFFDADCE0)

    // Text
    val TextPrimary   = Color(0xFF202124)
    val TextSecondary = Color(0xFF5F6368)
    val TextDisabled  = Color(0xFF9AA0A6)
    val TextLink      = Color(0xFF1A73E8)

    // Gmail Red — FAB + destructive ONLY
    val Red        = Color(0xFFD93025)
    val RedPressed = Color(0xFFB3261E)
    val RedSubtle  = Color(0xFFFCE8E6)

    // Google 4-color brand (avatars, chips, Smart Reply)
    val GoogleRed    = Color(0xFFEA4335)
    val GoogleYellow = Color(0xFFFBBC04)
    val GoogleGreen  = Color(0xFF34A853)
    val GoogleBlue   = Color(0xFF4285F4)

    // Semantic
    val StarYellow   = Color(0xFFF5BA18)
    val ArchiveTeal  = Color(0xFF1E8E3E)
    val SelectedRow  = Color(0xFFE8F0FE)
    val SnoozePurple = Color(0xFFA142F4)

    // Dark Mode (Google's standard charcoal — NOT pure black)
    val DarkCanvas      = Color(0xFF202124)
    val DarkSurface1    = Color(0xFF28292C)
    val DarkSurface2    = Color(0xFF303134)
    val DarkDivider     = Color(0xFF3C4043)
    val DarkTextPrimary = Color(0xFFE8EAED)
    val DarkTextSecondary = Color(0xFFBDC1C6)
    val DarkRed         = Color(0xFFF28B82) // FAB shifts lighter on dark
}
```

Wire it into a Material 3 scheme. Gmail's dark mode is `#202124` charcoal — never `#000`.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.dynamicDarkColorScheme
import androidx.compose.material3.dynamicLightColorScheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.ui.platform.LocalContext
import android.os.Build

private val GmailLight = lightColorScheme(
    primary           = GmailColors.Red,         // the FAB
    onPrimary         = Color.White,
    secondary         = GmailColors.TextLink,    // tonal buttons / active tab
    background        = GmailColors.Canvas,
    onBackground      = GmailColors.TextPrimary,
    surface           = GmailColors.Canvas,
    onSurface         = GmailColors.TextPrimary,
    surfaceVariant    = GmailColors.Surface,
    secondaryContainer = GmailColors.SelectedRow, // Material active-indicator pill
    outline           = GmailColors.Divider,
    error             = GmailColors.Red,
)

private val GmailDark = darkColorScheme(
    primary           = GmailColors.DarkRed,
    onPrimary         = GmailColors.DarkCanvas,
    secondary         = GmailColors.TextLink,
    background        = GmailColors.DarkCanvas,
    onBackground      = GmailColors.DarkTextPrimary,
    surface           = GmailColors.DarkSurface1,
    onSurface         = GmailColors.DarkTextPrimary,
    surfaceVariant    = GmailColors.DarkSurface2,
    secondaryContainer = GmailColors.TextLink.copy(alpha = 0.24f),
    outline           = GmailColors.DarkDivider,
    error             = GmailColors.DarkRed,
)

@Composable
fun GmailTheme(
    dark: Boolean = isSystemInDarkTheme(),
    dynamic: Boolean = true,  // Gmail is Material-native — Material You ON by default
    content: @Composable () -> Unit,
) {
    val ctx = LocalContext.current
    val scheme = when {
        dynamic && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S ->
            if (dark) dynamicDarkColorScheme(ctx) else dynamicLightColorScheme(ctx)
        dark -> GmailDark
        else -> GmailLight
    }
    MaterialTheme(colorScheme = scheme, typography = GmailTypography, content = content)
}
```

## 2. Typography

Two families: **Google Sans** (titles, buttons, structural — geometric humanist) and **Roboto** (body, metadata — the Android system default). Drop Google Sans TTFs in `res/font/`; Roboto is the platform fallback so you can rely on the system family or bundle it (Apache 2.0).

```kotlin
// ui/theme/GmailType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val GoogleSans = FontFamily(
    Font(R.font.google_sans_regular, FontWeight.Normal),  // 400
    Font(R.font.google_sans_medium,  FontWeight.Medium),  // 500
    Font(R.font.google_sans_bold,    FontWeight.Bold),    // 700
)
val Roboto = FontFamily(
    Font(R.font.roboto_regular, FontWeight.Normal),
    Font(R.font.roboto_medium,  FontWeight.Medium),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1, line-height = size × ratio)
object GmailText {
    val TitleLarge    = TextStyle(GoogleSans, fontWeight = FontWeight.Normal, fontSize = 28.sp, lineHeight = 34.sp)
    val TitleCompact  = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 20.sp, lineHeight = 24.sp, letterSpacing = 0.15.sp)
    val SectionHeader = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 13.sp, lineHeight = 16.sp, letterSpacing = 0.8.sp)
    val ThreadSubject = TextStyle(GoogleSans, fontWeight = FontWeight.Normal, fontSize = 22.sp, lineHeight = 28.sp)
    val SenderUnread  = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 17.sp)
    val SubjectUnread = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 18.sp)
    val SenderRead    = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 14.sp, lineHeight = 17.sp)
    val Snippet       = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 17.sp)
    val Body          = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 15.sp, lineHeight = 23.sp)
    val FabButton     = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 16.sp, lineHeight = 16.sp, letterSpacing = 0.1.sp)
    val TonalButton   = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 14.sp, letterSpacing = 0.1.sp)
    val SmartReply    = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 14.sp, lineHeight = 14.sp)
    val LabelChip     = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.2.sp)
    val Tab           = TextStyle(GoogleSans, fontWeight = FontWeight.Medium, fontSize = 12.sp, lineHeight = 12.sp, letterSpacing = 0.1.sp)
    val Timestamp     = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 13.sp, lineHeight = 16.sp)
    val Meta          = TextStyle(Roboto,     fontWeight = FontWeight.Normal, fontSize = 12.sp, lineHeight = 16.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val GmailTypography = Typography(
    headlineLarge = GmailText.TitleLarge,
    headlineSmall = GmailText.ThreadSubject,
    titleMedium   = GmailText.SenderUnread,
    bodyMedium    = GmailText.Body,
    labelLarge    = GmailText.TonalButton,
    labelSmall    = GmailText.Tab,
)
```

## 3. Signature Components

### Compose FAB (the Gmail-red squircle)

Use the real Material `FloatingActionButton` / `ExtendedFloatingActionButton`. The Gmail FAB is a 16.dp-rounded squircle — override the default circular shape.

```kotlin
import androidx.compose.animation.AnimatedVisibility
import androidx.compose.animation.expandHorizontally
import androidx.compose.animation.shrinkHorizontally
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Edit
import androidx.compose.material3.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.hapticfeedback.HapticFeedbackType
import androidx.compose.ui.platform.LocalHapticFeedback
import androidx.compose.ui.unit.dp

@Composable
fun ComposeFab(
    extended: Boolean,            // true on tablet / scroll-up — shows "Compose"
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val haptics = LocalHapticFeedback.current
    ExtendedFloatingActionButton(
        onClick = {
            haptics.performHapticFeedback(HapticFeedbackType.LongPress) // ≈ .impact(.medium)
            onClick()
        },
        expanded = extended,
        icon = { Icon(Icons.Filled.Edit, contentDescription = "Compose") },
        text = { Text("Compose", style = GmailText.FabButton) },
        containerColor = MaterialTheme.colorScheme.primary, // Gmail red / dark red
        contentColor = Color.White,
        shape = RoundedCornerShape(16.dp),                  // Material You squircle, NOT a circle
        modifier = modifier,
    )
}
```

Place it in `Scaffold(floatingActionButton = { ComposeFab(...) }, floatingActionButtonPosition = FabPosition.End)` so it floats bottom-right with 16.dp clearance above the bottom bar.

### Email List Row (with swipe actions)

```kotlin
import androidx.compose.material3.SwipeToDismissBox
import androidx.compose.material3.SwipeToDismissBoxValue
import androidx.compose.material3.rememberSwipeToDismissBoxState
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.filled.Archive
import androidx.compose.material.icons.filled.Delete
import androidx.compose.material.icons.filled.Star
import androidx.compose.material.icons.outlined.StarBorder
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.draw.clip
import androidx.compose.ui.text.style.TextOverflow

@Composable
fun EmailRow(
    sender: String,
    subject: String,
    snippet: String,
    timestamp: String,
    isUnread: Boolean,
    isStarred: Boolean,
    avatarColor: Color,
    avatarInitial: String,
    onOpen: () -> Unit,
    onArchive: () -> Unit,
    onDelete: () -> Unit,
) {
    val dismiss = rememberSwipeToDismissBoxState(
        confirmValueChange = {
            when (it) {
                SwipeToDismissBoxValue.StartToEnd -> { onArchive(); true } // swipe →: archive (teal)
                SwipeToDismissBoxValue.EndToStart -> { onDelete(); true }  // swipe ←: delete (red)
                else -> false
            }
        },
        positionalThreshold = { it * 0.4f }, // 40% commit threshold
    )

    SwipeToDismissBox(
        state = dismiss,
        backgroundContent = {
            val (color, icon, align) = when (dismiss.dismissDirection) {
                SwipeToDismissBoxValue.StartToEnd -> Triple(GmailColors.ArchiveTeal, Icons.Filled.Archive, Alignment.CenterStart)
                else -> Triple(GmailColors.Red, Icons.Filled.Delete, Alignment.CenterEnd)
            }
            Box(Modifier.fillMaxSize().background(color).padding(horizontal = 24.dp), contentAlignment = align) {
                Icon(icon, contentDescription = null, tint = Color.White)
            }
        },
    ) {
        Row(
            Modifier
                .fillMaxWidth()
                .height(72.dp)
                .background(MaterialTheme.colorScheme.surface)
                .clickable(onClick = onOpen)
                .padding(horizontal = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(12.dp),
        ) {
            // Avatar — colored fallback with initial
            Box(Modifier.size(36.dp).clip(CircleShape).background(avatarColor), contentAlignment = Alignment.Center) {
                Text(avatarInitial, style = GmailText.SenderUnread.copy(fontSize = 16.sp), color = Color.White)
            }
            Column(Modifier.weight(1f), verticalArrangement = Arrangement.spacedBy(2.dp)) {
                Text(
                    sender,
                    style = if (isUnread) GmailText.SenderUnread else GmailText.SenderRead,
                    color = GmailColors.TextPrimary,
                    maxLines = 1, overflow = TextOverflow.Ellipsis,
                )
                Row {
                    Text(
                        subject,
                        style = if (isUnread) GmailText.SubjectUnread else GmailText.SenderRead,
                        color = GmailColors.TextPrimary,
                        maxLines = 1, overflow = TextOverflow.Ellipsis,
                    )
                    Text(
                        " — $snippet",
                        style = GmailText.Snippet,
                        color = GmailColors.TextSecondary,
                        maxLines = 1, overflow = TextOverflow.Ellipsis,
                    )
                }
            }
            Column(horizontalAlignment = Alignment.End, verticalArrangement = Arrangement.spacedBy(4.dp)) {
                Text(
                    timestamp,
                    style = GmailText.Timestamp,
                    color = if (isUnread) GmailColors.TextPrimary else GmailColors.TextSecondary,
                )
                Icon(
                    if (isStarred) Icons.Filled.Star else Icons.Outlined.StarBorder,
                    contentDescription = if (isStarred) "Starred" else "Not starred",
                    tint = if (isStarred) GmailColors.StarYellow else GmailColors.TextSecondary,
                    modifier = Modifier.size(18.dp),
                )
            }
        }
    }
}

// Avatar color — deterministic from sender email's first letter
fun gmailAvatarColor(email: String): Pair<Color, String> {
    val initial = email.firstOrNull()?.uppercaseChar() ?: '?'
    val color = when (initial) {
        in 'A'..'F' -> GmailColors.GoogleRed
        in 'G'..'L' -> GmailColors.GoogleYellow
        in 'M'..'R' -> GmailColors.GoogleGreen
        else        -> GmailColors.GoogleBlue
    }
    return color to initial.toString()
}
```

### Tonal Button (Reply / Reply all / Forward)

```kotlin
@Composable
fun TonalButton(text: String, icon: androidx.compose.ui.graphics.vector.ImageVector, onClick: () -> Unit) {
    FilledTonalButton(
        onClick = onClick,
        shape = CircleShape, // 500pt pill
        colors = ButtonDefaults.filledTonalButtonColors(
            containerColor = GmailColors.Surface,
            contentColor = GmailColors.TextLink, // the one place link blue is button text
        ),
        contentPadding = PaddingValues(horizontal = 24.dp, vertical = 10.dp),
    ) {
        Icon(icon, contentDescription = null, modifier = Modifier.size(18.dp))
        Spacer(Modifier.width(6.dp))
        Text(text, style = GmailText.TonalButton)
    }
}
```

### Smart Reply Chip + Label Chip

```kotlin
@Composable
fun SmartReplyRow(suggestions: List<String>, onPick: (String) -> Unit) {
    val haptics = LocalHapticFeedback.current
    Row(
        Modifier.horizontalScroll(rememberScrollState()),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        suggestions.forEach { text ->
            AssistChip(
                onClick = {
                    haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                    onPick(text)
                },
                label = { Text(text, style = GmailText.SmartReply) },
                shape = CircleShape,
                colors = AssistChipDefaults.assistChipColors(labelColor = GmailColors.TextLink),
                border = AssistChipDefaults.assistChipBorder(enabled = true, borderColor = GmailColors.Divider),
            )
        }
    }
}

@Composable
fun LabelChip(label: String, color: Color) {
    Box(
        Modifier
            .clip(CircleShape)
            .background(color.copy(alpha = 0.1f))
            .border(1.dp, color, CircleShape)
            .padding(horizontal = 10.dp, vertical = 4.dp),
    ) {
        Text(label, style = GmailText.LabelChip, color = color)
    }
}
```

## 4. Gmail-Specific Feature: Compose FAB Scroll Behavior + Swipe Actions

The FAB is THE Gmail. It must always be reachable: on scroll-down it collapses to icon-only (56.dp); on scroll-up it re-expands to `ExtendedFloatingActionButton` with the "Compose" label. Drive `extended` from a `NestedScrollConnection` so width animates over ~200ms (the `ExtendedFloatingActionButton` `expanded` flag animates this for you).

```kotlin
import androidx.compose.ui.input.nestedscroll.NestedScrollConnection
import androidx.compose.ui.input.nestedscroll.nestedScroll
import androidx.compose.ui.geometry.Offset

@Composable
fun InboxScreen(emails: List<EmailModel>) {
    var fabExtended by remember { mutableStateOf(true) }
    val scrollConn = remember {
        object : NestedScrollConnection {
            override fun onPreScroll(available: Offset, source: androidx.compose.ui.input.nestedscroll.NestedScrollSource): Offset {
                if (available.y < -4f) fabExtended = false // scrolling down → collapse
                if (available.y >  4f) fabExtended = true  // scrolling up → expand
                return Offset.Zero
            }
        }
    }

    Scaffold(
        floatingActionButton = { ComposeFab(extended = fabExtended, onClick = { /* open compose */ }) },
        floatingActionButtonPosition = FabPosition.End,
    ) { padding ->
        LazyColumn(Modifier.padding(padding).nestedScroll(scrollConn)) {
            items(emails, key = { it.id }) { e ->
                val (avColor, initial) = gmailAvatarColor(e.senderEmail)
                EmailRow(
                    sender = e.sender, subject = e.subject, snippet = e.snippet,
                    timestamp = e.timestamp, isUnread = e.isUnread, isStarred = e.isStarred,
                    avatarColor = avColor, avatarInitial = initial,
                    onOpen = { /* open thread */ },
                    onArchive = { /* archive + animate row height 72 → 0 */ },
                    onDelete = { /* delete */ },
                )
            }
        }
    }
}
```

`SwipeToDismissBox` (in `EmailRow`) is the Material-idiomatic equivalent of iOS `.swipeActions`: swipe-right reveals the teal archive pane, swipe-left the red delete pane, with a 40% positional commit threshold and an automatic spring snap-back below it. On archive, animate the row's height `72.dp → 0` via `animateContentSize()` so adjacent rows shift up.

## 5. Navigation

Use Material 3 `NavigationBar` — Gmail's iOS active-indicator pill is *literally* the Material You `secondaryContainer` pill, so this is the one app where the stock component matches the source exactly. 2–4 configurable tabs: Mail, Chat, Spaces, Meet. Android has no live blur, so the bar is the opaque canvas surface with a 0.5dp top divider.

```kotlin
@Composable
fun GmailBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    Column {
        HorizontalDivider(color = GmailColors.Divider, thickness = 0.5.dp)
        NavigationBar(containerColor = MaterialTheme.colorScheme.surface, tonalElevation = 0.dp) {
            val items = listOf(
                "Mail"   to Icons.Filled.Email,
                "Chat"   to Icons.Filled.ChatBubble,
                "Spaces" to Icons.Filled.Groups,
                "Meet"   to Icons.Filled.Videocam,
            )
            items.forEachIndexed { i, (label, icon) ->
                NavigationBarItem(
                    selected = selected == i,
                    onClick = { onSelect(i) },
                    icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(24.dp)) },
                    label = { Text(label, style = GmailText.Tab) },
                    colors = NavigationBarItemDefaults.colors(
                        selectedIconColor   = GmailColors.TextLink,
                        selectedTextColor   = GmailColors.TextLink,
                        unselectedIconColor = GmailColors.TextSecondary,
                        unselectedTextColor = GmailColors.TextSecondary,
                        indicatorColor      = GmailColors.SelectedRow, // the #E8F0FE Material pill — keep it
                    ),
                )
            }
        }
    }
}
```

The top app bar uses `TopAppBar` with a leading hamburger (opens a `ModalNavigationDrawer` of mailboxes), a centered pill search field (`#F6F8FC`, 500pt radius), and a trailing 28.dp account avatar.

## 6. Motion

| Moment | Compose recipe |
|--------|----------------|
| FAB tap | `FloatingActionButton` ripple + `HapticFeedbackType.LongPress` (≈ `.impact(.medium)`) |
| FAB scroll collapse/expand | `ExtendedFloatingActionButton(expanded = …)` animates width 120 → 56.dp over ~200ms |
| Swipe action | `SwipeToDismissBox`, 40% positional threshold; below it springs back ~250ms |
| Star tap | `Animatable` 1.0 → 1.2 → 1.0 `spring`, 300ms; `HapticFeedbackType.LongPress` (≈ `.impact(.light)`) |
| Archive completion | row `animateContentSize()` height 72 → 0 over 300ms; adjacent rows shift up |
| Smart Reply pick | chip ripple + text `fadeIn` into compose box, 200ms |

```kotlin
// Star toggle bounce
@Composable
fun StarToggle(starred: Boolean, onToggle: () -> Unit) {
    val scale = remember { Animatable(1f) }
    val haptics = LocalHapticFeedback.current
    val scope = rememberCoroutineScope()
    Icon(
        if (starred) Icons.Filled.Star else Icons.Outlined.StarBorder,
        contentDescription = if (starred) "Starred" else "Not starred",
        tint = if (starred) GmailColors.StarYellow else GmailColors.TextSecondary,
        modifier = Modifier
            .size(18.dp)
            .scale(scale.value)
            .clickable {
                scope.launch {
                    scale.animateTo(1.2f, tween(120)); scale.animateTo(1f, spring(dampingRatio = 0.5f))
                }
                haptics.performHapticFeedback(HapticFeedbackType.LongPress)
                onToggle()
            },
    )
}
```

Haptics: prefer `LocalHapticFeedback`. `HapticFeedbackType.LongPress` approximates iOS impact; for richer control use `LocalView.current.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (API 30+) or a `Vibrator` `VibrationEffect.createOneShot(12, …)`.

## 7. Icons

Gmail uses Material Symbols natively; `androidx.compose.material:material-icons-extended` is the first-party match. The Gmail "M" logo and the pull-to-refresh swirl ship as vector drawables (`ImageVector.vectorResource(R.drawable.…)`).

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Compose FAB | `square.and.pencil` | `Icons.Filled.Edit` |
| Mail (tab) | `envelope` / `.fill` | `Icons.Filled.Email` |
| Chat (tab) | `bubble.left` | `Icons.Filled.ChatBubble` |
| Spaces (tab) | `person.3` | `Icons.Filled.Groups` |
| Meet (tab) | `video` | `Icons.Filled.Videocam` |
| Search | `magnifyingglass` | `Icons.Filled.Search` |
| Menu hamburger | `line.3.horizontal` | `Icons.Filled.Menu` |
| Archive | `archivebox` | `Icons.Filled.Archive` |
| Delete | `trash` | `Icons.Filled.Delete` |
| Mark unread | `envelope.badge` | `Icons.Filled.MarkEmailUnread` |
| Snooze | `clock` | `Icons.Filled.Schedule` |
| Star | `star` / `.fill` | `Icons.Outlined.StarBorder` / `Icons.Filled.Star` |
| Reply | `arrowshape.turn.up.left` | `Icons.AutoMirrored.Filled.Reply` |
| Reply all | `arrowshape.turn.up.left.2` | `Icons.AutoMirrored.Filled.ReplyAll` |
| Forward | `arrowshape.turn.up.right` | `Icons.AutoMirrored.Filled.Forward` |
| Attachment | `paperclip` | `Icons.Filled.AttachFile` |
| Send | `paperplane.fill` | `Icons.AutoMirrored.Filled.Send` |
| Back | `chevron.left` | `Icons.AutoMirrored.Filled.ArrowBack` |

## 8. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; `SwipeToDismissBox` and modern motion are comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()` in the `Activity`; the white canvas wants dark status-bar icons (charcoal `#202124` in dark wants light icons). Use `Scaffold` insets so the FAB floats 16.dp above the gesture nav and the top bar clears the camera cutout.
- **Font scaling**: `sp` honors the user's font scale automatically — keep it on email body, subjects, snippets, thread content. Pin layout-sensitive text (13sp section headers, 13sp timestamps, 12sp tab labels, 14sp Smart Reply / 12sp label chips) by deriving size from `dp` or wrapping in `CompositionLocalProvider(LocalDensity provides Density(density, fontScale = 1f))` so the fixed 72.dp rows never clip.
- **TalkBack**: collapse each email row into one node with `Modifier.semantics(mergeDescendants = true)` — "Sarah Chen, unread, Q4 planning update. Wanted to share the latest thinking. 10:42 AM. Not starred. Double-tap to open." Expose archive/delete as custom accessibility actions since swipe is not discoverable via TalkBack.
- **Touch targets**: Material guidance is 48.dp minimum. The FAB (56.dp) is well clear; ensure the 18.dp star and 24.dp top-bar icons sit in ≥48.dp hit areas via padding.
- **Contrast**: `#202124` on `#FFFFFF` exceeds AAA. `#5F6368` secondary on white meets AA at 13sp+ — boost the snippet/timestamp toward `#3C4043` when the system "increase contrast" setting (or `isHighTextContrastEnabled`) is on. In dark mode, switch the layered shadows for a tonal `surfaceColorAtElevation()` tint since shadows vanish on `#202124` (Material's dark-mode depth model).
- **Dynamic color**: **RECOMMENDED — embrace `dynamicColorScheme()`.** Gmail is a Material-native Google app; on Android 12+ it should adopt Material You and tint chrome from the user's wallpaper. Keep only the *semantic* anchors fixed: the Gmail-red FAB (`primary`), the Google 4-color avatar palette, the star yellow `#F5BA18`, and the teal/red swipe panes — these carry meaning and must not drift with the wallpaper. Everything else (surfaces, the active-indicator pill, tonal-button container) should flow from `dynamicLightColorScheme()` / `dynamicDarkColorScheme()`.
