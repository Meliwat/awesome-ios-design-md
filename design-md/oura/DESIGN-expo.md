# Oura (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Oura's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, and `react-native-svg` (for the score ring).

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#0B0B0F',
  surface1: '#16161C',
  surface2: '#1E1E26',
  surface3: '#27272F',
  divider:  '#2A2A33',

  textPrimary:   '#FFFFFF',
  textSecondary: '#9A9AA5',
  textTertiary:  '#6A6A73',

  readiness:     '#4FD1C5',
  readinessDeep: '#37B3A8',
  sleep:         '#7C6FF0',
  sleepDeep:     '#6354D6',
  activity:      '#F5A623',
  activityDeep:  '#D98A12',

  negativeDelta: '#E0746B',
} as const;

export type OuraDomain = 'readiness' | 'sleep' | 'activity';

export const domainHue: Record<OuraDomain, string> = {
  readiness: colors.readiness, sleep: colors.sleep, activity: colors.activity,
};
export const domainDeep: Record<OuraDomain, string> = {
  readiness: colors.readinessDeep, sleep: colors.sleepDeep, activity: colors.activityDeep,
};
export const domainLabel: Record<OuraDomain, string> = {
  readiness: 'READINESS', sleep: 'SLEEP', activity: 'ACTIVITY',
};
```

## 2. Typography

Load Inter via `expo-font`. Enable tabular figures so scores never reflow.

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
  return <Stack screenOptions={{ headerShown: false }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tabular = { fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] };
const white = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  ringScore:   { ...white, fontFamily: 'Inter-Bold',     fontSize: 52, letterSpacing: -1, ...tabular },
  screenTitle: { ...white, fontFamily: 'Inter-Bold',     fontSize: 26, letterSpacing: -0.4 },
  section:     { ...white, fontFamily: 'Inter-Bold',     fontSize: 20, letterSpacing: -0.2 },
  score2:      { ...white, fontFamily: 'Inter-Bold',     fontSize: 34, letterSpacing: -0.6, ...tabular },
  cardTitle:   { ...white, fontFamily: 'Inter-SemiBold', fontSize: 17, letterSpacing: -0.1 },
  metricVal:   { ...white, fontFamily: 'Inter-SemiBold', fontSize: 22, letterSpacing: -0.2, ...tabular },
  contributor: { ...white, fontFamily: 'Inter-SemiBold', fontSize: 15 },
  body:        { ...white, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 23 },
  subtitle:    {           fontFamily: 'Inter-Regular',  fontSize: 13, color: '#9A9AA5' },
  delta:       {           fontFamily: 'Inter-SemiBold', fontSize: 13, ...tabular },
  domainLabel: {           fontFamily: 'Inter-Bold',     fontSize: 13, letterSpacing: 0.8, textTransform: 'uppercase' as const },
  button:      {           fontFamily: 'Inter-SemiBold', fontSize: 16, color: '#0B0B0F' },
  tab:         {           fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.2 },
  caption:     {           fontFamily: 'Inter-Regular',  fontSize: 11, color: '#6A6A73' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Score Ring (signature)

```tsx
// components/ScoreRing.tsx
import { useEffect, useState } from 'react';
import { View, Text } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import Animated, {
  useSharedValue, useAnimatedProps, withTiming, Easing,
} from 'react-native-reanimated';
import { colors, domainHue, domainLabel, OuraDomain } from '../theme/colors';
import { typography } from '../theme/typography';

const ACircle = Animated.createAnimatedComponent(Circle);

