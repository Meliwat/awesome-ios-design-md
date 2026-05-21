# GitHub (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports GitHub's visual language (the Primer design system) to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the repo home + code browser, the contributions heatmap, the diff viewer, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral (Primer is cross-platform by design). This file keeps the *visual* identity — Primer dark default `#0D1117`, the one accent blue, the reserved green, semantic state colors, the iconic heatmap ramp, monospace for all code/SHAs/paths — while making everything idiomatic Android: a `NavigationBar` instead of a UITabBar, `LazyColumn` diffs, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, [Coil](https://coil-kt.github.io/coil/) `2.6+` for avatars. **No Material You / dynamic color** — GitHub's Primer palette and its semantic state colors must hold regardless of wallpaper. Dark is the default; light is a first-class peer.

## 1. Color Tokens

```kotlin
// ui/theme/GitHubColors.kt
import androidx.compose.ui.graphics.Color

object GH {
    // ---- Dark (default) ----
    val Canvas         = Color(0xFF0D1117) // blue-tinted near-black
    val CanvasSubtle   = Color(0xFF161B22) // raised surfaces, card headers, heatmap L0
    val CanvasInset    = Color(0xFF21262D) // default button, code-block bg, count pills
    val SurfacePressed = Color(0xFF30363D)
    val BorderDefault  = Color(0xFF30363D)
    val BorderMuted    = Color(0xFF21262D)

    val Fg          = Color(0xFFE6EDF3)
    val FgMuted     = Color(0xFF7D8590)
    val FgSubtle    = Color(0xFF6E7681)
    val FgOnEmphasis = Color(0xFFFFFFFF)

    val AccentFg       = Color(0xFF2F81F7) // links, owner/name, active
    val AccentEmphasis = Color(0xFF1F6FEB)
    val BtnPrimary     = Color(0xFF238636) // RESERVED — Code / Merge only
    val BtnPrimaryHover = Color(0xFF2EA043)
    val BtnDanger      = Color(0xFFDA3633)

    // Semantic state (fixed meaning)
    val Open      = Color(0xFF3FB950) // open issue/PR, passing
    val Merged    = Color(0xFFA371F7) // merged PR (purple = done)
    val Closed    = Color(0xFFF85149) // closed, failing, delete
    val Draft     = Color(0xFF6E7681) // draft/neutral
    val Attention = Color(0xFFD29922) // pending/warning
    val Sponsor   = Color(0xFFDB61A2)

    val TabUnderline = Color(0xFFF78166) // orange-coral active underline

    val DiffAddBg = Color(0xFF3FB950).copy(alpha = 0.15f)
    val DiffDelBg = Color(0xFFF85149).copy(alpha = 0.15f)

    // ---- Light (first-class peer) ----
    val LCanvas        = Color(0xFFFFFFFF)
    val LCanvasSubtle  = Color(0xFFF6F8FA)
    val LBorderDefault = Color(0xFFD0D7DE)
    val LFg            = Color(0xFF1F2328)
    val LFgMuted       = Color(0xFF656D76)
    val LAccentFg      = Color(0xFF0969DA)
    val LBtnPrimary    = Color(0xFF1F883D)
    val LOpen          = Color(0xFF1A7F37)
    val LMerged        = Color(0xFF8250DF)
    val LClosed        = Color(0xFFCF222E)
    val LTabUnderline  = Color(0xFFFD8C73)

    // Contributions heatmap ramp — the iconic GitHub visual (never alter)
    val HeatDark  = listOf(Color(0xFF161B22), Color(0xFF0E4429), Color(0xFF006D32), Color(0xFF26A641), Color(0xFF39D353))
    val HeatLight = listOf(Color(0xFFEBEDF0), Color(0xFF9BE9A8), Color(0xFF40C463), Color(0xFF30A14E), Color(0xFF216E39))
}
```

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.runtime.Composable

private val GHDark = darkColorScheme(
    primary        = GH.BtnPrimary,   // reserved green (Code / Merge)
    onPrimary      = GH.FgOnEmphasis,
    background     = GH.Canvas,
    onBackground   = GH.Fg,
    surface        = GH.CanvasSubtle,
    onSurface      = GH.Fg,
    surfaceVariant = GH.CanvasInset,
    outline        = GH.BorderDefault,
    error          = GH.Closed,
)

