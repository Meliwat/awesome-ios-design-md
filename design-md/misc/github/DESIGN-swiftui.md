# GitHub (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates GitHub's Primer visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. GitHub's signature is the repo home + code browser with semantic state color and the contributions heatmap.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — Primer "dark default")
    static let ghCanvas       = Color(red: 0.051, green: 0.067, blue: 0.090) // #0D1117
    static let ghCanvasSubtle = Color(red: 0.086, green: 0.106, blue: 0.133) // #161B22
    static let ghSurface      = Color(red: 0.129, green: 0.149, blue: 0.176) // #21262D
    static let ghSurfacePressed = Color(red: 0.188, green: 0.212, blue: 0.239) // #30363D
    static let ghBorder       = Color(red: 0.188, green: 0.212, blue: 0.239) // #30363D
    static let ghBorderMuted  = Color(red: 0.129, green: 0.149, blue: 0.176) // #21262D

    // MARK: - Canvas & Surfaces (Light)
    static let ghCanvasLight       = Color.white                                  // #FFFFFF
    static let ghCanvasSubtleLight = Color(red: 0.965, green: 0.973, blue: 0.980) // #F6F8FA
    static let ghBorderLight       = Color(red: 0.816, green: 0.843, blue: 0.871) // #D0D7DE

    // MARK: - Text (Foreground)
    static let ghFgDefault = Color(red: 0.902, green: 0.929, blue: 0.953) // #E6EDF3
    static let ghFgMuted   = Color(red: 0.490, green: 0.522, blue: 0.565) // #7D8590
    static let ghFgSubtle  = Color(red: 0.431, green: 0.463, blue: 0.506) // #6E7681
    static let ghFgDefaultLight = Color(red: 0.122, green: 0.137, blue: 0.157) // #1F2328

    // MARK: - Accent (link / active)
    static let ghAccent         = Color(red: 0.184, green: 0.506, blue: 0.969) // #2F81F7
    static let ghAccentEmphasis = Color(red: 0.122, green: 0.435, blue: 0.922) // #1F6FEB
    static let ghAccentLight    = Color(red: 0.035, green: 0.412, blue: 0.855) // #0969DA

    // MARK: - Button Primary (RESERVED — Code / Merge only)
    static let ghBtnPrimary      = Color(red: 0.137, green: 0.525, blue: 0.212) // #238636
    static let ghBtnPrimaryHover = Color(red: 0.180, green: 0.627, blue: 0.263) // #2EA043

    // MARK: - Semantic State
    static let ghOpen      = Color(red: 0.247, green: 0.725, blue: 0.314) // #3FB950 (success)
    static let ghMerged    = Color(red: 0.639, green: 0.443, blue: 0.969) // #A371F7 (done)
    static let ghClosed    = Color(red: 0.973, green: 0.318, blue: 0.286) // #F85149 (danger)
    static let ghDraft     = Color(red: 0.431, green: 0.463, blue: 0.506) // #6E7681
    static let ghAttention = Color(red: 0.824, green: 0.600, blue: 0.137) // #D29922
    static let ghSponsor   = Color(red: 0.859, green: 0.380, blue: 0.635) // #DB61A2

    // MARK: - Tab active underline
    static let ghTabUnderline = Color(red: 0.969, green: 0.506, blue: 0.400) // #F78166

    // MARK: - Contributions heatmap
    static let ghContrib0 = Color(red: 0.086, green: 0.106, blue: 0.133) // #161B22
    static let ghContrib1 = Color(red: 0.055, green: 0.267, blue: 0.161) // #0E4429
    static let ghContrib2 = Color(red: 0.000, green: 0.427, blue: 0.196) // #006D32
    static let ghContrib3 = Color(red: 0.149, green: 0.651, blue: 0.255) // #26A641
    static let ghContrib4 = Color(red: 0.224, green: 0.827, blue: 0.325) // #39D353
}
```

## 2. Typography

GitHub uses Mona Sans for UI and a monospace for all code/SHAs/file names. Bundle Mona Sans; `SF Mono` is the system monospace.

```swift
extension Font {
    static let ghDisplay   = Font.custom("MonaSans-ExtraBold", size: 32).weight(.heavy)
    static let ghRepoTitle = Font.custom("MonaSans-Bold",      size: 24).weight(.bold)
    static let ghSection   = Font.custom("MonaSans-SemiBold",  size: 20).weight(.semibold)
    static let ghSubtitle  = Font.custom("MonaSans-SemiBold",  size: 16).weight(.semibold)
    static let ghBody      = Font.custom("MonaSans-Regular",   size: 14).weight(.regular)
    static let ghBodyStrong = Font.custom("MonaSans-SemiBold", size: 14).weight(.semibold)
    static let ghMeta      = Font.custom("MonaSans-Regular",   size: 12).weight(.regular)
    static let ghLabel     = Font.custom("MonaSans-Medium",    size: 12).weight(.medium)
    static let ghCount     = Font.custom("MonaSans-SemiBold",  size: 11).weight(.semibold)
    static let ghTab       = Font.custom("MonaSans-Medium",    size: 10).weight(.medium)
    static let ghCaption   = Font.custom("MonaSans-Regular",   size: 11).weight(.regular)

