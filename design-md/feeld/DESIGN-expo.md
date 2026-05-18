# Feeld (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Feeld's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. Feeld ships dark-only — build dark-native.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, and `react-native-reanimated` v3 + `react-native-gesture-handler`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark-native, only mode)
  canvas:   '#000000',
  surface1: '#121212',
  surface2: '#1C1C1E',
  surface3: '#262626',
  divider:  '#2A2A2A',
  scrim:    'rgba(0,0,0,0.45)',

  // Brand
  acid:      '#E8FF63',
  acidPress: '#D4EB4F',
  pink:      '#FF5C8A',
  pinkDeep:  '#E0447A',
  lilac:     '#C9B8FF',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#A8A8A8',
  textTertiary:  '#6E6E6E',
  onAcid:        '#000000',
  onPink:        '#FFFFFF',

  // Semantic
  success: '#66E0A3',
  error:   '#FF6B6B',
} as const;

export type FeeldColor = keyof typeof colors;

export const chipStyles = {
  selected:   { bg: colors.acid,                 text: colors.onAcid,       border: 'transparent' },
  unselected: { bg: colors.surface2,             text: colors.textPrimary,  border: colors.divider },
  emotional:  { bg: 'rgba(255,92,138,0.16)',     text: colors.pink,         border: 'rgba(255,92,138,0.4)' },
} as const;
```

## 2. Typography

Load Space Grotesk (personality) + Inter (legibility) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'SpaceGrotesk-SemiBold': require('../assets/fonts/SpaceGrotesk-SemiBold.ttf'),
    'SpaceGrotesk-Bold':     require('../assets/fonts/SpaceGrotesk-Bold.ttf'),
    'Inter-Regular':         require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold':        require('../assets/fonts/Inter-SemiBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ headerShown: false, contentStyle: { backgroundColor: '#000000' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  display:    { fontFamily: 'SpaceGrotesk-Bold',     fontSize: 32, lineHeight: 37, letterSpacing: -0.5, color: '#FFFFFF' },
  name:       { fontFamily: 'SpaceGrotesk-Bold',     fontSize: 26, lineHeight: 30, letterSpacing: -0.4, color: '#FFFFFF' },
  section:    { fontFamily: 'SpaceGrotesk-SemiBold', fontSize: 22, lineHeight: 26, letterSpacing: -0.3, color: '#FFFFFF' },
  subsection: { fontFamily: 'SpaceGrotesk-SemiBold', fontSize: 18, lineHeight: 24, color: '#FFFFFF' },
  listTitle:  { fontFamily: 'SpaceGrotesk-SemiBold', fontSize: 15, lineHeight: 20, color: '#FFFFFF' },
  button:     { fontFamily: 'SpaceGrotesk-SemiBold', fontSize: 16, lineHeight: 16 },
  body:       { fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 24, color: '#FFFFFF' },
  meta:       { fontFamily: 'Inter-Regular',  fontSize: 14, lineHeight: 20, color: '#A8A8A8' },
  snippet:    { fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 18, color: '#A8A8A8' },
  chip:       { fontFamily: 'Inter-SemiBold', fontSize: 12, lineHeight: 12 },
  caption:    { fontFamily: 'Inter-Regular',  fontSize: 12, lineHeight: 16, color: '#6E6E6E' },
  tab:        { fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Discover Card (Couple/Group-aware)

```tsx
// components/DiscoverCard.tsx
import { Image, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { BlurView } from 'expo-blur';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { DesireChips } from './DesireChips';

type Desire = { label: string; state: 'selected' | 'unselected' | 'emotional' };

export function DiscoverCard({
  photoUri, names, ages, distanceKm, kind, desires, verified,
}: {
  photoUri: string; names: string[]; ages: number[];
  distanceKm: number; kind: string; desires: Desire[]; verified: boolean;
}) {
  return (
    <View style={{ flex: 1, marginHorizontal: 16, borderRadius: 28, overflow: 'hidden' }}>
      <Image source={{ uri: photoUri }} style={{ ...StyleSheetAbsolute }} resizeMode="cover" />
      <LinearGradient
        colors={['transparent', 'rgba(0,0,0,0.72)', 'rgba(0,0,0,0.92)']}
        locations={[0, 0.6, 1]}
        style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: '56%' }}
      />
      {verified && (
        <BlurView intensity={28} tint="dark" style={{
          position: 'absolute', top: 18, right: 18,
          flexDirection: 'row', alignItems: 'center', gap: 5,
          paddingHorizontal: 10, paddingVertical: 6, borderRadius: 999, overflow: 'hidden',
        }}>
          <Ionicons name="checkmark" size={12} color={colors.acid} />
          <Text style={[typography.caption, { color: '#FFFFFF', fontFamily: 'Inter-SemiBold' }]}>Verified</Text>
        </BlurView>
      )}
      <View style={{ position: 'absolute', left: 0, right: 0, bottom: 0, paddingHorizontal: 22, paddingBottom: 18 }}>
        <Text style={typography.name} numberOfLines={2}>{names.join(' & ')}</Text>
        <Text style={[typography.meta, { color: 'rgba(255,255,255,0.78)' }]}>
          {kind} · {ages.join(' & ')} · {distanceKm} km away
        </Text>
        <View style={{ marginTop: 8 }}>
          <DesireChips desires={desires} />
        </View>
      </View>
    </View>
  );
}

