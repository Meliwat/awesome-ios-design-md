# PayPal (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates PayPal's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets for the signature balance card, Activity row, and Send Money flow.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  payPalBlue:     '#003087',
  payPalSky:      '#0070BA',
  payPalCobalt:   '#001C64',
  payPalBlueDark: '#3B82F6', // dark-mode shifted

  // Canvas & surfaces (light)
  canvas:        '#FFFFFF',
  surfaceGray:   '#F5F7FA',
  surfaceGray2:  '#EEF1F4',
  divider:       '#E5E8ED',

  // Text (light)
  textPrimary:   '#001435',
  textSecondary: '#2C2E2F',
  textMuted:     '#687173',
  textTertiary:  '#9DA3A6',

  // Semantic
  success:       '#1C8B43',
  successBg:     '#E4F5EA',
  error:         '#D20021',
  errorBg:       '#FCE5E8',
  warning:       '#FFB81C',
  warningBg:     '#FFF6E0',
  warningText:   '#A06B00',

  // Activity icon colors
  iconSent:      '#003087',
  iconReceived:  '#1C8B43',
  iconCard:      '#0070BA',
  iconReward:    '#FFB81C',

  // Dark mode
  darkCanvas:    '#0A0E1A',
  darkSurface1:  '#141A2A',
  darkSurface2:  '#1F2740',
  darkDivider:   '#2A3142',
  darkTextPri:   '#FFFFFF',
  darkTextSec:   '#A8AEC4',
} as const;

export type PayPalColor = keyof typeof colors;

// Brand gradient (for the P-P wordmark blend, occasional hero banners)
export const payPalGradient = ['#003087', '#0070BA'] as const;
```

## 2. Typography

PayPal Sans Big (display) + PayPal Sans Small (body) are proprietary (Monotype, ~2018). Bundle via `expo-font`; fall back to `Inter` from Google Fonts with `fontVariant: ['tabular-nums']` on amounts.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'PayPalSansBig-Regular':    require('../assets/fonts/PayPalSansBig-Regular.otf'),
    'PayPalSansBig-Medium':     require('../assets/fonts/PayPalSansBig-Medium.otf'),
    'PayPalSansBig-Bold':       require('../assets/fonts/PayPalSansBig-Bold.otf'),
    'PayPalSansSmall-Regular':  require('../assets/fonts/PayPalSansSmall-Regular.otf'),
    'PayPalSansSmall-Medium':   require('../assets/fonts/PayPalSansSmall-Medium.otf'),
    'PayPalSansSmall-Bold':     require('../assets/fonts/PayPalSansSmall-Bold.otf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tabular = { fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] };
const dark    = { color: '#001435' } satisfies TextStyle;
const muted   = { color: '#687173' } satisfies TextStyle;

export const typography = {
  // Hero (Big variant)
  balanceHero:    { ...dark, ...tabular, fontFamily: 'PayPalSansBig-Bold',   fontSize: 36, lineHeight: 36, letterSpacing: -0.5 },
  sendAmountHero: { ...dark, ...tabular, fontFamily: 'PayPalSansBig-Bold',   fontSize: 56, lineHeight: 56, letterSpacing: -1 },

  // Titles (Big)
  screenTitle:    { ...dark, fontFamily: 'PayPalSansBig-Bold',   fontSize: 24, lineHeight: 28, letterSpacing: -0.2 },
  sectionHeader:  { ...dark, fontFamily: 'PayPalSansBig-Bold',   fontSize: 18, lineHeight: 22 },
  cardTitle:      { ...dark, fontFamily: 'PayPalSansBig-Medium', fontSize: 16, lineHeight: 21 },
  activityAmount: { ...dark, ...tabular, fontFamily: 'PayPalSansBig-Medium', fontSize: 16, lineHeight: 19 },

  // Body (Small variant)
  body:           { ...dark,  fontFamily: 'PayPalSansSmall-Regular', fontSize: 16, lineHeight: 23 },
  bodySmall:      { ...dark,  fontFamily: 'PayPalSansSmall-Regular', fontSize: 14, lineHeight: 20 },
  activityTitle:  { ...dark,  fontFamily: 'PayPalSansSmall-Medium',  fontSize: 15, lineHeight: 20 },
  activitySub:    { ...muted, fontFamily: 'PayPalSansSmall-Regular', fontSize: 13, lineHeight: 17 },
  meta:           { ...muted, fontFamily: 'PayPalSansSmall-Regular', fontSize: 13, lineHeight: 17 },
  link:           { color: '#0070BA', fontFamily: 'PayPalSansSmall-Medium', fontSize: 15 },
  tab:            { fontFamily: 'PayPalSansSmall-Medium', fontSize: 11, letterSpacing: 0.1 },
  chip:           { fontFamily: 'PayPalSansSmall-Medium', fontSize: 13 },

  // Buttons
  button:         { color: '#FFFFFF', fontFamily: 'PayPalSansBig-Bold',    fontSize: 17, lineHeight: 19 },
  buttonOutline:  { color: '#003087', fontFamily: 'PayPalSansBig-Bold',    fontSize: 17, lineHeight: 19 },
  buttonSmall:    { color: '#003087', fontFamily: 'PayPalSansSmall-Medium', fontSize: 15, lineHeight: 18 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Balance Card

```tsx
// components/BalanceCard.tsx
import { View, Text, Pressable } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BalanceCard({ balance }: { balance: number }) {
  return (
    <View style={{ marginHorizontal: 16, backgroundColor: colors.canvas, borderRadius: 16, borderWidth: 1, borderColor: colors.divider, padding: 20, shadowColor: '#000', shadowOpacity: 0.06, shadowRadius: 8, shadowOffset: { width: 0, height: 2 }, elevation: 2 }}>
      <Text style={typography.activitySub}>PayPal balance</Text>
      <Text style={[typography.balanceHero, { marginTop: 8 }]}>
        ${balance.toLocaleString(undefined, { minimumFractionDigits: 2, maximumFractionDigits: 2 })}
      </Text>
      <View style={{ flexDirection: 'row', gap: 12, marginTop: 16 }}>
        <ActionPill label="Add Money" filled />
        <ActionPill label="Transfer"  filled={false} />
      </View>
    </View>
  );
}

