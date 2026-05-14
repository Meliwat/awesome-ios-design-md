# Apple Maps (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Apple Maps' visual language into paste-ready Expo / React Native code: a design-token module, themed components for the sliding search card, place card, current-location puck with pulse, and map controls.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `expo-blur`, `@gorhom/bottom-sheet` for the sliding card, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Map cartography
  mapCream:        '#F6F1E6',
  mapWater:        '#B9D9EB',
  mapParkGreen:    '#D6E5C9',
  mapRoadWhite:    '#FFFFFF',
  mapRoadOutline:  '#D5CFC0',
  mapHighway:      '#FFD966',
  mapBuilding2D:   '#EFE9DD',
  mapBuilding3D:   '#E6DFD0',
  mapLabel:        '#594F3F',

  // Brand
  blue:            '#0A84FF',
  bluePressed:     '#0967D2',
  blueDark:        '#409CFF',
  red:             '#FF3B30',  // pin + identity, iOS system red

  // Category colors
  food:            '#FF9500',
  coffee:          '#A5694C',
  drinks:          '#5AC8FA',
  shopping:        '#FFCC00',
  parking:         '#AF52DE',
  hotel:           '#5856D6',
  health:          '#FF2D55',
  evGreen:         '#34C759',

  // UI chrome
  cardCanvas:      '#FFFFFF',
  surfaceGray:     '#F2F2F7',
  surfaceGray2:    '#E5E5EA',
  divider:         '#C7C7CC',

  // Text
  ink:             '#000000',
  secondary:       '#3C3C43',  // apply opacity 0.60 / 0.30 / 0.18

  // Semantic
  success:         '#34C759',
  warning:         '#FF9500',
  error:           '#FF3B30',

  // Dark mode
  darkMapLand:     '#2A2826',
  darkMapWater:    '#1C2638',
  darkMapPark:     '#2C3A2A',
  darkCardSurf:    '#1C1C1E',
  darkSurface2:    '#2C2C2E',
  darkDivider:     '#38383A',
  darkInk:         '#FFFFFF',
  darkSecondary:   '#EBEBF5',
} as const;
```

## 2. Typography

Apple Maps uses SF Pro at the system stack. On iOS this resolves natively; on Android we fall back to Roboto, which is acceptable.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const ink = '#000000';
const tabular = { fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] };

export const typography = {
  placeTitle:      { color: ink, fontSize: 28, fontWeight: '700', letterSpacing: -0.4, lineHeight: 32 },
  placeSubtitle:   { color: '#3C3C43',         fontSize: 15, fontWeight: '400', lineHeight: 20 },

  searchPlaceholder: { color: ink, fontSize: 17, fontWeight: '400', lineHeight: 22 },
  searchInput:     { color: ink, fontSize: 17, fontWeight: '400', lineHeight: 22 },

  navTitle:        { color: ink, fontSize: 17, fontWeight: '600', letterSpacing: -0.2, lineHeight: 20 },

  sectionHdr:      { color: '#3C3C43', fontSize: 13, fontWeight: '600', letterSpacing: 0.4, textTransform: 'uppercase' as const, lineHeight: 16 },

  listTitle:       { color: ink, fontSize: 17, fontWeight: '400', lineHeight: 22 },
  listSubtitle:    { color: '#3C3C43', fontSize: 13, fontWeight: '400', lineHeight: 16 },

  etaTime:         { ...tabular, color: ink, fontSize: 22, fontWeight: '600', letterSpacing: -0.2, lineHeight: 26 },
  etaDuration:     { ...tabular, color: '#3C3C43', fontSize: 17, fontWeight: '500', lineHeight: 22 },

  stepTitle:       { color: ink, fontSize: 22, fontWeight: '600', letterSpacing: -0.2, lineHeight: 26 },
  stepDistance:    { ...tabular, color: '#3C3C43', fontSize: 17, fontWeight: '500', lineHeight: 22 },

  categoryLabel:   { color: ink, fontSize: 14, fontWeight: '500', lineHeight: 17 },

  button:          { fontSize: 17, fontWeight: '600', lineHeight: 22 },

  transitBadge:    { color: '#FFFFFF', fontSize: 12, fontWeight: '700', lineHeight: 14 },

  caption:         { color: '#3C3C43', fontSize: 12, fontWeight: '400', lineHeight: 16 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Search Card (Sliding Bottom Sheet — Hero Component)

Use `@gorhom/bottom-sheet` for the three-detent sliding card.

```tsx
// components/SearchCard.tsx
import { useMemo, useRef } from 'react';
import { View, Text, TextInput, ScrollView } from 'react-native';
import BottomSheet, { BottomSheetView } from '@gorhom/bottom-sheet';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { CategoryPill } from './CategoryPill';

