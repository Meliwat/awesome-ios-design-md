# Overcast (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Overcast's visual language into paste-ready Expo / React Native code: a design-token module, themed components (the orange-ring play button, Now Playing player, Smart Speed / Voice Boost rows, playlist rows), and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `expo-linear-gradient`, `@react-native-community/slider`, and `react-native-reanimated` v3. Optional `expo-font` for an Inter cross-platform fallback.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand (single accent)
  ocOrange:        '#FC7E0F',
  ocOrangePressed: '#E06A00',
  linkBlue:        '#2D7FF9',

  // Canvas & surfaces (light — paper)
  paper:        '#FBFAF6',
  cream:        '#F7F5EF',
  pressedLight: '#EDEBE3',
  dividerLight: '#E4E1D8',

  // Canvas & surfaces (dark)
  canvas:    '#121212',
  surface1:  '#1C1C1E',
  surface2:  '#2A2A2C',
  dividerDk: '#303032',

  // Text
  textPrimaryLt:   '#1A1A1A',
  textSecondaryLt: '#6E6E6E',
  textPrimaryDk:   '#F2F2F2',
  textSecondaryDk: '#9A9A9A',
  textTertiary:    '#9A9A9A',

  // Semantic
  success: '#34C759',
  error:   '#FF3B30',
  warn:    '#FF9F0A',

  // Playlist icon tiles
  plAll:      '#FC7E0F',
  plProgress: '#2D7FF9',
  plStarred:  '#34C759',
  plDownload: '#5856D6',
} as const;

export type OCColor = keyof typeof colors;
```

```tsx
// theme/useOCScheme.ts — resolve light(paper)/dark tokens
import { useColorScheme } from 'react-native';
import { colors } from './colors';

