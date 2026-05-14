# Strava (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Strava's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated patterns for the kudos confetti, Record button, and route polyline.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, `react-native-maps`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Strava Orange — the only accent
  orange:         '#FC4C02',
  orangePressed:  '#D44002',
  orangeHalo:     'rgba(252,76,2,0.3)',

  // Canvas, surface, divider (light)
  canvas:         '#FFFFFF',
  surfaceWarm:    '#F5F4F2',
  surfaceCool:    '#F0F0F0',
  divider:        '#E5E5E5',
  mapTile:        '#E8E5DD',

  // Text (light)
  inkPrimary:     '#0E0E0E',
  inkSecondary:   '#666666',
  inkTertiary:    '#9A9A9A',
  inverted:       '#FFFFFF',

  // Dark mode
  darkCanvas:     '#0F0F0F',
  darkSurface:    '#1A1A1A',
  darkSurface2:   '#262626',
  darkDivider:    '#2A2A2A',
  darkText:       '#F2F2F2',
  darkTextSec:    '#A0A0A0',
  darkMapTile:    '#1B1B1B',

  // Achievement
  prGold:         '#F5C24A',
  silver:         '#C6C6C6',
  bronze:         '#CD7F32',
  komCrown:       '#FFD700',

  // Chart & semantic
  success:        '#22C55E',
  heartRed:       '#E74C3C',
  paceBlue:       '#3B82F6',
  elevBrown:      '#8B6F47',
  warning:        '#F59E0B',
  error:          '#EF4444',
} as const;

export type StravaColor = keyof typeof colors;
```

## 2. Typography

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
import { Platform } from 'react-native';

const sysDisplay = Platform.select({ ios: 'SF Pro Display', default: 'System' });
const sysText    = Platform.select({ ios: 'SF Pro Text',    default: 'System' });

const tabular = { fontVariant: ['tabular-nums' as const] };

export const typography = {
  // Hero stats (activity detail) — Black weight, tabular
  heroStat:      { fontFamily: sysDisplay, fontSize: 44, fontWeight: '900', letterSpacing: -0.6, ...tabular },
  heroUnit:      { fontFamily: sysText,    fontSize: 13, fontWeight: '600', letterSpacing: 0.5, textTransform: 'uppercase' as const },

  largeNav:      { fontFamily: sysDisplay, fontSize: 28, fontWeight: '700', letterSpacing: -0.4 },

  // Card titles & athlete
  activityTitle: { fontFamily: sysText,    fontSize: 17, fontWeight: '600', letterSpacing: -0.2 },
  athlete:       { fontFamily: sysText,    fontSize: 15, fontWeight: '600' },

  // UPPERCASE labels
  statLabel:     { fontFamily: sysText,    fontSize: 11, fontWeight: '600', letterSpacing: 0.6, textTransform: 'uppercase' as const },
  sectionHdr:    { fontFamily: sysText,    fontSize: 13, fontWeight: '700', letterSpacing: 0.5, textTransform: 'uppercase' as const },

  // Stat values
  statValue:     { fontFamily: sysText,    fontSize: 17, fontWeight: '700', ...tabular },

  // Body / meta
  body:          { fontFamily: sysText,    fontSize: 15, fontWeight: '400', lineHeight: 21 },
  bodySmall:     { fontFamily: sysText,    fontSize: 13, fontWeight: '400' },
  kudosCount:    { fontFamily: sysText,    fontSize: 13, fontWeight: '600', ...tabular },
  meta:          { fontFamily: sysText,    fontSize: 13, fontWeight: '400' },

  // Buttons
  button:        { fontFamily: sysText,    fontSize: 17, fontWeight: '600', letterSpacing: -0.2 },
  buttonSm:      { fontFamily: sysText,    fontSize: 15, fontWeight: '600' },

  // Tab & rank
  tab:           { fontFamily: sysText,    fontSize: 10, fontWeight: '600', letterSpacing: 0.2 },
  rank:          { fontFamily: sysText,    fontSize: 13, fontWeight: '900', ...tabular },

  // Caption
  caption:       { fontFamily: sysText,    fontSize: 11, fontWeight: '400' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Stat Cell (3-up grid primitive)

```tsx
// components/StatCell.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StatCell({ label, value }: { label: string; value: string }) {
  return (
    <View style={{ flex: 1, alignItems: 'center', paddingVertical: 16 }}>
      <Text style={[typography.statLabel, { color: colors.inkSecondary }]}>{label}</Text>
      <Text style={[typography.statValue, { color: colors.inkPrimary, marginTop: 4 }]}>{value}</Text>
    </View>
  );
}

