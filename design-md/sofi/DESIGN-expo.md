# SoFi (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates SoFi's visual language into paste-ready Expo / React Native code: a design-token module, the gradient member hero, account tiles, cross-sell cards, the activity row, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand (Blue)
  sofiBlue:        '#00A0DF',
  sofiBlueBright:  '#29C2FF',
  sofiBluePressed: '#0086BC',
  blueSoft:     '#E6F4FB',  // light
  blueSoftDark: '#0E2A44',  // dark-native
  onBlueInk:    '#042235',

  // Canvas & Surfaces (dark-native — canonical)
  navy:     '#0A0E27',
  surface1: '#121736',
  surface2: '#1B2147',
  divider:  '#252C55',

  // Canvas & Surfaces (light)
  lightCanvas:   '#F4F7FB',
  lightSurface1: '#FFFFFF',
  lightSurface2: '#EEF2F8',
  lightDivider:  '#E2E7F0',

  // Text (on navy)
  textPrimary:   '#E8EBF7',
  textSecondary: '#9BA3C7',
  textTertiary:  '#6B7299',
  textPrimaryLight:   '#0C1330',
  textSecondaryLight: '#5A6286',

  // Semantic
  positive:      '#2FD08A',  // navy
  positiveLight: '#0FA968',  // light
  negative:      '#FF6B6B',  // navy
  negativeLight: '#E0484D',  // light
  gold:          '#F2C14E',  // navy
  goldLight:     '#C8920F',  // light
  pillGreen:     '#6EF0B6',  // change-pill text on hero
} as const;

export type SofiColor = keyof typeof colors;
```

## 2. Typography

SoFi uses a confident geometric sans — **Manrope** is the faithful free stand-in. Load via `expo-font`. Use tabular figures for amounts (`fontVariant: ['tabular-nums']`).

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

const tnum = { fontVariant: ['tabular-nums'] as const };
const primary = { color: '#E8EBF7' } satisfies TextStyle;

export const typography = {
  netWorth:    { ...tnum, color: '#FFFFFF', fontFamily: 'Manrope-ExtraBold', fontSize: 40, lineHeight: 42, letterSpacing: -1.2 },
  screenTitle: { ...primary, fontFamily: 'Manrope-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  section:     { ...primary, fontFamily: 'Manrope-Bold',      fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  tileValue:   { ...tnum, ...primary, fontFamily: 'Manrope-Bold', fontSize: 18, lineHeight: 23, letterSpacing: -0.4 },
  cardTitle:   { ...primary, fontFamily: 'Manrope-Bold',      fontSize: 16, lineHeight: 21 },
  body:        { ...primary, fontFamily: 'Manrope-Regular',   fontSize: 16, lineHeight: 24 },
  rowTitle:    { ...primary, fontFamily: 'Manrope-Bold',      fontSize: 15, lineHeight: 20 },
  amount:      { ...tnum, ...primary, fontFamily: 'Manrope-ExtraBold', fontSize: 15, lineHeight: 18 },
  meta:        { color: '#9BA3C7', fontFamily: 'Manrope-Regular',  fontSize: 14, lineHeight: 19 },
  caption:     { color: '#9BA3C7', fontFamily: 'Manrope-SemiBold', fontSize: 12, lineHeight: 17, letterSpacing: 0.1 },
  button:      { color: '#042235', fontFamily: 'Manrope-Bold',     fontSize: 16, lineHeight: 16 },
  tab:         { fontFamily: 'Manrope-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  chip:        { fontFamily: 'Manrope-Bold', fontSize: 12, lineHeight: 12, letterSpacing: 0.1 },
  link:        { color: '#29C2FF', fontFamily: 'Manrope-Bold', fontSize: 13, lineHeight: 17 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Member Hero (gradient)

```tsx
// components/MemberHero.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MemberHero({
  greeting, netWorth, change, isUp,
}: { greeting: string; netWorth: string; change: string; isUp: boolean }) {
  const insets = useSafeAreaInsets();
  return (
    <LinearGradient
      colors={[colors.sofiBlue, '#1B53C4', colors.navy]}
      locations={[0, 0.45, 1]}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
      style={{ paddingTop: insets.top + 8, paddingHorizontal: 20, paddingBottom: 28 }}
    >
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingBottom: 18 }}>
        <Text style={{ fontFamily: 'Manrope-Bold', fontSize: 18, color: '#FFF' }}>{greeting}</Text>
        <View style={{ flexDirection: 'row', gap: 16 }}>
          <Ionicons name="notifications-outline" size={22} color="#FFF" />
          <Ionicons name="person-circle-outline" size={22} color="#FFF" />
        </View>
      </View>

      <Text style={[typography.caption, { color: 'rgba(255,255,255,0.75)' }]}>Net worth</Text>
      <Text style={[typography.netWorth, { marginTop: 4 }]}>{netWorth}</Text>

      <View style={{
        flexDirection: 'row', alignItems: 'center', gap: 5, alignSelf: 'flex-start',
        marginTop: 8, paddingHorizontal: 10, paddingVertical: 5, borderRadius: 999,
        backgroundColor: 'rgba(47,208,138,0.18)',
      }}>
        <Ionicons name={isUp ? 'arrow-up' : 'arrow-down'} size={11} color={colors.pillGreen} />
        <Text style={[typography.link, { color: colors.pillGreen }]}>{change}</Text>
      </View>
    </LinearGradient>
  );
}
```

### Account Tile

```tsx
// components/AccountTile.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type SubKind = 'gain' | 'loss' | 'rewards' | 'neutral';
const subColor: Record<SubKind, string> = {
  gain: colors.positive, loss: colors.negative, rewards: colors.gold, neutral: colors.textSecondary,
};

