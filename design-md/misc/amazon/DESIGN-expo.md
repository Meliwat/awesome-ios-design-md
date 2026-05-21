# Amazon (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Amazon's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, `expo-haptics`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas
  canvas:        '#FFFFFF',
  surfaceMuted:  '#F3F3F3',
  surfaceTint:   '#F7F8F8',
  divider:       '#DDDDDD',
  borderDefault: '#D5D9D9',

  // Text
  textPrimary:   '#0F1111',
  textSecondary: '#565959',
  textTertiary:  '#848A8C',

  // Brand
  yellow:           '#FF9900',
  yellowPressed:    '#E68A00',
  yellowHighlight:  '#FCD200',
  buyNowOrange:     '#F08804',
  deepNavy:         '#131921',
  secondaryNavy:    '#232F3E',

  // Semantic
  priceRed:      '#B12704',
  alertRed:      '#CC0C39',
  successGreen:  '#007600',
  primeTeal:     '#007185',
  primeSky:      '#00A8E1',
  ratingGold:    '#FFA41C',
  lowStockAmber: '#C45500',

  // Dark
  darkCanvas:    '#0F1111',
  darkSurface1:  '#1A1F25',
  darkSurface2:  '#232F3E',
  darkDivider:   '#3A4553',
  darkTextPrim:  '#F5F5F5',
  darkTextSec:   '#AAB7B8',
} as const;

