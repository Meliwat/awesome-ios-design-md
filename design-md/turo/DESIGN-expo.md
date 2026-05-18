# Turo (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Turo's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets for the signature photo hero, host card, and sticky Book bar.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-image`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  turoPurple:        '#593BFB', // booking action
  turoPurpleBright:  '#7C5CFF', // link / gradient hi
  turoPurplePressed: '#4A2FD6',
  turoTeal:          '#5CE0B8', // trust / value
  turoTealDeep:      '#1FB890',

  // Surfaces (light)
  canvas:        '#FFFFFF',
  surface1:      '#F6F5FA',
  surface2:      '#FFFFFF',
  pressed:       '#ECEAF4',
  divider:       '#E6E4EF',

  // Surfaces (dark)
  darkCanvas:    '#0F0F12',
  darkSurface1:  '#1A1A1F',
  darkSurface2:  '#25252C',
  darkDivider:   '#2D2D35',

  // Text
  textPrimary:   '#15131F',
  textSecondary: '#6B6878',
  textTertiary:  '#9C99AB',
  darkTextPrimary:   '#F2F1F6',
  darkTextSecondary: '#A4A2B3',
  darkTextTertiary:  '#6F6D7E',
  onPurple:      '#FFFFFF',
  onTeal:        '#06231B',

  // Semantic
  success:       '#1FB890',
  successDark:   '#5CE0B8',
  error:         '#F5536B',
  star:          '#FFB400',
} as const;

export type TuroColor = keyof typeof colors;
```

## 2. Typography

Turo's brand face is a geometric humanist sans. If licensed, bundle it; otherwise ship **Manrope** (SIL OFL). Prices/counts use `fontVariant: ['tabular-nums']`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    // swap Manrope-* for the licensed brand face if available — keep the keys
    'Turo-Regular':   require('../assets/fonts/Manrope-Regular.ttf'),
    'Turo-Medium':    require('../assets/fonts/Manrope-Medium.ttf'),
    'Turo-SemiBold':  require('../assets/fonts/Manrope-SemiBold.ttf'),
    'Turo-Bold':      require('../assets/fonts/Manrope-Bold.ttf'),
    'Turo-ExtraBold': require('../assets/fonts/Manrope-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tnum = { fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] };

export const typography = {
  display:    { fontFamily: 'Turo-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  priceHero:  { fontFamily: 'Turo-ExtraBold', fontSize: 26, lineHeight: 29, letterSpacing: -0.3, ...tnum },
  carTitle:   { fontFamily: 'Turo-ExtraBold', fontSize: 23, lineHeight: 28, letterSpacing: -0.3 },
  section:    { fontFamily: 'Turo-Bold',      fontSize: 18, lineHeight: 24, letterSpacing: -0.1 },
  body:       { fontFamily: 'Turo-Regular',   fontSize: 16, lineHeight: 24 },
  bodyEmph:   { fontFamily: 'Turo-SemiBold',  fontSize: 16, lineHeight: 24 },
  cardTitle:  { fontFamily: 'Turo-SemiBold',  fontSize: 15, lineHeight: 20 },
  cardPrice:  { fontFamily: 'Turo-ExtraBold', fontSize: 14, lineHeight: 17, ...tnum },
  meta:       { fontFamily: 'Turo-Regular',   fontSize: 14, lineHeight: 20 },
  eyebrow:    { fontFamily: 'Turo-Bold',      fontSize: 13, lineHeight: 16, letterSpacing: 0.5 },
  caption:    { fontFamily: 'Turo-Medium',    fontSize: 12, lineHeight: 16 },
  button:     { fontFamily: 'Turo-ExtraBold', fontSize: 16, lineHeight: 16, letterSpacing: 0.1 },
  tab:        { fontFamily: 'Turo-SemiBold',  fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
  ratingNum:  { fontFamily: 'Turo-Bold',      fontSize: 13, lineHeight: 13, ...tnum },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Photo Hero (full-bleed carousel)

```tsx
// components/CarPhotoHero.tsx
import { useState } from 'react';
import { View, Pressable, FlatList, useWindowDimensions } from 'react-native';
import { Image } from 'expo-image';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function CarPhotoHero({
  photos, isFavorite, onToggleFavorite, onBack, onShare,
}: {
  photos: string[]; isFavorite: boolean;
  onToggleFavorite: () => void; onBack: () => void; onShare: () => void;
}) {
  const { width } = useWindowDimensions();
  const [page, setPage] = useState(0);

  return (
    <View style={{ height: 260, backgroundColor: colors.darkSurface2 }}>
      <FlatList
        data={photos} horizontal pagingEnabled showsHorizontalScrollIndicator={false}
        keyExtractor={(_, i) => String(i)}
        onMomentumScrollEnd={(e) => setPage(Math.round(e.nativeEvent.contentOffset.x / width))}
        renderItem={({ item }) => (
          <Image source={{ uri: item }} style={{ width, height: 260 }} contentFit="cover" />
        )}
      />

      {/* Page dots — active is an 18px pill */}
      <View style={{ position: 'absolute', bottom: 14, left: 0, right: 0,
        flexDirection: 'row', justifyContent: 'center', gap: 5 }}>
        {photos.map((_, i) => (
          <View key={i} style={{
            width: i === page ? 18 : 6, height: 6, borderRadius: 3,
            backgroundColor: i === page ? '#FFFFFF' : 'rgba(255,255,255,0.45)',
          }} />
        ))}
      </View>

      {/* Floating controls */}
      <View style={{ position: 'absolute', top: 56, left: 16, right: 16,
        flexDirection: 'row', justifyContent: 'space-between' }}>
        <HeroBtn icon="chevron-back" onPress={onBack} />
        <View style={{ flexDirection: 'row', gap: 10 }}>
          <HeroBtn icon="share-outline" onPress={onShare} />
          <HeroBtn icon={isFavorite ? 'heart' : 'heart-outline'}
            tint={isFavorite ? colors.turoTeal : '#FFFFFF'} onPress={onToggleFavorite} />
        </View>
      </View>
    </View>
  );
}

