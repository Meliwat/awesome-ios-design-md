# Marriott Bonvoy (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Marriott Bonvoy's visual language into paste-ready Expo / React Native code: a design-token module, the signature Bonvoy points panel, points-aware rate cards, the hotel hero, the Mobile Key, and the bottom tab bar.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `expo-image`, `expo-blur`, `expo-linear-gradient`, and `react-native-reanimated` v3. Marriott Bonvoy uses the **system font** (San Francisco on iOS) — no custom fonts to bundle.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceCream:   '#F6F4EF',
  surfacePressed: '#ECE9E1',
  divider:        '#E3E1DA',

  // Surfaces (dark)
  darkCanvas:     '#121214',
  darkSurface1:   '#1A1A1D',
  darkSurface2:   '#232327',
  darkDivider:    '#2E2E33',

  // Structure (navy) — constant across schemes
  navy:        '#16264A',
  nearBlack:   '#1C1C1C',
  navyLine:    '#243A66',
  navyPressed: '#1F3157',

  // Member value (gold)
  gold:        '#B3852A',
  goldBright:  '#D2A23E',
  goldPressed: '#8F6A1F',
  onGold:      '#1C1206',

  // Text
  textPrimary:    '#1A1A1C',
  textSecondary:  '#5C5C63',
  textTertiary:   '#8E8E96',
  darkTextPrimary:   '#EDEDEF',
  darkTextSecondary: '#A0A0A8',

  // Semantic
  success: '#2E8B57',
  error:   '#C7453B',
  warning: '#C8901C',
} as const;

export type Tier = 'member' | 'silver' | 'gold' | 'platinum' | 'titanium' | 'ambassador';

export const tierStyle: Record<Tier, { label: string; fg: string; bg: string; border: string }> = {
  member:     { label: 'MEMBER',          fg: colors.textSecondary, bg: colors.darkSurface2,         border: 'rgba(255,255,255,0.06)' },
  silver:     { label: 'SILVER ELITE',    fg: '#C7C7CE',            bg: '#2A2A2E',                   border: '#3A3A40' },
  gold:       { label: 'GOLD ELITE',      fg: colors.goldBright,    bg: 'rgba(179,133,42,0.16)',     border: 'rgba(210,162,62,0.40)' },
  platinum:   { label: 'PLATINUM ELITE',  fg: '#9FB6E0',            bg: 'rgba(36,58,102,0.40)',      border: colors.navyLine },
  titanium:   { label: 'TITANIUM ELITE',  fg: colors.goldBright,    bg: colors.navy,                 border: colors.navyLine },
  ambassador: { label: 'AMBASSADOR ELITE',fg: colors.goldBright,    bg: colors.navy,                 border: colors.gold },
};
```

## 2. Typography

Marriott Bonvoy ships **no custom typeface** — use the system font. Eyebrows are letter-spaced uppercase small-caps; the points balance is an 800-weight numeral.

```ts
// theme/typography.ts
import { Platform, type TextStyle } from 'react-native';

const sys = Platform.select({ ios: 'System', default: 'sans-serif' });
const primary = { color: '#1A1A1C' } satisfies TextStyle;

