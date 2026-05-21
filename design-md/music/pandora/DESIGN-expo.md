# Pandora (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Pandora's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets for the signature thumb up/down, album-art gradient Now Playing, and station-list row.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-image`, `@expo/vector-icons`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  pandBlue:        '#224099', // heritage
  pandBright:      '#3668FF', // THE accent
  pandBrightPressed:'#2A55D8',

  // Canvas & Surfaces (dark — primary)
  canvas:        '#0B0F1C',
  surface1:      '#141A2B',
  surface2:      '#1E2538',
  divider:       '#28304A',
  gradientMid:   '#1A2240',

  // Canvas & Surfaces (light)
  canvasLight:   '#FFFFFF',
  surface1Light: '#F4F6FB',
  dividerLight:  '#E4E8F2',

  // Text
  textPrimary:   '#EDF0F7',
  textSecondary: '#9AA3BD',
  textTertiary:  '#646E8C',
  textPrimaryLight:   '#11162A',
  textSecondaryLight: '#5C6680',
  onBright:      '#FFFFFF',

  // Semantic
  success:       '#2BC48A',
  warning:       '#F2A93A',
  error:         '#F0526B',
} as const;

export type PandColor = keyof typeof colors;
```

## 2. Typography

Pandora's brand face is a clean grotesque. If licensed, bundle it; otherwise ship **Inter** (SIL OFL). Timecodes/counts use `fontVariant: ['tabular-nums']`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    // swap Inter-* for the licensed brand face if available — keep the keys
    'Pand-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
    'Pand-Medium':    require('../assets/fonts/Inter-Medium.ttf'),
    'Pand-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
    'Pand-Bold':      require('../assets/fonts/Inter-Bold.ttf'),
    'Pand-ExtraBold': require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tnum = { fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] };

export const typography = {
  display:     { fontFamily: 'Pand-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  screenTitle: { fontFamily: 'Pand-ExtraBold', fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  trackTitle:  { fontFamily: 'Pand-ExtraBold', fontSize: 21, lineHeight: 25, letterSpacing: -0.3 },
  section:     { fontFamily: 'Pand-Bold',      fontSize: 17, lineHeight: 22, letterSpacing: -0.1 },
  body:        { fontFamily: 'Pand-Regular',   fontSize: 16, lineHeight: 24 },
  stationName: { fontFamily: 'Pand-SemiBold',  fontSize: 15, lineHeight: 20 },
  meta:        { fontFamily: 'Pand-Regular',   fontSize: 14, lineHeight: 20 },
  albumLine:   { fontFamily: 'Pand-Medium',    fontSize: 13, lineHeight: 18 },
  eyebrow:     { fontFamily: 'Pand-Bold',      fontSize: 12, lineHeight: 14, letterSpacing: 1.0 },
  time:        { fontFamily: 'Pand-Medium',    fontSize: 11, lineHeight: 14, ...tnum },
  button:      { fontFamily: 'Pand-Bold',      fontSize: 16, lineHeight: 16, letterSpacing: 0.1 },
  tab:         { fontFamily: 'Pand-SemiBold',  fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
  thumbCount:  { fontFamily: 'Pand-SemiBold',  fontSize: 12, lineHeight: 14, ...tnum },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Thumb Up / Down (the signature atom)

```tsx
// components/ThumbControls.tsx
import { useRef } from 'react';
import { Pressable, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export type ThumbState = 'none' | 'up' | 'down';

export function ThumbButton({
  kind, state, onPress,
}: { kind: 'up' | 'down'; state: ThumbState; onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  // HARD RULE: thumb-up may be SOLID #3668FF when active; thumb-down is ALWAYS an outline
  const active = (kind === 'up' && state === 'up') || (kind === 'down' && state === 'down');
  const filled = kind === 'up' && active;
  const iconName =
    kind === 'up'
      ? filled ? 'thumbs-up' : 'thumbs-up-outline'
      : 'thumbs-down-outline'; // never the filled variant
  const tint = filled
    ? colors.pandBright
    : kind === 'up'
      ? colors.textTertiary
      : active ? colors.textSecondary : colors.textTertiary;

  return (
    <Pressable
      onPress={() => {
        scale.value = withSequence(withTiming(1.2, { duration: 120 }), withTiming(1, { duration: 120 }));
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
        onPress();
      }}
      hitSlop={10}
      style={{ width: 44, height: 44, alignItems: 'center', justifyContent: 'center' }}
    >
      <Animated.View style={style}>
        <Ionicons name={iconName as any} size={26} color={tint} />
      </Animated.View>
    </Pressable>
  );
}

export function ThumbControls({
  state, onUp, onDown,
}: { state: ThumbState; onUp: () => void; onDown: () => void }) {
  return (
    <View style={{ flexDirection: 'row', justifyContent: 'space-between', width: '100%' }}>
      <ThumbButton kind="down" state={state} onPress={onDown} />
      <ThumbButton kind="up" state={state} onPress={onUp} />
    </View>
  );
}
```

### Now Playing (album-art gradient hero)

```tsx
// components/NowPlaying.tsx
import { View, Text, Pressable, useWindowDimensions } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { Image } from 'expo-image';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { ThumbButton, ThumbState } from './ThumbControls';

export function NowPlaying({
  artwork, title, artist, albumLine, stationName,
  artTopColor, progress, elapsed, remaining,
  isPlaying, onPlayPause, thumb, onUp, onDown,
}: {
  artwork: string; title: string; artist: string; albumLine: string; stationName: string;
  artTopColor: string; progress: number; elapsed: string; remaining: string;
  isPlaying: boolean; onPlayPause: () => void; thumb: ThumbState; onUp: () => void; onDown: () => void;
}) {
  const { width } = useWindowDimensions();
  const art = width - 64;

  return (
    <View style={{ flex: 1, backgroundColor: colors.canvas }}>
      {/* album-art gradient hero — NO card frame */}
      <LinearGradient
        colors={[artTopColor, colors.gradientMid, colors.canvas]}
        style={{ position: 'absolute', inset: 0 }}
      />

      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
        paddingHorizontal: 20, paddingTop: 8 }}>
        <Ionicons name="chevron-down" size={22} color={colors.textPrimary} />
        <View style={{ alignItems: 'center' }}>
          <Text style={[typography.eyebrow, { color: colors.textSecondary }]}>STATION</Text>
          <Text style={{ fontFamily: 'Pand-Bold', fontSize: 13, color: colors.textPrimary, marginTop: 2 }}>
            {stationName}
          </Text>
        </View>
        <Ionicons name="ellipsis-horizontal" size={22} color={colors.textPrimary} />
      </View>

      <Image source={{ uri: artwork }} style={{
        width: art, height: art, borderRadius: 10, alignSelf: 'center', marginTop: 26,
        shadowColor: '#000', shadowOpacity: 0.7, shadowRadius: 25, shadowOffset: { width: 0, height: 24 },
      }} contentFit="cover" />

      <View style={{ paddingHorizontal: 32, marginTop: 22 }}>
        <Text style={[typography.trackTitle, { color: colors.textPrimary }]}>{title}</Text>
        <Text style={{ ...typography.stationName, fontFamily: 'Pand-Medium',
          color: colors.textSecondary, marginTop: 4 }}>{artist}</Text>
        <Text style={[typography.albumLine, { color: colors.textTertiary, marginTop: 2 }]}>{albumLine}</Text>
      </View>

      {/* scrubber */}
      <View style={{ paddingHorizontal: 32, marginTop: 20 }}>
        <View style={{ height: 4, backgroundColor: colors.surface2, borderRadius: 2 }}>
          <View style={{ position: 'absolute', left: 0, top: 0, bottom: 0,
            width: `${progress * 100}%`, backgroundColor: colors.pandBright, borderRadius: 2 }} />
          <View style={{ position: 'absolute', left: `${progress * 100}%`, top: -4,
            width: 12, height: 12, marginLeft: -6, borderRadius: 6, backgroundColor: '#FFFFFF' }} />
        </View>
        <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginTop: 8 }}>
          <Text style={[typography.time, { color: colors.textTertiary }]}>{elapsed}</Text>
          <Text style={[typography.time, { color: colors.textTertiary }]}>{remaining}</Text>
        </View>
      </View>

      {/* transport: thumb-down · skip-back · play · skip-forward · thumb-up */}
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
        paddingHorizontal: 36, marginTop: 22 }}>
        <ThumbButton kind="down" state={thumb} onPress={onDown} />
        <Ionicons name="play-skip-back" size={26} color={colors.textPrimary} />
        <Pressable onPress={onPlayPause} style={{
          width: 66, height: 66, borderRadius: 33, backgroundColor: colors.pandBright,
          alignItems: 'center', justifyContent: 'center',
          shadowColor: colors.pandBright, shadowOpacity: 0.7, shadowRadius: 13, shadowOffset: { width: 0, height: 10 },
        }}>
          <Ionicons name={isPlaying ? 'pause' : 'play'} size={26} color="#FFFFFF" />
        </Pressable>
        <Ionicons name="play-skip-forward" size={26} color={colors.textPrimary} />
        <ThumbButton kind="up" state={thumb} onPress={onUp} />
      </View>
    </View>
  );
}
```

### Station List Row

```tsx
// components/StationRow.tsx
import { useEffect } from 'react';
import { Pressable, Text, View } from 'react-native';
import { Image } from 'expo-image';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StationRow({
  artwork, name, subtitle, isPlaying, onPress,
}: { artwork: string; name: string; subtitle: string; isPlaying: boolean; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{
      flexDirection: 'row', alignItems: 'center', gap: 14, padding: 10, borderRadius: 12,
      backgroundColor: isPlaying ? colors.surface2 : 'transparent',
    }}>
      <Image source={{ uri: artwork }} style={{ width: 52, height: 52, borderRadius: 8 }} contentFit="cover" />
      <View style={{ flex: 1 }}>
        <Text style={{ ...typography.stationName, fontFamily: 'Pand-Bold', color: colors.textPrimary }}>{name}</Text>
        <Text style={[typography.thumbCount, { color: colors.textSecondary, marginTop: 2 }]}>{subtitle}</Text>
      </View>
      {isPlaying && <EqualizerBars />}
    </Pressable>
  );
}

