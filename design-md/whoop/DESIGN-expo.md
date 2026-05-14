# WHOOP (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates WHOOP's instrument-panel aesthetic into paste-ready Expo / React Native code: a design-token module, themed components, the Recovery ring drawn with `react-native-svg`, and Reanimated for the strain marker spring.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, `react-native-reanimated` v3, and `react-native-svg`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark-mode-only)
  canvas:        '#0A0A0A',
  surface1:      '#1C1C1E',
  surface2:      '#2A2A2A',
  surface3:      '#3A3A3A',
  divider:       '#252525',
  hairline:      '#1A1A1A',

  // Strain (primary brand)
  strain:        '#00FF7B',
  strainBright:  '#67E26B',
  strainDim:     '#005F2F',

  // Recovery spectrum
  recoveryRed:    '#FF0026',
  recoveryYellow: '#FFDE00',
  recoveryGreen:  '#16EC06',

  // Sleep
  sleepBlue:      '#0093E7',
  sleepBlueBri:   '#4FB8FF',
  remPurple:      '#9C4DFF',
  deepIndigo:     '#3D3DFF',
  lightCyan:      '#3DD9FF',

  // Text
  brightWhite:   '#FFFFFF',
  softWhite:     '#F5F5F7',
  gray400:       '#A1A1AA',
  gray600:       '#5F5F65',
  gray700:       '#3F3F45',

  // Semantic
  alertRed:      '#FF453A',
  warning:       '#FFAA00',
  infoBlue:      '#0A84FF',
  success:       '#30D158',
  gold:          '#D4AF37',
} as const;

export type WhoopColor = keyof typeof colors;

