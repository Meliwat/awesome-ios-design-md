# Zomato (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Zomato's visual language into paste-ready Expo / React Native code: a design-token module, the semantic rating pill, the veg/non-veg mark, the bordered ADD button, and the Delivery/Dining toggle.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand (interactive)
  zomatoRed:        '#E23744',
  zomatoRedPressed: '#C42531',

  // Rating scale (semantic)
  ratingGreen:  '#267E3E',
  ratingAmber:  '#DB7C38',
  ratingRed:    '#E23744', // = brand
  ratingGrey:   '#9C9C9C',

  // Membership & dining
  gold:         '#EFC75E',
  diningBlue:   '#256FEF',

  // Surfaces (light)
  canvas:       '#FFFFFF',
  surface1:     '#F4F4F5',
  surface2:     '#EBEBEC',
  divider:      '#E8E8E8',

  // Surfaces (dark)
  darkCanvas:   '#121212',
  darkSurface1: '#1C1C1E',
  darkSurface2: '#262629',
  darkDivider:  '#2C2C2E',

  // Text
  textPrimary:    '#1C1C1C',
  textSecondary:  '#696969',
  textTertiary:   '#9C9C9C',
  darkTextPrimary:'#F2F2F2',

  // Veg / non-veg
  vegGreen:     '#267E3E',
  nonVegMaroon: '#B91C1C',

  // Semantic
  error:        '#E03A3A',
} as const;

export type ZomatoColor = keyof typeof colors;

// Semantic rating → fill color
export function ratingFill(score: number): string {
  if (score >= 4.0) return colors.ratingGreen;
  if (score >= 3.0) return colors.ratingAmber;
  if (score > 0)    return colors.ratingRed;
  return colors.ratingGrey;
}
```

## 2. Typography

Zomato's brand face is a custom Okra-family grotesque; the recommended free substitute is **Inter**. Load Inter via `expo-font`. Prices/ratings use tabular figures via `fontVariant`.

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
    'Inter-Black':     require('../assets/fonts/Inter-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#1C1C1C' } satisfies TextStyle;
const tabular = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  screenTitle:    { ...primary, fontFamily: 'Inter-Black',     fontSize: 32, lineHeight: 37, letterSpacing: -0.5 },
  restaurantHero: { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 24, lineHeight: 29, letterSpacing: -0.4 },
  section:        { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 20, lineHeight: 26, letterSpacing: -0.3 },
  menuCategory:   { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 17, lineHeight: 22, letterSpacing: -0.2 },
  itemName:       { ...primary, fontFamily: 'Inter-Bold',      fontSize: 15, lineHeight: 20 },
  body:           { color: '#696969', fontFamily: 'Inter-Medium', fontSize: 14, lineHeight: 20 },
  price:          { ...primary, fontFamily: 'Inter-Bold',      fontSize: 14, lineHeight: 18, ...tabular },
  meta:           { color: '#696969', fontFamily: 'Inter-Regular', fontSize: 13, lineHeight: 18 },
  ratingPill:     { color: '#FFFFFF', fontFamily: 'Inter-ExtraBold', fontSize: 14, lineHeight: 14, ...tabular },
  ratingCaption:  { color: '#696969', fontFamily: 'Inter-Regular', fontSize: 11, lineHeight: 14, textDecorationLine: 'underline' as const },
  tag:            { color: '#DB7C38', fontFamily: 'Inter-ExtraBold', fontSize: 11, lineHeight: 11, letterSpacing: 0.4 },
  button:         { color: '#FFFFFF', fontFamily: 'Inter-ExtraBold', fontSize: 16, lineHeight: 16 },
  addLabel:       { color: '#E23744', fontFamily: 'Inter-ExtraBold', fontSize: 14, lineHeight: 14, letterSpacing: 0.3 },
  tab:            { color: '#888888', fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  caption:        { color: '#696969', fontFamily: 'Inter-Medium', fontSize: 12, lineHeight: 16 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Semantic Rating Pill

```tsx
// components/RatingPill.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, ratingFill } from '../theme/colors';
import { typography } from '../theme/typography';

