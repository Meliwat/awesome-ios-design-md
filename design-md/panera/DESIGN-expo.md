# Panera (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Panera's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  cream:          '#F4EFE1',
  oat:            '#FAF6EC',
  surfacePressed: '#EDE6D4',
  divider:        '#E7E0CF',

  // Surfaces (dark)
  darkCanvas:     '#14140F',
  darkSurface1:   '#1E1E18',
  darkSurface2:   '#28281F',
  darkDivider:    '#33332A',

  // Text
  textPrimary:    '#2A2A1F',
  textSecondary:  '#6C6655',
  textTertiary:   '#9C9684',
  darkTextPrimary:'#F3EFE4',
  darkTextSecondary:'#B4AE9C',

  // Brand
  green:          '#4C8B2B',
  greenBright:    '#6BBE45',
  greenPressed:   '#3C6E22',
  greenTint:      '#EAF3E3',
  greenTintDark:  '#1E2A18',

  // Photographic accents
  breadTan:       '#D8B271',
  soupOrange:     '#E07A2F',
  berry:          '#B0324B',
  goldStar:       '#F2B705',
  sipCoffee:      '#5A3A22',

  // Semantic
  error:          '#D6452F',
  errorDark:      '#E0594B',
} as const;

export type PaneraColor = keyof typeof colors;
```

## 2. Typography

Load Poppins (display/brand) and Inter (body/UI) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Poppins-SemiBold':  require('../assets/fonts/Poppins-SemiBold.ttf'),
    'Poppins-Bold':      require('../assets/fonts/Poppins-Bold.ttf'),
    'Poppins-ExtraBold': require('../assets/fonts/Poppins-ExtraBold.ttf'),
    'Inter-Regular':     require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':      require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':    require('../assets/fonts/Inter-SemiBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#2A2A1F' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...primary, fontFamily: 'Poppins-ExtraBold', fontSize: 32, lineHeight: 38, letterSpacing: -0.5 },
  hero:        { ...primary, fontFamily: 'Poppins-Bold',      fontSize: 26, lineHeight: 33, letterSpacing: -0.3 },
  section:     { ...primary, fontFamily: 'Poppins-Bold',      fontSize: 22, lineHeight: 29, letterSpacing: -0.2 },
  cardTitle:   { ...primary, fontFamily: 'Poppins-SemiBold',  fontSize: 18, lineHeight: 23 },
  itemName:    { ...primary, fontFamily: 'Poppins-SemiBold',  fontSize: 15, lineHeight: 20 },
  price:       { ...primary, fontFamily: 'Poppins-Bold',      fontSize: 14, lineHeight: 18 },
  rewardTag:   { color: '#4C8B2B', fontFamily: 'Poppins-Bold', fontSize: 12, lineHeight: 14, letterSpacing: 0.4 },
  button:      { color: '#FFFFFF', fontFamily: 'Poppins-Bold', fontSize: 16, lineHeight: 18, letterSpacing: 0.2 },
  body:        { ...primary, fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 24 },
  meta:        { color: '#6C6655', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 19 },
  pill:        { ...primary, fontFamily: 'Inter-SemiBold', fontSize: 13, lineHeight: 16 },
  tab:         { fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
  caption:     { color: '#6C6655', fontFamily: 'Inter-Medium', fontSize: 12, lineHeight: 17 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### MyPanera Rewards Card

```tsx
// components/MyPaneraCard.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MyPaneraCard({ headline, subtext, progress }: {
  headline: string; subtext: string; progress: number;
}) {
  return (
    <LinearGradient
      colors={[colors.green, colors.greenBright]}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
      style={{ borderRadius: 18, padding: 16,
               shadowColor: '#3C2810', shadowOpacity: 0.06, shadowRadius: 8, shadowOffset: { width: 0, height: 2 } }}
    >
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' }}>
        <Text style={{ fontFamily: 'Poppins-Bold', fontSize: 11, letterSpacing: 0.6, color: 'rgba(255,255,255,0.92)' }}>
          MYPANERA REWARDS
        </Text>
        <Ionicons name="chevron-forward" size={13} color="rgba(255,255,255,0.9)" />
      </View>
      <Text style={{ fontFamily: 'Poppins-Bold', fontSize: 19, color: '#FFFFFF', marginTop: 8 }}>{headline}</Text>
      <Text style={{ ...typography.caption, color: 'rgba(255,255,255,0.92)', marginTop: 3 }}>{subtext}</Text>
      <View style={{ height: 6, borderRadius: 3, backgroundColor: 'rgba(255,255,255,0.28)', marginTop: 12, overflow: 'hidden' }}>
        <View style={{ height: '100%', width: `${Math.round(progress * 100)}%`, backgroundColor: '#FFFFFF', borderRadius: 3 }} />
      </View>
    </LinearGradient>
  );
}
```

### Menu Item Row

```tsx
// components/MenuItemRow.tsx
import { View, Text, Image, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MenuItemRow({ uri, name, desc, price, rewardTag, onAdd }: {
  uri: string; name: string; desc: string; price: string; rewardTag?: string; onAdd: () => void;
}) {
  return (
    <View style={{ flexDirection: 'row', gap: 14, paddingVertical: 14,
                   borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      <Image source={{ uri }} style={{ width: 92, height: 92, borderRadius: 14 }} />
      <View style={{ flex: 1 }}>
        <Text style={typography.itemName}>{name}</Text>
        <Text style={[typography.caption, { marginTop: 3 }]} numberOfLines={2}>{desc}</Text>
        {rewardTag ? <Text style={[typography.rewardTag, { marginTop: 4 }]}>{'★ ' + rewardTag}</Text> : null}
        <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', marginTop: 8 }}>
          <Text style={typography.price}>{price}</Text>
          <Pressable onPress={onAdd} hitSlop={8}
            style={{ width: 30, height: 30, borderRadius: 15, backgroundColor: colors.green,
                     alignItems: 'center', justifyContent: 'center' }}>
            <Ionicons name="add" size={15} color="#FFFFFF" />
          </Pressable>
        </View>
      </View>
    </View>
  );
}
```

### Quantity Stepper

```tsx
// components/QuantityStepper.tsx
import { View, Text, Pressable } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function QuantityStepper({ count, setCount }: { count: number; setCount: (n: number) => void }) {
  const tick = (n: number) => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); setCount(n); };
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 16, paddingHorizontal: 8,
                   borderRadius: 999, backgroundColor: colors.oat, borderWidth: 1, borderColor: colors.divider }}>
      <Pressable onPress={() => count > 1 && tick(count - 1)} style={{ width: 44, height: 44, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontFamily: 'Poppins-Bold', fontSize: 16, color: colors.green }}>−</Text>
      </Pressable>
      <Text style={{ fontFamily: 'Poppins-Bold', fontSize: 15, color: colors.textPrimary, minWidth: 16, textAlign: 'center' }}>{count}</Text>
      <Pressable onPress={() => tick(count + 1)} style={{ width: 44, height: 44, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontFamily: 'Poppins-Bold', fontSize: 16, color: colors.green }}>+</Text>
      </Pressable>
    </View>
  );
}
```

### Sticky "Add to Order" Bar

```tsx
// components/AddToOrderBar.tsx
import { View, Text, Pressable } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AddToOrderBar({ priceLabel, onPress }: { priceLabel: string; onPress: () => void }) {
  return (
    <View style={{ paddingHorizontal: 16, paddingVertical: 10, backgroundColor: colors.canvas,
                   shadowColor: '#3C2810', shadowOpacity: 0.10, shadowRadius: 16, shadowOffset: { width: 0, height: 4 } }}>
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
        style={({ pressed }) => ({
          backgroundColor: pressed ? colors.greenPressed : colors.green,
          borderRadius: 999, paddingVertical: 16, alignItems: 'center',
          transform: [{ scale: pressed ? 0.98 : 1 }],
        })}>
        <Text style={typography.button}>{`Add to Order · ${priceLabel}`}</Text>
      </Pressable>
    </View>
  );
}
```

### Pickup / Delivery Toggle

```tsx
// components/PickupDeliveryToggle.tsx
import { View, Text, Pressable } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PickupDeliveryToggle({ isPickup, setIsPickup }: { isPickup: boolean; setIsPickup: (b: boolean) => void }) {
  const Seg = ({ label, on, tap }: { label: string; on: boolean; tap: () => void }) => (
    <Pressable onPress={tap} style={{ flex: 1, paddingVertical: 10, borderRadius: 999,
      backgroundColor: on ? colors.green : 'transparent', alignItems: 'center' }}>
      <Text style={[typography.pill, { color: on ? '#FFFFFF' : colors.textSecondary }]}>{label}</Text>
    </Pressable>
  );
  return (
    <View style={{ flexDirection: 'row', padding: 4, borderRadius: 999,
                   backgroundColor: colors.oat, borderWidth: 1, borderColor: colors.divider }}>
      <Seg label="Pickup" on={isPickup} tap={() => setIsPickup(true)} />
      <Seg label="Delivery" on={!isPickup} tap={() => setIsPickup(false)} />
    </View>
  );
}
```

### Reward / Loyalty Chip

```tsx
// components/RewardChip.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';

