# Yelp (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Yelp's visual language into paste-ready Expo / React Native code: a design-token module, a half-star rating, the review card, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand (interactive)
  yelpRed:        '#FF1A1A',
  yelpRedLogo:    '#D32323',
  yelpRedPressed: '#B81E1E',

  // Rating
  starFilled:    '#F25C05',
  starEmpty:     '#E3E3E0',
  starEmptyDark: '#3A3A3A',

  // Surfaces (light)
  canvas:      '#FFFFFF',
  surfaceGray: '#F5F5F5',
  sectionBand: '#F0F0F0',
  divider:     '#E0E0E0',
  pressedRow:  '#EDEDED',

  // Surfaces (dark)
  darkCanvas:      '#161616',
  darkSurface1:    '#1E1E1E',
  darkSurface2:    '#2A2A2A',
  darkDivider:     '#303030',
  darkSectionBand: '#0C0C0C',

  // Text
  textPrimary:       '#2B2B2B',
  textSecondary:     '#6E6E6E',
  textTertiary:      '#9A9A9A',
  darkTextPrimary:   '#E6E6E6',
  darkTextSecondary: '#A8A8A8',

  // Semantic
  open:     '#2DA44E',
  closed:   '#E03E3E',
  warning:  '#D9730D',
  link:     '#0073BB',
  linkDark: '#4FA3D9',
  elite:    '#A2231D',
} as const;

