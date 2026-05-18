# Goodreads (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Goodreads' visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand & interactive
  brown:        '#382110',
  brownDeep:    '#58371F',
  tan:          '#F4F1EA',
  shelfGreen:   '#409D69',
  shelfGreenPressed: '#348355',
  linkTeal:     '#00635D',
  linkTealDark: '#6FB3AD',

  // Rating
  amber:        '#E9A100',
  starRail:     '#D8D2C4',
  starRailDark: '#4A3F2C',

  // Surfaces (light)
  canvas:       '#F4F1EA',
  cardWhite:    '#FFFFFF',
  surfaceSubtle:'#EBE6DA',
  divider:      '#DDD6C7',

  // Surfaces (dark)
  darkCanvas:   '#161310',
  darkSurface1: '#211C16',
  darkSurface2: '#2C261D',
  darkDivider:  '#3A3226',
  brownOnDark:  '#C9883D',

  // Text
  textPrimary:    '#382110',
  textSecondary:  '#6B5E47',
  textTertiary:   '#988B6F',
  darkTextPrimary:'#EDE6D8',
  darkTextSecondary:'#B3A88F',

  // Semantic
  error:        '#D9534F',
} as const;

export type GoodreadsColor = keyof typeof colors;

// Shelf pill style pairs
export const shelfPills = {
  read:    { bg: colors.brown,                       fg: colors.tan,        border: 'transparent' },
  current: { bg: 'rgba(64,157,105,0.18)',            fg: colors.shelfGreen, border: 'rgba(64,157,105,0.4)' },
  want:    { bg: colors.surfaceSubtle,               fg: colors.textSecondary, border: colors.divider },
} as const;
```

## 2. Typography

Load **Merriweather** (editorial: titles, synopses, reviews) and **Lato** (UI chrome) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Merriweather-Regular': require('../assets/fonts/Merriweather-Regular.ttf'),
    'Merriweather-Bold':    require('../assets/fonts/Merriweather-Bold.ttf'),
    'Merriweather-Black':   require('../assets/fonts/Merriweather-Black.ttf'),
    'Lato-Regular':         require('../assets/fonts/Lato-Regular.ttf'),
    'Lato-Bold':            require('../assets/fonts/Lato-Bold.ttf'),
    'Lato-Black':           require('../assets/fonts/Lato-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const ink = { color: '#382110' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...ink, fontFamily: 'Merriweather-Black',   fontSize: 30, lineHeight: 36, letterSpacing: -0.3 },
  bookTitle:   { ...ink, fontFamily: 'Merriweather-Black',   fontSize: 23, lineHeight: 28, letterSpacing: -0.2 },
  section:     { ...ink, fontFamily: 'Merriweather-Bold',    fontSize: 19, lineHeight: 25 },
  subsection:  { ...ink, fontFamily: 'Merriweather-Bold',    fontSize: 17, lineHeight: 23 },
  body:        { ...ink, fontFamily: 'Merriweather-Regular', fontSize: 15, lineHeight: 24 }, // ~1.6
  review:      { ...ink, fontFamily: 'Merriweather-Regular', fontSize: 13, lineHeight: 20 },
  button:      { color: '#FFFFFF', fontFamily: 'Lato-Black',  fontSize: 17, lineHeight: 17, letterSpacing: 0.1 },
  label:       { ...ink, fontFamily: 'Lato-Bold',    fontSize: 14, lineHeight: 18 },
  meta:        { color: '#6B5E47', fontFamily: 'Lato-Regular', fontSize: 13, lineHeight: 18 },
  link:        { color: '#00635D', fontFamily: 'Lato-Bold',    fontSize: 13, lineHeight: 18 },
  caption:     { color: '#988B6F', fontFamily: 'Lato-Regular', fontSize: 12, lineHeight: 16 },
  starCaption: { color: '#6B5E47', fontFamily: 'Lato-Bold',    fontSize: 12, lineHeight: 14 },
  overline:    { color: '#6B5E47', fontFamily: 'Lato-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.6 },
  tab:         { fontFamily: 'Lato-Bold', fontSize: 10, lineHeight: 12, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Book Cover

```tsx
// components/BookCover.tsx
import { Image, View, useColorScheme } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';

