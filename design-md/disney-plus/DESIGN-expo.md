# Disney+ (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Disney+'s visual language into paste-ready Expo / React Native code: a design-token module, the starfield billboard, the signature brand-portal tiles, the tile glow+scale focus language, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, `expo-linear-gradient`, `expo-blur`, and `expo-video`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#0A0E2A',
  surface1: '#12152E',
  surface2: '#1A1F3D',
  divider:  '#2A3050',

  textPrimary:   '#FFFFFF',
  textSecondary: '#A0A6C0',
  textTertiary:  '#5A6080',

  blue:        '#0063E5',
  glowBlue:    '#1A75FF',
  bluePressed: '#0052BD',
  liveRed:     '#E5484D',

  focusGlow: 'rgba(26,117,255,0.30)',
} as const;

export type DPColor = keyof typeof colors;
```

## 2. Typography

Disney's product sans is Avenir-family. Use `Avenir Next` (iOS system) or bundle the brand TTFs via `expo-font`; Inter is the closest free fallback.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'AvenirNext-Regular':  require('../assets/fonts/AvenirNext-Regular.ttf'),
    'AvenirNext-DemiBold': require('../assets/fonts/AvenirNext-DemiBold.ttf'),
    'AvenirNext-Bold':     require('../assets/fonts/AvenirNext-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ contentStyle: { backgroundColor: '#0A0E2A' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const w = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  billboard:   { ...w, fontFamily: 'AvenirNext-Bold',     fontSize: 30, lineHeight: 33, letterSpacing: -0.3 },
  detailTitle: { ...w, fontFamily: 'AvenirNext-Bold',     fontSize: 26, lineHeight: 30, letterSpacing: -0.2 },
  section:     { ...w, fontFamily: 'AvenirNext-Bold',     fontSize: 20, lineHeight: 24, letterSpacing: -0.1 },
  rowHeader:   { ...w, fontFamily: 'AvenirNext-Bold',     fontSize: 18, lineHeight: 22 },
  cardTitle:   { ...w, fontFamily: 'AvenirNext-DemiBold', fontSize: 14, lineHeight: 18 },
  synopsis:    {        fontFamily: 'AvenirNext-Regular',  fontSize: 15, lineHeight: 23, color: '#A0A6C0' },
  metaStrip:   {        fontFamily: 'AvenirNext-DemiBold', fontSize: 13, lineHeight: 17, letterSpacing: 0.3, color: '#A0A6C0' },
  subtitle:    {        fontFamily: 'AvenirNext-Regular',  fontSize: 14, lineHeight: 18, color: '#A0A6C0' },
  meta:        {        fontFamily: 'AvenirNext-Regular',  fontSize: 12, lineHeight: 16, color: '#A0A6C0' },
  badge:       { ...w, fontFamily: 'AvenirNext-Bold',     fontSize: 10, lineHeight: 12, letterSpacing: 1.0, textTransform: 'uppercase' as const },
  button:      { color: '#0A0E2A', fontFamily: 'AvenirNext-Bold', fontSize: 16, lineHeight: 20, letterSpacing: 0.3 },
  buttonSec:   { ...w, fontFamily: 'AvenirNext-DemiBold', fontSize: 14, lineHeight: 18 },
  tab:         {        fontFamily: 'AvenirNext-DemiBold', fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Starfield + Billboard with Auto-Trailer

```tsx
// components/Starfield.tsx
import { useMemo } from 'react';
import { View } from 'react-native';

export function Starfield({ count = 90 }: { count?: number }) {
  const stars = useMemo(
    () => Array.from({ length: count }).map(() => ({
      left: `${Math.random() * 100}%`,
      top: `${Math.random() * 100}%`,
      size: 1 + Math.random(),
      opacity: Math.random() * 0.5,
    })),
    [count],
  );
  return (
    <View pointerEvents="none" style={{ position: 'absolute', inset: 0 }}>
      {stars.map((s, i) => (
        <View key={i} style={{
          position: 'absolute', left: s.left as any, top: s.top as any,
          width: s.size, height: s.size, borderRadius: s.size / 2,
          backgroundColor: '#FFFFFF', opacity: s.opacity,
        }} />
      ))}
    </View>
  );
}
```

```tsx
// components/HeroBillboard.tsx
import { useEffect, useState } from 'react';
import { View, Image, Text, Dimensions } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { Starfield } from './Starfield';
import { PlayButton, SecondaryButton } from './Buttons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const H = Dimensions.get('window').height;

