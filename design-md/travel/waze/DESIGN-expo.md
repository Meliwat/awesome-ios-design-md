# Waze (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Waze's playful cartoon visual language into paste-ready Expo / React Native code: a design-token module, themed components for the hazard speech bubble, the next-turn card, the report FAB with tinted purple shadow, the cyan arrow puck, and the speed-limit tile.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  purple:         '#7E55BE',
  purpleDeep:     '#5B3C9A',
  purpleTint:     '#E8DEF5',
  cyan:           '#33CCFF',
  cyanDeep:       '#0099E5',

  // Hazard colors
  policeRed:      '#EF6A65',
  trafficOrange:  '#F69833',
  closureYellow:  '#F9C42E',
  clearedGreen:   '#75C73E',
  hazardBrown:    '#8B6F47',
  cameraGray:     '#6B6B6B',

  // Map cartography
  mapCream:       '#FFFCF2',
  mapWater:       '#9BDEEF',
  mapPark:        '#C5E89B',
  mapRoadMajor:   '#FFFFFF',
  mapRoadMinor:   '#F5F0E5',
  mapHighway:     '#FFD970',
  mapBuilding:    '#E8E2D1',
  mapLabel:       '#3D3D3D',

  // UI chrome
  cardCanvas:     '#FFFFFF',
  surfaceGray:    '#F5F5F7',
  surfaceGray2:   '#EAEAEC',
  divider:        '#D6D6D9',

  // Text
  ink:            '#1A1A1A',
  secondary:      '#6B6B6B',
  tertiary:       '#A0A0A0',

  // Semantic
  success:        '#34C759',
  warning:        '#F9C42E',
  error:          '#EF6A65',

  // Dark mode
  darkMapLand:    '#1E2026',
  darkMapWater:   '#0F3D5E',
  darkMapPark:    '#1F3A1F',
  darkCardSurf:   '#262932',
  darkSurface2:   '#3A3D47',
  darkDivider:    '#4A4D58',
  purpleDark:     '#9F76DA',
  cyanDark:       '#5DD9FF',
} as const;
```

## 2. Typography

Boing is proprietary to Waze. The correct fallback on iOS is SF Pro Rounded (use `fontFamily: 'System'` — iOS handles rounding via the design parameter at the component level isn't directly exposed in RN, so we approximate via Quicksand/Nunito or use `fontFamily: 'AvenirNext-Medium'` for a rounded approximation). For best fidelity, bundle Quicksand from Google Fonts.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Quicksand-Regular': require('../assets/fonts/Quicksand-Regular.ttf'),
    'Quicksand-Medium':  require('../assets/fonts/Quicksand-Medium.ttf'),
    'Quicksand-SemiBold': require('../assets/fonts/Quicksand-SemiBold.ttf'),
    'Quicksand-Bold':    require('../assets/fonts/Quicksand-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const family = {
  regular:  'Quicksand-Regular',
  medium:   'Quicksand-Medium',
  semibold: 'Quicksand-SemiBold',
  bold:     'Quicksand-Bold',
};

const tabular = { fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] };

export const typography = {
  // Hero next-turn
  heroStreet:    { fontFamily: family.bold,     fontSize: 32, color: '#FFFFFF', letterSpacing: -0.4, lineHeight: 36 },
  nextTurnDist:  { ...tabular, fontFamily: family.bold, fontSize: 28, color: '#FFFFFF', lineHeight: 32 },
  stepTitle:     { fontFamily: family.bold,     fontSize: 22, color: '#FFFFFF', letterSpacing: -0.2, lineHeight: 26 },
  stepSubtitle:  { fontFamily: family.regular,  fontSize: 17, color: 'rgba(255,255,255,0.8)', lineHeight: 22 },

  // ETA
  etaTime:       { ...tabular, fontFamily: family.bold, fontSize: 24, color: '#1A1A1A', lineHeight: 28 },
  etaDistance:   { ...tabular, fontFamily: family.medium, fontSize: 17, color: '#6B6B6B', lineHeight: 22 },

  // Place card
  placeTitle:    { fontFamily: family.bold,     fontSize: 26, color: '#1A1A1A', letterSpacing: -0.3, lineHeight: 30 },
  placeSubtitle: { fontFamily: family.regular,  fontSize: 15, color: '#6B6B6B', lineHeight: 20 },

  // Search
  searchPlaceholder: { fontFamily: family.regular, fontSize: 17, color: '#A0A0A0', lineHeight: 22 },
  section:       { fontFamily: family.bold,     fontSize: 13, color: '#6B6B6B', letterSpacing: 0.6, textTransform: 'uppercase' as const, lineHeight: 16 },

  // Lists
  listTitle:     { fontFamily: family.medium,   fontSize: 17, color: '#1A1A1A', lineHeight: 22 },
  listSubtitle:  { fontFamily: family.regular,  fontSize: 13, color: '#6B6B6B', lineHeight: 16 },

  // Speed tile
  speedLimitLbl: { fontFamily: family.bold,     fontSize: 9,  color: '#1A1A1A', lineHeight: 11 },
  speedLimitNum: { ...tabular, fontFamily: family.bold, fontSize: 22, color: '#1A1A1A', lineHeight: 24 },
  currentSpeed:  { ...tabular, fontFamily: family.bold, fontSize: 32, color: '#1A1A1A', lineHeight: 36 },
  mphLabel:      { fontFamily: family.regular,  fontSize: 11, color: '#6B6B6B', lineHeight: 13 },

  // Hazard speech bubble
  hazardTitle:   { fontFamily: family.bold,     fontSize: 14, color: '#FFFFFF', lineHeight: 16 },
  hazardTime:    { fontFamily: family.regular,  fontSize: 11, color: 'rgba(255,255,255,0.8)', lineHeight: 13 },

  // Buttons
  button:        { fontFamily: family.bold,     fontSize: 17, lineHeight: 22 },

  caption:       { fontFamily: family.regular,  fontSize: 12, color: '#6B6B6B', lineHeight: 16 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Hazard Speech Bubble (Signature)

```tsx
// components/HazardBubble.tsx
import { useEffect } from 'react';
import { View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Svg, { Path } from 'react-native-svg';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type HazardType = 'police' | 'traffic' | 'closure' | 'cleared' | 'pothole' | 'camera';

const config: Record<HazardType, { color: string; icon: keyof typeof Ionicons.glyphMap; title: string }> = {
  police:  { color: colors.policeRed,     icon: 'shield',                title: 'Police' },
  traffic: { color: colors.trafficOrange, icon: 'warning',               title: 'Traffic' },
  closure: { color: colors.closureYellow, icon: 'remove-circle',         title: 'Closure' },
  cleared: { color: colors.clearedGreen,  icon: 'checkmark-circle',      title: 'Cleared' },
  pothole: { color: colors.hazardBrown,   icon: 'alert',                 title: 'Pothole' },
  camera:  { color: colors.cameraGray,    icon: 'camera',                title: 'Camera' },
};

export function HazardBubble({ type, timeAgo }: { type: HazardType; timeAgo?: string }) {
  const scale = useSharedValue(0);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  useEffect(() => {
    scale.value = withSpring(1, { damping: 8, stiffness: 200 });
  }, []);

  const c = config[type];

  return (
    <Animated.View style={[{
      alignItems: 'center',
      shadowColor: '#000', shadowOpacity: 0.20, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
      elevation: 6,
    }, aStyle]}>
      <View style={{
        flexDirection: 'row', alignItems: 'center', gap: 8,
        paddingHorizontal: 12, paddingVertical: 8,
        backgroundColor: c.color, borderRadius: 14,
      }}>
        <Ionicons name={c.icon} size={20} color="#FFFFFF" />
        <View>
          <Text style={typography.hazardTitle}>{c.title}</Text>
          {timeAgo && <Text style={typography.hazardTime}>{timeAgo}</Text>}
        </View>
      </View>

      {/* Triangle tail */}
      <Svg width="12" height="8" viewBox="0 0 12 8">
        <Path d="M0 0 L12 0 L6 8 Z" fill={c.color} />
      </Svg>
    </Animated.View>
  );
}
```

### Floating Action Button (Tinted Purple Shadow)

```tsx
// components/WazeFAB.tsx
import { Pressable, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function WazeFAB({ onPress }: { onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Animated.View style={[{
      position: 'absolute', bottom: 100, right: 16,
      width: 56, height: 56, borderRadius: 28,
      shadowColor: colors.purple,
      shadowOpacity: 0.40,
      shadowRadius: 16,
      shadowOffset: { width: 0, height: 6 },
      elevation: 10,
    }, style]}>
      <Pressable
        onPressIn={() => { scale.value = withSpring(0.94); }}
        onPressOut={() => { scale.value = withSpring(1); }}
        onPress={() => {
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
          onPress();
        }}
        style={({ pressed }) => ({
          width: 56, height: 56, borderRadius: 28,
          backgroundColor: pressed ? colors.purpleDeep : colors.purple,
          alignItems: 'center', justifyContent: 'center',
        })}
      >
        <Ionicons name="warning" size={24} color="#FFFFFF" />
      </Pressable>
    </Animated.View>
  );
}
```

### Current-Location Puck (Cyan Arrow with Pulse)

```tsx
// components/LocationPuck.tsx
import { useEffect } from 'react';
import { View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, Easing } from 'react-native-reanimated';
import Svg, { Path } from 'react-native-svg';
import { colors } from '../theme/colors';

export function LocationPuck({ heading = 0 }: { heading?: number }) {
  const pulseScale = useSharedValue(1);
  const pulseOpacity = useSharedValue(0.15);

  useEffect(() => {
    pulseScale.value = withRepeat(withTiming(1.5, { duration: 2000, easing: Easing.out(Easing.cubic) }), -1, false);
    pulseOpacity.value = withRepeat(withTiming(0, { duration: 2000 }), -1, false);
  }, []);

  const pulseStyle = useAnimatedStyle(() => ({
    transform: [{ scale: pulseScale.value }],
    opacity: pulseOpacity.value,
  }));

  return (
    <View style={{ width: 80, height: 80, alignItems: 'center', justifyContent: 'center' }}>
      {/* Pulse ring */}
      <Animated.View style={[{
        position: 'absolute',
        width: 60, height: 60, borderRadius: 30,
        backgroundColor: colors.cyan,
      }, pulseStyle]} />

      {/* Arrow */}
      <View style={{
        transform: [{ rotate: `${heading}deg` }],
        shadowColor: '#000', shadowOpacity: 0.30, shadowRadius: 8, shadowOffset: { width: 0, height: 3 },
        elevation: 4,
      }}>
        <Svg width="32" height="36" viewBox="0 0 32 36">
          <Path d="M16 0 L32 30 L16 25 L0 30 Z" fill={colors.cyan} stroke="#FFFFFF" strokeWidth="3" strokeLinejoin="round" />
        </Svg>
      </View>
    </View>
  );
}
```

### Next-Turn Card

```tsx
// components/NextTurnCard.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  arrow: keyof typeof Ionicons.glyphMap;
  arrowRotation: number;
  distance: string;
  streetName: string;
  subInstruction?: string;
};

export function NextTurnCard({ arrow, arrowRotation, distance, streetName, subInstruction }: Props) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 16,
      paddingHorizontal: 20, paddingVertical: 16,
      backgroundColor: colors.purple,
      borderBottomLeftRadius: 16, borderBottomRightRadius: 16,
    }}>
      <View style={{ transform: [{ rotate: `${arrowRotation}deg` }] }}>
        <Ionicons name={arrow} size={44} color="#FFFFFF" />
      </View>

      <View style={{ flex: 1 }}>
        <Text style={typography.nextTurnDist}>{distance}</Text>
        <Text style={typography.stepTitle} numberOfLines={2}>{streetName}</Text>
        {subInstruction && <Text style={typography.stepSubtitle}>{subInstruction}</Text>}
      </View>
    </View>
  );
}
```

### ETA Bottom Bar

```tsx
// components/WazeETABar.tsx
import { View, Text, Pressable } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  duration: string;
  distance: string;
  arrival: string;
  alternativeSaves?: string;
  onEnd: () => void;
};

