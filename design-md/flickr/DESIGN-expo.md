# Flickr (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Flickr's visual language into paste-ready Expo / React Native code: a design-token module, the justified mosaic, the photo detail page, the EXIF table, and the favorite-star animation with Reanimated + Haptics.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-image`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — default for gallery viewing)
  canvas:        '#0C0D0E',
  surface1:      '#1A1B1D',
  surface2:      '#232427',
  divider:       '#2A2C30',

  // Surfaces (light)
  canvasLight:   '#FFFFFF',
  surfaceLight:  '#F5F6F7',
  surfacePressed:'#EAECEF',
  dividerLight:  '#E2E4E8',

  // Text
  textPrimary:    '#FFFFFF',
  textSecondary:  '#B0B3B8',
  textTertiary:   '#6E7176',
  textPrimaryLt:  '#1C1F23',
  textSecondaryLt:'#6B7077',

  // Brand (twin dots)
  flickrPink:        '#FF0084',
  flickrPinkPressed: '#D60070',
  flickrBlue:        '#0063DC',
  flickrBluePressed: '#0052B8',

  // Accent & semantic
  proGold: '#FFB200',
  success: '#00C781',
  error:   '#FF3B5C',

  // Photo scrim
  scrim:   'rgba(0,0,0,0.45)',
} as const;

export type FlickrColor = keyof typeof colors;
```

## 2. Typography

Load **Proza Libre** (titles/headers), **Inter** (UI), **IBM Plex Mono** (EXIF) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'ProzaLibre-Bold':     require('../assets/fonts/ProzaLibre-Bold.ttf'),
    'ProzaLibre-SemiBold': require('../assets/fonts/ProzaLibre-SemiBold.ttf'),
    'Inter-Regular':       require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':        require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':      require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':          require('../assets/fonts/Inter-Bold.ttf'),
    'IBMPlexMono-SemiBold':require('../assets/fonts/IBMPlexMono-SemiBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  // Editorial serif
  screenTitle: { fontFamily: 'ProzaLibre-Bold',     fontSize: 32, lineHeight: 38, letterSpacing: -0.4, color: '#FFFFFF' },
  sectionHead: { fontFamily: 'ProzaLibre-Bold',     fontSize: 26, lineHeight: 33, letterSpacing: -0.3, color: '#FFFFFF' },
  navTitle:    { fontFamily: 'ProzaLibre-SemiBold', fontSize: 22, lineHeight: 28, letterSpacing: -0.3, color: '#FFFFFF' },
  photoTitle:  { fontFamily: 'ProzaLibre-SemiBold', fontSize: 18, lineHeight: 24, letterSpacing: -0.2, color: '#FFFFFF' },

  // UI sans
  body:      { fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 24, color: '#FFFFFF' },
  cardTitle: { fontFamily: 'Inter-SemiBold', fontSize: 15, lineHeight: 20, color: '#FFFFFF' },
  button:    { fontFamily: 'Inter-Bold',     fontSize: 15, lineHeight: 15, color: '#FFFFFF' },
  meta:      { fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 18, color: '#B0B3B8' },
  faveCount: { fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 11, color: '#FFFFFF' },
  exifKey:   { fontFamily: 'Inter-Regular',  fontSize: 12, lineHeight: 17, color: '#B0B3B8' },
  tagChip:   { fontFamily: 'Inter-SemiBold', fontSize: 12, lineHeight: 12, color: '#FFFFFF' },
  tab:       { fontFamily: 'Inter-Medium',   fontSize: 10, lineHeight: 10, letterSpacing: 0.1, color: '#6E7176' },
  caption:   { fontFamily: 'Inter-Regular',  fontSize: 12, lineHeight: 16, color: '#B0B3B8' },

  // Monospace EXIF readout
  exifValue: { fontFamily: 'IBMPlexMono-SemiBold', fontSize: 11, lineHeight: 16, letterSpacing: 0.3, color: '#FFFFFF' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Justified Photo Mosaic

```tsx
// components/JustifiedMosaic.tsx
import { ScrollView, View, useWindowDimensions } from 'react-native';
import { MosaicTile } from './MosaicTile';

