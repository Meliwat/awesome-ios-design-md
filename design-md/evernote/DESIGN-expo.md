# Evernote (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Evernote's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3 (plus `react-native-gesture-handler` for swipe actions).

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand (interactive)
  everGreen:        '#00A82D',
  everGreenBright:  '#2DBE60',
  everGreenPressed: '#007A20',
  everGreenDeep:    '#108A00',
  everInk:          '#1C2B33',

  // Surfaces (light)
  canvas:    '#FFFFFF',
  offWhite:  '#FAFAFA',
  pressed:   '#F1F2F2',
  divider:   '#E4E5E5',
  border:    '#DCDEDE',

  // Surfaces (dark) — true near-black
  darkCanvas:   '#1C1C1E',
  darkSurface1: '#242426',
  darkSurface2: '#2E2E30',
  darkSurface3: '#3A3A3C',
  darkDivider:  '#38383A',

  // Text
  textPrimary:   '#1C2B33',
  textSecondary: '#5C6970',
  textTertiary:  '#8C969C',
  darkTextPrimary:   '#E4E4E6',
  darkTextSecondary: '#9A9A9E',
  darkTextTertiary:  '#6E6E72',

  // Tag chips
  tagBgLight:   '#E6F4EA',
  tagTextLight: '#1F7A33',
  tagBgDark:    '#1C3A24',
  tagTextDark:  '#5FD68A',

  // Semantic
  blue:    '#2F80ED',
  warn:    '#F6A609',
  error:   '#E5484D',
  yellow:  '#F6C544',
  hiLight: '#FFF3B0',
  hiDark:  '#5A4A12',

  // Notebook accents
  nbGreen:  '#00A82D',
  nbBlue:   '#2F80ED',
  nbYellow: '#F6C544',
  nbRed:    '#E5484D',
  nbPurple: '#7A5AF8',
  nbTeal:   '#1AAE9F',
} as const;

