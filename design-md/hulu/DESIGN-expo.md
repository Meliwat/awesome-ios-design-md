# Hulu (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Hulu's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:    '#0B0C0F',
  deepBlack: '#000000',
  surface1:  '#151619',
  surface2:  '#1E2024',
  surface3:  '#2A2D33',
  divider:   '#2A2D33',

  textPrimary:   '#FFFFFF',
  textSecondary: '#A0A4AB',
  textTertiary:  '#6B6F77',

  green:        '#1CE783',
  greenPressed: '#15B869',
  liveRed:      '#F0476A',
  errorRed:     '#F0476A',
} as const;

export type HuluColor = keyof typeof colors;
```

## 2. Typography

Load Graphik via `expo-font`. Fall back to Inter (or `System`, which is SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Graphik-Regular':  require('../assets/fonts/Graphik-Regular.ttf'),
    'Graphik-Semibold': require('../assets/fonts/Graphik-Semibold.ttf'),
    'Graphik-Bold':     require('../assets/fonts/Graphik-Bold.ttf'),
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
  heroTitle:      { ...base, fontFamily: 'Graphik-Bold',     fontSize: 32, lineHeight: 35, letterSpacing: -0.5 },
  titleLarge:     { ...base, fontFamily: 'Graphik-Bold',     fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  railHeader:     { ...base, fontFamily: 'Graphik-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  sectionHeader:  { ...base, fontFamily: 'Graphik-Bold',     fontSize: 20, lineHeight: 24, letterSpacing: -0.3 },
  episodeTitle:   { ...base, fontFamily: 'Graphik-Semibold', fontSize: 16, lineHeight: 20, letterSpacing: -0.1 },
  tileTitle:      { ...base, fontFamily: 'Graphik-Semibold', fontSize: 15, lineHeight: 19, letterSpacing: -0.1 },
  body:           { ...base, fontFamily: 'Graphik-Regular',  fontSize: 15, lineHeight: 22 },
  meta:           {           fontFamily: 'Graphik-Regular',  fontSize: 13, lineHeight: 17, color: '#A0A4AB' },
  tileSubtitle:   {           fontFamily: 'Graphik-Regular',  fontSize: 12, lineHeight: 16, color: '#A0A4AB' },
  labelUpper:     { ...base, fontFamily: 'Graphik-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.6, textTransform: 'uppercase' as const },
  button:         { color: '#0B0C0F', fontFamily: 'Graphik-Bold', fontSize: 16, lineHeight: 20, letterSpacing: 0.2 },
  buttonSecondary:{ ...base, fontFamily: 'Graphik-Semibold', fontSize: 15, lineHeight: 19 },
  tab:            {           fontFamily: 'Graphik-Semibold', fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
  badge:          { color: '#FFFFFF', fontFamily: 'Graphik-Bold', fontSize: 11, lineHeight: 13, letterSpacing: 0.4 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary Green Watch Button

```tsx
// components/WatchButton.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WatchButton({ title = 'Watch', onPress }: { title?: string; onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.97, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
        onPress();
      }}
    >
      <Animated.View
        style={[
          {
            height: 52, borderRadius: 8, backgroundColor: colors.green,
            flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8,
            shadowColor: colors.green, shadowOpacity: 0.30, shadowRadius: 24,
            shadowOffset: { width: 0, height: 8 },
          },
          style,
        ]}
      >
        <Ionicons name="play" size={18} color={colors.canvas} />
        <Text style={typography.button}>{title}</Text>
      </Animated.View>
    </Pressable>
  );
}
```

### Secondary Button (Details / Trailer)

```tsx
// components/SecondaryButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SecondaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        height: 52, borderRadius: 8,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? 'rgba(255,255,255,0.20)' : 'rgba(255,255,255,0.12)',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.buttonSecondary}>{title}</Text>
    </Pressable>
  );
}
```

### 16:9 Content Tile (with progress bar)

```tsx
// components/ContentTile.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ContentTile({
  title, subtitle, artworkUri, progress, width,
}: { title: string; subtitle: string; artworkUri: string; progress?: number; width: number }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withTiming(1.04, { duration: 180 }))}
      onPressOut={() => (scale.value = withTiming(1, { duration: 180 }))}
    >
      <Animated.View style={[{ width }, style]}>
        <View style={{ width, height: width * 9 / 16, borderRadius: 6, overflow: 'hidden' }}>
          <Image source={{ uri: artworkUri }} style={{ width: '100%', height: '100%' }} />
          {progress != null && (
            <View style={{ position: 'absolute', left: 2, right: 2, bottom: 2, height: 4, borderRadius: 2,
                           backgroundColor: 'rgba(255,255,255,0.25)', overflow: 'hidden' }}>
              <View style={{ height: '100%', width: `${progress * 100}%`, backgroundColor: colors.green }} />
            </View>
          )}
        </View>
        <Text style={[typography.tileTitle, { marginTop: 6 }]} numberOfLines={1}>{title}</Text>
        <Text style={typography.tileSubtitle} numberOfLines={1}>{subtitle}</Text>
      </Animated.View>
    </Pressable>
  );
}
```

### Details Hero (darkened backdrop + green CTA)

```tsx
// components/DetailsHero.tsx
import { Image, View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { WatchButton } from './WatchButton';

export function DetailsHero({
  title, metadata, backdropUri, onWatch,
}: { title: string; metadata: string; backdropUri: string; onWatch: () => void }) {
  return (
    <View style={{ backgroundColor: colors.canvas }}>
      <View style={{ height: 280 }}>
        <Image source={{ uri: backdropUri }} style={{ width: '100%', height: '100%' }} />
        <LinearGradient
          colors={['transparent', colors.canvas]}
          style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: '70%' }}
        />
        <View style={{ position: 'absolute', left: 16, bottom: 16, gap: 6 }}>
          <Text style={typography.heroTitle}>{title}</Text>
          <Text style={typography.meta}>{metadata}</Text>
        </View>
      </View>
      <View style={{ padding: 16, gap: 16 }}>
        <WatchButton onPress={onWatch} />
        <View style={{ flexDirection: 'row', justifyContent: 'space-around' }}>
          {[
            ['add', 'My Stuff'],
            ['arrow-down-circle-outline', 'Download'],
            ['share-outline', 'Share'],
          ].map(([icon, label]) => (
            <View key={label} style={{ alignItems: 'center', gap: 6, minWidth: 44 }}>
              <Ionicons name={icon as any} size={22} color={colors.textPrimary} />
              <Text style={typography.tileSubtitle}>{label}</Text>
            </View>
          ))}
        </View>
      </View>
    </View>
  );
}
```

## 4. Live Badge

```tsx
// components/LiveBadge.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LiveBadge() {
  const o = useSharedValue(1);
  useEffect(() => { o.value = withRepeat(withTiming(0.4, { duration: 700 }), -1, true); }, []);
  const dot = useAnimatedStyle(() => ({ opacity: o.value }));

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5, paddingHorizontal: 8,
                   paddingVertical: 4, borderRadius: 999, backgroundColor: colors.liveRed }}>
      <Animated.View style={[{ width: 6, height: 6, borderRadius: 3, backgroundColor: '#fff' }, dot]} />
      <Text style={typography.badge}>LIVE</Text>
    </View>
  );
}
```

## 5. Tab Bar

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
        tabBarActiveTintColor:  colors.green,     // green IS the indicator
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(11,12,15,0.6)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Graphik-Semibold', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home"        size={24} color={color} /> }} />
      <Tabs.Screen name="hubs"    options={{ title: 'Hubs',     tabBarIcon: ({ color }) => <Ionicons name="grid"        size={24} color={color} /> }} />
      <Tabs.Screen name="mystuff" options={{ title: 'My Stuff', tabBarIcon: ({ color }) => <Ionicons name="bookmark"    size={24} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search"      size={24} color={color} /> }} />
      <Tabs.Screen name="account" options={{ title: 'Account',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Tile press scale-up
scale.value = withTiming(1.04, { duration: 180 }); // onPressIn
scale.value = withTiming(1,    { duration: 180 }); // onPressOut

// Watch CTA tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Add to My Stuff
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Details enter: rows slide up 12pt + fade
// entering={FadeInDown.duration(250)} on each rail (react-native-reanimated layout animations)
```

