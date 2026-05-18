# Deliveroo (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Deliveroo's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets for the restaurant feed and menu.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand (the ONE color)
  rooTeal:        '#00CCBC',
  rooTealPressed: '#00A99C',
  rooTealInk:     '#003733', // on-teal content color (NOT white)

  // Membership & promo
  plusMint:       '#C4F4EF',
  promoGold:      '#FFC100',

  // Surfaces (light)
  canvas:         '#FFFFFF',
  surface1:       '#F4F4F2',
  surface2:       '#EAEAE8',
  divider:        '#E8E8E6',

  // Surfaces (dark)
  darkCanvas:     '#121212',
  darkSurface1:   '#1C1C1E',
  darkSurface2:   '#262629',
  darkDivider:    '#2C2C2E',

  // Text
  textPrimary:    '#1D1D1B',
  textSecondary:  '#6B6B6B',
  textTertiary:   '#A0A0A0',
  darkTextPrimary:'#F4F4F2',

  // Semantic
  error:          '#E2483D',

  // Plus banner gradient (deep teal)
  plusBgFrom:     '#003E3A',
  plusBgTo:       '#00524B',
} as const;

export type DeliverooColor = keyof typeof colors;
```

## 2. Typography

Deliveroo's brand face is a custom heavy grotesque; the recommended free substitute is **Inter**. Load Inter via `expo-font`. Prices/fees use tabular figures via `fontVariant`.

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

const primary = { color: '#1D1D1B' } satisfies TextStyle;
const tabular = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  screenTitle:    { ...primary, fontFamily: 'Inter-Black',     fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  restaurantHero: { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 26, lineHeight: 31, letterSpacing: -0.4 },
  section:        { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 22, lineHeight: 28, letterSpacing: -0.3 },
  subsection:     { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 20, lineHeight: 26, letterSpacing: -0.2 },
  restaurantName: { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 16, lineHeight: 21 },
  menuName:       { ...primary, fontFamily: 'Inter-Bold',      fontSize: 15, lineHeight: 20 },
  body:           { ...primary, fontFamily: 'Inter-Medium',    fontSize: 15, lineHeight: 22 },
  price:          { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 14, lineHeight: 18, ...tabular },
  meta:           { color: '#6B6B6B', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 20 },
  feePill:        { ...primary, fontFamily: 'Inter-Bold',      fontSize: 13, lineHeight: 17 },
  badge:          { color: '#003733', fontFamily: 'Inter-ExtraBold', fontSize: 12, lineHeight: 12, letterSpacing: -0.1 },
  button:         { color: '#003733', fontFamily: 'Inter-ExtraBold', fontSize: 16, lineHeight: 16 },
  tab:            { color: '#888888', fontFamily: 'Inter-Bold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  caption:        { color: '#6B6B6B', fontFamily: 'Inter-SemiBold', fontSize: 12, lineHeight: 16 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Restaurant Card

```tsx
// components/RestaurantCard.tsx
import { useState } from 'react';
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RestaurantCard({
  name, meta, rating, fee, badge, badgeIsPromo, imageUri,
}: {
  name: string; meta: string; rating: number; fee: string;
  badge?: string; badgeIsPromo?: boolean; imageUri: string;
}) {
  const [saved, setSaved] = useState(false);
  return (
    <View style={{ marginBottom: 22 }}>
      <View style={{ borderRadius: 14, overflow: 'hidden', aspectRatio: 5 / 3 }}>
        <Image source={{ uri: imageUri }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
        {badge ? (
          <View style={{
            position: 'absolute', top: 10, left: 10, borderRadius: 6,
            paddingHorizontal: 9, paddingVertical: 4,
            backgroundColor: badgeIsPromo ? colors.promoGold : colors.rooTeal,
          }}>
            <Text style={[typography.badge, badgeIsPromo && { color: '#1A1206' }]}>{badge}</Text>
          </View>
        ) : null}
        <Pressable onPress={() => setSaved(s => !s)}
          style={{
            position: 'absolute', top: 10, right: 10,
            width: 32, height: 32, borderRadius: 16,
            backgroundColor: 'rgba(0,0,0,0.4)',
            alignItems: 'center', justifyContent: 'center',
          }}>
          <Ionicons name={saved ? 'heart' : 'heart-outline'} size={16}
            color={saved ? colors.rooTeal : '#FFFFFF'} />
        </Pressable>
      </View>

      <View style={{ flexDirection: 'row', alignItems: 'center', marginTop: 10 }}>
        <Text style={typography.restaurantName}>{name}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginLeft: 'auto' }}>
          <Ionicons name="star" size={13} color={colors.rooTeal} />
          <Text style={typography.feePill}>{rating.toFixed(1)}</Text>
        </View>
      </View>
      <Text style={[typography.meta, { marginTop: 4 }]}>{meta}</Text>
      <FeePill text={fee} />
    </View>
  );
}

