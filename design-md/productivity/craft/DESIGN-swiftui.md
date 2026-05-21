# Craft (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Craft's visual language into paste-ready SwiftUI code: `Color` extensions, the brand `LinearGradient`, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let craftCanvas        = Color(red: 0.988, green: 0.988, blue: 0.992) // #FCFCFD
    static let craftSurfaceGray   = Color(red: 0.957, green: 0.957, blue: 0.965) // #F4F4F6
    static let craftCard          = Color.white                                   // #FFFFFF
    static let craftSurfacePressed = Color(red: 0.925, green: 0.925, blue: 0.937) // #ECECEF
    static let craftDivider       = Color(red: 0.902, green: 0.902, blue: 0.918) // #E6E6EA

    // MARK: - Canvas & Surfaces (Dark)
    static let craftDarkCanvas    = Color(red: 0.102, green: 0.102, blue: 0.118) // #1A1A1E
    static let craftDarkCard      = Color(red: 0.137, green: 0.137, blue: 0.157) // #232328
    static let craftDarkSurface2  = Color(red: 0.173, green: 0.173, blue: 0.196) // #2C2C32
    static let craftDarkDivider   = Color(red: 0.204, green: 0.204, blue: 0.235) // #34343C

    // MARK: - Text
    static let craftTextPrimary   = Color(red: 0.110, green: 0.110, blue: 0.133) // #1C1C22
    static let craftTextSecondary = Color(red: 0.416, green: 0.416, blue: 0.471) // #6A6A78
    static let craftTextTertiary  = Color(red: 0.608, green: 0.608, blue: 0.651) // #9B9BA6
    static let craftDarkTextPrimary = Color(red: 0.925, green: 0.925, blue: 0.937) // #ECECEF
    static let craftDarkTextTertiary = Color(red: 0.404, green: 0.404, blue: 0.478) // #67677A

    // MARK: - Brand
    static let craftBlue          = Color(red: 0.184, green: 0.357, blue: 0.918) // #2F5BEA
    static let craftBluePressed   = Color(red: 0.137, green: 0.278, blue: 0.788) // #2347C9
    static let craftPurple        = Color(red: 0.431, green: 0.337, blue: 0.812) // #6E56CF
    static let craftPurpleSoft    = Color(red: 0.545, green: 0.451, blue: 0.910) // #8B73E8

    // MARK: - Accents / Semantic
    static let craftGreen         = Color(red: 0.188, green: 0.643, blue: 0.424) // #30A46C
    static let craftAmber         = Color(red: 0.941, green: 0.663, blue: 0.169) // #F0A92B
    static let craftRed           = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
    static let craftPink          = Color(red: 0.839, green: 0.251, blue: 0.624) // #D6409F
    static let craftTeal          = Color(red: 0.071, green: 0.647, blue: 0.580) // #12A594
}

extension LinearGradient {
    /// The constant Craft brand identity — every primary action.
    static let craftBrand = LinearGradient(
        colors: [.craftBlue, .craftPurple],
        startPoint: .topLeading,
        endPoint: .bottomTrailing
    )
}
```

## 2. Typography

Craft uses Inter (default sans), Lora (optional serif reading mode), JetBrains Mono (code only). Bundle TTFs via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static let craftDocTitle  = Font.custom("Inter-ExtraBold", size: 28).weight(.heavy)
    static let craftH1         = Font.custom("Inter-Bold",     size: 24).weight(.bold)
    static let craftH2         = Font.custom("Inter-Bold",     size: 20).weight(.bold)
    static let craftH3         = Font.custom("Inter-SemiBold", size: 17).weight(.semibold)
    static let craftBody       = Font.custom("Inter-Regular",  size: 16).weight(.regular)
    static let craftBodyBold   = Font.custom("Inter-SemiBold", size: 16).weight(.semibold)
    static let craftCardTitle  = Font.custom("Inter-SemiBold", size: 15).weight(.semibold)
    static let craftCardSub    = Font.custom("Inter-Regular",  size: 12).weight(.regular)
    static let craftMeta       = Font.custom("Inter-Regular",  size: 13).weight(.regular)
    static let craftCaption    = Font.custom("Inter-Medium",   size: 12).weight(.medium)
    static let craftButton     = Font.custom("Inter-SemiBold", size: 15).weight(.semibold)
    static let craftLink       = Font.custom("Inter-Medium",   size: 16).weight(.medium)
    static let craftTab         = Font.custom("Inter-Medium",   size: 10).weight(.medium)
    static let craftSidebar     = Font.custom("Inter-Medium",   size: 14).weight(.medium)
    static let craftQuote       = Font.custom("Lora-Italic",    size: 17)
    static let craftCodeInline  = Font.custom("JetBrainsMono-Regular", size: 14)
    static let craftCodeBlock   = Font.custom("JetBrainsMono-Regular", size: 13)
}
```