## 7. Icon Library

Use `@expo/vector-icons` — ships Ionicons, Feather, MaterialCommunityIcons. Map to Hulu's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Play / Watch | `play` |
| Pause | `pause` |
| Skip ±10 | `play-forward` / `play-back` |
| Add to My Stuff | `add` / `checkmark` |
| Download | `arrow-down-circle-outline` / `arrow-down-circle` |
| Share | `share-outline` |
| Captions | `chatbox-outline` |
| AirPlay | `tv-outline` |
| Search | `search` |
| Home | `home-outline` / `home` |
| Hubs | `grid-outline` / `grid` |
| My Stuff | `bookmark-outline` / `bookmark` |
| Account | `person-circle-outline` / `person-circle` |

## 8. Platform Notes

- **iOS-only feel**: Use `expo-blur` for the tab bar to get the `.regularMaterial` equivalent. Android falls back to an opaque `#0B0C0F` background gracefully.
- **Status bar**: Set `<StatusBar style="light" />` from `expo-status-bar` globally — the near-black canvas requires light content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; let rails bleed to the right edge but inset content 16pt left
- **Dynamic Type**: React Native respects font scaling by default; set `allowFontScaling={false}` on tab labels, scrubber timestamps, and tile subtitles where dense layout breaks
- **Accessibility**: add `accessibilityRole="button"` + `accessibilityLabel` on tiles ("Watch The Bear, S3 E4, 24 min left"); expose progress via `accessibilityValue={{ now: pct, min: 0, max: 100 }}`
- **Reduce Motion**: gate the tile scale-up and LIVE pulse behind `AccessibilityInfo.isReduceMotionEnabled()`
