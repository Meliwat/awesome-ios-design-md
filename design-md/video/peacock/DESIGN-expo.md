# Peacock (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Peacock's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. Peacock is dark-only.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark-only product)
  canvas:      '#0A0A14',
  canvasWarm:  '#140E26',
  surface1:    '#1A1530',
  surface2:    '#241C3D',
  divider:     '#2C2545',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#B5B2C2',
  textTertiary:  '#7A7689',

  // Primary CTA (always white)
  cta:        '#FFFFFF',
  ctaPressed: '#E6E6EA',
  ctaLabel:   '#0A0A14',
  glass:      'rgba(255,255,255,0.14)',

  // Feather accent (identity / upsell / selection only)
  featherYellow: '#FACC15',
  featherOrange: '#F97316',
  featherPink:   '#EC4899',
  featherPurple: '#8B5CF6',
  featherBlue:   '#2563EB',

  // Semantic
  live:    '#E5142B',
  success: '#1DB954',
  error:   '#FF453A',
} as const;

// Gradient stop arrays for expo-linear-gradient
export const gradients = {
  feather: ['#FACC15', '#F97316', '#EC4899', '#8B5CF6', '#2563EB'],
  premium: ['#F97316', '#EC4899'],
  heroScrim: ['transparent', 'rgba(10,10,20,0.7)', '#0A0A14'],
} as const;

export type PeacockColor = keyof typeof colors;
```

## 2. Typography

Peacock Sans is proprietary; load **Poppins** (SIL OFL) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Poppins-Regular':    require('../assets/fonts/Poppins-Regular.ttf'),
    'Poppins-Medium':     require('../assets/fonts/Poppins-Medium.ttf'),
    'Poppins-SemiBold':   require('../assets/fonts/Poppins-SemiBold.ttf'),
    'Poppins-Bold':       require('../assets/fonts/Poppins-Bold.ttf'),
    'Poppins-ExtraBold':  require('../assets/fonts/Poppins-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ contentStyle: { backgroundColor: '#0A0A14' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const white = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...white, fontFamily: 'Poppins-ExtraBold', fontSize: 32, lineHeight: 35, letterSpacing: -0.5 },
  heroTitle:   { ...white, fontFamily: 'Poppins-ExtraBold', fontSize: 28, lineHeight: 30, letterSpacing: -0.5 },
  rowHeader:   { ...white, fontFamily: 'Poppins-Bold',      fontSize: 22, lineHeight: 25, letterSpacing: -0.2 },
  cardTitle:   { ...white, fontFamily: 'Poppins-Bold',      fontSize: 18, lineHeight: 22 },
  body:        { ...white, fontFamily: 'Poppins-SemiBold',  fontSize: 16, lineHeight: 24 },
  bodyRegular: { ...white, fontFamily: 'Poppins-Regular',   fontSize: 16, lineHeight: 24 },
  caption:     { color: '#B5B2C2', fontFamily: 'Poppins-Medium',  fontSize: 15, lineHeight: 20 },
  meta:        { color: '#B5B2C2', fontFamily: 'Poppins-Regular', fontSize: 13, lineHeight: 18 },
  posterCap:   { color: '#B5B2C2', fontFamily: 'Poppins-Medium',  fontSize: 11, lineHeight: 14 },
  tab:         { fontFamily: 'Poppins-SemiBold',  fontSize: 12, lineHeight: 12, letterSpacing: 0.1 },
  eyebrow:     { color: '#FACC15', fontFamily: 'Poppins-Bold', fontSize: 11, lineHeight: 11, letterSpacing: 1.4, textTransform: 'uppercase' as const },
  button:      { ...white, fontFamily: 'Poppins-Bold', fontSize: 15, lineHeight: 15 },
  rank:        { ...white, fontFamily: 'Poppins-ExtraBold', fontSize: 13, lineHeight: 13 },
  badge:       { ...white, fontFamily: 'Poppins-ExtraBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.4, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

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
    <ImageBackground source={{ uri: art }} style={{ height: 360, justifyContent: 'flex-end' }}>
      <LinearGradient
        colors={gradients.heroScrim}
        locations={[0.38, 0.7, 1]}
        style={{ position: 'absolute', left: 0, right: 0, top: 0, bottom: 0 }}
      />
      <View style={{ alignItems: 'center', paddingHorizontal: 20, paddingBottom: 14, gap: 8 }}>
        <Text style={typography.eyebrow}>{eyebrow}</Text>
        <Text style={[typography.heroTitle, { textAlign: 'center' }]}>{title}</Text>
        <Text style={typography.meta}>{meta}</Text>
        <View style={{ flexDirection: 'row', gap: 10, marginTop: 6 }}>
          <Pressable
            onPress={onPlay}
            style={({ pressed }) => ({
              flexDirection: 'row', alignItems: 'center', gap: 7,
              backgroundColor: pressed ? colors.ctaPressed : colors.cta,
              paddingVertical: 13, paddingHorizontal: 26, borderRadius: 6,
            })}
          >
            <Text style={[typography.button, { color: colors.ctaLabel }]}>▶  Play</Text>
          </Pressable>
          <Pressable onPress={onAdd} style={{ borderRadius: 6, overflow: 'hidden' }}>
            <BlurView intensity={24} tint="dark" style={{ paddingVertical: 12, paddingHorizontal: 18 }}>
              <Text style={typography.body}>+ My Stuff</Text>
            </BlurView>
          </Pressable>
        </View>
      </View>
    </ImageBackground>
  );
}
```

