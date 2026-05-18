# Substack (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Substack's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand & conversion — the ONE accent
  orange:        '#FF6719',
  orangePressed: '#E5560E',
  orangeTint:        '#FFF7F2',
  orangeTintBorder:  '#FFD9C2',
  orangeTintDark:        '#2A1C12',
  orangeTintBorderDark:  '#5C3A24',
  link:          '#4A6FE3',

  // Reading surface (light — "Reader")
  paper:       '#FFFFFF',
  readingInk:  '#1F1F1F',
  titleInk:    '#1A1A1A',
  subheadInk:  '#57534E',
  metaGrey:    '#8A8A8A',
  hairline:    '#ECECEC',

  // App surfaces (light)
  surfaceSubtle: '#F7F6F4',
  divider:       '#E7E5E1',

  // Dark
  darkCanvas:   '#121212',
  darkSurface1: '#1B1B1B',
  darkSurface2: '#262626',
  darkDivider:  '#2E2E2E',

  // Text
  textPrimary:    '#1A1A1A',
  textSecondary:  '#6B6B6B',
  darkTextPrimary:'#EDEDED',
  darkTextSecondary:'#A6A6A6',

  // Semantic
  error:   '#D93025',
  success: '#1A8917',
} as const;

export type SubstackColor = keyof typeof colors;
```

## 2. Typography

Substack sets everything-you-read in a reading serif (**Source Serif 4**; `Spectral` equal alternative) and everything-you-operate in **Inter**. Load both via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'SourceSerif4-Regular':  require('../assets/fonts/SourceSerif4-Regular.ttf'),
    'SourceSerif4-Italic':   require('../assets/fonts/SourceSerif4-Italic.ttf'),
    'SourceSerif4-SemiBold': require('../assets/fonts/SourceSerif4-SemiBold.ttf'),
    'SourceSerif4-SemiBoldItalic': require('../assets/fonts/SourceSerif4-SemiBoldItalic.ttf'),
    'SourceSerif4-Bold':     require('../assets/fonts/SourceSerif4-Bold.ttf'),
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  screenTitle: { fontFamily: 'SourceSerif4-Bold',   fontSize: 30, lineHeight: 36, letterSpacing: -0.4, color: '#1A1A1A' },
  postTitle:   { fontFamily: 'SourceSerif4-Bold',   fontSize: 26, lineHeight: 32, letterSpacing: -0.3, color: '#1A1A1A' },
  sectionHead: { fontFamily: 'SourceSerif4-Bold',   fontSize: 22, lineHeight: 29, color: '#1A1A1A' },
  pullQuote:   { fontFamily: 'SourceSerif4-SemiBoldItalic', fontSize: 21, lineHeight: 29, color: '#1A1A1A' },
  body:        { fontFamily: 'SourceSerif4-Regular', fontSize: 18, lineHeight: 30, color: '#1F1F1F' }, // ~1.65
  dek:         { fontFamily: 'SourceSerif4-Italic',  fontSize: 17, lineHeight: 24, color: '#57534E' },
  button:      { fontFamily: 'Inter-Bold',     fontSize: 15, lineHeight: 15, color: '#FFFFFF' },
  label:       { fontFamily: 'Inter-SemiBold', fontSize: 14, lineHeight: 18, color: '#1A1A1A' },
  meta:        { fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 18, color: '#8A8A8A' },
  caption:     { fontFamily: 'Inter-Regular',  fontSize: 12, lineHeight: 16, color: '#8A8A8A' },
  eyebrow:     { fontFamily: 'Inter-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.4, color: '#FF6719' },
  tab:         { fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 12, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Post Reader

```tsx
// components/PostReader.tsx
import { ScrollView, Text, View, useColorScheme } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Block = { type: 'text'; value: string } | { type: 'pull'; value: string };

