# Substack (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Substack's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand & Conversion (the one accent)
    static let subOrange        = Color(red: 1.000, green: 0.404, blue: 0.098) // #FF6719
    static let subOrangePressed = Color(red: 0.898, green: 0.337, blue: 0.055) // #E5560E
    static let subOrangeTint    = Color(red: 1.000, green: 0.969, blue: 0.949) // #FFF7F2
    static let subOrangeTintBorder = Color(red: 1.000, green: 0.851, blue: 0.761) // #FFD9C2
    static let subOrangeTintDark    = Color(red: 0.165, green: 0.110, blue: 0.071) // #2A1C12
    static let subOrangeTintBorderDark = Color(red: 0.361, green: 0.227, blue: 0.141) // #5C3A24
    static let subLink          = Color(red: 0.290, green: 0.435, blue: 0.890) // #4A6FE3

    // MARK: - Reading Surface (Light — "Reader")
    static let subPaper      = Color.white                                     // #FFFFFF
    static let subReadingInk = Color(red: 0.122, green: 0.122, blue: 0.122)    // #1F1F1F
    static let subTitleInk   = Color(red: 0.102, green: 0.102, blue: 0.102)    // #1A1A1A
    static let subSubheadInk = Color(red: 0.341, green: 0.325, blue: 0.302)    // #57534E
    static let subMetaGrey   = Color(red: 0.541, green: 0.541, blue: 0.541)    // #8A8A8A
    static let subHairline   = Color(red: 0.925, green: 0.925, blue: 0.925)    // #ECECEC

    // MARK: - App Surfaces (Light)
    static let subSurfaceSubtle = Color(red: 0.969, green: 0.965, blue: 0.957) // #F7F6F4
    static let subDivider       = Color(red: 0.906, green: 0.898, blue: 0.882) // #E7E5E1

    // MARK: - Dark
    static let subDarkCanvas   = Color(red: 0.071, green: 0.071, blue: 0.071)  // #121212
    static let subDarkSurface1 = Color(red: 0.106, green: 0.106, blue: 0.106)  // #1B1B1B
    static let subDarkSurface2 = Color(red: 0.149, green: 0.149, blue: 0.149)  // #262626
    static let subDarkDivider  = Color(red: 0.180, green: 0.180, blue: 0.180)  // #2E2E2E

    // MARK: - Text
    static let subTextPrimary    = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let subTextSecondary  = Color(red: 0.420, green: 0.420, blue: 0.420) // #6B6B6B
    static let subDarkTextPrimary = Color(red: 0.929, green: 0.929, blue: 0.929) // #EDEDED
    static let subDarkTextSecondary = Color(red: 0.651, green: 0.651, blue: 0.651) // #A6A6A6

    // MARK: - Semantic
    static let subError   = Color(red: 0.851, green: 0.188, blue: 0.145) // #D93025
    static let subSuccess = Color(red: 0.102, green: 0.537, blue: 0.090) // #1A8917
}
```

## 2. Typography

Substack sets everything-you-read in a reading serif (**Source Serif 4**; `Spectral` is an equal alternative) and everything-you-operate in **Inter**. Bundle both via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    // Reading — Source Serif 4
    static let subScreenTitle = Font.custom("SourceSerif4-Bold", size: 30)
    static let subPostTitle   = Font.custom("SourceSerif4-Bold", size: 26)
    static let subSectionHead = Font.custom("SourceSerif4-Bold", size: 22)
    static let subPullQuote   = Font.custom("SourceSerif4-SemiBold", size: 21).italic()
    static func subBody(_ size: CGFloat = 18) -> Font { .custom("SourceSerif4-Regular", size: size) }
    static let subDek         = Font.custom("SourceSerif4-Regular", size: 17).italic()

    // UI — Inter
    static let subButton   = Font.custom("Inter-Bold",     size: 15)
    static let subLabel    = Font.custom("Inter-SemiBold", size: 14)
    static let subMeta     = Font.custom("Inter-Regular",  size: 13)
    static let subCaption  = Font.custom("Inter-Regular",  size: 12)
    static let subEyebrow  = Font.custom("Inter-Bold",     size: 11)
    static let subTab      = Font.custom("Inter-SemiBold", size: 10)
}

// Body needs explicit line spacing for the 1.65 reading rhythm:
// Text(paragraph).font(.subBody(size)).lineSpacing(size * 0.65)
```

