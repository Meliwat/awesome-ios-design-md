# Obsidian (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Obsidian's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#1E1E1E',
  surface1: '#262626',
  surface2: '#2D2D2D',
  surface3: '#363636',
  divider:  '#363636',

  textPrimary:   '#DCDDDE',
  textSecondary: '#999999',
  textTertiary:  '#6B6B6B',

  purple:        '#7C3AED',
  purpleLink:    '#A78BFA',
  purplePressed: '#6D28D9',
  purpleTint:    'rgba(124,58,237,0.14)',

  externalLink: '#7C9CBF',
  success:      '#4ADE80',
  error:        '#F87171',
  highlight:    'rgba(255,213,79,0.25)',
} as const;

export type ObsColor = keyof typeof colors;
```

## 2. Typography

Obsidian uses a dual system: Inter for chrome/reading, JetBrains Mono for source/commands. Load both via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
    'JBMono-Regular':  require('../assets/fonts/JetBrainsMono-Regular.ttf'),
    'JBMono-SemiBold': require('../assets/fonts/JetBrainsMono-SemiBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#DCDDDE' } satisfies TextStyle;

export const typography = {
  noteTitle:  { ...base, fontFamily: 'Inter-Bold',     fontSize: 26, lineHeight: 33, letterSpacing: -0.3 },
  h1:         { ...base, fontFamily: 'Inter-Bold',     fontSize: 24, lineHeight: 31, letterSpacing: -0.2 },
  h2:         { ...base, fontFamily: 'Inter-Bold',     fontSize: 20, lineHeight: 26, letterSpacing: -0.2 },
  h3:         { ...base, fontFamily: 'Inter-SemiBold', fontSize: 17, lineHeight: 22, letterSpacing: -0.1 },
  reading:    { ...base, fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 26 },
  fileRow:    { ...base, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 20, letterSpacing: -0.1 },
  paneTitle:  {           fontFamily: 'Inter-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.6, textTransform: 'uppercase' as const, color: '#999999' },
  backlink:   {           fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 19, color: '#999999' },
  metadata:   {           fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 17, color: '#999999' },
  tagPill:    {           fontFamily: 'Inter-SemiBold', fontSize: 13, lineHeight: 13, color: '#A78BFA' },
  button:     { color: '#FFFFFF', fontFamily: 'Inter-SemiBold', fontSize: 15, lineHeight: 15, letterSpacing: -0.1 },
  nodeLabel:  {           fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 13, color: '#999999' },
  source:     { ...base, fontFamily: 'JBMono-Regular',  fontSize: 15, lineHeight: 23 },
  commandRow: { ...base, fontFamily: 'JBMono-Regular',  fontSize: 14, lineHeight: 18 },
  commandShort:{          fontFamily: 'JBMono-SemiBold', fontSize: 12, lineHeight: 12, color: '#999999' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### File-Tree Row (the navigation unit)

```tsx
// components/FileTreeRow.tsx
import { Pressable, Text, View, StyleSheet } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FileTreeRow({
  name, depth, isFolder, expanded, onPress, isActive, isModified,
}: {
  name: string; depth: number; isFolder?: boolean; expanded?: boolean;
  onPress: () => void; isActive?: boolean; isModified?: boolean;
}) {
  const rot = useSharedValue(expanded ? 1 : 0);
  const chev = useAnimatedStyle(() => ({ transform: [{ rotate: `${rot.value * 90}deg` }] }));
  return (
    <Pressable
      onPress={() => { if (isFolder) rot.value = withTiming(expanded ? 0 : 1, { duration: 180 }); onPress(); }}
      style={({ pressed }) => [
        styles.row,
        isActive && { backgroundColor: colors.purpleTint },
        pressed && !isActive && { backgroundColor: colors.surface2 },
      ]}
    >
      {isActive && <View style={styles.activeBar} />}
      <View style={{ width: depth * 16 }} />
      {isFolder ? (
        <Animated.View style={chev}><Ionicons name="chevron-forward" size={10} color={colors.textSecondary} /></Animated.View>
      ) : <View style={{ width: 10 }} />}
      <Ionicons name={isFolder ? 'folder' : 'document-text-outline'} size={14}
        color={isActive ? colors.purpleLink : colors.textSecondary} />
      <Text style={[typography.fileRow, { flex: 1 }]} numberOfLines={1}>{name}</Text>
      {isModified && <View style={styles.modDot} />}
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row:       { height: 32, flexDirection: 'row', alignItems: 'center', gap: 6, paddingHorizontal: 12 },
  activeBar: { position: 'absolute', left: 0, top: 0, bottom: 0, width: 2, backgroundColor: colors.purple },
  modDot:    { width: 6, height: 6, borderRadius: 3, backgroundColor: colors.purpleLink },
});
```

### Markdown Source Line (dimmed syntax)

```tsx
// components/SourceLine.tsx
import { Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Tok = { kind: 'syntax' | 'wikilink' | 'external' | 'code' | 'text'; text: string };

const tint = (k: Tok['kind']) =>
  k === 'syntax' ? colors.textTertiary
  : k === 'wikilink' ? colors.purpleLink
  : k === 'external' ? colors.externalLink
  : colors.textPrimary;

export function SourceLine({ tokens }: { tokens: Tok[] }) {
  return (
    <Text style={typography.source}>
      {tokens.map((t, i) => (
        <Text key={i} style={{ color: tint(t.kind), textDecorationLine: t.kind === 'wikilink' ? 'underline' : 'none' }}>
          {t.text}
        </Text>
      ))}
    </Text>
  );
}
```

### Tag Pill (a connection, not metadata)

```tsx
// components/TagPill.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TagPill({ tag, onPress }: { tag: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        paddingHorizontal: 9, paddingVertical: 3, borderRadius: 500,
        backgroundColor: pressed ? 'rgba(124,58,237,0.24)' : colors.purpleTint,
      })}
    >
      <Text style={typography.tagPill}>#{tag}</Text>
    </Pressable>
  );
}
```

### Graph View (physics-driven nodes)

```tsx
// components/GraphView.tsx — Skia canvas + a simple force tick
import { Canvas, Circle, Line, vec } from '@shopify/react-native-skia';
import { useEffect, useState } from 'react';
import { colors } from '../theme/colors';

