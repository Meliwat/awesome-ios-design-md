# Hopper (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Hopper's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:         '#FFFFFF',
  surface:        '#F5F5F7',
  divider:        '#E5E5EA',
  surfacePressed: '#EBEBF0',

  textPrimary:    '#1D1D1F',
  textSecondary:  '#6E6E73',
  textTertiary:   '#A1A1A6',

  red:            '#FA4747',
  redPressed:     '#E03A3A',
  buyGreen:       '#34C759',

  // Heatmap (prediction scale: cheapest → most expensive)
  buyBest:        '#34C759',
  buyGood:        '#A8E6B8',
  neutral:        '#F5F5F7',
  waitHigh:       '#FFC7C7',
  waitWorst:      '#FA4747',

  priceDrop:      '#34C759',
  errorRed:       '#D70015',
} as const;

export type HopColor = keyof typeof colors;
```

## 2. Typography

Load Hopper's face via `expo-font`, or substitute Inter. Fall back to `System` (SF Pro on iOS).

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

const ink = { color: '#1D1D1F' } satisfies TextStyle;

export const typography = {
  price:      { ...ink, fontFamily: 'Inter-Bold',     fontSize: 28, lineHeight: 31, letterSpacing: -0.5 },
  titleLarge: { ...ink, fontFamily: 'Inter-Bold',     fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  section:    { ...ink, fontFamily: 'Inter-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  route:      { ...ink, fontFamily: 'Inter-Bold',     fontSize: 17, lineHeight: 21 },
  cardTitle:  { ...ink, fontFamily: 'Inter-SemiBold', fontSize: 16, lineHeight: 21 },
  body:       { ...ink, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 22 },
  button:     { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 20 },
  verdict:    { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 13, lineHeight: 16, letterSpacing: 0.2 },
  meta:       {          fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 17, color: '#6E6E73' },
  calDay:     {          fontFamily: 'Inter-SemiBold', fontSize: 14, lineHeight: 16 },
  calPrice:   {          fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 12 },
  tab:        {          fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 13, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Price-Prediction Calendar Heatmap

```tsx
// components/CalendarHeatmap.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type Prediction = 'buyBest' | 'buyGood' | 'neutral' | 'waitHigh' | 'waitWorst';

const FILL: Record<Prediction, string> = {
  buyBest: colors.buyBest, buyGood: colors.buyGood, neutral: colors.neutral,
  waitHigh: colors.waitHigh, waitWorst: colors.waitWorst,
};
const isStrong = (p: Prediction) => p === 'buyBest' || p === 'waitWorst';

export function DayCell({
  day, price, prediction, selected, onPress,
}: { day: number; price: string; prediction: Prediction; selected: boolean; onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  const fg = isStrong(prediction) ? '#FFFFFF' : colors.textPrimary;

  return (
    <Pressable
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
        scale.value = withSequence(withTiming(1.05, { duration: 100 }), withTiming(1, { duration: 100 }));
        onPress();
      }}
      style={{ flex: 1 }}
    >
      <Animated.View
        style={[
          { height: 44, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
            backgroundColor: FILL[prediction],
            borderWidth: selected ? 2 : 0, borderColor: colors.textPrimary },
          style,
        ]}
      >
        <Text style={[typography.calDay, { color: fg }]}>{day}</Text>
        <Text style={[typography.calPrice, { color: fg, opacity: 0.85 }]}>{price}</Text>
      </Animated.View>
    </Pressable>
  );
}

export function HeatmapLegend() {
  return (
    <View style={{ gap: 4 }}>
      <LinearGradient
        colors={[colors.buyBest, colors.buyGood, colors.neutral, colors.waitHigh, colors.waitWorst]}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
        style={{ height: 6, borderRadius: 3 }}
      />
      <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
        <Text style={[typography.meta, { color: colors.buyGreen }]}>Buy</Text>
        <Text style={[typography.meta, { color: colors.red }]}>Wait</Text>
      </View>
    </View>
  );
}
```

### Fare Verdict Pill

```tsx
// components/VerdictPill.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function VerdictPill({
  verdict, detail,
}: { verdict: 'buy' | 'wait'; detail?: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', alignSelf: 'flex-start',
                   backgroundColor: verdict === 'buy' ? colors.buyGreen : colors.red,
                   paddingVertical: 5, paddingHorizontal: 12, borderRadius: 999, gap: 4 }}>
      <Text style={typography.verdict}>{verdict === 'buy' ? 'BUY' : 'WAIT'}</Text>
      {detail && <Text style={[typography.meta, { color: '#FFFFFF', opacity: 0.9 }]}>· {detail}</Text>}
    </View>
  );
}
```

### Fare Card

```tsx
// components/FareCard.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { VerdictPill } from './VerdictPill';
import { HopButton } from './HopButton';