### Channels Rail

```tsx
// components/ChannelsRail.tsx
import { ScrollView, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Channel = { id: string; name: string; gradient: [string, string]; live?: boolean };

export function ChannelsRail({ channels }: { channels: Channel[] }) {
  return (
    <View style={{ paddingVertical: 6 }}>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', paddingHorizontal: 18, marginBottom: 10 }}>
        <Text style={typography.rowHeader}>Channels</Text>
        <Text style={typography.caption}>See All</Text>
      </View>
      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ paddingHorizontal: 18, gap: 10 }}>
        {channels.map((c) => (
          <View key={c.id}>
            <LinearGradient
              colors={c.gradient}
              start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
              style={{ width: 60, height: 60, borderRadius: 14, alignItems: 'center', justifyContent: 'center' }}
            >
              <Text style={typography.badge}>{c.name}</Text>
            </LinearGradient>
            {c.live ? (
              <View style={{ position: 'absolute', top: 4, right: 4, backgroundColor: colors.live, borderRadius: 3, paddingHorizontal: 4, paddingVertical: 2 }}>
                <Text style={[typography.badge, { fontSize: 8 }]}>LIVE</Text>
              </View>
            ) : null}
          </View>
        ))}
      </ScrollView>
    </View>
  );
}
```

### Poster Card (rank + Top 10)

```tsx
// components/PosterCard.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function PosterCard({
  art, title, rank, top10,
}: { art: string; title: string; rank?: number; top10?: boolean }) {
  const scale = useSharedValue(1);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withTiming(0.97, { duration: 120 }))}
      onPressOut={() => (scale.value = withTiming(1, { duration: 120 }))}
      style={{ width: 112 }}
    >
      <Animated.View style={aStyle}>
        <View style={{ width: 112, height: 168, borderRadius: 8, overflow: 'hidden' }}>
          <Image source={{ uri: art }} style={{ width: '100%', height: '100%' }} />
          {top10 ? (
            <LinearGradient colors={gradients.premium} start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
              style={{ position: 'absolute', top: 6, left: 6, paddingHorizontal: 6, paddingVertical: 3, borderRadius: 4 }}>
              <Text style={typography.badge}>TOP 10</Text>
            </LinearGradient>
          ) : null}
          {rank != null ? (
            <View style={{ position: 'absolute', bottom: 6, left: 6, backgroundColor: 'rgba(0,0,0,0.45)', borderRadius: 6, paddingHorizontal: 7, paddingVertical: 2 }}>
              <Text style={typography.rank}>{rank}</Text>
            </View>
          ) : null}
        </View>
        <Text style={[typography.posterCap, { marginTop: 6 }]} numberOfLines={1}>{title}</Text>
      </Animated.View>
    </Pressable>
  );
}
```

### Live Badge

```tsx
// components/LiveBadge.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LiveBadge() {
  const o = useSharedValue(1);
  useEffect(() => { o.value = withRepeat(withTiming(0.4, { duration: 1200 }), -1, true); }, []);
  const dot = useAnimatedStyle(() => ({ opacity: o.value }));
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, backgroundColor: colors.live, borderRadius: 4, paddingHorizontal: 10, paddingVertical: 5 }}>
      <Animated.View style={[{ width: 7, height: 7, borderRadius: 4, backgroundColor: '#fff' }, dot]} />
      <Text style={[typography.badge, { letterSpacing: 0.6 }]}>LIVE</Text>
    </View>
  );
}
```

