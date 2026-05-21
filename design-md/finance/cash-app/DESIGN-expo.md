# Cash App (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Cash App's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets for the signature $-amount keypad.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces
  canvas:        '#000000',
  surface1:      '#0F0F0F',
  surface2:      '#1A1A1A',
  divider:       '#1F1F1F',
  hairline:      '#2A2A2A',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#9E9E9E',
  textTertiary:  '#6E6E6E',

  // Brand
  cashGreen:        '#00D632',
  cashGreenPressed: '#00B829',
  cashGreenDim:     '#008C20',
  bitcoinOrange:    '#F7931A',
  boostRed:         '#FF453A',

  // Semantic
  success:       '#00D632',
  error:         '#FF453A',
  warning:       '#FFA300',

  // Light mode (accessibility)
  lightCanvas:        '#FFFFFF',
  lightSurface1:      '#F5F5F5',
  lightSurface2:      '#EEEEEE',
  lightTextPrimary:   '#000000',
  lightTextSecondary: '#6E6E6E',
} as const;

// Random saturated avatar colors (no-photo fallback)
export const avatarColors = [
  '#FF6B9D', // pink
  '#FF953C', // orange
  '#619AFF', // blue
  '#5BD47B', // green
  '#AA77FF', // purple
  '#FFCA4C', // yellow
] as const;

export type CashColor = keyof typeof colors;
```

## 2. Typography

Cash Sans and Cash Sans Mono are proprietary. Bundle via `expo-font`, or fall back to `System` (SF Pro on iOS) and a monospaced family for the amounts.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'CashSans-Regular':     require('../assets/fonts/CashSans-Regular.otf'),
    'CashSans-Medium':      require('../assets/fonts/CashSans-Medium.otf'),
    'CashSans-Bold':        require('../assets/fonts/CashSans-Bold.otf'),
    'CashSans-Black':       require('../assets/fonts/CashSans-Black.otf'),
    'CashSansMono-Regular': require('../assets/fonts/CashSansMono-Regular.otf'),
    'CashSansMono-Medium':  require('../assets/fonts/CashSansMono-Medium.otf'),
    'CashSansMono-Bold':    require('../assets/fonts/CashSansMono-Bold.otf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const white = { color: '#FFFFFF' } satisfies TextStyle;
const gray  = { color: '#9E9E9E' } satisfies TextStyle;

export const typography = {
  // Mono amounts (tabular numerals)
  amountHero:    { ...white, fontFamily: 'CashSansMono-Bold',    fontSize: 96, lineHeight: 96, letterSpacing: -2 },
  amountReceive: { ...white, fontFamily: 'CashSansMono-Bold',    fontSize: 64, lineHeight: 64, letterSpacing: -1.5 },
  balance:       { ...white, fontFamily: 'CashSansMono-Bold',    fontSize: 48, lineHeight: 48, letterSpacing: -1 },
  cashtagLarge:  { ...white, fontFamily: 'CashSansMono-Medium',  fontSize: 22, lineHeight: 27 },
  amountRow:     { ...white, fontFamily: 'CashSansMono-Medium',  fontSize: 17, lineHeight: 22 },
  cashtagInline: { ...white, fontFamily: 'CashSansMono-Medium',  fontSize: 15, lineHeight: 20 },
  keypadDigit:   { ...white, fontFamily: 'CashSansMono-Regular', fontSize: 32, lineHeight: 32 },

  // Sans titles & body
  screenTitle:   { ...white, fontFamily: 'CashSans-Black',   fontSize: 28, lineHeight: 31, letterSpacing: -0.5 },
  sectionHeader: { ...white, fontFamily: 'CashSans-Bold',    fontSize: 22, lineHeight: 25 },
  cardLabel:     { ...white, fontFamily: 'CashSans-Medium',  fontSize: 16, lineHeight: 20 },
  body:          { ...white, fontFamily: 'CashSans-Regular', fontSize: 15, lineHeight: 21 },
  meta:          { ...gray,  fontFamily: 'CashSans-Regular', fontSize: 13, lineHeight: 17 },

  button:        { color: '#000000', fontFamily: 'CashSans-Bold',   fontSize: 17, lineHeight: 17 },
  buttonRequest: { ...white,         fontFamily: 'CashSans-Bold',   fontSize: 17, lineHeight: 17 },
  buttonSmall:   { ...white,         fontFamily: 'CashSans-Medium', fontSize: 15, lineHeight: 18 },
  allCaps:       { color: '#9E9E9E', fontFamily: 'CashSans-Bold',   fontSize: 11, letterSpacing: 1.5 },
  avatarInit:    { color: '#FFFFFF', fontFamily: 'CashSans-Bold',   fontSize: 22, lineHeight: 24 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### The $-Amount Keypad (Pay screen — the hero)

```tsx
// components/AmountKeypad.tsx
import { useState } from 'react';
import { View, Text, Pressable, StyleSheet } from 'react-native';
import Animated, { useAnimatedStyle, useSharedValue, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const amountFontSize = (len: number) =>
  len <= 3 ? 96 : len === 4 ? 80 : len === 5 ? 64 : 52;

export function AmountKeypad({ onPay, onRequest }: { onPay: (amount: string) => void; onRequest: (amount: string) => void }) {
  const [amount, setAmount] = useState('');
  const display = amount === '' ? '0' : amount;

  const append = (c: string) => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    setAmount((a) => a + c);
  };
  const backspace = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    setAmount((a) => a.slice(0, -1));
  };

  const validPay = amount !== '' && parseFloat(amount) > 0;

  return (
    <View style={styles.container}>
      {/* Top: giant amount */}
      <View style={styles.amountRow}>
        <Text style={[styles.dollar, { fontSize: amountFontSize(display.length) * 0.67 }]}>$</Text>
        <Text
          style={[
            styles.amount,
            { fontSize: amountFontSize(display.length), lineHeight: amountFontSize(display.length) },
            validPay && styles.amountGlow,
          ]}
        >
          {display}
        </Text>
      </View>

      {/* Keypad */}
      <View style={styles.keypad}>
        <KeypadRow keys={[ '1', '2', '3' ]} onPress={append} />
        <KeypadRow keys={[ '4', '5', '6' ]} onPress={append} />
        <KeypadRow keys={[ '7', '8', '9' ]} onPress={append} />
        <View style={styles.row}>
          <KeypadKey label="." onPress={() => append('.')} />
          <KeypadKey label="0" onPress={() => append('0')} />
          <KeypadKey iconName="backspace-outline" onPress={backspace} />
        </View>
      </View>

      {/* Pay / Request */}
      <View style={styles.actions}>
        <Pressable
          onPress={() => onRequest(amount)}
          style={({ pressed }) => [styles.actionBtn, { backgroundColor: pressed ? colors.hairline : colors.surface2 }]}
        >
          <Text style={typography.buttonRequest}>Request</Text>
        </Pressable>
        <Pressable
          onPress={() => {
            if (validPay) {
              Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
              onPay(amount);
            }
          }}
          disabled={!validPay}
          style={({ pressed }) => [
            styles.actionBtn,
            { backgroundColor: !validPay ? colors.surface2 : pressed ? colors.cashGreenPressed : colors.cashGreen },
          ]}
        >
          <Text style={[typography.button, !validPay && { color: colors.textTertiary }]}>Pay</Text>
        </Pressable>
      </View>
    </View>
  );
}