export function ScoreRing({
  score, domain, diameter = 220, stroke = 12,
}: { score: number; domain: OuraDomain; diameter?: number; stroke?: number }) {
  const r = (diameter - stroke) / 2;
  const c = 2 * Math.PI * r;
  const prog = useSharedValue(0);
  const [shown, setShown] = useState(0);

  useEffect(() => {
    prog.value = withTiming(score / 100, { duration: 900, easing: Easing.out(Easing.cubic) });
    const steps = 30;
    for (let i = 0; i <= steps; i++) {
      setTimeout(() => setShown(Math.round((score * i) / steps)), (900 * i) / steps);
    }
  }, [score]);

  const animatedProps = useAnimatedProps(() => ({
    strokeDashoffset: c * (1 - prog.value),
  }));

  return (
    <View style={{ width: diameter, height: diameter, alignItems: 'center', justifyContent: 'center' }}>
      <Svg width={diameter} height={diameter} style={{ position: 'absolute' }}>
        <Circle cx={diameter / 2} cy={diameter / 2} r={r}
          stroke={colors.surface3} strokeWidth={stroke} fill="none" />
        <ACircle cx={diameter / 2} cy={diameter / 2} r={r}
          stroke={domainHue[domain]} strokeWidth={stroke} fill="none"
          strokeLinecap="round" strokeDasharray={c} animatedProps={animatedProps}
          rotation={-90} origin={`${diameter / 2}, ${diameter / 2}`} />
      </Svg>
      <Text style={typography.ringScore}>{shown}</Text>
      <Text style={[typography.domainLabel, { color: domainHue[domain], marginTop: 4 }]}>
        {domainLabel[domain]}
      </Text>
    </View>
  );
}
```

### Contributor Bar List (signature)

```tsx
// components/ContributorBarList.tsx
import { View, Text } from 'react-native';
import { colors, domainHue, OuraDomain } from '../theme/colors';
import { typography } from '../theme/typography';

type Contributor = { name: string; normalized: number; value: string };

export function ContributorBarList({
  contributors, domain,
}: { contributors: Contributor[]; domain: OuraDomain }) {
  return (
    <View style={{ backgroundColor: colors.surface1, borderRadius: 16, overflow: 'hidden' }}>
      {contributors.map((c, i) => (
        <View key={c.name}>
          <View style={{
            flexDirection: 'row', alignItems: 'center', gap: 12,
            height: 44, paddingHorizontal: 16,
          }}>
            <Text style={[typography.contributor, { width: 130 }]} numberOfLines={1}>{c.name}</Text>
            <View style={{ flex: 1, height: 6, borderRadius: 3, backgroundColor: colors.surface3 }}>
              <View style={{
                width: `${c.normalized * 100}%`, height: 6, borderRadius: 3,
                backgroundColor: domainHue[domain],
              }} />
            </View>
            <Text style={[typography.delta, { width: 44, textAlign: 'right', color: '#FFF' }]}>
              {c.value}
            </Text>
          </View>
          {i < contributors.length - 1 && (
            <View style={{ height: 1, backgroundColor: colors.divider, marginLeft: 16 }} />
          )}
        </View>
      ))}
    </View>
  );
}
```

### Metric Tile

```tsx
// components/MetricTile.tsx
import { View, Text } from 'react-native';
import { colors, domainHue, OuraDomain } from '../theme/colors';
import { typography } from '../theme/typography';

export function MetricTile({
  label, value, unit, delta, domain,
}: { label: string; value: string; unit: string; delta: string; domain: OuraDomain }) {
  return (
    <View style={{ flex: 1, backgroundColor: colors.surface1, borderRadius: 16, padding: 16, gap: 10 }}>
      <Text style={[typography.domainLabel, { color: colors.textSecondary }]}>{label}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 4 }}>
        <Text style={typography.metricVal}>{value}</Text>
        <Text style={[typography.subtitle, { marginBottom: 3 }]}>{unit}</Text>
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
        <View style={{ width: 40, height: 3, borderRadius: 2, backgroundColor: domainHue[domain] }} />
        <Text style={[typography.delta, { color: domainHue[domain] }]}>{delta}</Text>
      </View>
    </View>
  );
}
```

### Insight Card

```tsx
// components/InsightCard.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, domainHue, OuraDomain } from '../theme/colors';
import { typography } from '../theme/typography';

