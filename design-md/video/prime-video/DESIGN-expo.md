# Prime Video (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Prime Video's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-blur`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:    '#0F171E',
  deepBlack: '#000000',
  surface1:  '#1A242F',
  surface2:  '#232F3E',
  surface3:  '#2E3B47',
  divider:   '#2E3B47',

  textPrimary:   '#FFFFFF',
  textSecondary: '#AAB7C4',
  textTertiary:  '#6E7B89',

  blue:        '#00A8E1',
  bluePressed: '#008CBD',
  imdbYellow:  '#F5C518',
  liveRed:     '#E50914',
  errorRed:    '#E50914',
} as const;

export type PrimeColor = keyof typeof colors;
```

## 2. Typography

Load Amazon Ember via `expo-font`. Fall back to Inter (or `System`, SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'AmazonEmber-Regular': require('../assets/fonts/AmazonEmber-Regular.ttf'),
    'AmazonEmber-Medium':  require('../assets/fonts/AmazonEmber-Medium.ttf'),
    'AmazonEmber-Bold':    require('../assets/fonts/AmazonEmber-Bold.ttf'),
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
  detailsTitle:   { ...base, fontFamily: 'AmazonEmber-Bold',    fontSize: 30, lineHeight: 33, letterSpacing: -0.4 },
  titleLarge:     { ...base, fontFamily: 'AmazonEmber-Bold',    fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  rowHeader:      { ...base, fontFamily: 'AmazonEmber-Bold',    fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  sectionHeader:  { ...base, fontFamily: 'AmazonEmber-Bold',    fontSize: 20, lineHeight: 24, letterSpacing: -0.3 },
  episodeTitle:   { ...base, fontFamily: 'AmazonEmber-Medium',  fontSize: 16, lineHeight: 20, letterSpacing: -0.1 },
  tileTitle:      { ...base, fontFamily: 'AmazonEmber-Medium',  fontSize: 15, lineHeight: 19, letterSpacing: -0.1 },
  body:           { ...base, fontFamily: 'AmazonEmber-Regular', fontSize: 15, lineHeight: 22 },
  meta:           {           fontFamily: 'AmazonEmber-Regular', fontSize: 13, lineHeight: 17, color: '#AAB7C4' },
  tileSubtitle:   {           fontFamily: 'AmazonEmber-Regular', fontSize: 12, lineHeight: 16, color: '#AAB7C4' },
  labelUpper:     { ...base, fontFamily: 'AmazonEmber-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.6, textTransform: 'uppercase' as const },
  button:         { color: '#0F171E', fontFamily: 'AmazonEmber-Bold', fontSize: 16, lineHeight: 20, letterSpacing: 0.2 },
  buttonSecondary:{ ...base, fontFamily: 'AmazonEmber-Medium',  fontSize: 15, lineHeight: 19 },
  tab:            {           fontFamily: 'AmazonEmber-Medium',  fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
  badge:          { ...base, fontFamily: 'AmazonEmber-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.4 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary Blue Play Button

```tsx
// components/PlayButton.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PlayButton({ title = 'Play', onPress }: { title?: string; onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.97, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
    >
      <Animated.View
        style={[
          { height: 52, borderRadius: 8, backgroundColor: colors.blue,
            flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8,
            shadowColor: colors.blue, shadowOpacity: 0.32, shadowRadius: 24, shadowOffset: { width: 0, height: 8 } },
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

### Watchlist Toggle

```tsx
// components/WatchlistButton.tsx
import { useEffect } from 'react';
import { Pressable, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WatchlistButton({ added, onToggle }: { added: boolean; onToggle: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  useEffect(() => {
    if (added) scale.value = withSequence(withSpring(1.15), withSpring(1));
  }, [added]);

  return (
    <Pressable
      onPress={() => {
        Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
        onToggle();
      }}
      style={({ pressed }) => ({
        backgroundColor: pressed ? 'rgba(255,255,255,0.22)' : 'rgba(255,255,255,0.14)',
      })}
    >
      <Animated.View
        style={[
          { height: 52, borderRadius: 8, flexDirection: 'row', alignItems: 'center',
            justifyContent: 'center', gap: 8 },
          style,
        ]}
      >
        <Ionicons name={added ? 'checkmark' : 'add'} size={16} color={added ? colors.blue : '#fff'} />
        <Text style={[typography.buttonSecondary, added && { color: colors.blue }]}>
          {added ? 'Watchlisted' : 'Watchlist'}
        </Text>
      </Animated.View>
    </Pressable>
  );
}
```

### Content Tile (2:3 poster with Prime ribbon)

```tsx
// components/ContentTile.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ContentTile({
  title, artworkUri, includedWithPrime, progress, width, aspect = 2 / 3,
}: { title: string; artworkUri: string; includedWithPrime?: boolean; progress?: number; width: number; aspect?: number }) {
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
          {progress != null && (
            <View style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: 3,
                           backgroundColor: 'rgba(255,255,255,0.25)' }}>
              <View style={{ height: '100%', width: `${progress * 100}%`, backgroundColor: colors.blue }} />
            </View>
          )}
        </View>
        <Text style={[typography.tileTitle, { marginTop: 6 }]} numberOfLines={1}>{title}</Text>
        {includedWithPrime && (
          <Text style={[typography.tileSubtitle, { color: colors.blue }]}>Included with Prime</Text>
        )}
      </Animated.View>
    </Pressable>
  );
}
```

### Hero Billboard

```tsx
// components/Billboard.tsx
import { Image, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { PlayButton } from './PlayButton';
import { WatchlistButton } from './WatchlistButton';

export function Billboard({
  title, metaLeading, imdb, stillUri, onPlay, watchlisted, onToggleWatchlist,
}: { title: string; metaLeading: string; imdb: string; stillUri: string;
     onPlay: () => void; watchlisted: boolean; onToggleWatchlist: () => void }) {
  return (
    <View style={{ height: 440, backgroundColor: colors.canvas }}>
      <Image source={{ uri: stillUri }} style={{ width: '100%', height: '100%' }} />
      <LinearGradient colors={['transparent', colors.canvas]}
                      style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: '60%' }} />
      <View style={{ position: 'absolute', left: 16, right: 16, bottom: 16, gap: 12 }}>
        <Text style={typography.detailsTitle}>{title}</Text>
        <Text style={typography.meta}>
          {metaLeading}<Text style={{ color: colors.imdbYellow }}>{imdb}</Text>
        </Text>
        <View style={{ flexDirection: 'row', gap: 12 }}>
          <View style={{ flex: 1 }}><PlayButton onPress={onPlay} /></View>
          <View style={{ flex: 1 }}><WatchlistButton added={watchlisted} onToggle={onToggleWatchlist} /></View>
        </View>
      </View>
    </View>
  );
}
```

### X-Ray Cast Overlay

```tsx
// components/XRayOverlay.tsx
import { FlatList, Image, Pressable, Text, View } from 'react-native';
import { BlurView } from 'expo-blur';
import Animated, { SlideInDown, SlideOutDown } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function XRayOverlay({
  cast, nowPlaying, shown, onClose,
}: { cast: { id: string; headshotUri: string; name: string; role: string }[];
     nowPlaying?: string; shown: boolean; onClose: () => void }) {
  if (!shown) return null;
  return (
    <Animated.View entering={SlideInDown.duration(280)} exiting={SlideOutDown.duration(280)}
                   style={{ position: 'absolute', left: 0, right: 0, bottom: 0 }}>
      <BlurView intensity={24} tint="dark"
                style={{ backgroundColor: 'rgba(35,47,62,0.96)',
                         borderTopLeftRadius: 16, borderTopRightRadius: 16, padding: 20, gap: 12 }}>
        <View style={{ flexDirection: 'row', alignItems: 'center' }}>
          <Text style={[typography.labelUpper, { color: colors.textSecondary, flex: 1 }]}>In this scene</Text>
          <Pressable onPress={onClose} hitSlop={12}>
            <Ionicons name="chevron-down" size={20} color={colors.textSecondary} />
          </Pressable>
        </View>
        <FlatList
          horizontal showsHorizontalScrollIndicator={false}
          data={cast}
          keyExtractor={(c) => c.id}
          ItemSeparatorComponent={() => <View style={{ width: 16 }} />}
          renderItem={({ item }) => (
            <View style={{ width: 80, alignItems: 'center', gap: 6 }}>
              <Image source={{ uri: item.headshotUri }} style={{ width: 56, height: 56, borderRadius: 28 }} />
              <Text style={typography.tileSubtitle} numberOfLines={1}>{item.name}</Text>
              <Text style={[typography.tileSubtitle, { color: colors.textSecondary }]} numberOfLines={1}>{item.role}</Text>
            </View>
          )}
        />
        {nowPlaying && (
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
            <Ionicons name="musical-note" size={16} color={colors.blue} />
            <Text style={typography.meta}>Now playing: {nowPlaying}</Text>
          </View>
        )}
      </BlurView>
    </Animated.View>
  );
}
```

## 4. Tab Bar

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
        tabBarActiveTintColor:  colors.blue,      // blue is the indicator
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(15,23,30,0.6)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'AmazonEmber-Medium', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Home',      tabBarIcon: ({ color }) => <Ionicons name="home"          size={24} color={color} /> }} />
      <Tabs.Screen name="store"     options={{ title: 'Store',     tabBarIcon: ({ color }) => <Ionicons name="bag-handle"    size={24} color={color} /> }} />
      <Tabs.Screen name="live"      options={{ title: 'Live',      tabBarIcon: ({ color }) => <Ionicons name="radio"        size={24} color={color} /> }} />
      <Tabs.Screen name="find"      options={{ title: 'Find',      tabBarIcon: ({ color }) => <Ionicons name="search"       size={24} color={color} /> }} />
      <Tabs.Screen name="downloads" options={{ title: 'Downloads', tabBarIcon: ({ color }) => <Ionicons name="arrow-down-circle" size={24} color={color} /> }} />
    </Tabs>
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

// Watchlist add — withSequence(withSpring(1.15), withSpring(1)) + NotificationFeedbackType.Success (see §3)

// X-Ray slide-up — SlideInDown / SlideOutDown.duration(280) on the panel

// Row snap — FlatList horizontal with snapToInterval={tileWidth + 12} decelerationRate="fast"
```

## 6. Icon Library

Use `@expo/vector-icons` — ships Ionicons, Feather, MaterialCommunityIcons. Map to Prime Video's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Play / Resume | `play` |
| Pause | `pause` |
| Skip ±10 | `play-forward` / `play-back` |
| Watchlist add | `add` / `checkmark` |
| X-Ray | `information-circle-outline` |
| Download | `arrow-down-circle-outline` / `arrow-down-circle` |
| Share | `share-outline` |
| Audio / subtitles | `chatbox-outline` |
| AirPlay | `tv-outline` |
| Search | `search` |
| Home | `home-outline` / `home` |
| Store | `bag-handle-outline` / `bag-handle` |
| Live | `radio-outline` / `radio` |
| Downloads | `arrow-down-circle-outline` / `arrow-down-circle` |

## 7. Platform Notes

- **iOS-only feel**: Use `expo-blur` for the tab bar AND the X-Ray overlay (`rgba(35,47,62,0.96)` + intensity 24). Android falls back to an opaque navy panel.
- **Status bar**: Set `<StatusBar style="light" />` from `expo-status-bar` globally — the dark-navy canvas requires light content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the X-Ray panel and player controls inset from the home indicator; rows bleed right
- **Row snap**: use `FlatList` `horizontal` with `snapToInterval={tileWidth + 12}` and `decelerationRate="fast"` to reproduce Prime's paged-row feel
- **Dynamic Type**: React Native respects font scaling by default; set `allowFontScaling={false}` on tab labels, scrubber timestamps, and tile subtitles where layout breaks
- **Accessibility**: add `accessibilityRole="button"` + `accessibilityLabel` on tiles ("Play The Citadel Files, included with Prime"); in X-Ray group each member as "Name as Role"
- **Reduce Motion**: gate the tile scale-up, X-Ray slide, and row snap behind `AccessibilityInfo.isReduceMotionEnabled()` (cross-fade X-Ray, static tiles)
