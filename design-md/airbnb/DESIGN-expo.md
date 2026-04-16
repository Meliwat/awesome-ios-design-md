# Airbnb (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Airbnb's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces
  canvas:        '#FFFFFF',
  surfaceGray:   '#F7F7F7',
  surfaceGray2:  '#EBEBEB',
  divider:       '#EBEBEB',

  // Text
  hof:           '#484848',  // primary text
  foggy:         '#767676',  // secondary text
  foggyLight:    '#B0B0B0',  // tertiary text
  ink:           '#222222',  // hero titles

  // Brand
  coral:         '#FF385C',  // primary (current)
  coralPressed:  '#E31C5F',
  rausch:        '#FF5A5F',  // heritage — Belo logomark only
  babu:          '#00A699',  // Experiences / Plus
  arches:        '#FC642D',  // Trips
  beach:         '#FFB400',  // star yellow (review screens)

  // Semantic
  success:       '#008A05',
  error:         '#C13515',

  // Dark mode
  darkCanvas:    '#121212',
  darkSurface:   '#1C1C1E',
  darkSurface2:  '#2A2A2A',
  darkText:      '#DDDDDD',
  darkTextSec:   '#A0A0A0',
} as const;

export type AirbnbColor = keyof typeof colors;
```

## 2. Typography

Airbnb Cereal is proprietary. Bundle via `expo-font`, or fall back to `System` which resolves to SF Pro on iOS — the warmest system face.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'AirbnbCereal-Book':       require('../assets/fonts/AirbnbCereal-Book.ttf'),
    'AirbnbCereal-Medium':     require('../assets/fonts/AirbnbCereal-Medium.ttf'),
    'AirbnbCereal-Bold':       require('../assets/fonts/AirbnbCereal-Bold.ttf'),
    'AirbnbCereal-ExtraBold':  require('../assets/fonts/AirbnbCereal-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#484848' } satisfies TextStyle;
const meta    = { color: '#767676' } satisfies TextStyle;

export const typography = {
  largeNav:    { color: '#222222', fontFamily: 'AirbnbCereal-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  hero:        { color: '#222222', fontFamily: 'AirbnbCereal-Bold',       fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:     { color: '#484848', fontFamily: 'AirbnbCereal-Bold',       fontSize: 22, lineHeight: 26 },
  subsection:  { color: '#484848', fontFamily: 'AirbnbCereal-Bold',       fontSize: 18, lineHeight: 23 },

  cardTitle:   { ...primary, fontFamily: 'AirbnbCereal-Medium',    fontSize: 15, lineHeight: 20 },
  body:        { ...primary, fontFamily: 'AirbnbCereal-Book',      fontSize: 16, lineHeight: 22 },
  bodySmall:   { ...primary, fontFamily: 'AirbnbCereal-Book',      fontSize: 14, lineHeight: 20 },
  ratingNum:   { ...primary, fontFamily: 'AirbnbCereal-Medium',    fontSize: 14, lineHeight: 17 },
  meta:        { ...meta,    fontFamily: 'AirbnbCereal-Book',      fontSize: 14, lineHeight: 18 },
  caption:     { ...meta,    fontFamily: 'AirbnbCereal-Book',      fontSize: 12, lineHeight: 16 },

  priceInline: { ...primary, fontFamily: 'AirbnbCereal-Bold',      fontSize: 15, lineHeight: 18 },
  priceHero:   { color: '#222222', fontFamily: 'AirbnbCereal-ExtraBold', fontSize: 22, lineHeight: 24 },

  button:      { color: '#FFFFFF', fontFamily: 'AirbnbCereal-Bold',      fontSize: 16, lineHeight: 20 },
  buttonSm:    { color: '#222222', fontFamily: 'AirbnbCereal-Medium',    fontSize: 14, lineHeight: 18 },
  tab:         { fontFamily: 'AirbnbCereal-Medium',                     fontSize: 10, lineHeight: 12, letterSpacing: 0.1 },
  chip:        { fontFamily: 'AirbnbCereal-Medium',                     fontSize: 12, lineHeight: 14 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Save Heart (top-right of every stay card)

```tsx
// components/SaveHeart.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withSequence } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function SaveHeart({ isSaved, onToggle }: { isSaved: boolean; onToggle: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const handlePress = () => {
    scale.value = withSequence(withSpring(1.25, { damping: 8 }), withSpring(1, { damping: 8 }));
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
    onToggle();
  };

  return (
    <Pressable onPress={handlePress} hitSlop={12} style={{ padding: 6 }}>
      <Animated.View
        style={[
          {
            width: 32, height: 32, alignItems: 'center', justifyContent: 'center',
            shadowColor: '#000', shadowOpacity: 0.2, shadowRadius: 4, shadowOffset: { width: 0, height: 2 },
          },
          style,
        ]}
      >
        <Ionicons
          name={isSaved ? 'heart' : 'heart-outline'}
          size={26}
          color={isSaved ? colors.coral : '#FFFFFF'}
          style={isSaved ? undefined : { textShadowColor: '#222', textShadowRadius: 0.5 }}
        />
      </Animated.View>
    </Pressable>
  );
}
```

### Rating Row

```tsx
// components/RatingRow.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RatingRow({ rating, reviews }: { rating: number; reviews: number }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
      <Ionicons name="star" size={12} color={colors.hof} />
      <Text style={typography.ratingNum}>{rating.toFixed(2)}</Text>
      <Text style={typography.meta}> · {reviews.toLocaleString()}</Text>
    </View>
  );
}
```

### Stay Card (the hero component)

```tsx
// components/StayCard.tsx
import { useState } from 'react';
import { View, Text, Image, Pressable, ScrollView, Dimensions, StyleSheet } from 'react-native';
import { SaveHeart } from './SaveHeart';
import { RatingRow } from './RatingRow';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const W = Dimensions.get('window').width - 32;      // 16pt side margins
const H = (W * 3) / 4;                              // 4:3 aspect

