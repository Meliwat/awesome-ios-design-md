# Etsy (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Etsy's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:         '#FAF5EF',
  card:           '#FFFFFF',
  surface:        '#F1F1F1',
  divider:        '#E1DDD5',
  surfacePressed: '#EFEAE2',

  textPrimary:    '#222222',
  textSecondary:  '#595959',
  textTertiary:   '#8A8A8A',

  orange:         '#F1641E',
  orangePressed:  '#D5571A',
  orangeTint:     '#FDEDE4',

  starFilled:     '#222222',
  saleRed:        '#A61A12',
  success:        '#258635',
} as const;

// Warm-tinted card shadow (brown, not neutral)
export const cardShadow = {
  shadowColor: '#785A32',
  shadowOpacity: 0.10,
  shadowRadius: 10,
  shadowOffset: { width: 0, height: 2 },
} as const;

export type EtsyColor = keyof typeof colors;
```

## 2. Typography

Load Graphik via `expo-font`, or substitute Inter. Fall back to `System` (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular': require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':  require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-Bold':    require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const ink = { color: '#222222' } satisfies TextStyle;

export const typography = {
  titleLarge: { ...ink, fontFamily: 'Inter-Bold',    fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  listing:    { ...ink, fontFamily: 'Inter-Bold',    fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  section:    { ...ink, fontFamily: 'Inter-Bold',    fontSize: 20, lineHeight: 24, letterSpacing: -0.2 },
  price:      { ...ink, fontFamily: 'Inter-Bold',    fontSize: 24, lineHeight: 26, letterSpacing: -0.3 },
  cardTitle:  { ...ink, fontFamily: 'Inter-Medium',  fontSize: 15, lineHeight: 20 },
  cardPrice:  { ...ink, fontFamily: 'Inter-Bold',    fontSize: 16, lineHeight: 19 },
  body:       { ...ink, fontFamily: 'Inter-Regular', fontSize: 15, lineHeight: 23 },
  shopName:   {          fontFamily: 'Inter-Medium',  fontSize: 14, lineHeight: 18, color: '#595959' },
  button:     { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 20 },
  meta:       {          fontFamily: 'Inter-Regular', fontSize: 13, lineHeight: 17, color: '#595959' },
  badge:      {          fontFamily: 'Inter-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.3, color: '#F1641E', textTransform: 'uppercase' as const },
  tab:        {          fontFamily: 'Inter-Medium',  fontSize: 11, lineHeight: 13, letterSpacing: 0.2 },
  reviewCnt:  {          fontFamily: 'Inter-Medium',  fontSize: 13, lineHeight: 16, color: '#595959' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Favorite Heart (the emotional core)

```tsx
// components/FavoriteHeart.tsx
import { Pressable, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function FavoriteHeart({
  saved, onToggle, overPhoto = true, size = 24,
}: { saved: boolean; onToggle: () => void; overPhoto?: boolean; size?: number }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const press = () => {
    scale.value = withSequence(
      withSpring(1.25, { damping: 6, stiffness: 220 }),
      withSpring(1, { damping: 10 }),
    );
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
    onToggle();
  };

  return (
    <Pressable onPress={press} hitSlop={12}>
      <Animated.View
        style={[
          overPhoto && {
            width: 36, height: 36, borderRadius: 18,
            backgroundColor: 'rgba(0,0,0,0.45)',
            alignItems: 'center', justifyContent: 'center',
          },
          style,
        ]}
      >
        <Ionicons
          name={saved ? 'heart' : 'heart-outline'}
          size={size}
          color={saved ? colors.orange : overPhoto ? '#FFFFFF' : colors.textPrimary}
        />
      </Animated.View>
    </Pressable>
  );
}
```

### Star Row

```tsx
// components/StarRow.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StarRow({ rating, reviews, size = 11 }: { rating: number; reviews: number; size?: number }) {
  const r = Math.round(rating);
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
      <View style={{ flexDirection: 'row' }}>
        {[1, 2, 3, 4, 5].map((i) => (
          <Ionicons key={i} name={i <= r ? 'star' : 'star-outline'} size={size} color={colors.starFilled} />
        ))}
      </View>
      <Text style={typography.reviewCnt}>({reviews.toLocaleString()})</Text>
    </View>
  );
}
```

### Handmade Product Card

```tsx
// components/ProductCard.tsx
import { useState } from 'react';
import { Image, Pressable, Text, View } from 'react-native';
import { colors, cardShadow } from '../theme/colors';
import { typography } from '../theme/typography';
import { FavoriteHeart } from './FavoriteHeart';
import { StarRow } from './StarRow';

export function ProductCard({
  title, photoUri, price, rating, reviews, shop, bestseller, onPress,
}: {
  title: string; photoUri: string; price: string; rating: number;
  reviews: number; shop: string; bestseller?: boolean; onPress: () => void;
}) {
  const [saved, setSaved] = useState(false);
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => [
        { backgroundColor: colors.card, borderRadius: 12, padding: 8, gap: 6,
          transform: [{ scale: pressed ? 0.98 : 1 }] },
        cardShadow,
      ]}
    >
      <View>
        <Image source={{ uri: photoUri }} style={{ width: '100%', aspectRatio: 1, borderRadius: 12 }} />
        {bestseller && (
          <View style={{ position: 'absolute', top: 8, left: 8, backgroundColor: colors.orangeTint,
                         paddingVertical: 4, paddingHorizontal: 10, borderRadius: 999 }}>
            <Text style={typography.badge}>Bestseller</Text>
          </View>
        )}
        <View style={{ position: 'absolute', top: 8, right: 8 }}>
          <FavoriteHeart saved={saved} onToggle={() => setSaved((s) => !s)} />
        </View>
      </View>

      <Text style={typography.cardTitle} numberOfLines={2}>{title}</Text>
      <Text style={typography.cardPrice}>{price}</Text>
      <StarRow rating={rating} reviews={reviews} />
      <Text style={typography.shopName}>{shop}</Text>
      <Text style={typography.meta}>Free shipping</Text>
    </Pressable>
  );
}
```

### Primary Button

```tsx
// components/EtsyButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function EtsyButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.orangePressed : colors.orange,
        paddingVertical: 16, borderRadius: 999, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Listing Detail Hero (gallery)

