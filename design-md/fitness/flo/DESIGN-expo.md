# Flo (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Flo's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, and `react-native-svg` (for the cycle wheel).

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:        '#FFFFFF',
  blush:         '#FFF0F3',
  surfaceSunken: '#FBE9ED',
  divider:       '#F3DDE3',

  textPrimary:   '#1A1A2E',
  textSecondary: '#6E6A82',
  textTertiary:  '#A09CB0',

  coral:        '#FF6B81',
  coralPressed: '#E85870',
  coralSoft:    '#FFD9DF',

  lavender:     '#C5B3E6',
  lavenderDeep: '#A893D6',
  lavenderSoft: '#EDE6F7',

  gentlePositive: '#7DC9A8',
  gentleAlert:    '#F2B36B',
} as const;

export type FloColor = keyof typeof colors;
```

## 2. Typography

Load Inter via `expo-font`. Use tabular figures on the wheel center and calendar.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
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
const ink = { color: '#1A1A2E' } satisfies TextStyle;

export const typography = {
  wheelCenter: { ...ink, fontFamily: 'Inter-Bold',     fontSize: 30, letterSpacing: -0.4, ...tabular },
  greeting:    { ...ink, fontFamily: 'Inter-Bold',     fontSize: 28, letterSpacing: -0.3 },
  screenTitle: { ...ink, fontFamily: 'Inter-Bold',     fontSize: 24, letterSpacing: -0.3 },
  section:     { ...ink, fontFamily: 'Inter-Bold',     fontSize: 22, letterSpacing: -0.2 },
  cardTitle:   { ...ink, fontFamily: 'Inter-SemiBold', fontSize: 18, letterSpacing: -0.1 },
  prediction:  {          fontFamily: 'Inter-Bold',     fontSize: 20, letterSpacing: -0.2, color: '#FF6B81' },
  body:        { ...ink, fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 25 },
  chipLabel:   { ...ink, fontFamily: 'Inter-SemiBold', fontSize: 15 },
  subtitle:    {          fontFamily: 'Inter-Regular',  fontSize: 14, lineHeight: 20, color: '#6E6A82' },
  button:      {          fontFamily: 'Inter-SemiBold', fontSize: 16, letterSpacing: 0.1, color: '#FFFFFF' },
  calDay:      { ...ink, fontFamily: 'Inter-SemiBold', fontSize: 15, ...tabular },
  meta:        {          fontFamily: 'Inter-Medium',   fontSize: 13, letterSpacing: 0.1, color: '#6E6A82' },
  tab:         {          fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.2 },
  labelUpper:  {          fontFamily: 'Inter-Bold',     fontSize: 12, letterSpacing: 0.8, color: '#6E6A82', textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Cycle Wheel (signature)

```tsx
// components/CycleWheel.tsx
import { useEffect } from 'react';
import { Pressable, Text, View } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import Animated, {
  useSharedValue, useAnimatedProps, useAnimatedStyle, withTiming, Easing,
} from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const ACircle = Animated.createAnimatedComponent(Circle);

type Segment = { start: number; end: number; color: string }; // fractions 0..1

