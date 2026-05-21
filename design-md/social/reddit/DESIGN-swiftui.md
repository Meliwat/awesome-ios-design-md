# Reddit (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Reddit's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let rdBrandRed        = Color(red: 1.000, green: 0.271, blue: 0.000) // #FF4500
    static let rdAlertRed        = Color(red: 1.000, green: 0.345, blue: 0.357) // #FF585B
    static let rdBrandRedPressed = Color(red: 0.800, green: 0.216, blue: 0.000) // #CC3700

    // MARK: - Vote Semantic Pair
    static let rdUpvote      = Color(red: 1.000, green: 0.529, blue: 0.090) // #FF8717
    static let rdUpvoteDark  = Color(red: 1.000, green: 0.667, blue: 0.400) // #FFAA66
    static let rdDownvote    = Color(red: 0.443, green: 0.576, blue: 1.000) // #7193FF
    static let rdDownvoteDark = Color(red: 0.580, green: 0.580, blue: 1.000) // #9494FF
    static let rdVoteInactive     = Color(red: 0.529, green: 0.541, blue: 0.549) // #878A8C
    static let rdVoteInactiveDark = Color(red: 0.506, green: 0.514, blue: 0.518) // #818384

    // MARK: - Canvas & Surface (Light)
    static let rdCanvasLight        = Color(red: 0.965, green: 0.969, blue: 0.973) // #F6F7F8
    static let rdCanvasClassicLight = Color(red: 0.855, green: 0.878, blue: 0.902) // #DAE0E6
    static let rdCardLight          = Color.white                                    // #FFFFFF
    static let rdSurface2Light      = Color(red: 0.949, green: 0.953, blue: 0.961)  // #F2F3F5
    static let rdDividerLight       = Color(red: 0.929, green: 0.937, blue: 0.945)  // #EDEFF1

    // MARK: - Canvas & Surface (Dark)
    static let rdCanvasDark    = Color(red: 0.102, green: 0.102, blue: 0.106) // #1A1A1B
    static let rdCardDark      = Color(red: 0.153, green: 0.153, blue: 0.161) // #272729
    static let rdSurface2Dark  = Color(red: 0.204, green: 0.208, blue: 0.212) // #343536
    static let rdDividerDark   = Color(red: 0.204, green: 0.208, blue: 0.212) // #343536

    // MARK: - Text
    static let rdTextPrimary       = Color(red: 0.102, green: 0.102, blue: 0.106) // #1A1A1B
    static let rdTextSecondary     = Color(red: 0.486, green: 0.486, blue: 0.486) // #7C7C7C
    static let rdTextTertiary      = Color(red: 0.686, green: 0.686, blue: 0.686) // #AFAFAF
    static let rdTextPrimaryDark   = Color(red: 0.843, green: 0.855, blue: 0.863) // #D7DADC
    static let rdTextSecondaryDark = Color(red: 0.506, green: 0.514, blue: 0.518) // #818384
    static let rdTextLink          = Color(red: 0.000, green: 0.475, blue: 0.827) // #0079D3

    // MARK: - Semantic
    static let rdSuccessGreen = Color(red: 0.275, green: 0.820, blue: 0.376) // #46D160
    static let rdWarningYellow = Color(red: 1.000, green: 0.690, blue: 0.000) // #FFB000
    static let rdNSFWYellow   = Color(red: 0.953, green: 0.698, blue: 0.000) // #F3B200
    static let rdGold         = Color(red: 1.000, green: 0.690, blue: 0.000) // #FFB000
    static let rdPremiumGold  = Color(red: 1.000, green: 0.839, blue: 0.208) // #FFD635
    static let rdSubredditDefault = Color(red: 0.000, green: 0.475, blue: 0.827) // #0079D3
}
```

## 2. Typography

Reddit Sans is proprietary — bundle via `Info.plist` or fall back to `.system()`.

```swift
extension Font {
    private static func rd(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        if UIFont(name: "RedditSans-Regular", size: size) != nil {
            let suffix: String = {
                switch weight {
                case .bold:     return "Bold"
                case .semibold: return "SemiBold"
                case .medium:   return "Medium"
                default:        return "Regular"
                }
            }()
            return .custom("RedditSans-\(suffix)", size: size)
        }
        return .system(size: size, weight: weight)
    }