export type EverColor = keyof typeof colors;
```

## 2. Typography

Evernote uses the iOS system font; for cross-platform parity load **Inter** (SIL OFL) via `expo-font`. Body is 16/26 (≈1.6 line-height) — reading comfort first.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-Semibold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#1C2B33' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...primary, fontFamily: 'Inter-Bold',     fontSize: 30, lineHeight: 36, letterSpacing: -0.4 },
  noteTitle:   { ...primary, fontFamily: 'Inter-Bold',     fontSize: 24, lineHeight: 30, letterSpacing: -0.3 },
  noteH1:      { ...primary, fontFamily: 'Inter-Bold',     fontSize: 20, lineHeight: 26, letterSpacing: -0.2 },
  noteH2:      { ...primary, fontFamily: 'Inter-Bold',     fontSize: 17, lineHeight: 23, letterSpacing: -0.1 },
  body:        { ...primary, fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 26 }, // ≈1.6
  listTitle:   { ...primary, fontFamily: 'Inter-Semibold', fontSize: 15, lineHeight: 20 },
  checklist:   { ...primary, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 22 },
  snippet:     { color: '#5C6970', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 20 },
  tag:         { fontFamily: 'Inter-Semibold', fontSize: 12, lineHeight: 14, letterSpacing: 0.1 },
  meta:        { color: '#8C969C', fontFamily: 'Inter-Regular', fontSize: 12, lineHeight: 16 },
  button:      { fontFamily: 'Inter-Semibold', fontSize: 15, lineHeight: 15 },
  tab:         { color: '#8C969C', fontFamily: 'Inter-Medium', fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
  code:        { ...primary, fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 21 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Tag Pill

```tsx
// components/TagPill.tsx
import { Text, View, useColorScheme } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TagPill({ text }: { text: string }) {
  const dark = useColorScheme() === 'dark';
  return (
    <View style={{
      alignSelf: 'flex-start',
      borderRadius: 999,
      paddingVertical: 3,
      paddingHorizontal: 9,
      backgroundColor: dark ? colors.tagBgDark : colors.tagBgLight,
    }}>
      <Text style={[typography.tag, { color: dark ? colors.tagTextDark : colors.tagTextLight }]}>{text}</Text>
    </View>
  );
}
```

### Checklist Item

```tsx
// components/ChecklistItem.tsx
import { useState } from 'react';
import { Pressable, Text, View, useColorScheme } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChecklistItem({ initialDone = false, text }: { initialDone?: boolean; text: string }) {
  const dark = useColorScheme() === 'dark';
  const [done, setDone] = useState(initialDone);
  const scale = useSharedValue(1);
  const boxStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  const green = dark ? colors.everGreenBright : colors.everGreen;

  const toggle = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    setDone((d) => !d);
    scale.value = withSpring(1.12, { damping: 6, stiffness: 220 }, () => { scale.value = withSpring(1); });
  };

  return (
    <View style={{ flexDirection: 'row', alignItems: 'flex-start', gap: 10 }}>
      <Pressable onPress={toggle} hitSlop={14} style={{ width: 44, height: 44, alignItems: 'flex-start' }}>
        <Animated.View style={[{
          width: 19, height: 19, borderRadius: 5,
          borderWidth: done ? 0 : 2, borderColor: colors.textTertiary,
          backgroundColor: done ? green : 'transparent',
          alignItems: 'center', justifyContent: 'center',
        }, boxStyle]}>
          {done && <Ionicons name="checkmark" size={11} color="#FFF" />}
        </Animated.View>
      </Pressable>
      <Text style={[typography.checklist, {
        color: done ? colors.textTertiary : (dark ? colors.darkTextPrimary : colors.textPrimary),
        textDecorationLine: done ? 'line-through' : 'none',
      }]}>
        {text}
      </Text>
    </View>
  );
}
```

### Note List Row

```tsx
// components/NoteRow.tsx
import { Text, View, useColorScheme } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function NoteRow({
  title, snippet, date, notebook, hasThumb = false,
}: {
  title: string; snippet: string; date: string; notebook: string; hasThumb?: boolean;
}) {
  const dark = useColorScheme() === 'dark';
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'flex-start', gap: 12,
      paddingVertical: 14,
      borderBottomWidth: 1,
      borderBottomColor: dark ? colors.darkDivider : colors.divider,
    }}>
      <View style={{ flex: 1 }}>
        <Text style={[typography.listTitle, dark && { color: colors.darkTextPrimary }]} numberOfLines={1}>{title}</Text>
        <Text style={[typography.snippet, dark && { color: colors.darkTextSecondary }]} numberOfLines={2}>{snippet}</Text>
        <Text style={[typography.meta, dark && { color: colors.darkTextTertiary }, { marginTop: 3 }]}>{date} · {notebook}</Text>
      </View>
      {hasThumb && (
        <View style={{ width: 52, height: 52, borderRadius: 8, backgroundColor: dark ? colors.darkSurface3 : colors.pressed }} />
      )}
    </View>
  );
}
```

### Format Toolbar (keyboard accessory)

```tsx
// components/FormatToolbar.tsx
import { View, Pressable, useColorScheme } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

const tools = ['text', 'list', 'checkbox-outline', 'attach', 'image-outline', 'link', 'color-fill-outline'] as const;

