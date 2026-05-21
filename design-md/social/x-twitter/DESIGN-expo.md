# X (Twitter) (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates X's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas (dark / default)
  canvas:      '#000000',
  surface1:    '#16181C',
  surface2:    '#1E2024',
  divider:     '#2F3336',

  // Dim mode
  dimCanvas:   '#15202B',
  dimSurface1: '#192734',
  dimDivider:  '#38444D',

  // Light mode
  lightCanvas:   '#FFFFFF',
  lightSurface1: '#F7F9F9',
  lightSurface2: '#EFF3F4',

  // Text
  textPrimaryDark:    '#E7E9EA',
  textPrimaryLight:   '#0F1419',
  textSecondaryDark:  '#71767B',
  textSecondaryLight: '#536471',

  // Brand / action
  blue:         '#1D9BF0',
  bluePressed:  '#1A8CD8',
  repostGreen:  '#00BA7C',
  likePink:     '#F91880',
  verifiedGold: '#EAB308',
  verifiedGray: '#829AAB',
  errorRed:     '#F4212E',
} as const;

export type XColor = keyof typeof colors;
```

## 2. Typography

Load Chirp via `expo-font`. Fall back to `System` which is SF Pro on iOS.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';
import { Stack } from 'expo-router';

export default function Root() {
  const [loaded] = useFonts({
    'Chirp-Regular':  require('../assets/fonts/Chirp-Regular.ttf'),
    'Chirp-Medium':   require('../assets/fonts/Chirp-Medium.ttf'),
    'Chirp-Bold':     require('../assets/fonts/Chirp-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#E7E9EA' } satisfies TextStyle;

export const typography = {
  screenTitle:   { ...base, fontFamily: 'Chirp-Bold',    fontSize: 20, lineHeight: 24, letterSpacing: -0.2 },
  sectionHeader: { ...base, fontFamily: 'Chirp-Bold',    fontSize: 17, lineHeight: 21 },
  displayName:   { ...base, fontFamily: 'Chirp-Bold',    fontSize: 15, lineHeight: 20 },
  postBody:      { ...base, fontFamily: 'Chirp-Regular', fontSize: 15, lineHeight: 20 },
  quotedBody:    { ...base, fontFamily: 'Chirp-Regular', fontSize: 14, lineHeight: 18 },
  handle:        { fontFamily: 'Chirp-Regular', fontSize: 15, lineHeight: 20, color: '#71767B' },
  actionCount:   { fontFamily: 'Chirp-Regular', fontSize: 13, lineHeight: 16, color: '#71767B' },
  trendingTopic: { ...base, fontFamily: 'Chirp-Bold',    fontSize: 15, lineHeight: 19 },
  trendingMeta:  { fontFamily: 'Chirp-Regular', fontSize: 13, lineHeight: 16, color: '#71767B' },
  button:        { fontFamily: 'Chirp-Bold',    fontSize: 15, lineHeight: 20 },
  dmBody:        { ...base, fontFamily: 'Chirp-Regular', fontSize: 15, lineHeight: 20 },
  dmTimestamp:   { fontFamily: 'Chirp-Regular', fontSize: 11, lineHeight: 13, color: '#71767B' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Post Row

```tsx
// components/PostRow.tsx
import { Image, Pressable, StyleSheet, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  displayName: string;
  handle: string;
  timestamp: string;
  isVerified?: boolean;
  avatarUri: string;
  body: string;
  replyCount?: number;
  repostCount?: number;
  likeCount?: number;
  viewCount?: number;
  isLiked?: boolean;
  isReposted?: boolean;
  onLike?: () => void;
  onRepost?: () => void;
  onReply?: () => void;
};

export function PostRow(props: Props) {
  const fmt = (n = 0) =>
    n >= 1_000_000 ? `${(n/1_000_000).toFixed(1)}M`
    : n >= 1_000   ? `${(n/1_000).toFixed(1)}K`
    : n > 0        ? `${n}` : '';

  return (
    <View style={styles.row}>
      <Image source={{ uri: props.avatarUri }} style={styles.avatar} />
      <View style={{ flex: 1, gap: 4 }}>
        <View style={styles.header}>
          <Text style={typography.displayName} numberOfLines={1}>{props.displayName}</Text>
          {props.isVerified && <Ionicons name="checkmark-circle" size={16} color={colors.blue} />}
          <Text style={typography.handle} numberOfLines={1}>@{props.handle}</Text>
          <Text style={typography.handle}>·</Text>
          <Text style={typography.handle}>{props.timestamp}</Text>
          <View style={{ flex: 1 }} />
          <Pressable hitSlop={12}>
            <Ionicons name="ellipsis-horizontal" size={18} color={colors.textSecondaryDark} />
          </Pressable>
        </View>

        <Text style={typography.postBody}>{props.body}</Text>

        <View style={styles.actions}>
          <ActionIcon icon="chatbubble-outline"  count={fmt(props.replyCount)}  color={colors.textSecondaryDark} onPress={props.onReply} />
          <ActionIcon icon="repeat"              count={fmt(props.repostCount)} color={props.isReposted ? colors.repostGreen : colors.textSecondaryDark} onPress={props.onRepost} />
          <ActionIcon icon={props.isLiked ? 'heart' : 'heart-outline'} count={fmt(props.likeCount)} color={props.isLiked ? colors.likePink : colors.textSecondaryDark} onPress={props.onLike} />
          <ActionIcon icon="bar-chart-outline"   count={fmt(props.viewCount)}   color={colors.textSecondaryDark} />
          <ActionIcon icon="share-outline"       color={colors.textSecondaryDark} />
        </View>
      </View>
    </View>
  );
}

