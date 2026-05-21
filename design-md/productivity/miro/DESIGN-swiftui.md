# Miro (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Miro's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the infinite canvas, sticky notes, and the floating toolbar.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Board & Chrome (Light)
    static let miroBoard        = Color(red: 0.961, green: 0.961, blue: 0.969) // #F5F5F7
    static let miroGrid         = Color(red: 0.843, green: 0.843, blue: 0.871) // #D7D7DE
    static let miroChrome       = Color.white                                   // #FFFFFF
    static let miroSurfaceGray  = Color(red: 0.945, green: 0.945, blue: 0.957) // #F1F1F4
    static let miroSurfacePressed = Color(red: 0.902, green: 0.902, blue: 0.922) // #E6E6EB
    static let miroDivider      = Color(red: 0.890, green: 0.890, blue: 0.910) // #E3E3E8

    // MARK: - Board & Chrome (Dark)
    static let miroDarkCanvas   = Color(red: 0.106, green: 0.106, blue: 0.122) // #1B1B1F
    static let miroDarkBoard    = Color(red: 0.125, green: 0.125, blue: 0.141) // #202024
    static let miroDarkGrid     = Color(red: 0.220, green: 0.220, blue: 0.247) // #38383F
    static let miroDarkSurface1 = Color(red: 0.149, green: 0.149, blue: 0.169) // #26262B
    static let miroDarkSurface2 = Color(red: 0.192, green: 0.192, blue: 0.220) // #313138

    // MARK: - Text
    static let miroInk          = Color(red: 0.020, green: 0.000, blue: 0.220) // #050038
    static let miroTextSecondary = Color(red: 0.420, green: 0.420, blue: 0.482) // #6B6B7B
    static let miroTextTertiary = Color(red: 0.604, green: 0.604, blue: 0.643) // #9A9AA4
    static let miroDarkTextPrimary = Color(red: 0.925, green: 0.925, blue: 0.937) // #ECECEF
    static let miroNoteInk      = Color(red: 0.165, green: 0.165, blue: 0.200) // #2A2A33

    // MARK: - Brand / Interactive
    static let miroYellow        = Color(red: 1.000, green: 0.816, blue: 0.184) // #FFD02F
    static let miroYellowPressed = Color(red: 0.910, green: 0.722, blue: 0.000) // #E8B800
    static let miroBlue          = Color(red: 0.259, green: 0.384, blue: 1.000) // #4262FF
    static let miroBluePressed   = Color(red: 0.184, green: 0.290, blue: 0.878) // #2F4AE0

    // MARK: - Sticky Note Palette (full-color in both modes)
    static let miroNoteYellow = Color(red: 0.996, green: 0.953, blue: 0.714) // #FEF3B6
    static let miroNotePink   = Color(red: 0.973, green: 0.784, blue: 0.847) // #F8C8D8
    static let miroNoteGreen  = Color(red: 0.780, green: 0.910, blue: 0.780) // #C7E8C7
    static let miroNoteBlue   = Color(red: 0.737, green: 0.878, blue: 0.961) // #BCE0F5
    static let miroNoteOrange = Color(red: 0.988, green: 0.851, blue: 0.714) // #FCD9B6
    static let miroNoteViolet = Color(red: 0.863, green: 0.788, blue: 0.941) // #DCC9F0

    // MARK: - Semantic
    static let miroError   = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
    static let miroSuccess = Color(red: 0.180, green: 0.647, blue: 0.416) // #2EA56A
    static let miroWarning = Color(red: 0.941, green: 0.663, blue: 0.169) // #F0A92B
}

