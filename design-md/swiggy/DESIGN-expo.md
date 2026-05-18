# Swiggy (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Swiggy's visual language into paste-ready Expo / React Native code: a design-token module, themed components (photo-forward restaurant card, dish row with the floating ADD button, rating chip, floating cart bar), and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-image`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  swiggyOrange:        '#FC8019',
  swiggyOrangePressed: '#E06D0C',
  swiggyOrangeTint:    'rgba(252,128,25,0.10)',

  // Rating & trust
  ratingGreen: '#48C479',
  ratingAmber: '#DB7C38',
  ratingRed:   '#E84A4A',

  // Vertical accents
  instamartPurple: '#982C61',
  dineoutRed:      '#D7385E',

  // Surfaces (light)
  canvas:     '#FFFFFF',
  surface1L:  '#F4F4F6',
  surface2L:  '#ECECEE',
  dividerL:   '#E2E2E7',

  // Surfaces (dark)
  darkCanvas:   '#121212',
  darkSurface1: '#1C1C1E',
  darkSurface2: '#2A2A2A',
  darkDivider:  '#2C2C2E',

  // Text
  textPrimaryL:   '#02060C',
  textSecondaryL: '#686B78',
  textTertiaryL:  '#93959F',
  textPrimaryD:   '#F2F2F2',
  textSecondaryD: '#A0A0A0',

  // Semantic
  error: '#E84A4A',
  info:  '#1A73E8',
} as const;

export type SwiggyColor = keyof typeof colors;

// Per-vertical accent (same layout grammar, swapped accent)
export const verticalAccent = {
  food:      colors.swiggyOrange,
  instamart: colors.instamartPurple,
  dineout:   colors.dineoutRed,
} as const;

