# Resy (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Resy's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. Resy is dark-first — the canvas is pure black.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & Surfaces (dark — primary brand)
  black:      '#000000',
  surface1:   '#121212',
  surface2:   '#1C1C1C',
  surface3:   '#242424',
  divider:    '#262626',

  // Canvas & Surfaces (light — secondary inversion)
  canvasLight:    '#FFFFFF',
  surfaceGray:    '#F4F4F4',
  surfacePressed: '#EAEAEA',
  dividerLight:   '#E2E2E2',

  // Text
  textPrimary:     '#F5F5F5',
  textSecondary:   '#9E9E9E',
  textTertiary:    '#6A6A6A',
  textPrimaryLt:   '#101010',
  textSecondaryLt: '#6B6B6B',

  // Brand
  red:        '#C73E3A',
  redBright:  '#E2504B',
  redPressed: '#A8302D',

  // Functional accents
  notifyAmber: '#D99A2B',
  hitGold:     '#C9A24B',
  confirmed:   '#4FA773',

  // Semantic
  error:      '#E2504B',
} as const;

export type ResyColor = keyof typeof colors;
```

## 2. Typography

Resy pairs a high-contrast display serif (Playfair Display, the closest free analog to Resy's brand serif) with Inter for UI. Load both via `expo-font`. The serif/sans split is the brand.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'PlayfairDisplay-Bold':      require('../assets/fonts/PlayfairDisplay-Bold.ttf'),
    'PlayfairDisplay-ExtraBold': require('../assets/fonts/PlayfairDisplay-ExtraBold.ttf'),
    'PlayfairDisplay-Italic':    require('../assets/fonts/PlayfairDisplay-Italic.ttf'),
    'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':    require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':      require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#F5F5F5' } satisfies TextStyle;

export const typography = {
  displayTitle: { ...primary, fontFamily: 'PlayfairDisplay-ExtraBold', fontSize: 34, lineHeight: 39, letterSpacing: -0.5 },
  restName:     { ...primary, fontFamily: 'PlayfairDisplay-Bold',      fontSize: 27, lineHeight: 31, letterSpacing: -0.3 },
  editorial:    { color: '#9E9E9E', fontFamily: 'PlayfairDisplay-Italic', fontSize: 15, lineHeight: 23, fontStyle: 'italic' as const },
  section:      { ...primary, fontFamily: 'Inter-Bold',     fontSize: 22, lineHeight: 29, letterSpacing: -0.2 },
  cardTitle:    { ...primary, fontFamily: 'Inter-SemiBold', fontSize: 18, lineHeight: 23 },
  body:         { ...primary, fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 24 },
  meta:         { color: '#9E9E9E', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 19 },
  slotTime:     { ...primary, fontFamily: 'Inter-Bold',     fontSize: 14, lineHeight: 17 },
  seating:      { color: '#9E9E9E', fontFamily: 'Inter-SemiBold', fontSize: 9, lineHeight: 11, letterSpacing: 0.3 },
  eyebrow:      { color: '#9E9E9E', fontFamily: 'Inter-Bold', fontSize: 12, lineHeight: 14, letterSpacing: 0.8 },
  button:       { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 18, letterSpacing: 0.2 },
  tab:          { fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
  caption:      { color: '#9E9E9E', fontFamily: 'Inter-Medium', fontSize: 12, lineHeight: 17 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Reservation Slot Grid (red-outline system)

```tsx
// components/SlotGrid.tsx
import { View, Text, Pressable } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type SlotKind = 'available' | 'primary' | 'notify';
export type Slot = { id: string; label: string; seating: string; kind: SlotKind };

