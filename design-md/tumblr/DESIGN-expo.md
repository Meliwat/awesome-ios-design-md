# Tumblr (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Tumblr's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#001935',   // deep navy — the default
  surface1: '#001020',
  surface2: '#0B2A45',
  surface3: '#13395B',
  divider:  '#36465D',

  textPrimary:   '#FFFFFF',
  textSecondary: '#8A9AAE',
  textTertiary:  '#5C6B80',

  blue:        '#00B8FF',
  bluePressed: '#0090CC',
  green:       '#00CF35',
  pinkRed:     '#FF4930',

  warning: '#FFB300',
} as const;

export type TumblrColor = keyof typeof colors;
```

## 2. Typography

Tumblr uses the system font (SF Pro on iOS); the web client renders Inter/Helvetica. Use `System`; for quote-type posts use a serif (load Georgia or use the platform serif).

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  titleLarge: { ...base, fontSize: 28, lineHeight: 34, fontWeight: '700' },
  quote:      { ...base, fontSize: 26, lineHeight: 34, fontWeight: '700',
                fontFamily: 'Georgia' },                                   // editorial quote posts
  section:    { ...base, fontSize: 20, lineHeight: 25, fontWeight: '700' },
  blogName:   { ...base, fontSize: 16, lineHeight: 20, fontWeight: '700' },
  body:       { ...base, fontSize: 16, lineHeight: 24, fontWeight: '400' },   // 1.5
  reblog:     { ...base, fontSize: 15, lineHeight: 22, fontWeight: '400' },
  bodySettings:{ ...base, fontSize: 15, lineHeight: 21, fontWeight: '400' },
  tag:        {           fontSize: 14, lineHeight: 18, fontWeight: '400', color: '#8A9AAE' },
  notes:      {           fontSize: 13, lineHeight: 17, fontWeight: '600', color: '#8A9AAE' },
  button:     {           fontSize: 16, lineHeight: 20, fontWeight: '700' },
  tab:        {           fontSize: 10, lineHeight: 12, fontWeight: '600' },
  reblogSrc:  {           fontSize: 14, lineHeight: 18, fontWeight: '700', color: '#00B8FF' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Dashboard Post Card (the core unit)

```tsx
// components/PostCard.tsx
import { ReactNode } from 'react';
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { FollowButton } from './FollowButton';
import { LikeButton } from './LikeButton';
import { ReblogButton } from './ReblogButton';
import { TagBar } from './TagBar';

export function PostCard({
  blogName, body, tags, notes, media,
}: { blogName: string; body?: string; tags: string[]; notes: number; media?: ReactNode }) {
  return (
    <View style={{ backgroundColor: colors.surface2, borderRadius: 8,
                   marginHorizontal: 8, marginBottom: 8, overflow: 'hidden' }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, padding: 12 }}>
        <View style={{ width: 36, height: 36, borderRadius: 6, backgroundColor: colors.surface3 }} />
        <Text style={typography.blogName}>{blogName}</Text>
        <View style={{ flex: 1 }} />
        <FollowButton />
        <Ionicons name="ellipsis-horizontal" size={22} color={colors.textSecondary} />
      </View>

      {media /* full-bleed inside the card, no letterboxing */}

      {body ? <Text style={[typography.body, { padding: 12 }]}>{body}</Text> : null}

      <TagBar tags={tags} />

      <View style={{ flexDirection: 'row', alignItems: 'center',
                     paddingHorizontal: 12, paddingBottom: 12, gap: 4 }}>
        <Text style={typography.notes}>{notes.toLocaleString()} notes</Text>
        <View style={{ flex: 1 }} />
        <Ionicons name="chatbubble-outline" size={22} color={colors.textSecondary} style={{ padding: 8 }} />
        <ReblogButton />
        <LikeButton />
        <Ionicons name="share-outline" size={22} color={colors.textSecondary} style={{ padding: 8 }} />
      </View>
    </View>
  );
}
```

### Like Button with Heart-Burst (the signature)

```tsx
// components/LikeButton.tsx
import { useState } from 'react';
import { Pressable, View } from 'react-native';
import Animated, {
  useSharedValue, useAnimatedStyle, withTiming, Easing, runOnJS,
} from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

const PARTICLES = 8;

