# Asana (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Asana's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceGray:    '#F9F8F8',
  surfacePressed: '#F0EFEF',
  divider:        '#EDEBE9',
  cardBorder:     '#E8E6E4',

  // Surfaces (dark)
  darkCanvas:     '#1E1F21',
  darkSurface1:   '#252628',
  darkSurface2:   '#2E2F31',
  darkDivider:    '#35363A',
  darkCardBorder: '#3A3B3F',

  // Text
  textPrimary:      '#1E1F21',
  textSecondary:    '#6D6E6F',
  textTertiary:     '#9CA3A6',
  darkTextPrimary:  '#F5F4F2',
  darkTextSecondary:'#A9A9AA',
  darkTextTertiary: '#6F7073',

  // Brand
  coral:        '#F06A6A',
  coralPressed: '#D85B5B',
  actionBlue:   '#4573D2',

  // Object palette
  plum:    '#4573D2',
  aqua:    '#4ECBC4',
  green:   '#62D26F',
  yellow:  '#F8DF72',
  orange:  '#F1BD6C',
  magenta: '#F26FD3',
  indigo:  '#5A3FFF',
  coolGray:'#8DA3B0',

  // Semantic
  success: '#62D26F',
  error:   '#E8384F',
  warning: '#F8DF72',
} as const;

export type AsanaColor = keyof typeof colors;

// Pair an object hue with its ~16% tint fill (8-digit hex alpha)
export const tagStyles = {
  plum:    { solid: colors.plum,    tint: '#4573D229' },
  aqua:    { solid: colors.aqua,    tint: '#4ECBC429' },
  green:   { solid: colors.green,   tint: '#62D26F29' },
  yellow:  { solid: colors.yellow,  tint: '#F8DF7233' },
  orange:  { solid: colors.orange,  tint: '#F1BD6C2E' },
  magenta: { solid: colors.magenta, tint: '#F26FD329' },
  indigo:  { solid: colors.indigo,  tint: '#5A3FFF29' },
} as const;

export const statusStyles = {
  onTrack:  { solid: colors.aqua,    tint: '#4ECBC42E', label: 'On track' },
  atRisk:   { solid: colors.yellow,  tint: '#F8DF7233', label: 'At risk' },
  offTrack: { solid: colors.error,   tint: '#E8384F2E', label: 'Off track' },
  complete: { solid: colors.success, tint: '#62D26F2E', label: 'Complete' },
} as const;
```

## 2. Typography

Load Inter via `expo-font`. Asana uses Inter across product surfaces.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold':require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#1E1F21' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.5 },
  navTitle:    { ...primary, fontFamily: 'Inter-Bold',      fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:     { ...primary, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  subsection:  { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 18, lineHeight: 23 },
  body:        { ...primary, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 24 },
  taskRow:     { ...primary, fontFamily: 'Inter-Medium',    fontSize: 15, lineHeight: 20 },
  listLabel:   { ...primary, fontFamily: 'Inter-Bold',      fontSize: 13, lineHeight: 16, letterSpacing: 0.2 },
  meta:        { color: '#6D6E6F', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 19 },
  pill:        { fontFamily: 'Inter-SemiBold', fontSize: 12, lineHeight: 12, letterSpacing: 0.1 },
  button:      { fontFamily: 'Inter-SemiBold', fontSize: 15, lineHeight: 15 },
  textAction:  { color: '#4573D2', fontFamily: 'Inter-SemiBold', fontSize: 14, lineHeight: 14 },
  avatar:      { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 10, lineHeight: 10, letterSpacing: 0.2 },
  tab:         { fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Task Row (with Completion Circle)

```tsx
// components/TaskRow.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors, tagStyles } from '../theme/colors';
import { typography } from '../theme/typography';

type Tag = { text: string; style: { solid: string; tint: string } };

