# Crunchyroll (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Crunchyroll's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. Crunchyroll is **dark-only** — there is no light theme.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark-only)
  canvas:    '#000000',  // true black OLED
  surface1:  '#16171A',  // sheets / modals
  surface2:  '#23252B',  // cards / press / search field
  surface3:  '#2E3035',  // progress track / chips
  divider:   '#2A2C31',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#A0A0A0',
  textTertiary:  '#6A6C70',
  textOnScrim:   '#D8D8D8',

  // Brand & status
  orange:        '#F47521',  // the ONLY accent
  orangePressed: '#D8610F',
  orangeTint:    '#FF8C42',
  premiumGold:   '#FFC107',  // Premium badge only
  premiumInk:    '#1A1304',  // text on gold
  simulcast:     '#2BB673',
  newBlue:       '#2A9DF4',
  error:         '#E03E3E',
} as const;

export type CRColor = keyof typeof colors;

// Hero-to-black scrim — pass to expo-linear-gradient
export const heroScrim = {
  colors: ['rgba(0,0,0,0)', 'rgba(0,0,0,0.55)', '#000000'] as const,
  locations: [0, 0.55, 1] as const,
};

// Content-badge palette
export const badgeStyles = {
  simulcast: { bg: colors.simulcast,   fg: '#FFFFFF', label: 'SIMULCAST' },
  premium:   { bg: colors.premiumGold, fg: colors.premiumInk, label: 'PREMIUM' },
  new:       { bg: colors.newBlue,     fg: '#FFFFFF', label: 'NEW EPISODE' },
} as const;
```

## 2. Typography

Load **Lato** via `expo-font`, leaning on the 900 (Black) weight for titles, CTAs, and badges.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Lato-Regular': require('../assets/fonts/Lato-Regular.ttf'),
    'Lato-Bold':    require('../assets/fonts/Lato-Bold.ttf'),
    'Lato-Black':   require('../assets/fonts/Lato-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ headerShown: false }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const white = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  screenTitle:   { ...white, fontFamily: 'Lato-Black',   fontSize: 32, lineHeight: 35, letterSpacing: -0.5 },
  heroTitle:     { ...white, fontFamily: 'Lato-Black',   fontSize: 28, lineHeight: 30, letterSpacing: -0.5 },
  sectionHeader: { ...white, fontFamily: 'Lato-Black',   fontSize: 22, lineHeight: 25, letterSpacing: -0.2 },
  rowHeader:     { ...white, fontFamily: 'Lato-Bold',    fontSize: 18, lineHeight: 22 },
  body:          { ...white, fontFamily: 'Lato-Regular', fontSize: 16, lineHeight: 24 },
  episodeTitle:  { ...white, fontFamily: 'Lato-Bold',    fontSize: 15, lineHeight: 20 },
  synopsis:      { color: '#A0A0A0', fontFamily: 'Lato-Regular', fontSize: 13, lineHeight: 18 },
  meta:          { color: '#A0A0A0', fontFamily: 'Lato-Regular', fontSize: 14, lineHeight: 20 },
  buttonLabel:   { ...white, fontFamily: 'Lato-Black',   fontSize: 16, lineHeight: 16, letterSpacing: 0.3 },
  segmented:     { fontFamily: 'Lato-Black', fontSize: 14, lineHeight: 14 },
  badge:         { fontFamily: 'Lato-Black', fontSize: 11, lineHeight: 11, letterSpacing: 0.4 },
  tab:           { fontFamily: 'Lato-Bold',  fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  caption:       { color: '#A0A0A0', fontFamily: 'Lato-Regular', fontSize: 12, lineHeight: 16 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Anime Detail Hero (full-bleed key-art + scrim)

```tsx
// components/AnimeHero.tsx
import { Image, Text, View, useWindowDimensions } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, heroScrim } from '../theme/colors';
import { typography } from '../theme/typography';
import { ContentBadge } from './ContentBadge';

