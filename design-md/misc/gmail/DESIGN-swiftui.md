# Gmail (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Gmail's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let gmailCanvas        = Color.white                                  // #FFFFFF
    static let gmailSurface       = Color(red: 0.965, green: 0.973, blue: 0.988) // #F6F8FC
    static let gmailSurfacePressed = Color(red: 0.945, green: 0.953, blue: 0.957) // #F1F3F4
    static let gmailDivider       = Color(red: 0.855, green: 0.863, blue: 0.878) // #DADCE0

    // MARK: - Text
    static let gmailTextPrimary   = Color(red: 0.125, green: 0.129, blue: 0.141) // #202124
    static let gmailTextSecondary = Color(red: 0.373, green: 0.388, blue: 0.408) // #5F6368
    static let gmailTextDisabled  = Color(red: 0.604, green: 0.627, blue: 0.651) // #9AA0A6
    static let gmailTextLink      = Color(red: 0.102, green: 0.451, blue: 0.910) // #1A73E8

    // MARK: - Gmail Red (FAB + destructive)
    static let gmailRed           = Color(red: 0.851, green: 0.188, blue: 0.145) // #D93025
    static let gmailRedPressed    = Color(red: 0.702, green: 0.149, blue: 0.118) // #B3261E
    static let gmailRedSubtle     = Color(red: 0.988, green: 0.910, blue: 0.902) // #FCE8E6

    // MARK: - Google 4-color brand
    static let googleRed          = Color(red: 0.918, green: 0.263, blue: 0.208) // #EA4335
    static let googleYellow       = Color(red: 0.984, green: 0.737, blue: 0.016) // #FBBC04
    static let googleGreen        = Color(red: 0.204, green: 0.659, blue: 0.325) // #34A853
    static let googleBlue         = Color(red: 0.259, green: 0.522, blue: 0.957) // #4285F4

    // MARK: - Semantic
    static let gmailStarYellow    = Color(red: 0.961, green: 0.729, blue: 0.094) // #F5BA18
    static let gmailArchiveTeal   = Color(red: 0.118, green: 0.557, blue: 0.243) // #1E8E3E
    static let gmailSelectedRow   = Color(red: 0.910, green: 0.941, blue: 0.996) // #E8F0FE
    static let gmailSnoozePurple  = Color(red: 0.631, green: 0.259, blue: 0.957) // #A142F4

    // MARK: - Dark Mode
    static let gmailDarkCanvas    = Color(red: 0.125, green: 0.129, blue: 0.141) // #202124
    static let gmailDarkSurface1  = Color(red: 0.157, green: 0.161, blue: 0.173) // #28292C
    static let gmailDarkSurface2  = Color(red: 0.188, green: 0.192, blue: 0.204) // #303134
    static let gmailDarkDivider   = Color(red: 0.235, green: 0.251, blue: 0.263) // #3C4043
    static let gmailDarkTextPrimary = Color(red: 0.910, green: 0.918, blue: 0.929) // #E8EAED
    static let gmailDarkRed       = Color(red: 0.949, green: 0.545, blue: 0.510) // #F28B82
}
```

## 2. Typography

Google Sans is proprietary — bundle TTFs via `Info.plist` (`UIAppFonts`). Roboto is Apache 2.0 licensed, also bundle. On iOS fall back to `.system` (SF Pro) for the cleanest native feel.

```swift
extension Font {
    // Google Sans (titles, buttons, structural)
    static let gmailTitleLarge    = Font.custom("GoogleSans-Regular", size: 28).weight(.regular)
    static let gmailTitleCompact  = Font.custom("GoogleSans-Medium",  size: 20).weight(.medium)
    static let gmailSectionHeader = Font.custom("GoogleSans-Medium",  size: 13).weight(.medium)
    static let gmailThreadSubject = Font.custom("GoogleSans-Regular", size: 22).weight(.regular)
    static let gmailSenderUnread  = Font.custom("GoogleSans-Medium",  size: 14).weight(.medium)
    static let gmailSubjectUnread = Font.custom("GoogleSans-Medium",  size: 14).weight(.medium)
    static let gmailButton        = Font.custom("GoogleSans-Medium",  size: 14).weight(.medium)
    static let gmailFabButton     = Font.custom("GoogleSans-Medium",  size: 16).weight(.medium)
    static let gmailSmartReply    = Font.custom("GoogleSans-Medium",  size: 14).weight(.medium)
    static let gmailLabelChip     = Font.custom("GoogleSans-Medium",  size: 12).weight(.medium)
    static let gmailTab           = Font.custom("GoogleSans-Medium",  size: 12).weight(.medium)