function KeypadRow({ keys, onPress }: { keys: string[]; onPress: (c: string) => void }) {
  return (
    <View style={styles.row}>
      {keys.map((k) => <KeypadKey key={k} label={k} onPress={() => onPress(k)} />)}
    </View>
  );
}

function KeypadKey({ label, iconName, onPress }: { label?: string; iconName?: keyof typeof Ionicons.glyphMap; onPress: () => void }) {
  const scale = useSharedValue(1);
  const animStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => { scale.value = withSpring(0.96, { damping: 12 }); }}
      onPressOut={() => { scale.value = withSpring(1, { damping: 12 }); }}
      onPress={onPress}
      style={styles.keyContainer}
    >
      <Animated.View style={[styles.key, animStyle]}>
        {label !== undefined && <Text style={typography.keypadDigit}>{label}</Text>}
        {iconName !== undefined && <Ionicons name={iconName} size={28} color={colors.textPrimary} />}
      </Animated.View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  container:  { flex: 1, backgroundColor: colors.canvas },
  amountRow:  { flex: 1, alignItems: 'center', justifyContent: 'center', flexDirection: 'row', gap: 4 },
  dollar:     { color: colors.textSecondary, fontFamily: 'CashSansMono-Bold' },
  amount:     { color: colors.textPrimary, fontFamily: 'CashSansMono-Bold' },
  amountGlow: { textShadowColor: colors.cashGreen, textShadowRadius: 8 },
  keypad:     { paddingHorizontal: 24, gap: 4 },
  row:        { flexDirection: 'row', gap: 4 },
  keyContainer: { flex: 1 },
  key:        { height: 64, borderRadius: 12, alignItems: 'center', justifyContent: 'center' },
  actions:    { flexDirection: 'row', gap: 12, paddingHorizontal: 24, paddingTop: 16, paddingBottom: 24 },
  actionBtn:  { flex: 1, height: 60, borderRadius: 30, alignItems: 'center', justifyContent: 'center' },
});
```

### Cash Card Render

```tsx
// components/CashCard.tsx
import { View, Text, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';

export function CashCard({ cashtag, accentColor = colors.cashGreen }: { cashtag: string; accentColor?: string }) {
  return (
    <View style={styles.card}>
      <View style={styles.row}>
        <Text style={[styles.tag, { color: accentColor }]}>{cashtag}</Text>
        <Text style={[styles.logo, { color: accentColor }]}>$</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  card: { aspectRatio: 1.586, backgroundColor: colors.canvas, borderRadius: 12, borderWidth: 1, borderColor: colors.divider, padding: 24, justifyContent: 'flex-end' },
  row:  { flexDirection: 'row', alignItems: 'flex-end', justifyContent: 'space-between' },
  tag:  { fontFamily: 'CashSansMono-Bold', fontSize: 24 },
  logo: { fontFamily: 'CashSans-Black', fontSize: 32 },
});
```

### Activity Row

```tsx
// components/ActivityRow.tsx
import { View, Text, StyleSheet } from 'react-native';
import { colors, avatarColors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  name: string;
  direction: 'sent' | 'received';
  subtitle: string;
  amount: string; // "20.00"
};

export function ActivityRow({ name, direction, subtitle, amount }: Props) {
  const avatar = avatarColors[name.charCodeAt(0) % avatarColors.length];
  const sign   = direction === 'received' ? '+' : '-';
  const color  = direction === 'received' ? colors.cashGreen : colors.textPrimary;

  return (
    <View style={styles.row}>
      <View style={[styles.avatar, { backgroundColor: avatar }]}>
        <Text style={typography.avatarInit}>{name.charAt(0).toUpperCase()}</Text>
      </View>
      <View style={{ flex: 1 }}>
        <Text style={typography.cardLabel}>{name}</Text>
        <Text style={typography.meta}>{subtitle}</Text>
      </View>
      <Text style={[typography.amountRow, { color }]}>{sign}${amount}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  row:    { flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 24, height: 64, backgroundColor: colors.canvas, borderBottomWidth: 0.5, borderBottomColor: colors.divider },
  avatar: { width: 40, height: 40, borderRadius: 20, alignItems: 'center', justifyContent: 'center' },
});
```

### Balance Tile

```tsx
// components/BalanceTile.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BalanceTile({ dollars, cents }: { dollars: number; cents: number }) {
  return (
    <View style={styles.tile}>
      <Text style={[typography.allCaps, { letterSpacing: 1.5 }]}>CASH BALANCE</Text>
      <View style={styles.amountRow}>
        <Text style={typography.balance}>${dollars.toLocaleString()}</Text>
        <Text style={[typography.balance, { fontSize: 24, lineHeight: 24 }]}>{`.${String(cents).padStart(2, '0')}`}</Text>
      </View>
      <View style={styles.btnRow}>
        <PillButton label="Add Cash" />
        <PillButton label="Cash Out" />
      </View>
    </View>
  );
}

