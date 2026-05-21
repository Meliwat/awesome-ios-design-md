# LinkedIn (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates LinkedIn's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let liCanvas         = Color(red: 0.953, green: 0.949, blue: 0.937)  // #F3F2EF
    static let liCardSurface    = Color.white                                    // #FFFFFF
    static let liElevated       = Color(red: 0.976, green: 0.976, blue: 0.976)  // #F9F9F9
    static let liDivider        = Color(red: 0.878, green: 0.875, blue: 0.863)  // #E0DFDC
    static let liDividerSubtle  = Color(red: 0.929, green: 0.929, blue: 0.929)  // #EDEDED

    // MARK: - Text
    static let liTextPrimary    = Color.black.opacity(0.9)                      // #000000E6
    static let liTextSecondary  = Color.black.opacity(0.6)                      // #00000099
    static let liTextTertiary   = Color.black.opacity(0.4)                      // #00000066

    // MARK: - Brand
    static let liBlue           = Color(red: 0.039, green: 0.400, blue: 0.761)  // #0A66C2
    static let liBluePressed    = Color(red: 0.000, green: 0.255, blue: 0.510)  // #004182
    static let liBlueSubtle     = Color(red: 0.906, green: 0.953, blue: 1.000)  // #E7F3FF

    // MARK: - Status
    static let liOpenToWork     = Color(red: 0.020, green: 0.463, blue: 0.259)  // #057642
    static let liPremiumGold    = Color(red: 0.569, green: 0.349, blue: 0.027)  // #915907
    static let liPremiumGoldHi  = Color(red: 0.765, green: 0.490, blue: 0.086)  // #C37D16

    // MARK: - Reactions
    static let liReactLike      = Color(red: 0.039, green: 0.400, blue: 0.761)  // #0A66C2
    static let liReactCelebrate = Color(red: 0.961, green: 0.733, blue: 0.000)  // #F5BB00
    static let liReactSupport   = Color(red: 0.698, green: 0.251, blue: 0.125)  // #B24020
    static let liReactLove      = Color(red: 0.875, green: 0.439, blue: 0.302)  // #DF704D
    static let liReactInsightful = Color(red: 0.906, green: 0.639, blue: 0.243) // #E7A33E
    static let liReactFunny     = Color(red: 0.000, green: 0.627, blue: 0.863)  // #00A0DC

    // MARK: - Dark Mode
    static let liDarkCanvas     = Color(red: 0.106, green: 0.122, blue: 0.137)  // #1B1F23
    static let liDarkCard       = Color(red: 0.114, green: 0.133, blue: 0.149)  // #1D2226
    static let liDarkBlue       = Color(red: 0.439, green: 0.710, blue: 0.976)  // #70B5F9
}
```

## 2. Typography

LinkedIn uses SF Pro (Apple system) on iOS — no custom fonts to bundle. Use `Font.system` helpers.

```swift
extension Font {
    // Display
    static let liProfileName   = Font.system(size: 24, weight: .bold, design: .default)
    static let liScreenTitle   = Font.system(size: 20, weight: .bold, design: .default)
    static let liSectionHeader = Font.system(size: 16, weight: .bold, design: .default)

    // Feed
    static let liPostAuthor    = Font.system(size: 14, weight: .semibold, design: .default)
    static let liHeadline      = Font.system(size: 12, weight: .regular, design: .default)
    static let liPostBody      = Font.system(size: 14, weight: .regular, design: .default)
    static let liMeta          = Font.system(size: 12, weight: .regular, design: .default)