export function useOCScheme() {
  const dark = useColorScheme() === 'dark';
  return {
    dark,
    canvas:    dark ? colors.canvas   : colors.paper,
    surface:   dark ? colors.surface1 : colors.cream,
    divider:   dark ? colors.dividerDk : colors.dividerLight,
    track:     dark ? colors.surface2 : colors.dividerLight,
    primary:   dark ? colors.textPrimaryDk   : colors.textPrimaryLt,
    secondary: dark ? colors.textSecondaryDk : colors.textSecondaryLt,
  };
}
```

## 2. Typography

Overcast uses the iOS system face on device. Always set `fontVariant: ['tabular-nums']` on timecodes and the time-saved stat.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  screenTitle: { fontSize: 32, fontWeight: '800', letterSpacing: -0.4, lineHeight: 38 },
  nowPlaying:  { fontSize: 22, fontWeight: '700', letterSpacing: -0.3, lineHeight: 28 },
  section:     { fontSize: 22, fontWeight: '700', letterSpacing: -0.2, lineHeight: 28 },
  episode:     { fontSize: 18, fontWeight: '700', lineHeight: 23 },
  rowTitle:    { fontSize: 15, fontWeight: '600', lineHeight: 20 },
  body:        { fontSize: 16, fontWeight: '400', lineHeight: 24 },
  showName:    { fontSize: 14, fontWeight: '500', lineHeight: 20 },
  meta:        { fontSize: 14, fontWeight: '400', lineHeight: 20 },
  plDesc:      { fontSize: 12, fontWeight: '400', lineHeight: 16 },
  button:      { fontSize: 16, fontWeight: '600', lineHeight: 16 },
  toggle:      { fontSize: 15, fontWeight: '600', lineHeight: 20 },
  chip:        { fontSize: 10, fontWeight: '600', letterSpacing: 0.1, lineHeight: 12 },
  tab:         { fontSize: 10, fontWeight: '600', letterSpacing: 0.1, lineHeight: 12 },
  count:       { fontSize: 14, fontWeight: '600', lineHeight: 16 },
  skipLabel:   { fontSize: 9,  fontWeight: '700', lineHeight: 10 },
  statCaption: { fontSize: 12, fontWeight: '600', letterSpacing: 0.4, lineHeight: 16, textTransform: 'uppercase' as const },
  timecode:    { fontSize: 12, fontWeight: '500', lineHeight: 14, fontVariant: ['tabular-nums'] as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Orange-Ring Play Button (the signature control)

```tsx
// components/PlayRing.tsx
import { Pressable, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function PlayRing({
  playing, onToggle, diameter = 66,
}: { playing: boolean; onToggle: () => void; diameter?: number }) {
  return (
    <Pressable
      onPress={onToggle}
      style={({ pressed }) => ({
        width: diameter, height: diameter, borderRadius: diameter / 2,
        borderWidth: 2.5, borderColor: pressed ? colors.ocOrangePressed : colors.ocOrange,
        alignItems: 'center', justifyContent: 'center',
        transform: [{ scale: pressed ? 0.96 : 1 }],
      })}
    >
      <Ionicons
        name={playing ? 'pause' : 'play'}
        size={diameter * 0.36}
        color={colors.ocOrange}
      />
    </Pressable>
  );
}
```

### Skip Button (chevron + interval label)

```tsx
// components/SkipButton.tsx
import { Pressable, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { typography } from '../theme/typography';
import { useOCScheme } from '../theme/useOCScheme';

export function SkipButton({
  forward, seconds, onPress,
}: { forward: boolean; seconds: number; onPress: () => void }) {
  const s = useOCScheme();
  return (
    <Pressable onPress={onPress} style={{ alignItems: 'center', gap: 2 }}>
      <Ionicons name={forward ? 'play-forward' : 'play-back'} size={26} color={s.primary} />
      <Text style={[typography.skipLabel, { color: s.secondary }]}>{seconds}</Text>
    </Pressable>
  );
}
```

### Now Playing Player

```tsx
// components/NowPlaying.tsx
import { useState } from 'react';
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Slider from '@react-native-community/slider';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { useOCScheme } from '../theme/useOCScheme';
import { PlayRing } from './PlayRing';
import { SkipButton } from './SkipButton';

export function NowPlaying({ episodeTitle, showName }: { episodeTitle: string; showName: string }) {
  const s = useOCScheme();
  const [progress, setProgress] = useState(0.38);
  const [playing, setPlaying] = useState(true);

  return (
    <View style={{ flex: 1, backgroundColor: s.canvas }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 18, paddingVertical: 8 }}>
        <Ionicons name="chevron-back" size={22} color={colors.ocOrange} />
        <Text style={{ fontSize: 13, fontWeight: '700', color: s.primary }}>Now Playing</Text>
        <Ionicons name="list" size={22} color={s.primary} />
      </View>

      <View style={{ paddingHorizontal: 34, paddingTop: 6, paddingBottom: 20 }}>
        <View style={{
          borderRadius: 10, overflow: 'hidden',
          shadowColor: '#000', shadowOpacity: s.dark ? 0.7 : 0.18, shadowRadius: 40, shadowOffset: { width: 0, height: 18 },
        }}>
          <LinearGradient colors={['#2B3A55', '#121A2E']} style={{ aspectRatio: 1 }} />
          <View style={{ height: 5, backgroundColor: colors.ocOrange }} />
        </View>
      </View>

      <View style={{ paddingHorizontal: 34 }}>
        <Text style={[typography.nowPlaying, { color: s.primary }]}>{episodeTitle}</Text>
        <Text style={[typography.showName, { color: s.secondary, marginTop: 4 }]}>{showName}</Text>
      </View>

      <View style={{ paddingHorizontal: 30, paddingTop: 4 }}>
        <Slider
          value={progress}
          onValueChange={setProgress}
          minimumTrackTintColor={colors.ocOrange}
          maximumTrackTintColor={s.track}
          thumbTintColor={colors.ocOrange}
        />
        <View style={{ flexDirection: 'row', justifyContent: 'space-between', paddingHorizontal: 6 }}>
          <Text style={[typography.timecode, { color: s.secondary }]}>48:12</Text>
          <Text style={[typography.timecode, { color: s.secondary }]}>-1:22:40</Text>
        </View>
      </View>

      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 50, paddingVertical: 14 }}>
        <SkipButton forward={false} seconds={30} onPress={() => {}} />
        <PlayRing playing={playing} onToggle={() => setPlaying((p) => !p)} />
        <SkipButton forward seconds={30} onPress={() => {}} />
      </View>

      <View style={{ flexDirection: 'row', justifyContent: 'space-around', paddingHorizontal: 26, paddingBottom: 14 }}>
        <Effect icon="speedometer" label="1.3×" active />
        <Effect icon="pulse" label="Smart Speed" active />
        <Effect icon="volume-high" label="Voice Boost" active />
        <Effect icon="star-outline" label="Star" />
      </View>
    </View>
  );

  function Effect({ icon, label, active }: { icon: any; label: string; active?: boolean }) {
    const c = active ? colors.ocOrange : s.secondary;
    return (
      <View style={{ alignItems: 'center', gap: 5 }}>
        <Ionicons name={icon} size={19} color={c} />
        <Text style={[typography.chip, { color: c }]}>{label}</Text>
      </View>
    );
  }
}
```

### Smart Speed / Voice Boost Toggle Row

```tsx
// components/EffectToggleRow.tsx
import { View, Text, Switch } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { useOCScheme } from '../theme/useOCScheme';

