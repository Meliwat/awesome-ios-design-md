# Uber (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Uber's visual language into paste-ready Expo / React Native code: a design-token module, themed components, Reanimated snippets, and map integration.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, `expo-haptics`, and `react-native-maps` or `expo-maps`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & brand
  black:        '#000000',
  white:        '#FFFFFF',
  canvasDark:   '#0C0C0C',

  // Gray ramp
  gray50:  '#F6F6F6',
  gray100: '#EEEEEE',
  gray200: '#E5E5E5',
  gray400: '#AFAFAF',
  gray600: '#757575',
  gray700: '#545454',
  gray900: '#2F2F2F',
  gray950: '#1A1A1A',

  // Functional
  green:  '#05A357',
  red:    '#D72113',
  blue:   '#0A47FF',
  amber:  '#FFCB00',

  // Dark-mode functional (slightly brightened for contrast on #0C0C0C)
  greenDark: '#22C77A',
  redDark:   '#FF4C3F',
  blueDark:  '#3D6DFF',
} as const;

export const light = {
  canvas: colors.white,
  surface: colors.white,
  surfaceAlt: colors.gray50,
  textPrimary: colors.black,
  textSecondary: colors.gray600,
  textTertiary: colors.gray400,
  divider: colors.gray200,
} as const;