    // Interactive
    static let liButtonPrimary   = Font.system(size: 16, weight: .semibold, design: .default)
    static let liButtonSecondary = Font.system(size: 14, weight: .semibold, design: .default)
    static let liActionBar       = Font.system(size: 13, weight: .semibold, design: .default)
    static let liTab             = Font.system(size: 11, weight: .medium, design: .default)
    static let liBadge           = Font.system(size: 11, weight: .bold, design: .default)
}
```

## 3. Signature Components

### Primary Pill Button (Connect / Follow / Apply)

```swift
struct LinkedInPillButton: View {
    enum Variant { case filled, outline }
    let title: String
    let systemImage: String?
    var variant: Variant = .filled
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                if let systemImage {
                    Image(systemName: systemImage)
                        .font(.system(size: 16, weight: .semibold))
                }
                Text(title)
                    .font(variant == .filled ? .liButtonPrimary : .liButtonSecondary)
            }
            .foregroundStyle(variant == .filled ? Color.white : Color.liBlue)
            .padding(.vertical, 8)
            .padding(.horizontal, 16)
            .background(
                Capsule().fill(variant == .filled ? Color.liBlue : Color.clear)
            )
            .overlay(
                Capsule().strokeBorder(variant == .outline ? Color.liBlue : Color.clear, lineWidth: 1)
            )
        }
        .buttonStyle(LinkedInPressableStyle())
        .sensoryFeedback(.impact(flexibility: .soft), trigger: UUID())
    }
}

struct LinkedInPressableStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1.0)
            .animation(.easeOut(duration: 0.12), value: configuration.isPressed)
    }
}
```

### Feed Post Card

```swift
struct FeedPostCard: View {
    let authorName: String
    let connectionDegree: String           // "1st", "2nd", "3rd+"
    let headline: String                   // job title + company
    let timeAgo: String                    // "3d •"
    let body: String
    let mediaImage: Image?
    let isPremium: Bool
    let isOpenToWork: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(alignment: .top, spacing: 12) {
                AvatarView(size: 56, isPremium: isPremium, isOpenToWork: isOpenToWork)

                VStack(alignment: .leading, spacing: 2) {
                    HStack(spacing: 4) {
                        Text(authorName).font(.liPostAuthor).foregroundStyle(Color.liTextPrimary)
                        Text("• \(connectionDegree)").font(.liMeta).foregroundStyle(Color.liTextSecondary)
                    }
                    Text(headline)
                        .font(.liHeadline)
                        .foregroundStyle(Color.liTextSecondary)
                        .lineLimit(2)
                    HStack(spacing: 4) {
                        Text(timeAgo).font(.liMeta).foregroundStyle(Color.liTextSecondary)
                        Image(systemName: "globe.americas.fill")
                            .font(.system(size: 11))
                            .foregroundStyle(Color.liTextSecondary)
                    }
                }

                Spacer()

                Image(systemName: "ellipsis")
                    .font(.system(size: 20))
                    .foregroundStyle(Color.liTextSecondary)
                    .frame(width: 44, height: 44)
            }
            .padding(.horizontal, 16)
            .padding(.top, 12)

            Text(body)
                .font(.liPostBody)
                .foregroundStyle(Color.liTextPrimary)
                .lineSpacing(4)
                .padding(.horizontal, 16)
                .padding(.top, 12)

            if let mediaImage {
                mediaImage
                    .resizable()
                    .aspectRatio(contentMode: .fill)
                    .frame(maxWidth: .infinity)
                    .frame(height: 220)
                    .clipped()
                    .padding(.top, 12)
            }

            ReactionFooterRow(reactionCount: 127, commentCount: 14)
                .padding(.horizontal, 16)
                .padding(.vertical, 8)

            Divider().background(Color.liDividerSubtle)

            ActionBar()
                .frame(height: 44)
        }
        .background(Color.liCardSurface)
    }
}
```

### Avatar with Status Rings

```swift
struct AvatarView: View {
    let size: CGFloat
    let isPremium: Bool
    let isOpenToWork: Bool
    var imageName: String = "person.crop.circle.fill"

