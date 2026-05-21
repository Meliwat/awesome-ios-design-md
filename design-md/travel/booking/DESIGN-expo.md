# Booking.com (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Booking.com's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets — including the signature review-score badge and the dense property card.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:      '#FFFFFF',
  surface:     '#F2F2F2',
  surfaceDeep: '#E6E6E6',
  divider:     '#E0E0E0',

  textPrimary:   '#1A1A1A',
  textSecondary: '#6B6B6B',
  textTertiary:  '#949494',

  blue:        '#003580',  // brand / structure
  cta:         '#0071C2',  // action
  ctaPressed:  '#005A9C',
  blueTint:    '#E7F0F7',
  yellow:      '#FEBB02',  // star rating only

  success: '#008009',
  deal:    '#CC0000',
  warning: '#F5A623',
} as const;

export type BKColor = keyof typeof colors;
```

## 2. Typography

Booking.com uses a BookingSans-equivalent grotesque — substitute Inter. Load via `expo-font`; fall back to `System`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const ink = { color: '#1A1A1A' } satisfies TextStyle;
const tnum = ['tabular-nums'] as const;

export const typography = {
  screenTitle: { ...ink, fontFamily: 'Inter-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  section:     { ...ink, fontFamily: 'Inter-Bold',     fontSize: 18, lineHeight: 23, letterSpacing: -0.2 },
  propName:    { ...ink, fontFamily: 'Inter-Bold',     fontSize: 17, lineHeight: 21, letterSpacing: -0.1 },
  price:       { ...ink, fontFamily: 'Inter-Bold',     fontSize: 17, lineHeight: 20, fontVariant: tnum },
  scoreNum:    { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 15, lineHeight: 18, fontVariant: tnum },
  body:        { ...ink, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 22 },
  button:      { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 20 },
  scoreWord:   { ...ink, fontFamily: 'Inter-Bold',     fontSize: 14, lineHeight: 17 },
  meta:        { fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 17, color: '#6B6B6B' },
  tag:         { fontFamily: 'Inter-SemiBold', fontSize: 12, lineHeight: 14, letterSpacing: 0.1 },
  tab:         { fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 13, letterSpacing: 0.1 },
  caption:     { fontFamily: 'Inter-Regular',  fontSize: 11, lineHeight: 14, color: '#6B6B6B' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Review-Score Badge

```tsx
// components/ReviewScoreBadge.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

function wordFor(score: number) {
  if (score >= 9) return 'Fabulous';
  if (score >= 8) return 'Very good';
  if (score >= 7) return 'Good';
  return 'Pleasant';
}

