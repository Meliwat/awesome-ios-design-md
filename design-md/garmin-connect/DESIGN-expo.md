# Garmin Connect (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Garmin Connect's data-cockpit visual language into paste-ready Expo / React Native: a design-token module, themed components, an SVG GPS route, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — primary)
  canvas:    '#000000',
  surface1:  '#121417',
  surface2:  '#1B1E22',
  surface3:  '#23272C',
  divider:   '#2A2E33',

  // Surfaces (light — optional)
  lightCanvas:  '#FFFFFF',
  lightSurface: '#F4F5F7',
  lightDivider: '#E2E5E9',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#A6ACB3',
  textTertiary:  '#6B7178',
  lightTextPrimary: '#1A1D21',

  // Brand (single accent)
  blue:        '#007CC3',
  blueOnDark:  '#2A9FD6',
  bluePressed: '#00689F',

  // Functional / data-viz (fixed meaning, theme-invariant)
  bodyBattery:   '#2EA8E0',
  bodyBatteryLo: '#1E6FA8',
  intensity:     '#00A8A8',
  steps:         '#C8B560',
  zone1:         '#9AA0A6',
  zone2:         '#4CAF50',
  zone3:         '#C8B560',
  zone4:         '#F0A030',
  zone5:         '#E5402A',

  // Semantic
  success: '#4CAF50',
  warning: '#F0A030',
  error:   '#E5402A',
  track:   '#23272C',
} as const;

export type GCColor = keyof typeof colors;

