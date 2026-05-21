# Threads (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Threads' visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas (dark / default)
  canvas:        '#000000',
  surface1:      '#101010',
  surface2:      '#181818',
  divider:       '#222222',
  line:          '#333333',

  // Canvas (light)
  lightCanvas:   '#FFFFFF',
  lightSurface1: '#FAFAFA',
  lightSurface2: '#F5F5F5',
  lightDivider:  '#DBDBDB',
  lightLine:     '#D9D9D9',

  // Text
  textPrimaryDark:   '#F5F5F5',
  textPrimaryLight:  '#000000',
  textSecondary:     '#777777',
  textTertiaryDark:  '#4D4D4D',
  textTertiaryLight: '#999999',

  // Brand / action
  linkBlue:      '#2D7FF9',
  likeCoral:     '#FE2C55',
  errorRed:      '#ED4956',
  successGreen:  '#58C322',
  igVerified:    '#0095F6',
} as const;

export type ThreadsColor = keyof typeof colors;
```

## 2. Typography

Load Instagram Sans via `expo-font`. Fall back to `System` which is SF Pro on iOS.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';
import { Stack } from 'expo-router';

export default function Root() {
  const [loaded] = useFonts({
    'InstagramSans-Regular':  require('../assets/fonts/InstagramSans-Regular.ttf'),
    'InstagramSans-Semibold': require('../assets/fonts/InstagramSans-Semibold.ttf'),
    'InstagramSans-Bold':     require('../assets/fonts/InstagramSans-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#F5F5F5' } satisfies TextStyle;

export const typography = {
  screenTitle:   { ...base, fontFamily: 'InstagramSans-Bold',     fontSize: 17, lineHeight: 21 },
  displayName:   { ...base, fontFamily: 'InstagramSans-Semibold', fontSize: 15, lineHeight: 20 },
  postBody:      { ...base, fontFamily: 'InstagramSans-Regular',  fontSize: 15, lineHeight: 21 },  // ~1.4
  quotedBody:    { ...base, fontFamily: 'InstagramSans-Regular',  fontSize: 14, lineHeight: 19 },
  handle:        { fontFamily: 'InstagramSans-Regular',  fontSize: 14, lineHeight: 18, color: '#777777' },
  actionCount:   { fontFamily: 'InstagramSans-Regular',  fontSize: 13, lineHeight: 16, color: '#777777' },
  profileBio:    { ...base, fontFamily: 'InstagramSans-Regular',  fontSize: 15, lineHeight: 21 },
  button:        { fontFamily: 'InstagramSans-Semibold', fontSize: 15, lineHeight: 18 },
  secondaryBtn:  { fontFamily: 'InstagramSans-Semibold', fontSize: 14, lineHeight: 18 },
  composePH:     { fontFamily: 'InstagramSans-Regular',  fontSize: 17, lineHeight: 22, color: '#777777' },
  dmBody:        { ...base, fontFamily: 'InstagramSans-Regular',  fontSize: 15, lineHeight: 20 },
  filterChip:    { fontFamily: 'InstagramSans-Semibold', fontSize: 14, lineHeight: 17 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Thread Post Row (with thread line)

```tsx
// components/ThreadPostRow.tsx
import { Image, Pressable, StyleSheet, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  displayName: string;
  handle: string;
  timestamp: string;
  avatarUri: string;
  body: string;
  hasReplies?: boolean;
  likeCount?: number;
  commentCount?: number;
  isLiked?: boolean;
  isReposted?: boolean;
  onLike?: () => void;
  onComment?: () => void;
  onRepost?: () => void;
  onShare?: () => void;
};