export function ReviewScoreBadge({ score, reviews }: { score: number; reviews: number }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
      <View style={{ backgroundColor: colors.blue, borderRadius: 5,
                      paddingHorizontal: 7, paddingVertical: 4 }}>
        <Text style={typography.scoreNum}>{score.toFixed(1)}</Text>{/* always one decimal */}
      </View>
      <Text style={typography.scoreWord}>{wordFor(score)}</Text>
      <Text style={[typography.tag, { color: colors.textSecondary, fontFamily: 'Inter-Regular' }]}>
        · {reviews.toLocaleString()} reviews
      </Text>
    </View>
  );
}
```

### Property Card (dense, border-defined)

```tsx
// components/PropertyCard.tsx
import { Image, Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { ReviewScoreBadge } from './ReviewScoreBadge';

export function PropertyCard({
  photoUri, name, area, distance, score, reviews, originalPrice, price, scarcity, onPress,
}: {
  photoUri: string; name: string; area: string; distance: string;
  score: number; reviews: number; originalPrice?: string; price: string;
  scarcity?: string; onPress: () => void;
}) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', gap: 12, padding: 12, borderRadius: 8,
        backgroundColor: colors.canvas, borderWidth: 1, borderColor: colors.divider,
        transform: [{ scale: pressed ? 0.99 : 1 }],
      })}>
      <Image source={{ uri: photoUri }}
        style={{ width: 120, height: 120, borderRadius: 8, backgroundColor: colors.surface }} />
      <View style={{ flex: 1, gap: 4 }}>
        <Text style={typography.propName} numberOfLines={2}>{name}</Text>
        <View style={{ flexDirection: 'row', gap: 6 }}>
          <Text style={[typography.meta, { color: colors.cta }]}>{area}</Text>
          <Text style={typography.meta}>{distance}</Text>
        </View>
        <Text style={[typography.meta, { color: colors.success }]}>Free cancellation</Text>
        <ReviewScoreBadge score={score} reviews={reviews} />
        <View style={{ flex: 1 }} />
        <View style={{ alignItems: 'flex-end' }}>
          {originalPrice && (
            <Text style={[typography.meta, { color: colors.textTertiary,
              textDecorationLine: 'line-through' }]}>{originalPrice}</Text>
          )}
          <Text style={typography.price}>{price}</Text>
          <Text style={typography.caption}>Includes taxes and fees</Text>
          {scarcity && <Text style={[typography.tag, { color: colors.deal }]}>{scarcity}</Text>}
        </View>
      </View>
    </Pressable>
  );
}
```

### Primary CTA

```tsx
// components/BookingCTA.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BookingCTA({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        height: 52, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.ctaPressed : colors.cta,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={typography.button}>{label}</Text>
    </Pressable>
  );
}
```

### Search Form Card

```tsx
// components/SearchFormCard.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { BookingCTA } from './BookingCTA';

function Row({ icon, text }: { icon: any; text: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, height: 52 }}>
      <Ionicons name={icon} size={18} color={colors.textSecondary} style={{ width: 22 }} />
      <Text style={typography.body}>{text}</Text>
    </View>
  );
}