// Fixed HR-zone palette — never recolor for theme
export const hrZones = [
  { name: 'Z1', color: colors.zone1 },
  { name: 'Z2', color: colors.zone2 },
  { name: 'Z3', color: colors.zone3 },
  { name: 'Z4', color: colors.zone4 },
  { name: 'Z5', color: colors.zone5 },
] as const;
```

## 2. Typography

Load Roboto (UI/body) and Roboto Condensed (metrics) via `expo-font`. Both Apache 2.0.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Roboto-Regular':  require('../assets/fonts/Roboto-Regular.ttf'),
    'Roboto-Medium':   require('../assets/fonts/Roboto-Medium.ttf'),
    'Roboto-Bold':     require('../assets/fonts/Roboto-Bold.ttf'),
    'Roboto-Black':    require('../assets/fonts/Roboto-Black.ttf'),
    'RobotoCondensed-Bold':    require('../assets/fonts/RobotoCondensed-Bold.ttf'),
    'RobotoCondensed-Regular': require('../assets/fonts/RobotoCondensed-Regular.ttf'),
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
  // Roboto — UI / body / labels
  screenTitle: { color: '#FFFFFF', fontFamily: 'Roboto-Black',  fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  section:     { color: '#FFFFFF', fontFamily: 'Roboto-Bold',   fontSize: 20, lineHeight: 24, letterSpacing: -0.1 },
  body:        { color: '#FFFFFF', fontFamily: 'Roboto-Medium', fontSize: 16, lineHeight: 24 },
  bodyRegular: { color: '#FFFFFF', fontFamily: 'Roboto-Regular',fontSize: 16, lineHeight: 24 },
  meta:        { color: '#A6ACB3', fontFamily: 'Roboto-Regular',fontSize: 14, lineHeight: 20 },
  eyebrow:     { color: '#A6ACB3', fontFamily: 'Roboto-Bold',   fontSize: 12, lineHeight: 15, letterSpacing: 0.5, textTransform: 'uppercase' as const },
  unit:        { color: '#6B7178', fontFamily: 'Roboto-Medium', fontSize: 11, lineHeight: 13 },
  caption:     { color: '#6B7178', fontFamily: 'Roboto-Medium', fontSize: 11, lineHeight: 14 },
  button:      { color: '#FFFFFF', fontFamily: 'Roboto-Bold',   fontSize: 15, lineHeight: 15, letterSpacing: 0.5, textTransform: 'uppercase' as const },
  tab:         { color: '#6B7178', fontFamily: 'Roboto-Medium', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  axis:        { color: '#6B7178', fontFamily: 'Roboto-Medium', fontSize: 10, lineHeight: 10 },

  // Roboto Condensed — metrics (tabular)
  heroMetric:   { ...TABULAR, color: '#FFFFFF', fontFamily: 'RobotoCondensed-Bold', fontSize: 40, lineHeight: 42, letterSpacing: -0.5 },
  activityName: { ...TABULAR, color: '#FFFFFF', fontFamily: 'RobotoCondensed-Bold', fontSize: 26, lineHeight: 29, letterSpacing: -0.2 },
  statValue:    { ...TABULAR, color: '#FFFFFF', fontFamily: 'RobotoCondensed-Bold', fontSize: 22, lineHeight: 24 },
  statValueLg:  { ...TABULAR, color: '#FFFFFF', fontFamily: 'RobotoCondensed-Bold', fontSize: 28, lineHeight: 30 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Activity Detail Header

```tsx
// components/ActivityHeader.tsx
import { View, Text } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function ActivityHeader({ type, name, when }: { type: string; name: string; when: string }) {
  return (
    <View style={{ paddingHorizontal: 16, paddingBottom: 16 }}>
      <Text style={[typography.eyebrow, { color: colors.blueOnDark }]}>{type}</Text>
      <Text style={[typography.activityName, { marginTop: 4 }]}>{name}</Text>
      <Text style={[typography.meta, { marginTop: 4 }]}>{when}</Text>
    </View>
  );
}
```

### GPS Route Map (SVG)

```tsx
// components/RouteMap.tsx
import { useEffect } from 'react';
import { View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Svg, { Path, Circle } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';

const APath = Animated.createAnimatedComponent(Path);

export function RouteMap({ d, start, end, length = 1 }:
  { d: string; start: { x: number; y: number }; end: { x: number; y: number }; length?: number }) {
  const draw = useSharedValue(length);
  useEffect(() => { draw.value = withTiming(0, { duration: 700 }); }, []);
  const animatedProps = useAnimatedProps(() => ({ strokeDashoffset: draw.value }));

  return (
    <View style={{ height: 168, marginHorizontal: 16, borderRadius: 12, overflow: 'hidden' }}>
      <LinearGradient colors={['#1A2329', '#0C1216']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ position: 'absolute', inset: 0 }} />
      <Svg width="100%" height="100%" viewBox="0 0 300 168" preserveAspectRatio="none">
        <APath d={d} stroke={colors.blue} strokeWidth={4} fill="none"
          strokeLinecap="round" strokeLinejoin="round"
          strokeDasharray={length} animatedProps={animatedProps} />
        <Circle cx={start.x} cy={start.y} r={7} fill={colors.success} stroke="#FFF" strokeWidth={3} />
        <Circle cx={end.x}   cy={end.y}   r={7} fill={colors.error}   stroke="#FFF" strokeWidth={3} />
      </Svg>
    </View>
  );
}
```

### Stat Grid

```tsx
// components/StatGrid.tsx
import { View, Text } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

type Stat = { label: string; value: string; unit?: string };

export function StatGrid({ stats }: { stats: Stat[] }) {
  return (
    <View style={{
      flexDirection: 'row', flexWrap: 'wrap', marginHorizontal: 16,
      backgroundColor: colors.divider, borderRadius: 12, overflow: 'hidden',
    }}>
      {stats.map((s, i) => (
        <View key={i} style={{
          width: '33.333%', backgroundColor: colors.surface1,
          paddingVertical: 14, paddingHorizontal: 12, minHeight: 64,
          borderRightWidth: (i % 3 === 2) ? 0 : 1, borderBottomWidth: 1,
          borderColor: colors.divider,
        }}>
          <Text style={typography.eyebrow}>{s.label}</Text>
          <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 3, marginTop: 4 }}>
            <Text style={typography.statValue}>{s.value}</Text>
            {s.unit ? <Text style={typography.unit}>{s.unit}</Text> : null}
          </View>
        </View>
      ))}
    </View>
  );
}
```

### Training Status Card (Ring)

```tsx
// components/TrainingStatusCard.tsx
import { useEffect } from 'react';
import { View, Text } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming, Easing } from 'react-native-reanimated';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

const ACircle = Animated.createAnimatedComponent(Circle);
const R = 22, C = 2 * Math.PI * R;

