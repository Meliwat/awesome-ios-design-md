# monday.com (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates monday.com's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceGray:    '#F6F7FB',
  surfacePressed: '#EDEFF5',
  divider:        '#E6E9F0',
  cellBorder:     '#D0D4E4',

  // Surfaces (dark / night)
  nightCanvas:     '#181B34',
  nightSurface1:   '#20243F',
  nightSurface2:   '#292F4C',
  nightDivider:    '#353B5C',
  nightCellBorder: '#3D436A',

  // Text
  textPrimary:      '#323338',
  textSecondary:    '#676879',
  textTertiary:     '#9699A6',
  darkTextPrimary:  '#F5F6F8',
  darkTextSecondary:'#A7AFC6',
  darkTextTertiary: '#6B7394',

  // Action
  blue:        '#0073EA',
  bluePressed: '#0060C2',

  // Multicolor data palette (full-bleed fills)
  red:        '#E2445C',
  orange:     '#FDAB3D',
  green:      '#00C875',
  brightBlue: '#579BFC',
  purple:     '#A25DDC',
  darkPurple: '#401694',
  indigo:     '#5559DF',
  yellow:     '#FFCB00',
  teal:       '#00D2D2',
  pink:       '#FF158A',
  brightGreen:'#9CD326',
  navy:       '#333D6E',

  // Semantic
  success: '#00C875',
  error:   '#E2445C',
  warning: '#FDAB3D',
} as const;

export type MondayColor = keyof typeof colors;

// On-color text: yellow / teal / bright-green flip to dark
const DARK_ON = new Set<string>([colors.yellow, colors.teal, colors.brightGreen]);
export const onColor = (bg: string) => (DARK_ON.has(bg) ? colors.textPrimary : '#FFFFFF');

// Default status set (users remap freely)
export const defaultStatuses = [
  { label: 'Done',          color: colors.green },
  { label: 'Working on it', color: colors.orange },
  { label: 'Stuck',         color: colors.red },
  { label: 'Not started',   color: colors.navy },
] as const;
```

## 2. Typography

Load Figtree via `expo-font`. monday.com uses Figtree (Poppins-class brand sans).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Figtree-Regular':  require('../assets/fonts/Figtree-Regular.ttf'),
    'Figtree-Medium':   require('../assets/fonts/Figtree-Medium.ttf'),
    'Figtree-SemiBold': require('../assets/fonts/Figtree-SemiBold.ttf'),
    'Figtree-Bold':     require('../assets/fonts/Figtree-Bold.ttf'),
    'Figtree-ExtraBold':require('../assets/fonts/Figtree-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#323338' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...primary, fontFamily: 'Figtree-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.5 },
  boardTitle:  { ...primary, fontFamily: 'Figtree-Bold',      fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  groupHeader: {              fontFamily: 'Figtree-ExtraBold', fontSize: 22, lineHeight: 26, letterSpacing: -0.2 }, // color set per group
  section:     { ...primary, fontFamily: 'Figtree-Bold',      fontSize: 18, lineHeight: 23 },
  body:        { ...primary, fontFamily: 'Figtree-Regular',   fontSize: 16, lineHeight: 24 },
  itemName:    { ...primary, fontFamily: 'Figtree-Medium',    fontSize: 14, lineHeight: 19 },
  columnHead:  { color: '#676879', fontFamily: 'Figtree-SemiBold', fontSize: 13, lineHeight: 16, letterSpacing: 0.2 },
  meta:        { color: '#676879', fontFamily: 'Figtree-Regular', fontSize: 14, lineHeight: 19 },
  statusLabel: { fontFamily: 'Figtree-Bold', fontSize: 11, lineHeight: 12, letterSpacing: 0.2 },
  button:      { fontFamily: 'Figtree-SemiBold', fontSize: 15, lineHeight: 15 },
  textAction:  { color: '#676879', fontFamily: 'Figtree-SemiBold', fontSize: 14, lineHeight: 14 },
  personInit:  { color: '#FFFFFF', fontFamily: 'Figtree-Bold', fontSize: 9, lineHeight: 9, letterSpacing: 0.2 },
  tab:         { fontFamily: 'Figtree-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Board Item Row (stripe + cells)

```tsx
// components/BoardItemRow.tsx
import { Text, View } from 'react-native';
import { colors, onColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function StatusCell({ label, color }: { label: string; color: string }) {
  return (
    <View style={{ minWidth: 88, borderRadius: 4, backgroundColor: color, paddingVertical: 5, paddingHorizontal: 10 }}>
      <Text style={[typography.statusLabel, { color: onColor(color), textAlign: 'center' }]}>{label.toUpperCase()}</Text>
    </View>
  );
}

export function BoardItemRow({
  groupColor, name, personInitials, personColor, status,
}: {
  groupColor: string;
  name: string;
  personInitials: string;
  personColor: string;
  status: { label: string; color: string };
}) {
  return (
    <View style={{ flexDirection: 'row', borderBottomWidth: 1, borderBottomColor: colors.divider }}>
      <View style={{ width: 6, backgroundColor: groupColor }} />
      <View style={{ flex: 1, flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 14, paddingVertical: 12 }}>
        <Text style={[typography.itemName, { flex: 1 }]} numberOfLines={1}>{name}</Text>
        <View style={{ width: 24, height: 24, borderRadius: 12, backgroundColor: personColor, alignItems: 'center', justifyContent: 'center' }}>
          <Text style={typography.personInit}>{personInitials}</Text>
        </View>
        <StatusCell label={status.label} color={status.color} />
      </View>
    </View>
  );
}
```

### Group Header (colored, collapsible)

```tsx
// components/GroupHeader.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function GroupHeader({ name, groupColor, count, expanded, onToggle }: {
  name: string; groupColor: string; count: number; expanded: boolean; onToggle: () => void;
}) {
  const rot = useSharedValue(expanded ? 90 : 0);
  const caret = useAnimatedStyle(() => ({ transform: [{ rotate: `${rot.value}deg` }] }));
  return (
    <Pressable
      onPress={() => { rot.value = withTiming(expanded ? 0 : 90, { duration: 150 }); onToggle(); }}
      style={{ flexDirection: 'row', alignItems: 'center', gap: 8, paddingHorizontal: 18, paddingTop: 12, paddingBottom: 8 }}
    >
      <Animated.View style={caret}>
        <Ionicons name="chevron-forward" size={12} color={groupColor} />
      </Animated.View>
      <Text style={[typography.groupHeader, { color: groupColor }]}>{name}</Text>
      <Text style={[typography.columnHead, { color: colors.textTertiary }]}>{count} items</Text>
    </Pressable>
  );
}
```

### Status Label Picker (sheet grid)

```tsx
// components/StatusPicker.tsx
import { Pressable, Text, View } from 'react-native';
import { onColor } from '../theme/colors';
import { typography } from '../theme/typography';

