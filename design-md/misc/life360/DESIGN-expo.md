# Life360 (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Life360's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `react-native-maps` (or `expo-maps`), `@gorhom/bottom-sheet`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — default)
  canvas:   '#161325', // violet-tinted night
  surface1: '#1F1B33',
  surface2: '#2A2542',
  sheet:    '#211D36',
  divider:  '#332E4D',
  border:   '#3D3759',

  // Map palette (dark-styled — keeps member pins salient)
  mapBase:  '#1A2138',
  mapRoad:  '#2C3654',
  mapPark:  '#1B3A2E',
  mapWater: '#16314A',
  mapLabel: '#7E7A99',

  // Surfaces (light)
  canvasLight:  '#FFFFFF',
  surfaceLight: '#F5F3FA',
  mapBaseLight: '#EDEFF5',
  dividerLight: '#E6E3F0',

  // Text
  textPrimary:   '#ECEAF5',
  textSecondary: '#A6A0C2',
  textTertiary:  '#6F6990',
  textOnLight:   '#1C1830',

  // Brand (Life360 Purple — NEVER a member color)
  purple:        '#582C83',
  purpleLight:   '#7E57C2',
  purpleBright:  '#8B5CF6',
  purplePressed: '#4A2470',

  // Safety semantic (reserved — rationed)
  safe:    '#34C759',
  sos:     '#FF6B6B',
  warning: '#FFB020',
} as const;

export type Life360Color = keyof typeof colors;

// Member identity palette — assign in Circle join order; fixed per person everywhere.
export const memberPalette = [
  '#F2A33C', // amber
  '#2DD4BF', // teal
  '#F472B6', // pink
  '#60A5FA', // blue
  '#FB7185', // coral
  '#A3E635', // lime
  '#38BDF8', // sky
] as const;

export const memberColor = (slot: number) => memberPalette[slot % memberPalette.length];
export const batteryColor = (pct: number) => (pct <= 20 ? colors.sos : colors.safe);
```

## 2. Typography

Life360's brand face is friendly rounded-humanist; **Plus Jakarta Sans** is the closest free analog. Load via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'PJS-Regular':   require('../assets/fonts/PlusJakartaSans-Regular.ttf'),
    'PJS-Medium':    require('../assets/fonts/PlusJakartaSans-Medium.ttf'),
    'PJS-SemiBold':  require('../assets/fonts/PlusJakartaSans-SemiBold.ttf'),
    'PJS-Bold':      require('../assets/fonts/PlusJakartaSans-Bold.ttf'),
    'PJS-ExtraBold': require('../assets/fonts/PlusJakartaSans-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ contentStyle: { backgroundColor: '#161325' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#ECEAF5' } satisfies TextStyle;

export const typography = {
  display:    { ...primary, fontFamily: 'PJS-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.5 },
  title:      { ...primary, fontFamily: 'PJS-Bold',      fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:    { ...primary, fontFamily: 'PJS-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  memberName: { ...primary, fontFamily: 'PJS-Bold',      fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  body:       { ...primary, fontFamily: 'PJS-Regular',   fontSize: 16, lineHeight: 24 },
  rowTitle:   { ...primary, fontFamily: 'PJS-SemiBold',  fontSize: 15, lineHeight: 20 },
  status:     { color: '#A6A0C2', fontFamily: 'PJS-SemiBold', fontSize: 13, lineHeight: 17 },
  meta:       { color: '#A6A0C2', fontFamily: 'PJS-Regular',  fontSize: 14, lineHeight: 19 },
  caption:    { color: '#A6A0C2', fontFamily: 'PJS-SemiBold',  fontSize: 12, lineHeight: 16, letterSpacing: 0.4 },
  pinInitial: { color: '#FFFFFF', fontFamily: 'PJS-Bold',     fontSize: 16, lineHeight: 16 },
  tab:        { color: '#6F6990', fontFamily: 'PJS-SemiBold',  fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  button:     { color: '#FFFFFF', fontFamily: 'PJS-Bold',     fontSize: 15, lineHeight: 15 },
  sos:        { color: '#FFFFFF', fontFamily: 'PJS-ExtraBold', fontSize: 15, lineHeight: 15, letterSpacing: 0.5 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Member Pin (the atomic Life360 element)

```tsx
// components/MemberPin.tsx
import { View, Text, Image } from 'react-native';
import { typography } from '../theme/typography';

