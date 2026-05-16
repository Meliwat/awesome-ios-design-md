# Max (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Max's visual language into paste-ready Expo / React Native code: a design-token module, the brand gradient, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:    '#12053A',
  deepBlack: '#000000',
  surface1:  '#1B0B4D',
  surface2:  '#250F5E',
  surface3:  '#36206E',
  divider:   '#36206E',

  textPrimary:   '#FFFFFF',
  textSecondary: '#B0A8D0',
  textTertiary:  '#7E76A6',

  gradientStart:   '#0046FF',
  gradientEnd:     '#7B2FF7',
  gradientSolid:   '#5A2BE0',
  gradientPressed: '#4A22BD',
  liveGold:        '#F2C94C',
  errorRed:        '#FF5C7A',
} as const;

// The Max brand gradient (use with expo-linear-gradient)
export const brandGradient = {
  colors: [colors.gradientStart, colors.gradientEnd] as const,
  start: { x: 0, y: 0 },
  end:   { x: 1, y: 1 },
};

export type MaxColor = keyof typeof colors;
```

## 2. Typography

Load Inter via `expo-font`. Fall back to `System` (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-ExtraBold': require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  billboardTitle: { ...base, fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 35, letterSpacing: -0.5 },
  titleLarge:     { ...base, fontFamily: 'Inter-ExtraBold', fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  rowHeader:      { ...base, fontFamily: 'Inter-ExtraBold', fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  sectionHeader:  { ...base, fontFamily: 'Inter-ExtraBold', fontSize: 20, lineHeight: 24, letterSpacing: -0.3 },
  episodeTitle:   { ...base, fontFamily: 'Inter-SemiBold',  fontSize: 16, lineHeight: 20, letterSpacing: -0.1 },
  tileTitle:      { ...base, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 19, letterSpacing: -0.1 },
  body:           { ...base, fontFamily: 'Inter-Regular',   fontSize: 15, lineHeight: 22 },
  meta:           {           fontFamily: 'Inter-Regular',   fontSize: 13, lineHeight: 17, color: '#B0A8D0' },
  tileSubtitle:   {           fontFamily: 'Inter-Regular',   fontSize: 12, lineHeight: 16, color: '#B0A8D0' },
  labelUpper:     { ...base, fontFamily: 'Inter-ExtraBold', fontSize: 11, lineHeight: 13, letterSpacing: 0.6, textTransform: 'uppercase' as const },
  button:         { ...base, fontFamily: 'Inter-ExtraBold', fontSize: 16, lineHeight: 20, letterSpacing: 0.2 },
  buttonSecondary:{ ...base, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 19 },
  tab:            {           fontFamily: 'Inter-SemiBold',  fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
  badge:          { ...base, fontFamily: 'Inter-ExtraBold', fontSize: 11, lineHeight: 13, letterSpacing: 0.4 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary Gradient Play Button (with shimmer)

```tsx
// components/PlayButton.tsx
import { useEffect } from 'react';
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, brandGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function PlayButton({ title = 'Play', onPress }: { title?: string; onPress: () => void }) {
  const scale = useSharedValue(1);
  const shimmer = useSharedValue(-220);
  useEffect(() => { shimmer.value = withRepeat(withTiming(220, { duration: 4000 }), -1, false); }, []);

  const scaleStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  const shimmerStyle = useAnimatedStyle(() => ({ transform: [{ translateX: shimmer.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.97, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
    >
      <Animated.View
        style={[
          { height: 52, borderRadius: 8, overflow: 'hidden',
            shadowColor: colors.gradientSolid, shadowOpacity: 0.35, shadowRadius: 24, shadowOffset: { width: 0, height: 8 } },
          scaleStyle,
        ]}
      >
        <LinearGradient {...brandGradient} style={{ ...StyleSheet.absoluteFillObject }} />
        <Animated.View
          style={[{ position: 'absolute', top: 0, bottom: 0, width: 80,
                    backgroundColor: 'rgba(255,255,255,0.18)' }, shimmerStyle]}
        />
        <View style={{ flex: 1, flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8 }}>
          <Ionicons name="play" size={18} color="#fff" />
          <Text style={typography.button}>{title}</Text>
        </View>
      </Animated.View>
    </Pressable>
  );
}
import { StyleSheet } from 'react-native';
```

### Secondary Button (More Info)

```tsx
// components/SecondaryButton.tsx
import { Pressable, Text } from 'react-native';
import { typography } from '../theme/typography';

export function SecondaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        height: 52, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? 'rgba(255,255,255,0.22)' : 'rgba(255,255,255,0.14)',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.buttonSecondary}>{title}</Text>
    </Pressable>
  );
}
```

### Max Originals Badge

```tsx
// components/OriginalsBadge.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, brandGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function OriginalsBadge() {
  return (
    <LinearGradient {...brandGradient} style={{ borderRadius: 999, padding: 1.5 }}>
      <View style={{ borderRadius: 999, backgroundColor: 'rgba(18,5,58,0.55)', paddingHorizontal: 8, paddingVertical: 4 }}>
        <Text style={typography.badge}>Max Originals</Text>
      </View>
    </LinearGradient>
  );
}
```

### Content Tile (with Originals badge)

```tsx
// components/ContentTile.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { OriginalsBadge } from './OriginalsBadge';

