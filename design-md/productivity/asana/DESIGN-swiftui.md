# Asana (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Asana's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let asanaCanvas        = Color.white                                   // #FFFFFF
    static let asanaSurfaceGray   = Color(red: 0.976, green: 0.973, blue: 0.973) // #F9F8F8
    static let asanaSurfacePressed = Color(red: 0.941, green: 0.937, blue: 0.937) // #F0EFEF
    static let asanaDivider       = Color(red: 0.929, green: 0.922, blue: 0.914) // #EDEBE9
    static let asanaCardBorder    = Color(red: 0.910, green: 0.902, blue: 0.894) // #E8E6E4

    // MARK: - Canvas & Surfaces (Dark)
    static let asanaDarkCanvas    = Color(red: 0.118, green: 0.122, blue: 0.129) // #1E1F21
    static let asanaDarkSurface1  = Color(red: 0.145, green: 0.149, blue: 0.157) // #252628
    static let asanaDarkSurface2  = Color(red: 0.180, green: 0.184, blue: 0.192) // #2E2F31
    static let asanaDarkDivider   = Color(red: 0.208, green: 0.212, blue: 0.227) // #35363A
    static let asanaDarkCardBorder = Color(red: 0.227, green: 0.231, blue: 0.247) // #3A3B3F

    // MARK: - Text
    static let asanaTextPrimary    = Color(red: 0.118, green: 0.122, blue: 0.129) // #1E1F21
    static let asanaTextSecondary  = Color(red: 0.427, green: 0.431, blue: 0.435) // #6D6E6F
    static let asanaTextTertiary   = Color(red: 0.612, green: 0.639, blue: 0.651) // #9CA3A6
    static let asanaDarkTextPrimary = Color(red: 0.961, green: 0.957, blue: 0.949) // #F5F4F2
    static let asanaDarkTextSecondary = Color(red: 0.663, green: 0.663, blue: 0.667) // #A9A9AA
    static let asanaDarkTextTertiary = Color(red: 0.435, green: 0.439, blue: 0.451) // #6F7073

    // MARK: - Brand
    static let asanaCoral         = Color(red: 0.941, green: 0.416, blue: 0.416) // #F06A6A
    static let asanaCoralPressed  = Color(red: 0.847, green: 0.357, blue: 0.357) // #D85B5B
    static let asanaActionBlue    = Color(red: 0.271, green: 0.451, blue: 0.824) // #4573D2

    // MARK: - Object Palette
    static let asanaPlum    = Color(red: 0.271, green: 0.451, blue: 0.824) // #4573D2
    static let asanaAqua    = Color(red: 0.306, green: 0.796, blue: 0.769) // #4ECBC4
    static let asanaGreen   = Color(red: 0.384, green: 0.824, blue: 0.435) // #62D26F
    static let asanaYellow  = Color(red: 0.973, green: 0.875, blue: 0.447) // #F8DF72
    static let asanaOrange  = Color(red: 0.945, green: 0.741, blue: 0.424) // #F1BD6C
    static let asanaMagenta = Color(red: 0.949, green: 0.435, blue: 0.827) // #F26FD3
    static let asanaIndigo  = Color(red: 0.353, green: 0.247, blue: 1.000) // #5A3FFF
    static let asanaCoolGray = Color(red: 0.553, green: 0.639, blue: 0.690) // #8DA3B0

    // MARK: - Semantic
    static let asanaSuccess  = Color(red: 0.384, green: 0.824, blue: 0.435) // #62D26F
    static let asanaError    = Color(red: 0.910, green: 0.220, blue: 0.310) // #E8384F
    static let asanaWarning  = Color(red: 0.973, green: 0.875, blue: 0.447) // #F8DF72
}

// Pair an object hue with its tint fill
struct AsanaTagStyle {
    let solid: Color
    let tint: Color   // ~16% of solid