export function MemberPin({
  initial, color, photoUri, selected = false,
}: { initial: string; color: string; photoUri?: string; selected?: boolean }) {
  return (
    <View style={{ alignItems: 'center' }}>
      <View style={{
        width: 46, height: 46, borderRadius: 23,
        borderWidth: selected ? 4 : 3, borderColor: '#FFFFFF',
        backgroundColor: color, alignItems: 'center', justifyContent: 'center',
        shadowColor: '#000', shadowOpacity: 0.5, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
        elevation: 8,
        ...(selected ? { /* glow handled by an outer halo view if desired */ } : {}),
      }}>
        {photoUri
          ? <Image source={{ uri: photoUri }} style={{ width: 40, height: 40, borderRadius: 20 }} />
          : <Text style={typography.pinInitial}>{initial}</Text>}
      </View>
      {/* downward pointer */}
      <View style={{
        width: 0, height: 0, marginTop: -1,
        borderLeftWidth: 6, borderRightWidth: 6, borderTopWidth: 8,
        borderLeftColor: 'transparent', borderRightColor: 'transparent', borderTopColor: '#FFFFFF',
      }} />
    </View>
  );
}
```

### Member Sheet Row

```tsx
// components/MemberRow.tsx
import { View, Text, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, batteryColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function MemberRow({
  initial, color, name, placeText, placeIcon, battery, onPress,
}: {
  initial: string; color: string; name: string;
  placeText: string; placeIcon: keyof typeof Ionicons.glyphMap; battery: number; onPress: () => void;
}) {
  const bc = batteryColor(battery);
  return (
    <Pressable onPress={onPress}
      style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingVertical: 10, paddingHorizontal: 18, minHeight: 62 }}>
      <View style={{ width: 42, height: 42, borderRadius: 21, backgroundColor: color, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={typography.rowTitle}>{initial}</Text>
      </View>
      <View style={{ flex: 1, minWidth: 0 }}>
        <Text style={typography.memberName}>{name}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5, marginTop: 2 }}>
          <Ionicons name={placeIcon} size={11} color={colors.textSecondary} />
          <Text style={typography.status}>{placeText}</Text>
        </View>
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5 }}>
        <BatteryGlyph level={battery} color={bc} />
        <Text style={[typography.status, { color: bc }]}>{battery}%</Text>
      </View>
    </Pressable>
  );
}