const StyleSheetAbsolute = { position: 'absolute' as const, top: 0, left: 0, right: 0, bottom: 0 };
```

### Action Row (Pass · Like · Wink)

```tsx
// components/ActionRow.tsx
import { Pressable, View } from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function ActionRow({ onPass, onLike, onWink }: { onPass: () => void; onLike: () => void; onWink: () => void }) {
  const likeScale = useSharedValue(1);
  const likeStyle = useAnimatedStyle(() => ({ transform: [{ scale: likeScale.value }] }));

  const handleLike = () => {
    likeScale.value = withSequence(withTiming(0.92, { duration: 0 }), withTiming(1, { duration: 180 }));
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
    onLike();
  };

  const circle = (size: number) => ({
    width: size, height: size, borderRadius: size / 2,
    alignItems: 'center' as const, justifyContent: 'center' as const,
  });

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 22, paddingVertical: 8 }}>
      <Pressable onPress={onPass} style={[circle(52), { backgroundColor: colors.surface2, borderWidth: 1, borderColor: colors.divider }]}>
        <Ionicons name="close" size={22} color={colors.textSecondary} />
      </Pressable>

      <Animated.View style={likeStyle}>
        <Pressable onPress={handleLike} style={[circle(64), {
          backgroundColor: colors.acid,
          shadowColor: colors.acid, shadowOpacity: 0.5, shadowRadius: 15, shadowOffset: { width: 0, height: 12 },
          elevation: 12,
        }]}>
          <Ionicons name="heart" size={28} color={colors.onAcid} />
        </Pressable>
      </Animated.View>

      <Pressable onPress={onWink} style={[circle(52), { backgroundColor: colors.surface2, borderWidth: 1, borderColor: colors.divider }]}>
        <Ionicons name="star" size={22} color={colors.lilac} />
      </Pressable>
    </View>
  );
}
```

### Desire Chips (wrapping)

```tsx
// components/DesireChips.tsx
import { Text, View } from 'react-native';
import { typography } from '../theme/typography';
import { chipStyles } from '../theme/colors';

type Desire = { label: string; state: keyof typeof chipStyles };