export function TaskRow({
  name, projectTag, due, assigneeInitials, assigneeColor,
}: {
  name: string;
  projectTag?: Tag;
  due?: { text: string; overdue?: boolean };
  assigneeInitials: string;
  assigneeColor: string;
}) {
  const [done, setDone] = useState(false);
  const scale = useSharedValue(1);
  const circleStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const toggle = () => {
    const next = !done;
    setDone(next);
    if (next) {
      Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
      scale.value = withSequence(withTiming(0.8, { duration: 80 }), withTiming(1.1, { duration: 80 }), withTiming(1, { duration: 60 }));
    }
  };

  const dueStyle = due?.overdue
    ? { solid: colors.error, tint: '#E8384F2E' }
    : { solid: colors.orange, tint: '#F1BD6C2E' };

  return (
    <View style={{ flexDirection: 'row', alignItems: 'flex-start', gap: 12, paddingHorizontal: 18, paddingVertical: 11, borderBottomWidth: 1, borderBottomColor: colors.divider }}>
      <Pressable onPress={toggle} hitSlop={12}>
        <Animated.View style={[{
          width: 20, height: 20, borderRadius: 10, alignItems: 'center', justifyContent: 'center',
          borderWidth: 1.5,
          borderColor: done ? colors.success : colors.textTertiary,
          backgroundColor: done ? colors.success : 'transparent',
        }, circleStyle]}>
          {done ? <Ionicons name="checkmark" size={11} color={colors.darkCanvas} /> : null}
        </Animated.View>
      </Pressable>

      <View style={{ flex: 1 }}>
        <Text style={[typography.taskRow, done && { color: colors.textTertiary, textDecorationLine: 'line-through' }]}>{name}</Text>
        <View style={{ flexDirection: 'row', gap: 8, marginTop: 6 }}>
          {projectTag ? <Pill text={projectTag.text} style={projectTag.style} /> : null}
          {due ? <Pill text={due.text} style={dueStyle} /> : null}
        </View>
      </View>

      <View style={{ width: 22, height: 22, borderRadius: 11, backgroundColor: assigneeColor, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={typography.avatar}>{assigneeInitials}</Text>
      </View>
    </View>
  );
}

export function Pill({ text, style }: Tag) {
  return (
    <View style={{ backgroundColor: style.tint, borderRadius: 999, paddingVertical: 3, paddingHorizontal: 9 }}>
      <Text style={[typography.pill, { color: style.solid }]}>{text}</Text>
    </View>
  );
}
```

### Section Header (collapsible)

```tsx
// components/SectionHeader.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SectionHeader({ title, count, expanded, onToggle }: {
  title: string; count: number; expanded: boolean; onToggle: () => void;
}) {
  const rot = useSharedValue(expanded ? 90 : 0);
  const caret = useAnimatedStyle(() => ({ transform: [{ rotate: `${rot.value}deg` }] }));
  return (
    <Pressable
      onPress={() => { rot.value = withTiming(expanded ? 0 : 90, { duration: 150 }); onToggle(); }}
      style={{ flexDirection: 'row', alignItems: 'center', gap: 8, paddingHorizontal: 18, paddingTop: 14, paddingBottom: 8 }}
    >
      <Animated.View style={caret}>
        <Ionicons name="chevron-forward" size={12} color={colors.textSecondary} />
      </Animated.View>
      <Text style={typography.listLabel}>{title}</Text>
      <Text style={[typography.pill, { color: colors.textTertiary }]}>{count}</Text>
    </Pressable>
  );
}
```

### Status Update Card

```tsx
// components/StatusUpdateCard.tsx
import { Text, View } from 'react-native';
import { colors, statusStyles } from '../theme/colors';
import { typography } from '../theme/typography';

export function StatusUpdateCard({
  status, postedDate, title, body,
}: {
  status: keyof typeof statusStyles;
  postedDate: string;
  title: string;
  body: string;
}) {
  const s = statusStyles[status];
  return (
    <View style={{
      padding: 18, borderRadius: 12, backgroundColor: colors.canvas,
      borderWidth: 1, borderColor: colors.divider,
    }}>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' }}>
        <View style={{ backgroundColor: s.tint, borderRadius: 999, paddingVertical: 6, paddingHorizontal: 14 }}>
          <Text style={[typography.pill, { color: s.solid, fontFamily: 'Inter-Bold' }]}>{s.label}</Text>
        </View>
        <Text style={typography.meta}>{postedDate}</Text>
      </View>
      <Text style={[typography.section, { marginTop: 12 }]}>{title}</Text>
      <Text style={[typography.body, { marginTop: 8 }]}>{body}</Text>
    </View>
  );
}
```

### Floating Action Button

```tsx
// components/CreateTaskFAB.tsx
import { Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function CreateTaskFAB({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        position: 'absolute', right: 18, bottom: 86,
        width: 56, height: 56, borderRadius: 28,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.coralPressed : colors.coral,
        shadowColor: colors.coral, shadowOpacity: 0.45, shadowRadius: 20, shadowOffset: { width: 0, height: 8 },
        elevation: 8,
        transform: [{ scale: pressed ? 0.94 : 1 }],
      })}
    >
      <Ionicons name="add" size={24} color="#FFFFFF" />
    </Pressable>
  );
}
```

### Board Column + Card

```tsx
// components/BoardColumn.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { Pill } from './TaskRow';

