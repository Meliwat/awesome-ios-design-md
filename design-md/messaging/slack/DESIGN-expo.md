# Slack (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Slack's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Sidebar (Aubergine default)
  aubergine:       '#4A154B',
  aubergineDark:   '#3F0E40',
  aubergineActive: '#1164A3',
  sidebarText:     '#CFC3CF',
  sidebarActiveText: '#FFFFFF',

  // Logo palette
  logoYellow: '#ECB22E',
  logoPink:   '#E01E5A',
  logoGreen:  '#2EB67D',
  logoBlue:   '#36C5F0',

  // Canvas (light)
  canvas:   '#FFFFFF',
  surface:  '#F8F8F8',
  pressed:  '#E6E6E6',
  divider:  '#DDDDDD',

  // Canvas (dark)
  darkCanvas:    '#1A1D21',
  darkSurface1:  '#222529',
  darkSurface2:  '#2C2D30',
  darkDivider:   '#35373B',

  // Text
  textPrimary:       '#1D1C1D',
  textSecondary:     '#616061',
  textTertiary:      '#868686',
  darkTextPrimary:   '#D1D2D3',
  darkTextSecondary: '#ABABAD',

  // Semantic
  onlineGreen: '#007A5A',
  mentionRed:  '#E01E5A',
  typingBlue:  '#1264A3',
  linkBlue:    '#1264A3',

  // Mention pill backgrounds
  mentionChannelBg: '#D4EBD8',
  mentionChannelText: '#0F5A3D',
  mentionHereBg:    '#FEF9E7',
  mentionHereText:  '#5D4A0D',
  mentionUserBg:    '#F9D5DB',
  mentionUserText:  '#5A2731',

  // Reaction (you reacted)
  reactionSelfBg: '#E3F2FC',
} as const;

export type SlackColor = keyof typeof colors;
```

## 2. Typography

Bundle Slack Lato via `expo-font`. Fall back to Lato (Apache 2.0) or system on iOS.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'SlackLato-Regular':  require('../assets/fonts/SlackLato-Regular.ttf'),
    'SlackLato-Semibold': require('../assets/fonts/SlackLato-Semibold.ttf'),
    'SlackLato-Bold':     require('../assets/fonts/SlackLato-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#1D1C1D' } satisfies TextStyle;

export const typography = {
  workspaceName:  { color: '#FFFFFF', fontFamily: 'SlackLato-Bold',    fontSize: 18, lineHeight: 22 },
  channelActive:  { color: '#FFFFFF', fontFamily: 'SlackLato-Bold',    fontSize: 15, lineHeight: 20 },
  channelDefault: { color: '#CFC3CF', fontFamily: 'SlackLato-Regular', fontSize: 15, lineHeight: 20 },
  channelUnread:  { color: '#FFFFFF', fontFamily: 'SlackLato-Bold',    fontSize: 15, lineHeight: 20 },
  channelHeader:  { ...primary, fontFamily: 'SlackLato-Bold',    fontSize: 17, lineHeight: 20 },
  channelTopic:   { color: '#616061', fontFamily: 'SlackLato-Regular', fontSize: 13, lineHeight: 17 },

  username:       { ...primary, fontFamily: 'SlackLato-Bold',    fontSize: 15, lineHeight: 20 },
  messageBody:    { ...primary, fontFamily: 'SlackLato-Regular', fontSize: 15, lineHeight: 22 },
  timestamp:      { color: '#616061', fontFamily: 'SlackLato-Regular', fontSize: 12, lineHeight: 14 },

  threadCount:    { color: '#1264A3', fontFamily: 'SlackLato-Semibold', fontSize: 13, lineHeight: 13 },
  reactionCount:  { ...primary, fontFamily: 'SlackLato-Semibold', fontSize: 12, lineHeight: 12 },

  buttonPrimary:  { color: '#FFFFFF', fontFamily: 'SlackLato-Bold',    fontSize: 15, lineHeight: 15 },
  buttonSecondary:{ ...primary, fontFamily: 'SlackLato-Semibold', fontSize: 14, lineHeight: 14 },
  tab:            { fontFamily: 'SlackLato-Semibold', fontSize: 11, lineHeight: 11, letterSpacing: 0.1 },
  sectionHeader:  { fontFamily: 'SlackLato-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 1.0, textTransform: 'uppercase' as const },

  codeInline:     { ...primary, fontFamily: 'Menlo-Regular', fontSize: 13, lineHeight: 18 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Slack Sidebar (Workspace Header + Channels)

```tsx
// components/Sidebar.tsx
import { Image, Pressable, Text, View, ScrollView } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type ChannelType = 'public' | 'private' | 'dm';
type Channel = { id: string; type: ChannelType; name: string; avatarUri?: string; isUnread: boolean; mentionCount: number };

