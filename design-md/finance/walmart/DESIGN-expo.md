# Walmart (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Walmart's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:      '#FFFFFF',
  surfaceTint: '#F2F8FD',
  surfaceGray: '#F5F5F5',
  divider:     '#E2E8F0',
  border:      '#C9D2DE',

  textPrimary:   '#2E2F32',
  textSecondary: '#74767C',
  textTertiary:  '#9BA0A8',

  blue:         '#0071DC',
  bluePressed:  '#004F9A',
  blueTint:     '#E6F1FC',
  spark:        '#FFC220',
  sparkPressed: '#E5A800',

  success:  '#1A7F37',
  savings:  '#2A8703',
  warning:  '#B25E00',
  error:    '#D03A2D',
  starGold: '#FFB81C',
} as const;

export type WalmartColor = keyof typeof colors;
```

## 2. Typography

Load Bogle via `expo-font`. Fall back to `System` (SF Pro on iOS); Inter is the closest webfont substitute.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Bogle-Regular':  require('../assets/fonts/Bogle-Regular.ttf'),
    'Bogle-Medium':   require('../assets/fonts/Bogle-Medium.ttf'),
    'Bogle-Semibold': require('../assets/fonts/Bogle-Semibold.ttf'),
    'Bogle-Bold':     require('../assets/fonts/Bogle-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#2E2F32' } satisfies TextStyle;

export const typography = {
  titleLarge:   { ...base, fontFamily: 'Bogle-Bold',     fontSize: 28, lineHeight: 34, letterSpacing: -0.3 },
  section:      { ...base, fontFamily: 'Bogle-Bold',     fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  priceLarge:   { ...base, fontFamily: 'Bogle-Bold',     fontSize: 22, lineHeight: 24, letterSpacing: -0.2, fontVariant: ['tabular-nums'] },
  subsection:   { ...base, fontFamily: 'Bogle-Semibold', fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  productTitle: { ...base, fontFamily: 'Bogle-Semibold', fontSize: 16, lineHeight: 21 },
  body:         { ...base, fontFamily: 'Bogle-Regular',  fontSize: 15, lineHeight: 22 },
  button:       { color: '#FFFFFF', fontFamily: 'Bogle-Semibold', fontSize: 16, lineHeight: 20 },
  priceInline:  { ...base, fontFamily: 'Bogle-Bold',     fontSize: 15, lineHeight: 18, fontVariant: ['tabular-nums'] },
  struck:       { fontFamily: 'Bogle-Regular', fontSize: 13, lineHeight: 16, color: '#74767C', textDecorationLine: 'line-through' as const },
  meta:         { fontFamily: 'Bogle-Regular', fontSize: 13, lineHeight: 17, color: '#74767C' },
  tag:          { fontFamily: 'Bogle-Bold',    fontSize: 12, lineHeight: 14, letterSpacing: 0.2, color: '#2E2F32', textTransform: 'uppercase' as const },
  tab:          { fontFamily: 'Bogle-Semibold', fontSize: 11, lineHeight: 13, letterSpacing: 0.1 },
  caption:      { fontFamily: 'Bogle-Regular',  fontSize: 11, lineHeight: 14, color: '#74767C' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary CTA — Add to Cart

```tsx
// components/CTAButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Variant = 'primary' | 'secondary' | 'deal';

