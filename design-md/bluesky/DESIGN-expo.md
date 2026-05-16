# Bluesky (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Bluesky's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, and `react-native-reanimated` v3.

> Bluesky's official app is itself a React Native app — this guide stays close to that grain.

## 1. Color Tokens

```ts
// theme/colors.ts
export const light = {
  canvas:   '#FFFFFF',
  surface1: '#F1F3F5',
  surface2: '#E2E8F0',
  divider:  '#E2E8F0',
  textPrimary:   '#0B0F14',
  textSecondary: '#697787',
  textTertiary:  '#8B98A5',
} as const;

export const dim = {
  canvas:   '#1E2936',
  surface1: '#27313E',
  surface2: '#2E4052',
  divider:  '#2E4052',
  textPrimary:   '#F1F3F5',
  textSecondary: '#9CA6B5',
  textTertiary:  '#697787',
} as const;

export const dark = {
  canvas:   '#0B0F14',
  surface1: '#161E27',
  surface2: '#1E2936',
  divider:  '#1E2936',
  textPrimary:   '#F1F3F5',
  textSecondary: '#8B98A5',
  textTertiary:  '#697787',
} as const;

export const brand = {
  blue:        '#1185FE',
  bluePressed: '#0F6FD6',
  like:        '#EC4899',
  repost:      '#2DBE85',
  error:       '#E5484D',
  warning:     '#F5A623',
} as const;
```

## 2. Typography