export function SlotGrid({ slots, selectedId, onSelect }: {
  slots: Slot[]; selectedId: string | null; onSelect: (id: string) => void;
}) {
  return (
    <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 9 }}>
      {slots.map((s) => {
        const isSel = s.id === selectedId;
        const solid = s.kind === 'primary' || (isSel && s.kind === 'available');
        const notify = s.kind === 'notify';
        return (
          <Pressable
            key={s.id}
            disabled={notify ? false : false}
            onPress={() => { if (!notify) { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onSelect(s.id); } }}
            style={({ pressed }) => ({
              flexGrow: 1, minWidth: 70, height: 56, borderRadius: 10,
              alignItems: 'center', justifyContent: 'center', gap: 3,
              backgroundColor: solid ? colors.red : 'transparent',
              borderWidth: solid ? 0 : 1,
              borderStyle: notify ? 'dashed' : 'solid',
              borderColor: solid ? 'transparent' : notify ? colors.divider : colors.red,
              transform: [{ scale: pressed && !notify ? 0.97 : 1 }],
            })}
          >
            <Text style={[
              notify ? { ...typography.slotTime, fontSize: 12 } : typography.slotTime,
              { color: solid ? '#FFFFFF' : notify ? colors.notifyAmber : colors.textPrimary },
            ]}>{s.label}</Text>
            <Text style={[typography.seating, {
              color: solid ? 'rgba(255,255,255,0.82)' : notify ? colors.notifyAmber : colors.textSecondary,
            }]}>{s.seating.toUpperCase()}</Text>
          </Pressable>
        );
      })}
    </View>
  );
}
```

### Reservation Date Strip

```tsx
// components/DateStrip.tsx
import { ScrollView, View, Text, Pressable } from 'react-native';
import { colors } from '../theme/colors';

export type DateCell = { id: string; dow: string; num: string };

