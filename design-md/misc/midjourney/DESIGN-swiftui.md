# Midjourney (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Midjourney's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. Midjourney is dark-only, true-black, with no brand accent — white is the only primary.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark-only — true OLED black)
    static let mjCanvas    = Color(red: 0.000, green: 0.000, blue: 0.000) // #000000
    static let mjSurface1  = Color(red: 0.055, green: 0.055, blue: 0.055) // #0E0E0E
    static let mjSurface2  = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let mjSurface3  = Color(red: 0.141, green: 0.141, blue: 0.141) // #242424
    static let mjDivider   = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A

    // MARK: - Text
    static let mjTextPrimary   = Color.white                                  // #FFFFFF
    static let mjTextSecondary = Color(red: 0.631, green: 0.631, blue: 0.631) // #A1A1A1
    static let mjTextTertiary  = Color(red: 0.431, green: 0.431, blue: 0.431) // #6E6E6E

    // MARK: - Primary (white is the ONLY primary — no brand accent)
    static let mjPrimary        = Color.white                                  // #FFFFFF
    static let mjPrimaryPressed = Color(red: 0.855, green: 0.855, blue: 0.855) // #DADADA

    // MARK: - Heritage / link
    static let mjRateBlue  = Color(red: 0.176, green: 0.498, blue: 0.976) // #2D7FF9
    static let mjBlurple   = Color(red: 0.302, green: 0.357, blue: 0.808) // #4D5BCE (Discord connect only)

    // MARK: - Semantic
    static let mjSuccess = Color(red: 0.180, green: 0.800, blue: 0.443) // #2ECC71
    static let mjWarning = Color(red: 0.961, green: 0.651, blue: 0.137) // #F5A623
    static let mjError   = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
}
```

## 2. Typography

Midjourney has two voices: a quiet sans for UI, monospace for prompts and parameters. Bundle Inter; SF Mono is the system monospace.

```swift
extension Font {
    // UI sans (Inter — falls back to SF Pro)
    static let mjScreenTitle = Font.custom("Inter-ExtraBold", size: 32).weight(.heavy)
    static let mjNavTitle    = Font.custom("Inter-Bold",      size: 22).weight(.bold)
    static let mjSection     = Font.custom("Inter-Bold",      size: 19).weight(.bold)
    static let mjBody        = Font.custom("Inter-Regular",   size: 16).weight(.regular)
    static let mjAction      = Font.custom("Inter-SemiBold",  size: 15).weight(.semibold)
    static let mjCardTitle   = Font.custom("Inter-SemiBold",  size: 15).weight(.semibold)
    static let mjMeta        = Font.custom("Inter-Regular",   size: 14).weight(.regular)
    static let mjCaption     = Font.custom("Inter-Medium",    size: 12).weight(.medium)
    static let mjTab         = Font.custom("Inter-Medium",    size: 10).weight(.medium)
    static let mjPlaceholder = Font.custom("Inter-Regular",   size: 14).weight(.regular)

    // Prompt / parameter monospace — the "this is an instruction" voice
    static let mjPrompt   = Font.system(size: 13, weight: .regular, design: .monospaced)
    static let mjParam    = Font.system(size: 13, weight: .semibold, design: .monospaced)
    static let mjJobChip  = Font.system(size: 13, weight: .semibold, design: .monospaced)
}
```

## 3. Signature Components

### Job Card (prompt + 2×2 grid + action row)

```swift
struct JobCard: View {
    let prompt: AttributedString          // key phrase styled #FFFFFF, rest #A1A1A1
    let cellColors: [Color]               // 4 placeholder fills or use AsyncImage
    let aspect: CGFloat                   // 1.0 for --ar 1:1, 1.5 for 3:2, etc.
    let onUpscale: (Int) -> Void
    let onFavorite: () -> Void
    let onDownload: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 10) {
            Text(prompt)
                .font(.mjPrompt)
                .foregroundStyle(Color.mjTextSecondary)
                .lineLimit(2)

            LazyVGrid(columns: [GridItem(.flexible(), spacing: 6),
                                GridItem(.flexible(), spacing: 6)], spacing: 6) {
                ForEach(0..<4, id: \.self) { i in
                    Rectangle()
                        .fill(cellColors[i])
                        .aspectRatio(aspect, contentMode: .fill)
                        .clipped()
                }
            }
            .clipShape(RoundedRectangle(cornerRadius: 12))

            HStack(spacing: 14) {
                Button(action: onFavorite) {
                    Image(systemName: "heart").font(.system(size: 18))
                }
                Button(action: onDownload) {
                    Image(systemName: "square.and.arrow.down").font(.system(size: 18))
                }
                Button { } label: {
                    Image(systemName: "ellipsis").font(.system(size: 18))
                }
                .foregroundStyle(Color.mjTextSecondary)
                Spacer()
                ForEach(1...4, id: \.self) { n in
                    UpscaleChip(label: "U\(n)") { onUpscale(n) }
                }
            }
            .foregroundStyle(Color.mjTextSecondary)
        }
        .padding(.horizontal, 14)
        .padding(.vertical, 9)
        .background(Color.mjCanvas)
    }
}
```

### Upscale / Variation Chips (the Discord-bot grammar)

```swift
struct UpscaleChip: View {
    let label: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.mjJobChip)
                .foregroundStyle(Color.black)
                .padding(.horizontal, 14).padding(.vertical, 8)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.mjPrimary))
        }
        .buttonStyle(PressableScale())
    }
}

