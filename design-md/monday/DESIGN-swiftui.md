# monday.com (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates monday.com's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let mndCanvas        = Color.white                                   // #FFFFFF
    static let mndSurfaceGray   = Color(red: 0.965, green: 0.969, blue: 0.984) // #F6F7FB
    static let mndSurfacePressed = Color(red: 0.929, green: 0.937, blue: 0.961) // #EDEFF5
    static let mndDivider       = Color(red: 0.902, green: 0.914, blue: 0.941) // #E6E9F0
    static let mndCellBorder    = Color(red: 0.816, green: 0.831, blue: 0.894) // #D0D4E4

    // MARK: - Canvas & Surfaces (Dark / Night)
    static let mndNightCanvas   = Color(red: 0.094, green: 0.106, blue: 0.204) // #181B34
    static let mndNightSurface1 = Color(red: 0.125, green: 0.141, blue: 0.247) // #20243F
    static let mndNightSurface2 = Color(red: 0.161, green: 0.184, blue: 0.298) // #292F4C
    static let mndNightDivider  = Color(red: 0.208, green: 0.231, blue: 0.361) // #353B5C
    static let mndNightCellBorder = Color(red: 0.239, green: 0.263, blue: 0.416) // #3D436A

    // MARK: - Text
    static let mndTextPrimary    = Color(red: 0.196, green: 0.200, blue: 0.220) // #323338
    static let mndTextSecondary  = Color(red: 0.404, green: 0.408, blue: 0.475) // #676879
    static let mndTextTertiary   = Color(red: 0.588, green: 0.600, blue: 0.651) // #9699A6
    static let mndDarkTextPrimary = Color(red: 0.961, green: 0.965, blue: 0.973) // #F5F6F8
    static let mndDarkTextSecondary = Color(red: 0.655, green: 0.686, blue: 0.776) // #A7AFC6
    static let mndDarkTextTertiary = Color(red: 0.420, green: 0.451, blue: 0.580) // #6B7394

    // MARK: - Action
    static let mndBlue         = Color(red: 0.000, green: 0.451, blue: 0.918) // #0073EA
    static let mndBluePressed  = Color(red: 0.000, green: 0.376, blue: 0.761) // #0060C2

    // MARK: - Multicolor Data Palette (full-bleed fills)
    static let mndRed       = Color(red: 0.886, green: 0.267, blue: 0.361) // #E2445C
    static let mndOrange    = Color(red: 0.992, green: 0.671, blue: 0.239) // #FDAB3D
    static let mndGreen     = Color(red: 0.000, green: 0.784, blue: 0.459) // #00C875
    static let mndBrightBlue = Color(red: 0.341, green: 0.608, blue: 0.988) // #579BFC
    static let mndPurple    = Color(red: 0.635, green: 0.365, blue: 0.863) // #A25DDC
    static let mndDarkPurple = Color(red: 0.251, green: 0.086, blue: 0.580) // #401694
    static let mndIndigo    = Color(red: 0.333, green: 0.349, blue: 0.875) // #5559DF
    static let mndYellow    = Color(red: 1.000, green: 0.796, blue: 0.000) // #FFCB00
    static let mndTeal      = Color(red: 0.000, green: 0.824, blue: 0.824) // #00D2D2
    static let mndPink      = Color(red: 1.000, green: 0.082, blue: 0.541) // #FF158A
    static let mndBrightGreen = Color(red: 0.612, green: 0.827, blue: 0.149) // #9CD326
    static let mndNavy      = Color(red: 0.200, green: 0.239, blue: 0.431) // #333D6E

    // MARK: - Semantic
    static let mndSuccess = Color(red: 0.000, green: 0.784, blue: 0.459) // #00C875
    static let mndError   = Color(red: 0.886, green: 0.267, blue: 0.361) // #E2445C
    static let mndWarning = Color(red: 0.992, green: 0.671, blue: 0.239) // #FDAB3D
}