export type YelpColor = keyof typeof colors;
```

## 2. Typography

Load Open Sans via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'OpenSans-Regular':    require('../assets/fonts/OpenSans-Regular.ttf'),
    'OpenSans-SemiBold':   require('../assets/fonts/OpenSans-SemiBold.ttf'),
    'OpenSans-Bold':       require('../assets/fonts/OpenSans-Bold.ttf'),
    'OpenSans-ExtraBold':  require('../assets/fonts/OpenSans-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#2B2B2B' } satisfies TextStyle;

export const typography = {
  screenTitle:  { ...primary, fontFamily: 'OpenSans-ExtraBold', fontSize: 32, lineHeight: 38, letterSpacing: -0.4 },
  bizName:      { ...primary, fontFamily: 'OpenSans-ExtraBold', fontSize: 24, lineHeight: 29, letterSpacing: -0.3 },
  section:      { ...primary, fontFamily: 'OpenSans-Bold',      fontSize: 20, lineHeight: 25, letterSpacing: -0.2 },
  cardHeader:   { ...primary, fontFamily: 'OpenSans-ExtraBold', fontSize: 16, lineHeight: 21 },
  emphasis:     { ...primary, fontFamily: 'OpenSans-Bold',      fontSize: 15, lineHeight: 20 },
  body:         { ...primary, fontFamily: 'OpenSans-Regular',   fontSize: 14, lineHeight: 22 },
  reviewerName: { ...primary, fontFamily: 'OpenSans-Bold',      fontSize: 14, lineHeight: 18 },
  meta:         { color: '#6E6E6E', fontFamily: 'OpenSans-SemiBold', fontSize: 13, lineHeight: 18 },
  caption:      { color: '#6E6E6E', fontFamily: 'OpenSans-SemiBold', fontSize: 12, lineHeight: 17, letterSpacing: 0.1 },
  button:       { color: '#FFFFFF', fontFamily: 'OpenSans-Bold',     fontSize: 15, lineHeight: 15 },
  tab:          { fontFamily: 'OpenSans-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  chip:         { fontFamily: 'OpenSans-SemiBold', fontSize: 13, lineHeight: 13 },
  link:         { color: '#0073BB', fontFamily: 'OpenSans-Bold', fontSize: 14, lineHeight: 18 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### 5-Star Rating (half-star)

```tsx
// components/RatingStars.tsx
import { View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function RatingStars({ rating, size = 18, dark = false }: { rating: number; size?: number; dark?: boolean }) {
  const empty = dark ? colors.starEmptyDark : colors.starEmpty;
  return (
    <View style={{ flexDirection: 'row', gap: 2 }} accessibilityLabel={`${rating.toFixed(1)} out of 5 stars`}>
      {[0, 1, 2, 3, 4].map((i) => {
        const fill = Math.min(Math.max(rating - i, 0), 1); // 0, 0.5, 1
        return (
          <View key={i} style={{ width: size, height: size }}>
            <Ionicons name="star" size={size} color={empty} style={{ position: 'absolute' }} />
            <View style={{ width: size * fill, overflow: 'hidden' }}>
              <Ionicons name="star" size={size} color={colors.starFilled} />
            </View>
          </View>
        );
      })}
    </View>
  );
}
```

### Interactive Rating Input

```tsx
// components/RatingInput.tsx
import { Pressable, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function RatingInput({ value, onChange, size = 32 }: { value: number; onChange: (v: number) => void; size?: number }) {
  return (
    <View style={{ flexDirection: 'row', gap: 6 }}>
      {[1, 2, 3, 4, 5].map((i) => (
        <Pressable
          key={i}
          onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onChange(i); }}
          hitSlop={6}
        >
          <Ionicons
            name={i <= value ? 'star' : 'star-outline'}
            size={size}
            color={i <= value ? colors.starFilled : colors.starEmpty}
          />
        </Pressable>
      ))}
    </View>
  );
}
```

### Business Header

```tsx
// components/BusinessHeader.tsx
import { Image, Text, View } from 'react-native';
import { RatingStars } from './RatingStars';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BusinessHeader({
  photoUri, name, rating, reviewCount, priceCategory, isOpen, hours,
}: {
  photoUri: string; name: string; rating: number; reviewCount: number;
  priceCategory: string; isOpen: boolean; hours: string;
}) {
  return (
    <View>
      <View style={{ height: 184 }}>
        <Image source={{ uri: photoUri }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
        <View style={{
          position: 'absolute', bottom: 12, right: 14,
          backgroundColor: 'rgba(0,0,0,0.6)', borderRadius: 6,
          paddingHorizontal: 10, paddingVertical: 4,
        }}>
          <Text style={[typography.caption, { color: '#FFF' }]}>1 / 248 photos</Text>
        </View>
      </View>

      <View style={{ padding: 16, gap: 6 }}>
        <Text style={typography.bizName}>{name}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
          <RatingStars rating={rating} />
          <Text style={typography.emphasis}>{rating.toFixed(1)}</Text>
          <Text style={typography.link}>{reviewCount.toLocaleString()} reviews</Text>
        </View>
        <Text style={typography.meta}>{priceCategory}</Text>
        <Text style={typography.meta}>
          <Text style={{ color: isOpen ? colors.open : colors.closed, fontFamily: 'OpenSans-Bold' }}>
            {isOpen ? 'Open' : 'Closed'}
          </Text>
          {`  ·  ${hours}`}
        </Text>
      </View>

      <View style={{ height: 8, backgroundColor: colors.sectionBand }} />
    </View>
  );
}
```

### Review Card

```tsx
// components/ReviewCard.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { RatingStars } from './RatingStars';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ReviewCard({
  initials, name, location, reviewerCount, rating, date, body,
}: {
  initials: string; name: string; location: string;
  reviewerCount: number; rating: number; date: string; body: string;
}) {
  const [useful, setUseful] = useState(false);
  return (
    <View style={{ paddingVertical: 16, borderBottomWidth: 1, borderBottomColor: colors.divider }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10 }}>
        <LinearGradient colors={[colors.yelpRedLogo, '#8A1818']}
          style={{ width: 40, height: 40, borderRadius: 20, alignItems: 'center', justifyContent: 'center' }}>
          <Text style={[typography.emphasis, { color: '#FFF' }]}>{initials}</Text>
        </LinearGradient>
        <View>
          <Text style={typography.reviewerName}>{name}</Text>
          <Text style={typography.caption}>{location} · {reviewerCount} reviews</Text>
        </View>
      </View>

      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', marginTop: 10 }}>
        <RatingStars rating={rating} size={15} />
        <Text style={typography.caption}>{date}</Text>
      </View>

      <Text style={[typography.body, { marginTop: 8 }]}>{body}</Text>

      <View style={{ flexDirection: 'row', gap: 18, marginTop: 10 }}>
        <Pressable onPress={() => setUseful((v) => !v)} style={{ flexDirection: 'row', alignItems: 'center', gap: 5 }}>
          <Ionicons name="thumbs-up-outline" size={13} color={useful ? colors.yelpRed : colors.textSecondary} />
          <Text style={[typography.caption, { color: useful ? colors.yelpRed : colors.textSecondary }]}>Useful 24</Text>
        </Pressable>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5 }}>
          <Ionicons name="happy-outline" size={13} color={colors.textSecondary} />
          <Text style={typography.caption}>Funny 6</Text>
        </View>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5 }}>
          <Ionicons name="heart-outline" size={13} color={colors.textSecondary} />
          <Text style={typography.caption}>Cool 11</Text>
        </View>
      </View>
    </View>
  );
}
```

### Primary Button & Category Chip

```tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, icon, onPress }: { title: string; icon?: any; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flex: 1, minHeight: 44, borderRadius: 8,
        flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 6,
        backgroundColor: pressed ? colors.yelpRedPressed : colors.yelpRed,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      {icon ? <Ionicons name={icon} size={16} color="#FFF" /> : null}
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function CategoryChip({ title, icon, selected }: { title: string; icon: any; selected: boolean }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 7,
      paddingHorizontal: 16, paddingVertical: 9, borderRadius: 999,
      backgroundColor: selected ? colors.yelpRed : colors.surfaceGray,
      borderWidth: 1, borderColor: selected ? colors.yelpRed : colors.divider,
    }}>
      <Ionicons name={icon} size={13} color={selected ? '#FFF' : colors.yelpRed} />
      <Text style={[typography.chip, { color: selected ? '#FFF' : colors.textPrimary }]}>{title}</Text>
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
        tabBarActiveTintColor: colors.yelpRed,
        tabBarInactiveTintColor: '#7E7E7E',
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'OpenSans-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search"          size={22} color={color} /> }} />
      <Tabs.Screen name="nearby"   options={{ title: 'Nearby',   tabBarIcon: ({ color }) => <Ionicons name="home"            size={22} color={color} /> }} />
      <Tabs.Screen name="write"    options={{ title: 'Write',    tabBarIcon: ({ color }) => <Ionicons name="create"          size={22} color={color} /> }} />
      <Tabs.Screen name="activity" options={{ title: 'Activity', tabBarIcon: ({ color }) => <Ionicons name="notifications"   size={22} color={color} /> }} />
      <Tabs.Screen name="me"       options={{ title: 'Me',       tabBarIcon: ({ color }) => <Ionicons name="person-circle"   size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, {
  useSharedValue, useAnimatedStyle, withSpring, withTiming, interpolate, useAnimatedScrollHandler,
} from 'react-native-reanimated';

// Photo header parallax + compact title cross-fade
const scrollY = useSharedValue(0);
const onScroll = useAnimatedScrollHandler((e) => { scrollY.value = e.contentOffset.y; });
const heroStyle  = useAnimatedStyle(() => ({ transform: [{ translateY: scrollY.value * 0.5 }] }));
const titleStyle = useAnimatedStyle(() => ({ opacity: interpolate(scrollY.value, [120, 180], [0, 1]) }));

// Vote tap — count bump spring
const scale = useSharedValue(1);
const onVote = () => { scale.value = withSpring(1.2, { damping: 6 }, () => { scale.value = withSpring(1); }); };

// Rating increment haptic
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // per star
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); // check-in

// Filter sheet — use @gorhom/bottom-sheet, snapPoints ['90%'], 300ms ease-out, scrim opacity 0.4
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Yelp's iconography is simple and line-based.

| Purpose | Ionicons |
|---------|----------|
| Search (tab) | `search` |
| Nearby (tab) | `home` |
| Write (tab) | `create` |
| Activity (tab) | `notifications` |
| Me (tab) | `person-circle` |
| Star filled | `star` |
| Star empty | `star-outline` |
| Star half | `star-half` |
| Write a Review | `create-outline` |
| Save | `bookmark-outline` / `bookmark` |
| Share | `share-outline` |
| Directions | `navigate-outline` |
| Call | `call` |
| Useful | `thumbs-up-outline` / `thumbs-up` |
| Funny | `happy-outline` |
| Cool | `heart-outline` / `heart` |
| Back | `chevron-back` |
| Filter | `options-outline` |
| Map toggle | `map-outline` |
| Add photo | `camera-outline` |
| Location pill | `chevron-down` |

## 7. Platform Notes

- **Font choice**: Open Sans is SIL OFL via Google Fonts — free to bundle. Ship Regular / SemiBold / Bold / ExtraBold
- **Status bar**: `<StatusBar style="light" />` over the photo header (dark imagery); flip to `"dark"` on light content, `"light"` on dark mode
- **Safe area**: the photo header is full-bleed into the top safe area; inset the floating back/share buttons with `useSafeAreaInsets()`; wrap list screens in `SafeAreaView`
- **Dynamic Type**: `<Text>` respects system scale by default — set `allowFontScaling={false}` on tab labels, chip text, the photo-count chip, and vote labels
- **Half-star rendering**: the overflow-clip technique above works on iOS and Android; for sub-pixel precision on Android consider an SVG mask via `react-native-svg`
- **Keyboard**: wrap the search results in `KeyboardAvoidingView`
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1`, switch `starEmpty → starEmptyDark`, `sectionBand → darkSectionBand`, `link → linkDark` — but keep `starFilled` (`#F25C05`) constant
- **Accessibility**: rating components set `accessibilityLabel="{x.x} out of 5 stars"`; the rating input sets `accessibilityRole="adjustable"` with `accessibilityActions` for increment/decrement; never convey open/closed by color alone — keep the text
- **Reduce motion**: read `AccessibilityInfo.isReduceMotionEnabled()` and disable parallax + vote spring; keep the star fill