private val GHLight = lightColorScheme(
    primary        = GH.LBtnPrimary,
    onPrimary      = GH.FgOnEmphasis,
    background     = GH.LCanvas,
    onBackground   = GH.LFg,
    surface        = GH.LCanvasSubtle,
    onSurface      = GH.LFg,
    surfaceVariant = GH.LCanvasSubtle,
    outline        = GH.LBorderDefault,
    error          = GH.LClosed,
)

@Composable
fun GitHubTheme(dark: Boolean = isSystemInDarkTheme(), content: @Composable () -> Unit) =
    MaterialTheme(colorScheme = if (dark) GHDark else GHLight, typography = GHTypography, content = content)
```

## 2. Typography (Material 3)

Mona Sans (GitHub's variable grotesque, SIL OFL) for UI; a monospace for **all** code, SHAs, file/branch names, diffs. Drop both in `res/font/`. Weight + size establish hierarchy (one sans family); the monospace contract is functional, not stylistic.

```kotlin
// ui/theme/GHType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val MonaSans = FontFamily(
    Font(R.font.mona_sans_regular,   FontWeight.Normal),
    Font(R.font.mona_sans_medium,    FontWeight.Medium),
    Font(R.font.mona_sans_semibold,  FontWeight.SemiBold),
    Font(R.font.mona_sans_bold,      FontWeight.Bold),
    Font(R.font.mona_sans_extrabold, FontWeight.ExtraBold),
)
val Mono = FontFamily(
    Font(R.font.jetbrains_mono_regular,  FontWeight.Normal),
    Font(R.font.jetbrains_mono_semibold, FontWeight.SemiBold),
)

// Named ramp — mirrors DESIGN.md §3 exactly (pt → sp 1:1)
object GHText {
    val Display    = TextStyle(MonaSans, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.4).sp)
    val RepoTitle  = TextStyle(MonaSans, fontWeight = FontWeight.Bold,      fontSize = 24.sp, lineHeight = 29.sp, letterSpacing = (-0.3).sp)
    val Section    = TextStyle(MonaSans, fontWeight = FontWeight.SemiBold,  fontSize = 20.sp, lineHeight = 26.sp, letterSpacing = (-0.2).sp)
    val Subtitle   = TextStyle(MonaSans, fontWeight = FontWeight.SemiBold,  fontSize = 16.sp, lineHeight = 22.sp)
    val Body       = TextStyle(MonaSans, fontWeight = FontWeight.Normal,    fontSize = 14.sp, lineHeight = 21.sp)
    val BodyStrong = TextStyle(MonaSans, fontWeight = FontWeight.SemiBold,  fontSize = 14.sp, lineHeight = 21.sp)
    val Code       = TextStyle(Mono,     fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 19.sp)
    val CodeInline = TextStyle(Mono,     fontWeight = FontWeight.Normal,    fontSize = 13.sp, lineHeight = 18.sp)
    val Sha        = TextStyle(Mono,     fontWeight = FontWeight.SemiBold,  fontSize = 12.sp, lineHeight = 12.sp)
    val Metadata   = TextStyle(MonaSans, fontWeight = FontWeight.Normal,    fontSize = 12.sp, lineHeight = 17.sp)
    val Label      = TextStyle(MonaSans, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 12.sp)
    val CountPill  = TextStyle(MonaSans, fontWeight = FontWeight.SemiBold,  fontSize = 11.sp, lineHeight = 11.sp)
    val Tab        = TextStyle(MonaSans, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val Caption    = TextStyle(MonaSans, fontWeight = FontWeight.Normal,    fontSize = 11.sp, lineHeight = 14.sp)
}

val GHTypography = Typography(
    headlineLarge = GHText.Display,
    headlineMedium = GHText.RepoTitle,
    titleLarge    = GHText.Section,
    titleMedium   = GHText.Subtitle,
    bodyMedium    = GHText.Body,
    labelSmall    = GHText.Tab,
)
```

## 3. Signature Components

### Repo Header (`owner / name` + stats + tab strip)

```kotlin
import androidx.compose.foundation.*
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

