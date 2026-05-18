# OpenTable (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates OpenTable's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceGray:    '#F6F6F7',
  surfaceRaised:  '#FBFBFC',
  surfacePressed: '#ECECEE',
  divider:        '#E4E4E7',

  // Surfaces (dark)
  darkCanvas:     '#121212',
  darkSurface1:   '#1C1C1E',
  darkSurface2:   '#262629',
  darkDivider:    '#2E2E31',

  // Text
  textPrimary:    '#1A1A1C',
  textSecondary:  '#6B6B70',
  textTertiary:   '#9A9A9F',
  darkTextPrimary:'#EDEDED',
  darkTextSecondary:'#A2A2A6',

  // Brand
  red:        '#DA3743',
  redBright:  '#F2545B',
  redPressed: '#B92C37',
  redTint:    '#FCEBEC',

  // Functional accents
  goldStar:   '#E8A33D',
  pointsTeal: '#1F8A8A',
  dinerGreen: '#2FA86A',
  notifyAmber:'#E08A2F',

  // Semantic
  success:    '#2FA86A',
  error:      '#D6342F',
  errorDark:  '#F2545B',
} as const;

export type OpenTableColor = keyof typeof colors;
```

## 2. Typography

OpenTable uses a custom grotesque sans ("OpenTable Sans"); Inter is the closest free analog. Load Inter via `expo-font`.

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
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#1A1A1C' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 38, letterSpacing: -0.5 },
  restName:    { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 26, lineHeight: 31, letterSpacing: -0.4 },
  section:     { ...primary, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 29, letterSpacing: -0.3 },
  cardTitle:   { ...primary, fontFamily: 'Inter-Bold',      fontSize: 18, lineHeight: 23 },
  body:        { ...primary, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 24 },
  listSub:     { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 20 },
  meta:        { color: '#6B6B70', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 19 },
  slotTime:    { ...primary, fontFamily: 'Inter-Bold',      fontSize: 14, lineHeight: 17 },
  pointsTag:   { color: '#1F8A8A', fontFamily: 'Inter-Bold', fontSize: 12, lineHeight: 14, letterSpacing: 0.3 },
  button:      { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 18 },
  pill:        { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 13, lineHeight: 16 },
  tab:         { fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
  caption:     { color: '#6B6B70', fontFamily: 'Inter-Medium', fontSize: 12, lineHeight: 17 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Reservation Time-Slot Grid

```tsx
// components/SlotGrid.tsx
import { View, Text, Pressable } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type Slot = { id: string; time: string; points?: number; recommended?: boolean };

export function SlotGrid({ slots, selectedId, onSelect }: {
  slots: Slot[]; selectedId: string | null; onSelect: (id: string) => void;
}) {
  return (
    <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 9 }}>
      {slots.map((s) => {
        const sel = s.id === selectedId || (selectedId == null && s.recommended);
        return (
          <Pressable
            key={s.id}
            onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onSelect(s.id); }}
            style={({ pressed }) => ({
              flexGrow: 1, minWidth: 64, height: 52, borderRadius: 10,
              alignItems: 'center', justifyContent: 'center',
              backgroundColor: sel ? colors.red : colors.surfaceGray,
              borderWidth: sel ? 0 : 1, borderColor: colors.divider,
              transform: [{ scale: pressed ? 0.97 : 1 }],
            })}
          >
            <Text style={[typography.slotTime, { color: sel ? '#FFFFFF' : colors.textPrimary }]}>{s.time}</Text>
            {s.points ? (
              <Text style={{ fontFamily: 'Inter-Bold', fontSize: 9,
                color: sel ? 'rgba(255,255,255,0.85)' : colors.pointsTeal }}>
                {`+${s.points.toLocaleString()}`}
              </Text>
            ) : null}
          </Pressable>
        );
      })}
    </View>
  );
}
```

### Star Rating

```tsx
// components/StarRating.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StarRating({ rating, count }: { rating: number; count: number }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
      <View style={{ flexDirection: 'row', gap: 1 }}>
        {[0, 1, 2, 3, 4].map((i) => (
          <Ionicons
            key={i}
            name={i + 0.5 < rating ? 'star' : i < rating ? 'star-half' : 'star-outline'}
            size={13} color={colors.goldStar}
          />
        ))}
      </View>
      <Text style={[typography.meta, { color: colors.textPrimary, fontFamily: 'Inter-SemiBold' }]}>
        {rating.toFixed(1)}
      </Text>
      <Text style={typography.meta}>{`(${count})`}</Text>
    </View>
  );
}
```

### Social-Proof Pill

```tsx
// components/BookedPill.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';

