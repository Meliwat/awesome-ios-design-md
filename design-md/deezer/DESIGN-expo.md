# Deezer (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Deezer's visual language into paste-ready Expo / React Native code: a design-token module, the living-gradient Flow artwork, the embedded equalizer, the circular gradient play button, the gradient scrubber, and the now-playing-aware song row.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — the only real theme)
  canvas:       '#0F0D13', // violet-tinted near-black, NOT pure black
  surface1:     '#19161F',
  surface2:     '#221E2B',
  divider:      '#2A2633',
  scrim:        'rgba(15,13,19,0.85)',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#A29CB0',
  textTertiary:  '#6E6880',

  // Brand (the gradient + its endpoints)
  purple:     '#A238FF',
  pink:       '#FF0092',
  purpleDeep: '#7C28C4',
  pinkPress:  '#D60079',
  artMagenta: '#C71F8E',

  // Semantic
  success: '#1ED760',
  error:   '#FF4D5E',
  warning: '#FFB02E',
} as const;

export type DZColor = keyof typeof colors;

// The Deezer signature gradient — pass to <LinearGradient colors={...}>.
// Use ONLY on alive elements: play button, scrubber fill, FLOW badge, equalizer.
export const gradients = {
  flow:       [colors.purple, colors.pink],                    // horizontal
  playButton: [colors.purple, colors.pink],                    // 135deg diagonal
  artwork:    [colors.purple, colors.artMagenta, colors.pink], // 3-stop diagonal
} as const;
export const artworkLocations = [0, 0.45, 1] as const;
```

## 2. Typography

Deezer ships **Deezer Sans**; use **Inter** as the faithful fallback (SIL OFL, free to bundle). Load via `expo-font`. Body/metadata uses Inter at 400/500; titles use the brand face at 700–800.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    // Swap DeezerSans-* → Inter-* if the brand face isn't licensed.
    'DeezerSans-Bold':     require('../assets/fonts/DeezerSans-Bold.ttf'),
    'DeezerSans-ExtraBold':require('../assets/fonts/DeezerSans-ExtraBold.ttf'),
    'DeezerSans-SemiBold': require('../assets/fonts/DeezerSans-SemiBold.ttf'),
    'Inter-Regular':       require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':        require('../assets/fonts/Inter-Medium.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const BRAND_XB = 'DeezerSans-ExtraBold';
const BRAND_B  = 'DeezerSans-Bold';
const BRAND_SB = 'DeezerSans-SemiBold';
const READ     = 'Inter-Regular';
const READ_M   = 'Inter-Medium';
const white = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  screenTitle:  { ...white, fontFamily: BRAND_XB, fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  nowPlaying:   { ...white, fontFamily: BRAND_XB, fontSize: 26, lineHeight: 31, letterSpacing: -0.4 },
  section:      { ...white, fontFamily: BRAND_B,  fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  subhead:      { ...white, fontFamily: BRAND_B,  fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  body:         { color: '#FFFFFF', fontFamily: READ, fontSize: 16, lineHeight: 24 },
  rowTitle:     { ...white, fontFamily: BRAND_SB, fontSize: 15, lineHeight: 20 },
  nowArtist:    { color: '#A29CB0', fontFamily: READ_M, fontSize: 15, lineHeight: 20 },
  meta:         { color: '#A29CB0', fontFamily: READ, fontSize: 14, lineHeight: 19 },
  overline:     { color: '#A29CB0', fontFamily: BRAND_B, fontSize: 12, lineHeight: 12, letterSpacing: 0.4 },
  scrubTime:    { color: '#A29CB0', fontFamily: READ_M, fontSize: 11, lineHeight: 11, fontVariant: ['tabular-nums'] as const },
  tabLabel:     { fontFamily: BRAND_SB, fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  button:       { color: '#FFFFFF', fontFamily: BRAND_B, fontSize: 16, lineHeight: 16 },
  chip:         { ...white, fontFamily: BRAND_B, fontSize: 13, lineHeight: 13, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Living-Gradient Flow Artwork (with FLOW badge + embedded equalizer)

```tsx
// components/FlowArtwork.tsx
import { useEffect } from 'react';
import { View, Text, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, Easing } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, gradients, artworkLocations } from '../theme/colors';
import { typography } from '../theme/typography';