type Photo = { id: string; aspect: number; gradient: [string, string, string]; faves?: number };

const GUTTER = 3;
const TARGET_ROW = 115;

function packRows(photos: Photo[], width: number): Photo[][] {
  const rows: Photo[][] = [];
  let current: Photo[] = [];
  let aspectSum = 0;
  for (const p of photos) {
    current.push(p);
    aspectSum += p.aspect;
    const rowHeight = (width - GUTTER * (current.length - 1)) / aspectSum;
    if (rowHeight <= TARGET_ROW) { rows.push(current); current = []; aspectSum = 0; }
  }
  if (current.length) rows.push(current);
  return rows;
}

export function JustifiedMosaic({ photos }: { photos: Photo[] }) {
  const { width } = useWindowDimensions();
  const rows = packRows(photos, width);
  return (
    <ScrollView style={{ backgroundColor: '#0C0D0E' }} showsVerticalScrollIndicator={false}>
      {rows.map((row, i) => {
        const aspectSum = row.reduce((s, p) => s + p.aspect, 0);
        const h = (width - GUTTER * (row.length - 1)) / aspectSum;
        return (
          <View key={i} style={{ flexDirection: 'row', marginBottom: GUTTER }}>
            {row.map((p, j) => (
              <View key={p.id} style={{ width: h * p.aspect, height: h, marginRight: j < row.length - 1 ? GUTTER : 0 }}>
                <MosaicTile photo={p} />
              </View>
            ))}
          </View>
        );
      })}
    </ScrollView>
  );
}
```

```tsx
// components/MosaicTile.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { typography } from '../theme/typography';

export function MosaicTile({ photo }: { photo: { gradient: [string, string, string]; faves?: number } }) {
  return (
    <View style={{ flex: 1, overflow: 'hidden' }}>
      <LinearGradient colors={photo.gradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ flex: 1 }} />
      {photo.faves != null && (
        <>
          <LinearGradient
            colors={['transparent', 'rgba(0,0,0,0.45)']}
            style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: '40%' }}
          />
          <View style={{ position: 'absolute', left: 8, bottom: 7, flexDirection: 'row', alignItems: 'center', gap: 4 }}>
            <Ionicons name="star" size={13} color="#FFFFFF" />
            <Text style={[typography.faveCount, { textShadowColor: 'rgba(0,0,0,0.7)', textShadowRadius: 3 }]}>
              {photo.faves}
            </Text>
          </View>
        </>
      )}
    </View>
  );
}
```

### Favorite Star (the like primitive)

```tsx
// components/FavoriteStar.tsx
import { useState } from 'react';
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

const AIcon = Animated.createAnimatedComponent(Ionicons);

export function FavoriteStar({ initial = false, count }: { initial?: boolean; count: number }) {
  const [faved, setFaved] = useState(initial);
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const onPress = () => {
    const next = !faved;
    setFaved(next);
    if (next) {
      Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
      scale.value = withSequence(
        withSpring(1.25, { damping: 6, stiffness: 180 }),
        withSpring(1.0,  { damping: 6, stiffness: 180 }),
      );
    }
  };

  return (
    <Pressable onPress={onPress} hitSlop={12} style={{ width: 44, height: 44, alignItems: 'center', justifyContent: 'center' }}>
      <AIcon
        name={faved ? 'star' : 'star-outline'}
        size={22}
        color={faved ? colors.flickrPink : colors.textPrimary}
        style={style}
      />
    </Pressable>
  );
}
```

### Photo Detail Page

```tsx
// components/PhotoDetail.tsx
import { ScrollView, View, Text, Image } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { PhotoActionBar } from './PhotoActionBar';
import { ExifTable } from './ExifTable';
import { FollowPill } from './FollowPill';