export function SearchCard() {
  const sheetRef = useRef<BottomSheet>(null);
  const snapPoints = useMemo(() => [88, '50%', '90%'], []);

  return (
    <BottomSheet
      ref={sheetRef}
      snapPoints={snapPoints}
      index={0}
      backgroundComponent={({ style }) => (
        <BlurView intensity={80} tint="light" style={[style, { borderTopLeftRadius: 10, borderTopRightRadius: 10, overflow: 'hidden' }]} />
      )}
      handleIndicatorStyle={{ backgroundColor: colors.secondary + '4D', width: 36, height: 5 }}
    >
      <BottomSheetView style={{ flex: 1, paddingHorizontal: 16, paddingTop: 12 }}>
        <View style={{
          flexDirection: 'row', alignItems: 'center', gap: 12,
          backgroundColor: colors.surfaceGray, borderRadius: 10, height: 44, paddingHorizontal: 16,
        }}>
          <Ionicons name="search" size={17} color={colors.secondary + '99'} />
          <TextInput
            placeholder="Search Maps"
            placeholderTextColor={colors.secondary + '4D'}
            style={[typography.searchInput, { flex: 1 }]}
          />
          <Ionicons name="mic" size={16} color={colors.secondary + '99'} />
        </View>

        <ScrollView
          horizontal
          showsHorizontalScrollIndicator={false}
          contentContainerStyle={{ paddingTop: 16, paddingBottom: 16, gap: 12 }}
        >
          <CategoryPill icon="restaurant"     label="Food"     color={colors.food} />
          <CategoryPill icon="cafe"           label="Coffee"   color={colors.coffee} />
          <CategoryPill icon="wine"           label="Drinks"   color={colors.drinks} />
          <CategoryPill icon="bag"            label="Shopping" color={colors.shopping} />
          <CategoryPill icon="car"            label="Parking"  color={colors.parking} />
          <CategoryPill icon="train"          label="Transit"  color={colors.blue} />
        </ScrollView>
      </BottomSheetView>
    </BottomSheet>
  );
}
```

### Category Pill

```tsx
// components/CategoryPill.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CategoryPill({ icon, label, color }: {
  icon: keyof typeof Ionicons.glyphMap;
  label: string;
  color: string;
}) {
  return (
    <View style={{
      width: 64, height: 88,
      backgroundColor: colors.cardCanvas, borderRadius: 14,
      alignItems: 'center', justifyContent: 'center',
      shadowColor: '#000', shadowOpacity: 0.12, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
      elevation: 3,
    }}>
      <Ionicons name={icon} size={28} color={color} />
      <Text style={[typography.categoryLabel, { marginTop: 8 }]}>{label}</Text>
    </View>
  );
}
```

### Current-Location Puck (Pulsing Blue Dot with Cone)

```tsx
// components/LocationPuck.tsx
import { useEffect } from 'react';
import { View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, Easing } from 'react-native-reanimated';
import Svg, { Defs, LinearGradient, Stop, Polygon } from 'react-native-svg';
import { colors } from '../theme/colors';

