# VSCO (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates VSCO's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the editor's signature hairline slider, film-preset carousel, and tool tray.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — the editor's only mode)
    static let vscoBlack     = Color.black                                     // #000000
    static let vscoSurface1  = Color(red: 0.055, green: 0.055, blue: 0.055)   // #0E0E0E
    static let vscoSurface2  = Color(red: 0.102, green: 0.102, blue: 0.102)   // #1A1A1A
    static let vscoSurface3  = Color(red: 0.141, green: 0.141, blue: 0.141)   // #242424
    static let vscoDivider   = Color(red: 0.165, green: 0.165, blue: 0.165)   // #2A2A2A
    static let vscoTrack     = Color(red: 0.227, green: 0.227, blue: 0.227)   // #3A3A3A

    // MARK: - Canvas & Surfaces (Light — browsing screens only, NOT the editor)
    static let vscoLightCanvas   = Color.white                                 // #FFFFFF
    static let vscoLightSurface1 = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2
    static let vscoLightSurface2 = Color(red: 0.910, green: 0.910, blue: 0.910) // #E8E8E8
    static let vscoLightDivider  = Color(red: 0.863, green: 0.863, blue: 0.863) // #DCDCDC

    // MARK: - Text
    static let vscoTextPrimary    = Color.white                                // #FFFFFF
    static let vscoTextSecondary  = Color(red: 0.702, green: 0.702, blue: 0.702) // #B3B3B3
    static let vscoTextTertiary   = Color(red: 0.463, green: 0.463, blue: 0.463) // #767676
    static let vscoTextOnLight    = Color.black                                // #000000

    // MARK: - Interactive (white only — VSCO has no accent color)
    static let vscoWhite        = Color.white                                  // #FFFFFF
    static let vscoWhitePressed = Color(red: 0.859, green: 0.859, blue: 0.859) // #DBDBDB

    // MARK: - Incidental Film Tones (preset-preview content only — never UI fills)
    static let vscoFilmWarm    = Color(red: 0.784, green: 0.659, blue: 0.420) // #C8A86B
    static let vscoFilmCool    = Color(red: 0.494, green: 0.561, blue: 0.631) // #7E8FA1
    static let vscoFilmNeutral = Color(red: 0.604, green: 0.576, blue: 0.533) // #9A9388
    static let vscoFilmShadow  = Color(red: 0.247, green: 0.220, blue: 0.173) // #3F382C

    // MARK: - Semantic
    static let vscoDestructive = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
}
```

## 2. Typography

VSCO's brand face is the proprietary "VSCO Gothic". Bundle **Inter** (SIL OFL) as the closest free substitute. The signature is UPPERCASE chrome with wide tracking — model that with a `kerning` modifier, not the font.

```swift
extension Font {
    // Editorial (sentence-case content screens)
    static let vscoDisplay  = Font.custom("Inter-Bold",     size: 32)  // -0.4 tracking applied via .tracking()
    static let vscoTitle    = Font.custom("Inter-SemiBold", size: 24)
    static let vscoSection  = Font.custom("Inter-SemiBold", size: 19)
    static let vscoBody     = Font.custom("Inter-Regular",  size: 16)
    static let vscoLabel    = Font.custom("Inter-Medium",   size: 14)
    static let vscoCaption  = Font.custom("Inter-Regular",  size: 13)

    // Live numeric value (slider readout)
    static let vscoValue    = Font.custom("Inter-SemiBold", size: 14)

    // Chrome (UPPERCASE, wide-tracked — apply .tracking() at the call site)
    static let vscoToolLabel  = Font.custom("Inter-SemiBold", size: 12)  // .tracking(1.6).textCase(.uppercase)
    static let vscoPresetTag  = Font.custom("Inter-SemiBold", size: 11)  // .tracking(1.0).textCase(.uppercase)
    static let vscoNavAction  = Font.custom("Inter-SemiBold", size: 13)  // .tracking(1.4).textCase(.uppercase)
    static let vscoTopTitle   = Font.custom("Inter-Medium",   size: 13)  // .tracking(1.4).textCase(.uppercase)
    static let vscoTabLabel   = Font.custom("Inter-SemiBold", size: 10)  // .tracking(0.6).textCase(.uppercase)
    static let vscoButton     = Font.custom("Inter-SemiBold", size: 12)  // .tracking(1.4).textCase(.uppercase)
}

