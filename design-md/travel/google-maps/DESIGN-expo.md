# Google Maps (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Google Maps' visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, `react-native-maps` (bare workflow / dev-client) or `expo-maps` on iOS.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas (light)
  canvas:        '#FFFFFF',
  surfaceMuted:  '#F1F3F4',
  divider:       '#DADCE0',

  // Text
  textPrimary:   '#202124',
  textSecondary: '#5F6368',
  textTertiary:  '#80868B',

  // Google logo colors
  blue:          '#4285F4',
  bluePressed:   '#1A73E8',
  blueDark:      '#174EA6',
  red:           '#EA4335',
  yellow:        '#FBBC04',
  green:         '#34A853',
  orange:        '#FB8C00',

  // Map tiles
  roadWhite:     '#FFFFFF',
  highwayYellow: '#FDF6E3',
  waterBlue:     '#AADAFF',
  parkGreen:     '#C8E6C9',
  buildingFill:  '#F0F0F0',

  // Dark
  darkCanvas:    '#202124',
  darkSurface1:  '#2D2E31',
  darkSurface2:  '#3C4043',
  darkTextPrim:  '#E8EAED',

  // Halo
  blueHalo:      'rgba(66,133,244,0.18)',
  blueHaloEdge:  'rgba(66,133,244,0.4)',
} as const;

export type GMColor = keyof typeof colors;
```

## 2. Typography

Load Roboto via `expo-font` (free on Google Fonts). Google Sans is restricted; fall back to `System` (SF Pro).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Roboto-Regular':    require('../assets/fonts/Roboto-Regular.ttf'),
    'Roboto-Medium':     require('../assets/fonts/Roboto-Medium.ttf'),
    'Roboto-Bold':       require('../assets/fonts/Roboto-Bold.ttf'),
    // If you have a licensed Google Sans bundle
    // 'GoogleSans-Medium': require('../assets/fonts/GoogleSans-Medium.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  navTurn:      { fontFamily: 'System', fontWeight: '700', fontSize: 36, lineHeight: 40, letterSpacing: -0.4, color: '#FFFFFF' },
  screenTitle:  { fontFamily: 'System', fontWeight: '500', fontSize: 28, lineHeight: 32, letterSpacing: -0.3, color: '#202124' },
  placeTitle:   { fontFamily: 'System', fontWeight: '500', fontSize: 20, lineHeight: 24, letterSpacing: -0.2, color: '#202124' },
  section:      { fontFamily: 'System', fontWeight: '500', fontSize: 16, lineHeight: 20, color: '#202124' },
  button:       { fontFamily: 'System', fontWeight: '500', fontSize: 16, color: '#FFFFFF' },
  buttonSmall:  { fontFamily: 'System', fontWeight: '500', fontSize: 14, color: '#4285F4' },
  tab:          { fontFamily: 'System', fontWeight: '500', fontSize: 11, letterSpacing: 0.2, color: '#5F6368' },
  chip:         { fontFamily: 'System', fontWeight: '500', fontSize: 14, color: '#202124' },

  rowTitle:     { fontFamily: 'Roboto-Medium',  fontSize: 16, lineHeight: 21, color: '#202124' },
  body:         { fontFamily: 'Roboto-Regular', fontSize: 14, lineHeight: 20, color: '#202124' },
  address:      { fontFamily: 'Roboto-Regular', fontSize: 14, lineHeight: 19, color: '#5F6368' },
  meta:         { fontFamily: 'Roboto-Regular', fontSize: 13, lineHeight: 17, color: '#5F6368' },
  rating:       { fontFamily: 'Roboto-Medium',  fontSize: 14, color: '#202124' },
  eta:          { fontFamily: 'Roboto-Medium',  fontSize: 16, color: '#202124' },
  distancePill: { fontFamily: 'Roboto-Medium',  fontSize: 13, color: '#202124' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Floating Top Search Bar

```tsx
// components/GMSearchBar.tsx
import { Pressable, StyleSheet, Text, View, Image } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function GMSearchBar({
  avatarUri, onTap, onMic,
}: { avatarUri?: string; onTap: () => void; onMic: () => void }) {
  return (
    <Pressable onPress={onTap} style={styles.wrapper}>
      <View style={styles.avatar}>
        {avatarUri ? (
          <Image source={{ uri: avatarUri }} style={StyleSheet.absoluteFill} />
        ) : (
          <Ionicons name="person" size={16} color="#FFFFFF" />
        )}
      </View>
      <Text style={[typography.button, { color: colors.textSecondary, flex: 1 }]}>Search here</Text>
      <Pressable onPress={onMic} hitSlop={12}>
        <Ionicons name="mic" size={20} color={colors.textSecondary} />
      </Pressable>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  wrapper: {
    marginHorizontal: 16,
    height: 48,
    borderRadius: 24,
    backgroundColor: colors.canvas,
    flexDirection: 'row',
    alignItems: 'center',
    paddingHorizontal: 16,
    gap: 12,
    shadowColor: '#000', shadowOpacity: 0.15, shadowRadius: 8, shadowOffset: { width: 0, height: 2 },
    elevation: 4,
  },
  avatar: {
    width: 28, height: 28, borderRadius: 14, backgroundColor: '#5F6368',
    alignItems: 'center', justifyContent: 'center', overflow: 'hidden',
  },
});
```

### Directions FAB

```tsx
// components/GMDirectionsFAB.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function GMDirectionsFAB({ onPress }: { onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.95, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
        onPress();
      }}
      style={{ position: 'absolute', right: 16, bottom: 24 }}
    >
      <Animated.View
        style={[{
          width: 56, height: 56, borderRadius: 28,
          backgroundColor: colors.blue,
          alignItems: 'center', justifyContent: 'center',
          shadowColor: '#000', shadowOpacity: 0.2, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
        }, style]}
      >
        <Ionicons name="navigate" size={26} color="#FFFFFF" />
      </Animated.View>
    </Pressable>
  );
}
```

### Your Location Dot

```tsx
// components/GMLocationDot.tsx
import { View } from 'react-native';
import Animated, {
  useSharedValue, useAnimatedStyle, withRepeat, withTiming, Easing,
} from 'react-native-reanimated';
import { colors } from '../theme/colors';

