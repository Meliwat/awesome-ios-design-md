# Coffee Meets Bagel (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Coffee Meets Bagel's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Primary action (Bagel Orange)
  bagel:     '#F4623A',
  bagelDeep: '#D94E2A',

  // Secondary brand (Brew Brown)
  brew:        '#A0522D',
  brewDeep:    '#7E3F22',
  brewPressed: '#693318',

  // Cream (replaces white)
  cream:     '#F3E4CF',
  creamSoft: '#E8D4B8',

  // Canvas & Surfaces (light)
  canvas:   '#FBF6EF',
  surface1L:'#FFFFFF',
  surface2L:'#F3ECE1',
  dividerL: '#E8DECF',

  // Canvas & Surfaces (dark)
  darkCanvas:   '#14100D',
  darkSurface1: '#1F1813',
  darkSurface2: '#2A2017',
  darkDivider:  '#33281D',

  // Text
  textPrimaryL:   '#3A2A18',
  textSecondaryL: '#8A7458',
  textPrimaryD:   '#EFE6DA',
  textSecondaryD: '#B7A48E',
  textTertiaryD:  '#7C6B57',

  // Status
  matchGreen:  '#4CC38A',
  premiumGold: '#E0A82E',
} as const;

export type CmbColor = keyof typeof colors;
```

## 2. Typography

CMB's brand face is Brandon Grotesque (licensed). Bundle **Poppins** via `expo-font` as the closest free face.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Poppins-Regular':   require('../assets/fonts/Poppins-Regular.ttf'),
    'Poppins-Medium':    require('../assets/fonts/Poppins-Medium.ttf'),
    'Poppins-SemiBold':  require('../assets/fonts/Poppins-SemiBold.ttf'),
    'Poppins-Bold':      require('../assets/fonts/Poppins-Bold.ttf'),
    'Poppins-ExtraBold': require('../assets/fonts/Poppins-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#EFE6DA' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...primary, fontFamily: 'Poppins-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  profileName: { color: '#FFFFFF', fontFamily: 'Poppins-Bold', fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:     { ...primary, fontFamily: 'Poppins-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  cardTitle:   { ...primary, fontFamily: 'Poppins-SemiBold',  fontSize: 18, lineHeight: 23 },
  body:        { ...primary, fontFamily: 'Poppins-Regular',   fontSize: 16, lineHeight: 24 },
  promptAns:   { ...primary, fontFamily: 'Poppins-SemiBold',  fontSize: 15, lineHeight: 21 },
  meta:        { color: '#F3E4CF', fontFamily: 'Poppins-Regular', fontSize: 14, lineHeight: 19 },
  chip:        { fontFamily: 'Poppins-SemiBold', fontSize: 13, lineHeight: 16 },
  badge:       { fontFamily: 'Poppins-Bold', fontSize: 12, lineHeight: 12, letterSpacing: 0.4 },
  number:      { fontFamily: 'Poppins-Bold', fontSize: 16, lineHeight: 16 },
  tab:         { fontFamily: 'Poppins-SemiBold', fontSize: 11, lineHeight: 11, letterSpacing: 0.1 },
  button:      { fontFamily: 'Poppins-Bold', fontSize: 16, lineHeight: 16 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Today's Bagel Card (core atom)

```tsx
// components/BagelCard.tsx
import { Image, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BagelCard({
  uri, name, age, meta, badge, interests,
}: {
  uri: string; name: string; age: number; meta: string; badge: string; interests: string[];
}) {
  return (
    <View style={{
      borderRadius: 24, overflow: 'hidden',
      shadowColor: '#000', shadowOpacity: 0.7, shadowRadius: 40, shadowOffset: { width: 0, height: 16 },
      elevation: 16, aspectRatio: 3 / 4,
    }}>
      <Image source={{ uri }} style={{ position: 'absolute', width: '100%', height: '100%' }} resizeMode="cover" />
      <LinearGradient
        colors={['transparent', 'rgba(20,16,13,0.95)']}
        locations={[0.45, 1]}
        style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: '55%' }}
      />

      {/* Badge */}
      <View style={{ position: 'absolute', top: 16, left: 16 }}>
        <BlurView intensity={24} tint="dark" style={{ flexDirection: 'row', alignItems: 'center', gap: 6, paddingVertical: 6, paddingHorizontal: 12, borderRadius: 999, overflow: 'hidden' }}>
          <Ionicons name="time" size={12} color={colors.cream} />
          <Text style={[typography.badge, { color: colors.cream }]}>{badge.toUpperCase()}</Text>
        </BlurView>
      </View>

      {/* Info */}
      <View style={{ position: 'absolute', left: 20, right: 20, bottom: 18 }}>
        <Text style={typography.profileName}>
          {name}<Text style={{ fontFamily: 'Poppins-Medium' }}> {age}</Text>
        </Text>
        <Text style={[typography.meta, { marginTop: 4 }]}>{meta}</Text>
        <View style={{ flexDirection: 'row', gap: 8, marginTop: 12, flexWrap: 'wrap' }}>
          {interests.map((c) => (
            <View key={c} style={{
              backgroundColor: 'rgba(243,228,207,0.18)', borderColor: 'rgba(243,228,207,0.28)', borderWidth: 1,
              borderRadius: 999, paddingVertical: 5, paddingHorizontal: 12,
            }}>
              <Text style={[typography.chip, { color: colors.cream }]}>{c}</Text>
            </View>
          ))}
        </View>
      </View>
    </View>
  );
}
```

### Action Trio (Pass / Like / Send a Bagel)

```tsx
// components/ActionTrio.tsx
import { Pressable, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

const shadow = { shadowColor: '#000', shadowOpacity: 0.6, shadowRadius: 20, shadowOffset: { width: 0, height: 8 }, elevation: 10 };

export function ActionTrio({ onPass, onLike, onSend }: { onPass: () => void; onLike: () => void; onSend: () => void }) {
  const heart = useSharedValue(1);
  const heartStyle = useAnimatedStyle(() => ({ transform: [{ scale: heart.value }] }));

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 28, paddingVertical: 16 }}>
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPass(); }}
        style={[{ width: 60, height: 60, borderRadius: 30, alignItems: 'center', justifyContent: 'center', backgroundColor: colors.darkSurface2, borderWidth: 1.5, borderColor: colors.darkDivider }, shadow]}
      >
        <Ionicons name="close" size={24} color={colors.textSecondaryD} />
      </Pressable>

      <Pressable
        onPress={() => {
          Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
          heart.value = withSequence(withSpring(1.15, { damping: 6 }), withSpring(1, { damping: 6 }));
          onLike();
        }}
        style={[{ width: 72, height: 72, borderRadius: 36, alignItems: 'center', justifyContent: 'center', backgroundColor: colors.bagel }, shadow]}
      >
        <Animated.View style={heartStyle}>
          <Ionicons name="heart" size={32} color="#fff" />
        </Animated.View>
      </Pressable>

      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onSend(); }}
        style={[{ width: 60, height: 60, borderRadius: 30, alignItems: 'center', justifyContent: 'center', backgroundColor: colors.brew }, shadow]}
      >
        <Ionicons name="star" size={20} color={colors.cream} />
      </Pressable>
    </View>
  );
}
```

### Curated Batch Header

```tsx
// components/BatchHeader.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BatchHeader({ title, countText, onBeans }: { title: string; countText: string; onBeans: () => void }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'flex-start', paddingHorizontal: 20, paddingBottom: 12 }}>
      <View style={{ flex: 1 }}>
        <Text style={[typography.cardTitle, { fontSize: 19 }]}>{title}</Text>
        <Text style={[typography.badge, { color: colors.textSecondaryD, letterSpacing: 0, fontFamily: 'Poppins-SemiBold' }]}>{countText}</Text>
      </View>
      <Pressable onPress={onBeans} style={{ width: 38, height: 38, borderRadius: 19, alignItems: 'center', justifyContent: 'center', backgroundColor: colors.darkSurface2 }}>
        <Ionicons name="leaf" size={18} color={colors.cream} />
      </Pressable>
    </View>
  );
}
```

### Interest Chip

```tsx
// components/InterestChip.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function InterestChip({ label, selected = false, onPress }: { label: string; selected?: boolean; onPress?: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        backgroundColor: selected ? colors.brew : colors.darkSurface2,
        borderColor: selected ? colors.brew : colors.darkDivider, borderWidth: 1,
        borderRadius: 999, paddingVertical: 8, paddingHorizontal: 14,
      }}
    >
      <Text style={[typography.chip, { color: selected ? colors.cream : colors.textPrimaryD }]}>{label}</Text>
    </Pressable>
  );
}
```

### Match Takeover

```tsx
// components/MatchTakeover.tsx
import { useEffect } from 'react';
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MatchTakeover({ onSayHi, onKeep }: { onSayHi: () => void; onKeep: () => void }) {
  const s = useSharedValue(0.9);
  const o = useSharedValue(0);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: s.value }], opacity: o.value }));

  useEffect(() => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
    s.value = withTiming(1, { duration: 400 });
    o.value = withTiming(1, { duration: 400 });
  }, []);

  return (
    <LinearGradient colors={[colors.bagel, colors.brew]} style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Animated.View style={[{ alignItems: 'center', gap: 20 }, style]}>
        <Text style={[typography.screenTitle, { color: '#fff' }]}>It's a match!</Text>
        <Ionicons name="checkmark-circle" size={44} color={colors.matchGreen} />
        <Pressable onPress={onSayHi} style={{ backgroundColor: colors.bagel, borderRadius: 999, paddingVertical: 14, paddingHorizontal: 40 }}>
          <Text style={[typography.button, { color: '#fff' }]}>Say hi</Text>
        </Pressable>
        <Pressable onPress={onKeep}><Text style={[typography.chip, { color: colors.cream }]}>Keep browsing</Text></Pressable>
      </Animated.View>
    </LinearGradient>
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
        tabBarActiveTintColor: colors.bagel,           // icon fills + label orange; no pill
        tabBarInactiveTintColor: colors.textTertiaryD,
        tabBarStyle: { backgroundColor: colors.darkCanvas, borderTopWidth: 0.5, borderTopColor: colors.darkDivider },
        tabBarLabelStyle: { fontFamily: 'Poppins-SemiBold', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Suggested', tabBarIcon: ({ color }) => <Ionicons name="ellipse-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover',  tabBarIcon: ({ color }) => <Ionicons name="search" size={22} color={color} /> }} />
      <Tabs.Screen name="chats"    options={{ title: 'Chats',     tabBarIcon: ({ color }) => <Ionicons name="chatbubble-outline" size={22} color={color} />, tabBarBadge: 3, tabBarBadgeStyle: { backgroundColor: colors.bagel } }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',   tabBarIcon: ({ color }) => <Ionicons name="person-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Like heart pop
heart.value = withSequence(withSpring(1.15, { damping: 6 }), withSpring(1, { damping: 6 }));
// Just-liked card wash: orange overlay opacity 0.12 → 0 over 500ms (withTiming)

// Card advance (like flies up-right)
tx.value = withTiming(320, { duration: 320 }); ty.value = withTiming(-120, { duration: 320 }); rot.value = withTiming(12, { duration: 320 });
// Next card cross-fade + scale 0.96 → 1.0 over 260ms

// Match takeover scale-in: withTiming(1, { duration: 400 })

// Chip select: animate backgroundColor over 180ms (Reanimated interpolateColor)

// Haptics
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // Like
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);              // Pass / chip
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);             // Match takeover / Send
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). CMB's bagel logomark is a brand mark — render it as a bundled SVG via `react-native-svg` for fidelity; the table uses the nearest stand-in.

