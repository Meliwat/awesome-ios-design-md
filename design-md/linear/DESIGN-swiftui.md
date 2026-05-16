# Linear (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Linear's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let linearCanvas    = Color(red: 0.031, green: 0.035, blue: 0.039) // #08090A
    static let linearSurface1  = Color(red: 0.078, green: 0.082, blue: 0.086) // #141516
    static let linearSurface2  = Color(red: 0.110, green: 0.114, blue: 0.122) // #1C1D1F
    static let linearSurface3  = Color(red: 0.137, green: 0.141, blue: 0.157) // #232428
    static let linearDivider   = Color(red: 0.137, green: 0.145, blue: 0.165) // #23252A

    // MARK: - Text
    static let linearTextPrimary   = Color(red: 0.969, green: 0.973, blue: 0.973) // #F7F8F8
    static let linearTextSecondary = Color(red: 0.541, green: 0.561, blue: 0.596) // #8A8F98
    static let linearTextTertiary  = Color(red: 0.361, green: 0.373, blue: 0.416) // #5C5F6A

    // MARK: - Brand
    static let linearPurple        = Color(red: 0.369, green: 0.416, blue: 0.824) // #5E6AD2
    static let linearPurplePressed = Color(red: 0.310, green: 0.345, blue: 0.722) // #4F58B8
    static let linearPurpleTint    = Color(red: 0.369, green: 0.416, blue: 0.824).opacity(0.14)

    // MARK: - Status & Semantic
    static let linearProgress = Color(red: 0.949, green: 0.812, blue: 0.298) // #F2C94C
    static let linearSuccess  = Color(red: 0.298, green: 0.718, blue: 0.510) // #4CB782
    static let linearError    = Color(red: 0.922, green: 0.341, blue: 0.341) // #EB5757
}
```

## 2. Typography

Linear uses Inter. Bundle the variable TTF via `Info.plist` (`UIAppFonts`) or register at runtime. Identifiers and shortcuts use a monospace face (`SF Mono` is the system fallback). Always enable tabular numerics on counts.

```swift
extension Font {
    static let linearTitleLarge = Font.custom("Inter-SemiBold", size: 28)
    static let linearViewTitle  = Font.custom("Inter-SemiBold", size: 22)
    static let linearSection    = Font.custom("Inter-SemiBold", size: 17)
    static let linearIssueTitle = Font.custom("Inter-Medium",   size: 15)
    static let linearBody       = Font.custom("Inter-Regular",  size: 15)
    static let linearMetadata   = Font.custom("Inter-Regular",  size: 13)
    static let linearLabelPill  = Font.custom("Inter-Medium",   size: 12)
    static let linearCommandRow = Font.custom("Inter-Medium",   size: 14)
    static let linearButton     = Font.custom("Inter-SemiBold", size: 14)
    static let linearTiny       = Font.custom("Inter-SemiBold", size: 11)

    // Monospace — identifiers and shortcuts
    static let linearMonoID       = Font.system(size: 13, weight: .medium, design: .monospaced)
    static let linearMonoShortcut = Font.system(size: 12, weight: .medium, design: .monospaced)
}