export function StatGrid({ stats }: { stats: { label: string; value: string }[] }) {
  return (
    <View style={{ flexDirection: 'row' }}>
      {stats.map((s, i) => (
        <View key={s.label} style={{ flex: 1, flexDirection: 'row' }}>
          <StatCell label={s.label} value={s.value} />
          {i < stats.length - 1 && (
            <View style={{ width: 1, backgroundColor: colors.surfaceCool, marginVertical: 12 }} />
          )}
        </View>
      ))}
    </View>
  );
}
```

### Route Map Snapshot

```tsx
// components/RouteMapSnapshot.tsx
import MapView, { Polyline, Marker } from 'react-native-maps';
import { View, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';

type Coord = { latitude: number; longitude: number };

export function RouteMapSnapshot({ coordinates }: { coordinates: Coord[] }) {
  const mid = coordinates[Math.floor(coordinates.length / 2)];
  return (
    <View style={styles.frame}>
      <MapView
        style={styles.map}
        scrollEnabled={false}
        zoomEnabled={false}
        rotateEnabled={false}
        pitchEnabled={false}
        initialRegion={{ latitude: mid.latitude, longitude: mid.longitude, latitudeDelta: 0.02, longitudeDelta: 0.02 }}
      >
        {/* Halo polyline first (thicker, lighter) */}
        <Polyline
          coordinates={coordinates}
          strokeColor={colors.orangeHalo}
          strokeWidth={6}
          lineCap="round"
          lineJoin="round"
        />
        {/* Main polyline on top */}
        <Polyline
          coordinates={coordinates}
          strokeColor={colors.orange}
          strokeWidth={4}
          lineCap="round"
          lineJoin="round"
        />
        {coordinates[0] && <Marker coordinate={coordinates[0]}><StartDot /></Marker>}
        {coordinates[coordinates.length - 1] && <Marker coordinate={coordinates[coordinates.length - 1]}><EndDot /></Marker>}
      </MapView>
    </View>
  );
}

function StartDot() {
  return <View style={{ width: 12, height: 12, borderRadius: 6, backgroundColor: '#FFF', borderWidth: 2, borderColor: colors.orange }} />;
}
function EndDot() {
  return <View style={{ width: 12, height: 12, borderRadius: 6, backgroundColor: colors.orange, borderWidth: 2, borderColor: '#FFF' }} />;
}

const styles = StyleSheet.create({
  frame: { width: '100%', aspectRatio: 16/9, borderRadius: 6, overflow: 'hidden', backgroundColor: colors.mapTile },
  map:   { ...StyleSheet.absoluteFillObject },
});
```

### Kudos Button (with confetti)

```tsx
// components/KudosButton.tsx
import { useState } from 'react';
import { Pressable, View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function KudosButton({ initialCount }: { initialCount: number }) {
  const [given, setGiven] = useState(false);
  const [count, setCount] = useState(initialCount);
  const scale = useSharedValue(1);
  const confettiOpacity = useSharedValue(0);
  const confettiY = useSharedValue(0);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  const confettiStyle = useAnimatedStyle(() => ({ opacity: confettiOpacity.value, transform: [{ translateY: confettiY.value }] }));

  const onPress = () => {
    const next = !given;
    setGiven(next);
    setCount((c) => c + (next ? 1 : -1));
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
    scale.value = withSequence(withSpring(1.3, { damping: 8 }), withSpring(1, { damping: 8 }));
    if (next) {
      confettiOpacity.value = 1;
      confettiY.value = 0;
      confettiOpacity.value = withTiming(0, { duration: 600 });
      confettiY.value = withTiming(-30, { duration: 600 });
    }
  };

  return (
    <Pressable onPress={onPress} style={{ flexDirection: 'row', alignItems: 'center', gap: 6, padding: 4 }}>
      <Animated.View style={aStyle}>
        <Ionicons
          name={given ? 'thumbs-up' : 'thumbs-up-outline'}
          size={20}
          color={given ? colors.orange : colors.inkSecondary}
        />
      </Animated.View>
      <Text style={[typography.kudosCount, { color: colors.inkPrimary }]}>{count}</Text>
      {/* Confetti dots */}
      <Animated.View pointerEvents="none" style={[{ position: 'absolute', flexDirection: 'row', gap: 4, top: -8 }, confettiStyle]}>
        {[0, 1, 2, 3, 4, 5, 6, 7].map((i) => (
          <View key={i} style={{ width: 4, height: 4, borderRadius: 2, backgroundColor: colors.orange }} />
        ))}
      </Animated.View>
    </Pressable>
  );
}
```

### Activity Feed Card (the hero component)

```tsx
// components/ActivityCard.tsx
import { View, Text, Image, Pressable } from 'react-native';
import { Ionicons, MaterialCommunityIcons } from '@expo/vector-icons';
import { RouteMapSnapshot } from './RouteMapSnapshot';
import { StatGrid } from './StatCell';
import { KudosButton } from './KudosButton';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  avatar: { uri: string };
  athleteName: string;
  timestamp: string;
  title: string;
  coordinates: { latitude: number; longitude: number }[];
  distance: string;
  elapsed: string;
  pace: string;
  kudos: number;
  comments: number;
};

export function ActivityCard(props: Props) {
  return (
    <Pressable style={{ backgroundColor: colors.canvas, paddingHorizontal: 16, paddingVertical: 16 }}>
      {/* Header */}
      <View style={{ flexDirection: 'row', alignItems: 'center' }}>
        <Image source={props.avatar} style={{ width: 40, height: 40, borderRadius: 20 }} />
        <View style={{ flex: 1, marginLeft: 12 }}>
          <Text style={[typography.athlete, { color: colors.inkPrimary }]}>{props.athleteName}</Text>
          <Text style={[typography.meta, { color: colors.inkSecondary, marginTop: 2 }]}>{props.timestamp}</Text>
        </View>
        <MaterialCommunityIcons name="run" size={20} color={colors.orange} />
      </View>

      {/* Title */}
      <Text style={[typography.activityTitle, { color: colors.inkPrimary, marginTop: 12 }]}>{props.title}</Text>

      {/* Map */}
      <View style={{ marginTop: 12 }}>
        <RouteMapSnapshot coordinates={props.coordinates} />
      </View>

      {/* Stats */}
      <View style={{ marginTop: 12 }}>
        <StatGrid stats={[
          { label: 'Distance', value: props.distance },
          { label: 'Time',     value: props.elapsed },
          { label: 'Pace',     value: props.pace },
        ]} />
      </View>

      {/* Divider */}
      <View style={{ height: 0.5, backgroundColor: colors.divider, marginTop: 8 }} />

      {/* Kudos + comments + view */}
      <View style={{ flexDirection: 'row', alignItems: 'center', marginTop: 8, gap: 16 }}>
        <KudosButton initialCount={props.kudos} />
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
          <Ionicons name="chatbubble-outline" size={16} color={colors.inkSecondary} />
          <Text style={[typography.kudosCount, { color: colors.inkSecondary }]}>{props.comments}</Text>
        </View>
        <View style={{ flex: 1 }} />
        <Text style={[typography.buttonSm, { color: colors.orange }]}>View Activity</Text>
      </View>
    </Pressable>
  );
}
```

### Record Button (the center tab)

```tsx
// components/RecordButton.tsx
import { Pressable, View, StyleSheet } from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function RecordButton({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy); onPress(); }}
      style={({ pressed }) => ([styles.btn, { transform: [{ scale: pressed ? 0.95 : 1 }] }])}
    >
      <Ionicons name="radio-button-on" size={26} color="#FFFFFF" />
    </Pressable>
  );
}