## 3. Signature Components

### Post Reader

```swift
struct PostReader: View {
    let pubInitials: String
    let pubName: String
    let subscribers: String        // "312,400 subscribers"
    let title: String
    let dek: String
    let byline: String             // "Mario Gabriele · May 14 · 18 min read"
    let paragraphs: [PostBlock]
    @Environment(\.colorScheme) private var scheme
    var readingSize: CGFloat = 18

    enum PostBlock { case text(String), pull(String) }

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 0) {
                HStack(spacing: 10) {
                    Text(pubInitials).font(.subLabel).foregroundStyle(.white)
                        .frame(width: 34, height: 34)
                        .background(LinearGradient(colors: [.subOrange, .subOrangePressed],
                                                   startPoint: .topLeading, endPoint: .bottomTrailing))
                        .clipShape(RoundedRectangle(cornerRadius: 6))
                    VStack(alignment: .leading, spacing: 1) {
                        Text(pubName).font(.subLabel).foregroundStyle(ink)
                        Text(subscribers).font(.subCaption).foregroundStyle(Color.subMetaGrey)
                    }
                }
                .padding(.bottom, 18)

                Text(title).font(.subPostTitle).tracking(-0.3).foregroundStyle(titleInk)
                    .padding(.bottom, 8)
                Text(dek).font(.subDek).foregroundStyle(Color.subSubheadInk)
                    .padding(.bottom, 14)

                HStack(spacing: 8) {
                    Circle().fill(Color.subHairline).frame(width: 26, height: 26)
                    Text(byline).font(.subMeta).foregroundStyle(Color.subMetaGrey)
                }
                .padding(.bottom, 16)
                Divider().overlay(scheme == .dark ? Color.subDarkDivider : Color.subHairline)
                    .padding(.bottom, 18)

                ForEach(Array(paragraphs.enumerated()), id: \.offset) { _, block in
                    switch block {
                    case .text(let p):
                        Text(p).font(.subBody(readingSize))
                            .foregroundStyle(readingInk)
                            .lineSpacing(readingSize * 0.65)
                            .padding(.bottom, 18)
                    case .pull(let q):
                        Text(q).font(.subPullQuote).foregroundStyle(titleInk)
                            .padding(.leading, 18)
                            .overlay(alignment: .leading) {
                                Rectangle().fill(Color.subOrange).frame(width: 3)
                            }
                            .padding(.vertical, 6).padding(.bottom, 14)
                    }
                }
            }
            .padding(.horizontal, 24)
            .padding(.vertical, 20)
        }
        .background(scheme == .dark ? Color.subDarkCanvas : Color.subPaper)
    }
    private var ink: Color { scheme == .dark ? .subDarkTextPrimary : .subTitleInk }
    private var titleInk: Color { scheme == .dark ? .subDarkTextPrimary : .subTitleInk }
    private var readingInk: Color { scheme == .dark ? .subDarkTextPrimary : .subReadingInk }
}
```

### Subscribe Card / Paywall Break

```swift
struct SubscribeCard: View {
    @Binding var subscribed: Bool
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        VStack(spacing: 0) {
            Text("Keep reading with a subscription")
                .font(.custom("SourceSerif4-Bold", size: 15))
                .foregroundStyle(scheme == .dark ? Color.subDarkTextPrimary : .subTitleInk)
            Text("Get every post, the full archive, and the private community.")
                .font(.subCaption).foregroundStyle(Color.subMetaGrey)
                .multilineTextAlignment(.center)
                .padding(.top, 4).padding(.bottom, 12)

            Button {
                withAnimation(.spring(response: 0.18, dampingFraction: 0.6)) {}
                withAnimation(.easeInOut(duration: 0.22)) { subscribed = true }
                UINotificationFeedbackGenerator().notificationOccurred(.success)
            } label: {
                Text(subscribed ? "Subscribed ✓" : "Subscribe")
                    .font(.subButton).foregroundStyle(.white)
                    .frame(maxWidth: .infinity, minHeight: 42)
                    .background(RoundedRectangle(cornerRadius: 6).fill(Color.subOrange))
            }
            .buttonStyle(.plain)

            Text("$8/month · $80/year · cancel anytime")
                .font(.custom("Inter-Regular", size: 11))
                .foregroundStyle(Color.subMetaGrey)
                .padding(.top, 8)
        }
        .padding(14)
        .background(
            RoundedRectangle(cornerRadius: 10)
                .fill(scheme == .dark ? Color.subOrangeTintDark : .subOrangeTint)
        )
        .overlay(
            RoundedRectangle(cornerRadius: 10)
                .strokeBorder(scheme == .dark ? Color.subOrangeTintBorderDark : .subOrangeTintBorder, lineWidth: 1)
        )
    }
}
```

