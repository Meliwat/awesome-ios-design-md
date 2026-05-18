# Pluto TV (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Pluto TV's visual language into paste-ready Expo / React Native code: a design-token module, the EPG grid, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark-only)
  canvas:   '#0B0F1F',
  surface1: '#141A2E',
  surface2: '#1C2440',
  surface3: '#25304F',
  divider:  '#2A3556',

  // Brand
  blue:        '#0048FF',
  bluePressed: '#0039CC',
  blueBright:  '#2C6BFF',
  yellow:      '#FFE100',
  cyan:        '#00C2FF',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#9DA8C7',
  textTertiary:  '#6B7595',
  onYellow:      '#0B0F1F',

  // Semantic
  live:    '#FF3B5C',
  success: '#21D07A',
  error:   '#FF4A6E',
} as const;

export type PlutoColor = keyof typeof colors;

// EPG layout constants
export const epg = {
  slotWidth: 96,    // px width of a 30-minute slot
  channelColW: 86,
  rowH: 64,
} as const;
```

## 2. Typography

Pluto's brand sans is a semi-rounded humanist grotesque; **Manrope** is the closest free analog (SIL OFL). Load via `expo-font`. Use `fontVariant: ['tabular-nums']` on channel numbers and times.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Manrope-Regular':   require('../assets/fonts/Manrope-Regular.ttf'),
    'Manrope-Medium':    require('../assets/fonts/Manrope-Medium.ttf'),
    'Manrope-SemiBold':  require('../assets/fonts/Manrope-SemiBold.ttf'),
    'Manrope-Bold':      require('../assets/fonts/Manrope-Bold.ttf'),
    'Manrope-ExtraBold': require('../assets/fonts/Manrope-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const white = { color: '#FFFFFF' } satisfies TextStyle;
const tabular = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  display:    { ...white, fontFamily: 'Manrope-ExtraBold', fontSize: 32, lineHeight: 36, letterSpacing: -0.5 },
  screenTitle:{ ...white, fontFamily: 'Manrope-ExtraBold', fontSize: 24, lineHeight: 28, letterSpacing: -0.3 },
  section:    { ...white, fontFamily: 'Manrope-Bold',      fontSize: 20, lineHeight: 24, letterSpacing: -0.2 },
  nowPlaying: { ...white, fontFamily: 'Manrope-Bold',      fontSize: 17, lineHeight: 21, letterSpacing: -0.2 },
  body:       { ...white, fontFamily: 'Manrope-Regular',   fontSize: 15, lineHeight: 23 },
  channel:    { ...white, fontFamily: 'Manrope-Bold',      fontSize: 13, lineHeight: 16 },
  program:    { ...white, fontFamily: 'Manrope-SemiBold',  fontSize: 13, lineHeight: 16 },
  meta:       { color: '#6B7595', fontFamily: 'Manrope-Medium', fontSize: 12, lineHeight: 16 },
  chNumber:   { color: '#6B7595', fontFamily: 'Manrope-ExtraBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.2, ...tabular },
  timeLabel:  { color: '#6B7595', fontFamily: 'Manrope-Bold', fontSize: 10, lineHeight: 10, ...tabular },
  badge:      { ...white, fontFamily: 'Manrope-ExtraBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.5 },
  tab:        { color: '#6B7595', fontFamily: 'Manrope-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### EPG Grid (the centerpiece)

Synchronize one horizontal scroll position across the time-bar and every channel row so the frozen channel column / time-bar behavior holds.

```tsx
// components/EpgGuide.tsx
import { useRef } from 'react';
import { Animated, ScrollView, Text, View, Image } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, epg } from '../theme/colors';
import { typography } from '../theme/typography';

type Program = { id: string; title: string; timeLabel: string; widthSlots: number; isOnNow: boolean };
type Channel = { id: string; number: string; abbrev: string; gradient: [string, string]; programs: Program[] };

