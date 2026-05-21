# Linear (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Linear's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#08090A',
  surface1: '#141516',
  surface2: '#1C1D1F',
  surface3: '#232428',
  divider:  '#23252A',

  textPrimary:   '#F7F8F8',
  textSecondary: '#8A8F98',
  textTertiary:  '#5C5F6A',

  purple:        '#5E6AD2',
  purplePressed: '#4F58B8',
  purpleTint:    'rgba(94,106,210,0.14)',

  progress: '#F2C94C',
  success:  '#4CB782',
  error:    '#EB5757',
} as const;

export type LinearColor = keyof typeof colors;
```

## 2. Typography

Load Inter via `expo-font`. Identifiers and shortcuts use a monospace family. Fall back to `System`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#F7F8F8' } satisfies TextStyle;

export const typography = {
  titleLarge: { ...base, fontFamily: 'Inter-SemiBold', fontSize: 28, lineHeight: 34, letterSpacing: -0.4 },
  viewTitle:  { ...base, fontFamily: 'Inter-SemiBold', fontSize: 22, lineHeight: 28, letterSpacing: -0.3 },
  section:    { ...base, fontFamily: 'Inter-SemiBold', fontSize: 17, lineHeight: 22, letterSpacing: -0.2 },
  issueTitle: { ...base, fontFamily: 'Inter-Medium',   fontSize: 15, lineHeight: 20, letterSpacing: -0.1 },
  body:       { ...base, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 23 },
  metadata:   {           fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 18, color: '#8A8F98' },
  labelPill:  { ...base, fontFamily: 'Inter-Medium',   fontSize: 12, lineHeight: 12 },
  commandRow: { ...base, fontFamily: 'Inter-Medium',   fontSize: 14, lineHeight: 18, letterSpacing: -0.1 },
  button:     { color: '#FFFFFF', fontFamily: 'Inter-SemiBold', fontSize: 14, lineHeight: 14, letterSpacing: -0.1 },
  tiny:       {           fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 13, letterSpacing: 0.4, textTransform: 'uppercase' as const, color: '#8A8F98' },
  monoId:     {           fontFamily: 'Menlo',          fontSize: 13, lineHeight: 17, color: '#8A8F98', fontVariant: ['tabular-nums'] as const },
  monoShortcut: {         fontFamily: 'Menlo',          fontSize: 12, lineHeight: 14, color: '#8A8F98' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Status Glyph

```tsx
// components/StatusGlyph.tsx
import { View } from 'react-native';
import Svg, { Circle, Path } from 'react-native-svg';
import { colors } from '../theme/colors';

type Status = 'backlog' | 'todo' | 'inProgress' | 'inReview' | 'done' | 'canceled';

