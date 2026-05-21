# Nubank (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Nubank's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  purple:        '#820AD1',
  purpleBright:  '#9B2BE0',
  purplePressed: '#6A07AD',
  purpleSoft:    '#C9A6E8',

  // Surfaces (light)
  canvas:         '#F4F2F7',
  surface:        '#FFFFFF',
  surfacePressed: '#ECE8F2',
  divider:        '#E6E1EE',

  // Surfaces (dark)
  darkCanvas:   '#15101C',
  darkSurface1: '#1F1729',
  darkSurface2: '#2A2038',
  darkDivider:  '#362B47',

  // Text
  textPrimary:       '#1A1523',
  textSecondary:     '#6B6178',
  textTertiary:      '#9C93AB',
  darkTextPrimary:   '#F3EEF8',
  darkTextSecondary: '#B6A8C8',
  darkTextTertiary:  '#7A6B8E',

  // Product accents (light / dark)
  greenLight: '#1FB76A', green: '#2ED47A',
  goldLight:  '#D9A227', gold:  '#F5C24B',
  pinkLight:  '#D9469E', pink:  '#E85FB0',
  infoLight:  '#3D7AE0', info:  '#5A95F0',

  // Semantic
  positiveLight: '#1FB76A', positive: '#2ED47A',
  errorLight:    '#E03A4A', error:    '#FF5C6C',
  warningLight:  '#D9821F', warning:  '#FFB347',
} as const;

export type NuColor = keyof typeof colors;

// Quick actions
export const quickActions = [
  { id: 'pix',      icon: 'arrow-up',      label: 'Pix' },
  { id: 'pay',      icon: 'barcode',       label: 'Pagar' },
  { id: 'cards',    icon: 'card',          label: 'Cartões' },
  { id: 'invest',   icon: 'trending-up',   label: 'Investir' },
  { id: 'loan',     icon: 'cash',          label: 'Empréstimo' },
] as const;
```

## 2. Typography

Nubank ships Graphik / Nu Sans; load **Inter** (SIL OFL) as the free substitute via `expo-font`. Enable tabular figures on money via `fontVariant`.

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
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#1A1523' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  screenTitle: { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  balance:     { ...tnum,    fontFamily: 'Inter-ExtraBold', fontSize: 26, lineHeight: 29, letterSpacing: -0.5 },
  greeting:    { ...primary, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  subsection:  { ...primary, fontFamily: 'Inter-Bold',      fontSize: 18, lineHeight: 23, letterSpacing: -0.2 },
  body:        { ...primary, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 23 },
  rowTitle:    { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 20, letterSpacing: -0.1 },
  amount:      { ...primary, ...tnum, fontFamily: 'Inter-Bold', fontSize: 15, lineHeight: 20, letterSpacing: -0.1 },
  meta:        { color: '#6B6178', fontFamily: 'Inter-Medium', fontSize: 13, lineHeight: 18 },
  label:       { color: '#6B6178', fontFamily: 'Inter-Medium', fontSize: 12, lineHeight: 16, letterSpacing: 0.1 },
  button:      { color: '#FFFFFF', fontFamily: 'Inter-Bold',  fontSize: 16, lineHeight: 16 },
  tab:         { fontFamily: 'Inter-Medium', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  quickAction: { color: '#6B6178', fontFamily: 'Inter-Medium', fontSize: 11, lineHeight: 13 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Purple Hero Header

```tsx
// components/HeroHeader.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import Ionicons from '@expo/vector-icons/Ionicons';
import { typography } from '../theme/typography';

