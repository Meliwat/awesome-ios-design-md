# iHeartRadio (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates iHeartRadio's visual language into paste-ready Expo / React Native code: a design-token module, the heart-logomark station tile, the pulsing LIVE badge, the scanning bar (no scrubber), the circular red play/stop button, and the live station row.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — the only real theme for the player)
  canvas:   '#120A0E', // warm maroon-black, NOT pure black
  surface1: '#1E1216',
  surface2: '#2A171D',
  divider:  '#341C24',
  scrim:    'rgba(18,10,14,0.85)',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#B8A0A8',
  textTertiary:  '#7C6168',

  // Brand
  red:       '#C6002B',
  coral:     '#F23A2F', // LIVE signal only
  magenta:   '#E40A5D',
  redPress:  '#9E0022',
  coralPress:'#CC2D24',

  // Semantic
  success: '#1ED760',
  error:   '#FF4D5E',
  warning: '#FFB02E',
} as const;

export type IHRColor = keyof typeof colors;

// iHeart system gradient — station logos, brand chips, hero cards
export const gradients = {
  system: [colors.red, colors.magenta],                       // 135deg
  tile:   [colors.surface2, colors.surface1, colors.canvas],  // 150deg, station tile
  scan:   ['transparent', colors.coral, 'transparent'],       // horizontal sweep band
} as const;
export const tileLocations = [0, 0.6, 1] as const;
```

## 2. Typography

iHeartRadio ships **iHeart Sans**; use **Inter** as the faithful fallback (SIL OFL, free to bundle). Load via `expo-font`. Body/metadata uses Inter at 400/500; titles use the brand face at 700–800; LIVE tags at 900.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    // Swap iHeartSans-* → Inter-* if the brand face isn't licensed.
    'iHeartSans-Bold':      require('../assets/fonts/iHeartSans-Bold.ttf'),
    'iHeartSans-ExtraBold': require('../assets/fonts/iHeartSans-ExtraBold.ttf'),
    'iHeartSans-Black':     require('../assets/fonts/iHeartSans-Black.ttf'),
    'iHeartSans-SemiBold':  require('../assets/fonts/iHeartSans-SemiBold.ttf'),
    'Inter-Regular':        require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':         require('../assets/fonts/Inter-Medium.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const BRAND_BK = 'iHeartSans-Black';
const BRAND_XB = 'iHeartSans-ExtraBold';
const BRAND_B  = 'iHeartSans-Bold';
const BRAND_SB = 'iHeartSans-SemiBold';
const READ     = 'Inter-Regular';
const READ_M   = 'Inter-Medium';
const white = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...white, fontFamily: BRAND_XB, fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  stationName: { ...white, fontFamily: BRAND_XB, fontSize: 26, lineHeight: 31, letterSpacing: -0.4 },
  section:     { ...white, fontFamily: BRAND_B,  fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  subhead:     { ...white, fontFamily: BRAND_B,  fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  body:        { color: '#FFFFFF', fontFamily: READ, fontSize: 16, lineHeight: 24 },
  rowTitle:    { ...white, fontFamily: BRAND_SB, fontSize: 15, lineHeight: 20 },
  nowAiring:   { color: '#B8A0A8', fontFamily: READ_M, fontSize: 15, lineHeight: 20 },
  meta:        { color: '#B8A0A8', fontFamily: READ, fontSize: 14, lineHeight: 19 },
  liveTag:     { color: '#F23A2F', fontFamily: BRAND_BK, fontSize: 12, lineHeight: 12, letterSpacing: 0.8 },
  freq:        { ...white, fontFamily: BRAND_XB, fontSize: 12, lineHeight: 12, letterSpacing: 0.3 },
  tabLabel:    { fontFamily: BRAND_SB, fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  button:      { color: '#FFFFFF', fontFamily: BRAND_B, fontSize: 16, lineHeight: 16 },
  chip:        { ...white, fontFamily: BRAND_B, fontSize: 13, lineHeight: 13, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Heart-Logomark Station Tile (with pulsing LIVE badge + frequency chip)

```tsx
// components/StationTile.tsx
import { View, Text, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, gradients, tileLocations } from '../theme/colors';
import { typography } from '../theme/typography';
import { LiveBadge } from './LiveBadge';

