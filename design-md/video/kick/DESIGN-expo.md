# Kick (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Kick's visual language into paste-ready Expo / React Native code: a design-token module, the live chat panel, the watch page, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-image`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark-only; NOT pure black)
  canvas:   '#0E0E10',
  surface1: '#161618',
  surface2: '#1F1F23',
  surface3: '#2A2A2E',
  divider:  '#2D2D31',

  // Brand
  green:        '#53FC18',
  greenPressed: '#45D912',
  greenDeep:    '#00E701',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#A6A6AD',
  textTertiary:  '#6E6E76',
  chatBody:      '#E4E4E9',
  onGreen:       '#0E0E10',

  // Chat roles
  mod: '#3EA6FF',
  sub: '#FFC700',
  vip: '#FF4FD8',

  // Semantic
  live:  '#FF1F44',
  error: '#FF4A4A',
} as const;

export type KickColor = keyof typeof colors;

export type ChatRole =
  | { kind: 'mod' }
  | { kind: 'sub'; months: number }
  | { kind: 'vip' }
  | { kind: 'og' }
  | { kind: 'verified' }
  | { kind: 'regular'; color: string };

export function roleUserColor(r: ChatRole): string {
  switch (r.kind) {
    case 'mod': return colors.mod;
    case 'sub': return colors.sub;
    case 'vip': return colors.vip;
    case 'og':  return colors.green;
    case 'verified': return colors.textPrimary;
    case 'regular': return r.color;
  }
}

export function roleBadge(r: ChatRole): { text: string; bg: string; fg: string } | null {
  switch (r.kind) {
    case 'mod': return { text: 'MOD', bg: colors.mod, fg: '#06121F' };
    case 'sub': return { text: String(r.months), bg: colors.sub, fg: '#1A1500' };
    case 'vip': return { text: 'VIP', bg: colors.vip, fg: '#2A0022' };
    case 'og':  return { text: 'OG', bg: colors.green, fg: colors.onGreen };
    case 'verified': return { text: '✓', bg: colors.surface3, fg: '#FFFFFF' };
    case 'regular': return null;
  }
}
```

## 2. Typography

Kick's brand face is a tight modern grotesque; **Inter** at heavy weights is the closest free analog (SIL OFL). Load via `expo-font`. Use `fontVariant: ['tabular-nums']` on viewer counts.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':    require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':      require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold': require('../assets/fonts/Inter-ExtraBold.ttf'),
    'Inter-Black':     require('../assets/fonts/Inter-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const white = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  display:     { ...white, fontFamily: 'Inter-Black',     fontSize: 32, lineHeight: 34, letterSpacing: -0.5 },
  screenTitle: { ...white, fontFamily: 'Inter-ExtraBold', fontSize: 24, lineHeight: 28, letterSpacing: -0.3 },
  section:     { ...white, fontFamily: 'Inter-ExtraBold', fontSize: 20, lineHeight: 24, letterSpacing: -0.2 },
  streamer:    { ...white, fontFamily: 'Inter-Bold',      fontSize: 16, lineHeight: 19 },
  body:        { ...white, fontFamily: 'Inter-Regular',   fontSize: 15, lineHeight: 23 },
  cardTitle:   { ...white, fontFamily: 'Inter-Bold',      fontSize: 13, lineHeight: 16 },
  chatUser:    { fontFamily: 'Inter-Bold',    fontSize: 13, lineHeight: 18 },
  chatMsg:     { color: '#E4E4E9', fontFamily: 'Inter-Regular', fontSize: 13, lineHeight: 18 },
  meta:        { color: '#A6A6AD', fontFamily: 'Inter-Regular', fontSize: 13, lineHeight: 17 },
  button:      { color: '#0E0E10', fontFamily: 'Inter-ExtraBold', fontSize: 15, lineHeight: 15 },
  viewers:     { ...white, fontFamily: 'Inter-Bold', fontSize: 11, lineHeight: 11, fontVariant: ['tabular-nums'] },
  badge:       { ...white, fontFamily: 'Inter-ExtraBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.5 },
  tab:         { color: '#6E6E76', fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Watch Page (video + streamer bar + chat)

```tsx
// components/KickWatchPage.tsx
import { View } from 'react-native';
import { colors } from '../theme/colors';
import { StreamerBar } from './StreamerBar';
import { ChatPanel } from './ChatPanel';