function PillButton({ label }: { label: string }) {
  return (
    <Pressable style={({ pressed }) => [styles.pill, { backgroundColor: pressed ? colors.hairline : colors.surface2 }]}>
      <Text style={typography.buttonSmall}>{label}</Text>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  tile:       { paddingHorizontal: 24, paddingVertical: 24, backgroundColor: colors.canvas, gap: 8 },
  amountRow:  { flexDirection: 'row', alignItems: 'baseline' },
  btnRow:     { flexDirection: 'row', gap: 8, marginTop: 12 },
  pill:       { borderRadius: 500, paddingHorizontal: 18, paddingVertical: 10 },
});
```

### Screen Title

```tsx
// components/ScreenTitle.tsx
import { View, Text, Image } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ScreenTitle({ title, avatarUri }: { title: string; avatarUri?: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 24, paddingTop: 16 }}>
      <Text style={typography.screenTitle}>{title}</Text>
      {avatarUri ? (
        <Image source={{ uri: avatarUri }} style={{ width: 36, height: 36, borderRadius: 18 }} />
      ) : (
        <View style={{ width: 36, height: 36, borderRadius: 18, backgroundColor: colors.surface2 }} />
      )}
    </View>
  );
}
```

## 4. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import MaterialCommunityIcons from '@expo/vector-icons/MaterialCommunityIcons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarShowLabel: false,        // NO LABELS — core to Cash App
        tabBarActiveTintColor:   colors.cashGreen,
        tabBarInactiveTintColor: colors.textPrimary,  // INACTIVE STAYS WHITE
        tabBarStyle: {
          backgroundColor: colors.canvas,
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
          height: 56 + 24,           // + safe area
        },
        headerShown: false,
      }}
    >
      <Tabs.Screen name="money"    options={{ tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'cash' : 'cash-outline'} size={28} color={color} /> }} />
      <Tabs.Screen name="card"     options={{ tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'card' : 'card-outline'} size={28} color={color} /> }} />
      <Tabs.Screen name="pay"      options={{ tabBarIcon: ({ color }) => <MaterialCommunityIcons name="currency-usd" size={32} color={color} /> }} />
      <Tabs.Screen name="activity" options={{ tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'time' : 'time-outline'} size={28} color={color} /> }} />
      <Tabs.Screen name="bitcoin"  options={{ tabBarIcon: ({ color }) => <MaterialCommunityIcons name="bitcoin" size={28} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// Keypad digit tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Pay success
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Cash Card flip (Reanimated)
const rotate = useSharedValue(0);
const cardStyle = useAnimatedStyle(() => ({ transform: [{ rotateY: `${rotate.value}deg` }] }));
const flip = () => { rotate.value = withSpring(rotate.value + 180, { damping: 12 }); };

// Tab switch
Haptics.selectionAsync();

// Keypad press scale
scale.value = withSpring(0.96, { damping: 12 });
```