export function HeroBillboard({ keyArtUri, logoUri, metadata }: {
  keyArtUri: string; logoUri: string; metadata: string;
}) {
  const [trailer, setTrailer] = useState(false);
  useEffect(() => { const t = setTimeout(() => setTrailer(true), 3000); return () => clearTimeout(t); }, []);

  return (
    <View style={{ height: H * 0.62, backgroundColor: colors.canvas }}>
      <Image source={{ uri: keyArtUri }} style={{ position: 'absolute', inset: 0 }} resizeMode="cover" />
      <Starfield />
      <LinearGradient colors={['transparent', 'transparent', colors.canvas]} style={{ position: 'absolute', inset: 0 }} />
      {/* When `trailer`, swap the Image for an <expo-video> muted looping VideoView */}
      <View style={{ flex: 1, justifyContent: 'flex-end', alignItems: 'center', paddingBottom: 24, gap: 14 }}>
        <Image source={{ uri: logoUri }} style={{ height: 110, width: 240 }} resizeMode="contain" />
        <Text style={typography.metaStrip}>{metadata}</Text>
        <View style={{ flexDirection: 'row', gap: 12, paddingHorizontal: 16 }}>
          <PlayButton label="Play" onPress={() => {}} />
          <SecondaryButton icon="add" label="Watchlist" onPress={() => {}} />
        </View>
      </View>
    </View>
  );
}
```

### Primary / Secondary Buttons

```tsx
// components/Buttons.tsx
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PlayButton({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        flex: 1, flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8,
        height: 48, borderRadius: 8, backgroundColor: pressed ? '#E8EAF2' : '#FFFFFF',
        transform: [{ scale: pressed ? 0.98 : 1 }],
        shadowColor: colors.glowBlue, shadowOpacity: 0.30, shadowRadius: 22, shadowOffset: { width: 0, height: 6 },
      })}
    >
      <Ionicons name="play" size={18} color={colors.canvas} />
      <Text style={typography.button}>{label}</Text>
    </Pressable>
  );
}

export function SecondaryButton({ icon, label, onPress }: { icon: any; label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flex: 1, flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8,
        height: 48, borderRadius: 8,
        backgroundColor: pressed ? 'rgba(255,255,255,0.20)' : 'rgba(255,255,255,0.12)',
        borderWidth: 1, borderColor: 'rgba(255,255,255,0.24)',
      })}
    >
      <Ionicons name={icon} size={18} color="#FFF" />
      <Text style={typography.buttonSec}>{label}</Text>
    </Pressable>
  );
}
```

### Brand-Portal Tile (Signature) + the Focus Glow Language

```tsx
// components/BrandPortalTile.tsx
import { Pressable, Image } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';

export function BrandPortalTile({ logoUri, gradient, onPress }: {
  logoUri: string; gradient: [string, string]; onPress: () => void;
}) {
  const focus = useSharedValue(0);
  const style = useAnimatedStyle(() => ({
    transform: [{ scale: 1 + focus.value * 0.04 }],
    borderColor: focus.value > 0.5 ? colors.glowBlue : colors.divider,
    borderWidth: focus.value > 0.5 ? 2 : 1,
    shadowColor: colors.glowBlue,
    shadowOpacity: focus.value * 0.35,
    shadowRadius: 24,
  }));
  return (
    <Pressable
      onPressIn={() => (focus.value = withTiming(1, { duration: 180 }))}
      onPressOut={() => (focus.value = withTiming(0, { duration: 180 }))}
      onPress={onPress}
    >
      <Animated.View style={[{ width: 160, height: 96, borderRadius: 10, overflow: 'hidden' }, style]}>
        <LinearGradient colors={gradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ flex: 1 }}>
          <Image source={{ uri: logoUri }} style={{ flex: 1, margin: 20 }} resizeMode="contain" />
        </LinearGradient>
      </Animated.View>
    </Pressable>
  );
}
```

```tsx
// hooks/useDPFocus.ts — the unified Disney+ selection language
import { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';

export function useDPFocus(radius = 6) {
  const f = useSharedValue(0);
  const style = useAnimatedStyle(() => ({
    transform: [{ scale: 1 + f.value * 0.04 }],
    borderRadius: radius,
    borderWidth: f.value > 0.5 ? 2 : 0,
    borderColor: colors.glowBlue,
    shadowColor: colors.glowBlue,
    shadowOpacity: f.value * 0.30,
    shadowRadius: 24,
  }));
  return {
    style,
    onPressIn: () => (f.value = withTiming(1, { duration: 180 })),
    onPressOut: () => (f.value = withTiming(0, { duration: 180 })),
  };
}
```

### 16:9 Content Card

```tsx
// components/ContentCard.tsx
import { Pressable, Image, View } from 'react-native';
import Animated from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { useDPFocus } from '../hooks/useDPFocus';

export function ContentCard({ keyArtUri, progress }: { keyArtUri: string; progress?: number }) {
  const focus = useDPFocus(6);
  return (
    <Pressable onPressIn={focus.onPressIn} onPressOut={focus.onPressOut}>
      <Animated.View style={[{ width: 220, height: 124, borderRadius: 6, overflow: 'hidden' }, focus.style]}>
        <Image source={{ uri: keyArtUri }} style={{ width: 220, height: 124 }} resizeMode="cover" />
        {progress != null && (
          <View style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: 3, backgroundColor: 'rgba(255,255,255,0.25)' }}>
            <View style={{ height: 3, width: `${progress * 100}%`, backgroundColor: colors.blue }} />
          </View>
        )}
      </Animated.View>
    </Pressable>
  );
}
```

## 4. Hero Auto-Trailer

```tsx
import { useVideoPlayer, VideoView } from 'expo-video';