export function Sidebar({
  workspaceName,
  sidebarColor = colors.aubergine,
  activeColor = colors.aubergineActive,
  sections,
  activeChannelId,
}: {
  workspaceName: string;
  sidebarColor?: string;
  activeColor?: string;
  sections: { title: string; channels: Channel[] }[];
  activeChannelId?: string;
}) {
  return (
    <View style={{ flex: 1, backgroundColor: sidebarColor, width: 280 }}>
      {/* Workspace header */}
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 16, height: 56 }}>
        <View style={{ width: 32, height: 32, borderRadius: 4, backgroundColor: colors.logoBlue, alignItems: 'center', justifyContent: 'center' }}>
          <Text style={{ color: '#FFF', fontFamily: 'SlackLato-Bold', fontSize: 16 }}>A</Text>
        </View>
        <View style={{ flex: 1 }}>
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
            <Text style={typography.workspaceName}>{workspaceName}</Text>
            <Ionicons name="chevron-down" size={10} color="rgba(255,255,255,0.7)" />
          </View>
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginTop: 2 }}>
            <View style={{ width: 8, height: 8, borderRadius: 4, backgroundColor: colors.onlineGreen }} />
            <Text style={{ color: colors.sidebarText, fontFamily: 'SlackLato-Regular', fontSize: 13 }}>You</Text>
          </View>
        </View>
        <Ionicons name="create-outline" size={18} color="rgba(255,255,255,0.7)" />
      </View>

      <ScrollView>
        {sections.map((section) => (
          <View key={section.title}>
            <Text style={[typography.sectionHeader, { color: colors.sidebarText, opacity: 0.5, paddingHorizontal: 16, paddingTop: 16, paddingBottom: 4 }]}>
              {section.title}
            </Text>
            {section.channels.map((channel) => (
              <ChannelRow
                key={channel.id}
                channel={channel}
                isActive={channel.id === activeChannelId}
                activeColor={activeColor}
              />
            ))}
          </View>
        ))}
      </ScrollView>
    </View>
  );
}

