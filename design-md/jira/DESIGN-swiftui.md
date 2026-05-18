# Jira (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Jira's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand (Interactive)
    static let jiraBlue        = Color(red: 0.000, green: 0.322, blue: 0.800) // #0052CC
    static let jiraBlueBold    = Color(red: 0.094, green: 0.408, blue: 0.859) // #1868DB
    static let jiraBluePressed = Color(red: 0.035, green: 0.196, blue: 0.424) // #09326C
    static let jiraLink        = Color(red: 0.047, green: 0.400, blue: 0.894) // #0C66E4
    static let jiraLinkDark    = Color(red: 0.341, green: 0.616, blue: 1.000) // #579DFF
    static let jiraInkNavy     = Color(red: 0.090, green: 0.169, blue: 0.302) // #172B4D

    // MARK: - Canvas & Surfaces (Light)
    static let jiraCanvas      = Color.white                                   // #FFFFFF
    static let jiraSunken      = Color(red: 0.969, green: 0.973, blue: 0.976) // #F7F8F9
    static let jiraSurfaceHover = Color(red: 0.945, green: 0.949, blue: 0.957) // #F1F2F4
    static let jiraDivider     = Color(red: 0.035, green: 0.118, blue: 0.259).opacity(0.14) // #091E4224

    // MARK: - Canvas & Surfaces (Dark)
    static let jiraDarkCanvas   = Color(red: 0.114, green: 0.129, blue: 0.145) // #1D2125
    static let jiraDarkSurface1 = Color(red: 0.133, green: 0.153, blue: 0.169) // #22272B
    static let jiraDarkSurface2 = Color(red: 0.173, green: 0.200, blue: 0.227) // #2C333A
    static let jiraDarkSurface3 = Color(red: 0.220, green: 0.255, blue: 0.290) // #38414A
    static let jiraDarkDivider  = Color(red: 0.220, green: 0.255, blue: 0.290) // #38414A

    // MARK: - Text
    static let jiraTextPrimary    = Color(red: 0.090, green: 0.169, blue: 0.302) // #172B4D
    static let jiraTextSubtle     = Color(red: 0.267, green: 0.329, blue: 0.435) // #44546F
    static let jiraTextSubtlest   = Color(red: 0.384, green: 0.435, blue: 0.525) // #626F86
    static let jiraDarkTextPrimary  = Color(red: 0.780, green: 0.820, blue: 0.859) // #C7D1DB
    static let jiraDarkTextSubtle   = Color(red: 0.624, green: 0.678, blue: 0.737) // #9FADBC
    static let jiraDarkTextSubtlest = Color(red: 0.451, green: 0.518, blue: 0.588) // #738496

    // MARK: - Issue-Type Accents
    static let jiraStoryGreen = Color(red: 0.122, green: 0.518, blue: 0.353) // #1F845A
    static let jiraBugRed     = Color(red: 0.788, green: 0.216, blue: 0.173) // #C9372C
    static let jiraTaskBlue   = Color(red: 0.094, green: 0.408, blue: 0.859) // #1868DB
    static let jiraEpicPurple = Color(red: 0.510, green: 0.439, blue: 0.859) // #8270DB
    static let jiraSubtaskGray = Color(red: 0.369, green: 0.424, blue: 0.515) // #5E6C84

    // MARK: - Semantic / Status (Light fill / text)
    static let jiraNeutralBg  = Color(red: 0.863, green: 0.875, blue: 0.894) // #DCDFE4
    static let jiraInfoBg     = Color(red: 0.914, green: 0.949, blue: 1.000) // #E9F2FF
    static let jiraInfoText   = Color(red: 0.000, green: 0.333, blue: 0.800) // #0055CC
    static let jiraSuccessBg  = Color(red: 0.863, green: 1.000, blue: 0.945) // #DCFFF1
    static let jiraSuccessText = Color(red: 0.129, green: 0.431, blue: 0.306) // #216E4E
    static let jiraWarnBg     = Color(red: 1.000, green: 0.969, blue: 0.839) // #FFF7D6
    static let jiraWarnText   = Color(red: 0.498, green: 0.373, blue: 0.004) // #7F5F01
    static let jiraDangerBg   = Color(red: 1.000, green: 0.925, blue: 0.922) // #FFECEB
    static let jiraDangerText = Color(red: 0.682, green: 0.165, blue: 0.098) // #AE2A19

    // MARK: - Semantic / Status (Dark fill / text)
    static let jiraInfoBgD    = Color(red: 0.035, green: 0.161, blue: 0.341) // #092957
    static let jiraInfoTextD  = Color(red: 0.561, green: 0.722, blue: 0.965) // #8FB8F6
    static let jiraSuccessBgD = Color(red: 0.110, green: 0.200, blue: 0.161) // #1C3329
    static let jiraSuccessTextD = Color(red: 0.494, green: 0.886, blue: 0.722) // #7EE2B8
    static let jiraDangerBgD  = Color(red: 0.365, green: 0.122, blue: 0.102) // #5D1F1A
    static let jiraDangerTextD = Color(red: 0.992, green: 0.592, blue: 0.569) // #FD9891

    // MARK: - Avatar / Label Palette
    static let jiraAvPurple  = Color(red: 0.369, green: 0.302, blue: 0.698) // #5E4DB2
    static let jiraAvTeal    = Color(red: 0.114, green: 0.498, blue: 0.549) // #1D7F8C
    static let jiraAvMagenta = Color(red: 0.580, green: 0.239, blue: 0.451) // #943D73
}
```

## 2. Typography

Jira ships **Atlassian Sans** + **Atlassian Mono**. On iOS, bundle the TTFs via `Info.plist`/`UIAppFonts`, or use the closest free analog **Inter**. Enable tabular numerals everywhere counts and keys appear.

```swift
extension Font {
    // Default = Atlassian Sans (substitute "Inter" if not bundled)
    static func jira(_ size: CGFloat, _ weight: Font.Weight = .regular) -> Font {
        Font.custom("AtlassianSans", size: size).weight(weight)
    }