export function StatusGlyph({ status, size = 16 }: { status: Status; size?: number }) {
  const r = size / 2 - 1.5;
  if (status === 'done' || status === 'canceled') {
    const fill = status === 'done' ? colors.purple : colors.textTertiary;
    return (
      <Svg width={size} height={size} viewBox={`0 0 ${size} ${size}`}>
        <Circle cx={size / 2} cy={size / 2} r={size / 2} fill={fill} />
        <Path
          d={status === 'done'
            ? `M${size * 0.3} ${size * 0.52} L${size * 0.44} ${size * 0.66} L${size * 0.7} ${size * 0.36}`
            : `M${size * 0.34} ${size * 0.34} L${size * 0.66} ${size * 0.66} M${size * 0.66} ${size * 0.34} L${size * 0.34} ${size * 0.66}`}
          stroke="#fff" strokeWidth={1.8} fill="none" strokeLinecap="round"
        />
      </Svg>
    );
  }
  const stroke = status === 'inProgress' ? colors.progress
    : status === 'inReview' ? colors.purple : colors.textSecondary;
  return (
    <Svg width={size} height={size} viewBox={`0 0 ${size} ${size}`}>
      <Circle cx={size / 2} cy={size / 2} r={r} stroke={stroke} strokeWidth={1.5}
        fill="none" strokeDasharray={status === 'backlog' ? '2 2' : undefined} />
      {status === 'inProgress' && (
        <Path d={`M${size / 2} ${size / 2} L${size / 2} 1.5 A${r} ${r} 0 0 1 ${size / 2} ${size - 1.5} Z`} fill={colors.progress} />
      )}
    </Svg>
  );
}
```

### Issue Row (the signature element)

```tsx
// components/IssueRow.tsx
import { Pressable, Text, View, StyleSheet } from 'react-native';
import { StatusGlyph } from './StatusGlyph';
import { PriorityBars } from './PriorityBars';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function IssueRow({
  identifier, title, status, priority, assignee, selected, onPress,
}: {
  identifier: string; title: string; status: any; priority: number;
  assignee: string; selected?: boolean; onPress: () => void;
}) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => [
        styles.row,
        selected && { backgroundColor: colors.purpleTint },
        pressed && !selected && { backgroundColor: colors.surface2 },
      ]}
    >
      {selected && <View style={styles.accent} />}
      <StatusGlyph status={status} />
      <Text style={typography.monoId}>{identifier}</Text>
      <Text style={[typography.issueTitle, styles.title]} numberOfLines={1}>{title}</Text>
      <PriorityBars level={priority} />
      <View style={styles.avatar}>
        <Text style={styles.avatarText}>{assignee.slice(0, 1)}</Text>
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row:    { height: 44, flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, gap: 10 },
  accent: { position: 'absolute', left: 0, top: 0, bottom: 0, width: 2, backgroundColor: colors.purple },
  title:  { flex: 1 },
  avatar: { width: 20, height: 20, borderRadius: 10, backgroundColor: colors.surface3, alignItems: 'center', justifyContent: 'center' },
  avatarText: { fontFamily: 'Inter-SemiBold', fontSize: 10, color: colors.textSecondary },
});
```

### Priority Bars

```tsx
// components/PriorityBars.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';

export function PriorityBars({ level }: { level: number }) {
  if (level === 4) {
    return (
      <View style={{ width: 14, height: 14, borderRadius: 2, backgroundColor: colors.progress, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontSize: 10, fontWeight: '700', color: '#000' }}>!</Text>
      </View>
    );
  }
  return (
    <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 2, width: 14, height: 14 }}>
      {[0, 1, 2].map((i) => (
        <View key={i} style={{
          width: 3, height: 6 + i * 4, borderRadius: 1,
          backgroundColor: i < level ? colors.textSecondary : 'rgba(92,95,106,0.4)',
        }} />
      ))}
    </View>
  );
}
```

### Primary Button

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={({ pressed }) => ({
        height: 32, paddingHorizontal: 14, borderRadius: 8,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.purplePressed : colors.purple,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Command Menu (Cmd+K)

```tsx
// components/CommandMenu.tsx
import { useState } from 'react';
import { TextInput, View, Text, FlatList, StyleSheet } from 'react-native';
import Animated, { FadeIn } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CommandMenu({ rows }: { rows: { icon: any; label: string; shortcut: string }[] }) {
  const [query, setQuery] = useState('');
  const [focused] = useState(0);
  return (
    <Animated.View entering={FadeIn.duration(120)} style={styles.sheet}>
      <View style={styles.search}>
        <Ionicons name="search" size={16} color={colors.textSecondary} />
        <TextInput
          value={query}
          onChangeText={setQuery}
          placeholder="Type a command or search…"
          placeholderTextColor={colors.textTertiary}
          style={[typography.commandRow, { flex: 1 }]}
          selectionColor={colors.purple}
        />
      </View>
      <View style={styles.divider} />
      <FlatList
        data={rows}
        keyExtractor={(_, i) => String(i)}
        renderItem={({ item, index }) => (
          <View style={[styles.cmdRow, index === focused && { backgroundColor: colors.purpleTint }]}>
            <Ionicons name={item.icon} size={15} color={colors.textSecondary} style={{ width: 18 }} />
            <Text style={[typography.commandRow, { flex: 1 }]}>{item.label}</Text>
            <Text style={typography.monoShortcut}>{item.shortcut}</Text>
          </View>
        )}
      />
    </Animated.View>
  );
}

