# Chime (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Chime's visual language into paste-ready Expo / React Native code: a design-token module, the balance hero, the transaction row, the SpotMe banner, the instant alert, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand (Mint)
  mint:        '#1EC677',
  mintBright:  '#00D67E',
  mintPressed: '#16A862',
  mintSoft:     '#E4F7EE',  // light
  mintSoftDark: '#123A2A',  // dark
  spotMe:       '#12B981',  // light
  spotMeDark:   '#2EE6A6',  // dark
  balanceInk:  '#062014',

  // Surfaces (light)
  canvas:   '#F6FBF8',
  surface1: '#FFFFFF',
  surface2: '#F0F6F2',
  divider:  '#E4EDE8',

  // Surfaces (dark)
  darkCanvas:   '#0E1411',
  darkSurface1: '#16201B',
  darkSurface2: '#1F2C25',
  darkDivider:  '#243029',

  // Text
  textPrimary:       '#0F1A14',
  textSecondary:     '#5C6E63',
  textTertiary:      '#90A399',
  darkTextPrimary:   '#EAF2EC',
  darkTextSecondary: '#9DB0A4',

  // Semantic
  positive:     '#0FAE63',  // light
  positiveDark: '#00D67E',  // dark
  negative:     '#E5484D',  // light
  negativeDark: '#FF6B6B',  // dark
  warning:      '#E08600',  // light
  warningDark:  '#FFB23E',  // dark
} as const;

export type ChimeColor = keyof typeof colors;
```

## 2. Typography

Chime uses a clean geometric sans — **DM Sans** is the faithful free stand-in. Load via `expo-font`. Use tabular figures for amounts (`fontVariant: ['tabular-nums']`).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'DMSans-Regular':  require('../assets/fonts/DMSans-Regular.ttf'),
    'DMSans-Medium':   require('../assets/fonts/DMSans-Medium.ttf'),
    'DMSans-SemiBold': require('../assets/fonts/DMSans-SemiBold.ttf'),
    'DMSans-Bold':     require('../assets/fonts/DMSans-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tnum = { fontVariant: ['tabular-nums'] as const };
const primary = { color: '#0F1A14' } satisfies TextStyle;

export const typography = {
  balance:      { ...tnum, color: '#062014', fontFamily: 'DMSans-Bold', fontSize: 44, lineHeight: 46, letterSpacing: -1.5 },
  balanceCents: { ...tnum, color: '#062014', fontFamily: 'DMSans-Bold', fontSize: 26, lineHeight: 28 },
  screenTitle:  { ...primary, fontFamily: 'DMSans-Bold',     fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  section:      { ...primary, fontFamily: 'DMSans-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  cardTitle:    { ...primary, fontFamily: 'DMSans-Bold',     fontSize: 18, lineHeight: 23 },
  body:         { ...primary, fontFamily: 'DMSans-Regular',  fontSize: 16, lineHeight: 24 },
  rowTitle:     { ...primary, fontFamily: 'DMSans-SemiBold', fontSize: 15, lineHeight: 20 },
  amount:       { ...tnum, ...primary, fontFamily: 'DMSans-Bold', fontSize: 15, lineHeight: 18 },
  meta:         { color: '#5C6E63', fontFamily: 'DMSans-Regular',  fontSize: 14, lineHeight: 19 },
  caption:      { color: '#5C6E63', fontFamily: 'DMSans-SemiBold', fontSize: 12, lineHeight: 17, letterSpacing: 0.1 },
  button:       { color: '#062014', fontFamily: 'DMSans-Bold',     fontSize: 16, lineHeight: 16 },
  tab:          { fontFamily: 'DMSans-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  chip:         { fontFamily: 'DMSans-Bold', fontSize: 12, lineHeight: 12, letterSpacing: 0.1 },
  link:         { color: '#00D67E', fontFamily: 'DMSans-Bold', fontSize: 13, lineHeight: 17 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Balance Hero Card

```tsx
// components/BalanceHero.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BalanceHero({
  dollars, cents, onMove, onPay,
}: { dollars: string; cents: string; onMove: () => void; onPay: () => void }) {
  return (
    <LinearGradient
      colors={[colors.mint, '#12A862']}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
      style={{
        marginHorizontal: 16, borderRadius: 24, padding: 22,
        shadowColor: colors.mint, shadowOpacity: 0.45, shadowRadius: 16,
        shadowOffset: { width: 0, height: 16 }, elevation: 10,
      }}
    >
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <Ionicons name="card" size={13} color="rgba(6,32,20,0.7)" />
        <Text style={[typography.caption, { color: 'rgba(6,32,20,0.7)' }]}>Checking</Text>
      </View>

      <View style={{ flexDirection: 'row', alignItems: 'flex-start', marginTop: 6 }}>
        <Text style={typography.balance}>{dollars}</Text>
        <Text style={[typography.balanceCents, { marginTop: 4 }]}>.{cents}</Text>
      </View>
      <Text style={[typography.caption, { color: 'rgba(6,32,20,0.65)', marginTop: 4 }]}>
        Available to spend
      </Text>

      <View style={{ flexDirection: 'row', gap: 10, marginTop: 20 }}>
        <HeroBtn icon="swap-horizontal" label="Move money" onPress={onMove} />
        <HeroBtn icon="paper-plane" label="Pay anyone" onPress={onPay} />
      </View>
    </LinearGradient>
  );
}

