# AllTrails (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates AllTrails' visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, and `react-native-svg` (for the route trace).

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:        '#FFFFFF',
  surfaceSage:   '#F2F4F1',
  surfaceSunken: '#EBEEE8',
  divider:       '#E2E6DF',

  textPrimary:   '#1A1A1A',
  textSecondary: '#6B6F68',
  textTertiary:  '#9A9E96',

  green:        '#428000',
  greenPressed: '#336300',
  greenSoft:    '#E8F0DF',

  easy:     '#428000',
  moderate: '#C77700',
  hard:     '#B3261E',
  easyBg:     '#E8F0DF',
  moderateBg: '#FBEEDD',
  hardBg:     '#F7E0DE',

  starGold: '#F2A93B',
} as const;

export type Difficulty = 'easy' | 'moderate' | 'hard';
export const difficultyColor: Record<Difficulty, string> = {
  easy: colors.easy, moderate: colors.moderate, hard: colors.hard,
};
export const difficultyBg: Record<Difficulty, string> = {
  easy: colors.easyBg, moderate: colors.moderateBg, hard: colors.hardBg,
};
export const difficultyLabel: Record<Difficulty, string> = {
  easy: 'EASY', moderate: 'MODERATE', hard: 'HARD',
};
```

## 2. Typography

Load Inter via `expo-font`. Use tabular figures on stats and ratings.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ headerShown: false }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tabular = { fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] };
const ink = { color: '#1A1A1A' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...ink, fontFamily: 'Inter-Bold',     fontSize: 28, letterSpacing: -0.4 },
  detailTitle: { ...ink, fontFamily: 'Inter-Bold',     fontSize: 24, letterSpacing: -0.3 },
  section:     { ...ink, fontFamily: 'Inter-Bold',     fontSize: 20, letterSpacing: -0.2 },
  cardTitle:   { ...ink, fontFamily: 'Inter-SemiBold', fontSize: 17, letterSpacing: -0.1 },
  body:        { ...ink, fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 25 },
  statValue:   { ...ink, fontFamily: 'Inter-Bold',     fontSize: 15, ...tabular },
  subtitle:    {          fontFamily: 'Inter-Regular',  fontSize: 14, lineHeight: 20, color: '#6B6F68' },
  difficulty:  {          fontFamily: 'Inter-Bold',     fontSize: 13, letterSpacing: 0.3 },
  statLabel:   {          fontFamily: 'Inter-Regular',  fontSize: 13, color: '#6B6F68' },
  button:      {          fontFamily: 'Inter-SemiBold', fontSize: 16, letterSpacing: 0.1, color: '#FFFFFF' },
  rating:      { ...ink, fontFamily: 'Inter-Bold',     fontSize: 14, ...tabular },
  meta:        {          fontFamily: 'Inter-Regular',  fontSize: 13, color: '#6B6F68' },
  tab:         {          fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.2 },
  pinLabel:    {          fontFamily: 'Inter-Bold',     fontSize: 12, color: '#FFFFFF' },
  labelUpper:  {          fontFamily: 'Inter-Bold',     fontSize: 12, letterSpacing: 0.8, color: '#6B6F68', textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Difficulty Pill

```tsx
// components/DifficultyPill.tsx
import { View, Text } from 'react-native';
import { difficultyColor, difficultyBg, difficultyLabel, Difficulty } from '../theme/colors';
import { typography } from '../theme/typography';

export function DifficultyPill({
  difficulty, solid = false,
}: { difficulty: Difficulty; solid?: boolean }) {
  return (
    <View style={{
      paddingVertical: 6, paddingHorizontal: 12, borderRadius: 11,
      backgroundColor: solid ? difficultyColor[difficulty] : difficultyBg[difficulty],
    }}>
      <Text style={[typography.difficulty,
        { color: solid ? '#FFFFFF' : difficultyColor[difficulty] }]}>
        {difficultyLabel[difficulty]}
      </Text>
    </View>
  );
}
```

### Trail Card (signature)

```tsx
// components/TrailCard.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { DifficultyPill } from './DifficultyPill';
import { colors, Difficulty } from '../theme/colors';
import { typography } from '../theme/typography';