/// Multiplayer cursor colors, assigned round-robin.
let miroCursorColors: [Color] = [
    Color(red: 0.259, green: 0.384, blue: 1.000), // #4262FF
    Color(red: 0.949, green: 0.282, blue: 0.133), // #F24822
    Color(red: 0.078, green: 0.682, blue: 0.361), // #14AE5C
    Color(red: 0.592, green: 0.278, blue: 1.000), // #9747FF
    Color(red: 0.949, green: 0.663, blue: 0.000), // #F2A900
    Color(red: 0.898, green: 0.282, blue: 0.620), // #E5489E
]
```

## 2. Typography

Miro's UI face is Inter (Caveat optional for handwriting sticky text, JetBrains Mono for code embeds). Bundle TTFs via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static let miroBoardTitle = Font.custom("Inter-ExtraBold", size: 28).weight(.heavy)
    static let miroH1          = Font.custom("Inter-Bold",     size: 22).weight(.bold)
    static let miroFrameLabel  = Font.custom("Inter-Bold",     size: 18).weight(.bold)
    static let miroSticky      = Font.custom("Inter-SemiBold", size: 16).weight(.semibold)
    static let miroBody        = Font.custom("Inter-Regular",  size: 16).weight(.regular)
    static let miroControl     = Font.custom("Inter-SemiBold", size: 15).weight(.semibold)
    static let miroButton      = Font.custom("Inter-Bold",     size: 15).weight(.bold)
    static let miroMeta        = Font.custom("Inter-Regular",  size: 14).weight(.regular)
    static let miroCaption     = Font.custom("Inter-SemiBold", size: 12).weight(.semibold)
    static let miroConnector   = Font.custom("Inter-Medium",   size: 13).weight(.medium)
    static let miroListRow     = Font.custom("Inter-Medium",   size: 16).weight(.medium)
    static let miroTab          = Font.custom("Inter-SemiBold", size: 10).weight(.semibold)
    static let miroAvatar       = Font.custom("Inter-Bold",    size: 11).weight(.bold)
}
```

## 3. Signature Components

### Infinite Canvas (pinch-zoom + pan)

```swift
struct MiroCanvas<Content: View>: View {
    @State private var zoom: CGFloat = 1
    @State private var lastZoom: CGFloat = 1
    @State private var offset: CGSize = .zero
    @State private var lastOffset: CGSize = .zero
    @Environment(\.colorScheme) private var scheme
    @ViewBuilder let content: () -> Content

    var body: some View {
        GeometryReader { _ in
            ZStack {
                (scheme == .dark ? Color.miroDarkBoard : Color.miroBoard)
                MiroGrid(spacing: 28 * zoom,
                         color: scheme == .dark ? .miroDarkGrid : .miroGrid,
                         offset: offset)
                content()
                    .scaleEffect(zoom)
                    .offset(offset)
            }
            .contentShape(Rectangle())
            .gesture(
                SimultaneousGesture(
                    MagnificationGesture()
                        .onChanged { zoom = (lastZoom * $0).clamped(0.2, 5) }
                        .onEnded { _ in lastZoom = zoom },
                    DragGesture()
                        .onChanged { offset = CGSize(width: lastOffset.width + $0.translation.width,
                                                     height: lastOffset.height + $0.translation.height) }
                        .onEnded { _ in lastOffset = offset }
                )
            )
            .onTapGesture(count: 2) {
                withAnimation(.easeOut(duration: 0.35)) { zoom = 1; offset = .zero; lastZoom = 1; lastOffset = .zero }
            }
        }
    }
}

extension CGFloat { func clamped(_ lo: CGFloat, _ hi: CGFloat) -> CGFloat { min(max(self, lo), hi) } }

struct MiroGrid: View {
    let spacing: CGFloat; let color: Color; let offset: CGSize
    var body: some View {
        Canvas { ctx, size in
            let s = max(spacing, 8)
            var x = offset.width.truncatingRemainder(dividingBy: s)
            while x < size.width { var y = offset.height.truncatingRemainder(dividingBy: s)
                while y < size.height {
                    ctx.fill(Path(ellipseIn: CGRect(x: x, y: y, width: 1.5, height: 1.5)), with: .color(color))
                    y += s }
                x += s }
        }
    }
}
```

### Sticky Note — *signature*

```swift
struct MiroStickyNote: View {
    @State private var text: String
    @State private var selected: Bool
    var fill: Color = .miroNoteYellow
    init(text: String = "", selected: Bool = false, fill: Color = .miroNoteYellow) {
        _text = State(initialValue: text); _selected = State(initialValue: selected); self.fill = fill
    }

    var body: some View {
        ZStack(alignment: .bottom) {
            RoundedRectangle(cornerRadius: 4)
                .fill(fill)
                .overlay(
                    Text(text)
                        .font(.miroSticky)
                        .foregroundStyle(Color.miroNoteInk)
                        .multilineTextAlignment(.center)
                        .minimumScaleFactor(0.5)   // auto-fit to box
                        .padding(10)
                )
                .frame(width: 96, height: 96)
                .shadow(color: .black.opacity(0.18), radius: 9, x: 0, y: 8)

            if selected {
                Capsule().fill(Color.miroBlue).frame(width: 30, height: 4).offset(y: 8)
            }
        }
        .overlay(
            selected
            ? RoundedRectangle(cornerRadius: 6)
                .strokeBorder(Color.miroBlue, lineWidth: 2)
                .padding(-4)
            : nil
        )
        .onTapGesture { selected.toggle() }
    }
}
```

