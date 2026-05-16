# Obsidian (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Obsidian's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let obsCanvas   = Color(red: 0.118, green: 0.118, blue: 0.118) // #1E1E1E
    static let obsSurface1 = Color(red: 0.149, green: 0.149, blue: 0.149) // #262626
    static let obsSurface2 = Color(red: 0.176, green: 0.176, blue: 0.176) // #2D2D2D
    static let obsSurface3 = Color(red: 0.212, green: 0.212, blue: 0.212) // #363636
    static let obsDivider  = Color(red: 0.212, green: 0.212, blue: 0.212) // #363636

    // MARK: - Text
    static let obsTextPrimary   = Color(red: 0.863, green: 0.867, blue: 0.871) // #DCDDDE
    static let obsTextSecondary = Color(red: 0.600, green: 0.600, blue: 0.600) // #999999
    static let obsTextTertiary  = Color(red: 0.420, green: 0.420, blue: 0.420) // #6B6B6B

    // MARK: - Brand
    static let obsPurple        = Color(red: 0.486, green: 0.227, blue: 0.929) // #7C3AED
    static let obsPurpleLink    = Color(red: 0.655, green: 0.545, blue: 0.980) // #A78BFA
    static let obsPurplePressed = Color(red: 0.427, green: 0.157, blue: 0.851) // #6D28D9
    static let obsPurpleTint    = Color(red: 0.486, green: 0.227, blue: 0.929).opacity(0.14)

    // MARK: - Editor & Semantic
    static let obsExternalLink = Color(red: 0.486, green: 0.612, blue: 0.749) // #7C9CBF
    static let obsSuccess      = Color(red: 0.290, green: 0.871, blue: 0.502) // #4ADE80
    static let obsError        = Color(red: 0.973, green: 0.443, blue: 0.443) // #F87171
    static let obsHighlight    = Color(red: 1.0, green: 0.835, blue: 0.310).opacity(0.25)
}
```

## 2. Typography

Obsidian uses a dual system: Inter for chrome/reading, JetBrains Mono for source/commands. Bundle both via `Info.plist` (`UIAppFonts`); the system mono is the fallback.

```swift
extension Font {
    // Interface (Inter)
    static let obsNoteTitle  = Font.custom("Inter-Bold",     size: 26)
    static let obsH1         = Font.custom("Inter-Bold",     size: 24)
    static let obsH2         = Font.custom("Inter-Bold",     size: 20)
    static let obsH3         = Font.custom("Inter-SemiBold", size: 17)
    static let obsReading    = Font.custom("Inter-Regular",  size: 16)
    static let obsFileRow    = Font.custom("Inter-Regular",  size: 15)
    static let obsPaneTitle  = Font.custom("Inter-Bold",     size: 11)
    static let obsBacklink   = Font.custom("Inter-Regular",  size: 13)
    static let obsMetadata   = Font.custom("Inter-Regular",  size: 13)
    static let obsTagPill    = Font.custom("Inter-SemiBold", size: 13)
    static let obsButton     = Font.custom("Inter-SemiBold", size: 15)
    static let obsNodeLabel  = Font.custom("Inter-SemiBold", size: 11)

    // Monospace (JetBrains Mono)
    static let obsSource       = Font.custom("JetBrainsMono-Regular",  size: 15)
    static let obsCommandRow   = Font.custom("JetBrainsMono-Regular",  size: 14)
    static let obsCommandShort = Font.custom("JetBrainsMono-SemiBold", size: 12)

    // System fallbacks
    static func obsSans(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
    static func obsMono(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .monospaced)
    }
}
```

## 3. Signature Components

### File-Tree Row (the navigation unit)

```swift
struct FileTreeRow: View {
    let name: String
    let depth: Int
    var isFolder: Bool = false
    @Binding var expanded: Bool
    var isActive: Bool = false
    var isModified: Bool = false

