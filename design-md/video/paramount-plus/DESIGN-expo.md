# Paramount+ (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Paramount+'s visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. Paramount+ is dark-only.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark-only product)
  canvas:    '#0A0E2D',
  canvas2:   '#0E1438',
  surface1:  '#161C44',
  surface2:  '#1F2655',
  divider:   '#2A3266',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#AEB4D6',
  textTertiary:  '#6F77A6',

  // Brand (single accent)
  blue:        '#0064FF',
  bluePressed: '#0052CC',
  sky:         '#4D9DFF',
  glass:       'rgba(255,255,255,0.14)',

  // Semantic
  live:    '#FF2D46',
  success: '#1ED760',
  error:   '#FF453A',
  gold:    '#F5C518',
} as const;

export const gradients = {
  heroScrim: ['transparent', 'rgba(10,14,45,0.72)', '#0A0E2D'],
  // network hub gradients
  cbs:      ['#0064FF', '#0040A8'],
  paramount:['#1B1B1B', '#000000'],
  mtv:      ['#C8102E', '#7A0A1C'],
  nick:     ['#FFB81C', '#C98A00'],
  comedy:   ['#5A2D8C', '#321650'],
  showtime: ['#008C45', '#045128'],
} as const;

export type ParamountColor = keyof typeof colors;
```

## 2. Typography

Paramount Sans is proprietary; load **Inter** (SIL OFL) via `expo-font`.

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
  return <Stack screenOptions={{ contentStyle: { backgroundColor: '#0A0E2D' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const white = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...white, fontFamily: 'Inter-Black',     fontSize: 32, lineHeight: 34, letterSpacing: -0.6 },
  heroTitle:   { ...white, fontFamily: 'Inter-ExtraBold', fontSize: 26, lineHeight: 27, letterSpacing: -0.5 },
  rowHeader:   { ...white, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 25, letterSpacing: -0.2 },
  cardTitle:   { ...white, fontFamily: 'Inter-Bold',      fontSize: 18, lineHeight: 22 },
  body:        { ...white, fontFamily: 'Inter-SemiBold',  fontSize: 16, lineHeight: 24 },
  bodyReg:     { ...white, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 24 },
  caption:     { color: '#AEB4D6', fontFamily: 'Inter-Medium',  fontSize: 15, lineHeight: 20 },
  meta:        { color: '#AEB4D6', fontFamily: 'Inter-Regular', fontSize: 13, lineHeight: 18 },
  posterCap:   { color: '#AEB4D6', fontFamily: 'Inter-Medium',  fontSize: 11, lineHeight: 14 },
  tab:         { fontFamily: 'Inter-SemiBold', fontSize: 12, lineHeight: 12, letterSpacing: 0.1 },
  eyebrow:     { color: '#4D9DFF', fontFamily: 'Inter-ExtraBold', fontSize: 11, lineHeight: 11, letterSpacing: 1.6, textTransform: 'uppercase' as const },
  button:      { ...white, fontFamily: 'Inter-Bold', fontSize: 15, lineHeight: 15 },
  hubLabel:    { ...white, fontFamily: 'Inter-ExtraBold', fontSize: 11, lineHeight: 11, letterSpacing: 0.3 },
  badge:       { ...white, fontFamily: 'Inter-ExtraBold', fontSize: 8, lineHeight: 8, letterSpacing: 0.5, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Brand-Hubs Row

```tsx
// components/BrandHubsRow.tsx
import { Pressable, ScrollView, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { typography } from '../theme/typography';

type Hub = { id: string; name: string; gradient: [string, string]; labelDark?: boolean };

export function BrandHubsRow({ hubs, onSelect }: { hubs: Hub[]; onSelect: (h: Hub) => void }) {
  return (
    <ScrollView
      horizontal
      showsHorizontalScrollIndicator={false}
      contentContainerStyle={{ paddingHorizontal: 18, gap: 8, paddingBottom: 12 }}
    >
      {hubs.map((h) => (
        <Pressable
          key={h.id}
          onPress={() => onSelect(h)}
          style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.96 : 1 }] })}
        >
          <LinearGradient
            colors={h.gradient}
            start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
            style={{ minWidth: 70, height: 40, borderRadius: 8, alignItems: 'center', justifyContent: 'center', paddingHorizontal: 12 }}
          >
            <Text style={[typography.hubLabel, h.labelDark && { color: '#1B1B1B' }]}>{h.name}</Text>
          </LinearGradient>
        </Pressable>
      ))}
    </ScrollView>
  );
}
```

### Hero Billboard

```tsx
// components/HeroBillboard.tsx
import { ImageBackground, Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { BlurView } from 'expo-blur';
import { colors, gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function HeroBillboard({
  art, eyebrow, title, meta, onPlay, onAdd,
}: { art: string; eyebrow: string; title: string; meta: string; onPlay: () => void; onAdd: () => void }) {
  return (
    <ImageBackground source={{ uri: art }} style={{ height: 332, justifyContent: 'flex-end' }}>
      <LinearGradient
        colors={gradients.heroScrim}
        locations={[0.36, 0.7, 1]}
        style={{ position: 'absolute', left: 0, right: 0, top: 0, bottom: 0 }}
      />
      <View style={{ paddingHorizontal: 20, paddingBottom: 14, gap: 8 }}>
        <Text style={typography.eyebrow}>{eyebrow}</Text>
        <Text style={typography.heroTitle}>{title}</Text>
        <Text style={typography.meta}>{meta}</Text>
        <View style={{ flexDirection: 'row', gap: 10, marginTop: 6 }}>
          <Pressable
            onPress={onPlay}
            style={({ pressed }) => ({
              flexDirection: 'row', alignItems: 'center', gap: 7,
              backgroundColor: pressed ? colors.bluePressed : colors.blue,
              paddingVertical: 13, paddingHorizontal: 28, borderRadius: 6,
            })}
          >
            <Text style={typography.button}>▶  Play</Text>
          </Pressable>
          <Pressable onPress={onAdd} style={{ borderRadius: 6, overflow: 'hidden' }}>
            <BlurView intensity={24} tint="dark" style={{ paddingVertical: 12, paddingHorizontal: 16 }}>
              <Text style={typography.body}>+ My List</Text>
            </BlurView>
          </Pressable>
        </View>
      </View>
    </ImageBackground>
  );
}
```

### Poster Card (LIVE / NEW)

```tsx
// components/PosterCard.tsx
import { useEffect } from 'react';
import { Image, Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withRepeat } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

function LiveFlag() {
  const o = useSharedValue(1);
  useEffect(() => { o.value = withRepeat(withTiming(0.4, { duration: 1200 }), -1, true); }, []);
  const dot = useAnimatedStyle(() => ({ opacity: o.value }));
  return (
    <View style={{ position: 'absolute', top: 6, left: 6, flexDirection: 'row', alignItems: 'center', gap: 4, backgroundColor: colors.live, borderRadius: 4, paddingHorizontal: 6, paddingVertical: 3 }}>
      <Animated.View style={[{ width: 5, height: 5, borderRadius: 3, backgroundColor: '#fff' }, dot]} />
      <Text style={typography.badge}>LIVE</Text>
    </View>
  );
}

export function PosterCard({
  art, title, live, isNew,
}: { art: string; title: string; live?: boolean; isNew?: boolean }) {
  const scale = useSharedValue(1);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <Pressable
      onPressIn={() => (scale.value = withTiming(0.97, { duration: 120 }))}
      onPressOut={() => (scale.value = withTiming(1, { duration: 120 }))}
      style={{ width: 116 }}
    >
      <Animated.View style={aStyle}>
        <View style={{ width: 116, height: 164, borderRadius: 8, overflow: 'hidden' }}>
          <Image source={{ uri: art }} style={{ width: '100%', height: '100%' }} />
          {live ? <LiveFlag /> : null}
          {!live && isNew ? (
            <View style={{ position: 'absolute', top: 6, left: 6, backgroundColor: colors.blue, borderRadius: 4, paddingHorizontal: 6, paddingVertical: 3 }}>
              <Text style={typography.badge}>NEW</Text>
            </View>
          ) : null}
        </View>
        <Text style={[typography.posterCap, { marginTop: 6 }]} numberOfLines={1}>{title}</Text>
      </Animated.View>
    </Pressable>
  );
}
```

### Keep-Watching Progress + Live Badge

```tsx
// components/WatchProgress.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withRepeat } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WatchProgress({ fraction, label }: { fraction: number; label: string }) {
  const w = useSharedValue(0);
  useEffect(() => { w.value = withTiming(fraction, { duration: 400 }); }, [fraction]);
  const fill = useAnimatedStyle(() => ({ width: `${w.value * 100}%` }));
  return (
    <View style={{ gap: 6 }}>
      <View style={{ height: 4, borderRadius: 2, backgroundColor: colors.surface2, overflow: 'hidden' }}>
        <Animated.View style={[{ height: 4, borderRadius: 2, backgroundColor: colors.blue }, fill]} />
      </View>
      <Text style={typography.posterCap}>{label}</Text>
    </View>
  );
}