export function StationTile({ frequency, isLive = true }: { frequency: string; isLive?: boolean }) {
  return (
    <View style={styles.tile}>
      <LinearGradient
        colors={gradients.tile} locations={tileLocations}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={StyleSheet.absoluteFill}
      />
      <LinearGradient
        colors={['rgba(198,0,43,0.35)', 'transparent']}
        start={{ x: 0.5, y: 0.42 }} end={{ x: 0.5, y: 1 }}
        style={StyleSheet.absoluteFill}
      />

      {/* iHeart heart-check logomark as artwork */}
      <View style={styles.heart}>
        <Ionicons name="heart" size={96} color={colors.red} />
        <Ionicons name="checkmark" size={40} color="#FFF" style={styles.check} />
      </View>

      {isLive && <View style={styles.badge}><LiveBadge /></View>}

      <View style={styles.freq}>
        <Text style={typography.freq}>{frequency}</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  tile: {
    aspectRatio: 1, borderRadius: 14, overflow: 'hidden',
    alignItems: 'center', justifyContent: 'center',
    shadowColor: colors.red, shadowOpacity: 0.45, shadowRadius: 24,
    shadowOffset: { width: 0, height: 24 }, elevation: 18,
  },
  heart: { alignItems: 'center', justifyContent: 'center' },
  check: { position: 'absolute' },
  badge: { position: 'absolute', top: 16, left: 16 },
  freq: {
    position: 'absolute', bottom: 16, right: 16,
    backgroundColor: 'rgba(18,10,14,0.7)', borderRadius: 8,
    paddingVertical: 6, paddingHorizontal: 12,
  },
});
```

### Pulsing LIVE Badge

```tsx
// components/LiveBadge.tsx
import { useEffect } from 'react';
import { View, Text, StyleSheet } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, Easing } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LiveBadge() {
  const p = useSharedValue(0);
  useEffect(() => {
    p.value = withRepeat(withTiming(1, { duration: 1600, easing: Easing.out(Easing.ease) }), -1, false);
  }, []);
  const ring = useAnimatedStyle(() => ({
    transform: [{ scale: 1 + p.value * 2 }],
    opacity: 0.7 * (1 - p.value),
  }));

  return (
    <View style={styles.badge}>
      <View style={styles.dotWrap}>
        <Animated.View style={[styles.ring, ring]} />
        <View style={styles.dot} />
      </View>
      <Text style={[typography.liveTag, { color: '#FFF' }]}>LIVE</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  badge: {
    flexDirection: 'row', alignItems: 'center', gap: 7,
    backgroundColor: colors.coral, borderRadius: 999,
    paddingVertical: 6, paddingLeft: 10, paddingRight: 12,
  },
  dotWrap: { width: 7, height: 7, alignItems: 'center', justifyContent: 'center' },
  dot: { width: 7, height: 7, borderRadius: 3.5, backgroundColor: '#FFF' },
  ring: { position: 'absolute', width: 7, height: 7, borderRadius: 3.5, borderWidth: 1.5, borderColor: '#FFF' },
});
```

### Scanning Bar (replaces the scrubber — live radio can't seek)

```tsx
// components/ScanningBar.tsx
import { useEffect, useState } from 'react';
import { View, Text, StyleSheet, LayoutChangeEvent } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, Easing, interpolate } from 'react-native-reanimated';
import { colors, gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function ScanningBar() {
  const [w, setW] = useState(1);
  const t = useSharedValue(0);
  useEffect(() => {
    t.value = withRepeat(withTiming(1, { duration: 2200, easing: Easing.inOut(Easing.ease) }), -1, false);
  }, []);
  const band = useAnimatedStyle(() => ({
    transform: [{ translateX: interpolate(t.value, [0, 1], [-w * 0.38, w]) }],
  }));

  return (
    <View>
      <View style={styles.track} onLayout={(e: LayoutChangeEvent) => setW(e.nativeEvent.layout.width)}>
        <Animated.View style={[{ width: '38%', height: 4 }, band]}>
          <LinearGradient
            colors={gradients.scan}
            start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
            style={{ flex: 1, borderRadius: 2 }}
          />
        </Animated.View>
      </View>
      <View style={styles.labelRow}>
        <View style={styles.ld} />
        <Text style={[typography.liveTag, { fontFamily: 'Inter-Medium', fontSize: 11, letterSpacing: 0.4 }]}>
          STREAMING LIVE
        </Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  track: { height: 4, borderRadius: 2, backgroundColor: colors.surface2, overflow: 'hidden' },
  labelRow: { flexDirection: 'row', alignItems: 'center', gap: 6, marginTop: 10 },
  ld: { width: 6, height: 6, borderRadius: 3, backgroundColor: colors.coral },
});
```

### Circular Red Play/Stop Button (no pause — live)

```tsx
// components/PlayStopButton.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function PlayStopButton({ playing, onToggle }: { playing: boolean; onToggle: () => void }) {
  const scale = useSharedValue(1);
  const a = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => { scale.value = withTiming(0.94, { duration: 120 }); }}
      onPressOut={() => { scale.value = withTiming(1, { duration: 120 }); }}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onToggle(); }}
    >
      <Animated.View style={[{
        width: 68, height: 68, borderRadius: 34, backgroundColor: colors.red,
        alignItems: 'center', justifyContent: 'center',
        shadowColor: colors.red, shadowOpacity: 0.65, shadowRadius: 14, shadowOffset: { width: 0, height: 12 }, elevation: 14,
      }, a]}>
        {/* live = STOP, not pause; stopped = play */}
        <Ionicons name={playing ? 'stop' : 'play'} size={26} color="#FFF" style={{ marginLeft: playing ? 0 : 2 }} />
      </Animated.View>
    </Pressable>
  );
}
```

### Live Station Row

```tsx
// components/LiveStationRow.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function LiveStationRow({ callSign, name, genre }: { callSign: string; name: string; genre: string }) {
  return (
    <Pressable style={({ pressed }) => [styles.row, pressed && { backgroundColor: colors.surface2 }]}>
      <LinearGradient colors={gradients.system} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={styles.logo}>
        <Text style={[typography.chip, { fontFamily: 'iHeartSans-Black', fontSize: 12 }]}>{callSign}</Text>
      </LinearGradient>
      <View style={{ flex: 1 }}>
        <Text style={typography.rowTitle}>{name}</Text>
        <View style={styles.sub}>
          <View style={styles.liveTag}>
            <View style={styles.d} />
            <Text style={[typography.liveTag, { fontSize: 10, letterSpacing: 0.5 }]}>LIVE</Text>
          </View>
          <Text style={typography.meta}>{genre}</Text>
        </View>
      </View>
      <Ionicons name="play-circle-outline" size={28} color={colors.coral} />
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row: { flexDirection: 'row', alignItems: 'center', gap: 12, height: 68, paddingHorizontal: 24 },
  logo: { width: 48, height: 48, borderRadius: 8, alignItems: 'center', justifyContent: 'center' },
  sub: { flexDirection: 'row', alignItems: 'center', gap: 6, marginTop: 2 },
  liveTag: { flexDirection: 'row', alignItems: 'center', gap: 4 },
  d: { width: 5, height: 5, borderRadius: 2.5, backgroundColor: colors.coral },
});
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
        headerShown: false,
        tabBarActiveTintColor: colors.coral,        // coral = the LIVE/active color
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(18,10,14,0.94)',
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'iHeartSans-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'For You',  tabBarIcon: ({ color }) => <Ionicons name="home"        size={22} color={color} /> }} />
      <Tabs.Screen name="radio"    options={{ title: 'Radio',    tabBarIcon: ({ color }) => <Ionicons name="radio"       size={22} color={color} /> }} />
      <Tabs.Screen name="podcasts" options={{ title: 'Podcasts', tabBarIcon: ({ color }) => <Ionicons name="mic"         size={22} color={color} /> }} />
      <Tabs.Screen name="search"   options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search"      size={22} color={color} /> }} />
      <Tabs.Screen name="library"  options={{ title: 'Library',  tabBarIcon: ({ color }) => <Ionicons name="person"      size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// LIVE badge pulse — continuous while live (~1.6s ring expand & fade)
p.value = withRepeat(withTiming(1, { duration: 1600, easing: Easing.out(Easing.ease) }), -1, false);

// Scanning bar sweep — continuous while streaming (~2.2s left→right)
t.value = withRepeat(withTiming(1, { duration: 2200, easing: Easing.inOut(Easing.ease) }), -1, false);

// Play/stop — scale 0.94 + soft haptic (NO pause state)
scale.value = withTiming(0.94, { duration: 120 });   // onPressIn
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Player expand/collapse (mini → full) — spring sheet
// withSpring({ damping: 18, stiffness: 140 }) or a Reanimated layout transition

// Station skip — tile slide + crossfade
// SlideInRight / FadeIn entering on the tile + metadata container

// Reduce Motion: skip both withRepeat loops — render a static LIVE badge and a
// static coral fill at the track's left edge; keep the play/stop feedback
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). iHeartRadio's iconography is simple; the heart logomark is `heart` + a `checkmark` overlay, the scanning bar is a drawn gradient band.

| Purpose | Ionicons |
|---------|----------|
| Collapse player | `chevron-down` |
| Overflow menu | `ellipsis-horizontal` |
| Play | `play` |
| Stop (live — no pause) | `stop` |
| Station skip back | `play-skip-back` |
| Station skip fwd | `play-skip-forward` |
| Player settings | `settings` |
| Favorite (filled) | `heart` |
| Heart logomark | `heart` + `checkmark` |
| Row play | `play-circle-outline` |
| For You (tab) | `home` |
| Radio (tab) | `radio` |
| Podcasts (tab) | `mic` |
| Search (tab) | `search` |
| Library (tab) | `person` |
| Add to library | `add` |
| Share | `share-outline` |
| Alarm / sleep timer | `alarm` |

## 7. Platform Notes

- **Font choice**: bundle **iHeart Sans** if licensed; otherwise ship **Inter** (SIL OFL) as the faithful fallback for both brand and reading roles — never leave titles to system-only
- **Status bar**: `<StatusBar style="light" />` always — the player is dark-native
- **Force dark**: do not branch on `useColorScheme()` for the player; pin the dark tokens. Only honor a forced-light host with the minimal fallback palette (DESIGN.md §2)
- **Safe area**: wrap screens in `SafeAreaView`; the player top bar and bottom tab bar need safe-area padding; the station tile and scrim extend edge-to-edge
- **Dynamic Type**: keep `allowFontScaling` on titles/body/rows; set `allowFontScaling={false}` on tab labels, LIVE tags, frequency chip, overline, chip text (layout-sensitive)
- **expo-linear-gradient**: required for the station tile, system gradient, and scanning-bar band — never fake the scan with a solid color
- **Reanimated**: the LIVE pulse and scanning sweep run on the UI thread for 60fps; cancel both when the stream stops
- **No scrubber, ever**: a live stream has no position — do not render a progress fill, knob, or timestamps; the scanning bar is indeterminate by design
- **Reduce Motion**: read `AccessibilityInfo.isReduceMotionEnabled()` — stop the pulse + sweep (static badge + static coral fill); keep the play/stop feedback
- **Accessibility**: the scanning bar is `accessibilityElementsHidden`; expose "Live" in the station/row `accessibilityLabel` (not coral color/pulse alone); label the transport button "Play"/"Stop" — never "Pause"