    static let plum   = AsanaTagStyle(solid: .asanaPlum,   tint: .asanaPlum.opacity(0.16))
    static let aqua   = AsanaTagStyle(solid: .asanaAqua,   tint: .asanaAqua.opacity(0.16))
    static let green  = AsanaTagStyle(solid: .asanaGreen,  tint: .asanaGreen.opacity(0.16))
    static let yellow = AsanaTagStyle(solid: .asanaYellow, tint: .asanaYellow.opacity(0.20))
    static let orange = AsanaTagStyle(solid: .asanaOrange, tint: .asanaOrange.opacity(0.18))
    static let magenta = AsanaTagStyle(solid: .asanaMagenta, tint: .asanaMagenta.opacity(0.16))
}
```

## 2. Typography

Asana uses **Inter** across product surfaces. Bundle Inter TTFs via `Info.plist` / `UIAppFonts`; the system falls back to SF Pro when absent.

```swift
extension Font {
    static func asana(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .regular:  return "Inter-Regular"
            case .medium:   return "Inter-Medium"
            case .semibold: return "Inter-SemiBold"
            case .bold:     return "Inter-Bold"
            case .heavy:    return "Inter-ExtraBold"
            default:        return "Inter-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let asanaScreenTitle = Font.custom("Inter-ExtraBold", size: 32)
    static let asanaNavTitle    = Font.custom("Inter-Bold",      size: 26)
    static let asanaSection     = Font.custom("Inter-Bold",      size: 22)
    static let asanaSubsection  = Font.custom("Inter-SemiBold",  size: 18)
    static let asanaBody        = Font.custom("Inter-Regular",   size: 16)
    static let asanaTaskRow     = Font.custom("Inter-Medium",    size: 15)
    static let asanaListLabel   = Font.custom("Inter-Bold",      size: 13)
    static let asanaMeta        = Font.custom("Inter-Regular",   size: 14)
    static let asanaPill        = Font.custom("Inter-SemiBold",  size: 12)
    static let asanaButton      = Font.custom("Inter-SemiBold",  size: 15)
    static let asanaTextAction  = Font.custom("Inter-SemiBold",  size: 14)
    static let asanaAvatar      = Font.custom("Inter-Bold",      size: 10)
    static let asanaTab         = Font.custom("Inter-SemiBold",  size: 10)
}
```

## 3. Signature Components

### Task Row (with Completion Circle)

```swift
struct TaskRow: View {
    let name: String
    let projectTag: (text: String, style: AsanaTagStyle)?
    let due: (text: String, overdue: Bool)?
    let assigneeInitials: String
    let assigneeColor: Color
    @State private var done = false

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            Button {
                withAnimation(.easeOut(duration: 0.2)) { done.toggle() }
            } label: {
                ZStack {
                    Circle()
                        .strokeBorder(done ? Color.asanaSuccess : Color.asanaTextTertiary, lineWidth: 1.5)
                        .background(Circle().fill(done ? Color.asanaSuccess : Color.clear))
                        .frame(width: 20, height: 20)
                    if done {
                        Image(systemName: "checkmark")
                            .font(.system(size: 11, weight: .bold))
                            .foregroundStyle(Color.asanaDarkCanvas)
                    }
                }
                .scaleEffect(done ? 1.0 : 1.0)
            }
            .buttonStyle(.plain)
            .sensoryFeedback(.success, trigger: done)

            VStack(alignment: .leading, spacing: 6) {
                Text(name)
                    .font(.asanaTaskRow)
                    .strikethrough(done)
                    .foregroundStyle(done ? Color.asanaTextTertiary : Color.asanaTextPrimary)

                HStack(spacing: 8) {
                    if let projectTag {
                        PillView(text: projectTag.text, style: projectTag.style)
                    }
                    if let due {
                        PillView(
                            text: due.text,
                            style: due.overdue
                                ? AsanaTagStyle(solid: .asanaError, tint: .asanaError.opacity(0.18))
                                : AsanaTagStyle(solid: .asanaOrange, tint: .asanaOrange.opacity(0.18))
                        )
                    }
                }
            }
            Spacer()
            Text(assigneeInitials)
                .font(.asanaAvatar)
                .foregroundStyle(.white)
                .frame(width: 22, height: 22)
                .background(Circle().fill(assigneeColor))
        }
        .padding(.horizontal, 18)
        .padding(.vertical, 11)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.asanaDivider).frame(height: 1)
        }
    }
}

