# Hevy (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Hevy's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets for the live workout log.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — default)
  canvas:    '#0E1116',
  card:      '#161B22',
  input:     '#1F2630',
  surface3:  '#283040',
  divider:   '#262C36',

  // Surfaces (light — optional)
  lightCanvas:  '#F4F6FA',
  lightCard:    '#FFFFFF',
  lightInput:   '#EEF1F6',
  lightDivider: '#E2E6EE',

  // Brand — single action color
  blue:        '#1E6FFF',
  bluePressed: '#1857CC',
  blueSoft:    '#14233F',

  // Text
  textPrimary:   '#F2F4F8',
  textSecondary: '#9BA3B0',
  textTertiary:  '#5E6675',

  // Semantic
  done:  '#2FBF71',
  pr:    '#F5B83D',
  error: '#F1545B',
  warn:  '#F5A623',
} as const;

export type HevyColor = keyof typeof colors;

// Translucent helpers
export const alpha = {
  rowWash:   'rgba(47,191,113,0.16)',  // completed set row
  prBadge:   'rgba(245,184,61,0.18)',  // PR chip bg
  ringTrack: 'rgba(30,111,255,0.30)',  // rest-timer track
  ringEdge:  'rgba(30,111,255,0.35)',  // rest pill border
  tabFill:   'rgba(30,111,255,0.16)',  // active tab icon fill
} as const;
```

## 2. Typography

Hevy ships one face — **Inter** — and uses tabular figures for every number. Load via `expo-font`; apply `fontVariant: ['tabular-nums']` on every numeric `<Text>`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':    require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':     require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':   require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':       require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold':  require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tnum = { fontVariant: ['tabular-nums'] as const };
const primary = { color: '#F2F4F8' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  routine:     { ...primary, fontFamily: 'Inter-Bold',      fontSize: 26, lineHeight: 31, letterSpacing: -0.4 },
  section:     { ...primary, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.3 },
  exercise:    { color: '#1E6FFF', fontFamily: 'Inter-Bold', fontSize: 18, lineHeight: 23, letterSpacing: -0.2 },
  metric:      { ...primary, ...tnum, fontFamily: 'Inter-Bold',     fontSize: 17, lineHeight: 20 },
  body:        { ...primary, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 24 },
  setCell:     { ...primary, ...tnum, fontFamily: 'Inter-SemiBold', fontSize: 15, lineHeight: 20 },
  cardTitle:   { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 20, letterSpacing: -0.1 },
  caption:     { color: '#9BA3B0', fontFamily: 'Inter-Medium', fontSize: 13, lineHeight: 18 },
  column:      { color: '#9BA3B0', fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 12, letterSpacing: 0.5 },
  tab:         { color: '#5E6675', fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
  button:      { color: '#FFFFFF', fontFamily: 'Inter-Bold',  fontSize: 16, lineHeight: 16 },
  badge:       { color: '#F5B83D', fontFamily: 'Inter-ExtraBold', fontSize: 10, lineHeight: 11, letterSpacing: 0.3 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Active-Workout Header

```tsx
// components/WorkoutHeader.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WorkoutHeader({
  routineName, duration, volume, setCount, onFinish,
}: {
  routineName: string; duration: string; volume: string; setCount: number; onFinish: () => void;
}) {
  const Stat = ({ label, value, live }: { label: string; value: string; live?: boolean }) => (
    <View>
      <Text style={[typography.caption, { fontSize: 11 }]}>{label}</Text>
      <Text style={[typography.metric, live && { color: colors.blue }]}>{value}</Text>
    </View>
  );
  return (
    <View style={{ paddingHorizontal: 18, paddingBottom: 12, gap: 12 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
        <Text style={typography.routine}>{routineName}</Text>
        <Pressable
          onPress={onFinish}
          style={{ backgroundColor: colors.blue, paddingVertical: 7, paddingHorizontal: 16, borderRadius: 10 }}>
          <Text style={{ color: '#fff', fontFamily: 'Inter-Bold', fontSize: 13 }}>Finish</Text>
        </Pressable>
      </View>
      <View style={{ flexDirection: 'row', gap: 22 }}>
        <Stat label="Duration" value={duration} live />
        <Stat label="Volume" value={volume} />
        <Stat label="Sets" value={String(setCount)} />
      </View>
    </View>
  );
}
```

### Set Row (the core atom)

```tsx
// components/SetRow.tsx
import { useState } from 'react';
import { Pressable, Text, TextInput, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors, alpha } from '../theme/colors';
import { typography } from '../theme/typography';

export function SetRow({ index, previous }: { index: number; previous: string }) {
  const [weight, setWeight] = useState('');
  const [reps, setReps] = useState('');
  const [done, setDone] = useState(false);
  const [focused, setFocused] = useState<'w' | 'r' | null>(null);

  const Cell = ({ value, set, k }: { value: string; set: (s: string) => void; k: 'w' | 'r' }) => (
    <TextInput
      value={value}
      onChangeText={set}
      keyboardType="decimal-pad"
      placeholder={previous.split(' ')[0]}
      placeholderTextColor={colors.textTertiary}
      onFocus={() => setFocused(k)}
      onBlur={() => setFocused(null)}
      style={[
        typography.setCell,
        {
          flex: 1, height: 36, textAlign: 'center', borderRadius: 8,
          backgroundColor: colors.input,
          color: done ? colors.done : colors.textPrimary,
          borderWidth: 1.5, borderColor: focused === k ? colors.blue : 'transparent',
        },
      ]}
    />
  );

  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 10, paddingVertical: 7,
      backgroundColor: done ? alpha.rowWash : 'transparent', borderRadius: 8,
    }}>
      <View style={{ width: 28, height: 28, borderRadius: 8, backgroundColor: colors.input,
                     alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontFamily: 'Inter-Bold', fontSize: 13, color: colors.textSecondary }}>{index}</Text>
      </View>
      <Text style={[typography.caption, { flex: 1, textAlign: 'center', color: colors.textTertiary, fontVariant: ['tabular-nums'] }]}>
        {previous}
      </Text>
      <Cell value={weight} set={setWeight} k="w" />
      <Cell value={reps} set={setReps} k="r" />
      <Pressable
        onPress={() => { setDone((d) => !d); Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); }}
        style={{
          width: 26, height: 26, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
          backgroundColor: done ? colors.done : 'transparent',
          borderWidth: 1.5, borderColor: done ? colors.done : colors.divider,
        }}>
        {done ? <Ionicons name="checkmark" size={14} color="#fff" /> : null}
      </Pressable>
    </View>
  );
}
```

### Rest-Timer Pill

```tsx
// components/RestTimerPill.tsx
import Svg, { Circle } from 'react-native-svg';
import { Pressable, Text, View } from 'react-native';
import { colors, alpha } from '../theme/colors';

