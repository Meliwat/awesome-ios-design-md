# Todoist (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Todoist's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the task row, floating add button, and quick-add card.

Targets iOS 17+. The modern `.sensoryFeedback`, swipe actions on `List`, and `matchedGeometryEffect` for completion animations assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let todoistCanvas       = Color(red: 1.00,  green: 1.00,  blue: 1.00)   // #FFFFFF
    static let todoistSurfaceGray  = Color(red: 0.980, green: 0.980, blue: 0.980)  // #FAFAFA
    static let todoistSurfaceGray2 = Color(red: 0.941, green: 0.941, blue: 0.941)  // #F0F0F0
    static let todoistDivider      = Color(red: 0.933, green: 0.933, blue: 0.933)  // #EEEEEE

    // MARK: - Text
    static let todoistInk          = Color(red: 0.125, green: 0.125, blue: 0.125)  // #202020 primary
    static let todoistSecondary    = Color(red: 0.502, green: 0.502, blue: 0.502)  // #808080
    static let todoistTertiary     = Color(red: 0.690, green: 0.690, blue: 0.690)  // #B0B0B0

    // MARK: - Brand & Priorities
    static let todoistRed          = Color(red: 0.863, green: 0.298, blue: 0.243)  // #DC4C3E primary
    static let todoistRedPressed   = Color(red: 0.710, green: 0.235, blue: 0.188)  // #B53C30
    static let todoistRedTint      = Color(red: 0.984, green: 0.898, blue: 0.886)  // #FBE5E2

    static let todoistP2Orange     = Color(red: 0.922, green: 0.537, blue: 0.035)  // #EB8909
    static let todoistP3Blue       = Color(red: 0.141, green: 0.435, blue: 0.878)  // #246FE0
    static let todoistP4Gray       = Color(red: 0.690, green: 0.690, blue: 0.690)  // #B0B0B0

    // MARK: - Semantic
    static let todoistSuccess      = Color(red: 0.020, green: 0.522, blue: 0.153)  // #058527
    static let todoistError        = Color(red: 0.820, green: 0.271, blue: 0.231)  // #D1453B

    // MARK: - Dark mode
    static let todoistDarkCanvas   = Color(red: 0.122, green: 0.122, blue: 0.122)  // #1F1F1F
    static let todoistDarkSurface  = Color(red: 0.157, green: 0.157, blue: 0.157)  // #282828
    static let todoistDarkSurface2 = Color(red: 0.212, green: 0.212, blue: 0.212)  // #363636
    static let todoistDarkDivider  = Color(red: 0.239, green: 0.239, blue: 0.239)  // #3D3D3D
    static let todoistDarkText     = Color(red: 0.910, green: 0.910, blue: 0.910)  // #E8E8E8
    static let todoistDarkTextSec  = Color(red: 0.627, green: 0.627, blue: 0.627)  // #A0A0A0
    static let todoistRedDark      = Color(red: 0.894, green: 0.263, blue: 0.196)  // #E44332

    // Project palette
    static let todoistProjBerryRed = Color(red: 0.722, green: 0.145, blue: 0.373)  // #B8255F
    static let todoistProjRed      = Color(red: 0.859, green: 0.251, blue: 0.208)  // #DB4035
    static let todoistProjOrange   = Color(red: 1.00,  green: 0.60,  blue: 0.20)   // #FF9933
    static let todoistProjYellow   = Color(red: 0.980, green: 0.816, blue: 0.00)   // #FAD000
    static let todoistProjGreen    = Color(red: 0.161, green: 0.580, blue: 0.220)  // #299438
    static let todoistProjMint     = Color(red: 0.416, green: 0.800, blue: 0.737)  // #6ACCBC
    static let todoistProjTeal     = Color(red: 0.082, green: 0.561, blue: 0.678)  // #158FAD
    static let todoistProjSky      = Color(red: 0.078, green: 0.667, blue: 0.961)  // #14AAF5
    static let todoistProjBlue     = Color(red: 0.251, green: 0.451, blue: 1.00)   // #4073FF
    static let todoistProjGrape    = Color(red: 0.533, green: 0.302, blue: 1.00)   // #884DFF
    static let todoistProjViolet   = Color(red: 0.686, green: 0.220, blue: 0.922)  // #AF38EB
    static let todoistProjMagenta  = Color(red: 0.878, green: 0.318, blue: 0.580)  // #E05194
}
```

## 2. Typography

Todoist uses the system font stack — SF Pro Display for display sizes, SF Pro Text for body. No custom face is bundled. Use `.system` to let iOS pick the right optical variant automatically.

```swift
extension Font {
    // Large titles
    static let todoistLargeNav    = Font.system(size: 34, weight: .bold,     design: .default)
    static let todoistInlineNav   = Font.system(size: 17, weight: .semibold, design: .default)

