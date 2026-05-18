# Miro (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Miro's visual language into paste-ready Expo / React Native code: a design-token module, themed components, the gesture-driven infinite canvas, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `react-native-gesture-handler`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Board & chrome (light)
  board:          '#F5F5F7',
  grid:           '#D7D7DE',
  chrome:         '#FFFFFF',
  surfaceGray:    '#F1F1F4',
  surfacePressed: '#E6E6EB',
  divider:        '#E3E3E8',

  // Board & chrome (dark)
  darkCanvas:   '#1B1B1F',
  darkBoard:    '#202024',
  darkGrid:     '#38383F',
  darkSurface1: '#26262B',
  darkSurface2: '#313138',

  // Text
  ink:             '#050038', // Miro Ink — primary text on light
  textSecondary:   '#6B6B7B',
  textTertiary:    '#9A9AA4',
  darkTextPrimary: '#ECECEF',
  noteInk:         '#2A2A33',

  // Brand / interactive
  yellow:        '#FFD02F',
  yellowPressed: '#E8B800',
  blue:          '#4262FF', // selection / interaction
  bluePressed:   '#2F4AE0',

  // Sticky note palette (full-color in both modes)
  noteYellow: '#FEF3B6',
  notePink:   '#F8C8D8',
  noteGreen:  '#C7E8C7',
  noteBlue:   '#BCE0F5',
  noteOrange: '#FCD9B6',
  noteViolet: '#DCC9F0',

  // Semantic
  error:   '#E5484D',
  success: '#2EA56A',
  warning: '#F0A92B',
} as const;

export type MiroColor = keyof typeof colors;

// Multiplayer cursor colors — assign round-robin
export const cursorColors = ['#4262FF', '#F24822', '#14AE5C', '#9747FF', '#F2A900', '#E5489E'] as const;

// Sticky note palette as an ordered list for the color picker
export const stickyPalette = [
  colors.noteYellow, colors.notePink, colors.noteGreen,
  colors.noteBlue, colors.noteOrange, colors.noteViolet,
] as const;
```

## 2. Typography

Load Inter (UI), Caveat (optional handwriting sticky), JetBrains Mono (code embeds) via `expo-font`.

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
    'Caveat-SemiBold': require('../assets/fonts/Caveat-SemiBold.ttf'),
    'JetBrainsMono-Regular': require('../assets/fonts/JetBrainsMono-Regular.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
const ink = { color: '#050038' } satisfies TextStyle;

export const typography = {
  boardTitle: { ...ink, fontFamily: 'Inter-ExtraBold', fontSize: 28, lineHeight: 34, letterSpacing: -0.5 },
  h1:         { ...ink, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.3 },
  frameLabel: { color: '#6B6B7B', fontFamily: 'Inter-Bold', fontSize: 18, lineHeight: 23, letterSpacing: -0.2 },
  sticky:     { color: '#2A2A33', fontFamily: 'Inter-SemiBold', fontSize: 16, lineHeight: 21 },
  body:       { ...ink, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 24 },
  control:    { ...ink, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 15 },
  button:     { color: '#050038', fontFamily: 'Inter-Bold', fontSize: 15, lineHeight: 15 },
  meta:       { color: '#6B6B7B', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 19 },
  caption:    { color: '#6B6B7B', fontFamily: 'Inter-SemiBold', fontSize: 12, lineHeight: 12, letterSpacing: 0.2 },
  connector:  { ...ink, fontFamily: 'Inter-Medium', fontSize: 13, lineHeight: 16 },
  listRow:    { ...ink, fontFamily: 'Inter-Medium', fontSize: 16, lineHeight: 21 },
  tab:        { fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  avatar:     { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 11, lineHeight: 11, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Infinite Canvas (pinch-zoom + pan)

```tsx
// components/MiroCanvas.tsx
import { useColorScheme, View } from 'react-native';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, runOnJS } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { MiroGrid } from './MiroGrid';