function BatteryGlyph({ level, color }: { level: number; color: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center' }}>
      <View style={{ width: 22, height: 11, borderWidth: 1.5, borderColor: color, borderRadius: 3, padding: 1.5, justifyContent: 'center' }}>
        <View style={{ height: '100%', width: `${level}%`, backgroundColor: color, borderRadius: 1 }} />
      </View>
      <View style={{ width: 2, height: 4, backgroundColor: color, borderRadius: 1, marginLeft: 1 }} />
    </View>
  );
}
```

### Circle Selector Pill

```tsx
// components/CircleSelector.tsx
import { View, Text, Pressable } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CircleSelector({
  circleName, hasAlerts, onSwitch, onBell,
}: { circleName: string; hasAlerts: boolean; onSwitch: () => void; onBell: () => void }) {
  return (
    <BlurView intensity={28} tint="dark" style={{
      flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
      paddingHorizontal: 16, paddingVertical: 10, borderRadius: 999, overflow: 'hidden',
      borderWidth: 1, borderColor: colors.border, backgroundColor: 'rgba(31,27,51,0.92)',
      shadowColor: '#000', shadowOpacity: 0.45, shadowRadius: 20, shadowOffset: { width: 0, height: 6 },
    }}>
      <Pressable onPress={onSwitch} style={{ flexDirection: 'row', alignItems: 'center', gap: 7 }}>
        <Text style={[typography.rowTitle, { fontFamily: 'PJS-Bold' }]}>{circleName}</Text>
        <Ionicons name="chevron-down" size={13} color={colors.textSecondary} />
      </Pressable>
      <Pressable onPress={onBell} style={{ width: 30, height: 30, borderRadius: 15, backgroundColor: colors.surface2, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="notifications" size={15} color={colors.textPrimary} />
        {hasAlerts ? <View style={{ position: 'absolute', top: 4, right: 4, width: 8, height: 8, borderRadius: 4, backgroundColor: colors.sos }} /> : null}
      </Pressable>
    </BlurView>
  );
}
```

### Primary & SOS Buttons

```tsx
// components/Buttons.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withSpring, runOnJS } from 'react-native-reanimated';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.purplePressed : colors.purpleLight,
        borderRadius: 28, paddingVertical: 14, paddingHorizontal: 26,
        transform: [{ scale: pressed ? 0.98 : 1 }], alignSelf: 'flex-start',
      })}>
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function SOSButton({ onTrigger }: { onTrigger: () => void }) {
  const progress = useSharedValue(0);
  const fillStyle = useAnimatedStyle(() => ({ transform: [{ scaleX: progress.value }] }));

  const hold = Gesture.LongPress().minDuration(1500)
    .onBegin(() => { progress.value = withTiming(1, { duration: 1500 }); })
    .onStart(() => { runOnJS(Haptics.notificationAsync)(Haptics.NotificationFeedbackType.Warning); runOnJS(onTrigger)(); })
    .onFinalize((_e, success) => { if (!success) progress.value = withSpring(0); });

  return (
    <GestureDetector gesture={hold}>
      <View style={{ borderRadius: 28, overflow: 'hidden', alignSelf: 'flex-start' }}>
        <View style={{ backgroundColor: 'rgba(255,107,107,0.55)' }}>
          <Animated.View style={[{ ...StyleSheetAbsolute, backgroundColor: colors.sos }, fillStyle, { transformOrigin: 'left' }]} />
          <Text style={[typography.sos, { paddingVertical: 14, paddingHorizontal: 26 }]}>SOS</Text>
        </View>
      </View>
    </GestureDetector>
  );
}
const StyleSheetAbsolute = { position: 'absolute' as const, top: 0, bottom: 0, left: 0, right: 0 };
```

## 4. Bottom Tab Bar + Member Sheet

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.purpleLight,   // brand
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.sheet, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'PJS-SemiBold', fontSize: 10, letterSpacing: 0.1 },
        headerShown: false,
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Map',     tabBarIcon: ({ color }) => <Ionicons name="location"     size={21} color={color} /> }} />
      <Tabs.Screen name="places"  options={{ title: 'Places',  tabBarIcon: ({ color }) => <Ionicons name="home"         size={21} color={color} /> }} />
      <Tabs.Screen name="driving" options={{ title: 'Driving', tabBarIcon: ({ color }) => <Ionicons name="car-sport"    size={21} color={color} /> }} />
      <Tabs.Screen name="safety"  options={{ title: 'Safety',  tabBarIcon: ({ color }) => <Ionicons name="shield"       size={21} color={color} /> }} />
    </Tabs>
  );
}
```

Use `@gorhom/bottom-sheet` for the draggable member sheet — `snapPoints={[180, '50%', '90%']}`, `backgroundStyle={{ backgroundColor: colors.sheet, borderRadius: 22 }}`, `handleIndicatorStyle={{ backgroundColor: colors.border, width: 38 }}`. Style the map dark via `react-native-maps` `customMapStyle` (or `expo-maps` dark config) using `mapBase`/`mapRoad`/`mapPark`/`mapWater`.

