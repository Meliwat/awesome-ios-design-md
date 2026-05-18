# Apple TV (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates the Apple TV app's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. The browse experience is true-black.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (true-black product)
  canvas:    '#000000',
  surface1:  '#1C1C1E',
  surface2:  '#2C2C2E',
  divider:   '#38383A',

  // Text (iOS dark label ramp)
  textPrimary:   '#FFFFFF',
  textSecondary: '#98989F',
  textTertiary:  '#636366',

  // Primary CTA (always white)
  cta:        '#FFFFFF',
  ctaPressed: '#E5E5EA',
  ctaLabel:   '#000000',

  // Accent (the only one — iOS dark systemBlue)
  blue:        '#0A84FF',
  bluePressed: '#0060DF',

  // iOS translucent control fill (secondary buttons, search base)
  glassFill:  'rgba(120,120,128,0.36)',

  // Semantic
  mls:     '#ED1A6F',   // MLS Season Pass ONLY
  live:    '#FF453A',
  success: '#30D158',
  gold:    '#FFD60A',
} as const;

export const gradients = {
  heroScrim: ['transparent', 'rgba(0,0,0,0.65)', 'rgba(0,0,0,0.92)'],
} as const;

export type AppleTVColor = keyof typeof colors;
```

## 2. Typography

The brand face is **SF Pro**. On iOS, React Native's default `System` font already resolves to SF Pro (use `fontFamily: undefined` or omit it). For Android / cross-platform parity, bundle **Inter** (SIL OFL) via `expo-font`.

```tsx
// app/_layout.tsx
import { Platform } from 'react-native';
import { useFonts } from 'expo-font';

export default function Root() {
  // On iOS the system font IS SF Pro — only load Inter for Android parity
  const [loaded] = useFonts(
    Platform.OS === 'android'
      ? {
          'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
          'Inter-Medium':    require('../assets/fonts/Inter-Medium.ttf'),
          'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
          'Inter-Bold':      require('../assets/fonts/Inter-Bold.ttf'),
          'Inter-ExtraBold': require('../assets/fonts/Inter-ExtraBold.ttf'),
        }
      : {},
  );
  if (!loaded) return null;
  return <Stack screenOptions={{ contentStyle: { backgroundColor: '#000000' } }} />;
}
```

```ts
// theme/typography.ts
import { Platform, type TextStyle } from 'react-native';

// On iOS: System (SF Pro). On Android: bundled Inter.
const ff = (w: 'Regular' | 'Medium' | 'SemiBold' | 'Bold' | 'ExtraBold') =>
  Platform.OS === 'android' ? { fontFamily: `Inter-${w}` } : {};

