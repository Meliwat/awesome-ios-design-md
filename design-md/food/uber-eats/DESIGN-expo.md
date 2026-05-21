# Uber Eats (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Uber Eats' visual language into paste-ready Expo / React Native code: a design-token module (light + dark), themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-maps`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const light = {
  canvas:   '#FFFFFF',
  surface:  '#F3F3F3',
  surface2: '#EEEEEE',
  divider:  '#E8E8E8',
  textPrimary:   '#000000',
  textSecondary: '#6B6B6B',
  textTertiary:  '#A6A6A6',
} as const;

export const dark = {
  canvas:   '#000000',
  surface:  '#1C1C1E',
  surface2: '#2C2C2E',
  divider:  '#2C2C2E',
  textPrimary:   '#FFFFFF',
  textSecondary: '#A6A6A6',
  textTertiary:  '#6B6B6B',
} as const;

// Brand — identical in light & dark
export const brand = {
  green:        '#06C167',
  greenPressed: '#05A658',
  greenTint:    '#E7F8EF',
  errorRed:     '#E11900',
  busyAmber:    '#FF8A00',
} as const;

export type UEColor = keyof typeof light;
```

```tsx
// theme/useTheme.ts
import { useColorScheme } from 'react-native';
import { light, dark } from './colors';
export const useTheme = () => (useColorScheme() === 'dark' ? dark : light);
```

## 2. Typography

Load Uber Move via `expo-font`. Fall back to Inter (or `System`, SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'UberMove-Regular': require('../assets/fonts/UberMove-Regular.ttf'),
    'UberMove-Medium':  require('../assets/fonts/UberMove-Medium.ttf'),
    'UberMove-Bold':    require('../assets/fonts/UberMove-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

// color is applied by components from useTheme(); these define font/size/spacing only
export const typography = {
  restaurantHeader: { fontFamily: 'UberMove-Bold',    fontSize: 30, lineHeight: 33, letterSpacing: -0.4 },
  titleLarge:       { fontFamily: 'UberMove-Bold',    fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  sectionHeader:    { fontFamily: 'UberMove-Bold',    fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  subsection:       { fontFamily: 'UberMove-Bold',    fontSize: 20, lineHeight: 24, letterSpacing: -0.3 },
  restaurantName:   { fontFamily: 'UberMove-Bold',    fontSize: 17, lineHeight: 21, letterSpacing: -0.1 },
  menuItemTitle:    { fontFamily: 'UberMove-Medium',  fontSize: 16, lineHeight: 21, letterSpacing: -0.1 },
  body:             { fontFamily: 'UberMove-Regular', fontSize: 15, lineHeight: 22 },
  price:            { fontFamily: 'UberMove-Bold',    fontSize: 16, lineHeight: 20 },
  meta:             { fontFamily: 'UberMove-Regular', fontSize: 14, lineHeight: 18 },
  pill:             { fontFamily: 'UberMove-Medium',  fontSize: 14, lineHeight: 18 },
  caption:          { fontFamily: 'UberMove-Regular', fontSize: 13, lineHeight: 17 },
  labelUpper:       { fontFamily: 'UberMove-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.6, textTransform: 'uppercase' as const },
  button:           { fontFamily: 'UberMove-Bold',    fontSize: 16, lineHeight: 20, letterSpacing: 0.2 },
  tab:              { fontFamily: 'UberMove-Medium',  fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
  cartBadge:        { fontFamily: 'UberMove-Bold',    fontSize: 12, lineHeight: 14 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary Green Button

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import { brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({
  title, trailing, onPress,
}: { title: string; trailing?: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={({ pressed }) => ({
        height: 52, borderRadius: 12,
        backgroundColor: pressed ? brand.greenPressed : brand.green,
        flexDirection: 'row', alignItems: 'center',
        justifyContent: trailing ? 'space-between' : 'center',
        paddingHorizontal: trailing ? 20 : 0,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={[typography.button, { color: '#fff' }]}>{title}</Text>
      {trailing && <Text style={[typography.button, { color: '#fff' }]}>{trailing}</Text>}
    </Pressable>
  );
}
```