export function PostReader({
  pubInitials, pubName, subscribers, title, dek, byline, blocks, readingSize = 18,
}: {
  pubInitials: string; pubName: string; subscribers: string;
  title: string; dek: string; byline: string; blocks: Block[]; readingSize?: number;
}) {
  const dark = useColorScheme() === 'dark';
  const ink = dark ? colors.darkTextPrimary : colors.titleInk;
  const bodyInk = dark ? colors.darkTextPrimary : colors.readingInk;
  return (
    <ScrollView
      style={{ flex: 1, backgroundColor: dark ? colors.darkCanvas : colors.paper }}
      contentContainerStyle={{ paddingHorizontal: 24, paddingVertical: 20 }}
      showsVerticalScrollIndicator={false}
    >
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, marginBottom: 18 }}>
        <LinearGradient colors={[colors.orange, colors.orangePressed]}
          start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ width: 34, height: 34, borderRadius: 6, alignItems: 'center', justifyContent: 'center' }}>
          <Text style={[typography.label, { color: '#FFF' }]}>{pubInitials}</Text>
        </LinearGradient>
        <View>
          <Text style={[typography.label, { color: ink }]}>{pubName}</Text>
          <Text style={[typography.caption]}>{subscribers}</Text>
        </View>
      </View>

      <Text style={[typography.postTitle, { color: ink, marginBottom: 8 }]}>{title}</Text>
      <Text style={[typography.dek, { marginBottom: 14 }]}>{dek}</Text>

      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, paddingBottom: 16 }}>
        <View style={{ width: 26, height: 26, borderRadius: 13, backgroundColor: colors.hairline }} />
        <Text style={typography.meta}>{byline}</Text>
      </View>
      <View style={{ height: 1, backgroundColor: dark ? colors.darkDivider : colors.hairline, marginBottom: 18 }} />

      {blocks.map((b, i) =>
        b.type === 'text' ? (
          <Text key={i} style={[typography.body, {
            color: bodyInk, fontSize: readingSize, lineHeight: readingSize * 1.65, marginBottom: 18,
          }]}>{b.value}</Text>
        ) : (
          <View key={i} style={{ borderLeftWidth: 3, borderLeftColor: colors.orange, paddingLeft: 18, marginBottom: 14 }}>
            <Text style={typography.pullQuote}>{b.value}</Text>
          </View>
        )
      )}
    </ScrollView>
  );
}
```

### Subscribe Card / Paywall Break

```tsx
// components/SubscribeCard.tsx
import { useState } from 'react';
import { Pressable, Text, View, useColorScheme } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SubscribeCard() {
  const [subscribed, setSubscribed] = useState(false);
  const dark = useColorScheme() === 'dark';
  const scale = useSharedValue(1);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <View style={{
      padding: 14, borderRadius: 10, alignItems: 'center',
      backgroundColor: dark ? colors.orangeTintDark : colors.orangeTint,
      borderWidth: 1, borderColor: dark ? colors.orangeTintBorderDark : colors.orangeTintBorder,
    }}>
      <Text style={{ fontFamily: 'SourceSerif4-Bold', fontSize: 15, color: dark ? colors.darkTextPrimary : colors.titleInk }}>
        Keep reading with a subscription
      </Text>
      <Text style={[typography.caption, { textAlign: 'center', marginTop: 4, marginBottom: 12 }]}>
        Get every post, the full archive, and the private community.
      </Text>
      <Pressable
        style={{ width: '100%' }}
        onPress={() => {
          scale.value = withSpring(0.97, { damping: 8 }, () => { scale.value = withSpring(1); });
          setSubscribed(true);
          Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
        }}
      >
        <Animated.View style={[{
          height: 42, borderRadius: 6, backgroundColor: colors.orange,
          alignItems: 'center', justifyContent: 'center',
        }, aStyle]}>
          <Text style={typography.button}>{subscribed ? 'Subscribed ✓' : 'Subscribe'}</Text>
        </Animated.View>
      </Pressable>
      <Text style={{ fontFamily: 'Inter-Regular', fontSize: 11, color: colors.metaGrey, marginTop: 8 }}>
        $8/month · $80/year · cancel anytime
      </Text>
    </View>
  );
}
```

### Pull-Quote

```tsx
// components/PullQuote.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PullQuote({ children }: { children: React.ReactNode }) {
  return (
    <View style={{ borderLeftWidth: 3, borderLeftColor: colors.orange, paddingLeft: 18, paddingVertical: 6 }}>
      <Text style={typography.pullQuote}>{children}</Text>
    </View>
  );
}
```

### Inbox Row

```tsx
// components/InboxRow.tsx
import { Pressable, Text, View, useColorScheme } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function InboxRow({ pubName, title, meta, unread, onPress }: {
  pubName: string; title: string; meta: string; unread?: boolean; onPress?: () => void;
}) {
  const dark = useColorScheme() === 'dark';
  return (
    <Pressable onPress={onPress} style={{
      flexDirection: 'row', gap: 12, paddingVertical: 14,
      borderBottomWidth: 1, borderBottomColor: dark ? colors.darkDivider : colors.divider,
    }}>
      <LinearGradient colors={[colors.orange, colors.orangePressed]}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ width: 56, height: 56, borderRadius: 6 }} />
      <View style={{ flex: 1, minWidth: 0 }}>
        <Text style={[typography.eyebrow]}>{pubName.toUpperCase()}</Text>
        <Text numberOfLines={2} style={{ fontFamily: 'SourceSerif4-Bold', fontSize: 15, lineHeight: 20,
          color: dark ? colors.darkTextPrimary : colors.textPrimary, marginVertical: 3 }}>{title}</Text>
        <Text style={[typography.caption, { color: dark ? colors.darkTextSecondary : colors.textSecondary }]}>{meta}</Text>
      </View>
      {unread ? <View style={{ width: 8, height: 8, borderRadius: 4, backgroundColor: colors.orange, marginTop: 6 }} /> : null}
    </Pressable>
  );
}
```

### Action Bar

```tsx
// components/PostActionBar.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PostActionBar({ initialLikes = 1200 }: { initialLikes?: number }) {
  const [liked, setLiked] = useState(false);
  const [count, setCount] = useState(initialLikes);
  const s = useSharedValue(1);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: s.value }] }));
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
      paddingHorizontal: 22, paddingVertical: 10 }}>
      <Pressable onPress={() => {
        const n = !liked; setLiked(n); setCount((c) => c + (n ? 1 : -1));
        s.value = withSpring(1.3, { damping: 5 }, () => { s.value = withSpring(1); });
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
      }} style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <Animated.View style={aStyle}>
          <Ionicons name={liked ? 'heart' : 'heart-outline'} size={18} color={liked ? colors.orange : colors.subheadInk} />
        </Animated.View>
        <Text style={[typography.meta, { color: liked ? colors.orange : colors.subheadInk }]}>{count}</Text>
      </Pressable>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <Ionicons name="chatbubble-outline" size={18} color={colors.subheadInk} />
        <Text style={typography.meta}>184</Text>
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <Ionicons name="repeat-outline" size={18} color={colors.subheadInk} />
        <Text style={typography.meta}>Restack</Text>
      </View>
      <Ionicons name="share-outline" size={18} color={colors.subheadInk} />
    </View>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useColorScheme } from 'react-native';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  const dark = useColorScheme() === 'dark';
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.orange,
        tabBarInactiveTintColor: dark ? colors.darkTextSecondary : '#9A9A9A',
        tabBarStyle: {
          backgroundColor: dark ? colors.darkCanvas : colors.paper,
          borderTopWidth: 0.5,
          borderTopColor: dark ? colors.darkDivider : colors.hairline,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Inbox',    tabBarIcon: ({ color }) => <Ionicons name="mail"               size={22} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="search"             size={22} color={color} /> }} />
      <Tabs.Screen name="notes"    options={{ title: 'Notes',    tabBarIcon: ({ color }) => <Ionicons name="reorder-three"      size={22} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle"      size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Subscribe success — press spring + crossfade label + success haptic
scale.value = withSpring(0.97, { damping: 8 }, () => { scale.value = withSpring(1); });
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Like — heart pop + soft haptic
s.value = withSpring(1.3, { damping: 5 }, () => { s.value = withSpring(1); });
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Paywall reveal — blur the locked text + slide the card up
// <BlurView intensity={locked ? 18 : 0} /> over the locked section;
// SubscribeCard entering={SlideInDown.duration(250)}

// Read-progress line — Animated width = scrollY / contentHeight, height 2, color orange, top edge

// Inbox → post — shared-element transition on the publication thumbnail
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Inbox | `mail` |
| Discover / Search | `search` |
| Notes | `reorder-three` |
| Profile | `person-circle` |
| Back | `chevron-back` |
| Share | `share-outline` |
| Overflow | `ellipsis-horizontal` |
| Like | `heart-outline` / `heart` |
| Comment | `chatbubble-outline` |
| Restack | `repeat-outline` |
| Save / Bookmark | `bookmark-outline` / `bookmark` |
| Mute publication | `notifications-off-outline` |
| Unread dot | `ellipse` (8px, orange) |
| Audio (listen) | `headset-outline` |
| Subscribed check | `checkmark` |

## 7. Platform Notes

- **Fonts**: Source Serif 4 (or Spectral) + Inter are both SIL OFL — free to bundle. Serif for everything-you-read; Inter for everything-you-operate
- **Reading rhythm**: body needs `lineHeight ≈ fontSize * 1.65` and a ~24pt horizontal inset; honor the user's in-app reading-size control as a multiplier on top of `fontSize`
- **Status bar**: `<StatusBar style="dark" />` on the white reading surface, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; the read-progress line hugs the top safe-area edge; the sticky Subscribe bar sits above the home indicator
- **Dynamic Type**: `<Text>` respects system scale — set `allowFontScaling={false}` only on tab labels, the publication eyebrow, captions, overlines
- **Dark mode**: `useColorScheme()` swaps to `darkCanvas` / `darkSurface1`; the orange `#FF6719` is constant in both modes (the one signal)
- **Comments are sans**: keep the comment composer and Notes in Inter — they are conversation, not the article; do not apply the serif body style there
- **Paywall blur**: use `expo-blur` `BlurView` over the locked section; keep the free preview crisp above the cut
- **Lists**: use `FlashList` for the Inbox and Notes feed — both can be very long
- **Accessibility**: label the Subscribe button "Subscribe, $8 per month"; inbox rows "{pubName}, {title}, {meta}, unread"; the like with state + count; publication logos with `accessibilityLabel={pubName}`
- **Cover/logo gradients on Android**: `LinearGradient` works cross-platform via `expo-linear-gradient`; thumbnails fall back to it when no publication art is set