export function AnimeHero({
  keyArtUri, title, meta, isSimulcast, isPremium,
}: {
  keyArtUri: string; title: string; meta: string;
  isSimulcast?: boolean; isPremium?: boolean;
}) {
  const { height } = useWindowDimensions();
  return (
    <View style={{ height: height * 0.6 }}>
      <Image source={{ uri: keyArtUri }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
      <LinearGradient
        colors={heroScrim.colors}
        locations={heroScrim.locations}
        style={{ position: 'absolute', left: 0, right: 0, bottom: 0, top: 0 }}
      />
      <View style={{ position: 'absolute', left: 20, right: 20, bottom: 18, gap: 8 }}>
        <View style={{ flexDirection: 'row', gap: 8 }}>
          {isSimulcast ? <ContentBadge kind="simulcast" /> : null}
          {isPremium ? <ContentBadge kind="premium" /> : null}
        </View>
        <Text style={typography.heroTitle} numberOfLines={2}>{title}</Text>
        <Text style={[typography.meta, { color: colors.textOnScrim }]}>{meta}</Text>
      </View>
    </View>
  );
}
```

### Primary CTA ("Start Watching" / "Resume")

```tsx
// components/WatchButton.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WatchButton({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        height: 52, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
        flexDirection: 'row', gap: 10,
        backgroundColor: pressed ? colors.orangePressed : colors.orange,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Ionicons name="play" size={18} color="#FFFFFF" />
      <Text style={typography.buttonLabel}>{label}</Text>
    </Pressable>
  );
}
```

### Episode Row (resume-aware)

```tsx
// components/EpisodeRow.tsx
import { Image, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function EpisodeRow({
  number, stillUri, title, synopsis, progress,
}: {
  number: string; stillUri: string; title: string; synopsis: string; progress: number;
}) {
  return (
    <View style={{
      flexDirection: 'row', gap: 12, paddingVertical: 12,
      borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      <View style={{ width: 112, height: 64, borderRadius: 6, overflow: 'hidden', backgroundColor: colors.surface2 }}>
        <Image source={{ uri: stillUri }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
        <View style={{
          position: 'absolute', top: 4, left: 4,
          backgroundColor: 'rgba(0,0,0,0.7)', borderRadius: 3,
          paddingVertical: 2, paddingHorizontal: 6,
        }}>
          <Text style={[typography.badge, { color: '#FFFFFF' }]}>{number}</Text>
        </View>
        {progress > 0 ? (
          <View style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: 3, backgroundColor: 'rgba(255,255,255,0.25)' }}>
            <View style={{ width: `${progress * 100}%`, height: '100%', backgroundColor: colors.orange }} />
          </View>
        ) : null}
      </View>

      <View style={{ flex: 1, gap: 3 }}>
        <Text style={typography.episodeTitle}>{title}</Text>
        <Text style={typography.synopsis} numberOfLines={2}>{synopsis}</Text>
      </View>
    </View>
  );
}
```

### Content Badge

```tsx
// components/ContentBadge.tsx
import { Text, View } from 'react-native';
import { badgeStyles } from '../theme/colors';
import { typography } from '../theme/typography';

export function ContentBadge({ kind }: { kind: keyof typeof badgeStyles }) {
  const s = badgeStyles[kind];
  return (
    <View style={{ backgroundColor: s.bg, borderRadius: 4, paddingVertical: 4, paddingHorizontal: 8 }}>
      <Text style={[typography.badge, { color: s.fg }]}>{s.label}</Text>
    </View>
  );
}
```

### Segmented Control (sliding orange underline)

```tsx
// components/CRSegmented.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { useState } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CRSegmented({ items, onChange }: { items: string[]; onChange: (i: number) => void }) {
  const [sel, setSel] = useState(0);
  const [widths, setWidths] = useState<number[]>([]);
  const [xs, setXs] = useState<number[]>([]);

  const barStyle = useAnimatedStyle(() => ({
    width: withTiming(widths[sel] ?? 0, { duration: 220 }),
    transform: [{ translateX: withTiming(xs[sel] ?? 0, { duration: 220 }) }],
  }));

  return (
    <View style={{ borderBottomWidth: 1, borderBottomColor: colors.divider }}>
      <View style={{ flexDirection: 'row', gap: 24, paddingBottom: 10 }}>
        {items.map((it, i) => (
          <Pressable
            key={it}
            onLayout={(e) => {
              const { x, width } = e.nativeEvent.layout;
              setXs((p) => { const n = [...p]; n[i] = x; return n; });
              setWidths((p) => { const n = [...p]; n[i] = width; return n; });
            }}
            onPress={() => { setSel(i); onChange(i); }}
          >
            <Text style={[typography.segmented, { color: i === sel ? colors.textPrimary : colors.textSecondary }]}>
              {it}
            </Text>
          </Pressable>
        ))}
      </View>
      <Animated.View style={[{ position: 'absolute', bottom: -1, height: 3, borderRadius: 2, backgroundColor: colors.orange }, barStyle]} />
    </View>
  );
}
```

### Sub | Dub Control

```tsx
// components/SubDubControl.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';

export function SubDubControl({ isDub, onToggle }: { isDub: boolean; onToggle: (d: boolean) => void }) {
  return (
    <View style={{ flexDirection: 'row' }}>
      {(['SUB', 'DUB'] as const).map((label) => {
        const active = (label === 'DUB') === isDub;
        return (
          <Pressable
            key={label}
            onPress={() => onToggle(label === 'DUB')}
            style={{
              paddingVertical: 6, paddingHorizontal: 14, borderRadius: 6,
              backgroundColor: active ? colors.orange : 'transparent',
              borderWidth: active ? 0 : 1, borderColor: 'rgba(255,255,255,0.5)',
            }}
          >
            <Text style={{ fontFamily: 'Lato-Black', fontSize: 12, color: '#FFFFFF' }}>{label}</Text>
          </Pressable>
        );
      })}
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
        tabBarActiveTintColor: colors.orange,        // the only accent
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(0,0,0,0.95)',
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Lato-Bold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Home',      tabBarIcon: ({ color }) => <Ionicons name="home"          size={22} color={color} /> }} />
      <Tabs.Screen name="browse"    options={{ title: 'Browse',    tabBarIcon: ({ color }) => <Ionicons name="grid"          size={22} color={color} /> }} />
      <Tabs.Screen name="watchlist" options={{ title: 'Watchlist', tabBarIcon: ({ color }) => <Ionicons name="add"           size={22} color={color} /> }} />
      <Tabs.Screen name="manga"     options={{ title: 'Manga',     tabBarIcon: ({ color }) => <Ionicons name="book"          size={22} color={color} /> }} />
      <Tabs.Screen name="profile"   options={{ title: 'Profile',   tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import { withTiming, withSpring } from 'react-native-reanimated';

// Segmented underline slide — withTiming(target, { duration: 220 })

// Card press — scale to 0.97
// style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.97 : 1 }] })}

// "Lights down" — fade detail to black before player
// opacity.value = withTiming(0, { duration: 250 }) then navigate to player

// Progress fill on mount — animate width 0 → watched
// width.value = withTiming(watched, { duration: 400 })

// Hero parallax — on scroll, key-art translateY = scrollY * 0.5 (useAnimatedScrollHandler)

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);   // watchlist toggle, segment change
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);  // Start Watching
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Crunchyroll's iconography is simple and stroked.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Browse | `grid` |
| Watchlist | `add` |
| Manga | `book` |
| Profile | `person-circle` |
| Play (CTA) | `play` |
| Add to Watchlist | `add` / `checkmark` (saved) |
| Rate | `thumbs-up-outline` |
| Share | `share-social-outline` |
| Comments | `chatbubble-outline` |
| Cast | `tv-outline` |
| Back | `chevron-back` |
| Search | `search` |
| Notifications | `notifications-outline` |
| Premium lock | `lock-closed` |
| Download | `download-outline` |

## 7. Platform Notes

- **Font choice**: Lato is SIL OFL — free to bundle. Ship Regular / Bold / Black; the 900 Black weight is mandatory for hero titles, CTAs, and badges
- **Dark-only**: do **not** read `useColorScheme()` to swap themes — Crunchyroll has no light mode. Set `<StatusBar style="light" />` permanently and lock the navigation/theme to dark
- **Status bar**: always `style="light"` (white glyphs) since the canvas is true black and the hero art is dark at the top
- **Safe area**: wrap screens in `SafeAreaView`; the detail-screen top controls (back / share / cast) float over the key-art and must clear the Dynamic Island — use `react-native-safe-area-context` insets
- **Full-bleed hero**: render the key-art `Image` edge-to-edge (extend under the status bar); keep title/meta inside the bottom safe area
- **Dynamic Type**: `<Text>` respects system scale — set `allowFontScaling={false}` on badges, tab labels, segmented items, and the episode-number chip so layout-sensitive chrome stays fixed
- **Accessibility**: give the episode row an `accessibilityLabel` like `Episode 1, The Blade Awakens, 72% watched`; expose progress via `accessibilityValue={{ now: pct }}`
- **Single-accent discipline**: keep `colors.orange` as the only `tabBarActiveTintColor` / CTA / progress color; never introduce a second accent in components
- **Player**: lock the video player to landscape and full-screen; precede it with the 250ms fade-to-black ("lights down") for the cinematic transition