Bluesky uses the system font (SF Pro on iOS); the web client renders Inter. Load Inter via `expo-font` only for web parity.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  titleLarge:  { fontSize: 28, lineHeight: 34, fontWeight: '700' },
  section:     { fontSize: 21, lineHeight: 26, fontWeight: '700' },
  displayName: { fontSize: 15, lineHeight: 19, fontWeight: '600', letterSpacing: -0.1 },
  body:        { fontSize: 15, lineHeight: 21, fontWeight: '400' },   // 1.4 ratio
  handle:      { fontSize: 14, lineHeight: 18, fontWeight: '400' },
  feedTab:     { fontSize: 15, lineHeight: 18, fontWeight: '600', letterSpacing: -0.1 },
  count:       { fontSize: 13, lineHeight: 16, fontWeight: '500' },
  button:      { fontSize: 15, lineHeight: 18, fontWeight: '600', letterSpacing: -0.1 },
  buttonSec:   { fontSize: 14, lineHeight: 17, fontWeight: '600' },
  tab:         { fontSize: 10, lineHeight: 12, fontWeight: '600' },
  replyCtx:    { fontSize: 13, lineHeight: 17, fontWeight: '400' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Skeet Card (the core unit)

```tsx
// components/SkeetCard.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { light as c } from '../theme/colors';
import { typography } from '../theme/typography';
import { LikeButton } from './LikeButton';
import { RepostButton } from './RepostButton';

export function SkeetCard({
  displayName, handle, timestamp, body, avatarUri, replyContext,
}: {
  displayName: string; handle: string; timestamp: string;
  body: string; avatarUri: string; replyContext?: string;
}) {
  return (
    <View style={{ paddingVertical: 12, paddingHorizontal: 16,
                   borderBottomWidth: 1, borderBottomColor: c.divider }}>
      {replyContext && (
        <Text style={[typography.replyCtx, { color: c.textSecondary, marginBottom: 6 }]}>
          ↩ {replyContext}
        </Text>
      )}
      <View style={{ flexDirection: 'row', gap: 12 }}>
        <Image source={{ uri: avatarUri }} style={{ width: 42, height: 42, borderRadius: 21 }} />
        <View style={{ flex: 1 }}>
          <Text numberOfLines={1}>
            <Text style={[typography.displayName, { color: c.textPrimary }]}>{displayName} </Text>
            <Text style={[typography.handle, { color: c.textSecondary }]}>@{handle} · {timestamp}</Text>
          </Text>
        </View>
        <Ionicons name="ellipsis-horizontal" size={18} color={c.textSecondary} />
      </View>

      <Text style={[typography.body, { color: c.textPrimary, marginTop: 8 }]}>{body}</Text>

      <View style={{ flexDirection: 'row', justifyContent: 'space-between',
                     marginTop: 8, paddingRight: 16 }}>
        <Action icon="chatbubble-outline" count={8} />
        <RepostButton count={14} />
        <LikeButton count={142} />
        <Action icon="share-outline" count={0} />
      </View>
    </View>
  );
}

function Action({ icon, count }: { icon: any; count: number }) {
  return (
    <Pressable hitSlop={12} style={{ flexDirection: 'row', alignItems: 'center', gap: 6, minWidth: 44 }}>
      <Ionicons name={icon} size={18} color="#697787" />
      {count > 0 && <Text style={[typography.count, { color: '#697787' }]}>{count}</Text>}
    </Pressable>
  );
}
```

### Like Button (the pop-scale signature)

```tsx
// components/LikeButton.tsx
import { useState } from 'react';
import { Pressable, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function LikeButton({ count }: { count: number }) {
  const [liked, setLiked] = useState(false);
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      hitSlop={12}
      style={{ flexDirection: 'row', alignItems: 'center', gap: 6, minWidth: 44 }}
      onPress={() => {
        setLiked(!liked);
        scale.value = withSequence(
          withSpring(1.25, { damping: 6 }),
          withSpring(1, { damping: 12 }),
        );
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
      }}
    >
      <Animated.View style={style}>
        <Ionicons name={liked ? 'heart' : 'heart-outline'} size={18}
          color={liked ? brand.like : '#697787'} />
      </Animated.View>
      <Text style={[typography.count, { color: liked ? brand.like : '#697787' }]}>
        {liked ? count + 1 : count}
      </Text>
    </Pressable>
  );
}
```

### Repost Button

```tsx
// components/RepostButton.tsx
import { useState } from 'react';
import { Pressable, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function RepostButton({ count }: { count: number }) {
  const [reposted, setReposted] = useState(false);
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      hitSlop={12}
      style={{ flexDirection: 'row', alignItems: 'center', gap: 6, minWidth: 44 }}
      onPress={() => {
        setReposted(!reposted);
        scale.value = withSequence(withSpring(1.1), withSpring(1));
        Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
      }}
    >
      <Animated.View style={style}>
        <Ionicons name="repeat" size={18} color={reposted ? brand.repost : '#697787'} />
      </Animated.View>
      <Text style={[typography.count, { color: reposted ? brand.repost : '#697787' }]}>
        {reposted ? count + 1 : count}
      </Text>
    </Pressable>
  );
}
```

### Primary Button & Compose FAB

```tsx
import { Pressable, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function BskyPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? brand.bluePressed : brand.blue,
        paddingVertical: 10, paddingHorizontal: 20, borderRadius: 500,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}>
      <Text style={[typography.button, { color: '#fff' }]}>{title}</Text>
    </Pressable>
  );
}

export function ComposeFAB({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={({ pressed }) => ({
        position: 'absolute', right: 16, bottom: 16,
        width: 56, height: 56, borderRadius: 28,
        backgroundColor: pressed ? brand.bluePressed : brand.blue,
        alignItems: 'center', justifyContent: 'center',
        transform: [{ scale: pressed ? 0.94 : 1 }],
        shadowColor: brand.blue, shadowOpacity: 0.35, shadowRadius: 20, shadowOffset: { width: 0, height: 6 },
      })}>
      <Ionicons name="create-outline" size={24} color="#fff" />
    </Pressable>
  );
}
```

## 4. Distinctive System — Pinned Custom-Feed Selector

```tsx
// components/FeedSelector.tsx
import { ScrollView, Pressable, Text, View } from 'react-native';
import { brand, light as c } from '../theme/colors';
import { typography } from '../theme/typography';

export function FeedSelector({
  feeds, selected, onSelect,
}: { feeds: string[]; selected: string; onSelect: (f: string) => void }) {
  return (
    <ScrollView horizontal showsHorizontalScrollIndicator={false}
      contentContainerStyle={{ paddingHorizontal: 16, gap: 20 }}>
      {feeds.map((feed) => {
        const active = feed === selected;
        return (
          <Pressable key={feed} onPress={() => onSelect(feed)} style={{ alignItems: 'center', paddingTop: 12 }}>
            <Text style={[typography.feedTab, { color: active ? brand.blue : c.textSecondary }]}>
              {feed}
            </Text>
            <View style={{ height: 3, marginTop: 8, alignSelf: 'stretch', borderRadius: 2,
                           backgroundColor: active ? brand.blue : 'transparent' }} />
          </Pressable>
        );
      })}
    </ScrollView>
  );
}
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { brand, light as c } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: brand.blue,
        tabBarInactiveTintColor: c.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="light" style={{ flex: 1, backgroundColor: 'rgba(255,255,255,0.6)' }} />
        ),
        tabBarLabelStyle: { fontSize: 10, fontWeight: '600' },
      }}
    >
      <Tabs.Screen name="index"         options={{ title: 'Home',          tabBarIcon: ({ color }) => <Ionicons name="home"      size={26} color={color} /> }} />
      <Tabs.Screen name="search"        options={{ title: 'Search',        tabBarIcon: ({ color }) => <Ionicons name="search"    size={26} color={color} /> }} />
      <Tabs.Screen name="notifications" options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Ionicons name="notifications" size={26} color={color} /> }} />
      <Tabs.Screen name="chat"          options={{ title: 'Chat',          tabBarIcon: ({ color }) => <Ionicons name="chatbubbles" size={26} color={color} /> }} />
      <Tabs.Screen name="profile"       options={{ title: 'Profile',       tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Like: heart fill #EC4899 + pop-scale 1 → 1.25 → 1 via withSequence + light haptic (see LikeButton)
// Repost: tint #2DBE85 + 1 → 1.1 → 1 bounce + success haptic (see RepostButton)

// Feed switch: cross-fade + small horizontal nudge
const x = useSharedValue(0);
x.value = withSequence(withSpring(6), withSpring(0));

// Compose FAB: scale 0.94 on press + soft haptic; present compose as a router modal

// Theme switch: animate a shared progress 0→1 and interpolateColor across surfaces over 200ms
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Bluesky's SF Symbol equivalents:

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Reply | `bubble.left` | `chatbubble-outline` |
| Repost | `arrow.2.squarepath` | `repeat` |
| Like | `heart` / `heart.fill` | `heart-outline` / `heart` |
| Share | `square.and.arrow.up` | `share-outline` |
| More | `ellipsis` | `ellipsis-horizontal` |
| Reply context | `arrow.turn.up.left` | `arrow-undo-outline` |
| Compose (FAB) | `square.and.pencil` | `create-outline` |
| Home (tab) | `house.fill` | `home` |
| Search (tab) | `magnifyingglass` | `search` |
| Notifications (tab) | `bell.fill` | `notifications` |
| Chat (tab) | `message.fill` | `chatbubbles` |
| Profile (tab) | `person.crop.circle` | `person-circle` |
| Feed settings | `slider.horizontal.3` | `options-outline` |

## 8. Platform Notes

- **iOS-only feel**: use `expo-blur` for the tab bar's `.regularMaterial` equivalent (light/dark tint per theme). Android falls back to a 94%-opaque solid surface.
- **Status bar**: drive `<StatusBar style="auto" />` from `expo-status-bar` so it flips per theme — dark content on Light, light content on Dim/Dark
- **Triple theme**: detect with `useColorScheme()` plus an app-level override (Light/Dim/Dark) persisted in storage; swap the `light`/`dim`/`dark` token objects and `interpolateColor` surfaces over 200ms
- **Safe area**: wrap screens in `SafeAreaView`; the profile banner goes edge-to-edge while content keeps 16px insets
- **Conversational sizing**: React Native honors user font scaling — keep it on skeet body and names; pin only tab labels
- **Accessibility**: add `accessibilityRole="button"` + state labels ("Liked", "Reposted"); expose the feed selector as a tab list with the active feed announced
