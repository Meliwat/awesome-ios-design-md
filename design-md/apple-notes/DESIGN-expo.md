# Apple Notes (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Apple Notes's paper-soft aesthetic into paste-ready Expo / React Native code: a design-token module, themed components, the iconic yellow folder glyph as an SVG, and the editor's text-selection highlight.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (Light — warm cream paper)
  cream:        '#FFFBED',
  creamSurf1:   '#FAF6E3',
  creamSurf2:   '#F2EDD6',
  dividerLight: '#EDEAD8',

  // Canvas & surfaces (Dark)
  darkPaper:    '#1A1A1A',
  darkSurf1:    '#262626',
  darkSurf2:    '#333333',
  darkDivider:  '#2A2A2A',

  // Brand
  orange:           '#F09A38',
  orangePressed:    '#D87E1F',
  orangeTint:       '#FFF1DD',
  folderYellow:     '#F5D773',
  folderHighlight:  '#FAE8A0',
  highlightYellow:  '#FFEB78',

  // Text
  ink:          '#1C1C1E',
  slate:        '#8E8E93',
  mute:         '#C7C7CC',
  softWhite:    '#F2F2F2',

  // Semantic
  success:      '#34C759',
  warning:      '#FFCC00',
  error:        '#FF3B30',
  infoBlue:     '#0A84FF',
  lockYellow:   '#FFCC00',

  // Highlight palette (text selection)
  highlightGreen:  '#B3E47B',
  highlightBlue:   '#A0D8FF',
  highlightPink:   '#FFB3DB',
  highlightPurple: '#D4B3FF',
} as const;

export type NotesColor = keyof typeof colors;
```

## 2. Typography

SF Pro is the default system face on iOS. The large nav title uses the rare Heavy weight (800). Body text uses 17pt 400 with a 1.5 line height — the calmest body in any iOS app.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const SYS = 'System'; // resolves to SF Pro on iOS
const MONO = 'Menlo'; // SF Mono fallback

const ink   = { color: '#1C1C1E' };
const slate = { color: '#8E8E93' };

export const typography = {
  // Large titles
  largeNav:    { ...ink, fontFamily: SYS, fontSize: 34, fontWeight: '800', letterSpacing: -0.4, lineHeight: 38 },
  folderSec:   { ...ink, fontFamily: SYS, fontSize: 22, fontWeight: '700', letterSpacing: -0.3, lineHeight: 26 },

  // Note row (three-line stack)
  rowTitle:    { ...ink,   fontFamily: SYS, fontSize: 16, fontWeight: '600', letterSpacing: -0.1, lineHeight: 21 },
  rowPreview:  { ...slate, fontFamily: SYS, fontSize: 14, fontWeight: '400', lineHeight: 19 },
  rowDate:     { ...slate, fontFamily: SYS, fontSize: 12, fontWeight: '400', lineHeight: 16 },

  // Folder row
  folderRow:   { ...ink, fontFamily: SYS, fontSize: 17, fontWeight: '400', lineHeight: 22 },

  // Note body
  bodyTitle:   { ...ink, fontFamily: SYS, fontSize: 20, fontWeight: '600', letterSpacing: -0.2, lineHeight: 26 },
  bodyHeading: { ...ink, fontFamily: SYS, fontSize: 17, fontWeight: '600', lineHeight: 24 },
  body:        { ...ink, fontFamily: SYS, fontSize: 17, fontWeight: '400', lineHeight: 25.5 },   // 1.5 line height
  bodyMono:    { ...ink, fontFamily: MONO, fontSize: 15, fontWeight: '400', lineHeight: 22 },

  // Misc
  tagChip:     { color: '#F09A38', fontFamily: SYS, fontSize: 14, fontWeight: '500', lineHeight: 17 },
  toolbar:     { fontFamily: SYS, fontSize: 11, fontWeight: '500', letterSpacing: 0.1, lineHeight: 13 },
  button:      { color: '#FFFFFF', fontFamily: SYS, fontSize: 17, fontWeight: '600', lineHeight: 22 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Yellow Folder Glyph (the iconic mark)

```tsx
// components/FolderGlyph.tsx
import Svg, { Path } from 'react-native-svg';
import { View } from 'react-native';
import { colors } from '../theme/colors';