export function LiveBadge() {
  const o = useSharedValue(1);
  useEffect(() => { o.value = withRepeat(withTiming(0.4, { duration: 1200 }), -1, true); }, []);
  const dot = useAnimatedStyle(() => ({ opacity: o.value }));
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, backgroundColor: colors.live, borderRadius: 4, paddingHorizontal: 10, paddingVertical: 5 }}>
      <Animated.View style={[{ width: 7, height: 7, borderRadius: 4, backgroundColor: '#fff' }, dot]} />
      <Text style={[typography.eyebrow, { color: '#fff', letterSpacing: 0.6 }]}>LIVE</Text>
    </View>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.sky,            // sky-blue active icon
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(10,14,45,0.96)',
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 12, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"  options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home"     size={22} color={color} /> }} />
      <Tabs.Screen name="live"   options={{ title: 'Live TV', tabBarIcon: ({ color }) => <Ionicons name="tv"       size={22} color={color} /> }} />
      <Tabs.Screen name="search" options={{ title: 'Search',  tabBarIcon: ({ color }) => <Ionicons name="search"   size={22} color={color} /> }} />
      <Tabs.Screen name="mylist" options={{ title: 'My List', tabBarIcon: ({ color }) => <Ionicons name="albums"   size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Poster press — scale 0.97
scale.value = withTiming(0.97, { duration: 120 }); // onPressIn
scale.value = withTiming(1,    { duration: 120 }); // onPressOut

// Live dot pulse
o.value = withRepeat(withTiming(0.4, { duration: 1200 }), -1, true);

// Hub re-skin — cross-fade browse into network color
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';
// key={activeHubId} entering={FadeIn.duration(300)} exiting={FadeOut.duration(300)}

// Hero cross-fade (~8s) — FadeIn/FadeOut between featured indices (400ms)

// Progress fill — withTiming over 400ms on appear

// Sheet present — @gorhom/bottom-sheet or Modal slide 300ms ease-out

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); // poster select
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);  // hub switch / add to My List
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The mountain logomark + "Paramount+" wordmark should be a bundled SVG (`react-native-svg`), not an icon-font glyph.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Live TV | `tv` |
| Search | `search` |
| My List | `albums` |
| Play (CTA) | `play` |
| Add | `add` / `checkmark` |
| Back | `chevron-back` |
| More info | `information-circle-outline` |
| Download | `arrow-down-circle-outline` |
| Cast | `tv-outline` (or `react-native-google-cast`) |
| Profile | `person-circle-outline` |
| Live | `radio-outline` |
| Trailer | `play-circle-outline` |
| Schedule | `calendar-outline` |
| Settings | `settings-outline` |
| Share | `share-outline` |

