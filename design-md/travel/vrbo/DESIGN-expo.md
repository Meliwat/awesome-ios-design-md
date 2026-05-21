# Vrbo (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Vrbo's visual language into paste-ready Expo / React Native code: a design-token module, the signature photo gallery, the sticky booking bar, listing cards, map price pins, and the bottom tab bar.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `expo-image`, `expo-blur`, `react-native-maps`, and `react-native-reanimated` v3. Vrbo uses the **system font** (San Francisco on iOS) — no custom fonts to bundle.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceGray:    '#F3F5F8',
  surfacePressed: '#E7EBF1',
  divider:        '#E1E5EB',

  // Surfaces (dark)
  darkCanvas:     '#101317',
  darkSurface1:   '#181C22',
  darkSurface2:   '#21262E',
  darkDivider:    '#2A3038',

  // Text
  textPrimary:    '#15181D',
  textSecondary:  '#5A6470',
  textTertiary:   '#8A93A0',
  darkTextPrimary:   '#EDEFF2',
  darkTextSecondary: '#A3AAB4',

  // Brand
  blue:        '#245ABC',
  bluePressed: '#1B4794',
  blueBright:  '#4F8BF0',
  skyBlue:     '#1D6FB8',

  // Accent & semantic
  goldStar: '#F2B01E', // the only warm color — constant across themes
  success:  '#1F9D57',
  error:    '#E04444',
  warning:  '#E8920C',
} as const;

export const pinStyle = {
  default:  { bg: colors.blue,         fg: '#FFFFFF',  border: 'transparent' },
  viewed:   { bg: colors.darkSurface2, fg: colors.darkTextSecondary, border: colors.darkDivider },
  selected: { bg: colors.goldStar,     fg: '#1A1206',  border: 'transparent' },
} as const;

export const badgeStyle = {
  premier: { fg: colors.blueBright, bg: 'rgba(36,90,188,0.16)',  border: 'rgba(79,139,240,0.35)' },
  instant: { fg: '#4ED98A',         bg: 'rgba(31,157,87,0.16)',  border: 'rgba(78,217,138,0.30)' },
} as const;
```

## 2. Typography

Vrbo ships **no custom typeface** — use the system font. Price is one of the heaviest styles on every screen.

```ts
// theme/typography.ts
import { Platform, type TextStyle } from 'react-native';

const sys = Platform.select({ ios: 'System', default: 'sans-serif' });
const primary = { color: '#15181D' } satisfies TextStyle;