/// VSCO's signature chrome text: UPPERCASE + wide tracking.
struct VSCOChromeLabel: ViewModifier {
    let tracking: CGFloat
    func body(content: Content) -> some View {
        content
            .textCase(.uppercase)
            .tracking(tracking)
    }
}
extension View {
    func vscoChrome(tracking: CGFloat) -> some View { modifier(VSCOChromeLabel(tracking: tracking)) }
}
```

## 3. Signature Components

### Hairline Bipolar Slider (the core atom)

```swift
struct VSCOHairlineSlider: View {
    let toolName: String
    @Binding var value: Double          // -6.0 ... +6.0, 0 = neutral
    var range: ClosedRange<Double> = -6...6
    var defaultValue: Double = 0

    @State private var trackWidth: CGFloat = 0

    private var fraction: CGFloat { CGFloat((value - range.lowerBound) / (range.upperBound - range.lowerBound)) }
    private var zeroFraction: CGFloat { CGFloat((0 - range.lowerBound) / (range.upperBound - range.lowerBound)) }

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack {
                Text(toolName)
                    .font(.vscoToolLabel)
                    .vscoChrome(tracking: 1.6)
                    .foregroundStyle(Color.vscoTextSecondary)
                Spacer()
                Text(value >= 0 ? "+\(String(format: "%.1f", value))" : String(format: "%.1f", value))
                    .font(.vscoValue)
                    .foregroundStyle(Color.vscoTextPrimary)
                    .monospacedDigit()
            }

            GeometryReader { geo in
                let w = geo.size.width
                ZStack(alignment: .leading) {
                    Rectangle()
                        .fill(Color.vscoTrack)
                        .frame(height: 1)

                    Rectangle()
                        .fill(Color.vscoWhite)
                        .frame(width: abs(fraction - zeroFraction) * w, height: 1)
                        .offset(x: min(fraction, zeroFraction) * w)

                    Circle()
                        .fill(Color.vscoWhite)
                        .frame(width: 18, height: 18)
                        .shadow(color: .black.opacity(0.6), radius: 4, y: 1)
                        .offset(x: fraction * w - 9)
                        .frame(width: 44, height: 44)        // 44pt hit target
                        .contentShape(Rectangle())
                        .offset(x: -22 + 9)
                }
                .frame(height: 44)
                .contentShape(Rectangle())
                .gesture(
                    DragGesture(minimumDistance: 0)
                        .onChanged { g in
                            let f = max(0, min(1, g.location.x / w))
                            value = range.lowerBound + Double(f) * (range.upperBound - range.lowerBound)
                        }
                )
                .onTapGesture(count: 2) {
                    withAnimation(.easeOut(duration: 0.2)) { value = defaultValue }
                }
                .onAppear { trackWidth = w }
            }
            .frame(height: 44)
        }
        .sensoryFeedback(.selection, trigger: Int(value.rounded()))   // soft tick near detents
        .padding(.horizontal, 24)
    }
}
```

### Film-Preset Carousel (the signature)

```swift
struct VSCOPreset: Identifiable {
    let id = UUID()
    let code: String                 // "C1", "A6", "M5"
    let previewGradient: [Color]     // live-preview tint of the photo under this grade
}

struct VSCOPresetCarousel: View {
    let presets: [VSCOPreset]
    @Binding var selectedID: UUID?

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 14) {
                ForEach(presets) { preset in
                    let isActive = preset.id == selectedID
                    VStack(spacing: 7) {
                        RoundedRectangle(cornerRadius: 2)
                            .fill(LinearGradient(colors: preset.previewGradient,
                                                 startPoint: .topLeading, endPoint: .bottomTrailing))
                            .frame(width: 56, height: 56)
                            .overlay(
                                RoundedRectangle(cornerRadius: 2)
                                    .strokeBorder(Color.vscoWhite, lineWidth: isActive ? 2 : 0)
                            )
                        Text(preset.code)
                            .font(.vscoPresetTag)
                            .vscoChrome(tracking: 1.0)
                            .foregroundStyle(isActive ? Color.vscoTextPrimary : Color.vscoTextSecondary)
                    }
                    .contentShape(Rectangle())
                    .onTapGesture {
                        withAnimation(.easeInOut(duration: 0.25)) { selectedID = preset.id }
                    }
                }
            }
            .padding(.horizontal, 20)
            .padding(.vertical, 14)
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: selectedID)
    }
}
```

### Tool Tray

```swift
struct VSCOTool: Identifiable {
    let id = UUID()
    let symbol: String   // SF Symbol
    let label: String
}

