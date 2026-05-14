# Todoist (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Todoist's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets for the signature task-completion animation.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces
  canvas:        '#FFFFFF',
  surfaceGray:   '#FAFAFA',
  surfaceGray2:  '#F0F0F0',
  divider:       '#EEEEEE',

  // Text
  ink:           '#202020',  // primary
  secondary:     '#808080',  // dates, project tags
  tertiary:      '#B0B0B0',  // placeholder, completed strike

  // Brand
  red:           '#DC4C3E',  // Todoist Red — FAB, P1, brand
  redPressed:    '#B53C30',
  redTint:       '#FBE5E2',

  // Priorities
  p1:            '#DC4C3E',
  p2:            '#EB8909',
  p3:            '#246FE0',
  p4:            '#B0B0B0',

  // Semantic
  success:       '#058527',
  error:         '#D1453B',

  // Dark mode
  darkCanvas:    '#1F1F1F',
  darkSurface:   '#282828',
  darkSurface2:  '#363636',
  darkDivider:   '#3D3D3D',
  darkText:      '#E8E8E8',
  darkTextSec:   '#A0A0A0',
  redDark:       '#E44332',
} as const;

// Project color palette (20 swatches — match Todoist's user-selectable set)
export const projectColors = {
  berryRed: '#B8255F',
  red:      '#DB4035',
  orange:   '#FF9933',
  yellow:   '#FAD000',
  olive:    '#AFB83B',
  lime:     '#7ECC49',
  green:    '#299438',
  mint:     '#6ACCBC',
  teal:     '#158FAD',
  sky:      '#14AAF5',
  lightBlue:'#96C3EB',
  blue:     '#4073FF',
  grape:    '#884DFF',
  violet:   '#AF38EB',
  lavender: '#EB96EB',
  magenta:  '#E05194',
  salmon:   '#FF8581',
  charcoal: '#808080',
  grey:     '#B8B8B8',
  taupe:    '#CCAC93',
} as const;
```

## 2. Typography

Todoist uses the system font. On iOS this resolves to SF Pro; on Android it resolves to Roboto, which is acceptable for cross-platform parity. No custom face is needed.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const ink       = { color: '#202020' } satisfies TextStyle;
const secondary = { color: '#808080' } satisfies TextStyle;

export const typography = {
  largeNav:     { ...ink, fontSize: 34, fontWeight: '700', letterSpacing: -0.4, lineHeight: 39 },
  inlineNav:    { ...ink, fontSize: 17, fontWeight: '600', letterSpacing: -0.2, lineHeight: 20 },

  sectionHdr:   { ...secondary, fontSize: 13, fontWeight: '600', letterSpacing: 0.6, textTransform: 'uppercase' as const, lineHeight: 16 },

  taskBody:     { ...ink, fontSize: 16, fontWeight: '400', letterSpacing: -0.1, lineHeight: 22 },
  taskBold:     { ...ink, fontSize: 16, fontWeight: '600', letterSpacing: -0.1, lineHeight: 22 },
  subtask:      { ...ink, fontSize: 15, fontWeight: '400', letterSpacing: -0.1, lineHeight: 20 },

  meta:         { ...secondary, fontSize: 13, fontWeight: '400', lineHeight: 16 },
  metaOverdue:  { color: '#DC4C3E', fontSize: 13, fontWeight: '500', lineHeight: 16 },
  metaToday:    { color: '#058527', fontSize: 13, fontWeight: '400', lineHeight: 16 },

  sidebarSys:   { ...ink, fontSize: 15, fontWeight: '500', lineHeight: 19 },
  sidebarProj:  { ...ink, fontSize: 15, fontWeight: '400', lineHeight: 19 },

  quickAdd:     { ...ink, fontSize: 17, fontWeight: '400', lineHeight: 22 },
  button:       { color: '#FFFFFF', fontSize: 16, fontWeight: '600', lineHeight: 20 },
  fabGlyph:     { color: '#FFFFFF', fontSize: 28, fontWeight: '300', lineHeight: 32 },

  tab:          { fontSize: 10, fontWeight: '500', letterSpacing: 0.1 },
  caption:      { ...secondary, fontSize: 12, fontWeight: '400', lineHeight: 16 },
  karmaNum:     { ...ink, fontSize: 28, fontWeight: '700', letterSpacing: -0.2, lineHeight: 32 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Priority Checkbox

```tsx
// components/Checkbox.tsx
import { Pressable, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';

export type Priority = 1 | 2 | 3 | 4;

const priorityColor: Record<Priority, string> = {
  1: colors.p1,
  2: colors.p2,
  3: colors.p3,
  4: colors.p4,
};

const priorityFill: Record<Priority, string> = {
  1: 'rgba(220,76,62,0.10)',
  2: 'rgba(235,137,9,0.08)',
  3: 'rgba(36,111,224,0.08)',
  4: 'transparent',
};

export function Checkbox({ priority, isComplete, onToggle }: {
  priority: Priority;
  isComplete: boolean;
  onToggle: () => void;
}) {
  const fill = useSharedValue(isComplete ? 1 : 0);
  const aStyle = useAnimatedStyle(() => ({
    opacity: fill.value,
  }));

  const handlePress = () => {
    fill.value = withTiming(isComplete ? 0 : 1, { duration: 200 });
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    onToggle();
  };

  return (
    <Pressable onPress={handlePress} hitSlop={11} style={{ width: 44, height: 44, alignItems: 'center', justifyContent: 'center' }}>
      <View
        style={[
          styles.box,
          {
            borderColor: priorityColor[priority],
            backgroundColor: isComplete ? priorityColor[priority] : priorityFill[priority],
          },
        ]}
      >
        <Animated.View style={aStyle}>
          {isComplete && <Ionicons name="checkmark" size={14} color="#FFFFFF" />}
        </Animated.View>
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  box: {
    width: 22, height: 22, borderRadius: 11,
    borderWidth: 1.5,
    alignItems: 'center', justifyContent: 'center',
  },
});
```

### Task Row (Hero Component)

```tsx
// components/TaskRow.tsx
import { View, Text, StyleSheet } from 'react-native';
import { useState } from 'react';
import { Checkbox, Priority } from './Checkbox';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type TaskDate =
  | { kind: 'today' }
  | { kind: 'tomorrow' }
  | { kind: 'overdue'; label: string }
  | { kind: 'future';  label: string };

type Props = {
  content: string;
  priority: Priority;
  date?: TaskDate;
  project?: { name: string; color: string };
  commentCount?: number;
};

export function TaskRow({ content, priority, date, project, commentCount = 0 }: Props) {
  const [isComplete, setIsComplete] = useState(false);

  return (
    <View style={styles.row}>
      <Checkbox priority={priority} isComplete={isComplete} onToggle={() => setIsComplete((c) => !c)} />

      <View style={{ flex: 1, marginLeft: 12 }}>
        <Text
          style={[
            typography.taskBody,
            isComplete && { color: colors.tertiary, textDecorationLine: 'line-through', opacity: 0.6 },
          ]}
          numberOfLines={3}
        >
          {content}
        </Text>

        {(date || project || commentCount > 0) && (
          <View style={styles.meta}>
            {date && <DatePill date={date} />}
            {project && (
              <View style={styles.projTag}>
                <View style={[styles.dot, { backgroundColor: project.color }]} />
                <Text style={typography.meta}># {project.name}</Text>
              </View>
            )}
            {commentCount > 0 && (
              <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
                <Ionicons name="chatbubble-outline" size={12} color={colors.secondary} />
                <Text style={typography.meta}>{commentCount}</Text>
              </View>
            )}
          </View>
        )}
      </View>
    </View>
  );
}

function DatePill({ date }: { date: TaskDate }) {
  switch (date.kind) {
    case 'today':    return <Text style={typography.metaToday}>Today</Text>;
    case 'tomorrow': return <Text style={typography.meta}>Tomorrow</Text>;
    case 'overdue':  return <Text style={typography.metaOverdue}>{date.label}</Text>;
    case 'future':   return <Text style={typography.meta}>{date.label}</Text>;
  }
}

const styles = StyleSheet.create({
  row: {
    flexDirection: 'row',
    alignItems: 'flex-start',
    paddingHorizontal: 16,
    paddingVertical: 12,
    minHeight: 52,
    backgroundColor: colors.canvas,
    borderBottomWidth: 0.5,
    borderBottomColor: colors.divider,
  },
  meta:    { flexDirection: 'row', alignItems: 'center', gap: 12, marginTop: 4 },
  projTag: { flexDirection: 'row', alignItems: 'center', gap: 6 },
  dot:     { width: 8, height: 8, borderRadius: 4 },
});
```

### Floating Action Button (Tinted Red Shadow — Signature)

```tsx
// components/FAB.tsx
import { Pressable, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function FAB({ onPress }: { onPress: () => void }) {
  const scale = useSharedValue(1);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Animated.View
      style={[
        {
          position: 'absolute', bottom: 24, right: 24,
          width: 56, height: 56, borderRadius: 28,
          shadowColor: colors.red,        // <-- TINTED RED SHADOW (signature)
          shadowOpacity: 0.35,
          shadowRadius: 16,
          shadowOffset: { width: 0, height: 6 },
          elevation: 8,
        },
        aStyle,
      ]}
    >
      <Pressable
        onPressIn={() => { scale.value = withSpring(0.94, { damping: 12 }); }}
        onPressOut={() => { scale.value = withSpring(1, { damping: 12 }); }}
        onPress={() => {
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
          onPress();
        }}
        style={({ pressed }) => ({
          width: 56, height: 56, borderRadius: 28,
          backgroundColor: pressed ? colors.redPressed : colors.red,
          alignItems: 'center', justifyContent: 'center',
        })}
      >
        <Ionicons name="add" size={28} color="#FFFFFF" />
      </Pressable>
    </Animated.View>
  );
}
```

### Quick-Add Card

```tsx
// components/QuickAddCard.tsx
import { View, Text, TextInput, Pressable, ScrollView } from 'react-native';
import { useState } from 'react';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function QuickAddCard({ onAdd }: { onAdd: (text: string) => void }) {
  const [text, setText] = useState('');
  const [date, setDate] = useState<string | null>('Today');
  const [priority, setPriority] = useState<1 | 2 | 3 | 4>(4);

  return (
    <View style={{ padding: 16, gap: 16, backgroundColor: colors.canvas, borderTopLeftRadius: 16, borderTopRightRadius: 16 }}>
      <Text style={typography.inlineNav}>Add task</Text>

      <TextInput
        value={text}
        onChangeText={setText}
        placeholder="Take out the trash today p1 #home"
        placeholderTextColor={colors.tertiary}
        style={typography.quickAdd}
        multiline
      />

      {text.length > 0 && (
        <View style={{ flexDirection: 'row', gap: 8, flexWrap: 'wrap' }}>
          {date && <SmartChip label={date} color={colors.success} />}
          {priority !== 4 && <SmartChip label={`P${priority}`} color={priority === 1 ? colors.p1 : priority === 2 ? colors.p2 : colors.p3} />}
        </View>
      )}

      <View style={{ height: 0.5, backgroundColor: colors.divider }} />

      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ gap: 8 }}>
        <ActionChip icon="calendar" label={date ?? 'Date'} />
        <ActionChip icon="flag-outline" label="Priority" />
        <ActionChip icon="folder-outline" label="Project" />
        <ActionChip icon="pricetag-outline" label="Labels" />
        <ActionChip icon="notifications-outline" label="Reminders" />
      </ScrollView>

      <View style={{ flexDirection: 'row', justifyContent: 'flex-end' }}>
        <Pressable
          onPress={() => {
            Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
            onAdd(text);
            setText('');
          }}
          disabled={text.length === 0}
          style={({ pressed }) => ({
            backgroundColor: text.length === 0 ? colors.surfaceGray2 : (pressed ? colors.redPressed : colors.red),
            paddingVertical: 12, paddingHorizontal: 24, borderRadius: 8,
          })}
        >
          <Text style={[typography.button, text.length === 0 && { color: colors.tertiary }]}>Add task</Text>
        </Pressable>
      </View>
    </View>
  );
}

