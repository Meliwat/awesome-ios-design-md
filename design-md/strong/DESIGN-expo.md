# Strong (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Strong's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets for the set-log table.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — default)
  canvas:    '#1A1A1A',
  card:      '#242424',
  input:     '#2E2E2E',
  surface3:  '#383838',
  divider:   '#333333',

  // Surfaces (light — optional)
  lightCanvas:  '#F2F2F2',
  lightCard:    '#FFFFFF',
  lightInput:   '#EBEBEB',
  lightDivider: '#DDDDDD',

  // Brand — single accent
  blue:        '#2F80ED',
  bluePressed: '#2566C0',
  blueSoft:    '#16263D',

  // Text
  textPrimary:   '#F5F5F5',
  textSecondary: '#9A9A9A',
  textTertiary:  '#636363',

  // Semantic
  done:  '#27AE60',
  pr:    '#F2C94C',
  error: '#EB5757',
  warn:  '#F2994A',
} as const;

export type StrongColor = keyof typeof colors;

// Translucent helpers
export const alpha = {
  rowFill:   'rgba(39,174,96,0.18)',   // logged set cells
  prFlag:    'rgba(242,201,76,0.18)',  // PR flag bg
  restFill:  'rgba(47,128,237,0.22)',  // rest-timer inner fill
  tabFill:   'rgba(47,128,237,0.18)',  // active tab icon fill
} as const;
```

## 2. Typography

Strong ships one face — **Inter** — and uses tabular figures for every number. Load via `expo-font`; apply `fontVariant: ['tabular-nums']` on every numeric `<Text>`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':    require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':      require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold': require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tnum = { fontVariant: ['tabular-nums'] as const };
const primary = { color: '#F5F5F5' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  workout:     { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 26, lineHeight: 31, letterSpacing: -0.4 },
  section:     { ...primary, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.3 },
  exercise:    { color: '#2F80ED', fontFamily: 'Inter-Bold', fontSize: 18, lineHeight: 23, letterSpacing: -0.2 },
  metric:      { ...primary, ...tnum, fontFamily: 'Inter-Bold',     fontSize: 17, lineHeight: 20 },
  body:        { ...primary, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 24 },
  setCell:     { ...primary, ...tnum, fontFamily: 'Inter-Bold',     fontSize: 15, lineHeight: 20 },
  rowTitle:    { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 20, letterSpacing: -0.1 },
  caption:     { color: '#9A9A9A', fontFamily: 'Inter-Medium', fontSize: 13, lineHeight: 18 },
  column:      { color: '#9A9A9A', fontFamily: 'Inter-Bold', fontSize: 11, lineHeight: 12, letterSpacing: 0.4 },
  tab:         { color: '#636363', fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
  button:      { color: '#FFFFFF', fontFamily: 'Inter-Bold',  fontSize: 16, lineHeight: 16 },
  flag:        { color: '#F2C94C', fontFamily: 'Inter-ExtraBold', fontSize: 10, lineHeight: 11, letterSpacing: 0.3 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Active-Workout Header (title + stat pills)

```tsx
// components/WorkoutHeader.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WorkoutHeader({
  title, time, volume, setCount, onFinish,
}: {
  title: string; time: string; volume: string; setCount: number; onFinish: () => void;
}) {
  const Pill = ({ label, value }: { label: string; value: string }) => (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4,
                    backgroundColor: colors.input, borderRadius: 7,
                    paddingVertical: 5, paddingHorizontal: 10 }}>
      <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 12, color: colors.textSecondary }}>{label}</Text>
      <Text style={{ fontFamily: 'Inter-Bold', fontSize: 12, color: colors.blue, fontVariant: ['tabular-nums'] }}>{value}</Text>
    </View>
  );
  return (
    <View style={{ paddingHorizontal: 18, paddingBottom: 10, gap: 8,
                    borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
        <Text style={typography.workout}>{title}</Text>
        <Pressable onPress={onFinish}
          style={{ backgroundColor: colors.blue, paddingVertical: 8, paddingHorizontal: 16, borderRadius: 8 }}>
          <Text style={{ color: '#fff', fontFamily: 'Inter-Bold', fontSize: 13 }}>Finish</Text>
        </Pressable>
      </View>
      <View style={{ flexDirection: 'row', gap: 8 }}>
        <Pill label="Time" value={time} />
        <Pill label="Volume" value={volume} />
        <Pill label="Sets" value={String(setCount)} />
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

export function SetRow({
  label, isWarmup, previous,
}: { label: string; isWarmup?: boolean; previous: string }) {
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
      style={[typography.setCell, {
        flex: 1, height: 34, textAlign: 'center', borderRadius: 7,
        backgroundColor: done ? alpha.rowFill : colors.input,
        color: done ? colors.done : colors.textPrimary,
        borderWidth: 1.5, borderColor: focused === k ? colors.blue : 'transparent',
      }]}
    />
  );

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, paddingVertical: 6 }}>
      <Text style={{ width: 36, textAlign: 'center', fontFamily: 'Inter-Bold', fontSize: 14,
                     color: isWarmup ? colors.warn : colors.textSecondary }}>{label}</Text>
      <Text style={[typography.caption, { flex: 1, textAlign: 'center', color: colors.textTertiary,
                     fontVariant: ['tabular-nums'] }]}>{previous}</Text>
      <Cell value={weight} set={setWeight} k="w" />
      <Cell value={reps} set={setReps} k="r" />
      <Pressable
        onPress={() => { setDone((d) => !d); Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); }}
        style={{
          width: 24, height: 24, borderRadius: 7, alignItems: 'center', justifyContent: 'center',
          backgroundColor: done ? colors.done : 'transparent',
          borderWidth: 1.5, borderColor: done ? colors.done : colors.divider,
        }}>
        {done ? <Ionicons name="checkmark" size={13} color="#fff" /> : null}
      </Pressable>
    </View>
  );
}
```

### Rest-Timer Bar

```tsx
// components/RestBar.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, alpha } from '../theme/colors';

