# Fitbit (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Fitbit's encouraging health-dashboard language into paste-ready Expo / React Native code: a design-token module, themed components (rings, tiles, scores), and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — default)
  canvas:    '#001017',
  surface1:  '#002A3A',
  surface2:  '#00384D',
  surface3:  '#0A475E', // ring track
  divider:   '#143E50',

  // Surfaces (light — secondary)
  canvasLight:   '#FFFFFF',
  surface1Light: '#F2F7F8',
  surface3Light: '#E3EEF0',
  dividerLight:  '#DCE7E9',

  // Brand
  teal:        '#00B0B9',
  tealBright:  '#21D9CE', // on-dark text/link/active tab
  tealPressed: '#008A91',
  tealTint:    'rgba(0,176,185,0.15)',

  // Per-metric hues (fixed across themes)
  steps:     '#00B0B9',
  heartRate: '#FF6B81',
  sleep:     '#7C5CFF',
  readiness: '#B8E986',
  zone:      '#FF8A3D',
  calories:  '#FFC233',
  spo2:      '#4FC3F7',

  // Text
  textPrimary:    '#EAF6F9',
  textSecondary:  '#8AAEB8',
  textTertiary:   '#5C808B',
  textPrimaryLt:  '#0B2A33',

  // Semantic
  success: '#3FC7A6',
  warning: '#FF8A3D',
  alert:   '#FF6B81',
} as const;

export const readinessGradient = ['#0E3A2E', '#14564A'] as const;

export type FitbitColor = keyof typeof colors;
```

## 2. Typography

Load **DM Sans** (SIL OFL) via `expo-font` as the open stand-in for Fitbit's brand sans. The number is always the hero.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'DMSans-Regular':  require('../assets/fonts/DMSans-Regular.ttf'),
    'DMSans-Medium':   require('../assets/fonts/DMSans-Medium.ttf'),
    'DMSans-SemiBold': require('../assets/fonts/DMSans-SemiBold.ttf'),
    'DMSans-Bold':     require('../assets/fonts/DMSans-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  metricHero:  { fontFamily: 'DMSans-Bold',     fontSize: 44, lineHeight: 46, letterSpacing: -1.0, color: '#EAF6F9' },
  screenTitle: { fontFamily: 'DMSans-Bold',     fontSize: 32, lineHeight: 37, letterSpacing: -0.5, color: '#EAF6F9' },
  tileValue:   { fontFamily: 'DMSans-Bold',     fontSize: 26, lineHeight: 28, letterSpacing: -0.5, color: '#EAF6F9' },
  greeting:    { fontFamily: 'DMSans-Bold',     fontSize: 22, lineHeight: 27, letterSpacing: -0.3, color: '#EAF6F9' },
  cardTitle:   { fontFamily: 'DMSans-Bold',     fontSize: 18, lineHeight: 23, color: '#EAF6F9' },
  body:        { fontFamily: 'DMSans-Regular',  fontSize: 16, lineHeight: 24, color: '#EAF6F9' },
  valueInline: { fontFamily: 'DMSans-SemiBold', fontSize: 15, lineHeight: 20, color: '#EAF6F9' },
  label:       { fontFamily: 'DMSans-Regular',  fontSize: 14, lineHeight: 19, color: '#8AAEB8' },
  unit:        { fontFamily: 'DMSans-SemiBold', fontSize: 13, lineHeight: 13, color: '#8AAEB8' },
  caption:     { fontFamily: 'DMSans-SemiBold', fontSize: 12, lineHeight: 16, color: '#8AAEB8' },
  tab:         { fontFamily: 'DMSans-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  button:      { fontFamily: 'DMSans-Bold',     fontSize: 15, lineHeight: 15, color: '#001017' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Progress Ring (the signature shape)

```tsx
// components/MetricRing.tsx
import { useEffect } from 'react';
import { View } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming, Easing } from 'react-native-reanimated';
import { colors } from '../theme/colors';

const ACircle = Animated.createAnimatedComponent(Circle);

