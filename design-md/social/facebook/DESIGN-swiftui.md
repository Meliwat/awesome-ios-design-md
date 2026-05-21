# Facebook (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Facebook's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let fbCanvas           = Color(red: 0.941, green: 0.949, blue: 0.961) // #F0F2F5
    static let fbCard             = Color.white                                   // #FFFFFF
    static let fbSurfaceTint      = Color(red: 0.969, green: 0.973, blue: 0.980) // #F7F8FA
    static let fbDivider          = Color(red: 0.894, green: 0.902, blue: 0.922) // #E4E6EB
    static let fbSeparator        = Color(red: 0.808, green: 0.816, blue: 0.831) // #CED0D4

    // MARK: - Canvas & Surfaces (Dark)
    static let fbDarkCanvas       = Color(red: 0.094, green: 0.098, blue: 0.102) // #18191A
    static let fbDarkCard         = Color(red: 0.141, green: 0.145, blue: 0.149) // #242526
    static let fbDarkSurfaceTint  = Color(red: 0.227, green: 0.231, blue: 0.235) // #3A3B3C
    static let fbDarkDivider      = Color(red: 0.243, green: 0.251, blue: 0.259) // #3E4042

    // MARK: - Text
    static let fbTextPrimaryLight   = Color(red: 0.020, green: 0.020, blue: 0.020) // #050505
    static let fbTextPrimaryDark    = Color(red: 0.894, green: 0.902, blue: 0.922) // #E4E6EB
    static let fbTextSecondaryLight = Color(red: 0.396, green: 0.404, blue: 0.420) // #65676B
    static let fbTextSecondaryDark  = Color(red: 0.690, green: 0.702, blue: 0.722) // #B0B3B8
    static let fbTextTertiary       = Color(red: 0.541, green: 0.553, blue: 0.569) // #8A8D91

    // MARK: - Brand
    static let fbBlue             = Color(red: 0.094, green: 0.467, blue: 0.949) // #1877F2
    static let fbBluePressed      = Color(red: 0.039, green: 0.373, blue: 0.784) // #0A5FC8
    static let fbBlueLight        = Color(red: 0.906, green: 0.953, blue: 1.000) // #E7F3FF

    // MARK: - Reactions
    static let fbLikeBlue         = Color(red: 0.094, green: 0.467, blue: 0.949) // #1877F2 (same as Blue)
    static let fbLovePink         = Color(red: 0.953, green: 0.259, blue: 0.373) // #F3425F
    static let fbCareYellow       = Color(red: 0.969, green: 0.725, blue: 0.157) // #F7B928
    static let fbHahaYellow       = Color(red: 0.969, green: 0.725, blue: 0.157) // #F7B928
    static let fbWowYellow        = Color(red: 0.969, green: 0.725, blue: 0.157) // #F7B928
    static let fbSadYellow        = Color(red: 0.969, green: 0.725, blue: 0.157) // #F7B928
    static let fbAngryOrange      = Color(red: 0.914, green: 0.443, blue: 0.059) // #E9710F

    // MARK: - Semantic
    static let fbLiveRed          = Color(red: 0.980, green: 0.243, blue: 0.243) // #FA3E3E
    static let fbErrorRed         = Color(red: 0.980, green: 0.220, blue: 0.243) // #FA383E
    static let fbSuccessGreen     = Color(red: 0.259, green: 0.718, blue: 0.165) // #42B72A
}
```

## 2. Typography

Facebook uses SF Pro (Apple system font). Use `.system` directly — no custom font registration needed.

```swift
extension Font {
    // Display (20pt+) — SF Pro Display automatic
    static let fbFLogo         = Font.system(size: 22, weight: .black)        // The "f" logo weight 900
    static let fbScreenTitle   = Font.system(size: 24, weight: .bold)
    static let fbSectionHeader = Font.system(size: 20, weight: .bold)

