# Lemon8 (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Lemon8's visual language into paste-ready Expo / React Native code: a design-token module, the two-column masonry feed, the lifestyle card, the pastel tag system, and the post-detail article with Reanimated + Haptics.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-image`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light — core identity)
  canvas:        '#FFFFFF',
  surface1:      '#FAFAFA',
  surface2:      '#F2F2F2',
  divider:       '#ECECEC',

  // Surfaces (dark)
  darkCanvas:    '#121212',
  darkSurface1:  '#1C1C1E',
  darkSurface2:  '#2A2A2A',
  darkDivider:   '#2E2E2E',

  // Text
  ink:           '#1A1A1A',
  textSecondary: '#6B6B6B',
  textTertiary:  '#9A9A9A',
  textPrimaryDk: '#F2F2F2',

  // Brand (sparing — always with ink text)
  yellow:        '#FFE600',
  yellowPressed: '#E6CF00',

  // Semantic
  likeRed: '#FF2E63',
  success: '#1FB877',
  error:   '#FF3B5C',

  // Photo chip scrim
  scrim:   'rgba(0,0,0,0.55)',
} as const;

// Pastel topic tags: { bg, fg } pairs (light) + dark equivalents
export const tagPairs = {
  mint:     { bg: '#E4F5EC', fg: '#1F8A52', darkBg: '#1F3A2C', darkFg: '#6FD49E' },
  blush:    { bg: '#FCE8EE', fg: '#C13E68', darkBg: '#3A2630', darkFg: '#F09BB6' },
  sky:      { bg: '#E6F0FB', fg: '#2D6FB8', darkBg: '#1F2F42', darkFg: '#8FBEE8' },
  butter:   { bg: '#FFF6CC', fg: '#8A7300', darkBg: '#3A350F', darkFg: '#E6CF66' },
  lavender: { bg: '#F0EAFB', fg: '#7050B0', darkBg: '#2E263C', darkFg: '#C0A8E8' },
} as const;

export type TagKind = keyof typeof tagPairs;
```

## 2. Typography

Load **Poppins** (display/titles) and **Inter** (UI/body) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Poppins-ExtraBold': require('../assets/fonts/Poppins-ExtraBold.ttf'),
    'Poppins-Bold':      require('../assets/fonts/Poppins-Bold.ttf'),
    'Poppins-SemiBold':  require('../assets/fonts/Poppins-SemiBold.ttf'),
    'Inter-Regular':     require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':      require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':    require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':        require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  // Display — Poppins
  display:     { fontFamily: 'Poppins-ExtraBold', fontSize: 32, lineHeight: 38, letterSpacing: -0.5, color: '#1A1A1A' },
  screenTitle: { fontFamily: 'Poppins-Bold',      fontSize: 26, lineHeight: 33, letterSpacing: -0.3, color: '#1A1A1A' },
  tabHeader:   { fontFamily: 'Poppins-Bold',      fontSize: 22, lineHeight: 29, letterSpacing: -0.3, color: '#1A1A1A' },
  postTitle:   { fontFamily: 'Poppins-SemiBold',  fontSize: 18, lineHeight: 24, letterSpacing: -0.2, color: '#1A1A1A' },
  subheading:  { fontFamily: 'Poppins-SemiBold',  fontSize: 17, lineHeight: 23, letterSpacing: -0.1, color: '#1A1A1A' },

  // UI / body — Inter
  body:      { fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 25, color: '#1A1A1A' },
  cardTitle: { fontFamily: 'Inter-SemiBold', fontSize: 13, lineHeight: 18, color: '#1A1A1A' },
  button:    { fontFamily: 'Inter-Bold',     fontSize: 15, lineHeight: 15, color: '#1A1A1A' },
  meta:      { fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 18, color: '#6B6B6B' },
  cardByline:{ fontFamily: 'Inter-Medium',   fontSize: 11, lineHeight: 14, color: '#6B6B6B' },
  tag:       { fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 11, letterSpacing: 0.1 },
  tab:       { fontFamily: 'Inter-Medium',   fontSize: 10, lineHeight: 10, letterSpacing: 0.1, color: '#9A9A9A' },
  caption:   { fontFamily: 'Inter-Regular',  fontSize: 12, lineHeight: 16, color: '#6B6B6B' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Two-Column Masonry Feed

```tsx
// components/MasonryFeed.tsx
import { ScrollView, View } from 'react-native';
import { LifestyleCard } from './LifestyleCard';
import { colors } from '../theme/colors';

type Post = {
  id: string; aspect: number; gradient: [string, string, string];
  title: string; tag: { text: string; bg: string; fg: string };
  creator: string; likes: string; photoChip?: string;
};

const GAP = 10;

function splitColumns(posts: Post[]): [Post[], Post[]] {
  const left: Post[] = [], right: Post[] = [];
  let lh = 0, rh = 0;
  for (const p of posts) {
    const h = 1 / p.aspect + 1.4; // cover ratio + ~body
    if (lh <= rh) { left.push(p); lh += h; } else { right.push(p); rh += h; }
  }
  return [left, right];
}