export const typography = {
  largeTitle:    { ...primary, fontFamily: sys, fontSize: 32, fontWeight: '800', letterSpacing: -0.4 },
  pointsBalance: { ...primary, fontFamily: sys, fontSize: 26, fontWeight: '800', letterSpacing: -0.3 },
  hotelName:     { ...primary, fontFamily: sys, fontSize: 22, fontWeight: '700', letterSpacing: -0.3 },
  section:       { ...primary, fontFamily: sys, fontSize: 18, fontWeight: '700', letterSpacing: -0.2 },
  body:          { ...primary, fontFamily: sys, fontSize: 16, fontWeight: '400', lineHeight: 24 },
  rateTitle:     { ...primary, fontFamily: sys, fontSize: 15, fontWeight: '600' },
  price:         { ...primary, fontFamily: sys, fontSize: 17, fontWeight: '800' },
  meta:          { color: '#5C5C63', fontFamily: sys, fontSize: 14, fontWeight: '400' },
  eyebrow:       { fontFamily: sys, fontSize: 11, fontWeight: '700', letterSpacing: 1.2 },
  pointsEarn:    { fontFamily: sys, fontSize: 11, fontWeight: '700', letterSpacing: 0.3 },
  button:        { fontFamily: sys, fontSize: 16, fontWeight: '700' },
  tab:           { fontFamily: sys, fontSize: 10, fontWeight: '500', letterSpacing: 0.1 },
  caption:       { color: '#5C5C63', fontFamily: sys, fontSize: 12, fontWeight: '500' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Bonvoy Points Panel (the core component)

```tsx
// components/BonvoyPointsPanel.tsx
import { useEffect, useState } from 'react';
import { View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, Easing } from 'react-native-reanimated';
import { colors, tierStyle, type Tier } from '../theme/colors';
import { typography } from '../theme/typography';

export function BonvoyPointsPanel({
  tier, memberName, points, progress, footnote,
}: { tier: Tier; memberName: string; points: number; progress: number; footnote: string }) {
  const [display, setDisplay] = useState(0);
  const fill = useSharedValue(0);
  const barStyle = useAnimatedStyle(() => ({ width: `${fill.value * 100}%` }));

  useEffect(() => {
    fill.value = withTiming(progress, { duration: 500, easing: Easing.out(Easing.cubic) });
    const start = Date.now();
    const tick = () => {
      const t = Math.min((Date.now() - start) / 600, 1);
      setDisplay(Math.round(points * (1 - Math.pow(1 - t, 3))));
      if (t < 1) requestAnimationFrame(tick);
    };
    requestAnimationFrame(tick);
  }, [points, progress]);

  return (
    <View style={{
      backgroundColor: colors.navy, borderRadius: 14, borderWidth: 1, borderColor: colors.navyLine, padding: 16,
    }}>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'flex-start' }}>
        <View>
          <Text style={[typography.eyebrow, { color: colors.goldBright }]}>{tierStyle[tier].label}</Text>
          <Text style={{ fontSize: 13, color: 'rgba(255,255,255,0.7)', marginTop: 2 }}>{memberName}</Text>
        </View>
        <View style={{ alignItems: 'flex-end' }}>
          <Text style={[typography.pointsBalance, { color: '#FFFFFF' }]}>{display.toLocaleString()}</Text>
          <Text style={{ fontSize: 11, fontWeight: '600', letterSpacing: 0.4, color: 'rgba(255,255,255,0.6)' }}>POINTS</Text>
        </View>
      </View>
      <View style={{ height: 6, borderRadius: 3, backgroundColor: 'rgba(255,255,255,0.14)', marginTop: 14, overflow: 'hidden' }}>
        <Animated.View style={[{ height: '100%', borderRadius: 3, backgroundColor: colors.goldBright }, barStyle]} />
      </View>
      <Text style={{ fontSize: 11, color: 'rgba(255,255,255,0.6)', marginTop: 8 }}>{footnote}</Text>
    </View>
  );
}
```

### Points-Aware Rate Card

```tsx
// components/RateCard.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RateCard({
  name, detail, priceText, pointsLine, selected, onPress,
}: { name: string; detail: string; priceText: string; pointsLine: string; selected: boolean; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
        padding: 14, borderRadius: 10, marginBottom: 10,
        backgroundColor: selected ? 'rgba(179,133,42,0.10)' : colors.darkSurface1,
        borderWidth: 1, borderColor: selected ? colors.gold : colors.darkDivider,
      }}
    >
      <View style={{ flex: 1 }}>
        <Text style={[typography.rateTitle, { color: colors.darkTextPrimary }]}>{name}</Text>
        <Text style={{ fontSize: 12, color: colors.darkTextSecondary, marginTop: 3 }}>{detail}</Text>
      </View>
      <View style={{ alignItems: 'flex-end' }}>
        <Text style={[typography.price, { color: colors.darkTextPrimary }]}>{priceText}</Text>
        <Text style={[typography.pointsEarn, { color: colors.goldBright, marginTop: 2 }]}>{pointsLine}</Text>
      </View>
    </Pressable>
  );
}
```

### Hotel Hero

```tsx
// components/HotelHero.tsx
import { View, Text, Pressable } from 'react-native';
import { Image } from 'expo-image';
import { BlurView } from 'expo-blur';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function HotelHero({
  imageUri, subBrand, name, location, onBack, onShare, saved, onToggleSave,
}: {
  imageUri: string; subBrand: string; name: string; location: string;
  onBack: () => void; onShare: () => void; saved: boolean; onToggleSave: () => void;
}) {
  return (
    <View style={{ height: 256 }}>
      <Image source={{ uri: imageUri }} style={{ width: '100%', height: 256 }} contentFit="cover" />
      <LinearGradient
        colors={['transparent', 'rgba(18,18,20,0.7)']}
        style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: 145 }}
        pointerEvents="none"
      />
      <View style={{ position: 'absolute', top: 54, left: 16, right: 16, flexDirection: 'row', justifyContent: 'space-between' }}>
        <HeroButton icon="chevron-back" onPress={onBack} />
        <View style={{ flexDirection: 'row', gap: 10 }}>
          <HeroButton icon="share-outline" onPress={onShare} />
          <HeroButton icon={saved ? 'heart' : 'heart-outline'} onPress={onToggleSave} />
        </View>
      </View>
      <View style={{ position: 'absolute', left: 18, right: 18, bottom: 16 }}>
        <Text style={[typography.eyebrow, { color: colors.goldBright, letterSpacing: 1.4 }]}>{subBrand.toUpperCase()}</Text>
        <Text style={{ fontSize: 23, fontWeight: '800', color: '#FFF', marginTop: 5 }}>{name}</Text>
        <Text style={{ fontSize: 13, color: 'rgba(255,255,255,0.82)', marginTop: 5 }}>{location}</Text>
      </View>
    </View>
  );
}

function HeroButton({ icon, onPress }: { icon: any; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} hitSlop={8}>
      <BlurView intensity={28} tint="dark" style={{ width: 36, height: 36, borderRadius: 18, alignItems: 'center', justifyContent: 'center', overflow: 'hidden' }}>
        <Ionicons name={icon} size={18} color="#FFF" />
      </BlurView>
    </Pressable>
  );
}
```

### Mobile Key

```tsx
// components/MobileKeyCard.tsx
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MobileKeyCard({
  room, checkIn, onUnlock,
}: { room: string; checkIn: string; onUnlock: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); onUnlock(); }}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 14, padding: 16,
        backgroundColor: colors.navy, borderRadius: 14, borderWidth: 1, borderColor: colors.navyLine,
      }}
    >
      <View style={{ width: 48, height: 48, borderRadius: 12, backgroundColor: 'rgba(210,162,62,0.16)', alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="lock-closed" size={22} color={colors.goldBright} />
      </View>
      <View style={{ flex: 1 }}>
        <Text style={{ fontSize: 14, fontWeight: '700', color: '#FFF' }}>Mobile Key ready</Text>
        <Text style={{ fontSize: 12, color: 'rgba(255,255,255,0.65)', marginTop: 3 }}>Skip the front desk · Tap your phone to unlock</Text>
        <Text style={[typography.eyebrow, { color: colors.goldBright, letterSpacing: 0.5, marginTop: 6 }]}>
          ROOM {room} · CHECK-IN {checkIn}
        </Text>
      </View>
    </Pressable>
  );
}
```

### Elite Tier Badge + Booking Bar

```tsx
// components/TierBadge.tsx
import { View, Text } from 'react-native';
import { tierStyle, type Tier } from '../theme/colors';
import { typography } from '../theme/typography';

export function TierBadge({ tier }: { tier: Tier }) {
  const s = tierStyle[tier];
  return (
    <View style={{ backgroundColor: s.bg, borderColor: s.border, borderWidth: 1, borderRadius: 999, paddingHorizontal: 12, paddingVertical: 6 }}>
      <Text style={[typography.eyebrow, { color: s.fg, letterSpacing: 0.6 }]}>{s.label}</Text>
    </View>
  );
}
```

```tsx
// components/BookingBar.tsx
import { View, Text, Pressable } from 'react-native';
import { BlurView } from 'expo-blur';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BookingBar({
  price, pointsEarned, onBook,
}: { price: string; pointsEarned: string; onBook: () => void }) {
  return (
    <BlurView intensity={40} tint="dark" style={{
      height: 76, flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
      paddingHorizontal: 18, borderTopWidth: 1, borderTopColor: colors.darkDivider,
    }}>
      <View>
        <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 4 }}>
          <Text style={{ fontSize: 19, fontWeight: '800', color: colors.darkTextPrimary }}>{price}</Text>
          <Text style={{ fontSize: 13, color: colors.darkTextSecondary, marginBottom: 3 }}>/ night</Text>
        </View>
        <Text style={[typography.pointsEarn, { color: colors.goldBright }]}>{pointsEarned}</Text>
      </View>
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onBook(); }}
        style={({ pressed }) => ({
          backgroundColor: pressed ? colors.goldPressed : colors.gold,
          paddingHorizontal: 26, paddingVertical: 13, borderRadius: 8,
          transform: [{ scale: pressed ? 0.98 : 1 }],
        })}
      >
        <Text style={[typography.button, { color: colors.onGold }]}>Book</Text>
      </Pressable>
    </BlurView>
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
        tabBarActiveTintColor: colors.goldBright,   // gold — loyalty is the spine
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.darkCanvas, borderTopWidth: 0.5, borderTopColor: colors.darkDivider },
        tabBarLabelStyle: { fontSize: 10, fontWeight: '500', letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',       tabBarIcon: ({ color }) => <Ionicons name="home-outline"     size={22} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search',     tabBarIcon: ({ color }) => <Ionicons name="search"           size={22} color={color} /> }} />
      <Tabs.Screen name="trips"   options={{ title: 'Trips',      tabBarIcon: ({ color }) => <Ionicons name="briefcase-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="key"     options={{ title: 'Mobile Key', tabBarIcon: ({ color }) => <Ionicons name="key-outline"      size={22} color={color} /> }} />
      <Tabs.Screen name="account" options={{ title: 'Account',    tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import { withTiming, withSpring, Easing } from 'react-native-reanimated';

// Points count-up + bar fill (member panel) — see BonvoyPointsPanel:
// requestAnimationFrame easeOutCubic for the numeral; withTiming(progress, 500ms) for the bar

// Rate select — gold border + tint (style switch is immediate; wrap in LayoutAnimation for smoothness)
// Booking-bar price/points cross-dissolve over 250ms (Reanimated opacity swap)

// Mobile Key unlock — success haptic + gold ring pulse
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
ring.value = withSpring(1, { damping: 8 }, () => { ring.value = withSpring(0); });

// Hero parallax — translate hero at 0.5x scroll via useAnimatedScrollHandler

// Card appear stagger
// entering={FadeInUp.delay(i * 60).duration(220)}

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);                 // Book
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);   // Mobile Key, confirmed
Haptics.selectionAsync();                                              // rate select, date endpoint
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The points progress bar and tier badges are drawn with plain `View`s; the gold sub-brand eyebrow is text, not a glyph.

| Purpose | Ionicons |
|---------|----------|
| Home (tab) | `home-outline` |
| Search (tab) | `search` |
| Trips (tab) | `briefcase-outline` |
| Mobile Key (tab) | `key-outline` |
| Account (tab) | `person-circle-outline` |
| Back (hero) | `chevron-back` |
| Share | `share-outline` |
| Save | `heart` / `heart-outline` |
| Rating star | `star` |
| Mobile Key glyph | `lock-closed` / `key` |
| Points / rewards | `star-half` |
| Free night | `moon` |
| Member tier | `ribbon-outline` |
| Calendar / dates | `calendar-outline` |
| Room & guests | `people-outline` |
| Location | `location-outline` |
| Amenities | `checkmark-circle-outline` |
| Filters | `options-outline` |
| Confirmed | `shield-checkmark` |

## 7. Platform Notes

- **Font choice**: Marriott Bonvoy uses the OS system font — `fontFamily: 'System'` on iOS, `'sans-serif'` on Android. Do not bundle a custom typeface
- **Status bar**: hero extends under the status bar — use `<StatusBar style="light" />` while a hero is in view (white overlay controls), per theme elsewhere
- **Safe area**: wrap screens in `SafeAreaView`; the booking bar and tab bar need safe-area padding; hero renders full-bleed with controls inset
- **expo-image**: use `expo-image` for hero + thumbnails (better caching/decoding); set `contentFit="cover"`
- **Dynamic Type**: RN respects system scale on `<Text>`; set `allowFontScaling={false}` on eyebrows, tab labels, points-earn lines, and progress legends (layout-sensitive small-caps)
- **Keyboard**: use `KeyboardAvoidingView` on search and form screens
- **Dark mode**: use `useColorScheme()`; navy `#16264A` stays constant (panels, secondary CTA, brand frame); gold resolves to `goldBright` on dark for points/tier/Book; canvas is `#121214`
- **Number formatting**: format points with `toLocaleString()` for grouped thousands; keep the count-up easing cubic-out
- **Accessibility**: announce the member panel (tier + points + progress %); rate cards with name + price + points; the Mobile Key as an actionable element; tier badges carry text labels, not color alone
- **Security/loyalty**: never log full membership numbers or Mobile Key tokens; treat them as sensitive; the Mobile Key unlock should require an explicit tap (no auto-unlock)