    // Monospace — code, SHAs, file/branch names, diffs (the contract)
    static let ghCode      = Font.system(size: 13, weight: .regular, design: .monospaced)
    static let ghCodeInline = Font.system(size: 13, weight: .regular, design: .monospaced)
    static let ghSHA       = Font.system(size: 12, weight: .semibold, design: .monospaced)
}
```

## 3. Signature Components

### Repo Header

```swift
struct RepoHeader: View {
    let owner: String, name: String, description: String
    let language: String, languageColor: Color
    let stars: String, forks: String

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack(spacing: 7) {
                Image(systemName: "book.closed")
                    .font(.system(size: 15)).foregroundStyle(Color.ghFgMuted)
                Text(owner).foregroundStyle(Color.ghAccent)
                Text("/").foregroundStyle(Color.ghFgMuted)
                Text(name).foregroundStyle(Color.ghAccent).fontWeight(.bold)
            }
            .font(.system(size: 17, weight: .semibold))

            Text(description)
                .font(.ghBody).foregroundStyle(Color.ghFgMuted)

            HStack(spacing: 16) {
                HStack(spacing: 5) {
                    Circle().fill(languageColor).frame(width: 11, height: 11)
                    Text(language)
                }
                HStack(spacing: 5) { Image(systemName: "star"); Text(stars) }
                HStack(spacing: 5) { Image(systemName: "tuningfork"); Text(forks) }
            }
            .font(.ghMeta).foregroundStyle(Color.ghFgMuted)
        }
        .padding(16)
        .background(Color.ghCanvas)
    }
}
```

### Tab Strip (orange-coral underline)

```swift
struct RepoTabStrip: View {
    let tabs: [(String, String?)]      // (title, count?)
    @Binding var selected: Int

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 20) {
                ForEach(Array(tabs.enumerated()), id: \.offset) { i, tab in
                    VStack(spacing: 10) {
                        HStack(spacing: 5) {
                            Text(tab.0)
                                .font(.ghBody.weight(i == selected ? .semibold : .regular))
                                .foregroundStyle(i == selected ? Color.ghFgDefault : Color.ghFgMuted)
                            if let c = tab.1 {
                                Text(c)
                                    .font(.ghCount)
                                    .foregroundStyle(Color.ghFgSubtle)
                                    .padding(.horizontal, 6).padding(.vertical, 1)
                                    .background(Capsule().fill(Color.ghCanvasSubtle))
                            }
                        }
                        Rectangle()
                            .fill(i == selected ? Color.ghTabUnderline : .clear)
                            .frame(height: 2)
                    }
                    .onTapGesture { withAnimation(.easeOut(duration: 0.2)) { selected = i } }
                }
            }
            .padding(.horizontal, 16)
        }
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.ghBorderMuted).frame(height: 1)
        }
    }
}
```

### Inline README Card & File Row

```swift
struct ReadmeCard<Body: View>: View {
    @ViewBuilder let content: () -> Body
    var body: some View {
        VStack(spacing: 0) {
            HStack(spacing: 8) {
                Image(systemName: "doc.text").font(.system(size: 15))
                    .foregroundStyle(Color.ghFgMuted)
                Text("README.md").font(.ghBody.weight(.semibold))
                    .foregroundStyle(Color.ghFgDefault)
                Spacer()
            }
            .padding(.horizontal, 14).padding(.vertical, 10)
            .background(Color.ghCanvasSubtle)
            .overlay(alignment: .bottom) { Rectangle().fill(Color.ghBorder).frame(height: 1) }

            content().padding(14).frame(maxWidth: .infinity, alignment: .leading)
        }
        .background(Color.ghCanvas)
        .clipShape(RoundedRectangle(cornerRadius: 8))
        .overlay(RoundedRectangle(cornerRadius: 8).strokeBorder(Color.ghBorder, lineWidth: 1))
    }
}