export function KickWatchPage({ VideoLayer, streamer, category, viewers, messages }: {
  VideoLayer: React.ReactNode; streamer: string; category: string; viewers: string; messages: ChatMessage[];
}) {
  return (
    <View style={{ flex: 1, backgroundColor: colors.canvas }}>
      <View style={{ aspectRatio: 16 / 9, width: '100%', position: 'relative' }}>
        {VideoLayer}
        <View style={{ position: 'absolute', top: 12, left: 12, flexDirection: 'row',
          alignItems: 'center', gap: 6, backgroundColor: colors.live, paddingHorizontal: 9, paddingVertical: 4, borderRadius: 5 }}>
          <View style={{ width: 6, height: 6, borderRadius: 3, backgroundColor: '#FFF' }} />
          <Text style={typography.badge}>LIVE</Text>
        </View>
        <View style={{ position: 'absolute', top: 12, right: 12, flexDirection: 'row',
          alignItems: 'center', gap: 5, backgroundColor: 'rgba(0,0,0,0.55)', paddingHorizontal: 9, paddingVertical: 4, borderRadius: 5 }}>
          <Ionicons name="eye" size={11} color="#FFF" />
          <Text style={typography.viewers}>{viewers}</Text>
        </View>
      </View>
      <StreamerBar name={streamer} category={category} viewers={viewers} />
      <ChatPanel messages={messages} style={{ flex: 1 }} />
    </View>
  );
}
```

### Streamer Bar

```tsx
// components/StreamerBar.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Animated, { useAnimatedStyle, useSharedValue, withSequence, withTiming } from 'react-native-reanimated';
import { Ionicons } from '@expo/vector-icons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StreamerBar({ name, category, viewers }: { name: string; category: string; viewers: string }) {
  const [following, setFollowing] = useState(false);
  const scale = useSharedValue(1);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 14,
      paddingVertical: 12, borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      <View style={{ width: 40, height: 40, borderRadius: 20, backgroundColor: colors.green,
        borderWidth: 2, borderColor: colors.green }} />
      <View style={{ flex: 1, minWidth: 0 }}>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5 }}>
          <Text style={typography.streamer}>{name}</Text>
          <Ionicons name="checkmark-circle" size={13} color={colors.green} />
        </View>
        <Text numberOfLines={1} style={{ ...typography.meta, fontSize: 12, color: colors.textSecondary }}>
          {category} · {viewers} watching
        </Text>
      </View>
      <Animated.View style={aStyle}>
        <Pressable
          onPress={() => {
            Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
            setFollowing((f) => !f);
            scale.value = withSequence(withTiming(1.06, { duration: 120 }), withTiming(1, { duration: 120 }));
          }}
          style={{
            paddingHorizontal: 16, paddingVertical: 8, borderRadius: 6,
            backgroundColor: following ? colors.surface2 : colors.green,
            borderWidth: following ? 1 : 0, borderColor: colors.surface3,
          }}
        >
          <Text style={{ fontFamily: 'Inter-ExtraBold', fontSize: 12,
            color: following ? colors.textPrimary : colors.onGreen }}>
            {following ? 'Following' : 'Follow'}
          </Text>
        </Pressable>
      </Animated.View>
    </View>
  );
}
```

### Live Chat Panel + Message

```tsx
// components/ChatPanel.tsx
import { useRef } from 'react';
import { FlatList, Pressable, Text, TextInput, View, ViewStyle } from 'react-native';
import { Image } from 'expo-image';
import { Ionicons } from '@expo/vector-icons';
import { colors, roleBadge, roleUserColor, type ChatRole } from '../theme/colors';
import { typography } from '../theme/typography';

type Segment = { type: 'text'; value: string } | { type: 'emote'; uri: string };
export type ChatMessage = { id: string; role: ChatRole; username: string; segments: Segment[]; mentionsMe: boolean };