export function EpgGuide({ timeLabels, channels }: { timeLabels: string[]; channels: Channel[] }) {
  const x = useRef(new Animated.Value(0)).current;
  const onScroll = Animated.event([{ nativeEvent: { contentOffset: { x } } }], { useNativeDriver: true });

  return (
    <ScrollView style={{ flex: 1, backgroundColor: colors.canvas }} showsVerticalScrollIndicator={false}>
      {/* Sticky time-bar */}
      <View style={{ flexDirection: 'row', borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
        <View style={{ width: epg.channelColW }} />
        <Animated.ScrollView
          horizontal
          showsHorizontalScrollIndicator={false}
          scrollEventThrottle={16}
          onScroll={onScroll}
        >
          {timeLabels.map((t) => (
            <Text key={t} style={[typography.timeLabel, { width: epg.slotWidth, paddingVertical: 6, paddingLeft: 8 }]}>
              {t}
            </Text>
          ))}
        </Animated.ScrollView>
      </View>

      {channels.map((ch) => (
        <EpgRow key={ch.id} channel={ch} sharedX={x} />
      ))}
    </ScrollView>
  );
}

function EpgRow({ channel, sharedX }: { channel: Channel; sharedX: Animated.Value }) {
  return (
    <View style={{ flexDirection: 'row', height: epg.rowH, borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      {/* Frozen channel column */}
      <View style={{ width: epg.channelColW, backgroundColor: colors.surface1, alignItems: 'center',
        justifyContent: 'center', gap: 3, borderRightWidth: 0.5, borderRightColor: colors.divider }}>
        <Text style={typography.chNumber}>{channel.number}</Text>
        <LinearGradient colors={channel.gradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ width: 40, height: 28, borderRadius: 5, alignItems: 'center', justifyContent: 'center' }}>
          <Text style={{ fontFamily: 'Manrope-ExtraBold', fontSize: 9, color: '#FFF' }}>{channel.abbrev}</Text>
        </LinearGradient>
      </View>

      {/* Program timeline — synced offset */}
      <Animated.ScrollView
        horizontal
        showsHorizontalScrollIndicator={false}
        scrollEventThrottle={16}
        contentOffset={{ x: 0, y: 0 }}
      >
        <View style={{ flexDirection: 'row' }}>
          {channel.programs.map((p) => (
            <View
              key={p.id}
              style={{
                width: epg.slotWidth * p.widthSlots, height: epg.rowH,
                paddingHorizontal: 10, paddingVertical: 8, justifyContent: 'center',
                backgroundColor: p.isOnNow ? colors.surface2 : colors.surface1,
                borderRightWidth: 1, borderRightColor: colors.canvas,
                borderLeftWidth: p.isOnNow ? 3 : 0, borderLeftColor: colors.yellow,
              }}
            >
              <Text numberOfLines={1} style={[typography.program,
                { color: p.isOnNow ? colors.textPrimary : 'rgba(255,255,255,0.85)' }]}>
                {p.title}
              </Text>
              <Text style={typography.meta}>{p.timeLabel}</Text>
            </View>
          ))}
        </View>
      </Animated.ScrollView>
    </View>
  );
}
```

> For perfectly-locked scrolling across all rows on production, drive every row's horizontal `ScrollView` from the shared `sharedX` via `scrollTo` in an effect, or render the whole timeline as one wide `FlatList` row inside a shared horizontal scroller.

### Mini-Player (pinned top)

```tsx
// components/MiniPlayer.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';

export function MiniPlayer({
  VideoLayer, channelLine, programTitle, elapsed,
}: {
  VideoLayer: React.ReactNode; channelLine: string; programTitle: string; elapsed: number;
}) {
  return (
    <View style={{ height: 150, overflow: 'hidden' }}>
      {VideoLayer}
      <LinearGradient colors={['transparent', 'rgba(0,0,0,0.55)']} start={{ x: 0, y: 0.4 }} end={{ x: 0, y: 1 }}
        style={{ position: 'absolute', inset: 0, padding: 14, justifyContent: 'space-between' }}>
        <View style={{ flexDirection: 'row' }}>
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, backgroundColor: 'rgba(0,0,0,0.4)',
            paddingHorizontal: 9, paddingVertical: 4, borderRadius: 5 }}>
            <View style={{ width: 7, height: 7, borderRadius: 4, backgroundColor: colors.live }} />
            <Text style={{ fontFamily: 'Manrope-ExtraBold', fontSize: 10, color: '#FFF', letterSpacing: 0.5 }}>LIVE</Text>
          </View>
        </View>
        <View>
          <Text style={{ fontFamily: 'Manrope-Bold', fontSize: 11, color: colors.cyan }}>{channelLine}</Text>
          <Text style={{ fontFamily: 'Manrope-ExtraBold', fontSize: 18, color: '#FFF' }}>{programTitle}</Text>
          <View style={{ height: 3, borderRadius: 2, backgroundColor: 'rgba(255,255,255,0.2)', marginTop: 8 }}>
            <View style={{ height: 3, borderRadius: 2, width: `${elapsed * 100}%`, backgroundColor: colors.yellow }} />
          </View>
        </View>
      </LinearGradient>
    </View>
  );
}
```

### Category Pill Strip

```tsx
// components/CategoryPills.tsx
import { Pressable, ScrollView, Text } from 'react-native';
import { colors } from '../theme/colors';

export function CategoryPills({
  categories, selected, onSelect,
}: { categories: string[]; selected: string; onSelect: (c: string) => void }) {
  return (
    <ScrollView horizontal showsHorizontalScrollIndicator={false}
      contentContainerStyle={{ paddingHorizontal: 16, gap: 8 }}>
      {categories.map((c) => {
        const active = c === selected;
        return (
          <Pressable
            key={c}
            onPress={() => onSelect(c)}
            style={{ paddingHorizontal: 12, paddingVertical: 6, borderRadius: 999,
              backgroundColor: active ? colors.blue : colors.surface2 }}
          >
            <Text style={{ fontFamily: 'Manrope-Bold', fontSize: 11,
              color: active ? '#FFF' : colors.textSecondary }}>{c}</Text>
          </Pressable>
        );
      })}
    </ScrollView>
  );
}
```

### Now-Playing Bar

```tsx
// components/NowPlayingBar.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';

export function NowPlayingBar({ title, channelLine, gradient }: { title: string; channelLine: string; gradient: [string, string] }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12,
      backgroundColor: colors.surface2, borderRadius: 12, padding: 12 }}>
      <LinearGradient colors={gradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ width: 56, height: 38, borderRadius: 6 }} />
      <View style={{ flex: 1 }}>
        <Text style={{ fontFamily: 'Manrope-Bold', fontSize: 13, color: '#FFF' }}>{title}</Text>
        <Text style={{ fontFamily: 'Manrope-Medium', fontSize: 11, color: colors.textSecondary }}>{channelLine}</Text>
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5 }}>
        <View style={{ width: 7, height: 7, borderRadius: 4, backgroundColor: colors.live }} />
        <Text style={{ fontFamily: 'Manrope-ExtraBold', fontSize: 10, color: colors.live, letterSpacing: 0.4 }}>LIVE</Text>
      </View>
    </View>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.yellow,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(11,15,31,0.96)',
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Manrope-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="live"     options={{ title: 'Live TV',   tabBarIcon: ({ color }) => <Ionicons name="tv" size={22} color={color} /> }} />
      <Tabs.Screen name="ondemand" options={{ title: 'On Demand', tabBarIcon: ({ color }) => <Ionicons name="play-circle" size={22} color={color} /> }} />
      <Tabs.Screen name="search"   options={{ title: 'Search',    tabBarIcon: ({ color }) => <Ionicons name="search" size={22} color={color} /> }} />
      <Tabs.Screen name="mypluto"  options={{ title: 'My Pluto',  tabBarIcon: ({ color }) => <Ionicons name="bookmark" size={22} color={color} /> }} />
      <Tabs.Screen name="account"  options={{ title: 'Account',   tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import * as Haptics from 'expo-haptics';

// Channel surf: cross-fade mini-player video on tune-in (~300ms)
videoOpacity.value = withTiming(0, { duration: 150 }, () => {
  // swap source on JS thread, then
  videoOpacity.value = withTiming(1, { duration: 300 });
});

// Time-bar / timeline sync: share one horizontal offset Animated.Value across time-bar + rows

// Mini-player elapsed bar — real time; reset+animate on channel change
elapsed.value = withTiming(currentElapsed, { duration: 300 });

// Category filter swap
rowsOpacity.value = withTiming(0, { duration: 100 }, () => {
  rowsOpacity.value = withTiming(1, { duration: 200 });
});

// Set Reminder confirm
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
// animate icon Bell → Check with a spring scale 1 → 1.2 → 1

// Optional On Now border breathing
opacity.value = withRepeat(withTiming(0.85, { duration: 2000 }), -1, true);
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons primary).

| Purpose | Ionicons |
|---------|----------|
| Live TV | `tv` / `tv-outline` |
| On Demand | `play-circle` / `play-circle-outline` |
| Search | `search` |
| My Pluto | `bookmark` / `bookmark-outline` |
| Account | `person-circle` / `person-circle-outline` |
| Play (CTA) | `play` |
| Live dot | `ellipse` (tinted `#FF3B5C`) |
| Add to watchlist | `add` → `checkmark` |
| Set reminder | `notifications-outline` → `notifications` |
| Share | `share-outline` |
| Back | `chevron-back` |
| Fullscreen | `expand` |
| Cast | `tv-outline` (or `react-native-google-cast` button) |
| Full schedule | `calendar-outline` |
| Mute / volume | `volume-mute` / `volume-high` |

## 7. Platform Notes

- **Dark-only**: lock the app to dark — set `userInterfaceStyle: 'dark'` in `app.json`; never derive a light palette. `<StatusBar style="light" />` everywhere.
- **Font choice**: Manrope is SIL OFL — free to bundle. Ship Regular→ExtraBold; heavy weights carry channel names and titles.
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on channel numbers and EPG times — otherwise the frozen channel column and the time-bar drift out of alignment with the rows.
- **EPG scroll sync**: the hardest part. Bind every channel row's horizontal scroll and the time-bar to one shared offset (Animated.Value + `scrollTo`, or a single wide virtualized list). The vertical channel list should be a `FlatList` for long channel counts.
- **Safe area**: wrap in `SafeAreaView`; the mini-player video should bleed under the status bar; the tab bar needs bottom safe-area padding; the sticky time-bar sits below the mini-player, never under the notch.
- **Performance**: hundreds of channels — virtualize the vertical list (`FlatList` with `windowSize`, `removeClippedSubviews`); memoize rows and program cells.
- **Dynamic Type**: set `allowFontScaling={false}` on EPG cell text, channel numbers, time-bar labels, badges, and tab labels (the grid is layout-locked); leave it on for titles/body.
- **Accessibility**: the On Now cell's `accessibilityLabel` must include "on now, live" (the yellow border is meaningful, not decorative) and be paired with the LIVE text so it isn't color-only; mini-player announces "Live: {program} on {channel}".
- **Reduce Motion**: gate the optional border pulse and the elapsed-bar animation behind `AccessibilityInfo.isReduceMotionEnabled`; category swap becomes an instant cut.
- **Reduce Transparency**: when enabled, replace the translucent tab bar with solid `#0B0F1F`.