export function RatingPill({ score, count }: { score: number; count?: string }) {
  return (
    <View style={{ alignItems: 'center', gap: 5 }}>
      <View style={{
        flexDirection: 'row', alignItems: 'center', gap: 4,
        backgroundColor: ratingFill(score), borderRadius: 7,
        paddingHorizontal: 9, paddingVertical: 4,
      }}>
        <Ionicons name="star" size={11} color="#FFFFFF" />
        <Text style={typography.ratingPill}>{score.toFixed(1)}</Text>
      </View>
      {count ? <Text style={typography.ratingCaption}>{count}</Text> : null}
    </View>
  );
}
```

### Veg / Non-Veg Mark

```tsx
// components/VegMark.tsx
import { View } from 'react-native';
import { colors } from '../theme/colors';

export function VegMark({ isVeg }: { isVeg: boolean }) {
  const c = isVeg ? colors.vegGreen : colors.nonVegMaroon;
  return (
    <View style={{
      width: 16, height: 16, borderRadius: 3,
      borderWidth: 1.5, borderColor: c,
      alignItems: 'center', justifyContent: 'center',
    }}>
      {isVeg ? (
        <View style={{ width: 7, height: 7, borderRadius: 4, backgroundColor: c }} />
      ) : (
        // maroon triangle via borders
        <View style={{
          width: 0, height: 0,
          borderLeftWidth: 5, borderRightWidth: 5, borderBottomWidth: 8,
          borderLeftColor: 'transparent', borderRightColor: 'transparent', borderBottomColor: c,
        }} />
      )}
    </View>
  );
}
```

### Menu Item Row (bordered ADD → stepper)

```tsx
// components/MenuItemRow.tsx
import { useState } from 'react';
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { VegMark } from './VegMark';

export function MenuItemRow({
  isVeg, bestseller, name, price, rating, ratingCount, imageUri,
}: {
  isVeg: boolean; bestseller?: boolean; name: string; price: string;
  rating: number; ratingCount: number; imageUri: string;
}) {
  const [qty, setQty] = useState(0);
  const tick = (n: number) => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); setQty(n); };

  return (
    <View style={{
      flexDirection: 'row', alignItems: 'flex-start', gap: 14,
      paddingVertical: 14, borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      <View style={{ flex: 1 }}>
        <VegMark isVeg={isVeg} />
        {bestseller ? (
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginTop: 7, marginBottom: 3 }}>
            <Ionicons name="star" size={9} color={colors.ratingAmber} />
            <Text style={typography.tag}>Bestseller</Text>
          </View>
        ) : null}
        <Text style={[typography.itemName, !bestseller && { marginTop: 7 }]}>{name}</Text>
        <Text style={[typography.price, { marginTop: 5 }]}>{price}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginTop: 6 }}>
          <Ionicons name="star" size={10} color={colors.ratingGreen} />
          <Text style={typography.caption}>{`${rating.toFixed(1)} (${ratingCount})`}</Text>
        </View>
      </View>

      <View style={{ width: 96, alignItems: 'center' }}>
        <Image source={{ uri: imageUri }} style={{ width: 96, height: 90, borderRadius: 12 }} resizeMode="cover" />
        <View style={{
          marginTop: -16, backgroundColor: colors.canvas,
          borderWidth: 1, borderColor: colors.zomatoRed, borderRadius: 10,
          shadowColor: '#000', shadowOpacity: 0.12, shadowRadius: 8, shadowOffset: { width: 0, height: 3 },
          elevation: 3,
        }}>
          {qty === 0 ? (
            <Pressable onPress={() => setQty(1)} style={{ paddingHorizontal: 22, paddingVertical: 8 }}>
              <Text style={typography.addLabel}>ADD</Text>
            </Pressable>
          ) : (
            <View style={{ flexDirection: 'row', alignItems: 'center' }}>
              <Pressable onPress={() => qty > 1 ? tick(qty - 1) : setQty(0)}
                style={{ width: 36, height: 36, alignItems: 'center', justifyContent: 'center' }}>
                <Ionicons name="remove" size={14} color={colors.zomatoRed} />
              </Pressable>
              <Text style={[typography.itemName, { color: colors.zomatoRed, minWidth: 24, textAlign: 'center' }]}>{qty}</Text>
              <Pressable onPress={() => tick(qty + 1)}
                style={{ width: 36, height: 36, alignItems: 'center', justifyContent: 'center' }}>
                <Ionicons name="add" size={14} color={colors.zomatoRed} />
              </Pressable>
            </View>
          )}
        </View>
      </View>
    </View>
  );
}
```

### Delivery / Dining Out Toggle

```tsx
// components/DeliveryDiningToggle.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';

