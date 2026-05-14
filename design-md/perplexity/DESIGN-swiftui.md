# Perplexity (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Perplexity's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the triple-circle brand mark `Shape`, themed components for the search input, source cards, citation chips, the answer block, and the streaming cursor.

Targets iOS 17+. The modern `.sensoryFeedback`, scroll-to-id animations, and `phaseAnimator` for the searching-dots all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas (dark-first)
    static let pplxCanvas    = Color(red: 0.039, green: 0.039, blue: 0.039) // #0A0A0A
    static let pplxSurface1  = Color(red: 0.090, green: 0.090, blue: 0.090) // #171717
    static let pplxSurface2  = Color(red: 0.122, green: 0.122, blue: 0.122) // #1F1F1F
    static let pplxSurface3  = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A
    static let pplxDivider   = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A

    // MARK: - Text
    static let pplxTextPrimary   = Color(red: 0.980, green: 0.980, blue: 0.980) // #FAFAFA off-white
    static let pplxTextSecondary = Color(red: 0.631, green: 0.631, blue: 0.631) // #A1A1A1
    static let pplxTextTertiary  = Color(red: 0.431, green: 0.431, blue: 0.431) // #6E6E6E
    static let pplxTextMuted     = Color(red: 0.290, green: 0.290, blue: 0.290) // #4A4A4A

    // MARK: - Perplexity Teal (signature accent)
    static let pplxTeal       = Color(red: 0.125, green: 0.722, blue: 0.804) // #20B8CD
    static let pplxTealBright = Color(red: 0.239, green: 0.839, blue: 0.925) // #3DD6EC streaming cursor
    static let pplxTealDeep   = Color(red: 0.082, green: 0.569, blue: 0.639) // #1591A3 pressed
    static let pplxTealSoft   = Color(red: 0.059, green: 0.227, blue: 0.259) // #0F3A42 Pro Steps fill

    // MARK: - Code & syntax
    static let pplxCodeBg     = Color(red: 0.055, green: 0.055, blue: 0.055) // #0E0E0E
    static let pplxCodeFg     = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5
    static let pplxSyntaxStr  = Color(red: 0.659, green: 0.878, blue: 0.388) // #A8E063 lime
    static let pplxSyntaxNum  = Color(red: 0.949, green: 0.651, blue: 0.353) // #F2A65A orange
    static let pplxSyntaxFunc = Color(red: 0.725, green: 0.533, blue: 0.949) // #B988F2 purple

    // MARK: - Semantic
    static let pplxSuccess = Color(red: 0.133, green: 0.773, blue: 0.369) // #22C55E
    static let pplxWarning = Color(red: 0.961, green: 0.620, blue: 0.043) // #F59E0B
    static let pplxError   = Color(red: 0.937, green: 0.267, blue: 0.267) // #EF4444
    static let pplxProGold = Color(red: 0.878, green: 0.702, blue: 0.255) // #E0B341

    // MARK: - Light mode (Perplexity supports light but defaults dark)
    static let pplxLightCanvas    = Color(red: 1.000, green: 1.000, blue: 1.000) // #FFFFFF
    static let pplxLightSurface1  = Color(red: 0.969, green: 0.969, blue: 0.969) // #F7F7F7
    static let pplxLightSurface2  = Color(red: 0.937, green: 0.937, blue: 0.937) // #EFEFEF
    static let pplxLightDivider   = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5
    static let pplxLightTextPri   = Color(red: 0.067, green: 0.067, blue: 0.067) // #111111
    static let pplxLightTextSec   = Color(red: 0.333, green: 0.333, blue: 0.333) // #555555
    static let pplxTealLight      = Color(red: 0.082, green: 0.569, blue: 0.639) // #1591A3 (WCAG)
}
```

## 2. Typography

FK Grotesk Neue is the brand-voice typeface; Inter is the body face. Bundle both via `Info.plist` (`UIAppFonts`). Falls back to SF Pro — both faces share geometric humanist characteristics.

```swift
extension Font {
    // Display & chrome (FK Grotesk)
    static let pplxDisplay     = Font.custom("FKGroteskNeue-Bold",     size: 30) // "Discover", "Library"
    static let pplxQuestion    = Font.custom("FKGroteskNeue-Semibold", size: 22) // Question at top of answer
    static let pplxSection     = Font.custom("FKGroteskNeue-Semibold", size: 18) // "Sources", "Related"
    static let pplxSubsection  = Font.custom("FKGroteskNeue-Semibold", size: 16)