type Props = {
  photos: string[];
  title: string;
  host: string;
  dates: string;
  pricePerNight: number;
  rating: number;
  reviews: number;
};

export function StayCard({ photos, title, host, dates, pricePerNight, rating, reviews }: Props) {
  const [saved, setSaved] = useState(false);
  const [idx, setIdx] = useState(0);

  return (
    <View style={{ width: W, marginHorizontal: 16, marginBottom: 24 }}>
      {/* Photo carousel */}
      <View style={{ width: W, height: H, borderRadius: 16, overflow: 'hidden' }}>
        <ScrollView
          horizontal
          pagingEnabled
          showsHorizontalScrollIndicator={false}
          onMomentumScrollEnd={(e) => setIdx(Math.round(e.nativeEvent.contentOffset.x / W))}
        >
          {photos.map((uri, i) => (
            <Image key={i} source={{ uri }} style={{ width: W, height: H }} />
          ))}
        </ScrollView>
        <View style={styles.dots}>
          {photos.slice(0, 6).map((_, i) => (
            <View key={i} style={[styles.dot, i === idx && styles.dotActive]} />
          ))}
        </View>
        <View style={styles.heart}>
          <SaveHeart isSaved={saved} onToggle={() => setSaved((s) => !s)} />
        </View>
      </View>

      <View style={{ marginTop: 8 }}>
        <RatingRow rating={rating} reviews={reviews} />
      </View>
      <Text style={[typography.cardTitle, { marginTop: 2 }]} numberOfLines={1}>{title}</Text>
      <Text style={[typography.meta, { marginTop: 2 }]} numberOfLines={1}>{host}</Text>
      <Text style={[typography.meta, { marginTop: 6 }]}>{dates}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 4, marginTop: 4 }}>
        <Text style={typography.priceInline}>${pricePerNight}</Text>
        <Text style={{ ...typography.bodySmall, fontSize: 15 }}>night</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  dots:      { position: 'absolute', bottom: 12, width: '100%', flexDirection: 'row', justifyContent: 'center', gap: 4 },
  dot:       { width: 6, height: 6, borderRadius: 3, backgroundColor: 'rgba(255,255,255,0.5)' },
  dotActive: { backgroundColor: '#FFFFFF' },
  heart:     { position: 'absolute', top: 12, right: 12 },
});
```

### Search Pill (Explore top)

```tsx
// components/SearchPill.tsx
import { Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SearchPill({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        marginHorizontal: 16,
        height: 56, borderRadius: 500,
        backgroundColor: colors.canvas,
        borderWidth: 0.5, borderColor: colors.divider,
        flexDirection: 'row', alignItems: 'center', paddingHorizontal: 20, gap: 12,
        shadowColor: '#000', shadowOpacity: 0.08, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
        elevation: 3,
        opacity: pressed ? 0.96 : 1,
      })}
    >
      <Ionicons name="search" size={16} color={colors.ink} />
      <View style={{ flex: 1 }}>
        <Text style={{ fontFamily: 'AirbnbCereal-Bold', fontSize: 14, color: colors.ink }}>Where to?</Text>
        <Text style={{ fontFamily: 'AirbnbCereal-Book', fontSize: 12, color: colors.foggy, marginTop: 2 }}>
          Anywhere · Any week · Add guests
        </Text>
      </View>
      <View style={{ width: 36, height: 36, borderRadius: 18, borderWidth: 1, borderColor: colors.divider, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="options-outline" size={16} color={colors.ink} />
      </View>
    </Pressable>
  );
}
```

### Category Bar

```tsx
// components/CategoryBar.tsx
import { ScrollView, Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Cat = { icon: keyof typeof Ionicons.glyphMap; label: string };

export function CategoryBar({ items, selected, onSelect }: { items: Cat[]; selected: number; onSelect: (i: number) => void }) {
  return (
    <View style={{ height: 72, backgroundColor: colors.canvas, borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ paddingHorizontal: 16, gap: 32 }}>
        {items.map((cat, i) => {
          const isSel = i === selected;
          const tint = isSel ? colors.ink : '#717171';
          return (
            <Pressable key={cat.label} onPress={() => onSelect(i)} style={{ alignItems: 'center', justifyContent: 'flex-end', paddingTop: 8 }}>
              <Ionicons name={cat.icon} size={24} color={tint} />
              <Text style={[typography.chip, { color: tint, marginTop: 6 }]}>{cat.label}</Text>
              <View style={{ marginTop: 8, height: 2, width: '120%', backgroundColor: isSel ? colors.ink : 'transparent' }} />
            </Pressable>
          );
        })}
      </ScrollView>
    </View>
  );
}
```

### Sticky Booking Footer

```tsx
// components/BookingFooter.tsx
import { View, Text, Pressable, Platform } from 'react-native';
import { BlurView } from 'expo-blur';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BookingFooter({ totalPrice, dateRange, onReserve }: { totalPrice: number; dateRange: string; onReserve: () => void }) {
  return (
    <BlurView intensity={80} tint="light" style={{ borderTopWidth: 0.5, borderTopColor: colors.divider, paddingBottom: 24 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 24, height: 80 }}>
        <View>
          <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 4 }}>
            <Text style={{ fontFamily: 'AirbnbCereal-Bold', fontSize: 16, color: colors.ink }}>${totalPrice}</Text>
            <Text style={typography.bodySmall}>total</Text>
          </View>
          <Text style={[typography.bodySmall, { textDecorationLine: 'underline', marginTop: 2 }]}>{dateRange}</Text>
        </View>
        <Pressable
          onPress={() => {
            Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
            onReserve();
          }}
          style={({ pressed }) => ({
            backgroundColor: pressed ? colors.coralPressed : colors.coral,
            borderRadius: 8, paddingVertical: 14, paddingHorizontal: 28,
            transform: [{ scale: pressed ? 0.98 : 1 }],
          })}
        >
          <Text style={typography.button}>Reserve</Text>
        </Pressable>
      </View>
    </BlurView>
  );
}
```

### Map Price Bubble

```tsx
// components/MapPriceBubble.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';