export function TrailCard({
  name, location, difficulty, rating, reviews, length, time, elevation,
  photo, saved, onToggleSave, onPress,
}: {
  name: string; location: string; difficulty: Difficulty;
  rating: number; reviews: number; length: string; time: string; elevation: string;
  photo: any; saved: boolean; onToggleSave: () => void; onPress: () => void;
}) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({ gap: 10, transform: [{ scale: pressed ? 0.98 : 1 }] })}
    >
      <View>
        <Image source={photo} style={{ width: '100%', aspectRatio: 4 / 3, borderRadius: 12 }} />
        <Pressable
          onPress={onToggleSave}
          style={{
            position: 'absolute', top: 12, right: 12, width: 34, height: 34,
            borderRadius: 17, backgroundColor: 'rgba(0,0,0,0.25)',
            alignItems: 'center', justifyContent: 'center',
          }}
        >
          <Ionicons name={saved ? 'heart' : 'heart-outline'} size={18}
            color={saved ? colors.green : '#FFF'} />
        </Pressable>
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
        <DifficultyPill difficulty={difficulty} />
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
          <Ionicons name="star" size={12} color={colors.starGold} />
          <Text style={typography.rating}>{rating.toFixed(1)}</Text>
          <Text style={typography.meta}>({reviews})</Text>
        </View>
      </View>
      <Text style={typography.cardTitle} numberOfLines={1}>{name}</Text>
      <Text style={typography.subtitle}>{location}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <Stat label="Length" value={length} />
        <Text style={{ color: colors.textTertiary }}>·</Text>
        <Stat label="Est." value={time} />
        <Text style={{ color: colors.textTertiary }}>·</Text>
        <Stat label="↑" value={elevation} />
      </View>
    </Pressable>
  );
}

function Stat({ label, value }: { label: string; value: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
      <Text style={typography.statLabel}>{label}</Text>
      <Text style={typography.statValue}>{value}</Text>
    </View>
  );
}
```

### Record Button (signature)

```tsx
// components/RecordButton.tsx
import { useEffect } from 'react';
import { Pressable } from 'react-native';
import Animated, {
  useSharedValue, useAnimatedStyle, withRepeat, withTiming, withSpring,
} from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function RecordButton({
  recording, onToggle,
}: { recording: boolean; onToggle: () => void }) {
  const scale = useSharedValue(1);

  useEffect(() => {
    scale.value = recording
      ? withRepeat(withTiming(1.06, { duration: 800 }), -1, true)
      : withSpring(1);
  }, [recording]);

  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onToggle(); }}
    >
      <Animated.View
        style={[
          {
            width: 60, height: 60, borderRadius: 30,
            backgroundColor: recording ? colors.hard : colors.green,
            alignItems: 'center', justifyContent: 'center',
            shadowColor: recording ? colors.hard : colors.green,
            shadowOpacity: 0.30, shadowRadius: 18, shadowOffset: { width: 0, height: 6 },
          },
          aStyle,
        ]}
      >
        <Ionicons name={recording ? 'stop' : 'radio-button-on'} size={26} color="#FFF" />
      </Animated.View>
    </Pressable>
  );
}
```

### Route Trace (signature)

```tsx
// components/RouteTrace.tsx
import { useEffect } from 'react';
import Svg, { Path } from 'react-native-svg';
import Animated, {
  useSharedValue, useAnimatedProps, withTiming, Easing,
} from 'react-native-reanimated';
import { colors } from '../theme/colors';

const APath = Animated.createAnimatedComponent(Path);

