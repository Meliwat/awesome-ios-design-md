# ClickUp (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates ClickUp's visual language into paste-ready Expo / React Native code: a design-token module, the brand gradient, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceGray:    '#F7F7FB',
  surfacePressed: '#EDEDF4',
  divider:        '#E7E7EF',
  cardBorder:     '#DEDEE9',

  // Surfaces (dark)
  darkCanvas:     '#1B1B2E',
  darkSurface1:   '#232338',
  darkSurface2:   '#2C2C44',
  darkDivider:    '#383852',
  darkCardBorder: '#42425C',

  // Text
  textPrimary:      '#1B1B2E',
  textSecondary:    '#5E5E7A',
  textTertiary:     '#9292A8',
  darkTextPrimary:  '#F1F1F6',
  darkTextSecondary:'#A6A6BC',
  darkTextTertiary: '#6E6E88',

  // Brand
  purple:        '#7B68EE',
  purplePressed: '#6A57DD',
  pink:          '#FD71AF',
  blue:          '#49CCF9',

  // Functional palette
  green:  '#2ECC71',
  red:    '#F44E6E',
  orange: '#FF9F1A',
  yellow: '#FFCC00',
  teal:   '#1ECBE1',
  gray:   '#5B5B79',

  // Semantic
  success: '#2ECC71',
  error:   '#F44E6E',
  warning: '#FF9F1A',
} as const;

export type ClickUpColor = keyof typeof colors;

// The 3-stop brand gradient — FAB / primary CTA / AI ONLY. Pass to <LinearGradient colors={...} />
export const brandGradient = [colors.purple, colors.pink, colors.blue] as const;
export const aiGradient = ['#7B68EE2E', '#FD71AF29', '#49CCF929'] as const; // ~18% backdrop tint

// On-solid text: blue / yellow / teal flip to dark
const DARK_ON = new Set<string>([colors.blue, colors.yellow, colors.teal]);
export const onColor = (bg: string) => (DARK_ON.has(bg) ? colors.textPrimary : '#FFFFFF');

export const priorityColors = {
  urgent: colors.red,
  high:   colors.orange,
  normal: colors.blue,
  low:    colors.textTertiary,
} as const;
```

## 2. Typography

Load Plus Jakarta Sans via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'PJS-Regular':  require('../assets/fonts/PlusJakartaSans-Regular.ttf'),
    'PJS-Medium':   require('../assets/fonts/PlusJakartaSans-Medium.ttf'),
    'PJS-SemiBold': require('../assets/fonts/PlusJakartaSans-SemiBold.ttf'),
    'PJS-Bold':     require('../assets/fonts/PlusJakartaSans-Bold.ttf'),
    'PJS-ExtraBold':require('../assets/fonts/PlusJakartaSans-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#1B1B2E' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...primary, fontFamily: 'PJS-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.5 },
  listTitle:   { ...primary, fontFamily: 'PJS-ExtraBold', fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:     { ...primary, fontFamily: 'PJS-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  subsection:  { ...primary, fontFamily: 'PJS-Bold',      fontSize: 17, lineHeight: 22 },
  body:        { ...primary, fontFamily: 'PJS-Regular',   fontSize: 15, lineHeight: 23 },
  taskName:    { ...primary, fontFamily: 'PJS-SemiBold',  fontSize: 14, lineHeight: 19 },
  statusLabel: {              fontFamily: 'PJS-ExtraBold', fontSize: 10, lineHeight: 12, letterSpacing: 0.6 },
  meta:        { color: '#5E5E7A', fontFamily: 'PJS-Regular', fontSize: 13, lineHeight: 18 },
  tag:         { fontFamily: 'PJS-Bold', fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
  button:      { color: '#FFFFFF', fontFamily: 'PJS-Bold', fontSize: 15, lineHeight: 15 },
  textAction:  { color: '#7B68EE', fontFamily: 'PJS-SemiBold', fontSize: 14, lineHeight: 14 },
  assignee:    { color: '#FFFFFF', fontFamily: 'PJS-Bold', fontSize: 9, lineHeight: 9, letterSpacing: 0.2 },
  tab:         { fontFamily: 'PJS-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Task Row (dense)

```tsx
// components/TaskRow.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

// Priority flag triangle (pure CSS borders)
function PriorityFlag({ color }: { color: string }) {
  return (
    <View style={{
      width: 0, height: 0,
      borderLeftWidth: 6, borderRightWidth: 6, borderBottomWidth: 10,
      borderLeftColor: 'transparent', borderRightColor: 'transparent', borderBottomColor: color,
    }} />
  );
}