struct FileRow: View {
    let isDir: Bool, name: String, message: String, time: String
    var body: some View {
        HStack(spacing: 10) {
            Image(systemName: isDir ? "folder.fill" : "doc")
                .font(.system(size: 15)).foregroundStyle(Color.ghFgMuted)
            Text(name).font(.ghBody).foregroundStyle(Color.ghFgDefault)
            Text(message).font(.ghMeta).foregroundStyle(Color.ghFgMuted)
                .lineLimit(1).frame(maxWidth: .infinity, alignment: .leading)
            Text(time).font(.ghCaption).foregroundStyle(Color.ghFgSubtle)
        }
        .padding(.horizontal, 14).padding(.vertical, 9)
        .overlay(alignment: .bottom) { Rectangle().fill(Color.ghBorderMuted).frame(height: 1) }
    }
}
```

### State Pill & Issue Label

```swift
enum GHState { case open, merged, closed, draft
    var color: Color { switch self { case .open: .ghOpen; case .merged: .ghMerged
                                      case .closed: .ghClosed; case .draft: .ghDraft } }
    var label: String { switch self { case .open: "Open"; case .merged: "Merged"
                                       case .closed: "Closed"; case .draft: "Draft" } }
    var symbol: String { switch self { case .open: "dot.circle"; case .merged: "arrow.triangle.merge"
                                        case .closed: "xmark.circle"; case .draft: "circle" } }
}

struct StatePill: View {
    let state: GHState
    var body: some View {
        HStack(spacing: 6) {
            Image(systemName: state.symbol).font(.system(size: 11, weight: .bold))
            Text(state.label).font(.ghLabel.weight(.semibold))
        }
        .foregroundStyle(.white)
        .padding(.horizontal, 12).padding(.vertical, 5)
        .background(Capsule().fill(state.color))
    }
}

struct IssueLabel: View {
    let text: String
    let color: Color           // the label's own color
    var body: some View {
        Text(text)
            .font(.ghLabel)
            .foregroundStyle(color)
            .padding(.horizontal, 10).padding(.vertical, 3)
            .background(Capsule().fill(color.opacity(0.1)))
            .overlay(Capsule().strokeBorder(color.opacity(0.4), lineWidth: 1))
    }
}
```

### Contributions Heatmap (signature)

```swift
struct ContributionsHeatmap: View {
    let levels: [[Int]]        // weeks × 7 days, value 0...4