struct VSCOToolTray: View {
    let tools: [VSCOTool]
    @Binding var activeID: UUID?

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 30) {
                ForEach(tools) { tool in
                    let isActive = tool.id == activeID
                    VStack(spacing: 8) {
                        Image(systemName: tool.symbol)
                            .font(.system(size: 22, weight: .regular))
                            .frame(width: 22, height: 22)
                        Text(tool.label)
                            .font(.vscoToolLabel)
                            .vscoChrome(tracking: 1.6)
                    }
                    .foregroundStyle(isActive ? Color.vscoTextPrimary : Color.vscoTextSecondary)
                    .frame(minWidth: 44, minHeight: 44)
                    .contentShape(Rectangle())
                    .onTapGesture {
                        withAnimation(.easeOut(duration: 0.18)) { activeID = tool.id }
                    }
                }
            }
            .padding(.horizontal, 26)
        }
        .frame(height: 84)
        .overlay(alignment: .top) {
            Rectangle().fill(Color.vscoDivider).frame(height: 0.5)
        }
    }
}
```

### Editor Top Bar

```swift
struct VSCOEditorTopBar: View {
    let onClose: () -> Void
    let onNext: () -> Void

    var body: some View {
        HStack {
            Button(action: onClose) {
                Image(systemName: "xmark")
                    .font(.system(size: 22, weight: .light))
                    .foregroundStyle(Color.vscoTextPrimary)
            }
            Spacer()
            Text("EDIT")
                .font(.vscoTopTitle)
                .vscoChrome(tracking: 1.4)
                .foregroundStyle(Color.vscoTextSecondary)
            Spacer()
            Button(action: onNext) {
                Text("NEXT")
                    .font(.vscoNavAction)
                    .vscoChrome(tracking: 1.4)
                    .foregroundStyle(Color.vscoTextPrimary)
            }
        }
        .padding(.horizontal, 20)
        .frame(height: 52)
        .background(Color.vscoBlack)
    }
}
```

### Studio Grid (gapless contact sheet)

```swift
struct VSCOStudioGrid: View {
    let photos: [URL]
    private let cols = Array(repeating: GridItem(.flexible(), spacing: 1), count: 4)