const styles = StyleSheet.create({
  sheet:   { maxWidth: 560, width: '100%', backgroundColor: colors.surface1, borderRadius: 12, borderWidth: 1, borderColor: colors.divider, overflow: 'hidden' },
  search:  { height: 44, flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 16 },
  divider: { height: 1, backgroundColor: colors.divider },
  cmdRow:  { height: 40, flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 16 },
});
```

### Cycle Progress Bar

```tsx
// components/CycleProgressBar.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CycleProgressBar({ completed, total, daysLeft }: { completed: number; total: number; daysLeft: number }) {
  return (
    <View style={{ gap: 6 }}>
      <View style={{ height: 6, borderRadius: 3, backgroundColor: colors.surface2, overflow: 'hidden' }}>
        <View style={{ height: '100%', borderRadius: 3, backgroundColor: colors.purple, width: `${(completed / Math.max(total, 1)) * 100}%` }} />
      </View>
      <Text style={typography.metadata}>{completed} of {total} completed · {daysLeft} days left</Text>
    </View>
  );
}
```

## 4. Sidebar Navigation (no bottom tab bar)

Linear has no tab bar. Use a `Drawer` (`expo-router` drawer) instead of `Tabs`.

```tsx
// app/_layout.tsx
import { Drawer } from 'expo-router/drawer';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export default function Layout() {
  return (
    <Drawer
      screenOptions={{
        headerStyle: { backgroundColor: colors.canvas },
        headerTintColor: colors.textPrimary,
        drawerStyle: { backgroundColor: colors.canvas, width: 280 },
        drawerActiveBackgroundColor: colors.purpleTint,
        drawerActiveTintColor: colors.textPrimary,
        drawerInactiveTintColor: colors.textSecondary,
        drawerLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 14, marginLeft: -16 },
        sceneContainerStyle: { backgroundColor: colors.canvas },
      }}
    >
      <Drawer.Screen name="inbox"    options={{ title: 'Inbox',     drawerIcon: ({ color }) => <Ionicons name="file-tray" size={16} color={color} /> }} />
      <Drawer.Screen name="my"       options={{ title: 'My Issues', drawerIcon: ({ color }) => <Ionicons name="ellipse-outline" size={16} color={color} /> }} />
      <Drawer.Screen name="team"     options={{ title: 'Engineering', drawerIcon: ({ color }) => <Ionicons name="people" size={16} color={color} /> }} />
    </Drawer>
  );
}
```

## 5. Motion

```tsx
// Command menu open: opacity + scale over 120ms
import Animated, { FadeIn, ZoomIn } from 'react-native-reanimated';
<Animated.View entering={FadeIn.duration(120)} /* + ZoomIn.from(0.96) */ />

// Row selection: color cross-fade only (handled by Pressable style — no transform)

// Group collapse: LayoutAnimation or reanimated layout
import { LinearTransition } from 'react-native-reanimated';
<Animated.View layout={LinearTransition.springify().damping(18)} />

// Haptic on commit / status change / command execute
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
```

## 6. Icon Library

Use `@expo/vector-icons`. Map to Linear's iconography (status glyphs are custom SVG — see §3):

| Purpose | Ionicons |
|---------|----------|
| Todo status | `ellipse-outline` |
| Done status | `checkmark-circle` |
| Canceled | `close-circle` |
| Command search | `search` |
| New issue | `create-outline` |
| Filter | `funnel-outline` |
| Display options | `options-outline` |
| Sidebar / menu | `menu` |
| Inbox | `file-tray` |
| My Issues | `ellipse-outline` |
| Team | `people` |
| Cycle | `sync` |
| Project | `cube-outline` |
| More | `ellipsis-horizontal` |

## 7. Platform Notes

- **No tab bar**: Linear's IA is a drawer + list, not bottom tabs — do not add `Tabs`
- **Status bar**: `<StatusBar style="light" />` from `expo-status-bar` — the near-black canvas requires light content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the list and composer must clear the home indicator
- **Hardware keyboard**: on iPad, wire `Cmd+K` via a `KeyboardEvents` listener (or `react-native-key-command`) to open the command menu; arrow keys move focus, Return executes
- **Tabular numerics**: set `fontVariant: ['tabular-nums']` on identifiers and counts so columns never jitter
- **Dynamic Type**: respect font scaling on titles/body; set `allowFontScaling={false}` on status/priority glyph labels and mono shortcuts where layout is fixed
- **Accessibility**: group issue-row text with `accessibilityRole="button"` + a combined `accessibilityLabel`; mark the trailing avatar/priority as decorative