export function LocationPuck({ heading }: { heading?: number }) {
  const pulseScale = useSharedValue(1);
  const pulseOpacity = useSharedValue(0.3);

  useEffect(() => {
    pulseScale.value = withRepeat(withTiming(1.6, { duration: 2000, easing: Easing.out(Easing.cubic) }), -1, false);
    pulseOpacity.value = withRepeat(withTiming(0, { duration: 2000, easing: Easing.linear }), -1, false);
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
        width: 30, height: 30, borderRadius: 15,
        backgroundColor: colors.blue,
      }, pulseStyle]} />

      {/* Directional cone */}
      {heading !== undefined && (
        <View style={{
          position: 'absolute',
          width: 80, height: 60,
          top: 0,
          transform: [{ rotate: `${heading}deg` }],
        }}>
          <Svg width="80" height="60" viewBox="0 0 80 60">
            <Defs>
              <LinearGradient id="cone" x1="0" y1="0" x2="0" y2="1">
                <Stop offset="0" stopColor={colors.blue} stopOpacity="0" />
                <Stop offset="1" stopColor={colors.blue} stopOpacity="0.4" />
              </LinearGradient>
            </Defs>
            <Polygon points="0,0 80,0 40,60" fill="url(#cone)" />
          </Svg>
        </View>
      )}

      {/* Blue dot */}
      <View style={{
        width: 22, height: 22, borderRadius: 11,
        backgroundColor: colors.blue,
        borderWidth: 3, borderColor: '#FFFFFF',
        shadowColor: '#000', shadowOpacity: 0.20, shadowRadius: 4, shadowOffset: { width: 0, height: 2 },
        elevation: 3,
      }} />
    </View>
  );
}
```

### Map Controls (Floating Top-Right Stack)

```tsx
// components/MapControls.tsx
import { Pressable, View } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function MapControls({ onLayer, onLocation, on3D }: { onLayer: () => void; onLocation: () => void; on3D: () => void }) {
  return (
    <View style={{
      position: 'absolute',
      top: 60, right: 16,
      gap: 8,
    }}>
      <ControlButton icon="layers" onPress={onLayer} />
      <ControlButton icon="locate" onPress={onLocation} />
      <ControlButton icon="cube" onPress={on3D} />
    </View>
  );
}

function ControlButton({ icon, onPress }: { icon: keyof typeof Ionicons.glyphMap; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
        onPress();
      }}
      style={({ pressed }) => ({
        width: 44, height: 44, borderRadius: 22,
        overflow: 'hidden',
        opacity: pressed ? 0.7 : 1,
      })}
    >
      <BlurView intensity={80} tint="light" style={{
        flex: 1, alignItems: 'center', justifyContent: 'center',
      }}>
        <Ionicons name={icon} size={18} color={colors.ink} />
      </BlurView>
    </Pressable>
  );
}
```

### Place Card

```tsx
// components/PlaceCard.tsx
import { View, Text, ScrollView, Pressable } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  title: string;
  subtitle: string;
  isOpen: boolean;
  closingTime: string;
  onDirections: () => void;
  onCall: () => void;
  onClose: () => void;
};