export function MetricRing({
  size = 116, stroke = 11, progress, color,
}: { size?: number; stroke?: number; progress: number; color: string }) {
  const r = (size - stroke) / 2;
  const c = 2 * Math.PI * r;
  const p = useSharedValue(0);

  useEffect(() => { p.value = withTiming(Math.min(progress, 1), { duration: 900, easing: Easing.out(Easing.cubic) }); }, [progress]);

  const animatedProps = useAnimatedProps(() => ({ strokeDashoffset: c * (1 - p.value) }));

  return (
    <Svg width={size} height={size}>
      <Circle cx={size / 2} cy={size / 2} r={r} stroke={colors.surface3} strokeWidth={stroke} fill="none" />
      <ACircle
        cx={size / 2} cy={size / 2} r={r}
        stroke={color} strokeWidth={stroke} fill="none"
        strokeDasharray={c} strokeLinecap="round"
        animatedProps={animatedProps}
        transform={`rotate(-90 ${size / 2} ${size / 2})`}
      />
    </Svg>
  );
}
```

### Steps Ring Hero

```tsx
// components/StepsHero.tsx
import { View, Text } from 'react-native';
import { MetricRing } from './MetricRing';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StepsHero({
  steps, goal, headline, support, trend,
}: { steps: number; goal: number; headline: string; support: string; trend: string }) {
  return (
    <View style={{ marginHorizontal: 20, backgroundColor: colors.surface1, borderRadius: 24, padding: 22,
      flexDirection: 'row', alignItems: 'center', gap: 18 }}>
      <View style={{ width: 116, height: 116, alignItems: 'center', justifyContent: 'center' }}>
        <MetricRing progress={steps / goal} color={colors.steps} />
        <View style={{ position: 'absolute', alignItems: 'center' }}>
          <Text style={typography.tileValue}>{steps.toLocaleString()}</Text>
          <Text style={{ fontFamily: 'DMSans-Regular', fontSize: 11, color: colors.textSecondary }}>
            of {goal.toLocaleString()} steps
          </Text>
        </View>
      </View>
      <View style={{ flex: 1, gap: 3 }}>
        <Text style={typography.valueInline}>{headline}</Text>
        <Text style={{ fontFamily: 'DMSans-Regular', fontSize: 13, color: colors.textSecondary }}>{support}</Text>
        <View style={{ alignSelf: 'flex-start', marginTop: 6, backgroundColor: colors.tealTint,
          paddingHorizontal: 10, paddingVertical: 5, borderRadius: 999 }}>
          <Text style={[typography.caption, { color: colors.tealBright }]}>{trend}</Text>
        </View>
      </View>
    </View>
  );
}
```

### Metric Tile

```tsx
// components/MetricTile.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MetricTile({
  metricColor, icon, label, value, unit, sub,
}: {
  metricColor: string; icon: any; label: string;
  value: string; unit?: string; sub: string;
}) {
  return (
    <View style={{ flex: 1, backgroundColor: colors.surface1, borderRadius: 20, padding: 16 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 7 }}>
        <View style={{ width: 24, height: 24, borderRadius: 7, backgroundColor: metricColor,
          alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name={icon} size={13} color={colors.canvas} />
        </View>
        <Text style={typography.caption}>{label}</Text>
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 3, marginTop: 10 }}>
        <Text style={typography.tileValue}>{value}</Text>
        {unit ? <Text style={[typography.unit, { marginBottom: 3 }]}>{unit}</Text> : null}
      </View>
      <Text style={{ fontFamily: 'DMSans-Regular', fontSize: 12, color: colors.textTertiary, marginTop: 3 }}>
        {sub}
      </Text>
    </View>
  );
}
```

### Score Badge + Daily Readiness Card

```tsx
// components/ReadinessCard.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Svg, { Circle } from 'react-native-svg';
import { colors, readinessGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function ScoreBadge({ score, color, size = 60 }: { score: number; color: string; size?: number }) {
  const r = (size - 4) / 2;
  return (
    <View style={{ width: size, height: size, alignItems: 'center', justifyContent: 'center' }}>
      <Svg width={size} height={size}>
        <Circle cx={size / 2} cy={size / 2} r={r} stroke={color} strokeWidth={4} fill="none" />
      </Svg>
      <Text style={{ position: 'absolute', fontFamily: 'DMSans-Bold', fontSize: size * 0.36, color }}>
        {score}
      </Text>
    </View>
  );
}

export function ReadinessCard({ score, message }: { score: number; message: string }) {
  return (
    <LinearGradient colors={readinessGradient as unknown as string[]}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
      style={{ marginHorizontal: 20, borderRadius: 20, padding: 18,
        flexDirection: 'row', alignItems: 'center', gap: 16 }}>
      <ScoreBadge score={score} color={colors.readiness} size={60} />
      <View style={{ flex: 1, gap: 3 }}>
        <Text style={typography.valueInline}>Daily Readiness</Text>
        <Text style={{ fontFamily: 'DMSans-Regular', fontSize: 12, color: '#A9D9C8' }}>{message}</Text>
      </View>
    </LinearGradient>
  );
}
```

### Primary Pill Button

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress?: () => void }) {
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        height: 52, borderRadius: 999, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.tealPressed : colors.teal,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Count-Up Number

```tsx
// components/CountUp.tsx
import { useEffect, useState } from 'react';
import { Text, TextStyle } from 'react-native';

export function CountUp({ target, style, duration = 700 }: { target: number; style?: TextStyle; duration?: number }) {
  const [val, setVal] = useState(0);
  useEffect(() => {
    const start = Date.now();
    const id = setInterval(() => {
      const t = Math.min(1, (Date.now() - start) / duration);
      const eased = 1 - Math.pow(1 - t, 3); // ease-out cubic
      setVal(Math.round(target * eased));
      if (t >= 1) clearInterval(id);
    }, 16);
    return () => clearInterval(id);
  }, [target]);
  return <Text style={style}>{val.toLocaleString()}</Text>;
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
    <Tabs screenOptions={{
      headerShown: false,
      tabBarActiveTintColor: colors.tealBright,
      tabBarInactiveTintColor: colors.textTertiary,
      tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 1, borderTopColor: colors.divider },
      tabBarLabelStyle: { fontFamily: 'DMSans-SemiBold', fontSize: 10, letterSpacing: 0.1 },
    }}>
      <Tabs.Screen name="index"     options={{ title: 'Today',     tabBarIcon: ({ color }) => <Ionicons name="ellipse-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="coach"     options={{ title: 'Coach',     tabBarIcon: ({ color }) => <Ionicons name="walk"            size={22} color={color} /> }} />
      <Tabs.Screen name="community" options={{ title: 'Community', tabBarIcon: ({ color }) => <Ionicons name="people"          size={22} color={color} /> }} />
      <Tabs.Screen name="you"       options={{ title: 'You',       tabBarIcon: ({ color }) => <Ionicons name="person-circle"   size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { FadeInDown, useSharedValue, withTiming, withSpring, Easing } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Ring sweep — withTiming 900ms ease-out cubic (see MetricRing)

// Number count-up — 700ms ease-out cubic (see CountUp)

// Today card stagger-in — Reanimated entering with index delay
// <Animated.View entering={FadeInDown.duration(350).delay(index * 40)}>

// Tile tap press — scale 1 → 0.97 → 1
scale.value = withTiming(0.97, { duration: 60 }, () => { scale.value = withTiming(1, { duration: 60 }); });

// Goal celebration — ring pulse + confetti (react-native-confetti-cannon) + success haptic
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Range switch — chart cross-fade (Animated opacity 400ms)

// Sleep stage bar — segments grow sequentially (stagger withTiming per segment)

// Haptics
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // goal hit / milestone
Haptics.selectionAsync();                                            // range / segment change
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);              // tile tap
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons / MaterialCommunityIcons). Per-metric glyphs render in `#001017` (deep ink) on their colored chip.

| Purpose | Ionicons |
|---------|----------|
| Today (tab) | `ellipse-outline` |
| Coach (tab) | `walk` |
| Community (tab) | `people` |
| You (tab) | `person-circle` |
| Steps | `footsteps` |
| Heart Rate | `heart` |
| Sleep | `moon` |
| Active Zone | `flash` |
| Calories | `flame` |
| Readiness | `checkmark-circle` |
| SpO2 | `pulse` |
| Distance | `location` |
| Trend up | `arrow-up` |
| Trend down | `arrow-down` |
| Back | `chevron-back` |
| Share | `share-outline` |
| More | `ellipsis-horizontal` |
| Add / Log | `add` |
| Settings | `settings-outline` |
| Notifications | `notifications-outline` |

## 7. Platform Notes

- **Fonts**: DM Sans is SIL OFL — free to bundle. It's a faithful open stand-in for Fitbit's brand sans; substitute the licensed face if available
- **Rings**: use `react-native-svg` with an animated `strokeDashoffset` (see `MetricRing`); rounded `strokeLinecap` is essential to the Fitbit look. For perf, memoize rings and only re-animate when the value changes
- **Status bar**: `<StatusBar style="light" />` for the teal-black dark theme; `"dark"` for the light theme
- **Safe area**: wrap screens in `SafeAreaView`; the greeting/avatar row should clear the Dynamic Island; the tab bar needs bottom safe-area padding
- **Dynamic Type**: allow scaling on greeting/titles/body/labels; set `allowFontScaling={false}` on unit suffixes, tab labels, and trend-chip text; let hero numbers use `adjustsFontSizeToFit numberOfLines={1}`
- **Per-metric color**: keep a single source of truth (`colors.steps`, `colors.heartRate`, …) and always render the metric icon + label alongside the hue — never rely on color alone (accessibility)
- **Health data**: request HealthKit scopes via `expo-health`/native module; never log raw biometric values; gate sensitive detail screens behind `expo-local-authentication` where appropriate
- **Charts**: use `react-native-svg` or `victory-native`/`react-native-skia`; draw bars/lines in the metric color, gridlines `#143E50`, rounded bar caps
- **Confetti**: use `react-native-confetti-cannon` for goal celebrations; trigger with a success haptic
- **Dark mode**: default to the teal-dark theme; if supporting light, swap only `canvas`→`#FFFFFF` and `textPrimary`→`#0B2A33` via `useColorScheme()` — every per-metric hue stays identical; on dark use `tealBright` for text/links/active tab
- **Accessibility**: set `accessibilityLabel` on rings ("Steps, 7,842 of 10,000, 78 percent") and tiles ("Heart Rate, 68 bpm, resting"); honor `AccessibilityInfo.isReduceMotionEnabled` to skip ring sweep / count-up / confetti