    static let jiraScreenTitle = Font.custom("AtlassianSans", size: 30).weight(.bold)
    static let jiraBoardTitle  = Font.custom("AtlassianSans", size: 22).weight(.bold)
    static let jiraIssueTitle  = Font.custom("AtlassianSans", size: 20).weight(.semibold)
    static let jiraSubsection  = Font.custom("AtlassianSans", size: 16).weight(.semibold)
    static let jiraBody        = Font.custom("AtlassianSans", size: 16).weight(.regular)
    static let jiraCardSummary = Font.custom("AtlassianSans", size: 15).weight(.medium)
    static let jiraMeta        = Font.custom("AtlassianSans", size: 14).weight(.regular)
    static let jiraFieldLabel  = Font.custom("AtlassianSans", size: 12).weight(.semibold)
    static let jiraColumnHead  = Font.custom("AtlassianSans", size: 12).weight(.bold)
    static let jiraLozenge     = Font.custom("AtlassianSans", size: 11).weight(.bold)
    static let jiraIssueKey    = Font.custom("AtlassianSans", size: 11).weight(.bold)
    static let jiraTab         = Font.custom("AtlassianSans", size: 10).weight(.medium)
    static let jiraCode        = Font.custom("AtlassianMono", size: 13).weight(.regular)
}