export function InsightCard({
  icon, title, body, domain,
}: { icon: any; title: string; body: string; domain: OuraDomain }) {
  return (
    <View style={{
      flexDirection: 'row', gap: 14, padding: 18,
      backgroundColor: colors.surface1, borderRadius: 16,
    }}>
      <Ionicons name={icon} size={24} color={domainHue[domain]} />
      <View style={{ flex: 1, gap: 6 }}>
        <Text style={typography.cardTitle}>{title}</Text>
        <Text style={[typography.body, { color: colors.textSecondary }]}>{body}</Text>
      </View>
    </View>
  );
}
```

### Primary Button

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { domainHue, domainDeep, OuraDomain } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({
  title, domain, onPress,
}: { title: string; domain: OuraDomain; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={({ pressed }) => ({
        paddingVertical: 14, paddingHorizontal: 28, borderRadius: 24, alignItems: 'center',
        backgroundColor: pressed ? domainDeep[domain] : domainHue[domain],
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

## 4. Tab Bar

`expo-router` Tabs with a charcoal `BlurView`. **Active tint is neutral white** — domain color never on chrome.

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: '#FFFFFF',
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={60} tint="dark"
            style={{ flex: 1, backgroundColor: 'rgba(11,11,15,0.82)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Today',     tabBarIcon: ({ color }) => <Ionicons name="ellipse"        size={24} color={color} /> }} />
      <Tabs.Screen name="vitals"   options={{ title: 'Vitals',    tabBarIcon: ({ color }) => <Ionicons name="pulse"          size={24} color={color} /> }} />
      <Tabs.Screen name="health"   options={{ title: 'My Health', tabBarIcon: ({ color }) => <Ionicons name="heart"          size={24} color={color} /> }} />
      <Tabs.Screen name="explore"  options={{ title: 'Explore',   tabBarIcon: ({ color }) => <Ionicons name="grid"           size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Score reveal — arc + count-up land together (see ScoreRing): withTiming 900ms ease-out cubic

// Contributor bars — animate width 0 → normalized, stagger ~40ms per row
bar.value = withDelay(idx * 40, withTiming(c.normalized, { duration: 500, easing: Easing.out(Easing.cubic) }));

// Range switch (7D/30D/90D) — morph dataset
data.value = withTiming(nextRange, { duration: 350, easing: Easing.inOut(Easing.ease) });

// Card tap — scale 0.98 via Pressable, ~200ms

// Sync pull-to-refresh — a ring filling, then:
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
```

Haptics via `expo-haptics`: `impactAsync(Soft)` on primary actions, `notificationAsync(Success)` on completed sync, `selectionAsync()` on range/tag changes.

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Oura's SF Symbol equivalents:

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Readiness | `bolt.heart` | `flash` |
| Sleep | `moon.zzz` | `moon` |
| Activity | `flame` | `flame` |
| HRV insight | `waveform.path.ecg` | `pulse` |
| Heart rate | `heart` | `heart` |
| Temperature | `thermometer.medium` | `thermometer` |
| Add tag | `plus.circle` | `add-circle-outline` |
| Share | `square.and.arrow.up` | `share-outline` |
| Info | `info.circle` | `information-circle-outline` |
| Date chevron | `chevron.down` | `chevron-down` |
| Ring battery | `circle.bottomhalf.filled` | `battery-half` |
| Today (tab) | `circle.circle.fill` | `ellipse` |
| Vitals (tab) | `waveform.path.ecg` | `pulse` |
| My Health (tab) | `heart.text.square` | `heart` |
| Explore (tab) | `square.grid.2x2` | `grid` |

## 7. Platform Notes

- **SVG ring**: the score ring needs `react-native-svg` + `react-native-reanimated`'s `useAnimatedProps` to animate `strokeDashoffset` on the UI thread so the sweep stays smooth
- **Tabular figures**: `fontVariant: ['tabular-nums']` works on iOS; on Android also ship an Inter build with tabular numerals baked in so the count-up animation does not jitter
- **Canvas**: set `#0B0B0F` everywhere; `<StatusBar style="light" />` from `expo-status-bar` globally
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the hero ring centers in the safe content area
- **Dynamic Type**: RN honors font scaling — keep it on titles/contributors/body; set `allowFontScaling={false}` on the ring score (must fit the circle), domain labels, deltas, axis captions, tab labels
- **Color is information**: always render the domain hue alongside its uppercase text label + icon — never hue alone (color-blind safety)
- **Accessibility**: give the ring a combined `accessibilityLabel` ("Readiness 82 out of 100"); on Reduce Motion skip the sweep + count-up and show final values; expose contributor rows with state words
- **Dark-first**: do not branch on `useColorScheme()` — Oura is a dark instrument panel
