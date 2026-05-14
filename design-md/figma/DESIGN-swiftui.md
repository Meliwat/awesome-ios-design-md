# Figma (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Figma's precise, productivity-tool aesthetic into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, file cards, cube avatars, comment pins, and haptics.

Targets iOS 17+. Modern `.sensoryFeedback`, `matchedGeometryEffect` on the file-card → detail transition all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - The Five Figma Cubes (brand accents only — never primary CTA)
    static let figmaRed     = Color(red: 0.949, green: 0.306, blue: 0.118) // #F24E1E
    static let figmaOrange  = Color(red: 1.000, green: 0.447, blue: 0.384) // #FF7262
    static let figmaPurple  = Color(red: 0.635, green: 0.349, blue: 1.000) // #A259FF
    static let figmaBlueCube= Color(red: 0.102, green: 0.737, blue: 0.996) // #1ABCFE
    static let figmaGreen   = Color(red: 0.039, green: 0.812, blue: 0.514) // #0ACF83

    /// The 5-element cube palette as an array for random assignment.
    static let figmaCubes: [Color] = [.figmaRed, .figmaOrange, .figmaPurple, .figmaBlueCube, .figmaGreen]

    // MARK: - UI Action Colors
    static let figmaActionBlue        = Color(red: 0.051, green: 0.600, blue: 1.000) // #0D99FF
    static let figmaActionBluePressed = Color(red: 0.027, green: 0.412, blue: 0.710) // #0769B5
    static let figmaBlurple           = Color(red: 0.333, green: 0.318, blue: 1.000) // #5551FF
    static let figmaSelectionBlue     = Color(red: 0.369, green: 0.714, blue: 0.988) // #5EB6FC

    // MARK: - Macaron
    static let figmaMacaronPink       = Color(red: 1.000, green: 0.784, blue: 0.784) // #FFC8C8
    static let figmaMacaronPinkBright = Color(red: 1.000, green: 0.651, blue: 0.651) // #FFA6A6

    // MARK: - Light surfaces
    static let figmaCanvas        = Color.white                                     // #FFFFFF
    static let figmaSurfaceLight1 = Color(red: 0.961, green: 0.961, blue: 0.961)    // #F5F5F5
    static let figmaSurfaceLight2 = Color(red: 0.898, green: 0.898, blue: 0.898)    // #E5E5E5
    static let figmaDividerLight  = Color(red: 0.898, green: 0.898, blue: 0.898)    // #E5E5E5
    static let figmaHairlineLight = Color(red: 0.933, green: 0.933, blue: 0.933)    // #EEEEEE

    // MARK: - Dark surfaces (Editor's exact tokens)
    static let figmaDarkCanvas    = Color(red: 0.118, green: 0.118, blue: 0.118)    // #1E1E1E
    static let figmaDarkSurface1  = Color(red: 0.173, green: 0.173, blue: 0.173)    // #2C2C2C
    static let figmaDarkSurface2  = Color(red: 0.220, green: 0.220, blue: 0.220)    // #383838
    static let figmaDarkSurface3  = Color(red: 0.267, green: 0.267, blue: 0.267)    // #444444
    static let figmaDarkDivider   = Color(red: 0.173, green: 0.173, blue: 0.173)    // #2C2C2C

    // MARK: - Text
    static let figmaInk            = Color(red: 0.118, green: 0.118, blue: 0.118)   // #1E1E1E
    static let figmaTextSecondary  = Color(red: 0.459, green: 0.459, blue: 0.459)   // #757575
    static let figmaTextTertiary   = Color(red: 0.702, green: 0.702, blue: 0.702)   // #B3B3B3
    static let figmaTextDarkSecondary = Color(red: 0.702, green: 0.702, blue: 0.702) // #B3B3B3

    // MARK: - Semantic
    static let figmaSuccess  = Color(red: 0.078, green: 0.682, blue: 0.361)         // #14AE5C
    static let figmaWarning  = Color(red: 0.949, green: 0.675, blue: 0.165)         // #F2AC2A
    static let figmaError    = Color(red: 0.878, green: 0.243, blue: 0.102)         // #E03E1A
}

// MARK: - Cube assignment helper

extension Color {
    /// Deterministic cube color from a user ID — always returns the same color for the same user.
    static func figmaCube(for userId: String) -> Color {
        let hash = abs(userId.hashValue)
        return figmaCubes[hash % figmaCubes.count]
    }
}
```

## 2. Typography

Inter is the open-source face Rasmus Andersson originally drew for Figma's marketing. Bundle via `Info.plist` (`UIAppFonts`). Fall back to system if not available.

```swift
extension Font {
    // Titles
    static let figmaHero       = Font.custom("Inter-Bold",     size: 28)
    static let figmaPageSec    = Font.custom("Inter-Bold",     size: 17)
    static let figmaSubhead    = Font.custom("Inter-SemiBold", size: 15)

