# Audible (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Audible's visual language into paste-ready Expo / React Native code: a design-token module, the serif/sans pairing, the signature speed-dial + 30s-skip player, the cover progress ring, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, `react-native-svg` (progress ring), and `@react-native-community/slider`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#1A1A1A',
  surface1: '#2A2A2A',
  surface2: '#343434',
  divider:  '#3A3A3A',

  textPrimary:   '#FFFFFF',
  textSecondary: '#B0B0B0',
  textTertiary:  '#6E6E6E',

  orange:        '#FF9900',
  orangePressed: '#E68A00',
  orangeGlow:    'rgba(255,153,0,0.28)',
  errorRed:      '#E5484D',
} as const;

export type AudColor = keyof typeof colors;
```

## 2. Typography

Two families, strict roles — **Playfair Display** for headings/titles only, **Inter** for body/UI. Both on Google Fonts; load via `expo-font`. Fall back to serif/system.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'PlayfairDisplay-Bold': require('../assets/fonts/PlayfairDisplay-Bold.ttf'),
    'Inter-Regular':        require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold':       require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':           require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ contentStyle: { backgroundColor: '#1A1A1A' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const w = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  // Headings — Playfair Display (serif) ONLY
  titleLarge: { ...w, fontFamily: 'PlayfairDisplay-Bold', fontSize: 30, lineHeight: 35 },
  bookTitle:  { ...w, fontFamily: 'PlayfairDisplay-Bold', fontSize: 26, lineHeight: 31 },
  section:    { ...w, fontFamily: 'PlayfairDisplay-Bold', fontSize: 22, lineHeight: 26 },
  cardTitle:  { ...w, fontFamily: 'PlayfairDisplay-Bold', fontSize: 17, lineHeight: 22 },
  miniTitle:  { ...w, fontFamily: 'PlayfairDisplay-Bold', fontSize: 14, lineHeight: 18 },

  // Body / UI — Inter (sans)
  author:     {        fontFamily: 'Inter-Regular',  fontSize: 14, lineHeight: 18, color: '#B0B0B0' },
  narrator:   {        fontFamily: 'Inter-SemiBold', fontSize: 13, lineHeight: 17, letterSpacing: 0.2, color: '#B0B0B0' },
  chapter:    { ...w,  fontFamily: 'Inter-SemiBold', fontSize: 16, lineHeight: 21 },
  body:       { ...w,  fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 23 },
  captions:   { ...w,  fontFamily: 'Inter-Regular',  fontSize: 18, lineHeight: 29 },
  meta:       {        fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 17, color: '#B0B0B0' },
  labelUpper: { ...w,  fontFamily: 'Inter-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.8, textTransform: 'uppercase' as const },
  button:     { color: '#1A1A1A', fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 20, letterSpacing: 0.2 },
  buttonSec:  { ...w,  fontFamily: 'Inter-SemiBold', fontSize: 14, lineHeight: 18 },
  speed:      { fontFamily: 'Inter-Bold', fontSize: 15, lineHeight: 18, fontVariant: ['tabular-nums'] as const, color: '#FF9900' },
  tab:        {        fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Cover with Orange Progress Ring

```tsx
// components/CoverRing.tsx
import { View, Image } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import { colors } from '../theme/colors';

