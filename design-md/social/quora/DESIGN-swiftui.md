# Quora (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Quora's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the serif/sans split, and the signature vote pill.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let quoraCanvas    = Color.white                                  // #FFFFFF
    static let quoraSurface   = Color(red: 0.969, green: 0.969, blue: 0.973) // #F7F7F8
    static let quoraDivider   = Color(red: 0.878, green: 0.878, blue: 0.878) // #E0E0E0

    // MARK: - Text
    static let quoraTextPrimary   = Color(red: 0.157, green: 0.157, blue: 0.161) // #282829
    static let quoraTextSecondary = Color(red: 0.388, green: 0.392, blue: 0.400) // #636466
    static let quoraTextTertiary  = Color(red: 0.604, green: 0.604, blue: 0.612) // #9A9A9C

    // MARK: - Brand
    static let quoraRed        = Color(red: 0.725, green: 0.169, blue: 0.153) // #B92B27
    static let quoraRedPressed = Color(red: 0.620, green: 0.122, blue: 0.106) // #9E1F1B
    static let quoraUpvote     = Color(red: 0.180, green: 0.412, blue: 1.000) // #2E69FF
    static let quoraUpvoteWash = Color(red: 0.918, green: 0.941, blue: 1.000) // #EAF0FF
    static let quoraSuccess    = Color(red: 0.122, green: 0.643, blue: 0.388) // #1FA463
    static let quoraRedWash    = Color(red: 0.984, green: 0.918, blue: 0.918) // #FBEAEA
}
```

## 2. Typography

Quora's split is the brand: **Georgia for questions** (ships with iOS), **Inter for answers and UI**. Bundle Inter via `Info.plist` (`UIAppFonts`); Georgia needs no bundling.

```swift
extension Font {
    // Questions — Georgia (the serif voice)
    static let quoraQuestionDetail  = Font.custom("Georgia-Bold", size: 26)
    static let quoraQuestionCard    = Font.custom("Georgia-Bold", size: 19)
    static let quoraQuestionRelated = Font.custom("Georgia",      size: 16)
    static let quoraQuestionPrompt  = Font.custom("Georgia-Bold", size: 17)

    // Answers / UI — Inter (the sans voice)
    static let quoraAnswerBody  = Font.custom("Inter-Regular",  size: 16)
    static let quoraAnswerLead  = Font.custom("Inter-Regular",  size: 17)
    static let quoraCredential  = Font.custom("Inter-SemiBold", size: 13)
    static let quoraAuthorName  = Font.custom("Inter-Bold",     size: 15)
    static let quoraSection     = Font.custom("Inter-Bold",     size: 18)
    static let quoraMeta        = Font.custom("Inter-Regular",  size: 13)
    static let quoraVoteCount   = Font.custom("Inter-SemiBold", size: 14)
    static let quoraButton      = Font.custom("Inter-Bold",     size: 15)
    static let quoraButtonSec   = Font.custom("Inter-SemiBold", size: 14)
    static let quoraTab         = Font.custom("Inter-SemiBold", size: 10)
    static let quoraSegment     = Font.custom("Inter-Bold",     size: 14)
    static let quoraCaption     = Font.custom("Inter-Regular",  size: 12)
}