## 6. Icon Library

Use `@expo/vector-icons` — particularly Ionicons and MaterialCommunityIcons for the dollar sign and Bitcoin glyphs.

| Purpose | Icon |
|---------|------|
| Money tab | `Ionicons cash-outline / cash` |
| Card tab | `Ionicons card-outline / card` |
| Pay tab (center, 32pt) | `MaterialCommunityIcons currency-usd` |
| Activity tab | `Ionicons time-outline / time` |
| Bitcoin tab | `MaterialCommunityIcons bitcoin` |
| Backspace (keypad) | `Ionicons backspace-outline` |
| Avatar fallback (initial letter rendered as Text, not an icon) | — |
| Search | `Ionicons search` |
| Settings (profile) | `Ionicons settings-outline` |

Note: For Boost glyphs on the Cash Card tab, use COMMISSIONED CUSTOM ART (SVG or PNG). Cash App boost icons are hand-drawn artist work — NOT stock iconography. Treat these as illustration assets, not icons.

## 7. Platform Notes

- **iOS-first dark**: Cash App's identity is dark. Set `<StatusBar style="light" />` from `expo-status-bar` on every screen.
- **No blur**: Cash App does not use translucent blur — keep the tab bar opaque `#000000`.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The Pay screen keypad needs `useSafeAreaInsets().bottom` to anchor above the home indicator.
- **Dynamic Type**: Do NOT scale the Pay screen `$-amount` — use fixed font-size that's computed by digit count. Activity row amounts can scale up to 22pt. Tab icons are fixed.
- **Mono fonts**: For tabular numerals on amounts, set `style={{ fontVariant: ['tabular-nums'] }}` even when using Cash Sans Mono — guarantees fixed-width digits across platforms.
- **Accessibility labels**: Since tabs have no visible text, every `tabBarIcon` must be paired with an `accessibilityLabel` on the screen ("Money", "Pay", "Activity") for VoiceOver.
- **Avatar randomization**: Cash App assigns avatar colors deterministically per user (so the same person always has the same color). Compute `avatarColors[hash(userId) % avatarColors.length]` rather than `Math.random()`.
- **Light mode (optional)**: Swap canvas to `#FFFFFF`, text primary to `#000000`, Cash Green to `#00B829` for AA contrast. Mono font and tabular numerals stay identical. Most users keep dark mode — light mode is an accessibility option, not the default brand expression.