export function EffectToggleRow({
  title, subtitle, value, onValueChange,
}: { title: string; subtitle: string; value: boolean; onValueChange: (v: boolean) => void }) {
  const s = useOCScheme();
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
      paddingVertical: 15, paddingHorizontal: 16,
      borderBottomWidth: 1, borderBottomColor: s.divider,
    }}>
      <View style={{ flex: 1, paddingRight: 12 }}>
        <Text style={[typography.toggle, { color: s.primary }]}>{title}</Text>
        <Text style={[typography.plDesc, { color: s.secondary }]}>{subtitle}</Text>
      </View>
      <Switch
        value={value}
        onValueChange={onValueChange}
        trackColor={{ true: colors.ocOrange, false: s.track }}
        thumbColor="#FFFFFF"
      />
    </View>
  );
}
```

### Playlist Row

```tsx
// components/PlaylistRow.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { typography } from '../theme/typography';
import { useOCScheme } from '../theme/useOCScheme';

export function PlaylistRow({
  icon, tile, name, desc, count,
}: { icon: any; tile: string; name: string; desc: string; count: number }) {
  const s = useOCScheme();
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 14,
      paddingVertical: 14, paddingHorizontal: 16,
      borderBottomWidth: 1, borderBottomColor: s.divider,
    }}>
      <View style={{ width: 38, height: 38, borderRadius: 9, backgroundColor: tile, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name={icon} size={18} color="#FFFFFF" />
      </View>
      <View style={{ flex: 1 }}>
        <Text style={[typography.rowTitle, { color: s.primary }]}>{name}</Text>
        <Text style={[typography.plDesc, { color: s.secondary }]} numberOfLines={1}>{desc}</Text>
      </View>
      <Text style={[typography.count, { color: s.secondary }]}>{count}</Text>
    </View>
  );
}
```

### Buttons

```tsx
// components/Buttons.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress?: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      backgroundColor: pressed ? colors.ocOrangePressed : colors.ocOrange,
      paddingVertical: 14, paddingHorizontal: 26, borderRadius: 10,
      transform: [{ scale: pressed ? 0.98 : 1 }],
    })}>
      <Text style={[typography.button, { color: '#FFFFFF' }]}>{title}</Text>
    </Pressable>
  );
}