export function FareCard({
  route, airline, duration, price, verdict, confidence,
}: {
  route: string; airline: string; duration: string; price: string;
  verdict: 'buy' | 'wait'; confidence: number;
}) {
  const filled = Math.round((confidence / 100) * 4);
  return (
    <View style={{ backgroundColor: colors.canvas, borderRadius: 16, borderWidth: 1,
                   borderColor: colors.divider, padding: 20, gap: 14,
                   shadowColor: '#000', shadowOpacity: 0.06, shadowRadius: 16,
                   shadowOffset: { width: 0, height: 4 } }}>
      <View style={{ gap: 4 }}>
        <Text style={typography.route}>{route}</Text>
        <Text style={typography.meta}>{airline} · {duration}</Text>
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 10 }}>
        <Text style={typography.price}>{price}</Text>
        <VerdictPill verdict={verdict} detail={verdict === 'buy' ? "won't drop" : 'cheaper soon'} />
      </View>
      <View style={{ gap: 6 }}>
        <View style={{ flexDirection: 'row', gap: 4 }}>
          {[0, 1, 2, 3].map((i) => (
            <View key={i} style={{ flex: 1, height: 6, borderRadius: 3,
              backgroundColor: i < filled ? (verdict === 'buy' ? colors.buyGreen : colors.red) : colors.divider }} />
          ))}
        </View>
        <Text style={typography.meta}>Hopper is {confidence}% confident</Text>
      </View>
      <HopButton title="Watch this trip" onPress={() => {}} />
    </View>
  );
}
```

### Watch-Price Toggle

```tsx
// components/WatchToggle.tsx
import { Pressable } from 'react-native';
import Animated, { useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function WatchToggle({
  on, onToggle,
}: { on: boolean; onToggle: () => void }) {
  const knob = useAnimatedStyle(() => ({
    transform: [{ translateX: withSpring(on ? 22 : 0, { damping: 14 }) }],
  }));
  return (
    <Pressable
      onPress={() => { Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); onToggle(); }}
      style={{ width: 52, height: 32, borderRadius: 16, padding: 2,
               backgroundColor: on ? colors.red : colors.divider, justifyContent: 'center' }}
    >
      <Animated.View
        style={[
          { width: 28, height: 28, borderRadius: 14, backgroundColor: '#FFFFFF',
            alignItems: 'center', justifyContent: 'center',
            shadowColor: '#000', shadowOpacity: 0.12, shadowRadius: 4, shadowOffset: { width: 0, height: 2 } },
          knob,
        ]}
      >
        {on && <Ionicons name="notifications" size={12} color={colors.red} />}
      </Animated.View>
    </Pressable>
  );
}
```

### Primary Button

```tsx
// components/HopButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function HopButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.redPressed : colors.red,
        paddingVertical: 16, borderRadius: 14, alignItems: 'center',
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
        tabBarActiveTintColor: colors.red,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="light" style={{ flex: 1, backgroundColor: 'rgba(255,255,255,0.92)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home"          size={24} color={color} /> }} />
      <Tabs.Screen name="watches" options={{ title: 'Watches', tabBarIcon: ({ color }) => <Ionicons name="notifications" size={24} color={color} /> }} />
      <Tabs.Screen name="trips"   options={{ title: 'Trips',   tabBarIcon: ({ color }) => <Ionicons name="briefcase"     size={24} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile', tabBarIcon: ({ color }) => <Ionicons name="person"        size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Calendar date select — cell pop + light haptic (see DayCell)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Heatmap color shift — when predictions change, animate each cell's backgroundColor
const bg = useDerivedValue(() => withTiming(FILL[prediction], { duration: 300 }));

// Watch toggle arm — knob spring + success haptic (see WatchToggle)
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Price-drop celebration — present a modal route, tick a counter, animate the bunny
// Verdict pill — entrance scale
const v = useSharedValue(0.94);
const vStyle = useAnimatedStyle(() => ({ transform: [{ scale: withSpring(v.value) }] }));
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons) mapped to Hopper's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Watch / bell | `notifications-outline` / `notifications` |
| Search | `search` |
| Swap route | `swap-vertical` |
| Calendar | `calendar-outline` |
| Plane | `airplane` |
| Price drop | `arrow-down` |
| Prediction info | `information-circle-outline` |
| Back | `chevron-back` |
| Home (tab) | `home-outline` / `home` |
| Watches (tab) | `notifications-outline` / `notifications` |
| Trips (tab) | `briefcase-outline` / `briefcase` |
| Profile (tab) | `person-outline` / `person` |

## 7. Platform Notes

- **iOS-only feel**: use `expo-blur` (`tint="light"`) for the tab bar `.regularMaterial` equivalent; Android falls back to an opaque white background
- **Status bar**: set `<StatusBar style="dark" />` from `expo-status-bar` — the white canvas requires dark content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the calendar grid respects the same 16pt insets
- **Dynamic Type**: React Native respects user font-scaling by default; set `allowFontScaling={false}` on calendar day/price labels, verdict pills, and tab labels (layout-sensitive)
- **Accessibility**: never rely on color alone for the verdict — give each calendar cell an `accessibilityLabel` like `"12, predicted $189, good price to buy"` and the verdict block a combined label; expose the watch toggle as `accessibilityRole="switch"`
- **Color-blind safety**: the per-cell price text and the literal "BUY"/"WAIT" label ensure the red/green heatmap is never the only channel of meaning