export function LikeButton() {
  const [liked, setLiked] = useState(false);
  const t = useSharedValue(0);   // 0 → 1 burst progress

  const fire = () => {
    t.value = 0;
    t.value = withTiming(1, { duration: 600, easing: Easing.out(Easing.cubic) });
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
  };

  return (
    <Pressable
      hitSlop={12}
      style={{ padding: 8, minWidth: 44, alignItems: 'center', justifyContent: 'center' }}
      onPress={() => { const n = !liked; setLiked(n); if (n) fire(); }}
    >
      {Array.from({ length: PARTICLES }).map((_, i) => {
        const angle = (i / PARTICLES) * Math.PI * 2;
        // eslint-disable-next-line react-hooks/rules-of-hooks
        const style = useAnimatedStyle(() => ({
          position: 'absolute',
          opacity: 1 - t.value,
          transform: [
            { translateX: Math.cos(angle) * 26 * t.value },
            { translateY: Math.sin(angle) * 26 * t.value - 8 * t.value },
            { scale: 1 - 0.7 * t.value },
          ],
        }));
        return (
          <Animated.View key={i} style={style} pointerEvents="none">
            <Ionicons name="heart" size={10} color={colors.pinkRed} />
          </Animated.View>
        );
      })}
      <Ionicons name={liked ? 'heart' : 'heart-outline'} size={22}
        color={liked ? colors.pinkRed : colors.textSecondary} />
    </Pressable>
  );
}
```

### Reblog Button

```tsx
// components/ReblogButton.tsx
import { useState } from 'react';
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function ReblogButton() {
  const [reblogged, setReblogged] = useState(false);
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable hitSlop={12} style={{ padding: 8, minWidth: 44, alignItems: 'center' }}
      onPress={() => {
        setReblogged(!reblogged);
        scale.value = withSequence(withSpring(1.15), withSpring(1));
        Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
      }}>
      <Animated.View style={style}>
        <Ionicons name="repeat" size={22} color={reblogged ? colors.green : colors.textSecondary} />
      </Animated.View>
    </Pressable>
  );
}
```

### Reblog Chain (the signature structure)

```tsx
// components/ReblogChain.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type Segment = { sourceBlog: string; comment: string; depth: number }; // 0 = original

export function ReblogChain({ segments, maxDepth }: { segments: Segment[]; maxDepth: number }) {
  return (
    <View style={{ padding: 12, gap: 12 }}>
      {segments.map((seg, idx) => {
        const indent = (maxDepth - seg.depth) * 12;
        return (
          <View key={idx} style={{ flexDirection: 'row', paddingLeft: indent }}>
            {indent > 0 && (
              <View style={{ width: 2, backgroundColor: colors.divider, marginRight: 12 }} />
            )}
            <View style={{ flex: 1, gap: 4 }}>
              <Text style={typography.reblogSrc}>{seg.sourceBlog}</Text>
              <Text style={typography.reblog}>{seg.comment}</Text>
            </View>
          </View>
        );
      })}
    </View>
  );
}
```

### Buttons (Follow / Primary / FAB)

```tsx
import { useState } from 'react';
import { Pressable, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FollowButton() {
  const [following, setFollowing] = useState(false);
  return (
    <Pressable onPress={() => setFollowing(!following)}
      style={{ paddingVertical: 8, paddingHorizontal: 18, borderRadius: 6,
               backgroundColor: following ? colors.green : 'transparent',
               borderWidth: following ? 0 : 1.5, borderColor: colors.green }}>
      <Text style={[typography.button, { color: following ? colors.canvas : colors.green }]}>
        {following ? 'Following' : 'Follow'}
      </Text>
    </Pressable>
  );
}

export function TmblrPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.bluePressed : colors.blue,
        paddingVertical: 12, paddingHorizontal: 24, borderRadius: 6,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={[typography.button, { color: colors.canvas }]}>{title}</Text>
    </Pressable>
  );
}

export function NewPostFAB({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={({ pressed }) => ({
        width: 52, height: 52, borderRadius: 26, marginTop: -10,
        backgroundColor: pressed ? colors.bluePressed : colors.blue,
        alignItems: 'center', justifyContent: 'center',
        transform: [{ scale: pressed ? 0.94 : 1 }],
        shadowColor: colors.blue, shadowOpacity: 0.35, shadowRadius: 18, shadowOffset: { width: 0, height: 6 },
      })}>
      <Ionicons name="add" size={24} color={colors.canvas} />
    </Pressable>
  );
}
```

### Tag Bar

```tsx
// components/TagBar.tsx
import { Text, View } from 'react-native';
import { typography } from '../theme/typography';