export function ratingColor(score: number) {
  if (score >= 4.0) return colors.ratingGreen;
  if (score >= 3.0) return colors.ratingAmber;
  return colors.ratingRed;
}
```

## 2. Typography

Load the ProximaNova-lineage display face and a Basis-Grotesque-style UI face via `expo-font`; fall back to system. Use tabular numerals for prices, ETAs, ratings.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'ProximaNova-Bold':        require('../assets/fonts/ProximaNova-Bold.ttf'),
    'ProximaNova-Semibold':    require('../assets/fonts/ProximaNova-Semibold.ttf'),
    'ProximaNova-Regular':     require('../assets/fonts/ProximaNova-Regular.ttf'),
    'BasisGrotesquePro-Bold':    require('../assets/fonts/BasisGrotesquePro-Bold.ttf'),
    'BasisGrotesquePro-Medium':  require('../assets/fonts/BasisGrotesquePro-Medium.ttf'),
    'BasisGrotesquePro-Regular': require('../assets/fonts/BasisGrotesquePro-Regular.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#F2F2F2' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  screenTitle: { ...primary, fontFamily: 'ProximaNova-Bold', fontSize: 32, lineHeight: 38, letterSpacing: -0.4 },
  hero:        { ...primary, fontFamily: 'ProximaNova-Bold', fontSize: 26, lineHeight: 33, letterSpacing: -0.3 },
  section:     { ...primary, fontFamily: 'ProximaNova-Bold', fontSize: 22, lineHeight: 29, letterSpacing: -0.2 },
  cardTitle:   { ...primary, fontFamily: 'ProximaNova-Bold', fontSize: 18, lineHeight: 23, letterSpacing: -0.3 },
  subsection:  { ...primary, fontFamily: 'ProximaNova-Bold', fontSize: 17, lineHeight: 23, letterSpacing: -0.2 },
  location:    { ...primary, fontFamily: 'ProximaNova-Bold', fontSize: 17, lineHeight: 20, letterSpacing: -0.2 },
  button:      { color: '#FFFFFF', fontFamily: 'ProximaNova-Bold', fontSize: 15, lineHeight: 15, letterSpacing: 0.2, textTransform: 'uppercase' as const },
  add:         { color: '#48C479', fontFamily: 'ProximaNova-Bold', fontSize: 13, lineHeight: 13, letterSpacing: 0.3, textTransform: 'uppercase' as const },
  badge:       { color: '#FFFFFF', fontFamily: 'ProximaNova-Bold', fontSize: 12, lineHeight: 13, letterSpacing: 0.3, textTransform: 'uppercase' as const },
  body:        { ...primary, fontFamily: 'BasisGrotesquePro-Regular', fontSize: 16, lineHeight: 24 },
  dishTitle:   { ...primary, fontFamily: 'BasisGrotesquePro-Medium', fontSize: 15, lineHeight: 21 },
  price:       { ...primary, ...tnum, fontFamily: 'BasisGrotesquePro-Medium', fontSize: 14, lineHeight: 20 },
  meta:        { color: '#A0A0A0', fontFamily: 'BasisGrotesquePro-Regular', fontSize: 14, lineHeight: 20 },
  rating:      { ...tnum, fontFamily: 'BasisGrotesquePro-Bold', fontSize: 12, lineHeight: 12, color: '#FFFFFF' },
  caption:     { color: '#A0A0A0', fontFamily: 'BasisGrotesquePro-Regular', fontSize: 12, lineHeight: 17 },
  tab:         { color: '#A0A0A0', fontFamily: 'BasisGrotesquePro-Medium', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Rating Chip

```tsx
// components/RatingChip.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, ratingColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function RatingChip({ score }: { score: number }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 3,
      paddingVertical: 2, paddingHorizontal: 6,
      borderRadius: 6, backgroundColor: ratingColor(score),
    }}>
      <Ionicons name="star" size={9} color="#FFFFFF" />
      <Text style={typography.rating}>{score.toFixed(1)}</Text>
    </View>
  );
}
```

### Veg / Non-Veg Mark

```tsx
// components/DietMark.tsx
import { View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function DietMark({ isVeg }: { isVeg: boolean }) {
  const tint = isVeg ? colors.ratingGreen : colors.error;
  return (
    <View style={{
      width: 16, height: 16, borderRadius: 3,
      borderWidth: 1.5, borderColor: tint,
      alignItems: 'center', justifyContent: 'center',
    }}>
      {isVeg
        ? <View style={{ width: 7, height: 7, borderRadius: 4, backgroundColor: tint }} />
        : <Ionicons name="triangle" size={8} color={tint} />}
    </View>
  );
}
```

### Photo-Forward Restaurant Card

```tsx
// components/RestaurantCard.tsx
import { Text, View } from 'react-native';
import { Image } from 'expo-image';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { RatingChip } from './RatingChip';

type Props = {
  imageUri: string; offerTitle: string; offerSub: string; name: string;
  rating: number; eta: string; cuisines: string; locality: string;
  footer: string; isAd?: boolean;
};

export function RestaurantCard(p: Props) {
  return (
    <View style={{ marginBottom: 22 }}>
      <View style={{ borderRadius: 16, overflow: 'hidden', aspectRatio: 16 / 10 }}>
        <Image source={{ uri: p.imageUri }} style={{ flex: 1 }} contentFit="cover" />
        <LinearGradient
          colors={['transparent', 'rgba(0,0,0,0.78)']}
          locations={[0.45, 1]}
          style={{ position: 'absolute', inset: 0 }}
        />
        <View style={{ position: 'absolute', left: 12, bottom: 10 }}>
          <Text style={[typography.cardTitle, { color: '#FFFFFF' }]}>{p.offerTitle}</Text>
          <Text style={[typography.badge, { opacity: 0.92 }]}>{p.offerSub}</Text>
        </View>
        {p.isAd && (
          <View style={{ position: 'absolute', top: 12, left: 12, backgroundColor: 'rgba(0,0,0,0.4)', borderRadius: 5, paddingVertical: 3, paddingHorizontal: 7 }}>
            <Text style={{ color: '#FFF', fontSize: 10, fontFamily: 'ProximaNova-Bold' }}>AD</Text>
          </View>
        )}
        <View style={{ position: 'absolute', top: 12, right: 12, width: 32, height: 32, borderRadius: 16, backgroundColor: 'rgba(0,0,0,0.4)', alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name="heart-outline" size={16} color="#FFF" />
        </View>
      </View>

      <Text style={[typography.cardTitle, { marginTop: 10 }]}>{p.name}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, marginTop: 6 }}>
        <RatingChip score={p.rating} />
        <Text style={{ color: colors.textTertiaryL }}>·</Text>
        <Text style={typography.price}>{p.eta}</Text>
      </View>
      <Text style={[typography.meta, { marginTop: 4 }]}>{p.cuisines}</Text>
      <Text style={[typography.meta, { marginTop: 2 }]}>{p.locality}</Text>

      <View style={{ borderTopWidth: 1, borderStyle: 'dashed', borderColor: colors.darkDivider, marginTop: 10, paddingTop: 10, flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <Ionicons name="time-outline" size={13} color={colors.swiggyOrange} />
        <Text style={[typography.rating, { color: colors.swiggyOrange }]}>{p.footer}</Text>
      </View>
    </View>
  );
}
```

### Dish Row with Floating ADD Button

```tsx
// components/DishRow.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import { Image } from 'expo-image';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { DietMark } from './DietMark';

type Props = {
  isVeg: boolean; name: string; price: string; rating: number;
  ratingCount: string; desc: string; imageUri: string;
};

export function DishRow(p: Props) {
  const [qty, setQty] = useState(0);

  const addBox = {
    backgroundColor: colors.darkSurface2,
    borderWidth: 1, borderColor: colors.ratingGreen, borderRadius: 10,
    shadowColor: '#000', shadowOpacity: 0.5, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
    elevation: 6,
  } as const;

  return (
    <View style={{ flexDirection: 'row', gap: 14, paddingVertical: 16, borderBottomWidth: 1, borderStyle: 'dashed', borderColor: colors.darkDivider }}>
      <View style={{ flex: 1, gap: 6 }}>
        <DietMark isVeg={p.isVeg} />
        <Text style={typography.dishTitle}>{p.name}</Text>
        <Text style={typography.price}>{p.price}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 3 }}>
          <Ionicons name="star" size={10} color={colors.ratingGreen} />
          <Text style={[typography.rating, { color: colors.ratingGreen }]}>
            {p.rating.toFixed(1)} ({p.ratingCount})
          </Text>
        </View>
        <Text style={typography.caption} numberOfLines={2}>{p.desc}</Text>
      </View>

      <View style={{ width: 110, alignItems: 'center' }}>
        <Image source={{ uri: p.imageUri }} style={{ width: 110, height: 110, borderRadius: 14 }} contentFit="cover" />
        <View style={{ marginTop: -12 }}>
          {qty === 0 ? (
            <Pressable
              onPress={() => { setQty(1); Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); }}
              style={[{ paddingVertical: 7, paddingHorizontal: 26 }, addBox]}
            >
              <Text style={typography.add}>ADD</Text>
            </Pressable>
          ) : (
            <View style={[{ flexDirection: 'row', alignItems: 'center', gap: 14, paddingVertical: 7, paddingHorizontal: 14 }, addBox]}>
              <Pressable onPress={() => setQty(qty - 1)}><Text style={typography.add}>−</Text></Pressable>
              <Text style={typography.add}>{qty}</Text>
              <Pressable onPress={() => setQty(qty + 1)}><Text style={typography.add}>+</Text></Pressable>
            </View>
          )}
        </View>
      </View>
    </View>
  );
}
```

### Primary Button

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        borderRadius: 12,
        paddingVertical: 14,
        alignItems: 'center',
        backgroundColor: pressed ? colors.swiggyOrangePressed : colors.swiggyOrange,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Floating Cart Bar

```tsx
// components/CartBar.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { SlideInDown, SlideOutDown } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CartBar({ itemCount, total, onView }: { itemCount: number; total: string; onView: () => void }) {
  return (
    <Animated.View entering={SlideInDown.springify().damping(14)} exiting={SlideOutDown}
      style={{
        marginHorizontal: 12,
        backgroundColor: colors.swiggyOrange, borderRadius: 12,
        paddingHorizontal: 18, paddingVertical: 16,
        flexDirection: 'row', alignItems: 'center',
        shadowColor: colors.swiggyOrange, shadowOpacity: 0.35, shadowRadius: 16, shadowOffset: { width: 0, height: 6 },
        elevation: 8,
      }}>
      <Text style={[typography.cardTitle, { color: '#FFF' }]}>
        {itemCount} item{itemCount === 1 ? '' : 's'}  |  {total}
      </Text>
      <Pressable onPress={onView} style={{ marginLeft: 'auto', flexDirection: 'row', alignItems: 'center', gap: 4 }}>
        <Text style={[typography.add, { color: '#FFF' }]}>VIEW CART</Text>
        <Ionicons name="chevron-forward" size={12} color="#FFF" />
      </Pressable>
    </Animated.View>
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
        tabBarActiveTintColor:  colors.swiggyOrange,
        tabBarInactiveTintColor: '#888888',
        tabBarStyle: {
          backgroundColor: colors.darkCanvas,
          borderTopWidth: 0.5,
          borderTopColor: colors.darkDivider,
          height: 70,
        },
        tabBarLabelStyle: { fontFamily: 'BasisGrotesquePro-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Swiggy',    tabBarIcon: ({ color }) => <Ionicons name="home"        size={22} color={color} /> }} />
      <Tabs.Screen name="instamart" options={{ title: 'Instamart', tabBarIcon: ({ color }) => <Ionicons name="grid"        size={22} color={color} /> }} />
      <Tabs.Screen name="dineout"   options={{ title: 'Dineout',   tabBarIcon: ({ color }) => <Ionicons name="send"        size={22} color={color} /> }} />
      <Tabs.Screen name="cart"      options={{ title: 'Cart',      tabBarIcon: ({ color }) => <Ionicons name="cart"        size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// ADD → stepper morph (instant swap + soft haptic; optional 180ms layout fade)
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';
// wrap the ADD / stepper in <Animated.View entering={FadeIn.duration(180)} />
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Cart bar reveal (first item)
// entering={SlideInDown.springify().damping(14)} exiting={SlideOutDown}

// Restaurant card → detail
// expo-router shared transition OR Reanimated layout; 320ms ease

// Filter chip select — cross-fade fill 150ms
// useAnimatedStyle interpolating backgroundColor over 150ms

// Bottom sheet (offers / customise)
// @gorhom/bottom-sheet with snapPoints + 20px top radius

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);                  // ADD, chip toggle, pull-to-refresh
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);    // Place Order success
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). For the brand wordmark, bundle an SVG via `expo-image`.