// Tabular numerals helper — apply to keys, points, counts
extension View {
    func jiraTabularNums() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Status Lozenge

```swift
enum JiraStatus { case todo, inProgress, done, blocked, epic

    var fill: Color {
        switch self {
        case .todo:       return .jiraNeutralBg
        case .inProgress: return .jiraInfoBg
        case .done:       return .jiraSuccessBg
        case .blocked:    return .jiraDangerBg
        case .epic:       return Color(red: 0.953, green: 0.941, blue: 1.0)
        }
    }
    var text: Color {
        switch self {
        case .todo:       return .jiraTextSubtle
        case .inProgress: return .jiraInfoText
        case .done:       return .jiraSuccessText
        case .blocked:    return .jiraDangerText
        case .epic:       return Color(red: 0.369, green: 0.302, blue: 0.698)
        }
    }
    var label: String {
        switch self {
        case .todo: "TO DO"; case .inProgress: "IN PROGRESS"
        case .done: "DONE"; case .blocked: "BLOCKED"; case .epic: "EPIC"
        }
    }
}

struct StatusLozenge: View {
    let status: JiraStatus
    var dropdown: Bool = false

    var body: some View {
        HStack(spacing: 4) {
            Text(status.label)
                .font(.jiraLozenge)
                .tracking(0.4)
            if dropdown {
                Image(systemName: "chevron.down").font(.system(size: 9, weight: .bold))
            }
        }
        .foregroundStyle(status.text)
        .padding(.vertical, 4)
        .padding(.horizontal, 8)
        .background(RoundedRectangle(cornerRadius: 3).fill(status.fill))
    }
}
```

### Issue-Type Icon

```swift
enum JiraIssueType { case story, bug, task, epic, subtask

    var color: Color {
        switch self {
        case .story: .jiraStoryGreen; case .bug: .jiraBugRed
        case .task: .jiraTaskBlue; case .epic: .jiraEpicPurple
        case .subtask: .jiraSubtaskGray
        }
    }
    var symbol: String {
        switch self {
        case .story: "bookmark.fill"; case .bug: "ladybug.fill"
        case .task: "checkmark"; case .epic: "bolt.fill"
        case .subtask: "arrow.turn.down.right"
        }
    }
}

struct IssueTypeIcon: View {
    let type: JiraIssueType
    var size: CGFloat = 16

    var body: some View {
        RoundedRectangle(cornerRadius: 3)
            .fill(type.color)
            .frame(width: size, height: size)
            .overlay(
                Image(systemName: type.symbol)
                    .font(.system(size: size * 0.6, weight: .bold))
                    .foregroundStyle(.white)
            )
    }
}
```

### Issue Card

```swift
struct IssueCard: View {
    let type: JiraIssueType
    let key: String
    let summary: String
    let labels: [(String, Color, Color)] // text, bg, fg
    let points: Int
    let assignee: (String, Color)        // initials, bg

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text(summary)
                .font(.jiraCardSummary)
                .foregroundStyle(Color.jiraTextPrimary)
                .lineLimit(2)

            if !labels.isEmpty {
                HStack(spacing: 5) {
                    ForEach(labels.indices, id: \.self) { i in
                        Text(labels[i].0.uppercased())
                            .font(.system(size: 10, weight: .bold))
                            .tracking(0.3)
                            .foregroundStyle(labels[i].2)
                            .padding(.vertical, 2).padding(.horizontal, 7)
                            .background(RoundedRectangle(cornerRadius: 3).fill(labels[i].1))
                    }
                }
                .padding(.top, 10)
            }

            HStack {
                HStack(spacing: 8) {
                    IssueTypeIcon(type: type)
                    Text(key).font(.jiraIssueKey).monospacedDigit()
                        .foregroundStyle(Color.jiraTextSubtlest)
                }
                Spacer()
                HStack(spacing: 8) {
                    Text("\(points)")
                        .font(.system(size: 11, weight: .bold)).monospacedDigit()
                        .foregroundStyle(Color.jiraTextSubtle)
                        .frame(minWidth: 18, minHeight: 18)
                        .padding(.horizontal, 5)
                        .background(Capsule().fill(Color(red: 0.867, green: 0.882, blue: 0.902)))
                    Circle().fill(assignee.1).frame(width: 22, height: 22)
                        .overlay(Text(assignee.0).font(.system(size: 10, weight: .bold)).foregroundStyle(.white))
                }
            }
            .padding(.top, 12)
        }
        .padding(12)
        .background(
            RoundedRectangle(cornerRadius: 8)
                .fill(Color.jiraCanvas)
                .shadow(color: Color.jiraInkNavy.opacity(0.13), radius: 1, y: 1)
        )
        .overlay(RoundedRectangle(cornerRadius: 8).strokeBorder(Color.jiraDivider, lineWidth: 1))
    }
}
```

### Board Column

```swift
struct BoardColumn<Card: View>: View {
    let name: String
    let count: Int
    @ViewBuilder let cards: () -> Card

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(spacing: 8) {
                Text(name.uppercased()).font(.jiraColumnHead).tracking(0.6)
                    .foregroundStyle(Color.jiraTextSubtlest)
                Text("\(count)").font(.system(size: 11, weight: .bold)).monospacedDigit()
                    .foregroundStyle(Color.jiraTextSubtlest)
                    .padding(.horizontal, 7).padding(.vertical, 1)
                    .background(Capsule().fill(Color.jiraSurfaceHover))
            }
            .padding(.bottom, 10)

            ScrollView(showsIndicators: false) {
                VStack(spacing: 8) { cards() }
            }
        }
        .frame(width: 248)
    }
}
```

### Issue Detail Field Row

```swift
struct FieldRow<Value: View>: View {
    let label: String
    @ViewBuilder let value: () -> Value

    var body: some View {
        HStack(alignment: .center) {
            Text(label.uppercased())
                .font(.jiraFieldLabel).tracking(0.4)
                .foregroundStyle(Color.jiraTextSubtle)
                .frame(width: 110, alignment: .leading)
            value()
            Spacer()
        }
        .frame(minHeight: 44)
        .overlay(Divider().background(Color.jiraDivider), alignment: .bottom)
    }
}
```

### Sprint Pill Strip

```swift
struct SprintPill: View {
    let title: String
    let active: Bool
    var body: some View {
        Text(title)
            .font(.system(size: 12, weight: .semibold))
            .foregroundStyle(active ? .white : Color.jiraTextSubtle)
            .padding(.vertical, 5).padding(.horizontal, 12)
            .background(RoundedRectangle(cornerRadius: 6)
                .fill(active ? Color.jiraBlue : Color.jiraSurfaceHover))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct JiraTabView: View {
    var body: some View {
        TabView {
            BoardsView().tabItem { Label("Boards", systemImage: "square.grid.2x2") }
            BacklogView().tabItem { Label("Backlog", systemImage: "list.bullet.indent") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            NotificationsView().tabItem { Label("Notifications", systemImage: "bell") }
            ProfileView().tabItem { Label("You", systemImage: "person.crop.circle") }
        }
        .tint(.jiraBlue) // active is Jira Blue — no pill indicator on iOS
    }
}
```

## 5. Motion

```swift
// Card drag — long-press then lift
@GestureState private var dragging = false
.scaleEffect(dragging ? 1.02 : 1.0)
.rotationEffect(.degrees(dragging ? 2 : 0))
.shadow(color: Color.jiraInkNavy.opacity(dragging ? 0.15 : 0), radius: dragging ? 8 : 1, y: dragging ? 4 : 1)
.animation(.easeOut(duration: 0.2), value: dragging)
// Drop: .easeOut(duration: 0.15) + medium haptic

// Status lozenge dropdown
.transition(.opacity.combined(with: .move(edge: .top))) // 150ms ease-out

// Section collapse (backlog)
withAnimation(.easeOut(duration: 0.2)) { expanded.toggle() }
// chevron: .rotationEffect(.degrees(expanded ? 90 : 0))

// Sprint progress bar fill on load
.animation(.easeOut(duration: 0.4), value: didLoad)

// Haptics
import UIKit
UIImpactFeedbackGenerator(style: .medium).impactOccurred()  // card drop, status change
UIImpactFeedbackGenerator(style: .light).impactOccurred()   // filter toggle, collapse
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Boards (tab) | `square.grid.2x2` / `.fill` | 24pt |
| Backlog (tab) | `list.bullet.indent` | 24pt |
| Search (tab) | `magnifyingglass` | 24pt |
| Notifications (tab) | `bell` / `bell.fill` | 24pt |
| You (tab) | `person.crop.circle` / `.fill` | 24pt |
| Story type | `bookmark.fill` | 16pt |
| Bug type | `ladybug.fill` | 16pt |
| Task type | `checkmark` (in square) | 16pt |
| Epic type | `bolt.fill` | 16pt |
| Sub-task type | `arrow.turn.down.right` | 16pt |
| Status dropdown | `chevron.down` | 9pt |
| Priority Highest | `chevron.up.2` | 14pt |
| Priority High | `chevron.up` | 14pt |
| Priority Medium | `equal` | 14pt |
| Priority Low | `chevron.down` | 14pt |
| Filter | `line.3.horizontal.decrease.circle` | 22pt |
| Overflow | `ellipsis` | 22pt |
| Watch | `eye` | 18pt |
| Add child / create | `plus` | 20pt |
| Flag | `flag.fill` | 12pt |
| Comment | `bubble.left` | 18pt |
| Attachment | `paperclip` | 16pt |

## 7. Dark Mode

```swift
struct JiraTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.jiraDarkCanvas : Color.jiraSunken)
            .foregroundStyle(scheme == .dark ? Color.jiraDarkTextPrimary : Color.jiraTextPrimary)
            .tint(scheme == .dark ? Color.jiraBlueBold : Color.jiraBlue)
    }
}