export function ChatPanel({ messages, style }: { messages: ChatMessage[]; style?: ViewStyle }) {
  const listRef = useRef<FlatList>(null);
  const draft = useRef('');

  return (
    <View style={[{ backgroundColor: colors.canvas }, style]}>
      <View style={{ flexDirection: 'row', alignItems: 'center', paddingHorizontal: 14, paddingTop: 10, paddingBottom: 8 }}>
        <Text style={{ fontFamily: 'Inter-ExtraBold', fontSize: 12, letterSpacing: 0.5, color: colors.textSecondary }}>
          STREAM CHAT
        </Text>
        <View style={{ flex: 1 }} />
        <Ionicons name="settings-outline" size={16} color={colors.textSecondary} />
      </View>

      <FlatList
        ref={listRef}
        data={messages}
        keyExtractor={(m) => m.id}
        showsVerticalScrollIndicator={false}
        contentContainerStyle={{ paddingHorizontal: 14, paddingVertical: 4 }}
        ItemSeparatorComponent={() => <View style={{ height: 9 }} />}
        onContentSizeChange={() => listRef.current?.scrollToEnd({ animated: true })}
        renderItem={({ item }) => <ChatRow message={item} />}
      />

      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, paddingHorizontal: 12,
        paddingTop: 10, paddingBottom: 12, borderTopWidth: 0.5, borderTopColor: colors.divider }}>
        <TextInput
          placeholder="Send a message"
          placeholderTextColor={colors.textTertiary}
          onChangeText={(t) => (draft.current = t)}
          style={{ flex: 1, height: 38, backgroundColor: colors.surface2, borderRadius: 8,
            paddingHorizontal: 12, color: '#FFF', fontFamily: 'Inter-Regular', fontSize: 13 }}
        />
        <Pressable style={{ width: 38, height: 38, borderRadius: 8, backgroundColor: colors.green,
          alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name="send" size={16} color={colors.onGreen} />
        </Pressable>
      </View>
    </View>
  );
}