export function AccountTile({
  icon, label, value, subStat, subKind = 'neutral',
}: { icon: any; label: string; value: string; subStat: string; subKind?: SubKind }) {
  return (
    <View style={{
      flex: 1, backgroundColor: colors.surface1, borderWidth: 1, borderColor: colors.divider,
      borderRadius: 18, padding: 16,
    }}>
      <View style={{
        width: 36, height: 36, borderRadius: 11, backgroundColor: colors.blueSoftDark,
        alignItems: 'center', justifyContent: 'center', marginBottom: 12,
      }}>
        <Ionicons name={icon} size={18} color={colors.sofiBlueBright} />
      </View>
      <Text style={typography.caption}>{label}</Text>
      <Text style={[typography.tileValue, { marginTop: 3 }]}>{value}</Text>
      <Text style={[typography.caption, { color: subColor[subKind], marginTop: 4 }]}>{subStat}</Text>
    </View>
  );
}

// 2-column grid: <View style={{ flexDirection: 'row', gap: 12 }}><AccountTile…/><AccountTile…/></View>
```

### Cross-Sell Product Card

```tsx
// components/ProductCard.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ProductCard({ icon, title, valueProp }: { icon: any; title: string; valueProp: string }) {
  return (
    <View style={{
      width: 240, backgroundColor: colors.surface2, borderWidth: 1, borderColor: colors.divider,
      borderRadius: 18, padding: 16,
    }}>
      <View style={{
        width: 36, height: 36, borderRadius: 11, backgroundColor: colors.blueSoftDark,
        alignItems: 'center', justifyContent: 'center', marginBottom: 12,
      }}>
        <Ionicons name={icon} size={18} color={colors.sofiBlueBright} />
      </View>
      <Text style={typography.cardTitle}>{title}</Text>
      <Text style={[typography.caption, { fontFamily: 'Manrope-Regular', marginTop: 3 }]}>{valueProp}</Text>
    </View>
  );
}
```

### Activity Row

```tsx
// components/ActivityRow.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ActivityRow({
  icon, tint, name, meta, amount, positive,
}: { icon: any; tint: string; name: string; meta: string; amount: string; positive: boolean }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 12,
      paddingVertical: 12, borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      <View style={{
        width: 40, height: 40, borderRadius: 13, backgroundColor: colors.blueSoftDark,
        alignItems: 'center', justifyContent: 'center',
      }}>
        <Ionicons name={icon} size={18} color={tint} />
      </View>
      <View style={{ flex: 1 }}>
        <Text style={typography.rowTitle}>{name}</Text>
        <Text style={typography.meta}>{meta}</Text>
      </View>
      <Text style={[typography.amount, { color: positive ? colors.positive : colors.textPrimary }]}>
        {amount}
      </Text>
    </View>
  );
}
```

### Primary Button & Performance Chip

```tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        minHeight: 52, borderRadius: 999, alignItems: 'center', justifyContent: 'center',
        paddingHorizontal: 28,
        backgroundColor: pressed ? colors.sofiBluePressed : colors.sofiBlue,  // FULL PILL
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

type Perf =
  | { kind: 'active' }
  | { kind: 'up'; value: string }
  | { kind: 'down'; value: string }
  | { kind: 'rewards'; value: string };