export function PlaceCard({ title, subtitle, isOpen, closingTime, onDirections, onCall, onClose }: Props) {
  return (
    <BlurView intensity={80} tint="light" style={{
      borderTopLeftRadius: 10, borderTopRightRadius: 10,
      paddingBottom: 24,
      overflow: 'hidden',
    }}>
      {/* Top: title + close */}
      <View style={{ flexDirection: 'row', alignItems: 'flex-start', padding: 20 }}>
        <View style={{ flex: 1 }}>
          <Text style={typography.placeTitle}>{title}</Text>
          <Text style={[typography.placeSubtitle, { marginTop: 4, color: colors.secondary + '99' }]}>{subtitle}</Text>
        </View>
        <Pressable onPress={onClose} style={{
          width: 30, height: 30, borderRadius: 15,
          backgroundColor: colors.surfaceGray,
          alignItems: 'center', justifyContent: 'center',
        }}>
          <Ionicons name="close" size={14} color={colors.secondary} />
        </Pressable>
      </View>

      {/* Action row */}
      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ paddingHorizontal: 20, gap: 8 }}>
        <ActionPill label="Directions" icon="navigate" style="filled" onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onDirections(); }} />
        <ActionPill label="Call"       icon="call"     style="outlined" onPress={onCall} />
        <ActionPill label="Share"      icon="share"    style="outlined" onPress={() => {}} />
        <ActionPill label="Save"       icon="heart"    style="outlined" onPress={() => {}} />
      </ScrollView>

      {/* Hours row */}
      <View style={{ flexDirection: 'row', alignItems: 'center', paddingHorizontal: 20, paddingTop: 16 }}>
        <Text style={{ ...typography.placeSubtitle, fontWeight: '600', color: isOpen ? colors.success : colors.error }}>
          {isOpen ? 'Open' : 'Closed'}
        </Text>
        <Text style={{ ...typography.placeSubtitle, color: colors.secondary + '99', marginLeft: 4 }}>
          · {closingTime}
        </Text>
      </View>
    </BlurView>
  );
}

function ActionPill({ label, icon, style, onPress }: { label: string; icon: keyof typeof Ionicons.glyphMap; style: 'filled' | 'outlined'; onPress: () => void }) {
  const isFilled = style === 'filled';
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 8,
        paddingVertical: 12, paddingHorizontal: 20,
        borderRadius: 22,
        backgroundColor: isFilled ? (pressed ? colors.bluePressed : colors.blue) : colors.cardCanvas,
        borderWidth: isFilled ? 0 : 1, borderColor: colors.blue,
      })}
    >
      <Ionicons name={icon} size={14} color={isFilled ? '#FFFFFF' : colors.blue} />
      <Text style={[typography.button, { color: isFilled ? '#FFFFFF' : colors.blue }]}>{label}</Text>
    </Pressable>
  );
}
```

### Direction Step Card

```tsx
// components/StepCard.tsx
import { View, Text } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StepCard({ arrow, arrowRotation, title, distance }: {
  arrow: keyof typeof Ionicons.glyphMap;
  arrowRotation: number;
  title: string;
  distance: string;
}) {
  return (
    <BlurView intensity={80} tint="light" style={{
      flexDirection: 'row', alignItems: 'center', gap: 16,
      paddingHorizontal: 20, height: 96,
    }}>
      <View style={{ transform: [{ rotate: `${arrowRotation}deg` }] }}>
        <Ionicons name={arrow} size={44} color={colors.blue} />
      </View>
      <View style={{ flex: 1 }}>
        <Text style={typography.stepTitle} numberOfLines={2}>{title}</Text>
        <Text style={[typography.stepDistance, { marginTop: 4 }]}>{distance}</Text>
      </View>
    </BlurView>
  );
}
```

### ETA Bottom Bar

```tsx
// components/ETABar.tsx
import { View, Text, Pressable } from 'react-native';
import { BlurView } from 'expo-blur';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ETABar({ duration, arrival, onEnd }: { duration: string; arrival: string; onEnd: () => void }) {
  return (
    <BlurView intensity={80} tint="light" style={{
      flexDirection: 'row', alignItems: 'center',
      paddingHorizontal: 20, height: 80,
    }}>
      <View style={{ flex: 1 }}>
        <Text style={typography.etaTime}>{duration}</Text>
        <Text style={[typography.etaDuration, { marginTop: 4 }]}>{arrival}</Text>
      </View>
      <Pressable
        onPress={onEnd}
        style={({ pressed }) => ({
          paddingVertical: 12, paddingHorizontal: 20,
          borderRadius: 22,
          backgroundColor: colors.cardCanvas,
          borderWidth: 1, borderColor: colors.error,
          opacity: pressed ? 0.85 : 1,
        })}
      >
        <Text style={[typography.button, { color: colors.error }]}>End</Text>
      </Pressable>
    </BlurView>
  );
}
```

### Transit Line Badge

```tsx
// components/TransitBadge.tsx
import { View, Text } from 'react-native';