export function SearchFormCard() {
  return (
    <View style={{ padding: 12, borderRadius: 8, backgroundColor: colors.canvas,
      shadowColor: colors.textPrimary, shadowOpacity: 0.08, shadowRadius: 6,
      shadowOffset: { width: 0, height: 2 } }}>
      <Row icon="location-outline" text="Where are you going?" />
      <View style={{ height: 1, backgroundColor: colors.divider }} />
      <Row icon="calendar-outline" text="Fri 12 Jul — Sun 14 Jul" />
      <View style={{ height: 1, backgroundColor: colors.divider }} />
      <Row icon="person-outline" text="2 adults · 0 children · 1 room" />
      <View style={{ height: 12 }} />
      <BookingCTA label="Search" onPress={() => {}} />
    </View>
  );
}
```

### Genius Banner

```tsx
// components/GeniusBanner.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function GeniusBanner() {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', padding: 16,
                    borderRadius: 8, backgroundColor: colors.blue }}>
      <View style={{ flex: 1 }}>
        <Text style={[typography.propName, { color: '#fff' }]}>Genius</Text>
        <Text style={[typography.meta, { color: 'rgba(255,255,255,0.9)' }]}>
          You're a Genius level 1 member — enjoy 10% off
        </Text>
      </View>
      <View style={{ backgroundColor: colors.yellow, borderRadius: 500,
        paddingHorizontal: 8, paddingVertical: 4 }}>
        <Text style={[typography.tag, { color: colors.blue }]}>10% off</Text>
      </View>
    </View>
  );
}
```

### Filter Chip

```tsx
// components/FilterChip.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FilterChip({
  label, selected, onPress,
}: { label: string; selected: boolean; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        paddingHorizontal: 16, paddingVertical: 8, borderRadius: 500,
        backgroundColor: selected ? colors.blueTint : colors.canvas,
        borderWidth: selected ? 1.5 : 1,
        borderColor: selected ? colors.cta : colors.divider,
      }}>
      <Text style={[typography.tag, {
        color: selected ? colors.cta : colors.textPrimary,
        fontFamily: selected ? 'Inter-Bold' : 'Inter-SemiBold',
      }]}>{label}</Text>
    </Pressable>
  );
}
```

## 4. Map Toggle + Price Pin

```tsx
// A "Map"/"List" pill toggle floats above the list. On the map, render rounded
// price-bubble pins; the selected pin recolors to colors.blue with white text and
// scales 1.12. Tapping a pin slides a single <PropertyCard> up from the bottom
// (an Animated.View with SlideInDown, or @gorhom/bottom-sheet at a low snap point).
import Animated, { withSpring } from 'react-native-reanimated';
// pinScale.value = withSpring(selected ? 1.12 : 1, { damping: 14 });
```

## 5. Tab Bar

The top bar is solid Booking Blue (set per-screen via the navigator header). The bottom tabs are white with a CTA-blue active tint.

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerStyle: { backgroundColor: colors.blue },
        headerTintColor: '#FFFFFF',
        headerTitleStyle: { fontFamily: 'Inter-Bold', fontSize: 16 },
        tabBarActiveTintColor:   colors.cta,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopColor: colors.divider, borderTopWidth: 0.5 },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search"          size={24} color={color} /> }} />
      <Tabs.Screen name="saved"    options={{ title: 'Saved',    tabBarIcon: ({ color }) => <Ionicons name="heart-outline"   size={24} color={color} /> }} />
      <Tabs.Screen name="bookings" options={{ title: 'Bookings', tabBarIcon: ({ color }) => <Ionicons name="briefcase-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-outline"  size={24} color={color} /> }} />
      <Tabs.Screen name="help"     options={{ title: 'Help',     tabBarIcon: ({ color }) => <Ionicons name="help-circle-outline" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Card tap: transform scale 0.99 via Pressable pressed state

// Filter chip toggle: ~150ms — animate border/background with withTiming on `selected`

// Map pin select: pinScale.value = withSpring(selected ? 1.12 : 1, { damping: 14 });
// then property card entering={SlideInDown.duration(300)}

// CTA press: transform scale 0.98 via pressed state

// Score badge: NO animation — consistency is the brand

// Sheet present (calendar / guests): expo-router modal or @gorhom/bottom-sheet; scrim rgba(26,26,26,0.45)
```

## 7. Icon Library

Use `@expo/vector-icons`. Map to Booking.com's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Search (tab) | `search` |
| Saved (tab) | `heart-outline` / `heart` |
| Bookings (tab) | `briefcase-outline` / `briefcase` |
| Profile (tab) | `person-outline` / `person` |
| Help (tab) | `help-circle-outline` |
| Destination | `location-outline` |
| Dates | `calendar-outline` |
| Guests | `person-outline` / `people-outline` |
| Map toggle | `map-outline` |
| Filter | `options-outline` |
| Star rating | `star` |
| Back | `chevron-back` |
| Stepper minus / plus | `remove` / `add` |

## 8. Platform Notes

- **Light-only feel**: the dense card marketplace depends on white — do not auto-invert; if you mirror system dark, keep both blues and the yellow identical
- **Status bar**: Set `<StatusBar style="light" />` from `expo-status-bar` — the navy top app bar wants light content; switch to `dark` on white-background screens without the navy bar
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the navy header respects the top inset, the tab bar and the map's floating card clear the home indicator
- **Tabular figures**: pass `fontVariant: ['tabular-nums']` on prices, review scores, and scarcity numbers so columns/badges align
- **Score formatting**: always render the score via `score.toFixed(1)` — exactly one decimal (`8.9`), never an integer or two decimals
- **Dynamic Type**: RN respects font scaling; set `allowFontScaling={false}` on the score-badge number and tab labels where layout breaks; let names and body scale
- **Accessibility**: combine the badge into one node — `accessible accessibilityLabel="Review score 8.9, Fabulous, 1,284 reviews"`; cards announce name → score → price → scarcity; filter chips use `accessibilityState={{ selected }}`; do not rely on the navy color alone for the score meaning