// On-color text helper: yellow/teal/bright-green flip to dark
func mndOnColor(_ bg: Color) -> Color {
    [Color.mndYellow, .mndTeal, .mndBrightGreen].contains(bg) ? .mndTextPrimary : .white
}
```

## 2. Typography

monday.com uses **Figtree** (Poppins-class brand sans). Bundle Figtree TTFs via `Info.plist` / `UIAppFonts`; falls back to SF Pro when absent.

```swift
extension Font {
    static func mnd(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .regular:  return "Figtree-Regular"
            case .medium:   return "Figtree-Medium"
            case .semibold: return "Figtree-SemiBold"
            case .bold:     return "Figtree-Bold"
            case .heavy, .black: return "Figtree-ExtraBold"
            default:        return "Figtree-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let mndScreenTitle = Font.custom("Figtree-ExtraBold", size: 32)
    static let mndBoardTitle  = Font.custom("Figtree-Bold",      size: 26)
    static let mndGroupHeader = Font.custom("Figtree-ExtraBold", size: 22)
    static let mndSection     = Font.custom("Figtree-Bold",      size: 18)
    static let mndBody        = Font.custom("Figtree-Regular",   size: 16)
    static let mndItemName    = Font.custom("Figtree-Medium",    size: 14)
    static let mndColumnHead  = Font.custom("Figtree-SemiBold",  size: 13)
    static let mndMeta        = Font.custom("Figtree-Regular",   size: 14)
    static let mndStatusLabel = Font.custom("Figtree-Bold",      size: 11)
    static let mndButton      = Font.custom("Figtree-SemiBold",  size: 15)
    static let mndTextAction  = Font.custom("Figtree-SemiBold",  size: 14)
    static let mndPersonInit  = Font.custom("Figtree-Bold",      size: 9)
    static let mndTab         = Font.custom("Figtree-SemiBold",  size: 10)
}
```

## 3. Signature Components

### Board Item Row (stripe + cells)

```swift
struct BoardItemRow: View {
    let groupColor: Color
    let name: String
    let personInitials: String
    let personColor: Color
    let status: (label: String, color: Color)

    var body: some View {
        HStack(spacing: 0) {
            Rectangle()
                .fill(groupColor)
                .frame(width: 6)               // full-height group stripe, flush leading

            HStack(spacing: 10) {
                Text(name)
                    .font(.mndItemName)
                    .foregroundStyle(Color.mndTextPrimary)
                    .lineLimit(1)
                Spacer(minLength: 8)

                Text(personInitials)
                    .font(.mndPersonInit)
                    .foregroundStyle(.white)
                    .frame(width: 24, height: 24)
                    .background(Circle().fill(personColor))

                StatusCell(label: status.label, color: status.color)
            }
            .padding(.horizontal, 14)
            .padding(.vertical, 12)
        }
        .overlay(alignment: .bottom) { Rectangle().fill(Color.mndDivider).frame(height: 1) }
    }
}

struct StatusCell: View {
    let label: String
    let color: Color
    var body: some View {
        Text(label.uppercased())
            .font(.mndStatusLabel)
            .foregroundStyle(mndOnColor(color))
            .frame(minWidth: 88)
            .padding(.vertical, 5)
            .padding(.horizontal, 10)
            .background(RoundedRectangle(cornerRadius: 4).fill(color))
    }
}
```

### Group Header (colored, collapsible)

```swift
struct GroupHeader: View {
    let name: String
    let groupColor: Color
    let count: Int
    @Binding var expanded: Bool

