# Pocket Casts (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Pocket Casts' visual language into paste-ready Expo / React Native code: a design-token module, the per-podcast theme-tint context, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font` (optional Inter fallback), `expo-haptics`, `expo-linear-gradient`, `@react-native-community/slider`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  pcRed:        '#F43E37',
  pcRedPressed: '#D32B25',

  // Canvas & surfaces (dark — canonical)
  canvas:   '#1A1A1A',
  surface1: '#232323',
  surface2: '#2E2E2E',
  divider:  '#383838',

  // Canvas & surfaces (light)
  canvasLight:  '#FFFFFF',
  surfaceLight: '#F2F2F4',
  dividerLight: '#E2E2E4',

  // Text
  textPrimary:    '#FFFFFF',
  textSecondary:  '#B8B8B8',
  textTertiary:   '#757575',
  textPrimaryLt:  '#1A1A1A',
  textSecondaryLt:'#6B6B6B',

  // Accent & semantic
  infoBlue:    '#03A9F4',
  playedGreen: '#78D549',
  starGold:    '#FBC02D',
  warnAmber:   '#FFB300',

  // Theme-tint examples (sampled from cover art at runtime)
  tintWarm:   '#E0533C',
  tintCool:   '#3E7BC2',
  tintGreen:  '#3FA66A',
  tintPurple: '#7E57C2',
  tintPink:   '#D6457E',
} as const;

export type PCColor = keyof typeof colors;
```

```tsx
// theme/ThemeTint.tsx — per-podcast accent provider
import { createContext, useContext } from 'react';
import { colors } from './colors';

const ThemeTintContext = createContext<string>(colors.pcRed);

export function ThemeTintProvider({ tint, children }: { tint: string; children: React.ReactNode }) {
  return <ThemeTintContext.Provider value={tint}>{children}</ThemeTintContext.Provider>;
}
export const useThemeTint = () => useContext(ThemeTintContext);
```

## 2. Typography

Pocket Casts uses the iOS system face on device. Bundle Inter only if you need a guaranteed cross-platform fallback. Always use `fontVariant: ['tabular-nums']` on timecodes.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...primary, fontSize: 32, fontWeight: '800', letterSpacing: -0.4, lineHeight: 38 },
  nowPlaying:  { ...primary, fontSize: 22, fontWeight: '700', letterSpacing: -0.3, lineHeight: 28 },
  section:     { ...primary, fontSize: 22, fontWeight: '700', letterSpacing: -0.2, lineHeight: 28 },
  episode:     { ...primary, fontSize: 18, fontWeight: '700', lineHeight: 23 },
  rowTitle:    { ...primary, fontSize: 15, fontWeight: '600', lineHeight: 20 },
  body:        { ...primary, fontSize: 16, fontWeight: '400', lineHeight: 24 },
  showName:    { color: '#B8B8B8', fontSize: 14, fontWeight: '500', lineHeight: 20 },
  meta:        { color: '#B8B8B8', fontSize: 14, fontWeight: '400', lineHeight: 20 },
  button:      { color: '#FFFFFF', fontSize: 16, fontWeight: '700', lineHeight: 16 },
  chip:        { color: '#B8B8B8', fontSize: 10, fontWeight: '600', letterSpacing: 0.1, lineHeight: 12 },
  tab:         { color: '#757575', fontSize: 10, fontWeight: '600', letterSpacing: 0.1, lineHeight: 12 },
  caption:     { color: '#B8B8B8', fontSize: 12, fontWeight: '400', lineHeight: 16 },
  eyebrow:     { color: '#757575', fontSize: 12, fontWeight: '700', letterSpacing: 0.6, lineHeight: 16, textTransform: 'uppercase' as const },
  timecode:    { color: '#B8B8B8', fontSize: 12, fontWeight: '500', lineHeight: 14, fontVariant: ['tabular-nums'] as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Now Playing Player

```tsx
// components/NowPlaying.tsx
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Slider from '@react-native-community/slider';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { useThemeTint } from '../theme/ThemeTint';