// If Inter is unavailable, fall back to the system sans:
extension Font {
    static func quoraSans(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### The Serif/Sans Split — Question + Answer Card

```swift
struct QuestionFeedCard: View {
    let authorName: String
    let credential: String
    let question: String
    let answerPreview: String

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack(spacing: 8) {
                Circle().fill(Color.quoraSurface).frame(width: 24, height: 24)
                VStack(alignment: .leading, spacing: 1) {
                    Text(authorName).font(.quoraAuthorName).foregroundStyle(Color.quoraTextPrimary)
                    Text(credential).font(.quoraCredential).foregroundStyle(Color.quoraTextSecondary)
                }
            }

            // QUESTION — Georgia, always
            Text(question)
                .font(.quoraQuestionCard)
                .foregroundStyle(Color.quoraTextPrimary)
                .lineSpacing(5)

            // ANSWER — Inter, always, 3-line clamp + (more)
            (Text(answerPreview).foregroundStyle(Color.quoraTextPrimary)
             + Text(" (more)").foregroundStyle(Color.quoraUpvote))
                .font(.quoraAnswerBody)
                .lineSpacing(6)
                .lineLimit(3)

            HStack(spacing: 24) {
                VotePill(count: "1.2K")
                IconAction(systemName: "bubble.left", label: "84")
                IconAction(systemName: "square.and.arrow.up", label: "Share")
                Spacer()
            }
        }
        .padding(16)
        .background(Color.quoraCanvas)
    }
}

struct IconAction: View {
    let systemName: String
    let label: String
    var body: some View {
        HStack(spacing: 6) {
            Image(systemName: systemName).font(.system(size: 18))
            Text(label).font(.quoraButtonSec)
        }
        .foregroundStyle(Color.quoraTextSecondary)
    }
}
```

### Upvote / Downvote Pill (Signature)

```swift
struct VotePill: View {
    let count: String
    @State private var state: VoteState = .none
    enum VoteState { case none, up, down }

    var body: some View {
        HStack(spacing: 0) {
            Button {
                state = state == .up ? .none : .up
            } label: {
                HStack(spacing: 6) {
                    Image(systemName: state == .up ? "arrowtriangle.up.fill" : "arrowtriangle.up")
                        .font(.system(size: 13, weight: .semibold))
                    Text(count).font(.quoraVoteCount)
                }
                .foregroundStyle(state == .up ? Color.quoraUpvote : Color.quoraTextSecondary)
                .padding(.horizontal, 12).frame(height: 32)
                .background(state == .up ? Color.quoraUpvoteWash : Color.clear)
            }

            Rectangle().fill(Color.quoraDivider).frame(width: 1, height: 20)

            Button {
                state = state == .down ? .none : .down  // downvote stays neutral, never red
            } label: {
                Image(systemName: state == .down ? "arrowtriangle.down.fill" : "arrowtriangle.down")
                    .font(.system(size: 13, weight: .semibold))
                    .foregroundStyle(Color.quoraTextSecondary)
                    .padding(.horizontal, 12).frame(height: 32)
            }
        }
        .overlay(Capsule().strokeBorder(Color.quoraDivider, lineWidth: 1))
        .clipShape(Capsule())
        .sensoryFeedback(.impact(weight: .light), trigger: state)
    }
}
```

### Primary "Answer" CTA

```swift
struct QuoraAnswerButton: View {
    let title: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                Image(systemName: "square.and.pencil").font(.system(size: 15, weight: .bold))
                Text(title).font(.quoraButton)
            }
            .foregroundStyle(.white)
            .padding(.vertical, 11).padding(.horizontal, 24)
            .background(RoundedRectangle(cornerRadius: 6).fill(Color.quoraRed))
        }
        .buttonStyle(QuoraPressable(pressedColor: .quoraRedPressed))
    }
}

struct QuoraPressable: ButtonStyle {
    var pressedColor: Color
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .brightness(configuration.isPressed ? -0.04 : 0)
            .animation(.spring(response: 0.2, dampingFraction: 0.85), value: configuration.isPressed)
    }
}
```

### Answer Detail Block (Credential Byline)

```swift
struct AnswerDetailBlock: View {
    let authorName: String
    let credential: String
    let timestamp: String
    let body: String

    var body: some View {
        VStack(alignment: .leading, spacing: 14) {
            HStack(spacing: 12) {
                Circle().fill(Color.quoraSurface).frame(width: 40, height: 40)
                VStack(alignment: .leading, spacing: 2) {
                    Text(authorName).font(.quoraAuthorName).foregroundStyle(Color.quoraTextPrimary)
                    Text(credential).font(.quoraCredential).foregroundStyle(Color.quoraTextSecondary)
                    Text(timestamp).font(.quoraMeta).foregroundStyle(Color.quoraTextSecondary)
                }
            }
            Text(body)
                .font(.quoraAnswerBody)
                .foregroundStyle(Color.quoraTextPrimary)
                .lineSpacing(6) // ≈ line-height 1.6 on 16pt
            HStack(spacing: 24) {
                VotePill(count: "3.4K")
                IconAction(systemName: "bubble.left", label: "212")
                IconAction(systemName: "bookmark", label: "")
                Spacer()
                Image(systemName: "ellipsis").foregroundStyle(Color.quoraTextSecondary)
            }
            Divider().background(Color.quoraDivider)
        }
        .padding(16)
        .background(Color.quoraCanvas)
    }
}
```

## 4. Spaces Carousel

```swift
struct SpacesRow: View {
    let spaces: [(name: String, members: String, banner: Color)]

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack {
                Text("Spaces for you").font(.quoraSection).foregroundStyle(Color.quoraTextPrimary)
                Spacer()
                Text("See all").font(.quoraButtonSec).foregroundStyle(Color.quoraUpvote)
            }
            .padding(.horizontal, 16)
            ScrollView(.horizontal, showsIndicators: false) {
                HStack(spacing: 12) {
                    ForEach(spaces, id: \.name) { s in
                        VStack(alignment: .leading, spacing: 8) {
                            s.banner.frame(height: 64)
                            Text(s.name).font(.quoraAuthorName).foregroundStyle(Color.quoraTextPrimary)
                            Text(s.members).font(.quoraCredential).foregroundStyle(Color.quoraTextSecondary)
                        }
                        .frame(width: 140, alignment: .leading)
                        .background(Color.quoraCanvas)
                        .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.quoraDivider, lineWidth: 1))
                        .clipShape(RoundedRectangle(cornerRadius: 10))
                    }
                }
                .padding(.horizontal, 16)
            }
        }
    }
}
```

## 5. Navigation

Quora pairs a top segmented control with a bottom `TabView`. The center "Answer" tab glyph is always red.

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithOpaqueBackground()
        a.backgroundColor = UIColor.white
        a.shadowColor = UIColor(Color.quoraDivider)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            FeedView().tabItem { Label("Home", systemImage: "house") }
            FollowingView().tabItem { Label("Following", systemImage: "person.2") }
            AnswerHubView().tabItem {
                Label("Answer", systemImage: "square.and.pencil")
            }
            SpacesView().tabItem { Label("Spaces", systemImage: "rectangle.3.group") }
            NotificationsView().tabItem { Label("Notifications", systemImage: "bell") }
        }
        .tint(.quoraTextPrimary) // active = near-black
        // Tint the center Answer item red via UITabBarItem appearance if isolating that tab
    }
}