    var body: some View {
        Button {
            withAnimation(.easeOut(duration: 0.2)) { expanded.toggle() }
        } label: {
            HStack(spacing: 8) {
                Image(systemName: "chevron.right")
                    .font(.system(size: 12, weight: .bold))
                    .foregroundStyle(groupColor)
                    .rotationEffect(.degrees(expanded ? 90 : 0))
                Text(name)
                    .font(.mndGroupHeader)
                    .foregroundStyle(groupColor)
                Text("\(count) items")
                    .font(.mndColumnHead)
                    .foregroundStyle(Color.mndTextTertiary)
                Spacer()
            }
            .padding(.horizontal, 18)
            .padding(.top, 12).padding(.bottom, 8)
        }
        .buttonStyle(.plain)
    }
}
```

### Status Label Picker (bottom sheet grid)

```swift
struct StatusOption: Identifiable { let id = UUID(); let label: String; let color: Color }

struct StatusPicker: View {
    let options: [StatusOption]
    let onPick: (StatusOption) -> Void

    var body: some View {
        LazyVGrid(columns: [GridItem(.flexible()), GridItem(.flexible())], spacing: 10) {
            ForEach(options) { opt in
                Button { onPick(opt) } label: {
                    Text(opt.label.uppercased())
                        .font(.mndStatusLabel)
                        .foregroundStyle(mndOnColor(opt.color))
                        .frame(maxWidth: .infinity)
                        .padding(.vertical, 14)
                        .background(RoundedRectangle(cornerRadius: 4).fill(opt.color))
                }
                .buttonStyle(.plain)
            }
        }
        .padding(20)
    }
}
```

### Battery Rollup (group health)

```swift
struct BatteryRollup: View {
    // proportions sum to 1.0
    let segments: [(color: Color, fraction: CGFloat)]

    var body: some View {
        GeometryReader { geo in
            HStack(spacing: 0) {
                ForEach(Array(segments.enumerated()), id: \.offset) { _, seg in
                    Rectangle()
                        .fill(seg.color)
                        .frame(width: geo.size.width * seg.fraction)
                }
            }
        }
        .frame(height: 24)
        .clipShape(RoundedRectangle(cornerRadius: 4))
        .background(RoundedRectangle(cornerRadius: 4).fill(Color.mndNightSurface2))
        .animation(.easeOut(duration: 0.25), value: segments.map(\.fraction))
    }
}
```

### Floating Action Button

```swift
struct CreateFAB: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: "plus")
                .font(.system(size: 24, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(Circle().fill(Color.mndBlue))
                .shadow(color: Color.mndBlue.opacity(0.45), radius: 20, y: 8)
        }
        .buttonStyle(.plain)
    }
}
```

### Kanban Card

```swift
struct KanbanCard: View {
    let groupColor: Color
    let name: String
    let personInitials: String
    let personColor: Color
    let status: (label: String, color: Color)

    var body: some View {
        HStack(spacing: 0) {
            Rectangle().fill(groupColor).frame(width: 6)
            VStack(alignment: .leading, spacing: 10) {
                Text(name).font(.mndItemName).foregroundStyle(Color.mndTextPrimary)
                HStack {
                    Text(personInitials).font(.mndPersonInit).foregroundStyle(.white)
                        .frame(width: 24, height: 24).background(Circle().fill(personColor))
                    Spacer()
                    StatusCell(label: status.label, color: status.color)
                }
            }
            .padding(12)
        }
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.mndCanvas))
        .overlay(RoundedRectangle(cornerRadius: 8).strokeBorder(Color.mndCellBorder, lineWidth: 1))
        .shadow(color: .black.opacity(0.06), radius: 4, y: 1)
        .clipShape(RoundedRectangle(cornerRadius: 8))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct MondayTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house") }
            BoardsView().tabItem { Label("Boards", systemImage: "tablecells") }
            InboxView().tabItem { Label("Inbox", systemImage: "bell") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
        }
        .tint(.mndBlue) // active = action blue, no pill indicator
    }
}
```

## 5. Motion

```swift
// Status change — picker open + cell crossfade + haptic
.sheet(isPresented: $showPicker) { StatusPicker(options: opts, onPick: { /* ... */ }) }
withAnimation(.easeInOut(duration: 0.2)) { status = newStatus }
.sensoryFeedback(.impact(flexibility: .soft), trigger: status.label)