### Photo-First Restaurant Card

```tsx
// components/RestaurantCard.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { brand } from '../theme/colors';
import { typography } from '../theme/typography';
import { useTheme } from '../theme/useTheme';

export function RestaurantCard({
  name, rating, eta, fee, freeDelivery, photoUri,
}: { name: string; rating: string; eta: string; fee: string; freeDelivery?: boolean; photoUri: string }) {
  const t = useTheme();
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withTiming(0.98, { duration: 150 }))}
      onPressOut={() => (scale.value = withTiming(1, { duration: 150 }))}
    >
      <Animated.View style={style}>
        <View style={{ aspectRatio: 16 / 9, borderRadius: 12, overflow: 'hidden' }}>
          <Image source={{ uri: photoUri }} style={{ width: '100%', height: '100%' }} />
          {freeDelivery && (
            <View style={{ position: 'absolute', top: 10, left: 10, backgroundColor: brand.green,
                           paddingHorizontal: 8, paddingVertical: 4, borderRadius: 999 }}>
              <Text style={[typography.caption, { color: '#fff', fontFamily: 'UberMove-Bold' }]}>$0 Delivery Fee</Text>
            </View>
          )}
          <View style={{ position: 'absolute', top: 10, right: 10, width: 36, height: 36, borderRadius: 18,
                         backgroundColor: 'rgba(255,255,255,0.9)', alignItems: 'center', justifyContent: 'center' }}>
            <Ionicons name="heart-outline" size={18} color="#000" />
          </View>
        </View>
        <Text style={[typography.restaurantName, { color: t.textPrimary, marginTop: 8 }]}>{name}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, marginTop: 4 }}>
          <Ionicons name="star" size={12} color={t.textPrimary} />
          <Text style={[typography.meta, { color: t.textPrimary }]}>{rating}</Text>
          <Text style={[typography.meta, { color: t.textSecondary }]}>· {eta} ·</Text>
          <Text style={[typography.meta, { color: freeDelivery ? brand.green : t.textSecondary }]}>{fee}</Text>
        </View>
      </Animated.View>
    </Pressable>
  );
}
```

### Sticky Cart Bar

```tsx
// components/StickyCartBar.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { SlideInDown, SlideOutDown } from 'react-native-reanimated';
import { brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function StickyCartBar({
  count, total, onView,
}: { count: number; total: string; onView: () => void }) {
  if (count === 0) return null;
  return (
    <Animated.View
      entering={SlideInDown.springify().damping(16)}
      exiting={SlideOutDown}
      style={{ position: 'absolute', left: 16, right: 16, bottom: 16 }}
    >
      <Pressable
        onPress={onView}
        style={{ height: 56, borderRadius: 12, backgroundColor: brand.green,
                 flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16,
                 shadowColor: '#000', shadowOpacity: 0.12, shadowRadius: 12, shadowOffset: { width: 0, height: -2 } }}
      >
        <View style={{ width: 28, height: 28, borderRadius: 14, backgroundColor: '#fff',
                       alignItems: 'center', justifyContent: 'center' }}>
          <Text style={[typography.cartBadge, { color: brand.green }]}>{count}</Text>
        </View>
        <Text style={[typography.button, { color: '#fff', flex: 1, textAlign: 'center' }]}>View cart</Text>
        <Text style={[typography.button, { color: '#fff' }]}>{total}</Text>
      </Pressable>
    </Animated.View>
  );
}
```

### Menu Item Row + Cart-Count Bump