function SmartChip({ label, color }: { label: string; color: string }) {
  return (
    <View style={{ backgroundColor: color + '1F', paddingVertical: 4, paddingHorizontal: 8, borderRadius: 6 }}>
      <Text style={{ fontSize: 13, fontWeight: '500', color }}>{label}</Text>
    </View>
  );
}

function ActionChip({ icon, label }: { icon: keyof typeof Ionicons.glyphMap; label: string }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 6,
      paddingVertical: 8, paddingHorizontal: 12, borderRadius: 500,
      borderWidth: 1, borderColor: colors.divider,
    }}>
      <Ionicons name={icon} size={12} color={colors.ink} />
      <Text style={{ fontSize: 13, fontWeight: '500', color: colors.ink }}>{label}</Text>
    </View>
  );
}
```

### Sidebar Row

```tsx
// components/SidebarRow.tsx
import { Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SidebarRow({ icon, label, count, isSelected, isOverdue, onPress }: {
  icon: keyof typeof Ionicons.glyphMap;
  label: string;
  count?: number;
  isSelected?: boolean;
  isOverdue?: boolean;
  onPress: () => void;
}) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 12,
        height: 44, paddingHorizontal: 16,
        backgroundColor: isSelected ? colors.red + '14' : 'transparent',
        borderLeftWidth: isSelected ? 3 : 0,
        borderLeftColor: colors.red,
      }}
    >
      <Ionicons name={icon} size={20} color={colors.ink} />
      <Text style={typography.sidebarSys}>{label}</Text>
      <View style={{ flex: 1 }} />
      {count !== undefined && count > 0 && (
        <View style={{
          backgroundColor: isOverdue ? colors.red : 'transparent',
          paddingVertical: 2, paddingHorizontal: 8, borderRadius: 12,
        }}>
          <Text style={{ ...typography.meta, color: isOverdue ? '#FFFFFF' : colors.secondary }}>
            {count}
          </Text>
        </View>
      )}
    </Pressable>
  );
}
```

### Section Header

```tsx
// components/SectionHeader.tsx
import { View, Text, Pressable } from 'react-native';
import { useState } from 'react';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SectionHeader({ title, isOverdue }: { title: string; isOverdue?: boolean }) {
  const [collapsed, setCollapsed] = useState(false);
  const tint = isOverdue ? colors.red : colors.secondary;
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
      height: 36, paddingHorizontal: 16,
      backgroundColor: colors.canvas,
      borderBottomWidth: 0.5, borderBottomColor: colors.divider,
    }}>
      <Text style={[typography.sectionHdr, { color: tint }]}>{title.toUpperCase()}</Text>
      <Pressable onPress={() => setCollapsed((c) => !c)} hitSlop={8}>
        <Ionicons name={collapsed ? 'chevron-forward' : 'chevron-down'} size={12} color={tint} />
      </Pressable>
    </View>
  );
}
```

## 4. Tab Bar (iPad) — expo-router

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.red,
        tabBarInactiveTintColor: colors.secondary,
        tabBarLabelStyle: { fontSize: 10, fontWeight: '500', letterSpacing: 0.1 },
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
      }}
    >
      <Tabs.Screen name="today"    options={{ title: 'Today',    tabBarIcon: ({ color }) => <Ionicons name="calendar"       size={22} color={color} /> }} />
      <Tabs.Screen name="upcoming" options={{ title: 'Upcoming', tabBarIcon: ({ color }) => <Ionicons name="calendar-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="search"   options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search"           size={22} color={color} /> }} />
      <Tabs.Screen name="browse"   options={{ title: 'Browse',   tabBarIcon: ({ color }) => <Ionicons name="grid"             size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// Task complete
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// FAB tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Quick-add commit
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Smart-chip appear
Haptics.selectionAsync();

// Task complete collapse animation
import { Layout, FadeOut, runOnJS } from 'react-native-reanimated';
<Animated.View
  layout={Layout.springify().damping(15)}
  exiting={FadeOut.duration(250)}
>
  <TaskRow ... />
</Animated.View>

// FAB press
scale.value = withSpring(0.94, { damping: 12 });
```