export function PerfChip({ perf }: { perf: Perf }) {
  const map = {
    active:  { bg: colors.blueSoftDark,           fg: colors.sofiBlueBright, text: 'Active' },
    up:      { bg: 'rgba(47,208,138,0.16)',       fg: colors.positive,       text: perf.kind === 'up' ? `▲ ${perf.value}` : '' },
    down:    { bg: 'rgba(255,107,107,0.16)',      fg: colors.negative,       text: perf.kind === 'down' ? `▼ ${perf.value}` : '' },
    rewards: { bg: 'rgba(242,193,78,0.16)',       fg: colors.gold,           text: perf.kind === 'rewards' ? perf.value : '' },
  } as const;
  const s = map[perf.kind];
  return (
    <View style={{ alignSelf: 'flex-start', backgroundColor: s.bg, borderRadius: 999, paddingHorizontal: 14, paddingVertical: 7 }}>
      <Text style={[typography.chip, { color: s.fg }]}>{s.text}</Text>
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
        tabBarActiveTintColor: colors.sofiBlueBright,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.navy, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Manrope-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home"            size={22} color={color} /> }} />
      <Tabs.Screen name="invest"  options={{ title: 'Invest',  tabBarIcon: ({ color }) => <Ionicons name="trending-up"     size={22} color={color} /> }} />
      <Tabs.Screen name="banking" options={{ title: 'Banking', tabBarIcon: ({ color }) => <Ionicons name="card"            size={22} color={color} /> }} />
      <Tabs.Screen name="borrow"  options={{ title: 'Borrow',  tabBarIcon: ({ color }) => <Ionicons name="business"        size={22} color={color} /> }} />
      <Tabs.Screen name="me"      options={{ title: 'Me',      tabBarIcon: ({ color }) => <Ionicons name="person-circle"   size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, {
  useSharedValue, useAnimatedProps, useAnimatedScrollHandler, useAnimatedStyle,
  withTiming, interpolate,
} from 'react-native-reanimated';
import { TextInput } from 'react-native';

const AnimatedText = Animated.createAnimatedComponent(TextInput);

// Net-worth count-up — odometer roll on load / after transfer
const nw = useSharedValue(0);
const props = useAnimatedProps(() => ({ text: `$${nw.value.toLocaleString('en-US', { minimumFractionDigits: 2 })}` } as any));
// nw.value = withTiming(actualNetWorth, { duration: 600 });

// Gradient parallax — hero scrolls ~0.7x content
const scrollY = useSharedValue(0);
const onScroll = useAnimatedScrollHandler((e) => { scrollY.value = e.contentOffset.y; });
const heroStyle = useAnimatedStyle(() => ({ transform: [{ translateY: scrollY.value * -0.3 }] }));

// Tile press — scale
// Use Pressable style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.97 : 1 }] })}

// Transfer / invest success — blue check via react-native-svg stroke-dashoffset
//   withTiming(0, { duration: 500 }) + Haptics.notificationAsync(Success)
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);  // transfer/invest success

// Cross-sell carousel — <Animated.ScrollView horizontal snapToInterval={252} decelerationRate="fast" />
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). SoFi's iconography is clean and line-based.

| Purpose | Ionicons |
|---------|----------|
| Home (tab) | `home` |
| Invest (tab) | `trending-up` |
| Banking (tab) | `card` |
| Borrow (tab) | `business` |
| Me (tab) | `person-circle` |
| Checking & Savings tile | `card-outline` |
| Invest tile | `trending-up` |
| Loans tile | `time-outline` |
| Credit Card tile | `star` |
| Rewards | `gift` |
| Net-worth up | `arrow-up` |
| Net-worth down | `arrow-down` |
| Notifications | `notifications-outline` |
| Transfer | `swap-horizontal` |
| Auto-invest | `repeat` |
| Direct deposit | `cash-outline` |
| Back | `chevron-back` |
| Settings | `settings-outline` |

## 7. Platform Notes

- **Font choice**: Manrope is SIL OFL — free to bundle. Ship Regular / Medium / SemiBold / Bold / ExtraBold
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on net worth and every amount so columns align and the count-up doesn't jitter (already wired in `typography`)
- **Status bar**: `<StatusBar style="light" />` — the navy/gradient ground is dark in both themes, so light-content system bars are correct (even in "light mode" the hero is the gradient)
- **Safe area**: the gradient hero pads `insets.top` and extends edge-to-edge; tab bar + sheet CTAs respect the home indicator via `useSafeAreaInsets()`
- **Dynamic Type**: `<Text>` respects system scale by default — set `allowFontScaling={false}` on tab labels, chip text, and the change-pill text
- **Dark-native**: SoFi's canonical canvas is navy `#0A0E27` in both themes. Use `useColorScheme()` only to lift surfaces in light mode (`surface1 → white`, `blueSoftDark → blueSoft`, `positive → positiveLight`, etc.) — the hero gradient and `#042235` ink stay constant and the navy + electric-blue identity must remain
- **Keyboard**: the Transfer / Invest amount entry is a sheet (use `@gorhom/bottom-sheet`); content scrolls above it
- **Accessibility**: the hero has `accessibilityLabel="Net worth, $84,210.55, up $1,840.22 this month"`; tiles announce label + value + sub-stat; activity rows announce name + meta + signed amount; never convey performance by color alone — keep the ▲/▼ glyph and `+`/`-` sign
- **Reduce motion**: read `AccessibilityInfo.isReduceMotionEnabled()` and disable the net-worth roll, gradient parallax, and check-stroke draw; keep tile press as opacity