const BARS = [0.38,0.62,0.88,0.54,0.76,0.42,0.68,0.92,0.50,0.72,0.34,0.60];

export function FlowArtwork({ isPlaying = true }: { isPlaying?: boolean }) {
  const drift = useSharedValue(0);
  useEffect(() => {
    if (isPlaying) drift.value = withRepeat(withTiming(1, { duration: 12000, easing: Easing.inOut(Easing.ease) }), -1, true);
  }, [isPlaying]);
  const bloom = useAnimatedStyle(() => ({ opacity: 0.7 + drift.value * 0.3 }));

  return (
    <View style={styles.art}>
      <LinearGradient
        colors={gradients.artwork} locations={artworkLocations}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={StyleSheet.absoluteFill}
      />
      <Animated.View pointerEvents="none" style={[StyleSheet.absoluteFill, bloom]}>
        <LinearGradient
          colors={['rgba(255,255,255,0.22)', 'transparent']}
          start={{ x: 0.78, y: 0.22 }} end={{ x: 0.3, y: 0.7 }}
          style={StyleSheet.absoluteFill}
        />
      </Animated.View>

      {/* FLOW badge */}
      <View style={styles.badge}>
        <Ionicons name="flash" size={12} color="#FFF" />
        <Text style={[typography.overline, { color: '#FFF' }]}>FLOW</Text>
      </View>

      {/* Embedded equalizer */}
      <View style={styles.eq}>
        {BARS.map((h, i) => (
          <View key={i} style={[styles.bar, { height: `${h * 46}%` }]} />
        ))}
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  art: {
    aspectRatio: 1, borderRadius: 16, overflow: 'hidden',
    shadowColor: colors.purple, shadowOpacity: 0.55, shadowRadius: 24,
    shadowOffset: { width: 0, height: 24 }, elevation: 18,
  },
  badge: {
    position: 'absolute', top: 16, left: 16, flexDirection: 'row', alignItems: 'center', gap: 7,
    backgroundColor: 'rgba(15,13,19,0.55)', borderRadius: 999,
    paddingVertical: 7, paddingLeft: 11, paddingRight: 13,
  },
  eq: {
    position: 'absolute', left: 18, right: 18, bottom: 22,
    flexDirection: 'row', alignItems: 'flex-end', gap: 4, height: '46%',
  },
  bar: { flex: 1, backgroundColor: 'rgba(255,255,255,0.62)', borderTopLeftRadius: 3, borderTopRightRadius: 3 },
});
```

### Circular Gradient Play Button

```tsx
// components/PlayButton.tsx
import { Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors, gradients } from '../theme/colors';