### Floating Toolbar

```swift
struct MiroToolbar: View {
    @Binding var active: Int
    let tools: [(String, String)]   // (sfSymbol, label)

    var body: some View {
        HStack(spacing: 4) {
            ForEach(Array(tools.enumerated()), id: \.offset) { i, tool in
                Button {
                    withAnimation(.easeOut(duration: 0.12)) { active = i }
                } label: {
                    Image(systemName: tool.0)
                        .font(.system(size: 20, weight: .medium))
                        .foregroundStyle(active == i ? Color.miroInk : Color.miroTextSecondary)
                        .frame(width: 40, height: 40)
                        .background(
                            RoundedRectangle(cornerRadius: 11)
                                .fill(active == i ? Color.miroYellow : .clear)
                        )
                }
                .buttonStyle(.plain)
                .sensoryFeedback(.selection, trigger: active == i)
            }
        }
        .padding(8)
        .background(
            RoundedRectangle(cornerRadius: 16)
                .fill(.regularMaterial)
                .shadow(color: .black.opacity(0.22), radius: 16, y: 14)
        )
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.miroDivider.opacity(0.5), lineWidth: 1))
    }
}
```

### Primary Button (Yellow)

```swift
struct MiroPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.miroButton)
                .foregroundStyle(Color.miroInk)
                .padding(.vertical, 13).padding(.horizontal, 26)
                .background(Color.miroYellow)
                .clipShape(RoundedRectangle(cornerRadius: 10))
        }
        .buttonStyle(MiroPressStyle())
    }
}

struct MiroPressStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Zoom Pill

```swift
struct MiroZoomPill: View {
    @Binding var zoomPercent: Int
    var body: some View {
        VStack(spacing: 0) {
            Button { zoomPercent = min(zoomPercent + 25, 500) } label: {
                Image(systemName: "plus").font(.system(size: 18, weight: .semibold))
            }.frame(width: 38, height: 36)
            Text("\(zoomPercent)%")
                .font(.miroCaption).foregroundStyle(Color.miroTextSecondary)
                .frame(width: 38).padding(.vertical, 4)
                .overlay(Rectangle().frame(height: 1).foregroundStyle(Color.miroDivider), alignment: .top)
                .overlay(Rectangle().frame(height: 1).foregroundStyle(Color.miroDivider), alignment: .bottom)
            Button { zoomPercent = max(zoomPercent - 25, 25) } label: {
                Image(systemName: "minus").font(.system(size: 18, weight: .semibold))
            }.frame(width: 38, height: 36)
        }
        .foregroundStyle(Color.miroInk)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.miroChrome)
            .shadow(color: .black.opacity(0.16), radius: 10, y: 8))
    }
}
```

### Multiplayer Cursor

```swift
struct MiroCursor: View {
    let name: String
    let colorIndex: Int
    var body: some View {
        let color = miroCursorColors[colorIndex % miroCursorColors.count]
        HStack(spacing: 5) {
            Image(systemName: "cursorarrow.rays")
                .font(.system(size: 14, weight: .bold))
                .foregroundStyle(color)
                .rotationEffect(.degrees(-35))
            Text(name)
                .font(.miroTab)
                .foregroundStyle(.white)
                .padding(.horizontal, 7).padding(.vertical, 2)
                .background(Capsule().fill(color))
        }
    }
}
// Animate position changes with .animation(.linear(duration: 0.05), value: position) so cursors glide.
```

## 4. Bottom Tab Bar (Boards Home)

Inside a board there is no tab bar (the toolbar floats). The Boards-home shell uses a standard `TabView`.

```swift
struct MiroHomeTabs: View {
    var body: some View {
        TabView {
            BoardsListView().tabItem { Label("Boards", systemImage: "square.grid.2x2") }
            TemplatesView().tabItem { Label("Templates", systemImage: "rectangle.on.rectangle") }
            ActivityView().tabItem { Label("Activity", systemImage: "bell") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.miroInk)   // active is ink, no tint pill
    }
}
```

## 5. Motion

```swift
// Pan inertia — DragGesture .onEnded with predictedEndTranslation, then decelerate
// Pinch zoom — MagnificationGesture, anchored at pinch midpoint
// Double-tap zoom-to-fit
withAnimation(.easeOut(duration: 0.35)) { zoom = 1; offset = .zero }

// Object create (sticky) — spring scale-in
.transition(.scale(scale: 0.85).combined(with: .opacity))
// withAnimation(.spring(response: 0.20, dampingFraction: 0.7))

// Object drag — slight scale-up + shadow bloom, settle with spring
.scaleEffect(dragging ? 1.03 : 1)
// drop: withAnimation(.spring(response: 0.30, dampingFraction: 0.82))

// Tool select — yellow fill crossfade
withAnimation(.easeOut(duration: 0.12)) { active = i }

// Selection box — fade in
.transition(.opacity)  // 120ms

// Multiplayer cursor — interpolate between updates
.animation(.linear(duration: 0.05), value: remoteCursorPos)

// Sheet present
.presentationDetents([.medium, .large])
// spring(response: 0.40, dampingFraction: 0.82)

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: objectCreated)
.sensoryFeedback(.impact(flexibility: .rigid), trigger: connectorSnapped)
.sensoryFeedback(.selection, trigger: activeTool)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Select tool | `cursorarrow` | 20pt |
| Sticky note tool | `square.fill` (custom rounded) | 20pt |
| Pen tool | `pencil.tip` | 20pt |
| Shapes tool | `square.on.circle` | 20pt |
| Text tool | `textformat` | 20pt |
| Frame tool | `rectangle.dashed` | 20pt |
| Comment tool | `bubble.left` | 20pt |
| More tool | `plus` | 20pt |
| Zoom in / out | `plus` / `minus` | 18pt |
| Fit / minimap | `arrow.up.left.and.arrow.down.right` | 18pt |
| Back | `chevron.left` | 18pt |
| Board menu | `chevron.down` | 14pt |
| Board actions | `ellipsis` | 18pt |
| Collaborators | `person.2.fill` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Cursor (remote) | `cursorarrow.rays` | 14pt |
| Boards (tab) | `square.grid.2x2` | 24pt |
| Templates (tab) | `rectangle.on.rectangle` | 24pt |
| Activity (tab) | `bell` / `bell.fill` | 24pt |
| Profile (tab) | `person.crop.circle` | 24pt |