type Props = { price: number; state?: 'default' | 'visited' | 'selected' };

export function MapPriceBubble({ price, state = 'default' }: Props) {
  const bg = state === 'selected' ? colors.coral : state === 'visited' ? colors.ink : '#FFFFFF';
  const fg = state === 'default' ? colors.ink : '#FFFFFF';
  return (
    <View
      style={{
        backgroundColor: bg, borderRadius: 12, paddingVertical: 6, paddingHorizontal: 10,
        shadowColor: '#000', shadowOpacity: 0.2, shadowRadius: 6, shadowOffset: { width: 0, height: 2 }, elevation: 3,
      }}
    >
      <Text style={{ fontFamily: 'AirbnbCereal-Bold', fontSize: 14, color: fg }}>${price}</Text>
    </View>
  );
}
```

## 4. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.coral,
        tabBarInactiveTintColor: '#717171',
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="light" style={{ flex: 1, backgroundColor: 'rgba(255,255,255,0.92)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'AirbnbCereal-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Explore',   tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'search' : 'search-outline'}          size={24} color={color} /> }} />
      <Tabs.Screen name="wishlists" options={{ title: 'Wishlists', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'heart'  : 'heart-outline'}           size={24} color={color} /> }} />
      <Tabs.Screen name="trips"     options={{ title: 'Trips',     tabBarIcon: ({ color })          => <Ionicons name="airplane"                                        size={22} color={color} /> }} />
      <Tabs.Screen name="inbox"     options={{ title: 'Inbox',     tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'chatbubble' : 'chatbubble-outline'}   size={24} color={color} /> }} />
      <Tabs.Screen name="profile"   options={{ title: 'Profile',   tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'person-circle' : 'person-circle-outline'} size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// Save heart tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Reserve CTA tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Booking confirmed
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Tab switch
Haptics.selectionAsync();

// Save heart bounce
scale.value = withSequence(withSpring(1.25, { damping: 8 }), withSpring(1, { damping: 8 }));

// Search pill → modal transition via expo-router presentation
// router.push({ pathname: '/search', params: { presentation: 'transparentModal' } });
```