    static let rdLargeTitle   = rd(24, weight: .bold)
    static let rdPostTitle    = rd(18, weight: .semibold)
    static let rdBody         = rd(14, weight: .regular)
    static let rdMetadata     = rd(12, weight: .regular)
    static let rdKarma        = rd(12, weight: .bold)
    static let rdSubredditPill = rd(12, weight: .semibold)
    static let rdFlairPill    = rd(11, weight: .semibold)
    static let rdUsername     = rd(12, weight: .medium)
    static let rdButton       = rd(14, weight: .semibold)
    static let rdTabLabel     = rd(11, weight: .medium)
    static let rdNavTitle     = rd(17, weight: .semibold)
    static let rdSectionHeader = rd(13, weight: .bold)
    static let rdCode         = Font.system(size: 13, weight: .regular, design: .monospaced)
}
```

## 3. Signature Components

### Vote Column (The Signature)

```swift
struct RDVoteColumn: View {
    enum VoteState { case none, up, down }

    @Binding var state: VoteState
    let baseKarma: Int

    @State private var upScale: CGFloat = 1.0
    @State private var downScale: CGFloat = 1.0

    private var displayedKarma: Int {
        switch state {
        case .up:   return baseKarma + 1
        case .down: return baseKarma - 1
        case .none: return baseKarma
        }
    }

    private var karmaColor: Color {
        switch state {
        case .up:   return .rdUpvote
        case .down: return .rdDownvote
        case .none: return .rdVoteInactive
        }
    }

    var body: some View {
        VStack(spacing: 4) {
            Button {
                state = (state == .up) ? .none : .up
                bounce(&upScale)
            } label: {
                Image(systemName: state == .up ? "arrow.up.square.fill" : "arrow.up")
                    .font(.system(size: 18, weight: .semibold))
                    .foregroundStyle(state == .up ? Color.rdUpvote : Color.rdVoteInactive)
                    .scaleEffect(upScale)
            }
            .sensoryFeedback(.selection, trigger: state == .up)

            Text("\(displayedKarma)")
                .font(.rdKarma)
                .foregroundStyle(karmaColor)
                .contentTransition(.numericText())
                .animation(.spring(response: 0.2, dampingFraction: 0.6), value: displayedKarma)

            Button {
                state = (state == .down) ? .none : .down
                bounce(&downScale)
            } label: {
                Image(systemName: state == .down ? "arrow.down.square.fill" : "arrow.down")
                    .font(.system(size: 18, weight: .semibold))
                    .foregroundStyle(state == .down ? Color.rdDownvote : Color.rdVoteInactive)
                    .scaleEffect(downScale)
            }
            .sensoryFeedback(.selection, trigger: state == .down)
        }
        .frame(width: 44)
    }

    private func bounce(_ value: inout CGFloat) {
        withAnimation(.spring(response: 0.18, dampingFraction: 0.55)) {
            value = 1.25
        }
        DispatchQueue.main.asyncAfter(deadline: .now() + 0.18) {
            withAnimation(.spring(response: 0.2, dampingFraction: 0.7)) {
                value = 1.0
            }
        }
    }
}
```

### Post Card

```swift
struct RDPostCard: View {
    let subredditName: String
    let subredditAvatar: Image?
    let subredditAccent: Color
    let timestamp: String
    let commentCount: Int
    let title: String
    let body: String?
    let flairs: [RDFlair]
    let mediaUri: String?
    @State private var voteState: RDVoteColumn.VoteState = .none
    let baseKarma: Int