struct VariationChip: View {
    let label: String                  // "V1" / "🔄 Re-roll"
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.mjJobChip)
                .foregroundStyle(Color.mjTextPrimary)
                .padding(.horizontal, 14).padding(.vertical, 8)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.mjSurface2))
                .overlay(RoundedRectangle(cornerRadius: 8).strokeBorder(Color.mjDivider, lineWidth: 1))
        }
        .buttonStyle(PressableScale())
    }
}

struct PressableScale: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.96 : 1)
            .opacity(configuration.isPressed ? 0.85 : 1)
            .animation(.easeOut(duration: 0.09), value: configuration.isPressed)
    }
}
```

### Prompt Bar (signature input)

```swift
struct PromptBar: View {
    @Binding var text: String
    @FocusState private var focused: Bool
    let onImagine: () -> Void

    var body: some View {
        HStack(spacing: 10) {
            TextField("", text: $text, prompt:
                Text("What will you imagine?").foregroundStyle(Color.mjTextTertiary))
                .font(.mjPrompt)
                .foregroundStyle(Color.mjTextPrimary)
                .focused($focused)
                .padding(.leading, 18)

            Button {
                UIImpactFeedbackGenerator(style: .soft).impactOccurred()
                onImagine()
            } label: {
                Image(systemName: "arrow.right")
                    .font(.system(size: 16, weight: .bold))
                    .foregroundStyle(Color.black)
                    .frame(width: 36, height: 36)
                    .background(Circle().fill(text.isEmpty ? Color.mjSurface3 : Color.mjPrimary))
            }
            .disabled(text.isEmpty)
            .padding(.trailing, 7)
        }
        .frame(height: 50)
        .background(
            RoundedRectangle(cornerRadius: 25).fill(Color.mjSurface2)
                .overlay(RoundedRectangle(cornerRadius: 25)
                    .strokeBorder(focused ? Color(white: 0.23) : Color.mjDivider, lineWidth: 1))
        )
        .padding(.horizontal, 14)
    }
}
```

### Parameter Helper Strip

```swift
struct ParameterStrip: View {
    let flags = ["--ar 1:1", "--ar 16:9", "--v 6", "--style raw", "--stylize 250"]
    let onPick: (String) -> Void
    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 8) {
                ForEach(flags, id: \.self) { f in
                    Button { onPick(f) } label: {
                        Text(f)
                            .font(.mjParam)
                            .foregroundStyle(Color.mjTextPrimary)
                            .padding(.horizontal, 14).padding(.vertical, 8)
                            .background(RoundedRectangle(cornerRadius: 8).fill(Color.mjSurface2))
                            .overlay(RoundedRectangle(cornerRadius: 8)
                                .strokeBorder(Color.mjDivider, lineWidth: 1))
                    }
                }
            }
            .padding(.horizontal, 14)
        }
        .transition(.opacity.combined(with: .move(edge: .bottom)))
    }
}
```

### Render-Progress Placeholder

```swift
struct RenderingGrid: View {
    let percent: Int
    @State private var shimmer = false
    var body: some View {
        ZStack {
            RoundedRectangle(cornerRadius: 12)
                .fill(LinearGradient(colors: [.mjSurface2, .mjSurface3, .mjSurface2],
                                     startPoint: shimmer ? .leading : .trailing,
                                     endPoint: shimmer ? .trailing : .leading))
                .aspectRatio(1, contentMode: .fit)
            Text("\(percent)%")
                .font(.mjMeta).foregroundStyle(Color.mjTextSecondary)
        }
        .onAppear {
            withAnimation(.easeInOut(duration: 1.4).repeatForever(autoreverses: true)) {
                shimmer = true
            }
        }
    }
}
```

### Primary Button (Imagine — white is the only primary)

```swift
struct ImagineButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.mjAction)
                .foregroundStyle(Color.black)
                .padding(.horizontal, 28).padding(.vertical, 14)
                .frame(maxWidth: .infinity)
                .background(Capsule().fill(Color.mjPrimary))
        }
        .buttonStyle(PressableScale())
    }
}
```

## 4. Bottom Tab Bar

```swift
struct MJTabView: View {
    var body: some View {
        TabView {
            CreateView().tabItem  { Label("Create",  systemImage: "house") }
            ExploreView().tabItem { Label("Explore", systemImage: "square.grid.2x2") }
            SearchView().tabItem  { Label("Search",  systemImage: "magnifyingglass") }
            LibraryView().tabItem { Label("Library", systemImage: "photo.on.rectangle") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.mjTextPrimary)            // active = white; NO tint pill (no brand accent)
        .toolbarBackground(Color.mjCanvas, for: .tabBar)
        .toolbarColorScheme(.dark, for: .tabBar)
    }
}
```

## 5. Motion

```swift
// Job submit — Imagine button pulse + new card slides in at top
withAnimation(.easeOut(duration: 0.25)) { jobs.insert(newJob, at: 0) }
// Imagine pulse
.scaleEffect(pulsing ? 0.96 : 1).animation(.easeOut(duration: 0.12), value: pulsing)

// Render shimmer — 1.4s repeating sweep #1A1A1A → #2A2A2A
.repeatForever(autoreverses: true)

// Resolve — placeholder crossfades to images, scale 0.98 → 1
.transition(.opacity).animation(.easeOut(duration: 0.25), value: resolved)

// Upscale → lightbox shared-element zoom
@Namespace var ns
// .matchedGeometryEffect(id: "cell\(i)", in: ns) — 220ms

// Lightbox paging — 1:1 drag, spring on release
.animation(.spring(response: 0.28, dampingFraction: 0.85), value: pageIndex)

// Tab change — instant color swap, NO slide animation

// Haptics
UIImpactFeedbackGenerator(style: .soft).impactOccurred()   // Imagine submit
UIImpactFeedbackGenerator(style: .light).impactOccurred()  // chip tap
UINotificationFeedbackGenerator().notificationOccurred(.success) // job complete
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Create (tab) | `house` / `house.fill` | 23pt |
| Explore (tab) | `square.grid.2x2` / `.fill` | 23pt |
| Search (tab) | `magnifyingglass` | 23pt |
| Library (tab) | `photo.on.rectangle` | 23pt |
| Profile (tab) | `person.crop.circle` | 23pt |
| Imagine (send) | `arrow.right` | 16pt |
| Favorite | `heart` / `heart.fill` | 18pt |
| Download | `square.and.arrow.down` | 18pt |
| More | `ellipsis` | 18pt |
| Filter | `line.3.horizontal.decrease` | 21pt |
| Re-roll | `arrow.clockwise` | 16pt |
| Zoom out | `arrow.down.backward.and.arrow.up.forward` | 18pt |
| Remix | `wand.and.stars` | 18pt |
| Vary | `square.on.square` | 18pt |
| Back | `chevron.left` | 22pt |
| Settings | `gearshape` | 21pt |
| Discord connect | `bubble.left.and.bubble.right` | 18pt |

## 7. Dark Mode

Midjourney is **dark-only** — there is no light scheme. Force dark and pin the canvas to true black.

```swift
struct MJTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.mjCanvas)
            .foregroundStyle(Color.mjTextPrimary)
            .preferredColorScheme(.dark)        // dark-only product
            .tint(.mjTextPrimary)               // white is the only primary
    }
}

extension View { func mjTheme() -> some View { modifier(MJTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.matchedGeometryEffect`, `.toolbarBackground` are comfortable here; `UIImpactFeedbackGenerator` for haptics works back to iOS 13).
- Bundle Inter (SIL OFL) for the UI face; SF Mono is the system monospace for prompts/parameters — no extra license needed.
- Dark-only: set `.preferredColorScheme(.dark)` at the root; never expose a light variant. Canvas is `#000000` exactly (OLED black) — do not substitute `#0E0E0E`.
- Dynamic Type: scale screen title, section headings, body, metadata; pin tab labels, job chips (U1/V1), parameter chips, and prompt monospace at FIXED sizes (the 2×2 grid layout is size-sensitive).
- VoiceOver: label the job card as "Job: {prompt}"; label each grid cell "Image {n} of 4, double-tap to open"; label upscale chips "Upscale image {n}" and variation chips "Variations of image {n}"; the re-roll chip as "Re-roll prompt".
- Color contrast: `#FFFFFF` on `#000000` is maximal; `#A1A1A1` on `#000000` passes WCAG AA for metadata at 14pt; `#6E6E6E` placeholder is intentionally low-emphasis (non-essential).
- No brand accent: never introduce a tint pill on the active tab or a colored primary button — white (`#FFFFFF`) is the only primary. Reserve blurple (`#4D5BCE`) strictly for the "Connect Discord" affordance.
- Reduce Motion: disable the render shimmer (show a static `#1A1A1A` placeholder + percentage), the resolve scale, and the lightbox spring — substitute instant crossfades.
- Reduce Transparency: replace the tab bar's `rgba(0,0,0,0.92)` blur with solid `#000000`.
- Imagery is content: never apply a tint, color filter, or overlay to generated images for "theming" — the grid is the only color in the UI and must render verbatim.