```tsx
// components/MenuItemRow.tsx
import { Image, Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { brand } from '../theme/colors';
import { typography } from '../theme/typography';
import { useTheme } from '../theme/useTheme';

export function MenuItemRow({
  name, desc, price, photoUri, onAdd,
}: { name: string; desc: string; price: string; photoUri: string; onAdd: () => void }) {
  const t = useTheme();
  return (
    <View style={{ flexDirection: 'row', paddingVertical: 12, gap: 12,
                    borderBottomWidth: 1, borderBottomColor: t.divider }}>
      <View style={{ flex: 1 }}>
        <Text style={[typography.menuItemTitle, { color: t.textPrimary }]}>{name}</Text>
        <Text style={[typography.meta, { color: t.textSecondary, marginTop: 4 }]} numberOfLines={2}>{desc}</Text>
        <Text style={[typography.body, { color: t.textPrimary, marginTop: 2 }]}>{price}</Text>
      </View>
      <View style={{ width: 88, height: 88 }}>
        <Image source={{ uri: photoUri }} style={{ width: 88, height: 88, borderRadius: 12 }} />
        <Pressable
          onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onAdd(); }}
          style={{ position: 'absolute', right: -6, bottom: -6, width: 28, height: 28, borderRadius: 14,
                   backgroundColor: brand.green, alignItems: 'center', justifyContent: 'center' }}
        >
          <Ionicons name="add" size={16} color="#fff" />
        </Pressable>
      </View>
    </View>
  );
}

// components/CartCountBadge.tsx
import { useEffect } from 'react';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';

export function CartCountBadge({ count }: { count: number }) {
  const scale = useSharedValue(1);
  useEffect(() => {
    scale.value = withSequence(withSpring(1.25, { damping: 6 }), withSpring(1, { damping: 12 }));
  }, [count]);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <Animated.View style={[{ minWidth: 18, height: 18, borderRadius: 9, backgroundColor: brand.green,
                             alignItems: 'center', justifyContent: 'center', paddingHorizontal: 4 }, style]}>
      <Text style={[typography.cartBadge, { color: '#fff' }]}>{count}</Text>
    </Animated.View>
  );
}
import { brand as _b } from '../theme/colors';
import { typography as _t } from '../theme/typography';
```

## 4. Live Map Order Tracking

```tsx
// components/OrderTracking.tsx
import { useEffect, useRef, useState } from 'react';
import { Text, View } from 'react-native';
import MapView, { Marker, Polyline, PROVIDER_DEFAULT } from 'react-native-maps';
import { brand } from '../theme/colors';
import { typography } from '../theme/typography';
import { useTheme } from '../theme/useTheme';

type LatLng = { latitude: number; longitude: number };

export function OrderTracking({
  route, etaText, step,
}: { route: LatLng[]; etaText: string; step: number }) {
  const t = useTheme();
  const [courier, setCourier] = useState(route[0]);
  const i = useRef(0);

  useEffect(() => {
    const id = setInterval(() => {
      i.current = Math.min(i.current + 1, route.length - 1);
      setCourier(route[i.current]); // Marker animates via coordinate prop interpolation
    }, 3000);
    return () => clearInterval(id);
  }, [route]);

  const steps = ['Preparing', 'Picked up', 'On the way', 'Delivered'];

  return (
    <View style={{ flex: 1 }}>
      <MapView provider={PROVIDER_DEFAULT} style={{ flex: 1 }}
               initialRegion={{ ...route[0], latitudeDelta: 0.02, longitudeDelta: 0.02 }}>
        <Polyline coordinates={route} strokeColor="#000000" strokeWidth={4} />
        <Marker coordinate={courier} anchor={{ x: 0.5, y: 0.5 }}>
          <View style={{ width: 22, height: 22, borderRadius: 11, backgroundColor: brand.green,
                         borderWidth: 3, borderColor: '#fff' }} />
        </Marker>
      </MapView>

      <View style={{ position: 'absolute', left: 0, right: 0, bottom: 0,
                     backgroundColor: t.canvas, borderTopLeftRadius: 16, borderTopRightRadius: 16,
                     padding: 20, gap: 16,
                     shadowColor: '#000', shadowOpacity: 0.16, shadowRadius: 28, shadowOffset: { width: 0, height: -8 } }}>
        <View style={{ width: 40, height: 5, borderRadius: 3, backgroundColor: t.divider, alignSelf: 'center' }} />
        <Text style={[typography.sectionHeader, { color: t.textPrimary }]}>{etaText}</Text>
        <View style={{ flexDirection: 'row', gap: 6 }}>
          {steps.map((_, idx) => (
            <View key={idx} style={{ flex: 1, height: 4, borderRadius: 2,
                                     backgroundColor: idx <= step ? brand.green : t.surface2 }} />
          ))}
        </View>
      </View>
    </View>
  );
}
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useTheme } from '../../theme/useTheme';
import { brand } from '../../theme/colors';

export default function TabsLayout() {
  const t = useTheme();
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:  t.textPrimary,   // near-black/white, NOT green
        tabBarInactiveTintColor: t.textTertiary,
        tabBarStyle: { backgroundColor: t.canvas, borderTopColor: t.divider, borderTopWidth: 0.5 },
        tabBarLabelStyle: { fontFamily: 'UberMove-Medium', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home"     size={24} color={color} /> }} />
      <Tabs.Screen name="browse"  options={{ title: 'Browse',  tabBarIcon: ({ color }) => <Ionicons name="grid"     size={24} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search',  tabBarIcon: ({ color }) => <Ionicons name="search"   size={24} color={color} /> }} />
      <Tabs.Screen name="cart"    options={{ title: 'Cart',    tabBarIcon: ({ color }) => <Ionicons name="cart"     size={24} color={color} />,
                                              tabBarBadge: 3, tabBarBadgeStyle: { backgroundColor: brand.green, color: '#fff' } }} />
      <Tabs.Screen name="account" options={{ title: 'Account', tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Cart-count bump — withSequence(withSpring(1.25), withSpring(1)) on count change (see §3)

// Sticky cart bar appear
// entering={SlideInDown.springify().damping(16)}, exiting={SlideOutDown}

// Map courier marker — react-native-maps animates the Marker when its `coordinate` prop changes;
// for finer control use MarkerAnimated + Animated.timing on an AnimatedRegion (≈1s ease-in-out).

// Pill select — withTiming on backgroundColor (150ms); scale 0.97 on press

// Add to cart haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
```