    // Body (Inter)
    static let pplxBody        = Font.custom("Inter-Regular",          size: 16) // Answer body
    static let pplxBodyBold    = Font.custom("Inter-SemiBold",         size: 16)
    static let pplxList        = Font.custom("Inter-Regular",          size: 16)
    static let pplxBodySmall   = Font.custom("Inter-Regular",          size: 14)
    static let pplxMeta        = Font.custom("Inter-Regular",          size: 12)
    static let pplxCaption     = Font.custom("Inter-Regular",          size: 11)

    // Source cards
    static let pplxSourceTitle  = Font.custom("Inter-Medium",          size: 13)
    static let pplxSourceDomain = Font.custom("Inter-Regular",         size: 11)

    // Citation chip (monospace for typographic alignment)
    static let pplxCitation     = Font.custom("FKGroteskMono-Medium",  size: 11)

    // Buttons & chrome
    static let pplxButton       = Font.custom("FKGroteskNeue-Semibold", size: 15)
    static let pplxProBadge     = Font.custom("FKGroteskNeue-Bold",     size: 11)
    static let pplxChip         = Font.custom("FKGroteskNeue-Medium",   size: 13)
    static let pplxTab          = Font.custom("FKGroteskNeue-Medium",   size: 10)
    static let pplxSenderLabel  = Font.custom("FKGroteskNeue-Medium",   size: 13)
    static let pplxGroupHeader  = Font.custom("FKGroteskNeue-Bold",     size: 11) // SIDEBAR DATE GROUPS

    // Code
    static let pplxCodeBlock    = Font.custom("JetBrainsMono-Regular",  size: 14)
    static let pplxCodeInline   = Font.custom("JetBrainsMono-Medium",   size: 14)
    static let pplxCodeLang     = Font.custom("JetBrainsMono-Regular",  size: 11)

    // Search input placeholder (matches body)
    static let pplxSearch       = Font.custom("Inter-Regular",          size: 16)
}