## 7. Dark Mode

```swift
struct MiroTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.miroDarkCanvas : Color.miroChrome)
            .foregroundStyle(scheme == .dark ? Color.miroDarkTextPrimary : Color.miroInk)
    }
}
extension View { func miroTheme() -> some View { modifier(MiroTheme()) } }
```

The board goes to `#202024` with grid `#38383F`; **sticky notes stay full-color pastels** (paper doesn't go dark — only chrome does). Miro Yellow and Miro Blue selection are identical across themes. Multiplayer cursor colors are unchanged. On dark, deepen object/chrome shadows and add a 1pt divider border to the floating toolbar/popovers as an elevation cue.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`); `.presentationDetents` requires iOS 16
- Bundle Inter (UI), Caveat (optional handwriting sticky), JetBrains Mono (code embeds) TTFs via `Info.plist` `UIAppFonts` — all SIL OFL licensed
- Dynamic Type: support on sheet titles, body, list rows, board title; keep toolbar labels, zoom %, avatar initials, connector labels, tab labels FIXED. Sticky-note text auto-fits its box independent of Dynamic Type
- VoiceOver: label sticky notes "Sticky note: {text}, {color}"; the canvas should expose objects as an accessible list (with a "Find on board" search) since spatial navigation is hard non-visually; toolbar tools labeled by name + selected state; remote cursors announced sparingly ("{name} is editing")
- Color contrast: `#050038` on `#FFD02F` passes WCAG AA for the primary button; `#2A2A33` on every sticky pastel passes AA; verify `#4262FF` selection chrome against the board
- Reduce Motion: disable pan inertia glide, the object scale-in, and zoom-to-fit animation (jump instead); keep the selection box (conveys state); freeze cursor interpolation to discrete updates
- Dark mode: board `#202024`, chrome `#26262B`; sticky notes stay full-color; shadows deepen + 1pt divider border on floating panels
- Touch targets: 40pt toolbar tiles, 38pt zoom buttons, 12pt resize handles all get ≥44pt hit areas via padding; list rows/buttons ≥44pt tall