function ChannelRow({ channel, isActive, activeColor }: { channel: Channel; isActive: boolean; activeColor: string }) {
  return (
    <Pressable style={{
      flexDirection: 'row', alignItems: 'center', gap: 12,
      paddingHorizontal: 16, height: 32,
      backgroundColor: isActive ? activeColor : 'transparent',
    }}>
      {channel.type === 'public' ? (
        <Text style={{ color: isActive || channel.isUnread ? '#FFF' : colors.sidebarText, fontFamily: 'SlackLato-Regular', fontSize: 16, width: 20 }}>#</Text>
      ) : channel.type === 'private' ? (
        <Ionicons name="lock-closed" size={14} color={isActive ? '#FFF' : colors.sidebarText} style={{ width: 20 }} />
      ) : (
        <Image source={{ uri: channel.avatarUri }} style={{ width: 20, height: 20, borderRadius: 3 }} />
      )}
      <Text style={
        isActive ? typography.channelActive :
        channel.isUnread ? typography.channelUnread :
        typography.channelDefault
      }>{channel.name}</Text>
      <View style={{ flex: 1 }} />
      {channel.mentionCount > 0 ? (
        <View style={{ backgroundColor: colors.mentionRed, borderRadius: 500, paddingHorizontal: 6, paddingVertical: 2 }}>
          <Text style={{ color: '#FFF', fontFamily: 'SlackLato-Bold', fontSize: 11 }}>{channel.mentionCount}</Text>
        </View>
      ) : null}
    </Pressable>
  );
}
```

### Message Row

```tsx
// components/MessageRow.tsx
import { Image, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { ReactionChip, AddReactionChip } from './ReactionChip';
import { ThreadIndicator } from './ThreadIndicator';

export function MessageRow({
  username, avatarUri, statusEmoji, status, isOnline, timestamp, body, reactions = [], threadReplyCount, threadLastReply, isNewSender = true,
}: {
  username: string; avatarUri?: string; statusEmoji?: string; status?: string; isOnline?: boolean;
  timestamp: string; body: string;
  reactions?: { emoji: string; count: number; youReacted: boolean }[];
  threadReplyCount?: number; threadLastReply?: string;
  isNewSender?: boolean;
}) {
  return (
    <View style={{ flexDirection: 'row', gap: 12, paddingHorizontal: 16, paddingVertical: isNewSender ? 8 : 2 }}>
      {isNewSender ? (
        <View style={{ width: 36, height: 36, position: 'relative' }}>
          <Image source={{ uri: avatarUri }} style={{ width: 36, height: 36, borderRadius: 4, backgroundColor: colors.surface }} />
          {isOnline ? (
            <View style={{ position: 'absolute', bottom: -2, right: -2, width: 10, height: 10, borderRadius: 5, backgroundColor: colors.onlineGreen, borderWidth: 2, borderColor: colors.canvas }} />
          ) : null}
        </View>
      ) : (
        <View style={{ width: 36 }} />
      )}
      <View style={{ flex: 1 }}>
        {isNewSender ? (
          <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 6, flexWrap: 'wrap' }}>
            <Text style={typography.username}>{username}</Text>
            {statusEmoji ? <Text>{statusEmoji}</Text> : null}
            {status ? <Text style={typography.channelTopic} numberOfLines={1}>{status}</Text> : null}
            <Text style={typography.timestamp}>{timestamp}</Text>
          </View>
        ) : null}
        <Text style={typography.messageBody}>{body}</Text>

        {reactions.length > 0 ? (
          <View style={{ flexDirection: 'row', gap: 4, marginTop: 6, flexWrap: 'wrap' }}>
            {reactions.map((r, i) => <ReactionChip key={i} {...r} />)}
            <AddReactionChip />
          </View>
        ) : null}

        {threadReplyCount && threadReplyCount > 0 ? (
          <ThreadIndicator replyCount={threadReplyCount} lastReply={threadLastReply ?? ''} />
        ) : null}
      </View>
    </View>
  );
}
```

### Reaction Chip

```tsx
// components/ReactionChip.tsx
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ReactionChip({
  emoji, count, youReacted,
}: { emoji: string; count: number; youReacted: boolean }) {
  const scale = useSharedValue(1);
  const animStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const onToggle = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
    scale.value = withSequence(withSpring(1.15), withSpring(1));
  };

  return (
    <Pressable onPress={onToggle}>
      <Animated.View
        style={[
          {
            flexDirection: 'row', alignItems: 'center', gap: 4,
            paddingHorizontal: 8, paddingVertical: 4,
            borderRadius: 500,
            backgroundColor: youReacted ? colors.reactionSelfBg : colors.surface,
            borderWidth: youReacted ? 1 : 0,
            borderColor: colors.typingBlue,
          },
          animStyle,
        ]}
      >
        <Text style={{ fontSize: 14 }}>{emoji}</Text>
        <Text style={typography.reactionCount}>{count}</Text>
      </Animated.View>
    </Pressable>
  );
}

export function AddReactionChip() {
  return (
    <Pressable>
      <View style={{
        flexDirection: 'row', alignItems: 'center',
        paddingHorizontal: 8, paddingVertical: 4,
        borderRadius: 500,
        backgroundColor: colors.surface,
      }}>
        <Ionicons name="happy-outline" size={14} color={colors.textSecondary} />
      </View>
    </Pressable>
  );
}
```

### Thread Indicator

```tsx
// components/ThreadIndicator.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ThreadIndicator({ replyCount, lastReply }: { replyCount: number; lastReply: string }) {
  const replyAvatarColors = [colors.logoYellow, colors.logoPink, colors.logoGreen];
  return (
    <Pressable style={{ flexDirection: 'row', alignItems: 'center', gap: 8, marginTop: 6 }}>
      <View style={{ flexDirection: 'row' }}>
        {Array.from({ length: Math.min(3, replyCount) }).map((_, i) => (
          <View key={i} style={{
            width: 16, height: 16, borderRadius: 3,
            backgroundColor: replyAvatarColors[i % 3],
            borderWidth: 1, borderColor: colors.canvas,
            marginLeft: i === 0 ? 0 : -6,
          }} />
        ))}
      </View>
      <Text style={typography.threadCount}>{replyCount} {replyCount === 1 ? 'reply' : 'replies'}</Text>
      <Text style={typography.timestamp}>• {lastReply}</Text>
      <View style={{ flex: 1 }} />
      <Ionicons name="chevron-forward" size={12} color={colors.textSecondary} />
    </Pressable>
  );
}
```

### Mention Pill

```tsx
// components/MentionPill.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';

