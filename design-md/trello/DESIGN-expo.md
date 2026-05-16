# Trello (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Trello's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `react-native-reanimated` v3, and `react-native-gesture-handler` for drag.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Action & board
  action:        '#0C66E4',
  actionPressed: '#0055CC',
  actionTint:    '#E9F2FF',
  boardBlue:     '#0079BF', // default backdrop

  // Surfaces & text
  card:          '#FFFFFF',
  list:          '#F1F2F4',
  sunken:        '#EBECF0',
  divider:       '#DFE1E6',
  border:        '#C1C7D0',
  textPrimary:   '#172B4D',
  textSecondary: '#5E6C84',
  textTertiary:  '#8993A4',

  // Card label palette (functional)
  labelGreen:  '#4BCE97',
  labelYellow: '#F5CD47',
  labelOrange: '#FEA362',
  labelRed:    '#F87168',
  labelPurple: '#9F8FEF',
  labelBlue:   '#579DFF',

  // Semantic
  success: '#1F845A',
  dueSoon: '#B65C02',
  overdue: '#C9372C',
} as const;

// Trello's signature navy-tinted shadow color
export const SHADOW_NAVY = 'rgba(9,30,66,1)'; // apply with shadowOpacity

export type TrelloColor = keyof typeof colors;
```

## 2. Typography

Trello uses the system font deliberately. RN's default `System` resolves to SF Pro on iOS; Inter is the closest substitute if you bundle a webfont.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#172B4D' } satisfies TextStyle;

export const typography = {
  sheetTitle: { ...base, fontWeight: '700', fontSize: 24, lineHeight: 29, letterSpacing: -0.3 },
  boardName:  { color: '#FFFFFF', fontWeight: '700', fontSize: 20, lineHeight: 24, letterSpacing: -0.2 },
  section:    { ...base, fontWeight: '700', fontSize: 17, lineHeight: 22, letterSpacing: -0.1 },
  listTitle:  { ...base, fontWeight: '600', fontSize: 15, lineHeight: 20 },
  cardTitle:  { ...base, fontWeight: '600', fontSize: 15, lineHeight: 20 },
  body:       { ...base, fontWeight: '400', fontSize: 15, lineHeight: 22 },
  button:     { color: '#FFFFFF', fontWeight: '600', fontSize: 16, lineHeight: 20 },
  badge:      { fontWeight: '600', fontSize: 12, lineHeight: 14, color: '#5E6C84' },
  subtitle:   { fontWeight: '400', fontSize: 13, lineHeight: 17, color: '#5E6C84' },
  labelUpper: { ...base, fontWeight: '700', fontSize: 11, lineHeight: 13, letterSpacing: 0.4, textTransform: 'uppercase' as const },
  composer:   { ...base, fontWeight: '400', fontSize: 15, lineHeight: 21 },
  caption:    { fontWeight: '400', fontSize: 11, lineHeight: 14, color: '#5E6C84' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Card (with drag lift)

```tsx
// components/Card.tsx
import { Text, View } from 'react-native';
import Animated, { useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function Card({
  title, labels, due, checklist, isDragging,
}: { title: string; labels: string[]; due?: string; checklist?: string; isDragging?: boolean }) {
  const style = useAnimatedStyle(() => ({
    transform: [{ scale: withSpring(isDragging ? 1.03 : 1, { damping: 16 }) }],
    shadowColor: SHADOW_NAVY_BASE,
    shadowOpacity: isDragging ? 0.25 : 0.13,
    shadowRadius: isDragging ? 24 : 1,
    shadowOffset: { width: 0, height: isDragging ? 12 : 1 },
    elevation: isDragging ? 12 : 1,
  }));

  return (
    <Animated.View style={[{ backgroundColor: colors.card, borderRadius: 8, paddingHorizontal: 12, paddingVertical: 8, gap: 8 }, style]}>
      {labels.length > 0 && (
        <View style={{ flexDirection: 'row', gap: 4 }}>
          {labels.map((c, i) => (
            <View key={i} style={{ width: 36, height: 8, borderRadius: 4, backgroundColor: c }} />
          ))}
        </View>
      )}
      <Text style={typography.cardTitle} numberOfLines={4}>{title}</Text>
      {(due || checklist) && (
        <View style={{ flexDirection: 'row', gap: 8 }}>
          {due && (
            <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
              <Ionicons name="time-outline" size={12} color={colors.dueSoon} />
              <Text style={[typography.badge, { color: colors.dueSoon }]}>{due}</Text>
            </View>
          )}
          {checklist && (
            <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
              <Ionicons name="checkbox-outline" size={12} color={colors.textSecondary} />
              <Text style={typography.badge}>{checklist}</Text>
            </View>
          )}
        </View>
      )}
    </Animated.View>
  );
}

const SHADOW_NAVY_BASE = '#091E42';
```

### List / Column

```tsx
// components/List.tsx
import { useState } from 'react';
import { Pressable, ScrollView, Text, TextInput, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { Card } from './Card';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function List({ title, cards }: { title: string; cards: any[] }) {
  const [composing, setComposing] = useState(false);
  const [draft, setDraft] = useState('');

  return (
    <View style={{ width: 272, backgroundColor: colors.list, borderRadius: 12,
      shadowColor: '#091E42', shadowOpacity: 0.08, shadowRadius: 2, shadowOffset: { width: 0, height: 1 } }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', paddingHorizontal: 8, paddingTop: 8, gap: 6 }}>
        <Text style={typography.listTitle}>{title}</Text>
        <Text style={typography.badge}>{cards.length}</Text>
        <View style={{ flex: 1 }} />
        <Ionicons name="ellipsis-horizontal" size={16} color={colors.textSecondary} />
      </View>

      <ScrollView showsVerticalScrollIndicator={false} style={{ maxHeight: 520 }} contentContainerStyle={{ padding: 8, gap: 8 }}>
        {cards.map((c, i) => (
          <Card key={i} title={c.title} labels={c.labels} due={c.due} checklist={c.checklist} />
        ))}
      </ScrollView>

      {composing ? (
        <View style={{ padding: 8, gap: 8 }}>
          <TextInput
            value={draft}
            onChangeText={setDraft}
            placeholder="Enter a title for this card…"
            placeholderTextColor={colors.textSecondary}
            multiline
            style={[typography.composer, { backgroundColor: colors.card, borderColor: colors.action, borderWidth: 2, borderRadius: 8, padding: 8 }]}
          />
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
            <Pressable style={{ backgroundColor: colors.action, borderRadius: 8, paddingHorizontal: 16, height: 36, justifyContent: 'center' }}>
              <Text style={typography.button}>Add card</Text>
            </Pressable>
            <Pressable onPress={() => setComposing(false)}>
              <Ionicons name="close" size={20} color={colors.textSecondary} />
            </Pressable>
          </View>
        </View>
      ) : (
        <Pressable onPress={() => setComposing(true)} style={{ flexDirection: 'row', alignItems: 'center', gap: 8, padding: 12 }}>
          <Ionicons name="add" size={16} color={colors.textSecondary} />
          <Text style={[typography.body, { color: colors.textSecondary }]}>Add a card</Text>
        </Pressable>
      )}
    </View>
  );
}
```

### Board (horizontal scroll, backdrop)

```tsx
// components/Board.tsx
import { ScrollView, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { List } from './List';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function Board({ boardName, lists, backdrop = colors.boardBlue }: { boardName: string; lists: any[]; backdrop?: string }) {
  return (
    <View style={{ flex: 1, backgroundColor: backdrop }}>
      {/* If a photo backdrop, layer <View style={{ ...StyleSheet.absoluteFillObject, backgroundColor: 'rgba(0,0,0,0.16)' }} /> */}
      <View style={{ flexDirection: 'row', alignItems: 'center', height: 44, paddingHorizontal: 12, gap: 12 }}>
        <Ionicons name="chevron-back" size={20} color="#FFF" />
        <Text style={typography.boardName}>{boardName}</Text>
        <View style={{ flex: 1 }} />
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, backgroundColor: 'rgba(255,255,255,0.16)', borderRadius: 8, paddingHorizontal: 12, height: 32 }}>
          <Ionicons name="filter" size={14} color="#FFF" />
          <Text style={[typography.badge, { color: '#FFF' }]}>Filter</Text>
        </View>
        <Ionicons name="ellipsis-horizontal" size={20} color="#FFF" />
      </View>

      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ padding: 8, gap: 8, alignItems: 'flex-start' }}>
        {lists.map((l, i) => (
          <List key={i} title={l.title} cards={l.cards} />
        ))}
        <View style={{ width: 272, height: 44, backgroundColor: 'rgba(255,255,255,0.16)', borderRadius: 12, justifyContent: 'center', paddingHorizontal: 12 }}>
          <Text style={[typography.body, { color: '#FFF' }]}>+ Add list</Text>
        </View>
      </ScrollView>
    </View>
  );
}
```

### Card Detail Sheet

```tsx
// Use a bottom-sheet lib (e.g. @gorhom/bottom-sheet) presented over a dimmed board.
import { ScrollView, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CardDetailSheet({ title, progress }: { title: string; progress: number }) {
  return (
    <ScrollView style={{ backgroundColor: colors.card }} contentContainerStyle={{ padding: 16, gap: 20 }}>
      <Text style={typography.sheetTitle}>{title}</Text>
      <Text style={typography.labelUpper}>Checklist</Text>
      <View style={{ height: 6, borderRadius: 3, backgroundColor: colors.sunken, overflow: 'hidden' }}>
        <View style={{ width: `${progress * 100}%`, height: '100%', backgroundColor: colors.success }} />
      </View>
      <Text style={typography.labelUpper}>Activity</Text>
      {/* comment rows */}
    </ScrollView>
  );
}
```

## 4. Drag-and-Drop Lift

```tsx
// With react-native-gesture-handler + Reanimated: on long-press, set isDragging.
// Visual contract: scale 1.03 + navy Level-3 shadow + a placeholder gap in the list.
import { View } from 'react-native';
import { colors } from '../theme/colors';

export function DropPlaceholder() {
  return <View style={{ height: 48, borderRadius: 8, backgroundColor: 'rgba(9,30,66,0.14)' }} />;
}
```

## 5. Navigation (no bottom tab)

```tsx
// Trello has NO bottom tab bar. The board is the primary surface.
// expo-router: a stack; boards list -> push a board that owns the screen.
// app/_layout.tsx
import { Stack } from 'expo-router';
export default function Layout() {
  return (
    <Stack screenOptions={{ headerShown: false }}>
      <Stack.Screen name="index" />          {/* Boards / Home */}
      <Stack.Screen name="board/[id]" />     {/* Board owns the screen, scrolls horizontally */}
    </Stack>
  );
}
// Do NOT add expo-router Tabs — there is no bottom tab bar in Trello.
```

## 6. Motion

```tsx
// Card lift on drag — Card: scale withSpring(1.03) + shadow ramp, ~220ms settle

// Card drop — withSpring settle, shadow returns to rest values

// Card detail present — bottom-sheet slide ~300ms over a rgba(0,0,0,0.4) dim

// Composer expand
import { LayoutAnimation } from 'react-native';
const openComposer = () => { LayoutAnimation.configureNext(LayoutAnimation.Presets.easeInEaseOut); setComposing(true); };

// Checklist progress: animate width with withTiming(200)
import { useSharedValue, withTiming } from 'react-native-reanimated';
```

Haptics: `Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium)` when a card is picked up; `Haptics.selectionAsync()` when it snaps into a new slot.

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Trello's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Back | `chevron-back` |
| Board overflow | `ellipsis-horizontal` |
| Filter (on board) | `filter` |
| Search | `search` |
| Add card / list | `add` |
| Close composer | `close` |
| Due date badge | `time-outline` |
| Checklist badge | `checkbox-outline` |
| Comment badge | `chatbubble-outline` |
| Member | `person-circle` |
| Attachment | `attach` |
| Move card | `arrow-redo-outline` |
| Checklist item | `checkbox` / `square-outline` |

## 8. Platform Notes

- **No bottom tab bar**: do not add `expo-router` `Tabs` — Trello navigates via the top bar and the horizontal board. The board is the primary scrollable surface
- **Backdrop owns the screen**: the board background (solid `#0079BF`, gradient, or an `ImageBackground`) fills the whole board area; layer a `rgba(0,0,0,0.16)` scrim over photos so white lists/cards keep contrast
- **Status bar**: `<StatusBar style="light" />` from `expo-status-bar` while a colored board is shown (white header content); switch to `dark` on white list/settings screens
- **Safe area**: wrap with `SafeAreaView`; the board scrolls beneath the on-board header which respects the notch/Island; no tab bar to clear at the bottom
- **Navy-tinted shadows**: always use `shadowColor: '#091E42'` (Trello navy), never pure black, so cards read on both colored backdrops and pale lists
- **Drag performance**: drive the lift with Reanimated shared values on the UI thread; keep the placeholder a cheap solid `rgba(9,30,66,0.14)` block
- **Dynamic Type**: RN respects font scaling; cards grow taller to fit. Set `allowFontScaling={false}` on badges and label chips where layout is rigid
- **Accessibility**: a card's `accessibilityLabel` should read "Card, <title>, 2 labels, due tomorrow, checklist 3 of 8"; expose "Move card" as an `accessibilityActions` entry since drag is hard for screen readers; never rely on label color alone — show label text in expanded mode