function ChatRow({ message }: { message: ChatMessage }) {
  const badge = roleBadge(message.role);
  const userColor = roleUserColor(message.role);

  return (
    <View
      style={[
        { flexDirection: 'row', flexWrap: 'wrap', alignItems: 'center' },
        message.mentionsMe && {
          backgroundColor: 'rgba(83,252,24,0.12)', borderLeftWidth: 2, borderLeftColor: colors.green,
          paddingVertical: 4, paddingHorizontal: 6, borderRadius: 4,
        },
      ]}
    >
      {badge && (
        <View style={{ backgroundColor: badge.bg, borderRadius: 3, paddingHorizontal: 4, paddingVertical: 1, marginRight: 4 }}>
          <Text style={{ fontFamily: 'Inter-Black', fontSize: 9, color: badge.fg }}>{badge.text}</Text>
        </View>
      )}
      <Text style={[typography.chatUser, { color: userColor }]}>{message.username}</Text>
      <Text style={[typography.chatUser, { color: userColor }]}>: </Text>
      {message.segments.map((s, i) =>
        s.type === 'text' ? (
          <Text key={i} style={typography.chatMsg}>{s.value}</Text>
        ) : (
          <Image key={i} source={{ uri: s.uri }} style={{ width: 18, height: 18, borderRadius: 3, marginHorizontal: 1 }} />
        ),
      )}
    </View>
  );
}
```

### Live Channel Card

```tsx
// components/LiveCard.tsx
import { Text, View } from 'react-native';
import { Image } from 'expo-image';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LiveCard({ title, streamer, category, viewers, thumbUri, avatarUri }: {
  title: string; streamer: string; category: string; viewers: string; thumbUri: string; avatarUri: string;
}) {
  return (
    <View style={{ flex: 1 }}>
      <View style={{ aspectRatio: 16 / 9, borderRadius: 8, overflow: 'hidden' }}>
        <Image source={{ uri: thumbUri }} style={{ width: '100%', height: '100%' }} contentFit="cover" />
        <View style={{ position: 'absolute', top: 6, left: 6, backgroundColor: colors.live,
          borderRadius: 3, paddingHorizontal: 6, paddingVertical: 2 }}>
          <Text style={{ fontFamily: 'Inter-ExtraBold', fontSize: 8, color: '#FFF', letterSpacing: 0.4 }}>LIVE</Text>
        </View>
        <View style={{ position: 'absolute', bottom: 6, left: 6, backgroundColor: 'rgba(0,0,0,0.6)',
          borderRadius: 3, paddingHorizontal: 6, paddingVertical: 2 }}>
          <Text style={{ fontFamily: 'Inter-Bold', fontSize: 9, color: '#FFF' }}>{viewers}</Text>
        </View>
      </View>
      <View style={{ flexDirection: 'row', gap: 8, marginTop: 8 }}>
        <Image source={{ uri: avatarUri }} style={{ width: 30, height: 30, borderRadius: 15,
          borderWidth: 2, borderColor: colors.green }} />
        <View style={{ flex: 1 }}>
          <Text numberOfLines={1} style={typography.cardTitle}>{title}</Text>
          <Text style={{ fontFamily: 'Inter-Regular', fontSize: 11, color: colors.textSecondary }}>
            {streamer} · {category}
          </Text>
        </View>
      </View>
    </View>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.green,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(14,14,16,0.96)',
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Home',      tabBarIcon: ({ color }) => <Ionicons name="home"          size={22} color={color} /> }} />
      <Tabs.Screen name="browse"    options={{ title: 'Browse',    tabBarIcon: ({ color }) => <Ionicons name="search"        size={22} color={color} /> }} />
      <Tabs.Screen name="following" options={{ title: 'Following', tabBarIcon: ({ color }) => <Ionicons name="radio"         size={22} color={color} /> }} />
      <Tabs.Screen name="clips"     options={{ title: 'Clips',     tabBarIcon: ({ color }) => <Ionicons name="film"          size={22} color={color} /> }} />
      <Tabs.Screen name="profile"   options={{ title: 'Profile',   tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import * as Haptics from 'expo-haptics';
import Animated, { FadeIn } from 'react-native-reanimated';

// New chat message — fade + slide on append; FlatList auto-scrolls
// <Animated.View entering={FadeIn.duration(120)}> wrapping each row
// onContentSizeChange={() => listRef.current?.scrollToEnd({ animated: true })}  // unless scrolled up

// Follow → Following
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
scale.value = withSequence(withTiming(1.06, { duration: 120 }), withTiming(1, { duration: 120 }));

// Live pill subtle pulse
opacity.value = withRepeat(withTiming(0.6, { duration: 1500 }), -1, true);

// Player controls fade (auto-hide after 3s idle)
controlsOpacity.value = withTiming(visible ? 1 : 0, { duration: 200 });

// Stream join: cross-fade into watch page
// <Animated.View entering={FadeIn.duration(300)}>

// Card press
scale.value = withTiming(pressed ? 0.98 : 1, { duration: 120 });
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons primary).

| Purpose | Ionicons |
|---------|----------|
| Home | `home` / `home-outline` |
| Browse | `search` |
| Following | `radio` (or `people`) |
| Clips | `film` / `film-outline` |
| Profile | `person-circle` / `person-circle-outline` |
| Verified check | `checkmark-circle` |
| Live (eye) | `eye` |
| Play / pause | `play` / `pause` |
| Volume | `volume-high` / `volume-mute` |
| Fullscreen | `expand` |
| Chat send | `send` |
| Chat settings | `settings-outline` |
| Emote picker | `happy-outline` |
| Share / clip | `share-outline` / `cut-outline` |
| Back | `chevron-back` |
| Subscribe | `star` / `diamond` |

## 7. Platform Notes

- **Dark-only**: lock the app to dark — set `userInterfaceStyle: 'dark'` in `app.json`; never derive a light palette. `<StatusBar style="light" />` everywhere.
- **Canvas is `#0E0E10`, never `#000000`**: `#53FC18` on true black vibrates; keep the slight lift (it's an accessibility decision too).
- **Font choice**: Inter is SIL OFL — free to bundle. Ship Regular→Black; heavy weights carry the wordmark and titles.
- **Chat rendering**: render rows as `View` + `flexWrap` runs (badge chip = small `View`, emote = `expo-image` 18×18) — NOT a single concatenated string — so badges and emotes lay out correctly. Use a `FlatList` with `windowSize`, `removeClippedSubviews`, and cap retained messages (~200) for a fast chat.
- **Auto-scroll**: `onContentSizeChange → scrollToEnd`, but suppress it when the user has scrolled up (track via `onScroll`) and show a green "↓ More messages" pill instead.
- **Safe area**: wrap in `SafeAreaView`; the video bleeds under the status bar; the chat input dock + tab bar need bottom safe-area padding; use `KeyboardAvoidingView` so the chat input rises above the keyboard with the list scrolling above it.
- **Dynamic Type**: set `allowFontScaling={false}` on LIVE/role badges, viewer counts, and tab labels (layout-sensitive); leave it on for titles/body; chat text may scale to L.
- **Accessibility**: chat row `accessibilityLabel` must speak the role ("Moderator {username} says: {message}") — role color is never the only cue and is always paired with the badge chip; emotes get `accessibilityLabel` of the emote name; @-mention rows announce "Mentions you".
- **Reduce Motion**: gate the live-pill pulse and the chat slide-in behind `AccessibilityInfo.isReduceMotionEnabled`; keep auto-scroll but make it instant.
- **Reduce Transparency**: when enabled, replace the translucent tab bar with solid `#0E0E10`.