export function ContentTile({
  title, subtitle, artworkUri, isOriginal, width, aspect = 16 / 9,
}: { title: string; subtitle: string; artworkUri: string; isOriginal?: boolean; width: number; aspect?: number }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withTiming(1.04, { duration: 180 }))}
      onPressOut={() => (scale.value = withTiming(1, { duration: 180 }))}
    >
      <Animated.View style={[{ width }, style]}>
        <View style={{ width, height: width / aspect, borderRadius: 6, overflow: 'hidden' }}>
          <Image source={{ uri: artworkUri }} style={{ width: '100%', height: '100%' }} />
          {isOriginal && <View style={{ position: 'absolute', top: 8, left: 8 }}><OriginalsBadge /></View>}
        </View>
        <Text style={[typography.tileTitle, { marginTop: 6 }]} numberOfLines={1}>{title}</Text>
        <Text style={typography.tileSubtitle} numberOfLines={1}>{subtitle}</Text>
      </Animated.View>
    </Pressable>
  );
}
```

### Billboard (auto-trailer hero with crossfade)

```tsx
// components/Billboard.tsx
import { useEffect, useState } from 'react';
import { Image, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { PlayButton } from './PlayButton';
import { SecondaryButton } from './SecondaryButton';
import { OriginalsBadge } from './OriginalsBadge';

export function Billboard({ items }: { items: { title: string; meta: string; posterUri: string }[] }) {
  const [i, setI] = useState(0);
  useEffect(() => {
    const id = setInterval(() => setI((p) => (p + 1) % items.length), 20000);
    return () => clearInterval(id);
  }, [items.length]);
  const item = items[i];

  return (
    <View style={{ height: 460, backgroundColor: colors.canvas }}>
      <Animated.View key={i} entering={FadeIn.duration(1200)} exiting={FadeOut.duration(1200)}
                     style={{ ...StyleSheet.absoluteFillObject }}>
        <Image source={{ uri: item.posterUri }} style={{ width: '100%', height: '100%' }} />
      </Animated.View>
      <LinearGradient colors={['transparent', colors.canvas]}
                      style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: '60%' }} />
      <View style={{ position: 'absolute', left: 16, right: 16, bottom: 16, gap: 12 }}>
        <View style={{ alignSelf: 'flex-start' }}><OriginalsBadge /></View>
        <Text style={typography.billboardTitle}>{item.title}</Text>
        <Text style={typography.meta}>{item.meta}</Text>
        <View style={{ flexDirection: 'row', gap: 12 }}>
          <View style={{ flex: 1 }}><PlayButton onPress={() => {}} /></View>
          <View style={{ flex: 1 }}><SecondaryButton title="More Info" onPress={() => {}} /></View>
        </View>
      </View>
    </View>
  );
}
import { StyleSheet } from 'react-native';
```

## 4. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { View } from 'react-native';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:  colors.textPrimary,   // white; underline accent is the indicator
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(18,5,58,0.6)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"  options={{ title: 'Home',    tabBarIcon: ({ color, focused }) => <TabIcon name="home"        color={color} focused={focused} /> }} />
      <Tabs.Screen name="search" options={{ title: 'Search',  tabBarIcon: ({ color, focused }) => <TabIcon name="search"      color={color} focused={focused} /> }} />
      <Tabs.Screen name="mylist" options={{ title: 'My List', tabBarIcon: ({ color, focused }) => <TabIcon name="albums"      color={color} focused={focused} /> }} />
    </Tabs>
  );
}

function TabIcon({ name, color, focused }: { name: any; color: string; focused: boolean }) {
  return (
    <View style={{ alignItems: 'center', gap: 4 }}>
      <Ionicons name={name} size={24} color={color} />
      {focused && <View style={{ width: 18, height: 3, borderRadius: 2, backgroundColor: colors.gradientSolid }} />}
    </View>
  );
}
```

## 5. Motion

```tsx
// Tile press scale-up
scale.value = withTiming(1.04, { duration: 180 }); // onPressIn
scale.value = withTiming(1,    { duration: 180 }); // onPressOut

// Play CTA tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Add to My List
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Billboard crossfade — FadeIn/FadeOut.duration(1200) keyed on index, advanced every 20s
// CTA shimmer — withRepeat(withTiming(...), -1, false) translating a translucent bar (see §3)
```

## 6. Icon Library

Use `@expo/vector-icons` — ships Ionicons, Feather, MaterialCommunityIcons. Map to Max's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Play | `play` |
| Pause | `pause` |
| Skip ±10 | `play-forward` / `play-back` |
| Add to My List | `add` / `checkmark` |
| Download | `arrow-down-circle-outline` / `arrow-down-circle` |
| Share | `share-outline` |
| More Info | `information-circle-outline` |
| Captions | `chatbox-outline` |
| AirPlay | `tv-outline` |
| Search | `search` |
| Home | `home-outline` / `home` |
| My List | `albums-outline` / `albums` |

## 7. Platform Notes

- **iOS-only feel**: Use `expo-blur` for the tab bar to get the `.regularMaterial` equivalent. Android falls back to an opaque `#12053A` background.
- **Status bar**: Set `<StatusBar style="light" />` from `expo-status-bar` globally — the deep-purple canvas requires light content
- **Gradient**: `expo-linear-gradient` renders the brand sweep; for the badge "border" use a 1.5pt gradient padding wrapper around an inner purple-tinted view (no native gradient stroke)
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; rows bleed to the right edge, content insets 16pt left
- **Dynamic Type**: React Native respects font scaling by default; set `allowFontScaling={false}` on tab labels, scrubber timestamps, and tile subtitles where layout breaks
- **Accessibility**: add `accessibilityRole="button"` + `accessibilityLabel` on tiles ("Play Dune: Prophecy, Max Originals"); group the badge into the tile label
- **Reduce Motion**: gate the CTA shimmer and billboard crossfade behind `AccessibilityInfo.isReduceMotionEnabled()` (snap-cut the billboard instead)