function FeePill({ text }: { text: string }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 5, alignSelf: 'flex-start',
      marginTop: 8, paddingHorizontal: 10, paddingVertical: 5,
      borderRadius: 999, backgroundColor: colors.surface1,
    }}>
      <Ionicons name="bicycle" size={11} color={colors.rooTeal} />
      <Text style={typography.feePill}>{text}</Text>
    </View>
  );
}
```

### Menu Item Row (floating teal +)

```tsx
// components/MenuItemRow.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MenuItemRow({
  name, desc, price, imageUri, onAdd,
}: { name: string; desc: string; price: string; imageUri: string; onAdd: () => void }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'flex-start', gap: 14,
      paddingVertical: 14, borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      <View style={{ flex: 1 }}>
        <Text style={typography.menuName}>{name}</Text>
        <Text style={[typography.body, { color: colors.textSecondary, marginTop: 4 }]} numberOfLines={2}>{desc}</Text>
        <Text style={[typography.price, { marginTop: 4 }]}>{price}</Text>
      </View>
      <View style={{ width: 84, height: 84 }}>
        <Image source={{ uri: imageUri }} style={{ width: 84, height: 84, borderRadius: 12 }} resizeMode="cover" />
        <Pressable onPress={onAdd}
          style={{
            position: 'absolute', bottom: -10, right: -8,
            width: 28, height: 28, borderRadius: 14,
            backgroundColor: colors.rooTeal,
            alignItems: 'center', justifyContent: 'center',
            shadowColor: '#000', shadowOpacity: 0.18, shadowRadius: 3, shadowOffset: { width: 0, height: 2 },
            elevation: 3,
          }}>
          <Ionicons name="add" size={16} color={colors.rooTealInk} />
        </Pressable>
      </View>
    </View>
  );
}
```

### Quantity Stepper

```tsx
// components/QuantityStepper.tsx
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function QuantityStepper({ quantity, onChange }: { quantity: number; onChange: (q: number) => void }) {
  const tick = (n: number) => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onChange(n); };
  const Control = ({ name, onPress, dim }: { name: 'remove' | 'add'; onPress: () => void; dim?: boolean }) => (
    <Pressable onPress={onPress}
      style={{ width: 32, height: 32, borderRadius: 16, backgroundColor: colors.rooTeal,
               alignItems: 'center', justifyContent: 'center', opacity: dim ? 0.35 : 1 }}>
      <Ionicons name={name} size={16} color={colors.rooTealInk} />
    </Pressable>
  );
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 14, alignSelf: 'flex-start' }}>
      <Control name="remove" dim={quantity <= 1} onPress={() => quantity > 1 && tick(quantity - 1)} />
      <Text style={[typography.button, { color: colors.textPrimary, minWidth: 18, textAlign: 'center' }]}>{quantity}</Text>
      <Control name="add" onPress={() => tick(quantity + 1)} />
    </View>
  );
}
```

### Sticky Basket Bar

```tsx
// components/BasketBar.tsx
import { useEffect } from 'react';
import { Pressable, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const APressable = Animated.createAnimatedComponent(Pressable);

export function BasketBar({ itemCount, total, onPress }: { itemCount: number; total: string; onPress: () => void }) {
  const scale = useSharedValue(1);
  useEffect(() => {
    scale.value = withSequence(withTiming(1.03, { duration: 100 }), withTiming(1, { duration: 100 }));
  }, [itemCount]);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <APressable onPress={onPress}
      style={[{
        marginHorizontal: 16, minHeight: 52, borderRadius: 999,
        backgroundColor: colors.rooTeal, alignItems: 'center', justifyContent: 'center',
      }, style]}>
      <Text style={typography.button}>{`View basket · ${itemCount} items · ${total}`}</Text>
    </APressable>
  );
}
```

### Category Icon Row

```tsx
// components/CategoryRow.tsx
import { useState } from 'react';
import { Pressable, ScrollView, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Cat = { icon: keyof typeof Ionicons.glyphMap; label: string };

export function CategoryRow({ cats }: { cats: Cat[] }) {
  const [selected, setSelected] = useState(0);
  return (
    <ScrollView horizontal showsHorizontalScrollIndicator={false}
      contentContainerStyle={{ gap: 18, paddingHorizontal: 16 }}>
      {cats.map((c, i) => {
        const active = i === selected;
        return (
          <Pressable key={c.label} onPress={() => setSelected(i)} style={{ alignItems: 'center', gap: 7 }}>
            <Ionicons name={c.icon} size={24}
              color={active ? colors.rooTealInk : colors.rooTeal}
              style={{
                width: 54, height: 54, borderRadius: 16, textAlign: 'center',
                textAlignVertical: 'center', lineHeight: 54,
                backgroundColor: active ? colors.rooTeal : colors.surface1,
              }} />
            <Text style={[typography.caption, active && { color: colors.textPrimary }]}>{c.label}</Text>
          </Pressable>
        );
      })}
    </ScrollView>
  );
}
```

### Primary Button

```tsx
// components/RooButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RooButton({
  title, onPress, variant = 'teal',
}: { title: string; onPress: () => void; variant?: 'teal' | 'dark' }) {
  const teal = variant === 'teal';
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        minHeight: 52, borderRadius: 999, alignItems: 'center', justifyContent: 'center',
        backgroundColor: teal
          ? (pressed ? colors.rooTealPressed : colors.rooTeal)
          : colors.textPrimary,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={[typography.button, !teal && { color: '#FFFFFF' }]}>{title}</Text>
    </Pressable>
  );
}
// Checkout: <RooButton title="Checkout · £24.50" variant="dark" onPress={...} />
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
        tabBarActiveTintColor:  colors.rooTeal,
        tabBarInactiveTintColor: '#888888',
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-Bold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"      options={{ title: 'Home',       tabBarIcon: ({ color }) => <Ionicons name="home"          size={22} color={color} /> }} />
      <Tabs.Screen name="search"     options={{ title: 'Search',     tabBarIcon: ({ color }) => <Ionicons name="search"        size={22} color={color} /> }} />
      <Tabs.Screen name="orders"     options={{ title: 'Orders',     tabBarIcon: ({ color }) => <Ionicons name="receipt-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="favourites" options={{ title: 'Favourites', tabBarIcon: ({ color }) => <Ionicons name="heart-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="account"    options={{ title: 'Account',    tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Floating + add → bounce then morph; basket slides up
import { withSequence, withSpring, withTiming } from 'react-native-reanimated';
plusScale.value = withSequence(withTiming(0.9, { duration: 90 }), withSpring(1, { damping: 8 }));
basketY.value = withTiming(0, { duration: 280 });

// Quantity stepper — Haptics + numeric crossfade
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Basket bar pulse on add
scale.value = withSequence(withTiming(1.03, { duration: 100 }), withTiming(1, { duration: 100 }));

// Category tile select — animate background color
// use Reanimated useDerivedValue / interpolateColor over 150ms

// Card → menu: shared element via expo-router + react-native-shared-element (320ms)

// Order tracking stage advance
// stageIndex bump + Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium)
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). For the moped/scooter fee-pill glyph use `bicycle` or `MaterialCommunityIcons` `moped`.

| Purpose | Ionicons |
|---------|----------|
| Home (tab) | `home` |
| Search (tab) | `search` |
| Orders (tab) | `receipt-outline` |
| Favourites (tab) | `heart-outline` |
| Account (tab) | `person-circle-outline` |
| Saved heart (card) | `heart` / `heart-outline` |
| Rating star | `star` |
| Address chevron | `chevron-down` |
| Basket | `bag-handle` |
| Fee pill (moped) | `bicycle` (or MCI `moped`) |
| Floating add | `add` |
| Stepper minus | `remove` |
| Category — Restaurants | `restaurant` |
| Category — Grocery | `cart` |
| Category — Fast | `time` |
| Category — Treats | `ice-cream` |
| Category — Offers | `pricetag` |
| Back | `chevron-back` |
| Share | `share-outline` |
| Rider (tracking) | `bicycle` |

## 7. Platform Notes

- **Font choice**: Deliveroo's brand face is proprietary — ship **Inter** (SIL OFL). Bundle Regular → Black weights (the app runs heavy, so ExtraBold/Black matter)
- **On-teal color**: never put white text on a teal `#00CCBC` button — use Teal Ink `#003733`. This is the Deliveroo signature and is also the only AA-passing pairing
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on prices, fees, minimums and quantity counters
- **Status bar**: `<StatusBar style="dark" />` on light mode; `"light"` on dark and over the tracking map (with a gradient scrim)
- **Safe area**: wrap screens in `SafeAreaView`; the address bar, bottom tab bar and the sticky basket bar need safe-area padding
- **Dynamic Type**: React Native respects system scale on `<Text>`; set `allowFontScaling={false}` on Plus/offer badges, the fee pill, tab labels and category labels
- **Sticky basket bar**: place it in an absolutely-positioned footer with `paddingBottom: insets.bottom`; embed `count` and tabular `total` in the label; pulse on add
- **Floating `+`**: render inside the thumbnail's parent `View` with negative `bottom`/`right` offsets so it overhangs; give it `elevation: 3` + iOS shadow
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` / `darkDivider`; keep teal constant; never dim food imagery
- **Maps**: use `react-native-maps` for the rider tracking map; the rider marker is a custom teal moped icon
- **Accessibility**: a restaurant card is one accessible element with a combined label; the floating `+` is an `accessibilityRole="button"` "Add {item}"; the basket bar announces changes via `accessibilityLiveRegion="polite"`
