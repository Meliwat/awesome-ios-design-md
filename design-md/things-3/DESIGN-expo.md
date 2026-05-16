# Things 3 (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Things 3's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#FFFFFF',
  surface1: '#F5F6F8',
  surface2: '#ECECEC',
  divider:  '#ECECEC',

  textPrimary:   '#1D1D1F',
  textSecondary: '#8A8A8E',
  textTertiary:  '#C7C7CC',

  blue:        '#4F97FF',
  bluePressed: '#3D7FE0',
  blueTint:    'rgba(79,151,255,0.10)',

  today:       '#FFD60A',
  todayPressed:'#E6BE00',
  deadline:    '#FF3B30',

  darkCanvas:  '#1C1C1E',
  darkSurface: '#2C2C2E',
} as const;

export type ThingsColor = keyof typeof colors;
```

## 2. Typography

Things uses SF Pro. On iOS use the `System` family (it *is* SF Pro). Load Inter only as the Android/cross-platform substitute.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    // iOS resolves 'System' to SF Pro automatically; load Inter for Android parity:
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
import { Platform, type TextStyle } from 'react-native';

const sans = Platform.select({ ios: 'System', default: 'Inter-Regular' });
const sansSemi = Platform.select({ ios: 'System', default: 'Inter-SemiBold' });
const sansBold = Platform.select({ ios: 'System', default: 'Inter-Bold' });

export const typography = {
  listTitle:    { fontFamily: sansBold, fontWeight: '700' as const, fontSize: 28, lineHeight: 34, letterSpacing: 0.3, color: '#1D1D1F' },
  projectTitle: { fontFamily: sansBold, fontWeight: '700' as const, fontSize: 24, lineHeight: 29, letterSpacing: 0.3, color: '#1D1D1F' },
  heading:      { fontFamily: sansBold, fontWeight: '700' as const, fontSize: 17, lineHeight: 22, letterSpacing: -0.2, color: '#1D1D1F' },
  taskTitle:    { fontFamily: sans,     fontWeight: '400' as const, fontSize: 17, lineHeight: 23, letterSpacing: -0.2, color: '#1D1D1F' },
  body:         { fontFamily: sans,     fontWeight: '400' as const, fontSize: 15, lineHeight: 22, color: '#1D1D1F' },
  subtitle:     { fontFamily: sans,     fontWeight: '400' as const, fontSize: 14, lineHeight: 19, color: '#8A8A8E' },
  metadata:     { fontFamily: sans,     fontWeight: '400' as const, fontSize: 13, lineHeight: 17, color: '#8A8A8E' },
  button:       { fontFamily: sansSemi, fontWeight: '600' as const, fontSize: 17, lineHeight: 17 },
  sidebar:      { fontFamily: sans,     fontWeight: '400' as const, fontSize: 16, lineHeight: 21, color: '#1D1D1F' },
  datePill:     { fontFamily: sansSemi, fontWeight: '600' as const, fontSize: 13, lineHeight: 13, color: '#4F97FF' },
  count:        { fontFamily: sans,     fontWeight: '400' as const, fontSize: 15, lineHeight: 15, color: '#8A8A8E', fontVariant: ['tabular-nums'] as const },
  tinyUpper:    { fontFamily: sansBold, fontWeight: '700' as const, fontSize: 12, lineHeight: 14, letterSpacing: 0.5, textTransform: 'uppercase' as const, color: '#8A8A8E' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Circular Checkbox + Fill Animation (the signature delight)

```tsx
// components/Checkbox.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function Checkbox({ done, onToggle, size = 22 }: { done: boolean; onToggle: () => void; size?: number }) {
  const fill = useSharedValue(done ? 1 : 0);
  const check = useSharedValue(done ? 1 : 0);

  const fillStyle = useAnimatedStyle(() => ({ transform: [{ scale: fill.value }] }));
  const checkStyle = useAnimatedStyle(() => ({ opacity: check.value, transform: [{ scale: 0.3 + check.value * 0.7 }] }));

  return (
    <Pressable
      hitSlop={12}
      onPress={() => {
        const next = !done;
        fill.value = withTiming(next ? 1 : 0, { duration: 180 });
        check.value = withSpring(next ? 1 : 0, { damping: 9 });
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
        onToggle();
      }}
      style={{ width: size, height: size, alignItems: 'center', justifyContent: 'center' }}
    >
      <Animated.View style={{ position: 'absolute', width: size, height: size, borderRadius: size / 2, borderWidth: 1.5, borderColor: colors.textTertiary, opacity: done ? 0 : 1 }} />
      <Animated.View style={[{ position: 'absolute', width: size, height: size, borderRadius: size / 2, backgroundColor: colors.blue }, fillStyle]} />
      <Animated.View style={checkStyle}>
        <Ionicons name="checkmark" size={size * 0.6} color="#fff" />
      </Animated.View>
    </Pressable>
  );
}
```

### To-Do Row

```tsx
// components/ToDoRow.tsx
import { useState } from 'react';
import { View, Text, StyleSheet } from 'react-native';
import Animated, { useAnimatedStyle, withTiming, withDelay } from 'react-native-reanimated';
import { Checkbox } from './Checkbox';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ToDoRow({
  title, tag, datePill, selected,
}: { title: string; tag?: string; datePill?: string; selected?: boolean }) {
  const [done, setDone] = useState(false);
  const rowStyle = useAnimatedStyle(() => ({
    opacity: withDelay(done ? 250 : 0, withTiming(done ? 0 : 1, { duration: 250 })),
  }));

  return (
    <Animated.View style={[styles.row, selected && { backgroundColor: colors.blueTint }, rowStyle]}>
      <Checkbox done={done} onToggle={() => setDone((d) => !d)} />
      <View style={{ flex: 1, gap: 4 }}>
        <Text style={[typography.taskTitle, done && { color: colors.textTertiary, textDecorationLine: 'line-through' }]}>
          {title}
        </Text>
        {(tag || datePill) && (
          <View style={{ flexDirection: 'row', gap: 6, alignItems: 'center' }}>
            {tag && (
              <View style={styles.tag}><Text style={typography.metadata}>{tag}</Text></View>
            )}
            {datePill && <Text style={typography.datePill}>{datePill}</Text>}
          </View>
        )}
      </View>
    </Animated.View>
  );
}