    // Roboto (body, metadata)
    static let gmailSenderRead    = Font.custom("Roboto-Regular", size: 14).weight(.regular)
    static let gmailSnippet       = Font.custom("Roboto-Regular", size: 13).weight(.regular)
    static let gmailBody          = Font.custom("Roboto-Regular", size: 15).weight(.regular)
    static let gmailTimestamp     = Font.custom("Roboto-Regular", size: 13).weight(.regular)
    static let gmailMeta          = Font.custom("Roboto-Regular", size: 12).weight(.regular)

    // System fallback
    static func gmailSystem(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Compose FAB (The Red Squircle)

```swift
struct ComposeFAB: View {
    var extended: Bool = false  // shows "Compose" label when true (tablet/landscape)
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: "square.and.pencil")
                    .font(.system(size: 24, weight: .medium))
                    .foregroundStyle(.white)
                if extended {
                    Text("Compose")
                        .font(.gmailFabButton)
                        .foregroundStyle(.white)
                }
            }
            .frame(width: extended ? 120 : 56, height: 56)
            .background(
                RoundedRectangle(cornerRadius: 16, style: .continuous)
                    .fill(Color.gmailRed)
            )
            .shadow(color: .black.opacity(0.2), radius: 12, y: 4)
        }
        .buttonStyle(GmailFABPressStyle())
        .sensoryFeedback(.impact(flexibility: .solid), trigger: UUID())
    }
}

struct GmailFABPressStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.96 : 1.0)
            .animation(.spring(response: 0.3, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Email List Row (with Swipe Actions)

```swift
struct EmailRow: View {
    let sender: String
    let subject: String
    let snippet: String
    let timestamp: String
    let isUnread: Bool
    let isStarred: Bool
    let avatarColor: Color
    let avatarInitial: Character

    var body: some View {
        HStack(spacing: 12) {
            // Avatar (colored fallback with initial)
            Circle()
                .fill(avatarColor)
                .frame(width: 36, height: 36)
                .overlay(
                    Text(String(avatarInitial))
                        .font(.custom("GoogleSans-Medium", size: 16))
                        .foregroundStyle(.white)
                )

            VStack(alignment: .leading, spacing: 2) {
                Text(sender)
                    .font(isUnread ? .gmailSenderUnread : .gmailSenderRead)
                    .foregroundStyle(Color.gmailTextPrimary)

                HStack(spacing: 0) {
                    Text(subject)
                        .font(isUnread ? .gmailSubjectUnread : .gmailSenderRead)
                        .foregroundStyle(Color.gmailTextPrimary)
                        .lineLimit(1)
                    Text(" — \(snippet)")
                        .font(.gmailSnippet)
                        .foregroundStyle(Color.gmailTextSecondary)
                        .lineLimit(1)
                }
            }

            Spacer()

            VStack(alignment: .trailing, spacing: 4) {
                Text(timestamp)
                    .font(.gmailTimestamp)
                    .foregroundStyle(isUnread ? Color.gmailTextPrimary : Color.gmailTextSecondary)

                Image(systemName: isStarred ? "star.fill" : "star")
                    .font(.system(size: 18))
                    .foregroundStyle(isStarred ? Color.gmailStarYellow : Color.gmailTextSecondary)
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 72)
        .background(Color.gmailCanvas)
        .contentShape(Rectangle())
        .swipeActions(edge: .leading, allowsFullSwipe: true) {
            Button {
                // archive
            } label: {
                Label("Archive", systemImage: "archivebox")
            }.tint(.gmailArchiveTeal)
        }
        .swipeActions(edge: .trailing, allowsFullSwipe: true) {
            Button(role: .destructive) {
                // delete
            } label: {
                Label("Delete", systemImage: "trash")
            }
        }
    }
}

// Avatar color helper — deterministic from sender email
enum GmailAvatarColor {
    static func color(for email: String) -> (Color, Character) {
        let initial = Character(String(email.first ?? "?").uppercased())
        switch initial {
        case "A"..."F": return (.googleRed, initial)
        case "G"..."L": return (.googleYellow, initial)
        case "M"..."R": return (.googleGreen, initial)
        default:        return (.googleBlue, initial)
        }
    }
}
```

### Thread View Header + Body

```swift
struct ThreadView: View {
    let subject: String
    let labels: [String]
    let emails: [EmailThread]

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 16) {
                Text(subject)
                    .font(.gmailThreadSubject)
                    .foregroundStyle(Color.gmailTextPrimary)
                    .padding(.horizontal, 16)
                    .padding(.top, 16)

                ScrollView(.horizontal, showsIndicators: false) {
                    HStack(spacing: 8) {
                        ForEach(labels, id: \.self) { label in
                            LabelChip(label: label, color: .googleBlue)
                        }
                    }
                    .padding(.horizontal, 16)
                }

                ForEach(emails) { email in
                    EmailThreadCard(email: email)
                        .padding(.horizontal, 16)
                }

                SmartReplyRow(suggestions: ["Thanks!", "Sounds good.", "I'll take a look."])
                    .padding(.horizontal, 16)
                    .padding(.vertical, 12)
            }
        }
        .background(Color.gmailCanvas)
    }
}
```

### Label Chip

```swift
struct LabelChip: View {
    let label: String
    let color: Color