// Fallback when fonts aren't bundled — SF Pro is the closest geometric humanist substitute
extension Font {
    static func pplx(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. The Triple-Circle Brand Mark

```swift
struct PerplexityMark: Shape {
    func path(in rect: CGRect) -> Path {
        var path = Path()
        let c = CGPoint(x: rect.midX, y: rect.midY)
        let r = min(rect.width, rect.height) * 0.32
        let d = r * 0.65

        // Three overlapping circles in a triangular formation
        let angles: [CGFloat] = [-CGFloat.pi / 2, CGFloat.pi / 6, 5 * CGFloat.pi / 6]
        for a in angles {
            let center = CGPoint(x: c.x + d * cos(a), y: c.y + d * sin(a))
            path.addEllipse(in: CGRect(x: center.x - r, y: center.y - r, width: r * 2, height: r * 2))
        }
        return path
    }
}

struct PerplexityAvatar: View {
    var size: CGFloat = 20
    var color: Color = .pplxTeal

    var body: some View {
        PerplexityMark()
            .fill(color)
            .frame(width: size, height: size)
    }
}
```

## 4. Signature Components

### Search Input (the home of the app)

```swift
struct SearchInput: View {
    @Binding var text: String
    @FocusState private var focused: Bool
    var onSubmit: () -> Void
    var onAttach: () -> Void

    var canSend: Bool { !text.trimmingCharacters(in: .whitespaces).isEmpty }

    var body: some View {
        HStack(alignment: .bottom, spacing: 8) {
            TextField("Ask anything…", text: $text, axis: .vertical)
                .focused($focused)
                .font(.pplxSearch)
                .foregroundStyle(Color.pplxTextPrimary)
                .padding(.vertical, 14)
                .lineLimit(1...8)

            Button(action: onAttach) {
                Image(systemName: "paperclip")
                    .font(.system(size: 18))
                    .foregroundStyle(Color.pplxTextSecondary)
            }
            .padding(.bottom, 14)

            Button(action: {
                if canSend { onSubmit(); text = "" }
            }) {
                Image(systemName: "arrow.up")
                    .font(.system(size: 14, weight: .heavy))
                    .foregroundStyle(canSend ? Color.pplxCanvas : Color.pplxTextTertiary)
                    .frame(width: 36, height: 36)
                    .background(Circle().fill(canSend ? Color.pplxTeal : Color.pplxSurface3))
            }
            .disabled(!canSend)
            .padding(.bottom, 10)
            .sensoryFeedback(.impact(weight: .medium), trigger: canSend)
        }
        .padding(.horizontal, 18)
        .background(
            RoundedRectangle(cornerRadius: 24)
                .fill(Color.pplxSurface1)
                .overlay(
                    RoundedRectangle(cornerRadius: 24)
                        .strokeBorder(focused ? Color.pplxTeal : Color.pplxSurface3, lineWidth: focused ? 1.5 : 1)
                )
                .shadow(color: focused ? Color.pplxTeal.opacity(0.25) : .clear, radius: 0, x: 0, y: 0)
                .overlay(
                    RoundedRectangle(cornerRadius: 24)
                        .strokeBorder(Color.pplxTeal.opacity(focused ? 0.25 : 0), lineWidth: 4)
                        .blur(radius: focused ? 4 : 0)
                )
        )
    }
}
```

### Source Card

```swift
struct SourceCard: View {
    let number: Int
    let faviconURL: URL?
    let domain: String
    let title: String

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            HStack(spacing: 6) {
                AsyncImage(url: faviconURL) { phase in
                    if let img = phase.image { img.resizable() }
                    else { Color.pplxSurface3 }
                }
                .frame(width: 16, height: 16)
                .clipShape(RoundedRectangle(cornerRadius: 3))

                Text(domain)
                    .font(.pplxSourceDomain)
                    .foregroundStyle(Color.pplxTextSecondary)
                    .lineLimit(1)

                Spacer()

                // Number badge
                Text("\(number)")
                    .font(.system(size: 10, weight: .bold, design: .monospaced))
                    .foregroundStyle(Color.pplxTextSecondary)
                    .padding(.horizontal, 5).padding(.vertical, 1)
                    .background(RoundedRectangle(cornerRadius: 3).fill(Color.pplxSurface2))
            }

            Text(title)
                .font(.pplxSourceTitle)
                .foregroundStyle(Color.pplxTextPrimary)
                .lineLimit(2)
                .multilineTextAlignment(.leading)
        }
        .padding(12)
        .frame(width: 200, height: 80, alignment: .leading)
        .background(
            RoundedRectangle(cornerRadius: 12)
                .fill(Color.pplxSurface1)
                .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.pplxSurface3, lineWidth: 1))
        )
    }
}
```

### Citation Chip (inline `[1]` `[2]` `[3]`)

```swift
struct CitationChip: View {
    let number: Int
    @State private var focused = false
    var onTap: () -> Void

    var body: some View {
        Button(action: {
            withAnimation(.spring(response: 0.2, dampingFraction: 0.7)) {
                focused = true
            }
            onTap()
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.3) {
                withAnimation { focused = false }
            }
        }) {
            Text("\(number)")
                .font(.pplxCitation)
                .foregroundStyle(focused ? Color.pplxTeal : Color.pplxTextSecondary)
                .padding(.horizontal, 5)
                .frame(height: 18)
                .background(
                    RoundedRectangle(cornerRadius: 4)
                        .fill(focused ? Color.pplxTealSoft : Color.pplxSurface2)
                        .overlay(
                            RoundedRectangle(cornerRadius: 4).strokeBorder(
                                focused ? Color.pplxTeal : Color.pplxSurface3,
                                lineWidth: 1
                            )
                        )
                )
        }
        .buttonStyle(.plain)
        .contentShape(Rectangle().inset(by: -8)) // 32pt tap target
        .sensoryFeedback(.impact(weight: .soft), trigger: focused)
    }
}
```

### Answer Block

```swift
struct AnswerBlock: View {
    let content: AttributedString  // pre-rendered markdown with citation references
    let isStreaming: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack(spacing: 8) {
                PerplexityAvatar(size: 18)
                Text("Answer")
                    .font(.pplxSenderLabel)
                    .foregroundStyle(Color.pplxTextSecondary)
            }

            Text(content)
                .font(.pplxBody)
                .foregroundStyle(Color.pplxTextPrimary)
                .lineSpacing(6) // ~1.5x line-height

            if isStreaming { StreamingCursor() }

            // Action row
            HStack(spacing: 12) {
                ActionPill(icon: "doc.on.doc", label: "Copy")
                ActionPill(icon: "square.and.arrow.up", label: "Share")
                ActionPill(icon: "bookmark", label: "Save")
            }
            .padding(.top, 8)
        }
        .padding(.horizontal, 4)
        .frame(maxWidth: .infinity, alignment: .leading)
    }
}

struct ActionPill: View {
    let icon: String
    let label: String

    var body: some View {
        HStack(spacing: 6) {
            Image(systemName: icon).font(.system(size: 13)).foregroundStyle(Color.pplxTextSecondary)
            Text(label).font(.pplxChip).foregroundStyle(Color.pplxTextPrimary)
        }
        .padding(.vertical, 8)
        .padding(.horizontal, 14)
        .background(
            RoundedRectangle(cornerRadius: 8)
                .fill(Color.pplxSurface2)
                .overlay(RoundedRectangle(cornerRadius: 8).strokeBorder(Color.pplxSurface3, lineWidth: 1))
        )
    }
}
```

### Streaming Cursor

```swift
struct StreamingCursor: View {
    @State private var visible = true

    var body: some View {
        Rectangle()
            .fill(Color.pplxTealBright)
            .frame(width: 6, height: 16)
            .cornerRadius(1)
            .opacity(visible ? 1 : 0)
            .onAppear {
                withAnimation(.linear(duration: 0.25).repeatForever()) { visible = false }
            }
            .accessibilityLabel("Generating answer")
    }
}
```

### Pro Search Toggle

```swift
struct ProSearchToggle: View {
    @Binding var isOn: Bool

    var body: some View {
        Button {
            withAnimation(.spring(response: 0.2, dampingFraction: 0.8)) {
                isOn.toggle()
            }
        } label: {
            HStack(spacing: 6) {
                Image(systemName: "sparkles")
                    .font(.system(size: 11, weight: .semibold))
                    .foregroundStyle(isOn ? Color.pplxCanvas : Color.pplxTextSecondary)
                Text("Pro")
                    .font(.pplxProBadge)
                    .foregroundStyle(isOn ? Color.pplxCanvas : Color.pplxTextSecondary)
            }
            .padding(.vertical, 6)
            .padding(.horizontal, 14)
            .background(
                Capsule().fill(isOn ? Color.pplxTeal : Color.pplxSurface2)
                    .overlay(Capsule().strokeBorder(isOn ? Color.clear : Color.pplxSurface3, lineWidth: 1))
            )
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.selection, trigger: isOn)
    }
}
```

### Searching the Web Indicator

```swift
struct SearchingIndicator: View {
    @State private var phase = 0

    var body: some View {
        HStack(spacing: 8) {
            HStack(spacing: 6) {
                ForEach(0..<3, id: \.self) { i in
                    Circle()
                        .fill(Color.pplxTeal)
                        .frame(width: 6, height: 6)
                        .scaleEffect(phase == i ? 1.3 : 1.0)
                        .animation(.easeInOut(duration: 0.4).delay(Double(i) * 0.15), value: phase)
                }
            }
            Text("Searching the web…")
                .font(.pplxSenderLabel)
                .foregroundStyle(Color.pplxTextSecondary)
        }
        .onAppear {
            Timer.scheduledTimer(withTimeInterval: 0.4, repeats: true) { _ in
                phase = (phase + 1) % 3
            }
        }
    }
}
```

### Pro Steps Card

```swift
struct ProStepsCard: View {
    let steps: [String]
    @State private var expanded = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Button {
                withAnimation(.easeOut(duration: 0.3)) { expanded.toggle() }
            } label: {
                HStack(spacing: 6) {
                    Image(systemName: "sparkles").font(.system(size: 14)).foregroundStyle(Color.pplxTeal)
                    Text("Steps").font(.pplxSenderLabel).foregroundStyle(Color.pplxTextPrimary)
                    Text("(\(steps.count))").font(.pplxMeta).foregroundStyle(Color.pplxTextSecondary)
                    Spacer()
                    Image(systemName: expanded ? "chevron.up" : "chevron.down")
                        .font(.system(size: 12))
                        .foregroundStyle(Color.pplxTextSecondary)
                }
            }

            if expanded {
                VStack(alignment: .leading, spacing: 10) {
                    ForEach(Array(steps.enumerated()), id: \.offset) { idx, step in
                        HStack(spacing: 8) {
                            Image(systemName: "checkmark.circle.fill")
                                .font(.system(size: 14))
                                .foregroundStyle(Color.pplxTeal)
                            Text(step).font(.pplxBodySmall).foregroundStyle(Color.pplxTextPrimary)
                            Spacer()
                        }
                    }
                }
                .padding(.top, 12)
            }
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.pplxTealSoft))
    }
}
```

### Related Questions Card

```swift
struct RelatedQuestionsCard: View {
    let questions: [String]
    var onSelect: (String) -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text("Related")
                .font(.pplxSenderLabel)
                .foregroundStyle(Color.pplxTextSecondary)
                .padding(.bottom, 8)

            ForEach(Array(questions.enumerated()), id: \.offset) { idx, q in
                Button { onSelect(q) } label: {
                    HStack {
                        Text(q).font(.pplxBodySmall).foregroundStyle(Color.pplxTextPrimary)
                            .lineLimit(1)
                        Spacer()
                        Image(systemName: "arrow.right")
                            .font(.system(size: 14))
                            .foregroundStyle(Color.pplxTextSecondary)
                    }
                    .padding(.vertical, 14)
                    .padding(.horizontal, 16)
                }
                .buttonStyle(.plain)
                .sensoryFeedback(.selection, trigger: idx)

                if idx < questions.count - 1 {
                    Rectangle().fill(Color.pplxSurface3).frame(height: 1).padding(.horizontal, 16)
                }
            }
        }
        .padding(.vertical, 16)
        .padding(.horizontal, 4)
    }
}
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        appearance.backgroundColor = UIColor(Color.pplxCanvas)
        appearance.shadowColor = UIColor(Color.pplxSurface3)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            HomeView()     .tabItem { Label("Home",     systemImage: "magnifyingglass") }
            DiscoverView() .tabItem { Label("Discover", systemImage: "safari") }
            LibraryView()  .tabItem { Label("Library",  systemImage: "books.vertical") }
            SpacesView()   .tabItem { Label("Spaces",   systemImage: "square.stack.3d.down.right") }
        }
        .tint(Color.pplxTextPrimary)
    }
}
```

## 6. Motion & Haptics

```swift
// Send tap
.sensoryFeedback(.impact(weight: .medium), trigger: sendPressed)

// Citation chip tap
.sensoryFeedback(.impact(weight: .soft), trigger: chipFocused)

// Pro toggle
.sensoryFeedback(.selection, trigger: proOn)

// Tab switch
.sensoryFeedback(.selection, trigger: selectedTab)

// Streaming cursor blink
withAnimation(.linear(duration: 0.25).repeatForever()) { visible.toggle() }

// Source card row cascade-in
withAnimation(.easeOut(duration: 0.35).delay(Double(idx) * 0.06)) { cardOpacity = 1; cardOffset = 0 }

// Citation → source card scroll-to
withAnimation(.easeOut(duration: 0.4)) { proxy.scrollTo(sourceID, anchor: .center) }
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Send (arrow up) | `arrow.up` | 14pt |
| Attach (paperclip) | `paperclip` | 18pt |
| Copy | `doc.on.doc` | 13pt |
| Share | `square.and.arrow.up` | 13pt |
| Save | `bookmark` / `bookmark.fill` | 13pt |
| Pro sparkles | `sparkles` | 11-14pt |
| Step check | `checkmark.circle.fill` | 14pt |
| Chevron up/down | `chevron.up` / `chevron.down` | 12pt |
| Related arrow | `arrow.right` | 14pt |
| Home tab | `magnifyingglass` | 22pt |
| Discover tab | `safari` | 22pt |
| Library tab | `books.vertical` | 22pt |
| Spaces tab | `square.stack.3d.down.right` | 22pt |
| Sidebar / hamburger | `line.3.horizontal` | 18pt |
| Overflow | `ellipsis` | 18pt |
| Settings | `gearshape` | 22pt |
| Back | `chevron.left` | 18pt |
| Show all sources | `chevron.right` | 14pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback`, animated scroll-to-id, and timer-based animations); on iOS 16 fall back to `UIImpactFeedbackGenerator`
- Support Dynamic Type on answer body, all headings, list items, source titles, question titles, related questions — fix citation chips, source domain text, tab labels, code-block language label (layout-sensitive)
- VoiceOver: answer block reads "Answer from Perplexity" then announces body content. Citation chips read as "Citation 1, button — opens source 1: Wikipedia article on Bayesian inference". Source cards read as "Source 1, wikipedia.org, Bayesian Inference Wikipedia article, button"
- Contrast: Text Primary `#FAFAFA` on Canvas `#0A0A0A` exceeds WCAG AAA; Text Secondary `#A1A1A1` meets AA at 14pt+; Text Tertiary `#6E6E6E` meets AA only at 18pt+ — avoid Text Tertiary on small body text
- Perplexity Teal `#20B8CD` on dark canvas reads great; on Perplexity Teal CTAs, use Canvas `#0A0A0A` text (not white) for WCAG AA contrast — this is reversed from typical patterns
- Reduce Motion: skip the searching-dot pulse, the source card cascade-in, the streaming cursor blink, and the focus-ring glow when `accessibilityReduceMotion` is true — preserve haptics
- Light mode: implement via `@Environment(\.colorScheme)` — flip canvas/surface/text tokens; Perplexity Teal darkens to `#1591A3` for WCAG AA on white backgrounds
- The triple-circle Perplexity mark: never tint it any color other than the teal scale (`#20B8CD` / `#3DD6EC` / `#1591A3`)
- Citation chips: provide a `.accessibilityCustomAction("Open source")` so VoiceOver users can navigate to the source without scroll. Inline citation chips should also be reachable via rotor under "Links"
- Sidebar: ensure the "+ New thread" button is the first accessibility element on focus, since it's the primary action of the sidebar
- Streaming text: announce "Generating answer" once on stream start, then "Answer complete" on finalization — avoid re-announcing every token