    // Section headers (UPPERCASE, tracked)
    static let todoistSectionHdr  = Font.system(size: 13, weight: .semibold, design: .default)

    // Task rows
    static let todoistTaskBody    = Font.system(size: 16, weight: .regular,  design: .default)
    static let todoistTaskBold    = Font.system(size: 16, weight: .semibold, design: .default)
    static let todoistSubtask     = Font.system(size: 15, weight: .regular,  design: .default)

    // Metadata
    static let todoistMeta        = Font.system(size: 13, weight: .regular,  design: .default)
    static let todoistMetaOverdue = Font.system(size: 13, weight: .medium,   design: .default)

    // Sidebar
    static let todoistSidebarSys  = Font.system(size: 15, weight: .medium,   design: .default)
    static let todoistSidebarProj = Font.system(size: 15, weight: .regular,  design: .default)

    // Inputs / Buttons
    static let todoistQuickAdd    = Font.system(size: 17, weight: .regular,  design: .default)
    static let todoistButton      = Font.system(size: 16, weight: .semibold, design: .default)
    static let todoistFABGlyph    = Font.system(size: 28, weight: .regular,  design: .default)

    // Misc
    static let todoistTab         = Font.system(size: 10, weight: .medium,   design: .default)
    static let todoistCaption     = Font.system(size: 12, weight: .regular,  design: .default)
    static let todoistKarmaNum    = Font.system(size: 28, weight: .bold,     design: .default)
}
```

## 3. Signature Components

### Priority Checkbox

```swift
enum Priority: Int { case p1 = 1, p2 = 2, p3 = 3, p4 = 4 }

extension Priority {
    var color: Color {
        switch self {
        case .p1: return .todoistRed
        case .p2: return .todoistP2Orange
        case .p3: return .todoistP3Blue
        case .p4: return .todoistP4Gray
        }
    }
    var fillTint: Color {
        switch self {
        case .p1: return Color.todoistRed.opacity(0.10)
        case .p2: return Color.todoistP2Orange.opacity(0.08)
        case .p3: return Color.todoistP3Blue.opacity(0.08)
        case .p4: return .clear
        }
    }
}

struct TodoistCheckbox: View {
    let priority: Priority
    @Binding var isComplete: Bool

    var body: some View {
        Button {
            withAnimation(.easeOut(duration: 0.2)) { isComplete.toggle() }
        } label: {
            ZStack {
                Circle()
                    .strokeBorder(priority.color, lineWidth: 1.5)
                    .background(Circle().fill(isComplete ? priority.color : priority.fillTint))
                    .frame(width: 22, height: 22)

                if isComplete {
                    Image(systemName: "checkmark")
                        .font(.system(size: 12, weight: .bold))
                        .foregroundStyle(.white)
                }
            }
            .frame(width: 44, height: 44) // 44pt hit area
            .contentShape(Rectangle())
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(weight: .light), trigger: isComplete)
    }
}
```

### Task Row (The Hero Component)

```swift
struct TaskRow: View {
    let content: String
    let priority: Priority
    let date: TaskDate?
    let project: (name: String, color: Color)?
    let commentCount: Int
    @State private var isComplete = false

    enum TaskDate { case today, tomorrow, overdue(String), future(String) }

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            TodoistCheckbox(priority: priority, isComplete: $isComplete)