export function RestTimerPill({
  remaining, total, onSkip,
}: { remaining: number; total: number; onSkip: () => void }) {
  const progress = total === 0 ? 0 : 1 - remaining / total;
  const C = 2 * Math.PI * 9;
  const mmss = `${Math.floor(remaining / 60)}:${String(Math.floor(remaining % 60)).padStart(2, '0')}`;
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 10, height: 44,
      paddingHorizontal: 14, marginHorizontal: 18, borderRadius: 12,
      backgroundColor: colors.blueSoft, borderWidth: 1, borderColor: alpha.ringEdge,
    }}>
      <Svg width={22} height={22}>
        <Circle cx={11} cy={11} r={9} stroke={alpha.ringTrack} strokeWidth={3} fill="none" />
        <Circle cx={11} cy={11} r={9} stroke={colors.blue} strokeWidth={3} fill="none"
                strokeDasharray={C} strokeDashoffset={C * (1 - progress)}
                strokeLinecap="round" transform="rotate(-90 11 11)" />
      </Svg>
      <Text style={{ flex: 1, fontFamily: 'Inter-SemiBold', fontSize: 14, color: colors.blue, fontVariant: ['tabular-nums'] }}>
        Rest · {mmss}
      </Text>
      <Pressable onPress={onSkip}>
        <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 13, color: colors.textSecondary }}>Skip</Text>
      </Pressable>
    </View>
  );
}
```

### PR Badge

```tsx
// components/PRBadge.tsx
import { useEffect } from 'react';
import { Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors, alpha } from '../theme/colors';
import { typography } from '../theme/typography';