    var body: some View {
        VStack(alignment: .leading, spacing: 10) {
            headerRow
            Text(title).font(.rdPostTitle).foregroundStyle(.rdTextPrimary).lineLimit(3)
            if !flairs.isEmpty { flairRow }
            if let mediaUri, let url = URL(string: mediaUri) {
                AsyncImage(url: url) { $0.resizable().aspectRatio(16/9, contentMode: .fill) } placeholder: {
                    Color.rdSurface2Light.frame(height: 200)
                }
                .frame(maxWidth: .infinity, maxHeight: 300)
                .clipShape(RoundedRectangle(cornerRadius: 8))
            }
            actionRow
        }
        .padding(12)
        .background(
            RoundedRectangle(cornerRadius: 16)
                .fill(Color.rdCardLight)
        )
        .padding(.horizontal, 16)
        .padding(.vertical, 4)
    }

    private var headerRow: some View {
        HStack(spacing: 6) {
            (subredditAvatar ?? Image(systemName: "circle.fill"))
                .resizable()
                .aspectRatio(contentMode: .fill)
                .frame(width: 20, height: 20)
                .clipShape(Circle())
            Text("r/\(subredditName)").font(.rdSubredditPill).foregroundStyle(.rdTextPrimary)
            Text("•").font(.rdMetadata).foregroundStyle(.rdTextSecondary)
            Text(timestamp).font(.rdMetadata).foregroundStyle(.rdTextSecondary)
            Spacer()
            Button { } label: {
                Text("Join").font(.rdButton).foregroundStyle(.white)
                    .padding(.horizontal, 12).padding(.vertical, 4)
                    .background(Capsule().fill(subredditAccent))
            }
        }
    }

    private var flairRow: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 6) {
                ForEach(flairs) { flair in RDFlairPillView(flair: flair) }
            }
        }
    }

    private var actionRow: some View {
        HStack(spacing: 16) {
            RDVoteColumn(state: $voteState, baseKarma: baseKarma)
            Spacer()
            HStack(spacing: 16) {
                actionIcon("text.bubble", "\(commentCount)")
                actionIcon("square.and.arrow.up", nil)
                actionIcon("bookmark", nil)
                actionIcon("ellipsis", nil)
            }
        }
    }

    private func actionIcon(_ systemName: String, _ text: String?) -> some View {
        HStack(spacing: 4) {
            Image(systemName: systemName).font(.system(size: 18))
            if let text {
                Text(text).font(.rdMetadata)
            }
        }
        .foregroundStyle(.rdTextSecondary)
    }
}
```

### Flair Pill

```swift
struct RDFlair: Identifiable {
    let id = UUID()
    let text: String
    let background: Color
    let foreground: Color
    let emoji: String?
}

struct RDFlairPillView: View {
    let flair: RDFlair
    var body: some View {
        HStack(spacing: 4) {
            if let emoji = flair.emoji {
                Text(emoji).font(.system(size: 11))
            }
            Text(flair.text).font(.rdFlairPill)
        }
        .foregroundStyle(flair.foreground)
        .padding(.horizontal, 6)
        .padding(.vertical, 3)
        .background(RoundedRectangle(cornerRadius: 4).fill(flair.background))
    }
}

// Common system flairs
extension RDFlair {
    static let nsfw    = RDFlair(text: "NSFW",    background: Color.rdNSFWYellow, foreground: .black, emoji: nil)
    static let spoiler = RDFlair(text: "SPOILER", background: .black,              foreground: .white, emoji: nil)
    static let oc      = RDFlair(text: "OC",      background: Color.rdSuccessGreen, foreground: .white, emoji: nil)
}
```

### Comment Row

```swift
struct RDComment: Identifiable {
    let id = UUID()
    let username: String
    let karma: Int
    let timestamp: String
    let body: String
    let depth: Int
    let replies: [RDComment]
}

struct RDCommentRow: View {
    let comment: RDComment
    @State private var isCollapsed = false