    var body: some View {
        Text(label)
            .font(.gmailLabelChip)
            .foregroundStyle(color)
            .padding(.vertical, 4)
            .padding(.horizontal, 10)
            .background(Capsule().fill(color.opacity(0.1)))
            .overlay(Capsule().strokeBorder(color, lineWidth: 1))
    }
}
```

### Tonal Button (Reply / Reply all / Forward)

```swift
struct TonalButton: View {
    let title: String
    let systemImage: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                Image(systemName: systemImage).font(.system(size: 18))
                Text(title).font(.gmailButton)
            }
            .foregroundStyle(Color.gmailTextLink)
            .padding(.vertical, 10)
            .padding(.horizontal, 24)
            .background(Capsule().fill(Color.gmailSurface))
        }
        .buttonStyle(GmailTonalPressStyle())
    }
}

struct GmailTonalPressStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1.0)
            .background(configuration.isPressed ? Color.gmailSurfacePressed : Color.clear)
    }
}
```

### Smart Reply Row

```swift
struct SmartReplyRow: View {
    let suggestions: [String]

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 8) {
                ForEach(suggestions, id: \.self) { text in
                    Button {
                        // insert into compose field
                    } label: {
                        Text(text)
                            .font(.gmailSmartReply)
                            .foregroundStyle(Color.gmailTextLink)
                            .padding(.vertical, 10)
                            .padding(.horizontal, 16)
                            .background(Capsule().fill(Color.clear))
                            .overlay(Capsule().strokeBorder(Color.gmailDivider, lineWidth: 1))
                    }
                    .sensoryFeedback(.impact(flexibility: .soft), trigger: UUID())
                }
            }
        }
    }
}
```

## 4. Tab Bar (Material-style Active Indicator)

```swift
struct GmailRootTabView: View {
    var body: some View {
        TabView {
            MailView().tabItem { Label("Mail", systemImage: "envelope") }
            ChatView().tabItem { Label("Chat", systemImage: "bubble.left") }
            SpacesView().tabItem { Label("Spaces", systemImage: "person.3") }
            MeetView().tabItem { Label("Meet", systemImage: "video") }
        }
        .tint(.gmailTextLink)
        .onAppear {
            let appearance = UITabBarAppearance()
            appearance.configureWithOpaqueBackground()
            appearance.backgroundColor = UIColor(Color.gmailCanvas)
            appearance.shadowColor = UIColor(Color.gmailDivider)
            // Material You "active indicator" pill
            appearance.stackedLayoutAppearance.selected.iconColor = UIColor(Color.gmailTextLink)
            appearance.stackedLayoutAppearance.selected.titleTextAttributes = [.foregroundColor: UIColor(Color.gmailTextLink)]
            appearance.stackedLayoutAppearance.normal.iconColor   = UIColor(Color.gmailTextSecondary)
            appearance.stackedLayoutAppearance.normal.titleTextAttributes = [.foregroundColor: UIColor(Color.gmailTextSecondary)]
            UITabBar.appearance().standardAppearance    = appearance
            UITabBar.appearance().scrollEdgeAppearance  = appearance
        }
    }
}
```

For the fully Material You pill background behind the active tab, you'll need a custom `TabView` — build a `HStack` of tab buttons with a `ZStack` background `Capsule` sliding via `matchedGeometryEffect` when the selected tab changes.

## 5. Scroll-to-Hide FAB

```swift
struct InboxWithFAB: View {
    @State private var fabExtended = true
    @State private var lastScrollY: CGFloat = 0