export function CycleWheel({
  cycleDay, phaseName, segments, todayFraction, onLog, size = 260,
}: {
  cycleDay: number; phaseName: string; segments: Segment[];
  todayFraction: number; onLog: () => void; size?: number;
}) {
  const r = (size - 14) / 2;
  const c = 2 * Math.PI * r;
  const sweep = useSharedValue(0);
  const centerS = useSharedValue(0.9);
  const centerO = useSharedValue(0);

  useEffect(() => {
    sweep.value = withTiming(1, { duration: 700, easing: Easing.out(Easing.cubic) });
    centerS.value = withTiming(1, { duration: 700, easing: Easing.out(Easing.cubic) });
    centerO.value = withTiming(1, { duration: 700 });
  }, []);

  const centerStyle = useAnimatedStyle(() => ({
    transform: [{ scale: centerS.value }], opacity: centerO.value,
  }));

  return (
    <View style={{ alignItems: 'center', gap: 16 }}>
      <View style={{ width: size, height: size, alignItems: 'center', justifyContent: 'center' }}>
        <Svg width={size} height={size} style={{ position: 'absolute' }}>
          <Circle cx={size / 2} cy={size / 2} r={r}
            stroke={colors.divider} strokeWidth={14} fill="none" />
          {segments.map((s, i) => {
            const aProps = useAnimatedProps(() => ({
              strokeDasharray: `${c * (s.end - s.start) * sweep.value} ${c}`,
              strokeDashoffset: -c * s.start,
            }));
            return (
              <ACircle key={i} cx={size / 2} cy={size / 2} r={r}
                stroke={s.color} strokeWidth={14} fill="none" strokeLinecap="round"
                rotation={-90} origin={`${size / 2}, ${size / 2}`} animatedProps={aProps} />
            );
          })}
        </Svg>
        <Animated.View style={[{ alignItems: 'center' }, centerStyle]}>
          <Text style={typography.wheelCenter}>Day {cycleDay}</Text>
          <Text style={typography.subtitle}>{phaseName}</Text>
        </Animated.View>
      </View>
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onLog(); }}
        style={({ pressed }) => ({
          paddingVertical: 14, paddingHorizontal: 32, borderRadius: 26,
          backgroundColor: pressed ? colors.coralPressed : colors.coral,
          transform: [{ scale: pressed ? 0.97 : 1 }],
        })}
      >
        <Text style={typography.button}>Log period</Text>
      </Pressable>
    </View>
  );
}
```

### Prediction Card (signature)

```tsx
// components/PredictionCard.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PredictionCard({
  label, emphasis, sub,
}: { label: string; emphasis: string; sub: string }) {
  return (
    <View
      style={{
        flexDirection: 'row', gap: 14, padding: 20, borderRadius: 20,
        backgroundColor: colors.blush,
        shadowColor: colors.coral, shadowOpacity: 0.10, shadowRadius: 20,
        shadowOffset: { width: 0, height: 6 },
      }}
    >
      <Ionicons name="water" size={28} color={colors.coral} />
      <View style={{ flex: 1, gap: 6 }}>
        <Text style={typography.labelUpper}>{label}</Text>
        <Text style={typography.prediction}>{emphasis}</Text>
        <Text style={typography.subtitle}>{sub}</Text>
      </View>
    </View>
  );
}
```

### Symptom Log Chip

```tsx
// components/SymptomChip.tsx
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SymptomChip({
  title, icon, selected, onToggle,
}: { title: string; icon?: any; selected: boolean; onToggle: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.selectionAsync(); onToggle(); }}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 8,
        paddingVertical: 10, paddingHorizontal: 16, borderRadius: 18,
        backgroundColor: selected ? colors.coralSoft : colors.blush,
        borderWidth: 1.5, borderColor: selected ? colors.coral : 'transparent',
        transform: [{ scale: pressed ? 0.96 : 1 }],
      })}
    >
      {icon && (
        <Ionicons name={icon} size={18}
          color={selected ? colors.coralPressed : colors.textPrimary} />
      )}
      <Text style={[typography.chipLabel,
        { color: selected ? colors.coralPressed : colors.textPrimary }]}>
        {title}
      </Text>
    </Pressable>
  );
}
```

### Calendar Day Cell

```tsx
// components/CalendarDayCell.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type DayState = 'ordinary' | 'predictedPeriod' | 'loggedPeriod' | 'fertile' | 'ovulation';