## 3. Signature Components

### Document Header (Cover + Emoji + Title)

```swift
struct CraftDocHeader: View {
    let coverUrl: String?
    let emoji: String
    @Binding var title: String
    let subtitle: String

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            if let coverUrl, let url = URL(string: coverUrl) {
                AsyncImage(url: url) { $0.resizable().aspectRatio(contentMode: .fill) }
                    placeholder: { LinearGradient.craftBrand }
                    .frame(height: 96)
                    .clipShape(RoundedRectangle(cornerRadius: 14))
                    .padding(.horizontal, 22)
            }
            Text(emoji)
                .font(.system(size: 40))
                .padding(.leading, 26)
                .offset(y: coverUrl != nil ? -38 : 0)
                .shadow(color: .black.opacity(0.4), radius: 6, y: 3)

            TextField("Untitled", text: $title)
                .font(.craftDocTitle)
                .foregroundStyle(Color.craftTextPrimary)
                .padding(.horizontal, 22)
                .padding(.top, 6)

            Text(subtitle)
                .font(.craftMeta)
                .foregroundStyle(Color.craftTextSecondary)
                .padding(.horizontal, 22)
                .padding(.top, 4)
                .padding(.bottom, 18)
        }
    }
}
```

### Card Block (Page / Link / nested document) — *signature*

```swift
struct CraftCardBlock: View {
    let emoji: String
    let title: String
    let subtitle: String
    var tint: Color = .craftBlue
    let onOpen: () -> Void

    var body: some View {
        Button(action: onOpen) {
            HStack(spacing: 12) {
                Text(emoji)
                    .font(.system(size: 17))
                    .frame(width: 36, height: 36)
                    .background(RoundedRectangle(cornerRadius: 9).fill(tint.opacity(0.12)))

                VStack(alignment: .leading, spacing: 1) {
                    Text(title).font(.craftCardTitle).foregroundStyle(Color.craftTextPrimary)
                    Text(subtitle).font(.craftCardSub).foregroundStyle(Color.craftTextSecondary)
                }
                Spacer()
                Image(systemName: "chevron.right")
                    .font(.system(size: 14, weight: .semibold))
                    .foregroundStyle(Color.craftTextTertiary)
            }
            .padding(14)
            .background(RoundedRectangle(cornerRadius: 12).fill(Color.craftCard))
            .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.craftDivider, lineWidth: 1))
            .shadow(color: .black.opacity(0.10), radius: 9, x: 0, y: 6)
        }
        .buttonStyle(.plain)
    }
}
```

### To-do Block

```swift
struct CraftTodoBlock: View {
    @State private var done: Bool
    let text: String
    init(done: Bool = false, text: String) { _done = State(initialValue: done); self.text = text }

    var body: some View {
        HStack(alignment: .top, spacing: 10) {
            Button {
                withAnimation(.spring(response: 0.22, dampingFraction: 0.6)) { done.toggle() }
            } label: {
                ZStack {
                    RoundedRectangle(cornerRadius: 6)
                        .fill(done ? Color.craftBlue : Color.clear)
                    RoundedRectangle(cornerRadius: 6)
                        .strokeBorder(done ? Color.craftBlue : Color.craftTextTertiary, lineWidth: 1.8)
                    if done {
                        Image(systemName: "checkmark")
                            .font(.system(size: 11, weight: .bold))
                            .foregroundStyle(.white)
                    }
                }
                .frame(width: 18, height: 18)
            }
            .buttonStyle(.plain)
            .sensoryFeedback(.impact(flexibility: .soft), trigger: done)

            Text(text)
                .font(.craftBody)
                .strikethrough(done)
                .foregroundStyle(done ? Color.craftTextTertiary : Color.craftTextPrimary)
            Spacer()
        }
        .padding(.vertical, 3)
    }
}
```

### Primary Button (Gradient)