    var body: some View {
        ZStack(alignment: .bottomTrailing) {
            ScrollView {
                LazyVStack(spacing: 0) {
                    ForEach(0..<50) { _ in
                        EmailRow(sender: "Sarah Chen",
                                 subject: "Q4 planning update",
                                 snippet: "Wanted to share the latest thinking on...",
                                 timestamp: "10:42 AM",
                                 isUnread: true,
                                 isStarred: false,
                                 avatarColor: .googleGreen,
                                 avatarInitial: "S")
                            .background(
                                GeometryReader { geo in
                                    Color.clear.preference(
                                        key: ScrollOffsetKey.self,
                                        value: geo.frame(in: .global).minY
                                    )
                                }
                            )
                    }
                }
            }
            .onPreferenceChange(ScrollOffsetKey.self) { y in
                let delta = y - lastScrollY
                if delta < -8 { fabExtended = false }
                if delta >  8 { fabExtended = true }
                lastScrollY = y
            }

            ComposeFAB(extended: fabExtended) {
                // open compose
            }
            .padding(.trailing, 16)
            .padding(.bottom, 16)
        }
    }
}

struct ScrollOffsetKey: PreferenceKey {
    static var defaultValue: CGFloat = 0
    static func reduce(value: inout CGFloat, nextValue: () -> CGFloat) {
        value = nextValue()
    }
}
```

## 6. Motion

Use `.sensoryFeedback` (iOS 17+) and SwiftUI springs.

```swift
// Compose FAB
.sensoryFeedback(.impact(flexibility: .solid), trigger: fabTapped)

// Star toggle
.sensoryFeedback(.impact(flexibility: .soft), trigger: isStarred)

// Archive swipe success
.sensoryFeedback(.success, trigger: archivedId)

// Smart Reply chip tap
.sensoryFeedback(.impact(flexibility: .soft), trigger: selectedChip)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Compose FAB | `square.and.pencil` or `pencil` | 24pt |
| Mail (tab) | `envelope` / `envelope.fill` | 24pt |
| Chat (tab) | `bubble.left` / `bubble.left.fill` | 24pt |
| Spaces (tab) | `person.3` / `person.3.fill` | 24pt |
| Meet (tab) | `video` / `video.fill` | 24pt |
| Search | `magnifyingglass` | 18pt |
| Menu hamburger | `line.3.horizontal` | 24pt |
| Archive | `archivebox` | 24pt |
| Delete | `trash` | 24pt |
| Mark unread | `envelope.badge` | 24pt |
| Snooze | `clock` | 24pt |
| Star | `star` / `star.fill` | 18pt |
| Reply | `arrowshape.turn.up.left` | 18pt |
| Reply all | `arrowshape.turn.up.left.2` | 18pt |
| Forward | `arrowshape.turn.up.right` | 18pt |
| Attachment | `paperclip` | 18pt |
| Send | `paperplane.fill` | 18pt |
| Back | `chevron.left` | 24pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Bundle Google Sans TTFs via `Info.plist` / `UIAppFonts`; bundle Roboto TTFs (Apache 2.0 licensed). If not bundled, `.system` on iOS renders SF Pro which is a reasonable substitute
- Dynamic Type: support on email body, subjects, snippets, thread content; keep section headers, timestamps, tab labels, Smart Reply chips, label chips at FIXED sizes for layout safety
- VoiceOver: group email row into single accessible element — "Sarah Chen, unread, Q4 planning update. Wanted to share the latest thinking. 10:42 AM. Not starred. Double tap to open, swipe left to archive, swipe right to delete."
- Reduce Motion: skip FAB scale spring and row shimmer; keep instantaneous swipe commit animation (without spring)
- High Contrast: boost snippet color from `#5F6368` to `#3C4043` when `UIAccessibility.isDarkerSystemColorsEnabled`
- Dark mode: switch shadow strategy from "alpha 0.15-0.2" shadows to a tonal surface tint (Material's dark-mode approach), since pure black shadows disappear on dark backgrounds