    var body: some View {
        ZStack {
            // Premium gold gradient frame
            if isPremium {
                Circle()
                    .strokeBorder(
                        LinearGradient(
                            colors: [.liPremiumGold, .liPremiumGoldHi],
                            startPoint: .bottom,
                            endPoint: .top
                        ),
                        lineWidth: 4
                    )
                    .frame(width: size + 4, height: size + 4)
            }

            // Open to Work ring (can layer over premium)
            if isOpenToWork {
                Circle()
                    .strokeBorder(Color.liOpenToWork, lineWidth: 4)
                    .frame(width: size + 4, height: size + 4)
            }

            Image(systemName: imageName)
                .resizable()
                .aspectRatio(contentMode: .fill)
                .frame(width: size, height: size)
                .clipShape(Circle())
        }
    }
}
```

### The 6-Reaction Picker (Long-press)

```swift
struct ReactionPicker: View {
    @Binding var isShown: Bool
    let onSelect: (Reaction) -> Void

    enum Reaction: String, CaseIterable {
        case like = "hand.thumbsup.fill"
        case celebrate = "hands.clap.fill"
        case support = "heart.circle.fill"
        case love = "heart.fill"
        case insightful = "lightbulb.fill"
        case funny = "face.smiling.fill"

        var color: Color {
            switch self {
            case .like:       return .liReactLike
            case .celebrate:  return .liReactCelebrate
            case .support:    return .liReactSupport
            case .love:       return .liReactLove
            case .insightful: return .liReactInsightful
            case .funny:      return .liReactFunny
            }
        }
    }

    var body: some View {
        HStack(spacing: 12) {
            ForEach(Array(Reaction.allCases.enumerated()), id: \.offset) { index, reaction in
                Button {
                    onSelect(reaction)
                    isShown = false
                } label: {
                    Image(systemName: reaction.rawValue)
                        .font(.system(size: 22))
                        .foregroundStyle(reaction.color)
                        .frame(width: 40, height: 40)
                }
                .transition(.scale.combined(with: .opacity))
                .sensoryFeedback(.impact(flexibility: .soft), trigger: isShown)
            }
        }
        .padding(.horizontal, 8)
        .padding(.vertical, 4)
        .background(
            Capsule().fill(Color.liCardSurface)
                .shadow(color: .black.opacity(0.12), radius: 16, y: 4)
        )
    }
}
```

### Reaction Footer (Post bottom stats row)

```swift
struct ReactionFooterRow: View {
    let reactionCount: Int
    let commentCount: Int

    var body: some View {
        HStack {
            HStack(spacing: -8) {
                reactionBubble(.liReactLike, icon: "hand.thumbsup.fill")
                reactionBubble(.liReactCelebrate, icon: "hands.clap.fill")
                reactionBubble(.liReactLove, icon: "heart.fill")
            }
            Text("\(reactionCount)")
                .font(.liMeta)
                .foregroundStyle(Color.liTextSecondary)
                .padding(.leading, 4)

            Spacer()

            Text("\(commentCount) comments")
                .font(.liMeta)
                .foregroundStyle(Color.liTextSecondary)
        }
    }

    func reactionBubble(_ color: Color, icon: String) -> some View {
        ZStack {
            Circle().fill(color).frame(width: 20, height: 20)
            Image(systemName: icon)
                .font(.system(size: 10, weight: .bold))
                .foregroundStyle(.white)
        }
        .overlay(Circle().stroke(Color.liCardSurface, lineWidth: 2))
    }
}
```

### Action Bar (Like / Comment / Repost / Send)

```swift
struct ActionBar: View {
    @State private var isLiked = false

    var body: some View {
        HStack(spacing: 0) {
            actionButton(icon: isLiked ? "hand.thumbsup.fill" : "hand.thumbsup",
                         label: "Like",
                         tint: isLiked ? .liBlue : .liTextSecondary) {
                isLiked.toggle()
            }
            actionButton(icon: "bubble.left", label: "Comment", tint: .liTextSecondary) {}
            actionButton(icon: "arrow.2.squarepath", label: "Repost", tint: .liTextSecondary) {}
            actionButton(icon: "paperplane", label: "Send", tint: .liTextSecondary) {}
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: isLiked)
    }