| Purpose | Ionicons |
|---------|----------|
| Swiggy / Home | `home` / `home-outline` |
| Instamart | `grid` / `grid-outline` |
| Dineout | `send` / `send-outline` |
| Cart | `cart` / `cart-outline` |
| Location pin | `location` |
| Location chevron | `chevron-down` |
| Search | `search` |
| Voice search | `mic` |
| Rating star | `star` |
| Favourite | `heart-outline` / `heart` |
| Filter | `options` |
| Footer delivery time | `time-outline` |
| Free delivery | `bicycle` |
| Back | `chevron-back` |
| Share | `share-outline` |
| Diet triangle (non-veg) | `triangle` |
| Cart chevron | `chevron-forward` |
| Add / qty | `add` / `remove` |

## 7. Platform Notes

- **Fonts**: bundle the licensed ProximaNova-lineage and Basis-Grotesque-style faces; never rely on a download at runtime — fall back to `System` if absent
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on every price / ETA / rating so steppers don't shift layout
- **Status bar**: `<StatusBar style="light" />` (dark canvas); restaurant detail hero bleeds under it with the scrim
- **Safe area**: wrap screens in `SafeAreaView`; the floating cart bar sits above the tab bar, both above the home indicator — pad with `useSafeAreaInsets()`
- **expo-image** for all food imagery (memory-disk cache, `contentFit="cover"`, blurhash placeholders) — imagery is the product
- **Dynamic Type**: `<Text>` respects system scale; set `allowFontScaling={false}` on rating chips, offer badges, tab labels, the qty stepper, and veg marks (layout-critical)
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` / `darkSurface2`; never desaturate food images
- **Per-vertical theme**: provide `verticalAccent` via context so Instamart re-themes to `#982C61` and Dineout to `#D7385E` while reusing every card/list component
- **Accessibility**: label cards, rating chips, ADD buttons, and diet marks (see DESIGN.md §9 / SwiftUI §8); ADD/stepper need a 44pt hit area via `hitSlop`
- **Cart bar**: keep mounted but animate in/out; never let it cover the last list item — add bottom content padding equal to its height when visible