export function ThreadPostRow(p: Props) {
  return (
    <View style={styles.row}>
      {/* Avatar + thread line */}
      <View style={{ alignItems: 'center' }}>
        <Image source={{ uri: p.avatarUri }} style={styles.avatar} />
        {p.hasReplies && <View style={styles.threadLine} />}
      </View>

      <View style={{ flex: 1, gap: 6 }}>
        {/* Header */}
        <View style={styles.header}>
          <Text style={typography.displayName} numberOfLines={1}>{p.displayName}</Text>
          <Text style={typography.handle} numberOfLines={1}>@{p.handle}</Text>
          <Text style={typography.handle}>·</Text>
          <Text style={typography.handle}>{p.timestamp}</Text>
          <View style={{ flex: 1 }} />
          <Pressable hitSlop={12}>
            <Ionicons name="ellipsis-horizontal" size={18} color={colors.textSecondary} />
          </Pressable>
        </View>

        <Text style={typography.postBody}>{p.body}</Text>

        <View style={styles.actions}>
          <ActionBtn icon={p.isLiked ? 'heart' : 'heart-outline'} count={p.likeCount}
                     tint={p.isLiked ? colors.likeCoral : colors.textSecondary} onPress={p.onLike} />
          <ActionBtn icon="chatbubble-outline" count={p.commentCount} tint={colors.textSecondary} onPress={p.onComment} />
          <ActionBtn icon={p.isReposted ? 'repeat' : 'repeat-outline'} tint={colors.textSecondary} onPress={p.onRepost} />
          <ActionBtn icon="paper-plane-outline" tint={colors.textSecondary} onPress={p.onShare} />
        </View>
      </View>
    </View>
  );
}

function ActionBtn({ icon, count, tint, onPress }: { icon: any; count?: number; tint: string; onPress?: () => void }) {
  return (
    <Pressable onPress={onPress} hitSlop={12} style={styles.action}>
      <Ionicons name={icon} size={22} color={tint} />
      {count != null && count > 0 && <Text style={[typography.actionCount, { color: tint }]}>{count}</Text>}
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row: {
    flexDirection: 'row',
    paddingHorizontal: 16,
    paddingTop: 16,
    paddingBottom: 12,
    gap: 12,
    borderBottomWidth: StyleSheet.hairlineWidth,
    borderBottomColor: colors.divider,
    backgroundColor: colors.canvas,
  },
  avatar:     { width: 36, height: 36, borderRadius: 18, backgroundColor: colors.surface2 },
  threadLine: { width: 1, flex: 1, backgroundColor: colors.line, marginTop: 4 },
  header:     { flexDirection: 'row', alignItems: 'center', gap: 4 },
  actions:    { flexDirection: 'row', alignItems: 'center', gap: 20, paddingTop: 4 },
  action:     { flexDirection: 'row', alignItems: 'center', gap: 6, minWidth: 44, minHeight: 44 },
});
```

### Primary Post Pill

```tsx
// components/PostPill.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PostPill({ title = 'Post', enabled, onPress }: { title?: string; enabled: boolean; onPress: () => void }) {
  return (
    <Pressable
      disabled={!enabled}
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
        onPress();
      }}
      style={({ pressed }) => ({
        paddingVertical: 8,
        paddingHorizontal: 20,
        borderRadius: 500,
        backgroundColor: colors.textPrimaryDark,
        opacity: enabled ? (pressed ? 0.8 : 1) : 0.3,
      })}
    >
      <Text style={[typography.button, { color: '#000' }]}>{title}</Text>
    </Pressable>
  );
}
```

### Follow Pill

```tsx
// components/FollowPill.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FollowPill({ isFollowing, onPress }: { isFollowing: boolean; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        paddingVertical: 8,
        paddingHorizontal: 20,
        borderRadius: 500,
        minWidth: 92,
        alignItems: 'center',
        backgroundColor: isFollowing ? 'transparent' : colors.textPrimaryDark,
        borderWidth: isFollowing ? 1 : 0,
        borderColor: colors.textSecondary,
        opacity: pressed ? 0.9 : 1,
      })}
    >
      <Text style={[typography.secondaryBtn, { color: isFollowing ? colors.textPrimaryDark : '#000' }]}>
        {isFollowing ? 'Following' : 'Follow'}
      </Text>
    </Pressable>
  );
}
```

### Activity Filter Chips

```tsx
// components/ActivityFilter.tsx
import { Pressable, ScrollView, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const CHIPS = ['All', 'Follows', 'Replies', 'Mentions', 'Quotes', 'Reposts', 'Verified'] as const;

export function ActivityFilter({ selected, onSelect }: { selected: string; onSelect: (v: string) => void }) {
  return (
    <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ gap: 8, paddingHorizontal: 16 }}>
      {CHIPS.map(chip => {
        const isOn = selected === chip;
        return (
          <Pressable
            key={chip}
            onPress={() => onSelect(chip)}
            style={{
              minHeight: 36,
              paddingVertical: 8,
              paddingHorizontal: 16,
              borderRadius: 500,
              backgroundColor: isOn ? colors.textPrimaryDark : 'transparent',
              borderWidth: isOn ? 0 : 1,
              borderColor: colors.line,
              justifyContent: 'center',
            }}
          >
            <Text style={[typography.filterChip, { color: isOn ? '#000' : colors.textPrimaryDark }]}>{chip}</Text>
          </Pressable>
        );
      })}
    </ScrollView>
  );
}
```

## 4. Threads-Specific Feature: Post Composer with Thread Line

```tsx
// app/compose.tsx  (expo-router modal)
import { KeyboardAvoidingView, Platform, Pressable, ScrollView, Text, TextInput, View } from 'react-native';
import { useState } from 'react';
import { router } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { PostPill } from '../components/PostPill';

