# Peloton (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Peloton's studio-black visual language into paste-ready Expo / React Native: a design-token module, themed components, an SVG output ring, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — the only mode)
  canvas:   '#000000',
  surface1: '#121212',
  surface2: '#1C1C1E',
  surface3: '#262629',
  divider:  '#2C2C2E',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#B5B5BA',
  textTertiary:  '#7A7A80',

  // Brand (single accent)
  red:        '#DF1E2E',
  redOnDark:  '#FF4B57',
  redPressed: '#B81825',
  whiteChip:  '#FFFFFF',

  // Functional / in-class metrics (fixed meaning, theme-invariant)
  cadence:    '#E5402A',
  resistance: '#F0A030',
  output:     '#DF1E2E',
  heartRate:  '#FF4B57',
  strive:     '#3DB8E0',
  pr:         '#2ECC71',

  // Semantic
  success: '#2ECC71',
  warning: '#F0A030',
  error:   '#DF1E2E',
  track:   '#262629',
} as const;

export type PLColor = keyof typeof colors;

// Fixed in-class metric palette — never recolor for theme
export const metricColors = {
  cadence:    colors.cadence,
  resistance: colors.resistance,
  output:     colors.output,
  heartRate:  colors.heartRate,
  strive:     colors.strive,
} as const;
```

## 2. Typography

Load Inter (all weights through Black) via `expo-font`. SIL OFL — free to bundle. Peloton sets it heavy and tight; numerals tabular.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':    require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold':   require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':       require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold':  require('../assets/fonts/Inter-ExtraBold.ttf'),
    'Inter-Black':      require('../assets/fonts/Inter-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const TABULAR: TextStyle = { fontVariant: ['tabular-nums'] };

export const typography = {
  outputNumber: { ...TABULAR, color: '#FFFFFF', fontFamily: 'Inter-Black',     fontSize: 44, lineHeight: 44, letterSpacing: -1 },
  screenTitle:  { color: '#FFFFFF', fontFamily: 'Inter-Black',     fontSize: 32, lineHeight: 35, letterSpacing: -0.6 },
  heroTitle:    { color: '#FFFFFF', fontFamily: 'Inter-ExtraBold', fontSize: 26, lineHeight: 30, letterSpacing: -0.5 },
  section:      { color: '#FFFFFF', fontFamily: 'Inter-ExtraBold', fontSize: 22, lineHeight: 26, letterSpacing: -0.4 },
  classTitle:   { color: '#FFFFFF', fontFamily: 'Inter-ExtraBold', fontSize: 17, lineHeight: 20, letterSpacing: -0.3 },
  metricValue:  { ...TABULAR, color: '#FFFFFF', fontFamily: 'Inter-ExtraBold', fontSize: 18, lineHeight: 20 },
  lbOutput:     { ...TABULAR, color: '#FFFFFF', fontFamily: 'Inter-ExtraBold', fontSize: 14, lineHeight: 14 },
  body:         { color: '#B5B5BA', fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 22 },
  meta:         { color: '#7A7A80', fontFamily: 'Inter-Regular',   fontSize: 14, lineHeight: 20 },
  eyebrow:      { color: '#FF4B57', fontFamily: 'Inter-Bold',      fontSize: 12, lineHeight: 15, letterSpacing: 0.6, textTransform: 'uppercase' as const },
  metricLabel:  { color: '#B5B5BA', fontFamily: 'Inter-SemiBold',  fontSize: 11, lineHeight: 13, letterSpacing: 0.4, textTransform: 'uppercase' as const },
  caption:      { color: '#7A7A80', fontFamily: 'Inter-SemiBold',  fontSize: 11, lineHeight: 14 },
  button:       { color: '#FFFFFF', fontFamily: 'Inter-Bold',      fontSize: 15, lineHeight: 15, letterSpacing: 0.2 },
  tab:          { color: '#7A7A80', fontFamily: 'Inter-SemiBold',  fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  badge:        { color: '#FFFFFF', fontFamily: 'Inter-Black',      fontSize: 10, lineHeight: 10, letterSpacing: 0.6, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Class Card

```tsx
// components/ClassCard.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { LiveBadge } from './LiveBadge';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function ClassCard({ discipline, title, instructor, duration, isLive, rating }: {
  discipline: string; title: string; instructor: string; duration: string; isLive?: boolean; rating: string;
}) {
  return (
    <View style={{ backgroundColor: colors.surface1, borderRadius: 14, borderWidth: 1, borderColor: colors.divider, overflow: 'hidden', marginBottom: 18 }}>
      <View style={{ height: 150 }}>
        <LinearGradient colors={['#4A1014', '#160508']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ position: 'absolute', inset: 0 }} />
        {isLive ? <View style={{ position: 'absolute', top: 14, left: 14 }}><LiveBadge /></View> : null}
        <View style={{ position: 'absolute', top: 14, right: 14, backgroundColor: 'rgba(0,0,0,0.6)', paddingHorizontal: 9, paddingVertical: 5, borderRadius: 5 }}>
          <Text style={typography.caption}>{duration}</Text>
        </View>
      </View>
      <View style={{ paddingHorizontal: 16, paddingVertical: 14 }}>
        <Text style={typography.eyebrow}>{discipline}</Text>
        <Text style={[typography.classTitle, { marginTop: 4 }]}>{title}</Text>
        <Text style={[typography.body, { marginTop: 3 }]}>{instructor}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginTop: 8 }}>
          <Ionicons name="star" size={11} color={colors.resistance} />
          <Text style={{ ...typography.caption, color: colors.textTertiary }}>{rating}</Text>
        </View>
      </View>
    </View>
  );
}
```

### Live Badge (pulsing dot)

```tsx
// components/LiveBadge.tsx
import { useEffect } from 'react';
import { View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function LiveBadge() {
  const o = useSharedValue(1);
  useEffect(() => { o.value = withRepeat(withTiming(0.4, { duration: 700 }), -1, true); }, []);
  const dot = useAnimatedStyle(() => ({ opacity: o.value }));

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, backgroundColor: colors.red, paddingHorizontal: 9, paddingVertical: 5, borderRadius: 5 }}>
      <Animated.View style={[{ width: 6, height: 6, borderRadius: 3, backgroundColor: '#FFF' }, dot]} />
      <Text style={typography.badge}>Live</Text>
    </View>
  );
}
```

### Output Ring (SVG)

```tsx
// components/OutputRing.tsx
import { useEffect } from 'react';
import { View, Text } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming, Easing } from 'react-native-reanimated';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