export function PhotoDetail({ photo }: { photo: { gradient: [string, string, string]; aspect: number } }) {
  return (
    <ScrollView style={{ backgroundColor: colors.canvas }}>
      <View style={{ width: '100%', aspectRatio: photo.aspect }}>
        <LinearGradient colors={photo.gradient} style={{ flex: 1 }} />
      </View>
      <Text style={[typography.photoTitle, { paddingHorizontal: 16, paddingTop: 16 }]}>Aurora over Vestrahorn</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, paddingHorizontal: 16, paddingTop: 12 }}>
        <LinearGradient colors={[colors.flickrPink, colors.flickrBlue]} style={{ width: 30, height: 30, borderRadius: 15 }} />
        <View style={{ flex: 1 }}>
          <Text style={[typography.meta, { color: colors.textPrimary, fontFamily: 'Inter-SemiBold' }]}>Sofia Marent</Text>
          <Text style={typography.caption}>312 faves · 2d ago</Text>
        </View>
        <FollowPill />
      </View>
      <PhotoActionBar />
      <View style={{ height: 0.5, backgroundColor: colors.divider }} />
      <Text style={[typography.body, { padding: 16 }]}>
        Shot on a 30-second exposure just after the storm cleared the ridge.
      </Text>
      <ExifTable />
    </ScrollView>
  );
}
```

### EXIF / Camera Detail Table

```tsx
// components/ExifTable.tsx
import { View, Text, Pressable } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const ROWS = [
  { k: 'Camera',       v: 'Sony ILCE-7M4',   tappable: true },
  { k: 'Lens',         v: 'FE 35mm F1.4 GM',  tappable: true },
  { k: 'Exposure',     v: '1/250 sec',       tappable: false },
  { k: 'Aperture',     v: 'f/2.8',           tappable: false },
  { k: 'Focal length', v: '35 mm',           tappable: false },
  { k: 'ISO',          v: '100',             tappable: false },
  { k: 'Date taken',   v: 'Jan 14, 2026',    tappable: false },
];

export function ExifTable() {
  return (
    <View style={{ margin: 16, backgroundColor: colors.surface1, borderRadius: 12, overflow: 'hidden' }}>
      <Text style={[typography.tab, { color: colors.textTertiary, fontFamily: 'Inter-Bold', letterSpacing: 0.8, paddingHorizontal: 16, paddingTop: 16, paddingBottom: 10 }]}>
        CAMERA DETAILS
      </Text>
      {ROWS.map((r, i) => (
        <Pressable key={r.k} disabled={!r.tappable}>
          <View style={{ flexDirection: 'row', justifyContent: 'space-between', paddingHorizontal: 16, paddingVertical: 7 }}>
            <Text style={typography.exifKey}>{r.k}</Text>
            <Text style={typography.exifValue}>{r.v}</Text>
          </View>
          {i < ROWS.length - 1 && <View style={{ height: 0.5, backgroundColor: colors.divider, marginLeft: 16 }} />}
        </Pressable>
      ))}
    </View>
  );
}
```

### Photo Action Bar + Follow Pill

```tsx
// components/PhotoActionBar.tsx
import { View, Text, Pressable } from 'react-native';
import { useState } from 'react';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PhotoActionBar() {
  const [faved, setFaved] = useState(false);
  const Item = ({ icon, label, tint, onPress }: any) => (
    <Pressable onPress={onPress} style={{ alignItems: 'center', gap: 6 }}>
      <Ionicons name={icon} size={18} color={tint} />
      <Text style={[typography.caption, { color: tint }]}>{label}</Text>
    </Pressable>
  );
  return (
    <View style={{ flexDirection: 'row', gap: 22, paddingHorizontal: 16, paddingVertical: 12 }}>
      <Item icon={faved ? 'star' : 'star-outline'} label="Fave" tint={faved ? colors.flickrPink : colors.textSecondary} onPress={() => setFaved(!faved)} />
      <Item icon="chatbubble-outline"   label="Comment" tint={colors.textSecondary} />
      <Item icon="share-outline"        label="Share"   tint={colors.textSecondary} />
      <Item icon="albums-outline"       label="Add"     tint={colors.textSecondary} />
    </View>
  );
}