export function MasonryFeed({ posts }: { posts: Post[] }) {
  const [left, right] = splitColumns(posts);
  return (
    <ScrollView style={{ backgroundColor: colors.canvas }} showsVerticalScrollIndicator={false}>
      <View style={{ flexDirection: 'row', gap: GAP, padding: 10 }}>
        <View style={{ flex: 1, gap: 12 }}>{left.map((p) => <LifestyleCard key={p.id} post={p} />)}</View>
        <View style={{ flex: 1, gap: 12 }}>{right.map((p) => <LifestyleCard key={p.id} post={p} />)}</View>
      </View>
    </ScrollView>
  );
}
```

### Lifestyle Card

```tsx
// components/LifestyleCard.tsx
import { View, Text, Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LifestyleCard({ post }: { post: any }) {
  return (
    <Pressable
      style={({ pressed }) => ({
        backgroundColor: colors.canvas,
        borderRadius: 14,
        overflow: 'hidden',
        transform: [{ scale: pressed ? 0.98 : 1 }],
        shadowColor: '#000', shadowOpacity: 0.06, shadowRadius: 3, shadowOffset: { width: 0, height: 1 },
        elevation: 2,
      })}
    >
      <View style={{ width: '100%', aspectRatio: post.aspect }}>
        <LinearGradient colors={post.gradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ flex: 1 }} />
        {post.photoChip && (
          <View style={{ position: 'absolute', left: 8, bottom: 8, backgroundColor: colors.scrim, borderRadius: 6, paddingVertical: 4, paddingHorizontal: 8 }}>
            <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 10, color: '#FFFFFF' }}>{post.photoChip}</Text>
          </View>
        )}
      </View>
      <View style={{ padding: 11, gap: 7 }}>
        <Text style={typography.cardTitle} numberOfLines={2}>{post.title}</Text>
        <View style={{ flexDirection: 'row', gap: 5, flexWrap: 'wrap' }}>
          <View style={{ backgroundColor: post.tag.bg, borderRadius: 5, paddingVertical: 3, paddingHorizontal: 7 }}>
            <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 9, color: post.tag.fg }}>{post.tag.text}</Text>
          </View>
        </View>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
          <LinearGradient colors={[colors.yellow, colors.likeRed]} style={{ width: 18, height: 18, borderRadius: 9 }} />
          <Text style={[typography.cardByline, { flex: 1 }]}>{post.creator}</Text>
          <Ionicons name="heart-outline" size={12} color={colors.textSecondary} />
          <Text style={typography.cardByline}>{post.likes}</Text>
        </View>
      </View>
    </Pressable>
  );
}
```

### Pastel Topic Tag

```tsx
// components/TopicTag.tsx
import { Text, View } from 'react-native';
import { tagPairs, type TagKind } from '../theme/colors';

export function TopicTag({ kind, text }: { kind: TagKind; text: string }) {
  const p = tagPairs[kind];
  return (
    <View style={{ backgroundColor: p.bg, borderRadius: 8, paddingVertical: 7, paddingHorizontal: 14 }}>
      <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.1, color: p.fg }}>{text}</Text>
    </View>
  );
}
```

### Top Segmented Tabs

```tsx
// components/TopTabs.tsx
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const TITLES = ['Following', 'For You', 'Nearby'];

export function TopTabs({ onChange }: { onChange?: (i: number) => void }) {
  const [sel, setSel] = useState(1);
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 22, paddingHorizontal: 18, paddingTop: 4, paddingBottom: 12 }}>
      {TITLES.map((t, i) => (
        <Pressable key={t} onPress={() => { setSel(i); onChange?.(i); }} style={{ alignItems: 'center', gap: 6 }}>
          <Text style={[typography.tabHeader, {
            color: sel === i ? colors.ink : colors.textTertiary,
            fontFamily: sel === i ? 'Poppins-Bold' : 'Poppins-SemiBold',
          }]}>
            {t}
          </Text>
          <View style={{ width: 20, height: 3, borderRadius: 2, backgroundColor: sel === i ? colors.yellow : 'transparent' }} />
        </Pressable>
      ))}
      <View style={{ flex: 1 }} />
      <Ionicons name="search" size={22} color={colors.ink} />
    </View>
  );
}
```

### Primary / Follow Buttons

```tsx
// components/Buttons.tsx
import { useState } from 'react';
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';

export function PrimaryButton({ title, onPress }: { title: string; onPress?: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.yellowPressed : colors.yellow,
        borderRadius: 999, paddingVertical: 13, paddingHorizontal: 28, alignSelf: 'flex-start',
      })}
    >
      <Text style={{ fontFamily: 'Inter-Bold', fontSize: 15, color: colors.ink }}>{title}</Text>
    </Pressable>
  );
}