export function MiroCanvas({ children }: { children: React.ReactNode }) {
  const dark = useColorScheme() === 'dark';
  const scale = useSharedValue(1);
  const savedScale = useSharedValue(1);
  const tx = useSharedValue(0); const ty = useSharedValue(0);
  const sx = useSharedValue(0); const sy = useSharedValue(0);

  const pinch = Gesture.Pinch()
    .onUpdate((e) => { scale.value = Math.min(Math.max(savedScale.value * e.scale, 0.2), 5); })
    .onEnd(() => { savedScale.value = scale.value; });

  const pan = Gesture.Pan().minPointers(2)
    .onUpdate((e) => { tx.value = sx.value + e.translationX; ty.value = sy.value + e.translationY; })
    .onEnd(() => { sx.value = tx.value; sy.value = ty.value; });

  const doubleTap = Gesture.Tap().numberOfTaps(2).onEnd(() => {
    scale.value = withTiming(1, { duration: 350 });
    tx.value = withTiming(0, { duration: 350 }); ty.value = withTiming(0, { duration: 350 });
    savedScale.value = 1; sx.value = 0; sy.value = 0;
  });

  const worldStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: tx.value }, { translateY: ty.value }, { scale: scale.value }],
  }));

  return (
    <GestureDetector gesture={Gesture.Simultaneous(pinch, pan, doubleTap)}>
      <View style={{ flex: 1, backgroundColor: dark ? colors.darkBoard : colors.board, overflow: 'hidden' }}>
        <MiroGrid scale={scale} tx={tx} ty={ty} color={dark ? colors.darkGrid : colors.grid} />
        <Animated.View style={[{ position: 'absolute', left: 0, top: 0 }, worldStyle]}>
          {children}
        </Animated.View>
      </View>
    </GestureDetector>
  );
}
```

```tsx
// components/MiroGrid.tsx — a zoom-scaled dotted grid (Skia or tiled View; Skia recommended)
import { Canvas, Circle, Group } from '@shopify/react-native-skia';
import { useWindowDimensions } from 'react-native';
import { useDerivedValue, type SharedValue } from 'react-native-reanimated';

export function MiroGrid({ scale, tx, ty, color }: { scale: SharedValue<number>; tx: SharedValue<number>; ty: SharedValue<number>; color: string }) {
  const { width, height } = useWindowDimensions();
  const dots = [];
  const base = 28;
  // Render a generous static dot field; offset/scale the Group with the canvas transform.
  for (let x = 0; x < width + base * 4; x += base) for (let y = 0; y < height + base * 4; y += base) dots.push({ x, y });
  const transform = useDerivedValue(() => [
    { translateX: tx.value % (base * scale.value) }, { translateY: ty.value % (base * scale.value) }, { scale: scale.value },
  ]);
  return (
    <Canvas style={{ position: 'absolute', width, height }}>
      <Group transform={transform}>
        {dots.map((d, i) => <Circle key={i} cx={d.x} cy={d.y} r={0.75} color={color} />)}
      </Group>
    </Canvas>
  );
}
```

### Sticky Note — *signature*

```tsx
// components/MiroStickyNote.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MiroStickyNote({ text, fill = colors.noteYellow, selected: initSel = false }: {
  text: string; fill?: string; selected?: boolean;
}) {
  const [selected, setSelected] = useState(initSel);
  return (
    <Pressable onPress={() => setSelected((s) => !s)} style={{ width: 96, height: 96 }}>
      <View style={{
        flex: 1, borderRadius: 4, backgroundColor: fill, padding: 10,
        alignItems: 'center', justifyContent: 'center',
        shadowColor: '#000', shadowOpacity: 0.18, shadowRadius: 9, shadowOffset: { width: 0, height: 8 }, elevation: 6,
      }}>
        <Text style={[typography.sticky, { textAlign: 'center' }]} adjustsFontSizeToFit numberOfLines={5}>{text}</Text>
      </View>
      {selected ? (
        <>
          <View pointerEvents="none" style={{
            position: 'absolute', left: -4, top: -4, right: -4, bottom: -4,
            borderWidth: 2, borderColor: colors.blue, borderRadius: 6,
          }} />
          <View style={{ position: 'absolute', bottom: -8, alignSelf: 'center', width: 30, height: 4, borderRadius: 3, backgroundColor: colors.blue }} />
        </>
      ) : null}
    </Pressable>
  );
}
```

### Floating Toolbar

```tsx
// components/MiroToolbar.tsx
import { Pressable, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

const TOOLS = ['arrow-up-outline', 'square', 'pencil', 'shapes-outline', 'text', 'scan-outline', 'chatbubble-outline', 'add'] as const;

export function MiroToolbar({ active, onSelect }: { active: number; onSelect: (i: number) => void }) {
  return (
    <View style={{
      flexDirection: 'row', gap: 4, padding: 8,
      backgroundColor: colors.chrome, borderRadius: 16,
      borderWidth: 1, borderColor: colors.divider,
      shadowColor: '#000', shadowOpacity: 0.22, shadowRadius: 16, shadowOffset: { width: 0, height: 14 }, elevation: 12,
    }}>
      {TOOLS.map((icon, i) => (
        <Pressable key={icon} onPress={() => { Haptics.selectionAsync(); onSelect(i); }}
          style={{
            width: 40, height: 40, borderRadius: 11, alignItems: 'center', justifyContent: 'center',
            backgroundColor: active === i ? colors.yellow : 'transparent',
          }}>
          <Ionicons name={icon as any} size={20} color={active === i ? colors.ink : colors.textSecondary} />
        </Pressable>
      ))}
    </View>
  );
}
```

### Primary Button (Yellow)

```tsx
// components/MiroPrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MiroPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      backgroundColor: pressed ? colors.yellowPressed : colors.yellow,
      paddingVertical: 13, paddingHorizontal: 26, borderRadius: 10, alignItems: 'center',
      transform: [{ scale: pressed ? 0.98 : 1 }],
    })}>
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Zoom Pill