// Top segmented bar
struct TopSegments: View {
    @Binding var selected: Int
    let tabs = ["For You", "Following", "Answer", "Spaces"]
    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 20) {
                ForEach(tabs.indices, id: \.self) { i in
                    VStack(spacing: 6) {
                        Text(tabs[i])
                            .font(.quoraSegment)
                            .foregroundStyle(selected == i ? Color.quoraTextPrimary : Color.quoraTextSecondary)
                        Rectangle()
                            .fill(selected == i ? Color.quoraRed : .clear)
                            .frame(height: 2)
                    }
                    .onTapGesture { selected = i }
                }
            }
            .padding(.horizontal, 16)
        }
        .frame(height: 44)
        .background(Color.quoraCanvas)
        .overlay(Rectangle().fill(Color.quoraDivider).frame(height: 1), alignment: .bottom)
    }
}
```

## 6. Motion

```swift
// Upvote tap — arrow fill + count roll, light haptic
.sensoryFeedback(.impact(weight: .light), trigger: voteState)

// Count roll
Text(count)
    .contentTransition(.numericText())
    .animation(.easeOut(duration: 0.2), value: count)

// Card → detail: crossfade + 8pt slide
.transition(.opacity.combined(with: .move(edge: .bottom)))

// Composer present: spring sheet
.presentationDetents([.large])
.animation(.spring(response: 0.32, dampingFraction: 0.85), value: showComposer)

// Follow toggle: label crossfade + border tween
.animation(.easeInOut(duration: 0.18), value: isFollowing)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Upvote | `arrowtriangle.up` / `arrowtriangle.up.fill` | 13pt |
| Downvote | `arrowtriangle.down` / `arrowtriangle.down.fill` | 13pt |
| Comment | `bubble.left` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Bookmark | `bookmark` / `bookmark.fill` | 18pt |
| More | `ellipsis` | 18pt |
| Answer / Compose | `square.and.pencil` | 15-24pt |
| Search | `magnifyingglass` | 16pt |
| Home (tab) | `house` / `house.fill` | 24pt |
| Following (tab) | `person.2` | 24pt |
| Spaces (tab) | `rectangle.3.group` | 24pt |
| Notifications (tab) | `bell` / `bell.fill` | 24pt |
| Back | `chevron.left` | 22pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on questions (Georgia) and answer bodies (Inter) — cap detail question at 30pt, feed at 22pt; pin vote counts, tab labels, segment labels
- VoiceOver: label the vote pill segments separately — "Upvote, 1,200" and "Downvote"; never expose a downvote count (Quora hides it)
- Read the credential byline aloud after the author name and before the timestamp — it is the trust signal
- Contrast: `#636466` secondary on `#FFFFFF` passes WCAG AA at 13pt+; `#9A9A9C` tertiary is for placeholders only, never essential text
- Preserve the serif/sans split for screen-reader users via clear element grouping: question element, then answer element