type Opt = { label: string; color: string };

export function StatusPicker({ options, onPick }: { options: Opt[]; onPick: (o: Opt) => void }) {
  return (
    <View style={{ padding: 20, flexDirection: 'row', flexWrap: 'wrap', gap: 10 }}>
      {options.map((o) => (
        <Pressable
          key={o.label}
          onPress={() => onPick(o)}
          style={{ flexBasis: '47%', flexGrow: 1, borderRadius: 4, backgroundColor: o.color, paddingVertical: 14, alignItems: 'center' }}
        >
          <Text style={[typography.statusLabel, { color: onColor(o.color) }]}>{o.label.toUpperCase()}</Text>
        </Pressable>
      ))}
    </View>
  );
}
```

### Battery Rollup (group health)

```tsx
// components/BatteryRollup.tsx
import { View } from 'react-native';

export function BatteryRollup({ segments }: { segments: { color: string; fraction: number }[] }) {
  return (
    <View style={{ flexDirection: 'row', height: 24, borderRadius: 4, overflow: 'hidden' }}>
      {segments.map((s, i) => (
        <View key={i} style={{ flex: s.fraction, backgroundColor: s.color }} />
      ))}
    </View>
  );
}
```

### Floating Action Button

```tsx
// components/CreateFAB.tsx
import { Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function CreateFAB({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        position: 'absolute', right: 18, bottom: 86,
        width: 56, height: 56, borderRadius: 28,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.bluePressed : colors.blue,
        shadowColor: colors.blue, shadowOpacity: 0.45, shadowRadius: 20, shadowOffset: { width: 0, height: 8 },
        elevation: 8,
        transform: [{ scale: pressed ? 0.94 : 1 }],
      })}
    >
      <Ionicons name="add" size={24} color="#FFFFFF" />
    </Pressable>
  );
}
```

### Kanban Card

```tsx
// components/KanbanCard.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { StatusCell } from './BoardItemRow';