export function TaskRow({
  name, statusColor, done, priorityColor, tags, due, assigneeInitials, assigneeColor, onCycleStatus,
}: {
  name: string;
  statusColor: string;
  done: boolean;
  priorityColor?: string;
  tags: { text: string; hue: string }[];
  due?: { text: string; overdue?: boolean };
  assigneeInitials: string;
  assigneeColor: string;
  onCycleStatus: () => void;
}) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 16, paddingVertical: 11, borderBottomWidth: 1, borderBottomColor: colors.divider }}>
      <View
        onTouchEnd={onCycleStatus}
        style={{
          width: 18, height: 18, borderRadius: 5, borderWidth: 2,
          borderColor: done ? colors.green : statusColor,
          backgroundColor: done ? colors.green : 'transparent',
          alignItems: 'center', justifyContent: 'center',
        }}
      >
        {done ? <Ionicons name="checkmark" size={10} color={colors.darkCanvas} /> : null}
      </View>

      <View style={{ flex: 1 }}>
        <Text style={[typography.taskName, done && { color: colors.textTertiary, textDecorationLine: 'line-through' }]} numberOfLines={1}>{name}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, marginTop: 6 }}>
          {priorityColor ? <PriorityFlag color={priorityColor} /> : null}
          {tags.map((t) => (
            <View key={t.text} style={{ backgroundColor: `${t.hue}38`, borderRadius: 999, paddingVertical: 2, paddingHorizontal: 7 }}>
              <Text style={[typography.tag, { color: t.hue }]}>{t.text}</Text>
            </View>
          ))}
          {due ? <Text style={[typography.meta, due.overdue && { color: colors.red }]}>{due.text}</Text> : null}
        </View>
      </View>

      <View style={{ width: 22, height: 22, borderRadius: 11, backgroundColor: assigneeColor, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={typography.assignee}>{assigneeInitials}</Text>
      </View>
    </View>
  );
}
```

### Custom-Status Group Header

```tsx
// components/StatusGroupHeader.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, onColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function StatusGroupHeader({ label, color, count, expanded, onToggle }: {
  label: string; color: string; count: number; expanded: boolean; onToggle: () => void;
}) {
  const rot = useSharedValue(expanded ? 90 : 0);
  const caret = useAnimatedStyle(() => ({ transform: [{ rotate: `${rot.value}deg` }] }));
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, paddingTop: 10, paddingBottom: 6 }}>
      <Pressable
        onPress={() => { rot.value = withTiming(expanded ? 0 : 90, { duration: 150 }); onToggle(); }}
        style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}
      >
        <Animated.View style={caret}>
          <Ionicons name="chevron-forward" size={11} color={colors.textTertiary} />
        </Animated.View>
        <View style={{ backgroundColor: color, borderRadius: 5, paddingVertical: 4, paddingHorizontal: 9 }}>
          <Text style={[typography.statusLabel, { color: onColor(color) }]}>{label.toUpperCase()}</Text>
        </View>
        <Text style={[typography.meta, { color: colors.textTertiary }]}>{count}</Text>
      </Pressable>
      <View style={{ flex: 1 }} />
      <Ionicons name="add" size={13} color={colors.textTertiary} />
    </View>
  );
}
```

### Brand-Gradient FAB (squircle) + Primary Button

```tsx
// components/GradientButtons.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { brandGradient, colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CreateFAB({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        position: 'absolute', right: 16, bottom: 84,
        shadowColor: colors.purple, shadowOpacity: 0.5, shadowRadius: 22, shadowOffset: { width: 0, height: 8 }, elevation: 10,
        transform: [{ scale: pressed ? 0.94 : 1 }],
      })}
    >
      <LinearGradient
        colors={brandGradient as unknown as string[]}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ width: 56, height: 56, borderRadius: 18, alignItems: 'center', justifyContent: 'center' }}
      >
        <Ionicons name="add" size={24} color="#FFFFFF" />
      </LinearGradient>
    </Pressable>
  );
}

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({ opacity: pressed ? 0.94 : 1 })}>
      <LinearGradient
        colors={brandGradient as unknown as string[]}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ borderRadius: 10, paddingVertical: 13, paddingHorizontal: 26, alignItems: 'center' }}
      >
        <Text style={typography.button}>{title}</Text>
      </LinearGradient>
    </Pressable>
  );
}
```

### ClickUp AI Bar

```tsx
// components/AIBar.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { aiGradient, colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AIBar({ action }: { action: string }) {
  return (
    <LinearGradient
      colors={aiGradient as unknown as string[]}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 10,
        paddingVertical: 10, paddingHorizontal: 14,
        borderRadius: 10, borderWidth: 1, borderColor: `${colors.purple}66`,
      }}
    >
      <Ionicons name="sparkles" size={16} color={colors.purple} />
      <Text style={typography.meta}>
        <Text style={{ fontFamily: 'PJS-Bold', color: colors.textPrimary }}>ClickUp AI</Text>
        {`  ·  ${action}`}
      </Text>
    </LinearGradient>
  );
}
```

### Board Card

```tsx
// components/BoardCard.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BoardCard({ statusColor, name, priorityColor, assigneeInitials, assigneeColor }: {
  statusColor: string; name: string; priorityColor?: string; assigneeInitials: string; assigneeColor: string;
}) {
  return (
    <View style={{
      borderRadius: 10, overflow: 'hidden', marginBottom: 8,
      backgroundColor: colors.canvas, borderWidth: 1, borderColor: colors.cardBorder,
      shadowColor: '#000', shadowOpacity: 0.07, shadowRadius: 4, shadowOffset: { width: 0, height: 1 }, elevation: 1,
    }}>
      <View style={{ height: 3, backgroundColor: statusColor }} />
      <View style={{ padding: 12, gap: 10 }}>
        <Text style={typography.taskName}>{name}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
          {priorityColor ? (
            <View style={{ width: 0, height: 0, borderLeftWidth: 6, borderRightWidth: 6, borderBottomWidth: 10, borderLeftColor: 'transparent', borderRightColor: 'transparent', borderBottomColor: priorityColor }} />
          ) : <View />}
          <View style={{ width: 22, height: 22, borderRadius: 11, backgroundColor: assigneeColor, alignItems: 'center', justifyContent: 'center' }}>
            <Text style={typography.assignee}>{assigneeInitials}</Text>
          </View>
        </View>
      </View>
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
        tabBarActiveTintColor:  colors.purple,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 1, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'PJS-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',   tabBarIcon: ({ color }) => <Ionicons name="home-outline"         size={22} color={color} /> }} />
      <Tabs.Screen name="tasks"   options={{ title: 'Tasks',  tabBarIcon: ({ color }) => <Ionicons name="list-outline"         size={22} color={color} /> }} />
      <Tabs.Screen name="inbox"   options={{ title: 'Inbox',  tabBarIcon: ({ color }) => <Ionicons name="notifications-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search', tabBarIcon: ({ color }) => <Ionicons name="search"               size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Status change — color crossfade + soft haptic
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
// animate border/fill color over 180ms via Reanimated interpolateColor

// Status group collapse/expand — caret rotate + layout animation
rot.value = withTiming(expanded ? 0 : 90, { duration: 150 });

// FAB → create — present a 'modal' route in expo-router

// Drag task / board card — react-native-draggable-flatlist; soft haptic on drag start; status recolors on drop
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// AI generation — animate a gradient phase (1.2s) with a MaskedView/LinearGradient; result FadeIn 200ms

// View tab underline — Animated translateX 200ms ease-out
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The priority flag is a pure-CSS triangle, not an icon.

| Purpose | Ionicons |
|---------|----------|
| Home | `home-outline` |
| Tasks | `list-outline` |
| Inbox | `notifications-outline` |
| Search | `search` |
| Add / FAB | `add` |
| AI / sparkle | `sparkles` |
| Status group chevron | `chevron-forward` (rotate 90°) |
| Task complete | `checkmark` (in status square) |
| Due date | `calendar-outline` |
| Assignee (empty) | `person-circle-outline` |
| Subtask | `return-down-forward` |
| Comment | `chatbubble-outline` |
| More | `ellipsis-horizontal` |
| Back | `chevron-back` |
| Breadcrumb sep | `chevron-forward` |
| Filter | `filter` |
| List view | `list-outline` |
| Board view | `albums-outline` |
| Calendar view | `calendar-outline` |
| Gantt view | `bar-chart-outline` |
| Doc view | `document-text-outline` |
| Slash menu | `code-slash` |

## 7. Platform Notes

- **Font choice**: Plus Jakarta Sans is SIL OFL — free to bundle. Ship Regular / Medium / SemiBold / Bold / ExtraBold
- **Gradient**: use `expo-linear-gradient`; the brand gradient is the FAB / primary CTA / AI ONLY — never a screen background
- **Status bar**: `<StatusBar style="dark" />` on light mode, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; the FAB sits 84pt from the bottom to clear the tab bar + home indicator
- **Dynamic Type**: React Native respects system scale on `<Text>`; set `allowFontScaling={false}` on status pills, tags, tab labels, assignee initials, status group labels (dense-layout-sensitive)
- **Tabular numerals**: add `fontVariant: ['tabular-nums']` to due-date / points / count text styles
- **On-color text**: always run status colors through `onColor()` — blue `#49CCF9`, yellow `#FFCC00`, teal `#1ECBE1` require dark text
- **Dark mode**: use `useColorScheme()` to swap chrome to `darkCanvas` / `darkSurface1`, but keep the gradient + functional palette identical; add 1pt `darkCardBorder` + a status-color top accent to board cards since shadows vanish
- **Status haptic**: `Haptics.impactAsync(Soft)` on status change, drag start, group toggle
- **Accessibility**: the dense row should be one accessible element with a combined label + custom actions (change status / set priority / open task); priority is announced by name, never color alone
- **Drag-to-reorder / change status**: use `react-native-draggable-flatlist`; on drop into a Board column, recolor the status accent; soft haptic
- **Reduce Motion**: gate the status crossfade + AI gradient shimmer behind `AccessibilityInfo.isReduceMotionEnabled` — snap / static instead