    func color(_ l: Int) -> Color {
        switch l { case 1: .ghContrib1; case 2: .ghContrib2
                   case 3: .ghContrib3; case 4: .ghContrib4; default: .ghContrib0 }
    }

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack(spacing: 3) {
                ForEach(Array(levels.enumerated()), id: \.offset) { _, week in
                    VStack(spacing: 3) {
                        ForEach(Array(week.enumerated()), id: \.offset) { _, day in
                            RoundedRectangle(cornerRadius: 2)
                                .fill(color(day))
                                .aspectRatio(1, contentMode: .fit)
                                .overlay(
                                    RoundedRectangle(cornerRadius: 2)
                                        .strokeBorder(day == 0 ? Color.ghFgMuted.opacity(0.15) : .clear,
                                                       lineWidth: 1))
                        }
                    }
                }
            }
            HStack(spacing: 4) {
                Text("Less").font(.ghCaption).foregroundStyle(Color.ghFgMuted)
                ForEach(0..<5) { l in
                    RoundedRectangle(cornerRadius: 2).fill(color(l)).frame(width: 11, height: 11)
                }
                Text("More").font(.ghCaption).foregroundStyle(Color.ghFgMuted)
            }
        }
    }
}
```

### Buttons (RESERVED green vs default)

```swift
struct CodeButton: View {                       // green — Code / Merge ONLY
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.ghBodyStrong).foregroundStyle(.white)
                .padding(.horizontal, 16).padding(.vertical, 10)
                .background(RoundedRectangle(cornerRadius: 6).fill(Color.ghBtnPrimary))
                .overlay(RoundedRectangle(cornerRadius: 6)
                    .strokeBorder(Color.white.opacity(0.1), lineWidth: 1))
        }
    }
}

struct DefaultButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.ghBodyStrong).foregroundStyle(Color.ghFgDefault)
                .padding(.horizontal, 16).padding(.vertical, 10)
                .background(RoundedRectangle(cornerRadius: 6).fill(Color.ghSurface))
                .overlay(RoundedRectangle(cornerRadius: 6)
                    .strokeBorder(Color.ghBorder, lineWidth: 1))
        }
    }
}
```

### Diff Viewer

```swift
struct DiffLine: View {
    enum Kind { case add, del, ctx }
    let kind: Kind, oldNo: String, newNo: String, code: String

    var rowBG: Color {
        switch kind { case .add: Color.ghOpen.opacity(0.15)
                      case .del: Color.ghClosed.opacity(0.15); default: .clear }
    }
    var marker: String { switch kind { case .add: "+"; case .del: "-"; default: " " } }
    var markerColor: Color { switch kind { case .add: .ghOpen; case .del: .ghClosed; default: .ghFgSubtle } }

    var body: some View {
        HStack(spacing: 0) {
            Text(oldNo).frame(width: 36, alignment: .trailing)
            Text(newNo).frame(width: 36, alignment: .trailing)
                .overlay(alignment: .trailing) {
                    Rectangle().fill(Color.ghBorderMuted).frame(width: 1)
                }
            Text(marker).foregroundStyle(markerColor).frame(width: 16)
            Text(code).foregroundStyle(Color.ghFgDefault)
            Spacer(minLength: 0)
        }
        .font(.ghCode)
        .foregroundStyle(Color.ghFgSubtle)
        .padding(.vertical, 1)
        .background(rowBG)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct GHTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem          { Label("Home",          systemImage: "house") }
            NotificationsView().tabItem { Label("Notifications", systemImage: "bell") }
            ExploreView().tabItem       { Label("Explore",       systemImage: "magnifyingglass") }
            ProfileView().tabItem       { Label("Profile",       systemImage: "person.crop.circle") }
        }
        .tint(.ghFgDefault)                            // active = FG default, no pill
        .toolbarBackground(Color.ghCanvas, for: .tabBar)
        .toolbarColorScheme(.dark, for: .tabBar)
    }
}
```

## 5. Motion

```swift
// Tab strip switch — orange underline slides + content crossfade
withAnimation(.easeOut(duration: 0.2)) { selected = i }
// underline via .matchedGeometryEffect on the Rectangle if you want a true slide

// Branch switcher — dropdown fade + 4pt slide-down
.transition(.opacity.combined(with: .move(edge: .top)))   // 150ms ease-out

// Star/Watch toggle — label flip + count-up + scale
.scaleEffect(tapped ? 0.97 : 1).animation(.easeOut(duration: 0.12), value: tapped)
UIImpactFeedbackGenerator(style: .soft).impactOccurred()

// Merge — spinner then morph to purple "Merged"
withAnimation(.easeInOut(duration: 0.25)) { state = .merged }
UINotificationFeedbackGenerator().notificationOccurred(.success)