export type AmazonColor = keyof typeof colors;
```

## 2. Typography

Amazon Ember is proprietary and requires a license. Load via `expo-font` if available; otherwise fall back to System (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    // If you have a licensed Ember bundle:
    // 'AmazonEmber-Regular': require('../assets/fonts/AmazonEmber-Regular.ttf'),
    // 'AmazonEmber-Medium':  require('../assets/fonts/AmazonEmber-Medium.ttf'),
    // 'AmazonEmber-Bold':    require('../assets/fonts/AmazonEmber-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { fontFamily: 'System' } satisfies TextStyle;

export const typography = {
  navHero:         { ...base, fontWeight: '700', fontSize: 22, lineHeight: 25, color: '#FFFFFF' },
  section:         { ...base, fontWeight: '700', fontSize: 20, lineHeight: 24, color: '#0F1111' },
  productTitle:    { ...base, fontWeight: '400', fontSize: 16, lineHeight: 21, color: '#0F1111' },
  pdpTitle:        { ...base, fontWeight: '500', fontSize: 20, lineHeight: 26, color: '#0F1111' },
  priceHero:       { ...base, fontWeight: '700', fontSize: 28, lineHeight: 28, color: '#0F1111' },
  priceSuperscript:{ ...base, fontWeight: '700', fontSize: 14, color: '#0F1111' },
  priceCard:       { ...base, fontWeight: '700', fontSize: 18, lineHeight: 20, color: '#0F1111' },
  priceStruck:     { ...base, fontWeight: '400', fontSize: 14, color: '#565959', textDecorationLine: 'line-through' },
  body:            { ...base, fontWeight: '400', fontSize: 15, lineHeight: 22, color: '#0F1111' },
  ratingCount:     { ...base, fontWeight: '400', fontSize: 13, color: '#007185' },
  delivery:        { ...base, fontWeight: '500', fontSize: 13, color: '#007600' },
  meta:            { ...base, fontWeight: '400', fontSize: 12, color: '#565959' },
  button:          { ...base, fontWeight: '500', fontSize: 15, color: '#0F1111' },
  buttonSmall:     { ...base, fontWeight: '400', fontSize: 14, color: '#0F1111' },
  primeBadge:      { ...base, fontWeight: '700', fontSize: 11, color: '#00A8E1', letterSpacing: 0.3 },
  tab:             { ...base, fontWeight: '500', fontSize: 10, color: '#565959' },
  searchPlaceholder: { ...base, fontWeight: '400', fontSize: 16, color: '#565959' },
  promoBadge:      { ...base, fontWeight: '700', fontSize: 11, color: '#FFFFFF', letterSpacing: 0.5 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Top Nav with Search Bar

```tsx
// components/AmazonTopNav.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AmazonTopNav({
  onSearchTap, onMicOrScan,
}: { onSearchTap: () => void; onMicOrScan: () => void }) {
  return (
    <View style={{
      height: 56,
      backgroundColor: colors.deepNavy,
      flexDirection: 'row',
      alignItems: 'center',
      paddingHorizontal: 12,
      gap: 12,
    }}>
      <Ionicons name="cart" size={28} color="#FFFFFF" />
      <Pressable
        onPress={onSearchTap}
        style={{
          flex: 1,
          height: 44,
          flexDirection: 'row',
          overflow: 'hidden',
          borderRadius: 8,
        }}
      >
        <View style={{
          flex: 1, flexDirection: 'row', alignItems: 'center', gap: 8,
          paddingHorizontal: 12, backgroundColor: colors.canvas,
        }}>
          <Ionicons name="search" size={18} color={colors.textSecondary} />
          <Text style={typography.searchPlaceholder}>Search Amazon</Text>
        </View>
        <Pressable
          onPress={onMicOrScan}
          style={{
            width: 72, backgroundColor: colors.yellow,
            alignItems: 'center', justifyContent: 'center',
            flexDirection: 'row', gap: 8,
          }}
        >
          <Ionicons name="mic" size={18} color={colors.textPrimary} />
          <Ionicons name="barcode-outline" size={22} color={colors.textPrimary} />
        </Pressable>
      </Pressable>
    </View>
  );
}
```

### Add to Cart Button (Yellow)

```tsx
// components/AddToCartButton.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AddToCartButton({ onPress }: { onPress: () => void }) {
  const scale = useSharedValue(1);
  const bg = useSharedValue(colors.yellow);
  const style = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
    backgroundColor: bg.value,
  }));

  return (
    <Pressable
      onPressIn={() => { scale.value = withTiming(0.98, { duration: 80 }); bg.value = withTiming(colors.yellowPressed); }}
      onPressOut={() => { scale.value = withTiming(1, { duration: 120 });  bg.value = withTiming(colors.yellow); }}
      onPress={() => { Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); onPress(); }}
    >
      <Animated.View style={[
        {
          height: 44, borderRadius: 8,
          alignItems: 'center', justifyContent: 'center',
          overflow: 'hidden',
          shadowColor: '#000', shadowOpacity: 0.1, shadowRadius: 2, shadowOffset: { width: 0, height: 1 },
        }, style,
      ]}>
        {/* 1pt highlight on top edge */}
        <View style={{
          position: 'absolute', top: 0, left: 0, right: 0, height: 1,
          backgroundColor: colors.yellowHighlight,
        }} />
        <Text style={typography.button}>Add to Cart</Text>
      </Animated.View>
    </Pressable>
  );
}
```

### Product Card (Grid Tile)

```tsx
// components/AmazonProductCard.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AmazonProductCard({
  title, rating, reviewCount, price, originalPrice, isPrime, deliveryLine, imageUri, onPress,
}: {
  title: string; rating: number; reviewCount: number;
  price: string; originalPrice?: string;
  isPrime: boolean; deliveryLine: string;
  imageUri: string; onPress: () => void;
}) {
  return (
    <Pressable onPress={onPress} style={{ padding: 8, gap: 6, flex: 1 }}>
      <Image source={{ uri: imageUri }}
             style={{ aspectRatio: 1, borderRadius: 4, backgroundColor: colors.surfaceMuted }} />
      <Text style={typography.productTitle} numberOfLines={2}>{title}</Text>

      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 2 }}>
        {[0,1,2,3,4].map(i => (
          <Ionicons key={i}
            name={i < Math.round(rating) ? 'star' : 'star-outline'}
            size={12} color={colors.ratingGold} />
        ))}
        <Text style={typography.ratingCount}> ({reviewCount.toLocaleString()})</Text>
      </View>

      <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 6 }}>
        <Text style={typography.priceCard}>{price}</Text>
        {originalPrice && <Text style={typography.priceStruck}>{originalPrice}</Text>}
      </View>

      {isPrime && (
        <View style={{ flexDirection: 'row', gap: 4, alignItems: 'baseline' }}>
          <Text style={typography.primeBadge}>prime</Text>
          <Text style={typography.delivery}>{deliveryLine}</Text>
        </View>
      )}
    </Pressable>
  );
}
```

### PDP Price Block (Superscript Cents)

```tsx
// components/PDPPriceBlock.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PDPPriceBlock({
  dollars, cents, originalPrice, savingsLine, deliveryText,
}: {
  dollars: number; cents: number;
  originalPrice?: string; savingsLine?: string;
  deliveryText: string;
}) {
  return (
    <View style={{ gap: 6 }}>
      <View style={{ flexDirection: 'row', alignItems: 'flex-start' }}>
        <Text style={[typography.priceSuperscript, { lineHeight: 14, marginTop: 4 }]}>$</Text>
        <Text style={typography.priceHero}>{dollars}</Text>
        <Text style={[typography.priceSuperscript, { lineHeight: 14, marginTop: 4 }]}>{cents.toString().padStart(2, '0')}</Text>
      </View>
      {originalPrice && <Text style={typography.priceStruck}>{originalPrice}</Text>}
      {savingsLine && <Text style={[typography.button, { color: colors.priceRed }]}>{savingsLine}</Text>}
      <View style={{ flexDirection: 'row', gap: 4, alignItems: 'baseline' }}>
        <Text style={typography.primeBadge}>prime</Text>
        <Text style={[typography.button, { color: colors.successGreen }]}>{deliveryText}</Text>
      </View>
    </View>
  );
}
```

### Lightning Deal Banner

```tsx
// components/LightningDealBanner.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LightningDealBanner({ countdown }: { countdown: string }) {
  return (
    <LinearGradient
      colors={[colors.priceRed, colors.alertRed]}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 10,
        paddingHorizontal: 12, paddingVertical: 8,
        borderRadius: 4,
      }}
    >
      <Ionicons name="flash" size={18} color="#FFFFFF" />
      <Text style={[typography.delivery, { color: '#FFFFFF', fontWeight: '700' }]}>Lightning Deal</Text>
      <View style={{ flex: 1 }} />
      <Text style={[typography.delivery, { color: '#FFFFFF', fontVariant: ['tabular-nums'] }]}>
        Ends in {countdown}
      </Text>
    </LinearGradient>
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