### Pull-Quote

```swift
struct PullQuote: View {
    let text: String
    var body: some View {
        Text(text)
            .font(.subPullQuote)
            .foregroundStyle(Color.subTitleInk)
            .padding(.leading, 18)
            .overlay(alignment: .leading) { Rectangle().fill(Color.subOrange).frame(width: 3) }
            .padding(.vertical, 6)
    }
}
```

### Inbox Row

```swift
struct InboxRow: View {
    let pubName: String
    let title: String
    let meta: String              // "Mario Gabriele · 18 min · May 14"
    let unread: Bool
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            RoundedRectangle(cornerRadius: 6)
                .fill(LinearGradient(colors: [.subOrange, .subOrangePressed],
                                     startPoint: .topLeading, endPoint: .bottomTrailing))
                .frame(width: 56, height: 56)
            VStack(alignment: .leading, spacing: 3) {
                Text(pubName.uppercased()).font(.subEyebrow).kerning(0.4)
                    .foregroundStyle(Color.subOrange)
                Text(title).font(.custom("SourceSerif4-Bold", size: 15))
                    .foregroundStyle(scheme == .dark ? Color.subDarkTextPrimary : .subTextPrimary)
                    .lineLimit(2)
                Text(meta).font(.subCaption)
                    .foregroundStyle(scheme == .dark ? Color.subDarkTextSecondary : .subTextSecondary)
            }
            Spacer(minLength: 4)
            if unread {
                Circle().fill(Color.subOrange).frame(width: 8, height: 8).padding(.top, 6)
            }
        }
        .padding(.vertical, 14)
        .overlay(alignment: .bottom) {
            Rectangle().fill(scheme == .dark ? Color.subDarkDivider : Color.subDivider).frame(height: 1)
        }
    }
}
```

### Action Bar

```swift
struct PostActionBar: View {
    @Binding var liked: Bool
    @Binding var likeCount: Int

    var body: some View {
        HStack {
            Button {
                liked.toggle(); likeCount += liked ? 1 : -1
                UIImpactFeedbackGenerator(style: .soft).impactOccurred()
            } label: {
                Label("\(likeCount)", systemImage: liked ? "heart.fill" : "heart")
                    .font(.subMeta)
                    .foregroundStyle(liked ? Color.subOrange : .subSubheadInk)
                    .symbolEffect(.bounce, value: liked)
            }
            Spacer()
            Label("184", systemImage: "bubble.left").font(.subMeta).foregroundStyle(Color.subSubheadInk)
            Spacer()
            Label("Restack", systemImage: "arrow.2.squarepath").font(.subMeta).foregroundStyle(Color.subSubheadInk)
            Spacer()
            Image(systemName: "square.and.arrow.up").font(.system(size: 18)).foregroundStyle(Color.subSubheadInk)
        }
        .buttonStyle(.plain)
        .padding(.horizontal, 22).padding(.vertical, 10)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct SubstackTabView: View {
    var body: some View {
        TabView {
            InboxView().tabItem { Label("Inbox", systemImage: "tray.full") }
            DiscoverView().tabItem { Label("Discover", systemImage: "magnifyingglass") }
            NotesView().tabItem { Label("Notes", systemImage: "text.alignleft") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.subOrange)   // the one accent; no tint pill
    }
}
```

## 5. Motion