export function PRBadge({ text }: { text: string }) {
  const s = useSharedValue(0.9);
  const o = useSharedValue(0);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: s.value }], opacity: o.value }));

  useEffect(() => {
    s.value = withSpring(1, { damping: 9, stiffness: 160 });
    o.value = withSpring(1);
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
  }, []);

  return (
    <Animated.View style={[style, {
      alignSelf: 'flex-start', paddingVertical: 3, paddingHorizontal: 7,
      borderRadius: 6, backgroundColor: alpha.prBadge,
    }]}>
      <Text style={[typography.badge, { textTransform: 'uppercase' }]}>{text}</Text>
    </Animated.View>
  );
}
```

### Exercise Card

```tsx
// components/ExerciseCard.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ExerciseCard({
  name, icon = 'barbell-outline', children, onAddSet,
}: {
  name: string; icon?: any; children: React.ReactNode; onAddSet: () => void;
}) {
  return (
    <View style={{
      backgroundColor: colors.card, borderColor: colors.divider, borderWidth: 1,
      borderRadius: 16, padding: 14, marginBottom: 14, gap: 12,
    }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10 }}>
        <View style={{ width: 36, height: 36, borderRadius: 10, backgroundColor: colors.surface3,
                       alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name={icon} size={20} color={colors.blue} />
        </View>
        <Text style={[typography.exercise, { flex: 1 }]}>{name}</Text>
        <Ionicons name="ellipsis-horizontal" size={18} color={colors.textTertiary} />
      </View>
      {children}
      <Pressable onPress={onAddSet} style={{
        height: 34, borderRadius: 9, backgroundColor: colors.input,
        alignItems: 'center', justifyContent: 'center',
      }}>
        <Text style={{ fontFamily: 'Inter-Bold', fontSize: 13, color: colors.textSecondary }}>+ Add Set</Text>
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
        tabBarActiveTintColor: colors.blue,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(14,17,22,0.94)',
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}>
      <Tabs.Screen name="index"     options={{ title: 'Home',      tabBarIcon: ({ color }) => <Ionicons name="home"          size={22} color={color} /> }} />
      <Tabs.Screen name="profile"   options={{ title: 'Profile',   tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
      <Tabs.Screen name="workout"   options={{ title: 'Workout',   tabBarIcon: ({ color }) => <Ionicons name="add-circle"    size={24} color={color} /> }} />
      <Tabs.Screen name="history"   options={{ title: 'History',   tabBarIcon: ({ color }) => <Ionicons name="calendar"      size={22} color={color} /> }} />
      <Tabs.Screen name="exercises" options={{ title: 'Exercises', tabBarIcon: ({ color }) => <Ionicons name="barbell"       size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Set check — green wash + success haptic + auto-start rest
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
// backgroundColor toggles to alpha.rowWash (instant or LayoutAnimation 150ms)

// PR badge — spring scale-in + medium haptic
s.value = withSpring(1, { damping: 9, stiffness: 160 });
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Add set — new row enters
import Animated, { FadeInDown } from 'react-native-reanimated';
// <Animated.View entering={FadeInDown.duration(200)}> ...row... </Animated.View>

// Finish → post-workout summary: push a modal route
// router.push('/workout/summary')  — present as a full-screen modal (320ms)

// Rest ring — drive strokeDashoffset from a timer (1s tick) or withTiming(total, { easing: linear })

// Haptics: Success on set check, Medium on PR, Light tick on rest ±/end
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Hevy's iconography is minimal and functional.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Profile | `person-circle` |
| Workout (center) | `add-circle` |
| History | `calendar` |
| Exercises | `barbell` |
| Set checkmark | `checkmark` |
| Exercise menu | `ellipsis-horizontal` |
| Rest timer | `timer-outline` |
| Add | `add` |
| PR badge | `star` |
| Superset | `link` |
| Warm-up | `flame-outline` |
| Reorder | `reorder-three` |
| Delete | `trash-outline` |
| Volume chart | `stats-chart` |
| Kudos | `thumbs-up` / `thumbs-up-outline` |
| Comment | `chatbubble-outline` |
| Share | `share-outline` |

## 7. Platform Notes

- **Font choice**: Inter is SIL OFL — free to bundle. Ship one face; do not offer alternates (brand consistency)
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on every numeric `<Text>` (weights, reps, sets, volume, 1RM, timers) so the set table and timers never reflow
- **Status bar**: `<StatusBar style="light" />` — Hevy is dark-first
- **Safe area**: wrap screens in `SafeAreaView`; the rest pill should float above the keyboard when a set cell is focused — use `KeyboardAvoidingView` / `react-native-keyboard-controller`
- **Dynamic Type**: keep `allowFontScaling={false}` on column labels, tab labels, PR badges, and the set-index chip (table layout is digit-width-sensitive); allow scaling on titles/body
- **Live Activity**: the rest-timer countdown should mirror to Dynamic Island / Lock Screen via a config plugin (`expo-apple-targets` or a custom native module) — Expo Go cannot host Live Activities; use a development build
- **Dark mode**: default to dark; expose an opt-in light theme via a settings context that swaps `canvas`/`card`/`input`/`divider` to the `light*` tokens
- **Keyboard**: use `keyboardType="decimal-pad"` on set cells; select-all on focus for fast overwrite (`selectTextOnFocus`)
- **Accessibility**: every set row is an accessible element labelled "Set {n}, previous {prev}, {weight} kg, {reps} reps, {done/not done}"; the checkmark is a toggle; announce PR via `AccessibilityInfo.announceForAccessibility`
- **Drag-to-reorder exercises**: use `react-native-draggable-flatlist`; soft haptic on lift