export function OutlineButton({ title, onPress }: { title: string; onPress?: () => void }) {
  return (
    <Pressable onPress={onPress} style={{
      borderWidth: 1.5, borderColor: colors.ocOrange,
      paddingVertical: 12, paddingHorizontal: 22, borderRadius: 10,
    }}>
      <Text style={{ fontSize: 15, fontWeight: '600', color: colors.ocOrange }}>{title}</Text>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { useColorScheme } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  const dark = useColorScheme() === 'dark';
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.ocOrange,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: dark ? colors.canvas : colors.paper,
          borderTopWidth: 0.5,
          borderTopColor: dark ? colors.dividerDk : colors.dividerLight,
        },
        tabBarLabelStyle: { fontSize: 10, fontWeight: '600', letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Playlists', tabBarIcon: ({ color }) => <Ionicons name="list"        size={22} color={color} /> }} />
      <Tabs.Screen name="podcasts"  options={{ title: 'Podcasts',  tabBarIcon: ({ color }) => <Ionicons name="grid"        size={22} color={color} /> }} />
      <Tabs.Screen name="search"    options={{ title: 'Search',    tabBarIcon: ({ color }) => <Ionicons name="search"      size={22} color={color} /> }} />
      <Tabs.Screen name="settings"  options={{ title: 'Settings',  tabBarIcon: ({ color }) => <Ionicons name="settings"    size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';

// Play ring press — scale 0.96 via Pressable style fn (see PlayRing)
// Scrubber knob grow — handled by @react-native-community/slider thumb
// Toggle flip — RN <Switch> animates implicitly; trackColor cross-fades
// Mini-player -> Full player — entering={FadeIn.duration(300)} on a bottom sheet (move edge bottom)
// Theme switch — cross-fade by animating canvas/text color with withTiming(250)

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); // play, toggle flip, scrub boundary, recommend
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Overcast iconography is simple stroked glyphs; the play control is an orange ring (drawn with a bordered View, not an icon).

| Purpose | Ionicons |
|---------|----------|
| Playlists (tab) | `list` |
| Podcasts (tab) | `grid` |
| Search (tab) | `search` |
| Settings (tab) | `settings` |
| Play / Pause | `play` / `pause` |
| Skip back / forward | `play-back` / `play-forward` |
| Smart Speed | `pulse` |
| Voice Boost | `volume-high` |
| Playback speed | `speedometer` |
| Star | `star-outline` / `star` |
| In Progress (playlist) | `time-outline` |
| Downloaded | `arrow-down-circle` |
| Collapse player | `chevron-back` |
| Queue / list | `list` |
| Recommend | `thumbs-up-outline` / `thumbs-up` |
| More | `ellipsis-horizontal` |
| Add podcast | `add` |

## 7. Platform Notes

- **Font choice**: Overcast uses the iOS system face; React Native's default font matches on iOS. Bundle Inter via `expo-font` only if you need an identical Android render
- **Paper canvas**: never use `#FFFFFF` for the light theme — use `#FBFAF6` paper-cream (surfaces `#F7F5EF`)
- **Tabular timecodes**: always set `fontVariant: ['tabular-nums']` on durations, timecodes, and the time-saved stat
- **Orange ring**: the play button is a `View` with `borderWidth: 2.5` and `borderColor: '#FC7E0F'` — never a filled circle
- **Status bar**: `<StatusBar style="dark" />` on the paper theme, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; the mini-player sits directly above the tab bar within the safe area
- **Dynamic Type**: keep `allowFontScaling` default on titles/body; set `allowFontScaling={false}` on tab labels, effect chips, timecodes, counts, skip-interval labels
- **Slider**: `@react-native-community/slider` — `minimumTrackTintColor` and `thumbTintColor` both `#FC7E0F`
- **Dark mode**: `useColorScheme()` swaps the paper tokens to the `#121212` dark set; the orange accent is unchanged
- **Accessibility**: every transport control and playlist row needs an `accessibilityLabel`; the scrubber should expose `accessibilityRole="adjustable"` with skip increment/decrement actions; never rely on orange alone for active effects — pair with the labeled toggle and time-saved stat