export function PlayButton({ playing, onToggle }: { playing: boolean; onToggle: () => void }) {
  const scale = useSharedValue(1);
  const a = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => { scale.value = withTiming(0.94, { duration: 120 }); }}
      onPressOut={() => { scale.value = withTiming(1, { duration: 120 }); }}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onToggle(); }}
    >
      <Animated.View style={[{
        width: 68, height: 68, borderRadius: 34, alignItems: 'center', justifyContent: 'center',
        shadowColor: colors.pink, shadowOpacity: 0.6, shadowRadius: 14, shadowOffset: { width: 0, height: 12 }, elevation: 14,
      }, a]}>
        <LinearGradient
          colors={gradients.playButton} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ ...StyleSheetAbsolute, borderRadius: 34 }}
        />
        <Ionicons name={playing ? 'pause' : 'play'} size={26} color="#FFF" style={{ marginLeft: playing ? 0 : 2 }} />
      </Animated.View>
    </Pressable>
  );
}
const StyleSheetAbsolute = { position: 'absolute' as const, top: 0, left: 0, right: 0, bottom: 0 };
```

### Gradient Scrubber

```tsx
// components/GradientScrubber.tsx
import { useState } from 'react';
import { View, Text, StyleSheet, LayoutChangeEvent } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import { colors, gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function GradientScrubber({
  progress, elapsed, remaining, onSeek,
}: { progress: number; elapsed: string; remaining: string; onSeek: (p: number) => void }) {
  const [w, setW] = useState(1);
  const [dragging, setDragging] = useState(false);
  const knob = dragging ? 17 : 13;

  const pan = Gesture.Pan()
    .onBegin(() => setDragging(true))
    .onChange((e) => onSeek(Math.min(Math.max(0, e.x / w), 1)))
    .onFinalize(() => setDragging(false));

  return (
    <View>
      <GestureDetector gesture={pan}>
        <View style={styles.hit} onLayout={(e: LayoutChangeEvent) => setW(e.nativeEvent.layout.width)}>
          <View style={styles.track} />
          <LinearGradient colors={gradients.flow} start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
            style={[styles.fill, { width: `${progress * 100}%` }]} />
          <View style={[styles.knob, { width: knob, height: knob, left: `${progress * 100}%`, marginLeft: -knob / 2 }]} />
        </View>
      </GestureDetector>
      <View style={styles.times}>
        <Text style={typography.scrubTime}>{elapsed}</Text>
        <Text style={typography.scrubTime}>{remaining}</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  hit: { height: 17, justifyContent: 'center' },
  track: { height: 4, borderRadius: 2, backgroundColor: colors.surface2 },
  fill: { position: 'absolute', left: 0, height: 4, borderRadius: 2 },
  knob: {
    position: 'absolute', borderRadius: 999, backgroundColor: '#FFF',
    shadowColor: '#000', shadowOpacity: 0.5, shadowRadius: 3, shadowOffset: { width: 0, height: 2 },
  },
  times: { flexDirection: 'row', justifyContent: 'space-between', marginTop: 9 },
});
```

### Now-Playing-Aware Song Row

```tsx
// components/SongRow.tsx
import { useEffect } from 'react';
import { View, Text, Pressable, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function SongRow({ title, artist, isPlaying }: { title: string; artist: string; isPlaying: boolean }) {
  return (
    <Pressable style={({ pressed }) => [styles.row, pressed && { backgroundColor: colors.surface2 }]}>
      <LinearGradient colors={gradients.artwork} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={styles.thumb} />
      <View style={{ flex: 1 }}>
        <Text style={[typography.rowTitle, isPlaying && { color: colors.pink }]}>{title}</Text>
        <Text style={typography.meta}>{artist}</Text>
      </View>
      {isPlaying ? <EqualizerMark /> : <Ionicons name="ellipsis-horizontal" size={18} color={colors.textSecondary} />}
    </Pressable>
  );
}

function EqualizerMark() {
  const t = useSharedValue(0);
  useEffect(() => { t.value = withRepeat(withTiming(1, { duration: 500 }), -1, true); }, []);
  const base = [7, 14, 5, 11];
  return (
    <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 2, height: 16 }}>
      {base.map((h, i) => <Bar key={i} h={h} alt={base[(i + 1) % base.length]} t={t} />)}
    </View>
  );
}
function Bar({ h, alt, t }: { h: number; alt: number; t: any }) {
  const a = useAnimatedStyle(() => ({ height: h + (alt - h) * t.value }));
  return <Animated.View style={[{ width: 3, borderRadius: 1, backgroundColor: colors.pink }, a]} />;
}