export function RestBar({
  remaining, total, onAdjust,
}: { remaining: number; total: number; onAdjust: (s: number) => void }) {
  const fill = total === 0 ? 0 : remaining / total;
  const mmss = `${Math.floor(remaining / 60)}:${String(Math.floor(remaining % 60)).padStart(2, '0')}`;
  return (
    <View style={{ height: 40, marginHorizontal: 18, borderRadius: 8, overflow: 'hidden',
                    backgroundColor: colors.blueSoft, justifyContent: 'center' }}>
      <View style={{ position: 'absolute', left: 0, top: 0, bottom: 0,
                      width: `${fill * 100}%`, backgroundColor: alpha.restFill }} />
      <View style={{ flexDirection: 'row', alignItems: 'center', paddingHorizontal: 12, gap: 10 }}>
        <Ionicons name="timer-outline" size={16} color={colors.blue} />
        <Text style={{ flex: 1, fontFamily: 'Inter-Bold', fontSize: 13, color: colors.blue,
                       fontVariant: ['tabular-nums'] }}>Rest Timer · {mmss}</Text>
        <Pressable onPress={() => onAdjust(-15)}>
          <Text style={{ fontFamily: 'Inter-Bold', fontSize: 12, color: colors.textSecondary }}>−15</Text>
        </Pressable>
        <Pressable onPress={() => onAdjust(15)}>
          <Text style={{ fontFamily: 'Inter-Bold', fontSize: 12, color: colors.textSecondary }}>+15</Text>
        </Pressable>
      </View>
    </View>
  );
}
```

### PR Flag

```tsx
// components/PRFlag.tsx
import { useEffect } from 'react';
import { Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors, alpha } from '../theme/colors';
import { typography } from '../theme/typography';