```swift
// Subscribe success — spring + crossfade to "Subscribed ✓" + success haptic
withAnimation(.spring(response: 0.18, dampingFraction: 0.6)) { /* press scale */ }
withAnimation(.easeInOut(duration: 0.22)) { subscribed = true }
UINotificationFeedbackGenerator().notificationOccurred(.success)

// Like — heart pop + soft haptic
.symbolEffect(.bounce, value: liked)            // 1.0→1.3→1.0
UIImpactFeedbackGenerator(style: .soft).impactOccurred()

// Paywall reveal — blur the locked text, slide the card up
.blur(radius: locked ? 6 : 0).animation(.easeOut(duration: 0.25), value: locked)
// SubscribeCard: .transition(.move(edge: .bottom)) with .easeOut(0.25)

// Read-progress line at the top edge
// GeometryReader-driven width = scrollFraction * screenWidth, color .subOrange, height 2

// Inbox → post: matchedGeometryEffect on the publication thumbnail for a shared-element push
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Inbox (tab) | `tray` / `tray.full` | 22pt |
| Discover (tab) | `magnifyingglass` | 22pt |
| Notes (tab) | `text.alignleft` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Back | `chevron.left` | 19pt |
| Share | `square.and.arrow.up` | 18pt |
| Overflow | `ellipsis` | 19pt |
| Like | `heart` / `heart.fill` | 18pt |
| Comment | `bubble.left` | 18pt |
| Restack | `arrow.2.squarepath` | 18pt |
| Bookmark / Save | `bookmark` / `bookmark.fill` | 18pt |
| Mute publication | `bell.slash` | 18pt |
| Search | `magnifyingglass` | 16pt |
| Unread | `circle.fill` (8pt, orange) | 8pt |
| Audio (listen) | `headphones` | 18pt |
| Subscribed check | `checkmark` | 14pt |

## 7. Dark Mode

```swift
struct SubstackTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.subDarkCanvas : Color.subPaper)
            .foregroundStyle(scheme == .dark ? Color.subDarkTextPrimary : Color.subReadingInk)
            .tint(.subOrange)   // constant in both modes
    }
}
extension View { func substackTheme() -> some View { modifier(SubstackTheme()) } }
```

Dark mode (Reader theme) softens the page to charcoal `#121212` — never pure black — with body ink `#EDEDED`; cards lift to `#1B1B1B`. Substack Orange `#FF6719` is held identical — it is the single conversion signal for Subscribe, the active tab, the unread dot, and the liked heart. The Subscribe card uses the dark orange-tint wash `#2A1C12` with border `#5C3A24` and relies on its border (shadows are invisible on dark). Publication art and avatars stay full-color; only chrome and the page dim.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.symbolEffect(.bounce)` is iOS 17 — fall back to a manual scale animation; `UINotificationFeedbackGenerator` / `UIImpactFeedbackGenerator` shown for haptics)
- Bundle Source Serif 4 (Regular/SemiBold/Bold + Italics) for the reading face and Inter (Regular/SemiBold/Bold) for chrome — both SIL OFL, free to distribute
- The post body must honor the user's in-app reading-size control AND system Dynamic Type as a multiplier — never hard-cap article text size
- VoiceOver: announce the post title + dek + byline as a header group; the Subscribe button as "Subscribe, button — \(price)"; the like as "Like, \(count), button" with state; inbox rows as "\(pubName), \(title), \(meta), unread"
- Publication logos / cover art: `.accessibilityLabel(pubName)`; never leave them unlabeled
- Color contrast: `#1F1F1F` on `#FFFFFF` passes WCAG AA at 18pt; orange `#FF6719` on white is used for large/bold UI and the Subscribe button which has white text on orange (passes AA at 15pt bold) — pair the unread dot with row text so meaning never rests on color alone
- Reduce Motion: disable the heart bounce, the Subscribe crossfade spring, and the paywall blur transition — keep the state changes (use instant fill / crossfade)
- Reduce Transparency: make the sticky Subscribe bar and tab bar opaque instead of translucent
- The read-progress line is ambient; expose post progress via VoiceOver as a percent rather than relying on the line
- Comments/Notes are set in Inter (sans) deliberately — do not "fix" them to serif; they are conversation, not the article