export function FollowPill() {
  const [following, setFollowing] = useState(false);
  return (
    <Pressable
      onPress={() => setFollowing(!following)}
      style={{
        paddingVertical: 7, paddingHorizontal: 18, borderRadius: 999,
        backgroundColor: following ? 'transparent' : colors.yellow,
        borderWidth: following ? 1 : 0, borderColor: '#D8D8D8',
      }}
    >
      <Text style={{ fontFamily: 'Inter-Bold', fontSize: 12, color: following ? colors.textSecondary : colors.ink }}>
        {following ? 'Following' : 'Follow'}
      </Text>
    </Pressable>
  );
}
```

### Like Heart (animated)

```tsx
// components/LikeHeart.tsx
import { useState } from 'react';
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

const AIcon = Animated.createAnimatedComponent(Ionicons);

export function LikeHeart() {
  const [liked, setLiked] = useState(false);
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  const onPress = () => {
    const next = !liked;
    setLiked(next);
    if (next) {
      Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
      scale.value = withSequence(withSpring(1.2, { damping: 6 }), withSpring(1, { damping: 6 }));
    }
  };
  return (
    <Pressable onPress={onPress} hitSlop={12} style={{ width: 44, height: 44, alignItems: 'center', justifyContent: 'center' }}>
      <AIcon name={liked ? 'heart' : 'heart-outline'} size={24} color={liked ? colors.likeRed : colors.textSecondary} style={style} />
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

The center "Post" slot is a yellow rounded rectangle, not an icon-only tab.

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { View } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

function PostFab() {
  return (
    <View style={{ width: 42, height: 30, borderRadius: 9, backgroundColor: colors.yellow, alignItems: 'center', justifyContent: 'center' }}>
      <Ionicons name="add" size={20} color={colors.ink} />
    </View>
  );
}

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.ink,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
        tabBarBackground: () => <BlurView tint="light" intensity={90} style={{ flex: 1 }} />,
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home-outline" size={23} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="search" size={23} color={color} /> }} />
      <Tabs.Screen name="post"     options={{ title: '',         tabBarIcon: () => <PostFab /> }} />
      <Tabs.Screen name="inbox"    options={{ title: 'Inbox',    tabBarIcon: ({ color }) => <Ionicons name="chatbubble-outline" size={23} color={color} /> }} />
      <Tabs.Screen name="me"       options={{ title: 'Me',       tabBarIcon: ({ color }) => <Ionicons name="person-outline" size={23} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Masonry card press — transform scale 0.98 (Pressable style fn above)

// Card -> post detail: shared-element via react-navigation-shared-element OR
// reanimated SharedTransition — 300ms ease-out expand of the cover into the carousel

// Top tab underline: animate width/opacity or translate a shared underline View
// withTiming(targetX, { duration: 220 })

// Like heart burst
scale.value = withSequence(withSpring(1.2, { damping: 6 }), withSpring(1, { damping: 6 }));

// New masonry cards
import Animated, { FadeInDown } from 'react-native-reanimated';
// wrap card: <Animated.View entering={FadeInDown.duration(220)}>

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // like
Haptics.selectionAsync();                                // tab switch
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); // follow / save
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Home (tab) | `home-outline` / `home` |
| Discover (tab) | `search` |
| Post (tab, center) | `add` (ink on yellow rect) |
| Inbox (tab) | `chatbubble-outline` / `chatbubble` |
| Me (tab) | `person-outline` / `person` |
| Like (inactive) | `heart-outline` |
| Like (active) | `heart` (`#FF2E63`) |
| Save | `bookmark-outline` / `bookmark` |
| Share | `share-outline` |
| Comment | `chatbubble-ellipses-outline` |
| Search (top) | `search` |
| Back | `chevron-back` |
| Overflow | `ellipsis-horizontal` |

## 7. Platform Notes

- **Fonts**: Poppins, Inter are SIL OFL — free to bundle via `expo-font`
- **Images**: use `expo-image` `contentFit="cover"` for card covers; varied `aspectRatio` per card feeds the masonry
- **Status bar**: `<StatusBar style="dark" />` (Lemon8 is light-first); switch to `"light"` only in dark mode
- **Safe area**: tab bar is `position: absolute` with a `BlurView` — pad scroll content bottom; feed keeps ~10pt insets, article 16pt
- **Dynamic Type**: set `allowFontScaling={false}` on tags, card bylines, tab labels (layout-sensitive in the masonry); allow scaling on display/titles/body; recompute the column split when text scale changes
- **Masonry balancing**: re-run `splitColumns` on width / scale change (rotation, iPad split view); for very long feeds, consider `@shopify/flash-list` with a 2-column masonry layout
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` and the `darkBg`/`darkFg` tag variants; Lemon8 Yellow is constant and always pairs with ink — never white-on-yellow
- **Accessibility**: label cards "Post: {title}, by {creator}, {likes} likes"; like control as a toggle button with selected state; tags as "Topic: {name}"; the Post FAB as "Create post"
- **Reduce Motion**: gate the like spring and card->detail shared transition behind `AccessibilityInfo.isReduceMotionEnabled()` — fall back to crossfades; keep the tab underline as an instant move