export function DesireChips({ desires }: { desires: Desire[] }) {
  return (
    <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 7 }}>
      {desires.map((d) => {
        const s = chipStyles[d.state];
        return (
          <View key={d.label} style={{
            backgroundColor: s.bg, borderColor: s.border, borderWidth: 1,
            paddingHorizontal: 15, paddingVertical: 9, borderRadius: 999,
          }}>
            <Text style={[typography.chip, { color: s.text }]}>{d.label}</Text>
          </View>
        );
      })}
    </View>
  );
}
```

### Connections Row

```tsx
// components/ConnectionRow.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ConnectionRow({
  gradient, name, snippet, time, unread,
}: { gradient: [string, string]; name: string; snippet: string; time: string; unread: boolean }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 14,
      paddingVertical: 12, borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      <LinearGradient colors={gradient} start={{ x: 0.35, y: 0.3 }} style={{ width: 48, height: 48, borderRadius: 24 }} />
      <View style={{ flex: 1 }}>
        <Text style={typography.listTitle}>{name}</Text>
        <Text style={typography.snippet} numberOfLines={1}>{snippet}</Text>
      </View>
      <View style={{ alignItems: 'flex-end', gap: 6 }}>
        <Text style={typography.caption}>{time}</Text>
        {unread && <View style={{ width: 6, height: 6, borderRadius: 3, backgroundColor: colors.acid }} />}
      </View>
    </View>
  );
}
```

### Buttons

```tsx
// components/FeeldButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FeeldPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.acidPress : colors.acid,
        paddingVertical: 15, paddingHorizontal: 30, borderRadius: 999,
        alignItems: 'center', transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={[typography.button, { color: colors.onAcid }]}>{title}</Text>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { Ionicons } from '@expo/vector-icons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.acid,        // active icon = acid; no pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarShowLabel: false,                     // icons-only is valid Feeld
        tabBarBackground: () => <BlurView intensity={30} tint="dark" style={{ flex: 1 }} />,
        tabBarStyle: {
          position: 'absolute',
          backgroundColor: 'rgba(0,0,0,0.92)',
          borderTopWidth: 0.5, borderTopColor: colors.divider,
        },
      }}
    >
      <Tabs.Screen name="index"       options={{ tabBarIcon: ({ color }) => <Ionicons name="search"  size={23} color={color} /> }} />
      <Tabs.Screen name="likes"       options={{ tabBarIcon: ({ color }) => <Ionicons name="heart-outline" size={23} color={color} /> }} />
      <Tabs.Screen name="connections" options={{ tabBarIcon: ({ color }) => <Ionicons name="chatbubbles-outline" size={23} color={color} /> }} />
      <Tabs.Screen name="profile"     options={{ tabBarIcon: ({ color }) => <Ionicons name="person-outline" size={23} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Card swipe — gesture-handler PanGestureHandler + Reanimated, rotation ±8°, spring commit
import { Gesture } from 'react-native-gesture-handler';
import { withSpring, runOnJS } from 'react-native-reanimated';

const pan = Gesture.Pan()
  .onChange((e) => {
    tx.value = e.translationX;
    rot.value = (e.translationX / cardWidth) * 8;
  })
  .onEnd((e) => {
    if (Math.abs(e.translationX) > cardWidth * 0.35) {
      tx.value = withSpring(e.translationX > 0 ? 800 : -800, { damping: 18 });
      runOnJS(onSwiped)(e.translationX > 0 ? 'like' : 'pass');
    } else {
      tx.value = withSpring(0, { damping: 16 });
      rot.value = withSpring(0);
    }
  });

// Like tap — withSequence(0.92 → 1.0 over 180ms) + Haptics.Soft (see ActionRow)

// Wink bounce — scale 1 → 1.25 → 1 over 260ms
winkScale.value = withSequence(withTiming(1.25, { duration: 130 }), withTiming(1, { duration: 130 }));

// Connection made — full-screen, avatars slide from edges with withTiming(0, { duration: 320 }) + acid burst

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);    // Like commit
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);   // Wink
Haptics.selectionAsync();                                  // chip toggle
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // Connection
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Feeld's iconography is simple line/solid; Ionicons covers it.

| Purpose | Ionicons |
|---------|----------|
| Discover (tab) | `search` |
| Likes (tab) | `heart-outline` / `heart` |
| Connections (tab) | `chatbubbles-outline` |
| Profile (tab) | `person-outline` |
| Pass | `close` |
| Like | `heart` |
| Wink / super-interest | `star` |
| Verified | `checkmark` |
| Filters | `options-outline` |
| Settings | `settings-outline` |
| Back | `chevron-back` |
| Send message | `arrow-up-circle` |
| Block / report | `alert-circle-outline` |
| Add photo | `add` |
| Photo nav dots | custom small `View` dots |

## 7. Platform Notes

- **Dark-only**: do not implement a light theme. Lock the app dark — set `contentStyle.backgroundColor: '#000000'` on the root `Stack`, `<StatusBar style="light" />` everywhere, and never branch on `useColorScheme()` for palette
- **Fonts**: Space Grotesk + Inter are SIL OFL — free to bundle. Ship both; Grotesk for personality, Inter for legibility
- **Couple/group names**: set `numberOfLines={2}` and consider `adjustsFontSizeToFit` on the name `<Text>` so a two-name couple never truncates
- **Status bar**: always `<StatusBar style="light" />` (white text on black)
- **Safe area**: wrap screens in `SafeAreaView`; the absolute tab bar needs bottom safe-area padding; the action row must sit above the tab bar
- **Blur**: `expo-blur` `BlurView` powers the verified pill and tab bar; on Android blur is approximate — fall back to `rgba(0,0,0,0.85)` solid
- **Dynamic Type**: set `allowFontScaling={false}` on Desire chip text, tab labels, verified-pill text (layout-sensitive pills); allow scaling on display/name/section/body/snippet
- **Gestures**: card swipe needs `react-native-gesture-handler`'s `Gesture.Pan()` + Reanimated worklets for 60fps; commit threshold at 35% card width
- **Accessibility**: give the Discover card an `accessibilityLabel` describing names/kind/ages/distance; expose Like/Pass/Wink as `accessibilityActions` so the card works without swiping; the acid Like glow is decorative — the heart icon must carry the meaning