    // Text (<20pt) — SF Pro Text automatic
    static let fbDisplayName   = Font.system(size: 15, weight: .semibold)
    static let fbPostBody      = Font.system(size: 15, weight: .regular)
    static let fbPostBodyLarge = Font.system(size: 24, weight: .semibold)  // Auto-styled short-text posts
    static let fbCommentBody   = Font.system(size: 14, weight: .regular)
    static let fbCommentName   = Font.system(size: 13, weight: .semibold)
    static let fbTimestamp     = Font.system(size: 13, weight: .regular)
    static let fbReactionCount = Font.system(size: 13, weight: .regular)
    static let fbActionLabel   = Font.system(size: 15, weight: .semibold)
    static let fbCTA           = Font.system(size: 17, weight: .semibold)
    static let fbTabLabel      = Font.system(size: 11, weight: .medium)
    static let fbSponsored     = Font.system(size: 13, weight: .regular)
    static let fbLiveBadge     = Font.system(size: 10, weight: .bold)
}
```

## 3. Signature Components

### Post Card

```swift
struct FBPostCard: View {
    let displayName: String
    let timestamp: String
    let audience: String // "Public", "Friends"
    let avatar: Image
    let body: String
    let media: Image?
    @State private var userReaction: FBReaction? = nil
    @State private var showReactions = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            // Header
            HStack(spacing: 8) {
                avatar.resizable().frame(width: 40, height: 40).clipShape(Circle())
                VStack(alignment: .leading, spacing: 2) {
                    Text(displayName)
                        .font(.fbDisplayName)
                        .foregroundStyle(.fbTextPrimaryLight)
                    HStack(spacing: 4) {
                        Text(timestamp).font(.fbTimestamp).foregroundStyle(.fbTextSecondaryLight)
                        Text("·").foregroundStyle(.fbTextSecondaryLight)
                        Image(systemName: "globe.americas.fill")
                            .font(.system(size: 12))
                            .foregroundStyle(.fbTextSecondaryLight)
                        Text(audience).font(.fbTimestamp).foregroundStyle(.fbTextSecondaryLight)
                    }
                }
                Spacer()
                Button { } label: {
                    Image(systemName: "ellipsis")
                        .font(.system(size: 20))
                        .foregroundStyle(.fbTextSecondaryLight)
                }
            }
            .padding(12)

            // Body
            Text(body)
                .font(.fbPostBody)
                .foregroundStyle(.fbTextPrimaryLight)
                .padding(.horizontal, 12)
                .padding(.bottom, 12)

            // Media
            if let media {
                media.resizable().aspectRatio(contentMode: .fit)
            }

            // Reaction summary
            HStack(spacing: 6) {
                HStack(spacing: -4) {
                    Circle().fill(Color.fbLikeBlue).frame(width: 18, height: 18).overlay(Image(systemName: "hand.thumbsup.fill").font(.system(size: 9)).foregroundStyle(.white))
                    Circle().fill(Color.fbLovePink).frame(width: 18, height: 18).overlay(Image(systemName: "heart.fill").font(.system(size: 9)).foregroundStyle(.white))
                }
                Text("You, Sarah, and 45 others").font(.fbReactionCount).foregroundStyle(.fbTextSecondaryLight)
                Spacer()
                Text("12 comments · 3 shares").font(.fbReactionCount).foregroundStyle(.fbTextSecondaryLight)
            }
            .padding(.horizontal, 12)
            .padding(.vertical, 8)

            Divider().background(Color.fbDivider)

            // Action row
            HStack(spacing: 0) {
                FBActionButton(icon: userReaction?.symbol ?? "hand.thumbsup",
                               label: userReaction?.label ?? "Like",
                               color: userReaction?.color ?? .fbTextSecondaryLight,
                               onTap: {
                                   userReaction = userReaction == nil ? .like : nil
                               },
                               onLongPress: { showReactions = true })
                FBActionButton(icon: "bubble.left", label: "Comment", color: .fbTextSecondaryLight, onTap: {}, onLongPress: {})
                FBActionButton(icon: "arrowshape.turn.up.right", label: "Share", color: .fbTextSecondaryLight, onTap: {}, onLongPress: {})
            }
            .padding(.vertical, 4)

            if showReactions {
                ReactionsPopover(selected: $userReaction, isShown: $showReactions)
            }
        }
        .background(Color.fbCard)
        .cornerRadius(8)
        .padding(.horizontal, 8)
        .padding(.vertical, 4)
    }
}
```

### Action Button (Like / Comment / Share)

```swift
struct FBActionButton: View {
    let icon: String
    let label: String
    let color: Color
    let onTap: () -> Void
    let onLongPress: () -> Void