export function AutoTrailer({ uri }: { uri: string }) {
  const player = useVideoPlayer(uri, (p) => { p.loop = true; p.muted = true; p.play(); });
  return <VideoView player={player} style={{ position: 'absolute', inset: 0 }} contentFit="cover" nativeControls={false} />;
}
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: '#FFFFFF',
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => <BlurView intensity={90} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(10,14,42,0.96)' }} />,
        tabBarLabelStyle: { fontFamily: 'AvenirNext-DemiBold', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Home',      tabBarIcon: ({ color }) => <Ionicons name="home"            size={24} color={color} /> }} />
      <Tabs.Screen name="search"    options={{ title: 'Search',    tabBarIcon: ({ color }) => <Ionicons name="search"          size={24} color={color} /> }} />
      <Tabs.Screen name="watchlist" options={{ title: 'Watchlist', tabBarIcon: ({ color }) => <Ionicons name="add-circle"      size={24} color={color} /> }} />
      <Tabs.Screen name="downloads" options={{ title: 'Downloads', tabBarIcon: ({ color }) => <Ionicons name="arrow-down-circle" size={24} color={color} /> }} />
      <Tabs.Screen name="profile"   options={{ title: 'Profile',   tabBarIcon: ({ color }) => <Ionicons name="person-circle"   size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Tile glow + scale (signature): focus → scale 1→1.04 + #1A75FF border + focusGlow over withTiming(180)
// Hero auto-trailer: setTimeout 3000ms → swap Image for AutoTrailer with a fade
// Play tap: scale 0.98 + Haptics.impactAsync(Medium)
// Brand-portal open: push the hub route with a shared-element-style cross-fade
// Detail open: shared key-art rise; scrim fade over 300ms
// Starfield: optional very slow Reanimated translateY loop (sub-pixel) — ambient
```

## 7. Icon Library

Use `@expo/vector-icons`. Map to Disney+'s SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Play | `play` |
| Play overlay (card) | `play-circle` |
| Watchlist | `add` / `checkmark` |
| Download | `arrow-down-circle-outline` / `arrow-down-circle` |
| Share | `share-outline` |
| Rate | `thumbs-up-outline` |
| Mute toggle | `volume-mute` / `volume-high` |
| Back | `chevron-back` |
| Cast / AirPlay | `tv-outline` |
| More | `ellipsis-horizontal` |
| Search | `search` |
| Home | `home-outline` / `home` |
| Watchlist (tab) | `add-circle-outline` / `add-circle` |
| Downloads (tab) | `arrow-down-circle-outline` |

## 8. Platform Notes

- **Dark-only**: Disney+ has no light mode. Force `#0A0E2A` on the navigator; never read `useColorScheme()`.
- **Status bar**: `<StatusBar style="light" />` globally from `expo-status-bar`.
- **16:9 discipline**: content cards are `aspectRatio: 16/9` — never square; prefer branded key art over text titles.
- **Auto-trailer**: use `expo-video` (`useVideoPlayer`, `muted`, `loop`); gate it behind an "Auto-Play Previews" setting and `AccessibilityInfo.isReduceMotionEnabled()`.
- **Starfield**: lots of tiny `View`s is fine at ~90; for more, draw on `react-native-svg` or a single pre-rendered image. Mark it `accessibilityElementsHidden`.
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the billboard bleeds full-bleed under the status bar.
- **Dynamic Type**: keep `allowFontScaling` ON for titles/synopsis/metadata; OFF only on badges and tab labels.
- **Accessibility**: billboard `accessibilityLabel` = "<Series>, <metadata>"; brand tiles = "Disney universe, button"; mirror continue-watching with `accessibilityValue`.
