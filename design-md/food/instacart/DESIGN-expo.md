# Instacart (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Instacart's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:  '#FFFFFF',
  surface: '#F6F7F8',
  divider: '#E8E9EB',

  textPrimary:   '#242529',
  textSecondary: '#72757E',
  textTertiary:  '#A6A8AE',

  green:        '#0AAD0A',
  greenPressed: '#098F09',
  greenTint:    '#E6F6E6',

  carrot:     '#FF7009',
  carrotTint: '#FFF0E6',
  error:      '#D6203A',
} as const;

export type ICColor = keyof typeof colors;
```

## 2. Typography

Load Inter via `expo-font`. Fall back to `System` which is SF Pro on iOS.

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

const ink = { color: '#242529' } satisfies TextStyle;

export const typography = {
  screenTitle:  { ...ink, fontFamily: 'Inter-Bold',     fontSize: 26, lineHeight: 31, letterSpacing: -0.4 },
  section:      { ...ink, fontFamily: 'Inter-Bold',     fontSize: 20, lineHeight: 25, letterSpacing: -0.3 },
  storeName:    { ...ink, fontFamily: 'Inter-Bold',     fontSize: 17, lineHeight: 22, letterSpacing: -0.2 },
  itemTitle:    { ...ink, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 20 },
  price:        { ...ink, fontFamily: 'Inter-Bold',     fontSize: 15, lineHeight: 18, fontVariant: ['tabular-nums'] },
  body:         { ...ink, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 22 },
  button:       { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 20 },
  stepperValue: { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 20, fontVariant: ['tabular-nums'] as const },
  meta:         { fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 17, color: '#72757E' },
  badge:        { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 12, lineHeight: 14, letterSpacing: 0.2 },
  tab:          { fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 13, letterSpacing: 0.1 },
  caption:      { fontFamily: 'Inter-Regular',  fontSize: 11, lineHeight: 14, color: '#72757E' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Quantity Stepper (− n +)

```tsx
// components/QuantityStepper.tsx
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function QuantityStepper({
  quantity, onChange,
}: { quantity: number; onChange: (q: number) => void }) {
  const step = (delta: number) => {
    const next = Math.max(0, quantity + delta);
    if (next !== quantity) {
      Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
      onChange(next);
    }
  };
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', height: 32,
                   borderRadius: 500, backgroundColor: colors.green }}>
      <Pressable onPress={() => step(-1)} hitSlop={12}
        style={{ width: 36, height: 32, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ color: '#fff', fontSize: 18, fontWeight: '700', lineHeight: 20 }}>−</Text>
      </Pressable>
      <Text style={[typography.stepperValue, { minWidth: 24, textAlign: 'center' }]}>{quantity}</Text>
      <Pressable onPress={() => step(1)} hitSlop={12}
        style={{ width: 36, height: 32, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ color: '#fff', fontSize: 18, fontWeight: '700', lineHeight: 20 }}>＋</Text>
      </Pressable>
    </View>
  );
}
```

### Add Button → Stepper Morph

```tsx
// components/AddOrStepper.tsx
import { useState } from 'react';
import { Pressable, Text } from 'react-native';
import Animated, { FadeIn, FadeOut, LinearTransition } from 'react-native-reanimated';
import { QuantityStepper } from './QuantityStepper';
import { colors } from '../theme/colors';