    // File / project labels
    static let figmaFileName   = Font.custom("Inter-Medium",   size: 14)

    // Body & comments
    static let figmaBody       = Font.custom("Inter-Regular",  size: 14)
    static let figmaCommentBody = Font.custom("Inter-Regular", size: 13)
    static let figmaCommentAuthor = Font.custom("Inter-SemiBold", size: 13)

    // Metadata
    static let figmaMetadata   = Font.custom("Inter-Regular",  size: 12)
    static let figmaCaption    = Font.custom("Inter-Medium",   size: 11)

    // Mono — code / dimensions / hex
    static let figmaMono       = Font.custom("SFMono-Medium",  size: 12).monospacedDigit()

    // Buttons & tabs
    static let figmaButton     = Font.custom("Inter-SemiBold", size: 14)
    static let figmaTab        = Font.custom("Inter-Medium",   size: 11)

    // Avatar initials
    static let figmaAvatar24   = Font.custom("Inter-SemiBold", size: 14)
    static let figmaAvatar32   = Font.custom("Inter-SemiBold", size: 16)
    static let figmaAvatar40   = Font.custom("Inter-SemiBold", size: 18)
}

// Fallback when Inter isn't bundled
extension Font {
    static func figma(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default).monospacedDigit()
    }
}
```

## 3. Signature Components

### Cube Avatar

```swift
struct CubeAvatar: View {
    let userId: String
    let initial: String
    let size: CGFloat   // 24, 32, or 40

    var avatarFont: Font {
        switch size {
        case 24:  return .figmaAvatar24
        case 40:  return .figmaAvatar40
        default:  return .figmaAvatar32
        }
    }

    var body: some View {
        Circle()
            .fill(Color.figmaCube(for: userId))
            .frame(width: size, height: size)
            .overlay(
                Text(initial.prefix(1).uppercased())
                    .font(avatarFont)
                    .foregroundStyle(.white)
            )
    }
}
```

### Avatar Stack (Collaborators)

```swift
struct AvatarStack: View {
    struct Collaborator: Identifiable {
        let id: String
        let initial: String
    }

    let collaborators: [Collaborator]
    var maxVisible: Int = 3
    var size: CGFloat = 24

    var body: some View {
        HStack(spacing: -8) {
            ForEach(collaborators.prefix(maxVisible)) { c in
                CubeAvatar(userId: c.id, initial: c.initial, size: size)
                    .overlay(Circle().strokeBorder(Color.figmaCanvas, lineWidth: 2))
            }
            if collaborators.count > maxVisible {
                Text("+\(collaborators.count - maxVisible)")
                    .font(.figmaCaption)
                    .foregroundStyle(Color.figmaInk)
                    .frame(width: size, height: size)
                    .background(
                        Circle()
                            .fill(Color.figmaSurfaceLight1)
                            .overlay(Circle().strokeBorder(Color.figmaCanvas, lineWidth: 2))
                    )
            }
        }
    }
}
```

### File Card (16:10 thumbnail + name + metadata)

```swift
struct FileCard: View {
    let id: String
    let name: String
    let metadata: String
    let thumbnailImage: Image?
    let collaborators: [AvatarStack.Collaborator]

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            // 16:10 thumbnail
            ZStack {
                RoundedRectangle(cornerRadius: 6)
                    .fill(Color.figmaSurfaceLight1)
                if let img = thumbnailImage {
                    img
                        .resizable()
                        .aspectRatio(16/10, contentMode: .fill)
                        .clipShape(RoundedRectangle(cornerRadius: 6))
                } else {
                    // No-preview cover: random cube color with file's first letter
                    RoundedRectangle(cornerRadius: 6)
                        .fill(Color.figmaCube(for: id))
                        .overlay(
                            Text(name.prefix(1).uppercased())
                                .font(.custom("Inter-Bold", size: 32))
                                .foregroundStyle(.white)
                        )
                }
            }
            .aspectRatio(16/10, contentMode: .fit)
            .overlay(
                RoundedRectangle(cornerRadius: 6).strokeBorder(Color.figmaDividerLight, lineWidth: 1)
            )