export function TrainingStatusCard({ progress, status, detail }:
  { progress: number; status: string; detail: string }) {
  const p = useSharedValue(0);
  useEffect(() => { p.value = withTiming(progress, { duration: 600, easing: Easing.out(Easing.ease) }); }, []);
  const animatedProps = useAnimatedProps(() => ({ strokeDashoffset: C * (1 - p.value) }));

  return (
    <View style={card}>
      <Svg width={52} height={52}>
        <Circle cx={26} cy={26} r={R} stroke={colors.track} strokeWidth={6} fill="none" />
        <ACircle cx={26} cy={26} r={R} stroke={colors.blue} strokeWidth={6} fill="none"
          strokeLinecap="round" strokeDasharray={C} animatedProps={animatedProps}
          transform="rotate(-90 26 26)" />
      </Svg>
      <View style={{ flex: 1, marginLeft: 14 }}>
        <Text style={typography.eyebrow}>Training Status</Text>
        <Text style={[typography.body, { color: colors.blueOnDark, marginTop: 2 }]}>{status}</Text>
        <Text style={[typography.unit, { marginTop: 2 }]}>{detail}</Text>
      </View>
    </View>
  );
}

const card = {
  flexDirection: 'row' as const, alignItems: 'center' as const,
  marginHorizontal: 16, padding: 16, borderRadius: 12,
  backgroundColor: colors.surface1, borderWidth: 1, borderColor: colors.divider,
};
```

### Body Battery Gauge

```tsx
// components/BodyBatteryCard.tsx
import { useEffect } from 'react';
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function BodyBatteryCard({ value }: { value: number }) {
  const w = useSharedValue(0);
  useEffect(() => { w.value = withTiming(value / 100, { duration: 600 }); }, []);
  const fill = useAnimatedStyle(() => ({ width: `${w.value * 100}%` }));

  return (
    <View style={{ marginHorizontal: 16, padding: 16, borderRadius: 12, backgroundColor: colors.surface1, borderWidth: 1, borderColor: colors.divider }}>
      <View style={{ flexDirection: 'row', alignItems: 'baseline', justifyContent: 'space-between' }}>
        <Text style={{ ...typography.body, fontFamily: 'Roboto-Medium', fontSize: 13 }}>Body Battery</Text>
        <Text style={[typography.statValue, { color: colors.bodyBattery }]}>{value}</Text>
      </View>
      <View style={{ height: 8, borderRadius: 4, backgroundColor: colors.track, marginTop: 12, overflow: 'hidden' }}>
        <Animated.View style={fill}>
          <LinearGradient colors={[colors.bodyBatteryLo, colors.bodyBattery]}
            start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }} style={{ flex: 1, borderRadius: 4 }} />
        </Animated.View>
      </View>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginTop: 6 }}>
        <Text style={typography.axis}>0</Text>
        <Text style={typography.axis}>Charged</Text>
        <Text style={typography.axis}>100</Text>
      </View>
    </View>
  );
}
```

### Heart Rate Zones

```tsx
// components/HRZonesCard.tsx
import { View, Text } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

type Zone = { name: string; frac: number; dur: string; color: string };