| Purpose | Ionicons |
|---------|----------|
| Suggested (tab) | `ellipse-outline` (stand-in for bagel logomark) |
| Discover (tab) | `search` |
| Chats (tab) | `chatbubble-outline` |
| Profile (tab) | `person-outline` |
| Pass | `close` |
| Like | `heart` |
| Send a Bagel | `star` |
| Beans / currency | `leaf` (stand-in for coffee bean) |
| Active badge | `time` |
| Match check | `checkmark-circle` |
| Back | `chevron-back` |
| Send message | `arrow-up` |
| Filter | `options` |
| Report / overflow | `ellipsis-horizontal` |
| Premium / Boost | `flash` |

## 7. Platform Notes

- **Font choice**: bundle Poppins (SIL OFL — free) as the closest face; do not redistribute Brandon Grotesque (CMB's licensed brand face).
- **Status bar**: `<StatusBar style="light" />` on the dark coffee canvas; `"dark"` on the warm light canvas.
- **Safe area**: wrap screens in `SafeAreaView`; the action trio and tab bar need bottom safe-area padding; profile-detail action bar floats above the home indicator.
- **One card at a time**: keep a single `currentBagel` (and `nextBagel` preloaded) in state; the curated batch is finite — never an infinite list. Render the next card behind the current one for the cross-fade.
- **Card gestures**: pair the buttons with a `Gesture.Pan()` so users can also swipe the card; commit threshold ~35% width; on commit run the same fly-out + advance.
- **Blur badges**: `expo-blur` `BlurView` needs `overflow: 'hidden'` on a rounded container to clip correctly.
- **Dynamic Type**: allow scaling on profile name/body/prompts; set `allowFontScaling={false}` on chips, badges, bean numbers, tab labels.
- **Dark mode**: `useColorScheme()` swaps to `darkCanvas` / `darkSurface1`; Bagel Orange + Brew Brown are constant; cream replaces white over photos in both modes.
- **Accessibility**: the card is one accessible element ("Profile: {name}, {age}, {meta}"); the action buttons read "Like {name}" / "Pass on {name}" / "Send a Bagel to {name}"; expose Like/Pass as `accessibilityActions` on the card.