export function NowPlaying({ episodeTitle, showName }: { episodeTitle: string; showName: string }) {
  const tint = useThemeTint();
  const [progress, setProgress] = useState(0.42);
  const [playing, setPlaying] = useState(true);

  return (
    <View style={{ flex: 1, backgroundColor: colors.canvas }}>
      {/* Top bar */}
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 20, paddingVertical: 8 }}>
        <Ionicons name="chevron-down" size={20} color={colors.textPrimary} />
        <Text style={[typography.eyebrow, { color: colors.textSecondary, letterSpacing: 1 }]}>Now Playing</Text>
        <Ionicons name="ellipsis-horizontal" size={20} color={colors.textPrimary} />
      </View>

      {/* Cover with theme-tint glow */}
      <View style={{ paddingHorizontal: 36, paddingTop: 8, paddingBottom: 22 }}>
        <LinearGradient
          colors={[tint, `${tint}8C`]}
          start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{
            aspectRatio: 1, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
            shadowColor: tint, shadowOpacity: 0.55, shadowRadius: 24, shadowOffset: { width: 0, height: 8 },
          }}
        >
          <View style={{ width: 130, height: 130, borderRadius: 65, borderWidth: 7, borderColor: 'rgba(255,255,255,0.92)' }} />
        </LinearGradient>
      </View>

      {/* Meta */}
      <View style={{ paddingHorizontal: 36 }}>
        <Text style={typography.nowPlaying}>{episodeTitle}</Text>
        <Text style={[typography.showName, { color: tint, marginTop: 4 }]}>{showName}</Text>
      </View>

      {/* Scrubber */}
      <View style={{ paddingHorizontal: 30, paddingTop: 10 }}>
        <Slider
          value={progress}
          onValueChange={setProgress}
          minimumTrackTintColor={tint}
          maximumTrackTintColor={colors.surface2}
          thumbTintColor="#FFFFFF"
        />
        <View style={{ flexDirection: 'row', justifyContent: 'space-between', paddingHorizontal: 6 }}>
          <Text style={typography.timecode}>14:22</Text>
          <Text style={typography.timecode}>-19:48</Text>
        </View>
      </View>

      {/* Transport */}
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 40, paddingVertical: 18 }}>
        <Ionicons name="play-back" size={24} color={colors.textPrimary} />
        <Ionicons name="play-skip-back" size={28} color={colors.textPrimary} />
        <Pressable
          onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); setPlaying((p) => !p); }}
          style={({ pressed }) => ({
            width: 68, height: 68, borderRadius: 34, backgroundColor: tint,
            alignItems: 'center', justifyContent: 'center',
            transform: [{ scale: pressed ? 0.94 : 1 }],
            shadowColor: tint, shadowOpacity: 0.55, shadowRadius: 24, shadowOffset: { width: 0, height: 8 },
          })}
        >
          <Ionicons name={playing ? 'pause' : 'play'} size={26} color="#FFFFFF" />
        </Pressable>
        <Ionicons name="play-skip-forward" size={28} color={colors.textPrimary} />
        <Ionicons name="play-forward" size={24} color={colors.textPrimary} />
      </View>

      {/* Action chips */}
      <View style={{ flexDirection: 'row', justifyContent: 'space-around', paddingHorizontal: 28, paddingBottom: 14 }}>
        <Chip icon="list" label="Up Next" active />
        <Chip icon="speedometer" label="1.2×" />
        <Chip icon="pulse" label="Trim" active />
        <Chip icon="star-outline" label="Star" />
      </View>
    </View>
  );

  function Chip({ icon, label, active }: { icon: any; label: string; active?: boolean }) {
    const c = active ? tint : colors.textSecondary;
    return (
      <View style={{ alignItems: 'center', gap: 5 }}>
        <Ionicons name={icon} size={19} color={c} />
        <Text style={[typography.chip, { color: c }]}>{label}</Text>
      </View>
    );
  }
}
```

### Episode List Row

```tsx
// components/EpisodeRow.tsx
import { View, Text, Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function EpisodeRow({
  dateLabel, title, meta, art = ['#E0533C', '#7E2018'] as [string, string],
}: { dateLabel: string; title: string; meta: string; art?: [string, string] }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 14,
      paddingVertical: 14, paddingHorizontal: 16,
      borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      <LinearGradient colors={art} style={{ width: 52, height: 52, borderRadius: 6 }} />
      <View style={{ flex: 1 }}>
        <Text style={typography.eyebrow}>{dateLabel}</Text>
        <Text style={typography.rowTitle} numberOfLines={1}>{title}</Text>
        <Text style={typography.meta}>{meta}</Text>
      </View>
      <Pressable style={{
        width: 30, height: 30, borderRadius: 15,
        borderWidth: 1.5, borderColor: colors.textSecondary,
        alignItems: 'center', justifyContent: 'center',
      }}>
        <Ionicons name="play" size={12} color={colors.textPrimary} />
      </Pressable>
    </View>
  );
}
```

### Pill Buttons

```tsx
// components/Buttons.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { useThemeTint } from '../theme/ThemeTint';