```swift
struct CraftPrimaryButton: View {
    let title: String
    var systemImage: String? = nil
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                if let systemImage { Image(systemName: systemImage).font(.system(size: 15, weight: .semibold)) }
                Text(title).font(.craftButton)
            }
            .foregroundStyle(.white)
            .padding(.vertical, 13)
            .padding(.horizontal, 26)
            .background(LinearGradient.craftBrand)
            .clipShape(RoundedRectangle(cornerRadius: 12))
        }
        .buttonStyle(CraftPressStyle())
    }
}

struct CraftPressStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .brightness(configuration.isPressed ? -0.06 : 0)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Toggle Block

```swift
struct CraftToggleBlock<Content: View>: View {
    let title: String
    @ViewBuilder let content: () -> Content
    @State private var open = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Button {
                withAnimation(.spring(response: 0.32, dampingFraction: 0.85)) { open.toggle() }
            } label: {
                HStack(spacing: 6) {
                    Image(systemName: "chevron.right")
                        .font(.system(size: 14))
                        .foregroundStyle(Color.craftTextTertiary)
                        .rotationEffect(.degrees(open ? 90 : 0))
                    Text(title).font(.craftBody.weight(.medium)).foregroundStyle(Color.craftTextPrimary)
                    Spacer()
                }
                .padding(.vertical, 6)
            }
            .buttonStyle(.plain)
            .sensoryFeedback(.impact(flexibility: .soft), trigger: open)

            if open {
                content()
                    .padding(.leading, 20)
                    .transition(.opacity.combined(with: .move(edge: .top)))
            }
        }
    }
}
```

### Callout Block

```swift
struct CraftCalloutBlock: View {
    let emoji: String
    let text: String
    var tint: Color = .craftBlue

    var body: some View {
        HStack(alignment: .top, spacing: 10) {
            Text(emoji).font(.system(size: 20))
            Text(text).font(.craftBody).foregroundStyle(Color.craftTextPrimary)
            Spacer(minLength: 0)
        }
        .padding(14)
        .background(RoundedRectangle(cornerRadius: 12).fill(tint.opacity(0.08)))
    }
}
```

### `/` Block Inserter

```swift
struct CraftSlashInserter: View {
    struct Block: Identifiable { let id = UUID(); let icon: String; let title: String; let subtitle: String }
    let blocks: [Block]
    @State private var selected = 0
    let onPick: (Block) -> Void

    var body: some View {
        VStack(spacing: 0) {
            ForEach(Array(blocks.enumerated()), id: \.offset) { i, b in
                HStack(spacing: 12) {
                    Text(b.icon)
                        .font(.system(size: 14))
                        .frame(width: 28, height: 28)
                        .background(RoundedRectangle(cornerRadius: 7).fill(Color.craftSurfaceGray))
                    VStack(alignment: .leading, spacing: 1) {
                        Text(b.title).font(.craftCaption.weight(.semibold)).foregroundStyle(Color.craftTextPrimary)
                        Text(b.subtitle).font(.system(size: 11)).foregroundStyle(Color.craftTextSecondary)
                    }
                    Spacer()
                }
                .padding(.horizontal, 14)
                .frame(height: 44)
                .background(i == selected ? Color.craftBlue.opacity(0.10) : .clear)
                .contentShape(Rectangle())
                .onTapGesture { selected = i; onPick(b) }
            }
        }
        .padding(.vertical, 6)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.craftCard)
            .shadow(color: .black.opacity(0.14), radius: 16, y: 8))
        .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.craftDivider, lineWidth: 1))
        .frame(width: 300)
        .transition(.opacity.combined(with: .move(edge: .bottom)))
    }
}
```

## 4. Bottom Tab Bar

Craft's bar is a split layout with a center gradient FAB — model it as a custom bar (not a stock `TabView`) for the FAB.

```swift
struct CraftBottomBar: View {
    @Binding var selection: Int
    let onAdd: () -> Void

    var body: some View {
        HStack {
            barIcon("house", 0); Spacer()
            barIcon("magnifyingglass", 1); Spacer()

            Button(action: onAdd) {
                Image(systemName: "plus")
                    .font(.system(size: 22, weight: .semibold))
                    .foregroundStyle(.white)
                    .frame(width: 46, height: 46)
                    .background(LinearGradient.craftBrand)
                    .clipShape(RoundedRectangle(cornerRadius: 14))
                    .shadow(color: Color.craftBlue.opacity(0.6), radius: 10, y: 8)
            }
            .buttonStyle(CraftPressStyle())

            Spacer(); barIcon("doc.text", 2)
            Spacer(); barIcon("person.crop.circle", 3)
        }
        .padding(.horizontal, 22).padding(.top, 10)
        .background(.bar)
        .overlay(Divider(), alignment: .top)
    }