export const typography = {
  largeTitle:   { ...primary, fontFamily: sys, fontSize: 32, fontWeight: '800', letterSpacing: -0.4 },
  priceHero:    { ...primary, fontFamily: sys, fontSize: 26, fontWeight: '800', letterSpacing: -0.3 },
  listingTitle: { ...primary, fontFamily: sys, fontSize: 21, fontWeight: '700', letterSpacing: -0.3 },
  section:      { ...primary, fontFamily: sys, fontSize: 18, fontWeight: '700', letterSpacing: -0.2 },
  body:         { ...primary, fontFamily: sys, fontSize: 16, fontWeight: '400', lineHeight: 24 },
  cardTitle:    { ...primary, fontFamily: sys, fontSize: 15, fontWeight: '600' },
  factValue:    { ...primary, fontFamily: sys, fontSize: 16, fontWeight: '700' },
  meta:         { color: '#5A6470', fontFamily: sys, fontSize: 14, fontWeight: '400' },
  badge:        { fontFamily: sys, fontSize: 12, fontWeight: '700', letterSpacing: 0.2 },
  button:       { color: '#FFFFFF', fontFamily: sys, fontSize: 16, fontWeight: '700' },
  tab:          { fontFamily: sys, fontSize: 10, fontWeight: '500', letterSpacing: 0.1 },
  caption:      { color: '#5A6470', fontFamily: sys, fontSize: 12, fontWeight: '500' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Photo Gallery (the core component)

```tsx
// components/PhotoGallery.tsx
import { useState } from 'react';
import { View, Text, Pressable, useWindowDimensions, FlatList } from 'react-native';
import { Image } from 'expo-image';
import { BlurView } from 'expo-blur';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PhotoGallery({
  photos, onBack, onShare, saved, onToggleSave,
}: { photos: string[]; onBack: () => void; onShare: () => void; saved: boolean; onToggleSave: () => void }) {
  const { width } = useWindowDimensions();
  const [index, setIndex] = useState(0);

  return (
    <View style={{ height: 290, width }}>
      <FlatList
        data={photos}
        horizontal
        pagingEnabled
        showsHorizontalScrollIndicator={false}
        keyExtractor={(_, i) => String(i)}
        onMomentumScrollEnd={(e) => setIndex(Math.round(e.nativeEvent.contentOffset.x / width))}
        renderItem={({ item }) => (
          <Image source={{ uri: item }} style={{ width, height: 290 }} contentFit="cover" />
        )}
      />
      <LinearGradient
        colors={['transparent', 'rgba(16,19,23,0.55)']}
        style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: 145 }}
        pointerEvents="none"
      />
      <View style={{ position: 'absolute', top: 54, left: 16, right: 16, flexDirection: 'row', justifyContent: 'space-between' }}>
        <GalleryButton icon="chevron-back" onPress={onBack} />
        <View style={{ flexDirection: 'row', gap: 10 }}>
          <GalleryButton icon="share-outline" onPress={onShare} />
          <GalleryButton icon={saved ? 'heart' : 'heart-outline'} tint={saved ? colors.error : '#FFF'} onPress={onToggleSave} />
        </View>
      </View>
      <View style={{ position: 'absolute', bottom: 14, left: 0, right: 0, flexDirection: 'row', justifyContent: 'center', gap: 5 }}>
        {photos.map((_, i) => (
          <View key={i} style={{ width: i === index ? 18 : 6, height: 6, borderRadius: 3,
            backgroundColor: i === index ? '#FFF' : 'rgba(255,255,255,0.45)' }} />
        ))}
      </View>
      <View style={{ position: 'absolute', bottom: 14, right: 16, flexDirection: 'row', alignItems: 'center', gap: 6,
        backgroundColor: 'rgba(16,19,23,0.7)', paddingHorizontal: 11, paddingVertical: 5, borderRadius: 999 }}>
        <Ionicons name="images-outline" size={12} color="#FFF" />
        <Text style={[typography.caption, { color: '#FFF' }]}>{index + 1} / {photos.length}</Text>
      </View>
    </View>
  );
}

function GalleryButton({ icon, tint = '#FFF', onPress }: { icon: any; tint?: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} hitSlop={8}>
      <BlurView intensity={28} tint="dark" style={{ width: 36, height: 36, borderRadius: 18, alignItems: 'center', justifyContent: 'center', overflow: 'hidden' }}>
        <Ionicons name={icon} size={18} color={tint} />
      </BlurView>
    </Pressable>
  );
}
```

### Sticky Booking Bar

```tsx
// components/BookingBar.tsx
import { View, Text, Pressable } from 'react-native';
import { BlurView } from 'expo-blur';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BookingBar({
  price, dateRange, onDates, onBook,
}: { price: number; dateRange: string; onDates: () => void; onBook: () => void }) {
  return (
    <BlurView intensity={40} tint="dark" style={{
      height: 76, flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
      paddingHorizontal: 18, borderTopWidth: 1, borderTopColor: colors.darkDivider,
    }}>
      <View>
        <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 4 }}>
          <Text style={[typography.priceHero, { color: colors.darkTextPrimary }]}>${price}</Text>
          <Text style={{ fontSize: 13, color: colors.darkTextSecondary, marginBottom: 3 }}>/ night</Text>
        </View>
        <Pressable onPress={onDates}>
          <Text style={{ fontSize: 12, color: colors.darkTextSecondary, textDecorationLine: 'underline' }}>{dateRange}</Text>
        </Pressable>
      </View>
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onBook(); }}
        style={({ pressed }) => ({
          backgroundColor: pressed ? colors.bluePressed : colors.blue,
          paddingHorizontal: 26, paddingVertical: 13, borderRadius: 10,
          transform: [{ scale: pressed ? 0.98 : 1 }],
        })}
      >
        <Text style={typography.button}>Book now</Text>
      </Pressable>
    </BlurView>
  );
}
```

### Listing Card (results)

```tsx
// components/ListingCard.tsx
import { View, Text, Pressable } from 'react-native';
import { Image } from 'expo-image';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ListingCard({
  imageUri, title, location, rating, reviews, price, saved, onToggleSave,
}: {
  imageUri: string; title: string; location: string; rating: number;
  reviews: number; price: number; saved: boolean; onToggleSave: () => void;
}) {
  return (
    <View style={{ gap: 8 }}>
      <View>
        <Image source={{ uri: imageUri }} style={{ width: '100%', aspectRatio: 4 / 3, borderRadius: 14 }} contentFit="cover" />
        <Pressable onPress={onToggleSave} style={{ position: 'absolute', top: 10, right: 10 }} hitSlop={8}>
          <BlurView intensity={28} tint="dark" style={{ width: 36, height: 36, borderRadius: 18, alignItems: 'center', justifyContent: 'center', overflow: 'hidden' }}>
            <Ionicons name={saved ? 'heart' : 'heart-outline'} size={17} color={saved ? colors.error : '#FFF'} />
          </BlurView>
        </Pressable>
      </View>
      <Text style={[typography.caption, { color: colors.darkTextSecondary }]}>Entire home</Text>
      <Text style={[typography.cardTitle, { color: colors.darkTextPrimary }]}>{title}</Text>
      <Text style={[typography.meta, { color: colors.darkTextSecondary }]}>{location}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <Ionicons name="star" size={12} color={colors.goldStar} />
        <Text style={{ fontSize: 14, fontWeight: '700', color: colors.darkTextPrimary }}>{rating.toFixed(1)}</Text>
        <Text style={[typography.meta, { color: colors.darkTextSecondary }]}>· {reviews}</Text>
      </View>
      <Text style={{ color: colors.darkTextPrimary }}>
        <Text style={{ fontSize: 16, fontWeight: '700' }}>${price}</Text>
        <Text style={{ fontSize: 14 }}> night</Text>
      </Text>
    </View>
  );
}
```

### Map Price Pin + Trust Badge

```tsx
// components/MapPricePin.tsx
import { View, Text } from 'react-native';
import { pinStyle, badgeStyle } from '../theme/colors';
import { typography } from '../theme/typography';

export function MapPricePin({ price, state }: { price: number; state: keyof typeof pinStyle }) {
  const s = pinStyle[state];
  return (
    <View style={{
      backgroundColor: s.bg, borderRadius: 999, paddingHorizontal: 12, paddingVertical: 7,
      borderWidth: state === 'viewed' ? 1 : 0, borderColor: s.border,
      transform: [{ scale: state === 'selected' ? 1.12 : 1 }],
      shadowColor: '#000', shadowOpacity: state === 'selected' ? 0.35 : 0.2, shadowRadius: 8, shadowOffset: { width: 0, height: 3 },
    }}>
      <Text style={{ fontSize: 14, fontWeight: '700', color: s.fg }}>${price}</Text>
    </View>
  );
}

export function TrustBadge({ kind }: { kind: keyof typeof badgeStyle }) {
  const s = badgeStyle[kind];
  return (
    <View style={{ backgroundColor: s.bg, borderColor: s.border, borderWidth: 1, borderRadius: 6, paddingHorizontal: 9, paddingVertical: 4 }}>
      <Text style={[typography.badge, { color: s.fg }]}>{kind === 'premier' ? 'Premier Host' : 'Instant Book'}</Text>
    </View>
  );
}
```

### Trip Board Card

```tsx
// components/TripBoardCard.tsx
import { View, Text } from 'react-native';
import { Image } from 'expo-image';
import { colors } from '../theme/colors';

export function TripBoardCard({
  thumb, name, facts, rating, tripName,
}: { thumb: string; name: string; facts: string; rating: number; tripName: string }) {
  return (
    <View style={{
      flexDirection: 'row', gap: 12, padding: 12, borderRadius: 12,
      backgroundColor: colors.darkSurface2, borderWidth: 1, borderColor: colors.darkDivider,
    }}>
      <Image source={{ uri: thumb }} style={{ width: 64, height: 64, borderRadius: 8 }} contentFit="cover" />
      <View style={{ flex: 1 }}>
        <Text style={{ fontSize: 14, fontWeight: '700', color: colors.darkTextPrimary }}>{name}</Text>
        <Text style={{ fontSize: 12, color: colors.darkTextSecondary, marginTop: 3 }}>{facts}</Text>
        <Text style={{ fontSize: 11, fontWeight: '700', color: colors.goldStar, marginTop: 8 }}>
          ★ {rating.toFixed(1)} · Saved to "{tripName}"
        </Text>
      </View>
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
        tabBarActiveTintColor: colors.blueBright,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.darkCanvas, borderTopWidth: 0.5, borderTopColor: colors.darkDivider },
        tabBarLabelStyle: { fontSize: 10, fontWeight: '500', letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"         options={{ title: 'Search',        tabBarIcon: ({ color }) => <Ionicons name="search"               size={22} color={color} /> }} />
      <Tabs.Screen name="trips"         options={{ title: 'Trips',         tabBarIcon: ({ color }) => <Ionicons name="briefcase-outline"    size={22} color={color} /> }} />
      <Tabs.Screen name="inbox"         options={{ title: 'Inbox',         tabBarBadge: '', tabBarIcon: ({ color }) => <Ionicons name="chatbubble-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="notifications" options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Ionicons name="notifications-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="account"       options={{ title: 'Account',       tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { FadeInUp, useSharedValue, useAnimatedStyle, withSpring, withTiming } from 'react-native-reanimated';

// Gallery progress dot — width animates on page change (interpolate index → width)

// Booking-bar price update — cross-dissolve (Reanimated opacity) over 250ms
opacity.value = withTiming(0, { duration: 120 }, () => { /* swap value */ opacity.value = withTiming(1, { duration: 130 }); });

// Save heart pop
const scale = useSharedValue(1);
const onSave = () => {
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
  scale.value = withSpring(1.25, { damping: 6 }, () => { scale.value = withSpring(1); });
};

// Map pin select — scale + recolor; peek card slides up
// Use @gorhom/bottom-sheet for the peek card (250ms slide-up)

// Results card stagger
// entering={FadeInUp.delay(i * 60).duration(220)}

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);  // Book now, save
Haptics.selectionAsync();                                // date range endpoint, tab change
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The map price pin and progress dots are drawn with plain `View`s; the gold star uses the `star` glyph.

| Purpose | Ionicons |
|---------|----------|
| Search (tab) | `search` |
| Trips (tab) | `briefcase-outline` |
| Inbox (tab) | `chatbubble-outline` |
| Notifications (tab) | `notifications-outline` |
| Account (tab) | `person-circle-outline` |
| Back (gallery) | `chevron-back` |
| Share | `share-outline` |
| Save | `heart` / `heart-outline` |
| Photo grid / counter | `images-outline` |
| Rating star | `star` |
| Guests | `people-outline` |
| Bedrooms | `bed-outline` |
| Bathrooms | `water-outline` |
| Whole home | `home-outline` |
| Filters | `options-outline` |
| Map toggle | `map-outline` |
| Calendar / dates | `calendar-outline` |
| Location | `location-outline` |
| Instant Book | `flash` |

## 7. Platform Notes

- **Font choice**: Vrbo uses the OS system font — `fontFamily: 'System'` on iOS, `'sans-serif'` on Android. Do not bundle a custom typeface
- **Status bar**: gallery extends under the status bar — use `<StatusBar style="light" />` while a gallery is in view (white overlay controls), `"dark"`/`"light"` per theme elsewhere
- **Safe area**: wrap screens in `SafeAreaView`; the booking bar and tab bar need safe-area padding; gallery and map render full-bleed with controls inset
- **expo-image**: use `expo-image` for gallery + card photos (better caching/decoding); set `contentFit="cover"` and preserve aspect ratio
- **Maps**: use `react-native-maps` with custom `Marker` children rendering the `MapPricePin`; on press, animate the pin and present a `@gorhom/bottom-sheet` peek card
- **Dynamic Type**: RN respects system scale on `<Text>`; set `allowFontScaling={false}` on tab labels, trust badges, the photo counter, and progress-dot legends (layout-sensitive)
- **Keyboard**: use `KeyboardAvoidingView` on search and message screens
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1`; links/pins/outline buttons resolve to `blueBright` on dark — the "Book now" fill stays `blue`; the gold star is constant across schemes
- **Accessibility**: announce gallery position ("Photo 1 of 42"); label cards with title + rating + price; trust badges carry text, not color alone
- **Performance**: virtualize results with `FlashList`/`FlatList`; lazy-load gallery photos; debounce map-region price-pin refetch