export function PrimaryButton({ title, onPress }: { title: string; onPress?: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      backgroundColor: pressed ? colors.pcRedPressed : colors.pcRed,
      paddingVertical: 14, paddingHorizontal: 28, borderRadius: 999,
      transform: [{ scale: pressed ? 0.98 : 1 }],
    })}>
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function TintButton({ title, onPress }: { title: string; onPress?: () => void }) {
  const tint = useThemeTint();
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      backgroundColor: tint, opacity: pressed ? 0.88 : 1,
      paddingVertical: 14, paddingHorizontal: 28, borderRadius: 999,
    })}>
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function OutlineButton({ title, onPress }: { title: string; onPress?: () => void }) {
  return (
    <Pressable onPress={onPress} style={{
      borderWidth: 1.5, borderColor: colors.textSecondary,
      paddingVertical: 12, paddingHorizontal: 22, borderRadius: 999,
    }}>
      <Text style={{ ...typography.rowTitle, color: colors.textPrimary }}>{title}</Text>
    </Pressable>
  );
}
```

### Trim Silence Toggle Row

```tsx
// components/EffectRow.tsx
import { View, Text, Switch } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function EffectRow({
  title, subtitle, value, onValueChange,
}: { title: string; subtitle: string; value: boolean; onValueChange: (v: boolean) => void }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
      paddingVertical: 14, paddingHorizontal: 16,
      borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      <View style={{ flex: 1 }}>
        <Text style={typography.rowTitle}>{title}</Text>
        <Text style={typography.caption}>{subtitle}</Text>
      </View>
      <Switch
        value={value}
        onValueChange={onValueChange}
        trackColor={{ true: colors.pcRed, false: colors.surface2 }}
        thumbColor="#FFFFFF"
      />
    </View>
  );
}
```

### Mini-Player

```tsx
// components/MiniPlayer.tsx
import { View, Text, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { useThemeTint } from '../theme/ThemeTint';

export function MiniPlayer({
  title, show, playing, onToggle,
}: { title: string; show: string; playing: boolean; onToggle: () => void }) {
  const tint = useThemeTint();
  return (
    <View style={{ height: 64, backgroundColor: colors.surface1, borderTopWidth: 2, borderTopColor: tint, flexDirection: 'row', alignItems: 'center', paddingHorizontal: 14, gap: 12 }}>
      <View style={{ width: 44, height: 44, borderRadius: 6, backgroundColor: tint }} />
      <View style={{ flex: 1 }}>
        <Text style={[typography.rowTitle]} numberOfLines={1}>{title}</Text>
        <Text style={typography.caption} numberOfLines={1}>{show}</Text>
      </View>
      <Pressable onPress={onToggle}>
        <Ionicons name={playing ? 'pause' : 'play'} size={22} color={colors.textPrimary} />
      </Pressable>
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
        tabBarActiveTintColor: colors.pcRed,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontSize: 10, fontWeight: '600', letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Podcasts', tabBarIcon: ({ color }) => <Ionicons name="grid"    size={22} color={color} /> }} />
      <Tabs.Screen name="filters"  options={{ title: 'Filters',  tabBarIcon: ({ color }) => <Ionicons name="filter"  size={22} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="compass" size={22} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { FadeIn, FadeOut, useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';

// Play button press — scale 0.94
// style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.94 : 1 }] })}

// Theme-tint cross-fade on show change — animate the tint-driven props with withTiming(250)
const tintProgress = useSharedValue(0);
// tintProgress.value = withTiming(1, { duration: 250 });

// Mini-player -> Full player
// entering={FadeIn.duration(300)} on a bottom Modal/sheet (move edge bottom)

// Up Next reorder — react-native-draggable-flatlist; soft haptic on activation + drop
// Download complete tick — withTiming scale 0 -> 1, 150ms spring

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); // play, scrub boundary, reorder, toggle
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Pocket Casts iconography is simple stroked glyphs with circular play buttons.

| Purpose | Ionicons |
|---------|----------|
| Podcasts (tab) | `grid` |
| Filters (tab) | `filter` |
| Discover (tab) | `compass` |
| Profile (tab) | `person-circle` |
| Play | `play` |
| Pause | `pause` |
| Previous / Next | `play-skip-back` / `play-skip-forward` |
| Skip back / forward | `play-back` / `play-forward` |
| Up Next | `list` |
| Playback speed | `speedometer` |
| Trim Silence | `pulse` |
| Volume Boost | `volume-high` |
| Star | `star-outline` / `star` |
| Mark played | `checkmark-circle` |
| Download | `arrow-down-circle-outline` |
| Downloaded | `arrow-down-circle` |
| Collapse player | `chevron-down` |
| More | `ellipsis-horizontal` |
| Search | `search` |
| Reorder handle | `reorder-three` |
| AirPlay | `tv-outline` |

## 7. Platform Notes

- **Font choice**: Pocket Casts uses the iOS system face; React Native's default system font matches on iOS. Bundle Inter via `expo-font` only if you need an identical Android render
- **Tabular timecodes**: always set `fontVariant: ['tabular-nums']` on durations/timecodes so digits don't shift while scrubbing
- **Theme tint at runtime**: extract a dominant color from cover art (e.g. with a native image-color module or a precomputed server value) and pass it into `ThemeTintProvider`; default to `#F43E37` when contrast is too low
- **Status bar**: `<StatusBar style="light" />` on the dark canonical theme
- **Safe area**: wrap screens in `SafeAreaView`; the mini-player sits directly above the tab bar within the safe area
- **Dynamic Type**: keep `allowFontScaling` default on titles/body; set `allowFontScaling={false}` on tab labels, chip labels, timecodes, date eyebrows
- **Slider**: `@react-native-community/slider` gives the native scrubber; set `minimumTrackTintColor` to the theme tint and `thumbTintColor` white
- **Dark mode**: this is the canonical theme — `useColorScheme()` only matters if you also ship the light theme (`#FFFFFF` canvas, `#1A1A1A` text)
- **Up Next reorder**: use `react-native-draggable-flatlist`; trigger a soft haptic on drag activation and on drop
- **Accessibility**: every transport control and episode row needs an `accessibilityLabel`; the scrubber should expose `accessibilityRole="adjustable"` with increment/decrement actions for 15s/30s skips