```tsx
// components/MiroZoomPill.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MiroZoomPill({ percent, onZoom }: { percent: number; onZoom: (d: number) => void }) {
  return (
    <View style={{
      backgroundColor: colors.chrome, borderRadius: 12,
      shadowColor: '#000', shadowOpacity: 0.16, shadowRadius: 10, shadowOffset: { width: 0, height: 8 }, elevation: 6,
    }}>
      <Pressable onPress={() => onZoom(25)} style={{ width: 38, height: 36, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="add" size={18} color={colors.ink} />
      </Pressable>
      <Text style={[typography.caption, { textAlign: 'center', paddingVertical: 4,
        borderTopWidth: 1, borderBottomWidth: 1, borderColor: colors.divider }]}>{percent}%</Text>
      <Pressable onPress={() => onZoom(-25)} style={{ width: 38, height: 36, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="remove" size={18} color={colors.ink} />
      </Pressable>
    </View>
  );
}
```

### Multiplayer Cursor

```tsx
// components/MiroCursor.tsx
import { Text, View } from 'react-native';
import Animated, { useAnimatedStyle, withTiming, type SharedValue } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { cursorColors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MiroCursor({ name, colorIndex, x, y }: { name: string; colorIndex: number; x: SharedValue<number>; y: SharedValue<number> }) {
  const color = cursorColors[colorIndex % cursorColors.length];
  const style = useAnimatedStyle(() => ({
    // interpolate between network updates so the cursor glides, never jumps
    transform: [{ translateX: withTiming(x.value, { duration: 50 }) }, { translateY: withTiming(y.value, { duration: 50 }) }],
  }));
  return (
    <Animated.View style={[{ position: 'absolute', flexDirection: 'row', alignItems: 'center', gap: 5 }, style]}>
      <Ionicons name="navigate" size={14} color={color} style={{ transform: [{ rotate: '-35deg' }] }} />
      <View style={{ backgroundColor: color, paddingHorizontal: 7, paddingVertical: 2, borderRadius: 10 }}>
        <Text style={typography.avatar}>{name}</Text>
      </View>
    </Animated.View>
  );
}
```

## 4. Bottom Tab Bar (Boards Home)

Inside a board there is no tab bar (the toolbar floats). The Boards-home shell uses `expo-router` Tabs.