export function DeliveryDiningToggle({
  isDelivery, onChange,
}: { isDelivery: boolean; onChange: (delivery: boolean) => void }) {
  const Seg = ({ label, active, onPress }: { label: string; active: boolean; onPress: () => void }) => (
    <Pressable onPress={onPress} style={{
      flex: 1, paddingVertical: 9, borderRadius: 999, alignItems: 'center',
      backgroundColor: active ? colors.zomatoRed : 'transparent',
    }}>
      <Text style={{
        fontFamily: 'Inter-Bold', fontSize: 13,
        color: active ? '#FFFFFF' : colors.textSecondary,
      }}>{label}</Text>
    </Pressable>
  );
  return (
    <View style={{ flexDirection: 'row', backgroundColor: colors.surface1, borderRadius: 999, padding: 4 }}>
      <Seg label="Delivery"   active={isDelivery}  onPress={() => onChange(true)} />
      <Seg label="Dining Out" active={!isDelivery} onPress={() => onChange(false)} />
    </View>
  );
}
```

### Restaurant Detail Header (hero + pull-up card)

```tsx
// components/RestaurantDetailHeader.tsx
import { Image, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { RatingPill } from './RatingPill';

export function RestaurantDetailHeader({
  imageUri, name, meta, deliveryRating, diningRating,
}: {
  imageUri: string; name: string; meta: string;
  deliveryRating: number; diningRating: number;
}) {
  const HeroBtn = ({ icon }: { icon: keyof typeof Ionicons.glyphMap }) => (
    <View style={{
      width: 34, height: 34, borderRadius: 17,
      backgroundColor: 'rgba(0,0,0,0.4)', alignItems: 'center', justifyContent: 'center',
    }}>
      <Ionicons name={icon} size={17} color="#FFFFFF" />
    </View>
  );
  return (
    <View>
      <View style={{ height: 200 }}>
        <Image source={{ uri: imageUri }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
        <View style={{
          position: 'absolute', top: 14, left: 16, right: 16,
          flexDirection: 'row', alignItems: 'center',
        }}>
          <HeroBtn icon="chevron-back" />
          <View style={{ marginLeft: 'auto', flexDirection: 'row', gap: 10 }}>
            <HeroBtn icon="search" />
            <HeroBtn icon="heart-outline" />
          </View>
        </View>
      </View>
      <View style={{
        marginTop: -22, backgroundColor: colors.canvas,
        borderTopLeftRadius: 22, borderTopRightRadius: 22,
        paddingHorizontal: 18, paddingTop: 20,
      }}>
        <Text style={typography.restaurantHero}>{name}</Text>
        <Text style={[typography.meta, { marginTop: 5 }]}>{meta}</Text>
        <View style={{ flexDirection: 'row', gap: 18, marginTop: 14 }}>
          <RatingPill score={deliveryRating} count="12.4K Delivery" />
          <RatingPill score={diningRating} count="3.1K Dining" />
        </View>
      </View>
    </View>
  );
}
```

### Primary Button

```tsx
// components/ZButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ZButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        minHeight: 52, borderRadius: 12, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.zomatoRedPressed : colors.zomatoRed,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
// Embeds price: <ZButton title="Add item · ₹320" onPress={...} />
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
        tabBarActiveTintColor:  colors.zomatoRed,
        tabBarInactiveTintColor: '#888888',
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Delivery', tabBarIcon: ({ color }) => <Ionicons name="home"     size={22} color={color} /> }} />
      <Tabs.Screen name="dining"  options={{ title: 'Dining',   tabBarIcon: ({ color }) => <Ionicons name="restaurant" size={22} color={color} /> }} />
      <Tabs.Screen name="live"    options={{ title: 'Live',     tabBarIcon: ({ color }) => <Ionicons name="pulse"    size={22} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// ADD → stepper morph (in place), cart slides up
import { withSpring, withTiming } from 'react-native-reanimated';
addWidth.value = withSpring(stepperWidth, { damping: 14 });
cartY.value = withTiming(0, { duration: 280 });

// Quantity stepper — Haptics + numeric crossfade
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Delivery/Dining toggle — sliding red fill
// Animate the highlight x with withSpring(target, { damping: 16, stiffness: 180 }); content crossfade 200ms

// Hero pull-up parallax
// Animated.ScrollView + useAnimatedScrollHandler → hero translateY = scrollY * 0.5

// Card → detail: shared element via expo-router + react-native-shared-element (320ms)

// Order tracking stage advance
// stageIndex bump + Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium)
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The veg/non-veg mark is drawn with views (not an icon). For Pro/Gold use `MaterialCommunityIcons` `crown`.

| Purpose | Ionicons |
|---------|----------|
| Delivery (tab) | `home` |
| Dining (tab) | `restaurant` |
| Live (tab) | `pulse` |
| Profile (tab) | `person-circle-outline` |
| Rating star | `star` |
| Hero back | `chevron-back` |
| Hero search | `search` |
| Hero save | `heart-outline` / `heart` |
| ADD minus | `remove` |
| ADD plus | `add` |
| Share | `share-outline` |
| Dining — book | `calendar-outline` |
| Offer | `pricetag` |
| Pro / Gold | MCI `crown` |
| Rider (tracking) | `bicycle` |

## 7. Platform Notes

- **Font choice**: Zomato's brand face (Okra family) is proprietary — ship **Inter** (SIL OFL). Bundle Regular → Black; the app runs heavy (ExtraBold/Black for names and titles)
- **Semantic rating**: always derive the pill fill from `ratingFill(score)` — never hard-code one color; the color carrying the score's quality is the core of Zomato
- **Veg/non-veg is not color-only**: pair the mark with an accessible label ("Vegetarian"/"Non-vegetarian"); legally significant in India
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on ratings, prices, review counts and quantity counters
- **Status bar**: `<StatusBar style="dark" />` on light mode; `"light"` over the hero photo (with a top gradient scrim) and on dark
- **Safe area**: wrap screens in `SafeAreaView`; the hero nav scrims need top safe-area padding, the bottom tab + sticky cart bar need bottom padding
- **Dynamic Type**: React Native respects system scale on `<Text>`; set `allowFontScaling={false}` on the rating pill, veg/non-veg mark, Bestseller tag, tab labels and the ADD label
- **ADD button**: render inside the thumbnail column with negative `marginTop` so it overlaps the photo; give it `elevation: 3` + iOS shadow; morph it to the stepper in place
- **Hero parallax**: use `Animated.ScrollView` with `useAnimatedScrollHandler`; translate the hero at 0.5x and fade the nav from transparent to solid as the hero scrolls away
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` / `darkDivider`; keep brand red, the rating scale, veg colors, gold and dining blue constant; never dim food imagery
- **Maps**: use `react-native-maps` for the rider tracking map; Zomato-Red route + rider marker
- **Accessibility**: the rating pill announces score + quality; the menu row is one accessible element combining veg status, name, price and rating; the ADD button is `accessibilityRole="button"`