export function QuantityStepper({ count, onChange }: { count: number; onChange: (n: number) => void }) {
  return (
    <View style={{
      flexDirection: 'row',
      borderRadius: 8,
      backgroundColor: colors.surfaceMuted,
      borderWidth: 1, borderColor: colors.borderDefault,
      height: 32, alignItems: 'center',
    }}>
      <Pressable onPress={() => count > 1 && onChange(count - 1)} hitSlop={8} style={{ width: 44, alignItems: 'center' }}>
        <Ionicons name="remove" size={16} color={colors.textPrimary} />
      </Pressable>
      <Text style={[typography.button, { width: 36, textAlign: 'center' }]}>{count}</Text>
      <Pressable onPress={() => onChange(count + 1)} hitSlop={8} style={{ width: 44, alignItems: 'center' }}>
        <Ionicons name="add" size={16} color={colors.textPrimary} />
      </Pressable>
    </View>
  );
}
```

## 4. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.yellow,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: {
          backgroundColor: colors.canvas,
          borderTopColor: colors.divider,
          borderTopWidth: 0.5,
        },
        tabBarLabelStyle: { fontSize: 10, fontWeight: '500' },
      }}
    >
      <Tabs.Screen name="index"  options={{ title: 'Home',   tabBarIcon: ({ color }) => <Ionicons name="home"       size={24} color={color} /> }} />
      <Tabs.Screen name="menu"   options={{ title: 'Menu',   tabBarIcon: ({ color }) => <Ionicons name="menu"       size={24} color={color} /> }} />
      <Tabs.Screen name="cart"   options={{ title: 'Cart',   tabBarIcon: ({ color }) => <Ionicons name="cart"       size={24} color={color} />, tabBarBadge: 2, tabBarBadgeStyle: { backgroundColor: colors.alertRed } }} />
      <Tabs.Screen name="you"    options={{ title: 'You',    tabBarIcon: ({ color }) => <Ionicons name="person"     size={24} color={color} /> }} />
      <Tabs.Screen name="search" options={{ title: 'Search', tabBarIcon: ({ color }) => <Ionicons name="search"     size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Cart badge bump
const bump = useSharedValue(1);
const bumpStyle = useAnimatedStyle(() => ({ transform: [{ scale: bump.value }] }));
const onCartAdd = () => {
  bump.value = withSequence(withSpring(1.3, { damping: 8 }), withSpring(1, { damping: 12 }));
};

// Add-to-cart toast (slides up from bottom)
const toastY = useSharedValue(60);
const toastStyle = useAnimatedStyle(() => ({ transform: [{ translateY: toastY.value }] }));
const showToast = () => {
  toastY.value = withSpring(0, { damping: 14 });
  setTimeout(() => (toastY.value = withTiming(60, { duration: 200 })), 2500);
};
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons):

| Purpose | Ionicons |
|---------|----------|
| Cart | `cart` / `cart-outline` |
| Search | `search` |
| Mic | `mic` |
| Barcode | `barcode-outline` |
| Lightning Deal | `flash` |
| Star rating | `star` / `star-outline` |
| Checkmark success | `checkmark-circle` |
| Plus / Minus | `add` / `remove` |
| Menu | `menu` |
| Bell (notifications) | `notifications-outline` |
| Person (You tab) | `person` / `person-outline` |
| Home (tab) | `home` / `home-outline` |
| Lock (secure) | `lock-closed` |

## 7. Platform Notes

- **Safe area**: Wrap screen root with `SafeAreaView` from `react-native-safe-area-context`. The navy nav bar extends under the status bar on iOS — set `StatusBar` to `style="light"` while it's visible.
- **Status bar**: `<StatusBar style="light" />` from `expo-status-bar` whenever the navy nav is the top surface.
- **Dynamic Type**: React Native honors user scaling by default. Set `allowFontScaling={false}` on the `prime` wordmark (fixed brand mark) and the superscript cents (layout-sensitive). Cap product titles to `maxFontSizeMultiplier={1.4}`.
- **Dark mode**: Detect with `useColorScheme()`. Swap canvas to `#0F1111`, surfaces to `#1A1F25`, dividers to `#3A4553`. Yellow CTA color stays the same — `#FF9900` + black text is legible on both.
- **Accessibility**:
  - Add to Cart: `accessibilityLabel="Add [product] to cart for [price]"`
  - Star rating: `accessibilityLabel="4.7 out of 5, 12345 reviews"`
  - Prime badge: `accessibilityLabel="Prime eligible, free delivery"`
  - Lightning deal countdown: announce live region with remaining hours
- **Haptics**:
  - `Haptics.notificationAsync(Success)` on Add to Cart
  - `Haptics.impactAsync(Medium)` on "Place your order"
  - `Haptics.selectionAsync()` on quantity stepper and filter chip tap
  - `Haptics.notificationAsync(Error)` on "Out of stock" / form error
- **Image handling**: Use `expo-image` for faster product-image loading with built-in placeholders and transitions — critical for dense product grids.
