# Plex (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Plex's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. Plex's media experience is **dark-first** on a cool charcoal canvas.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3. (No gradient needed — Plex is flat.)

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark-first, cool charcoal — NOT true black)
  canvas:    '#1F2326',
  surface1:  '#282C30',  // server pill / sheet
  surface2:  '#32373B',  // rows / search / ghost button
  surface3:  '#3C4146',  // selected row / progress track
  divider:   '#34393D',

  // Text
  textPrimary:   '#F2F3F4',
  textSecondary: '#9BA0A4',
  textTertiary:  '#6B7075',

  // Brand & status
  yellow:        '#E5A00D',  // the ONLY accent
  yellowPressed: '#C98A09',
  yellowInk:     '#1A1304',  // text on yellow — mandatory, never white
  online:        '#4CAF50',
  offline:       '#6B7075',
  error:         '#E5484D',
} as const;

export type PlexColor = keyof typeof colors;
```

## 2. Typography

Load **Inter** via `expo-font` — neutral, legible, functional.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':    require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':      require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold': require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ headerShown: false }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#F2F3F4' } satisfies TextStyle;

export const typography = {
  screenTitle:   { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  heroTitle:     { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  sectionHeader: { ...primary, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 26, letterSpacing: -0.2 },
  subsection:    { ...primary, fontFamily: 'Inter-Bold',      fontSize: 18, lineHeight: 23 },
  body:          { ...primary, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 24 },
  cardTitle:     { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 20 },
  meta:          { color: '#9BA0A4', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 20 },
  buttonLabel:   { color: '#1A1304', fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 16 },
  serverName:    { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 14, lineHeight: 18 },
  serverMeta:    { color: '#9BA0A4', fontFamily: 'Inter-Regular', fontSize: 12, lineHeight: 16 },
  tab:           { fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  techChip:      { color: '#9BA0A4', fontFamily: 'Inter-SemiBold', fontSize: 12, lineHeight: 16, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Server Picker Pill + Sheet Row

```tsx
// components/ServerPill.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ServerPill({
  name, isOnline, onPress,
}: { name: string; isOnline: boolean; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 8, alignSelf: 'flex-start',
        backgroundColor: colors.surface1,
        borderWidth: 1, borderColor: colors.divider, borderRadius: 999,
        paddingVertical: 7, paddingHorizontal: 14,
      }}
    >
      <View style={{ width: 8, height: 8, borderRadius: 4, backgroundColor: isOnline ? colors.online : colors.offline }} />
      <Text style={[typography.serverName, { fontSize: 13 }]}>{name}</Text>
      <Ionicons name="chevron-down" size={11} color={colors.textSecondary} />
    </Pressable>
  );
}
```

```tsx
// components/ServerSheetRow.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ServerSheetRow({
  name, meta, isOnline, isSelected, onPress,
}: { name: string; meta: string; isOnline: boolean; isSelected: boolean; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 12,
        paddingVertical: 13, paddingHorizontal: 14, borderRadius: 8,
        backgroundColor: isSelected ? colors.surface3 : colors.surface2,
      }}
    >
      <View style={{ width: 9, height: 9, borderRadius: 4.5, backgroundColor: isOnline ? colors.online : colors.offline }} />
      <View style={{ flex: 1 }}>
        <Text style={typography.serverName}>{name}</Text>
        <Text style={typography.serverMeta}>{meta}</Text>
      </View>
      {isSelected ? <Ionicons name="checkmark" size={16} color={colors.yellow} /> : null}
    </Pressable>
  );
}
```

### On Deck Tile (resume-aware)

```tsx
// components/OnDeckTile.tsx
import { Image, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function OnDeckTile({
  artUri, title, sub, progress,
}: { artUri: string; title: string; sub: string; progress: number }) {
  return (
    <View style={{ width: 220 }}>
      <View style={{ width: 220, height: 124, borderRadius: 6, overflow: 'hidden', backgroundColor: colors.surface2 }}>
        <Image source={{ uri: artUri }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
        <View style={{
          position: 'absolute', top: '50%', left: '50%',
          width: 42, height: 42, borderRadius: 21, marginLeft: -21, marginTop: -21,
          backgroundColor: 'rgba(0,0,0,0.55)', alignItems: 'center', justifyContent: 'center',
        }}>
          <Ionicons name="play" size={16} color="#FFFFFF" style={{ marginLeft: 2 }} />
        </View>
        <View style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: 3, backgroundColor: 'rgba(255,255,255,0.22)' }}>
          <View style={{ width: `${progress * 100}%`, height: '100%', backgroundColor: colors.yellow }} />
        </View>
      </View>
      <Text style={[typography.cardTitle, { marginTop: 8 }]} numberOfLines={1}>{title}</Text>
      <Text style={[typography.meta, { marginTop: 2 }]} numberOfLines={1}>{sub}</Text>
    </View>
  );
}
```

### Primary Play Button (dark-on-yellow)

```tsx
// components/PlayButton.tsx
import { Pressable, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PlayButton({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8,
        paddingVertical: 14, borderRadius: 8,
        backgroundColor: pressed ? colors.yellowPressed : colors.yellow,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Ionicons name="play" size={16} color={colors.yellowInk} />
      <Text style={typography.buttonLabel}>Play</Text>
    </Pressable>
  );
}
```

### Library Poster + Unwatched Dot

```tsx
// components/LibraryPoster.tsx
import { Image, View } from 'react-native';
import { colors } from '../theme/colors';

export function LibraryPoster({ artUri, unwatched }: { artUri: string; unwatched: boolean }) {
  return (
    <View style={{ aspectRatio: 2 / 3, borderRadius: 6, overflow: 'hidden', backgroundColor: colors.surface2 }}>
      <Image source={{ uri: artUri }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
      {unwatched ? (
        <View style={{
          position: 'absolute', top: 7, right: 7,
          width: 9, height: 9, borderRadius: 4.5, backgroundColor: colors.yellow,
          shadowColor: '#000', shadowOpacity: 0.5, shadowRadius: 1.5, shadowOffset: { width: 0, height: 1 },
          elevation: 2,
        }} />
      ) : null}
    </View>
  );
}
```

### Tech / Quality Chip

```tsx
// components/TechChip.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TechChip({ text }: { text: string }) {
  return (
    <View style={{ backgroundColor: colors.surface2, borderRadius: 4, paddingVertical: 4, paddingHorizontal: 8 }}>
      <Text style={typography.techChip}>{text}</Text>
    </View>
  );
}
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
        tabBarActiveTintColor: colors.yellow,        // the only accent
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(31,35,38,0.96)',
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home"          size={22} color={color} /> }} />
      <Tabs.Screen name="library"  options={{ title: 'Library',  tabBarIcon: ({ color }) => <Ionicons name="grid"          size={22} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="play-circle"   size={22} color={color} /> }} />
      <Tabs.Screen name="livetv"   options={{ title: 'Live TV',  tabBarIcon: ({ color }) => <Ionicons name="tv"            size={22} color={color} /> }} />
      <Tabs.Screen name="you"      options={{ title: 'You',      tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import { withTiming } from 'react-native-reanimated';

// Server switch — check fades in, sheet dismisses, content cross-fades
// check opacity: withTiming(1, { duration: 150 }); then close sheet + cross-fade Home (250ms)

// On Deck resume — tile press scale + progress fill on return
// style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.97 : 1 }] })}
// fillWidth.value = withTiming(watched, { duration: 350 })  // on focus

// Mark as Watched — unwatched dot fades out
// dotOpacity.value = withTiming(0, { duration: 200 })

// Status dot online — calm gray → green, no spring
// color cross-fade withTiming(..., { duration: 200 })

// Haptics — light only; Plex motion is deliberately quiet
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);  // server select, tile resume, mark watched
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Plex iconography is clean and functional.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Library | `grid` |
| Discover | `play-circle` |
| Live TV | `tv` |
| You | `person-circle` |
| Server chevron | `chevron-down` |
| Selected server | `checkmark` |
| Play (CTA / overlay) | `play` |
| Mark as Watched | `checkmark-circle-outline` |
| Add to Playlist | `add-circle-outline` |
| Search | `search` |
| Settings | `settings-outline` |
| Cast | `tv-outline` |
| Back | `chevron-back` |
| Download | `download-outline` |
| Online/Offline dot | (a `View` circle, not an icon) |

## 7. Platform Notes

- **Font choice**: Inter is SIL OFL — free to bundle. Ship Regular / Medium / SemiBold / Bold / ExtraBold; falls back to SF Pro 1:1
- **Dark-first media**: keep the library/browse experience on the cool charcoal canvas — do **not** swap to a pure-white light theme via `useColorScheme()`. Set `<StatusBar style="light" />` for the media surfaces
- **Not true black**: the canvas is `#1F2326` — never `#000000`; this is the Plex identity
- **Dark-on-yellow is mandatory**: every yellow fill uses `colors.yellowInk` (`#1A1304`) for its text/icon — never white. Centralize this so it can't drift
- **Safe area**: wrap screens in `SafeAreaView`; Plex keeps a clean top inset (server pill / nav) — do not extend art under the status bar
- **Server sheet**: use `@gorhom/bottom-sheet` or a `Modal` with detents; respect the bottom safe area and place the grabber inside it
- **Dynamic Type**: `<Text>` respects system scale — set `allowFontScaling={false}` on tab labels, tech chips, and the unwatched dot's container so layout-sensitive chrome stays fixed
- **Accessibility**: give the server pill `accessibilityLabel` "Server: {name}, {online/offline}"; On Deck tiles "{title}, {sub}, {pct}% watched" with `accessibilityValue`; status dots must be backed by the text label ("Online"/"Offline") so state isn't color-only
- **Single-accent discipline**: keep `colors.yellow` as the only `tabBarActiveTintColor` / Play / progress / unwatched-dot color; never introduce a second accent
- **Player**: lock the video player to landscape full-screen; pull-to-refresh rescans the current server with a subtle yellow spinner