export function AddOrStepper() {
  const [qty, setQty] = useState(0);
  return (
    <Animated.View layout={LinearTransition.springify().damping(16)}>
      {qty === 0 ? (
        <Animated.View entering={FadeIn} exiting={FadeOut}>
          <Pressable
            onPress={() => setQty(1)}
            style={{ height: 32, paddingHorizontal: 20, borderRadius: 500,
                     backgroundColor: colors.canvas, borderWidth: 1.5, borderColor: colors.green,
                     alignItems: 'center', justifyContent: 'center' }}>
            <Text style={{ color: colors.green, fontFamily: 'Inter-Bold', fontSize: 14 }}>Add</Text>
          </Pressable>
        </Animated.View>
      ) : (
        <Animated.View entering={FadeIn} exiting={FadeOut}>
          <QuantityStepper quantity={qty} onChange={setQty} />
        </Animated.View>
      )}
    </Animated.View>
  );
}
```

### Persistent Green Cart Bar

```tsx
// components/CartBar.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { SlideInDown, SlideOutDown } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CartBar({
  itemCount, subtotal, onPress,
}: { itemCount: number; subtotal: string; onPress: () => void }) {
  if (itemCount === 0) return null;
  return (
    <Animated.View entering={SlideInDown.springify().damping(18)} exiting={SlideOutDown}
      style={{ position: 'absolute', left: 16, right: 16, bottom: 16 }}>
      <Pressable
        onPress={onPress}
        style={({ pressed }) => ({
          height: 56, borderRadius: 12, paddingHorizontal: 16,
          flexDirection: 'row', alignItems: 'center',
          backgroundColor: pressed ? colors.greenPressed : colors.green,
          transform: [{ scale: pressed ? 0.99 : 1 }],
        })}>
        <View style={{ backgroundColor: '#fff', borderRadius: 500, paddingHorizontal: 12, paddingVertical: 6 }}>
          <Text style={{ color: colors.green, fontFamily: 'Inter-Bold', fontSize: 13,
                          fontVariant: ['tabular-nums'] }}>{itemCount} items</Text>
        </View>
        <View style={{ flex: 1 }} />
        <Text style={[typography.button, { fontVariant: ['tabular-nums'] }]}>View cart  {subtotal}</Text>
      </Pressable>
    </Animated.View>
  );
}
```

### Store Card Row

```tsx
// components/StoreRow.tsx
import { Image, Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StoreRow({
  logoUri, name, eta, deliveryFee, onPress,
}: { logoUri: string; name: string; eta: string; deliveryFee: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 12,
        paddingVertical: 16, paddingHorizontal: 16,
        backgroundColor: pressed ? colors.surface : colors.canvas,
      })}>
      <Image source={{ uri: logoUri }} style={{ width: 56, height: 56, borderRadius: 12 }} />
      <View style={{ flex: 1, gap: 4 }}>
        <Text style={typography.storeName}>{name}</Text>
        <Text style={typography.meta}>{eta} · {deliveryFee} delivery</Text>
      </View>
      <Text style={{ color: colors.textTertiary, fontSize: 18 }}>›</Text>
    </Pressable>
  );
}
```

### Product Card

```tsx
// components/ProductCard.tsx
import { Image, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { AddOrStepper } from './AddOrStepper';

export function ProductCard({
  photoUri, price, unitPrice, title, dealFlag,
}: { photoUri: string; price: string; unitPrice: string; title: string; dealFlag?: string }) {
  return (
    <View style={{ width: 150, gap: 6 }}>
      <View>
        <Image source={{ uri: photoUri }}
          style={{ width: 150, height: 150, borderRadius: 12, backgroundColor: colors.surface }} />
        {dealFlag && (
          <View style={{ position: 'absolute', top: 8, left: 8, backgroundColor: colors.carrot,
                          borderRadius: 500, paddingHorizontal: 8, paddingVertical: 4 }}>
            <Text style={typography.badge}>{dealFlag}</Text>
          </View>
        )}
        <View style={{ position: 'absolute', right: 8, bottom: 8 }}><AddOrStepper /></View>
      </View>
      <Text style={typography.price}>{price}</Text>
      <Text style={typography.meta}>{unitPrice}</Text>
      <Text style={typography.itemTitle} numberOfLines={2}>{title}</Text>
    </View>
  );
}
```

### Replacement-Preference Radio Row

```tsx
// components/ReplacementOption.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ReplacementOption({
  label, selected, onPress,
}: { label: string; selected: boolean; onPress: () => void }) {
  return (
    <Pressable onPress={onPress}
      style={{ flexDirection: 'row', alignItems: 'center', padding: 16, borderRadius: 12,
               borderWidth: 1.5, borderColor: selected ? colors.green : colors.divider }}>
      <Text style={[typography.body, { flex: 1 }]}>{label}</Text>
      <View style={{ width: 22, height: 22, borderRadius: 11, borderWidth: 1.5,
                     borderColor: selected ? colors.green : colors.divider,
                     alignItems: 'center', justifyContent: 'center' }}>
        {selected && <View style={{ width: 12, height: 12, borderRadius: 6, backgroundColor: colors.green }} />}
      </View>
    </Pressable>
  );
}
```

## 4. Add-to-Cart Fly-Up

```tsx
// Measure the card thumbnail and the cart-bar anchor, then drive a shared value
// 0 → 1 with withTiming(450, Easing.out(Easing.quad)); interpolate translateX/Y
// along an arc (translateY adds a -60 mid-bump). On complete, bounce the cart
// badge: withSequence(withSpring(1.2), withSpring(1)) and call Haptics.impactAsync(Light).
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.green,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopColor: colors.divider, borderTopWidth: 0.5 },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Stores',       tabBarIcon: ({ color }) => <Ionicons name="storefront-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="buy-again" options={{ title: 'Buy it again', tabBarIcon: ({ color }) => <Ionicons name="repeat"            size={24} color={color} /> }} />
      <Tabs.Screen name="lists"     options={{ title: 'Lists',        tabBarIcon: ({ color }) => <Ionicons name="list"              size={24} color={color} /> }} />
      <Tabs.Screen name="account"   options={{ title: 'Account',      tabBarIcon: ({ color }) => <Ionicons name="person-outline"    size={24} color={color} /> }} />
      <Tabs.Screen name="cart"      options={{ title: 'Cart',         tabBarBadge: 8, tabBarIcon: ({ color }) => <Ionicons name="cart-outline" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Stepper tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Add pill ↔ stepper morph: Animated.View layout={LinearTransition.springify().damping(16)}
// with entering/exiting FadeIn/FadeOut

// Cart bar entrance: entering={SlideInDown.springify().damping(18)}

// Replacement select: animate the inner dot scale with withSpring on `selected`

// Add-to-cart fly-up: shared value 0→1, withTiming(450, Easing.out(Easing.quad)),
// arced translate; badge bounce withSequence(withSpring(1.2), withSpring(1))
```

## 7. Icon Library

Use `@expo/vector-icons`. Map to Instacart's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Stepper minus / plus | `remove` / `add` |
| Stores | `storefront-outline` / `storefront` |
| Buy it again | `repeat` |
| Lists | `list-outline` / `list` |
| Account | `person-outline` / `person` |
| Cart | `cart-outline` / `cart` |
| Search | `search` |
| Chevron | `chevron-forward` |
| Save / heart | `heart-outline` / `heart` |
| Filter | `options-outline` |
| Deal / tag | `pricetag` |
| Replacement / swap | `swap-horizontal` |

## 8. Platform Notes

- **Light-only feel**: Instacart's catalog model wants a white canvas — do not auto-invert to dark; if you support system dark, keep green and carrot identical
- **Status bar**: Set `<StatusBar style="dark" />` from `expo-status-bar` — light canvas requires dark content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the cart bar sits at `bottom: 16` and must clear the home indicator (add `useSafeAreaInsets().bottom`)
- **Tabular figures**: pass `fontVariant: ['tabular-nums']` on every price / quantity / total `Text` so list columns align
- **Dynamic Type**: RN respects font scaling; set `allowFontScaling={false}` only on the stepper value and tab labels where layout breaks
- **Accessibility**: give the stepper `accessibilityRole="adjustable"` with `accessibilityValue={{ text: \`\${quantity}\` }}`; label the cart bar "View cart, N items, $X"