export function WazeETABar({ duration, distance, arrival, alternativeSaves, onEnd }: Props) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 16,
      paddingHorizontal: 20, height: 80,
      backgroundColor: colors.cardCanvas,
    }}>
      <View style={{ flex: 1 }}>
        <Text style={typography.etaTime}>{duration}</Text>
        <Text style={typography.etaDistance}>{distance} · {arrival}</Text>
      </View>

      {alternativeSaves && (
        <View style={{ backgroundColor: colors.clearedGreen, paddingVertical: 8, paddingHorizontal: 14, borderRadius: 500 }}>
          <Text style={{ fontFamily: 'Quicksand-Bold', fontSize: 14, color: '#FFFFFF' }}>{alternativeSaves}</Text>
        </View>
      )}

      <Pressable
        onPress={onEnd}
        style={({ pressed }) => ({
          paddingVertical: 12, paddingHorizontal: 20,
          borderRadius: 500,
          backgroundColor: colors.cardCanvas,
          borderWidth: 1.5, borderColor: colors.error,
          opacity: pressed ? 0.85 : 1,
        })}
      >
        <Text style={[typography.button, { color: colors.error }]}>End</Text>
      </Pressable>
    </View>
  );
}
```

### Speed Limit Tile

```tsx
// components/SpeedTile.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = { limit: number; current: number };