export function BookCover({ uri, width = 104 }: { uri?: string; width?: number }) {
  const dark = useColorScheme() === 'dark';
  const height = width * 1.52;
  return (
    <View style={{
      width, height, borderRadius: 3, overflow: 'hidden',
      shadowColor: '#000', shadowOpacity: dark ? 0.55 : 0.18,
      shadowRadius: dark ? 8 : 5, shadowOffset: { width: 0, height: dark ? 6 : 4 },
      elevation: 6,
    }}>
      {uri ? (
        <Image source={{ uri }} style={{ width, height }} resizeMode="cover" />
      ) : (
        <LinearGradient colors={[colors.brownDeep, colors.brown]}
          start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ width, height }} />
      )}
      <View style={{ position: 'absolute', left: 0, top: 0, bottom: 0, width: 6, backgroundColor: 'rgba(0,0,0,0.35)' }} />
    </View>
  );
}
```

### Star Rating (display)

```tsx
// components/StarRating.tsx
import { View, useColorScheme } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function StarRating({ value, size = 15 }: { value: number; size?: number }) {
  const dark = useColorScheme() === 'dark';
  const rail = dark ? colors.starRailDark : colors.starRail;
  return (
    <View style={{ flexDirection: 'row', gap: 1 }}>
      {[0, 1, 2, 3, 4].map((i) => {
        const fill = Math.min(Math.max(value - i, 0), 1);
        return (
          <View key={i} style={{ width: size, height: size }}>
            <Ionicons name="star" size={size} color={rail} style={{ position: 'absolute' }} />
            <View style={{ width: size * fill, overflow: 'hidden' }}>
              <Ionicons name="star" size={size} color={colors.amber} />
            </View>
          </View>
        );
      })}
    </View>
  );
}
```

### Interactive "Rate this Book"

```tsx
// components/RateThisBook.tsx
import { useState } from 'react';
import { Pressable, Text, View, useColorScheme } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const AStar = Animated.createAnimatedComponent(Ionicons);

export function RateThisBook({ onRate }: { onRate?: (n: number) => void }) {
  const [rating, setRating] = useState(0);
  const dark = useColorScheme() === 'dark';

  return (
    <View style={{
      marginHorizontal: 16, padding: 14, borderRadius: 6, alignItems: 'center',
      backgroundColor: dark ? colors.darkSurface1 : colors.cardWhite,
    }}>
      <Text style={typography.overline}>RATE THIS BOOK</Text>
      <View style={{ flexDirection: 'row', gap: 6, marginTop: 10 }}>
        {[1, 2, 3, 4, 5].map((i) => {
          const scale = useSharedValue(1);
          const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
          return (
            <Pressable key={i} onPress={() => {
              setRating(i);
              scale.value = withSpring(1.2, { damping: 6 }, () => { scale.value = withSpring(1); });
              Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
              onRate?.(i);
            }}>
              <Animated.View style={aStyle}>
                <Ionicons name="star" size={28}
                  color={i <= rating ? colors.amber : (dark ? colors.starRailDark : colors.starRail)} />
              </Animated.View>
            </Pressable>
          );
        })}
      </View>
    </View>
  );
}
```

### Want-to-Read CTA Block

```tsx
// components/ShelfCTA.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ShelfCTA() {
  const [shelf, setShelf] = useState<string | null>(null);
  const scale = useSharedValue(1);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <View style={{ marginHorizontal: 16, gap: 8 }}>
      <Pressable onPress={() => {
        setShelf((s) => s ?? 'Want to Read');
        scale.value = withSpring(0.96, { damping: 8 }, () => { scale.value = withSpring(1); });
        Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
      }}>
        <Animated.View style={[{
          height: 44, borderRadius: 4, backgroundColor: colors.shelfGreen,
          flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8,
        }, aStyle]}>
          {shelf ? <Ionicons name="checkmark" size={16} color="#FFF" /> : null}
          <Text style={typography.button}>{shelf ?? 'Want to Read'}</Text>
        </Animated.View>
      </Pressable>

      <View style={{ flexDirection: 'row', borderWidth: 1, borderColor: colors.divider, borderRadius: 4, overflow: 'hidden' }}>
        {['★ Rate', 'Review', 'Shelve ▾'].map((seg, idx) => (
          <View key={seg} style={{
            flex: 1, height: 38, alignItems: 'center', justifyContent: 'center',
            borderRightWidth: idx < 2 ? 1 : 0, borderRightColor: colors.divider,
          }}>
            <Text style={[typography.starCaption, seg === '★ Rate' && { color: colors.amber }]}>{seg}</Text>
          </View>
        ))}
      </View>
    </View>
  );
}
```

### Shelf Status Pill

```tsx
// components/ShelfPill.tsx
import { Text, View } from 'react-native';
import { typography } from '../theme/typography';
import { shelfPills } from '../theme/colors';