/** Interpolates the recovery ring color for 0–100. */
export function recoveryColor(percent: number): string {
  const p = Math.max(0, Math.min(100, percent)) / 100;
  if (p < 0.33) return colors.recoveryRed;
  if (p < 0.67) {
    const t = (p - 0.33) / 0.34;
    const r = 255;
    const g = Math.round(0 + t * 222);
    const b = Math.round(38 * (1 - t));
    return `rgb(${r}, ${g}, ${b})`;
  } else {
    const t = (p - 0.67) / 0.33;
    const r = Math.round(255 * (1 - t) + 22 * t);
    const g = Math.round(222 + (236 - 222) * t);
    const b = Math.round(0 + 6 * t);
    return `rgb(${r}, ${g}, ${b})`;
  }
}
```

## 2. Typography

DIN 2014 is the proprietary face. Bundle via `expo-font`, or fall back to Inter — the closest geometric tabular substitute.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'DIN2014-Regular':   require('../assets/fonts/DIN2014-Regular.ttf'),
    'DIN2014-Demi':      require('../assets/fonts/DIN2014-Demi.ttf'),
    'DIN2014-Bold':      require('../assets/fonts/DIN2014-Bold.ttf'),
    'DIN2014-ExtraBold': require('../assets/fonts/DIN2014-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const DIN_REG  = 'DIN2014-Regular';
const DIN_DEMI = 'DIN2014-Demi';
const DIN_BOLD = 'DIN2014-Bold';
const DIN_XB   = 'DIN2014-ExtraBold';
const TNUM: TextStyle = { fontVariant: ['tabular-nums'] };

export const typography = {
  // Hero metrics
  recoveryHero: { ...TNUM, color: '#FFFFFF', fontFamily: DIN_XB,   fontSize: 76, letterSpacing: -1.6, lineHeight: 76 },
  strainHero:   { ...TNUM, color: '#FFFFFF', fontFamily: DIN_XB,   fontSize: 56, letterSpacing: -1.0, lineHeight: 56 },
  sleepHero:    { ...TNUM, color: '#FFFFFF', fontFamily: DIN_XB,   fontSize: 48, letterSpacing: -0.8, lineHeight: 48 },

  // Titles
  largeNav:     { color: '#FFFFFF', fontFamily: DIN_BOLD, fontSize: 24, letterSpacing: -0.2, lineHeight: 28 },
  subhead:      { color: '#FFFFFF', fontFamily: DIN_BOLD, fontSize: 18, lineHeight: 22 },
  cardTitle:    { color: '#FFFFFF', fontFamily: DIN_DEMI, fontSize: 15, lineHeight: 19 },

  // Body
  body:         { color: '#F5F5F7', fontFamily: DIN_REG,  fontSize: 14, lineHeight: 20 },
  bodySmall:    { color: '#F5F5F7', fontFamily: DIN_REG,  fontSize: 13, lineHeight: 18 },

  // Headers — ALL CAPS with wide tracking
  sectionCaps:  { color: '#A1A1AA', fontFamily: DIN_BOLD, fontSize: 14, letterSpacing: 1.6, lineHeight: 14, textTransform: 'uppercase' as const },
  metaCaps:     { color: '#A1A1AA', fontFamily: DIN_BOLD, fontSize: 11, letterSpacing: 1.2, lineHeight: 11, textTransform: 'uppercase' as const },
  metricLabel:  { color: '#A1A1AA', fontFamily: DIN_DEMI, fontSize: 11, letterSpacing: 0.6, lineHeight: 11, textTransform: 'uppercase' as const },
  chartAxis:    { ...TNUM, color: '#A1A1AA', fontFamily: DIN_DEMI, fontSize: 10, letterSpacing: 0.2, lineHeight: 10 },

  // Metric values
  metricValue:  { ...TNUM, color: '#FFFFFF', fontFamily: DIN_BOLD, fontSize: 18, lineHeight: 22 },

  // Buttons & tabs
  button:       { color: '#0A0A0A', fontFamily: DIN_BOLD, fontSize: 14, letterSpacing: 1.0, lineHeight: 14, textTransform: 'uppercase' as const },
  tab:          { fontFamily: DIN_BOLD, fontSize: 10, letterSpacing: 0.8, lineHeight: 10, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Recovery Ring

```tsx
// components/RecoveryRing.tsx
import { View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedProps, withTiming, Easing } from 'react-native-reanimated';
import Svg, { Circle, G } from 'react-native-svg';
import { useEffect } from 'react';
import { colors, recoveryColor } from '../theme/colors';
import { typography } from '../theme/typography';

const AnimatedCircle = Animated.createAnimatedComponent(Circle);

type Props = { percent: number; diameter?: number; stroke?: number };

export function RecoveryRing({ percent, diameter = 240, stroke = 10 }: Props) {
  const radius = (diameter - stroke) / 2;
  const circumference = 2 * Math.PI * radius;
  const animated = useSharedValue(0);

  useEffect(() => {
    animated.value = withTiming(percent / 100, { duration: 1200, easing: Easing.out(Easing.cubic) });
  }, [percent]);

  const animatedProps = useAnimatedProps(() => ({
    strokeDashoffset: circumference * (1 - animated.value * 0.99),
  }));

  return (
    <View style={{ width: diameter, height: diameter, alignItems: 'center', justifyContent: 'center' }}>
      <Svg width={diameter} height={diameter}>
        <G rotation={-90} origin={`${diameter / 2}, ${diameter / 2}`}>
          {/* Background ring with 2pt gap at top */}
          <Circle
            cx={diameter / 2}
            cy={diameter / 2}
            r={radius}
            stroke={colors.surface2}
            strokeWidth={stroke}
            fill="none"
            strokeLinecap="round"
            strokeDasharray={`${circumference * 0.99} ${circumference}`}
          />
          {/* Filled ring */}
          <AnimatedCircle
            cx={diameter / 2}
            cy={diameter / 2}
            r={radius}
            stroke={recoveryColor(percent)}
            strokeWidth={stroke}
            fill="none"
            strokeLinecap="round"
            strokeDasharray={circumference}
            animatedProps={animatedProps}
          />
        </G>
      </Svg>
      <View style={{ position: 'absolute', alignItems: 'center', gap: 4 }}>
        <Text style={typography.sectionCaps}>RECOVERY</Text>
        <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 4 }}>
          <Text style={typography.recoveryHero}>{Math.round(percent)}</Text>
          <Text style={{ ...typography.recoveryHero, fontSize: 24, letterSpacing: 0 }}>%</Text>
        </View>
      </View>
    </View>
  );
}
```

### Strain Bar

```tsx
// components/StrainBar.tsx
import { View, Text, useWindowDimensions } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const TICKS = [
  { pos: 0.40, label: 'LOW' },
  { pos: 0.50, label: 'MOD' },
  { pos: 0.70, label: 'HIGH' },
  { pos: 0.90, label: 'ALL-OUT' },
];