const ACircle = Animated.createAnimatedComponent(Circle);
const R = 40, C = 2 * Math.PI * R;

export function OutputRing({ progress, totalKJ }: { progress: number; totalKJ: number }) {
  const p = useSharedValue(0);
  useEffect(() => { p.value = withTiming(progress, { duration: 600, easing: Easing.out(Easing.ease) }); }, [progress]);
  const animatedProps = useAnimatedProps(() => ({ strokeDashoffset: C * (1 - p.value) }));

  return (
    <View style={{ width: 96, height: 96, alignItems: 'center', justifyContent: 'center' }}>
      <Svg width={96} height={96} style={{ position: 'absolute' }}>
        <Circle cx={48} cy={48} r={R} stroke={colors.track} strokeWidth={9} fill="none" />
        <ACircle cx={48} cy={48} r={R} stroke={colors.red} strokeWidth={9} fill="none"
          strokeLinecap="round" strokeDasharray={C} animatedProps={animatedProps}
          transform="rotate(-90 48 48)" />
      </Svg>
      <Text style={{ ...typography.metricValue, fontFamily: 'Inter-Black', fontSize: 26 }}>{totalKJ}</Text>
      <Text style={{ ...typography.badge, color: colors.textTertiary, marginTop: 3, fontSize: 9 }}>Total kJ</Text>
    </View>
  );
}
```

### In-Class Metric Column

```tsx
// components/MetricColumn.tsx
import { View, Text } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

type Metric = { label: string; value: string; unit?: string; color: string };

export function MetricColumn({ metrics }: { metrics: Metric[] }) {
  return (
    <View style={{ flex: 1 }}>
      {metrics.map((m, i) => (
        <View key={m.label} style={{
          flexDirection: 'row', alignItems: 'baseline', justifyContent: 'space-between',
          paddingVertical: 7,
          borderBottomWidth: i < metrics.length - 1 ? 1 : 0, borderBottomColor: colors.divider,
        }}>
          <Text style={typography.metricLabel}>{m.label}</Text>
          <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 3 }}>
            <Text style={[typography.metricValue, { color: m.color }]}>{m.value}</Text>
            {m.unit ? <Text style={{ ...typography.caption, color: colors.textTertiary }}>{m.unit}</Text> : null}
          </View>
        </View>
      ))}
    </View>
  );
}
```

### Real-Time Leaderboard

```tsx
// components/Leaderboard.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

type Rider = { rank: number; name: string; output: number; isMe?: boolean };

export function Leaderboard({ riders, total }: { riders: Rider[]; total: number }) {
  return (
    <View style={{ backgroundColor: colors.surface1, borderRadius: 14, borderWidth: 1, borderColor: colors.divider, overflow: 'hidden' }}>
      <Text style={{ ...typography.metricLabel, paddingHorizontal: 16, paddingTop: 14, paddingBottom: 10 }}>
        Leaderboard · {total} riders
      </Text>
      {riders.map((r) => (
        <View key={r.rank} style={{
          flexDirection: 'row', alignItems: 'center', gap: 12,
          paddingHorizontal: 16, paddingVertical: 10,
          backgroundColor: r.isMe ? 'rgba(223,30,46,0.12)' : 'transparent',
          borderTopWidth: 1, borderTopColor: colors.divider,
        }}>
          <Text style={[typography.lbOutput, { width: 26, color: r.isMe ? colors.redOnDark : colors.textSecondary }]}>{r.rank}</Text>
          <LinearGradient colors={['#5A2B82', colors.red]} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
            style={{ width: 30, height: 30, borderRadius: 15 }} />
          <Text style={[typography.body, { flex: 1, color: '#FFF' }]}>{r.name}</Text>
          <Text style={typography.lbOutput}>{r.output}</Text>
        </View>
      ))}
    </View>
  );
}
```

### Filter Pill (white-chip) + Primary Button

```tsx
// components/FilterPill.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';