struct PillView: View {
    let text: String
    let style: AsanaTagStyle
    var body: some View {
        Text(text)
            .font(.asanaPill)
            .foregroundStyle(style.solid)
            .padding(.vertical, 3)
            .padding(.horizontal, 9)
            .background(Capsule().fill(style.tint))
    }
}
```

### Section Header (collapsible)

```swift
struct SectionHeader: View {
    let title: String
    let count: Int
    @Binding var expanded: Bool

    var body: some View {
        Button {
            withAnimation(.easeOut(duration: 0.2)) { expanded.toggle() }
        } label: {
            HStack(spacing: 8) {
                Image(systemName: "chevron.right")
                    .font(.system(size: 12, weight: .bold))
                    .foregroundStyle(Color.asanaTextSecondary)
                    .rotationEffect(.degrees(expanded ? 90 : 0))
                Text(title)
                    .font(.asanaListLabel)
                    .foregroundStyle(Color.asanaTextPrimary)
                Text("\(count)")
                    .font(.asanaPill)
                    .foregroundStyle(Color.asanaTextTertiary)
                Spacer()
            }
            .padding(.horizontal, 18)
            .padding(.top, 14)
            .padding(.bottom, 8)
        }
        .buttonStyle(.plain)
    }
}
```

### Status Badge & Status Update Card

```swift
enum ProjectStatus {
    case onTrack, atRisk, offTrack, complete
    var label: String {
        switch self {
        case .onTrack: "On track"; case .atRisk: "At risk"
        case .offTrack: "Off track"; case .complete: "Complete"
        }
    }
    var color: Color {
        switch self {
        case .onTrack: .asanaAqua; case .atRisk: .asanaYellow
        case .offTrack: .asanaError; case .complete: .asanaSuccess
        }
    }
}

struct StatusUpdateCard: View {
    let status: ProjectStatus
    let postedDate: String
    let title: String
    let body: String

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack {
                Text(status.label)
                    .font(.asana(12, weight: .bold))
                    .foregroundStyle(status.color)
                    .padding(.vertical, 6).padding(.horizontal, 14)
                    .background(Capsule().fill(status.color.opacity(0.18)))
                Spacer()
                Text(postedDate).font(.asanaMeta).foregroundStyle(Color.asanaTextSecondary)
            }
            Text(title).font(.asanaSection).foregroundStyle(Color.asanaTextPrimary)
            Text(body).font(.asanaBody).foregroundStyle(Color.asanaTextPrimary)
        }
        .padding(18)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.asanaCanvas))
        .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.asanaDivider, lineWidth: 1))
    }
}
```

### Floating Action Button

```swift
struct CreateTaskFAB: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: "plus")
                .font(.system(size: 24, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(Circle().fill(Color.asanaCoral))
                .shadow(color: Color.asanaCoral.opacity(0.45), radius: 20, y: 8)
        }
        .buttonStyle(.plain)
    }
}
```

### Board Column + Card

```swift
struct BoardColumn<Content: View>: View {
    let title: String
    @ViewBuilder let content: () -> Content
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text(title.uppercased())
                .font(.asana(12, weight: .bold))
                .foregroundStyle(Color.asanaTextSecondary)
                .padding(.bottom, 2)
            content()
        }
        .padding(12)
        .frame(width: 280, alignment: .leading)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.asanaSurfaceGray))
    }
}

struct BoardCard: View {
    let title: String
    let tag: (text: String, style: AsanaTagStyle)
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text(title).font(.asana(13, weight: .medium)).foregroundStyle(Color.asanaTextPrimary)
            PillView(text: tag.text, style: tag.style)
        }
        .padding(12)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(RoundedRectangle(cornerRadius: 10).fill(Color.asanaCanvas))
        .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.asanaCardBorder, lineWidth: 1))
        .shadow(color: .black.opacity(0.06), radius: 3, y: 1)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct AsanaTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house") }
            MyTasksView().tabItem { Label("My Tasks", systemImage: "checkmark.circle") }
            InboxView().tabItem { Label("Inbox", systemImage: "bell") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
        }
        .tint(.asanaCoral) // active = coral, no pill indicator
    }
}
```

## 5. Motion

```swift
// Task complete — circle bounce + checkmark + haptic
withAnimation(.spring(response: 0.2, dampingFraction: 0.55)) { done.toggle() }
.sensoryFeedback(.success, trigger: done)