const styles = StyleSheet.create({
  row: { flexDirection: 'row', alignItems: 'flex-start', gap: 12, paddingHorizontal: 20, paddingVertical: 12, minHeight: 44, borderRadius: 8 },
  tag: { paddingHorizontal: 8, paddingVertical: 3, borderRadius: 500, backgroundColor: colors.surface1 },
});
```

### Magic-Plus Button (the signature control)

```tsx
// components/MagicPlus.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function MagicPlus({ onAdd }: { onAdd: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.94, { damping: 12 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 12 }))}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onAdd(); }}
      // onLongPress + PanResponder: lift to scale 1.05, show a #4F97FF insertion line
      // tracking the nearest row gap; on release insert an editable to-do there.
    >
      <Animated.View style={[{
        width: 56, height: 56, borderRadius: 28, backgroundColor: colors.blue,
        alignItems: 'center', justifyContent: 'center',
        shadowColor: '#4F97FF', shadowOpacity: 0.35, shadowRadius: 20, shadowOffset: { width: 0, height: 8 },
      }, style]}>
        <Ionicons name="add" size={26} color="#fff" />
      </Animated.View>
    </Pressable>
  );
}
```

### List Title Header

```tsx
// components/ListTitleHeader.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ListTitleHeader({ title, isToday }: { title: string; isToday?: boolean }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, paddingHorizontal: 20, paddingTop: 24, paddingBottom: 8 }}>
      {isToday && <Ionicons name="star" size={22} color={colors.today} />}
      <Text style={typography.listTitle}>{title}</Text>
    </View>
  );
}
```

### Section Heading + Hairline Divider

```tsx
// components/SectionHeading.tsx
import { Pressable, View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SectionHeading({ title, collapsed, onToggle }: { title: string; collapsed: boolean; onToggle: () => void }) {
  const rot = useSharedValue(collapsed ? -1 : 0);
  const chev = useAnimatedStyle(() => ({ transform: [{ rotate: `${rot.value * 90}deg` }] }));
  return (
    <View>
      <Pressable
        onPress={() => { rot.value = withTiming(collapsed ? 0 : -1, { duration: 200 }); onToggle(); }}
        style={{ flexDirection: 'row', alignItems: 'center', gap: 6, paddingHorizontal: 20, paddingTop: 24, paddingBottom: 8 }}
      >
        <Text style={typography.heading}>{title}</Text>
        <Animated.View style={chev}><Ionicons name="chevron-down" size={12} color={colors.textSecondary} /></Animated.View>
      </Pressable>
      <View style={{ height: 1, backgroundColor: colors.divider, marginHorizontal: 20 }} />
    </View>
  );
}
```

### Project Pie-Progress Ring

```tsx
// components/PieProgress.tsx
import Svg, { Circle } from 'react-native-svg';
import { colors } from '../theme/colors';

export function PieProgress({ fraction, size = 20 }: { fraction: number; size?: number }) {
  const r = size / 2 - 1;
  const c = 2 * Math.PI * r;
  return (
    <Svg width={size} height={size} viewBox={`0 0 ${size} ${size}`}>
      <Circle cx={size / 2} cy={size / 2} r={r} stroke={colors.divider} strokeWidth={2} fill="none" />
      <Circle
        cx={size / 2} cy={size / 2} r={r}
        stroke={colors.blue} strokeWidth={2} fill="none" strokeLinecap="round"
        strokeDasharray={`${c * fraction} ${c}`}
        transform={`rotate(-90 ${size / 2} ${size / 2})`}
      />
    </Svg>
  );
}
```

## 4. Sidebar Navigation (no bottom tab bar)

Things has no tab bar — use a `Drawer` (`expo-router` drawer), not `Tabs`. Keep the scrim soft.

```tsx
// app/_layout.tsx
import { Drawer } from 'expo-router/drawer';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export default function Layout() {
  return (
    <Drawer
      screenOptions={{
        headerStyle: { backgroundColor: colors.canvas, shadowColor: 'transparent' },
        headerTintColor: colors.textPrimary,
        drawerStyle: { backgroundColor: colors.canvas, width: 300 },
        drawerActiveBackgroundColor: colors.blueTint,
        drawerActiveTintColor: colors.textPrimary,
        drawerInactiveTintColor: colors.textPrimary,
        drawerLabelStyle: { fontFamily: 'System', fontSize: 16, marginLeft: -16 },
        sceneContainerStyle: { backgroundColor: colors.canvas },
      }}
    >
      <Drawer.Screen name="inbox"    options={{ title: 'Inbox',    drawerIcon: ({ color }) => <Ionicons name="file-tray" size={18} color={color} /> }} />
      <Drawer.Screen name="today"    options={{ title: 'Today',    drawerIcon: () => <Ionicons name="star" size={18} color={colors.today} /> }} />
      <Drawer.Screen name="upcoming" options={{ title: 'Upcoming', drawerIcon: ({ color }) => <Ionicons name="calendar" size={18} color={color} /> }} />
      <Drawer.Screen name="anytime"  options={{ title: 'Anytime',  drawerIcon: ({ color }) => <Ionicons name="layers" size={18} color={color} /> }} />
      <Drawer.Screen name="someday"  options={{ title: 'Someday',  drawerIcon: ({ color }) => <Ionicons name="archive" size={18} color={color} /> }} />
    </Drawer>
  );
}
```

## 5. Motion

```tsx
// Checkbox: center-out fill (withTiming 180ms) + check spring + soft haptic, then row fade-out (delayed)
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Magic-Plus tap: spring scale 0.94 + medium haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Heading collapse: chevron rotate (withTiming 200ms) + LinearTransition on child rows
import { LinearTransition } from 'react-native-reanimated';
<Animated.View layout={LinearTransition.duration(200)} />

// Pie-progress: animate strokeDasharray fraction over 300ms
// Sidebar: drawer default spring with a soft scrim (overlayColor 'rgba(0,0,0,0.2)')
```

## 6. Icon Library

Use `@expo/vector-icons`:

| Purpose | Ionicons |
|---------|----------|
| Unchecked checkbox | drawn (bordered circle) |
| Completed check | `checkmark` |
| Magic-Plus | `add` |
| Today / scheduled | `star` |
| Inbox (sidebar) | `file-tray` |
| Upcoming (sidebar) | `calendar` |
| Anytime (sidebar) | `layers` |
| Someday (sidebar) | `archive` |
| Logbook (sidebar) | `book` |
| Heading chevron | `chevron-down` |
| Deadline flag | `flag` |
| Search | `search` |
| More | `ellipsis-horizontal` |
| Tag | `pricetag` |

## 7. Platform Notes

- **No tab bar**: Things' IA is a drawer + list — do not add `Tabs`
- **SF Pro on iOS**: use `fontFamily: 'System'` on iOS so you get the real system face + optical sizing; Inter is the Android substitute
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` — dark content on the white canvas
- **Safe area**: wrap with `SafeAreaView` from `react-native-safe-area-context`; the Magic-Plus floats above the home indicator
- **Whitespace**: keep the generous 20pt horizontal margins and roomy row padding — do not tighten them for density
- **Dynamic Type**: this is a reading app — leave font scaling ON for titles/tasks/notes; set `allowFontScaling={false}` only on the 22pt checkbox-adjacent layout and tabular counts
- **Accessibility**: give the checkbox `accessibilityRole="checkbox"` + `accessibilityState={{ checked: done }}`; announce "scheduled today" as text alongside the star; provide a tap fallback for the Magic-Plus drag gesture