## 7. Icon Library

Use `@expo/vector-icons` — ships Ionicons, Feather, MaterialCommunityIcons. Map to Uber Eats' SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Add | `add` |
| Quantity − / + | `remove` / `add` |
| Rating star | `star` (monochrome) |
| Favorite | `heart-outline` / `heart` |
| Share | `share-outline` |
| Search | `search` |
| Address chevron | `chevron-down` |
| Courier message | `chatbubble` |
| Courier call | `call` |
| Home | `home-outline` / `home` |
| Browse | `grid-outline` / `grid` |
| Search (tab) | `search` |
| Cart | `cart-outline` / `cart` |
| Account | `person-circle-outline` / `person-circle` |

## 8. Platform Notes

- **Light-first**: default to the light theme; `useColorScheme()` swaps neutrals to the dark set but `brand.green` is unchanged across both
- **Status bar**: use `<StatusBar style="auto" />` from `expo-status-bar` so it follows the scheme (dark content on the white canvas, light on dark)
- **Maps**: `react-native-maps` with `PROVIDER_DEFAULT` (Apple Maps on iOS) for the tracking screen; animate the courier `Marker` by updating its `coordinate` or using `MarkerAnimated` + `AnimatedRegion`
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the sticky cart bar and map sheet inset above the home indicator; featured rails bleed right
- **Dynamic Type**: React Native respects font scaling by default; set `allowFontScaling={false}` on tab labels, the cart badge, and the map ETA chip where single-line layout breaks
- **Accessibility**: add `accessibilityRole="button"` + `accessibilityLabel` on cards ("Sunrise Diner, 4.8 stars, 25 min, 99 cent delivery fee") and the cart bar ("View cart, 3 items, $28.40"); expose live ETA on the map via `accessibilityLiveRegion="polite"`
- **Reduce Motion**: gate the cart-count bump, cart-bar slide, and map marker easing behind `AccessibilityInfo.isReduceMotionEnabled()` (instant updates / fade only)