function HeroBtn({ icon, onPress, tint = '#FFFFFF' }: { icon: any; onPress: () => void; tint?: string }) {
  return (
    <Pressable onPress={onPress}>
      <BlurView intensity={28} tint="dark" style={{
        width: 38, height: 38, borderRadius: 19, overflow: 'hidden',
        alignItems: 'center', justifyContent: 'center', backgroundColor: 'rgba(15,15,18,0.45)',
      }}>
        <Ionicons name={icon} size={18} color={tint} />
      </BlurView>
    </Pressable>
  );
}
```

### Specs Strip

```tsx
// components/SpecsStrip.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Spec = { icon: any; value: string; label: string };

export function SpecsStrip({ specs }: { specs: Spec[] }) {
  return (
    <View style={{ flexDirection: 'row', gap: 10, marginHorizontal: 20 }}>
      {specs.map((s) => (
        <View key={s.label} style={{
          flex: 1, alignItems: 'center', paddingVertical: 12, gap: 6,
          backgroundColor: colors.darkSurface1, borderRadius: 12,
          borderWidth: 1, borderColor: colors.darkDivider,
        }}>
          <Ionicons name={s.icon} size={18} color={colors.turoTeal} />
          <Text style={{ fontFamily: 'Turo-Bold', fontSize: 13, color: colors.darkTextPrimary }}>{s.value}</Text>
          <Text style={{ fontFamily: 'Turo-Regular', fontSize: 10, color: colors.darkTextSecondary }}>{s.label}</Text>
        </View>
      ))}
    </View>
  );
}
```

### Host Card

```tsx
// components/HostCard.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function HostCard({
  initials, name, allStar, subline, onPress,
}: { initials: string; name: string; allStar: boolean; subline: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{
      flexDirection: 'row', alignItems: 'center', gap: 12, padding: 14,
      backgroundColor: colors.darkSurface1, borderRadius: 14,
      borderWidth: 1, borderColor: colors.darkDivider, marginHorizontal: 20,
    }}>
      <LinearGradient colors={[colors.turoPurpleBright, colors.turoTeal]}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ width: 46, height: 46, borderRadius: 23, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontFamily: 'Turo-ExtraBold', fontSize: 17, color: '#FFFFFF' }}>{initials}</Text>
      </LinearGradient>

      <View style={{ flex: 1, gap: 3 }}>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
          <Text style={[typography.cardTitle, { color: colors.darkTextPrimary }]}>{name}</Text>
          {allStar && (
            <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4,
              paddingHorizontal: 8, paddingVertical: 3, borderRadius: 500,
              backgroundColor: 'rgba(92,224,184,0.16)' }}>
              <Ionicons name="star" size={9} color={colors.turoTeal} />
              <Text style={{ fontFamily: 'Turo-ExtraBold', fontSize: 10, color: colors.turoTeal }}>ALL-STAR HOST</Text>
            </View>
          )}
        </View>
        <Text style={[typography.caption, { color: colors.darkTextSecondary }]}>{subline}</Text>
      </View>
      <Ionicons name="chevron-forward" size={14} color={colors.darkTextTertiary} />
    </Pressable>
  );
}
```

### Sticky Price + Book Bar

```tsx
// components/BookBar.tsx
import { View, Text, Pressable } from 'react-native';
import { BlurView } from 'expo-blur';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BookBar({
  dailyRate, estTotal, onTotalPress, onContinue,
}: { dailyRate: string; estTotal: string; onTotalPress: () => void; onContinue: () => void }) {
  const insets = useSafeAreaInsets();
  return (
    <BlurView intensity={40} tint="dark" style={{
      position: 'absolute', left: 0, right: 0, bottom: 0,
      borderTopWidth: 0.5, borderTopColor: colors.darkDivider,
      backgroundColor: 'rgba(15,15,18,0.92)',
    }}>
      <View style={{
        flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
        paddingHorizontal: 20, paddingTop: 14, paddingBottom: 14 + insets.bottom,
      }}>
        <View style={{ gap: 2 }}>
          <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 4 }}>
            <Text style={{ fontFamily: 'Turo-ExtraBold', fontSize: 20,
              color: colors.darkTextPrimary, fontVariant: ['tabular-nums'] }}>{dailyRate}</Text>
            <Text style={{ fontFamily: 'Turo-Medium', fontSize: 13, color: colors.darkTextSecondary }}>/ day</Text>
          </View>
          <Pressable onPress={onTotalPress}>
            <Text style={{ fontFamily: 'Turo-SemiBold', fontSize: 11,
              color: colors.turoTeal, textDecorationLine: 'underline' }}>{estTotal}</Text>
          </Pressable>
        </View>
        <Pressable onPress={onContinue} style={{
          backgroundColor: colors.turoPurple, paddingHorizontal: 30, height: 44,
          borderRadius: 10, alignItems: 'center', justifyContent: 'center',
        }}>
          <Text style={[typography.button, { color: colors.onPurple }]}>Continue</Text>
        </Pressable>
      </View>
    </BlurView>
  );
}
```

### Search / Listing Card

```tsx
// components/CarCard.tsx
import { View, Text, Pressable } from 'react-native';
import { Image } from 'expo-image';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function CarCard({
  photo, title, ratingLine, price, saved, onToggleSave,
}: { photo: string; title: string; ratingLine: string; price: string; saved: boolean; onToggleSave: () => void }) {
  return (
    <View style={{
      width: 200, borderRadius: 14, overflow: 'hidden',
      backgroundColor: colors.darkSurface2, borderWidth: 1, borderColor: colors.darkDivider,
    }}>
      <View>
        <Image source={{ uri: photo }} style={{ height: 110, width: '100%' }} contentFit="cover" />
        <Pressable onPress={onToggleSave} style={{
          position: 'absolute', top: 8, right: 8, width: 26, height: 26, borderRadius: 13,
          backgroundColor: 'rgba(15,15,18,0.5)', alignItems: 'center', justifyContent: 'center',
        }}>
          <Ionicons name={saved ? 'heart' : 'heart-outline'} size={14}
            color={saved ? colors.turoTeal : '#FFFFFF'} />
        </Pressable>
      </View>
      <View style={{ paddingHorizontal: 12, paddingVertical: 10, gap: 3 }}>
        <Text style={{ fontFamily: 'Turo-Bold', fontSize: 13, color: colors.darkTextPrimary }}>{title}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
          <Ionicons name="star" size={11} color={colors.star} />
          <Text style={{ fontFamily: 'Turo-Regular', fontSize: 11, color: colors.darkTextSecondary }}>{ratingLine}</Text>
        </View>
        <Text style={{ fontFamily: 'Turo-ExtraBold', fontSize: 14, marginTop: 6,
          color: colors.darkTextPrimary, fontVariant: ['tabular-nums'] }}>{price}</Text>
      </View>
    </View>
  );
}
```

### Map Price Pin

```tsx
// components/MapPricePin.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';