const styles = StyleSheet.create({
  btn: {
    width: 56, height: 56, borderRadius: 28, backgroundColor: colors.orange,
    alignItems: 'center', justifyContent: 'center',
    shadowColor: colors.orange, shadowOpacity: 0.4, shadowRadius: 8, shadowOffset: { width: 0, height: 8 },
    elevation: 12,
    position: 'absolute',
    bottom: 28, alignSelf: 'center',
  },
});
```

### Leaderboard Row

```tsx
// components/LeaderboardRow.tsx
import { View, Text, Image } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LeaderboardRow({ rank, avatar, name, date, time, gap }: any) {
  const bg = rank === 1 ? colors.prGold : rank === 2 ? colors.silver : rank === 3 ? colors.bronze : colors.surfaceCool;
  const fg = rank <= 3 ? '#FFFFFF' : colors.inkPrimary;

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, height: 56, borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      <View style={{ width: 32, height: 32, borderRadius: 16, backgroundColor: bg, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={[typography.rank, { color: fg }]}>{rank}</Text>
      </View>
      <Image source={avatar} style={{ width: 36, height: 36, borderRadius: 18, marginLeft: 12 }} />
      <View style={{ flex: 1, marginLeft: 12 }}>
        <Text style={[typography.athlete, { color: colors.inkPrimary }]}>{name}</Text>
        <Text style={[typography.caption, { color: colors.inkSecondary, marginTop: 2 }]}>{date}</Text>
      </View>
      <View style={{ alignItems: 'flex-end' }}>
        <Text style={[typography.statValue, { color: colors.inkPrimary }]}>{time}</Text>
        <Text style={[typography.caption, { color: colors.inkSecondary, marginTop: 2 }]}>{gap}</Text>
      </View>
    </View>
  );
}
```

### Achievement Badge

```tsx
// components/AchievementBadge.tsx
import { View, Text } from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AchievementBadge({ icon, text }: { icon: any; text: string }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 4,
      paddingHorizontal: 10, paddingVertical: 4, borderRadius: 500,
      backgroundColor: 'rgba(252,76,2,0.08)',
    }}>
      <Ionicons name={icon} size={14} color={colors.orange} />
      <Text style={[typography.kudosCount, { color: colors.inkPrimary }]}>{text}</Text>
    </View>
  );
}
```

## 4. Tab Bar with Center Record (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { View } from 'react-native';
import { Ionicons, MaterialCommunityIcons } from '@expo/vector-icons';
import { colors } from '../../theme/colors';
import { RecordButton } from '../../components/RecordButton';

export default function TabsLayout() {
  return (
    <View style={{ flex: 1 }}>
      <Tabs
        screenOptions={{
          tabBarActiveTintColor: colors.orange,
          tabBarInactiveTintColor: colors.inkSecondary,
          tabBarLabelStyle: { fontFamily: 'System', fontSize: 10, fontWeight: '600' },
          tabBarStyle: { height: 64 + 24, paddingTop: 8, borderTopWidth: 0.5, borderTopColor: colors.divider },
        }}
      >
        <Tabs.Screen name="index"  options={{ title: 'Home',   tabBarIcon: ({ color }) => <Ionicons name="home-outline" size={24} color={color} /> }} />
        <Tabs.Screen name="maps"   options={{ title: 'Maps',   tabBarIcon: ({ color }) => <Ionicons name="map-outline"  size={24} color={color} /> }} />
        <Tabs.Screen name="record" options={{ title: '',       tabBarButton: () => null }} />
        <Tabs.Screen name="groups" options={{ title: 'Groups', tabBarIcon: ({ color }) => <Ionicons name="people-outline" size={24} color={color} /> }} />
        <Tabs.Screen name="you"    options={{ title: 'You',    tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={26} color={color} /> }} />
      </Tabs>
      <RecordButton onPress={() => { /* open record sheet */ }} />
    </View>
  );
}
```

