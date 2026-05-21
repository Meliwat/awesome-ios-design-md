# ESPN (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates ESPN's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. ESPN's scoreboard identity is **dark-first** on a near-black canvas, with **tabular numerals on every score**.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark-first, near-black — NOT pure black)
  canvas:    '#0E0F11',
  surface1:  '#18191B',  // game-card body
  surface2:  '#212327',  // ticker chip / card header
  surface3:  '#2B2D31',  // pressed / FINAL fill
  divider:   '#2A2C30',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#9AA0A6',
  textTertiary:  '#6B7075',

  // Brand & status
  red:        '#D50A0A',  // brand accent
  redBright:  '#CC0000',  // CTA / links / category tags
  redPressed: '#A50808',
  live:       '#FF1A1A',  // LIVE pill + pulse
  win:        '#1FAA59',  // winning team's score
  rankGold:   '#F2C200',
  error:      '#E5484D',
} as const;

export type ESPNColor = keyof typeof colors;
```

## 2. Typography

ESPN's brand face is a proprietary condensed sans; the closest free analog is **Archivo** (load via `expo-font`). Scores/clocks **must** be tabular — use `fontVariant: ['tabular-nums']`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Archivo-Regular':  require('../assets/fonts/Archivo-Regular.ttf'),
    'Archivo-Medium':   require('../assets/fonts/Archivo-Medium.ttf'),
    'Archivo-SemiBold': require('../assets/fonts/Archivo-SemiBold.ttf'),
    'Archivo-Bold':     require('../assets/fonts/Archivo-Bold.ttf'),
    'Archivo-Black':    require('../assets/fonts/Archivo-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ headerShown: false }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const white = { color: '#FFFFFF' } satisfies TextStyle;
const tabular = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  screenTitle:   { ...white, fontFamily: 'Archivo-Black',   fontSize: 32, lineHeight: 35, letterSpacing: -0.5 },
  headline:      { ...white, fontFamily: 'Archivo-Black',   fontSize: 26, lineHeight: 30, letterSpacing: -0.3 },
  sectionHeader: { ...white, fontFamily: 'Archivo-Bold',    fontSize: 22, lineHeight: 26, letterSpacing: -0.2 },
  teamName:      { ...white, fontFamily: 'Archivo-Bold',    fontSize: 18, lineHeight: 22 },
  body:          { ...white, fontFamily: 'Archivo-Regular', fontSize: 16, lineHeight: 24 },
  storyTitle:    { ...white, fontFamily: 'Archivo-Bold',    fontSize: 15, lineHeight: 20 },
  meta:          { color: '#9AA0A6', fontFamily: 'Archivo-Regular', fontSize: 14, lineHeight: 20 },
  record:        { color: '#9AA0A6', fontFamily: 'Archivo-Regular', fontSize: 12, lineHeight: 16 },
  buttonLabel:   { ...white, fontFamily: 'Archivo-Bold',    fontSize: 16, lineHeight: 16, letterSpacing: 0.3 },
  statusBadge:   { fontFamily: 'Archivo-Black', fontSize: 11, lineHeight: 11, letterSpacing: 0.5 },
  categoryTag:   { color: '#CC0000', fontFamily: 'Archivo-Black', fontSize: 10, lineHeight: 10, letterSpacing: 0.5 },
  tab:           { fontFamily: 'Archivo-Bold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  // Scores — tabular, non-negotiable
  scoreLarge:    { ...white, ...tabular, fontFamily: 'Archivo-Black', fontSize: 26, lineHeight: 28 },
  scoreTicker:   { ...white, ...tabular, fontFamily: 'Archivo-Bold',  fontSize: 13, lineHeight: 14 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Game Card (the core atom)

```tsx
// components/GameCard.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { StatusBadge } from './StatusBadge';

type Team = { logo: string; name: string; record: string; score: number; winning: boolean };