function ActionIcon({ icon, count, color, onPress }: { icon: any; count?: string; color: string; onPress?: () => void }) {
  return (
    <Pressable onPress={onPress} hitSlop={12} style={styles.action}>
      <Ionicons name={icon} size={18} color={color} />
      {count ? <Text style={[typography.actionCount, { color }]}>{count}</Text> : null}
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row:     { flexDirection: 'row', paddingHorizontal: 16, paddingVertical: 12, gap: 12,
             borderBottomWidth: StyleSheet.hairlineWidth, borderBottomColor: colors.divider,
             backgroundColor: colors.canvas },
  avatar:  { width: 32, height: 32, borderRadius: 16, backgroundColor: colors.surface1 },
  header:  { flexDirection: 'row', alignItems: 'center', gap: 4 },
  actions: { flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingTop: 8, paddingRight: 16 },
  action:  { flexDirection: 'row', alignItems: 'center', gap: 4, minWidth: 44, minHeight: 44 },
});
```

### Floating Post Button (FAB)

```tsx
// components/PostFAB.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function PostFAB({ onPress, dark = true }: { onPress: () => void; dark?: boolean }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.95, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
        onPress();
      }}
      style={{ position: 'absolute', right: 16, bottom: 90 }}
    >
      <Animated.View
        style={[
          {
            width: 56, height: 56, borderRadius: 28,
            backgroundColor: dark ? '#FFFFFF' : colors.textPrimaryLight,
            alignItems: 'center', justifyContent: 'center',
            shadowColor: '#000', shadowOpacity: 0.4, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
          },
          style,
        ]}
      >
        <Ionicons name="create" size={24} color={dark ? '#000' : '#FFF'} />
      </Animated.View>
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
        paddingHorizontal: 16,
        borderRadius: 500,
        minWidth: 80,
        alignItems: 'center',
        backgroundColor: isFollowing ? 'transparent' : colors.textPrimaryDark,
        borderWidth: isFollowing ? 1 : 0,
        borderColor: colors.textSecondaryDark,
        opacity: pressed ? 0.9 : 1,
      })}
    >
      <Text style={[typography.button, { color: isFollowing ? colors.textPrimaryDark : '#000' }]}>
        {isFollowing ? 'Following' : 'Follow'}
      </Text>
    </Pressable>
  );
}
```

### Feed Filter ("For you" / "Following")

```tsx
// components/FeedFilter.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FeedFilter({ selection, onSelect }: { selection: 0 | 1; onSelect: (i: 0 | 1) => void }) {
  const x = useSharedValue(selection === 0 ? 0 : 1);
  const indicatorStyle = useAnimatedStyle(() => ({
    left: `${x.value * 50 + 25 - 5}%` as any,
  }));

  return (
    <View style={{ height: 48, flexDirection: 'row', borderBottomWidth: 1, borderBottomColor: colors.divider }}>
      {(['For you', 'Following'] as const).map((t, i) => (
        <Pressable
          key={t}
          onPress={() => {
            x.value = withSpring(i, { damping: 18 });
            onSelect(i as 0 | 1);
          }}
          style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}
        >
          <Text style={[typography.button, { color: selection === i ? colors.textPrimaryDark : colors.textSecondaryDark }]}>
            {t}
          </Text>
        </Pressable>
      ))}
      <Animated.View
        style={[{ position: 'absolute', bottom: 0, width: 40, height: 4, borderRadius: 2, backgroundColor: colors.blue }, indicatorStyle]}
      />
    </View>
  );
}
```

## 4. X-Specific Feature: Timeline Row with Like Burst

```tsx
// components/LikeBurst.tsx
import { View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

const PARTICLE_COUNT = 6;

export function LikeButton({ isLiked, count, onToggle }: { isLiked: boolean; count: number; onToggle: () => void }) {
  const scale = useSharedValue(1);
  const burst = useSharedValue(0);

  const heartStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const onPress = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    scale.value = withSequence(withSpring(0.85, { damping: 10 }), withSpring(1.2, { damping: 8 }), withSpring(1, { damping: 10 }));
    burst.value = 0;
    burst.value = withTiming(1, { duration: 400 });
    onToggle();
  };

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, minWidth: 44, minHeight: 44 }}>
      <Animated.View style={heartStyle}>
        <Ionicons
          name={isLiked ? 'heart' : 'heart-outline'}
          size={18}
          color={isLiked ? colors.likePink : colors.textSecondaryDark}
          onPress={onPress}
        />
      </Animated.View>
      {Array.from({ length: PARTICLE_COUNT }).map((_, i) => {
        const angle = (i * Math.PI * 2) / PARTICLE_COUNT;
        const particleStyle = useAnimatedStyle(() => ({
          opacity: 1 - burst.value,
          transform: [
            { translateX: Math.cos(angle) * 18 * burst.value },
            { translateY: Math.sin(angle) * 18 * burst.value },
          ],
        }));
        return (
          <Animated.View
            key={i}
            style={[
              { position: 'absolute', width: 4, height: 4, borderRadius: 2, backgroundColor: colors.likePink, left: 7, top: 7 },
              particleStyle,
            ]}
          />
        );
      })}
      {count > 0 && (
        <Animated.Text style={{ fontFamily: 'Chirp-Regular', fontSize: 13, color: isLiked ? colors.likePink : colors.textSecondaryDark }}>
          {count >= 1000 ? `${(count/1000).toFixed(1)}K` : count}
        </Animated.Text>
      )}
    </View>
  );
}
```

## 5. Tab Bar

X uses icon-only tabs — no labels, ever.

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
        tabBarInactiveTintColor: colors.textSecondaryDark,
        tabBarShowLabel: false, // X is icon-only
        tabBarStyle: {
          position: 'absolute',
          borderTopWidth: 0,
          backgroundColor: 'transparent',
          height: 49,
        },
        tabBarBackground: () => (
          <BlurView intensity={90} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(0,0,0,0.7)' }} />
        ),
      }}
    >
      <Tabs.Screen name="index"         options={{ tabBarIcon: ({ color }) => <Ionicons name="home"     size={26} color={color} /> }} />
      <Tabs.Screen name="search"        options={{ tabBarIcon: ({ color }) => <Ionicons name="search"   size={26} color={color} /> }} />
      <Tabs.Screen name="communities"   options={{ tabBarIcon: ({ color }) => <Ionicons name="people"   size={26} color={color} /> }} />
      <Tabs.Screen name="notifications" options={{ tabBarIcon: ({ color }) => <Ionicons name="notifications" size={26} color={color} /> }} />
      <Tabs.Screen name="messages"      options={{ tabBarIcon: ({ color }) => <Ionicons name="mail"     size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Like tap (see LikeButton above for the full burst + haptic)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Repost icon spin
const rot = useSharedValue(0);
const repostStyle = useAnimatedStyle(() => ({ transform: [{ rotate: `${rot.value}deg` }] }));
const onRepost = () => {
  rot.value = withTiming(rot.value + 360, { duration: 400 });
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
};

// Compose modal open
// Use expo-router modal presentation:
// router.push('/compose')  — in app/_layout.tsx set `presentation: 'modal'`
```