export function HRZonesCard({ zones }: { zones: Zone[] }) {
  return (
    <View style={{ marginHorizontal: 16, padding: 16, borderRadius: 12, backgroundColor: colors.surface1, borderWidth: 1, borderColor: colors.divider }}>
      <Text style={{ ...typography.body, fontFamily: 'Roboto-Medium', fontSize: 13, marginBottom: 12 }}>Heart Rate Zones</Text>
      {zones.map((z) => (
        <View key={z.name} style={{ flexDirection: 'row', alignItems: 'center', gap: 10, marginBottom: 8 }}>
          <Text style={[typography.caption, { color: colors.textSecondary, width: 30 }]}>{z.name}</Text>
          <View style={{ flex: 1, height: 10, borderRadius: 3, backgroundColor: colors.track, overflow: 'hidden' }}>
            <View style={{ height: '100%', width: `${z.frac * 100}%`, borderRadius: 3, backgroundColor: z.color }} />
          </View>
          <Text style={[typography.caption, { color: colors.textSecondary, width: 40, textAlign: 'right' }]}>{z.dur}</Text>
        </View>
      ))}
    </View>
  );
}
```

### Primary Button

```tsx
// components/GCButton.tsx
import { Pressable, Text } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function GCButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.bluePressed : colors.blue,
        borderRadius: 6, paddingVertical: 14, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={typography.button}>{title}</Text>
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
        tabBarActiveTintColor:  colors.blueOnDark,   // brightened Garmin Blue, no pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.surface1, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Roboto-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}>
      <Tabs.Screen name="index"      options={{ title: 'My Day',     tabBarIcon: ({ color }) => <Ionicons name="ellipse-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="challenges" options={{ title: 'Challenges', tabBarIcon: ({ color }) => <Ionicons name="trending-up"    size={22} color={color} /> }} />
      <Tabs.Screen name="calendar"   options={{ title: 'Calendar',   tabBarIcon: ({ color }) => <Ionicons name="calendar-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="more"       options={{ title: 'More',       tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Route line draws on — animate SVG strokeDashoffset length → 0 over 700ms
draw.value = withTiming(0, { duration: 700, easing: Easing.out(Easing.ease) });

// Ring / gauge fill from 0 (slight overshoot)
p.value = withSpring(progress, { damping: 16, stiffness: 120 });

// Bar fill
w.value = withTiming(value / 100, { duration: 600 });

// Stat cells stagger-rise
entering={FadeInDown.duration(200).delay(index * 30)}

// Card expand (Splits / HR Zones)
import Animated, { useAnimatedStyle, withTiming } from 'react-native-reanimated';
const chevron = useAnimatedStyle(() => ({ transform: [{ rotate: `${open ? 90 : 0}deg` }] }));
// content: <Animated.View entering={FadeIn} exiting={FadeOut}>

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);                    // tab change, goal met
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);       // sync complete
Haptics.selectionAsync();                                                  // range segment change
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons / MaterialCommunityIcons). Garmin ships custom activity glyphs — closest mappings below.

| Purpose | Ionicons / MCI |
|---------|----------------|
| My Day (tab) | `ellipse-outline` / `ellipse` |
| Challenges (tab) | `trending-up` |
| Calendar (tab) | `calendar-outline` |
| More (tab) | `person-circle-outline` |
| Back | `chevron-back` |
| Settings | `settings-outline` |
| Share | `share-social-outline` |
| Expand card | `chevron-forward` → `chevron-down` |
| Heart rate | `heart` |
| Run | MCI `run` |
| Cycling | MCI `bike` |
| Swim | MCI `swim` |
| Elevation | MCI `image-filter-hdr` |
| Body Battery | `flash` |
| Steps | `walk` |
| Map / route | `map-outline` |
| Sync | `sync` |
| Goal met | `checkmark-circle` |

## 7. Platform Notes

- **Font choice**: Roboto + Roboto Condensed are both Apache 2.0 — free to bundle. Ship Condensed weights specifically for numerals; do not substitute a proportional font for metrics
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on every numeric `<Text>` so split tables and live activity values don't shift layout
- **Status bar**: `<StatusBar style="light" />` (dark-first app). On the optional light theme, `"dark"`
- **Safe area**: wrap screens in `SafeAreaView`; the full-width primary button needs bottom safe-area padding above the home indicator
- **Dynamic Type**: RN respects system scale on `<Text>`; set `allowFontScaling={false}` on stat values, units, tab labels, chart axis, and eyebrow labels (tight cells must not reflow)
- **SVG**: use `react-native-svg` for the route polyline and ring arcs; animate via `react-native-reanimated`'s `useAnimatedProps`
- **Maps**: for a real basemap use `react-native-maps` with a dark style JSON; overlay the route `Polyline` in `#007CC3` with green start / red end markers
- **Dark mode**: use `useColorScheme()` only to swap chrome tokens — never recolor functional/data-viz colors
- **Accessibility**: combine label+value+unit into one `accessibilityLabel`; HR-zone bars must keep the Z2…Z5 text label so color isn't the only signal; expose the route map with a summary label
- **Reduce Motion**: gate the route draw-on, ring/bar fill, and stagger behind `AccessibilityInfo.isReduceMotionEnabled()` — fall back to instant final values