export function DateStrip({ days, selectedId, onSelect }: {
  days: DateCell[]; selectedId: string | null; onSelect: (id: string) => void;
}) {
  return (
    <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ gap: 8 }}>
      {days.map((d) => {
        const sel = d.id === selectedId;
        return (
          <Pressable key={d.id} onPress={() => onSelect(d.id)}
            style={{ minWidth: 56, height: 60, borderRadius: 10, alignItems: 'center', justifyContent: 'center', gap: 3,
                     backgroundColor: sel ? colors.red : colors.surface1,
                     borderWidth: 1, borderColor: sel ? colors.red : colors.divider }}>
            <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.5,
                           color: sel ? '#FFFFFF' : colors.textSecondary }}>
              {d.dow.toUpperCase()}
            </Text>
            <Text style={{ fontFamily: 'Inter-Bold', fontSize: 17, color: sel ? '#FFFFFF' : colors.textPrimary }}>
              {d.num}
            </Text>
          </Pressable>
        );
      })}
    </ScrollView>
  );
}
```

### Editorial Restaurant Header

```tsx
// components/RestaurantHeader.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RestaurantHeader({ name, rating, meta, tagline }: {
  name: string; rating: string; meta: string; tagline: string;
}) {
  return (
    <View>
      <Text style={typography.restName}>{name}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, marginTop: 9, flexWrap: 'wrap' }}>
        <Text style={{ fontFamily: 'Inter-Bold', fontSize: 13, color: colors.hitGold }}>{`★ ${rating}`}</Text>
        <Text style={{ color: colors.textTertiary }}>·</Text>
        <Text style={typography.meta}>{meta}</Text>
      </View>
      <Text style={[typography.editorial, { marginTop: 12 }]}>{tagline}</Text>
    </View>
  );
}
```

### Notify Chip

```tsx
// components/NotifyChip.tsx
import { Pressable, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function NotifyChip({ onList, setOnList }: { onList: boolean; setOnList: (b: boolean) => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); setOnList(!onList); }}
      style={{ flexDirection: 'row', alignItems: 'center', gap: 7, alignSelf: 'flex-start',
               paddingVertical: 8, paddingHorizontal: 16, borderRadius: 999,
               backgroundColor: onList ? colors.notifyAmber : 'transparent',
               borderWidth: onList ? 0 : 1, borderColor: colors.notifyAmber }}>
      <Ionicons name="notifications" size={12} color={onList ? '#FFFFFF' : colors.notifyAmber} />
      <Text style={{ fontFamily: 'Inter-Bold', fontSize: 13, color: onList ? '#FFFFFF' : colors.notifyAmber }}>
        {onList ? 'On the Notify list' : 'Notify when available'}
      </Text>
    </Pressable>
  );
}
```

### Primary Button

```tsx
// components/ResyPrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ResyPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); onPress(); }}
      style={({ pressed }) => ({
        borderRadius: 8, paddingVertical: 16, alignItems: 'center',
        backgroundColor: pressed ? colors.redPressed : colors.red,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={typography.button}>{title}</Text>
    </Pressable>
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
        tabBarActiveTintColor:  colors.redBright,   // bright red for contrast on pure black
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.black, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"        options={{ title: 'Search',       tabBarIcon: ({ color }) => <Ionicons name="search"        size={21} color={color} /> }} />
      <Tabs.Screen name="hitlist"      options={{ title: 'Hit List',     tabBarIcon: ({ color }) => <Ionicons name="bookmark"      size={21} color={color} /> }} />
      <Tabs.Screen name="reservations" options={{ title: 'Reservations', tabBarIcon: ({ color }) => <Ionicons name="calendar"      size={21} color={color} /> }} />
      <Tabs.Screen name="notify"       options={{ title: 'Notify',       tabBarIcon: ({ color }) => <Ionicons name="notifications" size={21} color={color} /> }} />
      <Tabs.Screen name="account"      options={{ title: 'Account',      tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={21} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Slot select — outlined → solid red on press + soft haptic; Book button updates
// transform: [{ scale: pressed ? 0.97 : 1 }] + Haptics.impactAsync(Soft)

// Date strip — fill red 150ms; slot grid cross-fades to that day
import Animated, { FadeIn } from 'react-native-reanimated';

// Slot grid load — staggered fade-in + 4pt slide-up
import Animated, { FadeInDown } from 'react-native-reanimated';
// entering={FadeInDown.duration(200).delay(index * 30)}

// Notify toggle — dashed → amber fill 150ms + haptic

// Restaurant detail open — expo-router shared transition or FadeIn on hero carousel

// Guest / calendar sheet — @gorhom/bottom-sheet, snapPoints ['50%']

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);                 // slot/date/Notify/guest select
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);   // reservation confirmed
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Search | `search` |
| Hit List | `bookmark` / `bookmark-outline` |
| Reservations | `calendar` |
| Notify | `notifications` / `notifications-outline` |
| Account | `person-circle` |
| Back | `chevron-back` |
| Hit List (save) | `heart` / `heart-outline` |
| Share | `share-outline` |
| Rating star | `star` |
| Notify bell | `notifications` |
| Guests | `people` |
| Date | `calendar` |
| Daypart | `sunny` / `moon` |
| Confirmed | `checkmark-circle` |
| Menu | `restaurant-outline` |
| Location | `location` |
| Carousel page | `ellipse` (dots) |

## 7. Platform Notes

- **Font choice**: Resy's brand voice is a high-contrast display serif; ship Playfair Display (SIL OFL) + Inter (SIL OFL) — the serif/sans split is the brand, not optional
- **Dark-first**: Resy's canvas is pure black `#000000`. Set the navigation theme and `<StatusBar style="light" />` globally; the light inversion (white canvas, `#101010` text) is secondary
- **Safe area**: wrap screens in `SafeAreaView`; the tab bar and any sticky Book button respect the home indicator; over the full-bleed hero carousel keep `StatusBar` light with a top scrim
- **Dynamic Type**: React Native respects system scale on `<Text>`; set `allowFontScaling={false}` on tab labels, seating labels, slot-time text (the slot grid is layout-sensitive — let chips wrap)
- **No shadows on black**: drop-shadows are invisible on `#000000` — use a 0.5–1pt `divider` border on sheets/floating panels (`@gorhom/bottom-sheet` `backgroundStyle` + `handleIndicatorStyle`) as the elevation cue
- **Image-heavy**: use `expo-image` with `contentFit="cover"` and `transition` for the hero photo carousel; cache aggressively — photography carries the editorial feel
- **Keyboard**: `KeyboardAvoidingView` on search; guest/calendar pickers are bottom sheets above the keyboard
- **Accessibility**: slot chips are accessible buttons ("{time}, {seating}, available, select" / "primary slot" / "sold out, Notify me"); the Notify chip toggles list membership; the rating reads "{n} out of 10"; serif restaurant names get an explicit `accessibilityRole="header"`