export function FormatToolbar() {
  const dark = useColorScheme() === 'dark';
  return (
    <View style={{
      flexDirection: 'row', justifyContent: 'space-around', alignItems: 'center',
      paddingVertical: 12,
      backgroundColor: dark ? colors.darkSurface2 : colors.canvas,
      borderTopWidth: 0.5, borderTopColor: dark ? colors.darkDivider : colors.divider,
    }}>
      {tools.map((t) => (
        <Pressable key={t} hitSlop={10}>
          <Ionicons name={t} size={20} color={dark ? colors.darkTextSecondary : colors.textSecondary} />
        </Pressable>
      ))}
    </View>
  );
}
```

### Floating Action Button

```tsx
// components/EvernoteFAB.tsx
import { Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function EvernoteFAB({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        width: 52, height: 52, borderRadius: 26,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.everGreenPressed : colors.everGreen,
        transform: [{ scale: pressed ? 0.96 : 1 }],
        shadowColor: colors.everGreen, shadowOpacity: 0.4, shadowRadius: 16, shadowOffset: { width: 0, height: 6 },
        elevation: 8,
      })}
    >
      <Ionicons name="add" size={24} color="#FFF" />
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar (with center FAB)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';
import { EvernoteFAB } from '../../components/EvernoteFAB';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.everGreen,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="home"   options={{ title: 'Home',  tabBarIcon: ({ color }) => <Ionicons name="home-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="notes"  options={{ title: 'Notes', tabBarIcon: ({ color }) => <Ionicons name="document-text-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="create" options={{
        title: '',
        tabBarButton: () => <View style={{ top: -6 }}><EvernoteFAB onPress={() => {/* open capture sheet */}} /></View>,
      }} />
      <Tabs.Screen name="notebooks" options={{ title: 'Notebooks', tabBarIcon: ({ color }) => <Ionicons name="library-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="search"    options={{ title: 'Search',    tabBarIcon: ({ color }) => <Ionicons name="search" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { FadeIn, FadeOut, useSharedValue, withSpring, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Checkbox toggle — spring fill (see ChecklistItem above)
// scale.value = withSpring(1.12, { damping: 6, stiffness: 220 }, () => scale.value = withSpring(1))

// Note open (list → editor) — expo-router stack push (300ms native slide)

// FAB → capture sheet — @gorhom/bottom-sheet or Modal; slide-up 300ms ease-out
// FAB press: transform scale 0.96 (Pressable style above)

// Sync toast
// <Animated.View entering={FadeIn} exiting={FadeOut}> ... </Animated.View>  (auto-dismiss 2s)

// Stack expand
const rot = useSharedValue(0); // 0 -> 90 via withTiming(90, { duration: 150 })
// rows: <Animated.View entering={FadeIn.duration(200)} exiting={FadeOut.duration(150)}>

// Tag add
// entering={FadeIn.duration(150)} + initial scale 0.8

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);  // checkbox toggle, tab change
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); // swipe-action commit, note delete
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // FAB long-press
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Evernote's UI maps cleanly to outline Ionicons; the brand green carries state.

| Purpose | Ionicons |
|---------|----------|
| Home (tab) | `home-outline` |
| Notes (tab) | `document-text-outline` |
| FAB / new note | `add` |
| Notebooks (tab) | `library-outline` |
| Search (tab) | `search` |
| Back | `chevron-back` |
| Overflow | `ellipsis-horizontal` |
| Note info | `information-circle-outline` |
| Checkbox empty | `square-outline` |
| Checkbox done | `checkbox` |
| Bold | `text` |
| List | `list` |
| Checklist | `checkbox-outline` |
| Attach | `attach` |
| Photo / camera | `image-outline` / `camera-outline` |
| Audio | `mic-outline` |
| Link | `link` |
| Highlight | `color-fill-outline` |
| Table | `grid-outline` |
| Tag | `pricetag-outline` |
| Pin | `bookmark-outline` |
| Reminder | `time-outline` |
| Notebook | `book-outline` |
| Stack | `albums-outline` |

## 7. Platform Notes

- **Font choice**: Evernote uses the system font on iOS; bundle **Inter** (SIL OFL) for visual parity across the Expo/Compose targets — keep body at 16/26 (≈1.6)
- **Reading comfort**: never tighten `body.lineHeight` below ~24 at 16px; 22px horizontal editor padding for line-length
- **Status bar**: `<StatusBar style="dark" />` on light, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; the FAB must sit above the bottom safe-area inset (raise it ~6px above the tab strip)
- **Swipe actions**: use `react-native-gesture-handler` `Swipeable` (or `ReanimatedSwipeable`) — leading pin/move, trailing delete (red `#E5484D`)/tag; full-swipe with medium haptic
- **Format toolbar**: render it inside `InputAccessoryView` (iOS) or a `KeyboardAvoidingView`-pinned bar so it docks directly above the keyboard while editing
- **Dynamic Type**: set `allowFontScaling={false}` on tab labels, tag pills, and sync-toast text; allow scaling on titles/body/snippets/checklist
- **Dark mode**: use `useColorScheme()`; the dark canvas is true near-black `#1C1C1E` (NOT charcoal-gray); brighten interactive green to `everGreenBright`; replace shadows with surface step + 1px border on menus/sheets (FAB keeps its green glow)
- **Keyboard**: `KeyboardAvoidingView` around the editor; body scrolls above the docked format toolbar
- **Accessibility**: note rows `accessibilityLabel` "{title}, {snippet}, {date}, {notebook}"; checklist items announce checked/unchecked and toggle on activate; never convey done-state by color alone — strikethrough + dim is the secondary cue; the FAB has a distinct circular shape and "New note" label