export default function Compose() {
  const [drafts, setDrafts] = useState<string[]>(['']);
  const canPost = drafts.some(d => d.trim().length > 0);

  return (
    <KeyboardAvoidingView behavior={Platform.OS === 'ios' ? 'padding' : undefined} style={{ flex: 1, backgroundColor: colors.canvas }}>
      {/* Top bar */}
      <View style={{ flexDirection: 'row', alignItems: 'center', padding: 16 }}>
        <Pressable onPress={() => router.dismiss()}>
          <Text style={[typography.button, { color: colors.textPrimaryDark, fontWeight: '400' }]}>Cancel</Text>
        </Pressable>
        <View style={{ flex: 1 }} />
        <PostPill enabled={canPost} onPress={() => router.dismiss()} />
      </View>

      <ScrollView>
        {drafts.map((draft, i) => (
          <View key={i} style={{ flexDirection: 'row', paddingHorizontal: 16, paddingVertical: 12, gap: 12 }}>
            <View style={{ alignItems: 'center' }}>
              <View style={{ width: 36, height: 36, borderRadius: 18, backgroundColor: colors.surface2 }} />
              {(i < drafts.length - 1 || draft.length > 0) && (
                <View style={{ width: 1, flex: 1, backgroundColor: colors.line, marginTop: 4 }} />
              )}
            </View>
            <View style={{ flex: 1, gap: 4 }}>
              <Text style={typography.displayName}>you</Text>
              <TextInput
                placeholder={i === 0 ? 'Start a thread...' : 'Say more...'}
                placeholderTextColor={colors.textSecondary}
                multiline
                value={draft}
                onChangeText={t => {
                  const next = [...drafts]; next[i] = t; setDrafts(next);
                }}
                style={[typography.composePH, { color: colors.textPrimaryDark, paddingTop: 0 }]}
              />
            </View>
          </View>
        ))}

        {/* Add to thread */}
        <Pressable
          onPress={() => setDrafts([...drafts, ''])}
          style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingLeft: 24, paddingVertical: 12 }}
        >
          <View style={{ width: 20, height: 20, borderRadius: 10, borderWidth: 1, borderColor: colors.line, alignItems: 'center', justifyContent: 'center' }}>
            <Ionicons name="add" size={10} color={colors.textSecondary} />
          </View>
          <Text style={[typography.handle, { color: colors.textSecondary }]}>Add to thread</Text>
        </Pressable>
      </ScrollView>

      {/* Bottom toolbar */}
      <View style={{ flexDirection: 'row', gap: 20, paddingHorizontal: 24, paddingVertical: 12, borderTopWidth: 1, borderTopColor: colors.divider }}>
        <Ionicons name="image-outline"     size={22} color={colors.textSecondary} />
        <Ionicons name="videocam-outline"  size={22} color={colors.textSecondary} />
        <Ionicons name="mic-outline"       size={22} color={colors.textSecondary} />
        <Ionicons name="stats-chart-outline" size={22} color={colors.textSecondary} />
        <Ionicons name="location-outline"  size={22} color={colors.textSecondary} />
      </View>
    </KeyboardAvoidingView>
  );
}
```

Configure modal presentation in `app/_layout.tsx`:

```tsx
<Stack>
  <Stack.Screen name="(tabs)" />
  <Stack.Screen name="compose" options={{ presentation: 'modal', animation: 'slide_from_bottom' }} />