export function BookedPill({ count }: { count: number }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5, alignSelf: 'flex-start',
                   paddingVertical: 4, paddingHorizontal: 10, borderRadius: 999,
                   backgroundColor: 'rgba(47,168,106,0.16)' }}>
      <Text>🔥</Text>
      <Text style={{ fontFamily: 'Inter-Bold', fontSize: 11, color: colors.dinerGreen }}>
        {`Booked ${count} times today`}
      </Text>
    </View>
  );
}
```

### Party-Size / Date Selector Chip

```tsx
// components/SelectorChip.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SelectorChip({ icon, label, onPress }: {
  icon: any; label: string; onPress: () => void;
}) {
  return (
    <Pressable onPress={onPress}
      style={{ flex: 1, height: 40, borderRadius: 999, flexDirection: 'row',
               alignItems: 'center', justifyContent: 'center', gap: 6,
               backgroundColor: colors.surfaceGray, borderWidth: 1, borderColor: colors.divider }}>
      <Ionicons name={icon} size={14} color={colors.textSecondary} />
      <Text style={typography.pill}>{label}</Text>
    </Pressable>
  );
}
```

### Sticky Reserve Bar

```tsx
// components/ReserveBar.tsx
import { View, Text, Pressable } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ReserveBar({ timeLabel, onPress }: { timeLabel: string; onPress: () => void }) {
  return (
    <View style={{ paddingHorizontal: 16, paddingVertical: 12, backgroundColor: colors.canvas,
                   borderTopWidth: 0.5, borderTopColor: colors.divider,
                   shadowColor: '#000', shadowOpacity: 0.10, shadowRadius: 16, shadowOffset: { width: 0, height: -2 } }}>
      <Pressable
        onPress={() => { Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); onPress(); }}
        style={({ pressed }) => ({
          height: 50, borderRadius: 999, alignItems: 'center', justifyContent: 'center',
          backgroundColor: pressed ? colors.redPressed : colors.red,
        })}>
        <Text style={typography.button}>{`Reserve for ${timeLabel}`}</Text>
      </Pressable>
    </View>
  );
}
```

### Points / Rewards Chip

```tsx
// components/PointsChip.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function PointsChip({ text }: { text: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 7, alignSelf: 'flex-start',
                   paddingVertical: 7, paddingHorizontal: 14, borderRadius: 999,
                   backgroundColor: 'rgba(31,138,138,0.16)', borderWidth: 1, borderColor: 'rgba(31,138,138,0.4)' }}>
      <Ionicons name="star" size={12} color={colors.pointsTeal} />
      <Text style={{ fontFamily: 'Inter-Bold', fontSize: 13, color: colors.pointsTeal }}>{text}</Text>
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
        tabBarActiveTintColor:  colors.red,      // redBright in dark mode
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"        options={{ title: 'Discover',     tabBarIcon: ({ color }) => <Ionicons name="search"          size={21} color={color} /> }} />
      <Tabs.Screen name="saved"        options={{ title: 'Saved',        tabBarIcon: ({ color }) => <Ionicons name="heart"           size={21} color={color} /> }} />
      <Tabs.Screen name="reservations" options={{ title: 'Reservations', tabBarIcon: ({ color }) => <Ionicons name="calendar"        size={21} color={color} /> }} />
      <Tabs.Screen name="rewards"      options={{ title: 'Rewards',      tabBarIcon: ({ color }) => <Ionicons name="star"            size={21} color={color} /> }} />
      <Tabs.Screen name="profile"      options={{ title: 'Profile',      tabBarIcon: ({ color }) => <Ionicons name="person-circle"   size={21} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Slot select — scale 0.97 bounce on press + fill to red + soft haptic
// transform: [{ scale: pressed ? 0.97 : 1 }] + Haptics.impactAsync(Soft)
// updates the ReserveBar label

// Slot grid load — staggered fade-in + 4pt slide-up
import Animated, { FadeInDown } from 'react-native-reanimated';
// entering={FadeInDown.duration(200).delay(index * 30)}

// Filter chip toggle — withTiming border/fill 150ms

// Restaurant detail open — expo-router shared transition or FadeIn on hero
import Animated, { FadeIn } from 'react-native-reanimated';

// Party/date sheet — @gorhom/bottom-sheet, snapPoints ['45%']

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);                 // slot select, filter, stepper
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);   // reservation confirmed
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Discover | `search` |
| Saved | `heart` / `heart-outline` |
| Reservations | `calendar` |
| Rewards | `star` |
| Profile | `person-circle` |
| Back | `chevron-back` |
| Save (heart) | `heart` / `heart-outline` |
| Share | `share-outline` |
| Review star | `star` / `star-half` / `star-outline` |
| Points | `star` |
| Party size | `people` |
| Date | `calendar` |
| Time | `time` |
| Notify / waitlist | `notifications-outline` |
| Location | `location` |
| Menu | `restaurant-outline` |
| Confirmed | `checkmark-circle` |

## 7. Platform Notes

- **Font choice**: OpenTable uses a custom "OpenTable Sans"; ship Inter (SIL OFL) as the free analog — confident grotesque sans with heavy weights for names/CTAs
- **Status bar**: `<StatusBar style="dark" />` on light mode, `"light"` on dark; over the full-bleed restaurant hero photo use `"light"` with a top scrim
- **Safe area**: wrap screens in `SafeAreaView`; the sticky Reserve bar needs `paddingBottom: insets.bottom`; the tab bar respects the home indicator
- **Dynamic Type**: React Native respects system scale on `<Text>`; set `allowFontScaling={false}` on tab labels, points tags, slot-time text (the slot grid is layout-sensitive — let chips wrap)
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1`; switch the action color to `redBright` so the Reserve CTA reads on `#121212`
- **Image-heavy**: use `expo-image` with `contentFit="cover"` and `transition` for restaurant photos; cache aggressively
- **Keyboard**: `KeyboardAvoidingView` on search; party/date pickers are bottom sheets that sit above the keyboard
- **Accessibility**: slot chips are accessible buttons ("{time}, earns {points} points, select"); the Reserve bar announces "Reserve a table for {time}"; star rating reads "{rating} stars, {count} reviews"; the booked pill is announced for trust context