    func actionButton(icon: String, label: String, tint: Color, action: @escaping () -> Void) -> some View {
        Button(action: action) {
            HStack(spacing: 6) {
                Image(systemName: icon).font(.system(size: 20))
                Text(label).font(.liActionBar)
            }
            .foregroundStyle(tint)
            .frame(maxWidth: .infinity, maxHeight: .infinity)
        }
    }
}
```

## 4. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = .white
        appearance.shadowColor = UIColor(Color.liDivider)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            NetworkView().tabItem { Label("My Network", systemImage: "person.2.fill") }
            PostView().tabItem { Label("Post", systemImage: "plus.app.fill") }
            NotificationsView().tabItem { Label("Notifications", systemImage: "bell.fill") }
            JobsView().tabItem { Label("Jobs", systemImage: "briefcase.fill") }
        }
        .tint(.liTextPrimary)  // active is near-black, not blue
    }
}
```

## 5. Top Nav (Search Bar + Messaging Entry)

```swift
struct LinkedInTopNav: View {
    @State private var searchText = ""

    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: "person.crop.circle.fill")
                .resizable()
                .frame(width: 28, height: 28)
                .clipShape(Circle())

            HStack {
                Image(systemName: "magnifyingglass")
                    .font(.system(size: 16))
                    .foregroundStyle(Color.liTextSecondary)
                TextField("Search", text: $searchText)
                    .font(.system(size: 14))
            }
            .padding(.horizontal, 12)
            .frame(height: 40)
            .background(Capsule().fill(Color.liDividerSubtle))

            Image(systemName: "message")
                .font(.system(size: 22))
                .foregroundStyle(Color.liTextPrimary)
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 8)
        .background(Color.liCardSurface)
    }
}
```

## 6. Motion

Use `.sensoryFeedback` (iOS 17+) and SwiftUI springs.

```swift
// Connect tap
.sensoryFeedback(.success, trigger: connectTapped)

// Like tap (soft)
.sensoryFeedback(.impact(flexibility: .soft), trigger: isLiked)

// Long-press reaction picker open
// Use .onLongPressGesture(minimumDuration: 0.4) with .animation(.spring(response: 0.35, dampingFraction: 0.7))

// Message send
// Arrow rotation + slide-off: Animation(.easeOut(duration: 0.2))
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house.fill` | 24pt |
| Network (tab) | `person.2.fill` | 24pt |
| Post (tab) | `plus.app.fill` | 24pt |
| Notifications (tab) | `bell.fill` | 24pt |
| Jobs (tab) | `briefcase.fill` | 24pt |
| Search | `magnifyingglass` | 18pt |
| Messaging | `message` / `message.fill` | 22pt |
| Ellipsis | `ellipsis` | 20pt |
| Like | `hand.thumbsup` / `hand.thumbsup.fill` | 20pt |
| Celebrate | `hands.clap.fill` | 22pt |
| Love | `heart.fill` | 22pt |
| Support | `heart.circle.fill` | 22pt |
| Insightful | `lightbulb.fill` | 22pt |
| Funny | `face.smiling.fill` | 22pt |
| Comment | `bubble.left` | 20pt |
| Repost | `arrow.2.squarepath` | 20pt |
| Send | `paperplane` / `paperplane.fill` | 20pt |
| Connect | `person.badge.plus` | 16pt |
| Globe (public post) | `globe.americas.fill` | 11pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on post body, comments, profile name — keep timestamps, connection degree, tab labels, and badge labels FIXED
- VoiceOver: add `.accessibilityLabel` for the 6 reactions (e.g., "React with Like", "React with Celebrate")
- Contrast: Body text at `#000000E6` on `#F3F2EF` canvas and `#FFFFFF` cards both meet WCAG AA for 14pt body; secondary `#00000099` also passes AA for body sizes
- Reduce Motion: disable the scale-and-stagger entrance on the reaction picker (fade-in only) when `UIAccessibility.isReduceMotionEnabled` is true
- VoiceOver on feed card: group author row into a single accessible element; read as "Sarah Chen, 1st connection, Principal Designer at Figma, posted 3 days ago"