type Node = { id: string; x: number; y: number; r: number; active?: boolean };
type Edge = { a: string; b: string };

export function GraphView({ initial, edges, width, height }: { initial: Node[]; edges: Edge[]; width: number; height: number }) {
  const [nodes, setNodes] = useState(initial);

  useEffect(() => {
    // Fixed-timestep force sim: edge springs + node repulsion + light centering.
    const id = setInterval(() => {
      setNodes((ns) => ns.map((n) => ({ ...n /* integrate velocity here */ })));
    }, 1000 / 60);
    return () => clearInterval(id);
  }, []);

  const at = (id: string) => nodes.find((n) => n.id === id)!;
  return (
    <Canvas style={{ width, height, backgroundColor: colors.canvas }}>
      {edges.map((e, i) => {
        const a = at(e.a), b = at(e.b);
        const bright = a.active || b.active;
        return <Line key={i} p1={vec(a.x, a.y)} p2={vec(b.x, b.y)}
          color={bright ? colors.purple : colors.divider} strokeWidth={1} />;
      })}
      {nodes.map((n) => (
        <Circle key={n.id} cx={n.x} cy={n.y} r={n.r}
          color={n.active ? colors.purpleLink : colors.purple} />
      ))}
    </Canvas>
  );
}
```

### Command Palette

```tsx
// components/CommandPalette.tsx
import { useState } from 'react';
import { TextInput, View, Text, FlatList, StyleSheet } from 'react-native';
import Animated, { FadeIn } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CommandPalette({ rows }: { rows: { label: string; shortcut: string }[] }) {
  const [query, setQuery] = useState('');
  const focused = 0;
  return (
    <Animated.View entering={FadeIn.duration(130)} style={styles.sheet}>
      <View style={styles.input}>
        <Text style={[typography.commandRow, { color: colors.textTertiary }]}>{'>'}</Text>
        <TextInput
          value={query} onChangeText={setQuery}
          placeholder="Type a command…" placeholderTextColor={colors.textTertiary}
          style={[typography.commandRow, { flex: 1 }]} selectionColor={colors.purpleLink}
        />
      </View>
      <View style={styles.divider} />
      <FlatList
        data={rows}
        keyExtractor={(_, i) => String(i)}
        renderItem={({ item, index }) => (
          <View style={[styles.row, index === focused && { backgroundColor: colors.purpleTint }]}>
            <Text style={[typography.commandRow, { flex: 1 }]}>{item.label}</Text>
            <Text style={typography.commandShort}>{item.shortcut}</Text>
          </View>
        )}
      />
    </Animated.View>
  );
}