export function ShelfPill({ kind }: { kind: keyof typeof shelfPills }) {
  const s = shelfPills[kind];
  const label = kind === 'read' ? 'Read' : kind === 'current' ? 'Currently Reading' : 'Want to Read';
  return (
    <View style={{
      paddingHorizontal: 14, paddingVertical: 7, borderRadius: 999,
      backgroundColor: s.bg, borderWidth: 1, borderColor: s.border,
    }}>
      <Text style={[typography.link, { color: s.fg }]}>{label}</Text>
    </View>
  );
}
```

### Community Review Card

```tsx
// components/ReviewCard.tsx
import { Text, View, useColorScheme } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { StarRating } from './StarRating';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ReviewCard({ initials, name, meta, stars, body }: {
  initials: string; name: string; meta: string; stars: number; body: string;
}) {
  const dark = useColorScheme() === 'dark';
  return (
    <View style={{ padding: 16, backgroundColor: dark ? colors.darkSurface1 : colors.cardWhite }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10 }}>
        <LinearGradient colors={[colors.brownDeep, colors.brown]}
          start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ width: 36, height: 36, borderRadius: 18, alignItems: 'center', justifyContent: 'center' }}>
          <Text style={[typography.label, { color: colors.tan }]}>{initials}</Text>
        </LinearGradient>
        <View>
          <Text style={typography.label}>{name}</Text>
          <Text style={typography.caption}>{meta}</Text>
        </View>
      </View>
      <View style={{ marginTop: 8, marginBottom: 6 }}><StarRating value={stars} size={13} /></View>
      <Text style={typography.review}>{body}</Text>
    </View>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useColorScheme } from 'react-native';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  const dark = useColorScheme() === 'dark';
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: dark ? colors.brownOnDark : colors.brown,
        tabBarInactiveTintColor: dark ? colors.darkTextSecondary : colors.textTertiary,
        tabBarStyle: {
          backgroundColor: dark ? colors.darkCanvas : colors.canvas,
          borderTopWidth: 0.5,
          borderTopColor: dark ? colors.darkDivider : colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Lato-Bold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home"           size={23} color={color} /> }} />
      <Tabs.Screen name="mybooks"  options={{ title: 'My Books', tabBarIcon: ({ color }) => <Ionicons name="library"        size={23} color={color} /> }} />
      <Tabs.Screen name="browse"   options={{ title: 'Browse',   tabBarIcon: ({ color }) => <Ionicons name="search"         size={23} color={color} /> }} />
      <Tabs.Screen name="updates"  options={{ title: 'Updates',  tabBarIcon: ({ color }) => <Ionicons name="notifications"  size={23} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={23} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Shelve action — green button spring + success haptic
scale.value = withSpring(0.96, { damping: 8 }, () => { scale.value = withSpring(1); });
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Star commit — per-star scale bounce + medium haptic, staggered fill
scale.value = withSpring(1.2, { damping: 6 }, () => { scale.value = withSpring(1); });
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// "…more" expander — animate height via Reanimated layout / measured height
import Animated, { FadeIn } from 'react-native-reanimated';
// <Animated.View entering={FadeIn.duration(220)}>{fullSynopsis}</Animated.View>

// Reading Challenge ring — animate strokeDashoffset with withTiming(target, { duration: 700 })

// Cover → detail — use react-native-shared-element or a measured scale transition
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| My Books | `library` |
| Browse / Search | `search` |
| Updates | `notifications` |
| Profile | `person-circle` |
| Star (rating) | `star` / `star-outline` |
| Half star | `star-half` |
| Back | `chevron-back` |
| Share | `share-outline` |
| Add to network | `people-outline` |
| Shelve chevron | `chevron-down` |
| Want to Read check | `checkmark` |
| Progress update | `sync-outline` |
| Like (review) | `heart-outline` / `heart` |
| Comment | `chatbubble-outline` |
| Scan ISBN | `barcode-outline` |

## 7. Platform Notes

- **Fonts**: Merriweather and Lato are both SIL OFL — free to bundle. Merriweather for any text the user *reads*; Lato for any text the user *acts on*
- **Reading rhythm**: review/synopsis text needs `lineHeight ≈ fontSize * 1.6`; do not let it collapse on small screens
- **Status bar**: `<StatusBar style="dark" />` on the tan light theme, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; nav and tab bar need safe-area padding
- **Dynamic Type**: `<Text>` respects system scale — set `allowFontScaling={false}` only on tab labels, overlines, star captions, shelf pills
- **Dark mode**: `useColorScheme()` swaps to `darkCanvas` / `darkSurface1`; amber stays constant, brown → `brownOnDark`, link teal → `linkTealDark`
- **Cover shadows on Android**: iOS `shadow*` props are ignored on Android — also set `elevation` (shown above) so jackets still lift
- **Accessibility**: give every `BookCover` an `accessibilityLabel` of "{title} by {author}"; the rate strip should use `accessibilityRole="adjustable"` with `accessibilityValue`
- **Star rendering**: clip a filled star with an `overflow:'hidden'` width box for fractional averages — never tint a partial star grey
- **Lists**: use `FlashList` for cover grids and the reviews feed — both can be very long