export function GameCard({
  league, isLive, isFinal, clock, home, away,
}: {
  league: string; isLive: boolean; isFinal: boolean; clock: string; home: Team; away: Team;
}) {
  const scoreColor = (t: Team) =>
    isFinal ? (t.winning ? colors.win : colors.textSecondary)
            : (t.winning ? colors.textPrimary : colors.textSecondary);

  const TeamRow = ({ t }: { t: Team }) => (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingVertical: 8 }}>
      <View style={{ width: 30, height: 30, borderRadius: 15, backgroundColor: t.logo }} />
      <View style={{ flex: 1 }}>
        <Text style={typography.teamName}>{t.name}</Text>
        <Text style={typography.record}>{t.record}</Text>
      </View>
      <Text style={[typography.scoreLarge, { color: scoreColor(t) }]}>{t.score}</Text>
    </View>
  );

  return (
    <View style={{
      backgroundColor: colors.surface1, borderRadius: 12,
      borderWidth: 1, borderColor: colors.divider, overflow: 'hidden', marginBottom: 16,
    }}>
      <View style={{
        flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
        paddingHorizontal: 14, paddingVertical: 10, backgroundColor: colors.surface2,
      }}>
        <Text style={[typography.statusBadge, { color: colors.textSecondary }]}>{league}</Text>
        <StatusBadge isLive={isLive} isFinal={isFinal} label={isLive ? 'LIVE' : isFinal ? 'FINAL' : clock} />
      </View>

      <View style={{ padding: 14 }}>
        <TeamRow t={home} />
        <View style={{ height: 1, backgroundColor: colors.divider }} />
        <TeamRow t={away} />
      </View>

      <View style={{
        flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
        paddingHorizontal: 14, paddingVertical: 10, borderTopWidth: 1, borderTopColor: colors.divider,
      }}>
        <Text style={[typography.body, { fontFamily: 'Archivo-Bold', fontSize: 13 }]}>{clock}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
          <Ionicons name="play" size={12} color={colors.redBright} />
          <Text style={{ fontFamily: 'Archivo-Bold', fontSize: 12, color: colors.redBright }}>Watch on ESPN+</Text>
        </View>
      </View>
    </View>
  );
}
```

### Scores Ticker

```tsx
// components/ScoresTicker.tsx
import { ScrollView, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { StatusBadge } from './StatusBadge';

type Side = { abbr: string; logo: string; score: string; win: boolean };
type Chip = { league: string; status: string; isLive: boolean; isFinal: boolean; a: Side; b: Side };

export function ScoresTicker({ chips }: { chips: Chip[] }) {
  const Line = ({ s }: { s: Side }) => (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <View style={{ width: 16, height: 16, borderRadius: 8, backgroundColor: s.logo }} />
        <Text style={[typography.scoreTicker, { color: s.win ? colors.textPrimary : colors.textSecondary }]}>{s.abbr}</Text>
      </View>
      <Text style={[typography.scoreTicker, { color: s.win ? colors.textPrimary : colors.textSecondary }]}>{s.score}</Text>
    </View>
  );

  return (
    <View style={{ borderBottomWidth: 1, borderBottomColor: colors.divider, paddingBottom: 12 }}>
      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ paddingHorizontal: 12, gap: 8 }}>
        {chips.map((c, i) => (
          <View key={i} style={{ width: 116, backgroundColor: colors.surface2, borderRadius: 8, padding: 10, gap: 6 }}>
            <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
              <Text style={{ fontFamily: 'Archivo-Bold', fontSize: 10, color: colors.textSecondary }}>{c.league}</Text>
              <StatusBadge isLive={c.isLive} isFinal={c.isFinal} label={c.status} compact />
            </View>
            <Line s={c.a} />
            <Line s={c.b} />
          </View>
        ))}
      </ScrollView>
    </View>
  );
}
```

### Live Status Badge (pulsing dot)

```tsx
// components/StatusBadge.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StatusBadge({
  isLive, isFinal, label, compact = false,
}: { isLive: boolean; isFinal: boolean; label: string; compact?: boolean }) {
  const op = useSharedValue(1);
  useEffect(() => {
    if (isLive) op.value = withRepeat(withTiming(0.4, { duration: 1200 }), -1, true);
  }, [isLive]);
  const dotStyle = useAnimatedStyle(() => ({ opacity: op.value }));

  const bg = isLive ? colors.live : isFinal ? colors.surface3 : 'transparent';
  const fg = isFinal ? colors.textSecondary : '#FFFFFF';

  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 5,
      paddingHorizontal: compact ? 5 : 9, paddingVertical: compact ? 2 : 4,
      borderRadius: 3, backgroundColor: bg,
      borderWidth: !isLive && !isFinal ? 1 : 0, borderColor: 'rgba(255,255,255,0.4)',
    }}>
      {isLive ? <Animated.View style={[{ width: 6, height: 6, borderRadius: 3, backgroundColor: '#FFFFFF' }, dotStyle]} /> : null}
      <Text style={[typography.statusBadge, { color: fg, fontSize: compact ? 9 : 11 }]}>{label.toUpperCase()}</Text>
    </View>
  );
}
```

### Primary CTA ("Watch Live")

```tsx
// components/WatchLiveButton.tsx
import { Pressable, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WatchLiveButton({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8,
        paddingVertical: 14, borderRadius: 6,
        backgroundColor: pressed ? colors.redPressed : colors.red,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Ionicons name="play" size={16} color="#FFFFFF" />
      <Text style={typography.buttonLabel}>Watch Live</Text>
    </Pressable>
  );
}
```

### SportsCenter Story Row

```tsx
// components/StoryRow.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StoryRow({
  thumb, category, title, meta,
}: { thumb: string; category: string; title: string; meta: string }) {
  return (
    <View style={{
      flexDirection: 'row', gap: 12, paddingVertical: 14,
      borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      <View style={{ width: 96, height: 72, borderRadius: 8, backgroundColor: thumb }} />
      <View style={{ flex: 1 }}>
        <Text style={typography.categoryTag}>{category.toUpperCase()}</Text>
        <Text style={[typography.storyTitle, { marginTop: 4 }]} numberOfLines={2}>{title}</Text>
        <Text style={[typography.meta, { fontSize: 12, marginTop: 6 }]}>{meta}</Text>
      </View>
    </View>
  );
}
```

## 4. Bottom Tab Bar

ESPN marks the active tab with a small red dot under a white icon — not a tint pill. A custom `tabBarIcon` wraps the dot.

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

function TabIcon({ name, color, focused }: { name: any; color: string; focused: boolean }) {
  return (
    <View style={{ alignItems: 'center', gap: 3 }}>
      <Ionicons name={name} size={22} color={color} />
      <View style={{ width: 4, height: 4, borderRadius: 2, backgroundColor: focused ? colors.red : 'transparent' }} />
    </View>
  );
}

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.textPrimary,    // white icon when active
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(14,15,17,0.96)',
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Archivo-Bold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',     tabBarIcon: (p) => <TabIcon name="home"               {...p} /> }} />
      <Tabs.Screen name="scores"  options={{ title: 'Scores',   tabBarIcon: (p) => <TabIcon name="basketball"         {...p} /> }} />
      <Tabs.Screen name="watch"   options={{ title: 'Watch',    tabBarIcon: (p) => <TabIcon name="play-circle"        {...p} /> }} />
      <Tabs.Screen name="bet"     options={{ title: 'ESPN BET', tabBarIcon: (p) => <TabIcon name="stats-chart"        {...p} /> }} />
      <Tabs.Screen name="more"    options={{ title: 'More',     tabBarIcon: (p) => <TabIcon name="ellipsis-horizontal"{...p} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import { withRepeat, withTiming, withSequence } from 'react-native-reanimated';

// Live pulse — the signature cue
op.value = withRepeat(withTiming(0.4, { duration: 1200 }), -1, true);  // dot opacity

// Score update — cross-fade + 1.06 pop
scale.value = withSequence(withTiming(1.06, { duration: 100 }), withTiming(1, { duration: 100 }));

// Game ends — LIVE → FINAL, winner score → green
// Swap badge + animate score color via interpolateColor over 300ms

// Tabbed underline — red bar slide via withTiming(targetX, { duration: 200 })

// Card press — scale 0.98
// style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.98 : 1 }] })}

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);   // follow toggle, tab change
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);  // tracked team's score change
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). ESPN iconography is bold and simple.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Scores | `basketball` |
| Watch | `play-circle` |
| ESPN BET | `stats-chart` |
| More | `ellipsis-horizontal` |
| Watch / play (CTA) | `play` |
| Follow team | `add` / `checkmark` (following) |
| Alerts / bell | `notifications-outline` |
| Search | `search` |
| Share | `share-social-outline` |
| Back | `chevron-back` |
| Box score | `grid-outline` |
| Stats | `stats-chart-outline` |
| Cast | `tv-outline` |
| Live dot | (a `View` circle, not an icon) |

## 7. Platform Notes

- **Font choice**: ESPN's brand face is proprietary; **Archivo** (SIL OFL, free) is the closest analog — ship Regular / Medium / SemiBold / Bold / Black. Without it, fall back to the system font and keep `fontVariant: ['tabular-nums']` on scores
- **Tabular numerals are mandatory**: every score / clock / record uses `fontVariant: ['tabular-nums']` — proportional digits shift columns and break the scoreboard. This is correctness, not style
- **Dark-first scoreboard**: keep the scoreboard surfaces on the near-black canvas — do **not** swap to a pure-white theme via `useColorScheme()`. Set `<StatusBar style="light" />`
- **Not pure black**: the canvas is `#0E0F11` — never `#000000`; cards need contrast
- **Pinned ticker**: render the `ScoresTicker` outside the feed `FlatList` (pinned below the top bar) so it stays glanceable while the feed scrolls
- **Safe area**: wrap screens in `SafeAreaView`; the top bar + ticker sit below the Dynamic Island; a persistent watch mini-bar (if playing) sits above the tab bar
- **Dynamic Type**: `<Text>` respects system scale — set `allowFontScaling={false}` on ticker scores/chips, status badges, tab labels, and the pulse dot so the fixed-rhythm scoreboard doesn't reflow
- **Accessibility**: give the game card `accessibilityLabel` "{away} {awayScore}, {home} {homeScore}, {status}"; back the win-green and live-red with text ("FINAL", "LIVE Q3") so state isn't color-only; the LIVE badge should announce "Live, {period}"
- **Single-accent discipline**: keep `colors.red`/`redBright` for brand/CTA/tags only; `live` and `win` are *status* colors, not accents — don't reuse them as general UI tints
- **Live updates**: drive scores via a websocket/poll; on a tracked team's score change fire `Haptics.ImpactFeedbackStyle.Medium` and the 1.06 score pop