export const dark = {
  canvas: colors.canvasDark,
  surface: colors.gray950,
  surfaceAlt: colors.gray900,
  textPrimary: colors.white,
  textSecondary: colors.gray400,
  textTertiary: colors.gray600,
  divider: '#3A3A3A',
} as const;
```

## 2. Typography

Load Uber Move via `expo-font`. Fallback to `System` (SF Pro on iOS) with `variant: 'tabular-nums'` approximating the Mono cut.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'UberMove-Bold':          require('../assets/fonts/UberMove-Bold.ttf'),
    'UberMove-Medium':        require('../assets/fonts/UberMove-Medium.ttf'),
    'UberMoveText-Regular':   require('../assets/fonts/UberMoveText-Regular.ttf'),
    'UberMoveText-Medium':    require('../assets/fonts/UberMoveText-Medium.ttf'),
    'UberMoveText-Bold':      require('../assets/fonts/UberMoveText-Bold.ttf'),
    'UberMoveMono-Regular':   require('../assets/fonts/UberMoveMono-Regular.ttf'),
    'UberMoveMono-Medium':    require('../assets/fonts/UberMoveMono-Medium.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  hero:         { fontFamily: 'UberMove-Bold',        fontSize: 36, lineHeight: 40, letterSpacing: -0.6 },
  sheetTitle:   { fontFamily: 'UberMove-Bold',        fontSize: 24, lineHeight: 28, letterSpacing: -0.4 },
  navTitle:     { fontFamily: 'UberMove-Medium',      fontSize: 18, lineHeight: 22 },

  whereTo:      { fontFamily: 'UberMoveText-Medium',  fontSize: 18, lineHeight: 22, letterSpacing: -0.1 },
  rowTitle:     { fontFamily: 'UberMoveText-Medium',  fontSize: 16, lineHeight: 20, letterSpacing: -0.1 },
  body:         { fontFamily: 'UberMoveText-Regular', fontSize: 15, lineHeight: 21 },
  meta:         { fontFamily: 'UberMoveText-Regular', fontSize: 14, lineHeight: 18 },
  labelUpper:   { fontFamily: 'UberMoveText-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.6, textTransform: 'uppercase' as const },
  button:       { fontFamily: 'UberMoveText-Medium',  fontSize: 17, lineHeight: 20 },
  buttonSmall:  { fontFamily: 'UberMoveText-Medium',  fontSize: 15, lineHeight: 18 },
  tab:          { fontFamily: 'UberMoveText-Medium',  fontSize: 11, lineHeight: 13, letterSpacing: 0.2 },
  caption:      { fontFamily: 'UberMoveText-Regular', fontSize: 12, lineHeight: 16 },

  price:        { fontFamily: 'UberMoveMono-Medium',  fontSize: 16, lineHeight: 20 },
  eta:          { fontFamily: 'UberMoveMono-Medium',  fontSize: 14, lineHeight: 18 },
  etaBadge:     { fontFamily: 'UberMoveMono-Medium',  fontSize: 18, lineHeight: 22 },
  address:      { fontFamily: 'UberMoveMono-Regular', fontSize: 13, lineHeight: 17 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary Black CTA

```tsx
// components/PrimaryButton.tsx
import { ActivityIndicator, Pressable, Text, ViewStyle } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({
  title, isLoading = false, onPress, style,
}: { title: string; isLoading?: boolean; onPress: () => void; style?: ViewStyle }) {
  const pressed = useSharedValue(0);
  const animStyle = useAnimatedStyle(() => ({
    transform: [{ scale: 1 - pressed.value * 0.02 }],
    backgroundColor: pressed.value > 0 ? colors.gray900 : colors.black,
  }));

  return (
    <Pressable
      onPressIn={() => (pressed.value = withSpring(1, { damping: 18 }))}
      onPressOut={() => (pressed.value = withSpring(0, { damping: 18 }))}
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
        onPress();
      }}
      disabled={isLoading}
    >
      <Animated.View style={[{ height: 56, borderRadius: 8, alignItems: 'center', justifyContent: 'center' }, animStyle, style]}>
        {isLoading
          ? <ActivityIndicator color="#fff" />
          : <Text style={[typography.button, { color: colors.white }]}>{title}</Text>}
      </Animated.View>
    </Pressable>
  );
}
```

### "Where To?" Input

```tsx
// components/WhereToInput.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WhereToInput({ onPress, onSchedule }: { onPress: () => void; onSchedule: () => void }) {
  return (
    <Pressable onPress={onPress}>
      <View style={{ flexDirection: 'row', alignItems: 'center', height: 56, backgroundColor: colors.gray50, borderRadius: 8, paddingHorizontal: 16 }}>
        <View style={{ alignItems: 'center', gap: 3, marginRight: 14 }}>
          <View style={{ width: 8, height: 8, backgroundColor: colors.black }} />
          <View style={{ width: 1, height: 10, backgroundColor: colors.gray400 }} />
          <View style={{ width: 8, height: 8, backgroundColor: colors.black }} />
        </View>
        <Text style={[typography.whereTo, { color: colors.black, flex: 1 }]}>Where to?</Text>
        <Pressable
          onPress={onSchedule}
          style={{ flexDirection: 'row', alignItems: 'center', gap: 6, backgroundColor: colors.white, borderColor: colors.gray200, borderWidth: 1, borderRadius: 500, paddingHorizontal: 12, paddingVertical: 8 }}
        >
          <Ionicons name="time-outline" size={14} color={colors.black} />
          <Text style={[typography.buttonSmall, { color: colors.black }]}>Later</Text>
        </Pressable>
      </View>
    </Pressable>
  );
}
```

### Ride Option Card

```tsx
// components/RideOptionCard.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RideOptionCard({
  name, eta, capacity, price, selected, onPress, carSource,
}: {
  name: string; eta: string; capacity: number; price: string;
  selected: boolean; onPress: () => void; carSource: any;
}) {
  return (
    <Pressable
      onPress={() => { Haptics.selectionAsync(); onPress(); }}
      style={{
        height: 72,
        flexDirection: 'row',
        alignItems: 'center',
        gap: 12,
        paddingHorizontal: 16,
        backgroundColor: selected ? colors.gray50 : colors.white,
        borderRadius: 8,
        borderWidth: selected ? 2 : 1,
        borderColor: selected ? colors.black : colors.gray200,
      }}
    >
      <Image source={carSource} style={{ width: 56, height: 56, resizeMode: 'contain' }} />
      <View style={{ flex: 1, gap: 4 }}>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
          <Text style={[typography.rowTitle, { color: colors.black }]}>{name}</Text>
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 2 }}>
            <Ionicons name="person" size={10} color={colors.gray600} />
            <Text style={[typography.caption, { color: colors.gray600 }]}>{capacity}</Text>
          </View>
        </View>
        <Text style={[typography.address, { color: colors.gray600 }]}>{eta} away</Text>
      </View>
      <Text style={[typography.price, { color: colors.black }]}>{price}</Text>
    </Pressable>
  );
}
```

### Active Trip Card

```tsx
// components/ActiveTripCard.tsx
import { Image, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ActiveTripCard({
  driverName, rating, carModel, plate, photoUri,
}: { driverName: string; rating: number; carModel: string; plate: string; photoUri: string }) {
  return (
    <View style={{ padding: 16, gap: 16 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12 }}>
        <Image source={{ uri: photoUri }} style={{ width: 48, height: 48, borderRadius: 24 }} />
        <View style={{ flex: 1 }}>
          <Text style={[typography.rowTitle, { color: colors.black }]}>{driverName}</Text>
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
            <Ionicons name="star" size={11} color={colors.gray600} />
            <Text style={[typography.eta, { color: colors.gray600 }]}>{rating.toFixed(2)}</Text>
          </View>
        </View>
        <View style={{ backgroundColor: colors.gray50, borderRadius: 500, paddingHorizontal: 10, paddingVertical: 4 }}>
          <Text style={[typography.eta, { color: colors.black }]}>{plate}</Text>
        </View>
      </View>
      <Text style={[typography.meta, { color: colors.gray700 }]}>{carModel}</Text>
      <View style={{ height: 1, backgroundColor: colors.gray200 }} />
      <View style={{ flexDirection: 'row', gap: 24 }}>
        {[
          { icon: 'chatbubble', label: 'Message' },
          { icon: 'call', label: 'Call' },
          { icon: 'share-outline', label: 'Share' },
        ].map(({ icon, label }) => (
          <View key={label} style={{ alignItems: 'center', gap: 6 }}>
            <View style={{ width: 44, height: 44, borderRadius: 22, backgroundColor: colors.gray50, alignItems: 'center', justifyContent: 'center' }}>
              <Ionicons name={icon as any} size={20} color={colors.black} />
            </View>
            <Text style={[typography.caption, { color: colors.gray600 }]}>{label}</Text>
          </View>
        ))}
      </View>
    </View>
  );
}
```

### Bottom Sheet (with detents)

Use `@gorhom/bottom-sheet` — the de facto Expo solution. Snap points match Uber's collapsed / medium / expanded detents.

```tsx
// components/UberBottomSheet.tsx
import { useMemo, useRef, type PropsWithChildren } from 'react';
import { View } from 'react-native';
import BottomSheet, { BottomSheetView } from '@gorhom/bottom-sheet';
import { colors } from '../theme/colors';

export function UberBottomSheet({ children }: PropsWithChildren) {
  const ref = useRef<BottomSheet>(null);
  const snapPoints = useMemo(() => [140, 360, '90%'], []);

  return (
    <BottomSheet
      ref={ref}
      index={1}
      snapPoints={snapPoints}
      handleIndicatorStyle={{ backgroundColor: colors.gray200, width: 36, height: 4 }}
      backgroundStyle={{
        backgroundColor: colors.white,
        borderTopLeftRadius: 16,
        borderTopRightRadius: 16,
      }}
    >
      <BottomSheetView style={{ paddingHorizontal: 16, paddingTop: 6, paddingBottom: 24 }}>
        {children}
      </BottomSheetView>
    </BottomSheet>
  );
}
```

## 4. Map Integration

Use `react-native-maps` (Expo dev client) for full control, or `expo-maps` (Expo SDK 51+) for Apple-native MapKit on iOS.

```tsx
// components/RideMap.tsx
import MapView, { Marker, Polyline, PROVIDER_DEFAULT } from 'react-native-maps';
import { colors } from '../theme/colors';

export function RideMap({ region, pickup, destination, route }: any) {
  return (
    <MapView
      provider={PROVIDER_DEFAULT}
      style={{ flex: 1 }}
      initialRegion={region}
      showsUserLocation
      showsPointsOfInterest={false}
      showsBuildings={false}
      customMapStyle={uberMapStyle}
    >
      <Marker coordinate={pickup}>
        <View style={{ width: 14, height: 14, borderRadius: 7, backgroundColor: colors.black, borderWidth: 2, borderColor: colors.white }} />
      </Marker>
      <Marker coordinate={destination}>
        <View style={{ width: 32, height: 40, backgroundColor: colors.black, borderRadius: 4 }} />
      </Marker>
      <Polyline
        coordinates={route}
        strokeColor={colors.black}
        strokeWidth={5}
        lineCap="round"
        lineJoin="round"
      />
    </MapView>
  );
}

// Uber-flavored minimalist map style — strips POIs, flattens colors
const uberMapStyle = [
  { featureType: 'poi', stylers: [{ visibility: 'off' }] },
  { featureType: 'transit', stylers: [{ visibility: 'off' }] },
  { featureType: 'water',      elementType: 'geometry', stylers: [{ color: '#D9E5F2' }] },
  { featureType: 'landscape',  elementType: 'geometry', stylers: [{ color: '#F2F2F2' }] },
  { featureType: 'road',       elementType: 'geometry', stylers: [{ color: '#FFFFFF' }] },
  { featureType: 'road',       elementType: 'labels',    stylers: [{ visibility: 'off' }] },
];
```

### Floating Map Control

```tsx
// components/FloatingMapButton.tsx
import { Pressable, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function FloatingMapButton({ icon, onPress }: { icon: keyof typeof Ionicons.glyphMap; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        width: 44, height: 44, borderRadius: 22,
        backgroundColor: colors.white,
        alignItems: 'center', justifyContent: 'center',
        shadowColor: '#000', shadowOpacity: 0.12, shadowRadius: 8, shadowOffset: { width: 0, height: 2 },
        elevation: 3,
      }}
    >
      <Ionicons name={icon} size={20} color={colors.black} />
    </Pressable>
  );
}
```

### Driver Arrival Beacon

```tsx
// components/DriverBeacon.tsx
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, withDelay, Easing } from 'react-native-reanimated';
import { useEffect } from 'react';
import { View } from 'react-native';
import { colors } from '../theme/colors';