const white = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  largeTitle: { ...white, ...ff('ExtraBold'), fontSize: 34, lineHeight: 36, letterSpacing: -0.8, fontWeight: '800' },
  heroTitle:  { ...white, ...ff('ExtraBold'), fontSize: 28, lineHeight: 30, letterSpacing: -0.6, fontWeight: '800' },
  rowHeader:  { ...white, ...ff('Bold'),      fontSize: 22, lineHeight: 26, letterSpacing: -0.3, fontWeight: '700' },
  title3:     { ...white, ...ff('SemiBold'),  fontSize: 20, lineHeight: 25, letterSpacing: -0.2, fontWeight: '600' },
  body:       { ...white, ...ff('Regular'),   fontSize: 17, lineHeight: 25, fontWeight: '400' },
  headline:   { ...white, ...ff('SemiBold'),  fontSize: 15, lineHeight: 20, fontWeight: '600' },
  subhead:    { color: '#98989F', ...ff('Regular'), fontSize: 13, lineHeight: 18, fontWeight: '400' },
  caption:    { ...white, ...ff('Medium'),    fontSize: 12, lineHeight: 14, fontWeight: '500' },
  eyebrow:    { color: '#98989F', ...ff('Bold'), fontSize: 11, lineHeight: 11, letterSpacing: 1.2, fontWeight: '700', textTransform: 'uppercase' as const },
  button:     { ...white, ...ff('SemiBold'),  fontSize: 15, lineHeight: 15, fontWeight: '600' },
  channelTag: { ...white, ...ff('Bold'),      fontSize: 9,  lineHeight: 9,  letterSpacing: 0.4, fontWeight: '700' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Inset Rounded Hero Card

```tsx
// components/HeroCard.tsx
import { ImageBackground, Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function HeroCard({
  art, eyebrow, title, meta, onPlay, onAdd,
}: { art: string; eyebrow: string; title: string; meta: string; onPlay: () => void; onAdd: () => void }) {
  return (
    <View style={{ marginHorizontal: 14, borderRadius: 16, overflow: 'hidden' }}>
      <ImageBackground source={{ uri: art }} style={{ height: 380, justifyContent: 'flex-end' }}>
        <LinearGradient
          colors={gradients.heroScrim}
          locations={[0.4, 0.72, 1]}
          style={{ position: 'absolute', left: 0, right: 0, top: 0, bottom: 0 }}
        />
        <View style={{ padding: 18, gap: 7 }}>
          <Text style={typography.eyebrow}>{eyebrow}</Text>
          <Text style={typography.heroTitle}>{title}</Text>
          <Text style={[typography.caption, { color: colors.textSecondary }]}>{meta}</Text>
          <View style={{ flexDirection: 'row', gap: 10, marginTop: 7 }}>
            <Pressable
              onPress={onPlay}
              style={({ pressed }) => ({
                flexDirection: 'row', alignItems: 'center', gap: 7,
                backgroundColor: pressed ? colors.ctaPressed : colors.cta,
                paddingVertical: 13, paddingHorizontal: 30, borderRadius: 12,
              })}
            >
              <Text style={[typography.button, { color: colors.ctaLabel }]}>▶  Play</Text>
            </Pressable>
            <Pressable onPress={onAdd} style={{ width: 44, height: 44, borderRadius: 22, overflow: 'hidden' }}>
              <BlurView intensity={28} tint="dark" style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
                <Ionicons name="add" size={20} color="#fff" />
              </BlurView>
            </Pressable>
          </View>
        </View>
      </ImageBackground>
    </View>
  );
}
```

### Up Next Thumbnail (16:9 + resume bar)

```tsx
// components/UpNextThumb.tsx
import { Image, Pressable, Text, View } from 'react-native';
import { BlurView } from 'expo-blur';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function UpNextThumb({
  art, title, subhead, progress = 0, channelTag = 'Apple TV+',
}: { art: string; title: string; subhead: string; progress?: number; channelTag?: string | null }) {
  const scale = useSharedValue(1);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <Pressable
      onPressIn={() => (scale.value = withTiming(0.97, { duration: 120 }))}
      onPressOut={() => (scale.value = withTiming(1, { duration: 120 }))}
      style={{ width: 196 }}
    >
      <Animated.View style={aStyle}>
        <View style={{ width: 196, height: 110, borderRadius: 10, overflow: 'hidden' }}>
          <Image source={{ uri: art }} style={{ width: '100%', height: '100%' }} />
          {channelTag ? (
            <View style={{ position: 'absolute', top: 8, left: 8, borderRadius: 5, overflow: 'hidden' }}>
              <BlurView intensity={20} tint="dark" style={{ paddingHorizontal: 6, paddingVertical: 3 }}>
                <Text style={typography.channelTag}>{channelTag}</Text>
              </BlurView>
            </View>
          ) : null}
          {progress > 0 ? (
            <View style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: 4, backgroundColor: 'rgba(255,255,255,0.28)' }}>
              <View style={{ width: `${progress * 100}%`, height: '100%', backgroundColor: '#fff' }} />
            </View>
          ) : null}
        </View>
        <Text style={[typography.headline, { marginTop: 8 }]} numberOfLines={1}>{title}</Text>
        <Text style={[typography.subhead, { marginTop: 2 }]} numberOfLines={1}>{subhead}</Text>
      </Animated.View>
    </Pressable>
  );
}
```

### Live Badge & MLS Chip

```tsx
// components/Badges.tsx
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
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, backgroundColor: colors.live, borderRadius: 6, paddingHorizontal: 10, paddingVertical: 5 }}>
      <Animated.View style={[{ width: 7, height: 7, borderRadius: 4, backgroundColor: '#fff' }, dot]} />
      <Text style={[typography.eyebrow, { color: '#fff', letterSpacing: 0.6 }]}>LIVE</Text>
    </View>
  );
}

export function MLSChip({ title }: { title: string }) {
  // #ED1A6F is the ONLY non-system brand color — MLS Season Pass surfaces only
  return (
    <View style={{ backgroundColor: colors.mls, borderRadius: 10, paddingHorizontal: 14, paddingVertical: 7 }}>
      <Text style={[typography.caption, { fontWeight: '700' }]}>{title}</Text>
    </View>
  );
}
```

### Shelf Header

```tsx
// components/ShelfHeader.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ShelfHeader({ title, accessory }: { title: string; accessory?: React.ReactNode }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, paddingHorizontal: 18, paddingBottom: 10 }}>
      <Text style={typography.rowHeader}>{title}</Text>
      {accessory}
      <View style={{ flex: 1 }} />
      <Ionicons name="chevron-forward" size={16} color={colors.textTertiary} />
    </View>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.blue,            // system blue active
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.33, borderTopColor: colors.divider, backgroundColor: 'transparent' },
        tabBarBackground: () => <BlurView intensity={40} tint="dark" style={{ flex: 1 }} />,
        tabBarLabelStyle: { fontSize: 12, fontWeight: '500' },
      }}
    >
      <Tabs.Screen name="index"  options={{ title: 'Watch Now', tabBarIcon: ({ color }) => <Ionicons name="play"   size={24} color={color} /> }} />
      <Tabs.Screen name="tvplus" options={{ title: 'TV+',       tabBarIcon: ({ color }) => <Ionicons name="tv"     size={24} color={color} /> }} />
      <Tabs.Screen name="store"  options={{ title: 'Store',     tabBarIcon: ({ color }) => <Ionicons name="bag"    size={24} color={color} /> }} />
      <Tabs.Screen name="search" options={{ title: 'Search',    tabBarIcon: ({ color }) => <Ionicons name="search" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Thumbnail press — scale 0.97
scale.value = withTiming(0.97, { duration: 120 }); // onPressIn
scale.value = withTiming(1,    { duration: 120 }); // onPressOut

// Live dot pulse
o.value = withRepeat(withTiming(0.4, { duration: 1200 }), -1, true);

// Hero cross-fade (~10s, if multiple) — FadeIn/FadeOut between featured (400ms)
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';
// key={featuredIndex} entering={FadeIn.duration(400)} exiting={FadeOut.duration(400)}

// Large title — use expo-router Stack large header OR a manual collapse driven by scroll offset

// Detail open — expo-router push / Modal (use system-feeling default 350ms transition)

// Resume bar appears at value (no fill animation — it's state, not feedback)

// Haptics — sparing, system feel
import * as Haptics from 'expo-haptics';
Haptics.selectionAsync(); // thumbnail tap
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons) — its glyphs read close to SF Symbols. The Apple TV logomark should be a bundled SVG (`react-native-svg`), not an icon-font glyph.

| Purpose | Ionicons |
|---------|----------|
| Watch Now | `play` / `play-circle` |
| TV+ | `tv` |
| Store | `bag` |
| Search | `search` |
| MLS (when subscribed) | `football` / `trophy` |
| Play (CTA) | `play` |
| Add | `add` / `checkmark` |
| Back | `chevron-back` |
| More / disclosure | `chevron-forward` |
| Download | `arrow-down-circle-outline` |
| AirPlay / Cast | `tv-outline` |
| Profile | (avatar) / `person-circle-outline` |
| Live | `radio-outline` |
| Trailer | `play-circle-outline` |
| Info | `information-circle-outline` |
| Settings | `settings-outline` |

## 7. Platform Notes

- **Font choice**: on iOS the default `System` font IS SF Pro — do not override `fontFamily`; only bundle **Inter** (SIL OFL) for Android parity. SF Pro itself is not redistributable off-platform
- **True-black**: set `contentStyle.backgroundColor = '#000000'`, `<StatusBar style="light" />`, and lock dark — the browse experience is always true-black; only embedded Store/account sheets follow system appearance
- **Inset hero**: the hero is `marginHorizontal: 14` + `borderRadius: 16` + `overflow: 'hidden'` — NOT full-bleed; this floating treatment is the signature; do not remove the inset
- **Glass chrome**: use `expo-blur` `BlurView` (`tint="dark"`) for the tab bar background and channel tags; on Android blur is weaker — fall back to `rgba(0,0,0,0.92)` solid for the tab bar
- **Resume bar**: render at the watched fraction with no animation — it is persistent state, not progress feedback
- **Rows bleed off the right edge**: horizontal `FlatList`/`ScrollView` `contentContainerStyle={{ paddingHorizontal: 18 }}`; let the last thumbnail clip — the "more content" affordance
- **Safe area**: wrap screens in `SafeAreaView`; the hero card sits below the large title; the absolute glass tab bar needs bottom safe-area padding and content padding so shelves scroll behind it
- **Dynamic Type**: `<Text>` honors system scale; set `allowFontScaling={false}` on tab labels, channel tags, eyebrows, "LIVE"
- **Accessibility**: label Up Next thumbnails "{title}, {subhead}, {progress}% watched"; mark the pulsing live dot `accessibilityElementsHidden`; the white CTA reads "Play {title}"
- **Reduce motion / transparency**: gate the hero cross-fade and live-dot pulse behind `AccessibilityInfo.isReduceMotionEnabled()`; swap `BlurView` chrome for opaque `#000000` when `isReduceTransparencyEnabled()`
- **MLS pink**: `#ED1A6F` is the ONLY non-system brand color — MLS Season Pass surfaces only; everything else is monochrome + the blue link accent