```tsx
// components/ListingHero.tsx
import { useState } from 'react';
import { Dimensions, FlatList, Image, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { FavoriteHeart } from './FavoriteHeart';

const W = Dimensions.get('window').width;

export function ListingHero({ photos }: { photos: string[] }) {
  const [page, setPage] = useState(0);
  const [saved, setSaved] = useState(false);
  return (
    <View>
      <FlatList
        data={photos}
        horizontal pagingEnabled showsHorizontalScrollIndicator={false}
        keyExtractor={(_, i) => String(i)}
        onMomentumScrollEnd={(e) => setPage(Math.round(e.nativeEvent.contentOffset.x / W))}
        renderItem={({ item }) => (
          <Image source={{ uri: item }} style={{ width: W, aspectRatio: 4 / 5 }} />
        )}
      />
      <View style={{ position: 'absolute', top: 16, right: 16 }}>
        <FavoriteHeart saved={saved} onToggle={() => setSaved((s) => !s)} />
      </View>
      <View style={{ position: 'absolute', bottom: 14, left: 0, right: 0,
                     flexDirection: 'row', justifyContent: 'center', gap: 6 }}>
        {photos.map((_, i) => (
          <View key={i} style={{ width: 6, height: 6, borderRadius: 3,
            backgroundColor: i === page ? '#FFFFFF' : 'rgba(255,255,255,0.5)' }} />
        ))}
      </View>
      <View style={{ position: 'absolute', bottom: 12, right: 12, backgroundColor: 'rgba(0,0,0,0.5)',
                     paddingVertical: 4, paddingHorizontal: 10, borderRadius: 999 }}>
        <Text style={[typography.meta, { color: '#FFFFFF' }]}>{page + 1} / {photos.length}</Text>
      </View>
    </View>
  );
}
```

## 4. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.orange,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="light" style={{ flex: 1, backgroundColor: 'rgba(250,245,239,0.97)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Home',      tabBarIcon: ({ color }) => <Ionicons name="home"          size={24} color={color} /> }} />
      <Tabs.Screen name="search"    options={{ title: 'Search',    tabBarIcon: ({ color }) => <Ionicons name="search"        size={24} color={color} /> }} />
      <Tabs.Screen name="you"       options={{ title: 'You',       tabBarIcon: ({ color }) => <Ionicons name="person"        size={24} color={color} /> }} />
      <Tabs.Screen name="favorites" options={{ title: 'Favorites', tabBarIcon: ({ color }) => <Ionicons name="heart"         size={24} color={color} /> }} />
      <Tabs.Screen name="cart"      options={{ title: 'Cart',      tabBarIcon: ({ color }) => <Ionicons name="cart"          size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Favorite-heart bounce — see FavoriteHeart (withSequence spring 1.25 → 1, success haptic)

// Add-to-cart — medium haptic + a fly-to-cart animation of the thumbnail; bump the cart badge
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Card tap — scale 0.98 (Pressable style)

// Gallery paging — light selection haptic at each page
Haptics.selectionAsync();
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons) mapped to Etsy's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Favorite | `heart-outline` / `heart` |
| Star | `star-outline` / `star` |
| Search | `search` |
| Share | `share-outline` |
| Back | `chevron-back` |
| Cart | `cart-outline` / `cart` |
| Filters | `options-outline` |
| Message seller | `chatbubble-outline` |
| Home (tab) | `home-outline` / `home` |
| You (tab) | `person-outline` / `person` |
| Favorites (tab) | `heart-outline` / `heart` |
| Cart (tab) | `cart-outline` / `cart` |

## 7. Platform Notes

- **iOS-only feel**: use `expo-blur` (`tint="light"`) for the warm tab bar `.regularMaterial` equivalent; Android falls back to an opaque cream background
- **Status bar**: set `<StatusBar style="dark" />` from `expo-status-bar` — the warm cream canvas requires dark content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the listing hero photo bleeds full-width while the sticky bar respects the inset
- **Warm shadow on Android**: iOS `shadowColor` honors the brown tint; on Android use `elevation` (which loses the warm tint) plus a subtle `borderColor: colors.divider` so cards still separate from cream
- **Dynamic Type**: React Native respects user font-scaling by default; set `allowFontScaling={false}` on badge text, tab labels, the gallery photo-count pill, and the star glyphs (geometric units)
- **Accessibility**: add `accessibilityRole="button"` + `accessibilityLabel` on the favorite heart; expose the star row as a single combined label; group product-card text for VoiceOver
- **Performance**: product photos are large 1:1 images in a 2-column grid — use `expo-image` with `recyclingKey` for memory efficiency in long browse sessions