export function TransitBadge({ label, color }: { label: string; color: string }) {
  return (
    <View style={{
      minWidth: 24, height: 24,
      paddingHorizontal: 6, borderRadius: 4,
      backgroundColor: color,
      alignItems: 'center', justifyContent: 'center',
    }}>
      <Text style={{ color: '#FFFFFF', fontSize: 12, fontWeight: '700' }}>{label}</Text>
    </View>
  );
}
```

## 4. Motion & Haptics

```tsx
// Pin drop
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Directions tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Arrived at destination
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Map style picker selection
Haptics.selectionAsync();

// Pin drop animation
pinY.value = withSequence(
  withTiming(-100, { duration: 0 }),
  withSpring(0, { damping: 6, stiffness: 200 })
);

// Pulse on location puck (infinite)
pulseScale.value = withRepeat(withTiming(1.6, { duration: 2000 }), -1, false);
pulseOpacity.value = withRepeat(withTiming(0, { duration: 2000 }), -1, false);
```

## 5. Icon Library

Use `@expo/vector-icons` (Ionicons primarily; MaterialIcons for a few subway-specific glyphs):

| Purpose | Ionicons |
|---------|----------|
| Search | `search` |
| Microphone | `mic` |
| Close | `close` |
| Layer picker | `layers` |
| Current location (locked) | `locate` |
| Current location (unlocked) | `compass-outline` |
| 3D toggle | `cube` |
| Compass | `compass` |
| Dropped pin | `location` |
| Directions | `navigate` |
| Call | `call` |
| Share | `share-social` |
| Save | `heart-outline` / `heart` |
| Food | `restaurant` |
| Coffee | `cafe` |
| Drinks | `wine` |
| Shopping | `bag` |
| Parking | `car` |
| Transit | `train` / `bus` |
| Direction right | `arrow-redo` (rotate as needed) |
| Direction left | `arrow-undo` |
| Slight right | `arrow-forward` (rotated) |
| Address | `location-outline` |
| Phone | `call` |
| Globe | `globe` |
| Look Around | `eye` |
| Back | `chevron-back` |

## 6. Platform Notes

- **Bottom sheet**: install `@gorhom/bottom-sheet` for the three-detent sliding card with smooth gesture handling. Configure `enablePanDownToClose={false}` so the small detent always stays visible.
- **BlurView on Android**: `expo-blur` falls back to an opaque background on Android (`intensity={80} tint="light"` becomes `rgba(255,255,255,0.85)`). Apple Maps' translucent feel won't render natively; the visual is approximate.
- **Status bar**: Set `<StatusBar style="dark" />` on light maps; `style="light"` in dark mode. The map controls' BlurView reads the system theme automatically.
- **Safe area**: Wrap the MapView in `SafeAreaProvider` from `react-native-safe-area-context`. Map controls use absolute positioning with `top: insets.top + 16`.
- **Tabular figures**: Set `fontVariant: ['tabular-nums']` on every Text rendering distance, time, ETA, or price.
- **Map provider**: use `react-native-maps` with `provider={PROVIDER_DEFAULT}` (which is Apple Maps on iOS by default — perfect for matching the cream cartography). Override the `customMapStyle` only if absolutely necessary.
- **Pin drop animation**: use `Animated.Value` with a spring config to make the pin "bounce" into place on long-press.
- **Current-location puck**: implement the pulse with Reanimated's `withRepeat` + `withTiming`. The directional cone uses `react-native-svg` with a linear gradient.
- **Dark mode**: use `useColorScheme()` to switch between map styles (Apple Maps' native dark style) and UI tokens. Maps Blue brightens to `#409CFF` for dark mode legibility.
- **Accessibility**: Set `accessibilityRole="button"` on every map control button; `accessibilityLabel="Center on current location"`, etc. The current-location puck announces as `"Your current location"`.
- **Performance**: when rendering many pins on the map, use `react-native-maps` clustering or virtualize off-screen pins to maintain 60fps panning.