export function StrainBar({ currentStrain }: { currentStrain: number }) {
  const { width } = useWindowDimensions();
  const trackWidth = width - 40;
  const height = 12;
  const markerX = useSharedValue(0);

  useEffect(() => {
    markerX.value = withSpring((trackWidth * currentStrain) / 21, { damping: 14, stiffness: 200 });
  }, [currentStrain, trackWidth]);

  const markerStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: Math.max(8, markerX.value) - 8 }],
  }));

  return (
    <View style={{ paddingHorizontal: 20, gap: 6 }}>
      {/* Tick labels */}
      <View style={{ height: 14, position: 'relative' }}>
        {TICKS.map((t) => (
          <Text
            key={t.label}
            style={[typography.chartAxis, {
              position: 'absolute',
              left: trackWidth * t.pos,
              transform: [{ translateX: -t.label.length * 2.5 }],
            }]}
          >
            {t.label}
          </Text>
        ))}
      </View>

      {/* Track */}
      <View style={{ width: trackWidth, height, borderRadius: 6, backgroundColor: colors.surface2, overflow: 'hidden' }}>
        <LinearGradient
          colors={[colors.strainDim, colors.strain, colors.strainBright]}
          start={{ x: 0, y: 0.5 }} end={{ x: 1, y: 0.5 }}
          style={{ width: (trackWidth * currentStrain) / 21, height }}
        />
      </View>

      {/* Marker */}
      <Animated.View
        style={[
          { position: 'absolute', top: 22, width: 16, height: 16, borderRadius: 8, backgroundColor: colors.brightWhite },
          markerStyle,
        ]}
      />
    </View>
  );
}
```

### Sleep Stage Chart

```tsx
// components/SleepStageChart.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type Stage = 'awake' | 'light' | 'deep' | 'rem';
export type Segment = { stage: Stage; durationMin: number };

const stageColor: Record<Stage, string> = {
  awake: colors.gray600,
  light: colors.lightCyan,
  deep:  colors.deepIndigo,
  rem:   colors.remPurple,
};