## 7. Platform Notes

- **Font choice**: Paramount Sans is proprietary — ship **Inter** (SIL OFL, free) as the closest substitute; never bundle the brand face
- **Dark-only**: lock the app to dark — set `contentStyle.backgroundColor = '#0A0E2D'`, `<StatusBar style="light" />`, and ignore `useColorScheme()`; there is no light theme
- **Hubs row first**: render the `BrandHubsRow` pinned directly under the top bar and above the hero — it is the structural anchor; do not push it below the hero
- **Hero scrim**: use `expo-linear-gradient` with `locations={[0.36, 0.7, 1]}` so the title/buttons stay legible over arbitrary key art
- **Glass buttons**: `expo-blur` `BlurView` tint `"dark"` intensity ~24; on Android blur is weaker, so fall back to `rgba(255,255,255,0.14)` solid
- **Rows bleed off the right edge**: give horizontal `ScrollView`/`FlatList` `contentContainerStyle={{ paddingHorizontal: 18 }}` and let the last card clip — the "more content" affordance
- **Safe area**: wrap screens in `SafeAreaView`; the hero `ImageBackground` should bleed under the status bar (`edges={['bottom']}` only)
- **Dynamic Type**: `<Text>` honors system scale; set `allowFontScaling={false}` on tab labels, eyebrows, hub labels, badges, and "LIVE"
- **Accessibility**: label hub chips "{network} hub"; mark the pulsing live dot `accessibilityElementsHidden`; the blue CTA reads "Play {title}"
- **Reduce motion**: gate the hub re-skin cross-fade, hero cross-fade, and live-dot pulse behind `AccessibilityInfo.isReduceMotionEnabled()`
- **Performance**: use `FlatList`/`FlashList` `horizontal` for long rows; memoize `PosterCard`; cache art with `expo-image` for smooth momentum scroll