    var body: some View {
        HStack(spacing: 6) {
            Spacer().frame(width: CGFloat(depth) * 16)
            if isFolder {
                Image(systemName: "chevron.right")
                    .font(.system(size: 10, weight: .semibold))
                    .foregroundStyle(.obsTextSecondary)
                    .rotationEffect(.degrees(expanded ? 90 : 0))
            } else {
                Spacer().frame(width: 10)
            }
            Image(systemName: isFolder ? "folder.fill" : "doc.text")
                .font(.system(size: 13))
                .foregroundStyle(isActive ? Color.obsPurpleLink : .obsTextSecondary)
            Text(name)
                .font(.obsFileRow)
                .foregroundStyle(.obsTextPrimary)
                .lineLimit(1)
            Spacer()
            if isModified {
                Circle().fill(Color.obsPurpleLink).frame(width: 6, height: 6)
            }
        }
        .padding(.horizontal, 12)
        .frame(height: 32)
        .background(isActive ? Color.obsPurpleTint : .clear)
        .overlay(alignment: .leading) {
            if isActive { Rectangle().fill(Color.obsPurple).frame(width: 2) }
        }
        .contentShape(Rectangle())
        .onTapGesture { if isFolder { withAnimation(.easeInOut(duration: 0.18)) { expanded.toggle() } } }
    }
}
```

### Markdown Source Line (dimmed syntax)

```swift
/// Renders one source line with dimmed markers, purple wikilinks, and code styling.
struct SourceLine: View {
    let tokens: [MarkdownToken]   // produced by your lightweight tokenizer

    var body: some View {
        tokens.reduce(Text("")) { acc, tok in
            acc + styled(tok)
        }
        .font(.obsSource)
        .lineSpacing(15 * 0.55)
    }

    private func styled(_ tok: MarkdownToken) -> Text {
        switch tok.kind {
        case .syntax:   return Text(tok.text).foregroundColor(.obsTextTertiary)   // #, **, [[
        case .wikilink: return Text(tok.text).foregroundColor(.obsPurpleLink).underline()
        case .external: return Text(tok.text).foregroundColor(.obsExternalLink)
        case .code:     return Text(tok.text).foregroundColor(.obsTextPrimary)
        case .text:     return Text(tok.text).foregroundColor(.obsTextPrimary)
        }
    }
}

struct MarkdownToken { enum Kind { case syntax, wikilink, external, code, text }; let kind: Kind; let text: String }
```

### Tag Pill (a connection, not metadata)

```swift
struct TagPill: View {
    let tag: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text("#\(tag)")
                .font(.obsTagPill)
                .foregroundStyle(Color.obsPurpleLink)
                .padding(.horizontal, 9).padding(.vertical, 3)
                .background(Capsule().fill(Color.obsPurpleTint))
        }
        .buttonStyle(.plain)
    }
}
```

### Graph View (physics-driven nodes)

```swift
import SwiftUI

struct GraphNode: Identifiable { let id = UUID(); var position: CGPoint; var radius: CGFloat; let label: String; var isActive: Bool }
struct GraphEdge { let a: UUID; let b: UUID }

struct GraphView: View {
    @State var nodes: [GraphNode]
    let edges: [GraphEdge]
    // Drive `nodes` from a force-simulation tick (e.g., a TimelineView or CADisplayLink).

    var body: some View {
        TimelineView(.animation) { _ in
            Canvas { ctx, _ in
                // Edges
                for e in edges {
                    guard let a = nodes.first(where: { $0.id == e.a }),
                          let b = nodes.first(where: { $0.id == e.b }) else { continue }
                    var path = Path()
                    path.move(to: a.position); path.addLine(to: b.position)
                    let bright = a.isActive || b.isActive
                    ctx.stroke(path, with: .color(bright ? .obsPurple : .obsDivider), lineWidth: 1)
                }
                // Nodes
                for n in nodes {
                    let rect = CGRect(x: n.position.x - n.radius, y: n.position.y - n.radius,
                                      width: n.radius * 2, height: n.radius * 2)
                    if n.isActive {
                        ctx.fill(Circle().path(in: rect.insetBy(dx: -8, dy: -8)),
                                 with: .color(.obsPurpleTint))
                    }
                    ctx.fill(Circle().path(in: rect),
                             with: .color(n.isActive ? .obsPurpleLink : .obsPurple))
                }
            }
            .background(Color.obsCanvas)
        }
    }
}
```

### Command Palette

```swift
struct CommandPalette: View {
    @Binding var query: String
    let rows: [(label: String, shortcut: String)]
    @State private var focused = 0