## 7. Icon Library

Use `@expo/vector-icons` — ships with Ionicons. Map to X's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Reply | `chatbubble-outline` |
| Repost | `repeat` |
| Like (outline) | `heart-outline` |
| Like (filled) | `heart` |
| Views | `bar-chart-outline` |
| Bookmark (outline) | `bookmark-outline` |
| Bookmark (filled) | `bookmark` |
| Share | `share-outline` |
| Verified | `checkmark-circle` |
| Overflow | `ellipsis-horizontal` |
| Compose (FAB) | `create` |
| Home | `home` |
| Search | `search` |
| Communities | `people` |
| Notifications | `notifications` |
| Messages | `mail` |
| Grok | `sparkles` |

## 8. Platform Notes

- **iOS-first feel**: `expo-blur` on the top nav + tab bar gives the translucent canvas effect; Android falls back to a solid color close to `rgba(0,0,0,0.9)`
- **Status bar**: set `<StatusBar style="light" />` globally — the pure-black canvas requires light content
- **Safe area**: wrap every screen in `SafeAreaView` from `react-native-safe-area-context`. FAB floats 16pt above bottom safe inset + tab bar height
- **Dynamic Type**: React Native respects user font-scaling by default; set `allowFontScaling={false}` on action counts (13pt layout is tight) and tab icons
- **Accessibility**: add `accessibilityRole="button"` + descriptive labels ("Like this post, 1,234 likes") on every action icon. Group the post row as a container element for VoiceOver navigation
- **Pure black canvas**: iOS OLED displays benefit from `#000000` (true black) — don't substitute `#111`. Set `<StatusBar translucent />` when using modal sheets so the canvas extends behind the status bar
- **Keyboard avoidance**: use `KeyboardAvoidingView` with `behavior="padding"` on the compose modal to keep the toolbar visible above the keyboard