export function HeroHeader({ initials, name }: { initials: string; name: string }) {
  const insets = useSafeAreaInsets();
  return (
    <LinearGradient
      colors={['#820AD1', '#9B2BE0', '#6A07AD']}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
      style={{ paddingTop: insets.top + 8, paddingBottom: 22 }}
    >
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 22, paddingBottom: 16 }}>
        <View style={{ width: 38, height: 38, borderRadius: 19, backgroundColor: 'rgba(255,255,255,0.18)', alignItems: 'center', justifyContent: 'center' }}>
          <Text style={{ color: '#FFF', fontFamily: 'Inter-Bold', fontSize: 15 }}>{initials}</Text>
        </View>
        <View style={{ flexDirection: 'row', gap: 18 }}>
          <Ionicons name="settings-outline" size={21} color="#FFF" />
          <Ionicons name="notifications-outline" size={21} color="#FFF" />
          <Ionicons name="help-circle-outline" size={21} color="#FFF" />
        </View>
      </View>
      <Text style={[typography.label, { color: 'rgba(255,255,255,0.85)', paddingHorizontal: 22 }]}>Olá,</Text>
      <Text style={[typography.greeting, { color: '#FFF', paddingHorizontal: 22 }]}>{name}</Text>
    </LinearGradient>
  );
}
```

### NuConta Balance Tile

```tsx
// components/AccountTile.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function AccountTile({ balance }: { balance: string }) {
  const [hidden, setHidden] = useState(false);
  return (
    <View style={{
      marginHorizontal: 18, backgroundColor: colors.darkSurface1,
      borderRadius: 16, borderWidth: 1, borderColor: colors.darkDivider, padding: 18,
    }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12 }}>
        <Ionicons name="wallet-outline" size={28} color={colors.purpleSoft} />
        <Text style={[typography.rowTitle, { color: colors.darkTextPrimary, flex: 1 }]}>Conta</Text>
        <Pressable onPress={() => setHidden(h => !h)} hitSlop={8}>
          <Ionicons name={hidden ? 'eye-off-outline' : 'eye-outline'} size={20} color={colors.darkTextSecondary} />
        </Pressable>
      </View>
      <Text style={[typography.balance, { color: colors.darkTextPrimary, marginTop: 14 }]}>
        {hidden ? 'R$ ••••••' : balance}
      </Text>
      <Text style={[typography.meta, { color: colors.darkTextSecondary, marginTop: 2 }]}>Saldo disponível</Text>
    </View>
  );
}
```

### Credit-Card (Roxinho) Tile

```tsx
// components/CreditTile.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function CreditTile({
  invoice, dueText, limitText, used,
}: { invoice: string; dueText: string; limitText: string; used: number }) {
  return (
    <View style={{ marginHorizontal: 18, marginTop: 14, backgroundColor: colors.darkSurface1, borderRadius: 16, padding: 18 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12 }}>
        <LinearGradient colors={['#820AD1', '#9B2BE0']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ width: 40, height: 26, borderRadius: 5 }} />
        <Text style={[typography.rowTitle, { color: colors.darkTextPrimary }]}>Cartão de crédito</Text>
      </View>
      <Text style={[typography.subsection, { color: colors.darkTextPrimary, marginTop: 14, fontFamily: 'Inter-ExtraBold' }]}>{invoice}</Text>
      <Text style={[typography.meta, { color: colors.darkTextSecondary, marginTop: 2 }]}>{dueText}</Text>
      <View style={{ height: 6, borderRadius: 3, backgroundColor: colors.darkDivider, marginTop: 12, overflow: 'hidden' }}>
        <View style={{ height: 6, borderRadius: 3, backgroundColor: colors.purpleBright, width: `${used * 100}%` }} />
      </View>
      <Text style={[typography.meta, { color: colors.darkTextSecondary, marginTop: 8 }]}>{limitText}</Text>
    </View>
  );
}
```

### Quick-Action Shortcut

```tsx
// components/QuickAction.tsx
import { Pressable, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function QuickAction({ icon, label, onPress }: { icon: any; label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({ width: 72, alignItems: 'center', gap: 8, transform: [{ scale: pressed ? 0.96 : 1 }] })}
    >
      <Pressable style={{
        width: 52, height: 52, borderRadius: 26, backgroundColor: colors.darkSurface2,
        alignItems: 'center', justifyContent: 'center',
      }}>
        <Ionicons name={icon} size={22} color={colors.purpleSoft} />
      </Pressable>
      <Text style={[typography.quickAction, { color: colors.darkTextSecondary, textAlign: 'center' }]}>{label}</Text>
    </Pressable>
  );
}
```

### Activity Row

```tsx
// components/ActivityRow.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function ActivityRow({
  icon, title, subtitle, amount, isIncome, isPending,
}: {
  icon: any; title: string; subtitle: string; amount: string;
  isIncome?: boolean; isPending?: boolean;
}) {
  return (
    <Pressable style={({ pressed }) => ({
      flexDirection: 'row', alignItems: 'center', gap: 14,
      paddingHorizontal: 22, paddingVertical: 10,
      opacity: isPending ? 0.6 : 1,
      backgroundColor: pressed ? colors.darkSurface2 : 'transparent',
    })}>
      <View style={{ width: 38, height: 38, borderRadius: 19, backgroundColor: colors.darkSurface2, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name={icon} size={19} color={colors.purpleSoft} />
      </View>
      <View style={{ flex: 1 }}>
        <Text style={[typography.rowTitle, { color: colors.darkTextPrimary }]}>{title}</Text>
        <Text style={[typography.meta, { color: colors.darkTextSecondary }]}>{subtitle}</Text>
      </View>
      <Text style={[typography.amount, { color: isIncome ? colors.positive : colors.darkTextPrimary }]}>{amount}</Text>
    </Pressable>
  );
}
```

### Pill Buttons

```tsx
// components/Buttons.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.purplePressed : colors.purple,
        borderRadius: 500, paddingVertical: 15, paddingHorizontal: 28, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function SecondaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{ borderWidth: 1.5, borderColor: colors.purpleSoft, borderRadius: 500, paddingVertical: 13, paddingHorizontal: 24 }}
    >
      <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 15, color: colors.purpleSoft }}>{title}</Text>
    </Pressable>
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
        tabBarActiveTintColor: colors.purpleSoft,   // dark: Purple Soft; use colors.purple on light
        tabBarInactiveTintColor: colors.darkTextTertiary,
        tabBarStyle: {
          backgroundColor: colors.darkCanvas,
          borderTopWidth: 0.5,
          borderTopColor: colors.darkDivider,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Início',   tabBarIcon: ({ color }) => <Ionicons name="home"        size={22} color={color} /> }} />
      <Tabs.Screen name="cards"   options={{ title: 'Cartões',  tabBarIcon: ({ color }) => <Ionicons name="card"        size={22} color={color} /> }} />
      <Tabs.Screen name="invest"  options={{ title: 'Investir', tabBarIcon: ({ color }) => <Ionicons name="trending-up" size={22} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Buscar',   tabBarIcon: ({ color }) => <Ionicons name="search"      size={22} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Perfil',   tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Tile / quick-action press — scale on tap-down (Pressable style fn or Reanimated)
transform: [{ scale: pressed ? 0.98 : 1 }]

// Balance reveal — eye toggle (LayoutAnimation or fade)
import { LayoutAnimation } from 'react-native';
LayoutAnimation.configureNext(LayoutAnimation.Presets.easeInEaseOut);

// Pix success — green check stroke draw (react-native-svg + Reanimated strokeDashoffset)
// animate strokeDashoffset from full → 0 over 400ms; then count-up the amount

// Number ticker — animate a sharedValue and render Math.round → currency format

// Tab change — instant color crossfade (system handles); ~120ms feel

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);            // quick action / tile press
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // Pix / payment done
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Nubank's product glyphs map cleanly to the outline set.

| Purpose | Ionicons |
|---------|----------|
| Início | `home` |
| Cartões | `card` |
| Investir | `trending-up` |
| Buscar | `search` |
| Perfil | `person-circle` |
| Settings (hero) | `settings-outline` |
| Notifications (hero) | `notifications-outline` |
| Help (hero) | `help-circle-outline` |
| Pix | `arrow-up` |
| Pagar | `barcode` |
| Empréstimo | `cash` |
| Conta value | `wallet-outline` |
| Pix received | `arrow-down` |
| Hide balance | `eye-outline` / `eye-off-outline` |
| Back | `chevron-back` |
| Receipt | `receipt-outline` |
| Success check | `checkmark-circle` |

## 7. Platform Notes

- **Font choice**: Nubank ships Graphik / Nu Sans; bundle **Inter** (SIL OFL) as the free substitute. Ship Regular / Medium / SemiBold / Bold / ExtraBold
- **Tabular numbers**: always set `fontVariant: ['tabular-nums']` on balances, invoice totals, and amounts so currency columns align
- **Status bar**: `<StatusBar style="light" />` over the purple hero; keep it light if you ship dark-first, else swap on theme
- **Safe area**: the purple hero extends under the status bar via `useSafeAreaInsets().top` padding; tab bar and sheets respect bottom inset
- **Dynamic Type**: RN respects system scale on `<Text>`; set `allowFontScaling={false}` on tab labels and quick-action labels
- **Keyboard**: amount entry uses `keyboardType="decimal-pad"`; wrap Pix flow in `KeyboardAvoidingView` so the running value never hides
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` / `darkTextPrimary`; keep Nu Purple and product accents unchanged; switch interactive tint to `purpleSoft`
- **Hero parallax**: drive the header height/scale from an `Animated.ScrollView` `onScroll` shared value; clamp so it never inverts
- **Accessibility**: label the NuConta tile with its balance + "double-tap to open"; the eye-toggle announces hide/show; quick actions expose their label; ensure the credit progress bar reports a value