export function DriverBeacon() {
  const rings = [0, 0.6, 1.2];
  return (
    <View style={{ width: 48, height: 48, alignItems: 'center', justifyContent: 'center' }}>
      {rings.map((delay, i) => <Ring key={i} delay={delay} />)}
      <View style={{ width: 24, height: 24, borderRadius: 12, backgroundColor: colors.white, borderWidth: 1, borderColor: colors.black }} />
    </View>
  );
}

function Ring({ delay }: { delay: number }) {
  const scale = useSharedValue(1);
  const opacity = useSharedValue(0.3);
  useEffect(() => {
    scale.value = withDelay(delay * 1000,
      withRepeat(withTiming(2.4, { duration: 1800, easing: Easing.inOut(Easing.ease) }), -1, false));
    opacity.value = withDelay(delay * 1000,
      withRepeat(withTiming(0, { duration: 1800, easing: Easing.inOut(Easing.ease) }), -1, false));
  }, []);
  const style = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
    opacity: opacity.value,
  }));
  return (
    <Animated.View style={[{ position: 'absolute', width: 48, height: 48, borderRadius: 24, backgroundColor: colors.green }, style]} />
  );
}
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.black,
        tabBarInactiveTintColor: colors.gray600,
        tabBarStyle: {
          backgroundColor: colors.white,
          borderTopColor: colors.gray200,
          borderTopWidth: 0.5,
        },
        tabBarLabelStyle: { fontFamily: 'UberMoveText-Medium', fontSize: 11, letterSpacing: 0.2 },
        headerShown: false,
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home"        size={24} color={color} /> }} />
      <Tabs.Screen name="services" options={{ title: 'Services', tabBarIcon: ({ color }) => <Ionicons name="grid"        size={24} color={color} /> }} />
      <Tabs.Screen name="activity" options={{ title: 'Activity', tabBarIcon: ({ color }) => <Ionicons name="time"        size={24} color={color} /> }} />
      <Tabs.Screen name="account"  options={{ title: 'Account',  tabBarIcon: ({ color }) => <Ionicons name="person"      size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion & Haptics

```tsx
import * as Haptics from 'expo-haptics';

// Confirm CTA
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Driver arrived
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Cancel ride confirmation
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Warning);

// Ride option selection
Haptics.selectionAsync();

// Polyline draw animation — animate strokeDashoffset via Reanimated
// (react-native-maps Polyline doesn't support dashes; use react-native-svg
// on a transparent overlay MapView for animated line draw)
```

## 7. Icon Library

`@expo/vector-icons` ships Ionicons, which maps cleanly to Uber's geometric icon set:

| Purpose | Ionicons |
|---------|----------|
| Home (tab) | `home` / `home-outline` |
| Services | `grid` / `grid-outline` |
| Activity | `time` / `time-outline` |
| Account | `person` / `person-outline` |
| Search | `search` |
| Recenter | `locate` |
| Back | `chevron-back` |
| Close | `close` |
| Clock (Later) | `time-outline` |
| Saved home | `home` |
| Saved work | `briefcase` |
| Saved star | `star` |
| Message | `chatbubble` / `chatbubble-outline` |
| Call | `call` |
| Share | `share-outline` |
| Car | `car` / `car-sport` |
| Payment | `card` |
| Capacity person | `person` (10pt) |

## 8. Platform Notes

- **iOS-first feel**: Use `expo-blur` sparingly — Uber's chrome is flat. Use blur only on the optional top-nav material when scrolling over a white surface.
- **Status bar**: `<StatusBar style="dark" />` in light mode, `"light"` in dark mode. The map extends under the status bar on every screen.
- **Safe area**: wrap screens with `SafeAreaView` from `react-native-safe-area-context`. The bottom sheet's collapsed detent (140pt) must sit above the home indicator.
- **Dynamic Type**: React Native respects OS font scaling. Set `allowFontScaling={false}` on the 56pt CTA (label truncates), the map-pin labels, and the license plate pill.
- **Accessibility**: mark the primary CTA with `accessibilityRole="button"` and `accessibilityLabel` including the destination ("Confirm UberX to 212 Market Street, estimated $14.82"). Group the driver card into a single element. Announce arrival with `AccessibilityInfo.announceForAccessibility("Your driver has arrived")`.
- **Reduced motion**: honor `AccessibilityInfo.isReduceMotionEnabled()` — skip beacon pulse and polyline draw animations, snap to final states instead.
- **Dark mode**: subscribe to `useColorScheme()` and swap the `light` / `dark` token sets. Map also swaps to the dark `customMapStyle` variant.