export function FilterPill({ title, selected, onPress }: { title: string; selected: boolean; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{
      backgroundColor: selected ? colors.whiteChip : colors.surface2,
      borderWidth: selected ? 0 : 1, borderColor: colors.divider,
      paddingHorizontal: 16, paddingVertical: 8, borderRadius: 500,
    }}>
      <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 13, color: selected ? '#000' : colors.textSecondary }}>
        {title}
      </Text>
    </Pressable>
  );
}

// components/PLButton.tsx
import { Pressable, Text } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function PLButton({ title, variant = 'red', onPress }:
  { title: string; variant?: 'red' | 'white' | 'outline'; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      backgroundColor: variant === 'red' ? (pressed ? colors.redPressed : colors.red)
        : variant === 'white' ? '#FFF' : 'transparent',
      borderWidth: variant === 'outline' ? 1.5 : 0,
      borderColor: 'rgba(255,255,255,0.4)',
      borderRadius: 500, paddingVertical: 15, alignItems: 'center',
      transform: [{ scale: pressed ? 0.98 : 1 }],
    })}>
      <Text style={[typography.button, { color: variant === 'white' ? '#000' : '#FFF' }]}>{title}</Text>
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
        tabBarActiveTintColor:  '#FFFFFF',          // active is white, no pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.surface1, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}>
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home"          size={22} color={color} /> }} />
      <Tabs.Screen name="classes"  options={{ title: 'Classes',  tabBarIcon: ({ color }) => <Ionicons name="play-circle"   size={22} color={color} /> }} />
      <Tabs.Screen name="schedule" options={{ title: 'Schedule', tabBarIcon: ({ color }) => <Ionicons name="stats-chart"   size={22} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// LIVE dot pulse — the brand heartbeat
o.value = withRepeat(withTiming(0.4, { duration: 700 }), -1, true);

// Output ring fill from 0 at class start (then live tracking)
p.value = withTiming(progress, { duration: 600, easing: Easing.out(Easing.ease) });
// live: p.value = withTiming(live, { duration: 300 });

// Card tap scale
style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.98 : 1 }] })}

// Leaderboard reorder — use Reanimated layout animation
import Animated, { LinearTransition } from 'react-native-reanimated';
// <Animated.View layout={LinearTransition.duration(250)} key={rider.id}>

// PR burst
import Animated, { ZoomIn } from 'react-native-reanimated';
// entering={ZoomIn.springify().damping(12)}

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.selectionAsync();                                              // pill select, tab change
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);               // Take Class
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);   // PR / class complete
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons / MaterialCommunityIcons). Peloton ships custom metric glyphs — closest mappings below.

| Purpose | Ionicons / MCI |
|---------|----------------|
| Home (tab) | `home` |
| Classes (tab) | `play-circle` |
| Schedule (tab) | `stats-chart` |
| Profile (tab) | `person-circle` |
| Search | `search` |
| Filter | `options` |
| Play (thumbnail) | `play` |
| Rating star | `star` |
| Add to Stack | `add-circle-outline` |
| Cadence | MCI `metronome` |
| Resistance | MCI `speedometer` |
| Output / power | `flash` |
| Heart rate | `heart` |
| Leaderboard | `list` |
| PR / trophy | `trophy` |
| Music | `musical-notes` |

## 7. Platform Notes

- **Font choice**: Inter (all weights through Black) is SIL OFL — free to bundle. Peloton uses ExtraBold/Black heavily; ship those weights
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on the output number, metric values, and leaderboard outputs so live ticking and reorder don't shift layout
- **Status bar**: `<StatusBar style="light" />` — Peloton is always dark; never ship a light theme
- **Safe area**: wrap screens in `SafeAreaView`; sticky "Take Class" pill needs bottom safe-area padding; the in-class metric overlay must respect all safe-area edges over the video
- **Video**: the studio feed uses `expo-video` (or `react-native-video`) full-bleed; overlay the ring + leaderboard with absolute positioning
- **Dynamic Type**: RN respects system scale on `<Text>`; set `allowFontScaling={false}` on the output number, metric values/labels, leaderboard, tab labels, and badges
- **SVG**: use `react-native-svg` for the output and mini metric rings; animate via `react-native-reanimated` `useAnimatedProps`
- **Leaderboard reorder**: wrap rows in `Animated.View` with `layout={LinearTransition.duration(250)}` keyed by stable rider id so they slide as ranks change
- **Dark mode**: there is only one mode — do not branch on `useColorScheme()`; pin dark. Never recolor in-class metric colors
- **Accessibility**: announce LIVE as "Live class"; metric rows as "Cadence 94"; leaderboard rows include rank + name + output; keep text labels so color isn't the only signal
- **Reduce Motion**: gate the LIVE pulse, ring fill, card scale, leaderboard reorder, and PR burst behind `AccessibilityInfo.isReduceMotionEnabled()`