            VStack(alignment: .leading, spacing: 2) {
                Text(name)
                    .font(.figmaFileName)
                    .foregroundStyle(Color.figmaInk)
                    .lineLimit(1)
                HStack(spacing: 8) {
                    Text(metadata)
                        .font(.figmaMetadata)
                        .foregroundStyle(Color.figmaTextSecondary)
                        .lineLimit(1)
                    Spacer()
                    if !collaborators.isEmpty {
                        AvatarStack(collaborators: collaborators, maxVisible: 3, size: 20)
                    }
                }
            }
        }
        .sensoryFeedback(.impact(weight: .light), trigger: UUID())
    }
}
```

### Primary CTA

```swift
struct FigmaPrimaryButton: View {
    let label: String
    var action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.figmaButton)
                .foregroundStyle(.white)
                .padding(.vertical, 10)
                .padding(.horizontal, 16)
                .frame(minHeight: 36)
                .background(
                    RoundedRectangle(cornerRadius: 6)
                        .fill(pressed ? Color.figmaActionBluePressed : Color.figmaActionBlue)
                )
                .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .onLongPressGesture(minimumDuration: 0, perform: {}, onPressingChanged: { pressed = $0 })
        .sensoryFeedback(.impact(weight: .light), trigger: pressed)
    }
}
```

### Comment Pin (on a frame in mirror view)

```swift
struct CommentPin: View {
    let userId: String
    let number: Int
    @Environment(\.colorScheme) private var colorScheme
    @State private var pulse = false

    var body: some View {
        Button {
            withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) {
                pulse.toggle()
            }
        } label: {
            Text("\(number)")
                .font(.custom("Inter-Bold", size: 13))
                .foregroundStyle(.white)
                .frame(width: 28, height: 28)
                .background(
                    Circle()
                        .fill(Color.figmaCube(for: userId))
                        .overlay(
                            Circle().strokeBorder(
                                colorScheme == .dark ? Color.figmaDarkCanvas : Color.figmaCanvas,
                                lineWidth: 2
                            )
                        )
                        .shadow(color: .black.opacity(0.16), radius: 4, y: 2)
                )
                .scaleEffect(pulse ? 1.15 : 1)
        }
    }
}
```

### Comment Thread Sheet

```swift
struct CommentThreadSheet: View {
    struct Reply: Identifiable {
        let id = UUID()
        let userId: String
        let initial: String
        let author: String
        let body: String
        let timestamp: String
    }

    let firstComment: Reply
    let replies: [Reply]
    @State private var draft = ""

    var body: some View {
        VStack(alignment: .leading, spacing: 16) {
            // Original comment
            CommentRow(reply: firstComment, isOriginal: true)

            if !replies.isEmpty {
                VStack(alignment: .leading, spacing: 12) {
                    ForEach(replies) { reply in
                        CommentRow(reply: reply, isOriginal: false)
                            .padding(.leading, 12)
                    }
                }
                .overlay(alignment: .leading) {
                    Rectangle().fill(Color.figmaDividerLight).frame(width: 1)
                }
            }

            CommentComposer(text: $draft)
        }
        .padding(20)
        .background(Color.figmaCanvas)
    }
}

struct CommentRow: View {
    let reply: CommentThreadSheet.Reply
    let isOriginal: Bool
    var body: some View {
        HStack(alignment: .top, spacing: 8) {
            CubeAvatar(userId: reply.userId, initial: reply.initial, size: 32)
            VStack(alignment: .leading, spacing: 4) {
                HStack(spacing: 8) {
                    Text(reply.author).font(.figmaCommentAuthor).foregroundStyle(Color.figmaInk)
                    Text(reply.timestamp).font(.figmaMetadata).foregroundStyle(Color.figmaTextSecondary)
                }
                Text(reply.body).font(.figmaCommentBody).foregroundStyle(Color.figmaInk)
            }
        }
    }
}
```

### Comment Composer

```swift
struct CommentComposer: View {
    @Binding var text: String

    var body: some View {
        HStack(spacing: 8) {
            Image(systemName: "at")
                .font(.system(size: 14))
                .foregroundStyle(Color.figmaTextSecondary)

            TextField("Reply", text: $text, axis: .vertical)
                .font(.figmaCommentBody)
                .lineLimit(1...4)

            if !text.isEmpty {
                Button {
                    // send
                } label: {
                    Image(systemName: "paperplane.fill")
                        .font(.system(size: 14))
                        .foregroundStyle(.white)
                        .frame(width: 28, height: 28)
                        .background(Circle().fill(Color.figmaActionBlue))
                }
                .sensoryFeedback(.success, trigger: text.isEmpty)
            }
        }
        .padding(.horizontal, 12)
        .padding(.vertical, 8)
        .frame(minHeight: 36)
        .background(
            RoundedRectangle(cornerRadius: 18)
                .fill(Color.figmaSurfaceLight1)
        )
    }
}
```

### Project Row

```swift
struct ProjectRow: View {
    let id: String
    let name: String
    let fileCount: Int
    let initial: String