    var body: some View {
        Button(action: onTap) {
            HStack(spacing: 6) {
                Image(systemName: icon)
                    .font(.system(size: 20))
                    .foregroundStyle(color)
                Text(label)
                    .font(.fbActionLabel)
                    .foregroundStyle(color)
            }
            .frame(maxWidth: .infinity, minHeight: 40)
            .contentShape(Rectangle())
        }
        .onLongPressGesture(minimumDuration: 0.4, perform: onLongPress)
        .sensoryFeedback(.impact(flexibility: .soft), trigger: label)
    }
}
```

### Reactions Popover

```swift
enum FBReaction: String, CaseIterable, Identifiable {
    case like, love, care, haha, wow, sad, angry
    var id: String { rawValue }
    var label: String {
        switch self {
        case .like: "Like"; case .love: "Love"; case .care: "Care"
        case .haha: "Haha"; case .wow: "Wow";   case .sad: "Sad";    case .angry: "Angry"
        }
    }
    var symbol: String {  // SF Symbol approximation
        switch self {
        case .like:  "hand.thumbsup.fill"
        case .love:  "heart.fill"
        case .care:  "hands.sparkles.fill"
        case .haha:  "face.smiling.fill"
        case .wow:   "mouth.fill"
        case .sad:   "face.dashed.fill"
        case .angry: "flame.fill"
        }
    }
    var color: Color {
        switch self {
        case .like:  .fbLikeBlue
        case .love:  .fbLovePink
        case .care, .haha, .wow, .sad: .fbHahaYellow
        case .angry: .fbAngryOrange
        }
    }
}

struct ReactionsPopover: View {
    @Binding var selected: FBReaction?
    @Binding var isShown: Bool
    @State private var hoveredIndex: Int? = nil

    var body: some View {
        HStack(spacing: 4) {
            ForEach(Array(FBReaction.allCases.enumerated()), id: \.offset) { i, reaction in
                Image(systemName: reaction.symbol)
                    .font(.system(size: 28))
                    .foregroundStyle(reaction.color)
                    .frame(width: 48, height: 48)
                    .scaleEffect(hoveredIndex == i ? 1.3 : 1.0)
                    .animation(.spring(response: 0.25), value: hoveredIndex)
                    .onTapGesture {
                        selected = reaction
                        isShown = false
                    }
            }
        }
        .padding(.horizontal, 8)
        .padding(.vertical, 6)
        .background(
            Capsule().fill(Color.fbCard)
                .shadow(color: .black.opacity(0.2), radius: 16, y: 4)
        )
        .sensoryFeedback(.selection, trigger: hoveredIndex)
        .transition(.scale.combined(with: .opacity))
    }
}
```

### Top Nav Bar (with "f" logo)

```swift
struct FBTopNav: View {
    var body: some View {
        HStack {
            // Blue "f" logo in rounded square
            ZStack {
                RoundedRectangle(cornerRadius: 8)
                    .fill(Color.fbBlue)
                    .frame(width: 36, height: 36)
                Text("f")
                    .font(.fbFLogo)
                    .foregroundStyle(.white)
                    .offset(y: -1)
            }

            Spacer()

            HStack(spacing: 8) {
                FBCircleIconButton(system: "magnifyingglass")
                FBCircleIconButton(system: "ellipsis.message.fill")
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 56)
        .background(Color.fbCard)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.fbDivider).frame(height: 1)
        }
    }
}

struct FBCircleIconButton: View {
    let system: String
    var body: some View {
        Button { } label: {
            Image(systemName: system)
                .font(.system(size: 18))
                .foregroundStyle(.fbTextPrimaryLight)
                .frame(width: 36, height: 36)
                .background(Circle().fill(Color.fbDivider))
        }
    }
}
```

## 4. Facebook-Specific Feature: Reactions Popover with Drag-Select

```swift
struct ReactionsLongPressModifier: ViewModifier {
    @Binding var selection: FBReaction?
    @State private var showPopover = false
    @State private var dragIndex: Int? = nil

    func body(content: Content) -> some View {
        content
            .simultaneousGesture(
                LongPressGesture(minimumDuration: 0.4)
                    .onEnded { _ in
                        showPopover = true
                        UIImpactFeedbackGenerator(style: .light).impactOccurred()
                    }
            )
            .simultaneousGesture(
                DragGesture(minimumDistance: 0)
                    .onChanged { value in
                        guard showPopover else { return }
                        let x = value.location.x
                        let idx = min(max(Int(x / 48), 0), FBReaction.allCases.count - 1)
                        if idx != dragIndex {
                            dragIndex = idx
                            UISelectionFeedbackGenerator().selectionChanged()
                        }
                    }
                    .onEnded { _ in
                        if let i = dragIndex, showPopover {
                            selection = FBReaction.allCases[i]
                            UIImpactFeedbackGenerator(style: .medium).impactOccurred()
                        }
                        showPopover = false
                        dragIndex = nil
                    }
            )
            .overlay(alignment: .top) {
                if showPopover {
                    ReactionsPopover(selected: $selection, isShown: $showPopover)
                        .offset(y: -56)
                }
            }
    }
}