## 5. Motion & Haptics

```tsx
// Kudos tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
scale.value = withSequence(withSpring(1.3, { damping: 8 }), withSpring(1, { damping: 8 }));

// Record button tap — heaviest in the app
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);

// Tab switch
Haptics.selectionAsync();

// Activity card tap → push to detail
Haptics.selectionAsync();

// PR celebration on first view
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
trophyScale.value = withSequence(withTiming(1.1, { duration: 300 }), withTiming(1, { duration: 300 }));
```

## 6. Icon Library

| Purpose | Ionicons / MCI |
|---------|----------------|
| Kudos (un-given) | `thumbs-up-outline` |
| Kudos (given) | `thumbs-up` |
| Comment | `chatbubble-outline` |
| Activity — Run | `run` (MaterialCommunityIcons) |
| Activity — Ride | `bike` (MCI) |
| Activity — Swim | `swim` (MCI) |
| Activity — Hike | `hiking` (MCI) |
| Record (center) | `radio-button-on` (Ionicons) |
| Trophy / PR | `trophy` (Ionicons) |
| Crown / KOM | `medal` (Ionicons) |
| Home tab | `home-outline` / `home` |
| Maps tab | `map-outline` / `map` |
| Groups tab | `people-outline` / `people` |
| Profile tab | `person-circle-outline` / `person-circle` |
| Search | `search` |
| Filter | `options-outline` |
| Share | `share-outline` |
| Heart rate | `heart` |
| Elevation | `triangle` (MCI) |

## 7. Platform Notes

- **react-native-maps polyline**: render two `<Polyline>` components on the same `<MapView>` — the halo first (strokeWidth 6, `colors.orangeHalo`), the main on top (strokeWidth 4, `colors.orange`). Z-order matches render order; lineCap/lineJoin must both be `round`.
- **Custom map style**: Strava ships a custom tile style that's warm parchment in light mode and charcoal in dark. Pass `customMapStyle={STRAVA_MAP_STYLE_JSON}` to `<MapView provider="google" />` — the JSON is a Google Maps style declaration.
- **Map snapshot performance**: in long lists, use `react-native-maps` `liteMode` on Android and `loadingEnabled` on iOS to avoid render thrash; or pre-render the snapshot to a PNG on the server and ship it as an image.
- **Status bar**: `<StatusBar style="dark" />` on light canvas, `style="light"` on dark.
- **Safe area**: wrap in `SafeAreaView` from `react-native-safe-area-context`. The Record button needs to clear the home indicator — use `useSafeAreaInsets().bottom` to position it.
- **Tabular numerals**: `fontVariant: ['tabular-nums']` works on iOS but is ignored on Android — use `fontFamily: 'monospace'` as a fallback for splits tables on Android if column alignment matters.
- **Confetti performance**: keep the confetti to 8 simple `<View>` dots driven by Reanimated shared values — don't load a confetti library for a single micro-interaction.
- **Dark mode**: `useColorScheme()` to swap the canvas / surface / text tokens; Strava Orange stays identical. Switch the map style JSON between the warm and dark variants.