export function RouteTrace({ d, length }: { d: string; length: number }) {
  const progress = useSharedValue(0);
  useEffect(() => {
    progress.value = withTiming(1, { duration: 1000, easing: Easing.out(Easing.cubic) });
  }, []);
  const aProps = useAnimatedProps(() => ({
    strokeDashoffset: length * (1 - progress.value),
  }));
  return (
    <Svg style={{ position: 'absolute', inset: 0 }}>
      <APath
        d={d} stroke={colors.green} strokeWidth={4} fill="none"
        strokeLinecap="round" strokeLinejoin="round"
        strokeDasharray={length} animatedProps={aProps}
      />
    </Svg>
  );
}
```

### Primary / Outline Buttons

```tsx
// components/Buttons.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ATPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={({ pressed }) => ({
        paddingVertical: 14, paddingHorizontal: 28, borderRadius: 25, alignItems: 'center',
        backgroundColor: pressed ? colors.greenPressed : colors.green,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function ATOutlineButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        paddingVertical: 13, paddingHorizontal: 24, borderRadius: 25, alignItems: 'center',
        borderWidth: 1.5, borderColor: colors.green,
        backgroundColor: pressed ? colors.greenSoft : 'transparent',
      })}
    >
      <Text style={[typography.button, { color: colors.green }]}>{title}</Text>
    </Pressable>
  );
}
```

## 4. Tab Bar

`expo-router` Tabs with a near-opaque white `BlurView`. **Active tint is AllTrails Green.**

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
        headerShown: false,
        tabBarActiveTintColor: colors.green,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider,
          backgroundColor: 'transparent',
        },
        tabBarBackground: () => (
          <BlurView intensity={70} tint="light"
            style={{ flex: 1, backgroundColor: 'rgba(255,255,255,0.96)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Explore',   tabBarIcon: ({ color }) => <Ionicons name="map"             size={24} color={color} /> }} />
      <Tabs.Screen name="saved"     options={{ title: 'Saved',     tabBarIcon: ({ color }) => <Ionicons name="bookmark"        size={24} color={color} /> }} />
      <Tabs.Screen name="navigator" options={{ title: 'Navigator', tabBarIcon: ({ color }) => <Ionicons name="navigate-circle" size={24} color={color} /> }} />
      <Tabs.Screen name="community" options={{ title: 'Community', tabBarIcon: ({ color }) => <Ionicons name="people"          size={24} color={color} /> }} />
      <Tabs.Screen name="profile"   options={{ title: 'Profile',   tabBarIcon: ({ color }) => <Ionicons name="person-circle"   size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Route trace draw — strokeDashoffset length → 0 over 1000ms ease-out (see RouteTrace)

// Map pin select — pin scales ~1.2× + white ring; mini card slides up 300ms ease-out
pinScale.value = withTiming(1.2, { duration: 300, easing: Easing.out(Easing.cubic) });

// Card tap — scale 0.98 via Pressable, ~200ms

// Map/List toggle — active fill slides 250ms
fill.value = withTiming(isMap ? 0 : 1, { duration: 250 });

// Record start — green circle → red stop, firm haptic, banner expands
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Save heart — fill + bounce 1.0 → 1.15 → 1.0 over 300ms
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
```

Haptics via `expo-haptics`: `impactAsync(Soft)` on primary CTAs, `impactAsync(Medium)` on Record start/stop, `notificationAsync(Success)` on save.

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to AllTrails' SF Symbol equivalents:

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Record | `record.circle` | `radio-button-on` |
| Stop recording | `stop.fill` | `stop` |
| Save | `heart` / `heart.fill` | `heart-outline` / `heart` |
| Star (rating) | `star.fill` | `star` |
| Directions | `arrow.triangle.turn.up.right.diamond.fill` | `navigate` |
| Share | `square.and.arrow.up` | `share-outline` |
| Filter | `slider.horizontal.3` | `options-outline` |
| Search | `magnifyingglass` | `search` |
| Elevation | `arrow.up.forward` | `trending-up` |
| Explore (tab) | `map.fill` | `map` |
| Saved (tab) | `bookmark.fill` | `bookmark` |
| Navigator (tab) | `location.north.circle.fill` | `navigate-circle` |
| Community (tab) | `person.2.fill` | `people` |
| Profile (tab) | `person.crop.circle.fill` | `person-circle` |

## 7. Platform Notes

- **Map**: use `react-native-maps` (Apple Maps on iOS) for the Explore base; render difficulty-tinted markers and the route polyline. For the animated trace overlay, an `Svg` layer on top of a static snapshot keeps the draw smooth
- **SVG route**: the route trace needs `react-native-svg` + `react-native-reanimated`'s `useAnimatedProps` so `strokeDashoffset` animates on the UI thread
- **Tabular figures**: `fontVariant: ['tabular-nums']` works on iOS; on Android also ship an Inter build with tabular numerals so a scrolling trail list keeps its stat columns aligned
- **Light-first**: set `#FFFFFF` canvas; `<StatusBar style="dark" />` from `expo-status-bar` globally. A dark mode exists for OS parity / night nav — branch on `useColorScheme()` only if you ship it; light is primary
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the map extends edge-to-edge under floating controls
- **Outdoor legibility**: keep contrast high; do not auto-dim content — the bright light theme is intentional for in-sun reading
- **Dynamic Type**: RN honors font scaling — keep it on titles/names/body; allow the stat row to wrap; set `allowFontScaling={false}` on the difficulty pill, tab labels, map-pin labels
- **Accessibility**: trail card is one element with a full `accessibilityLabel` (name, difficulty, rating, stats); never rely on the difficulty color alone — the pill always carries the word; on Reduce Motion show the full route immediately