    var body: some View {
        VStack(spacing: 0) {
            HStack(spacing: 10) {
                Text(">").font(.obsCommandRow).foregroundStyle(.obsTextTertiary)
                TextField("Type a command…", text: $query)
                    .font(.obsCommandRow)
                    .foregroundStyle(.obsTextPrimary)
                    .tint(.obsPurpleLink)
            }
            .padding(.horizontal, 16)
            .frame(height: 44)
            Divider().overlay(Color.obsDivider)
            ScrollView {
                LazyVStack(spacing: 0) {
                    ForEach(Array(rows.enumerated()), id: \.offset) { i, r in
                        HStack {
                            Text(r.label).font(.obsCommandRow).foregroundStyle(.obsTextPrimary)
                            Spacer()
                            Text(r.shortcut).font(.obsCommandShort).foregroundStyle(.obsTextSecondary)
                        }
                        .padding(.horizontal, 16)
                        .frame(height: 38)
                        .background(i == focused ? Color.obsPurpleTint : .clear)
                    }
                }
            }
        }
        .frame(maxWidth: 560)
        .background(RoundedRectangle(cornerRadius: 10).fill(Color.obsSurface1))
        .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.obsDivider, lineWidth: 1))
        .shadow(color: .black.opacity(0.6), radius: 56, y: 20)
        .transition(.scale(scale: 0.97).combined(with: .opacity))
    }
}
```

### Backlinks Pane

```swift
struct BacklinksPane: View {
    let backlinks: [(title: String, context: String)]

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack {
                Text("LINKED MENTIONS").font(.obsPaneTitle).tracking(0.6).foregroundStyle(.obsTextSecondary)
                Text("\(backlinks.count)").font(.obsMetadata).foregroundStyle(.obsTextSecondary)
            }
            .padding(.horizontal, 16).padding(.vertical, 12)

            ForEach(Array(backlinks.enumerated()), id: \.offset) { _, b in
                VStack(alignment: .leading, spacing: 4) {
                    Text(b.title).font(.obsButton).foregroundStyle(.obsTextPrimary)
                    Text(b.context).font(.obsBacklink).foregroundStyle(.obsTextSecondary).lineLimit(2)
                }
                .padding(.horizontal, 16).padding(.vertical, 10)
            }
        }
        .background(Color.obsSurface1)
    }
}
```

### Primary Button

```swift
struct ObsidianPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.obsButton)
                .foregroundStyle(.white)
                .padding(.vertical, 9).padding(.horizontal, 16)
                .frame(height: 34)
                .background(RoundedRectangle(cornerRadius: 6).fill(Color.obsPurple))
        }
        .buttonStyle(ObsPressable())
        .sensoryFeedback(.impact(weight: .light), trigger: title)
    }
}

struct ObsPressable: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .overlay(configuration.isPressed
                ? RoundedRectangle(cornerRadius: 6).fill(Color.obsPurplePressed).blendMode(.multiply) : nil)
            .animation(.spring(response: 0.2, dampingFraction: 0.85), value: configuration.isPressed)
    }
}
```

## 4. Navigation — Ribbon + File-Tree Pane (no bottom tab bar)

Obsidian has no tab bar. A left ribbon plus a slide-over file tree on iPhone; a multi-pane workspace on iPad (`NavigationSplitView`).

```swift
struct RootWorkspace: View {
    @State private var showTree = false
    var body: some View {
        HStack(spacing: 0) {
            Ribbon()                       // 40pt vertical action strip
            ZStack(alignment: .leading) {
                EditorView()
                if showTree {
                    Color.black.opacity(0.5).ignoresSafeArea()
                        .onTapGesture { withAnimation { showTree = false } }
                    FileTreePane().frame(width: 280)
                        .background(Color.obsSurface1)
                        .transition(.move(edge: .leading))
                }
            }
        }
        .background(Color.obsCanvas)
        .animation(.spring(response: 0.35, dampingFraction: 0.9), value: showTree)
    }
}