    var body: some View {
        HStack(spacing: 12) {
            RoundedRectangle(cornerRadius: 6)
                .fill(Color.figmaCube(for: id))
                .frame(width: 32, height: 32)
                .overlay(
                    Text(initial)
                        .font(.figmaCaption)
                        .foregroundStyle(.white)
                )
            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.figmaSubhead).foregroundStyle(Color.figmaInk)
                Text("\(fileCount) files").font(.figmaMetadata).foregroundStyle(Color.figmaTextSecondary)
            }
            Spacer()
            Image(systemName: "chevron.right").font(.system(size: 12)).foregroundStyle(Color.figmaTextSecondary)
        }
        .padding(.horizontal, 16)
        .frame(height: 56)
    }
}
```

### Plan Badge

```swift
struct PlanBadge: View {
    enum Tier { case free, pro, org }
    let tier: Tier

    var bg: Color {
        switch tier {
        case .free: return Color.figmaMacaronPink
        case .pro:  return Color.figmaBlurple
        case .org:  return Color.figmaActionBlue
        }
    }
    var fg: Color {
        tier == .free ? Color.figmaInk : .white
    }
    var label: String {
        switch tier {
        case .free: return "FREE"
        case .pro:  return "PRO"
        case .org:  return "ORG"
        }
    }

    var body: some View {
        Text(label)
            .font(.custom("Inter-SemiBold", size: 10))
            .foregroundStyle(fg)
            .padding(.vertical, 2)
            .padding(.horizontal, 6)
            .background(RoundedRectangle(cornerRadius: 4).fill(bg))
    }
}
```

## 4. Tab Bar

```swift
struct RootTabView: View {
    @Environment(\.colorScheme) private var colorScheme

    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.shadowColor = UIColor(Color.figmaDividerLight)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            RecentsView()       .tabItem { Label("Recents",       systemImage: "clock") }
            DraftsView()        .tabItem { Label("Drafts",        systemImage: "doc") }
            NotificationsView() .tabItem { Label("Notifications", systemImage: "bell") }
            ProfileView()       .tabItem { Label("Profile",       systemImage: "person") }
        }
        .tint(colorScheme == .dark ? Color.figmaCanvas : Color.figmaInk)
    }
}
```

## 5. Motion & Haptics

```swift
// File card tap
.sensoryFeedback(.impact(weight: .light), trigger: tappedFile)

// Comment pin tap — scale + sheet rise
.sensoryFeedback(.impact(weight: .light), trigger: pinTapped)

// Send reply
.sensoryFeedback(.success, trigger: replySent)

// Tab switch
.sensoryFeedback(.selection, trigger: selectedTab)

// File card → detail shared element
.matchedGeometryEffect(id: file.id, in: fileNS)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Recents tab | `clock` / `clock.fill` | 22pt |
| Drafts tab | `doc` / `doc.fill` | 22pt |
| Notifications tab | `bell` / `bell.fill` | 22pt |
| Profile tab | `person` / `person.fill` | 22pt |
| Comment | `bubble.left` | 18pt |
| Mirror | `iphone` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Mention | `at` | 14pt |
| Send | `paperplane.fill` | 14pt |
| Search | `magnifyingglass` | 14pt |
| Row chevron | `chevron.right` | 12pt |
| Plus | `plus` | 14pt |
| Filter | `line.3.horizontal.decrease` | 18pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback` and `.matchedGeometryEffect` on the file-card → detail transition). On iOS 16, fall back to `UIImpactFeedbackGenerator`.
- Support Dynamic Type on body, comment bodies, metadata, large nav title. **Fix** tab labels, avatar initials, hex codes, dimension labels.
- VoiceOver: read a file card as `"Onboarding flows, edited 2 hours ago by Sarah Chen, 4 collaborators, opens file"`; a comment pin as `"Comment 1 by Sarah Chen, double-tap to open thread"`; the avatar stack as `"4 collaborators: Sarah, Jordan, Alex, and 1 more"`.
- Contrast: Ink `#1E1E1E` on white meets AAA. Text Secondary `#757575` meets AA only at 14pt+ — avoid using it for tiny labels.
- Dark mode is first-class: implement via `@Environment(\.colorScheme)`. The canvas `#1E1E1E` matches the Figma Editor — this is intentional.
- Cube color persistence: derive cube color from a stable user ID (hash modulo 5). Store in user metadata so it never changes session-to-session.
- Reduce Motion: skip the comment-pin scale, the avatar slide-in, and the cursor trailing lag when `accessibilityReduceMotion` is true.