            VStack(alignment: .leading, spacing: 4) {
                Text(content)
                    .font(.todoistTaskBody)
                    .foregroundStyle(isComplete ? Color.todoistTertiary : Color.todoistInk)
                    .strikethrough(isComplete, color: .todoistTertiary)
                    .lineLimit(3)
                    .opacity(isComplete ? 0.6 : 1)

                if date != nil || project != nil || commentCount > 0 {
                    HStack(spacing: 12) {
                        if let date = date { datePill(date) }
                        if let p = project { projectTag(p.name, p.color) }
                        if commentCount > 0 { commentBadge(commentCount) }
                        Spacer()
                    }
                }
            }

            Spacer(minLength: 0)
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 12)
        .frame(minHeight: 52)
        .background(Color.todoistCanvas)
        .overlay(alignment: .bottom) {
            Rectangle()
                .fill(Color.todoistDivider)
                .frame(height: 0.5)
                .padding(.leading, 64)
        }
    }

    @ViewBuilder
    private func datePill(_ d: TaskDate) -> some View {
        switch d {
        case .today:           Text("Today").font(.todoistMeta).foregroundStyle(Color.todoistSuccess)
        case .tomorrow:        Text("Tomorrow").font(.todoistMeta).foregroundStyle(Color.todoistInk)
        case .overdue(let s):  Text(s).font(.todoistMetaOverdue).foregroundStyle(Color.todoistRed)
        case .future(let s):   Text(s).font(.todoistMeta).foregroundStyle(Color.todoistSecondary)
        }
    }

    private func projectTag(_ name: String, _ color: Color) -> some View {
        HStack(spacing: 6) {
            Circle().fill(color).frame(width: 8, height: 8)
            Text("# \(name)").font(.todoistMeta).foregroundStyle(Color.todoistSecondary)
        }
    }

    private func commentBadge(_ count: Int) -> some View {
        HStack(spacing: 4) {
            Image(systemName: "bubble.right")
                .font(.system(size: 12))
                .foregroundStyle(Color.todoistSecondary)
            Text("\(count)").font(.todoistMeta).foregroundStyle(Color.todoistSecondary)
        }
    }
}
```

### Floating Action Button (Tinted Red Shadow — Signature)

```swift
struct TodoistFAB: View {
    var action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button {
            action()
        } label: {
            Image(systemName: "plus")
                .font(.todoistFABGlyph)
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(
                    Circle().fill(pressed ? Color.todoistRedPressed : Color.todoistRed)
                )
                .shadow(color: Color.todoistRed.opacity(0.35), radius: 8, y: 6)
                .scaleEffect(pressed ? 0.94 : 1)
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(weight: .medium), trigger: pressed)
        .pressEvents(onPress: { pressed = true }, onRelease: { pressed = false })
    }
}