extension View { func jiraTheme() -> some View { modifier(JiraTheme()) } }

// Card surface helper — swaps white→#22272B, navy shadow→surface step
func jiraCardBackground(_ scheme: ColorScheme) -> some View {
    RoundedRectangle(cornerRadius: 8)
        .fill(scheme == .dark ? Color.jiraDarkSurface1 : Color.jiraCanvas)
        .shadow(color: scheme == .dark ? .clear : Color.jiraInkNavy.opacity(0.13), radius: 1, y: 1)
}
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.monospacedDigit()`, `Grid`, modern transitions all available)
- Bundle Atlassian Sans + Atlassian Mono TTFs via `Info.plist` `UIAppFonts`; if licensing precludes shipping them, substitute **Inter** (SIL OFL) — keep tabular numerals on
- Dynamic Type: scale screen titles, issue title, body/description, comments; keep status lozenges, label chips, issue keys, column headers, and tab labels FIXED (board column width is layout-sensitive)
- VoiceOver: label issue cards as "{type}, {key}, {summary}, {points} points, assigned to {name}"; label the status lozenge dropdown as "Status: {value}, button"; expose card drag via `.accessibilityAction(named: "Move to column")`
- Color contrast: `#172B4D` on `#FFFFFF` and `#C7D1DB` on `#1D2125` both pass WCAG AA; every semantic lozenge pair is AA-validated — never recolor a pair ad hoc
- Don't encode status by color alone — the lozenge always carries an uppercase text label (and issue-type icons carry distinct shapes: square/circle/check/lightning)
- Reduce Motion: disable card drag tilt and slide transitions, substitute crossfades; keep instant lozenge color changes
- Dark mode: invert via `jiraDark*`; shadows are near-invisible on dark, so signal elevation with the surface step (`#22272B`→`#2C333A`→`#38414A`) plus a 1px border on overlays and menus
- Hit targets: full-card tap ≥64pt; lozenge dropdown 44pt hit; tab/icon buttons 44pt; field rows full-row 44pt