### Continue Watching Progress

```tsx
// components/WatchProgress.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function WatchProgress({ fraction, label }: { fraction: number; label: string }) {
  const w = useSharedValue(0);
  useEffect(() => { w.value = withTiming(fraction, { duration: 400 }); }, [fraction]);
  const fill = useAnimatedStyle(() => ({ width: `${w.value * 100}%` }));
  return (
    <View style={{ gap: 6 }}>
      <View style={{ height: 4, borderRadius: 2, backgroundColor: colors.surface2, overflow: 'hidden' }}>
        <Animated.View style={fill}>
          <LinearGradient colors={gradients.premium} start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }} style={{ height: 4 }} />
        </Animated.View>
      </View>
      <Text style={typography.posterCap}>{label}</Text>
    </View>
  );
}
```

### Premium Upsell Button

```tsx
// components/PremiumButton.tsx
import { Pressable, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function PremiumButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{ borderRadius: 6, overflow: 'hidden' }}>
      <LinearGradient colors={gradients.feather} start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
        style={{ paddingVertical: 12, paddingHorizontal: 22 }}>
        <Text style={typography.button}>{title}</Text>
      </LinearGradient>
    </Pressable>
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
        tabBarActiveTintColor: colors.textPrimary,    // white icon fill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(10,10,20,0.96)',
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Poppins-SemiBold', fontSize: 12, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home"      size={22} color={color} /> }} />
      <Tabs.Screen name="channels" options={{ title: 'Channels', tabBarIcon: ({ color }) => <Ionicons name="tv"        size={22} color={color} /> }} />
      <Tabs.Screen name="search"   options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search"    size={22} color={color} /> }} />
      <Tabs.Screen name="mystuff"  options={{ title: 'My Stuff', tabBarIcon: ({ color }) => <Ionicons name="bookmark"  size={22} color={color} /> }} />
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

// Hero cross-fade (~8s) — FadeIn/FadeOut between featured indices
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';
// entering={FadeIn.duration(400)} exiting={FadeOut.duration(400)}

// Progress fill — withTiming over 400ms on appear

// Sheet present — @gorhom/bottom-sheet or Modal slide 300ms ease-out

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); // poster select
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);  // add to My Stuff
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The 5-quill feather logomark should be a bundled SVG (`react-native-svg`), not an icon-font glyph.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Channels | `tv` |
| Search | `search` |
| My Stuff | `bookmark` |
| Play (CTA) | `play` |
| Add | `add` / `checkmark` |
| Back | `chevron-back` |
| More info | `information-circle-outline` |
| Download | `arrow-down-circle-outline` |
| Cast | `tv-outline` (or `react-native-google-cast`) |
| Profile | `person-circle-outline` |
| Live | `radio-outline` |
| Trailer | `play-circle-outline` |
| Settings | `settings-outline` |
| Share | `share-outline` |

## 7. Platform Notes

- **Font choice**: Peacock Sans is proprietary — ship **Poppins** (SIL OFL, free) as the closest substitute; never bundle the brand face
- **Dark-only**: lock the app to dark — set `contentStyle.backgroundColor = '#0A0A14'`, `<StatusBar style="light" />`, and ignore `useColorScheme()`; there is no light theme
- **Hero scrim**: use `expo-linear-gradient` with `locations={[0.38, 0.7, 1]}` so the title/buttons stay legible over arbitrary key art
- **Glass buttons**: `expo-blur` `BlurView` tint `"dark"` intensity ~24; on Android blur is weaker, so fall back to `rgba(255,255,255,0.14)` solid
- **Rows bleed off the right edge**: give horizontal `ScrollView`/`FlatList` `contentContainerStyle={{ paddingHorizontal: 18 }}` and let the last card clip — the "more content" affordance
- **Safe area**: wrap screens in `SafeAreaView`; hero `ImageBackground` should bleed under the status bar (`edges={['bottom']}` only)
- **Dynamic Type**: `<Text>` honors system scale; set `allowFontScaling={false}` on tab labels, eyebrows, badges, rank numerals, and "LIVE"
- **Accessibility**: mark the pulsing live dot `accessibilityElementsHidden`; label posters with title + rank; the white CTA reads "Play {title}"
- **Reduce motion**: gate the hero cross-fade and live-dot pulse behind `AccessibilityInfo.isReduceMotionEnabled()`
- **Performance**: use `FlatList`/`FlashList` with `horizontal` for long rows; memoize `PosterCard`; cache art with `expo-image` for smooth momentum scroll