export function SpeedTile({ limit, current }: Props) {
  const isSpeeding = current > limit;
  return (
    <View style={{
      width: 80,
      paddingVertical: 8, paddingHorizontal: 8,
      backgroundColor: colors.cardCanvas, borderRadius: 12,
      alignItems: 'center', gap: 6,
      shadowColor: '#000', shadowOpacity: 0.10, shadowRadius: 8, shadowOffset: { width: 0, height: 4 },
      elevation: 4,
    }}>
      <Text style={typography.speedLimitLbl}>SPEED LIMIT</Text>
      <View style={{
        width: 40, height: 40, borderRadius: 20,
        backgroundColor: '#FFFFFF',
        borderWidth: 2, borderColor: colors.ink,
        alignItems: 'center', justifyContent: 'center',
      }}>
        <Text style={typography.speedLimitNum}>{limit}</Text>
      </View>

      <View style={{ height: 0.5, backgroundColor: colors.divider, width: '70%' }} />

      <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 4 }}>
        <Text style={[typography.currentSpeed, { color: isSpeeding ? colors.error : colors.ink }]}>{current}</Text>
        <Text style={typography.mphLabel}>mph</Text>
      </View>
    </View>
  );
}
```

### "Go" Button

```tsx
// components/GoButton.tsx
import { Pressable, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function GoButton({ onPress }: { onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Animated.View style={[{
      shadowColor: colors.purple, shadowOpacity: 0.30, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
      elevation: 8,
    }, style]}>
      <Pressable
        onPressIn={() => { scale.value = withSpring(0.97); }}
        onPressOut={() => { scale.value = withSpring(1); }}
        onPress={() => {
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);
          onPress();
        }}
        style={({ pressed }) => ({
          height: 56, borderRadius: 28,
          backgroundColor: pressed ? colors.purpleDeep : colors.purple,
          alignItems: 'center', justifyContent: 'center',
          paddingHorizontal: 32,
        })}
      >
        <Text style={[typography.button, { color: '#FFFFFF' }]}>Go</Text>
      </Pressable>
    </Animated.View>
  );
}
```

### Wazer Avatar (Mood Picker)

```tsx
// components/WazerAvatar.tsx
import { Pressable, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withSequence } from 'react-native-reanimated';
import { colors } from '../theme/colors';