function ActionPill({ label, filled }: { label: string; filled: boolean }) {
  return (
    <Pressable
      style={({ pressed }) => ({
        flex: 1, height: 44, borderRadius: 22,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: filled ? (pressed ? colors.payPalCobalt : colors.payPalBlue) : 'transparent',
        borderWidth: filled ? 0 : 1.5, borderColor: colors.payPalBlue,
      })}
    >
      <Text style={filled ? typography.button : typography.buttonOutline}>{label}</Text>
    </Pressable>
  );
}
```

### Activity Row

```tsx
// components/ActivityRow.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Direction = 'sent' | 'received';
type IconType  = 'sent' | 'received' | 'card' | 'reward';

const ICONS: Record<IconType, { name: keyof typeof Ionicons.glyphMap; color: string }> = {
  sent:     { name: 'arrow-up',       color: colors.iconSent },
  received: { name: 'arrow-down',     color: colors.iconReceived },
  card:     { name: 'card',           color: colors.iconCard },
  reward:   { name: 'gift',           color: colors.iconReward },
};

type Props = {
  name: string;
  subtitle: string;
  amount: number;
  direction: Direction;
  icon: IconType;
};

export function ActivityRow({ name, subtitle, amount, direction, icon }: Props) {
  const ico = ICONS[icon];
  const sign  = direction === 'received' ? '+' : '';
  const color = direction === 'received' ? colors.success : colors.textPrimary;

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 16, minHeight: 68, paddingVertical: 12, backgroundColor: colors.canvas, borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      <View style={{ width: 40, height: 40, borderRadius: 20, backgroundColor: ico.color, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name={ico.name} size={20} color="#FFFFFF" />
      </View>
      <View style={{ flex: 1 }}>
        <Text style={typography.activityTitle} numberOfLines={1}>{name}</Text>
        <Text style={typography.activitySub} numberOfLines={1}>{subtitle}</Text>
      </View>
      <Text style={[typography.activityAmount, { color }]}>
        {sign}${amount.toLocaleString(undefined, { minimumFractionDigits: 2, maximumFractionDigits: 2 })}
      </Text>
    </View>
  );
}
```

### Primary CTA (Pill Button)

```tsx
// components/PayPalButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Variant = 'primary' | 'outline';

export function PayPalButton({ label, variant = 'primary', onPress }: { label: string; variant?: Variant; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', justifyContent: 'center',
        height: 48, borderRadius: 24, paddingHorizontal: 24,
        backgroundColor: variant === 'primary' ? (pressed ? colors.payPalCobalt : colors.payPalBlue) : 'transparent',
        borderWidth: variant === 'outline' ? 1.5 : 0, borderColor: colors.payPalBlue,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={variant === 'primary' ? typography.button : typography.buttonOutline}>{label}</Text>
    </Pressable>
  );
}
```

### Send Money Amount Screen

```tsx
// app/(send)/amount.tsx
import { useState } from 'react';
import { View, Text } from 'react-native';
import { PayPalButton } from '../../components/PayPalButton';
import { colors } from '../../theme/colors';
import { typography } from '../../theme/typography';

export default function SendAmount() {
  const [amount, setAmount] = useState('0');
  const numeric = parseFloat(amount || '0');
  const sendLabel = `Send $${numeric.toLocaleString(undefined, { minimumFractionDigits: 2 })}`;

  return (
    <View style={{ flex: 1, backgroundColor: colors.canvas, justifyContent: 'space-between' }}>
      <View />

      <View style={{ alignItems: 'center', gap: 8 }}>
        <Text style={typography.meta}>USD</Text>
        <View style={{ flexDirection: 'row', alignItems: 'baseline' }}>
          <Text style={[typography.sendAmountHero, { fontSize: 40, color: colors.textMuted }]}>$</Text>
          <Text style={typography.sendAmountHero}>{amount}</Text>
        </View>
      </View>

      {/* Numeric keypad component goes here — wire up to setAmount */}

      <View style={{ paddingHorizontal: 16, paddingBottom: 16 }}>
        <PayPalButton label={sendLabel} onPress={() => { /* send */ }} />
      </View>
    </View>
  );
}
```

### P-P Wordmark Logomark

```tsx
// components/PayPalWordmark.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';

export function PayPalWordmark({ size = 32 }: { size?: number }) {
  return (
    <View style={{ width: size * 1.4, height: size * 1.2, flexDirection: 'row' }}>
      <Text style={{ position: 'absolute', left: 0, fontFamily: 'PayPalSansBig-Bold', fontSize: size, fontStyle: 'italic', color: colors.payPalSky }}>P</Text>
      <Text style={{ position: 'absolute', left: size * 0.42, fontFamily: 'PayPalSansBig-Bold', fontSize: size, fontStyle: 'italic', color: colors.payPalBlue }}>P</Text>
    </View>
  );
}
```

### Activity Filter Chip

```tsx
// components/FilterChip.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FilterChip({ label, selected, onPress }: { label: string; selected: boolean; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        paddingHorizontal: 16, paddingVertical: 8, borderRadius: 16,
        backgroundColor: selected ? colors.payPalBlue : colors.surfaceGray2,
      }}
    >
      <Text style={[typography.chip, { color: selected ? '#FFFFFF' : colors.textMuted }]}>{label}</Text>
    </Pressable>
  );
}
```

### Status Pill

```tsx
// components/StatusPill.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';