type Props = { size?: number };

export function FolderGlyph({ size = 24 }: Props) {
  const w = size;
  const h = size * 0.78;
  const tabH = h * 0.22;

  // Folder body path: tab on top-left, rectangle below
  const path = `
    M 0 ${tabH}
    L 0 ${h - 1}
    Q 0 ${h} 1 ${h}
    L ${w - 1} ${h}
    Q ${w} ${h} ${w} ${h - 1}
    L ${w} ${tabH - 2}
    L ${w * 0.5 + 6} ${tabH - 2}
    Q ${w * 0.5 + 3} 0 ${w * 0.5} 0
    L 3 0
    Q 0 0 0 3
    Z
  `;

  return (
    <View style={{ width: w, height: h, transform: [{ rotate: '-2deg' }] }}>
      <Svg width={w} height={h} viewBox={`0 0 ${w} ${h}`}>
        <Path d={path} fill={colors.folderYellow} stroke={colors.orange} strokeWidth={1} />
        {/* Top edge highlight */}
        <Path d={`M 6 ${tabH + 1} L ${w - 6} ${tabH + 1}`} stroke={colors.folderHighlight} strokeWidth={0.5} fill="none" />
      </Svg>
    </View>
  );
}
```

### Note Row

```tsx
// components/NoteRow.tsx
import { Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = { title: string; preview: string; date: string; isPinned?: boolean; onPress: () => void };

export function NoteRow({ title, preview, date, isPinned = false, onPress }: Props) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      paddingHorizontal: 16, paddingTop: 16, height: 84,
      backgroundColor: pressed ? colors.creamSurf2 : colors.cream,
      borderBottomWidth: 0.5, borderBottomColor: colors.dividerLight,
    })}>
      <View style={{ gap: 4 }}>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
          {isPinned && <Ionicons name="pin" size={11} color={colors.orange} />}
          <Text style={typography.rowTitle} numberOfLines={1}>{title}</Text>
        </View>
        <Text style={typography.rowPreview} numberOfLines={1}>{preview}</Text>
        <Text style={typography.rowDate}>{date}</Text>
      </View>
    </Pressable>
  );
}
```

### Folder Row

```tsx
// components/FolderRow.tsx
import { Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { FolderGlyph } from './FolderGlyph';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = { name: string; count: number; isSmart?: boolean; onPress: () => void };

export function FolderRow({ name, count, isSmart = false, onPress }: Props) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      flexDirection: 'row', alignItems: 'center', gap: 12,
      paddingHorizontal: 16, height: 44,
      backgroundColor: pressed ? colors.creamSurf2 : colors.cream,
    })}>
      {isSmart && <Ionicons name="sparkles" size={14} color={colors.orange} />}
      <FolderGlyph size={24} />
      <Text style={[typography.folderRow, { flex: 1 }]}>{name}</Text>
      <Text style={[typography.folderRow, { color: colors.slate }]}>{count}</Text>
      <Ionicons name="chevron-forward" size={13} color={colors.slate} />
    </Pressable>
  );
}
```

### New Note FAB

```tsx
// components/NewNoteFAB.tsx
import { Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function NewNoteFAB({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        position: 'absolute', bottom: 16, right: 16,
        width: 56, height: 56, borderRadius: 28,
        backgroundColor: colors.orange,
        alignItems: 'center', justifyContent: 'center',
        shadowColor: colors.orange, shadowOpacity: 0.30, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
        elevation: 8,
        transform: [{ scale: pressed ? 0.95 : 1 }],
      })}
    >
      <Ionicons name="create" size={22} color="#FFFFFF" />
    </Pressable>
  );
}
```

### Primary CTA

```tsx
// components/NotesPrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function NotesPrimaryButton({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={({ pressed }) => ({
        minHeight: 44, borderRadius: 12, paddingHorizontal: 24, paddingVertical: 12,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.orangePressed : colors.orange,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{label}</Text>
    </Pressable>
  );
}
```

### Tag Chip

```tsx
// components/TagChip.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = { name: string; isSelected: boolean; onPress: () => void };

