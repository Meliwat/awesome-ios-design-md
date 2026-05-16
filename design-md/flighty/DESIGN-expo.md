# Flighty (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Flighty's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, and `react-native-svg` for the flight arc.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#0B0B0F',
  surface1: '#1A1A1F',
  surface2: '#222228',
  surface3: '#2C2C34',
  divider:  '#2E2E36',

  textPrimary:   '#FFFFFF',
  textSecondary: '#8E8E96',
  textTertiary:  '#5A5A62',

  blue:        '#0A84FF',
  bluePressed: '#0066CC',
  blueGlow:    'rgba(10,132,255,0.45)',

  // Status — strict semantics
  onTime:    '#30D158',
  delay:     '#FFD60A',
  cancelled: '#FF453A',

  // Map
  mapLand:      '#16161B',
  mapGraticule: '#1F1F26',
  arcRemaining: 'rgba(10,132,255,0.35)',
} as const;

export type FltColor = keyof typeof colors;
```

## 2. Typography

Flighty uses SF Pro (the iOS system font). Use `System` and tabular figures, or substitute Inter.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const ink = { color: '#FFFFFF' } satisfies TextStyle;
// Apply fontVariant: ['tabular-nums'] on every numeric flight field.
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  timeHero:   { ...ink, ...tnum, fontFamily: 'Inter-Bold',     fontSize: 32, lineHeight: 34, letterSpacing: -0.5 },
  titleLarge: { ...ink, fontFamily: 'Inter-Bold',     fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  routeCode:  { ...ink, fontFamily: 'Inter-Bold',     fontSize: 24, lineHeight: 26, letterSpacing: 0.5 },
  section:    { ...ink, fontFamily: 'Inter-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  cardTitle:  { ...ink, fontFamily: 'Inter-SemiBold', fontSize: 17, lineHeight: 21 },
  body:       { ...ink, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 22 },
  button:     { ...ink, fontFamily: 'Inter-SemiBold', fontSize: 16, lineHeight: 20 },
  status:     {          fontFamily: 'Inter-Bold',     fontSize: 13, lineHeight: 16, letterSpacing: 0.3 },
  gate:       { ...tnum, fontFamily: 'Inter-SemiBold', fontSize: 15, lineHeight: 18, letterSpacing: 0.5, color: '#8E8E96' },
  meta:       {          fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 17, color: '#8E8E96' },
  onTimePct:  { ...ink, ...tnum, fontFamily: 'Inter-Bold',     fontSize: 20, lineHeight: 22 },
  tab:        {          fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 13, letterSpacing: 0.2 },
  timestamp:  { ...tnum, fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 13, color: '#8E8E96' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Flight Status Chip

```tsx
// components/StatusChip.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Status = { kind: 'onTime' } | { kind: 'delayed'; mins: number } | { kind: 'cancelled' };

const COLOR = { onTime: colors.onTime, delayed: colors.delay, cancelled: colors.cancelled };

export function StatusChip({ status }: { status: Status }) {
  const c = COLOR[status.kind === 'delayed' ? 'delayed' : status.kind];
  const label =
    status.kind === 'onTime' ? 'ON TIME'
    : status.kind === 'delayed' ? `DELAYED ${status.mins}m`
    : 'CANCELLED';
  return (
    <View style={{ alignSelf: 'flex-start', backgroundColor: c + '26', /* ~15% */
                   paddingVertical: 5, paddingHorizontal: 10, borderRadius: 999 }}>
      <Text style={[typography.status, { color: c }]}>{label}</Text>
    </View>
  );
}
```

### Live Flight Map Arc

```tsx
// components/FlightArc.tsx
import { useEffect } from 'react';
import { View } from 'react-native';
import Svg, { Path } from 'react-native-svg';
import Animated, {
  useSharedValue, useAnimatedProps, withTiming, Easing,
} from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

const AnimatedPath = Animated.createAnimatedComponent(Path);

// origin/destination/control are points in a 0..100 viewBox
export function FlightArc({
  w, h, o, d, live,
}: {
  w: number; h: number;
  o: { x: number; y: number }; d: { x: number; y: number }; live: number; // 0..1
}) {
  const c = { x: (o.x + d.x) / 2, y: Math.min(o.y, d.y) - h * 0.18 };
  const path = `M ${o.x} ${o.y} Q ${c.x} ${c.y} ${d.x} ${d.y}`;
  const progress = useSharedValue(0);

  useEffect(() => {
    progress.value = withTiming(live, { duration: 900, easing: Easing.out(Easing.cubic) });
  }, [live]);

  // Approximate trim via strokeDasharray on a measured length
  const LEN = Math.hypot(d.x - o.x, d.y - o.y) * 1.25;
  const animProps = useAnimatedProps(() => ({
    strokeDashoffset: LEN * (1 - progress.value),
  }));

  const pt = (t: number) => ({
    x: (1 - t) ** 2 * o.x + 2 * (1 - t) * t * c.x + t * t * d.x,
    y: (1 - t) ** 2 * o.y + 2 * (1 - t) * t * c.y + t * t * d.y,
  });
  const plane = pt(live);

  return (
    <View style={{ width: w, height: h }}>
      <Svg width={w} height={h} viewBox={`0 0 ${w} ${h}`}>
        {/* Remaining: dashed dim */}
        <Path d={path} stroke={colors.arcRemaining} strokeWidth={2}
              strokeDasharray="4 5" fill="none" />
        {/* Flown: solid glowing (glow approximated via a wide translucent under-stroke) */}
        <Path d={path} stroke={colors.blueGlow} strokeWidth={9} fill="none" strokeLinecap="round" />
        <AnimatedPath d={path} stroke={colors.blue} strokeWidth={2.5} fill="none"
                      strokeLinecap="round" strokeDasharray={LEN} animatedProps={animProps} />
      </Svg>
      <Ionicons
        name="airplane" size={18} color="#FFFFFF"
        style={{ position: 'absolute', left: plane.x - 9, top: plane.y - 9,
                 textShadowColor: colors.blueGlow, textShadowRadius: 10 }}
      />
    </View>
  );
}
```

### Flight Card

```tsx
// components/FlightCard.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { StatusChip } from './StatusChip';