export function CTAButton({
  title, variant = 'primary', onPress,
}: { title: string; variant?: Variant; onPress: () => void }) {
  const bg = variant === 'primary' ? colors.blue : variant === 'deal' ? colors.spark : 'transparent';
  const fg = variant === 'primary' ? '#FFFFFF' : variant === 'deal' ? colors.textPrimary : colors.blue;
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={({ pressed }) => ({
        minHeight: 48,
        alignItems: 'center',
        justifyContent: 'center',
        borderRadius: 24,
        backgroundColor: pressed && variant === 'primary' ? colors.bluePressed : bg,
        borderWidth: variant === 'secondary' ? 1.5 : 0,
        borderColor: colors.blue,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={[typography.button, { color: fg }]}>{title}</Text>
    </Pressable>
  );
}
```

### Rollback Price Tag (signature)

```tsx
// components/RollbackTag.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Kind = 'rollback' | 'clearance' | 'bestSeller';

export function RollbackTag({ kind = 'rollback' }: { kind?: Kind }) {
  const map = {
    rollback:   { bg: colors.spark,    fg: colors.textPrimary, label: 'Rollback' },
    clearance:  { bg: colors.error,    fg: '#FFFFFF',          label: 'Clearance' },
    bestSeller: { bg: colors.blueTint, fg: colors.blue,        label: 'Best seller' },
  }[kind];
  return (
    <View style={{ alignSelf: 'flex-start', backgroundColor: map.bg, paddingHorizontal: 8, paddingVertical: 3, borderRadius: 4 }}>
      <Text style={[typography.tag, { color: map.fg }]}>{map.label}</Text>
    </View>
  );
}
```

### Price Block

```tsx
// components/PriceBlock.tsx
import { Text, View } from 'react-native';
import { RollbackTag } from './RollbackTag';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function PriceBlock({
  price, wasPrice, savings, rollback,
}: { price: string; wasPrice?: string; savings?: string; rollback?: boolean }) {
  return (
    <View style={{ gap: 4 }}>
      {rollback && <RollbackTag kind="rollback" />}
      <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 8 }}>
        <Text style={typography.priceLarge}>{price}</Text>
        {wasPrice && <Text style={typography.struck}>{wasPrice}</Text>}
      </View>
      {savings && <Text style={[typography.meta, { color: colors.savings, fontFamily: 'Bogle-Semibold' }]}>{savings}</Text>}
    </View>
  );
}
```

### Product Card

```tsx
// components/ProductCard.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { PriceBlock } from './PriceBlock';
import { CTAButton } from './CTAButton';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ProductCard({
  title, price, wasPrice, rollback, rating, onAdd,
}: { title: string; price: string; wasPrice?: string; rollback?: boolean; rating: number; onAdd: () => void }) {
  return (
    <View style={{
      backgroundColor: colors.canvas, borderColor: colors.divider, borderWidth: 1,
      borderRadius: 12, padding: 12, gap: 8,
      shadowColor: '#101828', shadowOpacity: 0.06, shadowRadius: 3, shadowOffset: { width: 0, height: 1 },
    }}>
      <View style={{ aspectRatio: 1, backgroundColor: colors.surfaceGray, borderRadius: 8 }} />
      <PriceBlock price={price} wasPrice={wasPrice} rollback={rollback} />
      <Text style={typography.productTitle} numberOfLines={2}>{title}</Text>
      <View style={{ flexDirection: 'row', gap: 2 }}>
        {Array.from({ length: 5 }).map((_, i) => (
          <Ionicons key={i} name={i < rating ? 'star' : 'star-outline'} size={11} color={colors.starGold} />
        ))}
      </View>
      <View style={{ height: 40 }}>
        <CTAButton title="Add to cart" onPress={onAdd} />
      </View>
    </View>
  );
}
```

### Pickup / Delivery Toggle (signature)

```tsx
// components/FulfillmentToggle.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { useState } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FulfillmentToggle({ onChange }: { onChange?: (v: 'pickup' | 'delivery') => void }) {
  const [delivery, setDelivery] = useState(false);
  const thumb = useAnimatedStyle(() => ({ left: withTiming(delivery ? '50%' : '0%', { duration: 220 }) }));

  const pick = (v: boolean) => { setDelivery(v); onChange?.(v ? 'delivery' : 'pickup'); };

  return (
    <View style={{ flexDirection: 'row', backgroundColor: colors.surfaceTint, borderRadius: 20, padding: 4, position: 'relative' }}>
      <Animated.View
        style={[{ position: 'absolute', top: 4, bottom: 4, width: '50%', backgroundColor: '#FFF', borderRadius: 16,
                  shadowColor: '#101828', shadowOpacity: 0.10, shadowRadius: 6, shadowOffset: { width: 0, height: 2 } }, thumb]}
      />
      {(['pickup', 'delivery'] as const).map((seg, i) => {
        const active = (i === 1) === delivery;
        return (
          <Pressable key={seg} onPress={() => pick(i === 1)} style={{ flex: 1, minHeight: 32, alignItems: 'center', justifyContent: 'center' }}>
            <Text style={[typography.button, { color: active ? colors.blue : colors.textSecondary, textTransform: 'capitalize' }]}>{seg}</Text>
          </Pressable>
        );
      })}
    </View>
  );
}
```

### Quantity Stepper

```tsx
// components/QuantityStepper.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function QuantityStepper({ qty, setQty }: { qty: number; setQty: (n: number) => void }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 16, height: 36, paddingHorizontal: 14,
                   borderWidth: 1, borderColor: colors.border, borderRadius: 18, alignSelf: 'flex-start' }}>
      <Pressable onPress={() => setQty(Math.max(0, qty - 1))}>
        <Ionicons name={qty <= 1 ? 'trash-outline' : 'remove'} size={16} color={colors.blue} />
      </Pressable>
      <Text style={[typography.button, { color: colors.textPrimary }]}>{qty}</Text>
      <Pressable onPress={() => setQty(qty + 1)}>
        <Ionicons name="add" size={16} color={colors.blue} />
      </Pressable>
    </View>
  );
}
```

## 4. Spark Logo Mark

```tsx
// components/SparkMark.tsx
import { View } from 'react-native';
import { colors } from '../theme/colors';