export function TagChip({ name, isSelected, onPress }: Props) {
  return (
    <Pressable
      onPress={() => { Haptics.selectionAsync(); onPress(); }}
      style={{
        paddingHorizontal: 12, paddingVertical: 6,
        backgroundColor: isSelected ? colors.orange : colors.orangeTint,
        borderRadius: 16,
        flexDirection: 'row',
      }}
    >
      <Text style={[typography.tagChip, { color: isSelected ? '#FFFFFF' : colors.orange }]}>#{name}</Text>
    </Pressable>
  );
}
```

### Search Bar

```tsx
// components/NotesSearchBar.tsx
import { View, TextInput, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useState } from 'react';
import { colors } from '../theme/colors';

export function NotesSearchBar({ value, onChangeText }: { value: string; onChangeText: (s: string) => void }) {
  const [focused, setFocused] = useState(false);
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 8,
      paddingHorizontal: 12, height: 36, borderRadius: 10,
      backgroundColor: colors.creamSurf1,
      borderWidth: focused ? 2 : 0, borderColor: colors.orange,
    }}>
      <Ionicons name="search" size={14} color={colors.slate} />
      <TextInput
        value={value}
        onChangeText={onChangeText}
        placeholder="Search"
        placeholderTextColor={colors.slate}
        onFocus={() => setFocused(true)}
        onBlur={() => setFocused(false)}
        style={{ flex: 1, fontSize: 17, fontWeight: '400', color: colors.ink, fontFamily: 'System' }}
      />
      {value ? (
        <Pressable onPress={() => onChangeText('')}>
          <Ionicons name="close-circle" size={14} color={colors.slate} />
        </Pressable>
      ) : (
        <Ionicons name="mic" size={14} color={colors.slate} />
      )}
    </View>
  );
}
```

### Checklist Item

```tsx
// components/ChecklistItem.tsx
import { useState } from 'react';
import { Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChecklistItem({ text, initialChecked = false }: { text: string; initialChecked?: boolean }) {
  const [checked, setChecked] = useState(initialChecked);
  const fillScale = useSharedValue(initialChecked ? 1 : 0);
  const textOpacity = useSharedValue(initialChecked ? 0.6 : 1.0);

  useEffect(() => {
    fillScale.value   = withTiming(checked ? 1   : 0,   { duration: 200 });
    textOpacity.value = withTiming(checked ? 0.6 : 1.0, { duration: 300 });
  }, [checked]);

  const fillStyle = useAnimatedStyle(() => ({ transform: [{ scale: fillScale.value }] }));
  const textStyle = useAnimatedStyle(() => ({ opacity: textOpacity.value }));

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingVertical: 4 }}>
      <Pressable onPress={() => { Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); setChecked((c) => !c); }}>
        <View style={{ width: 24, height: 24, borderRadius: 12, borderWidth: 1.5, borderColor: colors.slate, alignItems: 'center', justifyContent: 'center' }}>
          <Animated.View style={[{ position: 'absolute', width: 24, height: 24, borderRadius: 12, backgroundColor: colors.orange, alignItems: 'center', justifyContent: 'center' }, fillStyle]}>
            {checked && <Ionicons name="checkmark" size={12} color="#FFFFFF" />}
          </Animated.View>
        </View>
      </Pressable>
      <Animated.Text style={[typography.body, textStyle, checked && { textDecorationLine: 'line-through', color: colors.slate }]}>
        {text}
      </Animated.Text>
    </View>
  );
}
```

### Pinned Card (horizontal scroll)

```tsx
// components/PinnedCard.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PinnedCard({ title, preview }: { title: string; preview: string }) {
  return (
    <View style={{ width: 160, height: 96, padding: 12, borderRadius: 10, backgroundColor: colors.creamSurf1 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
        <Text style={{ ...typography.rowTitle, fontSize: 15 }} numberOfLines={1}>{title}</Text>
        <Ionicons name="pin" size={11} color={colors.orange} />
      </View>
      <Text style={{ ...typography.rowPreview, fontSize: 12, marginTop: 4 }} numberOfLines={3}>{preview}</Text>
    </View>
  );
}
```

## 4. Navigation (no tab bar — hierarchical)

```tsx
// app/_layout.tsx
import { Stack } from 'expo-router';
import { colors } from '../theme/colors';

export default function Root() {
  return (
    <Stack
      screenOptions={{
        headerStyle:   { backgroundColor: colors.cream },
        headerTintColor: colors.orange,
        headerTitleStyle: { color: colors.ink, fontWeight: '700' },
        headerLargeTitle: true,
        headerLargeTitleStyle: { fontSize: 34, fontWeight: '800', color: colors.ink },
      }}
    >
      <Stack.Screen name="index"          options={{ title: 'Folders' }} />
      <Stack.Screen name="folder/[id]"    options={{ title: 'Notes' }} />
      <Stack.Screen name="note/[id]"      options={{ title: '', headerBackTitle: 'All Notes' }} />
    </Stack>
  );
}
```

## 5. Motion & Haptics

```tsx
// FAB tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Primary CTA tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Checklist toggle
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Pin / Unpin swipe
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Tag chip selection
Haptics.selectionAsync();

// Checklist fill — Reanimated withTiming over 200ms; text strikethrough fade over 300ms
```

## 6. Icon Library

`@expo/vector-icons` — Ionicons. Mapping:

| Purpose | Ionicons |
|---------|----------|
| New Note FAB | `create` / `create-outline` |
| Pin indicator | `pin` / `pin-outline` |
| Smart folder | `sparkles` |
| Search | `search` |
| Voice search | `mic` |
| Clear search | `close-circle` |
| Checklist toolbar | `checkbox-outline` |
| Checkmark (filled) | `checkmark` |
| Share | `share-outline` |
| More menu | `ellipsis-horizontal-circle` |
| Back chevron | `chevron-back` |
| Row chevron | `chevron-forward` |
| Photo toolbar | `camera-outline` |
| Table toolbar | `grid-outline` |
| Format toolbar | `text` |
| Lock | `lock-closed` |

## 7. Platform Notes

- **Warm cream canvas**: the entire light-mode theme is rendered on `#FFFBED`. Test on real hardware — the cream is subtle but unmistakable on an actual iPhone display.
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` in light mode, `style="light"` in dark mode (auto via `useColorScheme()`).
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The FAB sits 16pt above the bottom safe-area inset.
- **Dynamic Type**: Notes is the most Dynamic-Type-friendly Apple app. Apply `allowFontScaling={true}` (default) on every text element except tag chip labels and toolbar labels.
- **Note body line-height**: 1.5 × fontSize is non-negotiable for the body — this is the calmness anchor of the app.
- **Reduce Motion**: check `AccessibilityInfo.isReduceMotionEnabled()` — when true, set checklist fill `withTiming` duration to 0, skip the FAB scale, and disable the folder glyph tilt.
- **Folder glyph**: render via `react-native-svg` `Path`. For pixel-perfect rendering on retina, ensure `vector-effect="non-scaling-stroke"` is set on the stroke (mostly handled automatically by SVG).
- **Rich text editing**: React Native's `TextInput` doesn't support inline rich text. For production, use `react-native-rich-editor` or a `WebView`-based editor (e.g., `expo-quill`). Apply the body styles via CSS-in-WebView.
- **Text selection highlight color**: iOS uses the system selection highlight, which is configurable on UITextView but not on RN `TextInput`. The custom yellow highlight `#FFEB78` works only in a WebView-based rich text editor.
- **Dark mode**: `useColorScheme()` swaps the token object. Notes Orange `#F09A38` and Folder Yellow `#F5D773` stay identical across light and dark — they are brand assets.