// System fallback if Inter is unavailable
extension Font {
    static func linear(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}

// Apply tabular numerics to any count / identifier Text
extension View {
    func linearTabular() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Status Glyph

```swift
enum IssueStatus { case backlog, todo, inProgress, inReview, done, canceled }

struct StatusGlyph: View {
    let status: IssueStatus
    var size: CGFloat = 16

    var body: some View {
        ZStack {
            switch status {
            case .backlog:
                Circle().strokeBorder(Color.linearTextSecondary, style: StrokeStyle(lineWidth: 1.5, dash: [2, 2]))
            case .todo:
                Circle().strokeBorder(Color.linearTextSecondary, lineWidth: 1.5)
            case .inProgress:
                Circle().strokeBorder(Color.linearProgress, lineWidth: 1.5)
                Circle().trim(from: 0, to: 0.5).fill(Color.linearProgress).rotationEffect(.degrees(-90))
            case .inReview:
                Circle().strokeBorder(Color.linearPurple, lineWidth: 1.5)
                Circle().trim(from: 0, to: 0.66).stroke(Color.linearPurple, lineWidth: 1.5).rotationEffect(.degrees(-90))
            case .done:
                Circle().fill(Color.linearPurple)
                Image(systemName: "checkmark").font(.system(size: size * 0.5, weight: .bold)).foregroundStyle(.white)
            case .canceled:
                Circle().fill(Color.linearTextTertiary)
                Image(systemName: "xmark").font(.system(size: size * 0.5, weight: .bold)).foregroundStyle(.white)
            }
        }
        .frame(width: size, height: size)
        .animation(.spring(response: 0.15, dampingFraction: 0.85), value: status)
    }
}
```

### Issue Row (the signature element)

```swift
struct IssueRow: View {
    let identifier: String
    let title: String
    let status: IssueStatus
    let priority: Int        // 0 none … 4 urgent
    let assignee: String
    var isSelected: Bool = false

    var body: some View {
        HStack(spacing: 10) {
            StatusGlyph(status: status)

            Text(identifier)
                .font(.linearMonoID)
                .foregroundStyle(.linearTextSecondary)
                .linearTabular()

            Text(title)
                .font(.linearIssueTitle)
                .foregroundStyle(.linearTextPrimary)
                .lineLimit(1)

            Spacer(minLength: 8)

            PriorityBars(level: priority)

            Circle()
                .fill(Color.linearSurface3)
                .frame(width: 20, height: 20)
                .overlay(Text(String(assignee.prefix(1)))
                    .font(.system(size: 10, weight: .semibold))
                    .foregroundStyle(.linearTextSecondary))
        }
        .padding(.horizontal, 16)
        .frame(height: 44)
        .background(isSelected ? Color.linearPurpleTint : .clear)
        .overlay(alignment: .leading) {
            if isSelected {
                Rectangle().fill(Color.linearPurple).frame(width: 2)
            }
        }
        .contentShape(Rectangle())
    }
}

struct PriorityBars: View {
    let level: Int  // 0 none, 1 low, 2 med, 3 high, 4 urgent
    var body: some View {
        if level == 4 {
            RoundedRectangle(cornerRadius: 2)
                .fill(Color.linearProgress)
                .frame(width: 14, height: 14)
                .overlay(Text("!").font(.system(size: 10, weight: .bold)).foregroundStyle(.black))
        } else {
            HStack(alignment: .bottom, spacing: 2) {
                ForEach(0..<3) { i in
                    RoundedRectangle(cornerRadius: 1)
                        .fill(i < level ? Color.linearTextSecondary : Color.linearTextTertiary.opacity(0.4))
                        .frame(width: 3, height: CGFloat(6 + i * 4))
                }
            }
            .frame(width: 14, height: 14, alignment: .bottom)
        }
    }
}
```

### Primary Button

```swift
struct LinearPrimaryButton: View {
    let title: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.linearButton)
                .foregroundStyle(.white)
                .padding(.vertical, 8)
                .padding(.horizontal, 14)
                .frame(height: 32)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.linearPurple))
        }
        .buttonStyle(LinearPressable())
        .sensoryFeedback(.impact(weight: .light), trigger: title)
    }
}

struct LinearPressable: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .brightness(configuration.isPressed ? -0.05 : 0)
            .animation(.spring(response: 0.18, dampingFraction: 0.85), value: configuration.isPressed)
    }
}
```

### Command Menu (Cmd+K)

```swift
struct CommandMenu: View {
    @Binding var query: String
    let rows: [(icon: String, label: String, shortcut: String)]
    @State private var focused = 0

    var body: some View {
        VStack(spacing: 0) {
            HStack(spacing: 10) {
                Image(systemName: "magnifyingglass")
                    .font(.system(size: 16))
                    .foregroundStyle(.linearTextSecondary)
                TextField("Type a command or search…", text: $query)
                    .font(.linearCommandRow)
                    .foregroundStyle(.linearTextPrimary)
                    .tint(.linearPurple)
            }
            .padding(.horizontal, 16)
            .frame(height: 44)

            Divider().overlay(Color.linearDivider)

            ScrollView {
                LazyVStack(spacing: 0) {
                    ForEach(Array(rows.enumerated()), id: \.offset) { i, row in
                        HStack(spacing: 10) {
                            Image(systemName: row.icon)
                                .font(.system(size: 15))
                                .foregroundStyle(.linearTextSecondary)
                                .frame(width: 18)
                            Text(row.label)
                                .font(.linearCommandRow)
                                .foregroundStyle(.linearTextPrimary)
                            Spacer()
                            Text(row.shortcut)
                                .font(.linearMonoShortcut)
                                .foregroundStyle(.linearTextSecondary)
                        }
                        .padding(.horizontal, 16)
                        .frame(height: 40)
                        .background(i == focused ? Color.linearPurpleTint : .clear)
                    }
                }
            }
        }
        .frame(maxWidth: 560)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.linearSurface1))
        .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.linearDivider, lineWidth: 1))
        .shadow(color: .black.opacity(0.6), radius: 64, y: 24)
        .transition(.scale(scale: 0.96).combined(with: .opacity))
    }
}
```

### Cycle Progress Bar

```swift
struct CycleProgressBar: View {
    let completed: Int
    let total: Int
    let daysLeft: Int

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.linearSurface2)
                    Capsule().fill(Color.linearPurple)
                        .frame(width: geo.size.width * CGFloat(completed) / CGFloat(max(total, 1)))
                }
            }
            .frame(height: 6)

            Text("\(completed) of \(total) completed · \(daysLeft) days left")
                .font(.linearMetadata)
                .foregroundStyle(.linearTextSecondary)
                .linearTabular()
        }
    }
}
```

## 4. Sidebar Navigation

Linear has **no bottom tab bar**. Navigation is a slide-over sidebar. On iPad use `NavigationSplitView`; on iPhone present the sidebar as a leading drawer.

```swift
struct RootView: View {
    @State private var showSidebar = false

    var body: some View {
        ZStack(alignment: .leading) {
            IssueListView()

            if showSidebar {
                Color.black.opacity(0.5).ignoresSafeArea()
                    .onTapGesture { withAnimation { showSidebar = false } }

                Sidebar()
                    .frame(width: 280)
                    .background(Color.linearCanvas)
                    .transition(.move(edge: .leading))
            }
        }
        .animation(.spring(response: 0.35, dampingFraction: 0.9), value: showSidebar)
    }
}

struct Sidebar: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 2) {
            SidebarItem(icon: "tray.fill",   label: "Inbox",     active: true)
            SidebarItem(icon: "circle.dashed", label: "My Issues", active: false)
            Divider().overlay(Color.linearDivider).padding(.vertical, 8)
            SidebarItem(icon: "person.2.fill", label: "Engineering", active: false)
        }
        .padding(12)
        .frame(maxHeight: .infinity, alignment: .top)
    }
}

struct SidebarItem: View {
    let icon: String; let label: String; let active: Bool
    var body: some View {
        HStack(spacing: 8) {
            Image(systemName: icon)
                .font(.system(size: 16))
                .foregroundStyle(active ? Color.linearPurple : .linearTextSecondary)
                .frame(width: 18)
            Text(label).font(.linearCommandRow)
                .foregroundStyle(active ? Color.linearTextPrimary : .linearTextSecondary)
            Spacer()
        }
        .padding(.horizontal, 8)
        .frame(height: 32)
        .background(active ? Color.linearPurpleTint : .clear)
        .clipShape(RoundedRectangle(cornerRadius: 6))
    }
}
```

## 5. Motion

Linear's motion budget is tiny — every transition should feel instant.

```swift
// Command menu open: scale 0.96 → 1 + opacity over 120ms
withAnimation(.easeOut(duration: 0.12)) { showCommandMenu = true }

// Row selection: color cross-fade only, no movement
.animation(.easeInOut(duration: 0.09), value: isSelected)

// Group collapse / expand
.animation(.spring(response: 0.3, dampingFraction: 0.85), value: isCollapsed)

// Status change glyph morph
.animation(.spring(response: 0.15, dampingFraction: 0.85), value: status)

// Haptic on primary action / status change / command execute
.sensoryFeedback(.impact(weight: .light), trigger: commitCount)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Backlog status | drawn (dashed circle) | 16pt |
| Todo status | `circle` | 16pt |
| In Progress | drawn (half-pie) | 16pt |
| Done | `checkmark` in filled circle | 16pt |
| Canceled | `xmark` in filled circle | 16pt |
| Command search | `magnifyingglass` | 16pt |
| New issue | `square.and.pencil` | 18pt |
| Filter | `line.3.horizontal.decrease` | 18pt |
| Display options | `slider.horizontal.3` | 18pt |
| Sidebar toggle | `sidebar.left` | 18pt |
| Inbox | `tray.fill` | 16pt |
| My Issues | `circle.dashed` | 16pt |
| Team | `person.2.fill` | 16pt |
| Cycle | `arrow.triangle.2.circlepath` | 16pt |
| More | `ellipsis` | 18pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on issue titles, body, and metadata — pin status/priority glyphs (16/14pt) and mono shortcuts (12pt) which are layout-critical
- VoiceOver: combine the issue row with `.accessibilityElement(children: .combine)` and read "ENG-1423, Fix flaky auth redirect, In Progress, High priority, assigned to Sam"
- The command menu must be fully keyboard-navigable: arrow keys move focus, Return executes, Escape dismisses — wire `onKeyPress` (iOS 17+) or hardware-keyboard `UIKeyCommand`
- Contrast: `#8A8F98` secondary on `#08090A` passes WCAG AA at 13pt+ — validate at 11–12pt label sizes and bump toward `#A0A4AD` if targeting strict compliance
- Respect `accessibilityReduceMotion`: drop the command-menu scale, keep the opacity fade only