export function SleepStageChart({ segments, startHour, endHour }: { segments: Segment[]; startHour: string; endHour: string }) {
  const total = segments.reduce((sum, s) => sum + s.durationMin, 0);
  return (
    <View style={{ gap: 6 }}>
      <View style={{ flexDirection: 'row', height: 32, borderRadius: 4, overflow: 'hidden', gap: 1 }}>
        {segments.map((seg, i) => (
          <View key={i} style={{ flex: seg.durationMin, backgroundColor: stageColor[seg.stage] }} />
        ))}
      </View>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
        <Text style={typography.chartAxis}>{startHour}</Text>
        <Text style={typography.chartAxis}>{endHour}</Text>
      </View>
    </View>
  );
}
```

### Primary CTA

```tsx
// components/WhoopPrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WhoopPrimaryButton({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        height: 48, borderRadius: 4, paddingHorizontal: 28, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.strainBright : colors.strain,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{label.toUpperCase()}</Text>
    </Pressable>
  );
}
```

### Outline Button

```tsx
// components/WhoopOutlineButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WhoopOutlineButton({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        height: 48, borderRadius: 4, paddingHorizontal: 28, alignItems: 'center', justifyContent: 'center',
        borderWidth: 1.5, borderColor: colors.strain, backgroundColor: 'transparent',
      }}
    >
      <Text style={[typography.button, { color: colors.strain }]}>{label.toUpperCase()}</Text>
    </Pressable>
  );
}
```

### Activity Row

```tsx
// components/ActivityRow.tsx
import { Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = { icon: keyof typeof Ionicons.glyphMap; name: string; strain: number; duration: string; startTime: string; onPress: () => void };

export function ActivityRow({ icon, name, strain, duration, startTime, onPress }: Props) {
  return (
    <Pressable onPress={onPress} style={{ flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, height: 72, borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      <View style={{ width: 40, height: 40, borderRadius: 20, borderWidth: 1.5, borderColor: colors.strain, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name={icon} size={18} color={colors.strain} />
      </View>
      <View style={{ marginLeft: 12, flex: 1, gap: 2 }}>
        <Text style={typography.cardTitle}>{name}</Text>
        <Text style={[typography.metricValue, { color: colors.strain }]}>{strain.toFixed(1)}</Text>
      </View>
      <View style={{ alignItems: 'flex-end', gap: 2 }}>
        <Text style={typography.bodySmall}>{duration}</Text>
        <Text style={typography.chartAxis}>{startTime}</Text>
      </View>
      <Ionicons name="chevron-forward" size={14} color={colors.gray600} style={{ marginLeft: 12 }} />
    </Pressable>
  );
}
```

### Metric Card

```tsx
// components/MetricCard.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MetricCard({ label, children }: { label: string; children: React.ReactNode }) {
  return (
    <View style={{
      padding: 20, borderRadius: 12, backgroundColor: colors.surface1,
      borderWidth: 1, borderColor: colors.divider, gap: 12,
    }}>
      <Text style={typography.metricLabel}>{label.toUpperCase()}</Text>
      {children}
    </View>
  );
}
```

### Live Workout Banner

```tsx
// components/LiveWorkoutBanner.tsx
import { View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, Easing } from 'react-native-reanimated';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LiveWorkoutBanner({ elapsed, currentStrain }: { elapsed: string; currentStrain: number }) {
  const opacity = useSharedValue(0.4);
  useEffect(() => {
    opacity.value = withRepeat(withTiming(1, { duration: 600, easing: Easing.inOut(Easing.ease) }), -1, true);
  }, []);
  const dotStyle = useAnimatedStyle(() => ({ opacity: opacity.value }));

  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', height: 28, paddingHorizontal: 16, gap: 8,
      backgroundColor: colors.strain, shadowColor: colors.strain, shadowOpacity: 0.32, shadowRadius: 24,
      elevation: 8,
    }}>
      <Animated.View style={[{ width: 8, height: 8, borderRadius: 4, backgroundColor: colors.canvas }, dotStyle]} />
      <Text style={[typography.metaCaps, { color: colors.canvas, letterSpacing: 0.6 }]}>
        LIVE — {elapsed} · STRAIN {currentStrain.toFixed(1)}
      </Text>
    </View>
  );
}
```

### Day Switcher

```tsx
// components/DaySwitcher.tsx
import { Pressable, View, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Day = { label: string };

export function DaySwitcher({ days, selectedIndex, onSelect }: { days: Day[]; selectedIndex: number; onSelect: (i: number) => void }) {
  return (
    <View style={{ flexDirection: 'row', justifyContent: 'space-between', paddingHorizontal: 20 }}>
      {days.map((day, i) => {
        const isSel = i === selectedIndex;
        return (
          <Pressable
            key={day.label + i}
            onPress={() => { Haptics.selectionAsync(); onSelect(i); }}
            style={{
              width: 36, height: 36, borderRadius: 18,
              alignItems: 'center', justifyContent: 'center',
              backgroundColor: isSel ? colors.strain : 'transparent',
              borderWidth: isSel ? 0 : 1, borderColor: colors.gray600,
              transform: [{ scale: isSel ? 1.08 : 1 }],
            }}
          >
            <Text style={[typography.chartAxis, { color: isSel ? colors.canvas : colors.gray400 }]}>{day.label}</Text>
          </Pressable>
        );
      })}
    </View>
  );
}
```

## 4. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.strain,
        tabBarInactiveTintColor: colors.gray600,
        tabBarStyle: { backgroundColor: colors.surface1, borderTopWidth: 0.5, borderTopColor: colors.divider, height: 84 },
        tabBarLabelStyle: { fontFamily: 'DIN2014-Bold', fontSize: 10, letterSpacing: 0.8, textTransform: 'uppercase' },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'OVERVIEW',  tabBarIcon: ({ color }) => <Ionicons name="ellipse-outline"    size={22} color={color} /> }} />
      <Tabs.Screen name="coaching"  options={{ title: 'COACHING',  tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="community" options={{ title: 'COMMUNITY', tabBarIcon: ({ color }) => <Ionicons name="people"              size={22} color={color} /> }} />
      <Tabs.Screen name="stats"     options={{ title: 'STATS',     tabBarIcon: ({ color }) => <Ionicons name="stats-chart"         size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// Recovery ring fill — see RecoveryRing.tsx
// withTiming over 1200ms with cubic easing

// Strain marker spring — see StrainBar.tsx
// withSpring damping 14, stiffness 200

// CTA tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Day switcher tap
Haptics.selectionAsync();

// Sleep stage tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Sync complete
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Heart-rate beat pulse — see LiveWorkoutBanner.tsx pattern
```

## 6. Icon Library

`@expo/vector-icons` — Ionicons. In production WHOOP ships a proprietary DIN-icon set; substitute Ionicons during prototyping.

| Purpose | Ionicons |
|---------|----------|
| Overview tab | `ellipse-outline` / `ellipse` |
| Coaching tab | `person-circle-outline` / `person-circle` |
| Community tab | `people-outline` / `people` |
| Stats tab | `stats-chart-outline` / `stats-chart` |
| Run | `body-outline` |
| Cycling | `bicycle-outline` |
| Yoga | `flower-outline` |
| Strength | `barbell-outline` |
| Heart rate | `heart` (filled, pulses) |
| Battery | `battery-half` |
| Sync | `sync` |
| Search | `search` |
| Row chevron | `chevron-forward` |

## 7. Platform Notes

- **Dark-mode-only**: WHOOP has no light theme. Force dark at the root: `<StatusBar style="light" />` from `expo-status-bar`.
- **OLED-friendly**: the canvas `#0A0A0A` is near-black to bond with the device bezel; Strain Green `#00FF7B` is OLED-vibrant.
- **Status bar**: `style="light"` always.
- **Safe area**: Wrap screens in `SafeAreaView`. The live workout banner sits *above* the status bar (replaces it).
- **Tabular numerals**: Apply `fontVariant: ['tabular-nums']` to every metric value. Non-negotiable.
- **Reanimated v3**: required for the Recovery ring `useAnimatedProps` and the StrainBar marker spring.
- **Heart-rate live trace**: For production, use `react-native-skia` to draw a scrolling polyline at 60fps. Reanimated alone is not performant enough for continuous chart updates.
- **Reduce Motion**: check `AccessibilityInfo.isReduceMotionEnabled()` — when true, set ring `withTiming` duration to 0, disable strain marker spring (use linear interpolation), and stop the live banner pulse loop.
- **DIN 2014**: License from ParaType. For prototyping, substitute Inter via Google Fonts — its geometric proportions and tabular figures are the closest free substitute. Do NOT substitute a humanist sans (e.g., Open Sans, Lato) — it breaks the instrument-panel feel.