export function GMLocationDot({ headingDeg }: { headingDeg?: number }) {
  const pulse = useSharedValue(1);
  // Start pulse loop once
  if (pulse.value === 1) {
    pulse.value = withRepeat(withTiming(1.15, { duration: 900, easing: Easing.inOut(Easing.ease) }), -1, true);
  }
  const dotStyle = useAnimatedStyle(() => ({ transform: [{ scale: pulse.value }] }));

  return (
    <View style={{ alignItems: 'center', justifyContent: 'center' }}>
      {/* Accuracy circle */}
      <View style={{
        position: 'absolute', width: 140, height: 140, borderRadius: 70,
        backgroundColor: colors.blueHalo, borderWidth: 1, borderColor: colors.blueHaloEdge,
      }} />

      {/* Heading cone */}
      {headingDeg != null && (
        <View style={{
          position: 'absolute',
          transform: [{ rotate: `${headingDeg}deg` }, { translateY: -30 }],
        }}>
          <View style={{
            width: 0, height: 0,
            borderLeftWidth: 20, borderRightWidth: 20, borderBottomWidth: 38,
            borderLeftColor: 'transparent', borderRightColor: 'transparent',
            borderBottomColor: 'rgba(66,133,244,0.55)',
            transform: [{ rotate: '180deg' }],
          }} />
        </View>
      )}

      {/* Inner dot + white ring */}
      <Animated.View style={[{
        width: 18, height: 18, borderRadius: 9,
        backgroundColor: '#FFFFFF',
        alignItems: 'center', justifyContent: 'center',
        shadowColor: '#000', shadowOpacity: 0.2, shadowRadius: 2,
      }, dotStyle]}>
        <View style={{ width: 12, height: 12, borderRadius: 6, backgroundColor: colors.blue }} />
      </Animated.View>
    </View>
  );
}
```

### Map Pin (Teardrop)

```tsx
// components/GMMapPin.tsx
import { View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export type PinKind = 'default' | 'saved' | 'homeWork' | 'category';

const fillFor: Record<PinKind, string> = {
  default:  colors.red,
  saved:    colors.green,
  homeWork: colors.blue,
  category: colors.orange,
};

export function GMMapPin({ kind, icon }: { kind: PinKind; icon?: string }) {
  return (
    <View style={{ alignItems: 'center', width: 32, height: 44 }}>
      <View style={{
        width: 32, height: 32, borderRadius: 16, backgroundColor: fillFor[kind],
        alignItems: 'center', justifyContent: 'center',
        shadowColor: '#000', shadowOpacity: 0.25, shadowRadius: 3, shadowOffset: { width: 0, height: 4 },
      }}>
        {kind === 'default' ? (
          <View style={{ width: 10, height: 10, borderRadius: 5, backgroundColor: '#FFFFFF' }} />
        ) : (
          <Ionicons name={(icon ?? 'bookmark') as any} size={16} color="#FFFFFF" />
        )}
      </View>
      <View style={{
        width: 0, height: 0, marginTop: -6,
        borderLeftWidth: 8, borderRightWidth: 8, borderTopWidth: 12,
        borderLeftColor: 'transparent', borderRightColor: 'transparent',
        borderTopColor: fillFor[kind],
      }} />
    </View>
  );
}
```

### Place Sheet (Bottom Drawer Card)

```tsx
// components/GMPlaceCard.tsx
import { View, Text, Image, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function GMPlaceCard({
  title, rating, reviewCount, category, distance, isOpen, photoUri,
}: {
  title: string; rating: number; reviewCount: number;
  category: string; distance: string; isOpen: boolean; photoUri?: string;
}) {
  return (
    <View style={{ padding: 16, backgroundColor: colors.canvas, gap: 12 }}>
      <View style={{ flexDirection: 'row', gap: 12 }}>
        {photoUri ? (
          <Image source={{ uri: photoUri }} style={{ width: 72, height: 72, borderRadius: 12 }} />
        ) : (
          <View style={{ width: 72, height: 72, borderRadius: 12, backgroundColor: colors.surfaceMuted }} />
        )}
        <View style={{ flex: 1, gap: 4 }}>
          <Text style={typography.placeTitle} numberOfLines={2}>{title}</Text>
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
            <Text style={[typography.rating, { fontVariant: ['tabular-nums'] }]}>{rating.toFixed(1)}</Text>
            {[0,1,2,3,4].map(i => (
              <Ionicons key={i}
                name={i < Math.round(rating) ? 'star' : 'star-outline'}
                size={12} color={colors.yellow} />
            ))}
            <Text style={typography.body}> ({reviewCount.toLocaleString()})</Text>
          </View>
          <Text style={typography.meta}>{category} · {distance}</Text>
          {isOpen && <Text style={[typography.meta, { color: colors.green, fontWeight: '500' }]}>Open now</Text>}
        </View>
      </View>
      <ActionRow />
    </View>
  );
}

function ActionRow() {
  return (
    <View style={{ flexDirection: 'row', gap: 12 }}>
      <PillButton icon="navigate" title="Directions" filled />
      <PillButton icon="bookmark-outline" title="Save" />
      <PillButton icon="share-outline" title="Share" />
      <PillButton icon="call" title="Call" />
    </View>
  );
}

function PillButton({ icon, title, filled }: { icon: string; title: string; filled?: boolean }) {
  return (
    <Pressable
      style={({ pressed }) => ({
        height: 36,
        paddingHorizontal: 16,
        borderRadius: 18,
        flexDirection: 'row',
        alignItems: 'center',
        gap: 6,
        backgroundColor: filled ? colors.blue : 'transparent',
        borderWidth: filled ? 0 : 1,
        borderColor: colors.divider,
        opacity: pressed ? 0.85 : 1,
      })}
    >
      <Ionicons name={icon as any} size={16} color={filled ? '#FFFFFF' : colors.blue} />
      <Text style={[typography.buttonSmall, { color: filled ? '#FFFFFF' : colors.blue }]}>{title}</Text>
    </Pressable>
  );
}
```

### Navigation Turn Card

```tsx
// components/GMTurnCard.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function GMTurnCard({
  instruction, distance, nextInstruction,
}: { instruction: string; distance: string; nextInstruction?: string }) {
  return (
    <View style={{
      marginHorizontal: 16,
      backgroundColor: colors.blue,
      borderRadius: 16,
      padding: 16,
      shadowColor: '#000', shadowOpacity: 0.2, shadowRadius: 16, shadowOffset: { width: 0, height: 4 },
    }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 16 }}>
        <Ionicons name="arrow-redo" size={44} color="#FFFFFF" />
        <View style={{ flex: 1 }}>
          <Text style={{ fontSize: 22, fontWeight: '700', color: '#FFFFFF' }} numberOfLines={2}>
            {instruction}
          </Text>
          <Text style={{ fontSize: 14, color: 'rgba(255,255,255,0.7)', fontVariant: ['tabular-nums'] }}>
            in {distance}
          </Text>
        </View>
      </View>
      {nextInstruction && (
        <View style={{
          marginTop: 10, paddingTop: 10,
          borderTopWidth: 1, borderTopColor: 'rgba(255,255,255,0.2)',
          flexDirection: 'row', alignItems: 'center', gap: 10,
        }}>
          <Ionicons name="arrow-undo" size={22} color="rgba(255,255,255,0.8)" />
          <Text style={{ color: 'rgba(255,255,255,0.8)', fontSize: 14 }}>Then {nextInstruction}</Text>
        </View>
      )}
    </View>
  );
}
```

## 4. Route Polyline (react-native-maps)

```tsx
import MapView, { Polyline } from 'react-native-maps';