// Group collapse/expand
withAnimation(.easeOut(duration: 0.2)) { expanded.toggle() }
// caret: .rotationEffect(.degrees(expanded ? 90 : 0))

// FAB → new-item sheet
.sheet(isPresented: $showNew) { NewItemSheet() }   // 280ms ease-out rise

// Drag item / Kanban card
// DragGesture 1:1; neighbors .easeOut(0.2); drop .easeOut(0.15); stripe recolors on drop
.sensoryFeedback(.impact(flexibility: .soft), trigger: dragStarted)

// Battery segments
.animation(.easeOut(duration: 0.25), value: segments.map(\.fraction))

// View tab underline slide
withAnimation(.easeOut(duration: 0.2)) { selectedView = tapped }
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Boards (tab) | `tablecells` / `tablecells.fill` | 22pt |
| Inbox (tab) | `bell` / `bell.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| Add / FAB | `plus` | 24pt |
| Group caret | `chevron.right` (rotate 90°) | 12pt |
| Person (empty) | `person.crop.circle` | 24pt |
| Date column | `calendar` | 16pt |
| Timeline column | `chart.bar.xaxis` | 16pt |
| Numbers column | `number` | 16pt |
| More actions | `ellipsis` | 20pt |
| Back | `chevron.left` | 24pt |
| Filter | `line.3.horizontal.decrease` | 16pt |
| Sort | `arrow.up.arrow.down` | 16pt |
| Updates / posts | `bubble.left` | 16pt |
| Automation | `bolt` | 16pt |
| Main Table view | `tablecells` | 18pt |
| Kanban view | `rectangle.split.3x1` | 18pt |
| Timeline view | `calendar.day.timeline.left` | 18pt |
| Chart view | `chart.pie` | 18pt |
| Attachment | `paperclip` | 16pt |

## 7. Dark Mode

```swift
struct MondayTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.mndNightCanvas : Color.mndCanvas)
            .foregroundStyle(scheme == .dark ? Color.mndDarkTextPrimary : Color.mndTextPrimary)
    }
}

extension View { func mondayTheme() -> some View { modifier(MondayTheme()) } }
```

Night mode uses the deep indigo `#181B34` (blue-tinted, NOT pure black). The multicolor data palette is **identical and fully saturated in both themes** — status cells must never dim, because color IS the meaning. The action blue `#0073EA` is also unchanged. Text inverts to `#F5F6F8`; surfaces lift in cool indigo steps. Gray shadows vanish on night, so Kanban cards use a 1pt `mndNightCellBorder` outline; the FAB keeps its blue glow.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator(style: .soft)` for status-change/drag haptics)
- Bundle Figtree TTFs via `Info.plist` `UIAppFonts` — SIL OFL licensed for free distribution
- Dynamic Type: support on screen/board/group titles, body, item names, updates; keep status labels, tab labels, person initials, column headers at FIXED sizes (cell-layout-sensitive)
- VoiceOver: announce a status cell as "Status: {label}" and make it a button ("opens label picker"); announce the group stripe color as part of the group context ("Group {name}, item {name}"); the battery rollup as "Group progress: {x}% done, {y}% working, {z}% stuck"
- The full-bleed status cell relies on color — never use color alone for meaning; always include the text label and a VoiceOver string
- Color contrast: white on `#00C875` / `#FDAB3D` / `#E2445C` passes WCAG AA at 11pt/700; yellow `#FFCB00` and teal `#00D2D2` MUST use `#323338` text (white fails) — the `mndOnColor` helper enforces this
- Reduce Motion: disable the cell-color crossfade and battery-segment animation (snap instead); keep the soft haptic
- Dark mode: invert chrome via `mndNight*` but keep the data palette fully saturated and identical; add 1pt `mndNightCellBorder` to cards where shadows vanish