type Status = 'completed' | 'pending' | 'failed' | 'refunded';

const CONFIG: Record<Status, { bg: string; fg: string; label: string }> = {
  completed: { bg: colors.successBg,   fg: colors.success,     label: 'Completed' },
  pending:   { bg: colors.warningBg,   fg: colors.warningText, label: 'Pending' },
  failed:    { bg: colors.errorBg,     fg: colors.error,       label: 'Failed' },
  refunded:  { bg: colors.surfaceGray2, fg: colors.textMuted,  label: 'Refunded' },
};

export function StatusPill({ status }: { status: Status }) {
  const c = CONFIG[status];
  return (
    <View style={{ alignSelf: 'flex-start', paddingHorizontal: 10, paddingVertical: 4, backgroundColor: c.bg, borderRadius: 4 }}>
      <Text style={{ color: c.fg, fontFamily: 'PayPalSansSmall-Medium', fontSize: 12 }}>{c.label}</Text>
    </View>
  );
}
```

## 4. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.payPalBlue,
        tabBarInactiveTintColor: colors.textMuted,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider, height: 56 + 24 },
        tabBarLabelStyle: { fontFamily: 'PayPalSansSmall-Medium', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="home"     options={{ title: 'Home',     tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'home' : 'home-outline'} size={24} color={color} /> }} />
      <Tabs.Screen name="send"     options={{ title: 'Send',     tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'paper-plane' : 'paper-plane-outline'} size={24} color={color} /> }} />
      <Tabs.Screen name="wallet"   options={{ title: 'Wallet',   tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'wallet' : 'wallet-outline'} size={24} color={color} /> }} />
      <Tabs.Screen name="activity" options={{ title: 'Activity', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'time' : 'time-outline'} size={24} color={color} /> }} />
      <Tabs.Screen name="finances" options={{ title: 'Finances', tabBarIcon: ({ color }) => <Ionicons name="trending-up" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// Primary button tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Send success
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Tab switch
Haptics.selectionAsync();

// Filter chip tap
Haptics.selectionAsync();

// Success checkmark scale-in (Reanimated)
const scale = useSharedValue(0.5);
const opacity = useSharedValue(0);
const animateIn = () => {
  scale.value   = withSpring(1, { damping: 12 });
  opacity.value = withTiming(1, { duration: 200 });
};

// Balance update — animate with a smooth withTiming
const animatedBalance = useSharedValue(currentBalance);
animatedBalance.value = withTiming(newBalance, { duration: 400 });
```