const styles = StyleSheet.create({
  sheet:   { maxWidth: 560, width: '100%', backgroundColor: colors.surface1, borderRadius: 10, borderWidth: 1, borderColor: colors.divider, overflow: 'hidden' },
  input:   { height: 44, flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 16 },
  divider: { height: 1, backgroundColor: colors.divider },
  row:     { height: 38, flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16 },
});
```

### Backlinks Pane

```tsx
// components/BacklinksPane.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BacklinksPane({ backlinks }: { backlinks: { title: string; context: string }[] }) {
  return (
    <View style={{ backgroundColor: colors.surface1 }}>
      <View style={{ flexDirection: 'row', gap: 8, paddingHorizontal: 16, paddingVertical: 12 }}>
        <Text style={typography.paneTitle}>Linked Mentions</Text>
        <Text style={typography.metadata}>{backlinks.length}</Text>
      </View>
      {backlinks.map((b, i) => (
        <View key={i} style={{ paddingHorizontal: 16, paddingVertical: 10, gap: 4 }}>
          <Text style={[typography.button, { color: colors.textPrimary }]}>{b.title}</Text>
          <Text style={typography.backlink} numberOfLines={2}>{b.context}</Text>
        </View>
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
        height: 34, paddingHorizontal: 16, borderRadius: 6, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.purplePressed : colors.purple,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

## 4. Navigation — Ribbon + File-Tree Drawer (no bottom tab bar)

Obsidian has no tab bar. Use a `Drawer` for the file tree; render the ribbon as a fixed left strip.

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
        drawerStyle: { backgroundColor: colors.surface1, width: 280 },
        drawerActiveBackgroundColor: colors.purpleTint,
        drawerActiveTintColor: colors.textPrimary,
        drawerInactiveTintColor: colors.textSecondary,
        drawerLabelStyle: { fontFamily: 'Inter-Regular', fontSize: 15, marginLeft: -16 },
        sceneContainerStyle: { backgroundColor: colors.canvas },
      }}
    >
      <Drawer.Screen name="vault" options={{ title: 'Vault', drawerIcon: ({ color }) => <Ionicons name="folder" size={16} color={color} /> }} />
      <Drawer.Screen name="graph" options={{ title: 'Graph', drawerIcon: ({ color }) => <Ionicons name="git-network" size={16} color={color} /> }} />
      <Drawer.Screen name="search" options={{ title: 'Search', drawerIcon: ({ color }) => <Ionicons name="search" size={16} color={color} /> }} />
    </Drawer>
  );
}
```

## 5. Motion

```tsx
// Command palette open: opacity + scale over 130ms
import Animated, { FadeIn } from 'react-native-reanimated';
<Animated.View entering={FadeIn.duration(130)} /* + ZoomIn.from(0.97) */ />

// File-tree folder toggle: chevron rotate (withTiming 180ms) + LinearTransition on children
import { LinearTransition } from 'react-native-reanimated';
<Animated.View layout={LinearTransition.duration(180)} />

// Node focus: animate the active node color/halo over 250ms (drive from sim state)
// Reading/source toggle: 200ms cross-fade between mono and Inter views
// Pane slide-over: drawer default spring with a 0.5 scrim
// Haptic on note open / command run / tag tap
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
```

For the graph, run a fixed-timestep force loop (edge springs + repulsion + centering) and let nodes drift toward equilibrium over ~600ms after any change. `@shopify/react-native-skia` keeps redraws smooth.

## 6. Icon Library

Use `@expo/vector-icons`:

| Purpose | Ionicons |
|---------|----------|
| File (tree) | `document-text-outline` |
| Folder (tree) | `folder` |
| Folder chevron | `chevron-forward` |
| Graph (ribbon) | `git-network` |
| Command palette (ribbon) | `terminal` |
| Search (ribbon) | `search` |
| New note (ribbon) | `create-outline` |
| Settings (ribbon) | `settings-outline` |
| Reading/source toggle | `book-outline` / `code-slash` |
| Backlink | `arrow-undo` |
| Tag | `pricetag` |
| Modified dot | `ellipse` |
| More | `ellipsis-horizontal` |

## 7. Platform Notes

- **No tab bar**: Obsidian's IA is a ribbon + file-tree panes — do not add `Tabs`
- **Status bar**: `<StatusBar style="light" />` from `expo-status-bar` — light content on the charcoal canvas
- **Safe area**: wrap with `SafeAreaView` from `react-native-safe-area-context`; the editor + keyboard accessory clear the home indicator
- **Two type systems**: keep Inter for chrome/reading and JetBrains Mono for source/commands — do not collapse them
- **Graph rendering**: use `@shopify/react-native-skia` (or `react-native-svg` for small graphs) and a fixed-timestep force loop; throttle redraws on large vaults
- **Dynamic Type**: respect scaling on reading body / titles / file rows; keep a 13pt floor on the mono source to preserve the grid; pin node labels and command shortcuts with `allowFontScaling={false}`
- **Accessibility**: the Skia/SVG graph is opaque to screen readers — expose a parallel accessible list of nodes; give wikilinks `accessibilityRole="link"` and an "internal link" label; don't rely on purple color alone