// Diff "Expand N lines" — height ease-out
withAnimation(.easeOut(duration: 0.15)) { expanded = true }

// Navigation — native iOS push (NavigationStack), 300ms slide

// List append — fade in
.transition(.opacity)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Notifications (tab) | `bell` / `bell.fill` | 22pt |
| Explore (tab) | `magnifyingglass` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Repo | `book.closed` | 15–17pt |
| Folder | `folder.fill` | 15pt |
| File | `doc` | 15pt |
| README | `doc.text` | 15pt |
| Branch | `arrow.triangle.branch` | 13pt |
| Star | `star` / `star.fill` | 13pt |
| Fork | `tuningfork` | 13pt |
| Issue open | `dot.circle` / `smallcircle.filled.circle` | 12pt |
| PR merged | `arrow.triangle.merge` | 12pt |
| Closed | `xmark.circle` | 12pt |
| Draft | `circle` | 12pt |
| Code button | `chevron.left.forwardslash.chevron.right` | 14pt |
| Commit | `circle.circle` | 13pt |
| Watch | `eye` | 14pt |
| Back | `chevron.left` | 22pt |
| Overflow | `ellipsis` | 22pt |
| Search | `magnifyingglass` | 16pt |

## 7. Dark Mode

GitHub ships Primer light + dark; **dark default** is primary. The token sets mirror Primer's `dark` and `light` color modes exactly.

```swift
struct GHTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.ghCanvas : Color.ghCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.ghFgDefault : Color.ghFgDefaultLight)
            .tint(.ghAccent)
    }
}
extension View { func ghTheme() -> some View { modifier(GHTheme()) } }

// Per-mode swaps (Primer parity):
// canvas      #0D1117 → #FFFFFF
// canvasSubtle#161B22 → #F6F8FA
// border      #30363D → #D0D7DE
// accent      #2F81F7 → #0969DA
// btnPrimary  #238636 → #1F883D
// open        #3FB950 → #1A7F37 | merged #A371F7 → #8250DF | closed #F85149 → #CF222E
// heatmap     dark ramp → light ramp (#EBEDF0 → #9BE9A8 → #40C463 → #30A14E → #216E39)
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`NavigationStack`, `.toolbarBackground` comfortable; `UIImpactFeedbackGenerator` back to iOS 13). `.monospaced` system font design is iOS 15+.
- Bundle Mona Sans (SIL OFL) for the UI face; `SF Mono` is the system monospace for code/SHAs — no extra license.
- Dynamic Type: scale repo title, sections, body, and code; pin tab labels, count pills, SHAs, and heatmap squares at FIXED sizes (table/tree-layout-sensitive). Code blocks scale but stay monospace and horizontally scrollable.
- VoiceOver: read state pills as "Open issue" / "Merged pull request" / "Closed" (semantic, not just color); label heatmap squares "{n} contributions on {date}"; read file rows "File {name}, last commit {message}, {time}"; the `owner/name` header as a single link "{owner} slash {name}".
- Color is not the only signal: state pills carry a leading glyph (open ●, merged ⑂, closed ✕, draft ○) and a text label so color-blind users get the meaning; diff additions/deletions carry `+`/`-` markers, not just the green/red wash.
- Contrast: `#E6EDF3` on `#0D1117` passes WCAG AA; `#7D8590` muted text passes AA for ≥12pt; the green `#238636` button text (`#FFFFFF`) passes AA.
- Reserved green: never style a non-"Code"/non-"Merge" control with `#238636` — it's a learned affordance ("download / merge"); use the default `#21262D` button for everything else.
- Reduce Motion: disable the tab-underline slide and the merge morph (instant state swap); keep the count-up as an instant set; diff "Expand" becomes an instant insert.
- Reduce Transparency: replace the tab bar's `rgba(13,17,23,0.94)` blur with solid `#0D1117`.
- Monospace contract: never substitute the sans face for code, SHAs, file paths, or branch names — glyph distinction (`0`/`O`, `1`/`l`) is an accessibility and correctness requirement for developers.