    var body: some View {
        ScrollView {
            LazyVGrid(columns: cols, spacing: 1) {
                ForEach(photos, id: \.self) { url in
                    AsyncImage(url: url) { img in
                        img.resizable().aspectRatio(1, contentMode: .fill)
                    } placeholder: {
                        Rectangle().fill(Color.vscoSurface2)
                    }
                    .aspectRatio(1, contentMode: .fill)
                    .clipped()       // 0pt radius — film negatives, no rounding
                }
            }
        }
        .background(Color.vscoSurface1)
    }
}
```

### Primary Button (2pt square, not a pill)

```swift
struct VSCOPrimaryButton: View {
    let title: String
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.vscoButton)
                .vscoChrome(tracking: 1.4)
                .foregroundStyle(Color.vscoBlack)
                .padding(.vertical, 14)
                .padding(.horizontal, 28)
                .background(RoundedRectangle(cornerRadius: 2)
                    .fill(pressed ? Color.vscoWhitePressed : Color.vscoWhite))
        }
        .buttonStyle(.plain)
        .scaleEffect(pressed ? 0.98 : 1)
        .simultaneousGesture(DragGesture(minimumDistance: 0)
            .onChanged { _ in pressed = true }
            .onEnded { _ in pressed = false })
    }
}
```

## 4. Bottom Tab Bar

```swift
struct VSCOTabView: View {
    var body: some View {
        TabView {
            StudioView().tabItem  { Image(systemName: "square.grid.2x2.fill") }
            FeedView().tabItem    { Image(systemName: "arrow.up") }
            DiscoverView().tabItem{ Image(systemName: "camera") }
            ProfileView().tabItem { Image(systemName: "person") }
        }
        .tint(.vscoWhite)              // active = white fill, NO tint pill
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = .black
            a.shadowColor = UIColor(white: 0.165, alpha: 1)   // #2A2A2A hairline
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// Preset apply — photo cross-dissolves between grades
withAnimation(.easeInOut(duration: 0.25)) { selectedPreset = newPreset }

// Slider double-tap reset
.onTapGesture(count: 2) { withAnimation(.easeOut(duration: 0.2)) { value = defaultValue } }

// Tool switch — slider fades in / carousel slides out (8pt horizontal offset)
.transition(.opacity.combined(with: .move(edge: .leading)))   // 0.18s ease-out

// Before/after press — INSTANT swap, no animation (immediacy is the point)
.onLongPressGesture(minimumDuration: 0, pressing: { showOriginal = $0 }) { }

// Tab switch — cross-fade only, no slide (set via TabView default + .animation)

// Haptics
.sensoryFeedback(.selection, trigger: detentCrossing)          // zero-detent tick
.sensoryFeedback(.impact(flexibility: .soft), trigger: selectedPresetID)
.sensoryFeedback(.impact(flexibility: .soft), trigger: didReset)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Close editor | `xmark` | 22pt (weight .light) |
| Filters tool | `camera.filters` | 22pt |
| Exposure tool | `sun.max` | 22pt |
| Contrast tool | `circle.lefthalf.filled` | 22pt |
| Saturation tool | `drop` | 22pt |
| Sharpen tool | `triangle` | 22pt |
| Crop tool | `crop` | 22pt |
| Straighten tool | `crop.rotate` | 22pt |
| Grain tool | `circle.grid.3x3` | 22pt |
| HSL tool | `paintpalette` | 22pt |
| Studio (tab) | `square.grid.2x2` / `.fill` | 23pt |
| Feed (tab) | `arrow.up` | 23pt |
| Discover (tab) | `camera` / `camera.fill` | 23pt |
| Profile (tab) | `person` / `person.fill` | 23pt |
| Import | `square.and.arrow.down` | 22pt |
| Settings | `gearshape` | 22pt |
| Recipe save | `plus.rectangle.on.rectangle` | 20pt |
| Before/after | (no icon — long-press the photo) | — |
| Multi-select check | `checkmark` | 14pt |

## 7. Dark Mode

VSCO's editor is **dark-native and has no light variant** — never invert the editing surface. Only the browsing screens (Studio grid, Discover, Profile) offer a light theme.

```swift
/// The editor is ALWAYS this — ignore colorScheme.
struct VSCOEditorTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.vscoBlack)
            .foregroundStyle(Color.vscoTextPrimary)
            .preferredColorScheme(.dark)        // force — the darkroom is never light
    }
}

/// Browsing screens MAY follow the system.
struct VSCOBrowseTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.vscoSurface1 : Color.vscoLightSurface1)
            .foregroundStyle(scheme == .dark ? Color.vscoTextPrimary : Color.vscoTextOnLight)
    }
}

extension View {
    func vscoEditorTheme() -> some View { modifier(VSCOEditorTheme()) }
    func vscoBrowseTheme() -> some View { modifier(VSCOBrowseTheme()) }
}
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UISelectionFeedbackGenerator`)
- Bundle Inter TTFs via `Info.plist` `UIAppFonts` — Inter is SIL OFL, free to ship; it substitutes for the proprietary VSCO Gothic
- Dynamic Type: scale editorial titles, section headers, body, captions; keep tool labels, preset codes, slider values, nav actions, tab labels FIXED (single-line, layout-critical) via `.font(...)` literal sizes
- VoiceOver: label the slider as "{Tool}, adjustable, {value}" and support increment/decrement via `.accessibilityValue` + `.accessibilityAdjustableAction`; label preset tiles "{Code} preset, selected/not selected"; the before/after long-press needs an `.accessibilityAction(named: "Show original")`
- Color contrast: `#FFFFFF` on `#000000` is maximal AAA; secondary `#B3B3B3` on black passes AA for the small UPPERCASE labels at 12pt; `#767676` is reserved for tertiary/disabled only
- Reduce Motion: replace preset cross-fades and tool-switch slides with instant cuts; the slider reset jumps rather than springs; keep the white selection ring (it conveys state, not motion)
- Reduce Transparency: VSCO uses no blur in the editor, so this is a no-op there; on browsing sheets it is already opaque `#1A1A1A`
- The slider knob's `rgba(0,0,0,0.6) 0 1px 4px` shadow is functional, not decorative — keep it even under Reduce Motion so the white knob stays visible over bright photo regions
- Force the editor to `.dark` regardless of system setting — a "light editor" would break the darkroom model and harm color judgement
