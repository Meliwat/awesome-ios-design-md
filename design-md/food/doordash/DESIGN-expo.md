# DoorDash (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md). Paste-ready Expo / React Native code for DoorDash's design language.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  red:          '#EB1700',
  redPressed:   '#C31500',
  redTint:      '#FFEBE8',

  canvas:       '#FFFFFF',
  canvasDark:   '#191919',

  charcoal:     '#191919',
  textSecondary: '#757575',
  textTertiary:  '#AFAFAF',
  divider:       '#E5E5E5',
  surfaceMuted:  '#F7F7F7',
  surfaceTint:   '#FAFAFA',

  rating:        '#FF8000',
  feeGreen:      '#008B4A',
  warning:       '#F5B800',
  error:         '#D1350F',
  info:          '#0066E3',
  dashPass:      '#006B82',

  surfaceDark1: '#262626',
  surfaceDark2: '#303030',
  dividerDark:  '#3A3A3A',
} as const;
```

## 2. Typography

Load TT Norms Pro via `expo-font`. Fallback: system (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'TTNormsPro-Regular': require('../assets/fonts/TTNormsPro-Regular.ttf'),
    'TTNormsPro-Medium':  require('../assets/fonts/TTNormsPro-Medium.ttf'),
    'TTNormsPro-Bold':    require('../assets/fonts/TTNormsPro-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
const tabular = { fontVariant: ['tabular-nums' as const] };

export const typography = {
  heroTitle:    { fontFamily: 'TTNormsPro-Bold',    fontSize: 28, letterSpacing: -0.4, color: '#191919' },
  section:      { fontFamily: 'TTNormsPro-Bold',    fontSize: 22, letterSpacing: -0.3, color: '#191919' },
  merchant:     { fontFamily: 'TTNormsPro-Bold',    fontSize: 18, letterSpacing: -0.2, color: '#191919' },
  menuItem:     { fontFamily: 'TTNormsPro-Medium',  fontSize: 17, letterSpacing: -0.15, color: '#191919' },
  body:         { fontFamily: 'TTNormsPro-Regular', fontSize: 15, lineHeight: 21, color: '#191919' },
  priceLarge:   { fontFamily: 'TTNormsPro-Bold',    fontSize: 20, ...tabular, color: '#191919' },
  priceBody:    { fontFamily: 'TTNormsPro-Medium',  fontSize: 15, ...tabular, color: '#191919' },
  meta:         { fontFamily: 'TTNormsPro-Regular', fontSize: 13, color: '#757575' },
  chip:         { fontFamily: 'TTNormsPro-Medium',  fontSize: 14, color: '#191919' },
  buttonCTA:    { fontFamily: 'TTNormsPro-Bold',    fontSize: 16, color: '#FFFFFF' },
  buttonSmall:  { fontFamily: 'TTNormsPro-Medium',  fontSize: 14, color: '#191919' },
  tab:          { fontFamily: 'TTNormsPro-Medium',  fontSize: 11, letterSpacing: 0.1 },
  promoBadge:   { fontFamily: 'TTNormsPro-Bold',    fontSize: 11, letterSpacing: 0.3, textTransform: 'uppercase' as const, color: '#EB1700' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Floating Checkout CTA

```tsx
// components/FloatingCheckoutCTA.tsx
import { Pressable, Text, View, StyleSheet } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FloatingCheckoutCTA({
  itemCount, total, onPress,
}: { itemCount: number; total: number; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => [styles.cta, { opacity: pressed ? 0.92 : 1, transform: [{ scale: pressed ? 0.98 : 1 }] }]}
    >
      <View style={styles.left}>
        <View style={styles.countPill}>
          <Text style={styles.countText}>{itemCount}</Text>
        </View>
        <Text style={[typography.buttonCTA]}>Checkout</Text>
      </View>
      <Text style={[typography.buttonCTA, { fontVariant: ['tabular-nums'] }]}>
        {total.toLocaleString('en-US', { style: 'currency', currency: 'USD' })}
      </Text>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  cta: {
    height: 52, marginHorizontal: 16, borderRadius: 28,
    backgroundColor: colors.red,
    flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
    paddingHorizontal: 16,
    shadowColor: colors.red, shadowOpacity: 0.35, shadowRadius: 24, shadowOffset: { width: 0, height: 8 },
    elevation: 8,
  },
  left:      { flexDirection: 'row', alignItems: 'center', gap: 8 },
  countPill: { backgroundColor: '#fff', minWidth: 22, height: 22, borderRadius: 11, paddingHorizontal: 6, alignItems: 'center', justifyContent: 'center' },
  countText: { color: colors.red, fontFamily: 'TTNormsPro-Bold', fontSize: 11 },
});
```

### Merchant Card

```tsx
// components/MerchantCard.tsx
import { Image, Pressable, Text, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MerchantCard({
  name, rating, deliveryMinutes, deliveryFee, hasFreeDelivery, promoBadge, photoUri, isSaved, onToggleSave, onPress,
}: {
  name: string; rating: number; deliveryMinutes: number; deliveryFee: string;
  hasFreeDelivery: boolean; promoBadge?: string; photoUri: string;
  isSaved: boolean; onToggleSave: () => void; onPress: () => void;
}) {
  return (
    <Pressable onPress={onPress} style={styles.card}>
      <View style={styles.photoWrap}>
        <Image source={{ uri: photoUri }} style={styles.photo} />
        {promoBadge && (
          <View style={styles.promo}>
            <Text style={typography.promoBadge}>{promoBadge}</Text>
          </View>
        )}
        <Pressable onPress={onToggleSave} style={styles.heart}>
          <Ionicons name={isSaved ? 'heart' : 'heart-outline'} size={16} color={isSaved ? colors.red : '#000'} />
        </Pressable>
      </View>
      <View style={styles.info}>
        <Text style={typography.merchant} numberOfLines={1}>{name}</Text>
        <View style={styles.metaRow}>
          <Text style={{ color: colors.rating, fontSize: 12 }}>★</Text>
          <Text style={typography.meta}>{rating.toFixed(1)}</Text>
          <Text style={typography.meta}>·</Text>
          <Text style={typography.meta}>{deliveryMinutes} min</Text>
          <Text style={typography.meta}>·</Text>
          {hasFreeDelivery
            ? <Text style={[typography.meta, { color: colors.feeGreen, fontFamily: 'TTNormsPro-Medium' }]}>$0 Delivery</Text>
            : <Text style={typography.meta}>{deliveryFee}</Text>}
        </View>
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  card: {
    width: 260, backgroundColor: '#fff', borderRadius: 12, overflow: 'hidden',
    shadowColor: '#000', shadowOpacity: 0.06, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
  },
  photoWrap: { aspectRatio: 16/10, position: 'relative' },
  photo:     { width: '100%', height: '100%', backgroundColor: colors.surfaceMuted },
  promo:     { position: 'absolute', top: 8, left: 8, backgroundColor: colors.redTint, paddingVertical: 3, paddingHorizontal: 7, borderRadius: 4 },
  heart:     { position: 'absolute', top: 8, right: 8, width: 32, height: 32, borderRadius: 16, backgroundColor: 'rgba(255,255,255,0.85)', alignItems: 'center', justifyContent: 'center' },
  info:      { padding: 12, gap: 4 },
  metaRow:   { flexDirection: 'row', alignItems: 'center', gap: 4 },
});
```

### Category Chip

```tsx
// components/CategoryChip.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CategoryChip({ emoji, label, active, onPress }: {
  emoji: string; label: string; active: boolean; onPress: () => void;
}) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 6,
        paddingVertical: 8, paddingHorizontal: 14, borderRadius: 20,
        backgroundColor: active ? colors.charcoal : colors.surfaceMuted,
      }}
    >
      <Text>{emoji}</Text>
      <Text style={[typography.chip, { color: active ? '#fff' : colors.charcoal }]}>{label}</Text>
    </Pressable>
  );
}
```

### Quantity Stepper

```tsx
// components/QuantityStepper.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function QuantityStepper({ count, setCount, min = 0, max = 99 }: {
  count: number; setCount: (n: number) => void; min?: number; max?: number;
}) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12 }}>
      <Pressable
        onPress={() => count > min && setCount(count - 1)}
        style={({ pressed }) => ({
          width: 28, height: 28, borderRadius: 14,
          backgroundColor: colors.surfaceMuted,
          alignItems: 'center', justifyContent: 'center',
          opacity: pressed ? 0.6 : (count > min ? 1 : 0.5),
        })}
      >
        <Ionicons name="remove" size={16} color={count > min ? colors.charcoal : colors.textTertiary} />
      </Pressable>
      <Text style={{ fontFamily: 'TTNormsPro-Bold', fontSize: 16, minWidth: 20, textAlign: 'center' }}>{count}</Text>
      <Pressable
        onPress={() => count < max && setCount(count + 1)}
        style={({ pressed }) => ({
          width: 28, height: 28, borderRadius: 14,
          backgroundColor: colors.surfaceMuted,
          alignItems: 'center', justifyContent: 'center',
          opacity: pressed ? 0.6 : 1,
        })}
      >
        <Ionicons name="add" size={16} color={colors.charcoal} />
      </Pressable>
    </View>
  );
}
```

## 4. Tab Bar (always labeled)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.red,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarLabelStyle:        { fontFamily: 'TTNormsPro-Medium', fontSize: 11, letterSpacing: 0.1 },
        tabBarStyle:             { backgroundColor: '#fff', borderTopColor: colors.divider },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'home' : 'home-outline'}   size={24} color={color} /> }} />
      <Tabs.Screen name="grocery" options={{ title: 'Grocery', tabBarIcon: ({ color }) => <Ionicons name="basket-outline"     size={24} color={color} /> }} />
      <Tabs.Screen name="offers"  options={{ title: 'Offers',  tabBarIcon: ({ color }) => <Ionicons name="pricetag-outline"   size={24} color={color} /> }} />
      <Tabs.Screen name="orders"  options={{ title: 'Orders',  tabBarIcon: ({ color }) => <Ionicons name="receipt-outline"    size={24} color={color} /> }} />
      <Tabs.Screen name="account" options={{ title: 'Account', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'person' : 'person-outline'} size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// Add to cart bump
const scale = useSharedValue(1);
const onAdd = () => {
  scale.value = withSequence(withSpring(1.08), withSpring(1));
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
};

// Checkout tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Order placed
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
```

## 6. Icon Mappings

| Purpose | Ionicons |
|---------|----------|
| Home | `home-outline` / `home` |
| Grocery | `basket-outline` / `basket` |
| Offers | `pricetag-outline` / `pricetag` |
| Orders | `receipt-outline` / `receipt` |
| Account | `person-outline` / `person` |
| Heart | `heart-outline` / `heart` |
| Search | `search-outline` / `search` |
| Star | `star` (tint with `#FF8000`) |
| DashPass | `sparkles` |
| Cart | `bag-outline` / `bag` |
| Location | `location-outline` / `location` |

## 7. Platform Notes

- **Safe area**: wrap content; floating CTA sits 16pt above safe area bottom
- **Status bar**: `<StatusBar style="dark" />` on light canvas
- **Tabular numerals**: required on price (`$23.40` shouldn't jitter when quantity changes)
- **Dynamic Type**: body/meta scale freely; price numerals cap at 140%
- **Haptics on Android**: `expo-haptics` maps to short vibrations — acceptable fallback
- **Shadow on Android**: use `elevation` prop alongside `shadowColor/Opacity/Radius`