</Stack>
```

## 5. Tab Bar

Threads uses icon-only tabs with Compose as a dedicated tab (no floating FAB).

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.textPrimaryDark,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarShowLabel: false,
        tabBarStyle: {
          position: 'absolute',
          borderTopWidth: 0,
          backgroundColor: 'transparent',
          height: 49,
        },
        tabBarBackground: () => (
          <BlurView intensity={90} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(0,0,0,0.85)' }} />
        ),
      }}
    >
      <Tabs.Screen name="index"    options={{ tabBarIcon: ({ color }) => <Ionicons name="home"          size={26} color={color} /> }} />
      <Tabs.Screen name="search"   options={{ tabBarIcon: ({ color }) => <Ionicons name="search"        size={26} color={color} /> }} />
      <Tabs.Screen name="compose"  options={{ tabBarIcon: ({ color }) => <Ionicons name="add-circle-outline" size={30} color={color} /> }} />
      <Tabs.Screen name="activity" options={{ tabBarIcon: ({ color }) => <Ionicons name="heart"         size={26} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ tabBarIcon: ({ color }) => <Ionicons name="person"        size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Like tap — scale bounce + coral fill
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

function LikeButton({ isLiked, onToggle }: { isLiked: boolean; onToggle: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const press = () => {
    scale.value = withSequence(withSpring(0.85, { damping: 10 }), withSpring(1.15, { damping: 8 }), withSpring(1, { damping: 10 }));
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    onToggle();
  };
  // wrap heart in Animated.View with `style`
  return { press, style };
}

// Repost cross-fade (no color change)
// Swap icon name from 'repeat-outline' → 'repeat' with `Animated.View` opacity fade

// Compose open — full-screen modal via expo-router
import { router } from 'expo-router';
router.push('/compose');

// Pull-to-refresh — @-logo pulse
// Use react-native's RefreshControl with a custom indicator that tracks scroll offset via
// Animated.ScrollView + onScroll -> useSharedValue for `@` logo scale 1.0 → 1.15
```

## 7. Icon Library

Use `@expo/vector-icons` — ships with Ionicons. Map to Threads' SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Like (outline) | `heart-outline` |
| Like (filled) | `heart` |
| Comment | `chatbubble-outline` |
| Repost (outline) | `repeat-outline` |
| Repost (filled) | `repeat` |
| Share | `paper-plane-outline` |
| Overflow | `ellipsis-horizontal` |
| Home | `home-outline` / `home` |
| Search | `search` |
| Compose (tab) | `add-circle-outline` / `add-circle` |
| Activity | `heart-outline` / `heart` |
| Profile | `person-outline` / `person` |
| Verified (IG-inherited) | `checkmark-circle` (tinted `#0095F6`) |
| Add-to-thread plus | `add` (inside a circle border) |

## 8. Platform Notes

- **iOS-first feel**: `expo-blur` on the top nav + tab bar gives the translucent canvas effect; Android falls back to solid black `rgba(0,0,0,0.9)`
- **Status bar**: set `<StatusBar style="light" />` globally in dark mode — the canvas is pure black
- **Safe area**: wrap every screen in `SafeAreaView` from `react-native-safe-area-context`. Compose modal presents from bottom with safe-area handling
- **Dynamic Type**: React Native respects user font-scaling by default; set `allowFontScaling={false}` on action counts (13pt layout is tight) and tab icons
- **Accessibility**: add `accessibilityRole="button"` + descriptive labels ("Like post, 234 likes"). Group the post row as a single container element
- **Thread line rendering**: use `flex: 1` + `width: 1` on the line View to grow it vertically with content — works because the avatar column and content column share the same row height
- **Keyboard avoidance in compose**: use `KeyboardAvoidingView` with `behavior="padding"` on iOS — the toolbar needs to stay above the keyboard
- **Multi-post thread**: reorder handles and post-limit enforcement should be implemented as separate logic — the UI pattern above renders the thread visual correctly when state is an array
- **Instagram gradient (onboarding only)**: use `expo-linear-gradient` with `colors={['#F56040', '#A020F0', '#405DE6']}` and `start={{ x: 0, y: 0 }}` / `end={{ x: 1, y: 1 }}` for the Connect with Instagram CTA