## 6. Icon Library

Use `@expo/vector-icons` — ships with Ionicons, Feather, MaterialCommunityIcons, etc. Map to Airbnb's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Save (unsaved/saved) | `heart-outline` / `heart` |
| Rating star | `star` |
| Search | `search` / `search-outline` |
| Filters | `options-outline` |
| Show all photos | `grid-outline` |
| Superhost | `shield-checkmark` |
| Explore tab | `search-outline` / `search` |
| Wishlists tab | `heart-outline` / `heart` |
| Trips tab | `airplane` |
| Inbox tab | `chatbubble-outline` / `chatbubble` |
| Profile tab | `person-circle-outline` / `person-circle` |
| Wifi | `wifi` |
| TV | `tv-outline` |
| Parking | `car-outline` |
| Back | `chevron-back` |
| Share | `share-outline` |

## 7. Platform Notes

- **iOS-first blur**: Use `expo-blur` `tint="light"` at intensity 80 for the sticky booking footer and tab bar. Android falls back to an opaque `rgba(255,255,255,0.96)` background.
- **Status bar**: Set `<StatusBar style="dark" />` from `expo-status-bar` on light-canvas screens.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The sticky booking footer must respect the home indicator — use `useSafeAreaInsets().bottom`.
- **Dynamic Type**: React Native respects user font-scaling by default. Set `allowFontScaling={false}` only on rating numbers, tab labels, chip labels, and map price bubbles where layout breaks.
- **Accessibility**: Add `accessibilityRole="button"` + descriptive `accessibilityLabel` on the save heart (`"Save, button"` / `"Saved, button"`); group the stay card as a single element with a combined label for VoiceOver.
- **Photo carousel performance**: For large lists, swap `ScrollView` for `FlatList` with `pagingEnabled` or use `react-native-reanimated-carousel` for 60fps swipes on older devices.
- **Dark mode**: use `useColorScheme()` to switch the token object between the light and dark palettes — Primary Coral stays identical across both.