## 6. Swipe Actions (react-native-gesture-handler)

```tsx
import Swipeable from 'react-native-gesture-handler/Swipeable';
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';

function ScheduleAction() {
  return (
    <View style={{ backgroundColor: colors.success, justifyContent: 'center', paddingHorizontal: 24 }}>
      <Text style={{ color: '#FFFFFF', fontWeight: '600' }}>Schedule</Text>
    </View>
  );
}

function PostponeAction() {
  return (
    <View style={{ backgroundColor: colors.p2, justifyContent: 'center', paddingHorizontal: 24 }}>
      <Text style={{ color: '#FFFFFF', fontWeight: '600' }}>Postpone</Text>
    </View>
  );
}

<Swipeable
  renderLeftActions={ScheduleAction}
  renderRightActions={PostponeAction}
>
  <TaskRow ... />
</Swipeable>
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Todoist's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Checkbox empty | `ellipse-outline` |
| Checkbox complete | `checkmark` (over filled circle) |
| Priority flag (filled) | `flag` |
| Priority flag (outline) | `flag-outline` |
| FAB plus | `add` |
| Today | `calendar` |
| Inbox | `mail-outline` |
| Upcoming | `calendar-outline` |
| Filters & Labels | `search` |
| Project (sidebar) | colored dot only |
| Comment | `chatbubble-outline` |
| Sort / Filter | `options-outline` |
| Menu drawer | `menu` |
| Karma flame | `flame` |
| Schedule (swipe) | `calendar` |
| Reminders | `notifications-outline` |
| Labels | `pricetag-outline` |
| Project | `folder-outline` |
| Settings | `settings-outline` |

## 8. Platform Notes

- **Tinted FAB shadow on Android**: React Native shadows on Android only respect `elevation`, not `shadowColor`. To get the tinted red shadow look on Android, wrap the FAB in a `View` with a semi-transparent red background underneath, slightly offset and blurred via `BlurView` — or accept the platform default (gray shadow at `elevation: 8`).
- **Status bar**: Set `<StatusBar style="dark" />` from `expo-status-bar` on light canvas; `style="light"` on dark mode.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The FAB must sit 24pt above the safe area bottom.
- **Dynamic Type**: React Native respects user font-scaling by default. Set `allowFontScaling={false}` only on section headers, tab labels, and the FAB glyph where layout breaks.
- **Quick-add modal**: use `react-native-bottom-sheet` or `presentationStyle="formSheet"` for the slide-up modal. Detents: `'medium'` and `'large'`.
- **Sidebar on iPhone**: implement as a `Drawer` from `expo-router` with `drawerType: 'front'` and a custom drawer content component using `SidebarRow`.
- **Dark mode**: use `useColorScheme()` to switch the token object between palettes; Todoist Red brightens to `#E44332` in dark mode.
- **Accessibility**: Set `accessibilityRole="checkbox"` and `accessibilityState={{ checked: isComplete }}` on the Checkbox; the task row's `accessibilityLabel` should combine priority + content + date + project: `"P1, Buy milk, today, Groceries"`.