function EqualizerBars() {
  const t = useSharedValue(0);
  useEffect(() => { t.value = withRepeat(withTiming(1, { duration: 500 }), -1, true); }, []);
  const heights = [0.6, 1.0, 0.4, 0.8];
  return (
    <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 2, height: 16 }}>
      {heights.map((h, i) => {
        const style = useAnimatedStyle(() => ({
          height: 16 * (h * (0.5 + 0.5 * (i % 2 === 0 ? t.value : 1 - t.value))),
        }));
        return <Animated.View key={i} style={[{ width: 3, backgroundColor: colors.pandBright, borderRadius: 1 }, style]} />;
      })}
    </View>
  );
}
```

### Primary Button

```tsx
// components/PandPrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { useState } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PandPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  const [pressed, setPressed] = useState(false);
  return (
    <Pressable
      onPress={onPress}
      onPressIn={() => setPressed(true)}
      onPressOut={() => setPressed(false)}
      style={{
        height: 50, paddingHorizontal: 28, borderRadius: 500, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.pandBrightPressed : colors.pandBright,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      }}
    >
      <Text style={[typography.button, { color: colors.onBright }]}>{title}</Text>
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
        tabBarActiveTintColor: colors.textPrimary,        // text-primary active (no pill)
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Pand-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"      options={{ title: 'For You',       tabBarIcon: ({ color }) => <Ionicons name="home"          size={22} color={color} /> }} />
      <Tabs.Screen name="browse"     options={{ title: 'Browse',        tabBarIcon: ({ color }) => <Ionicons name="search"        size={22} color={color} /> }} />
      <Tabs.Screen name="collection" options={{ title: 'My Collection', tabBarIcon: ({ color }) => <Ionicons name="list"          size={22} color={color} /> }} />
      <Tabs.Screen name="recents"    options={{ title: 'Recents',       tabBarIcon: ({ color }) => <Ionicons name="time"          size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import { withSequence, withTiming, withRepeat } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Thumb tap — scale pop 1.0 → 1.2 → 1.0 (240ms) + light haptic
// scale.value = withSequence(withTiming(1.2, { duration: 120 }), withTiming(1, { duration: 120 }));
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
// thumb-down also triggers the skip transition

// Track change — art crossfade + scale 0.96 → 1.0 (320ms); gradient re-derives (400ms)
// Re-key the Image / animate opacity; interpolate artTopColor with withTiming over 400ms

// Skip — art slides horizontally then settles 260ms
// Play/pause — icon swap 150ms; button scale 1 → 0.96 → 1 on press
// Scrubber drag — knob/fill follow finger 1:1; timecodes update live (tabular)

// Mini → Now Playing — shared element art expand 320ms (expo-router shared transition)
// Station start — equalizer fades in (withRepeat) then push Now Playing 300ms
// Sheet present — @gorhom/bottom-sheet 320ms + scrim
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // station created
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The thumb up/down map cleanly to `thumbs-up`/`thumbs-up-outline` and `thumbs-down-outline` — never use the filled thumbs-down variant.

| Purpose | Ionicons |
|---------|----------|
| For You (tab) | `home` |
| Browse (tab) | `search` |
| My Collection (tab) | `list` |
| Recents (tab) | `time` |
| Thumb up | `thumbs-up-outline` / `thumbs-up` |
| Thumb down | `thumbs-down-outline` (outline only) |
| Play | `play` |
| Pause | `pause` |
| Skip forward | `play-skip-forward` |
| Skip back | `play-skip-back` |
| Collapse Now Playing | `chevron-down` |
| Overflow | `ellipsis-horizontal` |
| Create station | `add-circle` |
| Shuffle | `shuffle` |
| Add variety | `options` |
| Why this track | `information-circle-outline` |
| Share | `share-outline` |
| Search | `search` |

## 7. Platform Notes

- **Font choice**: Pandora's brand face is proprietary — license to ship it, or use Inter (SIL OFL) and keep the named ramp identical so layout doesn't shift
- **Tabular numbers**: set `fontVariant: ['tabular-nums']` on timecodes, durations, and thumb counts (already baked into `typography`)
- **Thumb asymmetry**: enforce in `ThumbButton` — `thumbs-up` may render the solid icon when active; `thumbs-down` always uses `thumbs-down-outline`. This is a hard rule, not a style choice
- **Album-art color sampling**: derive `artTopColor` with a native module or `react-native-image-colors`; clamp it dark enough that `#EDF0F7` title text keeps ≥ 4.5:1 contrast; cache per track and cross-fade on change
- **Status bar**: `<StatusBar style="light" />` everywhere (dark-first; the gradient + navy canvas are always dark)
- **Safe area**: wrap screens in `SafeAreaProvider`; the mini player + tab bar pin above the home indicator; the Now Playing gradient extends under the safe areas (`position: absolute, inset: 0`)
- **Dynamic Type**: RN scales `<Text>` — set `allowFontScaling={false}` on tab labels, the eyebrow, timecodes, and thumb counts (layout-critical); album art never scales
- **Gradient**: `expo-linear-gradient` carries the Now Playing hero; the top stop is the sampled color, then `#1A2240`, then `#0B0F1C`
- **Dark-first**: there is effectively no light Now Playing — light mode applies only to settings/account flows; keep `pandBright` constant
- **Accessibility**: give the thumb-up `accessibilityLabel="Thumb up, shapes this station"` and thumb-down `"Thumb down, skips and tunes away"` with `accessibilityState={{ selected }}`; the play button a Play/Pause label; the station row a full label
- **Image performance**: `expo-image` with prefetch of the next track's artwork; cache station-art thumbnails for the list