// Section collapse/expand
withAnimation(.easeOut(duration: 0.2)) { expanded.toggle() }
// chevron: .rotationEffect(.degrees(expanded ? 90 : 0))

// FAB → new-task sheet
.sheet(isPresented: $showNewTask) { NewTaskSheet() }   // 280ms ease-out rise

// Board card drag
// DragGesture 1:1; neighbors animate .easeOut(duration: 0.2); drop .easeOut(0.15)
.sensoryFeedback(.impact(flexibility: .soft), trigger: dragStarted)

// View tab underline slide
withAnimation(.easeOut(duration: 0.2)) { selectedTab = tapped }

// Celebration creature (section fully complete)
// Animate an Image flying x: -80 → screenWidth+80 over 1.2s, then opacity → 0
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| My Tasks (tab) | `checkmark.circle` / `checkmark.circle.fill` | 22pt |
| Inbox (tab) | `bell` / `bell.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| Task incomplete | `circle` | 20pt |
| Task complete | `checkmark.circle.fill` | 20pt |
| Add / FAB | `plus` | 24pt |
| Section caret | `chevron.right` (rotate 90°) | 12pt |
| Due date | `calendar` | 14pt |
| Assignee (empty) | `person.crop.circle` | 22pt |
| Subtask | `arrow.turn.down.right` | 14pt |
| Attachment | `paperclip` | 16pt |
| Comment | `bubble.left` | 16pt |
| More actions | `ellipsis` | 20pt |
| Back | `chevron.left` | 24pt |
| Filter | `line.3.horizontal.decrease` | 16pt |
| Board view | `rectangle.split.3x1` | 18pt |
| Calendar view | `calendar` | 18pt |
| Timeline view | `chart.bar.xaxis` | 18pt |

## 7. Dark Mode

```swift
struct AsanaTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.asanaDarkCanvas : Color.asanaCanvas)
            .foregroundStyle(scheme == .dark ? Color.asanaDarkTextPrimary : Color.asanaTextPrimary)
    }
}

extension View {
    func asanaTheme() -> some View { modifier(AsanaTheme()) }
}
```

Dark mode keeps `asanaCoral` and `asanaSuccess` identical (they read well on dark). Canvas is the signature charcoal `#1E1F21`, NOT pure black. Gray shadows nearly vanish on dark, so board cards use a 1pt `asanaDarkCardBorder` outline for separation while the FAB keeps its coral glow. Object-tag tints step up to ~25% opacity on dark so they still register.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UINotificationFeedbackGenerator().notificationOccurred(.success)` for the complete-task haptic)
- Bundle Inter TTFs via `Info.plist` `UIAppFonts` — SIL OFL licensed for free distribution
- Dynamic Type: support on screen title, nav title, section headers, body, task-row names, comments; keep pills, tab labels, avatar initials, list section labels at FIXED sizes
- VoiceOver: label the completion circle "Mark complete: {task name}" / "Completed: {task name}"; label task rows with name + project + due + assignee; expose swipe actions via `.accessibilityAction`
- The completion circle and the row tap are separate accessibility actions — circle = toggle complete, row = open detail
- Color contrast: `#1E1F21` on `#FFFFFF` and `#F5F4F2` on `#1E1F21` pass WCAG AA; tag pills use a saturated text on a light tint — verify yellow (`#F8DF72` text needs a dark-on-light or boosted tint to pass)
- Reduce Motion: disable the completion-circle bounce and the celebration creature (use a simple crossfade fill); keep the success haptic
- Dark mode: invert per the `asanaDark*` palette — `#1E1F21`, NOT true black; `#1E1F21` text → `#F5F4F2`; bump tag tints to ~25%; add 1pt `asanaDarkCardBorder` to cards since shadows vanish