export function KanbanCard({ groupColor, name, personInitials, personColor, status }: {
  groupColor: string; name: string; personInitials: string; personColor: string; status: { label: string; color: string };
}) {
  return (
    <View style={{
      flexDirection: 'row', borderRadius: 8, overflow: 'hidden',
      backgroundColor: colors.canvas, borderWidth: 1, borderColor: colors.cellBorder, marginBottom: 8,
      shadowColor: '#000', shadowOpacity: 0.06, shadowRadius: 4, shadowOffset: { width: 0, height: 1 }, elevation: 1,
    }}>
      <View style={{ width: 6, backgroundColor: groupColor }} />
      <View style={{ flex: 1, padding: 12, gap: 10 }}>
        <Text style={typography.itemName}>{name}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
          <View style={{ width: 24, height: 24, borderRadius: 12, backgroundColor: personColor, alignItems: 'center', justifyContent: 'center' }}>
            <Text style={typography.personInit}>{personInitials}</Text>
          </View>
          <StatusCell label={status.label} color={status.color} />
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
        tabBarActiveTintColor:  colors.blue,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 1, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Figtree-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',   tabBarIcon: ({ color }) => <Ionicons name="home-outline"         size={22} color={color} /> }} />
      <Tabs.Screen name="boards"  options={{ title: 'Boards', tabBarIcon: ({ color }) => <Ionicons name="grid-outline"         size={22} color={color} /> }} />
      <Tabs.Screen name="inbox"   options={{ title: 'Inbox',  tabBarIcon: ({ color }) => <Ionicons name="notifications-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search', tabBarIcon: ({ color }) => <Ionicons name="search"               size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Status change — picker open + cell crossfade + soft haptic
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
// fade old cell color to new with Animated interpolation over 200ms

// Group collapse/expand — caret rotate + layout animation
rot.value = withTiming(expanded ? 0 : 90, { duration: 150 });

// FAB → new item — present a 'modal' route in expo-router

// Drag item / Kanban card — react-native-draggable-flatlist; soft haptic on drag start
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Battery segments — animate flex via Reanimated withTiming(fraction, { duration: 250 })

// View tab underline — Animated translateX 200ms ease-out
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Home | `home-outline` |
| Boards | `grid-outline` |
| Inbox | `notifications-outline` |
| Search | `search` |
| Add / FAB | `add` |
| Group caret | `chevron-forward` (rotate 90°) |
| Person (empty) | `person-circle-outline` |
| Date column | `calendar-outline` |
| Timeline column | `bar-chart-outline` |
| Numbers column | `calculator-outline` |
| More | `ellipsis-horizontal` |
| Back | `chevron-back` |
| Filter | `filter` |
| Sort | `swap-vertical` |
| Updates | `chatbubble-outline` |
| Automation | `flash-outline` |
| Main Table view | `grid-outline` |
| Kanban view | `albums-outline` |
| Timeline view | `git-branch-outline` |
| Chart view | `pie-chart-outline` |
| Attachment | `attach` |

## 7. Platform Notes

- **Font choice**: Figtree is SIL OFL — free to bundle. Ship Regular / Medium / SemiBold / Bold / ExtraBold
- **Status bar**: `<StatusBar style="dark" />` on light mode, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; the FAB sits 86pt from the bottom to clear the tab bar + home indicator
- **Dynamic Type**: React Native respects system scale on `<Text>`; set `allowFontScaling={false}` on status labels, tab labels, person initials, column headers (cell-layout-sensitive)
- **Tabular numerals**: add `fontVariant: ['tabular-nums']` to Numbers / date / percentage text styles
- **On-color text**: always run label colors through `onColor()` — yellow `#FFCB00` and teal `#00D2D2` require dark text or they fail contrast
- **Dark mode**: use `useColorScheme()` to swap chrome to `nightCanvas` / `nightSurface1`, but keep the data palette fully saturated and identical; add 1pt `nightCellBorder` to Kanban cards since shadows vanish
- **Status haptic**: `Haptics.impactAsync(Soft)` on status change, drag start, group toggle
- **Accessibility**: never rely on cell color alone — always render the text label and set `accessibilityLabel` ("Status: Working on it, double tap to change")
- **Drag-to-reorder / change status**: use `react-native-draggable-flatlist`; on drop into a Kanban column, recolor the stripe + status; soft haptic
- **Reduce Motion**: gate the cell crossfade + battery animation behind `AccessibilityInfo.isReduceMotionEnabled` — snap instead