// Helper modifier for press detection
extension View {
    func pressEvents(onPress: @escaping () -> Void, onRelease: @escaping () -> Void) -> some View {
        modifier(PressEventsModifier(onPress: onPress, onRelease: onRelease))
    }
}
struct PressEventsModifier: ViewModifier {
    let onPress: () -> Void
    let onRelease: () -> Void
    func body(content: Content) -> some View {
        content.simultaneousGesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in onPress() }
                .onEnded   { _ in onRelease() }
        )
    }
}
```

### Quick-Add Card

```swift
struct QuickAddCard: View {
    @State private var taskText: String = ""
    @State private var selectedDate: String? = "Today"
    @State private var selectedPriority: Priority = .p4
    var onAdd: (String) -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 16) {
            Text("Add task")
                .font(.todoistInlineNav)
                .foregroundStyle(Color.todoistInk)

            TextField("Take out the trash today p1 #home", text: $taskText, axis: .vertical)
                .font(.todoistQuickAdd)
                .foregroundStyle(Color.todoistInk)
                .lineLimit(1...5)

            // Smart-parsing chip preview
            if !taskText.isEmpty {
                HStack(spacing: 8) {
                    if let d = selectedDate { smartChip(d, color: .todoistSuccess) }
                    if selectedPriority != .p4 { smartChip("P\(selectedPriority.rawValue)", color: selectedPriority.color) }
                    Spacer()
                }
            }

            Divider()

            // Action chips bar
            ScrollView(.horizontal, showsIndicators: false) {
                HStack(spacing: 8) {
                    actionChip(icon: "calendar",  label: selectedDate ?? "Date")
                    actionChip(icon: "flag",      label: "Priority")
                    actionChip(icon: "number",    label: "Project")
                    actionChip(icon: "tag",       label: "Labels")
                    actionChip(icon: "bell",      label: "Reminders")
                }
                .padding(.horizontal, 2)
            }

            HStack {
                Spacer()
                Button(action: { onAdd(taskText) }) {
                    Text("Add task")
                        .font(.todoistButton)
                        .foregroundStyle(.white)
                        .padding(.vertical, 12)
                        .padding(.horizontal, 24)
                        .background(
                            RoundedRectangle(cornerRadius: 8)
                                .fill(taskText.isEmpty ? Color.todoistSurfaceGray2 : Color.todoistRed)
                        )
                }
                .disabled(taskText.isEmpty)
                .sensoryFeedback(.success, trigger: taskText)
            }
        }
        .padding(16)
        .background(
            RoundedRectangle(cornerRadius: 16, style: .continuous)
                .fill(Color.todoistCanvas)
        )
        .shadow(color: .black.opacity(0.06), radius: 4, y: 1)
    }

    private func smartChip(_ label: String, color: Color) -> some View {
        Text(label)
            .font(.system(size: 13, weight: .medium))
            .foregroundStyle(color)
            .padding(.vertical, 4).padding(.horizontal, 8)
            .background(RoundedRectangle(cornerRadius: 6).fill(color.opacity(0.12)))
    }

    private func actionChip(icon: String, label: String) -> some View {
        HStack(spacing: 6) {
            Image(systemName: icon).font(.system(size: 12))
            Text(label).font(.system(size: 13, weight: .medium))
        }
        .foregroundStyle(Color.todoistInk)
        .padding(.vertical, 8).padding(.horizontal, 12)
        .background(
            RoundedRectangle(cornerRadius: 500)
                .strokeBorder(Color.todoistDivider, lineWidth: 1)
        )
    }
}
```

### Sidebar Row

```swift
struct SidebarRow: View {
    let icon: String
    let label: String
    let count: Int?
    let isSelected: Bool
    let isOverdue: Bool

    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: icon)
                .font(.system(size: 20, weight: .regular))
                .foregroundStyle(Color.todoistInk)
                .frame(width: 24)
            Text(label)
                .font(.todoistSidebarSys)
                .foregroundStyle(Color.todoistInk)
            Spacer()
            if let count = count, count > 0 {
                Text("\(count)")
                    .font(.todoistMeta)
                    .foregroundStyle(isOverdue ? .white : Color.todoistSecondary)
                    .padding(.vertical, 2)
                    .padding(.horizontal, 8)
                    .background(
                        Capsule().fill(isOverdue ? Color.todoistRed : .clear)
                    )
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 44)
        .background(
            isSelected
            ? Color.todoistRed.opacity(0.08)
            : Color.clear
        )
        .overlay(alignment: .leading) {
            if isSelected {
                Rectangle().fill(Color.todoistRed).frame(width: 3)
            }
        }
    }
}
```

### Section Header

```swift
struct TodoistSectionHeader: View {
    let title: String
    let isOverdue: Bool
    @State private var collapsed = false

    var body: some View {
        HStack {
            Text(title.uppercased())
                .font(.todoistSectionHdr)
                .tracking(0.6)
                .foregroundStyle(isOverdue ? Color.todoistRed : Color.todoistSecondary)
            Spacer()
            Image(systemName: collapsed ? "chevron.right" : "chevron.down")
                .font(.system(size: 12, weight: .semibold))
                .foregroundStyle(isOverdue ? Color.todoistRed : Color.todoistSecondary)
                .onTapGesture { withAnimation { collapsed.toggle() } }
        }
        .padding(.horizontal, 16)
        .frame(height: 36)
        .background(Color.todoistCanvas)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.todoistDivider).frame(height: 0.5)
        }
    }
}
```

### Project Tag (Inline)

```swift
struct ProjectTag: View {
    let name: String
    let color: Color