type State = 'default' | 'visited' | 'selected';

export function MapPricePin({ price, state = 'default' }: { price: string; state?: State }) {
  const bg = state === 'default' ? colors.turoPurple : state === 'selected' ? '#FFFFFF' : colors.darkSurface2;
  const fg = state === 'default' ? '#FFFFFF' : state === 'selected' ? colors.turoPurple : colors.darkTextSecondary;
  return (
    <View style={{
      paddingHorizontal: 12, paddingVertical: 7, borderRadius: 500, backgroundColor: bg,
      borderWidth: state === 'visited' ? 1 : 0, borderColor: colors.darkDivider,
      shadowColor: '#000', shadowOpacity: 0.5, shadowRadius: 8, shadowOffset: { width: 0, height: 3 }, elevation: 4,
    }}>
      <Text style={{ fontFamily: 'Turo-ExtraBold', fontSize: 13, color: fg, fontVariant: ['tabular-nums'] }}>{price}</Text>
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
        tabBarActiveTintColor: colors.turoPurple,        // Turo Purple active
        tabBarInactiveTintColor: colors.darkTextTertiary,
        tabBarStyle: { backgroundColor: colors.darkCanvas, borderTopWidth: 0.5, borderTopColor: colors.darkDivider },
        tabBarLabelStyle: { fontFamily: 'Turo-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Search',    tabBarIcon: ({ color }) => <Ionicons name="search"    size={22} color={color} /> }} />
      <Tabs.Screen name="trips"     options={{ title: 'Trips',     tabBarIcon: ({ color }) => <Ionicons name="car"       size={22} color={color} /> }} />
      <Tabs.Screen name="favorites" options={{ title: 'Favorites', tabBarIcon: ({ color }) => <Ionicons name="heart-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="inbox"     options={{ title: 'Inbox',     tabBarIcon: ({ color }) => <Ionicons name="chatbubble-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="more"      options={{ title: 'More',      tabBarIcon: ({ color }) => <Ionicons name="menu"      size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { FadeIn, useSharedValue, useAnimatedStyle, withTiming, withSequence, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Photo carousel — animate the active page dot width 6 → 18
// width: withTiming(active ? 18 : 6, { duration: 200 }) + paging tick:
// Haptics.selectionAsync() on page change

// Favorite tap — heart pop + teal fill
const fav = useSharedValue(1);
const favStyle = useAnimatedStyle(() => ({ transform: [{ scale: fav.value }] }));
function toggleFav() {
  fav.value = withSequence(withTiming(1.25, { duration: 140 }), withSpring(1, { damping: 6 }));
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
}

// Listing open — shared element-style: card photo expands into the 260px hero
// Use expo-router's shared element transition or a manual Animated layout (320ms easeOut)

// Map pin select — scale 1.0 → 1.12 + recolor to white selected (150ms)
const pin = useSharedValue(1);
// pin.value = withTiming(1.12, { duration: 150 }); + recolor via state

// Book bar CTA press: scale 1 → 0.98 (120ms) + Purple → Pressed Purple
// Filter chip toggle: backgroundColor cross-fade to purple (150ms)
// Sheet present (price breakdown): @gorhom/bottom-sheet or Modal slide 320ms + scrim
// Booking confirmed: Haptics.notificationAsync(Success)
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). For brand-specific glyphs (instant-book lightning) Ionicons covers it; use `expo-image` for the host avatar.

| Purpose | Ionicons |
|---------|----------|
| Search (tab) | `search` |
| Trips (tab) | `car` |
| Favorites (tab) | `heart-outline` / `heart` |
| Inbox (tab) | `chatbubble-outline` |
| More (tab) | `menu` |
| Back | `chevron-back` |
| Share | `share-outline` |
| Favorite | `heart-outline` / `heart` |
| Star rating | `star` |
| All-Star badge | `star` |
| Instant book | `flash` |
| Fuel / electric | `flash-outline` / `car-sport` |
| Seats | `people` |
| Transmission | `settings` |
| Doors | `car-outline` |
| Location | `location-outline` |
| Trip dates | `calendar-outline` |
| Chevron (row) | `chevron-forward` |
| Filters | `options-outline` |

## 7. Platform Notes

- **Font choice**: Turo's brand face is proprietary — license to ship it, or use Manrope (SIL OFL) and keep the named ramp identical so layout doesn't shift
- **Tabular numbers**: set `fontVariant: ['tabular-nums']` on prices, est. totals, trip counts, and ratings (already baked into `typography`)
- **Status bar**: `<StatusBar style="light" />` over the photo hero and on the near-black canvas (dark mode is the primary rendering)
- **Safe area**: wrap screens in `SafeAreaProvider`; the Book bar reads `useSafeAreaInsets().bottom` and pins above the home indicator; floating photo controls offset for the notch
- **Dynamic Type**: RN scales `<Text>` — set `allowFontScaling={false}` on tab labels, badge eyebrows, and the rating number (layout-critical); the photo hero never scales
- **One Purple per screen**: the booking CTA is the only loud `colors.turoPurple` fill; trust badges/savings are `colors.turoTeal`
- **Photos sacred**: never apply a tint/overlay to `expo-image` car photos — only thin top/bottom legibility gradients via `expo-linear-gradient`
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` / `darkSurface2`; keep purple/teal constant, brighten links to `turoPurpleBright`
- **Blur**: `expo-blur` carries the Book bar and floating controls; provide a solid fallback for Reduce Transparency and Android (`BlurView` is weaker there — fall back to `rgba(15,15,18,0.92)`)
- **Image performance**: use `expo-image` with `contentFit="cover"` and prefetch the next carousel photo; cache search-card thumbnails
- **Accessibility**: give the photo hero an `accessibilityLabel` ("Photo 1 of 8, Tesla Model 3"), the host card a full trust summary, and the Book CTA a label including rate + est. total