## 5. Motion

```tsx
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withSequence, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Pin movement along a path (never teleport) via animated coordinate + pulse
const scale = useSharedValue(1);
function onPinUpdate() {
  scale.value = withSequence(withTiming(1.08, { duration: 250 }), withTiming(1, { duration: 250 }));
}
// For the coordinate, animate the marker's lat/lng with withTiming over ~600ms (Reanimated + AnimatedRegion)

// Map auto-frame on open/recenter — mapRef.fitToCoordinates(allMembers, { animated: true }) (~400ms)

// Pin tap → focus: mapRef.animateCamera({ center }, { duration: 350 }) + grow ring + member glow

// Geofence entry ripple
const ripple = useSharedValue(0);
ripple.value = withSequence(withTiming(1, { duration: 350 }), withTiming(0, { duration: 0 }));
const rippleStyle = useAnimatedStyle(() => ({ transform: [{ scale: 1 + ripple.value * 0.06 }], opacity: 1 - ripple.value }));

// Breadcrumb draw — animate a Polyline coordinate slice head→tail over ~500ms

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);                          // sheet detent snap
Haptics.selectionAsync();                                                        // pin tap
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Warning);             // new Alert-red notification
// SOS hold uses an escalating sequence of impactAsync(Heavy) ticks
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The member pin and battery glyph are custom views, not icon-font glyphs.

| Purpose | Ionicons |
|---------|----------|
| Map (tab) | `location` |
| Places (tab) | `home` |
| Driving (tab) | `car-sport` |
| Safety (tab) | `shield` |
| Member at place | `location-outline` |
| Member driving | `time` |
| Circle chevron | `chevron-down` |
| Notification bell | `notifications` |
| Add place | `add` |
| Recenter map | `navigate` |
| Place — Home | `home` |
| Place — School | `school` |
| Place — Work | `briefcase` |
| Driving event | `warning` |
| Crash detected | `car` |
| Check in | `checkmark-circle` |
| Invite | `person-add` |
| Back | `chevron-back` |

## 7. Platform Notes

- **Font choice**: Plus Jakarta Sans is SIL OFL — free to bundle. Single brand face; no user switching. Falls back gracefully to the system rounded face if absent
- **Map styling**: pass a dark `customMapStyle` (Google) / dark config (`expo-maps`) using `mapBase`/`mapRoad`/`mapPark`/`mapWater` so colored member pins are the most salient objects; switch to a day style only in light mode
- **Status bar**: `<StatusBar style="light" />` over the dark map; the map is full-bleed behind it
- **Safe area**: only chrome respects insets — the Circle pill sits below the safe area / Dynamic Island, the member sheet and tab bar respect the home indicator; the map extends edge-to-edge under both
- **Member identity color is data, not theme**: store each member's slot/color on the member model and reuse it for pin ring, avatar, breadcrumb `Polyline`, and driving-report trail — never recompute per screen
- **Dynamic Type**: set `allowFontScaling={false}` on pin initials, tab labels, and place-tag text (map-layout-sensitive); allow scaling on names/body and let member rows grow
- **Battery is required**: always render the battery glyph + percentage on member rows — it's a safety signal, not optional chrome
- **SOS accessibility**: the press-and-hold must have an alternative — expose an "Emergency" action in the Safety tab and an `accessibilityActions` entry on the button for users who can't perform a long-press
- **Dark mode**: the violet night + dark map is the default. If supporting light, swap canvas/map to the light set via `useColorScheme()`, but keep member colors, brand purple, Safe green, and SOS red identical (they carry meaning)
- **Reduce motion**: respect `AccessibilityInfo.isReduceMotionEnabled` — snap pins instead of animating along the path, skip the geofence ripple and breadcrumb draw; never disable the SOS haptic ramp