@Composable
fun RepoHeader(
    owner: String, name: String, description: String,
    language: String, languageColor: Color, stars: String, forks: String,
    tabs: List<String> = listOf("Code", "Issues", "Pull requests", "Actions"),
    activeTab: Int, onTab: (Int) -> Unit,
) {
    Column(Modifier.fillMaxWidth().background(GH.Canvas).padding(top = 12.dp)) {
        Row(Modifier.padding(horizontal = 16.dp), verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(6.dp)) {
            Icon(GitHubIcons.Repo, null, tint = GH.FgMuted, modifier = Modifier.size(17.dp))
            Text(owner, style = GHText.Subtitle, color = GH.AccentFg)
            Text("/", style = GHText.Subtitle, color = GH.FgMuted)
            Text(name, style = GHText.Subtitle.copy(fontWeight = FontWeight.Bold), color = GH.AccentFg)
        }
        Text(description, style = GHText.Body, color = GH.FgMuted, modifier = Modifier.padding(horizontal = 16.dp, vertical = 6.dp))
        Row(Modifier.padding(horizontal = 16.dp, vertical = 4.dp), verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(16.dp)) {
            Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(5.dp)) {
                Box(Modifier.size(11.dp).clip(CircleShape).background(languageColor))
                Text(language, style = GHText.Metadata, color = GH.FgMuted)
            }
            StatGlyph(GitHubIcons.Star, stars)
            StatGlyph(GitHubIcons.Fork, forks)
        }
        ScrollableTabRow(
            selectedTabIndex = activeTab,
            containerColor = GH.Canvas,
            edgePadding = 16.dp,
            divider = { HorizontalDivider(thickness = 1.dp, color = GH.BorderDefault) },
            indicator = { pos ->
                TabRowDefaults.SecondaryIndicator(
                    Modifier.tabIndicatorOffset(pos[activeTab]),
                    height = 2.dp, color = GH.TabUnderline, // orange-coral
                )
            },
        ) {
            tabs.forEachIndexed { i, t ->
                Tab(selected = i == activeTab, onClick = { onTab(i) }) {
                    Text(
                        t, style = GHText.Body.copy(fontWeight = if (i == activeTab) FontWeight.SemiBold else FontWeight.Normal),
                        color = if (i == activeTab) GH.Fg else GH.FgMuted,
                        modifier = Modifier.padding(vertical = 12.dp),
                    )
                }
            }
        }
    }
}

@Composable
private fun StatGlyph(icon: ImageVector, value: String) {
    Row(verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
        Icon(icon, null, tint = GH.FgMuted, modifier = Modifier.size(13.dp))
        Text(value, style = GHText.Metadata, color = GH.FgMuted)
    }
}
```

### Branch Pill + Latest-Commit Strip

```kotlin
import androidx.compose.foundation.shape.RoundedCornerShape
import coil.compose.AsyncImage