## 6. Icon Library

Use `@expo/vector-icons` with Ionicons as the primary set.

| Purpose | Icon |
|---------|------|
| Home tab | `Ionicons home-outline / home` |
| Send tab | `Ionicons paper-plane-outline / paper-plane` |
| Wallet tab | `Ionicons wallet-outline / wallet` |
| Activity tab | `Ionicons time-outline / time` |
| Finances tab | `Ionicons trending-up` |
| Activity icon Sent | `Ionicons arrow-up` (inside colored circle) |
| Activity icon Received | `Ionicons arrow-down` (inside colored circle) |
| Activity icon Card | `Ionicons card` (inside colored circle) |
| Activity icon Reward | `Ionicons gift` (inside colored circle) |
| Notifications | `Ionicons notifications-outline / notifications` |
| Help | `Ionicons help-circle-outline` |
| Search | `Ionicons search-outline` |
| Back | `Ionicons chevron-back` |
| Success | `Ionicons checkmark-circle` |
| Failed | `Ionicons close-circle` |
| Pending | `Ionicons time` |

## 7. Platform Notes

- **iOS-first portrait**: PayPal is portrait-locked on phones; lock with `expo-screen-orientation`.
- **Status bar**: `<StatusBar style="dark" />` on light mode, `style="light"` on dark.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The Send Money fullscreen needs the keypad to respect `useSafeAreaInsets().bottom`.
- **Tabular numerals**: every Text rendering a $-amount must include `style={{ fontVariant: ['tabular-nums'] }}` so the Activity feed aligns vertically.
- **Floating label input**: build with `react-native-reanimated` — when `value !== ''` or focused, animate the label's top position from 18pt to 6pt and its fontSize from 16pt to 12pt over 200ms.
- **Brand gradient**: use `expo-linear-gradient` for the rare hero banners — colors `['#003087', '#0070BA']` with direction `{ start: { x: 0, y: 0 }, end: { x: 1, y: 1 } }`.
- **Accessibility**: Activity row icons carry directional meaning (arrow-up = sent, arrow-down = received) — never rely on color alone. Add `accessibilityLabel`s on every Activity row like `"Sent to Sarah Kim, $24.50, yesterday at 3:42 PM, completed"`.
- **Dark mode**: use `useColorScheme()` to switch the token object. Note: dark canvas is `#0A0E1A` (slight blue tint), NOT pure black — preserves the brand's blue identity. PayPal Blue shifts to `#3B82F6` on dark for AA contrast.
- **Send Money success**: present as a fullscreen modal with `presentation: 'fullScreenModal'` and a celebratory but restrained checkmark animation — no confetti (PayPal is institutional).