export function RewardChip({ text }: { text: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8,
                   paddingVertical: 8, paddingLeft: 10, paddingRight: 16, borderRadius: 999,
                   backgroundColor: colors.oat, borderWidth: 1, borderColor: colors.divider }}>
      <LinearGradient colors={[colors.greenBright, colors.green]} start={{x:0,y:0}} end={{x:1,y:1}}
        style={{ width: 22, height: 22, borderRadius: 11 }} />
      <Text style={{ fontFamily: 'Poppins-Bold', fontSize: 13, color: colors.textPrimary }}>{text}</Text>
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
        tabBarActiveTintColor:  colors.green,      // greenBright in dark mode
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Home',      tabBarIcon: ({ color }) => <Ionicons name="home"           size={22} color={color} /> }} />
      <Tabs.Screen name="order"     options={{ title: 'Order',     tabBarIcon: ({ color }) => <Ionicons name="bag-handle"     size={22} color={color} /> }} />
      <Tabs.Screen name="rewards"   options={{ title: 'Rewards',   tabBarIcon: ({ color }) => <Ionicons name="star"           size={22} color={color} /> }} />
      <Tabs.Screen name="favorites" options={{ title: 'Favorites', tabBarIcon: ({ color }) => <Ionicons name="bookmark"       size={22} color={color} /> }} />
      <Tabs.Screen name="account"   options={{ title: 'Account',   tabBarIcon: ({ color }) => <Ionicons name="person-circle"  size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Add to Order — scale-bounce on press + cart increment haptic
// transform: [{ scale: pressed ? 0.98 : 1 }] + Haptics.impactAsync(Soft)

// Rewards progress fill (earned reward)
import Animated, { useSharedValue, withTiming, useAnimatedStyle } from 'react-native-reanimated';
// width.value = withTiming(newPct, { duration: 600 });

// Category underline slide — withTiming(targetX, { duration: 200 })

// Item detail open — expo-router shared transition or FadeIn on hero image
import Animated, { FadeIn } from 'react-native-reanimated';

// Cart sheet — @gorhom/bottom-sheet, snapPoints ['50%','90%']

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);          // stepper, toggle, add-to-order
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // reward unlocked
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Order | `bag-handle` |
| Rewards | `star` |
| Favorites | `bookmark` |
| Account | `person-circle` |
| Add to order | `add` |
| Stepper minus / plus | `remove` / `add` |
| Back | `chevron-back` |
| Favorite (heart) | `heart` / `heart-outline` |
| Share | `share-outline` |
| Reward star | `star` |
| Sip Club | `cafe` |
| Pickup / location | `location` |
| Delivery | `bicycle` |
| Calories / info | `flame` |
| Search | `search` |
| Disclosure | `chevron-forward` |

## 7. Platform Notes

- **Font choice**: Poppins (display/brand, closest free analog to Panera's marketing face) + Inter (body/UI) — both SIL OFL, free to bundle
- **Status bar**: `<StatusBar style="dark" />` on light mode, `"light"` on dark; over the full-bleed item-detail hero photo use `"light"` with a top scrim
- **Safe area**: wrap screens in `SafeAreaView`; the sticky "Add to Order" bar needs `paddingBottom: insets.bottom`; the tab bar respects the home indicator
- **Dynamic Type**: React Native respects system scale on `<Text>`; set `allowFontScaling={false}` on tab labels, reward tags, filter chips, stepper glyphs
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1`; switch the action color to `greenBright` so CTAs read on `#14140F`
- **Image-heavy**: use `expo-image` with `contentFit="cover"` and `transition` for menu photos; cache aggressively — food photography is the product
- **Keyboard**: `KeyboardAvoidingView` on search and address-entry screens
- **Accessibility**: every menu row is a single accessible element ("{name}, {price}, {calories}, add to order"); the add `+` button needs its own `accessibilityLabel`; rewards card announces progress