export function CoverRing({ uri, progress, size = 280, ring = 4 }: {
  uri: string; progress: number; size?: number; ring?: number;
}) {
  const r = size / 2 - ring / 2;
  const c = 2 * Math.PI * r;
  const inner = size - ring * 6;
  return (
    <View style={{ width: size, height: size, alignItems: 'center', justifyContent: 'center' }}>
      <Svg width={size} height={size} style={{ position: 'absolute' }}>
        <Circle cx={size / 2} cy={size / 2} r={r} stroke={colors.divider} strokeWidth={ring} fill="none" />
        <Circle
          cx={size / 2} cy={size / 2} r={r}
          stroke={colors.orange} strokeWidth={ring} fill="none" strokeLinecap="round"
          strokeDasharray={c} strokeDashoffset={c * (1 - progress)}
          transform={`rotate(-90 ${size / 2} ${size / 2})`}
        />
      </Svg>
      <Image source={{ uri }} style={{
        width: inner, height: inner, borderRadius: 10,
        shadowColor: '#000', shadowOpacity: 0.5, shadowRadius: 36, shadowOffset: { width: 0, height: 14 },
      }} />
    </View>
  );
}
```

### Speed-Dial + 30s-Skip Transport (Signature)

```tsx
// components/Transport.tsx
import { Pressable, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withSequence, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function PlayButton({ isPlaying, onPress, size = 72 }: { isPlaying: boolean; onPress: () => void; size?: number }) {
  const s = useSharedValue(1);
  const st = useAnimatedStyle(() => ({ transform: [{ scale: s.value }] }));
  return (
    <Pressable
      onPressIn={() => (s.value = withSpring(0.93, { damping: 14 }))}
      onPressOut={() => (s.value = withSpring(1, { damping: 14 }))}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
    >
      <Animated.View style={[{
        width: size, height: size, borderRadius: size / 2, backgroundColor: colors.orange,
        alignItems: 'center', justifyContent: 'center',
        shadowColor: '#FF9900', shadowOpacity: 0.28, shadowRadius: 22, shadowOffset: { width: 0, height: 6 },
      }, st]}>
        <Ionicons name={isPlaying ? 'pause' : 'play'} size={size * 0.44} color={colors.canvas} />
      </Animated.View>
    </Pressable>
  );
}

export function SkipButton({ dir, onPress }: { dir: 'back' | 'forward'; onPress: () => void }) {
  const flash = useSharedValue(0);
  const st = useAnimatedStyle(() => ({ opacity: 1 }));
  const color = useSharedValue(0);
  return (
    <Pressable
      onPress={() => {
        flash.value = withSequence(withTiming(1, { duration: 90 }), withTiming(0, { duration: 180 }));
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
        onPress();
      }}
    >
      {/* gobackward.30 / goforward.30 have no Ionicons 1:1 — compose icon + "30" label */}
      <View style={{ width: 44, height: 44, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name={dir === 'back' ? 'play-back' : 'play-forward'} size={26} color="#FFF" />
        <Animated.Text style={[{ position: 'absolute', bottom: 2, fontSize: 9, fontWeight: '700', color: '#FFF' }, st]}>
          30
        </Animated.Text>
      </View>
    </Pressable>
  );
}
```

### Speed Dial Sheet

```tsx
// components/SpeedDial.tsx
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';
import Slider from '@react-native-community/slider';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const PRESETS = [1.0, 1.25, 1.5, 2.0];

export function SpeedDial({ speed, onChange }: { speed: number; onChange: (s: number) => void }) {
  const [last, setLast] = useState(speed);
  return (
    <View style={{ backgroundColor: colors.surface1, borderTopLeftRadius: 16, borderTopRightRadius: 16, padding: 20, gap: 24 }}>
      <View style={{ width: 36, height: 4, borderRadius: 2, backgroundColor: colors.textTertiary, alignSelf: 'center' }} />
      <Text style={[typography.speed, { fontSize: 22, textAlign: 'center' }]}>{speed.toFixed(2).replace(/0$/, '')}×</Text>
      <Slider
        minimumValue={0.5} maximumValue={3.5} step={0.05} value={speed}
        minimumTrackTintColor={colors.orange} maximumTrackTintColor={colors.divider} thumbTintColor={colors.orange}
        onValueChange={(v) => {
          if (Math.round(v * 4) === v * 4 && Math.round(v * 4) !== Math.round(last * 4)) {
            Haptics.selectionAsync(); setLast(v);
          }
          onChange(v);
        }}
      />
      <View style={{ flexDirection: 'row', gap: 10, justifyContent: 'center' }}>
        {PRESETS.map(p => (
          <Pressable key={p} onPress={() => onChange(p)} style={{
            paddingVertical: 8, paddingHorizontal: 16, borderRadius: 8,
            backgroundColor: speed === p ? colors.orange : colors.surface2,
          }}>
            <Text style={[typography.speed, { color: speed === p ? colors.canvas : '#FFF' }]}>{p}×</Text>
          </Pressable>
        ))}
      </View>
    </View>
  );
}
```

### Continue-Listening Row

```tsx
// components/ContinueRow.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { CoverRing } from './CoverRing';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ContinueRow({ title, author, remaining, uri, progress }: {
  title: string; author: string; remaining: string; uri: string; progress: number;
}) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 14, padding: 14, borderRadius: 12, backgroundColor: colors.surface1 }}>
      <CoverRing uri={uri} progress={progress} size={72} ring={3} />
      <View style={{ flex: 1, gap: 3 }}>
        <Text style={typography.cardTitle} numberOfLines={1}>{title}</Text>
        <Text style={[typography.author, { fontSize: 13 }]} numberOfLines={1}>{author}</Text>
        <Text style={[typography.meta, { fontSize: 12 }]}>{remaining}</Text>
      </View>
      <View style={{ width: 56, height: 56, borderRadius: 28, backgroundColor: colors.orange, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="play" size={18} color={colors.canvas} />
      </View>
    </View>
  );
}
```

## 4. Captions (Synced Text)

```tsx
export function Captions({ line, activeWord }: { line: string; activeWord: number }) {
  const words = line.split(' ');
  return (
    <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 4, padding: 16, borderRadius: 12, backgroundColor: colors.surface1 }}>
      {words.map((wd, i) => (
        <Text key={i} style={[typography.captions, { color: i === activeWord ? '#FFF' : colors.textSecondary }]}>{wd}</Text>
      ))}
    </View>
  );
}
```

## 5. Tab Bar

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
        tabBarActiveTintColor: colors.orange,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => <BlurView intensity={90} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(26,26,26,0.96)' }} />,
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home"          size={24} color={color} /> }} />
      <Tabs.Screen name="library"  options={{ title: 'Library',  tabBarIcon: ({ color }) => <Ionicons name="library"       size={24} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="search"        size={24} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Play tap: scale 0.93 withSpring + Haptics.impactAsync(Medium)
// 30s skip: orange flash withSequence(90ms/180ms) + Haptics.impactAsync(Light); ring jumps 30s
// Speed dial: each .25 tick → Haptics.selectionAsync(); big number cross-fades on change
// Cover ring: animate strokeDashoffset linearly with playback (no easing)
// Chapter change: animate the orange leading bar position with withTiming(220)
// Mini-bar → Full player: push /player; shared cover via a shared progress value
// Captions: each word color transitions with a 180ms timing
```