function HeroBtn({ icon, label, onPress }: { icon: any; label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        flex: 1, height: 40, borderRadius: 12,
        flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 6,
        backgroundColor: 'rgba(6,32,20,0.14)',
      }}
    >
      <Ionicons name={icon} size={15} color={colors.balanceInk} />
      <Text style={typography.link}>{label}</Text>
    </Pressable>
  );
}
```

### Transaction Row

```tsx
// components/TransactionRow.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Tile = { type: 'merchant'; initial: string } | { type: 'action'; icon: any };

export function TransactionRow({
  tile, name, meta, amount, positive,
}: { tile: Tile; name: string; meta: string; amount: string; positive: boolean }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 12,
      paddingVertical: 12, borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      {tile.type === 'merchant' ? (
        <LinearGradient colors={['#2A3A33', '#1A2620']}
          style={{ width: 42, height: 42, borderRadius: 14, alignItems: 'center', justifyContent: 'center' }}>
          <Text style={[typography.amount, { color: '#FFF' }]}>{tile.initial}</Text>
        </LinearGradient>
      ) : (
        <View style={{
          width: 42, height: 42, borderRadius: 14, alignItems: 'center', justifyContent: 'center',
          backgroundColor: colors.mintSoft,
        }}>
          <Ionicons name={tile.icon} size={19} color={colors.spotMe} />
        </View>
      )}
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

### SpotMe Banner

```tsx
// components/SpotMeBanner.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SpotMeBanner({ limit }: { limit: string }) {
  return (
    <View style={{
      marginHorizontal: 16, backgroundColor: colors.surface1,
      borderWidth: 1, borderColor: colors.divider, borderRadius: 16,
      padding: 14, flexDirection: 'row', alignItems: 'center', gap: 12,
    }}>
      <View style={{
        width: 38, height: 38, borderRadius: 12, backgroundColor: colors.mintSoft,
        alignItems: 'center', justifyContent: 'center',
      }}>
        <Ionicons name="star" size={19} color={colors.spotMe} />
      </View>
      <View style={{ flex: 1 }}>
        <Text style={[typography.rowTitle, { fontFamily: 'DMSans-Bold' }]}>SpotMe is on</Text>
        <Text style={typography.caption}>Overdraft up to your limit, fee-free</Text>
      </View>
      <Text style={[typography.amount, { color: colors.spotMe }]}>{limit}</Text>
    </View>
  );
}
```

### Instant Transaction Alert

```tsx
// components/InstantAlert.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Animated, { SlideInUp, SlideOutUp } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function InstantAlert({
  title, sub, amount, onDismiss,
}: { title: string; sub: string; amount: string; onDismiss: () => void }) {
  useEffect(() => {
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
    const t = setTimeout(onDismiss, 4000);
    return () => clearTimeout(t);
  }, []);
  return (
    <Animated.View
      entering={SlideInUp.springify().damping(18)}
      exiting={SlideOutUp.duration(250)}
      style={{
        marginHorizontal: 16, backgroundColor: colors.surface2,
        borderWidth: 1, borderColor: colors.divider, borderRadius: 18,
        padding: 14, flexDirection: 'row', alignItems: 'center', gap: 14,
        shadowColor: colors.textPrimary, shadowOpacity: 0.16, shadowRadius: 14,
        shadowOffset: { width: 0, height: 12 }, elevation: 12,
      }}
    >
      <View style={{
        width: 44, height: 44, borderRadius: 22, backgroundColor: colors.mint,
        alignItems: 'center', justifyContent: 'center',
      }}>
        <Ionicons name="checkmark" size={22} color={colors.balanceInk} />
      </View>
      <View style={{ flex: 1 }}>
        <Text style={[typography.rowTitle, { fontFamily: 'DMSans-Bold' }]}>{title}</Text>
        <Text style={typography.caption}>{sub}</Text>
      </View>
      <Text style={[typography.cardTitle, { color: colors.positive, fontVariant: ['tabular-nums'] }]}>
        {amount}
      </Text>
    </Animated.View>
  );
}
```

### Primary Button & Status Chip

```tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        minHeight: 52, borderRadius: 14, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.mintPressed : colors.mint,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

const STATUS = {
  posted:   { bg: colors.mintSoft,            fg: colors.positive, label: 'Posted' },
  pending:  { bg: 'rgba(224,134,0,0.15)',     fg: colors.warning,  label: 'Pending' },
  spotMe:   { bg: 'rgba(18,185,129,0.14)',    fg: colors.spotMe,   label: 'SpotMe covered' },
  declined: { bg: 'rgba(229,72,77,0.15)',     fg: colors.negative, label: 'Declined' },
} as const;

export function StatusChip({ status }: { status: keyof typeof STATUS }) {
  const s = STATUS[status];
  return (
    <View style={{ alignSelf: 'flex-start', backgroundColor: s.bg, borderRadius: 999, paddingHorizontal: 14, paddingVertical: 7 }}>
      <Text style={[typography.chip, { color: s.fg }]}>{s.label}</Text>
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
        tabBarActiveTintColor: colors.mintBright,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.surface1, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'DMSans-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index" options={{ title: 'Home', tabBarIcon: ({ color }) => <Ionicons name="home"             size={22} color={color} /> }} />
      <Tabs.Screen name="move"  options={{ title: 'Move', tabBarIcon: ({ color }) => <Ionicons name="swap-horizontal"  size={22} color={color} /> }} />
      <Tabs.Screen name="card"  options={{ title: 'Card', tabBarIcon: ({ color }) => <Ionicons name="card"             size={22} color={color} /> }} />
      <Tabs.Screen name="grow"  options={{ title: 'Grow', tabBarIcon: ({ color }) => <Ionicons name="trending-up"      size={22} color={color} /> }} />
      <Tabs.Screen name="me"    options={{ title: 'Me',   tabBarIcon: ({ color }) => <Ionicons name="person-circle"    size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, {
  useSharedValue, useAnimatedProps, withTiming, withSpring, withSequence,
} from 'react-native-reanimated';
import { TextInput } from 'react-native';

const AnimatedText = Animated.createAnimatedComponent(TextInput);

// Balance count-up — odometer roll on load / after transfer
const bal = useSharedValue(0);
const props = useAnimatedProps(() => ({ text: `$${bal.value.toFixed(2)}` } as any));
// bal.value = withTiming(actualBalance, { duration: 600 });

// Instant alert — SlideInUp.springify().damping(18) + Haptics.Success (see InstantAlert)

// SpotMe boost — number scale bump + medium haptic
const scale = useSharedValue(1);
const onBoost = () => {
  scale.value = withSequence(withSpring(1.15, { damping: 6 }), withSpring(1));
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
};

// Transfer success — full-screen mint check; use react-native-svg stroke-dashoffset
//   withTiming(0, { duration: 500 }) + Haptics.notificationAsync(Success)

// New transaction insert — Animated.View entering={FadeInUp.duration(250)}
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Chime's iconography is simple and rounded.

| Purpose | Ionicons |
|---------|----------|
| Home (tab) | `home` |
| Move (tab) | `swap-horizontal` |
| Card (tab) | `card` |
| Grow (tab) | `trending-up` |
| Me (tab) | `person-circle` |
| Move money | `swap-horizontal` |
| Pay anyone | `paper-plane` |
| SpotMe | `star` / `shield-checkmark` |
| Deposit / incoming | `arrow-down-circle` |
| Alert check | `checkmark` |
| Notifications | `notifications-outline` |
| Add to Spot | `add` |
| Back | `chevron-back` |
| Card frozen | `snow` |
| Settings | `settings-outline` |
| Direct deposit | `cash-outline` |
| Grow / savings | `leaf` |

## 7. Platform Notes

- **Font choice**: DM Sans is SIL OFL via Google Fonts — free to bundle. Ship Regular / Medium / SemiBold / Bold
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on the balance and every amount so columns align and the count-up doesn't jitter (already wired in `typography`)
- **Status bar**: `<StatusBar style="dark" />` on light mode, `"light"` on dark; the mint hero is bright so dark-content icons over it read fine when it reaches the top
- **Safe area**: wrap screens in `SafeAreaView`; the instant alert mounts below the top safe area; tab bar + sheet CTAs respect the home indicator via `useSafeAreaInsets()`
- **Dynamic Type**: `<Text>` respects system scale by default — set `allowFontScaling={false}` on tab labels, chip text, and the cents superscript
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1`; switch `mintSoft → mintSoftDark`, `spotMe → spotMeDark`, `positive → positiveDark`, `negative → negativeDark` — but keep the mint hero gradient and `#062014` ink constant
- **Keyboard**: the Move money amount entry is a sheet (use `@gorhom/bottom-sheet`); content scrolls above it
- **Accessibility**: the balance has `accessibilityLabel="Available to spend, $2,847.19"`; transaction rows announce name + meta + signed amount; the instant alert uses `AccessibilityInfo.announceForAccessibility(...)` so it's read aloud on appear; never convey money direction by color alone — keep the `+`/`-` sign and the word "received"
- **Reduce motion**: read `AccessibilityInfo.isReduceMotionEnabled()` and disable the balance roll, SpotMe bump, and check-stroke draw; keep the alert as a fade