type MentionType = 'channel' | 'here' | { user: string };

export function MentionPill({ type }: { type: MentionType }) {
  const { text, bg, fg } = (() => {
    if (type === 'channel') return { text: '@channel', bg: colors.mentionChannelBg, fg: colors.mentionChannelText };
    if (type === 'here')    return { text: '@here',    bg: colors.mentionHereBg,    fg: colors.mentionHereText };
    return { text: `@${type.user}`, bg: colors.mentionUserBg, fg: colors.mentionUserText };
  })();

  return (
    <View style={{ paddingHorizontal: 4, paddingVertical: 1, borderRadius: 4, backgroundColor: bg }}>
      <Text style={{ color: fg, fontFamily: 'SlackLato-Bold', fontSize: 15 }}>{text}</Text>
    </View>
  );
}
```

### Huddles Banner

```tsx
// components/HuddlesBanner.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function HuddlesBanner({ participantAvatars, onJoin }: { participantAvatars: string[]; onJoin: () => void }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 16, height: 56, backgroundColor: colors.typingBlue }}>
      {/* 4-color pinwheel */}
      <View style={{ width: 24, height: 24 }}>
        <View style={{ position: 'absolute', top: 0, left: 0, width: 12, height: 12, backgroundColor: colors.logoYellow }} />
        <View style={{ position: 'absolute', top: 0, right: 0, width: 12, height: 12, backgroundColor: colors.logoPink }} />
        <View style={{ position: 'absolute', bottom: 0, left: 0, width: 12, height: 12, backgroundColor: colors.logoGreen }} />
        <View style={{ position: 'absolute', bottom: 0, right: 0, width: 12, height: 12, backgroundColor: colors.logoBlue }} />
      </View>
      <Text style={typography.buttonPrimary}>Huddle in progress</Text>
      <View style={{ flex: 1 }} />
      <Pressable
        onPress={onJoin}
        style={({ pressed }) => ({
          paddingHorizontal: 16, paddingVertical: 8,
          borderRadius: 4,
          backgroundColor: pressed ? '#EEE' : '#FFF',
        })}
      >
        <Text style={[typography.buttonSecondary, { color: colors.typingBlue }]}>Join</Text>
      </Pressable>
    </View>
  );
}
```

### Message Composer

```tsx
// components/MessageComposer.tsx
import { useState } from 'react';
import { Pressable, TextInput, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function MessageComposer({ placeholder = 'Message #design', onSend }: { placeholder?: string; onSend: (text: string) => void }) {
  const [text, setText] = useState('');
  const canSend = text.trim().length > 0;

  return (
    <View style={{
      flexDirection: 'row', alignItems: 'flex-end', gap: 8,
      paddingHorizontal: 8, paddingVertical: 8,
      backgroundColor: colors.canvas,
      borderTopWidth: 1, borderTopColor: colors.divider,
    }}>
      <Pressable hitSlop={8} style={{ width: 40, height: 40, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="add" size={22} color={colors.textSecondary} />
      </Pressable>

      <TextInput
        value={text}
        onChangeText={setText}
        placeholder={placeholder}
        placeholderTextColor={colors.textSecondary}
        multiline
        style={{
          flex: 1, minHeight: 40, maxHeight: 160,
          paddingHorizontal: 12, paddingVertical: 10,
          borderRadius: 8,
          backgroundColor: colors.surface,
          fontFamily: 'SlackLato-Regular', fontSize: 15,
        }}
      />

      <Pressable
        disabled={!canSend}
        onPress={() => {
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
          onSend(text);
          setText('');
        }}
        style={{
          width: 32, height: 32, borderRadius: 4,
          alignItems: 'center', justifyContent: 'center',
          backgroundColor: canSend ? colors.onlineGreen : colors.textTertiary,
        }}
      >
        <Ionicons name="send" size={16} color="#FFF" />
      </Pressable>
    </View>
  );
}
```

## 4. Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:  colors.textPrimary,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: {
          backgroundColor: colors.canvas,
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'SlackLato-Semibold', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home-outline"          size={24} color={color} /> }} />
      <Tabs.Screen name="dms"      options={{ title: 'DMs',      tabBarIcon: ({ color }) => <Ionicons name="chatbubbles-outline"   size={24} color={color} /> }} />
      <Tabs.Screen name="activity" options={{ title: 'Activity', tabBarIcon: ({ color }) => <Ionicons name="notifications-outline" size={24} color={color} />, tabBarBadge: 3 }} />
      <Tabs.Screen name="later"    options={{ title: 'Later',    tabBarIcon: ({ color }) => <Ionicons name="bookmark-outline"      size={24} color={color} /> }} />
      <Tabs.Screen name="search"   options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search"                size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Sidebar Swipe (Edge-swipe to open)

```tsx
// Use expo-router Drawer + react-native-gesture-handler
import { Drawer } from 'expo-router/drawer';

export default function Layout() {
  return (
    <Drawer
      screenOptions={{
        drawerType: 'slide',
        drawerStyle: { width: 280, backgroundColor: 'transparent' },
      }}
    />
  );
}
```

Or use `react-native-reanimated` + `Gesture` from `react-native-gesture-handler`:

```tsx
const translateX = useSharedValue(-280);
const gesture = Gesture.Pan()
  .activeOffsetX([-10, 10])
  .onChange((e) => { translateX.value = Math.max(-280, Math.min(0, translateX.value + e.changeX)); })
  .onEnd(() => {
    translateX.value = withSpring(translateX.value > -140 ? 0 : -280, { damping: 14 });
  });
```

## 6. Motion

```tsx
// Message send
// Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
// Scale send button 1.0 → 0.9 → 1.0 (withSequence + withSpring)

// Reaction add
// scale.value = withSequence(withSpring(1.15), withSpring(1))
// Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft)

// Thread pane slide
// <Animated.View entering={SlideInRight.duration(250)} exiting={SlideOutRight.duration(200)} />

// Huddle join
// Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success)

// Typing indicator pulse
// const opacity = useSharedValue(1);
// opacity.value = withRepeat(withTiming(0.6, { duration: 600 }), -1, true);
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Slack's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Home (tab) | `home-outline` |
| DMs (tab) | `chatbubbles-outline` |
| Activity (tab) | `notifications-outline` |
| Later (tab) | `bookmark-outline` |
| Search (tab) | `search` |
| Channel (public) | text `#` |
| Channel (private) | `lock-closed` |
| Mute | `notifications-off-outline` |
| Huddle start | `headset` |
| Channel info | `information-circle-outline` |
| Send | `send` |
| Attach | `add` |
| Emoji picker | `happy-outline` |
| Mention | `at` |
| Voice note | `mic-outline` |
| Thread chevron | `chevron-forward` |
| Bookmark (Later) | `bookmark` |
| Star | `star` |

## 8. Platform Notes

- **Slack Lato**: proprietary; for prototypes, fall back to Lato (Apache 2.0) from Google Fonts — visually close. `.system` on iOS renders SF Pro which is technically crisper but loses Lato's warmth
- **Avatars are rounded-square** (4pt `borderRadius`), NOT circles — Slack's signature
- **Sidebar color customization**: accept a `sidebarColor` hex prop; compute luminance and adjust text color (light/dark) automatically for contrast
- **Status bar**: `<StatusBar style="light" />` when sidebar is open (aubergine is dark); `style="dark"` when in channel on light canvas
- **Safe area**: wrap screens in `SafeAreaView`; the sidebar and bottom tab both need safe-area padding
- **Dynamic Type**: React Native respects system scale; set `allowFontScaling={false}` on tab labels, section headers, reaction counts, timestamps
- **Keyboard**: `KeyboardAvoidingView` with `behavior="padding"` on iOS for the composer
- **Thread pane**: on iPhone, present as a full-screen navigated route via `expo-router`; on iPad, render as a side pane using a responsive layout
- **Workspace switcher**: if showing the vertical strip of workspace icons to the left of the sidebar, use a fixed 60pt-wide `View` with aubergine darker (`#3F0E40`) and the workspace icons as 40pt rounded-squares stacked vertically
- **RTL support**: flip sidebar to right side and thread pane to left when `I18nManager.isRTL`