extension View {
    func fbReactions(selection: Binding<FBReaction?>) -> some View {
        modifier(ReactionsLongPressModifier(selection: selection))
    }
}
```

## 5. Tab Bar

Facebook's tab bar shows labels under every icon — NOT icon-only like X/Threads.

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor(Color.fbCard)
        appearance.shadowColor = UIColor(Color.fbDivider)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            HomeView()         .tabItem { Label("Home",          systemImage: "house.fill") }
            VideoView()        .tabItem { Label("Video",         systemImage: "play.rectangle.fill") }
            MarketplaceView()  .tabItem { Label("Marketplace",   systemImage: "cart.fill") }
            NotificationsView().tabItem { Label("Notifications", systemImage: "bell.fill") }.badge(3)
            MenuView()         .tabItem { Label("Menu",          systemImage: "line.3.horizontal") }
        }
        .tint(.fbBlue)
    }
}
```

## 6. Motion

Use SwiftUI spring defaults. Haptics via the modern `.sensoryFeedback` API (iOS 17+).

```swift
// Like tap
.sensoryFeedback(.impact(flexibility: .soft), trigger: isLiked)

// Reactions hover
.sensoryFeedback(.selection, trigger: hoveredIndex)

// Reactions selected
.sensoryFeedback(.impact(weight: .medium), trigger: selectedReaction)

// Card tap highlight
.scaleEffect(isPressed ? 0.99 : 1.0)

// Stories ring tap → opens story viewer
.sheet(isPresented: $showStory) {
    StoryViewerView()
        .presentationDetents([.large])
        .presentationBackground(.black)
}

// Reaction emoji swap into Like slot
.transition(.asymmetric(insertion: .scale.combined(with: .opacity),
                        removal: .scale.combined(with: .opacity)))
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Like (outline) | `hand.thumbsup` | 20pt |
| Like (filled) | `hand.thumbsup.fill` | 20pt |
| Love | `heart.fill` | 20pt (popover: 28pt emoji) |
| Comment | `bubble.left` | 20pt |
| Share | `arrowshape.turn.up.right` | 20pt |
| Overflow | `ellipsis` | 20pt |
| Search | `magnifyingglass` | 18pt |
| Messenger | `ellipsis.message.fill` | 18pt |
| Home tab | `house.fill` / `house` | 26pt |
| Video tab | `play.rectangle.fill` / `play.rectangle` | 26pt |
| Marketplace tab | `cart.fill` / `cart` | 26pt |
| Notifications tab | `bell.fill` / `bell` | 26pt |
| Menu tab | `line.3.horizontal` | 26pt |
| Photo picker | `photo.on.rectangle` | 24pt |
| Live video | `dot.radiowaves.left.and.right` | 24pt |
| Audience (Public) | `globe.americas.fill` | 12pt |
| Audience (Friends) | `person.2.fill` | 12pt |
| Friends | `person.fill.checkmark` | 16pt |
| Add Friend | `person.badge.plus` | 16pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator` / `UISelectionFeedbackGenerator`)
- Support Dynamic Type on post body, comment body, bio — cap display name/timestamps/tab labels to preserve card layout
- VoiceOver: announce reactions as "Liked by you and 45 others, tap to see breakdown"; label each reaction in the popover as a button with its name ("Love reaction")
- Reactions popover should be reachable without long-press for VoiceOver users — expose a "Choose Reaction" action that opens the popover via accessibility action
- Contrast: `#050505` on `#FFFFFF` exceeds WCAG AAA; `#65676B` secondary gray on white meets AA at 13pt+
- Reduce Motion: respect `@Environment(\.accessibilityReduceMotion)` — skip the stagger animation on reactions popover; show all 7 icons instantly. Keep color-change and haptic channels active
- Reduce Transparency: use solid backgrounds when enabled (Facebook doesn't rely heavily on blur, but Messenger overlay chat heads do)
- Card accessibility: each post card should be its own accessibility container — group header, body, reactions, action row so VoiceOver can navigate post-by-post rather than element-by-element
- The reaction illustrations are complex — provide `accessibilityLabel` strings on each ("Love reaction, heart icon") for VoiceOver users