<MapView style={{ flex: 1 }}>
  {/* Casing (wider, darker) */}
  <Polyline
    coordinates={routeCoords}
    strokeWidth={7}
    strokeColor={colors.blueDark}
    lineCap="round" lineJoin="round"
  />
  {/* Fill */}
  <Polyline
    coordinates={routeCoords}
    strokeWidth={5}
    strokeColor={colors.blue}
    lineCap="round" lineJoin="round"
  />
  {/* Traffic overlay segments — draw separate Polylines per color */}
</MapView>
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.blue,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: {
          backgroundColor: colors.canvas,
          borderTopColor: colors.divider,
          borderTopWidth: 0.5,
        },
        tabBarLabelStyle: { fontFamily: 'System', fontWeight: '500', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"      options={{ title: 'Explore',    tabBarIcon: ({ color }) => <Ionicons name="compass"  size={24} color={color} /> }} />
      <Tabs.Screen name="go"         options={{ title: 'Go',         tabBarIcon: ({ color }) => <Ionicons name="navigate" size={24} color={color} /> }} />
      <Tabs.Screen name="saved"      options={{ title: 'Saved',      tabBarIcon: ({ color }) => <Ionicons name="bookmark" size={24} color={color} /> }} />
      <Tabs.Screen name="contribute" options={{ title: 'Contribute', tabBarIcon: ({ color }) => <Ionicons name="add-circle" size={24} color={color} /> }} />
      <Tabs.Screen name="updates"    options={{ title: 'Updates',    tabBarIcon: ({ color }) => <Ionicons name="newspaper" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Pin drop — spring with overshoot
const y = useSharedValue(-40);
const drop = () => {
  y.value = withSpring(0, { damping: 10, stiffness: 180 });
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
};

// Bottom sheet detents (expo-router + @gorhom/bottom-sheet)
// <BottomSheet snapPoints={[140, 380, '85%']} index={1} handleStyle={...}>

// Location dot pulse — already implemented with withRepeat above.

// Route stroke-on animation — if using SVG polyline, animate strokeDashoffset 0 → length over 600ms ease-out.
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map Maps-specific icons:

| Purpose | Ionicons |
|---------|----------|
| Directions FAB | `navigate` |
| Search mic | `mic` |
| Bookmark / Save | `bookmark-outline` / `bookmark` |
| Share | `share-outline` |
| Call | `call` |
| Recenter | `locate` |
| Layers | `layers` |
| Compass | `compass` |
| Turn right | `arrow-redo` |
| Turn left | `arrow-undo` |
| Straight | `arrow-up` |
| Rating star | `star` / `star-outline` |
| Home (tab) | `compass` |
| Saved (tab) | `bookmark` |
| Updates (tab) | `newspaper` |

## 8. Platform Notes

- **Maps backend**: Use `react-native-maps` with `provider="google"` for a consistent look across platforms; iOS requires a dev-client build. `expo-maps` is simpler but Apple Maps-backed on iOS — Google tile look is easier with `react-native-maps` + Google tile styling JSON.
- **Google Maps styling JSON**: Apply `customMapStyle` to match the light/dark palettes above — Google publishes styling JSONs on mapstyle.withgoogle.com. Start from the "Standard" or "Silver" preset and override `road.highway` with `#FDF6E3`, water with `#AADAFF`, parks with `#C8E6C9`.
- **Safe area**: Wrap the screen root in `SafeAreaView` from `react-native-safe-area-context`; place the floating search bar at `insets.top + 8`.
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` in light mode, `style="light"` in dark.
- **Dynamic Type**: Default React Native font-scaling honors user settings. Set `allowFontScaling={false}` on the nav turn instruction (fixed 36pt), speed chip, and distance labels on the map to preserve layout.
- **Accessibility**:
  - Search bar: `accessibilityRole="search"`, `accessibilityLabel="Search Google Maps"`
  - Location dot: `accessibilityLabel="Your current location"`, announce accuracy on focus
  - Map pins: `accessibilityLabel` = place name + category + distance
- **Haptics**: `Haptics.impactAsync(Medium)` on pin drop and "Start", `Haptics.selectionAsync()` on chip tap, `Haptics.notificationAsync(Success)` on "Arrived".
- **Dark mode**: Detect with `useColorScheme()` and swap both token set and the map styling JSON.
