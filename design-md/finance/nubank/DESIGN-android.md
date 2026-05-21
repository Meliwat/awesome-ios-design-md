# Nubank (iOS) — Jetpack Compose Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file ports Nubank's visual language to **Android with Jetpack Compose (Material 3)**: a color token object, a `Typography` set, paste-ready `@Composable`s, the purple hero + tiles + quick actions, motion, and haptics.

> Why a Compose guide for an iOS-referenced app? The DESIGN.md tokens are platform-neutral. This file keeps the *visual* identity (the full-bleed Nu Purple hero, the calm tile stack, the Roxinho card, pill buttons) while making everything idiomatic Android — `NavigationBar` instead of a UITabBar, `LazyColumn` for tiles, `LazyRow` for quick actions, `Brush.linearGradient` for the hero, `sp`/`dp` instead of `pt`.

Assumes Compose BOM `2024.09+`, Kotlin `2.0+`, `minSdk 24`, Material 3, and [Coil](https://coil-kt.github.io/coil/) `2.6+` if you load avatars. Do **not** enable Material You `dynamicColorScheme()` — Nu Purple is the identity and must hold regardless of wallpaper. A full dark scheme (deep aubergine) is provided.

## 1. Color Tokens

```kotlin
// ui/theme/NuColors.kt
import androidx.compose.ui.graphics.Color

object NuColors {
    // Brand
    val Purple        = Color(0xFF820AD1)
    val PurpleBright  = Color(0xFF9B2BE0)
    val PurplePressed = Color(0xFF6A07AD)
    val PurpleSoft    = Color(0xFFC9A6E8)

    // Canvas & Surfaces (Light)
    val Canvas         = Color(0xFFF4F2F7)
    val Surface        = Color(0xFFFFFFFF)
    val SurfacePressed = Color(0xFFECE8F2)
    val Divider        = Color(0xFFE6E1EE)

    // Canvas & Surfaces (Dark)
    val DarkCanvas   = Color(0xFF15101C) // deep aubergine — NOT pure black
    val DarkSurface1 = Color(0xFF1F1729)
    val DarkSurface2 = Color(0xFF2A2038)
    val DarkDivider  = Color(0xFF362B47)

    // Text
    val TextPrimary       = Color(0xFF1A1523)
    val TextSecondary     = Color(0xFF6B6178)
    val TextTertiary      = Color(0xFF9C93AB)
    val DarkTextPrimary   = Color(0xFFF3EEF8)
    val DarkTextSecondary = Color(0xFFB6A8C8)
    val DarkTextTertiary  = Color(0xFF7A6B8E)

    // Product accents (light / dark)
    val GreenLight = Color(0xFF1FB76A); val Green = Color(0xFF2ED47A)
    val GoldLight  = Color(0xFFD9A227); val Gold  = Color(0xFFF5C24B)
    val PinkLight  = Color(0xFFD9469E); val Pink  = Color(0xFFE85FB0)
    val InfoLight  = Color(0xFF3D7AE0); val Info  = Color(0xFF5A95F0)

    // Semantic
    val PositiveLight = Color(0xFF1FB76A); val Positive = Color(0xFF2ED47A)
    val ErrorLight    = Color(0xFFE03A4A); val Error    = Color(0xFFFF5C6C)
    val WarningLight  = Color(0xFFD9821F); val Warning  = Color(0xFFFFB347)
}
```

Wire it into both schemes. Nubank is light-first with a bold purple hero; the dark scheme uses `#15101C` deep aubergine, never true black.

```kotlin
// ui/theme/Theme.kt
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.material3.MaterialTheme

private val NuLight = lightColorScheme(
    primary        = NuColors.Purple,
    onPrimary      = Color.White,
    background     = NuColors.Canvas,
    onBackground   = NuColors.TextPrimary,
    surface        = NuColors.Surface,
    onSurface      = NuColors.TextPrimary,
    surfaceVariant = NuColors.SurfacePressed,
    outline        = NuColors.Divider,
    error          = NuColors.ErrorLight,
)

private val NuDark = darkColorScheme(
    primary        = NuColors.PurpleSoft, // interactive tint lifts for contrast
    onPrimary      = NuColors.DarkCanvas,
    background     = NuColors.DarkCanvas,
    onBackground   = NuColors.DarkTextPrimary,
    surface        = NuColors.DarkSurface1,
    onSurface      = NuColors.DarkTextPrimary,
    surfaceVariant = NuColors.DarkSurface2,
    outline        = NuColors.DarkDivider,
    error          = NuColors.Error,
)

@Composable
fun NuTheme(
    dark: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit,
) = MaterialTheme(
    colorScheme = if (dark) NuDark else NuLight,
    typography = NuTypography,
    content = content,
)
```

## 2. Typography

Nubank ships Graphik / Nu Sans; bundle **Inter** (SIL OFL) as the free substitute in `res/font/`. Currency uses tabular figures via a font feature setting. Body 400; greeting/section 700; balance/screen-title 800.

```kotlin
// ui/theme/NuType.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.Font
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Inter = FontFamily(
    Font(R.font.inter_regular,   FontWeight.Normal),
    Font(R.font.inter_medium,    FontWeight.Medium),
    Font(R.font.inter_semibold,  FontWeight.SemiBold),
    Font(R.font.inter_bold,      FontWeight.Bold),
    Font(R.font.inter_extrabold, FontWeight.ExtraBold),
)

object NuText {
    val ScreenTitle = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 32.sp, lineHeight = 37.sp, letterSpacing = (-0.6).sp)
    val Balance     = TextStyle(Inter, fontWeight = FontWeight.ExtraBold, fontSize = 26.sp, lineHeight = 29.sp, letterSpacing = (-0.5).sp, fontFeatureSettings = "tnum")
    val Greeting    = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 22.sp, lineHeight = 26.sp, letterSpacing = (-0.3).sp)
    val Subsection  = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 18.sp, lineHeight = 23.sp, letterSpacing = (-0.2).sp)
    val Body        = TextStyle(Inter, fontWeight = FontWeight.Normal,    fontSize = 16.sp, lineHeight = 23.sp)
    val RowTitle    = TextStyle(Inter, fontWeight = FontWeight.SemiBold,  fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp)
    val Amount      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 15.sp, lineHeight = 20.sp, letterSpacing = (-0.1).sp, fontFeatureSettings = "tnum")
    val Meta        = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 13.sp, lineHeight = 18.sp)
    val Label       = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 12.sp, lineHeight = 16.sp, letterSpacing = 0.1.sp)
    val Button      = TextStyle(Inter, fontWeight = FontWeight.Bold,      fontSize = 16.sp, lineHeight = 16.sp)
    val Tab         = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 10.sp, lineHeight = 10.sp, letterSpacing = 0.1.sp)
    val QuickAction = TextStyle(Inter, fontWeight = FontWeight.Medium,    fontSize = 11.sp, lineHeight = 13.sp)
}

// Map onto Material 3 slots so stock components inherit the brand
val NuTypography = Typography(
    headlineLarge = NuText.ScreenTitle,
    headlineMedium = NuText.Greeting,
    titleMedium   = NuText.Subsection,
    bodyMedium    = NuText.Body,
    labelSmall    = NuText.Tab,
)
```

## 3. Signature Components

### Purple Hero Header

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.outlined.*
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.unit.dp
import androidx.compose.foundation.layout.statusBarsPadding

@Composable
fun NuHeroHeader(initials: String, name: String, modifier: Modifier = Modifier) {
    Column(
        modifier
            .fillMaxWidth()
            .background(Brush.linearGradient(listOf(Color(0xFF820AD1), Color(0xFF9B2BE0), Color(0xFF6A07AD))))
            .statusBarsPadding()
            .padding(top = 8.dp, bottom = 22.dp),
    ) {
        Row(
            Modifier.fillMaxWidth().padding(horizontal = 22.dp).padding(bottom = 16.dp),
            verticalAlignment = Alignment.CenterVertically,
        ) {
            Box(
                Modifier.size(38.dp).clip(CircleShape).background(Color.White.copy(alpha = 0.18f)),
                contentAlignment = Alignment.Center,
            ) { Text(initials, color = Color.White, style = NuText.RowTitle) }
            Spacer(Modifier.weight(1f))
            Row(horizontalArrangement = Arrangement.spacedBy(18.dp)) {
                Icon(Icons.Outlined.Settings, null, tint = Color.White, modifier = Modifier.size(21.dp))
                Icon(Icons.Outlined.Notifications, null, tint = Color.White, modifier = Modifier.size(21.dp))
                Icon(Icons.Outlined.HelpOutline, null, tint = Color.White, modifier = Modifier.size(21.dp))
            }
        }
        Text("Olá,", style = NuText.Label, color = Color.White.copy(alpha = 0.85f), modifier = Modifier.padding(horizontal = 22.dp))
        Text(name, style = NuText.Greeting, color = Color.White, modifier = Modifier.padding(horizontal = 22.dp))
    }
}
```

### NuConta Balance Tile

```kotlin
import androidx.compose.material.icons.outlined.Visibility
import androidx.compose.material.icons.outlined.VisibilityOff
import androidx.compose.foundation.clickable
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.runtime.*

@Composable
fun NuAccountTile(balance: String, modifier: Modifier = Modifier) {
    var hidden by remember { mutableStateOf(false) }
    Column(
        modifier
            .padding(horizontal = 18.dp)
            .fillMaxWidth()
            .clip(RoundedCornerShape(16.dp))
            .background(NuColors.DarkSurface1)
            .border(1.dp, NuColors.DarkDivider, RoundedCornerShape(16.dp))
            .padding(18.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically) {
            Icon(Icons.Outlined.AccountBalanceWallet, null, tint = NuColors.PurpleSoft, modifier = Modifier.size(28.dp))
            Spacer(Modifier.width(12.dp))
            Text("Conta", style = NuText.RowTitle, color = NuColors.DarkTextPrimary, modifier = Modifier.weight(1f))
            Icon(
                if (hidden) Icons.Outlined.VisibilityOff else Icons.Outlined.Visibility, "Toggle balance",
                tint = NuColors.DarkTextSecondary,
                modifier = Modifier.size(20.dp).clickable { hidden = !hidden },
            )
        }
        Text(if (hidden) "R$ ••••••" else balance, style = NuText.Balance, color = NuColors.DarkTextPrimary, modifier = Modifier.padding(top = 14.dp))
        Text("Saldo disponível", style = NuText.Meta, color = NuColors.DarkTextSecondary, modifier = Modifier.padding(top = 2.dp))
    }
}
```

### Credit-Card (Roxinho) Tile

```kotlin
@Composable
fun NuCreditTile(
    invoice: String, dueText: String, limitText: String, used: Float,
    modifier: Modifier = Modifier,
) {
    Column(
        modifier
            .padding(horizontal = 18.dp)
            .fillMaxWidth()
            .clip(RoundedCornerShape(16.dp))
            .background(NuColors.DarkSurface1)
            .padding(18.dp),
    ) {
        Row(verticalAlignment = Alignment.CenterVertically) {
            Box(
                Modifier.size(width = 40.dp, height = 26.dp)
                    .clip(RoundedCornerShape(5.dp))
                    .background(Brush.linearGradient(listOf(NuColors.Purple, NuColors.PurpleBright)))
            )
            Spacer(Modifier.width(12.dp))
            Text("Cartão de crédito", style = NuText.RowTitle, color = NuColors.DarkTextPrimary)
        }
        Text(invoice, style = NuText.Subsection.copy(fontWeight = FontWeight.ExtraBold),
            color = NuColors.DarkTextPrimary, modifier = Modifier.padding(top = 14.dp))
        Text(dueText, style = NuText.Meta, color = NuColors.DarkTextSecondary, modifier = Modifier.padding(top = 2.dp))
        Box(
            Modifier.fillMaxWidth().height(6.dp).padding(top = 0.dp)
                .clip(RoundedCornerShape(3.dp)).background(NuColors.DarkDivider)
                .padding(top = 12.dp)
        )
        Box(Modifier.fillMaxWidth().padding(top = 12.dp).height(6.dp).clip(RoundedCornerShape(3.dp)).background(NuColors.DarkDivider)) {
            Box(Modifier.fillMaxWidth(used).height(6.dp).clip(RoundedCornerShape(3.dp)).background(NuColors.PurpleBright))
        }
        Text(limitText, style = NuText.Meta, color = NuColors.DarkTextSecondary, modifier = Modifier.padding(top = 8.dp))
    }
}
```

### Quick-Action Shortcut

```kotlin
import androidx.compose.ui.graphics.vector.ImageVector

@Composable
fun NuQuickAction(icon: ImageVector, label: String, onClick: () -> Unit) {
    Column(
        Modifier.width(72.dp).clickable(
            indication = null,
            interactionSource = remember { MutableInteractionSource() },
        ) { onClick() },
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Box(
            Modifier.size(52.dp).clip(CircleShape).background(NuColors.DarkSurface2),
            contentAlignment = Alignment.Center,
        ) { Icon(icon, contentDescription = label, tint = NuColors.PurpleSoft, modifier = Modifier.size(22.dp)) }
        Spacer(Modifier.height(8.dp))
        Text(label, style = NuText.QuickAction, color = NuColors.DarkTextSecondary)
    }
}
```

### Activity Row

```kotlin
import androidx.compose.foundation.draw.alpha

@Composable
fun NuActivityRow(
    icon: ImageVector, title: String, subtitle: String, amount: String,
    isIncome: Boolean = false, isPending: Boolean = false,
) {
    Row(
        Modifier.fillMaxWidth().alpha(if (isPending) 0.6f else 1f).padding(horizontal = 22.dp, vertical = 10.dp),
        verticalAlignment = Alignment.CenterVertically,
        horizontalArrangement = Arrangement.spacedBy(14.dp),
    ) {
        Box(
            Modifier.size(38.dp).clip(CircleShape).background(NuColors.DarkSurface2),
            contentAlignment = Alignment.Center,
        ) { Icon(icon, null, tint = NuColors.PurpleSoft, modifier = Modifier.size(19.dp)) }
        Column(Modifier.weight(1f)) {
            Text(title, style = NuText.RowTitle, color = NuColors.DarkTextPrimary)
            Text(subtitle, style = NuText.Meta, color = NuColors.DarkTextSecondary)
        }
        Text(amount, style = NuText.Amount, color = if (isIncome) NuColors.Positive else NuColors.DarkTextPrimary)
    }
}
```

### Pill Buttons

```kotlin
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.OutlinedButton
import androidx.compose.foundation.BorderStroke

@Composable
fun NuPrimaryButton(title: String, onClick: () -> Unit, modifier: Modifier = Modifier) {
    Button(
        onClick = onClick,
        modifier = modifier.fillMaxWidth().heightIn(min = 48.dp),
        shape = RoundedCornerShape(500.dp),
        colors = ButtonDefaults.buttonColors(containerColor = NuColors.Purple, contentColor = Color.White),
    ) { Text(title, style = NuText.Button) }
}

@Composable
fun NuSecondaryButton(title: String, onClick: () -> Unit) {
    OutlinedButton(
        onClick = onClick,
        shape = RoundedCornerShape(500.dp),
        border = BorderStroke(1.5.dp, NuColors.PurpleSoft),
    ) { Text(title, style = NuText.RowTitle.copy(fontWeight = FontWeight.SemiBold), color = NuColors.PurpleSoft) }
}
```

## 4. Navigation

Nubank has a 5-tab bottom strip with **no tint pill** — active is just the purple icon + label (Nu Purple on light, Purple Soft on dark).

```kotlin
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*

@Composable
fun NuBottomBar(selected: Int, onSelect: (Int) -> Unit) {
    NavigationBar(containerColor = NuColors.DarkCanvas, tonalElevation = 0.dp) {
        val items = listOf(
            "Início"   to Icons.Filled.Home,
            "Cartões"  to Icons.Filled.CreditCard,
            "Investir" to Icons.Filled.TrendingUp,
            "Buscar"   to Icons.Filled.Search,
            "Perfil"   to Icons.Filled.AccountCircle,
        )
        items.forEachIndexed { i, (label, icon) ->
            NavigationBarItem(
                selected = selected == i,
                onClick = { onSelect(i) },
                icon = { Icon(icon, contentDescription = label, modifier = Modifier.size(22.dp)) },
                label = { Text(label, style = NuText.Tab) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor   = NuColors.PurpleSoft,
                    selectedTextColor   = NuColors.PurpleSoft,
                    unselectedIconColor = NuColors.DarkTextTertiary,
                    unselectedTextColor = NuColors.DarkTextTertiary,
                    indicatorColor      = Color.Transparent, // no Material pill — Nubank has none
                ),
            )
        }
    }
}
```

The Home screen is a `LazyColumn`: the purple hero (`item`, full-bleed), the NuConta tile (`item`), the quick-actions `LazyRow` (`item`), the credit tile (`item`), then the "Atividade" section header + activity rows. A collapsing-toolbar effect (hero compresses, first tile slides over it) is achieved with a `nestedScroll` connection driving the hero height.

## 5. Motion

Nubank motion is gentle and reassuring — subtle press feedback, a soft balance reveal, a celebratory Pix check.

| Moment | Compose recipe |
|--------|----------------|
| Tile / quick-action press | `Modifier.scale(animateFloatAsState(if (pressed) 0.98f else 1f, tween(80)))` |
| Balance reveal | `AnimatedContent` between masked/real with `fadeIn + fadeOut tween(200)` |
| Pix success | a `Canvas` check path with `animateFloatAsState` `pathMeasure` draw `tween(400)` + amount count-up |
| Number ticker | `animateIntAsState(targetCents, tween(250))` then format to BRL |
| Hero parallax | `nestedScroll` shrinks hero height; first tile `offset` slides over it |
| Tab change | instant color crossfade (NavigationBar handles); ~120ms feel |
| Page navigation | Nav3/`NavHost` slide push `tween(300)` |

```kotlin
// Balance ticker — the canonical Nubank "value updated" motion
val shown by animateIntAsState(balanceCents, tween(250), label = "balanceTick")
Text(formatBRL(shown), style = NuText.Balance, color = NuColors.DarkTextPrimary)
```

Haptics: prefer `LocalHapticFeedback.performHapticFeedback(HapticFeedbackType.TextHandleMove)` (light) for tile and quick-action presses; for Pix/payment success use `view.performHapticFeedback(HapticFeedbackConstants.CONFIRM)`. Tab change gets a soft `CLOCK_TICK`. The purple hero has no motion of its own beyond the parallax compress.

## 6. Icons

Nubank's product glyphs map cleanly to `androidx.compose.material:material-icons-extended`. The Roxinho mini-card is a gradient `Box`, not an icon.

| Purpose | SF Symbol (iOS) | Material Icon (Compose) |
|---------|-----------------|--------------------------|
| Início (tab) | `house.fill` | `Icons.Filled.Home` |
| Cartões (tab) | `creditcard.fill` | `Icons.Filled.CreditCard` |
| Investir (tab) | `chart.line.uptrend.xyaxis` | `Icons.Filled.TrendingUp` |
| Buscar (tab) | `magnifyingglass` | `Icons.Filled.Search` |
| Perfil (tab) | `person.crop.circle.fill` | `Icons.Filled.AccountCircle` |
| Settings (hero) | `gearshape` | `Icons.Outlined.Settings` |
| Notifications (hero) | `bell` | `Icons.Outlined.Notifications` |
| Help (hero) | `questionmark.circle` | `Icons.Outlined.HelpOutline` |
| Pix | `arrow.up` | `Icons.Filled.ArrowUpward` |
| Pagar | `barcode` | `Icons.Filled.QrCodeScanner` |
| Empréstimo | `dollarsign.circle` | `Icons.Filled.AttachMoney` |
| Conta value | `brazilianrealsign.circle` | `Icons.Outlined.AccountBalanceWallet` |
| Pix received | `arrow.down` | `Icons.Filled.ArrowDownward` |
| Hide balance | `eye` / `eye.slash` | `Icons.Outlined.Visibility` / `VisibilityOff` |
| Back | `chevron.left` | `Icons.Filled.ArrowBack` |
| Receipt | `doc.text` | `Icons.Filled.Receipt` |
| Success check | `checkmark.circle.fill` | `Icons.Filled.CheckCircle` |

## 7. Minimum SDK & Accessibility Notes

- **minSdk 24** (Compose floor is 21; gradient brushes + modern motion comfortable at 24). `compileSdk 34+`, Compose BOM `2024.09+`, Kotlin `2.0+`.
- **Edge-to-edge**: call `enableEdgeToEdge()`; the purple hero extends under the status bar — keep light-content system icons over it via `WindowCompat`. Sheets respect the IME with `Modifier.imePadding()`.
- **Tabular figures**: set `fontFeatureSettings = "tnum"` on balance and amount styles so currency columns align; verify on your bundled Inter.
- **Font scaling**: `sp` honors the user's font scale — keep it on screen title, balance, greeting, body, row title, meta. Pin layout-sensitive text (10sp tab labels, quick-action labels) by deriving from `dp` or a fixed-`fontScale` `CompositionLocalProvider`.
- **TalkBack**: label the NuConta tile "Conta, saldo disponível {amount}, double-tap to open"; the eye-toggle "Hide balance" / "Show balance"; activity rows "{title}, {amount}, {subtitle}{, pendente}"; expose the credit progress bar via `progressBarRangeInfo`.
- **Touch targets**: Material guidance is 48.dp. Tiles are full-tile tappable (≥72.dp); give 52.dp quick-action circles ≥48.dp hit; 22.dp tab icons a 48.dp hit area; primary buttons ≥ 48.dp.
- **Contrast**: `#1A1523` on `#F4F2F7` and `#F3EEF8` on `#15101C` pass WCAG AA for body; white on Nu Purple `#820AD1` passes AA — validate product-accent chips, especially gold, with a contrast checker.
- **Reduce motion**: when `Settings.Global.ANIMATOR_DURATION_SCALE == 0`, disable the Pix check draw (show final state), the balance ticker (set value directly), and the hero parallax (static header); keep the income/spend color cue.
- **Dark mode**: invert via the `Dark*` palette — `#15101C` deep aubergine, NOT true black; tiles `#1F1729`; text `#F3EEF8`; interactive tint shifts to Purple Soft. Shadows are weak on dark, so a 1dp `DarkDivider` border on tiles/sheets is the elevation cue. Do **not** enable `dynamicColorScheme()` — Nu Purple is the identity and must hold regardless of wallpaper.