```tsx
// app/(home)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function HomeTabs() {
  return (
    <Tabs screenOptions={{
      tabBarActiveTintColor: colors.ink,
      tabBarInactiveTintColor: colors.textSecondary,
      tabBarStyle: { backgroundColor: colors.chrome, borderTopWidth: 0.5, borderTopColor: colors.divider },
      tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
    }}>
      <Tabs.Screen name="boards"    options={{ title: 'Boards',    tabBarIcon: ({ color }) => <Ionicons name="grid" size={24} color={color} /> }} />
      <Tabs.Screen name="templates" options={{ title: 'Templates', tabBarIcon: ({ color }) => <Ionicons name="copy-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="activity"  options={{ title: 'Activity',  tabBarIcon: ({ color }) => <Ionicons name="notifications-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="profile"   options={{ title: 'Profile',   tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Pan/zoom — react-native-gesture-handler Pinch + Pan (Simultaneous), shared values
// Double-tap zoom-to-fit
scale.value = withTiming(1, { duration: 350 });

// Object create (sticky) — spring scale-in from 0.85
import { withSpring } from 'react-native-reanimated';
scaleIn.value = withSpring(1, { damping: 12, stiffness: 220 }); // start 0.85

// Object drag — slight scale-up + shadow bloom; settle with spring on release
// drop: withSpring(target, { damping: 16, stiffness: 180 }) + Haptics

// Tool select — yellow fill crossfade ~120ms; sub-palette withSpring pop

// Multiplayer cursor — withTiming(pos, { duration: 50 }) to glide between updates

// Sheet present — @gorhom/bottom-sheet spring { damping: 18, stiffness: 180 }

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // object create, drag drop
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Rigid);  // connector snap
Haptics.selectionAsync();                                  // tool select
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Select tool | `arrow-up-outline` |
| Sticky note tool | `square` |
| Pen tool | `pencil` |
| Shapes tool | `shapes-outline` |
| Text tool | `text` |
| Frame tool | `scan-outline` |
| Comment tool | `chatbubble-outline` |
| More tool | `add` |
| Zoom in / out | `add` / `remove` |
| Fit / minimap | `expand-outline` |
| Back | `chevron-back` |
| Board menu | `chevron-down` |
| Board actions | `ellipsis-horizontal` |
| Collaborators | `people` |
| Share | `share-outline` |
| Cursor (remote) | `navigate` |
| Boards (tab) | `grid` |
| Templates (tab) | `copy-outline` |
| Activity (tab) | `notifications-outline` |
| Profile (tab) | `person-circle` |

## 7. Platform Notes

- **Fonts**: Inter (UI), Caveat (optional handwriting sticky), JetBrains Mono (code embeds) — all SIL OFL, free to bundle
- **Canvas perf**: render the grid with `@shopify/react-native-skia` (or a memoized tiled `View`); transform the world layer with a single Reanimated `transform` for 60fps pan/zoom; virtualize off-screen objects on large boards
- **Gestures**: use `react-native-gesture-handler` `Gesture.Simultaneous(Pinch, Pan, DoubleTap)`; require 2 pointers for pan so 1-finger drag can move objects
- **Status bar**: `<StatusBar style="dark" />` on light, `"light"` on dark; the board top bar is translucent over the canvas
- **Safe area**: float the toolbar + zoom pill above the home indicator; the canvas bleeds edge-to-edge under chrome
- **Dynamic Type**: chrome `<Text>` respects system scale; set `allowFontScaling={false}` on toolbar labels, zoom %, avatar initials, connector labels, tab labels; sticky-note text uses `adjustsFontSizeToFit`
- **Dark mode**: `useColorScheme()` → board `darkBoard`, chrome `darkSurface1`; **sticky notes stay full-color** (don't theme them); cursor colors unchanged
- **Multiplayer**: drive remote cursor/object positions from shared values updated by your realtime layer; always `withTiming(..., { duration: 50 })` so they interpolate
- **Accessibility**: expose canvas objects as an accessible list with a "Find on board" search (spatial nav is hard non-visually); label sticky notes "Sticky note: {text}, {color}"; announce remote presence sparingly