## 7. Icon Library

Use `@expo/vector-icons`. Audible's `gobackward.30` / `goforward.30` have no Ionicons 1:1 — compose a skip glyph + a "30" label (shown above):

| Purpose | Ionicons |
|---------|----------|
| Play / Pause | `play` / `pause` |
| Skip back 30s | `play-back` + "30" label |
| Skip forward 30s | `play-forward` + "30" label |
| Sleep timer | `moon-outline` |
| Bookmark | `bookmark-outline` / `bookmark` |
| Chapters | `list` |
| Car mode | `car-outline` |
| Captions | `chatbox-outline` / `chatbox` |
| Finished check | `checkmark` |
| Share | `share-outline` |
| More | `ellipsis-horizontal` |
| Search | `search` |
| Home | `home-outline` / `home` |
| Library | `library-outline` / `library` |

## 8. Platform Notes

- **Warm-dark by default**: the player/library default to charcoal `#1A1A1A`; force it on those navigators — the warmth is core to the literary identity.
- **Status bar**: `<StatusBar style="light" />` globally from `expo-status-bar`.
- **Two-family discipline**: never apply `PlayfairDisplay-Bold` to body, metadata, or captions — serif is headings/titles only. Keep the serif at 17pt+ (its high stroke contrast thins at small sizes).
- **Progress ring**: `react-native-svg` `Circle` with `strokeDasharray`/`strokeDashoffset`; animate offset with Reanimated for the live sweep.
- **Slider detents**: `@react-native-community/slider` has no native detents — snap with `step={0.05}` and fire `Haptics.selectionAsync()` at .25 boundaries.
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; mini-bar above the home indicator.
- **Dynamic Type**: keep `allowFontScaling` ON for captions especially (readability while listening); set it OFF only on the speed value and tab labels.
- **Accessibility**: label skip buttons "Skip back/forward 30 seconds"; expose speed as adjustable; mirror ring progress in the cover's `accessibilityLabel`.