const styles = StyleSheet.create({
  row: { flexDirection: 'row', alignItems: 'center', gap: 12, height: 64, paddingHorizontal: 24 },
  thumb: { width: 44, height: 44, borderRadius: 6 },
});
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
        tabBarActiveTintColor: colors.purple,       // Deezer purple, NOT the gradient
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(15,13,19,0.94)',
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'DeezerSans-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home"            size={22} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search',  tabBarIcon: ({ color }) => <Ionicons name="search"          size={22} color={color} /> }} />
      <Tabs.Screen name="music"   options={{ title: 'Music',   tabBarIcon: ({ color }) => <Ionicons name="musical-notes"   size={22} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile', tabBarIcon: ({ color }) => <Ionicons name="person"          size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Play/pause — scale 0.94 + soft haptic
scale.value = withTiming(0.94, { duration: 120 });   // onPressIn
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Player expand/collapse (mini → full) — spring sheet
// Use a Reanimated layout transition or react-native-reanimated `withSpring({ damping: 18, stiffness: 140 })`

// Living gradient drift (only while Flow plays)
drift.value = withRepeat(withTiming(1, { duration: 12000, easing: Easing.inOut(Easing.ease) }), -1, true);

// Equalizer — withRepeat(..., -1, true) while playing; cancel/freeze on pause
// Reduce Motion: skip the withRepeat, render static bars

// Song-row activation — color crossfade
// Animate title color #FFFFFF → #FF0092 with withTiming(180)

// Skip / next — artwork slide + crossfade
// SlideInRight / FadeIn entering on the artwork+metadata container
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Deezer's iconography is simple line/solid; the equalizer and Flow artwork are drawn primitives, not icons.

| Purpose | Ionicons |
|---------|----------|
| Collapse player | `chevron-down` |
| Overflow menu | `ellipsis-horizontal` |
| Play | `play` |
| Pause | `pause` |
| Previous | `play-skip-back` |
| Next | `play-skip-forward` |
| Shuffle | `shuffle` |
| Repeat | `repeat` |
| Favorite (filled) | `heart` |
| FLOW badge | `flash` |
| Home (tab) | `home` |
| Search (tab) | `search` |
| Music (tab) | `musical-notes` |
| Profile (tab) | `person` |
| Voice search | `mic` |
| Download | `arrow-down-circle` |
| Add | `add` |
| Lyrics | `chatbox-ellipses` |
| Queue | `list` |

## 7. Platform Notes

- **Font choice**: bundle **Deezer Sans** if licensed; otherwise ship **Inter** (SIL OFL) as the faithful fallback for both brand and reading roles — never leave titles to system-only
- **Status bar**: `<StatusBar style="light" />` always — Deezer is dark-native
- **Force dark**: do not branch on `useColorScheme()`; pin the dark tokens. Only honor a forced-light host with the minimal fallback palette (DESIGN.md §2)
- **Safe area**: wrap screens in `SafeAreaView`; the Now Playing top bar and bottom tab bar need safe-area padding; the artwork and scrim extend edge-to-edge
- **Dynamic Type**: keep `allowFontScaling` on titles/body/rows; set `allowFontScaling={false}` on tab labels, scrubber time, overline, FLOW badge, chip text (layout-sensitive)
- **expo-linear-gradient**: required for the artwork, play button, scrubber fill, and FLOW selected chips — the gradient must never be faked with a solid color
- **Reanimated**: the equalizer and living-gradient drift should run on the UI thread (`react-native-reanimated` worklets) for 60fps; cancel animations on pause
- **Gesture Handler**: the scrubber uses `Gesture.Pan()` with 1:1 tracking; give the knob a 44pt vertical hit area
- **Reduce Motion**: read `AccessibilityInfo.isReduceMotionEnabled()` — disable the gradient drift, the equalizer loop (static bars), and the skip slide; keep the play/pause feedback
- **Accessibility**: the equalizer is `accessibilityElementsHidden`; expose the now-playing state via `accessibilityState={{ selected: isPlaying }}` and a "Now playing" label, not pink color alone