    var body: some View {
        HStack(alignment: .top, spacing: 0) {
            ForEach(0..<comment.depth, id: \.self) { _ in
                Rectangle()
                    .fill(Color.rdDividerLight)
                    .frame(width: 2)
                    .padding(.leading, 10)
            }

            VStack(alignment: .leading, spacing: 6) {
                HStack(spacing: 6) {
                    Button { isCollapsed.toggle() } label: {
                        Image(systemName: isCollapsed ? "plus.square" : "minus.square")
                            .font(.system(size: 12))
                            .foregroundStyle(.rdTextSecondary)
                    }
                    Text("u/\(comment.username)").font(.rdUsername).foregroundStyle(.rdTextPrimary)
                    Text("•").font(.rdMetadata).foregroundStyle(.rdTextSecondary)
                    Text(comment.timestamp).font(.rdMetadata).foregroundStyle(.rdTextSecondary)
                    Text("•").font(.rdMetadata).foregroundStyle(.rdTextSecondary)
                    Text("\(comment.karma)").font(.rdMetadata.weight(.medium)).foregroundStyle(.rdTextSecondary)
                }

                if !isCollapsed {
                    Text(comment.body).font(.rdBody).foregroundStyle(.rdTextPrimary).lineSpacing(4)
                    actionRow
                }
            }
            .padding(.leading, 8)
            .padding(.vertical, 10)
        }
    }

    private var actionRow: some View {
        HStack(spacing: 16) {
            Image(systemName: "arrow.up").font(.system(size: 14))
            Image(systemName: "arrow.down").font(.system(size: 14))
            Image(systemName: "arrowshape.turn.up.left").font(.system(size: 14))
            Image(systemName: "square.and.arrow.up").font(.system(size: 14))
            Image(systemName: "ellipsis").font(.system(size: 14))
        }
        .foregroundStyle(.rdTextSecondary)
    }
}
```

### Subreddit Banner (Detail Screen)

```swift
struct RDSubredditBanner: View {
    let subredditName: String
    let memberCount: String
    let bannerUri: String?
    let avatarUri: String?
    let accentColor: Color
    @State private var joined = false

    var body: some View {
        VStack(spacing: 0) {
            ZStack(alignment: .bottomLeading) {
                Group {
                    if let bannerUri, let url = URL(string: bannerUri) {
                        AsyncImage(url: url) { $0.resizable().aspectRatio(contentMode: .fill) } placeholder: {
                            accentColor
                        }
                    } else {
                        accentColor
                    }
                }
                .frame(height: 160)
                .clipped()

                AsyncImage(url: URL(string: avatarUri ?? "")) { $0.resizable() } placeholder: {
                    Circle().fill(accentColor)
                }
                .frame(width: 72, height: 72)
                .clipShape(Circle())
                .overlay(Circle().stroke(Color.white, lineWidth: 3))
                .offset(x: 16, y: 36)
            }
            .frame(height: 160)
            .overlay(alignment: .topTrailing) {
                Button { joined.toggle() } label: {
                    Label(joined ? "Joined" : "Join", systemImage: joined ? "checkmark" : "plus")
                        .font(.rdButton)
                        .foregroundStyle(joined ? accentColor : .white)
                        .padding(.horizontal, 16).padding(.vertical, 6)
                        .background(Capsule().fill(joined ? Color.white : accentColor))
                        .overlay(Capsule().stroke(accentColor, lineWidth: joined ? 1.5 : 0))
                }
                .padding(16)
                .sensoryFeedback(.success, trigger: joined)
            }

            VStack(alignment: .leading, spacing: 4) {
                Text("r/\(subredditName)").font(.rdLargeTitle).foregroundStyle(.rdTextPrimary)
                Text(memberCount).font(.rdMetadata).foregroundStyle(.rdTextSecondary)
            }
            .padding(.top, 48)
            .padding(.horizontal, 16)
            .padding(.bottom, 12)
        }
    }
}
```

## 4. Tab Bar

```swift
struct RDRootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            HomeFeedView().tabItem { Label("Home", systemImage: "house.fill") }
            CommunitiesView().tabItem { Label("Communities", systemImage: "person.3.fill") }
            CreatePostView().tabItem { Label("Create", systemImage: "plus.circle.fill") }
            ChatView().tabItem { Label("Chat", systemImage: "bubble.left.and.bubble.right.fill") }
            InboxView().tabItem { Label("Inbox", systemImage: "bell.fill") }
                .badge(3)
        }
        .tint(.rdBrandRed)
    }
}
```

## 5. Motion

```swift
// Vote bounce
.sensoryFeedback(.selection, trigger: state)
withAnimation(.spring(response: 0.18, dampingFraction: 0.55)) {
    scale = 1.25
}