struct Ribbon: View {
    var body: some View {
        VStack(spacing: 4) {
            ribbonIcon("circle.hexagongrid")     // graph
            ribbonIcon("command")                // palette
            ribbonIcon("magnifyingglass")        // search
            ribbonIcon("square.and.pencil")      // new note
            Spacer()
            ribbonIcon("gearshape")              // settings
        }
        .padding(.vertical, 12)
        .frame(width: 40)
        .background(Color.obsSurface1)
    }
    private func ribbonIcon(_ name: String) -> some View {
        Image(systemName: name)
            .font(.system(size: 20))
            .foregroundStyle(.obsTextSecondary)
            .frame(width: 40, height: 40)
    }
}
```

## 5. Motion

```swift
// Command palette open: scale 0.97 → 1 + opacity over 130ms
withAnimation(.easeOut(duration: 0.13)) { showPalette = true }

// File-tree folder toggle: chevron rotation + child height ease 180ms
.animation(.easeInOut(duration: 0.18), value: expanded)

// Node focus: brighten + halo fade over 250ms (drive via the simulation state)
.animation(.easeInOut(duration: 0.25), value: isActive)

// Reading/source toggle: 200ms cross-fade
.transition(.opacity).animation(.easeInOut(duration: 0.2), value: readingMode)

// Pane slide-over: spring 220ms with a 0.5 scrim
.animation(.spring(response: 0.35, dampingFraction: 0.9), value: showTree)

// Haptic on note open / command run / tag tap
.sensoryFeedback(.impact(weight: .light), trigger: openCount)
```

For the graph force simulation, run a fixed-timestep integrator (repulsion + spring along edges + light centering) on a `CADisplayLink` / `TimelineView(.animation)` and let nodes drift ~600ms toward equilibrium after any change.

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| File (tree) | `doc.text` | 13pt |
| Folder (tree) | `folder.fill` | 13pt |
| Folder chevron | `chevron.right` | 10pt |
| Graph (ribbon) | `circle.hexagongrid` | 20pt |
| Command palette (ribbon) | `command` | 20pt |
| Search (ribbon) | `magnifyingglass` | 20pt |
| New note (ribbon) | `square.and.pencil` | 20pt |
| Settings (ribbon) | `gearshape` | 20pt |
| Reading/source toggle | `book` / `chevron.left.forwardslash.chevron.right` | 18pt |
| Backlink | `arrow.uturn.backward` | 14pt |
| Tag | `number` | 13pt |
| Modified dot | `circle.fill` | 6pt |
| More | `ellipsis` | 18pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on reading body, note titles, and file-tree rows; the mono source scales but keep a 13pt floor to preserve the monospace grid; pin graph node labels (11pt) and command shortcuts (12pt)
- VoiceOver: graph view needs an alternative — expose nodes as an accessible list ("Note Name, 5 links, linked to …") since a Canvas is opaque to assistive tech; the active node announces "current note"
- Wikilinks must be reachable as links/buttons with labels ("[[Project Plan]], internal link"); don't rely on the purple color alone — also expose an "internal link" trait
- Contrast: `#999999` secondary on `#1E1E1E` passes WCAG AA at 13pt+; `#6B6B6B` syntax markers are intentionally low-contrast — fine for decoration but never essential text
- Respect `accessibilityReduceMotion`: freeze the graph physics (static layout), drop the command-palette scale, keep the opacity fade only
- The command palette must be fully keyboard-navigable on iPad: arrow keys move focus, Return runs, Escape dismisses (`onKeyPress` iOS 17+ or `UIKeyCommand`)