export function SparkMark({ size = 28 }: { size?: number }) {
  return (
    <View style={{ width: size, height: size, alignItems: 'center', justifyContent: 'center' }}>
      {Array.from({ length: 6 }).map((_, i) => (
        <View
          key={i}
          style={{
            position: 'absolute',
            width: size * 0.16, height: size * 0.42,
            backgroundColor: colors.spark, borderRadius: size * 0.08,
            transform: [{ rotate: `${i * 60}deg` }, { translateY: -size * 0.30 }],
          }}
        />
      ))}
    </View>
  );
}
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
        tabBarActiveTintColor:   colors.blue,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopColor: colors.divider, borderTopWidth: 0.5 },
        tabBarLabelStyle: { fontFamily: 'Bogle-Semibold', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Shop',     tabBarIcon: ({ color }) => <Ionicons name="home"        size={24} color={color} /> }} />
      <Tabs.Screen name="services" options={{ title: 'Services', tabBarIcon: ({ color }) => <Ionicons name="construct"   size={24} color={color} /> }} />
      <Tabs.Screen name="search"   options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search"      size={24} color={color} /> }} />
      <Tabs.Screen name="cart"     options={{ title: 'Cart',     tabBarBadge: 3, tabBarIcon: ({ color }) => <Ionicons name="cart" size={24} color={color} /> }} />
      <Tabs.Screen name="account"  options={{ title: 'Account',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Add-to-cart "bump"
import { useSharedValue, useAnimatedStyle, withSequence, withTiming, withSpring } from 'react-native-reanimated';

const scale = useSharedValue(1);
const bumpStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

function onAddToCart() {
  scale.value = withSequence(
    withTiming(0.96, { duration: 100 }),
    withTiming(1.04, { duration: 80 }),
    withSpring(1, { damping: 8 }),
  );
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
  // increment cart badge with a small pop
}

// Subtotal roll on quantity change: animate a vertically-translating <Text> stack,
// or use a number-flip library; keep duration 200ms ease.
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Walmart's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Shop (tab) | `home-outline` / `home` |
| Services (tab) | `construct-outline` / `construct` |
| Search | `search` |
| Cart (tab) | `cart-outline` / `cart` |
| Account (tab) | `person-circle-outline` / `person-circle` |
| Barcode scan | `barcode-outline` |
| Heart / list | `heart-outline` / `heart` |
| Rating star | `star-outline` / `star` |
| Quantity minus | `remove` |
| Quantity remove | `trash-outline` |
| Quantity plus | `add` |
| Location chevron | `chevron-down` |
| Fulfillment ETA | `time-outline` |
| Success | `checkmark-circle` |

## 8. Platform Notes

- **Light-first**: Walmart is a bright white store; do not invert to dark by default. If you support system dark mode, lift blue to `#4D9BEA` and canvas to `#0E0F11`
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` — dark content on the white canvas
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the sticky "Add to cart" bar on PDP must clear the home indicator
- **Tab bar is opaque**: no `expo-blur` — Walmart's bar is a solid white surface with a hairline top border for readability
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on every price and the stepper count so columns align
- **Dynamic Type**: RN respects font scaling by default; set `allowFontScaling={false}` only on Rollback tags, tab labels, and the stepper digits where layout is rigid
- **Accessibility**: add `accessibilityRole="button"` + a product-specific `accessibilityLabel` on Add to cart; group card text and expose the CTA as a distinct button