// Karma number ticker
.contentTransition(.numericText())

// Card press
.scaleEffect(pressed ? 0.99 : 1.0)
.animation(.easeOut(duration: 0.12), value: pressed)

// Comment collapse
withAnimation(.easeOut(duration: 0.2)) { isCollapsed.toggle() }

// Join button success
.sensoryFeedback(.success, trigger: joined)

// Pull-to-refresh (custom Snoo)
// Replace the default refresh indicator with a ZStack containing a rotating Snoo PNG
ProgressView().progressViewStyle(.circular).tint(.rdBrandRed)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Upvote | `arrow.up` / `arrow.up.square.fill` | 18pt |
| Downvote | `arrow.down` / `arrow.down.square.fill` | 18pt |
| Comment count | `text.bubble` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Save | `bookmark` / `bookmark.fill` | 18pt |
| More | `ellipsis` | 18pt |
| Reply | `arrowshape.turn.up.left` | 14pt |
| Join (state icon) | `plus` / `checkmark` | 14pt |
| Home tab | `house.fill` | 22pt |
| Communities tab | `person.3.fill` | 22pt |
| Create tab | `plus.circle.fill` | 26pt |
| Chat tab | `bubble.left.and.bubble.right.fill` | 22pt |
| Inbox tab | `bell.fill` | 22pt |
| Search | `magnifyingglass` | 18pt |
| Collapse comment | `minus.square` / `plus.square` | 12pt |
| Sort | `arrow.up.arrow.down` | 18pt |

## 7. Subreddit Accent Color (Dynamic Theming)

Subreddits set their own accent color. Model this as a `@State` or a value passed per-screen.

```swift
struct RDSubredditContext {
    var accentColor: Color = .rdSubredditDefault // #0079D3
    var displayName: String
    var isDark: Bool = false
}

// Pass via .environment or as an init parameter to RDPostCard / RDSubredditBanner
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback`, `.contentTransition(.numericText())` require iOS 17; fall back appropriately)
- Dynamic Type: support on post title, body, metadata; fix karma number, vote arrow icons, tab labels, and flair pills
- VoiceOver: combine vote column into single element ("12 karma, vote up, vote down"); read voting states clearly
- Reduce motion: disable the vote-tap scale bounce and comment collapse animation when `UIAccessibility.isReduceMotionEnabled`
- Color blindness: vote arrows are iconographic (up vs down) so they remain semantic without color; ensure NSFW and OC flair text is readable not color-only
- Contrast: `#7C7C7C` metadata on `#FFFFFF` card meets WCAG AA at 12pt; validate dark-mode `#818384` on `#272729`
- Markdown rendering: parse `**bold**`, `*italic*`, `> quote`, `[text](url)`, `` `code` ``, triple-backtick blocks via `AttributedString` or a third-party markdown renderer
- Subreddit accent contrast: validate subreddit-chosen hex against text color and Join-button white; swap to a darker variant if contrast fails
- Snoo alien in empty states: bundle as SVG asset; support light + dark palette variants