// components/FollowPill.tsx
export function FollowPill() {
  const [following, setFollowing] = useState(false);
  return (
    <Pressable
      onPress={() => setFollowing(!following)}
      style={{
        paddingVertical: 8, paddingHorizontal: 18, borderRadius: 999,
        backgroundColor: following ? 'transparent' : colors.flickrPink,
        borderWidth: following ? 1 : 0, borderColor: colors.textSecondary,
      }}
    >
      <Text style={{ fontFamily: 'Inter-Bold', fontSize: 13, color: following ? colors.textSecondary : '#FFFFFF' }}>
        {following ? 'Following' : 'Follow'}
      </Text>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.textPrimary,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
        tabBarBackground: () => <BlurView tint="dark" intensity={80} style={{ flex: 1 }} />,
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Feed',   tabBarIcon: ({ color }) => <Ionicons name="home-outline" size={23} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search', tabBarIcon: ({ color }) => <Ionicons name="search" size={23} color={color} /> }} />
      <Tabs.Screen name="camera"  options={{ title: '',       tabBarIcon: () => <Ionicons name="camera" size={27} color={colors.flickrPink} /> }} />
      <Tabs.Screen name="notify"  options={{ title: 'Notify', tabBarIcon: ({ color }) => <Ionicons name="notifications-outline" size={23} color={color} /> }} />
      <Tabs.Screen name="you"     options={{ title: 'You',    tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={23} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Fave star burst
scale.value = withSequence(
  withSpring(1.25, { damping: 6, stiffness: 180 }),
  withSpring(1.0,  { damping: 6, stiffness: 180 }),
);

// Grid -> detail: use a shared-element library (react-navigation-shared-element) or
// react-native-reanimated `SharedTransition` — 320ms ease-out expand

// Lightbox: react-native-gesture-handler pinch + pan; swipe-down dismiss with rubber-band;
// chrome dims to rgba(0,0,0,0.55) via Animated backdrop

// New justified rows
import Animated, { FadeIn } from 'react-native-reanimated';
// wrap each row: <Animated.View entering={FadeIn.duration(200)}>

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // fave
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); // add to album
Haptics.selectionAsync();                                // tab change
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Feed (tab) | `home-outline` / `home` |
| Search (tab) | `search` |
| Camera (tab, center) | `camera` (tint `#FF0084`) |
| Notify (tab) | `notifications-outline` / `notifications` |
| You (tab) | `person-circle-outline` |
| Favorite (inactive) | `star-outline` |
| Favorite (active) | `star` (`#FF0084`) |
| Comment | `chatbubble-outline` |
| Share | `share-outline` |
| Add to album | `albums-outline` |
| Back | `chevron-back` |
| Overflow | `ellipsis-horizontal` |
| Download | `download-outline` |
| Pro badge | `star` (`#FFB200`) |
| Camera (nav) | `camera-outline` |

## 7. Platform Notes

- **Fonts**: Proza Libre, Inter, IBM Plex Mono are all SIL OFL — free to bundle via `expo-font`
- **Images**: use `expo-image` with `contentFit="cover"` for tiles and `"contain"` for the lightbox; enable disk caching for the photostream
- **Status bar**: `<StatusBar style="light" />` (Flickr defaults dark); switch to `"dark"` only in light mode
- **Safe area**: tab bar is `position: absolute` with a `BlurView` — add bottom safe-area padding to scroll content; the photo grid is full-bleed (ignore side insets), text content keeps 16pt insets
- **Dynamic Type**: set `allowFontScaling={false}` on EXIF values, fave-count overlays, and tab labels (layout-sensitive in the justified grid); allow scaling on titles/body/meta
- **Justified packing**: recompute `packRows` on `useWindowDimensions` width change (rotation / iPad split view); memoize per width
- **Dark mode**: use `useColorScheme()` to swap to `canvasLight` / `surfaceLight` / `textPrimaryLt`; **photos never dim** — only chrome inverts
- **Accessibility**: label tiles "Photo by {author}, {n} favorites"; the fave control as a toggle button with selected state; EXIF rows "{key}: {value}"; expose Camera/Lens "see more shot with this" as an accessibility action
- **Reduce Motion**: gate the fave spring and grid->detail shared transition behind `AccessibilityInfo.isReduceMotionEnabled()` — fall back to crossfades