export function WazerAvatar({ emoji, onPress }: { emoji: string; onPress?: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable onPress={() => {
      scale.value = withSequence(withSpring(1.2, { damping: 4 }), withSpring(1, { damping: 8 }));
      onPress?.();
    }}>
      <Animated.View style={[{
        width: 24, height: 24, borderRadius: 12,
        backgroundColor: '#FFFFFF',
        borderWidth: 2, borderColor: '#FFFFFF',
        alignItems: 'center', justifyContent: 'center',
        shadowColor: '#000', shadowOpacity: 0.20, shadowRadius: 4, shadowOffset: { width: 0, height: 2 },
        elevation: 3,
      }, style]}>
        <Text style={{ fontSize: 16 }}>{emoji}</Text>
      </Animated.View>
    </Pressable>
  );
}
```

## 4. Tab Bar (iPad / Settings Mode)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.purple,
        tabBarInactiveTintColor: colors.secondary,
        tabBarLabelStyle: { fontFamily: 'Quicksand-Medium', fontSize: 10 },
        tabBarStyle: { backgroundColor: colors.cardCanvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
      }}
    >
      <Tabs.Screen name="map"      options={{ title: 'Drive',  tabBarIcon: ({ color }) => <Ionicons name="navigate"  size={22} color={color} /> }} />
      <Tabs.Screen name="plan"     options={{ title: 'Plan',   tabBarIcon: ({ color }) => <Ionicons name="map"       size={22} color={color} /> }} />
      <Tabs.Screen name="inbox"    options={{ title: 'Inbox',  tabBarIcon: ({ color }) => <Ionicons name="mail"      size={22} color={color} /> }} />
      <Tabs.Screen name="settings" options={{ title: 'Me',     tabBarIcon: ({ color }) => <Ionicons name="person"    size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// FAB tap (open report wheel)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// "Go" tap (start navigation)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);

// Hazard report submitted
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Arrived
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Speeding warning
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Warning);

// Hazard bubble "pop" appearance
scale.value = withSpring(1, { damping: 8, stiffness: 200 });

// Polyline ant trail (chevron animation)
dashPhase.value = withRepeat(withTiming(-24, { duration: 2000 }), -1, false);
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons + MaterialCommunityIcons for hazard variety):

| Purpose | Ionicons |
|---------|----------|
| FAB report | `warning` (or `chatbox` for speech bubble feel) |
| Police hazard | `shield-checkmark` |
| Traffic | `warning` |
| Closure | `remove-circle` |
| Cleared | `checkmark-circle` |
| Pothole | `alert` |
| Camera | `camera` |
| Direction right | `arrow-redo` |
| Direction left | `arrow-undo` |
| Search | `search` |
| Speed warning | `alert-circle` |
| Gas | `car` (or MCI `gas-station`) |
| Home | `home` |
| Work | `briefcase` |
| Favorite | `star` |
| Recent | `time` |
| Map (tab) | `map` |
| Plan (tab) | `compass` |
| Inbox (tab) | `mail` |
| Me (tab) | `person` |
| Mood emoji | (use `<Text>` emoji rendering) |

## 7. Platform Notes

- **Tinted shadows on Android**: Android's `elevation` doesn't tint the shadow color. To approximate the tinted purple FAB shadow on Android, wrap the FAB in a `View` with a semi-transparent purple background slightly offset and blurred, or accept the gray default with `elevation: 10`.
- **SVG for the speech bubble tail and cyan arrow**: use `react-native-svg`. These shapes can't be approximated with `borderRadius` alone.
- **Quicksand font as Boing fallback**: bundle via `expo-font`. iOS-specific: you can also use `AvenirNext-DemiBold` for the rounded look without bundling fonts, but Quicksand more accurately captures Boing's playful warmth.
- **Status bar**: Set `<StatusBar style="light" />` when the navigation purple card is visible at the top; `style="dark"` otherwise.
- **Safe area**: Wrap navigation in `SafeAreaProvider`. The next-turn card extends into the safe area at the top; the ETA bar respects safe area at the bottom.
- **Tabular numerals**: Use `fontVariant: ['tabular-nums']` on every Text that renders distance, time, speed, or ETA — non-tabular numerals break the at-a-glance read while driving.
- **Map provider**: Waze uses a proprietary map renderer. With `react-native-maps`, you can approximate the Waze cartography by providing a `customMapStyle` JSON that overrides land, water, parks, roads — match the colors from the cartography tokens above.
- **Dark mode**: use `useColorScheme()` to swap map cartography and UI surfaces. Critically, Waze Purple and Cyan stay at brand saturation (brightened to `#9F76DA` and `#5DD9FF`) — do not desaturate brand colors in dark mode.
- **Accessibility**: hazard speech bubbles need `accessibilityLabel="Police reported 5 minutes ago, ahead on Market Street"`. The cyan arrow puck reads as `"Your current position, heading north"`.
- **Performance**: hazard reports along a busy route can number in the dozens — virtualize off-screen reports and only render those within the viewport. Animations are inexpensive (single `withSpring`), but rendering 50+ SVG bubbles can drop frame rate; switch to a single `Canvas` (`@shopify/react-native-skia`) for high-density rendering if needed.