    @ViewBuilder private func barIcon(_ name: String, _ idx: Int) -> some View {
        Button { selection = idx } label: {
            Image(systemName: name)
                .font(.system(size: 21, weight: .regular))
                .foregroundStyle(selection == idx ? Color.craftBlue : Color.craftTextSecondary)
        }
    }
}
```

## 5. Motion

```swift
// Card open — push with parallax (use NavigationStack default push, 320ms)
// To-do complete — spring scale-pop
withAnimation(.spring(response: 0.22, dampingFraction: 0.6)) { done.toggle() }

// Toggle expand/collapse
withAnimation(.spring(response: 0.32, dampingFraction: 0.85)) { open.toggle() }
// chevron: .rotationEffect(.degrees(open ? 90 : 0))

// / inserter
.transition(.opacity.combined(with: .move(edge: .bottom)))   // 180ms in / 150ms out

// Sheet present (Style / Share / New)
.presentationDetents([.medium, .large])
// spring(response: 0.40, dampingFraction: 0.82)

// FAB / button press
CraftPressStyle()  // scale 0.98 + brightness -0.06

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: todoDone)
.sensoryFeedback(.impact(flexibility: .soft), trigger: toggleOpen)
.sensoryFeedback(.selection, trigger: slashOpened)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 21pt |
| Search (tab) | `magnifyingglass` | 21pt |
| Documents (tab) | `doc.text` / `doc.text.fill` | 21pt |
| Profile (tab) | `person.crop.circle` | 21pt |
| Add (FAB) | `plus` | 22pt |
| Back | `chevron.left` | 18pt |
| Page actions | `ellipsis` | 18pt |
| Card chevron | `chevron.right` | 14pt |
| To-do unchecked | `square` (custom rounded) | 18pt |
| To-do checked | `checkmark` (in fill) | 11pt |
| Toggle | `chevron.right` (rotate 90°) | 14pt |
| Copy code | `doc.on.doc` | 14pt |
| Share | `square.and.arrow.up` | 18pt |
| Link to page | `link` | 16pt |
| Daily Note | `calendar` | 17pt |
| Page block | `doc` | 17pt |
| Image block | `photo` | 17pt |
| Style sheet | `paintbrush` | 18pt |

## 7. Dark Mode

```swift
struct CraftTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.craftDarkCanvas : Color.craftCanvas)
            .foregroundStyle(scheme == .dark ? Color.craftDarkTextPrimary : Color.craftTextPrimary)
    }
}
extension View { func craftTheme() -> some View { modifier(CraftTheme()) } }
```

Dark canvas is `#1A1A1E` (NOT pure black); cards lift to `#232328`. The brand gradient is identical across themes. On dark, deepen card shadows to ~0.6 opacity and add a 1pt `craftDarkDivider` border to floating panels (`/` inserter, block menu) as the elevation cue.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`); `.presentationDetents` requires iOS 16
- Bundle Inter, Lora, JetBrains Mono TTFs via `Info.plist` `UIAppFonts` — all SIL OFL licensed for free distribution
- Dynamic Type: support on doc title, headings, body, quote, captions; keep card subtitles, tab labels, slash-menu rows, sidebar labels at FIXED sizes
- VoiceOver: label card blocks "Page: {title}, {subtitle}"; to-do as "To-do: {text}, checked/unchecked"; toggle as "Toggle: {title}, expanded/collapsed"; expose block actions via `.accessibilityAction(named:)` so the long-press menu is reachable
- Color contrast: `#1C1C22` on `#FCFCFD` passes WCAG AA for body at 16pt; the gradient-on-white button text (`#FFFFFF`) passes AA; verify amber callouts with a contrast checker
- Reduce Motion: replace spring expand/scale with crossfade; keep selection highlight (conveys state)
- Dark mode: invert per `craftDark*`; gradient unchanged; shadows deepen + 1pt divider border on floating panels
- The FAB is a 46pt rounded square (≥44pt target); give 18pt to-do checkbox and 14pt toggle chevron a 44pt hit area