    var body: some View {
        HStack(spacing: 6) {
            Circle().fill(color).frame(width: 8, height: 8)
            Text("# \(name)")
                .font(.todoistMeta)
                .foregroundStyle(Color.todoistSecondary)
        }
    }
}
```

## 4. Tab Bar (iPad)

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            TodayView()    .tabItem { Label("Today",    systemImage: "calendar") }
            UpcomingView() .tabItem { Label("Upcoming", systemImage: "calendar.badge.clock") }
            SearchView()   .tabItem { Label("Search",   systemImage: "magnifyingglass") }
            BrowseView()   .tabItem { Label("Browse",   systemImage: "rectangle.grid.2x2") }
        }
        .tint(Color.todoistRed)
    }
}
```

## 5. Motion & Haptics

```swift
// Task completion
.sensoryFeedback(.impact(weight: .light), trigger: isComplete)

// FAB tap
.sensoryFeedback(.impact(weight: .medium), trigger: fabPressed)

// Quick-add commit
.sensoryFeedback(.success, trigger: taskAdded)

// Smart-parsing chip appearance
.sensoryFeedback(.selection, trigger: chipCount)

// Task complete animation
withAnimation(.easeOut(duration: 0.25)) {
    isComplete = true
}

// Quick-add sheet
.sheet(isPresented: $showQuickAdd) {
    QuickAddCard(onAdd: addTask)
        .presentationDetents([.medium, .large])
        .presentationDragIndicator(.visible)
}
```

## 6. Swipe Actions

```swift
List(tasks) { task in
    TaskRow(...)
        .swipeActions(edge: .leading) {
            Button {
                schedule(task, to: .today)
            } label: {
                Label("Schedule", systemImage: "calendar")
            }
            .tint(Color.todoistSuccess)
        }
        .swipeActions(edge: .trailing) {
            Button { postpone(task) } label: {
                Label("Postpone", systemImage: "arrow.right")
            }
            .tint(Color.todoistP2Orange)

            Button { showMore(task) } label: {
                Label("More", systemImage: "ellipsis")
            }
            .tint(Color.todoistSecondary)
        }
}
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Checkbox empty | `circle` | 22pt |
| Checkbox complete | `circle.fill` + `checkmark` | 22pt + 12pt |
| Priority flag | `flag.fill` / `flag` | 14pt |
| FAB | `plus` | 28pt |
| Today tab / inbox | `tray` / `tray.fill` | 22pt |
| Today system filter | `calendar` | 22pt |
| Upcoming | `calendar.badge.clock` | 22pt |
| Filters & Labels | `magnifyingglass` | 22pt |
| Project (sidebar) | `number` (or color dot) | 8pt dot |
| Comment count | `bubble.right` | 12pt |
| Sort / filter | `slider.horizontal.3` | 22pt |
| Menu (drawer) | `line.horizontal.3` | 22pt |
| Karma flame | `flame.fill` | 14pt |
| Schedule (swipe) | `calendar` | 18pt |
| Reminders (chip) | `bell` | 12pt |
| Labels (chip) | `tag` | 12pt |
| Project (chip) | `number` | 12pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback` and modern swipe actions); fall back to `UIImpactFeedbackGenerator` on iOS 16
- Support Dynamic Type on task content, sidebar items, large titles; fix section headers, date metadata, FAB glyph (layout-sensitive)
- VoiceOver: task row reads as `"<priority>, <content>, <date>, <project>, <comment count>"` — e.g., `"P1, Buy milk and eggs, today, Groceries, 2 comments"`; checkbox is a separate element `"Mark complete, button"` / `"Mark incomplete, button"`
- Contrast: Ink `#202020` on white meets WCAG AAA at all sizes; Secondary `#808080` meets AA at 18pt+ Bold or 14pt+ Medium — used carefully for metadata
- Reduce Motion: skip the 250ms task-completion collapse animation when `accessibilityReduceMotion` is true — fall back to an instant fade
- Dark mode: implement via `@Environment(\.colorScheme)` — swap canvas, text, surface, and divider tokens; Todoist Red brightens slightly to `#E44332` for legibility
- The 22pt checkbox has a 44pt hit area — never compress this even if the row visually appears smaller
- Priority colors are non-decorative meaning — when communicating priority to screen readers, the priority must be in the label, not implied by color alone