@Composable
fun BranchPill(branch: String) {
    Row(
        Modifier
            .clip(RoundedCornerShape(6.dp))
            .background(GH.CanvasSubtle)
            .border(1.dp, GH.BorderDefault, RoundedCornerShape(6.dp))
            .padding(horizontal = 12.dp, vertical = 7.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(6.dp),
    ) {
        Icon(GitHubIcons.Branch, null, tint = GH.FgMuted, modifier = Modifier.size(13.dp))
        Text(branch, style = GHText.Sha, color = GH.Fg) // monospace
    }
}

@Composable
fun LatestCommit(avatarUrl: String, message: String, sha: String, whenText: String) {
    Row(
        Modifier
            .fillMaxWidth()
            .background(GH.CanvasSubtle)
            .drawBottomBorder(GH.BorderDefault)
            .padding(12.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
    ) {
        AsyncImage(model = avatarUrl, contentDescription = null, modifier = Modifier.size(18.dp).clip(CircleShape))
        Text(message, style = GHText.Body, color = GH.Fg, maxLines = 1, modifier = Modifier.weight(1f))
        Text(sha, style = GHText.Caption.copy(fontFamily = Mono), color = GH.FgMuted) // monospace SHA
        Text(whenText, style = GHText.Caption, color = GH.FgMuted)
    }
}
```

### Code Browser (sticky path breadcrumb + monospace blob)

```kotlin
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.itemsIndexed
import androidx.compose.foundation.horizontalScroll
import androidx.compose.foundation.rememberScrollState

@Composable
fun CodeBrowser(path: String, lines: List<String>) {
    Column(Modifier.fillMaxSize().background(GH.Canvas)) {
        // Sticky path breadcrumb — monospace
        Box(
            Modifier.fillMaxWidth().background(GH.CanvasSubtle).drawBottomBorder(GH.BorderDefault).padding(horizontal = 16.dp, vertical = 10.dp)
        ) { Text(path, style = GHText.Code, color = GH.FgMuted) }

        LazyColumn(Modifier.fillMaxWidth().background(GH.CanvasSubtle).horizontalScroll(rememberScrollState())) {
            itemsIndexed(lines) { i, ln ->
                Row(Modifier.padding(horizontal = 12.dp, vertical = 1.dp)) {
                    Text("${i + 1}", style = GHText.Code, color = GH.FgSubtle, modifier = Modifier.widthIn(min = 36.dp))
                    Spacer(Modifier.width(16.dp))
                    Text(ln, style = GHText.Code, color = GH.Fg)
                }
            }
        }
    }
}
```

### State Pill (open / merged / closed / draft) — color carries meaning

```kotlin
enum class IssueState(val color: Color, val icon: ImageVector, val label: String) {
    Open(GH.Open, GitHubIcons.IssueOpened, "Open"),
    Merged(GH.Merged, GitHubIcons.Merge, "Merged"),
    Closed(GH.Closed, GitHubIcons.IssueClosed, "Closed"),
    Draft(GH.Draft, GitHubIcons.Draft, "Draft"),
}

@Composable
fun StatePill(state: IssueState) {
    Row(
        Modifier.clip(RoundedCornerShape(999.dp)).background(state.color).padding(horizontal = 12.dp, vertical = 5.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(5.dp),
    ) {
        Icon(state.icon, null, tint = Color.White, modifier = Modifier.size(12.dp))
        Text(state.label, style = GHText.Label, color = Color.White) // glyph + label so it's not color-only
    }
}
```

### Contributions Heatmap (signature)

```kotlin
@Composable
fun ContributionsHeatmap(weeks: List<List<Int>> /* [week][day] -> 0..4 */) {
    Column {
        Row(horizontalArrangement = Arrangement.spacedBy(3.dp)) {
            weeks.forEach { week ->
                Column(verticalArrangement = Arrangement.spacedBy(3.dp)) {
                    week.forEach { level ->
                        Box(
                            Modifier
                                .size(11.dp)
                                .clip(RoundedCornerShape(2.dp))
                                .background(GH.HeatDark[level])
                                .then(if (level == 0) Modifier.border(1.dp, Color(0xFF6E7681).copy(alpha = 0.15f), RoundedCornerShape(2.dp)) else Modifier)
                        )
                    }
                }
            }
        }
        Row(Modifier.padding(top = 8.dp), verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(4.dp)) {
            Text("Less", style = GHText.Caption, color = GH.FgMuted)
            GH.HeatDark.forEach { c -> Box(Modifier.size(11.dp).clip(RoundedCornerShape(2.dp)).background(c)) }
            Text("More", style = GHText.Caption, color = GH.FgMuted)
        }
    }
}
```

### Diff Viewer (additions green wash / deletions red wash)

```kotlin
data class DiffLine(val kind: String /* add | del | ctx */, val text: String)

@Composable
fun DiffViewer(file: String, adds: Int, dels: Int, lines: List<DiffLine>) {
    Column(Modifier.clip(RoundedCornerShape(6.dp)).border(1.dp, GH.BorderDefault, RoundedCornerShape(6.dp))) {
        Row(
            Modifier.fillMaxWidth().background(GH.CanvasSubtle).drawBottomBorder(GH.BorderDefault).padding(10.dp),
            verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.spacedBy(8.dp),
        ) {
            Text(file, style = GHText.Code, color = GH.Fg, modifier = Modifier.weight(1f)) // monospace filename
            Text("+$adds", style = GHText.Code, color = GH.Open)
            Text("-$dels", style = GHText.Code, color = GH.Closed)
        }
        Column(Modifier.fillMaxWidth().background(GH.CanvasSubtle).horizontalScroll(rememberScrollState())) {
            lines.forEach { l ->
                val bg = when (l.kind) { "add" -> GH.DiffAddBg; "del" -> GH.DiffDelBg; else -> Color.Transparent }
                Row(Modifier.fillMaxWidth().background(bg)) {
                    Text(if (l.kind == "add") "+" else if (l.kind == "del") "-" else " ", style = GHText.Code, color = GH.FgSubtle, modifier = Modifier.width(28.dp))
                    Text(l.text, style = GHText.Code, color = GH.Fg, modifier = Modifier.padding(end = 16.dp))
                }
            }
        }
    }
}
```

### Buttons (reserved green + default)

```kotlin
// RESERVED — only for "Code" download and "Merge pull request"
@Composable
fun GreenButton(title: String, icon: ImageVector, onClick: () -> Unit) {
    Button(
        onClick = onClick,
        shape = RoundedCornerShape(6.dp),
        colors = ButtonDefaults.buttonColors(containerColor = GH.BtnPrimary, contentColor = Color.White),
        contentPadding = PaddingValues(horizontal = 16.dp, vertical = 10.dp),
    ) {
        Icon(icon, null, modifier = Modifier.size(16.dp))
        Spacer(Modifier.width(8.dp))
        Text(title, style = GHText.BodyStrong, color = Color.White)
    }
}

@Composable
fun DefaultButton(title: String, onClick: () -> Unit) {
    OutlinedButton(
        onClick = onClick,
        shape = RoundedCornerShape(6.dp),
        border = BorderStroke(1.dp, GH.BorderDefault),
        colors = ButtonDefaults.outlinedButtonColors(containerColor = GH.CanvasInset, contentColor = GH.Fg),
        contentPadding = PaddingValues(horizontal = 16.dp, vertical = 10.dp),
    ) { Text(title, style = GHText.BodyStrong) }
}
```

> `drawBottomBorder` is a tiny helper: `fun Modifier.drawBottomBorder(c: Color) = drawBehind { drawLine(c, Offset(0f, size.height), Offset(size.width, size.height), 1.dp.toPx()) }`.

## 4. Navigation (Bottom Bar)

4 destinations, `Canvas` container, 1dp top `BorderMuted`, no tint pill; active `Fg`, inactive `FgMuted`. (Android has no live blur for nav bars — use the solid `#0D1117`; the iOS translucency is cosmetic.)

```kotlin
@Composable
fun GitHubBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    Column {
        HorizontalDivider(thickness = 1.dp, color = GH.BorderMuted)
        NavigationBar(containerColor = GH.Canvas, tonalElevation = 0.dp) {
            val items = listOf(
                "Home"          to GitHubIcons.Home,
                "Notifications" to GitHubIcons.Bell,
                "Explore"       to GitHubIcons.Telescope,
                "Profile"       to GitHubIcons.Person,
            )
            items.forEachIndexed { i, (label, icon) ->
                NavigationBarItem(
                    selected = selected == i,
                    onClick = { onSelect(i) },
                    icon = { Icon(icon, label, modifier = Modifier.size(22.dp)) },
                    label = { Text(label, style = GHText.Tab) },
                    colors = NavigationBarItemDefaults.colors(
                        selectedIconColor = GH.Fg,
                        selectedTextColor = GH.Fg,
                        unselectedIconColor = GH.FgMuted,
                        unselectedTextColor = GH.FgMuted,
                        indicatorColor = Color.Transparent, // no Material pill — GitHub has none
                    ),
                )
            }
        }
    }
}
```

## 5. Motion

GitHub motion is utilitarian — no gratuitous animation; state changes are crisp.

| Moment | Compose recipe |
|--------|----------------|
| Tab-strip underline | `ScrollableTabRow` indicator `tabIndicatorOffset` slides `tween(200)` |
| Merge button → "Merged" | `Crossfade(targetState = merged, tween(200))` swapping label + container `#238636` → `#A371F7` |
| Star count-up | `animateIntAsState(stars, tween(250))` |
| Diff "Expand hidden" | `AnimatedVisibility(expandVertically(tween(180)))` to reveal collapsed context |
| List/tree expand | `animateContentSize(tween(180))` on the file-tree folder |
| Pull-to-refresh | `PullToRefreshBox` tinted `FgMuted` |

```kotlin
// Merge confirm — the one expressive moment
Crossfade(targetState = isMerged, animationSpec = tween(200), label = "merge") { merged ->
    if (merged) StatePill(IssueState.Merged) else GreenButton("Merge pull request", GitHubIcons.Merge) { /* merge */ }
}
```

Haptics: `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.LongPress)` on Merge confirm and Star toggle only — GitHub is not a haptic-heavy app. No haptics on scroll, tab switch, or navigation.

## 6. Icons

GitHub ships **Octicons**. Compose has no bundled Octicons, so either vector-drawable the official Octicons SVGs into `res/drawable` and expose them, or map to the closest `material-icons-extended`. Define a `GitHubIcons` object so call sites stay stable:

| Purpose | Octicon (iOS) | Compose source |
|---------|---------------|----------------|
| Repo | `repo` | Octicon `repo` vector, or `Icons.Filled.Book` |
| Code / `<>` | `code` | Octicon `code`, or `Icons.Filled.Code` |
| Issue opened | `issue-opened` | Octicon, or `Icons.Outlined.Adjust` |
| Issue closed | `issue-closed` | Octicon, or `Icons.Filled.CheckCircle` |
| Pull request | `git-pull-request` | Octicon, or `Icons.Filled.CallMerge` |
| Merge | `git-merge` | Octicon, or `Icons.Filled.Merge` |
| Draft PR | `git-pull-request-draft` | Octicon, or `Icons.Outlined.Circle` |
| Branch | `git-branch` | Octicon, or `Icons.Filled.AltRoute` |
| Commit | `git-commit` | Octicon, or `Icons.Filled.Commit` |
| Star | `star` / `star-fill` | Octicon, or `Icons.Filled.Star` |
| Fork | `repo-forked` | Octicon, or `Icons.Filled.ForkRight` |
| Folder (tree) | `file-directory-fill` | Octicon, or `Icons.Filled.Folder` |
| File (tree) | `file` | Octicon, or `Icons.Filled.InsertDriveFile` |
| Home (tab) | `home` | Octicon, or `Icons.Filled.Home` |
| Notifications (tab) | `bell` | Octicon, or `Icons.Filled.Notifications` |
| Explore (tab) | `telescope` | Octicon, or `Icons.Filled.Explore` |
| Profile (tab) | `person` | Octicon, or `Icons.Filled.Person` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (`compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`). `Coil` for avatars; vector-drawable the Octicons for a faithful glyph set.
- **Edge-to-edge**: `enableEdgeToEdge()`; dark canvas wants light-content system bars (dark-content in light theme). The code browser must scroll horizontally — wrap the blob in `horizontalScroll`.
- **No dynamic color**: do **not** call `dynamicColorScheme()` — Primer's palette and the semantic state colors (open/merged/closed/draft) must be exact regardless of wallpaper; Material You would corrupt the "green = merge" learned affordance.
- **Reserved green**: `GH.BtnPrimary` (`#238636`) is ONLY the "Code" download and "Merge pull request" buttons — every other action uses `DefaultButton`. Never tint another control green.
- **Monospace contract**: code, SHAs, file/branch names, and diffs MUST use `Mono` — never substitute `MonaSans`; glyph distinction (`0`/`O`, `1`/`l`) is an accessibility and correctness requirement for developers.
- **Heatmap ramp**: never alter the 5 stops (`#161B22 → #0E4429 → #006D32 → #26A641 → #39D353` dark) — it's the single most recognizable GitHub visual.
- **Font scaling**: `sp` honors the user's scale — keep it on titles, body, and code (code stays monospace as it scales). Pin layout-sensitive text (10sp tab labels, count pills, SHAs in tables/trees) via `dp` or a fixed-`fontScale` `CompositionLocalProvider`.
- **TalkBack**: state pills carry a leading glyph + text label so the meaning isn't color-only; diff lines expose `+`/`-` markers in the content description, not just the wash; label the repo header "Repository {owner} slash {name}"; the heatmap squares as "{n} contributions on {date}".
- **Touch targets**: Material guidance is 48dp — give the 13–17dp inline glyphs (star, fork, branch) a 48dp hit via padding when tappable; file-tree rows are full-row tappable; primary buttons ≥ 36dp.
- **Contrast**: `#E6EDF3` on `#0D1117` passes WCAG AA; `#7D8590` muted text passes AA for ≥12sp; the reserved green `#238636` with `#FFFFFF` text passes AA. Validate any custom issue-label tint (text = label color on a 10% fill).
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, make the tab-underline an instant jump, the merge an instant state swap, the count an instant set, and diff-expand an instant insert.