export function CalendarDayCell({
  day, state, isToday,
}: { day: number; state: DayState; isToday: boolean }) {
  const fill =
    state === 'loggedPeriod' ? colors.coralPressed :
    state === 'fertile'      ? colors.lavenderSoft : 'transparent';
  const border =
    state === 'predictedPeriod' ? colors.coral :
    state === 'ovulation'       ? colors.lavenderDeep : 'transparent';
  const borderW = state === 'ovulation' ? 2 : state === 'predictedPeriod' ? 1.5 : 0;
  const textColor =
    state === 'loggedPeriod'   ? '#FFFFFF' :
    state === 'predictedPeriod' ? colors.coral : colors.textPrimary;

  return (
    <View style={{ width: 40, height: 40, alignItems: 'center', justifyContent: 'center' }}>
      <View style={{
        width: 40, height: 40, borderRadius: 20, alignItems: 'center', justifyContent: 'center',
        backgroundColor: fill, borderWidth: borderW, borderColor: border,
      }}>
        <Text style={[typography.calDay, { color: textColor }]}>{day}</Text>
      </View>
      {isToday && (
        <View style={{
          position: 'absolute', bottom: 2, width: 5, height: 5, borderRadius: 3,
          backgroundColor: colors.coral,
        }} />
      )}
    </View>
  );
}
```

## 4. Tab Bar

`expo-router` Tabs with a near-opaque white `BlurView`. **Active tint is Flo Coral.**

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
        tabBarActiveTintColor: colors.coral,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider,
          backgroundColor: 'transparent',
        },
        tabBarBackground: () => (
          <BlurView intensity={60} tint="light"
            style={{ flex: 1, backgroundColor: 'rgba(255,255,255,0.94)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Today',    tabBarIcon: ({ color }) => <Ionicons name="ellipse"          size={24} color={color} /> }} />
      <Tabs.Screen name="calendar" options={{ title: 'Calendar', tabBarIcon: ({ color }) => <Ionicons name="calendar"         size={24} color={color} /> }} />
      <Tabs.Screen name="insights" options={{ title: 'Insights', tabBarIcon: ({ color }) => <Ionicons name="bulb"             size={24} color={color} /> }} />
      <Tabs.Screen name="partner"  options={{ title: 'Partner',  tabBarIcon: ({ color }) => <Ionicons name="heart-circle"     size={24} color={color} /> }} />
      <Tabs.Screen name="more"     options={{ title: 'More',     tabBarIcon: ({ color }) => <Ionicons name="ellipsis-horizontal" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Wheel reveal — ring sweeps, center scales+fades over 700ms ease-out (see CycleWheel)

// Wheel phase morph — crossfade segment colors when the cycle advances
segColor.value = withTiming(nextColor, { duration: 300, easing: Easing.inOut(Easing.ease) });

// Chip select — fill animates blush → coral-soft over 200ms (Pressable style transition)

// Card tap — scale 0.98 via Pressable, ~200ms

// Calendar day select — cell scales ~1.08 with a soft shadow bloom
scale.value = withTiming(1.08, { duration: 200, easing: Easing.out(Easing.cubic) });

// Log success — soft sage check + gentle haptic, wheel morphs to new state
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
```

Haptics via `expo-haptics`: `impactAsync(Soft)` on primary CTA, `selectionAsync()` on chip toggle, `notificationAsync(Success)` on log saved. Keep them gentle — Flo is calm.

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Flo's SF Symbol equivalents:

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Period / flow | `drop.fill` | `water` |
| Fertile / nature | `leaf.fill` | `leaf` |
| Ovulation | `sparkle` | `sparkles` |
| Mood | `face.smiling` | `happy-outline` |
| Symptom (generic) | `heart.text.square` | `medical-outline` |
| Add / log | `plus` | `add` |
| Edit | `pencil` | `pencil` |
| Info | `info.circle` | `information-circle-outline` |
| Share | `square.and.arrow.up` | `share-outline` |
| Search | `magnifyingglass` | `search` |
| Today (tab) | `circle.circle.fill` | `ellipse` |
| Calendar (tab) | `calendar` | `calendar` |
| Insights (tab) | `lightbulb.fill` | `bulb` |
| Partner (tab) | `heart.circle.fill` | `heart-circle` |
| More (tab) | `ellipsis` | `ellipsis-horizontal` |

## 7. Platform Notes

- **SVG wheel**: the cycle wheel needs `react-native-svg` + `react-native-reanimated`'s `useAnimatedProps` so the sweep and phase morph run on the UI thread and stay smooth
- **Tabular figures**: `fontVariant: ['tabular-nums']` works on iOS; on Android also ship an Inter build with tabular numerals so the calendar grid and wheel center never jitter
- **Light-first**: set `#FFFFFF` canvas; `<StatusBar style="dark" />` from `expo-status-bar` globally. A dark mode exists for OS parity — branch on `useColorScheme()` only if you ship it; the light blush experience is primary
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the wheel centers in the safe content area
- **Dynamic Type**: RN honors font scaling — keep it on greetings/titles/body; set `allowFontScaling={false}` on the wheel center (must fit the circle), calendar day numbers, tab labels, captions
- **Sensitive content**: Flo content is private — implement an app-lock and obscure the screen in the iOS app switcher (a cover view on `AppState` change) when the user enables it
- **Accessibility**: give the wheel a combined `accessibilityLabel` ("Cycle day 14, follicular phase, period in 5 days"); calendar cells announce state; never rely on coral/lavender alone — pair with the legend and text; on Reduce Motion skip the sweep + morph