export function BoardColumn({ title, children }: { title: string; children: React.ReactNode }) {
  return (
    <View style={{ width: 280, backgroundColor: colors.surfaceGray, borderRadius: 12, padding: 12 }}>
      <Text style={[typography.pill, { color: colors.textSecondary, fontFamily: 'Inter-Bold', marginBottom: 8 }]}>
        {title.toUpperCase()}
      </Text>
      {children}
    </View>
  );
}

export function BoardCard({ title, tag }: { title: string; tag: { text: string; style: { solid: string; tint: string } } }) {
  return (
    <View style={{
      backgroundColor: colors.canvas, borderRadius: 10, padding: 12, marginBottom: 8,
      borderWidth: 1, borderColor: colors.cardBorder,
      shadowColor: '#000', shadowOpacity: 0.06, shadowRadius: 3, shadowOffset: { width: 0, height: 1 }, elevation: 1,
    }}>
      <Text style={[typography.taskRow, { fontSize: 13 }]}>{title}</Text>
      <View style={{ marginTop: 8, alignSelf: 'flex-start' }}><Pill text={tag.text} style={tag.style} /></View>
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
        tabBarActiveTintColor:  colors.coral,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 1, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home-outline"           size={22} color={color} /> }} />
      <Tabs.Screen name="tasks"    options={{ title: 'My Tasks', tabBarIcon: ({ color }) => <Ionicons name="checkmark-circle-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="inbox"    options={{ title: 'Inbox',    tabBarIcon: ({ color }) => <Ionicons name="notifications-outline"   size={22} color={color} /> }} />
      <Tabs.Screen name="search"   options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search"                  size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Task complete — circle bounce + success haptic
import { withSequence, withTiming } from 'react-native-reanimated';
scale.value = withSequence(withTiming(0.8, { duration: 80 }), withTiming(1.1, { duration: 80 }), withTiming(1, { duration: 60 }));
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Section collapse/expand — caret rotate + LayoutAnimation / Reanimated layout
rot.value = withTiming(expanded ? 0 : 90, { duration: 150 });

// FAB → new-task — present a modal route ('modal' presentation in expo-router)

// Board card drag — react-native-draggable-flatlist or Reanimated gesture; soft impact on drag start
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// View tab underline — Animated translateX over 200ms ease-out

// Celebration creature — Animated.View flying translateX -80 → width+80 over 1200ms then opacity → 0
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Home | `home-outline` |
| My Tasks | `checkmark-circle-outline` |
| Inbox | `notifications-outline` |
| Search | `search` |
| Task incomplete | `ellipse-outline` |
| Task complete | `checkmark` (inside filled circle) |
| Add / FAB | `add` |
| Section caret | `chevron-forward` (rotate 90°) |
| Due date | `calendar-outline` |
| Assignee (empty) | `person-circle-outline` |
| Subtask | `return-down-forward` |
| Attachment | `attach` |
| Comment | `chatbubble-outline` |
| More | `ellipsis-horizontal` |
| Back | `chevron-back` |
| Filter | `filter` |
| Board view | `albums-outline` |
| Calendar view | `calendar-outline` |
| Timeline view | `bar-chart-outline` |

## 7. Platform Notes

- **Font choice**: Inter is SIL OFL — free to bundle. Ship Regular / Medium / SemiBold / Bold / ExtraBold
- **Status bar**: `<StatusBar style="dark" />` on light mode, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; the FAB sits 86pt from the bottom to clear the tab bar + home indicator
- **Dynamic Type**: React Native respects system scale on `<Text>`; set `allowFontScaling={false}` on pills, tab labels, avatar initials, list section labels
- **Tabular numerals**: add `fontVariant: ['tabular-nums']` to due-date / count text styles so the list doesn't jitter
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` and bump tag tints (~25% alpha); keep `coral` and `success` identical; add 1pt `darkCardBorder` to board cards since shadows vanish on dark
- **Completion haptic**: `Haptics.notificationAsync(Success)` on complete; `Haptics.impactAsync(Soft)` on drag start & section toggle
- **Accessibility**: completion circle and row body are separate touchables — circle = `accessibilityLabel="Mark complete"`, row = `accessibilityRole="button"` opening detail
- **Drag-to-reorder board cards**: use `react-native-draggable-flatlist`; lift-to-drag soft haptic on start
- **Reduce Motion**: gate the circle bounce + celebration creature behind `AccessibilityInfo.isReduceMotionEnabled` — fall back to a plain fill crossfade