export function FlightCard({
  airline, flightNo, originCode, destCode, depTime, arrTime, depGate, arrGate, meta, status,
}: any) {
  return (
    <View style={{ backgroundColor: colors.surface1, borderRadius: 16, borderWidth: 1,
                   borderColor: colors.divider, padding: 18, gap: 16,
                   shadowColor: '#000', shadowOpacity: 0.4, shadowRadius: 16,
                   shadowOffset: { width: 0, height: 4 } }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
        <Text style={typography.cardTitle}>{airline} · {flightNo}</Text>
        <StatusChip status={status} />
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 16 }}>
        <Text style={typography.routeCode}>{originCode}</Text>
        <View style={{ flex: 1, alignItems: 'center' }}>
          <Ionicons name="airplane" size={14} color={colors.blue} />
        </View>
        <Text style={typography.routeCode}>{destCode}</Text>
      </View>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
        <View style={{ gap: 4 }}>
          <Text style={typography.timeHero}>{depTime}</Text>
          <Text style={typography.gate}>{depGate}</Text>
        </View>
        <View style={{ gap: 4, alignItems: 'flex-end' }}>
          <Text style={typography.timeHero}>{arrTime}</Text>
          <Text style={typography.gate}>{arrGate}</Text>
        </View>
      </View>
      <Text style={typography.meta}>{meta}</Text>
    </View>
  );
}
```

### On-Time Percentage Ring

```tsx
// components/OnTimeRing.tsx
import { Text, View } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function OnTimeRing({ percent, size = 72 }: { percent: number; size?: number }) {
  const r = size / 2 - 3;
  const circ = 2 * Math.PI * r;
  return (
    <View style={{ width: size, height: size }}>
      <Svg width={size} height={size}>
        <Circle cx={size / 2} cy={size / 2} r={r} stroke={colors.divider} strokeWidth={3} fill="none" />
        <Circle
          cx={size / 2} cy={size / 2} r={r} stroke={colors.onTime} strokeWidth={3} fill="none"
          strokeLinecap="round" strokeDasharray={circ}
          strokeDashoffset={circ * (1 - percent / 100)}
          transform={`rotate(-90 ${size / 2} ${size / 2})`}
        />
      </Svg>
      <View style={{ position: 'absolute', inset: 0, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={typography.onTimePct}>{percent}%</Text>
        <Text style={typography.timestamp}>on time</Text>
      </View>
    </View>
  );
}
```

### Primary Button

```tsx
// components/FltButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FltButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.bluePressed : colors.blue,
        paddingVertical: 15, borderRadius: 14, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

## 4. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.blue,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(11,11,15,0.92)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Flights', tabBarIcon: ({ color }) => <Ionicons name="airplane"  size={24} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search',  tabBarIcon: ({ color }) => <Ionicons name="search"    size={24} color={color} /> }} />
      <Tabs.Screen name="airport" options={{ title: 'Airport', tabBarIcon: ({ color }) => <Ionicons name="business"  size={24} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile', tabBarIcon: ({ color }) => <Ionicons name="person"    size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Arc draw-in — see FlightArc (withTiming 900ms, Easing.out(Easing.cubic))

// Plane advance — animate `live` toward the new fraction; the marker follows the curve
const live = useSharedValue(0);
live.value = withTiming(updatedFraction, { duration: 1000 });

// Status change — cross-fade chip color + single medium haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Latest timeline node pulse — withRepeat(withSequence(...)) scaling a glow ring

// Card press — scale 0.98 (Pressable style)
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons) mapped to Flighty's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Plane / arc | `airplane` |
| Search | `search` |
| Refresh | `refresh` |
| Notify | `notifications-outline` / `notifications` |
| Share | `share-outline` |
| Open map | `map-outline` / `map` |
| Add flight | `add` |
| On-time | `checkmark-circle` |
| Delay | `alert-circle-outline` |
| Flights (tab) | `airplane` |
| Airport (tab) | `business-outline` / `business` |
| Profile (tab) | `person-outline` / `person` |

## 7. Platform Notes

- **iOS-only feel**: use `expo-blur` (`tint="dark"`) for the tab bar `.regularMaterial` equivalent; Android falls back to an opaque `#0B0B0F`
- **Status bar**: set `<StatusBar style="light" />` from `expo-status-bar` — the deep-black canvas requires light content
- **Safe area**: wrap screens in `SafeAreaView`; the live map bleeds full-width with a subtle top scrim, the detail sheet respects bottom insets
- **Live Activity**: there is no Expo-Go ActivityKit API — use a dev-client + a native module (or a config plugin) to render the Lock Screen / Dynamic Island Live Activity that mirrors the in-app status bar
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on every numeric flight field so live updates don't shift layout; pin those with `allowFontScaling={false}`
- **Accessibility**: never rely on color alone for status — give the chip an `accessibilityLabel` (`"Delayed 22 minutes"`) and the arc a combined label (`"Flight 62% complete, SFO to JFK"`)
- **Performance**: animate the arc with Reanimated worklets (UI thread); avoid re-rendering the SVG on every live-position tick — drive the plane marker via a shared value