export function TagBar({ tags }: { tags: string[] }) {
  return (
    <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 8,
                   paddingHorizontal: 12, paddingBottom: 8 }}>
      {tags.map((t) => <Text key={t} style={typography.tag}>#{t}</Text>)}
    </View>
  );
}
```

## 4. Distinctive System — Heart-Burst & Reblog Chain

The two souls of Tumblr are coded above: `LikeButton` scatters 8 heart particles on radial vectors over 0.6s driven by one shared `t` value (double-tap on media should call the same `fire()` centered on the image); `ReblogChain` renders progressively-indented attributed segments with a 2px `#36465D` left rule, blue source names, and the newest comment unindented at the bottom.

## 5. Tab Bar

Tumblr's tab bar is deep-navy opaque with a raised blue center New-post button. `expo-router` `Tabs` supports a custom center button via `tabBarButton`.

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';
import { NewPostFAB } from '../../components/NewPostFAB';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.blue,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: colors.surface1, borderTopColor: colors.divider, borderTopWidth: 0.5, height: 84 },
        tabBarLabelStyle: { fontSize: 10, fontWeight: '600' },
      }}
    >
      <Tabs.Screen name="index"  options={{ title: 'Home',   tabBarIcon: ({ color }) => <Ionicons name="home"   size={26} color={color} /> }} />
      <Tabs.Screen name="search" options={{ title: 'Search', tabBarIcon: ({ color }) => <Ionicons name="search" size={26} color={color} /> }} />
      <Tabs.Screen name="new"    options={{ title: '', tabBarButton: (p) => <NewPostFAB onPress={() => p.onPress?.()} /> }} />
      <Tabs.Screen name="activity" options={{ title: 'Activity', tabBarIcon: ({ color }) => <Ionicons name="notifications" size={26} color={color} /> }} />
      <Tabs.Screen name="account"  options={{ title: 'Account',  tabBarIcon: ({ color }) => <Ionicons name="person" size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Heart-burst: 8 hearts scatter on radial vectors, fade+shrink over 0.6s + light haptic (see LikeButton)
// Reblog: tint #00CF35 + 1 → 1.15 → 1 bounce + success haptic (see ReblogButton)

// Double-tap media: call the same fire() centered on the image (overlay an absolute LikeButton burst)

// FAB press: scale 0.94, #0090CC, soft haptic; post-type picker as a router modal

// Tag press: flash to #00B8FF then route to /tag/[name]
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Tumblr's SF Symbol equivalents:

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Reply | `bubble.left` | `chatbubble-outline` |
| Reblog | `arrow.2.squarepath` | `repeat` |
| Like | `heart` / `heart.fill` | `heart-outline` / `heart` |
| Share | `square.and.arrow.up` | `share-outline` |
| More | `ellipsis` | `ellipsis-horizontal` |
| New post (center FAB) | `plus` | `add` |
| Home (tab) | `house.fill` | `home` |
| Search (tab) | `magnifyingglass` | `search` |
| Activity (tab) | `bell.fill` | `notifications` |
| Account (tab) | `person.crop.square` | `person` |
| Messaging | `paperplane.fill` | `paper-plane` |
| Mature label | `exclamationmark.triangle.fill` | `warning` |

## 8. Platform Notes

- **Deep navy, dark default**: keep `colors.canvas` `#001935`; a light theme exists but is opt-in. Force `<StatusBar style="light" />` from `expo-status-bar` for the default navy experience.
- **Media full-bleed**: render GIFs/photosets inside the card with no padding and `overflow: 'hidden'` on the card so the 8pt radius clips them — no letterboxing
- **GIFs**: use `expo-image` (it animates GIFs/WebP) for the dashboard media — Tumblr is GIF-heavy
- **Safe area**: wrap screens in `SafeAreaView`; cards use 8px side margins, media bleeds within the card
- **Long-form**: React Native honors user font scaling — keep it on post body and quote posts; pin only tab labels and reblog-source minimums
- **Accessibility**: mark the burst particles `accessibilityElementsHidden`; expose like state ("Liked"); announce reblog-chain segments as "<source> reblogged: <comment>"; give the bright-blue primary button dark navy text for contrast