export function PRFlag({ text }: { text: string }) {
  const s = useSharedValue(0.92);
  const o = useSharedValue(0);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: s.value }], opacity: o.value }));

  useEffect(() => {
    s.value = withSpring(1, { damping: 11, stiffness: 150 });
    o.value = withSpring(1);
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
  }, []);

  return (
    <Animated.View style={[style, {
      alignSelf: 'flex-start', paddingVertical: 2, paddingHorizontal: 6,
      borderRadius: 5, backgroundColor: alpha.prFlag,
    }]}>
      <Text style={[typography.flag, { textTransform: 'uppercase' }]}>{text}</Text>
    </Animated.View>
  );
}
```

### Exercise Card (flat, borderless)

```tsx
// components/ExerciseCard.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ExerciseCard({
  name, icon = 'barbell-outline', note, children, onAddSet,
}: {
  name: string; icon?: any; note?: string; children: React.ReactNode; onAddSet: () => void;
}) {
  return (
    <View style={{ backgroundColor: colors.card, borderRadius: 14, padding: 14,
                    marginBottom: 12, gap: 10 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10 }}>
        <View style={{ width: 34, height: 34, borderRadius: 9, backgroundColor: colors.surface3,
                        alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name={icon} size={19} color={colors.blue} />
        </View>
        <Text style={[typography.exercise, { flex: 1 }]}>{name}</Text>
        <Ionicons name="ellipsis-horizontal" size={18} color={colors.textTertiary} />
      </View>
      {note ? <Text style={typography.caption}>{note}</Text> : null}
      {children}
      <Pressable onPress={onAddSet} style={{
        height: 34, borderRadius: 8, backgroundColor: colors.input,
        alignItems: 'center', justifyContent: 'center',
      }}>
        <Text style={{ fontFamily: 'Inter-Bold', fontSize: 13, color: colors.blue }}>+ Add Set</Text>
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
          backgroundColor: 'rgba(26,26,26,0.94)',
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}>
      <Tabs.Screen name="profile"   options={{ title: 'Profile',   tabBarIcon: ({ color }) => <Ionicons name="menu"        size={22} color={color} /> }} />
      <Tabs.Screen name="history"   options={{ title: 'History',   tabBarIcon: ({ color }) => <Ionicons name="calendar"    size={22} color={color} /> }} />
      <Tabs.Screen name="index"     options={{ title: 'Workout',   tabBarIcon: ({ color }) => <Ionicons name="add-circle" size={24} color={color} /> }} />
      <Tabs.Screen name="exercises" options={{ title: 'Exercises', tabBarIcon: ({ color }) => <Ionicons name="barbell"     size={22} color={color} /> }} />
      <Tabs.Screen name="settings"  options={{ title: 'Settings',  tabBarIcon: ({ color }) => <Ionicons name="settings"    size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Set check — green cells + success haptic + auto-run rest
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
// cell backgroundColor toggles to alpha.rowFill (instant or LayoutAnimation 140ms)

// PR flag — fade + spring scale-in + light haptic
s.value = withSpring(1, { damping: 11, stiffness: 150 });
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Add set — new row enters
import Animated, { FadeInDown } from 'react-native-reanimated';
// <Animated.View entering={FadeInDown.duration(200)}> ...row... </Animated.View>

// Finish → post-workout summary: push a modal route
// router.push('/workout/summary')  — present full-screen (300ms)

// Rest bar — drive the fill width from a 1s-tick countdown (or withTiming linear over total)

// Haptics: Success on set check, Light tick on rest ±/end and PR
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Strong's iconography is minimal and functional.

| Purpose | Ionicons |
|---------|----------|
| Profile | `menu` |
| History | `calendar` |
| Workout (center) | `add-circle` |
| Exercises | `barbell` |
| Settings | `settings` |
| Set checkmark | `checkmark` |
| Exercise menu | `ellipsis-horizontal` |
| Rest timer | `timer-outline` |
| Add | `add` |
| PR flag | `caret-up` |
| Superset | `link` |
| Plate calculator | `disc-outline` |
| Reorder | `reorder-three` |
| Delete | `trash-outline` |
| Volume / 1RM chart | `stats-chart` |
| Body measurements | `resize-outline` |
| Search | `search` |
| Share | `share-outline` |

## 7. Platform Notes

- **Font choice**: Inter is SIL OFL — free to bundle. Ship one face; do not offer alternates (brand consistency)
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on every numeric `<Text>` (weights, reps, sets, volume, 1RM, timers) so the set table and timers never reflow
- **Status bar**: `<StatusBar style="light" />` — Strong is dark-first
- **Safe area**: wrap screens in `SafeAreaView`; the rest bar should float above the keyboard when a set cell is focused — use `KeyboardAvoidingView` / `react-native-keyboard-controller`
- **Dynamic Type**: keep `allowFontScaling={false}` on column labels, tab labels, the PR flag, and the set-index marker (table layout is digit-width-sensitive); allow scaling on titles/body
- **Live Activity**: the rest-timer countdown should mirror to Dynamic Island / Lock Screen via a config plugin — Expo Go cannot host Live Activities; use a development build
- **Dark mode**: default to dark; expose an opt-in light theme via a settings context that swaps `canvas`/`card`/`input`/`divider` to the `light*` tokens
- **Keyboard**: use `keyboardType="decimal-pad"` on set cells; `selectTextOnFocus` for fast overwrite
- **Accessibility**: every set row is an accessible element labelled "Set {label}, previous {prev}, {weight} kg, {reps} reps, {logged/not logged}"; the checkmark is a toggle; announce PR via `AccessibilityInfo.announceForAccessibility`
- **Flat cards**: exercise cards have no border by design — do not add one for "definition"; the `#242424` surface lift is the depth cue
- **Drag-to-reorder exercises**: use `react-native-draggable-flatlist`; soft haptic on lift
